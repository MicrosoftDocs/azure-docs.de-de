---
title: Startdiagnose für virtuelle Computer in Azure | Microsoft-Dokumentation
description: Übersicht über die beiden Debugfeatures für virtuelle Computer in Azure
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 59602977c1b7f6dd0524c6535d8458d3eb1a3f26
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505876"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Es wird beschrieben, wie Sie die Startdiagnose zum Beheben von Problemen mit virtuellen Computern in Azure verwenden.

Es gibt viele mögliche Gründe dafür, dass ein virtueller Computer in einen nicht startfähigen Zustand wechselt. Um Probleme mit virtuellen Computern, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden, zu behandeln, können Sie die folgenden Debugfunktionen verwenden: Konsolenausgaben- und Screenshotunterstützung für virtuelle Azure-Computer. 

Für virtuelle Linux-Computer können Sie die Ausgabe des Konsolenprotokolls über das Portal anzeigen. Azure ermöglicht Ihnen für virtuelle Windows- und Linux-Computer das Anzeigen eines Screenshots des virtuellen Computers im Hypervisor. Beide Funktionen werden für virtuelle Azure-Computer in allen Regionen unterstützt. Hinweis: Es kann bis zu 10 Minuten dauern, bis Screenshots und Ausgaben in Ihrem Speicherkonto angezeigt werden.

Sie können die Option **Startdiagnose** aktivieren, um das Protokoll und den Screenshot anzuzeigen.

![Ressourcen-Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Häufige Startfehler

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Betriebssystem nicht gefunden](https://support.microsoft.com/help/4010142)
- [Startfehler oder INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Aktivieren der Diagnose auf einem virtuellen Computer, der über das Azure-Portal erstellt wurde

Das folgende Verfahren gilt für mit dem Resource Manager-Bereitstellungsmodell erstellte virtuelle Computer.

Stellen Sie auf der Registerkarte **Verwaltung** im Bereich **Überwachung** sicher, dass **Startdiagnose** aktiviert ist. Wählen Sie in der Dropdownliste **Diagnosespeicherkonto** ein Speicherkonto aus, in dem die Diagnosedateien gespeichert werden sollen.
 
![Erstellen eines virtuellen Computers](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Das Feature „Startdiagnose“ unterstützt keine Storage Premium-Konten. Wenn Sie ein Premium-Speicherkontos für die Startdiagnose verwenden, erhalten Sie beim Starten der VM möglicherweise den Fehler „StorageAccountTypeNotSupported“.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Bereitstellen aus einer Azure Resource Manager-Vorlage

Beim Bereitstellen aus einer Azure Resource Manager-Vorlage navigieren Sie zur VM-Ressource und fügen den Diagnoseprofilabschnitt an. Legen Sie den API-Versionsheader mindestens auf „2015-06-15“ fest. Die neueste Version ist „2018-10-01“.

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Das Diagnoseprofil ermöglicht Ihnen die Auswahl des Speicherkontos, in dem diese Protokolle abgelegt werden sollen.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Weitere Informationen zum Bereitstellen von Ressourcen mithilfe von Vorlagen finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Aktivieren der Startdiagnose auf vorhandenen virtuellen Computern 

Zum Aktivieren von Startdiagnoseeinstellungen auf vorhandenen virtuellen Computern führen Sie diese Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann den virtuellen Computer aus.
2. Wählen Sie im Abschnitt **Support + Problembehandlung** die Option **Startdiagnose** und dann die Registerkarte **Einstellungen** aus.
3. Ändern Sie in den Einstellungen für **Startdiagnose** den Status in **Ein**, und wählen Sie in der Dropdownliste **Speicherkonto** ein Speicherkonto aus. 
4. Speichern Sie die Änderungen.

    ![Aktualisieren eines vorhandenen virtuellen Computers](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Sie müssen den virtuellen Computer neu starten, damit die Änderung wirksam wird.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Aktivieren der Startdiagnose über die Azure-Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle verwenden, um die Startdiagnose auf einem virtuellen Azure-Computer zu aktivieren. Weitere Informationen finden Sie unter [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
