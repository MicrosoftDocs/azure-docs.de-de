---
title: Überwachen der Integrität ihrer Datenconnectors mit dieser Azure Sentinel-Arbeitsmappe | Microsoft-Dokumentation
description: Verwenden Sie die Arbeitsmappe zur Überwachung der Integrität, um die Konnektivität und Leistung Ihrer Datenconnectors nachzuverfolgen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656989"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Überwachen der Integrität ihrer Datenconnectors mit dieser Azure Sentinel-Arbeitsmappe

Mithilfe der **Arbeitsmappe zur Überwachung der Integrität von Datenconnectors** können Sie die Integrität, Konnektivität und Leistung Ihrer Datenconnectors aus Azure Sentinel heraus nachverfolgen. Die Arbeitsmappe bietet zusätzliche Monitore, erkennt Anomalien und gibt Aufschluss über den Datenerfassungsstatus des Arbeitsbereichs. Sie können die Logik der Arbeitsmappe verwenden, um die allgemeine Integrität der erfassten Daten zu überwachen und benutzerdefinierte Ansichten und regelbasierte Warnungen zu erstellen.

## <a name="use-the-health-monitoring-workbook"></a>Verwenden der Arbeitsmappe zur Überwachung der Integrität

1. Wählen Sie im Azure Sentinel-Portal im Menü **Bedrohungsmanagement** die Option **Arbeitsmappen** aus.

1. Geben Sie im **Arbeitsmappenkatalog** in der Suchleiste *Integrität* ein, und wählen Sie in den Ergebnissen **Überwachung der Datenerfassungsintegrität** aus.

1. Wählen Sie **Vorlage anzeigen** aus, um die Arbeitsmappe unverändert zu verwenden, oder wählen Sie **Speichern** aus, um eine bearbeitbare Kopie der Arbeitsmappe zu erstellen. Wenn die Kopie erstellt wurde, wählen Sie **Gespeicherte Arbeitsmappe anzeigen** aus.

1. Wählen Sie in der Arbeitsmappe zunächst das **Abonnement** und den **Arbeitsbereich**, den Sie anzeigen möchten, und definieren Sie dann den **Zeitbereich**, um die Daten gemäß Ihren Anforderungen zu filtern. Verwenden Sie die Umschaltfläche **Hilfe anzeigen**, um eine direkte Erläuterung der Arbeitsmappe anzuzeigen.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="Landing Page der Arbeitsmappe zur Überwachung der Integrität von Datenconnectors" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Diese Arbeitsmappe enthält drei Registerkartenabschnitte:

1. Auf der Registerkarte **Übersicht** wird der allgemeine Status der Datenerfassung im ausgewählten Arbeitsbereich angezeigt: Volumemeasures, EPS-Raten und die Zeit des letzten Protokollempfangs.

1. Mithilfe der Registerkarte **Datensammlung: Anomalien** können Sie Anomalien im Datensammlungsprozess nach Tabelle und Datenquelle erkennen. Auf den einzelnen Registerkarten werden Anomalien für eine bestimmte Tabelle angezeigt (die Registerkarte **Allgemein** enthält eine Sammlung von Tabellen). Die Anomalien werden mit der Funktion **series_decompose_anomalies()** berechnet, bei der eine **Anomaliebewertung** zurückgegeben wird. Lesen Sie die [weiteren Informationen zu dieser Funktion](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Legen Sie die folgenden Parameter für die auszuwertende Funktion fest:

    - **AnomaliesTimeRange**: Diese Zeitauswahl gilt nur für die Ansicht mit den Anomalien der Datensammlung.
    - **SampleInterval**: Das Zeitintervall, in dem für Daten im angegebenen Zeitbereich Stichproben genommen werden. Die Anomaliebewertung wird nur anhand der Daten des letzten Intervalls berechnet.
    - **PositiveAlertThreshold**: Mit diesem Wert wird der positive Schwellenwert für die Anomaliebewertung definiert. Akzeptiert werden Dezimalwerte.
    - **NegativeAlertThreshold**: Mit diesem Wert wird der negative Schwellenwert für die Anomaliebewertung definiert. Akzeptiert werden Dezimalwerte.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="Seite „Anomalien“ der Arbeitsmappe zur Überwachung der Integrität von Datenconnectors" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. Auf der Registerkarte **Informationen zum Agent** werden Informationen zur Integrität der Log Analytics-Agents angezeigt, die auf Ihren verschiedenen Computern installiert sind. Dabei kann es sich um Azure-VMs, andere Cloud-VMs, lokale VMs oder physische Computer handeln. Folgendes kann überwacht werden:

   - Systemstandort

   - Heartbeatstatus und Latenz

   - Verfügbarer Arbeitsspeicher und Speicherplatz

   - Agentvorgänge

    In diesem Abschnitt müssen Sie die Registerkarte auswählen, die der Umgebung Ihrer Computer entspricht: Wählen Sie die Registerkarte **Von Azure verwaltete Computer** aus, wenn Sie nur die mit Azure Arc verwaltete Computer anzeigen möchten. Wählen Sie die Registerkarte **Alle Computer** aus, um sowohl verwaltete als auch Nicht-Azure-Computer mit installiertem Log Analytics-Agent anzuzeigen.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="Seite „Informationen zum Agent“ der Arbeitsmappe zur Überwachung der Integrität von Datenconnectors" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich, wie Sie [Ihre Daten in Azure Sentinel integrieren](quickstart-onboard.md), [Datenquellen verbinden](connect-data-sources.md) und [Einblicke in Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).