#指定运行此配置文件所需的 CMake 的最低版本
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

#项目名称
PROJECT(mDemoPrj)

SET (Demo_VERSION_MAJOR 3)
SET (Demo_VERSION_MINOR 6)

# 可选的<mode>关键字确定消息的类型，这会影响消息的处理方式：
# FATAL_ERROR:        CMake错误，停止处理和生成。
# SEND_ERROR:         CMake错误，继续处理，但跳过生成。
# WARNING:            CMake警告，继续处理。
# AUTHOR_WARNING:     CMake警告（dev），继续处理。
# DEPRECATION:        如果分别启用了变量CMAKE_ERROR_DEPRECATED或CMAKE_WARN_DEPRECATED，则CMake弃用错误或警告，否则没有消息。
# (none) or NOTICE:   打印到stderr的重要消息以吸引用户的注意。
# STATUS:             项目用户可能会感兴趣的主要有趣消息。理想情况下，这些消息应该简洁明了，不超过一行，但仍能提供信息。
# VERBOSE:            适用于项目用户的详细参考消息。这些消息应提供在大多数情况下不会引起关注的其他详细信息，但对于希望更深入地了解正在发生的事情的项目开发人员来说可能很有用。
# DEBUG:              详细的信息性消息旨在提供给在项目本身上进行开发的开发人员，而不是只想构建该项目的用户。这些消息通常对构建项目的其他用户不感兴趣，并且通常与内部实现细节密切相关。
# TRACE:              具有非常低级的实现细节的细粒度消息。使用此日志级别的消息通常仅是临时的，并且有望在发布项目，打包文件等之前被删除。
# CMake命令行工具在stdout上显示STATUS 到 TRACE 的消息，该消息前面带有两个连字符和一个空格。所有其他消息类型都发送到stderr，并且不带连字符前缀。
MESSAGE(STATUS "-------- MAIN CMAKELISTS MSG --------")

# 存放cmake指令指向的 CMakeLists.txt 的路径变量:
#     PROJECT_SOURCE_DIR
#     <projectname>_SOURCE_DIR
# 用户执行cmake指令的路径:
#     PROJECT_BINARY_DIR
#     <projectname>_BINARY_DIR
MESSAGE(STATUS "    PROJECT_SOURCE_DIR: " ${PROJECT_SOURCE_DIR})
MESSAGE(STATUS "    mDemoPrj_SOURCE_DIR: " ${mDemoPrj_SOURCE_DIR})
MESSAGE(STATUS "    PROJECT_BINARY_DIR: " ${PROJECT_BINARY_DIR})
MESSAGE(STATUS "    mDemoPrj_BINARY_DIR: " ${mDemoPrj_BINARY_DIR})
MESSAGE(STATUS "    CMAKE_ROOTR: " ${CMAKE_ROOT})

# 让 CMake 支持 gdb 只需要指定 Debug 模式下开启 -g 选项:
SET(CMAKE_BUILD_TYPE "Debug")
SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
# 指定编译器
# SET(CMAKE_C_COMPILER "/usr/bin/arm-linux-gnueabihf-gcc")
# SET(CMAKE_CXX_COMPILER "/usr/.....")

# 变量 CMAKE_INCLUDE_CURRENT_DIR 的作用
# 自动添加 CMAKE_CURRENT_BINARY_DIR 和 CMAKE_CURRENT_SOURCE_DIR 到当前处理的 CMakeLists.txt。相当于在每个 CMakeLists.txt 加入:
# INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR})
# CMAKE_CURRENT_BINARY_DIR 和 CMAKE_CURRENT_SOURCE_DIR 是面向 CMakeLists.txt 文件的源和目标根目录，而不像 PROJECT_SOURCE_DIR 和 PROJECTE_BINARY_DIR 是面向整个项目的源和目标的根目录
SET(CMAKE_INCLUDE_CURRENT_DIR ON)  # 不知道为什么失灵
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR})
MESSAGE(STATUS "    CMAKE_CURRENT_BINARY_DIR: " ${CMAKE_CURRENT_BINARY_DIR})
MESSAGE(STATUS "    CMAKE_CURRENT_SOURCE_DIR: " ${CMAKE_CURRENT_SOURCE_DIR})

# 指定生成链接链接库的存放路径,不指定的话默认是存放在跟当前 CMakeLists.txt 一样的路径
# 该设置也可以跟随 ADD_LIBRARY 指令，放在该指令之前，这样控制更精确
MESSAGE(STATUS "    Before set LIBRARY_OUTPUT_PATH: " ${LIBRARY_OUTPUT_PATH})
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
MESSAGE(STATUS "    After set LIBRARY_OUTPUT_PATH: " ${LIBRARY_OUTPUT_PATH})

# 指定生成可执行文件后的存放路径
# 该设置也可以跟随 ADD_EXECUTABLE 指令，放在该指令之前，这样控制更精确
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)


# ======================================================================================================================== custome compile options
# configure_file(<input> <output> [COPYONLY] [ESCAPE_QUOTES] [@ONLY] [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
# configure_file 主要实现如下两个功能:
#     1. 将 <input> 文件里面的内容全部复制到 <output> 文件中；
#     2. 根据参数规则，替换 @VAR@ 或 ${VAR} 变量；
# 参数解析
#     COPYONLY        仅拷贝 <input> 文件里面的内容到 <output> 文件， 不进行变量的替换；
#     ESCAPE_QUOTES   使用反斜杠（C语言风格）来进行转义；
#     @ONLY           限制替换， 仅仅替换 @VAR@ 变量， 不替换 ${VAR} 变量
#     NEWLINE_STYLE   指定输入文件的新行格式， 例如：Unix 中使用的是 \n, windows 中使用的 \r\n
# **注意: ** COPYONLY 和 NEWLINE_STYLE 是冲突的，不能同时使用；
# 加入一个配置头文件，用于处理 CMake 对源码的设置
# configure_file 命令用于加入一个配置头文件 config.h ，这个文件由 CMake 从 config.h.in 生成，通过这样的机制，将可以通过预定义一些参数和变量来控制代码的生成。
# configure.h.in 中的 #cmakedefin USE_MYMATH 可以对应到 CMakeLists.txt 中的宏
CONFIGURE_FILE (
    "${PROJECT_SOURCE_DIR}/config.h.in"
    "${PROJECT_BINARY_DIR}/config.h"
    )

# option(<option_variable> "help string describing option" [initial value])
# 提供一个选项供用户选择为ON或OFF。 如果未提供初始值，则使用OFF。该选则可以在调用CMAKE时使用形如
#     cmake -D USE_MYMATH=OFF ..
#     或 cmake -DUSE_MYMATH=OFF ..
# 的方式来设定，通过cmake指令设定的优先级要高于 option 的默认值
# 是否使用自己的 MathFunctions 库
# option 命令添加了一个 USE_MYMATH 选项，并且默认值为 ON 。
OPTION(USE_MYMATH "Use provided math implementation" ON)
MESSAGE(STATUS "    USE_MYMATH is: " ${USE_MYMATH})

# 是否加入 MathFunctions 库
if (USE_MYMATH)
    INCLUDE_DIRECTORIES ("${PROJECT_SOURCE_DIR}/src/math")
    ADD_SUBDIRECTORY (src/math)
    SET (EXTRA_LIBS ${EXTRA_LIBS} mathFunctions)
endif (USE_MYMATH)
# ======================================================================================================================== custome compile options end


# 指定生成目标
# ADD_EXECUTABLE(Demo ${DIR_SRCS})
# TARGET_LINK_LIBRARIES (Demo  ${EXTRA_LIBS})

# SET(<VAR> -) 主调用CMakeLists.txt文件通过SET设置的变量可以传递到子目录的CMakeLists.txt中，但反过来不行,无论在 ADD_SUBDIRECTORY 之前还是之后
# 主调CMakeLists.txt向子目录中的CMakeLists.txt中传递变量时需要将变量的设置放在 ADD_SUBDIRECTORY 指令之前，否则传不下去
SET(MAINVAL "this is main CMakeLists VAL")

# 如果工程存在多个目录，需要确保每个要管理的目录存在一个CMakeLists.txt
# 无论CMakeLists.txt文件在根目录还是子目录中，也无论一个工程有一个还是多个CMakeList文件，文件里的路径起点均为文件当前位置
# 该指令可以指定中间二进制和目标二进制文件的存放位置，可以使用类似 ADD_SUBDIRECTORY(src bin) 的形式，这项当于在编译的时候把 src的名字改成bin。
ADD_SUBDIRECTORY(src)

# ======================================================================================================================== install and test
# install用于指定在安装时运行的规则。它可以用来安装很多内容，可以包括目标二进制、动态库、静态库以及文件、目录、脚本等：
#     install(TARGETS <target>... [...])              目标文件：通过ADD_EXECUTABLE或者ADD_LIBRARY定义的目标文件，即可执行二进制、动态库、静态库
#     install({FILES | PROGRAMS} <file>... [...])     普通文件/脚本之类：用于安装一般文件
#     install(DIRECTORY <dir>... [...])               目录：安装目录
#     install(SCRIPT <file> [...])                    cmake脚本
#     install(CODE <code> [...])
#     install(EXPORT <export-name> [...])
# 参数 DESTINATION 用于指定安装目录，该指定是以 CMAKE_INSTALL_PREFIX 变量指向的路径开始，例如 DESTINATION bin 表示 ${CMAKE_INSTALL_PREFIX}/bin，但是如果 bin 以 / 开头就表示绝对路径，变量 CMAKE_INSTALL_PREFIX 就不起作用了
# 变量 CMAKE_INSTALL_PREFIX 可以在编译的时候进行控制，例如: cmake -DCMAKE_INSTALL_PREFIX=. .. 或 cmake -DCMAKE_INSTALL_PREFIX=$PWD ..
MESSAGE(STATUS "    CMAKE_INSTALL_PREFIX: " ${CMAKE_INSTALL_PREFIX})
# 指定 mathFunctions 库的安装路径
if (USE_MYMATH)
    INSTALL(TARGETS mathFunctions DESTINATION bin)
    INSTALL(FILES src/math/mathFunctions.h DESTINATION include)  # 安装文件时注意以当前 CMakeListas.txt 文件为路径的起点
endif (USE_MYMATH)
INSTALL(TARGETS demo DESTINATION bin)
INSTALL(FILES "${PROJECT_BINARY_DIR}/config.h" DESTINATION include)

# ======================================================================================================================== install and test end


# ======================================================================================================================== 使用外部库
# 原始方法
# 使用外部库最基础的写法是需要一下三个部分
#     1. INCLUDE_DIRECTORIES  对应 gcc/g++ 的 -I 参数，写在顶层主调 CMakeLists.txt 中就可以，可以传递到子文件夹中
#     2. LINK_DIRECTORIES 对应 gcc/g++ 的 -L 参数,指定库文件的路径
#     3. TARGET_LINK_LIBRARIES 对应 gcc/g++ 的 -l 参数，为了方便使用可以在第2步之后使用一个变量保存用到的库 (SET(......))

# 使用查找包的方式
# FIND_PACKAGE 用来调用预定义在 CMAKE_MODULE_PATH 下的 Find<name>.cmake模块
# FIND_PACKAGE 执行后会赋值的变量:
#     <name>_FOUND
#     <name>_INCLUDE_DIRS 或 <name>_INCLUDES
#     <name>_LIBRARIES 或 <name>_LIBS
#     <name>_DEFINITIONS
message(STATUS "    CMAKE_MODULE_PATH: " ${CMAKE_MODULE_PATH})
FIND_PACKAGE(OpenCV REQUIRED)
IF(NOT OpenCV_FOUND)
    MESSAGE(FATAL_ERROR "OpenCV Not Found!")
ENDIF(NOT OpenCV_FOUND)
message(STATUS "OpenCV library status:")
message(STATUS "    config: ${OpenCV_DIR}")
message(STATUS "    version: ${OpenCV_VERSION}")
message(STATUS "    libraries: ${OpenCV_LIBS}")
message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")

# ======================================================================================================================== 使用外部库  end

# ======================================================================================================================== add test
# CMake 提供了一个称为 CTest 的测试工具。我们要做的只是在项目根目录的 CMakeLists 文件中调用一系列的 add_test 命令
# 启用测试
ENABLE_TESTING()

# 测试程序是否成功运行
ADD_TEST(test_run ${PROJECT_BINARY_DIR}/bin/demo 5 2)

# 测试帮助信息是否可以正常提示
add_test (test_usage ${PROJECT_BINARY_DIR}/bin/demo)
set_tests_properties (test_usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage: .* base exponent")

# 测试 5 的平方
add_test (test_5_2 ${PROJECT_BINARY_DIR}/bin/demo 5 2)

set_tests_properties (test_5_2
 PROPERTIES PASS_REGULAR_EXPRESSION "is 25")

# 测试 10 的 5 次方
add_test (test_10_5 ${PROJECT_BINARY_DIR}/bin/demo 10 5)

set_tests_properties (test_10_5
 PROPERTIES PASS_REGULAR_EXPRESSION "is 100000")

# 测试 2 的 10 次方
add_test (test_2_10 ${PROJECT_BINARY_DIR}/bin/demo 2 10)

set_tests_properties (test_2_10
 PROPERTIES PASS_REGULAR_EXPRESSION "is 1024")

# 上面的代码包含了四个测试。第一个测试 test_run 用来测试程序是否成功运行并返回 0 值。剩下的三个测试分别用来测试 5 的 平方、10 的 5 次方、2 的 10 次方是否都能得到正确的结果。其中 PASS_REGULAR_EXPRESSION 用来测试输出是否包含后面跟着的字符串。

# 如果要测试更多的输入数据，像上面那样一个个写测试用例未免太繁琐。这时可以通过编写宏来实现：
# 定义一个宏，用来简化测试工作
# macro (do_test arg1 arg2 result)
#   add_test (test_${arg1}_${arg2} ${PROJECT_BINARY_DIR}/bin/demo ${arg1} ${arg2})
#   set_tests_properties (test_${arg1}_${arg2}
#     PROPERTIES PASS_REGULAR_EXPRESSION ${result})
# endmacro (do_test)
#  
# # 使用该宏进行一系列的数据测试
# do_test (5 2 "is 25")
# do_test (10 5 "is 100000")
# do_test (2 10 "is 1024")
# ======================================================================================================================== add test end

# ======================================================================================================================== CPack 包
# 构建一个 CPack 安装包
include (InstallRequiredSystemLibraries)
set (CPACK_RESOURCE_FILE_LICENSE
  "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set (CPACK_PACKAGE_VERSION_MAJOR "${Demo_VERSION_MAJOR}")
set (CPACK_PACKAGE_VERSION_MINOR "${Demo_VERSION_MINOR}")
include (CPack)

# 上面的代码做了以下几个工作：
#     1. 导入 InstallRequiredSystemLibraries 模块，以便之后导入 CPack 模块；
#     2. 设置一些 CPack 相关变量，包括版权信息和版本信息，其中版本信息用了上一节定义的版本号；
#     3. 导入 CPack 模块。
# 接下来的工作是像往常一样构建工程，并执行 cpack 命令。
# 生成二进制安装包：
#     cpack -C CPackConfig.cmake
# 生成源码安装包
#     cpack -C CPackSourceConfig.cmake

# ======================================================================================================================== CPack 包 end

MESSAGE(STATUS "-------- MAIN CMAKELISTS MSG END --------")
