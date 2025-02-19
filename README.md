<h1 align="center">BaseMaster</h1>
BaseMaster - Калькулятор систем счисления
Программа BaseMaster представляет собой калькулятор, который позволяет выполнять арифметические и математические операции с числами, представленными в различных системах счисления (от двоичной до 36-ричной). Она поддерживает как целые, так и дробные числа, а также предоставляет возможность выбора системы счисления для ввода чисел и вывода результата. Программа полезна для студентов, программистов и всех, кто работает с различными системами счисления.  


**Как работает программа:**
1) Пользователь вводит два числа (или одно для унарных операций).
2) Для каждого числа выбирается система счисления (от 2 до 36).
3) Выбирается операция (например, сложение, вычитание, квадратный корень и т.д.).
4) Выбирается система счисления для результата.
5) Программа переводит введенные числа из указанных систем счисления в десятичную с помощью функции translate_to_dec.
6) Программа выполняет выбранную операцию с помощью функции performOperation.
7) Результат операции переводится из десятичной системы в выбранную пользователем систему счисления с помощью функции translate_to.
8) Программа выводит результат в формате: число1(система1) операция число2(система2) = результат(система_результата).



| Операция      | Описание           |
| ------------- |:------------------:|
|+|Сложение|
|-|Вычитание|
|*|Умножение|
|/|Деление|
|^|Возведение в степень (pow(num1, num2))|
|%|Остаток от деления (fmod(num1, num2)). Используется fmod, так как он работает с числами типа double|
|s|Квадратный корень (sqrt(num1)). Использует только num1|
|l|Натуральный логарифм (log(num1)). Использует только num1|
|L|Десятичный логарифм (log10(num1)). Использует только num1|
|a|Модуль числа (abs(num1)). Использует только num1|
|r|Округление до ближайшего целого (round(num1)). Использует только num1|
|f|Округление вниз (floor(num1)). Использует только num1|
|c|Округление вверх (ceil(num1)). Использует только num1|    

**Пример использования:**
```
int main() {
    try {
        cout << "2 ^ 3 = " << performOperation(2, 3, '^') << endl; // 8
        cout << "10 % 3 = " << performOperation(10, 3, '%') << endl; // 1
        cout << "sqrt(16) = " << performOperation(16, 0, 's') << endl; // 4
        cout << "log(100) = " << performOperation(100, 0, 'L') << endl; // 2
        cout << "abs(-5) = " << performOperation(-5, 0, 'a') << endl; // 5
        cout << "round(3.6) = " << performOperation(3.6, 0, 'r') << endl; // 4
    } catch (const runtime_error& e) {
        cout << e.what() << endl;
    }
    return 0;
}
```




**Листинг программы**
```
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

// Функция для выполнения арифметических и математических операций
double performOperation(double num1, double num2, char operation) {
    switch (operation) {
    case '+': return num1 + num2; // Сложение
    case '-': return num1 - num2; // Вычитание
    case '*': return num1 * num2; // Умножение
    case '/': // Деление
        if (num2 == 0) throw runtime_error("Ошибка: Деление на ноль!");
        return num1 / num2;
    case '^': return pow(num1, num2); // Возведение в степень
    case '%': // Остаток от деления
        if (num2 == 0) throw runtime_error("Ошибка: Деление на ноль!");
        return fmod(num1, num2); // fmod для работы с double
    case 's': return sqrt(num1); // Квадратный корень (использует только num1)
    case 'l': return log(num1); // Натуральный логарифм (использует только num1)
    case 'L': return log10(num1); // Десятичный логарифм (использует только num1)
    case 'a': return abs(num1); // Модуль числа (использует только num1)
    case 'r': return round(num1); // Округление до ближайшего целого (использует только num1)
    case 'f': return floor(num1); // Округление вниз (использует только num1)
    case 'c': return ceil(num1); // Округление вверх (использует только num1)
    default:
        throw runtime_error("Некорректная операция!");
    }
}

// Функция для перевода числа из любой системы счисления в десятичную
double translate_to_dec(const char* number, int from_) {
    double decimal = 0.0; // Переменная для хранения десятичного значения
    bool isNegative = false; // Флаг для отрицательных чисел

    if (number[0] == '-') {
        isNegative = true;
        number++;
    }

    if (from_ < 2 || from_ > 36) throw runtime_error("Некорректная система счисления. Должна быть от 2 до 36.");

    int i = 0; // Индекс символа
    bool isFraction = false; // Флаг для дробной части
    int fractionalBase = 1; // Основание для дробной части

    while (number[i] != '\0') {
        if (number[i] == '.') {
            isFraction = true;
            i++;
            continue;
        }
        int digitValue;

        // Определяем значение символа
        if (number[i] >= '0' && number[i] <= '9') digitValue = number[i] - '0';
        else if (number[i] >= 'A' && number[i] <= 'Z') digitValue = number[i] - 'A' + 10;
        else if (number[i] >= 'a' && number[i] <= 'z') digitValue = number[i] - 'a' + 10;
        else throw runtime_error("Некорректный символ: " + string(1, number[i]));

        // Проверка на допустимость цифры в данной системе счисления
        if (digitValue >= from_) throw runtime_error("Некорректная цифра для системы " + to_string(from_) + ": " + string(1, number[i]));

        // Обработка дробной части
        if (isFraction) decimal += digitValue / pow(from_, fractionalBase++); // Используем степень основания
        else decimal = decimal * from_ + digitValue;
        i++;
    }
    if (isNegative) decimal = -decimal;
    return decimal;
}

// Функция для перевода числа из 10-ой системы счисления в другую
string translate_to(double decimal, int to_) {
    bool isNegative = (decimal < 0);
    decimal = abs(decimal);

    int intPart = static_cast<int>(decimal); // Целая часть
    double floatPart = decimal - intPart; // Дробная часть

    string resultInt; // Результат целой части
    string resultFloat; // Результат дробной части

    // Перевод целой части
    if (intPart == 0) resultInt = "0"; // Если целая часть равна 0
    else {
        while (intPart > 0) {
            int remainder = intPart % to_;
            if (remainder < 10) resultInt = to_string(remainder) + resultInt; // < 10
            else resultInt = char('A' + (remainder - 10)) + resultInt; // >= 10
            intPart /= to_;
        }
    }

    // Перевод дробной части
    while (floatPart > 0 && resultFloat.length() < 10) {
        floatPart *= to_; // Умножаем на основание целевой системы
        int digit = static_cast<int>(floatPart); // Получаем целую часть дробной
        if (digit < 10) resultFloat += to_string(digit); // < 10
        else resultFloat += char('A' + (digit - 10)); // >= 10
        floatPart -= digit;
    }

    if (isNegative) return "-" + resultInt + (!resultFloat.empty() ? "." + resultFloat : "");
    return resultInt + (!resultFloat.empty() ? "." + resultFloat : "");
}

int main() {
    setlocale(LC_ALL, "rus");

    string num1, num2;
    int s_num1, s_num2, s_result;
    char operation;

    cout << "=== Калькулятор систем счисления ===\n";
    cout << "Введите 'q' для выхода.\n";

    while (true) {
        cout << "Первое число: ";
        cin >> num1;
        if (num1 == "q") break; // Выход из программы

        cout << "Система счисления первого числа (2-36): ";
        cin >> s_num1;
        if (s_num1 < 2 || s_num1 > 36) {
            cout << "Некорректная система счисления. Должна быть от 2 до 36.\n";
            continue;
        }

        cout << "Операция (+, -, *, /, ^, %, s, l, L, a, r, f, c): ";
        cin >> operation;

        // Для операций, которые используют только одно число (например, квадратный корень)
        bool isUnaryOperation = (operation == 's' || operation == 'l' || operation == 'L' || operation == 'a' || operation == 'r' || operation == 'f' || operation == 'c');

        if (!isUnaryOperation) {
            cout << "Второе число: ";
            cin >> num2;
            if (num2 == "q") break; // Выход из программы

            cout << "Система счисления второго числа (2-36): ";
            cin >> s_num2;
            if (s_num2 < 2 || s_num2 > 36) {
                cout << "Некорректная система счисления. Должна быть от 2 до 36.\n";
                continue;
            }
        }

        cout << "Система счисления результата (2-36): ";
        cin >> s_result;
        if (s_result < 2 || s_result > 36) {
            cout << "Некорректная система счисления. Должна быть от 2 до 36.\n";
            continue;
        }

        try {
            double result;
            if (isUnaryOperation) result = performOperation(translate_to_dec(num1.c_str(), s_num1), 0, operation);
            else result = performOperation(translate_to_dec(num1.c_str(), s_num1), translate_to_dec(num2.c_str(), s_num2), operation);
            string resultStr = translate_to(result, s_result);
            cout << "\n" << num1 << "(" << s_num1 << ") " << operation << " " << (isUnaryOperation ? "" : num2 + "(" + to_string(s_num2) + ")") << " = " << resultStr << "(" << s_result << ")\n\n";
        }
        catch (const runtime_error& e) cout << e.what() << endl;
    }

    cout << "Программа завершена.\n";
    return 0;
}
```



**Пример работы программы:**
```
=== Калькулятор систем счисления ===
Первое число: 101
Система счисления первого числа: 2
Операция (+, -, *, /): +
Второе число: 110
Система счисления второго числа: 2
Система счисления результата: 10

101(2) + 110(2) = 11(10)
```

 



