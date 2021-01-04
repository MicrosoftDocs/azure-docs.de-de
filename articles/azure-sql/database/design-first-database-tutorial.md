---
title: 'Tutorial: Entwurf Ihrer ersten relationalen Datenbank mit SSMS'
description: Hier erfahren Sie, wie Sie Ihre erste relationale Datenbank in Azure SQL-Datenbank mit SQL Server Management Studio entwerfen.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674892"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Tutorial: Entwerfen einer relationalen Datenbank in Azure SQL-Datenbank unter Verwendung von SSMS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL-Datenbank ist eine relationale DBaaS-Lösung (Database-as-a-Service) in Microsoft Cloud (Azure). In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal und [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) für folgende Zwecke verwenden:

> [!div class="checklist"]
>
> - Erstellen einer Datenbank über das Azure-Portal*
> - Einrichten einer IP-Firewallregel auf Serverebene mit dem Azure-Portal
> - Herstellen einer Verbindung für die Datenbank mit SSMS
> - Erstellen von Tabellen mit SSMS
> - Massenladen von Daten mit BCP
> - Abfragen von Daten mit SSMS

*Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

> [!TIP]
> In dem folgenden kostenlosen Microsoft Learn-Modul lernen Sie, wie Sie eine [ASP.NET-Anwendung entwickeln und konfigurieren, die eine Azure SQL-Datenbank abfragt](/learn/modules/develop-app-that-queries-azure-sql/). Sie erfahren u. a., wie Sie eine einfache Datenbank erstellen.
> [!NOTE]
> Für dieses Tutorial wird Azure SQL-Datenbank verwendet. Sie können auch eine Pooldatenbank in einem Pool für elastische Datenbanken oder eine verwaltete SQL-Instanz verwenden. Informationen zu Verbindungen mit einer verwalteten SQL-Instanz finden Sie in den folgenden Schnellstartanleitungen zu verwalteten SQL-Instanzen: [Schnellstart: Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit einer verwalteten Azure SQL-Instanz](../managed-instance/connect-vm-instance-configure.md) und [Schnellstart: Konfigurieren einer Point-to-Site-Verbindung von einem lokalen Computer mit einer verwalteten Azure SQL-Instanz](../managed-instance/point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial ausführen können, müssen folgende Komponenten installiert sein:

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (aktuelle Version)
- [BCP und sqlcmd](https://www.microsoft.com/download/details.aspx?id=36433) (aktuelle Version)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Erstellen einer leeren Datenbank in Azure SQL-Datenbank

Eine Datenbank in Azure SQL-Datenbank wird mit einer definierten Gruppe von Compute- und Speicherressourcen erstellt. Die Datenbank wird in einer [Azure-Ressourcengruppe](../../active-directory-b2c/overview.md) erstellt und mit einer [logischen SQL Server-Instanz](logical-servers.md) verwaltet.

Führen Sie die folgenden Schritte zum Erstellen einer leeren Datenbank aus.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
2. Wählen Sie auf der Seite **Neu** im Abschnitt „Azure Marketplace“ die Option **Datenbanken** aus, und klicken Sie dann im Abschnitt **Empfohlen** auf **SQL-Datenbank** .

   ![Leere Datenbank erstellen](./media/design-first-database-tutorial/create-empty-database.png)

3. Geben Sie die folgenden Informationen in das **SQL-Datenbank** -Formular ein, wie in der obigen Abbildung dargestellt:

    | Einstellung       | Vorgeschlagener Wert | BESCHREIBUNG |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Datenbankname** | *yourDatabase* | Gültige Datenbanknamen finden Sie unter [Datenbankbezeichner](/sql/relational-databases/databases/database-identifiers). |
    | **Abonnement** | *yourSubscription*  | Ausführliche Informationen zu Ihren Abonnements finden Sie unter [Abonnements](https://account.windowsazure.com/Subscriptions). |
    | **Ressourcengruppe** | *yourResourceGroup* | Gültige Ressourcengruppennamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Benennungsregeln und Einschränkungen). |
    | **Quelle auswählen** | Leere Datenbank | Gibt an, dass eine leere Datenbank erstellt werden soll. |

4. Klicken Sie auf **Server** , um einen vorhandenen Server zu verwenden oder um einen neuen Server zu erstellen und zu konfigurieren. Wählen Sie einen vorhandenen Server aus, oder klicken Sie auf **Neuen Server erstellen** , und geben Sie im Formular **Neuer Server** die folgenden Informationen ein:

    | Einstellung       | Vorgeschlagener Wert | BESCHREIBUNG |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servername** | Ein global eindeutiger Name | Gültige Servernamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Benennungsregeln und Einschränkungen). |
    | **Serveradministratoranmeldung** | Ein gültiger Name | Gültige Anmeldenamen finden Sie unter [Datenbankbezeichner](/sql/relational-databases/databases/database-identifiers). |
    | **Kennwort** | Ein gültiges Kennwort | Ihr Kennwort muss mindestens acht Zeichen umfassen und Zeichen aus drei der folgenden Kategorien enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen. |
    | **Location** | Gültiger Standort | Informationen zu Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/). |

    ![Erstellung des Datenbankservers](./media/design-first-database-tutorial/create-database-server.png)

5. Klicken Sie auf **Auswählen** .
6. Klicken Sie auf **Tarif** , um die Dienstebene, die Anzahl von DTUs oder virtuellen Kernen und die Speichermenge anzugeben. Sie können sich mit den Optionen für die Anzahl von DTUs/virtuellen Kernen sowie mit den Speicheroptionen vertraut machen, die für die einzelnen Dienstebenen verfügbar sind.

    Klicken Sie auf **Übernehmen** , wenn Sie die Dienstebene, die Anzahl von DTUs oder virtuellen Kernen und die Menge an Speicherplatz ausgewählt haben.

7. Geben Sie eine **Sortierung** für die leere Datenbank ein (verwenden Sie in diesem Tutorial den Standardwert). Weitere Informationen über Sortierungen finden Sie unter [Sortierungen](/sql/t-sql/statements/collations).

8. Nachdem Sie das **SQL-Datenbank** -Formular ausgefüllt haben, können Sie auf **Erstellen** klicken, um die Datenbank bereitzustellen. Dieser Schritt kann einige Minuten dauern.

9. Klicken Sie in der Symbolleiste auf **Benachrichtigungen** , um den Bereitstellungsprozess zu überwachen.

   ![Screenshot, in dem das Menü „Benachrichtigungen“ mit „Bereitstellung wird durchgeführt“ gezeigt ist](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Erstellen einer IP-Firewallregel auf Serverebene

Azure SQL-Datenbank erstellt eine IP-Firewall auf Serverebene. Diese Firewall verhindert, dass externe Anwendungen und Tools eine Verbindung mit dem Server und Datenbanken auf dem Server herstellen, sofern die IP-Adresse nicht durch eine Firewallregel zugelassen wird. Um externe Verbindungen mit Ihrer Datenbank zuzulassen, müssen Sie zunächst eine IP-Firewallregel für Ihre IP-Adresse (oder einen IP-Adressbereich) hinzufügen. Führen Sie die folgenden Schritte aus, um eine [IP-Firewallregel auf Serverebene](firewall-configure.md) zu erstellen.

> [!IMPORTANT]
> Azure SQL-Datenbank kommuniziert über Port 1433. Wenn Sie versuchen, mit diesem Dienst eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit der Datenbank herstellen, wenn Ihr Administrator Port 1433 öffnet.

1. Wählen Sie nach Abschluss der Bereitstellung im Menü des Azure-Portals die Option **SQL-Datenbanken** aus, oder suchen Sie auf einer beliebigen Seite nach *SQL-Datenbanken* , und wählen Sie die entsprechende Option aus.  

1. Wählen Sie auf der Seite **SQL-Datenbanken** die Datenbank *yourDatabase* aus. Daraufhin wird die Übersichtsseite für Ihre Datenbank geöffnet. Dort finden Sie unter **Servername** den vollqualifizierten Servernamen (z. B. `contosodatabaseserver01.database.windows.net`) sowie Optionen für die weitere Konfiguration.

   ![Servername](./media/design-first-database-tutorial/server-name.png)

1. Kopieren Sie diesen vollqualifizierten Servernamen, um in SQL Server Management Studio eine Verbindung mit Ihrem Server und den Datenbanken herzustellen.

1. Klicken Sie auf der Symbolleiste auf **Serverfirewall festlegen** . Die Seite **Firewalleinstellungen** für den Server wird geöffnet.

   ![IP-Firewallregel auf Serverebene](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Klicken Sie auf der Symbolleiste auf **Client-IP-Adresse hinzufügen** , um Ihre aktuelle IP-Adresse einer neuen IP-Firewallregel hinzuzufügen. Eine IP-Firewallregel kann Port 1433 für eine einzelne IP-Adresse oder einen Bereich von IP-Adressen öffnen.

1. Klicken Sie auf **Speichern** . Für Ihre aktuelle IP-Adresse wird eine IP-Firewallregel auf Serverebene erstellt, und auf dem Server wird der Port 1433 geöffnet.

1. Klicken Sie auf **OK** , und schließen Sie anschließend die Seite **Firewalleinstellungen** .

Die IP-Adresse kann nun die IP-Firewall passieren. Nun können Sie mit SQL Server Management Studio oder einem anderen Tool Ihrer Wahl die Verbindung mit der Datenbank herstellen. Verwenden Sie das Serveradministratorkonto, das Sie zuvor erstellt haben.

> [!IMPORTANT]
> Standardmäßig ist der Zugriff über die SQL-Datenbank-IP-Firewall für alle Azure-Dienste aktiviert. Klicken Sie auf dieser Seite auf **AUS** , um dies für alle Azure-Dienste zu deaktivieren.

## <a name="connect-to-the-database"></a>Herstellen der Verbindung mit der Datenbank

Verwenden Sie [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), um eine Verbindung mit Ihrer Datenbank herzustellen.

1. Öffnen Sie SQL Server Management Studio.
2. Geben Sie im Dialogfeld **Mit Server verbinden** die folgenden Informationen ein:

   | Einstellung       | Vorgeschlagener Wert | BESCHREIBUNG |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servertyp** | Datenbank-Engine | Dieser Wert ist erforderlich. |
   | **Servername** | Der vollqualifizierte Servername | Beispiel: *yourserver.database.windows.net* |
   | **Authentifizierung** | SQL Server-Authentifizierung | In diesem Tutorial haben Sie als einzigen Authentifizierungstyp die SQL-Authentifizierung konfiguriert. |
   | **Anmeldung** | Das Serveradministratorkonto | Hierbei handelt es sich um das Konto, das Sie bei der Servererstellung angegeben haben. |
   | **Kennwort** | Das Kennwort für das Serveradministratorkonto | Das Kennwort, das Sie beim Erstellen des Servers angegeben haben |

   ![Verbindung mit dem Server herstellen](./media/design-first-database-tutorial/connect.png)

3. Klicken Sie im Dialogfeld **Mit Server verbinden** auf **Optionen** . Geben Sie im Abschnitt **Verbindung mit Datenbank herstellen** den Namen *yourDatabase* ein, um eine Verbindung mit dieser Datenbank herzustellen.

    ![Herstellen einer Verbindung mit der Datenbank auf dem Server](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. Klicken Sie auf **Verbinden** . Das **Objekt-Explorer** -Fenster wird in SSMS geöffnet.

5. Erweitern Sie im **Objekt-Explorer** die Option **Datenbanken** und anschließend die Option *yourDatabase* , um die Objekte in der Beispieldatenbank anzuzeigen.

   ![Datenbankobjekte](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Erstellen von Tabellen in Ihrer Datenbank

Erstellen Sie mit [Transact-SQL-](/sql/t-sql/language-reference) ein Datenbankschema mit vier Tabellen, die ein Studentenverwaltungssystem für Universitäten modellieren:

- Person
- Course (Lehrveranstaltung)
- Student
- Quelle

Die folgende Abbildung zeigt, wie diese Tabellen miteinander verknüpft sind. Aus einigen dieser Tabellen wird auf Spalten in anderen Tabellen verwiesen. Beispielsweise wird aus der Tabelle *Student* auf die Spalte *PersonId* der Tabelle *Person* verwiesen. Sehen Sie sich die Abbildung an, um zu verstehen, wie die Tabellen in diesem Tutorial miteinander verknüpft sind. Eine ausführliche Beschreibung, wie effiziente Datenbanktabellen erstellt werden, finden Sie unter [Erstellen von effizienten Datenbanktabellen](/previous-versions/tn-archive/cc505842(v=technet.10)). Informationen zum Auswählen von Datentypen finden Sie unter [Datentypen](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Sie können auch den [Tabellen-Designer in SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) verwenden, um diese Tabellen zu entwerfen und zu erstellen.

![Tabellenbeziehungen](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. Klicken Sie mit der rechten Maustaste im **Objekt-Explorer** auf *yourDatabase* , und wählen Sie **Neue Abfrage** aus. Ein leeres Abfragefenster mit einer Verbindung mit Ihrer Datenbank wird geöffnet.

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

   ![Erstellen von Tabellen](./media/design-first-database-tutorial/create-tables.png)

3. Erweitern Sie im **Objekt-Explorer** unter *yourDatabase* den Knoten **Tabellen** , um die von Ihnen erstellten Tabellen anzuzeigen.

   ![Erstellte Tabellen in SMS](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Laden von Daten in die Tabellen

1. Erstellen Sie in Ihrem Ordner „Downloads“ einen Ordner namens *sampleData* , in dem die Beispieldaten für Ihre Datenbank gespeichert werden.

2. Klicken Sie mit der rechten Maustaste auf die folgenden Links, und speichern Sie sie im Ordner *sampleData* .

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Öffnen Sie ein Eingabeaufforderungsfenster, und navigieren Sie zum Ordner *sampleData* .

4. Führen Sie die folgenden Befehle aus, um Beispieldaten in die Tabellen einzufügen. Ersetzen Sie dabei die Werte für *server* , *database* , *user* und *password* durch die Werte für Ihre Umgebung.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Sie haben jetzt Beispieldaten in die Tabellen geladen, die Sie zuvor erstellt haben.

## <a name="query-data"></a>Daten abfragen

Führen Sie die folgenden Abfragen aus, um Informationen aus den Datenbanktabellen abzurufen. Weitere Informationen zum Schreiben von SQL-Abfragen finden Sie unter [Writing SQL Queries](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) (Schreiben von SQL-Abfragen). In der ersten Abfrage werden alle vier Tabellen verknüpft, um alle Kursteilnehmer zu finden, die von „Dominick Pope“ unterrichtet werden und ein Ergebnis (Grade) haben, das über 75 Prozent liegt. In der zweiten Abfrage werden alle vier Tabellen verknüpft und die Lehrveranstaltungen gesucht, für die „Noe Coleman“ jemals eingeschrieben war.

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

In diesem Tutorial haben Sie viele grundlegende Datenbankaufgaben kennengelernt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
>
> - Erstellen einer Datenbank über das Azure-Portal*
> - Einrichten einer IP-Firewallregel auf Serverebene mit dem Azure-Portal
> - Herstellen einer Verbindung für die Datenbank mit SSMS
> - Erstellen von Tabellen mit SSMS
> - Massenladen von Daten mit BCP
> - Abfragen von Daten mit SSMS

Im nächsten Tutorial erfahren Sie, wie Sie eine Datenbank mit Visual Studio und C# entwerfen.

> [!div class="nextstepaction"]
> [Entwerfen einer relationalen Datenbank in Azure SQL-Datenbank mit C# und ADO.NET](design-first-database-csharp-tutorial.md)