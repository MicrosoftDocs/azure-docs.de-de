---
title: Azure AD Domain Services für Cloudlösungsanbieter | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie durch Azure Active Directory Domain Services verwaltete Domänen für Azure Cloud Solution Providers aktivieren und verwalten
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619898"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Bereitstellen und Verwalten von Azure Active Directory Domain Services für Azure Cloud Solution Providers

Azure Cloud Solutions Providers (CSP) ist ein Programm für Microsoft-Partner und stellt einen Lizenzkanal für verschiedene Microsoft-Clouddienste bereit. Mit Azure CSP können Partner den Vertrieb steuern, Kunden die jeweilige Nutzung in Rechnung stellen, technischen Support und Abrechnungssupport bieten und als einziger Ansprechpartner des Kunden fungieren. Darüber hinaus bietet Azure CSP eine vollständige Sammlung von Tools, z.B. ein Self-Service-Portal und zugehörige APIs. Mit diesen Tools können CSP-Partner auf einfache Weise Azure-Ressourcen bereitstellen und verwalten sowie Abrechnungsfunktionen für Kunden und ihre Abonnements bereitstellen.

Das [Partner Center-Portal](/partner-center/azure-plan-lp) ist der Einstiegspunkt für alle Azure CSP-Partner und bietet umfassende Funktionen für die Kundenverwaltung, automatisierte Verarbeitung und vieles mehr. Azure CSP-Partner können die Partner Center-Funktionen über eine webbasierte Benutzeroberfläche oder mithilfe von PowerShell und verschiedenen API-Aufrufen verwenden.

Die folgende Abbildung veranschaulicht die Funktionsweise des CSP-Modells auf Übersichtsebene. In diesem Beispiel verfügt Contoso über einen Azure Active Directory (Azure AD)-Mandanten. Das Unternehmen hat eine Partnerschaft mit einem CSP, der Ressourcen in seinem Azure CSP-Abonnement bereitstellt und verwaltet. Contoso verfügt möglicherweise auch über reguläre (direkte) Azure-Abonnements, die Contoso direkt in Rechnung gestellt werden.

![Übersicht über das CSP-Modell](./media/csp/csp_model_overview.png)

Der Mandant des CSP-Partners verwendet drei spezielle Agent-Gruppen: *Administrator*-Agents, *Helpdesk*-Agents und *Vertriebs*-Agents.

Der Gruppe „*Administrator*-Agents“ wird die Mandantenadministratorrolle im Azure AD-Mandanten von Contoso zugewiesen. Daher verfügt ein Benutzer, der zur Gruppe „Administrator-Agents“ des CSP-Partners gehört, über Mandantenadministratorrechte im Azure AD-Mandanten von Contoso.

Wenn der CSP-Partner ein Azure CSP-Abonnement für Contoso bereitstellt, wird der Gruppe der Administrator-Agents die Besitzerrolle für dieses Abonnement zugewiesen. Daher verfügen die Administrator-Agents des CSP-Partners über die erforderlichen Berechtigungen zum Bereitstellen von Azure-Ressourcen wie virtuellen Computern, virtuellen Netzwerken und Azure AD Domain Services im Auftrag von Contoso.

Weitere Informationen finden Sie in der [Übersicht zu Azure CSP](/partner-center/azure-plan-lp).

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Vorteile der Verwendung von Azure AD DS in einem Azure CSP-Abonnement

Azure Active Directory Domain Services (Azure AD DS) stellt verwaltete Domänendienste (z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos-/NTLM-Authentifizierung) bereit, die mit Windows Server Active Directory Domain Services vollständig kompatibel sind. Im Laufe der Jahrzehnte wurden viele Anwendungen erstellt, die mit diesen Funktionen in AD zusammenarbeiten. Viele unabhängige Softwarehersteller (ISVs) haben Anwendungen vor Ort bei ihren Kunden erstellt und bereitgestellt. Diese Anwendungen sind schwer zu unterstützen, da dies oft den Zugriff auf die verschiedenen Umgebungen erfordert, in denen diese Anwendungen bereitgestellt werden. Mit Azure CSP-Abonnements haben Sie eine einfachere Alternative mit der Skalierung und Flexibilität von Azure.

Azure AD DS unterstützt Azure CSP-Abonnements. Sie können Ihre Anwendung in einem Azure CSP-Abonnement bereitstellen, das an den Azure AD-Mandanten Ihres Kunden gebunden ist. Auf diese Weise können Ihre Mitarbeiter (Supportpersonal) die virtuellen Computer, auf denen Ihre Anwendung bereitgestellt wird, mit den Anmeldeinformationen Ihres Unternehmens verwalten, pflegen und warten.

Sie können auch im Azure AD Mandanten Ihres Kunden eine in Azure AD DS verwaltete Domäne bereitstellen. Ihre Anwendung wird dann mit der verwalteten Domäne Ihres Kunden verbunden. Funktionen innerhalb Ihrer Anwendung, die auf Kerberos/NTLM, LDAP oder die [System.DirectoryServices-API](/dotnet/api/system.directoryservices) angewiesen sind, arbeiten nahtlos mit der Domäne Ihres Kunden zusammen. Ihre Endkunden profitieren von der Nutzung Ihrer Anwendung als Dienst, ohne sich um die Verwaltung der Infrastruktur kümmern zu müssen, in der die Anwendung bereitgestellt wird.

Alle Abrechnungen für Azure-Ressourcen, die Sie in diesem Abonnement verbrauchen (einschließlich Azure AD DS), werden Ihnen belastet. Sie behalten die vollständige Kontrolle über die Beziehung zum Kunden, wenn es um Verkauf, Abrechnung, technischen Support usw. geht. Mit der Flexibilität der Azure CSP-Plattform kann ein kleines Team von Supportmitarbeitern viele solcher Kunden betreuen, die Instanzen Ihrer Anwendung bereitstellen.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>CSP-Bereitstellungsmodelle für Azure AD DS

Es gibt zwei Möglichkeiten für die Verwendung von Azure AD DS mit einem Azure CSP-Abonnement. Entscheiden Sie sich basierend auf den Sicherheits- und Einfachheitserwägungen Ihrer Kunden für die richtige Lösung.

### <a name="direct-deployment-model"></a>Direktes Bereitstellungsmodell

In diesem Bereitstellungsmodell wird Azure AD DS innerhalb eines virtuellen Netzwerks aktiviert, das zum Azure CSP-Abonnement gehört. Die Administrator-Agents des CSP-Partners verfügen über die folgenden Berechtigungen:

* *Globaler Administrator* im Azure AD-Mandanten des Kunden.
* *Abonnementbesitzer* für das Azure CSP-Abonnement.

![Direktes Bereitstellungsmodell](./media/csp/csp_direct_deployment_model.png)

In diesem Bereitstellungsmodell können die Administrator-Agents des CSP-Anbieters Identitäten für den Kunden verwalten. Diese Administrator-Agents können Aufgaben wie Bereitstellen neuer Benutzer oder Gruppen oder Hinzufügen von Anwendungen im Azure AD-Mandanten des Kunden ausführen.

Dieses Bereitstellungsmodell ist möglicherweise für kleinere Organisationen geeignet, die keinen dedizierten Identitätsadministrator haben oder es bevorzugen, dass der CSP-Partner Identitäten in ihrem Auftrag verwaltet.

### <a name="peered-deployment-model"></a>Peerbereitstellungsmodell

In diesem Bereitstellungsmodell wird Azure AD DS in einem virtuellen Netzwerk aktiviert, das dem Kunden gehört – ein direktes Azure-Abonnement, das vom Kunden bezahlt wird. Der CSP-Partner kann Anwendungen innerhalb eines virtuellen Netzwerks bereitstellen, das zum CSP-Abonnement des Kunden gehört. Die virtuellen Netzwerke können dann mithilfe von Azure-Peering virtueller Netzwerke verbunden werden.

Durch diese Bereitstellung können die vom CSP-Partner im Azure CSP-Abonnement bereitgestellten Workloads oder Anwendungen mit der verwalteten Domäne des Kunden verbunden werden, die im direkten Azure-Abonnement des Kunden bereitgestellt wird.

![Peerbereitstellungsmodell](./media/csp/csp_peered_deployment_model.png)

Dieses Bereitstellungsmodell bietet eine Trennung der Berechtigungen und ermöglicht es den Helpdesk-Agents des CSP-Partners, das Azure-Abonnement zu verwalten und die darin enthaltenen Ressourcen bereitzustellen und zu verwalten. Die Helpdesk-Agents des CSP-Partners benötigen jedoch keine globalen Administratorrechte für das Azure AD-Verzeichnis des Kunden. Die Identitätsadministratoren des Kunden können weiterhin Identitäten für ihre Organisation verwalten.

Dieses Bereitstellungsmodell eignet sich möglicherweise für Szenarien, in denen ein ISV eine gehostete Version seiner lokalen Anwendung bereitstellt, die ebenfalls eine Verbindung mit dem Azure AD des Kunden herstellen muss.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Verwalten von Azure AD DS in CSP-Abonnements

Die folgenden wichtigen Überlegungen gelten für die Verwaltung einer verwalteten Domäne in einem Azure CSP-Abonnement:

* **CSP-Administrator-Agents können eine verwaltete Domäne mit ihren Anmeldeinformationen bereitstellen:** Azure AD DS unterstützt Azure CSP-Abonnements. Benutzer, die zur Gruppe der Administrator-Agents eines CSP-Partners gehören, können eine neue verwaltete Domäne bereitstellen.

* **CSPs können Skripte für die Erstellung von neuen verwalteten Domänen für ihre Kunden mithilfe von PowerShell bereitstellen:** Ausführliche Informationen finden Sie unter [Aktivieren von Azure AD DS mithilfe von PowerShell](powershell-create-instance.md).

* **CSP-Administrator-Agents können in der verwalteten Domäne keine laufenden Verwaltungsaufgaben mit ihren Anmeldeinformationen ausführen:** CSP-Administratorbenutzer können innerhalb der verwalteten Domäne keine routinemäßigen Verwaltungsaufgaben mit ihren Anmeldeinformationen ausführen. Diese Benutzer sind für den Azure AD-Mandanten des Kunden externe Benutzer, deren Anmeldeinformationen im Azure AD-Mandanten des Kunden nicht verfügbar sind. Azure AD DS hat keinen Zugriff auf die Kerberos- und NTLM-Kennworthashes für diese Benutzer, sodass diese in verwalteten Domänen nicht authentifiziert werden können.

  > [!WARNING]
  > Sie müssen ein Benutzerkonto im Verzeichnis des Kunden erstellen, um laufende Verwaltungsaufgaben für die verwaltete Domäne auszuführen.
  >
  > Sie können sich nicht mit den Anmeldeinformationen eines CSP-Administratorbenutzers bei der verwalteten Domäne anmelden. Verwenden Sie dafür die Anmeldeinformationen eines Benutzerkontos, das zum Azure AD-Mandanten des Kunden gehört. Sie benötigen diese Anmeldeinformationen für Aufgaben wie das Einbinden virtueller Computer in die verwaltete Domäne, die DNS-Verwaltung oder die Verwaltung von Gruppenrichtlinien.

* **Das für die laufende Verwaltung erstellte Benutzerkonto muss der Gruppe *AAD DC-Administratoren* hinzugefügt werden:** Die Gruppe *AAD DC-Administratoren* verfügt über Berechtigungen, um bestimmte delegierte Verwaltungsaufgaben in der verwalteten Domäne auszuführen. Zu diesen Aufgaben gehören die Konfiguration von DNS, das Erstellen von Organisationseinheiten und die Verwaltung von Gruppenrichtlinien.
    
    Damit ein CSP-Partner diese Aufgaben für eine verwaltete Domäne ausführen kann, muss im Azure AD-Mandanten des Kunden ein Benutzerkonto erstellt werden. Die Anmeldeinformationen für dieses Konto müssen für die Administrator-Agents des CSP-Partners freigegeben werden. Außerdem muss dieses Benutzerkonto der Gruppe *AAD DC-Administratoren* hinzugefügt werden, damit Konfigurationsaufgaben für die verwaltete Domäne mit diesem Benutzerkonto ausgeführt werden können.

## <a name="next-steps"></a>Nächste Schritte

[Registrieren Sie sich zunächst beim Azure CSP-Programm](/partner-center/enrolling-in-the-csp-program). Sie können dann Azure AD Domain Services im [Azure-Portal](tutorial-create-instance.md) oder mit [Azure PowerShell](powershell-create-instance.md) aktivieren.