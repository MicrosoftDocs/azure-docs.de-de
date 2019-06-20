---
title: 'Mandanten- und Hostpoolerstellung in Windows Virtual Desktop: Azure'
description: Beheben von Problemen bei der Konfiguration eines Mandanten und eines virtuellen Sitzungshostcomputers (VM) in einer Windows Virtual Desktop-Umgebung.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64928771"
---
# <a name="tenant-and-host-pool-creation"></a>Mandanten- und Hostpoolerstellung

Verwenden Sie diesen Artikel, um Probleme zu behandeln, die bei der Konfiguration von virtuellen Computern (VMs) der Windows Virtual Desktop-Sitzung auftreten.

## <a name="provide-feedback"></a>Feedback geben

Solange sich Windows Virtual Desktop in der Vorschauphase befindet, werden noch keine Supportanfragen angenommen. In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuelle Computer werden der Domäne nicht hinzugefügt

Befolgen Sie diese Anweisungen, wenn Probleme beim Hinzufügen virtueller Computer zur Domäne auftreten.

- Fügen Sie die VM manuell über den unter [Hinzufügen eines virtuellen Windows Server-Computers zu einer verwalteten Domäne](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) beschriebenen Prozess hinzu, oder verwenden Sie die [Vorlage für den Domänenbeitritt](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Versuchen Sie, den Domänennamen über die Befehlszeile auf dem virtuellen Computer zu pingen.
- Überprüfen Sie die Liste der Fehlermeldungen zum Domänenbeitritt unter [Fehlermeldungen bei der Problembehandlung von Domänenbeitritten](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Fehler Falsche Anmeldeinformationen

**Ursache:** Es gab einen Schreibfehler, als die Anmeldeinformationen in die Schnittstellenkorrekturen der Azure Resource Manager-Vorlage eingegeben wurden.

**Behebung:** Befolgen Sie diese Anweisungen, um die Anmeldeinformationen zu korrigieren.

1. Fügen Sie die virtuellen Computer einer Domäne manuell hinzu.
2. Führen Sie die Bereitstellung erneut aus, nachdem die Anmeldeinformationen bestätigt wurden. Siehe [Erstellen eines Hostpools mit PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Fügen Sie einer Domäne VMs mithilfe einer Vorlage zum [Hinzufügen eines vorhandenen virtuellen Windows-Computers zu einer AD-Domäne](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/) hinzu.

### <a name="error-timeout-waiting-for-user-input"></a>Fehler Timeout beim Warten auf eine Benutzereingabe.

**Ursache:** Das Konto, dar zum Abschließen des Domänenbeitritts verwendet wird, nutzt möglicherweise Multi-Factor Authentication (MFA).

**Behebung:** Befolgen Sie diese Anweisungen, um den Domänenbeitritt abzuschließen.

1. Entfernen Sie vorübergehend MFA für das Konto.
2. Verwenden Sie ein Dienstkonto.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Fehler Das während der Bereitstellung verwendete Konto ist nicht berechtigt, den Vorgang abzuschließen.

**Ursache:** Das verwendete Konto besitzt aufgrund von Compliance und Vorschriften keine Berechtigungen, der Domäne VMs hinzuzufügen.

**Behebung:** Befolgen Sie diese Anweisungen.

1. Verwenden Sie ein Konto, das Mitglied der Gruppe „Administratoren“ ist.
2. Erteilen Sie dem verwendeten Konto die erforderlichen Berechtigungen.

### <a name="error-domain-name-doesnt-resolve"></a>Fehler Der Domänenname wird nicht aufgelöst.

**Ursache 1:** VMs befinden sich in einer Ressourcengruppe, die nicht dem virtuellen Netzwerk (VNET) zugeordnet ist, in dem sich die Domäne befindet.

**Behebung 1:** Erstellen Sie VNET-Peering zwischen dem VNET, in dem VMs bereitgestellt wurden, und dem VNET, in dem der Domänencontroller (DC) ausgeführt wird. Siehe [Erstellen eines Peerings in virtuellen Netzwerken: Resource Manager, verschiedene Abonnements](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Ursache 2:** Beim Verwenden von AadService (AADS) wurden keine DNS-Einträge festgelegt.

**Behebung 2:** Informationen zum Festlegen von Domänendiensten finden Sie unter [Aktivieren von Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Der Windows Virtual Desktop-Agent und das Windows Virtual Desktop-Startladeprogramm sind nicht installiert.

Die empfohlene Methode zur Bereitstellung von VMs ist die Verwendung der Azure Resource Manager-Vorlage **Erstellen und Bereitstellen von Windows Virtual Desktop-Hostpools**. Die Vorlage installiert automatisch den Windows Virtual Desktop-Agent und das Windows Virtual Desktop-Agent-Startladeprogramm.

Befolgen Sie diese Anweisungen, um die Installation der Komponenten zu bestätigen und nach Fehlermeldungen zu suchen.

1. Vergewissern Sie sich, dass die beiden Komponenten installiert sind, indem Sie **Systemsteuerung** > **Programme** > **Programme und Funktionen** auswählen. Wenn **Windows Virtual Desktop-Agent** und das **Windows Virtual Desktop-Agent-Startladeprogramm** nicht angezeigt werden, sind sie auf der VM nicht installiert.
2. Öffnen Sie den **Datei-Explorer**, und navigieren Sie zu **C:\Windows\Temp\scriptlogs.log**. Wenn die Datei nicht vorhanden ist, zeigt dies an, dass die PowerShell-DSC, die die beiden Komponenten installiert hat, nicht in der Lage war, im bereitgestellten Sicherheitskontext ausgeführt zu werden.
3. Wenn die Datei **C:\Windows\Temp\scriptlogs.log** vorhanden ist, öffnen Sie sie, und suchen Sie nach Fehlermeldungen.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Fehler Der Windows Virtual Desktop-Agent und das Windows Virtual Desktop-Agent--Startladeprogramm sind nicht vorhanden. „C:\Windows\Temp\scriptlogs.log“ fehlt ebenfalls.

**Ursache 1:** Die bei der Eingabe für die Azure Resource Manager-Vorlage angegebenen Anmeldeinformationen waren falsch, oder die Berechtigungen waren unzureichend.

**Behebung 1:** Fügen Sie den VMs die fehlenden Komponenten manuell mithilfe von [Erstellen eines Hostpools mit PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) hinzu.

**Ursache 2:** Die PowerShell-DSC konnte gestartet und ausgeführt, aber nicht abgeschlossen werden, da sie sich nicht bei Windows Virtual Desktop anmelden und die erforderlichen Informationen abrufen kann.

**Behebung 2:** Bestätigen Sie die einzelnen Punkte in der folgenden Liste.

- Stellen Sie sicher, dass das Konto keine MFA verwendet.
- Bestätigen Sie, dass der Mandantenname richtig und der Mandant in Windows Virtual Desktop vorhanden ist.
- Bestätigen Sie, dass das Konto mindestens über die Berechtigung „RDS-Mitwirkender“ verfügt.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Fehler Authentifizierungsfehler, Fehler in „C:\Windows\Temp\scriptlogs.log“.

**Ursache:** Die PowerShell-DSC konnte ausgeführt werden, aber keine Verbindung mit Windows Virtual Desktop herstellen.

**Behebung:** Bestätigen Sie die einzelnen Punkte in der folgenden Liste.

- Registrieren Sie die virtuellen Computer manuell beim Windows Virtual Desktop-Dienst.
- Bestätigen Sie, dass das Konto, das für die Verbindung mit Windows Virtual Desktop verwendet wird, auf dem Mandanten über die Berechtigung verfügt, Hostpools zu erstellen.
- Bestätigen Sie, dass das Konto keine MFA verwendet.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Der Windows Virtual Desktop-Agent ist nicht beim Windows Virtual Desktop-Dienst registriert.

Wenn der Windows Virtual Desktop-Agent zum ersten Mal auf Sitzungshost-VMs installiert wird (entweder manuell oder über die Azure Resource Manager-Vorlage und PowerShell-DSC), stellt er ein Registrierungstoken bereit. Der folgende Abschnitt beschreibt die Behandlung von Problemen, die für den Windows Virtual Desktop-Agent und das Token gelten.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Fehler Der Status im Cmdlet Get-RdsSessionHost wird als „Nicht verfügbar“ angegeben.

![Das Cmdlet Get-RdsSessionHost zeigt den Status als „Nicht verfügbar“ an.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Ursache:** Der Agent kann sich nicht selbst auf eine neue Version aktualisieren.

**Behebung:** Führen Sie diese Anweisungen manuell aus, um den Agent zu aktualisieren.

1. Laden Sie eine neue Version des Agents auf die Sitzungshost-VM herunter.
2. Starten Sie den Task-Manager, und beenden Sie auf der Registerkarte „Dienst“ den RDAgentBootLoader-Dienst.
3. Führen Sie den Installer für die neue Version des Windows Virtual Desktop-Agents aus.
4. Wenn Sie zur Eingabe des Registrierungstokens aufgefordert werden, entfernen Sie den Eintrag INVALID_TOKEN, und klicken Sie auf „Weiter“ (ein neues Token ist nicht erforderlich).
5. Führen Sie den Installations-Assistenten aus.
6. Öffnen Sie den Task-Manager, und starten Sie den RDAgentBootLoader-Dienst.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Fehler  Der Registrierungseintrag IsRegistered des Windows Virtual Desktop-Agents zeigt einen Wert von 0 an.

**Ursache:** Das Registrierungstoken ist abgelaufen oder wurde mit einem Ablaufwert von 999999 generiert.

**Behebung:** Befolgen Sie diese Anweisungen, um den Fehler in der Agent-Registrierung zu beheben.

1. Wenn bereits ein Registrierungstoken vorhanden ist, entfernen Sie es mit Remove-RDSRegistrationInfo.
2. Generieren Sie ein neues Token mit Rds-NewRegistrationInfo.
3. Bestätigen Sie, dass der Parameter -ExpriationHours auf 72 festgelegt ist (der Maximalwert ist 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Fehler Der Windows Virtual Desktop-Agent meldet bei der Ausführung von Get-RdsSessionHost keinen Heartbeat.

**Ursache 1:** Der RDAgentBootLoader-Dienst wurde beendet.

**Behebung 1:** Starten Sie den Task-Manager, und starten Sie den Dienst, wenn die Registerkarte „Dienst“ einen beendeten Status für den RDAgentBootLoader-Dienst meldet.

**Ursache 2:** Port 443 ist ggf. geschlossen.

**Behebung 2:** Befolgen Sie diese Anweisungen, um Port 443 zu öffnen.

1. Bestätigen Sie, dass Port 443 geöffnet ist, indem Sie das PSPing-Tool von [Sysinternal Tools](https://docs.microsoft.com/sysinternals/downloads/psping) herunterladen.
2. Installieren Sie PSPing auf der Sitzungshost-VM, auf der der Agent ausgeführt wird.
3. Öffnen Sie die Eingabeaufforderung als Administrator, und geben Sie den unten gezeigten Befehl aus:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Bestätigen Sie, das PSPing Informationen vom RDBroker zurückerhalten hat:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Problembehandlung mit dem parallelen Stapel für Windows Virtual Desktop

Der parallele Stapel für Windows Virtual Desktop wird automatisch mit Windows Server 2019 installiert. Verwenden Sie Microsoft Installer (MSI), um den parallelen Stapel unter Microsoft Windows Server 2016 oder Windows Server 2012 R2 zu installieren. Für Microsoft Windows 10 wird der parallele Stapel für Windows Virtual Desktop mit **enablesxstackrs.ps1** aktiviert.

Es gibt drei Hauptmethoden, wie der parallele Stapel auf Sitzungshostpool-VMs installiert oder aktiviert wird:

- Mit der Azure Resource Manager-Vorlage **Erstellen und Bereitstellen von Windows Virtual Desktop-Hostpools**.
- Durch Einbinden in das und Aktivieren im Masterimage.
- Durch manuelles Installieren oder Aktivieren auf jeder VM (oder mit Erweiterungen/PowerShell).

Wenn Sie Probleme mit dem parallelen Stapel von Windows Virtual Desktop haben, geben Sie den Befehl **qwinsta** an der Eingabeaufforderung ein, um zu bestätigen, dass der parallele Stapel installiert oder aktiviert ist.

Die Ausgabe von **qwinsta** listet **rdp-sxs** in der Ausgabe auf, wenn der parallele Stapel installiert und aktiviert ist.

![Paralleler Stapel installiert oder aktiviert, qwinsta-Ausgabe mit rdp-sxs.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Untersuchen Sie die unten aufgeführten Registrierungseinträge und bestätigen Sie, dass ihre Werte übereinstimmen. Wenn Registrierungsschlüssel fehlen oder Werte nicht übereinstimmen, befolgen Sie die Anweisungen unter [Erstellen eines Hostpools mit PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) zum erneuten Installieren des parallelen Stapels.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Fehler O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE-Fehlercode.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Ursache:** Der parallele Stapel ist nicht auf der Sitzungshost-VM installiert.

**Behebung:** Befolgen Sie diese Anweisungen, um den parallelen Stapel auf der Sitzungshost-VM zu installieren.

1. Verwenden Sie RDP (Remote Desktop Protocol), um als lokaler Administrator direkt in die Sitzungshost-VM zu gelangen.
2. Laden Sie das [Windows Virtual Desktop-PowerShell-Modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) herunter, und importieren Sie es, um es in Ihrer PowerShell-Sitzung verwenden zu können.
3. Installieren Sie den parallelen Stapel mithilfe von [Erstellen eines Hostpools mit PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Beheben der Fehlfunktion eines parallelen Stapels für Windows Virtual Desktop

Es sind Umstände bekannt, die zu einer Fehlfunktion des parallelen Stapels führen können:

- Nichtbefolgen der richtigen Reihenfolge der Schritte zum Aktivieren des parallelen Stapels
- Automatisches Update auf Windows 10 Enhanced Versatile Disc (EVD)
- Fehlende RDSH-Rolle (Remotedesktop-Sitzungshost)
- Mehrmaliges Ausführen von Enablesxsstackrc.ps1
- Ausführen von Enablesxsstackrc.ps1 in einem Konto, das nicht über lokale Administratorrechte verfügt

Die Anweisungen in diesem Abschnitt können Ihnen helfen, den parallelen Stapel für Windows Virtual Desktop zu deinstallieren. Sobald Sie den parallelen Stapel deinstalliert haben, navigieren Sie zu „Registrieren der VM beim Windows Virtual Desktop-Hostpool“ in [Erstellen eines Hostpools mit PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell), um den parallelen Stapel erneut zu installieren.

Die VM, mit der die Wiederherstellung durchgeführt wird, muss sich im gleichen Subnetz und in derselben Domäne befinden wie die VM mit dem fehlerhaften parallelen Stapel.

Befolgen Sie diese Anweisungen, um die Fehlerbehebung aus demselben Subnetz und derselben Domäne durchzuführen:

1. Stellen Sie eine Verbindung mit der VM mit dem RDP-Standardprotokoll (Remote Desktop Protocol) her, von der aus die Fehlerkorrektur angewendet wird.
2. Laden Sie PsExec von https://docs.microsoft.com/sysinternals/downloads/psexec herunter.
3. Extrahieren Sie die heruntergeladene Datei.
4. Starten Sie eine Eingabeaufforderung als lokaler Administrator.
5. Navigieren Sie zu dem Ordner, in den PsExec extrahiert wurde.
6. Verwenden Sie in der Eingabeaufforderung den folgenden Befehl:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname ist der Computername des virtuellen Computers mit dem fehlerhaften parallelen Stapel.

7. Stimmen Sie dem PsExec-Lizenzvertrag zu, indem Sie auf „Agree“ (Ich stimme zu) klicken.

    ![Screenshot: Softwarelizenzvereinbarung.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Dieses Dialogfeld wird nur bei der ersten Ausführung von PsExec angezeigt.

8. Nachdem die Eingabeaufforderungssitzung auf der VM mit dem fehlerhaften parallelen Stapel geöffnet wurde, führen Sie qwinsta aus und bestätigen, dass ein Eintrag namens rdp-sxs verfügbar ist. Wenn dies nicht der Fall ist, ist auf der VM kein paralleler Stapel vorhanden, sodass das Problem nicht durch den parallelen Stapel verursacht wird.

    ![Administratoreingabeaufforderung](media/AdministratorCommandPrompt.png)

9. Führen Sie den folgenden Befehl aus, der die auf der VM installierten Microsoft-Komponenten mit dem fehlerhaften parallelen Stapel auflistet.

    ```cmd
        wmic product get name
    ```

10. Führen Sie den folgenden Befehl mit Produktnamen aus dem vorherigen Schritt aus.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Deinstallieren Sie alle Produkte, die mit „Remotedesktop“ beginnen.

12. Nachdem alle Windows Virtual Desktop-Komponenten deinstalliert wurden, führen Sie die Anweisungen für Ihr Betriebssystem aus:

13. Wenn Ihr Betriebssystem Windows Server ist, starten Sie die VM neu, die den fehlerhaften parallelen Stapel aufwies (entweder mit dem Azure-Portal oder mit dem PsExec-Tool).

Wenn das Betriebssystem Microsoft Windows 10 ist, fahren Sie mit den folgenden Anweisungen fort:

14. Öffnen Sie auf der VM, die PsExec ausführt, den Datei-Explorer, und kopieren Sie „disablesxsstackrc.ps1“ auf das Systemlaufwerk der VM mit dem fehlerhaften parallelen Stapel.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname ist der Computername des virtuellen Computers mit dem fehlerhaften parallelen Stapel.

15. Empfohlene Vorgehensweise: Starten Sie PowerShell aus dem PsExec-Tool, und navigieren Sie zum Ordner aus dem vorherigen Schritt. Führen Sie dann „disablesxsstackrc.ps1“ aus. Alternativ können Sie die folgenden Cmdlets ausführen:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Wenn die Ausführung der Cmdlets abgeschlossen ist, starten Sie die VM mit dem fehlerhaften parallelen Stapel neu.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung beim Erstellen eines Mandanten- und Hostpools in einer Windows Virtual Desktop-Umgebung finden Sie unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung bei Problemen mit Windows Virtual Desktop-Clientverbindungen finden Sie unter [Remotedesktop-Clientverbindungen](troubleshoot-client-connection.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop: PowerShell](troubleshoot-powershell.md).
- Weitere Informationen zum Vorschaudienst finden Sie unter [Umgebung der Windows Desktop-Vorschau](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).