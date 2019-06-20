---
title: Blobspeicheranforderungen für Microsoft Azure Data Box | Microsoft-Dokumentation
description: Lernen Sie die unterstützten Versionen von APIs, SDKs und Clientbibliotheken für Azure Data Box-Blobspeicher kennen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61436493"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Blobspeicheranforderungen für Azure Data Box

In diesem Artikel werden die Versionen der Azure-APIs, -Clientbibliotheken und -Tools aufgeführt, die mit dem Data Box-Blobspeicher unterstützt werden. Der Data Box-Blobspeicher bietet Blobverwaltungsfunktionen mit einer in Azure konsistenten Semantik. Dieser Artikel bietet auch eine Übersicht über die bekannten Unterschiede zwischen Azure Data Box-Blobspeicher und Azure Storage-Diensten.

Sie sollten die Informationen sorgfältig lesen, bevor Sie eine Verbindung mit dem Data Box-Blobspeicher herstellen. Auch später sollten Sie bei Bedarf als Referenz darauf zurückgreifen.


## <a name="storage-differences"></a>Speicherunterschiede

|     Feature                                             |     Azure Storage                                     |     Data Box-Blobspeicher |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure-Dateispeicher                                   |    Cloudbasierte SMB-Dateifreigaben unterstützt              |    Nicht unterstützt      |
|    Dienstverschlüsselung für ruhende Daten                  |    256-Bit-AES-Verschlüsselung                             |    256-Bit-AES-Verschlüsselung |
|    Speicherkontotyp                                 |    Allgemeine Speicherkonten und Azure Blob Storage-Konten    |    Nur Konten vom Typ „Universell V1“|
|    Blobname                                            |    1\.024 Zeichen (2.048 Bytes)                     |    880 Zeichen (1.760 Bytes)|
|    Maximale Blockblobgröße                              |    4,75 TB (100 MB X 50.000 Blöcke)                   |    4,75 TB (100 MB × 50.000 Blöcke) für Azure Data Box ab Version 1.8.|
|    Maximale Seitenblobgröße                               |    8 TB                                               |    1 TB                   |
|    Seitenblob – Seitengröße                                  |    512 Bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Unterstützte API-Versionen

Die folgenden Versionen von Azure Storage-Dienst-APIs werden mit Data Box-Blobspeicher unterstützt:

Azure Data Box ab Version 1.8

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>Unterstützte Azure-Clientbibliotheken

Für Data Box-Blobspeicher sind bestimmte Clientbibliotheken vorhanden, und es gelten bestimmte Anforderungen an Endpunktsuffixe. Die Data Box-Blobspeicher-Endpunkte sind der neuesten Version der REST-API für Azure Blob Storage nicht vollständig gleichgestellt, siehe [Unterstützte Versionen für Azure Data Box ab Version 1.8](#supported-api-versions). Bei den Speicherclientbibliotheken müssen Sie auf die Version achten, die mit der REST-API kompatibel ist.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box ab Version 1.8

| Clientbibliothek     |Unterstützte Version von Data Box-Blobspeicher     | Link   |     Endpunktspezifikation      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    NuGet-Paket: https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub-Release: https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config-Datei                 |
|    Java                |    7.0.0                                           |    Maven-Paket: https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub-Release: https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Verbindungszeichenfolgen-Setup         |
|    Node.js             |    2.8.3                                           |    NPM-Link:   https://www.npmjs.com/package/azure-storage (Befehl: `npm install azure-storage@2.7.0` )   <br>GitHub-Release: https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Dienstinstanzdeklaration    |
|    C++                 |    5.2.0                                           |    NuGet-Paket: https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub-Release: https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Verbindungszeichenfolgen-Setup         |
|    PHP                 |    1.2.0                                           |    GitHub-Release:<br>Allgemein: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Installation über Composer (Weitere Informationen finden Sie unten.)                                                                                                             |    Verbindungszeichenfolgen-Setup         |
|    Python              |    1.1.0                                           |    GitHub-Release:<br>Allgemein: https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob: https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Dienstinstanzdeklaration    |
|    Ruby                |    1.0.1                                           |    RubyGems-Paket:<br>Allgemein: https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub-Release:<br>Allgemein: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Verbindungszeichenfolgen-Setup         |



### <a name="install-php-client-via-composer---current"></a>Installation des PHP-Clients per Composer – Aktuell

Installation per Composer: (Blob als Beispiel verwenden).
1. Erstellen Sie mit folgendem Code eine Datei mit dem Namen „composer.json“ im Stammverzeichnis des Projekts:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Laden Sie `composer.phar` in das Stammverzeichnis des Projekts herunter.

3. Führen Sie „php composer.phar install“ aus.

### <a name="endpoint-declaration"></a>Endpunktdeklaration

Ein Azure Data Box-Blobspeicher-Endpunkt besteht aus zwei Teilen: dem Namen einer Region und der Data Box-Domäne. Im Data Box Blob Storage SDK lautet der Standardendpunkt `\<serial no. of the device>.microsoftdatabox.com`.  Weitere Informationen zu Blobdienstendpunkten finden Sie unter [Herstellen einer Verbindung über Data Box-Blobspeicher](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Beispiele

### <a name="net"></a>.NET

Das Endpunktsuffix für Data Box-Blobspeicher wird in der `app.config`-Datei angegeben:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Das Endpunktsuffix für Data Box-Blobspeicher wird in der Deklarationsinstanz angegeben:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Das Endpunktsuffix für Data Box-Blobspeicher wird in der Deklarationsinstanz angegeben:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen Ihrer Azure Data Box](data-box-deploy-ordered.md)
