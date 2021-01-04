---
title: Erforderliche Zertifikate zum Zulassen von Back-End-Servern
titleSuffix: Azure Application Gateway
description: In diesem Artikel erfahren Sie, wie Sie ein TLS-/SSL-Zertifikat in ein Authentifizierungszertifikat und ein vertrauenswürdiges Stammzertifikat konvertieren können, die erforderlich sind, um Back-End-Instanzen in Azure Application Gateway zuzulassen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/17/2020
ms.author: absha
ms.openlocfilehash: 874e554063f64ddefce99a223678d64b2e0774c3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397721"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Erstellen von Zertifikaten zum Zulassen des Back-Ends für Azure Application Gateway

Für die End-to-End-TLS-Verschlüsselung erfordert Application Gateway, dass Back-End-Instanzen zugelassen werden, indem Authentifizierungs- bzw. vertrauenswürdige Stammzertifikate hochgeladen werden. Für die v1-SKU sind Authentifizierungszertifikate erforderlich, aber für die v2-SKU werden vertrauenswürdige Stammzertifikate benötigt, um die Zertifikate zuzulassen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:


- Exportieren des Authentifizierungszertifikats aus einem Back-End-Zertifikat (für die v1-SKU)
- Exportieren des vertrauenswürdigen Stammzertifikats aus einem Back-End-Zertifikat (für die v2-SKU)

## <a name="prerequisites"></a>Voraussetzungen

Es muss ein Back-End-Zertifikat vorhanden sein, um die Authentifizierungszertifikate oder vertrauenswürdigen Stammzertifikate zu generieren, die für das Zulassen von Back-End-Instanzen mit Application Gateway erforderlich sind. Das Back-End-Zertifikat kann mit dem TLS-/SSL-Zertifikat identisch sein (oder ein anderes Zertifikat, um die Sicherheit zu erhöhen). Application Gateway bietet keine Möglichkeit, ein TLS-/SSL-Zertifikat zu erstellen oder zu erwerben. Sie können zu Testzwecken ein selbstsigniertes Zertifikat erstellen. Dieses sollten Sie aber nicht für Produktionsworkloads verwenden. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportieren des Authentifizierungszertifikats (für die v1-SKU)

Sie benötigen ein Authentifizierungszertifikat, um Back-End-Instanzen in der v1-SKU von Application Gateway zulassen zu können. Das Authentifizierungszertifikat ist der öffentliche Schlüssel der Back-End-X.509-Serverzertifikate im Base64-codierten Format (.CER). In diesem Beispiel verwenden Sie ein TLS-/SSL-Zertifikat als Back-End-Zertifikat und exportieren dessen öffentlichen Schlüssel, der dann als Authentifizierungszertifikat verwendet wird. Sie verwenden zudem den Windows-Zertifikat-Manager, um die erforderlichen Zertifikate zu exportieren. Sie können auch ein beliebiges anderes Tool verwenden, das für Ihre Zwecke geeignet ist.

Exportieren Sie die CER-Datei mit dem öffentlichen Schlüssel aus Ihrem TLS-/SSL-Zertifikat (nicht den privaten Schlüssel). Führen Sie die folgenden Schritte durch, um die CER-Datei mit dem Base64-kodierten X.509-Zertifikat für Ihr Zertifikat zu exportieren:

1. Öffnen Sie **Benutzerzertifikate verwalten** , um eine CER-Datei für das Zertifikat zu erhalten. Suchen Sie das Zertifikat (in der Regel befindet es sich unter „Certificates > Aktueller Benutzer\Personal\Certificates“), und klicken Sie mit der rechten Maustaste darauf. Klicken Sie auf **Alle Aufgaben** und anschließend auf **Exportieren**. Dadurch wird der **Zertifikatexport-Assistent** geöffnet. Wenn Sie das Zertifikat unter „Aktueller Benutzer\Eigene Zertifikate\Zertifikate“ nicht finden, haben Sie unter Umständen versehentlich „Zertifikate – Lokaler Benutzer“ anstelle von „Zertifikate – Aktueller Benutzer“ geöffnet. Wenn Sie den Zertifikat-Manager im Bereich für den aktuellen Benutzer mithilfe von PowerShell öffnen möchten, geben Sie *certmgr* ins Konsolenfenster ein.

   ![Screenshot des Zertifikat-Managers, in dem „Zertifikate“ und in einem Kontextmenü „Alle Aufgaben“ und „Exportieren“ ausgewählt sind](./media/certificates-for-backend-authentication/export.png)

2. Klicken Sie im Assistenten auf **Weiter**.

   ![Exportieren des Zertifikats](./media/certificates-for-backend-authentication/exportwizard.png)

3. Wählen Sie **Nein, privaten Schlüssel nicht exportieren** aus, und klicken Sie dann auf **Weiter**.

   ![Nein, privaten Schlüssel nicht exportieren](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Wählen Sie auf der Seite **Dateiformat für den Export** die Option **Base-64-codiert X.509 (.CER)** aus, und klicken Sie dann auf **Weiter**.

   ![Base64-codiert](./media/certificates-for-backend-authentication/base64.png)

5. Wählen Sie unter **Zu exportierende Datei** die Option **Durchsuchen** aus, um zu dem Speicherort zu wechseln, an den das Zertifikat exportiert werden soll. Geben Sie unter **Dateiname** einen Namen für die Zertifikatdatei ein. Klicken Sie auf **Weiter**.

   ![Screenshot des Zertifikatexport-Assistenten, in dem Sie eine Datei für den Export angeben können](./media/certificates-for-backend-authentication/browse.png)

6. Klicken Sie auf **Fertig stellen** , um das Zertifikat zu exportieren.

   ![Screenshot des Zertifikatexport-Assistenten, nachdem Sie den Dateiexport abgeschlossen haben](./media/certificates-for-backend-authentication/finish.png)

7. Das Zertifikat wurde erfolgreich exportiert.

   ![Screenshot einer Meldung über den Erfolg im Zertifikatexport-Assistenten](./media/certificates-for-backend-authentication/success.png)

   Das exportierte Zertifikat sieht in etwa wie folgt aus:

   ![Screenshot eines Zertifikatsymbols](./media/certificates-for-backend-authentication/exported.png)

8. Wenn Sie das exportierte Zertifikat mit Editor öffnen, ähnelt die Datei diesem Beispiel. Der blaue Abschnitt enthält die Informationen, die in Application Gateway hochgeladen werden. Wenn Sie das Zertifikat mit Editor öffnen und es nicht so ähnlich aussieht, bedeutet dies in der Regel, dass Sie es nicht als Base64-codiertes X.509-Zertifikat (.cer) exportiert haben. Wenn Sie darüber hinaus einen anderen Text-Editor verwenden möchten, sollten Sie beachten, dass einige Editoren unbeabsichtigte Formatierung im Hintergrund einführen können. Dies kann Probleme beim Hochladen des Texts aus diesem Zertifikat in Azure verursachen.

   ![Mit Editor öffnen](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportieren des vertrauenswürdigen Stammzertifikats (für die v2-SKU)

Sie benötigen ein vertrauenswürdiges Stammzertifikat, um Back-End-Instanzen in der Application Gateway v2-SKU zulassen zu können. Das Stammzertifikat ist ein Base64-kodiertes X.509-Stammzertifikat (.CER) aus den Zertifikaten des Back-End-Servers. In diesem Beispiel verwenden wir ein TLS-/SSL-Zertifikat als Back-End-Zertifikat, exportieren dessen öffentlichen Schlüssel, und exportieren anschließend das Stammzertifikat der vertrauenswürdigen Zertifizierungsstelle aus dem öffentlichen Schlüssel im Base64-codierten Format, um das vertrauenswürdige Stammzertifikat zu erhalten. Zwischenzertifikate sollten mit dem Serverzertifikat gebündelt und auf dem Back-End-Server installiert werden.

Führen Sie die folgenden Schritte durch, um die CER-Datei für Ihr Zertifikat zu exportieren:

1. Führen Sie die Schritte 1–8 im vorhergehenden Abschnitt [Exportieren des Authentifizierungszertifikats (für die v1-SKU)](#export-authentication-certificate-for-v1-sku) durch, um den öffentlichen Schlüssel aus Ihrem Back-End-Zertifikat zu exportieren.

2. Öffnen Sie die Datei, wenn der öffentliche Schlüssel exportiert wurde.

   ![Authentifizierungszertifikat öffnen](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![Zertifikatinformationen](./media/certificates-for-backend-authentication/general.png)

3. Wechseln Sie zur Ansicht mit dem Zertifizierungspfad, um die Zertifizierungsstelle anzuzeigen.

   ![Zertifikatdetails](./media/certificates-for-backend-authentication/certdetails.png)

4. Wählen Sie das Sicherheitszertifikat aus, und klicken Sie auf **Zertifikat anzeigen**.

   ![Zertifikatpfad](./media/certificates-for-backend-authentication/rootcert.png)

   Die Details des Stammzertifikats sollten angezeigt werden.

   ![Zertifikatinformationen](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Wechseln Sie zur Ansicht **Details** , und klicken Sie auf **In Datei kopieren**.

   ![Stammzertifikat kopieren](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Sie haben die Details des Stammzertifikats nun aus dem Back-End-Zertifikat extrahiert. Der **Zertifikatexport-Assistent** wird angezeigt. Führen Sie die Schritte 2 – 9 im Abschnitt **Exportieren des Authentifizierungszertifikats (für die v1-SKU)** durch, um das vertrauenswürdige Stammzertifikat im Base64-kodierten CER-Format als X.509-Zertifikat zu exportieren.

## <a name="next-steps"></a>Nächste Schritte

Jetzt verfügen Sie über das Authentifizierungszertifikat und das vertrauenswürdige Sicherheitszertifikat im CER-Format (Base64-kodierte X.509-Zertifikate). Sie können diese Zertifikate zu Application Gateway hinzufügen, um die End-to-End-TLS-Verschlüsselung auf Ihren Back-End-Servern zuzulassen. Weitere Informationen finden Sie unter [Konfigurieren von End-to-End-TLS mit Application Gateway mithilfe von PowerShell](./application-gateway-end-to-end-ssl-powershell.md).