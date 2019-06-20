---
title: 'Azure Active Directory Domain Services: Synchronisierung in verwalteten Domänen | Microsoft-Dokumentation'
description: Grundlegendes zur Synchronisierung in einer durch Azure Active Directory Domain Services verwalteten Domäne
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 295a991e610e76971413a2abdba1e2fcc5f9eba6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245403"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronisierung in einer durch Azure AD Domain Services verwalteten Domäne
In der folgenden Abbildung ist die Synchronisierung in durch Azure AD Domain Services verwalteten Domänen dargestellt.

![Synchronisierung in Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronisierung aus dem lokalen Verzeichnis mit dem Azure AD-Mandanten
Mithilfe der Azure AD Connect-Synchronisierung werden Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationshashes mit dem Azure AD-Mandanten synchronisiert. Die Attribute von Benutzerkonten wie der UPN und die lokale SID (Sicherheits-ID) werden synchronisiert. Wenn Sie Azure AD Domain Services verwenden, werden ältere Anmeldeinformationshashes, die für die NTLM- und Kerberos-Authentifizierung benötigt werden, auch mit dem Azure AD-Mandanten synchronisiert.

Wenn Sie das Zurückschreiben konfigurieren, werden Änderungen im Azure AD-Verzeichnis wieder mit dem lokalen Active Directory synchronisiert. Wenn Sie beispielsweise Ihr Kennwort mit der Azure AD-Verwaltung zur Self-Service-Kennwortänderung ändern, wird das geänderte Kennwort in der lokalen AD-Domäne aktualisiert.

> [!NOTE]
> Verwenden Sie immer die neueste Version von Azure AD Connect, um sicherzustellen, dass Sie über die Fehlerbehebungen für alle bekannten Fehler verfügen.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronisierung aus dem Azure AD-Mandanten mit der verwalteten Domäne
Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationshashes werden aus dem Azure AD-Mandanten mit der durch Azure AD Domain Services verwalteten Domäne synchronisiert. Dieser Synchronisierungsvorgang erfolgt automatisch. Sie müssen diesen Vorgang nicht konfigurieren, überwachen oder verwalten. Die erste Synchronisierung kann von einigen wenigen Stunden bis hin zu mehreren Tagen dauern – dies hängt von der Anzahl von Objekten in Ihrem Azure AD-Verzeichnis ab. Nach Abschluss der anfänglichen Synchronisierung dauert es in der Regel etwa 20–30 Minuten, bis in Azure AD durchgeführte Änderungen in Ihrer verwalteten Domäne wirksam werden. Dieses Synchronisierungsintervall gilt für Kennwortänderungen oder Änderungen an Attributen, die in Azure AD vorgenommen werden.

Der Synchronisierungsvorgang wird zudem unidirektional durchgeführt. Die verwaltete Domäne ist größtenteils schreibgeschützt, mit Ausnahme der benutzerdefinierten Organisationseinheiten, die Sie erstellen. Aus diesem Grund können Sie keine Änderungen an den Benutzerattributen, Benutzerkennwörtern oder Gruppenmitgliedschaften in der verwalteten Domäne vornehmen. Daher erfolgt keine umgekehrte Synchronisierung der Änderungen aus der verwalteten Domäne mit dem Azure AD-Mandanten.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronisierung aus einer lokalen Umgebung mit mehreren Gesamtstrukturen
Viele Organisationen verfügen über eine relativ komplexe lokale Identitätsinfrastruktur mit mehreren Kontogesamtstrukturen. Azure AD Connect unterstützt die Synchronisierung von Benutzern, Gruppen und Anmeldeinformationshashes aus Umgebungen mit mehreren Gesamtstrukturen mit dem Azure AD-Mandanten.

Im Gegensatz dazu handelt es sich bei dem Azure AD-Mandanten um einen viel einfacheren und flachen Namespace. Damit Benutzer verlässlich auf durch Azure AD gesicherte Anwendungen zugreifen können, müssen UPN-Konflikte in den Benutzerkonten in den unterschiedlichen Gesamtstrukturen behoben werden. Die durch Azure AD Domain Services verwaltete Domäne hat eine große Ähnlichkeit mit dem Azure AD-Mandanten. Sie sehen eine flache OE-Struktur in Ihrer verwalteten Domäne. Alle Benutzerkonten und -gruppen werden ungeachtet der Synchronisierung aus verschiedenen lokalen Domänen oder Gesamtstrukturen im Container „AADDC Users“ gespeichert. Möglicherweise haben Sie eine hierarchische lokale OE-Struktur konfiguriert. Ihre verwaltete Domäne weist weiterhin eine einfache, flache OE-Struktur auf.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Ausschlüsse – Objekte oder Attribute, die nicht mit der verwalteten Domäne synchronisiert werden
Die folgenden Objekte oder Attribute werden nicht mit dem Azure AD-Mandanten oder mit der verwalteten Domäne synchronisiert:

* **Ausgeschlossene Attribute:** Sie können festlegen, dass bestimmte Attribute von der Synchronisierung mithilfe von Azure AD Connect aus der lokalen Domäne mit dem Azure AD-Mandanten ausgeschlossen werden. Diese ausgeschlossenen Attribute sind in der verwalteten Domäne nicht verfügbar.
* **Gruppenrichtlinien:** Die in der lokalen Domäne konfigurierten Gruppenrichtlinien werden nicht mit der verwalteten Domäne synchronisiert.
* **SYSVOL-Freigabe:** Ebenso werden die Inhalte der SYSVOL-Freigabe in der lokalen Domäne nicht mit der verwalteten Domäne synchronisiert.
* **Computerobjekte:** Computerobjekte für in die lokale Domäne eingebundene Computer werden nicht mit der verwalteten Domäne synchronisiert. Diese Computer weisen keine Vertrauensstellung mit der verwalteten Domäne auf und gehören lediglich der lokalen Domäne an. In der verwalteten Domäne finden Sie nur Computerobjekte für Computer, die Sie ausdrücklich in die verwaltete Domäne eingebunden haben.
* **SidHistory-Attribute für Benutzer und Gruppen:** Die primären Benutzer-SIDs und primären Gruppen-SIDs aus der lokalen Domäne werden mit der verwalteten Domäne synchronisiert. Allerdings werden vorhandene SidHistory-Attribute für Benutzer und Gruppen nicht aus der lokalen Domäne mit der verwalteten Domäne synchronisiert.
* **OE-Strukturen (Organisationseinheiten):** Die in der lokalen Domäne definierten Organisationseinheiten werden nicht mit der verwalteten Domäne synchronisiert. Die verwaltete Domäne enthält zwei integrierte Organisationseinheiten. Standardmäßig weist die verwaltete Domäne eine flache OE-Struktur auf. Sie können jedoch [eine benutzerdefinierte Organisationseinheit in der verwalteten Domäne erstellen](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Synchronisierung bestimmter Attribute mit der verwalteten Domäne
In der folgenden Tabelle sind einige allgemeine Attribute sowie entsprechende Beschreibungen dazu aufgeführt, wie die Synchronisierung mit der verwalteten Domäne erfolgt.

| Attribut in der verwalteten Domäne | `Source` | Notizen |
|:--- |:--- |:--- |
| UPN |UPN-Attribut des Benutzers im Azure AD-Mandanten |Das UPN-Attribut aus dem Azure AD-Mandanten wird unverändert mit der verwalteten Domäne synchronisiert. Daher ist die Verwendung des UPN die zuverlässigste Möglichkeit der Anmeldung in der verwalteten Domäne. |
| SAMAccountName |mailNickname-Attribut des Benutzers im Azure AD-Mandanten oder automatisch generiert |Das SAMAccountName-Attribut wird aus dem mailNickname-Attribut im Azure AD-Mandanten erstellt. Wenn mehrere Benutzerkonten dasselbe mailNickname-Attribut aufweisen, wird das SAMAccountName-Attribut automatisch generiert. Wenn das mailNickname-Attribut oder das UPN-Präfix des Benutzers länger als 20 Zeichen ist, wird das SAMAccountName-Attribut automatisch generiert, wobei die Beschränkung von 20 Zeichen für SAMAccountName-Attribute eingehalten wird. |
| Kennwörter |Kennwort des Benutzers aus dem Azure AD-Mandanten |Anmeldeinformationshashes, die für die NTLM- oder Kerberos-Authentifizierung benötigt werden (auch als ergänzende Anmeldeinformationen bezeichnet), werden aus dem Azure AD-Mandanten synchronisiert. Wenn es sich bei dem Azure AD-Mandanten um einen synchronisierten Mandanten handelt, werden diese Anmeldeinformationen aus der lokalen Domäne erstellt. |
| Primäre Benutzer-/Gruppen-SID |Automatisch generiert |Die primäre SID für Benutzer- und Gruppenkonten wird in der verwalteten Domäne automatisch generiert. Dieses Attribut entspricht nicht der primären Benutzer-/Gruppen-SID des Objekts in der lokalen AD-Domäne. Sie stimmen nicht überein, da die verwaltete Domäne einen anderen SID-Namespace als die lokale Domäne aufweist. |
| SID-Verlauf für Benutzer und Gruppen |Lokale primäre Benutzer- und Gruppen-SID |Das SidHistory-Attribut für Benutzer und Gruppen in der verwalteten Domäne ist so festgelegt, dass es mit der entsprechenden primären Benutzer- oder Gruppen-SID in der lokalen Domäne übereinstimmt. Diese Funktion erleichtert die Verlagerung lokaler Anwendungen in die verwaltete Domäne, da Sie keine neuen Zugriffssteuerungslisten für Ressourcen einrichten müssen. |

> [!NOTE]
> **Anmeldung in der verwalteten Domäne mit dem UPN-Format:** Das SAMAccountName-Attribut wird für einige Benutzerkonten in der verwalteten Domäne möglicherweise automatisch generiert. Wenn mehrere Benutzer dasselbe mailNickname-Attribut aufweisen oder Benutzer übermäßig lange UPN-Präfixe aufweisen, wird das SAMAccountName-Attribut für diese Benutzer möglicherweise automatisch generiert. Daher ist das Format für SAMAccountName (z.B. „CONTOSO100\joeuser“) nicht immer eine verlässliche Möglichkeit für die Anmeldung in der Domäne. Das automatisch generierte SAMAccountName-Attribut von Benutzern kann von ihrem UPN-Präfix abweichen. Verwenden Sie das UPN-Format (z.B. „joeuser@contoso100.com“) für die zuverlässige Anmeldung bei der verwalteten Domäne.

### <a name="attribute-mapping-for-user-accounts"></a>Attributzuordnung für Benutzerkonten
In der folgenden Tabelle ist dargestellt, wie bestimmte Attribute für Benutzerobjekte im Azure AD-Mandanten mit den entsprechenden Attributen in der verwalteten Domäne synchronisiert werden.

| Benutzerattribut im Azure AD-Mandanten | Benutzerattribut in der verwalteten Domäne |
|:--- |:--- |
| accountEnabled |userAccountControl (legt das ACCOUNT_DISABLED-Bit fest oder löscht es) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (wird in einigen Fällen möglicherweise automatisch generiert) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (legt das DONT_EXPIRE_PASSWORD-Bit fest oder löscht es) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attributzuordnung für Gruppen
In der folgenden Tabelle ist dargestellt, wie bestimmte Attribute für Gruppenobjekte im Azure AD-Mandanten mit den entsprechenden Attributen in der verwalteten Domäne synchronisiert werden.

| Gruppenattribut im Azure AD-Mandanten | Gruppenattribut in der verwalteten Domäne |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (wird in einigen Fällen möglicherweise automatisch generiert) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Kennworthashsynchronisierung und Sicherheitsüberlegungen
Wenn Sie Azure AD Domain Services aktivieren, generiert und speichert Ihr Azure AD-Verzeichnis Kennworthashes in NTLM- und Kerberos-kompatiblen Formaten. 

Für vorhandene Cloudbenutzerkonten können diese Hashes nicht automatisch generiert werden, weil Azure AD niemals die zugehörigen Klartextkennwörter speichert. Microsoft fordert deshalb [Cloudbenutzer auf, ihre Kennwörter zurückzusetzen/zu ändern](active-directory-ds-getting-started-password-sync.md), damit die Kennworthashes in Azure AD generiert und gespeichert werden können. Für alle Cloudbenutzerkonten, die nach dem Aktivieren von Azure AD Domain Services in Azure AD erstellt werden, werden die Kennworthashes in den NTLM- und Kerberos-kompatiblen Formaten generiert und gespeichert. 

Für Benutzerkonten, die unter Verwendung von Azure AD Connect Sync aus lokalen AD-Verzeichnissen synchronisiert werden, müssen Sie [Azure AD Connect zum Synchronisieren von Kennworthashes in den NTLM- und Kerberos-kompatiblen Formaten konfigurieren](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Die NTLM- und Kerberos-kompatiblen Kennworthashes werden immer verschlüsselt in Azure AD gespeichert. Die Hashes werden so verschlüsselt, dass nur Azure AD Domain Services Zugriff auf die Entschlüsselungsschlüssel hat. Andere Dienste oder Komponenten in Azure AD können nicht auf die Entschlüsselungsschlüssel zugreifen. Die Verschlüsselungsschlüssel sind für jeden Azure AD-Mandanten eindeutig. Azure AD Domain Services synchronisiert die Kennworthashes mit den Domänencontrollern für Ihre verwaltete Domäne. Diese Kennworthashes werden auf diesen Domänencontrollern in ähnlicher Weise gespeichert und geschützt wie Kennwörter auf Windows Server-AD-Domänencontrollern. Die Datenträger für diese verwalteten Domänencontroller werden im Ruhezustand verschlüsselt.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objekte, die aus der verwalteten Domäne nicht mit dem Azure AD-Mandanten synchronisiert werden
Wie in einem vorherigen Abschnitt dieses Artikels beschrieben wurde, erfolgt keine Synchronisierung aus der verwalteten Domäne mit dem Azure AD-Mandanten. Sie können [eine benutzerdefinierte Organisationseinheit (OE)](create-ou.md) in der verwalteten Domäne erstellen. Darüber hinaus können Sie andere Organisationseinheiten, Benutzer, Gruppen oder Dienstkonten in diesen benutzerdefinierten Organisationseinheiten erstellen. Keines der in benutzerdefinierten Organisationseinheiten erstellten Objekte wird wieder mit dem Azure AD-Mandanten synchronisiert. Diese Objekte können nur in der verwalteten Domäne verwendet werden. Daher sind diese Objekte über Azure AD PowerShell-Cmdlets, die Azure AD Graph-API oder die Azure AD-Verwaltungsoberfläche nicht sichtbar.

## <a name="related-content"></a>Verwandte Inhalte
* [Features: Azure AD Domain Services](active-directory-ds-features.md)
* [Bereitstellungsszenarios: Azure AD Domain Services](scenarios.md)
* [Netzwerkaspekte für Azure AD Domain Services](network-considerations.md)
* [Erste Schritte mit Azure AD Domain Services](create-instance.md)
