---
title: Problembehandlung von Warnungen bei Secure LDAP in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme und häufige Warnungen im Zusammenhang mit Secure LDAP für Azure Active Directory Domain Services behandeln.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620306"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Bekannte Probleme: Warnungen bei Secure LDAP in Azure Active Directory Domain Services

Anwendungen und Dienste, die LDAP (Lightweight Directory Access Protocol) für die Kommunikation mit Azure Active Directory Domain Services (Azure AD DS) verwenden, [können für die Verwendung von Secure LDAP konfiguriert werden](tutorial-configure-ldaps.md). Ein geeignetes Zertifikat muss vorhanden sein, und die erforderlichen Netzwerkports müssen geöffnet sein, damit Secure LDAP ordnungsgemäß funktioniert.

Dieser Artikel hilft Ihnen dabei, häufige Warnungen bei einem Zugriff über Secure LDAP in Azure AD DS zu verstehen und aufzulösen.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Secure LDAP-Netzwerkkonfiguration

### <a name="alert-message"></a>Warnmeldung

*Secure LDAP über das Internet ist für die verwaltete Domäne aktiviert. Der Zugriff auf Port 636 ist jedoch nicht über eine Netzwerksicherheitsgruppe gesperrt. Dies kann Brute-Force-Kennwortangriffe auf Benutzerkonten in der verwalteten Domäne möglich machen.*

### <a name="resolution"></a>Lösung

Wenn Sie Secure LDAP aktivieren, empfiehlt es sich, zusätzliche Regeln zu erstellen, die den eingehenden LDAPS-Zugriff auf bestimmte IP-Adressen einschränken. Diese Regeln schützen die verwaltete Domäne vor Brute-Force-Angriffen. Führen Sie die folgenden Schritte aus, um die Netzwerksicherheitsgruppe zu aktualisieren und den Zugriff auf TCP-Port 636 für Secure LDAP zu beschränken:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.
1. Wählen Sie die Netzwerksicherheitsgruppe aus, die Ihrer verwalteten Domäne zugeordnet ist, z.B. *AADDS-contoso.com-NSG*, und wählen Sie dann **Eingangssicherheitsregeln** aus.
1. Wählen Sie **+ Hinzufügen** aus, um eine Regel für den TCP-Port 636 zu erstellen. Wählen Sie bei Bedarf im Fenster **Erweitert** aus, um eine Regel zu erstellen.
1. Wählen Sie als **Quelle** im Dropdown Menü *IP-Adressen* aus. Geben Sie die Quell-IP-Adressen ein, denen Sie Zugriff auf LDAPS-Datenverkehr gewähren möchten.
1. Wählen Sie *Beliebig* als **Ziel** aus, und geben Sie dann *636* für **Zielportbereiche** ein.
1. Legen Sie *TCP* als **Protokoll** und *Zulassen* als **Aktion** fest.
1. Geben Sie die Priorität für die Regel an, und geben Sie dann einen Namen ein, z.B. *RestrictLDAPS*.
1. Wählen Sie abschließend **Hinzufügen** aus, um die Regel zu erstellen.

Die Integrität der verwalteten Domäne wird innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

> [!TIP]
> TCP-Port 636 ist nicht die einzige Regel, die erforderlich ist, damit Azure AD DS reibungslos ausgeführt wird. Weitere Informationen finden Sie unter [Azure AD DS-Netzwerksicherheitsgruppen und erforderliche Ports](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP-Zertifikat läuft ab

### <a name="alert-message"></a>Warnmeldung

*Das sichere LDAP-Zertifikat für die verwaltete Domäne läuft am [Datum] ab.*

### <a name="resolution"></a>Lösung

Erstellen Sie ein Ersatz-Secure LDAP-Zertifikat, indem Sie die unter [Erstellen eines Zertifikats für Secure LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap) beschriebenen Schritte ausführen. Wenden Sie das Ersatzzertifikat auf Azure AD DS an, und verteilen Sie das Zertifikat an alle Clients, die mithilfe von Secure LDAP eine Verbindung herstellen.

## <a name="next-steps"></a>Nächste Schritte

Falls weiterhin Probleme auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um weitere Hilfe bei der Problembehandlung zu erhalten.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
