---
title: Kennzeichnen einer VM mithilfe von PowerShell
description: Erfahren Sie etwas über das Kennzeichnen eines virtuellen Computers mithilfe von PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594861"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Kennzeichnen eines virtuellen Computers in Azure mithilfe von PowerShell

In diesem Artikel wird das Markieren einer VM in Azure mit PowerShell beschrieben. Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die direkt auf einer Ressource oder einer Ressourcengruppe platziert werden können. Azure unterstützt derzeit bis zu 50 Tags pro Ressource und Ressourcengruppe. Tags können zum Zeitpunkt der Erstellung auf einer Ressource platziert werden oder zu einer vorhandenen Ressource hinzugefügt werden. Wenn Sie einen virtuellen Computer mit der Azure-Befehlszeilenschnittstelle kennzeichnen möchten, finden Sie weitere Informationen unter [Kennzeichnen eines virtuellen Computers in Azure über die Azure-Befehlszeilenschnittstelle](tag-cli.md).


Verwenden Sie das Cmdlet `Get-AzVM`, um die aktuelle Liste der Tags für Ihre VM anzuzeigen.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Wenn der virtuelle Computer bereits Tags enthält, werden anschließend alle Tags im Listenformat angezeigt.

Um Tags hinzuzufügen, verwenden Sie den `Set-AzResource`-Befehl. Beim Aktualisieren von Tags über PowerShell werden die Tags als Ganzes aktualisiert. Wenn Sie ein Tag einer Ressource hinzufügen, die bereits über Tags verfügt, müssen Sie alle Tags einschließen, die auf der Ressource platziert werden sollen. Es folgt ein Beispiel für das Hinzufügen zusätzlicher Tags zu einer Ressource über PowerShell-Cmdlets.

Weisen Sie alle aktuellen Tags für den virtuellen Computer der `$tags`-Variablen zu, indem Sie die `Get-AzResource`- und `Tags`-Eigenschaft verwenden.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Wenn Sie die aktuellen Tags anzeigen möchten, geben Sie die Variable ein.

```azurepowershell-interactive
$tags
```

Die Ausgabe kann wie folgt aussehen:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

Im folgenden Beispiel fügen wir ein Tag namens `Location` mit dem Wert `myLocation` hinzu. Verwenden Sie `+=`, um das neue Schlüssel-Wert-Paar der `$tags`-Liste anzufügen.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Verwenden Sie `Set-AzResource`, um alle Tags festzulegen, die in der *$tags*-Variablen auf dem virtuellen Computer definiert sind.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Verwenden Sie `Get-AzResource`, um alle Tags der Ressource anzuzeigen.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

Die Ausgabe sollte unter Einbeziehung des neuen Tags etwa folgendermaßen aussehen:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**Nächste Schritte**

- Weitere Informationen zum Markieren Ihrer Azure-Ressourcen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md) und [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).
- Informationen dazu, wie Tags Ihnen helfen können, die Verwendung Ihrer Azure-Ressourcen zu verwalten, finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) und [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
