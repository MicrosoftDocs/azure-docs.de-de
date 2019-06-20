---
title: Überlegungen zum Entwurf von Azure-VM-Skalierungsgruppen | Microsoft-Dokumentation
description: Erfahren Sie mehr über Überlegungen zum Entwurf Ihrer Azure-VM-Skalierungsgruppen.
keywords: virtueller Linux-Computer, Skalierungsgruppen für virtuelle Computer
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: 67bbad7e73f33d73d4c3f1d4f7e5599d2ef914e3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618471"
---
# <a name="design-considerations-for-scale-sets"></a>Überlegungen zum Entwurf von Skalierungsgruppen
In diesem Artikel werden Überlegungen zum Entwurf von VM-Skalierungsgruppen erörtert. Informationen darüber, was Skalierungsgruppen für virtuelle Computer sind, finden Sie unter [Übersicht über VM-Skalierungsgruppen](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Wann sollten Sie Skalierungsgruppen statt virtuellen Computern verwenden?
Skalierungsgruppen sind im Allgemeinen hilfreich für das Bereitstellen einer hoch verfügbaren Infrastruktur, in der eine Gruppe von Computern ähnlich konfiguriert sind. Jedoch sind einige Funktionen nur in Skalierungsgruppen verfügbar, andere nur bei virtuellen Computern. Um eine fundierte Entscheidung über die jeweils geeignete Technologie treffen zu können, müssen Sie sich zunächst mit einigen häufig verwendeten Funktionen beschäftigen, die nur in Skalierungsgruppen und nicht bei VMs verfügbar sind:

### <a name="scale-set-specific-features"></a>Skalierungsgruppenspezifische Funktionen

- Nachdem Sie die Konfiguration der Skalierungsgruppe festgelegt haben, können Sie die *Capacity*-Eigenschaft aktualisieren, um mehrere VMs parallel bereitzustellen. Das ist besser, als ein Skript zu schreiben, um die parallele Bereitstellung von vielen einzelnen VMs zu orchestrieren.
- Sie können [Azure Autoscale dazu verwenden, Skalierungsgruppen automatisch zu skalieren](./virtual-machine-scale-sets-autoscale-overview.md), nicht jedoch einzelne virtuelle Computer.
- Sie können [ein Reimaging für virtuelle Computer in einer Skalierungsgruppe ](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) durchführen, [nicht jedoch für einzelne virtuelle Computer](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Sie können virtuelle Computer in einer Skalierungsgruppe [überbereitstellen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning), um eine höhere Zuverlässigkeit und kürzere Bereitstellungszeiten zu erreichen. Eine Überprovisionierung einzelner VMs ist nur möglich, wenn Sie benutzerdefinierten Code zum Ausführen dieser Aktion schreiben.
- Sie können eine [Upgraderichtlinie](./virtual-machine-scale-sets-upgrade-scale-set.md) festlegen, um Upgrades auf den virtuellen Computern in Ihrer Skalierungsgruppe auf einfache Weise durchzuführen. Bei einzelnen virtuellen Computern müssen Sie die Updates selbst orchestrieren.

### <a name="vm-specific-features"></a>Spezifische Funktionen von virtuellen Computern

Einige Features sind derzeit nur auf VMs verfügbar:

- Sie können ein Image von einem einzelnen VM erfassen, nicht jedoch von einem VM in einer Skalierungsgruppe.
- Sie können einen einzelnen virtuellen Computer von einem nativen Datenträger zu einem verwalteten Datenträger migrieren, aber Sie können keine VM-Instanzen in einer Skalierungsgruppe migrieren.
- Sie können den virtuellen Netzwerkschnittstellenkarten (Network Interface Cards, NICs) einzelner VMs öffentliche IPv6-IP-Adressen zuweisen, bei VM-Instanzen in einer Skalierungsgruppe ist dies jedoch nicht möglich. Sie können Load Balancern sowohl bei VMs als auch bei VMs in einer Skalierungsgruppe öffentliche IPv6-Adressen zuweisen.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Skalierungsgruppen mit Azure Managed Disks
Skalierungsgruppen können mit [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) anstatt mit den traditionellen Azure-Speicherkonten erstellt werden. Das Feature „Managed Disks“ (Verwaltete Datenträger) bietet die folgenden Vorteile:
- Sie müssen für die VMs in der Skalierungsgruppe nicht vorab eine Gruppe von Azure-Speicherkonten erstellen.
- Sie können in Ihrer Skalierungsgruppe [angefügte Datenträger](virtual-machine-scale-sets-attached-disks.md) für die virtuellen Computer definieren.
- Skalierungsgruppen können so konfiguriert werden, dass [bis zu 1.000 virtuelle Computer in einer Gruppe unterstützt werden](virtual-machine-scale-sets-placement-groups.md). 

Wenn eine Vorlage vorhanden ist, können Sie diese auch [aktualisieren, um Managed Disks zu verwenden](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Vom Benutzer verwalteter Speicher
Eine Skalierungsgruppe, die nicht mit Azure Managed Disks definiert ist, benötigt vom Benutzer erstellte Speicherkonten zum Speichern der Betriebssystem-Datenträger der VMs in der Gruppe. Pro Speicherkonto werden maximal 20 VMs empfohlen, um die maximale E/A-Leistung zu erzielen und von der _Überbereitstellung_ zu profitieren. Außerdem sollten Sie als Anfangszeichen der Speicherkontonamen verschiedene Buchstaben verwenden. Auf diese Weise lässt sich die Last leichter auf verschiedene interne Systeme verteilen. 


## <a name="overprovisioning"></a>Überbereitstellung
Skalierungsgruppen übernehmen derzeit die „Überbereitstellung“ virtueller Computer als Standardeinstellung. Wenn die Überbereitstellung aktiviert ist, richtet die Skalierungsgruppe tatsächlich mehr VMs ein, als Sie angefordert haben, und löscht dann die zusätzlichen VMs, nachdem die angeforderte Anzahl von VMs erfolgreich bereitgestellt wurde. Die Überbereitstellung verbessert die Erfolgsquoten bei der Bereitstellung und verkürzt die Bereitstellungsdauer. Die zusätzlichen VMs werden Ihnen nicht berechnet und nicht auf Ihre Kontingentgrenzen angerechnet.

Während die Überbereitstellung die Erfolgsquoten bei der Bereitstellung verbessert, kann sie ein verwirrendes Verhalten bei Anwendungen verursachen, die nicht darauf ausgelegt sind, mit dem Vorhandensein und anschließenden Nichtvorhandensein von VMs umzugehen. Um die Überbereitstellung zu deaktivieren, stellen Sie sicher, dass Ihre Vorlage folgende Zeichenfolge enthält: `"overprovision": "false"`. Weitere Einzelheiten finden Sie in der [REST-API-Dokumentation für Skalierungsgruppen](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Wenn die Skalierungsgruppe vom Benutzer verwalteten Speicher nutzt und Sie die Überbereitstellung deaktivieren, sind mehr als 20 VMs pro Speicherkonto möglich, doch aus Gründen der E/A-Leistung werden nur maximal 40 empfohlen. 

## <a name="limits"></a>Einschränkungen
Eine für ein Marketplace-Image (auch Plattformimage genannt) erstellte Skalierungsgruppe, die für die Verwendung von Azure Managed Disks konfiguriert ist, unterstützt eine Kapazität von bis zu 1.000 VMs. Wenn Sie Ihre Skalierungsgruppe für eine Unterstützung von mehr als 100 VMs konfiguriert haben, funktionieren nicht alle Szenarien identisch (z.B. der Lastenausgleich). Weitere Informationen finden Sie unter [Verwenden umfangreicher VM-Skalierungsgruppen](virtual-machine-scale-sets-placement-groups.md). 

Eine Skalierungsgruppe mit vom Benutzer verwalteten Speicherkonten ist derzeit auf 100 VMs begrenzt (und für diese Skalierung werden 5 Speicherkonten empfohlen).

Bei Konfiguration mit Azure Managed Disks kann eine Skalierungsgruppe, die auf einem benutzerdefinierten (von Ihnen erstellten) Image basiert, bis zu 600 VMs aufweisen. Wenn die Skalierungsgruppe mit vom Benutzer verwalteten Speicherkonten konfiguriert ist, müssen alle Betriebssystem-Datenträger-VHDs innerhalb eines Speicherkontos erstellt werden. Folglich ist 20 die maximal empfohlene Anzahl von VMs in einer Skalierungsgruppe, die auf einem benutzerdefinierten Image und vom Benutzer verwalteten Speicher basiert. Wenn Sie die Überbereitstellung deaktivieren, können Sie bis zu 40 gehen.

Wenn die Anzahl benötigter virtueller Computer über diese Grenzwerte hinausgeht, müssen Sie mehrere Skalierungsgruppen bereitstellen, wie in dieser [Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)gezeigt.

