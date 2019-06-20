---
title: Zurücksetzen eines lokalen Windows-Kennworts ohne Azure-Agent | Microsoft Docs
description: 'Gewusst wie: Zurücksetzen des Kennworts eines lokalen Windows-Benutzerkontos, wenn der Azure-Gast-Agent auf einem virtuellen Computer nicht installiert ist oder nicht ausgeführt wird.'
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 3c0152726aba115e1b370838308a7bf0af08cab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708132"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Zurücksetzen eines lokalen Windows-Kennworts im Offlinemodus für einen virtuellen Azure-Computer
Sie können das lokale Windows-Kennwort eines virtuellen Computers in Azure im [Azure-Portal oder mithilfe von Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zurücksetzen, sofern der Azure-Gast-Agent installiert ist. Diese Methode ist die einfachste Möglichkeit zum Zurücksetzen eines Kennworts für einen virtuellen Azure-Computer. Wenn der Azure-Gast-Agent nicht reagiert oder nach dem Hochladen eines benutzerdefinierten Images nicht installiert wird, können Sie ein Windows-Kennwort manuell zurücksetzen. In diesem Artikel wird erläutert, wie das Kennwort eines lokalen Kontos durch Anfügen des virtuellen Quellbetriebssystem-Datenträgers an einen anderen virtuellen Computer zurückgesetzt wird. Die in diesem Artikel beschriebenen Schritte gelten nicht für Windows-Domänencontroller. 

> [!WARNING]
> Führen Sie diesen Vorgang nur als letzte Möglichkeit durch. Versuchen Sie immer zuerst, das Kennwort im [Azure-Portal oder mithilfe von Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zurückzusetzen.
> 
> 

## <a name="overview-of-the-process"></a>Übersicht über den Vorgang
Wenn kein Zugriff auf den Azure-Gast-Agent besteht, sehen die wichtigsten Schritte zum Zurücksetzen eines lokalen Kennworts für einen virtuellen Windows-Computer in Azure wie folgt aus:

* Löschen Sie den virtuellen Quellcomputer. Die virtuellen Datenträger werden beibehalten.
* Fügen Sie den Betriebssystemdatenträger des virtuellen Quellcomputers in demselben Verzeichnis innerhalb Ihres Azure-Abonnements an. Dieser virtuelle Computer wird als virtueller Problembehandlungscomputer bezeichnet.
* Erstellen Sie mit dem virtuellen Problembehandlungscomputer einige Konfigurationsdateien auf dem Betriebssystemdatenträger des virtuellen Quellcomputers.
* Trennen Sie den Betriebssystemdatenträger des virtuellen Quellcomputers vom virtuellen Problembehandlungscomputer.
* Erstellen Sie mithilfe einer Resource Manager-Vorlage und unter Verwendung des ursprünglichen virtuellen Datenträgers einen virtuellen Computer.
* Wenn der neue virtuelle Computer gestartet wird, wird das Kennwort des entsprechenden Benutzers über die erstellten Konfigurationsdateien aktualisiert.

> [!NOTE]
> Sie können die folgenden Prozesse automatisieren:
>
> - Erstellen einer Problembehebungs-VM
> - Anfügen des Betriebssystemdatenträgers
> - Erneutes Erstellen der ursprüngliche VM
> 
> Verwenden Sie hierzu die [Azure VM-Wiederherstellungsskripts](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Wenn Sie die Azure VM-Wiederherstellungsskripts verwenden möchten, können Sie den folgenden Prozess im Abschnitt „Detaillierte Schritte“ verwenden:
> 1. Überspringen Sie die Schritte 1 und 2, indem Sie mit Hilfe der Skripte den Betriebssystemdatenträger der betroffenen VM an eine Wiederherstellungs-VM anfügen.
> 2. Führen Sie die Schritte 3 bis 6 aus, um die Risikominderung anzuwenden.
> 3. Überspringen Sie die Schritte 7 bis 9 mithilfe der Skripts, um die VM neu zu erstellen.
> 4. Führen Sie die Schritte 10 und 11 aus.

## <a name="detailed-steps"></a>Ausführliche Schritte

> [!NOTE]
> Die Schritte gelten nicht für Windows-Domänencontroller. Sie können nur für einen eigenständigen Server oder einen Server ausgeführt werden, der Mitglied einer Domäne ist.
> 
> 

Versuchen Sie zunächst immer, ein Kennwort im [Azure-Portal oder mithilfe von Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zurückzusetzen, bevor Sie die folgenden Schritte ausführen. Vergewissern Sie sich zunächst, dass Sie über eine Sicherung des virtuellen Computers verfügen. 

1. Löschen Sie den betroffenen virtuellen Computer im Azure-Portal. Durch das Löschen des virtuellen Computers werden lediglich die Metadaten, d.h. der Verweis des virtuellen Computers in Azure, gelöscht. Die virtuellen Datenträger werden beim Löschen des virtuellen Computers beibehalten.
   
   * Wählen Sie den virtuellen Computer im Azure-Portal aus, und klicken Sie auf *Löschen*:
     
     ![Löschen eines vorhandenen virtuellen Computers](./media/reset-local-password-without-agent/delete_vm.png)
2. Fügen Sie den Betriebssystemdatenträger des virtuellen Quellcomputers an den virtuellen Problembehandlungscomputer an. Der virtuelle Problembehandlungscomputer muss sich in der gleichen Region wie der Betriebssystemdatenträger des virtuellen Quellcomputers befinden (z.B. `West US`):
   
   * Wählen Sie den virtuellen Problembehandlungscomputer im Azure-Portal aus. Klicken Sie auf *Datenträger* | *Vorhandenen anfügen*:
     
     ![Anfügen eines vorhandenen Datenträgers](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Wählen Sie *VHD-Datei* und dann das Speicherkonto aus, das den virtuellen Quellcomputer enthält:
     
     ![Auswählen des Speicherkontos](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Wählen Sie den Quellcontainer aus. Normalerweise ist *vhds* der Quellcontainer:
     
     ![Auswählen des Speichercontainers](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Wählen Sie die anzufügende Betriebssystem-VHD aus. Klicken Sie auf *Auswählen*, um den Vorgang abzuschließen:
     
     ![Auswählen des virtuellen Quelldatenträgers](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Stellen Sie per Remotedesktop eine Verbindung mit dem virtuellen Problembehandlungscomputer her, und prüfen Sie, ob der Betriebssystemdatenträger des virtuellen Quellcomputers angezeigt wird:
   
   * Wählen Sie den virtuellen Problembehandlungscomputer im Azure-Portal aus, und klicken Sie auf *Verbinden*.
   * Öffnen Sie die heruntergeladene RDP-Datei. Geben Sie den Benutzernamen und das Kennwort des virtuellen Problembehandlungscomputers ein.
   * Suchen Sie im Datei-Explorer den angefügten Datenträger. Wenn die VHD des virtuellen Quellcomputers als einziger Datenträger an den virtuellen Problembehandlungscomputer angefügt ist, sollte es sich um Laufwerk F: handeln:
     
     ![Anzeigen des angefügten Datenträgers](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Erstellen Sie `gpt.ini` in `\Windows\System32\GroupPolicy` auf dem Laufwerk des virtuellen Quellcomputers (wenn die Datei „gpt.ini“ vorhanden ist, benennen Sie sie in „gpt.ini.bak“ um):
   
   > [!WARNING]
   > Stellen Sie sicher, dass Sie die folgenden Dateien nicht versehentlich in „C:\Windows“, dem Betriebssystemlaufwerk für den virtuellen Problembehandlungscomputer, erstellen. Erstellen Sie die folgenden Dateien im Betriebssystemlaufwerk für den virtuellen Quellcomputer, der als Datenträger angefügt ist.
   > 
   > 
   
   * Fügen Sie in der erstellten Datei `gpt.ini` die folgenden Zeilen ein:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Erstellen von „gpt.ini“](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Erstellen Sie `scripts.ini` in `\Windows\System32\GroupPolicy\Machines\Scripts\`. Stellen Sie sicher, dass ausgeblendete Ordner angezeigt werden. Erstellen Sie gegebenenfalls den Ordner `Machine` oder `Scripts`.
   
   * Fügen Sie in der erstellten Datei `scripts.ini` die folgenden Zeilen ein:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Erstellen von „scripts.ini“](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Erstellen Sie `FixAzureVM.cmd` in `\Windows\System32` mit dem folgenden Inhalt, und ersetzen Sie dabei `<username>` und `<newpassword>` durch Ihre eigenen Werte:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Erstellen von „FixAzureVM.cmd“](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Beim Definieren des neuen Kennworts für den virtuellen Computer müssen Sie die konfigurierten Komplexitätsanforderungen für das Kennwort erfüllen.
7. Trennen Sie im Azure-Portal den Datenträger vom virtuellen Problembehandlungscomputer:
   
   * Wählen Sie den virtuellen Problembehandlungscomputer im Azure-Portal aus, und klicken Sie auf *Datenträger*.
   * Wählen Sie den in Schritt 2 angefügten Datenträger aus, und klicken Sie auf *Trennen*:
     
     ![Trennen des Datenträgers](./media/reset-local-password-without-agent/detach_disk.png)
8. Vor dem Erstellen eines virtuellen Computers benötigen Sie den URI für den Betriebssystemdatenträger des virtuellen Quellcomputers:
   
   * Wählen Sie das Speicherkonto im Azure-Portal aus, und klicken Sie auf *BLOBs*.
   * Wählen Sie den Container aus. Normalerweise ist *vhds* der Quellcontainer:
     
     ![Auswählen des Speicherkontoblobs](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Wählen Sie die Betriebssystem-VHD des virtuellen Quellcomputers aus, und klicken Sie auf die Schaltfläche *Kopieren* neben dem Namen der *URL*:
     
     ![Kopieren des Datenträger-URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Erstellen Sie einen virtuellen Computer über den Betriebssystemdatenträger des virtuellen Quellcomputers:
   
   * Verwenden Sie [diese Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet), um einen virtuellen Computer aus einer speziellen VHD zu erstellen. Klicken Sie auf die Schaltfläche `Deploy to Azure`, um das Azure-Portal mit den automatisch ausgefüllten Werten der Vorlage zu öffnen.
   * Wenn alle vorherigen Einstellungen für den virtuellen Computer beibehalten werden sollen, wählen Sie *Vorlage bearbeiten* aus, um das vorhandene VNET, das vorhandene Subnetz, die vorhandene Netzwerkkarte oder die vorhandene öffentliche IP-Adresse anzugeben.
   * Fügen Sie im Parametertextfeld `OSDISKVHDURI` den URI der Quell-VHD aus dem vorherigen Schritt ein:
     
     ![Erstellen eines virtuellen Computers über eine Vorlage](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Nachdem der neue virtuelle Computer ausgeführt wird, stellen Sie mit dem neuen im Skript `FixAzureVM.cmd` angegebenen Kennwort per Remotedesktop eine Verbindung mit dem virtuellen Computer her.
11. Entfernen Sie die folgenden Dateien aus der Remotesitzung mit dem neuen virtuellen Computer, um die Umgebung zu bereinigen:
    
    * In „%windir%\System32“
      * Entfernen von „FixAzureVM.cmd“
    * Aus %windir%\System32\GroupPolicy\Machine\Scripts
      * Entfernen von „scripts.ini“
    * In „%windir%\System32\GroupPolicy“
      * Entfernen von „gpt.ini“ (Wenn „gpt.ini“ bereits vorhanden war und Sie die Datei in „gpt.ini.bak“ umbenannt haben, benennen Sie die BAK-Datei wieder in „gpt.ini“ um.)

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie dennoch keine Verbindung per Remotedesktop herstellen können, finden Sie weitere Informationen in der [Anleitung zur Problembehandlung bei Remotedesktopverbindungen](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In der [ausführlichen Problembehandlung für Remotedesktopverbindungen](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) werden statt spezifischer Schritte allgemeine Problembehandlungsmethoden aufgezeigt. Sie können zudem [eine Azure-Supportanfrage stellen](https://azure.microsoft.com/support/options/), um praktische Unterstützung zu erhalten.

