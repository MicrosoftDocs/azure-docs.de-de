---
title: Erste Schritte mit Teams-Interoperabilität unter Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit der Chat-Clientbibliothek von Azure Communication Services einer Teams-Besprechung beitreten.
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 59e259df5d9e564e11dbebd416569727c3857632
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932293"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Schnellstart: Beitreten mit einer Chat-App zu einer Teams-Besprechung

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Steigen Sie in die Nutzung von Azure Communication Services ein, indem Sie Ihre Chatlösung über die JavaScript-Clientbibliothek mit Microsoft Teams verbinden. 

## <a name="prerequisites"></a>Voraussetzungen 

1. Eine  [Teams-Bereitstellung](/deployoffice/teams-install). 
2. Eine funktionierende [Chat-App](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Aktivieren der Teams-Interoperabilität 

Ein Communication Services-Benutzer, der einer Teams-Besprechung als Gastbenutzer hinzugefügt wird, kann nur auf den Chat der Besprechung zugreifen, wenn er der Teams-Telefonkonferenz beitritt. Weitere Informationen zum Hinzufügen eines Communication Services-Benutzers zu einer Teams-Telefonkonferenz finden Sie in der Dokumentation zur [Teams-Interoperabilität](../voice-video-calling/get-started-teams-interop.md).

Sie müssen Mitglied der besitzenden Organisation beider Entitäten sein, um dieses Feature verwenden zu können.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- Sehen Sie sich unser [Hero-Beispiel für Chat](../../samples/chat-hero-sample.md) an.
- Erfahren Sie mehr über die [Funktionsweise des Chats](../../concepts/chat/concepts.md).