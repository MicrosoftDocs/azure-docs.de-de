---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027407"
---
Dieses Beispiel beinhaltet die Verwendung des [Twilio](https://www.twilio.com/)-Diensts zum Senden von SMS-Nachrichten an ein Mobiltelefon. Azure Functions bietet über die [Twilio-Bindung](../articles/azure-functions/functions-bindings-twilio.md) bereits Unterstützung für Twilio an. Dieses Feature wird in dem Beispiel verwendet.

Zunächst benötigen Sie ein Twilio-Konto. Unter https://www.twilio.com/try-twilio können Sie kostenlos eines erstellen. Fügen Sie nach der Einrichtung Ihres Kontos die folgenden drei **App-Einstellungen** zu Ihrer Funktions-App hinzu.

| Name der App-Einstellung | Wertbeschreibung |
| - | - |
| **TwilioAccountSid**  | Die SID für Ihr Twilio-Konto |
| **TwilioAuthToken**   | Das Authentifizierungstoken für Ihr Twilio-Konto |
| **TwilioPhoneNumber** | Die Ihrem Twilio-Konto zugeordnete Telefonnummer. Diese wird für das Senden von SMS-Nachrichten verwendet. |