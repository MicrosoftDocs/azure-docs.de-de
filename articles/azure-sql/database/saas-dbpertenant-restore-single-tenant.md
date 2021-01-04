---
title: Wiederherstellen einer Datenbank in einer mehrinstanzenfähigen Saas-App
description: Erfahren Sie, wie Sie die Azure SQL-Datenbank-Instanz eines einzelnen Mandanten nach versehentlichem Löschen von Daten wiederherstellen.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780477"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Wiederherstellen eines einzelnen Mandanten mit einer SaaS-Anwendung mit einer Datenbank pro Mandant
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Mit dem Modell „eine Datenbank pro Mandant“ ist es ganz einfach, einen einzelnen Mandanten – ohne Auswirkungen auf andere Mandanten – auf einen früheren Zeitpunkt wiederherzustellen.

In diesem Tutorial lernen Sie zwei Muster der Datenwiederherstellung kennen:

> [!div class="checklist"]
> * Wiederherstellen einer Datenbank in eine parallele Datenbank (Nebeneinander)
> * Direktes Wiederherstellen einer Datenbank mit Ersetzen der vorhandenen Datenbank

| Muster | BESCHREIBUNG |
|:--|:--|
| Wiederherstellen in eine parallele Datenbank | Dieses Muster kann für Aufgaben wie Überprüfung, Überwachung und Compliance verwendet werden, um einem Mandanten das Überprüfen des Zustands seiner Daten zu einem früheren Zeitpunkt zu ermöglichen. Die aktuelle Datenbank des Mandanten bleibt online und unverändert erhalten. |
| Direktes Wiederherstellen | Dieses Muster wird normalerweise zum Wiederherstellen eines Mandanten auf einen früheren Zeitpunkt verwendet, nachdem der Mandant versehentlich Daten gelöscht oder beschädigt hat. Die ursprüngliche Datenbank wird offline geschaltet und durch die wiederhergestellte Datenbank ersetzt. |
|||

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die Wingtip-SaaS-App wird bereitgestellt. Unter [Bereitstellen und Kennenlernen der Wingtip-SaaS-Anwendung](./saas-dbpertenant-get-started-deploy.md) finden Sie Informationen zur Bereitstellung in weniger als fünf Minuten.
* Azure PowerShell wurde installiert. Ausführliche Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Einführung in die SaaS-Muster zur Mandantenwiederherstellung

Es gibt zwei einfache Muster zum Wiederherstellen der Daten eines einzelnen Mandanten. Da Mandantendatenbanken voneinander isoliert sind, hat das Wiederherstellen eines Mandanten keine Auswirkungen auf Daten anderer Mandanten. Das PITR-Feature (Point-in-Time-Wiederherstellung) von Azure SQL-Datenbank wird in beiden Mustern verwendet. Mit PITR wird stets eine neue Datenbank erstellt.

* **Paralleles Wiederherstellen** : Beim ersten Muster wird eine neue Datenbank parallel zur aktuellen Datenbank des Mandanten erstellt. Der Mandant erhält anschließend schreibgeschützten Zugriff auf die wiederhergestellte Datenbank. Die wiederhergestellten Daten können überprüft und möglicherweise zum Überschreiben der aktuellen Datenwerte verwendet werden. Es ist Aufgabe des App-Designers, die Art des Mandantenzugriffs auf die wiederhergestellte Datenbank zu bestimmen und festzulegen, welche Optionen für die Wiederherstellung bereitgestellt werden. In manchen Szenarien reicht es bereits aus, dem Mandanten nur das Überprüfen seiner Daten zu einem früheren Zeitpunkt zu ermöglichen.

* **Direktes Wiederherstellen** : Das zweite Muster ist hilfreich, wenn Daten verloren gegangen sind oder beschädigt wurden und der Mandant die Daten auf einen früheren Zeitpunkt wiederherstellen möchte. Der Mandant wird offline geschaltet, während die Datenbank wiederhergestellt wird. Die ursprüngliche Datenbank wird gelöscht, und die wiederhergestellte Datenbank wird umbenannt. Der Zugriff auf die Sicherungskette der ursprünglichen Datenbank bleibt nach dem Löschvorgang erhalten, sodass Sie bei Bedarf die Datenbank auf einen früheren Zeitpunkt wiederherstellen können.

Wenn die Datenbank die [aktive Georeplikation](active-geo-replication-overview.md) und die parallele Wiederherstellung verwendet, empfiehlt es sich, alle erforderlichen Daten aus der wiederhergestellten Kopie in die ursprüngliche Datenbank zu kopieren. Wenn Sie die ursprüngliche Datenbank durch die wiederhergestellte Datenbank ersetzen, müssen Sie die Georeplikation neu konfigurieren und synchronisieren.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Abrufen der Skripts zur SaaS-Anwendung Wingtip Tickets mit einer Datenbank pro Mandant

Die Skripts und der Anwendungsquellcode der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbank stehen im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) zur Verfügung. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-SaaS-Skripts finden Sie unter [Allgemeine Anweisungen zum Arbeiten mit den Wingtip-Tickets-Beispielanwendungen für SaaS](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Vorbereitung

Wenn eine Datenbank erstellt wird, kann es 10 bis 15 Minuten dauern, bevor die erste vollständige Sicherung für eine Wiederherstellung verfügbar ist. Wenn Sie die Anwendung gerade installiert haben, müssen Sie möglicherweise einige Minuten warten, bevor Sie dieses Szenario ausprobieren können.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulieren des versehentlichen Löschens von Daten durch den Mandanten

Um diese Wiederherstellungsszenarien zu demonstrieren, müssen Sie zuerst ein Ereignis in einer Mandantendatenbank „versehentlich“ löschen. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Öffnen der Events-App, um die aktuellen Ereignisse zu prüfen

1. Öffnen Sie den Event Hub (http://events.wtp.&lt;Benutzer&gt;.trafficmanager.net), und wählen Sie **Contoso Concert Hall** aus.

   ![Veranstaltungshub](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Scrollen Sie durch die Liste der Veranstaltungen, und notieren Sie sich die letzte Veranstaltung in der Liste.

   ![Die letzte Veranstaltung wird angezeigt.](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>„Versehentliches“ Löschen der letzten Veranstaltung

1. Öffnen Sie „...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* “ in der PowerShell ISE, und legen Sie den folgenden Wert fest:

   * **$DemoScenario** = **1** – *Löschen der letzten Veranstaltung (keine Ticketverkäufe)* .
2. Drücken Sie F5, um das Skript auszuführen und die letzte Veranstaltung zu löschen. Die folgende Bestätigungsmeldung wird angezeigt:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Die Contoso-Ereignisseseite wird geöffnet. Scrollen Sie nach unten, und überprüfen Sie, ob die Veranstaltung nicht mehr vorhanden ist. Wenn sich die Veranstaltung weiterhin in der Liste befindet, wählen Sie **Aktualisieren** aus, und überprüfen Sie, ob sie noch vorhanden ist.
   ![Letzte Veranstaltung entfernt](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Wiederherstellen einer Mandantendatenbank parallel mit der Produktionsdatenbank

In dieser Übung wird die Datenbank der Contoso Concert Hall mit dem Status eines bestimmten Zeitpunkts vor dem Löschen des Ereignisses wiederhergestellt. In diesem Szenario wird davon ausgegangen, dass Sie die gelöschten Daten in einer parallelen Datenbank nur überprüfen möchten.

 Mit dem Skript *Restore-TenantInParallel.ps1* wird eine parallele Mandantendatenbank namens *ContosoConcertHall\_old* mit einem parallelen Katalogeintrag erstellt. Dieses Wiederherstellungsmuster eignet sich am besten zur Wiederherstellung nach einem geringfügigen Datenverlust. Sie können dieses Muster auch verwenden, wenn Sie Daten für Compliance- oder Überwachungszwecke überprüfen müssen. Dies ist auch die empfohlene Vorgehensweise bei Verwendung der [aktiven Georeplikation](active-geo-replication-overview.md).

1. Schließen Sie den Abschnitt [Simulieren des versehentlichen Löschens von Daten durch den Mandanten](#simulate-a-tenant-accidentally-deleting-data) ab.
2. Öffnen Sie in der PowerShell ISE „...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_ “.
3. Legen Sie **$DemoScenario** = **2** fest, um das *Szenario zur parallelen Wiederherstellung des Mandanten* auszuwählen.
4. Drücken Sie F5, um das Skript auszuführen.

Mit dem Skript wird die Mandantendatenbank auf einen Zeitpunkt vor dem Löschen des Ereignisses wiederhergestellt. Die Datenbank wird in eine neue Datenbank namens _ContosoConcertHall\_old_ wiederhergestellt. Die Katalogmetadaten in der wiederhergestellten Datenbank werden gelöscht, und die Datenbank wird anschließend mithilfe eines aus dem Namen *ContosoConcertHall\_old* erstellten Schlüssels dem Katalog hinzugefügt.

Mit dem Demoskript wird die Seite mit den Ereignissen für diese neuen Mandantendatenbank in Ihrem Browser geöffnet. Anhand der URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` können Sie erkennen, dass auf dieser Seite Daten aus der wiederhergestellten Datenbank angezeigt werden, wobei dem Namen *_old* angefügt ist.

Scrollen Sie durch die im Browser aufgelisteten Veranstaltungen, um zu überprüfen, ob die im vorherigen Abschnitt gelöschte Veranstaltung wiederhergestellt wurde.

Sie würden einem Mandanten vermutlich den Zugriff auf wiederhergestellte Daten nicht gewähren, indem Sie den wiederhergestellten Mandanten als zusätzlichen Mandanten mit eigener Events-App verfügbar machen. Diese Vorgehensweise dient lediglich zum Veranschaulichen des verwendeten Wiederherstellungsmusters. Normalerweise würden Sie schreibgeschützten Zugriff auf die alten Daten gewähren und die wiederhergestellte Datenbank für einen bestimmten Zeitraum beibehalten. Im Beispiel können Sie, wenn Sie fertig sind, den wiederhergestellten Mandanteneintrag durch Ausführen des Szenarios _Entfernen des wiederhergestellten Mandanten_ löschen.

1. Legen Sie **$DemoScenario** = **4** fest – *Entfernen des wiederhergestellten Mandanten* .
2. Drücken Sie F5, um das Skript auszuführen.
3. Der Eintrag *ContosoConcertHall\_old* ist jetzt aus dem Katalog gelöscht. Schließen Sie die Veranstaltungsseite für diesen Mandanten in Ihrem Browser.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Direktes Wiederherstellen eines Mandanten, Ersetzen der vorhandenen Mandantendatenbank

In dieser Übung wird der Mandant „Contoso Concert Hall“ auf einen Zeitpunkt vor dem Löschen des Ereignisses wiederhergestellt. Mit dem Skript *Restore-TenantInPlace* wird eine Mandantendatenbank in eine neue Datenbank wiederhergestellt und die ursprüngliche Datenbank gelöscht. Dieses Wiederherstellungsmuster eignet sich optimal für die Wiederherstellung bei einer schwerwiegenden Datenbeschädigung, bei der der Mandant mit erheblichen Datenverlusten rechnen muss.

1. Öffnen Sie die Datei **Demo-RestoreTenant.ps1** in der PowerShell ISE.
2. Legen Sie **$DemoScenario** = **5** fest, um das *Szenario zum direkten Wiederherstellen des Mandanten* auszuwählen.
3. Drücken Sie F5, um das Skript auszuführen.

Mit dem Skript wird die Mandantendatenbank auf einen Zeitpunkt vor dem Löschen des Ereignisses wiederhergestellt. Zuerst wird der Mandant „Contoso Concert Hall“ offline geschaltet, um weitere Aktualisierungen zu verhindern. Anschließend wird durch die Wiederherstellung vom Wiederherstellungspunkt eine parallele Datenbank erstellt. Die wiederhergestellte Datenbank wird nach dem Zeitstempel benannt, um sicherzustellen, dass der Datenbankname nicht zu Konflikten mit dem Namen der vorhandenen Mandantendatenbank führt. Als Nächstes wird die alte Mandantendatenbank gelöscht, und die wiederhergestellte Datenbank wird mit dem Namen der ursprünglichen umbenannt. Schließlich wird Contoso Concert Hall wieder online geschaltet, um den App-Zugriff auf die wiederhergestellte Datenbank zu ermöglichen.

Sie haben die Datenbank erfolgreich mit dem Status zu einem bestimmten Zeitpunkt vor dem Löschen des Ereignisses wiederhergestellt. Die Seite **Events** wird geöffnet, sodass Sie sich von der Wiederherstellung der letzten Veranstaltung überzeugen können.

Sie müssen nach dem Wiederherstellen der Datenbank weitere 10 bis 15 Minuten warten, bis die erste vollständige Sicherung für ein erneutes Wiederherstellen verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Wiederherstellen einer Datenbank in eine parallele Datenbank (Nebeneinander)
> * Direktes Wiederherstellen einer Datenbank

Absolvieren Sie das Tutorial [Verwalten von Schemas in einer SaaS-Anwendung mit dem Muster für eine Datenbank pro Mandant](saas-tenancy-schema-management.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Zusätzliche Tutorials, die auf der Wingtip-SaaS-Anwendung aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Weitere Informationen zu Sicherungen für SQL-Datenbank](automated-backups-overview.md)