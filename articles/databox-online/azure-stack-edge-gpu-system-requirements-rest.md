---
title: Blobspeicheranforderungen für Microsoft Azure Stack Edge | Microsoft-Dokumentation
description: Lernen Sie die unterstützten Versionen von APIs, SDKs und Clientbibliotheken für Azure Stack Edge-Blobspeicher kennen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: f99a3110880626b3a809e6bab5edc02398094547
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426229"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Blobspeicheranforderungen für Azure Stack Edge

In diesem Artikel werden die Versionen der Azure-APIs, -Clientbibliotheken und -Tools aufgeführt, die mit dem Azure Stack Edge-Blobspeicher unterstützt werden. Der Azure Stack Edge-Blobspeicher bietet Blobverwaltungsfunktionen mit einer in Azure konsistenten Semantik. Dieser Artikel bietet auch eine Übersicht über die bekannten Unterschiede zwischen Azure Stack Edge-Blobspeicher und Azure Storage-Diensten.

Sie sollten die Informationen sorgfältig lesen, bevor Sie eine Verbindung mit dem Azure Stack Edge-Blobspeicher herstellen. Auch später sollten Sie bei Bedarf als Referenz darauf zurückgreifen.

## <a name="storage-differences"></a>Speicherunterschiede

|     Funktion                                             |     Azure Storage                                     |     Azure Stack Edge-Blobspeicher |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure-Dateispeicher                                   |    Cloudbasierte SMB-Dateifreigaben unterstützt              |    Nicht unterstützt      |
|    Speicherkontotyp                                 |    Allgemeine Speicherkonten und Azure Blob Storage-Konten    |    Nur Konten vom Typ „Universell V1“|
|    Blobname                                            |    1\.024 Zeichen (2.048 Bytes)                     |    880 Zeichen (1.760 Bytes)|
|    Maximale Blockblobgröße                              |    4,75 TB (100 MB X 50.000 Blöcke)                   |    4,75 TB (100 MB × 50.000 Blöcke) für Azure Stack Edge|
|    Maximale Seitenblobgröße                               |    8 TB                                               |    1 TB                   |
|    Seitenblob – Seitengröße                                  |    512 Bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Unterstützte API-Versionen

Die folgenden Versionen der Azure Storage-Dienst-APIs werden mit Azure Stack Edge-Blobspeicher unterstützt.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge ab Version 2.1.1377.2170

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Unterstützte Azure-Clientbibliotheken

Für Azure Stack Edge-Blobspeicher sind bestimmte Clientbibliotheken vorhanden, und es gelten bestimmte Anforderungen an Endpunktsuffixe. Die Blob Storage-Endpunkte für Azure Stack Edge entsprechen nicht vollständig der neuesten Version der REST-API für Azure Blob Storage. Weitere Informationen finden Sie unter [Unterstützte API-Versionen für Azure Stack Edge](#supported-api-versions). Bei den Speicherclientbibliotheken müssen Sie auf die Version achten, die mit der REST-API kompatibel ist.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge ab Version 2.1.1377.2170

Die folgenden Azure-Clientbibliotheksversionen werden für Azure Stack Edge-Blobspeicher unterstützt.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>Installation des PHP-Clients über Composer: Aktuell

So installieren Sie den PHP-Client über Composer

1. Erstellen Sie im Stammverzeichnis des Projekts die Datei „composer.json“ mit folgendem Code (im Beispiel wird der Dienst Azure Storage Blob verwendet).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Laden Sie `composer.phar` in das Stammverzeichnis des Projekts herunter.

3. Führen Sie „php composer.phar install“ aus.


## <a name="endpoint-declaration"></a>Endpunktdeklaration

Im Azure Stack Edge-Blobspeicher-SDK identifiziert das Endpunktsuffix – `<device serial number>.microsoftdatabox.com` – die Azure Stack Edge-Domäne. Weitere Informationen zum Blobdienstendpunkt finden Sie unter [Übertragen von Daten an Speicherkonten mit einem Azure Stack Edge Pro-Gerät mit GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md).


## <a name="examples"></a>Beispiele

### <a name="net"></a>.NET

Das Endpunktsuffix für Azure Stack Edge-Blobspeicher wird in der `app.config`-Datei angegeben:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Das Endpunktsuffix für Azure Stack Edge-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Das Endpunktsuffix für Azure Stack Edge-Blobspeicher wird in der Deklarationsinstanz angegeben:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Das Endpunktsuffix für Azure Stack Edge Blob Storage wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Das Endpunktsuffix für Azure Stack Edge Blob Storage wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Das Endpunktsuffix für Azure Stack Edge-Blobspeicher wird in der Deklarationsinstanz angegeben:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Das Endpunktsuffix für Azure Stack Edge Blob Storage wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten der Bereitstellung von Azure Stack Edge Pro-Geräten mit GPU](azure-stack-edge-gpu-deploy-prep.md)
