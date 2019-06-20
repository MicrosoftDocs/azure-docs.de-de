---
title: Herstellen einer Remoteverbindung mit dem StorSimple-Gerät | Microsoft Docs
description: Erläutert, wie das Gerät für die Remoteverwaltung konfiguriert und über HTTP oder HTTPS eine Verbindung mit Windows PowerShell für StorSimple hergestellt wird.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bec60f4c56c98e9b910b50e858656a2e5554b2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631782"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Herstellen einer Remoteverbindung mit Geräten der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht

Sie können mithilfe von Windows PowerShell eine Remoteverbindung mit Ihrem Gerät herstellen. Wenn Sie auf diese Weise eine Verbindung herstellen, wird kein Menü angezeigt. (Sie sehen nur ein Menü, wenn Sie zum Verbinden die serielle Konsole auf dem Gerät verwenden.) Mit Windows PowerShell-Remoting stellen Sie eine Verbindung mit einem bestimmten Runspace her. Sie können zudem die Anzeigesprache angeben.

Weitere Informationen zur Verwendung von Windows PowerShell-Remoting für die Verwaltung Ihres Geräts finden Sie unter [Verwenden von Windows PowerShell für StorSimple zum Verwalten Ihres StorSimple-Geräts](storsimple-8000-windows-powershell-administration.md).

In diesem Tutorial wird erläutert, wie das Gerät für die Remoteverwaltung konfiguriert und eine Verbindung mit Windows PowerShell für StorSimple hergestellt wird. Sie können HTTP oder HTTPS verwenden, um über Windows PowerShell eine Remoteverbindung herzustellen. Beachten Sie jedoch folgende Informationen, wenn Sie entscheiden, wie die Verbindung mit Windows PowerShell für StorSimple hergestellt werden soll:

* Das direkte Verbinden mit der seriellen Konsole des Geräts ist sicher, was für das Verbinden mit der seriellen Konsole über Netzwerkswitches nicht gilt. Bedenken Sie das Sicherheitsrisiko beim Herstellen einer Verbindung mit der seriellen Gerätekonsole über Netzwerkswitches.
* Das Herstellen einer Verbindung über eine HTTP-Sitzung bietet möglicherweise mehr Sicherheit als das Herstellen einer Verbindung über die serielle Konsole über das Netzwerk. Obwohl dies nicht die sicherste Methode ist, ist eine solche Verbindung bei vertrauenswürdigen Netzwerken akzeptabel.
* Eine Verbindung über eine HTTPS-Sitzung mit einem selbstsignierten Zertifikat ist die sicherste und empfohlene Option.

Sie können eine Remoteverbindung mit der Windows PowerShell-Benutzeroberfläche herstellen. Der Remotezugriff auf Ihr StorSimple-Gerät über die Windows PowerShell-Benutzeroberfläche ist jedoch standardmäßig nicht aktiviert. Sie müssen zunächst die Remoteverwaltung auf dem Gerät und anschließend auf dem Client aktivieren, der für den Zugriff auf das Gerät verwendet wird.

Die Schritte in diesem Artikel wurden auf einem Hostsystem mit Windows Server 2012 R2 ausgeführt.

## <a name="connect-through-http"></a>Verbinden über HTTP

Das Herstellen einer Verbindung mit Windows PowerShell für StorSimple über eine HTTP-Sitzung bietet mehr Sicherheit als das Herstellen einer Verbindung über die serielle Konsole des StorSimple-Geräts. Obwohl dies nicht die sicherste Methode ist, ist eine solche Verbindung bei vertrauenswürdigen Netzwerken akzeptabel.

Sie können die Remoteverwaltung mit dem Azure-Portal oder der seriellen Konsole konfigurieren. Wählen Sie zwischen den folgenden Vorgehensweisen:

* Verwenden des Azure-Portals zum Aktivieren der Remoteverwaltung über HTTP
* [Verwenden der seriellen Konsole zum Aktivieren der Remoteverwaltung über HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Nachdem Sie die Remoteverwaltung aktiviert haben, bereiten Sie den Client mit den folgenden Schritten für eine Remoteverbindung vor.

* [Vorbereiten des Clients für eine Remoteverbindung](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Verwenden des Azure-Portals zum Aktivieren der Remoteverwaltung über HTTP

Führen Sie die folgenden Schritte im Azure-Portal aus, um die Remoteverwaltung über HTTP zu aktivieren.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>So aktivieren Sie die Remoteverwaltung im Azure-Portal

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Wählen Sie **Geräte** aus, und klicken Sie dann auf das Gerät, das Sie für die Remoteverwaltung konfigurieren möchten. Wechseln Sie zu **Geräteeinstellungen > Sicherheit**.
2. Klicken Sie auf dem Blatt **Sicherheitseinstellungen** auf **Remoteverwaltung**.
3. Legen Sie auf dem Blatt **Remoteverwaltung** die Option **Remoteverwaltung aktivieren** auf **Ja** fest.
4. Sie können jetzt eine Verbindung über HTTP herstellen. (Die Standardeinstellung ist eine HTTPS-Verbindung.) Stellen Sie sicher, dass "HTTP" ausgewählt ist.
   
   > [!NOTE]
   > Verbindungen über HTTP sind nur in vertrauenswürdigen Netzwerken zulässig.
   
5. Klicken Sie auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Verwenden der seriellen Konsole zum Aktivieren der Remoteverwaltung über HTTP
Führen Sie die folgenden Schritte auf der seriellen Gerätekonsole aus, um die Remoteverwaltung zu aktivieren.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>So aktivieren Sie die Remoteverwaltung über die serielle Gerätekonsole
1. Wählen Sie im Menü der seriellen Konsole Option 1 aus. Weitere Informationen über die Verwendung der seriellen Konsole auf dem Gerät finden Sie unter [Herstellen einer Verbindung mit Windows PowerShell für StorSimple über die serielle Gerätekonsole](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Geben Sie an der Eingabeaufforderung Folgendes ein: `Enable-HcsRemoteManagement –AllowHttp`
3. Sie werden über die Sicherheitsrisiken bei der Verwendung von HTTP für die Verbindung mit dem Gerät informiert. Wenn Sie dazu aufgefordert werden, geben Sie **Y**zur Bestätigung ein.
4. Stellen Sie sicher, dass HTTP aktiviert ist, indem Sie Folgendes eingeben: `Get-HcsSystem`
5. Überprüfen Sie, ob im Feld **RemoteManagementMode** der Wert **HttpsAndHttpEnabled** angezeigt wird. Die folgende Abbildung zeigt diese Einstellungen in PuTTY.
   
     ![HTTPS und HTTP (seriell) aktiviert](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Vorbereiten des Clients für eine Remoteverbindung
Führen Sie die folgenden Schritte auf dem Client aus, um die Remoteverwaltung zu aktivieren.

#### <a name="to-prepare-the-client-for-remote-connection"></a>So bereiten Sie den Client für eine Remoteverbindung vor
1. Starten Sie eine Windows PowerShell-Sitzung als Administrator. Bei der Verwendung eines Windows 10-Clients wird der Windows-Remoteverwaltungsdienst standardmäßig auf „manuell“ festgelegt. Möglicherweise müssen Sie den Dienst starten, indem Sie Folgendes eingeben:

    `Start-Service WinRM`
    
2. Geben Sie den folgenden Befehl ein, um der Liste der vertrauenswürdigen Hosts des Clients die IP-Adresse des StorSimple-Geräts hinzuzufügen:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Ersetzen Sie <*device_ip*> durch die IP-Adresse Ihres Geräts. Beispiel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Geben Sie den folgenden Befehl ein, um die Anmeldeinformationen für das Gerät in einer Variablen zu speichern: 
   
    ```
    $cred = Get-Credential
    ```
    
4. Im angezeigten Dialogfeld:
   
   1. Geben Sie den Benutzernamen in folgendem Format ein: *Geräte-IP\SSAdmin*.
   2. Geben Sie das Kennwort des Geräteadministrators ein, das festgelegt wurde, als das Gerät mit dem Setup-Assistenten konfiguriert wurde. Das Standardkennwort lautet *Password1*.
5. Starten Sie eine Windows PowerShell-Sitzung auf dem Gerät, indem Sie den folgenden Befehl eingeben:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Fügen Sie zum Erstellen einer Windows PowerShell-Sitzung für die Verwendung mit dem virtuellen StorSimple-Gerät den `–Port`-Parameter an, und geben Sie den öffentlichen Port an, den Sie zum Remoting für das virtuelle StorSimple-Gerät konfiguriert haben.
   
   
Jetzt sollten Sie über eine aktive Windows PowerShell-Remotesitzung auf dem Gerät verfügen.
   
![PowerShell-Remoting über HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Verbinden über HTTPS

Das Herstellen einer Verbindung mit Windows PowerShell für StorSimple über eine HTTPS-Sitzung ist die sicherste und die empfohlene Methode zum Herstellen einer Remoteverbindung mit Ihrem Microsoft Azure StorSimple-Gerät. In den folgenden Vorgehensweisen wird erläutert, wie die serielle Konsole und der Clientcomputer so eingerichtet werden, dass Sie HTTPS verwenden können, um eine Verbindung mit Windows PowerShell für StorSimple herzustellen.

Sie können die Remoteverwaltung mit dem Azure-Portal oder der seriellen Konsole konfigurieren. Wählen Sie zwischen den folgenden Vorgehensweisen:

* Verwenden des Azure-Portals zum Aktivieren der Remoteverwaltung über HTTPS
* [Verwenden der seriellen Konsole zum Aktivieren der Remoteverwaltung über HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Nachdem Sie die Remoteverwaltung aktiviert haben, gehen Sie wie folgt vor, um den Host für eine Remoteverwaltung vorzubereiten und über den Remotehost eine Verbindung mit dem Gerät herzustellen.

* [Vorbereiten des Hosts für die Remoteverwaltung](#prepare-the-host-for-remote-management)
* [Herstellen einer Verbindung mit dem Gerät über den Remotehost](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Verwenden des Azure-Portals zum Aktivieren der Remoteverwaltung über HTTPS

Führen Sie die folgenden Schritte im Azure-Portal aus, um die Remoteverwaltung über HTTPS zu aktivieren.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>So aktivieren Sie die Remoteverwaltung über HTTPS im Azure-Portal

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Wählen Sie **Geräte** aus, und klicken Sie dann auf das Gerät, das Sie für die Remoteverwaltung konfigurieren möchten. Wechseln Sie zu **Geräteeinstellungen > Sicherheit**.
2. Klicken Sie auf dem Blatt **Sicherheitseinstellungen** auf **Remoteverwaltung**.
3. Legen Sie **Remoteverwaltung aktivieren** auf **Ja** fest.
4. Sie können jetzt eine Verbindung über HTTPS herstellen. (Die Standardeinstellung ist eine HTTPS-Verbindung.) Stellen Sie sicher, dass "HTTPS" ausgewählt ist.
5. Klicken Sie auf „...“ und dann auf **Remoteverwaltungszertifikat herunterladen**. Geben Sie einen Speicherort an, um diese Datei zu speichern. Sie müssen dieses Zertifikat auf dem Client oder dem Hostcomputer installieren, den Sie für die Verbindung mit dem Gerät verwenden werden.
6. Klicken Sie auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Verwenden der seriellen Konsole zum Aktivieren der Remoteverwaltung über HTTPS

Führen Sie die folgenden Schritte auf der seriellen Gerätekonsole aus, um die Remoteverwaltung zu aktivieren.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>So aktivieren Sie die Remoteverwaltung über die serielle Gerätekonsole
1. Wählen Sie im Menü der seriellen Konsole Option 1 aus. Weitere Informationen über die Verwendung der seriellen Konsole auf dem Gerät finden Sie unter [Herstellen einer Verbindung mit Windows PowerShell für StorSimple über die serielle Gerätekonsole](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
     `Enable-HcsRemoteManagement`
   
    Damit wird HTTPS auf Ihrem Gerät aktiviert.
3. Stellen Sie sicher, dass HTTPS aktiviert wurde, indem Sie Folgendes eingeben: 
   
     `Get-HcsSystem`
   
    Vergewissern Sie sich, dass das Feld **RemoteManagementMode** den Wert **HttpsEnabled** enthält. Die folgende Abbildung zeigt diese Einstellungen in PuTTY.
   
     ![HTTPS (seriell) aktiviert](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Kopieren Sie aus der Ausgabe von `Get-HcsSystem`die Seriennummer des Geräts, und speichern Sie sie zur späteren Verwendung.
   
   > [!NOTE]
   > Die Seriennummer entspricht dem CN-Namen im Zertifikat.
   
5. Rufen Sie ein Zertifikat für die Remoteverwaltung ab, indem Sie Folgendes eingeben: 
   
     `Get-HcsRemoteManagementCert`
   
    Ein Zertifikat wie das folgende wird angezeigt.
   
    ![Zertifikat für die Remoteverwaltung abrufen](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Kopieren Sie die Informationen im Zertifikat zwischen **-----BEGIN CERTIFICATE-----** und **-----END CERTIFICATE-----** in einen Text-Editor wie Editor, und speichern Sie sie als CER-Datei. (Sie kopieren diese Datei auf den Remotehost, wenn Sie den Host vorbereiten.)
   
   > [!NOTE]
   > Verwenden Sie das Cmdlet `Set-HcsRemoteManagementCert`, um ein neues Zertifikat zu generieren.
   
### <a name="prepare-the-host-for-remote-management"></a>Vorbereiten des Hosts für die Remoteverwaltung

Um den Hostcomputer für eine Remoteverbindung vorzubereiten, die eine HTTPS-Sitzung verwendet, führen Sie folgende Schritte aus:

* [Importieren Sie die CER-Datei in den Stammspeicher des Clients oder Remotehosts](#to-import-the-certificate-on-the-remote-host).
* [Fügen Sie der Datei "hosts" auf dem Remotehost die Seriennummern des Geräts hinzu](#to-add-device-serial-numbers-to-the-remote-host).

Diese anschließende Schritte werden im Folgenden beschrieben.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>So importieren Sie das Zertifikat auf den Remotehost
1. Klicken Sie mit der rechten Maustaste auf die CER-Datei, und wählen Sie **Zertifikat installieren**aus. Damit starten Sie den Zertifikatimport-Assistenten.
   
    ![Zertifikatimport-Assistent 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Wählen Sie für **Speicherort** die Option **Lokaler Computer** aus, und klicken Sie dann auf **Weiter**.
3. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** aus, und klicken Sie dann auf **Durchsuchen**. Navigieren Sie zum Stammspeicher des Remotehosts, und klicken Sie dann auf **Weiter**.
   
    ![Zertifikatimport-Assistent 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klicken Sie auf **Fertig stellen**. Eine Meldung wird angezeigt, die besagt, dass der Import erfolgreich war.
   
    ![Zertifikatimport-Assistent 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>So fügen Sie dem Remotehost die Seriennummern des Geräts hinzu
1. Starten Sie Editor als Administrator, und öffnen Sie die Datei "hosts" unter "\Windows\System32\Drivers\etc".
2. Fügen Sie Ihrer hosts-Datei die folgenden drei Einträge hinzu: **DATA 0 IP-Adresse**, **feste IP-Adresse für Controller 0** und **feste IP-Adresse für Controller 1**.
3. Geben Sie die Seriennummer des Geräts ein, die Sie zuvor gespeichert haben. Ordnen Sie sie der IP-Adresse zu, wie in der folgenden Abbildung dargestellt. Fügen Sie für Controller 0 und Controller 1 **Controller0** und **Controller1** am Ende der Seriennummer an (CN-Name).
   
    ![CN-Namen der Datei "hosts" hinzufügen](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Speichern Sie die Datei "hosts".

### <a name="connect-to-the-device-from-the-remote-host"></a>Herstellen einer Verbindung mit dem Gerät über den Remotehost

Verwenden Sie Windows PowerShell und SSL, um auf dem Gerät über einen Remotehost oder Client eine SSAdmin-Sitzung zu starten. Die SSAdmin-Sitzung entspricht Option 1 im Menü der [seriellen Konsole](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) Ihres Geräts.

Führen Sie die folgenden Schritte auf dem Computer aus, über den Sie die Windows PowerShell-Remoteverbindung herstellen möchten.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>So starten Sie mithilfe von Windows PowerShell und SSL eine SSAdmin-Sitzung auf dem Gerät
1. Starten Sie eine Windows PowerShell-Sitzung als Administrator. Bei der Verwendung eines Windows 10-Clients wird der Windows-Remoteverwaltungsdienst standardmäßig auf „manuell“ festgelegt. Möglicherweise müssen Sie den Dienst starten, indem Sie Folgendes eingeben:

    `Start-Service WinRM`

2. Fügen Sie die IP-Adresse des Geräts den vertrauenswürdigen Hosts des Clients hinzu, indem Sie Folgendes eingeben:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Dabei ist <*device_ip*> die IP-Adresse des Geräts, Beispiel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Erstellen Sie neue Anmeldeinformationen, indem Sie Folgendes eingeben:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Dabei ist <*IP of target device*> die IP-Adresse von DATA 0 für Ihr Gerät, beispielsweise **10.126.173.90**, wie in der obigen Abbildung der Datei "hosts" dargestellt. Geben Sie auch das Administratorkennwort für Ihr Gerät an.
4. Erstellen Sie eine Sitzung, indem Sie Folgendes eingeben:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Geben Sie für den Parameter „-ComputerName“ im Cmdlet die <*Seriennummer des Zielgeräts*> an. Diese Seriennummer wurde der IP-Adresse von DATA 0 in der Datei "hosts" auf dem Remotehost zugeordnet, beispielsweise **SHX0991003G44MT** , wie in der folgenden Abbildung dargestellt.
5. Geben Sie Folgendes ein:
   
     `Enter-PSSession $session`
6. Sie müssen einige Minuten warten, und dann wird über HTTPS über SSL eine Verbindung mit Ihrem Gerät hergestellt. Es wird eine Meldung darüber angezeigt, dass Sie mit dem Gerät verbunden sind.
   
    ![PowerShell-Remoting über HTTP und SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr zur [Verwendung von Windows PowerShell zum Verwalten Ihres StorSimple-Geräts](storsimple-8000-windows-powershell-administration.md).
* Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).

