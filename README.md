#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <algorithm>
using namespace std;
struct Book {
    string name;
    string author;
    string genre;
    int totalCopies;
    int availableCopies;

    Book(const std::string& n, const std::string& a, const std::string& g, int copies)
        : name(n), author(a), genre(g), totalCopies(copies), availableCopies(copies) {}
};

struct User {
    string name;
    string regNo;
    string password;
    vector<std::string> issuedBooks;


    User(const std::string& n, const std::string& r, const std::string& p)
        : name(n), regNo(r), password(p) {}
};


class Library {
private:
    string librarianName;
    string librarianPassword;
    vector<Book> books;
    vector<User> users;


    bool isValidString(const string& str) {
        return all_of(str.begin(), str.end(), [](char c) {
            return isalpha(c) || isspace(c);
        });
    }


    bool authenticateLibrarian(const string& password) {
        return password == librarianPassword;
    }


    bool authenticateUser(const string& regNo, const string& password) {
        auto user = findUser(regNo);
        return user != nullptr && user->password == password;
    }

    User* findUser(const string& regNo) {
        for (auto& user : users) {
            if (user.regNo == regNo) return &user;
        }
        return nullptr;
    }

    Book* findBook(const string& name) {
        for (auto& book : books) {
            if (book.name == name) return &book;
        }
        return nullptr;
    }

    string getValidStringInput(const std::string& prompt) {
        string input;
        while (true) {
            cout << prompt;
            getline(std::cin, input);
            if (isValidString(input)) {
                return input;
            }
            cout << "Invalid input! Please use only letters and spaces.\n";
        }
    }

    int getValidIntegerInput(const std::string& prompt) {
        string input;
        while (true) {
            cout << prompt;
            getline(cin, input);
            if (isValidNumber(input)) {
                return stoi(input);
            }
            cout << "Invalid input! Please enter a valid number.\n";
        }
    }

public:

    static bool isValidNumber(const string& str) {
        return !str.empty() && all_of(str.begin(), str.end(), ::isdigit);
    }

    void setupLibrarian() {
        cout << "Create Librarian Account\n";
        librarianName = getValidStringInput("Enter name: ");
        cout << "Enter password: ";
        getline(cin, librarianPassword);
        cout << "Librarian account created successfully!\n";
    }


    void addUser() {
        string name = getValidStringInput("Enter name: ");
        string regNo;

        while (true) {
            cout << "Enter registration number: ";
            getline(cin, regNo);
            if (isValidNumber(regNo)) {
                break;
            }
            cout << "Invalid registration number! Please use only digits.\n";
        }

        if (findUser(regNo) != nullptr) {
            cout << "User already exists!\n";
            return;
        }


        cout << "Enter password: ";
        string password;
        getline(cin, password);
        users.push_back(User(name, regNo, password));
        cout << "User account created successfully!\n";
    }


    void librarianMenu() {
        string password;
        cout << "Enter librarian password: ";
        getline(cin, password);

        if (!authenticateLibrarian(password)) {
            cout << "Invalid password!\n";
            return;
        }


        while (true) {
            cout << "\nLibrarian Menu\n";
            cout << "1. Add Book\n2. Remove Book\n3. Modify Book\n4. Show Book Records\n5. Logout\n";
            cout << "Enter choice: ";

            int choice = getValidIntegerInput("");

            switch (choice) {
                case 1: {

                    string name = getValidStringInput("Enter book name: ");
                    string author = getValidStringInput("Enter author: ");
                    string genre = getValidStringInput("Enter genre: ");
                    int copies = getValidIntegerInput("Enter number of copies: ");
                    books.push_back(Book(name, author, genre, copies));
                    cout << "Book added successfully!\n";
                    break;
                }
                case 2: {
                    string name = getValidStringInput("Enter book name to remove: ");
                    auto it = remove_if(books.begin(), books.end(),
                        [&name](const Book& book) { return book.name == name; });
                    if (it != books.end()) {
                        books.erase(it, books.end());
                        cout << "Book removed successfully!\n";
                    } else {
                        cout << "Book not found!\n";
                    }
                    break;
                }
                case 3: {
                    std::string name = getValidStringInput("Enter book name to modify: ");
                    Book* book = findBook(name);
                    if (book) {
                        book->name = getValidStringInput("Enter new name: ");
                        book->author = getValidStringInput("Enter new author: ");
                        book->genre = getValidStringInput("Enter new genre: ");
                        book->totalCopies = getValidIntegerInput("Enter new total copies: ");
                        book->availableCopies = getValidIntegerInput("Enter new available copies: ");
                        cout << "Book modified successfully!\n";
                    } else {
                        cout << "Book not found!\n";
                    }
                    break;
                }
                case 4: {

                    cout << "\nBook Records:\n";
                    for (const auto& book : books) {
                        cout << "Name: " << book.name
                                << ", Author: " << book.author
                                << ", Genre: " << book.genre
                                << ", Total Copies: " << book.totalCopies
                                << ", Available: " << book.availableCopies << "\n";
                    }
                    break;
                }
                case 5:
                    return;
                default:
                    cout << "Invalid choice!\n";
            }
        }
    }

    void userMenu() {
        string regNo;
        while (true) {
            cout << "Enter registration number: ";
            getline(cin, regNo);
            if (isValidNumber(regNo)) break;
            cout << "Invalid registration number! Please use only digits.\n";
        }

        cout << "Enter password: ";
        string password;
        getline(cin, password);

        User* user = findUser(regNo);
        if (!user || !authenticateUser(regNo, password)) {
            cout << "Invalid credentials!\n";
            return;
        }

        while (true) {

            cout << "\nUser Menu - Welcome " << user->name << "!\n";
            cout << "Issued Books:\n";
            for (const auto& book : user->issuedBooks) {
                cout << "- " << book << "\n";
            }

            cout << "\n1. View All Books\n2. Search Book\n3. Issue Book\n4. Return Book\n5. Logout\n";
            cout << "Enter choice: ";

            int choice = getValidIntegerInput("");

            switch (choice) {
                case 1: {
                    cout << "\nAvailable Books:\n";
                    for (const auto& book : books) {
                        if (book.availableCopies > 0) {
                            cout << "Name: " << book.name
                                    << ", Author: " << book.author
                                    << ", Genre: " << book.genre
                                    << ", Available: " << book.availableCopies << "\n";
                        }
                    }
                    break;
                }
                case 2: {

                   string name = getValidStringInput("Enter book name: ");
                    Book* book = findBook(name);
                    if (book) {
                        cout << "Name: " << book->name
                                << ", Author: " << book->author
                                << ", Genre: " << book->genre
                                << ", Available: " << book->availableCopies << "\n";
                    } else {
                        cout << "Book not found!\n";
                    }
                    break;
                }
                case 3: {

                    string name = getValidStringInput("Enter book name to issue: ");
                    Book* book = findBook(name);
                    if (book && book->availableCopies > 0) {
                        book->availableCopies--;
                        user->issuedBooks.push_back(book->name);
                        cout << "Book issued successfully!\n";
                    } else {
                        cout << "Book not available!\n";
                    }
                    break;
                }
                case 4: {

                    string name = getValidStringInput("Enter book name to return: ");
                    auto it = find(user->issuedBooks.begin(), user->issuedBooks.end(), name);
                    if (it != user->issuedBooks.end()) {
                        Book* book = findBook(name);
                        if (book) {
                            book->availableCopies++;
                            user->issuedBooks.erase(it);
                           cout << "Book returned successfully!\n";
                        }
                    } else {
                        cout << "Book not found in your issued books!\n";
                    }
                    break;
                }
                case 5:
                    return;
                default:
                    cout << "Invalid choice!\n";
            }
        }
    }
};

int main() {
    Library library;
    library.setupLibrarian();

    while (true) {
        cout << "\nLibrary Management System\n";
        cout << "1. Librarian Login\n2. User Login\n3. New User Registration\n4. Exit\n";
        cout << "Enter choice: ";


        string input;
        getline(cin, input);
        if (!Library::isValidNumber(input)) {
            cout << "Invalid choice! Please enter a number.\n";
            continue;
        }
        int choice = stoi(input);


        switch (choice) {
            case 1:
                library.librarianMenu();
                break;
            case 2:
                library.userMenu();
                break;
            case 3:
                library.addUser();
                break;
            case 4:
                return 0;
            default:
                std::cout << "Invalid choice!\n";
        }
    }
    return 0;
}
