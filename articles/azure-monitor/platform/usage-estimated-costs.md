---
title: Überwachen der Nutzung und geschätzten Kosten in Azure Monitor
description: Übersicht über die Verwendung der Azure Monitor-Seite für die Nutzung und geschätzten Kosten
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: eb96537f67c61fb31759da020068f784d0e89993
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020193"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Überwachen der Nutzung und geschätzten Kosten in Azure Monitor

> [!NOTE]
> In diesem Artikel wird beschrieben, wie die Nutzung und geschätzten Kosten für mehrere Azure-Überwachungsfunktionen angezeigt werden. Nachfolgend sind einige verwandte Artikel für bestimmte Komponenten von Azure Monitor aufgeführt:
> - [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](manage-cost-storage.md) beschreibt, wie Sie Ihre Kosten durch Ändern der Datenaufbewahrungsdauer steuern sowie Ihre Datennutzung analysieren und Warnungen dazu ausgeben.
> - [Verwalten der Nutzung und der Kosten für Application Insights](../app/pricing.md) beschreibt, wie Sie die Datennutzung in Application Insights analysieren.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor-Preismodell

Das grundlegende Azure Monitor-Abrechnungsmodell ist eine für die Cloud optimierte, nutzungsorientierte Preisgestaltung (nutzungsbasierte Bezahlung). Sie bezahlen nur für die tatsächliche Nutzung. Preisdetails sind für [Metriken, Warnungen, Benachrichtigungen](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) und [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) verfügbar. 

Zusätzlich zum Modell der nutzungsbasierten Bezahlung für Protokolldaten bietet Log Analytics eine Kapazitätsreservierung, mit der Sie Einsparungen von 25 % gegenüber der nutzungsbasierten Bezahlung erzielen können. Die Preise der Kapazitätsreservierung ermöglichen Ihnen den Kauf einer Reservierung ab 100 GB/Tag. Jeder über die Reservierung hinausgehende Verbrauch wird entsprechend der nutzungsbasierten Bezahlung berechnet. [Erfahren Sie mehr](https://azure.microsoft.com/pricing/details/monitor/) über Preise der Kapazitätsreservierung.

Einige Kunden haben Zugriff auf [Legacytarife für Log Analytics](./manage-cost-storage.md#legacy-pricing-tiers) und den [älteren Enterprise-Tarif für Application Insights](../app/pricing.md#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Informationen zu Ihren Azure Monitor-Kosten

Es gibt zwei Phasen, in denen die Kosten zu berücksichtigen sind. Die erste Phase ist, wenn Sie Azure Monitor als Überwachungslösung in Betracht ist. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Schätzen der Kosten für die Verwaltung Ihrer Umgebung

Wenn Sie noch keine Azure Monitor-Protokolle verwenden, können Sie mit dem [Azure Monitor-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=monitor) die Kosten für die Nutzung von Azure Monitor schätzen. Geben Sie „Azure Monitor“ im Suchfeld ein, und klicken Sie auf die ausgegebene Azure Monitor-Kachel. Scrollen Sie auf der Seite nach unten bis zu „Azure Monitor“, und wählen Sie eine der Optionen in der Dropdownliste „Typ“ aus:

- Metrikabfragen und Warnungen  
- Log Analytics
- Application Insights

Der Preisrechner bietet Ihnen jeweils Hilfe beim Schätzen Ihrer wahrscheinlichen Kosten auf Grundlage der erwarteten Nutzung.

Für Log Analytics können Sie z.B. die Anzahl der VMs sowie das Datenvolumen (in GB) eingeben, dessen Erfassung durch die einzelnen VMs erwartet wird. Von einer typischen Azure-VM werden in der Regel 1 bis 3 GB Daten pro Monat erfasst. Wenn Sie bereits Azure Monitor-Protokolle auswerten, können Sie die Datenstatistiken aus Ihrer eigenen Umgebung verwenden. Unten wird erläutert, wie die [Anzahl der überwachten VMs](./manage-cost-storage.md#understanding-nodes-sending-data) und das [erfasste Datenvolumen im Arbeitsbereich](./manage-cost-storage.md#understanding-ingested-data-volume) bestimmt werden.

Ähnlich ist es bei Application Insights: Wenn Sie „Schätzung des Datenvolumens anhand der Anwendungsaktivität“ aktivieren, können Sie Angaben zu Ihrer Anwendung machen (Anforderungen pro Monat und Seitenaufrufe pro Monat, falls Sie clientseitige Telemetriedaten sammeln), und der Rechner teilt Ihnen dann den Medianwert und das 90. Percentil der von ähnlichen Anwendungen gesammelten Datenmenge mit. Diese Anwendungen umfassen die gesamte Bandbreite an Application Insights-Konfigurationen (z.B. weisen einige die standardmäßige Stichprobenerstellung auf, einige keine Stichprobenerstellung usw.), sodass Sie bei der Stichprobenerstellung immer noch die Menge der erfassten Daten auf einen Wert weit unterhalb des Medianwerts reduzieren können. Dies ist jedoch ein Ausgangspunkt, um die Erfahrungen anderer, ähnlicher Kunden zu verstehen. [Erfahren Sie mehr](../app/pricing.md#estimating-the-costs-to-manage-your-application) über das Schätzen der Kosten für Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Informationen zur Nutzung und geschätzten Kosten

Es ist wichtig, die Nutzung von Azure Monitor zu verstehen und nachzuverfolgen, und es stehen zahlreiche Tools dafür bereit. 

Azure bietet im Hub [Azure Cost Management und Abrechnung](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) eine Vielzahl nützlicher Funktionen. Nachdem Sie den Hub **Azure Cost Management und Abrechnung** geöffnet haben, klicken Sie auf **Cost Management**, und wählen [Bereich](../../cost-management-billing/costs/understand-work-scopes.md) aus (die Gruppe von Ressourcen, die untersucht werden soll). 

Um die Azure Monitor-Kosten der letzten 30 Tage anzuzeigen, klicken Sie auf die Kachel **Kosten pro Tag**, wählen unter „Relative Datumsangaben“ die Option „Letzte 30 Tage“ aus und fügen einen Filter hinzu, mit dem die Dienstnamen ausgewählt werden:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insight & Analytics

Dies führt zu einer Ansicht, die folgender ähnelt:

![Screenshot von Azure Cost Management](./media/usage-estimated-costs/010.png)

Von dieser Übersicht der akkumulierten Kosten können Sie einen Drilldown durchführen, um genauere Details in der Ansicht „Kosten nach Ressource“ anzuzeigen. Bei den aktuellen Tarifen werden Azure-Protokolldaten für die gleiche Gruppe von Verbrauchseinheiten abgerechnet, unabhängig davon, ob Sie von Log Analytics oder Application Insights stammt. Um die Kosten für die Nutzung von Log Analytics und Application Insights voneinander zu trennen, können Sie einen Filter für **Ressourcentyp** hinzufügen. Zum Anzeigen aller Kosten für Application Insights setzen Sie den Filter für den Ressourcentyp auf “microsoft.insights/components“ und für die Kosten für Log Analytics auf „microsoft.operationalinsights/workspaces“. 

Weitere Details zur Nutzung erhalten Sie, wenn Sie [Ihre Nutzungsdaten aus dem Azure-Portal herunterladen](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). In der heruntergeladenen Tabelle ist der Verbrauch pro Azure-Ressource pro Tag aufgeführt. In dieser Excel-Tabelle können Sie den Verbrauch für Ihre Application Insights-Ressourcen ermitteln, indem Sie zuerst nach der Spalte „Kategorie der Verbrauchseinheit“ filtern, um „Application Insights“ und „Log Analytics“ anzuzeigen und dann in der Spalte „Instanz-ID“ einen Filter namens „contains microsoft.insights/components“ hinzufügen.  Die meiste Application Insights-Nutzung wird in Verbrauchseinheiten der entsprechenden Kategorie von Log Analytics gemeldet, da für alle Azure Monitor-Komponenten ein einzelnes Protokoll-Back-End vorhanden ist.  Nur Application Insights-Ressourcen zu Legacytarifen und mehrstufige Webtests werden in der Kategorie der Verbrauchseinheit von Application Insights gemeldet.  Der Verbrauch wird in der Spalte „Verbrauchte Menge“ aufgeführt, und die Einheit für jeden Eintrag wird in der Spalte „Maßeinheit“ angegeben.  Es sind weitere Einzelheiten verfügbar, anhand derer Sie [Ihre Microsoft Azure-Rechnung verstehen](../../cost-management-billing/understand/review-individual-bill.md). 

> [!NOTE]
> Die Verwendung von **Cost Management** im Hub **Azure Cost Management und Abrechnung** ist die bevorzugte Methode, um die Überwachungskosten grob zu verstehen.  Die Benutzeroberflächen **Nutzung und geschätzte Kosten** für [Log Analytics](./manage-cost-storage.md#understand-your-usage-and-estimate-costs) und [Application Insights](../app/pricing.md#understand-your-usage-and-estimate-costs) bieten tiefere Einblicke zur jeweiligen Komponente von Azure Monitor.

Eine weitere Möglichkeit zum Anzeigen Ihrer Azure Monitor-Nutzung ist die Seite **Nutzung und geschätzte Kosten** im Monitor-Hub. Hier wird die Nutzung wichtiger Überwachungsfunktionen wie [Metriken, Warnungen, Benachrichtigungen](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) und [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) angezeigt. Für Kunden mit Tarifen, die vor April 2018 verfügbar waren, umfasst dies auch die Log Analytics-Nutzung, die über das Insights- und Analytics-Angebot erworben wurde.

Auf dieser Seite können Benutzer ihre Ressourcennutzung der letzten 31 Tage, aggregiert pro Abonnement, einsehen. `Drill-ins` zeigen die Nutzungstrends über den Zeitraum von 31 Tagen. Für diese Schätzung müssen viele Daten zusammengefasst werden, sodass Sie beim Laden der Seite etwas Geduld aufbringen müssen.

Dieses Beispiel zeigt die Nutzungsüberwachung und eine Schätzung der anfallenden Kosten:

![Screenshot: „Nutzung und geschätzte Kosten“ im Portal](./media/usage-estimated-costs/001.png)

Wählen Sie in der Spalte „Monatliche Nutzung“ den Link, um ein Diagramm mit den Nutzungstrends für die letzten 31 Tage anzuzeigen: 

![Screenshot des Balkendiagramms: „Pro Knoten enthalten“](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Berechtigungen für Operations Management Suite-Abonnements

Kunden, die Microsoft Operations Management Suite E1 und E2 erworben haben, verfügen über Datenerfassungsberechtigungen pro Knoten für [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) und [Application Insights](../app/pricing.md). Um diese Berechtigungen für Log Analytics-Arbeitsbereiche oder Application Insights-Ressourcen in einem bestimmten Abonnement zu erhalten, muss folgende Voraussetzung erfüllt sein: 

- Log Analytics-Arbeitsbereiche sollten den Tarif „Pro-Knoten (OMS)“ verwenden.
- Application Insights-Ressourcen sollten den Tarif „Enterprise“ verwenden.

Je nach Anzahl von Knoten einer Suite, die von Ihrer Organisation erworben wurde, könnte die Umstellung einiger Abonnements auf den Tarif für nutzungsbasierte Bezahlung (pro GB) vorteilhaft sein. Dieser Schritt bedarf aber sorgfältiger Überlegung.

> [!WARNING]
> Wenn Ihre Organisation derzeit über Microsoft Operations Management Suite E1 und E2 verfügt, ist es in der Regel am besten, die Log Analytics Arbeitsbereiche im Tarif „Pro-Knoten (OMS)“ und die Application Insights-Ressourcen im Tarif „Enterprise“ zu belassen. 
>

