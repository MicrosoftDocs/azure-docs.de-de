---
title: Optimierung großer Dateidownloads mit Azure CDN
description: Erfahren Sie, wie das Herunterladen großer Dateien in Azure Content Delivery Network optimiert werden kann. Dieser Artikel umfasst verschiedene Szenarien.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: ed5768e89482d32bb140e9ba7064de2d20809892
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020720"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimierung großer Dateidownloads mit Azure CDN

Dateien mit Inhalten aus dem Internet werden aufgrund von immer umfangreicheren Funktionen, verbesserter Grafik und umfassenden Medieninhalten immer größer. Dieses Wachstum wird durch diverse Faktoren vorangetrieben – etwa durch eine bessere Breitbandversorgung, durch größere kostengünstige Speichergeräte sowie durch die vermehrte Nutzung von HD-Videos und Geräten mit Internetverbindung (IoT). Für große Dateien bedarf es daher eines schnelleren und effizienteren Übermittlungsverfahrens, um eine reibungslose und angemessene Kundenerfahrung zu gewährleisten.

Für die Bereitstellung von großen Dateien müssen mehrere Anforderungen erfüllt werden. Erstens kann die durchschnittliche Zeit zum Herunterladen einer großen Datei von Bedeutung sein, da Anwendungen ggf. nicht alle Daten sequenziell herunterladen. In einigen Fällen laden Anwendungen den letzten Teil der Datei unter Umständen vor dem ersten Teil herunter. Wenn nur eine kleine Datenmenge einer Datei angefordert wird oder ein Benutzer einen Download anhält, ist der Download ggf. nicht erfolgreich. Außerdem kann der Download verzögert werden, bis das Content Delivery Network (CDN) die gesamte Datei vom Ursprungsserver erhalten hat. 

Zweitens bestimmt die Wartezeit zwischen dem Computer eines Benutzers und der Datei die Geschwindigkeit, mit der Inhalte angezeigt werden können. Darüber hinaus können sich auch Netzwerküberlastungen und Kapazitätsprobleme auf den Durchsatz auswirken. Je größer die Entfernungen zwischen Servern und Benutzern sind, desto größer ist die Wahrscheinlichkeit für das Auftreten von Paketverlusten. Dies führt zu einer Verringerung der Qualität. Die reduzierte Qualität aufgrund von begrenztem Durchsatz und vermehrten Paketverlusten kann dazu führen, dass sich die Wartezeit bis zum Abschluss eines Dateidownloads verlängert. 

Drittens werden viele große Dateien nicht vollständig übermittelt. Benutzer brechen einen Download ggf. nach der Hälfte ab oder sehen sich nur die ersten Minuten eines langen MP4-Videos an. Aus diesem Grund möchten Unternehmen, die Software und Medien liefern, möglichst nur den Teil einer Datei bereitstellen, der angefordert wurde. Durch die effiziente Verteilung der angeforderten Teile reduziert sich der ausgehende Datenverkehr vom Ursprungsserver. Außerdem führt eine effiziente Verteilung dazu, dass der Arbeitsspeicher- und E/A-Druck auf dem Ursprungsserver hoch ist. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimieren der Übermittlung großer Dateien mit Azure CDN von Microsoft

Endpunkte vom Typ **Azure CDN Standard von Microsoft** übermitteln große Dateien ohne Beschränkung der Dateigröße. Die zusätzlichen Features werden standardmäßig aktiviert, um die Bereitstellung von großen Dateien zu beschleunigen.

### <a name="object-chunking"></a>Objektblockerstellung 

Bei **Azure CDN Standard von Microsoft** kommt eine Technik namens „Objektblockerstellung“ zum Einsatz. Wenn eine große Datei angefordert wird, ruft das CDN kleinere Teile der Datei vom Ursprung ab. Nachdem der CDN-POP-Server eine vollständige oder auf einen Bytebereich beschränkte Dateianforderung empfangen hat, fordert der CDN-Edgeserver die Datei beim Ursprung in Blöcken zu jeweils 8 MB an. 

Nachdem der Block im CDN-Edgebereich angekommen ist, wird er zwischengespeichert und sofort für den Benutzer bereitgestellt. Das CDN ruft den nächsten Block dann parallel dazu ab. Durch dieses Vorab-Abrufen wird sichergestellt, dass der Inhalt dem Benutzer immer einen Block voraus ist, sodass sich die Wartezeit reduziert. Dieser Prozess wird fortgesetzt, bis die gesamte Datei heruntergeladen wurde (falls angefordert), alle Bytebereiche verfügbar sind (falls angefordert) oder der Client die Verbindung beendet. 

Weitere Informationen zur Bytebereichsanforderung finden Sie unter [RFC 7233](https://tools.ietf.org/html/rfc7233).

Das CDN speichert alle Blöcke zwischen, sobald sie eingetroffen sind. Es ist nicht erforderlich, die gesamte Datei im CDN-Cache zwischenzuspeichern. Nachfolgende Anforderungen für die Datei- oder Bytebereiche werden aus dem CDN-Cache bereitgestellt. Wenn nicht alle Blöcke im CDN zwischengespeichert werden, wird das Vorab-Abrufen verwendet, um Blöcke vom Ursprung anzufordern. Diese Optimierung setzt voraus, dass der Ursprungsserver Bytebereichsanforderungen unterstützt. Wenn der Ursprungsserver keine Bytebereichsanforderungen unterstützt, tritt bei Anforderungen von mehr als 8 MB Daten ein Fehler auf. 

### <a name="conditions-for-large-file-optimization"></a>Bedingungen für die Optimierung großer Dateien
Es gilt keine Beschränkung in Bezug auf die maximale Dateigröße.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimieren der Übermittlung großer Dateien mit Azure CDN von Verizon

Endpunkte vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** übermitteln große Dateien ohne Beschränkung der Dateigröße. Die zusätzlichen Features werden standardmäßig aktiviert, um die Bereitstellung von großen Dateien zu beschleunigen.

### <a name="complete-cache-fill"></a>Complete Cache Fill

Mit dem Feature „Complete Cache Fill“ kann das CDN eine Datei im Cache ablegen, wenn die erste Anforderung vorzeitig beendet wird oder verloren geht. 

Complete Cache Fill ist für große Assets am nützlichsten. Normalerweise werden diese von Benutzern nicht von Anfang bis Ende heruntergeladen. Es wird ein progressiver Download verwendet. Standardmäßig wird der Edgeserver gezwungen, einen Hintergrundabruf des Assets vom Ursprungsserver zu initiieren. Anschließend befindet sich das Asset im lokalen Cache des Edgeservers. Nachdem sich das vollständige Objekt im Cache befindet, kann der Edgeserver Bytebereichsanforderungen an das CDN für das zwischengespeicherte Objekt beantworten.

Das Standardverhalten kann über die Regel-Engine in **Azure CDN Premium von Verizon** deaktiviert werden.

### <a name="peer-cache-fill-hot-filing"></a>Peercache-Auffüllung bei häufig verwendeten Dateien

Beim Standardfeature „Peercache-Auffüllung bei häufig verwendeten Dateien“ wird ein eigener anspruchsvoller Algorithmus verwendet. Es werden zusätzliche Edgezwischenspeicherungsserver basierend auf der Bandbreite und den Metriken von Aggregatanforderungen verwendet, um Clientanforderungen für große, beliebte Objekte zu erfüllen. Mit diesem Feature werden Situationen verhindert, in denen eine große Anzahl von zusätzlichen Anforderungen an den Ursprungsserver eines Benutzers gesendet werden. 

### <a name="conditions-for-large-file-optimization"></a>Bedingungen für die Optimierung großer Dateien

Die Features zur Optimierung großer Dateien für **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** werden standardmäßig aktiviert, wenn Sie den Optimierungstyp „Allgemeine Webbereitstellung“ verwenden. Es gilt keine Beschränkung in Bezug auf die maximale Dateigröße. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimieren der Übermittlung großer Dateien mit Azure CDN Standard von Akamai

Für Profilendpunkte vom Typ **Azure CDN Standard von Akamai** steht ein Feature zur Verfügung, mit dem große Dateien effizient an Benutzer auf der ganzen Welt übermittelt werden. Es entlastet die Ursprungsserver und verkürzt so die Wartezeiten.

Mit dem Feature zum Optimieren von großen Dateien werden Netzwerkoptimierungen und Konfigurationen aktiviert, um große Dateien schneller und reaktionsschneller bereitzustellen. Bei der allgemeinen Webbereitstellung mit Endpunkten vom Typ **Azure CDN Standard von Akamai** werden nur Dateien mit einer Größe von weniger als 1,8 GB zwischengespeichert. Für Dateien mit bis zu 150 GB kann ein Tunnel (ohne Zwischenspeicherung) eingerichtet werden. Bei der Optimierung großer Dateien werden Dateien mit einer Größe von bis zu 150 GB zwischengespeichert.

Die Optimierung großer Dateien ist effektiv, wenn bestimmte Bedingungen erfüllt sind. Zu diesen Bedingungen gehören die Funktionsweise des Ursprungsservers und die Größen und Arten von Dateien, die angefordert werden. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurieren eines Akamai-CDN-Endpunkts zur Optimierung der Übermittlung großer Dateien

Sie können Ihren Endpunkt vom Typ **Azure CDN Standard von Akamai** über das Azure-Portal für die Übertragung großer Dateien optimieren. Sie können dafür auch die REST-APIs oder eines der Client-SDKs verwenden. Die folgenden Schritte veranschaulichen die Vorgehensweise im Azure-Portal für ein Profil vom Typ **Azure CDN Standard von Akamai**:

1. Wählen Sie zum Hinzufügen eines neuen Endpunkts auf der Akamai-Seite **CDN-Profil** die Option **Endpunkt** aus.

    ![Neuer Endpunkt](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Wählen Sie in der Dropdownliste **Optimiert für** die Option **Download großer Dateien** aus.

    ![Auswahl der Optimierung großer Dateien](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Nachdem Sie den CDN-Endpunkt erstellt haben, werden die Optimierungen für große Dateien auf alle Dateien angewendet, die bestimmte Kriterien erfüllen. Dies wird im folgenden Abschnitt beschrieben.

### <a name="object-chunking"></a>Objektblockerstellung 

Bei der Optimierung großer Dateien mit **Azure CDN Standard von Akamai** kommt eine Technik namens „Objektblockerstellung“ zum Einsatz. Wenn eine große Datei angefordert wird, ruft das CDN kleinere Teile der Datei vom Ursprung ab. Nachdem der CDN-POP-Server eine vollständige oder auf einen Bytebereich beschränkte Dateianforderung empfangen hat, wird überprüft, ob der Dateityp für diese Optimierung unterstützt wird. Außerdem wird überprüft, ob der Dateityp die Größenanforderungen für Dateien erfüllt. Wenn die Datei größer als 10 MB ist, fordert der CDN-Edgeserver die Datei vom Ursprung in Blöcken von 2 MB an. 

Nachdem der Block im CDN-Edgebereich angekommen ist, wird er zwischengespeichert und sofort für den Benutzer bereitgestellt. Das CDN ruft den nächsten Block dann parallel dazu ab. Durch dieses Vorab-Abrufen wird sichergestellt, dass der Inhalt dem Benutzer immer einen Block voraus ist, sodass sich die Wartezeit reduziert. Dieser Prozess wird fortgesetzt, bis die gesamte Datei heruntergeladen wurde (falls angefordert), alle Bytebereiche verfügbar sind (falls angefordert) oder der Client die Verbindung beendet. 

Weitere Informationen zur Bytebereichsanforderung finden Sie unter [RFC 7233](https://tools.ietf.org/html/rfc7233).

Das CDN speichert alle Blöcke zwischen, sobald sie eingetroffen sind. Es ist nicht erforderlich, die gesamte Datei im CDN-Cache zwischenzuspeichern. Nachfolgende Anforderungen für die Datei- oder Bytebereiche werden aus dem CDN-Cache bereitgestellt. Wenn nicht alle Blöcke im CDN zwischengespeichert werden, wird das Vorab-Abrufen verwendet, um Blöcke vom Ursprung anzufordern. Diese Optimierung setzt voraus, dass der Ursprungsserver Bytebereichsanforderungen unterstützt. Andernfalls ist diese Optimierung nicht effektiv.

### <a name="caching"></a>Caching
Für die Optimierung großer Dateien werden unterschiedliche Standardzeiten für den Ablauf der Zwischenspeicherung verwendet, die von der allgemeinen Webbereitstellung abweichen. Die Unterscheidung erfolgt zwischen der positiven und negativen Zwischenspeicherung basierend auf HTTP-Antwortcodes. Wenn der Ursprungsserver in der Antwort eine Ablaufzeit per Cache-Control- oder Expires-Header angibt, wird dieser Wert vom CDN berücksichtigt. Macht der Ursprungsserver keine Angabe und erfüllt die Datei die Typ- und Größenbedingungen für diesen Optimierungstyp, dann verwendet das CDN die Standardwerte für die Optimierung großer Dateien. Andernfalls verwendet das CDN die Standardeinstellungen für die allgemeine Webbereitstellung.

| Caching  | Allgemeine Webübermittlung | Optimierung großer Dateien 
--- | --- | --- 
Caching: Positiv <br> HTTP 200, 203, 300, <br> 301, 302 und 410 | 7 Tage |1 Tag  
Caching: Negativ <br> HTTP 204, 305, 404 <br> und 405 | Keine | 1 Sekunde 

### <a name="deal-with-origin-failure"></a>Behandeln eines Ausfalls des Ursprungsservers

Die Dauer der Lesezeitüberschreitung des Ursprungs erhöht sich von zwei Sekunden für die allgemeine Webbereitstellung auf zwei Minuten für den Typ „Optimierung großer Dateien“. Diese Erhöhung deckt die Zunahme der Dateigrößen ab, um eine zu frühe Zeitüberschreitung für die Verbindung zu vermeiden.

Bei einem Verbindungstimeout führt das CDN eine bestimmte Anzahl von Wiederholungen aus und sendet schließlich den Fehler „504 - Gateway-Timeout“ an den Client. 

### <a name="conditions-for-large-file-optimization"></a>Bedingungen für die Optimierung großer Dateien

Die folgende Tabelle enthält die Kriterien für die Optimierung großer Dateien:

Bedingung | Werte 
--- | --- 
Unterstützte Dateitypen | 3G2, 3GP, ASF, AVI, BZ2, DMG, EXE, F4V, FLV, <br> GZ, HDP, ISO, JXR, M4V, MKV, MOV, MP4, <br> MPEG, MPG, MTS, PKG, QT, RM, SWF, TAR, <br> TGZ, WDP, WEBM, WEBP, WMA, WMV, ZIP  
Minimale Dateigröße | 10 MB 
Maximale Dateigröße | 150 GB 
Merkmale des Ursprungsservers | Muss Bytebereichsanforderungen unterstützen 

## <a name="additional-considerations"></a>Weitere Überlegungen

Berücksichtigen Sie für diesen Optimierungstyp auch folgende Punkte:

- Beim Prozess der Objekterstellung werden zusätzliche Anforderungen für den Ursprungsserver generiert. Das Gesamtvolumen der Daten, die vom Ursprung bereitgestellt werden, ist aber deutlich kleiner. Die Blockerstellung führt zu besseren Zwischenspeicherungsmerkmalen im CDN.

- Der Arbeitsspeicher- und E/A-Druck am Ursprung verringert sich, da kleinere Teile der Datei übermittelt werden.

- Für Blöcke, die im CDN zwischengespeichert werden, gibt es keine zusätzlichen Anforderungen an den Ursprung, bis der Inhalt abgelaufen ist oder aus dem Cache entfernt wird.

- Benutzer können Bereichsanforderungen an das CDN übermitteln. Diese werden wie normale Dateien behandelt. Die Optimierung gilt nur, wenn es sich um einen gültigen Dateityp handelt und der Bytebereich zwischen 10 MB und 150 GB liegt. Wenn die durchschnittliche Größe der angeforderten Dateien unter 10 MB liegt, verwenden Sie stattdessen die allgemeine Webbereitstellung.

