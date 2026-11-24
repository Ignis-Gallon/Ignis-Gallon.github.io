---
title: "과제2"
date: 2023-04-11
last_modified_at: 2023-05-05
category: major
tags: [운영체제, 스레드, C, 콘솔 게임]
---

# 과제2

작성일 2023-04-11 · 최종 수정일 2023-05-05
{ .post-date }

- [conio.h: 콘솔 키 입력 함수](03-conio-h.md)
- [Plan: 스레드 기반 콘솔 게임 구현 계획](04-assignment-2-plan.md)

**ex code**

```c
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include <windows.h>

#define WIDTH 30
#define HEIGHT 10
#define PUCK 'O'
#define PLAYER 'P'
#define OPPONENT 'E'
#define EMPTY ' '

void draw_board(char board[][WIDTH]);
void move_player(char board[][WIDTH], int* player_x, int* player_y, int dx, int dy);
void move_opponent(char board[][WIDTH], int* opponent_x, int* opponent_y, int player_x, int player_y);
void move_puck(char board[][WIDTH], int* puck_x, int* puck_y, int* puck_dx, int* puck_dy);
void check_collision(char board[][WIDTH], int* puck_x, int* puck_y, int* puck_dx, int* puck_dy, int player_x, int player_y, int opponent_x, int opponent_y);

int main() {
    char board[HEIGHT][WIDTH];

    int player_x = WIDTH / 2;
    int player_y = HEIGHT - 2;
    int opponent_x = WIDTH / 2;
    int opponent_y = 1;
    int puck_x = WIDTH / 2;
    int puck_y = HEIGHT / 2;
    int puck_dx = 1;
    int puck_dy = 1;

    while (1) {
        system("cls");
        for (int y = 0; y < HEIGHT; y++) {
            for (int x = 0; x < WIDTH; x++) {
                if (x == player_x && y == player_y) {
                    board[y][x] = PLAYER;
                }
                else if (x == opponent_x && y == opponent_y) {
                    board[y][x] = OPPONENT;
                }
                else if (x == puck_x && y == puck_y) {
                    board[y][x] = PUCK;
                }
                else {
                    board[y][x] = EMPTY;
                }
            }
        }

        draw_board(board);
        move_opponent(board, &opponent_x, &opponent_y, player_x, player_y);

        if (_kbhit()) {
            int dx = 0, dy = 0;
            switch (_getch()) {
            case 'w': dy = -1; break;
            case 'a': dx = -1; break;
            case 's': dy = 1; break;
            case 'd': dx = 1; break;
            case 'q': return 0;
            }
            move_player(board, &player_x, &player_y, dx, dy);
        }

        move_puck(board, &puck_x, &puck_y, &puck_dx, &puck_dy);
        check_collision(board, &puck_x, &puck_y, &puck_dx, &puck_dy, player_x, player_y, opponent_x, opponent_y);

        Sleep(50);
    }
}

// Add the other functions below the main function as shown in the previous responses.

void draw_board(char board[][WIDTH]) {
    for (int y = 0; y < HEIGHT; y++) {
        for (int x = 0; x < WIDTH; x++) {
            putchar(board[y][x]);
        }
        putchar('\n');
    }
}

void move_player(char board[][WIDTH], int* player_x, int* player_y, int dx, int dy) {
    int new_x = *player_x + dx;
    int new_y = *player_y + dy;

    if (new_x >= 0 && new_x < WIDTH && new_y >= 0 && new_y < HEIGHT) {
        *player_x = new_x;
        *player_y = new_y;
    }
}

void move_opponent(char board[][WIDTH], int* opponent_x, int* opponent_y, int player_x, int player_y) {
    int dx = 0, dy = 0;

    if (*opponent_x < player_x) {
        dx = 1;
    }
    else if (*opponent_x > player_x) {
        dx = -1;
    }

    if (*opponent_y < player_y) {
dy = 1;
    }
 else if (*opponent_y > player_y) {
 dy = -1;
    }

    int new_x = *opponent_x + dx;
    int new_y = *opponent_y + dy;

    if (new_x >= 0 && new_x < WIDTH && new_y >= 0 && new_y < HEIGHT) {
        *opponent_x = new_x;
        *opponent_y = new_y;
    }
}

void move_puck(char board[][WIDTH], int* puck_x, int* puck_y, int* puck_dx, int* puck_dy) {
    int new_x = *puck_x + *puck_dx;
    int new_y = *puck_y + *puck_dy;

    if (new_x < 0 || new_x >= WIDTH) {
        *puck_dx *= -1;
    }
    else {
        *puck_x = new_x;
    }

    if (new_y < 0 || new_y >= HEIGHT) {
        *puck_dy *= -1;
    }
    else {
        *puck_y = new_y;
    }
}

void check_collision(char board[][WIDTH], int* puck_x, int* puck_y, int* puck_dx, int* puck_dy, int player_x, int player_y, int opponent_x, int opponent_y) {
    int new_x = *puck_x + *puck_dx;
    int new_y = *puck_y + *puck_dy;

    if ((new_x == player_x && new_y == player_y) || (new_x == opponent_x && new_y == opponent_y)) {
        *puck_dx *= -1;
        *puck_dy *= -1;
    }
}
```
