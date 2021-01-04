---
title: Erstellen eines SaaS-Angebots im kommerziellen Microsoft-Marketplace
description: Erfahren Sie, wie Sie ein neues Software-as-a-Service (SaaS)-Angebot zum Auflisten oder Verkaufen in Microsoft AppSource, im Azure Marketplace oder durch das CSP (Cloud Solution Provider)-Programm im Portal des kommerziellen Marketplace im Microsoft Partner Center erstellen.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: f689993ce56a1125a1d1de8f65ce05d01f776ea9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130067"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Erstellen eines SaaS-Angebots im kommerziellen Marketplace

Als Herausgeber im kommerziellen Marketplace können Sie ein Software-as-a-Service (SaaS)-Angebot erstellen, damit potenzielle Kunden Ihre SaaS-basierte technische Lösung erwerben können. In diesem Artikel wird erläutert, wie Sie ein SaaS-Angebot für den kommerziellen Microsoft-Marketplace erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Lesen Sie [Planen eines SaaS-Angebots für den kommerziellen Marketplace](plan-saas-offer.md), falls Sie dies noch nicht getan haben. Dort werden die technischen Anforderungen für Ihre SaaS-App erläutert. Außerdem finden Sie Informationen und Ressourcen, die Sie beim Erstellen Ihres Angebots benötigen. Sofern Sie nicht nur eine einfache Auflistung (Auflistungsoption **Kontakt mit mir aufnehmen** ) im kommerziellen Marketplace veröffentlichen möchten, muss Ihre SaaS-Anwendung technische Anforderungen an die Authentifizierung erfüllen.

## <a name="create-a-new-saas-offer"></a>Erstellen eines neuen SaaS-Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
1. Wählen Sie auf der Registerkarte **Übersicht** die Option **+ Neues Angebot** > **SaaS (Software-as-a-Service)** aus.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Screenshot des linken Navigationsmenüs mit der Liste „Neues Angebot“":::

1. Geben Sie im Dialogfeld **Neues Angebot** eine **Angebots-ID** ein. Diese ID ist in der URL der Auflistung im kommerziellen Marketplace und ggf. in den Azure Resource Manager-Vorlagen sichtbar. Wenn Sie in diesem Feld z. B. **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
   + Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
   + Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
   + Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

   + Dieser Name ist nicht im kommerziellen Marketplace sichtbar und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
   + Nachdem Sie **Erstellen** ausgewählt haben, kann der Angebotsalias nicht mehr geändert werden.
1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

## <a name="configure-your-saas-offer-setup-details"></a>Details zur Einrichtung Ihres SaaS-Angebots

Auf der Registerkarte **Angebotseinrichtung** unter **Einrichtungsdetails** wählen Sie aus, ob Sie Ihr Angebot über Microsoft vertreiben oder Ihre Transaktionen unabhängig verwalten möchten. Über Microsoft vertriebene Angebote werden als _transaktionsfähige Angebote_ bezeichnet. Dabei wird der Geldfluss für eine Softwarelizenz im Namen des Herausgebers von Microsoft abgewickelt. Weitere Informationen zu diesen Optionen finden Sie unter [Auflisten von Optionen](plan-saas-offer.md#listing-options) und [Bestimmung Ihrer Veröffentlichungsoption](determine-your-listing-type.md).

1. Wählen Sie **Ja** aus, wenn der Verkauf über Microsoft erfolgen soll und die Transaktionen über uns abgewickelt werden sollen. Fahren Sie mit [Aktivieren einer Testversion](#enable-a-test-drive-optional) fort.

1. Um Ihr Angebot über den kommerziellen Marketplace aufzulisten und Transaktionen unabhängig abzuwickeln, wählen Sie **Nein** aus, und führen Sie dann eine der folgenden Aktionen aus:
   + Um ein kostenloses Abonnement für Ihr Angebot bereitzustellen, wählen Sie **Jetzt abrufen (kostenlos)** aus. Geben Sie dann im eingeblendeten Feld **Angebots-URL** die (mit *http* oder *https* beginnende) URL ein, unter der Kunden per [1-Klick-Authentifizierung über Azure Active Directory (Azure AD)](azure-ad-saas.md) eine Testversion erhalten können. Beispiel: `https://contoso.com/saas-app`.
   + Um eine kostenlose 30-Tage-Testversion bereitzustellen, wählen Sie **Kostenlose Testversion** aus und geben dann im eingeblendeten Feld **Test-URL** die (mit *http* oder *https* beginnende) URL ein, unter der Kunden per [1-Klick-Authentifizierung über Azure Active Directory (Azure AD)](azure-ad-saas.md) auf Ihre kostenlose Testversion zugreifen können. Beispiel: `https://contoso.com/trial/saas-app`.
   + Wählen Sie **Kontakt mit mir aufnehmen** aus, wenn potenzielle Kunden Kontakt zu Ihnen aufnehmen und Ihr Angebot erwerben sollen.

### <a name="enable-a-test-drive-optional"></a>Aktivieren einer Testversion (optional)

Eine Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen für eine festgelegte Anzahl von Stunden Zugang zu einer vorkonfigurierten Umgebung gewähren. Wenn Sie eine Testversion anbieten, können Sie die Konvertierungsrate erhöhen und hochqualifizierte Leads generieren. Weitere Informationen zu Testversionen finden Sie unter [Was ist eine Testversion?](./what-is-test-drive.md)

> [!TIP]
> Eine Testversion unterscheidet sich von einer kostenlosen Testversion. Sie können entweder eine Testversion, eine kostenlose Testversion oder beides anbieten. Mit beiden können Kunden Ihre Lösung für einen festgelegten Zeitraum nutzen. Eine Testversion umfasst jedoch auch eine praktische, selbstgesteuerte Tour durch die wichtigsten Features und Vorteile Ihres Produkts, die in einem praxisnahen Implementierungsszenario veranschaulicht werden.

**So aktivieren Sie eine Testversion**
1.  Aktivieren Sie unter **Testversion** das Kontrollkästchen **Aktivieren einer Testversion**.
1.  Wählen Sie in der eingeblendeten Liste den Typ der Testversion aus.

### <a name="configure-lead-management"></a>Konfigurieren der Leadverwaltung

Verbinden Sie Ihr Customer Relationship Management (CRM)-System mit Ihrem Angebot im kommerziellen Marketplace, damit Sie Kundenkontaktinformationen erhalten, wenn ein Kunde Interesse bekundet oder Ihr Produkt einsetzt. Sie können diese Verbindung während oder nach der Erstellung des Angebots jederzeit ändern.

> [!NOTE]
> Sie müssen die Leadverwaltung konfigurieren, wenn Sie Ihr Angebot über Microsoft verkaufen oder die Auflistungsoption **Kontakt mit mir aufnehmen** ausgewählt haben. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

#### <a name="to-configure-the-connection-details-in-partner-center"></a>So konfigurieren Sie die Verbindungsdetails im Partner Center

1.  Wählen Sie unter **Kundenleads** den Link **Verbinden** aus.
1. Wählen Sie im Dialogfeld **Verbindungsdetails** eine Leadzielgruppe aus der Liste aus.
1. Füllen Sie alle angezeigten Felder aus. Ausführliche Schritte finden Sie in den folgenden Artikeln:

   - [Konfigurieren des Angebots zum Senden von Leads an die Azure-Tabelle](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurieren Ihres Angebots zum Senden von Leads an Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (früher Dynamics CRM Online)
   - [Konfigurieren Ihres Angebots zum Senden von Leads an den HTTPS-Endpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurieren Ihres Angebots zum Senden von Leads zu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurieren Ihres Angebots zum Senden von Leads zu Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Um die von Ihnen angegebene Konfiguration zu überprüfen, wählen Sie den Link **Überprüfen** aus.
1. Wählen Sie **OK** aus, um das Dialogfeld zu schließen.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von SaaS-Angebotseigenschaften](create-new-saas-offer-properties.md)