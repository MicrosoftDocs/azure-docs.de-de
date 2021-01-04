---
title: 'Schnellstart: Sichern eines virtuellen Computers mit der Azure CLI'
description: In diesem Schnellstart erfahren Sie, wie Sie einen Recovery Services-Tresor erstellen, den Schutz für einen virtuellen Computer aktivieren und mit der Azure-Befehlszeilenschnittstelle einen anfänglichen Wiederherstellungspunkt erstellen.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 27294f91fd6c79b10a85678a7acd60de56cf1ca4
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562337"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-azure-cli"></a>Sichern eines virtuellen Computers in Azure mit der Azure CLI

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. Sie können Ihre Daten schützen, indem Sie in regelmäßigen Abständen Sicherungen erstellen. Azure Backup erstellt Wiederherstellungspunkte, die in georedundanten Recovery-Tresoren gespeichert werden können. In diesem Artikel wird ausführlich beschrieben, wie Sie einen virtuellen Computer (VM) in Azure mit der Azure CLI sichern. Sie können diese Schritte auch mit [Azure PowerShell](quick-backup-vm-powershell.md) oder im [Azure-Portal](quick-backup-vm-portal.md) ausführen.

In dieser Schnellstartanleitung wird die Sicherung für einen vorhandenen virtuellen Azure-Computer aktiviert. Wenn Sie eine VM erstellen müssen, können Sie die [Erstellung mit der Azure CLI durchführen](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für diesen Schnellstart ist mindestens Version 2.0.18 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Ein Recovery Services-Tresor ist ein logischer Container, in dem die Sicherungsdaten für jede geschützte Ressource, z.B. Azure-VMs, gespeichert werden. Wenn der Sicherungsauftrag für eine geschützte Ressource ausgeführt wird, wird im Recovery Services-Tresor ein Wiederherstellungspunkt erstellt. Sie können einen dieser Wiederherstellungspunkte dann verwenden, um Daten für einen bestimmten Zeitpunkt wiederherzustellen.

Erstellen Sie einen Recovery Services-Tresor mit [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Geben Sie dieselbe Ressourcengruppe und denselben Standort wie für die zu schützende VM an. Wenn Sie den [VM-Schnellstart](../virtual-machines/linux/quick-create-cli.md) verwendet haben, haben Sie Folgendes erstellt:

- Eine Ressourcengruppe namens *myResourceGroup*.
- Einen virtuellen Computer namens *myVM*.
- Ressourcen am Standort *eastus*.

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Für den Recovery Services-Tresor ist standardmäßig die georedundante Speicherung festgelegt. Georedundanter Speicher sorgt dafür, dass Ihre Sicherungsdaten in einer sekundären Azure-Region repliziert werden, die Hunderte von Kilometern von der primären Region entfernt ist. Verwenden Sie das Cmdlet [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set), um die Einstellungen für die Speicherredundanz zu ändern.

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>Aktivieren der Sicherung für eine Azure-VM

Erstellen Sie eine Schutzrichtlinie, um zu definieren, wann ein Sicherungsauftrag ausgeführt werden soll und wie lange die Wiederherstellungspunkte gespeichert werden sollen. Bei der Standardschutzrichtlinie wird jeden Tag ein Sicherungsauftrag ausgeführt, und Wiederherstellungspunkte werden 30 Tage lang beibehalten. Sie können diese Standardrichtlinienwerte verwenden, um Ihre VM schnell zu schützen. Verwenden Sie [az backup protection enable-for-vm](/cli/azure/backup/protection#az-backup-protection-enable-for-vm), um den Sicherungsschutz für einen virtuellen Computer zu aktivieren. Geben Sie die Ressourcengruppe und die zu schützende VM und anschließend die Richtlinie an, die verwendet werden soll:

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Wenn sich der virtuelle Computer nicht in der gleichen Ressourcengruppe befindet wie der Tresor, verweist „myResourceGroup“ auf die Ressourcengruppe, in der der Tresor erstellt wurde. Geben Sie anstelle des VM-Namens die VM-ID an, wie hier zu sehen:

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> Wenn Sie die Sicherung mithilfe der CLI für mehrere virtuelle Computer gleichzeitig aktivieren, dürfen pro Richtlinie maximal 100 virtuelle Computer zugeordnet sein. Dies wird als [bewährte Methode](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy) empfohlen. Zurzeit blockiert der PowerShell-Client die Aktivierung bei mehr als 100 virtuellen Computern nicht explizit. Diese Überprüfung soll aber in Zukunft hinzugefügt werden.

## <a name="start-a-backup-job"></a>Starten eines Sicherungsauftrags

Verwenden Sie [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now), um sofort einen Sicherungsvorgang zu starten, anstatt zu warten, bis die Standardrichtlinie den Auftrag zum geplanten Zeitpunkt ausführt. Bei diesem ersten Sicherungsauftrag wird ein vollständiger Wiederherstellungspunkt erstellt. Bei jedem Sicherungsauftrag nach diesem ersten Sicherungsvorgang werden dann inkrementelle Wiederherstellungspunkte erstellt. Inkrementelle Wiederherstellungspunkte sind in Bezug auf die Speicherung und die Dauer effizient, da nur Änderungen übertragen werden, die seit der letzten Sicherung vorgenommen wurden.

Die folgenden Parameter werden verwendet, um die VM zu sichern:

- `--container-name` ist der Name Ihrer VM.
- `--item-name` ist der Name Ihrer VM.
- Der Wert `--retain-until` sollte auf das letzte mögliche Datum festgelegt werden (im UTC-Zeitformat **tt-mm-jjjj**), bis zu dem der Wiederherstellungspunkt verfügbar sein soll.

Im folgenden Beispiel wird die VM mit dem Namen *myVM* gesichert und der Ablauf des Wiederherstellungspunkts auf den 18. Oktober 2017 festgelegt:

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>Überwachen des Sicherungsauftrags

Verwenden Sie [az backup job list](/cli/azure/backup/job#az-backup-job-list), um den Status von Sicherungsaufträgen zu überwachen:

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Die Ausgabe ähnelt dem folgenden Beispiel, bei dem für den Sicherungsauftrag der Status *InProgress* angezeigt wird:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Wenn der *Status* des Sicherungsauftrags als *Completed* gemeldet wird, ist Ihre VM durch Recovery Services geschützt und verfügt über einen gespeicherten vollständigen Wiederherstellungspunkt.

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Wenn die Komponenten nicht mehr benötigt werden, können Sie den Schutz auf der VM deaktivieren, die Wiederherstellungspunkte und den Recovery Services-Tresor entfernen und anschließend die Ressourcengruppe und die dazugehörigen VM-Ressourcen löschen. Falls Sie eine vorhandene VM verwendet haben, können Sie den abschließenden Befehl [az group delete](/cli/azure/group#az-group-delete) überspringen, damit die Ressourcengruppe und die VM erhalten bleiben.

Unter [Nächste Schritte](#next-steps) finden Sie ein Sicherungstutorial, in dem die Wiederherstellung von Daten für Ihren virtuellen Computer erläutert wird.

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Recovery Services-Tresor erstellt, den Schutz für einen virtuellen Computer aktiviert und den ersten Wiederherstellungspunkt erstellt. Weitere Informationen zu Azure Backup und Recovery Services sind in den Tutorials enthalten.

> [!div class="nextstepaction"]
> [Sichern von mehreren Azure-VMs](./tutorial-backup-vm-at-scale.md)
