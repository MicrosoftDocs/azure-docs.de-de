---
title: Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure AD?
description: Einführung in die Verwendung von Azure AD für die automatisierte Bereitstellung, Bereitstellungsaufhebung und fortlaufende Aktualisierung von Benutzerkonten für verschiedene SaaS-Drittanbieteranwendungen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 11/25/2019
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 5b593add9c9d7be446798eecdd03b396c3fe513a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174729"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure AD?

In Azure Active Directory (Azure AD) bezieht sich der Ausdruck **App-Bereitstellung** auf die automatische Erstellung von Benutzeridentitäten und Rollen in den Cloudanwendungen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), auf die Benutzer Zugriff benötigen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern. Gängige Szenarien sind die Bereitstellung eines Azure AD-Benutzers in Anwendungen wie [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) und anderen.

![Diagramm mit einer Übersicht über die Bereitstellung](./media/user-provisioning/provisioning-overview.png)

Dieses Feature ermöglicht Ihnen Folgendes:

- **Automatisieren der Bereitstellung**: Automatisches Erstellen neuer Konten in den richtigen Systemen für neue Benutzer, wenn diese Ihr Team oder Ihre Organisation verstärken
- **Automatisieren der Aufhebung der Bereitstellung**: Automatisches Deaktivieren von Konten in den richtigen Systemen, wenn Benutzer das Team oder die Organisation verlassen
- **Synchronisieren von Daten zwischen Systemen**: Sicherstellen, dass die Identitäten in Ihren Apps und Systemen bei Änderungen im Verzeichnis oder in Ihrem Personalsystem auf den neuesten Stand gebracht werden
- **Bereitstellen von Gruppen**: Bereitstellen von Gruppen für Anwendungen, die diese unterstützen
- **Steuern des Zugriffs**: Überwachen und Überprüfen, wer Bereitstellungen für Ihre Anwendungen durchgeführt hat
- **Nahtloses Bereitstellen bei Brownfield-Szenarien**: Abgleichen von vorhandenen Identitäten zwischen Systemen und Ermöglichen einer einfachen Integration, auch wenn Benutzer im Zielsystem bereits vorhanden sind
- **Nutzen der umfassenden Anpassung**: Nutzen von anpassbaren Attributzuordnungen, die definieren, welche Benutzerdaten vom Quellsystem an das Zielsystem fließen sollen
- **Erhalten von Warnungen zu kritischen Ereignissen**: Senden von Warnungen zu kritischen Ereignissen durch den Bereitstellungsdienst und Ermöglichen der Log Analytics-Integration, damit Sie benutzerdefinierte Warnungen zur Erfüllung Ihrer geschäftlichen Anforderungen definieren können

## <a name="benefits-of-automatic-provisioning"></a>Vorteile der automatischen Bereitstellung

Da die Anzahl der in modernen Organisationen genutzten Anwendungen weiter zunimmt, müssen IT-Administratoren eine bedarfsabhängige Zugriffsverwaltung durchführen können. Mit Standards wie SAML (Security Assertions Markup Language) oder OIDC (Open ID Connect) können Administratoren schnell einmaliges Anmelden (Single Sign-On, SSO) einrichten, aber für den Zugriff müssen außerdem Benutzer für die App bereitgestellt werden. Für viele Administratoren ist die Bereitstellung mit der wöchentlichen manuellen Erstellung jedes einzelnen Benutzerkontos bzw. dem Hochladen von CSV-Dateien verbunden, aber diese Prozesse sind zeitaufwändig, teuer und fehleranfällig. Lösungen wie SAML JIT (Just in Time) wurden eingeführt, um die Bereitstellung zu automatisieren. Unternehmen benötigen aber auch eine Lösung zum Aufheben der Bereitstellung von Benutzern, wenn diese die Organisation verlassen oder aufgrund einer Rollenänderung keinen Zugriff auf bestimmte Apps mehr benötigen.

Hier sind einige häufige Gründe für die Nutzung der automatischen Bereitstellung aufgeführt:

- Steigern der Effizienz und Genauigkeit von Bereitstellungsprozessen.
- Einsparen von Kosten im Zusammenhang mit dem Hosten und Verwalten von benutzerdefiniert entwickelten Bereitstellungslösungen und Skripts.
- Schützen Ihrer Organisation, indem Benutzeridentitäten sofort aus wichtigen SaaS-Apps entfernt werden, wenn Benutzer die Organisation verlassen.
- Einfaches Importieren einer großen Anzahl von Benutzern in eine bestimmte SaaS-Anwendung oder in ein bestimmtes System.
- Vorteil eines einzelnen Richtliniensatzes für die Ermittlung, wer bereitgestellt wird und wer sich bei einer App anmelden kann.

Die Azure AD-Benutzerbereitstellung kann zur Bewältigung dieser Aufgaben beitragen. Weitere Informationen zur Verwendung der Azure AD-Benutzerbereitstellung durch Kunden finden Sie in der [Kundengeschichte von ASOS](https://aka.ms/asoscasestudy). Das folgende Video enthält eine Übersicht über die Benutzerbereitstellung in Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Welche Anwendungen und Systeme kann ich mit der automatischen Benutzerbereitstellung von Azure AD verwenden?

Azure AD umfasst eine vorab integrierte Unterstützung für viele beliebte SaaS-Apps und Personalsysteme und bietet allgemeine Unterstützung für Apps, die bestimmte Teile des [SCIM 2.0-Standards](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010) implementieren.

* **Vorab integrierte Anwendungen (Katalog mit SaaS-Apps)** : Sie finden alle Anwendungen, für die Azure AD einen vorab integrierten Bereitstellungsconnector unterstützt, in der [Liste mit den Anwendungstutorials zur Benutzerbereitstellung](../saas-apps/tutorial-list.md). Für die vorab integrierten Anwendungen, die im Katalog aufgeführt sind, werden normalerweise SCIM 2.0-basierte Benutzerverwaltungs-APIs für die Bereitstellung verwendet. 

   ![Salesforce-Logo](./media/user-provisioning/gallery-app-logos.png)

   Wenn Sie eine neue Anwendung für die Bereitstellung anfordern möchten, können Sie [anfordern, dass Ihre Anwendung in unseren App-Katalog integriert wird](../develop/v2-howto-app-gallery-listing.md). Für die Anforderung der Benutzerbereitstellung muss die Anwendung über einen SCIM-konformen Endpunkt verfügen. Bitten Sie den Anwendungsanbieter darum, den SCIM-Standard einzuhalten, damit wir das Onboarding der App für unsere Plattform schnell durchführen können.

* **Anwendungen mit SCIM 2.0-Unterstützung**: Informationen zum generischen Verbinden von Anwendungen, die SCIM 2.0-basierte Benutzerverwaltungs-APIs implementieren, finden Sie unter [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Was ist das System für domänenübergreifendes Identitätsmanagement (SCIM)?

Als Hilfe bei der Automatisierung der Bereitstellung und Bereitstellungsaufhebung werden von Apps proprietäre Benutzer- und Gruppen-APIs verfügbar gemacht. Wenn Sie schon einmal Benutzer in mehr als einer App verwaltet haben, wissen Sie aber, dass in jeder App versucht wird, die gleichen einfachen Aktionen durchzuführen (z. B. Erstellen oder Aktualisieren von Benutzern, Hinzufügen von Benutzern zu Gruppen oder Aufheben der Bereitstellung von Benutzern). Die Implementierung all dieser einfachen Aktionen unterscheidet sich aber jeweils nur leicht, indem andere Endpunktpfade, unterschiedliche Methoden zum Angeben von Benutzerinformationen und ein anderes Schema zum Darstellen der einzelnen Informationselemente genutzt werden.

Zur Bewältigung dieser Aufgaben verfügt die SCIM-Spezifikation über ein gemeinsames Benutzerschema, damit Benutzer Apps zugeordnet werden können und diese verwenden können. SCIM wird immer mehr zum De-facto-Standard für die Bereitstellung. Bei Verwendung zusammen mit Verbundstandards wie SAML oder OpenID Connect verfügen Administratoren über eine auf Standards basierende End-to-End-Lösung für die Zugriffsverwaltung.

Eine ausführliche Anleitung zum Entwickeln eines SCIM-Endpunkts zum Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern und Gruppen für eine Anwendung finden Sie unter [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](use-scim-to-provision-users-and-groups.md). Für vorab integrierte Anwendungen im Katalog (Slack, Azure Databricks, Snowflake usw.) können Sie die Entwicklerdokumentation überspringen und die [hier](../saas-apps/tutorial-list.md) bereitgestellten Tutorials verwenden.

## <a name="manual-vs-automatic-provisioning"></a>Manuelle und automatische Bereitstellung

Die im Azure AD-Katalog enthaltenen Anwendungen unterstützen einen der zwei folgenden Bereitstellungsmodi:

* **Manuell** bedeutet, dass noch kein automatischer Azure AD-Bereitstellungsconnector für die App vorhanden ist. Benutzerkonten müssen manuell erstellt werden, beispielsweise indem Benutzer direkt zum Verwaltungsportal der App hinzugefügt werden oder indem eine Kalkulationstabelle mit Benutzerkontodetails hochgeladen wird. Die verfügbaren Mechanismen können Sie über die von der App bereitgestellte Dokumentation ermitteln, oder wenden Sie sich an den Entwickler der App.

* **Automatisch** bedeutet, dass für diese Anwendung ein Azure AD-Bereitstellungsconnector entwickelt wurde. Sie sollten das zum Einrichten der Bereitstellung für die jeweilige Anwendung spezifische Setuptutorial befolgen. App-Tutorials finden Sie unter [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](../saas-apps/tutorial-list.md).

Im Azure AD-Katalog sind Anwendungen, die die automatische Bereitstellung unterstützen, mit einem **Bereitstellungssymbol** gekennzeichnet. Wechseln Sie zur neuen Katalogvorschauoberfläche, um diese Symbole anzuzeigen. (Wählen Sie auf dem Banner oben auf der Seite **Eine Anwendung hinzufügen** den Link **Klicken Sie hier, um den neuen und verbesserten App-Katalog auszuprobieren** aus.)

![Bereitstellungssymbol im Anwendungskatalog](./media/user-provisioning/browse-gallery.png)

Der von einer Anwendung unterstützte Bereitstellungsmodus wird auch auf der Registerkarte **Bereitstellung** angezeigt, nachdem Sie Ihren **Unternehmens-Apps** die Anwendung hinzugefügt haben.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Wie richte ich die automatische Bereitstellung für eine Anwendung ein?

Für vorab integrierte Anwendungen, die im Katalog aufgeführt sind, ist eine Schritt-für-Schritt-Anleitung zum Einrichten der automatischen Bereitstellung verfügbar. Weitere Informationen finden Sie in der [Liste mit den Tutorials für integrierte Katalog-Apps](../saas-apps/tutorial-list.md). Im folgenden Video wird veranschaulicht, wie Sie die automatische Benutzerbereitstellung für Salesforce einrichten.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Führen Sie für andere Anwendungen, die SCIM 2.0 unterstützen, die im Artikel [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](use-scim-to-provision-users-and-groups.md) aufgeführten Schritte aus.


## <a name="next-steps"></a>Nächste Schritte

- [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
- [Anpassen von Attributzuordnungen für die Benutzerbereitstellung](customize-application-attributes.md)
- [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)