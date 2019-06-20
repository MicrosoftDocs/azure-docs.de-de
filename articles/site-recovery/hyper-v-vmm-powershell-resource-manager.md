---
title: Einrichten der Notfallwiederherstellung von Hyper-V-VMs in VMM-Clouds in einem sekundären Standort mit Azure Site Recovery und PowerShell | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie die Notfallwiederherstellung von Hyper-V-VMs, die sich in VMM-Clouds befinden, in einem sekundären VMM-Standort mithilfe von Azure Site Recovery und PowerShell einrichten.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 78bd077b5491b093510b9c55bf7b5a42ee9cb578
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362355"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Einrichten der Notfallwiederherstellung von Hyper-V-VMs in einen sekundären Standort mithilfe von PowerShell (Resource Manager)

In diesem Artikel wird beschrieben, wie Sie die Schritte für die Replikation von Hyper-V-VMs in System Center Virtual Machine Manager-Clouds in eine Virtual Machine Manager-Cloud an einem sekundären lokalen Standort mit [Azure Site Recovery](site-recovery-overview.md) automatisieren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Überprüfen Sie [Architektur und Komponenten des Szenarios](hyper-v-vmm-architecture.md).
- Überprüfen Sie die [Supportanforderungen](site-recovery-support-matrix-to-sec-site.md) für alle Komponenten.
- Stellen Sie sicher, dass die Virtual Machine Manager-Server und Hyper-V-Hosts den [Anforderungen für die Unterstützung](site-recovery-support-matrix-to-sec-site.md) entsprechen.
- Überprüfen Sie, ob die VMs, die Sie replizieren möchten, die Anforderungen für die [Unterstützung replizierter Computer](site-recovery-support-matrix-to-sec-site.md) erfüllen.


## <a name="prepare-for-network-mapping"></a>Bereiten Sie sich auf die Netzwerkzuordnung vor

Die [Netzwerkzuordnung](hyper-v-vmm-network-mapping.md) ist die Zuordnung zwischen lokalen Virtual Machine Manager-VM-Netzwerken in Quell- und Zielclouds. Die Zuordnung ermöglicht Folgendes:

- Nach dem Failover werden Verbindungen von VMs mit geeigneten Ziel-VM-Netzwerken hergestellt. 
- Replikat-VMs werden optimal auf Ziel-Hyper-V-Hostservern platziert. 
- Wenn Sie die Netzwerkzuordnung nicht konfigurieren, werden Replikat-VMs nach einem Failover nicht mit einem VM-Netzwerk verbunden.

Bereiten Sie Virtual Machine Manager wie folgt vor:

* Stellen Sie sicher, dass [logische Virtual Machine Manager-Netzwerke](https://docs.microsoft.com/system-center/vmm/network-logical) auf den Virtual Machine Manager-Quell- und -Zielservern vorhanden sind:

    - Das logische Netzwerk auf dem Quellserver sollte der Quellcloud zugeordnet sein, in der sich Hyper-V-Hosts befinden.
    - Das logische Netzwerk auf dem Zielserver sollte der Zielcloud zugeordnet werden.
* Stellen Sie sicher, dass [VM-Netzwerke](https://docs.microsoft.com/system-center/vmm/network-virtual) auf den Virtual Machine Manager-Quell- und -Zielservern vorhanden sind. VM-Netzwerke sollten an jedem Standort mit dem logischen Netzwerk verknüpft werden.
* Stellen Sie die Verbindung der virtuellen Computer auf den Hyper-V-Quellhosts mit dem VM-Quellnetzwerk her. 

## <a name="prepare-for-powershell"></a>Vorbereiten von PowerShell

Stellen Sie sicher, dass Azure PowerShell zur Verwendung bereit ist:

- Wenn Sie PowerShell bereits verwenden, aktualisieren Sie auf Version 0.8.10 oder höher. [Erfahren Sie mehr](/powershell/azureps-cmdlets-docs) über das Einrichten von PowerShell.
- Überprüfen Sie nach Abschluss der Einrichtung und Konfiguration von PowerShell die [Dienst-Cmdlets](/powershell/azure/overview).
- Weitere Informationen zur Verwendung der Parameterwerte, Eingaben und Ausgaben in PowerShell finden Sie in der Anleitung [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps) (Erste Schritte mit Azure PowerShell).

## <a name="set-up-a-subscription"></a>Einrichten eines Abonnements
1. Melden Sie sich in PowerShell bei Ihrem Azure-Konto an.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Rufen Sie über die Abonnement-IDs eine Liste Ihrer Abonnements ab. Notieren Sie die ID des Abonnements, in dem Sie den Recovery Services-Tresor erstellen möchten. 

        Get-AzSubscription
3. Legen Sie das Abonnement für den Tresor fest.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors
1. Erstellen Sie eine Azure Resource Manager-Ressourcengruppe, falls noch keine vorhanden ist.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Erstellen Sie einen neuen Recovery Services-Tresor. Speichern Sie das Tresorobjekt zu späteren Verwendung in einer Variablen. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Sie können das Tresorobjekt nach seiner Erstellung mit dem Cmdlet „Get-AzRecoveryServicesVault“ abrufen.

## <a name="set-the-vault-context"></a>Festlegen des Tresorkontexts
1. Rufen Sie einen vorhandenen Tresor ab.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Legen Sie den Tresorkontext fest.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Installieren des Site Recovery-Anbieters
1. Erstellen Sie auf dem Virtual Machine Manager-Computer ein Verzeichnis, indem Sie den folgenden Befehl ausführen:

       New-Item c:\ASR -type directory
2. Extrahieren Sie die Dateien mithilfe der heruntergeladenen Anbietersetupdatei.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installieren Sie den Anbieter, und warten Sie, bis die Installation abgeschlossen wurde.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Registrieren Sie den Server im Tresor.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Erstellen und Zuordnen einer Replikationsrichtlinie
1. Erstellen Sie wie folgt eine Replikationsrichtlinie (in diesem Fall für Hyper-V 2012 R2):

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Die Virtual Machine Manager-Cloud kann Hyper-V-Hosts enthalten, auf denen verschiedene Versionen von Windows Server ausgeführt werden, die Replikationsrichtlinie gilt jedoch für eine bestimmte Betriebssystemversion. Wenn Sie über verschiedene Hosts verfügen, die unter unterschiedlichen Betriebssystemversionen ausgeführt werden, erstellen Sie separate Replikationsrichtlinien für jedes System. Wenn Sie beispielsweise fünf Hosts unter Windows Server 2012 und drei Hosts unter Windows Server 2012 R2 haben, erstellen Sie zwei Replikationsrichtlinien. Für jeden Betriebssystemtyp erstellen Sie eine Richtlinie.

2. Rufen Sie den primären Schutzcontainer (primäre Virtual Machine Manager-Cloud) und den Schutzcontainer für die Wiederherstellung (Virtual Machine Manager-Cloud für die Wiederherstellung) ab.

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Rufen Sie die Replikationsrichtlinie, die Sie erstellt haben, mithilfe des Anzeigenamens ab.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Starten Sie die Zuordnung des Schutzcontainers (Virtual Machine Manager-Cloud) zur Replikationsrichtlinie.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Warten Sie, bis der Richtlinienzuordnungsauftrag abgeschlossen wurde. Mit dem folgenden PowerShell-Codeausschnitt können Sie überprüfen, ob der Auftrag abgeschlossen wurde:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Nachdem die Verarbeitung des Auftrags abgeschlossen wurde, führen Sie den folgenden Befehl aus:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Um den Abschluss des Vorgangs zu überprüfen, führen Sie die Schritte in [Überwachen der Aktivität](#monitor-activity) aus.

##  <a name="configure-network-mapping"></a>Konfigurieren der Netzwerkzuordnung
1. Verwenden Sie diesen Befehl, um Server für den aktuellen Tresor abzurufen. Der Befehl speichert die Site Recovery-Server in der $Servers-Arrayvariablen.

        $Servers = Get-AzSiteRecoveryServer
2. Führen Sie den folgenden Befehl aus, um die Netzwerke für den Virtual Machine Manager-Quellserver und den Virtual Machine Manager-Zielserver abzurufen.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Der Virtual Machine Manager-Quellserver kann der erste oder zweite Server im Serverarray sein. Überprüfen Sie die Namen der Virtual Machine Manager-Server, und rufen Sie die Netzwerke entsprechend ab.


3. Dieses Cmdlet erstellt eine Zuordnung zwischen dem primären Netzwerk und dem Wiederherstellungsnetzwerk. Es gibt das primäre Netzwerk als erstes Element von „$PrimaryNetworks“ an. Es gibt das Wiederherstellungsnetzwerk als erstes Element von „$RecoveryNetworks“ an.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Aktivieren des Schutzes für virtuelle Computer
Nach der korrekten Konfiguration der Server, Clouds und Netzwerke aktivieren Sie den Schutz für VMs in der Cloud.

1. Führen Sie den folgenden Befehl aus, um den Schutzcontainer abzurufen und den Schutz zu aktivieren:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Rufen Sie die Schutzentität (VM) wie folgt ab:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Aktivieren Sie die Replikation für die VM.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

Führen Sie zum Testen Ihrer Bereitstellung ein Testfailover für eine einzelne VM aus. Sie können auch einen Wiederherstellungsplan erstellen, der mehrere VMs enthält, und ein Testfailover für den Plan ausführen. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk.

1. Rufen Sie die VM ab, zu der das Failover für die VMs ausgeführt wird.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Führen Sie ein Testfailover aus.

   Für einen einzelnen virtuellen Computer

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Für einen Wiederherstellungsplan

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Um den Abschluss des Vorgangs zu überprüfen, führen Sie die Schritte in [Überwachen der Aktivität](#monitor-activity) aus.

## <a name="run-planned-and-unplanned-failovers"></a>Ausführen geplanter und ungeplanter Failover

1. Führen Sie ein geplantes Failover aus.

   Für einen einzelnen virtuellen Computer

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Für einen Wiederherstellungsplan

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Führen Sie ein ungeplantes Failover aus.

   Für einen einzelnen virtuellen Computer
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Für einen Wiederherstellungsplan

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Überwachen der Aktivität
Verwenden Sie die folgenden Befehle zum Überwachen der Failoveraktivität. Warten Sie zwischen Aufträgen, bis die Verarbeitung abgeschlossen wurde.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](/powershell/module/az.recoveryservices) über Site Recovery mit PowerShell-Cmdlets für Resource Manager.
