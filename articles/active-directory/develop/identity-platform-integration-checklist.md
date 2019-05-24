---
title: Integration in Microsoft Identity Platform | Azure
description: Erhalten Sie Informationen zu bewährten Methoden und häufigen Fehlern bei der Integration in Microsoft Identity Platform (v2. 0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 0d44e5d9f0d1ed893a16c318e3cfa0ee1bfc123b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823347"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Checkliste für die Integration in Microsoft Identity Platform

Die Checkliste für die Microsoft Identity Platform-Integration leitet Sie durch eine hochwertige und sichere Integration. Darin sind bewährte Methoden und häufige Fehler bei der Integration in Microsoft Identity Platform hervorgehoben. Überprüfen Sie die Liste daher in regelmäßigen Abständen, um sicherzustellen, dass die Qualität und Sicherheit der Integration Ihrer App in Identity Platform gewährleistet bleiben. Die Checkliste ist nicht auf die Überprüfung der gesamten Anwendung ausgerichtet. Der Inhalt der Checkliste kann jederzeit geändert werden, während wir Verbesserungen an der Plattform vornehmen.

Lesen Sie zum Einstieg die [Dokumentation](index.yml), um Grundlagen zu Authentifizierungsszenarien, Anwendungsszenarien in Microsoft Identity Platform und weitere Informationen zu erhalten.

## <a name="testing-your-integration"></a>Testen Ihrer Integration

Stellen Sie anhand der folgenden Checkliste sicher, dass Ihre Anwendung effektiv in [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) integriert wird.

### <a name="basics"></a>Grundlagen

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Beschäftigen Sie sich intensiv mit den [Richtlinien für die Microsoft-Plattform](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Stellen Sie sicher, dass Ihre Anwendung den dargelegten Bedingungen entspricht, weil diese für den Schutz der Benutzer und der Plattform entwickelt wurden. |

### <a name="ownership"></a>Besitz

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Stellen Sie sicher, dass die Informationen, die dem zur Registrierung und Verwaltung von Apps verwendeten Konto zugeordnet sind, auf dem neuesten Stand sind. |

### <a name="branding"></a>Branding

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Richten Sie sich nach den [Brandingrichtlinien für Anwendungen](howto-add-branding-in-azure-ad-apps.md). |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Geben Sie einen aussagekräftigen Namen und ein Logo für Ihre Anwendung ein. Diese Informationen werden in der Einwilligungsaufforderung Ihrer Anwendung angezeigt. Stellen Sie sicher, dass Name und Logo für Ihr Unternehmen/Produkt repräsentativ sind, damit Benutzer fundierte Entscheidungen treffen können. Stellen Sie sicher, dass Sie keine Markenrechte verletzen. |

### <a name="privacy"></a>Datenschutz

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Geben Sie Links zu den Nutzungsbedingungen und Datenschutzbestimmungen Ihrer App an. |

### <a name="security"></a>Sicherheit

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Verwalten Sie den Besitz all Ihrer Umleitungs-URIs, und halten Sie die zugehörigen DNS-Einträge auf dem neuesten Stand. Verwenden Sie keine Platzhalter (*) in Ihren URIs. Stellen Sie für Web-Apps sicher, dass alle URIs sicher und verschlüsselt sind (z.B. mithilfe von HTTPS-Schemas). Verwenden Sie für öffentliche Clients ggf. plattformspezifische Umleitungs-URIs (vor allem für iOS und Android). Verwenden Sie andernfalls Umleitungs-URIs mit hohem Zufallsfaktor, um Konflikte beim Rückruf an Ihre App zu vermeiden. Wenn Ihre App von einem isolierten Web-Agent aus verwendet wird, können Sie https://login.microsoftonline.com/nativeclient verwenden. Überprüfen und kürzen Sie in regelmäßigen Abständen alle nicht verwendeten oder nicht benötigten Umleitungs-URIs. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Wenn Ihre App in einem Verzeichnis registriert ist, minimieren Sie die Liste der Besitzer von App-Registrierungen, und überwachen Sie sie manuell. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Aktivieren Sie die Unterstützung für den [Flow zur impliziten OAuth2-Genehmigung](v2-oauth2-implicit-grant-flow.md) nur dann, wenn dies explizit erforderlich ist. Informationen zu einem gültigen Szenario finden Sie [hier](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Verwenden Sie keinen [Flow für Ressourcenbesitzer-Kennwortanmeldeinformationen (ROPC)](v2-oauth-ropc.md), in dem die Kennwörter von Benutzern direkt verarbeitet werden. Dieser Flow erfordert ein hohes Maß an Vertrauen und die Freigabe von Benutzeranmeldeinformationen. Er darf nur dann verwendet werden, wenn keine anderen, sichereren Flows genutzt werden können. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Schützen und verwalten Sie Ihre App-Anmeldeinformationen. Verwenden Sie [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md), keine Kennwortanmeldeinformationen (Clientgeheimnisse). Wenn Sie eine Kennwortanmeldeinformation verwenden müssen, legen Sie diese nicht manuell fest. Speichern Sie Anmeldeinformationen nicht im Code oder in der Konfiguration, und lassen Sie sie niemals von Personen bearbeiten. Verwenden Sie möglichst [verwaltete Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) oder [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) zum Speichern und regelmäßigen Rotieren Ihrer Anmeldeinformationen. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Stellen Sie sicher, dass Ihre Anwendung den Ansatz der geringsten Rechte verfolgt. Fordern Sie nur Berechtigungen an, die für Ihre Anwendung unbedingt erforderlich sind, und nur dann, wenn sie benötigt werden. Informieren Sie sich über die verschiedenen [Berechtigungstypen](v1-permissions-and-consent.md#types-of-permissions). Verwenden Sie Anwendungsberechtigungen nur im Bedarfsfall. Verwenden Sie nach Möglichkeit delegierte Berechtigungen. Eine vollständige Liste der Microsoft Graph-Berechtigungen finden Sie in dieser [Berechtigungsreferenz](https://docs.microsoft.com/graph/permissions-reference). |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Wenn Sie eine API über Microsoft Identity Platform schützen, erwägen Sie sorgfältig, welche Berechtigungen darin verfügbar gemacht werden sollen. Überlegen Sie sich die richtige Granularität für Ihre Lösung und entscheiden Sie, welche Berechtigungen die Einwilligung eines Administrators erfordern. Prüfen Sie auf erwartete Berechtigungen in den eingehenden Token, bevor Sie Autorisierungsentscheidungen treffen. |

### <a name="implementation"></a>Implementierung

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Verwenden Sie moderne Authentifizierungslösungen (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) zur sicheren Benutzeranmeldung. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementieren Sie die Protokolle nicht selbst. Verwenden Sie [von Microsoft unterstützte Authentifizierungsbibliotheken](reference-v2-libraries.md) (MSAL, Servermiddleware). Stellen Sie sicher, dass Sie die neueste Version der für die Integration verwendeten Authentifizierungsbibliothek einsetzen. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Wenn die von Ihrer App benötigten Daten über [Microsoft Graph](https://developer.microsoft.com/graph) verfügbar sind, fordern Sie Berechtigungen für diese Daten über den Microsoft Graph-Endpunkt statt über die einzelne API an. |

### <a name="end-user-experience"></a>Endbenutzererfahrung

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | [Beschäftigen Sie sich mit der Einwilligungsoberfläche](application-consent-experience.md), und konfigurieren Sie die Bestandteile der App-Einwilligungsaufforderung so, dass Endbenutzer und Administratoren genügend Informationen erhalten, um über die Vertrauenswürdigkeit Ihrer App zu entscheiden. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimieren Sie, wie häufig ein Benutzer bei Verwendung Ihrer App Anmeldeinformationen eingeben muss, indem vor interaktiven Flows versucht wird, die automatische Authentifizierung (automatischer Tokenabruf) durchzuführen. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Verwenden Sie nicht „prompt=consent“ für jede Anmeldung. Verwenden Sie „prompt=consent“ nur dann, wenn Sie festgestellt haben, dass für die zusätzlichen Berechtigungen eine Einwilligung erforderlich ist (z.B., wenn Sie erforderliche Berechtigungen Ihrer App geändert haben). |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Reichern Sie Ihre Anwendung ggf. mit Benutzerdaten an. Die Verwendung der [Microsoft Graph-API](https://developer.microsoft.com/graph) bietet dazu eine einfache Möglichkeit. Das Tool [Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) kann Sie bei den ersten Schritten unterstützen. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrieren Sie den vollständigen Satz an Berechtigungen, die für Ihre App erforderlich sind, damit Administratoren ihre Einwilligung für ihren Mandanten ganz leicht erteilen können. Verwenden Sie die [inkrementelle Einwilligung](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) zur Laufzeit. So können Benutzer nachvollziehen, warum Ihre App Berechtigungen anfordert, die Besorgnis oder Verwirrung hervorrufen können, wenn sie gleich beim ersten Start angefordert werden. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementieren Sie eine [saubere Oberfläche für einmaliges Abmelden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Dies ist eine Datenschutz- und Sicherheitsanforderung, die eine gute Benutzeroberfläche ausmacht. |

### <a name="testing"></a>Testen

|   |   |
|---|---|
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Testen Sie auf [Richtlinien für bedingten Zugriff](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut), die die Verwendung Ihrer Anwendung verhindern können. |
| ![Kontrollkästchen](./media/active-directory-integration-checklist/checkbox-two.svg) | Testen Sie Ihre Anwendung mit allen möglichen Konten, die Sie unterstützen möchten (z.B. Geschäfts-, Schul- oder Unikonten, persönliche Microsoft-Konten, Kinderkonten und unabhängige Konten). |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Hier erhalten Sie detaillierte Informationen zu v2.0:

* [Microsoft Identity Platform (Übersicht über v2.0)](v2-overview.md)
* [Referenz zu Microsoft Identity Platform-Protokollen](active-directory-v2-protocols.md)
* [Zugriffstokenreferenz](access-tokens.md)
* [ID-Tokenreferenz](id-tokens.md)
* [Referenz zu Authentifizierungsbibliotheken](reference-v2-libraries.md)
* [Berechtigungen und Einwilligung in Microsoft Identity Platform](v2-permissions-and-consent.md)
* [Microsoft Graph-API](https://developer.microsoft.com/graph)
