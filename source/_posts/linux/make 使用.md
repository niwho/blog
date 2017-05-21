title: Makefile模板 
date: 2014-10-21 20:55:10
tags:
- program
- linux
- make 
categories: 
- program
---

#Makefile Template
###使用：直接放到工程目录下， 根据工程添加 可能修改INC LIBS 甚至编译选项 C*FLAGS ,然后make即可
<!--more-->

    BIN = test
    CC = gcc
    CPP = g++
    #这里只加入库头文件路径及库路径 根据工程添加 可能修改INC LIBS 甚至编译选项 C*FLAGS 
    INCS = -I"/usr/local/include"
    LIBS = -L"/usr/local/lib"
    SUBDIRS =
    #生成依赖信息时的搜索目录，比如到下列目录中搜索一个依赖文件(比如.h文件)，例如 -I"./***/" -I"./base/"
    DEFINC =
    #给INCS加上依赖搜索路径，分开写可能会产生不一致情况，而且繁琐
    INCS += $(DEFINC)
    #
    #maintest.c tree/rbtree.c  多了子目录，那就直接添加 目录/*.c即可   所有的源文件--  .c文件列表
    CSRCS = $(wildcard ./*.c)
    CPPSRCS = $(wildcard ./*.cpp)
    #SRCS += $(wildcard ./../base/*.c)
    #SRCS += $(wildcard ./*.c ./src/*.c ./src/base/*.c ./src/base/tree/*.c ./src/mem/*.c)

    #所有的.o文件列表
    COBJS := $(CSRCS:.c=.o)
    CPPOBJS := $(CPPSRCS:.cpp=.o)

    #编译选项，可默认使用
    CFLAGS += $(INCS)
    CFLAGS += -O2 -Wall -fomit-frame-pointer -g -ansi
    CPPFLAGS += $(INCS)
    CPPFLAGS += -O2 -Wall -g
    #-g 生成调试信息
    #-pedantic参数与-ansi一起使用 会自动拒绝编译非ANSI程序
    #-fomit-frame-pointer 去除函数框架
    #-Wmissing-prototypes -Wstrict-prototypes 检查函数原型
    #针对每个.c文件的.d依赖文件列表
    CDEF = $(CSRCS:.c=.d)
    CPPDEF = $(CPPSRCS:.cpp=.d)

    all:$(BIN)

    #自动生成依赖，并且*.d自动更新，参照make写法
    $(CDEF) : %.d : %.c
        set -e;rm -f $@; \
        $(CC) -MM  $< > $@.$$$$; \
        sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@; \
        rm -f $@.$$$$
    $(CPPDEF) : %.d : %.cpp
        set -e;rm -f $@; \
        $(CC) -MM  $< > $@.$$$$; \
        sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@; \
        rm -f $@.$$$$

    # 链接为最终目标
    $(BIN) : $(COBJS) $(CPPOBJS)
        $(CPP) -o $(BIN) $(COBJS) $(CPPOBJS) $(LIBS)
    #rm -f $(COBJS)
    #rm -f $(CPPOBJS)
    #rm -f $(CDEF)
    #rm -f $(CPPDEF)
    #
    #导入依赖
    sinclude $(CDEF)
    sinclude $(CPPDEF)

    .PHONY:clean cleanall
    #清除所有目标文件以及生成的最终目标文件
    #保留依赖*.d文件
    clean:            
        rm -f $(BIN) 
        rm -f $(COBJS) 
        rm -f $(CPPOBJS)
    #清楚除源文件意外的所有编译生成的文件
    cleanall:
        rm -f $(CDEF) $(CPPDEF)
        rm -f $(BIN) $(COBJS) $(CPPOBJS)

