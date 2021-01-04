---
title: Sprach- und Videokonzepte in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie Informationen zu Anruftypen von Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 11c20f3f79a4fceafaca9b1a948f1b4a425dad87
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572710"
---
# <a name="voice-and-video-concepts"></a>Sprach- und Videokonzepte

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Mit Azure Communication Services können Sie Sprach- und Videoanrufe (1:1 oder in der Gruppe) tätigen und empfangen. Es können sowohl andere Geräte mit Internetverbindung als auch herkömmliche Telefone angerufen werden. Mit den JavaScript-, Android- oder iOS-Clientbibliotheken von Communication Services können Sie Anwendungen erstellen, die es Benutzern ermöglichen, sich privat oder in Gruppendiskussionen miteinander zu unterhalten. Von Azure Communication Services werden aus- und eingehende Anrufe mit Diensten oder Bots unterstützt.

## <a name="call-types-in-azure-communication-services"></a>Anruftypen in Azure Communication Services

In Azure Communication Services können verschiedene Arten von Anrufen verwendet werden. Die Art der verwendeten Anrufe hat Auswirkungen auf das Signalschema, auf die Datenverkehrsflüsse für Medien sowie auf das Preismodell.

### <a name="voice-over-ip-voip"></a>Sprachtelefonie über IP (Voice over IP, VoIP) 

Wenn ein Benutzer Ihrer Anwendung einen anderen Benutzer Ihrer Anwendung über eine Internet- oder Datenverbindung anruft, wird der Anruf über Voice over IP (VoIP) getätigt. In diesem Fall werden sowohl Signale als auch Medien über das Internet übertragen.

### <a name="public-switched-telephone-network-pstn"></a>Telefonfestnetz (Public Switched Telephone Network, PSTN)

Wenn Ihre Benutzer mit einer herkömmlichen Telefonnummer interagieren, werden Anrufe als Sprachanrufe über das Telefonfestnetz (Public Switched Telephone Network, PSTN) abgewickelt. Für aus- und eingehende PSTN-Anrufe müssen Ihrer Azure Communication Services-Ressource Telefoniefunktionen hinzugefügt werden. In diesem Fall wird für Signale und Medien eine Kombination aus IP- und PSTN-basierten Technologien verwendet, um die Verbindung zwischen den Benutzern herzustellen.

### <a name="one-to-one-call"></a>1:1-Anruf

Ein 1:1-Anruf in Azure Communication Services erfolgt, wenn einer Ihrer Benutzer unter Verwendung einer unserer Clientbibliotheken eine Verbindung mit einem anderen Benutzer herstellt. Bei dem Anruf kann es sich um einen VoIP- oder um einen PSTN-Anruf handeln.

### <a name="group-call"></a>Gruppenanruf

Ein Gruppenanruf in Azure Communication Services erfolgt, wenn mindestens drei Teilnehmer eine Verbindung miteinander herstellen. An einem Gruppenanruf kann eine beliebige Kombination aus Benutzern mit VoIP- oder PSTN-Verbindung teilnehmen. Ein 1:1-Anruf kann durch Hinzufügen weiterer Teilnehmer in einen Gruppenanruf konvertiert werden. Einer dieser Teilnehmer kann ein Bot sein.

### <a name="supported-video-standards"></a>Unterstützte Videostandards
Wir unterstützen H.264 (MPEG-4). 

### <a name="video-quality"></a>Videoqualität
In den nativen SDKs (iOS, Android) wird maximal Full HD mit 1080p unterstützt. Für das Web SDK (JS) wird Standard HD mit 720p unterstützt. Die Qualität hängt von der verfügbaren Bandbreite ab.  

### <a name="rooms-concept"></a>Raumkonzept
Bei Räumen handelt es sich um eine Gruppe von APIs und SDKs, mit denen Sie Ihrer Website oder nativen Anwendung mühelos Audio-, Video-, Bildschirmfreigabe- und PSTN-Funktionen sowie SMS-Interaktionen hinzufügen können.
Während der Vorschauphase können Sie die Gruppen-ID verwenden, um an der gleichen Konversation teilzunehmen. Sie können beliebig viele Gruppen-IDs erstellen und die Benutzer durch Räume voneinander trennen. Die Steuerungsmöglichkeiten im Zusammenhang mit Räumen werden von uns noch erweitert. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit Anrufen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Weitere Informationen finden Sie in den folgenden Artikeln:
- Machen Sie sich mit allgemeinen [Anrufflows](../call-flows.md) vertraut.
- [Planen Sie Ihre PSTN-Lösung.](../telephony-sms/plan-solution.md)
- Informieren Sie sich über die [Funktionen der Clientbibliothek für Telefonie](../voice-video-calling/calling-sdk-features.md).
