---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178457"
---
| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| Ressourcen pro [Ressourcengruppe](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), pro Ressourcentyp |800 |Variiert je nach Ressourcentyp |
| Bereitstellungen pro Ressourcenverlauf im Bereitstellungsverlauf |800<sup>1</sup> |800 |
| Ressourcen pro Bereitstellung |800 |800 |
| Managementsperren pro eindeutigem Bereich |20 |20 |
| Anzahl Tags pro Ressource oder Ressourcengruppe |15 |15 |
| Tagschlüssellänge |512 |512 |
| Tagwertlänge |256 |256 |

<sup>1</sup>Wenn der Grenzwert von 800 Bereitstellungen pro Ressourcengruppe erreicht ist, löschen Sie nicht mehr benötigte Bereitstellungen aus dem Verlauf. Das Löschen eines Eintrags aus dem Bereitstellungsverlauf wirkt sich nicht auf die bereitgestellten Ressourcen aus. Bei Verwendung der Azure CLI können Sie mit [az group deployment delete](/cli/azure/group/deployment) Einträge aus dem Verlauf löschen, in PowerShell verwenden Sie [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment).  [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f) ist ein Beispiel für ein PowerShell-Skript, das das Löschen von Bereitstellungen in einem CI/CD-Szenario (Continuous Integration/Continuous Delivery) automatisiert.

#### <a name="template-limits"></a>Vorlagengrenzwerte

| Wert | Standardlimit | Maximales Limit |
| --- | --- | --- |
| Parameter |256 |256 |
| Variables |256 |256 |
| Ressourcen (einschließlich copy-Anzahl) |800 |800 |
| Ausgaben |64 |64 |
| Vorlagenausdruck |24.576 Zeichen |24.576 Zeichen |
| Ressourcen in exportierten Vorlagen |200 |200 | 
| Vorlagengröße |1 MB |1 MB |
| Parameterdateigröße |64 KB |64 KB |

Sie können einige Vorlagengrenzwerte überschreiten, indem Sie eine geschachtelte Vorlage verwenden. Weitere Informationen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](../articles/azure-resource-manager/resource-group-linked-templates.md). Um die Anzahl von Parametern, Variablen oder Ausgaben zu reduzieren, können Sie mehrere Werte in einem Objekt kombinieren. Weitere Informationen finden Sie unter [Objekte als Parameter](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
