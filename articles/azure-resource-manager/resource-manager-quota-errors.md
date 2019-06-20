---
title: Azure Kontingentfehler | Microsoft-Dokumentation
description: Beschreibt das Beheben von Kontingentfehlern bei Ressourcen.
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
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 7938f2c47e4af8d8804191fbb9e55b379f9554ef
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390193"
---
# <a name="resolve-errors-for-resource-quotas"></a>Das Beheben von Fehlern bei Ressourcenkontingenten

Dieser Artikel beschreibt Kontingentfehler, die möglicherweise auftreten, wenn Sie Ressourcen bereitstellen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

Wenn Sie eine Vorlage bereitstellen, die Ressourcen erstellt, die Ihre Azure-Kontingente übersteigen, erhalten Sie einen Bereitstellungsfehler, ähnlich dem folgenden Fehler:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Oder Sie sehen möglicherweise:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Ursache

Kontingente werden pro Ressourcengruppe, Abonnements, Konten und anderen Bereichen angewendet. Ihr Abonnement kann beispielsweise so konfiguriert werden, um die Anzahl der Kerne für eine Region zu begrenzen. Wenn Sie versuchen, einen virtuellen Computer mit mehr als der zulässigen Anzahl von Kernen bereitzustellen, erhalten Sie eine Fehlermeldung, die darauf hinweist, dass das Kontingent überschritten wurde.
Die vollständigen Kontingentinformationen finden Sie unter [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie für Azure CLI den `az vm list-usage`-Befehl, um Kontingente für virtuelle Computer zu finden.

```azurecli
az vm list-usage --location "South Central US"
```

Ausgabe des Befehls:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Verwenden Sie für PowerShell den Befehl **Get-AzVMUsage**, um Kontingente für virtuelle Computer zu finden.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Ausgabe des Befehls:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Lösung

Wenn Sie eine Erhöhung des Kontingents anfordern möchten, rufen Sie das Portal auf, und reichen Sie ein Supportproblem ein. Fordern Sie im Supportproblem eine Erhöhung des Kontingents für die Region an, in der die Bereitstellung erfolgen soll.

> [!NOTE]
> Denken Sie daran, dass für Ressourcengruppen das Kontingent für jede einzelne Region und nicht für das gesamte Abonnement gilt. Wenn Sie 30 Kerne in der Region "USA, Westen" bereitstellen möchten, müssen Sie 30 Ressourcen-Manager-Kerne für "USA, Westen" anfordern. Wenn Sie 30 Kerne in einer der Regionen bereitstellen möchten, auf die Sie Zugriff haben, sollten Sie 30 Resource Manager-Kerne in allen Regionen anfordern.
>
>

1. Wählen Sie **Abonnements**.

   ![Abonnements](./media/resource-manager-quota-errors/subscriptions.png)

2. Wählen Sie das Abonnement aus, für das ein höheres Kontingent benötigt wird.

   ![Wählen Sie das Abonnement aus.](./media/resource-manager-quota-errors/select-subscription.png)

3. Wählen Sie **Nutzung + Kontingente** aus.

   ![Auswählen von „Nutzung + Kontingente“](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. Klicken Sie in der Ecke oben rechts auf **Erhöhung anfordern**.

   ![Anfordern einer Erhöhung](./media/resource-manager-quota-errors/request-increase.png)

5. Füllen Sie die Formulare für den Kontingenttyp aus, den Sie erhöhen müssen.

   ![Ausfüllen des Formulars](./media/resource-manager-quota-errors/forms.png)