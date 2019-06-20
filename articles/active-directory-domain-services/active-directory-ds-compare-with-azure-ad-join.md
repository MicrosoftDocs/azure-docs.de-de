---
title: Vergleich von Azure AD Join mit Azure Active Directory Domain Services | Microsoft-Dokumentation
description: Entscheidung zwischen Azure AD Join und Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: eaa8cb54a46b1ff3c2c0f7c40c824f6ddcca16b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234961"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Wählen zwischen Azure Active Directory Join und Azure Active Directory Domain Services
In diesem Artikel werden die Unterschiede zwischen Azure Active Directory Join (AD) und Azure AD Domain Services beschrieben, um Ihnen auf Basis Ihrer Anwendungsfälle eine Entscheidungshilfe zu bieten.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Bei Azure AD registrierte und in Azure AD eingebundene Geräte
Azure AD ermöglicht Ihnen das Verwalten der Identität von Geräten, die von Ihrer Organisation verwendet werden, und das Steuern des Zugriffs auf Unternehmensressourcen über diese Geräte. Benutzer können ihr persönliches Gerät bei Azure AD registrieren (Bring Your Own Device), sodass das Gerät eine Identität erhält. Azure AD kann das Gerät anschließend authentifizieren, wenn ein Benutzer sich bei Azure AD anmeldet und das Gerät für den Zugriff auf gesicherte Ressourcen verwendet. Darüber hinaus können Sie das Gerät mithilfe von Software zur mobilen Geräteverwaltung (Mobile Device Management, MDM) wie z.B. Microsoft Intune verwalten. Dieses Feature ermöglicht Ihnen, den Zugriff auf vertrauliche Ressourcen von verwalteten und richtlinienkompatiblen Geräten einzuschränken.

Sie können auch im Besitz der Organisation befindliche Geräte in Azure AD einbinden. Dieser Mechanismus bietet die gleichen Vorteile wie die Registrierung eines persönlichen Geräts bei Azure AD. Darüber hinaus können sich Benutzer mit Ihren Unternehmensanmeldeinformationen bei dem Gerät anmelden. In Azure AD eingebundene Geräte bieten Ihnen folgende Vorteile:
* Einmaliges Anmelden (Single Sign-on, SSO) bei Anwendungen, die durch Azure AD gesichert sind
* Mit der Unternehmensrichtlinie kompatibles Roaming von Benutzereinstellungen auf allen Geräten.
* Zugriff auf den Microsoft Store für Unternehmen mit Ihren Unternehmensanmeldeinformationen.
* Windows Hello for Business
* Eingeschränkter Zugriff auf Apps und Ressourcen über Geräte, die mit der Unternehmensrichtlinien kompatibel sind.

| **Gerätetyp** | **Geräteplattformen** | **Mechanismus** |
|:---| --- | --- |
| Persönliche Geräte | Windows 10, iOS, Android, Mac OS | Bei Azure AD registriert |
| Im Besitz der Organisation befindliches Gerät, das nicht in das lokale AD eingebunden ist | Windows 10 | In Azure AD eingebunden |
| Im Besitz der Organisation befindliches Gerät, das in ein lokales AD eingebunden ist | Windows 10 | Hybrid in Azure AD eingebunden |

Auf einem in Azure AD eingebundenen oder registrierten Gerät wird die Benutzerauthentifizierung mit modernen Protokollen auf OAuth-/OpenID Connect-Basis durchgeführt. Diese Protokolle sind für den Einsatz im Internet konzipiert und eignen sich hervorragend für mobile Szenarien, in denen Benutzer von überall auf Unternehmensressourcen zugreifen.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Beitreten zu einer verwalteten Azure AD Domain Services-Domäne
Azure AD Domain Services stellen eine verwaltete AD-Domäne in einem virtuellen Azure-Netzwerk bereit. Sie können dieser verwalteten Domäne mit herkömmlichen Domänenbeitrittsmechanismen Computer hinzufügen. Windows-Client- (Windows 7, Windows 10) und Windows-Servercomputer können der verwalteten Domäne beitreten. Darüber hinaus können Sie auch Linux- und Mac OS-Computer der verwalteten Domäne hinzufügen. Wenn Sie einen Computer einer AD-Domäne hinzufügen, können Benutzer sich mit ihren Unternehmensanmeldeinformationen bei dem Computer anmelden. Diese Computer können mit Gruppenrichtlinien verwaltet werden – so wird die Kompatibilität mit den Sicherheitsrichtlinien Ihrer Organisation erzwungen.

Bei einem in eine Domäne eingebundenen Computer erfolgt die Benutzerauthentifizierung mit NTLM oder Kerberos-Authentifizierungsprotokollen. Der in die Domäne eingebundene Computer benötigt eine Sichtverbindung mit den Domänencontrollern der verwalteten Domäne, damit die Benutzerauthentifizierung funktioniert. Aus diesem Grund muss sich der in die Domäne eingebundene Computer im selben virtuellen Netzwerk wie die verwaltete Domäne befinden. Alternativ muss eine Verbindung des in die Domäne eingebundenen Computers mit der verwalteten Domäne über ein virtuelles Netzwerk mit Peering oder eine Site-to-Site-VPN-/ExpressRoute-Verbindung hergestellt werden. Daher ist dieser Mechanismus nicht ideal für Geräte, die mobil sind oder von außerhalb des Unternehmensnetzwerks Verbindungen mit Ressourcen herstellen.

> [!NOTE]
> Technisch gesehen lässt sich eine lokale Clientarbeitsstation über eine Site-to-Site-VPN- oder ExpressRoute-Verbindung in die verwaltete Domäne einbinden. Endbenutzergeräte sollten Sie jedoch entweder bei Azure AD registrieren (persönliche Geräte) oder in Azure AD einbinden (unternehmenseigene Geräte). Dieser Mechanismus ist besser im Internet einsetzbar und ermöglicht Endbenutzern, von überall aus zu arbeiten. Azure AD Domain Services eignen sich hervorragend für in Ihren virtuellen Azure-Netzwerken bereitgestellte Windows- oder Linux-Server-VMs, auf denen Ihre Anwendungen bereitgestellt werden.


## <a name="summary---key-differences"></a>Zusammenfassung – Hauptunterschiede
| **Aspekt** | **Azure AD Join** | **Azure AD Domain Services** |
|:---| --- | --- |
| Gerät gesteuert von | Azure AD | Verwaltete Azure AD Domain Services-Domäne |
| Darstellung im Verzeichnis | Geräteobjekte im Azure AD-Verzeichnis. | Computerobjekte in der verwalteten AAD-DS-Domäne. |
| Authentication | Auf OAuth/OpenID Connect basierende Protokolle | Kerberos, NTLM-Protokolle |
| Verwaltung | Software zur mobilen Geräteverwaltung (Mobile Device Management, MDM) wie z.B. Intune | Gruppenrichtlinie |
| Netzwerk | Im Internet einsetzbar | Setzt voraus, dass die Computer sich im selben virtuellen Netzwerk befinden wie die verwaltete Domäne.|
| Ideal für... | Mobile oder Desktop-Endbenutzergeräte | In Azure bereitgestellte Server-VMs |


## <a name="next-steps"></a>Nächste Schritte
### <a name="learn-more-about-azure-ad-domain-services"></a>Weitere Informationen zu Azure AD Domain Services
* [Azure AD Domain Services](overview.md)
* [Funktionen](active-directory-ds-features.md)
* [Bereitstellungsszenarien](scenarios.md)
* [So entscheiden Sie, ob Azure AD Domain Services für Ihren Anwendungsfall geeignet ist](comparison.md)
* [Synchronisierung in einer durch Azure AD Domain Services verwalteten Domäne](synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Weitere Informationen zu Azure AD Join
* [Einführung in die Geräteverwaltung in Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Erste Schritte mit Azure AD Domain Services
* [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals (Vorschauversion)](create-instance.md)
