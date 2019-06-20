---
title: Anzeigen und Herunterladen der Microsoft Azure-Rechnung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Microsoft Azure-Rechnung anzeigen und herunterladen.
keywords: Rechnung, Rechnungsdownload, Azure-Rechnung, Azure-Nutzung
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f71fe9b02765e0fc8fd5f3b7abbd54c87b08132f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617931"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Anzeigen und Herunterladen der Microsoft Azure-Rechnung

Für die meisten Abonnements können Sie die Rechnung aus dem [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) herunterladen oder per E-Mail zugeschickt bekommen. Wenn Sie Azure-Kunde mit einem Enterprise Agreement (EA-Kunde) sind, können Sie die Rechnungen Ihrer Organisation nicht herunterladen. Rechnungen werden an den Benutzer gesendet, der für den Empfang von Rechnungen für die Registrierung festgelegt wurde.

Nur bestimmte Rollen (z. B. „Kontoadministrator“ oder „Unternehmensadministrator“) verfügen über die Berechtigung zum Anzeigen von Rechnungen. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](billing-manage-access.md).

Wenn Sie über eine [Microsoft-Kundenvereinbarung](#check-your-access-to-a-microsoft-customer-agreement) verfügen, müssen Sie eine der Rollen „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ besitzen, um Ihre Rechnungen abrufen zu können. Weitere Informationen zu Abrechnungsrollen für Microsoft-Kundenvereinbarungen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-your-azure-invoices-pdf"></a>Herunterladen Ihrer Azure-Rechnungen (PDF-Format)

Für die meisten Abonnements können Sie die Rechnung aus dem Azure-Portal herunterladen. Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, finden Sie weitere Informationen unter „Herunterladen von Rechnungen für ein Abrechnungsprofil“.

### <a name="download-invoices-for-an-individual-subscription"></a>Herunterladen von Rechnungen für ein einzelnes Abonnement

1. Wählen Sie im Azure-Portal auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement als [Benutzer mit Zugriff auf Rechnungen](billing-manage-access.md) aus.

2. Wählen Sie **Rechnungen** aus.

    ![Screenshot mit der Option „Abrechnung und Nutzung“](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicken Sie auf **Rechnung herunterladen**, um eine Kopie Ihrer PDF-Rechnung anzuzeigen. Falls **Nicht verfügbar** angezeigt wird, siehe [Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?](#noinvoice)

    ![Screenshot mit Abrechnungszeiträumen, der Option „Herunterladen“ und den Gesamtgebühren für jeden Abrechnungszeitraum](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Sie können Ihre Daten zur tägliche Nutzung auch durch Klicken auf den Abrechnungszeitraum anzeigen.

Weitere Informationen über Ihre Rechnung finden Sie unter [Erläuterungen zur Rechnung für Microsoft Azure](billing-understand-your-bill.md). Hilfe beim Verwalten Ihrer Kosten finden Sie unter [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Herunterladen von Rechnungen für eine Microsoft-Kundenvereinbarung

Rechnungen werden für jedes [Abrechnungsprofil](billing-mca-overview.md#understand-billing-profiles) in der Microsoft-Kundenvereinbarung generiert. Sie müssen über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, um Rechnungen über das Azure-Portal herunterladen zu können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
1. Wählen Sie **Rechnungen** aus.
1. Suchen Sie im Rechnungsraster nach der Zeile der Rechnung, die Sie herunterladen möchten.
1. Klicken Sie am Ende der Zeile auf die Auslassungspunkte (`...`).
    ![Screenshot mit den Auslassungspunkten am Ende der Zeile](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. Wählen Sie im Kontextmenü für den Download **Rechnung** aus.

    ![Screenshot des Kontextmenüs](./media/billing-download-azure-invoice/contextmenu.png)

Wenn für den letzten Abrechnungszeitraum keine Rechnung angezeigt wird, lesen Sie unter [Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?](#noinvoice) nach.

## <a name="get-your-invoice-in-email-pdf"></a>Empfangen Ihrer Rechnung per E-Mail (PDF)

Sie können die entsprechende Option aktivieren und zusätzliche Empfänger konfigurieren, die Ihre Azure-Rechnung in einer E-Mail erhalten. Diese Funktion ist für bestimmte Abonnements möglicherweise nicht verfügbar, z.B. für Supportangebote, Enterprise Agreements oder Azure in Open. Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, finden Sie weitere Informationen unter „Abrufen der Rechnungen für Abonnements per E-Mail“.

### <a name="get-your-subscriptions-invoices-in-email"></a>Abrufen der Rechnungen für Abonnements per E-Mail

1. Wählen Sie auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement aus. Wählen Sie jedes Abonnement aus, das Sie besitzen. Klicken Sie auf **Rechnungen** und dann auf **Rechnung per E-Mail**.

    ![Screenshot mit dem Ablauf der Aktivierung](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klicken Sie auf **Aktivieren**, und stimmen Sie den Bedingungen zu.

    ![Screenshot mit dem Ablauf von Schritt 2 der Aktivierung](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Nachdem Sie die Vereinbarung akzeptiert haben, können Sie zusätzliche Empfänger konfigurieren. Wenn ein Empfänger entfernt wird, wird die E-Mail-Adresse nicht mehr gespeichert. Wenn Sie Ihre Meinung ändern, müssen Sie ihn erneut hinzufügen.

    ![Screenshot mit dem Ablauf von Schritt 3 der Aktivierung](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Wenn Sie keine E-Mail erhalten, nachdem Sie diese Schritte befolgt haben, stellen Sie sicher, dass Ihre E-Mail-Adresse in den [Kommunikationseinstellungen auf Ihrem Profil](https://account.windowsazure.com/profile) richtig ist.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Kündigen des Erhalts der Rechnungen für Abonnements per E-Mail

Sie können den Erhalt von Rechnungen per E-Mail kündigen, indem Sie die oben aufgeführten Schritte befolgen und auf **Abonnement für Rechnungen per E-Mail kündigen** klicken. Dadurch werden alle E-Mail-Adressen entfernt, an die Rechnungen gesendet werden sollten. Sie können die Empfänger neu konfigurieren, wenn Sie die Rechnungen wieder per E-Mail empfangen möchten.

 ![Screenshot mit dem Ablauf der Kündigung](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Abrufen von Rechnungen für die Microsoft-Kundenvereinbarung per E-Mail

Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, können Sie Ihre Rechnungen per E-Mail empfangen. Alle Benutzer mit den Rollen „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ und „Rechnungs-Manager“ erhalten die Rechnungen dann per E-Mail. Leseberechtigte können die Einstellungen für den Empfang der Rechnungen per E-Mail jedoch nicht bearbeiten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Rechnung per E-Mail** die Option **Einstellung für Rechnung per E-Mail aktualisieren** aus.

    ![Screenshot mit Eigenschaften für Rechnungs-E-Mails](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Wählen Sie **Aktivieren** aus.
1. Klicken Sie auf **Aktualisieren**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Kündigen des Erhalts von Rechnungen für die Microsoft-Kundenvereinbarung per E-Mail

Sie können den Erhalt von Rechnungen per E-Mail kündigen, indem Sie die oben aufgeführten Schritte befolgen und auf **Deaktivieren** klicken. Alle Benutzer mit den Rollen „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ und „Rechnungs-Manager“ erhalten dann ebenfalls keine Rechnungen mehr per E-Mail. Als Leseberechtigter können Sie die Einstellungen für den Empfang der Rechnungen per E-Mail nicht bearbeiten.

### <a name="noinvoice"></a> Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?

Mehrere Gründe können dafür ausschlaggebend sein, dass Sie keine Rechnung sehen:

- Es sind weniger als 30 Tage seit dem Abschluss Ihres Azure-Abonnements vergangen.

- Die Rechnung ist nicht noch erstellt. Warten Sie bis zum Ende des Abrechnungszeitraums.

- Sie haben keine Berechtigung zur Ansicht von Rechnungen. Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, müssen Sie eine der Rollen „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ besitzen. Für andere Abonnements werden ältere Rechnungen nur angezeigt, wenn Sie der Kontoadministrator sind. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](billing-manage-access.md).

- Wenn Sie eine kostenlose Testversion oder eine nicht überschrittene monatliche Gutschrift für Ihr Abonnement verwenden, erhalten Sie nur eine Rechnung, wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um mehr über Ihre Rechnung und Gebühren zu erfahren:

- [Anzeigen und Herunterladen der Microsoft Azure-Nutzung und -Gebühren](billing-download-azure-daily-usage.md)
- [Grundlegendes zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md)
- [Grundlegendes zu den Benennungen in Ihrer Azure-Rechnung](billing-understand-your-invoice.md)
- [Grundlegendes über Benennungen zu den Gebühren in der Datei mit ausführlichen Nutzungsdaten zu Microsoft Azure](billing-understand-your-usage.md)
- [Anzeigen der Azure Enterprise Agreement-Preise für Ihre Organisation](billing-ea-pricing.md)

Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, lesen Sie die folgenden Artikel:

- [Grundlegendes zu den Gebühren auf der Rechnung für Ihr Abrechnungsprofil](billing-mca-understand-your-bill.md)
- [Grundlegendes zu den Benennungen auf der Rechnung für Ihr Abrechnungsprofil](billing-mca-understand-your-invoice.md)
- [Grundlegendes zur CSV-Datei für die Azure-Nutzung und -Gebühren für Ihr Abrechnungsprofil](billing-mca-understand-your-usage.md)
- [Anzeigen und Herunterladen von Steuerdokumenten für Ihr Abrechnungsprofil](billing-mca-download-tax-document.md)
- [Anzeigen der Azure Enterprise Agreement-Preise für Ihre Organisation](billing-ea-pricing.md)
