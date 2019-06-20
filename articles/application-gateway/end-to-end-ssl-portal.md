---
title: 'Schnellstart: Konfigurieren der End-to-End-SSL-Verschlüsselung mit Azure Application Gateway im Azure-Portal | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das Azure-Portal zum Erstellen einer Azure Application Gateway-Instanz mit End-to-End-SSL-Verschlüsselung verwenden.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: bd165f81b45e3ae0c121fb8876ed88e68d493195
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64946798"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurieren von End-to-End-SSL mit Application Gateway im Azure-Portal

In diesem Artikel erfahren Sie, wie Sie das Azure-Portal zum Konfigurieren der End-to-End-SSL-Verschlüsselung mit einer Application Gateway v1-SKU verwenden.  

> [!NOTE]
> Zum Aktivieren der End-to-End-Konfiguration mit einer Application Gateway v2-SKU sind vertrauenswürdige Stammzertifikate erforderlich. Die Unterstützung für das Hinzufügen vertrauenswürdiger Stammzertifikate im Portal ist noch nicht verfügbar. Daher sollten Sie bei Verwendung einer v2-SKU die Anweisungen unter [Konfigurieren von End-to-End-SSL mithilfe von PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) befolgen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

Zum Konfigurieren von End-to-End-SSL mit einem Anwendungsgateway wird ein Zertifikat für das Gateway benötigt. Außerdem sind weitere Zertifikate für die Back-End-Server erforderlich. Das Gatewayzertifikat wird zum Ableiten eines symmetrischen Schlüssels gemäß der Spezifikation des SSL-Protokolls verwendet. Der symmetrische Schlüssel wird dann zum Verschlüsseln und Entschlüsseln des Datenverkehrs mit dem Gateway verwendet. Für die End-to-End-SSL-Verschlüsselung muss das Back-End der Whitelist des Anwendungsgateways hinzugefügt werden. Hierzu laden Sie das öffentliche Zertifikat der Back-End-Server, auch bekannt als Authentifizierungszertifikate, auf das Anwendungsgateway hoch. Durch das Hinzufügen des Zertifikats wird sichergestellt, dass das Anwendungsgateway nur mit bekannten Back-End-Instanzen kommuniziert. Außerdem wird auf diese Weise die End-to-End-Kommunikation gesichert.

Weitere Informationen finden Sie unter [SSL termination and end-to-end SSL (Übersicht über End-to-End-SSL mit Application Gateway)](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Erstellen eines neuen Anwendungsgateways mit End-to-End-SSL

Sie müssen zunächst die SSL-Beendigung beim Erstellen eines neuen Anwendungsgateways aktivieren, um ein neues Anwendungsgateway mit End-to-End-SSL-Verschlüsselung zu erstellen. Dadurch wird die SSL-Verschlüsselung für die Kommunikation zwischen dem Client und dem Anwendungsgateway aktiviert. Anschließend müssen Sie die Zertifikate für die Back-End-Server in den HTTP-Einstellungen in die Whitelist aufnehmen, um die SSL-Verschlüsselung für die Kommunikation zwischen dem Anwendungsgateway und den Back-End-Servern zu aktivieren, wodurch die End-to-End-SSL-Verschlüsselung ermöglicht wird.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Aktivieren der SSL-Beendigung beim Erstellung eines neuen Anwendungsgateways

Informationen zum Aktivieren der SSL-Beendigung beim Erstellen eines neuen Anwendungsgateways finden Sie unter [Konfigurieren eines Anwendungsgateways mit SSL-Beendigung mithilfe des Azure-Portals](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Aufnehmen der Zertifikate für Back-End-Server in die Whitelist

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

2. Klicken Sie im linken Menü auf **HTTP-Einstellungen**. Azure erstellt automatisch eine HTTP-Standardeinstellung (**appGatewayBackendHttpSettings**), wenn Sie das Anwendungsgateway erstellen. 

3. Wählen Sie **appGatewayBackendHttpSettings** aus.

4. Wählen Sie unter **Protokoll** die Option **HTTPS** aus. Daraufhin wird der Bereich **Back-End-Authentifizierungszertifikate** angezeigt. 

5. Klicken Sie unter **Back-End-Authentifizierungszertifikate** auf **Neu erstellen**.

6. Geben Sie einen passenden **Namen** ein.

7. Laden Sie das Zertifikat mithilfe des Felds **CER-Zertifikat hochladen** hoch. ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Das in diesem Schritt bereitgestellte Zertifikat sollte dem öffentlichen Schlüssel des PFX-Zertifikats entsprechen, das auf dem Back-End vorliegt. Exportieren Sie das auf dem Back-End-Server im CER-Format (Claim, Evidence, and Reasoning) installierte Zertifikat (nicht das Stammzertifikat), und verwenden Sie es für diesen Schritt. Durch diesen Schritt wird das Back-End beim Anwendungsgateway auf die Whitelist gesetzt.

8. Wählen Sie **Speichern** aus.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Aktivieren von End-to-End-SSL für vorhandene Anwendungsgateways

Sie müssen zunächst die SSL-Beendigung im Listener aktivieren, um ein vorhandenes Anwendungsgateway mit End-to-End-SSL-Verschlüsselung zu konfigurieren. Dadurch wird die SSL-Verschlüsselung für die Kommunikation zwischen dem Client und dem Anwendungsgateway aktiviert. Anschließend müssen Sie die Zertifikate für die Back-End-Server in den HTTP-Einstellungen in die Whitelist aufnehmen, um die SSL-Verschlüsselung für die Kommunikation zwischen dem Anwendungsgateway und den Back-End-Servern zu aktivieren, wodurch die End-to-End-SSL-Verschlüsselung ermöglicht wird.

Sie müssen einen Listener mit HTTPS-Protokoll und -Zertifikat zum Aktivieren der SSL-Beendigung verwenden. Sie können das Protokoll eines vorhandenen Listeners nicht ändern. Daher können Sie entweder einen vorhandenen Listener mit HTTPS-Protokoll und -Zertifikat verwenden oder einen neuen Listener erstellen. Wenn Sie sich für ersteres entscheiden, können Sie die folgenden Schritte zum **Aktivieren der SSL-Beendigung in einem vorhandenen Anwendungsgateway** überspringen und mit dem Abschnitt **Aufnehmen der Zertifikate für Back-End-Server in die Whitelist** fortfahren. Wenn Sie sich für letzteres entschieden haben, verwenden Sie diese Schritte.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Aktivieren der SSL-Beendigung in einem vorhandenen Anwendungsgateway

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

2. Klicken Sie im linken Menü auf **Listener**.

3. Wählen Sie nach Bedarf einen **einfachen** Listener oder einen Listener **für mehrere Standorte** aus.

4. Wählen Sie unter **Protokoll** die Option **HTTPS** aus. Daraufhin wird der Bereich **Zertifikat** angezeigt.

5. Laden Sie das PFX-Zertifikat hoch, das Sie für die SSL-Beendigung zwischen dem Client und dem Anwendungsgateway verwenden möchten.

   > [!NOTE]
   > Sie können zu Testzwecken ein selbstsigniertes Zertifikat verwenden. Für Produktionsworkloads sollten Sie jedoch nie selbstsignierte Zertifikate verwenden. Hier finden Sie Informationen zum [Erstellen eines selbstsignierten Zertifikats](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Fügen Sie nach Bedarf weitere Einstellungen für den **Listener** hinzu.

7. Klicken Sie zum Speichern auf **OK**.

### <a name="whitelist-certificates-for-backend-servers"></a>Aufnehmen der Zertifikate für Back-End-Server in die Whitelist

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

2. Klicken Sie im linken Menü auf **HTTP-Einstellungen**. Sie können entweder Zertifikate in einer vorhandenen Back-End-HTTP-Einstellung in die Whitelist aufnehmen oder eine neue HTTP-Einstellung erstellen. Im folgenden Schritt wird das Zertifikat für die HTTP-Standardeinstellung (**appGatewayBackendHttpSettings**) in die Whitelist aufgenommen.

3. Wählen Sie **appGatewayBackendHttpSettings** aus.

4. Wählen Sie unter **Protokoll** die Option **HTTPS** aus. Daraufhin wird der Bereich **Back-End-Authentifizierungszertifikate** angezeigt. 

5. Klicken Sie unter **Back-End-Authentifizierungszertifikate** auf **Neu erstellen**.

6. Geben Sie einen passenden **Namen** ein.

7. Laden Sie das Zertifikat mithilfe des Felds **CER-Zertifikat hochladen** hoch. ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Das in diesem Schritt bereitgestellte Zertifikat sollte dem öffentlichen Schlüssel des PFX-Zertifikats entsprechen, das auf dem Back-End vorliegt. Exportieren Sie das auf dem Back-End-Server im CER-Format (Claim, Evidence, and Reasoning) installierte Zertifikat (nicht das Stammzertifikat), und verwenden Sie es für diesen Schritt. Durch diesen Schritt wird das Back-End beim Anwendungsgateway auf die Whitelist gesetzt.

8. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Webdatenverkehr mit einem Anwendungsgateway per Azure CLI](./tutorial-manage-web-traffic-cli.md)
