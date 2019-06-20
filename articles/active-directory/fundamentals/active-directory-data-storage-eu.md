---
title: Identitätsdatenspeicher für europäische Kunden – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wo identitätsbezogene Daten für europäische Kunden durch Azure Active Directory gespeichert werden.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82a78953c4385f7688705d4ab3f697be9c3ddbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235161"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identitätsdatenspeicher für europäische Kunden in Azure Active Directory
Identitätsdaten werden von Azure AD an einem geografischen Standort basierend auf der Adresse gespeichert, die Ihre Organisation beim Abonnieren eines Microsoft-Onlinediensts wie Office 365 und Azure angibt. Informationen darüber, wo Ihre Identitätsdaten gespeichert werden, finden Sie im Microsoft Trust Center im Abschnitt [Wo wir Ihre Daten speichern](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located).

Azure AD speichert die meisten Identitätsdaten von Kunden, die eine Adresse in Europa angegeben haben, in europäischen Rechenzentren. Dieses Dokument enthält Informationen zu Daten, die von Azure AD-Diensten außerhalb von Europa gespeichert werden.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)
    
- Alle zweistufigen Authentifizierungen per Telefon oder SMS stammen aus Rechenzentren in den USA und werden auch von globalen Anbietern weitergeleitet.
- Von der Microsoft Authenticator-App gesendete Pushbenachrichtigungen stammen aus Rechenzentren in den USA. Darüber hinaus können auch geräteherstellerspezifische Dienste hinzukommen, die möglicherweise außerhalb Europas angeboten werden.
- OATH-Codes werden immer in den USA überprüft.

Weitere Informationen zu den Benutzerinformationen, die vom Azure MFA-Server (Multi-Factor Authentication Server) und von der cloudbasierten Azure MFA gesammelt werden, finden Sie unter [Azure Multi-Factor Authentication – Erfassen von Benutzerdaten](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C-Richtlinienkonfigurationsdaten und Schlüsselcontainer werden in Rechenzentren in den USA gespeichert. Diese enthalten keine persönlichen Benutzerdaten. Weitere Informationen zu Richtlinienkonfigurationen finden Sie im Artikel [Azure Active Directory B2C: Integrierte Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B speichert Einladungen mit Informationen zu Einlösungslinks und Umleitungs-URLs in Rechenzentren in den USA. Darüber hinaus werden auch die E-Mail-Adressen von Benutzern, die B2B-Einladungen abbestellen, in Rechenzentren in den USA gespeichert.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Für Azure AD DS werden die Benutzerdaten an dem Ort gespeichert, der vom Kunden für das virtuelle Azure-Netzwerk ausgewählt wurde. Wenn das Netzwerk also außerhalb von Europa liegt, werden die Daten außerhalb Europas repliziert und gespeichert.

## <a name="other-considerations"></a>Weitere Überlegungen

Dienste und Anwendungen, die in Azure AD integriert werden, haben Zugriff auf Identitätsdaten. Evaluieren Sie alle von Ihnen verwendeten Dienste und Anwendungen, um zu ermitteln, wie Identitätsdaten vom jeweiligen Dienst und von der jeweiligen Anwendung verarbeitet werden und ob die Datenspeicheranforderungen Ihres Unternehmens erfüllt werden.

Weitere Informationen zur Datenaufbewahrung finden Sie im Microsoft Trust Center im Abschnitt [Wo sich Ihre Daten befinden](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den oben beschriebenen Features und Funktionen finden Sie in diesen Artikeln:
- [Was ist Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Self-Service-Kennwortzurücksetzung in Azure AD für IT-Experten](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Was ist Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Was ist die Azure AD B2B-Zusammenarbeit?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
