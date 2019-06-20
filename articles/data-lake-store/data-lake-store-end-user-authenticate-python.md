---
title: 'Authentifizierung von Endbenutzern: Python mit Azure Data Lake Storage Gen1 mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory mit Python umsetzen.
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
ms.openlocfilehash: 8b72604d7e736230911d0a0987b88d372be4ddf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878049"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mit Python
> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Verwenden des .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-end-user-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

In diesem Artikel erfahren Sie, wie Sie mithilfe des Python SDK die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 durchführen. Endbenutzerauthentifizierung kann weiter in zwei Kategorien unterteilt werden:

* Endbenutzerauthentifizierung ohne mehrstufige Authentifizierung
* Endbenutzerauthentifizierung mit mehrstufiger Authentifizierung

Beide Optionen werden in diesem Artikel erläutert. Informationen zur Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe von Python finden Sie unter [Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mit Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Python**. Sie können Python [hier](https://www.python.org/downloads/) herunterladen. In diesem Artikel wird Python 3.6.2 verwendet.

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer nativen Azure Active Directory-Anwendung.** Sie müssen die Schritte unter [Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) ausgeführt haben.

## <a name="install-the-modules"></a>Installieren der Module

Zum Verwenden von Data Lake Storage Gen1 mit Python müssen Sie drei Module installieren.

* Das Modul `azure-mgmt-resource`, in dem Azure-Module für Active Directory usw. enthalten sind.
* Das Modul `azure-mgmt-datalake-store`, das die Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum Verwaltungsmodul für Azure Data Lake Storage Gen1](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Das Modul `azure-datalake-store`, das die Dateisystemvorgänge für Azure Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum azure-datalake-store-Dateisystemmodul](https://azure-datalake-store.readthedocs.io/en/latest/).

Verwenden Sie die folgenden Befehle, um die Module zu installieren:

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Erstellen Sie in der IDE Ihrer Wahl eine neue Python-Anwendung, z.B. **mysample.py**.

2. Fügen Sie den folgenden Codeausschnitt hinzu, um die erforderlichen Module zu importieren:

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Speichern Sie Ihre Änderungen an „mysample.py“.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Endbenutzerauthentifizierung mit mehrstufiger Authentifizierung

### <a name="for-account-management"></a>Für die Kontenverwaltung

Verwenden Sie den folgenden Codeausschnitt zur Authentifizierung bei Azure AD für Kontoverwaltungsvorgänge, die ein Data Lake Storage Gen1-Konto betreffen. Sie können den folgenden Codeausschnitt verwenden, um Ihre Anwendung per Multi-Factor Authentication zu authentifizieren. Geben Sie die Werte unten für eine vorhandene **native** Azure AD-Anwendung an.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Für Dateisystemvorgänge

Führen Sie hiermit die Authentifizierung bei Azure AD für Dateisystemvorgänge durch, die ein Data Lake Storage Gen1-Konto betreffen. Sie können den folgenden Codeausschnitt verwenden, um Ihre Anwendung per Multi-Factor Authentication zu authentifizieren. Geben Sie die Werte unten für eine vorhandene **native** Azure AD-Anwendung an.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Endbenutzerauthentifizierung ohne mehrstufige Authentifizierung

Dies ist veraltet. Weitere Informationen finden Sie unter [Authentifizieren mit Tokenanmeldeinformationen](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung von Endbenutzern verwenden, um sich mithilfe von Python bei Azure Data Lake Storage Gen1 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie Python mit Azure Data Lake Storage Gen1 verwenden.

* [Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit Python](data-lake-store-get-started-python.md)
* [Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit Python](data-lake-store-data-operations-python.md)

