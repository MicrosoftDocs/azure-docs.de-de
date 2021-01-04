---
title: 'Azure AD Connect: Problembehebung bei Konnektivitätsproblemen | Microsoft-Dokumentation'
description: Erklärt, wie Konnektivitätsprobleme mit Azure AD Connect behoben werden können
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 56e9820c5e3a750a35b7271b86750df00eb4784e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677056"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Problembehebung bei Azure AD-Konnektivitätsproblemen
Dieser Artikel erklärt, wie die Konnektivität zwischen Azure AD Connect und Azure AD funktioniert und wie Konnektivitätsprobleme behoben werden können. Diese Probleme können insbesondere in einer Umgebung mit einem Proxyserver auftreten.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Konnektivitätsprobleme mit dem Installations-Assistent beheben
Azure AD Connect verwendet zur Authentifizierung die moderne Authentifizierung (mithilfe der ADAL-Bibliothek). Für den Installations-Assistenten und das Synchronisierungsmodul ist eine ordnungsgemäße Konfiguration von „machine.config“ erforderlich, weil es sich bei diesen Komponenten um .NET-Anwendungen handelt.

In diesem Artikel zeigen wir Ihnen, wie Fabrikam durch seinen Proxy mit Azure AD verbunden wird. Der Proxyserver heißt fabrikamproxy und verwendet Port 8080.

Sie müssen zunächst sicherstellen, dass [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) ordnungsgemäß konfiguriert ist und der **Microsoft Azure AD-Synchronisierungsdienst** nach der Aktualisierung der Datei „machine.config“ neu gestartet wurde.
![Screenshot mit einem Teil der Datei „machine.config“.](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> In einigen Nicht-Microsoft-Blogs ist dokumentiert, dass Änderungen stattdessen an „miiserver.exe.config“ vorgenommen werden sollen. Diese Datei wird jedoch mit jeder Aktualisierung überschrieben. Selbst wenn das System nach der Erstinstallation funktionieren sollte, ist das nach der ersten Aktualisierung nicht mehr der Fall. Daher wird empfohlen, stattdessen „machine.config“ zu aktualisieren.
>
>

Der Proxyserver muss die erforderlichen URLs geöffnet haben. Die offizielle Liste ist unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) zu finden.

Die in der folgenden Tabelle aufgeführten URLs stellen die Grundvoraussetzungen für eine Verbindung mit Azure AD dar. Diese Liste beinhaltet keine optionalen Funktionen, wie die Kennwortrückschreibung oder Azure AD Connect Health. Sie soll bei der Problembehebung während der Erstkonfiguration helfen.

| URL | Port | BESCHREIBUNG |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Wird verwendet um CRL-Listen (Zertifikatsperrlisten) herunterzuladen. |
| \*.verisign.com |HTTP/80 |Wird verwendet um CRL-Listen (Zertifikatsperrlisten) herunterzuladen. |
| \*.entrust.net |HTTP/80 |Wird verwendet um CRL-Listen (Zertifikatsperrlisten) für MFA herunterzuladen. |
| \*.windows.net |HTTPS/443 |Wird für die Anmeldung bei Azure AD verwendet. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Wird für MFA verwendet. |
| \*.microsoftonline.com |HTTPS/443 |Wird zum Konfigurieren Ihres Azure AD-Verzeichnisses und zum Importieren/Exportieren von Daten verwendet. |
| \*.crl3.digicert.com |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |
| \*.crl4.digicert.com |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |
| \*.ocsp.digicert.com |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |
| \*. www.d-trust.net |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |
| \*.root-c3-ca2-2009.ocsp.d-trust.net |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |
| \*.crl.microsoft.com |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |
| \*.oneocsp.microsoft.com |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |
| \*.ocsp.msocsp.com |HTTP/80 |Wird zum Überprüfen von Zertifikaten verwendet. |

## <a name="errors-in-the-wizard"></a>Fehler im Assistenten
Der Installations-Assistent verwendet zwei verschiedene Sicherheitskontexte. Auf der Seite **Mit Azure AD verbinden** verwendet er den aktuell angemeldeten Benutzer. Auf der Seite **Konfigurieren** wechselt er zu dem [Konto, das den Dienst für das Synchronisierungsmodul ausführt](reference-connect-accounts-permissions.md#adsync-service-account). Wenn ein Problem vorliegt, tritt es wahrscheinlich bereits auf der Seite **Mit Azure AD verbinden** des Assistenten auf, da die Proxykonfiguration global ist.

Die folgenden Probleme sind die häufigsten Fehler, die im Installations-Assistenten auftreten.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Der Installations-Assistent wurde nicht richtig konfiguriert
Dieser Fehler tritt auf, wenn der Assistent den Proxy selbst nicht erreichen kann.
![Screenshot mit dem Fehler „Die Anmeldeinformationen können nicht überprüft werden“.](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Falls dieser Fehler angezeigt wird, überprüfen Sie, ob [machine.config](how-to-connect-install-prerequisites.md#connectivity) richtig konfiguriert wurde.
* Falls dies in Ordnung ist, befolgen Sie die Schritte zur [Überprüfung der Proxykonnektivität](#verify-proxy-connectivity) , um zu sehen, ob die Probleme außerhalb des Assistenten ebenfalls auftreten.

### <a name="a-microsoft-account-is-used"></a>Ein Microsoft-Konto wird verwendet
Bei Verwendung eines **Microsoft-Kontos** anstelle eines **Schul- oder Organisationskontos** wird ein generischer Fehler angezeigt.
![Ein Microsoft-Konto wird verwendet](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Der MFA-Endpunkt ist nicht erreichbar
Dieser Fehler wird angezeigt, wenn der Endpunkt **https://secure.aadcdn.microsoftonline-p.com** nicht erreichbar ist und Ihr globaler Administrator MFA aktiviert hat.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Wenn dieser Fehler angezeigt wird, stellen Sie sicher, dass dem Proxy der Endpunkt **secure.aadcdn.microsoftonline-p.com** hinzugefügt wurde.

### <a name="the-password-cannot-be-verified"></a>Das Kennwort kann nicht überprüft werden
Falls sich der Installations-Assistent erfolgreich mit Azure AD verbinden konnte, aber das Kennwort selbst nicht überprüft werden kann, wird Ihnen dieser Fehler angezeigt: ![Falsches Kennwort.](./media/tshoot-connect-connectivity/badpassword.png)

* Handelt es sich um ein temporäres Kennwort, das geändert werden muss? Handelt es sich um das richtige Kennwort? Versuchen Sie, sich bei `https://login.microsoftonline.com` (auf einem anderen Computer als dem Azure AD Connect-Server) anzumelden, und überprüfen Sie, ob das Konto verwendbar ist.

### <a name="verify-proxy-connectivity"></a>Überprüfung der Proxykonnektivität
Um nachzuprüfen, ob der Azure AD Connect-Server über eine Verbindung zum Proxy und dem Internet verfügt, verwenden wir einige PowerShell-Teile, um festzustellen, ob der Proxy Webanforderungen zulässt oder nicht. Führen Sie in PowerShell `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` aus. (Streng genommen ist der erste Aufruf an `https://login.microsoftonline.com` gerichtet. Dieser URI würde auch funktionieren, aber der andere URI antwortet schneller.)

PowerShell verwendet die Konfiguration aus machine.config , um den Proxy zu kontaktieren. Die Einstellungen in winhttp/netsh sollten sich nicht auf diese Cmdlets auswirken.

Wenn der Proxy richtig konfiguriert ist, sollten Sie einen Erfolgsstatus erhalten:  ![Screenshot: Bei ordnungsgemäßer Konfiguration des Proxy wird der Erfolgsstatus angezeigt](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Falls Sie die Meldung **Die Verbindung mit dem Remoteserver kann nicht hergestellt werden** erhalten, versucht PowerShell entweder gerade einen direkten Aufruf durchzuführen, ohne den Proxy zu verwenden, oder das DNS ist nicht richtig konfiguriert. Stellen Sie sicher, dass die Datei **machine.config** richtig konfiguriert ist.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Falls der Proxy nicht richtig konfiguriert ist, tritt folgender Fehler auf: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Fehler | Fehlertext | Comment |
| --- | --- | --- |
| 403 |Verboten |Der Proxy wurde für die angeforderte URL nicht geöffnet. Rufen Sie die Proxykonfiguration erneut auf, und stellen Sie sicher, dass die [URLs](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) geöffnet wurden. |
| 407 |Proxyauthentifizierung erforderlich |Der Proxyserver erfordert eine Anmeldung, die nicht erfolgt ist. Stellen Sie sicher, dass Sie in den Konfigurationen von „machine.config“ eine entsprechende Einstellung vorgenommen haben, falls Ihr Proxyserver eine Authentifizierung erfordert. Stellen Sie außerdem sicher, dass Sie sowohl für den Benutzer, der den Assistenten ausführt, als auch für das Dienstkonto Domänenkonten verwenden. |

### <a name="proxy-idle-timeout-setting"></a>Einstellung für Proxy-Leerlauftimeout
Wenn Azure AD Connect eine Exportanforderung an Azure AD gesendet hat, kann Azure AD bis zu 5 Minuten benötigen, die Anforderung zu verarbeiten und dann eine Antwort zu generieren. Dies kann insbesondere passieren, wenn eine Exportanforderung einige Gruppenobjekte mit großen Gruppenmitgliedschaften enthält. Stellen Sie sicher, dass das Proxy-Leerlauftimeout so konfiguriert ist, dass es größer ist als 5 Minuten. Andernfalls kann es vorkommen, dass auf dem Azure AD Connect-Server zeitweilig Konnektivitätsprobleme mit Azure AD auftreten.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Das Kommunikationsmuster zwischen Azure AD Connect und Azure AD
Falls Sie alle vorhergehenden Schritte ausgeführt haben und immer noch keine Verbindung herstellen können, sollten Sie sich Ihre Netzwerkprotokolle ansehen. Dieser Abschnitt dokumentiert ein normales und erfolgreiches Konnektivitätsmuster. Er zeigt auch häufig auftretende Meldungen, die Sie aber ignorieren können, wenn Sie die Netzwerkprotokolle lesen.

* Es erfolgen Aufrufe an `https://dc.services.visualstudio.com`. Für eine erfolgreiche Installation ist es nicht erforderlich, dass diese URL im Proxy geöffnet ist. Sie können diese Aufrufe daher ignorieren.
* Sie werden sehen, dass die DNS-Auflösung den tatsächlichen Host im DNS-Namen von nsatc.net und anderen Namespaces anzeigt, anstatt unter microsoftonline.com. Es werden jedoch keine Webdienstanfragen an die eigentlichen Servernamen gestellt, und Sie müssen diese URLs dem Proxy nicht hinzufügen.
* Die Endpunkte „adminwebservice“ und „provisioningapi“ sind Ermittlungsendpunkte und werden benutzt, um die tatsächlich zu verwendeten Endpunkte zu finden. Diese Endpunkte unterscheiden sich abhängig von Ihrer Region.

### <a name="reference-proxy-logs"></a>Verweisproxyprotokolle
Hier nun ein Auszug eines echten Proxyprotokolls und der Seite des Installations-Assistenten, von der er entnommen wurde. (Doppelte Einträge, die zum selben Endpunkt führen, wurden entfernt). Sie können diesen Abschnitt als Referenz für Ihre eigenen Proxy- und Netzwerkprotokolle verwenden. Die tatsächlichen Endpunkte können in Ihrer Umgebung abweichen (insbesondere die *kursiv* formatierten URLs).

**Herstellen einer Verbindung mit Azure AD**

| Time | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect:// *bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect:// *bwsc02-relay*.microsoftonline.com:443 |

**Konfigurieren**

| Time | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect:// *bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect:// *bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect:// *bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect:// *bwsc02-relay*.microsoftonline.com:443 |

**Erste Synchronisierung**

| Time | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect:// *bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect:// *bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Authentifizierungsfehler
Dieser Abschnitt deckt Fehler ab, die von ADAL (der von Azure AD Connect verwendeten Authentifizierungsbibliothek) und PowerShell zurückgegeben werden können. Die Fehlererläuterungen sollen Sie beim Verständnis der nächsten Schritte unterstützen.

### <a name="invalid-grant"></a>Ungültige Anmeldedaten
Ungültiger Benutzername oder ungültiges Kennwort. Weitere Informationen finden Sie unter [Das Kennwort kann nicht überprüft werden](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Unbekannter Benutzertyp
Ihr Azure AD-Verzeichnis wurde nicht gefunden oder kann nicht aufgelöst werden. Versuchen Sie möglicherweise, sich mit einem Benutzernamen in einer nicht überprüften Domäne anzumelden?

### <a name="user-realm-discovery-failed"></a>Fehler bei Benutzerbereichserkennung
Es liegen Probleme mit der Netzwerk- oder Proxykonfiguration vor. Das Netzwerk kann nicht erreicht werden. Siehe [Konnektivitätsprobleme mit dem Installations-Assistenten beheben](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Benutzerkennwort abgelaufen
Ihre Anmeldeinformationen sind abgelaufen. Ändern Sie Ihr Kennwort.

### <a name="authorization-failure"></a>Autorisierungsfehler
Fehler beim Autorisieren des Benutzers für die Durchführung einer Aktion in Azure AD.

### <a name="authentication-canceled"></a>Authentifizierung abgebrochen
Das MFA-Captcha (Multi-Factor Authentication) wurde abgebrochen.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Fehler beim Herstellen der Verbindung mit MS Online
Die Authentifizierung war erfolgreich, aber es liegt ein Authentifizierung bei Azure AD PowerShell vor.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Globale Administratorrolle für Azure AD erforderlich
Der Benutzer wurde erfolgreich authentifiziert. Dem Benutzer ist aber keine globale Administratorrolle zugewiesen. Auf diese Weise können Sie dem Benutzer [die globale Administratorrolle zuweisen](../roles/permissions-reference.md).

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management aktiviert
Die Authentifizierung war erfolgreich. Privileged Identity Management wurde aktiviert, und Sie sind aktuell kein globaler Administrator. Weitere Informationen finden Sie unter [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Unternehmensinformationen nicht verfügbar
Die Authentifizierung war erfolgreich. Es konnten keine Unternehmensinformationen aus Azure AD abgerufen werden.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Domäneninformationen nicht verfügbar
Die Authentifizierung war erfolgreich. Es konnten keine Domäneninformationen aus Azure AD abgerufen werden.

### <a name="unspecified-authentication-failure"></a>Nicht spezifizierter Authentifizierungsfehler
Wird als „Unerwarteter Fehler“ im Installations-Assistenten angezeigt. Dies kann auftreten, wenn Sie versuchen, ein **Microsoft-Konto** anstelle eines **Schul- oder Organisationskontos** zu verwenden.

## <a name="troubleshooting-steps-for-previous-releases"></a>Schritte zur Problembehandlung für frühere Versionen.
Ab Build 1.1.105.0 (veröffentlicht im Februar 2016) wurde der Anmelde-Assistent eingestellt. Dieser Abschnitt und die Konfiguration sind eigentlich nicht mehr erforderlich, werden aber als Referenz beibehalten.

Damit der Assistent für einmaliges Anmelden funktioniert, muss winhttp konfiguriert werden. Diese Konfiguration kann mit [**netsh**](how-to-connect-install-prerequisites.md#connectivity) durchgeführt werden.
![Screenshot mit einem Eingabeaufforderungsfenster, in dem das netsh-Tool zum Festlegen eines Proxys ausgeführt wird.](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Der Anmelde-Assistent wurde nicht richtig konfiguriert
Dieser Fehler tritt auf, wenn der Anmelde-Assistent den Proxy nicht erreichen kann oder der Proxy die Anfrage nicht zulässt.
![Screenshot mit dem Fehler „Die Anmeldeinformationen können nicht überprüft werden. Überprüfen Sie die Netzwerkkonnektivität und die Firewall- oder Proxyeinstellungen“.](./media/tshoot-connect-connectivity/nonetsh.png)

* Falls dieser Fehler angezeigt wird, prüfen Sie die Proxykonfiguration in [netsh](how-to-connect-install-prerequisites.md#connectivity).
  ![Screenshot mit einem Eingabeaufforderungsfenster, in dem das netsh-Tool ausgeführt wird, um die Proxykonfiguration anzuzeigen.](./media/tshoot-connect-connectivity/netshshow.png)
* Falls dies in Ordnung ist, befolgen Sie die Schritte zur [Überprüfung der Proxykonnektivität](#verify-proxy-connectivity) , um zu sehen, ob die Probleme außerhalb des Assistenten ebenfalls auftreten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
