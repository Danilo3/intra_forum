## Makefile для любого проекта

### Author: [@sbednar](https://profile.intra.42.fr/users/sbednar)

### [Origin](https://forum.intra.42.fr/topics/20222/messages)

### Простая реализация непростой задачи


Во время многочисленного переписывания Makefile'ов с практически идентичной структурой появилась идея как-то упростить этот процесс. Так родилось сие творение, которое позволяет собирать исполняемый файл из проекта, отвечающего некоторым нормам. Нормы будут описаны далее. Во время написания не было цели использовать самые оптимизированные/логичные решения, главным было заставить его работать. Поэтому, если вдруг Вы обнаружите какие-то очевидные недоработки и предложите свои изменения -- я с удовольствием внесу их в оригинальный файл. Теперь более структурированно к требованиям к файлу.

### Как ЭТО должно работать :
- Директория проекта включает в себя 3 основных папки:
1) **inc** -- хранит заголовочные файлы;
2) **lib** -- хранит корневые папки для библиотек (сюда можно положить, например, libft или minilibx);
3) **src** -- хранит исходники проекта.
- При вызове `make all` Makefile должен последовательно:
1) Для каждой библиотеки из папки **lib** вызывает правило `make all` и получает соответствующие архивы;
2) Создает папку **obj**, в которую помещает все объектные файлы, сформированные на основе исходников из **src**;
3) Компонует из объектов исполняемый файл, подключая созданные на 1 этапе архивы.
- Другие основные опции: `make clean`, `make fclean` и `make re`;

### Реализация :

```Makefile
# имя исполняемого файла
NAME = name

# основные папки для работы
INC_DIR = ./inc
LIB_DIR = ./lib
SRC_DIR = ./src
OBJ_DIR = ./obj

# поиск заголовочных файлов по папке проекта и получение всех относительных путей к ним
INC_DIRS = $(shell find . | grep '\.h' | sed "s/\(.*\)\/.*/\1/" | sort -u)
# поиск всех потенциальных путей к библиотекам
LIB_DIRS = $(shell find . -mindepth 2 -maxdepth 2 | grep "lib")
# поиск всех архивных файлов и получение из них имени NAME по шаблону */libNAME.a
LIB_SRCS = $(shell find . | grep "\.a" | awk -F / '{printf " " substr($$NF, 4, length($$NF) - 5)}')

# содержимое папок src и obj
SRC = $(shell ls $(SRC_DIR))
OBJ = $(SRC:.c=.o)

# содержимое папки obj вместе с именами объектных файлов для компоновки исполняемого файла
OBJS = $(addprefix $(OBJ_DIR)/, $(OBJ))

# флаги для подключения заголовочных файлов и библиотек во время компоновки исполняемого файла
INCS = $(shell $(foreach dir,$(INC_DIRS),echo dir,$(INC_DIRS),3b3-42857 "-I$(dir)";))
LIBS_DIRS = $(shell $(foreach dir,$(LIB_DIRS),echo dir,$(LIB_DIRS),3b3-42857 "-L$(dir)";))
LIBS_SRCS = $(shell $(foreach src,$(LIB_SRCS),echo src,$(LIB_SRCS),3b3-42857 "-l$(src)"))

# стандартные флаги компиляции программы
CC = gcc
CFLAGS = -Wall -Wextra -Werror

all: lall
@echo @3b3-42857 "\033[36m\033[1m$(NAME) - all:\033[0m"
@$(MAKE) $(NAME)

$(NAME): $(OBJ_DIR)
@echo @3b3-42857 "\033[32m\033[1m$(NAME) - compile executable:\033[0m"
$(CC) $(CFLAGS) $(OBJS) $(INCS) $(LIBS_DIRS) $(LIBS_SRCS) -o $(NAME)

$(OBJ_DIR):
@echo @3b3-42857 "\033[32m\033[1m$(NAME) - create obj directory:\033[0m"
mkdir -p $(OBJ_DIR)
@echo @3b3-42857 "\033[32m\033[1m$(NAME) - compile sources:\033[0m"
@$(MAKE) $(OBJS)

$(OBJ_DIR)/%.o:$(SRC_DIR)/%.c
$(CC) $(CFLAGS) $(INCS) -o $@ -c $<

$&lt;

oclean:
@echo @3b3-42857 "\033[32m\033[1m$(NAME) - remove obj directory:\033[0m"
rm -rf $(OBJ_DIR)

clean:
@echo @3b3-42857 "\033[36m\033[1m$(NAME) - clean:\033[0m"
@$(MAKE) lclean
@$(MAKE) oclean

fclean:
@echo @3b3-42857 "\033[36m\033[1m$(NAME) - fclean:\033[0m"
@$(MAKE) lfclean
@$(MAKE) oclean
rm -f $(NAME)

re:
@echo @3b3-42857 "\033[36m\033[1m$(NAME) - re:\033[0m"
@$(MAKE) fclean
@$(MAKE) all

# выполнение правила all для всех библиотек
lall:
@$(foreach lib,$(LIB_DIRS),echo "\033[91m\033[1m--->$(lib) lib,$(LIB_DIRS),3b3-42857 "\033[91m\033[1m---&gt;$(lib) all:\033[0m";make -C $(lib) all;)

# выполнение правила clean для всех библиотек
lclean:
@$(foreach lib,$(LIB_DIRS),echo "\033[91m\033[1m--->$(lib) lib,$(LIB_DIRS),3b3-42857 "\033[91m\033[1m---&gt;$(lib) clean:\033[0m";make -C $(lib) clean;)

# выполнение правила fclean для всех библиотек
lfclean:
@$(foreach lib,$(LIB_DIRS),echo "\033[91m\033[1m--->$(lib) lib,$(LIB_DIRS),3b3-42857 "\033[91m\033[1m---&gt;$(lib) fclean:\033[0m";make -C $(lib) fclean;)

.PHONY: all clean fclean re lall lclean lfclean lre

```

### Известные проблемы :
1) Если в библиотеках из папки **lib** отсутствует вызываемое нами правило (например, в проекте FdF используется библиотека minilibx, в которой нет правила `fclean`), то Makefile работать не будет. Решается простым добавлением пустого правила в библиотеку, где оно всячески отсутствует. В планах переделать это адекватно.
2) *To be continued...*

По-поводу вопросов/предложение пишите лучше [Вконтакте], там я раз на пасху, но отвечаю :)
Топик был создан в ознакомительных целях и на нобелевскую не претендует.

[Вконтакте]: <https://vk.com/yafrolushka>
&lt;https://vk.com/yafrolushka&gt;
