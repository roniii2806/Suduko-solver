#include <iostream>
#include <vector>
#include <ctime>
#include <cstdlib>
using namespace std;

const int N = 9;

class Sudoku {
private:
    int board[N][N];
    int rowMask[N], colMask[N], boxMask[N];

    int getBoxIndex(int i, int j) {
        return (i/3)*3 + (j/3);
    }

    bool isSafe(int i, int j, int num) {
        int mask = 1 << num;
        return !(rowMask[i] & mask) && !(colMask[j] & mask) && !(boxMask[getBoxIndex(i,j)] & mask);
    }

    void placeNumber(int i, int j, int num) {
        board[i][j] = num;
        int mask = 1 << num;
        rowMask[i] |= mask;
        colMask[j] |= mask;
        boxMask[getBoxIndex(i,j)] |= mask;
    }

    void removeNumber(int i, int j, int num) {
        board[i][j] = 0;
        int mask = ~(1 << num);
        rowMask[i] &= mask;
        colMask[j] &= mask;
        boxMask[getBoxIndex(i,j)] &= mask;
    }

    bool solve(int i = 0, int j = 0) {
        if (i == N)
            return true;
        if (j == N)
            return solve(i + 1, 0);
        if (board[i][j] != 0)
            return solve(i, j + 1);
        
        for (int num = 1; num <= 9; num++) {
            if (isSafe(i, j, num)) {
                placeNumber(i, j, num);
                if (solve(i, j + 1))
                    return true;
                removeNumber(i, j, num);
            }
        }
        return false;
    }

public:
    Sudoku() {
        for (int i = 0; i < N; i++)
            for (int j = 0; j < N; j++)
                board[i][j] = 0;
        
        for (int i = 0; i < N; i++)
            rowMask[i] = colMask[i] = boxMask[i] = 0;
    }

    void generateFullSolution() {
        solve();
    }

    void generatePuzzle(int difficulty) {
        generateFullSolution();
        int cellsToRemove = 20 + difficulty * 10;
        while (cellsToRemove > 0) {
            int i = rand() % 9;
            int j = rand() % 9;
            if (board[i][j] != 0) {
                int num = board[i][j];
                removeNumber(i, j, num);
                board[i][j] = 0;
                cellsToRemove--;
            }
        }
    }

    void inputBoard() {
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                cin >> board[i][j];
                if (board[i][j] != 0)
                    placeNumber(i, j, board[i][j]);
            }
        }
    }

    bool solveSudoku() {
        return solve();
    }

    void printBoard() {
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                cout << board[i][j] << " ";
            }
            cout << endl;
        }
    }
};

int main() {
    srand(time(0));
    Sudoku sdk;
    
    cout << "Choose Mode:\n1. Generate Puzzle\n2. Input Puzzle\n";
    int choice;
    cin >> choice;
    
    if (choice == 1) {
        cout << "Choose Difficulty (1-Easy, 2-Medium, 3-Hard): ";
        int level;
        cin >> level;
        sdk.generatePuzzle(level);
        cout << "Generated Puzzle:\n";
        sdk.printBoard();
        cout << "Solving Puzzle...\n";
        if (sdk.solveSudoku()) {
            cout << "Solved Sudoku:\n";
            sdk.printBoard();
        } else {
            cout << "No solution exists!\n";
        }
    } 
    else {
        cout << "Input 9x9 Sudoku board (0 for empty cells):\n";
        sdk.inputBoard();
        if (sdk.solveSudoku()) {
            cout << "Solved Sudoku:\n";
            sdk.printBoard();
        } else {
            cout << "No solution exists!\n";
        }
    }
    return 0;
}
