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
> cmake_minimum_required(VERSION 3.4)
> project(formatter)
> EOF


$ cat >> CMakeLists.txt << EOF
> set(CMAKE_CXX_STANDART_ 11)
> set(CMAKE_CXX_STANDART_REQUIRED ON)
> EOF

$ cat >> formatter_lib/CMakeLists.txt << EOF
> add_library (formatter STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)
> EOF


$ cat >> formatter_lib/CMakeLists.txt << EOF
> include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
> EOF

$ cmake -H. -B_bulid

$ cmake --build _build


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
>set(CMAKE_CURRENT_SOURCE_DIR ~/snoreoh/workspace/projects/lab03.1)
> EOF


$ cat >> CMakeLists.txt << EOF
> add_library(formatter_ex STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/formatter_ex.cpp)
> EOF

$ cat >> CMakeLists.txt << EOF
> include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
> include_directories(${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
> EOF

$ cat >> CMakeLists.txt << EOF
>target_link_libraries(formatter_ex formatter)
>EOF

$ cmake -H. -B_bulid

$ cmake --build _build
```

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.
   
* For *hello_world*  
   
```sh
$ cat >> CMakeLists.txt << EOF
 > cmake_minimum_required(VERSION 3.4)
 > project(hello_world)

 > set(CMAKE_CXX_STANDART 11)
 > set(CMAKE_CXX_STANDART_REQUIRED ON)
 > set(CMAKE_CURRENT_SOURCE_DIR /home/snoreoh/snoreoh/workspace/projects/lab03.1/lab03)

 > add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world_application/hello_world.cpp)

 > include_directories(${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
 > include_directories(${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)

 > find_library(formatter_ex NAMES libformatter_ex.a PATHS ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/_build)
 > find_library(formatter NAMES libformatter.a PATHS ${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib/_build)

 > target_link_libraries(hello_world ${formatter_ex} ${formatter})
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
$ cd solver_lib
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

$ cd solver_application
$ cat >> CMakeLists.txt >> EOF
 > cmake_minimum_required(VERSION 3.4)
 > project(solver)
 
 > set(CMAKE_CXX_STANDART 11)
 > set(CMAKE_CXX_STANDART_REQUIRED ON)
 > set(CMAKE_CURRENT_SOURCE_DIR /home/snoreoh/snoreoh/workspace/projects/lab03.1/lab03) 
 
 > include_directories(${CMAKE_CURRENT_SOURCE_DIR}/solver_lib)
 > include_directories(${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
 > include_directories(${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)
 
 > add_executable(solver ${CMAKE_CURRENT_SOURCE_DIR}/solver_application/equation.cpp)
 
 > find_library(formatter_ex NAMES libformatter_ex.a PATHS ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/_build)
 > find_library(formatter NAMES libformatter.a PATHS ${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib/_build)
 > find_library(solver_lib NAMES libsolver_lib.a PATHS ${CMAKE_CURRENT_SOURCE_DIR}/solver_lib/_build)
 
 > target_link_libraries(solver ${formatter_ex} ${formatter} ${solver_lib})
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
