#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>   // For usleep() to delay execution

#define H 15  // This is playfield height
#define W 40  // This is playfield width
#define YELLOW "\x1B[33m"
#define RESET "\x1b[0m"

char playfield[H][W] = {
    {"**************"},
    {"* .....................................*"},
    {".........................."},
    {".........................."},
    {".........................."},
    {".........................."},
    {".........................."},
    {"......................................"},
    {".........................."},
    {".........................."},
    {".........................."},
    {".........................."},
    {".........................."},
    {"......................................"},
    {"**************"}
}; // This is our playfield

int food_collect = 0, game_end = 0;
int py = 1, px = 1;                  // Pacman coordinates
int gy1 = 1, gx1 = 38, gy2 = 13, gx2 = 1; // Ghosts coordinates

void game_result() {
    // This function checks if you won the game or not
    if (food_collect == 500) {
        printf("\n\n\n\n\n\n\n\n\n");
        printf("\t\t\t        Congratulations!\n");
        printf("\t\t\t       You won the game!\n");
        printf("\t\t\t     Your total score is %d", food_collect);
    } else {
        printf("\n\n\n\n\n\n\n\n\n");
        printf("\t\t\t          Better luck!\n");
        printf("\t\t\t       You lose the game!\n");
        printf("\t\t\t     Your total score is %d\n", food_collect);
    }
}

void move_ghosts() {
    if (gy1 == 1 && gx1 > 1) {
        gx1--; // Move 1st ghost to the left
    } else if (gx1 == 1 && gy1 < 7) {
        gy1++; // Move 1st ghost down
    } else if (gy1 == 7 && gx1 < 38) {
        gx1++; // Move 1st ghost to the right
    } else if (gx1 == 38 && gy1 > 1) {
        gy1--; // Move 1st ghost up
    }

    if (gy2 == 13 && gx2 < 38) {
        gx2++; // Move 2nd ghost to the right
    } else if (gx2 == 38 && gy2 > 7) {
        gy2--; // Move 2nd ghost up
    } else if (gy2 == 7 && gx2 > 1) {
        gx2--; // Move 2nd ghost to the left
    } else if (gx2 == 1 && gy2 < 13) {
        gy2++; // Move 2nd ghost down
    }
}

void user_input() {
    // Non-blocking user input
    char c1= getchar();
    system("stty raw -echo"); // Switch terminal to raw mode (no Enter key needed)

    if ((c1 = getchar()) != EOF) {
        switch (c1) {
            case 'w': py -= 1; break; // 'w' for up
            case 's': py += 1; break; // 's' for down
            case 'a': px -= 1; break; // 'a' for left
            case 'd': px += 1; break; // 'd' for right
            case 'q': game_end = 1; break; // 'q' to quit the game
        }
    }

    system("stty cooked echo"); // Switch terminal back to normal mode
}

void setup() {
    int i, j;
    for (i = 0; i < H; i++) {
        for (j = 0; j < W; j++) {
            if (playfield[i][j] == '#') {
                playfield[i][j] = ' ';
            }
            if (playfield[i][j] == '@') {
                playfield[i][j] = '.';
            }
        }
    }

    if (playfield[py][px] == '.') {
        food_collect++;
    }

    if (playfield[py][px] == '*') {
        py = 1;
        px = 1;
    }

    playfield[py][px] = '#';
    playfield[gy1][gx1] = '@';
    playfield[gy2][gx2] = '@';

    if (playfield[py][px] == '@') {
        game_end = 1;
    }
}

void draw_playfield() {
    printf("\n\n\n\n");
    for (int i = 0; i < H; i++) {
        printf("\t\t");
        for (int j = 0; j < W; j++) {
            printf("%c", playfield[i][j]);
        }
        printf("\n");
    }
    printf("Score is %d\n", food_collect);
}

int main() {
    int i = 100;
    while (game_end != 1) { // This loop runs until game_end = 1
        system("clear"); // Clear the screen

        setup();
        user_input(); // Call user input
        move_ghosts();
        draw_playfield();

        usleep(100000); // Delay program execution (100ms)
    }

    game_result();
    getchar();
}
