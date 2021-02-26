---
title: Erste Schritte mit Azure Cost Management für Partner
description: In diesem Artikel wird erläutert, wie Partner Features von Azure Cost Management verwenden und den Zugriff auf Cost Management für ihre Kunden aktivieren.
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 40c683d98a13bf4810ec4575af3fba5ae64961f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946990"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Erste Schritte mit Azure Cost Management für Partner

Azure Cost Management ist für direkte Partner, die ihre Kunden in eine Microsoft-Kundenvereinbarung aufgenommen und [einen Azure-Plan erworben](/partner-center/purchase-azure-plan) haben, nativ verfügbar. In diesem Artikel wird erläutert, wie Partner mithilfe der Funktionen von [Azure Cost Management](../index.yml) Kosten für Abonnements im Azure-Plan einsehen. Außerdem wird beschrieben, wie Partner ihren Kunden den Zugriff auf Cost Management zu Einzelhandelspreisen ermöglichen.

Bei direkten Partnern und indirekten Anbietern können der globale Administrator und die Administrator-Agents zu den fakturierten Preisen im Partnermandanten auf Cost Management zugreifen und die Kosten verwalten.

Handelspartner und Kunden können im Kundenmandanten auf Cost Management zugreifen und die Verbrauchskosten für die Abonnements anzeigen. Die Kosten werden in dieser Ansicht zu Einzelhandelspreisen berechnet und angezeigt. Allerdings müssen sie über Azure RBAC-Zugriff auf das Abonnement im Kundenmandanten verfügen, um Kosten anzeigen zu können. Die Richtlinie für die Sichtbarkeit von Kosten muss vom Anbieter für den Kundenmandanten aktiviert werden.

Kunden können Cost Management-Funktionen verwenden, wenn sie von ihrem CSP-Partner aktiviert werden.

CSP-Partner verwenden Cost Management für folgende Zwecke:

- Erläuterung in Rechnung gestellter Kosten und Zuordnung der Kosten zu Kunden, Abonnements, Ressourcengruppen und Diensten.
- Gewinnen einer intuitiven Übersicht über die Azure-Kosten in der [Kostenanalyse ](quick-acm-cost-analysis.md) mit Funktionen zur Analyse der Kosten nach Kunde, Abonnement, Ressourcengruppe, Ressource, Verbrauchseinheit, Dienst und vielen anderen Dimensionen.
- Anzeigen von Ressourcenkosten, für die ein Partner Earned Credit (PEC) in der Kostenanalyse angewendet wird.
- Einrichten von Benachrichtigungen und Automatisierung mithilfe programmgesteuerter [Budgets](tutorial-acm-create-budgets.md) und Warnungen, wenn die Kosten Budgets überschreiten.
- Aktivieren der Azure Resource Manager-Richtlinie, die dem Kunden Zugriff auf Cost Management-Daten ermöglicht. Kunden können dann die Verbrauchskostendaten für ihre Abonnements mit [nutzungsbasierten Tarifen](https://azure.microsoft.com/pricing/calculator/) anzeigen.
- Exportieren ihrer Daten zu Kosten und Nutzung eines Abonnements mit nutzungsbasierter Bezahlung in ein Speicherblob.

Nachstehend finden Sie ein Beispiel, das die Kosten für alle Kunden anzeigt.

[![Beispiel: Kosten für alle Kunden](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

Das folgende Beispiel zeigt die Kosten für einen einzelnen Kunden an.

[![Beispiel: Kosten für einen einzelnen Kunden](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Alle Funktionen, die in Azure Cost Management verfügbar sind, sind auch mit Rest-APIs verfügbar. Verwenden Sie die APIs, um Kostenverwaltungsaufgaben zu automatisieren.

## <a name="prerequisites"></a>Voraussetzungen

Für Partner ist Azure Cost Management nur für Abonnements nativ verfügbar, für die der Azure-Plan gilt.

Wenn Sie Azure Cost Management im Azure-Portal aktivieren möchten, müssen Sie die Microsoft-Kundenvereinbarung im Namen des Kunden akzeptiert und den Kunden in den Azure-Plan übertragen haben. Nur die Kosten für Abonnements, die in den Azure-Plan übertragen wurden, sind in Azure Cost Management verfügbar.

Azure Cost Management erfordert Lesezugriff auf Ihr Abrechnungskonto oder Ihr Abonnement.

Weitere Informationen zum Aktivieren und Zuweisen der Zugriffsrechte auf Azure Cost Management für ein Abrechnungskonto finden Sie unter [Zuweisen von Benutzerrollen und Berechtigungen](/partner-center/permissions-overview). Die Rollen **Globaler Administrator** und **Administrator-Agent** können die Kosten für ein Abrechnungskonto verwalten.

Damit der Zugriff auf Azure Cost Management in den Abonnementbereichen möglich ist, können alle Benutzer, die über Azure RBAC-Zugriff auf ein Abonnement verfügen, Kosten zu Einzelhandelspreisen (nutzungsbasierte Bezahlung) anzeigen. Die [Richtlinie für die Sichtbarkeit von Kosten muss für den Kundenmandanten](#enable-the-policy-to-view-azure-usage-charges) aber aktiviert sein. Eine vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](understand-cost-mgt-data.md).

Beim Übertragen von vorhandenen Abrechnungsvereinbarungen auf einen neuen Partner sind die Cost Management-Funktionen nur für die aktuelle Abrechnungsbeziehung mit dem Partner verfügbar. Die vor der Übertragung auf den neuen Partner angefallenen Kosten werden nicht in das neue Abrechnungskonto übernommen. Im ursprünglich zugeordneten Abrechnungskonto bleibt der Verlauf der Kosten aber erhalten.

## <a name="how-cost-management-uses-scopes"></a>Zweck von Bereichen in Cost Management

In Bereichen können Sie Abrechnungsdaten verwalten, mit für Zahlungen spezifischen Rollen arbeiten, Rechnungen einsehen und die allgemeine Kontenverwaltung durchführen. Rechnungs- und Kontorollen werden getrennt von Bereichen für die Ressourcenverwaltung verwaltet, die Azure RBAC (rollenbasierte Zugriffssteuerung) verwenden. Um den Zweck der einzelnen Bereiche, einschließlich der Unterschiede bei der Zugriffssteuerung, klar zu unterscheiden, werden diese als Abrechnungsbereiche und Azure RBAC-Bereiche bezeichnet.

Informationen zu Abrechnungsbereichen und Azure RBAC-Bereichen sowie zur Funktionsweise der Kostenverwaltung mit Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Verwalten von Kosten mit Abrechnungsbereichen von Partnermandanten

Nachdem Sie Ihre Kunden in eine Microsoft-Kundenvereinbarung aufgenommen haben, sind die folgenden _Abrechnungsbereiche_  in Ihrem Mandanten verfügbar. Verwenden Sie die Bereiche, um Kosten in Cost Management zu verwalten.

### <a name="billing-account-scope"></a>Abrechnungskontobereich

Verwenden Sie den Abrechnungskontobereich, um die Kosten vor Steuern für alle Ihre Kunden und Abrechnungsprofile anzuzeigen. Rechnungskosten werden nur für verbrauchsbasierte Produkte in der Microsoft-Kundenvereinbarung von Kunden angezeigt. Rechnungskosten werden jedoch für erworbene Produkte für Kunden im Rahmen der Microsoft-Kundenvereinbarung und des CSP-Angebots angezeigt. Derzeit werden die Kosten im Bereich standardmäßig in US-Dollar angezeigt. Die Budgets werden für den Bereich ebenfalls in US-Dollar festgelegt.

Unabhängig von den verschiedenen für die Abrechnung verwendeten Währungen nutzen Partner den Abrechnungskontobereich, um in US-Dollar Budgets festzulegen und Kosten für ihre Kunden, Abonnements, Ressourcen und Ressourcengruppen zu verwalten.

Partner filtern auch Kosten in einer bestimmten Abrechnungswährung kundenübergreifend in der Kostenanalyseansicht. Wählen Sie die Liste **Tatsächliche Kosten** aus, um die Kosten in den für die Abrechnung unterstützten Währungen anzuzeigen.

![Beispiel mit der Auswahl „Tatsächliche Kosten“ für Währungen](./media/get-started-partners/actual-cost-selector.png)

Verwenden Sie die [Ansicht der amortisierten Kosten](quick-acm-cost-analysis.md#customize-cost-views) in Abrechnungsbereichen, um die amortisierten Kosten einer reservierten Instanz in einem Reservierungszeitraum anzuzeigen.

### <a name="billing-profile-scope"></a>Abrechnungsprofilbereich

Verwenden Sie den Abrechnungsprofilbereich, um für alle Ihre Kunden für alle in einer Rechnung enthaltenen Produkte und Abonnements die Kosten vor Steuern in der Abrechnungswährung anzuzeigen. Mithilfe des Filters **InvoiceID** können Sie die Kosten in einem Abrechnungsprofil für eine bestimmte Rechnung filtern. Der Filter zeigt die Verbrauchs- und Produktkaufkosten für eine bestimmte Rechnung an. Sie können auch die Kosten für einen bestimmten Kunden in der Rechnung filtern, um die Kosten vor Steuern anzuzeigen.

Nachdem Sie Kunden in eine Microsoft-Kundenvereinbarung aufgenommen haben, erhalten Sie eine Rechnung, die alle Gebühren für alle Produkte (Nutzung, Käufe und Berechtigungen) für diese Kunden in der Microsoft-Kundenvereinbarung enthält. Wenn diese Rechnungen in derselben Währung abgerechnet werden, enthalten sie auch die Gebühren für Berechtigungen und erworbene Produkte wie SaaS, Azure Marketplace und Reservierungen für Kunden, für die noch das klassische CSP-Angebot oder der Azure-Plan gilt.

Um Gebühren mit der Kundenrechnung abzustimmen, können Sie im Abrechnungsprofilbereich alle Kosten anzeigen, die im Rahmen einer Rechnung für Ihre Kunden anfallen. Wie die Rechnung zeigt der Bereich Kosten für jeden Kunden an, der in die neue Microsoft-Kundenvereinbarung eingebunden ist. Der Bereich zeigt auch sämtliche Gebühren für Kundenberechtigungsprodukte an, die noch zum aktuellen CSP-Angebot gehören.

Abrechnungsprofil- und Abrechnungskontobereich sind die einzigen Bereiche, die Gebühren für Berechtigungen und kaufbasierte Produkte wie Azure Marketplace und erworbene Reservierungen anzeigen.

Abrechnungsprofile definieren die Abonnements, die in einer Rechnung berücksichtigt werden. Abrechnungsprofile sind das funktionale Äquivalent zu einer Enterprise Agreement-Registrierung. Ein Abrechnungsprofil ist der Bereich, in dem Rechnungen generiert werden.

Derzeit ist die Abrechnungswährung die Standardwährung beim Anzeigen der Kosten im Abrechnungsprofilbereich. Im Abrechnungsprofilbereich erfolgt die Festlegung von Budgets in der Abrechnungswährung.

Partner können den Bereich verwenden, um Rechnungen abzustimmen. Außerdem verwenden sie den Bereich, um Budgets in der Abrechnungswährung für folgende Elemente festzulegen:

- Spezifisch gefilterte Rechnungen
- Kunde
- Subscription
- Resource group
- Resource
- Azure-Dienst
- Zähler
- ResellerMPNID

### <a name="customer-scope"></a>Kundenbereich

Partner verwenden den Bereich, um Kosten zu verwalten, die in die Microsoft-Kundenvereinbarung eingebundenen Kunden zugeordnet sind. Mit dem Bereich können Partner die Kosten vor Steuern eines bestimmten Kunden in einer Abrechnungswährung anzeigen. Sie können auch die Kosten vor Steuern für ein bestimmtes Abonnement, eine Ressourcengruppe oder Ressource filtern.

Der Kundenbereich umfasst keine Kunden, die dem aktuellen CSP-Angebot unterliegen. Der Bereich umfasst nur Kunden, die über eine Microsoft-Kundenvereinbarung verfügen. Berechtigungskosten, nicht die Azure-Nutzung, für Kunden im Rahmen des aktuellen CSP-Angebots sind im Abrechnungskonto- und Abrechnungsprofilbereich verfügbar, wenn Sie den Kundenfilter anwenden. In diesem Bereich erfolgt die Festlegung von Budgets in der Abrechnungswährung.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partnerzugriff auf Abrechnungsbereiche in Cost Management

Nur die Benutzer mit den Rollen **Globaler Administrator** und **Administrator-Agent** können Kosten für Abrechnungskonten, Abrechnungsprofile und Kunden direkt im Azure-Mandanten des Partners verwalten und anzeigen. Weitere Informationen zu Partner Center-Rollen finden Sie unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview).

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Aktivieren der Kostenverwaltung für Abonnements des Kundenmandanten

Partner können den Zugriff auf Cost Management aktivieren, nachdem Kunden in eine Microsoft-Kundenvereinbarung aufgenommen wurden. Partner können dann eine Richtlinie aktivieren, mit der Kunden ihre Kosten für die in Azure genutzten Dienste anzeigen können, die nach nutzungsbasierten Preisen berechnet werden. Die Kosten werden in der Abrechnungswährung der Kunden für ihren Verbrauch im Azure RBAC-Abonnement- und Ressourcengruppenbereich angezeigt.

Wenn der Partner die Richtlinie für Kostensichtbarkeit aktiviert hat, kann jeder Benutzer mit Azure Resource Manager-Zugriff auf das Abonnement die Kosten, die sich aus nutzungsbasierten Preisen zusammensetzen, verwalten und analysieren. Reseller und Kunden, die über den entsprechenden Azure RBAC-Zugriff auf die Azure-Abonnements verfügen, können Kosten anzeigen.

Unabhängig von der Richtlinie können globale Administratoren und Administrator-Agents des Anbieters die Abonnementkosten anzeigen, wenn sie Zugriff auf das Abonnement und die Ressourcengruppe haben.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Aktivieren der Richtlinie zum Anzeigen von Azure-Nutzungsgebühren

Sie müssen Mitglied der Gruppe **Administrator-Agent** sein, um die Richtlinie anzuzeigen und zu aktualisieren. Verwenden Sie die folgenden Informationen, um die Richtlinie zu aktivieren, die den Kunden das Anzeigen von Azure-Nutzungsgebühren ermöglicht.

Melden Sie sich im Azure-Portal beim *Partnermandanten* an, und wählen Sie **Kostenverwaltung + Abrechnung** aus. Wählen Sie unter „Abrechnungsbereich“ den relevanten Abrechnungsbereich aus, und wählen Sie dann **Kunden** aus. Die Liste der Kunden wird mit dem Abrechnungskonto verknüpft. *Wenn Sie sich versehentlich beim Kundenmandanten anmelden, wird die Liste der **Kunden** nicht angezeigt.*

Wählen Sie in der Liste der Kunden den Kunden aus, dem Sie die Anzeige von Kosten erlauben möchten.

[![Auswählen von Kunden in Cost Management](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

Wählen Sie unter **Einstellungen** die Option **Richtlinien** aus.

Die aktuelle Kostensichtbarkeits-Richtlinie wird für die Gebühren für die **Azure-Nutzung** angezeigt, die den Abonnements für den ausgewählten Kunden zugeordnet ist.
![Richtlinie, die Kunden ermöglicht, nutzungsbasierte Gebühren anzuzeigen](./media/get-started-partners/cost-management-billing-policies.png)

Wenn die Richtlinie auf **Nein** festgelegt ist, ist Azure Cost Management für Abonnementbenutzer, die dem Kunden zugeordnet sind, nicht verfügbar. Sofern die Kostensichtbarkeits-Richtlinie nicht von einem Partner aktiviert wird, ist sie standardmäßig für alle Abonnementbenutzer deaktiviert.

Wenn die Kostenrichtlinie auf **Ja** festgelegt ist, können Abonnementbenutzer, die dem Kundenmandanten zugeordnet sind, nutzungsbasierte Nutzungsgebühren anzeigen.

Wenn die Kostensichtbarkeits-Richtlinie aktiviert ist, werden für alle Dienste im Rahmen der Abonnementnutzung die durch nutzungsbasierte Gebühren anfallenden Kosten angezeigt. Die Reservierungsnutzung wird ohne Gebühren für tatsächliche und amortisierte Kosten angezeigt. Käufe und Berechtigungen werden keinem bestimmten Abonnement zugeordnet. Käufe werden also nicht im Abonnementbereich angezeigt. Der globale Administrator/Administrator-Agent eines direkten Partners oder indirekten Anbieters kann auch mithilfe der [Update Customer-API](/rest/api/billing/2019-10-01-preview/policies/updatecustomer) die Richtlinie für Kostentransparenz jedes Kunden im gewünschten Umfang festlegen.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Anzeigen der Abonnementkosten auf dem Kundenmandanten

Öffnen Sie zum Anzeigen der Kosten für ein Abonnement auf dem Azure-Mandanten des Kunden die Option **Kostenverwaltung + Abrechnung**. Wählen Sie die Option **Kostenanalyse** für das erforderliche Abonnement aus, um mit der Überprüfung der Kosten zu beginnen. Sie können die Verbrauchskosten für jedes Abonnement einzeln auf dem Kundenmandanten anzeigen.

[![Anzeigen der Kostenanalyse als Kunde](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

Kostenanalyse, Budgets und Warnungen sind für die Azure RBAC-Bereiche „Abonnement“ und „Ressourcengruppe“ für nutzungsbasierte Kosten verfügbar.

In amortisierten Ansichten und für tatsächliche Kosten für reservierte Instanzen in den Azure RBAC-Bereichen werden keine Gebühren angezeigt. Die Anschaffungskosten für Berechtigungen wie reservierte Instanzen und Marketplace-Gebühren werden nur in Abrechnungsbereichen im Mandanten des Kunden angezeigt, in dem die Einkäufe erfolgt sind.

Die Einzelhandelspreise, die zum Berechnen der in der Ansicht enthaltenen Kosten verwendet werden, entsprechen den Preisen, die im Azure-Preisrechner für alle Kunden angezeigt werden. Die angezeigten Kosten enthalten keine Rabatte oder Guthaben, über die der Partner ggf. verfügt, z. B. vom Partner erworbene Guthaben, tarifspezifische Rabatte und Rabatte für globale Dienste.

 

## <a name="analyze-costs-in-cost-analysis"></a>Analysieren von Kosten mithilfe der Kostenanalyse

Partner mit Zugriff auf Abrechnungsbereiche im Partnermandanten können Kosten in der Kostenanalyse für einen bestimmten Kunden oder für eine Rechnung untersuchen und analysieren. In der Ansicht [Kostenanalyse](quick-acm-cost-analysis.md) können Sie auch [Ansichten speichern](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) und Daten in [CSV- und PNG-Dateien](quick-acm-cost-analysis.md#download-usage-data) exportieren.

Azure RBAC-Benutzer mit Zugriff auf ein Abonnement können auch Einzelhandelskosten für Abonnements im Kundenmandanten analysieren, Ansichten speichern und Daten in CSV- und PNG-Dateien exportieren.

Sie können die Filter- und Gruppierungsfunktionen in der Kostenanalyse verwenden, um die Kosten anhand mehrerer Feldern zu analysieren. Partnerspezifische Felder werden im nächsten Abschnitt angezeigt.

## <a name="data-fields"></a>Datenfelder

Die folgenden Datenfelder befinden sich im Nutzungsdetaildateien und Cost Management-APIs. Falls verfügbar, werden entsprechende Partner Center-Informationen angezeigt. Für die folgenden Felder in Fettschrift können Partner Filter- und Gruppierungsfeatures in der Kostenanalyse verwenden, um die Kosten anhand mehrerer Feldern zu analysieren. Beide Felder gelten nur für Microsoft-Kundenvereinbarungen, die von Partnern unterstützt werden.

| **Feldname** | **Beschreibung** | **Partner Center-Entsprechung** |
| --- | --- | --- |
| invoiceId | Rechnungs-ID auf der Rechnung für die jeweilige Transaktion. | Nummer der Rechnung, in der die Transaktion aufgeführt wird. |
| previousInvoiceID | Verweis auf die ursprüngliche Rechnung, wenn es sich um eine Rückerstattung handelt (negative Kosten). Wird nur aufgefüllt, wenn eine Rückerstattung vorliegt. | – |
| billingAccountName | Name des Abrechnungskontos, das den Partner repräsentiert. Hier werden alle Kosten für folgenden Kundengruppen zusammengefasst: alle Kunden, die eine Microsoft-Kundenvereinbarung abgeschlossen haben, sowie für alle CSP-Kunden, die berechtigungsbasierte Käufe getätigt haben, z. B. über SaaS, Azure Marketplace oder Reservierungen. | – |
| billingAccountID | Bezeichner des Abrechnungskontos, das den Partner repräsentiert. | Commerce-Stamm-ID des MCAPI-Partners. Wird in API-Anforderungen verwendet, jedoch nicht in Antworten eingeschlossen.|
| billingProfileID | Bezeichner des Abrechnungsprofils, in dem Kosten für folgende Kundengruppen rechnungsübergreifend in einer einzigen Abrechnungswährung zusammengefasst werden: alle Kunden, die eine Microsoft-Kundenvereinbarung abgeschlossen haben, sowie alle CSP-Kunden, die berechtigungsbasierte Käufe getätigt haben, z. B. über SaaS, Azure Marketplace oder Reservierungen. | MCAPI-Partnerabrechnungsgruppen-ID. Wird in API-Anforderungen verwendet, jedoch nicht in Antworten eingeschlossen. |
| billingProfileName | Name des Abrechnungsprofils, in dem Kosten für folgende Kundengruppen rechnungsübergreifend in einer einzigen Abrechnungswährung zusammengefasst werden: alle Kunden, die eine Microsoft-Kundenvereinbarung abgeschlossen haben, sowie alle CSP-Kunden, die berechtigungsbasierte Käufe getätigt haben, z. B. über SaaS, Azure Marketplace oder Reservierungen. | – |
| invoiceSectionName | Name des Projekts, das in Rechnung gestellt wird. Gilt nicht für Microsoft-Kundenvereinbarungen, die über Partner abgeschlossen wurden. | – |
| invoiceSectionID | Bezeichner des Projekts, das in Rechnung gestellt wird. Gilt nicht für Microsoft-Kundenvereinbarungen, die über Partner abgeschlossen wurden. | – |
| **CustomerTenantID** | Bezeichner des Azure Active Directory-Mandanten des Kundenabonnements. | Organisations-ID des Kunden: die Azure Active Directory-TenantID des Kunden. |
| **CustomerName** | Name des Azure Active Directory-Mandanten des Kundenabonnements. | Der im Partner Center angegebene Organisationsname des Kunden. Wichtig für die Abstimmung der Rechnung mit Ihren Systeminformationen. |
| **CustomerTenantDomainName** | Domänenname des Azure Active Directory-Mandanten des Kundenabonnements. | Azure Active Directory-Mandantendomäne des Kunden. |
| **PartnerTenantID** | Bezeichner für den Azure Active Directory-Mandanten des Partners. | Die Azure Active Directory-Mandanten-ID des Partners wird als Partner-ID im GUID-Format aufgerufen. |
| **PartnerName** | Name des Azure Active Directory-Mandanten des Partners. | Name des Partners |
| **ResellerMPNID** | MPNID des Handelspartners, der dem Abonnement zugeordnet ist. | MPN-ID des Resellers für das Abonnement. Nicht verfügbar für die aktuelle Aktivität. |
| costCenter | Dem Abonnement zugeordnete Kostenstelle. | – |
| billingPeriodStartDate | Startdatum des Abrechnungszeitraums, wie auf der Rechnung aufgeführt. | – |
| billingPeriodEndDate | Enddatum des Abrechnungszeitraums, wie auf der Rechnung aufgeführt. | – |
| servicePeriodStartDate | Startdatum des Bewertungszeitraums, für den die Dienstnutzung zur Gebührenermittlung bewertet wurde. Die Preise für Azure-Dienste werden basierend auf dem Bewertungszeitraum bestimmt. | ChargeStartDate in Partner Center.  Startdatum des Abrechnungszyklus, ausgenommen bei der Darstellung von Daten zu den zuvor nicht fakturierten latenten Nutzungsdaten aus dem vorherigen Abrechnungszyklus. Die Uhrzeit ist immer der Tagesanfang (0:00 Uhr). |
| servicePeriodEndDate | Enddatum des Bewertungszeitraums, für den die Dienstnutzung zur Gebührenermittlung bewertet wurde. Die Preise für Azure-Dienste werden basierend auf dem Bewertungszeitraum bestimmt. | – |
| date | Bei Azure-Verbrauchsdaten wird das bewertete Nutzungsdatum angezeigt. Bei reservierten Instanzen wird das Datum des Erwerbs angezeigt. Bei wiederkehrenden und einmaligen Gebühren beispielsweise für Marketplace und Support wird das Datum des Erwerbs angezeigt. | – |
| productID | Bezeichner des Produkts, für das basierend auf Verbrauch oder Erwerb Gebühren anfallen. Hierbei handelt es sich um den aus productID und SkuID verketteten Product Key, wie im Partner Center angezeigt. | Die ID des Produkts. |
| product | Name des Produkts, für das basierend auf Verbrauch oder Erwerb Gebühren anfallen, wie auf der Rechnung angezeigt. | Der Produktname im Katalog. |
| serviceFamily | Zeigt die Dienstfamilie des erworbenen oder gebührenpflichtigen Produkts an. Beispiel: „Storage“ oder „Compute“. | – |
| productOrderID | Der Bezeichner der Ressource oder des Azure-Plans, der bzw. dem das Abonnement angehört. Beispiel: „Azure-Plan“. | CommerceSubscriptionID |
| productOrderName | Der Name des Azure-Plans, zu dem das Abonnement gehört. Beispiel: „Azure-Plan“. | –|
| consumedService | Der genutzte Dienst (Legacytaxonomie), wie in den Legacy-EA-Nutzungsdetails verwendet. | Der im Partner Center angezeigte Dienst. Beispiele: „Microsoft. Storage“, „Microsoft. Compute“ und „Microsoft. operationalinsights“. |
| meterID | Der Bezeichner der Verbrauchseinheit für die gemessene Nutzung. | Die ID der verwendeten Verbrauchseinheit. |
| meterName | Identifiziert den Namen der Verbrauchseinheit für die gemessene Nutzung. | Der Name der verwendeten Verbrauchseinheit. |
| meterCategory | Identifiziert den Dienst der obersten Ebene für die Nutzung an. | Der Dienst der obersten Ebene für die Nutzung. |
| meterSubCategory | Definiert den Typ oder die Unterkategorie des Azure-Diensts, der bzw. die sich auf den Tarif auswirken kann. | Der Typ des Azure-Diensts, der sich auf den Tarif auswirken kann.|
| meterRegion | Gibt den Standort des Rechenzentrums für bestimmte Dienste an, die basierend auf dem Standort des Rechenzentrums berechnet werden. | Der regionale Standort eines Rechenzentrums für Dienste, bei denen dies zutrifft und die Spalte aufgefüllt ist. |
| Abonnement-ID | Der eindeutige von Microsoft generierte Bezeichner für das Azure-Abonnement. | EntitlementID |
| subscriptionName | Der Name des Azure-Abonnements. | – |
| Begriff | Zeigt den Zeitraum für die Gültigkeit des Angebots an. Für reservierte Instanzen werden beispielsweise 12 Monate eines Jahreszeitraums der reservierten Instanz angezeigt. Bei einmaligen oder wiederkehrenden Käufen für SaaS, Azure Marketplace und Support zeigt der Zeitraum einen Monat an. Dies gilt nicht für die Azure-Nutzung. | – |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Der Herausgebertyp, der den Herausgeber als Erstanbieter, Drittanbieter-Handelspartner oder Drittanbieter-Agentur identifiziert. | – |
| partNumber | Teilenummer für nicht genutzte reservierte Instanzen und Azure Marketplace-Dienste. | – |
| publisherName | Name des Herausgebers des Diensts – Microsoft oder anderer Herausgeber. | Der Name des Herausgebers des Produkts.|
| reservationId | Bezeichner für die erworbene reservierte Instanz. | – |
| reservationName | Name der reservierten Instanz. | – |
| reservationOrderId | Auftrags-ID der reservierten Instanz. | – |
| frequency | Zahlungshäufigkeit für eine reservierte Instanz. | – |
| resourceGroup | Name der Azure-Ressourcengruppe, der für die Lebenszyklusverwaltung der Ressource verwendet wird. | Name der Ressourcengruppe |
| instanceID (oder) ResourceID | Bezeichner der Ressourceninstanz. | Wird als ResourceURI angezeigt, der die kompletten Ressourceneigenschaften enthält. |
| resourceLocation | Name des Ressourcenspeicherorts. | Der Speicherort der Ressource. |
| Standort | Normalisierter Speicherort der Ressourcengruppe. | – |
| effectivePrice | Der geltende Einheitenpreis für den Dienst in der Abrechnungswährung. Dieser Preis ist für jedes Produkt, jede Dienstfamilie, jede Verbrauchseinheit und jedes Angebot einzigartig. Wird mit den Preisen im Preisblatt für das Abrechnungskonto verwendet. Bei Stufenpreisen oder enthaltenen Mengen wird der gemischte Preis für die Nutzung angezeigt. | Der Einzelpreis nach den vorgenommenen Anpassungen. |
| Menge | Die gemessene Menge, die erworben oder genutzt wurde. Die Menge der Verbrauchseinheit, die während des Abrechnungszeitraums genutzt wurde. | Anzahl der Einheiten. Stellen Sie sicher, dass die Angaben den Informationen in Ihrem Abrechnungssystem während des Abgleichs entsprechen. |
| unitOfMeasure | Gibt die Einheit an, in der der Dienst in Rechnung gestellt wird. Beispiele: GB oder Stunden. | Gibt die Einheit an, in der der Dienst in Rechnung gestellt wird. Beispiele sind GB, Stunden und 10.000 s. |
| pricingCurrency | Die Währung, die den Einheitenpreis definiert. | Die Währung in der Preisliste.|
| billingCurrency | Die Währung, in der die abgerechneten Kosten definiert sind. | Die auf der Rechnung als Abrechnungswährung definierte Währung |
| chargeType | Definiert den Gebührentyp, den die Kosten in Azure Cost Management repräsentieren, z. B. Erwerb oder Erstattung. | Der Typ der Gebühr oder Berichtigung. Nicht verfügbar für die aktuelle Aktivität. |
| costinBillingCurrency | Geschätzte erweiterte oder gemischte Kosten vor Steuern in der Abrechnungswährung. | – |
| costinPricingCurrency | Erweiterte oder gemischte Kosten vor Steuern in der Währung, die mit den Preisen korreliert. | – |
| **costinUSD** | Geschätzte erweiterte oder gemischte Kosten vor Steuern in USD. | – |
| **paygCostInBillingCurrency** | Zeigt Kosten an, wenn die Preise als Verkaufspreise angegeben sind. Zeigt die Preise für die nutzungsbasierte Bezahlung in der Abrechnungswährung an. Nur verfügbar bei Azure RBAC-Bereichen. | – |
| **paygCostInUSD** | Zeigt Kosten an, wenn die Preise als Verkaufspreise angegeben sind. Zeigt die Preise für die nutzungsbasierte Bezahlung in USD an. Nur verfügbar bei Azure RBAC-Bereichen. | – |
| exchangeRate | Der für die Umrechnung aus der Preiswährung in die Abrechnungswährung verwendete Wechselkurs. | Wird im Partner Center als PCToBCExchangeRate bezeichnet. Der Wechselkurs Preiswährung/Abrechnungswährung.|
| exchangeRateDate | Das Datum des für die Umrechnung aus der Preiswährung in die Abrechnungswährung verwendeten Wechselkurses. | Wird im Partner Center als PCToBCExchangeRateDat bezeichnet. Das Datum des Wechselkurses Preiswährung/Abrechnungswährung.|
| isAzureCreditEligible | Gibt an, ob die Kosten mit Azure-Guthaben bezahlt werden können. | – |
| serviceInfo1 | Ein Legacyfeld, in dem optionale dienstspezifische Metadaten erfasst werden. | Interne Azure-Dienstmetadaten. |
| serviceInfo2 | Ein Legacyfeld, in dem optionale dienstspezifische Metadaten erfasst werden. | Dienstinformationen. Beispielsweise ein Imagetyp für einen virtuellen Computer und ein ISP-Name für ExpressRoute.|
| additionalInfo | Dienstspezifische Metadaten. Dies kann beispielsweise ein Imagetyp für einen virtuellen Computer sein. | Alle zusätzlichen Informationen, die in den anderen Spalten nicht enthalten sind. Die dienstspezifischen Metadaten. Dies kann beispielsweise ein Imagetyp für einen virtuellen Computer sein.|
| tags | Ein Tag, den Sie der Verbrauchseinheit zuweisen. Verwenden Sie Tags zum Gruppieren von Abrechnungsdatensätzen. Beispielsweise können Sie Tags verwenden, um Kosten nach den Abteilungen zu unterteilen, die die Verbrauchseinheit nutzen. | Vom Kunden hinzugefügte Tags.|
| **partnerEarnedCreditRate** | Rabatt, der angewendet wird, wenn basierend auf dem Zugriff über einen Partneradministratorlink ein Partner Earned Credit (PEC) vorhanden ist. | Die PEC-Rate (Partner Earned Credit). Beispiel: 0 % oder 15 %. |
| **partnerEarnedCreditApplied** | Gibt an, ob der Partner Earned Credit angewendet wurde. | – |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Anzeigen der Ressourcenkosten für Partner Earned Credit (PEC)

In Azure Cost Management können Partner die Kostenanalyse verwenden, um die Kosten anzuzeigen, auf die sich die PEC-Vorteile ausgewirkt haben.

Melden Sie sich im Azure-Portal beim Partnermandanten an, und wählen Sie **Kostenverwaltung + Abrechnung** aus. Wählen Sie unter **Cost Management** die Option **Kostenanalyse** aus.

In der Kostenanalyseansicht werden die Kosten des Abrechnungskontos für den Partner angezeigt. Wählen Sie nach Bedarf den **Bereich** für den Partner, einen bestimmten Kunden oder ein Abrechnungsprofil aus, um Rechnungen abzustimmen.

Klicken Sie in einem Ringdiagramm auf die Dropdownliste, und wählen Sie **PartnerEarnedCreditApplied** aus, um PEC-Kosten im Detail anzuzeigen.

![Beispiel für das Anzeigen von Partner Earned Credit](./media/get-started-partners/cost-analysis-pec1.png)

Wenn die **PartnerEarnedCreditApplied**-Eigenschaft _True_ ist, wirkt sich der Vorteil des Partner Earned-Administratorzugriffs auf die zugeordneten Kosten aus.

Wenn die **PartnerEarnedCreditApplied**-Eigenschaft _False_  ist, haben die zugeordneten Kosten die erforderliche Berechtigung für die Gutschrift nicht erfüllt. Es kann auch sein, dass der erworbene Dienst nicht für Partner Earned Credit berechtigt ist.

In der Regel dauert es 8-24 Stunden, bis Dienstnutzungsdaten in Cost Management angezeigt werden. Weitere Informationen finden Sie unter [Aktualisierung und Aufbewahrung von Kosten- und Nutzungsdaten](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). PEC-Gutschriften werden innerhalb von 48 Stunden ab dem Zeitpunkt des Zugriffs in Azure Cost Management angezeigt.


Sie können auch nach der **PartnerEarnedCreditApplied**-Eigenschaft gruppieren und filtern, indem Sie die **Gruppieren nach**-Optionen verwenden. Verwenden Sie die Optionen, um zu überprüfen, auf welche Kosten PEC angewendet wird und auf welche nicht.

![Gruppieren oder Filtern nach Partner Earned Credit](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Exportieren von Kostendaten in Azure Storage

Partner mit Zugriff auf Abrechnungsbereiche in einem Partnermandanten können ihre Kosten- und Nutzungsdaten in ein Blob in Azure Storage exportieren. Das Blob muss sich in einem Abonnement im Mandanten des Partners befinden, bei dem es sich nicht um ein [gemeinsames Abonnement für einen Dienst](/partner-center/shared-services) oder das Abonnement eines Kunden handelt. Wenn Sie Kostendaten exportieren möchten, sollten Sie im Partnermandanten ein unabhängiges Abonnement mit nutzungsbasierter Bezahlung einrichten, um die exportierten Kostendaten zu hosten. Das Speicherkonto für die Exporte wird im Azure Storage-Blob erstellt, das im Abonnement mit nutzungsbasierter Bezahlung gehostet wird. Je nach Bereich, in dem der Partner den Export ausführt, werden die dazugehörigen Daten in regelmäßigen Abständen automatisch in das Speicherkonto exportiert.

Benutzer mit Azure RBAC-Zugriff auf das Abonnement können die Kostendaten auch in ein Azure Storage-Blob exportieren, das in einem beliebigen Abonnement im Kundenmandanten gehostet wird.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Erstellen eines Exports in einem Partnermandanten oder Kundenmandanten

Melden Sie sich im Azure-Portal beim Partnermandanten oder beim Kundenmandanten an, und wählen Sie **Kostenverwaltung + Abrechnung** aus. Wählen Sie einen passenden Bereich aus, z. B. ein Abrechnungskonto für eine Microsoft Partner-Vereinbarung, und wählen Sie dann **Kostenanalyse** aus. Wenn die Seite geladen ist, klicken Sie auf **Export**. Wählen Sie unter „Export planen“ die Option **Alle Exporte anzeigen** aus.

![Auswählen von „Exportieren“ und „Alle Exporte anzeigen“](./media/get-started-partners/export01.png)

Wählen Sie dann **Hinzufügen** aus, und geben Sie den Namen ein, und wählen Sie den Exporttyp aus. Wählen Sie die Registerkarte **Speicher** aus, und geben Sie die erforderlichen Informationen ein.

![Hinzufügen eines neuen Exports und Auswählen der Registerkarte „Speicher“](./media/get-started-partners/export02.png)

Wenn Sie einen Export im Partnermandanten erstellen, wählen Sie das Abonnement mit nutzungsbasierter Bezahlung im Partnermandanten aus. Erstellen Sie mit diesem Abonnement ein Azure Storage-Konto.

Wählen Sie für Azure RBAC-Benutzer im Kundenmandanten ein Abonnement im Kundenmandanten aus. Erstellen Sie mit diesem Abonnement ein Azure Storage-Konto.

Überprüfen Sie die Angaben, und klicken Sie dann auf **Erstellen**, um den Export zu planen.

Wählen Sie den Name des Speicherkontos aus, um die Daten in der Exportliste zu bestätigen. Wählen Sie auf der Seite „Speicherkonto“ die Option **Container** aus, und wählen Sie dann den Container aus. Navigieren Sie zum entsprechenden Ordner, und wählen Sie die CSV-Datei aus. Wählen Sie **Herunterladen** aus, um die CSV-Datei herunterzuladen, und öffnen Sie diese. Die exportierten Daten ähneln den Kostendaten, die wiederum den Nutzungsinformationen aus dem Azure-Portal ähneln.

![Beispiel für exportierte Daten](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Cost Management-REST-APIs

Partner und Kunden können die in den folgenden Abschnitten für häufige Aufgaben beschriebenen Cost Management-APIs verwenden.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management-APIs – Direkte und indirekte Anbieter

Partner mit Zugriff auf Abrechnungsbereiche in einem Partnermandanten können die folgenden APIs verwenden, um in Rechnung gestellte Kosten anzuzeigen.

APIs im Abonnementbereich können von einem Partner unabhängig von der Kostenrichtlinie aufgerufen werden, wenn sie auf das Abonnement zugreifen können. Andere Benutzer mit Zugriff auf das Abonnement, z. B. der Kunde oder der Handelspartner, können die APIs nur aufrufen, nachdem der Partner die Kostenrichtlinie für den Kundenmandanten aktiviert hat.


#### <a name="to-get-a-list-of-billing-accounts"></a>So rufen Sie eine Liste von Abrechnungskonten ab

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>So rufen Sie eine Liste von Kunden ab

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>So rufen Sie eine Liste von Abonnements ab

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>So rufen Sie eine Liste der Rechnungen für einen Zeitraum ab

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

Der API-Aufruf gibt ein Array von Rechnungen zurück, das ähnliche Elemente wie der folgende JSON-Code aufweist.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Verwenden Sie den zuvor zurückgegebenen ID-Feldwert und ersetzen Sie ihn im folgenden Beispiel als Gültigkeitsbereich für die Abfrage von Nutzungsdetails.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

Im Beispiel werden die Nutzungsdatensätze für die jeweilige Rechnung zurückgegeben.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>So rufen Sie die Richtlinie für Kunden zum Anzeigen von Kosten ab

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>So legen Sie die Richtlinie für Kunden zum Anzeigen von Kosten fest

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>So rufen Sie die Azure-Dienstnutzung für ein Abrechnungskonto ab

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>So laden Sie die Azure-Dienstnutzung eines Kunden herunter

Der folgende GET-Aufruf ist ein asynchroner Vorgang.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Ruft den in der Antwort zurückgegebenen `Location`-URI auf, um den Vorgangsstatus zu überprüfen. Wenn der Status *Completed* ist, enthält die Eigenschaft `downloadUrl` einen Link, mit dem Sie den generierten Bericht herunterladen können.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>So rufen Sie das Preisblatt für genutzte Azure-Dienste ab oder laden es herunter

Verwenden Sie zunächst den folgenden POST-Aufruf.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Rufen Sie dann den Eigenschaftswert des asynchronen Vorgangs auf. Beispiel:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Der vorherige GET-Befehl gibt den Downloadlink mit dem Preisblatt zurück.


#### <a name="to-get-aggregated-costs"></a>So rufen Sie aggregierte Kosten ab

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Erstellen eines Budgets für einen Partner

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Erstellen eines Budgets für einen Kunden

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Löschen eines Budgets

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Nächste Schritte
- [Beginnen mit der Kostenanalyse](quick-acm-cost-analysis.md) in Cost Management
- [Erstellen und Verwalten von Budgets](tutorial-acm-create-budgets.md) in Cost Management
