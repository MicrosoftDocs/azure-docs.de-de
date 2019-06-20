---
title: Zweistufige Überprüfung für Azure MFA und AD FS – Azure Active Directory
description: Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS beschrieben.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58cfac56af9074e065431f6adbd44496adc7c7a6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414506"
---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Erste Schritte mit Azure Multi-Factor Authentication und Active Directory-Verbunddiensten

<center>

![Erste Schritte mit Azure MFA und AD FS](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Wenn Ihre Organisation über einen Verbund Ihres lokalen Active Directory mit Azure Active Directory über AD FS verfügt, gibt es zwei Optionen für die Verwendung von Multi-Factor Authentication.

* Sichern von Cloud-Ressourcen mit Azure Multi-Factor Authentication oder Active Directory-Verbunddiensten
* Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server

In der folgenden Tabelle sind die Überprüfungsverfahren beim Sichern von Ressourcen mit Azure Multi-Factor Authentication und AD FS zusammengefasst.

| Überprüfungsvorgang bei browserbasierten Apps | Überprüfungsvorgang bei Nicht-Browser-Apps |
|:--- |:--- |
| Sichern von Azure AD-Ressourcen mit Azure Multi-Factor Authentication |<li>Der erste Überprüfungsschritt erfolgt lokal über AD FS.</li> <li>Der zweite Schritt ist eine telefonbasierte Methode mithilfe der Cloudauthentifizierung.</li> |
| Sichern von Azure AD-Ressourcen mit Active Directory-Verbunddiensten |<li>Der erste Überprüfungsschritt erfolgt lokal über AD FS.</li><li>Der zweite Schritt wird lokal unter Berücksichtigung des Anspruchs ausgeführt.</li> |

Sicherheitshinweise zu App-Kennwörtern für Verbundbenutzer:

* App-Kennwörter werden über die Cloudauthentifizierung überprüft, daher werden Verbunde umgangen. Der Verbund wird nur beim Einrichten eines App-Kennworts aktiv verwendet.
* Lokale Einstellungen für die Clientzugriffssteuerung werden von App-Kennwörtern nicht berücksichtigt.
* Wenn Sie App-Kennwörter verwenden, geht die Möglichkeit zum lokalen Protokollieren der Authentifizierung verloren.
* Das Deaktivieren oder Löschen von Konten kann für die Verzeichnissynchronisierung bis zu drei Stunden dauern, sodass das Deaktivieren bzw. Löschen von App-Kennwörtern in der Cloudidentität verzögert wird.

Informationen zum Einrichten von Azure Multi-Factor Authentication oder des Azure Multi-Factor Authentication-Servers mit AD FS finden Sie in den folgenden Artikeln:

* [Sichern von Cloudressourcen mit Azure Multi-Factor Authentication und AD FS](howto-mfa-adfs.md)
* [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md)
* [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0](howto-mfaserver-adfs-2.md)
