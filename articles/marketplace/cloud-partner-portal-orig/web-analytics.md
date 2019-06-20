---
title: Web Analytics
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 4b75b2cbd629204e7edbf1196eec9b03cb7a6736
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943155"
---
<a name="web-analytics"></a>Web Analytics
=============

Dieser Artikel enthält Anweisungen, wie Sie Web Analytics kennenlernen und verwenden können, um Ihr Unternehmen optimal zu unterstützen. Derzeit ist diese Insights-Registerkarte für alle AppSource-Angebote verfügbar.

Nachdem Sie jetzt Ihr Angebot erstellt und veröffentlicht haben, besteht der nächste Schritt darin, dessen Erfolg zu verfolgen und zu messen\'. Mit **Web Analytics** haben wir die Möglichkeit hinzugefügt, genau zu überprüfen, wie gut sich jedes Ihrer Angebote im Marketplace schlägt. Navigieren Sie zu Beginn Ihrer Tour auf der linken Seite des Cloud-Partnerportals zur Seite „Erkenntnisse“, um die neue Registerkarte für Analysen anzuzeigen.

![Seite „WebAnalytics“](./media/si-getting-started/WebAnalytics1.png)

Es wird ein ausführliches Dashboard für Ihre Herausgeber-ID angezeigt, das mit Microsoft Power BI erstellt wurde und es Ihnen ermöglicht, alle Ihre Angebote\' anzuzeigen, die täglich aktualisiert werden.

<a name="microsoft-campaigns"></a>**Microsoft-Kampagnen**
-----------------------

Damit Sie Ihre Angebote erweitern und das Wachstum Ihrer Angebote verfolgen können, haben wir die Möglichkeit geschaffen, **Microsoft-Kampagnen** im Cloud-Partnerportal zu nutzen. Kampagnen sind ein neu unterstütztes Feature für den Marketplace, mit dem Sie die verschiedenen Kanäle verfolgen können, die Kunden auf Ihre App-Detailseite senden.

### <a name="how-to-make-a-campaign"></a>**Gewusst wie: Erstellen einer Kampagne**

Kampagnen lassen sich am einfachsten als das Hinzufügen eines benutzerdefiniertes Wort bzw. eines benutzerdefinierten Begriffs zu Ihrer URL beschreiben, die auf Ihrer App-Detailseite im Marketplace landet. Google, Bing, LinkedIn und viele andere Websites bestärken Sie darin, einen Werbelink von ihrer Website zu Ihrer gewünschten Website zu erstellen.

Im Allgemeinen sollen diese Bemühungen dazu beitragen, neue Kunden zu Ihrem Produkt zu bringen, und es ist wichtig, den Erfolg zu messen, wie sich jeder Ihrer Kanäle entwickelt. Hier kommen die Kampagnen ins Spiel.

Es gibt zwei Möglichkeiten, eine eigene Kampagne zu erstellen.

1. Fügen Sie zu Ihrer URL den Abfrageparameter **mktcmpid** hinzu, der die Kampagne beschreibt und angibt, von welcher Seite bzw. welchem Ereignis diese Kunden stammen.

Sie können z. B. Folgendes verwenden: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Erweitert): Verwenden Sie eine unserer unterstützten, generischen Kampagnen-IDs in der URL. Wir möchten Sie mit zusätzlich zu verwendenden ref-Tags unterstützen, daher unterstützen wir die Konvention, diese zusätzlichen Tags automatisch zu erkennen:
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

Sie können z. B. Folgendes verwenden: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Sie können eine Kombination aus mehreren dieser Kampagnen-IDs wählen, um mehrere Quellen weiter zu identifizieren, die den Datenverkehr für die Kampagne beeinflussen, z. B. woher der Kunde kommt (E-Mail, Blog, Social Media-Quelle, usw.).

Beispiel:

1. Newsletter-Verweis: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn-Verweis: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Sicherstellen, dass Kampagnen alle Ihre Seiten durchlaufen**

Es kann ein Szenario vorkommen, in dem Ihre Kampagnen eine Zwischenseite aufweisen, auf der Sie den Datenverkehr zu dieser Seite lenken und dann die Kunden zum Marketplace senden. Es ist wichtig, dass Sie Ihre ersten Kampagnen-IDs an die endgültige URL weitergeben, die Sie an den Marketplace senden.

Beispiel:

1. Marketingmitarbeiter kauft Anzeigen bei Google, um den Datenverkehr zur Angebotsseite <https://contoso.com> des Unternehmens zu steigern. Diese Angebotsseite verfügt über einen \"Produkt testen\"-Link, der zu <https://appsource.com> führt.
2. Ein Benutzer klickt auf die Anzeige und landet auf der Angebotsseite seines Unternehmens\'.
    1.  Verweis-URL = google.com
    2.  URL der Angebotsseite = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. Der Benutzer klickt auf den Link \"Produkt testen\" und gelangt zur AppSource.
    1. Verweis-URL = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. URL der Angebotsseite (**Stellen Sie sicher, dass dieser URL utm\_campaign und utm\_source hinzugefügt wurde**) = [https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?**utm\_ campaign=MyCampaignAdName&utm\_ source=MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Auswerten des Erfolgs einer Kampagne
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Seitenaufrufe nach Kampagne**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Dies ist die Aufschlüsselung der einzelnen täglichen Seitenaufrufe nach der Kampagne, aus der sie stammen.

### <a name="conversion-rate-by-campaign"></a>**Konvertierungsrate nach Kampagne**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Ähnlich wie wir die Konvertierungsrate Ihres gesamten Angebots darstellen, können Sie in diesem Diagramm die Aufschlüsselung der Entwicklung Ihrer verschiedenen Kampagnen sehen. Dieses Diagramm soll Ihnen helfen zu erkennen, woher Ihre Kampagnen mit höherer Konvertierungsrate stammen.

### <a name="distribution-by-campaign"></a>**Verteilung nach Kampagne**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Ähnlich wie wir die Domänen Ihrer Kunden betrachten, können Sie in diesem Diagramm die Verteilung Ihrer Daten pro Kampagne sehen, über die die Benutzer zum Marketplace gelangen. \_NoCampaign bedeutet, dass der Kunde keine Kampagnen-ID in der URL angegeben hatte, als er zum Marketplace navigierte.

<a name="next-steps"></a>**Next Steps**
--------------

Nachdem Sie jetzt die Möglichkeit haben, den Erfolg Ihrer Angebote zu verfolgen, möchten wir Sie ermutigen, Ihre eigenen Kampagnen zu erstellen.

Wenn Sie Fragen/Featureanforderungen haben, teilen Sie diese über die Feedbackoption in der oberen rechten Ecke mit.

![Feedback im Cloud-Partnerportal](./media/si-getting-started/WebAnalytics5.png)
