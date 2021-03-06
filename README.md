# utl_natural_sorting_to_sort_columns_of_a_dataset_and_dataframe
 R/SAS: Natural sorting to sort columns of a dataset and dataframe.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Natural sorting to sort columns of a dataset and dataframe

    github
    https://goo.gl/ZyjLNe
    https://github.com/rogerjdeangelis/utl_natural_sorting_to_sort_columns_of_a_dataset_and_dataframe

      Two solutions
         1. R    (mixedsort)
         2. SAS  (sortseq=linguistic(Numeric_Collation=ON)

    see
    https://goo.gl/Ws832a
    https://stackoverflow.com/questions/48292178/natural-sorting-to-sort-columns-of-a-dataframe-by-column-name

    G5w profile
    https://stackoverflow.com/users/4752675/g5w


    INPUT
    =====

     SD1.HAVE total obs=10

       D10    D3    D70    D2    D5    D101    D6    D4    D8

        55    42     54     8    69      7     95    34    83
        14    67     61    12    11     62     73    16    70
        88    26     76    94    28     29     12    64    91
         4    14     68    68    23     51     75    14    63
        23    31     80    18    41      8     46    63    28
        61    86     65    25    23     61     17    32    56
        47    26     48    37    16     79     56     0    74
        86    84     17    72    20     79     20    58    93
        18    79     30    19    17     52     97    24    41
        79    46     46    13    56     40     77     7    22


    PROCESS (need sort option sortseq=linguistic(Numeric_Collation=ON) )
    ====================================================================

     R Working Code

       want <- have[,mixedsort(colnames(have))];

     SAS

       data want;

         * GET META DATA;
         if _n_=0 then do;
            %let rc=%sysfunc(dosubl('
                * proc sql dictionaries can be very slow;
                proc transpose data=sd1.have out=havXpo(keep=_name_);
                   var _all_;
                run;quit;
                proc sort in=havXpo out=havSrt
                   sortseq=linguistic(Numeric_Collation=ON);
                  by _name_;
                run;quit;
                proc sql;
                   select _name_ into :nams separated by " " from havSrt
                quit;
            '));
         end;

         retain &nams.;
         set have;

       run;quit;


    OUTPUT
    ======

     WORK.WANT total obs=10

       D2    D3    D4    D5    D6    D8    D10    D70    D101

        8    42    34    69    95    83     55     54      7
       12    67    16    11    73    70     14     61     62
       94    26    64    28    12    91     88     76     29
       68    14    14    23    75    63      4     68     51
       18    31    63    41    46    28     23     80      8
       25    86    32    23    17    56     61     65     61
       37    26     0    16    56    74     47     48     79
       72    84    58    20    20    93     86     17     79
       19    79    24    17    97    41     18     30     52
       13    46     7    56    77    22     79     46     40

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have(drop=rec sfx);
     array ds[9] D10 D3 D70 D2 D5 D101 D6 D4 D8;
     do rec=1 to 10;
       do sfx=1 to 9;
         ds[sfx]=int(100*uniform(-1));
       end;
       output;
     end;
    run;quit;

    *____
    |  _ \
    | |_) |
    |  _ <
    |_| \_\

    ;

    %utl_submit_wps64('
    libname sd1 sas7bdat "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.2";
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.2/etc/Rprofile.site", echo=T);
    library(haven);
    library(gtools);
    have<-read_sas("d:/sd1/have.sas7bdat");
    mixedsort(colnames(have));
    want <- have[,mixedsort(colnames(have))];
    endsubmit;
    import r=want  data=wrk.wantwps;
    run;quit;
    ');

    *
     ___  __ _ ___
    / __|/ _` / __|
    \__ \ (_| \__ \
    |___/\__,_|___/

    ;

    data want;

      * GET META DATA;
      if _n_=0 then do;
         %let rc=%sysfunc(dosubl('
             * proc sql dictionaries can be very slow;
             proc transpose data=sd1.have out=havXpo(keep=_name_);
                var _all_;
             run;quit;
             proc sort in=havXpo out=havSrt
                sortseq=linguistic(Numeric_Collation=ON);
               by _name_;
             run;quit;
             proc sql;
                select _name_ into :nams separated by " " from havSrt
             quit;
         '));
      end;

      retain &nams.;
      set have;

    run;quit;

