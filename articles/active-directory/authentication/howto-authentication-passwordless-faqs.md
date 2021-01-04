---
title: 'Häufig gestellte Fragen zur Bereitstellung hybrider FIDO2-Sicherheitsschlüssel: Azure Active Directory'
description: Hier finden Sie einige häufig gestellte Fragen zur kennwortlosen Anmeldung mit hybridem FIDO2-Sicherheitsschlüssel unter Verwendung von Azure Active Directory (Vorschau).
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
ms.openlocfilehash: 98cb990ede7c4d6e261bba05b0b8c97d758e6c32
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743529"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>Häufig gestellte Fragen (FAQs) zur Bereitstellung hybrider FIDO2-Sicherheitsschlüssel in Azure AD

In diesem Artikel werden häufig gestellte Fragen (Frequently Asked Questions, FAQs) zur Bereitstellung von in Azure AD eingebundenen Hybridgeräten und zur kennwortlosen Anmeldung bei lokalen Ressourcen behandelt. Bei diesem kennwortlosen Feature können Sie die Azure AD-Authentifizierung auf Windows 10-Geräten für in Azure AD eingebundene Hybridgeräte mithilfe von FIDO2-Sicherheitsschlüsseln aktivieren. Benutzer können sich auf ihren Geräten mit modernen Anmeldeinformationen wie FIDO2-Schlüsseln bei Windows anmelden und auf herkömmliche AD DS-basierte Ressourcen (Active Directory Domain Services) zugreifen, indem sie für ihre lokalen Ressourcen nahtloses einmaliges Anmelden (Single Sign-On, SSO) nutzen.

Für Benutzer in einer Hybridumgebung werden folgende Szenarien unterstützt:

* Anmelden mit FIDO2-Sicherheitsschlüsseln bei in Azure AD eingebundenen Hybridgeräten und Erhalten von SSO-Zugriff auf lokale Ressourcen
* Anmelden mit FIDO2-Sicherheitsschlüsseln bei in Azure AD eingebundenen Geräten und Erhalten von SSO-Zugriff auf lokale Ressourcen

Informationen zu den ersten Schritten mit FIDO2-Sicherheitsschlüsseln und dem Hybridzugriff auf lokale Ressourcen finden Sie in den folgenden Artikeln:

* [Kennwortlose FIDO2-Sicherheitsschlüssel](howto-authentication-passwordless-security-key.md)
* [Kennwortlos: Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Kennwortlos: lokal](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2-Sicherheitsschlüssel sind eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="security-keys"></a>Sicherheitsschlüssel

* [Meine Organisation erfordert die Verwendung der zweistufigen Authentifizierung für den Zugriff auf Ressourcen. Was kann ich tun, um diese Anforderung zu unterstützen?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Wo finde ich konforme FIDO2-Sicherheitsschlüssel?](#where-can-i-find-compliant-fido2-security-keys)
* [Was ist zu tun, wenn ich meinen Sicherheitsschlüssel verliere?](#what-if-i-lose-my-security-key)
* [Wie werden die Daten im FIDO2-Sicherheitsschlüssel geschützt?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Wie funktioniert die Registrierung von FIDO2-Sicherheitsschlüsseln?](#how-does-the-registering-of-fido2-security-keys-work)
* [Können Administratoren die Schlüssel direkt für die Benutzer bereitstellen?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Meine Organisation erfordert die Verwendung der mehrstufigen Authentifizierung für den Zugriff auf Ressourcen. Was kann ich tun, um diese Anforderung zu unterstützen?

FIDO2-Sicherheitsschlüssel sind in einer Vielzahl von Formfaktoren verfügbar. Wenden Sie sich an den jeweiligen Gerätehersteller, um zu erfahren, wie die Geräte mit einer PIN oder biometrisch als zweitem Faktor aktiviert werden können. Eine Liste mit unterstützten Anbietern finden Sie unter [FIDO2-Sicherheitsschlüsselanbieter](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Wo finde ich konforme FIDO2-Sicherheitsschlüssel?

Eine Liste mit unterstützten Anbietern finden Sie unter [FIDO2-Sicherheitsschlüsselanbieter](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>Was kann ich tun, wenn ich meinen Sicherheitsschlüssel verliere?

Sie können Schlüssel über das Azure-Portal entfernen, indem Sie zur Seite **Sicherheitsinformation** navigieren und den FIDO2-Sicherheitsschlüssel entfernen.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Wie werden die Daten im FIDO2-Sicherheitsschlüssel geschützt?

FIDO2-Sicherheitsschlüssel verfügen über sichere Enclaves zum Schutz der darin gespeicherten privaten Schlüssel. Darüber hinaus verfügen FIDO2-Sicherheitsschlüssel über integrierte Anti-Hammering-Eigenschaften (wie etwa bei Windows Hello), um die Extraktion des privaten Schlüssels zu verhindern.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Wie funktioniert die Registrierung von FIDO2-Sicherheitsschlüsseln?

Weitere Informationen zur Registrierung und Verwendung von FIDO2-Sicherheitsschlüsseln finden Sie unter [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln (Vorschauversion)](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Können Administratoren die Schlüssel direkt für die Benutzer bereitstellen?

Nein, das ist derzeit nicht möglich.

## <a name="prerequisites"></a>Voraussetzungen

* [Funktioniert dieses Feature ohne Internetkonnektivität?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Welche spezifischen Endpunkte müssen für Azure AD geöffnet sein?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Wie kann ich den Domänenbeitrittstyp (in Azure AD eingebunden oder hybrid in Azure AD eingebunden) für mein Windows 10-Gerät ermitteln?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Wie lautet die Empfehlung hinsichtlich der Anzahl von Domänencontrollern, die gepatcht werden sollten?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Kann ich den FIDO2-Anmeldeinformationsanbieter auf einem rein lokalen Gerät bereitstellen?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [Die Anmeldung mit FIDO2-Sicherheitsschlüssel funktioniert für mein Domänenadministratorkonto oder für andere Konten mit hohen Berechtigungen nicht. Warum?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Funktioniert dieses Feature ohne Internetkonnektivität?

Internetkonnektivität ist erforderlich, um dieses Feature aktivieren zu können. Wenn sich ein Benutzer erstmals mit FIDO2-Sicherheitsschlüsseln anmeldet, muss er über Internetkonnektivität verfügen. Bei nachfolgenden Anmeldeereignissen sollte die zwischengespeicherte Anmeldung herangezogen werden und die Authentifizierung des Benutzers ohne Internetkonnektivität ermöglichen.

Stellen Sie sicher, dass die Geräte über Internetzugriff sowie über eine Sichtverbindung mit Domänencontrollern verfügen, um eine konsistente Erfahrung zu gewährleisten.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Welche spezifischen Endpunkte müssen für Azure AD geöffnet sein?

Für die Registrierung und Authentifizierung sind folgende Endpunkte erforderlich:

* * *.microsoftonline.com*
* * *.microsoftonline-p.com*
* * *.msauth.net*
* * *.msauthimages.net*
* * *.msecnd.net*
* * *.msftauth.net*
* * *.msftauthimages.net*
* * *.phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Eine vollständige Liste der Endpunkte, die für die Verwendung von Microsoft-Onlineprodukten erforderlich sind, finden Sie unter [Office 365-URLs und -IP-Adressbereiche](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Wie kann ich den Domänenbeitrittstyp (in Azure AD eingebunden oder hybrid in Azure AD eingebunden) für mein Windows 10-Gerät ermitteln?

Verwenden Sie den folgenden Befehl, um zu überprüfen, ob das Windows 10-Clientgerät über den richtigen Domänenbeitrittstyp verfügt:

```console
Dsregcmd/status
```

In der folgenden Beispielausgabe ist zu sehen, dass das Gerät in Azure AD eingebunden ist, da *AzureADJoined* auf *YES* (JA) festgelegt ist:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

In der folgenden Beispielausgabe ist zu sehen, dass das Gerät hybrid in Azure AD eingebunden ist, da auch *DomainedJoined* auf *YES* (JA) festgelegt ist: Außerdem wird der Domänenname (*DomainName*) angezeigt:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Vergewissern Sie sich bei einem Domänencontroller unter Windows Server 2016 oder 2019, dass die im Anschluss angegebenen Patches angewendet wurden. Führen Sie bei Bedarf Windows Update aus, um sie zu installieren:

* Windows Server 2016: [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019: [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

Führen Sie auf einem Clientgerät den folgenden Befehl aus, um die Konnektivität mit einem geeigneten Domänencontroller mit den installierten Patches zu überprüfen:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Wie lautet die Empfehlung hinsichtlich der Anzahl von Domänencontrollern, die gepatcht werden sollten?

Es empfiehlt sich, den Patch auf einen Großteil Ihrer Domänencontroller unter Windows Server 2016 oder 2019 anzuwenden, um sicherzustellen, dass sie die Authentifizierungsanforderungslast Ihrer Organisation bewältigen können.

Vergewissern Sie sich bei einem Domänencontroller unter Windows Server 2016 oder 2019, dass die im Anschluss angegebenen Patches angewendet wurden. Führen Sie bei Bedarf Windows Update aus, um sie zu installieren:

* Windows Server 2016: [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019: [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Kann ich den FIDO2-Anmeldeinformationsanbieter auf einem rein lokalen Gerät bereitstellen?

Nein. Dieses Feature wird für rein lokale Geräte nicht unterstützt. Der FIDO2 Anmeldeinformationsanbieter wird nicht angezeigt.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>Die Anmeldung mit FIDO2-Sicherheitsschlüssel funktioniert für mein Domänenadministratorkonto oder für andere Konten mit hohen Berechtigungen nicht. Warum?

Durch die Standardsicherheitsrichtlinie wird keine Azure AD-Berechtigung für die Anmeldung von Konten mit hohen Berechtigungen bei lokalen Ressourcen gewährt.

Verwenden Sie zum Aufheben der Kontenblockierung **Active Directory-Benutzer und -Computer**, um die Eigenschaft *msDS-NeverRevealGroup* des *Azure AD Kerberos-Computerobjekts (CN=AzureADKerberos,OU=Domain Controllers,\<domain-DN>)* zu ändern.

## <a name="under-the-hood"></a>Hinter den Kulissen

* [Wie ist Azure AD Kerberos mit meiner lokalen Active Directory Domain Services-Umgebung verknüpft?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Wo kann ich die Kerberos-Serverobjekte anzeigen, die in AD erstellt und in Azure AD veröffentlicht werden?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Warum kann der öffentliche Schlüssel nicht bei der lokalen AD DS-Instanz registriert werden, um vom Internet unabhängig zu sein?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Wie werden die Schlüssel für das Kerberos-Serverobjekt rotiert?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Warum ist Azure AD Connect erforderlich? Werden dadurch Informationen aus Azure AD in AD DS zurückgeschrieben?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Wie sieht die HTTP-Anforderung/-Antwort aus, wenn PRT und partielles TGT angefordert werden?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Wie ist Azure AD Kerberos mit meiner lokalen Active Directory Domain Services-Umgebung verknüpft?

Es gibt zwei Komponenten: die lokale AD DS-Umgebung und den Azure AD-Mandanten.

**Active Directory Domain Services (AD DS)**

Der Azure AD Kerberos-Server wird in einer lokalen AD DS-Umgebung als DC-Objekt (Domänencontroller) dargestellt. Dieses DC-Objekt setzt sich aus mehreren Objekten zusammen:

* *CN=AzureADKerberos,OU=Domain Controllers,\<domain-DN>*
    
    Ein Objekt vom Typ *Computer*, das einen schreibgeschützten Domänencontroller (Read-Only Domain Controller, RODC) in AD DS darstellt. Diesem Objekt ist kein Computer zugeordnet. Stattdessen handelt es sich um eine logische Darstellung eines DC.

* *CN=krbtgt_AzureAD,CN=Users,\<domain-DN>*

    Ein Objekt vom Typ *User* (Benutzer), das einen RODC-Kerberos-TGT-Verschlüsselungsschlüssel (Ticket Granting Ticket) darstellt.

* *CN=900274c4-b7d2-43c8-90ee-00a9f650e335,CN=AzureAD,CN=System,\<domain-DN>*

    Ein Objekt vom Typ *ServiceConnectionPoint* (Dienstverbindungspunkt) zum Speichern von Metadaten zu den Azure AD Kerberos-Serverobjekten. Dieses Objekt wird von den Verwaltungstools verwendet, um die Azure AD Kerberos-Serverobjekte zu identifizieren und zu suchen.

**Azure Active Directory**

Der Azure AD Kerberos-Server wird in Azure AD als Objekt vom Typ *KerberosDomain* (Kerberos-Domäne) dargestellt. Lokale AD DS-Umgebungen werden im Azure AD-Mandanten jeweils als einzelnes Objekt vom Typ *KerberosDomain* (Kerberos-Domäne) dargestellt.

So können Sie beispielsweise über eine AD DS-Gesamtstruktur mit zwei Domänen (beispielsweise *contoso.com* und *fabrikam.com*) verfügen. Wenn Sie für Azure AD die Ausstellung von Kerberos-TGTs (Ticket Granting Tickets) für die Gesamtstruktur zulassen, sind in Azure AD zwei Objekte vom Typ *KerberosDomain* (Kerberos-Domäne) vorhanden: eins für *contoso.com* und eins für *fabrikam.com*.

Wenn Sie über mehrere AD DS-Gesamtstrukturen verfügen, ist für jede Domäne in jeder Gesamtstruktur ein Objekt vom Typ *KerberosDomain* (Kerberos-Domäne) vorhanden.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Wo kann ich die Kerberos-Serverobjekte anzeigen, die in AD DS erstellt und in Azure AD veröffentlicht werden?

Verwenden Sie zum Anzeigen aller Objekte die PowerShell-Cmdlets für Azure AD Kerberos-Server aus der neuesten Version von Azure AD Connect.

Weitere Informationen finden Sie unter [Erstellen eines Kerberos-Serverobjekts](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object). Hier erfahren Sie auch wie Sie die Objekte anzeigen.

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Warum kann der öffentliche Schlüssel nicht bei der lokalen AD DS-Instanz registriert werden, um vom Internet unabhängig zu sein?

Wir haben Feedback zur Komplexität des Bereitstellungsmodells für Windows Hello for Business erhalten und wollten daher das Bereitstellungsmodell vereinfachen, um weder Zertifikate noch eine PKI verwenden zu müssen. (Von FIDO2 werden keine Zertifikate verwendet.)

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Wie werden die Schlüssel für das Kerberos-Serverobjekt rotiert?

Die Verschlüsselungsschlüssel vom Typ *krbtgt* des Azure AD Kerberos-Servers sollten genau wie bei anderen DCs regelmäßig rotiert werden. Dabei sollte der gleiche Zeitplan verwendet werden wie beim Rotieren aller anderen AD DS-Schlüssel vom Typ *krbtgt*.

> [!NOTE]
> Schlüssel vom Typ *krbtgt* können zwar auch mit anderen Tools rotiert werden, Sie müssen jedoch die [PowerShell-Cmdlets zum Rotieren der Schlüssel vom Typ *krbtgt*](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) Ihres Azure AD Kerberos-Servers verwenden. Dadurch wird sichergestellt, dass die Schlüssel sowohl in der lokalen AD DS-Umgebung als auch in Azure AD aktualisiert werden.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Warum ist Azure AD Connect erforderlich? Werden dadurch Informationen aus Azure AD in AD DS zurückgeschrieben?

Von Azure AD Connect werden keine Informationen aus Azure AD in AD DS zurückgeschrieben. Das Hilfsprogramm umfasst das PowerShell-Modul für die Erstellung des Kerberos-Serverobjekts in AD DS und die Veröffentlichung des Objekts in Azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Wie sieht die HTTP-Anforderung/-Antwort aus, wenn PRT und partielles TGT angefordert werden?

Die HTTP-Anforderung ist eine standardmäßige PRT-Anforderung (Primary Refresh Token, primäres Aktualisierungstoken). Diese PRT-Anforderung enthält einen Anspruch, der angibt, dass ein Kerberos Ticket Granting Ticket (TGT) benötigt wird.

| Anspruch | Wert | BESCHREIBUNG                             |
|-------|-------|-----------------------------------------|
| tgt   | true  | Der Anspruch gibt an, dass der Client ein TGT benötigt. |

Azure AD kombiniert den verschlüsselten Clientschlüssel und den Nachrichtenpuffer als zusätzliche Eigenschaften in der PRT-Antwort. Die Nutzlast wird unter Verwendung des Azure AD-Gerätesitzungsschlüssels verschlüsselt.

| Feld              | type   | BESCHREIBUNG  |
|--------------------|--------|--------------|
| tgt_client_key     | Zeichenfolge | Base64-codierter Clientschlüssel (Geheimnis). Dieser Schlüssel ist der geheime Clientschlüssel, der zum Schutz des TGT verwendet wird. In diesem kennwortlosen Szenario wird der geheime Clientschlüssel bei jeder TGT-Anforderung durch den Server generiert und dann in der Antwort an den Client zurückgegeben. |
| tgt_key_type       | INT    | Der lokale AD DS-Schlüsseltyp, der sowohl für den Clientschlüssel als auch für den in „KERB_MESSAGE_BUFFER“ enthaltenen Kerberos-Sitzungsschlüssel verwendet wird. |
| tgt_message_buffer | Zeichenfolge | KERB_MESSAGE_BUFFER (Base64-codiert). |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit FIDO2-Sicherheitsschlüsseln und dem Hybridzugriff auf lokale Ressourcen finden Sie in den folgenden Artikeln:

* [Kennwortlose FIDO2-Sicherheitsschlüssel](howto-authentication-passwordless-security-key.md)
* [Kennwortlos: Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Kennwortlos: lokal](howto-authentication-passwordless-security-key-on-premises.md)
