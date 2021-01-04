---
title: 'Azure Active Directory Domain Services: Übersicht | Microsoft Docs'
description: In dieser Übersicht erfahren Sie, welche Funktionen Azure Active Directory Domain Services (Azure AD DS) bietet und wie Sie diese Funktionen in Ihrer Organisation verwenden, um Identitätsdienste für Anwendungen und Dienste in der Cloud bereitzustellen.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2020
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 99a207810f51949b0da0e78b1cb0e0a7f8bc95e3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027338"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Was ist Azure Active Directory Domain Services?

Azure Active Directory Domain Services (AD DS) verfügt über verwaltete Domänendienste wie Domänenbeitritt, Gruppenrichtlinien, Lightweight Directory Access Protocol (LDAP) und Kerberos/NTLM-Authentifizierung. Sie können diese Domänendienste nutzen, ohne Domänencontroller (DCs) in der Cloud bereitstellen, verwalten und patchen zu müssen.

Mit einer verwalteten Azure AD DS-Domäne können Sie Legacyanwendungen in der Cloud ausführen, für die keine modernen Authentifizierungsmethoden genutzt werden können oder bei denen Sie nicht möchten, dass Verzeichnissuchen immer in einer lokalen AD DS-Umgebung durchgeführt werden. Sie können diese Legacyanwendungen per Lift & Shift-Vorgang aus Ihrer lokalen Umgebung in eine verwaltete Domäne verschieben, ohne dass Sie die AD DS-Umgebung in der Cloud verwalten müssen.

Azure AD DS lässt sich in Ihren vorhandenen Azure AD-Mandanten integrieren. Diese Integration ermöglicht Benutzern, sich bei Diensten und Anwendungen, die mit der verwalteten Domäne verbunden sind, mit ihren vorhandenen Anmeldeinformationen anzumelden. Sie können auch vorhandene Gruppen und Benutzerkonten verwenden, um den Zugriff auf Ressourcen abzusichern. So können Sie für eine reibungslosere Lift & Shift-Migration lokaler Ressourcen zu Azure sorgen.

> [!div class="nextstepaction"]
> [Im ersten Schritt erstellen Sie im Azure-Portal eine verwaltete Azure AD DS-Domäne][tutorial-create].

## <a name="how-does-azure-ad-ds-work"></a>Wie funktioniert Azure AD DS?

Beim Erstellen einer verwalteten Azure AD DS-Domäne definieren Sie einen eindeutigen Namespace. Dieser Namespace ist der Domänenname, z. B. *aaddscontoso.com*. Anschließend werden zwei Windows Server-Domänencontroller (DCs) in Ihrer ausgewählten Azure-Region bereitgestellt. Diese Bereitstellung von Domänencontrollern wird als Replikatgruppe bezeichnet.

Sie müssen diese Domänencontroller nicht verwalten, konfigurieren oder aktualisieren. Die Azure-Plattform führt die Schritte für die Domänencontroller im Rahmen der verwalteten Domäne aus – einschließlich Sicherung und Verschlüsselung ruhender Daten mit Azure Disk Encryption.

Eine verwaltete Domäne ist so konfiguriert, dass sie eine unidirektionale Synchronisierung von Azure AD durchführt, um Zugriff auf einen zentralen Satz mit Benutzern, Gruppen und Anmeldeinformationen zu ermöglichen. Sie können Ressourcen direkt in der verwalteten Domäne erstellen, die aber mit Azure AD nicht erneut synchronisiert werden. Anwendungen, Dienste und VMs in Azure, die eine Verbindung mit der verwalteten Domäne herstellen, können gemeinsame AD DS-Features wie Domänenbeitritt, Gruppenrichtlinien, LDAP und Kerberos- bzw. NTLM-Authentifizierung nutzen.

In einer Hybridumgebung mit einer lokalen AD DS-Umgebung synchronisiert [Azure AD Connect][azure-ad-connect] Identitätsinformationen mit Azure AD, die dann wiederum mit der verwalteten Domäne synchronisiert werden.

![Synchronisierung in Azure AD Domain Services mit Azure AD und lokaler AD DS-Instanz per AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS repliziert Identitätsinformationen aus Azure AD und lässt sich daher für rein cloudbasierte Azure AD-Mandanten sowie für Mandanten einsetzen, die mit einer lokalen AD DS-Umgebung synchronisiert werden. Für beide Umgebungen stehen die gleichen Azure AD DS-Features zur Verfügung.

* Wenn Sie bereits über eine lokale AD DS-Umgebung verfügen, können Sie Benutzerkontoinformationen synchronisieren, um eine konsistente Identität für Benutzer bereitzustellen. Weitere Informationen finden Sie unter [Synchronisieren von Objekten und Anmeldeinformationen in einer verwalteten Azure AD Domain Services-Domäne][synchronization].
* In reinen Cloudumgebungen benötigen Sie keine herkömmliche AD DS-Umgebung, um die zentralisierten Identitätsdienste von Azure AD DS zu nutzen.

Sie können eine verwaltete Domäne erweitern, sodass mehrere Replikatgruppen pro Azure AD-Mandant festgelegt sind. Replikatgruppen können einem beliebigen virtuellen Netzwerk mit Peering in einer beliebigen Azure-Region mit Azure AD DS-Unterstützung hinzugefügt werden. Zusätzliche Replikatgruppen in verschiedenen Azure-Regionen bieten eine geografische Notfallwiederherstellung für Legacyanwendungen, wenn eine Azure-Region offline geschaltet wird. Replikatgruppen sind derzeit als Vorschau verfügbar. Weitere Informationen finden Sie unter [Konzepte und Features von Replikatgruppen für Azure Active Directory Domain Services (Vorschau)][concepts-replica-sets].

Das folgende Video bietet einen Überblick über die Integration von Azure AD DS in Ihre Anwendungen und Workloads zum Bereitstellen von Identitätsdiensten in der Cloud:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Sie können die folgenden Beispiele nutzen, um sich über die Szenarien der Azure AD DS-Bereitstellung zu informieren:

* [Azure AD DS für Hybridorganisationen](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS für rein cloudbasierte Organisationen](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS: Features und Vorteile

Azure AD DS bietet Identitätsdienste für Anwendungen und VMs in der Cloud. Dabei ist der Dienst bei Prozessen wie Domänenbeitritt, Secure LDAP (LDAPS), Gruppenrichtlinie, der Verwaltung von DNS sowie der Unterstützung für LDAP-basierte Bindungs- und Lesevorgänge vollständig kompatibel mit herkömmlichen AD DS-Umgebungen. Unterstützung für LDAP-Schreibvorgänge ist für Objekte verfügbar, die in der verwalteten Domäne erstellt wurden, aber nicht für Ressourcen, die aus Azure AD synchronisiert wurden.

Weitere Informationen zu Ihren Identitätsoptionen finden Sie auf der Seite mit dem [Vergleich von Azure AD DS mit Azure AD, AD DS auf Azure-VMs und einer lokalen AD DS-Instanz][compare].

Die folgenden Features von Azure AD DS vereinfachen die Bereitstellung und Verwaltung:

* **Vereinfachte Bereitstellung**: Sie können Azure AD DS-Funktionen für Ihren Azure AD-Mandanten über einen einzelnen Assistenten im Azure-Portal einrichten und verwalten.
* **In Azure AD integriert:** Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen stehen aus dem Azure AD-Mandanten automatisch zur Verfügung. Neue Benutzer, neue Gruppen oder Änderungen an Attributen in Ihrem Azure AD-Mandanten oder Ihrem lokalen AD DS-Verzeichnis werden automatisch mit Azure AD DS synchronisiert.
    * Konten in externen Verzeichnissen, die mit Ihrer Azure AD-Instanz verknüpft sind, sind in Azure AD DS nicht verfügbar. Da für diese externen Verzeichnisse keine Anmeldeinformationen verfügbar sind, können sie nicht mit einer verwalteten Domäne synchronisiert werden.
* **Verwenden der Anmeldeinformationen/Kennwörter Ihres Unternehmens:** Kennwörter für Benutzer in Azure AD DS sind mit denen in Ihrem Azure AD-Mandanten identisch. Benutzer können ihre Unternehmensanmeldeinformationen verwenden, um Computer in die Domäne aufzunehmen, sich interaktiv anzumelden oder Remotedesktop zu nutzen und die Authentifizierung gegenüber der verwalteten Domäne durchzuführen.
* **NTLM- und Kerberos-Authentifizierung:** Durch die Unterstützung der NTLM- und Kerberos-Authentifizierung können Sie Anwendungen bereitstellen, die auf der integrierten Windows-Authentifizierung beruhen.
* **Hochverfügbarkeit:** Azure AD DS umfasst mehrere Domänencontroller, die Hochverfügbarkeit für Ihre verwaltete Domäne bereitstellen. Diese Hochverfügbarkeit garantiert eine hohe Betriebszeit der Dienste und Ausfallsicherheit bei Fehlern.
    * In Regionen, die [Azure-Verfügbarkeitszonen][availability-zones] unterstützen, werden diese Domänencontroller für zusätzliche Resilienz ebenfalls über Zonen hinweg verteilt.
    * [Replikatgruppen][concepts-replica-sets] können auch verwendet werden, um eine geografische Notfallwiederherstellung für Legacyanwendungen für den Fall bereitzustellen, dass eine Azure-Region in den Offlinezustand versetzt wird.

Einige wichtige Aspekte einer verwalteten Domäne sind:

* Die verwaltete Domäne ist eine eigenständige Domäne. Es handelt sich nicht um eine Erweiterung einer lokalen Domäne.
    * Bei Bedarf können Sie eine unidirektionale ausgehende Gesamtstrukturvertrauensstellung aus Azure AD DS in einer lokalen AD DS-Umgebung erstellen. Weitere Informationen finden Sie unter [Ressourcengesamtstrukturkonzepte und -features für Azure Active Directory Domain Services][ forest-trusts].
* Ihr IT-Team muss keine Domänencontroller für die verwaltete Domäne verwalten, patchen oder überwachen.

In Hybridumgebungen, in denen AD DS lokal ausgeführt wird, müssen Sie sich nicht um die AD-Replikation in der verwalteten Domäne kümmern. Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen aus Ihrem lokalen Verzeichnis werden über [Azure AD Connect][azure-ad-connect] mit Azure AD synchronisiert. Diese Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen stehen innerhalb der verwalteten Domäne automatisch zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

Einen Vergleich zwischen Azure AD DS und anderen Identitätslösungen sowie Informationen zur Funktionsweise der Synchronisierung finden Sie in den folgenden Artikeln:

* [Vergleichen von Azure AD DS mit Azure AD, Active Directory Domain Services auf Azure-VMs und lokalen Active Directory Domain Services][compare]
* [Synchronisierung in einer durch Azure AD Domain Services verwalteten Domäne][synchronization]
* Weitere Informationen zum Verwalten einer verwalteten Domäne finden Sie unter [Verwaltungskonzepte für Benutzerkonten, Kennwörter und die Verwaltung in Azure AD DS][administration-concepts].

Im ersten Schritt [erstellen Sie im Azure-Portal eine verwaltete Domäne][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
