---
title: Benutzerdefinierte Befehle – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Eine Übersicht der Features, Funktionen und Einschränkungen für „Benutzerdefinierte Befehle“, einer Lösung zum Erstellen von Sprachanwendungen.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 40736764cf9cec6f652e0147eb25d83f15c5bee2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024941"
---
# <a name="what-is-custom-commands"></a>Was sind benutzerdefinierte Befehle?

Anwendungen, wie etwa [Sprachassistenten](voice-assistants.md), hören den Benutzern zu und ergreifen als Reaktion eine Aktion, wobei sie dabei oftmals antworten. Sie verwenden die [Spracherkennung](speech-to-text.md), um die Sprache des Benutzers zu erfassen, und ergreifen dann Maßnahmen zum in natürlicher Sprache verstandenen Text. Diese Aktion umfasst häufig die Sprachausgabe des Assistenten, die mit [Sprachsynthese](text-to-speech.md) generiert wurde. Geräte stellen eine Verbindung mit Assistenten über das `DialogServiceConnector`-Objekt des Speech SDK her.

Mit **benutzerdefinierten Befehlen** ist es einfach, funktionsreiche Apps mit Sprachsteuerung zu erstellen, die für Voice-First-Interaktionserfahrungen optimiert sind. Sie bieten eine einheitliche Benutzeroberfläche für die Erstellung, ein automatisches Hostingmodell und eine relativ geringe Komplexität, sodass Sie sich auf die Entwicklung der besten Lösung für Ihre Szenarien mit Sprachsteuerung konzentrieren können.

Benutzerdefinierte Befehle eignen sich am besten für die Erledigung von Aufgaben oder für Befehl-und-Steuerung-Szenarien (Command-and-Control), besonders gut geeignet für IoT-Geräte (Internet der Dinge), Ambient- und monitorlose Geräte. Beispiele hierfür sind Lösungen für das Hotel- und Gaststättengewerbe, den Einzelhandel und die Automobilindustrie, die es Ihnen ermöglichen, die besten sprachgesteuerten Benutzeroberflächen für Ihre Gäste in Räumen zu schaffen, den Bestand in Ihrem Geschäft zu verwalten und die Funktionalität unterwegs im Auto zu steuern.

> [!TIP]
> Sehen Sie sich unsere Beispieldemos auf unserer Landing Page unter [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) an.

Wenn Sie daran interessiert sind, komplexe Konversations-Apps zu erstellen, sollten Sie das Bot-Framework mit der [Lösung für virtuelle Assistenten](/azure/bot-service/bot-builder-enterprise-template-overview) ausprobieren. Mit Direct Line Speech können Sie jedem Bot des Bot-Frameworks eine Stimme hinzufügen.

Geeignete Kandidaten für Benutzerdefinierte Befehle verfügen über ein festes Vokabular mit klar definierten Variablensätzen. So sind z. B. Aufgaben zur Gebäudeautomatisierung, wie die Steuerung eines Thermostaten, ideal.

   ![Beispiele für Szenarien zur Aufgabenerledigung](media/voice-assistants/task-completion-examples.png "Beispiele für die Aufgabenerledigung")

## <a name="getting-started-with-custom-commands"></a>Erste Schritte mit benutzerdefinierten Befehlen

Unser Ziel mit benutzerdefinierten Befehlen ist es, Ihre kognitive Belastung zu reduzieren, um all die verschiedenen Technologien zu erlernen und sich auf die Entwicklung Ihrer sprachgesteuerten App zu konzentrieren. Erster Schritt für die Verwendung benutzerdefinierter Befehle zur <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Erstellung einer Azure Speech-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Sie können Ihre App für benutzerdefinierte Befehle in Speech Studio erstellen und veröffentlichen. Anschließend kann eine Anwendung auf dem Gerät über das Speech SDK mit ihr kommunizieren.

#### <a name="authoring-flow-for-custom-commands"></a>Erstellungsablauf für benutzerdefinierte Befehle
   ![Erstellungsablauf für benutzerdefinierte Befehle](media/voice-assistants/custom-commands-flow.png "Erstellungsablauf für benutzerdefinierte Befehle")

Befolgen Sie unsere Schnellstartanleitung, damit Ihre erste App für benutzerdefinierte Befehle in weniger als 10 Minuten entsprechenden Code ausführt.

* [Erstellen eines Sprachassistenten mithilfe von benutzerdefinierten Befehlen](quickstart-custom-commands-application.md)

Wenn Sie mit der Schnellstartanleitung fertig sind, sehen Sie sich unsere Leitfäden für ausführliche Schritte zum Entwerfen, Entwickeln, Debuggen, Bereitstellen und Integrieren einer Anwendung für benutzerdefinierte Befehle an.

## <a name="building-voice-assistants-with-custom-commands"></a>Erstellen von Sprachassistenten mit benutzerdefinierten Befehlen
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Nächste Schritte

* [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
* [Beispiele finden Sie in unserem Repository für Sprachassistenten auf GitHub](https://aka.ms/speech/cc-samples)
* [Wechseln Sie zum Speech Studio, um Benutzerdefinierte Befehle auszuprobieren](https://speech.microsoft.com/customcommands)
* [Abrufen des Speech SDK](speech-sdk.md)