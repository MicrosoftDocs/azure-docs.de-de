---
title: Single-Page-Webanwendung (App-Registrierung) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung (App-Registrierung) erstellen.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080094"
---
# <a name="single-page-application---app-registration"></a>Single-Page-Webanwendung – App-Registrierung

Auf dieser Seite werden die Besonderheiten der App-Registrierung für eine Single-Page-Webanwendung (Single-Page App, SPA) erläutert.

Befolgen Sie die Schritte, um [eine neue Anwendung bei Microsoft Identity Platform zu registrieren](quickstart-register-app.md), und wählen Sie die unterstützten Konten für Ihre Anwendung aus. Das SPA-Szenario kann die Authentifizierung mit Konten in Ihrer Organisation oder in anderen Organisation und persönlichen Microsoft-Konten unterstützen.

Erfahren Sie nun mehr zu den Punkten der Anwendungsregistrierung, die für Single-Page-Webanwendungen gelten.

## <a name="register-a-redirect-uri"></a>Registrieren eines Umleitungs-URI

Der implizite Fluss sendet die Token in einer Umleitung an die Single-Page-Webanwendung, die in einem Webbrowser ausgeführt wird. Deshalb wird vorausgesetzt, dass Sie einen Umleitungs-URI registrieren, über den Ihre Anwendung die Token empfangen kann. Achten Sie darauf, dass der Umleitungs-URI genau mit dem URI Ihrer Anwendung übereinstimmt.

Navigieren Sie im [Azure-Portal](https://go.microsoft.com/fwlink/?linkid=2083908) zu Ihrer registrierten Anwendung, wählen Sie auf der Seite **Authentifizierung** **Web** als Plattform aus, und geben Sie im Feld **Umleitungs-URI** den Wert des Umleitungs-URI für Ihre Anwendung ein.

## <a name="enable-the-implicit-flow"></a>Aktivieren des impliziten Flows

Aktivieren Sie außerdem auf der Seite **Authentifizierung** unter **Erweiterte Einstellungen** die Option **Implizite Genehmigung**. Wenn Ihre Anwendung nur Benutzer anmeldet und ID-Token abruft, reicht es aus, die Option **ID-Token** zu aktivieren.

Wenn Ihre Anwendung zudem Zugriffstoken zum Aufrufen von APIs benötigt, aktivieren Sie zusätzlich die Option **Zugriffstoken**. Weitere Informationen finden Sie unter [ID-Token](./id-tokens.md) und [Zugriffstoken](./access-tokens.md).

## <a name="api-permissions"></a>API-Berechtigungen

SPAs können APIs im Namen angemeldeter Benutzer aufrufen. Sie müssen delegierte Berechtigungen anfordern. Weitere Informationen finden Sie unter [Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Codekonfiguration der App](scenario-spa-app-configuration.md)
