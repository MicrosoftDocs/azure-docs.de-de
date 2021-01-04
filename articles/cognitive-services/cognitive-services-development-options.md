---
title: Entwicklungsoptionen für Azure Cognitive Services
description: Erfahren Sie, wie Sie Azure Cognitive Services mit verschiedenen Entwicklungs- und Bereitstellungsoptionen wie etwa Clientbibliotheken, REST-APIs, Logik-Apps, Power Automate, Azure Functions, Azure App Service, Azure Databricks und vielen weiteren verwenden.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: a952bfa45515b9d35549d03d18fd94103679344d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349468"
---
# <a name="cognitive-services-development-options"></a>Entwicklungsoptionen für Cognitive Services

Dieses Dokument gibt einen allgemeinen Überblick der Entwicklungs- und Bereitstellungsoptionen und unterstützt Sie so bei den ersten Schritten in Azure Cognitive Services.

Azure-Cognitive Services sind cloudbasierte KI-Dienste, die Entwicklern die Integration von Intelligenz in ihre Anwendungen und Produkte auch ohne fundierte Kenntnisse von Machine Learning ermöglichen. Mit Cognitive Services haben Sie Zugriff auf KI-Funktionen oder -Modelle, die von Microsoft erstellt, trainiert und aktualisiert werden – bereit für den Einsatz in Ihren Anwendungen. In vielen Fällen haben Sie außerdem die Möglichkeit, die Modelle für Ihre geschäftlichen Anforderungen anzupassen. 

Cognitive Services sind in vier Kategorien unterteilt: Entscheidungsfindung, Sprachverständnis, gesprochene Sprache und maschinelles Sehen. Normalerweise erfolgt der Zugriff auf diese Dienste über REST-APIs, Clientbibliotheken und benutzerdefinierte Tools (wie Befehlszeilenschnittstellen), die von Microsoft stammen. Dies ist jedoch nur ein Weg zum Erfolg. Mithilfe von Azure haben Sie außerdem Zugriff auf verschiedene Entwicklungsoptionen, darunter:

* Automatisierungs- und Integrationstools wie Logic Apps und Power Automate
* Bereitstellungsoptionen wie Azure Functions und App Service 
* Cognitive Services-Docker-Container für sicheren Zugriff
* Tools wie Apache Spark, Azure Databricks, Azure Synapse Analytics und Azure Kubernetes Service für Big Data-Szenarien 

Bevor wir fortfahren, ist es wichtig zu wissen, dass die Cognitive Services hauptsächlich für zwei verschiedene Aufgaben verwendet werden. Je nach der Aufgabe, die Sie ausführen möchten, stehen verschiedene Entwicklungs- und Bereitstellungsoptionen zur Wahl. 

* [Entwicklungsoptionen für Vorhersage und Analyse](#development-options-for-prediction-and-analysis)
* [Tools zum Anpassen und Konfigurieren von Modellen](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Entwicklungsoptionen für Vorhersage und Analyse 

Die Tools, die Sie zum Anpassen und Konfigurieren von Modellen verwenden, unterscheiden sich von denen, die Sie zum Aufrufen der Cognitive Services verwenden. Vorkonfiguriert und ohne weitere Anpassung ermöglichen es Ihnen die meisten Cognitive Services, Daten zu senden und Erkenntnisse zu empfangen. Zum Beispiel: 

* Sie können ein Bild an den Dienst für maschinelles Sehen senden, um Wörter und Sätze zu erkennen oder die Anzahl der Personen im Bild zu erfassen
* Sie können eine Audiodatei an den Speech-Dienst senden und Transkriptionen sowie zugleich eine Übertragung der Sprache in Text abrufen
* Sie können eine PDF-Datei an den Formularerkennungsdienst senden, um Tabellen, Zellen und Text innerhalb dieser Zellen zu ermitteln, und eine JSON-Ausgabe mit Koordinaten und Details abzurufen

Azure bietet eine große Bandbreite an Tools, die für unterschiedliche Benutzertypen entwickelt wurden, und viele von ihnen lassen sich zusammen mit Cognitive Services verwenden. Vom Designer gesteuerte Tools lassen sich besonders einfach verwenden und sind schnell einzurichten und zu automatisieren, viele bieten aber nur eingeschränkte Möglichkeiten zur Anpassung. Unsere REST-APIs und Clientbibliotheken bieten Benutzern mehr Kontrolle und Flexibilität, das Erstellen einer Lösung erfordert aber mehr Anstrengung, Zeit und Fachwissen. Wenn Sie REST-APIs und Clientbibliotheken verwenden, wird von der Annahme ausgegangen, dass Sie mit dem Arbeiten in modernen Programmiersprachen wie C#, Java, Python, JavaScript, oder einer anderen beliebten Programmiersprache vertraut sind. 

Sehen wir uns einmal die verschiedenen Möglichkeiten des Arbeitens mit Cognitive Services an.

### <a name="client-libraries-and-rest-apis"></a>Clientbibliotheken und REST-APIs

Mit Cognitive Services-Clientbibliotheken und REST-APIs erhalten Sie direkten Zugriff auf Ihren Dienst. Diese Tools bieten den programmgesteuerten Zugriff auf Cognitive Services und die ihnen zugrunde liegenden Modelle, und ermöglichen es Ihnen oftmals, Ihre Modelle und Lösungen programmgesteuert anzupassen. 

* **Zielbenutzer**: Entwickler und Datenanalysten
* **Vorteile:** Bieten größtmögliche Flexibilität zum Aufrufen der Dienste aus jeder Sprache und Umgebung. 
* **Benutzeroberfläche**: nicht zutreffend – nur Code
* **Abonnement(s)** : Azure-Konto + Cognitive Services-Ressourcen

Wenn Sie mehr über die verfügbaren Clientbibliotheken und REST APIs erfahren möchten, verwenden Sie unsere [Übersicht zu Cognitive Services](index.yml), um einen Dienst auszuwählen, und steigen Sie mit einem unserer Schnellstarts zu maschinellem Sehen, Entscheidungsfindung, Sprachverständnis und gesprochener Sprache praktisch ein.

### <a name="cognitive-services-for-big-data"></a>Cognitive Services für Big Data

Cognitive Services für Big Data ermöglicht die direkte Einbettung intelligenter, kontinuierlich optimierter Modelle in Apache Spark&trade; und SQL-Berechnungen. Dank dieser Tools müssen sich Entwickler nicht mehr um sämtliche Netzwerkdetails kümmern und können sich stattdessen auf die Erstellung intelligenter, verteilter Anwendungen konzentrieren. Cognitive Services für Big Data unterstützt die folgenden Plattformen und Connectors: Azure Databricks, Azure Synapse, Azure Kubernetes Service und Datenconnectors.

* **Zielbenutzer**: Datenanalysten und Datentechniker
* **Vorteile:** Azure Cognitive Services für Big Data ermöglicht es Benutzern, mithilfe von Apache Spark&trade; mehrere Terabyte an Daten durch Cognitive Services zu leiten. Es ist ganz einfach, umfangreiche intelligente Anwendungen mit einem beliebigen Datenspeicher zu erstellen.
* **Benutzeroberfläche**: nicht zutreffend – nur Code
* **Abonnement(s)** : Azure-Konto + Cognitive Services-Ressourcen

Wenn Sie mehr zu Big Data für Cognitive Services erfahren möchten, ist die [Übersicht](./big-data/cognitive-services-for-big-data.md) ein guter Ausgangspunkt. Wenn Sie bereit sind, in das Erstellen von Builds einzusteigen, testen Sie unsere [Python](./big-data/samples-python.md)- oder [Scala](./big-data/samples-scala.md)-Beispiele.

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions und Azure Service Web-Aufträge

Sowohl [Azure Functions-](../azure-functions/index.yml) als auch [Azure App Service-Webaufträge](../app-service/index.yml) bieten Integrationsdienste mit vorgelagerter Codeerstellung, die für Entwickler entworfen wurden und auf [Azure App Services](../app-service/index.yml) aufbauen. Diese Produkte stellen eine serverlose Infrastruktur zum Schreiben von Code zur Verfügung. Innerhalb dieses Codes können Si Aufrufe an Ihre Dienste mithilfe unserer Clientbibliotheken und REST-APIs vornehmen. 

* **Zielbenutzer**: Entwickler und Datenanalysten
* **Vorteile:** Serverloser Computedienst, der die Ausführung von ereignisgesteuertem Code ermöglicht. 
* **Benutzeroberfläche**: Ja
* **Abonnement(s)** : Azure-Konto + Cognitive Services-Ressource + Azure Functions-Abonnement

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) teilt mit Power Automate den Workflow-Designer und die Connectors, bietet aber erweiterte Kontrolle, einschließlich Integrationen in Visual Studio und DevOps. Power Automate macht die Integration mit Ihren Cognitive Services-Ressourcen dank dienstspezifischer Connectors einfach, die einen Proxy oder Wrapper für die APIs bereitstellen. Es handelt sich um die gleichen Connectors, die in Power Automate zur Verfügung stehen. 

* **Zielbenutzer**: Entwickler, Integratoren, IT-Experten, DevOps
* **Vorteile:** Designer-First (deklaratives) Entwicklungsmodell, das erweiterte Optionen und Integration in einer Lösung mit geringem Codeanteil bietet
* **Benutzeroberfläche**: Ja
* **Abonnement(s)** : Azure-Konto + Cognitive Services-Ressourcen + Logic Apps-Bereitstellung

### <a name="power-automate"></a>Power Automate 

Power Automate ist ein Dienst der [Power Platform](/power-platform/), der Sie beim Erstellen automatisierter Workflows zwischen Apps und Services unterstützt, ohne dass Sie Code schreiben müssten. Wir bieten verschiedene Connectors, um Ihnen die Interaktion mit Ihrer Cognitive Services-Ressource in einer Power Automate-Lösung zu erleichtern. Power Automate basiert auf Logic Apps. 

* **Zielbenutzer**: Geschäftsbenutzer (Analysten) und Sharepoint-Administratoren
* **Vorteile:** Automatisieren Sie sich wiederholende manuelle Aufgaben, indem Sie einfach Mausklicks, Tastendrücke und Kopieren-/Einfügen-Schritte auf Ihrem Desktop aufzeichnen!
* **Tools mit Benutzeroberfläche**: Ja – nur Benutzeroberfläche
* **Abonnement(s)** : Azure-Konto + Cognitive Services-Ressource + Power Automate-Abonnement + Office 365-Abonnement

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview) ist eine Microsoft Power Platform-Funktion, die Sie verwenden können, um die Unternehmensleistung zu steigern, indem Sie Prozesse automatisieren und Ergebnisse vorhersagen. Dank AI Builder können Ihre Lösungen die Leistungsfähigkeit von KI mithilfe einer Point-and-Klick-Oberfläche nutzen. Viele Cognitive Services, wie die Formularerkennung, die Textanalyse und maschinelles Sehen, wurden hier direkt integriert, und Sie brauchen keine eigenen Cognitive Services zu erschaffen. 

* **Zielbenutzer**: Geschäftsbenutzer (Analysten) und Sharepoint-Administratoren
* **Vorteile:** Eine schlüsselfertige Lösung, die die Leistungsfähigkeit von KI über eine Point-and-Klick-Benutzeroberfläche verfügbar macht. Es sind keine Fähigkeiten zur Codeerstellung oder Datenanalyse erforderlich.
* **Tools mit Benutzeroberfläche**: Ja – nur Benutzeroberfläche
* **Abonnement(s)** : AI Builder

### <a name="continuous-integration-and-deployment"></a>Continuous Integration und Continuous Deployment

Sie können Azure DevOps- und GitHub-Aktionen verwenden, um Ihre Bereitstellungen zu verwalten. Im [Erläuterungsabschnitt unten](#continuous-integration-and-delivery-with-devops-and-github-actions) gibt es zwei Beispiele für CI/CD-Integrationen zum Trainieren und Bereitstellen von benutzerdefinierten Modellen für den Speech- und den Language Understanding-Dienst (LUIS). 

* **Zielbenutzer**: Entwickler, Datenanalysten und Datentechniker
* **Vorteile:** Ermöglicht Ihnen das programmgesteuerte Anpassen, Aktualisieren und Bereitstellen von Anwendungen und Modellen. Die regelmäßige Nutzung Ihrer Daten zum Verbessern und Aktualisieren Ihrer Modelle für Sprachverständnis, maschinelles Sehen, Sprachausgabe und Entscheidungsfindung bietet beträchtliche Vorteile. 
* **Tools mit Benutzeroberfläche**: nicht zutreffend – nur Code 
* **Abonnement(s)** : Azure-Konto + Cognitive Services-Ressource + GitHub-Konto

## <a name="tools-to-customize-and-configure-models"></a>Tools zum Anpassen und Konfigurieren von Modellen

Wenn Sie auf Ihrem Weg zum Erstellen einer Anwendung oder eines Workflows mit den Cognitive Services Fortschritte machen, stellen Sie möglicherweise fest, dass Sie das Modell anpassen müssen, um die gewünschte Leistung zu erzielen. Viele unserer Dienste ermöglichen es Ihnen, auf den vorgefertigten Modellen aufzubauen, um Ihre speziellen geschäftlichen Anforderungen zu erfüllen. Für alle unsere anpassbaren Dienste bieten wir sowohl eine Erfahrung mit Benutzeroberfläche, die Sie durch den Prozess führt, als auch APIs für das codegestützte Training. Zum Beispiel:

* Sie möchten ein Custom Speech-Modell für das richtige Erkennen von medizinischen Fachbegriffen mit einer Wort-Fehler-Rate (WER) unterhalb von 3 % trainieren
* Sie möchten einen Bildklassifizierer mit Custom Vision erstellen, der den Unterschied zwischen Koniferen und laubabwerfenden Bäumen erkennen kann
* Sie möchten eine benutzerdefinierte neuronale Stimme mit Ihren persönlichen Stimmdaten für ein besseres automatisiertes Kundenerlebnis erstellen

Die Tools, die Sie zum Trainieren und Konfigurieren von Modellen verwenden, unterscheiden sich von denen, die Sie zum Aufrufen der Cognitive Services verwenden. In vielen Fällen stellen Cognitive Services, die Anpassung unterstützen, Portale und Tools mit Benutzeroberfläche zur Verfügung, die dafür ausgelegt sind, Ihnen beim Trainieren, Bewerten und Bereitstellen von Modellen zu helfen. Sehen wir uns nun eine Reihe von Optionen an:<br><br>

| Säule | Dienst | Benutzeroberfläche für die Anpassung | Schnellstart |
|--------|---------|------------------|------------|
| Bildanalyse | Custom Vision | https://www.customvision.ai/ | [Schnellstart](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Bildanalyse | Formularerkennung | Tool für die Beschriftung von Beispielen | [Schnellstart](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Entscheidung | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Schnellstart](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Entscheidung | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [Schnellstart](./metrics-advisor/quickstarts/web-portal.md) |
| Entscheidung | Personalisierung | Eine Benutzeroberfläche steht im Azure-Portal unter Ihrer Personalisierungsressource zur Verfügung. | [Schnellstart](./personalizer/quickstart-personalizer-sdk.md) |
| Sprache | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Sprache | QnA Maker | https://www.qnamaker.ai/ | [Schnellstart](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Sprache | Textübersetzung/Benutzerdefinierter Translator | https://portal.customtranslator.azure.ai/ | [Schnellstart](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Spracheingabe/-ausgabe | Benutzerdefinierte Befehle | https://speech.microsoft.com/ | [Schnellstart](./speech-service/custom-commands.md) |
| Spracheingabe/-ausgabe | Custom Speech | https://speech.microsoft.com/ | [Schnellstart](./speech-service/custom-speech-overview.md) |
| Spracheingabe/-ausgabe | Custom Voice | https://speech.microsoft.com/ | [Schnellstart](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Continuous Integration und Continuous Delivery mit DevOps- und GitHub-Aktionen

Language Understanding und der Speech-Dienst bieten Lösungen für Continuous Integration und Continuous Deployment, die sich auf Azure DevOps- und GitHub-Aktionen stützen. Diese Tools werden für automatisiertes Training, automatisiertes Testen und automatisierte Releaseverwaltung von benutzerdefinierten Modellen verwendet. 

* [CI/CD für Custom Speech](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD für LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Lokale Container 

Viele der Cognitive Services können in Containern für den lokalen Zugriff und die lokale Verwendung bereitgestellt. Die Verwendung dieser Container gibt Ihnen die Flexibilität, Cognitive Services aus Compliance-, Sicherheits- oder anderen betrieblichen Gründen näher an Ihre Daten heranzubringen. Eine umfassende Liste der Cognitive Services-Container finden Sie unter [Lokale Container für Cognitive Services](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Nächste Schritte
<!--
* Learn more about low code development options for Cognitive Services -->
* [Erstellen einer Cognitive Services-Ressource und Einstieg in die Erstellung](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)