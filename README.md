#include <iostream>
#include <vector>

using namespace std;

const int BOARD_SIZE = 40;
const int NUM_PLAYERS = 4;
const int NUM_PIECES_PER_PLAYER = 4;

class LudoGame {
private:
    vector<int> board;
    vector<vector<int>> players;
    int currentPlayer;

public:
    LudoGame() {
        // Initialize the board
        board.resize(BOARD_SIZE, -1); // -1 indicates an empty space
        players.resize(NUM_PLAYERS, vector<int>(NUM_PIECES_PER_PLAYER, -1));

        // Initialize player positions
        for (int i = 0; i < NUM_PLAYERS; ++i) {
            for (int j = 0; j < NUM_PIECES_PER_PLAYER; ++j) {
                players[i][j] = i * 10; // Starting positions for each player
                board[i * 10] = i;      // Marking player's pieces on the board
            }
        }

        // Set the starting player
        currentPlayer = 0;
    }

    void rollDice() {
        // Generate a random number between 1 and 6
        int dice = rand() % 6 + 1;
        cout << "Player " << currentPlayer << " rolled a " << dice << endl;

        // Move the current player's piece
        movePiece(currentPlayer, dice);

        // Switch to the next player
        currentPlayer = (currentPlayer + 1) % NUM_PLAYERS;
    }

    void movePiece(int player, int steps) {
        // Find the first movable piece for the player
        for (int i = 0; i < NUM_PIECES_PER_PLAYER; ++i) {
            int currentPosition = players[player][i];
            if (currentPosition != -1) {
                // Calculate the new position after rolling the dice
                int newPosition = (currentPosition + steps) % BOARD_SIZE;

                // Check if the new position is occupied by another player's piece
                if (board[newPosition] != -1 && board[newPosition] != player) {
                    cout << "Player " << player << "'s piece was knocked off by Player " << board[newPosition] << endl;
                    // Reset the knocked off piece to its starting position
                    players[board[newPosition]][i] = board[newPosition] * 10;
                }

                // Update the board and player's position
                board[currentPosition] = -1;
                board[newPosition] = player;
                players[player][i] = newPosition;
                return;
            }
        }
    }

    bool isGameOver() {
        // Check if any player has moved all their pieces to the end
        for (int i = 0; i < NUM_PLAYERS; ++i) {
            bool allPiecesAtEnd = true;
            for (int j = 0; j < NUM_PIECES_PER_PLAYER; ++j) {
                if (players[i][j] != BOARD_SIZE - 1) {
                    allPiecesAtEnd = false;
                    break;
                }
            }
            if (allPiecesAtEnd) {
                cout << "Player " << i << " wins!" << endl;
                return true;
            }
        }
        return false;
    }
};

int main() {
    LudoGame game;
    while (!game.isGameOver()) {
        game.rollDice();
    }
    return 0;
}
