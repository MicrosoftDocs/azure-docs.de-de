---
title: Erstellen benutzerdefinierter Dashboards in Azure Application Insights | Microsoft-Dokumentation
description: Tutorial zum Erstellen benutzerdefinierter KPI-Dashboards mithilfe von Azure Application Insights
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 701480300101b019830c57d9aa000534fa63bb6a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028528"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Erstellen benutzerdefinierter KPI-Dashboards mithilfe von Azure Application Insights

Sie können mehrere Dashboards im Azure-Portal erstellen, wobei jedes Kacheln enthält, die Daten aus mehreren Azure-Ressourcen in verschiedenen Ressourcengruppen und Abonnements visualisieren.  Außerdem lassen sich verschiedene Diagramme und Ansichten aus Azure Application Insights anheften, um benutzerdefinierte Dashboards zu erstellen, mit denen Sie einen vollständigen Überblick über die Integrität und Leistung der Anwendung erhalten. Dieses Tutorial führt Sie durch die Erstellung eines benutzerdefinierten Dashboards, das mehrere Daten- und Visualisierungstypen aus Azure Application Insights enthält.

 Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines benutzerdefinierten Dashboards in Azure
> * Hinzufügen einer Kachel aus dem Kachelkatalog
> * Hinzufügen von Standardmetriken in Application Insights zum Dashboard
> * Hinzufügen eines benutzerdefinierten Metrikdiagramms in Application Insights zum Dashboard
> * Hinzufügen der Ergebnisse einer Protokolle (Analytics)-Abfrage zum Dashboard

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Stellen Sie eine .NET-Anwendung in Azure bereit, und [aktivieren Sie das Application Insights SDK](../app/asp-net.md).

> [!NOTE]
> Die erforderlichen Berechtigungen für die Verwendung von Dashboards werden im Artikel mit [grundlegenden Informationen zur Zugriffssteuerung für Dashboards](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards) erläutert.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-a-new-dashboard"></a>Erstellen eines neuen Dashboards

> [!WARNING]
> Wenn Sie Ihre Application Insights-Ressource in eine andere Ressourcengruppe oder ein anderes Abonnement verschieben, müssen Sie das Dashboard manuell aktualisieren, indem Sie die alten Kacheln entfernen und neue Kacheln aus der gleichen Application Insights-Ressource an einer neuen Position anheften.

Ein einzelnes Dashboard kann Ressourcen aus mehreren Anwendungen, Ressourcengruppen und Abonnements enthalten.  Starten Sie das Tutorial, indem Sie ein neues Dashboard für Ihre Anwendung erstellen.  

1. Klicken Sie im Azure-Portal im Dropdownmenü auf der linken Seite auf **Dashboard**.

    ![Dropdownmenü im Azure-Portal](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. Klicken Sie im Bereich „Dashboard“ zunächst auf **Neues Dashboard** und dann auf **Leeres Dashboard**.

   ![Neues Dashboard](media/tutorial-app-dashboards/new-dashboard.png)

3. Geben Sie einen Namen für das Dashboard ein.
4. Werfen Sie einen Blick auf den **Kachelkatalog**. Dort finden Sie verschiedene Kacheln, die Sie zum Dashboard hinzufügen können.  Sie können aber nicht nur Kacheln aus dem Katalog hinzufügen. Sie haben außerdem die Möglichkeit, Diagramme und andere Ansichten direkt aus Application Insights an das Dashboard anzuheften.
5. Suchen Sie die Kachel **Markdown**, und ziehen Sie sie in Ihr Dashboard.  Diese Kachel ermöglicht es Ihnen, in Markdown formatierten Text hinzuzufügen. Diese Funktion eignet sich ideal, um Ihrem Dashboard beschreibenden Text hinzuzufügen. Weitere Informationen finden Sie unter [Verwenden einer Markdown-Kachel in Azure-Dashboards zum Anzeigen von benutzerdefinierten Inhalten](../../azure-portal/azure-portal-markdown-tile.md).
6. Fügen Sie den Eigenschaften der Kachel Text hinzu, und passen Sie deren Größe im Dashboardbereich an.

    [![Bearbeiten der Markdown-Kachel](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. Klicken Sie am oberen Bildschirmrand auf **Anpassung abgeschlossen**, um den Bearbeitungsmodus für die Kachel zu beenden.

## <a name="add-health-overview"></a>Hinzufügen der Integritätsübersicht

Ein Dashboard, das statischen Text enthält, ist nicht besonders interessant. Fügen Sie daher eine Kachel aus Application Insights hinzu, um Informationen zu Ihrer Anwendung darzustellen. Application Insights-Kacheln lassen sich aus dem Kachelkatalog hinzufügen oder direkt von Application Insights-Bildschirmen anheften. Dadurch können Sie Diagramme und Ansichten konfigurieren, mit denen Sie bereits vertraut sind, bevor Sie diese an Ihr Dashboard anheften.  Fügen Sie zuerst die Standardübersicht über die Integrität Ihrer Anwendung hinzu.  Dies erfordert keine Konfiguration und ermöglicht minimale Anpassungen im Dashboard.


1. Wählen Sie auf dem Startbildschirm Ihre **Application Insights**-Ressource aus.
2. Klicken Sie im Bereich **Übersicht** auf das Stecknadelsymbol ![Stecknadelsymbol](media/tutorial-app-dashboards/pushpin.png), um die Kachel an ein Dashboard anzuheften.
3. Wählen Sie auf der Registerkarte „An Dashboard anheften“ aus, zu welchem Dashboard die Kachel hinzugefügt werden soll, oder erstellen Sie ein neues Dashboard.
 
3. Rechts oben wird eine Benachrichtigung mit dem Hinweis angezeigt, dass die Kachel an Ihr Dashboard angeheftet wurde.  Klicken Sie in der Benachrichtigung auf **An Dashboard angeheftet**, um zum Dashboard zurückzukehren oder den Bereich „Dashboard“ zu verwenden.
4. Diese Kachel wurde Ihrem Dashboard hinzugefügt. Wählen Sie **Bearbeiten**, um die Positionierung der Kachel zu ändern. Klicken Sie darauf, ziehen Sie sie an die gewünschte Position, und klicken Sie dann auf **Anpassung abgeschlossen**. Ihr Dashboard verfügt jetzt über eine Kachel mit hilfreichen Informationen.

    [![Dashboard im Bearbeitungsmodus](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Hinzufügen eines benutzerdefinierten Metrikdiagramms

Im Bereich **Metriken** können Sie eine Metrik grafisch darstellen, die von Application Insights über einen Zeitraum hinweg mit optionalen Filtern und Gruppierungen erfasst wurde.  Wie alle anderen Elemente in Application Insights auch, können Sie dieses Diagramm dem Dashboard hinzufügen.  Dies erfordert aber kleine Anpassungen.

1. Wählen Sie auf dem Startbildschirm die **Application Insights**-Ressource aus.
1. Klicken Sie auf **Metriken**.  
2. Ein leeres Diagramm wurde bereits erstellt, und Sie werden aufgefordert, eine Metrik hinzuzufügen.  Fügen Sie dem Diagramm eine Metrik hinzu sowie optional einen Filter und eine Gruppierung.  Im folgenden Beispiel wird die Anzahl der Serveranforderungen gruppiert nach Erfolg dargestellt.  Dies bietet einen aktuellen Überblick über erfolgreiche und fehlgeschlagene Anforderungen.

    [![Hinzufügen von Metriken](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Wählen Sie rechts die Option **An Dashboard anheften** aus.

3.  Rechts oben wird eine Benachrichtigung mit dem Hinweis angezeigt, dass die Kachel an Ihr Dashboard angeheftet wurde. Klicken Sie in der Benachrichtigung auf **An Dashboard angeheftet**, um zum Dashboard zurückzukehren oder die Registerkarte „Dashboard“ zu verwenden.

4. Diese Kachel wurde Ihrem Dashboard hinzugefügt. Wählen Sie **Bearbeiten**, um die Positionierung der Kachel zu ändern. Klicken Sie auf die Kachel, ziehen Sie sie an die gewünschte Position, und klicken Sie dann auf **Anpassung abgeschlossen**.

## <a name="add-logs-query"></a>Hinzufügen von Protokollabfragen

Azure Application Insights-Protokolle bietet eine leistungsfähige Abfragesprache, mit der Sie alle von Application Insights erfassten Daten analysieren können. Sie können Ihrem Dashboard die Ausgabe einer Protokollabfrage genauso wie Diagramme und andere Ansichten hinzufügen.

1. Wählen Sie auf dem Startbildschirm die **Application Insights**-Ressource aus.
2. Klicken Sie links unter „Überwachung“ auf **Protokolle**, um die Registerkarte „Protokolle“ zu öffnen.
3. Geben Sie die folgende Abfrage ein, die die zehn am häufigsten angeforderten Seiten und die Anzahl der Anforderungen zurückgibt:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Klicken Sie auf **Ausführen**, um die Ergebnisse der Abfrage zu überprüfen.
5. Wählen Sie das Anheftsymbol aus ![Stecknadelsymbol](media/tutorial-app-dashboards/pushpin.png) und wählen Sie den Namen des Dashboards aus.

5. Fügen Sie eine weitere Abfrage hinzu, bevor Sie zum Dashboard zurückkehren. Rendern Sie sie dieses Mal als Diagramm, um die verschiedenen Möglichkeiten der Visualisierung von Protokollabfragen in einem Dashboard kennenzulernen. Beginnen Sie mit der folgenden Abfrage, die die zehn häufigsten Vorgänge mit den meisten Ausnahmen zusammenfasst.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Klicken Sie auf **Diagramm**, und ändern Sie es in ein **Ringdiagramm**, um die Ausgabe anzuzeigen.

    [![Ringdiagramm mit der obigen Abfrage](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Wählen Sie das Anheftsymbol aus ![Stecknadelsymbol](media/tutorial-app-dashboards/pushpin.png) das sich in der Ecke rechts oben befindet, um das Diagramm an Ihr Dashboard anzuheften, und navigieren Sie dann zurück zu Ihrem Dashboard.
7. Die Ergebnisse der Abfragen werden Ihrem Dashboard jetzt in dem Format hinzugefügt, das Sie ausgewählt haben. Klicken Sie darauf, ziehen Sie sie jeweils an die gewünschte Position, und klicken Sie dann auf **Anpassung abgeschlossen**.
8. Klicken Sie auf das Stiftsymbol ![Stiftsymbol](media/tutorial-app-dashboards/pencil.png) jedes Titels, um einen aussagekräftigen Titel einzugeben.

## <a name="share-dashboard"></a>Dashboard freigeben

1. Klicken Sie am oberen Rand des Dashboards auf **Freigeben**, um Ihre Änderungen zu veröffentlichen.
2. Sie können optional bestimmte Benutzer definieren, die Zugriff auf das Dashboard haben sollen. Weitere Informationen finden Sie unter [Freigeben von Azure-Dashboards mithilfe der rollenbasierten Zugriffssteuerung](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Wählen Sie **Veröffentlichen**.

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt wissen, wie Sie benutzerdefinierte Dashboards erstellen, können Sie einen Blick auf die übrige Application Insights-Dokumentation einschließlich einer Fallstudie werfen.

> [!div class="nextstepaction"]
> [Eingehende Diagnose](../app/devops.md)
