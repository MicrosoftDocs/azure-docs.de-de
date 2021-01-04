---
title: Bereitstellen neuer Mandanten in einer mehrinstanzenfähigen App
description: Erfahren Sie, wie Sie neue Mandanten in einer mehrinstanzenfähigen SaaS-App in Azure SQL-Datenbank bereitstellen und katalogisieren können.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 26add03929551c912b4d7b7cf10741d53333689a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780562"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Hier erfahren Sie, wie Sie neue Mandanten bereitstellen und sie im Katalog registrieren.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial erfahren Sie, wie Sie SaaS-Muster bereitstellen und katalogisieren. Sie erfahren außerdem, wie diese Muster in der Wingtip Tickets SaaS-Anwendung mit einer Datenbank pro Mandant implementiert werden. Sie erstellen und initialisieren neue Mandantendatenbanken und registrieren sie im Mandantenkatalog der Anwendung. Der Katalog ist eine Datenbank, in der die Zuordnung zwischen den zahlreichen Mandanten von SaaS-Anwendungen und den dazugehörigen Daten verwaltet wird. Der Katalog spielt eine wichtige Rolle bei der Weiterleitung von Anwendungs- und Verwaltungsanforderungen an die richtige Datenbank.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Bereitstellen eines einzelnen neuen Mandanten
> * Bereitstellen eines Batches von zusätzlichen Mandanten


Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die Wingtip Tickets-SaaS-App mit einer Datenbank pro Mandant ist bereitgestellt. Unter [Bereitstellen und Kennenlernen der Wingtip Tickets SaaS-App mit einer Datenbank pro Mandant](./saas-dbpertenant-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.
* Azure PowerShell wurde installiert. Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Einführung in das SaaS-Katalogmuster

Für eine mehrinstanzenfähige SaaS-Anwendung mit Datenbankunterstützung ist es wichtig zu wissen, wo die Informationen über jeden einzelnen Mandanten gespeichert sind. Im SaaS-Katalogmuster wird eine Katalogdatenbank zum Speichern der Zuordnungen zwischen den einzelnen Mandanten und der Datenbank verwendet, in der die Daten gespeichert werden. Dieses Muster trifft immer dann zu, wenn Mandantendaten auf mehrere Datenbanken verteilt sind.

Jeder Mandant wird anhand eines Schlüssels im Katalog identifiziert, der dem Speicherort der jeweiligen Datenbank zugeordnet ist. In der Wingtip Tickets-App wird der Schlüssel aus einem Hash des Mandantennamens gebildet. Mithilfe dieses Schemas kann die App den Schlüssel aus dem in der Anwendungs-URL enthaltenen Mandantennamen bilden. Es können andere Mandantenschlüsselschemas verwendet werden.

Dank des Katalogs kann der Name oder Speicherort der Datenbank ohne größere Auswirkungen auf die Anwendung geändert werden. In einem Datenbankmodell mit mehreren Mandanten kann mithilfe dieser Funktion auch ein Mandant zwischen Datenbanken „verschoben“ werden. Darüber hinaus kann der Katalog angeben, ob ein Mandant oder eine Datenbank offline ist (beispielsweise zur Wartung). Weitere Informationen zu dieser Funktion finden Sie im Tutorial zum [Wiederherstellen eines einzelnen Mandanten](saas-dbpertenant-restore-single-tenant.md).

Im Katalog können auch zusätzliche Mandanten- oder Datenbankmetadaten gespeichert werden, z.B. Schemaversion, Dienstplan oder Mandanten angebotene SLAs. Im Katalog können andere Informationen gespeichert werden, die eine Anwendungsverwaltung, Kundensupport oder DevOps ermöglichen.

Abgesehen von der SaaS-Anwendung kann der Katalog auch die Verwendung von Datenbanktools ermöglichen. Im Beispiel zu Wingtip Tickets SaaS mit einer Datenbank pro Mandant wird der Katalog verwendet, um mandantenübergreifende Abfragen zu ermöglichen, die im [Tutorial zur Ad-hoc-Berichterstellung](saas-tenancy-cross-tenant-reporting.md) erläutert werden. Die datenbankübergreifende Auftragsverwaltung wird in den Tutorials zur [Schemaverwaltung](saas-tenancy-schema-management.md) und [Mandantenanalyse](saas-tenancy-tenant-analytics.md) behandelt.

In den Beispielen zu Wingtip Tickets SaaS wird der Katalog mithilfe der Shardverwaltungsfeatures der [Clientbibliothek für elastische Datenbanken (Elastic Database Client Library, EDCL)](elastic-database-client-library.md) implementiert. Die EDCL ist in Java und .Net Framework verfügbar. Die EDCL ermöglicht einer Anwendung die Erstellung, Verwaltung und Verwendung einer datenbankgestützten Shardzuordnung.

Eine Shardzuordnung enthält eine Liste mit Shards (Datenbanken) und die Zuordnung zwischen Schlüsseln (Mandanten) und Shards. EDCL-Funktionen werden im Rahmen der Mandantenbereitstellung verwendet, um Einträge in der Shardzuordnung zu erstellen. Sie werden zur Laufzeit von Anwendungen genutzt, um eine Verbindung mit der richtigen Datenbank herzustellen. EDCL speichert Verbindungsinformationen zwischen, um den Datenverkehr für die Katalogdatenbank zu minimieren und die Anwendung zu beschleunigen.

> [!IMPORTANT]
> Sie können auf die Zuordnungsdaten in der Katalogdatenbank zugreifen, *allerdings sollten Sie sie nicht bearbeiten*. Bearbeiten Sie Zuordnungsdaten nur über EDCL-APIs. Ein direktes Bearbeiten der Zuordnungsdaten birgt das Risiko, den Katalog zu beschädigen, und wird daher nicht unterstützt.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Einführung in das SaaS-Bereitstellungsmuster

Wenn Sie einen neuen Mandanten in einer SaaS-Anwendung hinzufügen, die ein Datenbankmodell mit einem einzelnen Mandanten verwendet, müssen Sie eine neue Mandantendatenbank bereitstellen. Die Datenbank muss am korrekten Ort und auf der korrekten Dienstebene erstellt werden. Sie muss außerdem mit dem korrekten Schema und den korrekten Referenzdaten initialisiert werden. Zudem muss sie im Katalog unter dem korrekten Mandantenschlüssel registriert werden.

Für die Datenbankbereitstellung können verschiedene Ansätze verwendet werden. Sie können SQL-Skripts ausführen, eine BACPAC-Datei bereitstellen oder eine Vorlagendatenbank kopieren.

Die Datenbankbereitstellung muss Teil Ihrer Schemaverwaltungsstrategie sein. Sie müssen sicherstellen, dass neue Datenbanken mit dem neuesten Schema bereitgestellt werden. Diese Anforderung wird im [Tutorial zur Schemaverwaltung](saas-tenancy-schema-management.md) behandelt.

Die Wingtip Tickets-App mit einer Datenbank pro Mandant stellt neue Mandanten bereit, indem sie eine auf dem Katalogserver bereitgestellte Vorlagendatenbank namens _basetenantdb_ kopiert. Die Bereitstellung kann im Rahmen der Registrierung in die Anwendung integriert werden. Sie kann auch offline mithilfe von Skripts unterstützt werden. In diesem Tutorial wird die Bereitstellung mithilfe von PowerShell durchgeführt.

Bereitstellungsskripts kopieren die Datenbank _basetenantdb_, um eine neue Mandantendatenbank in einem Pool für elastische Datenbanken zu erstellen. Die Mandantendatenbank wird in dem Mandantenserver erstellt, der dem _newtenant_-DNS-Alias zugeordnet ist. In diesem Alias wird ein Verweis auf den Server verwaltet, mit dem neue Mandanten bereitgestellt werden, und dieser Alias wird aktualisiert, um auf einen Wiederherstellungsmandantenserver in den Tutorials zur Notfallwiederherstellung zu verweisen ([Notfallwiederherstellung mithilfe von Geowiederherstellung](./saas-dbpertenant-dr-geo-restore.md), [Notfallwiederherstellung mithilfe von Georeplikation](./saas-dbpertenant-dr-geo-replication.md)). Anschließend wird die Datenbank von den Skripts mit mandantenspezifischen Informationen initialisiert und in der Katalogshardzuordnung registriert. Mandantendatenbanken erhalten ihre Namen auf Grundlage des Mandantennamens. Dieses Benennungsschema ist kein wichtiger Bestandteil des Musters. Da der Katalog den Mandantenschlüssel dem Datenbanknamen zuordnet, kann jede Benennungskonvention verwendet werden.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Abrufen der Skripts zur SaaS-Anwendung Wingtip Tickets mit einer Datenbank pro Mandant

Die Wingtip Tickets SaaS-Skripts und der Quellcode der Anwendung stehen im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) zur Verfügung. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-SaaS-Skripts finden Sie unter [General guidance for working with Wingtip Tickets sample SaaS apps](saas-tenancy-wingtip-app-guidance-tips.md) (Allgemeine Hinweise zur Verwendung von Wingtip Tickets-Beispiel-SaaS-Apps).


## <a name="provision-and-catalog-detailed-walkthrough"></a>Bereitstellen und Katalogisieren: detaillierte exemplarische Vorgehensweise

Um nachzuvollziehen, wie die Wingtip Tickets-Anwendung die Bereitstellung neuer Mandanten implementiert, fügen Sie einen Haltepunkt hinzu, und folgen Sie während der Bereitstellung eines Mandanten dem Workflow.

1. Öffnen Sie in der PowerShell ISE ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_, und legen Sie die folgenden Parameter fest:

   * **$TenantName** = Name des neuen Veranstaltungsorts (z.B., *Bushwillow Blues*).
   * **$VenueType** = einer der vordefinierten Veranstaltungsorttypen: _blues, classicalmusic, dance, jazz, judo, motor racing, multipurpose, opera, rockmusic, soccer_
   * **$DemoScenario** = **1**, *Bereitstellen eines einzelnen Mandanten*

2. Zum Hinzufügen eines Haltepunkts bewegen Sie den Cursor an eine beliebige Stelle in der Zeile mit *New-Tenant `* . Drücken Sie dann F9.

   ![Screenshot: Skript mit hervorgehobener Zeile „New-Tenant“ zum Hinzufügen eines Breakpoints.](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Drücken Sie F5, um das Skript auszuführen.

4. Nachdem die Skriptausführung am Haltepunkt beendet wurde, drücken Sie F11, um den Code schrittweise auszuführen.

   ![Debuggen](./media/saas-dbpertenant-provision-and-catalog/debug.png)



Verfolgen Sie die Ausführung des Skripts mithilfe der Menüoptionen unter **Debug**. Drücken Sie F10 und F11, um die aufgerufenen Funktionen zu überspringen oder schrittweise auszuführen. Weitere Informationen zum Debuggen von PowerShell-Skripts finden Sie unter [Tipps zum Arbeiten mit und Debuggen von PowerShell-Skripts](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Sie müssen diesen Workflow nicht genau befolgen. Er erläutert, wie Sie das Skript debuggen.

* **Importieren Sie das Modul „CatalogAndDatabaseManagement.psm1“.** Es stellt eine Abstraktion auf Katalog- und Mandantenebene über die [Shardverwaltungsfunktionen](elastic-scale-shard-map-management.md) bereit. Dieses Modul kapselt einen Großteil des Katalogmusters, und es lohnt sich, dieses Modul genauer zu betrachten.
* **Importieren Sie das Modul „SubscriptionManagement.psm1“.** Es enthält Funktionen für die Anmeldung bei Azure und das Auswählen des Azure-Abonnements, mit dem Sie arbeiten möchten.
* **Rufen Sie Konfigurationsinformationen ab.** Wechseln Sie mit F11 zu „Get-Configuration“, und sehen Sie sich an, wie die App-Konfiguration angegeben ist. Hier sind Ressourcennamen und andere App-spezifische Werte definiert. Ändern Sie diese Werte nicht, es sei denn, sie kennen sich gut mit Skripts aus.
* **Rufen Sie das Katalogobjekt ab.** Wechseln Sie zu „Get-Catalog“. Hierdurch wird ein im übergeordneten Skript verwendetes Katalogobjekt erstellt und zurückgegeben. Diese Funktion verwendet aus **AzureShardManagement.psm1** importierte Shardverwaltungsfunktionen. Das Katalogobjekt besteht aus den folgenden Elementen:

   * „$catalogServerFullyQualifiedName“ wird aus dem Standardstamm plus Ihrem Benutzernamen gebildet: _catalog-\<user\>.database.windows.net_.
   * „$catalogDatabaseName“ wird aus der Konfigurationsdatei abgerufen: *tenantcatalog*.
   * Das Objekt „$shardMapManager“ wird aus der Katalogdatenbank initialisiert.
   * Das Objekt „$shardMap“ wird aus der Shardzuordnung _tenantcatalog_ in der Katalogdatenbank initialisiert. Ein Katalogobjekt wird erstellt und zurückgegeben. Es wird im Skript der höheren Ebene verwendet.
* **Berechnen Sie den neuen Mandantenschlüssel.** Eine Hashfunktion wird verwendet, um den Mandantenschlüssel aus dem Mandantennamen zu erstellen.
* **Überprüfen Sie, ob der Mandantenschlüssel bereits vorhanden ist.** Der Katalog wird überprüft, um sicherzustellen, dass der Schlüssel verfügbar ist.
* **Die Mandantendatenbank wird mit „New-TenantDatabase“ bereitgestellt.** Drücken Sie F11, um zu sehen, wie die Datenbank mithilfe einer [Azure Resource Manager-Vorlage](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) bereitgestellt wird.

    Der Datenbankname wird aus dem Mandantennamen gebildet, um zu verdeutlichen, welcher Shard zu welchem Mandanten gehört. Sie können auch andere Benennungskonventionen für Datenbanken verwenden. Mit einer Resource Manager-Vorlage wird eine Mandantendatenbank erstellt, indem eine Vorlagendatenbank (_baseTenantDB_) auf dem Katalogserver kopiert wird. Alternativ können Sie eine Datenbank erstellen und sie durch Importieren einer BACPAC-Datei initialisieren. Sie können auch ein Initialisierungsskript von einem bekannten Speicherort ausführen.

    Die Resource Manager-Vorlage befindet sich im Ordner „……\Learning Modules\Common\“: *tenantdatabasecopytemplate.json*

* **Die Mandantendatenbank wird weiter initialisiert.** Der Name des Veranstaltungsorts (Mandantenname) und der Veranstaltungsorttyp werden hinzugefügt. Sie können hier auch weitere Initialisierungen vornehmen.

* **Die Mandantendatenbank wird im Katalog registriert.** Zur Registrierung wird *Add-TenantDatabaseToCatalog* mit dem Mandantenschlüssel verwendet. Drücken Sie F11, um sich die Schritte im Detail anzuschauen:

    * Die Katalogdatenbank wird der Shardzuordnung (Liste der bekannten Datenbanken) hinzugefügt.
    * Die Zuordnung, die den Schlüsselwert mit dem Shard verknüpft, wird erstellt.
    * Zusätzliche Metadaten für den Mandanten (Name des Veranstaltungsorts) werden der Mandantentabelle im Katalog hinzugefügt. Die Mandantentabelle ist nicht Teil des Schemas „ShardManagement“ und wird nicht durch die EDCL installiert. Diese Tabelle veranschaulicht, wie die Katalogdatenbank erweitert werden kann, um zusätzliche anwendungsspezifische Daten zu unterstützen.


Nach Abschluss der Bereitstellung kehrt die Ausführung zum ursprünglichen *Demo-ProvisionAndCatalog*-Skript zurück. Die Seite **Ereignisse** für den neuen Mandanten wird im Browser geöffnet.

   ![Seite „Ereignisse“](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Bereitstellen eines Batches von Mandanten

In dieser Übung wird ein Batch mit 17 Mandanten bereitgestellt. Wir empfehlen, diesen Mandantenbatch bereitzustellen, bevor Sie mit anderen Tutorials zu Wingtip Tickets SaaS mit einer Datenbank pro Mandant beginnen. Es stehen Ihnen mehrere Datenbanken zur Verfügung.

1. Öffnen Sie in der PowerShell ISE ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Ändern Sie den Parameter *$DemoScenario* in „3“:

   * **$DemoScenario** = **3**, *Bereitstellen eines Batches von Mandanten*
2. Drücken Sie F5, um das Skript auszuführen.

Das Skript stellt einen Batch von zusätzlichen Mandanten bereit. Es verwendet eine [Azure Resource Manager-Vorlage](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), die den Batch steuert und die Bereitstellung der einzelnen Datenbanken an eine verknüpfte Vorlage delegiert. Indem Sie Vorlagen auf diese Weise verwenden, können Sie mit Azure Resource Manager den Bereitstellungsprozess für das Skript vermitteln. Die Vorlagen stellen Datenbanken parallel bereit und verarbeiten nötigenfalls Wiederholungen. Das Skript ist idempotent. Wenn also ein Fehler auftritt oder das Skript aus irgendeinem Grund angehalten wird, führen Sie es erneut aus.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Überprüfen des erfolgreich bereitgestellten Mandantenbatches

* Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Serverliste, und öffnen Sie den Server *tenants1*. Wählen Sie **SQL-Datenbanken** aus, und überprüfen Sie, ob der Batch von 17 zusätzlichen Datenbanken jetzt in der Liste aufgeführt ist.

   ![Datenbankliste](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Weitere Bereitstellungsmuster

Weitere Bereitstellungsmuster, die in diesem Tutorial nicht behandelt werden:

**Vorabbereitstellung von Datenbanken**: Die Vorabbereitstellung profitiert von der Tatsache, dass Datenbanken in einem Pool für elastische Datenbanken keine zusätzlichen Kosten hervorrufen. Die Abrechnung erfolgt für den Pool für elastische Datenbanken und nicht für die Datenbanken. Datenbanken im Leerlauf verbrauchen keine Ressourcen. Indem Sie Datenbanken vorab in einem Pool bereitstellen und später bei Bedarf zuordnen, können Sie den Zeitaufwand für das Hinzufügen von Mandanten verringern. Die Anzahl der vorab bereitgestellten Datenbanken kann bedarfsorientiert angepasst werden, um einen für die erwartete Bereitstellungsrate geeigneten Puffer bereitzuhalten.

**Automatische Bereitstellung**: Beim Muster der automatischen Bereitstellung stellt ein Bereitstellungsdienst Server, Pools und Datenbanken bei Bedarf automatisch bereit. Auf Wunsch können Sie die Vorabbereitstellung von Datenbanken in Pools für elastische Datenbanken einbinden. Wenn Datenbanken außer Betrieb genommen und gelöscht werden, können Lücken in den Pools für elastische Datenbanken durch den Bereitstellungsdienst gefüllt werden. Ein solcher Dienst kann einfach oder komplex sein. Die Bereitstellung kann beispielsweise über mehrere geografische Regionen erfolgen und die Georeplikation zur Notfallwiederherstellung eingerichtet werden.

Beim Muster der automatischen Bereitstellung sendet eine Clientanwendung oder ein Skript eine Bereitstellungsanforderung an eine Warteschlange, damit diese vom Bereitstellungsdienst verarbeitet wird. Anschließend wird vom Dienst der Abschluss des Vorgangs abgefragt. Bei Verwendung der Vorabbereitstellung werden Anforderungen schnell verarbeitet. Der Dienst stellt eine Ersatzdatenbank im Hintergrund bereit.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
>
> * Bereitstellen eines einzelnen neuen Mandanten
> * Bereitstellen eines Batches von zusätzlichen Mandanten
> * Kennenlernen der Details zur Bereitstellung von Mandanten und zu deren Registrierung im Katalog

Sehen Sie sich das [Tutorial zur Leistungsüberwachung](./saas-dbpertenant-performance-monitoring.md) an.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Weitere Tutorials, die auf der Wingtip Tickets SaaS-Anwendung mit einer Datenbank pro Mandant aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md)
* [Debuggen von Skripts in der Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)