---
title: Arbeitsmappe für Downtime, SLA und Ausfälle – Application Insights
description: Berechnen und melden Sie die Vereinbarung zum Servicelevel für Webtests über eine einzelne zentralisierte Benutzeroberfläche in Ihren Application Insights-Ressourcen und Azure-Abonnements.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: 884d65d10ab0360957360efa8bf9dc4cac59949b
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989186"
---
# <a name="downtime-sla-and-outages-workbook"></a>Arbeitsmappe für Downtime, SLA und Ausfälle

Dies ist eine Einführung in eine einfache Möglichkeit zum Berechnen und Melden der Vereinbarung zum Servicelevel (Service Level Agreement, SLA) für Webtests über eine einzelne zentralisierte Benutzeroberfläche in Ihren Application Insights-Ressourcen und Azure-Abonnements. Der Bericht „Downtime & Outages“ (Downtime und Ausfälle) bietet leistungsfähige vorgefertigte Abfragen und Datenvisualisierungen, mit denen Sie Ihr Wissen über die Konnektivität Ihres Kunden, die typische Antwortzeit einer Anwendung und Downtimes erweitern können.

Sie können über den Arbeitsmappenkatalog in Ihrer Application Insights-Ressource oder über die Registerkarte „Verfügbarkeit“ auf die Vorlage für die SLA-Arbeitsmappe zugreifen, indem Sie oben auf **SLA Report** (SLA-Bericht) klicken.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Screenshot: Registerkarte „Verfügbarkeit“ mit hervorgehobener Option „SLA Report“ (SLA-Bericht)" lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Screenshot: Arbeitsmappenkatalog mit hervorgehobener Arbeitsmappe „Downtime & Outages“ (Downtime und Ausfälle)" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Parameterflexibilität

Die in der Arbeitsmappe festgelegten Parameter beeinflussen den Rest des Berichts.

:::image type="content" source="./media/sla-report/outages.png" alt-text="Screenshot: Registerkarte „Outage/Maintenance Parameters“ (Ausfall- und Wartungsparameter) in der Arbeitsmappe „Downtime & Outages“ (Downtime und Ausfälle)" lightbox ="./media/sla-report/outages.png":::

Die Parameter `Subscriptions`, `App Insights Resources` und `Web Test` legen die Optionen für Ressourcen auf allgemeiner Ebene fest. Diese Parameter basieren auf Log Analytics-Abfragen und werden in jeder Berichtsabfrage verwendet.

`Failure Threshold` und `Outage Window` ermöglichen es Ihnen, Ihre eigenen Kriterien für einen Dienstausfall festzulegen, beispielsweise die Kriterien für eine App Insights-Verfügbarkeitswarnung basierend auf dem Zähler für Standorte, bei denen in einem bestimmten Zeitraum ein Fehler aufgetreten ist. Der typische Schwellenwert beträgt drei Standorte in einem Zeitfenster von fünf Minuten.

Mit `Maintenance Period` können Sie die normale Wartungshäufigkeit auswählen. `Maintenance Window` ist ein DateTime-Selektor für einen Beispielwartungszeitraum. Alle Daten, die während des identifizierten Zeitraums auftreten, werden in den Ergebnissen ignoriert.

`Availability Target 9s` gibt Ihr Ziel in Bezug auf Neunen mit zwei bis fünf Neunen an.

## <a name="overview-page"></a>Seite „Übersicht“

Die Übersichtsseite enthält allgemeine Informationen zur gesamten SLA (mit Ausnahme von Wartungszeiträumen, sofern definiert), zu kompletten Ausfällen und zu Downtimes von Anwendungen. Ausfallinstanzen werden definiert, wenn bei einem Test Fehler auftreten, bis er basierend auf Ihren Ausfallparametern erfolgreich ist. Wenn bei einem Test um 8:00 Uhr ein Fehler auftritt und dieser Test um 10:00 Uhr wieder erfolgreich ausgeführt wird, gilt der gesamte Zeitraum der Daten als derselbe Ausfall.

:::image type="content" source="./media/sla-report/overview.gif" alt-text="GIF der Seite „Übersicht“ mit der nach Webtest sortierten Tabelle „Overview“ (Übersicht)" lightbox="./media/sla-report/overview.gif":::

Sie können auch den längsten Ausfall untersuchen, der im Verlauf des Berichtszeitraums aufgetreten ist.

Für einige Tests können Sie zur weiteren Untersuchung eine Verknüpfung zurück zur Application Insights-Ressource einfügen, aber dies ist nur in einer [arbeitsbereichsbasierten Application Insights-Ressource](create-workspace-resource.md) möglich.

## <a name="downtime-outages-and-failures"></a>Downtime, Ausfälle und Fehler

Auf der Registerkarte **Outages & Downtime** (Ausfälle und Downtime) finden Sie Informationen zu den gesamten Ausfällen und nach Test aufgeschlüsselten gesamten Downtimes. Die Registerkarte **Failures by Location** (Fehler nach Standort) umfasst eine geografische Karte mit den Teststandorten, an denen ein Fehler aufgetreten ist, um Ihnen dabei zu helfen, potenziell problematische Verbindungsbereiche zu identifizieren.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text="GIF der Registerkarte „Outages & Downtime“ (Ausfälle und Downtime) und Registerkarte „Failures by Location“ (Fehler nach Standort) in der Arbeitsmappe „Downtime & Outages“ (Downtime und Ausfälle)" lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Bearbeiten des Berichts

Sie können den Bericht wie alle anderen [Azure Monitor-Arbeitsmappen](../platform/workbooks-overview.md) bearbeiten. Sie können die Abfragen oder Visualisierungen basierend auf den Anforderungen Ihres Teams anpassen.

:::image type="content" source="./media/sla-report/edit.gif" alt-text="GIF zum Klicken auf die Schaltfläche „Bearbeiten“ zum Ändern der Visualisierung in ein Kreisdiagramm" lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

Die Abfragen können alle in [Log Analytics](../log-query/log-analytics-overview.md) ausgeführt und in anderen Berichten oder auf anderen Dashboards verwendet werden. Entfernen Sie die Parametereinschränkung, und verwenden Sie die Core-Abfrage.

:::image type="content" source="./media/sla-report/logs.gif" alt-text="GIF der Protokollabfrage" lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Zugriff und Freigabe

Sie können den Bericht für Ihre Teams und Führungskräfte freigeben oder an ein Dashboard zur weiteren Verwendung anpinnen. Der Benutzer muss über eine Leseberechtigung bzw. den Zugriff auf die Application Insights-Ressource verfügen, in der die eigentliche Arbeitsmappe gespeichert ist.

:::image type="content" source="./media/sla-report/share.png" alt-text="Screenshot: Freigeben der Vorlage" lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie sich die Tipps zum [Optimieren von Protokollabfragen in Azure Monitor](../log-query/query-optimization.md) durch.
- Informieren Sie sich, wie Sie ein [Diagramm in Arbeitsmappen](../platform/workbooks-chart-visualizations.md) erstellen.
- Lesen Sie nach, wie Sie Ihre Website mit [Verfügbarkeitstests](monitor-web-app-availability.md) überwachen.
