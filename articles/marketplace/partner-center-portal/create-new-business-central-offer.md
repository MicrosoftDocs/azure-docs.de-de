---
title: Erstellen eines Angebots für Dynamics 365 Business Central im kommerziellen Microsoft-Marketplace
description: Hier lernen Sie die Schritte und Überlegungen zum Erstellen eines neuen Angebots für Dynamics 365 Business Central im kommerziellen Microsoft-Marketplace im Partner Center kennen. Sie können Ihr Angebot im Azure Marketplace oder über das CSP-Programm (Cloud Solution Provider) anbieten oder verkaufen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: 71cbc09f21e21c325665809953f209615d835124
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130152"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Erstellen eines Angebots für Dynamics 365 Business Central

In diesem Artikel wird erläutert, wie Sie ein neues Angebot für Dynamics 365 Business Central erstellen. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) ist ein ERP-System (Enterprise Resource Planning), mit dem eine Vielzahl von Geschäftsprozessen verarbeitet werden kann, z. B. für Finanzierung, operative Vorgänge, Lieferketten-, CRM- und Projektmanagement sowie E-Commerce. Premium-Pakete unterstützen außerdem das klassische Bereitstellungsmodell und die Fertigung. Alle Angebote für Dynamics 365 Business Central müssen unseren Zertifizierungsprozess durchlaufen.

Bevor Sie beginnen, [erstellen Sie ein Konto im kommerziellen Marketplace in Partner Center](create-account.md), sofern Sie dies noch nicht getan haben. Vergewissern Sie sich, dass Ihr Konto im Programm „Kommerzieller Marketplace“ registriert ist.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Übersicht**.
3. Klicken Sie auf der Übersichtsseite auf **+ Neues Angebot** > **Dynamics 365 Business Central**.

    ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Nach der Veröffentlichung eines Angebots werden die daran in Partner Center vorgenommenen Änderungen erst nach der erneuten Veröffentlichung des Angebots in Onlineshops angezeigt. Achten Sie darauf, nach dem Vornehmen von Änderungen immer erneut zu veröffentlichen.

## <a name="new-offer"></a>Neues Angebot

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID wird für Kunden unter der Webadresse für das Marketplace-Angebot und ggf. in Azure Resource Manager-Vorlagen angezeigt.
- Die Angebots-ID darf zusammen mit der Herausgeber-ID nicht länger als 40 Zeichen lang sein.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen. Wenn die Herausgeber-ID z. B. „testpublisherid“ heißt und Sie hier **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird im Marketplace nicht verwendet und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
- Nachdem Sie **Erstellen** ausgewählt haben, kann der Angebotsalias nicht mehr geändert werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

## <a name="offer-setup"></a>Angebotseinrichtung

Führen Sie die unten angegebenen Schritte aus, um Ihr Angebot einzurichten.

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

Von Dynamics 365 Business Central-Angeboten wird derzeit keine Testversion unterstützt. Deaktivieren Sie das Kontrollkästchen **Testversion aktivieren** , um die Testversion aus Ihrem Angebot zu entfernen.

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

Auf der Seite können Sie Details für Ihr Angebot definieren, z. B. den Namen, eine Beschreibung und Bilder.

> [!NOTE]
> Die Details eines Angebotseintrags können nur in einer Sprache angegeben werden. Eine Angabe in englischer Sprache ist nicht erforderlich, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt. Es ist auch zulässig, eine *Hilfelink-URL* bereitzustellen, um Inhalte in einer anderen als der im Angebotseintrag verwendeten Sprache anzubieten.

Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen in Microsoft AppSource angezeigt werden (die aufgelisteten Preise dienen lediglich als Beispiele und stellen keine tatsächlichen Kosten dar):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Veranschaulicht, wie dieses Angebot in Microsoft AppSource angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Logo
2. Produkte
3. Kategorien
4. Supportadresse (Link)
5. Nutzungsbedingungen
6. Datenschutzrichtlinie
7. Angebotsname
8. Zusammenfassung
9. BESCHREIBUNG
10. Screenshots/Videos

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

### <a name="helpprivacy-urls"></a>Hilfe-/Datenschutz-URLs

In diesem Abschnitt können Sie Links bereitstellen, die Kunden dabei helfen, mehr über Ihr Angebot zu erfahren.

#### <a name="help-link"></a>Hilfelink

Geben Sie die URL ein, unter der Kunden mehr über Ihr Angebot erfahren können. Ihr **Hilfelink** und Ihre **Support-URL** dürfen nicht identisch sein (siehe unten).

#### <a name="privacy-policy-link"></a>Link zur Datenschutzrichtlinie

Geben Sie die URL zur Datenschutzrichtlinie Ihrer Organisation ein. Sie müssen sicherzustellen, dass die App die Datenschutzgesetze und -bestimmungen erfüllt, und Sie müssen eine gültige Datenschutzrichtlinie bereitstellen.

### <a name="contact-information"></a>Kontaktinformationen

In diesem Abschnitt müssen Sie den Namen, die E-Mail-Adresse und die Telefonnummer für einen **Supportkontakt** und einen **Engineering-Kontakt** angeben. Diese Informationen werden Kunden nicht angezeigt, sind aber für Microsoft verfügbar und können CSP-Partnern bereitgestellt werden.

Im Abschnitt **Supportkontakt** müssen Sie auch die **Support-URL** angeben, unter der CSP-Partner Support für Ihr Angebot erhalten. Ihre Support-URL und Ihr **Hilfelink** dürfen nicht identisch sein.

### <a name="supporting-documents"></a>Unterstützende Dokumente

Geben Sie mindestens ein (und höchstens drei) zugehörige Marketingdokumente an, z. B. Whitepaper, Broschüren, Checklisten oder Präsentationen. Diese Dokumente müssen im PDF-Format vorliegen.

### <a name="marketplace-images"></a>Marketplace-Bilder

Geben Sie eine PNG-Datei für das **große** Logo an. Dies wird in Partner Center verwendet, um ein **kleines** Logo zu erstellen. Später können Sie dieses Logo optional durch ein anderes Bild ersetzen.

- **Groß** (von 216 x 216 bis 350 x 350 px, erforderlich)
- **Klein** (48 x 48 px, optional)

Diese Logos werden an unterschiedlichen Stellen in der Auflistung verwendet:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Wenn beim Hochladen von Dateien ein Problem auftritt, vergewissern Sie sich, dass das lokale Netzwerk den von Partner Center verwendeten Dienst `https://upload.xboxlive.com` nicht blockiert.

#### <a name="screenshots"></a>Screenshots

Fügen Sie Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Mindestens drei Screenshots sind erforderlich, und Sie können bis zu fünf hinzufügen. Alle Screenshots müssen in der Größe 1280 x 720 Pixel vorliegen.

#### <a name="videos"></a>Videos

Optional können Sie bis zu fünf Videos hinzufügen, die Ihr Angebot veranschaulichen. Diese Videos sollten auf YouTube und/oder Vimeo gehostet werden. Geben Sie für jedes Video den Namen, seine URL und ein Miniaturbild (1280 x 720 Pixel) ein.

#### <a name="additional-marketplace-listing-resources"></a>Zusätzliche Ressourcen für Marketplace-Listen

[Bewährte Methoden für Angebotslistung](../gtm-offer-listing-best-practices.md)

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="availability"></a>Verfügbarkeit

Auf dieser Seite finden Sie Optionen, mit denen Sie festlegen können, wo und wie Sie Ihr Angebot zur Verfügung stellen.

### <a name="markets"></a>Märkte

In diesem Abschnitt können Sie angeben, in welchen Märkten das Angebot verfügbar sein soll. Wählen Sie hierzu **Edit markets** (Märkte bearbeiten) aus. Dadurch wird das Popupfenster **Marktauswahl** angezeigt.

Wählen Sie mindestens einen Markt aus, um Ihr Angebot zu veröffentlichen. Klicken Sie auf **Alle auswählen** , um Ihr Angebot in allen möglichen Märkten verfügbar zu machen, oder wählen Sie bestimmte Märkte aus, die Sie hinzufügen möchten.

Ihre hier getroffene Auswahl gilt nur für neue Käufe. Wenn Ihre App bereits von einem Benutzer in einem bestimmten Markt verwendet wird und Sie den Markt später entfernen, können Benutzer, die in diesem Markt bereits über Ihr Angebot verfügen, es weiterhin nutzen, aber Ihr Angebot ist nicht für neue Kunden in diesem Markt erhältlich.

> [!IMPORTANT]
> Es liegt in ihrer Verantwortung, alle lokalen rechtlichen Anforderungen zu erfüllen, auch wenn diese Anforderungen nicht hier oder im Partner Center aufgeführt sind.

Beachten Sie Folgendes: Auch wenn Sie alle Märkte auswählen, kann es sein, dass bestimmte Angebote in einigen Ländern und Regionen aufgrund lokaler Gesetze und Einschränkungen oder anderer Faktoren nicht gelistet werden.

### <a name="preview-audience"></a>Vorschauzielgruppe

Bevor Sie Ihr Angebot im Marketplace für die breite Öffentlichkeit live schalten, müssen Sie es zunächst für eine begrenzte **Vorschauzielgruppe** verfügbar machen. Geben Sie hier einen **Hide-Schlüssel** (eine beliebige Zeichenfolge, die nur Kleinbuchstaben und/oder Ziffern enthält) ein. Mitglieder Ihrer Vorschauzielgruppe können diesen Hide-Schlüssel als Token verwenden, um eine Vorschau Ihres Angebots im Marketplace anzuzeigen.

Wenn Sie anschließend bereit sind, Ihr Angebot verfügbar zu machen und die Vorschaueinschränkung zu entfernen, müssen Sie den **Hide-Schlüssel** entfernen und das Angebot erneut veröffentlichen.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="technical-configuration"></a>Technische Konfiguration

Auf dieser Seite werden die technischen Informationen definiert, die für das Herstellen einer Verbindung mit Ihrem Angebot verwendet werden. Durch diese Verbindung können wir Ihr Angebot für den Endkunden bereitstellen, wenn er es kaufen möchte.

### <a name="package-type"></a>Pakettyp

Wählen Sie die Option aus, die für Ihr Angebot gilt:

* **Add-On** : Eine Add-On-App erweitert die Oberfläche und die vorhandene Funktionalität von Dynamics 365 Business Central. Weitere Informationen finden Sie unter [Add-On-Apps](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Connect** : Eine Connect-App kann in Szenarios verwendet werden, in denen eine Point-to-Point-Verbindung zwischen Dynamics 365 Business Central und einer Drittanbieterlösung oder einem Drittanbieterdienst hergestellt werden muss. Weitere Informationen finden Sie unter [Connect-Apps](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Dateiupload

Wenn Sie oben **Add-On** ausgewählt haben, laden Sie dort die Paketdatei Ihres Angebots zusammen mit den Paketdateien für alle Erweiterungen hoch, für die sie Abhängigkeiten aufweist.

#### <a name="extensions-package-file"></a>Erweiterungspaketdatei

Laden Sie die Erweiterungspaketdatei (APP-Datei) für Ihr Angebot hoch.

#### <a name="library-package-file"></a>Bibliothekspaketdatei

Erforderlich, wenn Ihr Angebot zusammen mit einer anderen Erweiterung installiert werden muss, die nicht im Marketplace veröffentlicht wird. Wenn dies der Fall ist, laden Sie deren APP-Datei hier hoch.

>[!NOTE]
>Die Abhängigkeitspaketdatei wird nicht mehr verwendet. Laden Sie stattdessen eine Bibliothekspaketdatei hoch.

### <a name="url-to-app-installation"></a>URL zur App-Installation

Wenn Sie oben **Connect** ausgewählt haben, geben Sie hier die URL-Adresse für Ihre App-Installation an. Bei verbundenen Diensten, die keine Installation erfordern, geben Sie die URL-Adresse für die Angebotsseite oder Registrierungsseite Ihres Diensts an.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="test-drive-technical-configuration"></a>Technische Konfiguration der Testversion

Auf dieser Seite können Sie eine Demonstration („Testversion“) einrichten, mit der Kunden Ihr Angebot vor dem Erwerb ausprobieren können. Weitere Informationen finden Sie unter [Was ist die Testversion?](../what-is-test-drive.md).

Aktivieren Sie auf der Registerkarte [Angebotseinrichtung](#test-drive) das Kontrollkästchen **Testversion aktivieren** , um eine Testversion zu aktivieren. Deaktivieren Sie dieses Kontrollkästchen, um die Testversion aus Ihrem Angebot zu entfernen.

Sobald Sie die Einrichtung der Testversion abgeschlossen haben, wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="supplemental-content"></a>Ergänzender Inhalt

Auf dieser Seite können Sie zusätzliche Informationen zu Ihrem Angebot bereitstellen, um uns bei der Validierung Ihres Angebots zu helfen. Diese Informationen werden weder den Kunden angezeigt noch im Marketplace veröffentlicht.

### <a name="target-release"></a>Zielrelease

Geben Sie an, auf welches Release von Microsoft Dynamics Business Central Ihre Lösung abzielt: die **aktuelle Version** , die **nächste Hauptversion** oder die **nächste Nebenversion**. Diese Informationen ermöglichen es uns, Ihre Lösung entsprechend zu testen.

### <a name="supported-editions"></a>Unterstützte Editionen

Wenn Ihr Angebot die Premium Edition von Microsoft Dynamics 365 Business Central erfordert, wählen Sie nur **Premium** aus. Wählen Sie andernfalls sowohl **Essentials** als auch **Premium** aus.

### <a name="key-usage-scenario"></a>Wichtige Verwendungsszenarien

Sie müssen eine PDF-Datei hochladen, in der die wichtigsten Verwendungsszenarien für Ihr Angebot in einem Dokument (PDF-Format) aufgelistet sind. Alle hier aufgeführten Szenarien können von unserem Validierungsteam überprüft werden, bevor wir Ihr Angebot für den Marketplace genehmigen.

### <a name="app-tests-automation"></a>App-Testautomatisierung

Wenn es sich bei Ihrem Angebot um eine Add-On-App handelt, müssen Sie eine **App-Testautomatisierungsdatei** (.app) hochladen. Diese Datei ist für Connect-Apps nicht anwendbar.

### <a name="test-accounts"></a>Testkonten

Wenn unser Zertifizierungsteam ein Testkonto benötigt, um Ihr Angebot ordnungsgemäß zu überprüfen, laden Sie eine PDF-, DOC- oder DOCX-Datei mit den Informationen zu Ihren **Testkonten** hoch.

## <a name="publish"></a>Veröffentlichen

### <a name="submit-offer-to-preview"></a>Übermitteln des Angebots für die Vorschau

Nachdem Sie alle erforderlichen Abschnitte des Angebots abgeschlossen haben, klicken Sie rechts oben im Portal auf **Veröffentlichen**. Sie werden zur Seite **Review and publish** (Überprüfen und veröffentlichen) weitergeleitet. 

Wenn Sie das Angebot zum ersten Mal veröffentlichen, haben Sie folgende Möglichkeiten:

- Anzeigen des Abschlussstatus für die einzelnen Abschnitte des Angebots.
    - *Nicht gestartet:* Der Abschnitt wurde nicht bearbeitet und muss abgeschlossen werden.
    - *Unvollständig:* Der Abschnitt enthält Fehler, die behoben werden müssen, oder erfordert eine Ergänzung der Informationen. Kehren Sie zu den Abschnitten zurück, und nehmen Sie eine Aktualisierung vor.
    - *Abgeschlossen:* Der Abschnitt ist abgeschlossen. Alle erforderlichen Daten wurden angegeben, und es sind keine Fehler vorhanden. Alle Abschnitte des Angebots müssen abgeschlossen sein, bevor Sie das Angebot einreichen können.
- Geben Sie dem Zertifizierungsteam im Abschnitt **Hinweise zur Zertifizierung** Testanweisungen, um sicherzustellen, dass Ihre App ordnungsgemäß getestet wird, sowie ergänzende Hinweise, die das Verständnis Ihrer App erleichtern.
- Senden Sie dazu das Angebot zur Veröffentlichung, indem Sie auf **Senden** klicken. Wir senden Ihnen eine E-Mail, wenn eine Vorschauversion des Angebots verfügbar ist, damit Sie es überprüfen und genehmigen können. Kehren Sie zu Partner Center zurück, und wählen Sie **Live schalten** aus, um das Angebot für eine öffentliche Zielgruppe zu veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](./update-existing-offer.md)