---
title: 'Tutorial: SAP HANA-DB-Sicherung in Azure per Azure CLI'
description: In diesem Tutorial wird beschrieben, wie Sie SAP HANA-Datenbanken, die auf einem virtuellen Azure-Computer ausgeführt werden, über die Azure CLI in einem Azure Backup Recovery Services-Tresor sichern.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41869ac4dfc0d3964fb48463e5f142b8391e2d94
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579251"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Sichern von SAP HANA-Datenbanken auf einem virtuellen Azure-Computer über die Azure CLI

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Dokumentation wird beschrieben, wie Sie eine SAP HANA-Datenbank sichern und bedarfsgesteuerte Sicherungen auslösen – alles über die Azure CLI (Azure-Befehlszeilenschnittstelle). Sie können diese Schritte auch über das [Azure-Portal](./backup-azure-sap-hana-database.md) ausführen.

In diesem Dokument wird davon ausgegangen, dass Sie auf einem virtuellen Azure-Computer bereits eine SAP HANA-Datenbank installiert haben. (Sie können auch [per Azure CLI eine VM erstellen](../virtual-machines/linux/quick-create-cli.md).) In diesem Tutorial wird Folgendes vermittelt:

> [!div class="checklist"]
>
> * Erstellen eines Recovery Services-Tresors
> * Registrieren einer SAP HANA-Instanz und Ermitteln von zugehörigen Datenbanken
> * Aktivieren der Sicherung für eine SAP HANA-Datenbank
> * Auslösen einer bedarfsgesteuerten Sicherung

Informieren Sie sich über die [Szenarien, die von uns für SAP HANA derzeit unterstützt werden](./sap-hana-backup-support-matrix.md#scenario-support).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.30 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Ein Recovery Services-Tresor ist ein logischer Container, in dem die Sicherungsdaten für jede geschützte Ressource gespeichert werden, z. B. virtuelle Azure-Computer oder Workloads, die auf Azure-VMs ausgeführt werden (wie SQL- oder HANA-Datenbanken). Wenn der Sicherungsauftrag für eine geschützte Ressource ausgeführt wird, wird im Recovery Services-Tresor ein Wiederherstellungspunkt erstellt. Sie können einen dieser Wiederherstellungspunkte dann verwenden, um Daten für einen bestimmten Zeitpunkt wiederherzustellen.

Erstellen Sie einen Recovery Services-Tresor mit [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Geben Sie dieselbe Ressourcengruppe und denselben Standort wie für die zu schützende VM an. Informieren Sie sich in dieser [VM-Schnellstartanleitung](../virtual-machines/linux/quick-create-cli.md), wie Sie über die Azure CLI eine VM erstellen.

In diesem Tutorial wird Folgendes verwendet:

* eine Ressourcengruppe mit dem Namen *saphanaResourceGroup*
* eine VM mit dem Namen *saphanaVM*
* Ressourcen in der Region *westus2*

Wir erstellen einen Tresor mit dem Namen *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Für den Recovery Services-Tresor ist standardmäßig die georedundante Speicherung festgelegt. Georedundanter Speicher sorgt dafür, dass Ihre Sicherungsdaten in einer sekundären Azure-Region repliziert werden, die Hunderte von Kilometern von der primären Region entfernt ist. Verwenden Sie das Cmdlet [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set), um bei Bedarf die Einstellungen für die Speicherredundanz zu ändern.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Verwenden Sie das Cmdlet [az backup vault list](/cli/azure/backup/vault#az-backup-vault-list), um zu ermitteln, ob die Erstellung Ihres Tresors erfolgreich war. Sie erhalten die folgende Antwort:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrieren und Schützen der SAP HANA-Instanz

Damit die SAP HANA-Instanz (die VM mit der SAP HANA-Installation) von den Azure-Diensten ermittelt werden kann, muss auf dem SAP HANA-Computer ein [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) ausgeführt werden. Stellen Sie sicher, dass alle [Voraussetzungen](./tutorial-backup-sap-hana-db.md#prerequisites) erfüllt sind, bevor Sie das Skript ausführen. Weitere Informationen zu den Funktionen des Skripts finden Sie im Abschnitt [Einrichten von Berechtigungen](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

Nachdem das Skript ausgeführt wurde, kann die SAP HANA-Instanz für den Recovery Services-Tresor registriert werden, den Sie zuvor erstellt haben. Verwenden Sie das Cmdlet [az backup container register](/cli/azure/backup/container#az-backup-container-register), um die Instanz zu registrieren. *VMResourceId* ist die Ressourcen-ID der VM, die Sie für die Installation von SAP HANA erstellt haben.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Wenn sich die VM nicht in derselben Ressourcengruppe wie der Tresor befindet, verweist *saphanaResourceGroup* auf die Ressourcengruppe, in der der Tresor erstellt wurde.

Beim Registrieren der SAP HANA-Instanz werden automatisch alle Datenbanken ermittelt, die dafür derzeit vorhanden sind. Informationen zur Ermittlung von neuen Datenbanken, die in Zukunft ggf. hinzugefügt werden, finden Sie im Abschnitt [Schützen von neuen Datenbanken, die einer SAP HANA-Instanz hinzugefügt wurden](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance).

Verwenden Sie das Cmdlet [az backup container list](/cli/azure/backup/container#az-backup-container-list), um zu überprüfen, ob die Registrierung der SAP HANA-Instanz für Ihren Tresor erfolgreich war. Sie erhalten die folgende Antwort:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Die Spalte „name“ in der obigen Ausgabe bezieht sich auf den Containernamen. Dieser Containername wird in den nächsten Abschnitten verwendet, um Sicherungen zu aktivieren und auszulösen. In diesem Fall ist dies *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Aktivieren der Sicherung für eine SAP HANA-Datenbank

Mit dem Cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) werden alle Datenbanken der SAP HANA-Instanz aufgelistet, die Sie im vorherigen Schritt registriert haben.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Die Datenbank, die Sie sichern möchten, sollte in der folgenden Liste enthalten sein:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Wie Sie in der obigen Ausgabe erkennen können, lautet die SID des SAP HANA-Systems „HXE“. In diesem Tutorial konfigurieren wir die Sicherung für die Datenbank *saphanadatabase;hxe;hxe*, die sich auf dem Server *hxehost* befindet.

Wir verwenden das Cmdlet [az backup protection enable-for-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl), um die Sicherungen für eine Datenbank einzeln zu schützen und zu konfigurieren. Geben Sie den Namen der Richtlinie an, die Sie verwenden möchten. Verwenden Sie das Cmdlet [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create), um per CLI eine Richtlinie zu erstellen. In diesem Tutorial verwenden wir die Richtlinie *sapahanaPolicy*.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Mit dem Cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list) können Sie überprüfen, ob die obige Sicherungskonfiguration vollständig ist. Die Ausgabe wird wie folgt angezeigt:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Mit dem Cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list) werden alle Sicherungsaufträge aufgelistet (geplant oder bedarfsgesteuert), die auf der geschützten Datenbank bereits ausgeführt wurden bzw. derzeit ausgeführt werden. Darüber hinaus sind auch andere Vorgänge aufgeführt, z. B. Durchführen der Registrierung, Konfigurieren einer Sicherung, Löschen von Sicherungsdaten.

>[!NOTE]
>Azure Backup passt Änderungen an der Sommer- oder Winterzeit bei der Sicherung einer auf einem virtuellen Azure-Computer ausgeführten SAP HANA-Datenbank nicht automatisch an.
>
>Ändern Sie die Richtlinie nach Bedarf manuell.

## <a name="trigger-an-on-demand-backup"></a>Auslösen einer bedarfsgesteuerten Sicherung

Nachdem im obigen Abschnitt beschrieben wurde, wie Sie eine geplante Sicherung konfigurieren, geht es in diesem Abschnitt nun um das Auslösen einer bedarfsgesteuerten Sicherung. Hierfür verwenden wir das Cmdlet [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now).

>[!NOTE]
> Die Aufbewahrungsrichtlinie einer bedarfsgesteuerten Sicherung wird durch die zugrunde liegende Aufbewahrungsrichtlinie für die Datenbank bestimmt.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

Die Ausgabe wird wie folgt angezeigt:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

In der Antwort ist der Auftragsname enthalten. Dieser Auftragsname kann zum Nachverfolgen des Auftragsstatus mit dem Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show) verwendet werden.

>[!NOTE]
>Es kann nicht nur eine vollständige oder differenzielle Sicherung geplant werden, sondern derzeit ist auch eine manuelle Auslösung möglich. Protokollsicherungen werden automatisch ausgelöst und von SAP HANA intern verwaltet.
>
> Inkrementelle Sicherungen werden von Azure Backup derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Wiederherstellen einer SAP HANA-Datenbank auf einem virtuellen Azure-Computer über die CLI finden Sie im Tutorial [Wiederherstellen von SAP HANA-Datenbanken auf einer Azure-VM über die Azure CLI](tutorial-sap-hana-restore-cli.md).

* Informationen zum Sichern einer auf einer Azure-VM ausgeführten SAP HANA-Datenbank über das Azure-Portal finden Sie unter [Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern](./backup-azure-sap-hana-database.md).
