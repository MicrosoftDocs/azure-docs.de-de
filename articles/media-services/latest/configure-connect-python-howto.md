---
title: Verbinden mit der Azure Media Services v3-API – Python
description: Erfahren Sie, wie Sie mit Python eine Verbindung mit der Media Services v3-API herstellen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733095"
---
# <a name="connect-to-media-services-v3-api---python"></a>Verbinden mit der Media Services v3-API – Python

Dieser Artikel zeigt Ihnen, wie Sie mit der Methode der Dienstprinzipalanmeldung eine Verbindung zum Azure Media Services v3 Python SDK herstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Laden Sie Python von [python.org](https://www.python.org/downloads/) herunter.
- Achten Sie darauf, die Umgebungsvariable `PATH` festzulegen.
- [Erstellen Sie ein Media Services-Konto.](create-account-cli-how-to.md) Merken Sie sich unbedingt den Namen der Ressourcengruppe und den Namen des Media Services-Kontos.
- Führen Sie die Schritte im Thema [Zugriff auf Azure Media Services API mit Azure CLI](access-api-cli-how-to.md) aus. Halten Sie die Abonnement-ID, die Anwendungs-ID (Client-ID), den Authentifizierungsschlüssel und die Mandanten-ID fest. Sie benötigen diese Angaben in einem späteren Schritt.

## <a name="install-the-modules"></a>Installieren der Module

Zum Arbeiten in Azure Media Services mit Python müssen Sie diese Module installieren.

* Das Modul `azure-mgmt-resource`, in dem Azure-Module für Active Directory enthalten sind.
* Das `azure-mgmt-media` Modul, das die Media Services-Entitäten enthält.

Öffnen Sie ein Befehlszeilentool, und führen Sie die folgenden Befehle aus, um die Module zu installieren.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Herstellen der Verbindung mit dem Python-Client

1. Erstellen Sie eine Datei mit der Erweiterung `.py`.
1. Öffnen Sie die Datei in einem Text-Editor Ihrer Wahl.
1. Fügen Sie der Datei folgenden Code hinzu. Der Code importiert die erforderlichen Module und erstellt das Active Directory-Anmeldeinformationsobjekt, das zum Herstellen der Verbindung mit Media Services benötigt wird.

      Legen Sie die Werte der Variablen auf die Werte fest, die Sie in [Zugriff auf APIs](access-api-cli-how-to.md) erhalten haben.

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Führen Sie die Datei aus.

## <a name="next-steps"></a>Nächste Schritte

- Verwenden Sie das [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Machen Sie sich mit der [Python-Referenzdokumentation](https://aka.ms/ams-v3-python-ref) vertraut.
