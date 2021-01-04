---
title: Bekannte Probleme und Problembehandlung bei hybriden FIDO2-Sicherheitsschlüsseln – Azure Active Directory
description: Informationen zu einigen bekannten Problemen und Möglichkeiten zur Problembehandlung bei kennwortloser Anmeldung mit hybridem FIDO2-Sicherheitsschlüssel mithilfe von Azure Active Directory (Vorschau)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea034974e85febcffeb2494d87b666a39e524eb1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743274"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>Problembehandlung bei Hybridbereitstellungen von FIDO2-Sicherheitsschlüsseln in Azure AD (Vorschau)

In diesem Artikel werden häufig gestellte Fragen zu in Azure AD eingebundenen Hybridgeräten und zur kennwortlosen Anmeldung bei lokalen Ressourcen behandelt. Bei diesem kennwortlosen Feature können Sie die Azure AD-Authentifizierung auf Windows 10-Geräten für in Azure AD eingebundene Hybridgeräte mithilfe von FIDO2-Sicherheitsschlüsseln aktivieren. Benutzer können sich auf ihren Geräten mit modernen Anmeldeinformationen wie FIDO2-Schlüsseln bei Windows anmelden und auf herkömmliche Active Directory Domain Services (AD DS)-basierte Ressourcen zugreifen, indem sie für ihre lokalen Ressourcen nahtloses einmaliges Anmelden (Single Sign-on, SSO) nutzen.

Die folgenden Szenarien für Benutzer in einer Hybridumgebung werden unterstützt:

* Melden Sie sich mit FIDO2-Sicherheits Schlüsseln bei in Azure AD verbundenen Hybridgeräten an, und erhalten Sie SSO-Zugriff auf lokale Ressourcen.
* Melden Sie sich mit FIDO2-Sicherheitsschlüsseln bei in Azure AD verbundenen Geräten an, und erhalten Sie SSO-Zugriff auf lokale Ressourcen.

Informationen zu den ersten Schritten mit FIDO2-Sicherheitsschlüsseln und dem Hybridzugriff auf lokale Ressourcen finden Sie in den folgenden Artikeln:

* [Kennwortlose Sicherheitsschlüssel](howto-authentication-passwordless-security-key.md)
* [Kennwortlos: Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Kennwortlos: lokal](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2-Sicherheitsschlüssel sind eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Bekannte Probleme

* [Benutzer können sich mit FIDO2-Sicherheitsschlüsseln nicht anmelden, da die Gesichtserkennung in Windows Hello zu schnell und der Standardmechanismus für Anmeldungen ist](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Benutzer können nach dem Erstellen eines in Azure AD eingebundenen Hybridcomputers FIDO2-Sicherheitsschlüssel nicht sofort verwenden](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Benutzer können kein SSO für meine NTLM-Netzwerkressource abrufen, nachdem sie sich mit einem FIDO2-Sicherheitsschlüssel angemeldet und eine Aufforderung zur Eingabe der Anmeldeinformationen erhalten haben](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Benutzer können sich mit FIDO2-Sicherheitsschlüsseln nicht anmelden, da die Gesichtserkennung in Windows Hello zu schnell und der Standardmechanismus für Anmeldungen ist

Die Gesichtserkennung in Windows Hello ist die vorgesehene beste Oberfläche für ein Gerät, auf dem ein Benutzer registriert wurde. FIDO2-Sicherheitsschlüssel sind vorgesehen für eine Verwendung auf freigegebenen Geräten oder dort, wo die Registrierung bei Windows Hello for Business eine Barriere ist.

Wenn die Gesichtserkennung in Windows Hello verhindert, dass Benutzer das Anmeldeszenario mit FIDO2-Sicherheitsschlüssel ausprobieren, können sie diese Art der Anmeldung deaktivieren, indem sie in **Einstellungen > Anmeldeoptionen** die Option für Registrierung per Gesichtserkennung entfernen.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Benutzer können nach dem Erstellen eines in Azure AD eingebundenen Hybridcomputers FIDO2-Sicherheitsschlüssel nicht sofort verwenden

Nach dem Einbinden in die Domäne und dem Neustart bei der Neuinstallation eines in Azure AD eingebundenen Hybridcomputers müssen Sie sich mit einem Kennwort anmelden und warten, bis die Richtlinie synchronisiert wurde, bevor Sie sich mithilfe eines FIDO2-Sicherheitsschlüssels anmelden können.

Dieses Verhalten ist eine bekannte Einschränkung bei in die Domäne eingebundenen Geräten und für FIDO2-Sicherheitsschlüssel nicht spezifisch.

Mit dem Befehl `dsregcmd /status` können Sie den aktuellen Status überprüfen. Überprüfen Sie, ob sowohl bei *AzureAdJoined* als auch bei *DomainJoined* der Wert *YES* angezeigt wird.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Benutzer können kein SSO für meine NTLM-Netzwerkressource abrufen, nachdem sie sich mit einem FIDO2-Sicherheitsschlüssel angemeldet und eine Aufforderung zur Eingabe der Anmeldeinformationen erhalten haben

Stellen Sie sicher, dass genügend DCs gepatcht werden, damit sie rechtzeitig reagieren können, um Ihre Ressourcenanforderung zu bearbeiten. Wenn Sie überprüfen möchten, ob ein Server mit dem gerade ausgeführten Feature angezeigt wird, sehen Sie sich die Ausgabe von `nltest /dsgetdc:<dc name> /keylist /kdc` an.

Wenn bei diesem Feature ein DC angezeigt wird, hat sich das Kennwort des Benutzers nach seiner Anmeldung möglicherweise geändert, oder es liegt ein anderes Problem vor. Sammeln Sie Protokolle wie im folgenden Abschnitt beschrieben, damit das Microsoft-Supportteam debuggen kann.

## <a name="troubleshoot"></a>Problembehandlung

Es gibt zwei Bereiche, in denen eine Problembehandlung erforderlich werden kann – [Windows-Clientprobleme](#windows-client-issues) und [Bereitstellungsprobleme](#deployment-issues).

### <a name="windows-client-issues"></a>Windows-Clientprobleme

Führen Sie zum Sammeln von Daten, die bei der Behebung von Problemen im Zusammenhang mit der Anmeldung bei Windows oder dem Zugriff auf lokale Ressourcen von Windows 10-Geräten helfen können, die folgenden Schritte aus:

1. Öffnen Sie die App **Feedback-Hub**. Vergewissern Sie sich, dass Ihr Name unten links in der App angezeigt wird, und wählen Sie **Create a new feedback item** (Neues Feedbackelement erstellen) aus.

    Wählen Sie als Art des Feedbackelements *Problem* aus.

1. Wählen Sie die Kategorie *Sicherheit und Datenschutz* aus, und klicken Sie auf die Unterkategorie *FIDO*.
1. Aktivieren Sie das Kontrollkästchen für *Send attached files and diagnostics to Microsoft along with my feedback* (Angefügte Dateien und Diagnosen zusammen mit meinem Feedback an Microsoft senden).
1. Wählen Sie *Problem reproduzieren* und dann *Aufnahme starten* aus.
1. Sperren und entsperren Sie den Computer mit dem FIDO2-Sicherheitsschlüssel. Wenn das Problem auftritt, versuchen Sie, die Sperre mit anderen Anmeldeinformationen aufzuheben.
1. Kehren Sie zu **Feedback-Hub** zurück, wählen Sie **Aufnahme beenden** aus, und senden Sie Ihr Feedback.
1. Wechseln Sie zur Seite *Feedback* und dann zur Registerkarte *Mein Feedback*. Wählen Sie Ihr kürzlich gesendetes Feedback aus.
1. Wählen Sie oben rechts die Schaltfläche *Freigeben* aus, um einen Link zum Feedback abzurufen. Fügen Sie diesen Link ein, wenn Sie eine Supportanfrage öffnen oder dem Entwickler antworten, der einer vorhandenen Supportanfrage zugewiesen wurde.

Die folgenden Ereignisprotokolle und Informationen zum Registrierungsschlüssel werden gesammelt:

**Registrierungsschlüssel**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [\*]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [\*]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [\*]*

**Diagnoseinformationen**

* Live-Kernelabbild
* Sammeln von AppX-Paketinformationen
* UIF-Kontextdateien

**Ereignisprotokolle**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Probleme bei der Bereitstellung

Verwenden Sie zur Behebung von Problemen bei der Bereitstellung des Azure AD Kerberos-Servers das neue PowerShell-Modul, das in Azure AD Connect enthalten ist.

#### <a name="viewing-the-logs"></a>Anzeigen der Protokolle

Die PowerShell-Cmdlets für den Azure AD Kerberos-Server verwenden dieselbe Protokollierung wie der standardmäßige Azure AD Connect-Assistent. Führen Sie zum Anzeigen von Informationen oder Fehlerdetails aus den Cmdlets die folgenden Schritte aus:

1. Navigieren Sie auf dem Server mit Azure AD Connect zu `C:\ProgramData\AADConnect\`. Dieser Ordner ist standardmäßig ausgeblendet.
1. Öffnen Sie die neueste `trace-*.log`-Datei im Verzeichnis, und zeigen Sie sie an.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Anzeigen der Azure AD Kerberos-Serverobjekte

Wenn Sie die Azure AD Kerberos-Serverobjekte anzeigen und überprüfen möchten, ob sie ordnungsgemäß ausgeführt werden, führen Sie die folgenden Schritte aus:

1. Öffnen Sie auf dem Azure AD Connect-Server PowerShell, und navigieren Sie zu `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`.
1. Führen Sie die folgenden PowerShell-Befehle aus, um den Azure AD Kerberos-Server sowohl aus Azure AD als auch aus lokalem AD DS anzuzeigen.

    Ersetzen Sie *corp.contoso.com* durch den Namen Ihrer lokalen AD DS-Domäne.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

Mit dem Befehl werden die Eigenschaften des Azure AD Kerberos-Servers sowohl aus Azure AD als auch dem lokalen AD DS ausgegeben. Überprüfen Sie die Eigenschaften, um sich zu vergewissern, dass alles in Ordnung ist. Verwenden Sie die folgende Tabelle zum Überprüfen der Eigenschaften.

Der erste Satz von Eigenschaften stammt aus den Objekten in der lokalen AD DS-Umgebung. Die zweite Hälfte (die Eigenschaften, die mit „*Cloud**“ anfangen) stammen aus dem Kerberos-Serverobjekt in Azure AD:

| Eigenschaft           | BESCHREIBUNG  |
|--------------------|--------------|
| Id                 | Die eindeutige *ID* des AD DS-Domänencontrollerobjekts. |
| DomainDnsName      | Der DNS-Domänenname der AD DS-Domäne. |
| ComputerAccount    | Das Computerkontoobjekt des Azure AD Kerberos-Serverobjekts (der DC). |
| UserAccount        | Das deaktivierte Benutzerkontoobjekt, das den TGT-Verschlüsselungsschlüssel des Azure AD Kerberos-Servers enthält. Der DN dieses Kontos lautet: *CN=krbtgt_AzureAD,CN=Users,<Domain-DN>* . |
| KeyVersion         | Die Schlüsselversion des TGT-Verschlüsselungsschlüssels des Azure AD Kerberos-Servers. Die Version wird beim Erstellen des Schlüssels zugewiesen. Die Version wird dann bei jeder Rotation des Schlüssels inkrementiert. Die Inkremente basieren auf Replikationsmetadaten und werden wahrscheinlich größer als ein Inkrement sein.<br /><br /> Die anfängliche Schlüsselversion (*KeyVersion*) könnte beispielsweise *192272* lauten. Bei der ersten Rotation des Schlüssels könnte die Version auf *212621* erhöht werden.<br /><br /> Sie müssen unbedingt überprüfen, ob die *KeyVersion* für das lokale Objekt und die *CloudKeyVersion* für das Cloudobjekt identisch sind. |
| KeyUpdatedOn       | Das Datum und die Uhrzeit der Aktualisierung oder Erstellung des TGT-Verschlüsselungsschlüssels des Azure AD Kerberos-Servers. |
| KeyUpdatedFrom     | Der DC, auf dem der TGT-Verschlüsselungsschlüssel des Azure AD Kerberos-Servers zuletzt aktualisiert wurde. |
| CloudId            | Die *ID* aus dem Azure AD-Objekt. Sie muss der obigen *ID* entsprechen. |
| CloudDomainDnsName | Der *DomainDnsName* aus dem Azure AD-Objekt. Dieser muss dem obigen *DomainDnsName* entsprechen. |
| CloudKeyVersion    | Die *KeyVersion* aus dem Azure AD-Objekt. Diese muss der obigen *KeyVersion* entsprechen. |
| CloudKeyUpdatedOn  | Der *KeyUpdatedOn*-Wert aus dem Azure AD-Objekt. Dieser muss dem obigen *KeyUpdatedOn*-Wert entsprechen. |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit FIDO2-Sicherheitsschlüsseln und dem Hybridzugriff auf lokale Ressourcen finden Sie in den folgenden Artikeln:

* [Kennwortlose FIDO2-Sicherheitsschlüssel](howto-authentication-passwordless-security-key.md)
* [Kennwortlos: Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Kennwortlos: lokal](howto-authentication-passwordless-security-key-on-premises.md)
