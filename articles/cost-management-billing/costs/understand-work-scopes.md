---
title: Verstehen von und Arbeiten mit Bereichen in Azure Cost Management
description: Dieser Artikel hilft Ihnen, die in Azure verfügbaren Bereiche für Abrechnung und Ressourcenverwaltung zu verstehen und wie Sie die Bereiche in Cost Management und APIs verwenden können.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 729444b1d1ccf55f34e54a4b59508131458c472b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054803"
---
# <a name="understand-and-work-with-scopes"></a>Verstehen von und Arbeiten mit Bereichen

Dieser Artikel hilft Ihnen, die in Azure verfügbaren Bereiche für Abrechnung und Ressourcenverwaltung zu verstehen und wie Sie die Bereiche in Cost Management und APIs verwenden können.

## <a name="scopes"></a>Bereiche

Ein _Bereich_ ist ein Knoten in der Azure-Ressourcenhierarchie, in dem Azure AD-Benutzer auf Dienste zugreifen und diese verwalten. Die meisten Azure-Ressourcen werden in Ressourcengruppen erstellt und bereitgestellt, die Teil von Abonnements sind. Microsoft bietet auch zwei Hierarchien oberhalb von Azure-Abonnements, die spezielle Rollen zur Verwaltung von Abrechnungsdaten bieten:
- Abrechnungsdaten, z.B. Zahlungen und Rechnungen
- Clouddienste wie Kosten und die Richtliniengovernance

In Bereichen können Sie Abrechnungsdaten verwalten, mit für Zahlungen spezifischen Rollen arbeiten, Rechnungen einsehen und die allgemeine Kontenverwaltung durchführen. Rechnungs- und Kontorollen werden getrennt von den Rollen für die Ressourcenverwaltung verwaltet, die [Azure RBAC](../../role-based-access-control/overview.md) (rollenbasierte Zugriffssteuerung) verwenden. Um den Zweck der einzelnen Bereiche, einschließlich der Unterschiede bei der Zugriffssteuerung, klar zu unterscheiden, werden diese als _Abrechnungsbereiche_ und _Azure RBAC-Bereiche_ bezeichnet.

Weitere Informationen zu Bereichen finden Sie im Video zur [Einrichtung von Hierarchien in Cost Management](https://www.youtube.com/watch?v=n3TLRaYJ1NY). Weitere Videos finden Sie im [YouTube-Kanal zu Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Zweck von Bereichen in Cost Management

Cost Management ist in allen Bereichen oberhalb von Ressourcen aktiv, damit Organisationen Kosten auf der Ebene verwalten können, auf der sie Zugriff haben, und zwar unabhängig davon, ob es sich um das gesamte Abrechnungskonto oder eine einzelne Ressourcengruppe handelt. Obwohl sich Abrechnungsbereiche je nach Vertrag mit Microsoft (Abonnementtyp) unterscheiden, gilt dies nicht für Azure RBAC-Bereiche.

## <a name="azure-rbac-scopes"></a>Azure RBAC-Bereiche

Azure unterstützt für die Ressourcenverwaltung drei Bereiche. Jeder Bereich unterstützt die Verwaltung von Zugriff und Governance, einschließlich, aber nicht beschränkt auf Kostenverwaltung.

- [**Verwaltungsgruppen**](../../governance/management-groups/overview.md): Hierarchische Container mit bis zu acht Ebenen zum Organisieren von Azure-Abonnements.

    Ressourcentyp: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Abonnements**: Primäre Container für Azure-Ressourcen.

    Ressourcentyp: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Ressourcengruppe**](../../azure-resource-manager/management/overview.md#resource-groups): Logische Gruppierungen zusammengehöriger Ressourcen für eine Azure-Lösung, die den gleichen Lebenszyklus haben. Dies sind z.B. Ressourcen, die gemeinsam bereitgestellt und gelöscht werden.

    Ressourcentyp: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Verwaltungsgruppen ermöglichen das Organisieren von Abonnements in einer Hierarchie. Sie können beispielsweise eine logische Organisationshierarchie mithilfe von Verwaltungsgruppen einrichten. Anschließend versehen Sie Teams mit Abonnements für Produktion- und Entwicklungs-/Testworkloads. Danach erstellen Sie Ressourcengruppen in den Abonnements, um die einzelnen Subsysteme oder Komponenten zu verwalten.

Der Aufbau einer Organisationshierarchie ermöglicht es, die Einhaltung von Kosten und Richtlinien unternehmensweit zu überprüfen. Anschließend kann jede Führungskraft ihre aktuellen Kosten einsehen und analysieren. Danach können sie Budgets erstellen, um ungünstige Ausgabemuster einzudämmen und Kosten zu optimieren, indem sie die Empfehlungen von Azure Advisor auf der untersten Ebene umsetzen.

Das Gewähren des Zugriffs zum Anzeigen der Kosten und zum optionalen Verwalten der Kostenkonfiguration, wie z. B. Budgets und Exporte, erfolgt über Governancebereiche unter Verwendung von Azure RBAC. Mit Azure RBAC gewähren Sie Azure AD-Benutzern und -Gruppen Zugriff auf einen vordefinierten Satz von Aktionen, die in einer Rolle für einen bestimmten Bereich und darunter definiert sind. So gewährt beispielsweise eine Rolle, die einem Verwaltungsgruppenbereich zugewiesen ist, auch die gleichen Berechtigungen für geschachtelte Abonnements und Ressourcengruppen.

Cost Management unterstützt für jeden der folgenden Bereiche die folgenden integrierten Rollen:

- [**Besitzer**](../../role-based-access-control/built-in-roles.md#owner): Ermöglicht das Anzeigen von Kosten und das Verwalten sämtlicher Aspekte einschließlich Kostenkonfiguration.
- [**Mitwirkender**](../../role-based-access-control/built-in-roles.md#contributor): Ermöglicht das Anzeigen von Kosten und das Verwalten sämtlicher Aspekte einschließlich Kostenkonfiguration, aber ausschließlich Zugriffssteuerung.
- [**Leser**](../../role-based-access-control/built-in-roles.md#reader): Ermöglicht das Anzeigen sämtlicher Daten, z. B. der Kostendaten und Konfiguration, aber nicht das Vornehmen von Änderungen.
- [**Cost Management-Mitwirkender**](../../role-based-access-control/built-in-roles.md#cost-management-contributor): Ermöglicht das Anzeigen der Kosten, das Verwalten der Kostenkonfiguration und die Anzeige von Empfehlungen.
- [**Cost Management-Leser**](../../role-based-access-control/built-in-roles.md#cost-management-reader): Ermöglicht die Anzeige von Kostendaten, Kostenkonfiguration und Empfehlungen.

„Cost Management-Mitwirkender“ ist die empfohlene Rolle mit geringstmöglichen Berechtigungen. Die Rolle ermöglicht den Benutzern die Erstellung und Verwaltung von Budgets und Exporten, um die Kosten effektiver zu überwachen und zu dokumentieren. Cost Management-Mitwirkende benötigen ggf. auch zusätzliche Rollen, um komplexe Kostenverwaltungsszenarien unterstützen zu können. Betrachten Sie die folgenden Szenarien:

- **Berichte zur Ressourcennutzung:** Azure Cost Management zeigt Kosten im Azure-Portal an. Dies umfasst die vollständigen Nutzungsmuster in Bezug auf die Kosten. Der Bericht kann auch Gebühren für die API und den Download anzeigen. Vielleicht möchten Sie aber auch detaillierte Nutzungsmetriken in Azure Monitor aufrufen, um ein tieferes Verständnis zu erhalten. Es empfiehlt sich, die Rolle [Überwachungsleser](../../role-based-access-control/built-in-roles.md#monitoring-reader) für jeden Bereich zu gewähren, in dem Sie auch Berichte zu detaillierten Nutzungsmetriken benötigen.
- **Eingreifen bei Überschreitung von Budgets:** Cost Management-Mitwirkende benötigen auch Zugriff für das Erstellen und Verwalten von Aktionsgruppen, um bei Überschreitungen automatisch zu reagieren. Erwägen Sie das Zuweisen der Rolle [Mitwirkender an der Überwachung](../../role-based-access-control/built-in-roles.md#monitoring-contributor) zu einer Ressourcengruppe, die die zu verwendende Aktionsgruppe enthält, wenn Budgetschwellenwerte überschritten werden. Die Automatisierung bestimmter Aktionen erfordert zusätzliche Rollen für die verwendeten spezifischen Dienste, wie z.B. Azure Automation und Azure Functions.
- **Planen des Exports von Kostendaten**: Cost Management-Mitwirkende benötigen auch Zugriff für das Verwalten von Speicherkonten, um einen Export zum Kopieren von Daten in ein Speicherkonto zu planen. Erwägen Sie das Zuweisen der Rolle [Speicherkontomitwirkender](../../role-based-access-control/built-in-roles.md#storage-account-contributor) zu einer Ressourcengruppe, die das Speicherkonto enthält, in das Kostendaten exportiert werden.
- **Anzeigen von Empfehlungen für Kosteneinsparungen**: Cost Management-Leser und Cost Management-Mitwirkende haben in der Standardeinstellung Zugriff auf die *Anzeige* von Empfehlungen. Der Zugriff auf die Kostenempfehlungen erfordert jedoch den Zugriff auf einzelne Ressourcen. Erwägen Sie, eine [dienstspezifische Rolle](../../role-based-access-control/built-in-roles.md#all) zuzulassen, wenn Sie auf eine kostenbasierte Empfehlung reagieren möchten.

Verwaltungsgruppen werden nur unterstützt, wenn sie Abonnements mit Enterprise Agreement (EA), nutzungsbasierter Bezahlung (Pay-as-you-go, PAYG) oder interne Microsoft-Abonnements enthalten. Für Verwaltungsgruppen mit anderen Abonnementtypen (z. B. Abonnements mit Microsoft-Kundenvereinbarung oder Azure Active Directory-Abonnements) können keine Kosten angezeigt werden. Wenn Sie über eine Mischung von Abonnements verfügen, verschieben Sie die nicht unterstützten Abonnements in einen separaten Arm der Verwaltungsgruppenhierarchie, um Cost Management für die unterstützten Abonnements zu aktivieren. Erstellen Sie z. B. zwei Verwaltungsgruppen unter der Stammverwaltungsgruppe: **Azure AD** und **Meine Organisation**. Verschieben Sie das Azure AD-Abonnement in die Verwaltungsgruppe **Azure AD**, und verwenden Sie dann die Verwaltungsgruppe **Meine Organisation** zum Anzeigen und Verwalten von Kosten.

## <a name="enterprise-agreement-scopes"></a>Enterprise Agreement-Bereiche

Enterprise Agreement-Abrechnungskonten (EA), sog. Registrierungen, haben die folgenden Bereiche:

- [**Abrechnungskonto**](../manage/view-all-accounts.md): Stellt eine EA-Registrierung dar. Rechnungen werden in diesem Bereich erstellt. Nicht nutzungsabhängige Käufe (Marketplace und Reservierungen) sind nur in diesem Bereich möglich. Sie werden in Abteilungen oder Registrierungskonten nicht berücksichtigt. Die Reservierungsnutzung wird zusammen mit der gesamten übrigen Nutzung auf einzelne Ressourcen angewendet. Die Nutzung wird in Abonnements innerhalb des Abrechnungskontos zusammengefasst. Wenn Sie die Reservierungskosten nach Ressource aufgeschlüsselt anzeigen möchten, wechseln Sie zur Ansicht **Amortisierte Kosten** in der Kostenanalyse.

    Ressourcentyp: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Abteilung** : Optionale Gruppierung von Registrierungskonten.

    Ressourcentyp: `Billing/billingAccounts/departments`

- **Registrierungskonto**: Stellt einen Besitzer eines einzelnen Kontos dar. Unterstützt nicht das Gewähren des Zugriffs für mehrere Personen.

    Ressourcentyp: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Governancebereiche sind ein einzelnes Verzeichnis gebunden, EA-Abrechnungsbereiche hingegen nicht. Ein EA-Abrechnungskonto kann Abonnements in einer beliebigen Anzahl von Azure AD-Verzeichnissen enthalten.

EA-Abrechnungsbereiche unterstützen die folgenden Rollen:

- **Unternehmensadministrator**: Ermöglicht die Verwaltung von und den Zugriff auf Abrechnungskontoeinstellungen, die Anzeige aller Kosten und die Verwaltung der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der EA-Abrechnungsbereich der [Azure-Rolle „Cost Management-Mitwirkender“](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Unternehmensbenutzer mit Leseberechtigung**: Ermöglicht das Anzeigen der Abrechnungskontoeinstellungen, Kostendaten und Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der EA-Abrechnungsbereich der [Azure-Rolle „Cost Management-Leser“](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Abteilungsadministrator**: Ermöglicht das Verwalten der Abteilungseinstellungen, wie z.B. Kostenstelle, sowie den Zugriff auf und die Anzeige aller Kosten sowie das Verwalten der Kostenkonfiguration. Beispiele: Budgets und Exporte.  Die Abrechnungskontoeinstellung **Abteilung – Gebühren anzeigen** muss für Abteilungsadministratoren und Benutzer mit Leseberechtigung aktiviert sein, um Kosten einzusehen. Wenn die Option **Abteilung – Gebühren anzeigen** deaktiviert ist, können Abteilungsbenutzer keine Kosten auf einer beliebigen Ebene einsehen, selbst wenn sie Konto- oder Abonnementbesitzer sind.
- **Abteilungsbenutzer mit Leseberechtigung**: Ermöglicht das Anzeigen der Abteilungseinstellungen, Kostendaten und Kostenkonfiguration. Beispiele: Budgets und Exporte. Wenn die Option **Abteilung – Gebühren anzeigen** deaktiviert ist, können Abteilungsbenutzer keine Kosten auf einer beliebigen Ebene einsehen, selbst wenn sie Konto- oder Abonnementbesitzer sind.
- **Kontobesitzer**: Ermöglicht das Verwalten von Registrierungskontoeinstellungen (z.B. Kostenstelle), das Anzeigen aller Kosten und das Verwalten der Kostenkonfiguration (z.B. Budgets und Exporte) für das Registrierungskonto. Die Abrechnungskontoeinstellung **Kontobesitzer – Gebühren anzeigen** muss für Kontobesitzer und Azure RBAC-Benutzer aktiviert sein, um Kosten einzusehen.

EA-Abrechnungskontobenutzer haben keinen direkten Zugriff auf Rechnungen. Rechnungen sind in einem externen Volumenlizenzierungssystem verfügbar.

Azure-Abonnements werden unter Registrierungskonten geschachtelt. Abrechnungsbenutzer haben Zugriff auf Kostendaten für die Abonnements und Ressourcengruppen, die sich unter ihren jeweiligen Bereichen befinden. Sie haben keinen Zugriff zum Anzeigen oder Verwalten von Ressourcen im Azure-Portal. Benutzer können Kosten einsehen, indem sie in der Liste der Dienste im Azure-Portal zu **Kostenverwaltung + Abrechnung** navigieren. Anschließend können sie die Kosten für die spezifischen Abonnements und Ressourcengruppen filtern, über die sie Berichte erstellen müssen.

Abrechnungsbenutzer haben keinen Zugriff auf Verwaltungsgruppen, da sie nicht explizit zu einem bestimmten Abrechnungskonto gehören. Der Zugriff muss Verwaltungsgruppen explizit gewährt werden. In Verwaltungsgruppen werden Kosten aus allen geschachtelten Abonnements zusammengetragen. Allerdings enthalten sie nur nutzungsbasierte Käufe. Sie enthalten keine Käufe wie Reservierungen und Marketplace-Angebote von Drittanbietern. Um diese Kosten anzuzeigen, verwenden Sie das EA-Abrechnungskonto.

## <a name="individual-agreement-scopes"></a>Bereiche für einzelne Verträge

Für aus einzelnen Angeboten wie nutzungsbasierte Bezahlung und verwandten Typen (z. B. kostenlose Testversionen und Entwicklungs-/Testangebote) erstellte Azure-Abonnements gibt es keinen expliziten Abrechnungskontobereich. Stattdessen hat jedes Abonnement einen Kontobesitzer oder Kontoadministrator, der mit dem EA-Kontobesitzer vergleichbar ist.

- [**Abrechnungskonto**](../manage/view-all-accounts.md): Stellt einen Besitzer mit einem einzelnen Konto für ein oder mehrere Azure-Abonnements dar. Unterstützt derzeit nicht das Gewähren des Zugriffs für mehrere Personen oder des Zugriffs auf aggregierte Kostenansichten.

    Ressourcentyp: Nicht verfügbar

Kontoadministratoren für einzelne Azure-Abonnements können Abrechnungsdaten, z. B. Rechnungen und Zahlungen, anzeigen, indem sie zu [Azure-Portal](https://portal.azure.com) > **Abonnements** navigieren und ein Abonnement auswählen.

Im Gegensatz zu EA können Kontoadministratoren für einzelne Azure-Abonnements ihre Rechnungen im Azure-Portal anzeigen. Beachten Sie, dass die Rollen „Cost Management-Leser“ und „Cost Management-Mitwirkender“ keinen Zugriff auf Rechnungen bieten. Weitere Informationen finden Sie unter [Gewähren des Zugriffs auf Rechnungen](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Bereiche für Microsoft-Kundenvereinbarung

Abrechnungskonten der Microsoft-Kundenvereinbarung haben die folgenden Bereiche:

- **Abrechnungskonto**: Stellt eine Kundenvereinbarung für mehrere Microsoft-Produkte und -Dienste dar. Abrechnungskonten der Microsoft-Kundenvereinbarung bieten nicht dieselben Funktionen wie EA-Registrierungen. EA-Registrierungen orientieren sich eher an Abrechnungsprofilen.

    Ressourcentyp: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Abrechnungsprofil**: Definiert die Abonnements, die in einer Rechnung berücksichtigt werden. Abrechnungsprofile entsprechen funktional einer EA-Registrierung, da dies der Bereich ist, in dem Rechnungen generiert werden. Nicht nutzungsabhängige Käufe (wie Marketplace und Reservierungen) sind auch nur in diesem Bereich möglich. Sie sind nicht in Rechnungsabschnitten enthalten.

    Ressourcentyp: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Rechnungsabschnitt**: Stellt eine Gruppe von Abonnements in einer Rechnung oder einem Abrechnungsprofil dar. Rechnungsabschnitte ähneln Abteilungen, d.h. mehrere Personen haben Zugriff auf einen Rechnungsabschnitt.

    Ressourcentyp: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Kunde**: Stellt eine Gruppe von Abonnements dar, die einem bestimmten Kunden zugeordnet sind, der von einem Partner in eine Microsoft-Kundenvereinbarung aufgenommen wird. Dieser Bereich gilt spezifisch für Cloud Solution Provider (CSP).

Im Gegensatz zu EA-Abrechnungsbereichen _sind_ Abrechnungskonten der Kundenvereinbarung an ein einzelnes Verzeichnis gebunden und lassen keine Abonnements in mehreren Azure AD-Verzeichnissen zu.

Abrechnungsbereiche der Kundenvereinbarung gelten nicht für Partner. Partnerrollen und -berechtigungen sind unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview) dokumentiert.

Abrechnungsbereiche der Kundenvereinbarung unterstützen die folgenden Rollen:

- **Besitzer**: Ermöglicht die Verwaltung von und den Zugriff auf Abrechnungseinstellungen, die Anzeige aller Kosten und die Verwaltung der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht dieser Abrechnungsbereich der Kundenvereinbarung der [Azure-Rolle „Cost Management-Mitwirkender“](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Mitwirkender**: Ermöglicht die Verwaltung von und den Zugriff auf Abrechnungseinstellungen, die Anzeige aller Kosten und die Verwaltung der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht dieser Abrechnungsbereich der Kundenvereinbarung der [Azure-Rolle „Cost Management-Mitwirkender“](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Leser**: Ermöglicht die Anzeige von Abrechnungseinstellungen, Kostendaten und der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht dieser Abrechnungsbereich der Kundenvereinbarung der [Azure-Rolle „Cost Management-Leser“](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Rechnungs-Manager**: Ermöglicht das Anzeigen und Bezahlen von Rechnungen sowie das Anzeigen von Kostendaten und Konfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht dieser Abrechnungsbereich der Kundenvereinbarung der [Azure-Rolle „Cost Management-Leser“](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-Abonnementersteller**: Ermöglicht das Erstellen von Azure-Abonnements, Anzeigen von Kosten und Verwalten der Kostenkonfiguration. Beispiele: Budgets und Exporte. In der Praxis entspricht der Abrechnungsbereich der Kundenvereinbarung der EA-Rolle „Registrierungskontobesitzer“.

Azure-Abonnements werden ähnlich wie unter EA-Registrierungskonten unter Rechnungsabschnitten geschachtelt. Abrechnungsbenutzer haben Zugriff auf Kostendaten für die Abonnements und Ressourcengruppen, die sich unter ihren jeweiligen Bereichen befinden. Sie haben jedoch keinen Zugriff zum Anzeigen oder Verwalten von Ressourcen im Azure-Portal. Abrechnungsbenutzer können Kosten einsehen, indem sie in der Liste der Dienste im Azure-Portal zu **Kostenverwaltung + Abrechnung** navigieren. Anschließend können sie die Kosten für die spezifischen Abonnements und Ressourcengruppen filtern, über die sie Berichte erstellen müssen.

Abrechnungsbenutzer haben keinen Zugriff auf Verwaltungsgruppen, da sie nicht explizit zum Abrechnungskonto gehören. Wenn jedoch Verwaltungsgruppen für die Organisation aktiviert sind, werden alle Abonnementkosten im Abrechnungskonto und in der Stammverwaltungsgruppe zusammengeführt, da sie beide auf ein einziges Verzeichnis beschränkt sind. Verwaltungsgruppen enthalten nur Käufe, die nutzungsbasiert sind. Käufe wie Reservierungen und Marketplace-Angebote von Drittanbietern fließen nicht in Verwaltungsgruppen ein. Daher können das Abrechnungskonto und die Stammverwaltungsgruppe unterschiedliche Summen ausweisen. Um diese Kosten anzuzeigen, verwenden Sie das Abrechnungskonto oder entsprechende Abrechnungsprofil.

## <a name="aws-scopes"></a>AWS-Bereiche

Wenn die AWS-Integration abgeschlossen ist, finden Sie weitere Informationen unter [Einrichten und Konfigurieren der AWS-Integration](aws-integration-set-up-configure.md). Folgende Bereiche sind verfügbar:

- **Externes Abrechnungskonto**: Repräsentiert eine Kundenvereinbarung mit einem Drittanbieter. Es ist vergleichbar mit dem Abrechnungskonto für Enterprise Agreements.

    Ressourcentyp: `Microsoft.CostManagement/externalBillingAccounts`

- **Externes Abonnement**: Repräsentiert ein operatives Kundenkonto bei einem Drittanbieter. Es ist vergleichbar mit einem Azure-Abonnement.

    Ressourcentyp: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Bereiche für Cloud Solution Provider (CSP)

Die folgenden Bereiche werden für CSPs mit Kunden in einer Microsoft-Kundenvereinbarung unterstützt:

- **Abrechnungskonto**: Stellt eine Kundenvereinbarung für mehrere Microsoft-Produkte und -Dienste dar. Abrechnungskonten der Microsoft-Kundenvereinbarung bieten nicht dieselben Funktionen wie EA-Registrierungen. EA-Registrierungen orientieren sich eher an Abrechnungsprofilen.

    Ressourcentyp: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Abrechnungsprofil**: Definiert die Abonnements, die in einer Rechnung berücksichtigt werden. Abrechnungsprofile entsprechen funktional einer EA-Registrierung, da dies der Bereich ist, in dem Rechnungen generiert werden. Nicht nutzungsabhängige Käufe (wie Marketplace und Reservierungen) sind auch nur in diesem Bereich möglich.

    Ressourcentyp: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Kunde**: Stellt eine Gruppe von Abonnements dar, die einem bestimmten Kunden zugeordnet sind, der von einem Partner in eine Microsoft-Kundenvereinbarung aufgenommen wird.

Nur die Benutzer mit den Rollen *Globaler Administrator* und *Administrator-Agent* können Kosten für Abrechnungskonten, Abrechnungsprofile und Kunden direkt im Azure-Mandanten des Partners verwalten und anzeigen. Weitere Informationen zu Partner Center-Rollen finden Sie unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview).

CSP-Partnerkunden werden von Azure Cost Management nur unterstützt, wenn die Kunden über eine Microsoft-Kundenvereinbarung verfügen. Informationen zu von CSP unterstützten Kunden ohne Microsoft-Kundenvereinbarung finden Sie unter [Übersicht über Partner Center](/azure/cloud-solution-provider/overview/partner-center-overview).

Verwaltungsgruppen in CSP-Bereichen werden von Cost Management nicht unterstützt. Wenn Sie über ein CSP-Abonnement verfügen und den Bereich für eine Verwaltungsgruppe in der Kostenanalyse festlegen, wird eine Fehlermeldung ähnlich der folgenden angezeigt:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Umschalten zwischen Bereichen in Cost Management

Alle Cost Management-Ansichten im Azure-Portal enthalten links oben die Auswahloption **Bereich**. Hierüber können Sie den Bereich rasch ändern. Wählen Sie die Option **Bereich** aus, um die Bereichsauswahl zu öffnen. Hier werden Abrechnungskonten, die Stammverwaltungsgruppe und alle Abonnements angezeigt, die nicht unter der Stammverwaltungsgruppe geschachtelt sind. Um einen Bereich auszuwählen, klicken Sie auf den Hintergrund, um ihn zu markieren, und dann unten auf **Auswählen**. Für eine Detailsuche in geschachtelten Bereichen, wie beispielsweise Ressourcengruppen in einem Abonnement, wählen Sie den Link „Bereichsname“ aus. Um den übergeordneten Bereich auf einer geschachtelten Ebene auszuwählen, wählen Sie oben in der Bereichsauswahl **Diesen &lt;Bereich&gt; auswählen** aus.

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
3. Wählen Sie den Namen des Abrechnungsprofils aus.
4. Wählen Sie im Menü „Abrechnungsprofil“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die ID des Abrechnungskontos.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Rechnungsabschnitte

1. Öffnen Sie das Azure-Portal, und navigieren Sie dann in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Rechnungsabschnitte** aus.
3. Wählen Sie den Namen des Rechnungsabschnitts aus.
4. Wählen Sie im Menü „Rechnungsabschnitt“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die IDs der Rechnungsabschnitte.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-Abteilungen

1. Öffnen Sie das Azure-Portal, und navigieren Sie dann in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Abteilungen** aus.
3. Wählen Sie den Namen der Abteilung aus.
4. Wählen Sie im Menü „Abteilung“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die Abteilungs-IDs.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA-Registrierungskonto

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Kostenverwaltung und Abrechnung**.
2. Wählen Sie im Menü „Abrechnungskonto“ **Registrierungskonten** aus.
3. Wählen Sie den Namen des Registrierungskontos aus.
4. Wählen Sie im Menü „Registrierungskonto“ **Eigenschaften** aus.
5. Kopieren Sie das Abrechnungskonto und die Registrierungskonto-IDs.
6. Ihr Bereich ist: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Verwaltungsgruppe

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Verwaltungsgruppen**.
2. Navigieren Sie zur Verwaltungsgruppe.
3. Kopieren Sie die Verwaltungsgruppen-ID aus der Tabelle.
4. Ihr Bereich ist: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscription

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Abonnements**.
2. Kopieren Sie die Abonnement-ID aus der Tabelle.
3. Ihr Bereich ist: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Ressourcengruppen

1. Öffnen Sie das Azure-Portal, und navigieren Sie in der Liste der Dienste zu **Ressourcengruppen**.
2. Wählen Sie den Namen der Ressourcengruppe aus.
3. Wählen Sie im Menü „Ressourcengruppe“ **Eigenschaften** aus.
4. Kopieren Sie den Wert des Felds „Ressourcen-ID“.
5. Ihr Bereich ist: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management wird derzeit in [Azure Global](https://management.azure.com) und [Azure Government](https://management.usgovcloudapi.net) unterstützt. Weitere Informationen zu Azure Government finden Sie unter [API-Endpunkte für Azure Global und Azure Government](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Nächste Schritte

- Falls Sie den ersten Schnellstart für Cost Management noch nicht abgeschlossen haben, lesen Sie ihn unter [Erste Schritte in der Analyse von Kosten](quick-acm-cost-analysis.md).