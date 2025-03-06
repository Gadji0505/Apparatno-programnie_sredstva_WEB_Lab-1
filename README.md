#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

// Функция сложения двух длинных чисел
vector<int> add(const vector<int>& a, const vector<int>& b, int base) {
    vector<int> result(max(a.size(), b.size()) + 1, 0);
    int carry = 0;

    size_t i = 0;
    while (i < a.size() || i < b.size() || carry) {
        int sum = carry;
        if (i < a.size()) sum += a[i];
        if (i < b.size()) sum += b[i];
        result[i] = sum % base;
        carry = sum / base;
        i++;
    }

    // Удаление ведущих нулей
    while (result.size() > 1 && result.back() == 0) {
        result.pop_back();
    }
    return result;
}

// Функция вычитания двух длинных чисел
vector<int> sub(const vector<int>& a, const vector<int>& b, int base) {
    vector<int> result;
    int borrow = 0;

    for (size_t i = 0; i < a.size(); ++i) {
        int sub = a[i] - borrow;
        if (i < b.size()) {
            sub -= b[i];
        }
        if (sub < 0) {
            sub += base;
            borrow = 1;
        } else {
            borrow = 0;
        }
        result.push_back(sub);
    }

    // Удаление ведущих нулей
    while (result.size() > 1 && result.back() == 0) {
        result.pop_back();
    }
    return result;
}

// Функция, которая возвращает целую часть деления на 10^k
vector<int> shift(const vector<int>& a, int k) {
    vector<int> result(a);
    result.insert(result.end(), k, 0);
    return result;
}

// Функция умножения с помощью алгоритма Карацубы
vector<int> karatsuba(const vector<int>& x, const vector<int>& y, int base) {
    // Базовый случай
    if (x.size() == 1 && y.size() == 1) {
        return vector<int>{x[0] * y[0]};
    }

    size_t n = max(x.size(), y.size());
    size_t half = n / 2;

    vector<int> x0(x.begin(), x.begin() + min(half, x.size()));
    vector<int> x1(x.begin() + min(half, x.size()), x.end());
    vector<int> y0(y.begin(), y.begin() + min(half, y.size()));
    vector<int> y1(y.begin() + min(half, y.size()), y.end());

    vector<int> z0 = karatsuba(x0, y0, base);
    vector<int> z1 = karatsuba(add(x0, x1, base), add(y0, y1, base), base);
    vector<int> z2 = karatsuba(x1, y1, base);

    vector<int> result = add(z0, shift(sub(z1, add(z0, z2, base), base), half), base);
    result = add(result, shift(z2, 2 * half), base);

    return result;
}

// Функция для вывода длинного числа
void printNumber(const vector<int>& number) {
    for (auto it = number.rbegin(); it != number.rend(); ++it) {
        cout << *it;
    }
    cout << endl;
}

// Основная функция для теста
int main() {
    // Пример: 12345 * 67890
    vector<int> number1 = {5, 4, 3, 2, 1}; // 12345
    vector<int> number2 = {0, 9, 8, 7, 6}; // 67890

    cout << "Number 1: ";
    printNumber(number1);

    cout << "Number 2: ";
    printNumber(number2);

    vector<int> product = karatsuba(number1, number2, 10);
    
    cout << "Product: ";
    printNumber(product);

    return 0;
}