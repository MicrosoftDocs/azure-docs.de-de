---
title: Behandeln von RDP-Verbindungsfehlern bei virtuellen Azure-Computern anhand der Ereignis-ID | Microsoft-Dokumentation
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485395"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Behandeln von RDP-Verbindungsfehlern bei virtuellen Azure-Computern anhand der Ereignis-ID 

Dieser Artikel erläutert die Verwendung von Ereignis-IDs zum Behandeln von Problemen, die eine RDP-Verbindung (Remotedesktopprotokoll) mit einem virtuellen Azure-Computer (VM) verhindern.

## <a name="symptoms"></a>Symptome

Sie versuchen, eine RDP-Verbindung (Remotedesktopprotokoll) zum Herstellen einer Verbindung mit einer Azure-VM zu verwenden. Nach dem Eingeben Ihrer Anmeldeinformationen tritt ein Verbindungsfehler auf, und Sie empfangen die folgende Fehlermeldung:

**Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen. Versuchen Sie noch einmal, die Verbindung herzustellen, und wenden Sie sich an den Besitzer des Remotecomputers oder an Ihren Netzwerkadministrator, wenn das Problem weiterhin besteht.**

Um dieses Problem zu behandeln, überprüfen Sie die Ereignisprotokolle auf der VM, und beziehen Sie sich dann auf die im Folgenden beschriebenen Szenarien.

## <a name="before-you-troubleshoot"></a>Vor Beginn der Problembehandlung

### <a name="create-a-backup-snapshot"></a>Erstellen einer Momentaufnahme für die Sicherung

Wenn Sie eine Momentaufnahme für die Sicherung erstellen möchten, führen Sie die unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md) beschriebenen Schritte aus.

### <a name="connect-to-the-vm-remotely"></a>Herstellen einer Remoteverbindung mit dem virtuellen Computer

Um eine Remoteverbindung mit der VM herzustellen, verwenden Sie eine der in [How to use remote tools to troubleshoot Azure VM issues](remote-tools-troubleshoot-azure-vm-issues.md) (Verwenden von Remotetools zur Behandlung von Azure-VM-Problemen) beschriebenen Methoden.

## <a name="scenario-1"></a>Szenario 1

### <a name="event-logs"></a>Ereignisprotokolle

Führen Sie in einer CMD-Instanz die folgenden Befehle aus, um zu überprüfen, ob innerhalb der letzten 24 Stunden das Ereignis 1058 oder 1057 im Systemprotokoll erfasst wurde:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Protokollname**:      System <br />
**Quelle:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *Uhrzeit* <br />
**Ereignis-ID:**      1058 <br />
**Aufgabenkategorie:** Keine <br />
**Ebene:**         Error <br />
**Schlüsselwörter:**      Klassisch <br />
**Benutzer:**          – <br />
**Computer:**      *Computer* <br />
**Beschreibung:** Der RD-Sitzungshostserver konnte das abgelaufene selbstsignierte Zertifikat nicht ersetzen, das für die Authentifizierung des RD-Sitzungshostservers bei SSL-Verbindungen verwendet wird. Der relevante Statuscode war „Zugriff verweigert“.

**Protokollname**:      System <br />
**Quelle:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *Uhrzeit* <br />
**Ereignis-ID:**      1058 <br />
**Aufgabenkategorie:** Keine <br />
**Ebene:**         Error <br />
**Schlüsselwörter:**      Klassisch <br />
**Benutzer:**          – <br />
**Computer:**      *Computer* <br />
**Beschreibung:** Der RD-Sitzungshostserver konnte kein neues selbstsigniertes Zertifikat für die Authentifizierung des RD-Sitzungshostservers bei SSL-Verbindungen erstellen, der relevante Statuscode war „Objekt bereits vorhanden“.

**Protokollname**:      System <br />
**Quelle:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *Uhrzeit* <br />
**Ereignis-ID:**      1057 <br />
**Aufgabenkategorie:** Keine <br />
**Ebene:**         Error <br />
**Schlüsselwörter:**      Klassisch <br />
**Benutzer:**          – <br />
**Computer:**      *Computer* <br />
**Beschreibung:** Der RD-Sitzungshostserver konnte kein neues selbstsigniertes Zertifikat für die Authentifizierung des RD-Sitzungshostservers bei SSL-Verbindungen erstellen. Der relevante Statuscode war „Keyset nicht vorhanden“.

Sie können außerdem nach den SCHANNEL-Fehlerereignissen 36872 und 36870 suchen, indem Sie die folgenden Befehle ausführen:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Protokollname**:      System <br />
**Quelle:**        Schannel <br />
**Datum:**          — <br />
**Ereignis-ID:**      36870 <br />
**Aufgabenkategorie:** Keine <br />
**Ebene:**         Error <br />
**Schlüsselwörter:**       <br />
**Benutzer:**          SYSTEM <br />
**Computer:**      *Computer* <br />
**Beschreibung:** Schwerwiegender Fehler beim Versuch, auf den privaten Schlüssel der SSL-Server-Anmeldeinformationen zuzugreifen. Der vom Kryptografiemodul zurückgegebene Fehlercode ist 0x8009030D.  <br />
Der interne Fehlerstatus ist 10001.

### <a name="cause"></a>Ursache
Dieses Problem tritt auf, weil kein Zugriff auf die lokalen RSA-Verschlüsselungsschlüssel im Ordner „MachineKeys“ auf der VM möglich ist. Dieses Problem kann aus einem der folgenden Gründe auftreten:

1. Falsche Konfiguration der Berechtigungen für den Machinekeys-Ordner oder die RSA-Dateien.

2. Beschädigter oder fehlender RSA-Schlüssel.

### <a name="resolution"></a>Lösung

Um dieses Problem zu behandeln, müssen Sie mithilfe dieser Schritte die ordnungsgemäßen Berechtigungen für das RDP-Zertifikat festlegen.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Erteilen der Berechtigung für den MachineKeys-Ordner

1. Erstellen Sie ein Skript mit dem folgenden Inhalt:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Führen Sie dieses Skript aus, um die Berechtigungen des MachineKey-Ordners zurückzusetzen und die RSA-Dateien auf die Standardwerte zurückzusetzen.

3.  Versuchen Sie erneut, auf den virtuellen Computer zuzugreifen.

Nachdem das Skript ausgeführt wurde, können Sie die folgenden Dateien überprüfen, bei denen Berechtigungsprobleme auftreten:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Erneuern eines selbstsignierten RDP-Zertifikats

Wenn das Problem weiterhin besteht, führen Sie das folgende Skript aus, um sicherzustellen, dass das selbstsignierte RDP-Zertifikat erneuert wurde:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Wenn Sie das Zertifikat nicht erneuern können, befolgen Sie diese Schritte, um das Zertifikat zu löschen:

1. Öffnen Sie auf einer anderen VM im gleichen VNET das Feld **Ausführen**, geben Sie **mmc** ein, und drücken Sie dann **OK**. 

2. Wählen Sie im **Datei**-Menü **Snap-In hinzufügen/entfernen** aus.

3. Wählen Sie in der Liste **Verfügbare Snap-Ins** **Zertifikate** und dann **Hinzufügen** aus.

4. Wählen Sie **Computerkonto** und dann **Weiter** aus.

5. Wählen Sie **Anderer Computer** aus, und fügen Sie dann die IP-Adresse der VM hinzu, bei der Probleme auftreten.
   >[!Note]
   >Versuchen Sie, das interne Netzwerk zu verwenden, um die Nutzung einer virtuellen IP-Adresse zu vermeiden.

6. Wählen Sie **Fertig stellen** und dann **OK** aus.

   ![Computer auswählen](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Erweitern Sie die Zertifikate, wechseln Sie zum Ordner „Remotedesktop\Zertifikate“, klicken Sie mit der rechten Maustaste auf das Zertifikat, und wählen Sie dann **Löschen** aus.

8. Starten Sie den Remotedesktop-Konfigurationsdienst neu:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Wenn Sie an diesem Punkt den Speicher von der MMC aus aktualisieren, wird das Zertifikat wieder angezeigt. 

Versuchen Sie noch einmal, mithilfe von RDP auf die VM zuzugreifen.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Aktualisieren des SSL-Zertifikats (Secure Sockets Layer)

Wenn Sie die VM für die Verwendung eines SSL-Zertifikats einrichten, führen Sie den folgenden Befehl aus, um den Fingerabdruck abzurufen. Überprüfen Sie dann, ob er mit dem Fingerabdruck des Zertifikats übereinstimmt:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Falls nicht, ändern Sie den Fingerabdruck:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Sie können außerdem versuchen, den Schlüssel zu löschen, so dass RDP das selbstsignierte Zertifikat für RDP verwendet:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Szenario 2:

### <a name="event-log"></a>Ereignisprotokoll

Führen Sie in einer CMD-Instanz die folgenden Befehle aus, um zu überprüfen, ob innerhalb der letzten 24 Stunden das SCHANNEL-Fehlerereignis 36871 im Systemprotokoll erfasst wurde:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Protokollname**:      System <br />
**Quelle:**        Schannel <br />
**Datum:**          — <br />
**Ereignis-ID:**      36871 <br />
**Aufgabenkategorie:** Keine <br />
**Ebene:**         Error <br />
**Schlüsselwörter:**       <br />
**Benutzer:**          SYSTEM <br />
**Computer:**      *Computer* <br />
**Beschreibung:** Schwerwiegender Fehler beim Erstellen von TLS-Server-Anmeldeinformationen. Der interne Fehlerstatus ist 10013.
 
### <a name="cause"></a>Ursache

Dieses Problem wird durch Sicherheitsrichtlinien verursacht. Wenn ältere Versionen von TLS (z. B. 1.0) deaktiviert sind, tritt beim RDP-Zugriff ein Fehler auf.

### <a name="resolution"></a>Lösung

RDP verwendet TLS 1.0 als Standardprotokoll. Allerdings kann das Protokoll in TLS 1.1 geändert werden, das den neuen Standard darstellt.

Informationen zur Behandlung dieses Problems finden Sie unter [Problembehandlung von Authentifizierungsfehlern bei der Verwendung von RDP für das Herstellen von Verbindungen mit Azure-VMs](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Szenario 3

Wenn Sie die **Remotedesktop-Verbindungsbroker**-Rolle auf der VM installiert haben, überprüfen Sie, ob innerhalb der letzten 24 Stunden Ereignis 2056 oder Ereignis 1296 aufgetreten sind. Führen Sie in einer CMD-Instanz die folgenden Befehle aus: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Protokollname**:      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Quelle:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:**          *Uhrzeit* <br />
**Ereignis-ID:**      2056 <br />
**Aufgabenkategorie:** (109) <br />
**Ebene:**         Error <br />
**Schlüsselwörter:**       <br />
**Benutzer:**          NETZWERKDIENST <br />
**Computer:**      *Computer fqdn* <br />
**Beschreibung:** Die Beschreibung für Ereignis-ID 2056 aus der Quelle „Microsoft-Windows-TerminalServices-SessionBroker“ wurde nicht gefunden. Entweder ist die Komponente, die dieses Ereignis auslöst, auf Ihrem lokalen Computer nicht installiert, oder die Installation ist beschädigt. Sie können die Komponente auf dem lokalen Computer installieren oder reparieren. <br />
Würde das Ereignis von einem anderen Computer stammen, hätten die Anzeigeinformationen zusammen mit dem Ereignis gespeichert werden müssen. <br />
Das Ereignis beinhaltet die folgenden Informationen: <br />
NULL <br />
NULL <br />
Fehler bei der Anmeldung bei der Datenbank.

**Protokollname**:      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Quelle:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Datum:**          *Uhrzeit* <br />
**Ereignis-ID:**      1296 <br />
**Aufgabenkategorie:** (104) <br />
**Ebene:**         Error <br />
**Schlüsselwörter:**       <br />
**Benutzer:**          NETZWERKDIENST <br />
**Computer:**      *Computer fqdn* <br />
**Beschreibung:** Die Beschreibung für Ereignis-ID 1296 aus der Quelle „Microsoft-Windows-TerminalServices-SessionBroker-Client“ wurde nicht gefunden. Entweder ist die Komponente, die dieses Ereignis auslöst, auf Ihrem lokalen Computer nicht installiert, oder die Installation ist beschädigt. Sie können die Komponente auf dem lokalen Computer installieren oder reparieren.
Würde das Ereignis von einem anderen Computer stammen, hätten die Anzeigeinformationen zusammen mit dem Ereignis gespeichert werden müssen.
Das Ereignis beinhaltet die folgenden Informationen:  <br />
*text* <br />
*text* <br />
Der Remotedesktop-Verbindungsbroker ist nicht für RPC-Kommunikation bereit.

### <a name="cause"></a>Ursache

Dieses Problem tritt auf, weil der Hostname des Remotedesktop-Verbindungsbrokers geändert wurde. Diese Änderung wird nicht unterstützt. 

Für den Hostnamen bestehen Einträge und Abhängigkeiten in und mit der internen Windows-Datenbank, die für die Funktion der Remotedesktopdienst-Farm erforderlich sind. Das Ändern des Hostnamens nach dem Erstellen der Farm führt zu vielen Fehlern und kann die Funktion des Brokerservers beenden.

### <a name="resolution"></a>Lösung 

Um dieses Problem zu lösen, müssen die Remotedesktop-Verbindungsbroker-Rolle und die interne Windows-Datenbank neu installiert werden.

## <a name="next-steps"></a>Nächste Schritte

[SChannel-Ereignisse](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Schannel SSP – technische Übersicht](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP Fails with Event ID 1058 & Event 36870 with Remote Desktop Session Host Certificate & SSL Communication](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/) (RDP-Fehler mit Ereignis-ID 1058 und Ereignis 36870 mit Remotedesktop-Sitzungshostzertifikat und SSL-Kommunikation)

[Schannel 36872 oder Schannel 36870 auf einem Domänencontroller](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Ereignis-ID 1058 – Remotedesktopdienste, Authentifizierung und Verschlüsselung](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

