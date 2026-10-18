# 🎯 Smart Number Guessing Game (VS Computer) with RPS Tie-Breaker

A feature-rich, console-based C++ application where players battle against an AI in a dual-guessing number game. The project integrates a **Rock-Paper-Scissors (RPS)** mini-game to determine turn order, dynamic difficulty modes (including a Binary Search algorithm for the Hard AI), configurable search ranges, and custom modular header libraries (`MyLib.h` & `RPSGame.h`).

---

## 🌟 Key Features

* **🎲 Interactive RPS Starter:** Plays a preliminary Rock-Paper-Scissors round (with automatic tie-breaker) to fairly decide who gets the first move advantage.
* **🧠 Smart AI Mechanics:**
  * **Easy Mode:** Computer makes purely randomized guess choices within valid bounds.
  * **Hard Mode:** Computer utilizes an optimized **Binary Search Algorithm** (`Min + (Max - Min) / 2`) to aggressively narrow down the secret number.
* **⚙️ Configurable Ranges:** Choose between 3 search space difficulties:
  * Range 1: `1 - 100`
  * Range 2: `1 - 500`
  * Range 3: `1 - 1000`
* **🔄 Interactive Feedback Loop:** Dynamic bounds adjustment (`MinRange` & `MaxRange`) based on user feedback (`Too High`, `Too Low`, `Correct`).
* **🧩 Modular Architecture:** Clean code organization separated across multiple header files and namespace encapsulation (`MyLip`).
* **📊 Detailed Game Results:** Displays round summaries, player secret number, computer target number, total rounds, and the final winner.

---

## 📁 Project Structure

```text
Number-Guessing-Game/
│
├── main.cpp         # Main game state manager, execution loop, and user feedback logic
├── RPSGame.h        # Rock-Paper-Scissors game engine & turn-determination logic
├── MyLib.h          # Custom core library (Input validation, RNG, UI headers & formatting)
└── README.md        # Detailed project documentation

🛠️ Concepts & Technologies Applied
Language: C++

Algorithms: Binary Search principle applied for AI decision-making in Hard mode.

Modular Software Design: Splitting project responsibilities across #include "MyLib.h" and #include "RPSGame.h".

Data Abstraction: Using nested struct objects (stGameLevelInfo, stGameResults, stRoundInfo) to handle clean data transfer between functions.

Strong Typing: Utilizing C++ enum types for type-safe state management (enWhoStartFirst, enGuessFeedback, enGameRange, enGameLevel, enWinner).

Input Handling: Robust range validation functions to prevent invalid user inputs.

⚙️ How to Compile and Run
1. Requirements
C++ Compiler (GCC, Clang, MSVC) supporting C++11 or higher.

2. Compilation
Ensure all source files (main.cpp, RPSGame.h, and MyLib.h) are located in the same directory.

Bash
g++ main.cpp -o guessing_game
3. Execution
Windows:

DOS
guessing_game.exe
Linux / macOS:

Bash
./guessing_game

🎮 How to Play
Select Game Range: Pick the maximum number limit (1-100, 1-500, or 1-1000).

Select Difficulty: Choose Easy (Random AI) or Hard (Smart Binary Search AI).

Rock-Paper-Scissors: Play RPS against the computer to win the right to start first.

Choose Secret Number: Set your target secret number according to the range chosen.

Take Turns:

Provide feedback (1: Higher, 2: Lower, 3: Correct) when the computer guesses your number.

Enter your guess when it's your turn to find the computer's secret number.

Win Condition: The first to correctly guess the opponent's number wins the game!

👤 Author
Ibrahim Al-Tarifi

Software Engineering Student & C++ Developer

Focused on Algorithms, Data Structures, and Software Design Principles.
