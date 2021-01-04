---
title: Verwenden von Azure Cosmos DB-Tabellen-API und Azure Table Storage mit Python
description: Speichern Sie mithilfe von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API unter Verwendung von Python strukturierte Daten in der Cloud.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.custom: devx-track-python
ms.openlocfilehash: 341615bf8fe231eafa606411948ad014399b8261
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079545"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Erste Schritte mit Azure Table Storage und der Azure Cosmos DB-Tabellen-API mit Python
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Table Storage und Azure Cosmos DB sind Dienste, die strukturierte NoSQL-Daten in der Cloud speichern und einen Schlüssel-/Attributspeicher mit einem schemalosen Design bieten. Aufgrund der Schemalosigkeit von Table Storage und Azure Cosmos DB ist es einfach, Ihre Daten an die Entwicklung Ihrer Anwendungen anzupassen. Viele Arten von Anwendungen können schnell und kostengünstig auf die Table Storage- und Tabellen-API-Daten zugreifen, und die Kosten liegen in der Regel unter den Kosten herkömmlicher SQL-Lösungen für vergleichbare Datenmengen.

Mit Table Storage oder Azure Cosmos DB können Sie flexible Datasets wie Benutzerdaten für Webanwendungen, Adressbücher, Geräteinformationen und andere Arten von Metadaten speichern, die Ihr Dienst benötigt. Sie können eine beliebige Anzahl von Entitäten in einer Tabelle speichern, und ein Speicherkonto kann eine beliebige Anzahl von Tabellen enthalten (bis zur Speicherkapazitätsgrenze eines Speicherkontos).

### <a name="about-this-sample"></a>Informationen zu diesem Beispiel

In diesem Beispiel erfahren Sie, wie Sie das [Azure Cosmos DB Table SDK für Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) in allgemeinen Azure Table Storage-Szenarien verwenden. Der Name des SDK weist darauf hin, dass es für Azure Cosmos DB gedacht ist. Es kann aber sowohl mit Azure Cosmos DB als auch mit Azure Table Storage verwendet werden, jeder Dienst hat lediglich einen eindeutigen Endpunkt. Diese Szenarien werden mithilfe von Python-Beispielen untersucht, die Folgendes veranschaulichen:

* Erstellen und Löschen von Tabellen
* Einfügen und Abfragen von Entitäten
* Ändern von Entitäten

Beim Durcharbeiten der Szenarien in diesem Beispiel finden Sie bei Bedarf weiterführende Informationen in der [API-Referenz zum Azure Cosmos DB SDK für Python](/python/api/overview/azure/cosmosdb?preserve-view=true&view=azure-python).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

* [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 oder 3.6
* [Azure Cosmos DB Table SDK für Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Dieses SDK stellt eine Verbindung mit Azure Table Storage sowie mit der Tabellen-API von Azure Cosmos DB her.
* [Azure-Speicherkonto](../storage/common/storage-account-create.md) oder [Azure Cosmos DB-Konto](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Erstellen eines Azure-Dienstkontos

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Erstellen eines Azure-Speicherkontos**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Erstellen eines Kontos für die Azure Cosmos DB-Tabellen-API**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Installieren des Azure Cosmos DB Table SDK für Python

Nach dem Erstellen eines Speicherkontos installieren Sie im nächsten Schritt das [Microsoft Azure Cosmos DB Table SDK für Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Ausführliche Informationen zum Installieren des SDK finden Sie in der Datei [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) im Repository für das Cosmos DB Table SDK für Python auf GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importieren der TableService- und der Entity-Klasse

Nutzen Sie zur Verwendung von Entitäten im Azure-Tabellenspeicherdienst in Python die Klassen [TableService][py_TableService] und [Entity][py_Entity]. Fügen Sie folgenden Code am Anfang der Python-Datei ein, um beide zu importieren:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Herstellen der Verbindung mit dem Azure-Tabellenspeicherdienst

Um eine Verbindung mit dem Azure Storage Table-Dienst herzustellen, erstellen Sie ein [TableService][py_TableService]-Objekt und übergeben den Speicherkontonamen und den Kontoschlüssel. Ersetzen Sie `myaccount` und `mykey` durch Ihren Kontonamen und Schlüssel.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Herstellen der Verbindung mit Azure Cosmos DB

Wenn Sie eine Verbindung mit Azure Cosmos DB herstellen möchten, kopieren Sie die primäre Verbindungszeichenfolge aus dem Azure-Portal und erstellen mithilfe der kopierten Verbindungszeichenfolge ein [TableService][py_TableService]-Objekt:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;')
```

## <a name="create-a-table"></a>Erstellen einer Tabelle

Rufen Sie zum Erstellen der Tabelle [create_table][py_create_table] auf.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

Zum Hinzufügen einer Entität erstellen Sie zunächst ein Objekt, das die Entität darstellt, und übergeben das Objekt dann an die Methode [TableService.insert_entity][py_TableService]. Das Entitätsobjekt kann ein Wörterbuch oder ein Objekt des Typs [Entity][py_Entity] sein und definiert die Eigenschaftennamen und Eigenschaftswerte der Entität. Jede Entität muss neben allen anderen Eigenschaften, die Sie für die Entität definieren, die erforderlichen Eigenschaften [PartitionKey und RowKey](#partitionkey-and-rowkey) enthalten.

In diesem Beispiel wird ein Wörterbuchobjekt erstellt, das eine Entität darstellt, und dann an die [insert_entity][py_insert_entity]-Methode übergeben, sodass es der Tabelle hinzugefügt wird:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

In diesem Beispiel wird ein [Entity][py_Entity]-Objekt erstellt und dann an die [insert_entity][py_insert_entity]-Methode übergeben, sodass es der Tabelle hinzugefügt wird:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey und RowKey

Sie müssen für jede Entität eine **PartitionKey** - und eine **RowKey** -Eigenschaft angeben. Diese bilden zusammen den Primärschlüssel einer Entität und sind daher die eindeutigen Bezeichner der Entitäten. Diese Werte können sehr viel schneller abgefragt werden als alle anderen Entitätseigenschaften, da nur diese Werte indiziert sind.

Der Tabellendienst verwendet **PartitionKey** für die intelligente Verteilung von Tabellenentitäten auf Speicherknoten. Entitäten mit dem gleichen **PartitionKey** werden auf dem gleichen Knoten gespeichert. **RowKey** ist eine eindeutige ID der Entität in der Partition, zu der sie gehört.

## <a name="update-an-entity"></a>Aktualisieren einer Entität

Rufen Sie zum Aktualisieren aller Eigenschaftswerte einer Entität die [update_entity][py_update_entity]-Methode auf. In diesem Beispiel wird gezeigt, wie eine vorhandene Entität durch eine aktualisierte Version ersetzt wird:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Beim Aktualisierungsvorgang tritt ein Fehler auf, wenn die zu aktualisierende Entität nicht bereits vorhanden ist. Verwenden Sie [insert_or_replace_entity][py_insert_or_replace_entity], wenn Sie eine Entität unabhängig davon speichern möchten, ob sie bereits vorhanden ist. Der erste Aufruf im folgenden Beispiel ersetzt die existierende Entität. Der zweite Aufruf fügt eine neue Entität ein, da keine Entität mit dem angegebenen PartitionKey und RowKey in der Tabelle vorhanden ist.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Mit der [update_entity][py_update_entity]-Methode werden alle Eigenschaften und Werte einer vorhandenen Entität ersetzt. Zudem können Sie mit dieser Methode auch Eigenschaften aus einer vorhandenen Entität entfernen. Mit der [merge_entity][py_merge_entity]-Methode können Sie eine vorhandene Entität mit neuen oder geänderten Eigenschaftswerten aktualisieren, ohne die Entität vollständig ersetzen zu müssen.

## <a name="modify-multiple-entities"></a>Ändern mehrerer Entitäten

Um die unteilbare Verarbeitung einer Anforderung durch den Tabellendienst sicherzustellen, können Sie mehrere Vorgänge zusammen in einem Batch senden. Verwenden Sie zunächst die [TableBatch][py_TableBatch]-Klasse, um einem einzelnen Batch mehrere Vorgänge hinzuzufügen. Rufen Sie anschließend [TableService][py_TableService].[commit_batch][py_commit_batch] auf, um die Vorgänge in einem unteilbaren Vorgang zu übermitteln. Alle als Batch zu ändernden Entitäten müssen sich auf derselben Partition befinden.

In diesem Beispiel werden zwei Entitäten als Batch hinzugefügt:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Batches können auch mit der Kontext-Manager-Syntax verwendet werden:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Abfragen einer Entität

Zum Abfragen einer Entität in einer Tabelle übergeben Sie die zugehörigen PartitionKey- und RowKey-Elemente an die [TableService][py_TableService].[get_entity][py_get_entity]-Methode.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Abfragen einer Gruppe von Entitäten

Eine Gruppe von Entitäten können Sie abfragen, indem Sie mit dem **filter** -Parameter eine Filterzeichenfolge angeben. In diesem Beispiel werden durch Anwenden eines Filters für PartitionKey alle Aufgaben in Seattle abgefragt:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Abfragen einer Teilmenge von Entitätseigenschaften

Es ist auch möglich, die Eigenschaften einzuschränken, die für jede Entität in einer Abfrage zurückgegeben werden. Bei dieser Methode, der sogenannten *Projektion* , wird die Bandbreite reduziert und die Abfrageleistung kann gesteigert werden, vor allem bei großen Entitäten oder Resultsets. Verwenden Sie den **select** -Parameter, und übergeben Sie die Namen der Eigenschaften, die an den Client zurückgegeben werden sollen.

Mit der Abfrage im folgenden Code werden nur die Beschreibungen von Entitäten in der Tabelle zurückgegeben.

> [!NOTE]
> Der folgende Codeausschnitt funktioniert nur mit Azure Storage. Er wird nicht vom Speicheremulator unterstützt.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Löschen einer Entität

Eine Entität können Sie löschen, indem Sie die zugehörigen **PartitionKey** - und **RowKey** -Elemente an die [delete_entity][py_delete_entity]-Methode übergeben.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Löschen einer Tabelle

Wenn Sie eine Tabelle und die Entitäten darin nicht mehr benötigen, können Sie die [delete_table][py_delete_table]-Methode aufrufen, um die Tabelle endgültig aus Azure Storage zu löschen.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Nächste Schritte

* [Häufig gestellte Fragen: Entwickeln mit der Tabellen-API](./faq.md)
* [API-Referenz zum Azure Cosmos DB SDK für Python](/python/api/overview/azure/cosmosdb?preserve-view=true&view=azure-python)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage-Explorer:](../vs-azure-tools-storage-manage-with-storage-explorer.md) kostenlose, plattformübergreifende Anwendung zur visuellen Verwendung von Azure Storage-Daten unter Windows, macOS und Linux.
* [Arbeiten mit Python in Visual Studio (Windows)](/visualstudio/python/overview-of-python-tools-for-visual-studio)



[py_commit_batch]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_create_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_delete_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_get_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_insert_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_insert_or_replace_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_Entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?preserve-view=true&view=azure-python
[py_merge_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_update_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_delete_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_TableService]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python&preserve-view=true