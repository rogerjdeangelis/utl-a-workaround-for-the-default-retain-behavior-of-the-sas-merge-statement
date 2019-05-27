# utl-a-workaround-for-the-default-retain-behavior-of-the-sas-merge-statement
Workaround for the default retain behavior of the sas merge statement
    Workaround for the default retain behavior of the sas merge statement

    This is worth highlighting

    Given a template dataset that has all ids and vists. Merge the template with previous incomplete
    ID and vistits data adding missing values for records that are in the template and not in incomplete table.

    github
    https://tinyurl.com/yykoakxu
    https://github.com/rogerjdeangelis/utl-a-workaround-for-the-default-retain-behavior-of-the-sas-merge-statement

    KSharpe
    https://communities.sas.com/t5/user/viewprofilepage/user-id/18408

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    data template;
    input ID Visit;
    cards4;
    11 1
    11 2
    11 3
    11 4
    11 5
    23 1
    23 2
    23 3
    23 4
    65 1
    65 2
    ;;;;
    run;quit;


    data incomplete;
    input Id Height weight;
    cards4;
    11 156 98
    11 156 94
    11 157 92
    23 187 65
    23 187 68
    23 187 .
    23 187 70
    65 166 84
    65 167 86
    ;;;;
    run;quit;


    WORK.TEMPLATE total obs=11

            VISIT_
      ID     COUNT

      11       1
      11       2
      11       3
      11       4
      11       5
      23       1
      23       2
      23       3
      23       4
      65       1
      65       2

    WORK.INCOMPLETE total obs=9

      ID    HEIGHT    WEIGHT

      11      156       98
      11      156       94
      11      157       92
      23      187       65
      23      187       68
      23      187        .
      23      187       70
      65      166       84
      65      167       86

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    WORK.WANT total obs=11
                                             | RULES
                 VISIT_                      |
    Obs    ID     COUNT    HEIGHT    WEIGHT  |
                                             |
      1    11       1        156       98    |
      2    11       2        156       94    |
      3    11       3        157       92    |

      4    11       4          .        .    |  Add ID and Vistits
      5    11       5          .        .    |  from template

      6    23       1        187       65    |
      7    23       2        187       68    |
      8    23       3        187        .    |
      9    23       4        187       70    |
     10    65       1        166       84    |
     11    65       2        167       86    |


    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;


    data want;
      ina=0;
      inb=0;
      merge template(in=ina) incomplete(in=inb);
       by id;
       if inb=0 then call missing(height,weight);
       if ina;
    run;

    *         _ _   _                 _
    __      _(_) |_| |__   ___  _   _| |_
    \ \ /\ / / | __| '_ \ / _ \| | | | __|
     \ V  V /| | |_| | | | (_) | |_| | |_
      \_/\_/ |_|\__|_| |_|\___/ \__,_|\__|
                        _                                   _
    __      _____  _ __| | ____ _ _ __ ___  _   _ _ __   __| |
    \ \ /\ / / _ \| '__| |/ / _` | '__/ _ \| | | | '_ \ / _` |
     \ V  V / (_) | |  |   < (_| | | | (_) | |_| | | | | (_| |
      \_/\_/ \___/|_|  |_|\_\__,_|_|  \___/ \__,_|_| |_|\__,_|

    ;


    data want;

      merge template(in=ina) incomplete(in=inb);
      by id;

    run;

    WORK.WANT total obs=11

      ID    VISIT    HEIGHT    WEIGHT

      11      1        156       98
      11      2        156       94
      11      3        157       92   * height and weight retained;

      11      4        157       92   * should both be missing;
      11      5        157       92   * but were retained;

      23      1        187       65
      23      2        187       68
      23      3        187        .
      23      4        187       70
      65      1        166       84
      65      2        167       86


