---
title: Was ist die Anwendungsverwaltung in Azure Active Directory?
description: Eine Übersicht über die Verwendung von Azure Active Directory (AD) als Identitäts- und Zugriffsverwaltungssystem (Identity and Access Management, IAM) für Ihre cloudbasierten und lokalen Anwendungen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: 247e824997fd95434246e49c78bf167f36e146c0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258590"
---
# <a name="what-is-application-management"></a>Worum handelt es sich bei der Anwendungsverwaltung?

Azure AD ist ein Identitäts- und Zugriffsverwaltungsystem (Identity and Access Management, IAM). Es stellt einen zentralen Ort zum Speichern von Informationen zu digitalen Identitäten bereit. Sie können Ihre Softwareanwendungen so konfigurieren, dass sie Azure AD als Speicherort für die Benutzerinformationen verwenden. 

Azure AD muss für die Integration mit einer Anwendung konfiguriert werden. Anders ausgedrückt: Azure AD muss wissen, von welchen Apps es für Identitäten verwendet wird. Dass Azure AD stets über diese Apps informiert ist und weiß, wie sie zu behandeln sind, wird als Anwendungsverwaltung bezeichnet.

Sie verwalten Anwendungen im Azure Active Directory-Portal unter „Verwalten“ auf der Seite **Unternehmensanwendungen**.

![Option „Unternehmensanwendungen“ im Azure AD-Portal im Abschnitt „Verwalten“.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Was ist ein Identitäts- und Zugriffsverwaltungsystem (IAM-System)?
Bei einer Anwendung handelt es sich um eine Software, die für bestimmte Zwecke verwendet wird. Die meisten Apps setzen voraus, dass sich Benutzer anmelden.

Ein zentrales Identitätssystem stellt einen zentralen Ort zum Speichern von Benutzerinformationen bereit, die dann von allen Anwendungen verwendet werden können. Diese Systeme werden als IAM-Systeme (Identitäts- und Zugriffsverwaltungssysteme) bezeichnet. Azure Active Directory ist das IAM-System für die Microsoft-Cloud.

>[!TIP]
>Ein IAM-System stellt einen zentralen Ort zum Nachverfolgen der Benutzeridentitäten bereit. Azure AD ist das IAM-System für die Microsoft-Cloud.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Gründe für die Verwaltung von Anwendungen mit einer Cloudlösung

Organisationen besitzen häufig Hunderte von Anwendungen, die für die Arbeit ihrer Benutzer unerlässlich sind. Der Benutzerzugriff auf diese Anwendungen erfolgt über eine Vielzahl von Geräten und Standorten. Neue Anwendungen werden ständig hinzugefügt, entwickelt und außer Betrieb genommen. Bei so vielen Apps und Zugriffspunkten ist es wichtig, eine universell funktionierende Identitätslösung zu verwenden.

>[!TIP]
>Der App-Katalog von Azure AD enthält viele beliebte Anwendungen, die bereits für die Verwendung mit Azure AD als Identitätsanbieter vorkonfiguriert sind.

## <a name="how-does-azure-ad-work-with-apps"></a>Wie funktioniert Azure AD mit Apps?

Azure AD befindet sich in der Mitte und bietet Identitätsverwaltung für cloudbasierte und lokale Apps. 

![Diagramm: Apps im Verbund über Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Reduzieren Sie Verwaltungskosten, indem Sie die [Benutzerbereitstellung automatisieren](../app-provisioning/user-provisioning.md), damit Benutzer automatisch zu Azure AD hinzugefügt werden, wenn Sie sie dem Personalsystem Ihres Unternehmens hinzufügen. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Welche Typen von Anwendungen kann ich in Azure AD integrieren?

Sie können Azure AD als Identitätssystem für nahezu jede App verwenden. Zahlreiche Apps sind bereits vorkonfiguriert und können mit minimalem Aufwand eingerichtet werden. Diese vorkonfigurierten Apps werden im [Azure AD-App-Katalog](/azure/active-directory/saas-apps/) veröffentlicht. 

Sie können die meisten Apps manuell für einmaliges Anmelden konfigurieren, wenn sie sich nicht bereits im Katalog befinden. Azure AD bietet mehrere Optionen für einmaliges Anmelden. Zu den beliebtesten zählen SAML-basiertes SSO und OIDC-basiertes SSO. Weitere Informationen zur Integration von Apps zum Aktivieren von SSO finden Sie unter [Optionen für einmaliges Anmelden in Azure AD](sso-options.md). 

Verwendet Ihre Organisation lokale Apps? Sie können sie mithilfe des App-Proxys integrieren. Weitere Informationen finden Sie unter [Remotezugriff auf lokale Anwendungen über den Azure AD-Anwendungsproxy](application-proxy.md).

>[!TIP]
>Wenn Sie eigene branchenspezifische Apps erstellen, können Sie sie in Azure AD integrieren, um einmaliges Anmelden zu unterstützen. Weitere Informationen zum Entwickeln von Apps für Azure AD finden Sie unter [Was ist die Microsoft Identity Platform?](..//develop/v2-overview.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Risikomanagement durch Richtlinien für bedingten Zugriff

In Kombination mit einmaligem Anmelden (Single Sign-On, SSO) mit Azure AD bietet [bedingter Zugriff](../conditional-access/concept-conditional-access-cloud-apps.md) ein hohes Maß an Sicherheit für den Zugriff auf Anwendungen. Richtlinien für bedingten Zugriff ermöglichen eine differenzierte Steuerung der Apps basierend auf den von Ihnen festgelegten Bedingungen. 

## <a name="improve-productivity-with-single-sign-on"></a>Produktivitätssteigerung durch einmaliges Anmelden

Einmaliges Anmelden (Single Sign-On, SSO) bietet eine einheitliche Benutzererfahrung zwischen Microsoft 365 und allen anderen von Ihnen verwendeten Apps. Sie müssen Ihren Benutzernamen und Ihr Kennwort nicht mehr eingeben.

Weitere Informationen zum einmaligen Anmelden finden Sie unter [Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Beheben von Governance- und Complianceproblemen

Überwachen Sie Apps mithilfe von Berichten, die SIEM-Tools (Security Information & Event Monitoring) nutzen. Sie können über das Portal oder über APIs auf die Berichte zugreifen. Lassen Sie programmgesteuert überwachen, wer auf Ihre Anwendungen zugreifen darf, und entfernen Sie den Zugriff für inaktive Benutzer durch Zugriffsüberprüfungen.

## <a name="manage-costs"></a>Verwalten von Kosten

Durch eine Migration zu Azure AD können Sie Ihre Kosten senken und sich den Aufwand der Verwaltung Ihrer lokalen Infrastruktur ersparen. Azure AD bietet außerdem Self-Service-Zugriff auf Anwendungen, was sowohl Administratoren als auch Benutzern Zeit erspart. Einmaliges Anmelden macht anwendungsspezifische Kennwörter überflüssig. Diese Option für die einmalige Anmeldung spart Kosten für das Zurücksetzen von Kennwörtern für Anwendungen und vermeidet Produktivitätsverluste durch das Abrufen von Kennwörtern.

Bei Anwendungen mit Schwerpunkt Personalwesen oder anderen Anwendungen mit einer großen Zahl von Benutzern können Sie die App-Bereitstellung nutzen, um die Arbeit zu erleichtern. Bei der App-Bereitstellung werden das Hinzufügen und Entfernen von Benutzern automatisiert. Weitere Informationen finden Sie unter [Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure AD?](../app-provisioning/user-provisioning.md).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
- [Erste Schritte mit der Anwendungsintegration](plan-an-application-integration.md)
- [Automatisieren der Benutzerbereitstellung](../app-provisioning/user-provisioning.md)