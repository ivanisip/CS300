# CS300

## Reflection

**What was the problem you were solving in the projects for this course?**  
I worked on organizing course data for an advising system.  
I needed a way to store courses, search through them, and display details quickly.  
My goal was to show course details and prerequisites in the right order.

**How did you approach the problem? Consider why data structures are important to understand.**  
I tried using different data structures like vectors, hash tables, and binary search trees.  
I tested each one to see which could search and sort data faster.  
This helped me learn that the right data structure makes a program work more efficiently.

**How did you overcome any roadblocks you encountered while going through the activities or project?**  
I broke the project into smaller parts and worked on one piece at a time.  
I tested my code with sample data to catch and fix errors quickly.  
This step-by-step approach helped me solve problems as they came up.

**How has your work on this project expanded your approach to designing software and developing programs?**  
I learned to plan my work before I start coding.  
I organized my tasks clearly, which made it easier to build a working system.  
This project showed me that taking small steps helps me design better software.

**How has your work on this project evolved the way you write programs that are maintainable, readable, and adaptable?**  
I now write code that is split into clear, small pieces.  
I use simple names for my variables and functions so the code is easy to follow.  
This approach makes it easier to update or fix my code in the future.

## Project One Analysis: Run-Time and Memory

**Data Structures**

- **Vector**
  - Run-time:
    - Search: O(n) – each course is checked.
    - Sort: O(n log n) – extra processing for ordering.
    - Add: O(1) – very fast insertion.
  - Memory:
    - Uses contiguous memory.
    - Minimal overhead per element.

- **Hash Table**
  - Run-time:
    - Average search: O(1) – very quick.
    - Worst-case search: O(n) – if many collisions occur.
    - Sorting for printing: O(n log n) – extra work needed.
  - Memory:
    - Extra space for the table.
    - Uses linked lists for handling collisions.

- **Binary Search Tree (BST)**
  - Run-time:
    - Search: O(log n) in balanced trees.
    - Print (in-order traversal): O(n).
    - Unbalanced trees can slow down operations.
  - Memory:
    - Uses nodes with left/right pointers.
    - Additional memory for pointer storage.

## Project Two Code: Sorting and Printing Courses

Below is the working C++ code that sorts and prints the courses in alphanumeric order.

```cpp
#include <iostream>
#include <fstream>
#include <sstream>
#include <string>
#include <vector>
using namespace std;

struct Course {
    string courseNumber;
    string title;
    vector<string> prerequisites;
};

struct Node {
    Course course;
    Node* left;
    Node* right;
    Node(Course c) {
        course = c;
        left = nullptr;
        right = nullptr;
    }
};

Node* root = nullptr;

string toUpperCase(const string& str) {
    string result = str;
    for (int i = 0; i < (int)result.size(); i++) {
        if (result[i] >= 'a' && result[i] <= 'z') {
            result[i] = result[i] - ('a' - 'A');
        }
    }
    return result;
}

Node* insertBST(Node* node, const Course& course) {
    if (node == nullptr) {
        Node* newNode = new Node(course);
        return newNode;
    }
    string current = toUpperCase(node->course.courseNumber);
    string incoming = toUpperCase(course.courseNumber);
    if (incoming < current) {
        node->left = insertBST(node->left, course);
    } else {
        node->right = insertBST(node->right, course);
    }
    return node;
}

void inOrderPrint(Node* node) {
    if (node == nullptr) {
        return;
    }
    inOrderPrint(node->left);
    cout << node->course.courseNumber << ", " << node->course.title << endl;
    inOrderPrint(node->right);
}

Node* searchBST(Node* node, const string& courseNum) {
    if (node == nullptr) {
        return nullptr;
    }
    string current = toUpperCase(node->course.courseNumber);
    string search = toUpperCase(courseNum);
    if (current == search) {
        return node;
    }
    else if (search < current) {
        return searchBST(node->left, courseNum);
    } else {
        return searchBST(node->right, courseNum);
    }
}

void destroyBST(Node* node) {
    if (node == nullptr) {
        return;
    }
    destroyBST(node->left);
    destroyBST(node->right);
    delete node;
}

void loadData() {
    cout << "Enter the course data file name: ";
    string fileName;
    cin >> fileName;
    ifstream file(fileName.c_str());
    if (!file.is_open()) {
        cout << "Error opening file." << endl;
        return;
    }
    destroyBST(root);
    root = nullptr;
    string line;
    while (getline(file, line)) {
        if (line.empty()) {
            continue;
        }
        vector<string> tokens;
        stringstream ss(line);
        string token;
        while (getline(ss, token, ',')) {
            tokens.push_back(token);
        }
        if (tokens.size() < 2) {
            continue;
        }
        Course course;
        course.courseNumber = tokens[0];
        course.title = tokens[1];
        for (int i = 2; i < (int)tokens.size(); i++) {
            course.prerequisites.push_back(tokens[i]);
        }
        root = insertBST(root, course);
    }
    file.close();
    cout << "Data loaded successfully." << endl;
}

void printCourseList() {
    if (root == nullptr) {
        cout << "Data not loaded yet. Please load data first." << endl;
        return;
    }
    cout << "Course List:" << endl;
    inOrderPrint(root);
}

void printCourseInfo() {
    if (root == nullptr) {
        cout << "Data not loaded yet. Please load data first." << endl;
        return;
    }
    cout << "What course do you want to know about? ";
    string courseNum;
    cin >> courseNum;
    Node* result = searchBST(root, courseNum);
    if (result == nullptr) {
        cout << "Course not found." << endl;
    } else {
        Course c = result->course;
        cout << c.courseNumber << ", " << c.title << endl;
        cout << "Prerequisites: ";
        if (c.prerequisites.empty()) {
            cout << "None";
        } else {
            for (int i = 0; i < (int)c.prerequisites.size(); i++) {
                cout << c.prerequisites[i];
                if (i < (int)c.prerequisites.size() - 1) {
                    cout << ", ";
                }
            }
        }
        cout << endl;
    }
}

int main() {
    int choice = 0;
    while (true) {
        cout << "\n1. Load Data Structure." << endl;
        cout << "2. Print Course List." << endl;
        cout << "3. Print Course." << endl;
        cout << "9. Exit" << endl;
        cout << "What would you like to do? ";
        cin >> choice;
        if (!cin.good()) {
            cin.clear();
            cin.ignore(1000, '\n');
            cout << "Invalid input. Please enter a number." << endl;
            continue;
        }
        if (choice == 1) {
            loadData();
        } else if (choice == 2) {
            printCourseList();
        } else if (choice == 3) {
            printCourseInfo();
        } else if (choice == 9) {
            cout << "Thank you for using the course planner!" << endl;
            break;
        } else {
            cout << choice << " is not a valid option." << endl;
        }
    }
    destroyBST(root);
    return 0;
}


