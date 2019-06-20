---
title: 'Python: Dateisystemvorgänge in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das Python SDK für das Data Lake Storage Gen1-Dateisystem verwenden.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 57efc718a51398b577a0078ba829d2f6209cab54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878818"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

In diesem Artikel wird beschrieben, wie Sie das Python SDK verwenden, um Dateisystemvorgänge in Azure Data Lake Storage Gen1 durchzuführen. Eine Anleitung zum Durchführen von Kontoverwaltungsvorgängen in Data Lake Storage Gen1 mit Python finden Sie unter [Kontoverwaltungsvorgänge für Data Lake Storage Gen1 mit Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Python**. Sie können Python [hier](https://www.python.org/downloads/) herunterladen. In diesem Artikel wird Python 3.6.2 verwendet.

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Ein Azure Data Lake Storage Gen1-Konto**. Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure Data Lake Storage Gen1 über das Azure-Portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Installieren der Module

Zum Verwenden von Data Lake Storage Gen1 mit Python müssen Sie drei Module installieren.

* Das Modul `azure-mgmt-resource`, in dem Azure-Module für Active Directory usw. enthalten sind.
* Das Modul `azure-mgmt-datalake-store`, das die Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum Modul azure-mgmt-datalake-store](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Das Modul `azure-datalake-store`, das die Dateisystemvorgänge für Azure Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum Modul azure-datalake-store file-system](https://azure-datalake-store.readthedocs.io/en/latest/).

Verwenden Sie die folgenden Befehle, um die Module zu installieren:

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Erstellen Sie in der IDE Ihrer Wahl eine neue Python-Anwendung, z.B. **mysample.py**.

2. Fügen Sie die folgenden Zeilen hinzu, um die erforderlichen Module zu importieren:

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Speichern Sie Ihre Änderungen an „mysample.py“.

## <a name="authentication"></a>Authentication

In diesem Abschnitt werden die unterschiedlichen Möglichkeiten zur Authentifizierung mit Azure AD beschrieben. Die verfügbaren Optionen sind:

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über Python](data-lake-store-end-user-authenticate-python.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Erstellen des Dateisystemclients

Der folgende Codeausschnitt erstellt zunächst den Data Lake Storage Gen1-Kontoclient. Er verwendet das Clientobjekt zum Erstellen eines Data Lake Storage Gen1-Kontos. Schließlich erstellt der Codeausschnitt ein Dateisystem-Clientobjekt.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Hochladen einer Datei


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Herunterladen einer Datei

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Nächste Schritte
* [Kontoverwaltungsvorgänge für Data Lake Storage Gen1 mit Python](data-lake-store-get-started-python.md)

## <a name="see-also"></a>Weitere Informationen

* [Python-Referenz zu Azure Data Lake Storage Gen1 (Dateisystem)](https://azure-datalake-store.readthedocs.io/en/latest)
* [Mit Azure Data Lake Storage Gen1 kompatible Open-Source-Big Data-Anwendungen](data-lake-store-compatible-oss-other-applications.md)
