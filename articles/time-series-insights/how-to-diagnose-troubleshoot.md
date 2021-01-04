---
title: Diagnose und Problembehandlung einer Gen2-Umgebung – Azure Time Series Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Diagnose und Problembehandlung in einer Azure Time Series Insights Gen2-Umgebung durchführen.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 3d26a0ef86ab96940f3d5bb96d87340b77f1faca
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016835"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Diagnose und Problembehandlung in einer Azure Time Series Insights Gen2-Umgebung

In diesem Artikel werden einige häufig auftretende Probleme zusammengefasst, die auftreten können, wenn Sie mit Ihrer Azure Time Series Insights Gen2-Umgebung arbeiten. Darüber hinaus werden mögliche Ursachen und Lösungen für jedes der Probleme beschrieben.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Problem: Ich kann meine Umgebung im Gen2-Explorer nicht finden.

Dieses Problem kann auftreten, wenn Sie nicht über die Berechtigung für den Zugriff auf die Time Series Insights-Umgebung verfügen. Benutzer benötigen eine Zugriffsrolle auf Leser-Ebene, um ihre Time Series Insights-Umgebung anzuzeigen. Um die aktuellen Zugriffsebenen zu überprüfen und zusätzlichen Zugriff zu gewähren, wechseln Sie zum Abschnitt **Datenzugriffsrichtlinien** in der Time Series Insights-Ressource im [Azure-Portal](https://portal.azure.com/).

  [![Überprüfen der Datenzugriffsrichtlinien](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Problem: Im Gen2-Explorer werden keine Daten angezeigt.

Es gibt verschiedene mögliche Gründe, aus denen Ihre Daten im [Azure Time Series Insights Gen2-Explorer](https://insights.timeseries.azure.com/preview) nicht angezeigt werden.

- Ihre Ereignisquelle empfängt möglicherweise keine Daten.

    Stellen Sie sicher, dass Ihre Ereignisquelle, bei der es sich um einen Event Hub oder einen IoT-Hub handelt, Daten von Ihren Tags oder Instanzen empfängt. Um dies zu überprüfen, wechseln Sie zur Übersichtsseite Ihrer Ressourcen im Azure-Portal.

    [![Übersicht über das Überprüfen von Dashboardmetriken](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Ihre Ereignisquelldaten sind nicht im JSON-Format.

    Time Series Insights unterstützt nur JSON-Daten. JSON-Beispiele finden Sie unter [Unterstützte JSON-Formen](./concepts-json-flattening-escaping-rules.md).

- Dem Schlüssel Ihrer Ereignisquelle fehlt eine erforderliche Berechtigung.

  - Für IoT Hub müssen Sie einen Schlüssel mit der Berechtigung **Dienstverbindung** bereitstellen.

    [![Überprüfen der IoT Hub-Berechtigungen](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - Die Richtlinien **iothubowner** und **service** funktionieren beide, da sie über die Berechtigung **Dienstverbindung** verfügen.

  - Für einen Event Hub müssen Sie einen Schlüssel mit der Berechtigung **Lauschen** bereitstellen.
  
    [![Überprüfen der Event Hub-Berechtigungen](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - Die Richtlinien **read** und **write** funktionieren beide, da sie über die Berechtigung **Lauschen** verfügen.

- Ihre bereitgestellte Consumergruppe ist für Time Series Insights nicht exklusiv.

    Während der Registrierung eines IoT-Hubs oder eines Event Hubs geben Sie die Consumergruppe an, die zum Lesen der Daten verwendet wird. Diese Consumergruppe muss pro Umgebung eindeutig sein. Wenn die Consumergruppe geteilt wird, trennt der zugrunde liegende Event Hub automatisch einen der Leser nach dem Zufallsprinzip. Stellen Sie eine eindeutige Consumergruppe bereit, aus der Time Series Insights lesen soll.

- Ihre Time Series-ID-Eigenschaft, die zum Zeitpunkt der Bereitstellung angegeben wurde, ist falsch, fehlt oder ist null.

    Dieses Problem kann auftreten, wenn die Time Series-ID-Eigenschaft zum Zeitpunkt der Bereitstellung der Umgebung fehlerhaft konfiguriert ist. Weitere Informationen finden Sie unter [Bewährte Methoden für die Auswahl einer Time Series-ID](./how-to-select-tsid.md). Zu diesem Zeitpunkt können Sie eine vorhandene Time Series Insights-Umgebung nicht so aktualisieren, dass sie eine andere Time Series-ID verwendet.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: Einige Daten werden angezeigt, andere hingegen fehlen.

Möglicherweise werden Sie Daten ohne die Time Series-ID.

- Dieses Problem kann auftreten, wenn Sie Ereignisse ohne das Feld „Time Series-ID“ in der Nutzlast senden. Weitere Informationen finden Sie unter [Unterstützte JSON-Formen](./concepts-json-flattening-escaping-rules.md).
- Dieses Problem kann auftreten, weil Ihre Umgebung gedrosselt wird.

    > [!NOTE]
    > Zurzeit unterstützt Time Series Insights eine maximale Datenerfassungsrate von 6 Mbit/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problem: Es wurden Daten angezeigt, aber die Erfassung wurde nun beendet.

- Möglicherweise wurde der Ereignisquellenschlüssel neu generiert, und ihre Gen2-Umgebung benötigt den neuen Ereignisquellenschlüssel.

Dieses Problem tritt auf, wenn der Schlüssel, der beim Erstellen der Ereignisquelle angegeben wurde, nicht mehr gültig ist. In Time Series Insights werden in Ihrem Hub Telemetriedaten, aber keine empfangenen Nachrichten angezeigt. Wenn Sie nicht sicher sind, ob der Schlüssel neu generiert wurde, können Sie das Aktivitätsprotokoll Ihres Event Hubs nach „Namespace-Autorisierungsregeln erstellen oder aktualisieren“ oder „Dient zum Erstellen oder Aktualisieren von IoT Hub-Ressourcen“ für den IoT-Hub durchsuchen.

Um Ihre Time Series Insights Gen2-Umgebung mit dem neuen Schlüssel zu aktualisieren, öffnen Sie die Hubressource im Azure-Portal und kopieren den neuen Schlüssel. Navigieren Sie zu ihrer TSI-Ressource, und klicken Sie auf „Ereignisquellen“.

   [![Screenshot, der die T S I-Ressource mit aufgerufenem Element des Menüs „Ereignisquellen“ zeigt.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Wählen Sie die Ereignisquelle(n) aus, deren Erfassung beendet wurde, fügen Sie den neuen Schlüssel ein, und klicken Sie auf „Speichern“.

   [![Screenshot, der die T S I-Ressource mit eingegebenem I o T Hub-Richtlinienschlüssel zeigt.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: Die Timestamp-Eigenschaft für den Namen meiner Ereignisquelle funktioniert nicht.

Stellen Sie sicher, dass Name und Wert den folgenden Regeln entsprechen:

- Der Name der Timestamp-Eigenschaft beachtet Groß-/Kleinschreibung.
- Der Wert für die Timestamp-Eigenschaft, der als JSON-Zeichenfolge von Ihrer Ereignisquelle eingeht, hat das Format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Ein Beispiel für eine solche Zeichenfolge ist `"2008-04-12T12:53Z"`.

Sie können am einfachsten sicherstellen, dass Ihr Name der Timestamp-Eigenschaft erfasst wird und richtig funktioniert, indem Sie den Time Series Insights Gen2-Explorer verwenden. Wählen Sie im Time Series Insights Gen2-Explorer mithilfe des Diagramms einen Zeitraum aus, nachdem Sie den Namen der Timestamp-Eigenschaft angegeben haben. Klicken Sie mit der rechten Maustaste auf die Auswahl, und wählen Sie die Option **Ereignisse untersuchen** aus. Die erste Spaltenüberschrift ist der Name Ihrer Timestamp-Eigenschaft. Er sollte neben dem Wort `Timestamp` die Zeichenfolge `($ts)` aufweisen, statt:

- `(abc)`, was anzeigt, dass Time Series Insights die Datenwerte als Zeichenfolgen liest.
- Das **Kalender** symbol, das anzeigt, dass Time Series Insights die Datenwerte als Datum/Uhrzeit-Wert liest.
- `#`, was anzeigt, dass Time Series Insights die Datenwerte als ganze Zahl liest.

Wenn die Timestamp-Eigenschaft nicht explizit angegeben ist, wird der Zeitpunkt der Einreihung eine Ereignisses in die Warteschlange für einen IoT-Hub oder Event Hub als Standardzeitstempel verwendet.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problem: Ich kann keine Daten aus meinem warmen Speicher im Explorer anzeigen.

- Möglicherweise haben Sie Ihren warmen Speicher erst vor Kurzem bereitgestellt, und die Daten werden noch übertragen.
- Möglicherweise haben Sie Ihren warmen Speicher gelöscht. in diesem Fall wären die Daten verloren.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: Ich kann mein Zeitreihenmodell nicht anzeigen oder bearbeiten.

- Möglicherweise greifen Sie auf eine Time Series Insights S1- oder S2-Umgebung zu.

   Zeitreihenmodelle werden nur in Umgebungen mit nutzungsbasierter Bezahlung unterstützt. Weitere Informationen zum Zugriff auf Ihre S1- oder S2-Umgebung über den Time Series Insights Gen2-Explorer finden Sie unter [Visualisieren von Daten im Explorer](./concepts-ux-panels.md).

   [![Keine Ereignisse in der Umgebung](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Möglicherweise besitzen Sie keine Berechtigungen zum Anzeigen und Bearbeiten des Modells.

   Benutzer benötigen Zugriff auf Mitwirkender-Ebene, um ihre Zeitreihenmodelle zu bearbeiten und anzuzeigen. Um die aktuellen Zugriffsebenen zu überprüfen und zusätzlichen Zugriff zu gewähren, wechseln Sie zum Abschnitt **Datenzugriffsrichtlinien** in Ihrer Time Series Insights-Ressource im Azure-Portal.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Problem: Bei allen meinen Instanzen im Gen2-Explorer fehlt ein übergeordnetes Element.

Dieses Problem kann auftreten, wenn in Ihrer Umgebung keine Zeitreihenmodell-Hierarchie definiert ist. Weitere Informationen finden Sie unter [Arbeiten mit Zeitreihenmodellen](./time-series-insights-overview.md).

  [![Bei Instanzen ohne übergeordnetes Element wird eine Warnung angezeigt.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="problem-power-bi-connector-shows-unable-to-connect"></a>Problem: Der Power BI-Connector zeigt „Es kann keine Verbindung hergestellt werden“ an.

Dieses Problem kann auftreten, wenn Sie nicht die neueste Version des Power BI-Connectors in Power BI Desktop in verwenden.

[![Screenshot, der das Dialogfeld „Es kann keine Verbindung hergestellt werden“ zeigt.](media/preview-troubleshoot/power-bi-unable-to-connect.png)](media/preview-troubleshoot/power-bi-unable-to-connect.png#lightbox)

- Überprüfen Sie die Version Ihres Power BI Desktops, und stellen Sie sicher, dass Sie die Version von Juli 2020 verwenden. Falls nicht, aktualisieren Sie Ihren Power BI Desktop, und führen Sie den Connector erneut aus.

## <a name="next-steps"></a>Nächste Schritte

- Informationen finden Sie unter [Arbeiten mit Zeitreihenmodellen](./time-series-insights-overview.md).

- Weitere Informationen zu [unterstützten JSON-Formen](./concepts-json-flattening-escaping-rules.md).

- Lesen Sie [Planung und Limits](./how-to-plan-your-environment.md) in Azure Time Series Insights Gen2.