# Useful SQL scripts

## Get datatype of the column
``` sql
    SELECT @ApplicationId_Column_Datatype = DATA_TYPE 
    FROM INFORMATION_SCHEMA.COLUMNS
    WHERE TABLE_NAME = 'table name' 
    AND COLUMN_NAME = 'column name'
```

## Get primary key constraint name
``` sql
    SELECT @PK_Constraint_Name = [name]
    FROM [sysobjects]
    WHERE [xtype] = 'PK'
    AND [parent_obj] = OBJECT_ID('table name')
```

## Declare table as variable
``` sql
    DECLARE @ProductTotals TABLE
    (
      ProductID int, 
      Revenue money
    )

    INSERT INTO @ProductTotals (ProductID, Revenue)
      SELECT ProductID, SUM(UnitPrice * Quantity)
      FROM [Order Details]
      GROUP BY ProductID
```

## Check if table exist and columns exists

``` sql
    IF EXISTS(SELECT * FROM Information_Schema.Tables WHERE Table_Name='PersonnelSignInReasons') AND
       NOT EXISTS(SELECT * FROM Information_Schema.Tables WHERE Table_Name='PersonnelSignInReasons_new')
    BEGIN
        IF (COL_LENGTH('[dbo].[PersonnelSignInReasons]', 'Id') IS NOT NULL) AND 
            (COL_LENGTH('[dbo].[PersonnelSignInReasons]', 'Reason') IS NOT NULL) AND 
            (COL_LENGTH('[dbo].[PersonnelSignInReasons]', 'RelatedTo') IS NOT NULL)
        BEGIN
            -- SQL script goes here...
        END
    END
    GO  
```

## Check if primary key defined/exists
``` sql
    DECLARE @pkColumnName varchar(10)

    SELECT @pkColumnName = COLUMN_NAME
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE
    WHERE OBJECTPROPERTY(OBJECT_ID(CONSTRAINT_SCHEMA + '.' + QUOTENAME(CONSTRAINT_NAME)), 'IsPrimaryKey') = 1
    AND TABLE_NAME = 'PersonnelSignInReasons' AND TABLE_SCHEMA = 'dbo'

    IF (@pkColumnName IS NOT NULL)
    BEGIN
        -- SQL script goes here...
    END
```

## Check if record exist
``` sql
    IF EXISTS(SELECT * FROM [SystemConfigs] WHERE [Key]='Publishing.External.IrishJobs.webservice.add')
    BEGIN
    
    END
    GO
```

## Change table - add columns
``` sql
    ALTER TABLE [Tenants]
    ADD CreatedOnUtc DATETIME;
```

## Change table - add columns with defaul values
``` sql
    ALTER TABLE {TABLENAME} 
    ADD {COLUMNNAME} {TYPE} {NULL|NOT NULL} 
    CONSTRAINT {CONSTRAINT_NAME} DEFAULT {DEFAULT_VALUE}
    WITH VALUES
```

## Dynamic script execution
``` sql
    SET @newReason = 'Call'
    SET @newRelatedTo = 'customer'

    EXEC('IF NOT EXISTS(SELECT * FROM [PersonnelSignInReasons] WHERE [Reason]=''' + @newReason + ''' AND [RelatedTo]=''' + @newRelatedTo + ''')
          BEGIN
                INSERT INTO [PersonnelSignInReasons] ([Reason], [RelatedTo]) VALUES(''' + @newReason + ''', ''' + @newRelatedTo + ''')
          END'
    )
```

## Aborting/stopping script execution
``` sql
    RAISERROR('Some columns are missing from the table [PersonnelSignInReasons_new]!', 20, -1) WITH LOG 

    SET NOEXEC ON / SET NOEXEC OFF
    
    SET PARSEONLY ON / SET PARSEONLY OFF 
```

## Renaming tables/columns/constraints
``` sql
    -- Renaming table
    EXEC sp_rename 'PersonnelSignInReasons_new', 'PersonnelSignInReasons'

    -- Renaming column
    EXEC sp_rename 'Semac_Log.Type', 'Status', 'COLUMN';

    -- Renaming constraint
    EXEC sp_rename 'PK_PersonnelSignInReasons_new', 'PK_PersonnelSignInReasons', 'object'
```

## Template for each SQL script
``` sql
    BEGIN TRY
        BEGIN TRANSACTION;

        IF EXISTS(SELECT * FROM Information_Schema.Tables WHERE Table_Name='table name')
        BEGIN
            IF (COL_LENGTH('[dbo].[table name]', 'column name') IS NOT NULL) AND 
                (COL_LENGTH('[dbo].[table name]', 'column name') IS NOT NULL) AND
                (COL_LENGTH('[dbo].[table name]', 'column name') IS NULL) AND
                (COL_LENGTH('[dbo].[table name]', 'column name') IS NULL)
            BEGIN
                -- SQL script goes here... 
            END
        END;

        IF @@TRANCOUNT > 0
        BEGIN
            COMMIT TRANSACTION;
        END;
    END TRY
    BEGIN CATCH
        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRANSACTION;
        END; 
    END CATCH;
```

## Template for each SQL script (with re-throw of exception)
``` sql
    BEGIN TRY
        BEGIN TRANSACTION;

        IF EXISTS(SELECT * FROM Information_Schema.Tables WHERE Table_Name='table name')
        BEGIN
            IF (COL_LENGTH('[dbo].[table name]', 'column name') IS NOT NULL) AND 
                (COL_LENGTH('[dbo].[table name]', 'column name') IS NOT NULL) AND
                (COL_LENGTH('[dbo].[table name]', 'column name') IS NULL) AND
                (COL_LENGTH('[dbo].[table name]', 'column name') IS NULL)
            BEGIN
                -- SQL script goes here... 
            END
        END;

        IF @@TRANCOUNT > 0
        BEGIN
            COMMIT TRANSACTION;
        END;
    END TRY
    BEGIN CATCH
        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRANSACTION;
        END;
        
        DECLARE @ErrorMessage nvarchar(max), @ErrorSeverity int, @ErrorState int;
        SELECT @ErrorMessage = ERROR_MESSAGE() + ' Line ' + cast(ERROR_LINE() as nvarchar(5)), @ErrorSeverity = ERROR_SEVERITY(), @ErrorState = ERROR_STATE();
        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);          
    END CATCH;
```













