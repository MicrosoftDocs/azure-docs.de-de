---
title: Problembehandlung bei Azure Point-to-Site-Verbindungsproblemen
titleSuffix: Azure VPN Gateway
description: Erfahren Sie, wie Sie häufige Probleme mit der Point-to-Site-Verbindung und andere Fehler und Probleme im virtuellen privaten Netzwerk beheben.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994858"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Problembehandlung: Azure Point-to-Site-Verbindungsprobleme

In diesem Artikel werden allgemeine Punkt-zu-Standort-Verbindungsprobleme aufgeführt, die auftreten können. Darüber hinaus werden die möglichen Ursachen und Lösungen für diese Probleme diskutiert.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-Clientfehler: Ein Zertifikat wurde nicht gefunden.

### <a name="symptom"></a>Symptom

Wenn Sie versuchen, mithilfe des VPN-Client mit einem virtuellen Netzwerk von Azure eine Verbindung herzustellen, erhalten Sie folgende Fehlermeldung:

**Es konnte kein Zertifikat gefunden werden, das mit dem Extensible Authentication-Protokoll verwendet werden kann. (Fehler 798)**

### <a name="cause"></a>Ursache

Dieses Problem tritt auf, wenn das Clientzertifikat in **Zertifikate – Aktueller Benutzer\Eigene Zertifikate\Zertifikate** fehlt.

### <a name="solution"></a>Lösung

Gehen Sie folgendermaßen vor, um dieses Problem zu beheben:

1. Öffnen Sie den Zertifikat-Manager: Klicken Sie auf **Start**, geben Sie **Computerzertifikate verwalten** ein, und klicken Sie dann in den Suchergebnissen auf **Computerzertifikate verwalten**.

2. Stellen Sie sicher, dass sich die folgenden Zertifikate im richtigen Speicherort befinden:

    | Zertifikat | Standort |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktueller Benutzer\Eigene Zertifikate\Zertifikate |
    | AzureRoot.cer    | Lokaler Computer\Vertrauenswürdige Stammzertifizierungsstellen|

3. Navigieren Sie zu „C:\Users\<UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>“, und installieren Sie das Zertifikat (CER-Datei) im Speicher für Computer und Benutzer.

Weitere Informationen zum Installieren des Clientzertifikats finden Sie unter [Generieren und Exportieren von Zertifikaten für Punkt-zu-Standort-Verbindungen mithilfe von PowerShell](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Wenn Sie das Clientzertifikat importieren, wählen Sie nicht die Option **Verstärkte Sicherheit für den privaten Schlüssel aktivieren** aus.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Die Netzwerkverbindung zwischen Ihrem Computer und dem VPN-Server konnte nicht hergestellt werden, weil der Remoteserver nicht antwortet.

### <a name="symptom"></a>Symptom

Wenn Sie versuchen, unter Windows mithilfe von IKEv2 eine Verbindung mit einem Gateway für virtuelle Azure-Netzwerke herzustellen, erhalten Sie die folgende Fehlermeldung:

**Die Netzwerkverbindung zwischen Ihrem Computer und dem VPN-Server konnte nicht hergestellt werden, weil der Remoteserver nicht antwortet.**

### <a name="cause"></a>Ursache
 
 Das Problem tritt auf, wenn die Version von Windows die IKE-Fragmentierung nicht unterstützt.
 
### <a name="solution"></a>Lösung

IKEv2 wird unter Windows 10 und Server 2016 unterstützt. Zur Verwendung von IKEv2 müssen Sie jedoch Updates installieren und lokal einen Registrierungsschlüsselwert festlegen. Betriebssystemversionen vor Windows 10 werden nicht unterstützt und können nur SSTP verwenden.

Vorbereitung von Windows 10 oder Server 2016 für IKEv2:

1. Installieren Sie das Update.

   | Betriebssystemversion | Date | Anzahl/Link |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, Version 1607 | 17. Januar 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, Version 1703 | 17. Januar 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10, Version 1709 | 22. März 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Legen Sie den Registrierungsschlüsselwert fest. Erstellen Sie den REG_DWORD-Schlüssel in der Registrierung unter `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload`, oder legen Sie ihn auf 1 fest.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-Clientfehler: Das Format der empfangenen Nachricht war unerwartet oder fehlerhaft.

### <a name="symptom"></a>Symptom

Wenn Sie versuchen, mithilfe des VPN-Client mit einem virtuellen Netzwerk von Azure eine Verbindung herzustellen, erhalten Sie folgende Fehlermeldung:

**Das Format der empfangenen Nachricht war unerwartet oder fehlerhaft. (Fehler 0x80090326)**

### <a name="cause"></a>Ursache

Dieses Problem tritt auf, wenn eine der folgenden Bedingungen zutrifft:

- Die benutzerdefinierten Routen (User-Defined Routes, UDR) mit der Standardroute für das Gatewaysubnetz ist falsch festgelegt.
- Der öffentliche Schlüssel des Stammzertifikats wird nicht in das VPN-Gateway von Azure hochgeladen. 
- Der Schlüssel ist beschädigt oder abgelaufen.

### <a name="solution"></a>Lösung

Gehen Sie folgendermaßen vor, um dieses Problem zu beheben:

1. Entfernen Sie die UDR für das Gatewaysubnetz. Stellen Sie sicher, dass die UDR sämtlichen Datenverkehr ordnungsgemäß weiterleitet.
2. Überprüfen Sie den Status des Stammzertifikats im Azure-Portal, um festzustellen, ob es widerrufen wurde. Wenn es nicht widerrufen wurde, versuchen Sie, das Stammzertifikat zu löschen und es erneut hochzuladen. Weitere Informationen finden Sie unter [Erstellen von Zertifikaten](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-Clientfehler: Eine Zertifikatkette wurde verarbeitet, aber beendet. 

### <a name="symptom"></a>Symptom 

Wenn Sie versuchen, mithilfe des VPN-Client mit einem virtuellen Netzwerk von Azure eine Verbindung herzustellen, erhalten Sie folgende Fehlermeldung:

**Eine Zertifikatkette wurde verarbeitet, aber in einem Stammzertifikat beendet, dem der Vertrauensanbieter nicht vertraut.**

### <a name="solution"></a>Lösung

1. Stellen Sie sicher, dass sich die folgenden Zertifikate im richtigen Speicherort befinden:

    | Zertifikat | Standort |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktueller Benutzer\Eigene Zertifikate\Zertifikate |
    | Azuregateway-*GUID*.cloudapp.net  | Aktueller Benutzer\Vertrauenswürdige Stammzertifizierungsstellen|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokaler Computer\Vertrauenswürdige Stammzertifizierungsstellen|

2. Wenn die Zertifikate bereits im Speicherort vorhanden sind, versuchen Sie, die Zertifikate zu löschen und neu zu installieren. Das Zertifikat **azuregateway-*GUID*.cloudapp.net** befindet sich im VPN-Clientkonfigurationspaket, das Sie aus dem Azure-Portal heruntergeladen haben. Sie können Dateiarchivierer verwenden, um die Dateien aus dem Paket zu extrahieren.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fehler beim Herunterladen der Datei: Ziel-URI ist nicht angegeben.

### <a name="symptom"></a>Symptom

Sie erhalten die folgende Fehlermeldung:

**Fehler beim Herunterladen der Datei. Ziel-URI ist nicht angegeben.**

### <a name="cause"></a>Ursache 

Dieses Problem tritt aufgrund eines falschen Gatewaytyps auf. 

### <a name="solution"></a>Lösung

Der VPN-Gatewaytyp muss **VPN** sein, und der VPN-Typ muss als **RouteBased** festgelegt sein.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-Clientfehler: Fehler des benutzerdefinierten Azure-VPN-Skripts 

### <a name="symptom"></a>Symptom

Wenn Sie versuchen, mithilfe des VPN-Client mit einem virtuellen Netzwerk von Azure eine Verbindung herzustellen, erhalten Sie folgende Fehlermeldung:

**Custom script (to update your routing table) failed. (Fehler mit benutzerdefiniertem Skript (beim Aktualisieren Ihrer Routingtabelle).) (Fehler 8007026f)**

### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn Sie versuchen, die Standort-zu-Punkt-VPN-Verbindung mithilfe einer Verknüpfung zu öffnen.

### <a name="solution"></a>Lösung 

Öffnen Sie das VPN-Clientpaket direkt, anstatt es über die Verknüpfung zu öffnen.

## <a name="cannot-install-the-vpn-client"></a>VPN-Client kann nicht installiert werden

### <a name="cause"></a>Ursache 

Ein zusätzliches Zertifikat ist erforderlich, um dem VPN-Gateway für Ihr virtuelles Netzwerk zu vertrauen. Das Zertifikat ist im VPN-Clientkonfigurationspaket enthalten, das vom Azure-Portal generiert wird.

### <a name="solution"></a>Lösung

Extrahieren Sie das VPN-Clientkonfigurationspaket, und suchen Sie nach der CER-Datei. Um das Zertifikat zu installieren, führen Sie die folgenden Schritte aus:

1. Öffnen Sie „mmc.exe“.
2. Fügen Sie das **Zertifikate** -Snap-in hinzu.
3. Wählen Sie das **Computer** -Konto für den lokalen Computer aus.
4. Klicken Sie mit der rechten Maustaste auf den Knoten **Vertrauenswürdige Stammzertifizierungsstellen**. Klicken Sie auf **All-Task** (Alle Aufgaben) > **Importieren**, und suchen Sie nach der CER-Datei, die Sie aus dem VPN-Clientkonfigurationspaket extrahiert haben.
5. Starten Sie den Computer neu. 
6. Versuchen Sie eine erneute Installation des VPN-Clients.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Fehler im Azure-Portal: Fehler beim Speichern des VPN-Gateways, und die Daten sind ungültig.

### <a name="symptom"></a>Symptom

Wenn Sie versuchen, die Änderungen für das VPN-Gateway im Azure-Portal zu speichern, erhalten Sie die folgende Fehlermeldung:

**Fehler beim Speichern des Gateways für virtuelle Netzwerke &lt;*Gatewayname*&gt;. Die Daten für das Zertifikat &lt;*Zertifikat-ID*&gt; sind ungültig.**

### <a name="cause"></a>Ursache 

Dieses Problem kann auftreten, wenn der öffentliche Schlüssel des Stammzertifikats, den Sie hochgeladen haben, ein ungültiges Zeichen wie z.B. ein Leerzeichen enthält.

### <a name="solution"></a>Lösung

Stellen Sie sicher, dass die Daten im Zertifikat keine ungültigen Zeichen wie Zeilenumbrüche (Wagenrückläufe) enthalten. Der gesamte Wert sollte in einer langen Zeile sein. Der folgende Text ist ein Beispiel für das Zertifikat:

```text
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
/0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
-----END CERTIFICATE-----
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Fehler im Azure-Portal: Fehler beim Speichern des VPN-Gateways, und der Ressourcenname ist ungültig.

### <a name="symptom"></a>Symptom

Wenn Sie versuchen, die Änderungen für das VPN-Gateway im Azure-Portal zu speichern, erhalten Sie die folgende Fehlermeldung: 

**Fehler beim Speichern des Gateways für virtuelle Netzwerke &lt;*Gatewayname*&gt;. Der Ressourcenname &lt;*Name des hochzuladenden Zertifikats*&gt; ist ungültig.**

### <a name="cause"></a>Ursache

Dieses Problem tritt auf, da der Name des Zertifikats ein ungültiges Zeichen enthält, z.B. ein Leerzeichen. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Fehler im Azure-Portal: Dateidownloadfehler 503 des VPN-Pakets

### <a name="symptom"></a>Symptom

Wenn Sie versuchen, das VPN-Clientkonfigurationspaket herunterzuladen, erhalten Sie die folgende Fehlermeldung:

**Fehler beim Herunterladen der Datei. Fehlerdetails: Fehler 503. Der Server ist ausgelastet.**
 
### <a name="solution"></a>Lösung

Dieser Fehler kann durch ein vorübergehendes Netzwerkproblem verursacht werden. Versuchen Sie, das VPN-Paket nach ein paar Minuten erneut herunterzuladen.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure VPN Gateway-Upgrade: Alle P2S-Clients können keine Verbindung herstellen.

### <a name="cause"></a>Ursache

Wenn mehr als 50 Prozent der Lebensdauer des Zertifikats vorbei sind, wird das Zertifikat ausgetauscht.

### <a name="solution"></a>Lösung

Laden Sie das P2S-Paket für alle Clients erneut herunter, und stellen Sie es erneut bereit, um dieses Problem zu beheben.

## <a name="too-many-vpn-clients-connected-at-once"></a>Zu viele VPN-Clients gleichzeitig verbunden

Die maximale Anzahl von zulässigen Verbindungen wurde erreicht. Sie können sich die Gesamtzahl von verbundenen Clients im Azure-Portal anzeigen lassen.

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-Client kann nicht auf die Netzwerkdateifreigaben zugreifen

### <a name="symptom"></a>Symptom

Der VPN-Client wurde mit dem virtuellen Azure-Netzwerk verbunden. Jedoch kann der Client nicht auf die Netzwerkfreigaben zugreifen.

### <a name="cause"></a>Ursache

Das SMB-Protokoll wird für den Zugriff auf Dateifreigaben verwendet. Wenn die Verbindung initiiert wird, werden die Anmeldeinformationen für die Sitzung vom VPN-Client hinzugefügt, und der Fehler tritt auf. Nachdem die Verbindung hergestellt wurde, wird der Client gezwungen, die Cache-Anmeldeinformationen für die Kerberos-Authentifizierung zu verwenden. Durch diesen Prozess werden Abfragen an das Schlüsselverteilungscenter (ein Domänencontroller) initiiert, um ein Token abzurufen. Weil der Clients eine Verbindung aus dem Internet herstellt, kann er möglicherweise den Domänencontroller nicht erreichen. Aus diesem Grund kann der Client kein Failover von Kerberos nach NTLM ausführen. 

Der Client wird nur dann nach Anmeldeinformation gefragt, wenn er ein gültiges Zertifikat (mit SAN=UPN) besitzt, das von der Domäne ausgestellt wurde, mit der der Client verbunden ist. Der Client muss auch physisch mit dem Domänennetzwerk verbunden sein. In diesem Fall versucht der Client, das Zertifikat zu verwenden, und wendet sich an den Domänencontroller. Anschließend gibt das Schlüsselverteilungscenter den Fehler „KDC_ERR_C_PRINCIPAL_UNKNOWN“ zurück. Dies zwingt den Client, ein Failover nach NTLM auszuführen. 

### <a name="solution"></a>Lösung

Deaktivieren Sie das Zwischenspeichern von Anmeldeinformationen für die Domäne aus dem folgenden Registrierungsunterschlüssel, um das Problem zu umgehen: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Kann die Punkt-zu-Standort-VPN-Verbindung in Windows nach der Neuinstallation des VPN-Clients nicht finden

### <a name="symptom"></a>Symptom

Sie entfernen die Punkt-zu-Standort-VPN-Verbindung und installieren anschließend erneut den VPN-Client. In diesem Fall wurde die VPN-Verbindung nicht erfolgreich konfiguriert. Sie sehen keine VPN-Verbindung in den Einstellungen zu **Netzwerkverbindungen** in Windows.

### <a name="solution"></a>Lösung

Um das Problem zu beheben, löschen Sie die alten VPN-Clientconfigurationsdateien aus **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** , und führen Sie das VPN-Clientinstallationsprogramm dann erneut aus.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Auflösung des FQDN der Ressourcen in der lokalen Domäne durch den Point-to-Site-VPN-Client nicht möglich

### <a name="symptom"></a>Symptom

Wenn der Client mithilfe einer Point-to-Site-VPN-Verbindung eine Verbindung mit Azure herstellt, kann Azure den FQDN der Ressourcen in Ihrer lokalen Domäne nicht auflösen.

### <a name="cause"></a>Ursache

Der Point-to-Site-VPN-Client verwendet normalerweise Azure DNS-Server, die im virtuellen Azure-Netzwerk konfiguriert sind. Die Azure DNS-Server haben gegenüber den im Client konfigurierten lokalen DNS-Servern Vorrang (sofern die Metrik der Ethernet-Schnittstelle nicht geringer ist), sodass alle DNS-Abfragen an die Azure DNS-Server gesendet werden. Wenn die Azure DNS-Server nicht die Datensätze für die lokalen Ressourcen beinhalten, tritt ein Fehler bei der Abfrage auf.

### <a name="solution"></a>Lösung

Stellen Sie zur Behebung des Problems sicher, dass die Azure DNS-Server, die im virtuellen Azure-Netzwerk verwendet werden, die DNS-Datensätze für lokale Ressourcen auflösen können. Hierfür können Sie DNS-Weiterleitungen oder bedingte Weiterleitungen verwenden. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Herstellen einer Verbindung mit Azure-Ressourcen trotz bestehender Point-to-Site-VPN-Verbindung nicht möglich 

### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn der VPN-Client nicht die Routen vom Azure-VPN-Gateway erhält.

### <a name="solution"></a>Lösung

Um dieses Problem zu lösen, [setzen Sie das Azure-VPN-Gateway zurück](./reset-gateway.md). Wenn Sie sicherstellen möchten, dass die neuen Routen verwendet werden, müssen die Point-to-Site-VPN-Clients nach der erfolgreichen Konfiguration des Peerings in virtuellen Netzwerken erneut heruntergeladen werden.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Error: „Die Sperrfunktion konnte die Sperre nicht überprüfen, weil der Sperrserver offline war. (Fehler 0x80092013)“

### <a name="causes"></a>Ursachen
Diese Fehlermeldung tritt auf, wenn der Client nicht auf http://crl3.digicert.com/ssca-sha2-g1.crl und http://crl4.digicert.com/ssca-sha2-g1.crl zugreifen kann.  Für die Sperrprüfung ist Zugriff auf diese beiden Websites erforderlich.  Dieses Problem tritt in der Regel auf dem Client auf, für den der Proxyserver konfiguriert wurde. In einigen Umgebungen wird diese an der Edgefirewall verweigert, wenn die Anforderungen nicht über den Proxyserver erfolgen.

### <a name="solution"></a>Lösung

Überprüfen Sie die Proxyservereinstellungen, und stellen Sie sicher, dass der Client auf http://crl3.digicert.com/ssca-sha2-g1.crl und http://crl4.digicert.com/ssca-sha2-g1.crl zugreifen kann.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-Clientfehler: Die Verbindung wurde aufgrund einer auf dem RAS-/VPN-Server konfigurierten Richtlinie verhindert. (Fehler 812)

### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn der für die Authentifizierung des VPN-Clients verwendete RADIUS-Server falsche Einstellungen aufweist oder das Azure-Gateway den RADIUS-Server nicht erreicht.

### <a name="solution"></a>Lösung

Stellen Sie sicher, dass der RADIUS-Server ordnungsgemäß konfiguriert ist. Weitere Informationen finden Sie unter [Integration der RADIUS-Authentifizierung mit dem Azure AD Multi-Factor Authentication-Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>„Fehler 405“ beim Herunterladen des Stammzertifikats aus VPN Gateway

### <a name="cause"></a>Ursache

Das Stammzertifikat wurde nicht installiert. Das Stammzertifikat ist im **Zertifikatspeicher für vertrauenswürdige Anbieter** des Clients installiert.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-Clientfehler: Die Remoteverbindung wurde aufgrund von VPN-Tunnelfehlern nicht hergestellt. (Fehler 800) 

### <a name="cause"></a>Ursache

Der NIC-Treiber ist veraltet.

### <a name="solution"></a>Lösung

Aktualisieren Sie den NIC-Treiber:

1. Klicken Sie auf **Start**, geben Sie **Geräte-Manager** ein, und wählen Sie ihn in der Liste der Ergebnisse aus. Wenn Sie zur Eingabe eines Administratorkennworts oder einer Bestätigung aufgefordert werden, geben Sie das Kennwort oder eine entsprechende Bestätigung ein.
2. Suchen Sie in der Kategorie **Netzwerkadapter** die NIC, die Sie aktualisieren möchten.  
3. Doppelklicken Sie auf den Gerätenamen, und klicken Sie auf **Treiber aktualisieren** und anschließend auf **Automatisch nach aktualisierter Treibersoftware suchen**.
4. Wenn Windows keinen neuen Treiber findet, können Sie auf der Website des Geräteherstellers nach Treibern suchen und sie gemäß den Anweisungen installieren.
5. Starten Sie den Computer neu, und versuchen Sie erneut, eine Verbindung herzustellen.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN-Clientfehler: Wählen von VPN-Verbindung <VPN Connection Name>, Status: VPN-Plattform hat Verbindung nicht hergestellt

Möglicherweise wird auch der folgende Fehler in der Ereignisanzeige von RasClient angezeigt: "Der Benutzer <User> hat eine Verbindung mit dem Namen <VPN Connection Name> gewählt, die jedoch nicht hergestellt werden konnte. Der durch den Fehler zurückgegebene Ursachencode lautet 1460."

### <a name="cause"></a>Ursache

Azure VPN Client verfügt nicht über die aktivierte App-Berechtigung „Hintergrund-Apps“ in den App-Einstellungen für Windows.

### <a name="solution"></a>Lösung

1. Wechseln Sie in Windows zu „Einstellungen“ -> „Privatsphäre“ -> „Hintergrund-Apps“.
2. Ändern Sie die Einstellung für „Ausführung von Apps im Hintergrund zulassen“ in „Ein“.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Error: Dateidownloadfehler. Ziel-URI ist nicht angegeben.

### <a name="cause"></a>Ursache

Die Ursache hierfür ist ein falsches konfiguriertes Gateway.

### <a name="solution"></a>Lösung

Als Azure-VPN-Gatewaytyp muss „VPN“ und als VPN-Typ muss **RouteBased** festgelegt sein.

## <a name="vpn-package-installer-doesnt-complete"></a>Das Installationsprogramm für VPN-Pakete wird nicht ausgeführt.

### <a name="cause"></a>Ursache

Dieses Problem kann durch vorherige VPN-Clientinstallationen verursacht werden. 

### <a name="solution"></a>Lösung

Löschen Sie die alten VPN-Clientconfigurationsdateien aus **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** , und führen Sie das VPN-Clientinstallationsprogramm dann erneut aus. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Der VPN-Client wechselt nach einiger Zeit in den Ruhezustand oder Energiesparmodus.

### <a name="solution"></a>Lösung

Überprüfen Sie die Einstellungen für Standbymodus und Ruhezustand auf dem Computer, auf dem der VPN-Client ausgeführt wird.