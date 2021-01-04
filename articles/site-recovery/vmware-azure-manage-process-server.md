---
title: Verwalten eines Prozessservers für die Notfallwiederherstellung von virtuellen VMware-Computern/physischen Servern in Azure Site Recovery
description: In diesem Artikel wird beschrieben, wie Sie einen Prozessserver für die Notfallwiederherstellung von VMware-VMs/physischen Servern mit Azure Site Recovery verwalten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019186"
---
# <a name="manage-process-servers"></a>Verwalten von Prozessservern

In diesem Artikel werden allgemeine Aufgaben zur Verwaltung des Site Recovery-Prozessservers beschrieben.

Der Prozessserver dient zum Empfangen, Optimieren und Senden von Replikationsdaten an Azure. Außerdem führt er eine Pushinstallation des Mobility Service auf VMware-VMs und physischen Servern, die Sie replizieren möchten, sowie die automatische Ermittlung von lokalen Computern durch. Für die Replikation von lokalen VMware-VMs oder physischen Servern nach Azure wird der Prozessserver standardmäßig auf dem Computer des Konfigurationsservers installiert. 

- Bei umfangreichen Bereitstellungen benötigen Sie möglicherweise zusätzliche lokale Prozessserver zur Skalierung der Kapazität.
- Für das Failback von Azure auf eine lokale Instanz müssen Sie einen temporären Prozessserver in Azure einrichten. Sie können diese VM nach Abschluss des Failbacks löschen. 

Weitere Informationen zum Prozessserver.


## <a name="upgrade-a-process-server"></a>Durchführen eines Upgrades für einen Prozessserver

Wenn Sie einen Prozessserver lokal oder als eine Azure-VM für das Failback bereitstellen, wird die neueste Version des Prozessservers installiert. Die Site Recovery-Teams veröffentlichen in regelmäßigen Abständen Fehlerbehebungen und Verbesserungen, und es wird empfohlen, Prozessserver aktuell zu halten. Sie können ein Upgrade für einen Prozessserver folgendermaßen durchführen:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Verschieben von VMs für einen Lastausgleich auf Prozessservern

Nehmen Sie einen Lastausgleich vor, indem Sie VMs zwischen zwei Prozessservern folgendermaßen verschieben:

1. Klicken Sie im Tresor unter **Verwalten** auf **Site Recovery-Infrastruktur**. Klicken Sie unter **Für VMware und physische Computer** auf **Konfigurationsserver**.
2. Klicken Sie auf den Konfigurationsserver, bei dem die Prozessserver registriert sind.
3. Klicken Sie auf den Prozessserver, bei dem Sie einen Lastausgleich für Datenverkehr vornehmen möchten.

    ![Screenshot: Prozessserver, für den Sie einen Lastausgleich für Datenverkehr vornehmen können](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klicken Sie auf **Lastausgleich**, und wählen Sie den Zielprozessserver aus, auf den Sie Computer verschieben möchten. Klicken Sie dann auf **OK**.

    ![Screenshot: Bereich „Lastenausgleich“ mit ausgewählter Option „Zielprozessserver auswählen“](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klicken Sie auf **Computer auswählen**, und wählen Sie die Computer aus, die Sie vom aktuellen Prozessserver auf den Zielprozessserver verschieben möchten. Für jeden virtuellen Computer werden Details zur durchschnittlichen Datenänderung angezeigt. Klicken Sie dann auf **OK**. 
3. Überwachen Sie im Tresor den Fortschritt des Auftrags unter **Überwachung** > **Site Recovery-Aufträge**.

Es dauert ungefähr 15 Minuten, bis Änderungen im Portal angezeigt werden. Wenn dies schneller geschehen soll, [aktualisieren Sie den Konfigurationsserver](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Wechseln einer ganzen Workload auf einen anderen Prozessserver

Verschieben Sie die gesamte von einem Prozessserver verarbeitete Workload folgendermaßen auf einen anderen Prozessserver:

1. Klicken Sie im Tresor unter **Verwalten** auf **Site Recovery-Infrastruktur**. Klicken Sie unter **Für VMware und physische Computer** auf **Konfigurationsserver**.
2. Klicken Sie auf den Konfigurationsserver, bei dem die Prozessserver registriert sind.
3. Klicken Sie auf den Prozessserver, von dem Sie die Workload wechseln möchten.
4. Klicken Sie auf **Switch**, und wählen Sie den Zielprozessserver aus, auf den Sie die Workload verschieben möchten. Klicken Sie dann auf **OK**.

    ![Screenshot: Bereich „Zielprozessserver auswählen“](media/vmware-azure-manage-process-server/Switch.PNG)

5. Überwachen Sie im Tresor den Fortschritt des Auftrags unter **Überwachung** > **Site Recovery-Aufträge**.

Es dauert ungefähr 15 Minuten, bis Änderungen im Portal angezeigt werden. Wenn dies schneller geschehen soll, [aktualisieren Sie den Konfigurationsserver](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Registrieren eines Masterzielservers

Der Masterzielserver befindet sich auf dem Konfigurationsserver und Prozessservern für die horizontale Skalierung. Er muss beim Konfigurationsserver registriert sein. Eine fehlerhafte Registrierung kann sich auf die Integrität der geschützten Elemente auswirken. Um den Masterzielserver beim Konfigurationsserver zu registrieren, melden Sie sich beim jeweiligen Konfigurationserver/Prozessserver für die horizontale Skalierung an, auf dem die Registrierung erforderlich ist. Navigieren Sie zum Ordner **%PROGRAMDATA%\ASR\Agent**, und führen Sie Folgendes an einer Administratoreingabeaufforderung aus.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Erneutes Registrieren eines Prozessservers

Registrieren Sie einen Prozessserver, der lokal oder auf einer Azure-VM ausgeführt wird, folgendermaßen erneut beim Konfigurationsserver:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Nachdem Sie die Einstellungen gespeichert haben, führen Sie folgende Schritte durch:

1. Öffnen Sie als Administrator eine Eingabeaufforderung auf dem Prozessserver.
2. Navigieren Sie zum Ordner **%PROGRAMDATA%\ASR\Agent**, und führen Sie den folgenden Befehl aus:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Ändern von Proxyeinstellungen für einen lokalen Prozessserver

Wenn ein lokaler Prozessserver die Verbindung mit Azure über einen Proxy herstellt, können Sie die Proxyeinstellungen folgendermaßen ändern:

1. Melden Sie sich beim Computer des Prozessservers an. 
2. Öffnen Sie als Administrator ein PowerShell-Eingabefenster, und führen Sie den folgenden Befehl aus:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Navigieren Sie zum Ordner **%PROGRAMDATA%\ASR\Agent**, und führen Sie diesen Befehl aus:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Entfernen eines Prozessservers

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Ausschließen von Ordnern aus Virenschutzsoftware

Wenn Virenschutzsoftware auf einem Prozessserver mit horizontaler Skalierung (oder einem Masterzielserver) ausgeführt wird, schließen Sie die folgenden Ordner von Virenschutzvorgängen aus:


- C:\Programme\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Installationsverzeichnis des Prozessservers. Beispiel: C:\Programme (x86)\Microsoft Azure Site Recovery