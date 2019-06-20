---
title: 'Azure Active Directory Domain Services: Übersicht | Microsoft Docs'
description: Übersicht über Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 4435407737508132fcf9f2ce6148650399e5316b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245435"
---
# <a name="azure-active-directory-ad-domain-services"></a>Azure Active Directory-Domänendienste (AD)
## <a name="overview"></a>Übersicht
Mithilfe von Azure-Infrastrukturdiensten können Sie eine Vielzahl von Computinglösungen auf flexible Weise bereitstellen. Dank Azure Virtual Machines können Sie Ihre Produkte praktisch sofort bereitstellen und erhalten eine minutengenaue Abrechnung. Mit der Unterstützung für Windows, Linux, SQL Server, Oracle, IBM, SAP und BizTalk ist die Bereitstellung jeder Workload in jeder Sprache auf fast jedem Betriebssystem möglich. Diese Vorteile versetzen Sie in die Lage, lokal bereitgestellte Legacyanwendungen zu Azure zu migrieren, um Betriebskosten zu sparen.

Ein wichtiger Aspekt der Migration von lokalen Anwendung zu Azure ist der Umgang mit den Identitätsanforderungen dieser Anwendungen. Verzeichnisorientierte Anwendungen nutzen ggf. LDAP für den Lese- und Schreibzugriff auf das Unternehmensverzeichnis oder die integrierte Windows-Authentifizierung (Kerberos- oder NTLM-Authentifizierung), um Endbenutzer zu authentifizieren. Branchenanwendungen, die unter Windows Server ausgeführt werden, werden normalerweise auf Computern bereitgestellt, die einer Domäne beigetreten sind. So können sie per Gruppenrichtlinie auf sichere Weise verwaltet werden. Um lokale Anwendungen in die Cloud verlagern zu können („Lift-and-Shift“), müssen diese Abhängigkeiten von der Identitätsinfrastruktur des Unternehmens beseitigt werden.

Administratoren nutzen häufig eine der folgenden Lösungen, um die Identitätsanforderungen der unter Azure bereitgestellten Anwendungen zu erfüllen:

* Bereitstellen einer Standort-zu-Standort-VPN-Verbindung zwischen Workloads, die in Azure-Infrastrukturdiensten ausgeführt werden, und dem lokalen Unternehmensverzeichnis
* Erweitern der AD-Domäne/Gesamtstruktur-Infrastruktur des Unternehmens, indem Replikatdomänencontroller mithilfe von virtuellen Azure-Computern eingerichtet werden
* Bereitstellen einer eigenständigen Domäne in Azure, indem als virtuelle Azure-Computer bereitgestellte Domänencontroller verwendet werden

All diese Ansätze sind mit hohen Kosten und hohem Verwaltungsaufwand verbunden. Administratoren müssen Domänencontroller mithilfe von virtuellen Azure-Computern bereitstellen. Sie müssen diese virtuellen Computer außerdem verwalten, schützen, patchen, überwachen und sichern und die Problembehandlung dafür durchführen. Aufgrund der Abhängigkeit von VPN-Verbindungen vom lokalen Verzeichnis sind unter Azure bereitgestellte Workloads für vorübergehende Netzwerkprobleme oder -ausfälle anfällig. Diese Netzwerkausfälle führen zu einer kürzere Betriebszeit und Zuverlässigkeit für diese Anwendungen.

Die Azure AD-Domänendienste sind für die Bereitstellung einer deutlich einfacheren Alternative konzipiert.

### <a name="watch-an-introductory-video"></a>Sehen Sie sich das Einführungsvideo an

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Einführung in Azure AD-Domänendienste

Azure AD Domain Services stellen verwaltete Domänendienste bereit, z.B. Domänenbeitritt, Gruppenrichtlinie, LDAP, Kerberos/NTLM-Authentifizierung, die mit Windows Server Active Directory vollständig kompatibel sind. Sie können diese Domänendienste nutzen, ohne dass Sie Domänencontroller in der Cloud bereitstellen, verwalten und patchen müssen. Die Azure AD-Domänendienste können in Ihren vorhandenen Azure AD-Mandanten integriert werden, wodurch es Benutzern ermöglicht wird, sich mit ihren Unternehmensanmeldeinformationen anzumelden. Außerdem können Sie vorhandene Gruppen und Benutzerkonten verwenden, um den Zugriff auf Ressourcen zu schützen. So stellen Sie sicher, dass die Verlagerung von lokalen Ressourcen auf Azure-Infrastrukturdienste reibungsloser verläuft.

Die Azure AD-Domänendienste arbeiten unabhängig davon nahtlos, ob Ihr Azure AD-Mandant auf die Cloud beschränkt ist oder mit Ihrer lokalen Active Directory-Instanz synchronisiert ist.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD-Domänendienste für Organisationen mit vollständiger Umstellung auf die Cloud

Ein auf die Cloud beschränkter Azure AD-Mandant (häufig als „verwaltete Mandanten“ bezeichnet) verfügt nicht über einen lokalen Identitätsfußabdruck. Anders ausgedrückt: Benutzerkonten, die zugehörigen Kennwörter und Gruppenmitgliedschaften liegen als native Daten in der Cloud vor und werden deshalb in Azure AD erstellt und verwaltet. Stellen Sie sich vor, dass Contoso ein auf die Cloud beschränkter Azure AD-Mandant ist. Wie in der folgenden Abbildung dargestellt wird, hat der Administrator von Contoso ein virtuelles Netzwerk in den Azure-Infrastrukturdiensten konfiguriert. Anwendungen und Serverworkloads werden in diesem virtuellen Netzwerk auf virtuellen Azure-Computern bereitgestellt. Da es sich bei Contoso um einen auf die Cloud beschränkten Mandanten handelt, werden alle Benutzeridentitäten, ihre Anmeldeinformationen und Gruppenmitgliedschaften in Azure AD erstellt und verwaltet.

![Azure AD-Domänendienste – Übersicht](./media/active-directory-domain-services-overview/aadds-overview.png)

Der IT-Administrator von Contoso kann Azure AD-Domänendienste für den Azure AD-Mandanten aktivieren und Domänendienste in diesem virtuellen Netzwerk zur Verfügung stellen. Anschließend stellen die Azure AD-Domänendienste eine verwaltete Domäne bereit und machen diese im virtuellen Netzwerk verfügbar. Alle Benutzerkonten, Gruppenmitgliedschaften und Benutzeranmeldeinformationen, die im Azure AD-Mandanten von Contoso verfügbar sind, sind auch in dieser neu erstellten Domäne verfügbar. Mit diesem Feature können Benutzer in der Organisation sich mit ihren Unternehmensanmeldeinformationen bei der Domäne anmelden, z.B. wenn per Remotedesktop eine Remoteverbindung mit Computern hergestellt wird, die der Domäne beigetreten sind. Administratoren können den Zugriff auf Ressourcen in der Domäne mit einer bereits vorhandenen Gruppenmitgliedschaft bereitstellen. Anwendungen, die auf virtuellen Computern im virtuellen Netzwerk bereitgestellt werden, können Features wie etwa Domänenbeitritt, LDAP-Lesevorgang, LDAP-Bindung, NTLM- und Kerberos-Authentifizierung sowie Gruppenrichtlinie nutzen.

Einige wichtige Aspekte der verwalteten Domäne, die von den Azure AD-Domänendiensten bereitgestellt wird, lauten:

* Der IT-Administrator von Contoso muss diese Domäne oder die Domänencontroller für diese verwaltete Domäne nicht verwalten, patchen oder überwachen.
* Es ist nicht erforderlich, die AD-Replikation für diese Domäne zu verwalten. Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen für den Azure AD-Mandanten von Contoso stehen innerhalb dieser verwalteten Domäne automatisch zur Verfügung.
* Da die Domäne über die Azure AD-Domänendienste verwaltet wird, verfügt der IT-Administrator von Contoso in dieser Domäne nicht über Domänenadministrator- oder Unternehmensadministratorrechte.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure Active Directory-Domänendienste für Hybridunternehmen
Unternehmen mit einer IT-Hybridinfrastruktur nutzen eine Mischung aus Cloudressourcen und lokalen Ressourcen. In diesen Unternehmen werden Identitätsinformationen im lokalen Verzeichnis mit dem Azure AD-Mandanten synchronisiert. Wenn Hybridorganisationen weitere lokale Anwendungen zur Cloud migrieren möchten, insbesondere verzeichnisfähige Legacyanwendungen, können die Azure AD-Domänendienste hierfür nützlich sein.

Die Litware Corporation hat [Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md)bereitgestellt, um Identitätsinformationen aus dem lokalen Verzeichnis mit dem Azure AD-Mandanten zu synchronisieren. Zu den synchronisierten Identitätsinformationen zählen Benutzerkonten, Anmeldeinformationshashes für die Authentifizierung (Kennworthashsynchronisierung) und Gruppenmitgliedschaften.

> [!NOTE]
> **Die Kennworthashsynchronisierung für Hybridorganisationen ist für die Verwendung von Azure AD-Domänendiensten verpflichtend**. Dies liegt daran, dass die Anmeldeinformationen der Benutzer in der von den Azure AD-Domänendiensten bereitgestellten verwalteten Domäne erforderlich sind, um diese Benutzer mithilfe von NTLM- oder Kerberos-Authentifizierungsverfahren authentifizieren zu können.
>
>

![Azure AD-Domänendienste für Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

In der obigen Abbildung ist zu sehen, wie Unternehmen mit IT-Hybridinfrastruktur, z.B. die Litware Corporation, die Azure AD-Domänendienste verwenden können. Die Anwendungen und Serverworkloads von Litware, die Domänendienste erfordern, werden in einem virtuellen Netzwerk unter den Azure-Infrastrukturdiensten bereitgestellt. Der IT-Administrator von Litware kann Azure AD-Domänendienste für den Azure AD-Mandanten aktivieren und eine verwaltete Domäne in diesem virtuellen Netzwerk zur Verfügung stellen. Da Litware ein Unternehmen mit einer IT-Hybridinfrastruktur ist, werden Benutzerkonten, Gruppen und Anmeldeinformationen aus dem lokalen Verzeichnis mit dem Azure AD-Mandanten synchronisiert. Mit diesem Feature können Benutzer sich mit ihren Unternehmensanmeldeinformationen bei der Domäne anmelden, z.B. wenn per Remotedesktop eine Remoteverbindung mit Computern hergestellt wird, die der Domäne beigetreten sind. Administratoren können den Zugriff auf Ressourcen in der Domäne mit einer bereits vorhandenen Gruppenmitgliedschaft bereitstellen. Anwendungen, die auf virtuellen Computern im virtuellen Netzwerk bereitgestellt werden, können Features wie etwa Domänenbeitritt, LDAP-Lesevorgang, LDAP-Bindung, NTLM- und Kerberos-Authentifizierung sowie Gruppenrichtlinie nutzen.

Einige wichtige Aspekte der verwalteten Domäne, die von den Azure AD-Domänendiensten bereitgestellt wird, lauten:

* Die verwaltete Domäne ist eine eigenständige Domäne. Sie stellt keine Erweiterung der lokalen Domäne von Litware dar.
* Der IT-Administrator von Litware muss die Domänencontroller für diese verwaltete Domäne nicht verwalten, patchen oder überwachen.
* Es ist nicht erforderlich, die AD-Replikation für diese Domäne zu verwalten. Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen aus dem lokalen Verzeichnis von Litware werden über Azure AD Connect mit Azure AD synchronisiert. Diese Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen stehen innerhalb der verwalteten Domäne automatisch zur Verfügung.
* Da die Domäne über die Azure AD-Domänendienste verwaltet wird, verfügt der IT-Administrator von Litware in dieser Domäne nicht über Domänenadministrator- oder Unternehmensadministratorrechte.

## <a name="benefits"></a>Vorteile
Mit Azure AD-Domänendiensten kommen Sie in den Genuss der folgenden Vorteile:

* **Einfach:** Sie können die Identitätsanforderungen für virtuelle Computer, die in den Azure-Infrastrukturdiensten bereitgestellt wurden, mit einigen wenigen Klicks erfüllen. Es ist nicht erforderlich, eine Identitätsinfrastruktur in Azure bereitzustellen und zu verwalten oder Konnektivität mit Ihrer lokalen Identitätsinfrastruktur einzurichten.
* **Integration:** Die Azure AD-Domänendienste sind tief in Ihren Azure AD-Mandanten integriert. Sie können Azure AD jetzt als integriertes cloudbasiertes Unternehmensverzeichnis nutzen, das sowohl die Anforderungen Ihrer modernen Anwendungen als auch die Anforderungen von traditionellen verzeichnisorientierten Anwendungen erfüllt.
* **Kompatibel:** Die Azure AD-Domänendienste basieren auf der bewährten, für Unternehmen konzipierten Infrastruktur von Windows Server Active Directory. Aus diesem Grund sind Ihre Anwendungen in höherem Maße mit Windows Server Active Directory-Features kompatibel. Derzeit sind nicht alle in Windows Server AD verfügbaren Features in den Azure AD-Domänendiensten verfügbar. Die verfügbaren Features sind aber mit den entsprechenden Windows Server AD-Features kompatibel, die Sie in Ihrer lokalen Infrastruktur nutzen. LDAP, Kerberos, NTLM, Gruppenrichtlinie und Domänenbeitritt sind ausgereifte Features, die über mehrere Windows Server-Versionen hinweg getestet und optimiert wurde.
* **Kosteneffektivität:** Mit Azure AD-Domänendiensten können Sie den Infrastruktur- und Verwaltungsaufwand vermeiden, der mit der Verwaltung der Identitätsinfrastruktur zur Unterstützung herkömmlicher verzeichnisorientierter Anwendungen verbunden ist. Sie können diese Anwendungen auf die Azure-Infrastrukturdienste verlagern und von höheren Einsparungen bei den Betriebskosten profitieren.


## <a name="next-steps"></a>Nächste Schritte
### <a name="learn-more-about-azure-ad-domain-services"></a>Weitere Informationen zu Azure AD Domain Services
* [Funktionen](active-directory-ds-features.md)
* [Bereitstellungsszenarien](scenarios.md)
* [So entscheiden Sie, ob Azure AD Domain Services für Ihren Anwendungsfall geeignet ist](comparison.md)
* [Synchronisierung in einer durch Azure AD Domain Services verwalteten Domäne](synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Erste Schritte mit Azure AD Domain Services
* [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals (Vorschauversion)](create-instance.md)
