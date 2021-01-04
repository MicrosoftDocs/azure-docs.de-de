---
title: Verschieben von Daten zwischen horizontal hochskalierten Clouddatenbanken
description: Hier wird erläutert, wie Sie Shards manipulieren und Daten über einen selbst gehosteten Dienst mithilfe von APIs für elastische Datenbanken verschieben.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5a646ffe1d306d7ea13da002715d5bd9b907107b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793465"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Verschieben von Daten zwischen horizontal hochskalierten Clouddatenbanken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Wenn Sie Software- oder Dienstentwickler sind, und plötzlich eine enorme Nachfrage nach Ihrer App entsteht, müssen Sie das Wachstum anpassen. So fügen Sie weitere Datenbanken (Shards) hinzu. Wie verteilen Sie die Daten ohne Unterbrechung der Datenintegrität neu auf die neuen Datenbanken? Verschieben Sie Daten mit dem **Split-Merge-Tool** aus eingeschränkten Datenbanken in die neuen Datenbanken.  

Das Split-Merge-Tool wird als Azure-Webdienst ausgeführt. Ein Administrator oder Entwickler verwendet das Tool zum Verschieben von Shardlets (Daten aus einem Shard) zwischen verschiedenen Datenbanken (Shards). Das Tool verwendet die Shard-Zuordnungsverwaltung, um die Dienstmetadatendatenbank zu verwalten und konsistente Zuordnungen sicherzustellen.

![Übersicht][1]

## <a name="download"></a>Download

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentation

1. [Tutorial zum Split-Merge-Tool für elastische Datenbanken](elastic-scale-configure-deploy-split-and-merge.md)
2. [Split-Merge-Sicherheitskonfiguration](elastic-scale-split-merge-security-configuration.md)
3. [Split-Merge-Sicherheitsüberlegungen](elastic-scale-split-merge-security-configuration.md)
4. [Shard-Zuordnungsverwaltung](elastic-scale-shard-map-management.md)
5. [Migrieren von vorhandenen Datenbanken zu horizontaler Hochskalierung](elastic-convert-to-use-elastic-tools.md)
6. [Tools für elastische Datenbanken](elastic-scale-introduction.md)
7. [Tools für elastische Datenbanken – Glossar](elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Warum sollten Sie das Split-Merge-Tool verwenden?

- **Flexibilität**

  Anwendungen müssen die Flexibilität über die Grenzen eines Singletons in Azure SQL-Datenbank hinaus ausdehnen. Verwenden Sie das Tool nach Bedarf, um Daten in neue Datenbanken zu verschieben und dabei die Integrität beizubehalten.

- **Teilen, um zu wachsen**

  Wenn die Gesamtkapazität erhöht werden soll, um explosionsartiges Wachstum in den Griff zu bekommen, schaffen Sie zusätzliche Kapazität durch Partitionieren der Daten und deren Verteilung über eine zunehmend größere Anzahl von Datenbanken, bis die Kapazitätsanforderungen erfüllt sind. Dies ist ein hervorragendes Beispiel für das **Split** -Feature.

- **Zusammenführen zum Verkleinern**

  Die Kapazität muss aufgrund saisonaler Schwankungen des Geschäftsbetriebs verkleinert werden. Mit dem Tool können Sie auf eine geringere Anzahl Einheiten zentral herunterskalieren, wenn das Geschäft nachlässt. Die Funktion „merge“ des Split-Merge-Diensts von Elastic Scale geht auf diese Anforderung ein.

- **Verwalten von Hotspots durch Verschieben von Shardlets**

  Wenn mehrere Mandanten in einer Datenbank verwaltet werden, kann die Zuweisung von Shardlets zu Shards bei einigen Shards zu Kapazitätsengpässen führen. Dann müssen Shards neu zugewiesen oder stark in Anspruch genommene Shardlets in neue oder weniger genutzte Shards verschoben werden.

## <a name="concepts--key-features"></a>Konzepte und wichtige Features

- **Von Kunden gehostete Dienste**

  Split-Merge wird als vom Kunden gehosteter Dienst bereitgestellt. Sie müssen den Dienst in Ihrem Microsoft Azure-Abonnement bereitstellen und hosten. Das Paket, das Sie von NuGet herunterladen, enthält eine Vorlage, die mit den Informationen für Ihre Bereitstellung ausgefüllt werden muss. Weitere Informationen finden Sie im [Split-Merge-Tutorial](elastic-scale-configure-deploy-split-and-merge.md) . Da der Dienst im Azure-Abonnement ausgeführt wird, können Sie die meisten Sicherheitsaspekte des Diensts steuern und konfigurieren. Die Standardvorlage enthält die Optionen zum Konfigurieren von TLS, zertifikatbasierter Clientauthentifizierung, Verschlüsselung für gespeicherte Anmeldeinformationen, DoS-Schutz und IP-Einschränkungen. Weitere Informationen zu Sicherheitsaspekten finden Sie im folgenden Dokument [Split-Merge-Sicherheitskonfiguration](elastic-scale-split-merge-security-configuration.md).

  Der standardmäßig bereitgestellte Dienst wird mit einer Workerrolle und einer Webrolle ausgeführt. Für beide wird die VM-Größe A1 in Azure Cloud Services verwendet. Sie können diese Einstellungen zwar nicht bei der Bereitstellung des Pakets ändern, nach einer erfolgreichen Bereitstellung können die Einstellungen jedoch im ausgeführten Clouddienst (über das Azure-Portal) geändert werden. Beachten Sie, dass die Workerrolle aus technischen Gründen nicht für mehr als eine einzelne Instanz konfiguriert werden darf.

- **Integration in Shardzuordnung**

  Der Split-Merge-Dienst interagiert mit der Shardzuordnung der Anwendung. Wenn der Split-Merge-Dienst zum Teilen oder Zusammenführen von Bereichen oder zum Verschieben von Shardlets zwischen Shards verwendet wird, aktualisiert der Dienst automatisch die Shardzuordnung. Zu diesem Zweck stellt der Dienst eine Verbindung mit der Verwaltungsdatenbank für die Shard-Zuordnung der Anwendung her und verwaltet die Bereiche und Zuordnungen, während Split/Merge/Move-Anforderungen ausgeführt werden. Dadurch wird sichergestellt, dass die Shard-Zuordnung während der Ausführung von Split-Merge-Vorgängen immer eine aktuelle Ansicht zeigt. Split-, Merge- und Move-Vorgänge werden implementiert, indem ein Batch von Shardlets von der Quell-Shard in die Ziel-Shard verschoben wird. Während des Verschiebens werden die Shardlets, die im aktuellen Batch enthalten sind, in der Shard-Zuordnung als offline markiert und stehen für datenabhängige Routingverbindungen mit der **OpenConnectionForKey** -API nicht zur Verfügung.

- **Konsistente Shardletverbindungen**

  Wenn die Datenverschiebung für einen neuen Batch von Shardlets gestartet wird, werden alle von der Shardzuordnung bereitgestellten datenabhängigen Routingverbindungen mit der Shard getrennt, in der das Shardlet gespeichert ist. Nachfolgende Verbindungen von den Shardzuordnungs-APIs mit diesen Shardlets werden beim Ausführen der Datenverschiebung blockiert, um Inkonsistenzen zu vermeiden. Verbindungen mit anderen Shardlets in der gleichen Shard werden auch beendet, werden jedoch bei einem erneuten Versuch sofort hergestellt. Nachdem der Batch verschoben worden ist, werden die Shardlets für die Ziel-Shard wieder als online markiert, und die Quelldaten werden aus der Quell-Shard entfernt. Der Dienst durchläuft diese Schritte für jeden Batch, bis alle Shardlets verschoben worden sind. Dies führt dazu, dass im Lauf eines Split/Merge/Move-Vorgangs mehrmals Verbindungen getrennt werden.  

- **Verwalten der Shardletverfügbarkeit**

  Durch die Beschränkung der oben beschriebenen Trennung von Verbindungen auf den aktuellen Batch von Shardlets, wird der Bereich der Nichtverfügbarkeit zu einem gegebenen Zeitpunkt jeweils auf eine Gruppe von Shardlets beschränkt. Dieses Vorgehen wird einem Ansatz vorgezogen, bei dem die komplette Shard während des gesamten Split- oder Merge-Vorgangs für alle zugehörigen Shardlets offline bleibt. Die Batchgröße, die als Anzahl unterschiedlicher Shardlets, die gleichzeitig verschoben werden sollen, definiert ist, ist ein Konfigurationsparameter. Sie kann für jeden Split- und Merge-Vorgang von der Verfügbarkeit und den Leistungsanforderungen der Anwendung abhängig definiert werden. Beachten Sie, dass der Bereich, der in der Shard-Zuordnung blockiert wird, unter Umständen größer als die angegebene Batchgröße ist. Dies ist so, weil der Dienst die Bereichsgröße so wählt, dass die tatsächliche Anzahl der Sharding-Schlüsselwerte in den Daten ungefähr der Batchgröße entspricht. Dies ist besonders bei spärlich belegten Sharding-Schlüsseln zu beachten.

- **Metadatenspeicherung**

  Der Split-Merge-Dienst verwendet eine Datenbank, um seinen Status zu verwalten und Protokolle während der Anforderungsverarbeitung zu speichern. Der Benutzer erstellt diese Datenbank in seinem Abonnement und stellt die zugehörige Verbindungszeichenfolge in der Konfigurationsdatei für die Dienstbereitstellung bereit. Administratoren der Organisation des Benutzers können auch eine Verbindung mit dieser Datenbank herstellen, um die Bearbeitung der Anforderung zu überprüfen und detaillierte Informationen zu möglichen Fehlern zu untersuchen.

- **Sharding-Unterstützung**

  Der Split-Merge-Dienst unterscheidet zwischen (1) partitionierten Tabellen, (2) Verweistabellen und (3) normalen Tabellen. Die Semantik eines Split/Merge/Move-Vorgangs hängt vom Typ der verwendeten Tabelle ab und wird wie folgt definiert:

  - **Shardtabellen**

    Mit Split-, Merge- und Move-Vorgängen werden Shardlets vom Quell- zum Ziel-Shard verschoben. Nach dem erfolgreichen Abschluss der gesamten Anforderung sind diese Shardlets nicht mehr in der Quelle vorhanden. Beachten Sie, dass die Zieltabellen in der Ziel-Shard vorhanden sein müssen und vor der Verarbeitung des Vorgangs keine Daten im Zielbereich enthalten dürfen.

  - **Verweistabellen**

    Bei Verweistabellen werden die Daten mit Split-, Merge- und Move-Vorgängen aus dem Quell- in den Ziel-Shard kopiert. Beachten Sie jedoch, dass die Ziel-Shard für eine bestimmte Tabelle nicht geändert wird, wenn in der Ziel-Shard in dieser Tabelle bereits Zeilen vorhanden sind. Die Tabelle muss leer sein, damit Kopiervorgänge aus der Verweistabelle verarbeitet werden.

  - **Andere Tabellen**

    Andere Tabellen können für die Quelle oder das Ziel eines Split- und Merge-Vorgangs vorhanden sein. Der Split-Merge-Dienst ignoriert diese Tabellen bei Datenverschiebungen oder Kopiervorgängen. Beachten Sie jedoch, dass sie diese Vorgänge bei Einschränkungen beeinträchtigen können.

    Die Informationen zu Verweistabellen und Shardtabellen werden von den `SchemaInfo`-APIs für die Shardzuordnung bereitgestellt. Das folgende Beispiel veranschaulicht die Verwendung dieser APIs anhand eines bestimmten Shardzuordnungs-Manager-Objekts:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    Die Tabellen "region" und "nation" sind als Verweistabellen definiert und werden mit Split/Merge/Move-Vorgängen kopiert. "customer" und "orders" sind dagegen als partitionierte Tabellen definiert. `C_CUSTKEY` und `O_CUSTKEY` dienen als Shardingschlüssel.

- **Referenzielle Integrität**

  Der Split-Merge-Dienst analysiert Abhängigkeiten zwischen Tabellen und verwendet Fremdschlüssel und Primärschlüsselbeziehungen, um die Vorgänge für das Verschieben von Verweistabellen und Shardlets bereitzustellen. Im Allgemeinen werden zuerst die Verweistabellen in der Reihenfolge ihrer Abhängigkeit kopiert, und dann werden die Shardlets in der Reihenfolge ihrer Abhängigkeiten innerhalb der einzelnen Batches kopiert. Dies ist erforderlich, damit Fremdschlüssel-Primärschlüssel-Einschränkungen für die Ziel-Shard berücksichtigt werden, wenn neue Daten eingehen.

- **Konsistenz und Fertigstellung der Shardzuordnung**

  Bei Fehlern nimmt der Split-Merge-Dienst die Vorgänge nach einem Ausfall wieder auf und versucht, die aktuell ausstehenden Anforderungen abzuschließen. Allerdings können nicht behebbare Fehler auftreten, z. B. wenn die Ziel-Shard verloren geht oder irreparabel beschädigt wird. Unter diesen Umständen können sich einige Shardlets, die verschoben werden sollten, weiterhin in der Quell-Shard befinden. Der Dienst stellt sicher, dass Shardlet-Zuordnungen erst aktualisiert werden, nachdem die erforderlichen Daten erfolgreich in das Ziel kopiert wurden. Shardlets werden erst dann in der Quelle gelöscht, nachdem alle Daten in das Ziel kopiert und die zugehörigen Zuordnungen erfolgreich aktualisiert wurden. Der Löschvorgang erfolgt im Hintergrund, während der Bereich bereits in der Ziel-Shard online ist. Der Split-Merge-Dienst stellt immer die Richtigkeit der in der Shard-Zuordnung gespeicherten Zuordnungen sicher.

## <a name="the-split-merge-user-interface"></a>Split-Merge-Benutzeroberfläche

Das Split-Merge-Dienstpaket enthält eine Workerrolle und eine Webrolle. Mit der Webrolle werden Split-Merge-Anforderungen interaktiv gesendet. Die Benutzeroberfläche enthält die folgenden Hauptkomponenten:

- **Vorgangstyp**

  Dies ist ein Optionsfeld, das die Art des Vorgangs steuert, der vom Dienst für diese Anforderung ausgeführt wird. Sie können zwischen Teilungs-, Zusammenführungs- und Verschiebungsszenarien wählen. Sie können auch einen zuvor gesendeten Vorgang abbrechen. Sie können Anforderungen für Shard-Zuordnungen teilen, zusammenführen und verschieben. Die Liste der Shard-Zuordnungen unterstützt nur Verschiebevorgänge.

- **Shardzuordnung**

  Der nächste Abschnitt von Anforderungsparametern enthält Informationen zu der Shardzuordnung und der Datenbank, in der Ihre Shardzuordnung gehostet wird. Sie müssen den Namen des Servers und der Datenbank, in der die Shardzuordnung gehostet wird, die Anmeldeinformationen zum Herstellen einer Verbindung mit der Shardzuordnungsdatenbank und schließlich den Namen der Shardzuordnung angeben. Der Vorgang akzeptiert momentan nur einen einzigen Satz von Anmeldeinformationen. Diese Anmeldeinformationen müssen über ausreichende Berechtigungen verfügen, um die Shard-Zuordnung und die Benutzerdaten in den Shards ändern zu können.

- **Quellbereich (Split und Merge)**

  Ein Split- und Merge-Vorgang verarbeitet einen Bereich mithilfe seines niedrigen und hohen Schlüssels. Um einen Vorgang mit einem unbegrenzt hohen Schlüsselwert anzugeben, aktivieren Sie das Kontrollkästchen "High key is max", und lassen Sie das Feld für den hohen Schlüssel leer. Die von Ihnen angegebenen Bereichsschlüsselwerte müssen nicht genau mit einer Zuordnung und ihren Grenzen in Ihrer Shard-Zuordnung übereinstimmen. Wenn Sie keine Bereichsgrenzen angeben, leitet der Dienst den nächsten Bereich automatisch für Sie ab. Sie können mit dem PowerShell-Skript GetMappings.ps1 die aktuellen Zuordnungen aus einer gegebenen Shard-Zuordnungen abrufen.

- **Quellenverhalten (Split)**

  Definieren Sie bei Split-Vorgängen, an welcher Stelle der Quellbereich geteilt werden soll. Hierzu geben Sie den Sharding-Schlüssel an, an dem die Aufteilung erfolgen soll. Definieren sie mit dem Optionsfeld, ob der untere Teil des Bereichs (exklusive Aufteilungsschlüssel) oder der obere Teil (inklusive Aufteilungsschlüssel) verschoben werden soll.

- **Quellshardlet (Move)**

  Move-Vorgänge unterscheiden sich von Split- oder Merge-Vorgängen, da hier kein Bereich zum Beschreiben der Quelle angegeben werden muss. Die Quelle für das Verschieben wird einfach durch den Sharding-Schlüsselwert identifiziert, der verschoben werden soll.

- **Zielshard (Split)**

  Nachdem Sie die Informationen zur Quelle des Split-Vorgangs angegeben haben, müssen Sie definieren, wohin die Daten kopiert werden sollen, indem Sie den Server und den Datenbanknamen des Ziels angeben.

- **Zielbereich (Merge)**

  Merge-Vorgänge verschieben Shardlets in einen vorhandenen Shard. Sie identifizieren die vorhandene Shard durch Angabe der Bereichsgrenzen des vorhandenen Bereichs, mit dem zusammengeführt werden soll.

- **Batchgröße**

  Die Batchgröße steuert die Anzahl der Shardlets, die während der Datenverschiebung gleichzeitig offline geschaltet werden. Dies ist ein ganzzahliger Wert, wobei Sie kleinere Werte angeben können, wenn Sie lange Ausfallzeiten für Shardlets vermeiden möchten. Bei Angabe größerer Werte vergrößert sich der Zeitraum, über den ein bestimmtes Shardlet offline ist, die Leistung kann dadurch jedoch verbessert werden.

- **Vorgangs-ID (Abbrechen)**

  Wenn Sie einen laufenden Vorgang nicht mehr benötigen, können Sie ihn abbrechen, indem Sie in diesem Feld die Vorgangs-ID angeben. Sie können die Vorgangs-ID der Anforderungsstatustabelle (siehe Abschnitt 8.1) oder der Ausgabe in dem Webbrowser entnehmen, mit dem Sie die Anforderung übermittelt haben.

## <a name="requirements-and-limitations"></a>Anforderungen und Einschränkungen

Die aktuelle Implementierung des Split-Merge-Diensts unterliegt den folgenden Anforderungen und Einschränkungen:

- Die Shards müssen vorhanden und in der Shardzuordnung registriert sein, damit ein Split-Merge-Vorgang für diese Shards ausgeführt werden kann.
- Der Dienst erstellt derzeit nicht automatisch Tabellen oder andere Datenbankobjekte im Rahmen der Vorgänge. Dies bedeutet, dass das Schema für alle Shardtabellen und Verweistabellen im Zielshard vorhanden sein muss, bevor ein Teilungs-/Zusammenführungs-/Verschiebevorgang ausgeführt werden kann. Sharding-Tabellen müssen insbesondere in dem Bereich leer sein, in dem die neuen Shardlets durch einen Split/Merge/Move-Vorgang hinzugefügt werden sollen. Andernfalls schlägt die anfänglichen Konsistenzprüfung der Ziel-Shard für den Vorgang fehl. Beachten Sie außerdem, dass Verweisdaten nur kopiert werden, wenn die Verweistabelle leer ist, und dass die Konsistenz in Bezug auf andere gleichzeitige Schreibvorgänge in den Verweistabellen nicht garantiert wird. Es wird empfohlen, dass beim Ausführen von Split/Merge-Vorgängen keine anderen Schreibvorgänge Änderungen an den Verweistabellen vornehmen.
- Der Dienst verwendet die Zeilenidentität, die durch einen eindeutigen Index oder Schlüssel mit dem Shardingschlüssel gegeben ist, zur Verbesserung der Leistung und Zuverlässigkeit für große Shardlets. Dadurch kann der Dienst Daten sogar in noch feineren Unterteilungen als den Sharding-Schlüsselwert verschieben. Dadurch lassen sich die maximale Größe des für Protokolle erforderlichen Speicherplatzes und die Anzahl von Sperren verringern, die während des Vorgangs erforderlich sind. Ziehen Sie das Erstellen eines eindeutigen Index oder eines Primärschlüssels unter Einbeziehung des Sharding-Schlüssels für eine bestimmte Tabelle in Betracht, wenn Sie diese Tabelle mit Split/Merge/Move-Anforderungen verwenden möchten. Um die Leistung zu verbessern, sollte der Sharding-Schlüssel die führende Spalte im Schlüssel bzw. Index bilden.
- Während der Verarbeitung der Anforderung können in Quell- und Ziel-Shard Shardlet-Daten vorhanden sein. Dies ist zum Schutz vor Ausfällen während der Shardletverschiebung erforderlich. Durch die Integration von Split-Merge in die Shardzuordnung wird sichergestellt, dass in Verbindungen, die mit der **OpenConnectionForKey** -Methode über die APIs für datenabhängiges Routing für die Shardzuordnung hergestellt werden, keine inkonsistenten Zwischenzustände auftreten. Wenn eine Verbindung mit dem Quell- oder Ziel-Shard allerdings nicht unter Verwendung der **OpenConnectionForKey** -Methode hergestellt wird, können während der Ausführung von Split/Merge/Move-Anforderungen inkonsistente Zwischenzustände auftreten. Abhängig vom Zeitpunkt oder der Shard, die der Verbindung zugrunde liegt, können in diesen Verbindungen unvollständige oder doppelte Ergebnisse angezeigt werden. Diese Einschränkung betrifft derzeit auch die Verbindungen, die durch Elastic Scale Multi-Shard-Abfragen hergestellt werden.
- Die Metadatendatenbank für den Split-Merge-Dienst darf nicht von verschiedenen Rollen gemeinsam verwendet werden. Beispielsweise muss eine Rolle des Split-Merge-Diensts, die in der Staging-Umgebung ausgeführt wird, auf eine andere Metadatendatenbank als die Produktionsrolle verweisen.

## <a name="billing"></a>Abrechnung

Der Split-Merge-Dienst wird als Clouddienst in Ihrem Microsoft Azure-Abonnement ausgeführt. Daher gelten Gebühren für Clouddienste für Ihre Instanz des Diensts. Sofern Sie nicht häufig Split/Merge/Move-Vorgänge ausführen, wird empfohlen, den Split-Merge-Clouddienst zu löschen. Dadurch werden Kosten für laufende oder bereitgestellte Clouddienstinstanzen gespart. Sie können ihn erneut bereitstellen und Ihre sofort ausführbare Konfiguration starten, wenn Sie Split- oder Merge-Vorgänge ausführen müssen.

## <a name="monitoring"></a>Überwachung

### <a name="status-tables"></a>Statustabellen

Der Split-Merge-Dienst stellt die **RequestStatus** -Tabelle in der Metadatenspeicher-Datenbank zum Überwachen abgeschlossener und laufender Anforderungen bereit. Die Tabelle enthält eine Zeile für jede Split-Merge-Anforderung, die an diese Instanz des Split-Merge-Diensts übermittelt wurde. Sie stellt für jede Anforderung die folgende Informationen bereit:

- **Timestamp**

  Uhrzeit und Datum, die zu dem Zeitpunkt galten, als die Anforderung gestartet wurde.

- **OperationId**

  Eine GUID, die die Anforderung eindeutig identifiziert. Diese Anforderung kann auch verwendet werden, um den Vorgang während der Ausführung abzubrechen.

- **Status**

  Der aktuelle Status der Anforderung. Bei laufenden Anforderungen wird auch die aktuelle Phase der Anforderung angegeben.

- **CancelRequest**

  Ein Flag, das angibt, ob die Anforderung abgebrochen wurde.

- **Progress**

  Ein Prozentwert, der angibt, zu welchem Anteil der Vorgang schätzungsweise fertiggestellt ist. Der Wert 50 gibt an, dass der Vorgang etwa zu 50 % abgeschlossen ist.

- **Details**

  Ein XML-Wert, der einen ausführlicheren Statusbericht bereitstellt. Der Statusbericht wird regelmäßig aktualisiert, wenn Zeilen aus der Quelle in das Ziel kopiert werden. Bei Fehlern oder Ausnahmen enthält diese Spalte auch detaillierte Informationen zu diesem Fehler.

### <a name="azure-diagnostics"></a>Azure-Diagnose

Der Split-Merge-Dienst verwendet die Azure-Diagnose basierend auf Azure SDK 2.5 zur Überwachung und Diagnose. Sie können die Diagnosekonfiguration wie im folgenden Artikel beschrieben steuern: [Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md). Das Downloadpaket umfasst zwei Diagnosekonfigurationen – eine für die Webrolle und eine für die Workerrolle. Sie enthalten die Definitionen zum Protokollieren von Leistungsindikatoren und die Definitionen für IIS-Protokolle, Windows-Ereignisprotokolle und Split-Merge-Anwendungsereignisprotokolle.

## <a name="deploy-diagnostics"></a>Bereitstellen der Diagnose

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Führen Sie die folgenden Befehle mit Azure PowerShell aus, um die Überwachung und Diagnose anhand der mit dem NuGet-Paket bereitgestellten Diagnosekonfiguration für die Web- und Workerrolle zu aktivieren:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Weitere Informationen zum Konfigurieren und Bereitstellen von Diagnoseeinstellungen finden Sie hier: [Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Abrufen der Diagnose

Sie können im Server-Explorer von Visual Studio im Azure-Teil der Server-Explorer-Struktur ganz einfach auf die Diagnose zugreifen. Öffnen Sie eine Visual Studio-Instanz, und klicken Sie in der Menüleiste auf "Ansicht" und "Server-Explorer". Klicken Sie auf das Azure-Symbol, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen. Navigieren Sie dann zu „Azure“ -> „Speicher“ -> „`<your storage account>`“ -> „Tabellen“ -> „WADLogsTable“. Weitere Informationen finden Sie unter [Server-Explorer](/previous-versions/x603htbk(v=vs.140)).

![WADLogsTable][2]

Die in der obigen Abbildung hervorgehobene Tabelle WADLogsTable enthält die detaillierten Ereignisse aus dem Anwendungsprotokoll des Split-Merge-Diensts. Beachten Sie, dass die Standardkonfiguration des heruntergeladenen Pakets auf die Bereitstellung in einer Produktionsumgebung ausgerichtet ist. Daher ist das Intervall, in dem Protokolle und Leistungsindikatoren von den Dienstinstanzen abgerufen werden, groß (5 Minuten). Für Test- und Entwicklungsumgebungen verkleinern Sie das Intervall, indem Sie die Diagnoseeinstellungen der Web- oder Workerrolle an Ihre Anforderungen anpassen. Klicken Sie im Server-Explorer von Visual Studio (siehe oben) mit der rechten Maustaste auf die Rolle, und passen Sie dann im Dialogfeld für die Konfigurationseinstellungen der Diagnose den Übertragungszeitraum an:

![Konfiguration][3]

## <a name="performance"></a>Leistung

Im Allgemeinen ist von den höheren, leistungsstärkeren Dienstebenen eine bessere Leistung zu erwarten. Die vom Split-Merge-Dienst verwendeten Vorgänge für Massenkopieren und -löschung profitieren von höheren E/A-, CPU- und Speicherressourcenzuteilungen für die höheren Dienstebenen. Aus diesem Grund erhöhen Sie die Dienstebene nur für diese Datenbanken für einen definierten, begrenzten Zeitraum.

Der Dienst führt im Rahmen der normalen Vorgänge auch Überprüfungsabfragen aus. Mit diesen Überprüfungsabfragen wird geprüft, ob Daten unerwartet im Zielbereich vorhanden sind, und sichergestellt, dass alle Split/Merge/Move-Vorgänge in einem konsistenten Zustand gestartet werden. Für diese Abfragen werden Sharding-Schlüsselbereiche verwendet, die durch den Umfang des Vorgangs und die Batchgröße, die als Teil der Anforderungsdefinition angegeben wird, definiert werden. Diese Abfragen funktionieren am besten, wenn ein Index vorhanden ist, in dem der Shardingschlüssel die führende Spalte bildet.

Darüber hinaus ermöglicht die Eindeutigkeit des Shardingschlüssels als führender Spalte dem Dienst den Einsatz eines optimierten Ansatzes, der den Ressourcenverbrauch im Hinblick auf Speicherplatz und Arbeitsspeicher beschränkt. Diese Eindeutigkeitseigenschaft ist zum Verschieben großer Datenmengen (in der Regel über 1 GB) erforderlich.

## <a name="how-to-upgrade"></a>Aktualisieren

1. Führen Sie die Schritte in [Lernprogramm zum Split-Merge-Tool für elastische Datenbanken](elastic-scale-configure-deploy-split-and-merge.md)aus.
2. Ändern Sie Ihre Clouddienst-Konfigurationsdatei für Ihre Split-Merge-Bereitstellung, um die neuen Konfigurationsparameter widerzuspiegeln. Ein neuer erforderliche Parameter stellt die Informationen zum Zertifikat dar, das für die Verschlüsselung verwendet wird. Dies kann einfach über den Vergleich der im Download enthaltenen neuen Konfigurationsvorlagendatei mit der vorhandenen Konfiguration erreicht werden. Stellen Sie sicher, dass Sie die Einstellungen für "DataEncryptionPrimaryCertificateThumbprint" und "DataEncryptionPrimary" für die Web- und die Workerrolle hinzufügen.
3. Bevor Sie das Update in Azure bereitstellen, stellen Sie sicher, dass alle aktuell ausgeführten Split-Merge-Vorgänge abgeschlossen wurden. Sie können dazu einfach die Tabellen "RequestStatus" und "PendingWorkflows" in der Split-Merge-Metadatendatenbank nach laufenden Anforderungen abfragen.
4. Aktualisieren Sie Ihre vorhandene Clouddienstbereitstellung für Split-Merge im Azure-Abonnement mit dem neuen Paket und der aktualisierten Dienstkonfigurationsdatei.

Für das Upgrade von Split-Merge müssen Sie keine neue Metadatendatenbank bereitstellen. Die neue Version aktualisiert die vorhandene Metadatendatenbank automatisch auf die neue Version.

## <a name="best-practices--troubleshooting"></a>Bewährte Methoden und Problembehandlung

- Definieren Sie einen Testmandanten, und probieren Sie Ihre wichtigsten Split/Merge/Move-Vorgänge mit dem Testmandanten über mehrere Shards hinweg aus. Stellen Sie sicher, dass alle Metadaten in der Shard-Zuordnung ordnungsgemäß definiert sind und dass die Vorgänge keine Einschränkungen oder Fremdschlüssel verletzen.
- Verwenden Sie für den Testmandanten eine Datengröße, welche die maximale Größe des größten Mandanten überschreitet, um sicherzustellen, dass keine Probleme im Zusammenhang mit der Datengröße auftreten. Damit können Sie den Zeitraum einschätzen, der zum Verschieben eines einzelnen Mandanten maximal benötigt wird.
- Stellen Sie sicher, dass Ihr Schema Löschungen zulässt. Der Split-Merge-Dienst erfordert, dass Daten aus der Quell-Shard entfernt werden können, nachdem sie erfolgreich an das Ziel kopiert wurden. Beispielsweise können **delete-Trigger** verhindern, dass der Dienst Daten aus der Quelle löscht, was dazu führen kann, dass Vorgänge nicht erfolgreich ausgeführt werden können.
- Der Sharding-Schlüssel sollte die führende Spalte im Primärschlüssel oder der eindeutigen Indexdefinition bilden. Damit wird die beste Leistung für die Split- oder Merge-Überprüfungsabfragen und die tatsächlichen Verschiebe- und Löschvorgänge der Daten sichergestellt, die immer auf Sharding-Schlüsselbereichen basieren.
- Ordnen Sie den Split-Merge-Dienst der Region und dem Rechenzentrum zu, in denen sich Ihre Datenbanken befinden.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/elastic-scale-overview-split-and-merge/diagnostics-config.png