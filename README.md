# Здесь представлен код моей лабы по Си (будет обновляться)

----

## Сам код

```
c
#include <stdio.h>      // Подключаем библиотеку для ввода-вывода
#include <stdlib.h>     // Подключаем библиотеку для работы с памятью
#include <string.h>     // Подключаем библиотеку для работы со строками
#include <locale.h>     // Подключаем библиотеку для установки локали

// Структура для узла студента
typedef struct Student {
    char name[50]; // Имя студента (массив из 50 символов)
    int grade;     // Оценка студента
    struct Student* next; // Указатель на следующий узел
} Student;

// Структура для односвязного списка
typedef struct SinglyLinkedList {
    Student* head; // Указатель на первый узел списка
} SinglyLinkedList;

// Функция для создания пустого списка
SinglyLinkedList* createList() {
    SinglyLinkedList* list = (SinglyLinkedList*)malloc(sizeof(SinglyLinkedList)); // Выделяем память под список
    list->head = NULL; // Инициализируем голову пустым указателем
    return list; // Возвращаем созданный список
}

// Проверка, пуст ли список
int isEmpty(SinglyLinkedList* list) {
    return list->head == NULL; // Возвращает 1, если голова равна NULL (список пуст)
}

// Функция для добавления студента в конец списка
void insertAtTail(SinglyLinkedList* list, const char* name, int grade) {
    Student* newStudent = (Student*)malloc(sizeof(Student)); // Создаем новый узел
    strcpy(newStudent->name, name); // Копируем имя студента в новый узел
    newStudent->grade = grade; // Устанавливаем оценку студента
    
    newStudent->next = newStudent; // Указатель на сам себя, для цикла

    if (isEmpty(list)) { // Если список пуст
        list->head = newStudent; // Новый узел становится головой списка
    } else {
        Student* temp = list->head; // Временная переменная для обхода списка
        
        // Находим последний узел
        while (temp->next != list->head) {
            temp = temp->next; // Переходим к следующему узлу
        }
        
        temp->next = newStudent; // Последний узел указывает на новый узел
    }
    
    newStudent->next = list->head; // Новый узел указывает на голову, создавая циклическую связь
}

// Функция для добавления студента в начало списка
void insertAtHead(SinglyLinkedList* list, const char* name, int grade) {
    Student* newStudent = (Student*)malloc(sizeof(Student)); // Создаем новый узел
    strcpy(newStudent->name, name); // Копируем имя
    newStudent->grade = grade; // Устанавливаем оценку

    newStudent->next = list->head; // Новый узел указывает на текущую голову списка
    list->head = newStudent; // Обновляем голову списка на новый узел
}

// Удаление узла с головы списка
void deleteFromHead(SinglyLinkedList* list) {
    if (isEmpty(list)) { // Проверяем, пуст ли список
        printf("Список пуст, нечего удалять.\n"); // Если пуст, выводим сообщение
        return; // Выходим из функции
    }

    Student* toDelete = list->head; // Сохраняем узел для удаления
    list->head = toDelete->next; // Обновляем голову списка
    free(toDelete); // Освобождаем память под удаляемый узел
}

// Удаление узла с конца списка
void deleteFromTail(SinglyLinkedList* list) {
    if (isEmpty(list)) { // Проверяем, пуст ли список
        printf("Список пуст, нечего удалять.\n");
        return; // Выходим из функции
    }

    // Если узел единственный в списке
    if (list->head->next == list->head) {
        free(list->head); // Освобождаем память под единственный узел
        list->head = NULL; // Теперь список пуст
        return;
    }

    Student* temp = list->head; // Временная переменная для обхода
    while (temp->next->next != list->head) { // Ищем предпоследний узел
        temp = temp->next; // Переходим к следующему узлу
    }
    
    free(temp->next); // Освобождаем память под последний узел
    temp->next = list->head; // Подключаем предпоследний узел к голове
}

// Удаление узла из указанной позиции
void deleteFromPosition(SinglyLinkedList* list, int position) {
    if (isEmpty(list)) { // Проверяем, пуст ли список
        printf("Список пуст, нечего удалять.\n");
        return; // Выходим из функции
    }

    if (position == 0) { // Если нужно удалить узел с головы
        deleteFromHead(list);
        return;
    }

    Student* temp = list->head; // Временная переменная для обхода
    // Находим узел перед позицией
    for (int i = 0; i < position - 1; i++) {
        temp = temp->next; // Переходим к следующему узлу
        if (temp == NULL || temp->next == NULL) { // Проверяем, не вышли ли за границы списка
            printf("Позиция превышает длину списка.\n");
            return; // Выходим из функции
        }
    }

    Student* toDelete = temp->next; // Узел, который нужно удалить
    temp->next = toDelete->next; // Пропускаем узел для удаления
    free(toDelete); // Освобождаем память
}

// Получение студента по индексу
void getStudentByIndex(SinglyLinkedList* list, int index) {
    if (isEmpty(list)) { // Проверяем, пуст ли список
        printf("Список пуст.\n");
        return; // Выходим из функции
    }

    Student* temp = list->head; // Временная переменная для обхода
    int currentIndex = 0; // Индекс для отслеживания позиции

    // Проходим по списку, пока не вернемся к голове
    do {
        if (currentIndex == index) { // Если нашли нужный индекс
            printf("Студент по индексу %d: Имя: %s, Оценка: %d\n", index, temp->name, temp->grade);
            return; // Выходим из функции
        }
        temp = temp->next; // Переходим к следующему узлу
        currentIndex++; // Увеличиваем текущий индекс
    } while (temp != list->head); // Пока не вернулись к голове

    printf("Индекс %d выходит за пределы.\n", index); // Если индекс выходит за границы
}

// Заполнение списка студентов вручную
void fillStudentList(SinglyLinkedList* list) {
    int n; // Количество студентов
    char name[50]; // Имя студента
    int grade; // Оценка студента

    while (1) { // Бесконечный цикл для повторения ввода
        printf("Введите количество студентов: ");
        if (scanf("%d", &n) == 1 && n > 0) { // Проверка, что ввод корректный
            break;
        } else {
            printf("ОШИБКА. Пожалуйста, введите корректное число.\n");
            while(getchar() != '\n'); // Очистка ввода
        }
    }

    for (int i = 0; i < n; i++) {
        printf("Введите имя студента %d: ", i + 1);
        scanf("%s", name); // Считываем имя студента

        printf("Введите оценку студента %d (0-5): ", i + 1);
        while (1) {
            if (scanf("%d", &grade) == 1 && grade >= 0 && grade <= 5) { // Проверка оценки
                break;
            } else {
                printf("ОШИБКА. Пожалуйста, введите корректную оценку (0-5).\n");
                while(getchar() != '\n'); // Очистка ввода
            }
        }
        insertAtTail(list, name, grade); // Добавляем студента в конец списка
    }
}

// Заполнение списка из файла
void fillListFromFile(SinglyLinkedList* list, const char* filename) {
    FILE* file = fopen(filename, "r"); // Открываем файл для чтения
    if (!file) {
        printf("Ошибка открытия файла!\n"); // Если не удалось открыть файл
        return; // Выходим из функции
    }

    char name[50]; // Имя студента
    int grade; // Оценка студента
    // Читаем данные из файла
    while (fscanf(file, "%49s %d", name, &grade) != EOF) {
        insertAtTail(list, name, grade); // Добавляем студентов в список
    }

    fclose(file); // Закрываем файл
}

// Вывод списка студентов
void displayList(SinglyLinkedList* list) {
    if (isEmpty(list)) { // Проверяем, пуст ли список
        printf("Список пуст.\n");
        return; // Выходим из функции
    }

    Student* temp = list->head; // Временная переменная для обхода
    int index = 0; // Индекс для отслеживания позиции
    printf("Список студентов:\n");
    do { // Используем do-while для циклического обхода
        printf("Индекс %d: Имя: %s, Оценка: %d\n", index, temp->name, temp->grade);
        temp = temp->next; // Переходим к следующему узлу
        index++; // Увеличиваем индекс
    } while (temp != list->head); // Пока не вернулись к голове
}

// Вывод отличников и добавление их в список отличников
void displayHonors(SinglyLinkedList* list, SinglyLinkedList* honorsList) {
    if (isEmpty(list)) { // Проверяем, пуст ли список
        printf("Список пуст.\n");
        return; // Выходим из функции
    }

    Student* temp = list->head; // Временная переменная для обхода
    do {
        if (temp->grade == 5) { // Если оценка студента равна 5 (отличник)
            insertAtTail(honorsList, temp->name, temp->grade); // Добавляем отличника в список отличников
        }
        temp = temp->next; // Переходим к следующему узлу
    } while (temp != list->head);
}

// Удаление списка и освобождение памяти
void deleteList(SinglyLinkedList* list) {
    if (isEmpty(list)) return; // Если список пуст, выходим из функции

    Student* current = list->head; // Текущий узел
    Student* nextNode; // Следующий узел

    // Освобождаем память для каждого узла
    do {
        nextNode = current->next; // Запоминаем следующий узел
        free(current); // Освобождаем текущий узел
        current = nextNode; // Переходим к следующему узлу
    } while (current != list->head); // Пока не вернулись к голове

    free(list); // Освобождаем память для самого списка
}

// Главная функция
int main() {
    setlocale(LC_ALL, "RUS"); // Устанавливаем локаль для поддержки русских символов
    SinglyLinkedList* group1 = createList(); // Создаем первый список студентов
    SinglyLinkedList* group2 = createList(); // Создаем второй список студентов
    SinglyLinkedList* honorStudents = createList();  // Создаем список для отличников
    int choice; // Выбор пользователя

    // Заполнение первого списка студентов
    printf("Заполните первый список студентов:\n");
    printf("Введите 1 для ручного ввода\nВведите 2 для чтения из файла\n");
    printf("Ваш выбор: ");

    while (1) { // Бесконечный цикл для выбора
        if (scanf("%d", &choice) == 1 && (choice == 1 || choice == 2)) {
            break;
        } else {
            printf("ОШИБКА. Пожалуйста, введите 1 или 2.\n");
            while(getchar() != '\n'); // Очистка ввода
        }
    }

    if (choice == 1) {
        fillStudentList(group1); // Заполнение вручную
    } else {
        char filename[100]; // Имя файла для чтения
        printf("Введите имя файла: ");
        scanf("%s", filename); // Считываем имя файла
        fillListFromFile(group1, filename); // Заполнение из файла
    }

    // Вывод студентов из первого списка
    displayList(group1);

    // Заполнение второго списка студентов
    printf("\nЗаполните второй список студентов:\n");
    printf("Введите 1 для ручного ввода\nВведите 2 для чтения из файла\n");
    printf("Ваш выбор: ");

    while (1) { // Бесконечный цикл для выбора
        if (scanf("%d", &choice) == 1 && (choice == 1 || choice == 2)) {
            break;
        } else {
            printf("ОШИБКА. Пожалуйста, введите 1 или 2.\n");
            while(getchar() != '\n'); // Очистка ввода
        }
    }

    if (choice == 1) {
        fillStudentList(group2); // Заполнение вручную
    } else {
        char filename[100]; // Имя файла для чтения
        printf("Введите имя файла: ");
        scanf("%s", filename); // Считываем имя файла
        fillListFromFile(group2, filename); // Заполнение из файла
    }

    // Вывод студентов из второго списка
    displayList(group2);

    // Заполнение студентов в списке
    while (1) { // Бесконечный цикл для добавления студентов
        int listChoice = 0; // Переменная для выбора списка
        int position; // Позиция для добавления

        while (1) {
            printf("\nВ какой список вы хотите добавить студента? (1 - первый список, 2 - второй список, 0 - не добавлять): ");
            if (scanf("%d", &listChoice) == 1 && (listChoice == 0 || listChoice == 1 || listChoice == 2)) {
                break;
            } else {
                printf("ОШИБКА. Пожалуйста, введите 0, 1 или 2.\n");
                while(getchar() != '\n'); // Очистка ввода
            }
        }

        if (listChoice == 0) {
            printf("Выход из режима добавления студентов.\n");
            break; // Выходим из цикла
        }

        char name[50]; // Имя студента
        int grade; // Оценка студента

        printf("Введите имя студента, которого хотите добавить: ");
        scanf("%s", name); // Считываем имя студента

        printf("Введите оценку студента (0-5): ");
        while (1) {
            if (scanf("%d", &grade) == 1 && grade >= 0 && grade <= 5) { // Проверка оценки
                break;
            } else {
                printf("ОШИБКА. Пожалуйста, введите корректную оценку (0-5).\n");
                while(getchar() != '\n'); // Очистка ввода
            }
        }

        // Проверка позиции для добавления студента
        printf("Выберите позицию: (0 - голова, 2 - хвост, 1 - случайное место): ");
        while (1) {
            if (scanf("%d", &position) == 1 && (position == 0 || position == 1 || position == 2)) {
                break;
            } else {
                printf("ОШИБКА. Пожалуйста, введите 0, 1 или 2.\n");
                while(getchar() != '\n'); // Очистка ввода
            }
        }

        // Добавление студента в первый список
        if (listChoice == 1) {
            if (position == 2) {
                insertAtTail(group1, name, grade); // Добавляем в конец
            } else if (position == 0) {
                insertAtHead(group1, name, grade); // Добавляем в начало
            } else {
                insertAtTail(group1, name, grade); // По умолчанию добавляем в конец
            }

            // Вывод обновленного списка после добавления
            printf("Обновленный первый список после добавления студента:\n");
            displayList(group1);
        } 
        // Добавление студента во второй список
        else if (listChoice == 2) {
            if (position == 2) {
                insertAtTail(group2, name, grade); // Добавляем в конец
            } else if (position == 0) {
                insertAtHead(group2, name, grade); // Добавляем в начало
            } else {
                insertAtTail(group2, name, grade); // По умолчанию добавляем в конец
            }

            // Вывод обновленного списка после добавления
            printf("Обновленный второй список после добавления студента:\n");
            displayList(group2);
        } 
        // Неправильный выбор
        else {
            printf("Неверный выбор! Пожалуйста, выберите 1 или 2.\n");
        }
    }

    // Запрос на удаление студентов
    while (1) { // Бесконечный цикл для удаления студентов
        int listChoice = 0; // Переменная для выбора списка

        while (1) {
            printf("\nИз какого списка вы хотите удалить студента? (1 - первый список, 2 - второй список, 0 - не удалять): ");
            if (scanf("%d", &listChoice) == 1 && (listChoice == 0 || listChoice == 1 || listChoice == 2)) {
                break;
            } else {
                printf("ОШИБКА. Пожалуйста, введите 0, 1 или 2.\n");
                while(getchar() != '\n'); // Очистка ввода
            }
        }

        if (listChoice == 0) {
            printf("Выход из режима удаления студентов.\n");
            break; // Выходим из цикла
        }

        int position; // Позиция для удаления

        // Проверка позиции для удаления
        printf("Выберите позицию для удаления: (0 - голова, 2 - хвост, 1 - случайное место): ");
        while (1) {
            if (scanf("%d", &position) == 1 && (position == 0 || position == 1 || position == 2)) {
                break;
            } else {
                printf("ОШИБКА. Пожалуйста, введите 0, 1 или 2.\n");
                while(getchar() != '\n'); // Очистка ввода
            }
        }

        // Удаление студента из первого списка
        if (listChoice == 1) {
            if (position == 0) {
                deleteFromHead(group1); // Удаляем с головы
                printf("Удален студент с головы первого списка.\n");
            } else if (position == 2) {
                deleteFromTail(group1); // Удаляем с хвоста
                printf("Удален студент с хвоста первого списка.\n");
            } else if (position == 1) {
                printf("Введите позицию (индекс) для удаления студента: ");
                while (1) {
                    if (scanf("%d", &position) == 1) { // Считываем индекс для удаления
                        deleteFromPosition(group1, position); // Удаляем по индексу
                        break;
                    } else {
                        printf("ОШИБКА. Пожалуйста, введите корректный индекс.\n");
                        while(getchar() != '\n'); // Очистка ввода
                    }
                }
            } else {
                printf("Неверный выбор! Пожалуйста, выберите 0, 1 или 2.\n");
            }

            // Вывод обновленного списка после удаления
            printf("Обновленный первый список после удаления студента:\n");
            displayList(group1);
        }
        // Удаление студента из второго списка
        else if (listChoice == 2) {
            if (position == 0) {
                deleteFromHead(group2); // Удаляем с головы
                printf("Удален студент с головы второго списка.\n");
            } else if (position == 2) {
                deleteFromTail(group2); // Удаляем с хвоста
                printf("Удален студент с хвоста второго списка.\n");
            } else if (position == 1) {
                printf("Введите позицию (индекс) для удаления студента: ");
                while (1) {
                    if (scanf("%d", &position) == 1) { // Считываем индекс для удаления
                        deleteFromPosition(group2, position); // Удаляем по индексу
                        break;
                    } else {
                        printf("ОШИБКА. Пожалуйста, введите корректный индекс.\n");
                        while(getchar() != '\n'); // Очистка ввода
                    }
                }
            } else {
                printf("Неверный выбор! Пожалуйста, выберите 0, 1 или 2.\n");
            }

            // Вывод обновленного списка после удаления
            printf("Обновленный второй список после удаления студента:\n");
            displayList(group2);
        } 
        // Неправильный выбор
        else {
            printf("Неверный выбор! Пожалуйста, выберите 1 или 2.\n");
        }
    }

    // Запрос на получение студента по индексу
    while (1) {
        int listChoice = 0; // Переменная для выбора списка
        printf("\nВведите 1, чтобы получить студента из первого списка, 2 - из второго списка, 0 - для выхода: ");
        
        if (scanf("%d", &listChoice) != 1) { // Проверяем ввод
            printf("ОШИБКА. Пожалуйста, введите 0, 1 или 2.\n");
            while(getchar() != '\n'); // Очистка ввода
            continue; // Продолжаем цикл
        }

        if (listChoice == 0) {
            printf("Выход из режима получения студента.\n");
            break; // Выходим из цикла
        }

        int index; // Индекс студента
        if (listChoice == 1) {
            printf("Введите индекс, чтобы получить студента из первого списка: ");
            if (scanf("%d", &index) == 1) { // Считываем индекс для получения
                getStudentByIndex(group1, index); // Получаем студента по индексу
            } else {
                printf("ОШИБКА. Пожалуйста, введите корректный индекс.\n");
                while(getchar() != '\n'); // Очистка ввода
            }

        } else if (listChoice == 2) {
            printf("Введите индекс, чтобы получить студента из второго списка: ");
            if (scanf("%d", &index) == 1) { // Считываем индекс для получения
                getStudentByIndex(group2, index); // Получаем студента по индексу
            } else {
                printf("ОШИБКА. Пожалуйста, введите корректный индекс.\n");
                while(getchar() != '\n'); // Очистка ввода
            }
        } else {
            printf("Неверный выбор! Пожалуйста, выберите 1 или 2.\n"); // Неправильный выбор
        }
    }

    // Заполнение списка отличников
    displayHonors(group1, honorStudents); // Добавляем отличников из первого списка
    displayHonors(group2, honorStudents); // Добавляем отличников из второго списка

    // Вывод отличников из общего списка
    printf("\nОтличники из обоих списков:\n");
    displayList(honorStudents); // Отображаем список отличников

    // Подтверждение удаления списков
    char confirmation; // Переменная для подтверждения
    printf("Вы действительно хотите удалить все списки? (Y или y/N или n): ");
    scanf(" %c", &confirmation); // Считываем подтверждение

    if (confirmation == 'y' || confirmation == 'Y') {
        deleteList(group1); // Удаляем первый список
        deleteList(group2); // Удаляем второй список
        deleteList(honorStudents); // Удаляем список отличников
        group1 = NULL; // Обнуляем указатель на первый список
        group2 = NULL; // Обнуляем указатель на второй список
        honorStudents = NULL; // Обнуляем указатель на список отличников

        printf("Память очищена\n"); // Подтверждение освобождения памяти
    } else {
        printf("Удаление списков отменено.\n"); // Отмена удаления
    }

    return 0; // Завершение программы
}
```


## Мой комментарий

**Этот код работает!!!** ~~но примет ли этот код Ольга Викторовна?~~
