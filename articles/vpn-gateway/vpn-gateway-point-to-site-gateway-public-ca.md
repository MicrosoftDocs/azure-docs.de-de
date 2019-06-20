---
title: Übergang zu öffentlichen Zertifizierungsstellenzertifikaten für P2S-Gateways | Azure VPN Gateway | Microsoft-Dokumentation
description: In diesem Artikel wird die Umstellung auf die neuen Zertifikate von öffentlichen Zertifizierungsstellen für P2S-Gateways erläutert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: cherylmc
ms.openlocfilehash: 1d45e1a5e4053ead4330967c5e250c0797c19fe7
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827469"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Übergang von einem Gatewayzertifikat einer öffentlichen Zertifizierungsstelle für P2S

Azure VPN Gateway stellt für P2S-Verbindungen keine selbstsignierten Zertifikate auf Azure-Ebene für Gateways mehr aus. Ausgestellte Zertifikate werden nun von einer öffentlichen Zertifizierungsstelle signiert. Allerdings verwenden einige der älteren Gateways ggf. weiterhin selbstsignierte Zertifikate. Diese selbstsignierten Zertifikate stehen kurz vor ihrem Ablaufdatum und müssen auf Zertifikate von öffentlichen Zertifizierungsstellen umgestellt werden.

>[!NOTE]
> * Selbstsignierte Zertifikate, die für die P2S-Clientauthentifizierung verwendet werden, sind von dieser Azure-Zertifikatänderung nicht betroffen. Sie können selbstsignierte Zertifikate weiterhin wie gewohnt ausstellen und verwenden.
>

Die Zertifikate in diesem Kontext sind ein zusätzliches Zertifikat auf der Azure-Ebene. Es handelt sich dabei nicht um die Zertifikatketten, die Sie beim Generieren Ihrer eigenen selbstsignierten Stammzertifikate und Clientzertifikate für die Authentifizierung verwenden. Diese Zertifikate sind nicht von der Änderung betroffen und laufen an den von Ihnen generierten Ablaufdaten ab.

Bisher musste das selbstsignierte Zertifikat für das Gateway (im Hintergrund von Azure ausgestellt) alle 18 Monate aktualisiert werden. VPN-Clientkonfigurationsdateien mussten anschließend generiert und für alle P2S-Clients erneut bereitgestellt werden. Durch die Umstellung auf Zertifikate von öffentlichen Zertifizierungsstellen entfällt diese Einschränkung. Neben der Umstellung bei Zertifikaten bietet diese Änderung auch Plattformverbesserungen, bessere Metriken und mehr Stabilität.

Von dieser Änderung sind nur ältere Gateways betroffen. Wenn Ihr Gatewayzertifikat umgestellt werden muss, erhalten Sie im Azure-Portal eine (Popup-) Benachrichtigung. Sie können anhand der in diesem Artikel beschriebenen Schritte überprüfen, ob Ihr Gateway betroffen ist.

> [!IMPORTANT]
> Die Umstellung auf öffentliche ZS-Zertifikate wurde am 12. Mai 2019 abgeschlossen. **Dieses Dokument wird am 31. Mai 2019 entfernt.**

## <a name="1-verify-your-certificate"></a>1. Überprüfen Ihres Zertifikats

### <a name="resource-manager"></a>Ressourcen-Manager

1. Prüfen Sie, ob Sie von diesem Update betroffen sind. Laden Sie Ihre aktuelle VPN-Client-Konfiguration unter Befolgung der Anweisungen in [diesem Artikel](point-to-site-vpn-client-configuration-azure-cert.md) herunter.

2. Öffnen oder extrahieren Sie die ZIP-Datei, und wechseln Sie zum Ordner „Generic“. Im Ordner „Generic“ finden Sie zwei Dateien, von denen eine *VPNSettings.xml* ist.
3. Öffnen Sie Datei *VPNSettings.xml* in einem beliebigen XML-Viewer/-Editor. Suchen Sie in der XML-Datei nach den folgenden Feldern:

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Wenn „DigiCert Global Root CA“ für *ServerCertRotCn* und *ServerCertIssuerCn* angezeigt wird, sind Sie von dieser Aktualisierung nicht betroffen und müssen die Schritte in diesem Artikel nicht durchführen. Wenn jedoch etwas anderes angezeigt wird, unterliegt Ihr Gatewayzertifikat der Aktualisierung und wird umgestellt.

### <a name="classic"></a>Klassisch

1. Navigieren Sie auf einem Clientcomputer zum Pfad `%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`. Im Ordner für die Gateway-ID können Sie das Zertifikat anzeigen.
2. Stellen Sie auf der Registerkarte „Allgemein“ für das Zertifikat sicher, dass „DigiCert Global Root CA“ die ausstellende Zertifizierungsstelle ist. Wenn eine andere als diese ausstellende Zertifizierungsstelle angezeigt wird, ist Ihr Gatewayzertifikat Teil des Updates und wird umgestellt.

## <a name="2-check-certificate-transition-schedule"></a>2. Überprüfen des Zeitplans für die Zertifikatsumstellung

Wenn Ihr Zertifikat der Aktualisierung unterliegt, wird Ihr Gatewayzertifikat umgestellt. Unter dem Hinweis **Wichtig** finden Sie Umstellungszeiten. Nach der Aktualisierung können P2S-Clients mit ihrem alten Profil keine Verbindung mehr herstellen. Sie müssen neue VPN-Clientprofile generieren und diese auf den Clients installieren.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Generieren von VPN-Clientkonfigurationsprofilen

Sobald das Zertifikat umgestellt wurde, laden Sie das neue VPN-Profil (mit den VPN-Clientkonfigurationsdateien) aus dem Azure-Portal herunter. Anweisungen finden Sie unter [Erstellen und Installieren von Konfigurationsdateien für VPN-Clients](point-to-site-vpn-client-configuration-azure-cert.md). Sie müssen keine neuen Clientzertifikate generieren.

## <a name="4-deploy-vpn-client-profile"></a>4. Bereitstellen des VPN-Clientprofils

Stellen Sie das neue Profil für alle Punkt-zu-Standort-VPN-Clients bereit. VPN-Clients verlieren die Konnektivität, bis das neue VPN-Profil für Punkt-zu-Standort-Verbindungen heruntergeladen und auf Clientgeräten bereitgestellt wurde. Die Clientzertifikate, die bereits auf VPN-Clientcomputern installiert sind, müssen nicht erneut ausgestellt werden.

## <a name="5-connect-the-vpn-client"></a>5. Herstellen der Verbindung zwischen VPN-Client und Azure

Stellen Sie wie gewohnt eine Verbindung zwischen dem VPN-Client und Azure her.
