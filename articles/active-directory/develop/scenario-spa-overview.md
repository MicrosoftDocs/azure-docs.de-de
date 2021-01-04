---
title: JavaScript-Szenario mit Single-Page-App – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung mithilfe der Microsoft Identity Platform erstellen (Szenarioübersicht).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 17acb2bc5e96a136f31371c0be912c2c758c0f76
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443856"
---
# <a name="scenario-single-page-application"></a>Szenario: Einseitige Anwendung

Erfahren Sie, wie Sie eine Single-Page-Webanwendung (SPA) erstellen.

## <a name="getting-started"></a>Erste Schritte

Erstellen Sie, falls noch nicht geschehen, Ihre erste App, indem Sie den „Schnellstart: JavaScript Single-Page-Webanwendung“ abschließen:

[Schnellstart: Single-Page-Webanwendung](./quickstart-v2-javascript.md)

## <a name="overview"></a>Übersicht

Viele moderne Webanwendungen werden als clientseitige Single-Page-Webanwendungen (SPAs) erstellt. Entwickler schreiben diese mithilfe von JavaScript oder eines SPA-Frameworks wie Angular, Vue oder React. Diese Anwendungen werden in einem Webbrowser ausgeführt und weisen andere Authentifizierungsmerkmale als herkömmliche serverseitige Webanwendungen auf.

Microsoft Identity Platform bietet **zwei** Optionen, um Single-Page-Webanwendungen das Anmelden von Benutzern und Abrufen von Token für den Zugriff auf Back-End-Dienste oder -Web-APIs zu ermöglichen:

- [OAuth 2.0-Autorisierungscodefluss (mit PKCE)](./v2-oauth2-auth-code-flow.md). Der Autorisierungscodefluss ermöglicht es der Anwendung, einen Autorisierungscode für **ID-Token** auszutauschen, um den authentifizierten Benutzer darzustellen, sowie **Zugriffstoken** auszutauschen, die zum Aufrufen geschützter APIs erforderlich sind. Darüber hinaus werden **Aktualisierungstoken** zurückgegeben, die langfristigen Zugriff auf Ressourcen im Auftrag von Benutzern bereitstellen, ohne dass eine Interaktion mit diesen Benutzern erforderlich ist. Dies ist die **empfohlene** Vorgehensweise.

![Autorisierungsfluss für Single-Page-Webanwendungen](./media/scenarios/spa-app-auth.svg)

- [Impliziter OAuth 2.0-Fluss](./v2-oauth2-implicit-grant-flow.md). Der Fluss für die implizite Genehmigung ermöglicht der Anwendung das Abrufen von **ID-** und **Zugriffstoken**. Im Gegensatz zum Autorisierungscodefluss gibt der Fluss für die implizite Genehmigung kein **Aktualisierungstoken** zurück.

![Impliziter Fluss für Single-Page-Webanwendungen](./media/scenarios/spa-app.svg)

Dieser Authentifizierungsfluss umfasst keine Anwendungsszenarios, die plattformübergreifende JavaScript-Frameworks verwenden, z. B. Electron und React-Native. Sie erfordern weitere Funktionen für die Interaktion mit den nativen Plattformen.

## <a name="specifics"></a>Besonderheiten

Zum Aktivieren dieses Szenarios für Anwendung benötigen Sie Folgendes:

* Eine Anwendungsregistrierung bei Azure Active Directory (Azure AD). Die Registrierungsschritte unterscheiden sich beim Fluss für die implizite Genehmigung und beim Autorisierungscodefluss.
* Sie benötigen eine Anwendungskonfiguration mit den registrierten Anwendungseigenschaften, wie z. B. der Anwendungs-ID.
* Sie müssen die Microsoft-Authentifizierungsbibliothek für JavaScript (MSAL.js) für den Authentifizierungsfluss zum Anmelden und Abrufen von Token verwenden.

## <a name="recommended-reading"></a>Empfohlene Literatur

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Registrierung](scenario-spa-app-registration.md).
