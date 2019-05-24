---
title: 'Tutorial: Entwerfen Ihrer ersten relationalen Datenbank in Azure SQL-Datenbank mit SSMS | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Ihre erste relationale Datenbank in einer Einzeldatenbank in Azure SQL-Datenbank mit SQL Server Management Studio entwerfen.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: a13d1f843604025ee0f843c0770b3d11b53dd837
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762881"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Tutorial: Entwerfen einer relationalen Datenbank in einer Einzeldatenbank in Azure SQL-Datenbank mit SSMS

Azure SQL-Datenbank ist eine relationale DBaaS-Lösung (Database-as-a-Service) in Microsoft Cloud (Azure). In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal und [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) für folgende Zwecke verwenden:

> [!div class="checklist"]
> - Erstellen einer Einzeldatenbank über das Azure-Portal*
> - Einrichten einer IP-Firewallregel auf Serverebene mit dem Azure-Portal
> - Herstellen einer Verbindung für die Datenbank mit SSMS
> - Erstellen von Tabellen mit SSMS
> - Massenladen von Daten mit BCP
> - Abfragen von Daten mit SSMS

*Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

> [!NOTE]
> Für dieses Tutorial nutzen wir eine Einzeldatenbank. Sie können auch eine in einem Pool zusammengefasste Datenbank in einem Pool für elastische Datenbanken oder eine Instanzdatenbank in einer verwalteten Instanz verwenden. Informationen zu Verbindungen mit einer verwalteten Instanz finden Sie in den folgenden Schnellstartanleitungen zu verwalteten Instanzen: [Schnellstart: Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-configure-vm.md) und [Schnellstart: Konfigurieren einer Point-to-Site-Verbindung von einem lokalen Computer mit einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial ausführen können, müssen folgende Komponenten installiert sein:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (aktuelle Version)
- [BCP und sqlcmd](https://www.microsoft.com/download/details.aspx?id=36433) (aktuelle Version)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-blank-single-database"></a>Erstellen einer leeren Einzeldatenbank

Eine Einzeldatenbank in Azure SQL-Datenbank wird mit einer definierten Gruppe von Compute- und Speicherressourcen erstellt. Die Datenbank wird in einer [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md) erstellt und mit einem [Datenbankserver](sql-database-servers.md) verwaltet.

Führen Sie die folgenden Schritte aus, um eine leere Einzeldatenbank zu erstellen:

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.
2. Wählen Sie auf der Seite **Neu** im Abschnitt „Azure Marketplace“ die Option **Datenbanken** aus, und klicken Sie dann im Abschnitt **Empfohlen** auf **SQL-Datenbank**.

   ![Leere Datenbank erstellen](./media/sql-database-design-first-database/create-empty-database.png)

3. Geben Sie die folgenden Informationen in das **SQL-Datenbank**-Formular ein, wie in der obigen Abbildung dargestellt:

    | Einstellung       | Empfohlener Wert | BESCHREIBUNG |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Datenbankname** | *yourDatabase* | Gültige Datenbanknamen finden Sie unter [Datenbankbezeichner](/sql/relational-databases/databases/database-identifiers). |
    | **Abonnement** | *yourSubscription*  | Ausführliche Informationen zu Ihren Abonnements finden Sie unter [Abonnements](https://account.windowsazure.com/Subscriptions). |
    | **Ressourcengruppe** | *yourResourceGroup* | Gültige Ressourcengruppennamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Benennungsregeln und Einschränkungen). |
    | **Quelle auswählen** | Leere Datenbank | Gibt an, dass eine leere Datenbank erstellt werden soll. |

4. Klicken Sie auf **Server**, um einen vorhandenen Datenbankserver zu verwenden oder um einen neuen Datenbankserver zu erstellen und zu konfigurieren. Wählen Sie einen vorhandenen Server aus, oder klicken Sie auf **Neuen Server erstellen**, und geben Sie im Formular **Neuer Server** die folgenden Informationen ein:

    | Einstellung       | Empfohlener Wert | BESCHREIBUNG |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servername** | Ein global eindeutiger Name | Gültige Servernamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Benennungsregeln und Einschränkungen). |
    | **Serveradministratoranmeldung** | Ein gültiger Name | Gültige Anmeldenamen finden Sie unter [Datenbankbezeichner](/sql/relational-databases/databases/database-identifiers). |
    | **Kennwort** | Ein gültiges Kennwort | Ihr Kennwort muss mindestens acht Zeichen umfassen und Zeichen aus drei der folgenden Kategorien enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen. |
    | **Location** | Gültiger Standort | Informationen zu Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/). |

    ![Erstellung des Datenbankservers](./media/sql-database-design-first-database/create-database-server.png)

5. Klicken Sie auf **Auswählen**.
6. Klicken Sie auf **Tarif**, um die Dienstebene, die Anzahl von DTUs oder virtuellen Kernen und die Speichermenge anzugeben. Sie können sich mit den Optionen für die Anzahl von DTUs/virtuellen Kernen sowie mit den Speicheroptionen vertraut machen, die für die einzelnen Dienstebenen verfügbar sind.

    Klicken Sie auf **Übernehmen**, wenn Sie die Dienstebene, die Anzahl von DTUs oder virtuellen Kernen und die Menge an Speicherplatz ausgewählt haben.

7. Geben Sie eine **Sortierung** für die leere Datenbank ein (verwenden Sie in diesem Tutorial den Standardwert). Weitere Informationen über Sortierungen finden Sie unter [Sortierungen](/sql/t-sql/statements/collations).

8. Nachdem Sie das Formular **SQL-Datenbank** ausgefüllt haben, können Sie auf **Erstellen** klicken, um die Einzeldatenbank bereitzustellen. Dieser Schritt kann einige Minuten dauern.

9. Klicken Sie in der Symbolleiste auf **Benachrichtigungen**, um den Bereitstellungsprozess zu überwachen.

   ![Benachrichtigung](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Erstellen einer IP-Firewallregel auf Serverebene

Der SQL-Datenbank-Dienst erstellt eine IP-Firewall auf Serverebene. Diese Firewall verhindert, dass externe Anwendungen und Tools eine Verbindung mit dem Server und Datenbanken auf dem Server herstellen, sofern die IP-Adresse nicht durch eine Firewallregel zugelassen wird. Um externe Verbindungen mit Ihrer Einzeldatenbank zuzulassen, müssen Sie zunächst eine IP-Firewallregel für Ihre IP-Adresse (oder einen IP-Adressbereich) hinzufügen. Führen Sie die folgenden Schritte aus, um eine [SQL-Datenbank-IP-Firewallregel auf Serverebene](sql-database-firewall-configure.md) zu erstellen.

> [!IMPORTANT]
> Der SQL-Datenbank-Dienst kommuniziert über Port 1433. Wenn Sie versuchen, mit diesem Dienst eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit der Einzeldatenbank herstellen, wenn Ihr Administrator Port 1433 öffnet.

1. Klicken Sie nach Abschluss der Bereitstellung im Menü auf der linken Seite auf **SQL-Datenbanken**, und klicken Sie dann auf der Seite **SQL-Datenbanken** auf *yourDatabase*. Die Übersichtsseite für Ihre Datenbank wird geöffnet, die den vollqualifizierten **Servernamen** (z.B. *yourserver.database.windows.net*) und Optionen für die weitere Konfiguration enthält.

2. Kopieren Sie diesen vollqualifizierten Servernamen, um in SQL Server Management Studio eine Verbindung mit Ihrem Server und den Datenbanken herzustellen.

   ![Servername](./media/sql-database-design-first-database/server-name.png)

3. Klicken Sie auf der Symbolleiste auf **Serverfirewall festlegen**. Die Seite **Firewalleinstellungen** für den SQL-Datenbank-Server wird geöffnet.

   ![IP-Firewallregel auf Serverebene](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Klicken Sie auf der Symbolleiste auf **Client-IP-Adresse hinzufügen**, um Ihre aktuelle IP-Adresse einer neuen IP-Firewallregel hinzuzufügen. Eine IP-Firewallregel kann Port 1433 für eine einzelne IP-Adresse oder einen Bereich von IP-Adressen öffnen.

5. Klicken Sie auf **Speichern**. Für Ihre aktuelle IP-Adresse wird eine IP-Firewallregel auf Serverebene erstellt, und auf dem SQL-Datenbank-Server wird der Port 1433 geöffnet.

6. Klicken Sie auf **OK**, und schließen Sie anschließend die Seite **Firewalleinstellungen**.

Die IP-Adresse kann nun die IP-Firewall passieren. Nun können Sie mit SQL Server Management Studio oder einem anderen Tool Ihrer Wahl die Verbindung mit der Einzeldatenbank herstellen. Verwenden Sie das Serveradministratorkonto, das Sie zuvor erstellt haben.

> [!IMPORTANT]
> Standardmäßig ist der Zugriff über die SQL-Datenbank-IP-Firewall für alle Azure-Dienste aktiviert. Klicken Sie auf dieser Seite auf **AUS**, um dies für alle Azure-Dienste zu deaktivieren.

## <a name="connect-to-the-database"></a>Herstellen einer Verbindung mit der Datenbank

Verwenden Sie [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), um eine Verbindung mit Ihrer Einzeldatenbank herzustellen.

1. Öffnen Sie SQL Server Management Studio.
2. Geben Sie im Dialogfeld **Mit Server verbinden** die folgenden Informationen ein:

   | Einstellung       | Empfohlener Wert | BESCHREIBUNG |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servertyp** | Datenbank-Engine | Dieser Wert ist erforderlich. |
   | **Servername** | Der vollqualifizierte Servername | Beispiel: *yourserver.database.windows.net* |
   | **Authentifizierung** | SQL Server-Authentifizierung | In diesem Tutorial haben Sie als einzigen Authentifizierungstyp die SQL-Authentifizierung konfiguriert. |
   | **Anmeldung** | Das Serveradministratorkonto | Hierbei handelt es sich um das Konto, das Sie bei der Servererstellung angegeben haben. |
   | **Kennwort** | Das Kennwort für das Serveradministratorkonto | Das Kennwort, das Sie beim Erstellen des Servers angegeben haben |

   ![Verbindung mit dem Server herstellen](./media/sql-database-design-first-database/connect.png)

3. Klicken Sie im Dialogfeld **Mit Server verbinden** auf **Optionen**. Geben Sie im Abschnitt **Verbindung mit Datenbank herstellen** den Namen *yourDatabase* ein, um eine Verbindung mit dieser Datenbank herzustellen.

    ![Herstellen einer Verbindung mit der Datenbank auf dem Server](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Klicken Sie auf **Verbinden**. Das **Objekt-Explorer**-Fenster wird in SSMS geöffnet.

5. Erweitern Sie im **Objekt-Explorer** die Option **Datenbanken** und anschließend die Option *yourDatabase*, um die Objekte in der Beispieldatenbank anzuzeigen.

   ![Datenbankobjekte](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Erstellen von Tabellen in Ihrer Datenbank

Erstellen Sie mit [Transact-SQL-](/sql/t-sql/language-reference) ein Datenbankschema mit vier Tabellen, die ein Studentenverwaltungssystem für Universitäten modellieren:

- Person
- Course (Lehrveranstaltung)
- Student
- Quelle

Die folgende Abbildung zeigt, wie diese Tabellen miteinander verknüpft sind. Aus einigen dieser Tabellen wird auf Spalten in anderen Tabellen verwiesen. Beispielsweise wird aus der Tabelle *Student* auf die Spalte *PersonId* der Tabelle *Person* verwiesen. Sehen Sie sich die Abbildung an, um zu verstehen, wie die Tabellen in diesem Tutorial miteinander verknüpft sind. Eine ausführliche Beschreibung, wie effiziente Datenbanktabellen erstellt werden, finden Sie unter [Erstellen von effizienten Datenbanktabellen](https://msdn.microsoft.com/library/cc505842.aspx). Informationen zum Auswählen von Datentypen finden Sie unter [Datentypen](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Sie können auch den [Tabellen-Designer in SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) verwenden, um diese Tabellen zu entwerfen und zu erstellen.

![Tabellenbeziehungen](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Klicken Sie mit der rechten Maustaste im **Objekt-Explorer** auf *yourDatabase*, und wählen Sie **Neue Abfrage** aus. Ein leeres Abfragefenster mit einer Verbindung mit Ihrer Datenbank wird geöffnet.

2. Führen Sie im Abfragefenster die folgende Abfrage aus, um vier Tabellen in der Datenbank zu erstellen:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Erstellen von Tabellen.](./media/sql-database-design-first-database/create-tables.png)

3. Erweitern Sie im **Objekt-Explorer** unter *yourDatabase* den Knoten **Tabellen**, um die von Ihnen erstellten Tabellen anzuzeigen.

   ![Erstellte Tabellen in SMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Laden von Daten in die Tabellen

1. Erstellen Sie in Ihrem Ordner „Downloads“ einen Ordner namens *sampleData*, in dem die Beispieldaten für Ihre Datenbank gespeichert werden.

2. Klicken Sie mit der rechten Maustaste auf die folgenden Links, und speichern Sie sie im Ordner *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Öffnen Sie ein Eingabeaufforderungsfenster, und navigieren Sie zum Ordner *sampleData*.

4. Führen Sie die folgenden Befehle aus, um Beispieldaten in die Tabellen einzufügen. Ersetzen Sie dabei die Werte für *server*, *database*, *user* und *password* durch die Werte für Ihre Umgebung.

   ```cmd
   bcp Course in SampleCourseData.txt -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData.txt -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData.txt -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData.txt -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Sie haben jetzt Beispieldaten in die Tabellen geladen, die Sie zuvor erstellt haben.

## <a name="query-data"></a>Abfragen von Daten

Führen Sie die folgenden Abfragen aus, um Informationen aus den Datenbanktabellen abzurufen. Weitere Informationen zum Schreiben von SQL-Abfragen finden Sie unter [Writing SQL Queries](https://technet.microsoft.com/library/bb264565.aspx) (Schreiben von SQL-Abfragen). In der ersten Abfrage werden alle vier Tabellen verknüpft, um alle Kursteilnehmer zu finden, die von „Dominick Pope“ unterrichtet werden und ein Ergebnis (Grade) haben, das über 75 Prozent liegt. In der zweiten Abfrage werden alle vier Tabellen verknüpft und die Lehrveranstaltungen gesucht, für die „Noe Coleman“ jemals eingeschrieben war.

1. Führen Sie in einem Abfragefenster von SQL Server Management Studio die folgende Abfrage aus:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. Führen Sie in einem Abfragefenster die folgende Abfrage aus:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie viele grundlegende Datenbankaufgaben kennengelernt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> - Erstellen einer Einzeldatenbank
> - Einrichten einer IP-Firewallregel auf Serverebene
> - Herstellen einer Verbindung mit der Datenbank mit [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)
> - Erstellen von Tabellen.
> - Massenladen von Daten
> - Abfragen der Daten

Im nächsten Tutorial erfahren Sie, wie Sie eine Datenbank mit Visual Studio und C# entwerfen.

> [!div class="nextstepaction"]
> [Entwerfen einer relationalen Datenbank in einer Einzeldatenbank in Azure SQL-Datenbank mit C# und ADO.NET](sql-database-design-first-database-csharp.md)
