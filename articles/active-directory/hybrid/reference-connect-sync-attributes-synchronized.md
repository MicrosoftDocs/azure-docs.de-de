---
title: Durch Azure AD Connect synchronisierte Attribute | Microsoft-Dokumentation
description: Listet die mit Azure Active Directory synchronisierten Attribute auf.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec05c4160c6502904644bf7035bda0bed66cc33
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413189"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect-Synchronisierung: Mit Azure Active Directory synchronisierte Attribute
In diesem Thema werden die Attribute aufgelistet, die bei der Azure AD Connect-Synchronisierung synchronisiert werden.  
Die Attribute sind nach den zugehörigen Azure AD-Apps gruppiert.

## <a name="attributes-to-synchronize"></a>Zu synchronisierende Attribute
Häufig wird die Frage gestellt, *welche Attribute als Mindestvoraussetzung synchronisiert werden müssen*. Die standardmäßige und empfohlene Vorgehensweise besteht darin, die Standardattribute beizubehalten, damit in der Cloud eine vollständige GAL (Globale Adressliste) erstellt werden kann und alle Features in Microsoft 365-Workloads abgerufen werden können. In einigen Fällen können Attribute vorhanden sein, die für Ihre Organisation nicht mit der Cloud synchronisiert werden sollen, da sie vertrauliche, personenbezogene Daten enthalten, wie etwa in diesem Beispiel:  
![Fehlerhafte Attribute](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

In diesem Fall beginnen Sie mit der in diesem Thema angegebenen Liste von Attributen und identifizieren die Attribute, die unter Umständen personenbezogene Informationen enthalten und nicht synchronisiert werden können. Deaktivieren Sie diese Attribute per [Azure AD-App- und Attributfilterung](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering) während der Installation.

> [!WARNING]
> Sie sollten beim Deaktivieren der Attribute äußerst vorsichtig vorgehen und nur diejenigen Attribute deaktivieren, die auf keinen Fall synchronisiert werden können. Das Deaktivieren von anderen Attributen kann eine negative Auswirkung auf Features haben.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Microsoft 365 Apps for Enterprise
| Attributname | Benutzer | Comment |
| --- |:---:| --- |
| accountEnabled |X |Definiert, ob ein Konto aktiviert ist. |
| cn |X | |
| displayName |X | |
| objectSID |X |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| pwdLastSet |X |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Wird von Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund verwendet. |
|samAccountName|X| |
| sourceAnchor |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| usageLocation |X |Mechanische Eigenschaft. Land/Region des Benutzers. Wird für die Lizenzzuweisung verwendet. |
| userPrincipalName |X |Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Attributname | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definiert, ob ein Konto aktiviert ist. |
| assistant |X |X | | |
| altRecipient |X | | |Erfordert Azure AD Connect Build 1.1.552.0 oder höher |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| info |X |X |X |Dieses Attribut wird derzeit nicht für Gruppen verwendet. |
| Initials |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Verfügbar in Azure AD Connect, Version 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute2 |X |X |X |Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute3 |X |X |X |Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute4 |X |X |X |Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchExtensionCustomAttribute5 |X |X |X |Dieses Attribut wird derzeit nicht für Exchange Online verwendet. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Von Kennwortsynchronisierung und Verbund verwendet. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Das Attribut wird nur einmal zwischen Azure AD und Exchange Online synchronisiert, nachdem Exchange Online zur Autoritätsquelle für dieses Attribut wurde, und spätere Änderungen am Attribut aus der lokalen Umgebung können nicht synchronisiert werden. Weitere Informationen finden Sie in [diesem KB-Artikel](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange).|
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |Mechanische Eigenschaft. Land/Region des Benutzers. Wird für die Lizenzzuweisung verwendet. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Attributname | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definiert, ob ein Konto aktiviert ist. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homePhone |X |X | | |
| info |X |X |X | |
| Initials |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| middleName |X |X | | |
| mobile |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Dieses Attribut wird derzeit nicht für SharePoint Online verwendet. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Wird von Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund verwendet. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Das Attribut wird nur einmal zwischen Azure AD und Exchange Online synchronisiert, nachdem Exchange Online zur Autoritätsquelle für dieses Attribut wurde, und spätere Änderungen am Attribut aus der lokalen Umgebung können nicht synchronisiert werden. Weitere Informationen finden Sie in [diesem KB-Artikel](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange).|
| title |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |Mechanische Eigenschaft. Land/Region des Benutzers
. Wird für die Lizenzzuweisung verwendet. |
| userPrincipalName |X | | |Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Teams und Skype for Business Online
| Attributname | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definiert, ob ein Konto aktiviert ist. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Wird von Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund verwendet. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Das Attribut wird nur einmal zwischen Azure AD und Exchange Online synchronisiert, nachdem Exchange Online zur Autoritätsquelle für dieses Attribut wurde, und spätere Änderungen am Attribut aus der lokalen Umgebung können nicht synchronisiert werden. Weitere Informationen finden Sie in [diesem KB-Artikel](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange).|
| title |X |X | | |
| usageLocation |X | | |Mechanische Eigenschaft. Land/Region des Benutzers. Wird für die Lizenzzuweisung verwendet. |
| userPrincipalName |X | | |Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attributname | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definiert, ob ein Konto aktiviert ist. |
| cn |X | |X |Allgemeiner Name oder Alias. Meistens das Präfix des Werts [mail]. |
| displayName |X |X |X |Eine Zeichenfolge, die den häufig als Anzeigename (Vorname Nachname) angezeigten Namen darstellt. |
| mail |X |X |X |Vollständige E-Mail-Adresse |
| member | | |X | |
| objectSID |X | |X |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| proxyAddresses |X |X |X |Mechanische Eigenschaft. Von Azure AD verwendet. Enthält alle sekundären E-Mail-Adressen für den Benutzer. |
| pwdLastSet |X | | |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. |
| sourceAnchor |X |X |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| usageLocation |X | | |Mechanische Eigenschaft. Land/Region des Benutzers. Wird für die Lizenzzuweisung verwendet. |
| userPrincipalName |X | | |Dieser Benutzerprinzipalname ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |

## <a name="intune"></a>Intune
| Attributname | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definiert, ob ein Konto aktiviert ist. |
| c |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | |X |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Wird von Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund verwendet. |
| sourceAnchor |X |X |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| usageLocation |X | | |Mechanische Eigenschaft. Land/Region des Benutzers. Wird für die Lizenzzuweisung verwendet. |
| userPrincipalName |X | | |Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attributname | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definiert, ob ein Konto aktiviert ist. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| objectSID |X | |X |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Wird von Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund verwendet. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |X | | |Mechanische Eigenschaft. Land/Region des Benutzers. Wird für die Lizenzzuweisung verwendet. |
| userPrincipalName |X | | |Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |

## <a name="3rd-party-applications"></a>Drittanbieteranwendungen
Diese Gruppe umfasst einen Satz von Attributen. Es handelt sich um die Attribute, die für eine generische Workload oder Anwendung mindestens benötigt werden. Dieser Attributsatz kann für eine oben nicht aufgeführte Workload oder für eine Nicht-Microsoft-App verwendet werden. Der Satz wird explizit für Folgendes verwendet:

* Yammer (nur „Benutzer“ wird genutzt)
* [Organisationsübergreifende Hybrid-B2B-Zusammenarbeit über Ressourcen wie SharePoint (siehe SharePoint Business to Business Collaboration: Extranet for Partners with Office 365 (SharePoint Business-to-Business-Zusammenarbeit: Extranet für Partner mit Office 365))](/sharepoint/create-b2b-extranet)

Diese Gruppe umfasst mehrere Attribute, die verwendet werden können, wenn das Azure AD-Verzeichnis nicht zur Unterstützung von Microsoft 365, Dynamics oder Intune verwendet wird. Er umfasst eine kleine Gruppe von Kernattributen. Beachten Sie, dass für das einmalige Anmelden oder die Bereitstellung für einige Drittanbieteranwendungen zusätzlich zu den hier beschriebenen Attributen das Konfigurieren der Synchronisierung von Attributen erforderlich ist. Die Anwendungsanforderungen werden im [Tutorial zur Saas-Anwendung](../saas-apps/tutorial-list.md) für jede Anwendung beschrieben.

| Attributname | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definiert, ob ein Konto aktiviert ist. |
| cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| member | | |X | |
| objectSID |X | | |Mechanische Eigenschaft. AD-Benutzer-ID, die verwendet wird, um die Synchronisierung zwischen Azure AD und AD aufrechtzuerhalten. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Mechanische Eigenschaft. Wird verwendet, um zu erfahren, wann bereits ausgestellte Token für ungültig erklärt werden sollen. Wird von Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund verwendet. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanische Eigenschaft. Unveränderlicher Bezeichner, mit dem die Beziehung zwischen AD DS und Azure AD aufrechterhalten wird. |
| usageLocation |X | | |Mechanische Eigenschaft. Land/Region des Benutzers. Wird für die Lizenzzuweisung verwendet. |
| userPrincipalName |X | | |Dieser Benutzerprinzipalname (UPN) ist die Anmelde-ID für den Benutzer. Meistens identisch mit dem Wert [mail]. |

## <a name="windows-10"></a>Windows 10
Bei unter Windows 10 per Domänenbeitritt in die Domäne eingebundenen Computern (Geräten) werden einige Attribute mit Azure AD synchronisiert. Weitere Informationen zu den Szenarien finden Sie unter [Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen](../devices/hybrid-azuread-join-plan.md). Diese Attribute werden immer synchronisiert, und Windows 10 wird nicht als App angezeigt, für die Sie die Auswahl aufheben können. Ein unter Windows 10 in die Domäne eingebundener Computer wird dadurch identifiziert, dass das Attribut „userCertificate“ aufgefüllt ist.

| Attributname | Sicherungsmedium | Comment |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hardcodierter Wert für in die Domäne eingebundene Computer |
| displayName |X | |
| ms-DS-CreatorSID |X |Auch als „registeredOwnerReference“ bezeichnet. |
| objectGUID |X |Auch als „deviceID“ bezeichnet. |
| objectSID |X |Auch als „onPremisesSecurityIdentifier“ bezeichnet. |
| operatingSystem |X |Auch als „deviceOSType“ bezeichnet. |
| operatingSystemVersion |X |Auch als „deviceOSVersion“ bezeichnet. |
| userCertificate |X | |

Diese Attribute für **Benutzer** ergänzen die anderen Apps, die Sie ausgewählt haben.  

| Attributname | Benutzer | Comment |
| --- |:---:| --- |
| domainFQDN |X |Auch als „dnsDomainName“ bezeichnet. Beispiel: contoso.com. |
| domainNetBios |X |Auch als „netBiosName“ bezeichnet. Beispiel: CONTOSO |
| msDS-KeyCredentialLink |X |Nach der Registrierung des Benutzers in Windows Hello für Business | 

## <a name="exchange-hybrid-writeback"></a>Exchange-Hybridrückschreiben
Diese Attribute werden vom Azure AD in das lokale Active Directory zurückgeschrieben, wenn Sie **Exchange-Hybrid** aktivieren. Abhängig von Ihrer Exchange-Version werden möglicherweise weniger Attribute synchronisiert.

| Attributname (lokales AD) | Attributname (Connect-Benutzeroberfläche) | Benutzer | Kontakt | Group | Comment |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Abgeleitet von cloudAnchor in Azure AD. Dies ist ein neues Attribut in Exchange 2016 und Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Onlinearchiv: Ermöglicht Kunden, E-Mail-Nachrichten zu archivieren. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtern: Schreibt lokale Filterung, Onlinesicherung und blockierte Absenderdaten von Clients zurück. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtern: Schreibt lokale Filterung, Onlinesicherung und blockierte Absenderdaten von Clients zurück. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtern: Schreibt lokale Filterung, Onlinesicherung und blockierte Absenderdaten von Clients zurück. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Aktivieren von Unified Messaging (UM) – Onlinevoicemail: Wird von Microsoft Lync Server-Integration verwendet, um Lync Server lokal zu melden, dass der Benutzer Voicemail in Onlinediensten verwendet. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |Beweissicherungsverfahren: Ermöglicht Clouddiensten, zu bestimmen, welche Benutzer einem Beweissicherungsverfahren unterliegen. |
| proxyAddresses| proxyAddresses |X |X |X |Nur die x500-Adresse von Exchange Online wird eingefügt. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Ermöglicht einem Exchange Online-Postfach, dass Benutzern mit lokalem Exchange-Postfach SendOnBehalfTo-Rechte erteilt werden. Erfordert Azure AD Connect Build 1.1.552.0 oder höher |

## <a name="exchange-mail-public-folder"></a>Öffentlicher Exchange-E-Mail-Ordner
Diese Attribute werden vom lokalen Active Directory mit Azure AD synchronisiert, wenn Sie den **öffentlichen Exchange-E-Mail-Ordner** aktivieren.

| Attributname | PublicFolder | Comment |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Geräterückschreiben
Geräteobjekte werden in Active Directory erstellt. Bei diesen Objekten kann es sich um mit Azure AD verknüpfte Geräte oder um in eine Domäne eingebundene Windows 10-Computer handeln.

| Attributname | Sicherungsmedium | Comment |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Nur mit Windows Server 2016-AD-Schema |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Notizen
* Bei Verwendung einer alternativen ID wird das lokale Attribut userPrincipalName mit dem Azure AD-Attribut onPremisesUserPrincipalName synchronisiert. Das alternative ID-Attribut, z.B. „mail“, wird mit dem Azure AD-Attribut userPrincipalName synchronisiert.
* In den obigen Listen gilt der Objekttyp **Benutzer** auch für den Objekttyp **iNetOrgPerson**.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md) .

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).