---
title: Verwenden von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über Node.js
description: Speichern Sie unter Verwendung von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über Node.js strukturierte Daten in der Cloud.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079698"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Verwenden von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über Node.js
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In diesem Artikel erfahren Sie, wie Sie Tabellen erstellen, Daten speichern und CRUD-Vorgänge für die Daten ausführen. Wählen Sie entweder den Azure Table-Dienst oder die Azure Cosmos DB-Tabellen-API aus. Die Beispiele sind in Node.js geschrieben.

## <a name="create-an-azure-service-account"></a>Erstellen eines Azure-Dienstkontos

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Erstellen eines Azure-Speicherkontos**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Erstellen eines Kontos für die Azure Cosmos DB-Tabellen-API**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Konfigurieren Ihrer Anwendung für den Zugriff auf Azure Storage oder die Azure Cosmos DB-Tabellen-API

Um Azure Storage oder Azure Cosmos DB verwenden zu können, müssen Sie das Azure Storage SDK für Node.js herunterladen. Es enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Verwenden des Node-Paket-Managers (NPM) zum Installieren des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zum Ordner, in dem Sie die Beispielanwendung erstellt haben.
2. Geben Sie **npm install azure-storage** in das Befehlsfenster ein. Die Ausgabe des Befehls ähnelt dem folgenden Beispiel.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner **node_modules** erstellt wurde. In diesem Ordner finden Sie das Paket **azure-storage** mit den Bibliotheken, die Sie benötigen, um auf den Speicher zuzugreifen.

### <a name="import-the-package"></a>Importieren des Pakets

Fügen Sie den folgenden Code am Anfang der Datei **server.js** in der Anwendung hinzu:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Hinzufügen der Verbindungszeichenfolge

Sie können entweder eine Verbindung mit dem Azure-Speicherkonto oder dem Konto für die Azure Cosmos DB-Tabellen-API herstellen. Rufen Sie die Verbindungszeichenfolge basierend auf dem jeweils verwendeten Kontotyp ab.

### <a name="add-an-azure-storage-connection"></a>Hinzufügen einer Azure Storage-Verbindung

Das Azure-Modul liest in den Umgebungsvariablen AZURE_STORAGE_ACCOUNT und AZURE_STORAGE_ACCESS_KEY oder AZURE_STORAGE_CONNECTION_STRING die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure Storage-Konto erforderlich sind. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen die Kontoinformationen beim Aufruf von `TableService` angegeben werden. Der folgende Code erstellt beispielsweise ein `TableService`-Objekt:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Hinzufügen einer Azure Cosmos DB-Verbindung

Wenn Sie eine Verbindung zu Azure Cosmos DB hinzufügen möchten, erstellen Sie ein `TableService`-Objekt, und geben Sie Ihren Kontonamen, den Primärschlüssel und den Endpunkt an. Sie können diese Werte aus **Einstellungen** > **Verbindungszeichenfolge** im Azure-Portal für Ihr Cosmos DB-Konto kopieren. Beispiel:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Erstellen einer Tabelle

Mit dem folgenden Code wird ein `TableService`-Objekt erstellt und zum Erstellen einer neuen Tabelle verwendet.

```javascript
var tableSvc = azure.createTableService();
```

Wenn Sie `createTableIfNotExists` aufrufen, wird eine neue Tabelle mit dem angegebenen Namen erstellt, sofern sie nicht bereits vorhanden ist. Im folgenden Beispiel wird eine neue Tabelle namens 'mytable' erstellt, wenn diese noch nicht vorhanden ist:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

Der Wert für `result.created` lautet `true`, wenn eine neue Tabelle erstellt wird, und `false`, wenn die Tabelle bereits vorhanden ist. `response` enthält Informationen über die Anforderung.

### <a name="apply-filters"></a>Anwenden von Filtern

Sie können optionale Filter auf Vorgänge anwenden, die mithilfe von `TableService` ausgeführt werden. Zu Filtervorgängen zählen z.B. Protokollierung, automatische Wiederholung usw. Filter sind Objekte, die eine Methode mit der Signatur implementieren:

```javascript
function handle (requestOptions, next)
```

Nachdem die Vorverarbeitung der Anforderungsoptionen abgeschlossen ist, muss die Methode **next** aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

```javascript
function (returnObject, finalCallback, next)
```

Nach der Verarbeitung von `returnObject` (die Antwort auf die an den Server gesendete Anforderung) muss der Rückruf entweder `next` aufrufen (sofern vorhanden), um weitere Filter zu verarbeiten, oder andernfalls einfach `finalCallback` aufrufen, um den Dienstaufruf zu beenden.

Das Azure SDK für Node.js enthält zwei Filter zur Implementierung von Wiederholungslogik: `ExponentialRetryPolicyFilter` und `LinearRetryPolicyFilter`. Der folgende Code erstellt ein Objekt vom Typ `TableService`, das `ExponentialRetryPolicyFilter` verwendet:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

Um eine Entität hinzuzufügen, erstellen Sie zunächst ein Objekt, das die Entitätseigenschaften definiert. Alle Entitäten müssen **PartitionKey** und **RowKey** enthalten, die eindeutige Bezeichner für die Entität darstellen.

* **PartitionKey** : bestimmt die Partition, in der die Entität gespeichert ist.
* **RowKey** : identifiziert die Entität innerhalb der Partition eindeutig.

**PartitionKey** und **RowKey** müssen Zeichenfolgenwerte sein. Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Nachfolgend sehen Sie ein Beispiel für die Definition einer Entität. **dueDate** ist als Typ `Edm.DateTime` definiert. Die Angabe des Typs ist optional. Nicht angegebene Typen werden abgeleitet.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Für jeden Datensatz gibt es auch ein `Timestamp`-Feld, das von Azure festgelegt wird, wenn eine Entität eingefügt oder aktualisiert wird.

Sie können Entitäten auch mit dem `entityGenerator` erstellen. Im folgenden Beispiel wird dieselbe Task-Entität mit dem `entityGenerator` erstellt.

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Um eine Entität der Tabelle hinzuzufügen, übergeben Sie das Entitätsobjekt der `insertEntity`-Methode.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Ist der Vorgang erfolgreich, enthält `result` das [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) des eingefügten Datensatzes, und `response` enthält Informationen zum Vorgang.

Beispielantwort:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Standardmäßig gibt `insertEntity` die eingefügte Entität nicht als Teil der `response`-Informationen zurück. Wenn Sie weitere Vorgänge mit der Entität ausführen oder die Informationen zwischenspeichern möchten, können Sie sie als Teil von `result`zurückgeben. Hierzu aktivieren Sie `echoContent` wie folgt:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Aktualisieren einer Entität

Es sind mehrere Methoden zum Aktualisieren einer vorhandenen Entität vorhanden:

* `replaceEntity`: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird.
* `mergeEntity`: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftswerte mit der vorhandenen Entität.
* `insertOrReplaceEntity`: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.
* `insertOrMergeEntity`: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftswerte mit der vorhandenen Entität. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

Das folgende Beispiel zeigt, wie eine Entität mit `replaceEntity` aktualisiert wird:

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Standardmäßig wird beim Aktualisieren einer Entität nicht überprüft, ob die aktualisierten Daten zuvor von einem anderen Prozess geändert wurden. Um gleichzeitige Aktualisierungen zu unterstützen, gehen Sie wie folgt vor:
>
> 1. Rufen Sie das Etag des aktualisierten Objekts ab. Es wird im Rahmen der `response` für jeden entitätsbezogenen Vorgang zurückgegeben und kann durch `response['.metadata'].etag` abgerufen werden.
> 2. Wenn Sie einen Aktualisierungsvorgang für eine Entität ausführen, sollten Sie der neuen Entität die zuvor abgerufenen ETag-Informationen hinzufügen. Beispiel:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Führen Sie den Aktualisierungsvorgang aus. Wurde die Entität seit dem Abruf des ETag-Werts beispielsweise durch eine andere Instanz Ihrer Anwendung geändert, wird ein `error` zurückgegeben. Der Wert besagt, dass die in der Anforderung angegebene Aktualisierungsbedingung nicht erfüllt ist.
>
>

Mit `replaceEntity` und `mergeEntity` schlägt die Aktualisierung fehl, wenn die Entität, die aktualisiert wird, nicht vorhanden ist. Wenn Sie eine Entität unabhängig davon, ob sie bereits vorhanden ist, speichern möchten, verwenden Sie `insertOrReplaceEntity` oder `insertOrMergeEntity`.

Bei erfolgreichen Aktualisierungsvorgängen enthält das `result` das **ETag** der aktualisierten Entität.

## <a name="work-with-groups-of-entities"></a>Arbeiten mit Gruppen von Entitäten

Gelegentlich ist es sinnvoll, mehrere Vorgänge zusammen in einem Batch zu senden, um die atomische Verarbeitung durch den Server sicherzustellen. Dazu erstellen Sie mit der `TableBatch`-Klasse einen Batch und führen dann mit der `executeBatch`-Methode von `TableService` die Batchvorgänge aus.

 Im folgenden Beispiel wird gezeigt, wie zwei Entitäten in einem Stapel übermittelt werden:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

Bei erfolgreichen Batchvorgängen enthält `result` Informationen für jeden Vorgang im Batch.

### <a name="work-with-batched-operations"></a>Arbeiten mit Batchvorgängen

Vorgänge, die zu einem Batch hinzugefügt wurden, können Sie durch Anzeigen der Eigenschaft `operations` überprüfen. Sie können auch die folgenden Methoden verwenden, um mit Vorgängen zu arbeiten.

* **clear** : löscht alle Vorgänge aus einem Batch.
* **getOperations** : ruft einen Vorgang aus dem Batch ab.
* **hasOperations** : gibt „true“ zurück, wenn der Batch Vorgänge enthält.
* **removeOperations** : entfernt einen Vorgang.
* **size** : gibt die Anzahl von Vorgängen im Batch zurück.

## <a name="retrieve-an-entity-by-key"></a>Abrufen einer Entität nach Schlüssel

Wenn Sie eine bestimmte Entität basierend auf **PartitionKey** und **RowKey** zurückgeben möchten, verwenden Sie die **retrieveEntity** -Methode.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Nach Abschluss des Vorgangs enthält `result` die Entität.

## <a name="query-a-set-of-entities"></a>Abfragen einer Gruppe von Entitäten

Um eine Tabelle abzufragen, erstellen Sie mithilfe des **TableQuery-Objekts** einen Abfrageausdruck mit folgenden Klauseln:

* **select** : die von der Abfrage zurückgegebenen Felder.
* **where** : die where-Klausel.

  * **and** : eine `and` where-Bedingung.
  * **or** : eine `or` where-Bedingung.
* **top** : die Anzahl der abzurufenden Elemente.

Im folgenden Beispiel wird eine Abfrage erstellt, die die ersten fünf Elemente mit dem PartitionKey „hometasks“ zurückgibt.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Da **select** nicht verwendet wird, werden alle Felder zurückgegeben. Verwenden Sie **queryEntities** , um die Abfrage für eine Tabelle auszuführen. Im folgenden Beispiel werden mit der Abfrage Entitäten aus 'mytable' zurückgegeben.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Nach erfolgreicher Ausführung enthält `result.entries` ein Array von Entitäten, die die Abfrage erfüllen. Wenn nicht alle Entitäten von der Abfrage zurückgegeben werden konnten, ist `result.continuationToken` ungleich *Null* und kann als dritter Parameter von **queryEntities** verwendet werden, um weitere Ergebnisse abzurufen. Verwenden Sie in der ersten Abfrage *null* als dritten Parameter.

### <a name="query-a-subset-of-entity-properties"></a>Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Felder einer Entität aufgerufen werden.
Somit wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Verwenden Sie die **select** -Klausel, und übergeben Sie die Namen der zurückzugebenden Felder. Die folgende Abfrage gibt beispielsweise nur die Felder **description** und **dueDate** zurück.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Löschen einer Entität

Sie können eine Entität unter Verwendung ihres Partitions- und Zeilenschlüssels löschen. In diesem Beispiel enthält das Objekt **task1** die **RowKey** - und **PartitionKey** -Werte der zu löschenden Entität. Dann wird das Objekt der **deleteEntity** -Methode übergeben.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Es ist ratsam, beim Löschen von Elementen ETags zu verwenden, um sicherzustellen, dass das Element nicht von einem anderen Prozess geändert wurde. Informationen zum Verwenden von ETags finden Sie unter [Aktualisieren einer Entität](#update-an-entity) .
>
>

## <a name="delete-a-table"></a>Löschen einer Tabelle

Mit dem folgenden Code wird eine Tabelle aus einem Speicherkonto gelöscht.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Wenn Sie nicht wissen, ob die Tabelle vorhanden ist, verwenden Sie **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Verwenden von Fortsetzungstoken

Wenn Sie in Tabellen umfangreiche Ergebnismengen abfragen, sollten Sie nach Fortsetzungstoken suchen. Möglicherweise sind umfangreiche Datenmengen für die Abfrage verfügbar, die Sie nicht erkennen können, wenn ein Fortsetzungstoken vorhanden ist.

Das beim Abfragen von Entitäten zurückgegebene **results** -Objekt legt eine `continuationToken`-Eigenschaft fest, wenn ein derartiges Token vorhanden ist. Diese können Sie dann beim Durchführen einer Abfrage verwenden, um die Partitions- und Tabellenentitäten zu durchlaufen.

Bei der Abfrage können Sie ein `continuationToken`-Parameter zwischen der query-Objektinstanz und der callback-Funktion bereitstellen:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Das `continuationToken`-Objekt enthält Eigenschaften wie z. B. `nextPartitionKey`, `nextRowKey` und `targetLocation`, die zum Durchlaufen der Ergebnisse verwendet werden können.

Sie können auch `top` zusammen mit `continuationToken` verwenden, um die Seitengröße festzulegen.

## <a name="work-with-shared-access-signatures"></a>Arbeiten mit Shared Access Signatures

Shared Access Signatures (SAS) ermöglichen auf sichere Art und Weise differenzierten Zugriff auf Tabellen, ohne Speicherkontonamen oder -schlüssel anzugeben. SAS werden häufig verwendet, um eingeschränkten Zugriff auf Ihre Daten zu bieten, beispielsweise um einer mobilen App die Abfrage von Datensätzen zu ermöglichen.

Eine vertrauenswürdige Anwendung, z. B. ein cloudbasierter Dienst, generiert mit der **generateSharedAccessSignature** -Methode des **TableService** -Objekts eine SAS und stellt sie für eine nicht vertrauenswürdige oder teilweise vertrauenswürdige Anwendung, z. B. eine mobile App, bereit. Die SAS wird mithilfe einer Richtlinie generiert, die das Anfangs- und das Enddatum der Gültigkeit der SAS sowie die Zugriffsstufe definiert, die dem Inhaber der SAS gewährt wird.

Im folgenden Beispiel wird eine neue Richtlinie für den freigegebenen Zugriff generiert, die dem SAS-Inhaber erlaubt, die Tabelle abzufragen ('r'), und 100 Minuten nach ihrer Erstellung abläuft.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Beachten Sie, dass Sie die Hostinformationen ebenfalls angeben müssen. Diese sind erforderlich, wenn der SAS-Inhaber versucht, auf die Tabelle zuzugreifen.

Die Clientanwendung verwendet die SAS dann zusammen mit **TableServiceWithSAS** , um Vorgänge für die Tabelle auszuführen. Im folgenden Beispiel wird eine Verbindung mit der Tabelle hergestellt und eine Abfrage ausgeführt. Im Artikel [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../storage/common/storage-sas-overview.md) können Sie sich das Format von tableSAS ansehen.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Da die SAS nur mit Abfragezugriff generiert wurde, wird ein Fehler zurückgegeben, wenn Sie versuchen, Entitäten einzufügen, zu aktualisieren oder zu löschen.

### <a name="access-control-lists"></a>Zugriffssteuerungslisten

Sie können auch eine Zugriffssteuerungsliste (Access Control List, ACL) verwenden, um die Zugriffsrichtlinie für eine SAS festzulegen. Dies ist nützlich, wenn Sie mehreren Clients Zugriff auf die Tabelle gewähren, aber für jeden Client andere Zugriffsrichtlinien angeben möchten.

Eine ACL wird in einem Array von Zugriffsrichtlinien implementiert, wobei jeder Richtlinie eine ID zugeordnet wird. Im folgenden Beispiel werden zwei Richtlinien definiert, eine für "user1" und eine für "user2":

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Im folgenden Beispiel wird zunächst die aktuelle ACL für die Tabelle **hometasks** abgerufen. Anschließend werden die neuen Richtlinien mit **setTableAcl** hinzugefügt. Dieser Ansatz ermöglicht Folgendes:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Nachdem die ACL festgelegt wurde, können Sie basierend auf der ID für eine Richtlinie eine SAS erstellen. Im folgenden Beispiel wird eine neue SAS für 'user2' erstellt:

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen.

* Beim [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.
* [Azure Storage SDK für Node.js](https://github.com/Azure/azure-storage-node)-Repository auf GitHub
* [Azure für Node.js-Entwickler](/azure/developer/javascript/)
* [Erstellen einer Node.js-Web-App in Azure](../app-service/quickstart-nodejs.md)
* [Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (mit Windows PowerShell)