---
title: Kennwortlose Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie die kennwortlose Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen mit Azure Active Directory aktivieren (Vorschauversion).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1459442f7d99d1de88a685eed34493da530c1a4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743478"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen mit Azure Active Directory (Vorschauversion)

In diesem Dokument liegt der Schwerpunkt auf der Aktivierung der kennwortlosen Authentifizierung bei lokalen Ressourcen für Umgebungen mit **in Azure AD eingebundenen** und **in Hybrid-Azure AD eingebundenen** Windows 10-Geräten. Diese Funktion ermöglicht nahtloses einmaliges Anmelden (Single Sign-On, SSO) bei lokalen Ressourcen mithilfe von Microsoft-kompatiblen Sicherheitsschlüsseln.

> [!NOTE]
> FIDO2-Sicherheitsschlüssel sind eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Einmaliges Anmelden bei lokalen Ressourcen mithilfe von FIDO2-Schlüsseln

Azure Active Directory (Azure AD) kann Kerberos-Tickets für die Vergabe von Tickets (Kerberos Ticket Granting Tickets, TGTs) für eine oder mehrere Ihrer Active Directory-Domänen ausstellen. Diese Funktion ermöglicht Benutzern die Windows-Anmeldung mit modernen Anmeldeinformationen wie FIDO2-Sicherheitsschlüsseln und den Zugriff auf herkömmliche Active Directory-basierte Ressourcen. Kerberos-Diensttickets und die Autorisierung werden weiterhin von Ihren lokalen Active Directory-Domänencontrollern gesteuert.

Ein Azure AD Kerberos-Serverobjekt wird in Ihrer lokalen Active Directory-Instanz erstellt und dann sicher in Azure Active Directory veröffentlicht. Das Objekt ist keinem physischen Server zugeordnet. Es handelt sich dabei einfach um eine Ressource, die von Azure Active Directory verwendet werden kann, um Kerberos-TGTs (Ticket Granting Tickets) für Ihre Active Directory-Domäne zu generieren.

![Anfordern eines Ticket Granting Tickets (TGT) aus Azure AD und AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Der Benutzer meldet sich mit einem FIDO2-Sicherheitsschlüssel bei seinem Windows 10-Gerät an und authentifiziert sich bei Azure AD.
1. Azure AD überprüft das Verzeichnis auf einen Kerberos-Serverschlüssel, der mit der lokalen AD-Domäne des Benutzers übereinstimmt.
   1. Azure AD generiert ein Kerberos-TGT für die lokale AD-Domäne des Benutzers. Das TGT enthält nur die Sicherheits-ID (SID) des Benutzers. Das TGT enthält keine Autorisierungsdaten.
1. Das TGT wird zusammen mit dem primären Azure AD-Aktualisierungstoken (Primary Refresh Token, PRT) an den Client zurückgegeben.
1. Der Clientcomputer kontaktiert einen lokalen AD-Domänencontroller und tauscht das partielle TGT gegen ein vollständig geformtes TGT aus.
1. Der Clientcomputer verfügt jetzt über ein Azure AD-PRT und ein vollständiges Active Directory-TGT und kann auf Cloud- und lokale Ressourcen zugreifen.

## <a name="requirements"></a>Requirements (Anforderungen)

Organisationen müssen vor dem Ausführen der Schritte die im Artikel [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei Windows 10-Geräten (Vorschauversion)](howto-authentication-passwordless-security-key.md) aufgeführten Schritte ausführen.

Organisationen müssen auch die folgenden Softwareanforderungen erfüllen.

- Auf Geräten muss Windows 10 (2004 oder höher) ausgeführt werden.
- Sie müssen [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 1.4.32.0 oder eine höhere Version verwenden.
  - Weitere Informationen zu den verfügbaren Authentifizierungsoptionen für Ihre Azure AD-Hybridlösung finden Sie unter [Auswählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](../hybrid/choose-ad-authn.md) und [Auswählen des Installationstyps für Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Auf Ihren Windows Server-Domänencontrollern müssen die folgenden Patches installiert sein:
    - Für Windows Server 2016: https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Für Windows Server 2019: https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Unterstützte Szenarios

In den beiden folgenden Szenarien wird einmaliges Anmelden (Single Sign-On, SSO) unterstützt:

- Für Cloudressourcen wie Microsoft 365 und andere SAML-fähige Anwendungen.
- Für lokale Ressourcen und die Windows-integrierte Authentifizierung bei Websites. Zu den Ressourcen können Websites und SharePoint-Websites zählen, die IIS-Authentifizierung erfordern, und/oder Ressourcen, die die NTLM-Authentifizierung verwenden.

### <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

Folgende Szenarios werden nicht unterstützt:

- Eine Bereitstellung von Windows Server Active Directory Domain Services (AD DS) für in die Domäne eingebundene (rein lokale) Geräte.
- RDP-, VDI- und Citrix-Szenarien mit einem Sicherheitsschlüssel.
- S/MIME mit einem Sicherheitsschlüssel.
- „Ausführen als“ mit einem Sicherheitsschlüssel.
- Die Anmeldung bei einem Server mithilfe eines Sicherheitsschlüssels.

## <a name="create-kerberos-server-object"></a>Erstellen eines Kerberos-Serverobjekts

Administratoren verwenden PowerShell-Tools auf ihrem Azure AD Connect-Server, um ein Azure AD Kerberos-Serverobjekt in ihrem lokalen Verzeichnis zu erstellen. Führen Sie die folgenden Schritte in jeder Domäne und Gesamtstruktur in Ihrer Organisation aus, die Azure AD-Benutzer enthält:

1. Führen Sie ein Upgrade auf die aktuelle Version von Azure AD Connect durch. Bei den Anweisungen wird davon ausgegangen, dass Sie Azure AD Connect bereits für die Unterstützung Ihrer Hybridumgebung konfiguriert haben.
1. Öffnen Sie auf dem Azure AD Connect Server eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und navigieren Sie zu `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`.
1. Führen Sie die folgenden PowerShell-Befehle aus, um ein neues Azure AD Kerberos-Serverobjekt sowohl in Ihrer lokalen Active Directory-Domäne als auch im Azure Active Directory-Mandanten zu erstellen.

> [!NOTE]
> Ersetzen Sie `contoso.corp.com` im folgenden Beispiel durch den Namen Ihrer lokalen Active Directory-Domäne.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Anzeigen und Überprüfen des Azure AD Kerberos-Servers

Sie können den neu erstellten Azure AD Kerberos-Server mit dem folgenden Befehl anzeigen und überprüfen:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Mit diesem Befehl werden die Eigenschaften des Azure AD Kerberos-Servers ausgegeben. Sie können die Eigenschaften überprüfen, um sich zu vergewissern, dass alles in Ordnung ist.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| id | Die eindeutige ID des AD DS DC-Objekts. Diese ID wird manchmal als „Slot-ID“ oder „Branch-ID“ bezeichnet. |
| DomainDnsName | Der DNS-Domänenname der Active Directory-Domäne. |
| ComputerAccount | Das Computerkontoobjekt des Azure AD Kerberos-Serverobjekts (der DC). |
| UserAccount | Das deaktivierte Benutzerkontoobjekt, das den TGT-Verschlüsselungsschlüssel des Azure AD Kerberos-Servers enthält. Der DN dieses Kontos ist `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>`. |
| KeyVersion | Die Schlüsselversion des TGT-Verschlüsselungsschlüssels des Azure AD Kerberos-Servers. Die Version wird beim Erstellen des Schlüssels zugewiesen. Die Version wird dann bei jeder Rotation des Schlüssels inkrementiert. Die Inkremente basieren auf Replikationsmetadaten und werden wahrscheinlich größer als ein Inkrement sein. Die anfängliche Schlüsselversion (*KeyVersion*) könnte beispielsweise *192272* lauten. Bei der ersten Rotation des Schlüssels könnte die Version auf *212621* erhöht werden. Sie müssen unbedingt überprüfen, ob die *KeyVersion* für das lokale Objekt und die *CloudKeyVersion* für das Cloudobjekt identisch sind. |
| KeyUpdatedOn | Das Datum und die Uhrzeit der Aktualisierung oder Erstellung des TGT-Verschlüsselungsschlüssels des Azure AD Kerberos-Servers. |
| KeyUpdatedFrom | Der DC, auf dem der TGT-Verschlüsselungsschlüssel des Azure AD Kerberos-Servers zuletzt aktualisiert wurde. |
| CloudId | Die ID aus dem Azure AD-Objekt. Diese ID muss der obigen ID entsprechen. |
| CloudDomainDnsName | Der *DomainDnsName* aus dem Azure AD-Objekt. Dieser muss dem obigen *DomainDnsName* entsprechen. |
| CloudKeyVersion | Die *KeyVersion* aus dem Azure AD-Objekt. Diese muss der obigen *KeyVersion* entsprechen. |
| CloudKeyUpdatedOn | Der *KeyUpdatedOn*-Wert aus dem Azure AD-Objekt. Dieser muss dem obigen *KeyUpdatedOn*-Wert entsprechen. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotieren des Azure AD Kerberos-Serverschlüssels

Die KRBTGT-Verschlüsselungsschlüssel des Azure AD Kerberos-Servers sollten in regelmäßigen Abständen rotiert werden. Sie sollten denselben Zeitplan wie beim Rotieren aller anderen KRBTGT-Schlüssel von Active Directory-Domänencontrollern befolgen.

> [!WARNING]
> Es sind weitere Tools zum Rotieren von KRBTGT-Schlüsseln verfügbar. Sie müssen jedoch die in diesem Dokument erwähnten Tools verwenden, um die KRBTGT-Schlüssel Ihres Azure AD Kerberos-Servers zu rotieren. Dadurch wird sichergestellt, dass die Schlüssel sowohl in der lokalen AD-Instanz als auch in Azure AD aktualisiert werden.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Entfernen des Azure AD Kerberos-Servers

Wenn Sie das Szenario zurücksetzen und den Azure AD Kerberos-Server aus der lokalen Active Directory-Instanz und aus Azure Active Directory entfernen möchten, führen Sie den folgenden Befehl aus:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Szenarien mit mehreren Gesamtstrukturen und mehreren Domänen

Das Azure AD Kerberos-Serverobjekt wird in Azure AD als *KerberosDomain*-Objekt dargestellt. Jede lokale Active Directory-Domäne wird in Azure AD als einzelnes *KerberosDomain*-Objekt dargestellt.

Beispiel: Ihre Organisation verfügt über eine Active Directory-Gesamtstruktur mit zwei Domänen, `contoso.com` und `fabrikam.com`. Wenn Sie festlegen, dass Azure AD Kerberos-TGTs für die gesamte Gesamtstruktur ausstellen kann, sind in Azure AD zwei *KerberosDomain*-Objekte vorhanden. Ein *KerberosDomain*-Objekt für `contoso.com` und eines für `fabrikam.com`. Wenn Sie über mehrere Active Directory-Gesamtstrukturen verfügen, ist für jede Domäne in jeder Gesamtstruktur ein *KerberosDomain*-Objekt vorhanden.

Sie müssen die unter [Erstellen eines Kerberos-Serverobjekts](#create-kerberos-server-object) aufgeführten Schritte in jeder Domäne und Gesamtstruktur in Ihrer Organisation ausführen, die Azure AD-Benutzer enthält.

## <a name="known-behavior"></a>Bekanntes Verhalten

Die Anmeldung mit FIDO wird blockiert, wenn Ihr Kennwort abgelaufen ist. Es wird erwartet, dass der Benutzer sein Kennwort zurücksetzt, bevor er sich mithilfe von FIDO anmelden kann.

## <a name="troubleshooting-and-feedback"></a>Problembehandlung und Feedback

Wenn Sie Feedback geben möchten oder Probleme beim Anzeigen der Vorschau dieses Features auftreten, teilen Sie uns dies in folgenden Schritten über die Windows-Feedback-Hub-App mit:

1. Starten Sie **Feedback-Hub**, und stellen Sie sicher, dass Sie angemeldet sind.
1. Senden Sie Ihr Feedback unter der folgenden Kategorisierung:
   - Kategorie: Sicherheit und Datenschutz
   - Unterkategorie: FIDO
1. Zum Erfassen von Protokollen verwenden Sie die Option **Problem reproduzieren**.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="does-this-work-in-my-on-premises-environment"></a>Funktioniert das in meiner lokalen Umgebung?

In einer rein lokalen Active Directory Domain Services-Umgebung (AD DS-Umgebung) funktioniert dieses Feature nicht.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Meine Organisation verlangt die Verwendung der zweistufigen Authentifizierung für den Zugriff auf Ressourcen. Was kann ich tun, um diese Anforderung zu unterstützen?

Sicherheitsschlüssel sind in einer Vielzahl von Formfaktoren verfügbar. Wenden Sie sich an den jeweiligen Gerätehersteller, um zu erfahren, wie die Geräte mit einer PIN oder biometrisch als zweitem Faktor aktiviert werden können.

### <a name="can-admins-set-up-security-keys"></a>Können Administratoren Sicherheitsschlüssel einrichten?

Wir arbeiten im Rahmen der allgemeinen Verfügbarkeit (General Availability, GA) dieses Features an dieser Funktion.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Wo kann ich kompatible Sicherheitsschlüssel finden?

[FIDO2-Sicherheitsschlüssel](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Was ist zu tun, wenn ich meinen Sicherheitsschlüssel verliere?

Sie können im Azure-Portal Schlüssel entfernen, indem Sie zur **Sicherheitsinformationsseite** navigieren und den Sicherheitsschlüssel entfernen.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Ich kann FIDO nicht sofort nach dem Erstellen eines in Hybrid-Azure AD eingebundenen Computers verwenden.

Wenn Sie einen in Hybrid-Azure AD eingebundenen Computer neu installieren, danach einer Domäne hinzufügen und neu starten, müssen Sie sich mit einem Kennwort anmelden und warten, bis die Richtlinie synchronisiert wird, bevor Sie sich mithilfe von FIDO anmelden können.

- Überprüfen Sie den aktuellen Status, indem Sie in ein Befehlsfenster den Befehl `dsregcmd /status` eingeben, und überprüfen Sie, ob sowohl für *AzureAdJoined* als auch für *DomainJoined* der Wert *JA* angezeigt wird.
- Diese Verzögerung ist eine bekannte Einschränkung bei in die Domäne eingebundenen Geräten und nicht FIDO-spezifisch.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Ich kann nach der Anmeldung mit FIDO kein SSO für meine NTLM-Netzwerkressource nutzen und erhalte eine Aufforderung zur Eingabe von Anmeldeinformationen.

Stellen Sie sicher, dass genügend Domänencontroller gepatcht sind, damit sie rechtzeitig reagieren können, um Ihre Ressourcenanforderung zu bearbeiten. Wenn Sie überprüfen möchten, ob ein Domänencontroller angezeigt wird, auf dem die Funktion ausgeführt wird, sehen Sie sich die Ausgabe von `nltest /dsgetdc:contoso /keylist /kdc` an.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über die kennwortlose Authentifizierung](concept-authentication-passwordless.md)
