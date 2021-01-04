---
title: Planen Ihrer Azure Communication Services-Lösung für Telefonie und SMS
titleSuffix: An Azure Communication Services concept document
description: Es wird beschrieben, wie Sie die Verwendung von Telefonnummern und der Telefoniefunktionen effektiv planen.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 271d7ce38d50fd21975327eb1f21528aeacc485e
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937235"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Planen Ihrer Telefonie- und SMS-Lösung

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Mit Azure Communication Services können Sie Telefonnummern verwenden, um Sprachanrufe zu tätigen und SMS-Nachrichten über das Telefonfestnetz (Public Switched Telephone Network, PSTN) zu versenden. In diesem Dokument werden die Telefonnummerntypen, Konfigurationsoptionen und die regionale Verfügbarkeit für die Planung Ihrer Telefonie- und SMS-Lösung mithilfe von Communication Services überprüft.





## <a name="phone-number-types-in-azure-communication-services"></a>Telefonnummerntypen in Azure Communication Services
 
Communication Services bietet zwei Arten von Telefonnummern an: **lokale** und **gebührenfreie**. 

### <a name="local-numbers"></a>Lokale Nummern
Lokale (geografische) Nummern sind 10-stellige Telefonnummern, die aus den Ortsvorwahlen in den USA bestehen. Beispiel: `+1 (206) XXX-XXXX` ist eine lokale Nummer mit einer Ortsvorwahl von `206`. Diese Vorwahl ist der Stadt Seattle zugeordnet. Diese Telefonnummern werden im Allgemeinen von Einzelpersonen und lokalen Unternehmen verwendet. Azure Communication Services bietet lokale Nummern in den USA an. Über diese Nummern können Sie telefonieren, aber keine SMS-Nachrichten versenden. 

### <a name="toll-free-numbers"></a>Gebührenfreie Nummern
Gebührenfreie Nummern sind 10-stellige Telefonnummern mit unterschiedlichen Vorwahlen, die von jeder Telefonnummer aus gebührenfrei angerufen werden können. Beispiel: `+1 (800) XXX-XXXX` ist eine gebührenfreie Nummer in der Region Nordamerika. Diese Telefonnummern werden im Allgemeinen für Kundendienstzwecke verwendet. Azure Communication Services bietet gebührenfreie Nummern in den USA an. Über diese Nummern können Sie telefonieren und SMS-Nachrichten senden. Gebührenfreie Nummern können nicht von Personen verwendet, sondern nur Anwendungen zugewiesen werden.

#### <a name="choosing-a-phone-number-type"></a>Auswählen eines Telefonnummerntyps

Wenn Ihre Telefonnummer von einer Anwendung verwendet wird (z. B. um Anrufe zu tätigen oder Nachrichten im Auftrag Ihres Diensts zu senden), können Sie eine gebührenfreie oder lokale (geografische) Nummer auswählen. Sie können eine gebührenfreie Nummer auswählen, wenn Ihre Anwendung SMS-Nachrichten versendet und/oder Anrufe tätigt.

Wenn Ihre Telefonnummer von einer Person (z. B. einem Benutzer Ihrer anrufenden Anwendung) verwendet wird, muss die lokale (geografische) Telefonnummer verwendet werden. 

In der folgenden Tabelle sind diese Telefonnummerntypen zusammengefasst: 

| Telefonnummerntyp | Beispiel                              | Länderverfügbarkeit    | Telefonnummernfähigkeit |Gängiger Anwendungsfall                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Lokal (geografisch)        | +1 (Ortsvorwahl) XXX XX XX  | US                      | Anrufe (ausgehend) | Zuweisen von Telefonnummern zu Benutzern in Ihren Anwendungen  |
| Gebührenfrei         | +1 (gebührenfreie *Ortsvorwahl*) XXX XX XX | US                      | Anrufe (ausgehend), SMS (eingehend/ausgehend)| Zuweisen von Telefonnummern zu IVR-Systemen (Interactive Voice Response, Interaktive Sprachantwort) oder Bots, SMS-Anwendungen                                        |


## <a name="phone-number-features-in-azure-communication-services"></a>Telefonnummernfeatures in Azure Communication Services 

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

Für die meisten Telefonnummern können die Features beliebig zusammengestellt werden. Diese Features können ausgewählt werden, wenn Sie Ihre Telefonnummern in Azure Communication Services leasen.

Welche Features Ihnen zur Verfügung stehen, hängt von dem Land/der Region ab, in dem/der Sie tätig sind, von Ihrem Anwendungsfall und von der Art der Telefonnummer, die Sie gewählt haben. Diese Features variieren aufgrund der rechtlichen Anforderungen je nach Land/Region. Azure Communication Services bietet die folgenden Telefonnummernfeatures:

- **Unidirektionale ausgehende SMS:** Diese Option ermöglicht es Ihnen, SMS-Nachrichten an Ihre Benutzer zu senden. Sie ist nützlich für die Benachrichtigung und Szenarien mit zweistufiger Authentifizierung. 
- **Bidirektionale ein- und ausgehende SMS:** Diese Option ermöglicht es Ihnen, Nachrichten von Ihren Benutzern mithilfe von Telefonnummern zu senden und zu empfangen. Diese Option ist in Kundendienstszenarien hilfreich.
- **Unidirektionale ausgehende Telefonanrufe:** Diese Option ermöglicht es Ihnen, Ihre Benutzer anzurufen und die Anrufer-ID für ausgehende Anrufe, die von Ihrem Dienst getätigt werden, zu konfigurieren. Diese Option ist in Kundendienst- und Sprachbenachrichtigungsszenarien hilfreich.

## <a name="countryregion-availability"></a>Verfügbarkeit in Land/Region

In der folgenden Tabelle wird gezeigt, wo Sie verschiedene Telefonnummerntypen sowie die mit diesen Telefonnummerntypen verbundenen ein- und ausgehenden Anruf- und SMS-Features abrufen können.

|Nummerntyp| Abrufen von Nummern in | Tätigen von Anrufen nach                                        | Empfangen von Anrufen aus                                    |Senden von Nachrichten nach       | Empfangen von Nachrichten aus |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Lokal (geografisch)  | US                 | USA, Kanada, Vereinigtes Königreich, Deutschland, Frankreich, +weitere*| USA, Kanada, Vereinigtes Königreich, Deutschland, Frankreich, +weitere* |Nicht verfügbar| Nicht verfügbar |
| Gebührenfrei | US                 | US                                                   | US                                                    |US                | US |

*Weitere Details zu Anrufzielen und Preisen finden Sie auf der [Seite mit der Preisübersicht](../pricing.md).

## <a name="azure-subscriptions-eligibility"></a>Berechtigung für Azure-Abonnements

Für den Erhalt einer Telefonnummer müssen Sie ein kostenpflichtiges Azure-Abonnement abgeschlossen haben. Telefonnummern können mit Testkonten oder kostenlosem Azure-Guthaben nicht erworben werden. 

Derzeit ist die Verfügbarkeit von Telefonnummern auf Azure-Abonnements beschränkt, die über eine Rechnungsadresse in den USA verfügen.

## <a name="next-steps"></a>Nächste Schritte

### <a name="quickstarts"></a>Schnellstarts

- [Beschaffen einer Telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Tätigen eines Anrufs](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Senden einer SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Dokumentation

- [Sprach- und Videokonzepte](../voice-video-calling/about-call-types.md)
- [Anrufabläufe](../call-flows.md)
- [Preise](../pricing.md)
