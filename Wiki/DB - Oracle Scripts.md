# Useful Oracle scripts




## Insert data
```sql
BEGIN
    DECLARE 
        test_variable NUMBER;
    BEGIN
        SELECT COLUMN_1
        INTO test_variable   
        FROM TABLE_1
        WHERE ID = 100;

        -- Inserting record 1
        MERGE INTO TABLE_2 target
        USING (SELECT 1 FROM DUAL) source
        ON (target.ID = 137)
        WHEN NOT MATCHED THEN
            INSERT (ID, TYPE_NAME, ENABLED, DESCRIPTION, SORT_ID, GROUP_ID)  
            VALUES (137, 'Sample type name 1', 1, 'Sample type name 1 description', test_variable, 20);  

        -- Inserting record 1
        MERGE INTO TABLE_2 target
        USING (SELECT 1 FROM DUAL) source
        ON (target.ID = 138)
        WHEN NOT MATCHED THEN
            INSERT (ID, TYPE_NAME, ENABLED, DESCRIPTION, SORT_ID, GROUP_ID)  
            VALUES (138, 'Sample type name 2', 1, 'Sample type name 2 description', test_variable, 20);  

        -- Inserting record 1
        MERGE INTO TABLE_2 target
        USING (SELECT 1 FROM DUAL) source
        ON (target.ID = 139)
        WHEN NOT MATCHED THEN
            INSERT (ID, TYPE_NAME, ENABLED, DESCRIPTION, SORT_ID, GROUP_ID)  
            VALUES (139, 'Sample type name 3', 1, 'Sample type name 3 description', test_variable, 20);  
        
        COMMIT;
    END;
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('An unknown error occurred: ' || SQLERRM);
END;
```

## Template for each SQL script
```sql
BEGIN
    DECLARE 
        test_variable NUMBER;
    BEGIN

        -- SQL script goes here... 
        
        COMMIT;
    END;
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('An unknown error occurred: ' || SQLERRM);
END;
```







