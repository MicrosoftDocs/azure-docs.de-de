---
title: Split-Merge-Sicherheitskonfiguration
description: Richten Sie X409-Zertifikate für die Verschlüsselung mit dem Split/Merge-Dienst für elastische Skalierung ein.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 235efc550fd47d4244a5bf081c75d5e824a8e4b4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793431"
---
# <a name="split-merge-security-configuration"></a>Split-Merge-Sicherheitskonfiguration
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Zur Verwendung des Split-Merge-Diensts müssen Sie die Sicherheit ordnungsgemäß konfigurieren. Der Dienst ist Teil des Features „Elastic Scale“ von Azure SQL-Datenbank. Weitere Informationen finden Sie im [Lernprogramm zum Split-Merge-Dienst für die elastische Skalierung](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurieren von Zertifikaten

Zertifikate werden auf zwei Arten konfiguriert. 

1. [So konfigurieren Sie das TLS/SSL-Zertifikat](#to-configure-the-tlsssl-certificate)
2. [So konfigurieren Sie Clientzertifikate](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>So rufen Sie Zertifikate ab

Zertifikate können von öffentlichen Zertifizierungsstellen (CAs) oder vom [Windows-Zertifikatdienst](/windows/win32/seccrypto/certificate-services)abgerufen werden. Dies sind die bevorzugten Methoden für den Erhalt von Zertifikaten.

Wenn diese Optionen nicht verfügbar sind, können Sie **selbstsignierte Zertifikate** generieren.

## <a name="tools-to-generate-certificates"></a>Tools zum Erstellen von Zertifikaten

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>So führen Sie die Tools aus

* An einer Developer-Eingabeaufforderung für Visual Studio; weitere Einzelheiten finden Sie unter [Visual Studio-Eingabeaufforderung](/dotnet/framework/tools/developer-command-prompt-for-vs) 
  
    Falls installiert, gehen Sie zu:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* Abrufen des WDK unter [Windows 8.1: Herunterladen der Kits für die Windows-Hardwareentwicklung](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>So konfigurieren Sie das TLS/SSL-Zertifikat

Ein TLS/SSL-Zertifikat ist zum Verschlüsseln der Kommunikation und zum Authentifizieren des Servers erforderlich. Wählen Sie das zutreffende unter den folgenden drei Szenarien, und führen Sie alle zugehörigen Schritte aus:

### <a name="create-a-new-self-signed-certificate"></a>Erstellen eines neuen selbstsignierten Zertifikats

1. [Erstellen eines selbstsignierten Zertifikats](#create-a-self-signed-certificate)
2. [Erstellen einer PFX-Datei für ein selbstsigniertes TLS/SSL-Zertifikat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Hochladen des TLS/SSL-Zertifikats in den Clouddienst](#upload-tlsssl-certificate-to-cloud-service)
4. [Aktualisieren des TLS/SSL-Zertifikats in der Dienstkonfigurationsdatei](#update-tlsssl-certificate-in-service-configuration-file)
5. [Importieren der TLS/SSL-Zertifizierungsstelle](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>So verwenden Sie ein vorhandenes Zertifikat aus dem Zertifikatspeicher
1. [Exportieren des TLS/SSL-Zertifikats aus dem Zertifikatspeicher](#export-tlsssl-certificate-from-certificate-store)
2. [Hochladen des TLS/SSL-Zertifikats in den Clouddienst](#upload-tlsssl-certificate-to-cloud-service)
3. [Aktualisieren des TLS/SSL-Zertifikats in der Dienstkonfigurationsdatei](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>So verwenden Sie ein vorhandenes Zertifikat in einer PFX-Datei
1. [Hochladen des TLS/SSL-Zertifikats in den Clouddienst](#upload-tlsssl-certificate-to-cloud-service)
2. [Aktualisieren des TLS/SSL-Zertifikats in der Dienstkonfigurationsdatei](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>So konfigurieren Sie Clientzertifikate
Clientzertifikate sind erforderlich, um Anforderungen an den Dienst zu authentifizieren. Wählen Sie das zutreffende unter den folgenden drei Szenarien, und führen Sie alle zugehörigen Schritte aus:

### <a name="turn-off-client-certificates"></a>Deaktivieren von Clientzertifikaten
1. [Deaktivieren der zertifikatbasierten Clientauthentifizierung](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Ausstellen eines neuen selbstsignierten Clientzertifikats
1. [Erstellen einer selbstsignierten Zertifizierungsstelle](#create-a-self-signed-certification-authority)
2. [Hochladen eines CA-Zertifikats zum Clouddienst](#upload-ca-certificate-to-cloud-service)
3. [Aktualisieren des CA-Zertifikats in der Dienstkonfigurationsdatei](#update-ca-certificate-in-service-configuration-file)
4. [Ausstellen von Clientzertifikaten](#issue-client-certificates)
5. [Erstellen von PFX-Dateien für Clientzertifikate](#create-pfx-files-for-client-certificates)
6. [Importieren des Clientzertifikats](#import-client-certificate)
7. [Kopieren der Clientfingerabdrücke](#copy-client-certificate-thumbprints)
8. [Konfigurieren der zulässigen Clients in der Dienstkonfigurationsdatei](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>So verwenden Sie vorhandene Clientzertifikate
1. [Find CA Public Key](#find-ca-public-key)
2. [Hochladen eines CA-Zertifikats zum Clouddienst](#upload-ca-certificate-to-cloud-service)
3. [Aktualisieren des CA-Zertifikats in der Dienstkonfigurationsdatei](#update-ca-certificate-in-service-configuration-file)
4. [Kopieren der Clientfingerabdrücke](#copy-client-certificate-thumbprints)
5. [Konfigurieren der zulässigen Clients in der Dienstkonfigurationsdatei](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurieren der Sperrprüfung für Clientzertifikate](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Zulässige IP-Adressen
Der Zugriff auf die Dienstendpunkte kann auf bestimmte Bereiche von IP-Adressen beschränkt werden.

## <a name="to-configure-encryption-for-the-store"></a>So konfigurieren Sie die Verschlüsselung für den Speicher
Ein Zertifikat ist erforderlich, um die Anmeldeinformationen zu verschlüsseln, die im Metadatenspeicher gespeichert sind. Wählen Sie das zutreffende unter den folgenden drei Szenarien, und führen Sie alle zugehörigen Schritte aus:

### <a name="use-a-new-self-signed-certificate"></a>Verwenden eines neuen selbstsignierten Zertifikats
1. [Erstellen eines selbstsignierten Zertifikats](#create-a-self-signed-certificate)
2. [Erstellen einer PFX-Datei für ein selbstsigniertes Verschlüsselungszertifikat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Hochladen eines Verschlüsselungszertifikats zum Clouddienst](#upload-encryption-certificate-to-cloud-service)
4. [Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Verwenden eines vorhandenen Zertifikats aus dem Zertifikatspeicher
1. [Exportieren des Verschlüsselungszertifikats aus dem Zertifikatspeicher](#export-encryption-certificate-from-certificate-store)
2. [Hochladen eines Verschlüsselungszertifikats zum Clouddienst](#upload-encryption-certificate-to-cloud-service)
3. [Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Verwenden eines vorhandenen Zertifikats in einer PFX-Datei
1. [Hochladen eines Verschlüsselungszertifikats zum Clouddienst](#upload-encryption-certificate-to-cloud-service)
2. [Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Standardkonfiguration
Die Standardkonfiguration verweigert den gesamten Zugriff auf den HTTP-Endpunkt. Dies ist die empfohlene Einstellung, da die Anforderungen an diese Endpunkte vertrauliche Informationen wie Datenbankanmeldeinformationen enthalten können.
In der Standardkonfiguration ist der gesamte Zugriff auf den HTTP-Endpunkt zugelassen. Diese Einstellung kann weiter eingeschränkt werden.

### <a name="changing-the-configuration"></a>Ändern der Konfiguration
Die Gruppe der auf den Endpunkt angewendeten Zugriffssteuerungsregeln wird im Abschnitt **\<EndpointAcls>** der **Dienstkonfigurationsdatei** konfiguriert.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Die Regeln einer Zugriffssteuerungsgruppe werden im Abschnitt \<AccessControl name=""> der Dienstkonfigurationsdatei konfiguriert. 

Das Format wird in der Dokumentation über Netzwerk-Zugriffsteuerungslisten erläutert.
Damit nur IP-Adressen im Bereich von 100.100.0.0 bis 100.100.255.255 auf den HTTPS-Endpunkt zugreifen können, würden die Regeln z. B. wie folgt aussehen:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Dienstverweigerungsschutz
Es werden zwei unterschiedliche Mechanismen zum Erkennen und Verhindern von Dienstverweigerungsangriffen unterstützt:

* Beschränken der Anzahl gleichzeitiger Anforderungen pro Remotehost (standardmäßig deaktiviert)
* Beschränken der Zugriffsrate pro Remotehost (standardmäßig aktiviert)

Sie basieren auf den Funktionen, die unter der dynamischen IP-Sicherheit in IIS eingehender dokumentiert sind. Wenn Sie diese Konfiguration ändern, beachten Sie die folgenden Faktoren:

* Das Verhalten der Proxys und Netzwerkadressübersetzungs-Geräte über die Remotehostinformationen
* Jede Anforderung an eine Ressource in der Webrolle wird berücksichtigt (z.B. das Laden von Skripts, Images usw.)

## <a name="restricting-number-of-concurrent-accesses"></a>Einschränken der Anzahl gleichzeitiger Zugriffe
Dieses Verhalten wird über folgende Einstellungen konfiguriert:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Ändern Sie DynamicIpRestrictionDenyByConcurrentRequests in „true“, um diesen Schutz zu aktivieren.

## <a name="restricting-rate-of-access"></a>Beschränken der Zugriffsrate
Dieses Verhalten wird über folgende Einstellungen konfiguriert:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurieren der Antwort auf eine verweigerte Anforderung
Die folgende Einstellung konfiguriert die Antwort auf eine verweigerte Anforderung:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Weitere unterstützte Werte entnehmen Sie der Dokumentation für dynamische IP-Sicherheit in IIS.

## <a name="operations-for-configuring-service-certificates"></a>Vorgänge für das Konfigurieren von Dienstzertifikaten
Dieses Thema dient nur zu Informationszwecken. Führen Sie die Konfigurationsschritte aus, die in den folgenden Themen beschrieben sind:

* Konfigurieren des TLS/SSL-Zertifikats
* Konfigurieren von Clientzertifikaten

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats
Führen Sie folgende Befehle aus:

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

Zur Anpassung:

* -n mit der Dienst-URL. Platzhalter ("CN=*.cloudapp.net") und alternative Namen ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") werden unterstützt.
* -e mit dem Ablaufdatum des Zertifikats. Erstellen Sie ein sicheres Kennwort, und geben Sie es bei Aufforderung an.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Erstellen einer PFX-Datei für ein selbstsigniertes TLS/SSL-Zertifikat
Führen Sie folgende Befehle aus:

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Geben Sie das Kennwort ein, und exportieren Sie dann das Zertifikat mit den folgenden Optionen:

* Ja, privaten Schlüssel exportieren
* Alle erweiterten Eigenschaften exportieren

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Exportieren des TLS/SSL-Zertifikats aus dem Zertifikatspeicher
* Suchen des Zertifikats
* Klicken Sie auf "Aktionen -> Alle Aufgaben -> Exportieren..."
* Exportieren Sie das Zertifikats mit den folgenden Optionen in eine.PFX-Datei:
  * Ja, privaten Schlüssel exportieren
  * Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen *Alle erweiterten Eigenschaften exportieren

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Hochladen des TLS/SSL-Zertifikats in den Clouddienst
Laden Sie das Zertifikat mit der vorhandenen oder generierten PFX-Datei mit dem TLS-Schlüsselpaar hoch:

* Geben Sie das Kennwort zum Schützen der Informationen des private Schlüssels ein.

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Aktualisieren des TLS/SSL-Zertifikats in der Dienstkonfigurationsdatei
Aktualisieren Sie den Fingerabdruckwert der folgenden Einstellung in der Dienstkonfigurationsdatei mit dem Fingerabdruck des in den Clouddienst hochgeladenen Zertifikats:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>Importieren der TLS/SSL-Zertifizierungsstelle
Führen Sie die folgenden Schritten für alle Konto/Computer aus, die mit dem Dienst kommunizieren werden:

* Doppelklicken Sie auf die.CER-Datei in Windows-Explorer.
* Klicken Sie im Dialogfeld "Zertifikat" auf "Zertifikat installieren...".
* Importieren Sie das Zertifikat in den Speicher für vertrauenswürdige Stammzertifizierungsstellen.

## <a name="turn-off-client-certificate-based-authentication"></a>Deaktivieren der zertifikatbasierten Clientauthentifizierung
Es wird nur die zertifikatbasierte Clientauthentifizierung unterstützt, und deren Deaktivierung lässt den öffentlichen Zugriff auf die Dienstendpunkte zu, sofern nicht andere Mechanismen eingerichtet wurden (z.B. Microsoft Azure Virtual Network).

Ändern Sie diese Einstellungen in der Dienstkonfigurationsdatei in „false“, um die Funktion zu deaktivieren:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Kopieren Sie dann denselben Fingerabdruck wie für das TLS/SSL-Zertifikat in die Einstellung des CA-Zertifikats:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Erstellen einer selbstsignierten Zertifizierungsstelle
Führen Sie die folgenden Schritte aus, um ein selbstsigniertes Zertifikat zu erstellen, das als Zertifizierungsstelle fungiert:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

Zur Anpassung:

* -e mit dem Ablaufdatum des Zertifikats

## <a name="find-ca-public-key"></a>Suchen des öffentlichen Schlüssels der Zertifizierungsstelle
Alle Clientzertifikate müssen von einer Zertifizierungsstelle ausgestellt werden, der der Dienst vertraut. Suchen Sie den öffentlichen Schlüssel der Zertifizierungsstelle, die die Clientzertifikate für die Authentifizierung ausgestellt hat, um sie in den Clouddienst hochzuladen.

Wenn die Datei mit dem öffentlichen Schlüssel nicht verfügbar ist, exportieren Sie sie aus dem Zertifikatspeicher:

* Suchen des Zertifikats
  * Suchen Sie ein Clientzertifikat, das von derselben Zertifizierungsstelle ausgestellt wurde.
* Doppelklicken Sie auf das Zertifikat.
* Wählen Sie die Registerkarte "Zertifizierungspfad" im Dialogfeld "Zertifikat" aus.
* Doppelklicken Sie auf den CA-Eintrag im Pfad.
* Notieren Sie die Zertifikateigenschaften.
* Schließen Sie das Dialogfeld **Zertifikat** .
* Suchen des Zertifikats
  * Suchen Sie nach der oben aufgeführten Zertifizierungsstelle.
* Klicken Sie auf "Aktionen -> Alle Aufgaben -> Exportieren..."
* Exportieren Sie das Zertifikat mit den folgenden Optionen in eine .CER-Datei:
  * **Nein, privaten Schlüssel nicht exportieren**
  * Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen
  * Alle erweiterten Eigenschaften exportieren

## <a name="upload-ca-certificate-to-cloud-service"></a>Hochladen eines CA-Zertifikats zum Clouddienst
Laden Sie das Zertifikat mit der vorhandenen oder generierten .CER-Datei mit dem öffentlichen Schlüssel der Zertifizierungsstelle hoch.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Aktualisieren des CA-Zertifikats in der Dienstkonfigurationsdatei
Aktualisieren Sie den Fingerabdruckwert der folgenden Einstellung in der Dienstkonfigurationsdatei mit dem Fingerabdruck des in den Clouddienst hochgeladenen Zertifikats:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Aktualisieren Sie den Wert der folgenden Einstellung mit dem gleichen Fingerabdruck:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Ausstellen von Clientzertifikaten
Für jede Person, die für den Zugriff auf den Dienst autorisiert ist, sollte ein Clientzertifikat für ihre exklusive Nutzung ausgestellt werden, und sie sollte ein eigenes sicheres Kennwort zum Schutz des privaten Schlüssels einrichten. 

Die folgenden Schritte müssen auf demselben Computer ausgeführt werden, auf dem das selbstsignierte Zertifikat der Zertifizierungsstelle generiert und gespeichert wurde:

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Anpassen:

* -n, mit einer ID für den Client, der mit diesem Zertifikat authentifiziert wird
* -e mit dem Ablaufdatum des Zertifikats
* MyID.pvk und MyID.cer mit eindeutigen Dateinamen für dieses Clientzertifikat

Dieser Befehl fordert dazu auf, ein Kennwort zu erstellen und anschließend einmal zu verwenden. Verwenden Sie ein sicheres Kennwort.

## <a name="create-pfx-files-for-client-certificates"></a>Erstellen von PFX-Dateien für Clientzertifikate
Führen Sie für jedes generiertes Clientzertifikat folgende Befehle aus:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Anpassen:

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

Geben Sie das Kennwort ein, und exportieren Sie dann das Zertifikat mit den folgenden Optionen:

* Ja, privaten Schlüssel exportieren
* Alle erweiterten Eigenschaften exportieren
* Die Person, für die dieses Zertifikat ausgestellt wird, muss das Exportkennwort auswählen.

## <a name="import-client-certificate"></a>Importieren des Clientzertifikats
Jede Person, für die ein Clientzertifikat ausgestellt wurde, sollte das Schlüsselpaar auf den Computer importieren, den sie für die Kommunikation mit dem Dienst verwenden möchte:

* Doppelklicken Sie auf die.PFX-Datei in Windows-Explorer.
* Importieren Sie das Zertifikat mit mindestens dieser Option in den persönlichen Zertifikatspeicher:
  * "Alle erweiterten Eigenschaften mit einbeziehen" aktiviert

## <a name="copy-client-certificate-thumbprints"></a>Kopieren der Clientfingerabdrücke
Jede Person, für die ein Clientzertifikat ausgegeben wurde, muss folgende Schritte zum Abrufen des Fingerabdrucks ihres Zertifikats ausführen, das der Dienstkonfigurationsdatei hinzugefügt wird:

* Führen Sie "certmgr.exe" aus.
* Wählen Sie die Registerkarte "Persönlich".
* Doppelklicken Sie auf das Clientzertifikat, das für die Authentifizierung verwendet werden soll.
* Wählen Sie im daraufhin geöffneten Dialogfeld "Zertifikat" die Registerkarte "Details".
* Stellen Sie sicher, dass unter "Anzeigen" die Option "Alle" angezeigt wird.
* Wählen Sie das Feld mit dem Namen "Fingerabdruck" in der Liste aus.
* Kopieren Sie den Wert des Fingerabdrucks.
  * Löschen Sie nicht sichtbare Unicode-Zeichen vor der ersten Ziffer.
  * Löschen Sie alle Leerzeichen.

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurieren der zulässigen Clients in der Dienstkonfigurationsdatei
Aktualisieren Sie den Wert für die folgende Einstellung in der Dienstkonfigurationsdatei mit einer kommagetrennten Liste von Fingerabdrücken der Clientzertifikate, die Zugriff auf den Dienst haben:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurieren der Sperrprüfung für Clientzertifikate
In der Standardeinstellung wird der Sperrstatus des Clientzertifikats bei der Zertifizierungsstelle nicht überprüft. Um die Überprüfungen zu aktivieren (falls die Zertifizierungsstelle, von der die Clientzertifikate ausgestellt wurden, derartige Überprüfungen unterstützt), ändern Sie die folgende Einstellung mit einem der Werte, die in der X509RevocationMode-Enumeration definiert sind:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Erstellen einer PFX-Datei für selbstsignierte Verschlüsselungszertifikate
Führen Sie für ein Verschlüsselungszertifikat Folgendes aus:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Anpassen:

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Geben Sie das Kennwort ein, und exportieren Sie dann das Zertifikat mit den folgenden Optionen:

* Ja, privaten Schlüssel exportieren
* Alle erweiterten Eigenschaften exportieren
* Sie benötigen das Kennwort zum Hochladen des Zertifikats in den Cloud-Dienst.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportieren des Verschlüsselungszertifikats aus dem Zertifikatspeicher
* Suchen des Zertifikats
* Klicken Sie auf "Aktionen -> Alle Aufgaben -> Exportieren..."
* Exportieren Sie das Zertifikats mit den folgenden Optionen in eine.PFX-Datei: 
  * Ja, privaten Schlüssel exportieren
  * Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen 
* Alle erweiterten Eigenschaften exportieren

## <a name="upload-encryption-certificate-to-cloud-service"></a>Hochladen eines Verschlüsselungszertifikats zum Clouddienst
Laden Sie das Zertifikat mit der vorhandenen oder generierten .PFX-Datei mit dem Verschlüsselungsschlüsselpaar hoch:

* Geben Sie das Kennwort zum Schützen der Informationen des private Schlüssels ein.

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei
Aktualisieren Sie den Fingerabdruckwert der folgenden Einstellungen in der Dienstkonfigurationsdatei mit dem Fingerabdruck des in den Clouddienst hochgeladenen Zertifikats:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Allgemeine Zertifikatvorgänge
* Konfigurieren des TLS/SSL-Zertifikats
* Konfigurieren von Clientzertifikaten

## <a name="find-certificate"></a>Suchen des Zertifikats
Folgen Sie diesen Schritten:

1. Führen Sie "mmc.exe" aus.
2. Datei -> Snap-In hinzufügen/entfernen…
3. Wählen Sie **Zertifikate** aus.
4. Klicken Sie auf **Hinzufügen** .
5. Wählen Sie den Speicherort des Zertifikats aus.
6. Klicken Sie auf **Fertig stellen** .
7. Klicken Sie auf **OK** .
8. Erweitern Sie **Zertifikate** .
9. Erweitern Sie den Knoten des Zertifikatspeichers.
10. Erweitern Sie den untergeordneten Knoten "Zertifikat".
11. Wählen Sie ein Zertifikat aus der Liste aus.

## <a name="export-certificate"></a>Exportieren des Zertifikats
Gehen Sie im **Zertifikatexport-Assistenten** wie folgt vor:

1. Klicken Sie auf **Weiter** .
2. Wählen Sie Ja **und** dann **Privaten Schlüssel exportieren** aus.
3. Klicken Sie auf **Weiter** .
4. Wählen Sie das gewünschte Ausgabeformat für die Datei aus.
5. Aktivieren Sie die gewünschten Optionen.
6. Aktivieren Sie **Kennwort** .
7. Geben Sie ein sicheres Kennwort ein, und bestätigen Sie es.
8. Klicken Sie auf **Weiter** .
9. Geben einen Dateinamen für das Speichern des Zertifikats ein, oder suchen Sie danach (verwenden Sie die Dateierweiterung .PFX).
10. Klicken Sie auf **Weiter** .
11. Klicken Sie auf **Fertig stellen** .
12. Klicken Sie auf **OK** .

## <a name="import-certificate"></a>Importieren des Zertifikats
Gehen Sie im Zertifikatimport-Assistenten wie folgt vor:

1. Wählen Sie den Speicherort aus.
   
   * Wählen Sie **Aktueller Benutzer** aus, wenn nur Prozesse, die unter dem aktuellen Benutzer ausgeführt werden, auf den Dienst zugreifen.
   * Wählen Sie **Lokaler Computer** aus, wenn andere Prozesse auf diesem Computer auf den Dienst zugreifen.
2. Klicken Sie auf **Weiter** .
3. Wenn Sie Daten aus einer Datei importieren, bestätigen Sie den Dateipfad.
4. Beim Importieren einer.PFX-Datei:
   1. Geben Sie das Kennwort ein, das den privaten Schlüssel schützt.
   2. Wählen Sie die Importoptionen aus.
5. Wählen Sie "Alle Zertifikate in folgendem Speicher speichern" aus.
6. Klicken Sie auf **Durchsuchen** .
7. Wählen Sie den gewünschten Speicher aus.
8. Klicken Sie auf **Fertig stellen** .
   
   * Wenn der Speicher vertrauenswürdiger Stammzertifizierungsstellen ausgewählt wurde, klicken Sie auf **Ja** .
9. Klicken Sie in allen Dialogfeldern auf **OK** .

## <a name="upload-certificate"></a>Hochladen des Zertifikats
Gehen Sie im [Azure-Portal](https://portal.azure.com/) wie folgt vor:

1. Wählen Sie **Cloud-Dienste** .
2. Wählen Sie den Clouddienst aus.
3. Klicken Sie im oberen Menü auf **Zertifikate** .
4. Klicken Sie auf der unteren Leiste auf **Hochladen** .
5. Wählen Sie die Zertifikatdatei aus.
6. Wenn es sich um eine PFX-Datei handelt, geben Sie das Kennwort für den privaten Schlüssel ein.
7. Nach Abschluss kopieren Sie den Zertifikatfingerabdruck aus dem neuen Eintrag in der Liste.

## <a name="other-security-considerations"></a>Weitere Überlegungen zur Sicherheit
Die in diesem Dokument beschriebenen TLS-Einstellungen verschlüsseln die Kommunikation zwischen dem Dienst und den zugehörigen Clients, wenn der HTTPS-Endpunkt verwendet wird. Dies ist wichtig, da die Anmeldeinformationen für den Datenbankzugriff und andere vertrauliche Informationen in der Kommunikation enthalten sind. Beachten Sie jedoch, dass der Dienst den internen Status, einschließlich der Anmeldeinformationen, in den internen Tabellen in der Azure SQL-Datenbank-Instanz, die Sie in Ihrem Microsoft Azure-Abonnement für Metadatenspeicher bereitgestellt haben, beibehält. Diese Datenbank wurde im Rahmen der folgenden Einstellung in der Dienstkonfigurationsdatei (.CSCFG-Datei) definiert: 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

In dieser Datenbank gespeicherte Anmeldeinformationen werden verschlüsselt. Stellen Sie als bewährte Methode außerdem sicher, dass die Web- und Worker-Rollen der Dienstbereitstellungen immer aktualisiert und sicher sind, da beide Zugriff auf die Metadaten-Datenbank und das Zertifikat haben, das für die Verschlüsselung und Entschlüsselung von gespeicherten Anmeldeinformationen verwendet wird. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]