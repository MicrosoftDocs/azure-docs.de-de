---
title: Verwalten des Ablaufs von Webinhalten in Azure CDN | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Ablauf von Inhalten aus Azure-Web-Apps/Cloud Services, ASP.NET oder IIS in Azure CDN verwalten.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: d8eb450d2010bf2a525a26f1c5ff48f59732ce43
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240969"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Verwalten des Ablaufs von Webinhalten in Azure CDN
> [!div class="op_single_selector"]
> * [Azure-Webinhalt](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Dateien von öffentlich zugänglichen Ursprungswebservern können bis zum Ende ihrer Gültigkeitsdauer im Azure Content Delivery Network (CDN) zwischengespeichert werden. Die Gültigkeitsdauer wird durch den `Cache-Control`-Header in der HTTP-Antwort des Ursprungsservers bestimmt. In diesem Artikel erfahren Sie, wie Sie `Cache-Control`-Header für das Web-Apps-Feature von Microsoft Azure App Service, Azure Cloud Services, ASP.NET-Anwendungen und Internetinformationsdienste-Websites (Internet Information Services, IIS) festlegen. Die Konfiguration ist dabei jeweils ähnlich. Sie können den `Cache-Control`-Header entweder mithilfe von Konfigurationsdateien oder programmgesteuert festlegen. 

Sie können die Cacheeinstellungen auch über das Portal durch Festlegen von [CDN-Cacheregeln](cdn-caching-rules.md) steuern. Wenn Sie Cacheregeln erstellen und das Zwischenspeicherverhalten auf **Außerkraftsetzung** oder **Cache umgehen** festlegen, werden die in diesem Artikel erörterten vom Ursprung angegebenen Cacheeinstellungen ignoriert. Weitere Informationen zu allgemeinen Cachekonzepten finden Sie unter [How caching works](cdn-how-caching-works.md) (Funktionsweise von Caching).

> [!TIP]
> Sie haben auch die Möglichkeit, für eine Datei keine Gültigkeitsdauer festzulegen. In diesem Fall wendet Azure CDN automatisch eine Standardgültigkeitsdauer von sieben Tagen an, es sei denn, Sie haben Cacheregeln im Azure-Portal eingerichtet. Diese Standardgültigkeitsdauer gilt nur für die Optimierung allgemeiner Webbereitstellungen. Bei der Optimierung großer Dateien beträgt die Standardgültigkeitsdauer einen Tag, bei der Optimierung für das Medienstreaming beträgt die Standardgültigkeitsdauer ein Jahr.
> 
> Weitere Informationen dazu, wie Azure CDN den Zugriff auf Dateien und andere Ressourcen beschleunigen kann, finden Sie in der [Übersicht über Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Festlegen von Cache-Control-Headern mithilfe von CDN-Cacheregeln
Die bevorzugte Methode zum Einrichten des `Cache-Control`-Headers für einen Webserver ist die Verwendung von Cacheregeln im Azure-Portal. Weitere Informationen zu CDN-Cacheregeln finden Sie unter [Steuern des Verhaltens beim Zwischenspeichern im Azure Content Delivery Network mit Cacheregeln](cdn-caching-rules.md).

> [!NOTE] 
> Cacheregeln sind nur für die Profile **Azure CDN Standard von Verizon** und **Azure CDN Standard von Akamai** verfügbar. Für Profile vom Typ **Azure CDN Premium von Verizon**  müssen Sie die [Azure CDN-Regel-Engine](./cdn-verizon-premium-rules-engine.md) im **Verwaltungsportal** verwenden, um von einer ähnlichen Funktionalität zu profitieren.

**So navigieren Sie zur Seite mit den CDN-Cacheregeln**

1. Wählen Sie im Azure-Portal ein CDN-Profil und anschließend den Endpunkt für den Webserver aus.

1. Wählen Sie im linken Bereich unter „Einstellungen“ die Option **Cacheregeln** aus.

   ![Schaltfläche für CDN-Cacheregeln](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Die Seite **Cacheregeln** wird geöffnet.

   ![Seite zur CDN-Zwischenspeicherung](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**So legen Sie mit globalen Cacheregeln Cache-Control-Header für einen Webserver fest**

1. Legen Sie unter **Globale Cacheregeln** die Option **Verhalten für das Zwischenspeichern von Abfragezeichenfolgen** auf **Abfragezeichenfolgen ignorieren** fest. Legen Sie dann das **Verhalten beim Zwischenspeichern** auf **Außerkraftsetzung** fest.
      
1. Geben Sie für **Dauer bis Cacheablauf** einen Wert von 3600 im Feld **Sekunden** ein, oder geben Sie im Feld **Stunden** den Wert 1 an. 

   ![Beispiel für globale CDN-Cacheregeln](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Mit diesen globalen Cacheregeln wird eine Cachedauer von 1 Stunde festgelegt, die sich auf alle Anforderungen an den Endpunkt auswirkt. Der festgelegte Ablauf überschreibt alle `Cache-Control`- oder `Expires`-HTTP-Header, die durch die vom Endpunkt festgelegten Ursprungsserver gesendet werden.   

1. Wählen Sie **Speichern** aus.

**So legen Sie mit benutzerdefinierten Cacheregeln Cache-Control-Header für einen Webserver fest**

1. Erstellen Sie unter **Benutzerdefinierte Cacheregeln** zwei Übereinstimmungsbedingungen:

     a. Legen Sie die erste **Übereinstimmungsbedingung** auf **Pfad** fest, und geben Sie für den **Übereinstimmungswert**`/webfolder1/*` ein. Legen Sie **Verhalten beim Zwischenspeichern** auf **Überschreiben** fest, und geben Sie „4“ in das Feld **Tage** ein.

     b. Legen Sie die erste **Übereinstimmungsbedingung** auf **Pfad** fest, und geben Sie für den **Übereinstimmungswert**`/webfolder1/file1.txt` ein. Legen Sie **Verhalten beim Zwischenspeichern** auf **Überschreiben** fest, und geben Sie „2“ in das Feld **Tage** ein.

    ![Beispiel für benutzerdefinierte CDN-Cacheregeln](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Die erste benutzerdefinierte Cacheregel legt eine Cachedauer von vier Stunden für alle Dateien im Ordner `/webfolder1` auf dem Ursprungsserver fest, der durch Ihren Endpunkt angegeben wird. Die zweite Regel setzt für die Datei `file1.txt` die erste Regel außer Kraft und legt eine Cachedauer von zwei Stunden fest.

1. Wählen Sie **Speichern** aus.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Festlegen von Cache-Control-Headern mithilfe von Konfigurationsdateien
Bei statischen Inhalten wie Bildern und Stylesheets können Sie die Aktualisierungshäufigkeit durch Ändern der Konfigurationsdateien **ApplicationHost.config** oder **Web.config** für Ihre Webanwendung steuern. Um den `Cache-Control`-Header für Ihren Inhalt festzulegen, verwenden Sie das `<system.webServer>/<staticContent>/<clientCache>`-Element in einer der beiden Dateien.

### <a name="using-applicationhostconfig-files"></a>Verwenden der „ApplicationHost.config“-Datei
Die Datei **ApplicationHost.config** ist die Stammdatei des Konfigurationssystems für die Internetinformationsdienste. Die Konfigurationseinstellungen in einer **ApplicationHost.config** -Datei wirken sich auf alle Anwendungen in der Website aus, werden aber durch die Einstellungen aller für eine Webanwendung vorhandenen **Web.config** -Dateien außer Kraft gesetzt.

### <a name="using-webconfig-files"></a>Verwenden von Web.config-Dateien
Mit einer **Web.config** -Datei können Sie das Verhalten Ihrer gesamten Webanwendung oder eines bestimmten Verzeichnisses in Ihrer Webanwendung anpassen. In der Regel befindet sich im Stammordner Ihrer Webanwendung mindestens eine **Web.config** -Datei. Für jede **Web.config** -Datei in einem bestimmten Ordner gilt: Die Konfigurationseinstellungen wirken sich auf alle Inhalte dieses Ordners sowie aller Unterordner aus, solange diese nicht auf Unterordnerebene von einer anderen **Web.config** -Datei außer Kraft gesetzt werden. 

Sie können z.B. ein `<clientCache>`-Element in einer **Web.config** -Datei im Stammordner Ihrer Webanwendung festlegen, um alle statischen Inhalte Ihrer Webanwendung drei Tage lang zwischenzuspeichern. Sie können auch eine **Web.config** -Datei in einem Unterordner mit variableren Inhalten hinzufügen (z.B. `\frequent`) und das `<clientCache>`-Element dieser Datei so festlegen, dass der Inhalt des Unterordners sechs Stunden lang zwischengespeichert wird. Dies führt zu folgendem Ergebnis: Der Inhalt der gesamten Website wird drei Tage lang zwischengespeichert, mit Ausnahme aller Inhalte im Verzeichnis `\frequent`, die nur sechs Stunden lang zwischengespeichert werden.  

Das folgende Beispiel einer XML-Konfigurationsdatei veranschaulicht, wie Sie das `<clientCache>`-Element festlegen, um ein maximales Alter von drei Tagen anzugeben:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Um das **cacheControlMaxAge** -Attribut zu verwenden, müssen Sie den Wert für das **cacheControlMode** -Attribut auf `UseMaxAge` festlegen. Diese Einstellung führt dazu, dass der HTTP-Header und die HTTP-Anweisung `Cache-Control: max-age=<nnn>` zur Antwort hinzugefügt werden. Das Format des timespan-Werts für das **cacheControlMaxAge** -Attribut ist `<days>.<hours>:<min>:<sec>`. Der Wert wird in Sekunden konvertiert und als Wert der `Cache-Control` `max-age`-Anweisung verwendet. Weitere Informationen zum `<clientCache>`-Element finden Sie unter [Clientcache\<clientCache>](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Programmgesteuertes Festlegen von Cache-Control-Headern
Bei ASP.NET-Anwendungen steuern Sie das Verhalten von CDN beim Zwischenspeichern mithilfe der **HttpResponse.Cache** -Eigenschaft der .NET-API programmgesteuert. Informationen zur **HttpResponse.Cache** -Eigenschaft finden Sie unter [HttpResponse.Cache-Eigenschaft](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) und [HttpCachePolicy-Klasse](/dotnet/api/system.web.httpcachepolicy).  

Führen Sie diese Schritte aus, um Anwendungsinhalt in ASP.NET programmgesteuert zwischenzuspeichern:
   1. Stellen Sie sicher, dass der Inhalt als für die Zwischenspeicherung geeignet gekennzeichnet ist, indem Sie `HttpCacheability` auf `Public` festlegen. 
   1. Legen Sie eine Cachevalidierung fest, indem Sie eine der folgenden `HttpCachePolicy`-Methoden aufrufen:
      - Rufen Sie `SetLastModified` auf, um einen timestamp-Wert für den `Last-Modified`-Header festzulegen.
      - Rufen Sie `SetETag` auf, um einen Wert für den `ETag`-Header festzulegen.
   1. Geben Sie optional eine Ablaufzeit für den Cache an, indem Sie `SetExpires` aufrufen, um einen Wert für den `Expires`-Header festzulegen. Andernfalls gilt die Standard-Cacheheuristik, die weiter oben in diesem Dokument beschrieben wurde.

Fügen Sie beispielsweise folgenden C#-Code hinzu, um Inhalt eine Stunde lang zwischenzuspeichern:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testen des Cache-Control-Headers
Sie können die Einstellungen für die Gültigkeitsdauer Ihres Webinhalts ganz einfach überprüfen. Testen Sie mithilfe der [Entwicklertools](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) Ihres Browsers, ob Ihr Webinhalt den `Cache-Control`-Antwortheader enthält. Sie können auch ein Tool wie **wget** , [Postman](https://www.getpostman.com/) oder [Fiddler](https://www.telerik.com/fiddler) verwenden, um die Antwortheader zu untersuchen.

## <a name="next-steps"></a>Nächste Schritte
* [Lesen Sie ausführliche Informationen zum **clientCache** -Element.](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Lesen Sie die Dokumentation für die **HttpResponse.Cache** -Eigenschaft.](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Lesen Sie die Dokumentation für die **HttpCachePolicy-Klasse**](/dotnet/api/system.web.httpcachepolicy).  
* [Informationen zu Cachekonzepten](cdn-how-caching-works.md)
