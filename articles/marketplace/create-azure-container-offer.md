---
title: Erstellen eines Azure-Containerangebots – Azure Marketplace
description: Hier wird erläutert, wie Sie ein Containerangebot für Azure Marketplace erstellen und dort veröffentlichen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: bcb334074d842fa5c35e4619f1c5d654405388a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459569"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Erstellen eines Azure-Containerangebots in Azure Marketplace

In diesem Artikel wird beschrieben, wie Sie ein Containerangebot für Azure Marketplace erstellen und dort veröffentlichen. Bevor Sie beginnen, [erstellen Sie ein Konto im kommerziellen Marketplace in Partner Center](./partner-center-portal/create-account.md), sofern Sie dies noch nicht getan haben. Vergewissern Sie sich, dass Ihr Konto im Programm „Kommerzieller Marketplace“ registriert ist.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

2. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.

3. Wählen Sie auf der Übersichtsseite die Option **+ Neues Angebot** > **Azure-Container** aus.

   ![Darstellung des Navigationsmenüs auf der linken Seite.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
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

Auf dieser Seite werden basierend auf dem aktuellen Status des Angebots unterschiedliche Links angezeigt. Beispiel:

- Wenn das Angebot einen Entwurf darstellt: Delete draft offer (Angebotsentwurf löschen)
- Wenn es sich um ein Liveangebot handelt: [Stop selling the offer](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) (Angebot nicht mehr verkaufen)
- Wenn das Angebot in der Vorschau ist: [Go-live](review-publish-offer.md#previewing-and-approving-your-offer) (Live schalten)
- Wenn Sie die Abmeldung beim Herausgeber nicht abgeschlossen haben: [Cancel publishing](review-publish-offer.md#cancel-publishing) (Veröffentlichung abbrechen).

## <a name="offer-setup"></a>Angebotseinrichtung

Führen Sie die unten angegebenen Schritte aus, um Ihr Angebot einzurichten.

### <a name="customer-leads--optional"></a>Kundenleads (optional)

Wenn Sie Ihr Angebot mithilfe des Partner Centers im kommerziellen Marketplace veröffentlichen, können Sie es mit Ihrem CRM-System (Customer Relationship Management) verbinden. Dadurch empfangen Sie Kontaktinformationen zum Kunden, sobald jemand sein Interesse an Ihrem Produkt bekundet oder es verwendet.

1. **Wählen Sie ein Leadziel aus, an das wir Kundenleads senden sollen**. Das Partner Center unterstützt die folgenden CRM-Systeme:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Wenn Ihr CRM-System oben nicht aufgeführt ist, verwenden Sie eine [Azure-Tabelle](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) oder einen [HTTPS-Endpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md), um die Leaddaten von Kunden zu speichern, und exportieren Sie die Daten dann in Ihr CRM-System.

2. Verbinden Sie Ihr Angebot bei der Veröffentlichung im Partner Center mit dem Leadziel.
3. Vergewissern Sie sich, dass die Verbindung mit dem Leadziel korrekt konfiguriert ist. Nach der Veröffentlichung Ihres Angebots im Partner Center überprüfen wir die Verbindung und senden Ihnen einen Testlead. Während Sie das Angebot vor der Liveschaltung als Vorschau anzeigen, können Sie Ihre Leadverbindung ebenfalls testen, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu kaufen.
4. Achten Sie darauf, dass die Verbindung mit dem Leadziel immer auf dem neuesten Stand ist, damit keine Leads verloren gehen.

Hier finden Sie einige zusätzliche Ressourcen zur Leadverwaltung:

- [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Häufige Fragen zur Leadverwaltung](lead-management-faq.md#common-questions-about-lead-management)
- [Problembehandlung bei Leadkonfigurationsfehlern](lead-management-faq.md#publishing-config-errors)
- [Übersicht über die Leadverwaltung](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)-PDF-Datei (vergewissern Sie sich, dass Ihr Popupblocker deaktiviert ist).

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

### <a name="properties"></a>Eigenschaften

Auf dieser Seite können Sie die Kategorien, die zum Gruppieren Ihres Angebots im Marketplace verwendet werden, und die Verträge definieren, die Ihr Angebot unterstützen.

#### <a name="category"></a>Category

Wählen Sie Kategorien und Unterkategorien aus, um Ihr Angebot in den geeigneten Marketplace-Suchbereichen zu platzieren. Vergessen Sie nicht, in der Angebotsbeschreibung anzugeben, inwiefern Ihr Angebot diesen Kategorien entspricht. Wählen Sie Folgendes:

- Mindestens eine und bis zu zwei Kategorien, einschließlich einer primären und einer sekundären Kategorie (optional).
- Bis zu zwei Unterkategorien für jede primäre und/oder sekundäre Kategorie. Wenn keine Unterkategorie auf Ihr Angebot anwendbar ist, wählen Sie **Nicht zutreffend** aus.

Eine vollständige Liste der Kategorien und Unterkategorien finden Sie unter [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md). Container werden immer unter **Container** und dann in der Kategorie **Containerimages** angezeigt.

#### <a name="legal"></a>Rechtliche Hinweise

Sie müssen die Nutzungsbedingungen für das Angebot angeben. Es gibt zwei Optionen:

- Verwenden des Standardvertrags für den kommerziellen Microsoft-Marketplace.
- Angeben eigener Geschäftsbedingungen.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardvertrag für den kommerziellen Microsoft-Marketplace

Wir bieten eine Vorlage für einen Standardvertrag, um Ihnen Transaktionen im kommerziellen Marketplace zu erleichtern. Sie können sich entscheiden, Ihr Angebot unter dem Standardvertrag anzubieten, der von Kunden nur einmal markiert und akzeptiert werden muss. Dies ist eine gute Option, wenn Sie keine benutzerdefinierten Geschäftsbedingungen erstellen möchten.

Weitere Informationen zum Standardvertrag finden Sie unter [Standardvertrag für den kommerziellen Microsoft-Marketplace](standard-contract.md). Sie können auch den [Standardvertrag](https://go.microsoft.com/fwlink/?linkid=2041178) als PDF-Datei herunterladen (achten Sie darauf, Ihren Popupblocker zu deaktivieren).

Um den Standardvertrag zu verwenden, aktivieren Sie die Option „**Use the Standard Contract for Microsoft's commercial marketplace](../standard-contract.md)“ (Standardvertrag für den kommerziellen Marketplace von Microsoft verwenden).

> [!NOTE]
> Nachdem Sie ein Angebot mit dem Standardvertrag für den kommerziellen Microsoft-Marketplace veröffentlicht haben, können Sie keine eigenen benutzerdefinierten Geschäftsbedingungen mehr verwenden. Entweder bieten Sie Ihre Lösung im Rahmen des Standardvertrags oder Ihrer eigenen Geschäftsbedingungen an.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Kontrollkästchen zur Verwendung des Standardvertrags für den kommerziellen Marketplace von Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Ihre eigenen Geschäftsbedingungen

Wenn Sie eigene Geschäftsbedingungen angeben möchten, geben Sie diese im Feld **Geschäftsbedingungen** ein. In diesem Feld können Sie Text mit unbegrenzter Zeichenanzahl eingeben. Kunden müssen diesen Bestimmungen zustimmen, bevor sie Ihr Angebot testen können.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit dem nächsten Abschnitt fortfahren, Angebotsliste.

## <a name="offer-listing"></a>Angebotsliste

Auf dieser Seite können Sie die Angebotsdetails definieren, die im kommerziellen Marketplace angezeigt werden. Dazu gehören der Name des Angebots, eine Beschreibung und Bilder.

> [!NOTE]
> Eine Angabe der Angebotsdetails in englischer Sprache ist nicht erforderlich, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt. Es ist auch in Ordnung, einen Nützlichen Link mit Inhalten in einer anderen Sprache als der für die Details der Angebotsliste verwendeten bereitzustellen.

### <a name="name"></a>Name

Der Name, den Sie hier eingeben, wird als Titel Ihres Angebots angezeigt. Dieses Feld ist bereits mit dem Text ausgefüllt, den Sie beim Erstellen des Angebots im Feld **Angebotsalias** eingegeben haben. Sie können diesen Namen später ändern.

Der Name:

- Kann markenrechtlich geschützt sein (und Sie können Marken- und Copyrightsymbole einschließen).
- Darf nicht mehr als 50 Zeichen lang sein.
- Darf keine Emojis enthalten.

### <a name="search-results-summary"></a>Zusammenfassung der Suchergebnisse

Eine kurze Beschreibung Ihres Angebots. Sie kann bis zu 100 Zeichen lang sein und wird in den Marketplace-Suchergebnissen verwendet.

### <a name="long-summary"></a>Lange Zusammenfassung

Eine detailliertere Beschreibung Ihres Angebots. Sie kann bis zu 256 Zeichen lang sein und wird in den Marketplace-Suchergebnissen verwendet.

### <a name="description"></a>BESCHREIBUNG

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Link zur Datenschutzrichtlinie

Geben Sie die Webadresse der Datenschutzrichtlinien Ihrer Organisation ein. Es liegt in Ihrer Verantwortung, sicherzustellen, dass Ihr Angebot den Gesetzen und Regelungen zum Datenschutz entspricht. Es liegt ferner in Ihrer Verantwortung, eine gültige Datenschutzrichtlinie auf Ihrer Website zu veröffentlichen.

#### <a name="useful-links"></a>Nützliche Links

Stellen Sie ergänzende Onlinedokumente zu Ihrem Angebot bereit. Sie können bis zu 25 Links hinzufügen. Um einen Link hinzuzufügen, wählen Sie **+ Link hinzufügen** aus, und füllen Sie dann die folgenden Felder aus:

- **Titel**: Kunden sehen diesen Titel auf der Detailseite Ihres Angebots.
- **Link (URL)** : Geben Sie einen Link ein, über den Kunden Ihr Onlinedokument anzeigen können. Der Link muss mit http:// oder https:// beginnen.

### <a name="contact-information"></a>Kontaktinformationen

Sie müssen den Namen, die E-Mail-Adresse und die Telefonnummer für einen **Supportkontakt** und einen **technischen Ansprechpartner** angeben. Diese Informationen werden nicht für Kunden angezeigt, sind aber für Microsoft verfügbar. Sie können auch für CSP-Partner (Cloud Solution Provider) bereitgestellt werden.

- Supportkontakt (erforderlich): Für allgemeine Supportfragen.
- Technischer Ansprechpartner (erforderlich): Für technische Fragen und Zertifizierungsprobleme.
- CSP-Programm-Kontakt (optional): Für Fragen von Handelspartnern zum CSP-Programm.

Geben Sie im Abschnitt **Supportkontakt** die **Supportwebsite** an, auf der Partner Support zu Ihrem Angebot finden können, je nachdem, ob Ihr Angebot global in Azure und/oder in Azure Government verfügbar ist.

Geben Sie im Abschnitt **CSP-Programm-Kontakt** den Link (**Marketingmaterial für das CSP-Programm**) an, in dem CSP-Partner Marketingmaterialien für Ihr Angebot finden können.

#### <a name="additional-marketplace-listing-resources"></a>Zusätzliche Ressourcen für Marketplace-Listen

Weitere Informationen zum Erstellen von Angebotslistungen finden Sie unter [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Marketplace-Bilder

Stellen Sie Logos und Bilder zur Verwendung mit Ihrem Angebot bereit. Alle Bilder müssen das PNG-Format aufweisen. Unscharfe Bilder werden abgelehnt.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Wenn beim Hochladen von Dateien ein Problem auftritt, vergewissern Sie sich, dass das lokale Netzwerk den von Partner Center verwendeten Dienst https://upload.xboxlive.com nicht blockiert.

#### <a name="store-logos"></a>Store-Logos

Geben Sie eine PNG-Datei für das **große** Logo an. Dies wird in Partner Center verwendet, um ein **kleines** und ein **mittleres** Logo zu erstellen. Sie können diese Logos später optional durch andere Bilder ersetzen.

- **Groß** (von 216 x 216 bis 350 x 350 Pixel, erforderlich)
- **Mittel** (90 x 90 Pixel, optional)
- **Klein** (48 x 48 Pixel, optional)

Diese Logos werden an unterschiedlichen Stellen in der Auflistung verwendet:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Screenshots (optional)

Fügen Sie bis zu fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Jeder muss 1.280 x 720 Pixel groß sein und im PNG-Format vorliegen.

#### <a name="videos-optional"></a>Videos (optional)

Fügen Sie optional bis zu fünf Videos hinzu, die Ihr Angebot vorstellen. Geben Sie den Namen des Videos, seine Webadresse und ein Miniaturbild des Videos im PNG-Format mit einer Größe von 1.280 x 720 Pixel ein.

#### <a name="offer-examples"></a>Angebotsbeispiele

In den folgenden Beispielen wird gezeigt, wie die Felder der Angebotsauflistung an verschiedenen Stellen des Angebots dargestellt werden.

Der folgende Screenshot zeigt die Seite **Angebotsliste** in Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Darstellung der Seite „Angebotsliste“ in Azure Marketplace." :::

Hier sehen Sie die Suchergebnisse in Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Darstellung der Suchergebnisse in Azure Marketplace.":::

Der folgende Screenshot zeigt die Seite **Angebotsliste** im Azure-Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Darstellung der Seite „Angebotsliste“ im Azure-Portal.":::

Hier sehen Sie die Suchergebnisse im Azure-Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Suchergebnisse im Azure-Portal.":::

## <a name="preview"></a>Vorschau

Auf der Registerkarte „Vorschau“ können Sie eine eingeschränkte **Vorschauzielgruppe** zur Prüfung Ihres Angebots auswählen, bevor Sie es live veröffentlichen.

> [!IMPORTANT]
> Nachdem Sie Ihr Angebot in der **Vorschau** angezeigt haben, müssen Sie **Live schalten** auswählen, um Ihr Angebot für die Öffentlichkeit zu veröffentlichen.

Geben Sie Ihre Vorschauzielgruppe mithilfe der GUIDs der Azure-Abonnement-ID an, begleitet von einer optionalen Beschreibung für jede. Keines dieser Felder ist für Kunden sichtbar.

> [!NOTE]
> Ihre Azure-Abonnement-ID finden Sie auf der Seite „Abonnements“ im Azure-Portal.

Fügen Sie mindestens eine Azure-Abonnement-ID hinzu, entweder einzeln (bis zu 10) oder durch Hochladen einer CSV-Datei (bis zu 100). Durch das Hinzufügen dieser Abonnement-IDs legen Sie fest, wer Ihr Angebot als Vorschau anzeigen kann, bevor es live veröffentlicht wird. Wenn Ihr Angebot bereits live geschaltet ist, können Sie eine Vorschauzielgruppe für das Testen von Änderungen oder Aktualisierungen Ihres Angebots auswählen.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="plan-overview"></a>Planübersicht

Auf dieser Registerkarte können Sie verschiedene Planoptionen innerhalb des gleichen Angebots angeben. Pläne (zuvor als SKUs bezeichnet) können sich hinsichtlich der verfügbaren Clouds, wie etwa globaler Clouds oder Government-Clouds, und des Images, auf das der Plan verweist, unterscheiden. Damit Ihr Angebot im kommerziellen Marketplace aufgelistet wird, müssen Sie mindestens einen Plan auswählen.

Sie können bis zu 100 Pläne für jedes Angebot erstellen. Bis zu 45 davon können privat sein. Erfahren Sie unter [Private Angebote im kommerziellen Microsoft-Marketplace](private-offers.md) mehr über private Pläne.

Nach dem Erstellen Ihres Plans zeigt die Registerkarte **Planübersicht** folgende Informationen an:

- Plannamen
- Preismodell
- Azure-Regionen (global oder Government)
- Aktueller Veröffentlichungsstatus
- Eventuell verfügbare Aktionen

Die in der Planübersicht verfügbaren Aktionen unterscheiden sich je nach dem aktuellen Status Ihres Plans. Dazu gehören:

- **Entwurf löschen**: Wenn es sich beim Plan um einen Entwurf handelt.
- **Stop sell plan** (Verkauf des Plans einstellen): Wenn der Plan live veröffentlicht wurde.

### <a name="create-new-plan"></a>Erstellen eines neuen Plans

Wählen Sie **Neuen Plan erstellen** aus. Das Dialogfeld **Neuer Plan** wird angezeigt.

Erstellen Sie im Feld **Plan-ID** für jeden Plan im Angebot einen eindeutigen Planbezeichner. Diese ID ist für Kunden unter der Webadresse des Produkts sichtbar. Verwenden Sie nur Kleinbuchstaben und Zahlen, Bindestriche oder Unterstriche bei maximal 50 Zeichen.

> [!NOTE]
> Die Plan-ID kann nicht mehr geändert werden, nachdem Sie **Erstellen** ausgewählt haben.

Geben Sie im Feld **Planname** einen Namen für diesen Plan ein. Kunden sehen diesen Namen, wenn sie sich für einen der Pläne in Ihrem Angebot interessieren. Erstellen Sie für jeden Plan im Angebot einen eindeutigen Namen. Beispielsweise können Sie den Angebotsnamen **Windows Server** für die Pläne **Windows Server 2016** und **Windows Server 2019** verwenden.

### <a name="plan-setup"></a>Plansetup

Auf dieser Registerkarte können Sie auswählen, in welchen Clouds der Plan verfügbar sein soll. Ihre Antworten auf dieser Registerkarte haben Einfluss darauf, welche Felder auf anderen Registerkarten angezeigt werden.

#### <a name="azure-regions"></a>Azure-Regionen

Alle Pläne für Angebote für Azure-Container werden automatisch in **Azure Global** verfügbar gemacht.  Ihr Plan kann von Kunden in allen Azure-Regionen weltweit verwendet werden, die den kommerziellen Marketplace nutzen. Details finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen](marketplace-geo-availability-currencies.md).

Wählen Sie [Azure Government](../azure-government/documentation-government-welcome.md) aus, um Ihre Lösung dort zu veröffentlichen. Dies ist eine Government-Community-Cloud mit kontrolliertem Zugriff für Kunden der US-Behörden auf Bundes-, Bundesstaats-, Gemeinde- und Stammesgebietsebene sowie für Partner, die Kunden erbringen. Als Herausgeber sind Sie für alle Kompatibilitätskontrollen, Sicherheitsmaßnahmen und bewährten Methoden für diese Cloudcommunity verantwortlich. Azure Government verwendet physisch isolierte Rechenzentren und Netzwerke (die sich ausschließlich in den USA befinden). Testen und bestätigen Sie Ihre Lösung vor dem [Veröffentlichen](../azure-government/documentation-government-manage-marketplace-partners.md) innerhalb dieses Gebiets, da sich die Ergebnisse unterscheiden können. Beantragen Sie zum Erstellen und Testen Ihrer Lösung ein Testkonto für die [Microsoft Azure Government-Testversion](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Nachdem Ihr Plan veröffentlicht und in einer bestimmten Region verfügbar gemacht wurde, können Sie diese Region nicht mehr entfernen.

#### <a name="azure-government-certifications"></a>Azure Government-Zertifizierungen

Diese Option ist nur sichtbar, wenn unter **Azure-Region** die Option **Azure Government** ausgewählt ist.

Azure Government-Dienste verarbeiten Daten, die bestimmten behördlichen Vorschriften und Anforderungen unterliegen. Beispielsweise FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 und CJIS.

Um Ihre Zertifizierungen für diese Programme anzuzeigen, können Sie bis zu 100 Links bereitstellen, unter denen diese Zertifizierungen beschrieben werden. Hierbei kann es sich um Links zu Ihren Auflistungen direkt im Programm oder auf Ihrer eigenen Website handeln. Diese Links sind nur für Azure Government-Kunden sichtbar.

### <a name="plan-listing"></a>Planlisting

Auf dieser Registerkarte werden spezifische Informationen für jeden Plan innerhalb des aktuellen Angebots angezeigt.

### <a name="plan-name"></a>Planname

Dieser ist mit dem Namen voraufgefüllt, den Sie Ihrem Plan beim Erstellen gegeben haben. Sie können diesen Namen bei Bedarf ändern. Der Name kann bis zu 50 Zeichen lang sein. Dieser Name wird in Azure Marketplace und im Azure-Portal als Titel dieses Plans angezeigt. Er wird als Standardmodulname verwendet, nachdem der Plan zur Verwendung bereit ist.

### <a name="plan-summary"></a>Planzusammenfassung

Eine kurze Zusammenfassung Ihres Softwareplans (nicht des Angebots). Diese Zusammenfassung wird in den Suchergebnissen im Azure Marketplace angezeigt und darf bis zu 100 Zeichen enthalten.

### <a name="plan-description"></a>Planbeschreibung

Beschreiben Sie, was Ihren Softwareplan von anderen unterscheidet und worin die Unterschiede zwischen den Plänen in Ihrem Angebot bestehen. Beschreiben Sie nicht das Angebot, sondern nur den Plan. Diese Beschreibung wird im Azure Marketplace und im Azure-Portal auf der Seite **Angebotsliste** angezeigt. Hier können Sie die gleichen Inhalte wie in der Planzusammenfassung verwenden. Ihnen stehen 2.000 Zeichen zur Verfügung.

Klicken Sie auf **Speichern**, nachdem Sie die Angaben in diesen Feldern vorgenommen haben.

#### <a name="plan-examples"></a>Planbeispiele

In den folgenden Beispielen wird gezeigt, wie die Felder der Planauflistung in verschiedenen Ansichten dargestellt werden.

Dies sind die Felder, die in Azure Marketplace für Plandetails angezeigt werden:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Darstellung der sichtbaren Felder in den Plandetails im Azure Marketplace.":::

Dies sind Plandetails im Azure-Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Darstellung der Plandetails im Azure-Portal.":::

### <a name="plan-availability"></a>Verfügbarkeit des Plans

Wenn Sie Ihr veröffentlichtes Angebot ausblenden möchten, damit Kunden es im Marketplace weder gezielt noch durch Stöbern finden und es auch nicht erwerben können, aktivieren Sie auf der Registerkarte **Verfügbarkeit** das Kontrollkästchen **Plan ausblenden**.

Dieses Kontrollkästchen wird in den folgenden Fällen verwendet:

- Das Angebot soll indirekt verwendet werden, wenn durch eine andere Anwendung darauf verwiesen wird.
- Das Angebot soll nicht einzeln erworben werden.
- Der Plan wurde für die anfänglichen Tests verwendet und ist nicht mehr relevant.
- Der Plan wurde für temporäre oder Saisonangebote verwendet und sollte nicht mehr angeboten werden.

## <a name="technical-configuration"></a>Technische Konfiguration

Containerimages müssen in einer privaten [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)-Instanz gehostet werden. Auf der Registerkarte **Technische Konfiguration** geben Sie Referenzinformationen für Ihr Containerimagerepository innerhalb der Azure Container Registry-Instanz an.

Nach dem Veröffentlichen des Angebots wird Ihr Containerimage in Azure Marketplace in eine bestimmte öffentliche Containerregistrierung kopiert. Alle Anforderungen zur Verwendung Ihrer Containerimages werden mit der öffentlichen Azure Marketplace-Containerregistrierung verarbeitet, nicht Ihrer private Containerregistrierung. Weitere Informationen finden Sie unter [Vorbereiten Ihrer technischen Azure-Containerressourcen](create-azure-container-technical-assets.md).

### <a name="image-repository-details"></a>Details des Imagerepositorys

Geben Sie auf der Registerkarte **Image repository details** (Details des Imagerepositorys) die folgenden Informationen an.

**Azure-Abonnement-ID**: Geben Sie die Abonnement-ID an, unter der der Verbrauch gemeldet und Dienste für die Azure Container Registry-Instanz mit Ihrem Containerimage abgerechnet werden. Sie finden diese ID auf der Seite [Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) im Azure-Portal.

**Azure-Ressourcengruppenname**: Geben Sie den Namen der [Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-portal.md) an, die die Azure Container Registry-Instanz mit Ihrem Containerimage enthält. Der Zugriff auf die Ressourcengruppe muss über die Abonnement-ID (oben) möglich sein. Sie finden den Namen im Azure-Portal auf der Seite [Ressourcengruppen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups).

**Azure Container Registry-Name**: Geben Sie den Namen der [Azure Container Registry](../container-registry/container-registry-intro.md)-Instanz an, die Ihr Containerimage enthält. Die Containerregistrierung muss der Azure-Ressourcengruppe zugeordnet sein, die Sie zuvor angegeben haben. Geben Sie nur den Registrierungsnamen an, nicht den vollständigen Namen des Anmeldeservers. Achten Sie unbedingt darauf, **azurecr.io** nicht in den Namen einzuschließen. Sie finden den Registrierungsnamen auf der Seite [Containerregistrierungen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) im Azure-Portal.

**Administratorbenutzername für die Azure Container Registry**: Geben Sie den [Administratorbenutzernamen](../container-registry/container-registry-authentication.md#admin-account) an, der mit der Azure Container Registry-Instanz mit Ihrem Containerimage verknüpft ist. Benutzername und Kennwort sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Um den Administratorbenutzernamen und das zugeordnete Kennwort abzurufen, legen Sie die Eigenschaft **admin-enabled** (Von Administrator aktiviert) an der Azure-Befehlszeilenschnittstelle auf **True** fest. Optional können Sie im Azure-Portal **Administratorbenutzer** auf **Aktivieren** festlegen.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Darstellung des Dialogfelds „Containerregistrierung aktualisieren“.":::

**Kennwort für die Azure Container Registry**: Geben Sie das Kennwort für den Administratorbenutzernamen ein, der der Azure Container Registry-Instanz mit Ihrem Containerimage zugeordnet ist. Benutzername und Kennwort sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Sie können das Kennwort im Azure-Portal unter **Containerregistrierung** > **Zugriffsschlüssel** oder in der Azure-Befehlszeilenschnittstelle mit dem [Befehl „show“](/cli/azure/acr/credential#az-acr-credential-show) abrufen.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Menü „Zugriffsschlüssel“.":::

**Repositoryname innerhalb der Azure Container Registry**. Geben Sie den Namen des Azure Container Registry-Repositorys ein, das Ihr Image enthält. Sie geben den Namen des Repositorys an, wenn Sie das Image in die Registrierung pushen. Sie finden den Namen des Repositorys auf der Seite [Containerregistrierung](https://azure.microsoft.com/services/container-registry/) > **Repositorys**. Weitere Informationen finden Sie unter [Anzeigen von Containerregistrierungsrepositorys im Azure-Portal](../container-registry/container-registry-repositories.md).

> [!NOTE]
> Nachdem der Name festgelegt wurde, kann er nicht mehr geändert werden. Verwenden Sie für jedes Angebot in Ihrem Konto einen eindeutigen Namen.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Imagetags für neue Versionen Ihres Angebots

Kunden müssen imstande sein, Updates beim Azure Marketplace automatisch abzurufen, wenn Sie ein Update veröffentlichen. Wenn sie kein Update ausführen möchten, müssen sie die Möglichkeit haben, bei einer bestimmten Version Ihres Images zu bleiben. Sie können dies erreichen, indem Sie für jedes Update des Images neue Imagetags hinzufügen.

### <a name="image-tag"></a>Imagetag

Dieses Feld muss ein **aktuellstes** Tag enthalten, das auf die letzte Version Ihres Images auf allen unterstützten Plattformen verweist. Es muss außerdem ein Versionstag enthalten (beispielsweise beginnend mit xx.xx.xx, wobei xx eine Zahl ist). Kunden sollten [Manifesttags](https://github.com/estesp/manifest-tool) verwenden, um mehrere Zielplattformen anzugeben. Zudem müssen alle Tags, auf die von einem Manifesttag verwiesen wird, hinzugefügt werden, damit wir sie hochladen können.

Alle Manifesttags (mit Ausnahme des aktuellsten Tags) müssen entweder mit X.Y **-**  oder X.Y.Z- beginnen, wobei X, Y und Z ganze Zahlen sind. Wenn beispielsweise ein **aktuellstes** Tag auf 1.0.1-linux-x64, 1.0.1-linux-arm32 und 1.0.1-windows-arm32 verweist, müssen dem Feld diese sechs Tags hinzugefügt werden. Weitere Informationen finden Sie unter [Vorbereiten Ihrer technischen Azure-Containerressourcen](create-azure-container-technical-assets.md).

> [!NOTE]
> Sie sollten Ihrem Image unbedingt ein Testtag hinzufügen, damit Sie das Image beim Testen erkennen können.

## <a name="review-and-publish"></a>Überprüfen und veröffentlichen

Nachdem Sie alle erforderlichen Abschnitte des Angebots abgeschlossen haben, können Sie es zum Überprüfen und Veröffentlichen einreichen.

Wählen Sie in der oberen rechten Ecke des Portals **Überprüfen und** **veröffentlichen** aus.

Auf der Überprüfungsseite haben Sie folgende Möglichkeiten:

- Anzeigen des Abschlussstatus für die einzelnen Abschnitte des Angebots. Sie können erst veröffentlichen, wenn alle Abschnitte des Angebots als abgeschlossen gekennzeichnet sind.
  - **Nicht gestartet**: Die Überprüfung wurde nicht gestartet und muss abgeschlossen werden.
  - **Unvollständig**: Es liegen Fehler vor, die behoben werden müssen, oder Sie müssen weitere Informationen angeben. Anweisungen dazu finden Sie in den Abschnitten weiter oben in diesem Dokument.
  - **Abgeschlossen**: Alle erforderlichen Daten sind vorhanden, und es liegen keine Fehler vor. Sie können das Angebot erst einreichen, wenn alle Abschnitte des Angebots abgeschlossen sind.
- Stellen Sie dem Zertifizierungsteam Testanweisungen zur Verfügung, um sicherzustellen, dass Ihr Angebot ordnungsgemäß getestet wird. Stellen Sie außerdem alle ergänzenden Anmerkungen bereit, die das Verständnis Ihres Angebots unterstützen.

Um das Angebot zur Veröffentlichung einzureichen, wählen Sie **Veröffentlichen** aus.

Wir senden Ihnen eine E-Mail, um Ihnen mitzuteilen, wann eine Vorschauversion des Angebots zur Überprüfung und Genehmigung verfügbar ist.

Kehren Sie zu Partner Center zurück, und wählen Sie **Live schalten** aus, um das Angebot für die Öffentlichkeit zu veröffentlichen.

## <a name="next-step"></a>Nächster Schritt

- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](./partner-center-portal/update-existing-offer.md)