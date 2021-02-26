---
title: Verwalten einzelner Sensoren
description: Erfahren Sie, wie Sie einzelne Sensoren verwalten. Dazu gehören das Verwalten von Aktivierungsdateien, Ausführen von Sicherungen und Aktualisieren eines eigenständigen Sensors.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b35851bae8db39392d10a302d5f1059ba3ace696
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508759"
---
# <a name="manage-individual-sensors"></a>Verwalten einzelner Sensoren

Dieser Artikel beschreibt, wie einzelne Sensoren verwaltet werden. Die Aufgaben umfassen das Verwalten von Aktivierungsdateien, Ausführen von Sicherungen und Aktualisieren eines eigenständigen Sensors.

Sie können auch bestimmte Sensorverwaltungsaufgaben über die lokale Verwaltungskonsole ausführen, in der mehrere Sensoren gleichzeitig verwaltet werden können.

Für das Integrieren und Registrieren von Sensoren verwenden Sie das Azure-Portal.

## <a name="manage-sensor-activation-files"></a>Verwalten von Sensoraktivierungsdateien

Die Sensoren wurden mit Azure Defender für IoT über das Azure-Portal integriert. Jeder Sensor wurde entweder als lokal verbundener Sensor oder als ein mit der Cloud verbundener Sensor integriert.

Auf jeden von Ihnen bereitgestellten Sensor wird eine eindeutige Aktivierungsdatei hochgeladen. Weitere Informationen dazu, wann und wie eine neue Datei verwendet wird, finden Sie unter [Hochladen neuer Aktivierungsdateien](#upload-new-activation-files). Wenn Sie die Datei nicht hochladen können, finden Sie weitere Informationen unter [Problembehandlung beim Hochladen der Aktivierungsdatei](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Informationen zu Aktivierungsdateien für lokal verbundene Sensoren

Lokal verbundene Sensoren sind einem Azure-Abonnement zugeordnet. Die Aktivierungsdatei für die lokal verbundenen Sensoren enthält ein Ablaufdatum. Einen Monat vor diesem Datum wird oben in der Sensorkonsole eine Warnmeldung angezeigt. Die Warnung wird so lange beibehalten, bis Sie die Aktivierungsdatei aktualisiert haben.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Screenshot der Systemeinstellungen":::

Sie können auch nach Ablauf der Aktivierungsdatei weiterhin mit Features von Azure Defender für IoT arbeiten. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Informationen zu Aktivierungsdateien für mit der Cloud verbundene Sensoren

Sensoren, die mit der Cloud verbunden sind, werden dem Defender für IoT-Hub zugeordnet. Diese Sensoren sind nicht durch Zeiträume für die Aktivierungsdatei einschränkt. Die Aktivierungsdatei für mit der Cloud verbundene Sensoren wird zur Gewährleistung einer Verbindung mit dem Defender für IoT-Hub verwendet.

### <a name="upload-new-activation-files"></a>Hochladen neuer Aktivierungsdateien

In folgenden Fällen müssen Sie möglicherweise eine neue Aktivierungsdatei für einen integrierten Sensor hochladen:

- Eine Aktivierungsdatei läuft auf einem lokal verbundenen Sensor ab. 

- Sie möchten in einem anderen Sensorverwaltungsmodus arbeiten. 

- Sie möchten einem mit der Cloud verbundenen Sensor einen neuen Defender für IoT-Hub zuweisen.

Zum Hinzufügen einer neuen Aktivierungsdateien gehen Sie folgendermaßen vor:

1. Navigieren Sie zur Seite **Sensorverwaltung**.

2. Wählen Sie den Sensor aus, für den Sie eine neue Aktivierungsdatei hochladen möchten.

3. Löschen Sie es.

4. Integrieren Sie den Sensor über die Seite **Onboarding** im neuen Modus oder in einen neuen Defender für IoT-Hub.

5. Laden Sie die Aktivierungsdatei von der Seite **Aktivierungsdatei herunterladen** herunter.

6. Speichern Sie die Datei .

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Herunterladen der Aktivierungsdatei vom Defender für IoT-Hub":::

7. Melden Sie sich bei der Sensorkonsole von Defender für IoT an.

8. Wählen Sie in der Sensorkonsole **Systemeinstellungen** > **Reaktivierung** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Auswahl „Reaktivierung“ auf dem Bildschirm „Systemeinstellungen“":::

9. Wählen Sie **Hochladen** und dann die gespeicherte Datei aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Hochladen der gespeicherten Datei":::

10. Wählen Sie **Aktivieren** aus.

### <a name="troubleshoot-activation-file-upload"></a>Problembehandlung beim Hochladen der Aktivierungsdatei

Sie erhalten eine Fehlermeldung, wenn die Aktivierungsdatei nicht hochgeladen werden konnte. Es können die folgenden Ereignisse aufgetreten sein:

- **Lokal verbundene Sensoren**: Die Aktivierungsdatei ist ungültig. Wenn die Datei ungültig ist, wechseln Sie zum Defender für IoT-Portal. Wählen Sie auf der Seite **Sensorverwaltung** den Sensor mit der ungültigen Datei aus, und laden Sie eine neue Aktivierungsdatei herunter.

- **Mit der Cloud verbundene Sensoren**: Der Sensor kann keine Verbindung mit dem Internet herstellen. Überprüfen Sie die Netzwerkkonfiguration des Sensors. Wenn der Sensor für den Zugriff auf das Internet eine Verbindung über einen Webproxy herstellen muss, vergewissern Sie sich auf dem Bildschirm **Sensornetzwerkkonfiguration**, dass der Proxyserver ordnungsgemäß konfiguriert ist. Stellen Sie sicher, dass in der Firewall und/oder im Proxy „\*.azure-devices.net:443“ zulässig ist. Wenn keine Platzhalter unterstützt werden oder Sie mehr Kontrolle wünschen, sollte der FQDN für den spezifischen Defender für IoT-Hub in der Firewall und/oder dem Proxy geöffnet werden. Ausführliche Informationen finden Sie unter [Referenz: IoT Hub-Endpunkte](../iot-hub/iot-hub-devguide-endpoints.md).  

- **Mit der Cloud verbundene Sensoren**: Die Aktivierungsdatei ist gültig, aber Defender für IoT hat sie abgelehnt. Wenn dieses Problem nicht behoben werden kann, können Sie eine andere Aktivierung von der Seite **Sensorverwaltung** im Defender für IoT-Portal herunterladen. Wenn das nicht funktioniert, wenden Sie sich an den Microsoft-Support.

## <a name="manage-certificates"></a>Verwalten von Zertifikaten

Nach der Sensorinstallation wird ein lokales selbstsigniertes Zertifikat generiert und für den Zugriff auf die Sensorwebanwendung verwendet. Bei der ersten Anmeldung beim Sensor werden Administratoren aufgefordert, ein SSL/TLS-Zertifikat anzugeben.  Weitere Informationen zur erstmaligen Einrichtung finden Sie unter [Anmelden und Aktivieren eines Sensors](how-to-activate-and-set-up-your-sensor.md).

Dieser Artikel enthält Informationen zum Aktualisieren von Zertifikaten, zum Arbeiten mit CLI-Befehlen für Zertifikate sowie zu unterstützten Zertifikaten und Zertifikatparametern.

### <a name="about-certificates"></a>Informationen zu Zertifikaten

Azure Defender für IoT verwendet SSL/TLS-Zertifikate für folgende Zwecke:

- Erfüllen spezifischer Zertifikat- und Verschlüsselungsanforderungen, die von Ihrer Organisation angefordert wurden, durch Hochladen des von der Zertifizierungsstelle signierten Zertifikats.

- Zulassen der Überprüfung zwischen der Verwaltungskonsole und verbundenen Sensoren sowie zwischen einer Verwaltungskonsole und einer Verwaltungskonsole für Hochverfügbarkeit. Die Überprüfung wird anhand einer Zertifikatssperrliste und des Ablaufdatums des Zertifikats ausgewertet. *Wenn bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen der Verwaltungskonsole und dem Sensor angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt*. Diese Option ist nach der Installation standardmäßig aktiviert.

 Weiterleitungsregeln von Drittanbietern, z. B. Warnungsinformationen, die an SYSLOG, Splunk oder ServiceNow gesendet werden, oder die Kommunikation mit Active Directory werden nicht überprüft.

#### <a name="ssl-certificates"></a>SSL-Zertifikate

Der Defender für IoT-Sensor und die lokale Verwaltungskonsole verwenden SSL- und TLS-Zertifikate für die folgenden Funktionen: 

 - Sichere Kommunikation zwischen Benutzern und der Webkonsole der Appliance. 
 
 - Sichere Kommunikation mit der REST-API im Sensor und der lokalen Verwaltungskonsole.
 
 - Sichere Kommunikation zwischen den Sensoren und einer lokalen Verwaltungskonsole. 

Nach der Installation generiert die Appliance ein lokales selbstsigniertes Zertifikat, um den vorläufigen Zugriff auf die Webkonsole zuzulassen. Enterprise SSL- und TLS-Zertifikate können mithilfe des Befehlszeilentools [`cyberx-xsense-certificate-import`](#cli-commands) installiert werden. 

 > [!NOTE]
 > Für Integrationen und Weiterleitungsregeln, bei denen es sich bei der Appliance um den Client und den Initiator der Sitzung handelt, werden spezifische Zertifikate verwendet, die nicht mit den Systemzertifikaten verknüpft sind.  
 >
 >In diesen Fällen werden die Zertifikate normalerweise vom Server empfangen, oder sie verwenden die asymmetrische Verschlüsselung, bei der ein bestimmtes Zertifikat zum Einrichten der Integration bereitgestellt wird.

Appliances können eindeutige Zertifikatdateien verwenden. Wenn Sie ein Zertifikat ersetzen müssen, das Sie hochgeladen haben:

- Ab Version 10.0 kann das Zertifikat aus dem Menü „Systemeinstellungen“ ersetzt werden.

- Für Versionen vor 10.0 kann das SSL-Zertifikat mithilfe des Befehlszeilentools ersetzt werden.

### <a name="update-certificates"></a>Aktualisieren von Zertifikaten

Sensoradministratoren können Zertifikate aktualisieren.

Zum Aktualisieren eines Zertifikats gehen Sie folgendermaßen vor:  

1. Wählen Sie **Systemeinstellungen** aus.

1. Wählen Sie **SSL/TLS-Zertifikate** aus.
1. Löschen oder bearbeiten Sie das Zertifikat, und fügen Sie ein neues Zertifikat hinzu.

    - Fügen Sie einen Zertifikatnamen hinzu.
    
    - Laden Sie eine CRT-Datei und eine Schlüsseldatei hoch, und geben Sie eine Passphrase ein.
    - Laden Sie bei Bedarf eine PEM-Datei hoch.

So ändern Sie die Überprüfungseinstellung

1. Aktivieren oder deaktivieren Sie mit dem Umschalter **Serverzertifikatüberprüfung aktivieren**.

1. Wählen Sie **Speichern** aus.

Wenn die Option aktiviert ist und bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen der Verwaltungskonsole und dem Sensor angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt.

### <a name="certificate-support"></a>Zertifikatunterstützung

Die folgenden Zertifikate werden unterstützt:

- Private und Enterprise Key-Infrastruktur (Private PKI)

- Public Key-Infrastruktur (Public PKI) 

- Lokal auf der Appliance generiert (lokal selbstsigniert) 

> [!IMPORTANT]
> Die Verwendung selbstsignierter Zertifikate wird nicht empfohlen. Diese Art der Verbindung ist nicht sicher und darf nur für Testumgebungen verwendet werden. Da der Besitzer des Zertifikats nicht überprüft und die Sicherheit Ihres Systems nicht aufrechterhalten werden kann, sollten selbstsignierte Zertifikate niemals für Produktionsnetzwerke verwendet werden.

### <a name="supported-ssl-certificates"></a>Unterstützte SSL-Zertifikate 

Die folgenden Parameter werden unterstützt. 

**CRT-Datei des Zertifikats**

- Die primäre Zertifikatsdatei für Ihren Domänennamen

- Signaturalgorithmus = SHA256RSA
- Signaturhashalgorithmus = SHA256
- Gültig von = gültiges Datum in der Vergangenheit
- Gültig bis = gültiges Datum in der Zukunft
- Öffentlicher Schlüssel = RSA 2.048 Bits (mindestens) oder 4.096 Bits
- CRL-Verteilungspunkt = URL zu CRL-Datei
- Antragsteller-CN = URL, kann ein Platzhalterzertifikat sein, z. B. Sensor.contoso.<span>com oder *.contoso.<span>com
- Land des Antragsstellers = definiert, z. B. USA
- Organisationseinheit des Antragsstellers = definiert, z. B. Contoso Labs
- Organisationseinheit des Antragsstellers = definiert, z. B. Contoso Inc.

**Schlüsseldatei**

- Die Schlüsseldatei, die beim Erstellen der CSR generiert wurde.

- RSA 2.048 Bits (mindestens) oder 4.096 Bits.

 > [!Note]
 > Bei einer Schlüssellänge von 4096 Bit gilt Folgendes:
 > - Der SSL-Handshake zu Beginn jeder Verbindung erfolgt langsamer.  
 > - Die CPU-Auslastung erhöht sich bei Handshakes. 

**Zertifikatkette**

- Die von Ihrer Zertifizierungsstelle bereitgestellte Zwischenversion der Zertifikatsdatei (sofern vorhanden)

- Das Zertifizierungsstellenzertifikat, das das Serverzertifikat ausgestellt hat, sollte sich an erster Stelle in der Datei befinden, gefolgt von allen anderen bis zum Stammzertifikat. 
- Kann Bag-Attribute enthalten.

**Passphrase**

- Ein Schlüssel wird unterstützt.

- Richten Sie ihn beim Importieren des Zertifikats ein.

Zertifikate mit anderen Parametern funktionieren möglicherweise, werden aber von Microsoft nicht unterstützt.

#### <a name="encryption-key-artifacts"></a>Verschlüsselungsschlüsselartefakte

**.pem: Zertifikatcontainerdatei**

PEM-Dateien (Privacy Enhanced Mail) wurden früher als allgemeiner Dateityp zum Schützen von E-Mails verwendet. Heutzutage werden PEM-Dateien mit Zertifikaten und X.509 ASN1-Schlüssel verwendet.  

Die Containerdatei ist in den RFCs 1421 bis 1424 definiert: ein Containerformat, das lediglich das öffentliche Zertifikat enthalten kann. Beispielsweise Apache-Installationen, Zertifikatdateien von Zertifizierungsstellen und ETC-, SSL- oder CERTS-Dateien. Dabei kann es sich um eine vollständige Zertifikatkette handeln, einschließlich öffentlichem Schlüssel, privatem Schlüssel und Stammzertifikaten.  

Außerdem kann eine CSR-Datei im PKCS10-Format codiert werden, die in PEM umgewandelt werden kann.

**.cert .cer .crt: Zertifikatcontainerdatei**

Eine Datei im `.pem`- oder `.der`-Format mit einer anderen Erweiterung. Die Datei wird in Windows-Explorer als Zertifikat erkannt. Die `.pem` -Datei wird in Windows-Explorer nicht erkannt.

**.key – Datei mit dem privaten Schlüssel**

Eine Schlüsseldatei weist das gleiche Format wie eine PEM-Datei auf, aber eine andere Erweiterung.

#### <a name="additional-commonly-available-key-artifacts"></a>Zusätzliche allgemein verfügbare Schlüsselartefakte

**.csr: Zertifikatsignieranforderung**.  

Diese Datei wird für die Übermittlung an Zertifizierungsstellen verwendet. Das tatsächliche Format ist PKCS10, das in RFC 2986 definiert ist, und kann einige oder alle Schlüsseldetails des angeforderten Zertifikats enthalten. Beispielsweise Antragsteller, Organisation und Status. Es handelt sich um den öffentlichen Schlüssel des Zertifikats, das von der Zertifizierungsstelle signiert wird und auf das ein Zertifikat zurückgegeben wird.  

Das zurückgegebene Zertifikat ist das öffentliche Zertifikat, das den öffentlichen Schlüssel, jedoch nicht den privaten Schlüssel enthält. 

**.pkcs12 .pfx .p12: Kennwortcontainer**. 

Ursprünglich durch RSA in den Public-Key Cryptography Standards (PKCS) definiert, wurde die PKCS12-Variante ursprünglich von Microsoft erweitert und später als RFC 7292 übermittelt.  

Dieses Containerformat erfordert ein Kennwort, das öffentliche sowie private Zertifikatpaare enthält. Im Unterschied zu `.pem` -Dateien ist dieser Container vollständig verschlüsselt.  

Mithilfe von OpenSSL können Sie die Datei in eine `.pem` -Datei mit öffentlichen und privaten Schlüsseln umwandeln: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**.der: Binär codierte PEM-Datei**.

Die Methode zum Codieren der ASN.1-Syntax in das binäre Format erfolgt über eine `.pem` -Datei, bei der es sich lediglich um eine Base64-codierte `.der`-Datei handelt. 

Mit OpenSSL können diese Dateien in `.pem`: `openssl x509 -inform der -in to-convert.der -out converted.pem` konvertiert werden.  

Diese Dateien werden unter Windows als Zertifikatdateien erkannt. Unter Windows werden Zertifikate standardmäßig als Dateien im `.der`-Format mit einer anderen Erweiterung exportiert.  

**.crl: Zertifikatsperrliste**.  
Zertifizierungsstellen können diese Dateien erstellen, um die Autorisierung von Zertifikaten vor deren Ablauf aufzuheben.
 
##### <a name="cli-commands"></a>CLI-Befehle

Importieren Sie Zertifikate mit dem CLI-Befehl `cyberx-xsense-certificate-import`. Zur Verwendung dieses Tools müssen Zertifikatdateien mithilfe von Tools wie WinSCP oder Wget auf das Gerät hochgeladen werden.

Der Befehl unterstützt die folgenden Eingabeflags:

- `-h`:  Zeigt die Syntax der Befehlszeilenhilfe.

- `--crt`:  Pfad zu einer Zertifikatdatei (CRT-Erweiterung)

- `--key`: \*.Schlüsseldatei. Die Schlüssellänge muss mindestens 2.048 Bits betragen.

- `--chain`:  Pfad zu einer Zertifikatskettendatei (optional).

- `--pass`:  Passphrase zum Verschlüsseln des Zertifikats (optional).

- `--passphrase-set`:  Standard = `False`, nicht verwendet. Legen Sie die Einstellung auf `True` fest, um die vorherige Passphrase zu verwenden, die mit dem vorherigen Zertifikat bereitgestellt wurde (optional).

Bei Verwendung des CLI-Befehls gilt Folgendes:

- Überprüfen Sie, ob die Zertifikatdateien auf der Appliance lesbar sind.

- Überprüfen Sie, ob der Domänenname und die IP-Adresse im Zertifikat der von der IT-Abteilung vorgesehenen Konfiguration entsprechen.

### <a name="use-openssl-to-manage-certificates"></a>Verwalten von Zertifikaten mithilfe von OpenSSL

Sie können Zertifikate mit den folgenden Befehlen verwalten:

| Beschreibung | CLI-Befehl |
|--|--|
| Generieren eines neuen privaten Schlüssels und einer Zertifikatsignieranforderung | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Generieren eines selbstsignierten Zertifikats | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Generieren einer Zertifikatsignieranforderung (Certificate Signing Request, CSR) für einen vorhandenen privaten Schlüssel | `openssl req -out CSR.csr -key privateKey.key -new` |
| Generieren einer Zertifikatsignieranforderung basierend auf einem vorhandenen Zertifikat | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Entfernen einer Passphrase aus einem privaten Schlüssel | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Wenn Sie die Informationen in einem Zertifikat, einer Zertifikatsignieranforderung oder einem privaten Schlüssel überprüfen möchten, verwenden Sie die folgenden Befehle:

| Beschreibung | CLI-Befehl |
|--|--|
| Überprüfen einer Zertifikatsignieranforderung (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Überprüfen eines privaten Schlüssels | `openssl rsa -in privateKey.key -check` |
| Überprüfen eines Zertifikats | `openssl x509 -in certificate.crt -text -noout`  |

Wenn in einer Fehlermeldung angezeigt wird, dass der private Schlüssel nicht mit dem Zertifikat übereinstimmt oder dass ein in einer Site installiertes Zertifikat nicht vertrauenswürdig ist, können Sie den Fehler mit den folgenden Befehlen beheben:

| Beschreibung | CLI-Befehl |
|--|--|
| Überprüfen eines MD5-Hash des öffentlichen Schlüssels, um sicherzustellen, dass er mit dem Inhalt einer Zertifikatsignieranforderung oder einem privaten Schlüssel übereinstimmt | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Führen Sie die folgenden Befehle aus, um Zertifikate und Schlüssel in unterschiedliche Formate zu konvertieren, sodass sie mit bestimmten Servertypen oder spezifischer Software kompatibel sind.

| Beschreibung | CLI-Befehl |
|--|--|
| Konvertieren einer DER-Datei (.crt .cer .der) in PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Konvertieren einer PEM-Datei in DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Konvertieren einer PKCS#12-Datei (.pfx .p12) mit einem privaten Schlüssel und Zertifikaten in PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Sie können `-nocerts` hinzufügen, um nur den privaten Schlüssel auszugeben, oder `-nokeys`, um nur die Zertifikate auszugeben. |
| Konvertieren einer PEM-Zertifikatdatei und eines privaten Schlüssels in PKCS#12 (.pfx .p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>Verbinden eines Sensors mit der Verwaltungskonsole

In diesem Abschnitt wird beschrieben, wie die Verbindung zwischen dem Sensor und der lokalen Verwaltungskonsole sichergestellt wird. Sie müssen dies tun, wenn Sie in einem Air-Gap-Netzwerk arbeiten und Geräte- und Warnungsinformationen vom Sensor an die Verwaltungskonsole senden möchten. Diese Verbindung ermöglicht der Verwaltungskonsole außerdem das Übertragen von Systemeinstellungen per Push an den Sensor und das Ausführen anderer Verwaltungsaufgaben auf dem Sensor.

Zum Herstellen einer Verbindung gehen Sie folgendermaßen vor:

1. Melden Sie sich bei der lokalen Verwaltungskonsole an.

2. Wählen Sie **Systemeinstellungen** aus.

3. Kopieren Sie im Abschnitt **Sensoreinrichtung – Verbindungszeichenfolge** die automatisch generierte Verbindungszeichenfolge.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Kopieren der Verbindungszeichenfolge auf dem Bildschirm"::: 

4. Melden Sie sich bei der Sensorkonsole an.

5. Wählen Sie im linken Bereich **Systemeinstellungen** aus.

6. Wählen Sie **Verwaltungskonsolenverbindung** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Screenshot des Dialogfelds „Verwaltungskonsolenverbindung“":::

7. Fügen Sie die **Verbindungszeichenfolge** im entsprechenden Feld ein, und wählen Sie **Verbinden** aus.

8. Weisen Sie in der lokalen Verwaltungskonsole im Fenster **Standortverwaltung** den Sensor einer Zone zu.

## <a name="change-the-name-of-a-sensor"></a>Ändern des Namens eines Sensors

Sie können den Namen Ihrer Sensorkonsole ändern. Der neue Name wird im Konsolenwebbrowser, in den verschiedenen Konsolenfenstern und in Problembehandlungsprotokollen angezeigt.

Die Vorgehensweise zum Ändern von Sensornamen ist bei lokal verbundenen Sensoren und mit der Cloud verbundenen Sensoren unterschiedlich. Der Standardname lautet **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Ändern des Namens eines lokal verbundenen Sensors

So ändern Sie den Namen

1. Wählen Sie unten im linken Bereich der Konsole die aktuelle Sensorbezeichnung aus.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Screenshot der Sensorbezeichnung":::

1. Geben Sie im Dialogfeld **Sensornamen bearbeiten** einen Namen ein.

1. Wählen Sie **Speichern** aus. Der neue Name wird übernommen.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Ändern des Namens eines mit der Cloud verbundenen Sensors

Wenn der Sensor als ein mit der Cloud verbundener Sensor registriert wurde, ist der Sensorname durch den während der Registrierung zugewiesenen Namen definiert. Der Name ist in der Aktivierungsdatei enthalten, die Sie bei der erstmaligen Anmeldung hochgeladen haben. Um den Namen des Sensors zu ändern, müssen Sie eine neue Aktivierungsdatei hochladen.

So ändern Sie den Namen

1. Navigieren Sie im Azure Defender für IoT-Portal zur Seite **Sensorverwaltung**.

1. Löschen Sie den Sensor aus dem Fenster **Sensorverwaltung**.

1. Führen Sie eine erneute Registrierung mit dem neuen Namen aus.

1. Laden Sie die neue Aktivierungsdatei herunter.

1. Melden Sie sich beim Sensor an, und laden Sie die neue Aktivierungsdatei hoch.

## <a name="update-the-sensor-network-configuration"></a>Aktualisieren der Sensornetzwerkkonfiguration

Die Sensornetzwerkkonfiguration wurde während der Sensorinstallation definiert. Sie können Konfigurationsparameter ändern. Sie können auch eine Proxykonfiguration einrichten.

Wenn Sie eine neue IP-Adresse erstellen, müssen Sie sich möglicherweise erneut anmelden.

Zum Ändern der Konfiguration gehen Sie folgendermaßen vor:

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie im Fenster **Systemeinstellungen** die Option **Netzwerk** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Konfigurieren Ihrer Netzwerkeinstellungen":::

3. Legen Sie die Parameter wie folgt fest:

    | Parameter | BESCHREIBUNG |
    |--|--|
    | IP-Adresse | Die IP-Adresse des Sensors |
    | Subnetzmaske | Die Maskenadresse |
    | Standardgateway | Die Standardgatewayadresse |
    | DNS | Die DNS-Serveradresse |
    | Hostname | Der Hostname des Sensors |
    | Proxy | Proxyhost- und Portname |

4. Wählen Sie **Speichern** aus.

## <a name="synchronize-time-zones-on-the-sensor"></a>Synchronisieren von Zeitzonen auf dem Sensor

Sie können die Uhrzeit und die Region des Sensors so konfigurieren, dass allen Benutzern dieselbe Uhrzeit und Region angezeigt werden.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Konfigurieren der Uhrzeit und Region":::

| Parameter | BESCHREIBUNG |
|--|--|
| Zeitzone | Die Zeitzonendefinition für:<br />– Warnungen<br />– Widgets für Trends und Statistiken<br />– Data Mining-Berichte<br />   – Risikobewertungsberichte<br />– Angriffsvektoren |
| Datumsformat | Wählen Sie eine der folgenden Formatoptionen aus:<br />– TT/MM/JJJJ hh:mm:ss<br />– MM/TT/JJJJ hh:mm:ss<br />– JJJJ/MM/TT hh:mm:ss |
| Datum und Uhrzeit | Zeigt das aktuelle Datum und die lokale Uhrzeit im ausgewählten Format an.<br />Wenn Ihr tatsächlicher Standort beispielsweise Amerika und New York ist, die Zeitzone aber auf Europa und Berlin festgelegt ist, wird die Uhrzeit entsprechend der Ortszeit in Berlin angezeigt. |

Zum Konfigurieren der Sensorzeit gehen Sie folgendermaßen vor:

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie im Fenster **Systemeinstellungen** die Option für **Zeit- und Regionseinstellungen** aus.

3. Legen Sie die Parameter fest, und wählen Sie **Speichern** aus.

## <a name="set-up-backup-and-restore-files"></a>Einrichten von Sicherungs- und Wiederherstellungsdateien

Eine Systemsicherung wird automatisch täglich um 3:00 Uhr morgens ausgeführt. Die Daten werden auf einem anderen Datenträger im Sensor gespeichert. Der Standardspeicherort ist `/var/cyberx/backups`.

Sie können diese Datei automatisch in das interne Netzwerk übertragen.

> [!NOTE]
> - Das Sicherungs- und Wiederherstellungsverfahren kann nur zwischen denselben Versionen durchgeführt werden.
> - In manchen Architekturen ist die Sicherung deaktiviert. Sie können sie in der Datei `/var/cyberx/properties/backup.properties` aktivieren.

Wenn Sie einen Sensor mithilfe der lokalen Verwaltungskonsole steuern, können Sie den Sicherungszeitplan des Sensors verwenden, um diese Sicherungen zu sammeln und in der Verwaltungskonsole oder auf einem externen Sicherungsserver zu speichern.

**Gesicherte Elemente:** Konfigurationen und Daten.

**Nicht gesicherte Elemente:** PCAP-Dateien und -Protokolle. Sie können PCAP-Dateien und -Protokolle manuell sichern und wiederherstellen.

Sensorsicherungsdateien werden automatisch im folgenden Format benannt: `<sensor name>-backup-version-<version>-<date>.tar`. z. B. `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Zum Konfigurieren der Sicherung gehen Sie folgendermaßen vor:

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-xsense-system-backup` ein.

So stellen Sie die neueste Sicherungsdatei wieder her

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-xsense-system-restore` ein.

So speichern Sie die Sicherung auf einem externen SMB-Server

1. Erstellen Sie auf dem externen SMB-Server einen freigegebenen Ordner.

    Rufen Sie den Ordnerpfad, den Benutzernamen und das Kennwort ab, die für den Zugriff auf den SMB-Server erforderlich sind.

2. Erstellen Sie auf dem Sensor ein Verzeichnis für die Sicherungen:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Bearbeiten Sie `fstab`: 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Bearbeiten und erstellen Sie Anmeldeinformationen zur Freigabe für den SMB-Server:

    `sudo nano /etc/samba/user` 

5. Hinzufügen:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Stellen Sie das Verzeichnis bereit:

    `sudo mount -a`

7. Konfigurieren Sie ein Sicherungsverzeichnis für den freigegebenen Ordner auf dem Defender für IoT-Sensor:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Wiederherstellen von Sensoren

Sie können Sicherungen über die Sensorkonsole und mithilfe der CLI wiederherstellen.

**Wiederherstellen über die Konsole:**

- Wählen Sie im Fenster **Systemeinstellungen** des Sensors die Option **Abbild wiederherstellen** aus.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Wiederherstellen des Abbilds durch Auswählen der Schaltfläche":::

In der Konsole werden Wiederherstellungsfehler angezeigt.

**Wiederherstellung mithilfe der CLI:**

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-management-system-restore` ein.

## <a name="update-a-standalone-sensor-version"></a>Aktualisieren einer eigenständigen Sensorversion

Im folgenden Verfahren wird beschrieben, wie Sie einen eigenständigen Sensor mithilfe der Sensorkonsole aktualisieren. Der Aktualisierungsvorgang dauert ungefähr 30 Minuten.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Wechseln Sie zu Defender für IoT.

3. Navigieren Sie zur Seite **Updates**.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Screenshot der Seite „Updates“ in Defender für IoT":::

4. Wählen Sie im Abschnitt **Sensoren** die Option **Herunterladen** aus, und speichern Sie die Datei.

5. Wählen Sie in der Seitenleiste der Sensorkonsole **Systemeinstellungen** aus.

6. Wählen Sie im Bereich **Versionsupgrade** die Option **Upgrade** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Screenshot des Upgradebereichs":::

7. Wählen Sie die Datei aus, die Sie von der Seite **Updates** in Defender für IoT heruntergeladen haben.

8. Der Aktualisierungsvorgang beginnt. Während dieses Zeitraums wird das System zweimal neu gestartet. Nach dem ersten Neustart (vor Abschluss des Aktualisierungsvorgangs) wird das System mit dem Anmeldefenster geöffnet. Nachdem Sie sich angemeldet haben, wird die Upgradeversion unten links auf der Seitenleiste angezeigt.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Screenshot der Upgradeversion, die nach der Anmeldung angezeigt wird":::

## <a name="forward-sensor-failure-alerts"></a>Weiterleiten von Warnungen für Sensorfehler 

Sie können Warnungen an Dritte weiterleiten, um Details zu Folgendem bereitzustellen:

- Getrennte Sensoren

- Remotesicherungsfehler

Diese Informationen werden gesendet, wenn Sie eine Weiterleitungsregel für Systembenachrichtigungen erstellen.

> [!NOTE]
> Administratoren können Systembenachrichtigungen senden.

Zum Senden von Benachrichtigungen gehen Sie folgendermaßen vor:

1. Melden Sie sich bei der lokalen Verwaltungskonsole an.
1. Wählen Sie im seitlichen Menü **Weiterleitung** aus.
1. Erstellen Sie eine Weiterleitungsregel.
1. Wählen Sie **Systembenachrichtigungen melden** aus.

Weitere Informationen zu Weiterleitungsregeln finden Sie unter [Weiterleiten von Warnungsinformationen](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Anpassen von Systemeigenschaften

Systemeigenschaften steuern verschiedene Vorgänge und Einstellungen im Sensor. Das Bearbeiten oder Ändern dieser Eigenschaften kann den Betrieb der Sensorkonsole beeinträchtigen.

Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/), bevor Sie Einstellungen ändern.

Zum Zugreifen auf Systemeigenschaften gehen Sie folgendermaßen vor:

1. Melden Sie sich bei der lokalen Verwaltungskonsole oder dem Sensor an.

2. Wählen Sie **Systemeinstellungen** aus.

3. Wählen Sie im Abschnitt **Allgemein** die Option **Systemeigenschaften** aus.

## <a name="see-also"></a>Weitere Informationen

[Forschung und Pakete zur Bedrohungsanalyse](how-to-work-with-threat-intelligence-packages.md)

[Verwalten von Sensoren über die Verwaltungskonsole](how-to-manage-sensors-from-the-on-premises-management-console.md)