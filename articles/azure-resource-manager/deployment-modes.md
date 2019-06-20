---
title: Azure Resource Manager-Bereitstellungsmodi | Microsoft-Dokumentation
description: Beschreibt das Festlegen, ob für Azure Resource Manager eine vollständige oder inkrementelle Bereitstellung verwendet wird.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: b4d3a9c5e985949d813b638806f60bc86f5a163a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65789267"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager-Bereitstellungsmodi

Bei der Bereitstellung Ihrer Ressourcen geben Sie an, dass es sich bei der Bereitstellung entweder um ein inkrementelles Update oder um ein vollständiges Update handelt.  Der Hauptunterschied zwischen diesen beiden Modi besteht darin, wie Resource Manager vorhandene Ressourcen in der Ressourcengruppe behandelt, die nicht in der Vorlage enthalten sind. Der Standardmodus ist inkrementell.

In beiden Modi versucht Resource Manager, alle in der Vorlage angegebenen Ressourcen zu erstellen. Wenn die Ressource bereits in der Ressourcengruppe vorhanden ist und ihre Einstellungen unverändert sind, wird für diese Ressource kein Vorgang ausgeführt. Wenn Sie die Eigenschaftswerte für eine Ressource ändern, wird die Ressource mit diesen neuen Werten aktualisiert. Falls Sie versuchen, den Speicherort oder Typ einer vorhandenen Ressource zu aktualisieren, tritt bei der Bereitstellung ein Fehler auf. Stellen Sie stattdessen eine neue Ressource mit dem gewünschten Speicherort oder Typ bereit.

## <a name="complete-mode"></a>Vollständiger Modus

Im vollständigen-Modus **löscht** Resource Manager Ressourcen, die in der Ressourcengruppe vorhanden, aber nicht in der Vorlage angegeben sind. Ressourcen, die in der Vorlage angegeben, aber nicht bereitgestellt werden, weil [condition](resource-group-authoring-templates.md#condition) als „false“ ausgewertet wird, werden nicht gelöscht.

Bei der Verarbeitung von Löschungen im vollständigen Modus gibt es zwischen Ressourcentypen einige Unterschiede. Übergeordnete Ressourcen werden automatisch gelöscht, wenn sie nicht in einer Vorlage enthalten sind, die im vollständigen-Modus bereitgestellt wird. Einige untergeordnete Ressourcen werden nicht automatisch gelöscht, wenn sie nicht in der Vorlage enthalten sind. Diese untergeordneten Ressourcen werden jedoch gelöscht, wenn die übergeordnete Ressource gelöscht wird. 

Beispiel: Wenn Ihre Ressourcengruppe eine DNS-Zone (Ressourcentyp „Microsoft.Network/dnsZones“) und einen CNAME-Eintrag (Ressourcentyp „Microsoft.Network/dnsZones/CNAME“) enthält, ist die DNS-Zone die übergeordnete Ressource des CNAME-Eintrags. Wenn Sie im vollständigen Modus bereitstellen und die DNS-Zone nicht in Ihre Vorlage aufnehmen, werden sowohl die DNS-Zone als auch der CNAME-Eintrag gelöscht. Wenn Sie die DNS-Zone in Ihre Vorlage aufnehmen, den CNAME-Eintrag jedoch nicht, wird der CNAME-Eintrag nicht gelöscht. 

Eine Liste der Verarbeitung von Löschungen durch Ressourcentypen finden Sie unter [Löschen von Azure-Ressourcen für Bereitstellungen im vollständigen Modus](complete-mode-deletion.md).

Wenn die Ressourcengruppe [gesperrt](resource-group-lock-resources.md) ist, werden die Ressourcen im vollständigen Modus nicht gelöscht.

> [!NOTE]
> Nur Vorlagen auf Stammebene unterstützen den vollständigen Bereitstellungsmodus. Für [verknüpfte oder geschachtelte Vorlagen](resource-group-linked-templates.md) müssen Sie den inkrementellen Modus verwenden. 
>
> [Bereitstellungen auf der Abonnementstufe](deploy-to-subscription.md) unterstützen den vollständigen Modus nicht.
>
> Das Portal unterstützt den vollständigen Modus derzeit nicht.
>

## <a name="incremental-mode"></a>Inkrementeller Modus

Im inkrementellen Modus lässt Resource Manager Ressourcen **unverändert**, die in der Ressourcengruppe vorhanden, aber nicht in der Vorlage angegeben sind.

Bei der erneuten Bereitstellung einer vorhandenen Ressource im inkrementellen Modus ist das Ergebnis jedoch anders. Geben Sie alle Eigenschaften für die Ressource an und nicht nur diejenigen, die Sie aktualisieren. Ein weit verbreitetes Missverständnis ist, dass Eigenschaften, die nicht angegeben sind, unverändert bleiben. Falls Sie bestimmte Eigenschaften nicht angeben, interpretiert Resource Manager das Update als Überschreibung dieser Werte.

## <a name="example-result"></a>Beispielergebnis

Im folgenden Szenario wird der Unterschied zwischen dem inkrementellen und dem vollständigen Modus veranschaulicht:

Die **Ressourcengruppe** enthält Folgendes:

* Ressource A
* Ressource B
* Ressource C

Die **Vorlage** enthält Folgendes:

* Ressource A
* Ressource B
* Ressource D

Bei der Bereitstellung im **inkrementellen** Modus enthält die Ressourcengruppe:

* Ressource A
* Ressource B
* Ressource C
* Ressource D

Bei der Bereitstellung im **vollständigen** Modus wird Ressource C gelöscht. Die Ressourcengruppe enthält:

* Ressource A
* Ressource B
* Ressource D

## <a name="set-deployment-mode"></a>Festlegen des Bereitstellungsmodus

Verwenden Sie zum Festlegen des Bereitstellungsmodus mit PowerShell den `Mode`-Parameter.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Verwenden Sie zum Festlegen des Bereitstellungsmodus mit der Azure CLI den `mode`-Parameter.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Im folgenden Beispiel ist eine verknüpfte Vorlage mit festgelegtem inkrementellen Bereitstellungsmodus dargestellt:

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Erstellen von Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
* Informationen zum Bereitstellen von Vorlagen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).
* Informationen zum Anzeigen der Vorgänge für einen Ressourcenanbieter finden Sie unter [Azure-REST-API](/rest/api/).
