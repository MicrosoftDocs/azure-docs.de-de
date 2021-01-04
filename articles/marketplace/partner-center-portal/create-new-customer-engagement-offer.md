---
title: Erstellen eines Angebots für Dynamics 365 for Customer Engagement & PowerApps im kommerziellen Microsoft-Marketplace
description: Erstellen eines neuen Angebots für Dynamics 365 for Customer Engagement & PowerApps für das Aufführen oder Verkaufen im Azure Marketplace, in AppSource oder über das CSP-Programm (Cloud Solution Provider) in Partner Center
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 96bdfc513023cafb2d94a68c06f1065cf20c3ba8
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130169"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Erstellen eines Angebots für Dynamics 365 for Customer Engagement und PowerApps

In diesem Thema wird erläutert, wie Sie ein neues Angebot für Dynamics 365 for Customer Engagement und PowerApps erstellen. Alle Apps für Dynamics 365 for Customer Engagement (PowerApps, Sales, Service, Project Service und Field Service) müssen unseren Zertifizierungsprozess durchlaufen und eine Testversion unterstützen. Der Zertifizierungsprozess überprüft Ihre Lösung auf Standardanforderungen, Kompatibilität und richtige Methoden. Die Testversion ermöglicht es Benutzern, Ihre Lösung in einer Dynamics 365-Liveumgebung bereitzustellen.

Bevor Sie beginnen, [erstellen Sie ein Konto im kommerziellen Marketplace in Partner Center](create-account.md), sofern Sie dies noch nicht getan haben. Vergewissern Sie sich, dass Ihr Konto im Programm „Kommerzieller Marketplace“ registriert ist.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Übersicht**.
3. Klicken Sie auf der Übersichtsseite auf **+ Neues Angebot** > **Dynamics 365 for Customer Engagement & PowerApps**.

    ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Nach der Veröffentlichung eines Angebots werden die daran im Partner Center vorgenommenen Änderungen erst nach der erneuten Veröffentlichung des Angebots in Onlineshops angezeigt. Achten Sie darauf, nach dem Vornehmen von Änderungen immer erneut zu veröffentlichen.

## <a name="new-offer"></a>Neues Angebot

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID wird für Kunden unter der Webadresse für das Marketplace-Angebot und ggf. in Azure Resource Manager-Vorlagen angezeigt.
- Die Angebots-ID darf zusammen mit der Herausgeber-ID nicht länger als 40 Zeichen lang sein.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen. Wenn die Herausgeber-ID z. B. „testpublisherid“ heißt und Sie **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird im Marketplace nicht verwendet und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
- Dies kann nach dem Auswählen von **Erstellen** nicht mehr geändert werden.

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

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

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

### <a name="applicable-dynamics-365-products"></a>Gilt für Dynamics 365-Produkte

Wählen Sie alle Dynamics 365-Produkte aus, für die dieses Angebot gilt.

### <a name="app-version"></a>App-Version

Geben Sie die Versionsnummer Ihres Angebots ein. Für Kunden wird diese Version auf der Detailseite des Angebots aufgeführt. Wenn Sie die Versionsnummer nur aufgrund von Änderungen bei Marketing/Beschreibungen aktualisieren, aktivieren Sie das Kontrollkästchen **Ausschließliche Marketingänderung**. Durch diese Option kann das Angebot die Zertifizierungs- und Bereitstellungsphase umgehen.

### <a name="terms-and-conditions"></a>Geschäftsbedingungen

Stellen Sie hier Ihre eigenen Geschäftsbedingungen bereit. Sie können auch die Adresse angeben, unter der Ihre Geschäftsbedingungen zu finden sind. Kunden müssen diese Bedingungen akzeptieren, bevor sie Ihr Angebot testen können.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="offer-listing"></a>Angebotsliste

Auf dieser Seite werden die Sprachen angezeigt, in denen Ihr Angebot aufgeführt wird. Derzeit ist **Englisch (Vereinigte Staaten)** die einzige verfügbare Option.

Definieren Sie hier für jede Sprache/jeden Markt Marketplace-Informationen (z. B. Angebotsname, Beschreibung und Bilder). Wählen Sie die Sprache bzw. den Namen des Markts aus, um diese Informationen anzugeben.

> [!NOTE]
> Die Inhalte der Angebotsliste (z. B. Beschreibung, Dokumente, Screenshots und Nutzungsbedingungen) müssen nicht in englischer Sprache vorliegen, solange die Angebotsbeschreibung mit dem folgenden Satz beginnt: „Diese Anwendung ist nur auf [nicht englische Sprache] verfügbar.“ Es ist auch zulässig, eine *Nützlicher Link-URL* bereitzustellen, um Inhalte in einer anderen Sprache als der in der Angebotsliste verwendeten anzubieten.

Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen in Microsoft AppSource angezeigt werden (die genannten Preise dienen lediglich als Beispiele und stellen keine tatsächlichen Kosten dar):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Veranschaulicht, wie dieses Angebot in Microsoft AppSource angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Logo
2. Produkte
3. Kategorien
4. Supportadresse (Link)
5. Adresse der Nutzungsbedingungen (Link)
6. Angebotsname
7. BESCHREIBUNG
8. Screenshots/Videos

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

Geben Sie die Adresse ein, unter der Kunden mehr über Ihr Angebot erfahren können.

#### <a name="privacy-policy-url"></a>URL zur Datenschutzrichtlinie

Geben Sie die Adresse der Datenschutzrichtlinie Ihrer Organisation ein. Sie müssen sicherzustellen, dass die App die Datenschutzgesetze und -bestimmungen erfüllt, und Sie müssen eine gültige Datenschutzrichtlinie bereitstellen.

### <a name="contacts"></a>Kontakte

Geben Sie den Namen, die E-Mail-Adresse und die Telefonnummer eines **Supportkontakts** und eines **technischen Ansprechpartners** an. Diese Informationen werden Kunden nicht angezeigt, sind aber für Microsoft verfügbar und werden möglicherweise an CSP-Partner weitergegeben.

Im Abschnitt **Supportkontakt** müssen Sie auch die **Support-URL** angeben, unter der CSP-Partner Support für Ihr Angebot erhalten.

### <a name="supporting-documents"></a>Unterstützende Dokumente

Geben Sie mindestens ein zugehöriges Marketingdokument (und höchstens drei) im PDF-Format an, z. B. Whitepapers, Broschüren, Checklisten oder Präsentationen.

### <a name="marketplace-images"></a>Marketplace-Bilder

Stellen Sie Logos und Bilder für Ihr Angebot bereit. Alle Bilder müssen das PNG-Format aufweisen.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
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

Auf dieser Seite können Sie definieren, wo und wie Ihr Angebot zur Verfügung gestellt werden soll.

### <a name="markets"></a>Märkte

Geben Sie die Märkte an, in denen Ihr Angebot verfügbar sein soll. Wählen Sie hierzu **Edit markets** (Märkte bearbeiten) aus. Dadurch wird das Popupfenster **Marktauswahl** angezeigt.

Standardmäßig sind keine Märkte ausgewählt, Sie müssen jedoch mindestens einen Markt auswählen, um Ihr Angebot zu veröffentlichen. Klicken Sie auf **Alle auswählen** , um Ihr Angebot in allen möglichen Märkten verfügbar zu machen, oder wählen Sie bestimmte Märkte aus, die Sie hinzufügen möchten.

Ihre hier getroffene Auswahl gilt nur für neue Käufe. Wenn Ihre App bereits von einem Benutzer in einem bestimmten Markt verwendet wird und Sie den Markt später entfernen, können Benutzer, die in diesem Markt bereits über Ihr Angebot verfügen, es weiterhin nutzen, aber Ihr Angebot ist nicht für neue Kunden in diesem Markt erhältlich.

> [!IMPORTANT]
> Es liegt in ihrer Verantwortung, alle lokalen rechtlichen Anforderungen zu erfüllen, auch wenn diese Anforderungen nicht hier oder im Partner Center aufgeführt sind. Auch wenn Sie alle Märkte auswählen, kann es sein, dass bestimmte Angebote in einigen Ländern und Regionen aufgrund lokaler Gesetze und Einschränkungen oder anderer Faktoren nicht gelistet werden.

### <a name="preview-audience"></a>Vorschauzielgruppe

Bevor Sie Ihr Angebot im Marketplace für die breite Öffentlichkeit live schalten, müssen Sie es zunächst für eine begrenzte **Vorschauzielgruppe** verfügbar machen. Geben Sie hier einen **Hide-Schlüssel** (eine beliebige Zeichenfolge, die nur Kleinbuchstaben und/oder Ziffern enthält) ein. Mitglieder Ihrer Vorschauzielgruppe können diesen Hide-Schlüssel als Token verwenden, um eine Vorschau Ihres Angebots im Marketplace anzuzeigen.

Wenn Sie anschließend bereit sind, Ihr Angebot verfügbar zu machen und die Vorschaueinschränkung zu entfernen, müssen Sie den **Hide-Schlüssel** entfernen und das Angebot erneut veröffentlichen.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="technical-configuration"></a>Technische Konfiguration

Auf dieser Seite werden die technischen Informationen definiert, die für das Herstellen einer Verbindung mit Ihrem Angebot verwendet werden. Durch diese Verbindung können wir Ihr Angebot für den Endkunden bereitstellen, wenn er es kaufen möchte.

### <a name="base-license-model"></a>Basislizenzmodell

Das Basislizenzmodell bestimmt, wie Kunden im CRM Admin Center Ihrer Anwendung zugewiesen werden. Wählen Sie **Ressource** für instanzbasierte Lizenzierung bzw. **Benutzer** aus, wenn Lizenzen pro Mandant zugewiesen werden.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Erfordert ausgehenden S2S- und CRM Secure Store-Zugriff

Aktivieren Sie dieses Kontrollkästchen, um die Konfiguration von CRM Secure Store- oder ausgehendem Server-zu-Server-Zugriff (S2S) zu ermöglichen. Dieses Feature muss während der Zertifizierungsphase besonders vom Dynamics 365-Team berücksichtigt werden. Microsoft kontaktiert Sie, um zusätzliche Schritte ausführen, die dieses Feature unterstützen.

### <a name="application-configuration-url"></a>Anwendungskonfigurations-URL

Geben Sie die URL für die Konfigurationswebseite an, über die der Kunde Ihre APP konfigurieren kann.

### <a name="crm-package"></a>CRM-Paket

Geben Sie im Feld **URL Ihres Paketspeicherorts** die URL eines Azure Blob Storage-Kontos ein, das die hochgeladene ZIP-Datei des CRM-Pakets enthält. Schließen Sie einen schreibgeschützten SAS-Schlüssel in die URL ein, damit Microsoft Ihr Paket zur Überprüfung öffnen kann.

> [!IMPORTANT]
> Um eine Veröffentlichungssperre zu vermeiden, stellen Sie sicher, dass das Ablaufdatum in der URL Ihres Blobspeichers nicht abgelaufen ist. Sie können das Datum überarbeiten, indem Sie auf Ihre Richtlinie zugreifen. Es wird empfohlen, den **Ablaufzeitpunkt** mindestens einen Monat in der Zukunft anzusetzen.

Aktivieren Sie ggf. das Kontrollkästchen **In der Paketdatei sind mehrere CRM-Pakete enthalten**. Wenn dies der Fall ist, stellen Sie sicher, dass Sie alle Pakete in die ZIP-Datei einschließen.

Ausführliche Informationen zum Erstellen des Pakets und zum Aktualisieren seiner Struktur finden Sie in [Schritt 3: Erstellen eines AppSource-Pakets für Ihre App](/powerapps/developer/common-data-service/create-package-app-appsource).

### <a name="crm-package-availability"></a>Verfügbarkeit von CRM-Paketen

Wählen Sie in diesem Abschnitt **+ Region hinzufügen** aus, um die geografischen Regionen anzugeben, in denen Ihr CRM-Paket für Kunden verfügbar sein wird. Die Bereitstellung in den folgenden unabhängigen Regionen erfordert eine spezielle Berechtigung und Überprüfung während des Zertifizierungsprozesses: [Deutschland](../../germany/index.yml), [Cloud for US Government](../../azure-government/documentation-government-welcome.md) und TIP.

Standardmäßig wird die zuvor eingegebene **Anwendungskonfigurations-URL** für jede Region verwendet. Wenn Sie möchten, können Sie eine separate Anwendungskonfigurations-URL für eine oder mehrere bestimmte Regionen eingeben. 

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="test-drive-technical-configuration"></a>Technische Konfiguration der Testversion

Auf dieser Seite können Sie eine Demonstration („Testversion“) einrichten, mit der Kunden Ihr Angebot vor dem Erwerb ausprobieren können. Weitere Informationen finden Sie unter [Was ist die Testversion?](../what-is-test-drive.md).

Aktivieren Sie auf der Registerkarte [Angebotseinrichtung](#test-drive) das Kontrollkästchen **Testversion aktivieren** , um eine Testversion zu aktivieren. Deaktivieren Sie dieses Kontrollkästchen, um die Testversion aus Ihrem Angebot zu entfernen.

Sobald Sie die Einrichtung der Testversion abgeschlossen haben, wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="supplemental-content"></a>Ergänzender Inhalt

Auf dieser Seite können Sie zusätzliche Informationen zu Ihrem Angebot bereitstellen, um uns bei der Validierung Ihres Angebots zu helfen. Diese Informationen werden weder den Kunden angezeigt noch im Marketplace veröffentlicht.

### <a name="key-usage-scenario"></a>Wichtige Verwendungsszenarien

Laden Sie eine PDF-Datei hoch, in der die wichtigsten Verwendungsszenarios für Ihr Angebot aufgeführt sind. Alle Szenarios werden möglicherweise von unserem Überprüfungsteam überprüft, bevor wir Ihr Angebot für den Marketplace genehmigen.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="publish"></a>Veröffentlichen

### <a name="submit-offer-to-preview"></a>Übermitteln des Angebots für die Vorschau

Wenn Sie alle erforderlichen Abschnitte des Angebots ausgefüllt haben, klicken Sie rechts oben im Portal auf **Review and publish** (Überprüfen und veröffentlichen).

Wenn Sie das Angebot zum ersten Mal veröffentlichen, haben Sie folgende Möglichkeiten:

- Anzeigen des Abschlussstatus für die einzelnen Abschnitte des Angebots.
    - **Nicht gestartet:** Der Abschnitt wurde nicht bearbeitet und sollte ausgefüllt werden.
    - **Unvollständig:** Der Abschnitt enthält Fehler, die behoben werden müssen, oder erfordert eine Ergänzung der Informationen. Kehren Sie zu den Abschnitten zurück, und nehmen Sie eine Aktualisierung vor.
    - **Vollständig:** Der Abschnitt ist vollständig. Alle erforderlichen Daten wurden angegeben, und es sind keine Fehler vorhanden. Alle Abschnitte des Angebots müssen abgeschlossen sein, bevor Sie das Angebot einreichen können.
- Geben Sie dem Zertifizierungsteam im Abschnitt **Hinweise zur Zertifizierung** Testanweisungen, um sicherzustellen, dass Ihre App ordnungsgemäß getestet wird, sowie ergänzende Hinweise, die das Verständnis Ihrer App erleichtern.
- Senden Sie dazu das Angebot zur Veröffentlichung, indem Sie auf **Senden** klicken. Wir senden Ihnen eine E-Mail, um Ihnen mitzuteilen, wann eine Vorschauversion des Angebots verfügbar ist, damit Sie es überprüfen und genehmigen können. Kehren Sie zu Partner Center zurück, und wählen Sie für das Angebot **Live schalten** aus, um es zu veröffentlichen.

## <a name="next-step"></a>Nächster Schritt

- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](./update-existing-offer.md)