---
title: Funktionsweise von SSO für lokale Ressourcen auf in Azure AD eingebundenen Geräten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure Active Directory eingebundene Hybridgeräte konfigurieren.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45941de6a90a5824ebc1e5d31b18b68f5fd9d493
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353192"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Funktionsweise von SSO für lokale Ressourcen auf in Azure AD eingebundenen Geräten

Es wird Sie wahrscheinlich nicht überraschen, dass für ein in Azure Active Directory (Azure AD) eingebundenes Gerät einmaliges Anmelden (Single Sign-On, SSO) für die Cloud-Apps Ihres Mandanten möglich ist. Falls Ihre Umgebung über eine lokale Active Directory-Instanz verfügt, können Sie SSO auf diese Geräte erweitern.

In diesem Artikel wird die entsprechende Vorgehensweise beschrieben.

## <a name="how-it-works"></a>So funktioniert's 

Da Sie sich nur einen Benutzernamen und das zugehörige Kennwort merken müssen, vereinfacht SSO den Zugriff auf Ihre Ressourcen und erhöht die Sicherheit Ihrer Umgebung. Mit einem in Azure AD eingebundenen Gerät verfügen Ihre Benutzer bereits über eine SSO-Umgebung für die Cloud-Apps in Ihrer Umgebung. Falls Ihre Umgebung über eine Azure AD-Instanz und eine lokale AD-Instanz verfügt, ist die Wahrscheinlichkeit hoch, dass Sie die SSO-Option auf Ihre lokalen Branchen-Apps, Dateifreigaben und Drucker erweitern möchten.  


In Azure AD eingebundene Geräte haben keine Informationen zu Ihrer lokalen AD-Umgebung, da sie nicht darin eingebunden sind. Sie können aber mit Azure AD Connect zusätzliche Informationen zu Ihrer lokalen AD-Umgebung auf diesen Geräten bereitstellen.
Eine Umgebung, die sowohl über eine Azure AD-Instanz als auch eine lokale AD-Instanz verfügt, wird auch als Hybridumgebung bezeichnet. Bei Verwendung einer Hybridumgebung haben Sie Azure AD Connect wahrscheinlich bereits bereitgestellt, um Ihre lokalen Identitätsinformationen mit der Cloud zu synchronisieren. Im Rahmen des Synchronisierungsprozesses synchronisiert Azure AD Connect die Informationen der lokalen Domäne mit Azure AD. Wenn sich ein Benutzer in einer Hybridumgebung an einem in Azure AD eingebundenen Gerät anmeldet, ist der Ablauf wie folgt:

1. Azure AD sendet den Namen der lokalen Domäne, deren Mitglied der Benutzer ist, zurück an das Gerät. 

2. Der Dienst für die lokale Sicherheitsautorität (Local Security Authority, LSA) ermöglicht die Kerberos-Authentifizierung auf dem Gerät.

Bei einem Zugriffsversuch auf eine Ressource in der lokalen Domäne des Benutzers wird für das Gerät Folgendes durchgeführt:

1. Die Domäneninformationen werden zum Ermitteln eines Domänencontrollers (DC) verwendet. 

2. Die Informationen der lokalen Domäne und die Benutzeranmeldeinformationen werden an den ermittelten DC gesendet, um den Benutzer zu authentifizieren.

3. Ein Kerberos [Ticket-Granting Ticket (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) wird empfangen, das zum Zugreifen auf in AD eingebundene Ressourcen verwendet wird.

Alle Apps, die für die **integrierte Windows-Authentifizierung** konfiguriert sind, erhalten SSO auf nahtlose Weise, wenn ein Benutzer darauf zugreift.  

Für Windows Hello for Business ist eine zusätzliche Konfiguration erforderlich, um lokales einmaliges Anmelden über ein in Azure AD eingebundenes Gerät zu ermöglichen. Weitere Informationen finden Sie unter [Configure Azure AD joined devices for On-premises Single-Sign On using Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base) (Konfigurieren von in Azure AD eingebundenen Geräten für lokales einmaliges Anmelden mit Windows Hello for Business). 

## <a name="what-you-get"></a>Ergebnis

Mit SSO haben Sie auf einem in Azure AD eingebundenen Gerät folgende Möglichkeiten: 

- Zugreifen auf einen UNC-Pfad auf einem AD-Mitgliedsserver

- Zugreifen auf einen AD-Mitgliedswebserver, der für integrierte Windows-Sicherheit konfiguriert ist 



Falls Sie Ihre lokale AD-Instanz über ein Windows-Gerät verwalten möchten, können Sie die [Remoteserver-Verwaltungstools für Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=45520) installieren.

Sie können Folgendes verwenden:

- Snap-In „Active Directory-Benutzer und -Computer“ (ADUC) zum Verwalten aller AD-Objekte. Allerdings müssen Sie die Domäne, mit der eine Verbindung hergestellt werden soll, manuell angeben.

- DHCP-Snap-In zum Verwalten eines in AD eingebundenen DHCP-Servers. Allerdings müssen Sie ggf. den DHCP-Servernamen oder die -Adresse angeben.

 
## <a name="what-you-should-know"></a>Wichtige Informationen

Unter Umständen müssen Sie Ihre [domänenbasierte Filterung](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) in Azure AD Connect anpassen, um sicherzustellen, dass die Daten zu den erforderlichen Domänen synchronisiert werden.

Apps und Ressourcen, die von der Active Directory-Computerauthentifizierung abhängig sind, funktionieren nicht, da in Azure AD eingebundene Geräte nicht über ein Computerobjekt in AD verfügen. 

Es ist nicht möglich, Dateien auf einem in Azure AD eingebundenen Gerät für andere Benutzer freizugeben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Worum handelt es sich bei der Geräteverwaltung in Azure Active Directory?](overview.md). 
