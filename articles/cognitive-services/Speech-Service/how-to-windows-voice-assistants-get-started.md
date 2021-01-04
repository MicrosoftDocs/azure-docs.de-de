---
title: 'Sprachassistenten unter Windows: Erste Schritte'
titleSuffix: Azure Cognitive Services
description: Die Schritte zum Einstieg in die Entwicklung eines Windows-Sprach-Agents, einschließlich einer Referenz zur Beispielcode-Schnellstartanleitung.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 77d756bc0c7bbdfa38bb1262638ef85b051fc234
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026487"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Erste Schritte mit Sprachassistenten unter Windows

Diese Anleitung führt Sie durch die ersten Schritte beim Entwickeln eines Sprachassistenten unter Windows.

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Um mit der Entwicklung eines Sprachassistenten für Windows zu beginnen, müssen Sie sicherstellen, dass Sie über die richtige Entwicklungsumgebung verfügen.

- **Visual Studio:** Sie müssen [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition oder höher, installieren.
- **Windows-Version**: Ein PC mit einem Windows-Insider-Fast-Ring-Build von Windows und der Windows-Insider-Version des Windows SDK.  Dieser Beispielcode wurde unter Windows Insider Release-Build 19025.vb_release_analog.191112-1600 mit dem Windows SDK 19018 als funktionierend geprüft.  Jeder Build oder jedes SDK mit höheren als den angegebenen Versionsnummern sollte kompatibel sein.
- **UWP-Entwicklungstools**: Die Workload Entwicklung für die universelle Windows-Plattform in Visual Studio.  Informationen zum Vorbereiten Ihres Computers für das Entwickeln von UWP-Anwendungen finden Sie auf der UWP-Seite [Einrichtung starten](/windows/uwp/get-started/get-set-up).
- **Ein funktionierendes Mikrofon und eine funktionierende Audioausgabe**

## <a name="obtain-resources-from-microsoft"></a>Abrufen von Ressourcen von Microsoft

Einige Ressourcen, die für einen vollständig angepassten Sprach-Agent unter Windows erforderlich sind, müssen von Microsoft bezogen werden. Das [UWP Voice Assistant-Beispiel](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) enthält Beispielversionen dieser Ressourcen für den Einstieg in Entwicklung und Testen, dieser Abschnitt muss also für den Einstieg in die Entwicklung nicht beachtet werden.

- **Schlüsselwortmodell:** Für die Sprachaktivierung ist ein Schlüsselwortmodell von Microsoft in Form einer BIN-Datei erforderlich. Die im UWP Voice Assistant-Beispiel zur Verfügung stehende BIN-Datei ist mit dem Schlüsselwort „Contoso“ trainiert.
- **Token für Features mit eingeschränkten Zugriff:** Da die APIs des ConversationalAgent Zugriff auf Audiosignale von einem Mikrofon bereitstellen, sind sie durch Einschränkungen für Features für eingeschränkten Zugriff geschützt.  Um ein Feature mit eingeschränktem Zugriff zu verwenden, müssen Sie bei Microsoft ein Token für Features mit eingeschränktem Zugriff erwerben, das mit der Paket-ID Ihrer Anwendung verbunden ist.

## <a name="establish-a-dialog-service"></a>Einrichten eines Dialogdiensts

Für eine vollständige Sprachassistenten-Erfahrung benötigt die Anwendung einen Dialogdienst mit diesen Fähigkeiten:

- Erkennen eines Schlüsselworts in einer bestimmten Audiodatei
- Lauschen nach Benutzereingaben und deren Konvertierung in Text
- Bereitstellen des Texts für einen Bot
- Übersetzen der Textantwort des Bots in eine Audioausgabe

Dies sind die Anforderungen zum Erstellen eines einfachen Dialogdiensts mithilfe von Direct Line Speech.

- **Speech Services-Abonnement:** Ein Abonnement von Cognitive Speech Services für Spracherkennung und Sprachsynthese. Testen Sie Speech Services kostenlos [hier](./overview.md#try-the-speech-service-for-free).
- **Bot Framework-Bot:**  Ein Bot, der mit der Bot Framework-Version 4.2 oder höher erstellt wurde und mit einem [Direct Line Speech](./direct-line-speech.md)-Abonnement versehen ist, um die Sprachein- und -ausgabe zu ermöglichen. [Dieser Leitfaden](./tutorial-voice-enable-your-bot-speech-sdk.md) enthält schrittweise Anleitungen zum Erstellen eines „Echobots“ und dessen Abonnements von Direct Line Speech. Alternativ können Sie die Schritte [hier](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) ausführen, um einen angepassten Bot zu erstellen, und dann die gleichen Schritten [hier](./tutorial-voice-enable-your-bot-speech-sdk.md) befolgen, um Direct Line Speech für ihn zu abonnieren, jedoch mit Ihrem eigenen neuen Bot anstelle des „Echobots“.

## <a name="try-out-the-sample-app"></a>Ausprobieren der Beispiel-App

Mit Ihrem Speech Services-Abonnementschlüssel und der Bot-ID des Echobots sind Sie bereit, das [UWP Voice Assistant-Beispiel](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) auszuprobieren. Befolgen Sie die Anweisungen in der Infodatei zum Ausführen der App und zum Eingeben Ihrer Anmeldeinformationen.

## <a name="create-your-own-voice-assistant-for-windows"></a>Erstellen eines eigenen Sprachassistenten für Windows

Sobald Sie Ihr Token für Features mit eingeschränktem Zugriff und die BIN-Datei von Microsoft erhalten haben, können Sie mit der Arbeit an Ihrem eigenen Sprachassistenten unter Windows beginnen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lesen Sie den Implementierungsleitfaden für Sprachassistenten](windows-voice-assistants-implementation-guide.md)