---
title: Log Analytics-Integration in Power BI und Excel
description: Hier erfahren Sie, wie Sie Ergebnisse von Log Analytics an Power BI senden.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: a4e2faf87ac3dddf91e6945343a46b02df72db0a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507386"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics-Integration in Power BI

Dieser Artikel konzentriert sich auf Möglichkeiten, Daten aus Log Analytics in Microsoft Power BI zu übertragen, um visuell ansprechendere Berichte und Dashboards zu erstellen. 

## <a name="background"></a>Hintergrund 

Azure Monitor-Protokolle ist eine Plattform, die eine End-to-End-Lösung für das Erfassen von Protokollen bietet. [Azure Monitor Log Analytics](../platform/data-platform.md#) ist die Schnittstelle zum Abfragen dieser Protokolle. Weitere Informationen zur gesamten Azure Monitor-Datenplattform einschließlich Log Analytics finden Sie unter [Azure Monitor-Datenplattform](../platform/data-platform.md). 

Microsoft Power BI ist die Datenvisualisierungsplattform von Microsoft. Weitere Informationen zu den ersten Schritten finden Sie auf der [Homepage von Power BI](https://powerbi.microsoft.com/). 


Im Allgemeinen können Sie kostenlose Power BI-Features verwenden, um visuell ansprechende Berichte und Dashboards zu integrieren und zu erstellen.

Für erweiterte Features ist möglicherweise der Erwerb eines Power BI Pro- oder Premium-Kontos erforderlich. Diese Features umfassen: 
 - Freigeben Ihrer Arbeit 
 - Geplante Aktualisierungen
 - Power BI-Apps 
 - Dataflows und inkrementelle Aktualisierung 

Weitere Informationen finden Sie unter den [Power BI-Preisen und -Features](https://powerbi.microsoft.com/pricing/). 

## <a name="integrating-queries"></a>Integrieren von Abfragen  

Power BI verwendet die [Abfragesprache M](/powerquery-m/power-query-m-language-specification/) als Hauptabfragesprache. 

Log Analytics-Abfragen können nach M exportiert und direkt in Power BI verwendet werden. Nachdem Sie eine erfolgreiche Abfrage ausgeführt haben, wählen Sie auf der Log Analytics-Benutzeroberfläche in der oberen Aktionsleiste im Menü der Schaltfläche **Exportieren** die Option **In Power BI exportieren (M-Abfrage)** aus.

:::image type="content" source="media/log-powerbi-excel/export-query2.png" alt-text="Log Analytics-Abfrage mit Pulldownmenü für Exportoption" border="true":::

Log Analytics erstellt eine TXT-Datei mit dem M-Code, der direkt in Power BI verwendet werden kann.

## <a name="connecting-your-logs-to-a-dataset"></a>Verbinden Ihrer Protokolle mit einem Dataset 

Ein Power BI-Dataset ist eine Datenquelle, die für die Berichterstellung und Visualisierung bereitsteht. Um eine Log Analytics-Abfrage mit einem Dataset zu verbinden, kopieren Sie den aus Log Analytics exportierten M-Code in eine leere Abfrage in Power BI. 

Weitere Informationen finden Sie in der [Übersicht über Power BI-Datasets](/power-bi/service-datasets-understand/). 

## <a name="collect-data-with-power-bi-dataflows"></a>Sammeln von Daten mit Power BI-Dataflows 

Mit Power BI-Dataflows können Sie auch Daten sammeln und speichern. Weitere Informationen finden Sie unter [Power BI-Dataflows](/power-bi/service-dataflows-overview).

Ein Dataflow ist eine Art „Cloud-ETL“, das Ihnen beim Sammeln und Vorbereiten von Daten hilft. Ein Dataset ist das „Modell“, das Sie dabei unterstützt, verschiedene Entitäten zu verbinden und entsprechend Ihren Anforderungen zu modellieren.

## <a name="incremental-refresh"></a>Inkrementelle Aktualisierung 

Sowohl Power BI-Datasets als auch Power BI-Dataflows bieten eine Option für die inkrementelle Aktualisierung. Power BI-Dataflows und Power BI-Datasets unterstützen diese Funktion, doch Sie benötigen Power BI Premium, um sie auch zu verwenden.  


Bei der inkrementellen Aktualisierung werden kleine Abfragen ausgeführt und kleinere Datenmengen pro Ausführung aktualisiert, anstatt beim Ausführen der Abfrage alle Daten immer wieder zu erfassen. Sie haben die Möglichkeit, große Datenmengen zu speichern, fügen aber bei jeder Ausführung der Abfrage ein neues Dateninkrement hinzu. Dieses Verhalten eignet sich optimal für Berichte mit längerer Ausführungszeit.

Die inkrementelle Aktualisierung von Power BI basiert auf dem Vorhandensein einer im Resultset abgelegten *datetime*. Stellen Sie vor dem Konfigurieren der inkrementellen Aktualisierung sicher, dass das Log Analytics-Abfrageresultset mindestens eine abgelegte *datetime* enthält. 

Weitere Informationen und Anleitungen zum Konfigurieren der inkrementellen Aktualisierung finden Sie unter [Power BI-Datasets und inkrementelle Aktualisierung](/power-bi/service-premium-incremental-refresh) und [Power BI-Dataflows und inkrementelle Aktualisierung](/power-bi/service-dataflows-incremental-refresh).

## <a name="reports-and-dashboards"></a>Berichte und Dashboards

Nachdem die Daten an Power BI gesendet wurden, können Sie mit der Verwendung von Power BI fortfahren, um Berichte und Dashboards zu erstellen.

Weitere Informationen finden Sie in [dieser Anleitung zum Erstellen Ihres ersten Power BI-Modells und -Berichts](/learn/modules/build-your-first-power-bi-report/).  

## <a name="excel-integration"></a>Excel-Integration

Sie können die gleiche M-Integration, die in Power BI verwendet wird, auch zum Integrieren in eine Excel-Tabelle nutzen. Weitere Informationen finden Sie in dieser [Anleitung zum Integrieren in Excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) und das anschließende Einfügen der aus Log Analytics exportierten M-Abfrage.

Zusätzliche Informationen finden Sie unter [integrieren von Log Analytics und Excel](log-excel.md).

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit [Log Analytics-Abfragen](log-query-overview.md)
