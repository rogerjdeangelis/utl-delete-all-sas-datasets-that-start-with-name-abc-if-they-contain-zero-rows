# utl-delete-all-sas-datasets-that-start-with-name-abc-if-they-contain-zero-rows
Delete all sas datasets that start with name abc  if they contain zero rows
    Delete all sas datasets that start with name abc  if they contain zero rows

    I thought it might be useful to post the status of proc datasets delete

     Two Solutions

           a. single DOSUBL packaged datastep
           b. single proc sql


    github
    https://tinyurl.com/uoqf3ln
    https://github.com/rogerjdeangelis/utl-delete-all-sas-datasets-that-start-with-name-abc-if-they-contain-zero-rows

    SAS Forum
    https://tinyurl.com/u4p2rly
    https://communities.sas.com/t5/SAS-Programming/delete-all-data-sets-that-start-with-name-quot-ABC-quot-if-they/m-p/614319

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    data have_alpha have_beta have_gamma;
      output have_beta;
    run;quit;

    *           _
     _ __ _   _| | ___  ___
    | '__| | | | |/ _ \/ __|
    | |  | |_| | |  __/\__ \
    |_|   \__,_|_|\___||___/

    ;
                                                                          | Rules
    NOTE: The data set WORK.HAVE_ALPHA has 0 observations and 0 variables.|  delete table
                                                                          |
    NOTE: The data set WORK.HAVE_BETA has 1 observations and 0 variables. |  keep
                                                                          |
    NOTE: The data set WORK.HAVE_GAMMA has 0 observations and 0 variables.|  delete  table

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    SYMBOLGEN:  Macro variable SYSERR resolves to 0
    **** proc datasets completed ****

    NOTE: Deleting WORK.HAVE_ALPHA (memtype=DATA).
    NOTE: Deleting WORK.HAVE_GAMMA (memtype=DATA).

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|
                   _       _            _
      __ _      __| | __ _| |_ __ _ ___| |_ ___ _ __
     / _` |    / _` |/ _` | __/ _` / __| __/ _ \ '_ \
    | (_| |_  | (_| | (_| | || (_| \__ \ ||  __/ |_) |
     \__,_(_)  \__,_|\__,_|\__\__,_|___/\__\___| .__/
                                               |_|
    ;
    %symdel delTbl / nowarn;

    data have_alpha have_beta have_gamma;
      output have_beta;
    run;quit;

    data _null_;

      if _n_=0 then do; %let rc=%sysfunc(dosubl('
          proc sql;
              select memname into :delTbl separated by " "
              from sashelp.vtable
              where memname eqt "HAVE_" and nlobs=0
          ;quit;
          '));
      end;

      rc=dosubl('

         proc datasets lib=work nolist;
             delete &delTbl;
         run;quit;
         %let cc=&syserr;
      ');

       if symgetn('cc')=0 then putlog "**** proc datasets completed ****";
       else putlog "**** proc datasets failed ****";
       output;
       stop;

    run;quit;

    *_                  _
    | |__     ___  __ _| |
    | '_ \   / __|/ _` | |
    | |_) |  \__ \ (_| | |
    |_.__(_) |___/\__, |_|
                     |_|
    ;

    %symdel delTbl / nowarn;

    data have_alpha have_beta have_gamma;
      output have_beta;
    run;quit;

    proc sql;
        select catx(" ","drop table",memname) into :delTbl separated by ";"
        from sashelp.vtable
        where memname eqt "HAVE_" and nlobs=0
    ;
    &delTbl
    ;quit;

    SYMBOLGEN:  Macro variable DELTBL resolves to drop table HAVE_ALPHA;drop table HAVE_GAMMA
    NOTE: Table WORK.HAVE_ALPHA has been dropped.
    NOTE: Table WORK.HAVE_GAMMA has been dropped.



