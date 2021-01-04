---
title: Volumenabrechnung für SaaS-Angebote mit dem Messungsdienst für den kommerziellen Marketplace von Microsoft
description: Erfahren Sie mehr über flexible Abrechnungsmodelle für SaaS-Angebote mit dem Messungsdienst für den kommerziellen Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892752"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Volumenabrechnung für SaaS mit dem Messungsdienst für den kommerziellen Marketplace

Mit dem Messungsdienst für den kommerziellen Marketplace können Sie Software-as-a-Service-Angebote (SaaS) erstellen, die nach nicht standardmäßigen Einheiten abgerechnet werden. Vor der Veröffentlichung eines SaaS-Angebots für den kommerziellen Marketplace definieren Sie die Abrechnungsdimensionen wie Bandbreite, Tickets oder verarbeitete E-Mails.  Kunden zahlen dann auf der Grundlage ihres Verbrauchs dieser Dimensionen, wobei Ihr System Microsoft über die API für den Messungsdienst für den kommerziellen Marketplace über abrechenbare Ereignisse informiert, sobald diese auftreten.  

## <a name="prerequisites-for-metered-billing"></a>Voraussetzungen für die getaktete Abrechnung

Damit die getaktete Abrechnung auf ein SaaS-Angebot angewendet werden kann, müssen folgende Voraussetzungen erfüllt sein:

- Es muss alle Angebotsanforderungen für ein [Angebot für den Verkauf durch Microsoft](../plan-saas-offer.md#listing-options) erfüllen, wie unter [Erstellen eines SaaS-Angebots im kommerziellen Marketplace](../create-new-saas-offer.md) beschrieben.
- Es muss in die [SaaS-Fulfillment-APIs](./pc-saas-fulfillment-api-v2.md) integriert werden, damit Kunden Ihr Angebot bereitstellen und eine Verbindung damit herstellen können.  
- Es muss ein **Pauschalgebührenmodell** konfiguriert werden, um Kunden Ihren Dienst in Rechnung stellen zu können.  Dimensionen sind eine optionale Erweiterung des Pauschalpreismodells. 

Anschließend kann das SaaS-Angebot in die [APIs für den Messungsdienst für den kommerziellen Marketplace](./marketplace-metering-service-apis.md) integriert werden, um Microsoft über abrechenbare Ereignisse zu informieren.

>[!Note]
>Der Marketplace-Messungsdienst ist nur für das pauschale Abrechnungsmodell verfügbar und gilt nicht für das Abrechnungsmodell pro Benutzer.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Zusammenspiel von getakteter Abrechnung und Preisgestaltung

Bei der Definition des Angebots und seiner Preismodelle ist es wichtig, die Angebotshierarchie zu verstehen.

- Jedes SaaS-Angebot ist so konfiguriert, dass es entweder über Microsoft verkauft wird oder nicht.  Ist ein Angebot einmal veröffentlicht, kann diese Einstellung nicht mehr geändert werden.
- Jedes SaaS-Angebot, das für den Verkauf über Microsoft konfiguriert ist, kann über einen oder mehrere Pläne verfügen.  Ein Benutzer abonniert das SaaS-Angebot, aber es wird über Microsoft im Rahmen eines Plans erworben.
- Jedem Plan ist ein Preismodell zugeordnet: **pauschal** oder **pro Benutzer**. Alle Pläne in einem Angebot müssen dem gleichen Preismodell zugeordnet sein. Es ist beispielsweise nicht zulässig, ein Angebot mit einem Pauschalgebührenmodell und einem benutzerbasierten Preismodell zu erstellen.
- In jedem Plan, der für ein pauschales Abrechnungsmodell konfiguriert wird, ist mindestens eine wiederkehrende Gebühr enthalten (die allerdings 0 EUR betragen kann):
    - Wiederkehrende **monatliche** Gebühr: Pauschale monatliche Gebühr, die monatlich im Voraus bezahlt wird, wenn der Benutzer den Tarif erwirbt
    - Wiederkehrende **Jahresgebühr** : Pauschale Jahresgebühr, die jährlich im Voraus bezahlt wird, wenn der Benutzer den Tarif erwirbt
- Zusätzlich zu den wiederkehrenden Gebühren kann der Pauschaltarif auch optionale, benutzerdefinierte Dimensionen enthalten, mit denen dem Kunden die zusätzliche, nicht in der Pauschale enthaltene Nutzung berechnet wird.  Jede Dimension stellt eine abrechenbare Einheit dar, die Ihr Dienst über die [API für den Messungsdienst für den kommerziellen Marketplace](./marketplace-metering-service-apis.md) an Microsoft übermittelt.

## <a name="sample-offer"></a>Beispielangebot

In diesem Beispiel ist Contoso ein Herausgeber mit einem SaaS-Dienst namens Contoso Notification Services (CNS). Mit CNS können Kunden Benachrichtigungen entweder per E-Mail oder per SMS senden. Contoso ist in Partner Center als Herausgeber für das kommerzielle Marketplace-Programm registriert und kann Angebote für Azure-Kunden veröffentlichen.  CNS sind zwei Pläne zugeordnet:

- Basic-Tarif
    - Bis 10.000 E-Mails und 1.000 SMS für 0 USD pro Monat (monatliche Pauschalgebühr)
    - Über 10.000 E-Mails: 1 USD pro 100 E-Mails
    - Über 1.000 SMS: 0,02 USD pro SMS

    [![Preise für den Standardtarif](./media/saas-basic-pricing.png "Für vergrößerte Ansicht klicken")](./media/saas-basic-pricing.png)

- Premium-Tarif
    - Bis 50.000 E-Mails und 10.000 SMS für 350 USD pro Monat oder 5 Mio. E-Mails und 1 Mio. SMS für 3.500 USD pro Jahr
    - Über 50.000 E-Mails: 0,5 USD pro 100 E-Mails
    - Über 10.000 SMS: 0,01 USD pro SMS

    [![Preise für den Premiumtarif](./media/saas-premium-pricing.png "Für vergrößerte Ansicht klicken")](./media/saas-premium-pricing.png)

- Enterprise-Tarif
    - Unbegrenzte Anzahl von E-Mails und 50.000 SMS für 400 USD pro Monat
    - Bei mehr als 50.000 SMS nur 0,005 USD pro SMS

    [![Preise für den Enterprise-Tarif](./media/saas-enterprise-pricing.png "Für vergrößerte Ansicht klicken")](./media/saas-enterprise-pricing.png)

Je nach ausgewähltem Tarif kann ein Azure-Kunde, der ein Abonnement für ein CNS-SaaS-Angebot abschließt, während der Abonnementlaufzeit (Monat oder Jahr, in den Abonnementdetails unter „StartDate“ und „EndDate“ aufgeführt) die enthaltene Menge an SMS und E-Mails senden.  Contoso erfasst die Nutzung bis zum enthaltenen Kontingent, ohne dass Nutzungsereignisse an Microsoft gesendet werden. Wenn Kunden mehr als die enthaltene Menge beanspruchen, müssen sie nicht zu einem anderen Plan wechseln oder ihr Verhalten ändern.  Contoso misst die Nutzung, die über das enthaltene Kontingent hinausgeht, und beginnt damit, über die [API für den Messungsdienst für den kommerziellen Marketplace](./marketplace-metering-service-apis.md) Nutzungsereignisse an Microsoft zu senden, um die zusätzliche Nutzung abzurechnen.  Microsoft berechnet dem Kunden wiederum die über das Pauschalkontingent hinausgehende Nutzung gemäß der Angabe des Herausgebers in den benutzerdefinierten Dimensionen. Die Abrechnung für die über das Pauschalkontingent hinausgehende Nutzung erfolgt im nächsten Abrechnungszeitraum (monatlich, bei einigen Kunden u. U. jedoch vierteljährlich oder jährlich).  Bei einem monatlichen Pauschaltarif wird die zusätzliche Nutzung jeweils für den Monat abgerechnet, in dem das Pauschalkontingent überschritten wurde.  Bei einem jährlichen Pauschaltarif wird die gesamte zusätzliche Nutzung, die vom benutzerdefinierten Messungsdienst übertragen wird, nachdem das im Tarif enthaltene Grundkontingent aufgebraucht ist, bis zum Ende des Jahresabonnements im jeweiligen Abrechnungszeitraum (monatlich) als Überschreitung abgerechnet.

## <a name="billing-dimensions"></a>Abrechnungsdimensionen

Jede Abrechnungsdimension definiert eine benutzerdefinierte Einheit, mit der ein unabhängiger Softwarehersteller Nutzungsereignisse senden kann.  Mithilfe von Abrechnungsdimensionen werden Kunden außerdem darüber informiert, wie ihre Softwarenutzung abgerechnet wird.  Sie sind wie folgt definiert:

- **Kennung:** Die unveränderliche Dimensionskennung, auf die bei der Ausgabe von Nutzungsereignissen verwiesen wird
- **Anzeigename:** Der Anzeigename, der der Dimension zugeordnet ist, z. B. „Gesendete SMS“
- **Maßeinheit:** Die Beschreibung der Abrechnungseinheit, z. B. „pro SMS“ oder „pro 100 E-Mails“
- **Preis pro Einheit in USD:** Der Preis für eine Einheit der Dimension.  Kann 0 (Null) entsprechen. 
- **Grundsätzlich enthaltene monatliche Menge:** Die Menge, die pro Monat für diese Dimension im Lieferumfang enthalten ist, wenn Kunden die wiederkehrende Monatsgebühr bezahlen. Muss eine ganze Zahl sein. Der Wert kann 0 oder höher (unbegrenzt) sein.
- **Grundsätzlich enthaltene jährliche Menge:** Die Menge, die pro Jahr für diese Dimension im Lieferumfang enthalten ist, wenn Kunden die wiederkehrende Jahresgebühr bezahlen. Muss eine ganze Zahl sein. Der Wert kann 0 oder höher (unbegrenzt) sein.

Abrechnungsdimensionen werden in allen Plänen für ein Angebot verwendet.  Manche Attribute gelten für die Dimension über alle Pläne hinweg, andere Attribute sind dagegen planspezifisch.

Die Attribute, die die Dimension selbst definieren, werden übergreifend für alle Pläne zu einem Angebot genutzt.  Vor der Angebotsveröffentlichung wirkt sich eine Änderung dieser Attribute im Kontext eines beliebigen Plans auf die Dimensionsdefinition für alle Pläne aus.  Nachdem Sie das Angebot veröffentlicht haben, können diese Attribute nicht mehr bearbeitet werden.  Zu diesen Attributen zählen folgende:

- id
- Anzeigename
- Berechnungseinheit

Die anderen Attribute einer Dimension sind planspezifisch und können von Plan zu Plan unterschiedliche Werte haben.  Vor dem Veröffentlichen des Plans können Sie diese Werte bearbeiten, und nur dieser Plan ist betroffen.  Nachdem Sie den Plan veröffentlicht haben, können diese Attribute nicht mehr bearbeitet werden.  Zu diesen Attributen zählen folgende:

- Preis pro Einheit in USD
- Grundsätzlich enthaltene monatliche Menge  
- Grundsätzlich enthaltene jährliche Menge  

Dimensionen verfügen auch über zwei spezielle Konzepte: „aktiviert“ und „unbegrenzt“:

- **Aktiviert** gibt an, dass dieser Plan Teil dieser Dimension ist.  Wenn Sie einen neuen Tarif erstellen, der keine auf dieser Dimension basierenden Nutzungsereignisse sendet, sollten Sie diese Option deaktiviert lassen.  Außerdem werden alle neuen Dimensionen, die nach der ersten Veröffentlichung eines Plans hinzugefügt wurden, für den bereits veröffentlichten Plan als „nicht aktiviert“ angezeigt.  Eine deaktivierte Dimension wird in keiner Dimensionenliste für einen Plan angezeigt, der für Kunden sichtbar ist.
- **Unbegrenzt** , dargestellt durch das Unendlichkeitssymbol „∞“, gibt an, dass diese Dimension in diesem Tarif zwar enthalten ist, die Nutzung aber nicht anhand dieser Dimension gemessen wird.  Dadurch können Sie Ihren Kunden vermitteln, dass die durch diese Dimension dargestellte Funktion im Plan enthalten und die Nutzung nicht begrenzt ist.  Eine Dimension mit unbegrenzter Nutzung wird in Dimensionenlisten für einen Plan, der für Kunden sichtbar ist, mit dem Hinweis angezeigt, dass dadurch für diesen Plan keine Kosten entstehen.

>[!Note] 
>Folgende Szenarien werden explizit unterstützt: <br> - Sie können einem neuen Plan eine neue Dimension hinzufügen.  Die neue Dimension wird nicht für bereits veröffentlichte Pläne aktiviert. <br> - Sie können einen **pauschalen** Plan ohne Dimensionen veröffentlichen und anschließend einen neuen Plan hinzufügen und eine neue Dimension für diesen Plan konfigurieren. Die neue Dimension wird nicht für bereits veröffentlichte Pläne aktiviert.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Festlegen des Dimensionspreises pro Einheit und unterstütztem Markt

Ebenso wie Pauschalgebühren können auch die Preise von Abrechnungsdimensionen pro unterstütztem Land oder unterstützter Region festgelegt werden. Sie müssen dafür das Feature zum Importieren und Exportieren von Preisdaten in Partner Center wie folgt verwenden.

1. Definieren Sie die gewünschten Dimensionen, und markieren Sie, welche Märkte unterstützt werden sollen. 
1. Exportieren Sie diese Daten in eine Datei.
1. Fügen Sie die korrekten Preise pro Land oder Region ein, und importieren Sie die Datei in Partner Center.

Die Benutzeroberfläche des Messungsdiensts wird aktualisiert und zeigt nun an, dass die Preise der Dimension nur noch in der Datei angezeigt werden können.

[![Dimensionen des Messungsdiensts für den kommerziellen Marketplace](media/metering-service-dimensions.png "Für vergrößerte Ansicht klicken")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Privater Tarif

Ebenso wie bei Pauschaltarifen kann ein Tarif mit Dimensionen als privat festgelegt werden. Er ist dann nur noch für die für den Tarif definierte Zielgruppe zugänglich.

## <a name="constraints"></a>Einschränkungen

### <a name="trial-behavior"></a>Testverhalten

Die getaktete Abrechnung mit dem Messungsdienst für den kommerziellen Marketplace ist nicht mit Angeboten für kostenlose Testversionen kompatibel.  Die getaktete Abrechnung und eine kostenlose Testversion können nicht gemeinsam in einem Plan konfiguriert werden.

### <a name="locking-behavior"></a>Sperrverhalten

Da eine mit dem Messungsdienst für den kommerziellen Marketplace verwendete Dimension bestimmt, wie ein Kunde für den Dienst bezahlt, können die Details einer Dimension nach der Veröffentlichung nicht mehr bearbeitet werden.  Daher ist es wichtig, dass Sie Ihre Dimensionen vor der Veröffentlichung vollständig für einen Plan definiert wurden.

Sobald ein Angebot mit einer Dimension veröffentlicht wurde, können die Details der Angebotsebene für diese Dimension nicht mehr geändert werden:

- id
- Anzeigename
- Berechnungseinheit

Nachdem ein Plan veröffentlicht wurde, können die Details auf der Planebene nicht mehr geändert werden:

- Preis pro Einheit in USD
- Grundsätzlich enthaltene monatliche Menge
- Grundsätzlich enthaltene jährliche Menge
- Ob die Dimension für den Plan aktiviert ist oder nicht

### <a name="upper-limits"></a>Obergrenzen

Für ein einzelnes Angebot können maximal 30 individuelle Dimensionen konfiguriert werden.

## <a name="get-support"></a>Support

Sollte eines der folgenden Probleme bei Ihnen auftreten, können Sie ein Supportticket erstellen:

- Technische Probleme mit der Marketplace-Messungsdienst-API
- Ein Problem, das aufgrund eines Fehlers auf Ihrer Seite eskaliert werden muss (beispielsweise ein falsches Nutzungsereignis)
- Ein anderes Problem im Zusammenhang mit der getakteten Abrechnung

Um die Supportoptionen für Herausgeber zu verstehen und ein Supportticket für Microsoft zu öffnen, befolgen Sie die Anweisungen unter [Support für das Programm „Kommerzieller Marketplace“ im Partner Center](../support.md).

## <a name="next-steps"></a>Nächste Schritte

- [Marketplace-Messungsdienst-APIs](./marketplace-metering-service-apis.md)
