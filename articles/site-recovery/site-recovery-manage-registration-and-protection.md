---
title: Entfernen von Servern und Deaktivieren des Schutzes | Microsoft Docs
description: In diesem Artikel wird erläutert, wie Sie die Registrierung von Servern im Site Recovery-Tresor aufheben und den Schutz für virtuelle Computer und physische Server deaktivieren.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 1b4cd5bb020e73dc9045eb164ce49931f818f72d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65415480"
---
# <a name="remove-servers-and-disable-protection"></a>Entfernen von Servern und Deaktivieren des Schutzes

In diesem Artikel erfahren Sie, wie Sie die Registrierung von Servern in einem Recovery Services-Tresor aufheben und wie Sie den Schutz für mit Site Recovery geschützte Computer deaktivieren.


## <a name="unregister-a--configuration-server"></a>Aufheben der Registrierung eines Konfigurationsservers

Wenn Sie VMware-VMs oder physische Windows-/Linux-Server in Azure replizieren, können Sie die Registrierung des nicht verbundenen Konfigurationsservers bei einem Tresor wie folgt aufheben:

1. [Deaktivieren des Schutzes für virtuelle Computer](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)
2. [Aufheben der Zuordnung oder Löschen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) von Replikationsrichtlinien
3. [Löschen des Konfigurationsservers](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Aufheben der Registrierung eines VMM-Servers

1. Beenden Sie die Replikation virtueller Computer in Clouds auf dem VMM-Server, den Sie entfernen möchten.
2. Löschen Sie Netzwerkzuordnungen, die von Clouds auf dem VMM-Server verwendet werden, den Sie löschen möchten. Klicken Sie unter **Site Recovery-Infrastruktur** > **Für System Center VMM** > **Netzwerkzuordnung** mit der rechten Maustaste auf die Netzwerkzuordnung, und klicken Sie dann auf **Löschen**.
3. Notieren Sie die ID des VMM-Servers.
4. Heben Sie die Zuordnung zwischen Replikationsrichtlinien und Clouds auf dem VMM-Server auf, den Sie entfernen möchten.  Doppelklicken Sie unter **Site Recovery-Infrastruktur** > **Für System Center VMM** >  **Replikationsrichtlinien** auf die zugehörige Richtlinie. Klicken Sie mit der rechten Maustaste auf die Cloud, und klicken Sie dann auf **Zuordnung aufheben**.
5. Löschen Sie den VMM-Server oder den aktiven Knoten. Klicken Sie unter **Site Recovery-Infrastruktur** > **Für System Center VMM** > **VMM-Server** mit der rechten Maustaste auf den Server, und klicken Sie dann auf **Löschen**.
6. Wenn Ihr VMM-Server nicht verbunden war, laden Sie das [Bereinigungsskript](https://aka.ms/asr-cleanup-script-vmm) auf den VMM-Server herunter und führen es aus. Öffnen Sie PowerShell mit der Option **Als Administrator ausführen**, um die Ausführungsrichtlinie für den Standardbereich (LocalMachine) zu ändern. Geben Sie im Skript die ID des VMM-Servers an, den Sie entfernen möchten. Das Skript entfernt die Informationen zu Registrierung und Cloudkopplung vom Server.
5. Führen Sie das Bereinigungsskript auf einem beliebigen sekundären VMM-Server aus.
6. Führen Sie das Bereinigungsskript auf allen anderen passiven VMM-Clusterknoten aus, auf denen der Anbieter installiert ist.
7. Deinstallieren Sie manuell den Anbieter auf dem VMM-Server. Wenn Sie einen Cluster verwenden, entfernen Sie ihn von allen Knoten.
8. Wenn Sie die Replikation Ihrer virtuellen Computer nach Azure durchführen, müssen Sie den Microsoft Recovery Services-Agent auf den Hyper-V-Hosts in den gelöschten Clouds deinstallieren.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Aufheben der Registrierung eines Hyper-V-Hosts an einem Hyper-V-Standort

Hyper-V-Hosts, die nicht von VMM verwaltet werden, werden an einem Hyper-V-Standort gesammelt. Entfernen Sie einen Host an einem Hyper-V-Standort wie folgt:

1. Deaktivieren Sie die Replikation für Hyper-V-VMs, die sich auf dem Host befinden.
2. Heben Sie die Zuordnung von Richtlinien für den Hyper-V-Standort auf. Doppelklicken Sie unter **Site Recovery-Infrastruktur** > **Für Hyper-V-Standorte** >  **Replikationsrichtlinien** auf die zugehörige Richtlinie. Klicken Sie mit der rechten Maustaste auf den Standort, und klicken Sie dann auf **Zuordnung aufheben**.
3. Löschen Sie Hyper-V-Hosts. Klicken Sie unter **Site Recovery-Infrastruktur** > **Für Hyper-V-Websites** > **Hyper-V-Hosts** mit der rechten Maustaste auf den Server, und klicken Sie dann auf **Löschen**.
4. Löschen Sie den Hyper-V-Standort, nachdem alle Hosts daraus entfernt wurden. Klicken Sie unter **Site Recovery-Infrastruktur** > **Für Hyper-V-Websites** > **Hyper-V-Websites** mit der rechten Maustaste auf die Website, und klicken Sie dann auf **Löschen**.
5. Wenn Ihr Hyper-V-Host den Status **Verbindung getrennt** aufweist, führen Sie das folgende Skript auf jedem Hyper-V-Host aus, den Sie entfernt haben. Das Skript bereinigt die Einstellungen auf dem Server und hebt die Registrierung beim Tresor auf.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Deaktivieren des Schutzes für eine VMware-VM oder einen physischen Server (VMware nach Azure)

1. Klicken Sie unter **Geschützte Elemente** > **Replizierte Elemente** mit der rechten Maustaste auf den Computer, und klicken Sie dann auf **Replikation deaktivieren**.
2. Wählen Sie auf der Seite **Replikation deaktivieren** eine der folgenden Optionen aus:
    - **Replikation deaktivieren und entfernen (empfohlen)** – diese Option entfernt das replizierte Element aus Azure Site Recovery. Außerdem wird die Replikation für den Computer beendet. Die Konfiguration der Replikation auf dem Konfigurationsserver wird bereinigt, und die Abrechnung für Site Recovery für diesen geschützten Server wird beendet. Beachten Sie, dass diese Option nur verwendet werden kann, wenn der Konfigurationsserver verbunden ist.
    - **Entfernen** – diese Option sollte nur verwendet werden, wenn die Quellumgebung gelöscht wurde oder darauf nicht zugegriffen werden kann (keine Verbindung). Dadurch wird das replizierte Element aus Azure Site Recovery entfernt (und die Abrechnung wird beendet). Die Konfiguration der Replikation auf dem Konfigurationsserver wird **nicht** bereinigt. 

> [!NOTE]
> Bei beiden Optionen wird der Mobility Service nicht von den geschützten Servern deinstalliert. Sie müssen die Deinstallation manuell vornehmen. Wenn Sie den Server erneut mit demselben Konfigurationsserver schützen möchten, können Sie die Deinstallation des Mobility Service überspringen.

## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Deaktivieren des Schutzes für eine Azure-VM (Azure zu Azure)

-  Klicken Sie unter **Geschützte Elemente** > **Replizierte Elemente** mit der rechten Maustaste auf den Computer, und klicken Sie dann auf **Replikation deaktivieren**.
> [!NOTE]
> Der Mobility Service wird nicht von den geschützten Servern deinstalliert, Sie müssen ihn manuell deinstallieren. Wenn Sie planen, den Server erneut zu schützen, können Sie die Deinstallation des Mobility Service überspringen.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Deaktivieren des Schutzes für einen virtuellen Hyper-V-Computer (Hyper-V nach Azure)

> [!NOTE]
> Gehen Sie folgendermaßen vor, wenn Sie Hyper-V-VMs ohne einen VMM-Server nach Azure replizieren. Wenn Sie Ihre virtuellen Computer von **System Center VMM nach Azure** replizieren, befolgen Sie die Anweisungen unter „Deaktivieren des Schutzes für einen virtuellen Hyper-V-Computer mit Replikation mithilfe von System Center VMM nach Azure“.

1. Klicken Sie unter **Geschützte Elemente** > **Replizierte Elemente** mit der rechten Maustaste auf den Computer, und klicken Sie dann auf **Replikation deaktivieren**.
2. Unter **Replikation deaktivieren** können Sie folgende Optionen auswählen:
   - **Replikation deaktivieren und entfernen (empfohlen)** – diese Option entfernt das replizierte Element aus Azure Site Recovery. Außerdem wird die Replikation für den Computer beendet. Die Konfiguration der Replikation auf dem lokalen virtuellen Computer wird bereinigt, und die Abrechnung für Site Recovery für diesen geschützten Server wird beendet.
   - **Entfernen** – diese Option sollte nur verwendet werden, wenn die Quellumgebung gelöscht wurde oder darauf nicht zugegriffen werden kann (keine Verbindung). Dadurch wird das replizierte Element aus Azure Site Recovery entfernt (und die Abrechnung wird beendet). Die Konfiguration der Replikation auf dem lokalen virtuellen Computer wird **nicht** bereinigt. 

     > [!NOTE]
     > Wenn Sie die Option **Entfernen** ausgewählt haben, führen Sie die folgenden Skripts aus, um die Replikationseinstellungen für den lokalen Hyper-V-Server zu bereinigen.
1. Entfernen Sie auf dem Hyper-V-Quellhostserver die Replikation für den virtuellen Computer. Ersetzen Sie SQLVM1 durch den Namen des virtuellen Computers, und führen Sie das Skript in einer administrativen PowerShell aus.

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Deaktivieren des Schutzes für einen virtuellen Hyper-V-Computer mit Replikation mithilfe von System Center VMM nach Azure

1. Klicken Sie unter **Geschützte Elemente** > **Replizierte Elemente** mit der rechten Maustaste auf den Computer, und klicken Sie dann auf **Replikation deaktivieren**.
2. Wählen Sie unter **Replikation deaktivieren** eine der folgenden Optionen aus:

   - **Replikation deaktivieren und entfernen (empfohlen)** – diese Option entfernt das replizierte Element aus Azure Site Recovery. Außerdem wird die Replikation für den Computer beendet. Die Konfiguration der Replikation auf dem lokalen virtuellen Computer wird bereinigt, und die Abrechnung für Site Recovery für diesen geschützten Server wird beendet.
   - **Entfernen** – diese Option sollte nur verwendet werden, wenn die Quellumgebung gelöscht wurde oder darauf nicht zugegriffen werden kann (keine Verbindung). Dadurch wird das replizierte Element aus Azure Site Recovery entfernt (und die Abrechnung wird beendet). Die Konfiguration der Replikation auf dem lokalen virtuellen Computer wird **nicht** bereinigt. 

     > [!NOTE]
     > Wenn Sie die Option **Entfernen** ausgewählt haben, führen Sie die folgenden Skripts aus, um die Replikationseinstellungen für den lokalen VMM-Server zu bereinigen.
3. Führen Sie dieses Skript auf dem VMM-Quellserver mithilfe von PowerShell (Administratorrechte erforderlich) an der VMM-Konsole aus. Ersetzen Sie den Platzhalter **SQLVM1** durch den Namen des virtuellen Computers.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Mit den obigen Schritten werden die Replikationseinstellungen auf dem VMM-Server gelöscht. Um die Replikation für den virtuellen Computer zu beenden, der auf dem Hyper-V-Hostserver ausgeführt wird, führen Sie dieses Skript aus. Ersetzen Sie „SQLVM1“ durch den Namen Ihres virtuellen Computers und „host01.contoso.com“ durch den Namen des Hyper-V-Hostservers.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Deaktivieren des Schutzes für einen virtuellen Hyper-V-Computer mit Replikation auf einen sekundären VMM-Server mithilfe von System Center VMM nach Azure

1. Klicken Sie unter **Geschützte Elemente** > **Replizierte Elemente** mit der rechten Maustaste auf den Computer, und klicken Sie dann auf **Replikation deaktivieren**.
2. Wählen Sie unter **Replikation deaktivieren** eine der folgenden Optionen aus:

   - **Replikation deaktivieren und entfernen (empfohlen)** – diese Option entfernt das replizierte Element aus Azure Site Recovery. Außerdem wird die Replikation für den Computer beendet. Die Konfiguration der Replikation auf dem lokalen virtuellen Computer wird bereinigt, und die Abrechnung für Site Recovery für diesen geschützten Server wird beendet.
   - **Entfernen** – diese Option sollte nur verwendet werden, wenn die Quellumgebung gelöscht wurde oder darauf nicht zugegriffen werden kann (keine Verbindung). Dadurch wird das replizierte Element aus Azure Site Recovery entfernt (und die Abrechnung wird beendet). Die Konfiguration der Replikation auf dem lokalen virtuellen Computer wird **nicht** bereinigt. Führen Sie die folgenden Skripts aus, um die Replikationseinstellungen für einen lokalen virtuellen Computer zu bereinigen.
     > [!NOTE]
     > Wenn Sie die Option **Entfernen** ausgewählt haben, führen Sie die folgenden Skripts aus, um die Replikationseinstellungen für den lokalen VMM-Server zu bereinigen.

3. Führen Sie dieses Skript auf dem VMM-Quellserver mithilfe von PowerShell (Administratorrechte erforderlich) an der VMM-Konsole aus. Ersetzen Sie den Platzhalter **SQLVM1** durch den Namen des virtuellen Computers.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Führen Sie auf dem sekundären VMM-Server das folgende Skript aus, um die Einstellungen für den sekundären virtuellen Computer zu bereinigen:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Aktualisieren Sie auf dem sekundären VMM-Server die virtuellen Computer auf dem Hyper-V-Hostserver, damit der sekundäre virtuelle Computer in der VMM-Konsole neu erkannt wird.
6. Mit den obigen Schritten werden die Replikationseinstellungen auf dem VMM-Server gelöscht. Wenn Sie die Replikation für den virtuelle Computer beenden möchten, führen Sie das folgende Skript auf dem primären und sekundären virtuellen Computer aus. Ersetzen Sie „SQLVM1“ durch den Namen des virtuellen Computers.

        Remove-VMReplication –VMName “SQLVM1”




