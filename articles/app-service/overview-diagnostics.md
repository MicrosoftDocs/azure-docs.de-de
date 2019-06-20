---
title: Übersicht über die Azure App Service-Diagnose | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der App Service-Diagnose Probleme bei einer App beheben können.
keywords: App Service, Azure App Service, Diagnose, Unterstützung, Web-App, Problembehandlung, Selbsthilfe
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 3e304df51133d53adad50e672249bde6c9960712
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539739"
---
# <a name="azure-app-service-diagnostics-overview"></a>Übersicht über die Azure App Service-Diagnose

Wenn Sie eine Webanwendung ausführen, möchten Sie auf möglicherweise auftretende Probleme vorbereitet sein – von Fehlern mit dem Code 500 bis zu Benachrichtigungen durch Benutzer, dass die Website ausgefallen ist. Die App Service-Diagnose ist eine intelligente und interaktive Komponente, mit der Sie Probleme bei Ihrer App beheben können, ohne dass eine Konfiguration erforderlich ist. Wenn bei Ihrer App Probleme auftreten, zeigt die App Service-Diagnose den Fehler auf, damit Sie die richtigen Informationen finden, um das Problem einfacher und schneller behandeln und lösen zu können.

Diese Komponente ist besonders hilfreich, wenn innerhalb der letzten 24 Stunden Probleme bei der App aufgetreten sind, jedoch stehen Ihnen alle Diagnosediagramme jederzeit für die Analyse zur Verfügung.

Die App Service-Diagnose funktioniert nicht nur bei Ihrer App unter Windows, sondern auch bei Apps für [Linux-Container](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), die [App Service-Umgebung](https://docs.microsoft.com/azure/app-service/environment/intro) und [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Öffnen der App Service-Diagnose

Navigieren Sie für den Zugriff auf die App Service-Diagnose zu Ihrer App Service-Web-App oder App Service-Umgebung im [Azure-Portal](https://portal.azure.com). Klicken Sie im linken Navigationsbereich auf **Diagnose und Problembehandlung**.

Navigieren Sie für Azure Functions zu Ihrer Funktions-App, klicken Sie im oberen Navigationsbereich auf **Plattformfeatures**, und wählen Sie **Diagnose und Problembehandlung** im Abschnitt **Ressourcenverwaltung** aus.

Auf der Startseite der App Service-Diagnose können Sie mithilfe der Schlüsselwörter auf den einzelnen Kacheln der Startseite die Kategorie auswählen, die auf das Problem mit Ihrer App am ehesten zutrifft. Außerdem finden Sie auf dieser Seite **Diagnosetools** für Windows-Apps. Weitere Informationen finden Sie unter [Diagnosetools (nur für Windows-Apps)](#diagnostic-tools-only-for-windows-app).

![Startseite](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interaktive Benutzeroberfläche

Nachdem Sie auf der Startseite eine Kategorie ausgewählt haben, die am ehesten auf das bei der App aufgetretene Problem zutrifft, können Sie über die interaktive Benutzeroberfläche Genie der App Service-Diagnose durch die Diagnose und Problembehebung für Ihre App geleitet werden. Sie können die in Genie angegebenen Kachelverknüpfungen verwenden, um den vollständigen Diagnosebericht der jeweiligen Problemkategorie anzuzeigen. Über die Kachelverknüpfungen haben Sie direkt die Möglichkeit, auf die Diagnosemetriken zuzugreifen.

![Kachelverknüpfungen](./media/app-service-diagnostics/tile-shortcuts-2.png)

Nach dem Klicken auf diese Kacheln wird eine Liste mit Themen angezeigt, die sich auf das in der jeweiligen Kachel beschriebene Problem beziehen. Diese Themen enthalten Auszüge mit relevanten Informationen aus dem vollständigen Bericht. Sie können auf jedes einzelne Thema klicken, um die Probleme weiter zu untersuchen. Zudem können Sie auf **Vollständigen Bericht anzeigen** klicken, um alle Themen auf einer Seite zu untersuchen.

![Themen](./media/app-service-diagnostics/application-logs-insights-3.png)

![Vollständigen Bericht anzeigen](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnosebericht

Nachdem Sie auf ein Thema geklickt haben, um das Problem weiter zu untersuchen, können Sie weitere Details zum Thema anzeigen, die häufig durch Diagramme und Markdowns ergänzt werden. Der Diagnosebericht kann sich als leistungsfähiges Tool für die genaue Ermittlung des Problems mit Ihrer App erweisen.

![Diagnosebericht](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Integritätsüberprüfung

Wenn Sie nicht wissen, welches Problem in der App auftritt oder mit welchen Problembehandlungsschritten Sie beginnen sollten, empfiehlt sich zunächst das Ausführen einer Integritätsüberprüfung. Bei der Integritätsüberprüfung werden Ihre Anwendungen analysiert, um Ihnen eine schnelle, interaktive Übersicht zu bieten. In dieser werden ordnungsgemäße und problembehaftete Elemente angegeben, und Sie erhalten Hinweise auf Bereiche, die Sie zum Untersuchen des Problems überprüfen sollten. Die intelligente und interaktive Benutzeroberfläche leitet Sie durch den Prozess der Problembehandlung. Die Integritätsüberprüfung ist in der Genie-Umgebung für Windows-Apps und im Diagnosebericht „Web-App ausgefallen“ für Linux-Apps integriert.

### <a name="health-checkup-graphs"></a>Diagramme in der Integritätsüberprüfung

Es gibt vier verschiedene Diagramme in der Integritätsüberprüfung.

- **Anforderungen und Fehler:** In diesem Diagramm werden die Anzahl der in den letzten 24 Stunden gesendeten Anforderungen mit den HTTP-Serverfehlern angezeigt.
- **Anwendungsleistung:** In diesem Diagramm wird die Antwortzeit in den letzten 24 Stunden für verschiedene Perzentilgruppen angezeigt.
- **CPU-Auslastung:** In diesem Diagramm wird die prozentuale CPU-Gesamtauslastung pro Instanz in den letzten 24 Stunden angezeigt.  
- **Speicherauslastung:** In diesem Diagramm wird die prozentuale Gesamtauslastung des physischen Speichers pro Instanz in den letzten 24 Stunden angezeigt.

![Integritätsüberprüfung](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Untersuchen von Problemen mit dem Anwendungscode (nur für Windows-Apps)

Da viele App-Probleme auf Ihren Anwendungscode zurückzuführen sind, ist die App Service-Diagnose in [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) integriert, um Ausnahmen und Probleme mit Abhängigkeiten zur entsprechend ausgewählten Ausfallzeiten hervorzuheben. Application Insights muss separat aktiviert werden.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Wählen Sie zum Anzeigen von Application Insights-Ausnahmen und -Abhängigkeiten eine der Kachelverknüpfungen **Web-App ausgefallen** oder **Web-App langsam** aus.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Schritte zur Problembehandlung (nur für Windows-Apps)

Wenn innerhalb der letzten 24 Stunden ein Problem einer bestimmten Kategorie erkannt wurde, können Sie den vollständigen Diagnosebericht anzeigen. Möglicherweise werden Sie von der App Service-Diagnose aufgefordert, weitere Hinweise zur Fehlerbehandlung und die nächsten Schritte anzuzeigen, um eine umfassendere Anleitung zu erhalten.

![Application Insights, Problembehandlung und nächste Schritte](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnosetools (nur für Windows-Apps)

Diagnosetools umfassen erweiterte Diagnosetools, mit denen Sie Probleme mit dem Anwendungscode, langsame Ausführung, Verbindungszeichenfolgen und vieles mehr untersuchen können. Darüber hinaus sind proaktive Tools enthalten, mit denen Sie Probleme mit der CPU-Auslastung, mit Anforderungen und dem Speicher beheben können.

### <a name="proactive-cpu-monitoring"></a>Proaktive CPU-Überwachung

Die proaktive CPU-Überwachung stellt eine einfache und proaktive Möglichkeit für Sie dar, mit einer Aktion einzugreifen, wenn eine App oder ein untergeordneter Prozess der App CPU-Ressourcen in hohem Maße auslastet. Sie können eigene Regeln für den CPU-Schwellenwert festlegen, um eine hohe CPU-Auslastung vorübergehend zu beheben, bis die tatsächliche Ursache für das unerwartete Problem gefunden wird.

![Proaktive CPU-Überwachung](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Proaktive automatische Reparatur

Wie die proaktive CPU-Überwachung bietet die proaktive automatische Reparatur eine einfache und proaktive Möglichkeit, unerwartetes Verhalten Ihrer App zu beheben. Sie können eigene Regeln basierend auf der Anzahl der Anforderungen, langsamen Anforderungen, dem Speicherlimit und dem HTTP-Statuscode festlegen, um Abhilfeaktionen auszulösen. Dieses Tool kann verwendet werden, um unerwartetes Verhalten vorübergehend zu beheben, bis die tatsächliche Ursache für das Problem gefunden wird. Weitere Informationen zur proaktiven automatischen Reparatur finden Sie unter [Announcing the new auto healing experience in app service diagnostics](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html) (Bekanntgabe der neuen Funktion zur automatischen Reparatur in der App Service-Diagnose).

![Proaktive automatische Reparatur](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis"></a>Ändern der Analyse

In einer schnelllebigen Entwicklungsumgebung kann es mitunter schwierig sein, alle Änderungen an Ihrer App nachzuverfolgen, geschweige denn eine Änderung zu erkennen, die zu einem fehlerhaften Verhalten geführt hat. Mit der Änderungsanalyse können Sie die an Ihrer App vorgenommenen Änderungen eingrenzen und so die Problembehebung vereinfachen. Die Änderungsanalyse ist in den Diagnosebericht eingebettet, z. B. **Anwendungsabstürze**, sodass Sie sie gleichzeitig mit anderen Metriken verwenden können.

![Standardseite der Änderungsanalyse](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Vergleichsansicht](./media/app-service-diagnostics/diff-view-12.png)

Die Änderungsanalyse muss vor der Verwendung aktiviert werden. Weitere Informationen zur Änderungsanalyse finden Sie unter [Announcing the new change analysis experience in App Service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html) (Bekanntgabe der neuen Funktion zur Änderungsanalyse in der App Service-Diagnose).