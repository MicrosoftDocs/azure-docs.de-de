---
title: Verwaltung von Ausnahmen – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Ausnahmeverwaltung im Threat Modeling Tool. Lesen Sie die Informationen zur Risikominderung, und sehen Sie sich die Codebeispiele an.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517426"
---
# <a name="security-frame-exception-management--mitigations"></a>Sicherheitsrahmen: Verwaltung von Ausnahmen | Risikominderung 
| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF – serviceDebug-Knoten nicht in die Konfigurationsdatei aufnehmen](#servicedebug)</li><li>[WCF – serviceMetadata-Knoten nicht in die Konfigurationsdatei aufnehmen](#servicemetadata)</li></ul> |
| **Web-API** | <ul><li>[Sicherstellen, dass eine ordnungsgemäße Ausnahmebehandlung in der ASP.NET-Web-API erfolgt](#exception)</li></ul> |
| **Web Application** | <ul><li>[Keine Sicherheitsdetails in Fehlermeldungen verfügbar machen](#messages)</li><li>[Implementieren der Standardseite für die Fehlerbehandlung](#default)</li><li>[Festlegen der Bereitstellungsmethode auf „Retail“ in IIS](#deployment)</li><li>[Sicherer Ausfall bei Ausnahmen](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF – serviceDebug-Knoten nicht in die Konfigurationsdatei aufnehmen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Schritte** | WCF-Dienste (Windows Communication Framework) können so konfiguriert werden, dass Debuginformationen verfügbar gemacht werden. Debuginformationen sollten in Produktionsumgebungen nicht verwendet werden. Das `<serviceDebug>`-Tag definiert, ob die Funktion für Debuginformationen für einen WCF-Dienst aktiviert ist. Wenn das Attribut „includeExceptionDetailInFaults“ auf „true“ festgelegt ist, werden Ausnahmeinformationen von der Anwendung an Clients zurückgegeben. Angreifer können die zusätzlichen Informationen nutzen, die sie mit der Debugausgabe erhalten, um Angriffe auf das Framework, die Datenbank oder andere von der Anwendung verwendete Ressourcen zu starten. |

### <a name="example"></a>Beispiel
Die folgende Konfigurationsdatei enthält das `<serviceDebug>`-Tag: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Deaktivieren Sie die Debuginformationen im Dienst. Dazu können Sie das `<serviceDebug>`-Tag aus der Konfigurationsdatei Ihrer Anwendung entfernen. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF – serviceMetadata-Knoten nicht in die Konfigurationsdatei aufnehmen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Allgemein, .NET Framework 3 |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Schritte** | Wenn Informationen zu einem Dienst öffentlich verfügbar gemacht werden, erhalten Angreifer wertvolle Einblicke in Möglichkeiten zum Missbrauch des Diensts. Das `<serviceMetadata>`-Tag aktiviert die Funktion zum Veröffentlichen von Metadaten. Dienstmetadaten können vertrauliche Informationen enthalten, die nicht öffentlich zugänglich sein sollten. Sie sollten nur vertrauenswürdigen Benutzern den Zugriff auf die Metadaten ermöglichen und sicherstellen, dass keine unnötigen Informationen verfügbar gemacht werden. Noch besser wäre es allerdings, die Möglichkeit zum Veröffentlichen von Metadaten vollständig zu deaktivieren. Eine sichere WCF-Konfiguration enthält das `<serviceMetadata>`-Tag nicht. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Sicherstellen, dass eine ordnungsgemäße Ausnahmebehandlung in der ASP.NET-Web-API erfolgt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC 5, MVC 6 |
| **Attribute**              | –  |
| **Referenzen**              | [Ausnahmebehandlung in der ASP.NET-Web-API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [Modellüberprüfung in der ASP.NET-Web-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Schritte** | Standardmäßig werden die meisten nicht abgefangenen Ausnahmen in der ASP.NET-Web-API in eine HTTP-Antwort mit dem Statuscode `500, Internal Server Error` übersetzt.|

### <a name="example"></a>Beispiel
Zum Steuern des Statuscodes, der von der API zurückgegeben wird, kann `HttpResponseException` verwendet werden, wie unten dargestellt: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Beispiel
Für eine weitere Steuerung der Antwort auf die Ausnahme kann die `HttpResponseMessage`-Klasse verwendet werden, wie unten dargestellt: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Um nicht behandelte Ausnahmen abzufangen, die nicht vom Typ `HttpResponseException` sind, können Ausnahmefilter verwendet werden. Ausnahmefilter implementieren die `System.Web.Http.Filters.IExceptionFilter`-Schnittstelle. Die einfachste Methode zum Schreiben eines Ausnahmefilters ist eine Ableitung von der `System.Web.Http.Filters.ExceptionFilterAttribute`-Klasse und das Überschreiben die OnException-Methode. 

### <a name="example"></a>Beispiel
Hier ist ein Filter, der `NotImplementedException`-Ausnahmen in den HTTP-Statuscode `501, Not Implemented` konvertiert: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Es gibt mehrere Möglichkeiten zum Registrieren eines Web-API-Ausnahmefilters:
- Nach Aktion
- Nach Controller
- Global

### <a name="example"></a>Beispiel
Um den Filter auf eine bestimmte Aktion anzuwenden, fügen Sie den Filter der Aktion als Attribut hinzu: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Beispiel
Um den Filter auf alle Aktionen von `controller` anzuwenden, fügen Sie den Filter als Attribut der `controller`-Klasse hinzu: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Beispiel
Um den Filter global auf alle Web-API-Controller anzuwenden, fügen Sie eine Instanz des Filters der `GlobalConfiguration.Configuration.Filters`-Sammlung hinzu. Ausnahmefilter in dieser Sammlung gelten für alle Web-API-Controlleraktionen. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Beispiel
Für die Modellüberprüfung kann der Modellzustand an die CreateErrorResponse-Methode übergeben werden, wie unten dargestellt: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Überprüfen Sie die Links im Abschnitt mit Referenzen auf weitere Informationen zur Behandlung von Ausnahmen und zur Modellüberprüfung in der ASP.NET-Web-API 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Keine Sicherheitsdetails in Fehlermeldungen verfügbar machen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Generische Fehlermeldungen werden dem Benutzer direkt bereitgestellt, ohne dass sensible Anwendungsdaten eingebunden werden. Beispiele für sensible Daten:</p><ul><li>Servernamen</li><li>Verbindungszeichenfolgen</li><li>Benutzernamen</li><li>Kennwörter</li><li>SQL-Prozeduren</li><li>Details von dynamischen SQL-Fehlern</li><li>Stapelüberwachungen und Codezeilen</li><li>Variablen im Speicher</li><li>Laufwerks- und Ordnerpfade</li><li>Anwendungsinstallationspunkte</li><li>Hostkonfigurationseinstellungen</li><li>Andere interne Anwendungsdetails</li></ul><p>Das Abfangen aller Fehler in einer Anwendung und das Bereitstellen generischer Fehlermeldungen sowie das Aktivieren benutzerdefinierter Fehler in IIS können dabei helfen, die Offenlegung von Informationen zu verhindern. Die Ausnahmebehandlung von SQL Server-Datenbanken und .NET sind neben anderen Architekturen für die Fehlerbehandlung besonders ausführlich und äußerst nützlich für einen böswilliger Benutzer, der ein Profil Ihrer Anwendung erstellt. Zeigen Sie den Inhalt einer Klasse, die von der .NET-Ausnahmeklasse abgeleitet wurde, nicht direkt an, und stellen Sie sicher, dass Sie über eine geeignete Ausnahmebehandlung verfügen, sodass eine unerwartete Ausnahme nicht versehentlich dem Benutzer direkt angezeigt wird.</p><ul><li>Stellen Sie für den Benutzer generische Fehlermeldungen bereit, aus denen spezifische Details entfernt wurden, die in der Ausnahme-/Fehlermeldung enthalten sind.</li><li>Zeigen Sie den Inhalt einer .NET-Ausnahmeklasse dem Benutzer nicht direkt an.</li><li>Fangen Sie alle Fehlermeldungen ab, und informieren Sie den Benutzer ggf. mithilfe einer generischen Fehlermeldung, die an den Anwendungsclient gesendet wird.</li><li>Machen Sie den Inhalt der Ausnahmeklasse dem Benutzer nicht direkt verfügbar. Dies gilt insbesondere für den Rückgabewert von `.ToString()` bzw. die Werte der Message- oder StackTrace-Eigenschaften. Protokollieren Sie diese Informationen auf sichere Weise, und zeigen Sie dem Benutzer eine unverfängliche Meldung an.</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementieren der Standardseite für die Fehlerbehandlung

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Dialogfeld „ASP.NET-Einstellungen für Fehlerseiten bearbeiten“](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **Schritte** | <p>Wenn eine ASP.NET-Anwendung fehlschlägt und zu einem Fehler vom Typ „HTTP/1.x 500: Interner Serverfehler“ führt oder wenn eine Funktionskonfiguration (z.B. Anforderungsfilterung) verhindert, dass eine Seite angezeigt wird, wird eine Fehlermeldung generiert. Administratoren können auswählen, ob die Anwendung dem Client eine benutzerfreundliche Meldung oder eine detaillierte Fehlermeldung anzeigt bzw. die detaillierte Fehlermeldung nur für „localhost“ anzeigt. Das `<customErrors>`-Tag in der Datei „web.config“ verfügt über drei Modi:</p><ul><li>**On:** Gibt an, dass benutzerdefinierte Fehler aktiviert sind. Benutzer sehen einen allgemeinen Fehler, wenn kein defaultRedirect-Attribut angegeben wird. Die benutzerdefinierten Fehler werden auf den Remoteclients und dem lokalen Host angezeigt.</li><li>**Off:** Gibt an, dass benutzerdefinierte Fehler deaktiviert sind. Die detaillierten ASP.NET-Fehler werden auf den Remoteclients und dem lokalen Host angezeigt.</li><li>**RemoteOnly:** Gibt an, dass benutzerdefinierte Fehler nur auf den Remoteclients angezeigt werden, und dass ASP.NET-Fehler auf dem lokalen Host angezeigt werden. Dies ist der Standardwert.</li></ul><p>Öffnen Sie die Datei `web.config` für die Anwendung/Website, und stellen Sie sicher, dass das Tag als `<customErrors mode="RemoteOnly" />` oder `<customErrors mode="On" />` definiert ist.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Festlegen der Bereitstellungsmethode auf „Retail“ in IIS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [deployment-Element (ASP.NET-Einstellungsschema)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **Schritte** | <p>Der Switch `<deployment retail>` ist für die Verwendung auf IIS-Produktionsservern vorgesehen. Dieser Switch wird verwendet, damit Anwendungen mit der bestmöglichen Leistung und einer möglichst geringen Offenlegung von Sicherheitsinformationen ausgeführt werden. Dazu werden folgende Funktionen in der Anwendung deaktiviert: Generieren der Ausgabe der Ablaufverfolgung auf einer Seite, Anzeigen detaillierter Fehlermeldungen für die Endbenutzer und der Debugswitch.</p><p>Häufig sind Switches und Optionen, die für Entwickler vorgesehen sind, z.B. Fehler bei der Ablaufverfolgung und beim Debuggen von Anforderungen, während der aktiven Entwicklung aktiviert. Es wird empfohlen, die Methode für die Bereitstellung auf allen Produktionsserver auf „retail“ festzulegen. Öffnen Sie die Datei „machine.config“, und stellen Sie sicher, dass `<deployment retail="true" />` weiterhin auf „true“ festgelegt ist.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Sicherer Ausfall bei Ausnahmen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Sicherer Ausfall](https://owasp.org/www-community/Fail_securely) |
| **Schritte** | Anwendungen sollten sicher ausfallen. Jede Methode, die einen booleschen Wert zurückgibt, der auf der getroffen Entscheidung basiert, sollte über einen sorgfältig erstellten Ausnahmeblock verfügen. Es gibt viele logische Fehler, die zu Sicherheitsproblemen führen können, wenn der Ausnahmeblock nicht sorgfältig geschrieben ist.|

### <a name="example"></a>Beispiel
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Die oben dargestellte Methode gibt immer „True“ zurück, wenn eine Ausnahme auftritt. Wenn der Endbenutzer eine falsch formatierte URL bereitstellt, die der Browser respektiert, der `Uri()`-Konstruktor aber nicht, wird eine Ausnahme ausgelöst, und der Benutzer gelangt zur gültigen, aber falsch formatierten URL.