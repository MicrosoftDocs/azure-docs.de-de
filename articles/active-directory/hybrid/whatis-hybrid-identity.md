---
title: Was bedeutet Hybrididentität in Azure Active Directory?
description: Bei der Hybrididentität gibt es eine gemeinsame Benutzeridentität für die lokale und die cloudbasierte Authentifizierung und Autorisierung.
keywords: Einführung in Azure AD Connect, Übersicht über Azure AD Connect, was ist Azure AD Connect, Active Directory installieren
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c43238d44b2309d105ef14e696a5a16848d0b58
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896828"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Was bedeutet Hybrididentität in Azure Active Directory?

Heutzutage werden Unternehmen und Konzerne immer mehr zu einer Mischung aus lokalen Anwendungen und Cloudanwendungen.  Benutzer benötigen lokal und in der Cloud Zugriff auf diese Anwendungen. Wenn Benutzer sowohl lokal als auch in der Cloud verwaltet werden, ergeben sich herausfordernde Szenarien. 

Identitätslösungen von Microsoft umfassen lokale und cloudbasierte Funktionen.  Diese Lösungen schaffen eine gemeinsame Benutzeridentität für die Authentifizierung und Autorisierung bei allen Ressourcen, unabhängig vom Standort. Wir bezeichnen dies als **Hybrididentität**.

Durch die Hybrididentität in Azure AD und die Hybrididentitätsverwaltung sind diese Szenarien möglich.

Um Hybrididentität in Azure AD zu erreichen, kann je nach Szenario eine von drei Authentifizierungsmethoden verwendet werden.   Die drei Methoden sind: 

- **[Kennworthashsynchronisierung (Password hash synchronization, PHS)](whatis-phs.md)**  
- **[Passthrough-Authentifizierung (PTA)](how-to-connect-pta.md)**  
- **[Verbund (AD FS)](whatis-fed.md)** 

Diese Authentifizierungsmethoden bieten auch [SSO](how-to-connect-sso.md)-Funktionen.  Einmaliges Anmelden meldet Benutzer automatisch bei ihren Unternehmensgeräten an, die mit dem Unternehmensnetzwerk verbunden sind.

Weitere Informationen finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Gängige Szenarien und Empfehlungen 

Im Folgenden sind einige verbreitete Hybrididentitäts- und Zugriffsverwaltungsszenarien mit Empfehlungen zur Auswahl der jeweils geeigneten Hybrididentitätsoptionen aufgeführt. 

|Ziel|PHS und SSO<sup>1</sup>| PTA und SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Automatisches Synchronisieren neuer Benutzer-, Kontakt- und Gruppenkonten, die in meiner lokalen Active Directory-Instanz erstellt werden, mit der Cloud|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| ![Empfohlen](./media/whatis-hybrid-identity/ic195031.png) |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 
|Einrichten meines Mandanten für Office 365-Hybridszenarios|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| ![Empfohlen](./media/whatis-hybrid-identity/ic195031.png) |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 
|Ermöglichen der Anmeldung und des Zugriffs auf Clouddienste für meine Benutzer mit ihrem lokalen Kennwort|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| ![Empfohlen](./media/whatis-hybrid-identity/ic195031.png) |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementieren des einmaligen Anmeldens mit Anmeldeinformationen des Unternehmens|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| ![Empfohlen](./media/whatis-hybrid-identity/ic195031.png) |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)|  
|Sicherstellen, dass keine Kennworthashes in der Cloud gespeichert werden| |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 
|Aktivieren der cloudbasierten Lösungen für mehrstufige Authentifizierung|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)|![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 
|Aktivieren der lokalen Multi-Factor Authentication-Lösungen| | |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 
|Unterstützen der Smartcard-Authentifizierung für meine Benutzer<sup>4</sup>| | |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 
|Anzeigen von Benachrichtigungen zum Kennwortablauf im Office-Portal und auf dem Windows 10-Desktop| | |![Empfohlen](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Kennworthashsynchronisierung mit einmaligem Anmelden 
> 
> <sup>2</sup> Pass-Through-Authentifizierung und einmaliges Anmelden  
> 
> <sup>3</sup> Einmalige Verbundanmeldung mit AD FS  
>  
> <sup>4</sup> AD FS kann in Ihre Unternehmens-PKI integriert werden, damit die Anmeldung mithilfe von Zertifikaten möglich ist. Bei diesen Zertifikaten kann es sich um Softzertifikate handeln, die über vertrauenswürdige Bereitstellungskanäle bereitgestellt werden, etwa MDM-, GPO- oder Smartcard-Zertifikate (einschließlich PIV/CAC-Karten) oder Hello for Business (cert-trust). Weitere Informationen zur Unterstützung der Smartcard-Authentifizierung finden Sie in [diesem Blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Lizenzanforderungen für die Verwendung von Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Nächste Schritte 

- [Worum handelt es sich bei Azure AD Connect und Connect Health?](whatis-azure-ad-connect.md) 
- [Was ist Kennworthashsynchronisierung (PHS)?](whatis-phs.md) 
- [Worum handelt es sich bei der Passthrough-Authentifizierung (PTA)?](how-to-connect-pta.md) 
- [Was ist ein Verbund?](whatis-fed.md) 
- [Nahtlose einmalige Anmeldung mit Azure Active Directory](how-to-connect-sso.md) 

