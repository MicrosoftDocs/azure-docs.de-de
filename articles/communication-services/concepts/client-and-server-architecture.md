---
title: Client- und Serverarchitektur
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr zur Architektur von Communication Services.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 8b63ce25d6839e8672027240099859dc7aad96c5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886072"
---
# <a name="client-and-server-architecture"></a>Client- und Serverarchitektur

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Jede Azure Communication Services-Anwendung weist **Clientanwendungen** auf, die **Dienste** nutzen, um die Kommunikation zwischen Personen zu erleichtern. Auf dieser Seite werden allgemeine Elemente der Architektur in einer Vielzahl von Szenarien dargestellt.

## <a name="user-access-management"></a>Verwaltung des Benutzerzugriffs

Azure Communication Services-Clientbibliotheken erfordern `user access tokens`, um sicher auf Communication Services-Ressourcen zuzugreifen. `User access tokens` sollten von einem vertrauenswürdigen Dienst generiert und verwaltet werden, und zwar aufgrund der vertraulichen Natur des Tokens und der für seine Generierung erforderlichen Verbindungszeichenfolge. Wenn Zugriffstoken nicht ordnungsgemäß verwaltet werden, kann dies zu zusätzlichen Kosten aufgrund des falschen Einsatzes von Ressourcen führen. Es wird dringend empfohlen, für die Benutzerverwaltung auf einen vertrauenswürdigen Dienst zurückzugreifen. Der vertrauenswürdige Dienst generiert die Token und gibt sie unter Verwendung der entsprechenden Verschlüsselung an den Client zurück. Ein Beispiel eines Flows durch die Architektur finden Sie nachstehend:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagramm der Architektur von Benutzerzugriffstoken":::

Weitere Informationen finden Sie unter [Bewährte Methoden für die Identitätsverwaltung](../../security/fundamentals/identity-management-best-practices.md).

## <a name="browser-communication"></a>Browserkommunikation

JavaScript-Clientbibliotheken von Azure Communication Services können Webanwendungen mit Rich-Text-, Sprach- und Videointeraktion ermöglichen. Die Anwendung interagiert direkt mit Azure Communication Services über die Clientbibliothek, um auf die Datenebene zuzugreifen und Text-, Sprach- und Videokommunikation in Echtzeit zu bieten. Ein Beispiel eines Flows durch die Architektur finden Sie nachstehend:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagramm der Browser-zu-Browser-Architektur für Communication Services":::

## <a name="native-app-communication"></a>Kommunikation über native Apps

Viele Szenarien eignen sich am besten für native Anwendungen. Azure Communication Services unterstützt sowohl Browser-zu-App- als auch App-zu-App-Kommunikation.  Beim Aufbau einer nativen Anwendungserfahrung werden Benutzer durch Pushbenachrichtigungen in die Lage versetzt, Anrufe entgegenzunehmen, auch wenn die Anwendung nicht läuft. Azure Communication Services ermöglicht dies auf einfache Weise mit integrierten Pushbenachrichtigungen an Google Firebase, Apple Push Notification Service und den Windows-Pushbenachrichtigungsdienst. Ein Beispiel eines Flows durch die Architektur finden Sie nachstehend:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagramm der Architektur von Communication Services für die Kommunikation über native Apps":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Sprach- und SMS-Kommunikation über das Telefonfestnetz

Die Kommunikation über das Telefonfestnetz kann die Reichweite Ihrer Anwendung erheblich vergrößern. Um Szenarien für Sprachkommunikation im Festnetz und mit SMS zu unterstützen, hilft Ihnen Azure Communication Services direkt im Azure-Portal oder mithilfe von REST-APIs und Clientbibliotheken beim [Bezug von Telefonnummern](../quickstarts/telephony-sms/get-phone-number.md). Sobald Telefonnummern bezogen wurden, können sie verwendet werden, um Kunden sowohl über das Festnetz als auch per SMS in ein- und ausgehenden Szenarien zu erreichen. Ein Beispiel eines Flows durch die Architektur finden Sie nachstehend:

> [!Note]
> Während der Public Preview-Phase steht die Bereitstellung von US-Telefonnummern für Kunden mit Rechnungsanschrift innerhalb der USA und Kanadas zur Verfügung. 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagramm der Festnetzarchitektur von Communication Services":::

Weitere Informationen zu Festnetz- und SMS-Lösungen finden Sie unter [Planen Ihrer Festnetz- und SMS-Lösung](../concepts/telephony-sms/plan-solution.md).

## <a name="humans-communicating-with-bots-and-other-services"></a>Kommunikation zwischen Menschen und Bots und anderen Diensten

Azure Communication Services unterstützt die Mensch-zu-System-Kommunikation über Text- und Sprachkanäle mit Diensten, die direkt auf die Datenebene von Azure Communication Services zugreifen. So können Sie beispielsweise einen Bot eingehende Telefonanrufe beantworten oder an einem Webchat teilnehmen lassen. Azure Communication Services bietet Clientbibliotheken, die diese Anruf- und Chatszenarien ermöglichen. Ein Beispiel eines Flows durch die Architektur finden Sie nachstehend:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagramm der Botarchitektur von Communication Services":::

## <a name="networking"></a>Netzwerk

Möglicherweise möchten Sie bestimmte Daten zwischen Benutzern austauschen, z. B. zur Synchronisierung einer gemeinsamen Mixed-Reality- oder Gaming-Erfahrung. Die Echtzeit-Datenebene, die für Text-, Sprach- und Videokommunikation verwendet wird, steht Ihnen auf zwei Arten direkt zur Verfügung:

- **Clientbibliothek „Calling“** : Geräte in einem Anruf haben Zugriff auf APIs zum Senden und Empfangen von Daten über den Anrufkanal. Dies ist die einfachste Möglichkeit zum Hinzufügen von Datenkommunikation zu einer vorhandenen Interaktion.
- **STUN/TURN**: Azure Communication Services stellt Ihnen standardkonforme STUN- und TURN-Dienste zur Verfügung. Dies ermöglicht Ihnen, eine umfassend angepasste Transportebene auf diesen standardisierten Primitiven aufzubauen. Sie können Ihren eigenen standardkonformen Client erstellen oder Open-Source-Bibliotheken wie [WinRTC](https://github.com/microsoft/winrtc) nutzen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen von Benutzerzugriffstoken](../quickstarts/access-tokens.md)

Weitere Informationen finden Sie in den folgenden Artikeln:

- Informationen zur [Authentifizierung](../concepts/authentication.md)
- Informationen zu [Festnetz- und SMS-Lösungen](../concepts/telephony-sms/plan-solution.md)

- [Hinzufügen von Chatfunktionen zu Ihrer App](../quickstarts/chat/get-started.md)
- [Hinzufügen von Sprachanrufen zu Ihrer App](../quickstarts/voice-video-calling/getting-started-with-calling.md)