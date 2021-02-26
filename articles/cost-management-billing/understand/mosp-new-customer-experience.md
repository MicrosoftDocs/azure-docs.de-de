---
title: Erste Schritte mit Ihrem aktualisierten Azure-Abrechnungskonto
description: Informieren Sie sich über die ersten Schritte mit Ihrem aktualisierten Azure-Abrechnungskonto, um sich mit den Änderungen auf der neuen Benutzeroberfläche für die Abrechnung und die Kostenverwaltung vertraut zu machen.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: banders
ms.openlocfilehash: 887b7013eb3060020a39d2df0082768b8185bdde
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575465"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Erste Schritte mit Ihrem aktualisierten Azure-Abrechnungskonto

Die Verwaltung der Kosten und Rechnungen ist eine der Hauptkomponenten Ihrer Cloudumgebung. Dies ermöglicht Ihnen die Kontrolle und ein besseres Verständnis Ihrer Ausgaben in der Cloud. Zur Vereinfachung der Verwaltung Ihrer Kosten und Rechnungen aktualisiert Microsoft Ihr Azure-Abrechnungskonto, um erweiterte Funktionen für die Kostenverwaltung und Abrechnung bereitzustellen. In diesem Artikel werden die Aktualisierungen Ihres Abrechnungskontos und Schritt für Schritt die neuen Funktionen beschrieben.

> [!IMPORTANT]
> Ihr Konto wird aktualisiert, wenn Sie von Microsoft eine E-Mail zu den Aktualisierungen Ihres Kontos erhalten. Die Benachrichtigung wird 60 Tage vor der Aktualisierung Ihres Kontos gesendet.

## <a name="more-flexibility-with-your-new-billing-account"></a>Mehr Flexibilität mit Ihrem neuen Abrechnungskonto

Das folgende Diagramm enthält einen Vergleich Ihres alten und neuen Abrechnungskontos:

![Diagramm: Vergleich zwischen der Abrechnungshierarchie im alten und neuen Konto](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Ihr neues Abrechnungskonto enthält ein oder mehrere Abrechnungsprofile, mit denen Sie Ihre Rechnungen und Zahlungsmethoden verwalten können. Jedes Abrechnungsprofil enthält einen oder mehrere Rechnungsabschnitte, mit denen Sie die Kosten auf der Rechnung des Abrechnungsprofils organisieren können.

![Diagramm: Neue Abrechnungshierarchie](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

Die Rollen für das Abrechnungskonto verfügen über die höchste Berechtigungsstufe. Diese Rollen sollten Benutzern zugewiesen werden, die Rechnungen anzeigen müssen und Kosten für Ihr gesamtes Konto nachverfolgen, z. B. Finanz- oder IT-Manager einer Organisation, oder der Person, die sich für ein Konto registriert hat. Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungskonto](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Bei der Aktualisierung Ihres Kontos erhält der Benutzer, der für das alte Abrechnungskonto über die Rolle „Kontoadministrator“ verfügt, die Rolle „Besitzer“ für das neue Konto.

## <a name="billing-profiles"></a>Abrechnungsprofile

Ein Abrechnungsprofil wird verwendet, um Ihre Rechnungen und Zahlungsmethoden zu verwalten. Am Anfang jedes Kalendermonats wird in Ihrem Konto eine monatliche Rechnung für jedes Abrechnungsprofil erstellt. Die Rechnung enthält die entsprechenden Gebühren aus dem vorherigen Monat für alle Abonnements, die dem Abrechnungsprofil zugeordnet sind.

Wenn Ihr Konto aktualisiert wird, wird für jedes Abonnement automatisch ein Abrechnungsprofil erstellt. Die Gebühren für Abonnements werden für das jeweilige Abrechnungsprofil in Rechnung gestellt und auf der zugehörigen Rechnung angezeigt.

Die Rollen für die Abrechnungsprofile verfügen über Berechtigungen zum Anzeigen und Verwalten von Rechnungen und Zahlungsmethoden. Diese Rollen sollten Benutzern zugewiesen werden, die Rechnungen bezahlen. Bei diesen Benutzern handelt es sich beispielsweise um Mitglieder des Buchhaltungsteams in einer Organisation. Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Wenn Ihr Konto aktualisiert wird, gilt für jedes Abonnement, für das Sie anderen Benutzern Berechtigungen zum [Anzeigen von Rechnungen](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice) gewährt haben, Folgendes: Benutzer, die über eine Azure-Rolle vom Typ „Besitzer“, „Mitwirkender“, „Leser“ oder „Abrechnungsleser“ verfügen, erhalten für das entsprechende Abrechnungsprofil die Rolle „Leser“.

## <a name="invoice-sections"></a>Rechnungsabschnitte

Ein Rechnungsabschnitt wird verwendet, um die Kosten auf Ihrer Rechnung zu organisieren. Unter Umständen benötigen Sie beispielsweise eine einzelne Rechnung, möchten aber die Kosten nach Abteilung, Team oder Projekt organisieren. In diesem Szenario verwenden Sie ein einzelnes Abrechnungsprofil, in dem Sie einen Rechnungsabschnitt für jede Abteilung, jedes Team oder Projekt erstellen.

Bei der Aktualisierung Ihres Kontos wird für jedes Abrechnungsprofil ein Rechnungsabschnitt erstellt, und das zugehörige Abonnement wird dem Rechnungsabschnitt zugewiesen. Wenn Sie weitere Abonnements hinzufügen, können Sie zusätzliche Abschnitte erstellen und die Abonnements den Rechnungsabschnitten zuweisen. Die Abschnitte werden auf der Rechnung im Abrechnungsprofil angezeigt. Sie zeigen den Verbrauch der einzelnen Abonnements, die Sie zugewiesen haben.

Die Rollen für den Rechnungsabschnitt verfügen über Berechtigungen zum Steuern, wer Azure-Abonnements erstellen darf. Die Rollen sollten Benutzern zugewiesen werden, die die Azure-Umgebung für Teams in einer Organisation einrichten. Bei diesen Benutzern handelt es sich beispielsweise um technische Leiter und technische Architekten. Weitere Informationen finden Sie unter [Rollen und Aufgaben für einen Rechnungsabschnitt](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Erweiterte Features auf Ihrer neuen Benutzeroberfläche

Ihre neue Benutzeroberfläche enthält die folgenden Funktionen für die Kostenverwaltung und Abrechnung, die Ihnen die Verwaltung Ihrer Kosten und Rechnungen vereinfachen:

#### <a name="invoice-management"></a>Rechnungsverwaltung

**Besser vorhersagbarer monatlicher Abrechnungszeitraum**: Bei Ihrem neuen Konto beginnt der Abrechnungszeitraum am ersten Tag des Monats und endet am letzten Tag des Monats. Dies gilt unabhängig davon, wann Sie sich für die Nutzung von Azure registrieren. Eine Rechnung wird am Anfang jedes Monats generiert und enthält alle Gebühren des vorherigen Monats.

**Nur eine monatliche Rechnung für mehrere Abonnements**: Sie können flexibel wählen, ob Sie eine monatliche Rechnung für jedes Abonnement oder nur eine Gesamtrechnung für mehrere Abonnements erhalten möchten.

**Nur eine monatliche Rechnung für Azure-Abonnements, Supportpläne und Azure Marketplace-Produkte**: Sie erhalten eine monatliche Rechnung mit allen Gebühren, einschließlich der Nutzungsgebühren für Azure-Abonnements, Supportpläne und Azure Marketplace-Käufe.

**Gruppierung der Kosten auf der Rechnung nach Ihren Anforderungen**: Sie können Kosten auf Ihrer Rechnung basierend auf Ihren Anforderungen gruppieren, und zwar nach Abteilung, Projekt oder Team.

**Festlegung einer optionalen Auftragsnummer auf der Rechnung**: Legen Sie eine Auftragsnummer fest, um Ihre Rechnung Ihren internen Finanzsystemen zuzuordnen. Sie können sie im Azure-Portal jederzeit verwalten und aktualisieren.

#### <a name="payment-management"></a>Zahlungsverwaltung

**Sofortige Bezahlung von Rechnungen per Kreditkarte**: Sie müssen nicht warten, bis Ihre Kreditkarte über den automatischen Bezahlvorgang mit dem Betrag belastet wird. Sie können eine beliebige Kreditkarte verwenden, um eine fällige oder überfällige Rechnung über das Azure-Portal zu bezahlen.

**Nachverfolgung aller auf das Konto angewendeten Zahlungen**: Zeigen Sie im Azure-Portal alle Zahlungen an, die auf Ihr Konto angewendet werden, einschließlich verwendete Zahlungsmethode, bezahlter Betrag und Zahlungsdatum.

#### <a name="cost-management"></a>Kostenverwaltung

**Planung von monatlichen Exporten der Nutzungsdaten für ein Speicherkonto**: Veröffentlichen Sie Ihre Kosten- und Nutzungsdaten täglich, wöchentlich oder monatlich unter Ihrem Speicherkonto.

#### <a name="account-and-subscription-management"></a>Konto- und Abonnementverwaltung

**Zuweisung von mehreren Administratoren zur Durchführung von Abrechnungsvorgängen**: Weisen Sie mehreren Benutzern Abrechnungsberechtigungen zu, um die Abrechnung für Ihr Konto zu verwalten. Sorgen Sie für Flexibilität, indem Sie für andere Benutzer die Lese-, Schreib- oder Lese-/Schreibberechtigung erteilen.

**Direkte Erstellung von weiteren Abonnements im Azure-Portal**: Erstellen Sie Ihre gesamten Abonnements mit nur einem Auswahlvorgang im Azure-Portal.

#### <a name="api-support"></a>API-Unterstützung

**Durchführung von Vorgängen zur Abrechnung und Kostenverwaltung per API, SDK und PowerShell**: Nutzen Sie APIs für Kostenverwaltung, Abrechnung und Verbrauch, um Abrechnungs- und Kostendaten in Ihre bevorzugten Datenanalysetools zu pullen.

**Durchführung aller Abonnementvorgänge per API, SDK und PowerShell**: Verwenden Sie Azure-Abonnement-APIs, um die Verwaltung Ihrer Azure-Abonnements zu automatisieren, z. B. Erstellung, Umbenennung und Kündigung eines Abonnements.

## <a name="get-prepared-for-your-new-experience"></a>Vorbereitung auf Ihre neue Benutzeroberfläche

Für die Vorbereitung auf Ihre neue Benutzeroberfläche empfehlen wir Ihnen Folgendes:

**Monatlicher Abrechnungszeitraum und geändertes Rechnungsdatum**

Auf der neuen Benutzeroberfläche wird Ihre Rechnung jeweils ungefähr am neunten Tag des Monats generiert und enthält alle Gebühren des vorherigen Monats. Dieses Datum kann sich von dem Datum unterscheiden, an dem Ihre Rechnung unter dem alten Konto generiert wird. Falls Sie Ihre Rechnungen für andere Personen freigeben, sollten Sie diese über das geänderte Datum informieren.

**Neue APIs für Abrechnung und Kostenverwaltung**

Falls Sie APIs für die Kostenverwaltung und Abrechnung nutzen, um Ihre Abrechnungs- oder Kostendaten abzufragen, müssen Sie neue APIs verwenden. In der Tabelle unten sind die APIs, die für das neue Abrechnungskonto nicht funktionieren, und die Änderungen aufgeführt, die Sie am neuen Abrechnungskonto vornehmen müssen.

|API | Änderungen  |
|---------|---------|
|[Billing Accounts – List](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | In der API für die Auflistung von Abrechnungskonten (Billing Accounts – List) verfügt Ihr altes Abrechnungskonto für „agreementType“ über den Wert **MicrosoftOnlineServiceProgram**. In Ihrem neuen Abrechnungskonto wird **MicrosoftCustomerAgreement** als „agreementType“ verwendet. Falls Sie eine Abhängigkeit von „agreementType“ nutzen, sollten Sie diese aktualisieren. |
|[Invoices – List By Billing Subscription](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Diese API gibt nur Rechnungen zurück, die vor der Aktualisierung Ihres Kontos generiert wurden. Sie müssen die API vom Typ [Invoices – List By Billing Account](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) verwenden, um Rechnungen abzurufen, die unter Ihrem neuen Abrechnungskonto generiert werden. |

## <a name="cost-management-updates-after-account-update"></a>Cost Management-Updates nach Kontoupdate

Mit Ihrem aktualisierten Azure-Abrechnungskonto für Ihre Microsoft-Kundenvereinbarung haben Sie Zugriff auf die neuen und erweiterten Cost Management-Benutzeroberflächen im Azure-Portal, die bei Verwendung des Kontos mit nutzungsbasierter Zahlung noch nicht verfügbar waren.

### <a name="new-capabilities"></a>Neue Funktionen

Die folgenden neuen Funktionen sind unter Ihrem Azure-Abrechnungskonto verfügbar.

#### <a name="new-billing-scopes"></a>Neue Abrechnungsbereiche

In Ihrem aktualisierten Konto verfügen Sie unter „Kostenverwaltung + Abrechnung“ über neue Bereiche. Diese sind nicht nur beim Aufbau einer hierarchischen Organisation und der Fakturierung hilfreich, sondern ermöglichen auch die Anzeige von kombinierten Gebühren für mehrere zugrunde liegende Abonnements. Weitere Informationen zu Abrechnungsbereichen finden Sie unter [Bereiche für Microsoft-Kundenvereinbarung](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

Sie können auch auf Cost Management-APIs zugreifen, um kombinierte Kostenübersichten für höhere Bereiche zu erhalten. Alle Cost Management-APIs, für die der Abonnementbereich genutzt wird, sind weiterhin verfügbar und weisen nur geringfügige Änderungen in Bezug auf das Schema auf. Weitere Informationen zu den APIs finden Sie in den Artikeln zu [Azure Cost Management-APIs](/rest/api/cost-management/) und [Azure-Nutzungs-APIs](/rest/api/consumption/).

#### <a name="cost-allocation"></a>Kostenzuteilung

Mit Ihrem aktualisierten Konto können Sie Funktionen für die Kostenzuteilung verwenden, um die Kosten für gemeinsam genutzte Dienste in Ihrer Organisation zu verteilen. Weitere Informationen zur Kostenzuteilung finden Sie unter [Erstellen und Verwalten von Azure-Kostenzuteilungsregeln (Vorschauversion)](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

Mit dem Azure Cost Management-Connector für Power BI Desktop können Sie benutzerdefinierte Visualisierungen und Berichte zur Nutzung und den Ausgaben in Azure erstellen. Sie greifen auf Ihre Kosten- und Nutzungsdaten zu, nachdem Sie eine Verbindung mit dem aktualisierten Konto hergestellt haben. Weitere Informationen zum Azure Cost Management-Connector für Power BI Desktop finden Sie unter [Erstellen von visuellen Elementen und Berichten mit dem Azure Cost Management-Connector in Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Aktualisierte Funktionen

Die folgenden aktualisierten Funktionen sind unter Ihrem Azure-Abrechnungskonto verfügbar.

#### <a name="cost-analysis"></a>Kostenanalyse

Sie können weiterhin Ihre Verbrauchskosten pro Monat anzeigen und nachverfolgen und jetzt zusätzlich die Kosten für Reservierungen und Marketplace-Käufe in der Kostenanalyse anzeigen.

Bei Verwendung Ihres aktualisierten Kontos erhalten Sie nur eine Rechnung für alle Azure-Gebühren. Darüber hinaus verfügen Sie jetzt auch über eine vereinfachte monatliche Kalenderansicht, die die vorherige Ansicht mit den Abrechnungszeiträumen ersetzt.

Wenn der Abrechnungszeitraum für Ihr altes Konto beispielsweise der Zeitraum vom 24. November bis zum 23. Dezember war, gilt nach dem Upgrade der Zeitraum 1. November bis 30. November, 1. Dezember bis 31. Dezember usw.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Abbildung: Vergleich zwischen alten und neuen Abrechnungszeiträumen" lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Budgets

Sie können jetzt Budgets für das Abrechnungskonto erstellen, um die Kosten übergreifend für die Abonnements nachverfolgen zu können. Mit Budgets sind Sie auch in Bezug auf Ihre Kaufgebühren immer auf dem aktuellen Stand. Weitere Informationen zu Budgets finden Sie unter [Erstellen und Verwalten von Azure-Budgets](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>Exports

Ihr neues Abrechnungskonto verfügt über verbesserte Exportfunktionen. Beispielsweise können Sie Exporte für die tatsächlichen Kosten erstellen, in denen Kaufbeträge oder die amortisierten Kosten (auf den Kaufzeitraum verteilte Kosten für Reservierungskäufe) enthalten sind. Sie können auch einen Export für das Abrechnungskonto erstellen, um die Daten zur Nutzung und zu Käufen für alle Abonnements des Abrechnungskontos zu erhalten. Weitere Informationen zu Exporten finden Sie unter [Erstellen und Verwalten von exportierten Daten](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> Bei Exporten vom Typ **Monatlicher Export der Kosten des letzten Monats**, die vor Ihrem Kontoupdate erstellt wurden, werden die Daten für den letzten Kalendermonat und nicht für den letzten Abrechnungszeitraum exportiert.

Bei einem Abrechnungszeitraum vom 23. Dezember bis zum 22. Januar enthält die exportierte CSV-Datei beispielsweise die Kosten- und Nutzungsdaten für diesen Zeitraum. Nach dem Update enthält der Export Daten für den Kalendermonat. Beispiel: 1. Januar bis 31. Januar usw.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Abbildung: Vergleich zwischen alten und neuen Exportdetails" lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Zusätzliche Informationen

Die folgenden Abschnitte enthalten zusätzliche Informationen zu Ihrer neuen Benutzeroberfläche.

**Keine Ausfallzeiten für Dienste** Für die Azure-Dienste unter Ihrem Abonnement kommt es nicht zu Unterbrechungen. Es wird lediglich Ihre Benutzeroberfläche für die Abrechnung aktualisiert. Dies hat keinerlei Auswirkungen auf vorhandene Ressourcen, Ressourcengruppen oder Verwaltungsgruppen.

**Keine Änderungen an Azure-Ressourcen** Der Zugriff auf Azure-Ressourcen, die per rollenbasierter Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) festgelegt wurden, wird durch die Aktualisierung nicht beeinträchtigt.

**Frühere Rechnungen sind auf der neuen Benutzeroberfläche verfügbar** Rechnungen, die vor der Aktualisierung Ihres Kontos generiert wurden, sind im Azure-Portal weiterhin verfügbar.

**Rechnungen für das Konto werden Mitte des Monats aktualisiert** Wenn Ihr Konto Mitte des Monats aktualisiert wird, erhalten Sie eine Rechnung über die Gebühren, die bis zum Tag der Kontoaktualisierung angefallen sind. Sie erhalten dann eine weitere Rechnung für den Rest des Monats. Angenommen, Ihr Konto verfügt über ein Abonnement und wird am 15. September aktualisiert. Sie erhalten dann eine Rechnung über die Gebühren, die bis zum 15. September angefallen sind. Anschließend erhalten Sie eine weitere Rechnung für den Zeitraum zwischen dem 15. und 30. September. Für die weiteren Monate erhalten Sie dann eine Rechnung pro Monat.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Ihrem Abrechnungskonto erhalten Sie in den folgenden Artikeln:

- [Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure](../manage/understand-mca-roles.md)
- [Erstellen eines zusätzlichen Azure-Abonnements](../manage/create-subscription.md)
- [Erstellen von Abschnitten in Ihrer Rechnung zum Organisieren von Kosten](../manage/mca-section-invoice.md)