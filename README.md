#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <algorithm>

// Глобальный счетчик операций
int operation_count = 0;

// Функция для разделения массива
int partition(std::vector<int>& arr, int low, int high) {
    int pivot = arr[high]; // Опорный элемент
    int i = low - 1;

    for (int j = low; j < high; j++) {
        operation_count++; // Счетчик операций
        if (arr[j] < pivot) {
            i++;
            std::swap(arr[i], arr[j]);
        }
    }
    std::swap(arr[i + 1], arr[high]);
    return i + 1;
}

// Быстрая сортировка
void quickSort(std::vector<int>& arr, int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

// Функция для генерации случайного массива
std::vector<int> generateRandomArray(int n) {
    std::vector<int> arr(n);
    for (int i = 0; i < n; i++) {
        arr[i] = rand() % 10000; // Случайные числа от 0 до 9999
    }
    return arr;
}

// Функция для генерации наихудшего случая (уже отсортированный массив)
std::vector<int> generateWorstCaseArray(int n) {
    std::vector<int> arr(n);
    for (int i = 0; i < n; i++) {
        arr[i] = i;
    }
    return arr;
}

// Функция для генерации наилучшего случая (массив, который делится на равные части)
std::vector<int> generateBestCaseArray(int n) {
    std::vector<int> arr(n);
    for (int i = 0; i < n; i++) {
        arr[i] = i;
    }
    std::random_shuffle(arr.begin(), arr.end());
    return arr;
}

int main() {
    srand(time(0));
    const int num_experiments = 1000;
    const int step = 100;
    const int max_n = 10000;

    for (int n = 100; n <= max_n; n += step) {
        long long total_operations = 0;
        int min_operations = INT_MAX;
        int max_operations = 0;

        for (int k = 0; k < num_experiments; k++) {
            operation_count = 0;
            std::vector<int> arr = generateRandomArray(n);
            quickSort(arr, 0, n - 1);
            total_operations += operation_count;
            min_operations = std::min(min_operations, operation_count);
            max_operations = std::max(max_operations, operation_count);
        }

        double mean_operations = static_cast<double>(total_operations) / num_experiments;

        std::cout << "n = " << n << ", Tmin(n) = " << min_operations
                  << ", Tmean(n) = " << mean_operations
                  << ", Tmax(n) = " << max_operations << std::endl;
    }

    return 0;
}