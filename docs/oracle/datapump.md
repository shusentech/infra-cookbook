# datapump use cases

- [export-data-onnly] (#export-data-only)
- [import-data-only] (#import-data-only)

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