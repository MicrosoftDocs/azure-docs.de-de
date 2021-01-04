---
title: Problembehandlung bei der Azure CDN-Dateikomprimierung | Microsoft Docs
description: Erfahren Sie, wie Sie Probleme mit der Dateikomprimierung im Azure Content Delivery Network beheben können. In diesem Artikel werden verschiedene mögliche Ursachen behandelt.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996150"
---
# <a name="troubleshooting-cdn-file-compression"></a>Problembehandlung bei der CDN-Dateikomprimierung
Dieser Artikel unterstützt Sie bei der Behandlung von Problemen mit der [CDN-Dateikomprimierung](cdn-improve-performance.md).

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.

## <a name="symptom"></a>Symptom
Die Komprimierung für Ihren Endpunkt ist aktiviert, die Dateien werden aber nicht komprimiert zurückgegeben.

> [!TIP]
> Um zu überprüfen, ob Ihre Dateien komprimiert zurückgegeben werden, müssen Sie ein Tool wie [Fiddler](https://www.telerik.com/fiddler) oder die [Entwicklertools](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) Ihres Browsers verwenden.  Überprüfen Sie die HTTP-Antwortheader, die mit Ihrem zwischengespeicherten CDN.Inhalt zurückgegeben werden.  Wenn ein Header mit dem Namen `Content-Encoding` mit einem Wert von **gzip**, **bzip2** oder **deflate** vorhanden ist, werden Ihre Inhalte komprimiert.
> 
> ![Content-Encoding-Header](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Ursache
Es gibt mehrere mögliche Ursachen, darunter folgende:

* Der angeforderte Inhalt eignet sich nicht zur Komprimierung.
* Die Komprimierung ist für den angeforderten Dateityp nicht aktiviert.
* Die HTTP-Anforderung enthielt keinen Header, um einen gültigen Komprimierungstyp anzufordern.
* Der Ursprung sendet segmentierten Inhalt.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
> [!TIP]
> Genauso wie beim Bereitstellen neuer Endpunkte dauert es eine gewisse Zeit, bis Änderungen an der CDN-Konfiguration im gesamten Netzwerk verteilt sind.  Änderungen werden in der Regel innerhalb von 90 Minuten angewendet.  Wenn Sie die Komprimierung für Ihren CDN-Endpunkt zum ersten Mal einrichten, sollten Sie jedoch 1–2 Stunden warten, um sicherzugehen, dass die Komprimierungseinstellungen an alle POPs verteilt wurden. 
> 
> 

### <a name="verify-the-request"></a>Überprüfen der Anforderung
Zuerst sollten Sie eine schnelle Integritätsprüfung der Anforderung durchführen.  Sie können die [Entwicklertools](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) Ihres Browsers verwenden, um die Anforderungen anzuzeigen.

* Überprüfen Sie die Anforderung, die an die Endpunkt-URL gesendet wird ( `<endpointname>.azureedge.net`), nicht Ihre ursprüngliche Anforderung.
* Überprüfen Sie, ob die Anforderung einen **Accept-Encoding**-Header enthält und ob der Wert für diesen Header **gzip**, **deflate** oder **bzip2** enthält.

> [!NOTE]
> **Azure CDN von Akamai**-Profile unterstützen nur **gzip**-Codierung.
> 
> 

![CDN-Anforderungsheader](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Überprüfen der Komprimierungseinstellungen (CDN-Standardprofile)
> [!NOTE]
> Dieser Schritt gilt, wenn Ihr CDN-Profil ein Profil vom Typ **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Verizon** oder **Azure CDN Standard von Akamai** ist. 
> 
> 

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Endpunkt, und klicken Sie auf die Schaltfläche **Konfigurieren** .

* Überprüfen Sie, ob die Komprimierung aktiviert ist.
* Überprüfen Sie, ob der MIME-Typ für den zu komprimierenden Inhalt in der Liste der komprimierten Formate enthalten ist.

![CDN-Komprimierungseinstellungen](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Überprüfen der Komprimierungseinstellungen (CDN-Premium-Profil)
> [!NOTE]
> Dieser Schritt gilt nur, wenn Ihr CDN-Profil ein Profil vom Typ **Azure CDN Premium von Verizon** ist.
> 
> 

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Endpunkt, und klicken Sie auf die Schaltfläche **Verwalten** .  Das zusätzliche Portal wird geöffnet.  Zeigen Sie auf die Registerkarte **HTTP Groß** und dann auf das Flyout **Cacheeinstellungen**.  Klicken Sie auf **Datenkomprimierung**. 

* Überprüfen Sie, ob die Komprimierung aktiviert ist.
* Überprüfen Sie, ob die Liste **Dateitypen** eine durch Trennzeichen getrennte Liste von MIME-Typen ohne Leerzeichen enthält.
* Überprüfen Sie, ob der MIME-Typ für den zu komprimierenden Inhalt in der Liste der komprimierten Formate enthalten ist.

![CDN-Komprimierungseinstellungen, Premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Sicherstellen, dass der Inhalt zwischengespeichert wird (CDN-Profile von Verizon)
> [!NOTE]
> Dieser Schritt gilt nur, wenn Ihr CDN-Profil ein Profil vom Typ **Azure CDN Standard von Verizon** oder **Azure CDN Premium von Verizon** ist.
> 
> 

Überprüfen Sie mithilfe der Entwicklertools Ihres Browsers die Antwortheader, um sicherzustellen, dass die Datei in der Region zwischengespeichert wird, in der sie angefordert wird.

* Überprüfen Sie den Antwortheader **Server** .  Dieser Header sollte wie im folgenden Beispiel das Format **Plattform (POP-/Server-ID)** aufweisen.
* Überprüfen Sie den Antwortheader **X-Cache** .  Dieser Header sollte **HIT** lauten.  

![CDN-Antwortheader](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Überprüfen, ob die Datei die Größenanforderungen erfüllt (CDN-Profile von Verizon)
> [!NOTE]
> Dieser Schritt gilt nur, wenn Ihr CDN-Profil ein Profil vom Typ **Azure CDN Standard von Verizon** oder **Azure CDN Premium von Verizon** ist.
> 
> 

Um sich für die Komprimierung zu eignen, muss eine Datei folgende Größenanforderungen erfüllen:

* Größer als 128 Bytes (Content-Length: 128)
* Kleiner als 3 MB

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Überprüfen Sie die Anforderung auf dem Ursprungsserver auf einen **Über** -Header.
Der **Über** -HTTP-Header informiert den Webserver darüber, dass die Anforderung von einem Proxyserver übergeben wird.  Microsoft IIS-Webserver komprimieren Antworten standardmäßig nicht, wenn die Anforderung einen **Über** -Header enthält.  Führen Sie folgende Schritte aus, um dieses Verhalten außer Kraft zu setzen:

* **IIS 6**: [Legen Sie in den IIS-Metabasiseigenschaften „HcNoCompressionForProxies="FALSE"“ fest](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 oder höher**: [Legen Sie in der Serverkonfiguration sowohl für **noCompressionForHttp10** als auch für **noCompressionForProxies** die Option „False“ fest.](https://www.iis.net/configreference/system.webserver/httpcompression)

