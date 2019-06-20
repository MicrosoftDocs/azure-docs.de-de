---
title: Registerkarte „Marketplace“ für ein Azure-Anwendungsangebot
description: Verwenden Sie die Registerkarte „Marketplace“, um Marketingressourcen für ein Azure-Anwendungsangebot anzugeben.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 7ea6e6be0597a114b02fad8c41e37d21ce1f6028
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942965"
---
# <a name="azure-application-marketplace-tab"></a>Registerkarte „Marketplace“ für eine Azure-Anwendung

Verwenden Sie die Registerkarte „Marketplace“, um Ihre Azure-Anwendung zu beschreiben und Marketingressourcen bereitzustellen. Diese Registerkarte umfasst die folgenden Formulare: „Übersicht“, „Marketingartefakte“, „Leadverwaltung“ und „Rechtlich“.

## <a name="overview-form"></a>Formular „Übersicht“

Das Formular „Übersicht“ hat die erforderlichen und optionalen Felder, die in der nächsten Bildschirmaufnahme gezeigt sind. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

![Formular „Übersicht“](./media/azureapp-marketplace-overview.png)

In der folgenden Tabelle sind die Einstellungen beschrieben, mit denen eine digitale Ladenzeile (Storefront) für das Angebot erstellt wird.   Die mit einem Sternchen gekennzeichneten Felder müssen ausgefüllt werden.

|      Feld         |    BESCHREIBUNG    |
|  ---------------   |  ---------------  |
| **Titel\***        | Der Titel des Angebots. Er wird im Marketplace gut sichtbar angezeigt. Die maximale Länge beträgt 50 Zeichen. |
| **Zusammenfassung\***      | Kurze Zusammenfassung zum Angebot. Die maximale Länge beträgt 100 Zeichen.           |
| **Lange Zusammenfassung\*** | Längere Zusammenfassung zum Angebot (kann auch mit der Zusammenfassung identisch sein). Die maximale Länge beträgt 256 Zeichen.           |
| **Beschreibung\***  | Beschreibung des Angebots. Die maximale Länge beträgt 3000 Zeichen. Einfache HTML-Formatierung ist zulässig, einschließlich &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; und Headertags.  |
| **Marketingbezeichner\*** | Eine eindeutige URL, die diesem Angebot zugeordnet wird und normalerweise den Namen Ihrer Organisation und der Lösung enthält. Die maximale Länge beträgt 50 Zeichen. Wählen Sie einen kurzen, benutzerfreundlichen Marketingbezeichner für Ihren Dienst. Dieser Bezeichner wird in den Marketplace-URLs für dieses Angebot verwendet. Angenommen, Ihre Herausgeber-ID lautet „contoso“ und Ihr Marketingbezeichner lautet „sampleApp“, dann ist die URL für Ihr Angebot im Azure Marketplace gleich https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp.  
| **Abonnement-IDs für die Vorschauversion\*** | Sie können zwischen 1 und 100 Abonnement-IDs für Benutzer der Vorschauversion hinzufügen. Diese auf der Whitelist befindlichen Abonnements haben Zugriff auf Ihr Angebot, solange es in der Vorschau verfügbar ist, nachdem es veröffentlicht wurde und bevor es live geschaltet wird.          |
| **Nützliche Links**    | Optional können Sie für Benutzer Ihres Angebots Links zu verschiedenen Ressourcen bereitstellen, z.B. Support, Dokumentation, Foren usw.  Es empfiehlt sich, mindestens einen Link hinzuzufügen, der zu Ihrer Dokumentation führt.            |
| **Vorgeschlagene Kategorien (max. 5)\*** | Wählen Sie bis zu fünf Kategorien aus. Die ausgewählten Kategorien werden verwendet, um Ihr Angebot den Produktkategorien zuzuordnen, die im Azure Marketplace und im Azure-Portal verfügbar sind. Sie werden auf Suchseiten und auf Ihrer Produktdetailseite angezeigt. |
|  |  |


## <a name="marketing-artifacts"></a>Marketingartefakte

Das Formular „Marketingartefakte“ hat die erforderlichen und optionalen Felder, die in der nächsten Bildschirmaufnahme gezeigt sind. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

![Formular „Marketingartefakte“](./media/azureapp-marketplace-artifacts.png)

In der folgenden Tabelle sind die Marketingartefakte beschrieben.

|      Feld         |    BESCHREIBUNG    |
|  ---------------   |  ---------------  |
| **Klein\***        | Kleines Logo: PNG-Format, 40 x 40 Pixel     |
| **Mittel\***       | Mittleres Logo: PNG-Format, 90 x 90 Pixel    |
| **Groß\***        | Großes Logo: PNG-Format, 115 x 115 Pixel   |
| **Breit\***         | Breites Logo: PNG-Format, 255 x 115 Pixel    |
| **Hero**           | Optionales Herologo: PNG-Format, 815 x 290 Pixel **Hinweis:** Nachdem das Herologo hochgeladen wurde, kann es nicht mehr gelöscht werden. |
| **Bildschirmfotos (max. 5)** |        Screenshots werden auf Ihrer Produktdetailseite angezeigt. Damit können Sie sehr gut die Features und die Funktionsweise Ihrer App visuell veranschaulichen. Sie können beispielsweise Architekturdiagramme oder Anwendungsfälle präsentieren. Screenshots sind optional, und pro SKU sind maximal 5 möglich. So fügen Sie einen Screenshot hinzu:<ul><li>Wählen Sie **+ Screenshot hinzufügen** aus, um das Fenster „Screenshot“ zu öffnen.</li><li>**Name**: Geben Sie einen Namen/Titel ein (maximale Länge ist 100 Zeichen).</li><li>**Hochladen**: Laden Sie das Bild hoch. Es muss im PNG-Format vorliegen und 533 x 324 Pixel groß sein.</li></ul>           |
| **Video hinzufügen**      | Optional werden Videos auf Ihrer Produktdetailseite angezeigt. Damit können Sie sehr gut die Features und die Funktionsweise Ihrer Anwendung visuell veranschaulichen. So fügen Sie ein Video hinzu: <ul><li>Wählen Sie **+ Video hinzufügen** aus, um das Fenster „Video“ zu öffnen.</li><li>**Name**: Geben Sie einen Namen/Titel ein (maximale Länge ist 100 Zeichen).</li><li>**Link**: Geben Sie die URL für die Website ein, auf der das Video hostet wird (YouTube oder Vimeo).</li><li>**Miniaturansicht**: Laden Sie das Bild einer Miniaturansicht hoch. Es muss im PNG-Format vorliegen und 533 x 324 Pixel groß sein.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Artefaktbeispiele in Azure Marketplace

In der nächsten Bildschirmaufnahme ist ein Beispiel für ein Marketplace-Suchergebnis gezeigt.

![Suchergebnis für Marketplace-Angebot](./media/azureapp-marketplace-example-browse.png)

Die folgende Abbildung zeigt, wie das Angebot im Marketplace angezeigt wird, nachdem ein Kunde im Suchergebnis auf die Kachel des Angebots geklickt hat.

![Suchergebnisdetails für Marketplace-Angebot](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Artefaktbeispiele im Azure-Portal

In den folgenden Bildschirmaufnahmen ist dargestellt, wie ein Angebot im Azure-Portal angezeigt wird. Das Anwendungsangebot in diesem Beispiel wird gefunden, indem Sie zu **Marketplace > Alles > Entwickeln und Testen > Jenkins** navigieren. Für das Jenkins-Angebot werden ein Logo, ein Titel und der Anzeigename des Herausgebers angezeigt.

![Navigieren zu Angeboten im Azure-Portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Die nächste Bildschirmaufnahme zeigt detaillierte Informationen über die Anwendung, wenn ein Benutzer „Jenkins“ auswählt.

![Angebotsdetails im Azure-Portal](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Richtlinien für Logos

Alle in das Cloud-Partnerportal hochgeladenen Logos müssen folgende Richtlinien erfüllen:

- Die Farbpalette des Azure-Designs ist einfach und geradlinig. Verwenden Sie auf Ihrem Logo möglichst wenige Primär- und Sekundärfarben.
- Die Designfarben des Azure-Portals sind Weiß und Schwarz. Verwenden Sie diese Farben nicht als Hintergrundfarbe für Ihre Logos. Verwenden Sie eine Farbe, die Ihre Logos im Azure-Portal markant hervorstechen lässt. Sie sollten einfache Primärfarben verwenden. Wenn Sie einen transparenten Hintergrund verwenden, sollten Sie sicherstellen, dass Logos und Text nicht weiß, schwarz oder blau sind.
- Verwenden Sie im Logo keinen Hintergrund mit Farbverlauf.
- Platzieren Sie nach Möglichkeit keinen Text auf dem Logo, auch nicht Ihren Firmen- oder Markennamen. Das Erscheinungsbild Ihres Logos sollte klar und direkt und frei von Farbverläufen sein.
- Strecken Sie das Logo nicht.


#### <a name="hero-logo"></a>Herologo

Ein Herologo ist optional.

>[!IMPORTANT]
>Das Herologo kann nicht mehr gelöscht werden, nachdem es hochgeladen wurde.

Für ein Herologo gelten folgende Richtlinien:

- Ein schwarzer, weißer oder transparenter Hintergrund ist nicht zulässig.
- Vermeiden Sie helle Farben als Hintergrund des Logos. Der Anzeigename des Herausgebers, der Plantitel und die ausführliche Angebotszusammenfassung werden in weißer Schrift angezeigt und müssen sich vom Hintergrund deutlich unterscheiden.
- Vermeiden Sie beim Entwerfen des Logos möglichst die Verwendung von Text. Der Name des Herausgebers, der Plantitel, die ausführliche Angebotszusammenfassung und die Erstellen-Schaltfläche werden programmgesteuert in das Logo eingebettet, wenn das Angebot live geschaltet wird.
- Fügen Sie auf der rechten Seite des Herologos einen leeren rechteckigen Bereich ein. Dieser leere Bereich hat eine Größe von 415 x 100 Pixel und ist 370 Pixel vom linken Rand entfernt.


## <a name="lead-management"></a>Leadverwaltung

Die Formular „Leadverwaltung“ hat ein optionales Feld zum Konfigurieren der Leadverwaltung. Um eine Leadverwaltung zu konfigurieren, wählen Sie „Leadziel“ in der Dropdownliste aus. In der nächsten Bildschirmaufnahme sind die verfügbaren Ziele gezeigt.

![Leadverwaltungsziel auswählen](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Wählen Sie das Informationssymbol aus, um diese Meldung anzuzeigen: „Wählen Sie das System aus, in dem Ihre Leads gespeichert werden. [Hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) erfahren Sie, wie Sie eine Verbindung mit Ihrem CRM-System herstellen.“

Weitere Informationen finden Sie unter [Konfigurieren von Kundenleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Rechtliche Hinweise

Verwenden Sie das Formular „Rechtliche Hinweise“, um die für jedes Angebot erforderliche rechtliche Dokumentation bereitzustellen.

Geben Sie die folgenden Informationen ein:

- **URL der Datenschutzrichtlinie\*** : Geben Sie einen Link zur Datenschutzrichtlinie Ihrer App ein.
- **Nutzungsbedingungen\*** : Geben Sie die Nutzungsbedingungen für Ihre App ein. Kunden müssen diese Bedingungen akzeptieren, bevor sie Ihre App testen können.

![Formular für rechtliche Hinweise](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Nächste Schritte

[Registerkarte „Support“](./cpp-support-tab.md)
