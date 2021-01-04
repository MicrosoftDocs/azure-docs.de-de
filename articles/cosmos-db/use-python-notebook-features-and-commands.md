---
title: Verwenden integrierter Notebookbefehle und -features in Python-Notebooks für Azure Cosmos DB (Vorschauversion)
description: Hier erfahren Sie, wie Sie integrierte Befehle und Features verwenden, um allgemeine Vorgänge unter Verwendung der in Azure Cosmos DB integrierten Python-Notebooks auszuführen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: 381aa88326440aba91e02393cfe2bdb1e2c38097
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340412"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Verwenden integrierter Notebookbefehle und -features in Python-Notebooks für Azure Cosmos DB (Vorschauversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Integrierte Jupyter Notebooks in Azure Cosmos DB ermöglichen es Ihnen, Ihre Daten über das Azure-Portal zu analysieren und zu visualisieren. In diesem Artikel wird beschrieben, wie Sie integrierte Notebookbefehle und -features verwenden, um allgemeine Vorgänge in Python-Notebooks durchzuführen.

## <a name="install-a-new-package"></a>Installieren eines neuen Pakets
Nachdem Sie die Notebookunterstützung für Ihre Azure Cosmos-Konten aktiviert haben, können Sie ein neues Notebook öffnen und ein Paket installieren.

Fügen Sie in einer neuen Codezelle den folgenden Code ein, und führen Sie ihn aus. Ersetzen Sie dabei ``PackageToBeInstalled`` durch das gewünschte Python-Paket.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Dieses Paket kann von einem beliebigen Notebook im Arbeitsbereich des Azure Cosmos-Kontos verwendet werden. 

> [!TIP]
> Wenn für Ihr Notebook ein benutzerdefiniertes Paket erforderlich ist, wird empfohlen, im Notebook eine Zelle hinzuzufügen, um das Paket zu installieren, da Pakete entfernt werden, wenn Sie den [Arbeitsbereich zurücksetzen](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Ausführen einer SQL-Abfrage

Sie können den Magic-Befehl ``%%sql`` verwenden, um eine [SQL-Abfrage](sql-query-getting-started.md) für einen beliebigen Container in Ihrem Konto auszuführen. Verwenden Sie die folgende Syntax:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Ersetzen Sie ``{database_id}`` und ``{container_id}`` durch den Namen der Datenbank und des Containers in Ihrem Cosmos-Konto. Wenn die Argumente ``--database`` und ``--container`` nicht angegeben werden, wird die Abfrage für die [Standarddatenbank und den Standardcontainer](#set-default-database-for-queries) ausgeführt.
- Sie können eine beliebige, in Azure Cosmos DB gültige SQL-Abfrage ausführen. Der Abfragetext muss in einer neuen Zeile enthalten sein.

Beispiel: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Führen Sie ```%%sql?``` in einer Zelle aus, um die Hilfedokumentation zum Magic-SQL-Befehl im Notebook anzuzeigen.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Ausführen einer SQL-Abfrage und Ausgeben des Ergebnisses in einen Pandas DataFrame

Sie können die Ergebnisse einer ``%%sql``-Abfrage in einen [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame) ausgeben. Verwenden Sie die folgende Syntax: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Ersetzen Sie ``{database_id}`` und ``{container_id}`` durch den Namen der Datenbank und des Containers in Ihrem Cosmos-Konto. Wenn die Argumente ``--database`` und ``--container`` nicht angegeben werden, wird die Abfrage für die [Standarddatenbank und den Standardcontainer](#set-default-database-for-queries) ausgeführt.
- Ersetzen Sie ``{outputDataFrameVar}`` durch den Namen der DataFrame-Variablen, die die Ergebnisse enthalten soll.
- Sie können eine beliebige, in Azure Cosmos DB gültige SQL-Abfrage ausführen. Der Abfragetext muss in einer neuen Zeile enthalten sein. 

Beispiel:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Festlegen der Standarddatenbank für Abfragen
Sie können die Standarddatenbank festlegen, die von ```%%sql```-Befehlen für das Notebook verwendet wird. Ersetzen Sie ```{database_id}``` durch den Namen Ihrer Datenbank.

```python
%database {database_id}
```
Führen Sie ```%database?``` in einer Zelle aus, um die Dokumentation im Notebook anzuzeigen.

## <a name="set-default-container-for-queries"></a>Festlegen des Standardcontainers für Abfragen
Sie können den Standardcontainer festlegen, der von ```%%sql```-Befehlen für das Notebook verwendet wird. Ersetzen Sie ```{container_id}``` durch den Namen Ihres Containers.

```python
%container {container_id}
```
Führen Sie ```%container?``` in einer Zelle aus, um die Dokumentation im Notebook anzuzeigen.

## <a name="upload-json-items-to-a-container"></a>Hochladen von JSON-Elementen in einen Container
Sie können den Magic-Befehl ``%%upload`` verwenden, um Daten aus einer JSON-Datei in einen angegebenen Azure Cosmos-Container hochzuladen. Laden Sie die Elemente mithilfe des folgenden Befehls hoch:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Ersetzen Sie ``{database_id}`` und ``{container_id}`` durch den Namen der Datenbank und des Containers in Ihrem Azure Cosmos-Konto. Wenn die Argumente ``--database`` und ``--container`` nicht angegeben werden, wird die Abfrage für die [Standarddatenbank und den Standardcontainer](#set-default-database-for-queries) ausgeführt.
- Ersetzen Sie ``{url_location_of_file}`` durch den Speicherort Ihrer JSON-Datei. Die Datei muss ein Array aus gültigen JSON-Objekten sein, und sie sollte im Internet öffentlich zugänglich sein.

Beispiel:

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Mit der Ausgabestatistik können Sie die effektive Rate an RU/s berechnen, die zum Hochladen der Elemente benötigt werden. Wenn beispielsweise 28.000 RUs in 38 Sekunden verarbeitet werden, liegt die effektive Rate an RU/s bei 25.000 RUs / 38 Sekunden = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Ausführen eines anderen Notebooks im aktuellen Notebook 
Sie können den Magic-Befehl ``%%run`` verwenden, um vom aktuellen Notebook in Ihrem Arbeitsbereich aus ein anderes Notebook auszuführen. Verwenden Sie die folgende Syntax:

```python
%%run ./path/to/{notebookName}.ipynb
```
Ersetzen Sie ``{notebookName}`` durch den Namen des Notebooks, das Sie ausführen möchten. Das Notebook muss sich in Ihrem aktuellen Arbeitsbereich „My Notebooks“ (Meine Notebooks) befinden. 

## <a name="use-built-in-nteract-data-explorer"></a>Verwenden des integrierten Daten-Explorers in nteract
Sie können den integrierten [Daten-Explorer in nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) verwenden, um einen Datenrahmen zu visualisieren. Legen Sie für die Option ``pd.options.display.html.table_schema`` den Wert ``True`` und für ``pd.options.display.max_rows`` einen gewünschten Wert fest, um diese Funktion zu aktivieren. Sie können für ``pd.options.display.max_rows`` den Wert ``None`` festlegen, um alle Ergebnisse anzuzeigen.

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="Daten-Explorer in nteract":::

## <a name="use-the-built-in-python-sdk"></a>Verwenden des integrierten Python SDKs
In der Notebookumgebung für das Azure Cosmos-Konto ist Version 4 des [Azure Cosmos DB Python SDKs für die SQL-API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) installiert und enthalten.

Verwenden Sie die integrierte ``cosmos_client``-Instanz, um SDK-Vorgänge auszuführen. 

Beispiel:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Siehe [Python SDK-Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Das integrierte Python SDK wird nur für SQL (Core)-API-Konten unterstützt. Bei anderen APIs müssen Sie [den relevanten Python-Treiber installieren](#install-a-new-package), der der API entspricht. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Erstellen einer benutzerdefinierten Instanz von ``cosmos_client``
Sie können eine benutzerdefinierte Instanz von ``cosmos_client`` erstellen, um mehr Flexibilität zu erhalten und:

- die [Verbindungsrichtlinie](/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?preserve-view=true&view=azure-python-preview) anzupassen.
- Vorgänge für ein anderes Azure Cosmos-Konto als das auszuführen, das Sie gerade verwenden.

Auf die Verbindungszeichenfolge und den Primärschlüssel des aktuellen Kontos können Sie über die [Umgebungsvariablen](#access-the-account-endpoint-and-primary-key-env-variables) zugreifen. 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Zugreifen auf die Umgebungsvariablen für den Kontoendpunkt und Primärschlüssel
Sie können auf den integrierten Endpunkt und den Schlüssel des Kontos zugreifen, in dem sich das Notebook befindet.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> Die Variablen ``COSMOS.ENDPOINT`` und ``COSMOS.KEY`` gelten nur für die SQL-API. Bei anderen APIs finden Sie den Endpunkt und den Schlüssel auf dem Blatt **Verbindungszeichenfolgen** oder **Schlüssel** in Ihrem Azure Cosmos-Konto.  

## <a name="reset-notebooks-workspace"></a>Zurücksetzen des Notebookarbeitsbereichs
Wählen Sie in der Befehlsleiste den Befehl zum **Zurücksetzen des Arbeitsbereichs** aus, um den Notebookarbeitsbereich auf die Standardeinstellungen zurückzusetzen. Dadurch werden alle installierten benutzerdefinierten Pakete entfernt und der Jupyter-Server neu gestartet. Ihre Notebooks, Dateien und Azure Cosmos-Ressourcen sind nicht betroffen.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Zurücksetzen des Notebookarbeitsbereichs":::

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den Vorteilen von [Jupyter Notebooks in Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
- Weitere Informationen zum [Azure Cosmos DB Python SDK für die SQL-API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)