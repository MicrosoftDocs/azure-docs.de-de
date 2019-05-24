---
title: B2B-Zusammenarbeit für Hybridorganisationen – Azure Active Directory | Microsoft-Dokumentation
description: Gewähren Sie Partnern mit Azure AD B2B-Zusammenarbeit Zugriff auf lokale und cloudbasierte Ressourcen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30e9c14863f67d28203eac916606e8786101e3b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65768284"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-Zusammenarbeit für Hybridorganisationen

Mit Azure Active Directory (Azure AD) B2B-Zusammenarbeit können Sie Ihren externen Partnern ganz einfach Zugriff auf Apps und Ressourcen in Ihrem Unternehmen gewähren. Dies gilt auch im Fall einer Hybridkonfiguration, in der Sie sowohl über lokale als auch cloudbasierte Ressourcen verfügen. Es spielt keine Rolle, ob Sie externe Partnerkonten gegenwärtig lokal in Ihrem lokalen Identitätssystem oder in der Cloud als Azure AD B2B-Benutzer verwalten. Sie können diesen Benutzern jetzt den Zugriff auf Ressourcen in beiden Umgebungen mit denselben Anmeldeinformationen ermöglichen.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Gewähren des Zugriffs auf lokale Apps für B2B-Benutzer in Azure AD

Wenn Ihr Unternehmen Funktionen der Azure AD B2B-Zusammenarbeit verwendet, um Gastbenutzer aus Partnerunternehmen zu Ihrem Azure AD-Verzeichnis einzuladen, können Sie für diese B2B-Benutzer jetzt Zugriff auf lokale Apps bereitstellen.

Apps, die die SAML-basierte Authentifizierung verwenden, können Sie über das Azure-Portal für B2B-Benutzer verfügbar machen, indem Sie den Azure AD-Anwendungsproxy für die Authentifizierung verwenden.

Für Apps, die die integrierte Windows-Authentifizierung (IWA) mit eingeschränkter Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) nutzen, können Sie ebenfalls den Azure AD-Anwendungsproxy für die Authentifizierung verwenden. Damit die Autorisierung funktioniert, ist jedoch ein Benutzerobjekt im lokalen Windows Server Active Directory-Verzeichnis erforderlich. Zum Erstellen von Benutzerobjekten, die Ihre B2B-Gastbenutzer darstellen, stehen zwei Methoden zur Auswahl.

- Sie können Microsoft Identity Manager (MIM) 2016 SP1 und den MIM-Verwaltungs-Agent für Microsoft Graph verwenden.
- Sie können ein PowerShell-Skript verwenden. (Für diese Lösung ist MIM nicht erforderlich.)

Ausführliche Informationen zum Implementieren dieser Lösungen finden Sie unter [Gewähren des Zugriffs auf lokale Anwendungen für B2B-Benutzer in Azure AD](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Gewähren des Zugriffs auf Cloudressourcen für lokal verwaltete Partnerkonten

Vor Azure AD haben Unternehmen mit lokalen Identitätssystemen Partnerkonten herkömmlicherweise in ihrem lokalen Verzeichnis verwaltet. In diesem Fall möchten Sie sicherstellen, dass Ihre Partner weiterhin Zugriff haben, wenn Sie Ihre Apps und andere Ressourcen zur Cloud migrieren. Im Idealfall sollen diese Benutzer mit denselben Anmeldeinformationen auf cloudbasierte und lokale Ressourcen zugreifen. 

Wir bieten jetzt Methoden, mit denen Sie diese lokalen Konten mithilfe von Azure AD Connect als „Gastbenutzer“ mit der Cloud synchronisieren können, sodass sich die Konten wie Azure AD B2B-Benutzer verhalten.

Zum Schutz Ihrer Unternehmensdaten können Sie den Zugriff auf bestimmte Ressourcen beschränken und Autorisierungsrichtlinien konfigurieren, die diese Gastbenutzer anders als Ihre Mitarbeiter behandeln.

Details zur Implementierung finden Sie unter [Gewähren des Zugriffs auf Cloudressourcen für lokal verwaltete Partnerkonten mit Azure AD B2B-Zusammenarbeit](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Nächste Schritte

- [Gewähren des Zugriffs auf lokale Anwendungen für B2B-Benutzer in Azure AD](hybrid-cloud-to-on-premises.md)
- [Gewähren des Zugriffs auf Cloudressourcen für lokal verwaltete Partnerkonten mit Azure AD B2B-Zusammenarbeit](hybrid-on-premises-to-cloud.md)


