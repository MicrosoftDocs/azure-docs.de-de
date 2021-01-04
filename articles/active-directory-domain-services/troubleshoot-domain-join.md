---
title: Problembehandlung beim Einbinden einer Domäne mit Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie häufige Probleme beheben können, wenn Sie versuchen, eine VM in eine Domäne einzubinden oder eine Anwendung mit Azure Active Directory Domain Services zu verbinden, und keine Verbindung mit der verwalteten Domäne herstellen oder sich dort nicht authentifizieren können.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618602"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Problembehandlung beim Einbinden einer Domäne mit Azure Active Directory Domain Services

Wenn Sie versuchen, einen virtuellen Computer (VM) in eine verwaltete Azure Active Directory Domain Services-Domäne (Azure AD DS) einzubinden oder eine Anwendung mit dieser Domäne zu verbinden, wird eventuell eine Fehlermeldung angezeigt, dass dies nicht möglich ist. Informationen zur Behandlung von Problemen beim Einbinden in die Domäne finden Sie unter den folgenden Punkten:

* Wenn Sie keine Authentifizierungsaufforderung erhalten, kann die VM oder Anwendung keine Verbindung mit der von Azure AD DS verwalteten Domäne herstellen.
    * Beginnen Sie mit der Behandlung von [Konnektivitätsproblemen beim Einbinden in die Domäne](#connectivity-issues-for-domain-join).
* Wird während der Authentifizierung ein Fehler angezeigt, wurde die Verbindung mit der verwalteten Domäne erfolgreich hergestellt.
    * Beginnen Sie mit der Behandlung von [Problemen mit Anmeldeinformationen während des Einbindens in die Domäne](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Konnektivitätsprobleme beim Einbinden in die Domäne

Kann der virtuelle Computer die verwaltete Domäne nicht finden, liegt in der Regel ein Problem mit der Netzwerkverbindung oder -konfiguration vor. Überprüfen Sie die folgenden Schritte zur Problembehandlung, um das Problem zu finden und zu beheben:

1. Stellen Sie sicher, dass der virtuelle Computer mit demselben virtuellen Netzwerk (oder einem virtuellen Peernetzwerk) verbunden ist wie die verwaltete Domäne. Andernfalls kann die VM die Domäne nicht finden und eine Verbindung mit ihr herstellen, um sich darin einzubinden.
    * Wenn die VM nicht mit demselben virtuellen Netzwerk verbunden ist, vergewissern Sie sich, dass der Status für das Peering virtueller Netzwerke oder die VPN-Verbindung *Aktiv* oder *Verbunden* lautet, damit der Datenverkehr ordnungsgemäß weitergeleitet werden kann.
1. Pingen Sie die Domäne unter Angabe des Domänennamens der verwalteten Domäne, z. B. `ping aaddscontoso.com`.
    * Schlägt die Pingantwort fehl, pingen Sie die IP-Adressen für die Domäne, die auf der Übersichtsseite im Portal für Ihre verwaltete Domäne angezeigt werden, z. B. `ping 10.0.0.4`.
    * Wenn Sie die IP-Adresse erfolgreich pingen können, aber nicht die Domäne, ist der DNS möglicherweise falsch konfiguriert. Stellen Sie sicher, dass Sie die [DNS-Server der verwalteten Domäne für das virtuelle Netzwerk konfiguriert][configure-dns] haben.
1. Versuchen Sie, den DNS-Resolvercache auf dem virtuellen Computer zu leeren, z.B. `ipconfig /flushdns`.

### <a name="network-security-group-nsg-configuration"></a>Konfiguration der Netzwerksicherheitsgruppe (NSG)

Wenn Sie eine verwaltete Domäne erstellen, wird gleichzeitig eine Netzwerksicherheitsgruppe mit den entsprechenden Regeln für einen erfolgreichen Domänenvorgang erstellt. Wenn Sie zusätzliche Regeln für die Netzwerksicherheitsgruppe bearbeiten oder erstellen, blockieren Sie möglicherweise versehentlich Ports, die von Azure AD DS für die Bereitstellung von Verbindungs- und Authentifizierungsdiensten benötigt werden. Diese Netzwerksicherheitsgruppen-Regeln können Probleme verursachen – z.B., die Kennwortsynchronisierung wird nicht abgeschlossen, Benutzer können sich nicht anmelden, oder es gibt Probleme beim Einbinden in die Domäne.

Wenn weiterhin Verbindungsprobleme auftreten, überprüfen Sie die folgenden Schritte zur Problembehandlung:

1. Überprüfen Sie den Integritätsstatus Ihrer verwalteten Domäne im Azure-Portal. Wenn eine Warnung für *AADDS001* angezeigt wird, blockiert eine Netzwerksicherheitsgruppen-Regel den Zugriff.
1. Überprüfen Sie die [erforderlichen Ports und Netzwerksicherheitsgruppen-Regeln][network-ports]. Stellen Sie sicher, dass Netzwerksicherheitsgruppen-Regeln, die auf die VM oder das virtuelle Netzwerk angewendet werden, von dem aus Sie eine Verbindung herstellen, diese Netzwerkports nicht blockieren.
1. Nachdem Konfigurationsprobleme bei der Netzwerksicherheitsgruppe behoben wurden, wird die Warnung *AADDS001* auf der Seite „Integrität“ in ungefähr 2 Stunden nicht mehr angezeigt. Wenn die Netzwerkkonnektivität jetzt verfügbar ist, versuchen Sie, die VM erneut in die Domäne einzubinden.

## <a name="credentials-related-issues-during-domain-join"></a>Probleme mit Anmeldeinformationen während des Einbindens in die Domäne

Wenn ein Dialogfeld angezeigt wird, das Anmeldeinformationen zum Einbinden in die verwaltete Domäne anfordert, kann der virtuelle Computer eine Verbindung mit der Domäne über das virtuelle Azure-Netzwerk herstellen. Der Prozess des Einbindens in die Domäne schlägt beim Authentifizieren bei der Domäne oder der Autorisierung fehl, sodass dieser Prozess unter Verwendung der von Ihnen bereitgestellten Anmeldeinformationen nicht abgeschlossen werden kann.

Sehen Sie sich die folgenden Schritte zur Problembehandlung an, um Probleme im Zusammenhang mit Anmeldeinformationen zu beheben:

1. Geben Sie die Anmeldeinformationen im UPN-Format an, z. B. als `dee@contoso.onmicrosoft.com`. Stellen Sie sicher, dass dieser UPN in Azure AD ordnungsgemäß konfiguriert wird.
    * Der *SAMAccountName* für Ihr Konto wird möglicherweise automatisch generiert, wenn mehrere Benutzer in Ihrem Mandanten das gleiche UPN-Präfix verwenden oder wenn Ihr UPN-Präfix übermäßig lang ist. Das Format *SAMAccountName* für Ihr Konto ist möglicherweise anders als Sie erwarten bzw. unterscheidet sich von dem, was Sie in Ihrer lokalen Domäne verwenden.
1. Versuchen Sie, die Anmeldeinformationen für ein Benutzerkonto zu verwenden, das Teil der verwalteten Domäne ist, um virtuelle Computer in die verwaltete Domäne einzubinden.
1. Stellen Sie sicher, dass Sie die [Kennwortsynchronisierung aktiviert][enable-password-sync] und so lange gewartet haben, bis die erste Kennwortsynchronisierung abgeschlossen war.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie sich über die Active Directory-Prozesse im Rahmen des Vorgangs zum Einbinden in eine Domäne genauer informieren möchten, lesen Sie [Probleme beim Einbinden und bei der Authentifizierung][join-authentication-issues].

Sollten beim Einbinden Ihres virtuellen Computers in die verwaltete Domäne weiterhin Probleme auftreten, [öffnen Sie ein Supportticket für Azure Active Directory][azure-ad-support], um Hilfe zu erhalten.

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
