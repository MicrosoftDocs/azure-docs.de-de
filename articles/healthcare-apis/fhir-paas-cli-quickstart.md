---
title: 'Schnellstart: Bereitstellen von Azure API for FHIR mithilfe der Azure CLI'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure API for FHIR in Azure mithilfe der Azure CLI bereitstellen.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3340cd0a39b0f5311487fec5b05d37e3376d433a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659250"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Schnellstart: Bereitstellen von Azure API for FHIR mithilfe der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure API for FHIR in Azure mithilfe der Azure CLI bereitstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>Hinzufügen der HealthcareAPIs-Erweiterung

```azurecli-interactive
az extension add --name healthcareapis
```

Rufen Sie eine Liste der Befehle für HealthcareAPIs ab:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe

Wählen Sie einen Namen für die Ressourcengruppe aus, in der Azure API for FHIR enthalten sein soll, und erstellen Sie sie:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Bereitstellen von Azure API for FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Abrufen der FHIR-API-Funktionsbestätigung

Rufen Sie wie folgt eine Funktionsbestätigung aus der FHIR-API ab:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die Ressourcengruppe wie folgt:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Azure API for FHIR in Ihrem Abonnement bereitgestellt. Wenn Sie zusätzliche Einstellungen in Azure API for FHIR festlegen möchten, fahren Sie mit der Schrittanleitung für zusätzliche Einstellungen fort. Wenn Sie Azure API for FHIR verwenden möchten, informieren Sie sich ausführlicher über das Registrieren von Anwendungen.

>[!div class="nextstepaction"]
>[Zusätzliche Einstellungen in Azure API for FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Registrieren der Azure Active Directory-Apps für Azure API for FHIR](fhir-app-registration.md)
