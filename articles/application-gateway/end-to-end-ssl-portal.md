---
title: Konfigurieren von End-to-End-TLS-Verschlüsselung mithilfe des Portals
titleSuffix: Azure Application Gateway
description: Erfahren Sie, wie Sie das Azure-Portal zum Erstellen eines Anwendungsgateways mit End-to-End-TLS-Verschlüsselung verwenden.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6e2d1828acefacb03cc2f42193b8cd8897578b6f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397500"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Konfigurieren von End-to-End-TLS mit Application Gateway im Azure-Portal

In diesem Artikel wird beschrieben, wie Sie das Azure-Portal zum Konfigurieren der End-to-End-TLS-Verschlüsselung (Transport Layer Security), vorher als SSL-Verschlüsselung (Secure Sockets Layer) bekannt, mit einer Azure Application Gateway v1-SKU verwenden.

> [!NOTE]
> Zum Aktivieren der End-to-End-Konfiguration mit einer Application Gateway v2-SKU sind vertrauenswürdige Stammzertifikate erforderlich.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

Zum Konfigurieren von End-to-End-TLS mit einem Anwendungsgateway benötigen Sie ein Zertifikat für das Gateway. Auch für die Back-End-Server sind Zertifikate erforderlich. Das Gatewayzertifikat wird zum Ableiten eines symmetrischen Schlüssels gemäß der Spezifikation des TLS-Protokolls verwendet. Der symmetrische Schlüssel wird dann zum Verschlüsseln und Entschlüsseln des Datenverkehrs mit dem Gateway verwendet. 

Für die End-to-End-TLS-Verschlüsselung müssen die richtigen Back-End-Server explizit im Application Gateway zugelassen werden. Um diesen Zugriff zu erlauben, laden Sie das öffentliche Zertifikat der Back-End-Server (auch als Authentifizierungszertifikate (v1) oder vertrauenswürdige Stammzertifikate (v2) bezeichnet) auf das Anwendungsgateway hoch. Durch das Hinzufügen des Zertifikats wird sichergestellt, dass das Anwendungsgateway nur mit bekannten Back-End-Instanzen kommuniziert. Außerdem wird mit dieser Konfiguration die End-to-End-Kommunikation geschützt.

Weitere Informationen finden Sie unter [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](./ssl-overview.md).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Erstellen eines neuen Anwendungsgateways mit End-to-End-TLS

Sie müssen zunächst die TLS-Terminierung beim Erstellen eines neuen Anwendungsgateways aktivieren, um ein neues Anwendungsgateway mit End-to-End-TLS-Verschlüsselung zu erstellen. Durch diese Aktion wird die TLS-Verschlüsselung für die Kommunikation zwischen dem Client und dem Anwendungsgateway aktiviert. Anschließend müssen Sie die Zertifikate für die Back-End-Server in den HTTP-Einstellungen der Liste der sicheren Empfänger hinzufügen. Durch diese Konfiguration wird die TLS-Verschlüsselung für die Kommunikation zwischen dem Anwendungsgateway und den Back-End-Servern aktiviert. Damit wird die End-to-End-TLS-Verschlüsselung eingerichtet.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Aktivieren der TLS-Terminierung beim Erstellen eines neuen Anwendungsgateways

Weitere Informationen finden Sie unter [Aktivieren der TLS-Terminierung beim Erstellen eines neuen Anwendungsgateways](./create-ssl-portal.md).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Hinzufügen von Authentifizierung/Stammzertifikaten für Back-End-Server

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

2. Wählen Sie im linken Menü **HTTP-Einstellungen** aus. Azure erstellt automatisch eine HTTP-Standardeinstellung ( **appGatewayBackendHttpSettings** ), wenn Sie das Anwendungsgateway erstellen. 

3. Wählen Sie **appGatewayBackendHttpSettings** aus.

4. Wählen Sie unter **Protokoll** die Option **HTTPS** aus. Daraufhin wird der Bereich für **Back-End-Authentifizierungszertifikate oder vertrauenswürdige Stammzertifikate** angezeigt.

5. Wählen Sie **Neu erstellen**.

6. Geben Sie im Feld **Name** einen geeigneten Namen ein.

7. Wählen Sie die Zertifikatsdatei im Feld **CER-Zertifikat hochladen** aus.

   Für Anwendungsgateways vom Typ Standard und WAF (v1) sollten Sie den öffentlichen Schlüssel des Back-End-Serverzertifikats im CER-Format hochladen.

   ![Hinzufügen des Zertifikats](./media/end-to-end-ssl-portal/addcert.png)

   Für Anwendungsgateways vom Typ Standard_v2 und WAF_v2 sollten Sie das Stammzertifikat des Back-End-Serverzertifikats im CER-Format hochladen. Wenn das Back-End-Zertifikat von einer bekannten Zertifizierungsstelle ausgestellt wird, können Sie das Kontrollkästchen **Bekanntes Zertifizierungsstellenzertifikat verwenden** aktivieren und müssen dann kein Zertifikat hochladen.

   ![Hinzufügen eines vertrauenswürdigen Stammzertifikats](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Stammzertifikat](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Wählen Sie **Speichern** aus.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Aktivieren von End-to-End-TLS für ein vorhandenes Anwendungsgateway

Sie müssen zunächst die TLS-Terminierung im Listener aktivieren, um ein vorhandenes Anwendungsgateway mit End-to-End-TLS-Verschlüsselung zu konfigurieren. Durch diese Aktion wird die TLS-Verschlüsselung für die Kommunikation zwischen dem Client und dem Anwendungsgateway aktiviert. Fügen Sie anschließend die Zertifikate für die Back-End-Server in den HTTP-Einstellungen der Liste der sicheren Empfänger hinzu. Durch diese Konfiguration wird die TLS-Verschlüsselung für die Kommunikation zwischen dem Anwendungsgateway und den Back-End-Servern aktiviert. Damit wird die End-to-End-TLS-Verschlüsselung eingerichtet.

Sie müssen einen Listener mit dem HTTPS-Protokoll und einem Zertifikat zum Aktivieren der TLS-Terminierung verwenden. Sie können einen vorhandenen Listener verwenden, der diese Bedingungen erfüllt, oder einen neuen Listener erstellen. Wenn Sie sich für die erste Option entscheiden, können Sie den folgenden Abschnitt zum Aktivieren der TLS-Terminierung in einem vorhandenen Anwendungsgateway überspringen und direkt mit dem Abschnitt „Hinzufügen von Authentifizierung/vertrauenswürdigen Stammzertifikaten für Back-End-Server“ fortfahren.

Wenn Sie sich für die zweite Option entscheiden, wenden Sie die Schritte im folgenden Verfahren an.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Aktivieren der TLS-Terminierung in einem vorhandenen Anwendungsgateway

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

2. Wählen Sie im linken Menü **Listener** aus.

3. Wählen Sie je nach Ihren Anforderungen Listener vom Typ **Basic** oder **Mehrere Standorte** aus.

4. Wählen Sie unter **Protokoll** die Option **HTTPS** aus. Ein Bereich für das **Zertifikat** wird angezeigt.

5. Laden Sie das PFX-Zertifikat hoch, das Sie für die TLS-Terminierung zwischen dem Client und dem Anwendungsgateway verwenden möchten.

   > [!NOTE]
   > Sie können zu Testzwecken ein selbstsigniertes Zertifikat verwenden. Dies wird jedoch nicht für Produktionsworkloads empfohlen, da sie schwieriger zu verwalten und nicht vollständig sicher sind. Weitere Informationen hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](./create-ssl-portal.md#create-a-self-signed-certificate).

6. Fügen Sie je nach Ihren Anforderungen weitere Einstellungen für den **Listener** hinzu.

7. Klicken Sie zum Speichern auf **OK**.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Hinzufügen von Authentifizierung/vertrauenswürdigen Stammzertifikaten für Back-End-Server

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

2. Wählen Sie im linken Menü **HTTP-Einstellungen** aus. Sie können Zertifikate in einer vorhandenen Back-End-HTTP-Einstellung in die Liste der sicheren Empfänger aufnehmen oder eine neue HTTP-Einstellung erstellen. (Im nächsten Schritt wird das Zertifikat für die Standard-HTTP-Einstellung **appGatewayBackendHttpSettings** der Liste der sicheren Empfänger hinzugefügt.)

3. Wählen Sie **appGatewayBackendHttpSettings** aus.

4. Wählen Sie unter **Protokoll** die Option **HTTPS** aus. Daraufhin wird der Bereich für **Back-End-Authentifizierungszertifikate oder vertrauenswürdige Stammzertifikate** angezeigt. 

5. Wählen Sie **Neu erstellen**.

6. Geben Sie im Feld **Name** einen geeigneten Namen ein.

7. Wählen Sie die Zertifikatsdatei im Feld **CER-Zertifikat hochladen** aus.

   Für Anwendungsgateways vom Typ Standard und WAF (v1) sollten Sie den öffentlichen Schlüssel des Back-End-Serverzertifikats im CER-Format hochladen.

   ![Hinzufügen des Zertifikats](./media/end-to-end-ssl-portal/addcert.png)

   Für Anwendungsgateways vom Typ Standard_v2 und WAF_v2 sollten Sie das Stammzertifikat des Back-End-Serverzertifikats im CER-Format hochladen. Wenn das Back-End-Zertifikat von einer bekannten Zertifizierungsstelle ausgestellt wird, können Sie das Kontrollkästchen **Bekanntes Zertifizierungsstellenzertifikat verwenden** aktivieren und müssen dann kein Zertifikat hochladen.

   ![Hinzufügen eines vertrauenswürdigen Stammzertifikats](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Webdatenverkehr mit einem Anwendungsgateway per Azure CLI](./tutorial-manage-web-traffic-cli.md)