---
title: Analysieren unerwarteter Azure-Gebühren
description: Erfahren Sie, wie Sie unerwartete Gebühren für Ihr Azure-Abonnement analysieren.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6d5198f47b6089730ff345eb12ab522fc5ce58a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575384"
---
# <a name="analyze-unexpected-charges"></a>Analysieren unerwarteter Gebühren

Die Cloudressourceninfrastruktur, die Sie für Ihre Organisation aufgebaut haben, ist wahrscheinlich komplex. Für viele Azure-Ressourcentypen können unterschiedliche Arten von Gebühren anfallen. Die Zuständigkeit für Azure-Ressourcen ist möglicherweise auf verschiedene Teams in Ihrer Organisation verteilt, und für unterschiedliche Ressourcen werden unter Umständen verschiedene Arten von Abrechnungsmodellen verwendet. Beginnen Sie Ihre Analyse mit einzelnen oder mehreren Strategien aus den folgenden Abschnitten, um die Gebühren besser zu verstehen.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Überprüfen der Rechnung auf die für die Gebühr verantwortliche Ressource

Die Art des Erwerbs Ihrer Azure-Dienste hilft Ihnen bei der Bestimmung der Methodik und Tools, die Ihnen bei der Ermittlung der mit einer Gebühr zusammenhängenden Ressource zur Verfügung stehen. [Ermitteln Sie zunächst Ihren Azure-Angebotstyp](../costs/understand-cost-mgt-data.md#determine-your-offer-type), um die passende Methodik zu bestimmen. Ermitteln Sie anschließend anhand der Liste der [unterstützten Azure-Angebote](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers) Ihre Kundenkategorie.

Die folgenden Artikel enthalten ausführliche Schritte zur Überprüfung Ihrer Rechnung auf der Grundlage Ihres Kundentyps. Die Artikel enthalten jeweils eine Anleitung zum Herunterladen einer CSV-Datei mit Nutzungs- und Kostendetails für einen bestimmten Abrechnungszeitraum.

- [Überprüfungsprozess für Rechnungen mit nutzungsbasierter Zahlung](review-individual-bill.md#charges)
- [Überprüfungsprozess für Enterprise Agreement-Rechnungen](review-enterprise-agreement-bill.md)
- [Überprüfungsprozess für MCA-Rechnungen (Microsoft Customer Agreement, Microsoft-Kundenvereinbarung)](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Überprüfungsprozess für MPA-Rechnungen (Microsoft Partner Agreement, Microsoft Partner-Vereinbarung)](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

In Ihrer Azure-Rechnung werden Gebühren für den Monat auf der Grundlage von _Verbrauchseinheiten_ aggregiert. Verbrauchseinheiten dienen zur Nachverfolgung der Nutzung einer Ressource im Laufe der Zeit und werden zur Berechnung Ihrer Rechnung verwendet. Wenn Sie eine einzelne Azure-Ressource (etwa einen virtuellen Computer) erstellen, wird für die Ressource mindestens eine Verbrauchseinheit erstellt.

Filtern Sie die CSV-Nutzungsdatei basierend auf dem Namen der in der Rechnung enthaltenen Verbrauchseinheit (_MeterName_), die Sie analysieren möchten, um alle Positionen anzuzeigen, die die Verbrauchseinheit betreffen. Die Instanz-ID (_InstanceID_) für die Position entspricht der eigentlichen Azure-Ressource, auf die die Gebühr zurückzuführen ist.

Wenn Sie die betreffende Ressource ermittelt haben, können Sie die ressourcenbezogenen Kosten mithilfe der Kostenanalyse in Azure Cost Management weiter analysieren. Weitere Informationen zur Verwendung der Kostenanalyse finden Sie unter [Schnellstart: Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Überprüfen der berechneten Gebühren in der Kostenanalyse

Navigieren Sie zum Anzeigen der Rechnungsdetails im Azure-Portal zur Kostenanalyse für den Bereich, der der von Ihnen analysierten Rechnung zugeordnet ist. Wählen Sie die Ansicht **Rechnungsdetails** aus. In den Rechnungsdetails werden die Gebühren wie auf der Rechnung angezeigt.

[![Beispiel: Anzeigen der Rechnungsdetails](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

Wenn Sie die Rechnungsdetails anzeigen, können Sie den Dienst identifizieren, der unerwartete Kosten verursacht, und ermitteln, welche Ressourcen in der Kostenanalyse direkt mit der Ressource verknüpft sind. Wenn Sie beispielsweise die Gebühren für den Virtual Machines-Dienst analysieren möchten, navigieren Sie zur Ansicht **Kumulierte Kosten**. Legen Sie dann die Granularität auf **Täglich** fest, filtern Sie nach Gebühren für **Dienstname: Virtual Machines**, und gruppieren Sie die Gebühren nach **Ressource**.

[![Beispiel für die akkumulierten Kosten für Virtual Machines](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identifizieren von Kostenspitzen im Zeitverlauf

Manchmal wissen Sie unter Umständen nicht, welche kürzlich angefallenen Kosten zu Änderungen an den abgerechneten Gebühren geführt haben. In diesem Fall können Sie mithilfe der Kostenanalyse [eine tages- oder monatsbasierte Aufschlüsselung der Kosten im Zeitverlauf anzeigen](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month), um zu ermitteln, was sich geändert hat. Gruppieren Sie die Gebühren nach Erstellung der Ansicht entweder nach **Dienst** oder nach **Ressource**, um die Änderungen zu ermitteln. Zur besseren Visualisierung der Daten kann die Ansicht auch als **Liniendiagramm** dargestellt werden.

![Beispiel für Kosten im Zeitverlauf in der Kostenanalyse](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Ermitteln der Ressourcenpreise und des Abrechnungsmodells

Für eine einzelne Ressource können Gebühren in mehreren Azure-Produkten und -Diensten anfallen. Weitere Informationen zu den Preisen für die einzelnen Azure-Dienste finden Sie auf der Seite [Preisangaben nach Produkten](https://azure.microsoft.com/pricing/#product-pricing). Für einen einzelnen, in Azure erstellten virtuellen Computer können zur Nachverfolgung der Nutzung beispielsweise die folgenden Verbrauchseinheiten erstellt werden. Dabei können jeweils unterschiedliche Preise gelten.

- Computestunden
- IP-Adressstunden
- Eingehende Datenübertragung
- Ausgehende Datenübertragung
- Verwalteter Datenträger Standard
- Managed Disks Standard-Vorgänge
- Standard-E/A – Datenträger
- Standard-E/A – Blockblob-Lesevorgangseinheiten
- Standard-E/A – Blockblob-Schreibvorgangseinheiten
- Standard-E/A – Blockblob-Löschvorgangseinheiten

Nachdem der virtuelle Computer erstellt wurde, beginnen diese Verbrauchseinheiten mit der Ausgabe von Nutzungsdatensätzen. Die Nutzung und der Preis der Verbrauchseinheit werden im Azure-System zur Messung von Verbrauchseinheiten nachverfolgt. Die zur Berechnung Ihrer Rechnung verwendeten Verbrauchseinheiten werden in der CSV-Nutzungsdatei angezeigt.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Ermitteln und Einbeziehen der für die Ressource zuständigen Personen

Das für eine bestimmte Ressource zuständige Team weiß häufig über Änderungen Bescheid, die für eine Ressource vorgenommen wurden. Daher ist es hilfreich, das Team in die Ermittlung der Ursache für entstandene Gebühren einzubeziehen. So kann es beispielsweise sein, dass das zuständige Team die Ressource vor Kurzem erstellt, die zugehörige SKU aktualisiert (und somit den Ressourcensatz geändert) oder aufgrund von Codeänderungen die Last für die Ressource erhöht hat. Die folgenden Abschnitte enthalten weitere Techniken zur Bestimmung des Ressourcenbesitzers.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Analysieren der Überwachungsprotokolle für die Ressource

Wenn Sie über Berechtigungen zum Anzeigen einer Ressource verfügen, sollten Sie auf die zugehörigen Überwachungsprotokolle zugreifen können. Ermitteln Sie anhand der Protokolle den Benutzer, der für die neuesten Änderungen an einer Ressource verantwortlich ist. Weitere Informationen finden Sie unter [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../../azure-monitor/essentials/activity-log.md#view-the-activity-log).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analysieren von Benutzerberechtigungen für den übergeordneten Bereich der Ressource

Personen mit Schreibzugriff auf ein Abonnement oder eine Ressourcengruppe verfügen in der Regel über Informationen zu den erstellten Ressourcen. Sie sollten den Zweck einer Ressource erklären oder Sie an die Person verweisen können, die über die entsprechenden Informationen verfügt. Informationen zum Identifizieren der Personen mit Berechtigungen für einen Abonnementbereich finden Sie unter [Überprüfen des Zugriffs für einen Benutzer auf Azure-Ressourcen](../../role-based-access-control/check-access.md). Für Ressourcengruppen kann ein ähnlicher Prozess verwendet werden.

## <a name="get-help-to-identify-charges"></a>Anfordern von Unterstützung bei der Identifizierung von Gebühren

Sollten Sie eine Gebühr trotz der obigen Strategien nicht nachvollziehen können oder anderweitige Unterstützung bei Abrechnungsproblemen benötigen, können Sie [eine Supportanfrage erstellen](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum [Optimieren der Cloudinvestitionen mit Azure Cost Management](../costs/cost-mgt-best-practices.md)