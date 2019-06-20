---
title: Remotedesktop-Lizenzserver beim Herstellen einer Verbindung mit einer Azure-VM nicht verfügbar | Microsoft-Dokumentation
description: Informationen zum Behandeln von RDP-Problemen aufgrund eines fehlenden Remotedesktop-Lizenzservers | Microsoft-Dokumentation
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 550b971602d1736e0ba3981a5b7ca546862ea034
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318951"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Remotedesktop-Lizenzserver beim Herstellen einer Verbindung mit einer Azure-VM nicht verfügbar

Dieser Artikel bietet Unterstützung bei der Problemlösung, wenn Sie keine Verbindung mit einem virtuellen Azure-Computer (Azure-VM) herstellen können, weil kein Remotedesktop-Lizenzserver zum Bereitstellen einer Lizenz verfügbar ist.

## <a name="symptoms"></a>Symptome

Wenn Sie versuchen, eine Verbindung mit einer VM herzustellen, beobachten Sie dieses Verhalten:

- Der VM-Screenshot zeigt, dass das Betriebssystem vollständig geladen ist und auf Anmeldeinformationen wartet.
- Sie erhalten die folgenden Fehlermeldungen, wenn Sie versuchen, eine Microsoft RDP-Verbindung (Remotedesktopprotokoll) herzustellen:

  - Die Remotesitzung wurde getrennt, weil kein Remotedesktop-Lizenzserver zum Bereitstellen einer Lizenz verfügbar ist.

  - Es ist kein Remotedesktop-Lizenzserver verfügbar. Die Remotedesktopdienste werden beendet, weil die Karenzzeit für diesen Computer abgelaufen ist und der Computer nicht mindestens einen gültigen Windows Server 2008-Lizenzserver kontaktiert hat. Wählen Sie diese Meldung aus, um die RD-Sitzungshost-Serverkonfiguration für eine Lizenzierungsdiagnose zu öffnen.

Sie können jedoch über eine administrative Sitzung eine normale Verbindung mit der VM herstellen:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Ursache

Dieses Problem tritt auf, wenn kein Remotedesktop-Lizenzserver verfügbar ist, um eine Lizenz zum Starten einer Remotesitzung bereitzustellen. Es gibt verschiedene Ursachen, selbst wenn eine Remotedesktop-Sitzungshostrolle auf der VM eingerichtet wurde:

- Es war nie eine Remotedesktop-Lizenzierungsrolle in der Umgebung vorhanden, und die Karenzzeit (180 Tage) ist abgelaufen.
- In der Umgebung wurde eine Remotedesktoplizenz installiert, aber nie aktiviert.
- Eine in der Umgebung vorhandene Remotedesktoplizenz umfasst keine Clientzugriffslizenzen (CALs) zum Einrichten der Verbindung.
- In der Umgebung wurde eine Remotedesktoplizenz installiert. Es sind CALs verfügbar, aber sie wurden nicht ordnungsgemäß konfiguriert.
- Es ist eine Remotedesktoplizenz mit CALs vorhanden, und diese wurde aktiviert. Probleme auf dem Remotedesktop-Lizenzserver verhindern jedoch, dass Lizenzen in der Umgebung bereitgestellt werden.

## <a name="solution"></a>Lösung

Um dieses Problem zu lösen [erstellen Sie eine Sicherung des Betriebssystemdatenträgers](../windows/snapshot-copy-managed-disk.md) und führen die folgenden Schritte aus:

1. Stellen Sie über eine administrative Sitzung eine Verbindung mit der VM her:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Wenn es nicht möglich ist, über eine Verwaltungssitzung eine Verbindung mit der VM herzustellen, können Sie die [serielle Konsole für VMs in Azure](serial-console-windows.md) für den Zugriff auf die VM verwenden:

    1. Greifen Sie auf die serielle Konsole zu, indem Sie **Support und Problembehandlung** > **Serielle Konsole (Vorschau)** auswählen. Wenn dieses Feature auf der VM aktiviert ist, können Sie erfolgreich eine Verbindung mit der VM herstellen.

    2. Erstellen Sie einen neuen Kanal für eine CMD-Instanz. Geben Sie **CMD** ein, um den Kanal zu starten und den Kanalnamen abzurufen.

    3. Wechseln Sie zu dem Kanal, in dem die CMD-Instanz ausgeführt wird. In diesem Fall ist das Kanal 1:

       ```
       ch -si 1
       ```

    4. Drücken Sie erneut die **EINGABETASTE**, und geben Sie einen gültigen Benutzernamen, ein Kennwort sowie eine lokale oder Domänen-ID für die VM ein.

2. Überprüfen Sie, ob für die VM eine Remotedesktop-Sitzungshostrolle aktiviert ist. Wenn die Rolle aktiviert ist, überprüfen Sie, ob sie ordnungsgemäß funktioniert. Öffnen Sie eine CMD-Instanz mit erhöhten Rechten, und führen Sie diese Schritte aus:

    1. Verwenden Sie den folgenden Befehl, um den Status der Remotedesktop-Sitzungshostrolle zu überprüfen:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Wenn dieser Befehl den Wert 0 zurückgibt, ist die Rolle deaktiviert, und Sie können mit Schritt 3 fortfahren.

    2. Verwenden Sie den folgenden Befehl, um die Richtlinien zu überprüfen und ggf. neu zu konfigurieren:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Wenn der **LicensingMode**-Wert auf einen anderen Wert als 4 pro Benutzer festgelegt ist, legen Sie den Wert auf 4 fest:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Wenn der **SpecifiedLicenseServers**-Wert nicht vorhanden ist oder über falsche Lizenzserverinformationen verfügt, nehmen Sie die folgenden Änderungen vor:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Starten Sie die VM neu, nachdem Sie Änderungen an der Registrierung vorgenommen haben.

    4. Wenn Sie nicht über Clientzugriffslizenzen (CALs) verfügen, entfernen Sie die Remotedesktop-Sitzungshostrolle. Dann wird das RDP wieder auf normal festgelegt. Es sind nur zwei gleichzeitige RDP-Verbindungen mit der VM möglich:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Wenn die VM über die Lizenzierungsrolle Remotedesktop verfügt und diese nicht verwendet wird, können Sie die Rolle auch entfernen:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Stellen Sie sicher, dass die VM eine Verbindung mit dem Remotedesktop-Lizenzserver herstellen kann. Sie können die Konnektivität mit Port 135 zwischen der VM und dem Lizenzserver testen: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Wenn kein Remotedesktop-Lizenzserver in der Umgebung vorhanden ist und Sie einen solchen Lizenzserver verwenden möchten, können Sie einen [Remotedesktop-Lizenzierungsrollendienst installieren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). [Konfigurieren Sie die RDS-Lizenzierung](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/) im Anschluss.

4. Wenn ein Remotedesktop-Lizenzserver konfiguriert ist und fehlerfrei arbeitet, stellen Sie sicher, dass der Remotedesktop-Lizenzserver mit Clientzugriffslizenzen (CALs) aktiviert ist.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem zu beheben.
