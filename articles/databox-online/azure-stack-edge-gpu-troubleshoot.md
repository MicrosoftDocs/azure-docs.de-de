---
title: Behandeln von Problemen mit Azure Stack Edge Pro mit GPU im Azure-Portal | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Azure Stack Edge Pro-GPU-Probleme behandeln können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.author: alkohli
ms.openlocfilehash: d07d9dccb0aa273f79b251f2ffb4a920f3cac2e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447621"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Beheben von Problemen auf Ihrem Azure Stack Edge Pro-GPU-Gerät 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In diesem Artikel wird beschrieben, wie Sie Probleme auf Ihrem Azure Stack Edge Pro-GPU-Gerät behandeln können. 


## <a name="run-diagnostics"></a>Ausführen von Diagnosen

Zum Diagnostizieren und Behandeln von Gerätefehlern können Sie die Diagnosetests ausführen. Gehen Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts wie folgt vor, um Diagnosetests auszuführen:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung > Diagnosetests**. Wählen Sie den gewünschten Test aus, und klicken Sie auf **Test ausführen**. Daraufhin werden die Tests ausgeführt, um mögliche Probleme mit Ihren Netzwerk-, Geräte-, Webproxy-, Zeit- oder Cloudeinstellungen zu diagnostizieren. Sie werden benachrichtigt, dass auf dem Gerät Tests ausgeführt werden.

    ![Auswählen von Tests ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Nach Abschluss der Tests werden die Ergebnisse angezeigt. 

    ![Testergebnisse anzeigen](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Sollte ein Test nicht erfolgreich sein, wird eine URL für die empfohlene Maßnahme angezeigt. Wählen Sie die URL aus, um die empfohlene Maßnahme anzuzeigen.
 
    ![Überprüfen von Warnungen für Tests mit Fehlern](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Zusammenstellen des Unterstützungspaket

Ein Protokollpaket enthält alle relevanten Protokolle, die das Supportteam von Microsoft bei der Behandlung von Geräteproblemen unterstützen. Sie können ein Unterstützungspaket über die lokale Webbenutzeroberfläche generieren.

Gehen Sie wie folgt vor, um ein Unterstützungspaket zusammenzustellen: 

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung > Support**. Wählen Sie **Supportpaket erstellen** aus. Daraufhin wird das Unterstützungspaket zusammengestellt. Dieser Vorgang kann mehrere Minuten dauern.

    ![Auswählen von „Benutzer hinzufügen“](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Nachdem das Supportpaket erstellt wurde, wählen Sie **Supportpaket herunterladen** aus. Ein ZIP-Paket wird an den gewünschten Pfad heruntergeladen. Sie können das Paket entpacken und sich die Systemprotokolldateien ansehen.

    ![Auswählen von „Benutzer 2 hinzufügen“](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Sammeln erweiterter Sicherheitsprotokolle

Bei den erweiterten Sicherheitsprotokollen kann es sich um software- oder hardwarebezogene Protokolle zu Angriffen auf Ihr Azure Stack Edge Pro-Gerät handeln.

### <a name="software-intrusion-logs"></a>Protokolle zu softwarebezogenen Angriffen

Die Protokolle zu softwarebezogenen Angriffen oder standardmäßigen Firewallprotokolle werden für ein- und ausgehenden Datenverkehr gesammelt. 

- Wenn für das Gerät ab Werk ein Image erstellt wird, ist die standardmäßige Firewallprotokollierung aktiviert. Diese Protokolle werden standardmäßig im Supportpaket gebündelt, wenn Sie ein Supportpaket über die lokale Benutzeroberfläche oder die Windows PowerShell-Schnittstelle des Geräts erstellen.

- Wenn im Supportpaket nur die Firewallprotokolle benötigt werden, um jegliche Angriffe auf die Software (NW) auf dem Gerät zu überprüfen, verwenden Sie bei der Erstellung des Supportpakets die Option `-Include FirewallLog`. 

- Wenn keine spezielle Include-Option angegeben wird, ist das Firewallprotokoll standardmäßig im Supportpaket enthalten.

- Im Supportpaket befindet sich das Firewallprotokoll in der Datei `pfirewall.log` im Stammordner. Nachfolgend finden Sie ein Beispiel des Softwareangriffsprotokolls für das Azure Stack Edge Pro-Gerät. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Protokolle zu hardwarebezogenen Angriffen

Um jegliche Angriffe auf die Hardware des Geräts zu erkennen, werden derzeit alle Ereignisse am Gehäuse wie Öffnen und Schließen protokolliert. 

- Das Systemereignisprotokoll des Geräts wird mit dem Cmdlet `racadm` ausgelesen. Diese Ereignisse werden dann in einer `HWIntrusion.txt`-Datei nach gehäusebezogenen Ereignissen gefiltert.

- Um nur das Hardwareangriffsprotokoll in das Supportpaket aufzunehmen, verwenden Sie bei Erstellung des Supportpakets die Option `-Include HWSelLog`. 

- Wenn keine spezielle Include-Option angegeben wird, ist das Hardwareangriffsprotokoll standardmäßig im Supportpaket enthalten.

- Im Supportpaket befindet sich das Hardwareangriffsprotokoll in der Datei `HWIntrusion.txt` im Stammordner. Nachfolgend finden Sie ein Beispiel des Hardwareangriffsprotokolls für das Azure Stack Edge Pro-Gerät. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Behandeln von Problemen anhand von Protokollen

Alle Fehler, die während der Upload- und Aktualisierungsprozesse aufgetreten sind, sind in den entsprechenden Fehlerdateien enthalten.

1. Navigieren Sie zum Anzeigen der Fehlerdateien zu Ihrer Freigabe, und wählen Sie die Freigabe aus, um den Inhalt anzuzeigen. 


2. Wählen Sie den Ordner _Microsoft Data Box Edge_ aus. Dieser Ordner enthält zwei Unterordner:

    - Der Ordner „Upload“ enthält Protokolldateien für Uploadfehler.
    - Der Ordner „Refresh“ ist für Fehler bei der Aktualisierung vorgesehen.

    Hier sehen Sie eine exemplarische Protokolldatei für die Aktualisierung.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Wenn diese Datei einen Fehler enthält (im Beispiel hervorgehoben), notieren Sie sich den Fehlercode (in diesem Fall: 16001). Suchen Sie anhand dieses Fehlercodes in der folgenden Fehlerreferenz nach der entsprechenden Fehlerbeschreibung.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Verwenden von Fehlerlisten zur Problembehandlung

Die Fehlerlisten werden anhand identifizierter Szenarien zusammengestellt und können zur Selbstdiagnose und Problembehandlung dienen. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Hier sind die Fehler, die bei der Konfiguration von Azure Resource Manager für den Zugriff auf Ihr Gerät auftreten können. 

| **Problem/Fehler** |  **Lösung** | 
|------------|-----------------|
|Allgemeine Probleme|<li>[Vergewissern Sie sich, dass das Edgegerät ordnungsgemäß konfiguriert ist](#verify-the-device-is-configured-properly).<li> [Vergewissern Sie sich, dass der Client ordnungsgemäß konfiguriert ist.](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: Fehler beim Senden der Anforderung.<br>Zeile:1 Zeichen:1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|Dieser Fehler bedeutet, dass Ihr Azure Stack Edge Pro-Gerät nicht erreichbar oder nicht ordnungsgemäß konfiguriert ist. Überprüfen Sie, ob Edgegerät und Client ordnungsgemäß konfiguriert sind. Eine Anleitung finden Sie in der Zeile **Allgemeine Probleme** dieser Tabelle.|
|Dienst hat einen Fehler zurückgegeben. Weitere Details finden Sie in der InnerException. Die zugrunde liegende Verbindung wurde geschlossen: Es konnte keine Vertrauensstellung für den sicheren SSL/TLS-Kanal eingerichtet werden. |   Dieser Fehler ist wahrscheinlich darauf zurückzuführen, dass ein oder mehrere Schritte bei der Bereitstellung Ihres eigenen Zertifikats nicht einwandfrei erfolgt sind. [Hier](./azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates) finden Sie Anleitungen. |
|Operation returned an invalid status code 'BadRequest'. (Der Vorgang hat den ungültigen Statuscode „ServiceUnavailable“ zurückgegeben.) <br> Der Antwortstatuscode gibt keinen Erfolg an: 503 (Dienst nicht verfügbar). | Dieser Fehler kann auf eine der folgenden Bedingungen zurückzuführen sein.<li>ArmStsPool befindet sich im beendeten Zustand.</li><li>Die Azure Resource Manager- oder die Sicherheitstokendienst-Website ist ausgefallen.</li><li>Die Azure Resource Manager-Clusterressource ist nicht verfügbar.</li><br><strong>Hinweis:</strong> Ein Neustart des Geräts kann das Problem möglicherweise beheben, aber Sie sollten das Supportpaket zusammenstellen, damit Sie es weiter untersuchen können.|
|AADSTS50126: Ungültiger Benutzername oder ungültiges Kennwort.<br>Ablaufverfolgungs-ID: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>Korrelations-ID: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Timestamp: 2019-11-15 09:21:57Z: The remote server returned an error: (400) Bad Request (400 (Ungültige Anforderung)).<br>Zeile:1 Zeichen:1 |Dieser Fehler kann auf eine der folgenden Bedingungen zurückzuführen sein.<li>Bei ungültigem Benutzernamen und Kennwort prüfen Sie, ob der Kunde das Kennwort im Azure-Portal geändert hat, indem Sie den Schritten [hier](./azure-stack-edge-j-series-set-azure-resource-manager-password.md) folgen und dann das richtige Kennwort verwenden.<li>Bei einer ungültigen Mandanten-ID ist die Mandanten-ID eine feste GUID und muss auf `c0257de7-538f-415c-993a-1b87a031879d` festgelegt werden.</li>|
|connect-AzureRmAccount : AADSTS90056: Die Ressource ist deaktiviert oder nicht vorhanden. Überprüfen Sie den Code Ihrer App, um sicherzustellen, dass Sie die genaue Ressourcen-URL für die Ressource angegeben haben, auf die Sie zugreifen möchten.<br>Ablaufverfolgungs-ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>Korrelations-ID: 75c8ef5a-830e-48b5-b039-595a96488ff9 Zeitstempel: 2019-11-18 07:00:51Z: The remote server returned an error: (400) Fehlerhaft |Die im Befehl `Add-AzureRmEnvironment` verwendeten Ressourcenendpunkte sind falsch.|
|Endpunkte können nicht aus der Cloud abgerufen werden.<br>Stellen Sie sicher, dass Sie über eine Netzwerkverbindung verfügen. Fehlerdetail: HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005): Maximale Anzahl von Wiederholungen überschritten mit URL: /metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |Dieser Fehler tritt meist in einer Mac-/Linux-Umgebung auf und ist auf die folgenden Probleme zurückzuführen:<li>Ein Zertifikat im PEM-Format wurde nicht dem Python-Zertifikatsspeicher hinzugefügt.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Überprüfen, ob das Gerät ordnungsgemäß konfiguriert ist

1. Überprüfen Sie auf der lokalen Benutzeroberfläche, ob das Gerätenetzwerk ordnungsgemäß konfiguriert ist.

2. Vergewissern Sie sich, dass die Zertifikate für alle Endpunkte wie [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates) erwähnt aktualisiert werden.

3. Rufen Sie den Verwaltungs- und Anmeldeendpunkt für Azure Resource Manager auf der Seite **Gerät** der lokalen Benutzeroberfläche ab.

4. Vergewissern Sie sich, dass das Gerät in Azure aktiviert und registriert ist.


### <a name="verify-the-client-is-configured-properly"></a>Überprüfen, ob der Client ordnungsgemäß konfiguriert ist

1. Vergewissern Sie sich, dass die richtige PowerShell-Version wie [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client) erwähnt installiert ist.

2. Vergewissern Sie sich, dass die richtigen PowerShell-Module wie [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client) erwähnt installiert sind.

3. Überprüfen Sie, ob die Azure Resource Manager- und Anmeldeendpunkte erreichbar sind. Sie können versuchen, die Endpunkte zu pingen. Beispiel:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Wenn sie nicht erreichbar sind, fügen Sie DNS-/Hostdateieinträge wie [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution) erwähnt hinzu.
   
4. Überprüfen Sie, ob die Clientzertifikate wie [hier](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell) erwähnt installiert sind.

5. Wenn der Kunde PowerShell verwendet, sollten Sie die Debugvoreinstellung aktivieren, um detaillierte Meldungen anzuzeigen, indem Sie diesen PowerShell-Befehl ausführen. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Blob Storage auf dem Gerät 

Nachfolgend sind die Fehler im Zusammenhang mit Blob Storage auf dem Azure Stack Edge Pro-/Data Box Gateway-Gerät aufgeführt.

| **Problem/Fehler** |  **Lösung** | 
|--------------------|-----------------|
|Untergeordnete Ressourcen können nicht abgerufen werden. Der Wert eines der HTTP-Header weist nicht das richtige Format auf.| Wählen Sie im Menü **Bearbeiten** die Option **Target Azure Stack APIs** (Azure Stack-APIs als Ziel verwenden) aus. Starten Sie dann Azure Storage-Explorer neu.|
|getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ folgendem Pfad hinzugefügt wurde: `C:\Windows\System32\drivers\etc\hosts` unter Windows oder `/etc/hosts` unter Linux.|
|Untergeordnete Ressourcen können nicht abgerufen werden.<br> Details: selbstsigniertes Zertifikat |Importieren Sie das SSL-Zertifikat für Ihr Gerät in Azure Storage-Explorer: <ol><li>Laden Sie das Zertifikat aus dem Azure-Portal herunter. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Wählen Sie im Menü **Bearbeiten** die Option SSL-Zertifikate und dann **Zertifikate importieren** aus.</li></ol>|
|Der AzCopy-Befehl reagiert eine Minute lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `C:\Windows\System32\drivers\etc\hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert eine Minute lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importieren Sie das SSL-Zertifikat für Ihr Gerät in den Zertifikatsspeicher des Systems. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `/etc/hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location… The SSL connection could not be established`. |Importieren Sie das SSL-Zertifikat für Ihr Gerät in den Zertifikatsspeicher des Systems. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `/etc/hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt: `Error parsing source location… The SSL connection could not be established`|Importieren Sie das SSL-Zertifikat für Ihr Gerät in den Zertifikatsspeicher des Systems. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Der Wert eines der HTTP-Header weist nicht das richtige Format auf.|Die installierte Version der Microsoft Azure Storage-Bibliothek für Python wird von Data Box nicht unterstützt. Unterstützte Versionen finden Sie in den Azure Data Box-Blobspeicheranforderungen.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …| Legen Sie vor dem Ausführen von Python die Umgebungsvariable REQUESTS_CA_BUNDLE auf den Pfad der Base64-codierten SSL-Zertifikatsdatei fest. (Weitere Informationen zum [Herunterladen des Zertifikats](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).) Beispiel:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Alternativ können Sie das Zertifikat dem Zertifikatspeicher des Systems hinzufügen und dann diese Umgebungsvariable auf den Pfad dieses Speichers festlegen. Beispiel für Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|Bei der Verbindung ist eine Zeitüberschreitung aufgetreten.|Melden Sie sich beim Azure Stack Edge Pro-Gerät an, und prüfen Sie dann, ob es entsperrt ist. Bei jedem Neustart des Geräts bleibt es gesperrt, bis sich ein Benutzer anmeldet.|



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Beheben von Problemen mit der Geräteaktivierung](azure-stack-edge-gpu-troubleshoot-activation.md).