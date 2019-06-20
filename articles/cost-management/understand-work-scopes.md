---
title: Verstehen von und Arbeiten mit Bereichen in Azure Cost Management | Microsoft-Dokumentation
description: Dieser Artikel hilft Ihnen, die in Azure verfügbaren Bereiche für Abrechnung und Ressourcenverwaltung zu verstehen und wie Sie die Bereiche in Cost Management und APIs verwenden können.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 717c0f110ebbeee53e2c9b9207350385288d57c3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991376"
---
# <a name="understand-and-work-with-scopes"></a>Verstehen von und Arbeiten mit Bereichen

Dieser Artikel hilft Ihnen, die in Azure verfügbaren Bereiche für Abrechnung und Ressourcenverwaltung zu verstehen und wie Sie die Bereiche in Cost Management und APIs verwenden können.

## <a name="scopes"></a>Bereiche

Ein _Bereich_ ist ein Knoten in der Azure-Ressourcenhierarchie, in dem Azure AD-Benutzer auf Dienste zugreifen und diese verwalten. Die meisten Azure-Ressourcen werden in Ressourcengruppen erstellt und bereitgestellt, die Teil von Abonnements sind. Microsoft bietet auch zwei Hierarchien oberhalb von Azure-Abonnements, die spezielle Rollen zur Verwaltung von Abrechnungsdaten bieten:
- Abrechnungsdaten, z.B. Zahlungen und Rechnungen
- Clouddienste wie Kosten und die Richtliniengovernance

In Bereichen können Sie Abrechnungsdaten verwalten, mit für Zahlungen spezifischen Rollen arbeiten, Rechnungen einsehen und die allgemeine Kontenverwaltung durchführen. Rechnungs- und Kontorollen werden getrennt von denen für die Ressourcenverwaltung verwaltet, die [Azure RBAC](../role-based-access-control/overview.md) (rollenbasierte Zugriffssteuerung) verwenden. Um den Zweck der einzelnen Bereiche, einschließlich der Unterschiede bei der Zugriffssteuerung, klar zu unterscheiden, werden diese als _Abrechnungsbereiche_ und _RBAC-Bereiche_ bezeichnet.

## <a name="how-cost-management-uses-scopes"></a>Zweck von Bereichen in Cost Management

Cost Management ist in allen Bereichen oberhalb von Ressourcen aktiv, damit Organisationen Kosten auf der Ebene verwalten können, auf der sie Zugriff haben, und zwar unabhängig davon, ob es sich um das gesamte Abrechnungskonto oder eine einzelne Ressourcengruppe handelt. Obwohl sich Abrechnungsbereiche je nach Vertrag mit Microsoft (Abonnementtyp) unterscheiden, gilt dies für RBAC-Bereiche nicht.

## <a name="azure-rbac-scopes"></a>Azure RBAC-Bereiche

Azure unterstützt für die Ressourcenverwaltung drei Bereiche. Jeder Bereich unterstützt die Verwaltung von Zugriff und Governance, einschließlich, aber nicht beschränkt auf Kostenverwaltung.

- [**Verwaltungsgruppen**](../governance/management-groups/index.md): Hierarchische Container mit bis zu acht Ebenen zum Organisieren von Azure-Abonnements.

    Ressourcentyp: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Abonnements**: Primäre Container für Azure-Ressourcen.

    Ressourcentyp: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Ressourcengruppe**](../azure-resource-manager/resource-group-overview.md#resource-groups): Logische Gruppierungen zusammengehöriger Ressourcen für eine Azure-Lösung, die den gleichen Lebenszyklus haben. Dies sind z.B. Ressourcen, die gemeinsam bereitgestellt und gelöscht werden.

    Ressourcentyp: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Verwaltungsgruppen ermöglichen das Organisieren von Abonnements in einer Hierarchie. Sie können beispielsweise eine logische Organisationshierarchie mithilfe von Verwaltungsgruppen einrichten. Anschließend versehen Sie Teams mit Abonnements für Produktion- und Entwicklungs-/Testworkloads. Danach erstellen Sie Ressourcengruppen in den Abonnements, um die einzelnen Subsysteme oder Komponenten zu verwalten.

Der Aufbau einer Organisationshierarchie ermöglicht es, die Einhaltung von Kosten und Richtlinien unternehmensweit zu überprüfen. Anschließend kann jede Führungskraft ihre aktuellen Kosten einsehen und analysieren. Danach können sie Budgets erstellen, um ungünstige Ausgabemuster einzudämmen und Kosten zu optimieren, indem sie die Empfehlungen von Azure Advisor auf der untersten Ebene umsetzen.

Das Gewähren des Zugriffs zum Anzeigen der Kosten und das optionale Verwalten der Kostenkonfiguration, wie z.B. Budgets und Exporte, erfolgt über Governancebereiche unter Verwendung von Azure RBAC. Mit Azure RBAC gewähren Sie Azure AD-Benutzern und -Gruppen Zugriff auf einen vordefinierten Satz von Aktionen, die in einer Rolle für einen bestimmten Bereich und darunter definiert sind. So gewährt beispielsweise eine Rolle, die einem Verwaltungsgruppenbereich zugewiesen ist, auch die gleichen Berechtigungen für geschachtelte Abonnements und Ressourcengruppen.

Cost Management unterstützt für jeden der folgenden Bereiche die folgenden integrierten Rollen:

- [**Besitzer**](../role-based-access-control/built-in-roles.md#owner): Ermöglicht das Anzeigen von Kosten und das Verwalten sämtlicher Aspekte einschließlich Kostenkonfiguration.
- [**Mitwirkender**](../role-based-access-control/built-in-roles.md#contributor): Ermöglicht das Anzeigen von Kosten und das Verwalten sämtlicher Aspekte einschließlich Kostenkonfiguration, aber ausschließlich Zugriffssteuerung.
- [**Leser**](../role-based-access-control/built-in-roles.md#reader): Ermöglicht das Anzeigen sämtlicher Daten einschließlich Kostendaten und Konfiguration, aber nicht das Vornehmen von Änderungen.
- [**Cost Management-Mitwirkender**](../role-based-access-control/built-in-roles.md#cost-management-contributor): Ermöglicht das Anzeigen der Kosten, das Verwalten der Kostenkonfiguration und die Anzeige von Empfehlungen.
- [**Cost Management-Leser**](../role-based-access-control/built-in-roles.md#cost-management-reader): Ermöglicht die Anzeige von Kostendaten, Kostenkonfiguration und Empfehlungen.

„Cost Management-Mitwirkender“ ist die empfohlene Rolle mit geringstmöglichen Berechtigungen. Sie ermöglicht den Zugriff für die Erstellung und Verwaltung von Budgets und Exporten, um die Kosten effektiver zu überwachen und zu dokumentieren. Cost Management-Mitwirkende benötigen ggf. auch zusätzliche Rollen, um Kostenverwaltungsszenarien durchgängig unterstützen zu können. Stellen Sie sich die folgenden Szenarien vor:

- **Eingreifen bei Überschreitung von Budgets**: Cost Management-Mitwirkende benötigen auch Zugriff für das Erstellen und/oder Verwalten von Aktionsgruppen, um bei Überschreitungen automatisch zu reagieren. Erwägen Sie das Zuweisen der Rolle [Mitwirkender an der Überwachung](../role-based-access-control/built-in-roles.md#monitoring-contributor) zu einer Ressourcengruppe, die die zu verwendende Aktionsgruppe enthält, wenn Budgetschwellenwerte überschritten werden. Die Automatisierung bestimmter Aktionen erfordert zusätzliche Rollen für die verwendeten spezifischen Dienste, wie z.B. Azure Automation und Azure Functions.
- **Planen des Exports von Kostendaten**: Cost Management-Mitwirkende benötigen auch Zugriff für das Verwalten von Speicherkonten, um einen Export zum Kopieren von Daten in ein Speicherkonto zu planen. Erwägen Sie das Zuweisen der Rolle [Speicherkontomitwirkender](../role-based-access-control/built-in-roles.md#storage-account-contributor) zu einer Ressourcengruppe, die das Speicherkonto enthält, in das Kostendaten exportiert werden.
- **Anzeigen von Empfehlungen für Kosteneinsparungen**: Cost Management-Leser und Cost Management-Mitwirkende haben in der Standardeinstellung Zugriff auf die *Anzeige* von Empfehlungen. Der Zugriff auf die Kostenempfehlungen erfordert jedoch den Zugriff auf einzelne Ressourcen. Erwägen Sie, eine [dienstspezifische Rolle](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) zuzulassen, wenn Sie auf eine kostenbasierte Empfehlung reagieren möchten.

## <a name="enterprise-agreement-scopes"></a>Enterprise Agreement-Bereiche

Enterprise Agreement-Abrechnungskonten (EA), sog. Registrierungen, haben die folgenden Bereiche:

- [**Abrechnungskonto**](../billing/billing-view-all-accounts.md): Stellt eine EA-Registrierung dar. Rechnungen werden in diesem Bereich erstellt. Nicht nutzungsabhängige Käufe (Marketplace und Reservierungen) sind nur in diesem Bereich möglich. Sie werden in Abteilungen oder Registrierungskonten nicht berücksichtigt.

    Ressourcentyp: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Abteilung** : Optionale Gruppierung von Registrierungskonten.

    Ressourcentyp: `Billing/billingAccounts/departments`

- **Registrierungskonto**: Stellt einen Besitzer eines einzelnen Kontos dar. Unterstützt nicht das Gewähren des Zugriffs für mehrere Personen.

    Ressourcentyp: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Governancebereiche sind ein einzelnes Verzeichnis gebunden, EA-Abrechnungsbereiche hingegen nicht. Ein EA-Abrechnungskonto kann Abonnements in einer beliebigen Anzahl von Azure AD-Verzeichnissen enthalten.

EA-Abrechnungsbereiche unterstützen die folgenden Rollen:

- **Unternehmensadministrator**: Ermöglicht die Verwaltung von und den Zugriff auf Abrechnungskontoeinstellungen, die Anzeige aller Kosten und die Verwaltung der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der EA-Abrechnungsbereich der [Azure RBAC-Rolle „Cost Management-Mitwirkender“](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Unternehmensbenutzer mit Leseberechtigung**: Ermöglicht das Anzeigen der Abrechnungskontoeinstellungen, Kostendaten und Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der EA-Abrechnungsbereich der [Azure RBAC-Rolle „Cost Management-Leser“](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Abteilungsadministrator**: Ermöglicht das Verwalten der Abteilungseinstellungen, wie z.B. Kostenstelle, sowie den Zugriff auf und die Anzeige aller Kosten sowie das Verwalten der Kostenkonfiguration. Beispiele: Budgets und Exporte.  Die Abrechnungskontoeinstellung **Abteilung – Gebühren anzeigen** muss für Abteilungsadministratoren und Benutzer mit Leseberechtigung aktiviert sein, um Kosten einzusehen. Wenn **Abteilung – Gebühren anzeigen** deaktiviert ist, können Abteilungsbenutzer keine Kosten auf einer beliebigen Ebene einsehen, selbst wenn sie Konto- oder Abonnementbesitzer sind.
- **Abteilungsbenutzer mit Leseberechtigung**: Ermöglicht das Anzeigen der Abteilungseinstellungen, Kostendaten und Kostenkonfiguration. Beispiele: Budgets und Exporte. Wenn **Abteilung – Gebühren anzeigen** deaktiviert ist, können Abteilungsbenutzer keine Kosten auf einer beliebigen Ebene einsehen, selbst wenn sie Konto- oder Abonnementbesitzer sind.
- **Kontobesitzer**: Ermöglicht das Verwalten von Registrierungskontoeinstellungen (z.B. Kostenstelle), das Anzeigen aller Kosten und das Verwalten der Kostenkonfiguration (z.B. Budgets und Exporte) für das Registrierungskonto. Die Abrechnungskontoeinstellung **Kontobesitzer – Gebühren anzeigen** muss für Kontobesitzer und RBAC-Benutzer aktiviert sein, um Kosten einzusehen.

EA-Abrechnungskontobenutzer haben keinen direkten Zugriff auf Rechnungen. Rechnungen sind in einem externen Volumenlizenzierungssystem verfügbar.

Azure-Abonnements werden unter Registrierungskonten geschachtelt. Abrechnungsbenutzer haben Zugriff auf Kostendaten für die Abonnements und Ressourcengruppen, die sich unter ihren jeweiligen Bereichen befinden. Sie haben keinen Zugriff zum Anzeigen oder Verwalten von Ressourcen im Azure-Portal. Abrechnungsbenutzer können Kosten einsehen, indem sie in der Liste der Dienste im Azure-Portal zu **Kostenverwaltung + Abrechnung** navigieren. Anschließend können sie die Kosten für die spezifischen Abonnements und Ressourcengruppen filtern, über die sie Berichte erstellen müssen.

Abrechnungsbenutzer haben keinen Zugriff auf Verwaltungsgruppen, da sie nicht explizit zu einem bestimmten Abrechnungskonto gehören. Der Zugriff muss Verwaltungsgruppen explizit gewährt werden. In Verwaltungsgruppen werden Kosten aus allen geschachtelten Abonnements zusammengetragen. Allerdings enthalten sie nur nutzungsbasierte Käufe. Sie enthalten keine Käufe wie Reservierungen und Marketplace-Angebote von Drittanbietern. Um diese Kosten anzuzeigen, verwenden Sie das EA-Abrechnungskonto.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Bereiche für Einzelverträge (nutzungsbasierte Zahlung)

Abonnements mit nutzungsbasierter Zahlung, einschließlich zugehöriger Typen wie kostenlose und Testversionen und Entwicklungs-/Testangebote, haben keinen expliziten Abrechnungskontobereich. Stattdessen hat jedes Abonnement einen Kontobesitzer oder Kontoadministrator, der mit dem EA-Kontobesitzer vergleichbar ist.

- [**Abrechnungskonto**](../billing/billing-view-all-accounts.md): Stellt einen Besitzer mit einem einzelnen Konto für ein oder mehrere Azure-Abonnements dar. Unterstützt derzeit nicht das Gewähren des Zugriffs für mehrere Personen oder des Zugriffs auf aggregierte Kostenansichten.

    Ressourcentyp: Nicht zutreffend

Kontoadministratoren für Abonnements mit nutzungsbasierter Zahlung können im [Azure-Kontocenter](https://account.azure.com/subscriptions) Abrechnungsdaten einsehen und verwalten, wie z.B. Rechnungen und Zahlungen. Sie können jedoch im Azure-Portal keine Kostendaten einsehen und keine Ressourcen verwalten. Um dem Kontoadministrator Zugriff zu gewähren, verwenden Sie die zuvor genannten Cost Management-Rollen.

Im Gegensatz zu EA können Kontoadministratoren für Abonnements mit nutzungsbasierter Zahlung ihre Rechnungen im Azure-Portal einsehen. Beachten Sie, dass die Rollen „Cost Management-Leser“ und „Cost Management-Mitwirkender“ keinen Zugriff auf Rechnungen bieten. Weitere Informationen finden Sie unter [Gewähren von Zugriff auf Rechnungen für nutzungsbasierte Zahlung](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Bereiche für Microsoft-Kundenvereinbarung

Abrechnungskonten der Microsoft-Kundenvereinbarung haben die folgenden Bereiche:

- **Abrechnungskonto**: Stellt eine Kundenvereinbarung für mehrere Microsoft-Produkte und -Dienste dar. Abrechnungskonten der Microsoft-Kundenvereinbarung bieten nicht dieselben Funktionen wie EA-Registrierungen. EA-Registrierungen orientieren sich eher an Abrechnungsprofilen.

    Ressourcentyp: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Abrechnungsprofil**: Definiert die Abonnements, die in einer Rechnung berücksichtigt werden. Abrechnungsprofile entsprechen funktional einer EA-Registrierung, da dies der Bereich ist, in dem Rechnungen generiert werden. Nicht nutzungsabhängige Käufe (wie Marketplace und Reservierungen) sind auch nur in diesem Bereich möglich. Sie sind nicht in Rechnungsabschnitten enthalten.

    Ressourcentyp: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Rechnungsabschnitt**: Stellt eine Gruppe von Abonnements in einer Rechnung oder einem Abrechnungsprofil dar. Rechnungsabschnitte ähneln Abteilungen, d.h. mehrere Personen haben Zugriff auf einen Rechnungsabschnitt.

    Ressourcentyp: `Microsoft.Billing/billingAccounts/invoiceSections`

Im Gegensatz zu EA-Abrechnungsbereichen _sind_ Abrechnungskonten der Kundenvereinbarung an ein einzelnes Verzeichnis gebunden und lassen keine Abonnements in mehreren Azure AD-Verzeichnissen zu.

Abrechnungsbereiche der Kundenvereinbarung unterstützen die folgenden Rollen:

- **Besitzer**: Ermöglicht die Verwaltung von und den Zugriff auf Abrechnungseinstellungen, die Anzeige aller Kosten und die Verwaltung der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der Abrechnungsbereich der Kundenvereinbarung der [Azure RBAC-Rolle „Cost Management-Mitwirkender“](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Mitwirkender**: Ermöglicht die Verwaltung von und den Zugriff auf Abrechnungseinstellungen, die Anzeige aller Kosten und die Verwaltung der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der Abrechnungsbereich der Kundenvereinbarung der [Azure RBAC-Rolle „Cost Management-Mitwirkender“](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Leser**: Ermöglicht die Anzeige von Abrechnungseinstellungen, Kostendaten und der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der Abrechnungsbereich der Kundenvereinbarung der [Azure RBAC-Rolle „Cost Management-Leser“](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Rechnungs-Manager**: Ermöglicht das Anzeigen und Bezahlen von Rechnungen sowie das Anzeigen von Kostendaten und Konfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der Abrechnungsbereich der Kundenvereinbarung der [Azure RBAC-Rolle „Cost Management-Leser“](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-Abonnementersteller**: Ermöglicht das Erstellen von Azure-Abonnements, Anzeigen von Kosten und Verwalten der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der Abrechnungsbereich der Kundenvereinbarung der EA-Rolle „Registrierungskontobesitzer“.

Azure-Abonnements werden ähnlich wie unter EA-Registrierungskonten unter Rechnungsabschnitten geschachtelt. Abrechnungsbenutzer haben Zugriff auf Kostendaten für die Abonnements und Ressourcengruppen, die sich unter ihren jeweiligen Bereichen befinden. Sie haben jedoch keinen Zugriff zum Anzeigen oder Verwalten von Ressourcen im Azure-Portal. Abrechnungsbenutzer können Kosten einsehen, indem sie in der Liste der Dienste im Azure-Portal zu **Kostenverwaltung + Abrechnung** navigieren. Anschließend können sie die Kosten für die spezifischen Abonnements und Ressourcengruppen filtern, über die sie Berichte erstellen müssen.

Abrechnungsbenutzer haben keinen Zugriff auf Verwaltungsgruppen, da sie nicht explizit zum Abrechnungskonto gehören. Wenn jedoch Verwaltungsgruppen für die Organisation aktiviert sind, werden alle Abonnementkosten im Abrechnungskonto und in der Stammverwaltungsgruppe zusammengeführt, da sie beide auf ein einziges Verzeichnis beschränkt sind. Verwaltungsgruppen enthalten nur Käufe, die nutzungsbasiert sind. Käufe wie Reservierungen und Marketplace-Angebote von Drittanbietern fließen nicht in Verwaltungsgruppen ein. Daher können das Abrechnungskonto und die Stammverwaltungsgruppe unterschiedliche Summen ausweisen. Um diese Kosten anzuzeigen, verwenden Sie das Abrechnungskonto oder entsprechende Abrechnungsprofil.

## <a name="cloud-solution-provider-csp-scopes"></a>Bereiche für Cloud Solution Provider (CSP)

Cloud Solution Provider-Partner (CSP) werden derzeit in Cost Management nicht unterstützt. Stattdessen können Sie das [Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) verwenden.

## <a name="switch-between-scopes-in-cost-management"></a>Umschalten zwischen Bereichen in Cost Management

Alle Cost Management-Ansichten im Azure-Portal enthalten links oben die Option **Bereich**. Hierüber können Sie den Bereich rasch ändern. Klicken Sie auf **Bereich**, um die Bereichsauswahl zu öffnen. Hier werden Abrechnungskonten, die Stammverwaltungsgruppe und alle Abonnements angezeigt, die nicht unter der Stammverwaltungsgruppe geschachtelt sind. Um einen Bereich auszuwählen, klicken Sie auf den Hintergrund, um ihn zu markieren, und dann unten auf **Auswählen**. Für eine Detailsuche in geschachtelten Bereichen, wie beispielsweise Ressourcengruppen in einem Abonnement, klicken Sie auf den Link „Bereichsname“. Um den übergeordneten Bereich auf einer geschachtelten Ebene auszuwählen, klicken Sie oben in der Bereichsauswahl auf **Diesen &lt;Bereich&gt; auswählen**.

## <a name="identify-the-resource-id-for-a-scope"></a>Bestimmen der Ressourcen-ID eines Bereichs

Bei der Arbeit mit Cost Management-APIs ist es wichtig, den Bereich zu kennen. Erstellen Sie anhand der folgenden Informationen den richtigen Bereichs-URI für Cost Management-APIs.

### <a name="billing-accounts"></a>Abrechnungskonten

1. Öffnen Sie das Azure-Portal, und navigieren Sie dann in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Eigenschaften** aus.
3. Kopieren Sie die ID des Abrechnungskontos.
4. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Abrechnungsprofile

1. Öffnen Sie das Azure-Portal, und navigieren Sie dann in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Abrechnungsprofile** aus.
3. Klicken Sie auf den Namen des gewünschten Abrechnungsprofils.
4. Wählen Sie im Menü „Abrechnungsprofil“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die ID des Abrechnungskontos.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Rechnungsabschnitte

1. Öffnen Sie das Azure-Portal, und navigieren Sie dann in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Rechnungsabschnitte** aus.
3. Klicken Sie auf den Namen des gewünschten Rechnungsabschnitts.
4. Wählen Sie im Menü „Rechnungsabschnitt“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die IDs der Rechnungsabschnitte.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-Abteilungen

1. Öffnen Sie das Azure-Portal, und navigieren Sie dann in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Abteilungen** aus.
3. Klicken Sie auf den Namen der gewünschten Abteilung.
4. Wählen Sie im Menü „Abteilung“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die Abteilungs-IDs.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA-Registrierungskonto

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Registrierungskonten** aus.
3. Klicken Sie auf den Namen des gewünschten Registrierungskontos.
4. Wählen Sie im Menü „Registrierungskonto“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die Registrierungskonto-IDs.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Verwaltungsgruppe

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Verwaltungsgruppen**.
2. Navigieren Sie zur gewünschten Verwaltungsgruppe.
3. Kopieren Sie die Verwaltungsgruppen-ID aus der Tabelle.
4. Ihr Bereich ist: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Abonnement

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Abonnements**.
2. Kopieren Sie die Abonnement-ID aus der Tabelle.
3. Ihr Bereich ist: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Ressourcengruppen

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Ressourcengruppen**.
2. Klicken Sie auf den Namen der gewünschten Ressourcengruppe.
3. Wählen Sie im Menü „Ressourcengruppe“ **Eigenschaften** aus.
4. Kopieren Sie den Wert des Felds „Ressourcen-ID“.
5. Ihr Bereich ist: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management wird derzeit in [Azure Global](https://management.azure.com) und [Azure Government](https://management.usgovcloudapi.net) unterstützt. Weitere Informationen zu Azure Government finden Sie unter [API-Endpunkte für Azure Global und Azure Government](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Nächste Schritte

- Falls Sie den ersten Schnellstart für Cost Management noch nicht abgeschlossen haben, lesen Sie ihn unter [Erste Schritte in der Analyse von Kosten](quick-acm-cost-analysis.md).
