---
title: Leitfaden für die Migration von ADAL zu MSAL (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Ihre Java-App mit Azure ADAL (Active Directory Authentication Library) zu MSAL (Microsoft Authentication Library) migrieren.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 620f77655f8281919ba0831a7e53af8cd28bc5f2
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583992"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Leitfaden für die Migration von ADAL zu MSAL für Java

In diesem Artikel werden die Änderungen beschrieben, die Sie durchführen müssen, um eine App, die ADAL (Azure Active Directory Authentification Library) verwendet, zu MSAL (Microsoft Authentication Library) zu migrieren.

Sowohl die Microsoft-Authentifizierungsbibliothek für Java (MSAL4J) als auch die Azure AD-Authentifizierungsbibliothek für Java (ADAL4J) werden zum Authentifizieren von Azure AD-Entitäten und zum Anfordern von Token von Azure AD verwendet. Bisher haben die meisten Entwickler die Azure AD für Entwickler-Plattform (v1.0) genutzt, um Azure AD-Identitäten (Geschäfts-, Schul- und Unikonten) zu authentifizieren, indem sie unter Verwendung der Azure AD-Authentifizierungsbibliothek (ADAL) Token anforderten.

MSAL bietet folgende Vorteile:

- Da das neue Microsoft Identity Platform verwendet wird, können Sie eine größere Gruppe von Microsoft-Identitäten wie Azure AD-Identitäten, Microsoft-Konten sowie Social Media- und lokale Konten über Azure AD B2C (Business to Consumer) authentifizieren.
- Ihre Benutzer erhalten die besten Funktionen für einmalige Anmelden (Single Sign-On, SSO).
- Ihre Anwendung kann inkrementelle Zustimmung aktivieren, und Unterstützung des bedingten Zugriffs ist einfacher.

MSAL für Java ist die empfohlene Authentifizierungsbibliothek für die Verwendung mit Microsoft Identity Platform. Für ADAL4J werden keine neuen Features implementiert. Der Schwerpunkt liegt künftig auf der Verbesserung von MSAL.

## <a name="differences"></a>Unterschiede

Wenn Sie bereits mit dem Azure AD für Entwickler (v1.0)-Endpunkt und ADAL4J gearbeitet haben, könnte Sie der Artikel [Neuerungen in Microsoft Identity Platform](../azuread-dev/azure-ad-endpoint-comparison.md) interessieren.

## <a name="scopes-not-resources"></a>Geltungsbereiche im Gegensatz zu Ressourcen

ADAL4J ruft Token für Ressourcen auf, während MSAL für Java Token für Geltungsbereiche abruft. Eine Reihe von Klassen in MSAL für Java erfordern einen scopes-Parameter. Bei diesem Parameter handelt es sich um eine Liste von Zeichenfolgen, die die gewünschten Berechtigungen und Ressourcen deklarieren, die angefordert werden. Beispiele für Geltungsbereiche finden Sie unter [Geltungsbereiche von Microsoft Graph](/graph/permissions-reference).

Sie können der Ressource das Bereichssuffix`/.default` hinzufügen, damit Sie Ihre Apps besser von ADAL zu MSAL migrieren können. Beispielsweise entspricht dem Ressourcenwert `https://graph.microsoft.com` der entsprechende Bereichswert `https://graph.microsoft.com/.default`.  Wenn sich die Ressource nicht im URL-Format befindet, aber eine Ressourcen-ID im Format `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` vorliegt, können Sie weiterhin den Bereichswert `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` verwenden.

Weitere Informationen zu den verschiedenen Bereichstypen finden Sie in den Artikeln [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](./v2-permissions-and-consent.md) und [Geltungsbereiche für eine Web-API, die v1.0-Token akzeptiert](./msal-v1-app-scopes.md).

## <a name="core-classes"></a>Core-Klassen

In ADAL4J stellt die `AuthenticationContext`-Klasse Ihre Verbindung mit dem Sicherheitstokendienst (STS) oder Autorisierungsserver über eine Autorität dar. MSAL für Java hingegen wurde mit dem Fokus auf Clientanwendungen konzipiert. Es werden zwei separate Klassen bereitgestellt: `PublicClientApplication` und `ConfidentialClientApplication`, um Clientanwendungen darzustellen.  Die letztere Klasse, `ConfidentialClientApplication`, stellt eine Anwendung dar, die ein Geheimnis (z. B. einen Anwendungsbezeichner) für eine Daemon-App verwaltet.

In der folgenden Tabelle wird gezeigt, wie ADAL4J-Funktionen den neuen MSAL für Java-Funktionen zugeordnet sind:

| ADAL4J-Methode| MSAL4J-Methode|
|------|-------|
|acquireToken(String-Ressource, ClientCredential-Anmeldeinformationen, AuthenticationCallback-Rückruf) | acquireToken(ClientCredentialParameters)|
|acquireToken(String-Ressource, ClientAssertion-Assertion, AuthenticationCallback-Rückruf)|acquireToken(ClientCredentialParameters)|
|acquireToken(String-Ressource, AsymmetricKeyCredential-Anmeldeinformationen, AuthenticationCallback-Rückruf)|acquireToken(ClientCredentialParameters)|
|acquireToken(String-Ressource, String-ClientID, String-Benutzername, String-Kennwort, AuthenticationCallback-Rückruf)| acquireToken(UsernamePasswordParameters)|
|acquireToken(String-Ressource, String-ClientID, String-Benutzername, String-Kennwort=null, AuthenticationCallback-Rückruf)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(String-Ressource, UserAssertion-Benutzerassertion, ClientCredential-Anmeldeinformationen, AuthenticationCallback-Rückruf)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() und acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount anstelle von IUser

In ADAL4J wurden Benutzer bearbeitet. Ein Benutzer stellt zwar einen einzelnen menschlichen oder softwarebasierten Agent dar, er kann jedoch im Microsoft-Identitätssystem über ein Konto oder mehrere Konten verfügen. Ein Benutzer kann beispielsweise mehrere Azure AD-, Azure AD B2C- oder persönlicher Microsoft-Konten verfügen.

MSAL für Java definiert das Konzept „Konto“ über die `IAccount`-Schnittstelle. Dies ist eine Breaking Change gegenüber ADAL4J. Dies ist jedoch von Vorteil, da der Umstand berücksichtigt wird, dass ein und derselbe Benutzer über mehrere Konten verfügen kann, vielleicht sogar in verschiedenen Azure AD-Verzeichnissen. MSAL für Java bietet bessere Informationen in Gastszenarien, da Informationen zum Stammkonto bereitgestellt werden.

## <a name="cache-persistence"></a>Cachepersistenz

ADAL4J bot keine Unterstützung des Tokencaches.
In MSAL für Java wird ein [Tokencache](msal-acquire-cache-tokens.md) eingeführt, um das Verwalten der Lebensdauer von Token zu vereinfachen. Dazu wird werden abgelaufene Token (soweit möglich) automatisch aktualisiert, und (soweit möglich) werden unnötige Aufforderungen zur Eingabe von Anmeldeinformationen für den Benutzer vermieden.

## <a name="common-authority"></a>Common-Autorität

Wenn Sie in v1.0 die `https://login.microsoftonline.com/common`-Autorität verwenden, können sich Benutzer mit einem beliebigen AAD-Konto (Azure Active Directory) für eine beliebige Organisation anmelden.

Bei Verwendung der `https://login.microsoftonline.com/common`-Autorität in v2.0 können sich Benutzer mit einer beliebigen AAD-Organisation oder sogar einem persönlichen Microsoft-Konto (MSA) anmelden. Wenn Sie in MSAL für Java die Anmeldung auf ein beliebiges AAD-Konto beschränken möchten, verwenden Sie die `https://login.microsoftonline.com/organizations`-Autorität(das Verhalten entspricht dem bei ADAL4J). Legen Sie zum Angeben einer Autorität den `authority`-Parameter in der [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html)-Methode fest, wenn Sie die `PublicClientApplication`-Klasse erstellen.

## <a name="v10-and-v20-tokens"></a>v1.0- und v2.0-Token

Der (von ADAL verwendete) v1.0-Endpunkt gibt nur v1.0-Token aus.

Der v2.0-Endpunkt (verwendet von MSAL) kann v1.0- und v2.0-Token ausgeben. Mithilfe einer Eigenschaft im Anwendungsmanifest der Web-API können Entwickler auswählen, welche Tokenversion akzeptiert wird. Informationen finden Sie unter `accessTokenAcceptedVersion` in der Referenzdokumentation zum [Anwendungsmanifest](./reference-app-manifest.md).

Weitere Informationen zu v1.0- und v2.0-Token finden Sie im Thema zu [Azure Active Directory-Zugriffstoken](./access-tokens.md).

## <a name="adal-to-msal-migration"></a>Migration von ADAL zu MSAL

In ADAL4J wurden die Aktualisierungstoken verfügbar gemacht, sodass sie von Entwicklern zwischengespeichert werden konnten. Anschließend verwendeten sie `AcquireTokenByRefreshToken()`, um Lösungen wie das Implementieren von Diensten mit langer Ausführungszeit zu implementieren, die Dashboards im Namen des Benutzers aktualisieren, wenn der Benutzer nicht mehr verbunden ist.

MSAL für Java stellt aus Sicherheitsgründen keine Aktualisierungstoken bereit. Stattdessen übernimmt MSAL das Aktualisieren von Token für Sie.

MSAL für Java verfügt über eine API, mit denen Sie mit ADAL4j abgerufene Aktualisierungstoken in die Clientanwendung migrieren können: [acquireToken(RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Dank dieser Methode können Sie das zuvor verwendete Aktualisierungstoken zusammen mit beliebigen Geltungsbereichen (Ressourcen) bereitstellen. Das Aktualisierungstoken wird durch ein neues ersetzt und für die Verwendung durch die Anwendung zwischengespeichert.

Der folgende Codeausschnitt enthält Migrationscode in einer vertraulichen Clientanwendung:

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult` gibt ein Zugriffstoken und ein ID-Token zurück, während Ihr neues Aktualisierungstoken im Cache gespeichert wird.
Die Anwendung enthält nun auch ein IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Rufen Sie zum Verwenden der nun im Cache enthaltenen Token Folgendes auf:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
