---
title: Sprachassistenten – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Ein Überblick über die Features, Funktionen und Einschränkungen für Sprachassistenten, die das Speech Software Development Kit (SDK) verwenden.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 09859be61029242e987603e01b5c3fba8dffb2ca
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483885"
---
# <a name="what-is-a-voice-assistant"></a>Was ist ein Sprachassistent?

Sprachassistenten, die den Speech-Dienst verwenden, ermöglichen es Entwicklern, natürliche Konversationsschnittstellen für ihre Anwendungen und Umgebungen zu erstellen, die der menschlichen Sprache nahekommen.

Der Sprachassistentendienst bietet eine schnelle, zuverlässige Interaktion zwischen einem Gerät und einer Assistentenimplementierung, die entweder (1) [Direct Line Speech](direct-line-speech.md) (über Azure Bot Service), um Ihren Bots Sprachfunktionen hinzuzufügen, oder (2) benutzerdefinierte Befehle für Szenarien mit Sprachsteuerung.

## <a name="choosing-an-assistant-solution"></a>Auswählen einer Assistenten-Lösung

Im ersten Schritt zur Erstellung eines Sprach-Assistenten wird festgelegt, welches Ziel erreicht werden soll. Der Speech-Dienst verfügt über mehrere Lösungen für die Entwicklung Ihrer Assistenten-Interaktionen, die sich gegenseitig ergänzen. Mit dem [Direct Line Speech](direct-line-speech.md)-Kanal können Sie Ihrem flexiblen und vielseitigen Bot, der mit Azure Bot Service erstellt wurde, Voice-In- und Voice-Out-Funktionen hinzufügen oder die Einfachheit der Erstellung einer App für [benutzerdefinierte Befehle](custom-commands.md) für einfache Szenarien mit Sprachsteuerung nutzen.

| Zielsetzung | Optionen | Beispiel |
|-------------------|------------------|----------------|
|Konversation mit offenem Ende mit Integration von stabilen Fähigkeiten und vollständiger Bereitstellungssteuerung | Azure Bot Service-Bot mit [Direct Line Speech](direct-line-speech.md)-Kanal | <ul><li>„Ich möchte nach Stuttgart fahren“</li><li>„Welche Art von Pizza kann ich bestellen?“</li></ul>
|Sprachgesteuerte oder einfache aufgabenorientierte Unterhaltungen mit vereinfachter Erstellung und vereinfachtem Hosting | [Benutzerdefinierte Befehle](custom-commands.md) | <ul><li>„Deckenlampe einschalten“</li><li>„Temperatur um fünf Grad erhöhen“</li><li>Weitere Beispiele sind [hier verfügbar](https://speech.microsoft.com/customcommands).</li></ul>

Wir empfehlen Ihnen die Verwendung von [Direct Line Speech](direct-line-speech.md) als Standardauswahl, falls Sie noch nicht genau wissen, wie Ihr Assistent eingesetzt werden soll. Hiermit erhalten Sie eine Integration mit umfassenden Tools und Erstellungshilfen, z. B. die [Lösung „Virtueller Assistent“ und die Unternehmensvorlage](/azure/bot-service/bot-builder-enterprise-template-overview) sowie den [QnA Maker-Dienst](../qnamaker/overview/overview.md), um häufig verwendete Muster zu nutzen und zu erweitern und Ihre vorhandenen Wissensquellen zu verwenden.

Mit [benutzerdefinierten Befehlen](custom-commands.md) ist es einfach, funktionsreiche Apps mit Sprachsteuerung zu erstellen, die für Voice-First-Interaktionserfahrungen optimiert sind. Sie bieten eine einheitliche Benutzeroberfläche für die Erstellung, ein automatisches Hostingmodell und eine relativ geringe Komplexität, sodass Sie sich auf die Entwicklung der besten Lösung für Ihre Szenarien mit Sprachsteuerung konzentrieren können.

   ![Vergleich von Assistentenlösungen](media/voice-assistants/assistant-solution-comparison.png "Vergleich von Assistentenlösungen")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Referenzarchitektur zum Erstellen eines Sprachassistenten mit dem Speech SDK

   ![Konzeptionelle Darstellung des Orchestrierungsdienstflows für den Sprachassistenten](media/voice-assistants/overview.png "Flow für Sprachassistenten")

## <a name="core-features"></a>Wichtige Funktionen

Sowohl bei Nutzung von [Direct Line Speech](direct-line-speech.md) als auch von [Benutzerdefinierte Befehle](custom-commands.md) zum Erstellen Ihrer Assistenten-Interaktionen können Sie auf umfassende Anpassungsfunktionen zurückgreifen, mit denen Sie Ihren Assistenten an Ihre Wünsche in Bezug auf Marke, Produkt und Persönlichkeit anpassen können.

| Category | Features |
|----------|----------|
|[Benutzerdefiniertes Schlüsselwort](./custom-keyword-basics.md) | Benutzer können Konversationen mit Assistenten über ein benutzerdefiniertes Schlüsselwort wie z. B. „Hallo, Contoso“ starten. Eine App verwendet hierzu eine benutzerdefinierte Schlüsselwort-Engine im Speech SDK, um ein benutzerdefiniertes Schlüsselwort zu konfigurieren, [das Sie hier generieren können](./custom-keyword-basics.md). Sprachassistenten können eine dienstseitige Schlüsselwortüberprüfung nutzen, um die Genauigkeit der Schlüsselwortaktivierung zu verbessern (statt diesen Vorgang nur über das Gerät durchzuführen).
|[Spracherkennung](speech-to-text.md) | Sprachassistenten wandeln mithilfe der [Spracherkennung](speech-to-text.md) des Speech-Diensts Audiodaten in Echtzeit in erkannten Text um. Dieser Text ist während der Transkription sowohl für Ihre Assistentenimplementierung als auch für Ihre Clientanwendung verfügbar.
|[Sprachsynthese](text-to-speech.md) | Textantworten von Ihrem Assistenten werden mithilfe der [Sprachsynthese](text-to-speech.md) des Speech-Diensts synthetisiert. Diese Synthese wird dann Ihrer Clientanwendung als Audiostream zur Verfügung gestellt. Microsoft bietet die Möglichkeit, eine benutzerdefinierte, hochwertige neuronale TTS-Stimme zu erstellen, die Ihrer Marke eine Stimme verleiht. [Kontaktieren Sie uns](mailto:mstts@microsoft.com), wenn Sie mehr darüber erfahren möchten.

## <a name="getting-started-with-voice-assistants"></a>Erste Schritte mit Sprachassistenten

Wir bieten Schnellstarts an, die so konzipiert sind, dass Sie in weniger als 10 Minuten Code ausführen können. Diese Tabelle enthält eine Liste mit Schnellstarts für Sprachassistenten, die nach Sprache sortiert sind.

* [Schnellstart: Erstellen eines benutzerdefinierten Sprachassistenten mit Direct Line Speech](quickstarts/voice-assistants.md)
* [Schnellstart: Erstellen einer sprachgesteuerten App mit benutzerdefinierten Befehlen](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Beispielcode und Tutorials

Beispielcode für die Erstellung eines Sprachassistenten ist auf GitHub verfügbar. Diese Beispiele decken die Clientanwendung für die Verbindung mit Ihrem Assistenten in mehreren gängigen Programmiersprachen ab.

* [Beispiele für Sprachassistenten auf GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Tutorial: Aktivieren der Sprachsteuerung für Ihren Assistenten, der mit Azure Bot Service mit dem C# Speech SDK erstellt wurde](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Tutorial: Erstellen einer Anwendung für benutzerdefinierte Befehle mit einfachen Sprachbefehlen](how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="customization"></a>Anpassung

Sprachassistenten, die mit Azure Speech-Diensten erstellt wurden, können die gesamte Palette von Anpassungsoptionen nutzen.

* [Custom Speech Service](./custom-speech-overview.md)
* [Custom Voice](how-to-custom-voice.md)
* [Benutzerdefiniertes Schlüsselwort](custom-keyword-overview.md)

> [!NOTE]
> Die Anpassungsoptionen unterscheiden sich je nach Sprache/Gebietsschema. Weitere Informationen erhalten Sie unter [Unterstützte Sprachen](language-support.md).

## <a name="next-steps"></a>Nächste Schritte

* [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
* [Weitere Informationen zu „Benutzerdefinierte Befehle“](custom-commands.md)
* [Informationen zu Direct Line Speech](direct-line-speech.md)
* [Abrufen des Speech SDK](speech-sdk.md)