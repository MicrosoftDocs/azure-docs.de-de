---
title: Häufig gestellte Fragen zu Sprachassistenten
titleSuffix: Azure Cognitive Services
description: Hier erhalten Sie Antworten auf die häufigsten Fragen zu Sprachassistenten, die benutzerdefinierte Befehle (Vorschauversion) oder den Kanal „Direct Line Speech“ verwenden.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: b43d446930d1f8c40c12e5d0aeba6eb202ef47ef
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353730"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Häufig gestellte Fragen zu Sprachassistenten

Wenn Sie in diesem Dokument keine Antworten auf Ihre Fragen finden, sehen Sie sich die [weiteren Supportoptionen](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) an.

## <a name="general"></a>Allgemein

**F: Was ist ein Sprachassistent?**

**A:** Ebenso wie Cortana ist ein Sprachassistent eine Lösung, die auf die gesprochenen Äußerungen eines Benutzers lauscht, den Inhalt dieser Äußerungen auf eine Bedeutung hin analysiert, eine oder mehrere Aktionen als Reaktion auf die Absicht der Äußerung ausführt und dann dem Benutzer eine Antwort präsentiert, die häufig eine gesprochene Komponente enthält. Es handelt sich um eine „Sprachein-/Sprachausgabe“-Funktion zur Interaktion mit einem System. Autoren von Sprachassistenten erstellen mithilfe des `DialogServiceConnector` im Sprach-SDK eine Anwendung auf dem Gerät, um mit einem Assistenten zu kommunizieren, der mithilfe [benutzerdefinierter Befehle (Vorschau)](custom-commands.md) oder mit dem Kanal [Direct Line Speech](direct-line-speech.md) des Bot Framework erstellt wurde. Diese Assistenten können benutzerdefinierte Schlüsselwörter, Custom Speech und Custom Voice verwenden, um eine auf Ihre Marke oder Ihr Produkt zugeschnittene Erfahrung zu bieten.

**F: Sollte ich benutzerdefinierte Befehle (Vorschau) oder „Direct Line Speech“ verwenden? Wo liegt der Unterschied?**

**A:** [Benutzerdefinierte Befehle (Vorschau)](custom-commands.md) ist eine Reihe von Tools mit geringer Komplexität, mit denen Sie problemlos einen Assistenten erstellen und hosten können, der für Aufgabenabschluss-Szenarien gut geeignet ist. [Direct Line Speech](direct-line-speech.md) bietet umfangreichere, anspruchsvollere Funktionen, die stabile Konversationsszenarien ermöglichen. Weitere Informationen finden Sie im [Vergleich der Assistenten-Lösungen](voice-assistants.md#choosing-an-assistant-solution).

**F: Wie fange ich an?**

**A:** Die beste Möglichkeit, um mit dem Erstellen einer Anwendung für benutzerdefinierte Befehle (Vorschau) oder eines grundlegenden Bot Framework-Bots zu beginnen.

- [Erstellen einer Anwendung für benutzerdefinierte Befehle (Vorschau)](./quickstart-custom-commands-application.md)
- [Tutorial: Erstellen und Bereitstellen eines Basisbots](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Herstellen einer Verbindung mit einem Direct Line Speech-Kanal für einen Bot](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Debuggen

**F: Wo befindet sich mein Kanalgeheimnis?**

**A:** Wenn Sie die Vorschauversion von Direct Line Speech verwendet haben oder die zugehörige Dokumentation lesen, erwarten Sie unter Umständen, dass auf der Direct Line Speech-Seite für die Kanalregistrierung ein geheimer Schlüssel vorhanden ist. Für v1.7 der `DialogServiceConfig`-Factorymethode `FromBotSecret` im Speech SDK wird dieser Wert ebenfalls erwartet.

In der neuesten Version von Direct Line Speech wurde der Prozess für die Kontaktaufnahme mit Ihrem Bot von einem Gerät aus vereinfacht. Auf der Seite für die Kanalregistrierung wird Ihre Direct Line Speech-Kanalregistrierung oben über die Dropdownliste einer Sprachressource zugeordnet. Nach der Zuordnung enthält Speech SDK v1.8 die `BotFrameworkConfig::FromSubscription`-Factorymethode, über die ein `DialogServiceConnector` für die Kontaktaufnahme mit dem Bot, den Sie Ihrem Abonnement zugeordnet haben, konfiguriert wird.

Falls Sie noch mit der Migration Ihrer Clientanwendung von v1.7 zu v1.8 beschäftigt sind, funktioniert `DialogServiceConfig::FromBotSecret` ggf. mit einem Wert vom Typ „nicht leer, nicht Null“ als Kanalgeheimnisparameter, z. B. Ihrem zuvor verwendeten Geheimnis. Dieser Wert wird einfach ignoriert, wenn ein Sprachabonnement verwendet wird, das einer neueren Kanalregistrierung zugeordnet ist. Hierbei ist es _obligatorisch_, dass der Wert nicht Null und nicht leer ist. Dies wird auf dem Gerät überprüft, bevor die dienstseitige Zuordnung relevant wird.

Eine ausführlichere Anleitung finden Sie im [Tutorialabschnitt](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel), in dem die Kanalregistrierung Schritt für Schritt beschrieben wird.

**F: Ich erhalte bei der Verbindungsherstellung eine 401-Fehlermeldung, und nichts funktioniert. Ich weiß, dass mein Speech-Abonnementschlüssel gültig. Was geht da vor?**

**A:** Wenn Sie Ihr Abonnement im Azure-Portal verwalten, stellen Sie sicher, dass Sie die Ressource **Speech** (Microsoft.CognitiveServicesSpeechServices, „Speech“) und _nicht_ die Ressource **Cognitive Services** (Microsoft.CognitiveServicesAllInOne, „Alle Cognitive Services“) verwenden. Überprüfen Sie außerdem die [Spracherkennungsdienst-Unterstützung für Sprachassistenten in der Region](regions.md#voice-assistants).

![Korrektes Abonnement für „Direct Line Speech“](media/voice-assistants/faq-supported-subscription.png "Beispiel für ein kompatibles Sprachabonnement")

**F: Mein `DialogServiceConnector` gibt erkannten Text zurück, es wird aber ein Fehler vom Typ 1011 angezeigt, und mein Bot reagiert nicht. Warum?**

**A:** Dieser Fehler weist auf ein Kommunikationsproblem zwischen dem Assistenten und dem Sprachassistentendienst hin.

- Stellen Sie für benutzerdefinierte Befehle (Vorschau) sicher, dass die Anwendung für benutzerdefinierte Befehle (Vorschau) veröffentlicht wurde.
- Stellen Sie für „Direct Line Speech“ sicher, dass Sie eine [Verbindung Ihres Bots mit dem Direct Line Speech-Kanal hergestellt](/azure/bot-service/bot-service-channel-connect-directlinespeech) und Ihrem Bot [Unterstützung für das Streaming-Protokoll hinzugefügt](/azure/bot-service/directline-speech-bot) haben (mit der entsprechenden Websocketunterstützung). Überprüfen Sie dann, ob Ihr Bot auf eingehende Anforderungen vom Kanal reagiert.

**F: Dieser Code funktioniert trotzdem nicht, und/oder ich erhalte bei der Verwendung eines `DialogServiceConnector` eine andere Fehlermeldung. Wie soll ich vorgehen?**

**A:** Die dateibasierte Protokollierung bietet deutlich mehr Details und kann Supportanfragen beschleunigen. Informationen zum Aktivieren dieser Funktion finden Sie unter [Verwenden der Dateiprotokollierung](how-to-use-logging.md).

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung](troubleshooting.md)
- [Versionshinweise](releasenotes.md)