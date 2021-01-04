---
title: 'Azure-Portal: Einschränken des Import-/Exportzugriffs auf verwaltete Datenträger mit privaten Links'
description: Aktivieren Sie über das Azure-Portal private Links für Ihre verwalteten Datenträger. Dadurch können Sie Datenträger innerhalb Ihres virtuellen Netzwerks sicher exportieren und importieren.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 68a308952b63d15aa6db90021400f826ef575c0c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537185"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Verwenden Sie das Azure-Portal zum Einschränken des Import-/Exportzugriffs für verwaltete Datenträger mit privaten Links.

Die Unterstützung für private Links für verwaltete Datenträger ermöglicht es Ihnen, den Export und Import von verwalteten Datenträgern einzuschränken, sodass diese Vorgänge nur in Ihrem virtuellen Azure-Netzwerk stattfinden. Sie können einen zeitgebundenen SAS-URI (Shared Access Signature) für nicht angefügte verwaltete Datenträger und Momentaufnahmen generieren, um die Daten zur regionalen Erweiterung, zur Notfallwiederherstellung und zum Lesen der Daten für die forensische Analyse in eine andere Region zu exportieren. Sie können den SAS-URI auch verwenden, um eine VHD aus der lokalen Umgebung direkt auf einen leeren Datenträger hochzuladen. Der Netzwerkdatenverkehr zwischen den Clients im virtuellen Netzwerk und den verwalteten Datenträgern wird nur über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet und somit nicht dem öffentlichen Internet ausgesetzt.

Sie können eine Datenträgerzugriffsressource erstellen und mit einem virtuellen Netzwerk im gleichen Abonnement verknüpfen, indem Sie einen privaten Endpunkt erstellen. Sie müssen einen Datenträger oder eine Momentaufnahme mit einem Datenträgerzugriff verknüpfen, um die Daten über private Links zu exportieren und zu importieren. Darüber hinaus müssen Sie die NetworkAccessPolicy-Eigenschaft des Datenträgers oder der Momentaufnahme auf `AllowPrivate` festlegen. 

Sie können die NetworkAccessPolicy-Eigenschaft auf `DenyAll` festlegen, um zu verhindern, dass der SAS-URI für einen Datenträger oder eine Momentaufnahme generiert wird. Der Standardwert für die NetworkAccessPolicy-Eigenschaft ist `AllowAll`.

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Erstellen einer Datenträgerzugriffsressource

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie über [diesen Link](https://aka.ms/disksprivatelinks) zu **Datenträgerzugriff**.

    > [!IMPORTANT]
    > Sie müssen über den [bereitgestellten Link](https://aka.ms/disksprivatelinks) zum Blatt „Datenträgerzugriff“ navigieren. Andernfalls wird das Blatt nicht im öffentlichen Portal angezeigt.

1. Wählen Sie **+ Hinzufügen** aus, um eine neue Datenträgerzugriffsressource zu erstellen.
1. Wählen Sie auf dem Blatt „Erstellen“ Ihr Abonnement und eine Ressourcengruppe aus, geben Sie einen Namen ein, und wählen Sie eine Region aus.
1. Klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Screenshot: Blatt zum Erstellen des Datenträgerzugriffs. Eingeben des gewünschten Namens, Auswählen einer Region und einer Ressourcengruppe und Fortfahren":::

Wenn Ihre Ressource erstellt wurde, navigieren Sie direkt zu ihr.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Screenshot: Schaltfläche „Zu Ressource wechseln“ im Portal":::

## <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

Nachdem Sie nun über eine Datenträgerzugriffsressource verfügen, können Sie damit den Zugriff auf die Exporte und Importe Ihres Datenträgers behandeln. Dafür werden private Endpunkte verwendet. Dementsprechend müssen Sie einen privaten Endpunkt erstellen und für den Datenträgerzugriff konfigurieren.

1. Wählen Sie über die Datenträgerzugriffsressource die Option **Verbindungen mit privatem Endpunkt** aus.
1. Wählen Sie **+ Privaten Endpunkt hinzufügen** aus.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Screenshot: Blatt „Übersicht“ für Ihre Datenträgerzugriffsressource. „Verbindungen mit privatem Endpunkt“ ist hervorgehoben.":::

1. Auswählen einer Ressourcengruppe
1. Geben Sie den Namen ein, und wählen Sie die Region aus, in der Ihre Datenträgerzugriffsressource erstellt wurde.
1. Klicken Sie auf **Weiter: Ressource >** .

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Screenshot: Workflow zum Erstellen des privaten Endpunkts (erstes Blatt) Wenn Sie nicht die entsprechende Region auswählen, treten später möglicherweise Probleme auf.":::

1. Wählen Sie auf dem Blatt **Ressource** die Option **Mit einer Azure-Ressource in meinem Verzeichnis verbinden** aus.
1. Wählen Sie für **Ressourcentyp** den Wert **Microsoft.Compute/diskAccesses** aus.
1. Wählen Sie für **Ressource** die zuvor erstellte Datenträgerzugriffsressource aus.
1. Übernehmen Sie unter **Untergeordnete Zielressource** den Wert **Datenträger**.
1. Wählen Sie **Weiter: Konfiguration >** .

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Screenshot: Workflow zum Erstellen des privaten Endpunkts (zweites Blatt) mit hervorgehobenen Werten (Ressourcentyp, Ressource, Untergeordnete Zielressource)":::

1. Wählen Sie das virtuelle Netzwerk aus, auf das Sie den Datenträgerexport beschränken möchten. Andere virtuelle Netzwerke können den Datenträger nicht exportieren.

    > [!NOTE]
    > Wenn für das ausgewählte Subnetz eine Netzwerksicherheitsgruppe (NSG) aktiviert ist, wird diese nur für private Endpunkte in diesem Subnetz deaktiviert. Für andere Ressourcen in diesem Subnetz wird die NSG weiterhin durchgesetzt.

1. Auswählen des geeigneten Subnetzes
1. Klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Screenshot: Workflow zum Erstellen des privaten Endpunkts (drittes Blatt) mit hervorgehobenem virtuellem Netzwerk und Subnetz":::

## <a name="enable-private-endpoint-on-your-disk"></a>Aktivieren des privaten Endpunkts auf Ihrem Datenträger

1. Navigieren Sie zu dem Datenträger, den Sie konfigurieren möchten.
1. Wählen Sie **Netzwerk**.
1. Wählen Sie **Private endpoint (through disk access)** (Privater Endpunkt (über Datenträgerzugriff)) und dann den zuvor erstellten Datenträgerzugriff aus.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Screenshot: Blatt „Netzwerk“ für verwaltete Datenträger. Privater Endpunkt und ausgewählter Datenträgerzugriff hervorgehoben. Durch das Speichern wird der Datenträger für diesen Zugriff konfiguriert.":::

Die Konfiguration privater Links ist nun abgeschlossen, und Sie können sie beim Importieren und Exportieren Ihrer verwalteten Datenträger verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen zu privaten Links](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportieren/Kopieren verwalteter Momentaufnahmen als VHD in ein Speicherkonto in einer anderen Region mit PowerShell](./scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md)
