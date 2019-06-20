---
title: Client- und Server-SDK-Versionskontrolle in Mobile Apps und Mobile Services | Microsoft-Dokumentation
description: Liste der Client-SDKs und Kompatibilität mit Server-SDK-Versionen für Mobile Services und Azure Mobile Apps
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: cfa6a363725c35083b32d6de1dd1371777f91907
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240301"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Client- und Serverversionsverwaltung in Mobile Apps und Mobile Services
Die neueste Version von Azure Mobile Services ist das Feature **Mobile Apps** von Azure App Service.

Die Mobile Apps-Client- und -Server-SDKs basieren ursprünglich auf den SDKs in Mobile Services, aber sie sind *nicht* miteinander kompatibel.
Dies bedeutet, dass Sie ein *Mobile Apps*-Client-SDK mit einem *Mobile Apps*-Server-SDK und ebenso für *Mobile Services* verwenden müssen. Dieser Vertrag wird mit einem speziellen Headerwert durchgesetzt, der von den Client- und Server-SDKs verwendet wird: `ZUMO-API-VERSION`.

Hinweis: Wenn in diesem Dokument auf ein *Mobile Services* -Back-End verwiesen wird, heißt das nicht zwangsläufig, dass es unter Mobile Services gehostet wird. Es ist jetzt möglich, einen mobilen Dienst für die Ausführung unter App Service ohne Codeänderungen zu migrieren, aber für den Dienst würden dabei trotzdem *Mobile Services*-SDK-Versionen verwendet werden.

Weitere Informationen zum Migrieren zu App Service ohne Codeänderungen finden Sie im Artikel [Migrieren eines Mobile Service zu Azure App Service].

## <a name="header-specification"></a>Headerspezifikation
Der Schlüssel `ZUMO-API-VERSION` kann entweder im HTTP-Header oder in der Abfragezeichenfolge angegeben werden. Der Wert ist eine Versionszeichenfolge im Format **x.y.z**.

Beispiel:

GET https://service.azurewebsites.net/tables/TodoItem

HEADER: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Deaktivieren der Versionsüberprüfung
Sie können die Versionsüberprüfung deaktivieren, indem Sie den Wert **true** für die App-Einstellung **MS_SkipVersionCheck** festlegen. Die Angabe kann entweder in der Datei „web.config“ oder im Azure-Portal im Abschnitt mit den Anwendungseinstellungen vorgenommen werden.

> [!NOTE]
> Es gibt einige Änderungen des Verhaltens zwischen Mobile Services und Mobile Apps, vor allem in den Bereichen der Offlinesynchronisierung, Authentifizierung und Pushbenachrichtigungen. Sie sollten die Versionsüberprüfung erst deaktivieren, wenn Sie sich durch umfassende Tests vergewissert haben, dass diese Änderungen des Verhaltens nicht zu Problemen mit den Funktionen Ihrer App führen.

## <a name="2.0.0"></a>Azure Mobile Apps-Client und -Server
### <a name="MobileAppsClients"></a> Mobile *Apps* -Client-SDKs
Die Versionsüberprüfung wurde ab den folgenden Versionen des Client-SDK für **Azure Mobile Apps**eingeführt:

| Clientplattform | Version | Versionsheaderwert |
| --- | --- | --- |
| Verwalteter Client (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Mobile *Apps* -Server-SDKs
Die Versionsüberprüfung ist in den folgenden Versionen des Server-SDK enthalten:

| Serverplattform | SDK | Akzeptierter Versionsheader |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Verhalten von Mobile Apps-Back-Ends
| ZUMO-API-VERSION | Wert von MS_SkipVersionCheck | response |
| --- | --- | --- |
| x.y.z oder NULL |True |200 – OK |
| Null |False/Nicht angegeben |400 – Ungültige Anforderung |
| 1.x.y |False/Nicht angegeben |400 – Ungültige Anforderung |
| 2.0.0 - 2.x.y |False/Nicht angegeben |200 – OK |
| 3.0.0-3.x.y |False/Nicht angegeben |400 – Ungültige Anforderung |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
