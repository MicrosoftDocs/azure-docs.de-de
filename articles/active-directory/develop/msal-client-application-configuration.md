---
title: Konfigurieren von Clientanwendungen (Microsoft-Authentifizierungsbibliothek) | Azure
description: Erfahren Sie mehr über Konfigurationsoptionen für öffentliche und vertrauliche Clientanwendungen in der Microsoft-Authentifizierungsbibliothek (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d731a8153dc6a70382c0d87cc20d8c961d9fe24
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546007"
---
# <a name="application-configuration-options"></a>Anwendungskonfigurationsoptionen

In Ihrem Code initialisieren Sie eine neue öffentliche oder vertrauliche Clientanwendung (oder bei MSAL.js einen Benutzer-Agent) zum Authentifizieren und Abrufen von Token.  Es gibt zahlreiche verschiedene Konfigurationsoptionen, die beim Initialisieren der Clientanwendung in MSAL festgelegt werden können. Diese Optionen können in zwei Gruppen unterteilt werden:

- Registrierungsoptionen, einschließlich:
    - [Autorität](#authority) (bestehend aus der [Instanz](#cloud-instance) des Identitätsanbieters und der [Zielgruppe](#application-audience) für die Anmeldung der Anwendung sowie darüber hinaus möglicherweise der Mandanten-ID).
    - [Client-ID](#client-id)
    - [Umleitungs-URI](#redirect-uri)
    - [Geheimer Clientschlüssel](#client-secret) (für vertrauliche Clientanwendungen)
- [Protokollierungsoptionen](#logging), einschließlich Protokollebene, Kontrolle über personenbezogene Daten und des Namens der Komponente, die die Bibliothek verwendet

## <a name="authority"></a>Authority
Die Autorität ist eine URL, die ein Verzeichnis angibt, von dem MSAL Token anfordern kann. Übliche Autoritäten sind:

- https://login.microsoftonline.com/&lt;Mandant&gt;/, wobei &lt;Mandant&gt; die Mandanten-ID des Azure AD-Mandanten oder eine Domäne ist, die diesem Azure AD-Mandanten zugeordnet ist.  Diese wird nur für die Anmeldung von Benutzern einer bestimmten Organisation verwendet.
- https://login.microsoftonline.com/common/. Damit werden Benutzer mit Geschäfts-, Schul- und Unikonten oder einem persönlichen Microsoft-Konto angemeldet.
- https://login.microsoftonline.com/organizations/. Damit werden Benutzer mit Geschäfts-, Schul- oder Unikonten angemeldet.
- https://login.microsoftonline.com/consumers/. Damit werden Benutzer angemeldet, die nur über ein persönliches Microsoft-Konto (live) verfügen.

Die Einstellung für die Autorität muss mit den Angaben im Anwendungsregistrierungs-Portal übereinstimmen.

Die Autoritäts-URL besteht aus der Instanz und der Zielgruppe.

Mögliche Autoritäten:
- Azure Active Directory-Cloudautorität
- Azure AD B2C-Autorität. Siehe [B2C-Besonderheiten](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- AD FS-Autorität. Siehe [AD FS-Unterstützung](https://aka.ms/msal-net-adfs-support).

Azure AD-Cloudautoritäten bestehen aus zwei Komponenten:
- Identitätsanbieter-**Instanz**
- **Zielgruppe** für die Anmeldung bei der Anwendung

Die Instanz und die Zielgruppe können verkettet und als Autoritäts-URL angegeben werden. In Versionen von MSAL.NET vor MSAL 3.x mussten Sie die Autorität selbst in Abhängigkeit von der Zielcloud und der Anmeldezielgruppe zusammenstellen.  Das folgende Diagramm zeigt, woraus sich die Autoritäts-URL zusammensetzt.

![Authority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Cloudinstanz
Die **Instanz** wird verwendet, um anzugeben, ob Ihre Anwendung Benutzer aus der öffentlichen Cloud von Microsoft Azure oder aus nationalen Clouds anmeldet. Wenn Sie in Ihrem Code die MSAL verwenden, kann die Azure-Cloudinstanz mithilfe einer Enumeration oder der Übergabe der URL an die [Instanz einer nationalen Cloud](authentication-national-cloud.md#azure-ad-authentication-endpoints) als das `Instance`-Element (sofern bekannt) festgelegt werden.

MSAL.NET löst eine explizite Ausnahme aus, wenn sowohl `Instance` als auch `AzureCloudInstance` angegeben werden. 

Wenn Sie keine Instanz angeben, wird als Ziel Ihrer App eine Instanz der öffentlichen Azure-Cloud (die Instanz der URL `https://login.onmicrosoftonline.com`) angenommen.

## <a name="application-audience"></a>Anwendungszielgruppe

Die Zielgruppe für die Anmeldung hängt von den geschäftlichen Anforderungen Ihrer Anwendung ab:
- Wenn Sie Entwickler einer Branchenanwendung (LOB) sind, werden Sie wahrscheinlich eine Einzelinstanzanwendung erstellen, die nur in Ihrer Organisation verwendet wird. In diesem Fall müssen Sie angeben, welche Organisation dies ist. Dazu können Sie die Mandanten-ID (ID in Azure Active Directory) oder einen Domänennamen verwenden, der dieser Azure Active Directory-Instanz zugeordnet ist.
- Wenn Sie ein Internetdienstanbieter sind, empfiehlt es sich, Benutzer in sämtlichen oder nur einigen Organisationen mit ihrem Geschäfts-, Schul- oder Unikonto anzumelden (mehrinstanzenfähige App). Sie können es Benutzern aber auch ermöglichen, sich mit einem persönlichen Microsoft-Konto anzumelden.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Angeben der Zielgruppe im Code bzw. der Konfiguration
Wenn Sie in Ihrem Code MSAL verwenden, geben Sie die Zielgruppe wie folgt an:
- entweder mit der Zielgruppenenumeration der Azure AD-Autorität 
- oder mit der Mandanten-ID, die Folgendes sein kann:
  - eine GUID (die ID in Azure Active Directory) – bei Anwendungen mit einem Mandanten
  - ein Domänenname, der Ihrer Azure Active Directory-Instanz zugeordnet ist – ebenfalls bei Anwendungen mit einem Mandanten
- oder einer dieser Platzhalter als Mandanten-ID anstelle der Zielgruppenenumeration der Azure AD-Autorität:
    - `organizations` bei einer Anwendung mit mehreren Mandanten
    - `consumers` für Benutzeranmeldungen ausschließlich mit persönlichen Konten
    - `common` für Benutzeranmeldungen mit Geschäfts-, Schul- und Unikonten oder persönlichen Microsoft-Konten

MSAL löst eine aussagekräftige Ausnahme aus, wenn Sie sowohl die Zielgruppe der Azure AD-Autorität als auch die Mandanten-ID angeben. 

Wenn Sie keine Zielgruppe angeben, verwendet Ihre App Azure AD als Ziel und persönliche Microsoft-Konten als eine Zielgruppe (d. h. `common`).

### <a name="effective-audience"></a>Effektive Zielgruppe
Die effektive Zielgruppe für Ihre Anwendung ist die Mindestzielgruppe (bei Überschneidungen), die Sie in Ihrer Anwendung festlegen, und die Zielgruppe, die in der Anwendungsregistrierung angegeben wurde. Tatsächlich können Sie auf der Oberfläche für die Anwendungsregistrierung ([App-Registrierung](https://aka.ms/appregistrations)) die Zielgruppe (unterstützte Kontotypen) für die Anwendung angeben. Weitere Informationen finden Sie unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md).

Derzeit stellt die einzige Möglichkeit zur Anmeldung von Benutzern bei einer Anwendung mit ausschließlich persönlichen Microsoft-Konten folgende dar:
- Legen Sie die Zielgruppe der App-Registrierung auf Geschäfts-, Schul- und Unikonten und persönliche Konten fest.
- Und legen Sie die Zielgruppe im Code bzw. der Konfiguration auf `AadAuthorityAudience.PersonalMicrosoftAccount` (oder `TenantID ` für Consumer) fest.

## <a name="client-id"></a>Client-ID
Die eindeutige Anwendungs-ID (Client-ID), die Ihrer App von Azure AD bei der Registrierung zugewiesen wurde.

## <a name="redirect-uri"></a>Umleitungs-URI
Der Umleitungs-URI gibt an, wohin der Identitätsanbieter die Sicherheitstoken zurücksenden soll. 

### <a name="redirect-uri-for-public-client-applications"></a>Umleitungs-URI für öffentliche Clientanwendungen
Für Entwickler öffentlicher Clientanwendungen mithilfe der MSAL:
- Sie müssen den ``RedirectUri`` nicht übergeben, da dies automatisch von der MSAL berechnet wird. Dieser Umleitungs-URI wird je nach Plattform auf die folgenden Werte festgelegt:

- ``urn:ietf:wg:oauth:2.0:oob`` für alle Windows-Plattformen
- ``msal{ClientId}://auth`` für Xamarin Android und iOS

Allerdings muss der Umleitungs-URI in der [App-Registrierung](https://aka.ms/appregistrations) konfiguriert werden.

![Umleitungs-URI im Portal](media/msal-client-application-configuration/redirect-uri.png)

Es ist möglich, den Umleitungs-URI mit der `RedirectUri`-Eigenschaft zu überschreiben, wenn Sie z. B. Broker verwenden. Hier sind einige Beispiele für Umleitungs-URIs in solchen Fällen:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Einzelheiten finden Sie in den Besonderheiten für Android und [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-applications"></a>Umleitungs-URI für vertrauliche Clientanwendungen
Bei Web-Apps ist der Umleitungs-URI (oder Antwort-URI) der URI, an den Azure AD der Anwendung den Token zurückgibt. Dies kann die URL der Webanwendung/Web-API sein, wenn die Vertraulichkeit entsprechend festgelegt ist.  Der Umleitungs-URI muss in der App-Registrierung registriert werden. Dies ist besonders wichtig, wenn Sie eine Anwendung bereitstellen, die Sie vorab lokal getestet haben. Sie müssen dann die Antwort-URL der bereitgestellten Anwendung im Anwendungsregistrierungsportal hinzufügen.

Für Daemon-Apps müssen Sie keinen Umleitungs-URI angeben.

## <a name="client-secret"></a>Geheimer Clientschlüssel
Der geheime Clientschlüssel für die vertrauliche Clientanwendung. Dieses Geheimnis (Anwendungskennwort) wird vom Anwendungsregistrierungsportal bereitgestellt. Bei einer Anwendungsregistrierung mit PowerShell Azure AD, PowerShell AzureRM oder der Azure-Befehlszeilenschnittstelle wird es von Azure AD bereitgestellt.

## <a name="logging"></a>Protokollierung
Die anderen Optionen ermöglichen die Protokollierung und Problembehandlung. Weitere Informationen zur Verwendung finden Sie auf der Seite [Protokollierung](msal-logging.md).

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie etwas über das [Instanziieren von Clientanwendungen mithilfe von MSAL.NET](msal-net-initializing-client-applications.md).

Erfahren Sie etwas über das [Instanziieren von Clientanwendungen mithilfe von MSAL.js](msal-js-initializing-client-applications.md).
