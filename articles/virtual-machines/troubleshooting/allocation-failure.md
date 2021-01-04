---
title: Problembehandlung für Zuordnungsfehler bei VMs in Azure | Microsoft-Dokumentation
description: Problembehandlung für einen Zuordnungsfehler beim Erstellen, Neustarten oder Ändern der Größe eines virtuellen Computers in Azure
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: daberry
ms.openlocfilehash: 79bc043a991404a3ee9da954b9639bf1a41f2c51
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365872"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Problembehandlung für Zuordnungsfehler beim Erstellen, Neustarten oder Ändern der Größen von virtuellen Computern in Azure

Wenn Sie einen virtuellen Computer (Virtual Machine, VM) erstellen, beendete VMs (VMs, deren Zuordnung aufgehoben wurde) neu starten oder die Größe eines VMs ändern, weist Microsoft Azure Ihrem Abonnement Computeressourcen zu. Wir investieren kontinuierlich in zusätzliche Infrastruktur und Funktionen, um sicherzustellen, dass immer alle VM-Typen zur Verfügung stehen, um die Kundenanforderungen erfüllen zu können. Gelegentlich können jedoch aufgrund der bisher beispiellosen Nachfrage nach Azure-Dienste in bestimmten Regionen Ressourcenzuordnungsfehler auftreten. Dieses Problem kann auftreten, wenn Sie versuchen, VMs in einer Region zu erstellen oder zu starten, obwohl auf den VMs der folgende Fehlercode und die folgende Meldung angezeigt werden:

**Fehlercode**: „AllocationFailed“ oder „ZonalAllocationFailed“

**Fehlermeldung**: „Fehler bei der Zuordnung. Wir verfügen in dieser Region nicht über genügend Kapazität für die angeforderte VM-Größe. Weitere Informationen zur Verbesserung der Erfolgschancen bei der Zuordnung finden Sie unter „https:\//aka.ms/allocation-guidance“.

> [!NOTE]
> Die Problembehandlung für eine VM-Skalierungsgruppe (VMSS) ist mit der für eine Standard-VM identisch. Befolgen Sie die Anweisungen in diesem Artikel, um das Problem zu beheben.
> 
>**Fehlermeldung**: „Fehler bei der Zuordnung. Wenn Sie versuchen, einer VM-Skalierungsgruppe mit einer einzelnen Platzierungsgruppe eine neue VM hinzuzufügen oder eine vorhandene VM zu aktualisieren bzw. ihre Größe zu ändern, sollten Sie beachten, dass diese Zuordnung nur auf einen einzelnen Cluster beschränkt ist und es möglich ist, dass der Cluster nicht über genügend Kapazität verfügt. Weitere Informationen zur Verbesserung der Erfolgschancen bei der Zuordnung finden Sie unter http:\//aka.ms/allocation-guidance.

In diesem Artikel werden die Ursachen einiger häufig auftretender Zuordnungsfehler erläutert und mögliche Korrekturmaßnahmen vorgeschlagen.

Suchen Sie in den Azure-Foren bei [MSDN und Stack Overflow](https://azure.microsoft.com/support/forums/), falls Sie ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können Ihr Problem in diesen Foren veröffentlichen oder auf Twitter an @AzureSupport senden. Darüber hinaus können Sie eine Azure-Supportanfrage stellen, indem Sie auf der Website des [Azure-Supports](https://azure.microsoft.com/support/options/) die Option „Support erhalten“ auswählen.

Bis der bevorzugte VM-Typ in Ihrer bevorzugten Region verfügbar ist, empfehlen wir Kunden, bei denen Bereitstellungsprobleme auftreten, die Anweisungen in der folgenden Tabelle als vorübergehende Problemumgehung in Betracht zu ziehen. 

Identifizieren Sie das Szenario, das Ihrem Fall am besten entspricht, und wiederholen Sie dann die Zuordnungsanforderung anhand der entsprechenden vorgeschlagenen Problemumgehung, um die Erfolgschancen bei der Zuordnung zu erhöhen. Alternativ können Sie den Vorgang auch zu einem späteren Zeitpunkt wiederholen. Denn möglicherweise wurden genügend Ressourcen im Cluster, in der Region oder in der Zone freigegeben, um Ihre Anforderung erfüllen zu können. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Ändern der Größe eines VMs oder Hinzufügen von VMs zu einer vorhandenen Verfügbarkeitsgruppe

### <a name="cause"></a>Ursache

Der Anforderungsversuch, die Größe eines virtuellen Computers zu ändern oder einen virtuellen Computer zu einer vorhandenen Verfügbarkeitsgruppe hinzuzufügen, muss im ursprünglichen Cluster ausgeführt werden, der die vorhandene Verfügbarkeitsgruppe hostet. Die angeforderte VM-Größe wird vom Cluster unterstützt, aber möglicherweise verfügt der Cluster aktuell nicht über genügend Kapazität. 

### <a name="workaround"></a>Problemumgehung

Falls der virtuelle Computer Teil einer anderen Verfügbarkeitsgruppe sein kann, erstellen Sie einen virtuellen Computer in einer anderen Verfügbarkeitsgruppe (in derselben Region). Dieser neue virtuelle Computer kann dann demselben virtuellen Netzwerk hinzugefügt werden.

Beenden Sie alle virtuellen Computer einer Verfügbarkeitsgruppe (heben Sie die Zuordnung auf), und starten Sie die einzelnen virtuellen Computer dann neu.
Gehen Sie zum Beenden wie folgt vor: Klicken Sie auf „Ressourcengruppen“ > [Ihre Ressourcengruppe] > „Ressourcen“ > [Ihre Verfügbarkeitsgruppe] > „Virtual Machines“ > [Ihr virtueller Computer] > „Beenden“.
Wählen Sie nach dem Beenden aller virtuellen Computer den ersten virtuellen Computer aus, und klicken Sie dann auf „Starten“.
Durch diesen Schritt wird sichergestellt, dass ein neuer Zuordnungsversuch ausgeführt wird und ein neuer Cluster ausgewählt werden kann, der über genügend Kapazität verfügt.

## <a name="restart-partially-stopped-deallocated-vms"></a>Neustart teilweise beendeter (zuordnungsaufgehobener) virtueller Computer 

### <a name="cause"></a>Ursache

Die Teilaufhebung der Zuordnung bedeutet, dass Sie mindestens einen, aber nicht alle virtuellen Computer innerhalb einer Verfügbarkeitsgruppe beendet (zuordnungsaufgehoben) haben. Wenn Sie die Zuordnung eines virtuellen Computers aufheben, werden die zugeordneten Ressourcen freigegeben. Das Neustarten virtueller Computer in einer Verfügbarkeitsgruppe, in der die Zuordnung teilweise aufgehoben wurde, entspricht dem Hinzufügen von virtuellen Computern zu einer vorhandenen Verfügbarkeitsgruppe. Aus diesem Grund muss der Zuordnungsanforderungsversuch im ursprünglichen Cluster ausgeführt werden, der die vorhandene Verfügbarkeitsgruppe hostet, die möglicherweise nicht über genügend Kapazität verfügt.

### <a name="workaround"></a>Problemumgehung

Beenden Sie alle virtuellen Computer einer Verfügbarkeitsgruppe (heben Sie die Zuordnung auf), und starten Sie die einzelnen virtuellen Computer dann neu.
Gehen Sie zum Beenden wie folgt vor: Klicken Sie auf „Ressourcengruppen“ > [Ihre Ressourcengruppe] > „Ressourcen“ > [Ihre Verfügbarkeitsgruppe] > „Virtual Machines“ > [Ihr virtueller Computer] > „Beenden“.
Wählen Sie nach dem Beenden aller virtuellen Computer den ersten virtuellen Computer aus, und klicken Sie dann auf „Starten“.
Dadurch wird sichergestellt, dass ein neuer Zuordnungsversuch ausgeführt wird und ein neuer Cluster ausgewählt werden kann, der über genügend Kapazität verfügt.

## <a name="restart-fully-stopped-deallocated-vms"></a>Neustart vollständig beendeter (zuordnungsaufgehobener) virtueller Computer

### <a name="cause"></a>Ursache

Die vollständige Aufhebung der Zuordnung bedeutet, dass Sie alle virtuellen Computer für eine Verfügbarkeitsgruppe beendet (zuordnungsaufgehoben) haben. Die Zuordnungsanforderung zum Neustarten dieser virtuellen Computer gilt für alle Cluster, die die gewünschte Größe in der Region oder Zone unterstützen. Ändern Sie Ihre Zuordnungsanforderung gemäß den Vorschlägen in diesem Artikel, und wiederholen Sie die Anforderung, um die Wahrscheinlichkeit einer erfolgreichen Zuordnung zu erhöhen. 

### <a name="workaround"></a>Problemumgehung

Bei Verwendung älterer VM-Serien oder -Größen (wie z. B. Dv1, DSv1, Av1, D15v2 oder DS15v2) sollten Sie erwägen, auf neuere Versionen umzusteigen. Beachten Sie diese Empfehlungen für bestimmte VM-Größen.
Wenn Sie nicht die Möglichkeit haben, eine andere VM-Größe zu verwenden, versuchen Sie es mit der Bereitstellung in einer anderen Region innerhalb desselben geografischen Raums. Weitere Informationen zu den verfügbaren VM-Größen in jeder Region finden Sie unter https://aka.ms/azure-regions.

Wenn Sie Verfügbarkeitszonen verwenden, versuchen Sie es mit einer anderen Zone in der Region, die über verfügbare Kapazität für die angeforderte VM-Größe verfügt.

Wenn Ihre Zuordnungsanforderung groß ist (mehr als 500 Kerne umfasst), lesen Sie die Anweisungen in den folgenden Abschnitten, um die Anforderung in kleinere Bereitstellungen aufzuteilen.

Versuchen Sie, [den virtuellen Computer erneut bereitzustellen](./redeploy-to-new-node-windows.md). Durch die erneute Bereitstellung des virtuellen Computers wird er einem neuen Cluster in der Region zugeordnet.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Zuordnungsfehler bei älteren VM-Größen (Av1, Dv1, DSv1, D15v2, DS15v2 usw.)

Im Zuge der Erweiterung der Azure-Infrastruktur stellen wir Hardware einer neueren Generation bereit, die zur Unterstützung der neuesten VM-Typen entwickelt wurde. Einige VMs der älteren Serien können nicht in unserer Infrastruktur der neuesten Generation ausgeführt werden. Aus diesem Grund können bei Kunden gelegentlich Zuordnungsfehler bei diesen älteren SKUs auftreten. Um dieses Problem zu vermeiden, empfehlen wir Kunden, die VMs älterer Serien verwenden, den Umstieg auf die entsprechenden neueren VMs gemäß den folgenden Empfehlungen. Diese VMs sind für die neueste Hardware optimiert, und dadurch können Sie von einer besseren Preisgestaltung und Leistung profitieren. 

|Ältere VM-Serie/-Größe|Empfohlene neuere VM-Serie/-Größe|Weitere Informationen|
|----------------------|----------------------------|--------------------|
|Av1-Serie|[Av2-Serie](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1- oder DSv1-Serie (D1 bis D5)|[Dv3- oder DSv3-Serie](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1- oder DSv1-Serie (D11 bis D14)|[Ev3- oder ESv3-Serie](../ev3-esv3-series.md)|
|D15v2 oder DS15v2|Wenn Sie das Ressourcen-Manager-Bereitstellungsmodell verwenden, um die Vorteile der größeren VM-Größen zu nutzen, sollten Sie den Umstieg auf D16v3/DS16v3 oder D32v3/DS32v3 in Erwägung ziehen. Diese sind für die Ausführung auf Hardware der neuesten Generation konzipiert. Wenn Sie das Ressourcen-Manager-Bereitstellungsmodell verwenden, um sicherzustellen, dass Ihre VM-Instanz auf für einen einzelnen Kunden dedizierte Hardware isoliert ist, sollten Sie in Erwägung ziehen, auf die neuen isolierten VM-Größen E64i_v3 oder E64is_v3 umzusteigen, die für die Ausführung auf Hardware der neuesten Generation konzipiert sind. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Zuordnungsfehler bei großen Bereitstellungen (mehr als 500 Kerne)

Verringern Sie die Anzahl der Instanzen der angeforderten VM-Größe, und wiederholen Sie dann den Bereitstellungsvorgang. Darüber hinaus sollten Sie bei größeren Bereitstellungen die [Azure-VM-Skalierungsgruppen](../../virtual-machine-scale-sets/index.yml) auswerten. Die Anzahl der VM-Instanzen kann je nach Bedarf oder definiertem Zeitplan automatisch erhöht oder verringert werden, und Sie haben eine größere Chance auf eine erfolgreiche Zuordnung, weil die Bereitstellungen auf mehrere Cluster verteilt werden können. 

## <a name="background-information"></a>Hintergrundinformationen
### <a name="how-allocation-works"></a>Funktionsweise der Zuordnung
Für die Server in Azure-Rechenzentren wird eine Partitionierung in Cluster vorgenommen. Normalerweise wird versucht, eine Zuordnungsanforderung in mehreren Clustern durchzuführen. Es ist aber möglich, dass bestimmte Einschränkungen der Zuordnungsanforderung die Azure-Plattform zwingen, die Anforderung nur in einen Cluster zu versuchen. In diesem Artikel wird dies als „verknüpft mit einem Cluster“ bezeichnet. Das unten stehende Diagramm 1 zeigt den Fall einer normalen Zuordnung, die in mehreren Clustern versucht wird. Diagramm 2 zeigt den Fall einer mit dem Cluster 2 verknüpften Zuordnung, da dort der vorhandene Clouddienst „CS_1“ oder die Verfügbarkeitsgruppe gehostet wird.
![Zuordnungsdiagramm](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Gründe für Zuordnungsfehler
Wenn eine Zuordnungsanforderung mit einem Cluster verknüpft ist, ist die Wahrscheinlichkeit höher, dass keine freien Ressourcen gefunden werden, da der verfügbare Ressourcenpool kleiner ist. Falls Ihre Zuordnungsanforderung mit einem Cluster verknüpft ist, der von Ihnen angeforderte Ressourcentyp für diesen Cluster aber nicht unterstützt wird, schlägt die Anforderung auch dann fehl, wenn der Cluster über eine freie Ressource verfügt. Im nachstehenden Diagramm 3 ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, weil der einzige Kandidatencluster nicht über freie Ressourcen verfügt. In Diagramm 4 ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, weil der einzige Kandidatencluster die angeforderte Größe des virtuellen Computers nicht unterstützt, obwohl der Cluster über freie Ressourcen verfügt.

![Verknüpfte Zuordnung, Fehler](./media/virtual-machines-common-allocation-failure/Allocation2.png)
