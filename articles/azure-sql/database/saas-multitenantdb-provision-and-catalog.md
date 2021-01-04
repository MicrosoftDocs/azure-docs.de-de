---
title: Bereitstellung in SaaS mit mehreren Mandanten
description: Erfahren Sie, wie Sie neue Mandanten in einer mehrinstanzenfähigen SaaS-App in Azure SQL-Datenbank bereitstellen und katalogisieren können.
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
ms.openlocfilehash: eddb0c8339069025f0742e9bcbc371efbef094ee
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793329"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Bereitstellen und Katalogisieren neuer Mandanten in einer SaaS-Anwendung unter Verwendung einer mehrinstanzenfähigen Azure SQL-Datenbank-Instanz mit Sharding
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel behandelt die Bereitstellung und Katalogisierung neuer Mandanten in einem Modell oder Muster für *mehrinstanzenfähige Datenbanken mit Sharding* .

Dieser Artikel setzt sich aus zwei Hauptteilen zusammen:

- [Erläuterung](#goto_2_conceptual) des Bereitstellungs- und Katalogisierungskonzept für neue Mandanten

- [Tutorial](#goto_1_tutorial) zur Behandlung des PowerShell-Skriptcodes für die Durchführung der Bereitstellung und Katalogisierung
  - Im Tutorial wird die SaaS-Anwendung Wingtip Tickets verwendet, die an das Muster für mehrinstanzenfähige Datenbanken mit Sharding angepasst ist.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Datenbankmuster

In diesem Abschnitt sowie einigen nachfolgenden Abschnitten werden die Konzepte des Musters für mehrinstanzenfähige Datenbanken mit Sharding erläutert.

In diesem mehrinstanzenfähigen Modell mit Sharding enthalten die Tabellenschemas in den einzelnen Datenbanken einen Mandantenschlüssel im primären Schlüssel für Tabellen, die Mandantendaten speichern. Mit dem Mandantenschlüssel können die einzelnen Datenbanken optional beliebig viele Mandanten speichern. Die Verwendung von Datenbanken mit Sharding ermöglicht eine bessere Unterstützung einer sehr großen Anzahl von Mandanten im Anwendungssystem. Alle Daten für einen Mandanten werden in einer Datenbank gespeichert. Die Vielzahl der Mandanten wird auf die zahlreichen Datenbanken mit Sharding verteilt. Eine Katalogdatenbank speichert die Zuordnung der einzelnen Mandanten zur Datenbank.

#### <a name="isolation-versus-lower-cost"></a>Isolation im Vergleich zu Kostenvorteilen

Ein Mandant, dem eine ganze Datenbank zur Verfügung steht, profitiert von den Vorteilen einer Isolation. So kann der Mandant die Datenbank zu einem früheren Zeitpunkt wiederherstellen lassen, ohne durch die Auswirkungen auf andere Mandanten eingeschränkt zu werden. Die Datenbankleistung kann für diesen Mandanten optimiert werden, ohne dass andere Mandanten hierdurch beeinträchtigt werden. Problematisch daran ist lediglich der Umstand, dass die Isolation kostspieliger ist als die gemeinsame Nutzung einer Datenbank mit anderen Mandanten.

Wird ein neuer Mandant bereitgestellt, so kann dieser eine Datenbank gemeinsam mit anderen Mandanten nutzen, oder ihm kann eine eigene neue Datenbank zugeordnet werden. Sie können dies jederzeit ändern und zur jeweils anderen Option wechseln.

Datenbanken mit mehreren Mandanten und einzelnen Mandanten können in ein und derselben SaaS-Anwendung kombiniert werden, um Kosten oder Isolierung für jeden Mandanten zu optimieren.

   ![Mehrinstanzenfähige Datenbanken-App mit Sharding mit Mandantenkatalog](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Mandantenkatalogmuster

Bei mindestens zwei Datenbanken mit jeweils mindestens einem Mandanten muss die Anwendung über die Möglichkeit verfügen, zu ermitteln, welche Datenbank den jeweils gewünschten Mandanten speichert. Diese Zuordnung wird in einer Katalogdatenbank gespeichert.

#### <a name="tenant-key"></a>Mandantenschlüssel

Für jeden Mandanten kann die Wingtip-Anwendung einen eindeutigen Schlüssel, nämlich den Mandantenschlüssel, ableiten. Die App extrahiert den Mandantennamen aus der URL einer Webseite. Die App erstellt zum Abrufen des Schlüssels einen Hashwert für den Namen. Die App greift mithilfe des Schlüssels auf den Katalog zu. Der Katalog stellt mithilfe von Querverweisen eine Verknüpfung für Informationen zu der Datenbank her, in der der Mandant gespeichert ist. Die App stellt anhand der Datenbankinformationen eine Verbindung her. Andere Mandantenschlüsselschemas können auch verwendet werden.

Die Verwendung eines Katalogs ermöglicht es, den Namen oder Speicherort einer Mandantendatenbank nach der Bereitstellung zu ändern, ohne die Anwendung zu stören. In einem mehrinstanzenfähigen Datenbankmodell kann ein Mandant im Katalog zwischen Datenbanken verschoben werden.

#### <a name="tenant-metadata-beyond-location"></a>Mandantenmetadaten nach Speicherort

Darüber hinaus kann der Katalog angeben, ob ein Mandant offline ist (beispielsweise zur Wartung). Zudem kann der Katalog erweitert werden, um zusätzliche Mandanten- oder Datenbankmetadaten wie etwa folgende Elemente zu speichern:
- Die Dienstebene oder die Edition einer Datenbank
- Die Version des Datenbankschemas.
- Den Namen des Mandanten und seiner SLA (Vereinbarung zum Servicelevel)
- Informationen zu Anwendungsverwaltung, Kundensupport oder DevOps-Prozessen

Der Katalog kann auch verwendet werden, um eine mandantenübergreifende Berichterstellung, Schemaverwaltung und Datenextraktion für Analysezwecke zu ermöglichen.

### <a name="elastic-database-client-library"></a>Clientbibliothek für elastische Datenbanken

In Wingtip wird der Katalog in der *tenantcatalog* -Datenbank implementiert. Die *tenantcatalog* -Datenbank wird mithilfe der Shardverwaltungsfeatures der [Clientbibliothek für elastische Datenbanken (Elastic Database Client Library, EDCL)](elastic-database-client-library.md) erstellt. Die Bibliothek ermöglicht einer Anwendung die Erstellung, Verwaltung und Verwendung einer *Shardzuordnung* , die in einer Datenbank gespeichert wird. Eine Shardzuordnung stellt mithilfe von Querverweisen eine Verknüpfung zwischen dem Mandantenschlüssel und dem Shard her, d.h. der Datenbank mit Sharding.

Bei der Mandantenbereitstellung können EDCL-Funktionen in Anwendungen oder PowerShell-Skripts verwendet werden, um Einträge in der Shardzuordnung zu erstellen. Später kann mithilfe der EDCL-Funktionen eine Verbindung mit der richtigen Datenbank hergestellt werden. Die EDCL speichert Verbindungsinformationen zwischen, um den Datenverkehr für die Katalogdatenbank zu minimieren und den Vorgang zum Herstellen einer Verbindung zu beschleunigen.

> [!IMPORTANT]
> Bearbeiten Sie die Daten in der Katalogdatenbank *nicht* durch direkten Zugriff. Direkte Updates werden aufgrund des hohen Risikos einer Datenbeschädigung nicht unterstützt. Bearbeiten Sie die Zuordnungsdaten stattdessen ausschließlich über EDCL-APIs.

## <a name="tenant-provisioning-pattern"></a>Muster der Bereitstellung von Mandanten

#### <a name="checklist"></a>Checkliste

Wenn Sie einen neuen Mandanten in einer vorhandenen gemeinsam genutzten Datenbank bereitstellen möchten, müssen Sie sich folgende Fragen zur gemeinsam genutzten Datenbank stellen:
- Ist ausreichend Speicherplatz für den neuen Mandanten verfügbar?
- Enthält die Datenbank Tabellen mit den erforderlichen Verweisdaten für den neuen Mandanten, oder können die Daten hinzugefügt werden?
- Weist sie die entsprechende Version des Basisschemas für den neuen Mandanten auf?
- Befindet sie sich am entsprechenden geografischen Standort in der Nähe des neuen Mandanten?
- Weist sie die richtige Dienstebene für den neuen Mandanten auf?

Wenn der neue Mandant in der eigenen Datenbank isoliert werden soll, können Sie eine entsprechende Datenbank gemäß den Spezifikation des Mandanten erstellen.

Nach Abschluss der Bereitstellung müssen Sie den Mandanten im Katalog registrieren. Schließlich kann die Mandantenzuordnung hinzugefügt werden, um auf den entsprechenden Shard zu verweisen.

#### <a name="template-database"></a>Vorlagendatenbank

Stellen Sie die Datenbank bereit, indem Sie SQL-Skripts ausführen, eine BACPAC-Datei einsetzen oder eine Vorlagendatenbank kopieren. Die Wingtip-Apps kopieren eine Vorlagendatenbank, um neue Mandantendatenbanken zu erstellen.

Wie jede Anwendung entwickelt sich Wingtip im Laufe der Zeit weiter. In einigen Fällen erfordert Wingtip Änderungen an der Datenbank. Hierzu zählen möglicherweise Änderungen an Folgendem:
- Neues oder geändertes Schema
- Neue oder geänderte Verweisdaten
- Routinemäßige Datenbankwartungstasks zur Sicherstellung einer optimalen Leistung

Bei einer SaaS-Anwendung müssen diese Änderungen u.U. koordiniert für eine große Anzahl von Mandantendatenbanken bereitgestellt werden. Damit diese Änderungen in künftigen Mandantendatenbanken angewendet werden, müssen sie in den Bereitstellungsprozess eingeschlossen werden. Diese Herausforderung wird im Tutorial zum [Verwalten von Schemas](saas-tenancy-schema-management.md) eingehender behandelt.

#### <a name="scripts"></a>Skripts

Die Mandantenbereitstellungsskripts in diesem Tutorial unterstützen beide folgende Szenarien:
- Bereitstellung eines Mandanten für eine vorhandene gemeinsam mit anderen Mandanten genutzte Datenbank
- Bereitstellung eines Mandanten in seiner eigenen Datenbank

Mandantendaten werden anschließend initialisiert und in der Shardzuordnung im Katalog registriert. In der Beispiel-App wird Datenbanken mit mehreren Mandanten ein generischer Name wie *tenants1* oder *tenants2* zugewiesen. Datenbanken mit einem einzelnen Mandanten werden nach dem Mandanten benannt. Die spezifischen Benennungskonventionen, die im Beispiel verwendet werden, sind kein kritischer Teil des Musters, da die Verwendung eines Katalogs es ermöglicht, der Datenbank einen beliebigen Namen zuzuweisen.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Tutorial: Erste Schritte

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen eines Mandanten in einer mehrinstanzfähigen Datenbank
> * Bereitstellen eines Mandanten in einer Datenbank mit nur einem Mandanten
> * Bereitstellen einer Gruppe von Mandanten in einer mehrinstanzfähigen Datenbank und in einer Datenbank mit nur einem Mandanten
> * Registrieren einer Datenbank und Mandantenzuordnung in einem Katalog

#### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Azure PowerShell wurde installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

- Die App „Wingtip Tickets SaaS Multi-tenant Database“ wurde bereitgestellt. Unter [Bereitstellen und Kennenlernen der App „Wingtip Tickets SaaS Multi-tenant Database“](./saas-multitenantdb-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.

- Rufen Sie die Wingtip-Skripts und den Quellcode ab:
    - Die Skripts und der Anwendungsquellcode der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbank stehen im GitHub-Repository [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) zur Verfügung.
    - Die Schritte zum Herunterladen und Entsperren der Wingtip-Skripts finden Sie im [allgemeinen Leitfaden](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Bereitstellen eines Mandanten in einer mit anderen Mandanten *gemeinsam genutzten* Datenbank

In diesem Abschnitt wird eine Liste wichtiger Aktionen für die Bereitstellung aufgeführt, die von den PowerShell-Skripts ausgeführt werden. Anschließend durchlaufen Sie mithilfe des PowerShell ISE-Debuggers schrittweise die Skripts, um sich mit den Aktionen im Code vertraut zu machen.

#### <a name="major-actions-of-provisioning"></a>Wichtige Aktionen für die Bereitstellung

Im Folgenden finden Sie die wichtigsten Elemente des Bereitstellungsworkflows, den Sie durchlaufen:

- **Berechnen Sie den neuen Mandantenschlüssel** : Eine Hashfunktion wird verwendet, um den Mandantenschlüssel aus dem Mandantennamen zu erstellen.
- **Überprüfen Sie, ob der Mandantenschlüssel bereits vorhanden ist** : Der Katalog wird überprüft, um sicherzustellen, dass der Schlüssel nicht bereits registriert wurde.
- **Initialisieren Sie den Mandanten in der Standarddatenbank des Mandanten** : Die Mandantendatenbank wird mit den hinzugefügten neuen Informationen des Mandanten aktualisiert.
- **Registrieren Sie den Mandanten im Katalog** : Die Zuordnung zwischen dem neuen Mandantenschlüssel und der bestehenden Datenbank „tenants1“ wird dem Katalog hinzugefügt.
- **Fügen Sie den Namen des Mandanten einer Erweiterungstabelle des Katalogs hinzu** : Der Name des Veranstaltungsorts wird der Tabelle „Tenants“ (Mandanten) im Katalog hinzugefügt.  Dieser Zusatz veranschaulicht, wie die Katalogdatenbank erweitert werden kann, um zusätzliche anwendungsspezifische Daten zu unterstützen.
- **Öffnen Sie die Seite „Events“ (Veranstaltungen) für den neuen Mandanten** : Die zu *Bushwillow Blues* gehörige Veranstaltungsseite wird im Browser geöffnet.

   ![Screenshot, der die Seite „Ereignisse“ für einen neuen Mandanten zeigt.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Schritte des Debuggers

Um nachzuvollziehen, wie die Wingtip-App die Bereitstellung neuer Mandanten in einer gemeinsam genutzten Datenbank implementiert, fügen Sie einen Haltepunkt hinzu und durchlaufen den Workflow:

1. Öffnen Sie in *PowerShell ISE* „...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* “, und legen Sie die folgenden Parameter fest:
   - **$TenantName** = **Bushwillow Blues** , der Name eines neuen Veranstaltungsorts
   - **$VenueType** = **blues** , einer der vordefinierten Veranstaltungsorttypen: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (in Kleinbuchstaben ohne Leerzeichen)
   - **$DemoScenario** = **1** , Bereitstellen eines Mandanten in einer mit anderen Mandanten gemeinsam genutzten Datenbank

2. Fügen Sie einen Haltepunkt hinzu, indem Sie den Cursor an eine beliebige Stelle in Zeile 38 bewegen. Es handelt sich um die Zeile, die folgendermaßen lautet: *New-Tenant `* . Drücken Sie dann **F9** .

   ![Screenshot, der die Zeile hervorhebt, die „Neuer Mandant“ enthält.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Führen Sie das Skript durch Drücken von **F5** aus.

4. Nachdem die Skriptausführung am Haltepunkt beendet wurde, drücken Sie **F11** , um den Code schrittweise auszuführen.

   ![Screenshot: Windows PowerShell-ISE mit geöffnetem Menü „Debuggen“ und ausgewählter Option „Schrittweise ausführen“.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Verfolgen Sie die Ausführung des Skripts mit den Optionen ( **F10** und **F11** ) im Menü **Debug** nach, um aufgerufene Funktionen zu überspringen oder einzeln auszuführen.

Weitere Informationen zum Debuggen von PowerShell-Skripts finden Sie unter [Tipps zum Arbeiten mit und Debuggen von PowerShell-Skripts](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Bereitstellen eines Mandanten in seiner *eigenen* Datenbank

#### <a name="major-actions-of-provisioning"></a>Wichtige Aktionen für die Bereitstellung

Im Folgenden finden Sie die wichtigsten Elemente des Workflows, den Sie beim Nachverfolgen des Skripts durchlaufen:

- **Berechnen Sie den neuen Mandantenschlüssel** : Eine Hashfunktion wird verwendet, um den Mandantenschlüssel aus dem Mandantennamen zu erstellen.
- **Überprüfen Sie, ob der Mandantenschlüssel bereits vorhanden ist** : Der Katalog wird überprüft, um sicherzustellen, dass der Schlüssel nicht bereits registriert wurde.
- **Erstellen Sie eine neue Mandantendatenbank** : Die Datenbank wird durch Kopieren der Datenbank *basetenantdb* unter Verwendung einer Ressourcen-Manager-Vorlage erstellt.  Der Name der neuen Datenbank basiert auf dem Mandantennamen.
- **Fügen Sie die Datenbank dem Katalog hinzu** : Die neue Mandantendatenbank wird als Shard im Katalog registriert.
- **Initialisieren Sie den Mandanten in der Standarddatenbank des Mandanten** : Die Mandantendatenbank wird mit den hinzugefügten neuen Informationen des Mandanten aktualisiert.
- **Registrieren Sie den Mandanten im Katalog** : Die Zuordnung zwischen dem neuen Mandantenschlüssel und der *sequoiasoccer* -Datenbank wird dem Katalog hinzugefügt.
- **Der Mandantenname wird dem Katalog hinzugefügt** : Der Name des Veranstaltungsorts wird der Erweiterungstabelle „Tenants“ (Mandanten) im Katalog hinzugefügt.
- **Öffnen Sie die Seite „Events“ (Veranstaltungen) für den neuen Mandanten** : Die zu *Sequoia Soccer* gehörige Veranstaltungsseite wird im Browser geöffnet.

   ![events](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Schritte des Debuggers

Durchlaufen Sie nun den Skriptprozess zum Erstellen eines Mandanten in seiner eigenen Datenbank:

1. Legen Sie noch in „\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* “ die folgenden Parameter fest:
   - **$TenantName** = **Sequoia Soccer** , der Name eines neuen Veranstaltungsorts
   - **$VenueType** = **soccer** , einer der vordefinierten Veranstaltungsorttypen: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (Kleinschreibung, keine Leerzeichen)
   - **$DemoScenario** = **2** , Bereitstellen eines Mandanten in seiner eigenen Datenbank

2. Fügen Sie einen neuen Haltepunkt hinzu, indem Sie den Cursor an eine beliebige Stelle in Zeile 57 bewegen (der Zeile mit *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `* ) und **F9** drücken.

   ![Haltepunkt](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Führen Sie das Skript durch Drücken von **F5** aus.

4. Nachdem die Skriptausführung am Haltepunkt beendet wurde, drücken Sie **F11** , um den Code schrittweise auszuführen.  Drücken Sie **F10** und **F11** , um zum Verfolgen der Ausführung Prozedurschritte zu durchlaufen und Funktionen schrittweise aufzurufen.

## <a name="provision-a-batch-of-tenants"></a>Bereitstellen eines Batches von Mandanten

In dieser Übung wird ein Batch mit 17 Mandanten bereitgestellt. Es empfiehlt sich, diese Mandantengruppe bereitzustellen, bevor Sie mit anderen Wingtip-SaaS-Tutorials beginnen, damit Sie mit mehreren Datenbanken arbeiten können.

1. Öffnen Sie in *PowerShell ISE* „...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* “, und ändern Sie den *$DemoScenario* -Parameter in „4“:
   - **$DemoScenario** = **4** , Bereitstellen einer Gruppe von Mandanten in einer gemeinsam genutzten Datenbank

2. Drücken Sie **F5** , um das Skript auszuführen.

### <a name="verify-the-deployed-set-of-tenants"></a>Überprüfen der bereitgestellten Gruppe von Mandanten

In dieser Phase haben Sie eine Kombination von Mandanten, die in einer gemeinsam genutzten Datenbank bereitgestellt wurden, und Mandanten, die in ihren eigenen Datenbanken bereitgestellt wurden. Im Azure-Portal können die erstellten Datenbanken überprüft werden. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Server **tenants1-mt-\<USER\>** , indem Sie zur Liste der SQL-Server navigieren.  Die Liste **SQL-Datenbanken** sollte die gemeinsame genutzte Datenbank **tenants1** und die Datenbanken für die Mandanten mit eigener Datenbank enthalten:

   ![Datenbankliste](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

Sie können zwar im Azure-Portal die Mandantendatenbanken sehen, aber nicht die Mandanten *innerhalb* der gemeinsam genutzten Datenbank einsehen. Die vollständige Liste der Mandanten sehen Sie auf der Wingtip-Website **Events Hub** (Veranstaltungshub) und bei Durchsuchen des Katalogs.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Verwenden der Wingtip Tickets-Seite zum Veranstaltungshub

Öffnen Sie die Seite „Event Hub“ im Browser (http:events.wingtip-mt.\<USER\>.trafficmanager.net).

#### <a name="using-catalog-database"></a>Verwenden der Katalogdatenbank

Die vollständige Liste der Mandanten und ihre entsprechende Datenbank sind im Katalog verfügbar. Es wird eine SQL-Ansicht bereitgestellt, die den Mandantennamen mit dem Datenbanknamen verknüpft. Die Ansicht veranschaulicht eindrucksvoll, wie nützlich das Erweitern der im Katalog gespeicherten Metadaten ist.
- Die SQL-Ansicht ist in der tenantcatalog-Datenbank verfügbar.
- Der Mandantenname wird in der Mandantentabelle gespeichert.
- Der Datenbankname wird in den Tabellen zur Shardverwaltung gespeichert.

1. Stellen Sie in SQL Server Management Studio (SSMS) mit dem Mandantenserver unter **catalog-mt.\<USER\>.database.windows.net** mit dem Anmeldenamen **developer** und dem Kennwort **P\@ssword1** eine Verbindung her.

    ![SSMS-Verbindungsdialogfeld](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Navigieren Sie im SSMS-Objekt-Explorer zu den Ansichten in der Datenbank *tenantcatalog* .

3. Klicken Sie mit der rechten Maustaste auf die Sicht *TenantsExtended* , und wählen Sie **Select Top 1000 Rows** aus. Beachten Sie die Zuordnung zwischen dem Namen des Mandanten und der Datenbank für die verschiedenen Mandanten.

    ![Sicht „ExtendedTenants“ in SSMS](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Weitere Bereitstellungsmuster

In diesem Abschnitt werden weitere interessante Bereitstellungsmuster erläutert.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Vorabbereitstellung von Datenbanken in Pools für elastische Datenbanken

Das Vorabbereitstellungsmuster profitiert von der Tatsache, dass bei Verwenden von Datenbanken in einem Pool für elastische Datenbanken Kosten für den Pool und nicht für die Datenbanken anfallen. Daher können Datenbanken ohne zusätzliche Kosten einem Pool für elastische Datenbanken hinzugefügt werden, bevor sie benötigt werden. Diese Vorabbereitstellung beschleunigt die Bereitstellung eines Mandanten in einer Datenbank um ein Vielfaches. Die Anzahl der vorab bereitgestellten Datenbanken kann bedarfsorientiert angepasst werden, um einen für die erwartete Bereitstellungsrate geeigneten Puffer bereitzuhalten.

#### <a name="auto-provisioning"></a>Automatische Bereitstellung

Beim Muster der automatischen Bereitstellung wird ein dedizierter Bereitstellungsdienst verwendet, um Server, Pools und Datenbanken bei Bedarf automatisch bereitzustellen. Diese Automatisierung schließt vorab bereitgestellte Datenbanken in Pools für elastische Datenbanken ein. Wenn Datenbanken außer Betrieb genommen und gelöscht werden, können entstehende Lücken in den Pools für elastische Datenbanken durch den Bereitstellungsdienst bedarfsgemäß gefüllt werden.

Diese Art von automatisiertem Dienst kann von einfacher oder komplexer Beschaffenheit sein. Die Bereitstellung kann mit der Automatisierung also beispielsweise über mehrere geografische Regionen erfolgen, und die Georeplikation kann zur Notfallwiederherstellung eingerichtet werden. Beim Muster der automatischen Bereitstellung sendet eine Clientanwendung oder ein Skript eine Bereitstellungsanforderung an eine Warteschlange, damit diese vom Bereitstellungsdienst verarbeitet wird. Anschließend führt das Skript einen Abruf durch, um festzustellen, ob der Vorgang abgeschlossen ist. Wenn Datenbanken vorab bereitgestellt wurden, werden Anforderungen schnell verarbeitet, während ein Hintergrunddienst die Bereitstellung einer Ersatzdatenbank abwickelt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md)
- [So wird's gemacht: Debuggen von Skripts in Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen eines neuen Mandanten in einer gemeinsam genutzten mehrinstanzfähigen Datenbank und in seiner eigenen Datenbank
> * Bereitstellen eines Batches von zusätzlichen Mandanten
> * Kennenlernen der Details zur Bereitstellung von Mandanten und zu deren Registrierung im Katalog

Sehen Sie sich das [Tutorial zur Leistungsüberwachung](./saas-multitenantdb-performance-monitoring.md) an.