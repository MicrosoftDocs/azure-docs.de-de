---
title: Konfigurieren von Always Encrypted mithilfe des Windows-Zertifikatspeichers
description: In diesem Artikel erfahren Sie, wie Sie vertrauliche Daten in Azure SQL-Datenbank mithilfe der Datenbankverschlüsselung unter Einsatz des Always-Encrypted-Assistenten in SQL Server Management Studio (SSMS) schützen. Sie erfahren hier auch, wie Sie Ihre Verschlüsselungsschlüssel im Windows-Zertifikatspeicher speichern.
keywords: Verschlüsseln von Daten, SQL-Verschlüsselung, Datenbankverschlüsselung, vertrauliche Daten, Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 04/23/2020
ms.openlocfilehash: 60dea826a12ea475806adb6db88faa88e26463a1
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674825"
---
# <a name="configure-always-encrypted-by-using-the-windows-certificate-store"></a>Konfigurieren von Always Encrypted mithilfe des Windows-Zertifikatspeichers

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel erfahren Sie, wie Sie vertrauliche Daten in Azure SQL-Datenbank oder in einer verwalteten Azure SQL-Instanz mithilfe der Datenbankverschlüsselung unter Einsatz des [Always-Encrypted-Assistenten](/sql/relational-databases/security/encryption/always-encrypted-wizard) in [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) schützen. Sie erfahren hier auch, wie Sie Ihre Verschlüsselungsschlüssel im Windows-Zertifikatspeicher speichern.

Always Encrypted ist eine Datenverschlüsselungstechnologie, mit der vertrauliche ruhende Daten auf dem Server, auf dem Weg zwischen Client und Server und während der Verwendung geschützt werden. So wird sichergestellt, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat auftauchen. Nach der Verschlüsselung der Daten können nur Clientanwendungen oder App-Server, für die Zugriff auf die Schlüssel besteht, auf Klartextdaten zugreifen. Ausführliche Informationen finden Sie unter [Always Encrypted (Datenbank-Engine)](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

Nach dem Konfigurieren der Datenbank für die Verwendung von Always Encrypted erstellen Sie eine Clientanwendung in C# mit Visual Studio, um mit den verschlüsselten Daten zu arbeiten.

Führen Sie die Schritte in diesem Artikel aus, um zu lernen, wie Sie Always Encrypted für Azure SQL-Datenbank oder eine verwaltete Azure SQL-Instanz einrichten. In diesem Artikel erlernen Sie die Vorgehensweise für folgende Aufgaben:

* Verwenden des Always Encrypted-Assistenten in SSMS zum Erstellen von [Always Encrypted-Schlüsseln](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3).
  * Erstellen Sie einen Spaltenhauptschlüssel ( [Column Master Key, CMK](/sql/t-sql/statements/create-column-master-key-transact-sql)).
  * Erstellen Sie einen Spaltenverschlüsselungsschlüssel ( [Column Encryption Key, CEK](/sql/t-sql/statements/create-column-encryption-key-transact-sql)).
* Erstellen einer Datenbanktabelle und Verschlüsseln von Spalten.
* Erstellen einer Anwendung zum Einfügen, Auswählen und Anzeigen von Daten aus den verschlüsselten Spalten

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie diese benötigen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/)registrieren.
- Eine Datenbank in [Azure SQL-Datenbank](single-database-create-quickstart.md) oder [Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md)
* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) Version 13.0.700.242 oder höher.
* [.NET Framework 4.6](/dotnet/framework/) oder höher (auf dem Clientcomputer)
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="enable-client-application-access"></a>Aktivieren des Zugriffs für Clientanwendungen

Sie müssen Ihre Clientanwendung aktivieren, um auf Azure SQL-Datenbank oder eine verwaltete Azure SQL-Instanz zuzugreifen. Richten Sie dazu eine Azure Active Directory-Anwendung (AAD) ein, und kopieren Sie die *Anwendungs-ID* und den *Schlüssel* , die Sie zum Authentifizieren der Anwendung benötigen.

Führen Sie zum Abrufen von *Anwendungs-ID* und *Schlüssel* die Schritte unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../../active-directory/develop/howto-create-service-principal-portal.md) aus.



## <a name="connect-with-ssms"></a>Herstellen einer Verbindung mit SSMS

Öffnen Sie SQL Server Management Studio (SSMS), und stellen Sie eine Verbindung zwischen Ihrer Datenbank und dem Server oder der verwalteten Instanz her.

1. Öffnen Sie SSMS. (Klicken Sie auf **Verbinden** > **Datenbank-Engine** , um das Fenster **Mit Server verbinden** zu öffnen, falls es nicht geöffnet ist.)
2. Geben Sie Ihren Servernamen und die Anmeldeinformationen ein.

    ![Verbindungszeichenfolge kopieren](./media/always-encrypted-certificate-store-configure/ssms-connect.png)

Wenn das Fenster **Neue Firewallregel** geöffnet wird, melden Sie sich bei Azure an und lassen SSMS eine neue Firewallregel für Sie erstellen.

## <a name="create-a-table"></a>Erstellen einer Tabelle

In diesem Abschnitt erstellen Sie eine Tabelle zum Speichern von Patientendaten. Das ist zunächst eine normale Tabelle – im nächsten Abschnitt konfigurieren Sie die Verschlüsselung.

1. Erweitern Sie **Datenbanken** .
2. Klicken Sie mit der rechten Maustaste auf die Datenbank **Clinic** , und klicken Sie dann auf **Neue Abfrage** .
3. Fügen Sie den folgenden Transact-SQL-Code (T-SQL) in das Fenster mit der neuen Abfrage ein, und wählen Sie **Ausführen** .
    
    ```tsql
    CREATE TABLE [dbo].[Patients](
    [PatientId] [int] IDENTITY(1,1),
    [SSN] [char](11) NOT NULL,
    [FirstName] [nvarchar](50) NULL,
    [LastName] [nvarchar](50) NULL,
    [MiddleName] [nvarchar](50) NULL,
    [StreetAddress] [nvarchar](50) NULL,
    [City] [nvarchar](50) NULL,
    [ZipCode] [char](5) NULL,
    [State] [char](2) NULL,
    [BirthDate] [date] NOT NULL
    PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
    GO
    ```

## <a name="encrypt-columns-configure-always-encrypted"></a>Verschlüsseln von Spalten (Konfigurieren von Always Encrypted)

SSMS enthält einen Assistenten zum einfachen Konfigurieren von Always Encrypted durch Einrichten von CMK, CEK und verschlüsselten Spalten.

1. Erweitern Sie **Datenbanken** > **Clinic** > **Tabellen** sichern.
2. Klicken Sie mit der rechten Maustaste auf die Tabelle **Patients** , und wählen Sie **Spalten verschlüsseln** aus, um den Always Encrypted-Assistenten zu öffnen:

    ![Der Screenshot zeigt die Menüoption „Spalten verschlüsseln“ in der Tabelle „Patients“.](./media/always-encrypted-certificate-store-configure/encrypt-columns.png)

Der Always Encrypted-Assistent enthält die folgenden Abschnitte: **Spaltenauswahl** , **Konfiguration des Hauptschlüssels** (CMK), **Prüfung** und **Zusammenfassung** .

### <a name="column-selection"></a>Spaltenauswahl

Klicken Sie auf der Seite **Einführung** auf **Weiter** , um die Seite **Spaltenauswahl** zu öffnen. Wählen Sie auf dieser Seite die Spalten, die Sie verschlüsseln möchten, [den Typ der Verschlüsselung, und welcher Spaltenverschlüsselungsschlüssel (Column Encryption Key, CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) verwendet werden soll.

Verschlüsseln Sie für jeden Patienten die Daten **SSN** (US-Sozialversicherungsnummer) und **BirthDate** (Geburtsdatum). Für die Spalte **SSN** wird die deterministische Verschlüsselung verwendet, die Suchvorgänge nach Gleichheit, Joins und Group By unterstützt. Für die Spalte **BirthDate** wird die zufällige Verschlüsselung verwendet, bei der keine Operationen unterstützt werden.

Legen Sie als **Verschlüsselungstyp** für die Spalte **SSN** die Option **Deterministisch** und für die Spalte **BirthDate** die Option **Zufällig** fest. Klicken Sie auf **Weiter** .

![Spalten verschlüsseln](./media/always-encrypted-certificate-store-configure/column-selection.png)

### <a name="master-key-configuration"></a>Hauptschlüsselkonfiguration

Auf der Seite **Hauptschlüsselkonfiguration** richten Sie Ihren CMK ein und wählen den Schlüsselspeicheranbieter aus, unter dem der CMK gespeichert werden soll. Derzeit können Sie einen CMK im Windows-Zertifikatspeicher, im Azure-Schlüsseltresor oder in einem Hardwaresicherheitsmodul (HSM) speichern. In diesem Tutorial wird gezeigt, wie Sie Ihre Schlüssel im Windows-Zertifikatspeicher speichern.

Vergewissern Sie sich, dass **Windows-Zertifikatspeicher** ausgewählt ist, und klicken Sie auf **Weiter** .

![Hauptschlüsselkonfiguration](./media/always-encrypted-certificate-store-configure/master-key-configuration.png)

### <a name="validation"></a>Überprüfen

Sie können die Spalten jetzt verschlüsseln oder ein PowerShell-Skript für die spätere Ausführung speichern. Wählen Sie für dieses Tutorial die Option **Jetzt fertig stellen** , und klicken Sie auf **Weiter** .

### <a name="summary"></a>Zusammenfassung

Überprüfen Sie, ob alle Einstellungen richtig sind, und klicken Sie auf **Fertig stellen** , um die Einrichtung von Always Encrypted abzuschließen.

![Der Screenshot zeigt die Ergebnisseite mit Aufgaben, die als „erfolgreich“ gekennzeichnet sind.](./media/always-encrypted-certificate-store-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Überprüfen der Aktionen des Assistenten

Nach Abschluss des Assistenten ist die Datenbank für Always Encrypted eingerichtet. Der Assistent führte die folgenden Aktionen aus:

* Er erstellte eine CMK.
* Er erstellte einen CEK.
* Konfigurieren der ausgewählten Spalten für die Verschlüsselung Die Tabelle **Patients** enthält derzeit noch keine Daten, aber alle vorhandenen Daten in den ausgewählten Spalten wären jetzt verschlüsselt.

Sie können die Erstellung der Schlüssel in SSMS überprüfen, indem Sie sich die Optionen **Clinic** > **Sicherheit** > **Always Encrypted-Schlüssel** ansehen. Sie sehen die neuen Schlüssel, die vom Assistenten für Sie generiert wurden.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Erstellen einer Clientanwendung für die Verwendung der verschlüsselten Daten

Nach dem Einrichten von Always Encrypted können Sie eine Anwendung erstellen, die *Einfügevorgänge* und *Auswahlvorgänge* in den verschlüsselten Spalten durchführt. Zum erfolgreichen Ausführen der Beispielanwendung müssen Sie diese auf dem gleichen Computer ausführen, auf dem Sie den Always Encrypted-Assistenten ausgeführt haben. Zum Ausführen der Anwendung auf einem anderen Computer müssen Sie Ihre Always Encrypted-Zertifikate auf dem Computer bereitstellen, auf dem die Client-App ausgeführt wird.  

> [!IMPORTANT]
> Für Ihre Anwendung müssen [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) -Objekte verwendet werden, wenn Klartextdaten an den Server mit Always Encrypted-Spalten übergeben werden. Das Übergeben von Literalwerten ohne SqlParameter-Objekte führt zu einer Ausnahme.

1. Öffnen Sie Visual Studio, und erstellen Sie eine neue C#-Konsolenanwendung. Stellen Sie sicher, dass Ihr Projekt auf **.NET Framework 4.6** oder höher festgelegt ist.
2. Geben Sie dem Projekt den Namen **AlwaysEncryptedConsoleApp** , und klicken Sie auf **OK** .

![Der Screenshot zeigt das neu benannte Projekt „AlwaysEncryptedConsoleApp“.](./media/always-encrypted-certificate-store-configure/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändern der Verbindungszeichenfolge zur Aktivierung von Always Encrypted

In diesem Abschnitt wird beschrieben, wie Sie Always Encrypted in Ihrer Datenbank-Verbindungszeichenfolge aktivieren. Im nächsten Abschnitt – „Always Encrypted-Beispielkonsolenanwendung“ – ändern Sie die gerade erstellte Konsolen-App.

Zum Aktivieren von Always Encrypted müssen Sie der Verbindungszeichenfolge das Schlüsselwort **Column Encryption Setting** hinzufügen und es auf **Enabled** festlegen.

Sie können dies direkt in der Verbindungszeichenfolge festlegen, oder Sie können zum Festlegen ein [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder)-Element verwenden. Die Beispielanwendung im nächsten Abschnitt veranschaulicht, wie Sie das **SqlConnectionStringBuilder** -Element verwenden.

> [!NOTE]
> Dies ist die einzige Änderung, die in einer Always Encrypted-Clientanwendung erforderlich ist. Wenn Sie über eine vorhandene Anwendung verfügen, bei der die Verbindungszeichenfolge extern gespeichert wird (also in einer Konfigurationsdatei), können Sie Always Encrypted unter Umständen auch aktivieren, ohne Code zu ändern.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivieren von Always Encrypted in der Verbindungszeichenfolge

Fügen Sie der Verbindungszeichenfolge das folgende Schlüsselwort hinzu:

`Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Aktivieren von Always Encrypted per SqlConnectionStringBuilder

Der folgende Code zeigt, wie Sie Always Encrypted aktivieren, indem Sie [SqlConnectionStringBuilder.ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) auf [Enabled](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting) festlegen.

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder =
    new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting =
    SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted-Beispielkonsolenanwendung

In diesem Beispiel wird Folgendes veranschaulicht:

* Ändern der Verbindungszeichenfolge zur Aktivierung von Always Encrypted
* Einfügen von Daten in die verschlüsselten Spalten
* Auswählen eines Datensatzes per Filterung nach einem bestimmten Wert in einer verschlüsselten Spalte

Ersetzen Sie die Inhalte von **Program.cs** durch den folgenden Code. Ersetzen Sie die Verbindungszeichenfolge für die globale connectionString-Variable in der Zeile direkt über der Main-Methode durch Ihre gültige Verbindungszeichenfolge aus dem Azure-Portal. Dies ist die einzige Änderung, die Sie an diesem Code vornehmen müssen.

Führen Sie die App aus, um Always Encrypted in Aktion zu erleben.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Überprüfen, ob die Daten verschlüsselt sind

Sie können durch Abfragen der **Patients** -Daten mit SSMS schnell überprüfen, ob die auf dem Server gespeicherten Daten verschlüsselt sind. (Verwenden Sie die aktuelle Verbindung, bei der die Spaltenverschlüsselungseinstellung noch nicht aktiviert ist.)

Führen Sie die folgende Abfrage für die Clinic-Datenbank aus.

```tsql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Sie sehen, dass die verschlüsselten Spalten keine Klartextdaten enthalten.

   ![Der Screenshot zeigt verschlüsselte Daten in den verschlüsselten Spalten.](./media/always-encrypted-certificate-store-configure/ssms-encrypted.png)

Um SSMS zu verwenden, um auf die Klartextdaten zuzugreifen, können Sie der Verbindung den Parameter **Column Encryption Setting=enabled** hinzufügen.

1. Klicken Sie in SSMS im **Objekt-Explorer** mit der rechten Maustaste auf Ihren Server, und klicken Sie dann auf **Trennen** .
2. Klicken Sie auf **Verbinden** > **Datenbank-Engine** , um das Fenster **Mit Server verbinden** zu öffnen, und klicken Sie dann auf **Optionen** .
3. Klicken Sie auf **Zusätzliche Verbindungsparameter** , und geben Sie **Column Encryption Setting=enabled** ein.

    ![Der Screenshot zeigt die Registerkarte „Zusätzliche Verbindungsparameter“ mit der Eingabe „Column Encryption Setting=enabled“ im Feld.](./media/always-encrypted-certificate-store-configure/ssms-connection-parameter.png)
4. Führen Sie die folgende Abfrage für die **Clinic** -Datenbank aus.

    ```tsql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

     Die Klartextdaten werden jetzt in den verschlüsselten Spalten angezeigt.

    ![Neue Konsolenanwendung](./media/always-encrypted-certificate-store-configure/ssms-plaintext.png)

> [!NOTE]
> Wenn Sie die Verbindung mit SSMS (oder einem beliebigen Client) von einem anderen Computer aus herstellen, besteht kein Zugriff auf die Verschlüsselungsschlüssel, und die Daten können nicht entschlüsselt werden.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Erstellen einer Datenbank, für die Always Encrypted verwendet wird, sollten Sie folgende Schritte ausführen:

* Führen Sie dieses Beispiel auf einem anderen Computer aus. Es besteht kein Zugriff auf die Verschlüsselungsschlüssel und somit auch kein Zugriff auf die Klartextdaten. Die Ausführung ist also nicht erfolgreich.
* [Lassen Sie Ihre Schlüssel rotieren, und bereinigen Sie sie](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
* [Migrieren Sie Daten, die bereits mit Always Encrypted verschlüsselt sind](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).
* [Stellen Sie Always Encrypted-Zertifikate auf anderen Clientcomputern bereit](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted#Anchor_1) (siehe Abschnitt „Verfügbarmachen von Zertifikaten für Anwendungen und Benutzer“).

## <a name="related-information"></a>Verwandte Informationen

* [Always Encrypted (Cliententwicklung)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
* [Transparente Datenverschlüsselung (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [SQL Server-Verschlüsselung](/sql/relational-databases/security/encryption/sql-server-encryption)
* [Always Encrypted Wizard](/sql/relational-databases/security/encryption/always-encrypted-wizard)
* [Always Encrypted Blog](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)