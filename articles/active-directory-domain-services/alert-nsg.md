---
title: Problembehandlung von Warnungen für Netzwerksicherheitsgruppen in Azure AD DS | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme und Warnungen im Zusammenhang mit der Konfiguration von Netzwerksicherheitsgruppen für Azure Active Directory Domain Services behandeln.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d8f2e77b7225306844cec85363a2971eaac4eebd
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620255"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Bekannte Probleme: Netzwerkkonfigurationswarnungen in Azure Active Directory Domain Services

Damit Anwendungen und Dienste ordnungsgemäß mit einer von Azure Active Directory Domain Services (Azure AD DS) verwalteten Domäne kommunizieren können, müssen bestimmte Netzwerkports offen sein und den Datenverkehr zulassen. In Azure steuern Sie den Datenverkehrsfluss mithilfe von Netzwerksicherheitsgruppen. Für den Integritätsstatus einer verwalteten Azure AD DS-Domäne wird eine Warnung angezeigt, wenn die erforderlichen Netzwerksicherheitsgruppen-Regeln nicht vorhanden sind.

Dieser Artikel hilft Ihnen dabei, häufige Warnungen bei Konfigurationsproblemen mit Netzwerksicherheitsgruppen besser zu verstehen und zu beheben.

## <a name="alert-aadds104-network-error"></a>Warnung AADDS104: Netzwerkfehler

### <a name="alert-message"></a>Warnmeldung

*Microsoft kann nicht auf die Domänencontroller für diese verwaltete Domäne zugreifen. Dies kann geschehen, wenn eine für Ihr virtuelles Netzwerk konfigurierte Netzwerksicherheitsgruppe (NSG) den Zugriff auf ihre verwaltete Domäne blockiert. Eine weitere mögliche Ursache ist eine benutzerdefinierte Route, die eingehenden Datenverkehr aus dem Internet blockiert.*

Die häufigste Ursache von Netzwerkfehlern für Azure AD DS sind ungültige Netzwerksicherheitsgruppen-Regeln. Die Netzwerksicherheitsgruppe für das virtuelle Netzwerk muss den Zugriff auf bestimmte Ports und Protokolle zulassen. Wenn diese Ports blockiert sind, kann die Azure-Plattform die verwaltete Domäne weder überwachen noch aktualisieren. Auch die Synchronisierung zwischen dem Azure AD-Verzeichnis und Azure AD DS ist betroffen. Halten Sie diese Standardports unbedingt offen, um Dienstunterbrechungen zu vermeiden.

## <a name="default-security-rules"></a>Standardsicherheitsregeln

Auf die Netzwerksicherheitsgruppe für eine verwaltete Domäne werden die folgenden standardmäßigen Sicherheitsregeln für den eingehenden und ausgehenden Datenverkehr angewendet. Diese Regeln sorgen für die Sicherheit von Azure AD DS und ermöglichen der Azure-Plattform, die verwaltete Domäne zu überwachen, zu verwalten und zu aktualisieren.

### <a name="inbound-security-rules"></a>Eingangssicherheitsregeln

| Priority | Name | Port | Protocol | `Source` | Destination | Aktion |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Any | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 65000    | AllVnetInBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Any | Allow |
| 65500    | DenyAllInBound | Any | Any | Any | Any | Verweigern |

> [!NOTE]
> Sie können auch eine zusätzliche Regel einrichten, die eingehenden Datenverkehr zulässt, wenn Sie [Secure LDAP (LDAPS) konfigurieren][configure-ldaps]. Diese zusätzliche Regel ist für die korrekte LDAPS-Kommunikation erforderlich.

### <a name="outbound-security-rules"></a>Ausgangssicherheitsregeln

| Priority | Name | Port | Protocol | `Source` | Destination | Aktion |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Any | Any |  Any | Internet | Allow |
| 65500    | DenyAllOutBound | Any | Any | Any | Any | Verweigern |

>[!NOTE]
> Für Azure AD DS ist ein uneingeschränkter ausgehender Zugriff vom virtuellen Netzwerk erforderlich. Es wird empfohlen, keine zusätzlichen Regeln zu erstellen, die den ausgehenden Zugriff für das virtuelle Netzwerk einschränken.

## <a name="verify-and-edit-existing-security-rules"></a>Überprüfen und Bearbeiten von vorhandenen Sicherheitsregeln

Führen Sie die folgenden Schritte aus, um die vorhandenen Sicherheitsregeln zu überprüfen und sicherzustellen, dass die Standardports geöffnet sind:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.
1. Wählen Sie die Netzwerksicherheitsgruppe aus, die Ihrer verwalteten Domäne zugeordnet ist, z.B. *AADDS-contoso.com-NSG*.
1. Auf der Seite **Übersicht** werden die vorhandenen Eingangs- und Ausgangssicherheitsregeln angezeigt.

    Überprüfen Sie die Eingangs- und Ausgangsregeln, und vergleichen Sie sie mit der Liste der erforderlichen Regeln im vorherigen Abschnitt. Wählen Sie bei Bedarf alle benutzerdefinierten Regeln aus, die den erforderlichen Datenverkehr blockieren, und löschen Sie sie. Wenn eine der erforderlichen Regeln fehlt, fügen Sie eine Regel hinzu, wie im nächsten Abschnitt beschrieben.

    Nachdem Sie Regeln hinzugefügt oder gelöscht haben, um den erforderlichen Datenverkehr zuzulassen, wird die Integrität der verwalteten Domäne innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

### <a name="add-a-security-rule"></a>Hinzufügen einer Sicherheitsregel

Führen Sie die folgenden Schritte aus, um eine fehlende Sicherheitsregel hinzuzufügen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.
1. Wählen Sie die Netzwerksicherheitsgruppe aus, die Ihrer verwalteten Domäne zugeordnet ist, z.B. *AADDS-contoso.com-NSG*.
1. Klicken Sie im linken Bereich unter **Einstellungen** entweder auf *Eingangssicherheitsregeln* oder auf *Ausgangssicherheitsregeln*, je nachdem, welche Regel hinzugefügt werden muss.
1. Wählen Sie **Hinzufügen** aus, und erstellen Sie dann anhand von Port, Protokoll, Richtung usw. die erforderliche Regel. Wählen Sie nach Abschluss des Vorgangs **OK** aus.

Es dauert einen Moment, bis die Sicherheitsregel hinzugefügt und in der Liste angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

Falls weiterhin Probleme auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um weitere Hilfe bei der Problembehandlung zu erhalten.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
