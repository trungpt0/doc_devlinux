.PHONY: obj shared

PRJ_NAME := hello
SHARED_LIB_NAME := libhello.so

CUR_DIR := .
INC_DIR := $(CUR_DIR)/inc
SRC_DIR := $(CUR_DIR)/src
OBJ_DIR := $(CUR_DIR)/obj
BIN_DIR := $(CUR_DIR)/bin

LIB_DIR := $(CUR_DIR)/libs
LIB_STATIC_DIR := $(LIB_DIR)/static
LIB_SHARED_DIR := $(LIB_DIR)/shared

CC := gcc
C_FLAGS := -c -fPIC -o

obj:
	$(CC) $(C_FLAGS) $(OBJ_DIR)/main.o $(CUR_DIR)/main.c -I $(INC_DIR)
	$(CC) $(C_FLAGS) $(OBJ_DIR)/hello1.o $(SRC_DIR)/hello1.c -I $(INC_DIR)
	$(CC) $(C_FLAGS) $(OBJ_DIR)/hello2.o $(SRC_DIR)/hello2.c -I $(INC_DIR)

shared:
	$(CC) -shared $(OBJ_DIR)/* -o $(LIB_SHARED_DIR)/$(SHARED_LIB_NAME)

install:
	install $(LIB_SHARED_DIR)/$(SHARED_LIB_NAME) /usr/lib 

all: obj shared install
	$(CC)  $(OBJ_DIR)/main.o -L$(LIB_SHARED_DIR) -lhello -o $(BIN_DIR)/$(PRJ_NAME)

clean: 
	rm -rf $(OBJ_DIR)/*
	rm -rf $(LIB_SHARED_DIR)/*
	rm -rf $(BIN_DIR)/*