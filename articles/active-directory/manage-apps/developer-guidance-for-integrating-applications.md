---
title: Registrieren von Anwendungen für die Verwendung von Azure Active Directory | Microsoft-Dokumentation
description: Dieser Artikel wendet sich an IT-Fachpersonal und erläutert Richtlinien zur Integration von Azure-Anwendungen in Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd51bfd377aed2a7edd8c64d2f3dc99c3388e45a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824670"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Entwickeln von Branchen-Apps für Azure Active Directory
Dieser Leitfaden bietet eine Übersicht über die Entwicklung von Branchenanwendungen (Line of Business, LoB) für Azure Active Directory (AD) und richtet sich primär an globale Administratoren von Active Directory/Office 365.

## <a name="overview"></a>Übersicht
Das Erstellen von Anwendungen mit Azure AD ermöglicht Benutzern der betreffenden Organisation das einmalige Anmelden mit Office 365. Die Integration der Anwendung in Azure AD bietet Ihnen Kontrolle über die für die Anwendung festgelegte Authentifizierungsrichtlinie. Weitere Informationen zum bedingten Zugriff und zum Schutz von Apps mit Multi-Factor Authentication (MFA) finden Sie unter [Konfigurieren von Zugriffsregeln](../conditional-access/app-based-mfa.md).

Registrieren Sie Ihre Anwendung für die Verwendung von Azure Active Directory. Wenn Sie die Anwendung registrieren, können Ihre Entwickler Azure AD verwenden, um Benutzer zu authentifizieren und Zugriff auf Benutzerressourcen wie E-Mails, Kalender und Dokumente anzufordern.

Jedes Mitglied des Verzeichnisses (nicht Gäste) kann eine Anwendung registrieren, was auch als *Erstellung eines Anwendungsobjekts*bezeichnet wird.

Durch das Registrieren einer Anwendung können alle Benutzer folgende Aktionen ausführen:

* Eine Identität für ihre Anwendung erhalten, die von Azure AD erkannt wird
* Ein oder mehrere Kennwörter/Schlüssel erhalten, mit denen die Anwendung sich gegenüber AD authentifizieren kann
* Die Anwendung im Azure-Portal mit einem benutzerdefinierten Namen, Logo usw. kennzeichnen
* Die Azure AD-Autorisierungsfunktionen auf ihre Anwendung anwenden, einschließlich:

  * Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)
  * Azure Active Directory als oAuth-Autorisierungsserver (Absichern einer von der Anwendung offengelegten API)
* Legen Sie die erforderlichen Berechtigungen für das ordnungsgemäße Funktionieren der Anwendung fest, einschließlich:

     - App-Berechtigungen (nur für globale Administratoren). Beispiel:  Rollenmitgliedschaft in einer anderen Azure AD-Anwendung oder Rollenmitgliedschaft in Bezug auf eine Azure-Ressource, eine Azure-Ressourcengruppe oder ein Azure-Abonnement
     - Delegierte Berechtigungen (jeder Benutzer) Beispiel:  Azure AD, Anmelden und Profil lesen

> [!NOTE]
> Standardmäßig kann jedes Mitglied eine Anwendung registrieren. Informationen zum Einschränken von Berechtigungen für die Registrierung von Anwendungen für bestimmte Mitglieder finden Sie unter [Wie werden Anwendungen zu Azure AD hinzugefügt?](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)
>
>

Folgende Schritte sind für globale Administratoren erforderlich, um Entwickler dabei zu unterstützen, ihre Anwendungen zur Produktionsreife zu bringen:

* Konfigurieren von Zugriffsregeln (Access Policy/MFA)
* Konfigurieren der Anwendung für das Erfordern von Benutzerrechten und Zuweisen von Benutzern
* Standardmäßiges Unterdrücken der Oberfläche für die Benutzerzustimmung

## <a name="configure-access-rules"></a>Konfigurieren von Zugriffsregeln
Konfigurieren Sie anwendungsspezifische Zugriffsregeln für Ihre SaaS-Apps. Sie können z.B. MFA anfordern oder den Zugriff ausschließlich für Benutzer in vertrauenswürdigen Netzwerken gewähren. Weitere Informationen dazu finden Sie im Dokument [Konfigurieren von Zugriffsregeln](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurieren der Anwendung für das Erfordern von Benutzerrechten und Zuweisen von Benutzern
Standardmäßig können Benutzer auf Anwendungen zugreifen, ohne diesen zugewiesen zu sein. Falls die Anwendung jedoch Rollen verfügbar macht oder Sie möchten, dass die Anwendung im Zugriffsbereich eines Benutzers angezeigt wird, sollten Sie Benutzerzuweisungen erforderlich machen.

Abonnenten von Azure AD Premium oder Enterprise Mobility Suite (EMS) empfehlen wir ausdrücklich die Nutzung von Gruppen. Durch das Zuweisen von Gruppen zu der Anwendung kann die laufende Zugriffsverwaltung an den Besitzer der Gruppe delegiert werden. Sie können eine solche Gruppe selbst erstellen oder die zuständige Person in Ihrer Organisation bitten, die Gruppe mithilfe des vorhandenen Tools für die Gruppenverwaltung zu erstellen.

[Zuweisen von Benutzern und Gruppen zu einer Anwendung](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Unterdrücken der Benutzerzustimmung
Standardmäßig muss jeder Benutzer einen Zustimmungsprozess durchlaufen, um sich bei einer Anwendung anmelden zu können. Während dieses Prozesses muss der Benutzer einer Anwendung Berechtigungen gewähren – dies kann für Benutzer verwirrend sein, die mit solchen Entscheidungen nicht vertraut sind.

Für Anwendungen, die Sie als vertrauenswürdig einstufen, können Sie die Benutzerfreundlichkeit verbessern, indem Sie der Anwendung im Auftrag Ihres Unternehmens die Zustimmung erteilen.

Weitere Informationen über die Benutzerzustimmung und den Zustimmungsprozess in Azure finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Verwandte Artikel
* [Ermöglichen des sicheren Remotezugriffs auf lokale Anwendungen mit dem Azure AD-Anwendungsproxy](application-proxy.md)
* [Verwalten des Zugriffs auf Apps mit Azure AD](what-is-access-management.md)

