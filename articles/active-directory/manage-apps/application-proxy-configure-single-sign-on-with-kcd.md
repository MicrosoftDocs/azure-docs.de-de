---
title: Einmaliges Anmelden mit Anwendungsproxy | Microsoft Docs
description: Erläutert das Bereitstellen von einmaligem Anmelden mit Azure AD-Anwendungsproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 545906af882be6e53297bf7a9ff2cd12e86d55f0
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859622"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Eingeschränkte Delegierung von Kerberos für die einmalige Anmeldung zu Ihren Apps mit dem Anwendungsproxy

Sie können das einmalige Anmelden für lokale Anwendungen, die über den Anwendungsproxy veröffentlicht und mit der integrierte Windows-Authentifizierung gesichert werden, bereitstellen. Diese Anwendungen erfordern ein Kerberos-Ticket für den Zugriff. Der Anwendungsproxy verwendet die eingeschränkte Kerberos-Delegierung (KCD), um diese Anwendungen zu unterstützen. 

Sie können für Anwendungen, die die integrierte Windows-Authentifizierung (IWA) verwenden, einmaliges Anmelden wie folgt aktivieren: Erteilen Sie einem Anwendungsproxy-Connector in Active Directory die Berechtigung, die Identität von Benutzern anzunehmen. Die Connectors verwenden diese Berechtigung zum Senden und Empfangen von Token im Auftrag dieser.

## <a name="how-single-sign-on-with-kcd-works"></a>So funktioniert das einmalige Anmelden mit KCD
Dieses Diagramm erläutert die Vorgänge, die beim Zugriff eines Benutzers auf eine lokale Anwendung mit IWA ablaufen.

![Flussdiagramm für Microsoft AAD-Authentifizierung](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Der Benutzer gibt die URL ein, um über den Anwendungsproxy auf die lokale Anwendung zuzugreifen.
2. Der Anwendungsproxy leitet die Anforderung zur Vorauthentifizierung an Azure AD-Authentifizierungsdienste weiter. Zu diesem Zeitpunkt wendet Azure AD alle gültigen Authentifizierungs- und Autorisierungsrichtlinien an, wie z. B. mehrstufige Authentifizierung. Nachdem der Benutzer überprüft wurde, erstellt Azure AD ein Token und sendet es an den Benutzer.
3. Der Benutzer übergibt das Token an den Anwendungsproxy.
4. Der Anwendungsproxy überprüft das Token und ruft den Benutzerprinzipalnamen (UPN) daraus ab. Dann überträgt der Connector den UPN per Pull und den Dienstprinzipalnamen (SPN) über einen zweifach authentifizierten, sicheren Kanal.
5. Der Connector führt eine Aushandlung der eingeschränkten Kerberos-Delegierung (KCD) mit dem lokalen AD aus und nimmt dabei die Identität des Benutzers an, um ein Kerberos-Token für die Anwendung zu erhalten.
6. Active Directory sendet das Kerberos-Token für die Anwendung an den Connector.
7. Der Connector sendet die ursprüngliche Anforderung an den Anwendungsserver und verwendet dabei das von AD empfangene Kerberos-Token.
8. Die Anwendung sendet die Antwort an den Connector, die dann an den Anwendungsproxydienst und schließlich an den Benutzer zurückgegeben wird.

## <a name="prerequisites"></a>Voraussetzungen
Vergewissern Sie sich vor Ihren ersten Schritten mit dem einmaligen Anmelden für IWA-Anwendungen, dass Ihre Umgebung über folgende Einstellungen und Konfigurationen verfügt:

* Ihre Apps (beispielsweise SharePoint-Web-Apps) sind für die Verwendung der integrierten Windows-Authentifizierung konfiguriert. Weitere Informationen finden Sie unter [Aktivieren der Unterstützung für die Kerberos-Authentifizierung](https://technet.microsoft.com/library/dd759186.aspx). Weitere Informationen zu SharePoint finden Sie unter [Planen der Kerberos-Authentifizierung in SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Alle Ihre Apps verfügen über [Dienstprinzipalnamen](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Der Server, auf dem der Connector ausgeführt wird, und der Server, auf dem die App ausgeführt wird, gehören einer Domäne an und sind Teil der gleichen Domäne bzw. der vertrauenswürdigen Domänen. Weitere Informationen zum Domänenbeitritt finden Sie unter [Hinzufügen eines Computers zu einer Domäne](https://technet.microsoft.com/library/dd807102.aspx).
* Der Server, auf dem der Connector ausgeführt wird, verfügt über Lesezugriff auf das Attribut „TokenGroupsGlobalAndUniversal“ für Benutzer. Hierbei handelt es sich um eine Standardeinstellung, die unter Umständen im Rahmen einer Sicherheitshärtung für die Umgebung geändert wurde.

### <a name="configure-active-directory"></a>Konfigurieren von Active Directory
Die Active Directory-Konfiguration variiert in Abhängigkeit davon, ob Ihr Anwendungsproxy-Connector und der Anwendungsserver sich in derselben Domäne befinden oder nicht.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Connector und Anwendungsserver in der gleichen Domäne
1. Wechseln Sie in Active Directory zu **Extras** > **Benutzer und Computer**.
2. Wählen Sie den Server aus, der den Connector ausführt.
3. Klicken Sie mit der rechten Maustaste, und wählen Sie **Eigenschaften** > **Delegierung** aus.
4. Wählen Sie **Computer nur bei Delegierungen angegebener Dienste vertrauen**. 
5. Wählen Sie die Option **Beliebiges Authentifizierungsprotokoll verwenden** aus.
6. Fügen Sie unter **Dienste, für die dieses Konto delegierte Anmeldeinformationen verwenden kann** den Wert für die Dienstprinzipalnamen-Identität (SPN) des Anwendungsservers hinzu. Auf diese Weise kann der Anwendungsproxy-Connector die Identität von Benutzern in AD für die Anwendungen annehmen, die in der Liste definiert sind.

   ![Screenshot des Connector-SVR-Eigenschaftenfensters](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Connector und Anwendungsserver in unterschiedlichen Domänen
1. Eine Liste der Voraussetzungen für das domänenübergreifende Arbeiten mit KCD finden Sie unter [Eingeschränkte Kerberos-Delegierung über Domänengrenzen hinweg](https://technet.microsoft.com/library/hh831477.aspx).
2. Verwenden Sie die `principalsallowedtodelegateto`-Eigenschaft auf dem Connectorserver, damit der Anwendungsproxy den Verbindungsserver delegieren kann. Der Anwendungsserver ist `sharepointserviceaccount` und der delegierende Server `connectormachineaccount`. Verwenden Sie diesen Code für Windows 2012 R2 als Beispiel:

```powershell
$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
```

`sharepointserviceaccount` kann das SPS-Computerkonto oder ein Dienstkonto sein, unter dem der SPS-App-Pool ausgeführt wird.

## <a name="configure-single-sign-on"></a>Einmaliges Anmelden konfigurieren 
1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](application-proxy-add-on-premises-application.md). Stellen Sie sicher, dass **Azure Active Directory** als **Präauthentifizierungsmethode** ausgewählt ist.
2. Wenn Ihre Anwendung in der Liste der Unternehmensanwendungen angezeigt wird, wählen Sie sie aus, und klicken auf **Einmaliges Anmelden**.
3. Legen Sie den Modus für einmaliges Anmelden auf **Integrierte Windows-Authentifizierung** fest.  
4. Geben Sie den Wert für **Interner Anwendungs-SPN** des Anwendungsservers ein. In diesem Beispiel ist der SPN für unsere veröffentlichte Anwendung http//www.contoso.com. Dieser SPN muss sich in der Liste mit Diensten befinden, an die der Connector delegierte Anmeldeinformationen ausgeben kann. 
5. Wählen Sie die **delegierte Identität für die Anmeldung** für den zu verwendenden Connector im Auftrag Ihrer Benutzer. Weitere Informationen finden Sie unter [Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](#working-with-different-on-premises-and-cloud-identities).

   ![Erweiterte Anwendungskonfiguration](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO für Nicht-Windows-Apps

Der Ablauf der Kerberos-Delegierung im Azure AD-Anwendungsproxy wird gestartet, wenn Azure AD den Benutzer in der Cloud authentifiziert. Sobald die Anforderung lokal ankommt, gibt der Azure AD-Anwendungsproxy-Connector durch Interaktion mit dem lokalen Active Directory ein Kerberos-Ticket für den Benutzer aus. Dieser Prozess wird als Kerberos Constrained Delegation (KCD) bezeichnet. In der nächsten Phase wird mit diesem Kerberos-Ticket eine Anforderung an die Back-End-Anwendung gesendet. 

Es gibt mehrere Protokolle, die definieren, wie solche Anforderungen gesendet werden. Die meisten Nicht-Windows Server erwarten, dass die Aushandlung über SPNEGO erfolgt. Dieses Protokoll wird auf dem Azure AD-Anwendungsproxy unterstützt, ist aber standardmäßig deaktiviert. Ein Server kann für SPNEGO oder die standardmäßige KCD konfiguriert werden, jedoch nicht für beide.

Wenn Sie einen Connectorcomputer für SPNEGO konfigurieren, stellen Sie sicher, dass alle anderen Connectors in dieser Connectorgruppe ebenfalls mit SPNEGO konfiguriert sind. Anwendungen, die eine standardmäßige KCD erwarten, sollten über andere Connectors weitergeleitet werden, die nicht für SPNEGO konfiguriert sind.
 

So aktivieren Sie SPNEGO:

1. Öffnen Sie eine Eingabeaufforderung, die mit Administratorrechten ausgeführt wird.
2. Führen Sie an der Eingabeaufforderung für die Connectorserver, die SPNEGO benötigen, die folgenden Befehle aus.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Weitere Informationen zu Kerberos finden Sie unter [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd) (Alles über die eingeschränkte Kerberos-Delegierung, KCD).

Nicht-Windows-Apps verwenden normalerweise Benutzernamen oder Namen von SAM-Konten statt E-Mail-Adressen von Domänen. Wenn diese Situation auf Ihre Anwendungen zutrifft, müssen Sie das Feld „Delegierte Identität für Anmeldung“ konfigurieren, um Ihre Cloudidentitäten mit Ihren Anwendungsidentitäten zu verbinden. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy
Der Anwendungsproxy geht davon aus, dass Benutzer in der Cloud genau dieselbe Identität wie lokal besitzen. Wenn dies nicht der Fall ist, können Sie weiterhin KCD für das einmalige Anmelden verwenden. Konfigurieren Sie eine **Delegierte Identität für Anmeldung** für jede Anwendung, um anzugeben, welche Identität bei der Durchführung des einmaligen Anmeldens verwendet werden soll.  

Diese Funktion ermöglicht vielen Organisationen mit unterschiedlichen lokalen Identitäten und Cloudidentitäten SSO aus der Cloud auf lokale Apps, ohne dass der Benutzer unterschiedliche Benutzernamen und Kennwörter eingeben muss. Hierzu gehören Organisationen mit folgenden Merkmalen:

* Sie besitzen intern mehrere Domänen (joe@us.contoso.com, joe@eu.contoso.com) und eine einzelne Domäne in der Cloud (joe@contoso.com).
* Sie besitzen intern einen nicht routingfähigen Domänennamen (joe@contoso.usa) und einen zulässigen Namen in der Cloud.
* Sie verwenden intern keine Domänennamen (Joe).
* Sie verwenden lokal und in der Cloud unterschiedliche Aliase. Beispiel, joe-johns@contoso.com vs. joej@contoso.com  

Mit dem Anwendungsproxy können Sie wählen, welche Identität verwendet werden soll, um das Kerberos-Ticket erhalten. Diese Einstellung erfolgt pro Anwendung. Einige dieser Optionen eignen sich für Systeme, die kein E-Mail-Adressformat akzeptieren, andere sind auf eine alternative Anmeldung ausgelegt.

![Screenshot: Parameter „Delegierte Identität für Anmeldung“](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Bei Verwendung der Delegierten Identität für Anmeldung ist der Wert unter Umständen nicht für alle Domänen oder Gesamtstrukturen in Ihrer Organisation eindeutig. Sie können dieses Problem umgehen, indem Sie die Anwendung zweimal mit zwei unterschiedlichen Connectorgruppen veröffentlichen. Da jede Anwendung einen anderen Benutzerkreis aufweist, lassen sich die Connectors mit einer unterschiedlichen Domäne verknüpfen.

### <a name="configure-sso-for-different-identities"></a>Konfigurieren von SSO für verschiedene Identitäten
1. Konfigurieren Sie die Azure AD Connect-Einstellungen so, dass die E-Mail-Adresse die Hauptidentität ist. Dies erfolgt als Teil des Anpassungsvorgangs durch Änderung des **Benutzerprinzipalnamens** in den Synchronisierungseinstellungen. Diese Einstellungen bestimmen auch, wie sich Benutzer bei Office 365, Windows 10-Geräten und anderen Anwendungen anmelden, die Azure AD als Identitätsspeicher verwenden.  
   ![Screenshot: Benutzer identifizieren – Dropdownliste für Benutzerprinzipalname](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Wählen Sie in den Anwendungskonfigurationseinstellungen für die zu modifizierende Anwendung die **Delegierte Identität für Anmeldung** aus:

   * Benutzerprinzipalname (z.B. joe@contoso.com)
   * Alternativer Benutzerprinzipalname (z.B. joed@contoso.local)
   * Benutzernamensteil des Benutzerprinzipalnamens (z.B. joe)
   * Benutzernamensteil des alternativen Benutzerprinzipalnamens (z.B. joed)
   * Lokaler SAM-Kontoname (je nach Konfiguration des lokalen Domänencontrollers)

### <a name="troubleshooting-sso-for-different-identities"></a>Problembehandlung bei SSO für verschiedene Identitäten
Wenn im SSO-Prozess ein Fehler auftritt, wird dieser im Ereignisprotokoll des Connectorcomputers aufgeführt, wie unter [Problembehandlung](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)beschrieben.
In einigen Fällen wird die Anforderung jedoch erfolgreich an die Back-End-Anwendung gesendet, während die Anwendung mit verschiedenen anderen HTTP-Antworten reagiert. Die Problembehandlung beginnt in diesen Fällen zweckmäßigerweise mit der Untersuchung der Ereignisnummer 24029 auf dem Connectorcomputer im Sitzungsereignisprotokoll des Anwendungsproxys. Die Benutzeridentität, die für die Delegierung verwendet wurde, wird im Feld „Benutzer“ in den Ereignisdetails angezeigt. Wählen Sie zum Aktivieren des Sitzungsprotokolls im Menü „Ansicht“ der Ereignisanzeige die Option **Analytische und Debugprotokolle einblenden** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren einer Anwendungsproxyanwendung zum Verwenden der eingeschränkten Kerberos-Delegierung](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md)


Aktuelle Neuigkeiten und Updates finden Sie im [Blog zum Anwendungsproxy](https://blogs.technet.com/b/applicationproxyblog/)

