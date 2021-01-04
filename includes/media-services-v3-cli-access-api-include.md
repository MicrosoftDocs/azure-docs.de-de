---
title: include file
description: include file
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 9a9aaf723b7b89b5b9c5611cea05c22c4003b99a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755937"
---
### <a name="access-the-media-services-api"></a>Zugreifen auf die Media Services-API

Verwenden Sie die Authentifizierung per Azure AD-Dienstprinzipal, um eine Verbindung mit den Azure Media Services-APIs herzustellen. Der folgende Befehl erstellt eine Azure AD-Anwendung und fügt einen Dienstprinzipal an das Konto an. Konfigurieren Sie Ihre Anwendung mit den zurückgegebenen Werten.

Ersetzen Sie vor der Ausführung des Skripts `amsaccount` und `amsResourceGroup` durch die Namen, die Sie beim Erstellen dieser Ressourcen ausgewählt haben. `amsaccount` ist der Name des Azure Media Services-Kontos, an das der Dienstprinzipal angefügt wird.

Sollten Sie Zugriff auf mehrere Abonnements haben, legen Sie zunächst das aktive Abonnement auf das Abonnement fest, in dem das Media Services-Konto erstellt wurde.

```azurecli
az account set --subscription subscriptionId
```

Der folgende Befehl gibt eine `json`-Ausgabe zurück:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Dieser Befehl erzeugt eine Antwort wie die folgende:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Wenn die Antwort `xml` enthalten soll, verwenden Sie den folgenden Befehl:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
