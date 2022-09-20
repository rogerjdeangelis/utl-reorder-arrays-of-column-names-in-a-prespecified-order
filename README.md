# utl-reorder-arrays-of-column-names-in-a-prespecified-order
Reorder arrays of column names in a prespecified order
    %let pgm=utl-reorder-arrays-of-column-names-in-a-prespecified-order;

    Reorder arrays of column names in a prespecified order

    Order Column names like this

    ID HEIGHT_1 HEIGHT_2 HEIGHT_3 WEIGHT_1 WEIGHT_2 WEIGHT_3 HIP_1 HIP_2 HIP_3

    github
    https://tinyurl.com/y74bdjxk
    https://github.com/rogerjdeangelis/utl-reorder-arrays-of-column-names-in-a-prespecified-order

    stackoverflow R
    https://stackoverflow.com/questions/73776552/how-to-order-variables-in-blocks

     Three Solution array, retain and length all work
     For arrray subsitute array[&n*&n=1] for retain

         1. SAS
         2. WPS
         3. SAS DOSUBL

    macros
    https://tinyurl.com/58pp9nz6
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    libname sd1 "d:/sd1";

    data sd1.have;
       input ID HEIGHT_1 WEIGHT_1 HIP_1 HEIGHT_2 WEIGHT_2 HIP_2 HEIGHT_3 WEIGHT_3 HIP_3;
    cards4;
    1 180 70 25 167 50 23 175 50 28
    2 192 90 29 168 50 27 176 70 28
    ;;;;
    run;quit;


      ID    HEIGHT_1    WEIGHT_1    HIP_1    HEIGHT_2    WEIGHT_2    HIP_2    HEIGHT_3    WEIGHT_3    HIP_3

       1       180         70         25        167         50         23        175         50         28
       2       192         90         29        168         50         27        176         70         28

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    Up to 40 obs WORK.WANT total obs=2 19SEP2022:16:20:21

              Put arra of heights first                    Then                           Then
            ================================    ================================   =========================
      ID    HEIGHT_1    HEIGHT_2    HEIGHT_3    WEIGHT_1    WEIGHT_2    WEIGHT_3    HIP_1    HIP_2    HIP_3

       1       180         167         175         70          50          50         25       23       28
       2       192         168         176         90          50          70         29       27       28

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
     _
    / |    ___  __ _ ___
    | |   / __|/ _` / __|
    | |_  \__ \ (_| \__ \
    |_(_) |___/\__,_|___/

    */
    * get the size of the vectors;;
    %let n =  %sysfunc(countw(%utl_varlist(have,prx=/height/i)));

    data want;
       retain
          id
          height_1-height_&n
          weight_1-weight_&n
             hip_1-   hip_&n ;
     set sd1.have;
    run;quit;

    /*___
    |___ \    __      ___ __  ___
      __) |   \ \ /\ / / `_ \/ __|
     / __/ _   \ V  V /| |_) \__ \
    |_____(_)   \_/\_/ | .__/|___/
                       |_|
    */

    %utl_submit_wps64('

    libname sd1 "d:/sd1";

    options sasautos=(c:/macros_rjd sasautos);

    %let n =  %sysfunc(countw(%utl_varlist(have,prx=/height/i)));

    data want;

       retain
          id
          height_1-height_&n
          weight_1-weight_&n
             hip_1-   hip_&n ;
     set sd1.have;

    run;quit;

    proc print;
    run;quit;
    ');

    The WPS System

    Obs    ID    HEIGHT_1    HEIGHT_2    HEIGHT_3    WEIGHT_1    WEIGHT_2    WEIGHT_3    HIP_1    HIP_2    HIP_3

     1      1       180         167         175         70          50          50         25       23       28
     2      2       192         168         176         90          50          70         29       27       28


    /*____                        _                 _     _
    |___ /    ___  __ _ ___    __| | ___  ___ _   _| |__ | |
      |_ \   / __|/ _` / __|  / _` |/ _ \/ __| | | | `_ \| |
     ___) |  \__ \ (_| \__ \ | (_| | (_) \__ \ |_| | |_) | |
    |____(_) |___/\__,_|___/  \__,_|\___/|___/\__,_|_.__/|_|

    */

    %symdel n / nowarn;
    data want_dosubl;

       if _n_=0 then do;
          %dosubl('
              data _null_;
                dsid  = open("sd1.have(keep=height:)", "i");
                nv    = attrn(dsid,"nvars");
                rc    = close(dsid);
                call symputx("n",nv);
              run;quit;
              ');
          end;

       retain
          id
          height_1-height_&n
          weight_1-weight_&n
             hip_1-   hip_&n ;
     set sd1.have;
    run;quit;


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
