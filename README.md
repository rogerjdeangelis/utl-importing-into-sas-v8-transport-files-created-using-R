# utl-importing-into-sas-v8-transport-files-created-using-R
Importing into SAS V8 transport files created using R
    Importing into SAS V8 transport files created using R

    See these two github repositories

    https://tinyurl.com/rxasm43
    https://github.com/rogerjdeangelis/utl-importing-into-sas-v8-transport-files-created-using-R

    https://tinyurl.com/y8q46cel
    https://github.com/rogerjdeangelis/utl_importing_r_created_v8_transport_files_into_sas_wps

    SAS Forum
    https://communities.sas.com/t5/SAS-Programming/XPT-file-example-in-v8-format/m-p/628364

    The V8 transport file format provides the following:

    1. Variable names can be up to 32 characters, and they are stored in their original case (upper or lower).
    2. Character variables can have lengths up to 32,767 bytes
    3. Character variable labels can have lengths up to 256 bytes.
    4  Variable names can contain any characters other than null ('00'x). Any variable name that contains
    5. Characters other than alphanumeric or underscores is represented in the SAS language as an n-literal (for example, 'a b'n).
    6. Table/Dataset names longer than 200 bytes.

    R can create V8 transport files, but I was unable to import formats or character variables over 200 bytes using SAS xpt2loc macro (SAS 9.4M2).
    I turned off formats and edited the code to handle very long character strings.

    You can find the macro at

    https://tinyurl.com/up46jkm
    https://github.com/rogerjdeangelis/utl_importing_r_created_v8_transport_files_into_sas_wps/blob/master/utl_importing_r_created_v8_transport_files_into_sas_wps_xpt2loc.sas

    and
    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories



    There are two sample V8 SAS Export files created using R

    https://tinyurl.com/vvyfz3r
    https://github.com/rogerjdeangelis/utl_importing_r_created_v8_transport_files_into_sas_wps/blob/master/have8.xpt

    https://tinyurl.com/rjn2amn
    https://github.com/rogerjdeangelis/utl_importing_r_created_v8_transport_files_into_sas_wps/blob/master/iris.xpt

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    * I removed formats;
    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      length chr500 $500;
      label chr500="String of 500 characters";
      set sashelp.iris;
      format _all_;
      informat _all_;
      chr500=repeat('A',499);
    run;quit;

    Data Set Name SD1.HAVE    Observations  150

    Variables in Creation Order

    #    Variable       Type    Len    Label

    1    CHR500         Char    500    String of 500 characters
    2    SPECIES        Char     10    Iris Species
    3    SEPALLENGTH    Num       8    Sepal Length (mm)
    4    SEPALWIDTH     Num       8    Sepal Width (mm)
    5    PETALLENGTH    Num       8    Petal Length (mm)
    6    PETALWIDTH     Num       8    Petal Width (mm)

    * create V8 transport file;

     %utl_submit_r64('
      library(haven);
      have<-read_sas("d:/sd1/have.sas7bdat");
      write_xpt(have,"d:/xpt/have8.xpt",version=8);
      ');

    * hex dump;

    NOTE RECORD SIX HAS THE DATASET NAME

    %utlrulr
       (
        uinflt =d:/xpt/have8.xpt,
        uprnlen =80,
        ulrecl  =80,
        urecfm   =F,
        uobs = 10,
        uchrtyp =ascii,
        uotflt =d:\txt\have.txt
       );

    --- Record Number ---  1   ---  Record Length ---- 80

    HEADER RECORD*******LIBV8   HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444532224444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAC92680008514520253F24111111100000000000000000000000000000000


     --- Record Number ---  2   ---  Record Length ---- 80

    SAS     SAS     SASLIB  6.06    bsd4.2                          04MAR20:22:23:35
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    54522222545222225454442232332222676323222222222222222222222222223344533333333333
    3130000031300000313C92006E0600002344E20000000000000000000000000004D1220A22A23A35


     --- Record Number ---  3   ---  Record Length ---- 80

    04MAR20:22:23:35
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    33445333333333332222222222222222222222222222222222222222222222222222222222222222
    04D1220A22A23A350000000000000000000000000000000000000000000000000000000000000000


     --- Record Number ---  4   ---  Record Length ---- 80

    HEADER RECORD*******MEMBV8  HEADER RECORD!!!!!!!000000000000000001600000000140
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444453224444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAD5D268008514520253F24111111100000000000000000160000000014000


     --- Record Number ---  5   ---  Record Length ---- 80

    HEADER RECORD*******DSCPTV8 HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222454555324444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAA433046808514520253F24111111100000000000000000000000000000000


     --- Record Number ---  6   ---  Record Length ---- 80

    SAS     have8                           SASDATA 6.06    bsd4.2  04MAR20:22:23:35
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    54522222667632222222222222222222222222225454454232332222676323223344533333333333
    3130000081658000000000000000000000000000313414106E0600002344E20004D1220A22A23A35

    ...

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    Note the different name (kas the export name)
    Unlike V5 transport we have

    Variable names longer than 8 characters
    Character length longer than 200 bytes

      WORK.HAVE8

                     Variables in Creation Order

     #    Variable       Type    Len    Label

     1    CHR500         Char    500    String of 500 characters
     2    SPECIES        Char     10    Iris Species
     3    SEPALLENGTH    Num       8    Sepal Length (mm)
     4    SEPALWIDTH     Num       8    Sepal Width (mm)
     5    PETALLENGTH    Num       8    Petal Length (mm)
     6    PETALWIDTH     Num       8    Petal Width (mm)


    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    * using my macro;

     %xpt2loc(libref=work,
       memlist=_all_,
       filespec='d:/xpt/have8.xpt' );

