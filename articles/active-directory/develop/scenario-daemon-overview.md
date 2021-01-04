---
title: Erstellen einer Daemon-App, die Web-APIs aufruft – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Daemon-App erstellen, die Web-APIs aufruft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e63a948260863c93a92e4241044be5e0baf8afca
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443261"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Szenario: Daemon-App zum Aufrufen von Web-APIs

Erfahren Sie alles über die Erstellung einer Daemonanwendung, die Web-APIs aufruft.

## <a name="overview"></a>Übersicht

Ihre Anwendung kann ein Token abrufen, um eine Web-API im eigenen Namen (nicht im Namen eines Benutzers) aufzurufen. Dieses Szenario eignet sich für Daemonanwendungen. Es verwendet die Standardzuweisung für [Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) von OAuth 2.0.

![Daemon-Apps](./media/scenario-daemon-app/daemon-app.svg)

Beispiele für Anwendungsfälle für Daemon-Apps:

- Webanwendungen zum Bereitstellen oder Verwalten von Benutzern oder für Batchprozesse in einem Verzeichnis
- Desktopanwendungen (z. B. Windows-Dienste unter Windows oder Daemonprozesse unter Linux), die Batchaufträge ausführen, oder ein Betriebssystemdienst, der im Hintergrund ausgeführt wird
- Web-APIs, die Verzeichnisse und keine bestimmten Benutzer bearbeiten müssen

Es gibt einen weiteren häufig vorkommenden Fall, bei dem Nicht-Daemonanwendungen Clientanmeldeinformationen verwenden: Auch wenn sie im Auftrag von Benutzern fungieren, müssen sie aus technischen Gründen unter ihrer eigenen Identität auf eine Web-API oder eine Ressource zugreifen. Ein Beispiel ist der Zugriff auf Geheimnisse in Azure Key Vault oder auf Azure SQL-Datenbank für einen Cache.

Für Anwendungen, die ein Token für ihre eigenen Identitäten abrufen, gilt Folgendes:

- Sie sind vertrauliche Clientanwendungen. Diese Apps müssen angesichts der Tatsache, dass sie unabhängig von Benutzern auf Ressourcen zugreifen, ihre Identität nachweisen. Sie sind außerdem eher sensible Apps. Sie müssen von den Azure Active Directory (Azure AD)-Mandantenadministratoren genehmigt werden.
- Sie haben bei Azure AD ein Geheimnis registriert (Anwendungskennwort oder Zertifikat). Dieses Geheimnis wird während des Aufrufs an Azure AD zum Abrufen eines Tokens übergeben.

## <a name="specifics"></a>Besonderheiten

> [!IMPORTANT]
>
> - Benutzer können nicht mit einer Daemonanwendung in Interaktion treten. Eine Daemonanwendung erfordert eine eigene Identität. Diese Art von Anwendung fordert auf der Grundlage ihrer Anwendungsidentität ein Zugriffstoken an und gibt dabei gegenüber Azure AD ihre Anwendungs-ID, ihre Anmeldeinformationen (Kennwort oder Zertifikat) sowie ihren Anwendungs-ID-URI an. Nach erfolgreicher Authentifizierung erhält der Daemon ein Zugriffstoken (und ein Aktualisierungstoken) vom Microsoft Identity Platform-Endpunkt. Dieses Token wird dann zum Aufrufen der Web-API verwendet (und nach Bedarf aktualisiert).
> - Da Benutzer nicht mit Daemonanwendungen interagieren können, ist keine inkrementelle Zustimmung möglich. Alle erforderlichen API-Berechtigungen müssen bei der Anwendungsregistrierung konfiguriert werden. Der Anwendungscode fordert nur statisch definierte Berechtigungen an. Dies bedeutet auch, dass Daemonanwendungen die inkrementelle Einwilligung nicht unterstützen.

Für Entwickler umfasst die End-to-End-Umgebung für dieses Szenario die folgenden Aspekte:

- Daemonanwendungen funktionieren nur in Azure AD-Mandanten. Es wäre nicht sinnvoll, eine Daemonanwendung zu erstellen, die persönliche Microsoft-Konten zu bearbeiten versucht. Wenn Sie Entwickler für eine branchenspezifische App sind, erstellen Sie Ihre Daemon-App in Ihrem Mandanten. Wenn Sie ein ISV sind, empfiehlt sich die Erstellung einer mehrinstanzenfähigen Daemonanwendung. Jeder Mandantenadministrator muss seine Zustimmung geben.
- Bei der [Anwendungsregistrierung](./scenario-daemon-app-registration.md) ist der Antwort-URI nicht erforderlich. Sie müssen Geheimnisse oder Zertifikate bzw. signierte Assertionen für Azure AD freigeben. Sie müssen auch Anwendungsberechtigungen anfordern und Administratorzustimmung erteilen, um diese App-Berechtigungen zu verwenden.
- Die [Anwendungskonfiguration](./scenario-daemon-app-configuration.md) muss die Clientanmeldeinformationen bereitstellen, die bei der Anwendungsregistrierung für Azure AD freigegeben wurden.
- Der [Bereich](scenario-daemon-acquire-token.md#scopes-to-request) zum Abrufen eines Tokens über den Clientanmeldeinformations-Flow muss statisch sein.

## <a name="recommended-reading"></a>Empfohlene Literatur

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Registrierung](./scenario-daemon-app-registration.md).
