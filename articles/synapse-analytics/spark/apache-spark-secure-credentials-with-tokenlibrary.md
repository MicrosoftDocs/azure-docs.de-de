---
title: Schützen von Anmeldeinformationen für den Zugriff mit verknüpften Diensten in Apache Spark für Azure Synapse Analytics
description: In den in diesem Artikel enthaltenen Konzepten wird beschrieben, wie Apache Spark für Azure Synapse Analytics mit verknüpften Diensten und der Tokenbibliothek sicher in andere Dienste integriert werden kann.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 8c2e2327667ccb9284a22e65418a80f3066d22df
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510793"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Schützen von Anmeldeinformationen mit verknüpften Diensten per Tokenbibliothek

Es kommt häufig vor, dass auf Daten aus externen Quellen zugegriffen werden muss. Wenn die externe Datenquelle keinen anonymen Zugriff zulässt, müssen Sie die Verbindung wahrscheinlich mithilfe von Anmeldeinformationen, einem geheimen Schlüssel oder einer Verbindungszeichenfolge absichern.  

Von Synapse wird standardmäßig ein AAD-Passthrough (Azure Active Directory) genutzt, um die Authentifizierung zwischen Ressourcen durchzuführen.  Verwenden Sie die Tokenbibliothek (TokenLibrary) direkt, falls Sie mit anderen Anmeldeinformationen eine Verbindung mit einer Ressource herstellen müssen.  Die TokenLibrary vereinfacht den Prozess zum Abrufen von SAS-Token, AAD-Token, Verbindungszeichenfolgen und Geheimnissen, die in einem verknüpften Dienst gespeichert sind oder sich in einer Azure Key Vault-Instanz befinden.

Beim Abrufen von Geheimnissen aus Azure Key Vault empfehlen wir Ihnen die Erstellung eines verknüpften Diensts für Ihre Azure Key Vault-Instanz.  Stellen Sie sicher, dass die verwaltete Dienstidentität (Managed Service Identity, MSI) des Synapse-Arbeitsbereichs über Berechtigungen für den Geheimnisabruf für Ihre Azure Key Vault-Instanz verfügt.  Synapse führt die Authentifizierung bei Azure Key Vault mit der verwalteten Dienstidentität des Synapse-Arbeitsbereichs durch. Wenn Sie ohne verknüpften Dienst eine direkte Verbindung mit Azure Key Vault herstellen, werden für die Authentifizierung Ihre Benutzeranmeldeinformationen von Azure Active Directory verwendet.

Weitere Informationen finden Sie unter [Verknüpfte Dienste](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Verwendung

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
Mit dieser Funktion wird die Hilfedokumentation für die TokenLibrary angezeigt.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary für Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>Primärer ADLS Gen2-Speicher

Für den Zugriff auf Dateien über primären Azure Data Lake Storage wird standardmäßig der Azure Active Directory-Passthrough für die Authentifizierung verwendet, sodass die explizite Nutzung der TokenLibrary nicht erforderlich ist.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>ADLS Gen2-Speicher mit verknüpften Diensten

Synapse verfügt über eine integrierte Funktion für verknüpfte Dienste, die für die Verbindungsherstellung mit Azure Data Lake Storage Gen2 genutzt wird.  Verknüpfte Dienste können für die Authentifizierung mit einem **Kontoschlüssel**, **Dienstprinzipal**, einer **verwalteten Identität** oder mit **Anmeldeinformationen** konfiguriert werden.

Wenn für den verknüpften Dienst die Authentifizierungsmethode **Kontoschlüssel** festgelegt ist, wird die Authentifizierung mit dem angegebenen Speicherkontoschlüssel durchgeführt. Anschließend wird ein SAS-Schlüssel angefordert und über den Anbieter **LinkedServiceBasedSASProvider** automatisch auf die Speicheranforderung angewendet.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Wenn die Authentifizierungsmethode für den verknüpften Dienst auf **Verwaltete Identität** oder **Dienstprinzipal** festgelegt ist, wird die verwaltete Identität oder das Dienstprinzipaltoken mit dem Anbieter **LinkedServiceBasedTokenProvider** verwendet.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2-Speicher (ohne verknüpfte Dienste)

Stellen Sie direkt eine Verbindung mit ADLS Gen2-Speicher her, indem Sie einen SAS-Schlüssel verwenden. Nutzen Sie den Anbieter **ConfBasedSASProvider**, und geben Sie den SAS-Schlüssel für die Konfigurationseinstellung **spark.storage.synapse.sas** an.

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>ADLS Gen2-Speicher mit Azure Key Vault

Stellen Sie mit einem SAS-Token, das in einem Azure Key Vault-Geheimnis gespeichert ist, eine Verbindung mit ADLS Gen2-Speicher her.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary für andere verknüpfte Dienste

Zum Herstellen einer Verbindung mit anderen verknüpften Diensten können Sie die TokenLibrary direkt aufrufen.

#### <a name="getconnectionstring"></a>getConnectionString()

 Um die Verbindungszeichenfolge abzurufen, verwenden Sie die Funktion **getConnectionString** und übergeben den **Namen des verknüpften Diensts**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

„getConnectionStringAsMap“ ist eine verfügbare Hilfsfunktion in Scala und Python, mit der bestimmte Werte aus einem Schlüssel-Wert-Paar (_key=value_) in der Verbindungszeichenfolge analysiert werden können, z. B.:

_`DefaultEndpointsProtocol=https;AccountName=\<ACCOUNT NAME>;AccountKey=\<ACCOUNT KEY>`_

Verwenden Sie die Funktion **getConnectionStringAsMap**, und übergeben Sie den Schlüssel, um den Wert zurückzugeben.  Im obigen Beispiel für eine Verbindungszeichenfolge 

_**TokenLibrary.getConnectionStringAsMap("DefaultEndpointsProtocol")**_

wird Folgendes zurückgegeben:

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Zum Abrufen eines Geheimnisses, das in Azure Key Vault gespeichert ist, empfehlen wir Ihnen die Erstellung eines verknüpften Diensts für Azure Key Vault innerhalb des Synapse-Arbeitsbereichs. Für die verwaltete Dienstidentität des Synapse-Arbeitsbereichs müssen **GET**-Berechtigungen gewährt werden, um Geheimnisse aus Azure Key Vault abrufen zu können.  Für den verknüpften Dienst wird die verwaltete Dienstidentität genutzt, um zum Abrufen des Geheimnisses eine Verbindung mit dem Azure Key Vault-Dienst herzustellen.  Ansonsten werden beim Herstellen einer direkten Verbindung mit Azure Key Vault die AAD-Anmeldeinformationen (Azure Active Directory) des Benutzers verwendet.  In diesem Fall müssen dem Benutzer die Berechtigungen zum Abrufen von Geheimnissen in Azure Key Vault gewährt werden.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Verwenden Sie zum Abrufen eines Geheimnisses aus Azure Key Vault die Funktion **TokenLibrary.getSecret()** .

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

- [Schreiben in einen dedizierten SQL-Pool](./synapse-spark-sql-pool-import-export.md)
