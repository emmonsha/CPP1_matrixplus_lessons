**Урок 1** {#lesson-01}

---

## **Урок 1: Базовый класс матрицы**  

### **1. Структура проекта**  
**Зачем?** Чтобы код был организован, а не в одной куче.  
**Создайте папки и файлы**:  
```bash
mkdir -p E42_matrix_oop/{src,tests}  # Основные папки
cd E42_matrix_oop
touch src/E42_matrix_oop.h src/E42_matrix_oop.cc  # Класс матрицы
touch tests/test_matrix.cc           # Тесты
touch Makefile                      # Для сборки
```

---

### **2. Реализация класса `E42Matrix`**  
**Зачем?** Это основа всей библиотеки. Начнём с конструкторов и деструктора.  

#### **Задание**:  
В файле `src/E42_matrix_oop.h` объявите класс:  
```cpp
#ifndef E42_MATRIX_OOP_H
#define E42_MATRIX_OOP_H

class E42Matrix {
 private:
  int rows_, cols_;   // Количество строк и столбцов
  double** matrix_;   // Указатель на двумерный массив

 public:
  E42Matrix();                          // Базовый конструктор (например, 1x1)
  E42Matrix(int rows, int cols);        // Параметризированный конструктор
  E42Matrix(const E42Matrix& other);    // Конструктор копирования
  E42Matrix(E42Matrix&& other);         // Конструктор перемещения
  ~E42Matrix();                         // Деструктор

  // Дополнительные методы (для тестов)
  int GetRows() const { return rows_; }  // Accessor для строк
  int GetCols() const { return cols_; }  // Accessor для столбцов
};

#endif  // E42_MATRIX_OOP_H
```

В файле `src/E42_matrix_oop.cc` реализуйте методы:  
```cpp
#include "E42_matrix_oop.h"
#include <iostream>

// Базовый конструктор (создаёт матрицу 1x1 с нулями)
E42Matrix::E42Matrix() : rows_(1), cols_(1) {
  matrix_ = new double*[rows_];
  matrix_[0] = new double[cols_]{0};
}

// Параметризированный конструктор (создаёт rows x cols)
E42Matrix::E42Matrix(int rows, int cols) : rows_(rows), cols_(cols) {
  if (rows < 1 || cols < 1) throw std::invalid_argument("Invalid matrix size");
  matrix_ = new double*[rows_];
  for (int i = 0; i < rows_; i++) {
    matrix_[i] = new double[cols_]{0};
  }
}

// Конструктор копирования
E42Matrix::E42Matrix(const E42Matrix& other) : rows_(other.rows_), cols_(other.cols_) {
  matrix_ = new double*[rows_];
  for (int i = 0; i < rows_; i++) {
    matrix_[i] = new double[cols_];
    for (int j = 0; j < cols_; j++) {
      matrix_[i][j] = other.matrix_[i][j];
    }
  }
}

// Конструктор перемещения (перенимает ресурсы)
E42Matrix::E42Matrix(E42Matrix&& other) noexcept 
    : rows_(other.rows_), cols_(other.cols_), matrix_(other.matrix_) {
  other.matrix_ = nullptr;  // Чтобы деструктор other не удалил данные
  other.rows_ = 0;
  other.cols_ = 0;
}

// Деструктор (освобождает память)
E42Matrix::~E42Matrix() {
  if (matrix_) {
    for (int i = 0; i < rows_; i++) {
      delete[] matrix_[i];
    }
    delete[] matrix_;
  }
}
```

---

### **3. Написание первых тестов (GTest)**  
**Зачем?** Чтобы убедиться, что конструкторы и деструктор работают правильно.  

#### **Задание**:  
В файле `tests/test_matrix.cc` напишите тесты:  
```cpp
#include <gtest/gtest.h>
#include "../src/E42_matrix_oop.h"

TEST(MatrixTest, DefaultConstructor) {
  E42Matrix matrix;
  EXPECT_EQ(matrix.GetRows(), 1);
  EXPECT_EQ(matrix.GetCols(), 1);
}

TEST(MatrixTest, ParameterizedConstructor) {
  E42Matrix matrix(3, 4);
  EXPECT_EQ(matrix.GetRows(), 3);
  EXPECT_EQ(matrix.GetCols(), 4);
}

TEST(MatrixTest, CopyConstructor) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(matrix1);  // Копирование
  EXPECT_EQ(matrix2.GetRows(), 2);
  EXPECT_EQ(matrix2.GetCols(), 2);
}

TEST(MatrixTest, MoveConstructor) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(std::move(matrix1));  // Перемещение
  EXPECT_EQ(matrix2.GetRows(), 2);
  EXPECT_EQ(matrix2.GetCols(), 2);
  EXPECT_EQ(matrix1.GetRows(), 0);  // Проверка, что matrix1 "опустошён"
}
```

---

### **4. Проверки и возможные ошибки**  
**Что тестируем**:  
1. **Конструкторы**:  
   - Создают матрицу нужного размера.  
   - Выбрасывают исключение при некорректных размерах (например, `E42Matrix(-1, 5)`).  
2. **Деструктор**:  
   - Корректно освобождает память (проверить через Valgrind).  
3. **Копирование и перемещение**:  
   - Данные не теряются при копировании.  
   - Ресурсы перемещаются, а не копируются.  

**Пример ошибки**:  
```cpp
TEST(MatrixTest, InvalidSize) {
  EXPECT_THROW(E42Matrix matrix(0, 5), std::invalid_argument);
}
```

---

### **5. Итог урока**  
**Что должно получиться**:  
- Рабочий класс `E42Matrix` с конструкторами и деструктором.  
- Простые тесты, проверяющие создание и удаление матриц.  
- Готовое окружение для дальнейшей работы.  

**Домашнее задание**:  
1. Добавьте тест для деструктора (например, создайте матрицу в блоке `{}` и убедитесь, что память освобождается).  
2. Реализуйте метод `SetRows(int new_rows)` и протестируйте его.  

---

Следующий урок — **Accessors/Mutators и простые операции (Sum, Sub)**. Если есть вопросы по первому уроку, задавайте!