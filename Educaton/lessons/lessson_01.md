**Урок 1** {#lesson-01}

---

## **Урок 1: Настройка окружения и базовый класс**  
**Цель**: Создать основу для работы с матрицами — настроить окружение, реализовать класс `S21Matrix` с конструкторами и деструктором, написать первые тесты.  

### **1. Установка инструментов**  
**Зачем?** Без этих инструментов невозможно компилировать и тестировать код.  
**Что установить**:  
- **g++** (компилятор C++):  
  ```bash
  sudo apt update && sudo apt install g++   # Для Linux (Debian/Ubuntu)
  brew install gcc                          # Для macOS (через Homebrew)
  ```
- **Git** (система контроля версий):  
  ```bash
  sudo apt install git                      # Linux
  brew install git                          # macOS
  ```
- **CMake** (опционально, для сложных проектов):  
  ```bash
  sudo apt install cmake                    # Linux
  brew install cmake                        # macOS
  ```
- **Google Test** (библиотека для тестирования):  
  ```bash
  sudo apt install libgtest-dev             # Linux
  ```

---

### **2. Структура проекта**  
**Зачем?** Чтобы код был организован, а не в одной куче.  
**Создайте папки и файлы**:  
```bash
mkdir -p s21_matrix_oop/{src,tests}  # Основные папки
cd s21_matrix_oop
touch src/s21_matrix_oop.h src/s21_matrix_oop.cc  # Класс матрицы
touch tests/test_matrix.cc           # Тесты
touch Makefile                      # Для сборки
```

---

### **3. Реализация класса `S21Matrix`**  
**Зачем?** Это основа всей библиотеки. Начнём с конструкторов и деструктора.  

#### **Задание**:  
В файле `src/s21_matrix_oop.h` объявите класс:  
```cpp
#ifndef S21_MATRIX_OOP_H
#define S21_MATRIX_OOP_H

class S21Matrix {
 private:
  int rows_, cols_;   // Количество строк и столбцов
  double** matrix_;   // Указатель на двумерный массив

 public:
  S21Matrix();                          // Базовый конструктор (например, 1x1)
  S21Matrix(int rows, int cols);        // Параметризированный конструктор
  S21Matrix(const S21Matrix& other);    // Конструктор копирования
  S21Matrix(S21Matrix&& other);         // Конструктор перемещения
  ~S21Matrix();                         // Деструктор

  // Дополнительные методы (для тестов)
  int GetRows() const { return rows_; }  // Accessor для строк
  int GetCols() const { return cols_; }  // Accessor для столбцов
};

#endif  // S21_MATRIX_OOP_H
```

В файле `src/s21_matrix_oop.cc` реализуйте методы:  
```cpp
#include "s21_matrix_oop.h"
#include <iostream>

// Базовый конструктор (создаёт матрицу 1x1 с нулями)
S21Matrix::S21Matrix() : rows_(1), cols_(1) {
  matrix_ = new double*[rows_];
  matrix_[0] = new double[cols_]{0};
}

// Параметризированный конструктор (создаёт rows x cols)
S21Matrix::S21Matrix(int rows, int cols) : rows_(rows), cols_(cols) {
  if (rows < 1 || cols < 1) throw std::invalid_argument("Invalid matrix size");
  matrix_ = new double*[rows_];
  for (int i = 0; i < rows_; i++) {
    matrix_[i] = new double[cols_]{0};
  }
}

// Конструктор копирования
S21Matrix::S21Matrix(const S21Matrix& other) : rows_(other.rows_), cols_(other.cols_) {
  matrix_ = new double*[rows_];
  for (int i = 0; i < rows_; i++) {
    matrix_[i] = new double[cols_];
    for (int j = 0; j < cols_; j++) {
      matrix_[i][j] = other.matrix_[i][j];
    }
  }
}

// Конструктор перемещения (перенимает ресурсы)
S21Matrix::S21Matrix(S21Matrix&& other) noexcept 
    : rows_(other.rows_), cols_(other.cols_), matrix_(other.matrix_) {
  other.matrix_ = nullptr;  // Чтобы деструктор other не удалил данные
  other.rows_ = 0;
  other.cols_ = 0;
}

// Деструктор (освобождает память)
S21Matrix::~S21Matrix() {
  if (matrix_) {
    for (int i = 0; i < rows_; i++) {
      delete[] matrix_[i];
    }
    delete[] matrix_;
  }
}
```

---

### **4. Написание первых тестов (GTest)**  
**Зачем?** Чтобы убедиться, что конструкторы и деструктор работают правильно.  

#### **Задание**:  
В файле `tests/test_matrix.cc` напишите тесты:  
```cpp
#include <gtest/gtest.h>
#include "../src/s21_matrix_oop.h"

TEST(MatrixTest, DefaultConstructor) {
  S21Matrix matrix;
  EXPECT_EQ(matrix.GetRows(), 1);
  EXPECT_EQ(matrix.GetCols(), 1);
}

TEST(MatrixTest, ParameterizedConstructor) {
  S21Matrix matrix(3, 4);
  EXPECT_EQ(matrix.GetRows(), 3);
  EXPECT_EQ(matrix.GetCols(), 4);
}

TEST(MatrixTest, CopyConstructor) {
  S21Matrix matrix1(2, 2);
  S21Matrix matrix2(matrix1);  // Копирование
  EXPECT_EQ(matrix2.GetRows(), 2);
  EXPECT_EQ(matrix2.GetCols(), 2);
}

TEST(MatrixTest, MoveConstructor) {
  S21Matrix matrix1(2, 2);
  S21Matrix matrix2(std::move(matrix1));  // Перемещение
  EXPECT_EQ(matrix2.GetRows(), 2);
  EXPECT_EQ(matrix2.GetCols(), 2);
  EXPECT_EQ(matrix1.GetRows(), 0);  // Проверка, что matrix1 "опустошён"
}
```

---

### **5. Проверки и возможные ошибки**  
**Что тестируем**:  
1. **Конструкторы**:  
   - Создают матрицу нужного размера.  
   - Выбрасывают исключение при некорректных размерах (например, `S21Matrix(-1, 5)`).  
2. **Деструктор**:  
   - Корректно освобождает память (проверить через Valgrind).  
3. **Копирование и перемещение**:  
   - Данные не теряются при копировании.  
   - Ресурсы перемещаются, а не копируются.  

**Пример ошибки**:  
```cpp
TEST(MatrixTest, InvalidSize) {
  EXPECT_THROW(S21Matrix matrix(0, 5), std::invalid_argument);
}
```

---

### **6. Итог урока**  
**Что должно получиться**:  
- Рабочий класс `S21Matrix` с конструкторами и деструктором.  
- Простые тесты, проверяющие создание и удаление матриц.  
- Готовое окружение для дальнейшей работы.  

**Домашнее задание**:  
1. Добавьте тест для деструктора (например, создайте матрицу в блоке `{}` и убедитесь, что память освобождается).  
2. Реализуйте метод `SetRows(int new_rows)` и протестируйте его.  

---

Следующий урок — **Accessors/Mutators и простые операции (Sum, Sub)**. Если есть вопросы по первому уроку, задавайте!