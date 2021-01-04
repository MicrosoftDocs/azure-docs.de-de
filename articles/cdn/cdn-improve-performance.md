---
title: Verbessern der Leistung durch Komprimieren von Dateien in Azure CDN | Microsoft Docs
description: Erfahren Sie, wie Sie die Geschwindigkeit von Dateiübertragungen erhöhen und die Leistung beim Laden von Seiten verbessern, indem Sie Ihre Dateien in Azure CDN komprimieren.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: ceed62d466627d6a23554229bd6f4b96c674c7e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993668"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Verbessern der Leistung durch Komprimieren von Dateien in Azure CDN
Die Dateikomprimierung reduziert die Größe einer Datei, bevor sie vom Server gesendet wird, und ist eine einfache und effektive Methode zum Verbessern der Geschwindigkeit von Dateiübertragungen sowie der Seitenladeleistung. Die Dateikomprimierung reduziert die Bandbreitenkosten und steigert die Benutzerfreundlichkeit.

Es gibt zwei Methoden zur Aktivierung der Dateikomprimierung:

- Sie aktivieren die Komprimierung auf Ihrem Ursprungsserver. In diesem Fall leitet das Azure CDN die komprimierten Dateien weiter und übermittelt sie an die Clients, von denen sie angefordert werden.
- Aktivieren Sie die Komprimierung direkt auf den CDN-POP-Servern (*Komprimierung bei laufendem Betrieb*). In diesem Fall komprimiert CDN die Dateien und sendet sie an die Endbenutzer, auch wenn sie nicht vom Ursprungsserver komprimiert wurden.

> [!IMPORTANT]
> Es dauert eine gewisse Zeit, bis Änderungen an der Azure CDN-Konfiguration im gesamten Netzwerk verteilt wurden: 
> - Bei Profilen vom Typ **Azure CDN Standard von Microsoft** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
> - Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
> - Bei Profilen vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
> 
> Wenn Sie die Komprimierung für Ihren CDN-Endpunkt zum ersten Mal einrichten, sollten Sie jedoch 1–2 Stunden warten, um sicherzugehen, dass die Komprimierungseinstellungen an alle POPs verteilt wurden. Erst danach lohnt sich ggf. eine Problembehandlung.

## <a name="enabling-compression"></a>Aktivieren der Komprimierung
Die CDN-Tarife „Standard“ und „Premium“ bieten die gleiche Komprimierungsfunktion, jedoch mit einer anderen Benutzeroberfläche. Weitere Informationen zu den Unterschieden zwischen den CDN-Tarifen „Standard“ und „Premium“ finden Sie unter [Azure CDN Overview](cdn-overview.md) (Übersicht über Azure CDN).

### <a name="standard-cdn-profiles"></a>Standard-CDN-Profile 
> [!NOTE]
> Dieser Abschnitt gilt für die Profile **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Verizon** und **Azure CDN Standard von Akamai**.
> 
> 

1. Wählen Sie auf der Seite „CDN-Profil“ den CDN-Endpunkt aus, den Sie verwalten möchten.

    ![CDN-Profilendpunkte](./media/cdn-file-compression/cdn-endpoints.png)

    Die Seite „CDN-Endpunkt“ wird geöffnet.
2. Wählen Sie **Komprimierung** aus.

    ![Screenshot eines Endpunkts mit ausgewählter Komprimierung im Portalmenü](./media/cdn-file-compression/cdn-compress-select-std.png)

    Die Seite „Komprimierung“ wird geöffnet.
3. Wählen Sie **On** (Ein) aus, um die Komprimierung zu aktivieren.

    ![Screenshot der Aktivierung der Komprimierung](./media/cdn-file-compression/cdn-compress-standard.png)
4. Verwenden Sie die Standard-MIME-Typen, oder ändern Sie die Liste, indem Sie MIME-Typen hinzufügen oder entfernen.

   > [!TIP]
   > Obwohl es möglich ist, die Komprimierung für komprimierte Formate zu aktivieren, wird dies nicht empfohlen. Beispiele sind ZIP, MP3, MP4 und JPG.
   > 

   > [!NOTE]
   > Das Ändern der Standardliste der MIME-Typen wird zurzeit in Azure CDN Standard von Microsoft nicht unterstützt.
   > 

5. Klicken Sie auf **Speichern**, nachdem Sie die Änderungen vorgenommen haben.

### <a name="premium-cdn-profiles"></a>Premium-CDN-Profile
> [!NOTE]
> Dieser Abschnitt gilt nur für **Azure CDN Premium von Verizon**-Profile.
> 

1. Klicken Sie auf der Seite „CDN-Profil“ auf **Manage** (Verwalten).

    ![Klicken auf die Verwaltungsoption für das CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Das CDN-Verwaltungsportal wird geöffnet.
2. Zeigen Sie auf die Registerkarte **HTTP Groß** und dann auf das Flyout **Cacheeinstellungen**. Wählen Sie **Komprimierung** aus.

    ![Auswahl der CDN-Komprimierung](./media/cdn-file-compression/cdn-compress-select.png)

    Die Komprimierungsoptionen werden angezeigt.

    ![Optionen für die CDN-Dateikomprimierung](./media/cdn-file-compression/cdn-compress-files.png)
3. Aktivieren Sie die Komprimierung, indem Sie **Compression Enabled** (Komprimierung aktiviert) auswählen. Geben Sie im Feld **Dateitypen** die zu komprimierenden MIME-Typen als durch Trennzeichen getrennte Liste (ohne Leerzeichen) ein.

   > [!TIP]
   > Obwohl es möglich ist, die Komprimierung für komprimierte Formate zu aktivieren, wird dies nicht empfohlen. Beispiele sind ZIP, MP3, MP4 und JPG.
   > 

4. Klicken Sie auf **Aktualisieren**, nachdem Sie die Änderungen vorgenommen haben.

## <a name="compression-rules"></a>Komprimierungsregeln

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard aus Microsoft-Profilen

Bei **Azure CDN Standard von Microsoft**-Profilen sind alle Dateien zur Komprimierung zugelassen. Eine Datei ist für die Komprimierung geeignet, wenn sie folgende Bedingungen erfüllt:
- Sie muss einen MIME-Typ aufweisen, der [für die Komprimierung konfiguriert](#enabling-compression) wurde.
- Größer als 1 KB
- Kleiner als 8 MB

Diese Profile unterstützen die folgenden Komprimierungscodierungen:
- GZIP (GNU Zip)
- Brotli 

Falls die Anforderung mehrere Komprimierungstypen unterstützt, hat die Brotli-Komprimierung Vorrang.

Wenn in einer Anforderung für eine Ressource Gzip-Komprimierung angegeben ist und die Anforderung zu einem Cachefehler führt, führt Azure CDN die Gzip-Komprimierung der Ressource direkt auf dem POP-Server durch. Anschließend wird die komprimierte Datei aus dem Cache bereitgestellt.

Wenn der Ursprung die Codierung für segmentierte Übertragung (CTE) verwendet, um komprimierte Daten an das CDN-POP zu senden, werden Antwortgrößen von mehr als 8 MB nicht unterstützt. 

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN von Verizon-Profile

Für **Azure CDN Standard von Verizon**-Profile und **Azure CDN Premium von Verizon**-Profile werden nur zugelassene Dateien komprimiert. Eine Datei ist für die Komprimierung geeignet, wenn sie folgende Bedingungen erfüllt:
- Größer als 128 Bytes
- Kleiner als 3 MB

Diese Profile unterstützen die folgenden Komprimierungscodierungen:
- GZIP (GNU Zip)
- VERKLEINERN
- BZIP2
- Brotli 

Falls die Anforderung mehrere Komprimierungstypen unterstützt, haben diese Komprimierungstypen Vorrang vor der Brotli-Komprimierung.

Wenn in einer Anforderung für eine Ressource die Brotli-Komprimierung angegeben ist (HTTP-Header `Accept-Encoding: br`) und die Anforderung zu einem Cachefehler führt, wird die Brotli-Komprimierung der Ressource durch Azure CDN direkt auf dem POP-Server durchgeführt. Anschließend wird die komprimierte Datei aus dem Cache bereitgestellt.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard von Akamai-Profile

Bei **Azure CDN Standard von Akamai**-Profilen sind alle Dateien zur Komprimierung zugelassen. Eine Datei muss allerdings einen MIME-Typ aufweisen, der [für die Komprimierung konfiguriert](#enabling-compression) wurde.

Diese Profile unterstützen nur die GZIP-Komprimierungscodierung. Wenn ein Profilendpunkt eine GZIP-codierte Datei anfordert, gilt die Anforderung immer für den Ursprungsserver – unabhängig von der Clientanforderung. 

## <a name="compression-behavior-tables"></a>Tabellen zum Komprimierungsverhalten
Die folgenden Tabellen beschreiben das Verhalten der Azure CDN-Komprimierung für jedes Szenario:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprimierung deaktiviert oder Datei nicht zur Komprimierung geeignet
| Vom Client angefordertes Format (über Accept-Encoding-Header) | Format der zwischengespeicherten Datei | CDN-Antwort an den Client | Hinweise&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Compressed |Compressed |Compressed | |
| Compressed |Nicht komprimiert |Nicht komprimiert | |
| Compressed |Nicht zwischengespeichert |Komprimiert oder nicht komprimiert |Die Antwort vom Ursprungsserver bestimmt, ob CDN eine Komprimierung durchführt. |
| Nicht komprimiert |Compressed |Nicht komprimiert | |
| Nicht komprimiert |Nicht komprimiert |Nicht komprimiert | |
| Nicht komprimiert |Nicht zwischengespeichert |Nicht komprimiert | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Komprimierung aktiviert und Datei zur Komprimierung geeignet
| Vom Client angefordertes Format (über Accept-Encoding-Header) | Format der zwischengespeicherten Datei | CDN-Antwort an den Client | Notizen |
| --- | --- | --- | --- |
| Compressed |Compressed |Compressed |CDN transcodiert zwischen unterstützten Formaten. |
| Compressed |Nicht komprimiert |Compressed |CDN führt eine Komprimierung durch. |
| Compressed |Nicht zwischengespeichert |Compressed |CDN führt eine Komprimierung durch, wenn der Ursprungsserver eine nicht komprimierte Datei zurückgibt. <br/>**Azure CDN von Verizon** übergibt die nicht komprimierte Datei bei der ersten Anforderung, komprimiert anschließend die Datei und speichert sie für nachfolgende Anforderungen zwischen. <br/>Dateien mit dem Header `Cache-Control: no-cache` werden nie komprimiert. |
| Nicht komprimiert |Compressed |Nicht komprimiert |CDN führt eine Dekomprimierung durch. |
| Nicht komprimiert |Nicht komprimiert |Nicht komprimiert | |
| Nicht komprimiert |Nicht zwischengespeichert |Nicht komprimiert | |

## <a name="media-services-cdn-compression"></a>Media Services-CDN-Komprimierung
Für Endpunkte, die für Media Services-CDN-Streaming aktiviert sind, ist die Komprimierung standardmäßig für die folgenden MIME-Typen aktiviert: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>Weitere Informationen
* [Problembehandlung bei der CDN-Dateikomprimierung](cdn-troubleshoot-compression.md)    

