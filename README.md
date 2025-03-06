#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <cmath>
#include <stdexcept>

using namespace std;

// Глобальный счетчик операций
int operation_count = 0;

// Функция для сложения двух длинных чисел
vector<int> add(const vector<int>& a, const vector<int>& b) {
    vector<int> result;
    int carry = 0;
    int max_len = max(a.size(), b.size());

    for (int i = 0; i < max_len || carry; ++i) {
        int digit_a = (i < a.size()) ? a[i] : 0;
        int digit_b = (i < b.size()) ? b[i] : 0;
        int sum = digit_a + digit_b + carry;
        carry = sum / 21;
        result.push_back(sum % 21);
        operation_count++; // Увеличиваем счетчик операций
    }

    return result;
}

// Функция для вычитания двух длинных чисел
vector<int> sub(const vector<int>& a, const vector<int>& b) {
    vector<int> result;
    int borrow = 0;

    for (int i = 0; i < a.size(); ++i) {
        int digit_a = a[i];
        int digit_b = (i < b.size()) ? b[i] : 0;
        int diff = digit_a - digit_b - borrow;

        if (diff < 0) {
            diff += 21;
            borrow = 1;
        } else {
            borrow = 0;
        }

        result.push_back(diff);
        operation_count++; // Увеличиваем счетчик операций
    }

    // Удаляем ведущие нули
    while (result.size() > 1 && result.back() == 0) {
        result.pop_back();
    }

    return result;
}

// Функция для сдвига длинного числа на shift разрядов
vector<int> sh(const vector<int>& a, int shift) {
    vector<int> result(shift, 0);
    result.insert(result.end(), a.begin(), a.end());
    operation_count += shift; // Увеличиваем счетчик операций
    return result;
}

// Рекурсивная функция умножения по алгоритму Карацубы
vector<int> mult(const vector<int>& a, const vector<int>& b) {
    int n = a.size();

    // Базовый случай: если длина числа <= 1, выполняем простое умножение
    if (n <= 1) {
        int product = a[0] * b[0];
        operation_count++; // Увеличиваем счетчик операций
        return {product % 21, product / 21};
    }

    // Если длина нечетная, добавляем ведущий ноль для выравнивания
    if (n % 2 != 0) {
        vector<int> a_padded = a;
        vector<int> b_padded = b;
        a_padded.insert(a_padded.begin(), 0);
        b_padded.insert(b_padded.begin(), 0);
        return mult(a_padded, b_padded);
    }

    int half = n / 2;

    // Разделяем числа на две части
    vector<int> a_low(a.begin(), a.begin() + half);
    vector<int> a_high(a.begin() + half, a.end());
    vector<int> b_low(b.begin(), b.begin() + half);
    vector<int> b_high(b.begin() + half, b.end());

    // Рекурсивно умножаем части
    vector<int> z0 = mult(a_low, b_low);
    vector<int> z2 = mult(a_high, b_high);
    vector<int> z1 = mult(add(a_low, a_high), add(b_low, b_high));
    z1 = sub(sub(z1, z0), z2);

    // Собираем результат
    vector<int> result = add(z0, sh(z1, half));
    result = add(result, sh(z2, 2 * half));

    return result;
}

// Функция для генерации тестовых длинных чисел
vector<int> generate_number(int n) {
    vector<int> number(n);
    for (int i = 0; i < n; ++i) {
        number[i] = rand() % 21; // Случайные цифры от 0 до 20
    }
    return number;
}

// Функция для тестирования
void test() {
    vector<int> n_values = {128, 256, 512, 1024, 2048, 4096, 8192, 16384};
    vector<int> operation_counts;

    for (int n : n_values) {
        operation_count = 0;
        vector<int> a = generate_number(n);
        vector<int> b = generate_number(n);
        try {
            vector<int> result = mult(a, b);
            operation_counts.push_back(operation_count);
            cout << "n = " << n << ", T(n) = " << operation_count << endl;
        } catch (const exception& e) {
            cerr << "Ошибка при умножении чисел длины " << n << ": " << e.what() << endl;
        }
    }

    // Построение графика T(n) (можно использовать внешние инструменты)
    cout << "График T(n):" << endl;
    for (int i = 0; i < n_values.size(); ++i) {
        cout << "n = " << n_values[i] << ", T(n) = " << operation_counts[i] << endl;
    }
}

int main() {
    srand(time(0)); // Инициализация генератора случайных чисел
    test(); // Запуск тестирования
    return 0;
}