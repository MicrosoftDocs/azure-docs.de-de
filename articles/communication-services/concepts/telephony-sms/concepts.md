---
title: SMS-Konzepte in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie Informationen zu SMS-Konzepten in Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6f03195c801f241a79a6bf68ef9d398370b0d4c6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391812"
---
# <a name="sms-concepts"></a>SMS-Konzepte

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services ermöglicht das Senden und Empfangen von SMS-Textnachrichten unter Verwendung der SMS-Client Bibliotheken von Communication Services. Diese Clientbibliotheken können zur Unterstützung von Kundendienstszenarien, Terminerinnerungen, zweistufiger Authentifizierung und anderer Echtzeitkommunikationsanforderungen verwendet werden. Das SMS-Feature von Communication Services ermöglicht das zuverlässige Senden von Nachrichten sowie die Nutzung von Informationen zur Zustellbarkeit und zur Antwortrate rund um Ihre Kampagnen.

Zu den wichtigsten Features der SMS-Clientbibliotheken von Azure Communication Services gehören folgende:

-  **Unkomplizierte Einrichtungsumgebung** zum Hinzufügen von SMS-Funktionen zu Ihren Anwendungen
- **Unterstützung von Hochgeschwindigkeitsnachrichten** über gebührenfreie Nummern für A2P-Anwendungsfälle (Application to Person) in den USA
- **Bidirektionale Konversationen** zur Unterstützung von Szenarien wie Kundensupport, Warnungen und Terminerinnerungen
- **Zuverlässige Zustellung** mit Zustellberichten in Echtzeit für Nachrichten, die von Ihrer Anwendung gesendet wurden
- **Analysen** zur Nachverfolgung von Verwendungsmustern und Kundeninteraktion
- **Unterstützung von Abmeldungen**, um Abmeldungen für gebührenfreie Nummern automatisch zu erkennen und zu berücksichtigen. Abmeldungen für gebührenfreie Nummern in den USA werden von US-Netzbetreibern vorgeschrieben und durchgesetzt.
  - STOP – Wenn ein SMS-Empfänger sich abmelden möchte, kann er „STOP“ an die gebührenfreie Nummer senden. Der Netzbetreiber sendet die folgende Standardantwort für „STOP“: *„NETZWERKNACHRICHT: You replied with the word „stop“ which blocks all texts sent from this number. Text back „unstop“ to receive messages again.“ (Sie haben mit dem Wort „stop“ geantwortet, das alle von dieser Nummer gesendeten Texte blockiert. Antworten Sie mit „unstop“, um wieder Nachrichten zu erhalten.)*
  - START/UNSTOP – Wenn der Empfänger Textnachrichten von einer gebührenfreien Nummer erneut abonnieren möchte, kann er „START“ oder „UNSTOP“ an die gebührenfreie Nummer senden. Der Netzbetreiber sendet die folgende Standardantwort für „START/UNSTOP“: *„NETZWERKNACHRICHT: You have replied „unstop“ and will begin receiving messages again from this number.“ (Sie haben mit „unstop“ geantwortet und werden wieder Nachrichten von dieser Nummer erhalten.)*
  - Azure Communication Services erkennt die STOP-Nachricht und blockiert alle weiteren Nachrichten an den Empfänger. Im Zustellbericht wird eine fehlerhafte Zustellung mit der Statusmeldung „Sender blocked for given recipient“ (Absender für bestimmten Empfänger gesperrt) angezeigt.
  - Die STOP-, UNSTOP- und START-Nachrichten werden an Sie weitergeleitet. Von Azure Communication Services wird die Überwachung und Implementierung dieser Abmeldungen empfohlen, um sicherzustellen, dass nicht weiter versucht wird, Nachrichten an Empfänger zu senden, die sich von Ihrer Kommunikation abgemeldet haben.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Senden einer SMS-Nachricht](../../quickstarts/telephony-sms/send.md)

Die Artikel zu den folgenden Themen könnten Sie auch interessieren:

- Informationen zur [Clientbibliothek für SMS](../telephony-sms/sdk-features.md)
- Beziehen einer für SMS geeigneten [Telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummerntypen in Azure Communication Services](../telephony-sms/plan-solution.md)
