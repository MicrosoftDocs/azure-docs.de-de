---
title: Transaktionsfunktionen im kommerziellen Microsoft Marketplace
description: In diesem Artikel werden Überlegungen zur Preisgestaltung, Abrechnung, Rechnungsstellung und Auszahlung für die Transaktionsoption im kommerziellen Marketplace beschrieben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 59768e4fc04613a5ecac6a76059382499b7aaa86
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986461"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Transaktionsfunktionen im kommerziellen Marketplace

In diesem Artikel werden Überlegungen zur Preisgestaltung, Abrechnung, Rechnungsstellung und Auszahlung im kommerziellen Microsoft-Marketplace beschrieben.

## <a name="transactions-by-listing-option"></a>Transaktionen nach Auflistungsoption

Für die Verwaltung von Softwarelizenztransaktionen für Angebote im kommerziellen Marketplace ist entweder der Herausgeber oder Microsoft verantwortlich. Wer die Transaktion verwaltet, wird durch die Auflistungsoption bestimmt, die Sie für Ihr Angebot auswählen. Informationen zur Verfügbarkeit und Erläuterungen der einzelnen Veröffentlichungsoptionen finden Sie unter [Einführung in Auflistungsoptionen](determine-your-listing-type.md).

### <a name="contact-me-free-trial-and-byol-options"></a>Optionen „Kontaktaufnahme“, „Kostenlose Testversion“ und „BYOL“

Herausgeber können die Optionen _Kontaktaufnahme_ und _Kostenlose Testversion_ für Werbezwecke und zur Neukundengewinnung auswählen. Bei manchen Angebotstypen können Sie als Herausgeber die Option „BYOL“ (Bring Your Own License) auswählen, damit Kunden mit einer Lizenz, die sie direkt bei Ihnen gekauft haben, ein Abonnement für Ihr Angebot erwerben können. Bei diesen Optionen ist Microsoft nicht direkt an den Softwarelizenztransaktionen des Herausgebers beteiligt, und es fallen keine damit verbundenen Transaktionsgebühren an.

Herausgeber sind dafür verantwortlich, alle Aspekte der Softwarelizenztransaktion zu unterstützen. Dazu zählen u. a. Auftrag, Erfüllung, Messung, Abrechnung, Rechnungstellung, Zahlung und Erfassung. Bei der Auflistungsoption „Kontaktaufnahme“ behalten Herausgeber 100 % der vom Kunden eingezogenen Softwarelizenzgebühren ein.

### <a name="transact-publishing-option"></a>Veröffentlichungsoption „Transaktion“

Wenn Sie sich dafür entscheiden, über Microsoft zu verkaufen, profitieren Sie von den E-Commerce-Funktionen von Microsoft und nutzen die End-to-End-Umgebung für Ermittlung und Auswertung bis hin zu Kauf und Implementierung. Bei einem transaktionsfähigen Angebot handelt es sich um ein Angebot, bei dem Microsoft den Geldfluss für eine Softwarelizenz im Namen des Herausgebers vereinfacht. Transaktionsfähige Angebote werden einem vorhandenen Microsoft-Abonnement in Rechnung gestellt oder per Kreditkarte abgerechnet, sodass Microsoft im Namen des Herausgebers Marketplace-Transaktionen in der Cloud hosten kann.

Sie wählen die Option „Transaktion“ aus, wenn Sie in Partner Center ein neues Angebot erstellen. Diese Option wird nur angezeigt, wenn die Option „Transaktion“ für Ihren Angebotstyp verfügbar ist.

## <a name="transact-overview"></a>Übersicht über „Transaktion“

Bei Verwendung der Option „Transaktion“ ermöglicht Microsoft den Verkauf von Drittanbietersoftware und die Bereitstellung einiger Angebotstypen für das Azure-Abonnement des Kunden. Sie als Herausgeber müssen bei der Auswahl eines Abrechnungsmodells für ein Angebot die Abrechnung der Infrastrukturgebühren und Ihrer eigenen Softwarelizenzgebühren berücksichtigen.

Die Veröffentlichungsoption „Transaktion“ wird derzeit für die folgenden Angebotstypen unterstützt:

- Virtuelle Computer
- Azure-Anwendungen
- SaaS-Anwendungen

### <a name="billing-infrastructure-costs"></a>Abrechnung von Infrastrukturkosten

Für **virtuelle Computer** und **Azure-Anwendungen** werden die Nutzungsgebühren für Azure-Infrastruktur im Azure-Abonnement des Kunden in Rechnung gestellt. Auf der Rechnung des Kunden werden die Infrastrukturnutzungsgebühren separat von den Lizenzgebühren des Softwareanbieters ausgewiesen.

Bei **SaaS-Apps** müssen Sie als Herausgeber die Nutzungsgebühren für Azure-Infrastruktur und Softwarelizenzgebühren als einen Kostenposten verrechnen.  Diese werden dem Kunden als Pauschalgebühr angegeben. Die Nutzung der Azure-Infrastruktur wird verwaltet und dem Herausgeber direkt in Rechnung gestellt. Tatsächliche Nutzungsgebühren für die Infrastruktur werden dem Kunden nicht angezeigt. Herausgeber entscheiden sich in der Regel dafür, die Nutzungsgebühren für Azure-Infrastruktur in ihren Softwarelizenzpreis einfließen zu lassen. Softwarelizenzgebühren werden nicht gemessen oder nutzungsabhängig ermittelt.

## <a name="pricing-models"></a>Preismodelle

Je nach verwendeter Transaktionsoption fallen folgende Abonnementgebühren an:

- **Jetzt herunterladen (kostenlos)** : Keine Softwarelizenzgebühren. Bei Nutzung eines kostenlosen Angebots fallen für den Kunden keine Azure Marketplace-Gebühren an. Kostenlose Angebote können nicht in ein kostenpflichtiges Angebot umgewandelt werden. Der Kunde muss ein kostenpflichtiges Angebot bestellen.
- **Bring-Your-Own-License (BYOL, Verwendung Ihrer eigenen Lizenz)** : Alle anfallenden Gebühren für Softwarelizenzen werden direkt zwischen Herausgeber und Kunde geregelt. Microsoft wickelt nur die Nutzungsgebühren für Azure-Infrastruktur ab. Wenn ein Angebot im kommerziellen Marketplace aufgelistet ist, werden dem Kunden, der außerhalb des kommerziellen Marketplace Zugriff auf das Angebot erhält bzw. das Angebot nutzt, keine kommerziellen Marketplace-Gebühren in Rechnung gestellt.
- **Abonnementpreise**: Softwarelizenzgebühren fallen als laufende Abonnementgebühr an, die monatlich oder jährlich fällig ist und als Pauschale oder pro Arbeitsplatz berechnet wird. Bei Kündigungen durch den Kunden in der Mitte der Laufzeit bzw. bei nicht genutzten Diensten erfolgt keine anteilsmäßige Erstattung von laufenden Abonnementgebühren. Laufende Abonnementgebühren können angerechnet werden, wenn der Kunde das Abonnement in der Mitte der Abonnementlaufzeit aktualisiert oder herabstuft.
- **Nutzungsbasierte Preisgestaltung**: Bei Angeboten für virtuelle Azure-Computer werden dem Kunden nutzungsabhängige Gebühren für das Angebot berechnet. Bei VM-Images wird dem Kunden ein Azure Marketplace-Stundensatz in Rechnung gestellt, der vom Herausgebern festgelegt und für die Nutzung der von den VM-Images bereitgestellten virtuellen Computer erhoben wird. Die Stundengebühr kann einheitlich sein oder je nach VM-Größe variieren. Angefangene Stunden werden pro Minute abgerechnet. Pläne werden monatlich abgerechnet.
- **Getaktetes Preismodell**: Bei Azure-Anwendungs- und SaaS-Angeboten können Herausgeber den [Marketplace-Messungsdienst](./partner-center-portal/marketplace-metering-service-apis.md) verwenden, um die Nutzung anhand der ausgewählten Messgrößen zu berechnen. Dazu zählen beispielsweise Bandbreite, Tickets oder verarbeitete E-Mails. Herausgeber können für jeden Plan eine oder mehrere Messdimensionen definieren. Herausgeber sind dafür verantwortlich, die Nutzung der einzelnen Kunden mit jeder im Angebot definierten Messgröße nachzuverfolgen. Ereignisse sollten innerhalb einer Stunde an Microsoft gemeldet werden. Microsoft erstellt die Kundenrechnungen auf Basis der von den Herausgebern für den jeweiligen Abrechnungszeitraum gemeldeten Nutzungsinformationen.
- **Kostenlose Testversion**: Für Softwarelizenzen, die zwischen 30 Tagen und sechs Monaten gültig sind, werden je nach Angebotstyp keine Kosten berechnet. Wenn Herausgeber eine kostenlose Testversion für mehrere Pläne innerhalb desselben Angebots bereitstellen, kann der Kunde zwar zu einer kostenlosen Testversion für einen anderen Plan wechseln, der Testzeitraum wird jedoch nicht neu gestartet. Bei Angeboten für virtuelle Computer werden dem Kunden die Azure-Infrastrukturkosten für die Nutzung des Angebots während eines Testzeitraums in Rechnung gestellt. Nach Ablauf des Testzeitraums wird dem Kunden automatisch der Standardtarif für den Plan in Rechnung gestellt, für den er zuletzt die Testversion verwendet hat, sofern er nicht vor dem Ende des Testzeitraums gekündigt hat.

> [!NOTE]
> Angebote, die nach Verbrauch abgerechnet werden, sind nach Verwendung der Lösung nicht erstattungsfähig.

Wenn ein Herausgeber die einem Angebot zugeordneten Nutzungsgebühren ändern möchte, sollte er zunächst das Angebot (oder den jeweiligen Plan innerhalb des Angebots) aus dem kommerziellen Marketplace entfernen. Das Entfernen sollte in Übereinstimmung mit den Anforderungen der [Microsoft-Herausgebervereinbarung](https://go.microsoft.com/fwlink/?LinkID=699560) erfolgen. Anschließend kann der Herausgeber ein neues Angebot (oder einen Plan innerhalb des Angebots) mit den neuen Nutzungsgebühren veröffentlichen. Weitere Informationen zum Entfernen eines Angebots oder Plans finden Sie unter [Einstellen des Verkaufs eines Angebots oder Plans](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Preisgestaltung für die Optionen „Kostenlos“, „Kontaktaufnahme“ und „BYOL“ (Bring-Your-Own-License)

Bei der Veröffentlichung eines Angebots mit der Option „Jetzt herunterladen (kostenlos)“, „Kontaktaufnahme“ oder „BYOL“ spielt Microsoft keine Rolle bei der Abwicklung der Verkaufstransaktion für Ihre Softwarelizenzgebühren. Wie bei den Veröffentlichungsoptionen „Liste“ und „Kostenlose Testversion“ behält der Herausgeber 100 % der Softwarelizenzgebühren ein.

### <a name="usage-based-and-subscription-pricing"></a>Nutzungsbasierte Preisgestaltung und Abonnementpreise

Bei der Veröffentlichung eines Angebots mit einer benutzerbasierten oder Abonnementtransaktion stellt Microsoft die Technologie und Dienste zum Verarbeiten von Käufen, Rückgaben und Rückbuchungen von Softwarelizenzen zur Verfügung. In diesem Szenario autorisiert der Herausgeber Microsoft, als Vermittler für diese Zwecke zu fungieren. Der Herausgeber erlaubt Microsoft die Abwicklung der Softwarelizenzierungstransaktion, wobei er seine Kennzeichnung als Verkäufer, Anbieter, Distributor und Lizenzgeber beibehält.

Microsoft ermöglicht es Kunden, Ihre Software zu bestellen, zu lizenzieren und zu nutzen, wobei die Bedingungen des kommerziellen Marketplace von Microsoft und Ihres Endbenutzerlizenzvertrags gelten. Sie müssen Ihren eigenen Endbenutzerlizenzvertrag bereitstellen oder beim Erstellen des Angebots den [Standardvertrag](./standard-contract.md) auswählen.

### <a name="free-software-trials"></a>Kostenlose Testversionen

Bei Veröffentlichungsszenarien des Typs „Transaktion“ können Sie je nach Abonnement eine Softwarelizenz zwischen 30 und 120 Tagen kostenlos zur Verfügung stellen. Diese Rabattierungsmöglichkeit schließt nicht die Kosten für die Nutzung der Azure-Infrastruktur ein, die durch die Nutzung der Partnerlösung verursacht wird.

### <a name="private-offers"></a>Private Angebote

Zusätzlich zur Verwendung von Angebotstypen und Abrechnungsmodellen zur Verrechnung eines Angebots können Sie ein vollständiges privates Angebot mit ausgehandelten, transaktionsspezifischen Preisen oder kundenspezifischen Konfigurationen anbieten. Private Angebote werden von allen drei Veröffentlichungsoptionen des Typs „Transaktion“ unterstützt.

Diese Option ermöglicht höhere oder niedrigere Preise als für das öffentlich verfügbare Angebot. Sie können private Angebote nutzen, um Rabatte zu gewähren oder eine Prämie zu einem Angebot hinzuzufügen. Sie können private Angebote einem oder mehreren Kunden bereitstellen, indem Sie das jeweilige Azure-Abonnement auf Angebotsebene auf die Zulassungsliste setzen.

### <a name="commercial-marketplace-service-fees"></a>Dienstgebühren im kommerziellen Marketplace

Wir berechnen eine Store-Standarddienstgebühr von 20 %, wenn Kunden Ihr Transaktionsangebot über den kommerziellen Marketplace erwerben. Ausführliche Informationen zu dieser Gebühr finden Sie in Abschnitt 5c der [Microsoft-Herausgebervereinbarung](https://go.microsoft.com/fwlink/?LinkID=699560).

Für bestimmte transaktionsfähige Angebote, die Sie im kommerziellen Marketplace veröffentlichen, können Sie eine reduzierte Store-Dienstgebühr von 10 % beantragen. Damit ein Angebot berechtigt ist, muss es von Microsoft als „Azure IP Co-Sell Incentivized“ gekennzeichnet worden sein. Die Anforderungen für die Berechtigung müssen mindestens fünf Arbeitstage vor dem Ende eines jeden Kalendermonats erfüllt sein, damit die reduzierte Marketplace-Dienstgebühr in Anspruch genommen werden kann. Sobald die Anforderungen für die Berechtigung erfüllt sind, wird die reduzierte Dienstgebühr für alle Transaktionen ab dem ersten Tag des folgenden Monats gewährt, bis der Azure IP-Co-Sell Incentivized-Status nicht mehr gegeben ist. Einzelheiten zur Berechtigung für das IP-Co-Selling finden Sie unter [Requirements for Co-sell Status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status) (Anforderungen für den Co-Selling-Status).

Die reduzierte Marketplace-Dienstgebühr gilt für SaaS, VMs, verwaltete Apps und andere berechtigte transaktionsfähige IaaS-Lösungen im Rahmen der Azure IP-Co-Selling-Incentives, die über den kommerziellen Marketplace bereitgestellt werden. Kostenpflichtige SaaS-Angebote, die einer Microsoft Teams-App oder mindestens zwei Microsoft 365-Add-Ins (wie Excel, PowerPoint, Word, Outlook und SharePoint) zugeordnet sind und in Microsoft AppSource veröffentlicht werden, erhalten ebenfalls diesen Rabatt.

### <a name="examples"></a>Beispiele

**Nutzungsbasiert** 

Die nutzungsbasierte Preisgestaltung weist folgende Kostenstruktur auf:

|Ihre Lizenzkosten  | 1,00 USD pro Stunde   |
|---------|---------|
|Azure-Nutzungskosten (D1/1-Kern)    |   0,14 USD pro Stunde     |
|*Microsoft führt die Abrechnung mit dem Kunden durch*    |  *1,14 USD pro Stunde*       |
||

In diesem Szenario berechnet Microsoft 1,14 USD pro Stunde für die Verwendung Ihres veröffentlichten VM-Images.

|Berechnung durch Microsoft  | 1,14 USD pro Stunde  |
|---------|---------|
|Microsoft zahlt Ihnen 80% Ihrer Lizenzkosten|   0,80 USD pro Stunde     |
|Microsoft behält 20% Ihrer Lizenzkosten ein  |  0,20 USD pro Stunde       |
|Microsoft behält die Kosten der Azure-Nutzung zu 100 % ein | 0,14 USD pro Stunde |
||

**BYOL (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz)**

BYOL weist die folgende Kostenstruktur auf:

|Ihre Lizenzkosten  | Ausgehandelte und von Ihnen in Rechnung gestellte Lizenzgebühr  |
|---------|---------|
|Azure-Nutzungskosten (D1/1-Kern)    |   0,14 USD pro Stunde     |
|*Microsoft führt die Abrechnung mit dem Kunden durch*    |  *0,14 USD pro Stunde*       |
||

In diesem Szenario berechnet Microsoft 0,14 USD pro Stunde für die Verwendung Ihres veröffentlichten VM-Images.

|Berechnung durch Microsoft  | 0,14 USD pro Stunde  |
|---------|---------|
|Microsoft behält die Kosten der Azure-Nutzung ein    |   0,14 USD pro Stunde     |
|Microsoft behält 0% Ihrer Lizenzkosten ein   |  0,00 USD pro Stunde       |
||

**SaaS-App-Abonnement**

Diese Option muss für den Verkauf über Microsoft konfiguriert sein und kann per Pauschalpreis oder pro Benutzer monatlich oder jährlich berechnet werden. Wenn Sie für ein SaaS-Angebot die Option **Über Microsoft verkaufen** aktivieren, verfügen Sie über die folgende Kostenstruktur:

| Ihre Lizenzkosten       | 100,00 USD pro Monat  |
|--------------|---------|
| Azure-Nutzungskosten (D1/1-Kern)    | Abrechnung direkt mit dem Herausgeber, nicht mit dem Kunden |
| *Microsoft führt die Abrechnung mit dem Kunden durch*    |  *100,00 USD pro Monat (der Herausgeber muss alle anfallenden oder weiterzugebenden Infrastrukturkosten in der Lizenzgebühr berücksichtigen)*  |
||

In diesem Szenario stellt Microsoft 100,00 USD für Ihre Softwarelizenz in Rechnung und zahlt 80,00 USD an den Herausgeber aus.

In diesem Szenario stellt Microsoft 100,00 USD für Ihre Softwarelizenz in Rechnung und zahlt 90,00 USD an den Herausgeber aus:

|Berechnung durch Microsoft  | 100,00 USD pro Monat  |
|---------|---------|
|Microsoft zahlt Ihnen 80% Ihrer Lizenzkosten <br> \* Für jede qualifizierte SaaS-App zahlt Microsoft 90 % Ihrer Lizenzkosten.   |   80,00 USD pro Monat <br> \* 90,00 USD pro Monat    |
|Microsoft behält 20% Ihrer Lizenzkosten ein <br> \* Für jede qualifizierte SaaS-App behält Microsoft 10 % Ihrer Lizenzkosten.  |  20,00 USD pro Monat <br> \* 10,00 USD     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Rechnungsstellung für den Kunden, Zahlung, Abrechnung und Inkasso

**Rechnungsstellung und Zahlung**: Sie können die vom Kunden bevorzugte Rechnungsstellungsmethode verwenden, um Abonnement- oder nutzungsbasierte Softwarelizenzgebühren zu entrichten.

**Enterprise Agreement**: Wenn die vom Kunden bevorzugte Rechnungsstellungsmethode „Microsoft Enterprise Agreement“ ist, werden Ihre Softwarelizenzgebühren mit dieser Rechnungsstellungsmethode als Postenkosten abgerechnet, und zwar getrennt von allen Azure-spezifischen Nutzungskosten.

**Kreditkarten und monatliche Rechnung**: Kunden können auch per Kreditkarte und monatlicher Rechnung bezahlen. In diesem Fall werden Ihre Softwarelizenzgebühren genau wie beim Enterprise Agreement-Szenario als Postenkosten abgerechnet, und zwar getrennt von allen Azure-spezifischen Nutzungskosten.

**Kostenlose Guthaben und Azure-Vorauszahlung**: Einige Kunden entscheiden sich dafür, Azure mit der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) im Enterprise Agreement-Rahmen zu bezahlen oder haben kostenlose Guthaben für die Nutzung mit Azure erhalten. Obwohl diese Guthaben zur Bezahlung der Azure-Nutzung verwendet werden können, dürfen sie nicht zur Zahlung von Softwarelizenzgebühren für Herausgeber verwendet werden.

**Abrechnung und Inkasso**: Die Abrechnung der Softwarelizenz des Herausgebers erfolgt nach der vom Kunden gewählten Methode der Rechnungsstellung und folgt dem Zeitrahmen der Rechnungsstellung. Kunden ohne Enterprise Agreement werden Marketplace-Softwarelizenzen monatlich in Rechnung gestellt. Kunden mit Enterprise Agreement wird monatlich eine Rechnung gestellt, die vierteljährlich ausgefertigt wird.

Bei Auswahl der Preismodelle „Abonnement“ oder „Nutzungsbasierte Bezahlung“ fungiert Microsoft als Vertreter des Herausgebers und ist für alle Aspekte der Abrechnung, Zahlung und des Inkassos verantwortlich.

### <a name="publisher-payout-and-reporting"></a>Auszahlung an den Herausgeber und Berichterstellung

Alle Softwarelizenzgebühren, die von Microsoft als Vertreter erhoben werden, unterliegen einer Transaktionsgebühr von 20 %, sofern nicht anders angegeben, und werden zum Zeitpunkt der Auszahlung an den Herausgeber einbehalten.

Kunden kaufen in der Regel im Rahmen eines Enterprise Agreement oder eines Vertrags mit nutzungsbasierter Bezahlung per Kreditkarte. Der Vertragstyp bestimmt den Zeitpunkt für Abrechnung, Rechnungsstellung, Inkasso und Auszahlung.

>[!NOTE]
>Alle Berichte und Ergebnisse für die Veröffentlichungsoption „Transaktion“ sind in Partner Center im Abschnitt „Analyse“ verfügbar.

#### <a name="billing-questions-and-support"></a>Fragen zu Abrechnung und Support

Weitere Informationen und gesetzliche Richtlinien finden Sie in der [Microsoft-Herausgebervereinbarung](https://go.microsoft.com/fwlink/?LinkID=699560) (in Partner Center verfügbar).

Hilfe zu Abrechnungsfragen erhalten Sie beim [Support für Herausgeber im kommerziellen Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Anforderungen für die Veröffentlichungsoption „Transaktion“

In diesem Abschnitt werden die Transaktionsanforderungen für verschiedene Angebotstypen behandelt.

### <a name="requirements-for-all-offer-types"></a>Anforderungen für alle Angebotstypen

- Für die Veröffentlichungsoption „Transaktion“ sind unabhängig vom Preismodell des Angebots ein Microsoft-Konto und Finanzinformationen erforderlich.
- Obligatorische Finanzinformationen sind beispielsweise ein Auszahlungskonto und ein Steuerprofil.
- Der Herausgeber muss sich in einem [unterstützten Land oder einer unterstützten Region](sell-from-countries.md) befinden.

Weitere Informationen zum Einrichten dieser Konten finden Sie unter [Verwalten Ihres Kontos im kommerziellen Marketplace in Partner Center](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Anforderungen für bestimmte Angebotstypen

Die Veröffentlichungsoption „Transaktion“ ist nur für die folgenden Typen von Marketplace-Angeboten verfügbar:

- **Virtueller Azure-Computer:** Wählen Sie eines der Modelle „Kostenlos“, „BYOL (Bring-Your-Own-License)“ und „Nutzungsbasierte Preisgestaltung“ aus, und weisen Sie es als auf Angebotsebene definierte Pläne aus. Auf der Azure-Rechnung des Kunden weist Microsoft die Softwarelizenzgebühren des Herausgebers getrennt von den zugrunde liegenden Azure-Infrastrukturgebühren aus. Azure-Infrastrukturgebühren hängen von der Verwendung der Software des Herausgebers ab.

- **Azure-Anwendung: Lösungsvorlage oder verwaltete App**: Muss einen oder mehrere virtuelle Computer bereitstellen und zieht sich durch die Summe der Preise für virtuelle Computer. Für verwaltete Apps mit einem Einzelplan kann anstelle der Preise für virtuelle Computer ein pauschales Monatsabonnement als Preismodell gewählt werden. In einigen Fällen werden dem Kunden die Nutzungsgebühren der Azure-Infrastruktur getrennt von den Softwarelizenzgebühren in Rechnung gestellt – aber auf derselben Abrechnung. Wenn Sie allerdings ein Angebot für eine verwaltete App für ISV-Infrastrukturgebühren konfigurieren, werden die Azure-Ressourcen dem Herausgeber berechnet. Für den Kunden wird eine Pauschalgebühr verwendet, die die Kosten für die Infrastruktur, Softwarelizenzen und Verwaltungsdienste abdeckt.

- **SaaS-Anwendung**: Muss eine mehrinstanzenfähige Lösung sein, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwenden und mit den [SaaS-Fulfillment-APIs](partner-center-portal/pc-saas-fulfillment-api-v2.md) integriert sein. Die Nutzung der Azure-Infrastruktur wird direkt mit Ihnen (dem Partner) verwaltet und abgerechnet. Daher sind Sie als einzige Kostenstelle für Azure-Infrastruktur-Nutzungsgebühren und Softwarelizenzgebühren verantwortlich. Ausführliche Anleitungen erhalten Sie unter [Erstellen eines neuen SaaS-Angebots im kommerziellen Marketplace](./create-new-saas-offer.md).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die erforderlichen Berechtigungen in den Veröffentlichungsoptionen des entsprechenden Angebotstypabschnitts, um Auswahl und Konfiguration Ihres Angebots abzuschließen.
- Entnehmen Sie den Veröffentlichungsmustern der Onlineshops Beispiele für die Zuordnung Ihrer Lösung zu einem Angebotstyp und einer Konfiguration.