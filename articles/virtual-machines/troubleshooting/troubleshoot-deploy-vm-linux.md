---
title: Behandlung von Problemen bei der Bereitstellung virtueller Linux-Computer in Azure | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Behandlung von Problemen bei der Bereitstellung virtueller Linux-Computer mit dem Azure Resource Manager-Bereitstellungsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 1317a4731d3598c5fba317167ba4a45d95823ca2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593269"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Behandlung von Problemen bei der Bereitstellung virtueller Linux-Computer in Azure

Um Bereitstellungsprobleme virtueller Computer in Azure zu behandeln, sehen Sie sich die [wichtigsten Probleme](#top-issues) für häufige Fehler und die Lösungen dazu an.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)Fragen stellen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.

## <a name="top-issues"></a>Häufigste Probleme
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Der Cluster unterstützt nicht die angeforderte Größe des virtuellen Computers.
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Wiederholen Sie die Anforderung mit einer geringeren Größe des virtuellen Computers.
- Wenn die Größe des angeforderten virtuellen Computers nicht geändert werden kann:
    - Beenden Sie alle virtuellen Computer in der Verfügbarkeitsgruppe. Klicken Sie auf **Ressourcengruppen** > Ihre Ressourcengruppe > **Ressourcen** > Ihre Verfügbarkeitsgruppe > **virtuelle Computer** > Ihr virtueller Computer > **Beenden**.
    - Nachdem alle virtuellen Computer beendet wurden, erstellen Sie den virtuellen Computer in der gewünschten Größe.
    - Starten Sie zunächst den neuen virtuellen Computer, wählen Sie dann alle angehaltenen virtuellen Computer aus, und klicken Sie auf „Starten“.


## <a name="the-cluster-does-not-have-free-resources"></a>Der Cluster besitzt keine freien Ressourcen.
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Versuchen Sie die Anforderung später erneut.
- Wenn der neue virtuelle Computer Teil einer anderen Verfügbarkeitsgruppe sein kann
    - Erstellen Sie einen virtuellen Computer in einer anderen Verfügbarkeitsgruppe (in derselben Region).
    - Fügen Sie den neuen virtuellen Computer zum gleichen virtuellen Netzwerk hinzu.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Wie aktiviere ich meine monatliche Gutschrift für Visual Studio Enterprise (BizSpark)?

Informationen zum Aktivieren Ihrer monatlichen Gutschrift finden Sie in [diesem Artikel](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Warum kann ich den GPU-Treiber nicht für eine NV-VM mit Ubuntu installieren?

Derzeit ist Linux-GPU-Unterstützung nur auf virtuellen Azure NC-Computern verfügbar, die Ubuntu Server 16.04 LTS ausführen. Weitere Informationen finden Sie unter [Einrichten von GPU-Treibern für virtuelle Computer der N-Serie unter Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Meine Treiber fehlen auf meiner Linux-VM der N-Serie

Treiber für auf Linux basierende VMs befinden sich [hier](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Ich kann keine GPU-Instanz in meiner VM der N-Serie finden

Nach der Bereitstellung müssen Sie auf jedem virtuellen Computer NVIDIA-Grafiktreiber installieren, um die GPU-Funktionen von virtuellen Azure-Computern der N-Serie mit Windows Server 2016 oder Windows Server 2012 R2 nutzen zu können. Informationen zur Einrichtung von Treibern stehen für [Windows-VMs](../windows/n-series-driver-setup.md) und [Linux-VMs](../linux/n-series-driver-setup.md) zur Verfügung.

## <a name="is-n-series-vms-available-in-my-region"></a>Sind VMs der N-Serie in meiner Region verfügbar?

Sie können die Verfügbarkeit in der Tabelle [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services) und die Preise [hier](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series) finden.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Ich kann keine VM-Größenfamilie sehen, die ich bei der Größenänderung meines virtuellen Computers haben möchte.

Wenn eine VM ausgeführt wird, wird Sie auf einem physischen Server bereitgestellt. Die physischen Server in Azure-Regionen werden in Clustern gemeinsamer physischer Hardware gruppiert. Das Ändern der Größe einer VM, das erfordert, dass die VM in andere Hardwareclustern verschoben wird, kann unterschiedlich sein, je nachdem, welches Bereitstellungsmodell zur Bereitstellung der VM verwendet wurde.

- Für VMs, die im klassischen Bereitstellungsmodell bereitgestellt werden, muss die Clouddienstbereitstellung entfernt werden und erneut bereitgestellt werden, um die Größe der VMs in eine andere Größenfamilie zu ändern.

- Für die im Resource Manager-Bereitstellungsmodell bereitgestellten virtuellen Computer müssen Sie alle VMs in der Verfügbarkeitsgruppe anhalten, bevor Sie die Größe einer VM in der Verfügbarkeitsgruppe ändern.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Die aufgelistete Größe des virtuellen Computers wird bei der Bereitstellung in der Verfügbarkeitsgruppe nicht unterstützt.

Wählen Sie eine Größe aus, die im Verfügbarkeitsgruppencluster unterstützt wird. Es wird empfohlen, dass Sie beim Erstellen der Verfügbarkeitsgruppe die größte VM-Größe auswählen, von der Sie glauben, dass Sie sie benötigen. Diese ist dann Ihre erste Bereitstellung für die Verfügbarkeitsgruppe.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Welche Linux-Verteilungen bzw. -Versionen werden in Azure unterstützt?

Sie können die Liste unter [Linux auf von Azure unterstützten Verteilungen](../linux/endorsed-distros.md) finden.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kann ich einen vorhandenen klassischen virtuellen Computer zu einer Verfügbarkeitsgruppe hinzufügen?

Ja. Sie können einen vorhandenen klassischen virtuellen Computer in eine neue oder vorhandene Verfügbarkeitsgruppe hinzufügen. Weitere Informationen finden Sie unter [Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)Fragen stellen.

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.
