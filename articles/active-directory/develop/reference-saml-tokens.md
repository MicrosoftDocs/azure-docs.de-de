---
title: Referenz zu Ansprüchen in SAML 2.0-Token | Azure
titleSuffix: Microsoft identity platform
description: Referenz zu Ansprüchen, in der Details zu den Ansprüchen beschrieben werden, die in von Microsoft Identity-Plattform ausgegebenen SAML 2.0-Token enthalten sind. Die entsprechenden JWT-Ansprüche werden ebenfalls behandelt.
author: kenwith
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/09/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: aaddev
ms.openlocfilehash: 9c3132985866a4c245984ef632107c05ca1b3350
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348381"
---
# <a name="saml-token-claims-reference"></a>Referenz zu Ansprüchen in SAML-Token

Microsoft Identity Platform stellt bei der Verarbeitung der einzelnen Authentifizierungsflows verschiedene Arten von Sicherheitstoken aus. In diesem Dokument werden das Format, die Sicherheitsmerkmale und der Inhalt von SAML 2.0-Token beschrieben.

## <a name="claims-in-saml-tokens"></a>Ansprüche in SAML-Token

> [!div class="mx-codeBreakAll"]
> | Name | Entsprechender JWT-Anspruch | BESCHREIBUNG | Beispiel |
> | --- | --- | --- | ------------|
> |Zielgruppe | `aud` |Der vorgesehene Empfänger des Tokens. Die Anwendung, die das Token empfängt, muss prüfen, ob der "Audience"-Wert ordnungsgemäß ist, und alle Token ablehnen, die für eine andere Zielgruppe vorgesehen sind. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Authentifizierungszeitpunkt | |Erfasst Datum und Uhrzeit der Authentifizierung. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
> |Authentifizierungsmethode | `amr` |Gibt an, wie der Antragsteller des Tokens authentifiziert wurde. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |First Name (Vorname) | `given_name` |Gibt den Vornamen des Benutzers entsprechend der Festlegung im Azure AD-Benutzerobjekt an. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Gruppen | `groups` |Enthält die Objekt-IDs, die die Gruppenmitgliedschaften des Antragstellers darstellen. Diese Werte sind eindeutig (siehe „Object ID“) und eignen sich zum sicheren Verwalten des Zugriffs, z.B. für das Erzwingen der Autorisierung für den Zugriff auf eine Ressource. Die im Anspruch "Groups" enthaltenen Gruppen werden über die "GroupMembershipClaims"-Eigenschaft des Anwendungsmanifests anwendungsbezogen konfiguriert. Mit dem Wert „Null“ werden alle Gruppen ausgeschlossen. Beim Wert „SecurityGroup“ sind nur Mitglieder von Active Directory-Sicherheitsgruppen enthalten. Beim Wert „All“ sind sowohl Sicherheitsgruppen als auch Microsoft 365-Verteilerlisten enthalten. <br><br> **Hinweise**: <br> Wenn die Anzahl von Gruppen, denen der Benutzer angehört, ein Limit übersteigt (150 für SAML, 200 für JWT), wird den Anspruchsquellen, die auf den Graph-Endpunkt mit der Liste der Gruppen für den Benutzer verweisen, ein Überschreitungsanspruch hinzugefügt. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Groups Overage Indicator | `groups:src1` | Bei Tokenanforderungen ohne Längenbeschränkung, die aber dennoch zu groß für das Token sind, ist für den Benutzer ein Link zur vollständigen Liste der Gruppen enthalten. Bei SAML wird er als neuer Anspruch anstelle des `groups`-Anspruchs hinzugefügt. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Identitätsanbieter | `idp` |Der Identitätsanbieter, der den Antragsteller des Tokens authentifiziert hat. Dieser Wert ist identisch mit dem Wert des "Issuer "-Anspruchs, es sei denn, das Benutzerkonto gehört zu einem anderen Mandanten als der Aussteller. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt (Ausgestellt um) | `iat` |Speichert die Uhrzeit, zu der das Token ausgestellt wurde. Er wird häufig verwendet, um die Aktualität von Token zu messen. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Issuer (Aussteller) | `iss` |Identifiziert den Sicherheitstokendienst (Security Token Service, STS), der das Token erstellt und zurückgibt. Bei den Token, die Azure AD zurückgibt, ist der Aussteller "sts.windows.net". Die GUID im "Issuer"-Anspruch ist die Mandanten-ID des Azure AD-Verzeichnisses. Die Mandanten-ID ist ein unveränderlicher und zuverlässiger Bezeichner des Verzeichnisses. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Last Name (Nachname) | `family_name` |Gibt den Nachnamen des Benutzers entsprechend der Definition im Azure AD-Benutzerobjekt an. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Name | `unique_name` |Ein lesbarer Wert, der Aufschluss über den Antragsteller des Tokens gibt. Es wird nicht garantiert, dass der Wert innerhalb eines Mandanten eindeutig ist, weshalb er nur zu Anzeigezwecken dient. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |ObjectID | `oid` |Enthält einen eindeutigen Bezeichner eines Objekts in Azure AD. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Verwenden Sie die Objekt-ID zum Identifizieren eines Objekts in Abfragen von Azure AD. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Rollen | `roles` |Stellt alle Anwendungsrollen dar, die dem Antragsteller direkt und indirekt über eine Gruppenmitgliedschaft zugewiesen wurden und zum Erzwingen der rollenbasierten Zugriffssteuerung verwendet werden können. Anwendungsrollen werden über die `appRoles` -Eigenschaft des Anwendungsmanifests anwendungsspezifisch definiert. Die `value` -Eigenschaft der einzelnen Anwendungsrollen ist der Wert, der im Roles-Anspruch enthalten ist. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Subject | `sub` |Identifiziert den Prinzipal, für den das Token Informationen bestätigt (beispielsweise der Benutzer einer Anwendung). Dieser Wert ist unveränderlich und kann nicht neu zugewiesen oder wiederverwendet werden. Daher kann er für die sichere Durchführung von Autorisierungsüberprüfungen verwendet werden. Da der Antragsteller immer in den Token vorhanden ist, die Azure AD ausstellt, wird die Nutzung dieses Werts in einem allgemeinen Autorisierungssystem empfohlen. <br> `SubjectConfirmation` ist kein Anspruch. Mit dem Element wird beschrieben, wie der Antragsteller des Tokens verifiziert wird. `Bearer` gibt an, dass der Antragsteller durch den Besitz des Tokens bestätigt wird. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |Mandanten-ID | `tid` |Ein unveränderlicher, nicht wieder verwendbarer Bezeichner, der den Verzeichnismandanten identifiziert, welcher das Token ausgestellt hat. Sie können diesen Wert verwenden, um auf mandantenspezifische Verzeichnisressourcen in einer mehrinstanzenfähigen Anwendung zugreifen. Sie können z.B. diesen Wert verwenden, um den Mandanten in einem Aufruf der Graph-API zu identifizieren. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Tokengültigkeitsdauer | `nbf`, `exp` |Das Zeitintervall, für das ein Token gültig ist. Der Dienst, der das Token überprüft, muss bestätigen, dass das aktuelle Datum innerhalb der Gültigkeitsdauer des Tokens liegt. Andernfalls wird das Token abgelehnt. Der Dienst kann einen Puffer von bis zu fünf Minuten über die Tokengültigkeitsdauer hinaus zulassen, um Differenzen bei der Istzeit (d. h. den zeitlichen Versatz) zwischen Azure AD und dem Dienst zu berücksichtigen. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>SAML-Beispieltoken

Dies ist ein Beispiel eines typischen SAML-Tokens.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
    <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
    </t:Lifetime>
    <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
            <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
    </wsp:AppliesTo>
    <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
            <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
            <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
                <ds:SignedInfo>
                    <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                    <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
                    <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                        <ds:Transforms>
                            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                        </ds:Transforms>
                        <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                        <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
                    </ds:Reference>
                </ds:SignedInfo>
                <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
                <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
                    <X509Data>
                        <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
                    </X509Data>
                </KeyInfo>
            </ds:Signature>
            <Subject>
                <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
                <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
            </Subject>
            <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
                <AudienceRestriction>
                    <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
                </AudienceRestriction>
            </Conditions>
            <AttributeStatement>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
                    <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
                    <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
                    <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
                    <AttributeValue>Admin</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
                    <AttributeValue>Sample</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
                    <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
                    <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
                    <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
                    <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
                    <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
                    <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
                    <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
                    <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
                    <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
                    <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
                    <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
                    <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
                    <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
                    <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
                </Attribute>
            </AttributeStatement>
            <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
                <AuthnContext>
                    <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
                </AuthnContext>
            </AuthnStatement>
        </Assertion>
    </t:RequestedSecurityToken>
    <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedAttachedReference>
    <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedUnattachedReference>
    <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
    <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
    <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
</t:RequestSecurityTokenResponse>
```

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie mehr über die Verwaltung der Richtlinie für die Tokengültigkeitsdauer über die Microsoft Graph-API erfahren möchten, lesen Sie die [Übersicht über die Azure AD-Richtlinienressource](/graph/api/resources/policy).
* Fügen Sie den Token für Ihre Anwendung [benutzerdefinierte und optionale](active-directory-optional-claims.md) Ansprüche hinzu.
* Verwenden Sie [einmaliges Anmelden (SSO) mit SAML](single-sign-on-saml-protocol.md).
* Verwenden Sie das [Azure-SAML-Protokoll für einmaliges Abmelden](single-sign-out-saml-protocol.md).
