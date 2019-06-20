---
title: Datenaufbewahrung und -speicherung in Azure Application Insights | Microsoft-Dokumentation
description: Hinweis zur Datenaufbewahrung und Datenschutzrichtlinie
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mbullwin
ms.openlocfilehash: 38723a5dd306c2a4b594d95e5cc660d117966bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65518841"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Datensammlung, -aufbewahrung und -speicherung in Application Insights

Wenn Sie das [Visual Studio Application Insights-SDK][start] in Ihrer App installieren, werden Telemetriedaten für Ihre App an die Cloud gesendet. Als Entwickler möchten Sie natürlich genau wissen, welche Daten gesendet werden, was mit den Daten geschieht und wie Sie die Kontrolle darüber behalten. Dabei ist insbesondere interessant, ob womöglich sensible Daten übermittelt werden, wo sie gespeichert werden und wie sicher die Daten sind. 

Zunächst die kurze Antwort:

* Bei Verwendung der Standardtelemetriemodule in der vorkonfigurierten Form ist es äußerst unwahrscheinlich, dass sensible Daten an den Dienst übermittelt werden. Bei der Telemetrie dreht sich alles um Lade-, Leistungs- und Nutzungsmetriken sowie um Ausnahmeberichte und andere Diagnosedaten. In den Diagnoseberichten sind als Benutzerdaten hauptsächlich URLs sichtbar. Ihre App sollte jedoch in keinem Fall sensible Daten im Nur-Text-Format in eine URL einfügen.
* Sie können Code schreiben, der zusätzliche benutzerdefinierte Telemetriedaten sendet, die Sie bei der Diagnose und Überwachung der Nutzung unterstützen. (Diese Erweiterbarkeit ist ein großartiges Feature von Application Insights.) Der Code kann theoretisch so geschrieben werden, dass versehentlich auch persönliche und andere sensible Daten einbezogen werden. Falls Ihre Anwendung mit solchen Daten arbeitet, müssen Sie sämtlichen Code, den Sie schreiben, einer gründlichen Prüfung unterziehen.
* Beim Entwickeln und Testen Ihrer App können Sie problemlos überprüfen, was vom SDK gesendet wird. Die Daten erscheinen in den Debugging-Ausgabefenstern von IDE und Browser. 
* Die Daten werden auf [Microsoft Azure](https://azure.com)-Servern in den USA oder in Europa gespeichert. (Ihre App kann jedoch überall ausgeführt werden.) Azure verfügt über [ausgefeilte Sicherheitsprozesse und erfüllt eine breite Palette von Kompatibilitätsstandards](https://azure.microsoft.com/support/trust-center/). Auf die Daten haben nur Sie selbst und Ihr Team Zugriff. Microsoft-Mitarbeiter können nur unter ganz bestimmten Bedingungen und mit Ihrem Wissen eingeschränkten Zugriff auf die Daten erlangen. Die Daten sind während der Übertragung und im Ruhezustand verschlüsselt.

Im Rest dieses Artikels werden die obigen Antworten näher erläutert. Der Artikel ist in sich geschlossen, sodass Sie ihn auch Kollegen zeigen können, die nicht direkt Ihrem Team angehören.

## <a name="what-is-application-insights"></a>Was ist Application Insights?
[Visual Studio Application Insights][start] ist ein Dienst von Microsoft, der Sie beim Optimieren der Leistung und Benutzerfreundlichkeit Ihrer aktiven Anwendung unterstützt. Er überwacht Ihre Anwendung während der gesamten Ausführung – sowohl in der Testphase als auch nach der Veröffentlichung oder Bereitstellung. Application Insights erstellt Diagramme und Tabellen, die beispielsweise Aufschluss darüber geben, zu welchen Tageszeiten das Benutzerinteresse besonders groß ist, wie gut die App reagiert und wie gut sie von externen Diensten versorgt wird, von denen sie unter Umständen abhängig ist. Im Falle von Abstürzen, Fehlern oder Leistungsproblemen können Sie die Telemetriedaten im Detail durchsuchen, um die Fehlerursache zu ermitteln. Darüber hinaus informiert Sie der Dienst per E-Mail, falls sich die Verfügbarkeit oder Leistung Ihrer App ändert.

Diese Funktionen erhalten Sie, indem Sie das Application Insights-SDK in Ihrer Anwendung installieren und es so in den Code integrieren. Während der App-Ausführung überwacht das SDK den Betrieb und sendet Telemetriedaten an den Application Insights-Dienst. Hierbei handelt es sich um einen von [Microsoft Azure](https://azure.com)gehosteten Clouddienst. (Application Insights kann aber nicht nur für in Azure gehostete Anwendungen, sondern für jede beliebige Anwendung verwendet werden.)

Der Application Insights-Dienst speichert und analysiert die Telemetriedaten. Wenn Sie sich die Analyse ansehen oder die gespeicherten Telemetriedaten durchsuchen möchten, melden Sie sich bei Ihrem Azure-Konto an, und öffnen Sie die Application Insights-Ressource für Ihre Anwendung. Sie können auch anderen Mitgliedern Ihres Teams oder bestimmten Azure-Abonnenten Zugriff auf die Daten gewähren.

Die Daten können vom Application Insights-Dienst exportiert werden (etwa in eine Datenbank oder in externe Tools). Für jedes Tool muss ein spezieller Schlüssel angegeben werden, den Sie über den Dienst erhalten. Der Schlüssel kann bei Bedarf widerrufen werden. 

Application Insights SDKs stehen für eine Reihe von Anwendungstypen zur Verfügung. Hierzu zählen auf eigenen Java EE- oder ASP.NET-Servern oder in Azure gehostete Webdienste, Webclients (also der innerhalb einer Webseite ausgeführte Code), Desktop-Apps und -Dienste sowie Geräte-Apps (etwa für Windows Phone, iOS und Android). Alle diese Anwendungen senden Telemetriedaten an den gleichen Dienst.

## <a name="what-data-does-it-collect"></a>Welche Daten werden dabei gesammelt?
### <a name="how-is-the-data-is-collected"></a>Wie werden die Daten gesammelt?
Es werden drei Datenquellen verwendet:

* Das SDK, das Sie entweder [bei der Entwicklung](../../azure-monitor/app/asp-net.md) oder [zur Laufzeit](../../azure-monitor/app/monitor-performance-live-website-now.md) in Ihre App integrieren. Für die unterschiedlichen Anwendungstypen stehen jeweils unterschiedliche SDKs zur Verfügung. Darunter ist auch ein [SDK für Webseiten](../../azure-monitor/app/javascript.md), das zusammen mit der Seite in den Browser des Endbenutzers geladen wird.
  
  * Jedes SDK verfügt über eine Reihe von [Modulen](../../azure-monitor/app/configuration-with-applicationinsights-config.md), die mithilfe unterschiedlicher Techniken verschiedene Arten von Telemetriedaten erfassen.
  * Wenn Sie das SDK während der Entwicklung installieren, können Sie zusätzlich zu den Standardmodulen die API des SDK verwenden, um Ihre eigenen Telemetriedaten zu senden. Diese benutzerdefinierten Telemetriedaten können beliebige Daten enthalten, die Sie senden möchten.
* Einige Webserver verfügen auch über Agents, die gemeinsam mit der App ausgeführt werden und Telemetriedaten zu CPU-, Arbeitsspeicher- und Netzwerkauslastung senden. Solche Agents können etwa bei Azure-VMs, Docker-Hosts und [Java EE-Servern](../../azure-monitor/app/java-agent.md) vorhanden sein.
* [Verfügbarkeitstests](../../azure-monitor/app/monitor-web-app-availability.md) werden von Microsoft in regelmäßigen Abständen Anforderungen an Ihre Web-App gesendet. Die Ergebnisse werden dann an den Application Insights-Dienst gesendet.

### <a name="what-kinds-of-data-are-collected"></a>Welche Arten von Daten werden gesammelt?
Die gesammelten Daten lassen sich in folgende Hauptkategorien unterteilen:

* [Webserver-Telemetriedaten](../../azure-monitor/app/asp-net.md) : HTTP-Anforderungen.  URI, Anforderungsverarbeitungsdauer, Antwortcode, Client-IP-Adresse. Sitzungs-ID.
* [Webseiten](../../azure-monitor/app/javascript.md) : Seiten-, Benutzer- und Sitzungszähler. Seitenladezeiten. Ausnahmen. AJAX-Aufrufe.
* Leistungsindikatoren: Arbeitsspeicher-, CPU-, E/A-, Netzwerkauslastung.
* Client- und Serverkontext: Betriebssystem, Gebietsschema, Gerätetyp, Browser, Bildschirmauflösung.
* [Ausnahmen](../../azure-monitor/app/asp-net-exceptions.md) und Abstürze: **Stapelabbilder**, Build-ID, CPU-Typ. 
* [Abhängigkeiten](../../azure-monitor/app/asp-net-dependencies.md) : Aufrufe an externe Dienste wie etwa REST, SQL und AJAX. URI oder Verbindungszeichenfolge, Dauer, Erfolg, Befehl.
* [Verfügbarkeitstests](../../azure-monitor/app/monitor-web-app-availability.md) : Testdauer und Schritte, Antworten.
* [Ablaufverfolgungsprotokolle](../../azure-monitor/app/asp-net-trace-logs.md) und [benutzerdefinierte Telemetriedaten](../../azure-monitor/app/api-custom-events-metrics.md) - **alles, was Sie als Code in Ihre Protokolle oder Telemetrie integrieren**.

[Weitere Details](#data-sent-by-application-insights)

## <a name="how-can-i-verify-whats-being-collected"></a>Wie kann ich überprüfen, was gesammelt wird?
Wenn Sie die App mit Visual Studio entwickeln, führen Sie sie im Debugmodus (F5) aus. Die Telemetriedaten werden im Ausgabefenster angezeigt. Dort können Sie sie kopieren und zur einfachen Überprüfung als JSON formatieren. 

![](./media/data-retention-privacy/06-vs.png)

Es gibt auch eine lesbarere Ansicht im Fenster „Diagnose“.

Öffnen Sie bei einer Webseite das Debuggingfenster Ihres Browsers.

![Drücken Sie F12, und öffnen Sie die Registerkarte „Netzwerk“.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kann ich Code schreiben, um die Telemetriedaten vor dem Senden zu filtern?
Hierzu können Sie ein [Telemetriedaten-Prozessor-Plug-In](../../azure-monitor/app/api-filtering-sampling.md)schreiben.

## <a name="how-long-is-the-data-kept"></a>Wie lange werden Daten aufbewahrt?
Rohdatenpunkte (also Elemente, die Sie in Analytics abfragen und in Search überprüfen können) werden bis zu 90 Tage lang aufbewahrt. Wenn Sie Daten länger beibehalten möchten, können Sie sie mit dem [fortlaufenden Export](../../azure-monitor/app/export-telemetry.md) in ein Speicherkonto kopieren.

Aggregierte Daten (d.h. Zählungen, Mittelwerte und andere statistischen Daten, die im Metrik-Explorer angezeigt werden) werden im Maß von 1 Minute für 90 Tage aufbewahrt.

> [!NOTE]
> Die variable Aufbewahrungsdauer für Application Insights befindet sich derzeit in der Vorschauphase. [Hier](https://feedback.azure.com/forums/357324-application-insights/suggestions/17454031)erhalten Sie weitere Informationen. 

[Debugmomentaufnahmen](../../azure-monitor/app/snapshot-debugger.md) werden fünfzehn Tage lang gespeichert. Diese Aufbewahrungsrichtlinie wird für jede Anwendung separat festgelegt. Wenn Sie diesen Wert erhöhen möchten, können Sie eine Erhöhung anfordern, indem Sie einen Supportfall im Azure-Portal eröffnen.

## <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?
Die Daten sind für Sie und, wenn Sie über ein Unternehmenskonto verfügen, für die Teammitglieder sichtbar. 

Sie und Ihre Teammitglieder können die Daten exportieren und an andere Speicherorte kopieren oder Sie an andere Personen weiterleiten.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Wie verwendet Microsoft die Daten, die meine App an Application Insights sendet?
Microsoft verwendet die Daten nur dazu, Ihnen den Dienst bereitstellen zu können.

## <a name="where-is-the-data-held"></a>Wo werden die Daten gespeichert?
* In den USA, Europa oder Südostasien. Sie können den Speicherort auswählen, wenn Sie eine neue Application Insights-Ressource erstellen. 

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Bedeutet dies, dass meine App in den USA, in Europa oder Südostasien gehostet werden muss?
* Nein. Die Anwendung kann überall ausgeführt werden – auf Ihren eigenen lokalen Hosts oder in der Cloud.

## <a name="how-secure-is-my-data"></a>Wie sicher sind meine Daten?
Application Insights ist ein Azure-Dienst. Sicherheitsrichtlinien werden im Whitepaper [Azure Security, Privacy, and Compliance (Azure-Sicherheit, -Datenschutz und -Kompatibilität)](https://go.microsoft.com/fwlink/?linkid=392408) beschrieben.

Die Daten werden auf Microsoft Azure-Servern gespeichert. Für Konten im Azure-Portal sind die Kontobeschränkungen im Dokument [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance](https://go.microsoft.com/fwlink/?linkid=392408)(in englischer Sprache) beschrieben.

Der Zugriff auf Ihre Daten durch Microsoft-Mitarbeiter ist eingeschränkt. Wir werden nur mit Ihrer Erlaubnis und ausschließlich dann auf Ihre Daten zugreifen, wenn es erforderlich ist, Sie bei der Verwendung von Application Insights zu unterstützen. 

Aggregierte Daten in den Anwendungen unserer Kunden (z. B. Datenraten und die durchschnittliche Größe von Ablaufverfolgungen) werden zur Verbesserung von Application Insights verwendet.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Könnte die Telemetrie eines anderen Benutzers meine Application Insights-Daten beeinträchtigen?
Sie könnten zusätzliche Telemetrie mit dem Instrumentationsschlüssel im Code Ihrer Webseiten an Ihr Konto senden. Bei genügend zusätzlichen Daten würden die Metriken die Leistung und Nutzung Ihrer App nicht ordnungsgemäß darstellen.

Wenn Sie Code für andere Projekte freigeben, sollten Sie Ihren Instrumentationsschlüssel entfernen.

## <a name="is-the-data-encrypted"></a>Werden die Daten verschlüsselt?
Alle Daten sind im Ruhezustand und während der Übertragung zwischen Rechenzentren verschlüsselt.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Werden die Daten während der Übertragung von meiner Anwendung zu Application Insights-Servern verschlüsselt?
Ja, wir verwenden HTTPS zum Senden von Daten an das Portal aus nahezu allen SDKs, darunter Webserver, Geräte und HTTPS-Webseiten. Die einzige Ausnahme sind Daten, die von einfachen HTTP-Webseiten gesendet werden.

## <a name="does-the-sdk-create-temporary-local-storage"></a>Erstellt das SDK einen temporären lokalen Speicher?

Ja, bestimmte Telemetriekanäle speichern Daten dauerhaft lokal, wenn ein Endpunkt nicht erreicht werden kann. Weiter unten in diesem Artikel erfahren Sie, welche Frameworks und Telemetriekanäle betroffen sind.

Telemetriekanäle, die lokalen Speicher nutzen, erstellen temporäre Dateien in den Verzeichnissen TEMP oder APPDATA, die auf das Konto beschränkt sind, in dem Ihre Anwendung ausgeführt wird. Dies kann passieren, wenn ein Endpunkt vorübergehend nicht verfügbar war oder das Drosselungslimit erreicht wurde. Sobald das Problem gelöst wurde, setzt der Telemetriekanal das Senden aller neuen und dauerhaft gespeicherten Daten fort.

Diese dauerhaft gespeicherten Daten werden nicht lokal verschlüsselt. Falls dies ein Problem darstellt, überprüfen Sie die Daten, und schränken Sie die Sammlung von privaten Daten ein. (Weitere Informationen finden Sie unter [Exportieren und Löschen personenbezogener Daten](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data).)

Wenn ein Kunde dieses Verzeichnis mit bestimmten Sicherheitsanforderungen konfigurieren muss, kann diese Konfiguration pro Framework erfolgen. Stellen Sie sicher, dass der Prozess, der Ihre Anwendung ausführt, über Schreibzugriff auf dieses Verzeichnis verfügt. Stellen Sie aber außerdem sicher, dass dieses Verzeichnis geschützt ist, um zu verhindern, dass Telemetriedaten von Benutzern gelesen werden, die dafür nicht zugelassen sind.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` wird für die dauerhafte Speicherung von Daten verwendet. Dieser Speicherort kann nicht über das config-Verzeichnis konfiguriert werden, und die Zugriffsberechtigungen für diesen Ordner sind auf einen bestimmten Benutzer mit den erforderlichen Anmeldeinformationen beschränkt. (Näheres dazu finden Sie in der [Implementierung](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Standardmäßig verwendet `ServerTelemetryChannel` den lokalen Ordner des aktuellen Benutzers für App-Daten (`%localAppData%\Microsoft\ApplicationInsights`) oder den temporären Ordner (`%TMP%`). (Näheres dazu finden Sie in der [Implementierung](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84).)


Per Konfigurationsdatei:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Per Code:

- Entfernen Sie „ServerTelemetryChannel“ aus der Konfigurationsdatei.
- Fügen Sie diesen Codeausschnitt zu Ihrer Konfiguration hinzu:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Standardmäßig verwendet `ServerTelemetryChannel` den lokalen Ordner des aktuellen Benutzers für App-Daten (`%localAppData%\Microsoft\ApplicationInsights`) oder den temporären Ordner (`%TMP%`). (Näheres dazu finden Sie in der [Implementierung](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84).) In einer Linux-Umgebung ist der lokale Speicher deaktiviert, sofern kein Speicherordner angegeben wird.

Der folgende Codeausschnitt zeigt, wie Sie `ServerTelemetryChannel.StorageFolder` in der `ConfigureServices()` -Methode Ihrer  `Startup.cs` -Klasse festlegen:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Weitere Informationen finden Sie unter [AspNetCore Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration). )

### <a name="nodejs"></a>Node.js

Standardmäßig wird `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` für die dauerhafte Speicherung von Daten verwendet. Die Berechtigungen für den Zugriff auf diesen Ordner sind auf den aktuellen Benutzer und Administratoren beschränkt. (Näheres dazu finden Sie in der [Implementierung](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts).)

Das Ordnerpräfix `appInsights-node` kann überschrieben werden, indem der Laufzeitwert der statischen Variablen `Sender.TEMPDIR_PREFIX` geändert wird, die sich in [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384) befindet.



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Wie sende ich mit TLS 1.2 Daten an Application Insights?

Um die Sicherheit von Daten bei der Übertragung an die Application Insights-Endpunkte sicherzustellen, wird Kunden dringend empfohlen, ihre Anwendung so zu konfigurieren, dass mindestens Transport Layer Security (TLS) 1.2 verwendet wird. Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**, und die Industrie ist bestrebt, diese älteren Protokolle schnell auszumustern. 

Das [PCI Security Standards Council](https://www.pcisecuritystandards.org/) hat den [30. Juni 2018 als Termin](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) für die Deaktivierung älterer Versionen von TLS/SSL und das Upgrade auf sicherere Protokolle festgelegt. Wenn Azure keine Legacyunterstützung mehr anbietet und Ihre Anwendung/Clients nicht mindestens über TLS 1.2 kommunizieren können, können Sie keine Daten an Application Insights senden. Welche Methode Sie verwenden, um die TLS-Unterstützung Ihrer Anwendung zu testen und zu überprüfen, hängt vom Betriebssystem bzw. von der Plattform sowie von der Sprache bzw. vom Framework ab, die von Ihrer Anwendung verwendet werden.

Wir empfehlen nicht, Ihre Anwendung explizit so einzurichten, dass nur TLS 1.2 verwendet wird, es sei denn, dies ist unbedingt erforderlich. Denn dadurch können Sicherheitsfeatures auf Plattformebene deaktiviert werden, mit deren Hilfe neuere, sicherere Protokolle wie TLS 1.3 automatisch erkannt und genutzt werden können, sobald diese verfügbar sind. Es wird empfohlen, gründlich zu überprüfen, ob der Code einer Anwendung Hartcodierungen bestimmter TLS/SSL-Versionen enthält.

### <a name="platformlanguage-specific-guidance"></a>Plattform-/sprachspezifischer Leitfaden

|Plattform/Sprache | Support | Weitere Informationen |
| --- | --- | --- |
| Azure App Services  | Wird unterstützt, möglicherweise ist eine Konfiguration erforderlich. | Unterstützung wurde im April 2018 angekündigt. Informationen zur Ankündigung finden Sie in den [Konfigurationsdetails](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Azure Function-Apps | Wird unterstützt, möglicherweise ist eine Konfiguration erforderlich. | Unterstützung wurde im April 2018 angekündigt. Informationen zur Ankündigung finden Sie in den [Konfigurationsdetails](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Wird unterstützt, Konfiguration hängt von der Version ab. | Ausführliche Informationen zur Konfiguration für .NET 4.7 und frühere Versionen finden Sie in [diesen Anweisungen](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Statusmonitor | Wird unterstützt, Konfiguration erforderlich | Statusmonitor greift zur Unterstützung von TLS 1.2 auf die [Betriebssystemkonfiguration](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET-Konfiguration](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) zurück.
|Node.js |  Wird in Version 10.5.0 unterstützt, möglicherweise ist eine Konfiguration erforderlich. | Informationen zu anwendungsspezifischen Konfigurationen finden Sie in der [offiziellen Dokumentation zu TLS/SSL bei Node.js](https://nodejs.org/api/tls.html). |
|Java | Wird unterstützt, JDK-Unterstützung für TLS 1.2 seit [JDK 6 Update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) und [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | Bei JDK 8 wird [standardmäßig TLS 1.2 verwendet](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linux-Distributionen greifen zur Unterstützung von TLS 1.2 tendenziell auf [OpenSSL](https://www.openssl.org) zurück.  | Überprüfen Sie anhand des [OpenSSL-Änderungsprotokolls](https://www.openssl.org/news/changelog.html), ob Ihre Version von OpenSSL unterstützt wird.|
| Windows 8.0 bis 10 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) verwenden.  |
| Windows Server 2012 bis 2016 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) verwenden. |
| Windows 7 SP1 und Windows Server 2008 R2 SP1 | Wird unterstützt, ist jedoch standardmäßig deaktiviert. | Details zur Aktivierung finden Sie auf der Seite [Transport Layer Security (TLS) registry settings (Registrierungseinstellungen für Transport Layer Security (TLS))](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2008 SP2 | Unterstützung für TLS 1.2 muss aktualisiert werden. | Informationen hierzu finden Sie unter [Update zum Hinzufügen der Unterstützung von TLS 1.1 und TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) in Windows Server 2008 SP2. |
|Windows Vista | Nicht unterstützt. | N/V

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Prüfen, welche Version von OpenSSL von Ihrer Linux-Distribution ausgeführt wird

Zum Prüfen, welche Version von OpenSSL installiert ist, öffnen Sie das Terminal und führen Sie folgenden Befehl aus:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Ausführen einer TLS-1.2-Testtransaktion unter Linux

Zum Ausführen eines einfachen vorläufigen Tests, um festzustellen, ob Ihr Linux-System über TLS 1.2 kommunizieren kann, öffnen Sie das Terminal und führen Sie folgenden Befehl aus:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Personenbezogene Daten, die in Application Insights gespeichert sind

In unserem [Artikel über personenbezogene Daten in Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) wird dieses Thema ausführlich besprochen.

#### <a name="can-my-users-turn-off-application-insights"></a>Können meine Benutzer Application Insights abschalten?
Nicht direkt. Wir bieten keinen Schalter, mit dem Ihre Benutzer Application Insights abschalten können.

Sie können jedoch eine solche Funktion in Ihrer Anwendung implementieren. Alle SDKs enthalten eine API-Einstellung, mit der die Telemetrie-Erfassung deaktiviert wird. 

## <a name="data-sent-by-application-insights"></a>Von Application Insights gesendete Daten
Die SDKs sind je nach Plattform unterschiedlich, und es gibt verschiedene Komponenten, die Sie installieren können. (Weitere Informationen finden Sie unter [Application Insights – Übersicht][start].) Jede Komponente sendet unterschiedliche Daten.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klassen von in verschiedenen Szenarien gesendeten Daten

| Aktion | Gesammelte Datenklassen (siehe nächste Tabelle) |
| --- | --- |
| [Hinzufügen des Application Insights SDK zu Ihrem .NET-Webprojekt][greenbrown] |ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>Sitzung<br/>users |
| [Installieren des Statusmonitors auf IIS][redfield] |Abhängigkeiten<br/>ServerContext<br/>Inferred<br/>Perf counters |
| [Hinzufügen des Application Insights SDK zu einer Java-Web-App][java] |ServerContext<br/>Inferred<br/>Anforderung<br/>Sitzung<br/>users |
| [Hinzufügen des JavaScript SDK zur Webseite][client] |ClientContext <br/>Inferred<br/>Seite<br/>ClientPerf<br/>Ajax |
| [Definieren von Standardeigenschaften][apiproperties] |**Properties** für alle standardmäßigen und benutzerdefinierten Ereignisse |
| [Aufrufen von TrackMetric][api] |Numerische Werte<br/>**Properties** |
| [Aufrufen von Track*][api] |Ereignisname<br/>**Properties** |
| [Aufrufen von TrackException][api] |**Ausnahmen**<br/>Stapelabbild<br/>**Properties** |
| SDK kann keine Daten sammeln. Beispiel: <br/> – auf Leistungsindikatoren kann nicht zugegriffen werden<br/> – Ausnahme beim Telemetrieinitialisierer |SDK-Diagnose |

Weitere Informationen zu [SDKs für andere Plattformen][platforms] finden Sie in den entsprechenden Dokumenten.

#### <a name="the-classes-of-collected-data"></a>Die Klassen der gesammelten Daten

| Gesammelte Datenklasse | Umfasst (keine vollständige Liste) |
| --- | --- |
| **Properties** |**Alle Daten – bestimmt durch Code** |
| DeviceContext |ID, IP, Gebietsschema, Gerätemodell, Netzwerk, Netzwerktyp, OEM-Name, Bildschirmauflösung, Rolleninstanz, Rollenname, Gerätetyp |
| ClientContext |Betriebssystem, Gebietsschema, Sprache, Netzwerk, Fensterauflösung |
| Sitzung (Session) |Sitzungs-ID |
| ServerContext |Computername, Gebietsschema, Betriebssystem, Gerät, Benutzersitzung, Benutzerkontext, Vorgang |
| Inferred |Geolocation anhand IP-Adresse, Zeitstempel, Betriebssystem, Browser |
| metrics |Metrikname und -wert |
| Events |Ereignisname und -wert |
| PageViews |URL und Seitenname oder Bildschirmname |
| Client perf |URL-/Seitenname, Browserladezeit |
| Ajax |HTTP-Aufrufe von der Webseite an den Server |
| Requests |URL, Dauer, Antwortcode |
| Abhängigkeiten |Typ (SQL, HTTP,...), Verbindungszeichenfolge oder URI Sync/Async, Dauer, Erfolg, SQL-Anweisung (mit Statusmonitor) |
| **Ausnahmen** |Typ, **Meldung**, Aufrufliste, Quelldatei und Zeilennummer, Thread-ID |
| Crashes |Prozess-ID, übergeordnete Vorgangs-ID, Thread-ID des Absturzes, Anwendungs-Patch, ID, Build, Typ der Ausnahme, Adresse, Ursache; abgeblendete Symbole und Registerkarten, binäre Start- und Endadressen, binärer Name und Pfad, CPU-Typ |
| Trace |**Meldung** und Schweregrad |
| Perf counters |Prozessorzeit, verfügbarer Speicher, Anforderungsrate, Ausnahmerate, private Bytes verarbeiten, E/A-Rate, Anforderungsdauer, Länge der Anforderungswarteschlange |
| Verfügbarkeit |Webtestantwortcode, Dauer der einzelnen Testschritte, Testname, Zeitstempel, Erfolg, Antwortzeit, Testverzeichnis |
| SDK-Diagnose |Ablaufverfolgungsmeldung oder Ausnahme |

Sie können [einige der Daten durch Bearbeiten von ApplicationInsights.config abschalten][config].

> [!NOTE]
> Die Client-IP-Adresse wird zum Ableiten des geografischen Standorts verwendet. Die IP-Daten werden jedoch standardmäßig nicht mehr gespeichert, und es werden ausschließlich Nullen in das entsprechende Feld geschrieben. Wenn Sie mehr über den Umgang mit personenbezogenen Daten wissen möchten, empfehlen wir diesen [Artikel](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Wenn Sie IP-Adressen speichern müssen, können Sie dazu einen [Telemetrieinitialisierer](./../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) verwenden.

## <a name="credits"></a>Guthaben
Dieses Produkt enthält GeoLite2-Daten, die von MaxMind erstellt wurden und unter [https://www.maxmind.com](https://www.maxmind.com) verfügbar sind.



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md

