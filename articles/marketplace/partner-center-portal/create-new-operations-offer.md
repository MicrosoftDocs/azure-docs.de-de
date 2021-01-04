---
title: Erstellen Sie eines Dynamics 365 for Operations-Angebots im kommerziellen Marketplace
description: Erstellen eines neuen Dynamics 365 for Operations-Angebots (Software-as-a-Service) für das Auflisten oder Verkaufen in Azure Marketplace, AppSource oder über das CSP-Programm (Cloud Solution Provider) mit dem kommerziellen Marketplace-Portal im Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 1c28b4edab1e1033bf9ac7df6b1249b549bb4fe1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131512"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Erstellen eines Angebots für Dynamics 365 for Operations

In diesem Thema wird erläutert, wie Sie ein neues Angebot für Dynamics 365 for Operations erstellen. [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) ist ein ERP-Dienst (Enterprise Resource Planning), der erweiterte Features für Finanzierung, operative Vorgänge, Fertigung und Lieferkettenmanagement unterstützt. Alle Angebote für Dynamics 365 for Operations müssen unseren Zertifizierungsprozess durchlaufen.

Bevor Sie beginnen, [erstellen Sie ein Konto im kommerziellen Marketplace in Partner Center](create-account.md), sofern Sie dies noch nicht getan haben. Vergewissern Sie sich, dass Ihr Konto im Programm „Kommerzieller Marketplace“ registriert ist.

>[!NOTE]
> Nach der Veröffentlichung eines Angebots werden Änderungen am Angebot nur in Partner Center und im Online Store aktualisiert, wenn Sie das Angebot erneut zur Veröffentlichung übermittelt haben.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Übersicht**.
3. Klicken Sie auf der Übersichtsseite auf **+ Neues Angebot** > **Dynamics 365 for Operations**.

    ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> Nach der Veröffentlichung eines Angebots werden die daran im Partner Center vorgenommenen Änderungen erst nach der erneuten Veröffentlichung des Angebots in Onlineshops angezeigt. Achten Sie darauf, nach dem Vornehmen von Änderungen immer erneut zu veröffentlichen.

## <a name="new-offer"></a>Neues Angebot

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID wird für Kunden unter der Webadresse für das Marketplace-Angebot und ggf. in Azure Resource Manager-Vorlagen angezeigt.
- Die Angebots-ID darf zusammen mit der Herausgeber-ID nicht länger als 40 Zeichen lang sein.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen. Wenn die Herausgeber-ID z. B. „testpublisherid“ heißt und Sie hier **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird im Marketplace nicht verwendet und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

## <a name="offer-setup"></a>Angebotseinrichtung

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Wie sollen potenzielle Kunden mit diesem Angebot interagieren?

Wählen Sie die Option aus, die Sie für dieses Angebot verwenden möchten.

#### <a name="get-it-now-free"></a>Jetzt abrufen (kostenlos)

Listen Sie Ihr Angebot für Kunden als kostenlos auf.

#### <a name="free-trial-listing"></a>Kostenlose Testversion (Listing)

Listen Sie Ihr Angebot für Kunden als kostenlos auf, und stellen Sie einen Link zu einer kostenlose Testversion bereit. Kostenlose Testversionen in der Angebotsliste werden von Ihrem Dienst erstellt, verwaltet und konfiguriert und weisen keine von Microsoft verwalteten Abonnements auf.

> [!NOTE]
> Die Token, die Ihre Anwendung über den Testlink erhält, können nur verwendet werden, um zum Automatisieren der Kontoerstellung in Ihrer App Benutzerinformationen aus Azure Active Directory (Azure AD) abzurufen. Die Authentifizierung mit diesem Token wird für Microsoft-Konten nicht unterstützt.

#### <a name="contact-me"></a>Kontakt mit mir aufnehmen

Sammeln Sie über Ihr CRM-System (Customer Relationship Management) Kundenkontaktinformationen. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Marketplace, auf dem der Kunde Ihr Angebot gefunden hat, werden an das CRM-System gesendet, das Sie konfiguriert haben. Weitere Informationen zum Konfigurieren des CRM-Systems finden Sie unter [Kundenleads](#customer-leads).

### <a name="test-drive"></a>Testversion

Eine Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen die Möglichkeit geben, das Produkt vor dem Kauf zu testen. Dies führt zu einer höheren Konversionsrate und zur Generierung von aussichtsreichen Leads. Um mehr zu erfahren, lesen Sie zunächst [Was ist die Testversion?](../what-is-test-drive.md)

Aktivieren Sie das Kontrollkästchen **Testversion aktivieren** , um eine Testversion für einen festen Zeitraum zu aktivieren. Deaktivieren Sie dieses Kontrollkästchen, um die Testversion aus Ihrem Angebot zu entfernen.

### <a name="customer-leads"></a>Kundenleads

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Weitere Informationen finden Sie in der [Übersicht über die Leadverwaltung](./commercial-marketplace-get-customer-leads.md).

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="properties"></a>Eigenschaften

Auf dieser Seite können Sie die Kategorien und Branchen, die zum Zuordnen Ihres Angebots im Marketplace verwendet werden, die Version Ihrer App und die Verträge für Ihr Angebot definieren.

### <a name="category"></a>Category

Wählen Sie Kategorien und Unterkategorien aus, um Ihr Angebot in den geeigneten Marketplace-Suchbereichen zu platzieren. Vergessen Sie nicht, in der Angebotsbeschreibung anzugeben, inwiefern Ihr Angebot diesen Kategorien entspricht. Wählen Sie Folgendes:

- Mindestens eine und bis zu zwei Kategorien, einschließlich einer primären und einer sekundären Kategorie (optional).
- Bis zu zwei Unterkategorien für jede primäre und/oder sekundäre Kategorie. Wenn keine Unterkategorie auf Ihr Angebot anwendbar ist, wählen Sie **Nicht zutreffend** aus.

Eine vollständige Liste der Kategorien und Unterkategorien finden Sie unter [Bewährte Methoden für Angebotslistung](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branche

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>App-Version

Geben Sie die Versionsnummer Ihres Angebots ein. Für Kunden wird diese Version auf der Detailseite des Angebots aufgeführt.

### <a name="terms-and-conditions"></a>Geschäftsbedingungen

Geben Sie im Feld **Geschäftsbedingungen** Ihre eigenen rechtlichen Bestimmungen an. Sie können auch die URL angeben, unter der Ihre Geschäftsbedingungen zu finden sind. Kunden müssen diese Bedingungen akzeptieren, bevor sie Ihr Angebot testen können.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="offer-listing"></a>Angebotsliste

Auf dieser Seite werden die Sprachen angezeigt, in denen Ihr Angebot aufgeführt wird. Derzeit ist **Englisch (Vereinigte Staaten)** die einzige verfügbare Option.

Sie müssen für jede Sprache/jeden Markt Marketplace-Informationen (Angebotsname, Beschreibung, Bilder usw.) definieren. Wählen Sie die Sprache bzw. den Namen des Markts aus, um diese Informationen anzugeben.

> [!NOTE]
> Die Inhalte der Angebotsliste (z. B. Beschreibung, Dokumente, Screenshots, Nutzungsbedingungen usw.) müssen nicht in englischer Sprache vorliegen, solange die Angebotsbeschreibung mit dem folgenden Satz beginnt: „Diese Anwendung ist nur auf [nicht englische Sprache] verfügbar.“. Es ist auch zulässig, eine *Nützlicher Link-URL* bereitzustellen, um Inhalte in einer anderen Sprache als der in der Angebotsliste verwendeten anzubieten.

Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen in Microsoft AppSource angezeigt werden:

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Veranschaulicht, wie dieses Angebot in Microsoft AppSource angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Logo
2. Produkte
3. Kategorien
4. Branchen
5. Supportadresse (Link)
6. Nutzungsbedingungen
7. Datenschutzrichtlinie
8. Angebotsname
9. Screenshots/Videos
10. BESCHREIBUNG

### <a name="name"></a>Name

Der hier eingegebene Name wird Kunden als Titel Ihrer Angebotsliste angezeigt. Dieses Feld ist bereits mit dem Text gefüllt, den Sie beim Erstellen des Angebots als **Angebotsalias** eingegeben haben. Sie können diesen Wert jedoch ändern. Dieser Name kann markenrechtlich geschützt sein (und Sie können Marken- oder Copyrightsymbole einschließen). Der Name darf nicht mehr als 50 Zeichen umfassen und keine Emojis enthalten.

### <a name="short-description"></a>Kurze Beschreibung

Geben Sie eine kurze Beschreibung Ihres Angebots an (bis zu 100 Zeichen). Diese Beschreibung kann in Marketplace-Suchergebnissen verwendet werden.

### <a name="description"></a>BESCHREIBUNG

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Suchbegriffe

Sie können optional bis zu drei Suchbegriffe eingeben, die Kunden helfen, Ihr Angebot im Marketplace zu finden. Um optimale Ergebnisse zu erzielen, sollten Sie diese Schlüsselwörter auch in ihrer Beschreibung verwenden.

### <a name="products-your-app-works-with"></a>Produkte, mit denen Ihre App zusammenarbeitet

Wenn Sie den Kunden mitteilen möchten, dass Ihre App mit bestimmten Produkten zusammenarbeitet, geben Sie hier bis zu drei Produktnamen ein.

### <a name="support-urls"></a>Support-URLs

In diesem Abschnitt können Sie Links bereitstellen, die Kunden dabei helfen, mehr über Ihr Angebot zu erfahren.

#### <a name="help-link"></a>Hilfelink

Geben Sie die URL ein, unter der Kunden mehr über Ihr Angebot erfahren können.

#### <a name="privacy-policy-url"></a>URL zur Datenschutzrichtlinie

Geben Sie die URL zur Datenschutzrichtlinie Ihrer Organisation ein. Sie müssen sicherzustellen, dass die App die Datenschutzgesetze und -bestimmungen erfüllt, und Sie müssen eine gültige Datenschutzrichtlinie bereitstellen.

### <a name="contacts"></a>Kontakte

Geben Sie in diesem Abschnitt den Namen, die E-Mail-Adresse und die Telefonnummer für einen **Supportkontakt** und einen **Engineering-Kontakt** an. Diese Informationen werden Kunden nicht angezeigt, sind aber für Microsoft verfügbar und können an CSP-Partner weitergegeben werden.

Geben Sie im Abschnitt **Supportkontakt** die **Support-URL** an, unter der CSP-Partner Support für Ihr Angebot erhalten.

### <a name="supporting-documents"></a>Unterstützende Dokumente

Geben Sie mindestens ein (und höchstens drei) zugehörige Marketingdokumente an, z. B. Whitepaper, Broschüren, Checklisten oder Präsentationen. Diese Dokumente müssen im PDF-Format vorliegen.

### <a name="marketplace-images"></a>Marketplace-Bilder

In diesem Abschnitt können Sie Logos und Bilder bereitstellen, die beim Anzeigen Ihres Angebots für Kunden verwendet werden. Alle Bilder müssen das PNG-Format aufweisen.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Wenn beim Hochladen von Dateien ein Problem auftritt, vergewissern Sie sich, dass das lokale Netzwerk den von Partner Center verwendeten Dienst https://upload.xboxlive.com nicht blockiert.

#### <a name="store-logos"></a>Store-Logos

Geben Sie eine PNG-Datei für das **große** Logo an. Dies wird in Partner Center verwendet, um ein **kleines** Logo zu erstellen. Später können Sie dieses Logo optional durch ein anderes Bild ersetzen.

- **Groß** (von 216 x 216 bis 350 x 350 px, erforderlich)
- **Klein** (48 x 48 px, optional)

Diese Logos werden an unterschiedlichen Stellen in der Auflistung verwendet:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshots

Fügen Sie Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Mindestens ein Screenshot ist erforderlich, und Sie können bis zu fünf hinzufügen. Alle Screenshots müssen in der Größe 1280 x 720 Pixel vorliegen.

#### <a name="videos"></a>Videos

Optional können Sie bis zu vier Videos hinzufügen, die Ihr Angebot veranschaulichen. Diese Videos sollten auf YouTube und/oder Vimeo gehostet werden. Geben Sie für jedes Video den Namen, seine URL und ein Miniaturbild (1280 x 720 Pixel) ein.

#### <a name="additional-marketplace-listing-resources"></a>Zusätzliche Ressourcen für Marketplace-Listen

[Bewährte Methoden für Angebotslistung](../gtm-offer-listing-best-practices.md)

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="availability"></a>Verfügbarkeit

Auf dieser Seite finden Sie Optionen, mit denen Sie festlegen können, wo und wie Sie Ihr Angebot zur Verfügung stellen.

### <a name="markets"></a>Märkte

In diesem Abschnitt können Sie angeben, in welchen Märkten das Angebot verfügbar sein soll. Wählen Sie hierzu **Märkte bearbeiten** aus. Dadurch wird das Popupfenster **Marktauswahl** angezeigt.

Standardmäßig sind keine Märkte ausgewählt. Wählen Sie mindestens einen Markt aus, um Ihr Angebot zu veröffentlichen. Klicken Sie auf **Alle auswählen** , um Ihr Angebot in allen möglichen Märkten verfügbar zu machen, oder wählen Sie die gewünschten Märkte aus, die Sie hinzufügen möchten. Wenn Sie fertig sind, wählen Sie **Speichern** aus.

Ihre hier getroffene Auswahl gilt nur für neue Käufe. Wenn Ihre App bereits von einem Benutzer in einem bestimmten Markt verwendet wird und Sie den Markt später entfernen, können Benutzer, die in diesem Markt bereits über Ihr Angebot verfügen, es weiterhin nutzen, aber Ihr Angebot ist nicht für neue Kunden in diesem Markt erhältlich.

> [!IMPORTANT]
> Es liegt in ihrer Verantwortung, alle lokalen rechtlichen Anforderungen zu erfüllen, auch wenn diese Anforderungen nicht hier oder im Partner Center aufgeführt sind.

Beachten Sie Folgendes: Auch wenn Sie „Alle Märkte“ auswählen, kann es sein, dass bestimmte Angebote in einigen Ländern und Regionen aufgrund lokaler Gesetze und Einschränkungen oder anderer Faktoren nicht gelistet werden.

### <a name="preview-audience"></a>Vorschauzielgruppe

Bevor Sie Ihr Angebot im Marketplace für die breite Öffentlichkeit live schalten, müssen Sie es zunächst für eine begrenzte **Vorschauzielgruppe** verfügbar machen. Geben Sie hier einen **Hide-Schlüssel** (eine beliebige Zeichenfolge, die nur Kleinbuchstaben und/oder Ziffern enthält) ein. Mitglieder Ihrer Vorschauzielgruppe können diesen Hide-Schlüssel als Token verwenden, um eine Vorschau Ihres Angebots im Marketplace anzuzeigen.

Wenn Sie anschließend bereit sind, Ihr Angebot verfügbar zu machen und die Vorschaueinschränkung zu entfernen, müssen Sie den **Hide-Schlüssel** entfernen und das Angebot erneut veröffentlichen.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="technical-configuration"></a>Technische Konfiguration

Auf dieser Seite werden die technischen Informationen definiert, die für das Herstellen einer Verbindung mit Ihrem Angebot verwendet werden. Durch diese Verbindung können wir Ihr Angebot für den Endkunden bereitstellen, wenn er es kaufen möchte.

### <a name="solution-identifier"></a>Lösungsbezeichner

Geben Sie den Lösungsbezeichner (GUID) für Ihre Lösung an.

So finden Sie Ihren Lösungsbezeichner:

1. Wählen Sie in Microsoft Dynamics Lifecycle Services (LCS) die Option **Lösungsverwaltung** aus.
2. Wählen Sie Ihre Lösung aus, und suchen Sie in der **Paketübersicht** nach dem **Lösungsbezeichner**. Wenn der Bezeichner leer ist, wählen Sie **Bearbeiten** aus, veröffentlichen Sie Ihr Paket erneut, und versuchen Sie es dann noch einmal.

### <a name="release-version"></a>Releaseversion

Wählen Sie die Version von Dynamics 365 for Finance and Operations aus, mit der diese Lösung verwendet werden kann.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="test-drive-technical-configuration"></a>Technische Konfiguration der Testversion

Auf dieser Seite können Sie eine Demonstration („Testversion“) einrichten, mit der Kunden Ihr Angebot vor dem Erwerb ausprobieren können. Weitere Informationen finden Sie unter [Was ist die Testversion?](../what-is-test-drive.md).

Aktivieren Sie auf der Registerkarte [Angebotseinrichtung](#test-drive) das Kontrollkästchen **Testversion aktivieren** , um eine Testversion zu aktivieren. Deaktivieren Sie dieses Kontrollkästchen, um die Testversion aus Ihrem Angebot zu entfernen.

Sobald Sie die Einrichtung der Testversion abgeschlossen haben, wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="supplemental-content"></a>Ergänzender Inhalt

Auf dieser Seite können Sie zusätzliche Informationen zu Ihrem Angebot bereitstellen, um uns bei der Validierung Ihres Angebots zu helfen. Diese Informationen werden weder den Kunden angezeigt noch im Marketplace veröffentlicht.

### <a name="validation-assets"></a>Validierungsressourcen

Laden Sie in diesem Abschnitt einen [Bericht zur Anpassungsanalyse](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) hoch. Dieser Bericht wird durch die Analyse der Anpassungs- und Erweiterungsmodelle basierend auf einem vordefinierten Satz von Regeln für bewährte Methoden generiert.

Diese Datei muss im XLS- oder XLSX-Format vorliegen. Wenn Sie über mehr als einen Bericht verfügen, können Sie eine ZIP-Datei mit allen Berichten hochladen.

### <a name="does-solution-include-localizations"></a>Umfasst die Lösung die Lokalisierungen?

Wählen Sie **Ja** aus, wenn die Lösung die Verwendung lokaler Standards und Richtlinien ermöglicht (z.B., wenn sie die verschiedenen Regeln zur Lohnbuchhaltung für verschiedene Länder/Regionen umfasst). Klicken Sie andernfalls auf **Nein**.

### <a name="does-solution-enable-translations"></a>Ermöglicht die Lösung die Übersetzungen?

Antworten Sie **Ja** , wenn der Text in Ihrer Lösung in andere Sprachen übersetzt werden kann. Klicken Sie andernfalls auf **Nein**.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="publish"></a>Veröffentlichen

### <a name="submit-offer-to-preview"></a>Übermitteln des Angebots für die Vorschau

Sobald Sie alle erforderlichen Abschnitte des Angebots ausgefüllt haben, klicken Sie rechts oben im Portal auf **Überprüfen und veröffentlichen**.

Wenn Sie das Angebot zum ersten Mal veröffentlichen, haben Sie folgende Möglichkeiten:

- Anzeigen des Abschlussstatus für die einzelnen Abschnitte des Angebots.
    - **Nicht gestartet:** Der Abschnitt wurde nicht bearbeitet und sollte ausgefüllt werden.
    - **Unvollständig:** Der Abschnitt enthält Fehler, die behoben werden müssen, oder erfordert eine Ergänzung der Informationen. Kehren Sie zu den Abschnitten zurück, und nehmen Sie eine Aktualisierung vor.
    - **Vollständig:** Der Abschnitt ist vollständig. Alle erforderlichen Daten wurden angegeben, und es sind keine Fehler vorhanden. Alle Abschnitte des Angebots müssen abgeschlossen sein, bevor Sie das Angebot einreichen können.
- Geben Sie dem Zertifizierungsteam im Abschnitt **Hinweise zur Zertifizierung** Testanweisungen, um sicherzustellen, dass Ihre App ordnungsgemäß getestet wird, sowie ergänzende Hinweise, die das Verständnis Ihrer App erleichtern. Weitere Informationen zu Testanweisungen und zum Abschließen einer ersten Veröffentlichung finden Sie unter [Funktionale Überprüfung von AppSource Dynamics 365 Finance and Operations](../dynamics-365-finance-operations-functional-validation.md).
- Senden Sie dazu das Angebot zur Veröffentlichung, indem Sie auf **Senden** klicken. Wir senden Ihnen eine E-Mail, um Ihnen mitzuteilen, wann eine Vorschauversion des Angebots verfügbar ist, damit Sie es überprüfen und genehmigen können. Kehren Sie zu Partner Center zurück, und wählen Sie **Live schalten** aus, um das Angebot für eine öffentliche Zielgruppe zu veröffentlichen.

## <a name="next-step"></a>Nächster Schritt

- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](./update-existing-offer.md)