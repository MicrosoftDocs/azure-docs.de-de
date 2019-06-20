---
title: Schnellstart mit Azure Application Insights | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zum schnellen Einrichten einer ASP.NET Core-Web-App für die Überwachung mit Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: a4bbb43cb00eeb5a9e741016e1648ce915935eb9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236593"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Starten der Überwachung Ihrer ASP.NET Core-Webanwendung

Mit Azure Application Insights können Sie die Verfügbarkeit, Leistung und Nutzung Ihrer Webanwendung ganz einfach überwachen. Sie können auch Fehler in Ihrer Anwendung schnell erkennen und diagnostizieren, ohne darauf warten zu müssen, dass diese Fehler von Benutzern gemeldet werden. 

Diese Schnellstartanleitung führt Sie durch die notwendigen Schritte, um das Application Insights SDK zu einer vorhandenen ASP.NET Core-Webanwendung hinzuzufügen. Informationen zum Konfigurieren von Application Insights ohne Visual Studio finden Sie in diesem [Artikel](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

- [Installieren Sie Visual Studio 2019](https://www.visualstudio.com/downloads/) mit den folgenden Workloads:
  - ASP.NET und Webentwicklung
  - Azure-Entwicklung
- [Installieren Sie das .NET Core 2.0 SDK](https://www.microsoft.com/net/core).
- Sie benötigen ein Azure-Abonnement und eine vorhandene .NET Core-Webanwendung.

Wenn Sie keine ASP.NET Core-Webanwendung haben, können Sie anhand unserer detaillierten Anleitung [eine ASP.NET Core-App erstellen und Application Insights hinzufügen](../../azure-monitor/app/asp-net-core.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Application Insights kann Telemetriedaten von jeder mit dem Internet verbundenen Anwendung erfassen, unabhängig davon, ob die Anwendung lokal oder in der Cloud ausgeführt wird. Geben Sie folgendermaßen vor, um diese Daten anzuzeigen.

1. Klicken Sie auf **Ressource erstellen** > **Entwicklertools** > **Application Insights**.

   ![Hinzufügen einer Application Insights-Ressource](./media/dotnetcore-quick-start/1createresourceappinsight.png)

    Ein Dialogfeld für die Konfiguration wird geöffnet. Füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus:

    | Einstellungen        |  Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert. |
   | **Anwendungstyp** | ASP.NET-Webanwendung | Der Typ der zu überwachenden App. |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. |
   | **Location** | USA (Ost) | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts, in dem Ihre App gehostet wird. |

2. Klicken Sie auf **Create**.

## <a name="configure-app-insights-sdk"></a>Konfigurieren des Application Insights SDK

1. Öffnen Sie das **Projekt** Ihrer ASP.NET Core-Web-App in Visual Studio. Klicken Sie mit der rechten Maustaste im **Projektmappen-Explorer** auf den Namen Ihrer App, und wählen Sie **Hinzufügen** > **Application Insights-Telemetrie** aus.

    ![Hinzufügen von Application Insights-Telemetrie](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klicken Sie auf die Schaltfläche **Erste Schritte**.

3. Wählen Sie Ihr Konto, Ihr Abonnement und die **vorhandene Ressource** aus, die Sie im Azure-Portal erstellt haben. Klicken Sie dann auf **Registrieren**.

4. Wählen Sie **Debuggen** > **Ohne Debuggen starten**, oder drücken Sie STRG+F5, um Ihre App zu starten.

    ![Application Insights – Menü „Übersicht“](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Es dauert ca. 3-5 Minuten, bis die ersten Daten im Portal angezeigt werden. Wenn es sich um eine Test-App mit geringem Datenverkehr handelt, denken Sie daran, dass die meisten Metriken nur erfasst werden, wenn aktive Anforderungen oder Vorgänge vorhanden sind.

## <a name="start-monitoring-in-the-azure-portal"></a>Starten der Überwachung im Azure-Portal

1. Öffnen Sie jetzt erneut die Seite **Übersicht** für Application Insights im Azure-Portal, indem Sie **Start** auswählen. Wählen Sie dann in den zuletzt verwendeten Ressourcen die zuvor erstellte Ressource aus, um Details zur aktuell ausgeführten Anwendung anzuzeigen.

   ![Application Insights – Menü „Übersicht“](./media/dotnetcore-quick-start/4overview.png)

2. Klicken Sie auf **Anwendungsübersicht**, um ein visuelles Layout der Abhängigkeitsbeziehungen zwischen den Komponenten Ihrer Anwendung zu erhalten. Jede Komponente zeigt KPIs wie z.B. Last, Leistung, Fehler und Warnungen an.

   ![Anwendungszuordnung](./media/dotnetcore-quick-start/5appmap.png)

3. Klicken Sie auf das Symbol für die **App-Analyse**: ![Symbol „Anwendungszuordnung“](./media/dotnetcore-quick-start/006.png) **In Analytics anzeigen**. Dadurch wird die **Application Insights-Analyse** geöffnet, die eine erweiterte Abfragesprache zum Analysieren aller Daten bereitstellt, die von Application Insights gesammelt werden. In diesem Fall wird eine Abfrage für Sie generiert, die die Anzahl von Anforderungen als Diagramm darstellt. Sie können selbst Abfragen zum Analysieren anderer Daten schreiben.

   ![Analysediagramm der Benutzeranforderungen in einem bestimmten Zeitraum](./media/dotnetcore-quick-start/6analytics.png)

4. Kehren Sie zur Seite **Übersicht** zurück, und untersuchen Sie die KPI-Dashboards.  Dieses Dashboard zeigt Statistiken zur Integrität Ihrer Anwendung, einschließlich der Anzahl von eingehenden Anforderungen, der Dauer dieser Anforderungen und aller auftretenden Fehler. 

   ![Diagramm der Übersichtszeitachse für die Integrität](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Klicken Sie auf der linken Seite auf **Metriken**. Untersuchen Sie mithilfe des Metrik-Explorers die Integrität und Auslastung Ihrer Ressource. Sie können auf **Neues Diagramm hinzufügen** klicken, um zusätzliche benutzerdefinierte Ansichten zu erstellen, oder auf **Bearbeiten** klicken, um Typ, Höhe, Farbpalette, Gruppierungen und Metriken der vorhandenen Diagramme zu ändern. Sie können beispielsweise ein Diagramm für die durchschnittliche Seitenladezeit des Browsers erstellen, indem Sie in der Metrik-Dropdownliste die Option für die Browser-Seitenladezeit und unter „Aggregation“ die Durchschnittsoption auswählen. Weitere Informationen zum Azure-Metrik-Explorer finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md).

     ![Registerkarte „Metriken“: Diagramm der durchschnittlichen Ladezeit der Browserseite](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- Ein externes Video mit ausführlichen Informationen zum [Konfigurieren von Application Insights mit .NET Core und Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) von Grund auf
- Ein externes Video mit ausführlichen Informationen zum [Konfigurieren von Application Insights mit .NET Core und Visual Studio Code](https://youtu.be/ygGt84GDync) von Grund auf

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die Tests abgeschlossen haben, können Sie die Ressourcengruppe und alle dazugehörigen Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen** und dann auf **myResourceGroup**.
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld **myResourceGroup** ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Suchen nach und Diagnostizieren von Laufzeitausnahmen](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
