---
title: Verwenden eigener Zertifikate mit Azure Data Box-/Azure Data Box Heavy-Geräten
description: 'Vorgehensweise: Verwenden eigener Zertifikate zum Zugreifen auf die lokale Webbenutzeroberfläche und den Blobspeicher auf Ihrem Data Box- oder Data Box Heavy-Gerät.'
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545172"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Verwenden eigener Zertifikate mit Data Box- und Data Box Heavy-Geräten

Während der Auftragsverarbeitung werden selbstsignierte Zertifikate für den Zugriff auf die lokale Webbenutzeroberfläche und den Blobspeicher für ein Data Box- oder Data Box Heavy-Gerät generiert. Wenn Sie lieber über einen vertrauenswürdigen Kanal mit Ihrem Gerät kommunizieren möchten, können Sie Ihre eigenen Zertifikate verwenden.

In diesem Artikel wird beschrieben, wie Sie Ihre eigenen Zertifikate installieren und die Standardzertifikate wiederherstellen, bevor Sie Ihr Gerät an das Rechenzentrum zurückgeben. Außerdem enthält er eine Zusammenfassung der Zertifikatanforderungen.

## <a name="about-certificates"></a>Informationen zu Zertifikaten

Ein Zertifikat stellt eine Verbindung zwischen einem **öffentlichen Schlüssel** und einer Entität (z. B. Domänenname) her, die durch eine vertrauenswürdige dritte Instanz (z. B. eine  **Zertifizierungsstelle**) **signiert** (verifiziert) wurde.  Ein Zertifikat bietet eine bequeme Möglichkeit, vertrauenswürdige öffentliche Verschlüsselungsschlüssel zu verteilen. Zertifikate stellen damit sicher, dass Ihre Kommunikation vertrauenswürdig ist und Sie verschlüsselte Informationen an den richtigen Server senden.

Wenn Ihr Data Box-Gerät anfänglich konfiguriert ist, werden automatisch selbstsignierte Zertifikate generiert. Optional können Sie Ihre eigenen Zertifikate vorlegen. Es gibt Richtlinien, die Sie befolgen müssen, wenn Sie Ihre eigenen Zertifikate verwenden möchten.

Auf einem Data Box- oder Data Box Heavy-Gerät werden zwei Arten von Endpunktzertifikaten verwendet:

- Blobspeicherzertifikat
- Zertifikat für lokale Benutzeroberfläche

### <a name="certificate-requirements"></a>Zertifikatanforderungen

Die Zertifikate müssen die folgenden Anforderungen erfüllen:

- Das Endpunktzertifikat muss im Format `.pfx` mit einem privaten Schlüssel, der exportiert werden kann, bereitgestellt werden.
- Sie können ein einzelnes Zertifikat für jeden Endpunkt, ein Mehrdomänenzertifikat für mehrere Endpunkte oder ein Platzhalter-Endpunktzertifikat verwenden.
- Die Eigenschaften eines Endpunktzertifikats ähneln denen eines typischen SSL-Zertifikats.
- Auf dem Clientcomputer ist ein entsprechendes Zertifikat im DER-Format (`.cer`-Dateierweiterung) erforderlich.
- Nachdem Sie das lokale Benutzeroberflächenzertifikat hochgeladen haben, müssen Sie den Browser neu starten und den Cache leeren. Weitere Informationen finden Sie in den speziellen Anweisungen für Ihren Browser.
- Die Zertifikate müssen geändert werden, wenn sich der Gerätename oder der DNS-Domänenname ändert.
- Verwenden Sie die folgende Tabelle beim Erstellen von Endpunktzertifikaten:

  |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
  |---------|---------|---------|---------|
  |Lokale Benutzeroberfläche| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blobspeicher|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Ein einzelnes Zertifikat für mehrere alternative Antragstellernamen|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Weitere Informationen finden Sie unter [Zertifikatanforderungen](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Hinzufügen von Zertifikaten zum Gerät

Sie können Ihre eigenen Zertifikate für den Zugriff auf die lokale Webbenutzeroberfläche und auf Blobspeicher verwenden.

> [!IMPORTANT]
> Wenn der Gerätename oder die DNS-Domäne geändert wird, müssen neue Zertifikate erstellt werden. Die Clientzertifikate und die Gerätezertifikate sollten dann mit dem neuen Gerätenamen und der DNS-Domäne aktualisiert werden.

Gehen Sie wie hier beschrieben vor, um Ihr eigenes Zertifikat Ihrem Gerät hinzuzufügen:

1. Wechseln Sie zu **Verwalten** > **Zertifikate**.

   In **Name** wird der Gerätename angezeigt. In **DNS-Domäne** wird der Domänenname für den DNS-Server angezeigt.

   Unten auf dem Bildschirm werden die derzeit verwendeten Zertifikate angezeigt. Bei einem neuen Gerät werden die selbstsignierten Zertifikate angezeigt, die während der Auftragsverarbeitung generiert wurden.

   ![Seite „Zertifikate“ für ein Data Box-Gerät](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Wenn Sie den **Namen** (Gerätename) oder **DNS-Domäne** (die Domäne des DNS-Servers für das Gerät) ändern müssen, tun Sie dies jetzt, bevor Sie das Zertifikat hinzufügen. Wählen Sie dann **Anwenden** aus.

   Das Zertifikat muss geändert werden, wenn sich der Gerätename oder der DNS-Domänenname ändert.

   ![Anwenden eines neuen Gerätenamens und einer DNS-Domäne für eine Data Box](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Um ein Zertifikat hinzuzufügen, wählen Sie **Zertifikat hinzufügen** aus, um das Panel **Zertifikat hinzufügen** zu öffnen. Wählen Sie dann den **Zertifikattyp aus** – entweder **Blobspeicher** oder **Lokale Webbenutzeroberfläche**.

   ![Panel „Zertifikate hinzufügen“ auf der Seite „Zertifikate“ für ein Data Box-Gerät](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Wählen Sie die Zertifikatdatei (im `.pfx`-Format) aus, und geben Sie das Kennwort ein, das beim Exportieren des Zertifikats festgelegt wurde. Wählen Sie dann **Überprüfen und hinzufügen** aus.

   ![Einstellungen zum Hinzufügen eines Blobendpunktzertifikats zu einer Data Box](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Nachdem das Zertifikat erfolgreich hinzugefügt wurde, wird auf dem Bildschirm **Zertifikate** der Fingerabdruck für das neue Zertifikat angezeigt. Der Status des Zertifikats ist **Gültig**.

   ![Ein gültiges neues Zertifikat, das erfolgreich hinzugefügt wurde](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Wählen Sie den Zertifikatnamen aus, und klicken Sie darauf, um die Zertifikatdetails anzuzeigen. Das Zertifikat läuft nach einem Jahr ab.

   ![Anzeigen von Zertifikatdetails für ein Data Box-Gerät](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Wenn Sie das Zertifikat für die lokale Webbenutzeroberfläche geändert haben, müssen Sie den Browser und dann die lokale Webbenutzeroberfläche neu starten. Dieser Schritt ist erforderlich, um Probleme mit dem SSL-Cache zu vermeiden.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Installieren Sie das neue Zertifikat auf dem Clientcomputer, den Sie für den Zugriff auf die lokale Webbenutzeroberfläche verwenden. Anweisungen hierzu finden Sie unter [Importieren von Zertifikaten in den Client](#import-certificates-to-client) weiter unten.


## <a name="import-certificates-to-client"></a>Importieren von Zertifikaten in den Client

Nachdem Sie Ihrem Data Box-Gerät ein Zertifikat hinzugefügt haben, müssen Sie das Zertifikat in den Clientcomputer importieren, den Sie für den Zugriff auf das Gerät verwenden. Sie importieren das Zertifikat in den Speicher der vertrauenswürdigen Stammzertifizierungsstelle für den lokalen Computer.

Führen Sie die folgenden Schritte aus, um ein Zertifikat in einen Windows-Client zu importieren:

1. Klicken Sie im Datei-Explorer mit der rechten Maustaste auf die Zertifikatdatei (im `.cer`-Format), und wählen Sie **Zertifikat installieren** aus. Mit dieser Aktion wird der Zertifikatimport-Assistent gestartet.

    ![Importieren des Zertifikats 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. Wählen Sie für **Speicherort** die Option **Lokaler Computer** und dann **Weiter** aus.

    ![Auswählen von „Lokaler Computer“ als Speicherort im Zertifikatimport-Assistenten](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern**, **Vertrauenswürdige Stammzertifizierungsstelle** und dann **Weiter** aus.

   ![Auswählen des Speichers für vertrauenswürdige Stammzertifizierungsstellen im Zertifikatimport-Assistenten](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Überprüfen Sie die Einstellungen, und wählen Sie **Fertig stellen** aus. Sie werden in einer Meldung darüber informiert, dass der Import erfolgreich war.

   ![Überprüfen der Zertifikateinstellungen und Beenden des Zertifikatimport-Assistenten](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Wiederherstellen von Standardzertifikaten

Bevor Sie Ihr Gerät an das Azure-Rechenzentrum zurückgeben, sollten Sie die ursprünglichen Zertifikate wiederherstellen, die während der Auftragsverarbeitung generiert wurden.

Führen Sie die folgenden Schritte aus, um die während der Auftragsverarbeitung generierten Zertifikate wiederherzustellen:

1. Wechseln Sie zu **Verwalten** > **Zertifikate**, und wählen Sie **Zertifikate wiederherstellen** aus.

   Das Wiederherstellen von Zertifikaten entspricht der Rückkehr zur Verwendung der selbstsignierten Zertifikate, die während der Auftragsverarbeitung generiert wurden. Ihre eigenen Zertifikate werden vom Gerät entfernt.

   ![Option „Zertifikate wiederherstellen“ in „Verwalten > Zertifikate“ für ein Data Box-Gerät](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Wechseln Sie nach erfolgreichem Abschluss der Zertifikatwiederherstellung zu **Herunterfahren oder neu starten**, und wählen Sie **Neu starten** aus. Dieser Schritt ist erforderlich, um Probleme mit dem SSL-Cache zu vermeiden.

   ![Herunterfahren oder Neustarten der lokalen Webbenutzeroberfläche nach dem Wiederherstellen von Zertifikaten auf einem Data Box-Gerät](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Warten Sie einige Minuten, und melden Sie sich dann erneut bei der lokalen Webbenutzeroberfläche an.
