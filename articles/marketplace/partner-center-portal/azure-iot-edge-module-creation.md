---
title: Erstellen eines Azure IoT Edge-Modulangebots im Azure Marketplace mit Partner Center
description: Erfahren Sie, wie Sie mithilfe des Partner Centers ein IoT Edge-Modulangebot im Azure Marketplace erstellen, konfigurieren und veröffentlichen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 62cb8db094cf1bfd7d81b1da06dd7a1b743ec045
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347481"
---
# <a name="create-an-iot-edge-module-offer"></a>Erstellen eines IoT Edge-Modulangebots

In diesem Artikel wird beschrieben, wie Sie ein IoT Edge-Modulangebot (Internet der Dinge) für den Azure Marketplace erstellen und veröffentlichen. Bevor Sie beginnen, [erstellen Sie ein Konto im kommerziellen Marketplace in Partner Center](create-account.md), sofern dies noch nicht geschehen ist. Vergewissern Sie sich, dass Ihr Konto im Programm „Kommerzieller Marketplace“ registriert ist.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
3. Wählen Sie auf der Übersichtsseite die Option **+ Neues Angebot** > **IoT Edge-Modul** aus.

    ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Nach der Veröffentlichung eines Angebots werden die daran im Partner Center vorgenommenen Änderungen erst nach der erneuten Veröffentlichung des Angebots in Onlineshops angezeigt. Achten Sie darauf, nach dem Vornehmen von Änderungen immer erneut zu veröffentlichen.

### <a name="offer-id-and-alias"></a>Angebots-ID und Angebotsalias

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID wird für Kunden unter der Webadresse für das Marketplace-Angebot und ggf. in Azure Resource Manager-Vorlagen angezeigt.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt. Wenn Sie hier z. B. **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird im Marketplace nicht verwendet und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
- Dies kann nach dem Auswählen von **Erstellen** nicht mehr geändert werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

## <a name="offer-overview"></a>Angebotsübersicht

Die Seite **Angebotsübersicht** zeigt eine visuelle Darstellung der zum Veröffentlichen dieses Angebots erforderlichen Schritte (sowohl der abgeschlossenen als auch der noch ausstehenden) und der erwarteten Dauer ihrer Ausführung.

Diese Seite enthält Links zum Durchführen von Vorgängen für dieses Angebot auf der Grundlage der von Ihnen ausgewählten Optionen. Beispiel:

- Wenn das Angebot einen Entwurf darstellt: Delete draft offer (Angebotsentwurf löschen)
- Wenn es sich um ein Liveangebot handelt: [Stop selling the offer](update-existing-offer.md#stop-selling-an-offer-or-plan) (Angebot nicht mehr verkaufen)
- Wenn das Angebot in der Vorschau ist: [Go-live](../review-publish-offer.md#previewing-and-approving-your-offer) (Live schalten)
- Wenn Sie die Abmeldung beim Herausgeber nicht abgeschlossen haben: [Cancel publishing](../review-publish-offer.md#cancel-publishing) (Veröffentlichung abbrechen).

## <a name="offer-setup"></a>Angebotseinrichtung

Führen Sie die unten angegebenen Schritte aus, um Ihr Angebot einzurichten.

### <a name="customer-leads"></a>Kundenleads

Wenn Sie Ihr Angebot mithilfe des Partner Centers im Marketplace veröffentlichen, können Sie es optional mit Ihrem CRM-System (Customer Relationship Management) verbinden. Dadurch empfangen Sie Kontaktinformationen zum Kunden, sobald jemand sein Interesse an Ihrem Produkt bekundet oder es verwendet.

1. Wählen Sie ein Leadziel aus, an das wir Kundenleads senden sollen. Das Partner Center unterstützt die folgenden CRM-Systeme:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Wenn Ihr CRM-System oben nicht aufgeführt ist, verwenden Sie eine [Azure-Tabelle](commercial-marketplace-lead-management-instructions-azure-table.md) oder einen [HTTPS-Endpunkt](commercial-marketplace-lead-management-instructions-https.md), um die Leaddaten von Kunden zu speichern, und exportieren Sie die Daten dann in Ihr CRM-System.

2. Verbinden Sie Ihr Angebot bei der Veröffentlichung im Partner Center mit dem Leadziel.
3. Vergewissern Sie sich, dass die Verbindung mit dem Leadziel ordnungsgemäß konfiguriert ist. Nach der Veröffentlichung Ihres Angebots im Partner Center überprüfen wir die Verbindung und senden Ihnen einen Testlead. Während Sie das Angebot vor der Liveschaltung als Vorschau anzeigen, können Sie Ihre Leadverbindung ebenfalls testen, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu kaufen.
4. Achten Sie darauf, dass die Verbindung mit dem Leadziel immer auf dem neuesten Stand ist, damit keine Leads verloren gehen.

Hier finden Sie einige zusätzliche Ressourcen zur Leadverwaltung:

- [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](commercial-marketplace-get-customer-leads.md)
- [Häufige Fragen zur Leadverwaltung](../lead-management-faq.md#common-questions-about-lead-management)
- [Problembehandlung bei Leadkonfigurationsfehlern](../lead-management-faq.md#publishing-config-errors)
- [Übersicht über die Leadverwaltung](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)-PDF-Datei (vergewissern Sie sich, dass Ihr Popupblocker deaktiviert ist).

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

### <a name="properties"></a>Eigenschaften

Auf dieser Seite können Sie die Kategorien, die zum Gruppieren Ihres Angebots im Marketplace verwendet werden, und die Verträge definieren, die Ihr Angebot unterstützen.

#### <a name="category"></a>Category

Wählen Sie Kategorien und Unterkategorien aus, um Ihr Angebot in den geeigneten Marketplace-Suchbereichen zu platzieren. Vergessen Sie nicht, in der Angebotsbeschreibung anzugeben, inwiefern Ihr Angebot diesen Kategorien entspricht. Wählen Sie Folgendes:

- Mindestens eine und bis zu zwei Kategorien, einschließlich einer primären und einer sekundären Kategorie (optional).
- Bis zu zwei Unterkategorien für jede primäre und/oder sekundäre Kategorie. Wenn keine Unterkategorie auf Ihr Angebot anwendbar ist, wählen Sie **Nicht zutreffend** aus.

Eine vollständige Liste der Kategorien und Unterkategorien finden Sie unter [Bewährte Methoden für Angebotslistung](../gtm-offer-listing-best-practices.md). Im Marketplace werden IoT Edge-Module immer unter der Kategorie  **Internet der Dinge** > **IoT Edge-Modul** angezeigt.

#### <a name="legal"></a>Rechtliche Hinweise

Sie müssen die Nutzungsbedingungen für das Angebot angeben. Sie haben zwei Möglichkeiten:

- Verwenden des Standardvertrags für den kommerziellen Microsoft-Marketplace.
- Angeben eigener Geschäftsbedingungen.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardvertrag für den kommerziellen Microsoft-Marketplace

Wir bieten eine Vorlage für einen Standardvertrag, um Ihnen Transaktionen im kommerziellen Marketplace zu erleichtern. Sie können sich entscheiden, Ihr Angebot unter dem Standardvertrag anzubieten, der von Kunden nur einmal markiert und akzeptiert werden muss. Dies ist eine gute Option, wenn Sie keine benutzerdefinierten Geschäftsbedingungen austüfteln möchten.

Weitere Informationen zum Standardvertrag finden Sie unter [Standardvertrag für den kommerziellen Microsoft-Marketplace](../standard-contract.md). Sie können auch den [Standardvertrag](https://go.microsoft.com/fwlink/?linkid=2041178) als PDF-Datei herunterladen (achten Sie darauf, Ihren Popupblocker zu deaktivieren).

Um den Standardvertrag zu verwenden, aktivieren Sie das Kontrollkästchen **Use the Standard Contract for Microsoft's commercial marketplace** (Standardvertrag für den kommerziellen Marketplace von Microsoft verwenden), und klicken Sie dann auf **Accept** (Akzeptieren).

> [!NOTE]
> Nachdem Sie ein Angebot mit dem Standardvertrag für den kommerziellen Microsoft-Marketplace veröffentlicht haben, können Sie keine eigenen benutzerdefinierten Geschäftsbedingungen mehr verwenden. Entweder bieten Sie Ihre Lösung im Rahmen des Standardvertrags oder Ihrer eigenen Geschäftsbedingungen an.

![Darstellung des Kontrollkästchens zum Verwenden des Standardvertrags für den kommerziellen Marketplace von Microsoft.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Ihre eigenen Geschäftsbedingungen

Um eigene Geschäftsbedingungen anzugeben, geben Sie diese im Feld **Terms and Conditions** (Geschäftsbedingungen) ein. In diesem Feld können Sie Text mit unbegrenzter Zeichenanzahl eingeben. Kunden müssen diesen Bestimmungen zustimmen, bevor sie Ihr Angebot testen können.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit dem nächsten Abschnitt fortfahren, Angebotsliste.

## <a name="offer-listing"></a>Angebotsliste

Hier definieren Sie die Angebotsdetails, die im Marketplace angezeigt werden. Dazu gehörten der Name des Angebots, eine Beschreibung, Bilder usw. Achten Sie unbedingt darauf, bei der Konfiguration dieses Angebots den Richtlinien auf der Richtlinienseite von Microsoft zu folgen.

> [!NOTE]
> Eine Angabe der Angebotsdetails in englischer Sprache ist nicht erforderlich, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt. Es ist auch in Ordnung, einen Nützlichen Link mit Inhalten in einer anderen Sprache als der für die Details der Angebotsliste verwendeten bereitzustellen.

### <a name="name"></a>Name

Der Name, den Sie hier eingeben, wird als Titel Ihres Angebots angezeigt. Dieses Feld ist bereits mit dem Text ausgefüllt, den Sie beim Erstellen des Angebots im Feld **Angebotsalias** eingegeben haben. Sie können diesen Namen später ändern.

Der Name:

- Kann markenrechtlich geschützt sein (und Sie können Marken- oder Copyrightsymbole einschließen).
- Darf nicht mehr als 50 Zeichen lang sein.
- Darf keine Emojis enthalten.

### <a name="search-results-summary"></a>Zusammenfassung der Suchergebnisse

Geben Sie eine kurze Beschreibung Ihres Angebots an. Sie kann bis zu 100 Zeichen lang sein und wird in den Marketplace-Suchergebnissen verwendet.

### <a name="long-summary"></a>Lange Zusammenfassung

Geben Sie eine detailliertere Beschreibung Ihres Angebots. Sie kann bis zu 256 Zeichen lang sein und wird in den Marketplace-Suchergebnissen verwendet.

### <a name="description"></a>BESCHREIBUNG

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge-Modulangebote müssen am Ende der Beschreibung einen Absatz zu den mindestens zu erfüllenden Hardwareanforderungen beinhalten, etwa:

- Mindestanforderungen für Hardware: Betriebssystem Linux x64 und ARM32, 1 GB RAM, 500 MB Speicher

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>URL zur Datenschutzrichtlinie

Geben Sie die Webadresse der Datenschutzrichtlinien Ihrer Organisation ein. Es liegt in Ihrer Verantwortung, sicherzustellen, dass Ihr Angebot den Gesetzen und Regelungen zum Datenschutz entspricht. Es liegt ferner in Ihrer Verantwortung, eine gültige Datenschutzrichtlinie auf Ihrer Website zu veröffentlichen.

#### <a name="useful-links"></a>Nützliche Links

Stellen Sie ergänzende Onlinedokumente zu Ihrem Angebot bereit. Sie können bis zu 25 Links hinzufügen. Um einen Link hinzuzufügen, wählen Sie **+ Link hinzufügen** aus, und füllen Sie dann die folgenden Felder aus:

- **Titel** : Kunden wird der Titel auf der Detailseite Ihres Angebots angezeigt.
- **Link (URL)** : Geben Sie einen Link für Kunden ein, um Ihr Onlinedokument anzuzeigen. Der Link muss mit `http://` oder `https://` beginnen.

Fügen Sie mindestens einen Link zu Ihrer Dokumentation und einen Link zu den kompatiblen IoT Edge-Geräten aus dem  [Azure IoT-Gerätekatalog](https://catalog.azureiotsolutions.com/) hinzu.

### <a name="contact-information"></a>Kontaktinformationen

Sie müssen den Namen, die E-Mail-Adresse und die Telefonnummer für einen **Supportkontakt** und einen **Technischen Ansprechpartner** angeben. Diese Informationen werden Kunden nicht angezeigt. Sie sind für Microsoft verfügbar und dürfen gegenüber CSP-Partnern (Cloud Solution Provider) angegeben werden.

- Supportkontakt (erforderlich): Für allgemeine Supportfragen.
- Technischer Ansprechpartner (erforderlich): Für technische Fragen und Zertifizierungsprobleme.
- CSP-Programm-Kontakt (optional): Für Fragen von Handelspartnern zum CSP-Programm.

Geben Sie im Abschnitt **Supportkontakt** die Webadresse der **Supportwebsite** an, auf der Partner Support zu Ihrem Angebot finden können, je nachdem, ob Ihr Angebot global in Azure, in Azure Government oder in beiden verfügbar ist.

Geben Sie im Abschnitt **CSP-Programm-Kontakt** den Link ( **Marketingmaterial für das CSP-Programm** ) an, in dem CSP-Partner Marketingmaterialien für Ihr Angebot finden können.

#### <a name="additional-marketplace-listing-resources"></a>Zusätzliche Ressourcen für Marketplace-Listen

Weitere Informationen zum Erstellen von Angebotslistungen finden Sie unter [Bewährte Methoden für Angebotslistung](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Marketplace-Bilder

Stellen Sie Logos und Bilder zur Verwendung mit Ihrem Angebot bereit. Alle Bilder müssen das PNG-Format aufweisen. Unscharfe Bilder werden abgelehnt.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Wenn beim Hochladen von Dateien ein Problem auftritt, vergewissern Sie sich, dass das lokale Netzwerk den von Partner Center verwendeten Dienst https://upload.xboxlive.com nicht blockiert.

#### <a name="store-logos"></a>Store-Logos

Geben Sie eine PNG-Datei für das **große** Logo an. Dies wird in Partner Center verwendet, um ein **kleines** und ein **mittleres** Logo zu erstellen. Sie können diese Logos später durch andere Bilder ersetzen.

- **Groß** (von 216 × 216 bis 350 × 350 Pixel, erforderlich)
- **Mittel** (90 × 90 Pixel, optional)
- **Klein** (48 × 48 Pixel, optional)

Diese Logos werden an unterschiedlichen Stellen in der Auflistung verwendet:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Screenshots (optional)

Fügen Sie bis zu fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Jeder muss 1.280 x 720 Pixel groß sein und im PNG-Format vorliegen.

#### <a name="videos-optional"></a>Videos (optional)

Fügen Sie optional bis zu fünf Videos hinzu, die Ihr Angebot vorstellen. Geben Sie den Namen des Videos, seine Webadresse und ein Miniaturbild des Videos im PNG-Format mit einer Größe von 1.280 x 720 Pixel ein.

#### <a name="marketplace--examples"></a>Marketplace-Beispiele

Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen im Azure Marketplace angezeigt werden:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Veranschaulicht, wie dieses Angebot im Azure Marketplace angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Großes Logo
2. Kategorien
3. Supportadresse (Link)
4. Geschäftsbedingungen
5. Adresse der Datenschutzrichtlinie (Link)
6. Name
7. Zusammenfassung
8. BESCHREIBUNG
9. Nützliche Links
10. Screenshots/Videos

<br>Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen in Azure Marketplace-Suchergebnissen angezeigt werden:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Veranschaulicht, wie dieses Angebot in Azure Marketplace-Suchergebnissen angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Kleines Logo
2. Angebotsname
3. Zusammenfassung der Suchergebnisse

<br>Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen im Azure-Portal angezeigt werden:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Veranschaulicht, wie dieses Angebot im Azure-Portal angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Name
2. BESCHREIBUNG
3. Nützliche Links
4. Screenshots

<br>Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen in den Suchergebnissen im Azure-Portal angezeigt werden:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Veranschaulicht, wie dieses Angebot in den Suchergebnissen im Azure-Portal angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Kleines Logo
2. Angebotsname
3. Zusammenfassung der Suchergebnisse

<br>Wählen Sie **Entwurf speichern** aus, bevor Sie mit dem nächsten Abschnitt „Vorschau“ fortfahren.

## <a name="preview"></a>Vorschau

Auf der **Registerkarte Vorschau** können Sie eine eingeschränkte **Vorschauzielgruppe** zur Überprüfung Ihres Angebots auswählen, bevor Sie es live für die breitere Zielgruppe der Marketplacebenutzer veröffentlichen.

> [!IMPORTANT]
> Nachdem Sie Ihr Angebot in der Vorschau angezeigt haben, müssen Sie **Live schalten** auswählen, um Ihr Angebot für die Öffentlichkeit zu veröffentlichen.

Geben Sie Ihre Vorschauzielgruppe mithilfe der GUIDs der Azure-Abonnement-ID an, begleitet von einer optionalen Beschreibung für jede. Keines dieser Felder ist für Kunden sichtbar.

> [!NOTE]
> Sie finden Ihre Azure-Abonnement-ID auf der Seite „Abonnement“ im Azure-Portal.

Fügen Sie mindestens eine Azure-Abonnement-ID hinzu, entweder einzeln (bis zu 10) oder durch Hochladen einer CSV-Datei (bis zu 100). Durch Hinzufügen dieser Abonnement-IDs definieren Sie, wer Ihr Angebot als Vorschau anzeigen kann, bevor es live veröffentlicht wird. Wenn Ihr Angebot bereits live geschaltet ist, können Sie eine Vorschauzielgruppe für das Testen von Änderungen oder Aktualisierungen Ihres Angebots definieren.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit dem nächsten Abschnitt „Planübersicht“ fortfahren.

## <a name="plan-overview"></a>Planübersicht

Auf dieser Registerkarte können Sie verschiedene Planoptionen innerhalb des gleichen Angebots im Partner Center angeben. Pläne (zuvor als SKUs bezeichnet) können sich hinsichtlich der verfügbaren Clouds, wie etwa globaler Clouds oder Government-Clouds, und des Images, auf das der Plan verweist, unterscheiden. Damit Ihr Angebot im Marketplace aufgelistet wird, müssen Sie mindestens einen Plan auswählen.

Sie können bis zu 100 Pläne für jedes Angebot erstellen. Bis zu 45 davon können privat sein. Erfahren Sie unter [Private Angebote im kommerziellen Microsoft-Marketplace](../private-offers.md) mehr über private Pläne.

Nach dem Erstellen Ihres Plans zeigt die Registerkarte **Planübersicht** folgende Informationen an:

- Plannamen
- Preismodell
- Azure-Regionen (global oder Government)
- Aktueller Veröffentlichungsstatus
- Eventuell verfügbare Aktionen

Die in der Planübersicht verfügbaren Aktionen unterscheiden sich je nach dem aktuellen Status Ihres Plans. Dazu gehören:

- **Entwurf löschen** : Wenn der Planstatus ein Entwurf ist.
- **Verkauf des Plans einstellen** : Wenn der Plan den Status „Live veröffentlicht“ hat.

### <a name="create-new-plan"></a>Erstellen eines neuen Plans

Wählen Sie **Neuen Plan erstellen** aus. Das Dialogfeld **Neuer Plan** wird angezeigt.

Erstellen Sie im Feld **Plan-ID** für jeden Plan im Angebot eine eindeutige Plan-ID. Diese ID ist für Kunden in der Webadresse des Produkts sichtbar. Verwenden Sie nur Kleinbuchstaben und Zahlen, Bindestriche oder Unterstriche bei maximal 50 Zeichen.

Geben Sie im Feld **Planname** einen Namen für diesen Plan ein. Kunden sehen diesen Namen, wenn sie sich für einen der Pläne in Ihrem Angebot interessieren. Erstellen Sie für jeden Plan im Angebot einen eindeutigen Namen. Beispielsweise können Sie den Angebotsnamen **Windows Server** für die Pläne **Windows Server 2016** und **Windows Server 2019** verwenden.

> [!NOTE]
> Die Plan-ID kann nicht mehr geändert werden, nachdem Sie **Erstellen** ausgewählt haben.

Klicken Sie auf **Erstellen**.

### <a name="plan-setup"></a>Plansetup

Auf dieser Registerkarte können Sie konfigurieren, in welchen Clouds der Plan verfügbar sein soll. Ihre Antworten auf dieser Registerkarte haben Einfluss darauf, welche Felder auf anderen Registerkarten angezeigt werden.

#### <a name="azure-regions"></a>Azure-Regionen

Alle Pläne für IoT Edge-Modulangebote werden automatisch in **Azure Global** verfügbar gemacht.  Ihr Plan kann von Kunden in allen Azure-Regionen weltweit verwendet werden, die den Marketplace nutzen. Details finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen](../marketplace-geo-availability-currencies.md).

Wählen Sie [Azure Government](../../azure-government/documentation-government-welcome.md) aus, um Ihre Lösung dort zu veröffentlichen. Dies ist eine Government-Community-Cloud mit kontrolliertem Zugriff für Kunden der US-Behörden auf Bundes-, Bundesstaats-, Gemeinde- und Stammesgebietsebene sowie für Partner, die Kunden erbringen. Als Herausgeber sind Sie für alle Kompatibilitätskontrollen, Sicherheitsmaßnahmen und bewährten Methoden für diese Cloudcommunity verantwortlich. Azure Government verwendet physisch isolierte Rechenzentren und Netzwerke (die sich ausschließlich in den USA befinden). Testen und bestätigen Sie Ihre Lösung vor dem [Veröffentlichen](../../azure-government/documentation-government-manage-marketplace-partners.md) innerhalb dieses Gebiets, da sich die Ergebnisse unterscheiden können. Fordern Sie für Staging und Test Ihrer Lösung ein Testkonto bei der [Microsoft Azure Government-Testversion](https://azure.microsoft.com/global-infrastructure/government/request/) an.

> [!NOTE]
> Nachdem Ihr Plan veröffentlicht und in einer bestimmten Region verfügbar gemacht wurde, können Sie diese Region nicht mehr entfernen.

#### <a name="azure-government-certifications"></a>Azure Government-Zertifizierungen

Diese Option ist nur sichtbar, wenn **Azure Government** unter **Azure-Regionen** ausgewählt ist.

Azure Government-Dienste verarbeiten Daten, die bestimmten behördlichen Vorschriften und Anforderungen unterliegen. Beispielsweise FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 und CJIS. Um bei Ihren Zertifizierungen ein Bewusstsein für diese Programme zu schaffen, können Sie bis zu 100 Links bereitstellen, in denen Ihre Zertifizierungen beschrieben werden. Hierbei kann es sich um Links zu Ihren Auflistungen direkt im Programm oder auf Ihrer eigenen Website handeln. Diese Links sind nur für Azure Government-Kunden sichtbar.

### <a name="plan-listing"></a>Planlisting

Auf dieser Registerkarte werden spezifische Informationen für jeden Plan innerhalb desselben Angebots angezeigt.

### <a name="plan-name"></a>Planname

Dieser ist mit dem Namen voraufgefüllt, den Sie Ihrem Plan beim Erstellen gegeben haben. Sie können diesen Namen bei Bedarf ändern. Der Name kann bis zu 50 Zeichen lang sein. Dieser Name wird in Azure Marketplace und im Azure-Portal als der Titel dieses Plans angezeigt. Er wird als Standardmodulname verwendet, nachdem der Plan zur Verwendung bereit ist.

### <a name="plan-summary"></a>Planzusammenfassung

Geben Sie eine kurze Zusammenfassung Ihres Plans (nicht des Angebots) an. Diese Zusammenfassung wird in den Suchergebnissen im Azure Marketplace angezeigt und darf bis zu 100 Zeichen enthalten.

### <a name="plan-description"></a>Planbeschreibung

Beschreiben Sie, was Ihren Plan von anderen unterscheidet und worin die Unterschiede zwischen den Plänen in Ihrem Angebot bestehen. Beschreiben Sie nicht das Angebot, sondern nur den Plan. Diese Beschreibung wird im Azure Marketplace und im Azure-Portal auf der Seite mit den Angebotsauflistungen angezeigt. Hier können Sie die gleichen Inhalte wie in der Planzusammenfassung verwenden. Ihnen stehen 2.000 Zeichen zur Verfügung.

Klicken Sie auf **Entwurf speichern** , nachdem Sie die Angaben in diesen Feldern vorgenommen haben.

#### <a name="plan-examples"></a>Planbeispiele

Hier sehen Sie ein Beispiel für Plandetails im Azure Marketplace (die aufgelisteten Preise dienen lediglich als Beispiele und stellen keine tatsächlichen Kosten dar):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Darstellung von Plandetails im Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Angebotsname
2. Planname
3. Planbeschreibung

<br>Hier sehen Sie ein Beispiel für die Plandetails im Azure-Portal (die aufgelisteten Preise dienen lediglich als Beispiele und stellen keine tatsächlichen Kosten dar):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Darstellung der Plandetails im Azure-Portal.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Angebotsname
2. Planname
3. Planbeschreibung

### <a name="availability"></a>Verfügbarkeit

Wenn Sie Ihr veröffentlichtes Angebot ausblenden möchten, damit Kunden es im Marketplace weder gezielt noch durch Stöbern suchen und es auch nicht erwerben können, aktivieren Sie auf der Registerkarte „Verfügbarkeit“ das Kontrollkästchen **Plan ausblenden**.

Dieses Feld wird häufig in folgenden Fällen verwendet:

- Das Angebot soll nur indirekt verwendet werden, wenn durch eine andere Anwendung darauf verwiesen wird.
- Das Angebot soll nicht einzeln erworben werden.
- Der Plan wurde für die anfänglichen Tests verwendet und ist nicht mehr relevant.
- Der Plan wurde für temporäre oder Saisonangebote verwendet und sollte nicht mehr angeboten werden.

## <a name="technical-configuration"></a>Technische Konfiguration

Der Angebotstyp **IoT Edge-Modul** ist ein spezifischer Typ Container, der auf einem IoT Edge-Gerät ausgeführt wird. Auf der Registerkarte **Technische Konfiguration** geben Sie Referenzinformationen für Ihr Containerimagerepository innerhalb der [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) zusammen mit Konfigurationseinstellungen an, mit denen Kunden das Modul komfortabel verwenden können.

Nach dem Veröffentlichen des Angebots wird Ihr IoT Edge-Containerimage in Azure Marketplace in eine bestimmte öffentliche Containerregistrierung kopiert. Alle Anforderungen von Azure-Benutzern zur Verwendung Ihres Moduls werden von der öffentlichen Containerregistrierung von Azure Marketplace bedient, nicht von Ihrer privaten Containerregistrierung.

Mithilfe von Tags können Sie mehrere Plattformen anzielen und verschiedene Versionen des Containerimages Ihres Moduls bereitstellen. Weitere Informationen zu Tags und Versionsverwaltung finden Sie unter [Vorbereiten der technischen Ressourcen für das IoT Edge-Modul](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Details des Imagerepositorys

Auf der Registerkarte **Image repository details** (Details des Imagerepositorys) geben Sie die folgenden Informationen an.

**Bildquelle auswählen** : Wählen Sie die Option **Azure Container Registry** aus.

**Azure-Abonnement-ID** : Geben Sie die Abonnement-ID an, unter der die Ressourcennutzung gemeldet und Dienste für die Azure Container Registry abgerechnet werden, die Ihr Containerimage enthält. Sie finden diese ID auf der Seite [Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) im Azure-Portal.

**Name der Azure-Ressourcengruppe** : Geben Sie den Namen der [Ressourcengruppe](../../azure-resource-manager/management/manage-resource-groups-portal.md) an, die die Azure Container Registry mit Ihrem Containerimage enthält. Der Zugriff auf die Ressourcengruppe muss über die Abonnement-ID (oben) möglich sein. Sie finden den Namen im Azure-Portal auf der Seite [Ressourcengruppen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups).

**Name der Azure-Containerregistrierung** : Geben Sie den Namen der [Azure Container Registry](../../container-registry/container-registry-intro.md) an, die Ihr Containerimage enthält. Die Containerregistrierung muss in der Azure-Ressourcengruppe vorhanden sein, die Sie zuvor angegeben haben. Geben Sie nur den Registrierungs Namen und nicht den vollständigen Anmelde Servernamen an. Achten Sie unbedingt darauf, **azurecr.io** nicht in den Namen einzuschließen. Sie finden den Registrierungsnamen auf der Seite [Containerregistrierungen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) im Azure-Portal.

**Administratorbenutzername für die Azure Container Registry** : Geben Sie den [Administratorbenutzernamen](../../container-registry/container-registry-authentication.md#admin-account) ein, der der Azure Container Registry zugeordnet ist, die Ihr Containerimage enthält. Benutzername und Kennwort sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Um den Administratorbenutzernamen und das zugeordnete Kennwort abzurufen, legen Sie die Eigenschaft **admin-enabled** (Von Administrator aktiviert) an der Azure-Befehlszeilenschnittstelle auf **True** fest. Optional können Sie im Azure-Portal **Administratorbenutzer** auf **Aktivieren** festlegen.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Darstellung des Dialogfelds „Containerregistrierung aktualisieren“.":::

#### <a name="call-out-description"></a>Beschreibungen zu den Nummern

1. Administratorbenutzer

<br>**Kennwort für die Azure Container Registry** : Geben Sie das Kennwort für den Administratorbenutzernamen ein, der der Azure Container Registry zugeordnet ist, die Ihr Containerimage enthält. Benutzername und Kennwort sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Sie können das Kennwort im Azure-Portal abrufen, indem Sie zu **Containerregistrierung** > **Zugriffsschlüssel** wechseln oder die Azure-Befehlszeilenschnittstelle und den Befehl [show](/cli/azure/acr/credential#az-acr-credential-show) verwenden.

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Darstellung des Zugriffsschlüssel-Bildschirms im Azure-Portal.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Zugriffsschlüssel
2. Username
3. Kennwort

**Repositoryname innerhalb der Azure Container Registry**. Geben Sie den Namen des Azure Container Registry-Repositorys ein, das Ihr Image enthält. Sie geben den Namen des Repositorys an, wenn Sie das Image per Push an die Registrierung übertragen. Sie finden den Namen des Repositorys auf der Seite [Container Registry](https://azure.microsoft.com/services/container-registry/) > **Repositorys**. Weitere Informationen finden Sie unter [Anzeigen von Azure Container Registry-Repositorys im Azure-Portal](../../container-registry/container-registry-repositories.md). Nachdem der Name festgelegt wurde, kann er nicht mehr geändert werden. Verwenden Sie für jedes Angebot in Ihrem Konto einen eindeutigen Namen.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Imagetags für neue Versionen Ihres Angebots

Kunden müssen imstande sein, Updates beim Azure Marketplace automatisch abzurufen, wenn Sie ein Update veröffentlichen. Wenn sie kein Update ausführen möchten, müssen sie die Möglichkeit haben, bei einer bestimmten Version Ihres Images zu bleiben. Sie können dies erreichen, indem Sie für jedes Update des Images neue Imagetags hinzufügen.

**Imagetag**. Dieses Feld muss ein **aktuellstes** Tag enthalten, das auf die letzte Version Ihres Images auf allen unterstützten Plattformen verweist. Es muss außerdem ein Versionstag enthalten (beispielsweise beginnend mit xx.xx.xx, wobei xx eine Zahl ist). Kunden sollten [Manifesttags](https://github.com/estesp/manifest-tool) verwenden, um mehrere Zielplattformen anzugeben. Zudem müssen alle Tags, auf die von einem Manifesttag verwiesen wird, hinzugefügt werden, damit wir sie hochladen können. Alle Manifesttags (mit Ausnahme des aktuellsten Tags) müssen entweder mit X.Y- oder X.Y.Z- beginnen, wobei X, Y und Z ganze Zahlen sind. Wenn beispielsweise ein aktuellstes Tag auf 1.0.1-linux-x64, 1.0.1-linux-arm32 und 1.0.1-windows-arm32 verweist, müssen diesem Feld diese sechs Tags hinzugefügt werden. Weitere Details zu Tags und Versionsverwaltung finden Sie unter [Vorbereiten der technischen Ressourcen für das IoT-Edge-Modul.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Standardeinstellungen für die Bereitstellung (optional)

Definieren Sie die am häufigsten verwendeten Einstellungen zum Bereitstellen des IoT Edge-Moduls. Sie können die Bereitstellungen von Kunden optimieren, indem diese Ihr IoT Edge-Modul mit diesen vorkonfigurierten Standardeinstellungen starten können.

**Standardrouten**. Der IoT Edge-Hub verwaltet die Kommunikation zwischen Modulen, IoT Hub und Geräten. Sie können Routen für die Datenein- und -ausgabe zwischen Modulen und dem IoT Hub festlegen. So können Sie Nachrichten flexibel an das gewünschte Ziel senden, ohne zusätzliche Dienste zum Verarbeiten von Nachrichten zu benötigen oder zusätzlichen Code schreiben zu müssen. Routen werden mit Name-Wert-Paaren erstellt. Sie können Namen für bis zu fünf Standardrouten definieren, von denen jeder bis zu 512 Zeichen lang sein darf.

Achten Sie darauf, die ordnungsgemäße [Routensyntax](../../iot-edge/module-composition.md#declare-routes) in Ihrem Routenwert zu verwenden (normalerweise definiert als „FROM/message/* INTO $upstream“). Dies bedeutet, dass alle Nachrichten, die von beliebigen Modulen gesendet werden, an Ihren IoT Hub geleitet werden. Um auf Ihr Modul zu verweisen, verwenden Sie seinen Standardmodulnamen, der aus Ihrem **Angebotsnamen** ohne Leerzeichen und Sonderzeichen besteht. Um auf weitere Module zu verweisen, die derzeit noch nicht bekannt sind, verwenden Sie die Konvention <VON_MODULNAME>, um Ihren Kunden deutlich zu machen, dass sie diese Informationen aktualisieren müssen. Ausführliche Informationen zu IoT Edge-Routen finden Sie unter [Deklarieren von Routen](../../iot-edge/module-composition.md#declare-routes).

Wenn beispielsweise das Modul ContosoModule an ContosoInput nach Eingaben und an ContosoOutput nach Ausgaben lauscht, ist es sinnvoll, die folgenden beiden Standardrouten zu definieren:

- Name 1: ToContosoModule
- Wert 1: FROM /messages/modules/<VON_MODULNAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Name 2: FromContosoModuleToCloud
- Wert 2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Gewünschte Standardeigenschaften für Modulzwilling**. Ein Modulzwilling ist ein auf dem IoT Hub gespeichertes JSON-Dokument mit den Zustandsinformationen für eine Modulinstanz, einschließlich der gewünschten Eigenschaften. Gewünschte Eigenschaften werden in Verbindung mit gemeldeten Eigenschaften zum Synchronisieren von Modulkonfigurationen oder -zuständen verwendet. Das Lösungs-Back-End kann gewünschte Eigenschaften festlegen, die vom Modul gelesen werden können. Das Modul kann außerdem Änderungsbenachrichtigungen zu den gewünschten Eigenschaften erhalten. Gewünschte Eigenschaften werden aus bis zu fünf Name-Wert-Paaren erstellt, und jeder Standardwert muss weniger als 512 Zeichen umfassen. Sie können bis zu fünf Namen und Werte für gewünschte Eigenschaften von Gerätezwillingen definieren. Die Werte für gewünschte Eigenschaften von Gerätezwillingen müssen gültige JSON-Werte ohne Escapezeichen und ohne Arrays sein. Sie können eine geschachtelte Hierarchie mit maximal vier Ebenen aufweisen. In einem Szenario, in dem ein für einen Standardwert erforderlicher Parameter nicht sinnvoll ist (z. B. die IP-Adresse eines Kundenservers), können Sie einen Parameter als Standardwert hinzufügen. Weitere Informationen zu gewünschten Eigenschaften von Gerätezwillingen finden Sie unter [Definieren oder Aktualisieren gewünschter Eigenschaften](../../iot-edge/module-composition.md#define-or-update-desired-properties).

Wenn ein Modul z. B. mithilfe von gewünschten Eigenschaften von Gerätezwillingen eine dynamisch konfigurierbare Aktualisierungsrate unterstützt, empfiehlt es sich, die folgende gewünschte Standardeigenschaft für Gerätezwillinge zu definieren:

- Name 1: RefreshRate
- Wert 1: 60

**Default environment variables** (Standardumgebungsvariablen). Umgebungsvariablen geben zusätzliche Informationen zu einem Modul an, die den Konfigurationsvorgang vereinfachen. Umgebungsvariablen werden mithilfe von Name-Wert-Paaren erstellt. Jeder Name und Wert einer Standardumgebungsvariablen muss weniger als 512 Zeichen aufweisen. Sie können bis zu fünf davon definieren. Wenn ein für einen Standardwert erforderlicher Parameter nicht sinnvoll ist (z. B. die IP-Adresse eines Kundenservers), können Sie einen Parameter als Standardwert hinzufügen.

Wenn vor dem Start eines Moduls beispielsweise die Zustimmung zu den Nutzungsbedingungen erforderlich ist, können Sie die folgende Standardumgebungsvariable definieren:

- Name 1: ACCEPT_EULA
- Wert 1: J

**Standardoptionen für die Containererstellung**. Optionen für die Containererstellung steuern die Erstellung des Docker-Containers für das IoT Edge-Modul. IOT Edge unterstützt Optionen zur Containererstellung der Docker-Engine-API. Alle Optionen finden Sie unter [Container auflisten](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList). Das Feld mit den Erstellungsoptionen muss gültigen JSON-Code ohne Escapezeichen und mit weniger als 512 Zeichen enthalten.

Wenn für ein Modul beispielsweise die Bindung an einen Port erforderlich ist, definieren Sie die folgenden Erstellungsoptionen:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Überprüfen und Veröffentlichen

Nachdem Sie alle erforderlichen Abschnitte des Angebots abgeschlossen haben, können Sie es zum Überprüfen und Veröffentlichen einreichen.

Wählen Sie in der oberen rechten Ecke des Portals **Überprüfen und veröffentlichen** aus.

Auf der Überprüfungsseite wird der Veröffentlichungsstatus angezeigt:

- Anzeigen des Abschlussstatus für die einzelnen Abschnitte des Angebots. Sie können erst veröffentlichen, wenn alle Abschnitte des Angebots als abgeschlossen gekennzeichnet sind.
    - **Nicht gestartet** : Die Überprüfung des Abschnitts wurde nicht gestartet und muss abgeschlossen werden.
    - **Unvollständig** : Der Abschnitt enthält Fehler, die behoben werden müssen, oder erfordert eine Ergänzung der Informationen. Anweisungen dazu finden Sie in den Abschnitten weiter oben in diesem Dokument.
    - **Abgeschlossen** : Der Abschnitt enthält alle erforderlichen Daten, und es sind keine Fehler aufgetreten. Sie können das Angebot erst einreichen, wenn alle Abschnitte des Angebots abgeschlossen sind.
- Stellen Sie dem Zertifizierungsteam Testanweisungen zur Verfügung, um sicherzustellen, dass Ihr Angebot ordnungsgemäß getestet wird. Stellen Sie außerdem alle ergänzenden Anmerkungen bereit, die das Verständnis Ihres Angebots unterstützen.

Um das Angebot zur Veröffentlichung einzureichen, wählen Sie **Veröffentlichen** aus.

Wir senden Ihnen eine E-Mail, um Ihnen mitzuteilen, wann eine Vorschauversion des Angebots zur Überprüfung und Genehmigung verfügbar ist. Kehren Sie zu Partner Center zurück, und wählen Sie **Live schalten** aus, um das Angebot für eine öffentliche Zielgruppe zu veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](update-existing-offer.md)