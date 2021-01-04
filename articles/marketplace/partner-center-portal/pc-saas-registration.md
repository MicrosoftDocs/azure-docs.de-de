---
title: Registrieren einer SaaS-Anwendung – Azure Marketplace
description: Erfahren Sie, wie Sie über das Azure-Portal eine SaaS-Anwendung registrieren und ein Azure Active Directory-Sicherheitstoken erhalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 39a0830806d2d9c7358d175cae703e9c81c45b02
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130016"
---
# <a name="register-a-saas-application"></a>Registrieren einer SaaS-Anwendung

In diesem Artikel erfahren Sie, wie Sie eine SaaS-Anwendung über das [Azure-Portal](https://portal.azure.com/) von Microsoft registrieren und das Zugriffstoken des Herausgebers (Azure Active Directory-Zugriffstoken) abrufen. Dieses Token wird vom Herausgeber verwendet, um die SaaS-Anwendung durch Aufrufen der SaaS-Fulfillment-APIs zu authentifizieren.  Von den Fulfillment-APIs werden die OAuth 2.0-Clientanmeldeinformationen für den Gewährungsflow auf Azure Active Directory-Endpunkten (v1.0) verwendet, um eine Dienst-zu-Dienst-Zugriffstokenanforderung durchzuführen.

Von Azure Marketplace werden keinerlei Vorgaben hinsichtlich der Authentifizierungsmethode gemacht, die von Ihrem SaaS-Dienst für Endbenutzer verwendet wird. Der folgende Flow ist nur zum Authentifizieren des SaaS-Diensts in Azure Marketplace erforderlich.

Weitere Informationen zu Azure Active Directory (AD) finden Sie unter [Was ist Authentifizierung?](../../active-directory/develop/authentication-vs-authorization.md).

## <a name="register-an-azure-ad-secured-app"></a>Registrieren einer über Azure AD geschützten App

Jede Anwendung muss zunächst in einem Azure AD-Mandanten registriert werden, um die Funktionen von Azure AD nutzen zu können. Im Rahmen dieses Registrierungsvorgangs müssen einige Details zu Ihrer Anwendung an Azure AD weitergegeben werden. Führen Sie die folgenden Schritte aus, um mit dem Azure-Portal eine neue Anwendung zu registrieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wenn Sie unter Ihrem Konto mehrere Zugriffsmöglichkeiten haben, können Sie oben rechts auf Ihr Konto klicken und Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten festlegen.
3. Klicken Sie im linken Navigationsbereich auf den **Azure Active Directory** -Dienst und dann auf **App-Registrierungen** und **Registrierung einer neuen Anwendung**.

    ![SaaS: AD-App-Registrierungen](./media/saas-offer-app-registration-v1.png)

4. Geben Sie auf der Seite „Erstellen“ die Registrierungsinformationen\' für Ihre Anwendung ein:
    -   **Name** : Geben Sie einen aussagekräftigen Anwendungsnamen ein.
    -   **Anwendungstyp** :  
        
        Wählen Sie die Option **Web-App/API** für [Clientanwendungen](../../active-directory/develop/developer-glossary.md#client-application) und [Ressourcen-/API-Anwendungen](../../active-directory/develop/developer-glossary.md#resource-server) aus, die auf einem sicheren Server installiert sind. Diese Einstellung wird für vertrauliche OAuth-[Webclients](../../active-directory/develop/developer-glossary.md#web-client) und für öffentliche [Clients auf Basis von Benutzer-Agents](../../active-directory/develop/developer-glossary.md#user-agent-based-client) verwendet.
        Außerdem kann dieselbe Anwendung sowohl einen Client als auch eine Ressource/API verfügbar machen.

        Spezifische Beispiele für Webanwendungen finden Sie in den Schnellstart-Einrichtungsanleitungen, die im Abschnitt [Erste Schritte](../../active-directory/develop/quickstart-create-new-tenant.md) des [Azure AD-Entwicklerleitfadens](../../active-directory/develop/index.yml) zur Verfügung stehen.

5. Klicken Sie abschließend auf **Registrieren**.  Ihrer neuen Anwendung wird von Azure AD eine eindeutige *Anwendungs-ID* zugewiesen. Es empfiehlt sich, eine einzelne App zu registrieren, die nur auf die API zugreift (als einzelner Mandant).

6. Navigieren Sie zum Erstellen eines geheimen Clientschlüssels zur Seite **Certificates & secrets** (Zertifikate und Geheimnisse), und klicken Sie auf **+Neuer geheimer Clientschlüssel**.  Kopieren Sie den Geheimniswert für die Verwendung in Ihrem Code.

Die **Azure AD-App-ID** ist Ihrer Herausgeber-ID zugeordnet. Achten Sie daher darauf, dass in allen Ihren Angeboten die gleiche *App-ID* verwendet wird.

>[!Note]
>Wenn ein Herausgeber in Partner Center über zwei unterschiedliche Konten verfügt, müssen auch zwei unterschiedliche Azure AD-App-IDs verwendet werden.  Von jedem Partnerkonto in Partner Center muss eine eindeutige Azure AD-App-ID für alle SaaS-Angebote verwendet werden, die über dieses Konto veröffentlicht werden.

## <a name="how-to-get-the-publishers-authorization-token"></a>Abrufen des Autorisierungstokens des Herausgebers

Nachdem Sie Ihre Anwendung registriert haben, können Sie das Autorisierungstoken des Herausgebers (Azure AD-Zugriffstoken, über den Azure AD v1-Endpunkt) programmgesteuert anfordern. Der Herausgeber muss dieses Token beim Aufrufen der verschiedenen SaaS-Fulfillment-APIs verwenden. Dieses Token ist nur eine Stunde lang gültig. 

Weitere Informationen zu diesen Token finden Sie unter [Azure Active Directory-Zugriffstoken](../../active-directory/develop/access-tokens.md).  Beachten Sie, dass im folgenden Flow das V1-Endpunkttoken verwendet wird.

### <a name="get-the-token-with-an-http-post"></a>Abrufen des Tokens mit „HTTP POST“

#### <a name="http-method"></a>HTTP-Methode

Post<br>

##### <a name="request-url"></a>*Anforderungs-URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI-Parameter*

|  Parametername    |  Erforderlich         |  BESCHREIBUNG |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  Die Mandanten-ID der registrierten AAD-Anwendung. |

##### <a name="request-header"></a>*Anforderungsheader*

|  Headername       |  Erforderlich         |  BESCHREIBUNG |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  Der Anforderung zugeordneter Inhaltstyp. Standardwert: `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Anforderungstext*

|  Eigenschaftenname     |  Erforderlich         |  BESCHREIBUNG |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  Gewährungstyp. Verwenden Sie `"client_credentials"`. |
|  `client_id`       |  True      |  Der Azure AD-App zugeordneter Client-/App-Bezeichner. |
|  `client_secret`   |  True      |  Der Azure AD-App zugeordnetes Geheimnis. |
|  `resource`        |  True      |  Zielressource, für die das Token angefordert wird. Verwenden Sie `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`, da die Marketplace-SaaS-API in diesem Fall immer die Zielressource ist. |

##### <a name="response"></a>*Antwort*

|  Name     |  type         |  BESCHREIBUNG |
|  ------   |  ---------------  | ------------ |
|  200 – OK   |  TokenResponse    |  Anforderung erfolgreich. |

##### <a name="tokenresponse"></a>*TokenResponse*

Beispiel für eine Antwort:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

Der Wert von `"access_token"` in der Antwort ist das Zugriffstoken (`<access_token>`), das Sie beim Aufrufen aller SaaS-Fulfillment- und Marketplace-Messungs-APIs übergeben.

## <a name="next-steps"></a>Nächste Schritte

In der über Azure AD geschützten App kann jetzt [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md) verwendet werden.