---
title: Überprüfen Ihrer individuellen Rechnung für das Azure-Abonnement
description: Hier finden Sie Informationen zum besseren Verständnis Ihrer Rechnung und Ressourcennutzung, und Sie erfahren, wie Sie Gebühren für Ihr individuelles Azure-Abonnement überprüfen, einschließlich der nutzungsbasierten Bezahlung.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: ef04c964f0037fcf36fe376084df75ffa0034957
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027559"
---
# <a name="tutorial-review-your-individual-azure-subscription-bill"></a>Tutorial: Überprüfen Ihrer individuellen Rechnung für das Azure-Abonnement

Dieser Artikel hilft Ihnen beim besseren Verständnis und beim Überprüfen der Rechnung für Ihr Azure-Abonnement mit nutzungsbasierter Bezahlung oder Visual Studio. In der Regel erhalten Sie für jeden Abrechnungszeitraum eine Rechnung per E-Mail. Die Rechnung stellt Ihre Azure-Rechnung dar. Die Kosteninformationen auf der Rechnung sind auch im Azure-Portal verfügbar. In diesem Tutorial vergleichen Sie Ihre Rechnung mit der Datei, die die ausführlichen Angaben zur täglichen Nutzung enthält, sowie mit der Kostenanalyse im Azure-Portal.

Dieses Tutorial ist nur für Azure-Kunden mit einem individuellen Abonnement relevant. Gängige individuelle Abonnements sind Abonnements mit nutzungsbasierter Bezahlung, die direkt über die Azure-Website erworben werden.

Informationen zum besseren Verständnis von unerwarteten Gebühren finden Sie unter [Analysieren unerwarteter Gebühren](analyze-unexpected-charges.md). Wenn Sie Ihr Azure-Abonnement kündigen möchten, finden Sie entsprechende Informationen unter [Kündigen Ihres Azure-Abonnements](../manage/cancel-azure-subscription.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Vergleichen abgerechneter Gebühren mit der Nutzungsdatei
> * Vergleichen von Gebühren und Nutzung in der Kostenanalyse

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein kostenpflichtiges Abrechnungskonto für das *Microsoft Online Services-Programm*. Das Konto wird erstellt, wenn Sie sich über die Azure-Website für Azure registrieren. Dies ist beispielsweise der Fall, wenn Sie über ein Konto mit nutzungsbasierter Bezahlung verfügen oder Visual Studio-Abonnent sind.

Rechnungen für kostenlose Azure-Konten werden nur bei Überschreitung des monatlichen Guthabens erstellt.

Seit dem Abschluss des Azure-Abonnements müssen mehr als 30 Tage vergangen sein. Die Abrechnung von Azure erfolgt am Ende Ihres Rechnungszeitraums.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

- Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="compare-billed-charges-with-your-usage-file"></a>Vergleichen der abgerechneten Gebühren mit Ihrer Nutzungsdatei

<a name="charges"></a>

Für einen Nutzungs- und Kostenvergleich müssen Sie zunächst Ihre Rechnungs- und Nutzungsdateien herunterladen. Die CSV-Datei mit ausführlichen Nutzungsdaten enthält Ihre Gebühren nach Abrechnungszeitraum und Daten zur täglichen Nutzung. Sie enthält keine Steuerinformationen. Zum Herunterladen der Dateien müssen Sie Kontoadministrator sein oder über die Rolle „Besitzer“ verfügen.

Geben Sie im Azure-Portal den Suchbegriff *Abonnements* in das Suchfeld ein, und klicken Sie anschließend auf **Abonnements**.

[![Navigieren zu Abonnements](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

Klicken Sie in der Abonnementliste auf das Abonnement.

Klicken Sie unter **Abrechnung** auf **Rechnungen**.

Suchen Sie in der Rechnungsliste nach der Rechnung, die Sie herunterladen möchten, und klicken Sie auf das Downloadsymbol. Ändern Sie ggf. die Zeitspanne, um ältere Rechnungen anzuzeigen. Es kann einige Minuten dauern, bis die Datei mit den Nutzungsdetails und die Rechnung generiert werden.

![Screenshot mit Abrechnungszeiträumen, der Option „Herunterladen“ und den Gesamtgebühren für jeden Abrechnungszeitraum](./media/review-individual-bill/download-invoice.png)

Klicken Sie im Fenster „Nutzung und Gebühren herunterladen“ auf **CSV herunterladen** und auf **Rechnung herunterladen**.

![Screenshot mit der Option „Rechnung herunterladen“ und Nutzungsseite](./media/review-individual-bill/usageandinvoice.png)

Sollte **Nicht verfügbar** angezeigt werden, gibt es verschiedene Gründe, warum keine Nutzungsdetails angezeigt werden bzw. keine Rechnung angezeigt wird:

- Es sind weniger als 30 Tage seit dem Abschluss Ihres Azure-Abonnements vergangen.
- Für den Abrechnungszeitraum liegt keine Nutzung vor.
- Es wurde noch keine Rechnung generiert. Warten Sie bis zum Ende des Abrechnungszeitraums.
- Sie haben keine Berechtigung zur Ansicht von Rechnungen. Alte Rechnungen werden möglicherweise nur für den Kontoadministrator angezeigt.
- Wenn Sie eine kostenlose Testversion oder eine nicht überschrittene monatliche Gutschrift für Ihr Abonnement verwenden, erhalten Sie nur eine Rechnung, wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben.

Überprüfen Sie als Nächstes die Gebühren. Ihre Rechnung enthält Werte für Steuern und Ihre Nutzungsgebühren.

![Azure-Beispielrechnung](./media/review-individual-bill/invoice-usage-charge.png)

Öffnen Sie die CSV-Nutzungsdatei, die Sie heruntergeladen haben. Addieren Sie am Ende der Datei die Werte aller Elemente in der Spalte *Cost* (Kosten).

![Exemplarische Nutzungsdatei mit addierten Kosten](./media/review-individual-bill/usage-file-usage-charges.png)

 Der addierte Wert für *Cost* (Kosten) muss exakt den *Nutzungsgebühren* aus Ihrer Rechnung entsprechen.

Ihre Nutzungsgebühren werden auf der Ebene der Verbrauchseinheiten angezeigt. Die Bedeutung der folgenden Benennungen ist in der Rechnung und in der Datei mit ausführlichen Nutzungsdaten identisch. Beispielsweise entspricht der Abrechnungszyklus in der Rechnung dem Abrechnungszeitraum in der Datei mit detaillierten Nutzungsdaten.

| Rechnung (PDF) | Detaillierte Nutzungsdaten (CSV)|
| --- | --- |
|Billing Cycle | BillingPeriodStartDate BillingPeriodEndDate |
|Name |Meter Category |
|type |Meter Subcategory |
|Resource |MeterName |
|Region |MeterRegion |
|Consumed | Menge |
|Enthalten |Included Quantity |
|Billable |Overage Quantity |
|Rate | EffectivePrice|
| Wert | Kosten |

Der Abschnitt **Nutzungsgebühren** Ihrer Rechnung enthält den Gesamtwert (Kosten) der einzelnen Verbrauchseinheiten im Abrechnungszeitraum. Die folgende Abbildung zeigt beispielsweise eine Nutzungsgebühr für den Azure Storage-Dienst für die Ressource *P10 Disks* (P10-Datenträger).

![Nutzungsgebühren (Rechnung)](./media/review-individual-bill/invoice-usage-charges.png)

Filtern Sie in Ihrer CSV-Nutzungsdatei die Spalte *MeterName* nach der entsprechenden Ressource aus Ihrer Rechnung. Addieren Sie dann den Wert *Cost* (Kosten) für Elemente in der Spalte. Hier sehen Sie ein Beispiel für den Namen der Verbrauchseinheit (P10-Datenträger), der demselben Posten auf der Rechnung entspricht.

![Nutzungsdatei: Addierter Wert für „MeterName“](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Der für *Cost* (Kosten) addierte Wert muss exakt den *Nutzungsgebühren* für die individuelle Ressource aus Ihrer Rechnung entsprechen.

## <a name="compare-billed-charges-and-usage-in-cost-analysis"></a>Vergleichen der abgerechneten Gebühren und Nutzung in der Kostenanalyse

Gebühren können auch mithilfe der Kostenanalyse im Azure-Portal überprüft werden. Wenn Sie sich einen schnellen Überblick über die in Rechnung gestellte Nutzung und Gebühren verschaffen möchten, wählen Sie im Azure-Portal auf der Seite Abonnements Ihr Abonnement aus. Klicken Sie als Nächstes auf **Kostenanalyse** und anschließend in der Ansichtsliste auf **Rechnungsdetails**.

![Beispiel: Auswählen von „Rechnungsdetails“](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Wählen Sie als Nächstes in der Datumsbereichsliste einen Zeitraum für Ihre Rechnung aus. Fügen Sie einen Filter für die Rechnungsnummer hinzu, und wählen Sie die Rechnungsnummer Ihrer Rechnung aus. Die Kostenanalyse zeigt Kostendetails für Ihre in Rechnung gestellten Elemente an.

![Beispiel: Details zu in Rechnung gestellten Kosten in der Kostenanalyse](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Die in der Kostenanalyse angezeigten Kosten müssen exakt den *Nutzungsgebühren* für die individuelle Ressource aus Ihrer Rechnung entsprechen.

![Nutzungsgebühren (Rechnung)](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-marketplace-services-are-billed-separately"></a>Separate Abrechnung der externen Marketplace-Dienste

<a name="external"></a>

Externe Dienste oder Marketplace-Gebühren gelten für Ressourcen, die von Drittanbietern erstellt wurden. Diese Ressourcen können über den Azure Marketplace genutzt werden. Barracuda Firewall ist beispielsweise eine von einem Drittanbieter angebotene Azure Marketplace-Ressource. Alle Gebühren für die Firewall und die entsprechenden Verbrauchseinheiten werden als Gebühren für externe Dienste angezeigt.

Gebühren für externe Dienste werden separat in Rechnung gestellt. Die Gebühren werden nicht in Ihrer Azure-Rechnung aufgeführt.

### <a name="resources-are-billed-by-usage-meters"></a>Abrechnung von Ressourcen nach Nutzungsverbrauchseinheiten

Azure wird nicht direkt nach Ressourcenkosten abgerechnet. Die Gebühren für eine Ressource werden mithilfe von Verbrauchseinheiten berechnet. Verbrauchseinheiten werden zum Nachverfolgen der Nutzung einer Ressource über die Dauer ihres Lebenszyklus verwendet. Diese Verbrauchseinheiten werden dann zum Berechnen der Rechnung verwendet.

Wenn Sie eine einzelne Azure-Ressource erstellen (beispielsweise einen virtuellen Computer), wird dafür mindestens eine Verbrauchseinheit erstellt. Verbrauchseinheiten werden verwendet, um die Nutzung der Ressource im zeitlichen Verlauf nachzuverfolgen. Jede Verbrauchseinheit gibt Verwendungsdatensätze aus, die von Azure zum Berechnen der Rechnung verwendet werden.

Für einen in Azure erstellten einzelnen virtuellen Computer können beispielsweise die folgenden Verbrauchseinheiten erstellt werden, um seine Verwendung nachzuverfolgen:

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

Nachdem der virtuelle Computer erstellt wurde, beginnen diese Verbrauchseinheiten mit der Ausgabe von Nutzungsdatensätzen. Diese Nutzung und der Preis der Verbrauchseinheit werden im Azure-System zur Messung von Verbrauchseinheiten nachverfolgt.

Die zur Berechnung Ihrer Rechnung verwendeten Verbrauchseinheiten werden in der CSV-Nutzungsdatei angezeigt (siehe Beispiel oben).

## <a name="pay-your-bill"></a>Bezahlen Ihrer Rechnung

<a name="payment"></a>

Wenn Sie eine Kreditkarte als Zahlungsmethode eingerichtet haben, wird die Zahlung automatisch zehn Tage nach Abschluss des Abrechnungszeitraums berechnet. Auf Ihrem Kreditkartenbeleg lautet die Postenzeile dann **MSFT Azure**.

Informationen zum Ändern der abgerechneten Kreditkarte finden Sie unter [Hinzufügen, Aktualisieren oder Entfernen einer Kreditkarte für Azure](../manage/change-credit-card.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Vergleichen abgerechneter Gebühren mit der Nutzungsdatei
> * Vergleichen von Gebühren und Nutzung in der Kostenanalyse

Schließen Sie die Schnellstartsitzung ab, um mit der Kostenanalyse zu beginnen.

> [!div class="nextstepaction"]
> [Kostenanalyse beginnen](../costs/quick-acm-cost-analysis.md)
