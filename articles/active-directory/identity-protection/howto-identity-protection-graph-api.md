---
title: Microsoft Graph-API für Azure Active Directory Identity Protection
description: In diesem Artikel erfahren Sie, wie Sie Risikoerkennungen und zugehörige Informationen in Microsoft Graph über Azure Active Directory abfragen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5367e5027bfae2fa3ed7e87a779e50e4048ba608
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861730"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Erste Schritte mit Azure Active Directory Identity Protection und Microsoft Graph

Microsoft Graph ist der einheitliche API-Endpunkt von Microsoft und stellt die [Azure Active Directory Identity Protection](./overview-identity-protection.md)-APIs zur Verfügung. Es gibt drei APIs, die Informationen zu riskanten Benutzern und Anmeldungen verfügbar machen. Mit der ersten API, **riskDetection**, können Sie in Microsoft Graph eine Liste mit Risikoerkennungen im Zusammenhang mit Benutzern und Anmeldungen sowie mit Informationen zur Erkennung abfragen. Mit der zweiten API, **riskyUsers**, können Sie in Microsoft Graph Informationen zu Benutzern abfragen, die von Identity Protection als Risiko eingestuft werden. Mit der dritten API, **signIn**, können Sie in Microsoft Graph Informationen zu Azure AD-Anmeldungen mit bestimmten Eigenschaften im Zusammenhang mit Risikozustand, Risikodetail und Risikostufe abfragen. 

In diesem Artikel erfahren Sie, wie Sie eine Verbindung mit Microsoft Graph herstellen und diese APIs abfragen. Eine ausführliche Einführung, die vollständige Dokumentation sowie Informationen zum Zugriff auf den Graph-Explorer finden Sie auf der [Website zu Microsoft Graph](https://graph.microsoft.io/) oder in der spezifischen Referenzdokumentation für diese APIs:

* [riskDetection-API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers-API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [signIn-API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Herstellen einer Verbindung mit Microsoft Graph

Zum Zugreifen auf die Identity Protection-Daten über Microsoft Graph werden vier Schritte ausgeführt:

- [Abrufen Ihres Domänennamens](#retrieve-your-domain-name)
- [Erstellen einer neuen App-Registrierung](#create-a-new-app-registration)
- [Konfigurieren von API-Berechtigungen](#configure-api-permissions)
- [Konfigurieren von gültigen Anmeldeinformationen](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Abrufen Ihres Domänennamens 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.  
1. Navigieren Sie zu **Azure Active Directory** > **Namen der benutzerdefinierten Domänen**. 
1. Notieren Sie sich die Domäne `.onmicrosoft.com`. Diese Informationen benötigen Sie in einem späteren Schritt.

### <a name="create-a-new-app-registration"></a>Erstellen einer neuen App-Registrierung

1. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **App-Registrierungen**.
1. Wählen Sie **Neue Registrierung** aus.
1. Führen Sie auf der Seite **Erstellen** die folgenden Schritte aus:
   1. Geben Sie im Textfeld **Name** einen Namen für Ihre Anwendung ein (Beispiel: die Azure AD-API für die Risikoerkennung).
   1. Wählen Sie unter **Unterstützte Kontotypen** den Kontotyp aus, der die APIs verwenden wird.
   1. Wählen Sie **Registrieren**.
1. Kopieren Sie die **Anwendungs-ID**.

### <a name="configure-api-permissions"></a>Konfigurieren von API-Berechtigungen

1. Klicken Sie in der von Ihnen erstellten **Anwendung** auf **API-Berechtigungen**.
1. Klicken Sie auf der Seite **Konfigurierte Berechtigungen** in der Symbolleiste oben auf **Berechtigung hinzufügen**.
1. Klicken Sie auf der Seite **API-Zugriff hinzufügen** auf **API auswählen**.
1. Wählen Sie auf der Seite **API auswählen** **Microsoft Graph** aus, und klicken Sie dann auf **Auswählen**.
1. Auf der Seite **API-Berechtigungen anfordern**: 
   1. Wählen Sie **Anwendungsberechtigungen**.
   1. Aktivieren Sie die Kontrollkästchen neben `IdentityRiskEvent.Read.All` und `IdentityRiskyUser.Read.All`.
   1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Klicken Sie auf **Grant admin consent for domain** (Administratoreinwilligung für Domäne gewähren). 

### <a name="configure-a-valid-credential"></a>Konfigurieren von gültigen Anmeldeinformationen

1. Klicken Sie in der von Ihnen erstellten **Anwendung** auf **Certificates & secrets** (Zertifikate und Geheimnisse).
1. Wählen Sie unter **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel**.
   1. Legen Sie für den geheimen Clientschlüssel eine **Beschreibung** und den Ablaufzeitpunkt gemäß den Richtlinien Ihrer Organisation fest.
   1. Wählen Sie **Hinzufügen**.

   > [!NOTE]
   > Wenn Sie den Schlüssel verlieren, müssen Sie diesen Abschnitt erneut aufrufen und einen neuen Schlüssel erstellen. Geben Sie den Schlüssel nicht weiter: Jeder, der diesen Schlüssel kennt, kann auf Ihre Daten zugreifen.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Authentifizieren bei Microsoft Graph und Abfragen der Identitätsrisikoerkennungs-API

Sie sollten jetzt über Folgendes verfügen:

- Den Namen der Domäne Ihres Mandanten
- Die Anwendungs-ID (Client) 
- Der geheime Clientschlüssel oder das Zertifikat 

Senden Sie für die Authentifizierung eine POST-Anforderung an `https://login.microsoft.com`. Fügen Sie die folgenden Parameter in den Anforderungstext ein:

- grant_type: „**client_credentials**“
- resource: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Ist die Anforderung erfolgreich, wird ein Authentifizierungstoken zurückgegeben.  
Erstellen Sie zum Aufrufen der API einen Header mit dem folgenden Parameter:

```
`Authorization`="<token_type> <access_token>"
```

Bei der Authentifizierung können Sie den Tokentyp und das Zugriffstoken anhand des zurückgegebenen Tokens ermitteln.

Senden Sie diesen Header als Anforderung an die folgende API-URL: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

Wenn die Anforderung erfolgreich ausgeführt wurde, wird als Antwort eine Sammlung der Identitätsrisikoerkennungen und zugehöriger Daten im OData-JSON-Format zurückgegeben. Die Antwort kann nach Bedarf analysiert und verarbeitet werden.

### <a name="sample"></a>Beispiel

Dieses Beispiel zeigt die Verwendung eines gemeinsamen geheimen Schlüssels zur Authentifizierung. In einer Produktionsumgebung wird das Speichern von geheimen Schlüsseln in Code allgemein missbilligt. Organisationen können diese Anmeldeinformationen mithilfe von verwalteten Identitäten für Azure-Ressourcen sichern. Weitere Informationen zu verwalteten Identitäten finden Sie im Artikel [Was sind verwaltete Identitäten für Azure-Ressourcen?](../managed-identities-azure-resources/overview.md).

Hier sehen Sie Beispielcode für das Authentifizieren und Aufrufen der API mit PowerShell.  
Fügen Sie einfach Ihre Client-ID, den geheimen Schlüssel und die Mandantendomäne hinzu.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Abfragen der APIs

Diese drei APIs bieten verschiedenste Gelegenheiten, um Informationen zu riskanten Benutzern und Anmeldungen in Ihrer Organisation abrufen. Im Anschluss finden Sie einige gängige Anwendungsfälle für diese APIs sowie die dazugehörigen Beispielanforderungen. Diese Abfragen können Sie mit dem weiter oben bereitgestellten Beispielcode oder über den [Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) ausführen.

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Abrufen aller Offlinerisikoerkennungen (riskDetection-API)

Mit Anmelderisiko-Richtlinien von Identity Protection können Bedingungen angewendet werden, wenn in Echtzeit ein Risiko erkannt wird. Aber was ist mit Erkennungen, die nicht in Echtzeit erfolgen? Um nachzuvollziehen, welche Erkennungen offline erfolgt sind und somit die Anmelderisiko-Richtlinie nicht ausgelöst haben, können Sie die riskDetection-API abfragen.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Abrufen aller Benutzer, die eine MFA-Überprüfung bestanden haben, die durch eine Richtlinie für riskante Anmeldungen ausgelöst wurde (riskyUsers-API)

Um die Auswirkungen zu verstehen, die risikobasierte Identity Protection-Richtlinien auf Ihre Organisation haben, können Sie alle Benutzer abfragen, die eine MFA-Überprüfung bestanden haben, die durch eine Richtlinie für riskante Anmeldungen ausgelöst wurde. Diese Informationen können Aufschluss darüber geben, welche Benutzer von Identity Protection ggf. fälschlicherweise als Risiko eingestuft wurden und welche rechtmäßigen Benutzer möglicherweise Aktionen ausführen, die die KI als riskant betrachtet.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch, Sie haben gerade Ihren ersten Aufruf an Microsoft Graph ausgeführt.  
Sie können nun Identitätsrisikoerkennungen abfragen und die Daten nach Bedarf verwenden.

Weitere Informationen zu Microsoft Graph und zum Erstellen von Anwendungen mithilfe der Graph-API finden Sie in der [Dokumentation](/graph/overview) sowie auf der Website zu [Microsoft Graph](https://developer.microsoft.com/graph). 

Verwandte Informationen

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Risikoerkennungstypen, die in Azure Active Directory Identity Protection erkannt werden](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Übersicht über Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root](/graph/api/resources/identityprotectionroot)
