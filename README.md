<h1 align="center">BaseMaster</h1>
BaseMaster - Калькулятор систем счисления
Программа-калькулятор для выполнения арифметических операций с числами в различных системах счисления (от 2 до 36).

**Возможности:**
Ввод двух чисел с выбором системы счисления для каждого.
Выполнение операций: сложение, вычитание, умножение, деление.
Вывод результата в выбранной системе счисления.
Поддержка целых и дробных чисел.
Контроль корректности ввода и обработка ошибок (недопустимые символы, деление на ноль и др.).

**Для кого:**
Программа полезна для студентов, программистов и всех, кто работает с различными системами счисления.
Использование:
Введите два числа, выбрав для каждого систему счисления.
Выберите операцию и систему счисления для результата.
Получите результат с учетом выбранных параметров.


**Листинг программы:**
```
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

// Функция для выполнения арифметических операций
double performOperation(double num1, double num2, char operation) {
    switch (operation) {
    case '+': return num1 + num2;
    case '-': return num1 - num2;
    case '*': return num1 * num2;
    case '/':
        if (num2 == 0) throw runtime_error("Ошибка: Деление на ноль!");
        return num1 / num2;
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

    if (from_ < 2 || from_ > 36) {
        cout << "Некорректная система счисления. Должна быть от 2 до 36." << endl;
        return 0;
    }

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
        else {
            cout << "Некорректный символ: " << number[i] << endl;
            return 0;
        }

        // Проверка на допустимость цифры в данной системе счисления
        if (digitValue >= from_) {
            cout << "Некорректная цифра для системы " << from_ << ": " << number[i] << endl;
            return 0;
        }

        // Обработка дробной части
        if (isFraction) decimal += digitValue / static_cast<double>(1 << (fractionalBase++)); // Используем битовый сдвиг для деления
        else decimal = decimal * from_ + digitValue;
        i++;
    }
    if (isNegative) decimal = -decimal;
    return decimal;
}

// Функция для перевода числа из 10ой системы счисления в другую
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
            if (remainder < 10) resultInt += to_string(remainder); // < 10ой
            else resultInt = char('A' + (remainder - 10)) + resultInt; // > 10ой
            intPart /= to_; 
        }
    }

    // Перевод дробной части
    while (floatPart > 0 && resultFloat.length() < 10) {
        floatPart *= to_; // Умножаем на основание целевой системы
        int digit = static_cast<int>(floatPart); // Получаем целую часть дробной
        if (digit < 10) resultFloat += to_string(digit); // < 10ой
        else resultFloat += char('A' + (digit - 10)); // > 10ой
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

    while (true) {
        cout << "Первое число: ";
        cin >> num1;
        cout << "Система счисления первого числа: ";
        cin >> s_num1;

        cout << "Операция (+, -, *, /): ";
        cin >> operation;

        cout << "Второе число: ";
        cin >> num2;
        cout << "Система счисления второго числа: ";
        cin >> s_num2;

        cout << "Система счисления результата: ";
        cin >> s_result;

        try {
            double result = performOperation(translate_to_dec(num1.c_str(), s_num1), translate_to_dec(num2.c_str(), s_num2), operation);
            string resultStr = translate_to(result, s_result);
            cout << "\n" << num1 << "(" << s_num1 << ") " << operation << " " << num2 << "(" << s_num2 << ") = " << resultStr << "(" << s_result << ")\n\n";
        }
        catch (const runtime_error& e) {
            cout << e.what() << endl;
        }
    }
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


**Основные компоненты программы:**
Функция performOperation:
Выполняет арифметические операции (+, -, *, /) над двумя числами.
Обрабатывает ошибку деления на ноль.

Функция translate_to_dec:
Переводит число из любой системы счисления (от 2 до 36) в десятичную.
Поддерживает отрицательные числа и дробные части.
Проверяет корректность введенных символов и системы счисления.

Функция translate_to:
Переводит число из десятичной системы в указанную систему счисления (от 2 до 36).
Поддерживает целые и дробные числа.
Ограничивает длину дробной части до 10 символов.

Основная функция main:
Организует взаимодействие с пользователем: ввод чисел, систем счисления и операции.
Выводит результат в выбранной системе счисления.
Обрабатывает исключения и выводит сообщения об ошибках.






