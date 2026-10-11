#pragma once

#include <iostream>
#include <cstdlib>
#include <ctime>
using namespace std;

enum enGameChoice { Stone = 1, Paper = 2, Scissors = 3 };
enum enWinner { player1 = 1, computer = 2, draw = 3 };

struct stRoundInfo
{
    short RoundNumber = 0;
    enGameChoice Player1Choice;
    enGameChoice ComputerChoice;
    enWinner Winner;
    string WinnerName;
};

int RandomNumber(int From, int To)
{
    srand(unsigned(time(NULL)));

    return rand() % (To - From + 1) + From;
}

enGameChoice GetComputerChoice()
{
    srand(unsigned(time(NULL)));

    return (enGameChoice)RandomNumber(1, 3);
}

enWinner WhoWonTheRound(stRoundInfo RoundInfo)
{
    if (RoundInfo.Player1Choice == RoundInfo.ComputerChoice)
        return enWinner::draw;

    switch (RoundInfo.Player1Choice)
    {
    case enGameChoice::Stone:
        return (RoundInfo.ComputerChoice == enGameChoice::Paper) ? enWinner::computer : enWinner::player1;
    case enGameChoice::Paper:
        return (RoundInfo.ComputerChoice == enGameChoice::Scissors) ? enWinner::computer : enWinner::player1;
    case enGameChoice::Scissors:
        return (RoundInfo.ComputerChoice == enGameChoice::Stone) ? enWinner::computer : enWinner::player1;
    }
}

string ChoiceName(enGameChoice Choice)
{
    string arrGameChoices[3] = { "Stone", "Paper", "Scissors" };
    return arrGameChoices[Choice - 1];
}

string WinnerName(enWinner Winner)
{
    string arrWinnerName[3] = { "Player1", "Computer", "No Winner (Draw)" };
    return arrWinnerName[Winner - 1];
}

enGameChoice ReadPlayer1Choice()
{
    short Choice;
    do
    {
        cout << "\nYour Choice: [1]:Stone, [2]:Paper, [3]:Scissors? ";
        cin >> Choice;
    } while (Choice < 1 || Choice > 3);
    return (enGameChoice)Choice;
}

void PrintRoundResults(stRoundInfo RoundInfo)
{
    cout << "\n____________ Round [" << RoundInfo.RoundNumber << "] ____________\n\n";
    cout << "Player1 Choice: " << ChoiceName(RoundInfo.Player1Choice) << endl;
    cout << "Computer Choice: " << ChoiceName(RoundInfo.ComputerChoice) << endl;
    cout << "Round Winner   : [" << RoundInfo.WinnerName << "]\n";
    cout << "_________________________________________\n" << endl;
}

// تعديل StartGame لتلعب جولة واحدة وترجع الفائز (إما Player1 أو Computer)
enWinner StartRPSGame()
{
    srand(unsigned(time(NULL)));

    stRoundInfo RoundInfo;
    RoundInfo.RoundNumber = 1;

    cout << "\n==================================================\n";
    cout << "    DETERMINING WHO STARTS (Rock-Paper-Scissors)\n";
    cout << "==================================================\n";

    RoundInfo.Player1Choice = ReadPlayer1Choice();
    RoundInfo.ComputerChoice = GetComputerChoice();
    RoundInfo.Winner = WhoWonTheRound(RoundInfo);
    RoundInfo.WinnerName = WinnerName(RoundInfo.Winner);

    PrintRoundResults(RoundInfo);

    // لو طلع تعادل، تعيد الجولة فوراً لضمان وجود فائز يبدأ اللعبة
    while (RoundInfo.Winner == enWinner::draw)
    {
        cout << "\nIt's a Draw! Playing a tie-breaker round...\n";
        RoundInfo.Player1Choice = ReadPlayer1Choice();
        RoundInfo.ComputerChoice = GetComputerChoice();
        RoundInfo.Winner = WhoWonTheRound(RoundInfo);
        RoundInfo.WinnerName = WinnerName(RoundInfo.Winner);
        PrintRoundResults(RoundInfo);
    }

    return RoundInfo.Winner;
}
