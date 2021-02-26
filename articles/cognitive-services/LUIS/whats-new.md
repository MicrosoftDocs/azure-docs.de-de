---
title: Neuigkeiten – Language Understanding (LUIS)
description: Dieser Artikel wird regelmäßig mit Neuigkeiten über die Language Understanding-API von Azure Cognitive Services aktualisiert.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: f4ac384a9b1bce944397369cb93392f179643f96
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558756"
---
# <a name="whats-new-in-language-understanding"></a>Neuerungen in Language Understanding

Informieren Sie sich über die Neuerungen im Dienst. Dabei kann es sich um Versionshinweise, Videos, Blogbeiträge und andere Informationen handeln. Legen Sie ein Lesezeichen für diese Seite an, um über den Dienst auf dem Laufenden zu bleiben.

## <a name="release-notes"></a>Versionshinweise

### <a name="january-2021"></a>Januar 2021

* Von der V3-Vorhersage-API wird jetzt die [Bing-Rechtschreibprüfungs-API](luis-tutorial-bing-spellcheck.md) unterstützt.
* Der regionalen Portale („au.luis.ai“ und „eu.luis.ai“) wurden zu einem einzelnen Portal mit einer einzelnen URL zusammengefasst. Wenn Sie eines dieser Portale genutzt haben, werden Sie automatisch zu luis.ai weitergeleitet.

### <a name="december-2020"></a>Dezember 2020

* Alle LUIS-Benutzer müssen die [Migration zu einer LUIS-Erstellungsressource](luis-migration-authoring.md) durchführen.
* Neue [Auswertungsendpunkte](luis-how-to-batch-test.md#batch-testing-using-the-rest-api), die die Übermittlung von Batchtests mithilfe der REST-API und das Abrufen von Genauigkeitsergebnissen für Ihre Absichten und Entitäten ermöglichen. Verfügbar ab LUIS-Endpunkt „v3.0-preview“

### <a name="june-2020"></a>Juni 2020

* [Vorschau 3.0 Erstellungs](luis-migration-authoring-entities.md)-SDK –
    * Version 3.2.0-preview.3 – [.NET – NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Version 4.0.0-preview.3 – [JS – NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Anwenden von DevOps-Methoden mit LUIS
    * Konzepte
        * [DevOps-Methoden für LUIS](luis-concept-devops-sourcecontrol.md)
        * [Continuous Integration- und Continuous Delivery-Workflows für LUIS DevOps](luis-concept-devops-automation.md)
        * [Testen für LUIS DevOps](luis-concept-devops-testing.md)
    * Vorgehensweise
        * [Anwenden von DevOps auf die LUIS-App-Entwicklung mithilfe von GitHub Actions](luis-how-to-devops-with-github.md)
    * [GitHub-Repository mit vollständigem Code](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Mai 2020: //Build

* Als **allgemein verfügbar** (generally available, GA) veröffentlicht:
    * [Language Understanding-Container](luis-container-howto.md)
    * Vorschauportal zum [aktuellen Portal](https://www.luis.ai) heraufgestuft, [vorheriges](https://previous.luis.ai) Portal aber weiterhin verfügbar
    * Neue Oberfläche für Entitätserstellung und Bezeichnung für Machine Learning
    * [Prozess zum Upgraden](migrate-from-composite-entity.md) von zusammengesetzten und einfachen Entitäten auf Machine Learning-Entitäten
    * [Einstellungsunterstützung](how-to-application-settings-portal.md) für die Normalisierung von Wortvarianten
* Änderungen an der Erstellungs-API (Vorschau)
    * App-Schema 7.x für geschachtelte Machine Learning-Entitäten
    * [Migration zu erforderlichem Feature](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Neue Ressourcen für Entwickler
    * [Tools für Continuous Integration](developer-reference-resource.md#continuous-integration-tools)
    * Workshop: Erlernen von bewährten Methoden für [_Verstehen natürlicher Sprache_ (Natural Language Understanding, NLU) mit LUIS](developer-reference-resource.md#workshops)
* [Vom Kunden verwaltete Schlüssel](luis-encryption-of-data-at-rest.md): Verschlüsseln aller Daten, die Sie in LUIS verwenden, mithilfe Ihres eigenen Schlüssels
* [KI-Show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (Video): die neuen Features in LUIS in Aktion



### <a name="march-2020"></a>März 2020

* TLS 1.2 wird nun für alle HTTP-Anforderungen erzwungen, die an diesen Dienst gerichtet werden. Weitere Informationen finden Sie unter [Sicherheit von Azure Cognitive Services](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4\. November 2019 – Ignite

* Video: [Erweiterte NLU-Modelle (Natural Language Understanding, Verstehen natürlicher Sprache) mit LUIS und Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Verbesserte Produktivität von Entwicklern
    * Allgemeine Verfügbarkeit unseres [Vorhersageendpunkts V3](luis-migration-api-v3.md).
    * Möglichkeit zum Importieren und Exportieren von Apps mit dem Format `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Dies ebnet den Weg für einen effektiven CI/CD-Prozess.
* Spracherweiterung
    * [Arabisch und Hindi](luis-language-support.md) in der öffentlichen Vorschau.
* Vordefinierte Modelle
    * [Vordefinierte Domänen](luis-reference-prebuilt-domains.md) sind jetzt allgemein verfügbar (GA)
    * Japanische [vordefinierte Entitäten](luis-reference-prebuilt-entities.md#japanese-entity-support) Alter, Währung, Anzahl und Prozentsatz: in V3 nicht unterstützt.
    * Italienische [vordefinierte Entitäten](luis-reference-prebuilt-entities.md#italian-entity-support) Alter, Währung, Dimension, Anzahl und Prozentsatz: die Auflösung wurde gegenüber V2 geändert.
* Verbesserte Benutzeroberfläche in [preview.luis.ai portal](https://preview.luis.ai): neu gestaltete Bezeichungsoberfläche, um das Erstellen und Debuggen komplexer Modelle zu ermöglichen. Testen Sie die Tutorials im Vorschauportal:
    * [Nur Absichten](tutorial-intents-only.md)
    * [Zerlegbare Machine Learning-Entität](tutorial-machine-learned-entity.md)
* Erweiterte Funktionen zum Sprachverständnis: [Erstellen ausgereifter Sprachmodelle](luis-concept-entity-types.md) mit weniger Aufwand.
* Definieren Sie Machine Learning-Funktionen auf Modellebene, und ermöglichen Sie die Verwendung von Modellen als Signale für andere Modelle, beispielsweise durch das Verwenden von Entitäten als Features für Absichten und weitere Entitäten.
* Neue, erweiterte [Grenzwerte](luis-limits.md): höheres Maximum für Ausdruckslisten und ganze Ausdrücke, neues Modell als Featuregrenzwert
* Extrahieren von Informationen aus Text im Format einer tiefen Hierarchiestruktur, wodurch Dialoganwendungen noch leistungsfähiger werden.

    ![Abbildung einer Machine Learning-Entität](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3\. September 2019

* Azure-Erstellungsressource: [jetzt migrieren](luis-migration-authoring.md).
    * 500 Apps pro Azure-Ressource
    * 100 Versionen pro App
* Unterstützung für vordefinierte Entitäten in Türkisch
* Unterstützung für datetimeV2 in Italienisch

### <a name="july-23-2019"></a>23. Juli 2019

* Aktualisierung von [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) auf 1.2.3
    * Alters-, Temperatur-, Dimensions-und Währungserkennungen auf Italienisch.
    * Verbesserung der Feiertagserkennung auf Englisch zur richtigen Berechnung von Datumsangaben, die auf Thanksgiving basieren.
    * Verbesserungen bei DateTime auf Französisch, um falsche positive Ergebnisse von anderen Entitäten als Datum und Uhrzeit zu verringern.
    * Unterstützung für Kalender-/Schul-/Geschäftsjahr und Akronyme bei DateRange auf Englisch.
    * Verbesserte PhoneNumber-Erkennung auf Chinesisch und Japanisch.
    * Verbesserte Unterstützung für NumberRange auf Englisch.
    * Leistungsverbesserungen.

### <a name="june-24-2019"></a>24. Juni 2019

* [Vordefinierte OrdinalV2-Entität](luis-reference-prebuilt-ordinal-v2.md) zur Unterstützung der Sortierung, z. B. „Nächste“, „Vorherige“ und „Letzte“. Gilt nur für die englische Kultur.

### <a name="may-6-2019---build-conference"></a>6\. Mai 2019 – //Build-Konferenz

Die folgenden Features wurden bei der Build 2019-Konferenz veröffentlicht:

* [Migrationsanleitung für die Vorschauversion der V3-API](luis-migration-api-v3.md)
* [Verbessertes Analytics-Dashboard](luis-how-to-use-dashboard.md)
* [Verbesserte vordefinierte Domänen](luis-reference-prebuilt-domains.md)
* [Entitäten vom Typ „dynamische Liste“](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe Entitäten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Videos

### <a name="2019-ignite-videos"></a>2019 Ignite-Videos

[Erweiterte NLU-Modelle (Natural Language Understanding, Verstehen natürlicher Sprache) mit LUIS und Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>Build 2019 – Videos

[How to use Azure Conversational AI to scale your business for the next generation (Bereiten Sie Ihr Unternehmen mit Azure Conversational AI auf die nächste Generation vor)](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Dienstupdates

[Azure-Updateankündigungen für Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
