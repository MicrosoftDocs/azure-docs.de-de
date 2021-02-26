---
title: Diagnostizieren von Laufzeitausnahmen mit Azure Application Insights | Microsoft-Dokumentation
description: Tutorial zum Suchen und Diagnostizieren von Laufzeitausnahmen in Ihrer Anwendung mithilfe von Azure Application Insights
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 98ccaef716ae2390dcbcfbc7c4a1916359115f93
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597635"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Suchen und Diagnostizieren von Laufzeitausnahmen mit Azure Application Insights

Azure Application Insights erfasst Telemetriedaten Ihrer Anwendung, um Laufzeitausnahmen zu identifizieren und diagnostizieren.  Dieses Tutorial führt Sie mit Ihrer Anwendung durch diesen Vorgang.  Folgendes wird vermittelt:

> [!div class="checklist"]
> * Ändern Ihres Projekt zum Aktivieren der Ausnahmeverfolgung
> * Identifizieren von Ausnahmen für verschiedene Komponenten Ihrer Anwendung
> * Anzeigen der Details einer Ausnahme
> * Herunterladen einer Momentaufnahme der Ausnahme in Visual Studio zum Debuggen
> * Analysieren der Details fehlgeschlagener Anforderungen mithilfe der Abfragesprache
> * Erstellen eines neuen Arbeitselements zum Korrigieren des fehlerhaften Codes


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit den folgenden Workloads:
    - ASP.NET und Webentwicklung
    - Azure-Entwicklung
- Laden Sie den [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger) herunter, und installieren Sie diesen.
- Aktivieren Sie [Debugmomentaufnahmen von Ausnahmen in .NET-Apps](../app/snapshot-debugger.md).
- Stellen Sie eine .NET-Anwendung in Azure bereit, und [aktivieren Sie das Application Insights SDK](../app/asp-net.md). 
- Das Tutorial verfolgt die Identifikation einer Ausnahme in Ihrer Anwendung, ändern Sie also den Code in Ihrer Entwicklungs- oder Testumgebung, um eine Ausnahme zu generieren. 

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.


## <a name="analyze-failures"></a>Analysieren von Fehlern
Application Insights erfasst alle Fehler in Ihrer Anwendung und ermöglicht Ihnen das Anzeigen deren Häufigkeit in verschiedenen Vorgänge, damit Sie sich auf die Fehler mit den höchsten Auswirkungen konzentrieren können.  Sie können dann die Ursache dieser Fehler identifizieren, indem Sie einen Drilldown zu deren Details ausführen.   

1. Wählen Sie **Application Insights** und anschließend Ihr Abonnement aus.  
2. Zum Öffnen des Bereichs **Fehler** wählen Sie entweder im Menü **Untersuchen** die Option **Fehler**, oder klicken Sie auf das Diagramm **Anforderungsfehler**.

    ![Anforderungsfehler](media/tutorial-runtime-exceptions/failed-requests.png)

3. Im Bereich **Anforderungsfehler** werden für jeden Vorgang für die Anwendung die Anzahl der fehlgeschlagenen Anforderungen und die Anzahl der betroffenen Benutzer angezeigt.  Durch das Sortieren dieser Informationen nach Benutzer können Sie die Fehler identifizieren, die sich auf die meisten Benutzer auswirken.  In diesem Beispiel sollten **GET Employees/Create** und **GET Customers/Details** aufgrund der hohen Anzahl von Fehlern und betroffenen Benutzern untersucht werden.  Durch Auswählen eines Vorgangs werden im rechten Bereich weitere Informationen zu diesem Vorgang angezeigt.

    ![Bereich „Anforderungsfehler“](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. Reduzieren Sie das Zeitfenster, um den Zeitraum zu vergrößern, in dem die Fehlerrate eine Spitze aufweist.

    ![Fenster „Anforderungsfehler“](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. Klicken Sie auf die Schaltfläche mit der Anzahl gefilterter Ergebnisse, um sich die entsprechenden Ergebnisse anzusehen. Die vorgeschlagenen Beispiele verfügen über entsprechende Telemetriedaten von allen Komponenten, auch wenn für sie ggf. die Stichprobenentnahme aktiviert war. Klicken Sie auf ein Suchergebnis, um Details des Fehlers anzuzeigen.

    ![Beispiele für Anforderungsfehler](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. Die Details des Anforderungsfehlers enthalten ein Gantt-Diagramm, dem Sie entnehmen können, dass bei dieser Transaktion zwei Abhängigkeitsfehler aufgetreten sind, die über 50 Prozent der Gesamtdauer der Transaktion ausgemacht haben. Auf dieser Oberfläche werden alle Telemetriedaten für die Komponenten einer verteilten Anwendung dargestellt, die mit dieser Vorgangs-ID zusammenhängen. [Weitere Informationen zur neuen Oberfläche](../app/transaction-diagnostics.md) Sie können auf beliebige Elemente klicken, um auf der rechten Seite entsprechende Details anzuzeigen. 

    ![Details zu Anforderungen mit Fehlern](media/tutorial-runtime-exceptions/failed-request-details.png)

7. In den Vorgangsdetails wird auch eine Ausnahme vom Typ „FormatException“ angezeigt, die den Fehler anscheinend verursacht hat.  Sie können sehen, dass die Ursache eine ungültige Postleitzahl ist. Sie können die Debugmomentaufnahme öffnen, um Debuginformationen auf Codeebene in Visual Studio anzuzeigen.

    ![Ausnahmedetails](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Fehlerhaften Code identifizieren
Der Momentaufnahmedebugger erfasst Momentaufnahmen der häufigsten Ausnahmen in Ihrer Anwendung, um Sie beim Diagnostizieren der Ursache in der Produktion zu unterstützen.  Sie können Debugmomentaufnahmen im Portal anzeigen, um die Aufrufliste anzuzeigen und die Variablen in jedem Aufruflistenrahmen zu überprüfen. Danach können Sie den Quellcode debuggen, indem Sie die Momentaufnahme herunterladen und in Visual Studio 2019 Enterprise öffnen.

1. Klicken Sie in den Eigenschaften der Ausnahme auf **Debugmomentaufnahme öffnen**.
2. Der Bereich **Momentaufnahme debuggen** Bereich wird mit der Aufrufliste für die Anforderung geöffnet.  Klicken Sie auf eine beliebige Methode, um die Werte aller lokalen Variablen zum Zeitpunkt der Anforderung anzuzeigen.  Beginnend mit der ersten Methode in diesem Beispiel sehen wir lokale Variablen, die keinen Wert aufweisen.

    ![Momentaufnahme debuggen](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. Der erste Aufruf mit gültigen Werten ist **ValidZipCode**, und wir können sehen, dass eine Postleitzahl mit Buchstaben angegeben wurde, die nicht in eine ganze Zahl übersetzt werden können.  Dies scheint der Fehler im Code zu sein, der behoben werden muss.

    ![Screenshot: Fehler im Code, der behoben werden muss    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. Dann haben Sie die Option, diese Momentaufnahme in Visual Studio herunterzuladen, wo wir den tatsächlichen Code finden, der korrigiert werden muss. Zu diesem Zweck klicken Sie auf **Momentaufnahme herunterladen**.
5. Die Momentaufnahme wird in Visual Studio geladen.
6. Jetzt können Sie eine Debugsitzung in Visual Studio Enterprise ausführen, mit der die Codezeile, die die Ausnahme verursacht hat, schnell identifiziert wird.

    ![Ausnahme im Code](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Verwenden von Analysedaten
Alle von Application Insights gesammelten Daten werden in Azure Log Analytics gespeichert, die eine umfangreiche Abfragesprache bereitstellt, die Ihnen eine Vielzahl von Möglichkeiten zum Analysieren der Daten bietet.  Wir können diese Daten verwenden, um die Anforderungen zu analysieren, die die von uns untersuchte Ausnahme generiert haben. 

1. Klicken Sie auf die CodeLens-Informationen über dem Code, um von Application Insights bereitgestellte Telemetriedaten anzuzeigen.

    ![Code](media/tutorial-runtime-exceptions/codelens.png)

1. Klicken Sie auf **Auswirkungen analysieren**, um Application Insights Analytics zu öffnen.  Die Analyse enthält mehrere Abfragen, die Details über Anforderungsfehler bereitstellen, z.B. betroffene Benutzer, Browser und Regionen.<br><br>![Screenshot des Application Insights-Fensters mit mehreren Abfragen](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Arbeitselement hinzufügen
Wenn Sie Application Insights mit einem Nachverfolgungssystem wie Azure DevOps oder GitHub verbinden, können Sie direkt in Application Insights ein Arbeitselement erstellen.

1. Kehren Sie zum Bereich mit den **Ausnahmeeigenschaften** in Application Insights zurück.
2. Klicken Sie auf **Neues Arbeitselement**.
3. Der Bereich **New Work Item** wird geöffnet und enthält bereits Details zur Ausnahme.  Sie können vor dem Speichern weitere Informationen hinzufügen.

    ![Neues Arbeitselement](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit dem Identifizieren von Laufzeitausnahmen vertraut gemacht haben, können Sie mit dem nächsten Tutorial fortfahren. Dort erfahren Sie, wie Leistungsprobleme identifiziert und diagnostiziert werden.

> [!div class="nextstepaction"]
> [Identifizieren von Leistungsproblemen](./tutorial-performance.md)

