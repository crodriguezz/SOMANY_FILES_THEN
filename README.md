# SOMANY_FILES_THEN
https://stackoverflow.com/questions/3750192/how-to-write-to-files-using-utl-file-in-oracle

####
https://living-sun.com/es/plsql/675613-how-to-write-to-files-using-utl_file-in-oracle-plsql.html




https://stackoverflow.com/questions/3750192/how-to-write-to-files-using-utl-file-in-oracle

https://stackoverflow.com/questions/3750192/how-to-write-to-files-using-utl-file-in-oracle


#### TECJJSJSJS
https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70919




###Extend UTL_FILE with function to return list of files in directory
Created on Jun 15, 2016 11:46 AM by MortenBraten - Last Modified: Jun 15, 2016 11:46 AM
score
480
You have not voted. Active

UTL_FILE is lacking a function that returns a list of files in a given directory. This is very unconvenient, as it is not possible to write PL/SQL code to process one or more files in a directory whose names are unknown. For example, I want to write a PL/SQL procedure that reads and processes a bunch of XML files that another process/system dumps in a folder which is mapped to a database directory object. With UTL_FILE I can only read files when I know the file name. If I could read out a list of file names, then I could loop through each of the files found and process them one by one.

 

The proposal is to add a new function called GET_FILE_LIST (or something like that) to UTL_FILE which would return a list of files.

The function signature could look like this:

 

function get_file_list (p_directory_name in varchar2,

                        p_file_pattern in varchar2 := null,

                                              p_max_files in number := null) return t_file_list

 

Note: Currently there is an undocumented procedure called dbms_backup_restore.searchfiles which can be used to implement such functionality, as shown here:

 

https://github.com/mortenbra/alexandria-plsql-utils/blob/master/extras/utl_file_nonstandard.pkb#L18

 

However, this proposal is made to get an official, documented and supported function into UTL_FILE, where it belongs.



--
create or replace package body sys.utl_file_nonstandard
as

  /*

  Purpose:    Package contains functionality missing from the standard UTL_FILE package

  Remarks:    This package MUST be created in the SYS schema due to a dependency on an X$ table
              See http://www.chrispoole.co.uk/tips/plsqltip2.htm

  Who     Date        Description
  ------  ----------  -------------------------------------
  MBR     30.07.2011  Created
  
  */


function get_file_list (p_directory_name in varchar2,
                        p_file_pattern in varchar2 := null,
                        p_max_files in number := null) return t_file_list
as
  l_user                 dba_tab_privs.grantee%type := user;
  l_directory_name       dba_directories.directory_name%type := upper(p_directory_name);
  l_directory_path       dba_directories.directory_path%type;
  l_pattern              varchar2(2000);
  l_dummy                varchar2(2000) := null;
  l_dir_sep              varchar2(1) := null;
  l_returnvalue          t_file_list;

  e_bug_data_conv        exception; 
  pragma exception_init  (e_bug_data_conv, -6502);

begin

  /*

  Purpose:    get list of files in directory

  Remarks:    This package MUST be created in the SYS schema due to a dependence on an X$ table
              See http://www.chrispoole.co.uk/tips/plsqltip2.htm

  Who     Date        Description
  ------  ----------  -------------------------------------
  MBR     30.07.2011  Created
  MBR     16.08.2011  Workaround for ORA-06502, see http://forums.oracle.com/forums/thread.jspa?threadID=662413
  
  */

  begin
    select table_name
    into l_directory_name
    from dba_tab_privs
    where table_name = l_directory_name
    and grantee = l_user
    and privilege = 'READ'
    and rownum = 1;
  exception
    when no_data_found then
      raise_application_error (-20000, 'User ' || l_user || ' does not have READ privilege on directory ' || l_directory_name);
  end;

  begin
    select directory_path
    into l_directory_path
    from dba_directories
    where directory_name = l_directory_name;
  exception
    when no_data_found then
      raise_application_error (-20000, 'Directory ' || l_directory_name || ' not found');
  end;

  -- Unix or Windows system?

  if instr(l_directory_path, '/') > 0 then
    l_dir_sep := '/';
  else
    l_dir_sep := '\';
  end if;

  -- make sure the path has a trailing directory separator

  if instr(l_directory_path, l_dir_sep, length(l_directory_path)) = 0 then
    l_directory_path := l_directory_path || l_dir_sep;
  end if;

  if p_file_pattern is not null then
    l_pattern := l_directory_path || '\' || p_file_pattern;
  else
    l_pattern := l_directory_path;
  end if;

  begin
    dbms_backup_restore.searchfiles (l_pattern, ns => l_dummy);
  exception
    when e_bug_data_conv then
      -- workaround: just try again
      dbms_backup_restore.searchfiles (l_pattern, ns => l_dummy);
  end;

  select fname_krbmsft as file_name
  bulk collect into l_returnvalue
  from x$krbmsft
  order by 1;

  return l_returnvalue;

end get_file_list;


end utl_file_nonstandard;
/

# # # # # BBBBBB

----https://rstopup.com/como-escribir-en-los-archivos-utilizando-utl_file-en-oracle.html-----
PROCEDURE usp_OUTPUT_ToFileAscii(p_Path IN VARCHAR2, p_FileName IN VARCHAR2, p_Input IN refCursor, p_Header in VARCHAR2, p_Footer IN VARCHAR2, p_WriteMode VARCHAR2) IS
vLine VARCHAR2(30000);
vFile UTL_FILE.file_type; 
vExists boolean;
vLength number;
vBlockSize number;
BEGIN
UTL_FILE.fgetattr(p_path, p_FileName, vExists, vLength, vBlockSize);
FETCH p_Input INTO vLine;
IF p_input%ROWCOUNT > 0
THEN
IF vExists THEN
vFile := UTL_FILE.FOPEN_NCHAR(p_Path, p_FileName, p_WriteMode);
ELSE
--even if the append flag is passed if the file doesn't exist open it with W.
vFile := UTL_FILE.FOPEN(p_Path, p_FileName, 'W');
END IF;
--GET HANDLE TO FILE
IF p_Header IS NOT NULL THEN 
UTL_FILE.PUT_LINE(vFile, p_Header);
END IF;
UTL_FILE.PUT_LINE(vFile, vLine);
DBMS_OUTPUT.PUT_LINE('Record count > 0');
--LOOP THROUGH CURSOR VAR
LOOP
FETCH p_Input INTO vLine;
EXIT WHEN p_Input%NOTFOUND;
UTL_FILE.PUT_LINE(vFile, vLine);
END LOOP;
IF p_Footer IS NOT NULL THEN 
UTL_FILE.PUT_LINE(vFile, p_Footer);
END IF;
CLOSE p_Input;
UTL_FILE.FCLOSE(vFile);
ELSE
DBMS_OUTPUT.PUT_LINE('Record count = 0');
END IF; 
EXCEPTION
WHEN UTL_FILE.INVALID_PATH THEN 
DBMS_OUTPUT.PUT_LINE ('invalid_path'); 
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;
WHEN UTL_FILE.INVALID_MODE THEN 
DBMS_OUTPUT.PUT_LINE ('invalid_mode'); 
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;
WHEN UTL_FILE.INVALID_FILEHANDLE THEN 
DBMS_OUTPUT.PUT_LINE ('invalid_filehandle'); 
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;
WHEN UTL_FILE.INVALID_OPERATION THEN 
DBMS_OUTPUT.PUT_LINE ('invalid_operation'); 
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;
WHEN UTL_FILE.READ_ERROR THEN  
DBMS_OUTPUT.PUT_LINE ('read_error');
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;
WHEN UTL_FILE.WRITE_ERROR THEN 
DBMS_OUTPUT.PUT_LINE ('write_error'); 
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;
WHEN UTL_FILE.INTERNAL_ERROR THEN 
DBMS_OUTPUT.PUT_LINE ('internal_error'); 
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;            
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE ('other write error'); 
DBMS_OUTPUT.PUT_LINE(SQLERRM);
RAISE;
END;



...
########...----

    Oracle PL / SQLSystem PackagesUTL_FILE

Catch different UTL_FILE related errors

  


SET ECHO ON
SET SERVEROUTPUT ON

DECLARE
     emp_data UTL_FILE.FILE_TYPE;
BEGIN
    emp_data := UTL_FILE.FOPEN ('c:\a','empdata.csv','W');
    UTL_FILE.FCLOSE (emp_data);

EXCEPTION
    WHEN UTL_FILE.internal_error THEN
        DBMS_OUTPUT.PUT_LINE ('UTL_FILE: An internal error occurred.');
        UTL_FILE.FCLOSE_ALL;
    WHEN UTL_FILE.invalid_filehandle THEN
        DBMS_OUTPUT.PUT_LINE ('UTL_FILE: The file handle was invalid.');
        UTL_FILE.FCLOSE_ALL;
    WHEN UTL_FILE.invalid_mode THEN
        DBMS_OUTPUT.PUT_LINE ('UTL_FILE: An invalid open mode was given.');
        UTL_FILE.FCLOSE_ALL;
    WHEN UTL_FILE.invalid_operation THEN
        DBMS_OUTPUT.PUT_LINE ('UTL_FILE: An invalid operation was attempted.');
        UTL_FILE.FCLOSE_ALL;
    WHEN UTL_FILE.invalid_path THEN
        DBMS_OUTPUT.PUT_LINE ('UTL_FILE: An invalid path was give for the file.');
        UTL_FILE.FCLOSE_ALL;
    WHEN UTL_FILE.read_error THEN
        DBMS_OUTPUT.PUT_LINE ('UTL_FILE: A read error occurred.');
        UTL_FILE.FCLOSE_ALL;
    WHEN UTL_FILE.write_error THEN
        DBMS_OUTPUT.PUT_LINE ('UTL_FILE: A write error occurred.');
        UTL_FILE.FCLOSE_ALL;
    WHEN others THEN
        DBMS_OUTPUT.PUT_LINE ('Some other error occurred.');
        UTL_FILE.FCLOSE_ALL;
END;
/

   
  
#####nene
https://connor-mcdonald.com/2018/06/20/the-death-of-utl_file/

####
https://living-sun.com/es/plsql/675613-how-to-write-to-files-using-utl_file-in-oracle-plsql.html


###https://connor-mcdonald.com/2018/06/20/the-death-of-utl_file/

...
...
