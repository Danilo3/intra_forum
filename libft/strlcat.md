## Libft. Strlcat, function they didn't want to.[ENG/RUS]

### Author: [@ftothmur](https://profile.intra.42.fr/users/ftothmur)
### [Origin](https://forum.intra.42.fr/topics/20387/messages)

> «While this may seem somewhat confusing, it was done...»

It will be about how to figure out what needs to be done to make the working version of ft_strlcat() appear in your library, and at the same time understand why this is so. Many who read this article have already tried to imagine what this function is doing - this is an unforgettable feeling of perplexity, heavily seasoned with annoyance and frustration from the first attempts to implement the working code ft_strlcat(). Know that probably not only you were unhappy, it is difficult to say that this function is the most nasty: the documentation or the intricacy of an idea. Maybe this circumstance makes this function an outcast of standard libraries and problematic when transferring code to other machines. I have long and carefully tried to figure out how what is written in man agrees with what the function does. The blessing now, as time passes, the information in me has settled, and I try to state the essence briefly and clearly.

First, I will write out everything that the man says exactly about this function, excluding information not related to:

> size_t strlcat(char *dst, const char *src, size_t size);
>
> Description. Strlcat() functions concatenate strings. They are designed to be safer, more consistent, and less error prone replacements for strncat(3). Unlike this function, strlcat() take the full size of the buffer (not just the length) and guarantee to NUL-terminate the result (as long as size is larger than 0 or, in the case of strlcat(), as long as there is at least one byte free in dst). Note that a byte for the NUL should be included in size. Also notestrlcat() only operate on true ''C'' strings. This means that strlcat() both src and dst must be NUL-terminated. The strlcat() function appends the NUL-terminated string src to the end of dst. It will append at most size - strlen(dst) - 1 bytes, NUL-terminating the result.
>
> &gt; size_t strlcat(char *dst, const char *src, size_t size);
&gt;
&gt; Description. Strlcat() functions concatenate strings. They are designed to be safer, more consistent, and less error prone replacements for strncat(3). Unlike this function, strlcat() take the full size of the buffer (not just the length) and guarantee to NUL-terminate the result (as long as size is larger than 0 or, in the case of strlcat(), as long as there is at least one byte free in dst). Note that a byte for the NUL should be included in size. Also notestrlcat() only operate on true ''C'' strings. This means that strlcat() both src and dst must be NUL-terminated. The strlcat() function appends the NUL-terminated string src to the end of dst. It will append at most size - strlen(dst) - 1 bytes, NUL-terminating the result.
&gt;
&gt; Return Values. The strlcat() function returns the total length of the string it tried to create. That means the initial length of dst plus the length of src. While this may seem somewhat confusing, it was done to make truncation detection simple. Note, however, that if strlcat() traverses size characters without finding a NUL, the length of the string is considered to be size and the destination string will not be NUL-terminated (since there was no space for the NUL). This keeps strlcat() from running off the end of a string. In practice this should not happen (as it means that either size is incorrect or that dst is not a proper ''C'' string). The check exists to prevent potential security problems in incorrect code.

The first thing that is obvious is that this function deals with concatenation, that is, writing the characters of one string after all the characters of the first. (It’s worth remembering that a string is used in two values, and most often a string is all characters up to the end of a line. It becomes easier to understand what an empty string is, although it takes 1 byte, not 0. Well, the second value This is the memory that contains the string and its end). The second thing that attracts attention is that not a single fantasy is enough to find it possible to consider that the type of the return value is directly related to the main work of this function. Leave the magicians their magic, there is really no direct connection. Third, this concept is called buffer in English. It should be translated as a synonym for the maximum available space allocated for the result of the operation. Think of the name yourself in one, well, at least two words. In general, a place for the result. And yet, this is probably already the fourth, we will definitely talk about this in other notes of the bugfinder - one of my most important concepts - expectations from work. These are the expectations from the function, and the expected result and the associated input values. This thing between the lines or explicitly, but inevitably pursues every idea of ​​each programmer. Probably the more explicit this idea is, the more well, popularly, the essence of the program, functions, etc. is stated. So, this function clearly states that a certain result is expected, and its full length will be returned. There will have to be detectives, and an intuition to imagine what is at stake. The function deals with concatenation. This is the main thing that it must submit after it retires. And it returns a pale glimpse of its colorful life - a numerical display of the result.

I will immediately answer the question that made me spend a whole day on one of the implementations of the function, and then change my mind and cut everything down as the result of the incarnation did not agree with the tests: “strlcat () ... guarantee to NUL-terminate the result And the destination string will not be NUL-terminated. Send your letters with the answers as it may be, but for now I will move on. I took the implementation of this function in the network. I looked at the FreeBSD implementation, but we have the same Macs, because here's an even shorter version from Apple (catch the links: [1][FreeBSD], [2][Apple]). I will not torment, the essence is this: if the size of the place without 1 byte is less than the length of the destination line, no concatenation will be made. And nobody already follows the first character of the destination line outside the place - 1 (minus 1 for the end of line character). And no one checks this C string (by definition, a C string has a line ending), or just some other sequence of characters. By the way, let's call it for definiteness the first case. And there will actually remain only two more cases.

The second case is the one in which the source line does not fit into the allotted space to join it to the destination line. Then everything that can be copied, with the exception of 1 byte at that very guaranteed ending, at the end of the line. Well, the rest is not copied. Oh, and the game is going on with terms, but at the end I will try again to summarize everything and very briefly define everything. Well, the third case is the one in which there was enough space for a full concatenation. The boundary case, when it was enough to put it in the way where you want.

So, well, this is what the function does to completion, and what does it return ?! And that's what! In the first case, the maximum space length (the value of the size variable) plus the length of the source string. By the way, this is where the gullibility with the expectation of a null-terminated string can fail and the program runs under the threat of a segmentation error. But relax, there is little that can be done. In the other two cases, the length of the destination line is less than the length of the place, and its length (destination line) is used, and the length of the source line is still closely following us.

What is the point of sharing the results and the return value, you ask. Well, this is a way to insure correct work. In all cases, when the returned result is more than the space allocated for the operation, the concatenation is not fully completed. Perhaps I do not know about it, to distinguish between incorrect cases among themselves, someone could check whether the last byte is equal to the end of the line.

### To summarize
**_##### The **_The function is designed to glue two lines together and return an indicator of work results. The third parameter is responsible for the marginal place reserved for the result. If it is enough to cram not only the destination line, but at least something from the source line, then gluing occurs, and the resulting string is guaranteed to be zero terminated. If not, no one even remembered the gluing process at the end of the line. In the first case, the sum of the lengths of the input lines will be returned, in the second case the sum of the length of the allocated space and the destination line. And all this time we hoped that the program did not end unexpectedly when the destination line turned out to be anything but the C line._**

Try for clarification to conduct such [tests][Тесты взяты отсюда]:

Before : || After :
dst | src | size || dst | return
------------------------||--------------------
dst\0 | src\0 | 0 || dst\0 | 3
dst\0 | src\0 | 1 || dst\0 | 4
dst\0 | src\0 | 2 || dst\0 | 5
dst\0 | src\0 | 3 || dst\0 | 6
dst\0 | src\0 | 4 || dst\0 | 6
dst\0 | src\0 | 5 || dsts\0 | 6
dst\0 | src\0 | 6 || dstsr\0 | 6
dst\0 | src\0 | 7 || dstsrc\0 | 6
dst\0 | src\0 | 8 || dstsrc\0 | 6

___

«While this may seem somewhat confusing, it was done...»

Речь пойдёт о том, как разобраться, что нужно сделать, чтобы рабочая версия ft_strlcat() появилась в вашей библиотеке, и при этом понимать почему именно так. Многие, кто читают эту статью, уже попытались представить, чем же занимается эта функция, – это незабываемое чувство озадаченности густо приправленное досадой и разочарованием от первых попыток реализовать код работающей ft_strlcat(). Знайте, что вероятно не только вы оказались недовольны, трудно сказать, что в этой функции более всего скверное: документация или замысловатость идеи. Может быть именно это обстоятельство делает эту функцию изгоем стандартных библиотек и проблемной при переносе кода на другие машины. Я долго и со всей тщательностью пытался разобраться как то, что написано в man, согласуется с тем, что делает функция. Благо теперь, по прошествии времени, информация во мне улеглась, и я пытаюсь кратко и ясно изложить суть.

Сперва я выпишу всё, что сообщает man ровно об этой функции, исключив сведения её не касающиеся:

> size_t strlcat(char *dst, const char *src, size_t size);
>
> Description. Strlcat() functions concatenate strings. They are designed to be safer, more consistent, and less error prone replacements for strncat(3). Unlike this function, strlcat() take the full size of the buffer (not just the length) and guarantee to NUL-terminate the result (as long as size is larger than 0 or, in the case of strlcat(), as long as there is at least one byte free in dst). Note that a byte for the NUL should be included in size. Also notestrlcat() only operate on true ''C'' strings. This means that strlcat() both src and dst must be NUL-terminated. The strlcat() function appends the NUL-terminated string src to the end of dst. It will append at most size - strlen(dst) - 1 bytes, NUL-terminating the result.
>
> &gt; size_t strlcat(char *dst, const char *src, size_t size);
&gt;
&gt; Description. Strlcat() functions concatenate strings. They are designed to be safer, more consistent, and less error prone replacements for strncat(3). Unlike this function, strlcat() take the full size of the buffer (not just the length) and guarantee to NUL-terminate the result (as long as size is larger than 0 or, in the case of strlcat(), as long as there is at least one byte free in dst). Note that a byte for the NUL should be included in size. Also notestrlcat() only operate on true ''C'' strings. This means that strlcat() both src and dst must be NUL-terminated. The strlcat() function appends the NUL-terminated string src to the end of dst. It will append at most size - strlen(dst) - 1 bytes, NUL-terminating the result.
&gt;
&gt; Return Values. The strlcat() function returns the total length of the string it tried to create. That means the initial length of dst plus the length of src. While this may seem somewhat confusing, it was done to make truncation detection simple. Note, however, that if strlcat() traverses size characters without finding a NUL, the length of the string is considered to be size and the destination string will not be NUL-terminated (since there was no space for the NUL). This keeps strlcat() from running off the end of a string. In practice this should not happen (as it means that either size is incorrect or that dst is not a proper ''C'' string). The check exists to prevent potential security problems in incorrect code.

Первое, что очевидно, эта функция занимается конкатенацией, т. е. т.&nbsp;е. записыванием символов одной строки после всех символов первой. (Тут стоит вспомнить, что слово "строка" используется в двух значениях, и чаще всего "строка" – это все символы до знака конца строки (не включаяч его). Отсюда становиться проще понять, что такое пустая строка, хотя она занимает 1 байт, а не 0. Ну а второе значение, это тот участок памяти, который содержит строку и её конец). Второе, что обращает на себя внимание, что не хватит силы ни одной фантазии, чтобы найти возможным счесть, что тип возвращаемого значения прямо связан с основной работой этой функции. Оставьте магию волхвам, тут действительно нет прямой связи. Третье, это понятие названное по-английски buffer. Его стоит перевести как синоним предельного доступного места выделенного для результата операции. Сами придумайте название в одно, ну хотя бы два слова. В общем место под результат. И ещё, это пожалуй уже четвёртое, мы об этом обязательно поговорим в других заметках багоискателя (bugfinder) – одно из самых, на мой взгляд, важных понятий – ожидания от работы. Это и ожидания от работы функции, и ожидаемый результат и связанные с этим входные значения. Эта вещь между строк или явно, но неизбежно преследует каждый замысел каждого программиста. Наверное, тем более этот замысел явный, чем более хорошо, популярно, изложена суть программы, функции и т. д. т.&nbsp;д. Так вот в этой функции явно сказано о том, что ожидается некий результат, а его полная длина и будет возвращена. Тут придётся стать детективами, и интуицией представить о чём идёт речь. Функция занимается конкатенацией. Это то основное, что она должна предъявить после того, как уйдёт на покой. А возвращает она бледный отблеск своей красочной жизни – численное отображение результата.

Сразу отвечу на тот вопрос, который заставил меня целый сутки потратить на одну из реализаций функции, а потом передумать и срубить всё под корень, так как результат воплощения не сходился с тестами: «strlcat() ... guarantee to NUL-terminate the result» и «the destination string will not be NUL-terminated». Присылайте свои письма с ответами как это может быть, а я пока буду двигаться дальше. Я взял реализацию этой функции в сети. Я рассматривал реализацию от FreeBSD, но у нас же маки, потому вот вам даже более короткая версия от Apple (ловите ссылки: [1][FreeBSD], [2][Apple]). Не буду томить, суть такова: если размер места без 1 байта меньше длины строки назначения, то не будет произведено никакой конкатенации. И за первым символом строки назначения, выходящим за пределы места - 1 (минус 1 для знака конца строки) уже никто не следит. И никто не проверяет это строка Си (по определения строка си имеет окончание строки), или просто какая-то иная последовательность символов. Кстати, давайте это для определённости назовём первым случаем. И останется на самом деле-то ещё всего лишь два случая.

Второй случай, это тот, в котором строка источника не умещается в отведённое пространство для присоединения её к строке назначения. Тогда копируется всё что может, за исключением 1 байта на то самое гарантированное окончание, на конец строки. Ну а остальное не копируется. Ох и дичь твориться с терминами, но я в конце ещё раз постараюсь всё подытожить и очень коротко всё обозначить. Ну а третий случай, это тот, в котором хватило места для полной конкатенации. Граничный случай, когда хватило тютелька в тютельку относите куда хотите.

Так, ну это то, что функция делает до завершения, а что же она возвращает?! А вот что! В первом случае – максимальную длину места (значение переменной size) плюс длину строки источника. Кстати вот тут то, доверчивость с ожиданием нуль терминированной строки может подвести и работа программы под угрозой ошибки сегментации. Но расслабьтесь, тут мало что можно сделать. В остальных двух случаях длина строки назначения оказывается меньше длины места, и используется именно её (строки назначения) длина, а длина строки источника всё также неотступно следует за нами.

Какой в этом смысл так разделять результаты и возвращаемое значение, спросите вы. Ну это способ страховки верной работы. Во всех случаях, когда возвращаемый результат больше выделенного для операции места – конкатенация прошла не полностью. Возможно, я не знаю об этом, различить неверные случаи между собой, кто-то мог бы проверив равен ли последний байт концу строки.

### Подытожим.
**_##### Функция **_Функция призвана склеивать две строки и возвращать показатель результатов работы. Третий параметр отвечает за предельное место, отведённое под результат. Если его хватает, чтобы впихнуть не только строку назначения, но и хоть что-то от строки источника, то склеивание происходит, а результирующая строка гарантировано нуль терминирована. Если нет, то склеивание даже не пытались начать и про конец строки никто и не вспоминает. В первом случае вернут сумму длин входных строк, во втором сумму длины выделенного места и строки назначения. И всё это время мы надеялись, что программа не завершиться неожиданно, когда строка назначения оказалась чем угодно, только не строкой Си._**

Попробуйте для уяснения провести такие [тесты][Тесты взяты отсюда]:

Before : || After :
dst | src | size || dst | return
------------------------||--------------------
dst\0 | src\0 | 0 || dst\0 | 3
dst\0 | src\0 | 1 || dst\0 | 4
dst\0 | src\0 | 2 || dst\0 | 5
dst\0 | src\0 | 3 || dst\0 | 6
dst\0 | src\0 | 4 || dst\0 | 6
dst\0 | src\0 | 5 || dsts\0 | 6
dst\0 | src\0 | 6 || dstsr\0 | 6
dst\0 | src\0 | 7 || dstsrc\0 | 6
dst\0 | src\0 | 8 || dstsrc\0 | 6

___
[FreeBSD]: https://github.com/lattera/freebsd/blob/master/sys/libkern/strlcat.c
[Apple]: https://opensource.apple.com/source/Libc/Libc-825.26/string/strlcat.c.auto.html
[Тесты взяты отсюда]: https://stackoverflow.com/questions/33154740/strlcat-is-dst-always-nul-terminated-what-are-size-and-the-returned-value
