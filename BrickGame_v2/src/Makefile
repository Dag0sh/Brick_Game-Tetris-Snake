CC := g++
CFLAGS := -Wall -Wextra -Werror -std=c++17 -Wpedantic -g
GCOV_FLAGS := -fprofile-arcs -ftest-coverage
LIB_FLAGS := -L/opt/homebrew/Cellar/check/0.15.2/lib -lcheck -lm -lncurses

OS_NAME = $(shell uname)

ifeq ($(OS_NAME),Linux)
OPEN = xdg-open
endif

ifeq ($(OS_NAME),Darwin)
OPEN = open
endif

PROJECT := main
GUI_CLI := gui/cli/cli.cpp
GUI_QT := gui/desktop/mainwindow.cpp
BUILD_DIR := build
DOCS_DIR := docs
REPORT_DIR := report
DIST_DIR := dist

all : install

install : tetris.a snake.a | build_dir
	$(CC) $(CFLAGS) $(GCOV_FLAGS) $(PROJECT)_cli.cpp controller/controller.cpp $(GUI_CLI) $(BUILD_DIR)/tetris.a -o $(BUILD_DIR)/tetris_cli $(LIB_FLAGS) -DTETRIS=1
	$(CC) $(CFLAGS) $(GCOV_FLAGS) $(PROJECT)_cli.cpp controller/controller.cpp $(GUI_CLI) $(BUILD_DIR)/snake.a -o $(BUILD_DIR)/snake_cli $(LIB_FLAGS) -DSNAKE=1
	rm -rf tmp && mkdir tmp && cd tmp && cmake ../cmake_snake_desktop && make && cp snake_desktop.app/Contents/MacOS/snake_desktop ../build/snake_desktop
	sed -i '' '3s/^/#define TETRIS\n/' controller/controller.h
	rm -rf tmp && mkdir tmp && cd tmp && cmake ../cmake_tetris_desktop && make && cp tetris_desktop.app/Contents/MacOS/tetris_desktop ../build/tetris_desktop
	sed -i '' '3d' controller/controller.h
	rm -rf tmp
	rm -rf $(BUILD_DIR)/*.gcda
	rm -rf $(BUILD_DIR)/*.gcno
	rm -rf $(BUILD_DIR)/tetris.a
	rm -rf $(BUILD_DIR)/snake.a

tetris.a : build_dir
	$(CC) $(CFLAGS) $(GCOV_FLAGS) -c brick_game/tetris/tetris.cpp -o $(BUILD_DIR)/tetris.o
	ar rc $(BUILD_DIR)/tetris.a $(BUILD_DIR)/tetris.o
	ranlib $(BUILD_DIR)/tetris.a
	rm -rf $(BUILD_DIR)/tetris.o

snake.a : build_dir
	$(CC) $(CFLAGS) $(GCOV_FLAGS) -c brick_game/snake/snake.cpp -o $(BUILD_DIR)/snake.o
	ar rc $(BUILD_DIR)/snake.a $(BUILD_DIR)/snake.o
	ranlib $(BUILD_DIR)/snake.a
	rm -rf $(BUILD_DIR)/snake.o

uninstall :
	rm -rf $(DOCS_DIR) $(BUILD_DIR)

rebuild : uninstall install


clean :
	rm -rf $(DOCS_DIR) $(BUILD_DIR) $(DIST_DIR) $(REPORT_DIR) tmp

dvi :
	Doxygen
	cd docs/latex && make all

dist : dist_dir
	tar cf $(DIST_DIR)/tetris-1.0.tar $(BUILD_DIR) $(DOCS_DIR)

test : tetris.a snake.a
	$(CC) $(CFLAGS) $(GCOV_FLAGS) main_tests.cpp brick_game/tetris/tetris_test.cpp brick_game/snake/snake_test.cpp \
	$(BUILD_DIR)/tetris.a $(BUILD_DIR)/snake.a \
	-o $(BUILD_DIR)/tests \
	$(LIB_FLAGS) \
	-L/opt/homebrew/Cellar/googletest/1.14.0/lib -lgtest -lgtest_main -lpthread
	./$(BUILD_DIR)/tests

gcov_report : test
	rm -rf $(REPORT_DIR)
	mkdir $(REPORT_DIR)
	gcovr --html-details $(REPORT_DIR)/coverage.html
	$(OPEN) $(REPORT_DIR)/coverage.html
	rm -rf *.gcda *.gcno *.o *.gcov

build_dir :
	mkdir -p $(BUILD_DIR)

dist_dir :
	mkdir -p $(DIST_DIR)