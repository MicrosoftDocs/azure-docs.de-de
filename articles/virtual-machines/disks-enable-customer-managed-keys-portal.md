---
title: 'Azure-Portal: Aktivieren kundenseitig verwalteter Schlüssel mit SSE – verwaltete Datenträger'
description: Aktivieren Sie kundenseitig verwaltete Schlüssel auf Ihren verwalteten Datenträgern über das Azure-Portal.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5452b1e2ffc3ca8a11f3acb591c6288806445e1b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499389"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Verwenden des Azure-Portals zum Aktivieren der serverseitigen Verschlüsselung mit kundenseitig verwalteten Schlüsseln für verwaltete Datenträger

Mit Azure Disk Storage können Sie Ihre eigenen Schlüssel verwalten, wenn Sie die serverseitige Verschlüsselung (Server Side Encryption, SSE) für verwaltete Datenträger verwenden. Informationen zum Konzept der SSE mit kundenseitig verwalteten Schlüsseln sowie weitere Verschlüsselungstypen für verwaltete Datenträger finden Sie im Abschnitt **Vom Kunden verwaltete Schlüssel** in folgenden Artikeln zur Datenträgerverschlüsselung:

- Linux: [Kundenseitig verwaltete Schlüssel](./disk-encryption.md#customer-managed-keys)
- Windows: [Kundenseitig verwaltete Schlüssel](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Beschränkungen

Vorerst gelten für vom Kunden verwaltete Schlüssel die folgenden Einschränkungen:

- Wenn dieses Feature für Ihren Datenträger aktiviert ist, können Sie es nicht deaktivieren.
    Bei Bedarf müssen Sie alle Daten auf einen anderen verwalteten Datenträger ohne kundenseitig verwaltete Schlüssel kopieren:

    - Linux: [Kopieren eines verwalteten Datenträgers](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Windows: [Kopieren eines verwalteten Datenträgers](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- Es werden ausschließlich [Software- und HSM RSA-Schlüssel](../key-vault/keys/about-keys.md) der Größen 2.048 Bit, 3.072 Bit und 4.096 Bit unterstützt.
    - Für [HSM](../key-vault/keys/hsm-protected-keys.md)-Schlüssel ist der **Premium**-Tarif von Azure Key Vault erforderlich.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

In den folgenden Abschnitten wird erläutert, wie Sie von Kunden verwaltete Schlüssel für verwaltete Datenträger aktivieren und verwenden:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Bereitstellen einer VM

Nachdem Sie nun Ihren Schlüsseltresor und den Datenträgerverschlüsselungssatz erstellt und eingerichtet haben, können Sie eine VM mit der Verschlüsselung bereitstellen.
Das Verfahren zur VM-Bereitstellung ähnelt dem Standardbereitstellungsverfahren. Die einzigen Unterschiede bestehen darin, dass Sie den virtuellen Computer in derselben Region wie Ihre anderen Ressourcen bereitstellen müssen und einen vom Kunden verwalteten Schlüssel verwenden.

1. Suchen Sie nach **Virtuelle Computer**, und wählen Sie **+ Hinzufügen** aus, um eine VM zu erstellen.
1. Wählen Sie auf dem Blatt **Standard** dieselbe Region aus, in der sich Ihr Datenträgerverschlüsselungssatz und Ihre Azure Key Vault-Instanz befinden.
1. Geben Sie die anderen Werte auf dem Blatt **Standard** nach Wunsch ein.

    ![Screenshot des VM-Erstellungsvorgangs mit hervorgehobenem Wert für die Region.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Wählen Sie auf dem Blatt **Datenträger** die Option **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** aus.
1. Wählen Sie den Datenträgerverschlüsselungssatz in der Dropdownliste **Datenträgerverschlüsselungssatz** aus.
1. Treffen Sie die verbleibende Auswahl nach Wunsch.

    ![Screenshot des VM-Erstellungsvorgangs, Datenträgerblatt. Das Dropdownfeld für den Datenträgerverschlüsselungssatz ist hervorgehoben.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Aktivieren auf einem vorhandenen Datenträger

> [!CAUTION]
> Zum Aktivieren der Datenträgerverschlüsselung auf Datenträgern, die an eine VM angefügt sind, müssen Sie die VM beenden.
    
1. Navigieren Sie zu einer VM, die sich in derselben Region befindet wie einer Ihrer Datenträgerverschlüsselungssätze.
1. Öffnen Sie die VM, und wählen Sie **Beenden** aus.

    ![Screenshot der Hauptüberlagerung für Ihre Beispiel-VM, wobei die Schaltfläche „Beenden“ hervorgehoben ist.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Nachdem die VM beendet wurde, wählen Sie **Datenträger** und dann den Datenträger aus, den Sie verschlüsseln möchten.

    ![Screenshot Ihrer Beispiel-VM mit geöffnetem Datenträgerblatt. Der Betriebssystemdatenträger ist als Beispieldatenträger hervorgehoben, den Sie auswählen können.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Wählen Sie **Verschlüsselung**, anschließend **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** und dann in der Dropdownliste den Datenträgerverschlüsselungssatz aus.
1. Wählen Sie **Speichern** aus.

    ![Screenshot Ihres Beispiel-Betriebssystemdatenträgers. Das Verschlüsselungsblatt ist geöffnet, die Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel und Ihr Beispiel-Azure Key Vault sind ausgewählt. Nach dem Treffen dieser Auswahl wird die Speichern-Schaltfläche ausgewählt.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Wiederholen Sie diesen Vorgang für alle anderen an die VM angefügten Datenträger, die Sie verschlüsseln möchten.
1. Wenn die Umstellung der Datenträger auf kundenseitig verwaltete Schlüssel abgeschlossen ist und Sie keine weiteren angefügten Datenträger mehr verschlüsseln möchten, können Sie Ihren virtuellen Computer starten.

> [!IMPORTANT]
> Von Kunden verwaltete Schlüssel basieren auf verwalteten Identitäten für Azure-Ressourcen, einem Feature von Azure Active Directory (Azure AD). Wenn Sie vom Kunden verwaltete Schlüssel konfigurieren, wird Ihren Ressourcen im Hintergrund automatisch eine verwaltete Identität zugewiesen. Wenn Sie anschließend das Abonnement, die Ressourcengruppe oder den verwalteten Datenträger von einem Azure AD-Verzeichnis in ein anderes Verzeichnis verschieben, wird die den verwalteten Datenträgern zugeordnete verwaltete Identität nicht an den neuen Mandanten übertragen, sodass kundenseitig verwaltete Schlüssel möglicherweise nicht mehr funktionieren. Weitere Informationen finden Sie unter [Übertragen eines Abonnements zwischen Azure AD-Verzeichnissen](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Nächste Schritte

- [Untersuchen der Azure Resource Manager-Vorlagen zum Erstellen verschlüsselter Datenträger mit vom Kunden verwalteten Schlüsseln](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)
- [Replizieren von Computern mit Datenträgern, die für kundenseitig verwaltete Schlüssel aktiviert sind](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Einrichten der Notfallwiederherstellung von virtuellen VMware-Computern in Azure mithilfe von PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Einrichten der Notfallwiederherstellung in Azure für Hyper-V-VMs mithilfe von PowerShell und Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)