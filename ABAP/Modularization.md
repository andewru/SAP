Modularization
========================

## Include Programs

[Include Program](https://www.youtube.com/watch?v=f_TGTsWnSe8&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=37)

Подключение программ из глобального репозитория системы.

- подключаемая программа не может вызывать сама себя и не может быть вызвана напрямую, она всегда вызывается из другой Executable программы.
- подключаемая программа должна быть законченной
- подключаемая программа должна иметь тип INCLUDE Program


### Syntax

1. Создать программу с типом INCLUDE
2. Создать программу с типом Executable в которой подключить первую программу

```
INCLUDE <PROGRAM NAME> [IF FOUND].
```

### Example

#### INCLUDE программа

```
*&---------------------------------------------------------------------*
*& Include ZTEST_INCLUDE1
*&---------------------------------------------------------------------*
PROGRAM ZTEST_INCLUDE1.

WRITE:
  / 'Program started by: ', SY-UNAME,
  / 'On host: ', SY-HOST,
  / 'Date:', SY-DATUM,
  / 'Time:', SY-UZEIT.
```
#### Executable программа

Создается программа типа Executable, в которой выполняется подключение INCLUDE программ. В вызывающей Executable программе не пишется REPORT, а сразу указываются INCLUDE программ.

```
*&---------------------------------------------------------------------*
*&  Executable программа
*&---------------------------------------------------------------------*
REPORT ZTEST_INCLUDE.

*Подключение INCLUDE программ
INCLUDE ZTEST_INCLUDE1 IF FOUND.
INCLUDE ....
```


## Functions Modules

[Function Module](https://www.youtube.com/watch?v=sFL-m5QOjes&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=38)

Function Module это публичная подпрограмма (процедура) с входными и выходными параметрами.

В отличие от INCLUDE программ FM могут быть вызваны напрямую и выполнены независимо.

### Компоненты FM

- IMPORT - задать переменные куда будут вставлены выходные значения из FM
- EXPORT - значения передаваемые в FM, задать как значения или через переменные
- EXCEPTIONS - определенный тип ошибки для FM
- TABLES - внутренняя таблица как вход или выход FM
- CHANGING - переменная или WA (структура) как вход или выход FM


### Создание FM в SE37

#### TODO добавить про создание

### CALL FUNCTION 'FM_NAME'

```
REPORT ZTEST.

*& Example OF CALL FUNCTION 'SPELL_AMOUNT'
*Структура передачи для сумм прописью
DATA result like spell.

PARAMETERS num_1 TYPE I.

CALL FUNCTION 'SPELL_AMOUNT'
 EXPORTING
*передача параметров в FM
   AMOUNT          = num_1
   CURRENCY        = ' '
   FILLER          = ' '
   LANGUAGE        = SY-LANGU
 IMPORTING
*в result будет возвращён результат FM
   IN_WORDS        = result
* EXCEPTIONS
*   NOT_FOUND       = 1
*   TOO_LARGE       = 2
*   OTHERS          = 3
          .
IF SY-SUBRC <> 0.
* Implement suitable error handling here
  WRITE: 'Value retuned is: ', SY-SUBRC.
ELSE.
  WRITE: 'Amount in woeds is: ', result-word.
ENDIF.
```

## Subroutines

[Subroutines](https://www.youtube.com/watch?v=zAm6hlgBxG0&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=39)

Subroutines это функции, объявляемые в коде и который могут быть вызваны как из текущей программы, где они объявлены, так и из других программ, что позволяет избежать дублирования кода.

В Subroutines можно передать параметры:
- как значение
- как ссылка
- как значение с возвратом обработанного значения обратно

### Виды Subroutines

#### LOCAL Subroutines Call

Это когда Subroutines объявлена и вызывается в одной и той же программе

#### GLOBAL Subroutines Call

Это когда Subroutines объявлена в одной программе, а вызывается в другой

### Subroutines Syntax

#### PASS PARAM BY REF

При передачи переменной параметра как ссылки, изменение внутри функции будут доступны в этой переменной вне функции.

```
*вызов функции
PERFORM <SUBROUTINE_NAME> USING <var_name>.

*DEFINE SUBROUTINE WITH PASS PARAM BY REF
FORM <SUBROUTINE_NAME> USING <var_name>.
 ...
ENDFORM.
```

##### Example PASS PARAM BY REF

```
REPORT ZTEST_SUBROUTINES.

DATA A TYPE I.
A = 10.

WRITE: / 'Before callig subroutine value of A is: ', A.
*CALL SUBROUTINE AS LOCAL
PERFORM PASS_BY_REF USING A.

WRITE: / 'After callig subroutine value of A is: ', A.

*DEFINE SUBROUTINE PASS PARAM BY REF
FORM PASS_BY_REF USING F_A.
  F_A = F_A + 10.
ENDFORM.
```


#### PASS PARAM BY VALUE

При передачи параметра как значения, его изменения внутри функции не отражаются на значении в переменной вне функции.

```
*вызов функции
PERFORM <SUBROUTINE_NAME> USING <var_name>.


*DEFINE SUBROUTINE WITH PASS PARAM BY VALUE
FORM <SUBROUTINE_NAME> USING VALUE(<var_name>).
  ...
ENDFORM.
```

##### Example PASS PARAM BY VALUE

```
REPORT ZTEST_SUBROUTINES.

DATA A TYPE I.
A = 10.

WRITE: / 'Before callig subroutine value of A is: ', A.
*CALL SUBROUTINE AS LOCAL
PERFORM PASS_BY_VALUE USING A.

WRITE: / 'After callig subroutine value of A is: ', A.

*DEFINE SUBROUTINE PASS PARAM BY VALUE
FORM PASS_BY_VALUE USING VALUE(F_A).
  F_A = F_A + 10.
ENDFORM.
```


#### PASS PARAM BY CHANGING VALUE
Параметр передается по значению, но изменения сделанные внутри функции перезаписываются в переменную отправленную в функцию. Внешний эффект аналогичен варианту передачи по ссылке, но суть разная.

```
*вызов функции
*PERFORM <SUBROUTINE_NAME> USING     <var_name>. *так тоже работает
PERFORM  <SUBROUTINE_NAME> CHANGING  <var_name>.

*DEFINE SUBROUTINE WITH PASS PARAM BY CHANGING VALUE
FORM <SUBROUTINE_NAME> CHANGING VALUE(<var_name>).
  ...
ENDFORM.
```
##### Example PASS PARAM BY CHANGING VALUE

```
REPORT ZTEST_SUBROUTINES.

DATA A TYPE I.
A = 10.

WRITE: / 'Before callig subroutine value of A is: ', A.

*CALL SUBROUTINE AS LOCAL
*PERFORM PASS_BY_CHANGING_VALUE USING A. *так тоже работает
PERFORM PASS_BY_CHANGING_VALUE CHANGING A.

WRITE: / 'After callig subroutine value of A is: ', A.

*DEFINE SUBROUTINE PASS PARAM BY CHANGING VALUE
FORM PASS_BY_CHANGING_VALUE CHANGING VALUE(F_A).
  F_A = F_A + 10.
ENDFORM.
```






