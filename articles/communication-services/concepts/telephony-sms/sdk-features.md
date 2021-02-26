---
title: Übersicht über die Clientbibliothek für SMS von Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie eine Übersicht über die Clientbibliothek für SMS und über ihre Funktionen.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2448840fcbbea686972f8f948d428bda7b3c2bf2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391744"
---
# <a name="sms-client-library-overview"></a>Übersicht über die Clientbibliothek für SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Mit den Azure Communication Services-Clientbibliotheken für SMS können Sie Ihren Anwendungen SMS-Messagingfunktionen hinzufügen.

## <a name="sms-client-library-capabilities"></a>Funktionen der Clientbibliothek für SMS

Die folgende Liste enthält die Features, die aktuell in unseren Clientbibliotheken verfügbar sind:

| Featuregruppe | Funktion                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Grundlegende Funktionen | Senden und Empfangen von SMS-Nachrichten </br> *Unicode-Emojis werden unterstützt.*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Empfangen von Zustellberichten für gesendete Nachrichten                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alle Zeichensätze (Sprach-/Unicode-Unterstützung)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Unterstützung langer Nachrichten (bis zu 2.048 Zeichen)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatische Verkettung langer Nachrichten                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Ereignisse            | Verwenden von Event Grid, um Webhooks für den Empfang von eingehenden Nachrichten und Zustellberichten zu konfigurieren | ✔️   | ✔️    | ✔️    | ✔️      |
| Rufnummer      | Gebührenfreie Nummern                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Gesetzliche Bestimmungen        | Behandlung von Abmeldungen                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Überwachung        | Überwachen der Nutzung für gesendete und empfangene Nachrichten                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-Anrufe      | Hinzufügen von PSTN-Anruffunktionen zu Ihrer für SMS geeigneten gebührenfreien Telefonnummer                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Senden einer SMS-Nachricht](../../quickstarts/telephony-sms/send.md)

Die Artikel zu den folgenden Themen könnten Sie auch interessieren:

- Informationen zu allgemeinen [SMS-Konzepten](../telephony-sms/concepts.md)
- Beziehen einer für SMS geeigneten [Telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummerntypen in Azure Communication Services](../telephony-sms/plan-solution.md)
