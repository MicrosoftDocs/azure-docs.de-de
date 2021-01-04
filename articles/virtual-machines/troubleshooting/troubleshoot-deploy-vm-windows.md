---
title: Problembehandlung von Problemen bei der Bereitstellung von virtuellen Windows-Computern in Azure | Microsoft-Dokumentation
description: Behandlung von Problemen bei der Bereitstellung virtueller Windows-Computer im Azure Resource Manager-Bereitstellungsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 8ed567ee20835cfe076c9b06234f2cba23f3a8bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002557"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Problembehandlung von Problemen bei der Bereitstellung von virtuellen Windows-Computern in Azure

Um Bereitstellungsprobleme virtueller Computer (VMs) in Azure zu behandeln, sehen Sie sich die [wichtigsten Probleme](#top-issues) für häufige Fehler und die Lösungen dazu an.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)Fragen stellen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten** aus.

## <a name="top-issues"></a>Häufigste Probleme
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Der Cluster unterstützt nicht die angeforderte Größe des virtuellen Computers.
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Wiederholen Sie die Anforderung mit einer geringeren Größe des virtuellen Computers.
- Wenn die Größe des angeforderten virtuellen Computers nicht geändert werden kann:
    - Beenden Sie alle virtuellen Computer in der Verfügbarkeitsgruppe. Klicken Sie auf **Ressourcengruppen** > Ihre Ressourcengruppe > **Ressourcen** > Ihre Verfügbarkeitsgruppe > **virtuelle Computer** > Ihr virtueller Computer > **Beenden**.
    - Nachdem alle virtuellen Computer beendet wurden, erstellen Sie den virtuellen Computer in der gewünschten Größe.
    - Starten Sie zunächst den neuen virtuellen Computer, wählen Sie dann alle angehaltenen virtuellen Computer aus, und klicken Sie auf „Starten“.


## <a name="the-cluster-does-not-have-free-resources"></a>Der Cluster besitzt keine freien Ressourcen.
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Versuchen Sie die Anforderung später erneut.
- Wenn der neue virtuelle Computer Teil einer anderen Verfügbarkeitsgruppe sein kann
    - Erstellen Sie einen virtuellen Computer in einer anderen Verfügbarkeitsgruppe (in derselben Region).
    - Fügen Sie den neuen virtuellen Computer zum gleichen virtuellen Netzwerk hinzu.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Wie kann ich ein Windows-Clientimage in Azure bereitstellen und verwenden?

Sie können Windows 7, Windows 8 oder Windows 10 in Azure für Entwicklungs- bzw. Testszenarios verwenden, sofern Sie über ein entsprechendes Visual Studio-Abonnement (früher MSDN) verfügen. Dieser [Artikel](../windows/client-images.md) beschreibt die erforderlichen Berechtigungen für die Ausführung des Windows-Clients in Azure und die Verwendung von Images aus dem Azure-Katalog.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Wie kann ich einen virtuellen Computer mithilfe des Hybridnutzungsvorteil (HUB) bereitstellen?

Es gibt verschiedene Möglichkeiten zum Bereitstellen von Windows-VMs mit dem Azure-HUB.

Für ein Abonnement mit einem Enterprise Agreement:

• Bereitstellen von virtuellen Computern aus bestimmten Marketplace-Images, die mit Azure-HUB vorkonfiguriert wurden.

Für das Enterprise Agreement:

• Laden Sie eine benutzerdefinierte VM hoch und stellen Sie diese mit einer Resource Manager-Vorlage oder Azure PowerShell bereit.

Weitere Informationen finden Sie in den folgenden Ressourcen:

 - [Überblick zum Hybridnutzungsvorteil von Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Herunterladen häufig gestellter Fragen](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Hybridnutzungsvorteil von Azure für Windows Server und Windows-Client](../windows/hybrid-use-benefit-licensing.md)

 - [Wie kann ich den Hybridnutzungsvorteil in Azure verwenden](/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Wie aktiviere ich mein monatliches Guthaben für Visual Studio Enterprise (BizSpark)

Um Ihr monatliches Guthaben zu aktivieren, lesen Sie diesen [Artikel](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Wie kann ich Enterprise Dev/Test in mein Enterprise Agreement (EA) einfügen, um Zugriff auf Windows-Clientimages zu erhalten?

Die Möglichkeit zum Erstellen von Abonnements basierend auf dem Enterprise Dev/Test-Angebot ist auf Kontobesitzer beschränkt, denen durch einen Unternehmensadministrator entsprechende Berechtigungen erteilt wurden. Der Kontobesitzer schließt Abonnements über das Azure-Kontoportal ab und sollte dann aktive Abonnenten von Visual Studio als Co-Administratoren hinzufügen. So kann er die erforderlichen Ressourcen für die Entwicklung und Prüfung verwalten und nutzen. Weitere Informationen finden Sie unter [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Die Treiber für meine Windows-VM der N-Serie fehlen.

Anleitungen zum Installieren der Treiber für Windows-basierte VMs finden Sie [hier](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Ich kann keine GPU-Instanz in meiner VM der N-Serie finden

Nach der Bereitstellung müssen Sie auf jeder VM Grafiktreiber installieren, um die GPU-Funktionen von Azure-VMs der N-Serie nutzen zu können. Informationen zur Treibereinrichtung sind [hier](../sizes-gpu.md#supported-operating-systems-and-drivers) verfügbar.

## <a name="are-n-series-vms-available-in-my-region"></a>Stehen VMs der N-Serie in meiner Region zur Verfügung?

Informationen zur Verfügbarkeit finden Sie in der [Tabelle verfügbarer Produkte nach Region](https://azure.microsoft.com/regions/services), und Informationen zu Preisen finden Sie [hier](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Welche Clientimages kann ich verwenden und in Azure bereitstellen, und wie kann ich diese erhalten?

Sie können Windows 7, Windows 8 oder Windows 10 in Azure für Entwicklungs-/Testszenarien verwenden, sofern Sie über ein entsprechendes Visual Studio-Abonnement (früher MSDN) verfügen. 

- Windows 10-Images stehen im Azure-Katalog unter [berechtigte Dev/Test-Angebote](../windows/client-images.md#eligible-offers). 
- Visual Studio-Abonnenten können auch in jedem Angebotstyp ein 64-Bit-Image für Windows 7, Windows 8 oder Windows 10 [angemessen vorbereiten und erstellen](../windows/prepare-for-upload-vhd-image.md) und dann [in Azure hochladen](../windows/upload-generalized-managed.md). Die Verwendung bleibt auf Entwicklung/Test durch aktive Visual Studio-Abonnenten beschränkt.

Dieser [Artikel](../windows/client-images.md) beschreibt die erforderlichen Berechtigungen für die Ausführung des Windows-Clients in Azure und die Verwendung von Images aus dem Azure-Katalog.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Ich kann keine VM-Größenfamilie sehen, die ich bei der Größenänderung meines virtuellen Computers haben möchte.

Wenn eine VM ausgeführt wird, wird Sie auf einem physischen Server bereitgestellt. Die physischen Server in Azure-Regionen werden in Clustern gemeinsamer physischer Hardware gruppiert. Das Ändern der Größe einer VM, das erfordert, dass die VM in andere Hardwareclustern verschoben wird, kann unterschiedlich sein, je nachdem, welches Bereitstellungsmodell zur Bereitstellung der VM verwendet wurde.

- Für VMs, die im klassischen Bereitstellungsmodell bereitgestellt werden, muss die Clouddienstbereitstellung entfernt werden und erneut bereitgestellt werden, um die Größe der VMs in eine andere Größenfamilie zu ändern.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Für die im Resource Manager-Bereitstellungsmodell bereitgestellten virtuellen Computer müssen Sie alle VMs in der Verfügbarkeitsgruppe anhalten, bevor Sie die Größe einer VM in der Verfügbarkeitsgruppe ändern.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Die aufgelistete Größe des virtuellen Computers wird bei der Bereitstellung in der Verfügbarkeitsgruppe nicht unterstützt.

Wählen Sie eine Größe aus, die im Verfügbarkeitsgruppencluster unterstützt wird. Es wird empfohlen, dass Sie beim Erstellen der Verfügbarkeitsgruppe die größte VM-Größe auswählen, von der Sie glauben, dass Sie sie benötigen. Diese ist dann Ihre erste Bereitstellung für die Verfügbarkeitsgruppe.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kann ich einen vorhandenen klassischen virtuellen Computer zu einer Verfügbarkeitsgruppe hinzufügen?

Ja. Sie können einen vorhandenen klassischen virtuellen Computer in eine neue oder vorhandene Verfügbarkeitsgruppe hinzufügen. Weitere Informationen finden Sie unter [Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)Fragen stellen.

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten** aus.
