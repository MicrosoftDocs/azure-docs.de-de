---
title: Herunterladen von Azure-Rechnungen und täglichen Nutzungsdaten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure-Rechnungen und tägliche Nutzungsdaten herunterladen oder ansehen können.
keywords: Rechnung, Rechnungsdownload, Azure-Rechnung, Azure-Nutzung
services: ''
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e57e3898c5e843dcc130746a10e78b0681303670
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Herunterladen oder Anzeigen Ihrer Azure-Rechnungen und täglichen Nutzungsdaten
Sie können Ihre Rechnung auch aus dem [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) herunterladen oder sie per E-Mail zugeschickt bekommen. Um Ihre Daten zur täglichen Nutzung herunterzuladen, wechseln Sie zum [Azure-Kontocenter](https://account.azure.com/Subscriptions). Nur bestimmte Rollen, z.B. der Kontoadministrator, haben die Berechtigung zum Abrufen von Abrechnungs- und Nutzungsinformationen. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](billing-manage-access.md).

Dieser Artikel gilt nicht für Enterprise Agreement-Kunden (EA). Wenn Sie EA-Kunde sind, werden Ihre Rechnungen direkt an die Registrierungsadministratoren gesendet.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="get-your-invoice-in-email-pdf"></a>Empfangen Ihrer Rechnung per E-Mail (PDF)
Sie können die entsprechende Option aktivieren und zusätzliche Empfänger konfigurieren, die Ihre Azure-Rechnung in einer E-Mail erhalten. Diese Funktion ist für bestimmte Abonnements möglicherweise nicht verfügbar, z.B. für Supportangebote, Enterprise Agreements oder Azure in Open.

1. Wählen Sie auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement aus. Aktivieren Sie die Option für jedes Abonnement, das Sie besitzen. Klicken Sie auf **Rechnungen** und dann auf **Rechnung per E-Mail**. 

    ![Screenshot mit dem Ablauf der Aktivierung](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Klicken Sie auf **Aktivieren**, und stimmen Sie den Bedingungen zu.

    ![Screenshot mit dem Ablauf der Aktivierung](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Nachdem Sie die Vereinbarung akzeptiert haben, können Sie zusätzliche Empfänger konfigurieren.

    ![Screenshot mit dem Ablauf der Aktivierung](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Wenn Sie keine E-Mail erhalten, nachdem Sie diese Schritte befolgt haben, stellen Sie sicher, dass Ihre E-Mail-Adresse in den [Kommunikationseinstellungen auf Ihrem Profil](https://account.windowsazure.com/profile) richtig ist.

## <a name="download-invoice-from-azure-portal-pdf"></a>Herunterladen der Rechnung aus dem Azure-Portal (PDF)

1. Wählen Sie im Azure-Portal auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement als [Benutzer mit Zugriff auf Rechnungen](billing-manage-access.md) aus.

2. Wählen Sie **Rechnungen** aus. 

    ![Screenshot mit der Option „Abrechnung und Nutzung“](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Klicken Sie auf **Rechnung herunterladen**, um eine Kopie Ihrer PDF-Rechnung anzuzeigen. Falls **Nicht verfügbar** angezeigt wird, siehe [Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?](#noinvoice)

    ![Screenshot mit Abrechnungszeiträumen, der Option „Herunterladen“ und den Gesamtgebühren für jeden Abrechnungszeitraum](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Sie können Ihre Daten zur tägliche Nutzung auch durch Klicken auf den Abrechnungszeitraum anzeigen. 

Weitere Informationen über Ihre Rechnung finden Sie unter [Erläuterungen zur Rechnung für Microsoft Azure](billing-understand-your-bill.md). Hilfe beim Verwalten Ihrer Kosten finden Sie unter [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Herunterladen der Nutzungsdaten aus dem Kontocenter (CSV)

1. Melden Sie sich als Kontoadministrator im [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) an.

2. Wählen Sie das Abonnement, für das Sie die Rechnung und Nutzungsinformationen abrufen möchten.

3. Klicken Sie auf **ABRECHNUNGSVERLAUF**. 

    ![Screenshot mit Abrechnungsverlaufsoption](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Sie können Ihre Aufstellungen für die letzten sechs Abrechnungszeiträume sowie den aktuellen, nicht berechneten Zeitraum anzeigen. 

    ![Screenshot, der Abrechnungszeiträume, Optionen zum Herunterladen von Rechnungen und Daten zur täglichen Nutzung sowie Gesamtgebühren für jeden Abrechnungszeitraum anzeigt](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Wählen Sie **Aktuelle Abrechnung anzeigen** , um eine Schätzung der Kosten zum Zeitpunkt der Erstellung der Schätzung anzuzeigen. Diese Informationen werden nur einmal täglich aktualisiert und umfassen möglicherweise nicht die gesamte Nutzung. Ihre monatliche Abrechnung kann von dieser Schätzung abweichen.

    ![Screenshot mit der Option „Aktuelle Erklärung anzeigen“](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Screenshot mit der Schätzung der aktuellen Gebühren](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Wählen Sie **Nutzung herunterladen**, um die täglichen Nutzungsdaten als CSV-Datei herunterzuladen. Wenn zwei Versionen verfügbar sind, laden Sie Version 2 herunter.

    ![Screenshot mit der Option „Nutzung herunterladen“](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Nur der Kontoadministrator hat Zugriff auf das Azure-Kontocenter. Andere Abrechnungsadministratoren, z.B. Besitzer, erhalten mithilfe der [Abrechnungs-APIs](billing-usage-rate-card-overview.md) Informationen zur Nutzung.

Weitere Informationen über Ihre Daten zur täglichen Nutzung finden Sie unter [Erläuterungen zur Rechnung für Microsoft Azure](billing-understand-your-bill.md). Hilfe beim Verwalten Ihrer Kosten finden Sie unter [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](billing-getting-started.md).

## <a name="noinvoice"></a> Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?

Mehrere Gründe können dafür ausschlaggebend sein, dass Sie keine Rechnung sehen:

- Sie haben einen monatlichen Guthabenbetrag in Ihrem Abonnement, den Sie nicht überschritten haben, oder eine kostenlose Testversion. Eine Rechnung wird nur generiert, wenn Sie Geld schuldig sind.

- Es sind weniger als 30 Tage seit dem Abschluss Ihres Azure-Abonnements vergangen.

- Die Rechnung ist nicht noch erstellt. Warten Sie bis zum Ende des Abrechnungszeitraums.

- Wenn Sie nicht der Kontoadministrator sind, stehen Ihnen ältere Rechnungen möglicherweise nicht zur Verfügung.

## <a name="need-help-contact-support"></a>Brauchen Sie Hilfe? Wenden Sie sich an den Support.
Bei weiteren Fragen [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu lösen.

