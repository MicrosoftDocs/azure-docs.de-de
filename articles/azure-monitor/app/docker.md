---
title: Überwachen von Docker-Anwendungen in Azure Application Insights | Microsoft Docs
description: In Application Insights können Docker-Leistungsindikatoren, -Ereignisse und -Ausnahmen zusammen mit der Telemetrie von in Containern ausgeführten Apps angezeigt werden.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 115e2d6b041ecc3f38a2a6438d90777da9660221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62098030"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Überwachen von Docker-Anwendungen in Application Insights (verwaltet)

> [!NOTE]
> Diese Lösung ist veraltet. Weitere Informationen zu unseren aktuellen Investitionen in die Containerüberwachung finden Sie unter [Azure Monitor für Container (Vorschau) – Übersicht](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

Lebenszyklusereignisse und Leistungsindikatoren aus [Docker](https://www.docker.com/) -Containern können in Application Insights in Diagrammen dargestellt werden. Wenn Sie das [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) -Image in einem Container auf Ihrem Host installieren, werden Leistungsindikatoren für den Host sowie für die anderen Images angezeigt.

Mit Docker verteilen Sie Ihre Apps zusammen mit allen Abhängigkeiten auf schlanke Container. Diese lassen sich auf allen Hostcomputern ausführen, auf denen eine Docker-Engine ausgeführt wird.

Wenn Sie das [Application Insights-Image](https://hub.docker.com/r/microsoft/applicationinsights/) auf Ihrem Docker-Host ausführen, ergeben sich für Sie folgende Vorteile:

* Lebenszyklustelemetrie für alle Container, die auf dem Host ausgeführt werden: Start, Ende usw.
* Leistungsindikatoren für alle Container. CPU, Arbeitsspeicher, Netzwerkauslastung und vieles mehr.
* Wenn Sie das [Application Insights-SDK](../../azure-monitor/app/java-get-started.md) in den in den Containern ausgeführten Apps installiert haben, weisen alle Telemetriedaten dieser Apps zusätzliche Eigenschaften auf, durch die der Container und der Hostcomputer identifiziert werden. Wenn beispielsweise Instanzen einer App auf mehreren Hosts ausgeführt werden, können Sie so die App-Telemetrie problemlos nach dem Host filtern.

## <a name="set-up-your-application-insights-resource"></a>Einrichten der Application Insights-Ressource

1. Melden Sie sich im [Microsoft Azure-Portal](https://azure.com) an, und öffnen Sie die Application Insights-Ressource für Ihre App, oder [erstellen Sie eine neue Ressource](../../azure-monitor/app/create-new-resource.md ). 
   
    *Welche Ressource soll ich verwenden?* Wenn die Apps, die Sie auf Ihrem Host ausführen, von einer anderen Person entwickelt wurden, müssen Sie [eine neue Application Insights-Ressource erstellen](../../azure-monitor/app/create-new-resource.md ). In dieser Ressource zeigen Sie die Telemetriedaten an und analysieren sie. (Wählen Sie „Allgemein“ als App-Typ aus.)
   
    Wenn Sie jedoch die Apps selbst entwickelt haben, haben Sie hoffentlich jeder App das [Application Insights-SDK hinzugefügt](../../azure-monitor/app/java-get-started.md) . Wenn es sich bei den Apps um Komponenten einer einzelnen Geschäftsanwendung handelt, können Sie alle Apps so konfigurieren, dass die Telemetriedaten an eine Ressource gesendet werden, und dann mithilfe dieser Ressource die Lebenszyklus- und Leistungsdaten von Docker anzeigen. 
   
    Im dritten möglichen Szenario haben Sie die meisten Apps selbst entwickelt, verwenden aber separate Ressourcen, um die zugehörigen Telemetriedaten anzuzeigen. In diesem Fall erstellen Sie wahrscheinlich auch eine separate Ressource für die Docker-Daten.

2. Klicken Sie auf die Dropdownliste **Essentials** , und kopieren Sie den Instrumentierungsschlüssel. Sie benötigen ihn, um dem SDK mitzuteilen, wohin die Telemetrie gesendet werden sollen.

Lassen Sie dieses Browserfenster geöffnet, Sie werden es gleich wieder brauchen, um Ihre Telemetrie anzuzeigen.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Ausführen der Application Insights-Überwachung auf dem Host

Nachdem nun ein Ort zum Anzeigen der Telemetrie vorhanden ist, können Sie die in Containern ausgeführte App einrichten, die die entsprechenden Daten sammelt und sendet.

1. Stellen Sie eine Verbindung mit Ihrem Docker-Host her.
2. Bearbeiten Sie den Instrumentierungsschlüssel im folgenden Befehl, und führen Sie ihn dann aus:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Pro Docker-Host ist nur ein Application Insights-Image erforderlich. Wenn Ihre Anwendung auf mehreren Docker-Hosts bereitgestellt wird, wiederholen Sie den Befehl auf jedem Host.

## <a name="update-your-app"></a>Aktualisieren Ihrer App
Wenn Ihre Anwendung mit dem [Application Insights-SDK für Java](../../azure-monitor/app/java-get-started.md) instrumentiert ist, fügen Sie in der Datei „ApplicationInsights.xml“ in Ihrem Projekt unter dem Element `<TelemetryInitializers>` die folgende Zeile ein:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Dadurch werden jedem über die App gesendeten Telemetrieelement Docker-Informationen hinzugefügt, z. B. die Container- und Host-ID.

## <a name="view-your-telemetry"></a>Anzeigen der Telemetrie
Kehren Sie zu Ihrer Application Insights-Ressource im Azure-Portal zurück.

Klicken Sie durch die Docker-Kachel.

Dort werden Sie gleich die von der Docker-App eingehenden Daten sehen, insbesondere wenn andere Container in Ihrer Docker-Engine ausgeführt werden.

### <a name="docker-container-events"></a>Ereignisse im Docker-Container
![Beispiel](./media/docker/13.png)

Um einzelne Ereignisse zu untersuchen, klicken Sie auf [Suche](../../azure-monitor/app/diagnostic-search.md). Suchen Sie die gewünschten Ereignisse, und filtern Sie sie bei Bedarf. Klicken Sie auf ein beliebiges Ereignis, um weitere Details anzuzeigen.

### <a name="exceptions-by-container-name"></a>Ausnahmen nach Containername
![Beispiel](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Hinzugefügter Docker-Kontext zur App-Telemetrie
Von der mit dem AI SDK instrumentierten Anwendung gesendete Anforderungstelemetriedaten werden mit Docker-Kontextinformationen angereichert.

## <a name="q--a"></a>Fragen und Antworten
*Was bietet mir Application Insights, das Docker nicht leisten kann?*

* Eine detaillierte Aufschlüsselung der Leistungsindikatoren nach Container und Image.
* Eine Zusammenführung von Container- und App-Daten in einem Dashboard.
* [Export der Telemetrie](export-telemetry.md) in eine Datenbank, in Power-BI oder ein anderes Dashboard, um die Daten eingehender zu analysieren.

*Wie erhalte ich Telemetrie aus der App selbst?*

* Installieren Sie das Application Insights SDK in der App. So gehen Sie vor bei [Java-Web-Apps](../../azure-monitor/app/java-get-started.md) und [Windows-Web-Apps](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nächste Schritte

* [Application Insights für Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights für Node.js](../../azure-monitor/app/nodejs.md)
* [Application Insights für ASP.NET](../../azure-monitor/app/asp-net.md)
