ZMD_MATANDINFOCR
================

[Home](../Index.md)

Development: DEV_MD01_427 Инструмент для загрузки новинок из Excel - создает материал в MM41, добавляет в ассортимент и расширяет на все магазины и РЦ.

## REPORT

```
*---------------------------------------------------------------------
* Development: DEV_MD01_427 Инструмент для загрузки новинок
*---------------------------------------------------------------------
* Developer: Svetlana Efimova (NVRDS)
* Consultant: Ivan Ivanov (NVRDS)
* Date: 09.07.2019 11:48:20
*---------------------------------------------------------------------
REPORT zmd_matandinfocr MESSAGE-ID zmd01_427.
INCLUDE zmd_matandinfocr_top IF FOUND.
INCLUDE zmd_matandinfocr_sscr IF FOUND.
INCLUDE zmd_matandinfocr_event IF FOUND.
INCLUDE zmd_matandinfocr_class IF FOUND.
INCLUDE zmd_matandinfocr_forms IF FOUND.
INCLUDE zmd_matandinfocr_pai IF FOUND.
INCLUDE zmd_matandinfocr_pbo IF FOUND.

START-OF-SELECTION.

  lcl_app=>main( ).
```

## INCLUDE zmd_matandinfocr_top

```
*&---------------------------------------------------------------------*
*& Include          ZMD_MATANDINFOCR_TOP
*&---------------------------------------------------------------------*
DATA go_cr TYPE REF TO zcl_md_matinfocr ##NEEDED.
CLASS lcl_app DEFINITION FINAL.
  PUBLIC SECTION.

    CLASS-METHODS:
      class_constructor,
      initialization,
      at_selection_screen,
      at_selection_screen_output,
      file_open_dialog,
      get_lifnr_text,
      get_ekorg_text,
      main.

  PROTECTED SECTION ##SECTION_OK.

  PRIVATE SECTION.
    CLASS-METHODS:
      _check_params
        RETURNING VALUE(rv_error) TYPE flag,
      _check_selection_screen.

ENDCLASS.
```

## INCLUDE zmd_matandinfocr_sscr

```
*&---------------------------------------------------------------------*
*& Include          ZMD_MATANDINFOCR_SSCR
*&---------------------------------------------------------------------*
TABLES:
  sscrfields.

DATA gv_lifnr_text TYPE string ##NEEDED.
DATA gv_ekorg_text TYPE t024e-ekotx ##NEEDED.

SELECTION-SCREEN BEGIN OF BLOCK block_0 WITH FRAME TITLE TEXT-000.
PARAMETERS p_mode01 RADIOBUTTON GROUP rgb1 USER-COMMAND mode.
PARAMETERS p_mode02 RADIOBUTTON GROUP rgb1 DEFAULT 'X'.
PARAMETERS p_mode03 RADIOBUTTON GROUP rgb1.
PARAMETERS p_mode04 RADIOBUTTON GROUP rgb1.
PARAMETERS p_mode05 RADIOBUTTON GROUP rgb1.
SELECTION-SCREEN END OF BLOCK block_0.

SELECTION-SCREEN BEGIN OF BLOCK block_1 WITH FRAME TITLE TEXT-001.
*PARAMETERS p_ekorg TYPE komg-ekorg MODIF ID eko.
*PARAMETERS p_lifnr TYPE komg-lifnr MODIF ID lif.

SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(31) TEXT-c01 FOR FIELD p_ekorg MODIF ID eko.
PARAMETERS p_ekorg TYPE komg-ekorg MODIF ID eko.
SELECTION-SCREEN COMMENT 38(40) comm1 MODIF ID eko.
SELECTION-SCREEN END OF LINE.

SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(31) TEXT-c02 FOR FIELD p_lifnr MODIF ID lif.
PARAMETERS p_lifnr TYPE komg-lifnr MODIF ID lif.
SELECTION-SCREEN COMMENT 45(35) comm2 MODIF ID lif.
SELECTION-SCREEN END OF LINE.

PARAMETERS p_file TYPE string MODIF ID obl.
SELECTION-SCREEN END OF BLOCK block_1.
```

## INCLUDE zmd_matandinfocr_event

```
*&---------------------------------------------------------------------*
*& Include          ZMD_MATANDINFOCR_EVENT
*&---------------------------------------------------------------------*
LOAD-OF-PROGRAM.
  go_cr = NEW zcl_md_matinfocr( ).

INITIALIZATION.
  lcl_app=>initialization( ).

AT SELECTION-SCREEN.
  lcl_app=>at_selection_screen( ).

AT SELECTION-SCREEN OUTPUT.
  lcl_app=>at_selection_screen_output( ).

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
  lcl_app=>file_open_dialog( ).

AT SELECTION-SCREEN ON p_lifnr.
  lcl_app=>get_lifnr_text( ).

AT SELECTION-SCREEN ON p_ekorg.
  lcl_app=>get_ekorg_text( ).
```

## INCLUDE zmd_matandinfocr_class

```
*&---------------------------------------------------------------------*
*& Include          ZMD_MATANDINFOCR_CLASS
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*& Class (Implementation) lcl_app
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
CLASS lcl_app IMPLEMENTATION.
  METHOD class_constructor ##NEEDED.
  ENDMETHOD.

  METHOD initialization ##NEEDED.
  ENDMETHOD.

  METHOD at_selection_screen ##NEEDED.

*    CASE sscrfields-ucomm.
*      WHEN 'MODE'.
*      WHEN OTHERS.
*    ENDCASE.

  ENDMETHOD.

  METHOD at_selection_screen_output.

    comm1 = gv_ekorg_text.
    comm2 = gv_lifnr_text.

    LOOP AT SCREEN.
      CASE screen-group1.
        WHEN 'OBL'.
          screen-required = 2.

        WHEN 'LIF'.
          IF go_cr->mv_migr = abap_true.
            screen-active = 0.
          ELSE.
            IF p_mode01 = abap_true
              OR p_mode02 = abap_true.
              screen-active = 1.
              IF screen-name CP 'P_*'.
                screen-required = 2.
              ENDIF.
            ELSE.
              screen-active = 0.
            ENDIF.
          ENDIF.

        WHEN 'EKO'.
          IF go_cr->mv_migr = abap_true.
            screen-active = 0.
          ELSE.
            IF p_mode01 = abap_true
              OR p_mode02 = abap_true.
              screen-active = 1.
              IF screen-name CP 'P_*'.
                screen-required = 2.
              ENDIF.
            ELSE.
              screen-active = 0.
            ENDIF.
          ENDIF.

        WHEN OTHERS.
      ENDCASE.
      MODIFY SCREEN.

    ENDLOOP.

  ENDMETHOD.

  METHOD file_open_dialog.
    DATA:
      lt_files  TYPE filetable,
      lv_rcode  TYPE i,
      lv_action TYPE i.


    REFRESH lt_files.

    CALL METHOD cl_gui_frontend_services=>file_open_dialog
      EXPORTING
*       default_extension       = '.xlsx'
        file_filter             = '(*.xlsx)|*.xlsx|(*.xls)|*.xls'
**       file_filter             = 'CSV files (*.csv)|*.csv|All files(*.*)|*.*'
*       file_filter             = 'CSV files (*.csv)|*.csv'
*       initial_directory       = 'C:\'
      CHANGING
        file_table              = lt_files
        rc                      = lv_rcode
        user_action             = lv_action
      EXCEPTIONS
        file_open_dialog_failed = 1
        cntl_error              = 2
        error_no_gui            = 3
        OTHERS                  = 4.

    IF sy-subrc = 0.
      p_file = VALUE #( lt_files[ 1 ] OPTIONAL ).
    ENDIF.

  ENDMETHOD.

  METHOD get_ekorg_text.

    CLEAR gv_ekorg_text.
    IF p_ekorg IS NOT INITIAL.
      SELECT SINGLE ekotx
        INTO gv_ekorg_text
        FROM t024e
        WHERE ekorg = p_ekorg.
      IF sy-subrc <> 0.
        MESSAGE e045(f2) WITH p_ekorg.
      ENDIF.
    ENDIF.

  ENDMETHOD.

  METHOD get_lifnr_text.
    DATA ls_lfa1 TYPE lfa1.

    CLEAR gv_lifnr_text.
    IF p_lifnr IS NOT INITIAL.
*      SELECT SINGLE name1
*        INTO gv_lifnr_text
*        FROM lfa1
*        WHERE lifnr = p_lifnr.

      CALL FUNCTION 'WY_LFA1_SINGLE_READ'
        EXPORTING
          pi_lifnr         = p_lifnr
        IMPORTING
          po_lfa1          = ls_lfa1
        EXCEPTIONS
          no_records_found = 1
          internal_error   = 2
          lifnr_blocked    = 3
          OTHERS           = 4.
      IF sy-subrc = 0.
        CONCATENATE ls_lfa1-name1 ls_lfa1-name2 ls_lfa1-name3 ls_lfa1-name4
          INTO gv_lifnr_text.
      ELSE.
*        "Кредитор & не создан.
*        MESSAGE e163(f2) WITH p_lifnr.
        "Указанный SAP код поставщика & не заведен в системе
        MESSAGE e086.
      ENDIF.
    ENDIF.

  ENDMETHOD.

  METHOD main.

    _check_selection_screen( ).

    CASE abap_true.
      WHEN p_mode04.
        "Загрузка сертификатов

        DATA(lo_srt) = NEW zcl_srt( ).

        IF lo_srt->get_data( iv_filename  = p_file ) = abap_true.
          lo_srt->process_data( ).
          lo_srt->show_log( ).
          lo_srt->show_result( ).
        ELSE.
          lo_srt->show_log( ).
        ENDIF.

      WHEN OTHERS.
        "Загрузка новинок

        CASE abap_true.
          WHEN p_mode01.
            DATA(lv_mode) = '01'.
            DATA(ls_params) = VALUE zcl_md_matinfocr=>ts_params(
              ekorg = p_ekorg
              lifnr = p_lifnr
            ).
          WHEN p_mode02.
            lv_mode = '02'.
            ls_params = VALUE #(
              ekorg = p_ekorg
              lifnr = p_lifnr
            ).
          WHEN p_mode03.
            lv_mode = '03'.
            CLEAR ls_params.

          WHEN p_mode05. "Ins. NVRDS.ES 17.10.2019 16:52:40
            lv_mode = '05'.
            CLEAR ls_params.

          WHEN OTHERS.
            RETURN.
        ENDCASE.

        DATA(lo_cr) = zcl_md_matinfocr=>get_instance( iv_mode = lv_mode ).
        IF lo_cr IS BOUND.
          lo_cr->set_data( is_params = ls_params ).
          IF lo_cr->get_data( iv_filename = p_file ) = abap_true.
            lo_cr->process_data( ).
            lo_cr->show_result( ).
          ELSE.
            lo_cr->show_errors( ).
          ENDIF.
        ENDIF.

    ENDCASE.

  ENDMETHOD.

  METHOD _check_params.
*        RETURNING VALUE(rv_error) TYPE flag,
    DATA li_log TYPE REF TO zif_log.

    li_log ?= zcl_log_syst=>new( ).

    IF go_cr->mr_meins[] IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_meins
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF go_cr->mr_meins_fab[] IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_meins_fab
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF go_cr->mr_mtart_fab[] IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_mtart_fab
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF go_cr->mr_mtart_segm[] IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_mtart_segm
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF go_cr->mr_mtart_sell[] IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_mtart_sell
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF go_cr->mr_mtart_pr_ctrl_s[] IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_mtart_pr_ctrl_s
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF go_cr->mr_werks_batch[] IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_werks_batch
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF go_cr->mv_charprof IS INITIAL.
      "Не создана настройка в STVARV &
      MESSAGE e002 WITH go_cr->mc_name_charprof
        INTO zcl_msg_helper=>last_message.
      li_log->add_msg( sy ).
    ENDIF.

    IF li_log->has_error( ).
      rv_error = abap_true.

      zcl_log_helper=>show_log(
        EXPORTING
          ii_log            = li_log
          iv_message_if_one = 'X'
          iv_title          = 'Предпосылки для работы программы'(t01)
*          iv_use_grid       = 'X'
          iv_popup          = 'X'
      ).
    ENDIF.
  ENDMETHOD.

  METHOD _check_selection_screen.

    IF p_mode04 = abap_false
      AND _check_params( ) = abap_true.
      LEAVE LIST-PROCESSING.
    ENDIF.

    IF p_file IS INITIAL.
      "Необходимо заполнить все обязательные поля
      MESSAGE s001 DISPLAY LIKE 'E'.
      LEAVE LIST-PROCESSING.
    ENDIF.

    IF go_cr->mv_migr = abap_false.
      CASE abap_true.
        WHEN p_mode01
          OR p_mode02.

          IF p_ekorg IS INITIAL
            OR p_lifnr IS INITIAL.
            "Необходимо заполнить все обязательные поля
            MESSAGE s001 DISPLAY LIKE 'E'.
            LEAVE LIST-PROCESSING.
          ENDIF.

        WHEN p_mode05.
          "Вариант загрузки Инфо-запись доступен только для режима миграции
          MESSAGE s016 DISPLAY LIKE 'E'.
          LEAVE LIST-PROCESSING.

        WHEN OTHERS.
      ENDCASE.
    ENDIF.

  ENDMETHOD.

ENDCLASS.
```

## INCLUDE zmd_matandinfocr_forms

```
*&---------------------------------------------------------------------*
*& Include          ZMD_MATANDINFOCR_FORMS
*&---------------------------------------------------------------------*
FORM merge_is_bapi_data
  USING headdata TYPE bapie1mathead
        clientext TYPE bapie1maraextrt_tab
        clientextx TYPE bapie1maraextrtx_tab
  CHANGING mara_ueb TYPE mara_ueb_tt
           mfieldres TYPE mfieldres_tt
           marm_ueb TYPE marm_ueb_tt ##NEEDED
           makt_ueb TYPE makt_ueb_tt ##NEEDED.

  DATA ls_zmara_c TYPE zcl_md_matinfocr=>ts_mara_ext.
  DATA ls_zmara_x TYPE zcl_md_matinfocr=>ts_mara_extx.
  DATA zmara_descr TYPE REF TO cl_abap_structdescr.

  DATA ls_zmakt_c TYPE zcl_md_matinfocr=>ts_makt_ext.
  DATA ls_zmakt_x TYPE zcl_md_matinfocr=>ts_makt_extx.
  DATA zmakt_descr TYPE REF TO cl_abap_structdescr.

*  BREAK nvevgego.

  "Z-поля MARA
  DO 1 TIMES.
    READ TABLE clientext ASSIGNING FIELD-SYMBOL(<s_clientext>) WITH KEY
      function = zcl_md_matinfocr=>mc_function_ext.
*      material = headdata-material.
    CHECK sy-subrc = 0.

    READ TABLE clientextx ASSIGNING FIELD-SYMBOL(<s_clientextx>) WITH KEY
      function = zcl_md_matinfocr=>mc_function_ext
      material = <s_clientext>-material.
    CHECK sy-subrc = 0.

    ls_zmara_c = <s_clientext>-field1.
    ls_zmara_x = <s_clientextx>-field1.

    IF ls_zmara_x IS NOT INITIAL.
      READ TABLE mara_ueb ASSIGNING FIELD-SYMBOL(<mara_ueb>) WITH KEY
        matnr = <s_clientext>-material.
      IF sy-subrc = 0.
        TRY.
            zmara_descr ?= cl_abap_typedescr=>describe_by_data( ls_zmara_c ).
            LOOP AT zmara_descr->components ASSIGNING FIELD-SYMBOL(<zmara_comp>).
              ASSIGN COMPONENT <zmara_comp>-name OF STRUCTURE ls_zmara_x TO FIELD-SYMBOL(<val_extx>).
              CHECK sy-subrc = 0 AND <val_extx> IS NOT INITIAL.
              ASSIGN COMPONENT <zmara_comp>-name OF STRUCTURE ls_zmara_c TO FIELD-SYMBOL(<val_ext>).
              CHECK sy-subrc = 0.
              ASSIGN COMPONENT <zmara_comp>-name OF STRUCTURE <mara_ueb> TO FIELD-SYMBOL(<val_mara>).
              CHECK sy-subrc = 0.
              IF <val_ext> IS INITIAL.
                APPEND INITIAL LINE TO mfieldres ASSIGNING FIELD-SYMBOL(<mfieldres>).
                MOVE-CORRESPONDING <mara_ueb> TO <mfieldres> ##ENH_OK.
                CONCATENATE 'MARA-' <zmara_comp>-name
                       INTO <mfieldres>-fname.
              ELSE.
                <val_mara> = <val_ext>.
              ENDIF.
            ENDLOOP.
          CATCH cx_sy_move_cast_error ##NO_HANDLER.
        ENDTRY.
      ENDIF.
    ENDIF.
  ENDDO.

  "Z-поля MAKT
  LOOP AT clientext ASSIGNING <s_clientext>
    WHERE function = zcl_md_matinfocr=>mc_function_ext2.
*      AND material = headdata-material.

    READ TABLE clientextx ASSIGNING <s_clientextx> WITH KEY
      function = <s_clientext>-function
      material = <s_clientext>-material
      field1 = <s_clientext>-field1. "Язык
    CHECK sy-subrc = 0.

    ls_zmakt_c = <s_clientext>-field2.
    ls_zmakt_x = <s_clientextx>-field2.

    IF ls_zmakt_x IS NOT INITIAL.
      READ TABLE makt_ueb ASSIGNING FIELD-SYMBOL(<makt_ueb>) WITH KEY
        matnr = <s_clientext>-material
        spras = <s_clientext>-field1 ##WARN_OK.
      IF sy-subrc = 0.
        TRY.
            zmakt_descr ?= cl_abap_typedescr=>describe_by_data( ls_zmakt_c ).
            LOOP AT zmakt_descr->components ASSIGNING FIELD-SYMBOL(<zmakt_comp>).
              ASSIGN COMPONENT <zmakt_comp>-name OF STRUCTURE ls_zmakt_x TO <val_extx>.
              CHECK sy-subrc = 0 AND <val_extx> IS NOT INITIAL.
              ASSIGN COMPONENT <zmakt_comp>-name OF STRUCTURE ls_zmakt_c TO <val_ext>.
              CHECK sy-subrc = 0.
              ASSIGN COMPONENT <zmakt_comp>-name OF STRUCTURE <makt_ueb> TO FIELD-SYMBOL(<val_makt>).
              CHECK sy-subrc = 0.

              <val_makt> = <val_ext>.
            ENDLOOP.
          CATCH cx_sy_move_cast_error ##NO_HANDLER.
        ENDTRY.
      ENDIF.
    ENDIF.
  ENDLOOP.

ENDFORM.

FORM fulfill_e1bpe1maraextrt
  TABLES ct_tclientext STRUCTURE bapie1maraextrt
  USING is_mara TYPE mara.
  DATA ls_tclientext TYPE bapie1maraextrt.
  DATA: BEGIN OF ls_name,
          field2 TYPE bapie1maraextrt-field2,
          field3 TYPE bapie1maraextrt-field3,
          field4 TYPE bapie1maraextrt-field4,
        END OF ls_name.

  CHECK is_mara IS NOT INITIAL.

  SELECT SINGLE zzmaktx99
    INTO @DATA(lv_zmaktx99)
    FROM makt
    WHERE matnr = @is_mara-matnr
      AND spras = @sy-langu.
  IF sy-subrc = 0
    AND lv_zmaktx99 IS NOT INITIAL.
    "Функция 001
    "Наименование товара (99)
    ls_tclientext = VALUE #(
      function = '001'
      material = is_mara-matnr
      material_long = is_mara-matnr
      field1 = 'NAME99'
      field2 = lv_zmaktx99
    ).
    APPEND ls_tclientext TO ct_tclientext.
  ENDIF.

  IF is_mara-zzmatc IS NOT INITIAL.
    "Функция 002 - Tоварная категория
    ls_name =  zcl_sign=>get_value_name(
                iv_zparam = zcl_sign=>mc_zparam-matc
                iv_zcod   = is_mara-zzmatc
               ).

    ls_tclientext = VALUE #(
      function = '002'
      material = is_mara-matnr
      material_long = is_mara-matnr
      field1 = is_mara-zzmatc
      field2 = ls_name-field2
      field3 = ls_name-field3
      field4 = ls_name-field4
    ).
    APPEND ls_tclientext TO ct_tclientext.
  ENDIF.

  IF is_mara-zzprdct IS NOT INITIAL.
    "Функция 003 - Продукт
    ls_name = zcl_sign=>get_value_name(
                iv_zparam = zcl_sign=>mc_zparam-prdct
                iv_zcod   = is_mara-zzprdct
              ) .

    ls_tclientext = VALUE #(
      function = '003'
      material = is_mara-matnr
      material_long = is_mara-matnr
      field1 = is_mara-zzprdct
      field2 = ls_name-field2
      field3 = ls_name-field3
      field4 = ls_name-field4
    ).
    APPEND ls_tclientext TO ct_tclientext.
  ENDIF.

  IF is_mara-zzoblpri IS NOT INITIAL.
    "Функция 004 - Область применения
    ls_name = zcl_sign=>get_value_name(
                iv_zparam = zcl_sign=>mc_zparam-oblpri
                iv_zcod   = is_mara-zzoblpri
              ) .

    ls_tclientext = VALUE #(
      function = '004'
      material = is_mara-matnr
      material_long = is_mara-matnr
      field1 = is_mara-zzoblpri
      field2 = ls_name-field2
      field3 = ls_name-field3
      field4 = ls_name-field4
    ).
    APPEND ls_tclientext TO ct_tclientext.
  ENDIF.

  IF is_mara-zzgrt IS NOT INITIAL.
    "Функция 005 - Область применения
    ls_name = zcl_sign=>get_value_name(
                iv_zparam = zcl_sign=>mc_zparam-grt
                iv_zcod   = is_mara-zzgrt
              ).

    ls_tclientext = VALUE #(
      function = '005'
      material = is_mara-matnr
      material_long = is_mara-matnr
      field1 = is_mara-zzgrt
      field2 = ls_name-field2
      field3 = ls_name-field3
      field4 = ls_name-field4
    ).
    APPEND ls_tclientext TO ct_tclientext.
  ENDIF.

  SORT ct_tclientext BY function.

ENDFORM.
```

## INCLUDE zmd_matandinfocr_pai

Не существует.

## INCLUDE zmd_matandinfocr_pbo

Не существует.