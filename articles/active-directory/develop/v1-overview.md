---
title: Azure Active Directory für Entwickler (v1.0) – Übersicht
description: Dieser Artikel enthält eine Übersicht über die Anmeldung bei Geschäfts-, Schul- oder Unikonten von Microsoft mit v1.0-Endpunkt und -Plattform von Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13cc5c7ae428f74f2892e6066dfdcd7efb73efbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545235"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Azure Active Directory für Entwickler (v1.0) – Übersicht

Azure Active Directory (Azure AD) ist ein Cloudidentitätsdienst, mit dem Entwickler Apps erstellen können, über die Benutzer sicher mit einem Geschäfts-, Schul- oder Unikonto von Microsoft angemeldet werden. Azure AD unterstützt sowohl Entwickler, die Branchen-Apps mit einem einzelnen Mandanten erstellen, als auch Entwickler, die mehrinstanzenfähige Apps erstellen möchten. Zusätzlich zur einfachen Anmeldung ermöglicht Azure AD Apps auch das Aufrufen von Microsoft-APIs, z.B. [Microsoft Graph](https://docs.microsoft.com/graph/overview), und von benutzerdefinierten APIs, die basierend auf der Azure AD-Plattform erstellt werden. In dieser Dokumentation erfahren Sie, wie Sie Ihrer Anwendung Azure AD-Unterstützung mit branchenüblichen Authentifizierungsprotokollen wie OAuth2.0 und OpenID Connect hinzufügen.

> [!NOTE]
> Auf dieser Seite geht es hauptsächlich um den v1.0-Endpunkt und die v1.0-Plattform, die beide nur Geschäfts-, Schul- oder Unikonten von Microsoft unterstützen. Wenn Sie die Anmeldung für Endverbraucher oder persönliche Microsoft-Konten durchführen möchten, lesen Sie die Informationen zum [v2.0-Endpunkt und zur v2.0-Plattform](v2-overview.md). Der v2.0-Endpunkt bietet eine einheitliche Entwicklungsumgebung für Apps, die alle Microsoft-Identitäten anmelden möchten.

| | |
| --- | --- |
|[Authentifizierungsszenarien für Azure AD](authentication-scenarios.md) | Eine Einführung in die Authentifizierung mit Azure AD. |
|[Anwendungsarten](app-types.md) | Eine Übersicht über die von Azure AD unterstützten Authentifizierungsszenarien. |
| | |

## <a name="get-started"></a>Erste Schritte

In den Schnellstartanleitungen und Tutorials zu v1.0 wird Schritt für Schritt die Erstellung einer App auf Ihrer bevorzugten Plattform mit dem ADAL SDK (Azure AD Authentication Library) beschrieben. Informationen zu den ersten Schritten finden Sie unter [Microsoft identity platform (Azure Active Directory for developers)](index.yml) (Microsoft-Identitätsplattform (Azure Active Directory für Entwickler)) in den **Schnellstartanleitungen zu v1.0** und in den **Tutorials zu v1.0**.

## <a name="how-to-guides"></a>Anleitungen

Ausführliche Informationen und exemplarische Vorgehensweisen für die gängigsten Aufgaben in Azure AD finden Sie in den **Schrittanleitungen zu v1.0**.

## <a name="reference-topics"></a>Referenzthemen

Die folgenden Artikel enthalten ausführliche Informationen zu APIs, Protokollmeldungen und Begriffen, die in Azure AD verwendet werden.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Authentifizierungsbibliotheken (ADAL)](active-directory-authentication-libraries.md)   | Eine Übersicht über die von Azure AD bereitgestellten Bibliotheken und SDKs. |
| [Codebeispiele](sample-v1-code.md)                                  | Eine Liste mit allen Azure AD-Codebeispielen. |
| [Glossar](developer-glossary.md)                                      | Begriffe und Definitionen von Wörtern, die in dieser Dokumentation verwendet werden. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
