---
title: Abrechnungsrollen für Microsoft-Kundenvereinbarungen – Azure
description: Hier erhalten Sie Informationen zu Abrechnungsrollen für Abrechnungskonten für Microsoft-Kundenvereinbarungen in Azure.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.openlocfilehash: 00ac61567502984759c5db9837060c86aaee378d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593585"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure

Zum Verwalten Ihres Abrechnungskontos für eine Microsoft-Kundenvereinbarung verwenden Sie die in den folgenden Abschnitten beschriebenen Rollen. Diese Rollen sind zusätzlich zu den integrierten Rollen verfügbar, die in Azure zum Steuern des Zugriffs auf Ressourcen vorhanden sind. Weitere Informationen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben.

## <a name="billing-role-definitions"></a>Definitionen von Abrechnungsrollen

In der folgenden Tabelle werden die Abrechnungsrollen beschrieben, die Sie zum Verwalten Ihres Abrechnungskontos, Ihrer Abrechnungsprofile und Rechnungsabschnitte verwenden.

|Role|BESCHREIBUNG|
|---|---|
|Besitzer des Abrechnungskontos|Verwalten aller Einstellungen für ein Abrechnungskonto|
|Mitwirkender am Abrechnungskonto|Verwalten aller Einstellungen außer den Berechtigungen für das Abrechnungskonto|
|Benutzer mit Leseberechtigung für Abrechnungskonto|Schreibgeschützte Ansicht aller Einstellungen für das Abrechnungskonto|
|Besitzer des Abrechnungsprofils|Verwalten aller Einstellungen für das Abrechnungsprofil|
|Mitwirkender am Abrechnungsprofil|Verwalten aller Einstellungen außer den Berechtigungen für das Abrechnungsprofil|
|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Schreibgeschützte Ansicht aller Einstellungen für das Abrechnungsprofil|
|Rechnungs-Manager|Anzeigen von Rechnungen für das Abrechnungsprofil|
|Rechnungsabschnitt (Besitzer)|Verwalten aller Einstellungen für den Rechnungsabschnitt|
|Rechnungsabschnitt (Mitwirkender)|Verwalten aller Einstellungen außer den Berechtigungen für den Rechnungsabschnitt|
|Rechnungsabschnitt (Leser)|Schreibgeschützte Ansicht aller Einstellungen für den Rechnungsabschnitt|
|Azure-Abonnementersteller|Erstellen von Azure-Abonnements|

## <a name="billing-account-roles-and-tasks"></a>Rollen und Aufgaben für ein Abrechnungskonto

Mit einem Abrechnungskonto können Sie die Abrechnung für Ihre Organisation verwalten. Sie verwenden ein Abrechnungskonto, um Kosten zu organisieren, Gebühren und Rechnungen zu überwachen und den Zugriff auf Abrechnungen für Ihre Organisation zu steuern. Weitere Informationen finden Sie unter [Grundlegendes zum Abrechnungskonto](../understand/mca-overview.md#your-billing-account).

In den folgenden Tabellen wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit dem Abrechnungskonto benötigen.

### <a name="manage-billing-account-permissions-and-properties"></a>Verwalten von Berechtigungen und Eigenschaften für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen vorhandener Berechtigungen für das Abrechnungskonto|✔|✔|✔|
|Zuweisen von Berechtigungen zum Anzeigen und Verwalten des Abrechnungskontos für andere Benutzer|✔|✘|✘|
|Anzeigen von Eigenschaften des Abrechnungskontos wie z. B. Firmenname und Adresse|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Verwalten von Abrechnungsprofilen für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller Abrechnungsprofile im Konto|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Verwalten von Rechnungen für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller Rechnungen im Konto|✔|✔|✔|
|Herunterladen von Rechnungen, Dateien zu Azure-Nutzung und -Gebühren, Preisblättern und Steuerdokumenten für das Konto|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Verwalten von Rechnungsabschnitten für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller Rechnungsabschnitte im Konto|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Verwalten von Transaktionen für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller Abrechnungstransaktionen für das Konto|✔|✔|✔|
|Anzeigen aller erworbenen Produkte für das Konto|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Verwalten von Abonnements für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller Azure-Abonnements im Abrechnungskonto|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Rollen und Aufgaben für ein Abrechnungsprofil

Mit einem Abrechnungsprofil können Sie Ihre Rechnungen und Zahlungsmethoden verwalten. Eine monatliche Rechnung wird für die Azure-Abonnements und andere Produkte generiert, die über das Abrechnungsprofil erworben wurden. Die Zahlungsmethoden werden für die Bezahlung der Rechnung verwendet. Weitere Informationen finden Sie unter [Grundlegendes zu Abrechnungsprofilen](../understand/mca-overview.md#billing-profiles).

In den folgenden Tabellen wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit dem Abrechnungsprofil benötigen.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Verwalten von Berechtigungen, Eigenschaften und Richtlinien für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen vorhandener Berechtigungen für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Zuweisen von Berechtigungen zum Anzeigen und Verwalten des Abrechnungsprofils für andere Benutzer|✔|✘|✘|✘|✘|✘|✘|
|Anzeigen der Eigenschaften des Abrechnungsprofils wie z. B. Auftragsnummer und Einstellung für Rechnungen per E-Mail|✔|✔|✔|✔|✔|✔|✔|
|Aktualisieren der Eigenschaften des Abrechnungsprofils |✔|✔|✘|✘|✘|✘|✘|
|Anzeigen der auf das Abrechnungsprofil angewendeten Richtlinien wie z. B. Azure-Reservierungseinkäufe aktivieren und Azure Marketplace-Einkäufe aktivieren|✔|✔|✔|✔|✔|✔|✔|
|Anwenden von Richtlinien auf das Abrechnungsprofil |✔|✔|✘|✘|✘|✘|✘|
|Verwalten von Reservierungsaufträgen |✔|✔|✘|✘|✘|✘|✘|
|Anzeigen von Reservierungsaufträgen |✔|✔|✔|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Verwalten von Rechnungen für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Rechnungen für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Herunterladen von Rechnungen, Dateien zu Azure-Nutzung und -Gebühren, Preisblättern und Steuerdokumenten für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Verwalten von Rechnungsabschnitten für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Rechnungsabschnitte für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Erstellen eines neuen Rechnungsabschnitts für das Abrechnungsprofil|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Verwalten von Transaktionen für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Abrechnungstransaktionen für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Verwalten von Zahlungsmethoden für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen von Zahlungsmethoden für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Nachverfolgen des Azure-Guthabens für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Verwalten von Abonnements für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Azure-Abonnements für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Rollen und Aufgaben für einen Rechnungsabschnitt

Mithilfe eines Rechnungsabschnitts können Sie die Kosten auf Ihrer Rechnung organisieren. Sie können einen Abschnitt erstellen, um Ihre Kosten nach Abteilung, nach Entwicklungsumgebung oder gemäß den Organisationsanforderungen zu organisieren. Erteilen Sie anderen Benutzern die Berechtigung zum Erstellen von Azure-Abonnements für den Abschnitt. Alle Nutzungsgebühren und Einkäufe für die Abonnements werden dann im Abschnitt der Rechnung angezeigt. Weitere Informationen finden Sie unter [Grundlegendes zu Rechnungsabschnitten](../understand/mca-overview.md#invoice-sections).

In den folgenden Tabellen wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit Rechnungsabschnitten benötigen.

### <a name="manage-invoice-section-permissions-and-properties"></a>Verwalten von Berechtigungen und Eigenschaften für einen Rechnungsabschnitt

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto |
|---|---|---|---|---|---|---|---|
|Anzeigen aller Berechtigungen für den Rechnungsabschnitt|✔|✔|✔|✔|✔|✔|✔|
|Zuweisen von Berechtigungen zum Anzeigen und Verwalten des Rechnungsabschnitts für andere Benutzer|✔|✘|✘|✘|✘|✘|✘|
|Anzeigen der Eigenschaften des Rechnungsabschnitts|✔|✔|✔|✔|✔|✔|✔|
|Aktualisieren der Eigenschaften des Rechnungsabschnitts|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Verwalten von Produkten für einen Rechnungsabschnitt

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller erworbenen Produkte im Rechnungsabschnitt|✔|✔|✔|✘|✔|✔|✔|
|Verwalten der Abrechnung für Produkte für den Rechnungsabschnitt wie z. B. Stornierungen und „Automatische Verlängerung deaktivieren“|✔|✔|✘|✘|✘|✘|✘|
|Ändern des Rechnungsabschnitts für die Produkte|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Verwalten von Abonnements für den Rechnungsabschnitt

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Azure-Abonnements für den Rechnungsabschnitt|✔|✔|✔|✘|✔|✔|✔|
|Ändern des Rechnungsabschnitts für die Abonnements|✔|✔|✘|✘|✘|✘|✘|
|Anfordern des Abrechnungsbesitzes von Abonnements von Benutzern in anderen Abrechnungskonten|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Rollen und Aufgaben für die Abonnementabrechnung

In der folgenden Tabelle wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit Abonnements benötigen.

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|
|---|---|---|---|---|
|Erstellen von Azure-Abonnements|✔|✔|✘|✔|
|Aktualisieren der Kostenstelle für das Abonnement|✔|✔|✘|✘|
|Ändern des Rechnungsabschnitts für das Abonnement|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Verwalten von Abrechnungsrollen im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Wählen Sie **Zugriffssteuerung (IAM)** für einen Bereich (z. B. Abrechnungskonto, Abrechnungsprofil oder Rechnungsabschnitt) aus, für den Sie Zugriff erteilen möchten.

4. Auf der Seite „Zugriffssteuerung (IAM)“ werden die jeder Rolle für diesen Bereich zugewiesenen Benutzer und Gruppen aufgelistet.

   ![Screenshot der Liste der Administratoren für ein Abrechnungskonto](./media/understand-mca-roles/billing-list-admins.png)

5. Um einem Benutzer Zugriff zu erteilen, wählen Sie oben auf der Seite die Option **Hinzufügen** aus. Wählen Sie in der Dropdownliste „Rolle“ eine Rolle aus. Geben Sie die E-Mail-Adresse des Benutzers ein, dem Sie Zugriff erteilen möchten. Wählen Sie **Speichern** aus, um die Rolle zuzuweisen.

   ![Screenshot, der das Hinzufügen eines Administrators zu einem Abrechnungskonto zeigt](./media/understand-mca-roles/billing-add-admin.png)

6. Um den Zugriff für einen Benutzer zu entfernen, wählen Sie den Benutzer mit der Rollenzuweisung aus, den Sie entfernen möchten. Wählen Sie „Entfernen“ aus.

   ![Screenshot, der das Entfernen eines Administrators aus einem Abrechnungskonto zeigt](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um Informationen zu einem Abrechnungskonto zu erhalten:

- [Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung](../understand/mca-overview.md)
- [Erstellen eines Azure-Abonnements für Ihr Abrechnungskonto für eine Microsoft-Kundenvereinbarung](create-subscription.md)
