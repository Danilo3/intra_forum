## libft. Как не завалить первый проект?

### Author: [@trhogoro](https://profile.intra.42.fr/users/trhogoro)

### [Original](https://forum.intra.42.fr/topics/19883/messages)

Доброго всем дня.

Проверив пару десятков библиотек у других студентов я наблюдал у большинства схожие ошибки, в связи с чем решил поделиться со всеми этой информацией.
В данном сообщении описаны только те баги, которые я нашел, так что, возможно, к вам лично придёт более внимательный пир и найдёт что-то еще.

Решение конкретной задачи может быть реализовано многими способами, так что не исключены фейлы, основанные исключительно на Вашем особенном подходе. На данный момент самый широкий пул тестов имеет [42filechecker](https://google.gik-team.com/?q=42filechecker)
Рекомендую в первую очередь проверить этим тестом свою библиотеку. Он, к сожалению, не обнаруживает большую часть проверок, предложенных в этом посте. Насколько мне известно, большая часть, а может быть, даже все проверки, приведенные ниже, не проверяются автоматической системой, так что если вы оптимист, можно их проигнорировать и понадеяться на 5 невнимательных и/или сговорчивых проверяющих. В любом случае, проверка своих функций на описанные уязвимости позволит вам защищаться более уверенно и комфортно.


Итак, начнем:

1. Безопасность функций:
* Проверьте на NULL все указатели, которые принимают все функции, кроме первой части. Согласно заданию - функции первой части должны иметь аналогичное поведение, что и оригиналы, многие из них генерируют segmentation fault на входящий NULL, так что мы не можем писать защиту от этого.
Почти все, кого я проверял, имели проверки на NULL указатели на начало строки но почему то забли проверять на NULL указатели на функции. Такая ошибка встречается часто в функция работы со списками (*ft_lstdel, ft_lstdelone, ft_lstmap, ft_lstiter*) и в функцияx работы со строкой (*ft_strmap, ft_strmapi, ft_striter, ft_striteri*).
* Проверка на то, что оба аргумента NULL в функциях *ft_memove, ft_memcpy, ft_memccpy*. ft_memcpy*. Просто сравните поведение своей функции и стандартного аналога в этом случае.
* Переполнение size_t и, как следствие, выделение недостаточного объема памяти, и segmentation fault. Этим страдаeт: *ft_strnew*.
2. Утечки памяти:
* Вне зависимости от поведения вашей функции необходимо убедиться, что во всех возможных ситуациях вы освобождаете с помощью *free* всю память, которую выделили с помощью *malloc* за исключением тех объектов, которые Вы передаёте в вызывающую функцию. Это могут быть или возвращаемые функцией значения, например для прототипа **char \*ft_ft(int param);**, или значения, записанные по указателю на указатель, такие как line в следующем прототипе: **int ft_ft(char **line);**.
Проблемы с утечками памяти чаще всего наблюдаются в функциях *ft_strsplit, ft_lstmap*.
3. Аналогичное поведение
* *ft_atoi*, проверьте примеры *"−9223372036854775828", "−9223372036854775028", "9223372036854775828", "9223372036854775808"*. Не пытайтесь делать костыли для конкретных входящих данных, используйте long int тип для переменной, в которой хранится промежуточное значение и которая проверяется на переполнение.

Всем удачной защиты и успехов в дальнейшем.

P.S. В параграфе про atoi исправил long long int на long int. На наших системах оба эти типа по 8 байт. Но видимо отслеживать переполнение long корректнее.