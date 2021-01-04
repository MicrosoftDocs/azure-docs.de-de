---
title: Funktionsweise der Synchronisierung in Azure AD Domain Services | Microsoft-Dokumentation
description: Funktionsweise des Synchronisierungsvorgangs für Objekte und Anmeldeinformationen aus einem Azure AD-Mandanten oder einer lokalen Active Directory Domain Services-Umgebung mit einer verwalteten Azure Active Directory Domain Services-Domäne.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 41ba337765b4a0a93be52f08ae6656707cf7aa73
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618806"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Synchronisieren von Objekten und Anmeldeinformationen in einer verwalteten Azure Active Directory Domain Services-Domäne

Objekte und Anmeldeinformationen in einer verwalteten Azure Active Directory Domain Services-Domäne (Azure AD DS) können lokal innerhalb der Domäne erstellt oder über einen Azure Active Directory-Mandanten (Azure AD) synchronisiert werden. Bei der ersten Bereitstellung von Azure AD DS wird eine automatische unidirektionale Synchronisierung konfiguriert und gestartet, um die Objekte aus Azure AD zu replizieren. Diese unidirektionale Synchronisierung wird weiterhin im Hintergrund ausgeführt, um die verwaltete Azure AD DS-Domäne mit allen Änderungen aus Azure AD auf dem neuesten Stand zu halten. Es findet keine Synchronisierung von Azure AD DS zurück zu Azure AD statt.

In einer Hybridumgebung können Objekte und Anmeldeinformationen aus einer lokalen AD DS-Domäne mithilfe von Azure AD Connect mit Azure AD synchronisiert werden. Nachdem diese Objekte und Anmeldeinformationen erfolgreich mit Azure AD synchronisiert wurden, werden sie mithilfe der automatischen Hintergrundsynchronisierung über die verwaltete Domäne in Anwendungen zur Verfügung gestellt.

Wenn lokale Instanzen von AD DS und Azure AD per AD FS für die Verbundauthentifizierung konfiguriert wurden, ist in Azure DS kein (aktueller/gültiger) Kennworthash vorhanden. Azure AD-Benutzerkonten, die vor der Implementierung der Verbundauthentifizierung erstellt wurden, verfügen unter Umständen über einen alten Kennworthash. Dieser entspricht jedoch wahrscheinlich keinem Hash ihres lokalen Kennworts. Die Anmeldeinformationen des Benutzers können daher nicht von Azure AD DS validiert werden.

In der folgenden Abbildung ist die Funktionsweise der Synchronisierung zwischen Azure AD DS, Azure AD und einer optionalen lokalen AD DS-Umgebung dargestellt:

![Übersicht über die Synchronisierung für eine verwaltete Azure AD Domain Services-Domäne](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synchronisierung aus Azure AD mit Azure AD DS

Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationshashes werden unidirektional aus Azure AD mit Azure AD DS synchronisiert. Dieser Synchronisierungsvorgang erfolgt automatisch. Sie müssen diesen Vorgang nicht konfigurieren, überwachen oder verwalten. Die erste Synchronisierung kann von einigen wenigen Stunden bis hin zu mehreren Tagen dauern – dies hängt von der Anzahl der Objekte im Azure AD-Verzeichnis ab. Nach Abschluss der Erstsynchronisierung werden in Azure AD vorgenommene Änderungen wie Kennwort- oder Attributänderungen automatisch mit Azure AD DS synchronisiert.

Wenn ein Benutzer in Azure AD erstellt wird, erfolgt die Synchronisierung mit Azure AD DS erst, wenn der Benutzer sein Kennwort in Azure AD ändert. Diese Kennwortänderung führt dazu, dass die Kennworthashes für die Kerberos- und NTLM-Authentifizierung in Azure AD generiert und gespeichert werden. Die Kennworthashes sind erforderlich, um einen Benutzer in Azure AD DS erfolgreich authentifizieren zu können.

Der Synchronisierungsvorgang wird unidirektional durchgeführt. Es erfolgt keine umgekehrte Synchronisierung von Änderungen aus Azure AD DS mit Azure AD. Eine verwaltete Domäne ist größtenteils schreibgeschützt, mit Ausnahme von benutzerdefinierten Organisationseinheiten, die Sie erstellen können. Sie können keine Änderungen an den Benutzerattributen, Benutzerkennwörtern oder Gruppenmitgliedschaften in einer verwalteten Domäne vornehmen.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synchronisierung und Zuordnung von Attributen mit Azure AD DS

In der folgenden Tabelle sind einige allgemeine Attribute und ihre Synchronisierung mit Azure AD DS aufgeführt.

| Attribut in Azure AD DS | `Source` | Notizen |
|:--- |:--- |:--- |
| UPN | *UPN*-Attribut des Benutzers im Azure AD-Mandanten | Das UPN-Attribut aus dem Azure AD-Mandanten wird unverändert mit Azure AD DS synchronisiert. Die Verwendung des UPN ist die zuverlässigste Möglichkeit der Anmeldung bei einer verwalteten Domäne. |
| SAMAccountName | *mailNickname*-Attribut des Benutzers im Azure AD-Mandanten oder automatisch generiert | Das *SAMAccountName*-Attribut wird aus dem *mailNickname*-Attribut im Azure AD-Mandanten erstellt. Wenn mehrere Benutzerkonten dasselbe *mailNickname*-Attribut aufweisen, wird das *SAMAccountName*-Attribut automatisch generiert. Wenn das *mailNickname*-Attribut oder das *UPN*-Präfix des Benutzers länger als 20 Zeichen ist, wird das *SAMAccountName*-Attribut automatisch generiert, wobei die Beschränkung von 20 Zeichen für *SAMAccountName*-Attribute eingehalten wird. |
| Kennwörter | Kennwort des Benutzers aus dem Azure AD-Mandanten | Legacykennworthashes, die für die NTLM- oder Kerberos-Authentifizierung erforderlich sind, werden aus dem Azure AD-Mandanten synchronisiert. Wenn der Azure AD-Mandant für die Hybridsynchronisierung mithilfe von Azure AD Connect konfiguriert ist, stammen die Kennworthashes aus der lokalen AD DS-Umgebung. |
| Primäre Benutzer-/Gruppen-SID | Automatisch generiert | Die primäre SID für Benutzer- und Gruppenkonten wird in Azure AD DS automatisch generiert. Dieses Attribut entspricht nicht der primären Benutzer- oder Gruppen-SID des Objekts in einer lokalen AD DS-Umgebung. Sie stimmen nicht überein, da die verwaltete Domäne einen anderen SID-Namespace als die lokale AD DS-Domäne aufweist. |
| SID-Verlauf für Benutzer und Gruppen | Lokale primäre Benutzer- und Gruppen-SID | Das *SidHistory*-Attribut für Benutzer und Gruppen in Azure AD DS ist so festgelegt, dass es mit der entsprechenden primären Benutzer- oder Gruppen-SID in einer lokalen AD DS-Umgebung übereinstimmt. Diese Funktion erleichtert die Verlagerung lokaler Anwendungen in Azure AD DS, da Sie keine neuen Zugriffssteuerungslisten für Ressourcen einrichten müssen. |

> [!TIP]
> **Anmeldung bei der verwalteten Domäne mit dem UPN-Format:** Das *SAMAccountName*-Attribut, z. B. `AADDSCONTOSO\driley`, wird für einige Benutzerkonten in einer verwalteten Domäne möglicherweise automatisch generiert. Das automatisch generierte *SAMAccountName*-Attribut von Benutzern kann sich von ihrem UPN-Präfix unterscheiden, sodass dies nicht immer eine zuverlässige Möglichkeit der Anmeldung ist.
>
> Wenn beispielsweise mehrere Benutzer dasselbe *mailNickname*-Attribut aufweisen oder Benutzer übermäßig lange UPN-Präfixe aufweisen, wird das *SAMAccountName*-Attribut für diese Benutzer möglicherweise automatisch generiert. Verwenden Sie das UPN-Format, z. B. `driley@aaddscontoso.com`, für die zuverlässige Anmeldung bei einer verwalteten Domäne.

### <a name="attribute-mapping-for-user-accounts"></a>Attributzuordnung für Benutzerkonten

In der folgenden Tabelle ist dargestellt, wie bestimmte Attribute für Benutzerobjekte in Azure AD mit den entsprechenden Attributen in Azure AD DS synchronisiert werden.

| Benutzerattribut in Azure AD | Benutzerattribut in Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (legt das ACCOUNT_DISABLED-Bit fest oder löscht es) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| employeedId |employeeId |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (wird in einigen Fällen möglicherweise automatisch generiert) |
| manager |manager |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (legt das DONT_EXPIRE_PASSWORD-Bit fest oder löscht es) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attributzuordnung für Gruppen

In der folgenden Tabelle ist dargestellt, wie bestimmte Attribute für Gruppenobjekte in Azure AD mit den entsprechenden Attributen in Azure AD DS synchronisiert werden.

| Gruppenattribut in Azure AD | Gruppenattribut in Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (wird in einigen Fällen möglicherweise automatisch generiert) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synchronisierung aus einer lokalen AD DS-Umgebung mit Azure AD und Azure AD DS

Mithilfe von Azure AD Connect werden Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationshashes aus einer lokalen AD DS-Umgebung mit Azure AD synchronisiert. Die Attribute von Benutzerkonten wie der UPN und die lokale Sicherheits-ID (SID) werden synchronisiert. Für die Anmeldung mithilfe von Azure AD DS werden Legacykennworthashes, die für die NTLM- und Kerberos-Authentifizierung erforderlich sind, auch mit Azure AD synchronisiert.

> [!IMPORTANT]
> Azure AD Connect sollte nur für die Synchronisierung mit lokalen AD DS-Umgebungen installiert und konfiguriert werden. Die Installation von Azure AD Connect in einer verwalteten Domäne zur erneuten Synchronisierung von Objekten mit Azure AD wird nicht unterstützt.

Wenn Sie das Zurückschreiben konfigurieren, werden Änderungen in Azure AD wieder mit der lokalen AD DS-Umgebung synchronisiert. Wenn ein Benutzer beispielsweise sein Kennwort mit der Azure AD-Self-Service-Kennwortverwaltung ändert, wird das Kennwort in der lokalen AD DS-Umgebung aktualisiert.

> [!NOTE]
> Verwenden Sie immer die neueste Version von Azure AD Connect, um sicherzustellen, dass Sie über die Fehlerbehebungen für alle bekannten Fehler verfügen.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronisierung aus einer lokalen Umgebung mit mehreren Gesamtstrukturen

Viele Organisationen verfügen über eine relativ komplexe lokale AD DS-Umgebung mit mehreren Kontogesamtstrukturen. Azure AD Connect unterstützt die Synchronisierung von Benutzern, Gruppen und Anmeldeinformationshashes aus Umgebungen mit mehreren Gesamtstrukturen mit Azure AD.

Azure AD umfasst einen viel einfacheren und flachen Namespace. Damit Benutzer verlässlich auf durch Azure AD gesicherte Anwendungen zugreifen können, müssen UPN-Konflikte in den Benutzerkonten in den unterschiedlichen Gesamtstrukturen behoben werden. Verwaltete Domänen weisen ähnlich wie Azure AD eine flache OE-Struktur auf. Alle Benutzerkonten und -gruppen werden ungeachtet der Synchronisierung aus verschiedenen lokalen Domänen oder Gesamtstrukturen im Container *AADDC Users* gespeichert, auch wenn Sie lokal eine hierarchische OE-Struktur konfiguriert haben. In der verwalteten Domäne werden alle hierarchischen OE-Strukturen vereinfacht.

Wie zuvor erläutert, erfolgt keine Synchronisierung aus Azure AD DS mit Azure AD. Sie können [benutzerdefinierte Organisationseinheiten (OE)](create-ou.md) in Azure AD DS und dann Benutzer, Gruppen oder Dienstkonten innerhalb dieser benutzerdefinierten Organisationseinheiten erstellen. Keines der in benutzerdefinierten Organisationseinheiten erstellten Objekte wird wieder mit Azure AD synchronisiert. Diese Objekte sind nur innerhalb der verwalteten Domäne verfügbar und über Azure AD-PowerShell-Cmdlets, die Microsoft Graph-API und die Azure AD-Verwaltungsoberfläche nicht sichtbar.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Objekte oder Attribute, die nicht mit Azure AD DS synchronisiert werden

Die folgenden Objekte oder Attribute werden nicht aus einer lokalen AD DS-Umgebung mit Azure AD oder Azure AD DS synchronisiert:

* **Ausgeschlossene Attribute:** Sie können festlegen, dass bestimmte Attribute von der Synchronisierung mit Azure AD aus einer lokalen AD DS-Umgebung mithilfe von Azure AD Connect ausgeschlossen werden. Diese ausgeschlossenen Attribute sind dann in Azure AD DS nicht verfügbar.
* **Gruppenrichtlinien:** Die in einer lokalen AD DS-Umgebung konfigurierten Gruppenrichtlinien werden nicht mit Azure AD DS synchronisiert.
* **Ordner „Sysvol“:** Die Inhalte des Ordners *Sysvol* in einer lokalen AD DS-Umgebung werden nicht mit Azure AD DS synchronisiert.
* **Computerobjekte:** Computerobjekte für Computer, die mit einer lokalen AD DS-Umgebung verknüpft sind, werden nicht mit Azure AD DS synchronisiert. Diese Computer weisen keine Vertrauensstellung mit der verwalteten Domäne auf und gehören lediglich der lokalen AD DS-Umgebung an. In Azure AD DS werden nur Computerobjekte für Computer angezeigt, die ausdrücklich in die verwaltete Domäne eingebunden wurden.
* **SidHistory-Attribute für Benutzer und Gruppen:** Die primären Benutzer-SIDs und primären Gruppen-SIDs aus einer lokalen AD DS-Umgebung werden mit Azure AD DS synchronisiert. Allerdings werden vorhandene *SidHistory*-Attribute für Benutzer und Gruppen nicht aus der lokalen AD DS-Umgebung mit Azure AD DS synchronisiert.
* **OE-Strukturen (Organisationseinheiten):** Organisationseinheiten, die in einer lokalen AD DS-Umgebung definiert sind, werden nicht mit Azure AD DS synchronisiert. Azure AD DS umfasst zwei integrierte Organisationseinheiten: eine für Benutzer und eine für Computer. Die verwaltete Domäne weist eine flache OE-Struktur auf. Sie können [eine benutzerdefinierte Organisationseinheit in der verwalteten Domäne erstellen](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Kennworthashsynchronisierung und Sicherheitsüberlegungen

Wenn Sie Azure AD DS aktivieren, sind Legacykennworthashes für die NTLM- und Kerberos-Authentifizierung erforderlich. In Azure AD werden keine Klartextkennwörter gespeichert, sodass diese Hashes für vorhandene Benutzerkonten nicht automatisch generiert werden können. Nach dem Generieren und Speichern werden NTLM- und Kerberos-kompatible Kennworthashes immer verschlüsselt in Azure AD gespeichert.

Die Verschlüsselungsschlüssel sind für jeden Azure AD-Mandanten eindeutig. Die Hashes werden so verschlüsselt, dass nur Azure AD DS Zugriff auf die Entschlüsselungsschlüssel hat. Andere Dienste oder Komponenten in Azure AD können nicht auf die Entschlüsselungsschlüssel zugreifen.

Legacykennworthashes werden dann aus Azure AD mit den Domänencontrollern für eine verwaltete Domäne synchronisiert. Die Datenträger für diese verwalteten Domänencontroller in Azure AD DS werden im Ruhezustand verschlüsselt. Die Kennworthashes werden auf diesen Domänencontrollern in ähnlicher Weise gespeichert und geschützt wie Kennwörter in einer lokalen AD DS-Umgebung.

Für reine Azure AD-Cloudumgebungen [müssen Benutzer ihr Kennwort zurücksetzen oder ändern](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds), damit die erforderlichen Kennworthashes in Azure AD generiert und gespeichert werden können. Für alle Cloudbenutzerkonten, die nach dem Aktivieren von Azure AD Domain Services in Azure AD erstellt werden, werden die Kennworthashes in den NTLM- und Kerberos-kompatiblen Formaten generiert und gespeichert. Für alle Cloudbenutzerkonten müssen die Kennwörter geändert werden, bevor sie mit Azure AD DS synchronisiert werden.

Für hybride Benutzerkonten, die unter Verwendung von Azure AD Connect aus der lokalen AD DS-Umgebung synchronisiert werden, müssen Sie [Azure AD Connect zum Synchronisieren von Kennworthashes in den NTLM- und Kerberos-kompatiblen Formaten konfigurieren](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Besonderheiten der Kennwortsynchronisierung finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

[Erstellen Sie eine verwaltete Domäne](tutorial-create-instance.md) für den Einstieg in Azure AD DS.
