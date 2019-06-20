---
title: Fehler bei der Azure-Ressourcenanbieterregistrierung | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Fehler bei der Azure-Ressourcenanbieterregistrierung beheben.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2f3db5e6260b065c83f0e337306d38dca6e5ff51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60389921"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Beheben von Fehlern bei der Ressourcenanbieterregistrierung

In diesem Artikel werden die Fehler beschrieben, die bei der Verwendung eines Ressourcenanbieters auftreten können, den Sie in Ihrem Abonnement zuvor noch nicht genutzt haben.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

Beim Bereitstellen von Ressourcen werden möglicherweise der folgende Fehlercode und die folgende Fehlermeldung angezeigt:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Oder Sie erhalten eine ähnliche Meldung, die Folgendes besagt:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Die Fehlermeldung enthält in der Regel Vorschläge für die unterstützten Standorte und API-Versionen. Sie können Ihre Vorlage in einen der vorgeschlagenen Werte ändern. Die meisten – jedoch nicht alle – Anbieter werden automatisch über das Azure-Portal oder die verwendete Befehlszeilenschnittstelle registriert. Wenn Sie einen bestimmten Ressourcenanbieter bisher noch nicht verwendet haben, müssen Sie diesen Anbieter unter Umständen registrieren.

Wenn Sie die automatisches Herunterfahren für virtuelle Computer deaktivieren, erhalten Sie möglicherweise eine Fehlermeldung ähnlich der folgenden:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Ursache

Sie erhalten diese Fehler aus einem der folgenden Gründe:

* Der erforderliche Ressourcenanbieter wurde für Ihr Abonnement nicht registriert.
* Die API-Version wird für den Ressourcentyp nicht unterstützt.
* Der Standort wird für den Ressourcentyp nicht unterstützt.
* Für das automatische Herunterfahren von virtuellen Computern muss der Microsoft.DevTestLab-Ressourcenanbieter registriert sein.

## <a name="solution-1---powershell"></a>Lösung 1: PowerShell

Für PowerShell verwenden Sie zum Anzeigen des Registrierungsstatus **Get-AzResourceProvider**.

```powershell
Get-AzResourceProvider -ListAvailable
```

Verwenden Sie **Register-AzResourceProvider**, um einen Anbieter zu registrieren, und geben Sie den Namen des zu registrierenden Ressourcenanbieters an.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Verwenden Sie zum Abrufen der unterstützten Standorte für einen bestimmten Ressourcentyp Folgendes:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Verwenden Sie zum Abrufen der unterstützten API-Versionen für einen bestimmten Ressourcentyp Folgendes:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Lösung 2: Azure CLI

Mit dem Befehl `az provider list` können Sie ermitteln, ob der Anbieter registriert ist.

```azurecli-interactive
az provider list
```

Verwenden Sie den Befehl `az provider register` , um einen Ressourcenanbieter zu registrieren, und geben Sie den zu registrierenden *Namespace* an.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Verwenden Sie zum Anzeigen der unterstützten Standorte und API-Versionen für einen Ressourcentyp Folgendes:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Lösung 3: Azure-Portal

Sie können den Registrierungsstatus sehen und einen Ressourcenanbieter-Namespace über das Portal registrieren.

1. Wählen Sie im Portal die Option **Alle Dienste** aus.

   ![Auswählen von „Alle Dienste“](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Wählen Sie **Abonnements**.

   ![Auswählen von Abonnements](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Wählen Sie in der Liste der Abonnements das Abonnement aus, das Sie für die Registrierung des Anbieters verwenden möchten.

   ![Auswählen eines Abonnements zum Registrieren eines Ressourcenanbieters](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Wählen Sie für Ihr Abonnement die Option **Ressourcenanbieter** aus.

   ![Auswählen von Ressourcenanbietern](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Sehen Sie sich die Liste der Ressourcenanbieter an, und wählen Sie bei Bedarf den Link **Registrieren** aus, um den Ressourcenanbieter des Typs zu registrieren, den Sie bereitstellen möchten.

   ![Ressourcenanbieter auflisten](./media/resource-manager-register-provider-errors/list-resource-providers.png)
