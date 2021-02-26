---
title: Verwenden von Zertifikaten mit Azure Stack Edge Pro-GPU | Microsoft-Dokumentation
description: Hier wird die Verwendung von Zertifikaten mit Azure Stack Edge Pro-GPU-Geräten beschrieben. Dabei werden die Gründe für die Verwendung und die möglichen Typen erörtert, und Sie erfahren, wie Sie Zertifikate auf Ihr Gerät hochladen können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: 5cec5eda7cf398949865bf6d3d3e8be5b2d5e840
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549436"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Verwenden von Zertifikaten mit einem Azure Stack Edge Pro-GPU-Gerät

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In diesem Artikel werden die Zertifikattypen beschrieben, die auf Ihrem Azure Stack Edge Pro-Gerät installiert werden können. Der Artikel enthält auch die Details für jeden Zertifikattyp zusammen mit dem Verfahren zum Installieren und Identifizieren des Ablaufdatums. 

## <a name="about-certificates"></a>Informationen zu Zertifikaten

Ein Zertifikat stellt eine Verbindung zwischen einem **öffentlichen Schlüssel** und einer Entität (z. B. Domänenname) her, die durch eine vertrauenswürdige dritte Instanz (z. B. eine  **Zertifizierungsstelle**) **signiert** (verifiziert) wurde.  Ein Zertifikat bietet eine bequeme Möglichkeit, vertrauenswürdige öffentliche Verschlüsselungsschlüssel zu verteilen. Zertifikate stellen damit sicher, dass Ihre Kommunikation vertrauenswürdig ist und dass Sie verschlüsselte Informationen an den richtigen Server senden. 

Bei der Erstkonfiguration Ihres Azure Stack Edge Pro-Geräts werden automatisch selbstsignierte Zertifikate generiert. Optional können Sie Ihre eigenen Zertifikate vorlegen. Es gibt Richtlinien, die Sie befolgen müssen, wenn Sie Ihre eigenen Zertifikate verwenden möchten.

## <a name="types-of-certificates"></a>Zertifikattypen

Auf Ihrem Azure Stack Edge Pro-Gerät werden die folgenden verschiedenen Zertifikattypen verwendet: 
- Signaturzertifikate
    - Stamm-CA
    - Fortgeschrittene Anfänger

- Endpunktzertifikate
    - Knotenzertifikat
    - Lokale Benutzeroberflächenzertifikate
    - Azure Resource Manager-Zertifikate
    - Blobspeicherzertifikate
    - IoT-Gerätezertifikate
    <!--- WiFi certificates
    - VPN certificates-->

- Verschlüsselungszertifikate
    - Supportsitzungszertifikate

Jedes dieser Zertifikate wird in den folgenden Abschnitten ausführlich erläutert.

## <a name="signing-chain-certificates"></a>Signaturkettenzertifikate

Dies sind die Zertifikate für die Zertifizierungsstelle, die die Zertifikate signiert, oder die signierende Zertifizierungsstelle. 

### <a name="types"></a>Typen

Diese Zertifikate können Stammzertifikate oder die Zwischenzertifikate sein. Die Stammzertifikate sind immer selbstsigniert. Die Zwischenzertifikate sind nicht selbstsigniert und werden von der signierenden Zertifizierungsstelle unterzeichnet.

### <a name="caveats"></a>Vorbehalte

- Die Stammzertifikate sollten Signaturkettenzertifikate sein.
- Die Stammzertifikate können in folgendem Format auf Ihr Gerät hochgeladen werden: 
    - **DER** – Diese sind als `.cer`-Dateierweiterung verfügbar.
    - **Base64-codiert** – Diese sind als `.cer`-Dateierweiterung verfügbar.
    - **P7b** – Dieses Format wird nur für Signaturkettenzertifikate verwendet, die die Stamm- und Zwischenzertifikate enthalten.
- Signaturkettenzertifikate werden immer hochgeladen, bevor Sie andere Zertifikate hochladen.


## <a name="node-certificates"></a>Knotenzertifikate

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Alle Knoten im Gerät kommunizieren ständig miteinander und müssen daher über eine Vertrauensstellung verfügen. Knotenzertifikate bieten eine Möglichkeit, diese Vertrauensstellung herzustellen. Knotenzertifikate kommen auch ins Spiel, wenn Sie eine Remoteverbindung mit dem Geräteknoten über eine PowerShell-Remotesitzung über HTTPS herstellen.

### <a name="caveats"></a>Vorbehalte

- Das Knotenzertifikat sollte im Format `.pfx` mit einem privaten Schlüssel, der exportiert werden kann, bereitgestellt werden. 
- Sie können ein Platzhalterknotenzertifikat oder vier einzelne Knotenzertifikate erstellen und hochladen. 
- Ein Knotenzertifikat muss geändert werden, wenn sich die DNS-Domäne, aber nicht der Gerätename ändert. Wenn Sie Ihr eigenes Knotenzertifikat bereitstellen, können Sie die Geräteseriennummer nicht ändern, sondern nur den Domänennamen.
- Verwenden Sie die folgende Tabelle als Leitfaden bei der Erstellung eines Knotenzertifikats.
   
    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Endpunktzertifikate

Für alle Endpunkte, die vom Gerät verfügbar gemacht werden, ist für eine vertrauenswürdige Kommunikation ein Zertifikat erforderlich. Die Endpunktzertifikate umfassen diejenigen, die für den Zugriff auf Azure Resource Manager und den Blobspeicher über die REST-APIs erforderlich sind. 

Wenn Sie ein eigenes signiertes Zertifikat verwenden, benötigen Sie auch die entsprechende Signaturkette des Zertifikats. Für die Signaturketten-, Azure Resource Manager- und Blobzertifikate auf dem Gerät benötigen Sie auch die entsprechenden Zertifikate auf dem Clientcomputer für die Authentifizierung und die Kommunikation mit dem Gerät.

### <a name="caveats"></a>Vorbehalte

- Die Endpunktzertifikate müssen im Format `.pfx` mit einem privaten Schlüssel vorliegen. Die Signaturkette sollte im DER-Format (`.cer`-Dateierweiterung) vorliegen. 
- Wenn Sie Ihre eigenen Endpunktzertifikate bereitstellen, können diese als Einzelzertifikate oder als Zertifikate für mehrere Domänen vorliegen. 
- Wenn Sie eine Signaturkette bereitstellen, muss das Signaturkettenzertifikat hochgeladen werden, bevor Sie ein Endpunktzertifikat hochladen.
- Diese Zertifikate müssen geändert werden, wenn sich der Gerätename oder die DNS-Domänennamen ändern.
- Es kann ein Platzhalterzeichen-Endpunktzertifikat verwendet werden.
- Die Eigenschaften der Endpunktzertifikate ähneln denen eines typischen SSL-Zertifikats. 
- Verwenden Sie die folgende Tabelle beim Erstellen eines Endpunktzertifikats:

    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Einzelnes Zertifikat für mehrere alternative Antragstellernamen für beide Endpunkte|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Lokale Benutzeroberflächenzertifikate

Sie können über einen Browser auf die lokale Webbenutzeroberfläche Ihres Geräts zugreifen. Um sicherzustellen, dass diese Kommunikation sicher ist, können Sie Ihr eigenes Zertifikat hochladen. 

### <a name="caveats"></a>Vorbehalte

- Das lokale Benutzeroberflächenzertifikat wird ebenfalls in einem `.pfx`-Format mit einem privaten Schlüssel hochgeladen, der exportiert werden kann.
- Nachdem Sie das lokale Benutzeroberflächenzertifikat hochgeladen haben, müssen Sie den Browser neu starten und den Cache leeren. Weitere Informationen finden Sie in den speziellen Anweisungen für Ihren Browser.

    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Lokale Benutzeroberfläche| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge-Gerätezertifikate

Ihr Azure Stack Edge Pro-Gerät ist auch ein IoT-Gerät, bei dem Compute durch ein damit verbundenes IoT Edge-Gerät aktiviert wird. Für jede sichere Kommunikation zwischen diesem IoT Edge-Gerät und den Downstreamgeräten, die sich mit ihm verbinden können, können Sie auch IoT Edge-Zertifikate hochladen. 

Das Gerät verfügt über selbstsignierte Zertifikate, die verwendet werden können, wenn Sie nur das Computeszenario mit dem Gerät verwenden möchten. Wenn das Azure Stack Edge Pro-Gerät jedoch mit Downstreamgeräten verbunden ist, müssen Sie Ihre eigenen Zertifikate bereitstellen.

Es gibt drei IoT Edge-Zertifikate, die Sie installieren müssen, um diese Vertrauensstellung zu aktivieren:

- **Stammzertifizierungsstelle oder die Zertifizierungsstelle des Besitzers.**
- **Zertifizierungsstelle für Gerätezertifikate** 
- **Zertifikat des Geräteschlüssels**

### <a name="caveats"></a>Vorbehalte

- Die IoT Edge-Zertifikate werden im Format `.pem` hochgeladen. 


Weitere Informationen zu IoT Edge-Zertifikaten finden Sie unter [Details zu Azure IoT Edge-Zertifikaten](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Supportsitzungszertifikate

Wenn auf Ihrem Azure Stack Edge Pro-Gerät Probleme auftreten, kann zur Behebung dieser Probleme eine Remotesitzung des PowerShell-Supports auf dem Gerät geöffnet werden. Sie können ein Zertifikat hochladen, um eine sichere, verschlüsselte Kommunikation über diese Supportsitzung zu ermöglichen.

### <a name="caveats"></a>Vorbehalte

- Stellen Sie sicher, dass das entsprechende `.pfx`-Zertifikat mit privatem Schlüssel mithilfe des Entschlüsselungstools auf dem Clientcomputer installiert ist.
- Stellen Sie sicher, dass das Feld **Schlüsselverwendung** für das Zertifikat nicht **Zertifikatssignierung** ist. Klicken Sie zur Überprüfung mit der rechten Maustaste auf das Zertifikat, wählen Sie **Öffnen** aus, und suchen Sie auf der Registerkarte **Details** nach **Schlüsselverwendung**. 


### <a name="caveats"></a>Vorbehalte

- Das Supportsitzungszertifikat muss im DER-Format mit der `.cer`-Erweiterung bereitgestellt werden.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Erstellen von Zertifikaten (optional)

Der folgende Abschnitt beschreibt das Verfahren zum Erstellen von Signaturkettenzertifikaten und Endpunktzertifikaten.

### <a name="certificate-workflow"></a>Zertifikatworkflow

Sie haben eine definierte Möglichkeit, die Zertifikate für die in Ihrer Umgebung betriebenen Geräte zu erstellen. Sie können die Zertifikate verwenden, die Ihnen von Ihrem IT-Administrator zur Verfügung gestellt werden. 

**Nur zu Entwicklungs- oder Testzwecken können Sie Windows PowerShell auch zum Erstellen von Zertifikaten auf Ihrem lokalen System verwenden.** Beachten Sie beim Erstellen der Zertifikate für den Client die folgenden Vorgaben:

1. Sie können eine der folgenden Arten von Zertifikaten erstellen:

    - Erstellen Sie ein einzelnes Zertifikat, das für die Verwendung mit einem einzelnen vollqualifizierten Domänennamen (FQDN) gültig ist. Beispiel: *mydomain.com*.
    - Erstellen Sie ein Platzhalterzeichenzertifikat, um den Hauptdomänennamen und auch mehrere Unterdomänen zu sichern. Beispiel: * *.mydomain.com*.
    - Erstellen Sie ein SAN-Zertifikat (alternativer Antragstellername), das mehrere Domänennamen in einem einzelnen Zertifikat abdeckt. 

2. Wenn Sie Ihr eigenes Zertifikat bereitstellen, benötigen Sie ein Stammzertifikat für die Signaturkette. Weitere Informationen finden Sie unter den Schritten zum [Erstellen von Signaturkettenzertifikaten](#create-signing-chain-certificate).

3. Dann können Sie die Endpunktzertifikate für die lokale Benutzeroberfläche, für das Blob und für Azure Resource Manager erstellen. Sie können drei separate Zertifikate für die Appliance, das Blob und Azure Resource Manager erstellen, oder Sie können ein Zertifikat für alle drei Endpunktzertifikate erstellen. Detaillierte Schritte finden Sie unter [Erstellen von Signatur- und Endpunktzertifikaten](#create-signed-endpoint-certificates).

4. Unabhängig davon, ob Sie drei separate Zertifikate oder ein Zertifikat erstellen, geben Sie die Antragstellernamen (SN) und die alternativen Antragstellernamen (SAN) gemäß den Richtlinien an, die für jeden Zertifikattyp bereitgestellt werden. 

### <a name="create-signing-chain-certificate"></a>Erstellen von Signaturkettenzertifikaten

Erstellen Sie diese Zertifikate über Windows PowerShell, das im Administratormodus ausgeführt wird. **Die auf diese Weise erstellten Zertifikate sollten nur für Entwicklungs- oder Testzwecke verwendet werden.**

Das Signaturkettenzertifikat muss nur einmal erstellt werden. Die anderen Endpunktzertifikate verweisen für die Signatur auf dieses Zertifikat.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Erstellen signierter Endpunktzertifikate

Erstellen Sie diese Zertifikate über Windows PowerShell, das im Administratormodus ausgeführt wird.

In diesen Beispielen werden Endpunktzertifikate für ein Gerät mit Folgendem erstellt:
    - Gerätename: `DBE-HWDC1T2`
    - DNS-Domäne: `microsoftdatabox.com`

Ersetzen Sie sie durch den Namen und die DNS-Domäne für Ihr Gerät, um Zertifikate für Ihr Gerät zu erstellen.
 
**Blobendpunktzertifikat**

Erstellen Sie ein Zertifikat für den Blobendpunkt in Ihrem persönlichen Speicher.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager-Endpunktzertifikat**

Erstellen Sie ein Zertifikat für die Azure Resource Manager-Endpunkte in Ihrem persönlichen Speicher.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Lokales Webbenutzeroberflächen-Gerätezertifikat**

Erstellen Sie in Ihrem persönlichen Speicher ein Zertifikat für die lokale Webbenutzeroberfläche des Geräts.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Ein einzelnes Zertifikat für mehrere alternative Antragstellernamen für alle Endpunkte**

Erstellen Sie ein einzelnes Zertifikat für alle Endpunktzertifikate in Ihrem persönlichen Speicher.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Nachdem die Zertifikate erstellt wurden, besteht der nächste Schritt darin, die Zertifikate auf Ihr Azure Stack Edge Pro-Gerät hochzuladen.


## <a name="upload-certificates"></a>Hochladen von Zertifikaten 

Die Zertifikate, die Sie für Ihr Gerät erstellt haben, befinden sich standardmäßig im **persönlichen Speicher** auf Ihrem Client. Diese Zertifikate müssen auf dem Client in entsprechende Formatdateien exportiert werden, die dann auf das Gerät hochgeladen werden können.

1. Das Stammzertifikat muss als DER-Format mit der `.cer`-Erweiterung exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten im DER-Format](#export-certificates-as-der-format).
2. Die Endpunktzertifikate müssen als *PFX*-Dateien mit privaten Schlüsseln exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten als *PFX*-Datei mit privaten Schlüsseln](#export-certificates-as-pfx-format-with-private-key). 
3. Das Stamm- und die Endpunktzertifikate werden dann über die Option **+ Zertifikat hinzufügen** auf der Seite „Zertifikate“ der lokalen Webbenutzeroberfläche auf das Gerät hochgeladen. 

    1. Laden Sie zuerst die Stammzertifikate hoch. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Zertifikate > + Zertifikat hinzufügen**.

        ![Hinzufügen des Signaturkettenzertifikats 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Laden Sie als nächstes die Endpunktzertifikate hoch. 

        ![Hinzufügen des Signaturkettenzertifikats 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Wählen Sie die Zertifikatsdateien im *PFX*-Format aus, und geben Sie das Kennwort ein, das Sie beim Exportieren des Zertifikats angegeben haben. Das Anwenden des Azure Resource Manager-Zertifikats kann einige Minuten dauern.

        Wenn die Signaturkette nicht zuerst aktualisiert wird und Sie versuchen, die Endpunktzertifikate hochzuladen, erhalten Sie einen Fehler.

        ![Anwenden von Zertifikatfehlern](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Wechseln Sie zurück, und laden Sie das Signaturkettenzertifikat hoch. Anschließend laden Sie die Endpunktzertifikate hoch und wenden sie an.

> [!IMPORTANT]
> Wenn der Gerätename oder die DNS-Domäne geändert wird, müssen neue Zertifikate erstellt werden. Die Clientzertifikate und die Gerätezertifikate sollten dann mit dem neuen Gerätenamen und der DNS-Domäne aktualisiert werden. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importieren von Zertifikaten auf den Client, der auf das Gerät zugreift

Die Zertifikate, die Sie erstellt und auf Ihr Gerät hochgeladen haben, müssen auf Ihrem Windows-Client (der auf das Gerät zugreift) in den entsprechenden Zertifikatspeicher importiert werden.

1. Das Stammzertifikat, das Sie im DER-Format exportiert haben, sollte nun in die **vertrauenswürdigen Stammzertifizierungsstellen** auf dem Clientsystem importiert werden. Ausführliche Schritte finden Sie unter [Importieren von Zertifikaten in den Speicher „Vertrauenswürdige Stammzertifizierungsstellen“](#import-certificates-as-der-format).

2. Die Endpunktzertifikate, die Sie als `.pfx` exportiert haben, müssen im DER-Format mit der Erweiterung `.cer` exportiert werden. Diese `.cer` wird dann in den **Zertifikatspeicher „Persönlich“** des Systems importiert. Ausführliche Schritte finden Sie unter [Importieren von Zertifikaten in den Zertifikatspeicher „Persönlich“](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importieren von Zertifikaten im DER-Format

Führen Sie die folgenden Schritte aus, um Zertifikate auf einem Windows-Client zu importieren:

1. Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Zertifikat installieren** aus. Mit dieser Aktion wird der Zertifikatimport-Assistent gestartet.

    ![Importieren des Zertifikats 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. Wählen Sie für **Speicherort** die Option **Lokaler Computer** und dann **Weiter** aus.

    ![Importieren des Zertifikats 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** und dann **Durchsuchen** aus. 

    - Um in den persönlichen Speicher zu importieren, navigieren Sie zum persönlichen Speicher Ihres Remotehosts, und wählen Sie dann **Weiter** aus.

        ![Importieren des Zertifikats 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Um in einen vertrauenswürdigen Speicher zu importieren, navigieren Sie zu der vertrauenswürdigen Stammzertifizierungsstelle, und wählen Sie dann **Weiter** aus.

        ![Importieren des Zertifikats 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Wählen Sie **Fertig stellen** aus. Eine Meldung wird angezeigt, die besagt, dass der Import erfolgreich war.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Exportieren von Zertifikaten als PFX-Format mit privatem Schlüssel

Führen Sie die folgenden Schritte aus, um ein SSL-Zertifikat mit privatem Schlüssel auf einem Windows-Computer zu exportieren. 

> [!IMPORTANT]
> Führen Sie diese Schritte auf demselben Computer aus, auf dem Sie das Zertifikat erstellt haben. 

1. Führen Sie *certlm.msc* aus, um den lokalen Computerzertifikatspeicher zu starten.

1. Doppelklicken Sie auf den Ordner **Persönlich** und dann auf **Zertifikate**.

    ![Exportieren des Zertifikats 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Klicken Sie mit der rechten Maustaste auf das Zertifikat, das Sie sichern möchten, und wählen Sie **Alle Aufgaben > Exportieren** aus.

    ![Exportieren des Zertifikats 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Folgen Sie dem Zertifikatexport-Assistenten, um Ihr Zertifikat in einer PFX-Datei zu sichern.

    ![Exportieren des Zertifikats 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Wählen Sie **Ja, privaten Schlüssel exportieren** aus.

    ![Exportieren des Zertifikats 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Wählen Sie **Wenn möglich, alle Zertifikate in den Zertifikatpfad einbeziehen**, **Alle erweiterten Eigenschaften exportieren** und **Zertifikatdatenschutz aktivieren** aus. 

    > [!IMPORTANT]
    > Wählen Sie NICHT die Option **Privaten Schlüssel löschen** aus, wenn der Export erfolgreich ist.

    ![Exportieren des Zertifikats 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Geben Sie ein Kennwort ein, das Sie sich merken können. Bestätigen Sie das Kennwort. Das Kennwort schützt den privaten Schlüssel.

    ![Exportieren des Zertifikats 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Wählen Sie diese Option aus, um die Datei an einem bestimmten Ort zu speichern.

    ![Exportieren des Zertifikats 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Wählen Sie **Fertig stellen** aus.

    ![Exportieren des Zertifikats 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Sie erhalten die Meldung, dass der Export erfolgreich war. Klicken Sie auf **OK**.

    ![Exportieren des Zertifikats 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

Die Sicherungskopie der PFX-Datei wird jetzt an dem von Ihnen gewählten Ort gespeichert und kann zu Ihrer Sicherheit verschoben oder aufbewahrt werden.


### <a name="export-certificates-as-der-format"></a>Exportieren von Zertifikaten im DER-Format

1. Führen Sie *certlm.msc* aus, um den lokalen Computerzertifikatspeicher zu starten.

1. Wählen Sie im Zertifikatspeicher „Persönlich“ das Stammzertifikat aus. Klicken Sie mit der rechten Maustaste, und wählen Sie **Alle Aufgaben > Exportieren** aus.

    ![Exportieren des Zertifikats DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Der Zertifikat-Assistent wird geöffnet. Wählen Sie das Format als **DER-codiertes binäres X.509 (.cer)** aus. Wählen Sie **Weiter** aus.

    ![Exportieren des Zertifikats DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Durchsuchen und wählen Sie den Speicherort, an den Sie die Datei im CER-Format exportieren möchten.

    ![Exportieren des Zertifikats DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Wählen Sie **Fertig stellen** aus.

    ![Exportieren des Zertifikats DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Unterstützte Zertifikatalgorithmen

 Von Ihrem Azure Stack Edge Pro-Gerät werden nur die RSA-Zertifikate (Rivest-Shamir-Adleman) unterstützt. Wenn ECDSA-Zertifikate (Elliptic Curve Digital Signature Algorithm) verwendet werden, ist das Geräteverhalten unbestimmt.

 Zertifikate, die einen öffentlichen RSA-Schlüssel enthalten, werden als RSA-Zertifikate bezeichnet. Zertifikate, die einen öffentlichen ECC-Schlüssel (Elliptic Curve Cryptographic) enthalten, werden als ECDSA-Zertifikate (Elliptic Curve Digital Signature Algorithm) bezeichnet. 


## <a name="view-certificate-expiry"></a>Anzeigen des Zertifikatablaufs

Wenn Sie Ihre eigenen Zertifikate bereitstellen, laufen die Zertifikate in der Regel nach einem Jahr oder sechs Monaten ab. Wenn Sie das Ablaufdatum auf Ihrem Zertifikat anzeigen möchten, wechseln Sie zur Seite **Zertifikate** auf der lokalen Webbenutzeroberfläche Ihres Geräts. Wenn Sie ein bestimmtes Zertifikat auswählen, können Sie das Ablaufdatum auf Ihrem Zertifikat anzeigen.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates](azure-stack-edge-j-series-manage-certificates.md).-->

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen Ihres Azure Stack Edge Pro-Geräts](azure-stack-edge-gpu-deploy-prep.md)
