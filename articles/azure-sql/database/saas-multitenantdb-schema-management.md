---
title: Verwalten des Schemas in einer mehrinstanzenfähigen App
description: Verwalten des Schemas für mehrere Mandanten in einer mehrinstanzenfähigen Anwendung, die Azure SQL-Datenbank verwendet
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011852"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>Verwalten von Schemas in einer SaaS-Anwendung, die mehrinstanzenfähige Datenbanken mit Sharding verwendet
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial werden die Herausforderungen bei der Verwaltung einer großen Anzahl von Datenbanken in einer SaaS-Anwendung (Software-as-a-Service) betrachtet. Für das Auffächern von Schemaänderungen über alle Datenbanken hinweg werden Lösungen gezeigt.

Wie jede andere Anwendung wird die Wingtip Tickets-SaaS-App laufend weiterentwickelt und macht dadurch Änderungen an der Datenbank erforderlich. Änderungen wirken sich möglicherweise auf das Schema oder Verweisdaten aus oder gehen mit Aufgaben zur Datenbankwartung einher. Bei einer SaaS-Anwendung, die das Muster mit einer Datenbank pro Mandant verwendet, müssen die Änderungen übergreifend für eine potenziell sehr große Anzahl von Mandantendatenbanken koordiniert werden. Darüber hinaus müssen Sie diese Änderungen in den Datenbankbereitstellungsprozess einbinden, um sicherzustellen, dass die Änderungen bei der Erstellung auch in neue Datenbanken einfließen.

#### <a name="two-scenarios"></a>Zwei Szenarien

In diesem Tutorial werden die folgenden zwei Szenarien untersucht:
- Bereitstellen der Aktualisierungen von Verweisdaten für alle Mandanten
- Neuerstellen eines Index für die Tabelle mit den Verweisdaten

Für die mandantendatenbankübergreifende Ausführung dieser Vorgänge wird das Feature [Elastische Aufträge](./elastic-jobs-overview.md) von Azure SQL-Datenbank verwendet. Die Aufträge gelten auch für die als Vorlage verwendete Mandantendatenbank. Bei der Wingtip Tickets-Beispielanwendung wird diese Vorlagendatenbank kopiert, um eine neue Mandantendatenbank bereitzustellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Auftrags-Agents
> * Ausführen einer T-SQL-Abfrage in mehreren Mandantendatenbanken
> * Aktualisieren von Verweisdaten in allen Mandantendatenbanken
> * Erstellen eines Index für eine Tabelle in allen Mandantendatenbanken

## <a name="prerequisites"></a>Voraussetzungen

- Die mehrinstanzenfähige Datenbankanwendung Wingtip Tickets muss bereitgestellt sein:
    - Anleitungen hierzu finden Sie im ersten Tutorial, in dem die mehrinstanzenfähige SaaS-Datenbankanwendung Wingtip Tickets vorgestellt wird:<br />[Bereitstellen und Kennenlernen einer mehrinstanzenfähigen Anwendung mit Sharding, die Azure SQL-Datenbank verwendet](./saas-multitenantdb-get-started-deploy.md)
        - Der Bereitstellungsprozess dauert weniger als fünf Minuten.
    - Sie müssen die *mehrinstanzenfähige Wingtip-Version mit Sharding* installiert haben. Die Versionen *Eigenständig* und *Datenbank pro Mandant* unterstützen das vorliegende Tutorial nicht.

- Die aktuelle Version von SQL Server Management Studio (SSMS) muss installiert sein. [Laden Sie SSMS herunter, und installieren Sie es](/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell muss installiert sein. Ausführliche Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

> [!NOTE]
> In diesem Tutorial werden Funktionen des Azure SQL-Datenbank-Diensts verwendet, die als eingeschränkte Vorschauversion vorliegen ([Aufträge für die elastische Datenbank](elastic-database-client-library.md)). Wenn Sie dieses Tutorial durcharbeiten möchten, geben Sie Ihre Abonnement-ID per E-Mail an *SaaSFeedback\@microsoft.com* mit dem Betreff „Elastic Jobs Preview“ an. Wenn Sie die Bestätigung erhalten haben, dass die Aktivierung für Ihr Abonnement ausgeführt wurde, [laden Sie die aktuellen Vorabversion-Cmdlets für Aufträge herunter und installieren Sie sie](https://github.com/jaredmoo/azure-powershell/releases). Die Vorschauversion ist eingeschränkt. Wenden Sie sich daher an *SaaSFeedback\@microsoft.com*, wenn Sie Fragen haben oder Support benötigen.

## <a name="introduction-to-saas-schema-management-patterns"></a>Einführung in SaaS-Schemaverwaltungsmuster

Das in diesem Beispiel verwendete mehrinstanzenfähige Datenbankmodell mit Sharding ermöglicht, dass eine Mandantendatenbank einen oder mehrere Mandanten enthält. In diesem Beispiel wird untersucht, wie Sie eine Mischung aus einer Datenbank mit mehreren Mandanten und einer Datenbank mit nur einem Mandanten und damit ein *hybrides* Mandantenverwaltungsmodell verwenden können. Das Verwalten von Änderungen an diesen Datenbanken kann kompliziert sein. [Elastische Aufträge](./elastic-jobs-overview.md) erleichtern die Verwaltung einer großen Anzahl von Datenbanken. Aufträge ermöglichen es Ihnen, auf sichere und zuverlässige Weise Transact-SQL-Skripts als Aufgaben für eine Gruppe von Mandantendatenbanken auszuführen. Die Aufgaben sind unabhängig von Benutzerinteraktionen oder Eingaben. Mithilfe dieser Methode können Änderungen an Schemadaten oder an gemeinsamen Verweisdaten für alle Mandanten in einer Anwendung bereitgestellt werden. Elastische Aufträge können auch verwendet werden, um eine Gold-Version der Datenbank als Vorlage zu verwalten. Diese Vorlage dient zum Erstellen neuer Mandanten, wodurch immer sichergestellt wird, das die neuesten Schema- und Verweisdaten verwendet werden.

![Bildschirm](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Eingeschränkte Vorschau – Elastische Aufträge

Es gibt eine neue Version von „Elastische Aufträge“, die nun eine integrierte Funktion von Azure SQL-Datenbank darstellt. Diese neue Version von Elastische Aufträge liegt derzeit als eingeschränkte Vorschauversion vor. Die eingeschränkte Vorschauversion unterstützt derzeit die Verwendung von PowerShell zum Erstellen eines Auftrags-Agents sowie T-SQL zum Erstellen und Verwalten von Aufträgen.
> [!NOTE]
> In diesem Tutorial werden Funktionen des SQL-Datenbank-Diensts verwendet, die als eingeschränkte Vorschauversion vorliegen (Aufträge für die elastische Datenbank). Wenn Sie dieses Tutorial durcharbeiten möchten, geben Sie Ihre Abonnement-ID per E-Mail an SaaSFeedback@microsoft.com mit dem Betreff „Vorschau von Elastische Aufträge“ an. Wenn das Aktivieren Ihres Abonnements bestätigt wurde, laden Sie die aktuelle Vorabversion der Job-Cmdlets herunter, und installieren Sie diese. Die Vorschauversion ist eingeschränkt, wenden Sie sich daher an SaaSFeedback@microsoft.com, wenn Sie Fragen haben oder Support benötigen.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Abrufen von Quellcode und Skripts zur Anwendung Wingtip Tickets SaaS Multi-tenant Database

Die Skripts und der Quellcode der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbank stehen im Repository [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) auf GitHub zur Verfügung. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-SaaS-Skripts finden Sie unter [General guidance for working with Wingtip Tickets sample SaaS apps](saas-tenancy-wingtip-app-guidance-tips.md) (Allgemeine Hinweise zur Verwendung von Wingtip Tickets-Beispiel-SaaS-Apps).

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Erstellen einer Auftrags-Agent-Datenbank und eines neuen Auftrags-Agents

Für dieses Tutorial müssen Sie mit PowerShell die Auftrags-Agent-Datenbank und den Auftrags-Agent erstellen. In gleicher Weise, wie SQL-Agent eine MSDB-Datenbank verwendet, nutzt ein Auftrags-Agent eine Datenbank in Azure SQL-Datenbank, um Auftragsdefinitionen, Auftragsstatus und den Verlauf zu speichern. Sobald der Auftrags-Agent erstellt wurde, können Sie sofort Aufträge erstellen und überwachen.

1. Öffnen Sie *…\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1* in der **PowerShell ISE**.
2. Drücken Sie **F5** , um das Skript auszuführen.

Das Skript *Demo-SchemaManagement.ps1* ruft das Skript *Deploy-SchemaManagement.ps1* auf, um eine Datenbank namens _jobagent_ auf dem Katalogserver zu erstellen. Das Skript erstellt dann den Auftrags-Agent, und übergibt die Datenbank _jobagent_ als Parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Erstellen eines Auftrags, um neue Verweisdaten für alle Mandanten bereitzustellen

#### <a name="prepare"></a>Vorbereiten

Jede Mandantendatenbank enthält in der Tabelle **VenueTypes** eine Gruppe von Veranstaltungsorttypen. Jeder Typ definiert eine bestimmte Art von Ereignissen, die an einem Veranstaltungsort präsentiert werden können. Diese Veranstaltungsorttypen entsprechen den Hintergrundbildern, die in der Mandantenereignis-App angezeigt werden.  In dieser Übung stellen Sie eine Aktualisierung für alle Datenbanken bereit, wobei zwei weitere Veranstaltungsorttypen hinzugefügt werden: *Motorcycle Racing* und *Swimming Club*.

Prüfen Sie zunächst die in jeder Mandantendatenbank enthaltenen Veranstaltungsorttypen. Stellen Sie in SQL Server Management Studio (SSMS) eine Verbindung mit einer der Mandantendatenbanken her, und überprüfen Sie die Tabelle VenueTypes.  Sie können diese Tabelle auch im Azure-Portal im Abfrage-Editor abfragen, den Sie über die Seite „Datenbank“ aufrufen können.

1. Öffnen Sie SSMS, und stellen Sie eine Verbindung mit dem Mandantenserver *tenants1-dpt-&lt;Benutzer&gt;.database.windows.net* her.
1. Um sich zu vergewissern, dass *Motorcycle Racing* und *Swimming Club* **nicht** in der Ergebnisliste enthalten sind, navigieren Sie zur Datenbank *contosoconcerthall* auf dem Server *tenants1-dpt-&lt;Benutzer&gt;* , und fragen Sie die Tabelle *VenueTypes* ab.



#### <a name="steps"></a>Schritte

Nun erstellen Sie einen Auftrag zum Aktualisieren der Tabelle **VenueTypes** (Veranstaltungsorttypen) in jeder Mandantendatenbank durch Hinzufügen der beiden neuen Veranstaltungsorttypen.

Zum Erstellen eines neuen Auftrags verwenden Sie die Gruppe der im System gespeicherten Auftragsprozeduren, die in der Datenbank _jobagent_ erstellt wurden. Die gespeicherten Prozeduren wurden beim Erstellen des Auftrags-Agents erstellt.

1. Stellen Sie in SSMS eine Verbindung mit dem Mandantenserver her: tenants1-mt-&lt;Benutzer&gt;.database.windows.net

2. Navigieren Sie zur Datenbank *tenants1*.

3. Fragen Sie die Tabelle *VenueTypes* ab, um sich zu überzeugen, dass *Motorcycle Racing* und *Swimming Club* noch nicht in der Ergebnisliste erscheinen.

4. Stellen Sie eine Verbindung mit dem Katalogserver her. Dieser lautet *catalog-mt-&lt;Benutzer&gt;.database.windows.net*.

5. Stellen Sie eine Verbindung zur Datenbank _jobagent_  auf dem Katalogserver her.

6. Öffnen Sie in SSMS die Datei *…\\Learning Modules\\Schema Management\\DeployReferenceData.sql*.

7. Ändern Sie die Anweisung „set @User = &lt;Benutzer&gt;“, und ersetzen Sie den Wert „Benutzer“ durch den Benutzer, der beim Bereitstellen der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbankanwendung verwendet wurde.

8. Drücken Sie **F5** , um das Skript auszuführen.

#### <a name="observe"></a>Beobachen

Beachten Sie die folgenden Elemente im Skript *DeployReferenceData.sql*:

- **sp\_add\_target\_group** erstellt den Zielgruppennamen *DemoServerGroup* und fügt Zielmember zur Gruppe hinzu.

- **sp\_add\_target\_group\_member** fügt die folgenden Elemente hinzu:
    - Einen Zielmembertyp *server*
        - Dies ist der Server *tenants1-mt-&lt;Benutzer&gt;* , der die Mandantendatenbanken enthält.
        - Durch das Einschließen des Servers werden auch die Mandantendatenbanken einbezogen, die zum Zeitpunkt der Auftragsausführung vorhanden sind.
    - Einen Zielmembertyp *database* für die Vorlagedatenbank (*basetenantdb*), die sich auf dem Server *catalog-mt-&lt;Benutzer&gt;* befindet
    - Einen Zielmembertyp *database*, der die in einem späteren Tutorial verwendete Datenbank *adhocreporting* enthält

- **sp\_add\_job** erstellt einen Auftrag mit dem Namen *Reference Data Deployment* (Verweisdatenbereitstellung).

- **sp\_add\_jobstep** erstellt den Auftragsschritt mit dem T-SQL-Befehlstext zum Aktualisieren der Verweistabelle „VenueTypes“.

- Die übrigen Ansichten im Skript zeigen das Vorhandensein der Objekte an und überwachen die Auftragsausführung. Verwenden Sie diese Abfragen, um den Statuswert in der Spalte **lifecycle** zu überprüfen und zu ermitteln, wann der Auftrag abgeschlossen wurde. Der Auftrag aktualisiert die Mandantendatenbank und die zwei weiteren Datenbanken mit der Verweistabelle.

Wechseln Sie in SSMS zur Mandantendatenbank auf dem Server *tenants1-mt-&lt;Benutzer&gt;* . Fragen Sie die Tabelle *VenueTypes* ab, um sich zu überzeugen, dass *Motorcycle Racing* und *Swimming Club* jetzt der Tabelle hinzugefügt wurden. Die Gesamtzahl der Veranstaltungsorttypen sollte um zwei gestiegen sein.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Erstellen eines Auftrags zum Verwalten des Index der Verweistabelle

In dieser Übung wird ein Auftrag erstellt, um den Index für den Primärschlüssel der Verweistabelle neu zu erstellen. Eine Indexneuerstellung ist ein typischer Datenbankverwaltungsvorgang, den Administratoren nach dem Laden großer Datenmengen ausführen, um die Leistung zu verbessern.

1. Stellen Sie in SSMS eine Verbindung zur Datenbank _jobagent_ auf dem Server *catalog-mt-&lt;Benutzer&gt;.database.windows.net* her.

2. Öffnen Sie die Datei *…\\Learning Modules\\Schema Management\\OnlineReindex.sql* in SSMS.

3. Drücken Sie **F5** , um das Skript auszuführen.

#### <a name="observe"></a>Beobachen

Beachten Sie folgende Elemente im Skript *OnlineReindex.sql*:

* **sp\_add\_job** erstellt einen neuen Auftrag mit dem Namen *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_add\_jobstep** erstellt den Auftragsschritt mit dem T-SQL-Befehlstext zum Aktualisieren des Index.

* Die verbleibenden Ansichten im Skript überwachen die Auftragsausführung. Verwenden Sie diese Abfragen, um den Statuswert in der **lifecycle**-Spalte zu überprüfen und zu ermitteln, wann der Auftrag für alle Zielgruppenelemente erfolgreich abgeschlossen wurde.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Verwalten horizontal hochskalierter Clouddatenbanken](./elastic-jobs-overview.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Auftrags-Agents zum datenbankübergreifenden Ausführen von T-SQL-Aufträgen
> * Aktualisieren von Verweisdaten in allen Mandantendatenbanken
> * Erstellen eines Index für eine Tabelle in allen Mandantendatenbanken

Absolvieren Sie als Nächstes das Tutorial [Ad-hoc-Berichterstellung](./saas-multitenantdb-adhoc-reporting.md), um das Ausführen verteilter Abfragen über Mandantendatenbanken hinweg zu untersuchen.