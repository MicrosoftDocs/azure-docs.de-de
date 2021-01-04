---
title: Registrieren einer Web-API, die Web-APIs aufruft – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Web-API erstellen, die Downstream-Web-APIs aufruft (App-Registrierung).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 790580160ec236d1923dc28b9990d2675c253b44
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442683"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Web-API, die Web-APIs aufruft: App-Registrierung

Eine Web-API, die Downstream-Web-APIs aufruft, verfügt über dieselbe Registrierung wie eine geschützte Web-API. Befolgen Sie daher die Anweisungen unter [Geschützte Web-API: App-Registrierung](scenario-protected-web-api-app-registration.md).

Da die Web-App jetzt Web-APIs aufruft, wird sie zu einer vertraulichen Clientanwendung. Deshalb sind weitere Registrierungsinformationen erforderlich: Die App muss Geheimnisse (Clientanmeldeinformationen) für Microsoft Identity Platform freigeben.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-Berechtigungen

Web-Apps rufen APIs im Namen von Benutzern auf, für die das Bearertoken empfangen wurde. Die Web-Apps müssen delegierte Berechtigungen anfordern. Weitere Informationen finden Sie unter [Hinzufügen von Zugriffsberechtigungen für Ihre Web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Codekonfiguration](scenario-web-api-call-api-app-configuration.md).
