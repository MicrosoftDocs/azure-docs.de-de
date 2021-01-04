---
title: Leadverwaltung im kommerziellen Microsoft-Marketplace
description: Erfahren Sie, wie Sie Kundenleads zu Ihren Angeboten bei Microsoft AppSource und im Azure Marketplace generieren und empfangen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489334"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Kundenleads aus Ihrem Angebot im kommerziellen Marketplace

Leads sind Kunden, die an Ihren Angeboten bei [Microsoft AppSource](https://appsource.microsoft.com) oder im [Azure Marketplace](https://azuremarketplace.microsoft.com) interessiert sind oder diese bereitstellen. Leads werden generiert, indem Sie ein Angebot im kommerziellen Marketplace veröffentlichen. In diesem Artikel werden die folgenden Konzepte der Leadverwaltung erläutert:

* Wie Ihr Angebot im kommerziellen Marketplace Kundenleads generiert, damit Sie keine Geschäftschancen mehr verpassen 
* Wie Sie Ihr System für das Kundenbeziehungsmanagement (Customer Relationship Management, CRM) mit Ihrem Angebot verknüpfen, damit Sie Ihre Leads an einem zentralen Ort verwalten können
* Welche Daten Sie erhalten, um Leads nachverfolgen zu können, die Kontakt zu Ihnen aufgenommen haben

## <a name="generate-customer-leads"></a>Generieren von Kundenleads

Stellen, an denen Leads generiert werden:

- Ein Kunde klickt im Marketplace auf **Kontakt mit mir aufnehmen** und erklärt sich so mit der Weitergabe seiner Daten einverstanden. Dieser Lead wird als Lead mit *initialem Interesse* bezeichnet. Sie erhalten Informationen zu Kunden, die Interesse am Erwerb Ihres Produkts bekundet haben. Der Lead befindet sich an oberster Stelle des Verkaufstrichters.

    ![Dynamics 365 – Kontakt mit mir aufnehmen](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Ein Kunde klickt auf **Jetzt holen** (oder auf **Erstellen** im [Azure-Portal](https://portal.azure.com/)), um Ihr Angebot zu beziehen. Dieser Lead ist ein *aktiver* Lead. Sie erhalten Informationen zu Kunden, die begonnen haben, Ihr Produkt bereitzustellen.

    ![Schaltfläche „Jetzt holen“ für SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Schaltfläche „Erstellen“ für Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Ein Kunde klickt auf **Testlaufwerk** oder **Kostenlose Testversion** , um Ihr Angebot auszuprobieren. (Kostenlose) Testversionen sind der optimale Weg, um Ihre Produkte umgehend und ohne Zugangsbeschränkungen für potenzielle Kunden zugänglich zu machen.

    ![Schaltfläche „Testlaufwerk“ für Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Schaltfläche „Kostenlose Testversion“ für Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Verbinden mit Ihrem CRM-System

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Grundlegendes zu Leaddaten

Für jeden Lead, den Sie während des Kundengewinnungsprozesses empfangen, gibt es Daten in bestimmten Feldern. Das erste Feld, auf das Sie achten sollten, ist das Feld `LeadSource` in folgendem Format: **Quelle-Aktion** | **Angebot**.

**Quellen** : Der Wert für dieses Feld wird basierend auf dem Marketplace gefüllt, über den der Lead generiert wurde. Mögliche Werte sind `"AzureMarketplace"`, `"AzurePortal"` und `"AppSource (SPZA)"`.

**Aktionen** : Der Wert in diesem Feld spiegelt die Aktion wider, die der Kunden in dem Marketplace ausgeführt hat, über den der Lead generiert wurde.

Mögliche Werte:

- **"INS":** Steht für *Installation*. Diese Aktion erfolgt im Azure Marketplace oder in AppSource, wenn ein Kunde Ihr Produkt erwirbt.
- **"PLT":** Steht für *Partner Led Trial* (Über Partner angebotene Testversion). Diese Aktion findet in AppSource statt, wenn ein Kunde auf **Kontakt mit mir aufnehmen** klickt.
- **"DNC":** Steht für *Do Not Contact* (Nicht kontaktieren). Diese Aktion findet in AppSource statt, wenn ein Partner, zu dem es auf Ihrer App-Seite einen Querverweis gab, um eine Kontaktaufnahme gebeten wird. Sie werden benachrichtigt, dass auf Ihrer App-Seite auf den Kunden verwiesen wurde, dieser jedoch nicht kontaktiert werden muss.
- **"Create":** Diese Aktion findet nur im Azure-Portal statt, wenn ein Kunde über sein Konto Ihr Angebot erwirbt.
- **"StartTestDrive":** Diese Aktion gilt nur für die Option **Testlaufwerke** und wird generiert, wenn ein Kunde seine Testversion startet.

**Angebote** : Sie können mehrere Angebote im kommerziellen Marketplace veröffentlichen. Der Wert für dieses Feld wird basierend auf dem Angebot ausgefüllt, über das der Lead generiert wurde. Die Herausgeber-ID und die Angebots-ID werden in dieses Feld eingefügt. Dabei handelt es sich jeweils um die Werte, die Sie angegeben haben, als Sie das Angebot im Marketplace veröffentlicht haben.

Die folgenden Beispiele zeigen Werte im erwarteten Format `publisherid.offerid`: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Kundeninformationen

Die Informationen des Kunden werden über mehrere Felder gesendet. Im folgenden Beispiel werden die Kundeninformationen gezeigt, die in einem Lead enthalten sind:

- Vorname: John
- Nachname: Smith
- E-Mail: jsmith\@Microsoft.com
- Telefon: 1234567890
- Land: US
- Unternehmen: Microsoft
- Titel: CTO

>[!NOTE]
>Nicht alle Daten in diesem Beispiel sind immer für jeden Leads verfügbar. Da Leads, wie im Abschnitt zu Kundenleads erwähnt, an mehreren Stellen generiert werden können, sollten Sie unbedingt die Datensätze deduplizieren und die Nachverfolgungen personalisieren. Auf diese Weise erhält jeder Kunde eine auf ihn zugeschnittene Mitteilung, und Sie bauen eine individuelle Beziehung auf.

## <a name="best-practices-for-lead-management"></a>Bewährte Methoden bei der Leadverwaltung

Im Anschluss finden Sie einige Empfehlungen für die Leadgenerierung in Ihrem Vertriebszyklus:

- **Vorgehensweise:** Definieren Sie einen klaren Vertriebsprozess mit Meilensteinen, KPIs und klarer Teamverantwortung.
- **Qualifizierung:** Definieren Sie die Voraussetzungen, die angeben, ob ein Lead vollständig qualifiziert wurde. Stellen Sie sicher, dass Vertriebs- oder Marketingmitarbeiter die Leads sorgfältig qualifizieren, bevor diese den vollständigen Vertriebsprozess durchlaufen.
- **Nachverfolgung:** Vergessen Sie die Nachverfolgung innerhalb von 24 Stunden nicht. Der Lead wird sofort nach der Bereitstellung der Testversion in das CRM Ihrer Wahl aufgenommen. Daraufhin sollten Sie ihm eine E-Mail senden, solange seine Eindrücke noch frisch sind. Fordern Sie die Planung eines Telefonats an, um besser zu verstehen, ob Ihr Produkt eine gute Lösung für sein Problem ist. Eine typische Transaktion erfordert eine Reihe von Folgeanrufen.
- **Pflege:** Pflegen Sie Ihre Leads, um langfristig eine höhere Gewinnspanne zu erzielen. Seien Sie engagiert, aber überrennen Sie sie nicht. Sie sollten den Leads noch einige E-Mails senden, bevor Sie aufgeben. Bleiben Sie ruhig eine Weile dran. Denken Sie daran: Diese Interessenten haben direkt mit Ihrem Produkt interagiert und Zeit mit einer kostenlosen Testversion verbracht; sie sind potenzielle Kunden erster Wahl.

Nach der technischen Einrichtung sollten Sie diese Leads in Ihre aktuelle Vertriebs- und Marketingstrategie sowie in Ihre Betriebsprozesse integrieren. Wir möchten gern mehr über Ihren allgemeinen Vertriebsprozess erfahren und enger mit Ihnen zusammenarbeiten, um Ihnen hochwertige Leads und ausreichend Daten zu bieten, um Ihren Erfolg zu sichern. Wir freuen uns, wenn Sie uns Ihr Feedback mitteilen würden, wie wir die Leads, die wir Ihnen zusenden, durch zusätzliche Daten optimieren und verbessern können, um diesen Kunden zum Erfolg zu verhelfen. Wir freuen uns auf [Ihr Feedback und Ihre Vorschläge](mailto:AzureMarketOnboard@microsoft.com). Nur so können wir Ihrem Vertriebsteam mithilfe von Marketplace-Leads zu größerem Erfolg verhelfen.

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen und Problembehandlung bei der Leadverwaltung](../lead-management-faq.md)