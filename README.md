#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <Windows.h>

using namespace std;

struct Product {
    string name;
    float price;
    int quantity;
};

void displayCatalog(const vector<Product>& catalog) {
    for (size_t i = 0; i < catalog.size(); ++i) {
        cout << i + 1 << ". " << catalog[i].name << " - Цена: " << catalog[i].price << ", Количество: " << catalog[i].quantity << endl;
    }
}

void saveToFile(const vector<Product>& catalog, const string& filename) {
    ofstream outFile(filename);
    for (const auto& product : catalog) {
        outFile << product.name << " " << product.price << " " << product.quantity << endl;
    }
}

void addProduct(vector<Product>& catalog, const string& filename) {
    Product newProduct;
    cout << "Название товара: ";
    cin.ignore();
    getline(cin, newProduct.name);
    cout << "Цена товара: ";
    cin >> newProduct.price;
    cout << "Количество товара: ";
    cin >> newProduct.quantity;
    catalog.push_back(newProduct);


    saveToFile(catalog, filename);
    cout << "Товар добавлен и каталог обновлен.\n";
}

void buyProducts(vector<Product>& catalog) {
    if (catalog.empty()) {
        cout << "Каталог пуст. Нет товаров для покупки.\n";
        return;
    }

    char continueBuying = 'y';
    while (continueBuying == 'y' || continueBuying == 'Y') {
        displayCatalog(catalog);

        int choice, quantity;
        cout << "Введите номер товара, который хотите купить: ";
        cin >> choice;

        if (choice < 1 || choice > catalog.size()) {
            cout << "Неверный выбор!\n";
            continue;
        }

        cout << "Введите количество: ";
        cin >> quantity;

        if (quantity > catalog[choice - 1].quantity) {
            cout << "Недостаточно товара на складе!\n";
        }
        else {
            catalog[choice - 1].quantity -= quantity;
            cout << "Вы купили " << quantity << " шт. товара \"" << catalog[choice - 1].name << "\" на сумму " << quantity * catalog[choice - 1].price << "!\n";

            if (catalog[choice - 1].quantity == 0) {
                cout << "Товар \"" << catalog[choice - 1].name << "\" закончился и будет удалён из каталога.\n";
                catalog.erase(catalog.begin() + (choice - 1));
            }
        }

        cout << "Хотите купить ещё товары? (y/n): ";
        cin >> continueBuying;
    }
}

void loadFromFile(vector<Product>& catalog, const string& filename) {
    ifstream inFile(filename);
    Product product;
    while (inFile >> product.name >> product.price >> product.quantity) {
        catalog.push_back(product);
    }
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);
    vector<Product> catalog;
    const string filename = "catalog.txt";

    loadFromFile(catalog, filename);

    int choice;
    do {
        cout << "1. Показать каталог\n2. Добавить товар\n3. Купить товары\n4. Сохранить и выйти\nВаш выбор: ";
        cin >> choice;

        switch (choice) {
        case 1:
            displayCatalog(catalog);
            break;
        case 2:
            addProduct(catalog, filename);
            break;
        case 3:
            buyProducts(catalog);
            break;
        case 4:
            saveToFile(catalog, filename);
            cout << "Каталог сохранен. Выход.\n";
            break;
        default:
            cout << "Неверный выбор.\n";
        }
    } while (choice != 4);

    return 0;
}
