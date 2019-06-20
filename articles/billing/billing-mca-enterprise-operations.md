---
title: Ausführen von Enterprise Agreement-Aufgaben in Microsoft-Kundenvereinbarung – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Enterprise Agreement-Aufgaben in Ihrem neuen Abrechnungskonto ausführen können.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371331"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Ausführen von Enterprise Agreement-Aufgaben in Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung

Wenn Ihre Organisation eine Microsoft-Kundenvereinbarung unterzeichnet hat, um Ihre Enterprise Agreement-Registrierung zu verlängern, wird ein neues Abrechnungskonto für die Vereinbarung eingerichtet. Die Abrechnung in Ihrem neuen Konto ist anders organisiert als Ihr Enterprise Agreement. In diesem Artikel wird beschrieben, wie Sie mit dem neuen Abrechnungskonto Aufgaben ausführen können, die Sie in Ihrem Enterprise Agreement ausgeführt haben.

## <a name="how-billing-is-organized-in-the-new-account"></a>Organisation der Abrechnung im neuen Konto

Das folgende Diagramm verdeutlicht, wie die Abrechnung in Ihrem neuen Abrechnungskonto organisiert ist.

![Abbildung der Hierarchie nach Umstellung von EA auf MCA](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Microsoft-Kundenvereinbarung    |
|------------------------|--------------------------------------------------------|
| Registrierung            | Mit dem Abrechnungsprofil verwalten Sie die Abrechnung für Ihre Organisation ähnlich wie bei der Enterprise Agreement-Registrierung. Unternehmensadministratoren werden Besitzer des Abrechnungsprofils. Weitere Informationen zu Abrechnungsprofilen finden Sie unter [Grundlegendes zu Abrechnungsprofilen](billing-mca-overview.md#understand-billing-profiles).
| Department            | Sie verwenden einen Rechnungsabschnitt zum Organisieren Ihrer Kosten. Dies ähnelt den Abteilungen in Ihrer Enterprise Agreement-Registrierung. Abteilungen werden zu Rechnungsabschnitten und Abteilungsadministratoren zu Besitzern der jeweiligen Rechnungsabschnitte. Weitere Informationen zu Rechnungsabschnitten finden Sie unter [Grundlegendes zu Rechnungsabschnitten](billing-mca-overview.md#understand-invoice-sections). |
| Konto               | Die Konten, die in Ihrem Enterprise Agreement erstellt wurden, werden beim neuen Abrechnungskonto nicht unterstützt. Die Abonnements des Kontos gehören dem jeweiligen Rechnungsabschnitt für die entsprechende Abteilung an. Kontobesitzer können Abonnements für ihre Rechnungsabschnitte erstellen und verwalten. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>Änderungen für Unternehmensadministratoren im neuen Abrechnungskonto

Die folgenden Änderungen gelten für Unternehmensadministratoren in einem Enterprise Agreement, das als Microsoft-Kundenvereinbarung verlängert wurde.

- Ein Abrechnungsprofil wird für Ihre Registrierung erstellt. Mit dem Abrechnungsprofil verwalten Sie die Abrechnung für Ihre Organisation wie bei der Enterprise Agreement-Registrierung. Weitere Informationen zu Abrechnungsprofilen finden Sie unter [Grundlegendes zu Abrechnungsprofilen](billing-mca-overview.md#understand-billing-profiles).
- Für jede Abteilung in Ihrer Enterprise Agreement-Registrierung wird ein Rechnungsabschnitt erstellt. Sie verwenden die Rechnungsabschnitte, um Ihre Abteilungen zu verwalten. Sie können zum Einrichten weiterer Abteilungen neue Rechnungsabschnitte erstellen. Weitere Informationen zu Rechnungsabschnitten finden Sie unter [Grundlegendes zu Rechnungsabschnitten](billing-mca-overview.md#understand-invoice-sections).
- Mit der Rolle des Erstellers von Azure-Abonnements geben Sie in Rechnungsabschnitten anderen Benutzer die Berechtigung zum Erstellen von Azure-Abonnements, wie bei den Konten, die in der Enterprise Agreement-Registrierung erstellt wurden.
- Im [Azure-Portal](https://portal.azure.com) statt über das Azure EA-Portal verwalten Sie die Abrechnung für Ihr Unternehmen.

Im neuen Abrechnungskonto erhalten Sie die folgenden Rollen:

**Besitzer des Abrechnungsprofils**: Ihnen wird die Rolle des Besitzers des Abrechnungsprofils für das Abrechnungsprofil zugewiesen, das bei der Unterzeichnung der Vereinbarung erstellt wurde. Mit der Rolle können Sie die Abrechnung für Ihre Organisation verwalten. Sie können Gebühren und Rechnungen anzeigen, Kosten auf der Rechnung ordnen, Zahlungsmethoden verwalten und den Zugriff auf die Abrechnung für Ihre Organisation steuern.

**Rechnungsabschnitt (Besitzer)** : Ihnen wird die Rolle des Rechnungsabschnittssbesitzers für alle Rechnungsabschnitte zugewiesen, die für die Abteilungen in Ihrer Enterprise Agreement-Registrierung erstellt werden. Mit der Rolle können Sie steuern, wer Azure-Abonnements erstellen und andere Produkte erwerben darf.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Anzeigen des Saldos von Gebühren und Guthaben für Ihre Organisation

Mit Ihrem Abrechnungsprofil verfolgen Sie den Saldo von Gebühren und Guthaben in Azure für Ihre Organisation ähnlich wie bei der Enterprise Agreement-Registrierung nach.

Wie Sie den Guthabensaldo für ein Abrechnungsprofil anzeigen, erfahren Sie unter [Nachverfolgen des Azure-Guthabensaldos für Ihr Abrechnungsprofil](billing-mca-check-azure-credits-balance.md).

Wie Sie Gebühren für ein Abrechnungsprofil anzeigen, erfahren Sie unter [Grundlagen der Gebühren für Ihre Microsoft-Kundenvereinbarung](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Anzeigen der Gebühren für eine Abteilung

Für jede Abteilung, die Sie in Ihrem Enterprise Agreement hatten, wird ein Rechnungsabschnitt erstellt. Sie können die Gebühren für einen Rechnungsabschnitt im Azure-Portal anzeigen. Weitere Informationen finden Sie unter [Anzeigen von Transaktionen nach Rechnungsabschnitten](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Anzeigen von Gebühren für ein Konto

Die bei Ihrer Enterprise Agreement-Registrierung erstellten Konten werden beim neuen Abrechnungskonto nicht unterstützt. Die Abonnements des Kontos gehören dem jeweiligen Rechnungsabschnitt für die entsprechende Abteilung an. Kontobesitzer können Abonnements für ihre Rechnungsabschnitte erstellen und verwalten.

Um die aggregierten Kosten für Abonnements anzuzeigen, die zu einem Konto gehörten, müssen Sie für jedes Abonnement eine Kostenstelle festlegen. Dann können Sie mit der CSV-Datei über Azure-Nutzung und -Gebühren die Abonnements nach der Kostenstelle filtern.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Herunterladen von CSV-Datei über Nutzung und Gebühren, Preisblatt und Steuerdokumenten

Für jedes Abrechnungsprofil in Ihrem Abrechnungskonto wird eine Monatsrechnung generiert. Für jede Rechnung können Sie die CSV-Datei über Azure-Nutzung und -Gebühren, Preisblatt und (falls zutreffend) Steuerdokument herunterladen. Sie können die CSV-Datei über Azure-Nutzung und -Gebühren auch für die Gebühren des aktuellen Monats herunterladen.

Wie Sie die CSV-Datei über Azure-Nutzung und -Gebühren herunterladen, erfahren Sie unter [Herunterladen der Nutzung für Ihre Microsoft-Kundenvereinbarung](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Wie Sie das Preisblatt herunterladen, erfahren Sie unter [Herunterladen und Anzeigen der Preise für Ihre Microsoft-Kundenvereinbarung](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement).

Wie Sie Steuerdokumente herunterladen, erfahren Sie unter [Herunterladen und Anzeigen von Steuerdokumenten](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Hinzufügen eines weiteren Unternehmensadministrators

Gewähren Sie Benutzern Zugriff auf das Abrechnungsprofil, damit sie die Abrechnung für Ihre Organisation anzeigen und verwalten können. Über den Link **Zugriffssteuerung (IAM)** im Azure-Portal können Sie Zugriff gewähren.  Weitere Informationen zu Rollen von Abrechnungsprofilen finden Sie unter [Rollen und Aufgaben für Abrechnungsprofile](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Weitere Informationen darüber, wie Sie Zugriff auf Ihr Abrechnungsprofil gewähren können, finden Sie unter [Verwalten von Abrechnungsrollen im Azure-Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Erstellen einer neuen Abteilung

Erstellen Sie einen Rechnungsabschnitt zum Organisieren Ihrer Kosten entsprechend Ihren Anforderungen ähnlich der Abteilungen in Ihrer Enterprise Agreement-Registrierung. Sie können im Azure-Portal einen neuen Rechnungsabschnitt erstellen. Weitere Informationen finden Sie unter [Erstellen von Abschnitten in Ihrer Rechnung zum Organisieren von Kosten](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Erstellen eines neuen Kontos

Weisen Sie Benutzern die Rolle des Erstellers von Azure-Abonnements in Rechnungsabschnitten zu, damit sie die Berechtigung zum Erstellen von Azure-Abonnements erhalten, wie bei den Konten, die in der Enterprise Agreement-Registrierung erstellt wurden. Weitere Informationen finden Sie unter [Gewähren der Berechtigung zum Erstellen von Azure-Abonnements für andere Benutzer](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>Änderungen für Abteilungsadministratoren im neuen Abrechnungskonto

Die folgenden Änderungen gelten für Abteilungsadministratoren in einem Enterprise Agreement, das als Microsoft-Kundenvereinbarung verlängert wurde.

- Für jede Abteilung in Ihrer Enterprise Agreement-Registrierung wird ein Rechnungsabschnitt erstellt. Sie verwenden die Rechnungsabschnitte, um Ihre Abteilungen zu verwalten. Weitere Informationen zu Rechnungsabschnitten finden Sie unter [Grundlegendes zu Rechnungsabschnitten](billing-mca-overview.md#understand-invoice-sections).
- Mit der Rolle des Erstellers von Azure-Abonnements geben Sie in Rechnungsabschnitten anderen Benutzer die Berechtigung zum Erstellen von Azure-Abonnements, wie bei den Konten, die in der Enterprise Agreement-Registrierung erstellt wurden.
- Im Azure-Portal statt über das Azure EA-Portal verwalten Sie die Abrechnung für Ihr Unternehmen.

Im neuen Abrechnungskonto erhalten Sie die folgenden Rollen:

**Rechnungsabschnitt (Besitzer)** : Ihnen wird die Rolle des Rechnungsabschnittssbesitzers für den Rechnungsabschnitt zugewiesen, der für die Abteilungen erstellt wird, die Sie in Ihrem Enterprise Agreement hatten. Mit der Rolle können Sie Gebühren anzeigen und nachverfolgen und steuern, wer für den Rechnungsabschnitt Azure-Abonnements erstellen und andere Produkte kaufen kann.

### <a name="view-charges-for-your-department"></a>Anzeigen der Gebühren für Ihre Abteilung

Sie können Gebühren für den Rechnungsabschnitt, der für Ihre Abteilung erstellt wird, im Azure-Portal unter [Kostenverwaltung + Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) anzeigen.

### <a name="add-an-additional-department-administrator"></a>Hinzufügen eines weiteren Abteilungsadministrators

Für jede Abteilung, die Sie in Ihrem Enterprise Agreement hatten, wird ein Rechnungsabschnitt erstellt. Sie können auf der Seite **Zugriffssteuerung (IAM)** im Azure-Portal anderen Personen Zugriff zum Anzeigen und Verwalten des Rechnungsabschnitts gewähren. Weitere Informationen zu Rechnungsabschnittrollen finden Sie unter [Rollen und Aufgaben im Rechnungsabschnitt](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Weitere Informationen darüber, wie Sie Zugriff auf Ihren Rechnungsabschnitt gewähren können, finden Sie unter [Verwalten von Abrechnungsrollen im Azure-Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Erstellen eines neuen Kontos in Ihrer Abteilung

Weisen Sie Benutzern die Rolle des Erstellers von Azure-Abonnements in dem Rechnungsabschnitt zu, der für Ihre Abteilung erstellt wird. Weitere Informationen finden Sie unter [Gewähren der Berechtigung zum Erstellen von Azure-Abonnements für andere Benutzer](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Anzeigen von Gebühren für Konten in Ihren Abteilungen

Die bei Ihrer Enterprise Agreement-Registrierung erstellten Konten werden beim neuen Abrechnungskonto nicht unterstützt. Die Abonnements des Kontos gehören dem jeweiligen Rechnungsabschnitt für die entsprechende Abteilung an. Kontobesitzer können Abonnements für ihre Rechnungsabschnitte erstellen und verwalten.

Um die aggregierten Kosten für Abonnements anzuzeigen, die zu einem Konto in Ihrer Abteilung gehörten, müssen Sie für jedes Abonnement eine Kostenstelle festlegen. Dann können Sie mit der Datei über Azure-Nutzung und -Gebühren die Abonnements nach der Kostenstelle filtern.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>Änderungen für Kontobesitzer im neuen Abrechnungskonto

Kontobesitzer im Enterprise Agreement erhalten die Berechtigung, im neuen Abrechnungskonto Azure-Abonnements zu erstellen. Ihre vorhandenen Azure-Abonnements gehören zu dem Rechnungsabschnitt, der für Ihre Abteilung erstellt wird. Wenn Ihr Konto nicht zu einer Abteilung gehört, gehören Ihre Abonnements zu einem Rechnungsabschnitt namens „Standardrechnungsabschnitt“.

Um zusätzliche Azure-Abonnements zu erstellen, erhalten Sie im neuen Abrechnungskonto die folgende Rolle.

**Azure-Abonnementersteller**: Ihnen wird die Rolle des Erstellers von Azure-Abonnements in dem Rechnungsabschnitt zugewiesen, der für Ihre Abteilung im Enterprise Agreement erstellt wird. Wenn Ihr Konto nicht zu einer Abteilung gehört, erhalten Sie die Rolle des Erstellers von Azure-Abonnements in einem Rechnungsabschnitt namens „Standardrechnungsabschnitt“. Mit dieser Rolle können Sie Azure-Abonnements für den Rechnungsabschnitt erstellen.

### <a name="create-an-azure-subscription"></a>Erstellt ein Azure-Abonnement.

Sie können Azure-Abonnements für Ihren Rechnungsabschnitt im Azure-Portal erstellen. Weitere Informationen finden Sie unter [Erstellen eines zusätzlichen Azure-Abonnements für eine Microsoft-Kundenvereinbarung](billing-mca-create-subscription.md).

### <a name="view-charges-for-your-account"></a>Anzeigen der Gebühren für Ihr Konto

Um Gebühren für Abonnements anzuzeigen, die zu einem Konto gehörten, wechseln Sie im Azure-Portal zur [Seite „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Auf der Seite „Abonnements“ werden die Gebühren für alle Ihre Abonnements angezeigt.

### <a name="view-charges-for-a-subscription"></a>Anzeigen von Gebühren für ein Abonnement

Sie können die Gebühren für ein Abonnement entweder auf der [Seite „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oder in der Azure-Kostenanalyse anzeigen. Weitere Informationen zur Azure-Kostenanalyse finden Sie unter [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zum Abrechnungskonto für eine Microsoft-Kundenvereinbarung](billing-mca-overview.md)
- [Informationen zu Ihrer Rechnung](billing-understand-your-bill.md)
- [Erläuterungen zur Rechnung](billing-understand-your-invoice.md)
- [Übernehmen des Abrechnungsbesitzes für Azure-Abonnements von anderen Benutzern](billing-mca-request-billing-ownership.md)
