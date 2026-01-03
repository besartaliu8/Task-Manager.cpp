src/main.cpp
#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <algorithm>
#include <limits>

struct Task {
    int id;
    std::string title;
};

class TaskManager {
public:
    void addTask(const std::string& title);
    void listTasks() const;
    void deleteTask(int id);
    void loadFromFile();
    void saveToFile() const;

private:
    std::vector<Task> tasks;
    void refreshIds();
};

void TaskManager::addTask(const std::string& title) {
    tasks.push_back({ static_cast<int>(tasks.size()) + 1, title });
    saveToFile();
    std::cout << "Detyra u shtua me sukses.\n";
}

void TaskManager::listTasks() const {
    if (tasks.empty()) {
        std::cout << "Nuk ka detyra te regjistruara.\n";
        return;
    }

    std::cout << "\n--- Lista e Detyrave ---\n";
    for (const auto& t : tasks) {
        std::cout << t.id << ". " << t.title << "\n";
    }
}

void TaskManager::deleteTask(int id) {
    auto it = std::remove_if(tasks.begin(), tasks.end(),
        [id](const Task& t) { return t.id == id; });

    if (it == tasks.end()) {
        std::cout << "Nuk u gjet detyre me kete ID.\n";
        return;
    }

    tasks.erase(it, tasks.end());
    refreshIds();
    saveToFile();
    std::cout << "Detyra u fshi me sukses.\n";
}

void TaskManager::refreshIds() {
    for (size_t i = 0; i < tasks.size(); ++i)
        tasks[i].id = static_cast<int>(i) + 1;
}

void TaskManager::loadFromFile() {
    tasks.clear();
    std::ifstream file("tasks.txt");

    if (!file) {
        std::cout << "Skedari tasks.txt nuk ekziston. Do krijohet automatikisht.\n";
        return;
    }

    std::string title;
    while (std::getline(file, title)) {
        if (!title.empty())
            tasks.push_back({ static_cast<int>(tasks.size()) + 1, title });
    }
}

void TaskManager::saveToFile() const {
    std::ofstream file("tasks.txt");
    for (const auto& t : tasks)
        file << t.title << "\n";
}

int main() {
    TaskManager tm;
    tm.loadFromFile();

    while (true) {
        std::cout << "\n--- Task Manager (Development Branch) ---\n";
        std::cout << "1 - Shto detyre\n";
        std::cout << "2 - Shfaq detyrat\n";
        std::cout << "3 - Fshi detyre\n";
        std::cout << "0 - Dil\n";
        std::cout << "Zgjedhja: ";

        int choice;
        std::cin >> choice;

        if (!std::cin) {
            std::cin.clear();
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
            std::cout << "Ju lutem shkruani numer.\n";
            continue;
        }

        if (choice == 0) break;

        if (choice == 1) {
            std::cin.ignore();
            std::string title;
            std::cout << "Titulli i detyres: ";
            std::getline(std::cin, title);

            if (title.empty()) {
                std::cout << "Titulli nuk mund te jete bosh.\n";
                continue;
            }

            tm.addTask(title);
        }
        else if (choice == 2) {
            tm.listTasks();
        }
        else if (choice == 3) {
            int id;
            std::cout << "Shkruaj ID e detyres: ";
            std::cin >> id;

            if (!std::cin || id <= 0) {
                std::cin.clear();
                std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
                std::cout << "ID e pavlefshme.\n";
                continue;
            }

            tm.deleteTask(id);
        }
        else {
            std::cout << "Opcioni i pasakte.\n";
        }
    }

    std::cout << "Programi u mbyll.\n";
    return 0;
}
