#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <Windows.h>

using namespace std;

// Структура для хранения информации о товаре
struct Product {
    string name; // Название товара
    float price; // Цена товара
    int quantity; // Количество товара
};

// Функция для отображения каталога товаров
void displayCatalog(const vector<Product>& catalog) {
    for (size_t i = 0; i < catalog.size(); ++i) {
        cout << i + 1 << ". " << catalog[i].name << " - Цена: " << catalog[i].price << ", Количество: " << catalog[i].quantity << endl;
    }
    cout << "-------------------------------------------" << endl;

}

// Функция для сохранения каталога в файл
void saveToFile(const vector<Product>& catalog, const string& filename) {
    ofstream outFile(filename);
    for (const auto& product : catalog) {
        outFile << product.name << " " << product.price << " " << product.quantity << endl;
    }
    cout << "-------------------------------------------" << endl;
}

// Функция для добавления нового товара в каталог
void addProduct(vector<Product>& catalog, const string& filename) {
    Product newProduct;
    cout << "Название товара: " << endl;
    cin.ignore(); // Очистка буфера ввода перед использованием getline
    getline(cin, newProduct.name); // Считывание названия товара
    cout << "Цена товара: " << endl;
    cin >> newProduct.price; // Ввод цены товара
    cout << "Количество товара: " << endl;
    cin >> newProduct.quantity; // Ввод количества товара
    catalog.push_back(newProduct); // Добавление товара в каталог

    saveToFile(catalog, filename); // Сохранение обновленного каталога в файл
    cout << "Товар добавлен и каталог обновлен." << endl;
    cout << "-------------------------------------------" << endl;
    system("cls"); // Очистка консоли (только для Windows)
}

// Функция для покупки товаров
void buyProducts(vector<Product>& catalog, const string& filename) {
    if (catalog.empty()) {
        cout << "Каталог пуст. Нет товаров для покупки." << endl;
        return;
    }

    char continueBuying = 'y'; // Переменная для продолжения покупок
    while (continueBuying == 'y' || continueBuying == 'Y') {
        displayCatalog(catalog);

        int choice, quantity;
        cout << "Введите номер товара, который хотите купить: " << endl;
        cin >> choice;

        if (choice < 1 || choice > catalog.size()) {
            cout << "Неверный выбор!" << endl;
            continue;
        }

        cout << "Введите количество: " << endl;
        cin >> quantity;

        if (quantity > catalog[choice - 1].quantity) {
            cout << "Недостаточно товара на складе!" << endl;
        }
        else {
            catalog[choice - 1].quantity -= quantity; // Уменьшаем количество купленного товара
            cout << "Вы купили " << quantity << " шт. товара \"" << catalog[choice - 1].name << "\" на сумму " << quantity * catalog[choice - 1].price << "!" << endl;

            // Если товар закончился, удаляем его из каталога
            if (catalog[choice - 1].quantity == 0) {
                cout << "Товар \"" << catalog[choice - 1].name << "\" закончился и будет удалён из каталога." << endl;
                catalog.erase(catalog.begin() + (choice - 1));
            }
            saveToFile(catalog, filename); // Сохраняем обновленный каталог в файл
        }

        cout << "Хотите купить ещё товары? (y/n): " << endl;
        cin >> continueBuying;
    }
}

// Функция для загрузки каталога из файла
void loadFromFile(vector<Product>& catalog, const string& filename) {
    ifstream inFile(filename);
    Product product;
    while (inFile >> product.name >> product.price >> product.quantity) {
        catalog.push_back(product);
    }
}

int main() {
    SetConsoleCP(1251); // Устанавливаем кодировку для ввода
    SetConsoleOutputCP(1251); // Устанавливаем кодировку для вывода
    vector<Product> catalog; // Вектор для хранения каталога товаров
    const string filename = "catalog.txt"; // Имя файла для хранения данных
    loadFromFile(catalog, filename); // Загружаем данные из файла при старте программы

    int choice;
    do {
        // Выводим меню на экран
        cout << "1. Показать каталог" << endl;
        cout << "2. Добавить товар" << endl;
        cout << "3. Купить товары" << endl;
        cout << "4. Сохранить и выйти" << endl;
        cout << "Ваш выбор: " << endl;
        cin >> choice;

        switch (choice) {
        case 1:
            displayCatalog(catalog); // Показать каталог
            break;
        case 2:
            addProduct(catalog, filename); // Добавить товар
            break;
        case 3:
            buyProducts(catalog, filename); // Купить товары
            break;
        case 4:
            saveToFile(catalog, filename); // Сохранить каталог и выйти
            cout << "Каталог сохранен. Выход." << endl;
            break;
        default:
            cout << "Неверный выбор." << endl;
        }
    } while (choice != 4);

    return 0;
}
