---
title: Einrichten Ihres Abrechnungskontos für eine Microsoft-Kundenvereinbarung – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihr Abrechnungskonto für eine Microsoft-Kundenvereinbarung einrichten.
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
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 284480ab8b9973c02f365dbf3ebfd3a103ef4b42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64919148"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Einrichten Ihres Abrechnungskontos für eine Microsoft-Kundenvereinbarung

Wenn Ihre Enterprise Agreement-Registrierung abgelaufen ist oder bald ablaufen wird, können Sie eine Microsoft-Kundenvereinbarung zum Erneuern Ihrer Registrierung unterzeichnen. Die Erneuerung umfasst die folgenden Schritte:

1. Akzeptieren Sie die neue Microsoft-Kundenvereinbarung. Lassen Sie sich von Ihrem lokalen Microsoft-Kundenbetreuer die Details erläutern, und akzeptieren Sie die neue Vereinbarung.
2. Richten Sie das neue Abrechnungskonto ein, das für die neue Microsoft-Kundenvereinbarung erstellt wird.

Dieser Artikel beschreibt die Änderungen an der vorhandenen Abrechnung nach der Einrichtung und führt Sie durch die Einrichtung Ihres neuen Abrechnungskontos.

Zum Einrichten des Abrechnungskontos müssen Sie die Abrechnung von Azure-Abonnements von Ihrer Enterprise Agreement-Registrierung auf das neue Konto umstellen. Die Einrichtung hat keine Auswirkungen auf Azure-Dienste, die in Ihren Abonnements ausgeführt werden. Es ändert sich jedoch die Art und Weise, wie Sie die Abrechnung Ihre Abonnements verwalten.

- Statt im [EA-Portal](https://ea.azure.com) verwalten Sie Ihre Azure-Dienste und die Abrechnung im [Azure-Portal](https://portal.azure.com).
- Sie erhalten monatlich eine digitale Rechnung über Ihre Gebühren. Sie können die Rechnung auf der Azure-Seite „Kostenverwaltung + Abrechnung“ anzeigen und analysieren.
- Anstelle von Abteilungen und Konto in Ihrer Enterprise Agreement-Registrierung verwenden Sie die Abrechnungsstruktur und Bereiche des neuen Kontos zum Verwalten und Organisieren Ihrer Abrechnung.

Bevor Sie mit dem Einrichten beginnen, sollten Sie sich mit folgenden Themen befassen:

- **Erläuterungen zum neuen Abrechnungskonto**
  - Das neue Konto vereinfacht die Abrechnung für Ihre Organisation. [Kurze Übersicht über Ihr neues Abrechnungskonto](billing-mca-overview.md).
- **Überprüfen des Zugriffs zum Abschließen der Einrichtung**
  - Nur Benutzer mit bestimmten Administratorberechtigungen können die Einrichtung abschließen. Überprüfen Sie, ob Sie über den [erforderlichen Zugriff zum Abschließen der Einrichtung](#access-required-to-complete-the-setup) verfügen.
- **Erläuterungen zu den Änderungen an Ihrer Abrechnungshierarchie**
  - Ihr neues Abrechnungskonto ist anders organisiert als Ihre Enterprise Agreement-Registrierung. [Erläuterungen zu den Änderungen an Ihrer Abrechnungshierarchie beim neuen Konto](#understand-changes-to-your-billing-hierarchy).
- **Erläuterungen zu den Änderungen am Zugriff Ihrer Abrechnungsadministratoren**
  - Administratoren Ihrer Enterprise Agreement-Registrierung erhalten Zugriff auf die Abrechnungsbereiche des neuen Kontos. [Erläuterungen zu den Änderungen beim Zugriff](#understand-changes-to-your-billing-administrators-access).
- **Informationen zu Enterprise Agreement-Funktionen, die durch das neue Konto ersetzt werden**
  - Informieren Sie sich über Funktionen der Enterprise Agreement-Registrierung, die durch Funktionen des neuen Kontos ersetzt werden.
- **Antworten auf die am häufigsten gestellten Fragen**
  - Lesen Sie [zusätzliche Informationen](#additional-information), um mehr über die Einrichtung zu erfahren.

## <a name="access-required-to-complete-the-setup"></a>Erforderlicher Zugriff zum Abschließen der Einrichtung

Zum Abschließen der Einrichtung benötigen Sie die folgenden Zugriffsrechte:

- Besitzer des Abrechnungsprofils, das bei Unterzeichnung der Microsoft-Kundenvereinbarung erstellt wurde. Weitere Informationen zu Abrechnungsprofilen finden Sie unter [Grundlegendes zu Abrechnungsprofilen](billing-mca-overview.md#understand-billing-profiles).

- Unternehmensadministrator für die Registrierung, die erneuert wird.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Wenn Sie kein Unternehmensadministrator für die Registrierung sind

Sie können die Unternehmensadministratoren für die Registrierung bitten, die Einrichtung Ihres Abrechnungskontos abzuschließen.

1. Melden Sie sich beim Azure-Portal über den Link in der E-Mail an, die bei Unterzeichnung der Microsoft-Kundenvereinbarung an Sie gesendet wurde.

2. Wenn eine andere Person in Ihrer Organisation die Vereinbarung unterzeichnet hat oder Sie nicht über die E-Mail verfügen, melden Sie sich über den folgenden Link an. Ersetzen Sie **enrollmentNumber** durch die Nummer Ihrer Enterprise Agreement-Registrierung, die erneuert wurde.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Wählen Sie die Unternehmensadministratoren aus, denen Sie die Anforderung senden möchten.

   ![Screenshot der Einladung von Unternehmensadministratoren](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Klicken Sie auf **Anforderung senden**.

   Die Administratoren erhalten eine E-Mail mit Anweisungen zum Abschließen der Einrichtung.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Wenn Sie kein Besitzer des Abrechnungsprofils sind

Der Benutzer in Ihrer Organisation, der die Microsoft-Kundenvereinbarung unterzeichnet hat, wird als der Besitzer des Abrechnungsprofils hinzugefügt. Bitten Sie den Benutzer, Sie als Besitzer hinzuzufügen, damit Sie die Einrichtung abschließen können.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Erläuterungen zu den Änderungen an Ihrer Abrechnungshierarchie

Das neue Abrechnungskonto vereinfacht die Abrechnung für Ihre Organisation und bietet gleichzeitig verbesserte Funktionen für Abrechnung und Kostenverwaltung. Das folgende Diagramm verdeutlicht, wie die Abrechnung beim neuen Abrechnungskonto organisiert ist.

![Abbildung der Hierarchie nach Umstellung von EA auf MCA](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Mit dem Abrechnungskonto verwalten Sie die Abrechnung für Ihre Microsoft-Kundenvereinbarung. Weitere Informationen zum Abrechnungskonto finden Sie unter [Grundlegendes zum Abrechnungskonto](billing-mca-overview.md#understand-billing-account).
2. Mit dem Abrechnungsprofil verwalten Sie die Abrechnung für Ihre Organisation ähnlich wie bei der Enterprise Agreement-Registrierung. Unternehmensadministratoren werden Besitzer des Abrechnungsprofils. Weitere Informationen zu Abrechnungsprofilen finden Sie unter [Grundlegendes zu Abrechnungsprofilen](billing-mca-overview.md#understand-billing-profiles).
3. Sie verwenden einen Rechnungsabschnitt zum Organisieren Ihrer Kosten entsprechend Ihren Anforderungen. Dies ähnelt den Abteilungen in Ihrer Enterprise Agreement-Registrierung. Abteilungen werden zu Rechnungsabschnitten und Abteilungsadministratoren zu Besitzern der jeweiligen Rechnungsabschnitte. Weitere Informationen zu Rechnungsabschnitten finden Sie unter [Grundlegendes zu Rechnungsabschnitten](billing-mca-overview.md#understand-invoice-sections).
4. Die Konten, die in Ihrem Enterprise Agreement erstellt wurden, werden beim neuen Abrechnungskonto nicht unterstützt. Die Abonnements des Kontos gehören dem jeweiligen Rechnungsabschnitt für die entsprechende Abteilung an. Kontobesitzer können Abonnements für ihre Rechnungsabschnitte erstellen und verwalten.

## <a name="understand-changes-to-your-billing-administrators-access"></a>Erläuterungen zu den Änderungen am Zugriff Ihrer Abrechnungsadministratoren

Je nach Zugriffsrechten erhalten die Abrechnungsadministratoren Ihrer Enterprise Agreement-Registrierung Zugriff auf die Abrechnungsbereiche des neuen Kontos. Die folgende Tabelle erläutert die Änderung des Zugriffs während der Einrichtung:

| Bestehende Rolle | Rolle nach der Umstellung |
| --- | --- |
| **Unternehmensadministrator (Schreibgeschützt = Nein)** | **– Besitzer des Abrechnungsprofils** </br> Verwalten aller Einstellungen für das Abrechnungsprofil </br> - **Besitzer aller Rechnungsabschnitte** </br> Verwalten aller Einstellungen für die Rechnungsabschnitte |
| **Unternehmensadministrator (Schreibgeschützt = Ja)** | **– Benutzer mit Leseberechtigung für das Abrechnungsprofil** </br> – Schreibgeschützte Ansicht aller Einstellungen für das Abrechnungskonto</br>**– Benutzer mit Leseberechtigung für alle Rechnungsabschnitte**</br> – Schreibgeschützte Ansicht aller Einstellungen für den Rechnungsabschnitt|
| **Abteilungsadministrator (Schreibgeschützt = Nein)** |**– Besitzer des für die jeweilige Abteilung erstellten Rechnungsabschnitts** </br>Verwalten aller Einstellungen für den Rechnungsabschnitt|
| **Abteilungsadministrator (Schreibgeschützt = Ja)**|**– Benutzer mit Leseberechtigung für den für die jeweilige Abteilung erstellten Rechnungsabschnitt**</br> Schreibgeschützte Ansicht aller Einstellungen für den Rechnungsabschnitt|
| **Kontobesitzer** | **– Azure-Abonnementersteller für den für die jeweilige Abteilung erstellten Rechnungsabschnitt** </br>  Erstellen von Azure-Abonnements für den jeweiligen Rechnungsabschnitt|

Bei Unterzeichnung der Microsoft-Kundenvereinbarung wird ein Azure Active Directory-Mandant für das neue Abrechnungskonto ausgewählt. Wenn für Ihre Organisation kein Mandant vorhanden ist, wird ein neuer Mandant erstellt. Der Mandant stellt Ihre Organisation innerhalb von Azure Active Directory dar. Globale Mandantenadministratoren in Ihrer Organisation verwenden den Mandanten, um den Zugriff auf Anwendungen und Daten in Ihrer Organisation zu verwalten.

Ihr neues Konto unterstützt nur Benutzer des Mandanten, der beim Unterzeichnen der Microsoft-Kundenvereinbarung ausgewählt wurde. Wenn Benutzer mit Administratorberechtigungen für Ihr Enterprise Agreement Teil des Mandanten sind, erhalten sie während der Einrichtung Zugriff auf das neue Abrechnungskonto. Wenn sie nicht Teil des Mandanten sind, können sie nur auf das neue Abrechnungskonto zugreifen, wenn sie von Ihnen eingeladen werden.

Beim Einladen von Benutzern werden diese dem Mandanten als Gastbenutzer hinzugefügt und erhalten Zugriff auf das Abrechnungskonto. Zum Einladen der Benutzer muss der Gastzugriff für den Mandanten aktiviert sein. Weitere Informationen finden Sie unter [Steuern des Gastzugriffs in Azure Active Directory](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Wenn der Gastzugriff deaktiviert ist, wenden Sie sich an die globalen Administratoren Ihres Mandanten, um ihn aktivieren zu lassen. <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>Informationen zu Funktionen, die durch das neue Abrechnungskonto ersetzt werden

Die folgenden Enterprise Agreement-Funktionen werden durch neue Funktionen im Abrechnungskonto für eine Microsoft-Kundenvereinbarung ersetzt.

### <a name="enterprise-agreement-accounts"></a>Enterprise Agreement-Konten

Die bei Ihrer Enterprise Agreement-Registrierung erstellten Konten werden beim neuen Abrechnungskonto nicht unterstützt. Die Abonnements des Kontos gehören dem Rechnungsabschnitt an, der für die jeweilige Abteilung erstellt wurde. Kontobesitzer werden zu Azure-Abonnementerstellern und können Abonnements für ihre Rechnungsabschnitte erstellen und verwalten.

### <a name="notification-contacts"></a>Benachrichtigungskontakte

Benachrichtigungskontakte erhalten E-Mail-Benachrichtigungen über das Azure Enterprise Agreement. Beim neuen Abrechnungskonto werden sie nicht unterstützt. E-Mail-Nachrichten zu Azure-Gutschriften und Rechnungen werden an Benutzer gesendet, die Zugriff auf Abrechnungsprofile in Ihrem Abrechnungskonto haben.

### <a name="spending-quotas"></a>Ausgabenkontingente

Ausgabenkontingente, die für Abteilungen in Ihrer Enterprise Agreement-Registrierung festgelegt wurden, werden durch Budgets im neuen Abrechnungskonto ersetzt. Ein Budget wird für jedes Ausgabenkontingent erstellt, das für Abteilungen in Ihrer Registrierung festgelegt wurde. Weitere Informationen zu Budgets finden Sie unter [Erstellen und Verwalten von Azure-Budgets](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Kostenstellen

Kostenstellen, die für die Azure-Abonnements in Ihrer Enterprise Agreement-Registrierung festgelegt wurden, werden in das neue Abrechnungskonto übernommen. Kostenstellen für Abteilungen und Enterprise Agreement-Konten werden jedoch nicht unterstützt.

## <a name="additional-information"></a>Zusätzliche Informationen

Die folgenden Abschnitte enthalten weitere Informationen zum Einrichten Ihres Abrechnungskontos.

### <a name="no-service-downtime"></a>Keine Dienstunterbrechung

Die Azure-Dienste in Ihrem Abonnement werden ohne Unterbrechung ausgeführt. Wir ändern nur die Abrechnungszusammenhänge in Ihren Azure-Abonnements. Dies wirkt sich nicht auf vorhandene Ressourcen, Ressourcengruppen oder Verwaltungsgruppen aus.

### <a name="user-access-to-azure-resources"></a>Benutzerzugriff auf Azure-Ressourcen

Der Zugriff auf Azure-Ressourcen, der mithilfe von Azure RBAC (Role Based Access Control, rollenbasierte Zugriffssteuerung) festgelegt wurde, ist während der Umstellung nicht beeinträchtigt.

### <a name="azure-reservations"></a>Azure-Reservierungen

Falls Sie Azure-Reservierungen in Ihrer Enterprise Agreement-Registrierung haben, werden diese zum neuen Abrechnungskonto verschoben. Während des Wechsels ergeben sich keine Änderungen an den Reservierungsrabatten, die für Ihre Abonnements gelten.

### <a name="azure-marketplace-products"></a>Azure Marketplace-Produkte

Alle Azure Marketplace-Produkte in Ihrer Enterprise Agreement-Registrierung werden zusammen mit den Abonnements verschoben. Während der Umstellung ergeben sich keine Änderungen in Hinsicht auf den Dienstzugriff der Marketplace-Produkte.

### <a name="support-plan"></a>Supportplan

Supportvorteile werden im Rahmen der Umstellung nicht übertragen. Erwerben Sie einen neuen Supportplan, um Vorteile für Azure-Abonnements bei Ihrem neuen Abrechnungskonto zu erhalten.

### <a name="past-charges-and-balance"></a>Ältere Kosten und Guthaben

Kosten und Guthaben vor der Umstellung können in Ihrer Enterprise Agreement-Registrierung über das Azure-Portal angezeigt werden. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Wann sollte die Einrichtung abgeschlossen werden?

Schließen Sie die Einrichtung Ihres Abrechnungskontos vor Ablauf Ihrer Enterprise Agreement-Registrierung ab. Wenn Ihre Registrierung abläuft, werden Dienste in Ihren Azure-Abonnements weiterhin ohne Unterbrechung ausgeführt. Allerdings werden die Dienste dann nach Einzelhandelspreisen abgerechnet.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Änderungen an der Enterprise Agreement-Registrierung nach der Einrichtung

Azure-Abonnements, die nach der Umstellung für die Enterprise Agreement-Registrierung erstellt werden, können manuell in das neue Abrechnungskonto verschoben werden kann. Weitere Informationen finden Sie unter [Übernehmen des Abrechnungsbesitzes für Azure-Abonnements von anderen Benutzern](billing-mca-request-billing-ownership.md). Wenn Sie Azure-Reservierungen verschieben möchten, die nach der Umstellung erworben wurden, [wenden Sie sich an den Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Sie können Benutzern auch nach der Umstellung Zugriff auf das Abrechnungskonto bereitstellen. Weitere Informationen finden Sie unter [Verwalten von Abrechnungsrollen im Azure-Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="revert-the-transition"></a>Rückgängigmachen der Umstellung

Die Umstellung kann nicht rückgängig gemacht werden. Sobald die Abrechnung Ihrer Azure-Abonnements auf das neue Abrechnungskonto umgestellt wurde, können Sie nicht mehr zur Enterprise Agreement-Registrierung zurückwechseln.

### <a name="closing-your-browser-during-setup"></a>Schließen des Browsers während der Einrichtung

Bevor Sie auf **Umstellung starten** klicken, können Sie den Browser schließen. Sie können über den Link, den Sie in der E-Mail erhalten haben, zur Einrichtung zurückkehren und die Umstellung starten. Wenn Sie den Browser nach dem Starten der Umstellung schließen, wird die Umstellung weiter ausgeführt. Kehren Sie zur Seite mit dem Umstellungsstatus zurück, um den aktuellen Status der Umstellung zu überwachen. Sie erhalten eine E-Mail, wenn die Umstellung abgeschlossen ist.

## <a name="complete-the-setup-in-the-azure-portal"></a>Abschließen der Einrichtung im Azure-Portal

Zum Abschließen der Einrichtung benötigen Sie Zugriff sowohl auf das neue Abrechnungskonto als auch die Enterprise Agreement-Registrierung. Weitere Informationen finden Sie unter [Erforderlicher Zugriff zum Abschließen der Einrichtung Ihres Abrechnungskontos](#access-required-to-complete-the-setup).

1. Melden Sie sich beim Azure-Portal über den Link in der E-Mail an, die bei Unterzeichnung der Microsoft-Kundenvereinbarung an Sie gesendet wurde.

2. Wenn eine andere Person in Ihrer Organisation die Vereinbarung unterzeichnet hat oder Sie nicht über die E-Mail verfügen, melden Sie sich über den folgenden Link an. Ersetzen Sie **enrollmentNumber** durch die Nummer Ihrer Enterprise Agreement-Registrierung, die erneuert wurde.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Wählen Sie **Umstellung starten** im letzten Schritt der Einrichtung aus. Nach dem Starten der Umstellung geschieht Folgendes:

    ![Screenshot des Setup-Assistenten](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - Eine Abrechnungshierarchie entsprechend Ihrer Enterprise Agreement-Hierarchie wird im neuen Abrechnungskonto erstellt. Weitere Informationen finden Sie unter [Erläuterungen zu den Änderungen an Ihrer Abrechnungshierarchie](#understand-changes-to-your-billing-hierarchy).
    - Administratoren Ihrer Enterprise Agreement-Registrierung erhalten Zugriff auf das neue Abrechnungskonto, damit sie weiterhin die Abrechnung für Ihre Organisation verwalten können.
    - Die Abrechnung Ihrer Azure-Abonnements wird an das neue Konto übergeben. **Während der Umstellung werden Ihre Azure-Dienste nicht beeinträchtigt. Sie werden ohne jegliche Unterbrechung weiterhin ausgeführt.**
    - Wenn Sie über Azure-Reservierungen verfügen, werden diese mit den gleichen Rabatten und Bedingungen in das neue Abrechnungskonto verschoben. Ihr Reservierungsrabatt wird während der Umstellung weiter angewendet.

4. Sie können den Status der Umstellung auf der Seite **Status der Abrechnungsumstellung** überwachen.

   ![Screenshot mit dem Status der Abrechnungsumstellung](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>Überprüfen der ordnungsgemäßen Einrichtung des Abrechnungskontos

 Überprüfen Sie Folgendes, um sicherzustellen, dass Ihr neues Abrechnungskonto ordnungsgemäß eingerichtet ist:

### <a name="azure-subscriptions"></a>Azure-Abonnements

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wählen Sie das Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** und dann das Abrechnungsprofil aus.

4. Wählen Sie auf der linken Seite **Azure-Abonnements** aus.

   ![Screenshot mit der Liste der Abonnements](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Azure-Abonnements, die von Ihrer Enterprise Agreement-Registrierung auf das neue Abrechnungskonto umgestellt werden, werden auf der Seite mit den Azure-Abonnements angezeigt. Wenn Sie der Meinung sind, dass ein Abonnement fehlt, stellen Sie die Abrechnung des Abonnements manuell im Azure-Portal um. Weitere Informationen finden Sie unter [Übernehmen des Abrechnungsbesitzes für Azure-Abonnements von anderen Benutzern](billing-mca-request-billing-ownership.md).

### <a name="azure-reservations"></a>Azure-Reservierungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wählen Sie einen Rechnungsabschnitt aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto oder ein Abrechnungsprofil auswählen.  Wählen Sie im Abrechnungskonto oder Abrechnungsprofil die Option **Rechnungsabschnitte** und dann einen Rechnungsabschnitt aus.

    ![Screenshot mit der Liste der Rechnungsabschnitte nach der Umstellung](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Wählen Sie auf der linken Seite **Alle Produkte** aus.

5. Suchen Sie nach **Reserviert**.

    ![Screenshot mit der Liste der Abonnements nach der Umstellung](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Azure-Reservierungen, die von Ihrer Enterprise Agreement-Registrierung in das neue Abrechnungskonto verschoben werden, werden auf der Seite „Alle Produkte“ angezeigt. Wiederholen Sie die Schritte für alle Rechnungsabschnitte, um sicherzustellen, dass alle Azure-Reservierungen von Ihrer Enterprise Agreement-Registrierung verschoben werden. Wenn Sie der Meinung sind, dass eine Azure-Reservierung fehlt, [wenden Sie sich an den Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um die Reservierung in das neue Abrechnungskonto verschieben zu lassen.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Zugriff von Unternehmensadministratoren auf das Abrechnungsprofil

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wählen Sie das Abrechnungsprofil aus, das für Ihre Registrierung erstellt wurde. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen.  Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** und dann das Abrechnungsprofil aus.

4. Wählen Sie auf der linken Seite **Zugriffssteuerung (IAM)** aus.

   ![Screenshot mit dem Zugriff von Unternehmensadministratoren nach der Umstellung](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Unternehmensadministratoren sind als Besitzer des Abrechnungsprofils aufgeführt, während Unternehmensadministratoren mit Nur-Lese Berechtigungen als Benutzer mit Leseberechtigung für das Abrechnungsprofil aufgeführt sind. Wenn Sie der Meinung sind, dass der Zugriff für einen Unternehmensadministrator fehlt, können Sie diesem im Azure-Portal die Zugriffsberechtigung erteilen. Weitere Informationen finden Sie unter [Verwalten von Abrechnungsrollen im Azure-Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Zugriff von Unternehmensadministratoren, Abteilungsadministratoren und Kontobesitzern auf Rechnungsabschnitte

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Wählen Sie einen Rechnungsabschnitt aus. Rechnungsabschnitte haben die gleichen Namen wie die jeweiligen Abteilungen in Enterprise Agreement-Registrierungen. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungsprofil oder ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungsprofil oder Abrechnungskonto die Option **Rechnungsabschnitte** aus, und wählen Sie dann einen Rechnungsabschnitt aus.

   ![Screenshot mit der Liste der Rechnungsabschnitte nach der Umstellung](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Wählen Sie auf der linken Seite **Zugriffssteuerung (IAM)** aus.

    ![Screenshot mit dem Zugriff von Abteilungs- und Kontoadministratoren nach der Umstellung](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Unternehmensadministratoren und Abteilungsadministratoren sind als Besitzer des Rechnungsabschnitts oder Benutzer mit Leseberechtigung für den Rechnungsabschnitt aufgeführt, während Kontobesitzer in der Abteilung als Azure-Abonnementersteller aufgeführt sind. Wiederholen Sie den Schritt für alle Rechnungsabschnitte, um den Zugriff für alle Abteilungen in Ihrer Enterprise Agreement-Registrierung zu überprüfen. Kontobesitzer, die nicht Teil einer Abteilung waren, erhalten die Berechtigungen für einen Rechnungsabschnitt mit dem Namen **Standardrechnungsabschnitt**. Wenn Sie der Meinung sind, dass der Zugriff für einen Administrator fehlt, können Sie diesem im Azure-Portal die Zugriffsberechtigung erteilen. Weitere Informationen finden Sie unter [Verwalten von Abrechnungsrollen im Azure-Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit Ihrem neuen Abrechnungskonto](billing-mca-overview.md)

- [Ausführen von Enterprise Agreement-Aufgaben in Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung](billing-mca-enterprise-operations.md)

- [Verwalten des Zugriffs auf Ihr Abrechnungskonto](billing-understand-mca-roles.md)
