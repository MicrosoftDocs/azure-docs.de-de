---
title: Tutorial für mehrinstanzenfähige SaaS-Anwendungen
description: Bereitstellen und Katalogisieren neuer Mandanten mithilfe des Musters für eigenständige Anwendung
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 2343800f8801105ca75f285972b441ecb027d1a0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793244"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Bereitstellen und Katalogisieren neuer Mandanten mithilfe des SaaS-Musters für eine Anwendung pro Mandant
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel behandelt die Bereitstellung und Katalogisierung neuer Mandanten mithilfe des SaaS-Musters für eine eigenständige Anwendung pro Mandant.
Dieser Artikel setzt sich aus zwei Hauptteilen zusammen:
* Erläuterung des Bereitstellungs- und Katalogisierungskonzepts für neue Mandanten
* Tutorial zur Behandlung des PowerShell-Beispielcodes für die Durchführung der Bereitstellung und Katalogisierung
    * Im Tutorial wird die SaaS-Beispielanwendung Wingtip Tickets verwendet, die an das Muster für eine eigenständige Anwendung pro Mandant angepasst ist.

## <a name="standalone-application-per-tenant-pattern"></a>Muster mit einer eigenständigen Anwendung pro Mandant

Das Muster für eine eigenständige Anwendung pro Mandant ist eines von mehreren Mustern für mehrinstanzenfähige SaaS-Anwendungen.  Bei diesem Muster wird für jeden Mandanten eine eigenständige Anwendung bereitgestellt. Die Anwendung besteht aus Komponenten auf Anwendungsebene und einer Azure SQL-Datenbank-Instanz.  Jede Mandantenanwendung kann im Abonnement des Anbieters bereitgestellt werden.  Alternativ bietet Azure ein [Programm für verwaltete Anwendungen](../../azure-resource-manager/managed-applications/overview.md), in dem eine Anwendung im Abonnement eines Mandanten bereitgestellt und vom Anbieter im Auftrag des Mandanten verwaltet werden kann.

   ![Muster für eine Anwendung pro Mandant](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Beim Bereitstellen einer Anwendung für einen Mandanten werden die Anwendung und die Datenbank in einer neuen Ressourcengruppe bereitgestellt, die für den Mandanten erstellt wurde.  Durch Verwendung separater Ressourcengruppen werden die Anwendungsressourcen der einzelnen Mandanten isoliert und können dadurch unabhängig verwaltet werden. Innerhalb jeder Ressourcengruppe sind die jeweiligen Anwendungsinstanzen so konfiguriert, dass direkt auf die entsprechende Datenbank zugegriffen wird.  Dieses Verbindungsmodell steht im Gegensatz zu anderen Mustern, bei denen Katalog-zu-Broker-Verbindungen zwischen der Anwendung und der Datenbank verwendet werden.  Da keine Ressourcenfreigabe erfolgt, muss jede Mandantendatenbank mit genügend Ressourcen für die jeweilige Spitzenlast bereitgestellt werden. Dieses Muster wird zumeist für SaaS-Anwendungen mit weniger Mandanten verwendet, bei denen der Schwerpunkt auf der Isolation von Mandanten und weniger auf den Ressourcenkosten liegt.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Verwenden eines Mandantenkatalogs mit dem Muster für eine Anwendung pro Mandant

Obwohl die Anwendung und die Datenbank jedes Mandanten vollständig isoliert sind, können verschiedene Verwaltungs- und Analyseszenarien mandantenübergreifend operieren.  Beispielsweise sind zum Anwenden einer Schemaänderung für eine neue Anwendungsversion Änderungen am Schema jeder Mandantendatenbank erforderlich. Berichterstellungs- und Analyseszenarien können auch den Zugriff auf alle Mandantendatenbanken erfordern und zwar unabhängig davon, wo diese bereitgestellt werden.

   ![Diagramm, das veranschaulicht, wie ein Mandantenkatalog mit dem Muster für eine Anwendung pro Mandant verwendet wird](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Der Mandantenkatalog enthält eine Zuordnung zwischen einer Mandanten-ID und einer Mandantendatenbank, sodass eine ID in einen Server- und Datenbanknamen aufgelöst werden kann.  In der Wingtip-SaaS-App wird die Mandanten-ID als Hash des Mandantennamens berechnet, doch können auch andere Schemas verwendet werden.  Zwar benötigen eigenständige Anwendungen den Katalog nicht zum Verwalten von Verbindungen, doch kann er verwendet werden, um andere Aktionen auf eine Gruppe von Mandantendatenbanken zu begrenzen. Beispielsweise kann eine elastische Abfrage den Katalog verwenden, um die Gruppe von Datenbanken zu bestimmen, auf die Abfragen für mandantenübergreifende Berichte verteilt werden.

## <a name="elastic-database-client-library"></a>Clientbibliothek für elastische Datenbanken

In der Wingtip-Beispielanwendung wird der Katalog durch die Shardverwaltungsfeatures der [Clientbibliothek für elastische Datenbanken (Elastic Database Client Library, EDCL)](elastic-database-client-library.md) implementiert.  Die Bibliothek ermöglicht einer Anwendung die Erstellung, Verwaltung und Verwendung einer Shardzuordnung, die in einer Datenbank gespeichert wird. Im Wingtip Tickets-Beispiel wird der Katalog in der Datenbank *Mandantenkatalog* gespeichert.  Der Shard ordnet einen Mandantenschlüssel dem Shard (Datenbank) zu, in dem die Daten dieses Mandanten gespeichert werden.  EDCL-Funktionen verwalten eine *globale Shardzuordnung* , die in Tabellen in der Datenbank *Mandantenkatalog* gespeichert ist, und eine *lokale Shardzuordnung* , die in jedem Shard gespeichert ist.

EDCL-Funktionen können aus Anwendungen oder PowerShell-Skripts aufgerufen werden, um Einträge in der Shardzuordnung zu erstellen und zu verwalten. Andere EDCL-Funktionen können zum Abrufen der Gruppe von Shards oder zum Herstellen einer Verbindung mit der richtigen Datenbank für den angegebenen Mandantenschlüssel verwendet werden.

> [!IMPORTANT]
> Bearbeiten Sie die Daten in der Katalogdatenbank oder die lokale Shardzuordnung in den Mandantendatenbanken nicht direkt. Direkte Updates werden aufgrund des hohen Risikos einer Datenbeschädigung nicht unterstützt. Bearbeiten Sie die Zuordnungsdaten stattdessen ausschließlich über EDCL-APIs.

## <a name="tenant-provisioning"></a>Bereitstellen von Mandanten

Jeder Mandant erfordert eine neue Azure-Ressourcengruppe, die erstellt werden muss, bevor Ressourcen darin bereitgestellt werden können. Sobald die Ressourcengruppe vorhanden ist, kann eine Azure Resource Manager-Vorlage zum Bereitstellen der Anwendungskomponenten und der Datenbank verwendet und dann die Datenbankverbindung konfiguriert werden. Zum Initialisieren des Datenbankschemas kann die Vorlage eine BACPAC-Datei importieren.  Alternativ kann die Datenbank als eine Kopie einer Vorlagendatenbank erstellt werden.  Die Datenbank wird dann mit anfänglichen Daten zu Veranstaltungsorten weiter aktualisiert und im Katalog registriert.

## <a name="tutorial"></a>Lernprogramm

In diesem Tutorial lernen Sie Folgendes:

* Bereitstellen eines Katalogs
* Registrieren der Beispielmandantendatenbanken, die Sie zuvor im Katalog bereitgestellt haben
* Bereitstellen eines zusätzlichen Mandanten und Registrieren im Katalog

Es wird eine Azure Resource Manager-Vorlage verwendet, um die Anwendung bereitzustellen und zu konfigurieren, die Mandantendatenbank zu erstellen und anschließend zum Initialisieren eine BACPAC-Datei zu importieren. Die Importanforderung wird möglicherweise vor der Ausführung für mehrere Minuten in die Warteschlange gestellt.

Am Ende dieses Tutorials verfügen Sie über eine Reihe eigenständiger Mandantenanwendungen, wobei jede Datenbank im Katalog registriert ist.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Azure PowerShell wurde installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).
* Die drei Beispielmandantenanwendungen sind bereitgestellt. Informationen zum Bereitstellen dieser Anwendungen in weniger als fünf Minuten finden Sie unter [Bereitstellen und Untersuchen des Musters der eigenständigen SaaS-Anwendung Wingtip Tickets](./saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Bereitstellen des Katalogs

In dieser Aufgabe erfahren Sie, wie Sie den Katalog bereitstellen, der zum Registrieren aller Mandantendatenbanken verwendet wird. In diesem Tutorial führen Sie folgende Schritte aus:

* **Bereitstellen der Katalogdatenbank** mithilfe einer Azure Resource Manager-Vorlage. Die Datenbank wird durch Importieren einer BACPAC-Datei initialisiert.
* **Registrieren der Beispielmandantenanwendungen** , die Sie zuvor bereitgestellt haben.  Jeder Mandant wird mithilfe eines Schlüssels registriert, der aus einem Hash des Mandantennamens erstellt wird.  Der Mandantenname wird auch in einer Erweiterungstabelle im Katalog gespeichert.

1. Öffnen Sie in der PowerShell ISE *...\Learning Modules\UserConfig.psm* , und aktualisieren Sie den Wert **\<user\>** in den Wert, den Sie bei der Bereitstellung der drei Beispielanwendungen verwendet haben.  **Speichern Sie die Datei** .
1. Öffnen Sie in PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* , und legen Sie **$Scenario = 1** fest. Stellen Sie den Mandantenkatalog bereit, und registrieren Sie die vordefinierten Mandanten.

1. Fügen Sie einen Haltepunkt hinzu, indem Sie den Cursor an eine beliebige Stelle in der Zeile mit `& $PSScriptRoot\New-Catalog.ps1` setzen und dann **F9** drücken.

    ![Festlegen eines Haltepunkts für die Ablaufverfolgung](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Führen Sie das Skript durch Drücken von **F5** aus.
1.  Nachdem die Skriptausführung am Haltepunkt beendet wurde, drücken Sie **F11** , um das Skript „New-Catalog.ps1“ schrittweise auszuführen.
1.  Verfolgen Sie die Ausführung des Skripts mit den Optionen (F10 und F11) im Menü „Debug“ nach, um aufgerufene Funktionen zu überspringen oder einzeln auszuführen.
    *   Weitere Informationen zum Debuggen von PowerShell-Skripts finden Sie unter [Tipps zum Arbeiten mit und Debuggen von PowerShell-Skripts](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Nach Abschluss des Skripts ist der Katalog vorhanden, und alle Beispielmandanten sind registriert.

Sehen Sie sich nun die von Ihnen erstellten Ressourcen an.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und durchsuchen Sie die Ressourcengruppen.  Öffnen Sie die Ressourcengruppe **wingtip-sa-catalog-\<user\>** , und notieren Sie sich den Katalogserver und die Datenbank.
1. Öffnen Sie die Datenbank im Portal, und wählen Sie im linken Menü die Option *Daten-Explorer* aus.  Klicken Sie auf den Befehl für die Anmeldung, und geben Sie dann das Kennwort = **P\@ssword1** ein.


1. Sehen Sie sich das Schema der Datenbank *tenantcatalog* an.
   * Die Objekte im Schema `__ShardManagement` werden alle von der Clientbibliothek für elastische Datenbanken bereitgestellt.
   * Die Tabelle `Tenants` und die Ansicht `TenantsExtended` sind Erweiterungen, die im Beispiel hinzugefügt wurden, und veranschaulichen, wie Sie den Katalog für zusätzlichen Nutzen erweitern können.
1. Führen Sie die Abfrage `SELECT * FROM dbo.TenantsExtended` aus.

   ![Daten-Explorer](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Als Alternative zur Verwendung des Daten-Explorers können Sie von SQL Server Management Studio aus eine Verbindung mit der Datenbank herstellen. Stellen Sie dazu eine Verbindung mit dem Server „wingtip“ her.


    Beachten Sie, dass Sie Daten nicht direkt im Katalog bearbeiten sollten. Verwenden Sie immer die Shardverwaltung-APIs.

## <a name="provision-a-new-tenant-application"></a>Bereitstellen einer neuen Mandantenanwendung

In dieser Aufgabe erfahren Sie, wie Sie eine einzelne Mandantenanwendung bereitstellen. In diesem Tutorial führen Sie folgende Schritte aus:

* **Erstellen einer neuen Ressourcengruppe** für den Mandanten.
* **Bereitstellen der Anwendung und der Datenbank** in der neuen Ressourcengruppe mithilfe einer Azure Resource Manager-Vorlage.  Diese Aktion umfasst das Initialisieren der Datenbank mit einem gemeinsamen Schema und das Verweisen auf Daten durch Importieren einer BACPAC-Datei.
* **Initialisieren der Datenbank mit grundlegenden Mandanteninformationen** . Diese Aktion umfasst das Angeben des Veranstaltungsorttyps, durch den bestimmt wird, welches Foto als Hintergrund auf der Website mit den jeweiligen Veranstaltungen verwendet wird.
* **Registrieren der Datenbank in der Katalogdatenbank** .

1. Öffnen Sie in PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* , und legen Sie **$Scenario = 2** fest. Stellen Sie den Mandantenkatalog bereit, und registrieren Sie die vordefinierten Mandanten.

1. Fügen Sie einen Haltepunkt im Skript hinzu, indem Sie den Cursor an eine beliebige Stelle in Zeile 49 mit `& $PSScriptRoot\New-TenantApp.ps1` setzen und dann **F9** drücken.
1. Führen Sie das Skript durch Drücken von **F5** aus.
1.  Nachdem die Skriptausführung am Haltepunkt beendet wurde, drücken Sie **F11** , um das Skript „New-Catalog.ps1“ schrittweise auszuführen.
1.  Verfolgen Sie die Ausführung des Skripts mit den Optionen (F10 und F11) im Menü „Debug“ nach, um aufgerufene Funktionen zu überspringen oder einzeln auszuführen.

Nachdem der Mandant bereitgestellt wurde, wird die Website mit den Veranstaltungen des neuen Mandanten geöffnet.

   ![Red Maple Racing](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Anschließend können Sie die neuen Ressourcen überprüfen, die im Azure-Portal erstellt wurden.

   ![Red Maple Racing-Ressourcen](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Löschen von Ressourcengruppen zum Beenden der Abrechnung

Nachdem Sie das Beispiel untersucht haben, löschen Sie alle von Ihnen erstellten Ressourcengruppen, um die zugehörige Abrechnung zu beenden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Weitere Informationen zu mehrinstanzenfähigen SaaS-Datenbankanwendungen finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen der eigenständigen SaaS-Anwendung Wingtip Tickets
> * Informationen zu Servern und Datenbanken, aus denen sich die App zusammensetzt
> * Löschen von Beispielressourcen, um die zugehörige Abrechnung einzustellen

Sie können untersuchen, wie der Katalog verwendet wird, um verschiedene mandantenübergreifende Szenarien mithilfe der Version für eine Datenbank pro Mandant der [Wingtip Tickets SaaS-Anwendung](./saas-dbpertenant-wingtip-app-overview.md) zu unterstützen.