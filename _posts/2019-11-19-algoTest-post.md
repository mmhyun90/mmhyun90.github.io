---
title: 백준_17779_게리맨더링2
date: 2019-11-19
comment: true
---

## 17779. [게리멘더링2](https://www.acmicpc.net/problem/17779)

''' java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	
	static int N;
	static int[][] map;
	static int[][] area;
	static int answer;
	static final int INF = Integer.MAX_VALUE >> 2;
	static int[][] dir = {{1, -1}, {1, 1}};		// 좌하, 우하
	static int allSum;
	static int[] countStore;
	
	public static void printArea() {
		for(int i = 0; i < N; i++) {
			for(int j = 0; j < N; j++) {
				System.out.print(area[i][j] + " ");
			}
			System.out.println();
		}
		System.out.println();
	}
	
	public static void resetStore() {
		for(int i = 0; i < 5; i++) {
			countStore[i] = 0;
		}
	}
	
	public static void makeArea(int r, int c, int lcnt, int rcnt) {
		int nextR, nextC, nowR, nowC;
		
		// 좌하 대각선 채우기
		for(int i = 0; i <= rcnt; i++) {
			
			nowR = r + dir[1][0]*i;
			nowC = c + dir[1][1]*i;
			
			for(int j = 0; j <= lcnt; j++) {
				nextR = nowR + dir[0][0]*j;
				nextC = nowC + dir[0][1]*j;
				
				area[nextR][nextC] = 5;
				countStore[4] += map[nextR][nextC];
			}
		}
		
		// 우하 대각선 채우기
		for(int i = 0; i < lcnt; i++) {
			
			nowR = r + 1 + dir[0][0]*i;
			nowC = c + dir[0][1]*i;
			
			for(int j = 0; j < rcnt; j++) {
				nextR = nowR + dir[1][0]*j;	// r, c의 한 칸 밑에서 시작
				nextC = nowC + dir[1][1]*j;
				
				area[nextR][nextC] = 5;
				countStore[4] += map[nextR][nextC];
			}
		}
		
		
		// 나머지 4분할 경계를 기준으로 채우기
		// 1. 좌상
		for(int i = 0; i < r + lcnt; i++) {
			for(int j = 0; j <= c; j++) {
				if(area[i][j] == 0) {
					area[i][j] = 1;
					countStore[0] += map[i][j];
				}
			}
		}
		
		// 2. 우상
		for(int i = 0; i <= r + rcnt; i++) {
			for(int j = c-1; j < N; j++) {
				if(area[i][j] == 0) {
					area[i][j] = 2;
					countStore[1] += map[i][j];
				}
			}
		}
		
		// 3. 좌하
		for(int i = r + lcnt; i < N; i++) {
			for(int j = 0; j < c - lcnt + rcnt; j++) {
				if(area[i][j] == 0) {
					area[i][j] = 3;
					countStore[2] += map[i][j];
				}
			}
		}
		
		// 4. 우하
		for(int i = r + rcnt + 1; i < N; i++) {
			for(int j = c - lcnt + rcnt; j < N; j++) {
				if(area[i][j] == 0) {
					area[i][j] = 4;
					countStore[3] += map[i][j];
				}
			}
		}
		
	}
	
	public static void initArea() {
		for(int i = 0; i < N; i++) {
			for(int j = 0; j < N; j++) {
				area[i][j] = 0;
			}
		}
	}
	
	// 구역을 나눌 수 있는 범위 안에서 한 점을 잡고 좌/우 길이를 조절하여 4점 경계를 잡는다
	public static void setBoundary(int r, int c) {
		int maxLlen = c;
		int maxRlen = N - 1 - c;
		int sub;
		
		for(int i = 1; i <= maxLlen; i++) {
			for(int j = 1; j <= maxRlen; j++) {
				if(r + i >= N) {
					continue;
				}
				
				if(r + j >= N) {
					continue;
				}
				
				if(r + i + j >= N) {
					continue;
				}
				
				// 경계 기준으로 구역 나누기
				makeArea(r, c, i, j);

				initArea();
				
				// answer 갱신
				Arrays.sort(countStore);
				sub = countStore[4] - countStore[0];
				answer = Math.min(answer, sub);
				resetStore();
			}
		}
		
	}
	
	public static void main(String[] args) throws Exception{
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;
		N = Integer.parseInt(br.readLine().trim());
		map = new int[N][N];
		area = new int[N][N];
		countStore = new int[5];
		
		allSum = 0;
		int nowInt;
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine().trim());
			
			for(int j = 0; j < N; j++) {
				map[i][j] = nowInt = Integer.parseInt(st.nextToken());
				allSum += nowInt;
			}
		}
		
		// Init
		answer = INF;
		
		// Sol
		// N안에서 시작 좌표를 설정할 수 있는 범위를 줄여놓는다
		for(int i = 0; i < N-2; i++) {
			for(int j = 1; j < N-1; j++) {
				setBoundary(i, j);
			}
		}
		
		
		// Print
		System.out.println(answer);
	}
}

'''