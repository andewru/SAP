LSMW
=====

[Home](../Index.md)


## Пример записи в таблицу

Пример ABAP кода из LSMW (KNVP_FILL и KNVP_FILL_2) для обновления и записи данных в таблицу KNVP - Основные записи клиентов: роли партнеров.

В LSMW блок кода выполняется для каждой строки обрабатываемой LSMW из загружаемого файла.


Блок кода из KNVP_FILL LSMW

```
data: ls_KNVP TYPE KNVP.
data: lt_KNVP TYPE TABLE OF KNVP.

*data: fmatnr type matnr.
data: fKUNNR like KNVP-KUNNR.
data: fVKORG like KNVP-VKORG.
data: fVTWEG like KNVP-VTWEG.
data: fSPART like KNVP-SPART.
data: fPARVW like KNVP-PARVW.
data: fPARZA like KNVP-PARZA.
data: fKUNN2 like KNVP-KUNN2.
data: fPERNR like KNVP-PERNR.
data: fPARNR like KNVP-PARNR.
data: fKNREF like KNVP-KNREF.
data: fDEFPA like KNVP-DEFPA.

*приведение значения из Excel вида 1234 к виду 000001234 - т.е. добавление необходимых ведущих нулей.
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          input  = STRUC1-KUNNR
       IMPORTING
          output = fKUNNR.

CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          input  = STRUC1-KUNN2
       IMPORTING
          output = fKUNN2.

*fKUNNR = STRUC1-KUNNR.
fVKORG = STRUC1-VKORG.
fVTWEG = STRUC1-VTWEG.
fSPART = STRUC1-SPART.
fPARVW = STRUC1-PARVW.
fPARZA = STRUC1-PARZA.
*fKUNN2 = STRUC1-KUNN2.
fPERNR = STRUC1-PERNR.
fPARNR = STRUC1-PARNR.
fKNREF = STRUC1-KNREF.
fDEFPA = STRUC1-DEFPA.

*BREAK nvevgpar.
*insert INITIAL LINE into table ls_KNVP.

move fKUNNR to ls_KNVP-KUNNR.
move fVKORG to ls_KNVP-VKORG.
move fVTWEG to ls_KNVP-VTWEG.
move fSPART to ls_KNVP-SPART.
move fPARVW to ls_KNVP-PARVW.
move fPARZA to ls_KNVP-PARZA.
move fKUNN2 to ls_KNVP-KUNN2.
move fPERNR to ls_KNVP-PERNR.
move fPARNR to ls_KNVP-PARNR.
move fKNREF to ls_KNVP-KNREF.
move fDEFPA to ls_KNVP-DEFPA.

*вставка новой строки из струтуры
*insert ls_KNVP into table KNVP.

*изменение сщестующей строки
modify KNVP FROM ls_KNVP.
```

Блок кода из KNVP_FILL_2 LSMW

```
data: ls_KNVP TYPE KNVP.
data: fKUNNR like KNVP-KUNNR.
data: fVKORG like KNVP-VKORG.
data: fVTWEG like KNVP-VTWEG.
data: fSPART like KNVP-SPART.
data: fPARVW like KNVP-PARVW.
data: fPARZA like KNVP-PARZA.
data: fKUNN2 like KNVP-KUNN2.
data: fPERNR like KNVP-PERNR.
data: fPARNR like KNVP-PARNR.
data: fKNREF like KNVP-KNREF.
data: fDEFPA like KNVP-DEFPA.

CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          input  = STRUC1-KUNNR
       IMPORTING
          output = fKUNNR.

CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          input  = STRUC1-KUNN2
       IMPORTING
          output = fKUNN2.


*fKUNNR = STRUC1-KUNNR.
fVKORG = STRUC1-VKORG.
fVTWEG = STRUC1-VTWEG.
fSPART = STRUC1-SPART.
fPARVW = STRUC1-PARVW.
fPARZA = STRUC1-PARZA.
*fKUNN2 = STRUC1-KUNN2.
fPERNR = STRUC1-PERNR.
fPARNR = STRUC1-PARNR.
fKNREF = STRUC1-KNREF.
fDEFPA = STRUC1-DEFPA.

*точка останова пользовательская
BREAK nvevgpar.

*точка останова для всех
*BREAK POINT.


*Выбор одной строки
*SELECT SINGLE * FROM mbew into ls_mbew
*WHERE matnr = fmatnr AND werks = STRUC1-werks.
*WHERE matnr = STRUC1-matnr AND BWKEY = STRUC1-werks.

SELECT SINGLE * FROM KNVP into ls_KNVP
*WHERE matnr = fmatnr AND werks = STRUC1-werks.
WHERE KUNNR = fKUNNR
 AND VKORG = STRUC1-VKORG
 AND VTWEG = STRUC1-VTWEG
 AND SPART = STRUC1-SPART
 AND PARVW = STRUC1-PARVW
 AND PARZA = STRUC1-PARZA.


move fKUNN2 to ls_KNVP-KUNN2.
move fPERNR to ls_KNVP-PERNR.
move fPARNR to ls_KNVP-PARNR.
move fKNREF to ls_KNVP-KNREF.
move fDEFPA to ls_KNVP-DEFPA.

modify KNVP FROM ls_KNVP.

```
