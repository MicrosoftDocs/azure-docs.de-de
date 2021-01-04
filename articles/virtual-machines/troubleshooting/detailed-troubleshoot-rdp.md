---
title: Ausführliche Problembehandlung für Remotedesktops in Azure | Microsoft Docs
description: Hier finden Sie detaillierte Informationen zur Behandlung von Remotedesktopfehlern, bei denen keine Remotedesktopverbindung mit virtuellen Windows-Computern in Azure hergestellt werden kann.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: keine Verbindung mit Remotedesktop, Problembehandlung für Remotedesktop, Remotedesktop kann keine Verbindung herstellen, Remotedesktopfehler, Remotedesktop Problembehandlung, Probleme mit Remotedesktop
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 91f15e32866cca008553286f7585247909d9a4ba
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002679"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detaillierte Problembehandlung beim Herstellen einer Remotedesktopverbindung mit virtuellen Windows-Computern in Azure
Dieser Artikel enthält ausführliche schrittweise Anleitungen zur Diagnose und Behebung komplexer Remotedesktop-Fehler für virtuelle windowsbasierte Azure-Computer.

> [!IMPORTANT]
> Um allgemeine Remotedesktopfehler als Ursache auszuschließen, lesen Sie zunächst den Artikel zur [grundlegenden Problembehandlung für Remotedesktop](troubleshoot-rdp-connection.md), bevor Sie mit dieser Problembehandlung fortfahren.

Es kann vorkommen, dass Sie eine Remotedesktop-Fehlermeldung erhalten, die keiner der spezifischen Fehlermeldungen ähnelt, die im Artikel zur [grundlegenden Problembehandlung für Remotedesktop](troubleshoot-rdp-connection.md) beschrieben sind. Führen Sie in diesem Fall die folgenden Schritte aus, um zu ermitteln, warum der RDP-Client (Remotedesktop) keine Verbindung mit dem RDP-Dienst auf dem virtuellen Azure-Computer herstellen kann.


Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Komponenten einer Remotedesktopverbindung
Eine Remotedesktopverbindung umfasst folgende Komponenten:

![Abbildung der an einer RDP-Verbindung (Remotedesktop) beteiligten Komponenten](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Bevor Sie fortfahren, kann es hilfreich sein zu überlegen, was sich seit der letzten erfolgreichen Remotedesktopverbindung mit dem virtuellen Computer geändert hat. Beispiel:

* Die öffentliche IP-Adresse des virtuellen Computers oder des Clouddiensts mit dem virtuellen Computer (auch virtuelle IP-Adresse [ [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)] genannt) hat sich geändert. Der RDP-Fehler kann darauf zurückzuführen sein, dass in Ihrem DNS-Clientcache immer noch die *alte IP-Adresse* für den DNS-Namen gespeichert ist. Löschen Sie den Cache des DNS-Clients, und versuchen Sie erneut eine Verbindung zum virtuellen Computer herzustellen. Oder versuchen Sie es direkt mit der neuen VIP.
* Sie verwenden nicht die vom Azure-Portal generierte Verbindung, sondern verwalten Ihre Remotedesktopverbindungen mithilfe einer Drittanbieteranwendung. Vergewissern Sie sich, dass die Anwendungskonfiguration den richtigen TCP-Port für den Remotedesktop-Datenverkehr enthält. Bei klassischen virtuellen Computern können Sie den Port im [Azure-Portal](https://portal.azure.com) überprüfen, indem Sie die Einstellungen für diesen virtuellen Computer aufrufen und auf „Endpunkte“ klicken.

## <a name="preliminary-steps"></a>Vorbereitende Schritte
Führen Sie die folgenden Schritte aus, bevor Sie mit der detaillierten Problembehandlung fortfahren:

* Überprüfen Sie im Azure-Portal, ob offensichtliche Probleme mit dem virtuellen Computer bestehen.
* Führen Sie dazu die im Artikel [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](troubleshoot-rdp-connection.md#quick-troubleshooting-steps)beschriebenen Schritte aus.
* Stellen Sie bei benutzerdefinierten Images sicher, dass Ihre VHD ordnungsgemäß vorbereitet ist, bevor Sie sie hochladen. Weitere Informationen finden Sie unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](../windows/prepare-for-upload-vhd-image.md).


Versuchen Sie anschließend erneut eine Remotedesktopverbindung zum virtuellen Computer herzustellen.

## <a name="detailed-troubleshooting-steps"></a>Ausführliche Problembehandlungsschritte
Wenn die Verbindung zwischen dem Remotedesktopclient und dem Remotedesktopdienst auf dem virtuellen Azure-Computer fehlschlägt, kann dies auf Fehler an folgenden Stellen zurückzuführen sein:

* [Remotedesktop-Clientcomputer](#source-1-remote-desktop-client-computer)
* [Edgegerät im Intranet des Unternehmens](#source-2-organization-intranet-edge-device)
* [Clouddienst-Endpunkt und die Zugriffssteuerungsliste (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Netzwerksicherheitsgruppen](#source-4-network-security-groups)
* [Windows-basierter virtueller Azure-Computer](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Quelle 1: Remotedesktop-Clientcomputer
Stellen Sie sicher, dass Ihr Computer Remotedesktopverbindungen mit einem anderen lokalen windowsbasierten Computer herstellen kann.

![Abbildung der Komponenten einer RDP-Verbindung (Remotedesktop). Der RDP-Client ist dabei hervorgehoben, und ein Pfeil zeigt zum anderen lokalen Computer, der dabei für die Verbindung steht.](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Sollte das nicht möglich sein, überprüfen Sie, ob auf Ihrem Computer Folgendes vorhanden ist:

* Eine lokale Firewalleinstellung, die den Remotedesktop-Datenverkehr blockiert.
* Lokal installierte Clientproxysoftware, die das Herstellen von Remotedesktopverbindungen verhindert.
* Lokal installierte Netzwerküberwachungssoftware, die das Herstellen von Remotedesktopverbindungen verhindert.
* Andere Arten von Sicherheitssoftware, die Datenverkehr überwachen oder bestimmte Arten von Datenverkehr zulassen/unterbinden und dadurch das Herstellen von Remotedesktopverbindungen verhindern.

Deaktivieren Sie die Software in diesen Fällen vorübergehend, und versuchen Sie dann, eine Remotedesktopverbindung mit einem lokalen Computer herzustellen. Wenn Sie damit die Ursache gefunden haben, korrigieren Sie zusammen mit Ihrem Netzwerkadministrator die Einstellungen der Software, um Remotedesktopverbindungen zu ermöglichen.

## <a name="source-2-organization-intranet-edge-device"></a>Quelle 2: Edgegerät im Intranet des Unternehmens
Stellen Sie sicher, dass ein direkt mit dem Internet verbundener Computer Remotedesktopverbindungen mit Ihrem virtuellen Azure-Computer herstellen kann.

![Abbildung der Komponenten einer RDP-Verbindung (Remotedesktop). Der RDP-Client, der mit dem Internet verbunden ist, ist dabei hervorgehoben, und ein Pfeil zeigt zu einer Azure-VM, der dabei für die Verbindung steht.](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Wenn Sie keinen Computer haben, der direkt mit dem Internet verbunden ist, können Sie einen neuen virtuellen Azure-Computer in einer Ressourcengruppe oder einem Clouddienst erstellen und mit diesem testen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Windows in Azure](../windows/quick-create-portal.md). Sie können den virtuellen Computer und die Ressourcengruppe bzw. den Clouddienst nach Abschluss des Tests löschen.

Wenn Sie eine Remotedesktopverbindung mit einem direkt mit dem Internet verbundenen Computer herstellen können, überprüfen Sie das Edgegerät für das Intranet Ihrer Organisation auf Folgendes:

* Eine interne Firewall, die HTTPS-Verbindungen mit dem Internet blockiert.
* Einen Proxyserver, der das Herstellen von Remotedesktopverbindungen verhindert.
* Software zur Angriffserkennung oder Netzwerküberwachung, die auf Geräten in Ihrem Edgenetzwerk ausgeführt wird und das Herstellen von Remotedesktopverbindungen verhindert.

Korrigieren Sie zusammen mit Ihrem Netzwerkadministrator die Einstellungen des Edgegeräts für das Intranet Ihrer Organisation, um HTTPS-basierte Remotedesktopverbindungen mit dem Internet zu ermöglichen.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Quelle 3: Clouddienst-Endpunkt und ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Überprüfen Sie für mit dem klassischen Bereitstellungsmodell erstellte virtuelle Computer, ob ein anderer virtueller Azure-Computer im gleichen Clouddienst oder virtuellen Netzwerk Remotedesktopverbindungen mit Ihrem virtuellen Azure-Computer herstellen kann.

![Abbildung der Komponenten einer RDP-Verbindung (Remotedesktop). Eine Azure-VM ist dabei hervorgehoben, und ein Pfeil zeigt zu einer anderen Azure-VM mit demselben Clouddienst, der dabei für eine Verbindung steht.](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Für virtuelle Computer, die im Resource Manager erstellt wurden, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#source-4-network-security-groups) fort.

Ist kein anderer virtueller Computer im gleichen Clouddienst oder virtuellen Netzwerk vorhanden, erstellen Sie einen. Führen Sie die unter [Erstellen eines virtuellen Computers unter Windows in Azure](../windows/quick-create-portal.md) beschriebenen Schritte aus. Löschen Sie den virtuellen Testcomputer nach Abschluss des Tests wieder.

Wenn Sie mit einem virtuellen Computer im gleichen Clouddienst oder virtuellen Netzwerk eine Remotedesktopverbindung herstellen können, überprüfen Sie Folgendes:

* Die Endpunktkonfiguration für den Remotedesktop-Datenverkehr auf der Ziel-VM: der private TCP-Port des Endpunkts muss mit dem TCP-Port übereinstimmen, den der Remotedesktop-Dienst der VM überwacht (standardmäßig 3389).
* Die ACL für den Endpunkt des Remotedesktop-Datenverkehrs auf der Ziel-VM: In ACLs können Sie anhand der Quell-IP-Adresse angeben, ob eingehender Datenverkehr aus dem Internet zugelassen oder verweigert wird. Falsch konfigurierte ACLs können verhindern, dass die Remotedesktop-Datenverkehr zum Endpunkt gelangt. Überprüfen Sie die ACLs, um sicherzustellen, dass eingehender Verkehr von den öffentlichen IP-Adressen des Proxy- oder eines anderen Edgeservers zugelassen wird. Weitere Informationen finden Sie unter [Was ist eine Endpunkt-Zugriffssteuerungsliste (Access Control List, ACL)?](/previous-versions/azure/virtual-network/virtual-networks-acl)

Um den Endpunkt als Problemquelle auszuschließen, entfernen Sie den aktuellen Endpunkt und erstellen Sie einen neuen Endpunkt. Wählen Sie einen zufälligen Port im Bereich von 49152 bis 65535 für die externe Portnummer aus. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

## <a name="source-4-network-security-groups"></a>Quelle 4: Netzwerksicherheitsgruppen
Netzwerksicherheitsgruppen ermöglichen Ihnen eine präzisere Steuerung des zulässigen eingehenden und ausgehenden Datenverkehrs. Sie können Regeln erstellen, die mehrere Subnetze und Clouddienste in einem virtuellen Azure-Netzwerk umfassen.

Verwenden Sie den Ansatz [Überprüfen des IP-Flusses](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md), um zu bestätigen, dass eine Regel in einer Netzwerksicherheitsgruppe den Datenverkehr an eine VM oder von einer VM blockiert. Sie können auch die aktiven Sicherheitsgruppenregeln überprüfen, um sicherzustellen, dass die NSG-Regel „Zulassen“ für eingehende Verbindungen vorhanden ist und für den RDP-Port (standardmäßig 3389) Priorität hat. Weitere Informationen finden Sie unter [Verwenden von effektiven Sicherheitsregeln zur Problembehandlung des Datenverkehrsflusses auf virtuellen Computern](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Quelle 5: Windows-basierter virtueller Azure-Computer
![Abbildung der Komponenten einer RDP-Verbindung (Remotedesktop) mit einer hervorgehoben Azure-VM innerhalb eines Clouddiensts und einer Meldung, dass es sich dabei um eine mögliche Fehlerquelle handeln könnte. Eine blaue Linie steht dafür, dass die Regeln der Netzwerksicherheitsgruppe den Datenverkehr zu oder von der Azure-VM blockieren könnten.](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Befolgen Sie die Anweisungen in [diesem Artikel](./reset-rdp.md). In diesem Artikel wird der Remotedesktopdienst auf dem virtuellen Computer zurückgesetzt:

* Die Windows-Firewall-Standardregel "Remotedesktop" (TCP-Port 3389) wird aktiviert.
* Remotedesktopverbindungen werden aktiviert, indem der HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections-Registrierungswert auf 0 festgelegt wird.

Versuchen Sie erneut, die Verbindung von Ihrem Computer aus herzustellen. Wenn Sie noch immer keine Remotedesktopverbindung herstellen können, überprüfen Sie folgende Fehlerquellen:

* Der Remotedesktopdienst wird auf dem virtuellen Zielcomputer nicht ausgeführt.
* Der Remotedesktopdienst überwacht nicht den TCP-Port 3389.
* Windows-Firewall oder eine andere lokale Firewall enthält eine Regel für ausgehenden Datenverkehr, durch die Remotedesktop-Datenverkehr verhindert wird.
* Software zur Erkennung von Eindringversuchen oder zur Netzwerküberwachung, die auf dem virtuellen Azure-Computer ausgeführt wird, verhindert Remotedesktopverbindungen.

Bei virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden, können Sie eine Azure PowerShell-Remotesitzung auf dem virtuellen Azure-Computer nutzen. Zunächst müssen Sie ein Zertifikat für den Clouddienst installieren, der als Host des virtuellen Computers fungiert. Navigieren Sie zu [Configure Secure Remote PowerShell Access to Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) (Konfigurieren des sicheren PowerShell-Remotezugriffs auf virtuelle Azure-Computer), und laden Sie die Skriptdatei **InstallWinRMCertAzureVM.ps1** auf Ihren lokalen Computer herunter.

Installieren Sie dann Azure PowerShell, sofern noch nicht geschehen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

Als Nächstes öffnen Sie eine Azure PowerShell-Eingabeaufforderung, und ändern Sie den aktuellen Ordner in den Speicherort der Skriptdatei **InstallWinRMCertAzureVM.ps1**. Um ein Azure PowerShell-Skript auszuführen, müssen Sie die richtige Ausführungsrichtlinie festlegen. Führen Sie den Befehl **Get-ExecutionPolicy** aus, um die aktuelle Richtlinienebene zu ermitteln. Weitere Informationen zum Festlegen der geeigneten Ebene finden Sie unter [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-5.1).

Geben Sie als Nächstes den Namen Ihres Azure-Abonnements, den Namen des Clouddiensts und den Namen des virtuellen Computers (ohne die Zeichen „<“ und „>“) ein, und führen Sie anschließend die folgenden Befehle aus:

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Den richtigen Abonnementnamen können Sie der Eigenschaft *SubscriptionName* in der Ausgabe des Befehls **Get-AzureSubscription** entnehmen. Der Name des Clouddiensts für den virtuellen Computer wird in der Spalte *ServiceName* in der Ausgabe des Befehls **Get-AzureVM** angezeigt.

Vergewissern Sie sich, dass Sie über das neue Zertifikat verfügen. Öffnen Sie hierzu ein Zertifikat-Snap-In für den aktuellen Benutzer, und navigieren Sie zum Ordner **Vertrauenswürdige Stammzertifizierungsstellen\Zertifikate**. Es sollte ein Zertifikat mit dem DNS-Namen des Clouddiensts in der Spalte "Ausgestellt für" angezeigt werden (Beispiel: "cloudservice4testing.cloudapp.net").

Starten Sie als Nächstes mit diesen Befehlen eine Azure PowerShell-Remotesitzung.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Nachdem Sie gültige Administratoranmeldeinformationen eingegeben haben, sollte Ihre Azure PowerShell-Eingabeaufforderung in etwa wie folgt aussehen:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Den ersten Teil dieser Eingabeaufforderung stellt der Name Ihres Clouddiensts dar, der die Ziel-VM enthält, z.B. „cloudservice4testing.cloudapp.net“. Sie können nun Azure PowerShell-Befehle für diesen Clouddienst eingeben, um die genannten Probleme zu untersuchen und Korrekturen an der Konfiguration vorzunehmen.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>So korrigieren Sie manuell den TCP-Port, der von den Remotedesktopdiensten überwacht wird
Führen Sie an der Eingabeaufforderung der Azure PowerShell-Remotesitzung den folgenden Befehl aus.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Die PortNumber-Eigenschaft enthält die aktuelle Portnummer. Ändern Sie bei Bedarf die Remotedesktop-Portnummer mit dem folgenden Befehl wieder in den Standardwert (3389).

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Stellen Sie sicher, dass die Portnummer mit diesem Befehl in 3389 geändert wurde.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Beenden Sie die Azure PowerShell-Remotesitzung mit dem folgenden Befehl.

```powershell
Exit-PSSession
```

Überprüfen Sie, ob der Remotedesktop-Endpunkt für den virtuellen Azure-Computer auch den TCP-Port 3398 als internen Port verwendet. Starten Sie den virtuellen Azure-Computer neu und versuchen Sie erneut, die Remotedesktopverbindung herzustellen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
[Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer](./reset-rdp.md)

[Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/)

[Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

[Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
