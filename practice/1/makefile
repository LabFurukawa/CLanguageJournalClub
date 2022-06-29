.PHONY : clean run

CC := gcc
CFLAGS := -Iinclude

build/p1.out : src/source.c build/basic.o
	@mkdir build/
	$(CC) $(CFLAGS) -o build/basic.out src/source.c build/basic.o

build/basic.o : src/basic.c include/basic.h
	@mkdir build/
	$(CC) $(CFLAGS) -o build/basic.o src/basic.cpp

clean :
	rm -r build

run : 
	./build/basic.out

