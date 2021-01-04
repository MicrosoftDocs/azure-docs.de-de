---
title: 'Überprüfen der Rechnung für Ihre Microsoft-Kundenvereinbarung: Azure'
description: Hier erfahren Sie, wie Sie Ihre Rechnung und Ihre Ressourcennutzung sowie Gebühren der Rechnung für Ihre Microsoft-Kundenvereinbarung überprüfen.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: banders
ms.openlocfilehash: b5de81f5eaf76c16c1ea8fc78075c4567c77aeee
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531977"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Tutorial: Überprüfen der Rechnung für Ihre Microsoft-Kundenvereinbarung

Durch Analysieren der einzelnen Transaktionen können Sie die Gebühren auf Ihrer Rechnung nachvollziehen. Im Abrechnungskonto für eine Microsoft-Kundenvereinbarung wird jeden Monat eine Rechnung für jedes Abrechnungsprofil generiert. Die Rechnung enthält alle Gebühren des vorherigen Monats. Sie können Ihre Rechnungen im Azure-Portal anzeigen und die Gebühren mit der Nutzungsdetaildatei vergleichen.

Dieses Tutorial gilt nur für Azure-Kunden mit einer Microsoft-Kundenvereinbarung.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen abgerechneter Transaktionen im Azure-Portal
> * Überprüfen der ausstehenden Gebühren zum Schätzen Ihrer nächsten Rechnung
> * Analysieren Ihrer Azure-Nutzungsgebühren

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung.

Sie müssen Zugriff auf eine Microsoft-Kundenvereinbarung haben. Sie müssen über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, um Abrechnungs- und Nutzungsinformationen anzeigen zu können. Weitere Informationen zu Abrechnungsrollen für Microsoft-Kundenvereinbarungen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Seit dem Abschluss des Azure-Abonnements müssen mehr als 30 Tage vergangen sein. Die Abrechnung von Azure erfolgt am Ende Ihres Rechnungszeitraums.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

- Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung

Überprüfen Sie den Vertragstyp, um zu bestimmen, ob Sie Zugriff auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung haben.

Geben Sie im Azure-Portal *Kostenverwaltung + Abrechnung* in das Suchfeld ein, und wählen Sie anschließend **Kostenverwaltung + Abrechnung** aus.

![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Wenn Sie Zugriff auf genau einen Abrechnungsbereich haben, wählen Sie auf der linken Seite **Eigenschaften** aus. Sie haben Zugriff auf ein Abrechnungskonto, wenn der Typ des Abrechnungskontos **Microsoft-Kundenvereinbarung** ist.

![Screenshot: Microsoft-Kundenvereinbarung auf der Eigenschaftenseite](./media/review-customer-agreement-bill/billing-mca-property.png)

Wenn Sie Zugriff auf mehrere Abrechnungsbereiche haben, überprüfen Sie in der Spalte „Abrechnungskonto“ den Typ. Sie haben Zugriff auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung, wenn der Abrechnungskontotyp für beliebige der Bereiche **Microsoft-Kundenvereinbarung** ist.

Wählen Sie auf der Seite mit den Abrechnungsbereichen die Option **Abrechnungsbereiche** und anschließend das Abrechnungskonto aus, das zur Zahlung der Abonnementnutzung verwendet wird. Das Abrechnungskonto sollte vom Typ **Microsoft-Kundenvereinbarung** sein.

![Screenshot: Microsoft-Kundenvereinbarung auf der Seite mit der Liste der Abrechnungskonten](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Überprüfen abgerechneter Transaktionen im Azure-Portal

Wählen Sie im Azure-Portal auf der linken Seite die Option **Alle Transaktionen** aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto, ein Abrechnungsprofil oder einen Rechnungsabschnitt und dann **Alle Transaktionen** auswählen.

Auf der Seite „Alle Transaktionen“ werden die folgenden Informationen angezeigt:

![Screenshot der Liste der abgerechneten Transaktionen](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Column  |Definition  |
|---------|---------|
|Date     | Das Datum der Transaktion  |
|Rechnungs-ID     | Der Bezeichner für die Rechnung, auf der die Transaktion in Rechnung gestellt wurde. Wenn Sie eine Supportanfrage senden, teilen Sie dem Azure-Support die ID mit, um die Bearbeitung Ihrer Supportanfrage zu beschleunigen. |
|Transaktionstyp     |  Der Typ der Transaktion, z. B. Erwerb, Stornierung und Nutzungsgebühren  |
|Produktfamilie     | Die Kategorie des Produkts, z. B. „Compute“ für virtuelle Computer oder „Datenbank“ für Azure SQL-Datenbank|
|Produkt-SKU     | Ein eindeutiger Code, der die Instanz Ihres Produkts identifiziert |
|Amount     |  Der Betrag der Transaktion      |
|Rechnungsabschnitt     | Die Transaktion wird in diesem Abschnitt der Rechnung des Abrechnungsprofils angezeigt. |
|Abrechnungsprofil     | Die Transaktion wird auf der Rechnung dieses Abrechnungsprofils angezeigt. |

Suchen Sie nach einer Rechnungs-ID, um die Transaktionen für die Rechnung zu filtern.

### <a name="view-transactions-by-invoice-sections"></a>Anzeigen von Transaktionen nach Rechnungsabschnitten

Mithilfe von Rechnungsabschnitten können Sie die Kosten auf einer Rechnung des Abrechnungsprofils organisieren. Weitere Informationen. Beim Generieren einer Rechnung werden die Gebühren für alle Abschnitte im Abrechnungsprofil auf der Rechnung aufgeführt.

In der folgenden Abbildung sind die Gebühren für den Rechnungsabschnitt „Accounting Dept“ (Buchhaltungsabteilung) einer Beispielrechnung dargestellt.

![Beispielabbildung der Details der Informationen eines Rechnungsabschnitts](./media/review-customer-agreement-bill/invoicesection-details.png)

Nachdem Sie die Gebühren für einen Rechnungsabschnitt identifiziert haben, können Sie im Azure-Portal die Transaktionen anzeigen, um die Gebühren nachzuvollziehen/zu verstehen.

Wechseln Sie im Azure-Portal zur Seite „Alle Transaktionen“, um die Transaktionen für eine Rechnung anzuzeigen.

Filtern Sie nach dem Namen eines Rechnungsabschnitts, um die Transaktionen anzuzeigen.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Überprüfen der ausstehenden Gebühren zum Schätzen Ihrer nächsten Rechnung

Im Abrechnungskonto für eine Microsoft-Kundenvereinbarung werden die Gebühren geschätzt und bis zur Rechnungsstellung als ausstehend betrachtet. Im Azure-Portal können Sie die ausstehenden Gebühren anzeigen, um Ihre nächste Rechnung zu schätzen. Ausstehende Gebühren sind Schätzungen und enthalten keine Steuern. Die tatsächlichen Gebühren in Ihrer nächsten Rechnung weichen von den ausstehenden Gebühren ab.

### <a name="view-summary-of-pending-charges"></a>Anzeigen der Zusammenfassung der ausstehenden Gebühren

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** aus, und wählen Sie dann ein Abrechnungsprofil aus.

Wählen Sie am oberen Bildschirmrand die Registerkarte **Zusammenfassung** aus.

Im Gebührenabschnitt werden die Gebühren für den bisherigen Kalendermonat und die Gebühren für den letzten Monat angezeigt.

![Screenshot mit einer Übersicht des Abrechnungsprofils](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Die Gebühren für den bisherigen Kalendermonat sind die ausstehenden Gebühren für den aktuellen Monat. Sie werden abgerechnet, wenn die Rechnung für den Monat generiert wird. Wenn die Rechnung für den letzten Monat noch nicht generiert wurde, sind die Gebühren im letzten Monat auch ausstehende Gebühren. Diese ausstehenden Gebühren werden dann in Ihrer nächsten Rechnung aufgeführt.

### <a name="view-pending-transactions"></a>Anzeigen von ausstehenden Transaktionen

Nachdem Sie die ausstehenden Gebühren identifiziert haben, können Sie die Gebühren nachvollziehen und verstehen, indem Sie die einzelnen Transaktionen analysieren, die zur Entstehung dieser Gebühren beigetragen haben. Zu diesem Zeitpunkt werden die ausstehenden Nutzungsgebühren nicht auf der Seite „Alle Transaktionen“ angezeigt. Sie können die ausstehenden Nutzungsgebühren auf der Azure-Abonnementseite anzeigen.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

Geben Sie im Azure-Portal *Kostenverwaltung + Abrechnung* in das Suchfeld ein, und wählen Sie anschließend **Kostenverwaltung + Abrechnung** aus.

Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** aus, und wählen Sie dann ein Abrechnungsprofil aus.

Wählen Sie links auf der Seite die Option **Alle Transaktionen** aus.

Suchen Sie nach *ausstehend*. Verwenden Sie den Filter **Zeitraum**, um die ausstehenden Gebühren für den aktuellen oder letzten Monat anzuzeigen.

![Screenshot der Liste der ausstehenden Transaktionen](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Anzeigen der ausstehenden Nutzungsgebühren

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

Geben Sie im Azure-Portal *Kostenverwaltung + Abrechnung* in das Suchfeld ein, und wählen Sie anschließend **Kostenverwaltung + Abrechnung** aus.

Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** aus, und wählen Sie dann ein Abrechnungsprofil aus.

Wählen Sie links auf der Seite die Option **Alle Abonnements** aus.

Auf der Azure-Abonnementseite werden die Gebühren des aktuellen und des letzten Monats für jedes Abonnement im Abrechnungsprofil angezeigt. Die Gebühren für den bisherigen Kalendermonat sind die ausstehenden Gebühren für den aktuellen Monat. Sie werden abgerechnet, wenn die Rechnung für den Monat generiert wird. Wenn die Rechnung für den letzten Monat noch nicht generiert wurde, sind die Gebühren im letzten Monat auch ausstehende Gebühren.

![Im Screenshot werden Abonnements mit Gebühren für den bisherigen Kalendermonat und Gebühren für den letzten Monat angezeigt.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analysieren Ihrer Azure-Nutzungsgebühren

Verwenden Sie die CSV-Datei zu Azure-Nutzung und -Gebühren, um Ihre nutzungsbasierten Gebühren zu analysieren. Sie können die Datei für eine Rechnung oder für ausstehende Gebühren herunterladen.

### <a name="download-your-invoice-and-usage-details"></a>Herunterladen Ihrer Rechnung und der Nutzungsdetails

Abhängig von Ihren Zugriffsberechtigungen müssen Sie unter „Kostenverwaltung + Abrechnung“ ggf. ein Abrechnungskonto oder ein Abrechnungsprofil auswählen. Wählen Sie im Menü auf der linken Seite die Option **Rechnungen** unter **Abrechnung** aus. Suchen Sie im Rechnungsraster nach der Zeile der Rechnung, die Sie herunterladen möchten. Wählen Sie am Ende der Zeile das Downloadsymbol oder die Auslassungspunkte (...) aus. Laden Sie im Feld **Herunterladen** die Datei mit den Nutzungsdetails und die Rechnung herunter.

### <a name="view-detailed-usage-by-invoice-section"></a>Anzeigen der detaillierten Nutzung nach Rechnungsabschnitt

Sie können die Datei zu Azure-Nutzung und -Gebühren filtern, um die Nutzungsgebühren für Ihre Rechnungsabschnitte abzustimmen.

Im Anschluss erfahren Sie Schritt für Schritt, wie Sie Computegebühren für den Rechnungsabschnitt „Accounting Dept“ (Buchhaltungsabteilung) abgleichen:

![Beispielabbildung der Details der Informationen eines Rechnungsabschnitts](./media/review-customer-agreement-bill/invoicesection-details.png)

| PDF-Rechnung | CSV-Datei zu Azure-Nutzung und -Gebühren |
| --- | --- |
|Accounting Dept (Buchhaltungsabteilung) |invoiceSectionName |
|Nutzungsgebühren – Microsoft Azure-Plan |productOrderName |
|Compute |serviceFamily |

Filtern Sie in der CSV-Datei die Spalte **invoiceSectionName** nach **Accounting Dept** (Buchhaltungsabteilung). Filtern Sie anschließend in der CSV-Datei die Spalte **productOrderName** nach **Microsoft Azure Plan**. Filtern Sie als Nächstes in der CSV-Datei die Spalte **serviceFamily** nach **Microsoft.Compute**.

![Screenshot der nach Rechnungsabschnitt gefilterten Datei zu Azure-Nutzung und -Gebühren](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Anzeigen der detaillierten Nutzung nach Abonnement

Sie können die CSV-Datei zu Azure-Nutzung und -Gebühren filtern, um die Nutzungsgebühren für Ihre Abonnements abzustimmen. Nachdem Sie die Gebühren für ein Abonnement identifiziert haben, können Sie die Gebühren mithilfe der CSV-Datei zu Azure-Nutzung und -Gebühren analysieren.

Die folgende Abbildung zeigt die Liste der Abonnements im Azure-Portal.

![Im Screenshot wird die Liste der Abonnements im Azure-Portal mit einem hervorgehobenen Abonnement angezeigt.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Filtern Sie in der CSV-Datei zu Azure-Nutzung und -Gebühren die Spalte **subscriptionName** nach **WA_Subscription**, um die detaillierten Nutzungsgebühren für „WA_Subscription“ anzuzeigen.

![Screenshot der nach Abonnement gefilterten Datei zu Azure-Nutzung und -Gebühren](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Bezahlen Ihrer Rechnung

Die Anweisungen zum Bezahlen Ihrer Rechnung finden Sie unten auf der Rechnung. [Informationen zur Bezahlung](mca-understand-your-invoice.md#how-to-pay)

Wenn Sie Ihre Rechnung bereits bezahlt haben, können Sie den Status der Zahlung im Azure-Portal auf der Seite „Rechnungen“ überprüfen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Überprüfen abgerechneter Transaktionen im Azure-Portal
> * Überprüfen der ausstehenden Gebühren zum Schätzen Ihrer nächsten Rechnung
> * Analysieren Ihrer Azure-Nutzungsgebühren

Durchlaufen Sie die Schnellstartanleitung, um mit der Kostenanalyse zu beginnen.

> [!div class="nextstepaction"]
> [Kostenanalyse beginnen](../costs/quick-acm-cost-analysis.md)
