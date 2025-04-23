CFLAGS=-I/opt/homebrew/Cellar/googletest/1.16.0/include \
		-L/opt/homebrew/Cellar/googletest/1.16.0/lib

test:
	@echo $(CFLAGS)
	@g++ -std=c++17 test_hello.cc $(CFLAGS) -lgtest -lgtest_main -pthread -o test
	@./test

.PHONY: test