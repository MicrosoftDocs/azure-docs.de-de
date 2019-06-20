---
title: Web-App, die Web-APIs aufruft (App-Registrierung) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Web-APIs aufruft (App-Registrierung).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080142"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Web-App, die Web-APIs aufruft – App-Registrierung

Eine Web-App, die Web-APIs aufruft, verfügt über die gleiche Registrierung wie eine Web-App, die Benutzer anmeldet. Befolgen Sie deshalb die Anweisungen unter [Web-App, die Benutzer anmeldet (App-Registrierung)](scenario-web-app-sign-user-app-registration.md).

Da die Web-App jedoch jetzt Web-APIs aufruft, wird sie zu einer vertraulichen Clientanwendung. Deshalb sind weitere Registrierungsschritte erforderlich: Die Anwendung muss Geheimnisse für Microsoft Identity Platform freigeben.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-Berechtigungen

Webanwendungen rufen APIs für angemeldete Benutzer auf. Sie müssen delegierte Berechtigungen anfordern. Weitere Informationen finden Sie unter [Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Codekonfiguration der App](scenario-web-app-call-api-app-configuration.md)
