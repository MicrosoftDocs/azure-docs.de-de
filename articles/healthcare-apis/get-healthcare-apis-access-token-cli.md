---
title: Abrufen von Zugriffstoken für die Azure API for FHIR mit Azure CLI
description: In diesem Artikel wird erläutert, wie Sie ein Zugriffstoken für die Azure API for FHIR über die Azure CLI erhalten.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: bfcf2b41cd1c117d1b41e0b3b4be4dd3277b2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730193"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Abrufen von Zugriffstoken für die Azure API for FHIR mit Azure CLI

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI ein Zugriffstoken für die Azure API for FHIR erhalten. Wenn Sie [die Azure API for FHIR bereitstellen](fhir-paas-portal-quickstart.md), konfigurieren Sie eine Reihe von Benutzern oder Dienstprinzipalen mit Zugriff auf den Dienst. Wenn sich Ihre Benutzerobjekt-ID in der Liste der zulässigen Objekt-IDs befindet, können Sie auf den Dienst über ein Token zugreifen, das Sie über die Azure CLI erhalten haben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Anmelden mit der Azure CLI

Bevor Sie einen Token erhalten können, müssen Sie sich mit dem Benutzer anmelden, für den Sie einen Token erhalten möchten:

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>Erhalten eines Tokens

Die Azure API for FHIR verwendet ein `resource` oder `Audience` mit URI `https://azurehealthcareapis.com`. Sie können ein Token erhalten und mit dem folgenden Befehl in einer Variablen (namens `$token`) speichern:

```azurecli-interactive
token=$(az account get-access-token --resource=https://azurehealthcareapis.com | jq -r .accessToken)
```

## <a name="use-with-azure-api-for-fhir"></a>Verwenden mit Azure API for FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mithilfe der Azure CLI ein Zugriffstoken für die Azure API for FHIR erhalten. Informationen zum Zugriff auf die FHIR-API mit Postman finden Sie im Tutorial zu Postman.

>[!div class="nextstepaction"]
>[Zugriff auf die FHIR-API mit Postman](access-fhir-postman-tutorial.md)