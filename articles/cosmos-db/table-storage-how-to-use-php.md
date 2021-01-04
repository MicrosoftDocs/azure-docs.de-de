---
title: Verwenden des Azure Table Storage-Diensts oder der Azure Cosmos DB-Tabellen-API mit PHP
description: Speichern Sie unter Verwendung von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über PHP strukturierte Daten in der Cloud.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 07/23/2020
ms.openlocfilehash: 9d059c899e4a64d4d2c1b880b2a1d0f89258f33b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079630"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>Verwenden des Azure Table Storage-Diensts oder der Azure Cosmos DB-Tabellen-API über PHP
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In diesem Artikel erfahren Sie, wie Sie Tabellen erstellen, Daten speichern und CRUD-Vorgänge für die Daten ausführen. Wählen Sie entweder den Azure Table-Dienst oder die Azure Cosmos DB-Tabellen-API aus. Die Beispiele sind in PHP geschrieben und greifen auf die [Azure Table Storage-Clientbibliothek für PHP][download] zurück. Die besprochenen Szenarien umfassen das **Erstellen und Löschen einer Tabelle** sowie das **Einfügen, Löschen und Abfragen von Entitäten in einer Tabelle**. Weitere Informationen zum Azure-Tabellenspeicherdienst finden Sie im Abschnitt [Nächste Schritte](#next-steps) .

## <a name="create-an-azure-service-account"></a>Erstellen eines Azure-Dienstkontos

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Erstellen eines Azure-Speicherkontos**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Erstellen eines Kontos für die Azure Cosmos DB-Tabellen-API**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Erstellen einer PHP-Anwendung

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung für den Zugriff auf den Table Storage-Dienst oder die Azure Cosmos DB-Tabellen-API sind Verweise auf Klassen im Azure Table Storage SDK für PHP innerhalb Ihres Codes. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In dieser Anleitung verwenden Sie Features des Table Storage-Diensts oder von Azure Cosmos DB, die lokal aus einer PHP-Anwendung heraus oder im Code aufgerufen werden, der in einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird.

## <a name="get-the-client-library"></a>Abrufen der Clientbibliothek

1. Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens „composer.json“, und fügen Sie in dieser den folgenden Code ein:
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. Laden Sie [composer.phar](https://getcomposer.org/composer.phar) in das Stammverzeichnis herunter. 
3. Öffnen Sie eine Eingabeaufforderung, und führen Sie im Stammverzeichnis Ihrer Projekts folgenden Befehl aus:
   ```
   php composer.phar install
   ```
   Navigieren Sie alternativ zur [Azure Table Storage-Clientbibliothek für PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) auf GitHub, um den Quellcode zu klonen.

## <a name="add-required-references"></a>Hinzufügen der erforderlichen Verweise

Um den Table Storage-Dienst oder die Azure Cosmos DB-APIs verwenden zu können, müssen Sie die folgenden Aktionen ausführen:

* Verweisen Sie mithilfe der [require_once][require_once]-Anweisung auf die Autoloaderdatei.
* Verweisen Sie auf alle Klassen, die Sie verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **TableRestProxy** -Klasse referenziert wird.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt. Jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

## <a name="add-your-connection-string"></a>Hinzufügen der Verbindungszeichenfolge

Sie können entweder eine Verbindung mit dem Azure-Speicherkonto oder dem Konto für die Azure Cosmos DB-Tabellen-API herstellen. Rufen Sie die Verbindungszeichenfolge basierend auf dem jeweils verwendeten Kontotyp ab.

### <a name="add-a-storage-table-service-connection"></a>Hinzufügen einer Table Storage-Dienstverbindung

Um einen Table Storage-Dienstclient zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Verbindungszeichenfolge für den Table Storage-Dienst lautet:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

### <a name="add-a-storage-emulator-connection"></a>Hinzufügen einer Speicheremulatorverbindung

So greifen Sie auf den Storage-Emulator zu

```php
UseDevelopmentStorage = true
```

### <a name="add-an-azure-cosmos-db-connection"></a>Hinzufügen einer Azure Cosmos DB-Verbindung

Um einen Azure Cosmos DB-Tabellenclient zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Verbindungszeichenfolge für Azure Cosmos DB lautet:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

Um einen Azure Table Storage-Dienstclient oder einen Azure Cosmos DB-Client zu erstellen, müssen Sie die **TableRestProxy** -Klasse verwenden. Ihre Möglichkeiten:

* die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
* den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
  * Standardmäßig verfügt sie über Unterstützung für eine externe Quelle – Umgebungsvariablen.
  * Sie können neue Quellen durch Erweitern der `ConnectionStringSource`-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Erstellen einer Tabelle

Mithilfe eines **TableRestProxy** -Objekts können Sie eine Tabelle mit der **createTable** -Methode erstellen. Wenn Sie eine Tabelle erstellen, können Sie das Timeout für den Tabellenspeicherdienst festlegen. (Weitere Informationen zum Timeout für den Tabellenspeicherdienst finden Sie unter [Festlegen von Timeouts für Tabellenspeicherdienstvorgänge][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Informationen zu Einschränkungen für Tabellennamen finden Sie unter [Grundlegendes zum Tabellenspeicherdienst-Datenmodell][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

Um einer Tabelle eine Entität hinzuzufügen, erstellen Sie ein neues **Entity** -Objekt und übergeben es an **TableRestProxy->insertEntity**. Beachten Sie, dass Sie beim Erstellen einer Entität einen `PartitionKey` und einen `RowKey` angeben müssen. Dabei handelt es sich um eindeutige Kennzeichner für eine Entität und um Werte, die viel schneller als andere Entitätseigenschaften abgefragt werden können. Das System verwendet `PartitionKey`, um die Entitäten der Tabelle automatisch über viele Speicherknoten zu verteilen. Entitäten mit dem gleichen `PartitionKey` werden auf dem gleichen Knoten gespeichert. (Vorgänge für mehrere Entitäten, die auf dem gleichen Knoten gespeichert sind, haben eine höhere Leistung als Vorgänge für Entitäten, die auf verschiedenen Knoten gespeichert sind.) Der `RowKey` ist die eindeutige ID einer Entität innerhalb einer Partition.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Informationen zu Tabelleneigenschaften und -typen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell][table-data-model].

Die **TableRestProxy** -Klasse bietet zwei alternative Methoden zum Einfügen von Entitäten: **insertOrMergeEntity** und **insertOrReplaceEntity**. Um diese Methoden zu verwenden, erstellen Sie ein neues **Entity** -Objekt, und übergeben Sie es als Parameter an eine Methode. Jede Methode fügt die Entität ein, falls sie nicht vorhanden ist. Falls die Entität bereits vorhanden ist, aktualisiert **insertOrMergeEntity** die Eigenschaftswerte, falls die Eigenschaften bereits vorhanden sind, und fügt neue Werte hinzu, falls sie nicht vorhanden sind, während **insertOrReplaceEntity** eine vorhandene Entität vollständig ersetzt. Das folgende Beispiel zeigt die Verwendung von **insertOrMergeEntity**. Wenn die Entität mit dem `PartitionKey` "tasksSeattle" und dem `RowKey` "1" noch nicht vorhanden ist, wird sie eingefügt. Falls sie jedoch zuvor eingefügt wurde (wie in dem Beispiel oben gezeigt), wird die `DueDate`-Eigenschaft aktualisiert, und die `Status`-Eigenschaft wird hinzugefügt. Auch die Eigenschaften `Description` und `Location` werden aktualisiert, allerdings mit Werten, mit denen sie effektiv unverändert bleiben. Falls die letzten zwei Eigenschaft nicht wie im Beispiel gezeigt hinzugefügt würden, sondern auf der Zielentität vorhanden wären, blieben ihre vorhandenen Werte unverändert.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Abrufen einer einzelnen Entität

Mit der **TableRestProxy->getEntity** -Methode können Sie eine einzelne Entität durch Abfragen der Werte für `PartitionKey` und `RowKey` abrufen. Im Beispiel unten werden der Partitionsschlüssel `tasksSeattle` und der Zeilenschlüssel `1` an die **getEntity** -Methode übergeben.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Abrufen aller Entitäten einer Partition

Entitätsabfragen werden mithilfe von Filtern erstellt. (Weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten][filters].) Um alle Entitäten in einer Partition abzurufen, verwenden Sie den Filter „PartitionKey eq *Partitionsname* “. Das folgende Beispiel zeigt, wie alle Entitäten in der Partition `tasksSeattle` durch Übergeben eines Filters an die **queryEntities** -Methode abgerufen werden.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Abrufen einer Teilmenge von Entitäten in einer Partition

Das Muster, das im vorherigen Beispiel verwendet wird, kann auch verwendet werden, um eine Teilmenge der Entitäten in einer Partition abzurufen. Die Teilmenge der Entitäten, die Sie abrufen, wird durch den verwendeten Filter bestimmt. (Weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten][filters].) Das folgende Beispiel zeigt, wie ein Filter zum Abrufen aller Entitäten mit bestimmten Werten für `Location` und `DueDate` vor einem angegebenen Datum verwendet wird.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Abrufen einer Teilmenge von Entitätseigenschaften

Eine Abfrage kann eine Teilmenge der Entitätseigenschaften abrufen. Diese Technik namens *Projektion* reduziert die Bandbreite und kann die Abfrageleistung, besonders für große Entitäten, verbessern. Um eine abzurufende Eigenschaft anzugeben, übergeben Sie den Namen der Eigenschaft an die **Query->addSelectField** -Methode. Sie können diese Methode mehrfach aufrufen, um weitere Eigenschaften hinzuzufügen. Nach dem Ausführen von **TableRestProxy->queryEntities** haben die zurückgegebenen Entitäten nur die ausgewählten Eigenschaften. (Wenn Sie eine Teilmenge der Tabellenentitäten zurückgeben möchten, verwenden Sie einen Filter, wie in den Abfragen oben gezeigt wird.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Aktualisieren einer Entität

Sie können eine vorhandene Entität mithilfe der Methoden **Entity->setProperty** und **Entity->addProperty** für die Entität und des darauf folgenden Aufrufs von **TableRestProxy->updateEntity** aktualisieren. Das folgende Beispiel ruft eine Entität ab, ändert eine Eigenschaft, entfernt eine andere Eigenschaft und fügt eine neue Eigenschaft hinzu. Beachten Sie, dass Sie eine Eigenschaft durch Festlegen ihres Werts auf **null** entfernen können.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Löschen einer Entität

Um eine Entität zu löschen, übergeben Sie den Tabellennamen und die Werte für `PartitionKey` und `RowKey` der Entität an die **TableRestProxy->deleteEntity** -Methode.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Für Parallelitätsüberprüfungen können Sie das ETag für eine zu löschende Entität mithilfe der **DeleteEntityOptions->setEtag** -Methode und durch Übergeben des **DeleteEntityOptions** -Objekts an **deleteEntity** als vierten Parameter festlegen.

## <a name="batch-table-operations"></a>Batch-Tabellenvorgänge

Mithilfe der **TableRestProxy->batch** -Methode können Sie mehrere Vorgänge in einer einzelnen Anforderung ausführen. Das Muster umfasst hier das Hinzufügen von Vorgängen zum **BatchRequest** -Objekt und die darauf folgende Übergabe des **BatchRequest** -Objekts an die **TableRestProxy->batch** -Methode. Um einem **BatchRequest** -Objekt einen Vorgang hinzuzufügen, können Sie eine der folgenden Methoden mehrfach aufrufen:

* **addInsertEntity** (fügt einen insertEntity-Vorgang hinzu)
* **addUpdateEntity** (fügt einen updateEntity-Vorgang hinzu)
* **addMergeEntity** (fügt einen mergeEntity-Vorgang hinzu)
* **addInsertOrReplaceEntity** (fügt einen insertOrReplaceEntity-Vorgang hinzu)
* **addInsertOrMergeEntity** (fügt einen insertOrMergeEntity-Vorgang hinzu)
* **addDeleteEntity** (fügt einen deleteEntity-Vorgang hinzu)

Das folgende Beispiel zeigt die Ausführung von **insertEntity** - und **deleteEntity** -Vorgängen in einer einzelnen Anforderung. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Weitere Informationen zu Batch-Tabellenvorgängen finden Sie unter [Durchführen von Entitätsgruppentransaktionen][entity-group-transactions].

## <a name="delete-a-table"></a>Löschen einer Tabelle

Um eine Tabelle zu löschen, übergeben Sie den Tabellennamen an die **TableRestProxy->deleteTable** -Methode.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Azure Table Storage-Diensts und von Azure Cosmos DB vertraut gemacht haben, finden Sie unter den Links weitere Informationen.

* Beim [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.

* [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: /rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: /rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: /rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: /rest/api/storageservices/Performing-Entity-Group-Transactions
