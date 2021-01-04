---
title: 'Azure Cosmos DB-Emulator: Download und Versionshinweise'
description: Hier finden Sie Hinweise zu verschiedenen Versionen des Azure Cosmos DB-Emulators sowie Downloadinformationen.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 5d105ad4c2aff03dae52b340037e4c193bf82ca1
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608434"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB-Emulator: Versionshinweise und Informationen zum Download
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dieser Artikel enthält die Versionshinweise zum Azure Cosmos DB-Emulator mit einer Liste der Featureupdates, die in den Versionen vorgenommen wurden. Auch die zum Herunterladen und Verwenden verfügbare neueste Version des Emulators wird aufgeführt.

## <a name="download"></a>Download

| | |
|---------|---------|
|**MSI-Download**|[Microsoft Download Center](https://aka.ms/cosmosdb-emulator)|
|**Erste Schritte**|[Lokale Entwicklung mit dem Azure Cosmos-Emulator](local-emulator.md)|

## <a name="release-notes"></a>Versionshinweise

### <a name="2119-3-december-2020"></a>2.11.9 (3. Dezember 2020)

 - In diesem Release werden neben allgemeinen Inhaltsaktualisierungen zur Berücksichtigung der neuesten Features und Verbesserungen in Azure Cosmos DB auch einige Probleme mit der Azure Cosmos DB-Emulatorfunktion behandelt:
 * Fix für ein Problem, das bei Verwendung von Direktmodus und Java-Clientanwendungen dazu führte, dass Anforderungen mit großen Dokumentnutzdaten nicht erfolgreich waren
 * Fix für ein Konnektivitätsproblem mit der MongoDB-Endpunktversion 3.6, wenn diese von .NET-basierten Anwendungen als Ziel verwendet wurde

### <a name="2118-6-november-2020"></a>2.11.8 (6. November 2020)

 - Diese Version enthält ein Update für den Daten-Explorers des Cosmos-Emulators und behebt ein Problem, bei dem TLS 1.3-Clients versuchen, den Daten-Explorer zu öffnen.

### <a name="2116-6-october-2020"></a>2.11.6 (6. Oktober 2020)

 - In diesem Release wird ein Problem in Zusammenhang mit der Parallelität behoben, das auftreten kann, wenn mehrere Container gleichzeitig erstellt werden. In solchen Fällen verbleiben die Daten des Emulators in einem beschädigten Zustand, und bei anschließenden API-Anforderungen an den Endpunkt des Emulators können Fehler mit der Meldung „Dienst nicht verfügbar“ auftreten. Dann ist ein Neustart erforderlich, und die lokalen Daten des Emulators müssen zurückgesetzt werden.

### <a name="2115-23-august-2020"></a>2.11.5 (23. August 2020)

In dieser Version wurden zwei neue Startoptionen für den Cosmos-Emulator hinzugefügt: 

* „/EnablePreview“: Mit dieser Option werden Previewfunktionen für den Emulator aktiviert. Auf die Previewfunktionen, die sich noch in der Entwicklung befinden, können Sie über CI und das Schreiben von Beispielen zugreifen.
* „/EnableAadAuthentication“: Diese Option ermöglicht es dem Emulator, benutzerdefinierte Azure Active Directory-Token als Alternative zu den Azure Cosmos-Primärschlüsseln zu akzeptieren. Diese Funktion befindet sich noch in der Entwicklung. Bestimmte Rollenzuweisungen und andere Einstellungen im Zusammenhang mit Berechtigungen werden derzeit nicht unterstützt.

### <a name="2112-07-july-2020"></a>2.11.2 (7. Juli 2020)

- In diesem Release wird die Erfassung der erforderlichen ETL-Ablaufverfolgungen beim Behandeln von Problemen mit dem Cosmos-Emulator geändert. WPR-Tools (Windows Performance Runtime) sind jetzt die Standardtools für die Erfassung ETL-basierter Ablaufverfolgungen. Die LOGMAN-basierte Erfassung ist veraltet. Diese Änderung ist unter anderem erforderlich, da die neuesten Windows-Sicherheitsupdates unerwartete Auswirkungen auf die Funktionsweise von LOGMAN hatten (bei Ausführung über den Cosmos-Emulator).

### <a name="2111-10-june-2020"></a>2.11.1 (10. Juni 2020)

- Mit diesem Release werden einige Fehler im Zusammenhang mit dem Daten-Explorer des Emulators behoben. In bestimmten Fällen, in denen der Daten-Explorer des Emulators über einen Webbrowser verwendet wird, kann keine Verbindung mit dem Cosmos-Emulatorendpunkt hergestellt werden, und alle zugehörigen Aktionen wie das Erstellen einer Datenbank oder eines Containers schlagen fehl. Das zweite behobene Problem steht im Zusammenhang mit dem Erstellen eines Elements aus einer JSON-Datei, wobei die Uploadaktion des Daten-Explorers verwendet wird.

### <a name="2110"></a>2.11.0

- In dieser Version wird die Unterstützung für den per Autoskalierung bereitgestellten Durchsatz eingeführt. Mit diesen neuen Features können Sie benutzerdefiniert und in Form von Anforderungseinheiten (RU/s) die Stufe für den maximal bereitgestellten Durchsatz festlegen, die automatische Skalierung für vorhandene Datenbanken und Container aktivieren und die programmgesteuerte Unterstützung durch Azure Cosmos DB SDKs nutzen.
- Behoben wurde ein Problem bei der Abfrage zahlreicher Dokumente (mehr als 1 GB), bei der ein Emulatorfehler mit dem internen Fehlerstatuscode 500 auftrat.

### <a name="292"></a>2.9.2

- In dieser Version wurde ein Fehler beim Aktivieren des Supports für die MongoDB-Endpunktversion 3.2 behoben. Hinzugefügt wurde außerdem Unterstützung für das Erstellen von ETL-Ablaufverfolgungen für die Problembehandlung mit WPR anstelle von LOGMAN.

### <a name="291"></a>2.9.1

- In diesem Release wurden einige Probleme mit der Abfrage-API-Unterstützung behoben, und die Kompatibilität mit älteren Betriebssystemen wie Windows Server 2012 wurde wiederhergestellt.

### <a name="290"></a>2.9.0

- In diesem Release werden die Option zum Festlegen der Konsistenz auf ein konsistentes Präfix sowie die Option zum Erhöhen der Obergrenze für Benutzer und Berechtigungen hinzugefügt.

### <a name="272"></a>2.7.2

- In diesem Release wird dem Cosmos-Emulator Serverunterstützung für MongoDB-Version 3.6 hinzugefügt. Wenn Sie einen MongoDB-Endpunkt für Version 3.6 des Diensts starten möchten, starten Sie den Emulator über eine Administratorbefehlszeile mit der Option „/EnableMongoDBEndpoint=3.6“.

### <a name="270"></a>2.7.0

- In diesem Release wurde eine Regression behoben, die Benutzer am Ausführen von Abfragen für das SQL-API-Konto über den Emulator bei Verwendung von .NET Core- oder x86 .NET-basierten Clients hinderte.

### <a name="246"></a>2.4.6

- Diese Version bietet Parität mit den Features im Azure Cosmos-Dienst ab Juli 2019, mit den in [Lokale Entwicklung mit dem Azure Cosmos-Emulator](local-emulator.md) angegebenen Ausnahmen. Außerdem werden mehrere Fehler im Zusammenhang mit dem Herunterfahren des Emulators behoben, wenn es über die Befehlszeile aufgerufen wird, sowie mit Überschreibungen von internen IP-Adressen für SDK-Clients bei Konnektivität des direkten Modus.

### <a name="243"></a>2.4.3

- Das Starten des MongoDB-Diensts ist standardmäßig deaktiviert. Nur der SQL-Endpunkt ist standardmäßig aktiviert. Der Benutzer muss den Endpunkt manuell über die Befehlszeilenoption „/EnableMongoDbEndpoint“ des Emulators starten. Jetzt ist es wie bei allen anderen Dienstendpunkten wie Gremlin, Cassandra und Table.
- Ein Fehler im Emulator beim Start mit „/AllowNetworkAccess“ wurde behoben, bei dem die Endpunkte Gremlin, Cassandra und Table Anfragen von externen Clients nicht korrekt behandelten.
- Direkte Verbindungsports wurden den Einstellungen für Firewallregeln hinzugefügt.

### <a name="240"></a>2.4.0

- Es wurde das Problem behoben, dass der Emulator nicht gestartet werden konnte, wenn Netzwerküberwachungs-Apps wie Pulse Client auf dem Hostcomputer vorhanden waren.
