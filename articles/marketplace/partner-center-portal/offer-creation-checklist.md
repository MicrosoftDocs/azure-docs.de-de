---
title: Prüfliste für die Angebotserstellung – Kommerzieller Marketplace für Azure
description: Die Informationen, die Sie bei der Angebotserstellung angeben können. – Kommerzieller Marketplace für Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 904058c2c98c8ded2ea9c91e8aa7ec595aa49b05
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482000"
---
# <a name="offer-creation-checklist"></a>Prüfliste für die Angebotserstellung

Bei der Angebotserstellung werden Sie durch mehrere Seiten geführt. Hier sind die Informationen, die Sie auf den einzelnen Seiten angeben können, sowie Links zu weiteren Informationen über die einzelnen Elemente aufgeführt.

Elemente, deren Angabe oder Festlegung erforderlich ist, sind unten aufgeführt. Einige Bereiche sind optional oder bieten Standardwerte, dass Sie nach Bedarf ändern können. Sie müssen diese Abschnitte nicht in der hier aufgeführten Reihenfolge bearbeiten.

| **Element**    | **Zweck**  |
| :---------- | :-------------------|
| [**Modales Fenster „Neues Angebot“** ](#new-offer-modal) | Erfasst Informationen zur Angebotsidentität.  |
| [Seite „Angebotseinrichtung“](#offer-setup-page) | Hier können Sie sich für die Verwendung wichtiger Features entscheiden und auswählen, wie Ihr Angebot über Microsoft verkauft werden soll.  |
| [Seite „Eigenschaften“](#properties-page) | Hier definieren Sie die Kategorien und Branchen, die zum Gruppieren Ihres Angebots in den Marketplaces verwendet werden, die Verträge für Ihr Angebot und die Version Ihrer App. |
| [Seite „Angebotsliste“](#offer-listing-page) | Hier definieren Sie die Angebotsdetails, die im Marketplace angezeigt werden sollen, einschließlich der Beschreibung des Angebots und der Marketingressourcen. |
| [Seite „Vorschau“](#preview-page) | Hier definieren Sie eine eingeschränkte Vorschauzielgruppe für das Freigeben Ihres Angebots, bevor Sie das Angebot für die größere Marketplace-Zielgruppe(n) live veröffentlichen. |
| [Seite „Technische Angebotskonfiguration“](#technical-configuration-page)  | Nur verfügbar, wenn Sie sich für den Vertrieb des Angebots über Microsoft entscheiden. Hier definieren Sie die technischen Informationen (URL-Pfad, Webhook, Mandanten-ID und App-ID) für die Verbindung mit Ihrem Angebot. |
| [**Modales Fenster „Neuer Plan“** ](#plan-identity-modal) | Erfasst Informationen zur Planidentität.  |
| [Seite „Planlisting“](#plan-listing-page)  | Nur verfügbar, wenn Sie sich für den Vertrieb des Angebots über Microsoft entscheiden. Hier definieren Sie die Informationen, die zum Auflisten des Plans im Marketplace verwendet werden.  |
| [Seite „Preise und Verfügbarkeit des Plans“](#plan-pricing--availability-page)  | Nur verfügbar, wenn Sie sich für den Vertrieb des Angebots über Microsoft entscheiden.  Erfasst die Geschäftsmerkmale (Preismodell), Zielgruppe und Marktverfügbarkeit für jeden Plan (Version) Ihres Angebots.  |
| [Seite „Testversionlisting“](#test-drive-listing-page)  | Nur verfügbar, wenn Sie sich zur Bereitstellung einer Testversion für Ihr Angebot entscheiden. Hier definieren Sie die Informationen, die zum Auflisten der Testversion im Marketplace verwendet werden.  |
| Seite „Technische Konfiguration der Testversion“  | Nur verfügbar, wenn Sie sich zur Bereitstellung einer Testversion für Ihr Angebot entscheiden. Hier definieren Sie die technischen Informationen für die Demonstration (oder „Testversion“), sodass Kunden Ihr Angebot testen können, bevor sie sich zum Kauf entschließen.  |
| [Seite „Überprüfen und veröffentlichen“](#review-and-publish-page)  | Wählen Sie die Änderungen aus, die Sie veröffentlichen möchten, sehen Sie sich den Status jeder Seite an, und stellen Sie Hinweise für das Zertifizierungsteam bereit.  |


## <a name="new-offer-modal"></a>Modales Fenster „Neues Angebot“ 

Die ersten Informationen, zu deren Eingabe Sie aufgefordert werden, sind ein Name und eine ID für Ihr Angebot. 

| **Feldname**    | **Hinweise**   |  
| :---------------- | :-----------| 
| Angebots-ID  | Erforderlich. Kann nach der Erstellung nicht geändert werden. Maximal 50 Zeichen und darf nur klein geschriebene alphanumerische Zeichen, Bindestriche und Unterstriche enthalten. |
| Angebotsname  | Erforderlich. |

## <a name="offer-setup-page"></a>Seite „Angebotseinrichtung“

Auf der Seite „Angebotseinrichtung“ können Sie verschiedene Kanäle und Verkaufsbewegungen auswählen und die Verwendung wichtiger Features wie Testversion und Kundenleads deklarieren. 

| **Feldname**    | **Hinweise**   | 
| :---------------- | :-----------|  
| Möchten Sie über Microsoft verkaufen?  | Erforderlich. Standardwert: Ja |
| Wie sollen potenzielle Kunden mit der Angebotsliste interagieren? (Handlungsaufforderung)  | Erforderlich, wenn der Verkauf nicht über Microsoft erfolgt. Standardwert: Kostenlose Testversion, Optionen: „Jetzt anfordern“, „Kostenlose Testversion“, „Kontakt mit mir aufnehmen“. |
| Test-URL  | Erforderlich, wenn „Kostenlose Testversion“ ausgewählt ist, um die Art der Interaktion der Kunden mit dem Angebot anzugeben. |
| Angebots-URL  | Erforderlich, wenn „Jetzt anfordern“ ausgewählt ist, um die Art der Interaktion der Kunden mit dem Angebot anzugeben. |
| Kanäle  | Optional. Standardwert: Nicht im CSP-Kanal (Handelspartner) registriert.  |
| Testversion | Optional. Standardwert: Keine Testversion aktiviert.  |
| Typ der Testversion | Erforderlich, wenn eine Testversion aktiviert ist. Standardwert: Keine Auswahl. Optionen: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, Logik-App, Power BI.  |
| Leadverwaltung – Verbindung mit einem CRM-System | Erforderlich, wenn der Verkauf über Microsoft erfolgt oder in der Liste die Option „Kontakt mit mir aufnehmen“ aufgeführt ist. Standardwert: Kein CRM-System verbunden. CRM-Optionen: Azure-Tabelle, Azure-Blob, Dynamics CRM Online, HTTPS-Endpunkt, Marketo, Salesforce  |

## <a name="properties-page"></a>Eigenschaftenseite

Auf der Eigenschaftenseite definieren Sie die Kategorien und Branchen, die zum Gruppieren Ihres Angebots in den Marketplaces verwendet werden, die Verträge für Ihr Angebot und die Version Ihrer App. Achten Sie darauf, dass Sie auf dieser Seite vollständige und genaue Angaben zu Ihrem Angebot machen, damit es richtig angezeigt und der richtigen Kundengruppe angeboten wird. 

| **Feldname**    | **Hinweise**   | 
| :---------------- | :-----------|  
| Kategorie und Unterkategorie | Erforderlich 1, maximal 3. Standardwert: Keine Auswahl. |
| Branchen und Unterbranchen | Optional. Maximal 2 L1-Branchen und maximal 2 Unterbranchen für jede L1-Branche, Standardwert: Keine Auswahl. |
| App-Version  | Optional. Standardwert: Keine. |
| Standardvertrag verwenden  | Optional. Standardwert: Nicht ausgewählt.  | |
| Nutzungsbedingungen  | Erforderlich, wenn der Standardvertrag nicht ausgewählt ist.  |

## <a name="offer-listing-page"></a>Seite „Angebotsliste“

Auf der Listenseite stellen Sie den Text und Bilder bereit, die Kunden beim Aufrufen des Listings für Ihr Angebot im Marketplace angezeigt werden. 

| **Feldname**    | **Hinweise**   |
| :---------------- | :-----------| 
| NAME  | Erforderlich, maximal 50 Zeichen. |
| Zusammenfassung  | Erforderlich, maximal 100 Zeichen. | 
| BESCHREIBUNG  | Erforderlich, maximal 3000 Zeichen. |
| Anweisungen für den Einstieg  | Erforderlich, maximal 3000 Zeichen. |
| Anweisungen für den Einstieg  | Erforderlich, maximal 3000 Zeichen. |
| Suchbegriffe  | Optional, empfohlen, maximal 3 Schlüsselwörter. |
| URL zur Datenschutzrichtlinie  | Erforderlich. |
| URL zu Marketingmaterial für das CSP-Programm  | Optional. |
| Nützliche Links: Titel und URL  | Optional. |
| Unterstützende Dokumente: Titel und Datei  | Erforderlich, mindestens 1 und maximal 3. Muss im PDF-Dateiformat vorliegen. |
| Screenshots  | Erforderlich, mindestens 1 und maximal 5 Screenshots; vier oder mehr empfohlen. Muss im PNG-Format 1280 x 720 vorliegen. |
| Store-Logos (Klein, Mittel, Groß, Breit, Hero)  | Klein (48 x 48) und Groß (216 x 216) erforderlich; andere Größen sind optional, jedoch empfohlen: Mittel (90 x 90), Breit (255 x 115), Hero (815 x 290). Muss im PNG-Format vorliegen. |
| Videos: Name, URL und Miniaturansicht  | Optional, empfohlen, maximal 4 Videos. Miniaturansicht muss im PNG-Format 1280 x 720 vorliegen. Video muss in YouTube oder Vimeo gehostet werden. |
| Kontakte (CSP-Programm, Engineering, Support)  | Kontakt für Engineering und Support erforderlich (Name, E-Mail und Telefonnummer); Kontakt für CSP-Programm optional, jedoch empfohlen. |
| Support-URL  | Erforderlich. |

## <a name="preview-page"></a>Seite „Vorschau“

Auf der Seite „Vorschau“ geben Sie die Zielgruppe an, die Zugriff auf Ihre Angebotsvorschau haben soll, um sicherzustellen, dass das Angebot alle Ihre Anforderungen erfüllt, bevor es live geschaltet wird. 

| **Feldname**    | **Hinweise**   | 
| :---------------- | :-----------| 
| AAD/MSA: E-Mail und Beschreibung | Erforderlich, mindestens 1 und maximal 10 bei manueller Eingabe oder bis zu 20 bei Hochladen einer CSV-Datei. |

## <a name="technical-configuration-page"></a>Seite „Technische Konfiguration“ 

Auf der Seite „Technische Konfiguration“ geben Sie die technischen Informationen an, die von Microsoft für die Verbindung zu Ihrem Angebot verwendet werden. Diese Seite wird Ihnen nur angezeigt, wenn Sie sich für den Verkauf über Microsoft entschieden haben.

| **Feldname**    | **Hinweise**   |  
| :---------------- | :-----------| 
| URL der Angebotsseite | Erforderlich, wenn der Verkauf über Microsoft erfolgt. |
| Verbindungswebhook | Erforderlich, wenn der Verkauf über Microsoft erfolgt. |
| Azure AD-Mandanten-ID | Erforderlich, wenn der Verkauf über Microsoft erfolgt. |
| Azure AD-App-ID | Erforderlich, wenn der Verkauf über Microsoft erfolgt. |

## <a name="plan-identity-modal"></a>Modales Fenster „Planidentität“

Die ersten Informationen, zu deren Eingabe Sie aufgefordert werden, sind ein Name und eine ID für Ihren Plan. Diese Seite wird Ihnen nur angezeigt, wenn Sie sich für den Verkauf über Microsoft entschieden haben.

| **Feldname**    | **Hinweise**   |  
| :---------------- | :-----------| 
| Plan-ID  | Erforderlich, wenn der Verkauf über Microsoft erfolgt. Kann nach der Erstellung nicht geändert werden. Maximal 50 Zeichen und darf nur klein geschriebene alphanumerische Zeichen, Bindestriche und Unterstriche enthalten. |
| Plan Name  | Erforderlich, wenn der Verkauf über Microsoft erfolgt. Muss für alle Pläne im Angebot eindeutig sein. Maximal 50 Zeichen. |

## <a name="plan-listing-page"></a>Seite „Planlisting“

Auf der Seite „Planlisting“ stellen Sie den Text für Kunden bereit, der beim Aufrufen des Plans im Marketplace angezeigt wird. Diese Seite wird Ihnen nur angezeigt, wenn Sie sich für den Verkauf über Microsoft entschieden haben.

| **Feldname**    | **Hinweise**   |  
| :---------------- | :-----------| 
| Planbeschreibung   | Erforderlich, wenn der Verkauf über Microsoft erfolgt. Maximal 500 Zeichen. | |

## <a name="plan-pricing--availability-page"></a>Seite „Preise und Verfügbarkeit des Plans“

Auf der Seite „Preise und Verfügbarkeit des Plans“ definieren Sie die Geschäftsmerkmale, Zielgruppe und Marktverfügbarkeit für jeden Plan (Version) Ihres Angebots. Diese Seite wird Ihnen nur angezeigt, wenn Sie sich für den Verkauf über Microsoft entschieden haben.

| **Feldname**    | **Hinweise**   | 
| :---------------- | :-----------| 
| Marktverfügbarkeit  | Erforderlich, mindestens 1 und maximal 141. |
| Preismodell  | Erforderlich. Standardwert: Pauschalpreis. Optionen: Pauschalpreis, pro Benutzer. |
| Minimale und maximale Arbeitsplätze  | Optional, nur bei Auswahl eines arbeitsplatzbasierten Preismodells verfügbar. |
| Abrechnungszeitraum  | Erforderlich. Standardwert: Monatlich. Optionen: Monatlich, Jährlich. |
| Preis  | Bei Auswahl des monatlichen Abrechnungszeitraums ist der Betrag in US-Dollar pro Monat erforderlich; bei Auswahl des jährlichen Abrechnungszeitraums ist der Betrag in US-Dollar pro Jahr erforderlich. |
| Planzielgruppe  | Optional. Standardwert: Öffentlicher Plan. Optionen: Öffentlich, Privat nach Mandanten-ID |
| Eingeschränkte Planzielgruppe (Mandanten-ID und Beschreibung)  | Erforderlich, wenn der private Plan ausgewählt ist. Mindestens 1 und maximal 10 Mandanten-IDs bei manueller Eingabe. Maximal 20000 bei Import einer CSV-Datei. |

## <a name="test-drive-listing-page"></a>Seite „Testversionlisting“

Nur verfügbar, wenn Sie sich zur Bereitstellung einer Testversion für Ihr Angebot entscheiden. Hier definieren Sie die Informationen, die zum Auflisten der Testversion im Marketplace verwendet werden.

| **Feldname**    | **Hinweise**   | 
| :---------------- | :-----------| 
| BESCHREIBUNG  | Erforderlich. |
| Benutzerhandbuch: Name und Datei  | Erforderlich, maximal 1 Dokument. Muss im PDF-Format vorliegen. |
| Video: Name, URL und Miniaturansicht  | Optional, empfohlen. Miniaturansicht muss im JPGP- oder PNG-Format 533 x 324 vorliegen. Video muss in YouTube oder Vimeo gehostet werden. |

## <a name="review-and-publish-page"></a>Seite „Überprüfen und veröffentlichen“

| **Feldname**    | **Hinweise**   | 
| :---------------- | :-----------| 
| Hinweise zur Zertifizierung  | Optional. |

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines neuen SaaS-Angebots](./create-new-saas-offer.md)
