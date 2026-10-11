#pragma once

#include <iostream>
#include <string>
#include <cmath>
#include <iomanip>
#include <cstdlib>
#include <vector>

using namespace std;

namespace MyLip
{
    inline void Test()
	{
		cout << "Hello in My First Library!" << endl;
	}

    //====================================[Read Number in Range]============================================
    inline int ReadNumber(string Message, short From, short To)
    {
        int Number = 0;
        do
        {

            cout << Message;
            cin >> Number;

        } while (Number < From || Number > To);

        return Number;
    }

    //=======================================[Tabs]=========================================
    inline string Tabs(short Number)
    {
        string Tabs = "";
        for (short i = 0; i < Number; i++)
        {
            Tabs = Tabs + "\t";
        }

        return Tabs;
    }

    //====================================[Get Random Number in Range]=================================
    inline int RandomNumber(short From, short To)
    {
        return rand() % (To - From + 1) + From;
    }

    //====================================[Reset Game]=================================
    inline void ResetGame()
    {
        system("cls");
        system("color 0F");
    }

    //====================================[Print Header For Program]=================================
    inline void PrintHeader(short Tab,string Message)
    {
        cout << "=====================================================\n";
        cout << Tabs(Tab)         << Message <<                      '\n';
        cout << "=====================================================\n";
    }
}