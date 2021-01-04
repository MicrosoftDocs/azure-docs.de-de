---
title: 'Azure AD Connect: Behandeln von Passthrough-Authentifizierungsproblemen | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Probleme bei der Azure AD-Passthrough-Authentifizierung (Azure Active Directory) behandelt werden.
services: active-directory
keywords: Probleme bei der Passthrough-Authentifizierung mit Azure AD Connect behandeln, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99da9b787bfe06bece8b8dafdafc257336dddf63
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96176187"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Behandlung von Problemen bei der Azure Active Directory-Passthrough-Authentifizierung

In diesem Artikel hilft Ihnen beim Auffinden von Informationen zur Problembehandlung von bekannten Problemen mit der Azure AD-Passthrough-Authentifizierung.

>[!IMPORTANT]
>Wenn Sie es mit Benutzeranmeldeproblemen bei der Passthrough-Authentifizierung zu tun haben, deaktivieren das Feature nicht. Deinstallieren auch keine Agents für die Passthrough-Authentifizierung ohne ein ausschließlich cloudbasiertes globales Administratorkonto, auf das ein Fallback ausgeführt werden kann. Erfahren Sie, wie Sie ein [rein cloudbasiertes Konto für den globalen Administrator hinzufügen](../fundamentals/add-users-azure-active-directory.md). Dieser Schritt ist unerlässlich und stellt sicher, dass Sie sich nicht aus Ihrem Mandanten aussperren.

## <a name="general-issues"></a>Allgemeine Probleme

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Überprüfen des Status der Funktion und der Authentifizierungs-Agents

Stellen Sie sicher, dass die Passthrough-Authentifizierung in Ihrem Mandanten immer noch **aktiviert** ist und der Status der Authentifizierungs-Agents als **aktiv** angezeigt wird, nicht als **inaktiv**. Um den Status zu überprüfen, können Sie zum **Azure AD Connect**-Blatt im [Azure Active Directory-Admin Center](https://aad.portal.azure.com/) navigieren.

![Azure Active Directory-Admin Center – Azure AD Connect-Blatt](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory-Admin Center – Passthrough-Authentifizierungs-Blatt](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Benutzerseitige Fehler bei der Anmeldung

Wenn der Benutzer sich nicht mit der Passthrough-Authentifizierung anmelden kann, wird ihm möglicherweise einer der folgenden benutzerseitigen Fehler auf der Azure AD-Anmeldeseite angezeigt: 

|Fehler|BESCHREIBUNG|Lösung
| --- | --- | ---
|AADSTS80001|Verbindung mit Active Directory kann nicht hergestellt werden.|Ensure that agent servers are members of the same AD forest as the users whose passwords need to be validated and they are able to connect to Active Directory. (Stellen Sie sicher, dass die Agent-Server Mitglieder derselben AD-Gesamtstruktur wie die Benutzer sind, deren Kennwörter überprüft werden müssen, und dass sie eine Verbindung zu Active Directory herstellen können.)  
|AADSTS8002|Bei der Verbindung mit Active Directory ist ein Timeout aufgetreten.|Check to ensure that Active Directory is available and is responding to requests from the agents. (Überprüfen Sie, ob Active Directory verfügbar ist und auf Anforderungen der Agents antwortet.)
|AADSTS80004|The username passed to the agent was not valid (Der an den Agent übergebene Benutzername war ungültig.)|Stellen Sie sicher, dass der Benutzer den richtigen Benutzernamen für die Anmeldung verwendet.
|AADSTS80005|Bei der Überprüfung ist eine unvorhersehbare WebException aufgetreten.|A transient error. (Vorübergehender Fehler.) Wiederholen Sie die Anforderung. Sollte der Fehler weiterhin auftreten, wenden Sie sich an den Microsoft-Support.
|AADSTS80007|Bei der Kommunikation mit Active Directory ist ein Fehler aufgetreten.|Suchen Sie in den Agent-Protokollen nach weiteren Informationen, und überprüfen Sie, ob Active Directory erwartungsgemäß funktioniert.

### <a name="users-get-invalid-usernamepassword-error"></a>Benutzer erhalten Fehler aufgrund eines ungültigen Benutzernamens/Kennworts. 

Dieser Fall kann eintreten, wenn sich der lokale Benutzerprinzipalname (User Principal Name, UPN) des Benutzers vom Cloud-UPN des Benutzers unterscheidet.

Überprüfen Sie zunächst, ob der Agent für die Passthrough-Authentifizierung ordnungsgemäß funktioniert:


1. Erstellen Sie ein Testkonto.  
2. Importieren Sie das PowerShell-Modul auf dem Agent-Computer:
 
 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Führen Sie den PowerShell-Befehl „Invoke“ aus: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie den für die Anmeldung verwendeten Benutzernamen und das entsprechende Kennwort ein (https://login.microsoftonline.com).

Sollte der gleiche Benutzernamen-/Kennwortfehler auftreten, funktioniert der Agent für die Passthrough-Authentifizierung ordnungsgemäß, und das Problem ist möglicherweise auf einen nicht routingfähigen lokalen UPN zurückzuführen. Weitere Informationen finden Sie unter [Konfigurieren einer alternativen Anmelde-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

> [!IMPORTANT]
> Wenn der Azure AD Connect-Server nicht in die Domäne eingebunden ist, eine Anforderung, die unter [Azure AD Connect: Voraussetzungen für die Installation](./how-to-connect-install-prerequisites.md#installation-prerequisites) erwähnt wird, tritt das Problem mit dem ungültigen Benutzernamen/Kennwort auf.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Gründe für Anmeldefehler im Azure Active Directory-Admin Center (Premium-Lizenz erforderlich)

Wenn Ihrem Mandanten eine Azure AD Premium-Lizenz zugeordnet ist, können Sie sich im [Azure Active Directory-Admin Center](https://aad.portal.azure.com/) auch den [Bericht zu den Anmeldeaktivitäten](../reports-monitoring/concept-sign-ins.md) ansehen.

![Azure Active Directory-Admin Center – Bericht zu Anmeldeaktivitäten](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navigieren Sie im [Azure Active Directory Admin Center](https://aad.portal.azure.com/) zu **Azure Active Directory** -> **Anmeldungen**, und klicken Sie auf die Anmeldeaktivität eines bestimmten Benutzers. Suchen Sie nach dem Feld **Code des Anmeldefehlers**. Ordnen Sie den Wert in diesem Feld mithilfe der folgenden Tabelle einer Ursache und einer Lösung zu:

|Anmeldefehler|Grund des Anmeldefehlers|Lösung
| --- | --- | ---
| 50144 | Das Active Directory-Kennwort des Benutzers ist abgelaufen. | Reset the user's password in your on-premises Active Directory. (Setzen Sie das Kennwort des Benutzers in Ihrem lokalen Active Directory zurück.)
| 80001 | Kein Authentifizierungs-Agent verfügbar. | Install and register an Authentication Agent. (Installieren und registrieren Sie einen Authentifizierungs-Agent.)
| 80002 | Zeitüberschreitung für die Anforderung zur Kennwortüberprüfung des Authentifizierungs-Agents. | Überprüfen Sie, ob Ihr Active Directory über den Authentifizierungs-Agent erreichbar ist.
| 80003 | Der Authentifizierungs-Agent hat eine ungültige Antwort erhalten. | If the problem is consistently reproducible across multiple users, check your Active Directory configuration. (Wenn das Problem bei mehreren Benutzer genauso reproduziert werden kann, überprüfen Sie die Active Directory-Konfiguration.)
| 80004 | In der Anmeldeanforderung wurde ein falscher Benutzerprinzipalname (UPN) verwendet. | Ask the user to sign in with the correct username. (Fordern Sie den Benutzer dazu auf, sich mit dem richtigen Benutzernamen anzumelden.)
| 80005 | Authentifizierungs-Agent: Fehler. | Transient error. (Vorübergehender Fehler.) Versuchen Sie es später noch einmal.
| 80007 | Der Authentifizierungs-Agent kann keine Verbindung mit Active Directory herstellen. | Überprüfen Sie, ob Ihr Active Directory über den Authentifizierungs-Agent erreichbar ist.
| 80010 | Der Authentifizierungs-Agent kann das Kennwort nicht entschlüsseln. | If the problem is consistently reproducible, install and register a new Authentication Agent. (Wenn das Problem konsistent reproduziert werden kann, installieren und registrieren Sie einen neuen Authentifizierungs-Agent.) And uninstall the current one. (Deinstallieren der Sie außerdem den aktuellen.) 
| 80011 | Der Authentifizierungs-Agent kann den Entschlüsselungsschlüssel nicht abrufen. | If the problem is consistently reproducible, install and register a new Authentication Agent. (Wenn das Problem konsistent reproduziert werden kann, installieren und registrieren Sie einen neuen Authentifizierungs-Agent.) And uninstall the current one. (Deinstallieren der Sie außerdem den aktuellen.)
| 80014 | Die Überprüfungsanforderung wurde erst nach Überschreiten der maximal zulässigen Zeit beantwortet. | Für den Authentifizierungs-Agent ist ein Timeout aufgetreten. Öffnen Sie ein Supportticket mit dem Fehlercode, der Korrelations-ID und dem Zeitstempel, um weitere Details zu diesem Fehler zu erhalten.

>[!IMPORTANT]
>Passthrough-Authentifizierungs-Agents authentifizieren Azure AD-Benutzer, indem sie deren Benutzernamen und Kennwörter für Active Directory durch Aufrufen der [Win32 LogonUser-API](/windows/win32/api/winbase/nf-winbase-logonusera) überprüfen. Wenn Sie in Active Directory die Einstellung „Anmeldung bei“ zum Einschränken des Anmeldezugriffs für Arbeitsstationen festgelegt haben, müssen Sie daher auch der Serverliste „Anmeldung bei“ Server hinzufügen, auf denen Passthrough-Authentifizierungs-Agents gehostet werden. Wenn Sie dies nicht tun, können sich Ihre Benutzer nicht bei Azure AD anmelden.

## <a name="authentication-agent-installation-issues"></a>Probleme bei der Installation des Authentifizierungs-Agents

### <a name="an-unexpected-error-occurred"></a>Ein unerwarteter Fehler ist aufgetreten.

[Sammeln Sie Agent-Protokolle](#collecting-pass-through-authentication-agent-logs) vom Server, und wenden Sie sich mit Ihrem Problem an den Microsoft-Support.

## <a name="authentication-agent-registration-issues"></a>Probleme bei der Registrierung des Authentifizierungs-Agents

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registration of the Authentication Agent failed due to blocked ports (Fehler bei der Registrierung des Authentifizierungs-Agents aufgrund von blockierten Ports)

Stellen Sie sicher, dass der Server, auf dem der Authentifizierungs-Agent installiert wurde, mit unseren Dienst-URLs und den [hier](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites) aufgeführten Ports kommunizieren kann.

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registration of the Authentication Agent failed due to token or account authorization errors (Fehler bei der Registrierung des Authentifizierungs-Agents aufgrund von Token- oder Kontoautorisierungsfehlern)

Stellen Sie sicher, dass Sie ein ausschließlich für die Cloud geltendes globales Administratorkonto für alle Vorgänge zur Installation und Registrierung von Azure AD Connect- oder eigenständigen Authentifizierungs-Agents verwenden. Es gibt ein bekanntes Problem mit MFA-fähigen globalen Administratorkonten. Deaktivieren Sie als Umgehungsmaßnahme vorübergehend MFA (nur bis zum Abschluss der Vorgänge).

### <a name="an-unexpected-error-occurred"></a>Ein unerwarteter Fehler ist aufgetreten.

[Sammeln Sie Agent-Protokolle](#collecting-pass-through-authentication-agent-logs) vom Server, und wenden Sie sich mit Ihrem Problem an den Microsoft-Support.

## <a name="authentication-agent-uninstallation-issues"></a>Probleme bei der Deinstallation von Authentifizierungs-Agents

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Warnmeldung bei der Deinstallation von Azure AD Connect

Wenn Sie die Passthrough-Authentifizierung für Ihren Mandanten aktiviert haben und versuchen, Azure AD Connect zu deinstallieren, wird die folgende Warnmeldung angezeigt: „Users will not be able to sign-in to Azure AD unless you have other Pass-through Authentication agents installed on other servers“ (Benutzer können sich nur dann bei Azure AD anmelden, wenn Sie andere Passthrough-Authentifizierungs-Agents auf anderen Servern installiert haben).

Stellen Sie sicher, dass das Setup [hoch verfügbar](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) ist, bevor Sie Azure AD Connect deinstallieren, um eine Unterbrechung von Benutzeranmeldungen zu vermeiden.

## <a name="issues-with-enabling-the-feature"></a>Probleme bei der Aktivierung des Features

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Enabling the feature failed because there were no Authentication Agents available (Beim Aktivieren des Features ist ein Fehler aufgetreten, weil keine Authentifizierungs-Agents verfügbar waren)

Es muss mindestens ein Authentifizierungs-Agent aktiv sein, damit die Passthrough-Authentifizierung in Ihrem Mandanten aktiviert werden kann. Sie können einen Authentifizierungs-Agent über Azure AD Connect oder als eigenständigen Authentifizierungs-Agent installieren.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Enabling the feature failed due to blocked ports (Beim Aktivierung des Features ist aufgrund blockierter Ports ein Fehler aufgetreten)

Stellen Sie sicher, dass der Server, auf dem Azure AD Connect installiert ist, mit unseren Dienst-URLs und den [hier](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites) aufgeführten Ports kommunizieren kann.

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Enabling the feature failed due to token or account authorization errors (Beim Aktivieren des Features ist aufgrund von Token- oder Kontoautorisierungsfehlern ein Fehler aufgetreten)

Stellen Sie sicher, dass Sie ein ausschließlich für die Cloud geltendes globales Administratorkonto verwenden, wenn Sie das Feature aktivieren. Es gibt ein bekanntes Problem mit MFA-fähigen (Multi-Factor Authentication) globalen Administratorkonten. Deaktivieren Sie als Umgehungsmaßnahme vorübergehend MFA (nur bis zum Abschluss der Vorgänge).

## <a name="collecting-pass-through-authentication-agent-logs"></a>Sammeln von Protokollen von Passthrough-Authentifizierungs-Agent

Je nach Problem müssen Sie an verschiedenen Stellen nach Protokollen von Passthrough-Authentifizierungs-Agents suchen.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect-Protokolle

Prüfen Sie bei Fehlern im Zusammenhang mit der Installation die Azure AD Connect-Protokolle unter **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Ereignisprotokolle von Authentifizierungs-Agents

Öffnen Sie bei Fehlern in Zusammenhang mit dem Authentifizierungs-Agent die Ereignisanzeige auf dem Server unter **Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**, und prüfen Sie sie.

Aktivieren Sie zur detaillierten Analyse das Sitzungsprotokoll. (Klicken Sie mit der rechten Maustaste in die Anwendung „Ereignisanzeige“, um diese Option zu finden.). Führen Sie den Authentifizierungs-Agent im Normalbetrieb nicht mit diesem Protokoll aus. Verwenden Sie es ausschließlich zur Problembehandlung. Die Protokollinhalte werden nur angezeigt, nachdem das Protokoll wieder deaktiviert wird.

Das Ereignismanifest des PTA-Agents finden Sie [hier](https://msazure.visualstudio.com/One/_git/AD-AppProxy?path=%2Fsrc%2FProduct%2FMUC%2FPTADiagnosticsResource%2FPTADiagnosticsResource%2FPTAConnectorDiagnosticsResource%2FPTAConnectorEventManifest.man&_a=contents&version=GBmaster).

### <a name="detailed-trace-logs"></a>Ausführliche Ablaufverfolgungsprotokolle

Um Benutzeranmeldefehler zu beheben, suchen Sie unter **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\** nach Ablaufverfolgungsprotokollen. Diese Protokolle enthalten die Gründe, warum eine bestimmte Benutzeranmeldung mittels der Passthrough-Authentifizierungsfunktion fehlgeschlagen ist. Diese Fehler werden auch den Gründen für Anmeldefehler zugeordnet, die in der obigen Tabelle mit den Gründen für Anmeldefehler aufgeführt sind. Es folgt ein Beispiel für einen Protokolleintrag:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Sie können erklärende Details zum Fehler (im obigen Beispiel „1328“) abrufen, indem Sie die Eingabeaufforderung öffnen und den folgenden Befehl ausführen (Hinweis: Ersetzen Sie „1328“ durch die tatsächliche Fehlernummer, die in Ihren Protokollen angezeigt wird):

`Net helpmsg 1328`

![Passthrough-Authentifizierung](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Domänencontrollerprotokolle

Weitere Informationen finden Sie auch in den Sicherheitsprotokollen Ihrer Domänencontroller, sofern die Überwachungsprotokollierung aktiviert ist. Eine einfache Möglichkeit zum Abfragen von Anmeldeanforderungen, die über Passthrough-Authentifizierungs-Agents gesendet werden, ist Folgende:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Leistungsüberwachungsindikatoren

Eine weitere Möglichkeit zum Überwachen des Authentifizierung-Agents ist das Nachverfolgen bestimmter Leistungsüberwachungsindikatoren auf jedem Server, auf dem der Authentifizierung-Agent installiert ist. Verwenden Sie die folgenden globalen Indikatoren **# PTA authentications**, **#PTA failed authentications** und **#PTA successful authentications**) und Fehlerzähler ( **# PTA authentication errors**):

![Passthrough-Authentifizierungs-Leistungsüberwachungsindikatoren](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Die Passthrough-Authentifizierung bietet mit mehreren Authentifizierungs-Agents Hochverfügbarkeit, jedoch _keinen_ Lastenausgleich. Abhängig von Ihrer Konfiguration erhalten _nicht_ alle Ihre Authentifizierungs-Agents ungefähr die _gleiche_ Anzahl von Anforderungen. Es ist möglich, dass ein bestimmter Authentifizierungs-Agent überhaupt keinen Datenverkehr empfängt.
