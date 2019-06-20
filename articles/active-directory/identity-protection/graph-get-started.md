---
title: Microsoft Graph für Azure AD Identity Protection | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Abfragen einer Liste mit Risikoereignissen und zugehöriger Informationen in Microsoft Graph aus Azure Active Directory ausführen.
services: active-directory
keywords: Azure Active Directory Identity Protection, Risikoereignis, Sicherheitsrisiko, Sicherheitsrichtlinie, Microsoft Graph
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: sahandle
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3357cfd5e845346534f263c768b5cf6b6a38ea4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60296203"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Erste Schritte mit Azure Active Directory Identity Protection und Microsoft Graph

Microsoft Graph ist der einheitliche API-Endpunkt von Microsoft und stellt die [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)-APIs zur Verfügung. Es gibt drei APIs, die Informationen zu riskanten Benutzern und Anmeldungen verfügbar machen. Mit der ersten API, **identityRiskEvents**, können Sie in Microsoft Graph eine Liste von [Risikoereignissen](../reports-monitoring/concept-risk-events.md) und zugehörige Informationen abfragen. Mit der zweiten API, **riskyUsers**, können Sie in Microsoft Graph Informationen zu Benutzern abfragen, die von Identity Protection als Risiko eingestuft werden. Mit der dritten API, **signIn**, können Sie in Microsoft Graph Informationen zu Azure AD-Anmeldungen mit bestimmten Eigenschaften im Zusammenhang mit Risikozustand, Risikodetail und Risikostufe abfragen. In diesem Artikel erfahren Sie, wie Sie eine Verbindung mit Microsoft Graph herstellen und diese APIs abfragen. Eine ausführliche Einführung, die vollständige Dokumentation sowie Informationen zum Zugriff auf den Graph-Explorer finden Sie auf der [Website zu Microsoft Graph](https://graph.microsoft.io/) oder in der spezifischen Referenzdokumentation für diese APIs:

* [identityRiskEvents-API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers-API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn-API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)


## <a name="connect-to-microsoft-graph"></a>Herstellen einer Verbindung mit Microsoft Graph

Zum Zugreifen auf die Identity Protection-Daten über Microsoft Graph werden vier Schritte ausgeführt:

1. Rufen Sie Ihren Domänennamen ab.
2. Erstellen Sie eine neue App-Registrierung. 
3. Authentifizieren Sie sich mit diesem geheimen Schlüssel und einigen anderen Informationen bei Microsoft Graph. Sie erhalten ein Authentifizierungstoken. 
4. Verwenden Sie dieses Token, um Anforderungen an den API-Endpunkt zu senden und Identity Protection-Daten zu erhalten.

Bevor Sie beginnen, benötigen Sie Folgendes:

* Administratorrechte, um die Anwendung in Azure AD erstellen zu können
* Den Namen der Domäne Ihres Mandanten (z.B. „contoso.onmicrosoft.com“)


## <a name="retrieve-your-domain-name"></a>Abrufen Ihres Domänennamens 

1. [Melden](https://portal.azure.com) Sie sich bei Ihrem Azure-Portal als Administrator an. 

2. Klicken Sie im linken Navigationsbereich auf **Active Directory**. 
   
    ![Erstellen einer Anwendung](./media/graph-get-started/41.png)


3. Klicken Sie im Bereich **Verwalten** auf **Eigenschaften**.

    ![Erstellen einer Anwendung](./media/graph-get-started/42.png)

4. Kopieren Sie Ihren Domänennamen.


## <a name="create-a-new-app-registration"></a>Erstellen einer neuen App-Registrierung

1. Klicken Sie auf der Seite **Active Directory** im Abschnitt **Verwalten** auf **App-Registrierungen**.

    ![Erstellen einer Anwendung](./media/graph-get-started/42.png)


2. Klicken Sie im Menü am oberen Rand auf **Registrierung einer neuen Anwendung**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/43.png)

3. Führen Sie auf der Seite **Erstellen** die folgenden Schritte aus:
   
    ![Erstellen einer Anwendung](./media/graph-get-started/44.png)

    a. Geben Sie im Textfeld **Name** einen Namen für Ihre Anwendung ein (Beispiel: „AADIP-Risikoereignis-API-Anwendung“).
   
    b. Wählen Sie als **Typ** die Option **Webanwendung und/oder Web-API** aus.
   
    c. Geben Sie im Textfeld **Anmelde-URL** die URL `http://localhost` ein.

    d. Klicken Sie auf **Create**.

4. Um die Seite **Einstellungen** zu öffnen, klicken Sie in der Liste der Anwendungen auf die neu erstellte Anwendungsregistrierung. 

5. Kopieren Sie die **Anwendungs-ID**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Gewähren von Anwendungsberechtigungen zum Verwenden der API

1. Klicken Sie auf der Seite **Einstellungen** auf **Erforderliche Berechtigungen**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/15.png)

2. Klicken Sie auf der Seite **Erforderliche Berechtigungen** auf der Symbolleiste am oberen Rand auf **Hinzufügen**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/16.png)
   
3. Klicken Sie auf der Seite **API-Zugriff hinzufügen** auf **API auswählen**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/17.png)

4. Wählen Sie auf der Seite **API auswählen** **Microsoft Graph** aus, und klicken Sie dann auf **Auswählen**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/18.png)

5. Klicken Sie auf der Seite **API-Zugriff hinzufügen** auf **Berechtigungen auswählen**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/19.png)

6. Klicken Sie auf der Seite **Zugriff aktivieren** auf **Alle Informationen zu Identitätsrisikoereignissen lesen**, und klicken Sie dann auf **Auswählen**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/20.png)

7. Klicken Sie auf der Seite **API-Zugriff hinzufügen** auf **Fertig**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/21.png)

8. Klicken Sie auf der Seite **Erforderliche Berechtigungen** auf **Berechtigungen erteilen** und anschließend auf **Ja**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Abrufen eines Zugriffsschlüssels

1. Klicken Sie auf der Seite **Einstellungen** auf **Schlüssel**.
   
    ![Erstellen einer Anwendung](./media/graph-get-started/23.png)

2. Führen Sie auf der Seite **Schlüssel** die folgenden Schritte durch:
   
    ![Erstellen einer Anwendung](./media/graph-get-started/24.png)

    a. Geben Sie im Textfeld **Schlüsselbeschreibung** eine Beschreibung ein (beispielsweise *AADIP-Risikoereignis*).
    
    b. Wählen Sie als **Dauer** die Option **In 1 Jahr** aus.

    c. Klicken Sie auf **Speichern**.
   
    d. Kopieren Sie den Schlüsselwert, und fügen Sie ihn an einem sicheren Speicherort ein.   
   
   > [!NOTE]
   > Wenn Sie den Schlüssel verlieren, müssen Sie diesen Abschnitt erneut aufrufen und einen neuen Schlüssel erstellen. Geben Sie den Schlüssel nicht weiter: Jeder, der diesen Schlüssel kennt, kann auf Ihre Daten zugreifen.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Authentifizieren bei Microsoft Graph und Abfragen der Identitätsrisikoereignis-API

Sie sollten jetzt über Folgendes verfügen:

- Den Namen der Domäne Ihres Mandanten

- Die Client-ID 

- Den Schlüssel 


Senden Sie für die Authentifizierung eine POST-Anforderung an `https://login.microsoft.com`. Fügen Sie die folgenden Parameter in den Anforderungstext ein:

- grant_type: „**client_credentials**“

-  resource: `https://graph.microsoft.com`

- client_id: \<Ihre Client-ID\>

- client_secret: \<Ihr Schlüssel\>


Ist die Anforderung erfolgreich, wird ein Authentifizierungstoken zurückgegeben.  
Erstellen Sie zum Aufrufen der API einen Header mit dem folgenden Parameter:

    `Authorization`=”<token_type> <access_token>"


Bei der Authentifizierung können Sie den Tokentyp und das Zugriffstoken anhand des zurückgegebenen Tokens ermitteln.

Senden Sie diesen Header als Anforderung an die folgende API-URL: `https://graph.microsoft.com/beta/identityRiskEvents`

Ist die Anforderung erfolgreich, wird als Antwort eine Sammlung der Identitätsrisikoereignisse und zugehöriger Daten im OData-JSON-Format zurückgegeben. Die Antwort kann nach Bedarf analysiert und verarbeitet werden.

Hier sehen Sie Beispielcode für das Authentifizieren und Aufrufen der API mit PowerShell.  
Fügen Sie einfach Ihre Client-ID, den geheimen Schlüssel und die Mandantendomäne hinzu.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 

## <a name="query-the-apis"></a>Abfragen der APIs

Diese drei APIs bieten verschiedenste Gelegenheiten, um Informationen zu riskanten Benutzern und Anmeldungen in Ihrer Organisation abrufen. Im Anschluss finden Sie einige gängige Anwendungsfälle für diese APIs sowie die dazugehörigen Beispielanforderungen. Diese Abfragen können Sie mit dem weiter oben bereitgestellten Beispielcode oder über den [Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) ausführen.

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Abrufen der Ereignisse mit hohem und mittlerem Risiko (identityRiskEvents-API)

Ereignisse mit hohem und mittlerem Risiko können potenziell Anmelde- oder Benutzerrisikorichtlinien von Identity Protection auslösen. Aufgrund der mittleren oder hohen Wahrscheinlichkeit, dass der sich anmeldende Benutzer nicht der rechtmäßige Besitzer der Identität ist, sollten diese Ereignisse mit Vorrang behandelt werden. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Abrufen aller Benutzer, die eine MFA-Überprüfung bestanden haben, die durch eine Richtlinie für riskante Anmeldungen ausgelöst wurde (riskyUsers-API)

Um die Auswirkungen zu verstehen, die risikobasierte Identity Protection-Richtlinien auf Ihre Organisation haben, können Sie alle Benutzer abfragen, die eine MFA-Überprüfung bestanden haben, die durch eine Richtlinie für riskante Anmeldungen ausgelöst wurde. Diese Informationen können Aufschluss darüber geben, welche Benutzer von Identity Protection ggf. fälschlicherweise als Risiko eingestuft wurden und welche rechtmäßigen Benutzer möglicherweise Aktionen ausführen, die die KI als riskant betrachtet.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Abrufen aller riskanten Anmeldungen für einen bestimmten Benutzer (signIn-API)

Sollten Sie den Verdacht haben, dass ein Benutzer kompromittiert wurde, können Sie alle riskanten Anmeldungen dieses Benutzers abrufen, um den Risikozustand besser einschätzen zu können. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```




## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch, Sie haben gerade Ihren ersten Aufruf an Microsoft Graph ausgeführt.  
Sie können nun Identitätsrisikoereignisse abfragen und die Daten nach Bedarf verwenden.


Weitere Informationen zu Microsoft Graph und zum Erstellen von Anwendungen mithilfe der Graph-API finden Sie in der [Dokumentation](https://docs.microsoft.com/graph/overview) sowie auf der Website zu [Microsoft Graph](https://developer.microsoft.com/graph). 


Verwandte Informationen

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Arten von Risikoereignissen, die von Azure Active Directory Identity Protection erkannt werden](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Übersicht über Microsoft Graph](https://developer.microsoft.com/graph/docs)

- [Azure AD Identity Protection Service Root](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)