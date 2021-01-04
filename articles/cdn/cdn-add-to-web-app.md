---
title: 'Tutorial: Hinzufügen von Azure CDN zu einer Azure App Service-Web-App | Microsoft-Dokumentation'
description: In diesem Tutorial wird einer Azure App Service-Web-App ein Content Delivery Network (CDN) hinzugefügt, um Ihre statischen Dateien über Server zwischenzuspeichern und bereitzustellen, die von Ihren weltweiten Kunden jeweils nicht weit entfernt sind.
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
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3f1dacb57931913edfb181f023bdf98717777d50
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562241"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Tutorial: Hinzufügen von Azure CDN zu einer Azure App Service-Web-App

In diesem Tutorial wird veranschaulicht, wie Sie [Azure Content Delivery Network (CDN)](cdn-overview.md) einer [Web-App in Azure App Service](../app-service/overview.md) hinzufügen. Web-Apps ist ein Dienst zum Hosten von Webanwendungen, REST-APIs und mobilen Back-Ends. 

Hier ist die Startseite der statischen HTML-Beispielwebsite dargestellt, mit der Sie arbeiten:

![Startseite der Beispiel-App](media/cdn-add-to-web-app/sample-app-home-page.png)

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines CDN-Endpunkts
> * Aktualisieren von zwischengespeicherten Objekten
> * Verwenden von Abfragezeichenfolgen zum Steuern von zwischengespeicherten Versionen


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- [Installation von Git](https://git-scm.com/)
- [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Erstellen der Web-App

Befolgen Sie zum Erstellen der Web-App, die Sie verwenden werden, die Anleitung unter [Erstellen einer statischen HTML-Web-App in Azure in fünf Minuten](../app-service/quickstart-html.md) bis zum Schritt **Navigieren zur App**.

## <a name="log-in-to-the-azure-portal"></a>Anmelden beim Azure-Portal

Öffnen Sie einen Browser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Optimieren der Beschleunigung dynamischer Websites
Wenn Sie Ihren CDN-Endpunkt für die Beschleunigung dynamischer Websites (Dynamic Site Acceleration, DSA) optimieren möchten, sollten Sie Ihr Profil und Ihren Endpunkt über das [CDN-Portal](cdn-create-new-endpoint.md) erstellen. Durch die [DSA-Optimierung](cdn-dynamic-site-acceleration.md) wird die Leistung von Webseiten mit dynamischen Inhalten merklich verbessert. Wie Sie einen CDN-Endpunkt über das CDN-Portal für DSA optimieren, erfahren Sie unter [Beschleunigung dynamischer Websites durch das Azure CDN](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). Falls Sie Ihren neuen Endpunkt nicht optimieren möchten, können Sie ihn gemäß der Anleitung im nächsten Abschnitt über das Web-App-Portal erstellen. Hinweis: Bei Profilen vom Typ **Azure CDN von Verizon** kann die Optimierung eines CDN-Endpunkts nach der Erstellung nicht mehr geändert werden.

## <a name="create-a-cdn-profile-and-endpoint"></a>Erstellen eines CDN-Profils und -Endpunkts

Wählen Sie im Navigationsbereich auf der linken Seite die Option **App Services** und dann die App aus, die Sie unter [Erstellen einer statischen HTML-Web-App in Azure in fünf Minuten](../app-service/quickstart-html.md) erstellt haben.

![Auswählen der App Service-App im Portal](media/cdn-add-to-web-app/portal-select-app-services.png)

Wählen Sie auf der Seite **App Service** im Abschnitt **Einstellungen** die Option **Netzwerk > Azure CDN für Ihre App konfigurieren**.

![Auswählen des CDN im Portal](media/cdn-add-to-web-app/portal-select-cdn.png)

Geben Sie auf der Seite **Azure Content Delivery Network** die Einstellungen für **Neuer Endpunkt** gemäß den Angaben in der Tabelle an.

![Erstellen des Profils und Endpunkts im Portal](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
| ------- | --------------- | ----------- |
| **CDN-Profil** | myCDNProfile | Ein CDN-Profil ist eine Sammlung von CDN-Endpunkten mit demselben Tarif. |
| **Preisstufe** | Standard Akamai | Über den [Tarif](cdn-features.md) werden der Anbieter und die verfügbaren Features angegeben. In diesem Tutorial wird *Standard Akamai* verwendet. |
| **CDN-Endpunktname** | Beliebiger Name, der in der Domäne „azureedge.net“ eindeutig ist | Sie greifen auf Ihre zwischengespeicherten Ressourcen in der Domäne *&lt;Endpunktname&gt;* .azureedge.net zu.

Wählen Sie **Erstellen** aus, um ein CDN-Profil zu erstellen.

Azure erstellt das Profil und den Endpunkt. Der neue Endpunkt wird in der Liste **Endpunkte** angezeigt. Nach seiner Bereitstellung lautet der Status **Wird ausgeführt**.

![Neuer Endpunkt in der Liste](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testen des CDN-Endpunkts

 Der Endpunkt kann nicht sofort verwendet werden, da die Verteilung der Registrierung über das CDN eine Weile dauern kann: 
   - Bei Profilen vom Typ **Azure CDN Standard von Microsoft** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
   - Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
   - Bei Profilen vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** ist die Weitergabe in der Regel in 90 Minuten abgeschlossen. 

Die Beispiel-App enthält die Datei *index.html* und die Ordner *css*, *img* und *js* mit anderen statischen Objekten. Die Inhaltspfade für alle diese Dateien sind auf dem CDN-Endpunkt gleich. Beispielsweise wird mit beiden folgenden URLs auf die Datei *bootstrap.css* im Ordner *css* zugegriffen:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Navigieren Sie in einem Browser zur folgenden URL:

```
http://<endpointname>.azureedge.net/index.html
```

![Startseite der Beispiel-App – Bereitstellung per CDN](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Daraufhin wird die Seite angezeigt, die Sie zuvor in einer Azure-Web-App ausgeführt haben. Azure CDN hat die Originalobjekte der Web-App abgerufen und sie über den CDN-Endpunkt bereitstellt.

Aktualisieren Sie die Seite, um sicherzustellen, dass diese Seite im CDN zwischengespeichert ist. Es kann sein, dass für ein Objekt zwei Anforderungen erforderlich sind, damit der angeforderte Inhalt vom CDN zwischengespeichert wird.

Weitere Informationen zum Erstellen von Azure CDN-Profilen und -Endpunkten finden Sie unter [Erste Schritte mit Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Bereinigen des CDN

Das CDN aktualisiert basierend auf der Gültigkeitsdauer (Time To Live, TTL) in regelmäßigen Abständen seine Ressourcen der ursprünglichen Web-App. Die Standardgültigkeitsdauer ist sieben Tage.

Es kann vorkommen, dass Sie das CDN vor Ablauf der Gültigkeitsdauer aktualisieren müssen, z.B. beim Bereitstellen von aktualisiertem Inhalt für die Web-App. Zum Auslösen einer Aktualisierung bereinigen Sie die CDN-Ressourcen manuell. 

In diesem Abschnitt des Tutorials stellen Sie eine Änderung für die Web-App bereit und bereinigen das CDN, um für das CDN die Aktualisierung des Caches auszulösen.

### <a name="deploy-a-change-to-the-web-app"></a>Bereitstellen einer Änderung für die Web-App

Öffnen Sie die Datei *index.html*, und fügen Sie der Überschrift H1 den Zusatz *- V2* hinzu. Dies ist im folgenden Beispiel dargestellt: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Übernehmen Sie die Änderung, und stellen Sie sie für die Web-App bereit.

```bash
git commit -am "version 2"
git push azure main
```

Navigieren Sie nach Abschluss der Bereitstellung zur Web-App-URL. Die Änderung wird angezeigt.

```
http://<appname>.azurewebsites.net/index.html
```

![„V2“ im Titel der Web-App](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Wenn Sie zur CDN-Endpunkt-URL der Startseite navigieren, ist die Änderung noch nicht zu sehen, weil die zwischengespeicherte Version im CDN noch nicht abgelaufen ist. 

```
http://<endpointname>.azureedge.net/index.html
```

![„V2“ nicht im Titel des CDN enthalten](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Bereinigen des CDN im Portal

Bereinigen Sie das CDN, um für das CDN die Aktualisierung der zwischengespeicherten Version auszulösen.

Wählen Sie im Navigationsbereich links im Portal die Option **Ressourcengruppen** und dann die Ressourcengruppe aus, die Sie für Ihre Web-App (myResourceGroup) erstellt haben.

![Auswählen der Ressourcengruppe](media/cdn-add-to-web-app/portal-select-group.png)

Wählen Sie in der Liste mit den Ressourcen Ihren CDN-Endpunkt aus.

![Auswählen des Endpunkts](media/cdn-add-to-web-app/portal-select-endpoint.png)

Wählen Sie oben auf der Seite **Endpunkt** die Option **Bereinigen** aus.

![Auswählen von „Bereinigen“](media/cdn-add-to-web-app/portal-select-purge.png)

Geben Sie die Inhaltspfade ein, die Sie bereinigen möchten. Sie können einen vollständigen Dateipfad übergeben, um eine einzelne Datei zu bereinigen, oder ein Pfadsegment, um den gesamten Inhalt eines Ordners zu bereinigen und zu aktualisieren. Da Sie *index.html* geändert haben, stellen Sie sicher, dass sich die Datei unter einem der Pfade befindet.

Wählen Sie am unteren Rand der Seite die Option **Bereinigen**.

![Seite „Bereinigen“](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Sicherstellen des aktualisierten Zustands für das CDN

Warten Sie, bis die Verarbeitung der Bereinigungsanforderung abgeschlossen ist. Dies dauert normalerweise einige Minuten. Wählen Sie zum Anzeigen des aktuellen Status oben auf der Seite das Glockensymbol. 

![Bereinigungsbenachrichtigung](media/cdn-add-to-web-app/portal-purge-notification.png)

Wenn Sie zur CDN-Endpunkt-URL für *index.html* navigieren, ist der Zusatz *V2* zu sehen, den Sie dem Titel auf der Startseite hinzugefügt haben. Dies bedeutet, dass der CDN-Cache aktualisiert wurde.

```
http://<endpointname>.azureedge.net/index.html
```

![„V2“ im Titel des CDN enthalten](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Weitere Informationen finden Sie unter [Löschen eines Azure CDN-Endpunkts](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Verwenden von Abfragezeichenfolgen zum Versehen von Inhalt mit einer Version

Azure CDN verfügt über die folgenden Optionen für das Zwischenspeicherverhalten:

* Ignorieren von Abfragezeichenfolgen
* Umgehen der Zwischenspeicherung für Abfragezeichenfolgen
* Zwischenspeichern jeder eindeutigen URL 

Die erste Option ist die Standardeinstellung. Dies bedeutet, dass nur eine zwischengespeicherte Version eines Objekts vorhanden ist, und zwar unabhängig von der Abfragezeichenfolge in der URL. 

In diesem Abschnitt des Tutorials ändern Sie das Zwischenspeicherverhalten, sodass jede eindeutige URL zwischengespeichert wird.

### <a name="change-the-cache-behavior"></a>Ändern des Cacheverhaltens

Wählen Sie im Azure-Portal auf der Seite **CDN-Endpunkt** die Option **Cache**.

Wählen Sie in der Dropdownliste **Verhalten für das Zwischenspeichern von Abfragezeichenfolgen** die Option **Jede eindeutige URL zwischenspeichern**.

Wählen Sie **Speichern** aus.

![Auswählen des Zwischenspeicherverhaltens für Abfragezeichenfolgen](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Sicherstellen der separaten Zwischenspeicherung eindeutiger URLs

Navigieren Sie in einem Browser zur Startseite des CDN-Endpunkts, und binden Sie dabei eine Abfragezeichenfolge ein: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN gibt den aktuellen Inhalt der Web-App zurück, der in der Überschrift den Zusatz *V2* enthält. 

Aktualisieren Sie die Seite, um sicherzustellen, dass diese Seite im CDN zwischengespeichert ist. 

Öffnen Sie *index.html*, ändern Sie *V2* in *V3*, und stellen Sie dann die Änderung bereit. 

```bash
git commit -am "version 3"
git push azure main
```

Navigieren Sie in einem Browser mit einer neuen Abfragezeichenfolge, z.B. `q=2`, zur CDN-Endpunkt-URL. Azure CDN ruft die aktuelle Datei *index.html* ab und zeigt *V3* an. Wenn Sie dagegen mit der Abfragezeichenfolge `q=1` zum CDN-Endpunkt navigieren, wird *V2* angezeigt.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![„V3“ im Titel des CDN, Abfragezeichenfolge 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![„V2“ im Titel des CDN, Abfragezeichenfolge 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Diese Ausgabe veranschaulicht, dass jede Abfragezeichenfolge anders behandelt wird:

* q=1 wurden zuvor verwendet, weshalb zwischengespeicherte Inhalte zurückgegeben werden (V2).
* q=2 ist neu, weshalb die neuesten Web-App-Inhalte abgerufen und zurückgegeben werden (V3).

Weitere Informationen finden Sie unter [Steuern des Azure CDN-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](../cdn/cdn-query-string.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines CDN-Endpunkts
> * Aktualisieren von zwischengespeicherten Objekten
> * Verwenden von Abfragezeichenfolgen zum Steuern von zwischengespeicherten Versionen

Die folgenden Artikel enthalten Informationen zur Optimierung der CDN-Leistung:

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen einer benutzerdefinierten Domäne zum Azure CDN-Endpunkt](cdn-map-content-to-custom-domain.md)