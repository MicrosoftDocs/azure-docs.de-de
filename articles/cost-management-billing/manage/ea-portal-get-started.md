---
title: Erste Schritte mit dem Azure Enterprise Portal
description: In diesem Artikel wird erläutert, wie Azure EA-Kunden (Azure Enterprise Agreement) das Azure Enterprise Portal verwenden.
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 88bb4c24489b973aa4230ba6f2b34756f83c7dda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030687"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Erste Schritte mit dem Azure Enterprise Portal

Dieser Artikel unterstützt direkte und indirekte Azure EA-Kunden (Azure Enterprise Agreement) beim Einstieg in die Verwendung des [Azure Enterprise Portals](https://ea.azure.com). Sie erhalten grundlegende Informationen zu folgenden Themen:

- Struktur des Azure Enterprise Portals
- Im Azure Enterprise Portal verwendete Rollen
- Erstellung von Abonnements
- Kostenanalyse im Azure Enterprise Portal und im Azure-Portal

## <a name="get-started-with-ea-onboarding"></a>Erste Schritte beim Onboarding von EA

Einen Onboardingleitfaden zu Azure EA finden Sie unter [Onboardingleitfaden zu Azure EA (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).

In diesem Video können Sie sich eine vollständige Onboardingsitzung für das Azure Enterprise Portal ansehen:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>Grundlegendes zu EA-Benutzerrollen und Einführung in die Benutzerhierarchie

Azure-Kunden mit einem Enterprise Agreement (EA) können zur Verwaltung ihrer Organisation und ihrer Ausgaben fünf verschiedene Administratorrollen zuweisen:

- Unternehmensadministrator
- Unternehmensadministrator (nur Leseberechtigung)
- Abteilungsadministrator
- Abteilungsadministrator (nur Leseberechtigung)
- Kontobesitzer

Für jede Rolle gelten unterschiedliche Benutzergrenzwerte und -berechtigungen. Weitere Informationen finden Sie unter [Organisationsstruktur und Berechtigungen nach Rolle](./understand-ea-roles.md#organization-structure-and-permissions-by-role).

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>Aktivieren der Registrierung, Erstellen eines Abonnements und andere administrative Aufgaben

Weitere Informationen zum Aktivieren der Registrierung, zum Erstellen einer Abteilung oder eines Abonnements, zum Hinzufügen von Administratoren und Kontobesitzern sowie zu anderen administrativen Aufgaben finden Sie unter [Azure EA-Portalverwaltung](./ea-portal-administration.md).

Wenn Sie mehr über das Übertragen eines Enterprise-Abonnements auf ein Abonnement mit nutzungsbasierter Zahlung erfahren möchten, lesen Sie die Informationen unter [Azure Enterprise-Übertragungen](./ea-transfers.md).

## <a name="view-usage-summary-and-download-reports"></a>Anzeigen der Verwendungszusammenfassung und Herunterladen von Berichten

Sie können Ihre Azure EA-Rechnung verwalten und Schritte im Zusammenhang mit der Rechnung ausführen. Ihre Rechnung stellt Ihre Abrechnungsdaten dar und sollte daher auf ihre Richtigkeit überprüft werden.

Informationen zum Anzeigen der Nutzungszusammenfassung, zum Herunterladen von Berichten und zum Verwalten von Registrierungsrechnungen finden Sie unter [Rechnungen für die Azure Enterprise-Registrierung](./ea-portal-enrollment-invoices.md).

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>Nachdem Sie sich mit den Grundlagen vertraut gemacht haben, finden Sie hier einige zusätzliche Links, die Ihnen beim Onboarding helfen.

Unter [Azure EA-Preise](./ea-pricing-overview.md) finden Sie ausführliche Informationen zur Berechnung der Nutzung sowie zu den Gebühren für verschiedene Azure-Dienste im Enterprise Agreement, das komplexere Berechnungen enthält.

Unter [Reservierte Azure EA-VM-Instanzen](./ea-portal-vm-reservations.md) erfahren Sie, wie Azure-Reservierungen für reservierte VM-Instanzen zur Senkung der Kosten für Ihre Unternehmensregistrierung beitragen können.

Informationen zu den REST-APIs, die mit der Azure Enterprise-Registrierung verwendet werden sollen, sowie eine Erläuterung zum Beheben allgemeiner Probleme mit REST-APIs finden Sie unter [Azure Enterprise-REST-APIs](./ea-portal-rest-apis.md).

Unter [Azure EA: Vereinbarungen und Ergänzungen](./ea-portal-agreements.md) wird beschrieben, wie sich Azure EA-Vereinbarungen und -Zusatzvereinbarungen darauf auswirken, wie Sie auf Azure-Dienste zugreifen, diese verwenden und für diese bezahlen.

Im Artikel [Azure Marketplace](./ea-azure-marketplace.md) wird erläutert, wie EA-Kunden und -Partner die Marketplace-Gebühren anzeigen und Azure Marketplace-Käufe aktivieren können.

Erläuterungen zu den allgemeinen Aufgaben, die ein Partner-EA-Administrator im Azure EA-Portal durchführt, finden Sie unter [Azure EA-Portalverwaltung für Partner](./ea-partner-portal-administration.md).

## <a name="get-started-on-azure-ea---faq"></a>Häufig gestellte Fragen zu den ersten Schritten mit Azure EA

Dieser Abschnitt enthält ausführliche Informationen zu typischen Fragen, die Kunden während des Onboardingprozesses stellen.  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Ich habe der Azure EA-Registrierung versehentlich mein vorhandenes Azure-Konto zugeordnet. Dadurch ist mein monatliches Guthaben verloren gegangen. Kann ich mein monatliches Guthaben wieder zurückerlangen?

Wenn Sie sich als Azure EA-Kontobesitzer mit den Anmeldeinformationen Ihres Visual Studio-Abonnements angemeldet haben, können Sie den individuellen Azure-Vorteil Ihres Visual Studio-Abonnements wiederherstellen, indem Sie eine der folgenden Aktionen ausführen:

- Löschen Sie den Kontobesitzer aus dem Azure Enterprise Portal, nachdem Sie die alle zugehörigen Azure-Abonnements entfernt oder verschoben haben. Registrieren Sie sich anschließend erneut für individuelle Visual Studio-Azure-Vorteile.
- Löschen Sie den Visual Studio-Abonnenten aus der Verwaltungswebsite in VLSC, und weisen Sie das Abonnement diesmal einem Konto mit anderen Anmeldeinformationen zu. Registrieren Sie sich anschließend erneut für individuelle Visual Studio-Azure-Vorteile.

### <a name="what-type-of-subscription-should-i-create"></a>Welche Art von Abonnement soll ich erstellen?

Im Azure Enterprise Portal stehen für Unternehmenskunden zwei Arten von Abonnements zur Verfügung:

- Microsoft Azure Enterprise. Ideal für:
  - Jegliche Verwendung in der Produktion
  - Beste Preise basierend auf Infrastrukturausgaben

  [Wenden Sie sich an den Azure-Vertrieb](https://azure.microsoft.com/pricing/enterprise-agreement/), um weitere Informationen zu erhalten.

- Enterprise Dev/Test. Ideal für:
  - Sämtliche Team-Dev/Test-Workloads
  - Einzelne Dev/Test-Workloads mit mittlerem bis hohem Umfang
  - Zugriff auf spezielle MSDN-Images und Vorzugstarife für Dienste

  Weitere Informationen finden Sie im [Enterprise Dev/Test-Angebot](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Der Name meines Abonnements ist mit dem Angebotsnamen identisch. Soll ich den Abonnementnamen auf einen aussagekräftigen Namen für meine Organisation festlegen?

Wenn Sie ein Abonnement erstellen, wird standardmäßig der Name des gewählten Angebotstyps verwendet. Es empfiehlt sich, den Abonnementnamen in einen Namen zu ändern, der die Nachverfolgung des Abonnements erleichtert.

So ändern Sie den Namen

1. Melden Sie sich bei [https://account.windowsazure.com](https://account.windowsazure.com) an.
1. Wählen Sie die Abonnementliste aus.
1. Wählen Sie das Abonnement aus, das Sie bearbeiten möchten.
1. Klicken Sie auf das Symbol **Abonnement verwalten**.
1. Bearbeiten Sie die Abonnementdetails.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Wie kann ich Kosten nach Kostenstelle nachverfolgen?

Wenn Sie Kosten nach Kostenstelle nachverfolgen möchten, müssen Sie die Kostenstelle auf einer der folgenden Ebenen definieren:

- Department
- Konto
- Subscription

Abhängig von Ihren Anforderungen können Sie die gleiche Kostenstelle verwenden, um die Nutzung und Kosten nachzuverfolgen, die einer bestimmten Kostenstelle zugeordnet sind.

Wenn Sie also beispielsweise die Kosten für ein spezielles Projekt nachverfolgen möchten, an dem mehrere Abteilungen beteiligt sind, können Sie die Kostenstelle auf der Abonnementebene definieren, um die Nutzung und die Kosten nachzuverfolgen.

Eine Kostenstelle kann nicht auf der Dienstebene definiert werden. Falls Sie die Nutzung auf Dienstebene nachverfolgen möchten, können Sie das auf der Dienstebene zur Verfügung stehende _Tag_-Feature verwenden.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Wie kann ich die Nutzung und die Ausgaben verschiedener Abteilungen in meiner Organisation nachverfolgen?

Sie können unter Ihrer Azure EA-Registrierung beliebig viele Abteilungen erstellen. Zur ordnungsgemäßen Nachverfolgung der Nutzung müssen Sie sicherstellen, dass Abonnements nicht abteilungsübergreifend verwendet werden.

Nachdem Sie Abteilungen und Abonnements erstellt haben, werden im Nutzungsbericht Daten angezeigt. Diese Informationen können Ihnen dabei helfen, die Nutzung zu verfolgen und Kosten und Ausgaben auf Abteilungsebene zu verwalten.

Sie können auch über die Berichterstellungs-API auf Nutzungsdaten zugreifen. Ausführliche Informationen und Beispielcode finden Sie unter [Azure Enterprise-REST-APIs](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Kann ich ein Ausgabenkontingent festlegen und Warnungen erhalten, wenn ich mich dem Grenzwert nähere?

Ein Ausgabenkontingent kann auf der Abteilungsebene festgelegt werden. Daraufhin werden Sie automatisch vom System benachrichtigt, wenn Ihre Ausgabenlimits 50 Prozent, 75 Prozent, 90 Prozent und 100 Prozent des von Ihnen definierten Kontingents erreichen.

Wählen Sie zum Definieren des Ausgabenkontingents eine Abteilung aus, und klicken Sie dann auf das Bearbeitungssymbol. Nachdem Sie die Details des Ausgabenlimits bearbeitet haben, klicken Sie auf **Speichern**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Ich habe Ressourcengruppen verwendet, um die RBAC zu implementieren und die Nutzung zu verfolgen. Wie kann ich die zugehörigen Nutzungsdetails anzeigen?

Wenn Sie _Ressourcengruppen_ und _Tags_ verwenden, werden diese Informationen auf Dienstebene nachverfolgt, und Sie können in der CSV-Downloaddatei mit ausführlichen Nutzungsdaten darauf zugreifen. Weitere Informationen finden Sie im Azure Enterprise Portal unter [Nutzungsbericht herunterladen](https://ea.azure.com/report/downloadusage).

Sie können auch über API auf die Nutzung zugreifen. Ausführliche Informationen und Beispielcode finden Sie unter [Azure Enterprise-REST-APIs](./ea-portal-rest-apis.md).

> [!NOTE]
> Sie können Tags nur auf Ressourcen anwenden, die Azure Resource Manager-Vorgänge unterstützen. Wenn Sie virtuelle Computer, virtuelle Netzwerke oder Speicher über das klassische Bereitstellungsmodell erstellt haben (etwa über das klassische Azure-Portal), kann auf diese Ressourcen kein Tag angewendet werden. Diese Ressourcen müssen per Resource Manager neu bereitgestellt werden, um die Verwendung von Tags zu unterstützen. Alle anderen Ressourcen unterstützen die Markierung durch Tags.

### <a name="can-i-perform-analyses-using-power-bi"></a>Kann ich Analysen mit Power BI durchführen?

Ja. Mit dem Microsoft Azure Enterprise-Inhaltspaket für Power BI können Sie folgende Aktionen ausführen:

- Schnelles Importieren und Analysieren des Azure-Verbrauchs für Ihre Unternehmensregistrierung
- Herausfinden, welche Abteilung, welches Konto oder welches Abonnement den größten Verbrauch generiert hat
- Erfahren, welcher Dienst in Ihrer Organisation am meisten verwendet wurde
- Verfolgen von Ausgaben und Nutzungstrends

So verwenden Sie Power BI

1. Rufen Sie die Power BI-Website auf.
1. Melden Sie sich mit einem gültigen Geschäfts-, Schul- oder Unikonto an.

   Das Geschäfts-, Schul- oder Unikonto kann das gleiche Konto sein, das auch für den Zugriff auf die Registrierung über das Azure Enterprise Portal verwendet wird. Dies ist jedoch nicht zwingend erforderlich.
1. Wählen Sie im Dashboard der Dienste die Kachel „Microsoft Azure Enterprise“ aus, und klicken Sie auf **Verbinden**.
1. Geben Sie im Bildschirm **Verbindung mit Azure Enterprise herstellen** Folgendes ein:
    - URL der Azure-Umgebung: [https://ea.azure.com](https://ea.azure.com)
    - Anzahl von Monaten: zwischen 1 und 36
    - Registrierungsnummer: Ihre Registrierungsnummer
1. Wählen Sie **Weiter** aus.
1. Geben Sie den API-Schlüssel im Feld **Kontoschlüssel** ein.

   Den API-Schlüssel finden Sie im Azure Enterprise Portal. Sehen Sie auf der Registerkarte **Nutzung herunterladen** nach, und wählen Sie dann **API-Zugriffsschlüssel** aus. Kopieren Sie den Schlüssel, und fügen Sie ihn in Power BI im Feld **Kontoschlüssel** ein.

Abhängig von der Größe des Datasets kann es zwischen fünf und 30 Minuten dauern, bis die Daten in Power BI geladen sind.

Die Power BI-Berichterstellung steht für direkte Azure EA-Kunden sowie für Partner und indirekte Kunden zur Verfügung, die Zugriff auf Abrechnungsinformationen haben.

## <a name="next-steps"></a>Nächste Schritte

- Azure Enterprise Portal-Administratoren finden unter [Azure Enterprise Portal-Verwaltung](ea-portal-administration.md) Informationen zu allgemeinen Verwaltungsaufgaben.
- Hilfe zu Problemen im Azure Enterprise Portal finden Sie unter [Beheben von Zugriffsproblemen beim Azure Enterprise Portal](ea-portal-troubleshoot.md).