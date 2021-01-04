---
title: Problembehandlung von Leistungsbeeinträchtigung
description: Erfahren Sie, wie Sie Probleme mit langsamer App-Leistung in Azure App Service beheben und durch Überwachen des App-Verhaltens und Erfassen von Daten das Problem lösen.
tags: top-support-issue
keywords: Web-App-Leistung, langsame App, App langsam
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: cf8b0e1fda03a74d30ec77c911d705bf12cf0126
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763807"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Problembehandlung bei niedriger App-Leistung in Azure App Service
In diesem Artikel erfahren Sie, wie Sie App-Leistungsprobleme in [Azure App Service](./overview.md) beheben.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.

## <a name="symptom"></a>Symptom
Beim Navigieren in der App werden die Seiten langsam geladen, und manchmal tritt beim Laden der Seiten ein Timeout auf.

## <a name="cause"></a>Ursache
Diese Fehler sind häufig auf Probleme auf der Anwendungsebene zurückzuführen, z. B.:

* Netzwerkanforderungen, die sehr lange dauern
* Ineffiziente Anwendungscodes oder Datenbankabfragen
* Eine Anwendung mit hoher Speicher-/CPU-Auslastung
* Eine Anwendung, die aufgrund einer Ausnahme abstürzt

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Die Problembehandlung lässt sich in diesem Fall in drei unterschiedliche Aufgaben unterteilen, die nacheinander ausgeführt werden:

1. [Beobachten und Überwachen des Anwendungsverhaltens](#observe)
2. [Sammeln von Daten](#collect)
3. [Minimieren der Auswirkungen des Problems](#mitigate)

[App Service](overview.md) bietet Ihnen bei jedem Schritt verschiedene Optionen.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Beobachten und Überwachen des Anwendungsverhaltens
#### <a name="track-service-health"></a>Nachverfolgen der Dienstintegrität
Microsoft Azure informiert jeweils aktuell über Dienstunterbrechungen oder Leistungsbeeinträchtigungen. Sie können die Integrität des Diensts im [Azure-Portal](https://portal.azure.com/) nachverfolgen. Weitere Informationen finden Sie unter [Nachverfolgen der Dienstintegrität](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Überwachen Ihrer App
Durch das Überwachen der Web-App können Sie herausfinden, ob in Ihrer Anwendung Probleme vorliegen. Klicken Sie auf dem Blatt Ihrer App auf die Kachel **Anforderungen und Fehler**. Auf dem Blatt **Metrik** werden alle Metriken angezeigt, die Sie hinzufügen können.

Sie können beispielsweise die folgenden Metriken für Ihre App überwachen:

* Durchschnittlicher Arbeitssatz für Arbeitsspeicher
* Durchschnittliche Reaktionszeit
* CPU-Zeit
* Arbeitssatz für Arbeitsspeicher
* Requests

![Überwachen der App-Leistung](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Weitere Informationen finden Sie unter

* [Überwachen von Apps in Azure App Service](web-sites-monitor.md)
* [Empfangen von Warnbenachrichtigungen](../azure-monitor/platform/alerts-overview.md)

#### <a name="monitor-web-endpoint-status"></a>Überwachen des Web-Endpunktstatus
Wenn Sie Ihre App im Tarif **Standard** ausführen, können Sie mit App Service zwei Endpunkte von drei geografischen Standorten aus überwachen.

Endpunktüberwachung konfiguriert Webtests von geografisch verteilten Standorten zum Testen der Reaktionszeit und der Verfügbarkeit von Web-URLs. Der Test führt einen HTTP GET-Aufruf für die Web-URL durch, um die Reaktionszeit und Verfügbarkeit der einzelnen Standorte zu ermitteln. Jeder konfigurierte Standort führt alle fünf Minuten einen Test aus.

Die Verfügbarkeit wird mit HTTP-Antwortcodes überwacht, und die Reaktionszeit wird in Millisekunden gemessen. Der Überwachungstest schlägt fehl, wenn der HTTP-Antwortcode größer als oder gleich 400 ist und die Antwort länger als 30 Sekunden benötigt. Der Endpunkt wird als verfügbar betrachtet, wenn die Überwachungstests von allen angegebenen Standorten aus erfolgreich waren.

Informationen zur Einrichtungen finden Sie unter [Gewusst wie: Überwachen von Apps in Azure App Service](web-sites-monitor.md).

Ein Video zur Endgeräteüberwachung finden Sie unter [Pflegen von Azure-Websites plus Endgeräteüberwachung - mit Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) .

#### <a name="application-performance-monitoring-using-extensions"></a>Überwachung der Anwendungsleistung mithilfe von Erweiterungen
Sie können die Anwendungsleistung auch mit einer *Websiteerweiterung* überwachen.

Jede App Service-App bietet einen erweiterbaren Verwaltungsendpunkt, mit dem Sie eine Reihe leistungsstarker Tools verwenden können, die als Websiteerweiterungen bereitgestellt werden. Zu den Erweiterungen zählen Folgende: 

- Quellcode-Editoren wie [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Verwaltungstools für verbundene Ressourcen wie eine MySQL-Datenbank, die mit einer App verbunden ist.

Ebenfalls zur Verfügung steht [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), eine Websiteerweiterung für die Leistungsüberwachung. Um Application Insights zu verwenden, erstellen Sie ihren Code mit einem SDK neu. Sie können auch eine Erweiterung installieren, die Zugriff auf zusätzliche Daten bietet. Mit dem SDK können Sie Code schreiben, um die Auslastung und Leistung Ihrer Anwendung genauer zu überwachen. Weitere Informationen finden Sie unter [Leistungsüberwachung in Webanwendungen](../azure-monitor/app/app-insights-overview.md).

<a name="collect"></a>

### <a name="2-collect-data"></a>2. Sammeln von Daten
App Service bietet Diagnosefunktionen zum Protokollieren von Informationen sowohl über den Webserver als auch über die Webanwendung. Die Informationen sind logisch in Webserverdiagnose und Anwendungsdiagnose unterteilt.

#### <a name="enable-web-server-diagnostics"></a>Aktivieren der Webserverdiagnose
Sie können die folgenden Protokollarten aktivieren oder deaktivieren:

* **Detaillierte Fehlerprotokollierung** - Detaillierte Fehlerinformationen für HTTP-Statuscodes, die auf einen Fehler hinweisen (Statuscode 400 oder höher). Diese können Informationen enthalten, mit deren Hilfe sich bestimmen lässt, warum der Server den Fehlercode zurückgegeben hat.
* **Ablaufverfolgung von Anforderungsfehlern** - Detaillierte Informationen zu fehlgeschlagenen Anforderungen, einschließlich der Verfolgung von IIS-Komponenten, die zur Verarbeitung der Anforderung verwendet wurden, sowie die in jeder Komponente benötigte Zeit. Dies kann hilfreich sein, wenn Sie versuchen, die Leistung von Apps zu verbessern oder die Ursache eines bestimmten HTTP-Fehlers zu ermitteln.
* **Webserverprotokollierung** : Informationen zu HTTP-Transaktionen im erweiterten W3C-Protokolldateiformat. Dies ist hilfreich, wenn Sie allgemeine App-Kennzahlen ermitteln möchten (etwa die Anzahl der verarbeiteten Anfragen oder die Anzahl der Anforderungen von einer bestimmten IP-Adresse).

#### <a name="enable-application-diagnostics"></a>Aktivieren der Anwendungsdiagnose
Es stehen verschiedene Optionen zur Verfügung: Sie können Daten zur Anwendungsleistung von App Service sammeln, Ihre Anwendung live über Visual Studio profilen oder den Anwendungscode ändern, um weitere Informationen und Ablaufverfolgungen zu protokollieren. Sie können die Optionen abhängig von dem Umfang des Zugriffs, der Ihnen für die Anwendung zur Verfügung steht, und den Erkenntnissen, die Sie durch die Überwachungstools gewonnen haben, auswählen.

##### <a name="use-application-insights-profiler"></a>Verwenden von Application Insights Profiler
Durch die Aktivierung von Application Insights Profiler können Sie mit der Erfassung ausführlicher Leistungsablaufverfolgungen beginnen. Wenn Sie Probleme untersuchen müssen, die in der Vergangenheit aufgetreten sind, können Sie bis zu fünf Tage rückwirkend auf Ablaufverfolgungen zugreifen. Sie können diese Option auswählen, solange Sie im Azure-Portal Zugriff auf die Application Insights-Ressource der App haben.

Application Insights Profiler bietet Statistiken zur Antwortzeit aller Webaufrufe und Ablaufverfolgungen, in denen angegeben ist, welche Codezeile die langsamen Antwortzeiten verursacht hat. Mitunter arbeitet die App Service-App langsam, da der Code nicht effektiv geschrieben wurde. Beispiele hierfür sind sequenzieller Code, der parallel ausgeführt werden kann, und unerwünschte Datenbanksperrkonflikte. Durch das Beseitigen dieser Engpässe im Code lässt sich die Leistung der App steigern, die aber ohne das Einrichten aufwendiger Ablaufverfolgungen und Protokolle schwer zu finden sind. Durch die von Application Insights Profiler gesammelten Ablaufverfolgungen können die Codezeilen identifiziert werden, die zur Verlangsamung der Anwendung führen. So kann dieses Problem in Bezug auf App Service-Apps behoben werden.

 Weitere Informationen finden Sie unter [Profilerstellung für Live-Azure App Service-Apps mit Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Verwenden von Remoteprofilerstellung
In Azure App Service kann eine Remoteprofilerstellung von Web-Apps, API-Apps, mobilen Back Ends und WebJobs ausgeführt werden. Wählen Sie diese Option, wenn Sie Zugriff auf die App-Ressource haben und wissen, wie das Problem reproduziert werden kann, oder wenn Sie das genaue Zeitintervall kennen, in dem das Leistungsproblem auftritt.

Die Remoteprofilerstellung ist nützlich, wenn die CPU-Auslastung des Prozesses hoch ist und der Prozess langsamer als erwartet ausgeführt wird oder wenn die Latenz von HTTP-Anforderungen außergewöhnlich hoch ist. In diesem Fall können Sie dann eine Remoteprofilerstellung des Prozesses ausführen und die CPU-Samplingaufruflisten abrufen, um die Prozessaktivität sowie die langsamsten Pfade im Code zu analysieren.

Weitere Informationen finden Sie unter [Unterstützung für Remoteprofilerstellung in Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Manuelles Einrichten von Diagnoseablaufverfolgungen
Wenn Sie Zugriff auf den Quellcode der Webanwendung haben, können Sie mit der Option „Anwendungsdiagnose“ die von einer Webanwendung erzeugten Informationen erfassen. ASP.NET-Anwendungen können die Klasse `System.Diagnostics.Trace` verwenden, um Informationen im Anwendungsdiagnoseprotokoll aufzuzeichnen. Allerdings müssen Sie den Code ändern und die Anwendung erneut bereitstellen. Diese Methode wird empfohlen, wenn Ihre App in einer Testumgebung ausgeführt wird.

Ausführlichen Anweisungen zum Konfigurieren der Anwendung für die Protokollierung finden Sie unter [Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Verwenden des Diagnosetools
App Service stellt eine intelligente und interaktive Komponente bereit, mit der Sie eine Problembehandlung für Ihre App durchführen können, ohne dass eine Konfiguration erforderlich ist. Wenn bei Ihrer App Probleme auftreten, stellt das Diagnosetool Informationen zum Fehler bereit, damit Sie die richtigen Informationen finden, um das Problem einfacher und schneller behandeln und lösen zu können.

Navigieren Sie für den Zugriff auf die App Service-Diagnose zu Ihrer App Service-App oder App Service-Umgebung im [Azure-Portal](https://portal.azure.com). Klicken Sie im linken Navigationsbereich auf **Diagnose und Problembehandlung**.

#### <a name="use-the-kudu-debug-console"></a>Verwenden der Kudu-Debugkonsole
App Service umfasst eine Debugkonsole zum Debuggen, Untersuchen und Hochladen von Dateien sowie JSON-Endpunkte zum Abrufen von Informationen über Ihre Umgebung. Diese Konsole wird als *Kudu-Konsole* oder *SCM-Dashboard* für Ihre App bezeichnet.

Sie können über den Link **https://&lt;Name_Ihrer_App>.scm.azurewebsites.net/** auf das Dashboard zugreifen.

Die Kudu-Konsole bietet z. B. folgende Funktionen:

* Umgebungseinstellungen für Ihre Anwendung
* Protokolldatenstrom
* Speicherabbild zu Diagnosezwecken
* Debugkonsole zum Ausführen von PowerShell-Cmdlets und grundlegenden DOS-Befehlen.

Eine weitere nützliche Funktion von Kudu ist die Möglichkeit, mithilfe von Kudu und dem SysInternals-Tool „Procdump“ Speicherabbilddateien zu erstellen, wenn Ihre Anwendung Ausnahmen (erste Chance) auslöst. Bei diesen Speicherabbilddateien handelt es sich um Momentaufnahmen des Prozesses, die beim Beheben komplizierterer Probleme mit Ihrer App hilfreich sein können.

Weitere Informationen zu den verfügbaren Funktionen in Kudu finden Sie unter [Tools für Azure DevOps, die Sie kennen sollten](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. Minimieren der Auswirkungen des Problems
#### <a name="scale-the-app"></a>Skalieren der App
In Azure App Service können Sie zum Steigern der Leistung und des Durchsatzes die Skalierung anpassen, mit der Sie Ihre Anwendung ausführen. Für das zentrale Hochskalieren einer App sind zwei zueinander in Beziehung stehende Schritte erforderlich: Sie müssen für den App Service-Plan ein Upgrade auf einen höheren Tarif durchführen, und Sie müssen nach dem Tarifwechsel bestimmte Einstellungen konfigurieren.

Weitere Informationen zur Skalierung finden Sie unter [Skalieren einer App in Azure App Service](manage-scale-up.md).

Außerdem haben Sie die Möglichkeit, Ihre Anwendung auf mehreren Instanzen auszuführen. Das horizontale Hochskalieren erhöht nicht nur die Verarbeitungskapazität, sondern bietet auch ein gewisses Maß an Fehlertoleranz. Wenn der Prozess in einer Instanz ausfällt, können die anderen Instanzen weiterhin Anforderungen verarbeiten.

Sie können die Skalierung auf „Manuell“ oder „Automatisch“ festlegen.

#### <a name="use-autoheal"></a>Verwenden von „AutoHeal“
„AutoHeal“ startet den Arbeitsprozess für Ihre App basierend auf von Ihnen ausgewählten Einstellungen neu (z. B. Konfigurationsänderungen, Anforderungen, speicherbasierte Grenzwerte oder zum Ausführen einer Anforderung benötigte Zeit). In den meisten Fällen ist das Neustarten des Prozesses die schnellste Methode zum Beheben eines Problems. Sie können die App zwar immer direkt im Azure-Portal neu starten, „AutoHeal“ führt diesen Schritt jedoch automatisch für Sie aus. Sie müssen lediglich einige Trigger in der Stammdatei „web.config“ Ihrer App hinzufügen. Diese Einstellungen werden immer auf die gleiche Weise angewandt, selbst wenn es sich bei Ihrer Anwendung nicht um eine .NET-App handelt.

Weitere Informationen finden Sie unter [Automatische Reparatur von Azure-Websites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Neustarten der App
Ein Neustart ist oft die einfachste Methode zum Beheben einmaliger Probleme. Im [Azure-Portal](https://portal.azure.com/) stehen auf dem Blatt Ihrer App Optionen zum Beenden oder Neustarten der App zur Verfügung.

 ![Neustart der App zum Beheben von Leistungsproblemen](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Sie können Ihre App auch mit Azure PowerShell verwalten. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](../azure-resource-manager/management/manage-resources-powershell.md).