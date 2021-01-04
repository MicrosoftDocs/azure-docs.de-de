---
title: Planen eines Angebots für Beratungsdienste im kommerziellen Marketplace von Microsoft
description: In diesem Artikel erfahren Sie, wie Sie ein neues Angebot für Beratungsdienste für Microsoft AppSource oder den Azure Marketplace mithilfe des kommerziellen Marketplace-Programms in Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 7f56ff94c4dcf276accc56e8d1076266099d635d
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780704"
---
# <a name="how-to-plan-a-consulting-service-offer-in-the-commercial-marketplace"></a>Planen eines Angebots für einen Beratungsdienst im kommerziellen Marketplace

In diesem Artikel werden die verschiedenen Optionen und Anforderungen zum Veröffentlichen eines Angebots für einen Beratungsdienst für den kommerziellen Marketplace von Microsoft erläutert. In diesem Artikel erfahren Sie, wie Sie ein Angebot für die Veröffentlichung in Partner Center vorbereiten.

## <a name="whats-a-consulting-service"></a>Was ist ein Beratungsdienst?

Beratungsdienste sind virtuelle oder persönliche Interaktionen mit Kunden, die die Nutzung von Microsoft-Produkten durch den Kunden unterstützen und erweitern. Mithilfe eines Beratungsdiensts können Kunden Lösungen bewerten, auswerten und bereitstellen, die ihre Geschäftsziele vorantreiben. Den Umfang, die Dauer und die Preisstruktur Ihres Beratungsdiensts (Festpreis oder kostenlos) legen Sie selbst fest.

Angebote für Beratungsdienste werden als Angebot des Typs **Kontakt mit mir aufnehmen** veröffentlicht. Dies bedeutet, dass Kunden Sie direkt über die Informationen kontaktieren, die Sie im Angebot angeben. Zwar hostet Microsoft das Angebot im kommerziellen Marketplace, Sie sind jedoch für die Verwaltung aller Interaktionen mit Kunden verantwortlich. Es ist Ihre Aufgabe, den Beratungsdienst zu leisten, die Abrechnung durchzuführen, die Rechnung zu erstellen und die Gebühren vom Kunden einzuholen.

## <a name="primary-products-and-online-stores"></a>Primäre Produkte und Onlinestores

Jeder Beratungsdienst muss sich auf eines dieser Microsoft-Produkte konzentrieren, das im Angebot als **primäres Produkt** bezeichnet wird:

|Hauptprodukt                               |                   |
|-------------------------------|-------------------|
|Azure                          |Dynamics 365 Project Service Automation |
|Dynamics 365 Business Central  |Dynamics 365 Sales |
|Dynamics 365 Commerce          |Dynamics 365 Supply Chain Management |
|Dynamics 365 Customer Insights |Microsoft 365 |
|Dynamics 365 Customer Service  |Power Apps |
|Dynamics 365 Field Service     |Power Automate |
|Dynamics 365 Finance           |Power BI |
|Dynamics 365 Marketing         |Power Virtual Agents |
|    |    |

Das in Partner Center ausgewählte primäre Produkt bestimmt, ob Ihr Angebot in Microsoft AppSource oder im Azure Marketplace veröffentlicht wird.

* Wenn das primäre Produkt Ihres Beratungsdiensts Azure ist, wird das Angebot im Azure Marketplace aufgeführt.
* Wenn es sich bei dem primären Produkt nicht um Azure handelt, wird das Angebot in AppSource veröffentlicht.

Weitere Informationen zu den Unterschieden zwischen AppSource und dem Azure Marketplace finden Sie unter [Onlinestores des kommerziellen Marketplace](./overview.md#commercial-marketplace-online-stores).

## <a name="eligibility-requirements"></a>Qualifizierungsanforderungen

Sie müssen vor dem Veröffentlichen eines Angebots für einen Beratungsdienst mehrere Eignungsanforderungen erfüllen, um den Kunden gegenüber Ihr Fachwissen auf einem Gebiet nachzuweisen. Die Anforderungen hängen vom Kernprodukt Ihres Angebots ab. Die komplette Liste der Eignungsanforderungen für die einzelnen primären Produkte finden Sie in den [Zertifizierungsrichtlinien für Beratungsdienste](/legal/marketplace/certification-policies#800-consulting-services).

> [!NOTE]
> Für einige primäre Produkte benötigen Sie die Microsoft-Kompetenz „Gold“ oder „Silver“ in ihrem Lösungsbereich. Weitere Informationen finden Sie unter [Kompetenzen für das Microsoft Partner Network](https://partner.microsoft.com/membership/competencies).

## <a name="service-type-and-duration"></a>Diensttyp und -dauer

Der kommerzielle Marketplace unterstützt fünf Arten von Beratungsdiensten:

* **Bewertung:** Hierbei handelt es sich um eine Auswertung der Umgebung eines Kunden, bei der die Eignung einer Lösung bestimmt und die Kosten sowie der Zeitrahmen für die Implementierung geschätzt wird.
* **Einweisung:** Hierbei handelt es sich um eine Einführung in eine Lösung oder einen Dienst mithilfe von Frameworks, Demos und Beispielen von Kunden.
* **Implementierung:** Dies ist eine umfassende Installation, die zu einer vollständig funktionierenden Lösung führt.
* **Proof of Concept:** Dies bezieht sich auf eine Implementierung in einem begrenzten Umfang, um zu ermitteln, ob eine Lösung die Anforderungen des Kunden erfüllt.
* **Workshop:** Dieses interaktive Angebot wird beim Kunden vor Ort durchgeführt. Dabei kann es sich um Schulungen, Einweisungen, Bewertungen oder Demos handeln, die auf den Daten oder der Umgebung des Kunden aufbauen.

Ihr Beratungsdienst sollte eine feste und vorab festgelegte Dauer von bis zu 10 Wochen haben. Die Dauer des Diensts muss im Angebot explizit aufgeführt werden.

## <a name="customer-leads"></a>Kundenleads

Um Kundeninformationen zu sammeln, müssen Sie Ihr Angebot mit Ihrem CRM-System (Customer Relationship Management) verbinden. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Onlineshop, auf dem der Kunde Ihr Angebot gefunden hat, werden an das von Ihnen konfigurierte CRM-System gesendet. Der kommerzielle Marketplace unterstützt eine Vielzahl von CRM-Systemen. Außerdem können Sie optional eine Azure-Tabelle verwenden oder einen HTTPS-Endpunkt mithilfe von Power Automate konfigurieren.

Eine CRM-Verbindung kann während der Angebotserstellung zu einem beliebigen Zeitpunkt hinzugefügt oder geändert werden. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

Wenn Sie ein Angebot für einen Beratungsdienst in Partner Center erstellen, fügen Sie Text, Bilder, Dokumente und andere Angebotsdetails hinzu. Dies sind die Informationen, die Kunden angezeigt werden, wenn sie Ihr Angebot in AppSource oder im Azure Marketplace entdecken. Sehen Sie sich folgendes Beispiel an:

![Screenshot: So wird ein Angebot für einen Beratungsdienst in den Onlinestores angezeigt.](./media/example-consulting-service.png)

**Beschreibungen zu den Nummern**

1. Logo
2. Dienstart
3. Kompetenzen
4. Lösungsbereiche (Azure Marketplace)/Produkte (AppSource)
5. Branchen
6. Land/Region
7. Angebotsname
8. Zusammenfassung der Suchergebnisse
9. BESCHREIBUNG
10. Screenshots/Videos
11. Unterstützende Dokumente

> [!NOTE]
> Wenn Sie Ihren Beratungsdienst in einer anderen Sprache als Englisch anbieten, kann das Angebot auch in dieser Sprache verfasst werden. Die Beschreibung muss jedoch mit dem englischen Satz „This service is available in &lt;Sprache Ihres Angebotsinhalts>” (Dieser Dienst ist auf <Sprache Ihres Angebotsinhalts> verfügbar) beginnen und enden. Sie können auch zusätzliche Dokumente in einer anderen Sprache als der für die Details der Angebotsliste verwendeten beifügen.

Um das Angebot einfacher zu gestalten, können Sie einige dieser Elemente vorab vorbereiten. Die folgenden Elemente sind erforderlich, sofern nicht anders angegeben:

**Name**: Dieser Name wird als Titel Ihrer Angebotsliste im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis (mit Ausnahme des Markenzeichen- und Copyrightsymbols) enthalten und ist auf 50 Zeichen begrenzt. Der Name muss die Dauer und den Typ des angebotenen Beratungsdiensts angeben, um eine maximale Suchmaschinenoptimierung zu erzielen. Das erforderliche Format ist wie folgt: *Name: Dauer + Typ*. Fügen Sie den Namen Ihres Unternehmens nur dann ein, wenn es sich auch um den Produktnamen handelt. Im Folgenden finden Sie einige Beispiele:

|Nicht zulässig |Sagen Sie: . |
|---|---|
|Erste Schritte mit Azure IoT in der Fertigung |IoT in der Fertigung: 2-Tag Bewertung |
|Workshop zu intelligenten Toastern |Intelligente Toaster: Woche-1 Workshop |
|PoC für SQL Server-Migration von Contoso |SQL-Migration: 3-tägige Proof of Concept-Verifizierung |
| | |

**Zusammenfassung der Suchergebnisse**: Beschreiben Sie den Zweck oder das Ziel Ihres Angebots in höchstens 200 Zeichen. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus den Angeboten im kommerziellen Marketplace erfasst werden. Sie sollte nicht mit dem Titel identisch sein. Fügen Sie ggf. Ihre wichtigsten SEO-Schlüsselwörter ein.

**Beschreibung**: Diese Beschreibung wird im Angebot im kommerziellen Marketplace angezeigt. Fügen Sie gegebenenfalls auch ein Nutzenversprechen, die wichtigsten Vorteile, die beabsichtigte Benutzerbasis und alle Kategorie- oder Branchenverbände hinzu.

Halten Sie sich beim Verfassen der Beschreibung für die jeweiligen Diensttypen an die folgenden Kriterien:

|Dienstart |Anforderungen an die Beschreibung |
|---|---|
|Bewertung |Fügen Sie eine detaillierte Agenda für Bewertungen hinzu, die länger als einen Tag dauern, und erläutern Sie, welches Ergebnis der Kunde erwarten kann. |
|Briefing |Erläutern Sie, welches Ergebnis der Kunde erwarten kann.|
|Implementierung |Fügen Sie eine detaillierte Agenda für Implementierungen hinzu, die länger als einen Tag dauern, und erläutern Sie, welche Entwicklungsänderungen, technischen oder andere Artefakte ein Kunde als Ergebnis des Auftrags erwarten kann. |
|Proof of Concept |Beschreiben Sie, welche Entwicklungsänderungen oder technischen oder anderen Artefakte ein Kunde als Ergebnis des Auftrags erwarten kann. |
|Workshop |Fügen Sie je nach Dauer Ihres Angebots eine detaillierte Tages-, Wochen- oder Monatsagenda hinzu. Formulieren Sie die Lernziele oder andere Ergebnisse Ihres Workshops. |
| | |

Hier einige Tipps zum Verfassen Ihrer Beschreibung:

* Schildern Sie in den ersten Sätzen deutlich den Nutzen Ihres Angebots, und geben Sie u. a. Folgendes an:
    * Typ von Benutzern, die von dem Angebot profitieren
    * Kundenbedürfnisse oder -probleme, an die sich das Angebot richtet.
* Beachten Sie, dass die ersten Sätze möglicherweise in den Suchergebnissen angezeigt werden.
* Wenn die Kosten Ihres Angebots nur geschätzt sind, erklären Sie, von welchen Faktoren der endgültige Preis abhängen wird.
* Verwenden Sie branchenspezifisches Vokabular.

Die Beschreibung kann mithilfe von HTML-Tags formatiert werden. Sie können bis zu 2.000 Zeichen Text in dieses Feld eingeben, einschließlich HTML-Tags und Leerzeichen. Weitere Informationen zur HTML-Formatierung finden Sie unter [In Angebotsbeschreibungen des kommerziellen Marketplace unterstützte HTML-Tags](./supported-html-tags.md).

**Suchbegriffe** (optional): Geben Sie bis zu drei Suchbegriffe ein, mit denen Kunden in den Onlineshops nach Ihrem Angebot suchen können. Der **Name** und die **Beschreibung** des Angebots müssen nicht aufgenommen werden.

**Dauer:** Ihr Angebot für einen Beratungsdienst muss eine vorab festgelegte Dauer von bis zu 10 Wochen aufweisen.

**Kontaktinformationen:** Sie werden in Partner Center aufgefordert, den Namen, die E-Mail-Adresse und die Telefonnummer von zwei Personen in Ihrem Unternehmen anzugeben (Sie können für einen der beiden Kontakte Ihre eigenen Kontaktinformationen verwenden.) Wir werden über diese Informationen bezüglich Ihres Angebots mit Ihnen kommunizieren. Diese Informationen werden Kunden nicht angezeigt, werden aber unter Umständen an Cloudlösungsanbieter weitergegeben.

**Unterstützende Dokumente:** Laden Sie mindestens ein und bis zu drei für Kunden sichtbare PDF-Dokumente hoch, die Informationen über Ihr Angebot enthalten. Dabei kann es sich beispielsweise um Whitepaper oder Broschüren handeln.

**Marketplace-Medien – Logos:** Stellen Sie eine PNG-Datei für das große Logo bereit. Hiermit wird in Partner Center ein kleines Logo erstellt. Sie können dieses Bild später optional durch ein anderes Bild ersetzen.

* Groß (von 216 × 216 bis 350 × 350 Pixel, erforderlich)
* Klein (48 × 48 Pixel, optional)

Das große Logo wird auf Ihrer Angebotsseite im Azure Marketplace oder in AppSource angezeigt. Das kleine Logo wird in den Suchergebnissen im Azure Marketplace oder auf der Haupt- und Suchergebnisseite von AppSource gezeigt.

Befolgen Sie die folgenden Richtlinien für Ihre Logos:

* Achten Sie darauf, dass das Bild nicht verschwommen oder verzerrt ist.
* Die Farbpalette des Azure-Designs ist einfach und geradlinig. Beschränken Sie die Anzahl der Primär- und Sekundärfarben auf Ihrem Logo.
* Wenn Sie einen transparenten Hintergrund verwenden, stellen Sie sicher, dass das Logo und der Text nicht weiß, schwarz oder blau sind.
* Vermeiden Sie Farbverläufe im Logo oder Hintergrund. Platzieren Sie keinen Text auf dem Logo, auch nicht Ihren Firmen- oder Markennamen.

**Marketplace-Medien – Screenshots:** Fügen Sie mindestens ein und bis zu fünf Bilder hinzu, die Ihr Angebot veranschaulichen. Alle Bilder müssen 1.280 × 720 Pixel groß sein und im PNG-Format vorliegen.

**Marketplace-Medien – Videos** (optional): Sie können bis zu vier Videos hinzufügen, die Ihr Angebot veranschaulichen. Die Videos müssen auf YouTube oder Vimeo gehostet werden und eine Vorschauminiatur aufweisen (1280 × 720, PNG-Datei).

## <a name="pricing-and-availability"></a>Preise und Verfügbarkeit

Ihr Beratungsdienstangebot kann in einem oder mehreren Ländern oder Regionen zur Verfügung gestellt werden. Sie können in Partner Center den Preis für jeden ausgewählten Markt festlegen. Eine umfassende Liste der unterstützten Märkte und Währungen finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen für den kommerziellen Marketplace](./marketplace-geo-availability-currencies.md).


## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Angebots für einen Beratungsdienst – kommerzieller Marketplace](./create-consulting-service-offer.md)
* [Bewährte Methoden für Angebotslistung](./gtm-offer-listing-best-practices.md)