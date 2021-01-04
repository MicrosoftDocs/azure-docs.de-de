---
title: Übersicht über „Azure Cost Management + Abrechnung“
description: Mit den Features „Azure Cost Management + Abrechnung“ können Sie Verwaltungsaufgaben für die Abrechnung ausführen und den Abrechnungszugriff auf Kosten verwalten. Außerdem können Sie mithilfe dieser Features Azure-Ausgaben überwachen und steuern sowie die Nutzung von Azure-Ressourcen optimieren.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34034a99641d75e44783cb5b87af8948b4db1628
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029939"
---
# <a name="what-is-azure-cost-management--billing"></a>Was ist „Azure Cost Management + Abrechnung“?

Mithilfe der Microsoft-Cloud können Sie die technische Leistung Ihrer geschäftlichen Workloads erheblich verbessern. Sie kann auch Ihre Kosten und den Mehraufwand senken, der zum Verwalten von Unternehmensressourcen erforderlich ist. Allerdings entsteht aufgrund der Möglichkeit von Verschwendung und Ineffizienzen, die in Ihre Cloudbereitstellungen eingeführt werden, ein Risiko für die Geschäftschance. „Azure Cost Management + Abrechnung“ ist eine Suite von Microsoft-Tools, die Ihnen beim Analysieren, Verwalten und Optimieren der Kosten für Ihre Workloads helfen. Mithilfe dieser Suite können Sie sicherstellen, dass Ihre Organisation die Vorteile der Cloud nutzt.

Sie können sich Ihre Azure-Workloads wie die Lampen in Ihrem Zuhause vorstellen. Bleiben die Lampen eingeschaltet, wenn Sie es morgens verlassen? Könnten Sie verschiedene effizientere Glühbirnen verwenden, um Ihre monatliche Stromrechnung zu senken? Gibt es in einem Zimmer mehr Lampen, als darin eigentlich erforderlich sind? Mit „Azure Cost Management + Abrechnung“ können Sie einen ähnlichen Denkprozess auf die von Ihrer Organisation genutzten Workloads anwenden.

Bei Azure-Produkten und -Diensten bezahlen Sie nur für die tatsächliche Nutzung. Wenn Sie Azure-Ressourcen erstellen und nutzen, werden Ihnen die Ressourcen in Rechnung gestellt. Aufgrund der Erleichterung bei der Bereitstellung neuer Ressourcen können die Kosten für Ihre Workloads ohne entsprechende Analyse und Überwachung erheblich steigen. Sie verwenden die Features „Azure Cost Management + Abrechnung“ für folgende Aufgaben:

- Ausführen administrativer Abrechnungsaufgaben, z. B. Bezahlen Ihrer Rechnung
- Verwalten des Abrechnungszugriffs auf Kosten
- Herunterladen der Kosten- und Nutzungsdaten, die zum Generieren Ihrer monatlichen Rechnung verwendet wurden
- Proaktives Anwenden der Datenanalyse auf Ihre Kosten
- Festlegen von Schwellenwerten für Ausgaben
- Erkennen von Möglichkeiten für Workloadänderungen, wodurch Ihre Ausgaben optimiert werden können

Wenn Sie sich weiter über den Kostenverwaltungsansatz von Organisationen informieren möchten, sehen Sie den Artikel [Azure Cost Management – bewährte Methoden](./costs/cost-mgt-best-practices.md) ein.

## <a name="understand-azure-billing"></a>Grundlegendes zur Azure-Abrechnung

Azure-Abrechnungsfeatures ermöglichen die Überprüfung Ihrer abgerechneten Kosten sowie die Verwaltung des Zugriffs auf Abrechnungsinformationen. In größeren Organisationen sind für Abrechnungsaufgaben in der Regel die Beschaffungs- und Finanzteams zuständig.

Ein Abrechnungskonto wird erstellt, wenn Sie sich für die Nutzung von Azure registrieren. Sie verwenden Ihr Abrechnungskonto zum Verwalten Ihrer Rechnungen und Zahlungen sowie zum Nachverfolgen von Kosten. Sie können über Zugriff auf mehrere Abrechnungskonten verfügen. Es kann beispielsweise sein, dass Sie sich für Ihre persönlichen Projekte für Azure registriert haben. Daher verfügen Sie möglicherweise über ein einzelnes Azure-Abonnement mit einem Abrechnungskonto. Unter Umständen können Sie auch über das Enterprise Agreement Ihrer Organisation oder über eine Microsoft-Kundenvereinbarung zugreifen. In diesen Szenarien verfügen Sie dann jeweils über ein separates Abrechnungskonto.

### <a name="billing-accounts"></a>Abrechnungskonten

Im Azure-Portal werden aktuell folgende Arten von Abrechnungskonten unterstützt:

- **Microsoft Online Services-Programm**: Ein individuelles Abrechnungskonto für ein Microsoft Online Services-Programm wird erstellt, wenn Sie sich über die Azure-Website für Azure registrieren. Beispiele hierfür sind die Registrierung für ein Kostenloses Azure-Konto, ein Angebot mit nutzungsbasierter Bezahlung oder als Visual Studio-Abonnent.

- **Enterprise Agreement**: Ein Abrechnungskonto für ein Enterprise Agreement wird erstellt, wenn Ihre Organisation ein Enterprise Agreement (EA) für die Nutzung von Azure unterzeichnet.

- **Microsoft-Kundenvereinbarung**: Ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung wird erstellt, wenn Ihre Organisation eine Microsoft-Kundenvereinbarung über einen Microsoft-Vertreter unterzeichnet. Einige Kunden in ausgewählten Regionen, die sich über die Azure-Website für ein Angebot mit nutzungsbasierter Bezahlung registrieren oder ein Upgrade ihres kostenlosen Azure-Kontos ausführen, verfügen möglicherweise auch über ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung.

### <a name="scopes-for-billing-accounts"></a>Bereiche für Abrechnungskonten
Ein Bereich ist ein Knoten in einem Abrechnungskonto, den Sie zum Anzeigen und Verwalten der Abrechnung verwenden. Dort verwalten Sie Abrechnungsdaten, Zahlungen und Rechnungen und führen allgemeine Kontoverwaltungsaufgaben aus.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services-Programm

|`Scope`  |Definition  |
|---------|---------|
|Abrechnungskonto     | Stellt einen einzigen Besitzer (Kontoadministrator) für ein oder mehrere Azure-Abonnements dar. Ein Kontoadministrator ist berechtigt, verschiedene Abrechnungsaufgaben wie das Erstellen von Abonnements, Anzeigen von Rechnungen oder Ändern der Abrechnung für Abonnements auszuführen.  |
|Subscription     |  Stellt eine Gruppierung von Azure-Ressourcen dar. Eine Rechnung wird für den Abonnementbereich generiert. Für jedes Abonnement gelten eigene Zahlungsmethoden, die zum Bezahlen der Rechnung verwendet werden.|

#### <a name="enterprise-agreement"></a>Enterprise Agreement

|`Scope`  |Definition  |
|---------|---------|
|Abrechnungskonto    | Stellt eine Enterprise Agreement-Registrierung dar. Die Rechnung wird im Abrechnungskontobereich generiert. Die Struktur basiert auf Abteilungen und Registrierungskonten.  |
|Department     |  Optionale Gruppierung von Registrierungskonten.      |
|Registrierungskonto     |  Stellt einen einzigen Kontobesitzer dar. Azure-Abonnements werden im Registrierungskontobereich erstellt.  |

#### <a name="microsoft-customer-agreement"></a>Microsoft-Kundenvereinbarung

|`Scope`  |Aufgaben  |
|---------|---------|
|Abrechnungskonto     |   Stellt eine Kundenvereinbarung für mehrere Microsoft-Produkte und -Dienste dar. Das Abrechnungskonto wird mithilfe von Abrechnungsprofilen und Rechnungsabschnitten strukturiert.   |
|Abrechnungsprofil     |  Stellt eine Rechnung und die zugehörigen Zahlungsmethoden dar. In diesem Bereich wird die Rechnung generiert. Das Abrechnungsprofil kann mehrere Rechnungsabschnitte enthalten.      |
|Rechnungsabschnitt     |   Stellt eine Gruppe von Kosten in einer Rechnung dar. Dem Rechnungsabschnittbereich werden Abonnements und andere Käufe zugeordnet.    |

## <a name="understand-azure-cost-management"></a>Grundlegendes zur Azure-Kostenverwaltung

Die Abrechnung steht zwar in enger Beziehung zur Kostenverwaltung, ist jedoch nicht identisch mit dieser. Bei der Abrechnung werden Kunden Waren oder Dienstleistungen in Rechnungen gestellt, zudem werden Geschäftsbeziehungen verwaltet.

Cost Management zeigt die Kosten und Verwendungsmuster der Organisation mit erweiterten Analysen. Berichte in Cost Management zeigen die nutzungsbasierten Kosten von Azure-Diensten und Marketplace-Angeboten von Drittanbietern an. Kosten basieren auf vereinbarten Preisen und berücksichtigen die Reservierung und Rabatte für den Azure-Hybridvorteil. Die Berichte liefern eine Übersicht Ihrer internen und externen Kosten für die Nutzung sowie Azure Marketplace-Gebühren. Andere Gebühren wie Reservierungseinkäufe, Support und Steuern werden in den Berichten noch nicht ausgewiesen. Anhand der Berichte verstehen Sie Ihre Ausgaben und Ihre Ressourcennutzung, und Sie können außergewöhnliche Ausgaben ermitteln. Vorhersageanalysen sind ebenfalls verfügbar. Cost Management zeigt anhand von Azure-Verwaltungsgruppen, Budgets und Empfehlungen deutlich die Struktur Ihrer Ausgaben sowie Möglichkeiten der Kostensenkung auf.

Über das Azure-Portal oder mithilfe diverser APIs können Sie Exportvorgänge automatisieren, um Kostendaten in externe Systeme und Prozesse zu integrieren. Automatisierter Abrechnungsdatenexport und geplante Berichte sind ebenfalls verfügbar.

Sehen Sie das Übersichtsvideo zu Azure Cost Management an, um sich einen schnellen Überblick zu verschaffen, wie Azure Cost Management Sie beim Sparen in Azure unterstützen kann. Weitere Videos finden Sie im [YouTube-Kanal zu Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Ausgaben planen und steuern

Zu den Methoden, mit denen Cost Management Ihnen beim Planen und Kontrollieren Ihrer Kosten hilft, zählen: Kostenanalyse, Budgets, Empfehlungen und Exportieren der Kostenverwaltungsdaten.

Mithilfe der Kostenanalyse ermitteln Sie für Ihre Organisation anfallende Kosten und analysieren diese. Durch die Anzeige aggregierter Kosten nach Organisationen können Sie Kostenstellen und Ausgabentrends nachvollziehen. Mithilfe der akkumulierten Kosten lassen sich zudem unter Berücksichtigung von Budgets Kostenschätzungen für monatliche, vierteljährliche oder jährliche Zeiträume erstellen.

Budgets erleichtern Ihnen das Planen und Realisieren finanzieller Zuständigkeiten in Ihrer Organisation. Mit ihnen kann verhindert werden, dass Kostenschwellenwerte und -limits überschritten werden. Anhand von Budgets können Sie auch andere über ihre Ausgaben informieren, um Kosten proaktiv zu steuern. Gemeinsam können Sie die Ausgabenentwicklung über einen Zeitraum untersuchen.

Empfehlungen zeigen Möglichkeiten der Optimierung und Steigerung der Effizienz auf, indem ungenutzte oder nicht ausreichend genutzte Ressourcen ermittelt werden. Auch kostengünstigere Möglichkeiten der Ressourcennutzung können vorgeschlagen werden. Wenn Sie aufgrund der Empfehlungen Maßnahmen ergreifen, ändern Sie Ihre Ressourcennutzung und können damit Geld zu sparen. Bevor Sie tätig werden, untersuchen Sie zunächst die Empfehlungen zur Kostenoptimierung, um mögliche ineffiziente Nutzungen aufzudecken. Anschließend befolgen Sie die Empfehlung und ändern Ihre Azure-Ressource so, dass eine kostengünstigere Option genutzt wird. Schließlich prüfen Sie die Ergebnisse, um sicherzustellen, dass die vorgenommene Änderung gewinnbringend ist.

Wenn Sie Kostenverwaltungsdaten mithilfe externer Systeme abrufen und prüfen, können Sie die Daten auf einfache Weise aus Azure exportieren. Sie können einen täglichen geplanten Export im CSV-Format einrichten und die Datendateien im Azure-Speicher speichern. Anschließend können Sie aus Ihrem externen System auf die Daten zugreifen.

### <a name="cloudyn-deprecation"></a>Einstellung von Cloudyn

Cloudyn ist ein Azure-Dienst in Verbindung mit Cost Management, der Ende 2020 eingestellt wird. Vorhandene Cloudyn-Funktionen werden nach Möglichkeit direkt in das Azure-Portal integriert. Zurzeit werden keine neuen Kunden integriert, der Support für das Produkt bleibt jedoch erhalten, bis es vollständig eingestellt wurde.
 
### <a name="additional-azure-tools"></a>Zusätzliche Azure-Tools

Azure bietet weitere Tools, die in der Featuregruppe „Azure Cost Management + Abrechnung“ nicht enthalten sind. Sie spielen dennoch eine wichtige Rolle im Kostenverwaltungsprozess. Weitere Informationen zu diesen Tools finden Sie unter den folgenden Links.

- [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/): Mit diesem Tool können Sie Ihre im Voraus anfallenden Cloudkosten schätzen.
- [Azure Migrate](../migrate/migrate-services-overview.md): Bewerten Sie die aktuelle Arbeitsauslastung Ihrer Rechenzentren, und stellen Sie fest, welche Anforderungen eine Azure-Austauschlösung erfüllen muss.
- [Azure Advisor](../advisor/advisor-overview.md): Identifizieren Sie nicht genutzte virtuelle Computer, und erhalten Sie Empfehlungen zum Kauf von reservierten Azure-VM-Instanzen.
- [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/): Nutzen Sie Ihre aktuellen Lizenzen für lokale Windows Server oder SQL Server für VMs in Azure, um Kosten zu sparen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit „Cost Management + Abrechnung“ vertraut gemacht haben, können Sie im nächsten Schritt mit der Nutzung des Diensts beginnen.

- Steigen Sie in Azure Cost Management ein, um [Kosten zu analysieren](./costs/quick-acm-cost-analysis.md).
- Weitere Informationen finden Sie auch in [Azure Cost Management – bewährte Methoden](./costs/cost-mgt-best-practices.md).