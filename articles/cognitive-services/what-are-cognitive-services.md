---
title: Was ist Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Mit Cognitive Services kann jeder Entwickler KI nutzen, ohne mit Machine Learning und Data Science vertraut sein zu müssen. Alles, was Sie brauchen, ist ein API-Aufruf in Ihrer Anwendung, um ihren Apps Funktionen zum Sehen (erweiterte Bildsuche und -erkennung), Hören, Sprechen und Suchen sowie für die Entscheidungsfindung hinzuzufügen.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: Cognitive Services, kognitive Intelligenz, kognitive Lösungen, KI-Dienste, kognitives Verständnis, kognitive Features
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d134532e7f285f391b6791a9fc73d5e3251ddac8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369408"
---
# <a name="what-are-azure-cognitive-services"></a>Was ist Azure Cognitive Services?

Azure Cognitive Services sind cloudbasierte Dienste mit REST-APIs und Clientbibliothek-SDKs, die Sie dabei unterstützen, Ihre Anwendungen mit kognitiver Intelligenz auszustatten. Für das Hinzufügen kognitiver Features zu Ihren Anwendungen sind keine KI- oder Data Science-Qualifikationen erforderlich. Azure Cognitive Services umfassen verschiedene KI-Dienste zur Erstellung kognitiver Lösungen, die sehen, hören, sprechen, verstehen und sogar Entscheidungen treffen können.

## <a name="categories-of-cognitive-services"></a>Kategorien von Cognitive Services

Der Katalog kognitiver Dienste mit kognitivem Verständnis ist in fünf Hauptkategorien unterteilt:

* Bildanalyse
* Spracheingabe
* Sprache
* Entscheidung
* Suchen,

In den folgenden Abschnitten dieses Artikels werden die zugehörigen Dienste dieser fünf Kategorien aufgelistet.

## <a name="vision-apis"></a>Bildanalyse-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Maschinelles Sehen](./computer-vision/index.yml "Maschinelles Sehen")|Über den Dienst für maschinelles Sehen haben Sie Zugriff auf erweiterte kognitive Algorithmen für die Bildverarbeitung und die Rückgabe von Informationen.|
|[Custom Vision Service](./custom-vision-service/overview.md "Custom Vision Service")|Mit dem Custom Vision Service können Sie benutzerdefinierte Bildklassifizierungen erstellen.|
|[Gesichtserkennung](./face/index.yml "Gesicht")| Der Gesichtserkennungsdienst ermöglicht den Zugriff auf erweiterte Algorithmen zur Gesichtserkennung, wodurch die Ermittlung von Gesichtsmerkmalen sowie die Gesichtserkennung ermöglicht wird.|
|[Formularerkennung](./form-recognizer/index.yml "Formularerkennung")|Die Formularerkennung identifiziert und extrahiert Schlüssel-Wert-Paare und Tabellendaten aus Formulardokumenten und gibt dann strukturierte Daten aus, die auch die Beziehungen in der ursprünglichen Datei umfassen.|
|[Freihanderkennung](./ink-recognizer/index.yml "Freihanderkennung") (wird eingestellt)|Die Freihanderkennung ermöglicht das Erkennen und Analysieren von Daten, Formen und handschriftlichen Inhalten aus Freihandeingaben sowie die Ausgabe einer Dokumentstruktur mit allen erkannten Entitäten.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer ermöglicht es Ihnen, Erkenntnisse aus Ihrem Video zu extrahieren.|

## <a name="speech-apis"></a>Spracherkennungs-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Speech-Dienst](./speech-service/index.yml "Speech-Dienst")|Der Spracherkennungsdienst erweitert Anwendungen um sprachaktivierte Features. Der Speech-Dienst umfasst verschiedene Funktionen wie Spracherkennung, Sprachsynthese, Sprachübersetzung und vieles mehr.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Sprache-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Language Understanding (LUIS)](./luis/index.yml "Language Understanding")|Mit dem Dienst „Language Understanding“ (LUIS) kann Ihre Anwendung es verstehen, wenn eine Person Wünsche in ihrer eigenen Sprache äußert.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker ermöglicht es Ihnen, aus Ihren teilstrukturierten Inhalten einen Frage- und Antwortdienst zu erstellen.|
|[Textanalyse](./text-analytics/index.yml "Textanalyse")| Die Textanalyse bietet die Verarbeitung von natürlicher Sprache für unformatierten Text für die Standpunktanalyse, die Schlüsselbegriffserkennung und die Sprachenerkennung.|
|[Translator](./translator/index.yml "Übersetzer")|Translator ermöglicht eine maschinenbasierte Textübersetzung in Quasi-Echtzeit.|
| [Plastischer Reader](./immersive-reader/index.yml "Plastischer Reader") | Plastischer Reader erweitert Ihre Anwendungen um Sprachausgabe sowie um Funktionen zum besseren Verständnis. |

## <a name="decision-apis"></a>Entscheidungs-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Anomalieerkennung](./anomaly-detector/index.yml "Anomalieerkennung") |Die Anomalieerkennung bietet Ihnen die Möglichkeit, Abweichungen in Ihren Zeitreihendaten zu überwachen und zu erkennen.|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Der Content Moderator bietet die Überwachung auf möglicherweise anstößige, unerwünschte und risikobehaftete Inhalte.|
|[Metrics Advisor](./metrics-advisor/index.yml) (Vorschauversion) | Metrics Advisor bietet eine anpassbare Anomalieerkennung für multivariate Zeitreihendaten sowie ein umfassendes Webportal, das Sie bei der Verwendung des Diensts unterstützt.|
|[Personalisierung](./personalizer/index.yml "Personalisierung")|Mit der Personalisierung können Sie die beste Benutzeroberfläche für Ihre Benutzer auswählen und dabei in Echtzeit von deren Verhalten lernen.|

## <a name="search-apis"></a>Such-APIs

> [!NOTE]
> Suchen Sie [Azure Cognitive Search](../search/index.yml)? Cognitive Services wird zwar für einige Aufgaben verwendet, doch handelt es sich dabei um eine andere Suchtechnologie, die andere Szenarien unterstützt.

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Bing-News-Suche](/azure/cognitive-services/bing-news-search/ "Bing News-Suche")|Die Bing-News-Suche gibt eine Liste von Nachrichtenartikeln zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Bing-Videosuche](/azure/cognitive-services/Bing-Video-Search/ "Bing-Videosuche")|Die Bing-Videosuche gibt eine Liste von Videos zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Bing-Websuche](./bing-web-search/index.yml "Bing-Websuche")|Die Bing-Websuche gibt eine Liste von Suchergebnissen zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Bing-Vorschlagssuche](/azure/cognitive-services/Bing-Autosuggest "Bing-Vorschlagssuche")|Mit der Bing-Vorschlagssuche können Sie einen Teil eines Suchbegriffs an Bing senden und erhalten dann eine Liste mit vorgeschlagenen Abfragen.|
|[Benutzerdefinierte Bing-Suche](/azure/cognitive-services/bing-custom-search "Benutzerdefinierte Bing-Suche")|Die benutzerdefinierte Bing-Suche ermöglicht das Erstellen einer maßgeschneiderten Suchbenutzeroberfläche für Themen, die Sie interessieren.|
|[Bing-Entitätssuche](/azure/cognitive-services/bing-entities-search/ "Bing-Entitätssuche")|Die Bing-Entitätssuche gibt Informationen zu Entitäten zurück, die nach Einschätzung von Bing für eine Abfrage des Benutzers relevant sind.|
|[Bing-Bildersuche](/azure/cognitive-services/bing-image-search "Bing-Bildersuche")|Die Bing-Bildersuche gibt eine Liste von Bildern zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Visuelle Bing-Suche](/azure/cognitive-services/bing-visual-search "Visuelle Bing-Suche")|Die visuelle Bing-Suche gibt Informationen zu einem Bild zurück, z. B. visuell ähnliche Bilder, Einkaufsquellen für abgebildete Elemente und verwandte Suchvorgänge.|
|[Bing-Suche für ortsansässige Unternehmen](/azure/cognitive-services/bing-local-business-search/ "Bing-Suche für ortsansässige Unternehmen")| Mit der Bing-API für die Suche nach ortsansässigen Unternehmen können Ihre Anwendungen basierend auf Suchabfragen Kontakt- und Standortdaten von Unternehmen in der näheren Umgebung suchen.|
|[Bing-Rechtschreibprüfung](/azure/cognitive-services/bing-spell-check/ "Bing-Rechtschreibprüfung")|Die Bing-Rechtschreibprüfung ermöglicht es Ihnen, kontextbezogene Grammatik- und Rechtschreibprüfungen durchzuführen.|

## <a name="development-options"></a>Entwicklungsoptionen 

Mit Azure und Cognitive Services stehen Ihnen verschiedene Entwicklungsoptionen zur Verfügung. Hierzu zählen unter anderem folgende:

* Automatisierungs- und Integrationstools wie Logic Apps und Power Automate
* Bereitstellungsoptionen wie Azure Functions und App Service 
* Cognitive Services-Docker-Container für sicheren Zugriff
* Tools wie Apache Spark, Azure Databricks, Azure Synapse Analytics und Azure Kubernetes Service für Big Data-Szenarien 

Weitere Informationen finden Sie unter [Entwicklungsoptionen für Cognitive Services](./cognitive-services-development-options.md).

## <a name="learn-with-the-quickstarts"></a>Lernen mit den Schnellstartanleitungen

Machen Sie sich anhand von praktischen Schnellstartanleitungen mit der Erstellung von Cognitive Services-Ressourcen vertraut. Folgende Methoden stehen zur Verfügung:

* [Azure portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure-Portal")
* [Azure-Befehlszeilenschnittstelle](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK-Clientbibliotheken](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Azure Resource Manager-Vorlagen](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager-Vorlagen")

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Sicheres Verwenden von Cognitive Services

Azure Cognitive Services bietet ein mehrschichtiges Sicherheitsmodell einschließlich [Authentifizierung](authentication.md "authentication") über Azure Active Directory-Anmeldeinformationen, eines gültigen Ressourcenschlüssels und [Azure Virtual Networks](cognitive-services-virtual-networks.md "Virtuelle Azure-Netzwerke").

## <a name="containers-for-cognitive-services"></a>Container für Cognitive Services

 Cognitive Services stellt Container für die Bereitstellung in der Azure-Cloud oder lokal bereit. Erfahren Sie mehr über [Containerunterstützung in Azure Cognitive Services](cognitive-services-container-support.md "Cognitive Services-Container").

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Die APIs in Cognitive Services werden in einem wachsenden Netzwerk mit von Microsoft verwalteten Rechenzentren gehostet. Informationen zur regionalen Verfügbarkeit der einzelnen APIs finden Sie in der [Liste mit den Azure-Regionen](https://azure.microsoft.com/regions "Liste der Azure-Regionen").

Suchen Sie nach einer Region, die noch nicht unterstützt wird? Teilen Sie uns dies mit, indem Sie im [UserVoice-Forum](https://cognitive.uservoice.com/ "UserVoice-Forum") eine Featureanfrage erstellen.

## <a name="supported-cultural-languages"></a>Unterstützte Kultursprachen

Cognitive Services unterstützt eine Vielzahl von Kultursprachen auf Dienstebene. Sie finden die Sprachenverfügbarkeit für jede API in der [Liste unterstützter Sprachen](language-support.md "Liste der unterstützten Sprachen").

## <a name="certifications-and-compliance"></a>Zertifizierungen und Compliance

Cognitive Services hat Zertifizierungen wie die CSA STAR-Zertifizierung, FedRAMP Moderate und HIPAA BAA erhalten. Sie können Zertifizierungen für eigene Überwachungen und Sicherheitsüberprüfungen [herunterladen](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "Download verfügbar ist").

Informationen zum Datenschutz und zur Datenverwaltung finden Sie im [Trust Center](https://servicetrust.microsoft.com/ "Trust Center").

## <a name="support"></a>Support

Für Cognitive Services stehen verschiedene Supportoptionen zur Verfügung, die Sie bei der Entwicklung intelligenter Anwendungen unterstützen. Bei spezifischen Fragen kann Ihnen außerdem die starke Entwicklercommunity von Cognitive Services behilflich sein. Eine vollständige Liste der verfügbaren Optionen finden Sie unter [Support- und Hilfeoptionen für Azure Cognitive Services](cognitive-services-support-options.md "Support- und Hilfeoptionen für Azure Cognitive Services").

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Cognitive Services-Kontos](cognitive-services-apis-create-account.md "Erstellen eines Cognitive Services-Kontos")
* [Neues in der Cognitive Services-Dokumentation](whats-new-docs.md "Neues in der Cognitive Services-Dokumentation")