---
title: Optimieren der Cloudinvestitionen mit Azure Cost Management | Microsoft-Dokumentation
description: Dieser Artikel bietet Informationen dazu, wie Sie Ihre Cloudinvestitionen optimal ausschöpfen, Ihre Kosten senken und auswerten, wofür genau Ihr Geld ausgegeben wird.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 7c562e6f0a1358d16b9abef08a5e582e4ff84472
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002044"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Optimieren der Cloudinvestitionen mit Azure Cost Management

Azure Cost Management bietet Tools, mit denen Sie Ihre Ausgaben planen, analysieren und reduzieren können, um Ihre Cloudinvestitionen zu maximieren. Dieser Artikel stellt einen methodischen Ansatz für das Kostenmanagement vor und beschreibt die Tools, die Ihnen zur Verfügung stehen, um die Kostenherausforderungen Ihrer Organisation zu bewältigen. Mit Azure lassen sich Cloudlösungen einfach erstellen und bereitstellen. Es ist jedoch wichtig, dass diese Lösungen optimiert werden, um die Kosten zu minimieren. Die in diesem Artikel erläuterten Konzepte und der Einsatz unserer Tools unterstützen Ihre Organisation auf dem Weg zum Erfolg.

## <a name="methodology"></a>Methodik

Das Kostenmanagement ist ein Problem, das die ganze Organisation betrifft. Es sollte ein fortlaufendes Verfahren sein und bereits beginnen, noch bevor Sie Mittel für Cloudressourcen ausgeben. Um ein erfolgreiches Kostenmanagement zu implementieren und die Kosten zu optimieren, muss Ihre Organisation folgende Maßnahmen ergreifen:

- Einsatz der richtigen Tools für eine erfolgreiche Umsetzung
- Übernahme der Kostenverantwortung
- Optimierung der Ausgaben mit den richtigen Methoden

In Ihrer Organisation müssen die drei unten genannten Schlüsselgruppen aufeinander abgestimmt sein, um ein erfolgreiches Kostenmanagement sicherzustellen.

- **Finanzabteilung**: Die Mitarbeiter dieser Abteilung sind für die Genehmigung von Budgetanfragen in der gesamten Organisation zuständig. Die Genehmigung basiert auf den prognostizierten Cloudausgaben. Sie zahlen die entsprechende Rechnung und weisen die Kosten verschiedenen Teams zu, um die Verantwortlichkeit zu unterstreichen.
- **Manager**: Die geschäftlichen Entscheidungsträger in einer Organisation müssen die Anforderungen an Cloudausgaben genau kennen, um sie optimal zu planen.
- **App-Teams**: Die Techniker verwalten die Cloudressourcen jeden Tag und entwickeln Dienste, um die Anforderungen der Organisation zu erfüllen. Diese Teams benötigen Flexibilität, um innerhalb ihrer definierten Budgets optimale Nutzen zu erbringen.

### <a name="key-principles"></a>Wichtige Prinzipien

Nutzen Sie die unten genannten Prinzipien, um für ein erfolgreiches Cloudkostenmanagement Ihrer Organisation zu sorgen.

#### <a name="planning"></a>Planung

Eine umfassende Vorausplanung ermöglicht es Ihnen, die Cloudnutzung exakt an Ihre speziellen Geschäftsanforderungen anzupassen. Stellen Sie sich folgende Fragen:

- Welches Geschäftsproblem muss ich lösen?
- Welche Nutzungsmuster erwarte ich von meinen Ressourcen?

Ihre Antworten helfen Ihnen bei der Wahl der geeigneten Angebote. Sie umreißen die zu nutzende Infrastruktur und bestimmen, wie diese genutzt werden soll, um die Effizienz Ihrer Azure-Lösung zu maximieren.

#### <a name="visibility"></a>Sichtbarkeit

Bei guter Strukturierung kann Cost Management Sie dabei unterstützen, die Benutzer über die Azure-Kosten zu informieren, für die sie verantwortlich sind, bzw. für die finanziellen Mittel, die sie ausgeben. Azure bietet Dienste, die Ihnen einen Überblick darüber verschaffen, *wo* Ihr Geld hinfließt. Nutzen Sie diese Tools. Sie können Ihnen dabei helfen, nicht ausgelastete Ressourcen zu ermitteln, überflüssige Ressourcen zu entfernen und kostensparende Möglichkeiten optimal zu nutzen.

#### <a name="accountability"></a>Verantwortlichkeit

Weisen Sie Kosten in Ihrer Organisation zu, sodass den Verantwortlichen die Ausgaben ihres Teams wirklich bewusst sind. Um die Ausgaben Ihrer Organisation für Azure vollständig zu verstehen, sollten Sie Ihre Ressourcen so organisieren, dass Sie maximalen Einblick in die Kostenzuweisung erhalten. Eine gute Organisation hilft dabei, Kosten zu verwalten und zu senken und den Mitarbeitern für effiziente Ausgaben in Ihrer Organisation Kostenverantwortung zu übertragen.

#### <a name="optimization"></a>Optimierung

Ergreifen Sie notwendige Maßnahmen, um Ihre Ausgaben zu senken. Optimieren Sie Ihre Ausgaben basierend auf den Erkenntnissen, die Sie durch Planung und verbesserte Kostentransparenz gewinnen. Ziehen Sie Optimierungen Ihrer Einkaufs- und Lizenzierungsverfahren ebenso in Betracht wie Änderungen an der Infrastrukturbereitstellung, die weiter unten in diesem Artikel genauer erläutert werden.

#### <a name="iteration"></a>Iteration

Alle Personen in Ihrer Organisation müssen in den Lebenszyklus des Kostenmanagements eingebunden werden. Sie müssen auf dem Laufenden bleiben, um die Kosten zu optimieren. Seien Sie strikt, was diesen iterativen Prozess angeht, und machen Sie ihn zu einem Hauptanliegen verantwortlicher Cloudgovernance in Ihrer Organisation.

![Diagramm mit wichtigen Prinzipien zu Sichtbarkeit, Verantwortlichkeit und Optimierung](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Kostenbewusste Planung

Bevor Sie Cloudressourcen bereitstellen, bewerten Sie die folgenden Punkte:

- Das Azure-Angebot, das Ihre Anforderungen am besten erfüllt
- Die Ressourcen, deren Verwendung Sie planen
- Die möglichen Kosten für diese Ressourcen

Azure bietet Tools, die Sie in diesem Bewertungsprozess unterstützen. Mithilfe dieser Tools erhalten Sie einen recht genauen Einblick darin, welche Investitionen erforderlich sind, um Ihre Workloads zu ermöglichen. Danach können Sie die beste Konfiguration für Ihre Situation auswählen.

### <a name="azure-onboarding-options"></a>Onboardingoptionen von Azure

Der erste Schritt bei der optimalen Nutzung von Cost Management besteht darin, die Azure-Angebote genau zu untersuchen und zu entscheiden, welches sich am besten für Sie eignet. Überlegen Sie, wie Sie Azure in Zukunft nutzen möchten. Denken Sie auch darüber nach, wie Ihr Abrechnungsmodell konfiguriert werden soll. Stellen Sie sich vor der Entscheidung die folgenden Fragen:

- Wie lange werde ich Azure verwenden? Teste ich die Plattform nur, oder plane ich den Aufbau einer längerfristigen Infrastruktur?
- Wie möchte ich für Azure zahlen? Soll ich mich für eine günstigere Vorauszahlung oder für eine Rechnung am Ende des Monats entscheiden?

Weitere Informationen zu den verschiedenen Optionen finden Sie unter [Azure erwerben](https://azure.microsoft.com/pricing/purchase-options/). Im Folgenden werden einige der gängigsten Abrechnungsmodelle erläutert.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Free](https://azure.microsoft.com/free/)

- 12 Monate kostenlose Nutzung beliebter Dienste
- Guthaben über 200 USD zum Erkunden von Diensten – 30 Tage lang
- Über 25 Dienste jederzeit kostenlos

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p)

- Keine Mindestgebühren oder Verpflichtungen
- Wettbewerbsfähige Preise
- Zahlen nur für wirklich genutzte Ressourcen
- Jederzeit kündbar

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Optionen für vorab vereinbarte Mindestabnahmen
- Zugang zu reduzierten Azure-Preisen

## <a name="estimate-the-cost-of-your-solution"></a>Schätzen der Kosten Ihrer Lösung

Bevor Sie eine Infrastruktur bereitstellen, schätzen und prognostizieren Sie die Kosten Ihrer Lösung. Diese Prognose hilft Ihnen dabei, Ihrer Organisation vorab ein Budget für die Workload vorzustellen. Im Lauf der Zeit können Sie ein Budget nutzen, um die Gültigkeit Ihrer anfänglichen Schätzung zu bewerten. Und Sie können das Budget mit den tatsächlichen Kosten Ihrer bereitgestellten Lösung vergleichen.

### <a name="azure-pricing-calculator"></a>Azure-Preisrechner

Mit dem Azure-Preisrechner können Sie verschiedene Azure-Dienste miteinander kombinieren und die Kosten dieser Kombinationen schätzen. Sie können Ihre Lösung auf verschiedene Arten in Azure implementieren – jede dieser Möglichkeiten wirkt sich auf die Gesamtausgaben aus. Wenn Sie alle Infrastrukturanforderungen Ihrer Cloudbereitstellung bereits frühzeitig in die Planung einbeziehen, können Sie das Tool auf höchst effektive Weise nutzen. So erhalten Sie eine solide Schätzung Ihrer voraussichtlichen Ausgaben in Azure.

Weitere Informationen finden Sie beim [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate ist ein Dienst, der die aktuellen Workloads Ihrer Organisation in Ihren lokalen Rechenzentren bewertet. So erhalten Sie einen Einblick darin, welche Elemente und Strukturen Sie in einer entsprechenden Azure-Lösung benötigen werden. Migrate analysiert zunächst Ihre lokalen Computer, um zu ermitteln, ob eine Migration überhaupt möglich ist. Danach empfiehlt der Dienst geeignete VM-Größen, um die Leistung zu maximieren. Abschließend erstellt der Dienst eine Kostenschätzung für eine Azure-basierte Lösung.

Weitere Informationen finden Sie unter [Azure Migrate](../migrate/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analysieren und Verwalten Ihrer Kosten

Bleiben Sie auf dem Laufenden über die langfristige Kostenentwicklung in Ihrer Organisation. Wenden Sie die folgenden Techniken an, um Ihre Ausgaben genau zu kennen und zu verwalten.

### <a name="organize-and-tag-your-resources"></a>Organisieren und Kennzeichnen von Ressourcen

Organisieren Sie Ihre Ressourcen mit Blick auf die Kosten. Wenn Sie Abonnements und Ressourcengruppen erstellen, bedenken Sie, welche Teams für die damit verbundenen Kosten verantwortlich sein sollen. Stellen Sie sicher, dass bei der Berichterstellung die Organisation im Mittelpunkt steht. Abonnements und Ressourcengruppen sind geeignete Elemente, um die Ausgaben über die gesamte Organisation hinweg zu organisieren und zuzuordnen. Tags bieten eine gute Möglichkeit für die Kostenzuordnung. Sie können Tags als Filter verwenden. Sie können Sie auch zum Gruppieren verwenden, wenn Sie Daten analysieren und Kosten untersuchen. Enterprise Agreement-Kunden können auch Abteilungen erstellen und Abonnements in diesen platzieren. Eine kostenbasierte Organisation in Azure hilft dabei, den relevanten Personen in Ihrer Organisation die Verantwortung für die Senkung der Ausgaben ihres Teams zu übertragen.

### <a name="use-cost-analysis"></a>Verwenden der Kostenanalyse

Mit einer Kostenanalyse können Sie die Kosten Ihrer Organisation sehr genau analysieren, indem Sie die Kosten anhand von standardmäßigen Ressourceneigenschaften detailliert aufschlüsseln. Betrachten Sie die folgenden häufig gestellten Fragen als Leitfaden für Ihre Analyse. Wenn Sie diese Fragen in regelmäßigen Abständen beantworten, bleiben Sie immer auf dem Laufenden und ermöglichen kostenbewusstere Entscheidungen.

- **Geschätzte Kosten für den laufenden Monat**: Wie viel habe ich diesen Monat schon ausgegeben? Bleibe ich innerhalb meines Budgets?
- **Untersuchen von Anomalien**: Führen Sie routinemäßige Überprüfungen durch, um sicherzustellen, dass die Kosten innerhalb eines vernünftigen Variationsbereichs der normalen Nutzung bleiben. Welche Trends gibt es? Sind Ausreißer vorhanden?
- **Rechnungsabgleich**: Ist meine Rechnung höher als im Vormonat? Wie haben sich die Ausgabegewohnheiten im Lauf der Monate verändert?
- **Interne verbrauchsbasierte Kostenzuteilung**: Ich weiß jetzt, wie hoch die Rechnung insgesamt ist. Wie werden die Gebühren auf die einzelnen Teams und Abteilungen meiner Organisation aufgeteilt?

Weitere Informationen finden Sie unter [Kostenanalyse](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Exportieren der Abrechnungsdaten nach einem Zeitplan

Müssen Sie Ihre Abrechnungsdaten in ein externes System wie z.B. ein Dashboard oder ein Finanzsystem importieren? Richten Sie automatisierter Exporte in Azure Storage ein, und vermeiden Sie das monatliche manuelle Herunterladen von Dateien. Sie können dann einfach automatische Integrationen in andere Systeme einrichten, um Ihre Abrechnungsdaten synchron zu halten.

Weitere Informationen zum Exportieren von Abrechnungsdaten finden Sie unter [Erstellen und Verwalten von exportierten Daten](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Erstellen von Budgets

Nachdem Sie Ihre Ausgabenmuster identifiziert und analysiert haben, ist es wichtig, Grenzwerte für sich selbst und Ihre Teams festzulegen. Mit Azure-Budgets können Sie entweder ein kosten- oder ein nutzungsbasiertes Budget mit vielen Schwellenwerten und Warnungen festlegen. Stellen Sie sicher, dass Sie die erstellten Budgets regelmäßig überprüfen, um die Ausgabenentwicklung im Blick zu behalten und bei Bedarf Änderungen vorzunehmen. Mit Azure-Budgets können Sie auch einen Trigger für automatisierte Aktionen konfigurieren, der ausgelöst wird, wenn ein bestimmter Budgetschwellenwert erreicht wird. Sie können beispielsweise Ihren Dienst so konfigurieren, dass VMs heruntergefahren werden. Sie können Ihre Infrastruktur als Reaktion auf einen Budgettrigger auch in einen anderen Tarif verlagern.

Weitere Informationen finden Sie unter [Azure-Budgets](tutorial-acm-create-budgets.md).

Weitere Informationen zu budgetbasierter Automatisierung finden Sie unter [Verwalten von Kosten mit Azure-Budgets](../billing/billing-cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Optimieren
Nutzen Sie die folgenden Möglichkeiten, um Ausgaben zu optimieren.

### <a name="cut-out-waste"></a>Vermeiden von Überschuss

Nachdem Sie Ihre Infrastruktur in Azure bereitgestellt haben, müssen Sie sicherstellen, dass sie tatsächlich genutzt wird. Die einfachste Möglichkeit zum sofortigen Erzielen von Einsparungen besteht darin, Ihre Ressourcen zu überprüfen und alle nicht genutzten Ressourcen zu entfernen. Darüber hinaus sollten Sie ermitteln, ob Ihre Ressourcen so effizient wie möglich genutzt werden.

#### <a name="azure-advisor"></a>Azure Advisor

Der Azure Advisor-Dienst ermittelt unter anderem virtuelle Computer, die hinsichtlich der CPU- oder Netzwerkauslastung nicht ausreichend genutzt werden. Basierend auf den geschätzten Kosten für die weitere Ausführung der Computer können Sie entscheiden, ob Sie die Computer herunterfahren oder ihre Größe ändern. Advisor bietet auch Empfehlungen für den Erwerb von reservierten Instanzen. Die Empfehlungen basieren auf der Nutzung der virtuellen Computer in den letzten 30 Tagen. Wenn solche Empfehlungen umgesetzt werden, können sie zum Senken Ihrer Ausgaben beitragen.

Weitere Informationen finden Sie unter[Azure Advisor](../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Auswählen der richtigen Größe Ihrer virtuellen Computer

Die Größe von virtuellen Computern wirkt sich erheblich auf die Gesamtkosten von Azure aus. Die Anzahl von virtuellen Computern, die in Azure benötigt werden, entspricht möglicherweise nicht der Anzahl von Computern, die zurzeit in Ihrem lokalen Rechenzentrum bereitgestellt sind. Stellen Sie sicher, dass Sie die richtigen Größen für die Workloads auswählen, die Sie ausführen möchten.

Weitere Informationen finden Sie unter [Azure IaaS: Proper Sizing and Cost](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/) (Azure-IaaS: Auswahl der richtigen Größe und entsprechende Kosten).

### <a name="use-purchase-discounts"></a>Nutzen von Rabatten beim Erwerb

Azure bietet viele verschiedene Rabatte, mit denen Ihre Organisation Geld sparen kann.

#### <a name="azure-reservations"></a>Azure-Reservierungen

Mit Azure-Reservierungen können Sie Computekapazitäten für Ihre virtuellen Computer oder SQL-Datenbank-Instanzen für ein Jahr oder drei Jahre im Voraus erwerben. Bei der Vorauszahlung können Sie einen Rabatt für die Ressourcen in Anspruch nehmen, die Sie nutzen. Azure-Reservierungen können die Computekosten für Ihre virtuellen Computer oder SQL-Datenbank-Instanzen mit einer Vorauszahlung für ein Jahr oder drei Jahre erheblich reduzieren (um bis zu 72 Prozent im Vergleich zu den Preisen bei nutzungsbasierter Bezahlung). Reservierungen bieten einen Abrechnungsrabatt und wirken sich nicht auf den Laufzeitstatus Ihrer virtuellen Computer oder SQL-Datenbank-Instanzen aus.

Weitere Informationen finden Sie unter [Was sind Azure-Reservierungen?](../billing/billing-save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Verwenden des Azure-Hybridvorteils

Wenn in Ihren lokalen Bereitstellungen Windows Server- oder SQL Server-Lizenzen vorhanden sind, können Sie den Azure-Hybridvorteil nutzen, um in Azure bares Geld zu sparen. Beim Vorteil für Windows Server deckt jede Lizenz die Kosten des Betriebssystems (auf bis zu zwei virtuellen Computern) ab, und Sie zahlen nur die grundlegenden Computekosten. Sie können vorhandene SQL Server-Lizenzen verwenden, um bei auf virtuellen Kernen basierenden SQL-Datenbank-Optionen bis zu 55 Prozent zu sparen. Zu den Optionen gehören SQL Server in Azure Virtual Machines und SQL Server Integration Services.

Weitere Informationen finden Sie unter [Einsparungsrechner für den Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Weitere Ressourcen

Azure bietet auch einen Dienst, mit dem Sie Dienste erstellen können, die überschüssige Kapazitäten in Azure zu reduzierten Preisen nutzen. Weitere Informationen finden Sie unter [Verwenden von VMs mit niedriger Priorität mit Batch](../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Nächste Schritte
- Wenn Sie mit Cost Management noch nicht vertraut sind, erfahren Sie unter [Was ist Azure Cost Management?](overview-cost-mgt.md), wie Sie mit diesem Dienst Ihre Azure-Ausgaben überwachen und steuern und die Ressourcennutzung optimieren können.
