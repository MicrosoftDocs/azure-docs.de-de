---
title: Erste Schritte bei der Integration von Apps in Azure AD | Microsoft-Dokumentation
description: In diesem Artikel sind die ersten Schritte für die Integration von lokalen Anwendungen und Cloudanwendungen in Azure Active Directory (AD)  aufgeführt.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 913b7975de4598bba7240647c1d9e8c81cd1cec9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659096"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Erste Schritte zur Integration von Anwendungen in Azure Active Directory

In diesem Thema wird der Prozess zur Integration von Anwendungen mit Azure Active Directory (AD) zusammengefasst. Jeder der folgenden Abschnitte enthält eine kurze Zusammenfassung eines ausführlicheren Themas, damit Sie feststellen können, welche Teile dieses Erste-Schritte-Leitfadens für Sie relevant sind.

Detaillierte Bereitstellungspläne finden Sie unter [Nächste Schritte](#next-steps).

## <a name="take-inventory"></a>Ermitteln des Bestands
Vor der Integration von Anwendungen in Azure AD sollten Sie unbedingt den aktuellen Stand ermitteln und festlegen, was Sie erreichen möchten.  Die folgenden Fragen helfen Ihnen bei Ihren Überlegungen zu Ihrem Azure AD-Projekt zur Anwendungsintegration.

### <a name="application-inventory"></a>Anwendungsbestand
* Wo befinden sich all Ihre Anwendungen? Wer ist der Eigentümer?
* Welche Art der Authentifizierung ist für Ihre Anwendungen erforderlich?
* Wer benötigt Zugriff auf welche Anwendungen?
* Möchten Sie eine neue Anwendung bereitstellen?
  * Wird diese innerbetrieblich erstellt und auf einer Azure Compute-Instanz bereitgestellt?
  * Möchten Sie eine Anwendung verwenden, die im Azure-Anwendungskatalog verfügbar ist?

### <a name="user-and-group-inventory"></a>Benutzer- und Gruppenbestand
* Wo befinden sich Ihre Benutzerkonten?
  * Lokales Active Directory
  * Azure AD
  * In einer separaten Anwendungsdatenbank, die Sie besitzen
  * In ungenehmigten Anwendungen
  * Alle oben genannten Möglichkeiten
* Welche Berechtigungen und Rollenzuweisungen gelten derzeit für die einzelnen Benutzer? Möchten Sie den Zugriff überprüfen, oder sind Sie sicher, dass die aktuellen Zugriffs- und Rollenzuweisungen angemessen sind?
* Sind in Ihrem lokalen Active Directory bereits Gruppen eingerichtet?
  * Wie sind Ihre Gruppen organisiert?
  * Wer sind die Mitglieder der Gruppen?
  * Welche Berechtigungen/Rollenzuweisungen gelten derzeit für die Gruppen?
* Müssen Sie vor der Integration Benutzer-/Gruppendatenbanken bereinigen?  (Dies ist eine ziemlich wichtige Frage. Bei einer fehlerhaften Ausgangslage lässt sich kein optimales Ergebnis erzielen – „Garbage In, Garbage Out“.)

### <a name="access-management-inventory"></a>Bestand der Zugriffsverwaltung
* Wie verwalten Sie derzeit den Benutzerzugriff auf Anwendungen? Muss daran etwas geändert werden?  Haben Sie andere Methoden zum Verwalten des Zugriffs erwogen, beispielsweise [Azure RBAC](../../role-based-access-control/role-assignments-portal.md)?
* Wer muss worauf zugreifen?

Vielleicht können Sie nicht alle Fragen im Voraus beantworten, aber das ist kein Problem.  Dieser Leitfaden hilft Ihnen, einige dieser Fragen zu beantworten und einige fundierte Entscheidungen zu treffen.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Suchen nach nicht sanktionierten Cloudanwendungen per Cloud Discovery

Wie oben erwähnt sind möglicherweise Anwendungen im Einsatz, die bis jetzt von Ihrer Organisation noch nicht verwaltet wurden.  Im Rahmen der Bestandsaufnahme können Sie nicht genehmigte Cloudanwendungen finden. Weitere Informationen finden Sie unter [Einrichten von Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrieren von Anwendungen in Azure Active Directory
In den folgenden Artikeln werden die verschiedenen Methoden zur Integration von Anwendungen in Azure AD erläutert und Anleitungen bereitgestellt.

* [Bestimmen des zu verwendenden Active Directory-Verzeichnisses](../fundamentals/active-directory-whatis.md)
* [Verwenden von Anwendungen im Azure-Anwendungskatalog](what-is-single-sign-on.md)
* [Liste der Tutorials zur Integration von SaaS-Anwendungen](../saas-apps/tutorial-list.md)

### <a name="authentication-types"></a>Authentifizierungstypen
Für jede Ihrer Anwendungen gelten möglicherweise unterschiedliche Authentifizierungsanforderungen. Bei Azure AD können Signaturzertifikate mit Anwendungen verwendet werden, die die Protokolle SAML 2.0, WS-Verbund oder OpenID Connect sowie das einmalige Anmelden per Kennwort verwenden. Weitere Informationen zu Anwendungsauthentifizierungstypen, die mit Azure AD verwendet werden können, finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) und unter [Kennwortbasierte einmalige Anmeldung](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivieren von SSO mit Azure AD-App-Proxy
Mit dem Microsoft Azure AD-Anwendungsproxy können Sie sicheren Zugriff von jedem Ort und mit jedem Gerät auf Anwendungen bereitstellen, die sich in Ihrem privaten Netzwerk befinden. Nachdem Sie einen Anwendungsproxy-Connector in Ihrer Umgebung installiert haben, können Sie ihn mit Azure AD leicht konfigurieren.

### <a name="integrating-custom-applications"></a>Integrieren benutzerdefinierter Anwendungen
Wenn Sie eine neue Anwendung schreiben und Entwickler beim Nutzen der Leistungsfähigkeit von Azure AD unterstützen möchten, finden Sie unter [Leitfaden für Entwickler](./developer-guidance-for-integrating-applications.md) weitere Informationen.

Wenn Sie Ihre benutzerdefinierte Anwendung dem Azure-Anwendungskatalog hinzufügen möchten, finden Sie weitere Informationen unter [“Bring your own app” with Azure AD Self-Service SAML configuration](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)(„Bring your own app“ mit Azure AD-Konfiguration für Self-Service SAML).

## <a name="managing-access-to-applications"></a>Verwalten des Zugriffs auf Anwendungen
In den folgenden Artikeln werden Möglichkeiten zur Verwaltung des Zugriffs auf Anwendungen beschrieben, nachdem sie mithilfe von Azure AD-Connectors und Azure AD in Azure AD integriert wurden.

* [Verwalten des Zugriffs auf Apps mit Azure AD](what-is-access-management.md)
* [Automatisieren mit Azure AD-Connectors](../app-provisioning/user-provisioning.md)
* [Zuweisen von Benutzern zu einer Anwendung](./assign-user-or-group-access-portal.md)
* [Zuweisen von Gruppen zu einer Anwendung](./assign-user-or-group-access-portal.md)
* [Gemeinsames Verwenden von Konten](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Nächste Schritte
Für ausführliche Informationen können Sie Azure Active Directory-Bereitstellungspläne von [GitHub](../fundamentals/active-directory-deployment-plans.md) herunterladen. Für Kataloganwendungen können Sie Bereitstellungspläne für einmaliges Anmelden, den bedingten Zugriff und die Benutzerbereitstellung über das [Azure-Portal](https://portal.azure.com) herunterladen. 

So laden Sie einen Bereitstellungsplan im Azure-Portal herunter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Unternehmensanwendungen** | **Eine App auswählen** | **Bereitstellungsplan** aus.

Feedback zu den Bereitstellungsplänen können Sie mithilfe der [Umfrage zu Bereitstellungsplänen](https://aka.ms/DeploymentPlanFeedback) geben.