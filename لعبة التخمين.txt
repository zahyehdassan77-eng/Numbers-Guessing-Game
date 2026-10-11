#pragma warning(disable : 4996)

#include <iostream>
#include <string>
#include <cmath>
#include <iomanip>
#include <cstdlib>
#include <ctime>
#include <cctype>
#include <vector>
#include <fstream>
#include "MyLip.h"
#include "RPSGame.h"

using std::cout;
using std::cin;
using std::endl;
using std::string;

using namespace std;

// خيارات من يبدأ بالتخمين
enum enWhoStartFirst { Player = 1, Computer = 2, Draw = 3 };

// خيارات الرد على التخمين
enum enGuessFeedback { TooHigh = 1, TooLow = 2, Correct = 3 };

// مستويات اللعبة 
enum enGameRange { Level_1 = 1, Level_2 = 2, Level_3 = 3 };

// لتحديد مستوى ذكاء البرنامج
enum enGameLevel { Easy = 1, Hard = 2 };

// تخزن المستوى و الرينج المخصص له
struct stGameLevelInfo {
    enGameLevel Level;
    enGameRange Range;
    int MinRange;
    int MaxRange;
    enWhoStartFirst StartFirst;
};

// إحصائيات ونتائج الجولة
struct stGameResults {
    short PlayerTargetNumber;   // الرقم اللي اختاره اللاعب
    short ComputerTargetNumber; // الرقم اللي اختاره الكمبيوتر
    short RoundNumber;          // عدد جولات اللعبة
    enWinner Winner;     // مين فاز بالجولة
};

// لعبة حجرة ورقة مقص لتحديد من سيبدأ اولا
enWhoStartFirst GetWhoStartFirst()
{
    enWinner WhoStarts = StartRPSGame();

    if (WhoStarts == enWinner::player1)
    {
        cout << "\n You won Rock-Paper-Scissors! YOU START FIRST!\n";

        system("pause");

        MyLip::ResetGame();

        return enWhoStartFirst::Player;
    }
    else
    {
        cout << "\n Computer won Rock-Paper-Scissors! COMPUTER STARTS FIRST!\n";
        cout << "Press any key to continue ...";
        system("pause");

        MyLip::ResetGame();

        return enWhoStartFirst::Computer;
    }
}

//يسأل المستخدم عن المستوى الذي يريد اللعب فيه
enGameRange ReadGameRange()
{
    short Range = 1;

    MyLip::PrintHeader(1, " NUMBERS GUESSING GAME (VS Computer)");

    cout << "[1]    ( Range: 1 - 100  )\n";
    cout << "[2]    ( Range: 1 - 500  )\n";
    cout << "[3]    ( Range: 1 - 1000 )\n";
    cout << "==================================================\n";

    Range = MyLip::ReadNumber("Choose Game Range [1 to 3]? ", 1, 3);

    cout << "\nThe range has been successfully defined.\n";

    return (enGameRange)Range; // Casting to Enum
}

//يسأل المستخدم عن مستوى صعوبة اللعبة
enGameLevel ReadGameLevel()
{
    short Level = 1;

    MyLip::PrintHeader(1, " NUMBERS GUESSING GAME (VS Computer)");

    cout << "[1]  Easy \n";
    cout << "[2]  Hard\n";
    cout << "==================================================\n";

    Level = MyLip::ReadNumber("Choose Game Level [1 OR 2]? ", 1, 2);

    cout << "\nThe level has been successfully defined.\n";

    return (enGameLevel)Level; // Casting to Enum
}


//تأخذ المستوى الذي اختاره المستخدم و ثم تعيد ستركت يحمل معلومات المستوى الذي اختاره المستخدم
stGameLevelInfo GetGameLevelInfo()
{
    stGameLevelInfo LevelInfo;
    enGameRange GameRange = ReadGameRange();

    system("pause");
    MyLip::ResetGame();

    enGameLevel GameLevel = ReadGameLevel();
    LevelInfo.Level = GameLevel;

    system("pause");

    switch (GameRange)
    {
    case enGameRange::Level_1:
        LevelInfo.Range = enGameRange::Level_1;
        LevelInfo.MaxRange = 100;
        LevelInfo.MinRange = 1;
        break;

    case enGameRange::Level_2:
        LevelInfo.Range = enGameRange::Level_2;
        LevelInfo.MaxRange = 500;
        LevelInfo.MinRange = 1;
        break;

    case enGameRange::Level_3:
        LevelInfo.Range = enGameRange::Level_3;
        LevelInfo.MaxRange = 1000;
        LevelInfo.MinRange = 1;
        break;
    }

    MyLip::ResetGame();

    // لتحديد من سيبدأ اول
    LevelInfo.StartFirst = GetWhoStartFirst();

    return LevelInfo;
}

//يخمن رقم عشوائي ضمن الاحتمالات المحتملة
short GetComputerGusses(short MinGussesNumber, short MaxGussesNumber, enGameLevel GameLevel)
{
    if(GameLevel == enGameLevel::Easy)
        return MyLip::RandomNumber(MinGussesNumber, MaxGussesNumber);

    else 
        return MinGussesNumber + (MaxGussesNumber - MinGussesNumber) / 2;
}

//بعد تحديد تخمين الكمبيوتر يقوم بطباعته
void PrintComputerGusses(short& ComputerGusses, short MaxGussesNumber, short MinGussesNumber, enGameLevel GameLevel)
{
    MyLip::PrintHeader(0, "----- Computer Gusses -----");
    cout << endl;

    ComputerGusses = GetComputerGusses(MinGussesNumber, MaxGussesNumber, GameLevel);

    cout << " >> The Computer Gusses Is : \" " << ComputerGusses << " \"" << endl;
}

//يقوم بسؤال المستخدم عن توقع الكمبيوتر و يعيد لنا اجابة من الثلاثة و هي ان التوقع اصغر او ان التوقع اكبر او ان التوقع صحيح
enGuessFeedback GetUserFeedback(short ComputerGusses)
{
    short UserFeedback = 0;
    cout << "\nIs your secret number:\n[1] Higher than " << ComputerGusses << "\n[2] Lower than " << ComputerGusses << "\n[3] Correct!\n\n";
    cout << "Choose Feedback[1 to 3]: ";
    cin >> UserFeedback;

    cout << endl;

    return enGuessFeedback(UserFeedback);
}

//يفحص تخمين الكمبيوتر مقارنة باجابة المستخدم ليعدل على رينج التوقعات للكمبيوتر او ليعلن فوز الكمبيوتر اذا كان تخمينه صحيح
void CheckUserFeedback(enGuessFeedback UserFeedback, enWinner& Winner, short ComputerGusses, short& MinGussesNumber, short& MaxGussesNumber)
{
    switch (UserFeedback)
    {
    case enGuessFeedback::TooHigh: MinGussesNumber = ComputerGusses + 1;
        break;

    case enGuessFeedback::TooLow: MaxGussesNumber = ComputerGusses - 1;
        break;

    case enGuessFeedback::Correct:
    {
        cout << "\n=======================================================\n";
        cout << "\nYou Lose :-(, The Winner Is Computer :-)\n";
        cout << "\n=======================================================\n";

        Winner = enWinner::computer;

        break;
    }
    }
}

//يسأل المستخدم عن تخمينه و يعيده لنا 
short GetUserGusses(stGameLevelInfo LevelInfo)
{
    MyLip::PrintHeader(0, "----- User Gusses -----");
    cout << endl;
    return MyLip::ReadNumber("Pleas Enter Your Gusses ? ", LevelInfo.MinRange, LevelInfo.MaxRange);
}

//بيفحص تخمين اللاعب بالنسبة للرقم الي اختاره الكمبيوتر و بيعطي فيد باك عليه اما انه اللاعب فاز و انتهت اللعبة او انه الرقم اصغر او انه الرقم اكبر من رقم الكمبيوتر
void CheckUserGusses(short UserGusses, short ComputerChoice, enWinner& Winner)
{
    if (UserGusses == ComputerChoice)
    {
        cout << "\n============================================\n\nYou Win :-) , Mubarak... \n\n============================================\n";
        Winner = enWinner::player1;
    }

    else if (UserGusses < ComputerChoice)
    {
        cout << "\nThe secret Number of Computer Is Bigger Than " << UserGusses << endl;
    }

    else if (UserGusses > ComputerChoice)
    {
        cout << "\nThe secret Number of Computer Is Smaller Than " << UserGusses << endl;
    }
    
    cout << endl;
}

//يقوم بتعبئة الستركت الخاص بالنتيجة النهائية
stGameResults FillGameResult(enWinner Winner, short RoundNumber, short ComputerChoice, short UserChoice)
{
    stGameResults GameResult;

    GameResult.Winner = Winner;
    GameResult.RoundNumber = RoundNumber;
    GameResult.ComputerTargetNumber = ComputerChoice;
    GameResult.PlayerTargetNumber = UserChoice;

    return GameResult;
}

// هذا الفنكشن يأخذ معلومات المستوى و يبدأ الجولة بناءا عليها عندما يكون الكمبيوتر سيبدأ أولا
stGameResults PlayRound(stGameLevelInfo LevelInfo)
{
    stGameResults GameResult;
    enWinner Winner = enWinner::draw;
    short ComputerChoice = MyLip::RandomNumber(LevelInfo.MinRange, LevelInfo.MaxRange);

    //تعرض الرقم الذي اختاره المستخدم لكي لا ينساه :-)
    cout << "======================================\n";
    short UserChoice = MyLip::ReadNumber("Pleas Enter your Secret Number ? ", LevelInfo.MinRange, LevelInfo.MaxRange);
    cout << "======================================\n\n";

    short ComputerGusses = 0, UserGusses = 0, RoundNumber = 0;
    short MaxGussesNumber = LevelInfo.MaxRange, MinGussesNumber = LevelInfo.MinRange;
    enWhoStartFirst StartFirst = LevelInfo.StartFirst;



    //شرط لتحديد ما اذا كان الكمبيوتر سيبدأ اولا ام المستخدم
    if (StartFirst == enWhoStartFirst::Computer)
    {
        while (Winner == enWinner::draw)
        {
            RoundNumber++;

            cout << "Round [" << RoundNumber << "] : \n\n";

            PrintComputerGusses(ComputerGusses, MaxGussesNumber, MinGussesNumber, LevelInfo.Level);
            CheckUserFeedback(GetUserFeedback(ComputerGusses), Winner, ComputerGusses, MinGussesNumber, MaxGussesNumber);

            if (Winner != enWinner::draw) break; // لو الكمبيوتر حزر، اطلع فورا

            UserGusses = GetUserGusses(LevelInfo);
            CheckUserGusses(UserGusses, ComputerChoice, Winner);
        }
    }

    else
    {
        while (Winner == enWinner::draw)
        {
            RoundNumber++;

            cout << "Round [" << RoundNumber << "] : \n";

            UserGusses = GetUserGusses(LevelInfo);
            CheckUserGusses(UserGusses, ComputerChoice, Winner);

            if (Winner != enWinner::draw) break; // لو الكمبيوتر حزر، اطلع فورا

            PrintComputerGusses(ComputerGusses, MaxGussesNumber, MinGussesNumber, LevelInfo.Level);
            CheckUserFeedback(GetUserFeedback(ComputerGusses), Winner, ComputerGusses, MinGussesNumber, MaxGussesNumber);

        }
    }

    GameResult = FillGameResult(Winner, RoundNumber, ComputerChoice, UserChoice);

    return GameResult;
}

//لتحول ال enum ل string
string WinnerGameName(enWinner Winner)
{
    if (Winner == enWinner::player1)
        return "Player 1";
    else 
        return "Computer";
}

//لطباعة النتيجة النهائية على الشاشة
void PrintGameResults(stGameResults GameResult)
{
    cout << endl;

    cout << MyLip::Tabs(5) << "____________________________________________________\n\n";
    cout << MyLip::Tabs(5) << "                 [ GAME OVER RESULTS ]               \n";
    cout << MyLip::Tabs(5) << "____________________________________________________\n\n";
    cout << MyLip::Tabs(5) << " Total Rounds        : " << GameResult.RoundNumber << endl;
    cout << MyLip::Tabs(5) << " Player Secret Num   : " << GameResult.PlayerTargetNumber << endl;
    cout << MyLip::Tabs(5) << " Computer Secret Num : " << GameResult.ComputerTargetNumber << endl;
    cout << MyLip::Tabs(5) << " Final Winner        : " << WinnerGameName(GameResult.Winner) << endl;
    cout << MyLip::Tabs(5) << "____________________________________________________\n";
}

// يمثل هذا الشكل النهائي بعد تجميع الفنكشن داخل بعضها حتى نصل الى هذا الفنكشن الذي عند استدعائه يستدعي جميع الفنكشن المستخدمة 
//باللعبة و بالترتيب المناسب لتظهر اللعبة بالشكل المطلوب 
void StartGussesGame()
{
    char PlayAgain = 'Y';

    do
    {
        MyLip::ResetGame();

        stGameLevelInfo LevelInfo = GetGameLevelInfo();

        stGameResults GameResult = PlayRound(LevelInfo); // استلام النتيجة

        system("pause");
        MyLip::ResetGame();

        PrintGameResults(GameResult); //  طباعة شاشة النتائج الاحترافية

        cout << '\n' << MyLip::Tabs(5) << "Do you want to play again? (Y/N): ";
        cin >> PlayAgain;

        cout << endl;

    } while (PlayAgain == 'Y' || PlayAgain == 'y');
}

int main()
{
    srand(unsigned(time(NULL)));

    StartGussesGame();

    return 0;
}