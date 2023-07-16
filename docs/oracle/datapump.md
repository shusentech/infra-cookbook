# datapump use cases

1. [export-data-onnly](#export-data-only)
2. [import-data-only](#import-data-only)
3. [metadata only](#metadata-only)
4. [exclude tables](#exclude-tables)

## export-data-only

```
DECLARE
h number;
BEGIN
  h := DBMS_DATAPUMP.OPEN( operation => 'EXPORT', job_mode => 'SCHEMA', job_name=>null);
  DBMS_DATAPUMP.ADD_FILE( handle => h, 
    filename => 'demo-%U.dmp', 
    directory => 'DATA_PUMP_DIR', 
    filesize => '30G',
    filetype => dbms_datapump.ku$_file_type_dump_file);
  DBMS_DATAPUMP.ADD_FILE( handle => h, 
    filename => 'exp-demo.log', 
    directory => 'DATA_PUMP_DIR', 
    filetype => dbms_datapump.ku$_file_type_log_file);
  DBMS_DATAPUMP.METADATA_FILTER(
    handle => h,
    name   => 'SCHEMA_EXPR',
    value  => ' in (''TEST1'',''TEST2'',''TEST3'')');
  DBMS_DATAPUMP.SET_PARALLEL(handle => h, degree => 4);
  DBMS_DATAPUMP.SET_PARAMETER(handle => h, name => 'INCLUDE_METADATA', value => 0);
  DBMS_DATAPUMP.START_JOB(h);
END;
/
```

## import-data-only

```
DECLARE
hdnl NUMBER;
BEGIN
  hdnl := DBMS_DATAPUMP.OPEN( operation => 'IMPORT', job_mode => 'SCHEMA', job_name=>null);
  DBMS_DATAPUMP.ADD_FILE( handle => hdnl, 
    filename => 'demo-%U.dmp', 
    directory => 'DATA_PUMP_DIR', 
    filetype => dbms_datapump.ku$_file_type_dump_file);
  DBMS_DATAPUMP.ADD_FILE( handle => hdnl, 
    filename => 'imp-demo.log', 
    directory => 'DATA_PUMP_DIR', 
    filetype => dbms_datapump.ku$_file_type_log_file);
  DBMS_DATAPUMP.METADATA_FILTER(
    handle => hdnl,
    name   => 'SCHEMA_EXPR',
    value  => ' in (''TEST1'',''TEST2'',''TEST3'')');
  DBMS_DATAPUMP.SET_PARALLEL(handle => hdnl, degree => 4);
  DBMS_DATAPUMP.SET_PARAMETER(handle => hdnl, name => 'TABLE_EXISTS_ACTION', value => 'TRUNCATE');
  DBMS_DATAPUMP.SET_PARAMETER(handle => hdnl, name => 'INCLUDE_METADATA', value => 0);
  DBMS_DATAPUMP.START_JOB(hdnl);
END;
/
```

## metadata only

```
DECLARE
h number;
BEGIN
  h := DBMS_DATAPUMP.OPEN( operation => 'EXPORT', job_mode => 'SCHEMA', job_name=>null);
  DBMS_DATAPUMP.ADD_FILE( handle => h, 
    filename => 'demp.dmp', 
    directory => 'DATA_PUMP_DIR', 
    filetype => dbms_datapump.ku$_file_type_dump_file);
  DBMS_DATAPUMP.ADD_FILE( handle => h, 
    filename => 'exp-demp.log', 
    directory => 'DATA_PUMP_DIR', 
    filetype => dbms_datapump.ku$_file_type_log_file);
  DBMS_DATAPUMP.METADATA_FILTER(
    handle => h,
    name   => 'SCHEMA_EXPR',
    value  => '= ''TEST1''');
  DBMS_DATAPUMP.METADATA_FILTER(
    handle => h,
    name   => 'INCLUDE_PATH_EXPR',
    value  => '= ''METADATA_ONLY''');
  DBMS_DATAPUMP.SET_PARALLEL(handle => h, degree => 1);
  DBMS_DATAPUMP.START_JOB(h);
END;
/
```

## exclude tables

```
DIRECTORY=DATAPUMP
dumpfile=test-%U.dmp 
logfile=exp-test.log 
parallel=6
filesize=20G
EXCLUDE=TABLE:"in ('TAB_X', 'TAB_Y')"
schemas=TEST1,TEST2,TEST3
```

