## Libft puzzles. Загадки первой библиотеки. 

### Authour: @ftothmur

### Good afternoon friends.

**One of the most exciting parts of the libft project was the quest: to determine the optimal order of designing functions. This worries me so far, and I decided to share my work with you.In essence, this is a realization of the principle ["DRY": Do not repeat yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)**

#### What I discovered.
**First** that the code of many functions is repeated. The results of the analysis of the convergence I put in **table 1**. “location boundary” - the boundaries of the memory area. It is represented in the table in two types: “buffer size” (this is something similar, but not equal to the array), and “string” (this is a memory block whose boundaries are defined by the separator '\ 0'). If this indicator is empty, then we are dealing with a section of memory that is defined differently: for example, with a basic data type, or a related list, the definition of the boundaries of which is too different to fit into this scheme. "Ptr arithmetic" speaks of moving the pointer inside a function. It is almost always associated with cycles. An “assignment” defines an assignment, i.e. a change in the value of some variables within a function. The “system” metrics block includes all possible system calls: “allocation” is the allocation of memory represented by “free” and “malloc”, as well as the input and output system “IO”, of which only one function is available within this project: “write ". More similar blocks can be found in other students for exam preparation.

**Secondly**, it is possible to distinguish more complex relations, for example, special cases (A ⊆ B. A are a call to function B, when at least one parameter becomes a constant). You can also talk about ⋂ (intersection), ∪ (union), \ (strict or, XOR), = (equality). I leave these concepts work your intuition. (See **Figure 1**)

Almost all functions of str have code similar to ft_strlen: looking for the end of the line. There are a number of functions that work with the separator, only in its quality it is used or not the end of the line, or not only it: ft_memchr, ft_strsplit, and others. Most of the other functions of mem and some others are dependent on the size of the buffer transferred to them.
Some functions like ft_strnlen work simultaneously with both the size of the buffer and the end of the line.

Several functions are not explicitly presented in the assignment, but can be very useful if you write them. By the way, they can also serve as the fact that you put in the "bonus" section for protection. (See **Figure 2**).

## Shortly.
### I bring to your attention a **table 2**, the last column of which contains the order of designing functions recommended by me. Remember that I wrote about the general and special case, about the similarities of functions. Use this information.
___

### Добрый день, друзья.

**Одна из самых волнующих меня частей проекта «libft» был квест: определить оптимальынй порядок проектирования функций. Это меня волнует до сих пор, и я решил поделиться с вами своими наработками. По сути это реализация принципа ["Не повторяй себя" или по-аглийски DRY](https://ru.wikipedia.org/wiki/Don%E2%80%99t_repeat_yourself)"**

"DRY"](https://ru.wikipedia.org/wiki/Don%E2%80%99t_repeat_yourself)"**

#### Что я обнаружил.
**Во-первых** то, что код многих функций повторяется. Результаты анализа сходситва я поместил в **таблицу 1**. «location boundary» – границы участка памяти. Он в таблице представлен двух видами: «buffer size» (это нечто подобное, но не равное массиву), и «string» (это блок памяти границы которого определены разделителем '\0'). Если этот показатель пуст, значит мы имеем дело с участком памяти определяемым иначе: например с базовым типом данных, или связанным перечнем, определение границ которых слишком отличается, чтобы укладываться в эту схему. «ptr arithmetic» говорит о перемещении указателя внутри функции. Почти всегда это связано с циклами. «assignment» определяет назначение, т. е. т.&nbsp;е. смену значения некоторых переменных внутри функции. Блок показателей «system» включает все возможные системные вызовы: «allocation» – выделение памяти, представленный «free» и «malloc», а также системой вводы и вывода «IO», из которых в пределах данного проекта доступна только одна функция: «write». Ещё подобные же блоки можно обнаружитьв рекомендацих других студентво для подготовки к экзаменам.

**Во-вторых**, можно выделить более сложные отношения, например частные случаи (А ⊆ Б. А являются вызовом функции Б, когда не менее одного параметра становятся константами). Можно также говорить о ⋂ (пересечении), ∪ (объединении), \ (строгом или, XOR), = (равенстве). Оставляю эти понятия работе вашей интуиции. (См. **рисунок 1**)

Почти все функции str имеют код подобный ft_strlen: ищут конец строки. Есть ряд функций, работающих с разделителем, только в его качестве используется или не конец строки, или не только он: ft_memchr, ft_strsplit и другие. Большинство прочих функций mem и некоторые другие зависимы от передаваемого им размера буфера.
Некоторые функции типа ft_strnlen работают одновременно и с размером буфера и с концом строки.

Несколько функций не представлены в задании явно, но могут быть очень полезны, если вы их напишите. Кстати они же могут послужить тем, что вы положите в раздел «бонус» для защиты. (См. **рисунок 2**).

## Коротко.
### Я предлагаю вашему вниманию **таблицу 2**, последний столбец которой содержит рекомендуемый мной порядок проектирования функций. Помните о том, что я писал про общий и частный случай, про сходства функций. Используйте эту информацию.
___
> &gt; _**Picture 1. Рисунок 1**_
> &gt; *ft_bzero ⊆ ft_memset, ft_strcpy ⊆ ft_memccpy, ft_strcmp ⊆ ft_strncmp, ft_strcat ⊆ ft_strncat, ft_strstr ⊆ ft_strnstr, ft_strequ ⊆ ft_strnequ, ft_ischar ⊆ ft_isalpha, (ft_isupper ∪ ft_islower) = ft_isalpha, (ft_isdigit ∪ ft_isalpha) = ft_isalnum ⊆ ft_isprint, (ft_isnotprint ∪ ft_isprint) = ft_isascii, ft_isspace ⊆ ft_isascii, ft_strnew ⊆ ft_memalloc, ft_strdel ⊆ ft_memdel, ft_strdup ⊆ ft_strjoin, ft_putnbr ⊆ ft_putnbr_fd, ft_putchar ⊆ ft_putchar_fd, ft_putstr ⊆ ft_putstr_fd ⊆ ft_putchar_fd, ft_lstdelone ⊆ ft_lstdel, ft_putendl ⊆ ft_putendl_fd ⊆ ft_putchar_fd, ft_strlen ⊆ ft_strnlen, ft_malloc ⊆ ft_realloc ⊆ ft_memjoin, (ft_itoa ⋂ ft_putnbr_fd) = ft_itostr, ft_strnequ \ ft_strncmp = logic_op().
Combinations (Комбинации): ft_strncpy : ft_strnlen + ft_strcpy + ft_bzero, ft_memccpy : ft_memcpy + ft_memchr, ft_memmove : ft_memcpy + ft_memrcpy, ft_memchr : ft_ischar etc. (и т.д.)*

___
> &gt; **_Picture 2. Рисунок 2_**
> &gt; **ft_ischar**. Takes two character values, returns 1 if the characters are equal, and 0 otherwise. Принимает два символьных значения, возвращает 1, если символы равны, и иначе 0.
> &gt; **ft_islower**. Is an ASCII lowercase letter. Является ли строчной буквой из множества ASCII
> &gt; **ft_isnotprint**. Denial ft_isprint. Отрицание ft_isprint.
> &gt; **ft_isspace**. Whether the input character is a void character is one of 5 from ASCII set. Является ли входной символ знаком пустоты, одним из 5 из множества ASCII.
> &gt; **ft_isupper**. Is it an ASCII capital letter? Является ли заглавной буквой из множества ASCII?
> &gt; **ft_itostr**. Accepts an integer value and a pointer to a string. Converts an integer to a string. Принимает целое значение и указатель на строку. Преобразует целое в строку.
> &gt; **ft_memjoin**. Some combination of ft_realloc and ft_strjoin, but only the boundaries of the memory sections are the sizes of the buffers. Некоторое объединение ft_realloc и ft_strjoin, но только границами участков памяти выступают размеры буферов.
> &gt; **ft_memrcpy**. Copies, moving from the last character to the first. Копирует, двигаясь от последнего символа к первому.
> &gt; **ft_realloc**. The handwritten implementation of the function of the same name, only this can be better protected, and then there is no way to do without passing the size of the buffer. Рукописная реализация одноимённой функции, только эту можно лучше защитить, и тут никак не обойтись без передачи размера буфера.
>**ft_strnlen**. &gt;**ft_strnlen**. Returns the minimum value of the length of the string and the maximum size (buffer). Возвращает минимальное значение из длины строки и максимального размера (буфера).
___
___
### Table 2. Таблица 2.
| name имя      | part часть    | order порядок |
| ------------- | ------------- | ------------- |
|ft_ischar      | Expansion	    |    1          |      
|ft_isdigit	    | Part1	        |    1          |   
|---------------|---------------|---------------|