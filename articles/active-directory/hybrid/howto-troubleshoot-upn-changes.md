---
title: Planung und Problembehandlung von Änderungen des Azure-Benutzerprinzipalnamens (UPN)
description: Informationen zu bekannten Problemen und entsprechende Abhilfe bei Änderungen des Benutzerprinzipalnamens
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19e40d135316c1c7cd270d2804fff1f487937685
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858534"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planung und Problembehandlung von Änderungen des Azure-Benutzerprinzipalnamens in Azure Active Directory

Ein Benutzerprinzipalname (UPN) ist ein Attribut, bei dem es sich um einen Internetkommunikationsstandard für Benutzerkonten handelt. Ein UPN besteht aus einem UPN-Präfix (dem Benutzerkontonamen) und einem UPN-Suffix (einem DNS-Domänennamen). Das Präfix wird über das \@-Symbol mit dem Suffix verknüpft. Beispiel: someone@example.com. Ein UPN muss für alle Sicherheitsprinzipalobjekte innerhalb einer Verzeichnisgesamtstruktur eindeutig sein. 

**In diesem Artikel wird davon ausgegangen, dass Sie den UPN als Benutzer-ID verwenden. Dieser Artikel behandelt die Planung von UPN-Änderungen sowie die Behebung von Problemen, die sich aus UPN-Änderungen ergeben können.**

> [!NOTE]
> Entwicklern empfehlen wir, anstelle von UPN oder E-Mail-Adressen die Objekt-ID des Benutzers als unveränderlichen Bezeichner zu verwenden, da sich die Werte von UPN oder E-Mail-Adressen ändern können.


## <a name="learn-about-upns-and-upn-changes"></a>Informationen zu UPNs und UPN-Änderungen
Auf Anmeldeseiten werden Benutzer häufig dazu aufgefordert, ihre E-Mail-Adresse einzugeben, obwohl der erforderliche Wert tatsächlich ihr UPN ist. Daher sollten Sie den UPN von Benutzern immer ändern, wenn ihre primäre E-Mail-Adresse geändert wird.

Die primären E-Mail-Adressen von Benutzern können aus vielen Gründen geändert werden:

* Rebranding eines Unternehmens

* Mitarbeiter, die in andere Firmenabteilungen wechseln 

* Fusionen und Übernahmen

* Änderungen von Mitarbeiternamen

### <a name="types-of-upn-changes"></a>Typen von UPN-Änderungen

Einen UPN können Sie ändern, indem Sie das Präfix, das Suffix oder beide ändern.

* **Ändern des Präfixes**

   *  Wenn sich z. B. der Name einer Person geändert hat, können Sie den zugehörigen Kontonamen ändern:  
‎BSimon@contoso.com in BJohnson@contoso.com

   * Sie können auch den Unternehmensstandard für Präfixe ändern:  
‎Bsimon@contoso.com in Britta.Simon@contoso.com

* **Ändern des Suffixes** <br>

    Wenn sich z. B. die Abteilung einer Person geändert hat, können Sie die zugehörige Domäne ändern: 

   * Britta.Simon@contoso.com in Britta.Simon@contosolabs.com <br>
     oder<br>
    * Britta.Simon@corp.contoso.com in Britta.Simon@labs.contoso.com 

Es wird empfohlen, den Benutzerprinzipalnamen (UPN) für die Benutzer bei jeder Aktualisierung der primären E-Mail-Adresse zu ändern.

Stellen Sie sicher, dass die E-Mail-Adressen der Benutzer bei der ersten Synchronisierung von Active Directory mit Azure AD identisch mit ihren UPNs sind.

### <a name="upns-in-active-directory"></a>UPNs in Active Directory

In Active Directory ist das UPN-Standardsuffix der DNS-Name der Domäne, in der Sie das Benutzerkonto erstellt haben. In den meisten Fällen ist dies der Domänenname, den Sie als Unternehmensdomäne im Internet registrieren. Wenn Sie das Benutzerkonto in der Domäne „contoso.com“ erstellen, lautet der Standard-UPN wie folgt:

username@contoso.com

 Jedoch können Sie mithilfe von Active Directory-Domänen und -Vertrauensstellungen [weitere UPN-Suffixe hinzufügen](../fundamentals/add-custom-domain.md). 

Beispielsweise können Sie „labs.contoso.com“ hinzufügen und dies in den UPNs und E-Mail-Adressen der Benutzer entsprechend angeben. Diese sehen dann wie folgt aus:

username@labs.contoso.com.

>[!IMPORTANT]
> Wenn Sie [das Suffix in Active Directory ändern](../fundamentals/add-custom-domain.md), müssen Sie sicherstellen, dass ein übereinstimmender benutzerdefinierter Domänenname [in Azure AD hinzugefügt und überprüft](../fundamentals/add-custom-domain.md) wurde. 

![Screenshot der überprüften Domänen](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPNs in Azure Active Directory

Benutzer melden sich bei Azure AD mit dem Wert in ihrem userPrincipalName-Attribut an. 

Wenn Sie Azure AD in Verbindung mit Ihrer lokalen Active Directory-Instanz verwenden, werden Benutzerkonten mithilfe des Azure AD Connect-Diensts synchronisiert. Standardmäßig verwendet der Azure AD Connect-Assistent das userPrincipalName-Attribut aus der lokalen Active Directory-Instanz als UPN in Azure AD. In einer benutzerdefinierten Installation können Sie dies in ein anderes Attribut ändern.

Es ist wichtig, dass Sie über einen definierten Prozess verfügen, wenn Sie einen Benutzerprinzipalnamen (UPN) eines einzelnen Benutzers oder für Ihre gesamte Organisation aktualisieren. 

Weitere Informationen finden Sie in diesem Dokument in den Abschnitten zu bekannten Problemen und Problemumgehungen.

Wenn Sie Benutzerkonten aus Active Directory mit Azure AD synchronisieren, stellen Sie sicher, dass die UPNs in Active Directory den überprüften Domänen in Azure AD zugeordnet sind.

![Screenshot, der Beispiele für UPNs zeigt, die verifizierten Azure AD-Domänen zugeordnet sind](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Wenn der Wert des userPrincipalName-Attributs nicht einer verifizierten Domäne in Azure AD entspricht, wird das Suffix im Synchronisierungsvorgang durch den Standardwert „.onmicrosoft.com“ ersetzt.


### <a name="roll-out-bulk-upn-changes"></a>Rollout von UPN-Massenänderungen

Wenden Sie für UPN-Massenänderungen die [bewährten Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md) an. Außerdem sollten Sie über einen getesteten Rollbackplan für die Wiederherstellung von UPNs verfügen, wenn Probleme auftreten, die nicht schnell behoben werden können. Wenn Ihr Pilotversuch ausgeführt wird, können Sie mit der Ausrichtung auf kleine Gruppen von Benutzern mit verschiedenen Organisationsrollen und ihren spezifischen Gruppen von Apps oder Geräten beginnen.

Beim Durchlaufen dieser ersten Teilgruppe von Benutzern erhalten Sie eine gute Vorstellung davon, was Benutzer als Teil der Änderung erwarten sollten. Nehmen Sie diese Informationen in die Kommunikation mit den Benutzern auf.

Erstellen Sie eine definierte Prozedur zum Ändern von UPNs für einzelne Benutzer als Teil des normalen Betriebs. Es wird empfohlen, dass Sie über eine getestete Prozedur verfügen, die eine Dokumentation zu bekannten Problemen und Problemumgehungen enthält.

In den folgenden Abschnitten werden potenzielle bekannte Probleme und Problemumgehungen in Bezug auf UPN-Änderungen ausführlich erläutert.

## <a name="apps-known-issues-and-workarounds"></a>Bekannte Probleme und Umgehungen bei Apps

[Software-as-a-Service- (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) und Branchenanwendungen berufen sich häufig auf UPNs, um Benutzer zu suchen und Benutzerprofilinformationen, einschließlich Rollen, zu speichern. UPN-Änderungen können sich auf Anwendungen auswirken, in denen Benutzerprofile mithilfe der [Just-in-Time-Bereitstellung](../app-provisioning/user-provisioning.md) erstellt werden, wenn Benutzer sich zum ersten Mal bei der Anwendung anmelden.

**Bekanntes Problem**<br>
Durch Ändern des UPN eines Benutzers kann die Beziehung zwischen dem Azure AD-Benutzer und dem in der Anwendung erstellten Benutzerprofil unterbrochen werden. Wenn in der Anwendung die [Just-in-Time-Bereitstellung](../app-provisioning/user-provisioning.md) verwendet wird, wird möglicherweise ein völlig neues Benutzerprofil erstellt. In diesem Fall muss der Anwendungsadministrator manuelle Änderungen vornehmen, um diese Beziehung zu korrigieren.

**Problemumgehung**<br>
Mithilfe der [automatisierten Benutzerbereitstellung in Azure AD](../app-provisioning/user-provisioning.md) können Sie die Benutzeridentitäten in unterstützten Cloudanwendungen automatisch erstellen, verwalten und entfernen. Durch Konfigurieren der automatisierten Benutzerbereitstellung für Ihre Anwendungen werden UPNs für die Anwendungen automatisch aktualisiert. Testen Sie die Anwendungen im Rahmen des progressiven Rollouts, um sicherzustellen, dass sie nicht von UPN-Änderungen betroffen sind.
Wenn Sie Entwickler sind, sollten Sie das [Hinzufügen der SCIM-Unterstützung zu Ihrer Anwendung](../app-provisioning/use-scim-to-provision-users-and-groups.md) in Betracht ziehen, um die automatische Benutzerbereitstellung über Azure Active Directory zu aktivieren. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Bekannte Probleme und Problemumgehungen bei verwalteten Geräten

Durch das [Bereitstellen Ihrer Geräte in Azure AD](../devices/overview.md) maximieren Sie die Produktivität Ihrer Benutzer durch einmaliges Anmelden (SSO) für Ihre gesamten Cloud- und lokalen Ressourcen.

### <a name="azure-ad-joined-devices"></a>In Azure AD eingebundene Geräte

[In Azure AD eingebundene ](../devices/concept-azure-ad-join.md) Geräte werden direkt mit Azure AD verknüpft und ermöglichen es Benutzern, sich mit der Identität ihrer Organisation beim jeweiligen Gerät anzumelden.

**Bekannte Probleme** <br>
Bei Benutzern treten möglicherweise Probleme beim einmaligen Anmelden bei Anwendungen auf, die für die Authentifizierung von Azure AD abhängen.

**Lösung** <br>
Die in diesem Abschnitt erwähnten Probleme wurden im Windows-Update vom 10. Mai 2020 (2004) behoben.

**Problemumgehung** <br>
Planen Sie genügend Zeit ein, damit die UPN-Änderung mit Azure AD synchronisiert werden kann. Nachdem Sie überprüft haben, ob der neue UPN im Azure AD-Portal angezeigt wird, bitten Sie den Benutzer, die Kachel „Anderer Benutzer“ auszuwählen und sich mit dem neuen UPN anzumelden. Die Überprüfung können Sie auch über [PowerShell](/powershell/module/azuread/get-azureaduser) durchführen. Nach der Anmeldung mit dem neuen UPN werden in der Windows-Einstellung „Zugriff auf Geschäft, Schule oder Uni“ möglicherweise weiterhin Verweise auf den alten UPN angezeigt.

![Screenshot der überprüften Domänen](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>In Azure AD eingebundene Hybridgeräte

[In Azure AD Hybrid eingebundene](../devices/concept-azure-ad-join-hybrid.md) Geräte werden mit Active Directory und Azure AD verknüpft. Sie können die Einbindung in Azure AD Hybrid implementieren, wenn Ihre Umgebung über einen lokalen Active Directory-Fußabdruck verfügt und Sie auch die Funktionen von Azure AD nutzen möchten.

**Bekannte Probleme** 

Bei in Azure AD Hybrid eingebundenen Geräten unter Windows 10 treten wahrscheinlich unerwartete Neustarts und Zugriffsprobleme auf.

Wenn sich Benutzer bei Windows anmelden, bevor der neue UPN mit Azure AD synchronisiert wurde, oder wenn sie weiterhin eine vorhandene Windows-Sitzung verwenden, treten in Anwendungen, in denen Azure AD zur Authentifizierung verwendet wird, möglicherweise Probleme beim einmaligen Anmelden auf, wenn der bedingte Zugriff so konfiguriert wurde, dass die Verwendung von in Azure AD Hybrid eingebundenen Geräten für den Zugriff auf Ressourcen erzwungen wird. 

Außerdem wird die folgende Meldung angezeigt und ein Neustart nach einer Minute erzwungen. 

„Ihr PC wird in einer Minute automatisch neu gestartet. Windows muss aufgrund eines Problems neu gestartet werden. Schließen Sie diese Meldung, und speichern Sie Ihre Daten.“

**Lösung** <br>
Die in diesem Abschnitt erwähnten Probleme wurden im Windows-Update vom 10. Mai 2020 (2004) behoben.

**Problemumgehung** 

Die Einbindung des Geräts in Azure AD muss aufgehoben und das Gerät neu gestartet werden. Nach dem Neustart wird das Gerät automatisch wieder in Azure AD eingebunden. Der Benutzer muss sich durch Auswählen der Kachel „Anderer Benutzer“ mit dem neuen UPN anmelden. Führen Sie den folgenden Befehl an einer Eingabeaufforderung aus, um die Einbindung eines Geräts in Azure AD aufzuheben:

**dsregcmd /leave**

Der Benutzer muss sich erneut für Windows Hello for Business [registrieren](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision), wenn das Programm verwendet wird. Windows 7- und Windows 8.1-Geräte sind von diesem Problem nach UPN-Änderungen nicht betroffen.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Bekannte Probleme und Problemumgehungen bei Microsoft Authenticator

Ihre Organisation erfordert möglicherweise die Verwendung der [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md) für die Anmeldung und den Zugriff auf Organisationsanwendungen und -daten. Ein Benutzername kann in der App zwar angezeigt werden, das Konto ist jedoch erst als Überprüfungsmethode eingerichtet, nachdem der Benutzer den Registrierungsvorgang abgeschlossen hat.

Die [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md) hat vier Hauptfunktionen:

* Mehrstufige Authentifizierung über eine Pushbenachrichtigung oder einen Überprüfungscode

* Authentifizierungsbroker auf iOS- und Android-Geräten, um einmaliges Anmelden für Anwendungen bereitzustellen, in denen die [Brokerauthentifizierung](../develop/msal-android-single-sign-on.md) verwendet wird

* Die Geräteregistrierung in Azure AD (auch bekannt als „Workplace Join“), die für andere Features wie dem Intune-App-Schutz und der Geräteregistrierung/-verwaltung erforderlich ist

* Anmeldung per Telefon, für die MFA und Geräteregistrierung erforderlich sind

### <a name="multi-factor-authentication-with-android-devices"></a>Mehrstufige Authentifizierung mit Android-Geräten

Die Microsoft Authenticator-App umfasst eine Out-of-Band-Überprüfungsoption. Anstelle eines automatischen Telefonanrufs oder einer SMS sendet [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) bei der Anmeldung des Benutzers eine Pushbenachrichtigung an die Microsoft Authenticator-App auf dem Smartphone oder Tablet des Benutzers. Der Benutzer tippt in der App einfach auf „Genehmigen“ (oder gibt eine PIN oder einen biometrischen Schlüssel ein und tippt auf „Authentifizieren“), um sich anzumelden.

**Bekannte Probleme** 

Wenn Sie den UPN eines Benutzers ändern, wird der alte UPN weiterhin im Benutzerkonto angezeigt, und es wird möglicherweise keine Benachrichtigung angezeigt. Die [Überprüfungscodes](../user-help/user-help-auth-app-faq.md) funktionieren weiterhin.

**Problemumgehung**

Wenn eine Benachrichtigung empfangen wird, weisen Sie den Benutzer an, die Benachrichtigung zu verwerfen, die Authenticator-App zu öffnen, auf die Option „Auf Benachrichtigungen prüfen“ zu tippen und die MFA-Aufforderung zu genehmigen. Anschließend wird der im Konto angezeigte UPN aktualisiert. Beachten Sie, dass der aktualisierte UPN möglicherweise als neues Konto angezeigt wird. Dies ist auf andere Authenticator-Funktionen zurückzuführen. Weitere Informationen finden Sie unter den anderen bekannten Problemen in diesem Artikel.

### <a name="brokered-authentication"></a>Brokerauthentifizierung

Unter Android und iOS wird über Broker wie Microsoft Authenticator Folgendes aktiviert:

* Einmaliges Anmelden (SSO): Ihre Benutzer müssen sich nicht bei jeder Anwendung anmelden.

* Geräteidentifikation: Der Broker greift auf das Gerätezertifikat zu, das auf dem Gerät erstellt wurde, als es in den Arbeitsplatz eingebunden wurde.

* Überprüfung der Anwendungsidentifikation: Wenn eine Anwendung den Broker aufruft, übergibt sie die Umleitungs-URL, und der Broker überprüft sie.

Darüber hinaus können in Anwendungen erweiterte Funktionen wie [Bedingter Zugriff](../conditional-access/index.yml) verwendet und [Microsoft Intune-Szenarien](../develop/msal-net-use-brokers-with-xamarin-apps.md) unterstützt werden.

**Bekannte Probleme**<br>
Der Benutzer erhält mehrere interaktive Authentifizierungsaufforderungen für neue Anwendungen, in denen eine brokergestützte Anmeldung verwendet wird, da der von der Anwendung übergebene Anmeldehinweis (login_hint) und der im Broker gespeicherte UPN nicht übereinstimmen.

**Problemumgehung** <br> Der Benutzer muss das Konto manuell aus Microsoft Authenticator entfernen und eine neue Anmeldung in einer brokergestützten Anwendung starten. Das Konto wird nach der ersten Authentifizierung automatisch hinzugefügt.

### <a name="device-registration"></a>Geräteregistrierung

Die Microsoft Authenticator-App dient der Registrierung des Geräts bei Azure AD. Durch die Geräteregistrierung kann das Gerät bei Azure AD authentifiziert werden. Diese Voraussetzung gilt für die folgenden Szenarien:

* Intune-App-Schutz

* Registrierung von Intune-Geräten

* Anmeldung per Telefon

**Bekannte Probleme**<br>
Wenn Sie den UPN ändern, wird in der Microsoft Authenticator-App ein neues Konto mit dem neuen UPN angezeigt, obwohl das Konto mit dem alten UPN weiterhin aufgelistet ist. Außerdem wird der alte UPN im Bereich „Geräteregistrierung“ in den App-Einstellungen angezeigt. Die normale Funktionalität der Geräteregistrierung oder der abhängigen Szenarien wird nicht geändert.

**Problemumgehung** <br> Um alle Verweise auf den alten UPN in der Microsoft Authenticator-App zu entfernen, weisen Sie den Benutzer an, das neue und das alte Konto manuell aus Microsoft Authenticator zu entfernen, sich erneut für MFA zu registrieren und das Gerät erneut einzubinden.

### <a name="phone-sign-in"></a>Anmeldung per Telefon

Bei der Anmeldung per Telefon können Benutzer sich ohne Kennwort bei Azure AD anmelden. Zum Aktivieren der Anmeldung per Telefon müssen Benutzer sich mithilfe der Authenticator-App für MFA registrieren und dann die Anmeldung per Telefon direkt in Authenticator aktivieren. Bei der Konfiguration wird das Gerät bei Azure AD registriert.

**Bekannte Probleme** <br>
Benutzer können die Anmeldung per Telefon nicht verwenden, da sie keine Benachrichtigung erhalten. Wenn der Benutzer auf „Auf Benachrichtigungen prüfen“ tippt, wird eine Fehlermeldung angezeigt.

**Problemumgehung**<br>
Der Benutzer muss das Dropdownmenü in dem Konto auswählen, das für die Anmeldung per Telefon aktiviert ist, und dann „Anmeldung per Telefon deaktivieren“ auswählen. Bei Bedarf kann die Anmeldung per Telefon wieder aktiviert werden.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Bekannte Probleme und Problemumgehungen beim Sicherheitsschlüssel (FIDO2)

**Bekannte Probleme** <br>
Wenn mehrere Benutzer für denselben Schlüssel registriert sind, wird auf dem Anmeldebildschirm eine Kontoauswahlseite angezeigt, auf der der alte UPN angezeigt wird. UPN-Änderungen wirken sich nicht auf Anmeldungen aus, bei denen Sicherheitsschlüssel verwendet werden.  

**Problemumgehung**<br>
Um Verweise auf alte UPNs zu entfernen, müssen Benutzer [den Sicherheitsschlüssel zurücksetzen und die Registrierung erneut ausführen](../authentication/howto-authentication-passwordless-security-key.md#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Bekannte Probleme und Problemumgehungen bei OneDrive

Es ist bekannt, dass bei Benutzern von OneDrive Probleme nach UPN-Änderungen auftreten. Weitere Informationen finden Sie unter [Auswirkungen von UPN-Änderungen auf die OneDrive-URL und OneDrive-Features](/onedrive/upn-changes).

## <a name="next-steps"></a>Nächste Schritte

Informationen finden Sie in folgenden Artikeln:
* [Azure AD Connect: Designkonzepte](./plan-connect-design-concepts.md)

* [Auffüllung des UserPrincipalName-Attributs in Azure AD](./plan-connect-userprincipalname.md)

* [Microsoft Identity Platform – ID-Token](../develop/id-tokens.md)
