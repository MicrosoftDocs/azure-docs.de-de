---
title: Befehlszeilen- und PowerShell-Referenz für den Azure Cosmos DB-Emulator
description: Erfahren Sie, welche Befehlszeilenparameter für den Azure Cosmos DB-Emulator es gibt, wie Sie den Emulator mit PowerShell steuern und die Anzahl von Containern ändern, die Sie im Emulator erstellen können.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979503"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Befehlszeilen- und PowerShell-Referenz für den Azure Cosmos DB-Emulator
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung bereit, die den Azure Cosmos DB-Dienst zu lokalen Entwicklungszwecken emuliert. Nach [Installation des Emulators](local-emulator.md) können Sie ihn mit Befehlszeilen- und PowerShell-Befehlen steuern. In diesem Artikel wird beschrieben, wie Sie mithilfe der Befehlszeilen- und PowerShell-Befehle den Emulator starten und anhalten, Optionen konfigurieren und andere Vorgänge ausführen. Sie müssen die Befehle vom Installationsort aus ausführen.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Verwalten des Emulators mit Befehlszeilensyntax

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Geben Sie zum Anzeigen der Liste der Optionen an der Eingabeaufforderung `Microsoft.Azure.Cosmos.Emulator.exe /?` ein.

|**Option** | **Beschreibung** | **Befehl**| **Argumente**|
|---|---|---|---|
|[Keine Argumente] | Startet den Azure Cosmos DB-Emulator mit den Standardeinstellungen. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Hilfe] |Zeigt die Liste mit unterstützten Befehlszeilenargumenten an.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Ruft den Status des Azure Cosmos DB-Emulators ab. Der Status wird durch den Exitcode angegeben: 1 = wird gestartet, 2 = wird ausgeführt, 3 = beendet. Ein negativer Exitcode gibt an, dass ein Fehler aufgetreten ist. Es wird keine andere Ausgabe erzeugt. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Fährt den Azure Cosmos DB-Emulator herunter.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Gibt den Pfad an, unter dem Datendateien gespeichert werden sollen. Der Standardwert ist „%LocalAppdata%\CosmosDBEmulator“. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Ein zugänglicher Pfad. |
|Port | Gibt die für den Emulator zu verwendende Portnummer an. Der Standardwert ist 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>: Einzelne Portnummer. |
| ComputePort | Geben Sie die Portnummer an, für die der Gatewaydienst für Compute-Interop verwendet werden soll. Der Testport für das Gateway des HTTP-Endpunkt wird als ComputePort + 79 berechnet. Daher müssen ComputePort und ComputePort + 79 offen und verfügbar sein. Der Standardwert lautet 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>: Einzelne Portnummer. |
| EnableMongoDbEndpoint=3.2 | Aktiviert die MongoDB-API 3.2. | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Aktiviert die MongoDB-API 3.6. | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Gibt die für die MongoDB-Kompatibilitäts-API zu verwendende Portnummer an. Der Standardwert ist 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: Einzelne Portnummer.|
| EnableCassandraEndpoint | Aktiviert die Cassandra-API. | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Gibt die für den Cassandra-Endpunkt zu verwendende Portnummer an. Der Standardwert ist 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: Einzelne Portnummer. |
| EnableGremlinEndpoint | Aktiviert die Gremlin-API. | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Die für den Gremlin-Endpunkt zu verwendende Portnummer. Der Standardwert ist 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: Einzelne Portnummer. |
|EnableTableEndpoint | Aktiviert die Azure-Tabellen-API | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Die für den Azure-Tabellenendpunkt zu verwendende Portnummer. Der Standardwert ist 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: Einzelne Portnummer.|
| KeyFile | Liest den Autorisierungsschlüssel aus der angegebenen Datei. Verwenden Sie die Option „/GenKeyFile“, um eine Schlüsseldatei zu generieren. | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Der Pfad zur Datei. |
| ResetDataPath | Entfernt rekursiv alle Dateien im angegebenen Pfad. Wenn Sie keinen Pfad angeben, wird standardmäßig „%LOCALAPPDATA%\CosmosDbEmulator“ verwendet. | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>: Dateipfad  |
| StartTraces  |  Startet das Sammeln von Ablaufverfolgungsprotokollen für das Debuggen mithilfe von LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Beendet das Sammeln von Ablaufverfolgungsprotokollen für das Debuggen mithilfe von LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Startet das Sammeln von Ablaufverfolgungsprotokollen für das Debuggen mit dem Windows-Tool für die Leistungsaufzeichnung. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Beendet das Sammeln von Ablaufverfolgungsprotokollen für das Debuggen mit dem Windows-Tool für die Leistungsaufzeichnung. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Das selbstsignierte TLS-/SSL-Zertifikat des Emulators wird standardmäßig neu generiert, wenn das SAN des Zertifikats den Domänennamen des Emulator-Hosts, die lokale IPv4-Adresse, „localhost“ und „127.0.0.0.1“ nicht enthält. Bei dieser Option tritt stattdessen beim Start des Emulators ein Fehler auf. Verwenden Sie dann die Option „/GenCert“, um ein neues, selbstsigniertes TLS-/SSL-Zertifikat zu erstellen und zu installieren. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Generiert und installiert ein neues, selbstsigniertes TLS-/SSL-Zertifikat. Enthält optional eine durch Trennzeichen getrennte Liste zusätzlicher DNS-Namen für den Zugriff auf den Emulator über das Netzwerk. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: Optionale durch Trennzeichen getrennte Liste zusätzlicher DNS-Namen  |
| DirectPorts |Gibt die für die direkte Konnektivität zu verwendenden Ports an. Die Standardwerte sind 10251, 10252, 10253, 10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Durch Trennzeichen getrennte Liste mit vier Ports. |
| Schlüssel |Autorisierungsschlüssel für den Emulator. Der Schlüssel muss die Base64-Codierung eines 64-Byte-Vektors sein. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>: Der Schlüssel muss die Base64-Codierung eines 64-Bytes-Vektors sein.|
| EnableRateLimiting | Gibt an, dass das Verhalten für die Anforderungsratenbegrenzung aktiviert ist. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Gibt an, dass das Verhalten für die Anforderungsratenbegrenzung deaktiviert ist. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Die Emulator-Benutzeroberfläche wird nicht angezeigt. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Der Daten-Explorer wird beim Start nicht angezeigt. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Legt die maximale Anzahl von partitionierten Containern fest. Weitere Informationen finden Sie unter [Ändern der Anzahl von Containern](#set-partitioncount). | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Maximale Anzahl der zulässigen Container mit nur einer Partition. Der Standardwert ist 25. Der zulässige Höchstwert beträgt 250.|
| DefaultPartitionCount| Gibt die Standardanzahl von Partitionen für einen partitionierten Container an. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\>er Standardwert ist 25.|
| AllowNetworkAccess | Ermöglicht Zugriff auf den Emulator über ein Netzwerk. Sie müssen aus „/Key=\<key_string\> or /KeyFile=\<file_name\>“ übergeben, um Netzwerkzugriff zu ermöglichen. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> oder Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Firewallregeln nicht anpassen, wenn Option „/AllowNetworkAccess“ verwendet wird. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Generiert einen neuen Autorisierungsschlüssel und speichert ihn in der angegebenen Datei. Der generierte Schlüssel kann mit den Optionen „/Key“ oder „/KeyFile“ verwendet werden. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| Konsistenz | Legt die Standardkonsistenzebene des Kontos fest. | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>: Der Wert muss eine der folgenden [Konsistenzebenen](consistency-levels.md) sein: Sitzung, stark, letztlich oder begrenzte Veraltung. Der Standardwert lautet „Sitzung“. |
| ? | Zeigt die Hilfemeldung an.| | |

## <a name="manage-the-emulator-with-powershell"></a>Verwalten des Emulators mit PowerShell

Der Emulator bietet ein PowerShell-Modul zum Starten, Beenden und Deinstallieren des Diensts und zum Abrufen des Dienststatus. Führen Sie das folgende Cmdlet aus, um das PowerShell-Modul zu verwenden:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

oder platzieren Sie das `PSModules`-Verzeichnis auf Ihrem `PSModulePath`, und importieren Sie es wie in folgendem Befehl gezeigt:

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Hier sehen Sie eine Zusammenfassung der Befehle zum Steuern des Emulators über PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Anmerkungen**

Gibt einen dieser ServiceControllerStatus-Werte zurück: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running oder ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Anmerkungen**

Startet den Emulator. Standardmäßig wartet der Befehl, bis der Emulator bereit ist, Anforderungen entgegenzunehmen. Verwenden Sie die Option -NoWait, wenn Sie möchten, dass das Cmdlet zurückgegeben wird, sobald der Emulator gestartet wurde.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Anmerkungen**

Beendet den Emulator. Standardmäßig wartet dieser Befehl, bis der Emulator vollständig heruntergefahren ist. Verwenden Sie die Option -NoWait, wenn Sie möchten, dass das Cmdlet zurückgegeben wird, sobald der Emulator heruntergefahren wird.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Anmerkungen**

Deinstalliert den Emulator und entfernt optional den gesamten Inhalt von $env:LOCALAPPDATA\CosmosDbEmulator.
Das Cmdlet stellt sicher, dass der Emulator vor der Deinstallation beendet wird.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Ändern der Anzahl von Standardcontainern

Sie können mit dem Azure Cosmos-Emulator standardmäßig bis zu 25 Container mit fester Größe (nur unterstützt durch Azure Cosmos DB-SDKs) oder 5 Container mit unbegrenzter Größe erstellen. Durch Ändern des Wertes **PartitionCount** können Sie bis zu 250 Container mit fester Größe oder 50 Container mit unbegrenzter Größe oder jede Kombination der beiden erstellen, die 250 Container mit fester Größe nicht überschreitet (wobei ein Container mit unbegrenzter Größe 5 Containern mit fester Größe entspricht). Es wird jedoch nicht empfohlen, den Emulator so einzurichten, dass er mit mehr als 200 Containern fester Größe ausgeführt wird. Wegen des Mehraufwands, der den E/A-Vorgängen des Datenträgers hinzugefügt wird, kann es zu unvorhersehbaren Timeouts bei der Verwendung der Endpunkt-APIs kommen.

Wenn Sie versuchen, einen Container zu erstellen, wenn die aktuelle Anzahl von Partitionen überschritten wurde, löst der Emulator eine ServiceUnavailable-Ausnahme mit der folgenden Meldung aus.

> Bedauerlicherweise liegt in dieser Region zurzeit eine starke Nachfrage vor. Daher kann Ihre Anfrage gegenwärtig nicht verarbeitet werden.  Wir arbeiten fortlaufend daran, immer mehr Kapazitäten online zu stellen. Bitte versuchen Sie es zu einem späteren Zeitpunkt noch mal.
> ActivityId: 12345678-1234-1234-1234-123456789abc

Gehen Sie wie folgt vor, um die Anzahl von Containern zu ändern, die für den Azure Cosmos DB-Emulator verfügbar sind:

1. Löschen Sie alle lokalen Azure Cosmos DB-Emulatordaten, indem Sie in der Taskleiste mit der rechten Maustaste auf das Symbol **Azure Cosmos DB-Emulator** klicken, und klicken Sie anschließend auf **Reset Data…** (Daten zurücksetzen...).

1. Löschen Sie alle Emulatordaten in diesem Ordner `%LOCALAPPDATA%\CosmosDBEmulator`.

1. Schließen Sie alle geöffneten Instanzen, indem Sie mit der rechten Maustaste auf der Taskleiste auf das Symbol **Azure Cosmos DB-Emulator** klicken, und klicken Sie anschließend auf **Beenden**. Bis alle Instanzen beendet wurden, kann unter Umständen eine Minute vergehen.

1. Installieren Sie die neueste Version des [Azure Cosmos DB-Emulators](https://aka.ms/cosmosdb-emulator).

1. Starten Sie den Emulator mit dem PartitionCount-Flag, indem Sie einen Wert <= 250 festlegen. Beispiel: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Nächste Schritte

* [Exportieren der Azure Cosmos DB-Emulatorzertifikate für die Verwendung mit Java-, Python- und Node.js-Apps](local-emulator-export-ssl-certificates.md)
* [Debuggen von Problemen mit dem Emulator](troubleshoot-local-emulator.md)
