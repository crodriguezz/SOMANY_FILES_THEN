# SOMANY_FILES_THEN
https://stackoverflow.com/questions/3750192/how-to-write-to-files-using-utl-file-in-oracle

####
https://living-sun.com/es/plsql/675613-how-to-write-to-files-using-utl_file-in-oracle-plsql.html




https://stackoverflow.com/questions/3750192/how-to-write-to-files-using-utl-file-in-oracle

https://stackoverflow.com/questions/3750192/how-to-write-to-files-using-utl-file-in-oracle


#### TECJJSJSJS
https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70919

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
