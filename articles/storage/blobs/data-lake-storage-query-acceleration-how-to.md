---
title: Filtern von Daten mithilfe der Abfragebeschleunigung für Azure Data Lake Storage | Microsoft-Dokumentation
description: Verwenden Sie die Abfragebeschleunigung, um eine Teilmenge von Daten aus Ihrem Speicherkonto abzurufen.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 5d5278c33c24c7f95459b9c121bca9e960615b9c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260179"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtern von Daten mithilfe der Abfragebeschleunigung für Azure Data Lake Storage

In diesem Artikel wird gezeigt, wie Sie mithilfe der Abfragebeschleunigung eine Teilmenge von Daten aus Ihrem Speicherkonto abrufen. 

Die Abfragebeschleunigung ermöglicht es Anwendungen und Analyseframeworks, die Datenverarbeitung drastisch zu optimieren. Dabei werden nur die Daten abgerufen, die für die Durchführung eines bestimmten Vorgangs erforderlich sind. Weitere Informationen finden Sie unter [Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Abonnement, um auf Azure Storage zuzugreifen. Wenn Sie noch kein Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Ein Speicherkonto vom Typ **Allgemein V2**. Informationen finden Sie unter [Erstellen eines Speicherkontos](../common/storage-account-create.md).

- Wählen Sie eine Registerkarte aus, um SDK-spezifische Voraussetzungen anzuzeigen.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Nicht zutreffend

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true), Version 8 oder höher

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > In diesem Artikel wird davon ausgegangen, dass Sie ein Java-Projekt mithilfe von Apache Maven erstellt haben. Ein Beispiel zum Erstellen eines Projekts mithilfe von Apache Maven finden Sie unter [Einrichten](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 oder höher.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Für die Verwendung des Node.js SDK sind keine weiteren Voraussetzungen zu erfüllen.

---

## <a name="enable-query-acceleration"></a>Aktivieren der Abfragebeschleunigung

Wenn Sie die Abfragebeschleunigung verwenden möchten, müssen Sie dieses Feature in Ihrem Abonnement registrieren. Nachdem Sie sich vergewissert haben, dass das Feature registriert ist, müssen Sie den Azure Storage-Ressourcenanbieter registrieren. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Schritt 1: Registrieren des Features Abfragebeschleunigung

Wenn Sie die Abfragebeschleunigung verwenden möchten, müssen Sie zunächst dieses Feature in Ihrem Abonnement registrieren. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Öffnen Sie ein Windows PowerShell-Befehlsfenster.

1. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement fest.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

3. Registrieren Sie das Feature Abfragebeschleunigung mithilfe des Befehls [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature).

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Öffnen Sie [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-Befehlszeilenschnittstelle lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

3. Registrieren Sie das Feature Abfragebeschleunigung mithilfe des Befehls [az feature register](/cli/azure/feature#az-feature-register).

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Schritt 2: Überprüfen, ob das Feature registriert ist

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Um zu überprüfen, ob die Registrierung abgeschlossen wurde, verwenden Sie den Befehl [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature).

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um zu überprüfen, ob die Registrierung abgeschlossen wurde, verwenden Sie den Befehl [az feature](/cli/azure/feature#az-feature-show).

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Schritt 3: Registrieren des Azure Storage-Ressourcenanbieters

Nachdem Ihre Registrierung genehmigt wurde, müssen Sie den Azure Storage-Ressourcenanbieter erneut registrieren. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Um den Ressourcenanbieter zu registrieren, verwenden Sie den Befehl [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider).

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Registrieren des Ressourcenanbieters den Befehl [az provider register](/cli/azure/provider#az-provider-register).

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Einrichten Ihrer Umgebung

### <a name="step-1-install-packages"></a>Schritt 1: Installieren von Paketen 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installieren Sie das Az-Modul, Version 4.6.0 oder höher.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Führen Sie zum Aktualisieren von einer älteren Version von Az den folgenden Befehl aus:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie mit dem Befehl `cd` in Ihren Projektordner. Beispiel:

   ```console
   cd myProject
   ```

2. Installieren Sie Version `12.5.0-preview.6` oder höher der Azure Blob Storage-Clientbibliothek für das .NET-Paket mit dem Befehl `dotnet add package`. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. In den Beispielen in diesem Artikel wird eine CSV-Datei mithilfe der Bibliothek [CsvHelper](https://www.nuget.org/packages/CsvHelper/) analysiert. Führen Sie den folgenden Befehl aus, um diese Bibliothek zu verwenden.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Öffnen Sie die Datei *pom.xml* des Projekts in einem Text-Editor. Fügen Sie der Gruppe der Abhängigkeiten das folgende Abhängigkeitselement hinzu: 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Installieren Sie die Azure Data Lake Storage-Clientbibliothek für Python mithilfe von [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installieren Sie die Data Lake-Clientbibliothek für JavaScript, indem Sie ein Terminalfenster öffnen und den folgenden Befehl eingeben.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Schritt 2: Hinzufügen von Anweisungen

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nicht zutreffend

#### <a name="net"></a>[.NET](#tab/dotnet)

Fügen Sie die folgenden `using`-Anweisungen am Anfang Ihrer Codedatei hinzu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Bei der Abfragebeschleunigung werden CSV- und JSON-formatierte Daten abgerufen. Stellen Sie deshalb sicher, dass Sie die using-Anweisungen für alle CSV- oder JSON-Analysebibliotheken hinzufügen, die Sie verwenden möchten. In den Beispielen in diesem Artikel wird eine CSV-Datei mithilfe der Bibliothek [CsvHelper](https://www.nuget.org/packages/CsvHelper/) analysiert, die auf NuGet verfügbar ist. Daher werden die folgenden `using`-Anweisungen am Anfang der Codedatei hinzugefügt.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Zum Kompilieren der Beispiele, die in diesem Artikel gezeigt werden, müssen Sie auch die folgenden `using`-Anweisungen hinzufügen.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Fügen Sie die folgenden `import`-Anweisungen am Anfang Ihrer Codedatei hinzu.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Fügen Sie diese Importanweisungen am Anfang Ihrer Codedatei hinzu.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Schließen Sie das `storage-blob`-Modul ein, indem Sie die folgende Anweisung am Anfang Ihrer Codedatei einfügen. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Bei der Abfragebeschleunigung werden CSV- und JSON-formatierte Daten abgerufen. Stellen Sie deshalb sicher, dass Sie Anweisungen für alle CSV- oder JSON-Analysemodule hinzufügen, die Sie verwenden möchten. In den Beispielen in diesem Artikel wird eine CSV-Datei mithilfe des Moduls [fast-csv](https://www.npmjs.com/package/fast-csv) analysiert. Daher wird diese Anweisung am Anfang der Codedatei hinzugefügt.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Abrufen von Daten mithilfe eines Filters

Sie können SQL verwenden, um die Zeilenfilterprädikate und Spaltenprojektionen in einer Abfragebeschleunigungsanforderung anzugeben. Der folgende Code fragt eine CSV-Datei im Speicher ab und gibt alle Datenzeilen zurück, bei denen die dritte Spalte mit dem Wert `Hemingway, Ernest` übereinstimmt. 

- In der SQL-Abfrage wird das Schlüsselwort `BlobStorage` verwendet, um die abzufragende Datei anzugeben.

- Spaltenverweise werden als `_N` angegeben, wobei die erste Spalte `_1` ist. Wenn die Quelldatei eine Kopfzeile enthält, können Sie mit dem Namen, der in der Kopfzeile angegeben ist, auf Spalten verweisen. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Die asynchrone Methode `BlobQuickQueryClient.QueryAsync` sendet die Abfrage an die Abfragebeschleunigungs-API und streamt dann die Ergebnisse als [Stream](/dotnet/api/system.io.stream)-Objekt an die Anwendung zurück.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Die Methode `BlobQuickQueryClient.openInputStream()` sendet die Abfrage an die Abfragebeschleunigungs-API und streamt dann die Ergebnisse als `InputStream`-Objekt, das wie jedes andere InputStream-Objekt gelesen werden kann, an die Anwendung zurück.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

In diesem Beispiel wird die Abfrage an die Abfragebeschleunigung-API gesendet, und die Ergebnisse werden anschließend zurückgestreamt. Das `blob`-Objekt, das an die Hilfsfunktion `queryHemingway` übergeben wird, ist vom Typ [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Informationen zum Abrufen eines [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient)-Objekts finden Sie unter [Schnellstart: Verwalten von Blobs per JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Abrufen bestimmter Spalten

Sie können die Ergebnisse auf eine Teilmenge der Spalten beschränken. Auf diese Weise rufen Sie nur die Spalten ab, die zum Ausführen einer bestimmten Berechnung benötigt werden. Dadurch werden die Anwendungsleistung verbessert und Kosten reduziert, da weniger Daten über das Netzwerk übertragen werden. 

Dieser Code ruft nur die Spalte `BibNum` für alle Bücher im Dataset ab. Außerdem werden die Informationen aus der Kopfzeile in der Quelldatei verwendet, um auf Spalten in der Abfrage zu verweisen.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

Im folgenden Code werden Zeilenfilterung und Spaltenprojektionen in derselben Abfrage kombiniert. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Abfragebeschleunigung – SQL-Sprachreferenz](query-acceleration-sql-reference.md)
