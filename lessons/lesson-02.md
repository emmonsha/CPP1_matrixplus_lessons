# **Урок 2: Accessors, Mutators и простые операции**  
**Цель**: Реализовать методы доступа к данным (`GetRows`, `SetRows`, `GetCols`, `SetCols`), базовые операции (`SumMatrix`, `SubMatrix`, `EqMatrix`) и перегрузку операторов (`+`, `-`, `+=`, `-=`, `==`).  

---

## **1. Accessors и Mutators**  
**Зачем?** Чтобы безопасно получать и изменять размеры матрицы.  

### **Задание**:  
Дополните класс `E42Matrix` в `src/E42_matrix_oop.h`:  
```cpp
class E42Matrix {
 public:
  // ... (предыдущие методы)  
  void SetRows(int new_rows);  
  void SetCols(int new_cols);  
};
```

В `src/E42_matrix_oop.cc` реализуйте методы:  
```cpp
void E42Matrix::SetRows(int new_rows) {
  if (new_rows < 1) throw std::invalid_argument("Rows count must be positive");
  if (new_rows == rows_) return;

  // Создаём новую матрицу с нужным числом строк
  double** new_matrix = new double*[new_rows];
  for (int i = 0; i < new_rows; i++) {
    new_matrix[i] = new double[cols_]{0};
    // Копируем старые данные (если новые строки больше, остальные заполняются нулями)
    if (i < rows_) {
      for (int j = 0; j < cols_; j++) {
        new_matrix[i][j] = matrix_[i][j];
      }
      delete[] matrix_[i];  // Освобождаем старую память
    }
  }

  // Удаляем старую матрицу и заменяем её новой
  delete[] matrix_;
  matrix_ = new_matrix;
  rows_ = new_rows;
}

void E42Matrix::SetCols(int new_cols) {
  if (new_cols < 1) throw std::invalid_argument("Cols count must be positive");
  if (new_cols == cols_) return;

  // Создаём новую матрицу с нужным числом столбцов
  for (int i = 0; i < rows_; i++) {
    double* new_row = new double[new_cols]{0};
    // Копируем старые данные (если новые столбцы больше, остальные заполняются нулями)
    for (int j = 0; j < std::min(cols_, new_cols); j++) {
      new_row[j] = matrix_[i][j];
    }
    delete[] matrix_[i];  // Освобождаем старую строку
    matrix_[i] = new_row;
  }
  cols_ = new_cols;
}
```

---

### **Тесты для Accessors/Mutators**  
В `tests/test_matrix.cc` добавьте:  
```cpp
TEST(MatrixTest, SetRows) {
  E42Matrix matrix(2, 3);
  matrix.SetRows(4);
  EXPECT_EQ(matrix.GetRows(), 4);
  EXPECT_THROW(matrix.SetRows(0), std::invalid_argument);
}

TEST(MatrixTest, SetCols) {
  E42Matrix matrix(2, 3);
  matrix.SetCols(5);
  EXPECT_EQ(matrix.GetCols(), 5);
  EXPECT_THROW(matrix.SetCols(-1), std::invalid_argument);
}
```

---

## **2. Операции `SumMatrix` и `SubMatrix`**  
**Зачем?** Для сложения и вычитания матриц одинакового размера.  

### **Задание**:  
Добавьте в `src/E42_matrix_oop.h`:  
```cpp
class E42Matrix {
 public:
  // ... (предыдущие методы)  
  void SumMatrix(const E42Matrix& other);  
  void SubMatrix(const E42Matrix& other);  
  bool EqMatrix(const E42Matrix& other) const;  
};
```

Реализация в `src/E42_matrix_oop.cc`:  
```cpp
void E42Matrix::SumMatrix(const E42Matrix& other) {
  if (rows_ != other.rows_ || cols_ != other.cols_) {
    throw std::invalid_argument("Matrices must have the same size for addition");
  }
  for (int i = 0; i < rows_; i++) {
    for (int j = 0; j < cols_; j++) {
      matrix_[i][j] += other.matrix_[i][j];
    }
  }
}

void E42Matrix::SubMatrix(const E42Matrix& other) {
  if (rows_ != other.rows_ || cols_ != other.cols_) {
    throw std::invalid_argument("Matrices must have the same size for subtraction");
  }
  for (int i = 0; i < rows_; i++) {
    for (int j = 0; j < cols_; j++) {
      matrix_[i][j] -= other.matrix_[i][j];
    }
  }
}

bool E42Matrix::EqMatrix(const E42Matrix& other) const {
  if (rows_ != other.rows_ || cols_ != other.cols_) return false;
  for (int i = 0; i < rows_; i++) {
    for (int j = 0; j < cols_; j++) {
      if (std::abs(matrix_[i][j] - other.matrix_[i][j]) > 1e-7) {
        return false;
      }
    }
  }
  return true;
}
```

---

### **Тесты для операций**  
```cpp
TEST(MatrixTest, SumMatrix) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(2, 2);
  matrix1.SumMatrix(matrix2);  // 0 + 0 = 0
  EXPECT_TRUE(matrix1.EqMatrix(matrix2));
  EXPECT_THROW(matrix1.SumMatrix(E42Matrix(3, 3)), std::invalid_argument);
}

TEST(MatrixTest, SubMatrix) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(2, 2);
  matrix1.SubMatrix(matrix2);  // 0 - 0 = 0
  EXPECT_TRUE(matrix1.EqMatrix(matrix2));
}

TEST(MatrixTest, EqMatrix) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(2, 2);
  EXPECT_TRUE(matrix1.EqMatrix(matrix2));
  matrix1.SetRows(3);
  EXPECT_FALSE(matrix1.EqMatrix(matrix2));
}
```

---

## **3. Перегрузка операторов**  
**Зачем?** Чтобы использовать привычный синтаксис (`A + B` вместо `A.SumMatrix(B)`).  

### **Задание**:  
Добавьте в `src/E42_matrix_oop.h`:  
```cpp
class E42Matrix {
 public:
  // ... (предыдущие методы)  
  E42Matrix operator+(const E42Matrix& other) const;  
  E42Matrix operator-(const E42Matrix& other) const;  
  E42Matrix& operator+=(const E42Matrix& other);  
  E42Matrix& operator-=(const E42Matrix& other);  
  bool operator==(const E42Matrix& other) const;  
};
```

Реализация в `src/E42_matrix_oop.cc`:  
```cpp
E42Matrix E42Matrix::operator+(const E42Matrix& other) const {
  E42Matrix result(*this);  // Копируем текущую матрицу
  result.SumMatrix(other);  // Применяем сложение
  return result;
}

E42Matrix E42Matrix::operator-(const E42Matrix& other) const {
  E42Matrix result(*this);
  result.SubMatrix(other);
  return result;
}

E42Matrix& E42Matrix::operator+=(const E42Matrix& other) {
  SumMatrix(other);
  return *this;
}

E42Matrix& E42Matrix::operator-=(const E42Matrix& other) {
  SubMatrix(other);
  return *this;
}

bool E42Matrix::operator==(const E42Matrix& other) const {
  return EqMatrix(other);
}
```

---

### **Тесты для операторов**  
```cpp
TEST(MatrixTest, OperatorPlus) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(2, 2);
  E42Matrix result = matrix1 + matrix2;
  EXPECT_TRUE(result == matrix1);
}

TEST(MatrixTest, OperatorMinus) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(2, 2);
  E42Matrix result = matrix1 - matrix2;
  EXPECT_TRUE(result == matrix1);
}

TEST(MatrixTest, OperatorPlusEquals) {
  E42Matrix matrix1(2, 2);
  E42Matrix matrix2(2, 2);
  matrix1 += matrix2;
  EXPECT_TRUE(matrix1 == matrix2);
}
```

---

## **4. Итог урока**  
**Что сделано**:  
- Добавлены `SetRows`, `SetCols` для изменения размеров.  
- Реализованы `SumMatrix`, `SubMatrix`, `EqMatrix`.  
- Перегружены операторы `+`, `-`, `+=`, `-=`, `==`.  

**Домашнее задание**:  
1. Добавьте тесты для `operator-=` и `operator-`.  
2. Реализуйте метод `FillMatrix(std::vector<std::vector<double>>)` для заполнения матрицы значениями и протестируйте его.  

---

Следующий урок — **Умножение матриц и индексация (`*`, `*=`, `(i, j)`)**. Если есть вопросы, задавайте!