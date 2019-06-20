---
title: Azure Active Directory Domain Services für Azure Cloud Solution Provider | Microsoft Docs
description: Azure Active Directory Domain Services für Azure Cloud Solution Provider.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: ergreenl
ms.openlocfilehash: 7a5d6371641f4299cddf9157b27a60ad947fc109
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245343"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services für Azure Cloud Solution Provider (CSP)
In diesem Artikel wird erläutert, wie Sie Azure AD Domain Services in einem Azure CSP-Abonnement verwenden können.

## <a name="overview-of-azure-csp"></a>Übersicht über Azure CSP
Azure CSP ist ein Programm für Microsoft-Partner und stellt einen Lizenzkanal für verschiedene Microsoft Cloud Services bereit. Mit Azure CSP können Partner den Vertrieb steuern, Kunden die jeweilige Nutzung in Rechnung stellen, technischen Support und Abrechnungssupport bieten und als einziger Ansprechpartner des Kunden fungieren. Darüber hinaus bietet Azure CSP eine vollständige Sammlung von Tools, z.B. ein Self-Service-Portal und zugehörige APIs. Mit diesen Tools können CSP-Partner auf einfache Weise Azure-Ressourcen bereitstellen und verwalten sowie Abrechnungsfunktionen für Kunden und ihre Abonnements bereitstellen.

Das [Partner Center-Portal](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) fungiert als Einstiegspunkt für alle Azure CSP-Partner. Es stellt Kunden umfassende Kundenverwaltungsfunktionen, automatisierte Verarbeitung und vieles mehr zur Verfügung. Azure CSP-Partner können die Partner Center-Funktionen über eine webbasierte Benutzeroberfläche oder mithilfe von PowerShell und verschiedenen API-Aufrufen verwenden.

Die folgende Abbildung veranschaulicht die Funktionsweise des CSP-Modells auf Übersichtsebene. Contoso verfügt über ein Azure AD Active Directory. Das Unternehmen hat eine Partnerschaft mit einem CSP, der Ressourcen in seinem Azure CSP-Abonnement bereitstellt und verwaltet. Contoso verfügt möglicherweise auch über reguläre (direkte) Azure-Abonnements, die Contoso direkt in Rechnung gestellt werden.

![Übersicht über das CSP-Modell](./media/csp/csp_model_overview.png)

Der Mandant des CSP-Partners verwendet drei speziellen Agent-Gruppen: Administrator-Agents, Helpdesk-Agents und Vertriebs-Agents. Der Gruppe „Administrator-Agents“ wird die Mandantenadministratorrolle im Azure AD-Verzeichnis von Contoso zugewiesen. Daher verfügt ein Benutzer, der zur Gruppe der Administrator-Agents des CSP-Partners gehört, über Administratorrechte für den Mandanten im Azure AD-Verzeichnis von Contoso. Wenn der CSP-Partner ein Azure CSP-Abonnement für Contoso bereitstellt, wird der Gruppe der Administrator-Agents die Besitzerrolle für dieses Abonnement zugewiesen. Daher verfügen die Administrator-Agents des CSP-Partners über die erforderlichen Berechtigungen zum Bereitstellen von Azure-Ressourcen wie virtuellen Computern, virtuellen Netzwerken und Azure AD Domain Services im Auftrag von Contoso.

Weitere Informationen finden Sie in der [Übersicht zu Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Vorteile der Verwendung von Azure AD Domain Services in einem Azure CSP-Abonnement
Azure AD Domain Services stellt mit Windows Server AD kompatible Dienste in Azure bereit, z.B. LDAP, Kerberos-/NTLM-Authentifizierung, Domänenbeitritt, Gruppenrichtlinien und DNS. Im Laufe der Jahrzehnte wurden viele Anwendungen erstellt, die mit diesen Funktionen in AD zusammenarbeiten. Viele unabhängige Softwarehersteller (ISVs) haben Anwendungen vor Ort bei ihren Kunden erstellt und bereitgestellt. Diese Anwendungen sind schwer zu unterstützen, da dies oft den Zugriff auf die verschiedenen Umgebungen erfordert, in denen diese Anwendungen eingesetzt werden. Mit Azure CSP-Abonnements haben Sie eine einfachere Alternative mit der Skalierung und Flexibilität von Azure.

Azure AD Domain Services unterstützt jetzt auch Azure CSP-Abonnements. Sie können Ihre Anwendung jetzt in einem Azure CSP-Abonnement bereitstellen, das an das Azure AD-Verzeichnis Ihres Kunden gebunden ist. Auf diese Weise können Ihre Mitarbeiter (Supportpersonal) die virtuellen Computer, auf denen Ihre Anwendung bereitgestellt wird, mit den Anmeldeinformationen Ihres Unternehmens verwalten, pflegen und warten. Darüber hinaus können Sie eine von Azure AD Domain Services verwaltete Domäne für das Azure AD-Verzeichnis Ihres Kunden bereitstellen. Ihre Anwendung ist mit der verwalteten Domäne Ihres Kunden verbunden. Daher arbeiten Funktionen innerhalb Ihrer Anwendung, die auf Kerberos/NTLM, LDAP oder die [System.DirectoryServices-API](/dotnet/api/system.directoryservices) angewiesen sind, nahtlos mit dem Verzeichnis Ihres Kunden zusammen. Ihre Endkunden profitieren in hohem Maße davon, dass Sie Ihre Anwendung als Dienst nutzen, ohne sich um die Verwaltung der Infrastruktur kümmern zu müssen, in der die Anwendung bereitgestellt wird.

Alle Abrechnungen für Azure-Ressourcen, die Sie in diesem Abonnement verbrauchen (einschließlich Azure AD Domain Services) werden Ihnen in Rechnung gestellt. Sie behalten die vollständige Kontrolle über die Beziehung zum Kunden, wenn es um Verkauf, Abrechnung, technischen Support usw. geht. Mit der Flexibilität der Azure CSP-Plattform kann ein kleines Team von Supportmitarbeitern viele solcher Kunden betreuen, die Instanzen Ihrer Anwendung bereitstellen.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>CSP-Bereitstellungsmodelle für Azure AD Domain Services
Es gibt zwei Möglichkeiten, wie Sie Azure AD Domain Services mit einem Azure CSP-Abonnement verwenden können. Entscheiden Sie sich basierend auf den Sicherheits- und Einfachheitserwägungen Ihrer Kunden für die richtige Lösung.

### <a name="direct-deployment-model"></a>Direktes Bereitstellungsmodell
In diesem Bereitstellungsmodell werden die Azure AD Domain Services innerhalb eines virtuellen Netzwerks aktiviert, das zum Azure CSP-Abonnement gehört. Die Administrator-Agents des CSP-Partners verfügen über die folgenden Berechtigungen:
* Globale Administratorberechtigungen im Azure AD-Verzeichnis des Kunden.
* Abonnementbesitzerberechtigungen für das Azure CSP-Abonnement.

![Direktes Bereitstellungsmodell](./media/csp/csp_direct_deployment_model.png)

In diesem Bereitstellungsmodell können die Administrator-Agents des CSP-Anbieters Identitäten für den Kunden verwalten. Diese Administrator-Agents haben die Möglichkeit, neue Benutzer und Gruppen bereitzustellen, Anwendungen im Azure AD-Verzeichnis des Kunden zu erstellen usw. Dieses Bereitstellungsmodell kann für kleinere Organisationen geeignet sein, die keinen dedizierten Identitätsadministrator haben oder es bevorzugen, dass der CSP-Partner Identitäten in ihrem Auftrag verwaltet.


### <a name="peered-deployment-model"></a>Peerbereitstellungsmodell
In diesem Bereitstellungsmodell werden die Azure AD Domain Services innerhalb eines virtuellen Netzwerks des Kunden aktiviert, d.h. es handelt sich um ein direktes Azure-Abonnement, das vom Kunden bezahlt wird. Der CSP-Partner kann dann Anwendungen innerhalb eines virtuellen Netzwerks bereitstellen, das zum CSP-Abonnement des Kunden gehört. Die virtuellen Netzwerke können dann mithilfe von Azure-Peering virtueller Netzwerke verbunden werden. Dadurch können die vom CSP-Partner im Azure CSP-Abonnement bereitgestellten Workloads/Anwendungen mit der verwalteten Domäne des Kunden eine Verbindung herstellen, die im direkten Azure-Abonnement des Kunden bereitgestellt wird.

![Peerbereitstellungsmodell](./media/csp/csp_peered_deployment_model.png)

Dieses Bereitstellungsmodell bietet eine Trennung der Berechtigungen und ermöglicht es den Helpdesk-Agents des CSP-Partners, das Azure-Abonnement zu verwalten und die darin enthaltenen Ressourcen bereitzustellen und zu verwalten. Die Helpdesk-Agents des CSP-Partners benötigen jedoch keine globalen Administratorberechtigungen für das Azure AD-Verzeichnis des Kunden. Die Identitätsadministratoren des Kunden können weiterhin Identitäten für ihre Organisation verwalten.

Dieses Bereitstellungsmodell eignet sich für Szenarien, in denen ein ISV (unabhängiger Softwarehersteller) eine gehostete Version seiner lokalen Anwendung bereitstellt, die ebenfalls eine Verbindung mit dem AD des Kunden herstellen muss.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Verwalten von durch Azure AD Domain Services verwalteten Domänen in CSP-Abonnements
Die folgenden wichtigen Überlegungen gelten für die Verwaltung einer verwalteten Domäne in einem Azure CSP-Abonnement:

* **CSP-Administrator-Agents können eine verwaltete Domäne mit ihren Anmeldeinformationen bereitstellen:** Azure AD Domain Services unterstützt Azure CSP-Abonnements. Daher können Benutzer, die zur Gruppe der Administrator-Agents eines CSP-Partners gehören, eine neue durch Azure AD Domain Services verwaltete Domäne bereitstellen.

* **CSPs können Skripte für die Erstellung von neuen verwalteten Domänen für ihre Kunden mithilfe von PowerShell bereitstellen:** Nähere Informationen finden Sie unter [Aktivieren von Azure AD Domain Services mithilfe von PowerShell](powershell-create-instance.md).

* **CSP-Administrator-Agents können keine laufenden Verwaltungsaufgaben auf der verwalteten Domäne mit ihren Anmeldeinformationen ausführen:** CSP-Administrator-Benutzer können keine routinemäßigen Verwaltungsaufgaben innerhalb der verwalteten Domäne mit ihren Anmeldeinformationen ausführen. Diese Benutzer sind für das Azure AD-Verzeichnis des Kunden extern, und ihre Anmeldeinformationen sind im Azure AD-Verzeichnis des Kunden nicht verfügbar. Daher besitzt Azure AD Domain Services keinen Zugriff auf die Kerberos- und NTLM-Kennworthashes für diese Benutzer. Infolgedessen können solche Benutzer nicht für von Azure AD Domain Services verwaltete Domänen authentifiziert werden.

  > [!WARNING]
  > **Sie müssen ein Benutzerkonto im Verzeichnis des Kunden erstellen, um laufende Verwaltungsaufgaben für die verwaltete Domäne auszuführen.**
  > Sie können sich nicht mit den Anmeldeinformationen eines CSP-Administratorbenutzers bei der verwalteten Domäne anmelden. Verwenden Sie die Anmeldeinformationen eines Benutzerkontos, das zum Azure AD-Verzeichnis des Kunden gehört. Sie benötigen diese Anmeldeinformationen für Aufgaben wie das Verbinden virtueller Computer mit der verwalteten Domäne, das Verwalten von DNS, das Verwalten von Gruppenrichtlinien usw.
  >

* **Das für die laufende Verwaltung erstellte Benutzerkonto muss der Gruppe „AAD DC Administrators“ hinzugefügt werden:** Die Gruppe „AAD DC Administrators“ verfügt über Berechtigungen, um bestimmte delegierte Verwaltungsaufgaben auf der verwalteten Domäne auszuführen. Zu diesen Aufgaben gehören die Konfiguration von DNS, das Erstellen von Organisationseinheiten, die Verwaltung von Gruppenrichtlinien usw. Damit ein CSP-Partner solche Aufgaben für eine verwaltete Domäne ausführen kann, muss ein Benutzerkonto im Azure AD-Verzeichnis des Kunden erstellt werden. Die Anmeldeinformationen für dieses Konto müssen für die Administrator-Agents des CSP-Partners freigegeben werden. Außerdem muss dieses Benutzerkonto der Gruppe „AAD DC-Administratoren“ hinzugefügt werden, damit Konfigurationsaufgaben für die verwaltete Domäne mit diesem Benutzerkonto ausgeführt werden können.


## <a name="next-steps"></a>Nächste Schritte
* [Registrieren Sie sich für das Azure CSP-Programm](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program), und profitieren Sie von Azure CSP.
* Prüfen Sie die Liste der [in Azure CSP verfügbaren Azure-Dienste](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Enable Azure AD Domain Services using PowerShell (Aktivieren von Azure AD Domain Services mit PowerShell)](powershell-create-instance.md)
* [Erste Schritte mit Azure AD Domain Services](create-instance.md)
