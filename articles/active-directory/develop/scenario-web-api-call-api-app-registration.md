---
title: Web-API, die Downstream-Web-APIs aufruft (App-Registrierung) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-API erstellen, die Downstream-Web-APIs aufruft (App-Registrierung).
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080168"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Web-API, die Web-APIs aufruft – App-Registrierung

Eine Web-API, die Downstream-Web-APIs aufruft, verfügt über dieselbe Registrierung wie eine geschützte Web-API. Befolgen Sie deshalb die Anweisungen unter [Geschützte Web-API – App-Registrierung](scenario-protected-web-api-app-registration.md).

Da die Web-App jedoch jetzt Web-APIs aufruft, wird sie zu einer vertraulichen Clientanwendung. Deshalb sind weitere Registrierungsinformationen erforderlich: Die Anwendung muss Geheimnisse (Clientanmeldeinformationen) für Microsoft Identity Platform freigeben.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-Berechtigungen

Webanwendung rufen APIs im Namen von Benutzern auf, für die das Bearertoken empfangen wurde. Sie müssen delegierte Berechtigungen anfordern. Weitere Informationen finden Sie unter [Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Codekonfiguration der App](scenario-web-api-call-api-app-configuration.md)
