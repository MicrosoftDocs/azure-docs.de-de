---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 258f0137d630f95fb7dd5dd17072bb77c1388be9
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581047"
---
Je nach Betriebssystem des Clients unterscheiden sich die Verfahren zum Herstellen von Remoteverbindungen mit dem Gerät.

### <a name="remotely-connect-from-a-windows-client"></a>Herstellen einer Remoteverbindung von einem Windows-Client

Vergewissern Sie sich vor dem Beginn, dass auf Ihrem Windows-Client Windows PowerShell 5.0 oder höher ausgeführt wird.

Befolgen Sie die folgenden Schritte, um eine Remoteverbindung von einem Windows-Client aus herzustellen.

1. Führen Sie eine Windows PowerShell-Sitzung als Administrator aus.
2. Stellen Sie sicher, dass der Dienst Windows-Remoteverwaltung auf dem Client ausgeführt wird. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `winrm quickconfig`

3. Weisen Sie der IP-Adresse des Geräts eine Variable zu.

    $ip = "<device_ip>"

    Ersetzen Sie `<device_ip>` durch die IP-Adresse des Geräts.

4. Geben Sie den folgenden Befehl ein, um der Liste der vertrauenswürdigen Hosts des Clients die IP-Adresse Ihres Geräts hinzuzufügen:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Starten Sie eine Windows PowerShell-Sitzung auf dem Gerät:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Geben Sie das Kennwort an, wenn Sie dazu aufgefordert werden. Verwenden Sie dasselbe Kennwort wie für die Anmeldung bei der lokalen Webbenutzeroberfläche. Das Standardkennwort für die lokale Webbenutzeroberfläche lautet *Password1*. Wenn Sie mithilfe von PowerShell eine Verbindung mit dem Gerät herstellen konnten, wird die folgende Beispielausgabe angezeigt:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Herstellen einer Remoteverbindung von einem Linux-Client

Gehen Sie auf dem Linux-Client, den Sie für die Verbindung verwenden, folgendermaßen vor:

- [Installieren Sie die neueste Version von PowerShell Core für Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6&preserve-view=true) von GitHub, um das Feature SSH-Remoting zu erhalten. 
- [Installieren Sie nur das Paket `gss-ntlmssp` aus dem NTLM-Modul](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Führen Sie bei Ubuntu-Clients den folgenden Befehl aus:
    - `sudo apt-get install gss-ntlmssp`

Weitere Informationen finden Sie unter [PowerShell-Remoting über SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6&preserve-view=true).

Befolgen Sie die folgenden Schritte, um eine Remoteverbindung von einem NFS-Client aus herzustellen.

1. Um eine PowerShell-Sitzung zu öffnen, geben Sie Folgendes ein:

    `sudo pwsh`
 
2. Zum Herstellen einer Verbindung mit dem Remoteclient geben Sie Folgendes ein:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Geben Sie bei Aufforderung das Kennwort zur Anmeldung auf Ihrem Gerät an.
 
> [!NOTE]
> Dieses Verfahren funktioniert nicht unter macOS.
