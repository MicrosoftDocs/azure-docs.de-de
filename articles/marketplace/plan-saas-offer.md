---
title: Planen eines SaaS-Angebots im kommerziellen Marketplace von Microsoft
description: Hier finden Sie Informationen zum Planen eines neuen Software-as-a-Service-Angebots (SaaS), das in Microsoft AppSource, Azure Marketplace oder über das CSP-Programm (Cloud Solution Provider) des kommerziellen Marketplace im Microsoft Partner Center aufgelistet oder verkauft werden kann.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: 5f7278a688147842b7dbfab79a20afea2b4a9288
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621602"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Planen eines SaaS-Angebots im kommerziellen Marketplace

In diesem Artikel werden die verschiedenen Optionen und Anforderungen zum Veröffentlichen eines SaaS-Angebots (Software-as-a-Service) für den kommerziellen Marketplace von Microsoft erläutert. Mit SaaS-Angeboten können Sie Softwarelösungen für Ihre Kunden über ein Onlineabonnement bereitstellen und lizenzieren, statt sie lokal auf den einzelnen Computern zu installieren. Dieser Artikel hilft Ihnen, Ihr Angebot für die Veröffentlichung auf dem kommerziellen Marketplace mit dem Partner Center vorzubereiten.

## <a name="listing-options"></a>Auflistungsoptionen

Wenn Sie die Veröffentlichung eines neuen SaaS-Angebots vorbereiten, müssen Sie sich für eine _Auflistungs_-Option entscheiden. Die von Ihnen ausgewählte Auflistungsoption bestimmt, welche zusätzlichen Informationen Sie bei der Erstellung Ihres Angebots im Partner Center bereitstellen müssen. Sie definieren Ihre Auflistungsoption auf der Seite **Angebotseinrichtung**, wie unter [Erstellen eines SaaS-Angebots im kommerziellen Marketplace](create-new-saas-offer.md) erläutert.

In der folgenden Tabelle sind die Auflistungsoptionen für SaaS-Angebote im kommerziellen Marketplace aufgeführt.

| Auflistungsoption | Transaktionsprozess |
| ------------ | ------------- |
| Kontakt mit mir aufnehmen | Der Kunde kontaktiert Sie direkt über die in Ihrer Auflistung enthaltenen Informationen.``*`` |
| Kostenlose Testversion | Der Kunde wird über Azure Active Directory (Azure AD) an Ihre Ziel-URL umgeleitet.``*`` |
| Jetzt abrufen (kostenlos) | Der Kunde wird über Azure AD an Ihre Ziel-URL umgeleitet.``*`` |
| Über Microsoft verkaufen  | Über Microsoft verkaufte Angebote werden als _transaktionsfähige_ Angebote bezeichnet. Bei einem transaktionsfähigen Angebot handelt es sich um ein Angebot, bei dem Microsoft den Geldfluss für eine Softwarelizenz im Namen des Herausgebers vereinfacht. Wir berechnen SaaS-Angebote mit dem von Ihnen gewählten Preismodell und verwalten Kundentransaktionen in Ihrem Namen. Die Nutzungsgebühren für Azure-Infrastruktur werden Ihnen als Partner direkt in Rechnung gestellt. Deshalb sollten Sie die Infrastrukturkosten in Ihrem Preismodell berücksichtigen. Dies wird im Abschnitt [SaaS-Abrechnung](#saas-billing) weiter unten ausführlich erläutert.  |
|||

``*`` Herausgeber sind für die Unterstützung aller Aspekte der Softwarelizenztransaktion verantwortlich, einschließlich, aber nicht beschränkt auf Auftrag, Auftragsabwicklung, Messung, Abrechnung, Rechnungsstellung, Zahlung und Inkasso.

Weitere Informationen zu diesen Auflistungsoptionen finden Sie unter [Transaktionsfunktionen im kommerziellen Marketplace](marketplace-commercial-transaction-capabilities-and-considerations.md).

Nachdem das Angebot veröffentlicht wurde, wird die von Ihnen gewählte Auflistungsoption als Schaltfläche in der oberen linken Ecke der Auflistungsseite Ihres Angebots angezeigt. Der folgende Screenshot zeigt z. B. die Auflistungsseite eines Angebots im Azure Marketplace mit den Schaltflächen **Kontakt mit mir aufnehmen** und **Testversion**.

![Veranschaulicht ein im Onlineshop aufgelistetes Angebot.](./media/listing-options.png)

## <a name="technical-requirements"></a>Technische Anforderungen

Die technischen Anforderungen richten sich nach der Auflistungsoption, die Sie für Ihr Angebot auswählen.

Für die Auflistungsoption _Kontakt mit mir aufnehmen_ gelten keine technischen Anforderungen. Sie haben die Möglichkeit, ein CRM-System (Customer Relationship Management) zum Verwalten von Kundenleads anzubinden. Dies wird im Abschnitt [Kundenleads](#customer-leads) weiter unten in diesem Artikel beschrieben.

Für die Auflistungsoptionen _Jetzt abrufen (kostenlos)_ , _Kostenlose Testversion_ und _Über Microsoft verkaufen_ gelten die folgenden technischen Anforderungen:

- Ihre SaaS-Anwendung muss eine mehrinstanzenfähige Lösung sein.
- Zum Authentifizieren von Benutzern können Sie sowohl Microsoft-Konten (MSA) als auch [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) aktivieren.
- Sie müssen eine Landing Page erstellen. Nachdem ein Benutzer das Angebot erworben hat, wird er auf die Landing Page umgeleitet. Dort können alle erforderlichen zusätzlichen Bereitstellungs- oder Einrichtungsschritte durchgeführt werden. Anleitungen zum Erstellen der Landing Page finden Sie in den folgenden Artikeln:
  - [Erstellen der Landing Page für Ihr transaktionsfähiges SaaS-Angebot im kommerziellen Marketplace](azure-ad-transactable-saas-landing-page.md)
  - [Erstellen der Landing Page für Ihr kostenloses SaaS-Angebot oder Ihr SaaS-Testangebot im kommerziellen Marketplace](azure-ad-free-or-trial-landing-page.md)

Diese zusätzlichen technischen Anforderungen gelten nur für die Auflistungsoption _Über Microsoft verkaufen_ (transaktionsfähig):

- Azure AD mit SSO-Identitätsverwaltung (einmaliges Anmelden) und Authentifizierung ist für den kaufenden Benutzer erforderlich, der auf die Angebotsseite zugreift. Ausführliche Informationen finden Sie unter [Azure AD und transaktionsfähige SaaS-Angebote im kommerziellen Marketplace](azure-ad-saas.md).
- Zur Integration des Angebots in Azure Marketplace und Microsoft AppSource müssen Sie die [SaaS-Fulfillment-APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md) verwenden. Sie müssen einen Dienst bereitstellen, der mit dem SaaS-Abonnement interagiert, um Benutzerkonten und Servicepläne erstellen, aktualisieren und löschen zu können. Wichtige API-Änderungen müssen innerhalb von 24 Stunden unterstützt werden. Weniger wichtige API-Änderungen werden regelmäßig veröffentlicht. Diagramme und ausführliche Erläuterungen zur Verwendung der gesammelten Felder finden Sie in der Dokumentation zu den [APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- Sie müssen mindestens einen Plan für Ihr Angebot erstellen. Der Plan für Ihr Angebot basiert auf dem Preismodell, das Sie vor der Veröffentlichung ausgewählt haben: _Pauschalgebühr_ oder _Pro Benutzer_. Weitere Details zu [Plänen](#plans) finden Sie weiter unten in diesem Artikel.
- Der Kunde kann Ihr Angebot jederzeit stornieren.

### <a name="technical-information"></a>Technische Informationen

Wenn Sie ein transaktionsfähiges Angebot erstellen, müssen Sie die folgenden Informationen für die Seite **Technische Konfiguration** sammeln. Wenn Sie Transaktionen unabhängig voneinander verarbeiten möchten, anstatt ein transaktionsfähiges Angebot zu erstellen, überspringen Sie diesen Abschnitt, und wechseln Sie zu [Testversionen](#test-drives).

- **URL der Angebotsseite**: Die URL der SaaS-Website (z. B. `https://contoso.com/signup`), zu der Benutzer umgeleitet werden, nachdem sie Ihr Angebot im kommerziellen Marketplace erworben und den Konfigurationsvorgang vom neu erstellten SaaS-Abonnement aus gestartet haben. An diese URL wird ein Token gesendet, das zum Aufrufen der Fulfillment-APIs verwendet werden kann. So lassen sich Bereitstellungsdetails für die interaktive Registrierungsseite abrufen.

  Die URL wird mit dem Identifizierungstokenparameter für den Marketplace-Kauf aufgerufen, der den SaaS-Kauf eines bestimmten Kunden eindeutig identifiziert. Sie müssen dieses Token mithilfe der [Auflösungs-API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) durch die entsprechenden SaaS-Abonnementdetails ersetzen. Diese und alle anderen Daten, die Sie sammeln möchten, sollten als Teil einer interaktiven, in Ihre Benutzeroberfläche integrierten Webseite für Kunden verwendet werden, wo sie die Kundenregistrierung abschließen und den Kauf aktivieren können. Auf dieser Seite sollte sich der Benutzer per 1-Klick-Authentifizierung über Azure Active Directory (Azure AD) registrieren.

  Die URL mit dem Identifizierungstokenparameter für den Marketplace-Kauf wird auch aufgerufen, wenn der Kunde die Oberfläche für verwaltete SaaS-Lösungen über das Azure-Portal oder das M365 Admin Center startet. Die beiden folgenden Abläufe sollten unterstützt werden: Wenn das Token nach dem Kauf erstmalig für neue Kunden bereitgestellt wird und wenn es für vorhandene Kunden erneut zum Verwalten der SaaS bereitgestellt wird.

    Die konfigurierte Landing Page sollte rund um die Uhr aktiv sein. Dies ist die einzige Möglichkeit, Sie über neue Käufe Ihrer SaaS-Angebote im kommerziellen Marketplace oder über Konfigurationsanforderungen eines aktiven Abonnements für ein Angebot zu benachrichtigen.

- **Verbindungswebhook**: Für alle asynchronen Ereignisse, die Microsoft an Sie senden muss (etwa, wenn das SaaS-Abonnement gekündigt wurde), muss eine Verbindungswebhook-URL angegeben werden. Diese URL wird aufgerufen, um Sie über das Ereignis zu informieren.

  Der von Ihnen bereitgestellte Webhook sollte rund um die Uhr aktiv sein. Dies ist die einzige Möglichkeit, Sie über Aktualisierungen der SaaS-Abonnements Ihrer Kunden, die über den kommerziellen Marketplace erworben wurden, zu benachrichtigen.

  > [!NOTE]
  > Innerhalb des Azure-Portals muss eine [Azure Active Directory-App](../active-directory/develop/howto-create-service-principal-portal.md) mit nur einem Mandanten erstellt werden, damit die Verbindung zwischen unseren beiden Diensten durch eine Azure-App-ID authentifiziert werden kann. Um die [Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) zu ermitteln, wechseln Sie zu Azure Active Directory, wählen Sie **Eigenschaften** aus, und suchen Sie nach der aufgeführten Verzeichnis-ID-Nummer. Beispiel: `50c464d3-4930-494c-963c-1e951d15360e`.

- **ID Ihres Azure Active Directory-Mandanten**: (auch bekannt als Verzeichnis-ID). Innerhalb des Azure-Portals muss eine [Azure Active Directory (AD)-App registriert werden](../active-directory/develop/howto-create-service-principal-portal.md), damit wir sie der Zugriffssteuerungsliste (Access Control List, ACL) der API hinzufügen und so sicherstellen können, dass Sie für den Aufruf autorisiert sind. Um die Mandanten-ID für Ihre Azure Active Directory-App (AD) zu ermitteln, rufen Sie in Azure Active Directory das Blatt [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) auf. Wählen Sie in der Spalte **Anzeigename** die App aus. Suchen Sie dann nach der aufgeführten Nummer für **Verzeichnis-ID (Mandant)** (z. B. `50c464d3-4930-494c-963c-1e951d15360e`).

- **ID der Azure Active Directory-Anwendung**: Sie benötigen auch Ihre [Anwendungs-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Um den Wert abzurufen, navigieren Sie in Azure Active Directory zum Blatt [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade). Wählen Sie in der Spalte **Anzeigename** die App aus. Suchen Sie dann nach der aufgeführten Nummer für die Anwendungs-ID (Client) wie z. B. `50c464d3-4930-494c-963c-1e951d15360e`.

  Die ID der Azure AD-Anwendung ist der Herausgeber-ID in Ihrem Partner Center-Konto zugeordnet. Für alle Angebote unter diesem Konto muss die gleiche Anwendungs-ID verwendet werden.

  > [!NOTE]
  > Wenn der Herausgeber über zwei oder mehr unterschiedliche Konten im Partner Center verfügt, sollten auch zwei oder mehr unterschiedliche Azure AD-App-IDs verwendet werden – jeweils eine für eines der Konten. Jedes Partnerkonto im Partner Center sollte eine eindeutige Azure AD-App-ID für alle SaaS-Angebote verwenden, die über dieses Konto veröffentlicht werden.

## <a name="test-drives"></a>Testversionen
Sie können eine Testversion für Ihre SaaS-App aktivieren. Über Testversionen können Kunden für eine feste Anzahl von Stunden auf eine vorkonfigurierte Umgebung zugreifen. Sie können Testversionen für beliebige Veröffentlichungsoptionen aktivieren, allerdings gelten für dieses Feature zusätzliche Anforderungen. Weitere Informationen zu Testversionen finden Sie unter [Was ist eine Testversion?](what-is-test-drive.md) Weitere Informationen zum Konfigurieren verschiedener Arten von Testversionen finden Sie unter [Technische Konfiguration der Testversion](test-drive-technical-configuration.md).

> [!TIP]
> Eine Testversion unterscheidet sich von einer [kostenlosen Testversion](plans-pricing.md#free-trials). Sie können entweder eine Testversion, eine kostenlose Testversion oder beides anbieten. Mit beiden Optionen können Kunden Ihre Lösung für einen festen Zeitraum nutzen. Eine Testversion umfasst jedoch auch eine praktische, selbstgesteuerte Tour durch die wichtigsten Features und Vorteile Ihres Produkts, die in einem praxisnahen Implementierungsszenario veranschaulicht werden.

## <a name="customer-leads"></a>Kundenleads

Um Kundeninformationen zu sammeln, müssen Sie Ihr Angebot mit Ihrem CRM-System (Customer Relationship Management) verbinden. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Onlineshop, auf dem der Kunde Ihr Angebot gefunden hat, werden an das von Ihnen konfigurierte CRM-System gesendet. Der kommerzielle Marketplace unterstützt eine Vielzahl von CRM-Systemen. Außerdem können Sie optional eine Azure-Tabelle verwenden oder einen HTTPS-Endpunkt mithilfe von Power Automate konfigurieren.

Eine CRM-Verbindung kann während der Angebotserstellung zu einem beliebigen Zeitpunkt hinzugefügt oder geändert werden. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Auswählen eines Onlineshops

Wenn Sie ein SaaS-Angebot veröffentlichen, wird es in Microsoft AppSource, Azure Marketplace oder beidem aufgelistet. Jeder Onlineshop erfüllt bestimmte Kundenanforderungen. AppSource ist für Geschäftslösungen und Azure Marketplace für IT-Lösungen vorgesehen. Ihr Angebotstyp, Ihre Transaktionsfunktionen und Ihre Kategorien bestimmen, wo Ihr Angebot veröffentlicht wird. Kategorien und Unterkategorien werden den einzelnen Onlineshops basierend auf dem Lösungstyp zugeordnet. 

Wenn Ihr SaaS-Angebot eine IT-Lösung (Azure Marketplace) *und* eine Geschäftslösung (AppSource) ist, wählen Sie eine Kategorie und Unterkategorie aus, die für jeden Onlineshop gilt. Bei Angeboten, die in beiden Onlineshops veröffentlicht werden, sollte sich das Nutzenversprechen auf eine IT-Lösung *und* auf eine Geschäftslösung beziehen.

> [!IMPORTANT]
> SaaS-Angebote mit [getakteter Abrechnung](partner-center-portal/saas-metered-billing.md) sind über den Azure Marketplace und das Azure-Portal verfügbar. SaaS-Angebote, die nur private Pläne umfassen, sind über das Azure-Portal verfügbar.

| Getaktete Abrechnung | Öffentlicher Plan | Privater Tarif | Verfügbar in: |
|---|---|---|---|
| Ja             | Ja         | Nein           | Azure Marketplace und Azure-Portal |
| Ja             | Ja         | Ja          | Azure Marketplace und Azure-Portal* |
| Ja             | Nein          | Ja          | Nur Azure-Portal |
| Nein              | Nein          | Ja          | Nur Azure-Portal |
|||||

&#42; Der private Plan des Angebots ist nur über das Azure-Portal verfügbar.

Beispielsweise wird ein Angebot mit getakteter Abrechnung, das nur einen privaten Plan (keinen öffentlichen Plan) umfasst, von Kunden im Azure-Portal erworben. Erfahren Sie mehr über [Private Angebote im kommerziellen Microsoft-Marketplace](private-offers.md).

Ausführliche Informationen zu Auflistungsoptionen, die von Onlineshops unterstützt werden, finden Sie unter [Auslistungs- und Preisoptionen nach Onlineshop](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Weitere Informationen zu Kategorien und Unterkategorien finden Sie unter [Kategorien und Unterkategorien im kommerziellen Marketplace](categories.md).

## <a name="legal-contracts"></a>Verträge

Zur Vereinfachung des Beschaffungsprozesses für Kunden und zur Reduzierung der rechtlichen Komplexität für Softwareanbieter stellt Microsoft eine Vorlage für Standardverträge bereit, die Sie für Ihre Angebote im kommerziellen Marketplace verwenden können. Wenn Sie Ihre Software unter dem Standardvertrag anbieten, müssen Kunden ihn nur einmal lesen und akzeptieren, und Sie müssen keine benutzerdefinierten Geschäftsbedingungen erstellen.

Wenn Sie sich für die Verwendung des Standardvertrags entscheiden, können Sie dem Standardvertrag universelle Zusatzbedingungen und bis zu 10 benutzerdefinierte Ergänzungen hinzufügen. Sie können anstelle des Standardvertrags Ihre eigenen Geschäftsbedingungen verwenden. Diese Details werden auf der Seite **Eigenschaften** verwaltet. Ausführliche Informationen finden Sie unter [Standardvertrag für den kommerziellen Microsoft-Marketplace](standard-contract.md).

> [!NOTE]
> Nachdem Sie ein Angebot mit dem Standardvertrag für den kommerziellen Marketplace veröffentlicht haben, können Sie keine eigenen benutzerdefinierten Geschäftsbedingungen mehr verwenden. Es handelt sich um ein „oder“-Szenario. Entweder bieten Sie Ihre Lösung im Rahmen des Standardvertrags oder Ihrer eigenen Geschäftsbedingungen an. Wenn Sie die Bedingungen des Standardvertrags ändern möchten, können Sie dies über Zusatzvereinbarungen für den Standardvertrag erreichen.

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

Wenn Sie ein [neues SaaS-Angebot](create-new-saas-offer.md) im Partner Center erstellen, geben Sie Text, Bilder, optionale Videos und andere Details auf der Seite **Angebotsliste** ein. Diese Informationen sind für Ihre Kunden sichtbar, wenn sie die Angebotsauflistung im kommerziellen Marketplace entdecken, wie im folgenden Beispiel gezeigt.

:::image type="content" source="./media/example-saas-1.png" alt-text="Veranschaulicht, wie dieses Angebot in Microsoft AppSource angezeigt wird.":::

**Beschreibungen zu den Nummern**

1. Logo
2. Kategorien
3. Branchen
4. Supportadresse (Link)
5. Nutzungsbedingungen
6. Datenschutzrichtlinie
7. Angebotsname
8. Zusammenfassung
9. BESCHREIBUNG
10. Screenshots/Videos
11. Dokumente

Das folgende Beispiel zeigt eine Angebotsauflistung im Azure-Portal.

![Veranschaulicht ein im Azure-Portal aufgelistetes Angebot.](./media/example-managed-services.png)

**Beschreibungen**

1. Titel
1. BESCHREIBUNG
1. Nützliche Links
1. Screenshots

> [!NOTE]
> Der Inhalt der Angebotsauflistung muss nicht in englischer Sprache verfasst werden, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt.

Um das Angebot einfacher zu gestalten, können Sie einige dieser Elemente vorab vorbereiten. Die folgenden Elemente sind erforderlich, sofern nicht anders angegeben:

- **Name**: Dieser Name wird als Titel Ihrer Angebotsliste im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis (mit Ausnahme des Markenzeichen- und Copyrightsymbols) enthalten und muss auf 50 Zeichen begrenzt sein.
- **Zusammenfassung der Suchergebnisse**: Beschreiben Sie den Zweck oder die Funktion des Angebots in einem Satz ohne Zeilenumbrüche und mit höchstens 100 Zeichen. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus Auflistungen im kommerziellen Marketplace erfasst werden.
- **Beschreibung**: Diese Beschreibung wird in der Übersicht der Auflistungen im kommerziellen Marketplace angezeigt. Sie können z. B ein Wertversprechen, wichtige Vorteile, Zielgruppe, Kategorie- oder Branchenzuordnungen, Möglichkeiten für In-App-Käufe, erforderliche Veröffentlichungen und einen Link zu weiteren Informationen eingeben.

    Dieses Textfeld enthält Rich-Text-Editor-Steuerelemente, mit denen Sie die Beschreibung ansprechender gestalten können. Die Beschreibung kann mithilfe von HTML-Tags formatiert werden. Sie können in diesem Feld bis zu 3.000 Zeichen Text eingeben, einschließlich HTML-Markup. Weitere Tipps finden Sie unter [Erstellen einer interessanten App-Beschreibung](/windows/uwp/publish/write-a-great-app-description).

- **Anweisungen zu den ersten Schritten**: Wenn Sie Ihr Angebot über Microsoft (transaktionsfähiges Angebot) verkaufen möchten, muss dieses Feld ausgefüllt werden. Diese Anweisungen helfen Kunden, eine Verbindung mit Ihrem SaaS-Angebot herzustellen. Sie können bis zu 3.000 Zeichen Text sowie Links zu einer ausführlicheren Onlinedokumentation hinzufügen.
- **Suchbegriffe** (optional): Geben Sie bis zu drei Suchbegriffe ein, mit denen Kunden in den Onlineshops nach Ihrem Angebot suchen können. Sie müssen den **Namen** und die **Beschreibung** des Angebots nicht einschließen: Dieser Text wird automatisch in die Suche aufgenommen.
- **Link zur Datenschutzrichtlinie**: Die URL zur Datenschutzrichtlinie Ihres Unternehmens. Sie müssen eine gültige Datenschutzrichtlinie bereitstellen und sicherstellen, dass die App die Datenschutzgesetze und -bestimmungen erfüllt.
- **Kontaktinformationen**: Sie müssen die folgenden Kontakte in Ihrer Organisation bereitstellen:
  - **Supportkontakt**: Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse für Microsoft-Partner an. Diese werden dann von Kunden bei der Eröffnung von Supporttickets verwendet. Sie müssen auch die URL für Ihre Supportwebsite einschließen.
  - **Technischer Ansprechpartner**: Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse an, die Microsoft direkt verwenden kann, wenn es Probleme mit Ihrem Angebot gibt. Diese Kontaktinformationen sind nicht im kommerziellen Marketplace aufgeführt.
  - **Ansprechpartner für CSP-Programm** (optional): Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse an, wenn Sie sich für das CSP-Programm entscheiden, damit Sie bei Fragen von Partnern kontaktiert werden können. Sie können auch eine URL zu Ihren Marketingmaterialien hinzufügen.
- **Nützliche Links** (optional): Sie können für Benutzer Ihres Angebots Links zu verschiedenen Ressourcen bereitstellen. Beispiele: Foren, FAQ und Versionshinweise.
- **Unterstützende Dokumente**: Sie können bis zu drei kundenorientierte Dokumente bereitstellen, z. B. Whitepaper, Broschüren, Checklisten oder PowerPoint-Präsentationen.
- **Medien – Logos**: Stellen Sie eine PNG-Datei für das **große** Logo bereit. Dies wird in Partner Center verwendet, um ein **kleines** und ein **mittleres** Logo zu erstellen. Sie können diese Logos später durch andere Bilder ersetzen.

   - Groß (von 216 × 216 bis 350 × 350 Pixel, erforderlich)
   - Mittel (90 × 90 Pixel, optional)
   - Klein (48 × 48 Pixel, optional)

  Diese Logos werden an unterschiedlichen Stellen in den Onlineshops verwendet:

  - Das kleine Logo erscheint in den Suchergebnissen von Azure Marketplace sowie auf der Hauptseite und Suchergebnisseite von Microsoft AppSource.
  - Das mittelgroße Logo wird angezeigt, wenn Sie eine neue Ressource in Microsoft Azure erstellen.
  - Das große Logo wird auf Ihrer Seite mit der Angebotsliste sowie im Azure Marketplace und in Microsoft AppSource angezeigt.

- **Medien – Screenshots**: Fügen Sie gemäß den folgenden Anforderungen mindestens einen und höchstens fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert:
  - 1280 x 720 Pixel
  - PNG-Datei
  - Beschriftung erforderlich
- **Medien – Videos** (optional): Fügen Sie gemäß den folgenden Anforderungen bis zu vier Videos hinzu, die Ihr Angebot veranschaulichen:
  - Name
  - URL: Video darf nur auf YouTube oder Vimeo gehostet werden.
  - Miniaturansicht: PNG-Datei (1280 x 720)

> [!Note]
> Ihr Angebot muss die allgemeinen [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#100-general) und die [Software-as-a-Service-Richtlinien](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) für die Veröffentlichung auf dem kommerziellen Marketplace erfüllen.

## <a name="preview-audience"></a>Vorschauzielgruppe
Eine Vorschauzielgruppe erhält vor der Liveveröffentlichung in den Onlineshops Zugriff auf Ihr Angebot, um die End-to-End-Funktionalität vorab zu testen. Auf der Seite **Vorschauzielgruppe** können Sie eine eingeschränkte Vorschauzielgruppe definieren. Diese Einstellung ist nicht verfügbar, wenn Sie Transaktionen unabhängig abwickeln möchten, anstatt ihr Angebot über Microsoft zu verkaufen. Wenn dies der Fall ist, können Sie diesen Abschnitt überspringen und zu [Zusätzliche Verkaufschancen](#additional-sales-opportunities) wechseln.

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einem privaten Plan. Ein privater Plan ist ein Angebot, das nur einem bestimmten Publikum zur Verfügung steht. Darüber hinaus können Sie einen benutzerdefinierten Plan mit bestimmten Kunden aushandeln. Weitere Informationen finden Sie im nächsten Abschnitt: zu Plänen.

Sie können Einladungen an E-Mail-Adressen mit Microsoft-Konto (MSA) oder in Azure Active Directory (Azure AD) senden. Fügen Sie bis zu 10 E-Mail-Adressen manuell hinzu, oder importieren Sie über eine CSV-Datei bis zu 20 E-Mail-Adressen. Wenn Ihr Angebot bereits live geschaltet ist, können Sie dennoch eine Vorschauzielgruppe für das Testen von Änderungen oder für Aktualisierungen Ihres Angebots definieren.

## <a name="plans"></a>Pläne

Für transaktionsfähige Angebote ist mindestens ein Plan erforderlich. Ein Plan definiert den Umfang und die Einschränkungen einer Lösung sowie ggf. die zugehörigen Preise. Sie können mehrere Pläne für Ihr Angebot erstellen, um Ihren Kunden verschiedene technische und preisliche Optionen zu bieten. Wenn Sie sich für die unabhängige Abwicklung von Transaktionen entscheiden, anstatt ein transaktionsfähiges Angebot zu erstellen, wird die Seite **Pläne** nicht angezeigt. Wenn dies der Fall ist, überspringen Sie diesen Abschnitt, und wechseln Sie direkt zu [Zusätzliche Verkaufschancen](#additional-sales-opportunities).

Eine allgemeine Anleitung zu Plänen, einschließlich Preismodellen, kostenlosen Testversionen und privaten Plänen, finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](plans-pricing.md). In den folgenden Abschnitten erhalten Sie zusätzliche Informationen zu SaaS-Angeboten.

### <a name="saas-pricing-models"></a>SaaS-Preismodelle

Für SaaS-Angebote können Sie bei jedem Plan eines von zwei Preismodellen verwenden: _Pauschalgebühr_ oder _Pro Benutzer_. Alle Pläne im selben Angebot müssen dem gleichen Preismodell zugeordnet sein. Beispielsweise kann ein Angebot nicht gleichzeitig über die Pläne „Pauschalgebühr“ und „Pro Benutzer“ verfügen.

**Pauschalgebühr**: Ermöglichen Sie den Zugriff auf Ihr Angebot mit einer einzelnen monatlichen oder jährlichen Pauschalgebühr. Dies wird manchmal als arbeitsplatzbasierter Preis bezeichnet. Bei diesem Preismodell können Sie optional Volumentarife definieren, um Kunden mithilfe der Marketplace-Messungsdienst-API die Nutzung in Rechnung zu stellen, die nicht durch die Pauschalgebühr abgedeckt ist. Weitere Informationen zur Volumenabrechnung finden Sie unter [Volumenabrechnung für SaaS mit dem Messungsdienst für den kommerziellen Marketplace](./partner-center-portal/saas-metered-billing.md). Sie sollten diese Option auch verwenden, wenn die Nutzung Ihres SaaS-Diensts schubweise erfolgt.

**Pro Benutzer**: Ermöglichen Sie den Zugriff auf Ihr Angebot mit einem Preis, der auf der Anzahl von Benutzern, die auf Ihr Angebot zugreifen können, oder auf der Anzahl beanspruchter Arbeitsplätze basiert. Mit diesem benutzerbasierten Modell können Sie die minimale und maximale Anzahl von Benutzern festlegen, die vom Plan unterstützt werden. Sie können mehrere Pläne erstellen, um verschiedene Preispunkte basierend auf der Anzahl der Benutzer zu konfigurieren. Diese Felder sind optional. Wenn keine Auswahl erfolgt, wird angenommen, dass die Anzahl der Arbeitsplätze unbegrenzt ist (mindestens 1 und maximal die vom Service unterstützte Anzahl). Diese Felder können beim Aktualisieren des Plans bearbeitet werden.

> [!IMPORTANT]
> Nachdem das Angebot veröffentlicht wurde, können Sie das Preismodell nicht mehr ändern. Darüber hinaus müssen alle Pläne für das gleiche Angebot dasselbe Preismodell aufweisen.

### <a name="saas-billing"></a>SaaS-Abrechnung

Für SaaS-Apps, die in Ihrem Azure-Abonnement (Herausgeber) ausgeführt werden, wird Ihnen die Nutzung der Infrastruktur direkt in Rechnung gestellt. Die tatsächlichen Nutzungsgebühren der Infrastruktur sind für Kunden nicht sichtbar. Sie sollten die Gebühren für die Nutzung der Azure-Infrastruktur in Ihre Softwarelizenz-Preisgestaltung einbinden, damit sich die Kosten für die Infrastruktur, die Sie für den Betrieb der Lösung einsetzen, amortisieren.

SaaS-Apps, die über Microsoft verkauft werden, unterstützen die monatliche oder jährliche Abrechnung auf der Grundlage einer Pauschalgebühr, pro Benutzer oder gemäß der über den [Gebührenabrechnungsdienst](./partner-center-portal/saas-metered-billing.md) ermittelten Nutzungsgebühren. Der kommerzielle Marketplace funktioniert nach einem Agenturmodell, bei dem die Herausgeber die Preise festlegen, Microsoft den Kunden Rechnungen stellt und Microsoft die Einnahmen dann unter Einbehaltung einer Agenturgebühr an die Herausgeber auszahlt.

Das folgende Beispiel zeigt eine exemplarische Aufschlüsselung der Kosten und Auszahlungen zur Veranschaulichung des Agenturmodells. In diesem Beispiel stellt Microsoft 100,00 USD dem Kunden für Ihre Softwarelizenz in Rechnung und zahlt 80,00 USD an den Herausgeber aus.

| Ihre Lizenzkosten | 100 USD pro Monat |
| ------------ | ------------- |
| Azure-Nutzungskosten (D1/1-Kern) | Abrechnung direkt mit dem Herausgeber, nicht mit dem Kunden |
| Microsoft führt die Abrechnung mit dem Kunden durch | 100,00 USD pro Monat (der Herausgeber muss alle anfallenden oder weiterzugebenden Infrastrukturkosten in der Lizenzgebühr berücksichtigen) |
| **Microsoft berechnet** | **100 USD pro Monat** |
| Microsoft zahlt Ihnen 80% Ihrer Lizenzkosten<br>`*` Für qualifizierte SaaS-Apps zahlt Microsoft 90 % Ihrer Lizenzkosten| 80,00 USD pro Monat<br>``*`` 90,00 USD pro Monat |
|||

**`*` Reduzierte Marketplace-Dienstgebühr:** Für bestimmte SaaS-Angebote, die Sie im kommerziellen Marketplace veröffentlicht haben, reduziert Microsoft die Marketplace-Dienstgebühr von 20 Prozent (wie im Microsoft-Herausgebervertrag angegeben) auf 10 Prozent. Damit Ihre Angebote qualifiziert sind, müssen sie von Microsoft als gefördert für Azure IP-Co-Selling gekennzeichnet worden sein. Die Anforderungen für die Berechtigung müssen mindestens fünf (5) Arbeitstage vor dem Ende eines jeden Kalendermonats erfüllt sein, damit die reduzierte Marketplace-Dienstgebühr für den Monat in Anspruch genommen werden kann. Einzelheiten zur Berechtigung für das IP-Co-Selling finden Sie unter [Requirements for Co-sell Status](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status) (Anforderungen für den Co-Selling-Status). Die reduzierte Marketplace-Dienstgebühr gilt auch für VMs, verwaltete Apps und andere berechtigte transaktionsfähige IaaS-Angebote im Rahmen der Azure IP-Co-Selling-Incentives, die über den kommerziellen Marketplace bereitgestellt werden.

## <a name="additional-sales-opportunities"></a>Zusätzliche Verkaufschancen

Sie haben die Möglichkeit, sich für von Microsoft unterstützte Marketing- und Vertriebskanäle zu entscheiden. Wenn Sie Ihr Angebot im Partner Center erstellen, werden gegen Ende des Prozesses zwei Registerkarten angezeigt:

- **Verkauf über CSPs**: Verwenden Sie diese Option, um Microsoft CSP-Partnern (Cloud Solution Providers) zu ermöglichen, Ihre Lösung als Teil eines Bundleangebots weiterzuverkaufen. Weitere Informationen zu diesem Programm finden Sie unter [Cloud Solution Provider-Programm](cloud-solution-providers.md).

- **Co-Selling mit Microsoft**: Mit dieser Option können die Microsoft-Verkaufsteams Ihre für IP-Co-Selling in Frage kommende Lösung in Betracht ziehen, wenn sie Kundenanforderungen evaluieren. Einzelheiten zur Berechtigung für das Co-Selling finden Sie unter [Requirements for Co-sell Status](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status) (Anforderungen für den Co-Selling-Status). Ausführliche Informationen dazu, wie Sie Ihr Angebot auf die Auswertung vorbereiten, finden Sie unter [Co-Selling-Option im Partner Center](commercial-marketplace-co-sell.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines SaaS-Angebots im kommerziellen Marketplace](create-new-saas-offer.md)
- [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md)
