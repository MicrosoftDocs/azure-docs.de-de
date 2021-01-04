---
title: VM-Momentaufnahmenerweiterung unter Windows für Azure Backup
description: Durchführen von anwendungskonsistenten Sicherungen des virtuellen Computers aus Azure Backup mit der VM-Momentaufnahmenerweiterung
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 10/15/2020
ms.author: trinadhk
ms.openlocfilehash: 1d1342cffc6bc8743c34cb4c028c0790de91dc50
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965866"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>VM-Momentaufnahmenerweiterung unter Windows für Azure Backup

Azure Backup bietet Unterstützung für die Sicherung von Workloads von einer lokalen Umgebung in der Cloud sowie die Sicherung von Cloudressourcen in einem Recovery Services-Tresor. Azure Backup führt mithilfe der VM-Momentaufnahmenerweiterung eine anwendungskonsistente Sicherung der virtuellen Azure-Computer durch, ohne dass die VM heruntergefahren werden muss. Die VM-Momentaufnahmenerweiterung wird von Microsoft im Rahmen des Azure Backup-Diensts veröffentlicht und unterstützt. Azure Backup installiert die Erweiterung als Teil der ersten geplanten Sicherung, die nach der Aktivierung der Sicherung ausgelöst wird. Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die VM-Momentaufnahmenerweiterung.

Die VMSnapshot-Erweiterung wird im Azure-Portal nur für nicht verwaltete VMs angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem
Eine Liste der unterstützten Betriebssysteme finden Sie unter [Von Azure Backup unterstützte Betriebssysteme](../../backup/backup-azure-arm-vms-prepare.md#before-you-start).

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die VM-Momentaufnahmenerweiterung. Für die Erweiterung sind folgende Angaben erforderlich: die Task-ID zur Identifizierung des Sicherungsauftrags, der die Momentaufnahme auf dem virtuellen Computer ausgelöst hat; der URI des Statusblobs, in den der Status des Momentaufnahmevorgangs geschrieben wird; die geplante Startzeit der Momentaufnahme; der URI des Protokollblobs, in den Protokolle entsprechend des Momentaufnahmetasks geschrieben werden; und „objstr“, die Darstellung der VM-Datenträger und Metadaten.  Da diese Einstellungen als vertrauliche Daten behandelt werden müssen, sollten sie in einer geschützten Einstellungskonfiguration gespeichert werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt. Beachten Sie, dass diese Einstellungen ausschließlich im Rahmen eines Sicherungsauftrags vom Azure Backup-Dienst übergeben werden sollten.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.Azure.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | Zeichenfolge |
| commandStartTimeUTCTicks | 6.36458E+17 | Zeichenfolge |
| locale | de-de | Zeichenfolge |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | Zeichenfolge |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | Zeichenfolge |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | Zeichenfolge |



## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Allerdings sollte eine VM-Momentaufnahmenerweiterung vorzugsweise durch Aktivieren der Sicherung auf dem virtuellen Computer zu einem virtuellen Computer hinzugefügt werden. Dies kann durch eine Resource Manager-Vorlage erreicht werden.  Eine Resource Manager-Beispielvorlage zur Durchführung einer Sicherung auf einem virtuellen Computer finden Sie im [Azure-Schnellstartkatalog](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

Über die Azure CLI kann eine Sicherung auf einem virtuellen Computer aktiviert werden. Nachdem die Sicherung aktiviert wurde, installiert der erste geplante Sicherungsauftrag die VM-Momentaufnahmenerweiterung auf der VM.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Fehlercodes und ihre Bedeutung

Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung bei der Sicherung virtueller Azure-Computer](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
