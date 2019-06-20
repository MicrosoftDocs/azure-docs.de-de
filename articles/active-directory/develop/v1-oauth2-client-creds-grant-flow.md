---
title: Dienst-zu-Dienst-Authentifizierung für Azure AD mit OAuth 2.0 | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Sie HTTP-Nachrichten zum Implementieren der Dienst-zu-Dienst-Authentifizierung über den Fluss zum Gewähren von OAuth 2.0-Clientanmeldeinformationen verwenden.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d734db7fbedaf3e3f3cd71c31f9391a2237f5b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545258"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Dienst-zu-Dienst-Aufrufe mit Clientanmeldeinformationen (freigegebenes Geheimnis oder Zertifikat)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Beim Fluss zur Gewährung von OAuth 2.0-Clientanmeldeinformationen kann ein Webdienst (ein *vertraulicher Client*) seine eigenen Anmeldeinformationen zum Authentifizieren verwenden, wenn ein anderer Webdienst aufgerufen wird, anstatt die Identität eines anderen Benutzers anzunehmen. In diesem Szenario ist der Client normalerweise ein Webdienst der mittleren Ebene, ein Daemondienst oder eine Website. Für ein höheres Maß an Sicherheit kann der aufrufende Dienst anstelle eines gemeinsamen Geheimnisses auch ein Zertifikat als Anmeldeinformationen verwenden.

## <a name="client-credentials-grant-flow-diagram"></a>Diagramm: Fluss zur Gewährung von Clientanmeldeinformationen
Im folgenden Diagramm wird veranschaulicht, wie der Fluss zur Gewährung von Clientanmeldeinformationen in Azure Active Directory (Azure AD) funktioniert.

![Fluss zum Gewähren von OAuth 2.0-Clientanmeldeinformationen](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Die Clientanwendung wird gegenüber dem Azure AD-Tokenausstellungs-Endpunkt authentifiziert und fordert ein Zugriffstoken an.
2. Der Azure AD-Tokenausstellungs-Endpunkt stellt das Zugriffstoken aus.
3. Das Zugriffstoken wird verwendet, um die geschützte Ressource zu authentifizieren.
4. Die Daten aus der geschützten Ressource werden an die Clientanwendung zurückgegeben.

## <a name="register-the-services-in-azure-ad"></a>Registrieren der Dienste in Azure AD
Registrieren Sie sowohl den aufrufenden Dienst als auch den empfangenden Dienst in Azure Active Directory (Azure AD). Ausführlichere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Anfordern eines Zugriffstokens
Verwenden Sie zum Anfordern eines Zugriffstokens ein HTTP POST-Element für den mandantenspezifischen Azure AD-Endpunkt.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Dienst-zu-Dienst-Zugriffstokenanforderung
Es sind zwei Fälle denkbar – je nachdem, ob die Clientanwendung durch ein gemeinsames Geheimnis oder durch ein Zertifikat geschützt wird.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Erster Fall: Zugriffstokenanforderung mit einem gemeinsamen Geheimnis
Bei Verwendung eines gemeinsamen Geheimnisses enthält eine Dienst-zu-Dienst-Zugriffstokenanforderung die folgenden Parameter:

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| grant_type |required |Gibt den angeforderten Gewährungstyp an. In einem Fluss zur Gewährung von Clientanmeldeinformationen muss der Wert **client_credentials** lauten. |
| client_id |required |Gibt die Azure AD-Client-ID des aufrufenden Webdiensts an. Klicken Sie zum Ermitteln der Client-ID der aufrufenden Anwendung im [Azure-Portal](https://portal.azure.com) auf **Azure Active Directory**, auf **App-Registrierungen** und auf die Anwendung. Die Client-ID (client_id) ist die *Anwendungs-ID*. |
| client_secret |required |Geben Sie einen Schlüssel ein, der für den aufrufenden Webdienst oder für die aufrufende Daemon-Anwendung in Azure AD registriert ist. Klicken Sie zum Erstellen eines Schlüssels im Azure-Portal auf **Azure Active Directory**, auf **App-Registrierungen** und auf die Anwendung. Klicken Sie anschließend auf **Einstellungen**, auf **Schlüssel**, und fügen Sie einen Schlüssel hinzu.  Führen Sie für dieses Geheimnis eine URL-Codierung durch, wenn Sie es bereitstellen. |
| resource |required |Geben Sie den App-ID-URI des empfangenden Webdiensts ein. Klicken Sie zum Ermitteln des App-ID-URI im Azure-Portal auf **Azure Active Directory**, auf **App-Registrierungen**, auf die Dienstanwendung und anschließend auf **Einstellungen** und **Eigenschaften**. |

#### <a name="example"></a>Beispiel
Mit dem folgenden HTTP POST-Element wird ein [Zugriffstoken](access-tokens.md) für den Webdienst https://service.contoso.com/ angefordert. Mit der `client_id` wird der Webdienst identifiziert, der das Zugriffstoken anfordert.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Zweiter Fall: Zugriffstokenanforderung mit einem Zertifikat
Eine Dienst-zu-Dienst-Zugriffstokenanforderung mit einem Zertifikat enthält die folgenden Parameter:

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| grant_type |required |Gibt den angeforderten Antworttyp an. In einem Fluss zur Gewährung von Clientanmeldeinformationen muss der Wert **client_credentials** lauten. |
| client_id |required |Gibt die Azure AD-Client-ID des aufrufenden Webdiensts an. Klicken Sie zum Ermitteln der Client-ID der aufrufenden Anwendung im [Azure-Portal](https://portal.azure.com) auf **Azure Active Directory**, auf **App-Registrierungen** und auf die Anwendung. Die Client-ID (client_id) ist die *Anwendungs-ID*. |
| client_assertion_type |required |Der Wert muss `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` sein. |
| client_assertion |required | Eine Assertion (JSON Web Token), die Sie benötigen, um das Zertifikat, das Sie als Anmeldeinformationen für Ihre Anwendung registriert haben, zu erstellen und sich damit anzumelden. Informationen zum Registrieren Ihres Zertifikats sowie zum Format der Assertion finden Sie im Abschnitt [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md).|
| resource | required |Geben Sie den App-ID-URI des empfangenden Webdiensts ein. Klicken Sie zum Ermitteln des App-ID-URI im Azure-Portal auf **Azure Active Directory**, auf **App-Registrierungen**, auf die Dienstanwendung und anschließend auf **Einstellungen** und **Eigenschaften**. |

Beachten Sie, dass die Parameter nahezu identisch mit den Parametern der Anforderung mit dem gemeinsamen geheimen Schlüssel sind. Einziger Unterschied: Anstelle des Parameters „client_secret“ werden die beiden Parameter „client_assertion_type“ und „client_assertion“ verwendet.

#### <a name="example"></a>Beispiel
Mit dem folgenden HTTP POST-Element wird ein Zugriffstoken für den Webdienst https://service.contoso.com/ mit einem Zertifikat angefordert. Mit der `client_id` wird der Webdienst identifiziert, der das Zugriffstoken anfordert.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Dienst-zu-Dienst-Zugriffstokenantwort

Eine erfolgreiche Antwort enthält eine JSON OAuth 2.0-Antwort mit den folgenden Parametern:

| Parameter | BESCHREIBUNG |
| --- | --- |
| access_token |Das angeforderte Zugriffstoken. Der aufrufende Webdienst kann dieses Token verwenden, um die Authentifizierung für den empfangenden Webdienst durchzuführen. |
| token_type |Gibt den Wert des Tokentyps an. **Bearertoken**ist der einzige Typ, den Azure AD unterstützt. Weitere Informationen zu Bearertoken finden Sie unter [OAuth 2.0-Autorisierungsframework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| expires_on |Die Uhrzeit, zu der das Zugriffstoken abläuft. Das Datum wird als Anzahl der Sekunden ab 1970-01-01T0:0:0Z UTC bis zur Ablaufzeit dargestellt. Dieser Wert wird verwendet, um die Lebensdauer von zwischengespeicherten Token zu bestimmen. |
| not_before |Der Zeitpunkt, ab dem das Zugriffstoken verwendet werden kann. Das Datum wird als Anzahl von Sekunden ab dem 1. Januar 1970 (1970-01-01T0:0:0Z UTC) bis zum Zeitpunkt der Tokengültigkeit dargestellt.|
| resource |Der App-ID-URI des empfangenden Webdiensts. |

#### <a name="example-of-response"></a>Antwortbeispiel
Das folgende Beispiel zeigt eine erfolgreiche Antwort auf eine Anforderung für ein Zugriffstoken von einem Webdienst.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Weitere Informationen
* [OAuth 2.0 in Azure AD](v1-protocols-oauth-code.md)
* [Beispiel des Dienst-zu-Dienst-Aufrufs mit einem gemeinsamen Geheimnis (C#)](https://github.com/Azure-Samples/active-directory-dotnet-daemon) und [Beispiel des Dienst-zu-Dienst-Aufrufs mit einem Zertifikat (C#)](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
