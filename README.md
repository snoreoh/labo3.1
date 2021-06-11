## Laboratory work III

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.
```sh
$ cat > CMakeLists.txt <<EOF
> cmake_minimum_required(VERSION 3.4) // устанавливаем минимальнуую версию cmakе
> project(formatter) // называем проект
> EOF


$ cat >> CMakeLists.txt << EOF
> set(CMAKE_CXX_STANDART_ 11) 
> set(CMAKE_CXX_STANDART_REQUIRED ON) // устанавливаем стандарты языка
> EOF

$ cat >> formatter_lib/CMakeLists.txt << EOF
> add_library(formatter STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp) // сборка статической библиотеки 
> EOF


$ cat >> formatter_lib/CMakeLists.txt << EOF
> include_directories(\${CMAKE_CURRENT_SOURCE_DIR}) // подключение директории с заголовочными файлами
> EOF

$ cmake -H. -B_bulid // создаем папку для сбоки

$ cmake --build _build // производим сборку


```
### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

```sh
$ cd formatter_ex_lib/

$ cat >> CMakeLists.txt << EOF
> cmake_minimum_required(VERSION 3.4)
> project(formatter_ex)
> EOF


$ cat > CMakeLists.txt  << EOF
> set(CMAKE_CXX_STANDARD 20)
> set(CMAKE_CXX_STANDARD_REQUIRED ON)
>set(CMAKE_CURRENT_SOURCE_DIR ~/snoreoh/workspace/projects/lab03.1) // меняем переменную CMAKE_CURRENT_SOURCE_DIR
> EOF


$ cat >> CMakeLists.txt << EOF
> add_library(formatter_ex STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/formatter_ex.cpp)
> EOF

$ cat >> CMakeLists.txt << EOF
> include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
> include_directories(${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
> EOF

$ cat >> CMakeLists.txt << EOF
>target_link_libraries(formatter_ex formatter) // линкуем библиотеки
>EOF

$ cmake -H. -B_bulid

$ cmake --build _build
```

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
 *hello_world*, которое использует библиотеку *formatter_ex*;
 *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.
   
* For *hello_world*  
   
```sh 
Для начала переносим библиотеку formatter_ex в папку с проектом hello_world

Затем делаем CMakeLists:

$ cat >> CMakeLists.txt << EOF
 
> cmake_minimum_required(VERSION 3.4)
> project(hello_world)
> include_directories(formatter_ex_lib)
> add_subdirectory(formatter_ex_lib)
> add_executable(hello_world hello_world.cpp)
> target_link_libraries(hello_world formatter_ex)
> EOF

$ cmake -H. -B_build
$ cmake --build _build

$ _build/hello_world && echo

Вывод:

-------------------------
hello, world!
-------------------------
```
* For *solver*

```sh
$ cd solver_lib   // все шаги аналогичны предыдущим пунктам
$ cat >> CMakeLists.txt << EOF
 > cmake_minimum_required(VERSION 3.4)
 > project(slover_lib)
 
 > set(CMAKE_CXX_STANDART 11)
 > set(CMAKE_CXX_STANDART_REQUIRED ON)
 > add_library(solver_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp)
 > include_directories(${CMAKE_CURRENT_SOURCE_DIR})
 
Вывод:

ERROR!!!: 'sqrtf' is not a member of std


To fix it:

 $ nano CMakeLists.txt
     1. Add #include <cmath>
     2. Change 'sqrtf' to 'sqrt'

$ cmake --build _build
$ ls _build/libsolver.a

```

```sh
Также переновим все необходимые библиотеки в папку solver_application

Затем CMakeLists:
$ cd solver_application
$ cat >> CMakeLists.txt << EOF
> cmake_minimum_required(VERSION 2.8)
> project(solver)
> add_executable(solver equation.cpp)
> include_directories(formatter_ex_lib)
> add_subdirectory(formatter_ex_lib)
> include_directories(solver_lib)
> add_subdirectory(solver_lib)
> target_link_libraries(solver formatter_ex)
> target_link_libraries(solver solver_lib)
> EOF
 
$ cmake -H. -B_build
$ cmake --build _build
$ _build/solver && echo

Вывод: 

1
2
1
-------------------------
x1 = -1.000000
-------------------------
-------------------------
x2 = -1.000000
-------------------------


```
