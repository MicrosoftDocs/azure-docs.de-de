---
title: Sicherer Remote-VM-Zugriff in Azure AD Domain Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Remotezugriff auf VMs mit Netzwerkrichtlinienserver (Network Policy Server, NPS) und Azure AD Multi-Factor Authentication mit einer Remotedesktopdienste-Bereitstellung in einer verwalteten Azure Active Directory Domain Services-Domäne sichern.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618925"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Sichern des Remotezugriffs auf virtuelle Computer in Azure Active Directory Domain Services

Zum Sichern des Remotezugriffs auf virtuelle Computer (VMs), die in einer verwalteten Azure Active Directory Domain Services (Azure AD DS)-Domäne ausgeführt werden, können Sie Remotedesktopdienste (RDS) und Netzwerkrichtlinienserver (NPS) verwenden. Azure AD DS authentifiziert Benutzer, wenn diese Zugriff über die RDS-Umgebung anfordern. Zum Erhöhen der Sicherheit können Sie Azure AD Multi-Factor Authentication integrieren, um für Anmeldeereignisse eine zusätzliche Authentifizierungsaufforderung bereitzustellen. Azure AD Multi-Factor Authentication verwendet eine Erweiterung für NPS, um dieses Feature bereitzustellen.

> [!IMPORTANT]
> Für das sichere Herstellen einer Verbindung mit Ihren VMs in einer verwalteten Azure AD DS-Domäne wird die Verwendung von Azure Bastion empfohlen, einem vollständig plattformverwalteten, in Ihrem virtuellen Netzwerk bereitgestellten PaaS-Dienst. Ein Bastionhost bietet sichere und nahtlose Remote Desktop Protocol (RDP)-Konnektivität mit Ihren VMs direkt im Azure-Portal über SSL. Wenn Sie eine Verbindung über einen Bastionhost herstellen, benötigen Ihre VMs keine öffentliche IP-Adresse, und Sie müssen keine Netzwerksicherheitsgruppen verwenden, um den RDP-Zugriff über TCP-Port 3389 verfügbar zu machen.
>
> Es wird dringend empfohlen, dass Sie Azure Bastion in allen Regionen verwenden, in denen dies unterstützt wird. Führen Sie in Regionen ohne Azure Bastion-Verfügbarkeit die in diesem Artikel erläuterten Schritte aus, bis Azure Bastion verfügbar ist. Lassen Sie beim Zuweisen von öffentlichen IP-Adressen zu mit Azure AD DS verknüpften VMs Vorsicht walten, wo sämtlicher eingehender RDP-Datenverkehr zulässig ist.
>
> Weitere Informationen finden Sie unter [Was ist Azure Bastion?][bastion-overview].

In diesem Artikel wird veranschaulicht, wie Sie RDS in Azure AD DS konfigurieren und optional die NPS-Erweiterung für Azure AD Multi-Factor Authentication verwenden.

![Übersicht über Remotedesktopdienste (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie folgende Ressourcen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance], sofern erforderlich.
* Ein in Ihrem virtuellen Azure Active Directory Domain Services-Netzwerk erstelltes *Workloads*-Subnetz.
    * Bei Bedarf [konfigurieren Sie virtuelle Netzwerke für eine verwaltete Azure Active Directory Domain Services-Domäne][configure-azureadds-vnet].
* Ein Benutzerkonto, das Mitglied der *Administratorengruppe für Azure AD-Domänencontroller* (AAD-DC-Administratoren) in Ihrem Azure AD-Mandanten ist.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Bereitstellen und Konfigurieren der Remotedesktopumgebung

Erstellen Sie zuerst mindestens zwei Azure-VMs, auf denen Windows Server 2016 oder Windows Server 2019 ausgeführt wird. Um Redundanz und Hochverfügbarkeit Ihrer RD-Umgebung (Remotedesktop) sicherzustellen, können Sie später weitere Hosts hinzufügen und einen Lastenausgleich ausführen.

Eine empfohlene RDS-Bereitstellung enthält die folgenden zwei VMs:

* *RDGVM01*: Hier werden der RD-Verbindungsbrokerserver, der RD-Webzugriffsserver und der RD-Gatewayserver ausgeführt.
* *RDSHVM01*: Führt den RD-Sitzungshostserver aus.

Stellen Sie sicher, dass VMs in einem *Workloads*-Subnetz Ihres virtuellen Azure AD DS-Netzwerks bereitgestellt werden, und verknüpfen Sie die VMs mit der verwalteten Domäne. Weitere Informationen erhalten Sie in der Beschreibung zum [Erstellen und Einbinden einer Windows Server-VM in eine verwaltete Domäne][tutorial-create-join-vm].

Die Bereitstellung der RD-Umgebung umfasst eine Reihe von Schritten. Das vorhandene RD-Bereitstellungshandbuch kann ohne besondere Änderungen für eine verwaltete Domäne verwendet werden:

1. Melden Sie bei für die RD-Umgebung erstellten VMs mit einem Konto an, das zur Gruppe *Azure AD DC-Administratoren* gehört, z. B. mit *contosoadmin*.
1. Verwenden Sie zum Erstellen und Konfigurieren von RDS das vorhandene [Bereitstellungshandbuch für die Remotedesktopumgebung][deploy-remote-desktop]. Verteilen Sie die RD-Serverkomponenten entsprechend den jeweiligen Anforderungen auf die Azure-VMs.
    * Spezifisch für Azure AD DS: Wenn Sie die RD-Lizenzierung konfigurieren, legen Sie anstelle des im Bereitstellungshandbuch angegebenen Modus **Pro Benutzer** den Modus **Pro Gerät** fest.
1. Wenn Sie den Zugriff mithilfe eines Webbrowsers ermöglichen möchten, [richten Sie den Remotedesktop-Webclient für Ihre Benutzer ein][rd-web-client].

Wenn RD in der verwalteten Domäne bereitgestellt ist, können Sie den Dienst genau wie eine lokale AD DS-Domäne verwalten und nutzen.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>Bereitstellen und Konfigurieren von NPS und der Azure AD MFA NPS-Erweiterung

Wenn Sie die Sicherheit der Benutzeranmeldung erhöhen möchten, können Sie optional Azure AD Multi-Factor Authentication in die RD-Umgebung integrieren. Bei einer solchen Konfiguration erhalten Benutzer während der Anmeldung eine zusätzliche Aufforderung, Ihre Identität zu bestätigen.

Zum Bereitstellen dieser Funktionalität wird zusammen mit der NPS-Erweiterung für Azure AD Multi-Factor Authentication ein zusätzlicher Netzwerkrichtlinienserver (Network Policy Server, NPS) installiert. Diese Erweiterung wird für Anforderung und Rückgabe des Status der Eingabeaufforderungen der mehrstufigen Authentifizierung in Azure AD integriert.

Benutzer müssen [für die Verwendung von Azure AD Multi-Factor Authentication registriert sein][user-mfa-registration]. Dafür sind möglicherweise zusätzliche Azure AD-Lizenzen erforderlich.

Erstellen Sie zum Integrieren von Azure AD Multi-Factor Authentication in Ihre Azure AD DS-Remotedesktopumgebung einen NPS-Server, und installieren Sie die Erweiterung:

1. Erstellen Sie eine zusätzliche Windows Server 2016- oder 2019-VM, z. B. *NPSVM01*, die mit einem *Workloads*-Subnetz im virtuellen Azure AD DS-Netzwerk verbunden ist. Binden Sie die VM in die verwaltete Domäne ein.
1. Melden Sie sich bei der NPS-VM mit einem Konto an, das zur Gruppe der *Azure AD DC-Administratoren* gehört, z. B. *contosoadmin*.
1. Wählen Sie unter **Server-Manager** die Option **Rollen und Features hinzufügen** aus, und installieren Sie anschließend die Rolle *Netzwerkrichtlinien- und Zugriffsdienste*.
1. Lesen Sie den vorhandenen Artikel mit Anleitungen zum [Installieren und Konfigurieren der Azure AD MFA NPS-Erweiterung][nps-extension].

Wenn Sie den NPS-Server und die NPS-Erweiterung für Azure AD Multi-Factor Authentication installiert haben, arbeiten Sie den nächsten Abschnitt durch, um die Konfiguration für die Verwendung mit der RD-Umgebung vorzunehmen.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Integrieren von Remotedesktopgateway und Azure AD Multi-Factor Authentication

Eine Anleitung zum Integrieren der NPS-Erweiterung für Azure AD Multi-Factor Authentication finden Sie im vorhandenen Artikel zum [Integrieren Ihrer Remotedesktopgateway-Infrastruktur mithilfe der Netzwerkrichtlinienserver-Erweiterung (Network Policy Server, NPS) und Azure AD][azure-mfa-nps-integration].

Die folgenden zusätzlichen Konfigurationsoptionen sind für die Integration in eine verwaltete Domäne erforderlich:

1. [Registrieren Sie den NPS-Server nicht in Active Directory][register-nps-ad]. Bei diesem Schritt tritt in einer verwalteten Domäne ein Fehler auf.
1. Aktivieren Sie in [Schritt 4 zum Konfigurieren der Netzwerkrichtlinie][create-nps-policy] auch das Kontrollkästchen **Benutzerkonto-Einwähleigenschaften ignorieren**.
1. Wenn Sie Windows Server 2019 für den NPS-Server und die NPS-Erweiterung für Azure AD Multi-Factor Authentication verwenden, führen Sie den folgenden Befehl aus, um den sicheren Kanal zu aktualisieren, damit der NPS-Server ordnungsgemäß kommunizieren kann:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Benutzer werden nun bei der Anmeldung zur Eingabe eines zusätzlichen Authentifizierungsfaktors aufgefordert; hierbei kann es sich z. B. um eine Textnachricht oder eine Eingabeaufforderung in der Microsoft Authenticator-App handeln.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verbessern der Resilienz Ihrer Bereitstellung finden Sie unter [Remotedesktopdienste – Hochverfügbarkeit][rds-high-availability].

Weitere Informationen zum Sichern der Benutzeranmeldung finden Sie unter [Funktionsweise: Azure AD Multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability