---
title: Analysieren von Trends in Visual Studio | Microsoft Docs
description: Es wird beschrieben, wie Sie Trends in den Application Insights-Telemetriedaten in Visual Studio analysieren, visualisieren und untersuchen.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 2b08dfd87910cbb9f23f6b108a970d160612e1a7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255887"
---
# <a name="analyzing-trends-in-visual-studio"></a>Analysieren von Trends in Visual Studio
Mit dem Application Insights-Tool „Trends“ wird visualisiert, wie sich die wichtigen Telemetrieereignisse Ihrer Webanwendung im Laufe der Zeit ändern, damit Sie Probleme und Anomalien schnell identifizieren können. Durch die Angabe von Links zu ausführlicheren Diagnoseinformationen kann das Trends-Tool Sie in Bezug auf das Verbessern der App-Leistung, das Ermitteln der Ursachen von Ausnahmen und das Erlangen von Einblicken in benutzerdefinierte Ereignisse unterstützen.

![Beispiel für Trends-Fenster](./media/visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Konfigurieren der Web-App für Application Insights

Falls noch nicht geschehen, können Sie [Ihre Web-App für Application Insights konfigurieren](../../azure-monitor/app/app-insights-overview.md). So wird ermöglicht, dass Telemetriedaten an das Application Insights-Portal gesendet werden. Mit dem Tool Trends können die Telemetriedaten darin dann gelesen werden.

Application Insights-Trends ist als Teil von Visual Studio 2015 Update 3 und höher verfügbar.

## <a name="open-application-insights-trends"></a>Öffnen von Application Insights-Trends
Gehen Sie wie folgt vor, um das Application Insights-Trends-Fenster zu öffnen:

* Wählen Sie über die Application Insights-Symbolleistenschaltfläche die Option **Telemetrietrends untersuchen** oder:
* Wählen Sie im Kontextmenü des Projekts die Option **Application Insights > Telemetrietrends untersuchen** oder:
* Wählen Sie in der Visual Studio-Menüleiste **Ansicht > Andere Fenster > Application Insights-Trends**.

Ggf. wird eine Eingabeaufforderung zum Auswählen einer Ressource angezeigt. Klicken Sie auf **Ressource auswählen**, melden Sie sich mit einem Azure-Abonnement an, und wählen Sie dann eine Application Insights-Ressource in der Liste aus, für die Sie Telemetrietrends analysieren möchten.

## <a name="choose-a-trend-analysis"></a>Auswählen einer Trendanalyse
![Menü mit häufigen Arten von Trendanalysen](./media/visual-studio-trends/app-insights-trends-1-750.png)

Wählen Sie zunächst eine von fünf gängigen Trendanalysen aus, bei denen jeweils die Daten der letzten 24 Stunden analysiert werden:

* **Untersuchen Sie Leistungsprobleme mit Ihren Serveranforderungen** : Anforderungen an Ihren Dienst, gruppiert nach Antwortzeiten
* **Analysieren Sie Fehler in Ihren Serveranforderungen** : Anforderungen an Ihren Dienst, gruppiert nach HTTP-Antwortcode
* **Untersuchen Sie die Ausnahmen in Ihrer Anwendung** : Ausnahmen aus Ihrem Dienst, gruppiert nach Ausnahmetyp
* **Überprüfen Sie die Leistung der Abhängigkeiten Ihrer Anwendung** : Von Ihrem Dienst aufgerufene Dienste, gruppiert nach Antwortzeiten
* **Untersuchen Sie Ihre benutzerdefinierten Ereignisse** : Eingerichtete benutzerdefinierte Ereignisse für Ihren Dienst, gruppiert nach Ereignistyp

Diese vorgefertigten Analysen sind später über die Schaltfläche **Zeigt gängige Typen der Telemetrieanalyse an** oben links im Trends-Fenster verfügbar.

## <a name="visualize-trends-in-your-application"></a>Visualisieren von Trends in der Anwendung
Application Insights Trends erstellt eine Zeitreihenvisualisierung für die Telemetriedaten der App. Für jede Zeitreihenvisualisierung wird eine Art von Telemetriedaten angezeigt, gruppiert nach einer Eigenschaft dieser Telemetriedaten für einen bestimmten Zeitbereich. Beispielsweise kann es sein, dass Sie Serveranforderungen gruppiert nach dem Ursprungsland für die letzten 24 Stunden anzeigen möchten. In diesem Beispiel stellte jede Blase der Visualisierung eine Anzahl von Serveranforderungen für ein Land bzw. eine Region und eine Stunde dar.

Verwenden Sie die Steuerelemente am oberen Rand des Fensters, um anzupassen, welche Arten von Telemetriedaten angezeigt werden. Wählen Sie zuerst die Telemetrietypen aus, an denen Sie interessiert sind:

* **Telemetrietyp**: Serveranforderungen, Ausnahmen, Abhängigkeiten oder benutzerdefinierte Ereignisse
* **Zeitbereich** : Zwischen den letzten 30 Minuten und den letzten drei Tagen
* **Gruppieren nach** : Ausnahmetyp, Problem-ID, Land/Region usw.

Klicken Sie anschließend auf **Telemetriedaten analysieren** , um die Abfrage auszuführen.

So navigieren Sie zwischen Blasen in der Visualisierung

* Klicken Sie auf eine Blase, um sie auszuwählen. Die Filter am unteren Rand des Fensters werden aktualisiert, und es werden nur die Ereignisse zusammengefasst, die während eines bestimmten Zeitraums eingetreten sind.
* Doppelklicken Sie auf eine Blase, um zum Suchtool zu navigieren und alle Telemetrieereignisse anzuzeigen, die während des Zeitraums eingetreten sind.
* Führen Sie STRG+Klicken auf einer Blase aus, um die Auswahl in der Visualisierung aufzuheben.

> [!TIP]
> Die Tools Trends und Suche arbeiten zusammen, damit Sie die Ursachen in Ihrem Dienst unter den Tausenden von Telemetrieereignissen ermitteln können. Falls Ihre Kunden an einem Nachmittag beispielsweise bemerken, dass die Reaktionsfähigkeit Ihrer App abnimmt, beginnen Sie mit der Verwendung von Trends. Analysieren Sie die Anforderungen an den Dienst im Verlauf der letzten Stunden, gruppiert nach der Antwortzeit. Achten Sie darauf, ob eine ungewöhnlich große Gruppe mit langsamen Anforderungen zu erkennen ist. Doppelklicken Sie dann auf diese Blase, um auf das Suchtool zuzugreifen, in dem eine nach diesen Anforderungsereignissen gefilterte Liste angezeigt wird. Mit dem Suchtool können Sie den Inhalt dieser Anforderungen untersuchen und zum jeweiligen Code navigieren, um das Problem zu beheben.
> 
> 

## <a name="filter"></a>Filter
Ermitteln Sie spezifischere Trends mit den Filtersteuerelementen am unteren Rand des Fensters. Klicken Sie auf den Namen eines Filters, um ihn anzuwenden. Sie können schnell zwischen verschiedenen Filtern wechseln, um Trends zu ermitteln, die ggf. in einer bestimmten Dimension Ihrer Telemetriedaten versteckt sind. Wenn Sie einen Filter in einer Dimension anwenden, z.B. „Ausnahmetyp“, bleiben die Filter in anderen Dimensionen auch dann zugänglich, wenn sie scheinbar abgeblendet sind. Klicken Sie erneut auf einen Filter, um die Anwendung aufzuheben. Klicken Sie bei gedrückter STRG-Taste, um mehrere Filter in derselben Dimension auszuwählen.

![Trendfilter](./media/visual-studio-trends/TrendsFiltering-750.png)

Was passiert, wenn Sie mehrere Filter anwenden möchten? 

1. Wenden Sie den ersten Filter an. 
2. Klicken Sie für den Namen der Dimension Ihres ersten Filters auf die Schaltfläche **Wendet die ausgewählten Filter an und führt die Abfrage erneut aus** . Die Telemetriedaten werden dann nur für die Ereignisse erneut abgefragt, die mit ersten Filter übereinstimmen. 
3. Wenden Sie einen zweiten Filter an. 
4. Wiederholen Sie den Vorgang, um Trends in bestimmten Teilmengen Ihrer Telemetriedaten zu finden. Beispiel: Serveranforderungen, die den Namen „GET Home/Index“ haben *und* aus Deutschland stammen *und* einen Antwortcode von 500 erhalten haben. 

Um einen dieser Filter aufzuheben, klicken Sie für die Dimension auf die Schaltfläche **Entfernt die ausgewählten Filter und führt die Abfrage erneut aus** .

![Mehrere Filter](./media/visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Suchen nach Anomalien
Mit dem Tool Trends können Sie Blasen mit Ereignissen hervorheben, die im Vergleich mit anderen Blasen in derselben Zeitreihe Anomalien aufweisen. Wählen Sie in der Dropdownliste „Ansichtstyp“ die Option **Anzahl im Zeitrahmen (Anomalien hervorheben)** oder **Prozentwerte im Zeitrahmen (Anomalien hervorheben)** . Rote Blasen weisen auf Anomalien hin. Anomalien sind als Blasen mit Anzahlen/Prozentwerten definiert, die das 2,1-fache der Standardabweichung von Anzahlen/Prozentwerten der letzten beiden Zeiträume überschreiten (48 Stunden, wenn Sie die letzten 24 Stunden betrachten, usw.).

![Farbige Punkte als Hinweis auf Anomalien](./media/visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> Die Hervorhebung von Anomalien ist besonders nützlich zum Ermitteln von Ausreißern in Zeitreihen von kleinen Blasen, die andernfalls scheinbar die gleiche Größe aufweisen.  
> 
> 

## <a name="next"></a>Nächste Schritte
|  |  |
| --- | --- |
| **[Arbeiten mit Application Insights in Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Durchsuchen Sie die Telemetriedaten, zeigen Sie Daten in CodeLens an, und konfigurieren Sie Application Insights. Alles in Visual Studio. |![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights > Durchsuchen“ aus.](./media/visual-studio-trends/34.png) |
| **[Hinzufügen weiterer Daten](../../azure-monitor/app/asp-net-more.md)**<br/>Überwachen Sie Nutzung, Verfügbarkeit, Abhängigkeiten und Ausnahmen. Integrieren Sie Ablaufverfolgungen aus Protokollierungsframeworks. Schreiben Sie benutzerdefinierte Telemetriedaten. |![Visual Studio](./media/visual-studio-trends/64.png) |
| **[Navigation und Dashboards im Application Insights-Portal](../../azure-monitor/app/overview-dashboard.md)**<br/>Enthält Informationen zu Dashboards, leistungsstarken Diagnose- und Analysetools, Warnungen und zum Export von Telemetriedaten sowie eine Livekarte der Abhängigkeiten Ihrer Anwendung. |![Visual Studio](./media/visual-studio-trends/62.png) |

