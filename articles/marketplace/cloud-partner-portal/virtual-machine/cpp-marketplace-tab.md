---
title: Registerkarte „Marketplace“ für VMs im Cloud-Partnerportal für Azure Marketplace
description: Enthält eine Beschreibung der Registerkarte „Marketplace“, die Sie beim Erstellen eines VM-Angebots für den Azure Marketplace verwenden.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d9b259ba93be369658638d034f109a3a6ba4c4a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938185"
---
# <a name="virtual-machine-marketplace-tab"></a>Registerkarte „Marketplace“ für VMs

Auf der Registerkarte **Marketplace** auf der Seite **Neues Angebot** können Sie für Ihre potenziellen Kunden Informationen zu Marketing, Vertrieb und rechtlichen Fragen und Vereinbarungen bereitstellen und die über den Marketplace generierten Leads verwalten. Dieses lange Formular ist in vier Abschnitte unterteilt: **Übersicht**, **Marketingartefakte**, **Leadverwaltung** und **Rechtlich**.


## <a name="overview-section"></a>Abschnitt „Übersicht“
In diesem Abschnitt geben Sie allgemeine Informationen zu Ihrem Azure Marketplace-Angebot ein.  Mit einem an den Feldnamen angefügten Sternchen (*) wird angegeben, dass eine Eingabe erforderlich ist.

![Abschnitt „Übersicht“ der Registerkarte „Marketplace“ für virtuelle Computer](./media/publishvm_008.png)

In der folgenden Tabelle werden Zweck und Inhalt dieser Felder beschrieben. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|  **Feld**                |     **Beschreibung**                                                          |
|  ---------                |     ---------------                                                          |
| **Titel\***                 | Der Titel des Angebots, meist der lange, formelle Name. Dieser Titel wird im Marketplace gut sichtbar angezeigt.  Die maximale Länge beträgt 50 Zeichen. |
| **Zusammenfassung\***               | Eine kurze Beschreibung des Zwecks oder der Funktion der Lösung.  Die maximale Länge beträgt 100 Zeichen. |
| **Lange Zusammenfassung\***          | Eine Beschreibung des Zwecks oder der Funktion der Lösung.  Die maximale Länge beträgt 256 Zeichen. |
| **Beschreibung\***           | Eine Beschreibung der Lösung.  Die maximale Länge beträgt 3.000 Zeichen, und die einfache HTML-Formatierung wird unterstützt. |
| **Microsoft CSP-Handelspartner-Kanal\*** | Die Nutzung des CSP-Partnerkanals (Cloud Solution Provider) ist jetzt verfügbar.  Unter [Cloud Solution Providers](../../cloud-solution-providers.md) finden Sie weitere Informationen zum Vermarkten Ihres Angebots über die Microsoft CSP-Partnerkanäle. |
| **Marketingbezeichner\***  | Eine eindeutige URL, die diesem Angebot zugeordnet wird und normalerweise den Namen Ihrer Organisation und der Lösung enthält. Die maximale Länge beträgt 50 Zeichen.  Beispiel: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Abonnement-IDs für die Vorschauversion\*** | Sie können zwischen 1 und 100 Abonnement-IDs für Benutzer hinzufügen, die die Vorschauversion testen. Diese auf der Whitelist befindlichen Abonnements haben Zugriff auf das Angebot, nachdem es veröffentlicht wurde und bevor es live geschaltet wird. |
| **Nützliche Links**          | Hinzufügen von URLs zu Dokumenten, Versionshinweisen, häufig gestellten Fragen usw. |
| **Vorgeschlagene Kategorien (max. 5)\*** | Mehrfachauswahl von geschäftlichen und technischen Kategorien, denen das Angebot am ehesten zugeordnet werden kann.  Maximal fünf Kategorien können ausgewählt werden.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Abschnitt „Marketingartefakte“

Dieser zweite Abschnitt ist in drei Unterabschnitte unterteilt: **Logos**, **Screenshot** und **Videos**. Nur bei Logos handelt es sich um erforderliche Marketingartefakte, aber es ist ratsam, alle Angaben vorzunehmen, um die Attraktivität für die Kunden zu erhöhen. 

![Abschnitt „Marketingartefakte“ der Registerkarte „Marketplace“ im Formular „Neues Angebot“ für virtuelle Computer](./media/publishvm_009.png)

In der folgenden Tabelle werden Zweck und Inhalt dieser Felder beschrieben. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|  **Feld**                |     **Beschreibung**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos*  |  |
| **Klein\***                 | ICO-Bitmap, 40x40 Pixel                                                      |
| **Mittel\***                | ICO-Bitmap, 90x90 Pixel                                                      |
| **Groß\***                 | ICO-Bitmap, 115x115 Pixel                                                   |
| **Breit\***                  | ICO-Bitmap, 255x115 Pixel                                                    |
| **Hero**                  | Bitmap 815x290.  Optional, aber nachdem das Herologo hochgeladen wurde, kann es nicht mehr gelöscht werden. |
| *Screenshots*  | Optional, aber maximal fünf Screenshots pro SKU. |
| **Name**                  | Name oder Titel <!-- TODO - max char length? none specified in UI -->                               |
| **Image**                 | Bildschirmaufnahme, 533x324 Pixel                                         |
| *Videos*  |  |
| **Name**                  | Name oder Titel  <!-- TODO - max char length? -->                              |
| **Link**                  | Video-URL, unter YouTube oder Vimeo gehostet                                        |
| **Miniaturansicht**             | Bitmap 533x324                                                               |
|   |   |

### <a name="logo-guidelines"></a>Richtlinien für Logos

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alle in das Cloud-Partnerportal hochgeladenen Logos müssen folgende Richtlinien erfüllen:

*  Die Farbpalette des Azure-Designs ist einfach und geradlinig. Verwenden Sie auf Ihrem Logo möglichst wenige Primär- und Sekundärfarben.
*  Die Designfarben des Azure-Portals sind Weiß und Schwarz. Daher sollten Sie diese Farben nicht als Hintergrundfarbe für Ihre Logos verwenden. Verwenden Sie Farben, die Ihre Logos im Azure-Portal markant hervorstechen lassen. Sie sollten einfache Primärfarben verwenden. Wenn Sie transparenten Hintergrund verwenden, sollten Sie sicherstellen, dass Logos oder Textelemente nicht weiß, schwarz oder blau sind.
*  Verwenden Sie im Logo keinen Hintergrund mit Farbverlauf.
*  Platzieren Sie nach Möglichkeit keinen Text auf dem Logo – auch nicht Ihren Firmen- oder Markennamen. Das Erscheinungsbild Ihres Logos sollte klar und direkt und frei von Farbverläufen sein.
*  Vermeiden Sie es, das Logo zu strecken.

#### <a name="hero-logo"></a>Herologo

Das Herologo ist optional. Nachdem das Herologo aber einmal hochgeladen wurde, kann es nicht mehr gelöscht werden.  Für das Herologosymbol sollten die folgenden Richtlinien eingehalten werden:

*  Ein schwarzer, weißer oder transparenter Hintergrund ist für Herosymbole nicht zulässig.
*  Vermeiden Sie helle Farben als Hintergrund des Herosymbols.  Anzeigename des Herausgebers, Plantitel und ausführliche Angebotsübersicht werden in weißer Schrift angezeigt und müssen sich vom Hintergrund deutlich unterscheiden.
*  Vermeiden Sie beim Entwerfen des Herologos möglichst die Verwendung von Text.  Der Name des Herausgebers, der Plantitel, die ausführliche Angebotsübersicht und die Erstellen-Schaltfläche werden programmgesteuert in das Herosymbol eingebettet, wenn das Angebot live geschaltet wird. 
* Fügen Sie auf der rechten Seite Ihres Herologos ein freies Rechteck ein, das eine Größe von 415x100 Pixel hat und 370 Pixel vom linken Rand entfernt ist.  

Hier ist beispielsweise das Herosymbol für den Azure Container Service angegeben.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Beispiel: Herologo für Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Beispiel für Marketinginformationen 

In der folgenden Abbildung ist dargestellt, wie auf der Hauptproduktseite von Microsoft Windows Server Marketinginformationen angezeigt werden.

![Beispiel: Produktseite für Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Abschnitt „Leadverwaltung“

Im dritten Abschnitt können Sie Kundenleads sammeln, die über Ihre Azure Marketplace-Angebote generiert werden. Für diese Leadinformationen werden die folgenden Speicheroptionen (in einer Dropdownliste) bereitgestellt.

* **Keine**: Dies ist die Standardeinstellung, und es werden keine Leadinformationen gesammelt.
* Azure-Tabelle: Die Informationen werden in die Azure-Tabelle geschrieben, die mit einer Verbindungszeichenfolge angegeben wird.
* Dynamics CRM Online: Die Informationen werden in die [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/)-Instanz geschrieben, die mit einer URL und Anmeldeinformationen für die Authentifizierung angegeben wird.
* HTTPS-Endpunkt: Die Informationen werden als JSON-Nutzlast auf den angegebenen HTTPS-Endpunkt geschrieben.
* Marketo: Die Informationen werden in die angegebene [Marketo](https://www.marketo.com/)-Instanz geschrieben, die mit einer Server-ID, Munchkin-ID und Formular-ID angegeben wird.
* Salesforce: Die Informationen werden in eine [Salesforce](https://www.salesforce.com/)-Datenbank geschrieben, die mit einem Objektbezeichner angegeben wird.

Nachdem Sie Ihr Angebot erfolgreich veröffentlicht haben, wird die Leadverbindung überprüft und ein Testlead automatisch an das konfigurierte Ziel gesendet. Die Leadinformationen sollten fortlaufend verwaltet werden. Es ist daher ratsam, diese Einstellungen sofort zu aktualisieren, wenn Änderungen an Ihrer Architektur für die Kundenverwaltung vorgenommen werden.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Abschnitt „Rechtliche Hinweise“

In diesem letzten Abschnitt können Sie die erforderlichen rechtlichen Dokumente bereitstellen, die für jedes Angebot erforderlich sind.  

|  **Feld**                    |     **Beschreibung**                                        |
|  ---------                    |     ---------------                                        |
| **URL der Datenschutzrichtlinie\***      | Die URL Ihrer bereitgestellten Datenschutzrichtlinie.                          |
| **Standardvertrag verwenden?\***  |   |
| **Nutzungsbedingungen\***            | Die Richtlinie als einfacher Text oder im einfachen HTML-Format.                       |
|  |  |


## <a name="next-steps"></a>Nächste Schritte

Auf der nächsten Registerkarte [Support](./cpp-support-tab.md) geben Sie Ressourcen für den technischen Support und den Benutzersupport für Ihr Angebot an.
