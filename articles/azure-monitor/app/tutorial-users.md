---
title: Analysieren von Kundenverhalten in Azure Application Insights | Microsoft-Dokumentation
description: 'Tutorial: Verwenden von Azure Application Insights zur Analyse der Kundeninteraktion mit Ihrer Anwendung.'
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/20/2017
ms.custom: mvc
ms.openlocfilehash: 4f6eeb5265d99a17ed18ace91fa5f2d2f744dbb2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597604"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Verwenden von Azure Application Insights zur Analyse der Kundeninteraktion mit Ihrer Anwendung

Azure Application Insights sammelt Nutzungsinformationen, mit denen Sie nachvollziehen können, wie Benutzer mit Ihrer Anwendung interagieren.  In diesem Tutorial werden verschiedene Ressourcen vorgestellt, die zur Analyse dieser Informationen verwendet werden können.  Sie lernen Folgendes:

> [!div class="checklist"]
> * Analysieren von Daten über Benutzer, die auf Ihre Anwendung zugreifen
> * Verwenden von Sitzungsinformationen zur Analyse der Kundeninteraktion mit Ihrer Anwendung
> * Definieren von Trichtern, mit denen Sie die gewünschte Benutzeraktivität mit der tatsächlichen Aktivität vergleichen können 
> * Erstellen einer Arbeitsmappe, mit der Visualisierungen und Abfragen in einem einzigen Dokument zusammengefasst werden
> * Gruppieren von ähnlichen Benutzern für eine Gesamtanalyse
> * Ermitteln von Benutzern, die Ihre Anwendung wiederholt nutzen
> * Analysieren der Benutzernavigation in Ihrer Anwendung


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit den folgenden Workloads:
    - ASP.NET und Webentwicklung
    - Azure-Entwicklung
- Laden Sie den [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger) herunter, und installieren Sie diesen.
- Stellen Sie eine .NET-Anwendung in Azure bereit, und [aktivieren Sie das Application Insights SDK](../app/asp-net.md). 
- [Senden Sie Telemetriedaten aus Ihrer Anwendung](../app/usage-overview.md#send-telemetry-from-your-app) zum Hinzufügen von benutzerdefinierten Ereignis-/Seitenansichten.
- Senden Sie [Benutzerkontext-IDs](../app/usage-send-user-context.md), um Benutzeraktivitäten über einen bestimmten Zeitraum nachzuverfolgen und alle Nutzungsfunktionen zu verwenden.

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="get-information-about-your-users"></a>Auswerten von Benutzerdaten
Im Bereich **Benutzer** können Sie wichtige Benutzerdaten auf unterschiedliche Weise analysieren. Beispielsweise können Sie hier benutzerbezogene Informationen wie den Ort der Verbindungsherstellung, Clientdaten und bestimmte Anwendungsbereiche nachvollziehen, auf die zugegriffen wurde. 

1. Klicken Sie auf **Application Insights**, und wählen Sie anschließend Ihr Abonnement aus.
2. Klicken Sie im Menü auf **Benutzer**.
3. In der Standardansicht wird die Anzahl der individuellen Benutzer angezeigt, die sich in den letzten 24 Stunden mit Ihrer Anwendung verbunden haben.  Sie können das Zeitfenster ändern und verschiedene andere Kriterien festlegen, um diese Informationen zu filtern.

    ![Abfrage-Generator](media/tutorial-users/QueryBuilder.png)

6. Klicken Sie auf die Dropdownliste **Während**, und legen Sie als Zeitfenster sieben Tage fest.  Hierdurch werden in den unterschiedlichen Diagrammen im Bereich mehr Daten angezeigt.

    ![Ändern des Zeitbereichs](media/tutorial-users/TimeRange.png)

4. Klicken Sie auf die Dropdownliste **Split by** (Trennen nach), um sich im Diagramm eine Aufschlüsselung nach Benutzereigenschaft anzeigen zu lassen.  Wählen Sie aus der Dropdownliste **Land oder Region** einen entsprechenden Eintrag aus.  Im Diagramm werden nun zwar dieselben Daten angezeigt, jedoch können Sie sich eine Aufschlüsselung nach der Anzahl der Benutzer jedes Landes/jeder Region anzeigen lassen.

    ![Diagramm „Land oder Region“](media/tutorial-users/CountryorRegion.png)

5. Positionieren Sie den Cursor über verschiedenen Balken im Diagramm. Beachten Sie hierbei, dass sich die angezeigte Anzahl für jedes Land/jede Region nur auf das Zeitfenster bezieht, das von diesem Balken dargestellt wird.
6. Auf der rechten Seite befindet sich die Spalte **Insights**, in der Benutzerdaten analysiert werden.  Hier werden Informationen wie die Anzahl der individuellen Sitzungen innerhalb des festgelegten Zeitraums und Datensätze mit gemeinsamen Eigenschaften angezeigt, die einen Großteil der Benutzerdaten darstellen. 

    ![Spalte „Insights“](media/tutorial-users/insights.png)


## <a name="analyze-user-sessions"></a>Analysieren von Benutzersitzungen
Der Bereich **Sessions** (Sitzungen) ist vergleichbar mit dem Bereich **Benutzer**.  Während Sie im Bereich **Benutzer** Daten über Benutzer analysieren können, die auf Ihre Anwendung zugreifen, hilft Ihnen der Bereich **Sessions** (Sitzungen) dabei, nachzuvollziehen, wie diese Benutzer die Anwendung verwenden.  

1. Klicken Sie im Menü auf **Sessions** (Sitzungen).
2. Beachten Sie, dass für das Diagramm dieselben Optionen zum Filtern und Unterteilen der Daten wie im Bereich **Benutzer** verfügbar sind.

    ![Abfrage-Generator für Sitzungen](media/tutorial-users/SessionsBuilder.png)

3. Auf der rechten Seite werden im Bereich **Stichprobe dieser Sitzungen** Sitzungen angezeigt, die viele Ereignisse enthalten.  Diese Sitzungen bieten sich für eine Analyse an.

    ![„Stichprobe dieser Sitzungen“](media/tutorial-users/SessionsSample.png)

4. Klicken Sie auf eine der Sitzungen, um sich die **Sitzungszeitachse** anzeigen zu lassen,die alle Aktionen der Sitzungen auflistet.  Auf diese Weise lassen sich Informationen wie z.B. Sitzungen mit einer großen Anzahl von Ausnahmen identifizieren.

    ![„Sitzungszeitachse“](media/tutorial-users/SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Gruppieren von ähnlichen Benutzern
Eine **Kohorte** umfasst Benutzer, die nach ähnlichen Eigenschaften gruppiert werden.  Sie können Kohorten zum Filtern von Daten in anderen Bereichen verwenden, um bestimmte Benutzergruppen zu analysieren.  Beispielsweise lassen sich so nur Benutzer analysieren, die einen Kaufvorgang abgeschlossen haben.

1.  Klicken Sie im Menü auf **Kohorten**.
2.  Klicken Sie auf **New** (Neu), um eine neue Kohorte zu erstellen.
3.  Wählen Sie in der Dropdownliste **Who used** (Verwendete Aktion) eine Aktion aus.  Nur Benutzer, die diese Aktion innerhalb des Zeitfensters des Berichts ausgeführt haben, werden angezeigt.

    ![Kohorte, die die angegebenen Aktionen ausgeführt hat](media/tutorial-users/CohortsDropdown.png)

4.  Klicken Sie im Menü auf **Benutzer**.
5.  Wählen Sie in der Dropdownliste **Anzeigen** die erstellte Kohorte aus.  Die Diagrammdaten werden auf diese Benutzer beschränkt.

    ![Kohorte in Benutzertool](media/tutorial-users/UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Vergleichen von gewünschter und tatsächlicher Aktivität
Während der Fokus in den vorherigen Bereichen auf dem tatsächlichen Benutzerverhalten lag, verschiebt sich dieser über **Trichter** nun auf das gewünschte Benutzerverhalten.  Ein Trichter setzt sich aus mehreren Schritten Ihrer Anwendung und dem Prozentsatz der Benutzer zusammen, der zwischen den einzelnen Schritten navigiert.  Sie können z.B. einen Trichter erstellen, mit dem der Prozentsatz der Benutzer erfasst wird, der eine Verbindung zu Ihrer Anwendung herstellt, um nach einem Produkt zu suchen.  Anschließend wird Ihnen der prozentuale Anteil der Benutzer angezeigt, der das Produkt zum Einkaufswagen hinzugefügt und ggf. den Kaufvorgang abgeschlossen hat.

1. Klicken Sie im Menü zuerst auf **Trichter** und anschließend auf **New** (Neu). 

    ![Screenshot: Erstellen eines neuen Trichters](media/tutorial-users/funnelsnew.png)

2. Geben Sie in das Feld **Trichtername** einen Wert ein.
3. Erstellen Sie einen Trichter mit mindestens zwei Schritten, indem Sie für jeden Schritt eine Aktion auswählen.  Die Liste der Aktionen basiert auf den von Application Insights gesammelten Nutzungsdaten.

    ![Screenshot: Erstellen von Schritten in einem neuen Trichter](media/tutorial-users/funnelsedit.png)

4. Klicken Sie auf **Save** (Speichern), um den Trichter zu speichern und sich die zugehörigen Ergebnisse anzeigen zu lassen.  Im Fenster rechts neben dem Trichter werden die am häufigsten aufgetretenen Ereignisse vor der ersten und nach der letzten Aktivität angezeigt. So können Sie Benutzertendenzen für eine bestimmte Aktionssequenz nachvollziehen.

    ![Screenshot: Ereignisse für einen neu erstellten Trichter](media/tutorial-users/funnelsright.png)


## <a name="learn-which-customers-return"></a>Ermitteln von Kunden, die wiederholt auf Ihre Anwendung zugreifen
Mit der Option **Retention** (Vermerkdauer) können Sie nachvollziehen, welche Benutzer Ihre Anwendung mehrfach nutzen.  

1. Klicken Sie im Menü auf **Retention** (Vermerkdauer).
2. Standardmäßig umfassen die analysierten Informationen Benutzer, die eine Aktion ausgeführt, die Anwendung verlassen und anschließend die Anwendung erneut verwendet haben, um eine weitere Aktion auszuführen.  Sie können diesen Filter z.B. so konfigurieren, dass nur Benutzer erfasst werden, die nach Abschluss eines Kaufvorgangs erneut auf Ihre Anwendung zugegriffen haben.

    ![Screenshot: Festlegen eines Filters für die Vermerkdauer](media/tutorial-users/retentionquery.png)

3. Die Anzahl der wiederkehrenden Benutzer, die den Kriterien entsprechen, wird in einem Diagramm und in einer Tabelle für verschiedene Zeiträume angezeigt.  Üblicherweise ergibt sich mit größer werdenden Zeiträumen als Muster ein sukzessiver Abfall von wiederkehrenden Benutzern.  Ein plötzlicher Abfall zwischen Zeiträumen kann unter Umständen Anlass für Bedenken sein. 

    ![Screenshot: Diagramm der Benutzer, die den festgelegten Kriterien für die Vermerkdauer entsprechen](media/tutorial-users/retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analysieren der Benutzernavigation
Mit einem **User flow** (Benutzerflow) wird die Navigation von Benutzern zwischen den Seiten und Funktionen Ihrer Anwendung visualisiert.  Dadurch können Sie ermitteln, zu welchem Ziel Benutzer in der Regel von einer bestimmten Seite aus navigieren, wie sie die Anwendung üblicherweise beenden und welche Aktionen wiederholt ausgeführt werden.

1.  Klicken Sie im Menü auf **User flows** (Benutzerflows).
2.  Klicken Sie zur Erstellung eines neuen Benutzerflows zuerst auf **New** (Neu) und anschließend auf **Bearbeiten**, um die zugehörigen Daten zu bearbeiten.
3.  Erhöhen Sie den **Zeitbereich** auf sieben Tage, und wählen Sie anschließend ein Ausgangsereignis aus.  Der Flow erfasst nun Benutzersitzungen, die mit diesem Ereignis beginnen.

    ![Screenshot: Erstellen eines neuen Benutzerflows](media/tutorial-users/flowsedit.png)

4.  Der Benutzerflow wird angezeigt. In diesem sehen Sie unterschiedliche Benutzerpfade und die Anzahl der Benutzersitzungen.  Blaue Linien stehen für eine Aktion, die der Benutzer nach der aktuellen Aktion ausgeführt hat.  Rote Linien stellen das Ende der Benutzersitzung dar.

    ![Screenshot: Anzeige der Benutzerpfade und der Anzahl der Benutzersitzungen für einen Benutzerflow](media/tutorial-users/flows.png)

5.  Klicken Sie zuerst auf das **x** in der Ecke des Aktionsfelds und anschließend auf **Diagramm erstellen**, um ein Ereignis aus dem Flow zu entfernen.  Daraufhin wird jede Instanz des Ereignisses entfernt, und das Diagramm wird neu gezeichnet.  Klicken Sie auf **Bearbeiten**, damit das Ereignis zu der Liste **Ausgeschlossene Ereignisse** hinzugefügt wird.

    ![Screenshot: Liste der ausgeschlossenen Ereignisse für einen Benutzerflow](media/tutorial-users/flowsexclude.png)

## <a name="consolidate-usage-data"></a>Zusammenfassung von Nutzungsdaten
In **Workbooks** (Arbeitsmappen) werden Datenvisualisierungen, Analytics-Abfragen und Text in interaktiven Dokumenten zusammengefasst.  Sie können Arbeitsmappen verwenden, um gemeinsame Nutzungsdaten zu gruppieren, Informationen zu einem bestimmten Vorfall zusammenzutragen oder ein Team über die beobachtete Nutzung Ihrer Anwendung zu informieren.

1.  Klicken Sie im Menü auf **Workbooks** (Arbeitsmappen).
2.  Klicken Sie auf **New** (Neu), um eine neue Arbeitsmappe zu erstellen.
3.  Eine Abfrage wird bereitgestellt, in der alle Nutzungsdaten des letzten Tags als Balkendiagramm angezeigt werden.  Sie können diese Abfrage verwenden oder manuell bearbeiten. Alternativ können Sie auf **Beispielabfragen** klicken, um weitere relevante Abfragen auszuwählen.

    ![Screenshot: Liste der Beispielabfragen, die verwendet werden können](media/tutorial-users/samplequeries.png)

4.  Klicken Sie auf **Bearbeitung abgeschlossen**.
5.  Klicken Sie im oberen Bereich auf **Bearbeiten**, um den Text oben in der Arbeitsmappe zu bearbeiten.  Dieser ist in Markdown formatiert.

    ![Screenshot: Bearbeiten des Texts oben in der Arbeitsmappe](media/tutorial-users/markdown.png)

6.  Klicken Sie auf **Benutzer hinzufügen**, um ein Diagramm mit Benutzerinformationen hinzuzufügen.  Bearbeiten Sie ggf. die Diagrammdaten, und klicken Sie anschließend auf **Bearbeitung abgeschlossen**, um das Diagramm zu speichern.


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie Benutzerdaten analysieren. Im nächsten Tutorial lernen Sie, benutzerdefinierte Dashboards zu erstellen, die die analysierten Informationen mit anderen nützlichen Daten über Ihre Anwendung verknüpfen.

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter Dashboards](./tutorial-app-dashboards.md)

