---
title: 'Schnellstart: Erstellen eines benutzerdefinierten Sprachassistenten: Speech-Dienst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187571"
---
In dieser Schnellstartanleitung wird mithilfe des [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) eine Anwendung mit benutzerdefiniertem Sprachassistent erstellt, die eine Verbindung mit einem bereits erstellten und konfigurierten Bot herstellt. Einen umfassenden Leitfaden für die Boterstellung finden Sie bei Bedarf in [diesem Tutorial](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md).

Nach Erfüllung einiger weniger Voraussetzungen können Sie in wenigen Schritten eine Verbindung für Ihren benutzerdefinierten Sprachassistenten herstellen:
> [!div class="checklist"]
> * Erstellen eines Objekts vom Typ `BotFrameworkConfig` auf der Grundlage Ihres Abonnementschlüssels und Ihrer Region
> * Erstellen eines Objekts vom Typ `DialogServiceConnector` unter Verwendung des obigen Objekts `BotFrameworkConfig`
> * Starten des Lauschprozesses für eine einzelne Äußerung unter Verwendung des Objekts `DialogServiceConnector`
> * Überprüfen des zurückgegebenen Objekts `ActivityReceivedEventArgs`
