---
title: Lokale Entwicklung mit dem Azure Cosmos-Emulator
description: Mit dem Azure Cosmos-Emulator können Sie Ihre Anwendung kostenlos lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen.
ms.service: cosmos-db
ms.topic: tutorial
author: deborahc
ms.author: dech
ms.date: 05/20/2019
ms.openlocfilehash: 9e7342ebcbcf536b26e6cf7fb89e3cf58666d24f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953959"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Verwenden des Azure Cosmos-Emulators für lokale Entwicklungs- und Testvorgänge

Der Azure Cosmos-Emulator stellt eine lokale Umgebung bereit, die zu Entwicklungszwecken den Azure Cosmos DB-Dienst emuliert. Bei Verwendung des Azure Cosmos-Emulators können Sie die Anwendung lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen oder sonstige Kosten zu verursachen. Wenn Sie mit der Funktion der Anwendung im Azure Cosmos-Emulator zufrieden sind, können Sie zur Verwendung eines Azure Cosmos-Kontos in der Cloud wechseln.

Sie können mit Azure Cosmos-Emulator mit [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api), und [Tabellen](local-emulator.md#table-api)-API-Konten entwickeln. Zu diesem Zeitpunkt unterstützt die Data Explorer-Ansicht im Emulator jedoch Clients vollständig nur für die SQL-API. 

## <a name="how-the-emulator-works"></a>Funktionsweise des Emulators

Der Azure Cosmos-Emulator stellt eine High-Fidelity-Emulation des Azure Cosmos DB-Diensts bereit. Er unterstützt die gleichen Funktionen wie Azure Cosmos DB, z.B. Erstellen und Abfragen von Daten, Bereitstellen und Skalieren von Containern und Ausführen von gespeicherten Prozeduren und Triggern. Sie können Anwendungen mit dem Azure Cosmos-Emulator entwickeln und testen und sie auf globaler Ebene in Azure bereitstellen, indem Sie nur eine einzige Konfigurationsänderung am Verbindungsendpunkt für Azure Cosmos DB vornehmen.

Die Emulation des Azure Cosmos DB-Diensts entspricht exakt dem Dienst, die Implementierung des Emulators weist allerdings einige Unterschiede zum Dienst auf. Der Emulator verwendet Standardkomponenten des Betriebssystems, wie z.B. das lokale Dateisystem für Persistenz und den HTTPS-Protokollstapel für Konnektivität. Funktionen, die die Azure-Infrastruktur benötigen, sind nicht anwendbar. Hierzu gehören beispielsweise die globale Replikation, Lese-/Schreibvorgänge mit Latenz im einstelligen Millisekundenbereich und optimierbare Konsistenzebenen.

Sie können Daten zwischen dem Azure Cosmos-Emulator und dem Azure Cosmos DB-Dienst migrieren, indem Sie das [Migrationstool für Azure Cosmos DB-Daten](https://github.com/azure/azure-documentdb-datamigrationtool) verwenden.

Sie können den Azure Cosmos-Emulator auf dem Windows-Docker-Container ausführen. Den Befehl „docker pull“ finden Sie in [Docker-Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) und den Emulator-Quellcode in [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker).

## <a name="differences-between-the-emulator-and-the-service"></a>Unterschiede zwischen dem Emulator und dem Dienst

Da der Azure Cosmos-Emulator eine emulierte Umgebung bereitstellt, die auf einer lokalen Entwicklerarbeitsstation ausgeführt wird, gibt es einige funktionelle Unterschiede zwischen dem Emulator und einem Azure Cosmos-Konto in der Cloud:

* Zurzeit unterstützt der Data Explorer im Emulator nur Clients für die SQL-API. Die Data Explorer-Ansicht und die Vorgänge für Azure Cosmos DB-APIs wie MongoDB, Tabelle, Grafik und Cassandra-APIs werden nicht vollständig unterstützt.
* Der Azure Cosmos-Emulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Hauptschlüssel. Die erneute Schlüsselgenerierung ist im Azure Cosmos-Emulator nicht möglich, der Standardschlüssel kann jedoch über die Befehlszeilenoption geändert werden.
* Der Azure Cosmos-Emulator ist kein skalierbarer Speicherdienst und unterstützt keine große Anzahl von Containern.
* Der Azure Cosmos-Emulator bietet keine unterschiedlichen [Azure Cosmos DB-Konsistenzebenen](consistency-levels.md).
* Der Azure Cosmos-Emulator bietet keine [Replikation in mehreren Regionen](distribute-data-globally.md).
* Unter Umständen ist Ihre Kopie des Azure Cosmos-Emulators nicht auf dem neuesten Stand der Änderungen im Azure Cosmos DB-Dienst. Verwenden Sie daher den [Azure Cosmos DB-Kapazitätsplaner](https://www.documentdb.com/capacityplanner), um den erforderlichen Produktionsdurchsatz (RUs, Request Units = Anforderungseinheiten) für Ihre Anwendung richtig einzuschätzen.
* Wenn Sie den Azure Cosmos-Emulator verwenden, können Sie standardmäßig bis zu 25 Container mit fester Größe (nur unterstützt durch Azure Cosmos DB SDKs) oder 5 Container mit dem Azure Cosmos-Emulator erstellen. Weitere Informationen zum Ändern dieses Werts finden Sie unter [Festlegen des PartitionCount-Werts](#set-partitioncount).

## <a name="system-requirements"></a>Systemanforderungen

Für den Azure Cosmos-Emulator gelten folgende Hardware- und Softwareanforderungen:

* Softwareanforderungen
  * Windows Server 2012 R2, Windows Server 2016 oder Windows 10
  * 64-Bit-Betriebssystem
* Minimale Hardwareanforderungen
  * 2 GB RAM
  * 10 GB verfügbarer Speicherplatz

## <a name="installation"></a>Installation

Sie können den Azure Cosmos-Emulator aus dem [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) herunterladen und installieren. Alternativ können Sie den Emulator auch unter Docker für Windows ausführen. Anweisungen zum Verwenden des Emulators in Docker für Windows finden Sie unter [Ausführen unter Docker](#running-on-docker).

> [!NOTE]
> Zum Installieren, Konfigurieren und Ausführen des Azure Cosmos-Emulators benötigen Sie Administratorrechte auf dem Computer. Der Emulator erstellt ein Zertifikat bzw. fügt es hinzu und legt auch die Firewallregeln fest, um seine Dienste auszuführen; daher ist es notwendig, dass der Emulator solche Vorgänge ausführen kann.

## <a name="running-on-windows"></a>Ausführen unter Windows

Wählen Sie zum Starten des Azure Cosmos-Emulators die Schaltfläche „Start“, oder drücken Sie die Windows-Taste. Beginnen Sie mit der Eingabe von **Azure Cosmos-Emulator**, und wählen Sie den Emulator in der Liste mit den Anwendungen aus.

![Wählen Sie die Schaltfläche „Start“, oder drücken Sie die Windows-Taste, beginnen Sie mit der Eingabe von **Azure Cosmos-Emulator**, und wählen Sie den Emulator aus der Liste mit den Anwendungen aus.](./media/local-emulator/database-local-emulator-start.png)

Wenn der Emulator ausgeführt wird, wird im Infobereich der Windows-Taskleiste ein Symbol angezeigt. ![Benachrichtigung des lokalen Azure Cosmos DB-Emulators in der Taskleiste](./media/local-emulator/database-local-emulator-taskbar.png)

Der Azure Cosmos-Emulator wird standardmäßig auf dem lokalen Computer („localhost“) ausgeführt und lauscht auf Port 8081.

Der Azure Cosmos-Emulator wird standardmäßig in `C:\Program Files\Azure Cosmos DB Emulator` installiert. Sie können den Emulator auch über die Befehlszeile starten und beenden. Weitere Informationen finden Sie in der [Referenz zum Befehlszeilentool](#command-line).

## <a name="start-data-explorer"></a>Starten des Daten-Explorers

Wenn der Azure Cosmos-Emulator gestartet wird, wird er in Ihrem Browser automatisch im Azure Cosmos-Daten-Explorer geöffnet. Die Adresse wird als `https://localhost:8081/_explorer/index.html` angezeigt. Wenn Sie den Explorer schließen und ihn später erneut öffnen möchten, können Sie entweder die URL in Ihrem Browser öffnen oder den Explorer über den Azure Cosmos-Emulator im Windows-Taskleistensymbol starten. Dies ist unten dargestellt.

![Startprogramm für den Daten-Explorer des lokalen Azure Cosmos-Emulators](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Überprüfen auf Updates

Der Daten-Explorer gibt an, ob ein neues Update zum Download zur Verfügung steht.

> [!NOTE]
> Der Zugriff auf Daten, die in einer bestimmten Version des Azure Cosmos DB-Emulators (siehe %LOCALAPPDATA%\CosmosDBEmulator oder die optionalen Einstellungen für den Datenpfad) erstellt wurden, ist bei Verwendung einer anderen Version nicht gewährleistet. Wenn Sie die Daten langfristig beibehalten müssen, wird empfohlen, sie in einem Azure Cosmos-Konto zu speichern, nicht im Azure Cosmos-Emulator.

## <a name="authenticating-requests"></a>Authentifizieren von Anforderungen

Wie bei Azure Cosmos DB in der Cloud muss jede Anforderung, die Sie für den Azure Cosmos-Emulator durchführen, authentifiziert werden. Der Azure Cosmos-Emulator unterstützt ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel für die Authentifizierung per Hauptschlüssel. Dieses Konto und dieser Schlüssel sind die einzigen Anmeldeinformationen, die für den Azure Cosmos-Emulator verwendet werden dürfen. Sie lauten wie folgt:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Der vom Azure Cosmos-Emulator unterstützte Hauptschlüssel ist nur für die Verwendung mit dem Emulator gedacht. Es ist nicht möglich, Ihr Azure Cosmos DB-Konto und den dazugehörigen Schlüssel mit dem Azure Cosmos-Emulator zu verwenden.

> [!NOTE]
> Wenn Sie den Emulator mit der Option „/Key“ gestartet haben, verwenden Sie den generierten Schlüssel anstelle von `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Weitere Informationen zur Option „/Key“ finden Sie in der [Referenz zum Befehlszeilentool](#command-line-syntax).

Genau wie Azure Cosmos DB unterstützt der Azure Cosmos-Emulator ausschließlich die sichere Kommunikation über SSL.

## <a name="running-on-a-local-network"></a>Ausführen im lokalen Netzwerk

Sie können den Emulator in einem lokalen Netzwerk ausführen. Um Netzwerkzugriff zu ermöglichen, geben Sie in der [Befehlszeile](#command-line-syntax) `/AllowNetworkAccess` an, wodurch Sie gleichzeitig auch `/Key=key_string` oder `/KeyFile=file_name` angeben müssen. Sie können mit `/GenKeyFile=file_name` im Vorfeld eine Datei mit einem zufälligen Schlüssel generieren. Dann übergeben Sie diese an `/KeyFile=file_name` oder `/Key=contents_of_file`.

Wenn der Netzwerkzugriff zum ersten Mal bereitgestellt wird, sollte der Benutzer den Emulator herunterfahren und das Datenverzeichnis des Emulators (%LOCALAPPDATA%\CosmosDBEmulator) löschen.

## <a name="developing-with-the-emulator"></a>Entwickeln mit dem Emulator

### <a name="sql-api"></a>SQL-API

Sobald der Azure Cosmos-Emulator auf Ihrem Desktop ausgeführt wird, können Sie jedes unterstützte [Azure Cosmos DB-SDK](sql-api-sdk-dotnet.md) oder die [Azure Cosmos DB-REST-API](/rest/api/cosmos-db/) für die Interaktion mit dem Emulator verwenden. Der Azure Cosmos-Emulator enthält auch einen integrierten Data Explorer, mit dem Sie Container für die SQL-API und die API für MongoDB von Cosmos DB erstellen sowie Elemente anzeigen und bearbeiten können, ohne Code schreiben zu müssen.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API für MongoDB

Geben Sie die folgende Verbindungszeichenfolge an, wenn Sie die [Azure Cosmos DB-API für MongoDB](mongodb-introduction.md) verwenden:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabelle-API

Sobald der Azure Cosmos-Emulator auf Ihrem Desktop ausgeführt wird, können Sie die [Azure Cosmos DB-Tabellen-API](table-storage-how-to-use-dotnet.md) für die Interaktion mit dem Emulator verwenden. Starten Sie den Emulator aus der Eingabeaufforderung als Administrator mit „/EnableTableEndpoint“. Führen Sie anschließend den folgenden Code aus, um eine Verbindung mit dem Tabellen-API-Konto herzustellen:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra-API

Starten Sie den Emulator aus der Eingabeaufforderung als Administrator mit „/EnableCassandraEndpoint“. Alternativ können Sie auch die Umgebungsvariable `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` festlegen.

* [Installieren Sie Python 2.7](https://www.python.org/downloads/release/python-2716/).

* [Installieren Sie Cassandra CLI/CQLSH](https://cassandra.apache.org/download/).

* Führen Sie die folgenden Befehle in einem regulären Eingabeaufforderungsfenster aus:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* Führen Sie in der CQLSH Shell die folgenden Befehle aus, um eine Verbindung zum Cassandra-Endpunkt herzustellen:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Gremlin-API

Starten Sie den Emulator aus der Eingabeaufforderung als Administrator mit „/EnableGremlinEndpoint“. Alternativ können Sie auch die Umgebungsvariable `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true` festlegen.

* [Installieren Sie apache-tinkerpop-gremlin-console-3.3.4](https://tinkerpop.apache.org/downloads.html).

* Erstellen Sie im Data Explorer des Emulators eine Datenbank „db1“ und eine Sammlung „coll1“; wählen Sie für den Partitionsschlüssel „/name“.

* Führen Sie die folgenden Befehle in einem regulären Eingabeaufforderungsfenster aus:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* Führen Sie in der Gremlin Shell die folgenden Befehle aus, um eine Verbindung zum Gremlin-Endpunkt herzustellen:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Exportieren des SSL-Zertifikats

.NET-Sprachen und -Laufzeit verwenden den Windows-Zertifikatspeicher, um sichere Verbindungen mit dem lokalen Azure Cosmos DB-Emulator herzustellen. Andere Sprachen bieten andere Methoden für die Verwaltung und Verwendung von Zertifikaten. Java verwendet einen eigenen [Zertifikatspeicher](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python dagegen verwendet [Wrapper für Socketobjekte](https://docs.python.org/2/library/ssl.html).

Um ein Zertifikat zur Verwendung mit Sprachen und Laufzeiten abzurufen, die nicht in den Windows-Zertifikatspeicher integriert sind, müssen Sie dieses mithilfe des Windows-Zertifikat-Managers exportieren. Starten Sie den Manager, indem Sie „certlm.msc“ ausführen oder die Schritt-für-Schritt-Anleitung unter [Exportieren der Azure Cosmos-Emulatorzertifikate](./local-emulator-export-ssl-certificates.md) befolgen. Sobald der Zertifikat-Manager ausgeführt wird, öffnen Sie die persönlichen Zertifikate, wie unten gezeigt, und exportieren Sie das Zertifikat mit dem Anzeigenamen „DocumentDBEmulatorCertificate“ als Base64-codierte X.509-CER-Datei.

![SSL-Zertifikat für den lokalen Azure Cosmos DB-Emulator](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Importieren Sie das X.509-Zertifikat in den Java-Zertifikatspeicher, indem Sie die Anweisungen unter [Hinzufügen eines Zertifikats zum Java CA-Zertifikatspeicher](https://docs.microsoft.com/azure/java-add-certificate-ca-store) befolgen. Sobald das Zertifikat in den Zertifikatsspeicher importiert wurde, können sich Clients für SQL und die API für MongoDB von Azure Cosmos DB mit dem Azure Cosmos-Emulator verbinden.

Beim Herstellen einer Verbindung mit dem Emulator in Python- und Node.js-SDKs ist die SSL-Überprüfung deaktiviert.

## <a id="command-line"></a>Referenz zum Befehlszeilentool
Sie können die Befehlszeile im Installationspfad verwenden, um den Emulator zu starten und zu beenden, Optionen zu konfigurieren und andere Vorgänge auszuführen.

### <a name="command-line-syntax"></a>Befehlszeilensyntax

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Geben Sie zum Anzeigen der Liste der Optionen an der Eingabeaufforderung `CosmosDB.Emulator.exe /?` ein.

|**Option** | **Beschreibung** | **Befehl**| **Argumente**|
|---|---|---|---|
|[Keine Argumente] | Startet den Azure Cosmos-Emulator mit den Standardeinstellungen. |CosmosDB.Emulator.exe| |
|[Hilfe] |Zeigt die Liste mit unterstützten Befehlszeilenargumenten an.|CosmosDB.Emulator.exe /? | |
| GetStatus |Ruft den Status des Azure Cosmos-Emulators ab. Der Status wird durch den Exitcode angegeben: 1 = wird gestartet, 2 = wird ausgeführt, 3 = beendet. Ein negativer Exitcode gibt an, dass ein Fehler aufgetreten ist. Es wird keine andere Ausgabe erzeugt. | CosmosDB.Emulator.exe /GetStatus| |
| Shutdown| Fährt den Azure Cosmos-Emulator herunter.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Gibt den Pfad an, unter dem Datendateien gespeichert werden sollen. Der Standardwert ist „%LocalAppdata%\CosmosDBEmulator“. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Ein zugänglicher Pfad. |
|Port | Gibt die für den Emulator zu verwendende Portnummer an. Der Standardwert ist 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<port\>: Einzelne Portnummer. |
| MongoPort | Gibt die für die MongoDB-Kompatibilitäts-API zu verwendende Portnummer an. Der Standardwert ist 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongoport\>: Einzelne Portnummer.|
| CassandraPort | Gibt die für den Cassandra-Endpunkt zu verwendende Portnummer an. Der Standardwert ist 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: Einzelne Portnummer. |
| ComputePort | Geben Sie die Portnummer an, für die der Gatewaydienst für Compute-Interop verwendet werden soll. Der Testport für das Gateway des HTTP-Endpunkt wird als ComputePort + 79 berechnet. Daher müssen ComputePort und ComputePort + 79 offen und verfügbar sein. Die Standardwerte sind 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: Einzelne Portnummer. |
| EnableCassandraEndpoint | Aktiviert die Cassandra-API. | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| EnableGremlinEndpoint | Aktiviert die Gremlin-API. | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Die für den Gremlin-Endpunkt zu verwendende Portnummer. Der Standardwert ist 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<port\>: Einzelne Portnummer. |
|TablePort | Die für den Azure-Tabellenendpunkt zu verwendende Portnummer. Der Standardwert ist 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<port\>: Einzelne Portnummer.|
| KeyFile | Liest den Autorisierungsschlüssel aus der angegebenen Datei. Verwenden Sie die Option „/GenKeyFile“, um eine Schlüsseldatei zu generieren. | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Der Pfad zur Datei. |
| ResetDataPath | Entfernt rekursiv alle Dateien im angegebenen Pfad. Wenn Sie keinen Pfad angeben, wird standardmäßig „%LOCALAPPDATA%\CosmosDbEmulator“ verwendet. | CosmosDB.Emulator.exe /ResetDataPath[=\<path>] | \<path\>: Dateipfad  |
| StartTraces  |  Startet das Sammeln von Debugablaufverfolgungsprotokollen. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Beendet das Sammeln von Debugablaufverfolgungsprotokollen. | CosmosDB.Emulator.exe /StopTraces  | |
|EnableTableEndpoint | Aktiviert die Azure-Tabellen-API | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|FailOnSslCertificateNameMismatch | Standardmäßig erneuert der Emulator sein selbstsigniertes SSL-Zertifikat, wenn das SAN des Zertifikats nicht den Domänennamen des Emulator-Hosts, die lokale IPv4-Adresse, den „localhost“ und „127.0.0.0.1“ enthält. Mit dieser Option schlägt der Emulator stattdessen beim Start fehl. Verwenden Sie dann die Option „/GenCert“, um ein neues, selbstsigniertes SSL-Zertifikat zu erstellen und zu installieren. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Generiert und installiert ein neues, selbstsigniertes SSL-Zertifikat. Optional mit einer kommagetrennten Liste zusätzlicher DNS-Namen für den Zugriff auf den Emulator über das Netzwerk. | CosmosDB.Emulator.exe /GenCert[ \<comma-separated list of additional dns-names\>] | |
| DirectPorts |Gibt die für die direkte Konnektivität zu verwendenden Ports an. Die Standardwerte sind 10251, 10252, 10253, 10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Durch Trennzeichen getrennte Liste mit vier Ports. |
| Schlüssel |Autorisierungsschlüssel für den Emulator. Der Schlüssel muss die Base64-Codierung eines 64-Byte-Vektors sein. | CosmosDB.Emulator.exe /Key:\<key\> | \<key\>: Der Schlüssel muss die Base64-Codierung eines 64-Bytes-Vektors sein.|
| EnableRateLimiting | Gibt an, dass das Verhalten für die Anforderungsratenbegrenzung aktiviert ist. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Gibt an, dass das Verhalten für die Anforderungsratenbegrenzung deaktiviert ist. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Die Emulator-Benutzeroberfläche wird nicht angezeigt. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Der Daten-Explorer wird beim Start nicht angezeigt. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Legt die maximale Anzahl von partitionierten Containern fest. Weitere Informationen finden Sie unter [Ändern der Anzahl von Containern](#set-partitioncount). | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Maximale Anzahl der zulässigen Container mit nur einer Partition. Der Standardwert ist 25. Der zulässige Höchstwert beträgt 250.|
| DefaultPartitionCount| Gibt die Standardanzahl von Partitionen für einen partitionierten Container an. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> Der Standardwert ist 25.|
| AllowNetworkAccess | Ermöglicht Zugriff auf den Emulator über ein Netzwerk. Sie müssen auch „/Key=\<key_string\>“ oder „/KeyFile=\<file_name\>“ übergeben, um Netzwerkzugriff zu ermöglichen. | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> oder CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Firewallregeln nicht anpassen, wenn Option „/AllowNetworkAccess“ verwendet wird. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Generiert einen neuen Autorisierungsschlüssel und speichert ihn in der angegebenen Datei. Der generierte Schlüssel kann mit den Optionen „/Key“ oder „/KeyFile“ verwendet werden. | CosmosDB.Emulator.exe /GenKeyFile=\<Pfad zur Schlüsseldatei\> | |
| Konsistenz | Legt die Standardkonsistenzebene des Kontos fest. | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<consistency\>: Der Wert muss eine der folgenden [Konsistenzebenen](consistency-levels.md) sein: Sitzung, stark, letztlich oder begrenzte Veraltung. Der Standardwert lautet „Sitzung“. |
| ? | Zeigt die Hilfemeldung an.| | |

## <a id="set-partitioncount"></a>Ändern der Anzahl von Containern

Sie können standardmäßig bis zu 25 Container mit fester Größe (nur unterstützt durch Azure Cosmos DB SDKs) oder 5 Container mit dem Azure Cosmos-Emulator erstellen. Durch Ändern des Wertes **PartitionCount** können Sie bis zu 250 Container mit fester Größe oder 50 Container mit unbegrenzter Größe oder jede Kombination der beiden erstellen, die 250 Container mit fester Größe nicht überschreitet (wobei ein Container mit unbegrenzter Größe 5 Containern mit fester Größe entspricht). Es wird jedoch nicht empfohlen, den Emulator so einzurichten, dass er mit mehr als 200 Containern fester Größe ausgeführt wird. Wegen des Mehraufwands, der den E/A-Vorgängen des Datenträgers hinzugefügt wird, kann es zu unvorhersehbaren Timeouts bei der Verwendung der Endpunkt-APIs kommen.


Wenn Sie versuchen, einen Container zu erstellen, wenn die aktuelle Anzahl von Partitionen überschritten wurde, löst der Emulator eine ServiceUnavailable-Ausnahme mit der folgenden Meldung aus.

„Sorry, we are currently experiencing high demand in this region, and cannot fulfill your request at this time. We work continuously to bring more and more capacity online, and encourage you to try again.
Please do not hesitate to email askcosmosdb@microsoft.com at any time or for any reason. ActivityId: 12345678-1234-1234-1234-123456789abc“ (Leider verzeichnen wir derzeit eine hohe Nachfrage in dieser Region und können Ihrer Anfrage derzeit nicht nachkommen. Wir arbeiten kontinuierlich daran, mehr Kapazitäten online zu stellen und möchten Sie daher bitten, es erneut zu versuchen. Sie können uns jederzeit eine E-Mail an  senden. ActivityId:12345678-1234-1234-1234-123456789abc).

Gehen Sie wie folgt vor, um die Anzahl von Containern zu ändern, die für den Azure Cosmos-Emulator verfügbar sind:

1. Löschen Sie alle lokalen Azure Cosmos-Emulatordaten, indem Sie in der Taskleiste mit der rechten Maustaste auf das Symbol **Azure Cosmos DB-Emulator** klicken, und klicken Sie anschließend auf **Reset Data…** (Daten zurücksetzen...).
2. Löschen Sie alle Emulatordaten in diesem Ordner `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Schließen Sie alle geöffneten Instanzen, indem Sie mit der rechten Maustaste auf der Taskleiste auf das Symbol **Azure Cosmos DB-Emulator** klicken, und klicken Sie anschließend auf **Beenden**. Bis alle Instanzen beendet wurden, kann unter Umständen eine Minute vergehen.
4. Installieren Sie die neueste Version des [Azure Cosmos-Emulators](https://aka.ms/cosmosdb-emulator).
5. Starten Sie den Emulator mit dem PartitionCount-Flag, indem Sie einen Wert <= 250 festlegen. Beispiel: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Steuern des Emulators

Der Emulator bietet ein PowerShell-Modul zum Starten, Beenden und Deinstallieren des Diensts und zum Abrufen des Dienststatus. Führen Sie das folgende Cmdlet aus, um das PowerShell-Modul zu verwenden:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

oder platzieren Sie das `PSModules`-Verzeichnis auf Ihrem `PSModulesPath`, und importieren Sie es wie in folgendem Befehl gezeigt:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
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

## <a name="running-on-docker"></a>Ausführen unter Docker

Der Azure Cosmos-Emulator kann unter Docker für Windows ausgeführt werden. Der Emulator funktioniert nicht in Docker für Oracle Linux.

Nachdem [Docker für Windows](https://www.docker.com/docker-windows) installiert ist, wechseln Sie zu den Windows-Containern, indem Sie mit der rechten Maustaste auf der Symbolleiste auf das Docker-Symbol klicken und **Switch to Windows containers** (Zu Windows-Containern wechseln) auswählen.

Rufen Sie als Nächstes das Emulator-Image per Pullvorgang aus dem Docker-Hub ab, indem Sie in Ihrer bevorzugten Shell den folgenden Befehl ausführen.

```bash
docker pull microsoft/azure-cosmosdb-emulator
```
Führen Sie die folgenden Befehle aus, um das Image zu starten:

Eingabe über die Befehlszeile:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator
```

Über PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator

```

Die Antwort kann wie folgt aussehen:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Verwenden Sie jetzt den Endpunkt- und den Hauptschlüssel aus der Antwort im Client, und importieren Sie das SSL-Zertifikat in den Host. Führen Sie an einer Eingabeaufforderung mit Administratorberechtigungen Folgendes aus, um das SSL-Zertifikat zu importieren:

Eingabe über die Befehlszeile:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Über PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Wenn Sie die interaktive Shell nach dem Starten des Emulators schließen, wird der Container des Emulators heruntergefahren.

Navigieren Sie in Ihrem Browser zu folgender URL, um den Daten-Explorer zu öffnen. Der Emulator-Endpunkt wird in der oben gezeigten Antwortnachricht bereitgestellt.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie die folgenden Tipps zum Behandeln von Problemen mit dem Azure Cosmos-Emulator:

- Wenn Sie eine neue Version des Emulators installiert haben und Fehler auftreten, setzen Sie Ihre Daten zurück. Klicken Sie hierzu mit der rechten Maustaste auf der Taskleiste auf das Symbol „Azure Cosmos-Emulator“ und anschließend auf „Daten zurücksetzen“. Wenn dies die Fehler nicht behebt, können Sie den Emulator und gegebenenfalls ältere Versionen des Emulators deinstallieren, das Verzeichnis „C:\Program files\Azure Cosmos DB Emulator“ entfernen und den Emulator neu installieren. Anweisungen finden Sie unter [Deinstallieren des lokalen Emulators](#uninstall).

- Bei einem Absturz des Azure Cosmos-Emulators erfassen Sie Dumpdateien aus dem Ordner „%LOCALAPPDATA%\CrashDumps“, komprimieren sie und fügen sie einer E-Mail an [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) als Anlage hinzu.

- Wenn es in `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` zu Abstürzen kommt, könnte dies ein Anzeichen dafür sein, dass sich die Leistungsindikatoren beschädigt sind. In der Regel wird dieses Problem durch die Ausführung des folgenden Befehls über die Eingabeaufforderung des Administrators gelöst:

  ```cmd
  lodctr /R
   ```

- Wenn ein Verbindungsproblem auftritt, [sammeln Sie Ablaufverfolgungsdateien](#trace-files), komprimieren sie und fügen sie einer E-Mail an [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) als Anhang hinzu.

- Wenn die Meldung **Dienst nicht verfügbar** angezeigt wird, bedeutet das, dass der Emulator den Netzwerkstapel möglicherweise nicht initialisieren kann. Überprüfen Sie, ob Sie den sicheren Pulse-Client oder den Juniper Networks-Client installiert haben. Die zugehörigen Treiber für Netzwerkfilter könnten das Problem verursachen. Durch Deinstallieren der Netzwerkfiltertreiber von Drittanbietern wird das Problem in der Regel behoben. Alternativ können Sie den Emulator auch mit „/DisableRIO“ starten, wodurch die Netzwerkkommunikation des Emulators auf reguläres Winsock umgeschaltet wird. 

- Wenn Ihr Computer während der Ausführung des Emulators in den Ruhezustand versetzt wird oder Betriebssystemupdates ausführt, wird möglicherweise die Meldung **Der Dienst ist zurzeit nicht verfügbar** angezeigt. Setzen Sie die Daten des Emulators zurück, indem Sie mit der rechten Maustaste auf das Symbol klicken, das im Windows-Infobereich angezeigt wird und **Daten zurücksetzen** auswählen.

### <a id="trace-files"></a>Sammeln von Ablaufverfolgungsdateien

Zum Sammeln von Debugablaufverfolgungen führen Sie die folgenden Befehle an einer Administratoreingabeaufforderung aus:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Beobachten Sie die Taskleiste, um sicherzugehen, dass das Programm beendet wurde; dies kann eine Minute dauern. Sie können auch einfach auf der Benutzeroberfläche des Azure Cosmos-Emulators auf **Beenden** klicken.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduzieren des Problems Wenn der Daten-Explorer nicht funktioniert, müssen Sie nur einige Sekunden warten, bis der Browser geöffnet wird, um den Fehler zu erfassen.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navigieren Sie zu `%ProgramFiles%\Azure Cosmos DB Emulator`, und suchen Sie die Datei „docdbemulator_000001.etl“.
7. Senden die ETL-Datei zusammen mit Reproduktionsschritten zum Debuggen an [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a id="uninstall"></a>Deinstallieren des lokalen Emulators

1. Schließen Sie alle geöffneten Instanzen des lokalen Emulators, indem Sie mit der rechten Maustaste auf der Taskleiste auf das Symbol für den Azure Cosmos-Emulator klicken und anschließend auf „Beenden“ klicken. Bis alle Instanzen beendet wurden, kann unter Umständen eine Minute vergehen.
2. Geben Sie in das Windows-Suchfeld **Apps & Features** ein, und klicken Sie auf das Ergebnis **Apps & Features (Systemeinstellungen)**.
3. Scrollen Sie in der Liste der Apps zu **Azure Cosmos DB-Emulator**, wählen ihn aus, und klicken Sie auf **Deinstallieren**. Bestätigen Sie den Vorgang, und klicken Sie erneut auf **Deinstallieren**.
4. Wenn die App deinstalliert ist, navigieren Sie zu `%LOCALAPPDATA%\CosmosDBEmulator` und löschen den Ordner.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie den lokalen Emulator für die kostenlose lokale Entwicklung verwenden. Sie können nun mit dem nächsten Tutorial fortfahren und sich darüber informieren, wie Sie SSL-Zertifikate für den Emulator exportieren.

> [!div class="nextstepaction"]
> [Exportieren der Azure Cosmos-Emulatorzertifikate](local-emulator-export-ssl-certificates.md)
