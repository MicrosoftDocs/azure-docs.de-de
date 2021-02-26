---
title: Überwachen von Azure Functions
description: Erfahren Sie, wie Sie Azure Application Insights mit Azure Functions zum Überwachen der Funktionsausführung verwenden.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 637f09c5ee52928631b965dfa6caea9368b44991
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550758"
---
# <a name="monitor-azure-functions"></a>Überwachen von Azure Functions

[Azure Functions](functions-overview.md) bietet von Haus aus Integration in [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) zum Überwachen von Funktionen. Dieser Artikel bietet eine Übersicht über die Überwachungsfunktionen von Azure für die Überwachung von Azure Functions.

Mit Application Insights werden Protokoll-, Leistungs- und Fehlerdaten erfasst. Der Dienst erkennt Leistungsanomalien automatisch und verfügt über leistungsstarke Analysetools, sodass Sie Probleme einfacher untersuchen und besser nachvollziehen können, wie Ihre Funktionen verwendet werden. Diese Tools unterstützen Sie bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit Ihrer Funktionen. Sie können Application Insights auch während der lokalen Entwicklung an Funktions-App-Projekten verwenden. Weitere Informationen finden Sie unter [Was ist Application Insights?](../azure-monitor/app/app-insights-overview.md).

Da die Instrumentierung von Application Insights in Azure Functions integriert ist, benötigen Sie einen gültigen Instrumentierungsschlüssel, um Ihre Funktions-App mit einer Application Insights-Ressource zu verbinden. Der Instrumentierungsschlüssel wird den Anwendungseinstellungen hinzugefügt, wenn Ihre Funktions-App-Ressource in Azure erstellt wird. Wenn Ihre Funktions-App noch nicht über diesen Schlüssel verfügt, können Sie [ihn manuell festlegen](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Application Insights – Preise und Limits

Sie können die Application Insights-Integration in Azure Functions kostenlos ausprobieren, indem Sie das Tageslimit für die kostenlose Verarbeitung von Daten nutzen.

Wenn Sie Application Insights in der Entwicklung aktivieren, kann dieser Grenzwert während der Tests erreicht werden. In Azure erhalten Sie Benachrichtigungen im Portal und per E-Mail, wenn Ihr Tageslimit beinahe erreicht ist. Wenn Sie diese Benachrichtigungen ignorieren und das Limit erreichen, werden neue Protokolle nicht in Application Insights-Abfragen angezeigt. Seien Sie sich des Grenzwerts bewusst, um unnötigen Zeitaufwand für die Problembehandlung zu vermeiden. Weitere Informationen finden Sie unter [Verwalten von Preisen und Datenvolumen in Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights verfügt über ein Feature zur [Stichprobenentnahme](../azure-monitor/app/sampling.md) als Schutz davor, dass bei Spitzenlast zu viele Telemetriedaten für erfolgte Vorgänge produziert werden. Sampling ist standardmäßig aktiviert. Wenn Ihnen Daten zu fehlen scheinen, müssen Sie möglicherweise nur die Sampling-Einstellungen an Ihr spezielles Überwachungsszenario anpassen. Weitere Informationen finden Sie unter [Konfigurieren des Samplings](configure-monitoring.md#configure-sampling).

Die vollständige Liste der Application Insights-Features, die für Ihre Funktions-App verfügbar sind, finden Sie unter [Unterstützte Features in Application Insights für Azure Functions](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="application-insights-integration"></a>Application Insights-Integration

In der Regel erstellen Sie eine Application Insights-Instanz, wenn Sie Ihre Funktions-App erstellen. In diesem Fall ist der für die Integration erforderliche Instrumentierungsschlüssel bereits als Anwendungseinstellung mit dem Namen *APPINSIGHTS_INSTRUMENTATIONKEY* festgelegt. Wenn aus irgendeinem Grund für Ihre Funktions-App kein Instrumentierungsschlüssel festgelegt ist, müssen Sie die [Application Insights-Integration aktivieren](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Sammeln von Telemetriedaten

Wenn die Application Insights-Integration aktiviert ist, werden Telemetriedaten an die verbundene Application Insights-Instanz gesendet. Diese Daten umfassen vom Functions-Host generierte Protokolle, Ablaufverfolgungen, die von Ihrem Funktionscode geschrieben wurden, und Leistungsdaten. 

>[!NOTE]
>Zusätzlich zu den Daten aus Ihren Funktionen und dem Functions-Host können Sie auch Daten aus dem [Functions-Skalierungscontroller](#scale-controller-logs) sammeln.   

### <a name="log-levels-and-categories"></a>Protokolliergrade und Kategorien

Wenn Sie Ablaufverfolgungen auf Grundlage des Anwendungscodes schreiben, sollten Sie den Ablaufverfolgungen eine Protokollebene zuweisen. Protokolliergrade bieten Ihnen die Möglichkeit, die Menge der Daten, die von ihren Ablaufverfolgungen gesammelt werden, zu begrenzen.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Weitere Informationen zu Protokolliergraden finden Sie unter [Konfigurieren von Protokollgraden](configure-monitoring.md#configure-log-levels).

Wenn Sie einer Kategorie protokollierte Elemente zuweisen, haben Sie mehr Kontrolle über die Telemetriedaten, die aus bestimmten Quellen in der Funktions-App generiert werden. Kategorien vereinfachen das Ausführen von Analysen der gesammelten Daten. Ablaufverfolgungen, die auf Grundlage Ihres Funktionscodes geschrieben wurden, werden einzelnen Kategorien basierend auf dem Funktionsnamen zugewiesen. Weitere Informationen zu Kategorien finden Sie unter [Konfigurieren von Kategorien](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Benutzerdefinierte Telemetriedaten

In [C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) und [JavaScript-](functions-reference-node.md#log-custom-telemetry)können Sie ein Application Insights SDK verwenden, um benutzerdefinierte Telemetriedaten zu schreiben.

### <a name="dependencies"></a>Abhängigkeiten

Ab Version 2.x von Functions sammelt die Runtime automatisch Daten zu Abhängigkeiten für Bindungen, die bestimmte Client-SDKs verwenden. Application Insights sammelt Daten über die folgenden Abhängigkeiten:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure Storage-Dienste (Blob, Queue und Table)

HTTP-Anforderungen und Datenbankaufrufe mithilfe von `SqlClient` werden ebenfalls erfasst. Eine umfassende Liste der Abhängigkeiten, die von Application Insights unterstützt werden, finden Sie unter [Automatisch nachverfolgte Abhängigkeiten](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights generiert eine _Anwendungsübersicht_ der gesammelten Abhängigkeitsdaten. Im Folgenden finden Sie ein Beispiel für eine Anwendungsübersicht einer HTTP-Triggerfunktion mit einer Queue Storage-Ausgabebindung.  

![Anwendungsübersicht mit Abhängigkeit](./media/functions-monitoring/app-map.png)

Abhängigkeiten werden mit `Information`-Grad geschrieben. Wenn Sie nach `Warning` oder einem höheren Grad filtern, werden keine Abhängigkeitsdaten angezeigt. Außerdem erfolgt die automatische Sammlung von Abhängigkeiten im Nichtbenutzerbereich. Um Abhängigkeitsdaten zu erfassen, stellen Sie sicher, dass der Grad mindestens auf `Information` außerhalb des Benutzerbereichs (`Function.<YOUR_FUNCTION_NAME>.User`) auf Ihrem Host festgelegt ist.

Zusätzlich zur automatischen Sammlung von Abhängigkeitsdaten können Sie auch eines der sprachspezifischen Application Insights-SDKs verwenden, um benutzerdefinierte Abhängigkeitsinformationen in die Protokolle zu schreiben. Ein Beispiel zum Schreiben von benutzerdefinierten Abhängigkeiten finden Sie in den folgenden sprachspezifischen Beispielen:

+ [Protokollieren von benutzerdefinierter Telemetrie in C#-Funktionen](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Protokollieren von benutzerdefinierter Telemetrie in JavaScript-Funktionen](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Schreiben in Protokolle 

Die Art und Weise, wie Sie in Protokolle schreiben und welche APIs verwendet werden, hängt von der Sprache des Funktions-App-Projekts ab.   
Weitere Informationen zum Schreiben von Protokollen aus ihren Funktionen finden Sie im Entwicklerhandbuch für Ihre Sprache.

+ [C# (.NET-Klassenbibliothek)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>Daten analysieren

Standardmäßig werden die von Ihrer Funktions-App gesammelten Daten in Application Insights gespeichert. Im [Azure-Portal](https://portal.azure.com) stellt Application Insights eine umfangreiche Sammlung von Visualisierungen für Ihre Telemetriedaten bereit. Sie können Drilldowns für Fehlerprotokolle, Abfrageereignisse und Metriken ausführen. Weitere Informationen (einschließlich grundlegender Beispiele für die Anzeige und Abfrage Ihrer erfassten Daten) finden Sie unter [Analysieren der Azure Functions-Telemetrie in Application Insights](analyze-telemetry-data.md). 

## <a name="streaming-logs"></a>Streamingprotokolle

Bei der Anwendungsentwicklung ist es häufig wünschenswert, nahezu in Echtzeit zu sehen, was bei der Ausführung in Azure in die Protokolle geschrieben wird.

Es gibt zwei Möglichkeiten, einen Datenstrom der Protokolldaten anzuzeigen, die bei den Ausführungen Ihrer Funktion generiert werden.

* **Integriertes Protokollstreaming**: Mithilfe der App Service-Plattform können Sie einen Datenstrom Ihrer Anwendungsprotokolldateien einsehen. Dieser Datenstrom entspricht der Ausgabe, die beim Debuggen Ihrer Funktionen bei der [lokalen Entwicklung](functions-develop-local.md) oder bei Verwenden der Registerkarte **Test** im Portal zu sehen ist. Alle protokollbasierten Informationen werden angezeigt. Weitere Informationen finden Sie unter [Streamen von Protokollen](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Diese Streamingmethode unterstützt nur eine einzelne Instanz und kann nicht mit einer App verwendet werden, die unter Linux in einem Verbrauchstarif ausgeführt wird.

* **Live Metrics Stream**: Wenn Ihre Funktions-App [mit Application Insights verbunden](configure-monitoring.md#enable-application-insights-integration) ist, können Sie im Azure-Portal mithilfe von [Live Metrics Stream](../azure-monitor/app/live-stream.md) Protokolldaten und andere Metriken nahezu in Echtzeit anzeigen. Verwenden Sie diese Methode, wenn Sie Funktionen überwachen, die auf mehreren Instanzen oder unter Linux in einem Verbrauchstarif ausgeführt werden. Diese Methode verwendet [Stichprobendaten](configure-monitoring.md#configure-sampling).

Protokolldatenströme können sowohl im Portal als auch in den meisten lokalen Entwicklungsumgebungen eingesehen werden. Informationen zum Aktivieren von Protokolldatenströmen finden Sie unter [Aktivieren von Datenströmen für Ausführungsprotokolle in Azure Functions](streaming-logs.md).

## <a name="diagnostic-logs"></a>Diagnoseprotokolle

_Dieses Feature befindet sich in der Vorschauphase._ 

Mit Application Insights können Sie Telemetriedaten für die langfristige Speicherung oder andere Analysis Services exportieren.  

Da Functions auch in Azure Monitor integriert ist, können Sie auch Diagnoseeinstellungen verwenden, um Telemetriedaten an verschiedene Ziele zu senden, einschließlich an Azure Monitor-Protokolle. Weitere Informationen finden Sie unter [Überwachen von Azure Functions mit Azure Monitor-Protokollen](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Skalieren von Controllerprotokollen

_Dieses Feature befindet sich in der Vorschauphase._ 

Der [Azure Functions-Skalierungscontroller](./event-driven-scaling.md#runtime-scaling) dient zum Überwachen von Instanzen des Azure Functions-Hosts, auf dem Ihre App ausgeführt wird. Dieser Controller entscheidet basierend auf der aktuellen Leistung über das Hinzufügen oder Entfernen von Instanzen. Der Skalierungscontroller kann Protokolle an Application Insights ausgeben, damit sie die Entscheidungen, die der Skalierungscontroller für Ihre Funktions-App trifft, besser nachvollziehen können. Sie können die generierten Protokolle auch im Blobspeicher speichern, damit Sie von einem anderen Dienst analysiert werden können. 

Um dieses Feature zu aktivieren, fügen Sie den Einstellungen ihrer Funktions-App eine Anwendungseinstellung mit dem Namen `SCALE_CONTROLLER_LOGGING_ENABLED` hinzu. Weitere Informationen finden Sie unter [Konfigurieren der Skalierung von Controllerprotokollen](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Melden von Problemen

[Erstellen Sie auf GitHub einen Problemeintrag](https://github.com/Azure/Azure-Functions/issues/new), um ein Problem mit der Application Insights-Integration in Functions zu melden oder einen Vorschlag oder eine Anfrage zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core-Protokollierung](/aspnet/core/fundamentals/logging/)
