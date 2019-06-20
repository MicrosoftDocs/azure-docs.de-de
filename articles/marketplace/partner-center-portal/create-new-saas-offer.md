---
title: Erstellen eines neuen SaaS-Angebots im kommerziellen Marketplace
description: Erstellen eines neuen SaaS-Angebots (Software-as-a-Service) für das Auflisten oder Verkaufen in Azure Marketplace, AppSource oder über das CSP-Programm (Cloud Solution Provider) mit dem kommerziellen Marketplace-Portal im Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9c8b8a6ad8a10a65a05b46bf433d00b4e3ecb402
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455629"
---
# <a name="create-a-new-saas-offer"></a>Erstellen eines neuen SaaS-Angebots

Um mit dem Erstellen von SaaS-Angeboten (Software-as-a-Service) zu beginnen, müssen Sie zunächst [ein Partner Center-Konto erstellen](./create-account.md) und das [Dashboard „Kommerzieller Marketplace“](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) öffnen, in dem die Registerkarte **Übersicht** ausgewählt ist.

![Dashboard „Kommerzieller Marketplace“ im Partner Center](./media/new-offer-overview.png)

Klicken Sie auf die Schaltfläche **Neues Angebot**, und wählen Sie dann das Menüelement **Software-as-a-Service** aus. 

Wenn Sie einen der anderen Angebotstypen auswählen, werden Sie zum älteren [Cloud-Partnerportal](https://cloudpartner.azure.com/) umgeleitet.  Im kommerziellen Marketplace im Partner Center-Portal sind derzeit nur SaaS-Angebote verfügbar. 

![Fenster „Neues Angebot“ im Partner Center](./media/new-offer-click.png)

Das Dialogfeld **Neues Angebot** wird angezeigt. 

![Dialogfeld „Neues Angebot“](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>Angebots-ID und Angebotsname

- **Angebots-ID:** Erstellen Sie einen eindeutigen Bezeichner für jedes Angebot in Ihrem Konto. Diese ID wird für Kunden in der URL-Adresse für das Marketplace-Angebot und ggf. in Azure Resource Manager-Vorlagen angezeigt. Die Angebots-ID darf keine Großbuchstaben enthalten und muss alphanumerisch sein (Bindestriche und Unterstriche sind zulässig, Leerzeichen jedoch nicht). Sie ist auf 50 Zeichen beschränkt und kann nach der Erstellung nicht aktualisiert werden.  
Beispiel: test-offer-1
<br>Dies resultiert in der folgenden URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Angebotsname:** Der offizielle Name Ihres SaaS-Anwendungsangebots, der in allen Veröffentlichungen, Ankündigungen und Websites einheitlich sein muss.  Dieser Name ist möglicherweise markenrechtlich geschützt.  Der Angebotsname darf keine Leerzeichen und Emojis (mit Ausnahme des Markenzeichen- oder Copyrightsymbols) enthalten und muss auf 50 Zeichen begrenzt sein.
<br>Beispiel: Testangebot 1&#8482;

Klicken Sie auf **Erstellen**.  Für das Angebot wird die Seite **Angebotsübersicht** erstellt.  

![Angebotsübersicht im Partner Center](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Angebotsübersicht

Die Seite **Angebotsübersicht** enthält Folgendes: 

- **Veröffentlichungsstatus** enthält eine visuelle Darstellung der zum Veröffentlichen des Angebots erforderlichen Schritte und Angaben zur Dauer der einzelnen Schritte. Die Symbole für unvollständige Veröffentlichungsschritte sind abgeblendet. 

- Das Menü **Angebotsübersicht** enthält eine Liste mit Links zum Ausführen von Vorgängen für das Angebot. Die Liste der Vorgänge ändert sich basierend auf der Auswahl, die Sie für das Angebot vornehmen.  
    - Wenn das Angebot ein Entwurf ist: „Entwurf löschen“ 
    - Wenn das Angebot live: „Stop sell offer“ (Verkauf des Angebots einstellen) 
    - Wenn das Angebot in der Vorschau ist: „Live schalten“ 
    - Wenn keine Bestätigung durch den Herausgeber erfolgt ist: „Veröffentlichung abbrechen“

## <a name="offer-setup"></a>Angebotseinrichtung

Auf der Registerkarte **Angebotseinrichtung** wird um die folgenden Informationen gebeten. Klicken Sie auf **Speichern**, nachdem Sie die Angaben in diesen Feldern vorgenommen haben.

- **Möchten Sie über Microsoft verkaufen?** (Ja/Nein)
    - **Ja**: Sie möchten Ihr Angebot über Microsoft verkaufen, wobei Microsoft Marketplace-Transaktionen in Ihrem Auftrag hostet, oder 
    - **Nein**: Sie möchten Ihr Angebot lediglich über die Marketplaces auflisten und alle finanziellen Transaktionen unabhängig von Microsoft abwickeln.    

### <a name="sell-through-microsoft"></a>Über Microsoft verkaufen

Der Verkauf über Microsoft ermöglicht eine bessere Ermittlung und Gewinnung von Kunden sowie das Hosten von Marketplace-Transaktionen durch Microsoft in Ihrem Namen. Außerdem lassen sich die weltweit verfügbaren Handelsfunktionen von Microsoft nutzen.

#### <a name="saas-offer-requirements"></a>Anforderungen für SaaS-Angebote

Um SaaS-Angebote (Software-as-a-Service) mit dem kommerziellen Marketplace im Partner Center aufzulisten, müssen die folgenden Kriterien erfüllt sein:

- Das Angebot muss mit Azure-Clients kompatibel sein. (Für optimale Leistung und Kompatibilität werden SaaS-Apps häufig auch in Azure gehostet, dies ist jedoch nicht erforderlich.) 
- Das Angebot muss die Identitätsverwaltung und Benutzerauthentifizierung von [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) verwenden.
- Das Angebot muss für die Integration in den Azure Marketplace [SaaS-Fulfillment-APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2) verwenden.

#### <a name="billing-infrastructure-costs"></a>Abrechnung von Infrastrukturkosten
Für SaaS-Angebote müssen Sie als Herausgeber die Nutzungsgebühren für Azure-Infrastruktur und Softwarelizenzgebühren als einen Kostenposten verrechnen. Diese Kosten werden dem Kunden als monatliche Pauschalgebühr ausgewiesen. Die Nutzung der Azure-Infrastruktur wird verwaltet und Ihnen als Partner direkt in Rechnung gestellt. Tatsächliche Nutzungsgebühren für die Infrastruktur werden dem Kunden nicht angezeigt. Herausgeber entscheiden sich in der Regel dafür, die Nutzungsgebühren für Azure-Infrastruktur in ihren Softwarelizenzpreis einfließen zu lassen. 

Softwarelizenzgebühren werden als monatliche standortbasierte Abonnementpauschalgebühr ausgewiesen und nicht nach Volumen oder Verbrauch berechnet.

|**Ihre Lizenzkosten**|**100 USD pro Monat**|
|:---|:---|
|Azure-Nutzungskosten (D1/1-Kern)|Abrechnung direkt mit dem Herausgeber, nicht mit dem Kunden|
|Microsoft führt die Abrechnung mit dem Kunden durch|100,00 USD pro Monat (der Herausgeber muss alle anfallenden oder weiterzugebenden Infrastrukturkosten in der Lizenzgebühr berücksichtigen)|

- In diesem Szenario stellt Microsoft 100,00 USD für Ihre Softwarelizenz in Rechnung und zahlt 80,00 USD an den Herausgeber aus.
- Für Partner, die für die **reduzierte Marketplace-Dienstgebühr** qualifiziert sind, wird von Mai 2019 bis Juni 2020 eine reduzierte Transaktionsgebühr für die SaaS-Angebote angezeigt. In diesem Szenario stellt Microsoft 100,00 USD für Ihre Softwarelizenz in Rechnung und zahlt 90,00 USD an den Herausgeber aus.

> [!NOTE]
> **Reduzierte Marketplace-Dienstgebühr**: Für SaaS-Angebote, die Sie in unserem kommerziellen Marketplace veröffentlicht haben, reduziert Microsoft die Marketplace-Dienstgebühr von 20 % (wie im Microsoft-Herausgebervertrag angegeben) auf 10 %. Damit Ihr Angebot qualifiziert ist, muss mindestens eines Ihrer Angebote von Microsoft als bereit für IP-Co-Selling oder als priorisiert für IP-Co-Selling gekennzeichnet worden sein.  Die Anforderungen für die Berechtigung müssen mindestens fünf (5) Arbeitstage vor dem Ende eines jeden Kalendermonats erfüllt sein, damit die reduzierte Marketplace-Dienstgebühr für den Monat in Anspruch genommen werden kann.  Die reduzierte Marketplace-Dienstgebühr gilt nicht für virtuelle Computer, verwaltete Apps oder andere Produkte, die über unseren kommerziellen Marketplace zur Verfügung gestellt werden.  Die reduzierte Marketplace-Dienstgebühr ist nur für qualifizierte Angebote bezüglich Lizenzgebühren verfügbar, die von Microsoft zwischen dem 1. Mai 2019 und dem 30. Juni 2020 vereinnahmt wurden.  Nach diesem Zeitpunkt gilt für die Marketplace-Dienstgebühr wieder der normale Betrag. 

|**Microsoft berechnet**|**100 USD pro Monat**|
|:---|:---|
|Microsoft zahlt Ihnen 80% Ihrer Lizenzkosten <br>**Für qualifizierte SaaS-Apps zahlt Microsoft 90 % Ihrer Lizenzkosten*|80,00 USD pro Monat <br>90,00 *$* pro Monat*|


#### <a name="csp-program-opt-in"></a>Abonnement des CSP-Programms
Mit dem [CSP-Programm (Cloud Solution Provider)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) können Softwareangebote mit minimalen Investitionen für Marketing und Vertrieb Millionen von qualifizierten Microsoft-Kunden erreichen.

- **Channels: Make my offer available in the CSP program** (Kanäle: Mein Angebot im CSP-Programm verfügbar machen) (Kontrollkästchen)

Wenn Sie die Option wählen, Ihr Angebot im CSP-Programm verfügbar zu machen, können Cloud Solution Provider Ihr Produkt als Teil einer Paketlösung verkaufen. 

### <a name="list-through-microsoft"></a>List through Microsoft (Über Microsoft auflisten)

Erstellen Sie eine Marketplace-Liste, um mit Microsoft für Ihr Geschäft zu werben. Wenn Sie Ihr Angebot nur auflisten und die Transaktion nicht über Microsoft ausführen lassen, nimmt Microsoft nicht direkt an Softwarelizenztransaktionen teil. Es gibt keine zugehörige Transaktionsgebühr, und der Herausgeber behält 100 % der vom Kunden gezahlten Softwarelizenzgebühren. Der Herausgeber ist für die Unterstützung aller Aspekte der Softwarelizenztransaktion verantwortlich, einschließlich, aber nicht beschränkt auf: Auftragsabwicklung, Messung, Abrechnung, Rechnungsstellung, Zahlung und Inkasso. 

- **Wie sollen potenzielle Kunden mit diesem Angebot interagieren?**

##### <a name="get-it-now-free"></a>Jetzt abrufen (kostenlos)
Listen Sie Ihr Angebot für Kunden als kostenlos auf, indem Sie eine gültige URL (mit „http“ oder „https“ beginnend) angeben, über die sie auf Ihre App zugreifen können.  Beispiel: `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Kostenlose Testversion
Listen Sie Ihr Angebot für Kunden als kostenlose Testversion auf, indem Sie eine gültige URL (mit „http“ oder „https“ beginnend) angeben, über die sie auf Ihre App zugreifen können.  Beispiel: `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Kontakt mit mir aufnehmen
Sammeln Sie über Ihr CRM-System (Customer Relationship Management) Kundenkontaktinformationen. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Marketplace, auf dem der Kunde Ihr Angebot gefunden hat, werden an das CRM-System gesendet, das Sie konfiguriert haben. Weitere Informationen zum Konfigurieren des CRM-Systems finden Sie unter [Einbinden der Leadverwaltung](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Liste der Marketplace-Beispielangebote

![Liste der Marketplace-Beispielangebote mit Hinweisen](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Aktivieren einer Testversion

Die Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen die Möglichkeit geben, vor dem Kauf einen Test durchzuführen, was zu einer höheren Konvertierung und der Generierung von aussichtsreichen Leads führt. [Erfahren Sie mehr über Testversionen.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Enable a test drive** (Testversion aktivieren) (Kontrollkästchen) 

Wenn Sie eine Testversion aktivieren, werden Sie aufgefordert, eine Demoumgebung zu konfigurieren, in der Kunden Ihr Angebot während eines festen Zeitraums testen können. 

### <a name="type-of-test-drive"></a>Typ der Testversion

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Eine Bereitstellungsvorlage, die alle Azure-Ressourcen enthält, aus denen Ihre Lösung besteht. In den für dieses Szenario geeigneten Produkten werden nur Azure-Ressourcen verwendet.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft hostet und verwaltet den Testversionsdienst (einschließlich Bereitstellung) für ein Business Central-Enterprise Resource Planning-System (Finanzen, Betrieb, Lieferkette, CRM usw.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft hostet und verwaltet den Testversionsdienst (einschließlich Bereitstellung) für ein Customer Engagement-System (Vertrieb, Kundendienst, Projektservice, Vor-Ort-Service usw.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft hostet und verwaltet den Testversionsdienst (einschließlich Bereitstellung) für ein Finance and Operations-Enterprise Resource Planning-System (Finanzen, Betrieb, Fertigung, Lieferkette usw.). 
- **[Logik-App](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Eine Bereitstellungsvorlage, die alle komplexen Lösungsarchitekturen umfasst. Alle benutzerdefinierten Produkte sollten mit diesem Testversionstyp genutzt werden.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Ein eingebetteter Link zu einem benutzerdefinierten Dashboard. Für Produkte, mit denen ein interaktives Power BI-Visual veranschaulicht werden soll, sollte dieser Testversionstyp verwendet werden. Sie müssen hier lediglich Ihre eingebettete Power BI-URL hochladen.

#### <a name="additional-test-drive-resources"></a>Zusätzliche Ressourcen zu Testversionen
- [Test Drive Technical Best Practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) (Bewährte technische Methoden für Testversionen, in englischer Sprache)
- [Testversion: Marketing und Best Practices](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Test Drives](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (Testversionen, Informationsblatt in englischer Sprache)

## <a name="connect-lead-management"></a>Einbinden der Leadverwaltung

Nehmen Sie direkt Kontakt zu Kunden auf, indem Sie Ihr Angebot in Marketplaces auflisten und Ihr CRM-System (Customer Relationship Management) einbinden. So erhalten Sie Kundenkontaktinformationen sofort, nachdem ein Kunde Interesse bekundet oder Ihr Produkt bereitgestellt hat.

- **Leadziel auswählen** (Dropdownmenü): Geben Sie Verbindungsinformationen für das CRM-System an, wenn Sie möchten, dass wir Kundenleads senden. 

Das Partner Center unterstützt die Leadverwaltung für die folgenden CRM-Systeme. Klicken Sie auf den Link, um Anweisungen zur Einrichtung zu erhalten.

- „Azure-BLOB“: Geben Sie die E-Mail-Adresse, den Containernamen und die Verbindungszeichenfolge für das Speicherkonto ein. 
- [Azure-Tabelle](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table): Geben Sie die E-Mail-Kontaktadresse und die Verbindungszeichenfolge für das Speicherkonto an. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics): Geben Sie die E-Mail-Kontaktadresse, die URL und den Authentifizierungsmodus (Office 365 oder Azure Active Directory) an.
- [HTTPS-Endpunkt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https): Geben Sie E-Mail-Kontaktadresse und die URL des HTTP-Endpunkts an. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo): Geben Sie die E-Mail-Kontaktadresse, die Formular-ID, Munchkin-Konto-ID und Server-ID an.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce): Geben Sie die E-Mail-Kontaktadresse und die Organisations-ID an. 

#### <a name="additional-lead-management-resources"></a>Zusätzliche Ressourcen zur Leadverwaltung
- [Häufig gestellte Fragen zur Leadverwaltung](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Häufige Leadkonfigurationsfehler](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Lead management at a glance](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (Übersicht über die Leadverwaltung, Informationsblatt in englischer Sprache)

Klicken Sie auf **Speichern**, bevor Sie mit dem nächsten Abschnitt fortfahren!

## <a name="properties"></a>Eigenschaften
Auf der Registerkarte **Eigenschaften** werden Sie aufgefordert, die Kategorien und Branchen, die zum Gruppieren Ihres Angebots in den Marketplaces verwendet werden, die Verträge für Ihr Angebot und die Version Ihrer App zu definieren. 

Klicken Sie auf **Speichern**, nachdem Sie die Angaben in diesen Feldern vorgenommen haben. 

### <a name="category"></a>Category (Kategorie)
Wählen Sie mindestens eine (1) und maximal drei (3) Kategorien zum Gruppieren Ihres Angebots in den entsprechenden Marketplace-Suchbereichen aus. Geben Sie in der Angebotsbeschreibung an, wie Ihr Angebot diesen Kategorien entspricht. 

### <a name="industry"></a>Branche
Wählen Sie bis zu zwei (2) Branchen, die zum Gruppieren Ihres Angebots in den entsprechenden Marketplace-Suchbereichen verwendet werden. Wenn Ihr Angebot nicht für eine bestimmte Branche geeignet ist, wählen Sie keine aus. Geben Sie in der Angebotsbeschreibung an, wie sich Ihr Angebot für die ausgewählten Branchen eignet. 

### <a name="app-version"></a>App-Version
Dies ist ein optionales Feld, das im AppSource Marketplace verwendet wird, um die Versionsnummer Ihres Angebots anzugeben. 

### <a name="standard-contract"></a>Standardvertrag

- **Standardvertrag verwenden?**

Zur Vereinfachung des Beschaffungsprozesses für Kunden und zur Verringerung der rechtlichen Komplexität für Softwareanbieter stellt Microsoft die Vorlage „Standardvertrag“ bereit, um eine Transaktion im Marketplace zu erleichtern. 

Statt benutzerdefinierte Geschäftsbedingungen zu erstellen, können Azure Marketplace-Herausgeber ihre Software wahlweise unter dem Standardvertrag anbieten, den Kunden nur einmal überprüfen und akzeptieren müssen. 

Der Standardvertrag ist hier zu finden: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Nutzungsbedingungen

Wenn Ihre Lizenzbedingungen vom Standardvertrag abweichen, können Sie hier die eigenen Nutzungsbedingungen eingeben. Sie können in diesem Feld bis zu 10.000 Zeichen Text eingeben. Wenn Ihre Nutzungsbedingungen eine längere Beschreibung erfordern, geben Sie in diesem Feld einen einzelnen URL-Link ein, über den Ihre zusätzlichen Lizenzbedingungen aufgerufen werden können. Er wird für Kunden als aktiver Link angezeigt.

Kunden müssen diese Bedingungen akzeptieren, bevor sie Ihre App testen können. 

Klicken Sie auf **Speichern**, bevor Sie mit dem nächsten Abschnitt fortfahren!

## <a name="offer-listing"></a>Angebotsliste

Auf der Registerkarte „Angebotsliste“ werden die Sprachen (und Märkte) angezeigt, in denen Ihr Angebot verfügbar ist. Derzeit ist nur der Standort „Englisch (USA)“ verfügbar. Auf dieser Seite werden zudem der Status der sprachspezifischen Liste und das Datum/die Uhrzeit der Hinzufügung angezeigt. Sie müssen für jede Sprache/jeden Markt die Marketplace-Informationen (Angebotsname, Beschreibung, Suchbegriffe usw.) definieren.

### <a name="offer-listings"></a>Angebotsauflistungen

Geben Sie Informationen an, die im Marketplace angezeigt werden sollen, einschließlich der Beschreibung des Angebots und der Marketingressourcen.

- **Name** (erforderlich): Der hier definierte Name wird als Titel der Angebotsliste in dem/den von Ihnen gewählten Marketplace(s) angezeigt. Der Name ist basierend auf Ihrer vorherigen Eingabe in **Neues Angebot** bereits eingetragen.  Er ist möglicherweise markenrechtlich geschützt.  Er darf keine Leerzeichen und Emojis (mit Ausnahme des Markenzeichen- und Copyrightsymbols) enthalten und muss auf 50 Zeichen begrenzt sein.
- **Zusammenfassung** (erforderlich): Geben Sie eine kurze Beschreibung Ihres Angebots ein, die in den Suchergebnissen für Marketplace-Liste(n) verwendet werden soll. In diesem Feld können bis zu 100 Zeichen Text eingegeben werden.
- **Beschreibung:** (erforderlich): Geben Sie eine Beschreibung Ihres Angebots ein, die in der Übersicht über die Marketplace-Liste(n) angezeigt werden soll. Sie können z.B ein Wertversprechen, wichtige Vorteile, Kategorie- oder Branchenzuordnungen, Möglichkeiten für In-App-Käufe, erforderliche Veröffentlichungen und einen Link zu weiteren Informationen eingeben.
In diesem Feld können bis zu 3.000 Zeichen Text eingegeben werden. Weitere Tipps finden Sie im Artikel [Erstellen einer interessanten App-Beschreibung](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Suchbegriffe**: Geben Sie bis zu drei Suchbegriffe ein, mit denen Kunden in dem/den Marketplace(s) nach Ihrem Angebot suchen können.
- **Anweisungen für den Einstieg** (erforderlich): Erläutern Sie potenziellen Kunden, wie die App konfiguriert und mit ihrer Verwendung begonnen wird.  Diese Schnellstartanleitung kann Links zu ausführlicherer Onlinedokumentation enthalten. In diesem Feld können bis zu 3.000 Zeichen Text eingegeben werden. 

#### <a name="links"></a>Links

- **Datenschutzrichtlinie** (erforderlich): Erstellen Sie eine Verknüpfung zur Datenschutzrichtlinie Ihrer Organisation. Sie müssen sicherzustellen, dass die App die Datenschutzgesetze und -bestimmungen erfüllt, und Sie müssen eine gültige Datenschutzrichtlinie bereitstellen.
- **Marketingmaterial für das CSP-Programm** (optional): Sie müssen einen Link zu Marketingmaterial einfügen, wenn Sie Ihr Angebot auf das [CSP-Programm (Cloud Solution Provider)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) ausdehnen. Durch CSP wird Ihr Angebot auf ein breiteres Spektrum von qualifizierten Kunden erweitert, indem CSP-Partnern das Bündeln, Vermarkten und Verkaufen Ihres Angebots ermöglicht wird. Diese Handelspartner benötigen Zugriff auf Materialien für die Vermarktung Ihres Angebots. Weitere Informationen finden Sie unter [Go-To-Market-Dienste](https://partner.microsoft.com/reach-customers/gtm).
- **Nützliche Links** (optional): Optionale zusätzliche Onlinedokumente zu Ihrer App oder zugehörigen Diensten, die durch die Angabe eines **Titels** und einer **URL** aufgeführt werden. Fügen Sie weitere nützliche Links hinzu, indem Sie auf **+ URL hinzufügen** klicken.

#### <a name="contact-information"></a>Kontaktinformationen

- **Kontakte**: Geben Sie für jeden Kundenkontakt einen **Namen**, eine **Telefonnummer** und eine **E-Mail**-Adresse des Mitarbeiters ein.  (Diese werden *nicht* öffentlich angezeigt). Zudem ist eine **Support-URL** für die Gruppe **Supportkontakt** erforderlich.  (Diese Information *wird* öffentlich angezeigt).

**Supportkontakt** (erforderlich): Für allgemeine Supportfragen.

**Technischer Ansprechpartner** (erforderlich): Für technische Fragen.

**Channel Manager contact** (Channel-Manager-Kontakt) (erforderlich): Für Fragen von Handelspartnern zum CSP-Programm.

#### <a name="files-and-images"></a>Files and Images (Dateien und Bilder)

- **Dokumente** (erforderlich): Fügen Sie zugehörige Marketingdokumente im PDF-Format für Ihr Angebot hinzu, mindestens ein (1) und maximal drei (3) Dokumente pro Angebot.
- **Bilder** (optional): In dem/den Marketplace(s) können Logobilder Ihres Angebots an mehreren Stellen angezeigt werden, wobei die folgenden Größen erforderlich sind. „Small: 48 x 48 pixels“  _(Klein: 48 x 48 Pixel) (erforderlich),_ „Medium: 90 x 90 pixels“ (Mittel: 90 x 90 Pixel), „Large: 216 x 216 pixels“  _(Groß: 216 x 216 Pixel) (erforderlich),_ „Wide: 255 x 115 pixels“ (Breit: 255 x 115 Pixel) und „Hero: 815 x 290 pixels“ (Hero: 815 x 290 Pixel). Alle Bilder müssen das PNG-Format aufweisen.
- **Screenshots** (erforderlich): Fügen Sie Screenshots hinzu, um Ihr Angebot zu veranschaulichen. Es können maximal fünf (5) Screenshots hinzugefügt werden. Ihre Größe sollte 1280 x 720 Pixel betragen. Alle Bilder müssen das PNG-Format aufweisen.
- **Videos** (optional): Fügen Sie Links zu Videos hinzu, um Ihr Angebot zu veranschaulichen. Sie können Links zu YouTube und/oder Vimeo-Videos angeben, die zusammen mit Ihrem Angebot bei Kunden angezeigt werden. Sie müssen auch ein Miniaturbild des Videos hinzufügen. Es muss das PNG-Format und eine Größe von 1280 x 720 Pixel aufweisen. Sie können bis zu vier Videos pro Angebot anzeigen.

Klicken Sie auf **Speichern**, bevor Sie mit dem nächsten Abschnitt fortfahren!

#### <a name="additional-marketplace-listing-resources"></a>Zusätzliche Ressourcen für Marketplace-Listen

- [Bewährte Methoden für Angebotslistung](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Vorschau

Sie können auf der Registerkarte **Vorschau** eine eingeschränkte **Vorschauzielgruppe** für das Freigeben Ihres Angebots definieren, bevor Sie das Angebot für die größere Marketplace-Zielgruppe live veröffentlichen.

> [!IMPORTANT]
> Sie müssen **Live schalten** auswählen, bevor Ihr Angebot live für die öffentliche Marketplace-Zielgruppe veröffentlicht wird, nachdem Sie es in der Vorschau überprüft haben.

- **Define a Preview Audience: Add a single AAD/MSA account email per line, along with an optional description.** (Definieren Sie eine Vorschauzielgruppe: Fügen Sie eine einzelne AAD/MSA-Konto-E-Mail-Adresse pro Zeile und optional eine Beschreibung hinzu.)

Fügen Sie manuell bis zu zehn (10) E-Mail-Adressen oder durch das Hochladen einer CSV-Datei bis zu zwanzig (20) E-Mail-Adressen für vorhandene MSA-Konten (Microsoft Account) oder AAD-Konten (Azure Active Directory) hinzu, damit die Besitzer dieser Konten sie bei der Überprüfung Ihres Angebots vor seiner Liveveröffentlichung unterstützen. Durch das Hinzufügen dieser Konten definieren Sie eine Zielgruppe, die Zugriff auf die Vorschau Ihres Angebot erhält, bevor es in dem/den Marketplace(s) veröffentlicht wird. Wenn Ihr Angebot bereits live geschaltet ist, können Sie dennoch eine Vorschauzielgruppe für das Testen von Änderungen oder Aktualisierungen Ihres Angebots definieren.

> [!NOTE]
> Die Vorschauzielgruppe unterscheidet sich von einer privaten Zielgruppe. Einer Vorschauzielgruppe wird Zugriff auf das Angebot gewährt, _bevor_ es in den Marketplaces live veröffentlicht wird. Sie können auch einen Plan erstellen und nur für eine private Zielgruppe verfügbar machen. Auf der Registerkarte **Planlisting** können Sie mit dem Kontrollkästchen **This is a private plan** (Dies ist ein privater Plan) eine private Zielgruppe definieren. Sie können mithilfe von Azure-Mandanten-IDs eine private Zielgruppe von bis zu 20.000 Kunden definieren.

## <a name="technical-configuration"></a>Technische Konfiguration

Auf der Registerkarte **Technische Konfiguration** werden die technischen Informationen (URL-Pfad, Webhook, Mandanten-ID und App-ID) für die Verbindung mit Ihrem Angebot definiert. Durch diese Verbindung können wir Ihr Angebot im Azure-Abonnement des Kunden als Ressource bereitstellen, wenn er es kaufen möchte.

- **URL der Angebotsseite** (erforderlich): Definieren Sie die URL der Website, zu der der Kunde weitergeleitet wird, nachdem er Ihr Angebot auf dem Marketplace erworben hat. Diese URL wird auch der Endpunkt, der die Verbindungs-APIs für Geschäfte mit Microsoft empfängt.

- **Verbindungswebhook** (erforderlich): Für alle asynchronen Ereignisse, die Microsoft im Auftrag des Kunden an Sie senden muss (Beispiel: das Azure-Abonnement ist ungültig geworden), müssen Sie einen Webhook für die Verbindung angeben. Wenn Sie noch nicht über ein Webhooksystem verfügen, ist die einfachste Konfiguration eine Logik-App am HTTP-Endpunkt, die auf alle veröffentlichten Ereignisse lauscht und sie entsprechend behandelt (z.B. https:\//prod-1westus.logic.azure.com:443/work). Weitere Informationen finden Sie unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten in Logik-Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID des Azure AD-Mandanten** (erforderlich): Es ist im Azure-Portal erforderlich, dass Sie eine [Azure AD-App (Active Directory) erstellen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal), damit überprüft werden kann, ob die Verbindung zwischen unseren beiden Diensten über eine authentifizierte Kommunikation erfolgt. Um die [Mandanten-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id) zu ermitteln, wechseln Sie zu Azure Active Directory, wählen Sie **Eigenschaften** aus, und suchen Sie nach der aufgeführten Nummer der **Verzeichnis-ID** (z.B. 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-App-ID** (erforderlich): Sie benötigen auch die [Anwendungs-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) und einen Authentifizierungsschlüssel. Um diese Werte zu ermitteln, wechseln Sie zu Azure Active Directory, und wählen Sie **App-Registrierungen** aus. Suchen Sie dann nach der aufgeführten Nummer der **Anwendungs-ID** (z.B. 50c464d3-4930-494c-963c-1e951d15360e). Um den Authentifizierungsschlüssel zu suchen, wechseln Sie zu **Einstellungen**, und wählen Sie **Schlüssel** aus. Sie müssen eine Beschreibung und eine Dauer angeben und erhalten dann einen Zahlenwert.

 Beachten Sie, dass die Azure-Anwendungs-ID Ihrer Herausgeber-ID zugeordnet ist. Stellen Sie also sicher, dass in allen Ihren Angeboten dieselbe Anwendungs-ID verwendet wird.

## <a name="plan-overview"></a>Planübersicht

Auf der Registerkarte **Planübersicht** können Sie eine Vielzahl von Planoptionen innerhalb desselben Angebots bereitstellen. Diese Pläne (auch als SKUs bezeichnet) können sich in Bezug auf Version, Monetarisierung und Dienstebenen unterscheiden. Sie müssen mindestens einen Plan einrichten, um Ihr Angebot im Marketplace zu verkaufen.

Nach der Erstellung werden die Plannamen, IDs und Preismodelle, die Verfügbarkeit (öffentlich oder privat), der aktuelle Veröffentlichungsstatus und alle verfügbaren Aktionen angezeigt.

Die unter **Planübersicht** verfügbaren **Aktionen** variieren je nach dem aktuellen Status des Plans und umfassen möglicherweise:

- Wenn der Planstatus **Entwurf** lautet: „Entwurf löschen“
- Wenn der Planstatus **Live** lautet: „Stop sell plan“ (Verkauf des Plans einstellen) oder „Sync private Audience“ (Private Zielgruppe synchronisieren)

**Neuen Plan erstellen** (mindestens ein Plan für diejenigen, die den Verkauf über Microsoft abwickeln möchten)

- **Plan-ID**: Erstellen Sie für jeden Plan im Angebot eine eindeutige Plan-ID. Diese ID wird für Kunden in der Produkt-URL-Adresse und ggf. in Azure Resource Manager-Vorlagen angezeigt. Verwenden Sie nur klein geschriebene alphanumerische Zeichen, Bindestriche und Unterstriche. Für die Plan-ID sind maximal 50 Zeichen zulässig. Die Plan-ID kann nach der Erstellung nicht geändert werden.
- **Planname:** Für Kunden wird dieser Name angezeigt, wenn sie einen Plan in Ihrem Angebot auswählen. Erstellen Sie für jeden Plan im Angebot einen eindeutigen Angebotsnamen. Der Planname wird verwendet, um Softwarepläne zu unterscheiden, die möglicherweise Teil desselben Angebots sind (Beispiel: Angebotsname: Windows Server, Pläne: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Planlisting

Auf der Registerkarte **Planlisting** werden die Sprachen (und Märkte) angezeigt, in denen Ihr Angebot verfügbar ist. Derzeit ist nur der Standort „Englisch (USA)“ verfügbar. Auf dieser Seite werden zudem der Status der sprachspezifischen Liste und das Datum/die Uhrzeit der Hinzufügung angezeigt. Sie müssen für jede Sprache/jeden Markt die Marketplace-Informationen (Angebotsname, Beschreibung, Suchbegriffe usw.) definieren.

#### <a name="plan-listing-details"></a>Planlistinginformationen

Nach Auswahl einer der Plansprachen werden Informationen zum **Planlisting** angezeigt, einschließlich **Name** und **Beschreibung**.

- **Name**: Ist auf Grundlage Ihre Vorschaueintrags **Neuer Plan** im Voraus ausgefüllt und wird als Titel des Softwareplans für Ihr Angebot im Marketplace angezeigt.
- **Beschreibung:** Diese Beschreibung bietet die Möglichkeit zu erklären, was diesen Softwareplan einzigartig macht und von anderen Softwareplänen in Ihrem Angebot unterscheidet. Sie darf bis zu 500 Zeichen enthalten.

Klicken Sie auf **Speichern**, nachdem Sie die Angaben in diesen Feldern vorgenommen haben.

#### <a name="plan-pricing-and-availability"></a>Preise und Verfügbarkeit des Plans

Auf der Registerkarte **Preise und Verfügbarkeit** können Sie die Märkte, in denen der Plan verfügbar ist, das gewünschte Monetarisierungsmodell, den Preis und den Abrechnungszeitraum konfigurieren. Darüber hinaus können Sie angeben, ob der Plan für alle Benutzer oder nur für bestimmte Kunden (eine private Zielgruppe) angezeigt werden soll.

#### <a name="markets"></a>Märkte

- **Edit markets** (Märkte bearbeiten) (optional)

Jeder Plan muss in mindestens einem Markt verfügbar sein. Aktivieren Sie das Kontrollkästchen für jeden Marktstandort, in dem Sie diesen Plan zur Verfügung stellen möchten. Zur Unterstützung sind ein Suchfeld und eine Schaltfläche zum Auswählen von Ländern vorhanden, in denen die Umsatz- und Gebrauchssteuer von Microsoft für Sie überwiesen wird. 


Wenn Sie bereits Preise in US-Dollar für Ihren Plan festgelegt haben und einen anderen Marktstandort hinzufügen, wird der Preis für den neuen Markt entsprechend dem aktuellen Wechselkurs berechnet. Sie sollten vor dem Veröffentlichen immer den Preis für jeden Markt überprüfen. Die Preise können nach dem Speichern der Änderungen mithilfe des Links „Export prices (xlsx)“ (Preise exportieren (.xlsx)) überprüft werden.

#### <a name="pricing"></a>Preise

- **Preismodell**: „Flat rate“ (Pauschalgebühr) oder „Seat based“ (Arbeitsplatzbasiert)

**Flat rate:** (Pauschalgebühr): Ermöglichen Sie den Zugriff auf Ihr Angebot mit einer einzelnen monatlichen oder jährlichen Pauschalgebühr. Dies wird manchmal als arbeitsplatzbasierter Preis bezeichnet.

**Seat based** (Arbeitsplatzbasiert): Ermöglichen Sie den Zugriff auf Ihr Angebot mit einem Preis, der auf der Anzahl der Benutzer, die auf Ihr Angebot zugreifen, oder auf der Anzahl der belegten *Arbeitsplätze* basiert. Mithilfe des arbeitsplatzbasierten Modells können Sie die minimal und maximal zulässige Anzahl von Arbeitsplätzen auf Grundlage des Preises festlegen. Auf diese Weise können durch das Konfigurieren mehrerer Pläne unterschiedliche Preise auf Grundlage der Anzahl der Benutzer festgelegt werden.  Diese Felder sind optional. Wenn keine Angabe erfolgt, wird angenommen, dass die Anzahl der Arbeitsplätze unbegrenzt ist (mindestens 1 und maximal die vom System unterstützte maximale Anzahl). Diese Felder können beim Aktualisieren des Plans bearbeitet werden.

Nach der Veröffentlichung kann das Abrechnungspreismodell nicht mehr geändert werden. Darüber hinaus müssen alle Pläne für das gleiche Angebot dasselbe Preismodell aufweisen.

- **Abrechnungszeitraum**: „Monatlich“ oder „Jährlich“

Wählen Sie aus, wie häufig Kunden den aufgeführten Preis zahlen müssen. Es muss mindestens ein monatlicher oder jährlicher Preis angegeben werden. Es ist auch möglich, für Kunden beide Optionen verfügbar zu machen.

- **Preis**: „USD per month“ (US-Dollar pro Monat) oder „USD per year“ (US-Dollar pro Jahr)

Preise in der lokalen Währung (USD = US-Dollar) werden während der Einrichtung gemäß dem aktuellen Wechselkurs in die lokale Währung aller ausgewählten Märkte konvertiert. Überprüfen Sie vor der Veröffentlichung die Preise in jedem Markt, indem Sie die Preistabellenkalkulation exportieren. Wenn Sie benutzerdefinierte Preise in einem einzelnen Markt festlegen möchten, ändern und importieren Sie die Preistabellenkalkulation. Sie sind für die Überprüfung des Preismodells und diese Einstellungen verantwortlich.
**Sie müssen Ihre Preisänderungen speichern, damit die Preisdaten exportiert werden können.*

Überprüfen Sie Ihre Preise vor der Veröffentlichung sorgfältig, da nicht alle Elemente eines Plans nach seiner Veröffentlichung geändert werden können:

- Das Preismodell kann nach dem Veröffentlichen eines Plans nicht geändert werden.
- Nachdem ein Abrechnungszeitraum für einen Plan veröffentlicht wurde, kann er nicht mehr entfernt werden.
- Nachdem ein Preis für einen Markt in Ihrem Plan veröffentlicht wurde, kann er nicht mehr geändert werden.

### <a name="plan-audience"></a>Plan Audience (Planzielgruppe)

Sie können festlegen, dass jeder Plan für alle Benutzer oder nur für eine von Ihnen ausgewählte Zielgruppe angezeigt wird. Sie können mithilfe von Azure AD-Mandanten-IDs die Mitgliedschaft in dieser eingeschränkten Zielgruppe zuweisen.

#### <a name="privacy"></a>Datenschutz

- **This is a private plan** (Dies ist ein privater Plan) (optionales Kontrollkästchen)

Aktivieren Sie dieses Kontrollkästchen, um den Plan als privaten Plan festzulegen und nur für die von Ihnen ausgewählte eingeschränkte Zielgruppe anzuzeigen. Nachdem Sie den Plan als privaten Plan veröffentlicht haben, können Sie die Zielgruppe aktualisieren oder den Plan für alle Benutzer verfügbar machen. Nachdem ein Plan als für alle Benutzer sichtbar veröffentlicht wurde, muss er für alle Benutzer sichtbar bleiben. (Der Plan kann nicht wieder als privater Plan konfiguriert werden).

- **Restricted Audience (Tenant IDs)** (Eingeschränkte Zielgruppe (Mandanten-IDs))

Weisen Sie die Zielgruppe zu, die Zugriff auf den privaten Plan hat. Der Zugriff wird mit Mandanten-IDs zugewiesen. Dabei kann eine Beschreibung jeder zugewiesenen Mandanten-ID angegeben werden. Es können maximal 10 Mandanten-IDs oder, wenn eine CSV-Tabellenkalkulationsdatei importiert wird, 20.000 Mandanten-IDs von Kunden hinzugefügt werden.

Ein Mandant stellt eine Organisation dar, wobei die ID als global eindeutiger Bezeichner (Globally Unique Identifier, GUID – eine 128-Bit-Ganzzahl zum Identifizieren von Ressourcen) dargestellt wird. Es handelt sich um eine dedizierte Instanz von Azure AD, die ein Unternehmen oder ein App-Entwickler erhält, wenn das Unternehmen oder der App-Entwickler eine Beziehung zu Microsoft eingeht (z.B. die Registrierung für Azure, Microsoft Intune oder Microsoft 365). Jeder Azure AD-Mandant ist eindeutig und von anderen Azure AD-Mandanten getrennt. Um den Mandanten zu überprüfen, melden Sie sich beim Azure-Portal mit dem Konto an, das Sie für die Verwaltung Ihrer Anwendung verwenden möchten. Falls Sie einen Mandanten besitzen, werden Sie automatisch angemeldet, und der Mandantenname wird direkt unter dem Kontonamen angezeigt. Zeigen Sie rechts oben im Azure-Portal auf Ihren Kontonamen, um Ihren Namen, Ihre E-Mail-Adresse, Ihr Verzeichnis und Ihre Mandanten-ID (GUID) sowie Ihre Domäne anzuzeigen. Falls Ihr Konto mehreren Mandanten zugeordnet ist, können Sie durch Klicken auf Ihren Kontonamen ein Menü öffnen, über das Sie zwischen Mandanten wechseln können. Jeder Mandant besitzt eine eigene Mandanten-ID. Sie können auch unter [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com) die Mandanten-ID Ihrer Organisation mithilfe einer Domänennamen-URL nachschlagen.

In SaaS-Angeboten werden private Zielgruppen mithilfe von Mandanten-IDs definiert, in anderen Angebotstypen können jedoch Azure-Abonnement-IDs (die auch als GUIDs dargestellt werden) verwendet werden.

> [!NOTE]
> Eine private Zielgruppe (oder eingeschränkte Zielgruppe) unterscheidet sich von einer Vorschauzielgruppe. Sie können auf der Registerkarte **[Vorschau](#preview)** eine Vorschauzielgruppe definieren. Einer Vorschauzielgruppe wird Zugriff auf das Angebot gewährt, *bevor* es im Marketplace live veröffentlicht wird. Die Festlegung als private Zielgruppe gilt nur für einen bestimmten Plan. Für die private Zielgruppe können alle Pläne (private und nicht private Pläne) angezeigt werden, jedoch nur während des eingeschränkten Vorschauzeitraums, in dem der Plan getestet und überprüft wird.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Beispielliste der Pläne innerhalb eines Marketplace-Angebots

![Liste der Marketplace-Beispielpläne mit Hinweisen](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Testversion

Auf der Registerkarte **Testversion** können Sie eine Demonstration (oder „Testversion“) einrichten, sodass Kunden Ihr Angebot testen können, bevor sie sich zum Kauf entschließen. Erfahren Sie mehr im Artikel [Was ist die Testversion?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Wenn Sie nicht mehr eine Testversion für Ihr Angebot bereitstellen möchten, kehren Sie zur Seite **[Angebotseinrichtung](#offer-setup)** zurück, und deaktivieren Sie **Testversion aktivieren**.

### <a name="technical-configuration"></a>Technische Konfiguration
Die folgenden Typen von Testversionen sind verfügbar, jeweils mit eigenen Anforderungen an die technische Konfiguration.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logik-App](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (keine technische Konfiguration erforderlich)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technical configuration for Azure Resource Manager test drive (Technische Konfiguration für Azure Resource Manager-Testversion)

Eine Bereitstellungsvorlage, die alle Azure-Ressourcen enthält, aus denen Ihre Lösung besteht. In den für dieses Szenario geeigneten Produkten werden nur Azure-Ressourcen verwendet. Erfahren Sie mehr über das Einrichten einer [Azure Resource Manager-Testversion](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regionen** (erforderlich): Es gibt derzeit 26 von Azure unterstützte Regionen, in denen Ihre Testversion zur Verfügung gestellt werden kann. In der Regel sollten Sie Ihre Testversion in den Regionen zur Verfügung stellen, in denen Sie die größte Anzahl von Kunden erwarten, damit sie für eine optimale Leistung die nächstgelegene Region auswählen können. Sie müssen sicherstellen, dass in Ihrem Abonnement alle Ressourcen in jeder von Ihnen ausgewählten Region bereitgestellt werden können.

- **Instances**: Wählen Sie den Typ („Heiß“ oder „Kalt“) und die Anzahl der verfügbaren Instanzen aus. Diese wird mit der Anzahl der Regionen multipliziert, in denen Ihr Angebot verfügbar ist.

**Hot**: Dieser Typ von Instanz wird bereits bereitgestellt, und auf ihn kann in jeder ausgewählten Region zugegriffen werden. Die Kunden haben sofort Zugriff auf Instanzen vom Typ *Heiß* und müssen nicht auf die Bereitstellung warten. Der Nachteil dabei ist, dass diese Instanzen immer unter Ihrem Azure-Abonnement ausgeführt werden, sodass höhere Betriebszeitkosten anfallen. Es wird dringend empfohlen, mindestens eine Instanz des Typs *Heiß* festzulegen, da die meisten Kunden nicht auf eine vollständige Bereitstellung warten möchten. Wenn keine Instanz vom Typ *Heiß* verfügbar ist, nimmt die Verwendung durch die Kunden ab.

**Kalt**: Dieser Typ von Instanz stellt die Gesamtzahl der Instanzen dar, die pro Region bereitgestellt werden können. Bei Instanzen des Typs „Kalt“ muss die Bereitstellung der gesamten Resource Manager-Vorlage für die Testversion durchgeführt werden, wenn der Kunde die Testversion anfordert. Daher dauert das Laden von Instanzen des Typs *Kalt* weitaus länger als das Laden von Instanzen des Typs *Heiß*. Sie müssen jedoch nur für die Dauer der Testversion bezahlen. Instanzen vom Typ „Kalt“ werden *nicht* wie Instanzen vom Typ *Heiß* stets im Azure-Abonnement ausgeführt.

- **Test drive Azure Resource Manager template** (Vorlage für Azure Resource Manager-Testversion): Laden Sie die ZIP-Datei hoch, die die Azure Resource Manager-Vorlage enthält.  Im Schnellstartartikel [Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) erhalten Sie weitere Informationen zum Erstellen einer Azure Resource Manager-Vorlage.

- **Dauer der Testversion** (erforderlich): Geben Sie den Zeitraum, in dem die Testversion aktiv bleibt, als Anzahl von Stunden ein. Nach Ablauf dieses Zeitraums wird die Testversion automatisch beendet. Dies Dauer kann nur als ganze Zahl von Stunden festgelegt werden (Beispiel: „2“ ist zulässig, „1,5“ ist unzulässig).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technical configuration for Dynamics 365 test drive (Technische Konfiguration für Dynamics 365-Testversion)

Microsoft kann mit diesem Typ von Testversion die Dienstbereitstellung hosten und verwalten, um das Einrichten einer Testversion zu vereinfachen. Die Konfiguration für diesen Typ von gehosteter Testversion ist gleich, unabhängig davon ob die Testversion für Business Central, Customer Engagement oder Operations vorgesehen ist.

- **Maximale Anzahl gleichzeitiger Testversionen** (erforderlich): Legen Sie die maximale Anzahl von Kunden fest, die Ihre Testversion gleichzeitig verwenden können. Jeder gleichzeitige Benutzer nutzt eine Dynamics 365-Lizenz, während die Testversion aktiv ist. Daher müssen Sie sicherstellen, dass genügend Lizenzen für die festgelegte maximale Anzahl verfügbar sind. Empfohlener Wert liegt zwischen 3 und 5.

- **Dauer der Testversion** (erforderlich): Geben Sie den Zeitraum ein, in dem die Testversion aktiv bleibt, indem Sie die Anzahl der Stunden festlegen. Nach Ablauf dieser Anzahl von Stunden wird die Sitzung beendet, und sie belegt nicht mehr eine Ihrer Lizenzen. Der empfohlene Wert beträgt 2 bis 24 Stunden, abhängig von der Komplexität Ihres Angebots. Dies Dauer kann nur als ganze Zahl von Stunden festgelegt werden (Beispiel: „2“ ist zulässig, „1,5“ ist unzulässig).  Der Benutzer kann eine neue Sitzung anfordern, wenn die Zeit abgelaufen ist und er erneut auf die Testversion zugreifen möchte.

- **Instanz-URL** (erforderlich): Die URL, über die der Kunde die Testversion startet. Dies ist in der Regel die URL Ihrer Dynamics 365-Instanz, auf der Ihre App mit installierten Beispieldaten ausgeführt wird (z.B. https://testdrive.crm.dynamics.com) ).

- **Web-API-URL der Instanz** (erforderlich): Rufen Sie die Web-API-URL für Ihre Dynamics 365-Instanz ab, indem Sie sich bei Ihrem Microsoft 365-Konto anmelden, zu **Einstellungen** \&gt; **Anpassung** \&gt; **Entwicklerressourcen** \&gt; **Instance Web API (Service Root URL)** (Instanz-Web-API (Stamm-URL des Diensts)) navigieren und die hier angegebene URL kopieren (z.B. https://testdrive.crm.dynamics.com/api/data/v9.0) ).

- **Rollenname** (erforderlich): Geben Sie den Namen der Sicherheitsrolle ein, den Sie in der benutzerdefinierten Dynamics 365-Testversion definiert haben. Dieser wird dem Benutzer während der Dauer der Testversion zugewiesen (z.B. „Testversionsrolle“).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Technical configuration for Logic app test drive (Technische Konfiguration für Logik-App-Testversion)

Für alle benutzerdefinierten Produkte sollte dieser Typ von Testversionsbereitstellungsvorlage, der vielfältige komplexe Lösungsarchitekturen umfasst, verwendet werden. Um weitere Informationen über das Einrichten von Logik-App-Testversionen zu erhalten, besuchen Sie [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) und [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) auf GitHub (in englischer Sprache).

- **Region** (erforderlich, Dropdownliste für Einfachauswahl): Es gibt derzeit 26 von Azure unterstützte Regionen, in denen Ihre Testversion zur Verfügung gestellt werden kann. Die Ressourcen für Ihre Logik-App werden in der von Ihnen gewählten Region bereitgestellt. Wenn die Logik-App benutzerdefinierte Ressourcen umfasst, die in einer bestimmten Region gespeichert sind, müssen Sie diese Region hier auswählen. Dazu empfiehlt es sich, die Logik-App lokal in Ihrem Azure-Abonnement im Portal vollständig bereitzustellen und zu überprüfen, ob sie ordnungsgemäß ausgeführt wird, bevor Sie diese Auswahl vornehmen.

- **Maximale Anzahl gleichzeitiger Testversionen** (erforderlich): Legen Sie die maximale Anzahl von Kunden fest, die Ihre Testversion gleichzeitig verwenden können. Diese Testversionen sind bereits bereitgestellt, sodass Kunden sofort auf sie zugreifen können, ohne auf eine Bereitstellung warten zu müssen.

- **Dauer der Testversion** (erforderlich): Geben Sie den Zeitraum, in dem die Testversion aktiv bleibt, als Anzahl von Stunden ein. Nach Ablauf dieses Zeitraums wird die Testversion automatisch beendet.

- **Name der Azure-Ressourcengruppe** (erforderlich): Geben Sie den Namen der [Azure-Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) ein, in dem die Logik-App-Testversion gespeichert wird.

- **Azure logic app name** (Name der Azure-Logik-App) (erforderlich): Geben Sie den Namen der Logik-App ein, von der die Testversion dem Benutzer zugewiesen wird. Diese Logik-App muss in der oben angegebenen Azure-Ressourcengruppe gespeichert werden.

- **Deprovision logic app name** (Logik-App-Name für Aufhebung der Bereitstellung) (erforderlich): Geben Sie den Namen der Logik-App ein, mit der die Bereitstellung der Testversion aufgehoben wird, wenn sie vom Kunden nicht mehr verwendet wird. Diese Logik-App muss in der oben angegebenen Azure-Ressourcengruppe gespeichert werden.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technical configuration not required for Power BI test drives (Power BI-Testversionen erfordern keine technische Konfiguration)

Für Produkte, mit denen ein interaktives Power BI-Visual veranschaulicht werden soll, kann ein eingebetteter Link zum Freigeben eines benutzerdefinierten Dashboards als Testversion verwendet werden. Deshalb ist keine technische Konfiguration erforderlich. Erfahren Sie mehr über das Einrichten von[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)-Vorlagen-Apps.

### <a name="deployment-subscription-details"></a>Abonnementdetails für Bereitstellung der Testversion

Um die Testversion in Ihrem Auftrag bereitzustellen, erstellen Sie ein eigenes eindeutiges Azure-Abonnement, und stellen Sie es bereit. (Power BI-Testversionen nicht erforderlich).

- **Azure-Abonnement-ID** (erforderlich für Azure Resource Manager und Logik-Apps): Geben Sie die Abonnement-ID ein, um für Ressourcennutzungsberichte und Abrechnungszwecke Zugriff auf die Dienste Ihres Azure-Kontos zu gewähren. Es wird empfohlen, [ein eigenes Azure-Abonnement zu erstellen](https://docs.microsoft.com/azure/billing/billing-create-subscription), das für Testversionen verwendet wird, falls Sie es noch nicht getan haben. Sie finden die Azure-Abonnement-IDs, indem Sie sich beim [Azure-Portal](https://portal.azure.com/) anmelden und im Menü auf der linken Seite zu den **Abonnements** navigieren. Durch Auswahl der Registerkarte wird Ihre Abonnement-ID angezeigt (z.B. a83645ac-1234-5ab6-6789-1h234g764ghty).

- **ID des Azure AD-Mandanten** (erforderlich): Geben Sie Ihre [Mandanten-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id) von Azure Active Directory (AD) ein. Um die ID zu ermitteln, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, wählen Sie im linken Menü die Registerkarte „Active Directory“ aus, klicken Sie auf **Eigenschaften**, und suchen Sie dann die aufgeführte Nummer der **Verzeichnis-ID** (z.B. 50c464d3-4930-494c-963c-1e951d15360e). Sie können auch unter [https://www.whatismytenantid.com](https://www.whatismytenantid.com) die Mandanten-ID Ihrer Organisation mithilfe einer Domänennamen-URL nachschlagen.

- **Name des Azure AD-Mandanten** (erforderlich für Dynamics 365): Geben Sie den Namen Ihres Azure Active Directory (AD) ein. Um diesen Namen zu suchen, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Oben rechts wird der Mandantenname unter Ihrem Kontonamen angegeben.

- **Azure AD-App-ID** (erforderlich): Geben Sie die [Anwendungs-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) von Azure Active Directory (AD) ein. Um die ID zu ermitteln, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, wählen Sie im linken Menü die Registerkarte „Active Directory“ aus, klicken Sie auf **App-Registrierungen**, und suchen Sie dann die aufgeführte Nummer der **Anwendungs-ID** (z.B. 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-App-Schlüssel** (erforderlich): Geben Sie den [Anwendungsschlüssel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) von Azure Active Directory (AD) ein. Um dieser ID zu ermitteln, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, wählen Sie im linken Menü die Registerkarte „Active Directory“ aus, klicken Sie auf **App-Registrierungen**, und wählen Sie dann **Einstellungen** > **Schlüssel** aus.

Klicken Sie auf **Speichern**, bevor Sie mit dem nächsten Abschnitt fortfahren!

### <a name="test-drive-listings-optional"></a>Test drive listings (Testversionslisten) (optional)

Auf der Registerkarte **Test drive listings** (Testversionslisten) unter **Testversion** werden die Sprachen (und Märkte) angezeigt, in denen Ihre Testversion verfügbar ist. Derzeit ist nur der Standort „Englisch (USA)“ verfügbar. Auf dieser Seite werden zudem der Status der sprachspezifischen Liste und das Datum/die Uhrzeit der Hinzufügung angezeigt. Sie müssen für jede Sprache/jeden Markt die Testversionsdetails (Beschreibung, Benutzerhandbuch, Videos usw.) definieren.

- **Beschreibung:** (erforderlich): Beschreiben Sie Ihre Testversion. Geben Sie an, was vorgeführt wird, nennen Sie die Ziele, mit denen der Benutzer experimentieren soll, sowie die zu erkundenden Funktionen, und geben Sie relevante Informationen an, die dem Benutzer helfen zu entscheiden, ob er Ihr Angebot erwerben soll. In diesem Feld können bis zu 3.000 Zeichen Text eingegeben werden. 

- **Zugriffsinformationen** (für Azure Resource Manager und Logik-Testversionen erforderlich): Erläutern Sie, was ein Kunde wissen muss, um auf die Testversion zuzugreifen und sie zu verwenden. Beschreiben Sie ein Szenario für die Verwendung Ihres Angebots, und teilen Sie genau mit, was der Kunde wissen sollte, um auf die Funktionen in der gesamten Testversion zuzugreifen. In diesem Feld können bis zu 10.000 Zeichen Text eingegeben werden.

- **Benutzerhandbuch** (erforderlich): Eine ausführliche Beschreibung der Verwendung Ihrer Testversion. Das Benutzerhandbuch sollte genau beschreiben, was der Kunde durch die Erkundung der Testversion erlangen soll, und als Referenz für die Fragen des Kunden dienen. Die Datei muss das PDF-Format aufweisen und nach dem Hochladen mit einem Namen (max. 255 Zeichen) versehen werden.

- **Videos: Videos hinzufügen** (optional): Videos können auf YouTube oder Vimeo hochgeladen werden, und hier kann mit einem Link und einer Miniaturansicht (533 x 324 Pixel) auf sie verwiesen werden. So erhalten die Kunden Informationen zu einzelnen Schritten, um ein besseres Verständnis der Testversion zu erlangen. Unter anderem können Sie ihnen zeigen, wie sie die Funktionen ihres Angebots erfolgreich verwenden, und Szenarien erläutern, in denen die Vorteile hervorgehoben werden.
  - **Name** (erforderlich)
  - **URL (nur YouTube oder Vimeo)** (erforderlich)
  - **Thumbnail (533 x 324px)** (Miniaturansicht (533 x 324 px)): Die Bilddatei muss im PNG-Format vorliegen.

Klicken Sie auf **Speichern**, nachdem Sie die Angaben in diesen Feldern vorgenommen haben.

## <a name="publish"></a>Veröffentlichen

#### <a name="submit-offer-to-preview"></a>Übermitteln des Angebots für die Vorschau

Nachdem Sie alle erforderlichen Abschnitte des Angebots abgeschlossen haben, klicken Sie rechts oben im Portal auf **Veröffentlichen**. Sie werden zur Seite **Review and publish** (Überprüfen und veröffentlichen) weitergeleitet. 

Wenn Sie das Angebot zum ersten Mal veröffentlichen, haben Sie folgende Möglichkeiten:

- Anzeigen des Abschlussstatus für die einzelnen Abschnitte des Angebots.
    - *Nicht gestartet*: Der Abschnitt wurde nicht bearbeitet und muss abgeschlossen werden.
    - *Unvollständig*: Der Abschnitt enthält Fehler, die behoben werden müssen, oder erfordert eine Ergänzung der Informationen. Kehren Sie zu dem/den Abschnitten zurück, und nehmen Sie eine Aktualisierung vor.
    - *Abgeschlossen*: Der Abschnitt ist abgeschlossen. Alle erforderlichen Daten wurden angegeben, und es sind keine Fehler vorhanden. Alle Abschnitte des Angebots müssen abgeschlossen sein, bevor Sie das Angebot einreichen können.
- Geben Sie dem Zertifizierungsteam Testanweisungen, um sicherzustellen, dass Ihre App ordnungsgemäß getestet wird, sowie ergänzende Hinweise, die das Verständnis Ihrer App erleichtern.
- Senden Sie dazu das Angebot zur Veröffentlichung, indem Sie auf **Senden** klicken. Wir senden Ihnen eine E-Mail, um Ihnen mitzuteilen, wann eine Vorschauversion des Angebots verfügbar ist, damit Sie es überprüfen und genehmigen können. Sie müssen zum Partner Center zurückkehren und **Live schalten** auswählen, um das Angebot für eine öffentliche Zielgruppe (oder bei einem privaten Angebot für eine private Zielgruppe) zu veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](./update-existing-offer.md)
