---
title: Planen eines Azure-Anwendungsangebots für den kommerziellen Marketplace
description: Erfahren Sie, wie Sie ein neues Azure-Anwendungsangebot zum Auflisten oder Verkaufen im Azure Marketplace oder über das CSP-Programm (Cloud Solution Provider) über das Portal für den kommerziellen Marketplace in Microsoft Partner Center planen.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 45bc3aaf5217c626e2593b4eda861eaace2a8be2
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620408"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Planen eines Azure-Anwendungsangebots für den kommerziellen Marketplace

In diesem Artikel werden die verschiedenen Optionen und Anforderungen für das Veröffentlichen eines Azure-Anwendungsangebots im kommerziellen Microsoft-Marketplace erläutert.

## <a name="before-you-begin"></a>Voraussetzungen

Das Entwerfen, Erstellen und Testen dieser Azure-Anwendungsangebote erfordert technische Kenntnisse in Bezug auf die Azure-Plattform und -Technologien, um das Angebot zu erstellen. Ihr Entwicklerteam sollte sich mit den folgenden Microsoft-Technologien auskennen:

- Grundkenntnisse in Bezug auf [Azure-Dienste](https://azure.microsoft.com/services/).
- [Entwerfen und Erstellen der Architektur für Azure-Anwendungen](https://azure.microsoft.com/solutions/architecture/).
- Erweiterte Grundkenntnisse in Bezug auf [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage) und [Azure-Netzwerk](https://azure.microsoft.com/services/?filter=networking#networking).
- Erweiterte Grundkenntnisse in Bezug auf [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
- Fundierte Kenntnisse zu [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Technische Dokumentation und Ressourcen

Nutzen Sie die folgenden Ressourcen, um Ihr Azure-Anwendungsangebot für den kommerziellen Marketplace zu planen.

- [Verstehen von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md)
- Schnellstartanleitungen:
    - [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/)
    - [Leitfaden zu bewährten Methoden für Azure-Vorlagen](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Veröffentlichen der Anwendungsdefinition](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Bereitstellen einer Dienstkatalog-App](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Tutorials:
    - [Erstellen von Definitionsdateien](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Beispiele:
    - [Azure-Befehlszeilenschnittstelle](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Verwaltete Anwendungslösungen](../azure-resource-manager/managed-applications/sample-projects.md)

Das Video [Erstellen von Lösungsvorlagen und verwalteten Anwendungen für den Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) enthält eine ausführliche Einführung in den Angebotstyp „Azure-Anwendung“:

- Welche Angebotstypen sind verfügbar?
- Welche technischen Ressourcen sind erforderlich?
- Erstellen einer Azure Resource Manager-Vorlage
- Entwickeln und Testen der App-Benutzeroberfläche
- Veröffentlichen des App-Angebots
- Der Anwendungsüberprüfungsprozess

### <a name="suggested-tools"></a>Vorgeschlagene Tools

Wählen Sie eine oder beide der folgenden Skriptumgebungen als Unterstützung bei der Verwaltung Ihrer Azure-Anwendung aus:

- [Azure PowerShell](/powershell/azure/)
- [Azure-Befehlszeilenschnittstelle](/cli/azure)

Es empfiehlt sich, dass Sie Ihrer Entwicklungsumgebung die folgenden Tools hinzufügen:

- [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    - Erweiterung: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Erweiterung: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Erweiterung: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Die verfügbaren Tools finden Sie auf der Seite [Azure-Entwicklertools](https://azure.microsoft.com/tools/). Und wenn Sie Visual Studio verwenden, sehen Sie sich den [Visual Studio Marketplace](https://marketplace.visualstudio.com/) an.

## <a name="listing-options"></a>Listungsoptionen

Nachdem das Angebot veröffentlicht wurde, werden die Auflistungsoptionen als Schaltfläche in der linken oberen Ecke der Auflistungsseite Ihres Angebots angezeigt. Der folgende Screenshot zeigt z. B. die Auflistungsseite eines Angebots im Azure Marketplace mit der Schaltfläche _Jetzt kaufen_. Wenn Sie eine Testversion anbieten, wird auch die Schaltfläche _Testversion_ angezeigt.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Darstellung einer Auflistungsseite im Azure Marketplace":::

## <a name="test-drive"></a>Testversion

Sie können eine Testversion für Ihr Azure-Anwendungsangebot aktivieren, über die Kunden Ihr Angebot vor dem Erwerb ausprobieren können. Weitere Informationen zu Testversionen finden Sie unter [Was ist eine Testversion?](what-is-test-drive.md) Weitere Informationen zum Konfigurieren verschiedener Arten von Testversionen finden Sie unter [Technische Konfiguration der Testversion](test-drive-technical-configuration.md).

Sie können auch mehr über die [bewährten Methoden für Testversionen](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) erfahren und die PDF-Datei mit einer [Übersicht über Testversionen](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) herunterladen (stellen Sie hierzu sicher, dass Ihr Popupblocker deaktiviert ist).

> [!NOTE]
> Informationen, die der Benutzer selbst beim Durchblättern bemerken sollte. Da alle Azure-Anwendungen mit einer Azure Resource Manager-Vorlage implementiert werden, ist die einzige Art von Testversion, die für Azure-Anwendungen verfügbar ist, eine [Azure Resource Manager-Testversion](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Kundenleads

Um Kundeninformationen zu sammeln, müssen Sie Ihr Angebot mit Ihrem CRM-System (Customer Relationship Management) verbinden. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Onlineshop, auf dem der Kunde Ihr Angebot gefunden hat, werden an das von Ihnen konfigurierte CRM-System gesendet. Der kommerzielle Marketplace unterstützt eine Vielzahl von CRM-Systemen. Außerdem können Sie optional eine Azure-Tabelle verwenden oder einen HTTPS-Endpunkt mithilfe von Power Automate konfigurieren.

Eine CRM-Verbindung kann während der Angebotserstellung zu einem beliebigen Zeitpunkt hinzugefügt oder geändert werden. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Kategorien und Unterkategorien

Sie können zwischen ein und zwei Kategorien auswählen, um Ihr Angebot in den entsprechenden Suchbereichen des kommerziellen Marketplace zu platzieren. Sie können bis zu zwei Unterkategorien für jede primäre oder sekundäre Kategorie auswählen. Eine vollständige Liste der Kategorien und Unterkategorien finden Sie unter [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md#categories).

## <a name="legal-contracts"></a>Verträge

Zur Vereinfachung des Beschaffungsprozesses für Kunden und zur Reduzierung der rechtlichen Komplexität für Softwareanbieter stellt Microsoft eine Vorlage für Standardverträge bereit, die Sie für Ihre Angebote im kommerziellen Marketplace verwenden können. Wenn Sie Ihre Software unter dem Standardvertrag anbieten, müssen Kunden ihn nur einmal lesen und akzeptieren, und Sie müssen keine benutzerdefinierten Geschäftsbedingungen erstellen.

Wenn Sie sich für die Verwendung des Standardvertrags entscheiden, können Sie dem Standardvertrag universelle Zusatzbedingungen und bis zu 10 benutzerdefinierte Ergänzungen hinzufügen. Sie können anstelle des Standardvertrags Ihre eigenen Geschäftsbedingungen verwenden. Diese Details werden auf der Seite **Eigenschaften** verwaltet. Ausführliche Informationen finden Sie unter [Standardvertrag für den kommerziellen Microsoft-Marketplace](standard-contract.md).

> [!NOTE]
> Nachdem Sie ein Angebot mit dem Standardvertrag für den kommerziellen Marketplace veröffentlicht haben, können Sie keine eigenen benutzerdefinierten Geschäftsbedingungen mehr verwenden. Es handelt sich um ein „oder“-Szenario. Entweder bieten Sie Ihre Lösung im Rahmen des Standardvertrags oder Ihrer eigenen Geschäftsbedingungen an. Wenn Sie die Bedingungen des Standardvertrags ändern möchten, können Sie dies über Zusatzvereinbarungen für den Standardvertrag erreichen.

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

Wenn Sie ein neues Azure-Anwendungsangebot in Partner Center erstellen, geben Sie Text, Bilder, optionale Videos und andere Details auf der Seite „Angebotsliste“ ein. Diese Informationen sind für Ihre Kunden sichtbar, wenn sie den Angebotseintrag im Azure Marketplace entdecken, wie im folgenden Beispiel gezeigt.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Veranschaulicht, wie dieses Angebot im Azure Marketplace angezeigt wird.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Logo
2. Kategorien
3. Supportadresse (Link)
4. Nutzungsbedingungen
5. Adresse der Datenschutzrichtlinie (Link)
6. Angebotsname
7. Zusammenfassung
8. BESCHREIBUNG
9. Screenshots/Videos

Der folgende Screenshot zeigt, wie Angebotsinformationen im Azure-Portal angezeigt werden:

[![Veranschaulicht, wie dieses Angebot im Azure-Portal angezeigt wird.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Titel
2. BESCHREIBUNG
3. Nützliche Links
4. Screenshots

> [!NOTE]
> Der Inhalt der Angebotsauflistung muss nicht in englischer Sprache verfasst werden, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt.

Um das Angebot einfacher zu gestalten, können Sie einige dieser Elemente vorab vorbereiten. Die folgenden Elemente sind erforderlich, sofern nicht anders angegeben:

- **Name**: Dieser Name wird als Titel Ihrer Angebotsliste im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis (mit Ausnahme des Markenzeichen- und Copyrightsymbols) enthalten und muss auf 50 Zeichen begrenzt sein.
- **Zusammenfassung der Suchergebnisse**: Beschreiben Sie den Zweck oder die Funktion des Angebots in einem Satz ohne Formatierungen oder Zeilenumbrüche und mit höchstens 100 Zeichen. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus Auflistungen im kommerziellen Marketplace erfasst werden.
- **Kurze Beschreibung:** Geben Sie bis zu 256 Zeichen unformatierten Text ein. Diese Zusammenfassung wird auf der Detailseite Ihres Angebots angezeigt.
- **Beschreibung**: Diese Beschreibung wird in der Übersicht der Auflistungen im Azure Marketplace angezeigt. Sie können z. B ein Wertversprechen, wichtige Vorteile, eine Zielgruppe, Kategorie- oder Branchenzuordnungen, Möglichkeiten für In-App-Käufe, Kundenanforderungen oder -probleme, die mit diesem Angebot gelöst werden, erforderliche Veröffentlichungen und einen Link zu weiteren Informationen eingeben.

    Dieses Textfeld enthält Rich-Text-Editor-Steuerelemente, mit denen Sie die Beschreibung ansprechender gestalten können. Die Beschreibung kann mithilfe von HTML-Tags formatiert werden. Sie können in diesem Feld bis zu 3.000 Zeichen Text eingeben, einschließlich HTML-Markup und Leerzeichen. Weitere Tipps finden Sie unter [Erstellen einer interessanten App-Beschreibung](/windows/uwp/publish/write-a-great-app-description) und [In Angebotsbeschreibungen des kommerziellen Marketplace unterstützte HTML-Tags](supported-html-tags.md).

- **Suchbegriffe** (optional): Geben Sie bis zu drei Suchschlüssel ein, mit denen Kunden in den Onlineshops nach Ihrem Angebot suchen können. Um optimale Ergebnisse zu erzielen, sollten Sie diese Schlüsselwörter auch in ihrer Beschreibung verwenden. Der **Name** und die **Beschreibung** des Angebots müssen nicht aufgenommen werden. Dieser Text wird automatisch in die Suche eingeschlossen.
- **Link zur Datenschutzrichtlinie**: Die URL zur Datenschutzrichtlinie Ihres Unternehmens. Sie müssen eine gültige Datenschutzrichtlinie bereitstellen und sicherstellen, dass die App die Datenschutzgesetze und -bestimmungen erfüllt.
- **Nützliche Links** (optional): Sie können für Benutzer Ihres Angebots Links zu verschiedenen Ressourcen bereitstellen. Beispiele: Foren, FAQ und Versionshinweise.
- **Kontaktinformationen**: Die folgenden Kontakte müssen in Ihrem Unternehmen festgelegt werden:
  - **Supportkontakt**: Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse für Microsoft-Partner an. Diese werden dann von Kunden bei der Eröffnung von Supporttickets verwendet. Sie müssen auch die URL für Ihre Supportwebsite einschließen.
  - **Technischer Ansprechpartner**: Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse an, die Microsoft direkt verwenden kann, wenn es Probleme mit Ihrem Angebot gibt. Diese Kontaktinformationen sind nicht im kommerziellen Marketplace aufgeführt.
  - **Ansprechpartner für CSP-Programm** (optional): Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse an, wenn Sie sich für das CSP-Programm (Cloud Solution Provider) entscheiden, damit Sie bei Fragen von Partnern kontaktiert werden können. Sie können auch eine URL zu Ihren Marketingmaterialien hinzufügen.
- **Medien – Logos**: Geben Sie eine PNG-Datei für das **große** Logo an. Dies wird in Partner Center verwendet, um ein **kleines** und ein **mittleres** Logo zu erstellen. Sie können diese Logos später durch andere Bilder ersetzen.
  - Groß (von 216 × 216 bis 350 × 350 Pixel, erforderlich)
  - Mittel (90 × 90 Pixel, optional)
  - Klein (48 × 48 Pixel, optional)

  Diese Logos werden an unterschiedlichen Stellen in den Onlineshops verwendet:
  - Das kleine Logo wird in Azure Marketplace-Suchergebnissen angezeigt.
  - Das mittelgroße Logo wird angezeigt, wenn Sie eine neue Ressource in Microsoft Azure erstellen.
  - Das große Logo wird auf Ihrer Seite mit dem Angebotseintrag im Azure Marketplace angezeigt.

  Befolgen Sie die folgenden Richtlinien für Ihre Logos:

  - Die Farbpalette des Azure-Designs ist einfach und geradlinig. Beschränken Sie die Anzahl der Primär- und Sekundärfarben auf Ihrem Logo.
  - Die Designfarben des Azure-Portals sind Weiß und Schwarz. Verwenden Sie diese Farben nicht als Hintergrundfarbe für Ihr Logo. Verwenden Sie eine Farbe, die Ihr Logo im Portal ideal zur Geltung bringt. Sie sollten einfache Primärfarben verwenden.
  - Wenn Sie einen transparenten Hintergrund verwenden, stellen Sie sicher, dass das Logo und der Text nicht weiß, schwarz oder blau sind.
  - Das Erscheinungsbild Ihres Logos sollte klar und direkt sein, und Sie sollten im Logo und Hintergrund auf Farbverläufe verzichten. Platzieren Sie keinen Text auf dem Logo, auch nicht Ihren Firmen- oder Markennamen. Bei unscharfen Bildern wird Ihre Übermittlung abgelehnt.
  - Stellen Sie sicher, dass das Logo nicht verzerrt wird.

- **Medien – Screenshots** (optional): Es wird empfohlen, Screenshots hinzuzufügen, die zeigen, wie Ihr Angebot funktioniert. Fügen Sie gemäß den folgenden Anforderungen bis zu fünf Screenshots hinzu, die Ihr Angebot veranschaulichen:
  - 1280 x 720 Pixel
  - PNG-Datei
  - Beschriftung erforderlich
- **Medien – Videos** (optional): Fügen Sie gemäß den folgenden Anforderungen bis zu fünf Videos hinzu, die Ihr Angebot veranschaulichen:
  - Name
  - URL: Video darf nur auf YouTube oder Vimeo gehostet werden.
  - Miniaturansicht: PNG-Datei (1280 x 720)

> [!NOTE]
> Ihr Angebot muss für die Veröffentlichung im kommerziellen Marketplace die allgemeinen [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#100-general.md) erfüllen.

## <a name="preview-audience"></a>Vorschauzielgruppe

Eine Vorschauzielgruppe erhält vor der Liveveröffentlichung in den Onlineshops Zugriff auf Ihr Angebot, um die End-to-End-Funktionalität vorab zu testen.

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einem privaten Plan. Ein privater Plan ist ein Angebot, das nur einem bestimmten Publikum zur Verfügung steht. Darüber hinaus können Sie einen benutzerdefinierten Plan mit bestimmten Kunden aushandeln.

Sie legen Ihre Vorschauzielgruppe mithilfe von Azure-Abonnement-IDs fest, begleitet jeweils von einer optionalen Beschreibung. Keines dieser Felder ist für Kunden sichtbar.

## <a name="technical-configuration"></a>Technische Konfiguration

Für verwaltete Anwendungen, die Messungsereignisse mithilfe der [Marketplace-Messungsdienst-APIs](partner-center-portal/marketplace-metering-service-apis.md) ausgeben, müssen Sie die Identität angeben, die Ihr Dienst für das Ausgeben von Messungsereignissen verwendet.

Diese Konfiguration ist erforderlich, wenn Sie das [Batchnutzungsereignis](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event) verwenden möchten. Wenn Sie [Nutzungsereignisse](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event) übermitteln möchten, können Sie [Instance Metadata Service](../active-directory/managed-identities-azure-resources/overview.md) verwenden, um das [JWT-Bearertoken (JSON Web Token)](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token) abzurufen.

- **Azure Active Directory-Mandanten-ID** (erforderlich): Im Azure-Portal müssen Sie eine [Azure AD-App (Active Directory) erstellen](../active-directory/develop/howto-create-service-principal-portal.md), damit überprüft werden kann, ob die Verbindung zwischen unseren beiden Diensten über eine authentifizierte Kommunikation erfolgt. Um die [Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) für Ihre Azure Active Directory-App (Azure AD) zu ermitteln, navigieren Sie in Azure Active Directory zum Blatt [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade). Wählen Sie in der Spalte **Anzeigename** die App aus. Suchen Sie nach **Eigenschaften** und nach der **Verzeichnis-ID (Mandant)** (z. B. `50c464d3-4930-494c-963c-1e951d15360e`).
- **Azure Active Directory-Anwendungs-ID** (erforderlich): Sie benötigen auch die [Anwendungs-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) und einen Authentifizierungsschlüssel. Um die Anwendungs-ID zu ermitteln, wechseln Sie in Azure Active Directory zum Blatt [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade). Wählen Sie in der Spalte **Anzeigename** die App aus, und suchen Sie dann nach der **Anwendungs-ID (Client)** (z. B. `50c464d3-4930-494c-963c-1e951d15360e`). Um den Authentifizierungsschlüssel zu suchen, wechseln Sie zu **Einstellungen**, und wählen Sie **Schlüssel** aus. Sie müssen eine Beschreibung und eine Dauer angeben und erhalten dann einen Zahlenwert.

> [!NOTE]
> Die Azure-Anwendungs-ID wird Ihrer Herausgeber-ID zugeordnet und kann nur mit diesem Herausgeberkonto wiederverwendet werden.

## <a name="additional-sales-opportunities"></a>Zusätzliche Verkaufschancen

Sie haben die Möglichkeit, sich für von Microsoft unterstützte Marketing- und Vertriebskanäle zu entscheiden. Wenn Sie Ihr Angebot im Partner Center erstellen, werden gegen Ende des Prozesses zwei Registerkarten angezeigt:

- **Verkauf über CSPs**: Verwenden Sie diese Option, um Microsoft CSP-Partnern (Cloud Solution Providers) zu ermöglichen, Ihre Lösung als Teil eines Bundleangebots weiterzuverkaufen. Weitere Informationen finden Sie unter [Cloud-Lösungsanbieter-Programm](./cloud-solution-providers.md).
- **Co-Selling mit Microsoft**: Mit dieser Option können die Microsoft-Verkaufsteams Ihre für IP-Co-Selling in Frage kommende Lösung in Betracht ziehen, wenn sie Kundenanforderungen evaluieren. Ausführliche Informationen zum Vorbereiten Ihres Angebots auf die Evaluierung finden Sie unter [Co-Selling-Optionen im kommerziellen Marketplace](commercial-marketplace-co-sell.md). Einzelheiten zu den Anforderungen an das IP-Co-Selling finden Sie unter [Requirements for Co-sell Status](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status) (Anforderungen für den Co-Selling-Status). Weitere Informationen zum Vermarkten Ihres Angebots über die Microsoft CSP-Partnerkanäle finden Sie unter [Cloud Solution Provider](cloud-solution-providers.md).

Weitere Informationen finden Sie unter [Weiten Sie Ihr Cloudgeschäft mit Azure Marketplace aus](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Pläne

Für Azure-Anwendungsangebote ist mindestens ein Plan erforderlich. Ein Plan definiert den Umfang und die Einschränkungen einer Lösung sowie ggf. die zugehörigen Preise. Sie können mehrere Pläne für Ihr Angebot erstellen, um Ihren Kunden verschiedene technische und preisliche Optionen zu bieten.

Eine allgemeine Anleitung zu Plänen, einschließlich Preismodellen und privaten Plänen, finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](plans-pricing.md). In den folgenden Abschnitten erhalten Sie zusätzliche Informationen zu Azure-Anwendungsangeboten.

### <a name="types-of-plans"></a>Plantypen

Es gibt zwei Typen von Azure-Anwendungsplänen: _Lösungsvorlage_ und _verwaltete Anwendung_. Beide Plantypen unterstützen die Automatisierung der Bereitstellung und die Konfiguration einer Lösung über eine einzelne VM hinaus. Sie können die Bereitstellung mehrerer Ressourcen automatisieren, z. B. VMs, Netzwerke und Speicherressourcen, um komplexe Lösungen wie IaaS-Lösungen bereitzustellen. Bei beiden Plantypen können viele verschiedene Arten von Azure-Ressourcen genutzt werden, einschließlich u. a. VMs.

- Pläne für **Lösungsvorlagen** zählen zu den wichtigsten Optionen für die Veröffentlichung von Lösungen im kommerziellen Marketplace. Pläne für Lösungsvorlagen sind keine Transaktionsangebote im kommerziellen Marketplace. Sie können jedoch zum Bereitstellen bezahlter VM-Angebote verwendet werden, die über den kommerziellen Marketplace abgerechnet werden. Verwenden Sie den Plantyp für Lösungsvorlagen, wenn der Kunde die Lösung verwaltet und die Transaktionen über einen anderen Plan abgerechnet werden. Weitere Informationen zum Erstellen von Lösungsvorlagen finden Sie unter [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md).
- Verwenden Sie Pläne für **verwaltete Anwendungen**, um mühelos vollständig verwaltete, schlüsselfertige Anwendungen für Ihre Kunden zu erstellen und bereitzustellen. Sie verfügen über die gleichen Funktionen wie Pläne für Lösungsvorlagen mit einigen wichtigen Unterschieden:
    - Die Ressourcen werden in einer Ressourcengruppe bereitgestellt und vom Herausgeber der App verwaltet. Die Ressourcengruppe befindet sich zwar im Abonnement des Verbrauchers, eine Identität im Mandanten des Herausgebers hat jedoch Zugriff auf die Ressourcengruppe. 
    - Als Herausgeber geben Sie die Kosten für den fortlaufende Support der Lösung an, und Transaktionen erfolgen über den kommerziellen Marketplace.
 
    Verwenden Sie den Plantyp für verwaltete Anwendungen, wenn Sie oder Ihr Kunde möchten, dass die Lösung von einem Partner verwaltet wird, oder wenn Sie eine abonnementbasierte Lösung bereitstellen. Weitere Informationen zu den Vorteilen und Typen von verwalteten Anwendungen finden Sie unter [Übersicht über verwaltete Azure-Anwendungen](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Planen einer Lösungsvorlage finden Sie unter [Planen einer Lösungsvorlage für ein Azure-Anwendungsangebot](plan-azure-app-solution-template.md).
- Weitere Informationen zum Planen einer verwalteten Azure-Anwendung finden Sie unter [Planen eines Azure-Anwendungsangebots für den kommerziellen Marketplace](plan-azure-app-managed-app.md).