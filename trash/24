Reports in ABAP
========================

## Classical reports

Вывод показывается на одном листе при помощи WRITE оператора внутри LOOP, не может содержать под отчеты.

Может использовать различные события, такие как INITIALIZATION, TOP-OF-PAGE и т.д. Каждыое из событий соответствует специфичной пользовательской активности и вызывается только когда пользователь выполнил это действие.


### Events in ABAP Programing

[Events in ABAP Programing](https://www.youtube.com/watch?v=4DW_51SKniE&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=42)

#### LOAD OF PROGRAM

Используется для загрузки программы в память для исполнения, это событие не доступно для использования.

#### INITIALIZATION.

Используется для инициализации значений переменных программы по умолчанию и переменных экрана выбора (selection-screen).

```
INITIALIZATION.
  PERFORM INIT_VARIABLES.
...
FORM INIT_VARIABLES .
  V_TITLE = 'CUSTOMER MASTER REPORT'.
  P_LAND1 = 'RU'.
ENDFORM.
```

#### AT Selection-screen output

Используется для модификации selection-screen динамически на основе пользовательских действий на экране выбора. Это событие возникает для каждого действия пользователя на экране выбора.


##### AT SELECTION-SCREEN ON <field_name>.

Используется для валидации одного поля ввода на selection-screen.

```
AT SELECTION-SCREEN ON P_LAND.
  PERFORM VALIDATE_LAND1.
...
FORM VALIDATE_LAND1 .
  SELECT LAND1 FROM KNA1 INTO V_LAND1 UP TO 1 ROWS WHERE LAND1 = P_LAND1.
    ENDSELECT.
    IF SY-SUBRC <> 0.
      MESSAGE 'INVALID COUNTRY' TYPE 'E'.
    ENDIF.
ENDFORM.
```

##### AT SELECTION-SCREEN ON VALUE-REQUEST FOR <field_name>.

Используется для предоставления поиска значения для входного поля, возникает при нажатии на поисковую кнопку в поле ввода.
```
AT SELECTION-SCREEN ON VALUE-REQUEST FOR P_FNAME.
  PERFORM GET_FILE_NAME.
...
FORM GET_FILE_NAME .
  CALL FUNCTION 'KD_GET_FILENAME_ON_F4'
    EXPORTING
     PROGRAM_NAME        = SYST-REPID
     DYNPRO_NUMBER       = SYST-DYNNR
     FIELD_NAME          = 'P_FNAME'
*     STATIC              = ' '
*     MASK                = ' '
*     FILEOPERATION       = 'R'
*     PATH                =
    CHANGING
      FILE_NAME           = P_FNAME.
*     LOCATION_FLAG       = 'P'
*   EXCEPTIONS
*     MASK_TOO_LONG       = 1
*     OTHERS              = 2
            .
  IF SY-SUBRC <> 0.
* Implement suitable error handling here
    MESSAGE 'ERROR OF SELECTION OF FILENAME' TYPE 'E'.
  ENDIF.
ENDFORM.
```

##### AT SELECTION-SCREEN ON HELP-REQUEST FOR <field_name>.

Используется для предоставления помощи для поля ввода, возникает при нажатии F1 в поле ввода.
```
AT SELECTION-SCREEN ON HELP-REQUEST FOR P_FNAME.
  PERFORM GET_HELP.
...
FORM GET_HELP .
  MESSAGE 'PLEASE CLICK ON F4 FOR FILE NAME' TYPE 'I'.
ENDFORM.
```

#### AT SELECTION-SCREEN.

Используется для валидации сразу нескольких полей ввода экрана выбора.


##### START-OF-SELECTION.

Используют для записи бизнес логики программы т.е. всех  блоков кода select. Так же используют для индикации старта программы. Это событие по умолчанию.

```
START-OF-SELECTION.
  PERFORM GEST_DATA.
END-OF-SELECTION.
...
FORM GEST_DATA.
  SELECT KUNNR LAND1 NAME1 ORT01 FROM KNA1 INTO TABLE I_KNA1 WHERE LAND1 = P_LAND1.
  IF SY-SUBRC <> 0.
    MESSAGE 'NO DATA FOUND' TYPE 'I'.
  ENDIF.
ENDFORM.
```

##### END-OF-SELECTION.

Показывает, что событие start of selection завершилось (закрывает блок START-OF-SELECTION).

#### TOP-OF-PAGE.

Используется для показа постоянного заголовка для всех страниц на экране вывода (output scren). Это событие вызывается после первого WRITE statement.

```
TOP-OF-PAGE.
  PERFORM DISPLAY_HEADING.
...
FORM DISPLAY_HEADING .
  WRITE / SY-ULINE.
  WRITE /45 V_TITLE COLOR 5.
  WRITE / SY-ULINE.
ENDFORM.
```

#### END-OF-PAGE.

Используется для показа постоянного футера для всех страниц многостраничного экрана вывода. В таком футере можно, например, вывести значение общего количества строк:

```
REPORT <REPORT_NAME> LINE COUNT TOTAL LINES(FOOTER LINES).
```

```
END-OF-PAGE.
  PERFORM DISPLAY_FOOTER.
...
FORM DISPLAY_FOOTER .
  WRITE / SY-ULINE.
  WRITE /45 'FOOTER' COLOR 5.
  WRITE / SY-ULINE.
ENDFORM.
```

### EXAMPLE of CLASSICAL REPORT WITH EVENTS


```
*&---------------------------------------------------------------------*
*& Report ZTEST_CLASSICALREPORT1
*&---------------------------------------------------------------------*

* NO STANDARD PAGE HEADING - пользовательский хедер
* LINE-COUNT - количество строк на страницу = 34 из них по 3 строки на хедер и на футер
REPORT ZTEST_CLASSICALREPORT1 NO STANDARD PAGE HEADING LINE-COUNT 34(3).

TYPES: BEGIN OF TY_KNA1,
  KUNNR TYPE KNA1-KUNNR,
  LAND1 TYPE KNA1-LAND1,
  NAME1 TYPE KNA1-NAME1,
  ORT01 TYPE KNA1-ORT01,
END OF TY_KNA1.

DATA:
      I_KNA1        TYPE TABLE OF TY_KNA1,
      WA_KNA1       TYPE TY_KNA1,
      V_TITLE(25)   TYPE C,
      V_LAND1       TYPE KNA1-LAND1,
      V_FNAME       TYPE STRING.

******* SELECTION SCREEN **********
PARAMETERS:
  P_LAND1     TYPE KNA1-LAND1,
  P_FNAME     TYPE RLGRAP-FILENAME,
  P_DLOAD     AS CHECKBOX.

INITIALIZATION.
  PERFORM INIT_VARIABLES.

***AT SELECTION-SCREEN OUTPUT.
AT SELECTION-SCREEN ON P_LAND1.
  PERFORM VALIDATE_LAND1.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR P_FNAME.
  PERFORM GET_FILE_NAME.

AT SELECTION-SCREEN ON HELP-REQUEST FOR P_FNAME.
  PERFORM GET_HELP.

***AT SELECTION-SCREEN.
START-OF-SELECTION.
*code of business logic
  PERFORM GEST_DATA.

END-OF-SELECTION.


IF P_DLOAD = 'X'.
  PERFORM DOWNLOAD_DATA.
ENDIF.

PERFORM DISPLAY_DATA.

TOP-OF-PAGE.
  PERFORM DISPLAY_HEADING.

END-OF-PAGE.
  PERFORM DISPLAY_FOOTER.


*&---------------------------------------------------------------------*
*& Form INIT_VARIABLES
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM INIT_VARIABLES .
  V_TITLE = 'CUSTOMER MASTER REPORT'.
  P_LAND1 = 'RU'.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form VALIDATE_LAND1
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM VALIDATE_LAND1 .
  SELECT LAND1 FROM KNA1 INTO V_LAND1 UP TO 1 ROWS WHERE LAND1 = P_LAND1.
    ENDSELECT.
    IF SY-SUBRC <> 0.
      MESSAGE 'INVALID COUNTRY' TYPE 'E'.
    ENDIF.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form GET_FILE_NAME
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM GET_FILE_NAME .
  CALL FUNCTION 'KD_GET_FILENAME_ON_F4'
    EXPORTING
     PROGRAM_NAME        = SYST-REPID
     DYNPRO_NUMBER       = SYST-DYNNR
     FIELD_NAME          = 'P_FNAME'
*     STATIC              = ' '
*     MASK                = ' '
*     FILEOPERATION       = 'R'
*     PATH                =
    CHANGING
      FILE_NAME           = P_FNAME.
*     LOCATION_FLAG       = 'P'
*   EXCEPTIONS
*     MASK_TOO_LONG       = 1
*     OTHERS              = 2
            .
  IF SY-SUBRC <> 0.
* Implement suitable error handling here
    MESSAGE 'ERROR OF SELECTION OF FILENAME' TYPE 'E'.
  ENDIF.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form GET_HELP
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM GET_HELP .
  MESSAGE 'PLEASE CLICK ON F4 FOR FILE NAME' TYPE 'I'.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form GEST_DATA
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM GEST_DATA .
  SELECT KUNNR LAND1 NAME1 ORT01 FROM KNA1 INTO TABLE I_KNA1 WHERE LAND1 = P_LAND1.
  IF SY-SUBRC <> 0.
    MESSAGE 'NO DATA FOUND' TYPE 'I'.
  ENDIF.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form DOWNLOAD_DATA
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM DOWNLOAD_DATA .
  V_FNAME = P_FNAME.
  CALL FUNCTION 'GUI_DOWNLOAD'
    EXPORTING
*     BIN_FILESIZE                    =
      FILENAME                        = V_FNAME " 'C:\KNA1.TXT'
      FILETYPE                        = 'ASC' "ASC means notepad file, DAT means Excel file
*     WRITE_FIELD_SEPARATOR           = ' '
    TABLES
      DATA_TAB                        = I_KNA1.
  IF SY-SUBRC <> 0.
* Implement suitable error handling here
    MESSAGE 'DATA IS SUCCESSFULLY DOWNLOADED' TYPE 'I'.
  ENDIF.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form DISPLAY_DATA
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM DISPLAY_DATA .
  LOOP AT I_KNA1 INTO WA_KNA1.
    WRITE: /
      WA_KNA1-KUNNR,
      WA_KNA1-LAND1,
      WA_KNA1-NAME1,
      WA_KNA1-ORT01.
  ENDLOOP.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form DISPLAY_HEADING
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM DISPLAY_HEADING .
  WRITE / SY-ULINE.
  WRITE /45 V_TITLE COLOR 5.
  WRITE / SY-ULINE.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form DISPLAY_FOOTER
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM DISPLAY_FOOTER .
  WRITE / SY-ULINE.
  WRITE /45 'FOOTER' COLOR 5.
  WRITE / SY-ULINE.
ENDFORM.
```



## Interactive reports

[Interactive Reports](https://www.youtube.com/watch?v=s0DTNk44PvY&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=43)

Ввод можно просматривать на нескольких листах и в интерактивном режиме, проваливаться в под отчеты, выполнять фильтры и сортировки.

- SY-LSIND - системная переменная не хранит списки.
- SY-LISEL - системная переменная хранит данные выбранной строки.


### Events in IR

#### AT LINE SELECTION

Возникает по двойному клику пользователя на строке основного отчета, что бы узнать информация по выбранной строке.

Используется с операторами:

##### HIDE

Используется для хранения данных во временной скрытой области памяти, куда данные можно положить в цикле. По двойному клику мыши на строке отчета срабатывает событие AT LINE SELECTION и система автоматически идентифицирует номер данной строки и записывает соответствующие данные этой строки в скрытую переменную.

```
...
LOOP AT I_MARA INTO WA_MARA.
  WRITE: /
    WA_MARA-MATNR,
    WA_MARA-MTART,
    WA_MARA-MBRSH,
    WA_MARA-MATKL,
    WA_MARA-MEINS.

*сохранение ключа каждой строки в скрытую область
*для использования потом в AT LINE-SELECTION
 HIDE: WA_MARA-MATNR.
ENDLOOP.
...
```

##### GET CURSOR

Используется для чтения и получения контента выбранной пользователем сроки.


##### Example AT LINE SELECTION HIDE

```
REPORT ZTEST_INTERACTIVEREPORT1.

TYPES: BEGIN OF TY_MARA,
  MATNR TYPE MARA-MATNR,
  MTART TYPE MARA-MTART,
  MBRSH TYPE MARA-MBRSH,
  MATKL TYPE MARA-MATKL,
  MEINS TYPE MARA-MEINS,
END OF TY_MARA.

TYPES: BEGIN OF TY_MAKT,
  MATNR TYPE MAKT-MATNR,
  SPRAS TYPE MAKT-SPRAS,
  MAKTX TYPE MAKT-MAKTX,
  MAKTG TYPE MAKT-MAKTG,
END OF TY_MAKT.

DATA: I_MARA  TYPE TABLE OF TY_MARA,
      WA_MARA TYPE TY_MARA,
      I_MAKT  TYPE TABLE OF TY_MAKT,
      WA_MAKT TYPE TY_MAKT.

PARAMETERS P_MATKL TYPE MARA-MATKL.


SELECT  MATNR
        MTART
        MBRSH
        MATKL
        MEINS
FROM MARA
  INTO TABLE I_MARA
WHERE MATKL = P_MATKL.

LOOP AT I_MARA INTO WA_MARA.
  WRITE: /
    WA_MARA-MATNR,
    WA_MARA-MTART,
    WA_MARA-MBRSH,
    WA_MARA-MATKL,
    WA_MARA-MEINS.

*сохранение ключа каждой строки в скрытую область
*для использования потом в AT LINE-SELECTION
 HIDE: WA_MARA-MATNR.
ENDLOOP.

AT LINE-SELECTION.
*Обработка дойного клика по строке основного отчета
*и вывод подотчета
  SELECT MATNR SPRAS MAKTX MAKTG
  FROM MAKT
  INTO TABLE I_MAKT
  WHERE MATNR = WA_MARA-MATNR.

  LOOP AT I_MAKT INTO WA_MAKT.
    WRITE: /
      WA_MAKT-MATNR,
      WA_MAKT-SPRAS,
      WA_MAKT-MAKTG,
      WA_MAKT-MAKTX.
  ENDLOOP.

```

#### AT USER COMMAND

Возникает по клику на пользовательской GUI кнопке.


#### TOP OF PAGE during line selection

Используется для создания постоянного заголовка страницы для всех страниц отчета

#### AT PF <Function Key> - устарело

Возникает по нажатию кнопки Fno  - например F1, F2 и т.д.





## ABAP List Viewer (ALV) reports

[ABAP List Viewer](https://www.youtube.com/watch?v=gUNtIuI9rSU&list=PLWPirh4EWFpH4i1J7CxvDabSycl5YbAhG&index=44)

Новый вид отчета с пред настроенным форматом, предоставляет сортировки, фильтры, поиск, табличный вид, списочный вид, опции настройки формата и вывода столбцов. Можно использовать различные ALV FM модуля для манипулирования данными в отчете.

### FM for developing ALV Report

#### REUSE_ALV_GRID_DISPLAY

Функциональный модуль для вывода ALV отчета как таблицы

#### REUSE_ALV_LIST_DISPLAY

Функциональный модуль для вывода ALV отчета как листа

#### REUSE_ALV_COMMENTARY_WRITE

Покажет:

##### TOP-OF-PAGE
##### LOGO
##### END-OF-LIST

#### REUSE_ALV_HIERSEQ_LIST_DISPLAY

Показ двух внутренних таблиц которые отформатированы как список иерархической последовательности.


#### REUSE_ALV_VARIAN

Показ диалога выбора вариантов отображения отчета.

#### REUSE_ALV_VARIAN_EXISTENCE

Проверка на существование варианта отображения отчета.

### Example

```
REPORT ZTEST_ALVREPORT1.

*Объявление структуры типа MARA
TABLES MARA.

DATA: I_MARA  TYPE TABLE OF MARA,
      WA_MARA TYPE MARA.

SELECT-OPTIONS SO_MATNR FOR MARA-MATNR.
PARAMETERS P_MTART TYPE MARA-MTART.

START-OF-SELECTION.
  PERFORM GET_DATA.
  PERFORM DISPLAY_DATA.
END-OF-SELECTION.

*&---------------------------------------------------------------------*
*& Form GET_DATA
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM GET_DATA .
  SELECT * FROM MARA
    INTO TABLE I_MARA
    WHERE MATNR IN SO_MATNR AND MTART = P_MTART.
ENDFORM.
*&---------------------------------------------------------------------*
*& Form DISPLAY_DATA
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM DISPLAY_DATA .
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
    EXPORTING
*     I_INTERFACE_CHECK                 = ' '
*     I_BYPASSING_BUFFER                = ' '
*     I_BUFFER_ACTIVE                   = ' '
      I_CALLBACK_PROGRAM                = 'SY-REPID'
*     I_CALLBACK_PF_STATUS_SET          = ' '
*     I_CALLBACK_USER_COMMAND           = ' '
*     I_CALLBACK_TOP_OF_PAGE            = ' '
*     I_CALLBACK_HTML_TOP_OF_PAGE       = ' '
*     I_CALLBACK_HTML_END_OF_LIST       = ' '
      I_STRUCTURE_NAME                  = 'MARA'
*     I_BACKGROUND_ID                   = ' '
*     I_GRID_TITLE                      =
*     I_GRID_SETTINGS                   =
*     IS_LAYOUT                         =
*     IT_FIELDCAT                       =
*     IT_EXCLUDING                      =
*     IT_SPECIAL_GROUPS                 =
*     IT_SORT                           =
*     IT_FILTER                         =
*     IS_SEL_HIDE                       =
*     I_DEFAULT                         = 'X'
*     I_SAVE                            = ' '
*     IS_VARIANT                        =
*     IT_EVENTS                         =
*     IT_EVENT_EXIT                     =
*     IS_PRINT                          =
*     IS_REPREP_ID                      =
*     I_SCREEN_START_COLUMN             = 0
*     I_SCREEN_START_LINE               = 0
*     I_SCREEN_END_COLUMN               = 0
*     I_SCREEN_END_LINE                 = 0
*     I_HTML_HEIGHT_TOP                 = 0
*     I_HTML_HEIGHT_END                 = 0
*     IT_ALV_GRAPHICS                   =
*     IT_HYPERLINK                      =
*     IT_ADD_FIELDCAT                   =
*     IT_EXCEPT_QINFO                   =
*     IR_SALV_FULLSCREEN_ADAPTER        =
*     O_PREVIOUS_SRAL_HANDLER           =
*   IMPORTING
*     E_EXIT_CAUSED_BY_CALLER           =
*     ES_EXIT_CAUSED_BY_USER            =
    TABLES
      T_OUTTAB                          = I_MARA.
*   EXCEPTIONS
*     PROGRAM_ERROR                     = 1
*     OTHERS                            = 2
            .
  IF SY-SUBRC <> 0.
* Implement suitable error handling here
    MESSAGE 'ERROR OF DISPLAY_DATA' TYPE 'E'.
  ENDIF.

ENDFORM.
```





