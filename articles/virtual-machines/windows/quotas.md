---
title: vCPU-Kontingente für Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über vCPU-Kontingente für Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: aa6e93ef7e403f1b230814b7afa20d2e8e68b71a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718646"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-Kontingente für virtuelle Computer

Die vCPU-Kontingente für virtuelle Computer und die VM-Skalierungsgruppen werden bei jedem Abonnement und in jeder Region in zwei Ebenen angeordnet. „Regionale vCPUs gesamt“ bilden die erste Ebene, die zweite Ebene besteht aus den Kernen der verschiedenen VM-Größenfamilien, z.B. vCPUs der D-Serie. Bei jeder Bereitstellung eines neuen virtuellen Computers dürfen die vCPUs für den virtuellen Computer das vCPU-Kontingent für die VM-Größenfamilie bzw. das regionale vCPU-Gesamtkontingent nicht überschreiten. Wird eines dieser Kontingente überschritten, wird die VM-Bereitstellung nicht zugelassen. Es gibt auch ein Kontingent für die Gesamtanzahl der virtuellen Computer in der Region. Die Details zu den einzelnen Kontingenten können Sie entweder im [Azure-Portal](https://portal.azure.com) auf der Seite **Abonnement** im Bereich **Nutzung + Kontingente** anzeigen oder die Werte mithilfe von PowerShell abfragen.

 [!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)] 
 
## <a name="check-usage"></a>Überprüfen der Nutzung

Mit dem Cmdlet [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) können Sie die Kontingentnutzung überprüfen.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Die Ausgabe sieht etwa wie folgt aus:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Reservierte VM-Instanzen
Reservierte VM-Instanzen, die einem Einzelabonnement ohne flexible VM-Größe zugeordnet sind, erweitern vCPU-Kontingente um einen neuen Aspekt. Diese Werte beschreiben die Anzahl der Instanzen der angegebenen Größe, die im Abonnement bereitstellbar sein muss. Sie fungieren im Kontingentsystem als Platzhalter, um sicherzustellen, dass ein Kontingent reserviert ist. Dadurch wird gewährleistet, dass reservierte VM-Instanzen im Abonnement bereitgestellt werden können. Wenn beispielsweise ein bestimmtes Abonnement über 10 reservierte VM-Instanzen der Größe „Standard_D1“ verfügt, ist „10“ der Nutzungsgrenzwert für reservierte Standard_D1-VM-Instanzen. Dadurch wird Azure veranlasst sicherzustellen, dass immer mindestens 10 vCPUs im regionalen vCPU-Gesamtkontingent sowie 10 vCPUs im vCPU-Kontingent für die Standard_D-Familie verfügbar sind, die für Standard_D1-Instanzen verwendet werden sollen.

Wenn eine Erhöhung des Kontingents erforderlich ist, z.B. um ein Einzelabonnement für eine reservierte Instanz (RI) zu erwerben, können Sie für Ihr Abonnement eine [Erhöhung des Kontingents anfordern](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Abrechnung und Kontingenten finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
