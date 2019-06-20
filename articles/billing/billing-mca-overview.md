---
title: Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung – Azure | Microsoft-Dokumentation
description: Grundlegendes zum Abrechnungskonto für eine Microsoft-Kundenvereinbarung
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371450"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung

Für jede von Ihnen unterzeichnete Microsoft-Vereinbarung über die Verwendung von Azure wird ein Abrechnungskonto erstellt. Sie verwenden Ihr Abrechnungskonto zum Verwalten der Abrechnung und zum Nachverfolgen von Kosten. Sie können über Zugriff auf mehrere Abrechnungskonten verfügen. So könnten Sie sich für Ihre persönlichen Projekte für Azure registriert haben. Und außerdem könnten Sie auch über das Enterprise Agreement Ihrer Organisation oder über eine Microsoft-Kundenvereinbarung auf Azure zugreifen. Für jedes dieser Szenarien würden Sie über ein separates Abrechnungskonto verfügen.

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Grundlegendes zum Abrechnungskonto

Ihr Abrechnungskonto für die Microsoft-Kundenvereinbarung enthält ein oder mehrere Abrechnungsprofile, mit denen Sie Ihre Rechnungen und Zahlungsmethoden verwalten können. Jedes Abrechnungsprofil enthält einen oder mehrere Rechnungsabschnitte, mit denen Sie die Kosten auf der Rechnung des Abrechnungsprofils organisieren können.

Im folgenden Diagramm ist die Beziehung zwischen einem Abrechnungskonto und den Abrechnungsprofilen und Rechnungsabschnitten dargestellt.

![Diagramm mit der Abrechnungshierarchie für eine Microsoft-Kundenvereinbarung](./media/billing-mca-overview/mca-billing-hierarchy.png)

Die Rollen für das Abrechnungskonto verfügen über die höchste Berechtigungsstufe. Standardmäßig erhalten nur globale Administratoren für die Azure Active Directory-Instanz Ihrer Organisation Zugriff auf das Abrechnungskonto. Diese Rollen sollten Benutzern zugewiesen werden, die Rechnungen anzeigen und Kosten für Ihre gesamte Organisation nachverfolgen müssen. Bei diesen Benutzern handelt es sich beispielsweise um Leiter der Finanzabteilung oder IT-Manager. Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungskonto](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Grundlegendes zu Abrechnungsprofilen

Mit einem Abrechnungsprofil können Sie Ihre Rechnung(en) und Zahlungsmethoden verwalten. Eine monatliche Rechnung wird für Azure-Abonnements und andere Produkte generiert, die über das Abrechnungsprofil erworben wurden. Die Zahlungsmethoden werden für die Bezahlung der Rechnung verwendet.

Für Ihr Abrechnungskonto wird automatisch ein Abrechnungsprofil erstellt. Sie können neue Abrechnungsprofile erstellen, um zusätzliche Rechnungen einzurichten. So können Sie beispielsweise verschiedene Rechnungen für jede Abteilung oder für jedes Projekt in Ihrer Organisation verwenden.

Sie können auch Rechnungsabschnitte erstellen, um die Kosten auf einer Rechnung des Abrechnungsprofils zu organisieren. Im jeweiligen Abschnitt werden die Gebühren für Azure-Abonnements und Produkte aufgeführt, die für den Rechnungsabschnitt erworben wurden. Die Rechnung des Abrechnungsprofils umfasst die Gebühren für alle Rechnungsabschnitte.

Die Rollen für die Abrechnungsprofile verfügen über Berechtigungen zum Anzeigen und Verwalten von Rechnungen und Zahlungsmethoden. Weisen Sie diese Rollen Benutzern zu, die Rechnungen bezahlen. Bei diesen Benutzern handelt es sich beispielsweise um Mitglieder des Buchhaltungsteams in Ihrer Organisation. Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Eine für jedes Abrechnungsprofil generierte Monatsrechnung

Für jedes Abrechnungsprofil wird am Rechnungsdatum eine Monatsrechnung generiert. Die Rechnung enthält alle Gebühren für den vorherigen Monat.

Im Azure-Portal können Sie die Rechnung anzeigen, Dokumente herunterladen und die Einstellung ändern, um zukünftige Rechnungen per E-Mail zu erhalten. Weitere Informationen finden Sie unter [Herunterladen von Rechnungen für eine Microsoft-Kundenvereinbarung](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Bezahlung von Rechnungen über Zahlungsmethoden

Jedes Abrechnungsprofil verfügt über eigene Zahlungsmethoden, die zum Bezahlen der Rechnungen verwendet werden. Die folgenden Zahlungsmethoden werden unterstützt:

| Type             | Definition  |
|------------------|-------------|
|Azure-Gutschriften    |  Gutschriften werden automatisch auf den in Rechnung gestellten Gesamtbetrag der Rechnung angewendet, um den Betrag zu berechnen, den Sie bezahlen müssen. Weitere Informationen finden Sie unter [Nachverfolgen des Azure-Guthabens für das Abrechnungsprofil](billing-mca-check-azure-credits-balance.md). |
|Scheck oder Überweisung | Sie können den fälligen Rechnungsbetrag per Scheck oder Überweisung bezahlen. Die Anweisungen für die Zahlung sind auf der Rechnung angegeben. |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Steuern von Azure Marketplace- und Azure-Reservierungseinkäufen durch das Anwenden von Richtlinien

Wenden Sie Richtlinien an, um die über ein Abrechnungsprofil getätigten Einkäufe zu steuern. Sie können Richtlinien festlegen, um den Erwerb von Azure-Reservierungen und Marketplace-Produkten zu deaktivieren. Wenn die Richtlinien angewendet werden, können die für die Rechnungsabschnitte im Abrechnungsprofil erstellten Abonnements nicht zum Erwerben von Azure-Reservierungen und Marketplace-Produkten verwendet werden.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Benutzern das Erstellen von Azure-Abonnements durch Aktivieren von Azure-Plänen erlauben

Azure-Pläne werden beim Erstellen eines Abrechnungsprofils automatisch aktiviert. Alle Rechnungsabschnitte im Abrechnungsprofil erhalten Zugriff auf diese Pläne. Benutzer mit Zugriff auf den Rechnungsabschnitt können die Pläne verwenden, um Azure-Abonnements zu erstellen. Azure-Abonnements können nur erstellt werden, wenn ein Azure-Plan für das Abrechnungsprofil aktiviert ist. Die folgenden Azure-Pläne werden in Abrechnungskonten für Microsoft-Kundenvereinbarungen unterstützt:

| Plan             | Definition  |
|------------------|-------------|
|Microsoft Azure-Plan   | Dieser Plan ermöglicht Benutzern das Erstellen von Abonnements, die beliebige Workloads ausführen können. Weitere Informationen finden Sie unter [Microsoft Azure-Plan](https://azure.microsoft.com/offers/ms-azr-0017g/). |
|Microsoft Azure-Plan für Dev/Test | Dieser Plan ermöglicht Visual Studio-Abonnenten das Erstellen von Abonnements, die auf Entwicklungs- oder Testworkloads beschränkt sind. Für diese Abonnements ergeben sich Vorteile wie niedrigere Tarife und der Zugriff auf exklusive Images virtueller Computer im Azure-Portal. Weitere Informationen finden Sie unter [Microsoft Azure-Plan für Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g/).|

## <a name="understand-invoice-sections"></a>Grundlegendes zu Rechnungsabschnitten

Erstellen Sie Rechnungsabschnitte, um die Kosten auf einer Rechnung des Abrechnungsprofils zu organisieren. Möglicherweise benötigen Sie z. B. eine einzelne Rechnung für Ihre Organisation, möchten jedoch die Kosten nach Abteilung, Team oder Projekt organisieren. In diesem Szenario verwenden Sie ein einzelnes Abrechnungsprofil, in dem Sie einen Rechnungsabschnitt für jede Abteilung, jedes Team oder Projekt erstellen.

Beim Erstellen eines Rechnungsabschnitts können Sie anderen Benutzern die Berechtigung zum Erstellen von Azure-Abonnements für den Abschnitt zuweisen. Alle Nutzungsgebühren und Einkäufe für die Abonnements werden dann im entsprechenden Abschnitt der Rechnung angezeigt.

Die Rollen für den Rechnungsabschnitt verfügen über Berechtigungen zum Steuern, wer Azure-Abonnements erstellen darf. Weisen Sie diese Rollen Benutzern zu, die Ihre Azure-Umgebung für Teams in der Organisation einrichten. Bei diesen Benutzern handelt es sich beispielsweise um technische Leiter und technische Architekten. Weitere Informationen finden Sie unter [Rollen und Aufgaben für einen Rechnungsabschnitt](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um Informationen zu einem Abrechnungskonto zu erhalten:

- [Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure](billing-understand-mca-roles.md)
- [Erstellen eines zusätzlichen Azure-Abonnements für eine Microsoft-Kundenvereinbarung](billing-mca-create-subscription.md)
- [Erstellen von Abschnitten in Ihrer Rechnung zum Organisieren von Kosten](billing-mca-section-invoice.md)