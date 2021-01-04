---
title: Grundlegendes zur Nutzung von Azure-Reservierungen für Enterprise Agreements
description: In diesem Artikel erfahren Sie, wie die Azure-Reservierung für den Konzernbeitritt angewendet wird.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: banders
ms.openlocfilehash: 874d5cb022a38b172bb37009bd86b5e6988f3204
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545603"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Abrufen von Reservierungskosten und Nutzung laut Enterprise Agreement

Reservierungskosten und Verwendungsdaten stehen für Enterprise Agreement-Kunden im Azure-Portal und in den REST-APIs zur Verfügung. Dieser Artikel hilft Ihnen bei Folgendem:

- Abrufen von Reservierungseinkaufsdaten
- Ermitteln des Abonnements bzw. der Ressourcengruppe oder Ressource, von dem bzw. von der die Reservierung verwendet wurde
- Verbrauchsbasierte Kostenzuteilung für Reservierungsnutzung
- Berechnen von Reservierungseinsparungen
- Abrufen von Daten zur Unterauslastung von Reservierungen
- Amortisieren von Reservierungskosten

Marketplace-Gebühren sind in Nutzungsdaten konsolidiert. Sie zeigen Gebühren für die Erstverwendernutzung, Marketplace-Nutzung und Käufe aus einer einzelnen Datenquelle an.

## <a name="reservation-charges-in-azure-usage-data"></a>Reservierungsgebühren in Azure-Nutzungsdaten

Daten werden in zwei gesonderte Datasets unterteilt: _Ist-Kosten_ und _Amortisierte Kosten_. Unterschiede zwischen diesen beiden Datasets:

**Ist-Kosten**: Bieten Daten für die Abstimmung mit Ihrer monatlichen Rechnung. Diese Daten enthalten Details zu den Kosten für den Reservierungserwerb sowie zur Reservierungsanwendung. Anhand dieser Daten können Sie ermitteln, welches Abonnement bzw. welche Ressourcengruppe oder Ressource den Reservierungsrabatt an einem bestimmten Tag erhalten hat. Der effektive Preis für die Nutzung mit dem Reservierungsrabatt ist Null.

**Amortisierte Kosten:** Dieses Dataset ähnelt dem Dataset für die tatsächlichen Kosten – mit dem Unterschied, dass es sich beim effektiven Preis für die Nutzung mit dem Reservierungsrabatt um die anteiligen Kosten der Reservierung handelt (und der Preis somit nicht Null ist). Dadurch können Sie den monetären Wert der Reservierungsnutzung nach Abonnement, Ressourcengruppe oder Ressource ermitteln und die Reservierungsnutzung bei Bedarf intern verrechnen. Das Dataset enthält außerdem nicht in Anspruch genommene Reservierungsstunden. Das Dataset enthält keine Datensätze für den Reservierungserwerb.

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

[Felddefinition anzeigen](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Abrufen von Azure-Verbrauchs- und Reservierungsnutzungsdaten mittels API

Sie können die Daten mithilfe der API abrufen oder aus dem Azure-Portal herunterladen.

Sie rufen die [Nutzungsdetails-API](/rest/api/consumption/usagedetails/list) auf, um die neuen Daten zu erhalten. Detailinformationen zur Terminologie finden Sie unter [Nutzungsbedingungen](../understand/understand-usage.md). Der Aufrufer sollte ein Unternehmensadministrator für das Enterprise Agreement sein, der das [EA-Portal](https://ea.azure.com) verwendet. Unternehmensadministratoren mit „Nur Lesen“-Berechtigung können die Daten auch abrufen.

Beachten Sie, dass diese Daten in [Berichterstellungs-APIs für Unternehmenskunden – Verwendungsdetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) nicht verfügbar sind.

Hier sehen Sie einen Beispielaufruf der Nutzungsdetails-API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Weitere Informationen zu {enrollmentId} und {billingPeriodId} finden Sie im API-Artikel [Nutzungsdetails – Liste](/rest/api/consumption/usagedetails/list).

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

Wenn Sie ein EA-Administrator sind, können Sie die CSV-Datei aus dem Azure-Portal herunterladen, die neue Nutzungsdaten enthält. Diese Daten sind über das EA-Portal (ea.azure.com) nicht verfügbar. Sie müssen die Nutzungsdatei vom Azure-Portal (portal.azure.com) herunterladen, um die neuen Daten anzuzeigen.

Navigieren Sie im Azure-Portal zu [Kostenverwaltung + Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Wählen sie das Abrechnungskonto aus.
2. Klicken Sie auf **Nutzung + Gebühren**.
3. Klicken Sie auf **Download**.  
![Ein Beispiel, das zeigt, wo Sie die CSV-Datei mit den Nutzungsdaten im Azure-Portal herunterladen können](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
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
- Resource group
- `Tags`
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>Abrufen der gemischten Rate für die verbrauchsbasierte Kostenzuteilung

Um die gemischte Rate zu bestimmen, rufen Sie die amortisierten Kostendaten ab, und aggregieren Sie die Gesamtkosten. Für virtuelle Computer können Sie entweder MeterName- oder ServiceType-Informationen aus den AdditionalInfo-JSON-Daten verwenden. Teilen Sie die Gesamtkosten durch die verwendete Menge, um die gemischte Rate zu erhalten.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Überwachen der optimalen Reservierung für Flexibilität bei der Instanzgröße

Multiplizieren Sie die Menge mit der _RINormalizationRatio_ aus AdditionalInfo. Die Ergebnisse zeigen an, wie viele Stunden der Reservierungsnutzung auf den Verwendungseintrag angewendet wurden.

### <a name="determine-reservation-savings"></a>Bestimmen von Reservierungseinsparungen

Rufen Sie die amortisierten Kostendaten ab, und filtern Sie die Daten nach einer reservierten Instanz. Führen Sie dann folgende Schritte aus:

1. Abrufen der geschätzten Kosten der nutzungsbasierten Bezahlung. Multiplizieren Sie den _UnitPrice_-Wert mit _Quantity_-Werten (Menge), um die geschätzten Kosten der nutzungsbasierten Bezahlung zu erhalten, falls kein Reservierungsrabatt auf die Nutzung angewendet wurde.
2. Abrufen der Reservierungskosten. Addieren Sie die _Cost_-Werte (Kosten), um den finanziellen Gegenwert dessen zu erhalten, was Sie für die reservierte Instanz bezahlt haben. Dieser enthält die verwendeten und nicht verwendeten Kosten der Reservierung.
3. Subtrahieren Sie die Reservierungskosten von den geschätzten Kosten der nutzungsbasierten Bezahlung, um die geschätzten Einsparungen zu erhalten.

Beachten Sie Folgendes: Wenn Sie über eine nicht ausgelastete Reservierung verfügen, wird der Eintrag _UnusedReservation_ für _ChargeType_ zu einem Faktor, der berücksichtigt werden muss. Wenn Sie über eine vollständig ausgelastete Reservierung verfügen, erhalten Sie die maximal möglichen Einsparungen. Jegliche ungenutzte Reservierung (_UnusedReservation_) führt dazu, dass weniger Einsparungen erzielt werden.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Reservierungseinkäufe und Amortisierung in der Kostenanalyse

Reservierungskosten sind in der [Kostenanalyse](https://aka.ms/costanalysis) verfügbar. In der Standardeinstellung werden in der Kostenanalyse **tatsächliche Kosten** angezeigt, die auch in Ihrer Abrechnung ausgewiesen werden. Um Reservierungseinkäufe aufgeschlüsselt und nach den Ressourcen anzuzeigen, welche den Nutzen in Anspruch genommen haben, wechseln Sie zu **Amortisierte Kosten**:

![Ein Beispiel, das zeigt, wo Sie in der Kostenanalyse amortisierte Kosten auswählen können](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Gruppieren Sie nach Gebührentyp, um eine Aufschlüsselung von Verbrauch, Einkäufen und Erstattungen aufzurufen; Sie können auch nach Reservierung gruppieren, um eine Aufschlüsselung von Reservierungskosten und Kosten für die bedarfsgesteuerte Bereitstellung zu erhalten. Beachten Sie, dass bei der Anzeige von tatsächlichen Kosten die einzigen aufgeführten Reservierungskosten Einkäufe sind; bei der Anzeige von amortisierten Kosten werden jedoch die Kosten den einzelnen Ressourcen zugeordnet, die den Nutzen in Anspruch genommen haben. Zudem wird bei der Anzeige von amortisierten Kosten der neue Gebührentyp **UnusedReservation** aufgeführt.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../../azure-sql/database/reserved-capacity-overview.md)
- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](../manage/understand-vm-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](reserved-instance-windows-software-costs.md)