# Tic-Tac-Toe using Processes and IPC

A C implementation of Tic-Tac-Toe that demonstrates fundamental **Linux Operating System concepts** through a multi-process architecture.

## Overview

The game consists of a **Judge process** and two separate **Player processes**. The Judge creates the player processes using `fork()` and executes the player program using `exec()`.

Communication between the Judge and Players is performed using a **pipe**, while signals are used to notify players when it is their turn.

## OS Concepts Used

- `fork()` – Create child/player processes
- `exec()` – Execute the player program
- `pipe()` – Inter-process communication
- `dup2()` – Redirect standard input using file descriptors
- `SIGUSR1` – Notify a player to make a move
- `SIGINT` – Terminate player processes
- `waitpid()` – Wait for and reap child processes
- File descriptors and process synchronization

## Working

1. The Judge creates a pipe for communication.
2. Two player processes are created using `fork()`.
3. Each child executes the player program using `exec()`.
4. The Judge signals the current player using `SIGUSR1`.
5. The player generates a move and sends the row and column through the pipe.
6. The Judge reads the move, validates it, and updates the board.
7. The Judge checks for a win or draw after every valid move.
8. When the game ends, the Judge terminates the player processes and uses `waitpid()` to clean them up.

## Features

- Two-player Tic-Tac-Toe
- Process-based player architecture
- Inter-process communication using pipes
- Signal-based player synchronization
- Invalid move handling
- Win and draw detection
- Proper child-process cleanup

## Process / Execution Tree

The overall process structure looks like this:

```text
                         ┌─────────────────┐
                         │   Judge Process │
                         │    (judge.c)    │
                         └────────┬────────┘
                                  │
                         fork()   │   fork()
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
          ┌─────────────────┐         ┌─────────────────┐
          │   Player 1      │         │   Player 2      │
          │   (child)       │         │   (child)       │
          └────────┬────────┘         └────────┬────────┘
                   │                           │
                 exec()                      exec()
                   │                           │
                   ▼                           ▼
          ┌─────────────────┐         ┌─────────────────┐
          │   player.c      │         │   player.c      │
          │  Player O       │         │  Player X       │
          └────────┬────────┘         └────────┬────────┘
                   │                           │
                   │       Pipe (IPC)          │
                   └────────────┬──────────────┘
                                │
                                ▼
                         ┌──────────────┐
                         │     Judge    │
                         │ Reads Moves  │
                         └──────────────┘

## Files

- `judge.c` – Controls the game and coordinates the players
- `player.c` – Handles player moves and communicates with the Judge

## Compilation

```bash
gcc judge.c -o judge
gcc player.c -o player


