---
title: Aktualisieren von Ressourcen in verwalteten Azure-Anwendungen | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Ressourcen in der verwalteten Ressourcengruppe für eine verwaltete Azure-Anwendung verwenden.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043406"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Verwenden von Ressourcen in der verwalteten Ressourcengruppe für eine verwaltete Azure-Anwendung

In diesem Artikel wird beschrieben, wie Sie Ressourcen aktualisieren, die als Teil einer verwalteten Anwendung bereitgestellt werden. Wie der Herausgeber einer verwalteten Anwendung haben Sie Zugriff auf die Ressourcen in der verwalteten Ressourcengruppe. Zum Aktualisieren dieser Ressourcen müssen Sie die verwaltete Ressourcengruppe ermitteln, die einer verwalteten Anwendung zugeordnet ist, und auf die Ressource in dieser Ressourcengruppe zugreifen.

In diesem Artikel wird davon ausgegangen, dass Sie die verwaltete Anwendung im Beispielprojekt [Managed Web Application (IaaS) with Azure management services](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) (Verwaltete Webanwendung (IaaS) mit Azure-Verwaltungsdiensten) bereitgestellt haben. Diese verwaltete Anwendung enthält einen virtuellen Computer vom Typ **Standard_D1_v2**. Wenn Sie diese verwaltete Anwendung nicht bereitgestellt haben, können Sie sich dennoch anhand dieses Artikels mit den Schritten zum Aktualisieren einer verwalteten Ressourcengruppe vertraut machen.

Die folgende Abbildung zeigt die bereitgestellte verwaltete Anwendung:

![Bereitgestellte verwaltete Anwendung](./media/update-managed-resources/deployed.png)

In diesem Artikel verwenden Sie die Azure CLI für Folgendes:

* Identifizieren der verwalteten Anwendung
* Identifizieren der verwalteten Ressourcengruppe
* Identifizieren der VM-Ressource(n) in der verwalteten Ressourcengruppe
* Ändern der Größe des virtuellen Computers (Verkleinerung bei Nichtverwendung oder Vergrößerung zur Unterstützung einer größeren Last)
* Zuweisen einer Richtlinie zur verwalteten Ressourcengruppe, die die zulässigen Standorte angibt

## <a name="get-managed-application-and-managed-resource-group"></a>Abrufen der verwalteten Anwendung und der verwalteten Ressourcengruppe

Verwenden Sie zum Abrufen der verwalteten Anwendungen in einer Ressourcengruppe Folgendes:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Verwenden Sie zum Abrufen der ID der verwalteten Ressourcengruppe Folgendes:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Ändern der Größe von virtuellen Computern in einer verwalteten Ressourcengruppe

Geben Sie zum Anzeigen der virtuellen Computer in der verwalteten Ressourcengruppe den Namen der verwalteten Ressourcengruppe an.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Verwenden Sie zum Aktualisieren der Größe virtueller Computer Folgendes:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Vergewissern Sie sich nach Abschluss des Vorgangs, dass die Anwendung unter „Standard D2 v2“ ausgeführt wird.

![Verwaltete Anwendung mit Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Übernehmen einer Richtlinie für eine verwaltete Ressourcengruppe

Rufen Sie die verwaltete Ressourcengruppe und die Zuweisung einer Richtlinie in diesem Bereich ab. Bei der Richtlinie **e56962a6-4747-49cd-b67b-bf8b01975c4c** handelt es sich um eine integrierte Richtlinie zum Angeben zulässiger Standorte.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Verwenden Sie zum Anzeigen der zulässigen Speicherorte Folgendes:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Die Richtlinienzuweisung wird im Portal angezeigt.

![Anzeigen der Richtlinienzuweisung](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Anwendungen](overview.md).
* Beispielprojekte finden Sie unter [Sample projects for Azure managed applications](sample-projects.md) (Beispielprojekte für verwaltete Azure-Anwendungen).
