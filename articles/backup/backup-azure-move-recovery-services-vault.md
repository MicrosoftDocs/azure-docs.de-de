---
title: Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements oder in eine andere Ressourcengruppe
description: Anweisungen zum Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sogup
ms.openlocfilehash: 72bfbc34f57e7725ae9556e893825900474317cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64876850"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen

In diesem Artikel wird das Verschieben eines für Azure Backup konfigurierten Recovery Services-Tresor zwischen Azure-Abonnements oder in eine andere Ressourcengruppe im selben Abonnement erläutert. Sie können Azure-Portal oder PowerShell zum Verschieben eines Recovery Services-Tresors verwenden.

## <a name="supported-region"></a>Unterstützte Region

Das Verschieben von Ressourcen für den Recovery Services-Tresor wird derzeit unterstützt in „Australien, Osten“, „Australien, Südosten“, „Kanada, Mitte“, „Kanada, Osten“, „Asien, Südosten“, „Asien, Osten“, „USA, Mitte“, „USA, Norden-Mitte“, „USA, Osten“, „USA, Osten 2“, „USA, Süden-Mitte“, „USA, Westen-Mitte“, „USA, Westen-Mitte 2“, „USA, Westen“, „Indien, Mitte“, „Indien, Süden“, „Japan, Osten“, „Japan, Westen“, „Südkorea, Mitte“, „Südkorea, Süden“, „Europa, Norden“, „Europa, Westen“, „Südafrika, Norden“, „Südafrika, Westen“, „Vereinigtes Königreich, Süden“ und „Vereinigtes Königreich, Westen“.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Voraussetzungen für das Verschieben eines Recovery Services-Tresors

- Während des Verschiebens des Tresors zwischen den Ressourcengruppen sind sowohl die Quell- als auch die Zielgruppen gesperrt und verhindern das Schreiben und Löschen. Weitere Informationen finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Nur das Administratorabonnement verfügt über die Berechtigungen, einen Tresor zu verschieben.
- Beim Verschieben eines Tresors zwischen Abonnements muss sich das Zielabonnement in einem aktivierten Status in demselben Mandanten befinden wie das Quellabonnement.
- Sie benötigen die Berechtigung zum Ausführen der Schreibvorgänge in der Zielressourcengruppe.
- Das Verschieben des Tresors ändert nur die Ressourcengruppe. Der Recovery Services-Tresor befindet sich im selben Speicherort und kann nicht geändert werden.
- Sie können nur einen Recovery Services-Tresor pro Region verschieben.
- Wenn ein virtueller Computer nicht mit dem Recovery Services-Tresor zwischen Abonnements oder in eine neue Ressourcengruppe verschoben wird, bleiben die aktuellen VM-Wiederherstellungspunkte im Tresor intakt, bis sie ablaufen.
- Unabhängig davon, ob die VM mit dem Tresor verschoben wird oder nicht, können Sie die VM immer anhand des Sicherungsverlaufs im Tresor wiederherstellen.
- Azure Disk Encryption setzt voraus, dass der Schlüsseltresor und die VMs zur selben Azure-Region und zum selben Azure-Abonnement gehören.
- Informationen zum Verschieben eines virtuellen Computers mit verwalteten Datenträgern finden Sie in diesem [Artikel](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Die Optionen zum Verschieben von Ressourcen, die über das klassische Modell bereitgestellt wurden, unterscheiden sich abhängig davon, ob Sie die Ressourcen innerhalb eines Abonnements oder in ein neues Abonnement verschieben. Weitere Informationen finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
- Für den Tresor definierte Sicherungsrichtlinien werden beibehalten, nachdem der Tresor zwischen Abonnements oder in eine neue Ressourcengruppe verschoben worden ist.
- Das Verschieben von Tresoren mit Azure Files, Azure-Dateisynchronisierung oder SQL in-IaaS-VMs zwischen Abonnements und Ressourcengruppen wird nicht unterstützt.
- Wenn Sie einen Tresor mit VM-Sicherungsdaten abonnementübergreifend verschieben, müssen Sie Ihre VMs in das gleiche Abonnement verschieben und die gleiche Zielressourcengruppe verwenden, um weiterhin Sicherungen durchführen zu können.<br>

> [!NOTE]
>
> Für die Verwendung mit **Azure Site Recovery** konfigurierte Recovery Services-Tresore können noch nicht verschoben werden. Wenn Sie virtuelle (Azure IaaS, Hyper-V, VMware) oder physische Computer für die Notfallwiederherstellung mit **Azure Site Recovery** konfiguriert haben, wird der Verschiebevorgang blockiert. Die Ressourcenverschiebefunktion für den Site Recovery-Dienst ist noch nicht verfügbar.


## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Verwenden des Azure-Portals zum Verschieben eines Recovery Services-Tresors in eine andere Ressourcengruppe

So verschieben Sie einen Recovery Services-Tresor und die zugehörigen Ressourcen in eine andere Ressourcengruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der Liste der **Recovery Services-Tresore** den Tresor aus, den Sie verschieben möchten. Wenn das Tresordashboard geöffnet wird, wird es wie in der folgenden Abbildung dargestellt angezeigt.

   ![Öffnen des Recovery Services-Tresors](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Wenn Sie die **Essentials**-Informationen für Ihren Tresor nicht sehen, klicken Sie auf das Dropdownsymbol. Jetzt sollten die Essentials-Informationen für Ihren Tresor angezeigt werden.

   ![Registerkarte „Essentials“-Informationen](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Klicken Sie im Übersichtsmenü des Tresors auf **Ändern** neben **Ressourcengruppe**, um das Blatt **Ressourcen verschieben** zu öffnen.

   ![Ressourcengruppe ändern](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Auf dem Blatt **Ressourcen verschieben** für den ausgewählten Tresor sollten die optionalen verwandten Ressourcen durch Aktivieren des Kontrollkästchens verschoben werden, wie in der folgenden Abbildung dargestellt.

   ![Abonnement verschieben](./media/backup-azure-move-recovery-services/move-resource.png)

5. Um die Zielressourcengruppe hinzuzufügen, wählen Sie in der Dropdownliste **Ressourcengruppe** eine vorhandene Ressource aus, oder klicken Sie auf die Option **Neue Gruppe erstellen**.

   ![Resource erstellen](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Bestätigen Sie nach dem Hinzufügen der Ressourcengruppe mit der Option **Mir ist bewusst, dass Tools und Skripts, die den verschobenen Ressourcen zugeordnet sind, erst funktionieren, wenn ich sie auf die Verwendung neuer Ressourcen-IDs und Namespacereferenzen aktualisiere**, und klicken Sie dann auf **OK**, um das Verschieben des Tresors abzuschließen.

   ![Bestätigungsmeldung](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Verwenden des Azure-Portals zum Verschieben eines Recovery Services-Tresors in ein anderes Abonnement

Sie können einen Recovery Services-Tresor und die zugehörigen Ressourcen in ein anderes Abonnement verschieben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der Liste der Recovery Services-Tresore den Tresor aus, den Sie verschieben möchten. Wenn das Tresordashboard geöffnet wird, wird es wie in der folgenden Abbildung dargestellt angezeigt.

    ![Öffnen des Recovery Services-Tresors](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Wenn Sie die **Essentials**-Informationen für Ihren Tresor nicht sehen, klicken Sie auf das Dropdownsymbol. Jetzt sollten die Essentials-Informationen für Ihren Tresor angezeigt werden.

    ![Registerkarte „Essentials“-Informationen](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Klicken Sie im Übersichtsmenü des Tresors auf **Ändern** neben **Abonnement**, um das Blatt **Ressourcen verschieben** zu öffnen.

   ![Abonnement ändern](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Wählen Sie die zu verschiebenden Ressourcen aus. Hier sollten Sie die Option **Alles markieren** verwenden, um alle aufgeführten optionalen Ressourcen auszuwählen.

   ![Ressource verschieben](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Wählen Sie das Zielabonnement, in das der Tresor verschoben werden soll, in der Dropdownliste **Abonnement** aus.
6. Um die Zielressourcengruppe hinzuzufügen, wählen Sie in der Dropdownliste **Ressourcengruppe** eine vorhandene Ressource aus, oder klicken Sie auf die Option **Neue Gruppe erstellen**.

   ![Abonnement hinzufügen](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klicken Sie zur Bestätigung auf **Mir ist bewusst, dass Tools und Skripts, die den verschobenen Ressourcen zugeordnet sind, erst funktionieren, wenn ich sie auf die Verwendung neuer Ressourcen-IDs** aktualisiere, und klicken Sie auf **OK**.

> [!NOTE]
> Abonnementübergreifende Sicherung (RS-Tresor und geschützte virtuelle Computer befinden sich in verschiedenen Abonnements) wird nicht unterstützt. Außerdem kann die Speicherredundanzoption von lokalem redundantem Speicher (LRS) zu globalem redundantem Speicher (GRS) und umgekehrt während der Verschiebung des Tresors nicht geändert werden.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Verwenden von PowerShell zum Verschieben eines Recovery Services-Tresors

Um einen Recovery Services-Tresor in eine andere Ressourcengruppe zu verschieben, verwenden Sie das `Move-AzureRMResource`-Cmdlet. `Move-AzureRMResource` benötigt den Ressourcennamen und den Typ der Ressource. Sie erhalten beides über das `Get-AzureRmRecoveryServicesVault`-Cmdlet.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Um Ressourcen in ein anderes Abonnement zu verschieben, schließen Sie den `-DestinationSubscriptionId`-Parameter ein.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Nach Ausführen der obigen Cmdlets werden Sie aufgefordert, zu bestätigen, dass Sie die angegebenen Ressourcen verschieben möchten. Geben Sie **J** zur Bestätigung ein. Nach einer erfolgreichen Validierung wird die Ressource verschoben.

## <a name="use-cli-to-move-recovery-services-vault"></a>Verwenden der CLI zum Verschieben eines Recovery Services-Tresors

Um einen Recovery Services-Tresor in eine andere Ressourcengruppe zu verschieben, verwenden Sie das folgende Cmdlet:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Um Ressourcen in ein neues Abonnement zu verschieben, geben Sie den Parameter`--destination-subscription-id` an.

## <a name="post-migration"></a>Nach der Migration

1. Sie müssen die Zugriffssteuerungen für die Ressourcengruppen festlegen/überprüfen.  
2. Die Funktion zur Berichterstellung über die Sicherung und deren Überwachung muss nach Abschluss der Verschiebung für den Tresor neu konfiguriert werden. Die vorherige Konfiguration geht während des Verschiebevorgangs verloren.



## <a name="next-steps"></a>Nächste Schritte

Sie können viele verschiedene Arten von Ressourcen zwischen Ressourcengruppen und Abonnements verschieben.

Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
