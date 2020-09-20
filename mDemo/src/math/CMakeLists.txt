MESSAGE(STATUS "-------- DIR: SRC/MATH --------")

# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_LIB_SRCS 变量
AUX_SOURCE_DIRECTORY(. DIR_LIB_SRCS_MATH)

# 生成链接库 通过add_library指定库的名字，生成之后名字会变成 lib<name>.<a/so>
# STATIC/SHARED/MODULE  静态库/共享库/在使用dyld的系统有效，如果不支持dyld，则被当作SHARD对待
# 如果希望输出的多个库名字一样，参考 SET_TARGET_PROPERTIES 指令的用法
ADD_LIBRARY (mathFunctions STATIC ${DIR_LIB_SRCS_MATH})
ADD_LIBRARY (mathFunctions2 SHARED ${DIR_LIB_SRCS_MATH})
ADD_LIBRARY (mathFunctions3 MODULE ${DIR_LIB_SRCS_MATH})

MESSAGE(STATUS "-------- DIR: SRC/MATH END --------")
