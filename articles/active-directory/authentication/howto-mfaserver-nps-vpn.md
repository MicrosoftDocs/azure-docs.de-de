---
title: Azure MFA-Server und Drittanbieter-VPNs – Azure Active Directory
description: Hier finden Sie Schritt-für-Schritt-Konfigurationsanleitungen für die Integration von Azure MFA-Server in Cisco, Citrix und Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fe09199cb50d2a3796c3b638dca1a723016dc4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742016"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Erweiterte Szenarien mit Azure MFA-Server und VPN-Lösungen von Drittanbietern

Azure Multi-Factor Authentication-Server (Azure MFA-Server) kann verwendet werden, um eine nahtlose Verbindung mit verschiedenen VPN-Lösungen von Drittanbietern herzustellen. In diesem Artikel geht es um die Cisco&reg; ASA-VPN-Appliance, die Citrix NetScaler SSL-VPN-Appliance und die Juniper Networks Secure Access/Pulse Secure Connect Secure SSL-VPN-Appliance. Wir haben Konfigurationsanleitungen für diese drei häufig verwendeten virtuellen Geräte erstellt. Azure MFA-Server kann auch in die meisten andere Systeme integriert werden, die RADIUS, LDAP, IIS oder anspruchsbasierte Authentifizierung bei AD FS verwenden. Weitere Details finden Sie unter [Konfigurationen von Azure MFA-Server](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die für die Anmeldung der Benutzer mehrstufige Authentifizierung (MFA) anfordern möchten, sollten cloudbasierte Multi-Factor Authentication von Azure AD verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Wenn Sie die cloudbasierte mehrstufige Authentifizierung verwenden, lesen Sie unter [Integration Ihrer VPN-Infrastruktur in Azure MFA](howto-mfa-nps-extension-vpn.md) nach.
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldeinformationen zur Aktivierung generieren.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA-VPN-Appliance und Azure MFA-Server
Azure MFA-Server kann in Ihre Cisco&reg; ASA-VPN-Appliance integriert werden, um bei Cisco AnyConnect&reg;-VPN-Anmeldungen und beim Portalzugriff für zusätzliche Sicherheit zu sorgen.  Sie können das LDAP- oder das RADIUS-Protokoll verwenden.  Wählen Sie eine der folgenden Optionen, um die ausführlichen Schritt-für-Schritt-Konfigurationsanleitungen herunterzuladen.

| Konfigurationshandbuch | BESCHREIBUNG |
| --- | --- |
| [Cisco ASA mit Anyconnect VPN und Azure MFA-Konfiguration für LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integration Ihrer Cisco ASA-VPN-Appliance in Azure MFA per LDAP |
| [Cisco ASA mit Anyconnect-VPN und Azure MFA-Konfiguration für RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integration Ihrer Cisco ASA-VPN-Appliance in Azure MFA per RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL-VPN und Azure MFA-Server
Azure MFA-Server kann in Ihre Citrix NetScaler SSL-VPN-Appliance integriert werden, um bei Citrix NetScaler SSL-VPN-Anmeldungen und beim Portalzugriff für zusätzliche Sicherheit zu sorgen.  Sie können das LDAP- oder das RADIUS-Protokoll verwenden.  Wählen Sie eine der folgenden Optionen, um die ausführlichen Schritt-für-Schritt-Konfigurationsanleitungen herunterzuladen.

| Konfigurationshandbuch | BESCHREIBUNG |
| --- | --- |
| [Citrix NetScaler SSL-VPN und Azure MFA-Konfiguration für LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integration Ihres Citrix NetScaler SSL-VPN in Azure MFA-Appliance per LDAP |
| [Citrix NetScaler SSL-VPN und Azure MFA-Konfiguration für RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integration Ihrer Citrix NetScaler SSL-VPN-Appliance in Azure MFA per RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL-VPN-Appliance und Azure MFA-Server
Azure MFA-Server kann in Ihre Juniper/Pulse Secure SSL-VPN-Appliance integriert werden, um bei Juniper/Pulse Secure SSL-VPN-Anmeldungen und beim Portalzugriff für zusätzliche Sicherheit zu sorgen.  Sie können das LDAP- oder das RADIUS-Protokoll verwenden.  Wählen Sie eine der folgenden Optionen, um die ausführlichen Schritt-für-Schritt-Konfigurationsanleitungen herunterzuladen.

| Konfigurationshandbuch | BESCHREIBUNG |
| --- | --- |
| [Juniper/Pulse Secure SSL-VPN und Azure MFA-Konfiguration für LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integration Ihres Juniper/Pulse Secure SSL-VPN in Azure MFA-Appliance per LDAP |
| [Juniper/Pulse Secure SSL-VPN und Azure MFA-Konfiguration für RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integration Ihrer Juniper/Pulse Secure SSL-VPN-Appliance in Azure MFA per RADIUS |

## <a name="next-steps"></a>Nächste Schritte

- [Verbessern der vorhandenen Authentifizierungsinfrastruktur mit der NPS-Erweiterung für Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurieren von Azure Multi-Factor Authentication-Einstellungen](howto-mfa-mfasettings.md)
