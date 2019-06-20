---
title: Daemon-App zum Aufrufen von Web-APIs (Übersicht) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Daemon-App erstellen, die Web-APIs aufruft.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080234"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Szenario: Daemon-App zum Aufrufen von Web-APIs

Erfahren Sie alles über die Erstellung einer Daemonanwendung, die Web-APIs aufruft.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Übersicht

Ihre Anwendung kann ein Token abrufen, um eine Web-API im eigenen Namen (nicht im Namen eines Benutzers) aufzurufen. Dieses Szenario eignet sich für Daemonanwendungen. Dabei werden die Standard-[Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) von OAuth 2.0 erteilt.

![Daemon-Apps](./media/scenario-daemon-app/daemon-app.svg)

Beispiele für Anwendungsfälle für Daemon-Apps:

- Webanwendungen zum Bereitstellen oder Verwalten von Benutzern oder zur Batchausführung von Prozessen in einem Verzeichnis
- Desktopanwendungen (z.B. Windows-Dienste für Windows oder Daemonprozesse unter Linux), die Batchaufträge ausführen, oder ein Betriebssystemdienst, der im Hintergrund ausgeführt wird
- Web-APIs, die Verzeichnisse und keine bestimmten Benutzer bearbeiten müssen

Es gibt einen weiteren häufig vorkommenden Anwendungsfall, bei dem Nicht-Daemonanwendungen Clientanmeldeinformationen verwenden: Auch wenn sie im Auftrag von Benutzern fungieren, müssen sie aus technischen Gründen mit ihrer Identität auf eine Web-API oder eine Ressource zugreifen. Ein Beispiel ist der Zugriff auf Geheimnisse in Key Vault oder auf eine Azure SQL-Datenbank für einen Cache.

Für Anwendungen, die ein Token für ihre eigenen Identitäten abrufen, gilt Folgendes:

- Sie sind vertrauliche Clientanwendungen. Diese Apps müssen angesichts der Tatsache, dass sie unabhängig von einem Benutzer auf Ressourcen zugreifen, ihre Identität nachweisen. Sie sind außerdem eher sensible Apps, sodass sie von den Azure AD-Mandantenadministratoren (Azure Active Directory) genehmigt werden müssen.
- Sie haben bei Azure AD ein Geheimnis registriert (Anwendungskennwort oder Zertifikat). Dieses Geheimnis wird während des Aufrufs an Azure AD zum Abrufen eines Tokens übergeben.

## <a name="specifics"></a>Besonderheiten

> [!IMPORTANT]
>
> - Die Benutzerinteraktion ist mit einer Daemonanwendung nicht möglich. Eine Daemonanwendung erfordert eine eigene Identität. Diese Art von Anwendung fordert auf der Grundlage ihrer Anwendungsidentität ein Zugriffstoken an und gibt dabei gegenüber Azure AD ihre Anwendungs-ID, ihre Anmeldeinformationen (Kennwort oder Zertifikat) sowie ihren Anwendungs-ID-URI an. Nach erfolgreicher Authentifizierung erhält der Daemon ein Zugriffstoken (und ein Aktualisierungstoken) vom Microsoft Identity Platform-Endpunkt, das dann zum Aufrufen der Web-API verwendet (und nach Bedarf aktualisiert) wird.
> - Da die Benutzerinteraktion nicht möglich ist, ist keine inkrementelle Einwilligung möglich. Alle erforderlichen API-Berechtigungen müssen bei der Anwendungsregistrierung konfiguriert werden, und der Code der Anwendung fordert nur statisch definierte Berechtigungen an. Dies bedeutet auch, dass Daemonanwendungen die inkrementelle Einwilligung nicht unterstützen.

Für Entwickler umfasst die End-to-End-Umgebung für dieses Szenario die folgenden Aspekte:

- Daemonanwendungen funktionieren nur in Azure AD-Mandanten. Es wäre nicht sinnvoll, eine Daemonanwendung zu erstellen, die persönliche Microsoft-Konten zu bearbeiten versucht. Wenn Sie Entwickler für eine branchenspezifische App sind, erstellen Sie Ihre Daemon-App in Ihrem Mandanten. Wenn Sie ein ISV sind, empfiehlt sich die Erstellung einer mehrinstanzenfähigen Daemonanwendung. Sie müssen sich von jedem Mandantenadministrator eine Einwilligung einholen.
- Während der [Anwendungsregistrierung](./scenario-daemon-app-registration.md) ist der **Antwort-URI** nicht erforderlich. Sie müssen Geheimnisse oder Zertifikate für Azure AD freigeben sowie Anwendungsberechtigungen anfordern und die Administratoreinwilligung zur Verwendung dieser App-Berechtigungen erteilen.
- Die [Anwendungskonfiguration](./scenario-daemon-app-configuration.md) muss Clientanmeldeinformationen bereitstellen, die während der Anwendungsregistrierung für Azure AD freigegeben werden.
- Der [Bereich](scenario-daemon-acquire-token.md#scopes-to-request) zum Abrufen eines Tokens über den Clientanmeldeinformations-Flow muss statisch sein.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Daemon-App: App-Registrierung](./scenario-daemon-app-registration.md)
