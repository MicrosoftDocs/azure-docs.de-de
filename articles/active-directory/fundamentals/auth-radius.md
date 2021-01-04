---
title: RADIUS-Authentifizierung mit Azure Active Directory
description: Architekturleitfaden zum Erzielen der RADIUS-Authentifizierung mit Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168626"
---
# <a name="radius-authentication-with-azure-active-directory"></a>RADIUS-Authentifizierung mit Azure Active Directory

RADIUS (Remote Authentication Dial-In User Service) ist ein Netzwerkprotokoll zum Schützen eines Netzwerks, indem die zentrale Authentifizierung und Autorisierung von Einwahlbenutzern ermöglicht wird. Für viele Anwendungen wird zum Authentifizieren von Benutzern weiterhin das RADIUS-Protokoll genutzt.

Microsoft Windows Server verfügt über die Rolle „Netzwerkrichtlinienserver“ (Network Policy Server, NPS). Ein Server dieser Art kann als RADIUS-Server fungieren und die RADIUS-Authentifizierung unterstützen.

Azure Active Directory (Azure AD) ermöglicht die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) mit RADIUS-basierten Systemen. Wenn ein Kunde Azure AD Multi-Factor Authentication auf die oben erwähnten RADIUS-Workloads anwenden möchte, kann er die zugehörige NPS-Erweiterung auf seinem Windows-Netzwerkrichtlinienserver installieren. 

Der Windows-Netzwerkrichtlinienserver führt die Authentifizierung der Anmeldeinformationen eines Benutzers für Active Directory durch und sendet die Anforderung zur mehrstufigen Authentifizierung anschließend an Azure. Der Benutzer erhält auf seinem mobilen Authentifikator dann eine Aufforderung. Wenn der Vorgang erfolgreich ist, kann mit der Clientanwendung eine Verbindung mit dem Dienst hergestellt werden. 

## <a name="use-when"></a>Zu verwenden in folgenden Fällen: 

Sie müssen die mehrstufige Authentifizierung für folgende Anwendungen hinzufügen, z. B.:
* Virtuelles privates Netzwerk (VPN)
* WLAN-Zugriff
* Remotedesktopgateway (RDG)
* Virtual Desktop Infrastructure (VDI)
* Alle anderen Anwendungen, für die das RADIUS-Protokoll zum Authentifizieren von Benutzern im Dienst genutzt wird 

> [!NOTE]
> Anstatt RADIUS und die NPS-Erweiterung für Azure AD Multi-Factor Authentication zu nutzen, um Azure AD MFA auf VPN-Workloads anzuwenden, wird empfohlen, Ihre VPNs auf SAML zu aktualisieren und für ein VPN dann einen direkten Verbund mit Azure AD einzurichten. Auf diese Weise erzielen Sie für Ihr VPN den gesamten Umfang des Azure AD-Schutzes, z. B. bedingter Zugriff, mehrstufige Authentifizierung, Gerätekonformität und Identity Protection.

![Architekturdiagramm](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Komponenten des Systems 

* **Clientanwendung (VPN-Client)** : Sendet eine Authentifizierungsanforderung an den RADIUS-Client.

* **RADIUS-Client**: Konvertiert Anforderungen der Clientanwendung und sendet sie an den RADIUS-Server, auf dem die NPS-Erweiterung installiert ist.

* **RADIUS-Server**: Stellt eine Verbindung mit Active Directory her, um die primäre Authentifizierung für die RADIUS-Anforderung durchzuführen. Wenn der Vorgang erfolgreich ist, wird die Anforderung an die NPS-Erweiterung von Azure AD Multi-Factor Authentication übergeben.

* **NPS-Erweiterung**: Löst eine Anforderung von Azure AD Multi-Factor Authentication für eine sekundäre Authentifizierung aus. Wenn der Vorgang erfolgreich ist, wird die Authentifizierungsanforderung von der NPS-Erweiterung abgeschlossen, indem für den RADIUS-Server Sicherheitstoken bereitgestellt werden. Die Sicherheitstoken enthalten den Multi-Factor Authentication-Anspruch, der vom Sicherheitstokendienst von Azure ausgestellt wird.

* **Azure AD Multi-Factor Authentication:** Kommuniziert mit Azure AD, um die Benutzerdetails abzurufen, und führt eine sekundäre Authentifizierung mithilfe einer Verifizierungsmethode aus, die vom Benutzer konfiguriert wird.

## <a name="implement-radius-with-azure-ad"></a>Implementieren von RADIUS mit Azure AD 

* [Integrieren Ihrer vorhandenen NPS-Infrastruktur in Azure AD Multi-Factor Authentication](../authentication/howto-mfa-nps-extension.md) 

* [Erweiterte Konfigurationsoptionen für die NPS-Erweiterung für Azure AD Multi-Factor Authentication](../authentication/howto-mfa-nps-extension-advanced.md) 

* [Integration Ihrer VPN-Infrastruktur in Azure AD MFA mit der Netzwerkrichtlinienserver-Erweiterung für Azure](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

