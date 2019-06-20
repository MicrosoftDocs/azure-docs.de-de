---
title: Problembehandlung bei Azure CDN-Endpunkten mit Statuscode 404 | Microsoft-Dokumentation
description: Problembehandlung beim Statuscode 404 bei Azure CDN-Endpunkten.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 66ee211856bb451caad7af02103aa306d76e8f97
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60323658"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Problembehandlung bei Azure CDN-Endpunkten mit Statuscode 404
Dieser Artikel enthält Informationen zum Behandeln von Problemen mit Azure CDN-Endpunkten (Content Delivery Network), bei denen der HTTP-Antwortstatuscode 404 zurückgegeben wird.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie die Option **Support erhalten**.

## <a name="symptom"></a>Symptom
Sie haben ein CDN-Profil und einen Endpunkt erstellt, Ihre Inhalte sind auf dem CDN jedoch anscheinend nicht verfügbar. Benutzer, die über die CDN-URL auf die Inhalte zugreifen möchten, erhalten den HTTP-Statuscode 404. 

## <a name="cause"></a>Ursache
Es gibt mehrere mögliche Ursachen, darunter folgende:

* Den Ursprung der Datei ist für das CDN nicht sichtbar.
* Der Endpunkt ist falsch konfiguriert, weshalb das CDN an der falschen Stelle sucht.
* Der Host lehnt den Host-Header aus dem CDN ab.
* Der Endpunkt konnte noch nicht über das CDN verteilt werden.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
> [!IMPORTANT]
> Ein CDN-Endpunkt kann nach seiner Erstellung nicht sofort verwendet werden, da die Verteilung der Registrierung über das CDN eine Weile dauern kann:
> - Bei Profilen vom Typ **Azure CDN Standard von Microsoft** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
> - Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
> - Bei Profilen vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** ist die Weitergabe in der Regel in 90 Minuten abgeschlossen. 
> 
> Wenn Sie die in diesem Dokument beschriebenen Schritte ausgeführt haben und dennoch weiterhin der Code 404 zurückgegeben wird, warten Sie unter Umständen ein paar Stunden, und versuchen Sie es dann erneut, bevor Sie ein Supportticket öffnen.
> 
> 

### <a name="check-the-origin-file"></a>Überprüfen der Ursprungsdatei
Überprüfen Sie zunächst, ob die zwischenzuspeichernde Datei auf dem Ursprungsserver vorhanden ist und darauf öffentlich im Internet zugegriffen werden kann. Das geht am schnellsten, wenn Sie eine InPrivate- oder Inkognito-Browsersitzung öffnen und die Datei direkt aufrufen. Wenn Sie die URL in das Adressfeld eingeben oder einfügen, können Sie prüfen,ob die gewünschte Datei angezeigt wird. Nehmen wir beispielsweise an, dass Sie eine Datei in einem Azure Storage-Konto besitzen, auf die unter „https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt“ zugegriffen werden kann. Wenn Sie den Inhalt dieser Datei laden können, war der Test erfolgreich.

![Erfolg!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Dies ist die schnellste und einfachste Möglichkeit, um zu prüfen, ob Ihre Datei öffentlich zugänglich ist. Bei manchen Netzwerkkonfigurationen von Organisationen kann unter Umständen der Eindruck entstehen, dass eine Datei öffentlich zugänglich ist, während sie jedoch nur Benutzern des Netzwerks angezeigt wird (selbst wenn sie in Azure gehostet wird). Um sicherzustellen, dass dies nicht der Fall ist, führen Sie den Test über einen externen Browser durch, z.B. über ein Mobilgerät, das nicht mit dem Netzwerk Ihrer Organisation verbunden ist, oder einen virtuellen Computer in Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Überprüfen der Ursprungseinstellungen
Nachdem Sie geprüft haben, ob die Datei im Internet öffentlich verfügbar ist, überprüfen Sie die Ursprungseinstellungen. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem CDN-Profil, und wählen Sie den betreffenden Endpunkt aus. Wählen Sie auf der angezeigten Seite **Endpunkt** den Ursprung aus.  

![Endpunktseite mit hervorgehobenem Ursprung](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Die Seite **Ursprung** wird angezeigt. 

![Ursprungsseite](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Ursprungstyp und Hostname
Überprüfen Sie die Werte **Ursprungstyp** und **Hostname des Ursprungs** auf Ihre Richtigkeit. In diesem Beispiel (https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt) ist *cdndocdemo.blob.core.windows.net* der Teil der URL, der den Hostnamen darstellt. Er ist korrekt. Da für Azure Storage-, Web-App- und Clouddienstursprünge ein Dropdownlisten-Wert für das Feld **Hostname des Ursprungs** verwendet wird, stellen Tippfehler kein Problem dar. Wenn Sie einen benutzerdefinierten Ursprung verwenden, müssen Sie jedoch sicherstellen, dass der Hostname richtig geschrieben ist.

#### <a name="http-and-https-ports"></a>HTTP- und HTTPS-Ports
Überprüfen Sie die **HTTP**- und **HTTPS-Ports**. Die Einstellungen 80 und 443 stimmen meistens und erfordern keine Änderungen.  Wenn der Ursprungsserver jedoch an einem anderen Port lauscht, muss dies hier angegeben werden. Wenn Sie sich in Bezug darauf nicht sicher sind, sehen Sie sich die URL Ihrer Ursprungsdatei an. Die HTTP- und HTTPS-Spezifikationen verwenden standardmäßig die Ports 80 und 443. In der Beispiel-URL „https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt“ ist kein Port angegeben. Daher wird vom Standardport 443 ausgegangen, und die Einstellungen sind korrekt.  

Nun nehmen wir jedoch an, dass die URL für die zuvor getestete Ursprungsdatei „http:\//www.contoso.com:8080/file.txt“ lautet. Am Ende des Hostnamensegments wird der Teil *:8080* angezeigt. Diese Zahl weist den Browser an, Port 8080 für die Verbindung mit dem Webserver unter www\.contoso.com zu verwenden. Geben Sie daher im Feld **HTTP-Port** den Port *8080* ein. Beachten Sie, dass sich diese Porteinstellungen nur darauf auswirken, welchen Port der Endpunkt zum Abrufen von Informationen vom Ursprung verwendet.

> [!NOTE]
> Endpunkte vom Typ **Azure CDN Standard von Akamai** lassen nicht den vollständigen TCP-Portbereich für Ursprünge zu.  Eine Liste der nicht zulässigen Ursprungsports finden Sie unter [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100))(Azure CDN von Akamai – Zulässige Ursprungsports).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Überprüfen der Endpunkteinstellungen
Klicken Sie auf der Seite **Endpunkt** auf die Schaltfläche **Konfigurieren**.

![Endpunktseite mit hervorgehobener Schaltfläche „Konfigurieren“](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Die CDN-Endpunktseite **Konfigurieren** wird angezeigt.

![Konfigurieren, Seite](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokolle
Prüfen Sie für die **Protokolle**, dass das von den Clients verwendete Protokoll ausgewählt ist. Das vom Client verwendete Protokoll wird für den Zugriff auf den Ursprung genutzt. Daher müssen die Ursprungsports wie im letzten Abschnitt beschrieben richtig konfiguriert sein. Der CDN-Endpunkt lauscht nur an den HTTP- und HTTPS-Standardports (80 und 443), unabhängig von den Ursprungsports.

Kehren wir nun zu unserem Beispiel mit „http:\//www.contoso.com:8080/file.txt“ zurück.  Wie Sie wissen, hat Contoso *8080* als HTTP-Port festgelegt. Nehmen wir nun einmal an, *44300* sei als HTTPS-Port festgelegt worden.  Wenn ein Endpunkt namens *contoso* erstellt wurde, ist *contoso.azureedge.net* der Hostname des CDN-Endpunkts.  Eine Anforderung für „http:\//contoso.azureedge.net/file.txt“ ist eine HTTP-Anforderung. Somit verwendet der Endpunkt HTTP an Port 8080 für den Abruf vom Ursprung.  Bei einer sicheren Anforderung über HTTPS (https:\//contoso.azureedge.net/file.txt) verwendet der Endpunkt HTTPS an Port 44300 beim Abruf der Datei vom Ursprung.

#### <a name="origin-host-header"></a>Header des Ursprungshosts
Der **Header des Ursprungshosts** ist der Hostheaderwert, der bei jeder Anforderung an den Ursprung übermittelt wird.  In den meisten Fällen entspricht dieser Wert dem **Hostname des Ursprungs**, den wir zuvor bereits geprüft haben.  Wenn in diesem Feld ein falscher Wert eingegeben ist, wird in der Regel kein 404-Status ausgegeben. Je nachdem, was der Ursprung erwartet, werden wahrscheinlich jedoch andere 4xx-Statuscodes zurückgegeben werden.

#### <a name="origin-path"></a>Ursprungspfad
Als Letztes sollte noch der **Ursprungspfad**geprüft werden.  Standardmäßig ist dieses Feld leer.  Verwenden Sie dieses Feld nur, wenn Sie den Umfang der Ressourcen vom Ursprungshost, die auf dem CDN verfügbar sein sollen, einschränken möchten.  

Im Beispielendpunkt sollen alle Ressourcen im Speicherkonto verfügbar sein, weshalb das Feld **Ursprungspfad** leer gelassen wurde.  Das bedeutet, dass eine Anforderung an „https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt“ dazu führt, dass eine Verbindung zwischen dem Endpunkt, der */publicblob/lorem.txt* anfordert, und „cdndocdemo.core.windows.net“ hergestellt wird.  Analog dazu führt eine Anforderung für „https:\//cdndocdemo.azureedge.net/donotcache/status.png“ dazu, dass der Endpunkt */donotcache/status.png* vom Ursprung anfordert.

Aber wie gehe ich vor, wenn das CDN nicht für jeden Ursprungspfad verwendet werden soll?  Angenommen, Sie möchten nur den Pfad *publicblob* verfügbar machen.  Durch Eingabe von */publicblob* im Feld **Ursprünglicher Pfad** fügt der Endpunkt vor jeder Anforderung an den Ursprung */publicblob* ein.  Das bedeutet, dass die Anforderung für „https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt“ nun tatsächlich den Anforderungsteil der URL ( */publicblob/lorem.txt*) verwendet und am Anfang */publicblob* einfügt. Dies führt zu einer Anforderung für */publicblob/publicblob/lorem.txt* vom Ursprung.  Wenn dieser Pfad zu keiner Datei führt, gibt der Ursprung den Status 404 zurück.  Die richtige URL zum Abruf von „lorem.txt“ wäre in diesem Beispiel „https:\//cdndocdemo.azureedge.net/lorem.txt“.  Wie Sie sehen, ist der Pfad */publicblob* hier nicht angegeben, da der Anforderungsteil der URL */lorem.txt* lautet und der Endpunkt */publicblob* hinzufügt, sodass */publicblob/lorem.txt* als Anforderung an den Ursprung weitergegeben wird.

