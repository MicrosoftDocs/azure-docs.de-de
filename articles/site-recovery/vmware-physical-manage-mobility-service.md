---
title: Verwalten des Mobilitäts-Agents auf Servern für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern mit Azure Site Recovery | Microsoft-Dokumentation
description: Verwalten Sie den Mobilitäts-Agent, um mit dem Azure Site Recovery-Dienst eine Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure auszuführen.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66117324"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Verwalten des Mobilitäts-Agents auf geschützten Computern

Sie richten einen Mobilitäts-Agent auf Ihrem Server ein, wenn Sie Azure Site Recovery für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure verwenden. Der Mobilitäts-Agent koodiniert die Kommunikation zwischen dem geschützten Computer und dem Konfigurationsserver/Prozessserver für horizontales Hochskalieren und verwaltet die Datenreplikation. In diesem Artikel werden häufige Aufgaben zur Verwaltung des Mobilitäts-Agents nach dessen Bereitstellung zusammengefasst.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aktualisieren des Mobilitätsdiensts über das Azure-Portal

1. Stellen Sie vor dem Start sicher, dass der Konfigurationsserver, der Prozessserver für horizontales Skalieren und alle Masterzielserver, die Teil der Bereitstellung sind, aktualisiert werden, bevor Sie mit der Aktualisierung von Mobility Service auf den geschützten Computern beginnen.
2. Öffnen Sie im Portal den Tresor > **Replizierte Elemente**.
3. Wenn der Konfigurationsserver bereits die neueste Version verwendet, wird eine Benachrichtigung angezeigt, dass ein neues Update für den Site Recovery-Replikations-Agent verfügbar ist. Sie können es per Klick installieren.

     ![Fenster „Replizierte Elemente“](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klicken Sie auf die Benachrichtigung, und wählen Sie in **Agent-Update** den Computer, auf dem Sie den Mobilitätsdienst aktualisieren möchten. Klicken Sie dann auf **OK**.

     ![VM-Liste mit replizierten Elementen](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Der Aktualisierungsauftrag für Mobility Service wird für jeden ausgewählten Computer gestartet.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aktualisieren des Mobilitätsdiensts über ein PowerShell-Skript unter Windows Server

Verwenden Sie das folgende Skript zum Aktualisieren des Mobilitätsdiensts auf einem Server über das PowerShell-Cmdlet:

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aktualisieren des für die Pushinstallation des Mobilitätsdiensts verwendeten Kontos

Wenn Sie Site Recovery eingesetzt haben, um die Pushinstallation des Mobilitätsdiensts zu aktivieren, haben Sie ein Konto angegeben, über das der Site Recovery-Prozessserver auf die Computer zugreift und den Dienst installiert, wenn die Replikation für den Computer aktiviert ist. Wenn Sie die Anmeldeinformationen für dieses Konto aktualisieren möchten, führen Sie [diese Anweisungen](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) aus.

## <a name="uninstall-mobility-service"></a>Deinstallieren des Mobilitätsdiensts

### <a name="on-a-windows-machine"></a>Auf einem Windows-Computer

Die Deinstallation erfolgt über die Benutzeroberfläche oder eine Eingabeaufforderung.

- **Über die Benutzeroberfläche**: Wählen Sie in der Systemsteuerung des Computers **Programme** aus. Wählen Sie **Microsoft Azure Site Recovery Mobility Service/Masterzielserver** > **Deinstallieren**.
- **Über eine Eingabeaufforderung**: Öffnen Sie als Administrator ein Eingabeaufforderungsfenster auf dem Computer. Führen Sie den folgenden Befehl aus: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Auf einem Linux-Computer
1. Melden Sie sich auf dem Linux-Computer als ein **root**-Benutzer an.
2. Navigieren Sie in einem Terminal zu „/user/local/ASR“.
3. Führen Sie den folgenden Befehl aus:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
