FM Modules
=========

[Home](../Index.md)

## CONVERSION_EXIT_ALPHA_INPUT

Выполняет приведение значения вида 1234 к виду 000001234 - т.е. добавление необходимых ведущих нулей.

### Call syntax

```
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
  EXPORTING
    INPUT         =
* IMPORTING
*   OUTPUT        =
          .
```


### Example

```
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          input  = <1234|переменная откуда брать вход>
       IMPORTING
          output = <000001234|переменная куда записать вывод>.
          
*Пример:
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          input  = STRUC1-KUNN2
       IMPORTING
          output = fKUNN2.
```

## GUI_DOWNLOAD

Выгрузка таблицы во внешний файл на ПК пользователя через GUI

![qownnotes-media-jiHTWI](../media/19553.png)

### Call syntax

```
CALL FUNCTION 'GUI_DOWNLOAD'
  EXPORTING
*   BIN_FILESIZE                    =
    FILENAME                        =
*   FILETYPE                        = 'ASC'
*   APPEND                          = ' '
*   WRITE_FIELD_SEPARATOR           = ' ' " # - это tab
*   HEADER                          = '00'
*   TRUNC_TRAILING_BLANKS           = ' '
*   WRITE_LF                        = 'X'
*   COL_SELECT                      = ' '
*   COL_SELECT_MASK                 = ' '
*   DAT_MODE                        = ' '
*   CONFIRM_OVERWRITE               = ' '
*   NO_AUTH_CHECK                   = ' '
*   CODEPAGE                        = ' '
*   IGNORE_CERR                     = ABAP_TRUE
*   REPLACEMENT                     = '#'
*   WRITE_BOM                       = ' '
*   TRUNC_TRAILING_BLANKS_EOL       = 'X'
*   WK1_N_FORMAT                    = ' '
*   WK1_N_SIZE                      = ' '
*   WK1_T_FORMAT                    = ' '
*   WK1_T_SIZE                      = ' '
*   WRITE_LF_AFTER_LAST_LINE        = ABAP_TRUE
*   SHOW_TRANSFER_STATUS            = ABAP_TRUE
*   VIRUS_SCAN_PROFILE              = '/SCET/GUI_DOWNLOAD'
* IMPORTING
*   FILELENGTH                      =
  TABLES
    DATA_TAB                        =
*   FIELDNAMES                      =
* EXCEPTIONS
*   FILE_WRITE_ERROR                = 1
*   NO_BATCH                        = 2
*   GUI_REFUSE_FILETRANSFER         = 3
*   INVALID_TYPE                    = 4
*   NO_AUTHORITY                    = 5
*   UNKNOWN_ERROR                   = 6
*   HEADER_NOT_ALLOWED              = 7
*   SEPARATOR_NOT_ALLOWED           = 8
*   FILESIZE_NOT_ALLOWED            = 9
*   HEADER_TOO_LONG                 = 10
*   DP_ERROR_CREATE                 = 11
*   DP_ERROR_SEND                   = 12
*   DP_ERROR_WRITE                  = 13
*   UNKNOWN_DP_ERROR                = 14
*   ACCESS_DENIED                   = 15
*   DP_OUT_OF_MEMORY                = 16
*   DISK_FULL                       = 17
*   DP_TIMEOUT                      = 18
*   FILE_NOT_FOUND                  = 19
*   DATAPROVIDER_EXCEPTION          = 20
*   CONTROL_FLUSH_ERROR             = 21
*   OTHERS                          = 22
          .
IF SY-SUBRC <> 0.
* Implement suitable error handling here
ENDIF.
```


### Example

```
*& Example of Select Options of Selection Screens
* скрины эранов выбора к этому коду см. выше
TABLES KNA1.

SELECT-OPTIONS S_KUNNR FOR KNA1-KUNNR.
PARAMETERS Download AS CHECKBOX.

DATA: I_KNA1 TYPE TABLE OF KNA1.
DATA: WA_KNA1 TYPE KNA1.

SELECT *
  INTO TABLE I_KNA1
  FROM KNA1
  WHERE KUNNR IN S_KUNNR.

IF Download = 'X'.
  CALL FUNCTION 'GUI_DOWNLOAD'
    EXPORTING
      FILENAME = 'c:\swsetup\papp\Tmp\KNA1.txt'
      FILETYPE = 'ASC'
*      WRITE_FIELD_SEPARATOR = 'X' -- это не срабатывает, по умочанию пробел
    TABLES
      DATA_TAB = I_KNA1.

  IF SY-SUBRC = 0.
    MESSAGE 'DATA SUCCESSFULLY DOWNLOADED' TYPE 'I'.
  ENDIF.
ENDIF.
```

## KD_GET_FILENAME_ON_F4

Диалог выбора файла и получение имени выбранного файла.

```
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
```