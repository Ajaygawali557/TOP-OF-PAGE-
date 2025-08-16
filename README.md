# TOP-OF-PAGE-
REPORT z_demo_top_of_page.

TABLES: sflight.

SELECT-OPTIONS: s_carr FOR sflight-carrid,
                s_conn FOR sflight-connid.

INITIALIZATION.
  sy-title = 'Flight List with Page Header'.

START-OF-SELECTION.
  "Fetch some demo data
  SELECT * FROM sflight
           INTO TABLE @DATA(gt_sflight)
           WHERE carrid IN @s_carr
             AND connid IN @s_conn.

  "Output (this triggers TOP-OF-PAGE automatically on first WRITE)
  LOOP AT gt_sflight INTO DATA(gs_sflight).
    WRITE: / gs_sflight-carrid, gs_sflight-connid, gs_sflight-fldate,
             gs_sflight-planetype, gs_sflight-price CURRENCY gs_sflight-currency.
    AT LINE-SELECTION.
      "optional: handle drilldown
    ENDAT.

    "Force a new page sometimes to demonstrate header repeating
    AT END OF carrid.
      NEW-PAGE.
    ENDAT.
  ENDLOOP.

TOP-OF-PAGE.
  PERFORM build_header.

FORM build_header.
  FORMAT COLOR COL_HEADING INTENSIFIED ON.
  ULINE.
  WRITE: / 'Company:' , 'Demo Airlines',
         60 'Report:'  , sy-title.
  WRITE: / 'Date:'    , sy-datum,
         60 'Time:'   , sy-uzeit.
  WRITE: / 'User:'    , sy-uname,
         60 'Page:'   , sy-pagno.
  ULINE.
  SKIP.
  FORMAT RESET.
  "Column headings for the list
  WRITE: / 'CARRID', 10 'CONNID', 20 'FLDATE', 35 'PLTYPE', 50 'PRICE'.
  ULINE.
ENDFORM.
