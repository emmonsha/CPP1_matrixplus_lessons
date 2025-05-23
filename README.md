# Учебный курс по C++: Реализация матричной библиотеки

## Оглавление уроков

0. [Урок 0: Настройка окружения для C++ (macOS/Linux)](./lessons/lesson_00.md)
1. [Урок 1: Настройка окружения и базовый класс](./lessons/lesson_01.md)
   - Установка компилятора (g++), Git, CMake
   - Создание структуры проекта
   - Реализация базового класса `E42Matrix`
   - Написание первых тестов (GTest)

2. [Урок 2: Accessors, Mutators и простые операции](./lessons/lesson_02.md)
   - Реализация `GetRows()`, `SetRows()`, `GetCols()`, `SetCols()`
   - Методы `EqMatrix`, `SumMatrix`, `SubMatrix`
   - Перегрузка операторов `==`, `+`, `-`, `+=`, `-=`

3. [Урок 3: Умножение и индексация](./lessons/lesson_03.md)
   - Реализация `MulNumber`, `MulMatrix`
   - Перегрузка `*`, `*=`
   - Оператор индексации `(int i, int j)`

4. [Урок 4: Транспонирование и алгебраические дополнения](./lessons/lesson_04.md)
   - Метод `Transpose()`
   - Начало реализации `CalcComplements()`

5. [Урок 5: Определитель матрицы](./lessons/lesson_05.md)
   - Рекурсивный метод вычисления определителя
   - Тестирование на матрицах 1x1, 2x2, 3x3

6. [Урок 6: Обратная матрица](./lessons/lesson_06.md)
   - Реализация `InverseMatrix()`
   - Обработка нулевого определителя

7. [Урок 7: Исключения и обработка ошибок](./lessons/lesson_07.md)
   - Добавление исключений
   - Тестирование ошибочных ситуаций

8. [Урок 8: Оптимизация и рефакторинг](./lessons/lesson_08.md)
   - Улучшение кода
   - Проверка утечек памяти (Valgrind)

9. [Урок 9: Дополнительные тесты и документация](./lessons/lesson_09.md)
   - 100% coverage тестами
   - Документирование кода

10. [Урок 10: Финальная проверка и сборка](./lessons/lesson_10.md)
    - Создание Makefile
    - Проверка стиля Google Style

## Структура проекта
E42_matrix_project/
├── src/
│   ├── E42_matrix_oop.h
│   └── E42_matrix_oop.cc
├── tests/
│   ├── CMakeLists.txt
│   └── test_matrix.cc
├── lessons/          # Папка с материалами уроков
│   ├── lesson_01.md
│   ├── lesson_02.md
│   └── ...
├── Makefile
└── README.md

## Рекомендуемые инструменты
- **Компилятор**: g++ (C++17)
- **Сборка**: Make/CMake
- **Тестирование**: Google Test
- **Редакторы**: VSCode, CLion
- **Отладка**: gdb, Valgrind

## Начало работы
```bash
git clone https://github.com/yourname/E42_matrix_project
cd E42_matrix_project
make all  # Сборка проекта
```
