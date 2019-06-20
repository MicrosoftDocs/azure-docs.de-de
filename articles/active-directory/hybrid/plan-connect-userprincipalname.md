---
title: Auffüllung des UserPrincipalName-Attributs in Azure AD
description: Im folgenden Dokument wird beschrieben, wie das UserPrincipalName-Attribut aufgefüllt wird.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c198b329f07c5c7459f25165b2dc0a3bfa032276
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60382066"
---
# <a name="azure-ad-userprincipalname-population"></a>Auffüllung des UserPrincipalName-Attributs in Azure AD

In diesem Artikel wird beschrieben, wie das UserPrincipalName-Attribut in Azure Active Directory (Azure AD) aufgefüllt wird.
Den Wert des UserPrincipalName-Attributs ist der Azure AD-Benutzername für die Benutzerkonten.

## <a name="upn-terminology"></a>Terminologie im Zusammenhang mit dem UPN
In diesem Artikel wird die folgende Terminologie verwendet:

|Begriff|BESCHREIBUNG|
|-----|-----|
|Anfangsdomäne|Die Standarddomäne (onmicrosoft.com) im Azure AD-Mandanten. Zum Beispiel contoso.onmicrosoft.com.|
|Microsoft Online Email Routing Address (MOERA)|Azure AD berechnet die MOERA anhand des MailNickName-Attributs in Azure AD und der Azure AD-Anfangsdomäne als &lt;MailNickName&gt;&#64;&lt;Anfangsdomäne&gt;.|
|Lokales mailNickName-Attribut|Ein Attribut in Active Directory, dessen Wert den Alias eines Benutzers in einer Exchange-Organisation darstellt|
|Lokales mail-Attribut|Ein Attribut in Active Directory, dessen Wert die E-Mail-Adresse eines Benutzers darstellt|
|Primäre SMTP-Adresse|Die primäre E-Mail-Adresse eines Exchange-Empfängerobjekts Beispiel: SMTP:user\@contoso.com.|
|Alternative Anmelde-ID|Ein anderes lokales Attribut als UserPrincipalName, z.B. das für die Anmeldung verwendete mail-Attribut|

## <a name="what-is-userprincipalname"></a>Was ist UserPrincipalName?
UserPrincipalName ist ein Attribut, d.h. ein Anmeldename im Internetformat für einen Benutzer basierend auf dem Internetstandard [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>UPN-Format
Ein UPN besteht aus einem UPN-Präfix (dem Benutzerkontonamen) und einem UPN-Suffix (einem DNS-Domänennamen). Das Präfix wird mithilfe des „\@“-Symbols mit dem Suffix verknüpft. Beispiel: jemand\@example.com. Ein UPN muss für alle Sicherheitsprinzipalobjekte innerhalb einer Verzeichnisgesamtstruktur eindeutig sein. 

## <a name="upn-in-azure-ad"></a>UPN in Azure AD 
Der UPN wird von Azure AD verwendet, um Benutzern die Anmeldung zu ermöglichen.  Die Auswahl der UPNs, die ein Benutzer verwenden kann, hängt davon ab, ob die Domäne überprüft wurde.  Wenn die Domäne überprüft wurde, kann sich ein Benutzer mit diesem Suffix bei Azure AD anmelden.  

Das Attribut wird durch Azure AD Connect synchronisiert.  Bei der Installation können Sie die Domänen anzeigen, die überprüft und nicht überprüft wurden.

   ![Nicht überprüfte Domänen](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternative Anmelde-ID
In einigen Umgebungen kennen Endbenutzer ggf. nur ihre E-Mail-Adresse und nicht ihren UPN.  Die Verwendung der E-Mail-Adresse kann aufgrund einer Unternehmensrichtlinie oder einer lokalen Branchenanwendungsabhängigkeit erfolgen.

Alternative Anmelde-IDs bieten die Möglichkeit, eine Anmeldeumgebung zu konfigurieren, in der sich Benutzer mit anderen Attributen als dem UPN anmelden können, z.B. mit der E-Mail-Adresse.

Um eine alternative Anmelde-ID in Azure AD zu aktivieren, sind bei der Verwendung von Azure AD Connect keine zusätzlichen Konfigurationsschritte erforderlich. Die alternative ID kann direkt anhand des Assistenten konfiguriert werden. Überprüfen Sie die Azure AD-Anmeldungskonfiguration für Ihre Benutzer im Abschnitt „Synchronisierung“. Wählen Sie in der Dropdownliste **Benutzerprinzipalname** das Attribut für die alternative Anmelde-ID aus.

![Nicht überprüfte Domänen](./media/plan-connect-userprincipalname/altloginid.png)  

Weitere Informationen finden Sie unter [Konfigurieren einer alternativen Anmelde-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) und [Konfiguration der Azure AD-Anmeldung](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Nicht überprüftes UPN-Suffix
Wenn das lokale UserPrincipalName-Attribut bzw. das Suffix für die alternative Anmelde-ID nicht mit dem Azure AD-Mandanten überprüft wird, wird der UserPrincipalName-Attributwert in Azure AD auf die MOERA festgelegt. Azure AD berechnet die MOERA anhand des MailNickName-Attributs in Azure AD und der Azure AD-Anfangsdomäne als &lt;MailNickName&gt;&#64;&lt;Anfangsdomäne&gt;.

## <a name="verified-upn-suffix"></a>Überprüftes UPN-Suffix
Wenn das lokale UserPrincipalName-Attribut bzw. das Suffix für die alternative Anmelde-ID nicht mit dem Azure AD-Mandanten überprüft wird, ist der UserPrincipalName-Attributwert in Azure AD mit dem lokalen UserPrincipalName-Attribut bzw. dem Wert der alternativen Anmelde-ID identisch.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Berechnung des MailNickName-Attributwerts in Azure AD
Da der Wert des UserPrincipalName-Attributs in Azure AD auf die MOERA festgelegt werden konnte, muss in jedem Fall erläutert werden, wie der MailNickName-Attributwert in Azure AD, d.h. das MOERA-Präfix, berechnet wird.

Wenn ein Benutzerobjekt zum ersten Mal mit einem Azure AD-Mandanten synchronisiert wurde, überprüft Azure AD die folgenden Elemente in der angegebenen Reihenfolge und legt den Wert des MailNickName-Attributs auf den ersten vorhandenen Wert fest:

- Lokales mailNickName-Attribut
- Präfix der primären SMTP-Adresse
- Präfix des lokalen mail-Attributs
- Präfix des lokalen userPrincipalName-Attributs bzw. der alternativen Anmelde-ID
- Präfix der sekundären SMTP-Adresse

Wenn die Updates für ein Benutzerobjekt mit dem Azure AD-Mandanten synchronisiert werden, aktualisiert Azure AD den MailNickName-Attributwert nur dann, wenn ein Update für den lokalen mailNickName-Attributwert verfügbar ist.

>[!IMPORTANT]
>Azure AD berechnet den Wert des UserPrincipalName-Attributs nur dann, wenn ein Update für den Wert des lokalen UserPrincipalName-Attributs bzw. der alternativen Anmelde-ID mit dem Azure AD-Mandanten synchronisiert wird. 
>
>Wenn Azure AD das UserPrincipalName-Attribut neu berechnet, wird auch die MOERA neu berechnet. 

## <a name="upn-scenarios"></a>UPN-Szenarien
Die folgenden Beispielszenarien zeigen, wie der UPN basierend auf dem vorgegebenen Szenario berechnet wird.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Szenario 1: Nicht überprüftes UPN-Suffix – Erstsynchronisierung

![Szenario1](./media/plan-connect-userprincipalname/example1.png)

Lokales Benutzerobjekt:
- mailNickName      : &lt;nicht festgelegt&gt;
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us3@contoso.com`

Zum ersten Mal mit dem Azure AD-Mandanten synchronisiertes Benutzerobjekt:
- Legen Sie das MailNickName-Attribut von Azure AD auf das Präfix der primären SMTP-Adresse fest.
- Legen Sie die MOERA auf &lt;MailNickName&gt;&#64;&lt;Anfangsdomäne&gt; fest.
- Legen Sie das UserPrincipalName-Attribut in Azure AD auf die MOERA fest.

Benutzerobjekt des Azure AD-Mandanten:
- MailNickName      : us1           
- UserPrincipalName : us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Szenario 2: Nicht überprüftes UPN-Suffix – Festlegen eines lokalen mailNickName-Attributs

![Szenario2](./media/plan-connect-userprincipalname/example2.png)

Lokales Benutzerobjekt:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us3@contoso.com

Synchronisieren von Updates für das lokale mailNickName-Attribut mit dem Azure AD-Mandanten
- Aktualisieren Sie das MailNickName-Attribut in Azure AD auf ein lokales mailNickName-Attribut.
- Da kein Update für das lokale userPrincipalName-Attribut vorhanden ist, bleibt das UserPrincipalName-Attribut in Azure AD unverändert.

Benutzerobjekt des Azure AD-Mandanten:
- MailNickName      : us4
- UserPrincipalName : us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Szenario 3: Nicht überprüftes UPN-Suffix – Aktualisieren des lokalen userPrincipalName-Attributs

![Szenario3](./media/plan-connect-userprincipalname/example3.png)

Lokales Benutzerobjekt:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us5@contoso.com

Synchronisieren von Updates für das lokale userPrincipalName-Attribut mit dem Azure AD-Mandanten:
- Ein Update für ein lokales userPrincipalName-Attribut löst eine Neuberechnung der MOERA und des UserPrincipalName-Attributs in Azure AD aus.
- Legen Sie die MOERA auf &lt;MailNickName&gt;&#64;&lt;Anfangsdomäne&gt; fest.
- Legen Sie das UserPrincipalName-Attribut in Azure AD auf die MOERA fest.

Benutzerobjekt des Azure AD-Mandanten:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Szenario 4: Nicht überprüftes UPN-Suffix – Aktualisieren der primären SMTP-Adresse und des lokalen mail-Attributs

![Szenario4](./media/plan-connect-userprincipalname/example4.png)

Lokales Benutzerobjekt:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- mail          : us7@contoso.com
- userPrincipalName : us5@contoso.com

Synchronisieren von Updates für das lokale mail-Attribut und die primäre SMTP-Adresse im Azure AD-Mandanten
- Nach der Erstsynchronisierung des Benutzerobjekts wirken sich Updates für das lokale mail-Attribut und die primäre SMTP-Adresse nicht auf das MailNickName- oder das UserPrincipalName-Attribut in Azure AD aus.

Benutzerobjekt des Azure AD-Mandanten:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Szenario 5: Nicht überprüftes UPN-Suffix – Aktualisieren des lokalen userPrincipalName-Attributsuffixes

![Szenario5](./media/plan-connect-userprincipalname/example5.png)

Lokales Benutzerobjekt:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- mail          : us7@contoso.com
- userPrincipalName : us5@verified.contoso.com

Synchronisieren von Updates für das lokale userPrincipalName-Attribut mit dem Azure AD-Mandanten:
- Updates für das lokale userPrincipalName-Attribut lösen eine Neuberechnung des Azure AD UserPrincipalName-Attributs aus.
- Legen Sie das UserPrincipalName-Attribut von Azure AD auf ein lokales userPrincipalName-Attribut fest, da das UPN-Suffix mit dem Azure AD-Mandanten überprüft wird.

Benutzerobjekt des Azure AD-Mandanten:
- MailNickName      : us4     
- UserPrincipalName : us5@verified.contoso.com

## <a name="next-steps"></a>Nächste Schritte
- [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](whatis-hybrid-identity.md)
- [Benutzerdefinierte Installation von Azure AD Connect](how-to-connect-install-custom.md)
