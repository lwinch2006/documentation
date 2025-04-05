# Useful SQL Server scripts

## Create table with primary key, index on it and eventual foreign key
```sql
    CREATE TABLE Shops
    (    
        Id UNIQUEIDENTIFIER NOT NULL,
        Name NVARCHAR(250) NOT NULL,
        CONSTRAINT PK_Shop PRIMARY KEY (Id)        
    );

    CREATE TABLE Products
    (
        Id UNIQUEIDENTIFIER NOT NULL,
        ShopId UNIQUEIDENTIFIER NOT NULL,
        Name NVARCHAR(250) NOT NULL,
        CONSTRAINT PK_Product PRIMARY KEY (Id),
        CONSTRAINT FK_ShopProduct FOREIGN KEY (ShopId)
        REFERENCES Shops(Id)        
    );
```

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
    ADD CreatedOnUtc DATETIME,
        UpdatedOnUtc DATETIME;
```

## Change table - add columns with defaul values
``` sql
    ALTER TABLE {TABLENAME} 
    ADD {COLUMNNAME} {TYPE} {NULL|NOT NULL} 
    CONSTRAINT {CONSTRAINT_NAME} DEFAULT {DEFAULT_VALUE}
    WITH VALUES
```

## Constraints - find default value constraint
``` sql
    SELECT [df].[name]
    FROM
        sys.default_constraints [df] INNER JOIN sys.columns [col] ON [df].[parent_object_id] = [col].[object_id]
        INNER JOIN sys.tables [t] ON [df].[parent_object_id] = [t].[object_id]
    WHERE [col].[column_id] = [df].[parent_column_id]
    AND [t].[name] = '[Table name]'
    AND [col].[name] = '[Column name]'
```

## Constraints - remove default value constraint
``` sql
    DECLARE @DFConstraintName VARCHAR(255);
    DECLARE @DropConstraintCommand VARCHAR(max);

    SELECT @DFConstraintName = [df].[name]
    FROM
        sys.default_constraints [df] INNER JOIN sys.columns [col] ON [df].[parent_object_id] = [col].[object_id]
        INNER JOIN sys.tables [t] ON [df].[parent_object_id] = [t].[object_id]
    WHERE [col].[column_id] = [df].[parent_column_id]
    AND [t].[name] = '[Table name]'
    AND [col].[name] = '[Column name]'

    SELECT @DropConstraintCommand = 'ALTER TABLE [Table name] DROP CONSTRAINT ' + @DFConstraintName;

    EXECUTE (@DropConstraintCommand);
```

## Constraints - remove unique value constraint
``` sql
    DECLARE @ConstraintName VARCHAR(255)
    DECLARE @TableName VARCHAR(15) = 'TableName'
    DECLARE @ColumnName VARCHAR(15) = 'ColumnName'
    
    SELECT @ConstraintName = [ccu].[CONSTRAINT_NAME]
    FROM [INFORMATION_SCHEMA].[CONSTRAINT_COLUMN_USAGE] [ccu] 
    INNER JOIN [INFORMATION_SCHEMA].[TABLE_CONSTRAINTS] [tc] ON [ccu].[CONSTRAINT_NAME] = [tc].[CONSTRAINT_NAME] 
    AND [ccu].[TABLE_NAME] = @TableName 
    AND [ccu].[COLUMN_NAME] = @ColumnName 
    AND [tc].[CONSTRAINT_TYPE] = 'Unique'
    
    IF @ConstraintName IS NOT NULL
    BEGIN
        DECLARE @DropConstraintCommand VARCHAR(500)
    
        SELECT @DropConstraintCommand = 'ALTER TABLE [' + @TableName + '] DROP CONSTRAINT ' + @ConstraintName;
    
        EXECUTE (@DropConstraintCommand);
    END
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

## Using cursor for loop-ing through dataset
```sql
    -- Declaring variables for for-loop
    DECLARE @ReadTenantId UNIQUEIDENTIFIER;
    DECLARE @ReadUserId UNIQUEIDENTIFIER;

    -- Declaring for-loop with base query
    DECLARE TenantUsersCursor CURSOR FOR 
        SELECT [TenantId], [UserId] 
        FROM [TenantUsers] 

    -- Starting for-loop
    OPEN TenantUsersCursor

    -- Fetching new data into defined variables
    FETCH NEXT FROM TenantUsersCursor
    INTO @ReadTenantId, @ReadUserId

    -- Loop-ing while data available
    WHILE @@FETCH_STATUS = 0
    BEGIN
        -- Doing some operation based on variables fetched 
        SELECT CONCAT([TenantId], ' - ', [UserId])
        FROM [TenantUsers]
        WHERE [TenantId] = @ReadTenantId AND [UserId] = @ReadUserId

        -- Fetching new data into defined variables
        FETCH NEXT FROM TenantUsersCursor
        INTO @ReadTenantId, @ReadUserId
    END

    -- Closing and deallocating for-loop
    CLOSE TenantUsersCursor;  
    DEALLOCATE TenantUsersCursor; 
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













