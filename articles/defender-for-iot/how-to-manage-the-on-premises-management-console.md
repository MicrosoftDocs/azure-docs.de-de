---
title: Verwalten der lokalen Verwaltungskonsole
description: Erfahren Sie mehr über die Möglichkeiten der lokalen Verwaltungskonsole wie Sicherung und Wiederherstellung, Definition des Hostnamens und Einrichtung eines Proxys für Sensoren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: d76db6830839902a46aaf6515f816fdcc36d0df5
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523939"
---
# <a name="manage-the-on-premises-management-console"></a>Verwalten der lokalen Verwaltungskonsole

In diesem Artikel werden die Möglichkeiten in der lokalen Verwaltungskonsole behandelt, wie z. B. Sicherung und Wiederherstellung, Herunterladen der Geräteaktivierungsdatei des Komitees, Aktualisieren von Zertifikaten und Einrichten eines Proxys für Sensoren.

Sie führen das Onboarding der lokalen Verwaltungskonsole über das Azure-Portal durch.

## <a name="upload-an-activation-file"></a>Hochladen einer Aktivierungsdatei

Wenn Sie sich zum ersten Mal anmelden, wird eine Aktivierungsdatei für die lokale Verwaltungskonsole heruntergeladen. Diese Datei enthält die aggregierten zugesicherten Geräte, die während des Onboardingprozesses definiert wurden. Die Liste enthält Sensoren, die mehreren Abonnements zugeordnet sind.

Nach der erstmaligen Aktivierung kann die Anzahl der überwachten Geräte die Anzahl der zugesicherten Geräte überschreiten, die während des Onboardings definiert wurden. Dies kann beispielsweise der Fall sein, wenn Sie weitere Sensoren mit der Verwaltungskonsole verbinden. Bei einer Abweichung zwischen der Anzahl überwachter Geräten und der Anzahl zugesicherter Geräten wird in der Verwaltungskonsole eine Warnung angezeigt. In diesem Fall sollten Sie eine neue Aktivierungsdatei hochladen.

So laden Sie eine Aktivierungsdatei hoch

1. Wechseln Sie zur Azure Defender für IoT-Seite **Preise**.
1. Wählen Sie die Registerkarte **Aktivierungsdatei für die Verwaltungskonsole herunterladen** aus. Die Aktivierungsdatei wird heruntergeladen.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Laden Sie die Aktivierungsdatei herunter.":::

1. Wählen Sie in der Verwaltungskonsole **Systemeinstellungen** aus.
1. Wählen Sie **Aktivierung** aus.
1. Wählen Sie **Datei auswählen** und dann die gespeicherte Datei aus.

## <a name="manage-certificates"></a>Verwalten von Zertifikaten

Nach der Installation der lokalen Verwaltungskonsole wird ein lokales selbstsigniertes Zertifikat generiert und für den Zugriff auf die Webanwendung der Verwaltungskonsole verwendet. Nachdem sich ein Administrator zum ersten Mal bei der Verwaltungskonsole angemeldet hat, wird er aufgefordert, ein SSL/TLS-Zertifikat bereitzustellen. Weitere Informationen zur erstmaligen Einrichtung finden Sie unter [Aktivieren und Einrichten Ihrer lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md).

Die folgenden Abschnitte enthalten Informationen zum Aktualisieren von Zertifikaten, Arbeiten mit CLI-Befehlen für Zertifikate sowie zu unterstützten Zertifikaten und Zertifikatparametern.

### <a name="about-certificates"></a>Informationen zu Zertifikaten

Azure Defender für IoT verwendet SSL- und TLS-Zertifikate für folgende Zwecke:

- Erfüllen spezifischer Zertifikat- und Verschlüsselungsanforderungen, die von Ihrer Organisation angefordert wurden, durch Hochladen des von der Zertifizierungsstelle signierten Zertifikats.

- Zulassen der Überprüfung zwischen der Verwaltungskonsole und verbundenen Sensoren sowie zwischen einer Verwaltungskonsole und einer Verwaltungskonsole für Hochverfügbarkeit. Die Überprüfung wird anhand einer Zertifikatssperrliste und des Ablaufdatums des Zertifikats ausgewertet. *Wenn bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen Verwaltungskonsole und Sensor angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt*. Diese Option ist nach der Installation standardmäßig aktiviert.

Weiterleitungsregeln von Drittanbietern werden nicht überprüft. Beispiele sind Warnungsinformationen, die an SYSLOG, Splunk oder ServiceNow gesendet werden, und die Kommunikation mit Active Directory.

#### <a name="ssl-certificates"></a>SSL-Zertifikate

Der Defender für IoT-Sensor und die lokale Verwaltungskonsole verwenden SSL- und TLS-Zertifikate für die folgenden Funktionen: 

 - Sichere Kommunikation zwischen Benutzern und der Webkonsole der Appliance 
 
 - Sichere Kommunikation mit der REST-API im Sensor und der lokalen Verwaltungskonsole
 
 - Sichere Kommunikation zwischen den Sensoren und einer lokalen Verwaltungskonsole 

Nach der Installation generiert die Appliance ein lokales selbstsigniertes Zertifikat, um den vorläufigen Zugriff auf die Webkonsole zuzulassen. SSL- und TLS-Zertifikate des Unternehmens können mithilfe des Befehlszeilentools [`cyberx-xsense-certificate-import`](#cli-commands) installiert werden. 

 > [!NOTE]
 > Für Integrationen und Weiterleitungsregeln, bei denen es sich bei der Appliance um den Client und Initiator der Sitzung handelt, werden spezifische Zertifikate verwendet, die nicht mit den Systemzertifikaten verknüpft sind.  
 >
 >In diesen Fällen werden die Zertifikate normalerweise vom Server empfangen, oder sie verwenden die asymmetrische Verschlüsselung, bei der ein bestimmtes Zertifikat zum Einrichten der Integration bereitgestellt wird. 

### <a name="update-certificates"></a>Aktualisieren von Zertifikaten

Administratoren der lokalen Verwaltungskonsole können Zertifikate aktualisieren.

So aktualisieren Sie ein Zertifikat  

1. Wählen Sie **Systemeinstellungen** aus.

1. Wählen Sie **SSL/TLS-Zertifikate** aus.
1. Löschen oder bearbeiten Sie das Zertifikat, und fügen Sie ein neues Zertifikat hinzu.
   
   - Fügen Sie einen Zertifikatnamen hinzu.
   
   - Laden Sie eine CRT-Datei und eine Schlüsseldatei hoch, und geben Sie eine Passphrase ein.
   - Laden Sie bei Bedarf eine PEM-Datei hoch.

So ändern Sie die Überprüfungseinstellung

1. Aktivieren oder deaktivieren Sie den Umschalter **Serverzertifikatüberprüfung aktivieren**.

1. Wählen Sie **Speichern** aus.

Wenn die Option aktiviert ist und bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen Verwaltungskonsole und Sensor angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt.

### <a name="certificate-support"></a>Zertifikatunterstützung

Die folgenden Zertifikate werden unterstützt:

- Private und Enterprise Key-Infrastruktur (Private PKI)
 
- Public Key-Infrastruktur (Public PKI) 

- Lokal auf der Appliance generiert (lokal selbstsigniert) 

  > [!IMPORTANT]
  > Die Verwendung selbstsignierter Zertifikate wird nicht empfohlen. Diese Art der Verbindung ist nicht sicher und darf nur für Testumgebungen verwendet werden. Da der Besitzer des Zertifikats nicht überprüft und die Sicherheit Ihres Systems nicht aufrechterhalten werden kann, sollten selbstsignierte Zertifikate niemals für Produktionsnetzwerke verwendet werden.

### <a name="supported-ssl-certificates"></a>Unterstützte SSL-Zertifikate 

Die folgenden Parameter werden unterstützt: 

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
- Organisationseinheit des Antragsstellers = definiert, z. B. Contoso Labs

**Schlüsseldatei**

- Die Schlüsseldatei, die beim Erstellen der Zertifikatsignieranforderung generiert wurde

- RSA 2.048 Bits (mindestens) oder 4.096 Bits

 > [!Note]
 > Bei einer Schlüssellänge von 4.096 Bit gilt Folgendes:
 > - Der SSL-Handshake zu Beginn jeder Verbindung erfolgt langsamer.  
 > - Die CPU-Auslastung erhöht sich bei Handshakes. 

**Zertifikatkette**

- Die von Ihrer Zertifizierungsstelle bereitgestellte Zwischenversion der Zertifikatsdatei (sofern vorhanden).

- Das Zertifikat der Zertifizierungsstelle, die das Serverzertifikat ausgestellt hat, muss sich an erster Stelle in der Datei befinden, gefolgt von allen anderen bis zum Stammzertifikat. 
- Die Kette kann Behälterattribute enthalten.

**Passphrase**

- Ein einzelner Schlüssel wird unterstützt.

- Richten Sie ihn beim Importieren des Zertifikats ein.

Zertifikate mit anderen Parametern funktionieren möglicherweise, werden aber von Microsoft nicht unterstützt.

#### <a name="encryption-key-artifacts"></a>Verschlüsselungsschlüsselartefakte

**.pem: Zertifikatcontainerdatei**

PEM-Dateien (Privacy Enhanced Mail) wurden früher als allgemeiner Dateityp zum Schützen von E-Mails verwendet. Heutzutage werden PEM-Dateien mit Zertifikaten und X.509 ASN1-Schlüssel verwendet.  

Die Containerdatei ist in den RFCs 1421 bis 1424 definiert: ein Containerformat, das lediglich das öffentliche Zertifikat enthalten kann, beispielsweise Apache-Installationen, Zertifikatdateien von Zertifizierungsstellen und ETC-, SSL- oder CERTS-Dateien. Dabei kann es sich um eine vollständige Zertifikatkette handeln, einschließlich öffentlichem Schlüssel, privatem Schlüssel und Stammzertifikaten.  

Außerdem kann eine CSR-Datei im PKCS10-Format codiert werden, die in PEM umgewandelt werden kann.

**.cert .cer .crt: Zertifikatcontainerdatei**

Eine Datei im `.pem`- oder `.der`-Format mit einer anderen Erweiterung. Die Datei wird in Windows-Explorer als Zertifikat erkannt. Die `.pem` -Datei wird in Windows-Explorer nicht erkannt.

**.key: Datei mit dem privaten Schlüssel**

Eine KEY-Datei hat das gleiche Format wie eine PEM-Datei, aber eine andere Erweiterung. 

#### <a name="additional-commonly-available-key-artifacts"></a>Zusätzliche allgemein verfügbare Schlüsselartefakte

**.csr: Zertifikatsignieranforderung**  

Diese Datei wird für die Übermittlung an Zertifizierungsstellen verwendet. Das tatsächliche Format ist PKCS10, das in RFC 2986 definiert ist, und kann einige oder alle Schlüsseldetails des angeforderten Zertifikats enthalten, z. B. Antragsteller, Organisation und Status. Es handelt sich um den öffentlichen Schlüssel des Zertifikats, das von der Zertifizierungsstelle signiert wird und auf das ein Zertifikat zurückgegeben wird.  

Das zurückgegebene Zertifikat ist das öffentliche Zertifikat, das den öffentlichen Schlüssel, jedoch nicht den privaten Schlüssel enthält. 

**.pkcs12, .pfx, .p12: Kennwortcontainer** 

Ursprünglich durch RSA in den Public-Key Cryptography Standards (PKCS) definiert, wurde die PKCS12-Variante zunächst von Microsoft erweitert und später als RFC 7292 eingereicht.  

Dieses Containerformat erfordert ein Kennwort, das öffentliche sowie private Zertifikatpaare enthält. Im Unterschied zu `.pem` -Dateien ist dieser Container vollständig verschlüsselt.  

Mithilfe von OpenSSL können Sie die Datei in eine `.pem` -Datei mit öffentlichen und privaten Schlüsseln umwandeln: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**.der: binär codierte PEM-Datei**

Die Methode zum Codieren der ASN.1-Syntax in das binäre Format erfolgt über eine `.pem` -Datei, bei der es sich lediglich um eine Base64-codierte `.der`-Datei handelt. 

Mit OpenSSL können diese Dateien in `.pem`: `openssl x509 -inform der -in to-convert.der -out converted.pem` konvertiert werden.  

Diese Dateien werden unter Windows als Zertifikatdateien erkannt. Unter Windows werden Zertifikate standardmäßig als Dateien im `.der`-Format mit einer anderen Erweiterung exportiert.

**.crl: Zertifikatssperrliste**  

Zertifizierungsstellen können diese Dateien erstellen, um die Autorisierung von Zertifikaten vor deren Ablauf aufzuheben. 

#### <a name="cli-commands"></a>CLI-Befehle

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
| Konvertieren einer DER-Datei (.crt, .cer, .der) in PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Konvertieren einer PEM-Datei in DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Konvertieren einer PKCS#12-Datei (.pfx, .p12) mit einem privaten Schlüssel und Zertifikaten in PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Sie können `-nocerts` hinzufügen, um nur den privaten Schlüssel auszugeben, oder `-nokeys`, um nur die Zertifikate auszugeben. |
| Konvertieren einer PEM-Zertifikatdatei und eines privaten Schlüssels in PKCS#12 (.pfx, .p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>Festlegen von Sicherungs- und Wiederherstellungseinstellungen

Die Sicherung des lokalen Verwaltungskonsolensystems erfolgt automatisch täglich. Die Daten werden auf einem anderen Datenträger gespeichert. Der Standardspeicherort ist `/var/cyberx/backups`. 

Sie können diese Datei automatisch in das interne Netzwerk übertragen. 

> [!NOTE]
> Sie können den Sicherungs- und Wiederherstellungsvorgang nur für dieselbe Version durchführen. 

So sichern Sie den Computer mit der lokalen Verwaltungskonsole 

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `sudo cyberx-management-backup -full` ein.

So stellen Sie die neueste Sicherungsdatei wieder her

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-management-system-restore` ein.

So speichern Sie die Sicherung auf einem externen SMB-Server

1. Erstellen Sie auf dem externen SMB-Server einen freigegebenen Ordner.  

   Rufen Sie die Angaben für Ordnerpfad, Benutzername und Kennwort ab, die für den Zugriff auf den SMB-Server erforderlich sind. 

2. Erstellen Sie in Defender für IoT ein Verzeichnis für die Sicherungen:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Bearbeiten Sie „fstab“:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Bearbeiten und erstellen Sie Anmeldeinformationen zur Freigabe für den SMB-Server: 

   - `sudo nano /etc/samba/user` 

5. Hinzufügen: 

   - `username=<user name>`

   - `password=<password>` 

6. Stellen Sie das Verzeichnis bereit: 

   - `sudo mount -a` 

7. Konfigurieren Sie in der lokalen Verwaltungskonsole von Defender für IoT ein Sicherungsverzeichnis für den freigegebenen Ordner:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Bearbeiten des Hostnamens

So bearbeiten Sie den im DNS-Server der Organisation konfigurierten Hostnamen der Verwaltungskonsole

1. Wählen Sie im linken Bereich der Verwaltungskonsole **Systemeinstellungen** aus.  

2. Wählen Sie im Bereich „Netzwerk“ der Konsole **Netzwerk** aus. 

3. Geben Sie den Hostnamen ein, der im DNS-Server der Organisation konfiguriert ist. 

4. Wählen Sie **Speichern** aus.

## <a name="define-vlan-names"></a>Festlegen von VLAN-Namen

VLAN-Namen werden nicht zwischen Sensor und Verwaltungskonsole synchronisiert. Sie müssen identische Namen für Komponenten festlegen.

Wählen Sie im Bereich „Netzwerk“ die Option **VLAN** aus, und fügen Sie den ermittelten VLAN-IDs Namen hinzu. Klicken Sie dann auf **Speichern**.

## <a name="define-a-proxy-to-sensors"></a>Festlegen eines Proxys für Sensoren

Erhöhen Sie die Systemsicherheit, indem Sie verhindern, dass sich Benutzer direkt am Sensor anmelden. Arbeiten Sie stattdessen mit Tunneln des Proxys, damit Benutzer mithilfe einer einzigen Firewallregel in der lokalen Verwaltungskonsole auf den Sensor zugreifen können. Diese Verbesserung schränkt die Möglichkeit eines nicht autorisierten Zugriffs auf die Netzwerkumgebung über den Sensor hinaus ein.

Arbeiten Sie mit einem Proxy in Umgebungen ohne direkte Konnektivität mit Sensoren.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Benutzer.":::

Mit dem folgenden Verfahren wird ein Sensor mit der lokalen Verwaltungskonsole verbunden und Tunneln für diese Konsole aktiviert:

1. Melden Sie sich mit administrativen Anmeldeinformationen bei der CLI der lokalen Verwaltungskonsole der Appliance an.

2. Geben Sie `sudo cyberx-management-tunnel-enable` ein, und drücken Sie dann die **EINGABETASTE**.

4. Geben Sie `--port 10000` ein, und drücken Sie dann die **EINGABETASTE**.

## <a name="adjust-system-properties"></a>Anpassen von Systemeigenschaften

Systemeigenschaften steuern verschiedene Vorgänge und Einstellungen in der Verwaltungskonsole. Das Bearbeiten oder Ändern dieser Eigenschaften kann den Betrieb der Verwaltungskonsole beeinträchtigen. Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com), ehe Sie Einstellungen ändern.

So greifen Sie auf Systemeigenschaften zu 

1. Melden Sie sich bei der lokalen Verwaltungskonsole oder dem Sensor an.

2. Wählen Sie **Systemeinstellungen** aus.

3. Wählen Sie im Abschnitt **Allgemein** die Option **Systemeigenschaften** aus.

## <a name="change-the-name-of-the-on-premises-management-console"></a>Ändern des Namens der lokalen Verwaltungskonsole

Sie können den Namen der lokalen Verwaltungskonsole ändern. Der neue Name wird im Konsolenwebbrowser, in verschiedenen Konsolenfenstern und Problembehandlungsprotokollen angezeigt. Der Standardname ist **Verwaltungskonsole**.

So ändern Sie den Namen

1. Wählen Sie unten im linken Bereich den aktuellen Namen aus.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Screenshot der Version der lokalen Verwaltungskonsole.":::

2. Geben Sie im Dialogfeld **Konfiguration der Verwaltungskonsole bearbeiten** den neuen Namen ein. Der Name darf maximal 25 Zeichen lang sein.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Screenshot der Bearbeitung der Konfiguration der Defender für IoT-Plattform.":::

3. Wählen Sie **Speichern** aus. Der neue Name wird übernommen.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Screenshot mit dem geänderten Namen der Konsole.":::

## <a name="password-recovery"></a>Kennwortwiederherstellung

Die Kennwortwiederherstellung für Ihre lokale Verwaltungskonsole ist an das Abonnement gebunden, zu dem das Gerät gehört. Sie können ein Kennwort nicht wiederherstellen, wenn Sie nicht wissen, zu welchem Abonnement ein Gerät gehört.

So setzen Sie Ihr Kennwort zurück:

1. Besuchen Sie die Anmeldeseite der lokalen Verwaltungskonsole.
1. Wählen Sie **Kennwortwiederherstellung** aus.
1. Kopieren Sie den eindeutigen Bezeichner.
1. Wechseln Sie zur Defender für IoT-Seite **Sites and Sensors** (Standorte und Sensoren), und wählen Sie die Registerkarte **Recover my password** (Kennwort wiederherstellen) aus.
1. Geben Sie den eindeutigen Bezeichner ein, und wählen Sie **Recover** (Wiederherstellen) aus. Die Aktivierungsdatei wird heruntergeladen.
1. Wechseln Sie zur Seite **Password Recovery** (Kennwortwiederherstellung), und laden Sie die Aktivierungsdatei hoch.
1. Wählen Sie **Weiter** aus.
 
   Sie erhalten nun Ihren Benutzernamen und ein neues vom System generiertes Kennwort.

> [!NOTE]
> Der Sensor ist mit dem Abonnement verknüpft, mit dem er ursprünglich verbunden war. Sie können das Kennwort nur wiederherstellen, wenn Sie dasselbe Abonnement verwenden, zu dem es gehört.

## <a name="update-the-software-version"></a>Aktualisieren der Softwareversion

Im folgenden Verfahren wird beschrieben, wie Sie die Softwareversion der lokalen Verwaltungskonsole aktualisieren. Der Aktualisierungsvorgang dauert ungefähr 30 Minuten.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

1. Wechseln Sie zu Defender für IoT.

1. Navigieren Sie zur Seite **Updates**.

1. Wählen Sie im Abschnitt für die lokale Verwaltungskonsole eine Version aus.

1. Wählen Sie **Herunterladen** aus, und speichern Sie die Datei.

1. Melden Sie sich bei der lokalen Verwaltungskonsole an, und wählen Sie im seitlichen Menü die Option **Systemeinstellungen** aus.

1. Wählen Sie im Bereich **Versionsupdate** die Option **Update** aus.

1. Wählen Sie die Datei aus, die Sie von der Seite **Updates** in Defender für IoT heruntergeladen haben.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Sensoren über die Verwaltungskonsole](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Verwalten einzelner Sensoren](how-to-manage-individual-sensors.md)
