---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: e30651cb0ed7d74082163a92acbc428c21018255
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66167224"
---
## <a name="c-program-example"></a>Beispiel für C#-Programm

In den nächsten Abschnitten dieses Artikels wird ein C#-Programm vorgestellt, das ADO.NET zum Senden von Transact-SQL-Anweisungen (T-SQL) an die SQL-Datenbank verwendet. Mit dem C#-Programm werden die folgenden Aktionen gezeigt:

- [Verbinden mit der SQL-Datenbank über ADO.NET](#cs_1_connect)
- [Methoden, die T-SQL-Anweisungen zurückgeben](#cs_2_return)
    - Erstellen von Tabellen.
    - Auffüllen von Tabellen mit Daten
    - Aktualisieren, Löschen und Auswählen von Daten
- [Übermitteln von T-SQL-Anweisungen an die Datenbank](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>Entitätsbeziehungsdiagramm

Die `CREATE TABLE`-Anweisungen enthalten das Schlüsselwort **REFERENCES**, um eine *Fremdschlüssel*-Beziehung (Foreign Key, FK) zwischen zwei Tabellen zu erstellen. Bei Verwendung von *tempdb* kommentieren Sie das Schlüsselwort `--REFERENCES` heraus, indem Sie zwei vorangestellte Bindestriche verwenden.

Das Entitätsbeziehungsdiagramm zeigt die Beziehung zwischen den beiden Tabellen. Die Werte in der *untergeordneten* Spalte **tabEmployee.DepartmentCode** sind auf die Werte beschränkt, die in der *übergeordneten* Spalte **tabDepartment.Department** angegeben sind.

![Entitätsbeziehungsdiagramm mit Fremdschlüssel](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> Sie können die T-SQL-Anweisungen bearbeiten, um den Tabellennamen ein `#`-Zeichen voranzustellen, damit sie in *tempdb* als temporäre Tabellen erstellt werden. Dies kann zu Demonstrationszwecken nützlich sein, wenn keine Testdatenbank verfügbar ist. Alle Verweise auf Fremdschlüssel werden bei ihrer Verwendung nicht erzwungen, und temporäre Tabellen werden automatisch gelöscht, wenn die Verbindung nach dem Abschluss der Programmausführung beendet wird.

### <a name="to-compile-and-run"></a>Kompilieren und Ausführen

Das C#-Programm ist logisch eine CS-Datei und physisch in mehrere Codeblöcke unterteilt, damit jeder Block leichter zu verstehen ist. Gehen Sie wie folgt vor, um das Programm zu kompilieren und auszuführen:

1. Erstellen Sie in Visual Studio ein C#-Projekt. Der Projekttyp sollte eine *Konsole* sein, die sich unter **Vorlagen** > **Visual C#** > **Windows Desktop** > **Konsolen-App (.NET Framework)** befindet.

1. Ersetzen Sie in der Datei *Program.cs* die Startzeilen des Codes, indem Sie die folgenden Schritte ausführen:

    1. Kopieren Sie die folgenden Codeblöcke (in derselben Reihenfolge, in der sie angezeigt werden), und fügen Sie sie ein. Siehe hierzu [Verbinden mit SQL-Datenbank](#cs_1_connect), [Generieren von T-SQL-Anweisungen](#cs_2_return) und [Übermitteln an die Datenbank](#cs_3_submit).

    1. Ändern Sie in der `Main`-Methode die folgenden Werte:

        - *cb.DataSource*
        - *cb.UserID*
        - *cb.Password*
        - *cb.InitialCatalog*

1. Stellen Sie sicher, dass auf die Assembly *System.Data.dll* verwiesen wird. Erweitern Sie für die Sicherstellung den Knoten **Verweise** im **Projektmappen-Explorer**-Bereich.

1. Wählen Sie die Schaltfläche **Start** aus, um das Programm zu erstellen und in Visual Studio auszuführen. Die Berichtsausgabe wird in einem Programmfenster angezeigt, auch wenn die GUID-Werte zwischen den Testläufen variieren.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"/>

### <a name="connect-to-sql-database-using-adonet"></a>Verbinden mit der SQL-Datenbank über ADO.NET

```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                var cb = new SqlConnectionStringBuilder();
                cb.DataSource = "your_server.database.windows.net";
                cb.UserID = "your_user";
                cb.Password = "your_password";
                cb.InitialCatalog = "your_database";

                using (var connection = new SqlConnection(cb.ConnectionString))
                {
                    connection.Open();

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
                        "@csharpParmDepartmentName", "Legal");

                    Submit_6_Tsql_SelectEmployees(connection);
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }

            Console.WriteLine("View the report output here, then press any key to end the program...");
            Console.ReadKey();
        }
```

<a name="cs_2_return"/>

### <a name="methods-that-return-t-sql-statements"></a>Methoden, die T-SQL-Anweisungen zurückgeben

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

static string Build_4_Tsql_UpdateJoin()
{
    return @"
        DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';

        -- Promote everyone in one department (see @parm...).
        UPDATE empl
        SET
            empl.EmployeeLevel += 1
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName1;
    ";
}

static string Build_5_Tsql_DeleteJoin()
{
    return @"
        DECLARE @DName2  nvarchar(128);
        SET @DName2 = @csharpParmDepartmentName;  --'Legal';

        -- Right size the Legal department.
        DELETE empl
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName2

        -- Disband the Legal department.
        DELETE tabDepartment
            WHERE DepartmentName = @DName2;
    ";
}

static string Build_6_Tsql_SelectEmployees()
{
    return @"
        -- Look at all the final Employees.
        SELECT
            empl.EmployeeGuid,
            empl.EmployeeName,
            empl.EmployeeLevel,
            empl.DepartmentCode,
            dept.DepartmentName
        FROM
            tabEmployee   as empl
        LEFT OUTER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        ORDER BY
            EmployeeName;
    ";
}
```

<a name="cs_3_submit"/>

### <a name="submit-t-sql-to-the-database"></a>Übermitteln von T-SQL-Anweisungen an die Datenbank

```csharp
static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("Now, SelectEmployees (6)...");

    string tsql = Build_6_Tsql_SelectEmployees();

    using (var command = new SqlCommand(tsql, connection))
    {
        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                    reader.GetGuid(0),
                    reader.GetString(1),
                    reader.GetInt32(2),
                    (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                    (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
            }
        }
    }
}

static void Submit_Tsql_NonQuery(
    SqlConnection connection,
    string tsqlPurpose,
    string tsqlSourceCode,
    string parameterName = null,
    string parameterValue = null
    )
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

    using (var command = new SqlCommand(tsqlSourceCode, connection))
    {
        if (parameterName != null)
        {
            command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                parameterName,
                parameterValue);
        }
        int rowsAffected = command.ExecuteNonQuery();
        Console.WriteLine(rowsAffected + " = rows affected.");
    }
}
} // EndOfClass
}
```