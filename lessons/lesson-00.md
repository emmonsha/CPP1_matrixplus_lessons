# **Урок 0: Настройка окружения для C++ (macOS/Linux)**  
**Цель**: Подготовить систему для разработки на C++ (компилятор, Git, CMake, Google Test).
# **Установка базовых инструментов**
## **1. Установка компилятора**
### **macOS**  
```bash
brew install gcc  # Установка GCC
g++ --version    # Проверка версии (должно быть 10+)
```

### **Linux (Debian/Ubuntu)**  
```bash
sudo apt update && sudo apt install g++
```

## **2. Установка Git и CMake**  
```bash
# macOS
brew install git cmake

# Linux
sudo apt install git cmake
```

## **3. Установка Google Test**  
### **macOS (через Homebrew)**  
```bash
brew install googletest
```

### **Linux**  
```bash
sudo apt install libgtest-dev
cd /usr/src/gtest
sudo cmake CMakeLists.txt
sudo make
sudo cp *.a /usr/lib  # Ручная установка библиотек
```

## **4. Проверка окружения**  
Создайте тестовый файл `test_hello.cc`:  
```cpp
#include <gtest/gtest.h>
TEST(HelloTest, Basic) { EXPECT_EQ(1 + 1, 2); }
int main(int argc, char** argv) {
  testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```

Компиляция и запуск:  
```bash
g++ -std=c++17 test_hello.cc -lgtest -lgtest_main -pthread -o test
./test
```

**Ожидаемый вывод**:  
```
[ OK ] HelloTest.Basic (0 ms)
```

---

## **2. Интеграция с IDE**  
### **Visual Studio Code (macOS/Linux)**  
1. **Установите расширения**:  
   - [C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) (от Microsoft)  
   - [CMake Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cmake-tools)  
   - [Google Test Adapter](https://marketplace.visualstudio.com/items?itemName=DavidSchuldenfrei.gtest-adapter)  

2. **Настройка IntelliSense**:  
   Создайте файл `.vscode/c_cpp_properties.json`:  
   ```json
   {
     "configurations": [
       {
         "name": "Mac",
         "includePath": [
           "${workspaceFolder}/**",
           "/usr/local/include",  // Путь к GTest (для Homebrew)
           "/opt/homebrew/include"  // Для Apple Silicon
         ],
         "compilerPath": "/usr/local/bin/g++",  // или /opt/homebrew/bin/g++
         "cppStandard": "c++17"
       }
     ]
   }
   ```

3. **Запуск тестов**:  
   Добавьте в `tasks.json`:  
   ```json
   {
     "type": "shell",
     "label": "Build and Run Tests",
     "command": "g++ -std=c++17 ${workspaceFolder}/tests/*.cc -I${workspaceFolder}/src -lgtest -lgtest_main -pthread -o ${workspaceFolder}/bin/tests && ${workspaceFolder}/bin/tests"
   }
   ```

---

### **Xcode (macOS)**  
1. **Создайте проект**:  
   `File → New → Project → Command Line Tool` (выберите C++).  

2. **Добавьте GTest**:  
   - В `Build Settings` укажите:  
     - **Header Search Paths**: `/usr/local/include` (или `/opt/homebrew/include`)  
     - **Library Search Paths**: `/usr/local/lib` (или `/opt/homebrew/lib`)  
     - **Other Linker Flags**: `-lgtest -lgtest_main -pthread`  

3. **Пример теста**:  
   В `main.cpp`:  
   ```cpp
   #include <gtest/gtest.h>
   TEST(XcodeTest, Demo) { EXPECT_TRUE(true); }
   int main(int argc, char** argv) {
     testing::InitGoogleTest(&argc, argv);
     return RUN_ALL_TESTS();
   }
   ```

---

## **3. Отладка**  
### **LLDB (macOS) / GDB (Linux)**  
1. **Компиляция с отладочной информацией**:  
   ```bash
   g++ -std=c++17 -g src/*.cc tests/*.cc -lgtest -pthread -o matrix_debug
   ```

2. **Запуск отладчика**:  
   ```bash
   lldb ./matrix_debug  # macOS
   gdb ./matrix_debug   # Linux
   ```

3. **Основные команды**:  
   - `break имя_файла:строка` – установка точки останова.  
   - `run` – запуск программы.  
   - `next` (`n`) – шаг без захода в функции.  
   - `step` (`s`) – шаг с заходом в функции.  
   - `print переменная` – вывод значения переменной.  

4. **Пример для отладки теста**:  
   ```bash
   (lldb) break test_matrix.cc:15  # Остановка на тесте DefaultConstructor
   (lldb) run
   ```

---

## **4. Полезные советы**  
### **Для Xcode**  
- Чтобы видеть вывод тестов:  
  `Product → Scheme → Edit Scheme → Run → Options → Console → "Use Terminal"`.  

### **Для VSCode**  
- Автоматическая отладка:  
  Создайте `.vscode/launch.json`:  
  ```json
  {
    "version": "0.2.0",
    "configurations": [
      {
        "name": "Debug Tests",
        "type": "cppdbg",
        "request": "launch",
        "program": "${workspaceFolder}/bin/tests",
        "args": [],
        "environment": [],
        "externalConsole": false,
        "MIMode": "lldb"
      }
    ]
  }
  ```

---

## **5. Проверка работоспособности**  
1. В **VSCode**: Нажмите `F5` для запуска отладки.  
2. В **Xcode**: Нажмите `Cmd + R` и проверьте вывод в консоли.  
3. В **терминале**:  
   ```bash
   lldb ./matrix_debug
   (lldb) run
   ```

---

### **Итог урока**  
Теперь вы можете:  
- Работать в **VSCode** или **Xcode** с поддержкой GTest.  
- Отлаживать код через **LLDB/GDB**.  
- Быстро находить ошибки в тестах.  

Следующий шаг — **[Урок 1: Реализация базового класса матрицы](#lesson-01)**.  

Если нужно добавить что-то ещё (например, настройку для CLion), дайте знать!


Хотите, чтобы я добавил что-то ещё в урок по настройке? Например, про:  
- Интеграцию с IDE (Xcode, VSCode)  
- Отладку через `lldb`/`gdb`  
- Альтернативные способы установки GTest?