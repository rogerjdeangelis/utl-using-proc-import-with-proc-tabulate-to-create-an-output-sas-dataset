# utl-using-proc-import-with-proc-tabulate-to-create-an-output-sas-dataset
Using proc import with proc tabulate to create an output sas tabulate dataset
    %let pgm=utl-using-proc-import-with-proc-tabulate-to-create-an-output-sas-dataset;

    %stop_submission;

    Using proc import with proc tabulate to create an output sas tabulate dataset

    PROBLEM

       IN ADDITION TO THIS STATIC REPORT

       ------------------------------------------------------------------------
       |     |          REPS           |            |            |            |
       |     |-------------------------|            |            |            |
       |     |     0      |     1      |    All     |            |    LGD     |
       |     |------------+------------+------------|            |------------|
       |     |     N      |     N      |     N      |    PctN    |    Mean    |
       |-----+------------+------------+------------+------------+------------|
       |LEAF |            |            |            |            |            |
       |-----|            |            |            |            |            |
       |1    |        2.00|           0|        2.00|       22.22|        0.33|
       |-----+------------+------------+------------+------------+------------|
       |2    |        1.00|        2.00|        3.00|       33.33|        0.25|
       |-----+------------+------------+------------+------------+------------|
       |3    |        2.00|        2.00|        4.00|       44.44|        0.33|
       |-----+------------+------------+------------+------------+------------|
       |All  |        5.00|        4.00|        9.00|      100.00|        0.30|
       ------------------------------------------------------------------------

       CREATE THIS SAS DATASET

       WORK.WANT

       LEAF  REP0 REP1 REPALL   PCTN   AVG_LGD

         1     2    0     2     22.22    0.33
         2     1    2     3     33.33    0.25
         3     2    2     4     44.44    0.33
       SUM     5    4     9    100.00    0.30


    CONTENTS

       1 default static tabulate report
       2 tabulate dataset
       3 sql sas r python

    github
    https://tinyurl.com/bdbdb3a9
    https://github.com/rogerjdeangelis/utl-using-proc-import-with-proc-tabulate-to-create-an-output-sas-dataset

    sas communities
    https://tinyurl.com/yfzhyr6f
    https://communities.sas.com/t5/Statistical-Procedures/Percent-e-Freq/m-p/759989#M37021

    Related repos
    github
    https://tinyurl.com/r3s8f5fy
    https://github.com/rogerjdeangelis/utl-creating-a-proc-tabulate-cross-tabulation-sas-dataset

    SOAPBOX ON
      One of the nice things about proc tabulate is the formatting
      options. It is fairly easy to make pro tabulate look like
      a retangular sas dataset, which can be imported by
      proc import.
    SOAPBOX OFF

    /**************************************************************************************************************************/
    /*  INPUT                  | PROCESS                                      | OUTPUT                                        */
    /*  ====                   | =======                                      | ======                                        */
    /*  LEAF  LGD REPS         | 1 STATIC REPORT                              | ---------------------------------------       */
    /*                         | ===============                              | |    |    REPS    |     |      |      |       */
    /*    1  0.15   0          | Options FORMCHAR='|----|+|---+=|-/\<>*' ;    | |    |------------|     |      |      |       */
    /*    2  0.25   1          | proc tabulate data=have;                     | |    |   0 |    1 | ALL |      |  LGD |       */
    /*    3  0.35   1          | class leaf reps;                             | |    |-----+------+-----|      |------|       */
    /*    3  0.23   0          | var LGD;                                     | |    |   N |    N |  N  | PctN | Mean |       */
    /*    2  0.27   1          | table (leaf all),                            | |----+-----+------+-----+------+------|       */
    /*    3  0.31   1          |   (reps All)*N                               | |LEAF|     |      |     |      |      |       */
    /*    3  0.41   0          |   PCTN LGD*Mean;                             | |----|     |      |     |      |      |       */
    /*    2  0.22   0          | run;                                         | |1   | 2.00|     0| 2.00| 22.22|  0.33|       */
    /*    1  0.51   0          |                                              | |----+-----+------+-----+------+------|       */
    /*                         |                                              | |2   | 1.00|  2.00| 3.00| 33.33|  0.25|       */
    /*  data have;             |                                              | |----+-----+------+-----+------+------|       */
    /*  input leaf             |                                              | |3   | 2.00|  2.00| 4.00| 44.44|  0.33|       */
    /*        lgd              |                                              | |----+-----+------+-----+------+------|       */
    /*        reps;            |                                              | |All | 5.00|  4.00| 9.00|100.00|  0.30|       */
    /*  cards;                 |                                              | ---------------------------------------       */
    /*  1 0.15 0               |                                              |                                               */
    /*  2 0.25 1               |----------------------------------------------------------------------------------------------*/
    /*  3 0.35 1               | 2 TABULATE DATASET                           |                                               */
    /*  3 0.23 0               | ==================                           |                                               */
    /*  2 0.27 1               |                                              | ods listing file: d:/txt/tab.txt              */
    /*  3 0.31 1               | title;                                       | Note datarow is 6 so import 'datarow=6'       */
    /*  3 0.41 0               | footnote;                                    |                                               */
    /*  2 0.22 0               |                                              | Intermediate text file                        */
    /*  1 0.51 0               | %utlfkil(d:/txt/tab.txt);                    |                                               */
    /*  ;;;;                   |                                              | 1  LEAF,REP0,REP1,REPALL,PCTN,AVG_LGD,        */
    /*  run;quit;              | ods listing                                  | 2                                             */
    /*                         |   file="d:/txt/tab.txt";                     | 3                                             */
    /*                         |                                              | 4  leaf, 0 , 1 , n , PctN , LGD ,             */
    /*                         | options                                      | 5                                             */
    /*                         |    missing=0                                 | 6* 1   , 2.00, 0, 2.00, 22.22, 0.33,          */
    /*                         |    formchar=","                              |                                               */
    /*                         |    ls=255;                                   |    2   , 1.00, 2.00, 3.00, 33.33, 0.25,       */
    /*                         |                                              |                                               */
    /*                         | title                                        |    3   , 2.00, 2.00, 4.00, 44.44, 0.33,       */
    /*                         |  ",LEAF,REP0,REP1,REPALL,PCTN,AVG_LGD,";     |                                               */
    /*                         |                                              |    SUM , 5.00, 4.00, 9.00, 100.00, 0.30,      */
    /*                         | proc tabulate data=have;                     |                                               */
    /*                         |                                              | WORK.WANT                                     */
    /*                         | class leaf reps;                             |                                               */
    /*                         |                                              | LEAF  REP0 REP1 REPALL   PCTN   AVG_LGD       */
    /*                         | var lgd;                                     |                                               */
    /*                         |                                              |   1     2    0     2     22.22    0.33        */
    /*                         | table (leaf="" all="SUM"),                   |   2     1    2     3     33.33    0.25        */
    /*                         |       (reps="" all="n")*n=""                 |   3     2    2     4     44.44    0.33        */
    /*                         |        pctn lgd*mean=""                      | SUM     5    4     9    100.00    0.30        */
    /*                         |    /rts=6 box="leaf";                        |                                               */
    /*                         | run;                                         |                                               */
    /*                         | ods listing;                                 |                                               */
    /*                         |                                              |                                               */
    /*                         | proc import                                  |                                               */
    /*                         |  datafile=                                   |                                               */
    /*                         |   "d:/txt/tab.txt"                           |                                               */
    /*                         |                                              |                                               */
    /*                         |  dbms=csv                                    |                                               */
    /*                         |                                              |                                               */
    /*                         |  out=want (                                  |                                               */
    /*                         |   drop=var:                                  |                                               */
    /*                         |   where=(not missing(leaf)))                 |                                               */
    /*                         |                                              |                                               */
    /*                         |  replace;                                    |                                               */
    /*                         |  getnames=YES;                               |                                               */
    /*                         |  datarow=6;                                  |                                               */
    /*                         |                                              |                                               */
    /*                         | run;quit;                                    |                                               */
    /*                         |                                              |                                               */
    /*                         |Options FORMCHAR='|----|+|---+=|-/\<>*' ;     |                                               */
    /*                         |                                              |                                               */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have;
    input leaf
          lgd
          reps;
    cards;
    1 0.15 0
    2 0.25 1
    3 0.35 1
    3 0.23 0
    2 0.27 1
    3 0.31 1
    3 0.41 0
    2 0.22 0
    1 0.51 0
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /* LEAF     LGD    REPS                                                                                                   */
    /*                                                                                                                        */
    /*   1     0.15      0                                                                                                    */
    /*   2     0.25      1                                                                                                    */
    /*   3     0.35      1                                                                                                    */
    /*   3     0.23      0                                                                                                    */
    /*   2     0.27      1                                                                                                    */
    /*   3     0.31      1                                                                                                    */
    /*   3     0.41      0                                                                                                    */
    /*   2     0.22      0                                                                                                    */
    /*   1     0.51      0                                                                                                    */
    /**************************************************************************************************************************/

    /*       _        _   _                                  _
    / |  ___| |_ __ _| |_(_) ___   _ __ ___ _ __   ___  _ __| |_
    | | / __| __/ _` | __| |/ __| | `__/ _ \ `_ \ / _ \| `__| __|
    | | \__ \ || (_| | |_| | (__  | | |  __/ |_) | (_) | |  | |_
    |_| |___/\__\__,_|\__|_|\___| |_|  \___| .__/ \___/|_|   \__|
                                           |_|
    */

    Options FORMCHAR='|----|+|---+=|-/\<>*' ;

    proc tabulate data=have;
    class leaf reps;
    var LGD;
    table (leaf all),
      (reps All)*N
      PCTN LGD*Mean /rts=7;
    run;

    /**************************************************************************************************************************/
    /*  ------------------------------------------------------------------------                                              */
    /* |     |          REPS           |            |            |            |                                               */
    /* |     |-------------------------|            |            |            |                                               */
    /* |     |     0      |     1      |    All     |            |    LGD     |                                               */
    /* |     |------------+------------+------------|            |------------|                                               */
    /* |     |     N      |     N      |     N      |    PctN    |    Mean    |                                               */
    /* |-----+------------+------------+------------+------------+------------|                                               */
    /* |LEAF |            |            |            |            |            |                                               */
    /* |-----|            |            |            |            |            |                                               */
    /* |1    |        2.00|           0|        2.00|       22.22|        0.33|                                               */
    /* |-----+------------+------------+------------+------------+------------|                                               */
    /* |2    |        1.00|        2.00|        3.00|       33.33|        0.25|                                               */
    /* |-----+------------+------------+------------+------------+------------|                                               */
    /* |3    |        2.00|        2.00|        4.00|       44.44|        0.33|                                               */
    /* |-----+------------+------------+------------+------------+------------|                                               */
    /* |All  |        5.00|        4.00|        9.00|      100.00|        0.30|                                               */
    /* ------------------------------------------------------------------------                                               */
    /**************************************************************************************************************************/

    /*___    _        _           _       _            _       _                 _
    |___ \  | |_ __ _| |__  _   _| | __ _| |_ ___   __| | __ _| |_ __ _ ___  ___| |_
      __) | | __/ _` | `_ \| | | | |/ _` | __/ _ \ / _` |/ _` | __/ _` / __|/ _ \ __|
     / __/  | || (_| | |_) | |_| | | (_| | ||  __/| (_| | (_| | || (_| \__ \  __/ |_
    |_____|  \__\__,_|_.__/ \__,_|_|\__,_|\__\___| \__,_|\__,_|\__\__,_|___/\___|\__|

    */

    proc datasets lib=work
      nolist nodetails;
     delete want;
    run;quit;

    title;
    footnote;

    %utlfkil(d:/txt/tab.txt);

    ods listing
      file="d:/txt/tab.txt";

    options
       missing=0
       formchar=","
       ls=255;

    title
     ",LEAF,REP0,REP1,REPALL,PCTN,AVG_LGD,";

    proc tabulate data=have;

    class leaf reps;

    var lgd;

    table (leaf="" all="SUM"),
          (reps="" all="n")*n=""
           pctn lgd*mean=""
       /rts=6 box="leaf";
    run;
    ods listing;

    proc import
     datafile=
      "d:/txt/tab.txt"

     dbms=csv

     out=want (
      drop=var:
      where=(not missing(leaf)))

     replace;
     getnames=YES;
     datarow=6;

    run;quit;

    options FORMCHAR='|----|+|---+=|-/\<>*' ;

    /**************************************************************************************************************************/
    /*   Intermediate dataset d:/txt/tab.txt                                                                                  */
    /*                                                                                                                        */
    /*  ,LEAF,REP0,REP1,REPALL,PCTN,AVG_LGD,                                                                                  */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  ,leaf,     0      ,     1      ,     n      ,    PctN    ,    LGD     ,                                               */
    /*                                                                                                                        */
    /*  ,1   ,        2.00,           0,        2.00,       22.22,        0.33,                                               */
    /*                                                                                                                        */
    /*  ,2   ,        1.00,        2.00,        3.00,       33.33,        0.25,                                               */
    /*                                                                                                                        */
    /*  ,3   ,        2.00,        2.00,        4.00,       44.44,        0.33,                                               */
    /*                                                                                                                        */
    /*  ,SUM ,        5.00,        4.00,        9.00,      100.00,        0.30,                                               */
    /*                                                                                                                        */
    /*  WORK.WANT                                                                                                             */
    /*   LEAF    REP0    REP1    REPALL     PCTN     AVG_LGD                                                                  */
    /*                                                                                                                        */
    /*   1         2       0        2       22.22      0.33                                                                   */
    /*   2         1       2        3       33.33      0.25                                                                   */
    /*   3         2       2        4       44.44      0.33                                                                   */
    /*   SUM       5       4        9      100.00      0.30                                                                   */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
