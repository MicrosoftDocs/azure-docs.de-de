---
title: Erstellen eines Power BI-App-Angebots in Microsoft AppSource
description: Erfahren Sie, wie Sie ein Power BI-App-Angebot erstellen und in Microsoft AppSource veröffentlichen.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: 99c60cf541b3997f2aeca2ccef5a5af6b9c88e08
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131495"
---
# <a name="create-a-power-bi-app-offer"></a>Erstellen eines Power BI-App-Angebots

In diesem Artikel wird beschrieben, wie Sie ein Power BI-App-Angebot erstellen und in [Microsoft AppSource](https://appsource.microsoft.com/) veröffentlichen.

Bevor Sie beginnen, [erstellen Sie ein Konto im kommerziellen Marketplace in Partner Center](create-account.md), sofern Sie dies noch nicht getan haben. Vergewissern Sie sich, dass Ihr Konto im Programm „Kommerzieller Marketplace“ registriert ist.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Übersicht**.
3. Wählen Sie auf der Übersichtsseite die Option **+ Neues Angebot** > **Power BI-Dienst-App** aus.

   ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Nach der Veröffentlichung eines Angebots werden die daran im Partner Center vorgenommenen Änderungen erst nach der erneuten Veröffentlichung des Angebots in Onlineshops angezeigt. Achten Sie darauf, nach dem Vornehmen von Änderungen immer erneut zu veröffentlichen.

> [!IMPORTANT]
> Wenn die Option **Power BI-Dienst-App** nicht angezeigt wird oder nicht aktiviert ist, verfügt Ihr Konto nicht über die Berechtigung zum Erstellen dieses Angebotstyps. Überprüfen Sie, ob Sie alle [Anforderungen](create-power-bi-app-overview.md) für diesen Angebotstyp erfüllen (einschließlich der Registrierung für ein Entwicklerkonto).

## <a name="new-offer"></a>Neues Angebot

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID wird für Kunden unter der Webadresse für das Marketplace-Angebot und ggf. in Azure Resource Manager-Vorlagen angezeigt.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt. Wenn Sie hier z. B. **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird im Marketplace nicht verwendet und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
- Nachdem Sie **Erstellen** ausgewählt haben, kann der Angebotsalias nicht mehr geändert werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

## <a name="offer-overview"></a>Angebotsübersicht

Diese Seite zeigt eine visuelle Darstellung der zum Veröffentlichen dieses Angebots erforderlichen Schritte (sowohl der abgeschlossenen als auch der noch ausstehenden) und der erwarteten Dauer ihrer Ausführung.

Sie enthält Links zum Durchführen von Vorgängen für dieses Angebot auf der Grundlage der von Ihnen ausgewählten Optionen. Beispiel:

- Wenn das Angebot einen Entwurf darstellt: Delete draft offer (Angebotsentwurf löschen)
- Wenn es sich um ein Liveangebot handelt: [Stop selling the offer](update-existing-offer.md#stop-selling-an-offer-or-plan) (Angebot nicht mehr verkaufen)
- Wenn das Angebot in der Vorschau ist: [Go-live](../review-publish-offer.md#previewing-and-approving-your-offer) (Live schalten)
- Wenn Sie die Abmeldung beim Herausgeber nicht abgeschlossen haben: [Cancel publishing](../review-publish-offer.md#cancel-publishing) (Veröffentlichung abbrechen).

## <a name="offer-setup"></a>Angebotseinrichtung

### <a name="customer-leads"></a>Kundenleads

Wenn Sie Ihr Angebot mithilfe des Partner Centers im Marketplace veröffentlichen, müssen Sie es mit Ihrem CRM-System (Customer Relationship Management) verbinden. Dadurch empfangen Sie Kontaktinformationen zum Kunden, sobald jemand sein Interesse an Ihrem Produkt bekundet oder es verwendet.

1. Wählen Sie ein Leadziel aus, an das wir Kundenleads senden sollen. Das Partner Center unterstützt die folgenden CRM-Systeme:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Wenn Ihr CRM-System nicht in dieser Liste aufgeführt ist, verwenden Sie eine [Azure-Tabelle](commercial-marketplace-lead-management-instructions-azure-table.md) oder einen [HTTPS-Endpunkt](commercial-marketplace-lead-management-instructions-https.md), um die Leaddaten von Kunden zu speichern. Exportieren Sie die Daten dann in Ihr CRM-System.

2. Verbinden Sie Ihr Angebot bei der Veröffentlichung im Partner Center mit dem Leadziel.
3. Vergewissern Sie sich, dass die Verbindung mit dem Leadziel ordnungsgemäß konfiguriert ist. Nach der Veröffentlichung Ihres Angebots im Partner Center überprüfen wir die Verbindung und senden Ihnen einen Testlead. Während Sie das Angebot vor der Liveschaltung als Vorschau anzeigen, können Sie Ihre Leadverbindung ebenfalls testen, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu kaufen.
4. Achten Sie darauf, dass die Verbindung mit dem Leadziel immer auf dem neuesten Stand ist, damit keine Leads verloren gehen.

Hier finden Sie einige zusätzliche Ressourcen zur Leadverwaltung:

- [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](commercial-marketplace-get-customer-leads.md)
- [Häufige Fragen zur Leadverwaltung](../lead-management-faq.md#common-questions-about-lead-management)
- [Problembehandlung bei Leadkonfigurationsfehlern](../lead-management-faq.md#publishing-config-errors)
- [Übersicht über die Leadverwaltung](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)-PDF-Datei (vergewissern Sie sich, dass Ihr Popupblocker deaktiviert ist).

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="properties"></a>Eigenschaften

Auf dieser Seite können Sie die Kategorien und Branchen, die zum Gruppieren Ihres Angebots im Marketplace verwendet werden, die Version Ihrer App und die Verträge für Ihr Angebot definieren.

### <a name="category"></a>Category

Wählen Sie Kategorien und Unterkategorien aus, um Ihr Angebot in den geeigneten Marketplace-Suchbereichen zu platzieren. Vergessen Sie nicht, in der Angebotsbeschreibung anzugeben, inwiefern Ihr Angebot diesen Kategorien entspricht. Wählen Sie Folgendes:

- Mindestens eine und bis zu zwei Kategorien, einschließlich einer primären und einer sekundären Kategorie (optional).
- Bis zu zwei Unterkategorien für jede primäre und/oder sekundäre Kategorie. Wenn keine Unterkategorie auf Ihr Angebot anwendbar ist, wählen Sie **Nicht zutreffend** aus.

Eine vollständige Liste der Kategorien und Unterkategorien finden Sie unter [Bewährte Methoden für Angebotslistung](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branche

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Rechtliche Hinweise

#### <a name="terms-and-conditions"></a>Geschäftsbedingungen

Um eigene Geschäftsbedingungen anzugeben, geben Sie bis zu 10.000 Zeichen im Feld **Terms and Conditions** (Geschäftsbedingungen) ein. Wenn Ihre Geschäftsbedingungen eine längere Beschreibung erfordern, geben Sie einen einzelnen Weblink zu diesen ein. Er wird für Kunden als aktiver Link angezeigt.

Kunden müssen diesen Bestimmungen zustimmen, bevor sie Ihr Angebot testen können.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit dem nächsten Abschnitt fortfahren, Angebotsliste.

## <a name="offer-listing"></a>Angebotsliste

Hier definieren Sie die Angebotsdetails, die im Marketplace angezeigt werden. Dazu gehören der Name des Angebots, eine Beschreibung, Bilder usw.

### <a name="language"></a>Sprache

Wählen Sie die Sprache aus, in der Ihr Angebot aufgeführt wird. Derzeit ist **Englisch (Vereinigte Staaten)** die einzige verfügbare Option.

Definieren Sie für jede Sprache/jeden Markt Marketplace-Informationen (z. B. Angebotsname, Beschreibung, Bilder usw.). Wählen Sie die Sprache bzw. den Namen des Markts aus, um diese Informationen anzugeben.

> [!NOTE]
> Eine Angabe der Angebotsdetails in englischer Sprache ist nicht erforderlich, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt. Sie können auch einen nützlichen Link mit Inhalten in einer anderen Sprache bereitstellen als der Sprache, die für die Angebotsliste verwendet wird.

Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen in Microsoft AppSource angezeigt werden (die aufgelisteten Preise dienen lediglich als Beispiele und stellen keine tatsächlichen Kosten dar):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Veranschaulicht, wie dieses Angebot in Microsoft AppSource angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Logo
2. Produkte
3. Kategorien
4. Branchen
5. Supportadresse (Link)
6. Nutzungsbedingungen
7. Datenschutzrichtlinie
8. Angebotsname
9. Zusammenfassung
10. BESCHREIBUNG
11. Screenshots/Videos

### <a name="name"></a>Name

Der Name, den Sie hier eingeben, wird als Titel Ihres Angebots angezeigt. Dieses Feld ist bereits mit dem Text ausgefüllt, den Sie beim Erstellen des Angebots im Feld **Angebotsalias** eingegeben haben. Sie können diesen Namen später ändern.

Der Name:

- Kann markenrechtlich geschützt sein (und Sie können Marken- oder Copyrightsymbole einschließen).
- Darf nicht mehr als 50 Zeichen lang sein.
- Darf keine Emojis enthalten.

### <a name="search-results-summary"></a>Zusammenfassung der Suchergebnisse

Geben Sie eine kurze Beschreibung Ihres Angebots an. Sie kann bis zu 100 Zeichen lang sein und wird in den Marketplace-Suchergebnissen verwendet.

### <a name="description"></a>BESCHREIBUNG

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Suchbegriffe

Geben Sie bis zu drei optionale Suchbegriffe ein, die Kunden helfen, Ihr Angebot im Marketplace zu finden. Um optimale Ergebnisse zu erzielen, sollten Sie diese Schlüsselwörter auch in ihrer Beschreibung verwenden.

### <a name="helpprivacy-web-addresses"></a>Webadressen für Hilfe/Datenschutz

Stellen Sie Links bereit, um den Kunden das Verständnis Ihres Angebots zu erleichtern.

#### <a name="help-link"></a>Hilfelink

Geben Sie die Webadresse ein, unter der Kunden mehr über Ihr Angebot erfahren können.

#### <a name="privacy-policy-url"></a>URL zur Datenschutzrichtlinie

Geben Sie die Webadresse der Datenschutzrichtlinien Ihrer Organisation ein. Es liegt in Ihrer Verantwortung, sicherzustellen, dass Ihr Angebot den Gesetzen und Regelungen zum Datenschutz entspricht. Es liegt ferner in Ihrer Verantwortung, eine gültige Datenschutzrichtlinie auf Ihrer Website zu veröffentlichen.

### <a name="contact-information"></a>Kontaktinformationen

Sie müssen den Namen, die E-Mail-Adresse und die Telefonnummer für einen **Supportkontakt** und einen **technischen Ansprechpartner** angeben. Diese Informationen werden Kunden nicht angezeigt. Sie sind für Microsoft verfügbar und dürfen gegenüber CSP-Partnern (Cloud Solution Provider) angegeben werden.

- Supportkontakt (erforderlich): Für allgemeine Supportfragen.
- Technischer Ansprechpartner (erforderlich): Für technische Fragen und Zertifizierungsprobleme.
- CSP-Programm-Kontakt (optional): Für Fragen von Handelspartnern zum CSP-Programm.

Geben Sie im Abschnitt **Supportkontakt** die Webadresse der **Supportwebsite** an, auf der Partner Support für Ihr Angebot erhalten.

### <a name="supporting-documents"></a>Unterstützende Dokumente

Geben Sie mindestens ein und bis zu drei verwandte Marketingdokumente im PDF-Format an. Beispiele: Whitepaper, Broschüren, Checklisten oder Präsentationen.

### <a name="marketplace-images"></a>Marketplace-Bilder

Stellen Sie Logos und Bilder zur Verwendung mit Ihrem Angebot bereit. Alle Bilder müssen das PNG-Format aufweisen. Unscharfe Bilder werden abgelehnt.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Wenn beim Hochladen von Dateien ein Problem auftritt, vergewissern Sie sich, dass das lokale Netzwerk den von Partner Center verwendeten Dienst `https://upload.xboxlive.com` nicht blockiert.

#### <a name="store-logos"></a>Store-Logos

Geben Sie eine PNG-Datei für das **große** Logo an. Dies wird in Partner Center verwendet, um ein **kleines** Logo zu erstellen. Später können Sie dieses Logo optional durch ein anderes Bild ersetzen.

- **Groß** (von 216 x 216 bis 350 x 350 Pixel, erforderlich)
- **Klein** (48 x 48 Pixel, optional)

Diese Logos werden an unterschiedlichen Stellen in der Auflistung verwendet:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshots

Fügen Sie mindestens einen und bis zu fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Jeder muss 1.280 x 720 Pixel groß sein und im PNG-Format vorliegen.

#### <a name="videos-optional"></a>Videos (optional)

Fügen Sie optional bis zu fünf Videos hinzu, die Ihr Angebot vorstellen. Geben Sie den Namen des Videos, seine Webadresse und ein Miniaturbild des Videos im PNG-Format mit einer Größe von 1280 x 720 Pixel ein.

#### <a name="additional-marketplace-listing-resources"></a>Zusätzliche Ressourcen für Marketplace-Listen

Weitere Informationen zum Erstellen von Angebotslistungen finden Sie unter [Bewährte Methoden für Angebotslistung](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Technische Konfiguration

Stufen Sie Ihre App im Power BI Dienst zur Produktionsreife hoch, und stellen Sie den Link zum Power BI-App-Installationsprogramm bereit, mit dem Kunden Ihre App installieren können. Weitere Informationen finden Sie unter [Veröffentlichen von Apps mit Dashboards und Berichten in Power BI](/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Ergänzender Inhalt

Stellen Sie zusätzliche Informationen zu Ihrem Angebot bereit, um uns bei seiner Validierung zu helfen. Diese Informationen werden weder den Kunden angezeigt noch im Marketplace veröffentlicht.

### <a name="validation-assets"></a>Validierungsressourcen

Fügen Sie optional Anweisungen (bis zu 3.000 Zeichen) hinzu, um das Microsoft-Überprüfungsteam beim Konfigurieren und Testen Ihrer App sowie beim Herstellen einer Verbindung mit Ihrer App zu unterstützen. Fügen Sie typische Konfigurationseinstellungen, Konten, Parameter oder andere Informationen ein, die zum Testen der Option „Daten verbinden“ verwendet werden können. Diese Informationen werden nur dem Überprüfungsteam angezeigt und dienen nur zu Überprüfungszwecken.

## <a name="review-and-publish"></a>Überprüfen und veröffentlichen

Nachdem Sie alle erforderlichen Abschnitte des Angebots durchgearbeitet haben, können Sie es zum Überprüfen und Veröffentlichen einreichen.

Wählen Sie in der oberen rechten Ecke des Portals **Überprüfen und veröffentlichen** aus.

Auf der Überprüfungsseite haben Sie folgende Möglichkeiten:

- Anzeigen des Abschlussstatus für die einzelnen Abschnitte des Angebots. Sie können erst veröffentlichen, wenn alle Abschnitte des Angebots als abgeschlossen gekennzeichnet sind.
  - **Nicht gestartet** : Die Überprüfung des Abschnitts wurde nicht gestartet und muss abgeschlossen werden.
  - **Unvollständig** : Der Abschnitt enthält Fehler, die behoben werden müssen, oder erfordert eine Ergänzung der Informationen. Anweisungen dazu finden Sie in den Abschnitten weiter oben in diesem Dokument.
  - **Abgeschlossen** : Der Abschnitt enthält alle erforderlichen Daten, und es sind keine Fehler aufgetreten. Sie können das Angebot erst einreichen, wenn alle Abschnitte des Angebots abgeschlossen sind.
- Stellen Sie dem Zertifizierungsteam Testanweisungen zur Verfügung, um sicherzustellen, dass Ihre App ordnungsgemäß getestet wird. Stellen Sie außerdem alle ergänzenden Anmerkungen bereit, die das Verständnis Ihres Angebots unterstützen.

Um das Angebot zur Veröffentlichung einzureichen, wählen Sie **Veröffentlichen** aus.

Wir senden Ihnen eine E-Mail, um Ihnen mitzuteilen, wann eine Vorschauversion des Angebots zur Überprüfung und Genehmigung verfügbar ist. Kehren Sie zu Partner Center zurück, und wählen Sie **Live schalten** aus, um das Angebot für eine öffentliche Zielgruppe zu veröffentlichen.