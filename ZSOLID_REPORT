*&---------------------------------------------------------------------* 
*& Report zsolid_report
*&---------------------------------------------------------------------* 
REPORT zsolid_report. 

INCLUDE: zsolid_report_top,
         zsolid_report_sel,
         zsolid_report_i01,
         zsolid_report_c01.

INITIALIZATION. 
  DATA(o_reporter) = NEW lcl_reporter( ). 

AT SELECTION-SCREEN. 
  o_reporter->at_selection_screen( ).

START-OF-SELECTION.
  o_reporter->get_data( ).

END-OF-SELECTION.
  o_reporter->get_screen( ).
