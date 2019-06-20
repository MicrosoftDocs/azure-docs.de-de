---
title: Konfigurieren von Kundenleads | Azure Marketplace
description: Konfigurieren von Kundenleads im Cloud-Partnerportal.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: c3e483a33d986eff767b3529e30208319ad90b23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64934998"
---
<a name="get-customer-leads"></a>Generieren von Kundenleads
==================

In diesem Artikel wird erläutert, wie Kundenleads im Cloud-Partnerportal erstellt werden. Sie können diese Leads mit Ihrem CRM-System verbinden und sie in Ihre Verkaufspipeline integrieren.

## <a name="leads"></a>Leads

Leads sind Kunden, die an Ihren Produkten im [Azure Marketplace](https://azuremarketplace.microsoft.com/) oder in [AppSource](https://appsource.microsoft.com) interessiert sind oder diese Produkte von dort bereitstellen.

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Der Kunde erhält eine Testversion Ihres Angebots. Diese auch „Test Drives“ genannten Versionen optimieren Ihre Chancen, Ihrem Unternehmen umgehend und ohne Zugangsbeschränkungen potenzielle Kunden zu vermitteln. Jedes Test Drive generiert einen Lead für einen Kunden, der daran interessiert ist, Ihr Produkt auszuprobieren und weitere Informationen zu erhalten. Weitere Informationen zu Test Drives finden Sie unter [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Marketplace-Beispiele für Testversionen (Test Drives)](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Der Kunde erklärt sich durch Auswählen von „Jetzt anfordern“ mit der Weitergabe seiner Informationen einverstanden. Bei diesem Lead handelt es sich um einen Lead mit **erstem Interesse**, in dem wir Informationen über einen Kunden mitteilen, der Interesse am Erhalt Ihres Produkts bekundet hat. Der Lead befindet sich an oberster Stelle des Verkaufstrichters.

   ![Option „Jetzt anfordern“](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Der Kunde wählt „Kaufen“ im [Azure-Portal](https://portal.azure.com/) aus, um Ihr Produkt zu erhalten. Dieser Lead ist ein **aktiver** Lead, in dem wir Informationen über einen Kunden weitergeben, der damit begonnen hat, Ihr Produkts bereitzustellen.

   ![Option „Kaufen“](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Der Kunde erhält eine Testversion Ihres Angebots. Diese auch „Test Drives“ genannten Versionen optimieren Ihre Chancen, Ihrem Unternehmen umgehend und ohne Zugangsbeschränkungen potenzielle Kunden zu vermitteln. Alle Test Drives generieren einen Lead für Kunden, die daran interessiert sind, Ihr Produkt auszuprobieren und weitere Informationen zu erhalten. Weitere Informationen zu Test Drives finden Sie unter [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Beispiel für Testversion (Test Drive)](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Der Kunde erklärt sich durch Auswählen von „Jetzt anfordern“ mit der Weitergabe seiner Informationen einverstanden. Bei diesem Lead handelt es sich um einen Lead mit **erstem Interesse**, in dem wir Informationen über einen Kunden mitteilen, der Interesse am Erhalt Ihres Produkts bekundet. Der Lead befindet sich an oberster Stelle des Verkaufstrichters.

      ![Option „Jetzt anfordern“](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Der Kunde wählt „Kontakt mit mir aufnehmen“ für Ihr Angebot aus. Dieser Lead ist ein **aktiver** Lead, in dem wir Informationen über einen Kunden weitergeben, der darum bittet, weitere Informationen zu Ihrem Produkt zu erhalten.

    ![Option „Kontakt mit mir aufnehmen“](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Leaddaten
---------

Für jeden Lead, den Sie während des Kundengewinnungsprozesses empfangen, gibt es Daten in bestimmten Feldern. Da Sie Leads aus mehreren Schritten erhalten, stellen Deduplizierung und Personalisierung der Nachverfolgungen die beste Möglichkeit im Umgang mit den Leads dar. Auf diese Weise erhält jeder Kunde eine auf ihn zugeschnittene Mitteilung und erhalten Sie die Möglichkeit, eine zielgerichtete Beziehung aufzubauen.

### <a name="lead-source"></a>Leadursprung

Das Format für einen Leadursprung ist **Ursprungs**-**Aktion** |  **Angebot**.

**Quellen**: „AzureMarketplace“, „AzurePortal“, „TestDrive“ und „AppSource (SPZA)“

**Aktionen**:
- „INS“: Installation. Diese Aktion findet im Azure Marketplace oder in AppSource statt, wenn ein Kunde Ihr Produkt kauft.
- „PLT“: Steht für „Partner Led Trial“ (partnergesteuerte Testversion). Diese Aktion findet in AppSource statt, wenn ein Kunde die Option „Kontakt mit mir aufnehmen“ verwendet.
- „DNC“: Steht für „Do Not Contact“ (keinen Kontakt aufnehmen). Diese Aktion findet in AppSource statt, wenn ein Partner, zu dem es auf Ihrer App-Seite einen Querverweis gab, um eine Kontaktaufnahme gebeten wird. Wir übermitteln die Mitteilung, dass es für diesen Kunden einen Querverweis zu Ihrer App gab, es ist aber nicht erforderlich, Kontakt zu dem Kunden aufzunehmen.
- „Create“: Diese Aktion findet nur im Azure-Portal statt und wird generiert, wenn ein Kunde mit seinem Konto Ihr Angebot erwirbt.
- „StartTestDrive“: Diese Aktion findet nur für Testversionen (Test Drives) statt und wird generiert, wenn ein Kunde seine Testversion startet.

**Angebote**

Die folgenden Beispiele zeigen eindeutige Bezeichner, die einem Herausgeber und einem bestimmten Angebot zugeordnet sind: „checkpoint.check-point-r77-10sg-byol“, „bitnami.openedxcypress“ und „docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a“.


### <a name="customer-info"></a>Kundeninformationen

Die Felder im folgenden Beispiel zeigen die Kundeninformationen, die in einem Lead enthalten sind.
- Vorname: John
- Nachname: Smith
- E-Mail: jsmith\@Microsoft.com
- Telefon: 1234567890
- Land: US
- Unternehmen: Microsoft
- Titel: CTO

>[!Note]
>Nicht alle Daten in diesem Beispiel sind immer für jeden Leads verfügbar.

Wir arbeiten aktiv an der Verbesserung von Leads. Wenn daher ein von Ihnen gewünschtes Datenfeld hier nicht angezeigt wird, [senden Sie uns Ihr Feedback](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Vorgehensweise zum Verbinden Ihres CRM-Systems mit dem Cloudpartnerportal
------------------------------------------------------------

Um den Erhalt von Leads in die Wege zu leiten, haben wir unseren Leadverwaltungsconnector in das Cloud-Partnerportal integriert, sodass Sie mühelos Ihre CRM-Daten einbinden können. Die Verbindung stellen wir für Sie her. Nun können Sie ohne großen Entwicklungsaufwand für die Integration in ein externes System mühelos die vom Marketplace generierten Leads nutzen.

![Leadverwaltungsconnector](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Leads können in eine Reihe von CRM-Systemen oder direkt in eine Azure Storage-Tabelle geschrieben werden, in der Sie die Leads ganz nach Ihrem Belieben verwalten können. Über jeden der folgenden Links erhalten Sie Anweisungen zum Herstellen einer Verbindung mit möglichen Leadzielen:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md), um die Anweisungen zur Konfiguration von Dynamics CRM Online für den Erhalt von Leads abzurufen.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md), um Anweisungen zum Einrichten der Marketo-Leadkonfiguration zum Abrufen von Leads zu erhalten.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md), um Anweisungen zum Einrichten Ihrer Salesforce-Instanz zum Abrufen von Leads zu erhalten.
-    [Azure-Tabelle](./cloud-partner-portal-lead-management-instructions-azure-table.md), um Anweisungen zum Einrichten Ihres Azure Storage-Kontos zum Abrufen von Leads in einer Azure-Tabelle zu erhalten.
-   [HTTPS-Endpunkt](./cloud-partner-portal-lead-management-instructions-https.md), um Anweisungen zum Einrichten Ihres HTTPS-Endpunkts zum Abrufen von Leads zu erhalten.

Nachdem Sie Ihr Leadziel konfiguriert und Ihr Angebot veröffentlicht haben, überprüfen wir die Verbindung und senden wir Ihnen einen Testlead. Wenn Sie das Angebot vor der Liveschaltung anzeigen, können Sie Ihre Leadverbindung ebenfalls testen, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu erwerben. Es muss unbedingt sichergestellt werden, dass Ihre Leadeinstellungen auf dem neuesten Stand sind, damit Sie keine Leads verlieren. Aktualisieren Sie daher immer diese Verbindungen, wenn sich bei Ihnen etwas geändert hat.

<a name="what-next"></a>Nächste Schritte
----------

Nach der technischen Einrichtung sollten Sie diese Leads in Ihre aktuelle Vertriebs- und Marketingstrategie sowie in Ihre Betriebsprozesse integrieren. Wir sind sehr daran interessiert, einen besseren Überblick über Ihren allgemeinen Vertriebsprozess zu erhalten, und möchten bei der Bereitstellung hochwertiger Leads und ausreichender Daten eng mit Ihnen zusammenarbeiten, um zu Ihrem Erfolg beizutragen. Wir freuen uns, wenn Sie uns Ihr Feedback mitteilen würden, wie wir die Leads, die wir Ihnen zusenden, durch zusätzliche Daten optimieren und verbessern können, um diesen Kunden zum Erfolg zu verhelfen. Teilen Sie uns Ihr [Feedback](mailto:AzureMarketOnboard@microsoft.com) und Ihre Vorschläge mit, damit wir Ihrem Vertriebsteam durch Marketplace-Leads zu größerem Erfolg verhelfen können.
