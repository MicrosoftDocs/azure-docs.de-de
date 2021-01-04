---
title: Verwalten des Schemas in einer Einzelmandanten-App
description: Verwalten des Schemas für mehrere Mandanten in einer App mit nur einem Mandanten, die Azure SQL-Datenbank verwendet
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: e4328be0aade0658dedb034dbbb6980b810f771a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793193"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Verwalten von Schemas in einer SaaS-Anwendung mit dem Muster für eine Datenbank pro Mandant in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Wenn eine Datenbankanwendung weiterentwickelt wird, müssen unweigerlich Änderungen am Datenbankschema oder den Verweisdaten vorgenommen werden.  Auch Datenbankwartungsaufgaben sind in regelmäßigen Abständen erforderlich. Um eine Anwendung, die das Muster für eine Datenbank pro Mandant verwendet, zu verwalten, müssen Sie diese Änderungen oder Wartungsaufgaben für eine ganze Reihe von Mandantendatenbanken übernehmen.

In diesem Tutorial werden zwei Szenarien erläutert: das Bereitstellen der Aktualisierungen von Verweisdaten für alle Mandanten und das erneute Erstellen eines Index für die Tabelle mit den Verweisdaten. Das Feature [Elastische Aufträge](./elastic-jobs-overview.md) wird zum Ausführen dieser Aktionen für alle Mandantendatenbanken sowie für die Vorlagendatenbank verwendet, die zum Erstellen neuer Mandantendatenbanken zur Anwendung kommt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> 
> * Erstellen eines Auftrags-Agents
> * Umsetzen der Ausführung von T-SQL-Aufträgen für alle Mandantendatenbanken
> * Aktualisieren von Verweisdaten in allen Mandantendatenbanken
> * Erstellen eines Index für eine Tabelle in allen Mandantendatenbanken


Stellen Sie vor dem Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die SaaS-App Wingtip Tickets mit einer Datenbank pro Mandant ist bereitgestellt. Unter [Bereitstellen und Kennenlernen der Wingtip Tickets SaaS-App mit einer Datenbank pro Mandant](./saas-dbpertenant-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.
* Azure PowerShell wurde installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).
* Die aktuelle Version von SQL Server Management Studio (SSMS) wurde installiert. [Herunterladen und Installieren von SSMS](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="introduction-to-saas-schema-management-patterns"></a>Einführung in SaaS-Schemaverwaltungsmuster

Das Muster für eine Datenbank pro Mandant isoliert Mandantendaten effektiv, erhöht jedoch die Anzahl der Datenbanken, die verwaltet werden müssen. [Elastische Aufträge](./elastic-jobs-overview.md) erleichtert die Verwaltung mehrerer Datenbanken. Mithilfe von Aufträgen können Sie auf sichere und zuverlässige Weise Aufgaben (T-SQL-Skripts) für eine Gruppe von Datenbanken ausführen. Aufträge können Änderungen an Schemadaten und gemeinsamen Verweisdaten für alle Mandanten in einer Anwendung bereitstellen. Mit „Elastische Aufträge“ kann auch eine *Vorlagendatenbank* verwaltet werden, anhand derer neue Mandanten erstellt werden. Dabei wird sichergestellt, dass sie stets die aktuellen Schema- und Verweisdaten aufweist.

![Bildschirm](./media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-public-preview"></a>Public Preview für „Elastische Aufträge“

Es gibt eine neue Version von „Elastische Aufträge“, die nun ein integriertes Feature von Azure SQL-Datenbank darstellt. Die neue Version von „Elastische Aufträge“ ist aktuell als Public Preview verfügbar. Diese Public Preview unterstützt aktuell die Erstellung eines Auftrags-Agents mithilfe von PowerShell sowie die Erstellung und Verwaltung von Aufträgen mithilfe von T-SQL.
Weitere Informationen finden Sie im Artikel [Erstellen, Konfigurieren und Verwalten von Aufträgen für die elastische Datenbank](./elastic-jobs-overview.md).

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Abrufen der Skripts zur SaaS-Anwendung Wingtip Tickets mit einer Datenbank pro Mandant

Der Quellcode der Anwendung und die Verwaltungsskripts sind im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) verfügbar. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-SaaS-Skripts finden Sie unter [General guidance for working with Wingtip Tickets sample SaaS apps](saas-tenancy-wingtip-app-guidance-tips.md) (Allgemeine Hinweise zur Verwendung von Wingtip Tickets-Beispiel-SaaS-Apps).

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Erstellen einer Auftrags-Agent-Datenbank und eines neuen Auftrags-Agents

Für dieses Tutorial müssen Sie mit PowerShell den Auftrags-Agent und die unterstützende Auftrags-Agent-Datenbank erstellen. Die Auftrags-Agent-Datenbank enthält Auftragsdefinitionen, den Auftragsstatus und den Verlauf. Wenn der Auftrags-Agent und die zugehörige Datenbank erstellt wurden, können Sie sofort Aufträge erstellen und überwachen.

1. Öffnen Sie „…\\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1* “ in der **PowerShell ISE** .
1. Drücken Sie **F5** , um das Skript auszuführen.

Das Skript *Demo-SchemaManagement.ps1* ruft das Skript *Deploy-SchemaManagement.ps1* auf, um die Datenbank *osagent* auf dem Katalogserver zu erstellen. Anschließend erstellt es den Auftrags-Agent mit der Datenbank als Parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Erstellen eines Auftrags, um neue Verweisdaten für alle Mandanten bereitzustellen

In der Wingtip Tickets-App gehört zu jeder Mandantendatenbank ein Satz von unterstützten Veranstaltungsorttypen. Jeder Veranstaltungsort weist einen bestimmten Veranstaltungsorttyp auf, der die Art der möglichen Veranstaltungen definiert und das Hintergrundbild in der App festlegt. Wenn die Anwendung neue Arten von Ereignissen unterstützen soll, müssen diese Verweisdaten aktualisiert und neue Veranstaltungsorttypen hinzugefügt werden.  In dieser Übung stellen Sie ein Update für alle Mandantendatenbanken bereit, wobei zwei weitere Veranstaltungsorttypen hinzugefügt werden: *Motorcycle Racing* und *Swimming Club* .

Prüfen Sie zunächst die in jeder Mandantendatenbank enthaltenen Veranstaltungsorttypen. Stellen Sie in SQL Server Management Studio (SSMS) eine Verbindung mit einer der Mandantendatenbanken her, und überprüfen Sie die Tabelle VenueTypes.  Sie können diese Tabelle auch im Azure-Portal im Abfrage-Editor abfragen, den Sie über die Seite „Datenbank“ aufrufen können. 

1. Öffnen Sie SSMS, und stellen Sie eine Verbindung mit dem Mandantenserver *tenants1-dpt-&lt;Benutzer&gt;.database.windows.net* her.
1. Um sich zu vergewissern, dass *Motorcycle Racing* und *Swimming Club* **nicht** in der Ergebnisliste enthalten sind, navigieren Sie zur Datenbank _contosoconcerthall_ auf dem Server *tenants1-dpt-&lt;Benutzer&gt;* , und fragen Sie die Tabelle *VenueTypes* ab.

Nun erstellen Sie einen Auftrag zum Aktualisieren der Tabelle *VenueTypes* in allen Mandantendatenbanken und fügen die neuen Veranstaltungsorttypen hinzu.

Zum Erstellen eines neuen Auftrags verwenden Sie eine Gruppe von gespeicherten Systemprozeduren für Aufträge, die beim Erstellen des Auftrags-Agents in der Datenbank _jobagent_ erstellt wurden.

1. Stellen Sie in SSMS eine Verbindung mit dem Katalogserver her: *catalog-dpt-&lt;Benutzer&gt;.database.windows.net* . 
1. Öffnen Sie in SSMS die Datei „…\\Learning Modules\\Schema Management\\DeployReferenceData.sql“.
1. Ändern Sie diese Anweisung: „SET @wtpUser = &lt;Benutzer&gt;“. Ersetzen Sie den Wert „Benutzer“ durch den Benutzer, der beim Bereitstellen der App „Wingtip Tickets SaaS Database Per Tenant“ verwendet wurde.
1. Stellen Sie sicher, dass Sie mit der Datenbank _jobagent_ verbunden sind, und drücken Sie **F5** , um das Skript auszuführen.

Beachten Sie die folgenden Elemente im Skript *DeployReferenceData.sql* :
* **sp\_add\_target\_group** erstellt den Zielgruppennamen „DemoServerGroup“.
* Mithilfe von **sp\_add\_target\_group\_member** wird der Satz von Zieldatenbanken erstellt.  Zuerst wird der Server _tenants1-dpt-&lt;Benutzer&gt;_ hinzugefügt.  Das Hinzufügen des Servers als Ziel führt dazu, dass die Datenbanken auf diesem Server zum Zeitpunkt der Auftragsausführung in den Auftrag eingeschlossen werden. Die Datenbanken _basetenantdb_ und *adhocreporting* (in einem späteren Tutorial verwendet) werden als Ziele hinzugefügt.
* **sp\_add\_job** erstellt einen Auftrag mit dem Namen _Reference Data Deployment_ (Verweisdatenbereitstellung).
* **sp\_add\_jobstep** erstellt den Auftragsschritt mit dem T-SQL-Befehlstext zum Aktualisieren der Verweistabelle „VenueTypes“.
* Die übrigen Ansichten im Skript zeigen das Vorhandensein der Objekte an und überwachen die Auftragsausführung. Verwenden Sie diese Abfragen, um den Statuswert in der Spalte **lifecycle** zu überprüfen und zu ermitteln, wann der Auftrag für alle Zieldatenbanken abgeschlossen wurde.

Nach Abschluss des Skripts können Sie überprüfen, ob die Verweisdaten aktualisiert wurden.  Navigieren Sie in SSMS zur Datenbank *contosoconcerthall* auf dem Server *tenants1-dpt-&lt;Benutzer&gt;* , und fragen Sie die Tabelle *VenueTypes* ab.  Vergewissern Sie sich, dass *Motorcycle Racing* und *Swimming Club* jetzt **vorhanden sind** .


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Erstellen eines Auftrags zum Verwalten des Index der Verweistabelle

In dieser Übung wird ein Auftrag verwendet, um den Index für den Primärschlüssel der Verweistabelle neu zu erstellen.  Dies ist ein typischer Datenbank-Wartungsvorgang, der beispielsweise nach dem Laden großer Datenmengen durchgeführt werden kann.

Erstellen Sie einen Auftrag mit den gleichen gespeicherten Systemprozeduren für Aufträge.

1. Öffnen Sie SSMS, und stellen Sie eine Verbindung mit dem Server _catalog-dpt-&lt;Benutzer&gt;.database.windows.net_ her.
1. Öffnen Sie die Datei _…\\Learning Modules\\Schema Management\\OnlineReindex.sql_ .
1. Klicken Sie mit der rechten Maustaste, wählen Sie die Option „Verbindung“ aus, und stellen Sie eine Verbindung mit dem Server _catalog-dpt-&lt;Benutzer&gt;.database.windows.net_ her, sofern dies noch nicht geschehen ist.
1. Stellen Sie sicher, dass Sie mit der Datenbank _jobagent_ verbunden sind, und drücken Sie **F5** , um das Skript auszuführen.

Beachten Sie die folgenden Elemente im Skript _OnlineReindex.sql_ :
* **sp\_add\_job** erstellt einen neuen Auftrag mit dem Namen „Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885“.
* **sp\_add\_jobstep** erstellt den Auftragsschritt mit dem T-SQL-Befehlstext zum Aktualisieren des Index.
* Die verbleibenden Ansichten im Skript überwachen die Auftragsausführung. Verwenden Sie diese Abfragen, um den Statuswert in der **lifecycle** -Spalte zu überprüfen und zu ermitteln, wann der Auftrag für alle Zielgruppenelemente erfolgreich abgeschlossen wurde.



## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> 
> * Erstellen eines Auftrags-Agents zum datenbankübergreifenden Ausführen von T-SQL-Aufträgen
> * Aktualisieren von Verweisdaten in allen Mandantendatenbanken
> * Erstellen eines Index für eine Tabelle in allen Mandantendatenbanken

Absolvieren Sie als Nächstes das Tutorial [Ad-hoc-Berichterstellung](./saas-tenancy-cross-tenant-reporting.md), um das Ausführen verteilter Abfragen über Mandantendatenbanken hinweg zu untersuchen.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Weitere Tutorials, die auf der ersten Anwendungsbereitstellung von Wingtip Tickets SaaS Database Per Tenant aufbauen](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Verwalten horizontal hochskalierter Clouddatenbanken](./elastic-jobs-overview.md)