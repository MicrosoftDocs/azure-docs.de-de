---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177892"
---
Dieses Beispiel beinhaltet die Verwendung des [Twilio](https://www.twilio.com/)-Diensts zum Senden von SMS-Nachrichten an ein Mobiltelefon. Azure Functions bietet über die [Twilio-Bindung](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio) bereits Unterstützung für Twilio an. Dieses Feature wird in dem Beispiel verwendet.

Zunächst benötigen Sie ein Twilio-Konto. Unter https://www.twilio.com/try-twilio können Sie kostenlos eines erstellen. Fügen Sie nach der Einrichtung Ihres Kontos die folgenden drei **App-Einstellungen** zu Ihrer Funktions-App hinzu.

| Name der App-Einstellung | Wertbeschreibung |
| - | - |
| **TwilioAccountSid**  | Die SID für Ihr Twilio-Konto |
| **TwilioAuthToken**   | Das Authentifizierungstoken für Ihr Twilio-Konto |
| **TwilioPhoneNumber** | Die Ihrem Twilio-Konto zugeordnete Telefonnummer. Diese wird für das Senden von SMS-Nachrichten verwendet. |