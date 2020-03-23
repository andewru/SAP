BADI
====

[SAP ABAP - BADI](https://www.youtube.com/watch?v=mlFQbBtfDGQ&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=61)


- BADI - Business Add Ins - это бизнес расширения похожие на Customer Exits. BADI позволяют вклинить код пользовательских расширений в стандартные САП программы не прибегая к модификации оригинального кода программы.
- BADI внедряются на основе ООП подхода программирования
- технически BADI это интерфейс
- каждый BADI содержит метод реализации называемый как BADI определение
- нужно создать класс для написания в нем кода реализующего методы объявленные в BADI реализации
- SE18 - для создания BADI Definition - сначала нужно создать объявление BADI
- - имя интерфейса BADI должно быть например:  IF_EX_<BADI_NAME>
- SE19 - для создания BADI implementation - затем создаем реализацию
- - имя класса BADI должно быть например:  ZCL_EX_<IMPLEMENTATION NAME>
- удобство BADI
- - можно создать множество реализаций для одного BADI Definition
- - BADI удобнее чем проект расширения т.к. пследний может использоваться только в одном месте, а BADI в нескольких местах(?)

## BADI Types

[SAP ABAP - BADI Types & Properties](https://www.youtube.com/watch?v=qlrtVRQIKKk&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=62)

1. Single implementation BADI
    - имеет только одну реализацию (один класс реализации)
2. Multiple implementation BADI
    - имеет несколько классов реализации
    - по умолчанию все реализации могут быть запущены
    - мы не можем контролировать последовательность исполнения множественных реализация
3. Filter BADI
    - этот тип имеет значение для фильтра и только те реализации выполняются, которые удовлетворяют условию фильтра
    - примеры таких BADI: LAND1, BUKRS, WERKS
4. Custom BADI (редко)


## BADI Properties

![qownnotes-media-xHKaIW](../media/137.png)

- checkbox Within SAP - BADI используется только SAP
- если checkbox Multiple отмечен, то это multiple BADI
- если checkbox Multiple не отмечен, то это single BADI
- если checkbox Filter-Depended отмечен, то это filter BADI
- - нужно тогда определить тип фильтра, например land1, bukrs, werks


## Finding the BADI for a Tcode

[SAP ABAP - Finding the BADI for a Tcode](https://www.youtube.com/watch?v=ye6zG2bvbg8&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=63)

### Using class CL_EXITHANDLER

`CL_EXITHANDLER` имеет метод `GET_INSTANCE` для поиска списка BADIs для транзакции

- идем в `SE24`
- задаем имя класса `CL_EXITHANDLER` и жмем показать
- дважды кликаем на методе `GET_INSTANCE` и ставим точку остановки внутри метода `GET_CLASS_BY_INTERFACE`  на строке `CALL METHOD cl_exithandler=>get_class_name_by_interface`

![qownnotes-media-szakpm](../media/13239.png)

- запускаем нужную нам транзакцию и перейдя в ходе выполнения на точку остановки смотрим в переменных искомую BADI

#### Example to find list of BADIs for XK02

Бизнес требования - при сохранении дебитора в xk02 сделать проверку на не пустой регион для страны German

- `SE24` и задать имя класса `CL_EXITHANDLER`

![qownnotes-media-ESsEfc](../media/31225.png)

- зайти в метод `GET_INSTANCE` и поставить точку остановки

![qownnotes-media-dYgBbO](../media/10285.png)

![qownnotes-media-UBUQHm](../media/27413.png)

- запустить `XK02`

![qownnotes-media-DJWlJE](../media/2177.png)

- что бы увидеть имя первой BADI нужно кликнуть на переменную exit_name и во вкладке variables будет имя BADI: `VENDOR_ADD_DATA`

![qownnotes-media-UywbWQ](../media/20931.png)

- что бы увидеть другие BADIs нужно продолжить дебаг и другие BADIs будут по очереди показаны во вкладке variables

![qownnotes-media-asQrzg](../media/21922.png)


### Using SE84

- ищем имя пакета для нужной нам транзакции
- идем в SE84
- раскрываем папку с расширениями (enhancement)
- раскрываем папку business add ins
- дважды кликаем на definition, задаем имя пакета и жмем выполнить
- лист BADIs будет показан


## BADI Definition

- SE18 -> задать имя BADI
- проверить все методы
- подобрать подходящий BADI и метод на основе описания и подписи
- создать реализацию


## BADI implementation

- SE19
- задать стандартное BADI name - пример: `VENDOR_ADD_DATA`

![qownnotes-media-MNUqUQ](../media/7809.png)

- задать имя реализации, например `ZVENDER_ADD_DATA`

![qownnotes-media-fzEpqn](../media/11293.png)

- нажать создать, задать описание

![qownnotes-media-BpSYbL](../media/22932.png)

- дважды кликнуть на метода CHECK_ALL_DATA

![qownnotes-media-LQfleT](../media/24958.png)

- установить статическую или динамическую точку остановки внутри метода и активировать метод класса

![qownnotes-media-COeOHq](../media/8001.png)

- выйти назад
- активировать реализацию
- протестировать, что выбрана корректная BADI
- - запустить транзакцию XK02 и воспроизвести бизнес требования, BADI будет вызвано через точку останова

![qownnotes-media-xcxlbO](../media/4638.png)

![qownnotes-media-SBeuho](../media/28422.png)

- написать свой код обработки для бизнес требований в найденном методе, сохранить и активировать метод, активировать реализацию

![qownnotes-media-NAAZFh](../media/18945.png)

![qownnotes-media-xrXDAU](../media/28006.png)

- протестировать реализацию

