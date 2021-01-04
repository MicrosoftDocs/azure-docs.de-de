---
title: Erstellen eines Azure-VM-Angebots aus einer genehmigten Basis, Azure Marketplace
description: Erfahren Sie, wie Sie ein Azure-VM-Angebot aus einer genehmigten Basis erstellen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: c28595458fea61f66b6930ce72fa0702e00aecd9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129295"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Erstellen eines virtuellen Computers mit einer genehmigten Basis

In diesem Artikel wird beschrieben, wie Sie mit Azure eine VM mit einem vorkonfigurierten, unterstützten Betriebssystem erstellen. Wenn es mit Ihrer Lösung nicht kompatibel ist, können Sie [eine lokale VM unter Verwendung eines genehmigten Betriebssystems erstellen und konfigurieren](azure-vm-create-using-own-image.md) und diese dann wie unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) beschrieben konfigurieren und für das Hochladen vorbereiten.

> [!NOTE]
> Bevor Sie beginnen, überprüfen Sie die [technischen Anforderungen](marketplace-virtual-machines.md#technical-requirements) für Azure-VM-Angebote, einschließlich der Anforderungen an virtuelle Festplatten (Virtual Hard Disks, VHDs).

## <a name="select-an-approved-base-image"></a>Auswählen eines genehmigten Basisimages

Wählen Sie eines der folgenden Windows- oder Linux-Images als Basis aus.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure bietet eine Reihe von genehmigten Linux-Distributionen. Eine aktuelle Liste finden Sie unter [Linux auf von Azure unterstützten Distributionen](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Erstellen der VM im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.
2. Wählen Sie **Virtuelle Computer** aus.
3. Wählen Sie **+ Hinzufügen** aus, um den Bildschirm **Virtuellen Computer erstellen** zu öffnen.
4. Wählen Sie das Image in der Dropdownliste oder **Alle öffentlichen und privaten Images durchsuchen** aus, um nach allen verfügbaren VM-Images zu suchen.
5. Um eine **Gen 2** -VM zu erstellen, navigieren Sie zur Registerkarte **Erweitert** und wählen die Option **Gen 2** aus.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Wählen Sie „Gen 1“ oder „Gen 2“ aus.":::

6. Wählen Sie die Größe der bereitzustellenden VM aus.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Wählen Sie eine empfohlene VM-Größe für das ausgewählte Image aus.":::

7. Geben Sie die anderen Details ein, die zum Erstellen der VM erforderlich sind.
8. Wählen Sie **Bewerten + erstellen** aus, um Ihre Auswahl zu überprüfen. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

Azure beginnt mit der Bereitstellung der von Ihnen angegebenen VM. Sie können den Fortschritt nachverfolgen, indem Sie im linken Menü die Registerkarte **Virtuelle Computer** auswählen. Nach der Erstellung ändert sich der Status des virtuellen Computers in **Wird ausgeführt**.


## <a name="configure-the-vm"></a>Konfigurieren der VM

In diesem Abschnitt wird beschrieben, wie Sie die Größe einer Azure-VM anpassen, sie aktualisieren und generalisieren. Diese Schritte sind erforderlich, um Ihre VM für die Bereitstellung in Azure Marketplace vorzubereiten.

### <a name="connect-to-your-vm"></a>Herstellen einer Verbindung mit Ihrer VM

In der folgenden Dokumentation erfahren Sie, wie Sie eine Verbindung mit einer [Windows](../virtual-machines/windows/connect-logon.md)- oder [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)-VM herstellen.

### <a name="install-the-most-current-updates"></a>Installieren der aktuellsten Updates

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Durchführen von zusätzlichen Sicherheitsüberprüfungen

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Durchführen der benutzerdefinierten Konfiguration und von geplanten Aufgaben

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Nächste Schritte

- Empfohlener nächster Schritt: [Testen Sie das VM-Image](azure-vm-image-test.md), um sicherzustellen, dass es die Veröffentlichungsanforderungen von Azure Marketplace erfüllt. Dies ist optional.
- Wenn Sie Ihr VM-Image nicht testen, fahren Sie mit dem [Generieren des SAS-URIs](azure-vm-get-sas-uri.md) fort.
- Wenn beim Erstellen der neuen Azure-basierten VHD Probleme auftreten, informieren Sie sich in den [FAQ zu VMs für Azure Marketplace](azure-vm-create-faq.md).