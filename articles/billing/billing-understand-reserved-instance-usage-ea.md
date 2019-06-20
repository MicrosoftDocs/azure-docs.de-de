---
title: Grundlegendes zur Nutzung von Azure-Reservierungen für Enterprise Agreements
description: In diesem Artikel erfahren Sie, wie die Azure-Reservierung für den Konzernbeitritt angewendet wird.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: b2452580eaecc0ab922f8e7db48676f70831a8ca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66126842"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Abrufen von Reservierungskosten und Nutzung laut Enterprise Agreement

Reservierungskosten und Verwendungsdaten stehen für Enterprise Agreement-Kunden im Azure-Portal und in den REST-APIs zur Verfügung. Dieser Artikel hilft Ihnen bei Folgendem:

- Abrufen von Reservierungseinkaufsdaten
- Abrufen von Daten zur Unterauslastung von Reservierungen
- Amortisieren von Reservierungskosten
- Verbrauchsbasierte Kostenzuteilung für Reservierungsnutzung
- Berechnen von Reservierungseinsparungen

Marketplace-Gebühren sind in Nutzungsdaten konsolidiert. Sie zeigen Gebühren für die Erstverwendernutzung, Marketplace-Nutzung und Käufe aus einer einzelnen Datenquelle an.

## <a name="reservation-charges-in-azure-usage-data"></a>Reservierungsgebühren in Azure-Nutzungsdaten

Daten werden in zwei gesonderte Datasets unterteilt: _Ist-Kosten_ und _Amortisierte Kosten_. Unterschiede zwischen diesen beiden Datasets:

**Ist-Kosten**: Bieten Daten für die Abstimmung mit Ihrer monatlichen Rechnung. Diese Daten enthalten Reservierungseinkaufskosten. Sie enthalten keinen EffectivePrice für die Nutzung, auf die der Reservierungsrabatt gewährt wurde.

**Amortisierte Kosten**: Die Ressourcen-EffectiveCost, für die der Reservierungsrabatt gewährt wird, sind die anteiligen Kosten der reservierten Instanz. Das Dataset enthält außerdem Kosten für nicht genutzte Reservierung. Die Summe der Reservierungskosten und ungenutzten Reservierung ergeben die täglichen amortisierten Kosten der Reservierung.

Vergleich von zwei Datasets:

| Daten | Dataset „Ist-Kosten“ | Dataset „Amortisierte Kosten“ |
| --- | --- | --- |
| Reservierungseinkäufe | Verfügbar in dieser Ansicht.<br><br>  Um diese Daten zu erhalten, filtern Sie auf ChargeType = &quot;Purchase&quot; (Einkauf). <br><br> Beziehen Sie sich auf die ReservationID oder den ReservationName, um festzustellen, für welche Reservierung die Gebühren anfallen.  | Auf diese Ansicht nicht anwendbar. <br><br> Einkaufskosten sind in den amortisierten Daten nicht enthalten. |
| EffectivePrice | Der Wert ist Null für die Nutzung, die einen Reservierungsrabatt erhält. | Der Wert sind anteilige Kosten pro Stunde für die Reservierung der Nutzung, für die der Reservierungsrabatt gilt. |
| Ungenutzte Reservierung (gibt die Anzahl von Stunden an, die die Reservierung an einem Tag nicht genutzt wurde, sowie den finanziellen Wert der Verschwendung) | In dieser Ansicht nicht anwendbar. | Verfügbar in dieser Ansicht.<br><br> Um diese Daten zu erhalten, filtern Sie auf ChargeType = &quot;UnusedReservation&quot; (ungenutzte Reservierung).<br><br>  Beziehen Sie sich auf die ReservationID oder den ReservationName, um festzustellen, welche Reservierung unterausgelastet war. Dies gibt an, wie viel der Reservierung an dem Tag verschwendet wurde.  |
| UnitPrice (Preis der Ressource gemäß Ihrem Preisblatt) | Verfügbar | Verfügbar |

Weitere in den Azure-Nutzungsdaten verfügbare Informationen haben sich geändert:

- Produkt- und Verbrauchseinheiteninformationen: Azure ersetzt die ursprünglich verbrauchte Verbrauchseinheit nicht durch die ReservationId und den ReservationName, wie es zuvor erfolgte.
- ReservationId und ReservationName: – Diese sind eigene Felder in den Daten. Bisher waren sie nur unter AdditionalInfo verfügbar.
- ProductOrderId: Die Reservierungsauftrags-ID, die als eigenes Feld hinzugefügt wurde.
- ProductOrderName: Der Produktname der gekauften Reservierung.
- Term (Laufzeit): 12 Monate oder 36 Monate.
- RINormalizationRatio: Verfügbar unter AdditionalInfo. Dies ist das Verhältnis, in dem die Reservierung auf den Verwendungseintrag angewendet wird. Wenn Flexibilität der Instanzgröße für Ihre Reservierung aktiviert ist, kann dies auch auf andere Größen gelten. Der Wert zeigt das Verhältnis an, in dem die Reservierung auf den Verwendungseintrag angewendet wurde.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Abrufen von Azure-Verbrauchs- und Reservierungsnutzungsdaten mittels API

Sie können die Daten mithilfe der API abrufen oder aus dem Azure-Portal herunterladen.

Sie rufen die [Nutzungsdetails-API](/rest/api/consumption/usagedetails/list) mit der API-Version &quot;2019-04-01-Preview&quot; auf, um die neuen Daten zu erhalten. Detailinformationen zur Terminologie finden Sie unter [Nutzungsbedingungen](billing-understand-your-usage.md). Der Aufrufer sollte ein Unternehmensadministrator für das Enterprise Agreement sein, der das [EA-Portal](https://ea.azure.com) verwendet. Unternehmensadministratoren mit „Nur Lesen“-Berechtigung können die Daten auch abrufen.

Die Daten sind nicht verfügbar in [Berichterstellungs-APIs für Unternehmenskunden – Verwendungsdetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Hier sehen Sie einen Beispielaufruf der API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Weitere Informationen zu {enrollmentId} und {billingPeriodId} finden Sie im API-Artikel [Nutzungsdetails – Liste](https://docs.microsoft.com/rest/api/consumption/usagedetails/list).

Die Informationen in der folgenden Tabelle zu Metrik und Filter können bei der Behebung häufiger Reservierungsprobleme helfen.

| **Typ der API-Daten** | API-Aufrufaktion |
| --- | --- |
| **Alle Gebühren (Nutzung und Käufe)** | {metric} durch ActualCost ersetzen |
| **Nutzung, für die ein Reservierungsrabatt gewährt wurde** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/reservationId%20ne%20 |
| **Nutzung, für die kein Reservierungsrabatt gewährt wurde** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/reservationId%20eq%20 |
| **Amortisierte Gebühren (Nutzung und Käufe)** | {metric} durch AmortizedCost ersetzen |
| **Bericht über nicht genutzte Reservierungen** | {metric} durch AmortizedCost ersetzen<br><br>{filter} ersetzen durch: properties/ChargeType%20eq%20'UnusedReservation' |
| **Reservierungseinkäufe** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/ChargeType%20eq%20'Purchase'  |
| **Rückerstattungen** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Herunterladen der CSV-Nutzungsdatei mit neuen Daten

Wenn Sie ein EA-Administrator sind, können Sie die CSV-Datei aus dem Azure-Portal herunterladen, die neue Nutzungsdaten enthält. Diese Daten sind nicht im [EA-Portal](https://ea.azure.com) verfügbar.

Navigieren Sie im Azure-Portal zu [Kostenverwaltung + Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Wählen sie das Abrechnungskonto aus.
2. Klicken Sie auf **Nutzung + Gebühren**.
3. Klicken Sie auf **Download**.  
![Ein Beispiel, das zeigt, wo Sie die CSV-Datei mit den Nutzungsdaten im Azure-Portal herunterladen können](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Wählen Sie in **Nutzung und Gebühren herunterladen** unter **Nutzungsdetails Version 2** die Option **Alle Gebühren (Nutzung und Käufe)** aus, und klicken Sie dann auf „Herunterladen“. Wiederholen Sie dies für **Amortisierte Gebühren (Nutzung und Käufe)** .

Die CSV-Dateien, die Sie herunterladen, enthalten Ist-Kosten und amortisierte Kosten.

## <a name="common-cost-and-usage-tasks"></a>Häufige Aufgaben im Zusammenhang mit Kosten und Nutzung

Die folgenden Abschnitte stellen häufige Aufgaben dar, die die meisten Leute verwenden, um ihre Reservierungskosten- und -nutzungsdaten anzuzeigen.

### <a name="get-reservation-purchase-costs"></a>Abrufen von Reservierungseinkaufsdaten

Reservierungseinkaufskosten sin in den Ist-Kostendaten enthalten. Filtern Sie nach _ChargeType = Puchase_ (Einkauf). Beziehen Sie sich auf ProductOrderID, um zu bestimmen, zu welchem Reservierungsauftrag der Einkauf gehört.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Abrufen von Mengen und Kosten für unterausgelastete Reservierungen

Rufen Sie die amortisierten Kostendaten ab, und filtern Sie nach _ChargeType_ _= UnusedReservation_ (ungenutzte Reservierung). Sie erhalten die täglichen Mengen und Kosten für nicht genutzte Reservierung. Sie können die Daten mithilfe der Felder _ReservationId_ und _ProductOrderId_ nach einer Reservierung oder einem Reservierungsauftrag filtern. Wenn eine Reservierung zu 100 % genutzt wurde, enthält der Eintrag die Menge 0.

### <a name="amortize-reservation-costs"></a>Amortisieren von Reservierungskosten

Rufen Sie die amortisierten Kostendaten ab, und filtern Sie nach einem Reservierungsauftrag mithilfe von _ProductOrderID_, um tägliche amortisierte Kosten für eine Reservierung zu erhalten.

### <a name="chargeback-for-a-reservation"></a>Verbrauchsbasierte Kostenzuteilung für eine Reservierung

Sie können eine verbrauchsbasierte Kostenzuteilung für die Reservierungsnutzung zu anderen Organisationen nach Abonnement, Ressourcengruppen oder Tags vornehmen. Amortisierte Kostendaten bieten den finanziellen Gegenwert der Nutzung einer Reservierung für die folgenden Datentypen:

- Ressourcen (z. B. ein virtueller Computer)
- Ressourcengruppe
- `Tags`
- Abonnement

### <a name="get-the-blended-rate-for-chargeback"></a>Abrufen der gemischten Rate für die verbrauchsbasierte Kostenzuteilung

Um die gemischte Rate zu bestimmen, rufen Sie die amortisierten Kostendaten ab, und aggregieren Sie die Gesamtkosten. Für virtuelle Computer können Sie entweder MeterName- oder ServiceType-Informationen aus den AdditionalInfo-JSON-Daten verwenden. Teilen Sie die Gesamtkosten durch die verwendete Menge, um die gemischte Rate zu erhalten.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Überwachen der optimalen Reservierung für Flexibilität bei der Instanzgröße

Multiplizieren Sie die Menge mit der _RINormalizationRatio_ aus AdditionalInfo. Die Ergebnisse zeigen an, wie viele Stunden der Reservierungsnutzung auf den Verwendungseintrag angewendet wurden.

### <a name="determine-reservation-savings"></a>Bestimmen von Reservierungseinsparungen

Rufen Sie die amortisierten Kostendaten ab, und filtern Sie die Daten nach einer reservierten Instanz. Führen Sie dann folgende Schritte aus:

1. Abrufen der geschätzten Kosten der nutzungsbasierten Bezahlung. Multiplizieren Sie den _UnitPrice_-Wert mit _Quantity_-Werten (Menge), um die geschätzten Kosten der nutzungsbasierten Bezahlung zu erhalten, falls kein Reservierungsrabatt auf die Nutzung angewendet wurde.
2. Abrufen der Reservierungskosten. Addieren Sie die _Cost_-Werte (Kosten), um den finanziellen Gegenwert dessen zu erhalten, was Sie für die reservierte Instanz bezahlt haben. Dieser enthält die verwendeten und nicht verwendeten Kosten der Reservierung.
3. Subtrahieren Sie die Reservierungskosten von den geschätzten Kosten der nutzungsbasierten Bezahlung, um die geschätzten Einsparungen zu erhalten.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>Reservierungseinkäufe und Amortisierung in der Azure-Kostenanalyse

Kosten der reservierten Instanz sind im [Vorschaumodus der Azure-Kostenanalyse](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) verfügbar. Standardmäßig zeigt die Kostendatenansicht Ist-Kosten an. Sie können aber zu amortisierten Kosten wechseln. Hier sehen Sie ein Beispiel.

![Ein Beispiel, das zeigt, wo Sie in der Kostenanalyse amortisierte Kosten auswählen können](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Wenden Sie Filter an, um Ihre Gebühren nach Reservierungs- oder Gebührentyp anzuzeigen. Gruppieren Sie nach dem Reservierungsnamen, um die nach Reservierungen aufgeschlüsselten Kosten anzuzeigen.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../sql-database/sql-database-reserved-capacity.md)
- [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md)
- [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](billing-understand-vm-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](billing-reserved-instance-windows-software-costs.md)
