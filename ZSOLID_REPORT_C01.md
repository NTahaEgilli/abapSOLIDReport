``` abap
*&---------------------------------------------------------------------* 
*&  Include  zsolid_report_c01
*&---------------------------------------------------------------------* 
CLASS lcl_reporter DEFINITION CREATE PRIVATE. 
  PUBLIC SECTION. 
    INTERFACES: if_reporter. 

    ALIASES: at_selection_screen FOR if_reporter~at_selection_screen,
             get_data FOR if_reporter~get_data,
             get_screen FOR if_reporter~get_screen.

    CLASS-METHODS: 
      get_instance 
        RETURNING VALUE(return_object) TYPE REF TO cl_main. 

  PRIVATE SECTION. 
    CLASS-DATA local_class_object TYPE REF TO cl_main. 

    METHODS constructor. 
    METHODS list_data. 
    METHODS edit_fcat CHANGING ct_fieldcat TYPE lvc_t_fcat. 
ENDCLASS. 

CLASS lcl_event_handler DEFINITION. 
  PUBLIC SECTION. 
    INTERFACES: if_event_handler.

    ALIASES: on_user_command FOR if_event_handler~on_user_command,
             on_double_click FOR if_event_handler~on_double_click,
             on_line_click FOR if_event_handler~on_line_click.
ENDCLASS.


CLASS lcl_reporter IMPLEMENTATION. 
  METHOD constructor. 

  ENDMETHOD. 

  METHOD get_instance. 
    IF local_class_object IS INITIAL. 
      local_class_object = NEW #( ). 
    ENDIF. 
    return_object = local_class_object. 
  ENDMETHOD. 

  METHOD at_selection_screen. 

  ENDMETHOD. 

  METHOD get_data. 

  ENDMETHOD. 

  METHOD get_screen. 
    list_data( ). 
  ENDMETHOD. 

  METHOD edit_fcat. 

  ENDMETHOD. 

  METHOD list_data. 
    DATA: lo_columns TYPE REF TO cl_salv_columns, 
          lo_column  TYPE REF TO cl_salv_column_table, 
          lo_display TYPE REF TO cl_salv_display_settings. 

    TRY. 
        cl_salv_table=>factory( 
          IMPORTING 
            r_salv_table = DATA(o_table) 
          CHANGING 
            t_table      = t_2000 ). 

        DATA(lo_event)  = o_table->get_event( ). 
        DATA(lo_events) = NEW lcl_handle_events( ). 

        SET HANDLER lo_events->if_event_handler~on_line_click   FOR lo_event. 
        SET HANDLER lo_events->if_event_handler~on_user_command FOR lo_event. 

        o_table->get_display_settings( )->set_striped_pattern( abap_true ). 
        o_table->get_columns( )->set_optimize( abap_true ). 
        o_table->get_functions( )->set_all( abap_true ). 
        o_table->get_selections( )->set_selection_mode( if_salv_c_selection_mode=>row_column ). 

        lo_display = o_table->get_display_settings( ). 
        lo_display->set_list_header( TEXT-002 ). 

        DATA(lo_layout) = o_table->get_layout( ). 
        lo_layout->set_key( VALUE salv_s_layout_key( report = sy-repid handle = 'MAIN' ) ). 
        lo_layout->set_save_restriction( if_salv_c_layout=>restrict_none ). 
        lo_layout->set_default( if_salv_c_bool_sap=>true ). 
        lo_columns = o_table->get_columns( ). 

        o_table->set_screen_status( 
          EXPORTING 
            report        = sy-repid 
            pfstatus      = 'STATUS' 
            set_functions = o_table->c_functions_all ). 

        DATA(lt_fcat) = cl_salv_controller_metadata=>get_lvc_fieldcatalog( 
          r_columns      = o_table->get_columns( ) 
          r_aggregations = o_table->get_aggregations( ) ). 

        me->edit_fcat( 
          CHANGING 
            ct_fieldcat = lt_fcat ). 

        LOOP AT lt_fcat INTO DATA(ls_fcat). 
          TRY. 
              lo_column ?= o_table->get_columns( )->get_column( ls_fcat-fieldname ). 
              IF ls_fcat-checkbox = 'X'. 
                lo_column->set_cell_type( if_salv_c_cell_type=>checkbox ). 
              ENDIF. 
            CATCH cx_salv_not_found. 
          ENDTRY. 
        ENDLOOP. 

        cl_salv_controller_metadata=>set_lvc_fieldcatalog( 
          t_fieldcatalog = lt_fcat 
          r_columns      = o_table->get_columns( ) 
          r_aggregations = o_table->get_aggregations( ) ).

        o_table->display( ). 

      CATCH cx_salv_msg. 
    ENDTRY. 
  ENDMETHOD. 
ENDCLASS. 

CLASS lcl_event_handler IMPLEMENTATION. 
  METHOD on_user_command. 

  ENDMETHOD. 

  METHOD on_double_click. 

  ENDMETHOD. 

  METHOD on_line_click. 

  ENDMETHOD. 
ENDCLASS. 
```
