---
title: Diagnostizieren von Fehlern und Ausnahmen in Web-Apps mit Azure Application Insights | Microsoft Docs
description: Erfassen von Ausnahmen von ASP.NET-Apps zusammen mit der Anforderungstelemetrie.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb32069de295b883cdc6d3a9fa495b1bea719c39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60691815"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostizieren von Ausnahmen in Ihren Web-Apps mit Application Insights
Ausnahmen in Ihrer Live-Web-App werden von [Application Insights](../../azure-monitor/app/app-insights-overview.md) gemeldet. Auf diese Weise können Sie Anforderungsfehler mit Ausnahmen und anderen Ereignissen auf dem Client und auf dem Server zueinander in Beziehung setzen und dadurch die Ursachen schnell diagnostizieren.

## <a name="set-up-exception-reporting"></a>Einrichten der Ausnahmeerfassung
* So werden Ausnahmen von Ihrer Server-App gemeldet:
  * Installieren Sie das [Application Insights SDK](../../azure-monitor/app/asp-net.md) in Ihrer App.
  * IIS-Webserver: Führen Sie den [Application Insights-Agent](../../azure-monitor/app/monitor-performance-live-website-now.md) aus, oder
  * Azure-Web-Apps: Fügen Sie die [Application Insights-Erweiterung](../../azure-monitor/app/azure-web-apps.md) hinzu.
  * Java-Web-Apps: Installieren Sie den [Java-Agent](../../azure-monitor/app/java-agent.md).
* Installieren Sie den [JavaScript-Codeausschnitt](../../azure-monitor/app/javascript.md) in Ihren Webseiten, um Browserausnahmen zu erfassen.
* In einigen Anwendungsframeworks oder bei bestimmten Einstellungen müssen Sie einige zusätzliche Schritte ausführen, um weitere Ausnahmen zu erfassen:
  * [Webformulare](#web-forms)
  * [MVC](#mvc)
  * [Web-API 1.*](#web-api-1x)
  * [Web-API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostizieren von Ausnahmen mithilfe von Visual Studio
Öffnen Sie für das Debuggen die App-Projektmappe in Visual Studio.

Führen Sie die App entweder auf dem Server oder Ihrem Entwicklungscomputer aus, indem Sie F5 drücken.

Öffnen Sie das Application Insights-Fenster „Suchen“ in Visual Studio, und legen Sie es auf auf das Anzeigen von Ereignissen aus Ihrer App fest. Während Sie debuggen, können Sie dazu einfach auf die Schaltfläche „Application Insights“ klicken.

![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights > Öffnen“ aus.](./media/asp-net-exceptions/34.png)

Beachten Sie, dass Sie den Bericht so filtern können, dass nur Ausnahmen angezeigt werden.

*Es werden keine Ausnahmen angezeigt? Informationen hierzu finden Sie unter [Erfassen von Ausnahmen](#exceptions).*

Klicken Sie auf einen Ausnahmebericht, um dessen Stapelüberwachung anzuzeigen.
Klicken Sie in der Stapelüberwachung auf einen Zeilenverweis, um die entsprechende Codedatei zu öffnen.

Beachten Sie, dass CodeLens im Code Daten zu den Ausnahmen anzeigt:

![CodeLens-Benachrichtigung über Ausnahmen.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostizieren von Fehlern im Azure-Portal
Application Insights umfasst eine kuratierte APM-Benutzeroberfläche, die Sie beim Diagnostizieren von Fehlern in Ihren überwachten Anwendungen unterstützt. Klicken Sie zum Starten auf die Option „Fehler“ im Application Insights-Ressourcenmenü, das Sie im Abschnitt „Untersuchen“ finden.
Daraufhin wird eine Vollbildansicht geöffnet, die die Fehlerratentrends für Ihre Anforderungen anzeigt und Informationen dazu bietet, bei wie vielen Anforderungen Fehler auftreten und wie viele Benutzer betroffen sind. Auf der rechten Seite sehen Sie einige der nützlichsten Verteilungen für den ausgewählten fehlerhaften Vorgang, einschließlich der Top-3-Antwortcodes, der Top-3-Ausnahmetypen und der Top-3-Abhängigkeitstypen mit Fehlern.

![Ansicht der Fehlerselektierung (Registerkarte mit Vorgängen)](./media/asp-net-exceptions/FailuresTriageView.png)

Mit nur einem Klick können Sie dann repräsentative Stichproben für jede dieser Vorgangsteilmengen überprüfen. Um Ausnahmen zu diagnostizieren, können Sie auf die Anzahl einer bestimmten Ausnahme klicken. Daraufhin wird ein Blatt mit Ausnahmedetails angezeigt, wie z.B. das folgende:

![Blatt mit Ausnahmedetails](./media/asp-net-exceptions/ExceptionDetailsBlade.png)

Anstatt Ausnahmen eines bestimmten Vorgangs mit Fehlern anzuzeigen, können Sie **alternativ dazu** in der Gesamtübersicht der Ausnahmen beginnen, indem Sie zur Registerkarte „Ausnahmen“ wechseln:

![Ansicht der Fehlerselektierung (Registerkarte mit Ausnahmen)](./media/asp-net-exceptions/FailuresTriageView_Exceptions.png)

Hier können Sie alle Ausnahmen anzeigen, die für Ihre überwachte App erfasst wurden.

*Es werden keine Ausnahmen angezeigt? Informationen hierzu finden Sie unter [Erfassen von Ausnahmen](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Benutzerdefinierte Ablaufverfolgung und Protokolldaten
Um spezifische Diagnosedaten für Ihre App zu erhalten, können Sie Code zum Senden Ihrer eigenen Telemetriedaten einfügen. Diese werden in der Diagnosesuche neben der Anforderung, der Seitenansicht und anderen automatisch erfassten Daten angezeigt.

Sie haben mehrere Möglichkeiten:

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) wird normalerweise zum Überwachen von Verwendungsmustern verwendet, jedoch werden die damit gesendeten Daten auch unter den benutzerdefinierten Ereignissen in der Diagnosesuche angezeigt. Ereignisse werden benannt und können Zeichenfolgeneigenschaften und numerische Metriken aufweisen, nach denen Sie [Ihre Diagnosesuchvorgänge filtern](../../azure-monitor/app/diagnostic-search.md) können.
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) können Sie längere Daten wie POST-Informationen senden.
* [TrackException()](#exceptions) sendet Stapelüberwachungen. [Weitere Informationen über Ausnahmen](#exceptions).
* Wenn Sie bereits ein Protokollierungsframework wie Log4Net oder NLog verwenden, können Sie [diese Protokolle erfassen](asp-net-trace-logs.md) und in der Diagnosesuche neben Anforderungs- und Ausnahmedaten anzeigen.

Öffnen Sie zum Anzeigen dieser Ereignisse [Suchen](../../azure-monitor/app/diagnostic-search.md) und anschließend „Filter“, und wählen Sie anschließend „Benutzerdefiniertes Ereignis“, „Ablaufverfolgung“ oder „Ausnahme“.

![Drillthrough](./media/asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Wenn die Anwendung viele Telemetriedaten generiert, reduziert das adaptive Stichprobenmodul automatisch die an das Verwaltungsportal gesendete Datenmenge, indem nur ein repräsentativer Bruchteil der Ereignisse gesendet wird. Ereignisse, die Teil des gleichen Vorgangs sind, werden als Gruppe aus- oder abgewählt, sodass Sie zwischen verwandten Ereignissen navigieren können. [Erfahren Sie mehr über das Erstellen von Stichproben.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Anzeigen von POST-Daten von Anforderungen
Anforderungsdetails enthalten nicht die Daten, die in einem POST-Aufruf an Ihre App gesendet wurden. So werden diese Daten gemeldet:

* [Installieren Sie das SDK](../../azure-monitor/app/asp-net.md) in Ihrem Anwendungsprojekt.
* Fügen Sie Code in die Anwendung ein, um [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) aufzurufen. Senden Sie die POST-Daten im "message"-Parameter. Es gibt eine Größenbeschränkung, daher sollten Sie versuchen, nur die notwendigen Daten zu senden.
* Wenn Sie eine fehlerhafte Anforderung untersuchen, suchen Sie die zugehörigen Ablaufverfolgungen.

![Drillthrough](./media/asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Erfassen von Ausnahmen und zugehörigen Diagnosedaten
Zunächst werden im Portal nicht alle Ausnahmen angezeigt, die in Ihrer App zu Fehlern führen. Sie sehen alle Browserausnahmen (bei Verwendung des [JavaScript-SDK](../../azure-monitor/app/javascript.md) in Ihren Webseiten). Die meisten Serverausnahmen werden jedoch von IIS abgefangen, und Sie müssen ein wenig Code schreiben, um sie anzuzeigen.

Ihre Möglichkeiten:

* **Ausnahmen explizit protokollieren** durch Einfügen von Code in Ausnahmehandlern, um Ausnahmen zu melden.
* **Ausnahmen automatisch erfassen** durch Konfigurieren Ihres ASP.NET-Frameworks. Die erforderlichen Änderungen unterscheiden sich für verschiedene Frameworktypen.

## <a name="reporting-exceptions-explicitly"></a>Explizites Melden von Ausnahmen
Am einfachsten ist es, in einem Ausnahmehandler einen Aufruf in TrackException() einzufügen.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Die Eigenschaften und Messparameter sind optional, aber hilfreich zum [Filtern und Hinzufügen](../../azure-monitor/app/diagnostic-search.md) zusätzlicher Informationen. Wenn Sie z. B. eine Anwendung haben, die mehrere Spiele ausführen kann, können Sie alle im Zusammenhang mit einem bestimmten Spiel stehenden Ausnahmeberichte ermitteln. Sie können jedem Wörterbuch beliebig viele Elemente hinzufügen.

## <a name="browser-exceptions"></a>Browserausnahmen
Die meisten Browserausnahmen werden gemeldet.

Wenn Ihre Webseite Skriptdateien aus Content Delivery Networks oder anderen Domänen umfasst, stellen Sie sicher, dass Ihr Skript-Tag das Attribut ```crossorigin="anonymous"``` besitzt und dass der Server [CORS-Header](https://enable-cors.org/) sendet. Dadurch können Sie eine Stapelüberwachung und Details für nicht behandelte JavaScript-Ausnahmen von diesen Ressourcen erhalten.

## <a name="web-forms"></a>Webformulare
Für Webformulare kann das HTTP-Modul die Ausnahmen erfassen, wenn keine Umleitungen mit CustomErrors konfiguriert sind.

Wenn jedoch aktive Umleitungen bestehen, fügen Sie der Funktion "Application_Error" in "Global.asax.cs" die folgenden Zeilen hinzu. (Fügen Sie eine Datei "Global.asax" hinzu, falls noch keine vorhanden ist.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
Ab Application Insights Web SDK Version 2.6 (beta3 und höher) sammelt Application Insights Ausnahmefehler automatisch, die in den MVC 5+-Controllermethoden ausgelöst werden. Wenn Sie bereits vorher einen benutzerdefinierten Handler zum Nachverfolgen dieser Ausnahmen hinzugefügt haben (wie in den folgenden Beispielen beschrieben), können Sie ihn entfernen, um eine doppelte Nachverfolgung von Ausnahmen zu verhindern.

Es gibt eine Reihe von Fällen, die von den Ausnahmefiltern nicht verarbeitet werden können. Beispiel:

* Von Controllerkonstruktoren ausgelöste Ausnahmen.
* Von Meldungshandlern ausgelöste Ausnahmen
* Während des Routings ausgelöste Ausnahmen.
* Während der Serialisierung von Antwortinhalten ausgelöste Ausnahmen.
* Ausnahme, die während des Anwendungsstarts ausgelöst wird.
* In Hintergrundaufgaben ausgelöste Ausnahme.

Alle Ausnahmen die von der Anwendung *verarbeitet* werden, müssen weiterhin manuell nachverfolgt werden.
Ausnahmefehler, die von Controllern ausgehen, führen normalerweise zu folgender Antwort: 500 „Interner Serverfehler“. Wenn eine Antwort dieser Art aufgrund einer behandelten Ausnahme (oder keiner Ausnahme) manuell erstellt wird, wird sie in der dazugehörigen Anforderungstelemetrie mit „`ResultCode` 500“ nachverfolgt. Mit dem Application Insights SDK kann die entsprechende Ausnahme aber nicht nachverfolgt werden.

### <a name="prior-versions-support"></a>Unterstützung von früheren Versionen
Wenn Sie MVC 4 (und früher) des Application Insights Web SDK 2.5 (und früher) verwenden, sind die folgenden Beispiele zum Nachverfolgen von Ausnahmen hilfreich.

Wenn die [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx)-Konfiguration `Off` lautet, stehen für das zu erfassende [HTTP-Modul](https://msdn.microsoft.com/library/ms178468.aspx) Ausnahmen zur Verfügung. Lautet sie allerdings `RemoteOnly` (Standardeinstellung) oder `On`, wird die Ausnahme gelöscht und steht für die automatische Erfassung durch Application Insights nicht zur Verfügung. Diesen Umstand können Sie beheben, indem Sie die [System.Web.Mvc.HandleErrorAttribute-Klasse](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx) außer Kraft setzen und die außer Kraft gesetzte Klasse wie unten gezeigt für die verschiedenen MVC-Versionen anwenden ([GitHub-Quelle](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Ersetzen Sie das HandleError-Attribut durch das neue Attribut in Ihren Controllern.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Beispiel](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registrieren Sie `AiHandleErrorAttribute` als globalen Filter in "Global.asax.cs":

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Beispiel](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registrieren Sie "AiHandleErrorAttribute" als globalen Filter in "FilterConfig.cs":

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Beispiel](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web-API
Ab Application Insights Web SDK Version 2.6 (beta3 und höher) sammelt Application Insights Ausnahmefehler, die in den Controllermethoden ausgelöst werden, für WebAPI 2+ automatisch. Wenn Sie bereits vorher einen benutzerdefinierten Handler zum Nachverfolgen dieser Ausnahmen hinzugefügt haben (wie in den folgenden Beispielen beschrieben), können Sie ihn entfernen, um eine doppelte Nachverfolgung von Ausnahmen zu verhindern.

Es gibt eine Reihe von Fällen, die von den Ausnahmefiltern nicht verarbeitet werden können. Beispiel:

* Von Controllerkonstruktoren ausgelöste Ausnahmen.
* Von Meldungshandlern ausgelöste Ausnahmen
* Während des Routings ausgelöste Ausnahmen.
* Während der Serialisierung von Antwortinhalten ausgelöste Ausnahmen.
* Ausnahme, die während des Anwendungsstarts ausgelöst wird.
* In Hintergrundaufgaben ausgelöste Ausnahme.

Alle Ausnahmen die von der Anwendung *verarbeitet* werden, müssen weiterhin manuell nachverfolgt werden.
Ausnahmefehler, die von Controllern ausgehen, führen normalerweise zu folgender Antwort: 500 „Interner Serverfehler“. Wenn eine Antwort dieser Art aufgrund einer behandelten Ausnahme (oder keiner Ausnahme) manuell erstellt wird, wird sie in einer dazugehörigen Anforderungstelemetrie mit „`ResultCode` 500“ nachverfolgt. Mit dem Application Insights SDK kann die entsprechende Ausnahme aber nicht nachverfolgt werden.

### <a name="prior-versions-support"></a>Unterstützung von früheren Versionen
Wenn Sie WebAPI 1 (und früher) des Application Insights Web SDK 2.5 (und früher) verwenden, sind die folgenden Beispiele zum Nachverfolgen von Ausnahmen hilfreich.

#### <a name="web-api-1x"></a>Web-API 1.x
Setzen Sie "System.Web.Http.Filters.ExceptionFilterAttribute" außer Kraft:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Sie könnten dieses außer Kraft gesetzte Attribut bestimmten Controllern oder der globalen Filterkonfiguration in der WebApiConfig-Klasse hinzufügen:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Beispiel](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web-API 2.x
Fügen Sie eine Implementierung von IExceptionLogger hinzu:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Fügen Sie den Diensten in WebApiConfig Folgendes hinzu:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Beispiel](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Als Alternativen können Sie folgende Aktionen ausführen:

1. Ersetzen Sie den einzigen ExceptionHandler durch eine benutzerdefinierte Implementierung von IExceptionHandler. Diese wird nur aufgerufen, wenn das Framework weiterhin wählen kann, welche Antwortnachricht gesendet wird (nicht wenn z. B. die Verbindung abgebrochen wird).
2. Ausnahmefilter (wie im obigen Abschnitt zu Web-API-1.x-Controllern beschrieben) – werden nicht in allen Fällen aufgerufen.

## <a name="wcf"></a>WCF
Fügen Sie eine Klasse hinzu, die "Attribute" erweitert und "IErrorHandler" und "IServiceBehavior" implementiert.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Beispiel](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Ausnahmeleistungsindikatoren
Wenn Sie den [Application Insights-Agent auf Ihrem Server installiert haben](../../azure-monitor/app/monitor-performance-live-website-now.md), können Sie ein Diagramm mit der von .NET gemessenen Ausnahmenrate abrufen. Dies enthält sowohl behandelte als auch nicht behandelte .NET-Ausnahmen.

Öffnen Sie ein "Metrik-Explorer"-Blatt, fügen Sie ein neues Diagramm hinzu, und wählen **Ausnahmerate**aus, das unter Leistungsindikatoren aufgelistet wird.

.NET Framework berechnet die Rate, indem die Anzahl von Ausnahmen innerhalb eines Intervalls gezählt und diese durch die Länge des Intervalls geteilt wird.

Dieser Wert unterscheidet sich von der Anzahl der „Ausnahmen“, die vom Application Insights-Portal durch Zählen von TrackException-Meldungen berechnet wird. Die Samplingintervalle sind unterschiedlich, und das SDK sendet keine TrackException-Meldungen für alle behandelten und nicht behandelten Ausnahmen.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen von REST, SQL und anderen Aufrufen von Abhängigkeiten](../../azure-monitor/app/asp-net-dependencies.md)
* [Überwachen von Seitenladezeiten, Browserausnahmen und AJAX-Aufrufen](../../azure-monitor/app/javascript.md)
* [Überwachen von Leistungsindikatoren](../../azure-monitor/app/performance-counters.md)
