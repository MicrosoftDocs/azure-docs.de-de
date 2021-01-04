---
title: In vier Schritten zu einem starken Identitätsfundament – Azure AD
description: In diesem Thema werden vier Schritte beschrieben, die Hybrididentitätskunden durchführen können, um eine sichere Identitätsgrundlage zu erstellen.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 795f5ede382e561ee810e54e1f8897c5d806e8b8
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412373"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>In vier Schritten zu einem sicheren Identitätsfundament mit Azure Active Directory

Das Verwalten des Zugriffs auf Apps und Daten darf sich bei der Netzwerksicherheit nicht mehr auf herkömmliche Begrenzungsstrategien wie Umkreisnetzwerke und Firewalls verlassen, da Apps immer schneller in die Cloud verschoben werden. Nun sollten Organisationen ihrer Identitätslösung vertrauen, um zu steuern, wer und was den Zugriff auf Apps und Daten der Organisation erhält. Immer mehr Organisationen erlauben Mitarbeitern das Mitbringen eigener Geräte zur Arbeit und die Verwendung ihrer Geräte von jedem beliebigen Standort mit Verbindung mit dem Internet aus. Mittlerweile ist das Sicherstellen, dass diese Geräte konform und sicher sind, ein wichtiger Aspekt der Identitätslösung geworden, die eine Organisation implementiert. Bei heuten digitalen Arbeitsplätzen ist die [Identität die primäre Steuerungsebene](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) jeder Organisation, die auf die Cloud umsteigt.

Durch den Einsatz einer Hybrididentitätslösung in Azure Active Directory (Azure AD) erhalten Organisationen Zugriff auf Premiumfunktionen, die durch Automatisierung, Delegierung, Self-Service- und SSO-Funktionen (einmaliges Anmelden) für eine höhere Produktivität sorgen. Damit ermöglichen Sie Ihren Mitarbeiter den Zugriff auf Unternehmensressourcen von jedem Ort aus, an dem sie arbeiten, und Ihrem IT-Team die Steuerung des Zugriffs, sodass nur die richtigen Personen Zugriff auf die richtigen Ressourcen erhalten und damit für eine sichere Produktionsumgebung gesorgt ist.

Diese Checkliste basiert auf unseren Erkenntnissen. Sie enthält bewährte Methoden, mit denen Sie die empfohlenen Aktionen schnell umsetzen und eine *sichere* Identitätsgrundlage in Ihrer Organisation schaffen können:

* Einfaches Verbinden mit Apps
* Automatisches Einrichten einer Identität für jeden Benutzer
* Sicheres Unterstützen Ihrer Benutzer
* Operationalisieren Ihrer Erkenntnisse

## <a name="step-1---connect-to-apps-easily"></a>Schritt 1: Einfaches Verbinden mit Apps

Durch das Verbinden Ihrer Apps mit Azure AD können Sie die Produktivität der Endbenutzer und die Sicherheit verbessern, indem Sie mit Azure AD einmaliges Anmelden (SSO) aktivieren und die Benutzerbereitstellung ausführen. Durch die Verwaltung Ihrer Apps an einem zentralen Ort können Sie mit Azure AD den Mehraufwand bei der Verwaltung minimieren. Gleichzeitig verfügen Sie so über einen zentralen Ort für die Steuerung von Sicherheits- und Compliancerichtlinien.

In diesem Abschnitt werden Ihre Optionen für das Verwalten des Benutzerzugriffs auf Apps und das Aktivieren von sicherem Remotezugriff auf interne Apps sowie die Vorteile einer Migration Ihrer Apps zu Azure AD erläutert.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Nahtloses Verfügbarmachen von Apps für Ihre Benutzer

Azure AD ermöglicht Administratoren das [Hinzufügen von Anwendungen](../manage-apps/add-application-portal.md) zum Katalog der Unternehmensanwendungen im [Azure-Portal](https://portal.azure.com/). Durch das Hinzufügen von Anwendungen zum Katalog der Unternehmensanwendungen wird das Konfigurieren von Anwendungen für die Verwendung von Azure AD als Identitätsanbieter vereinfacht. Darüber hinaus können Sie den Benutzerzugriff auf die Anwendung mit Richtlinien für bedingten Zugriff verwalten und einmaliges Anmelden (SSO) für Anwendungen konfigurieren, sodass Benutzer nicht ständig ihr Kennwort eingeben müssen, sondern stattdessen automatisch angemeldet werden – und zwar sowohl bei lokalen als auch bei cloudbasierten Anwendungen.

Nachdem Anwendungen dem Azure AD-Katalog hinzugefügt wurden, können Benutzer die ihnen zugewiesenen Apps anzeigen und suchen. Sie können ggf. auch weitere Apps anfordern. Azure AD bietet [mehrere Methoden](../manage-apps/end-user-experiences.md) für den Zugriff auf Apps durch Benutzer:

* Zugriffsbereich/Meine Apps
* Microsoft 365-App-Launcher
* Direkte Anmeldung bei Verbund-Apps
* Links für die direkte Anmeldung

Weitere Informationen zum Benutzerzugriff auf Apps finden Sie unter **Schritt 3: Sicheres Unterstützen Ihrer Benutzer** in diesem Artikel.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrieren von Apps aus Active Directory-Verbunddienste (AD FS) zu Azure AD

Durch das Migrieren der Konfiguration des einmaligen Anmeldens von Active Directory-Verbunddienste (AD FS) zu Azure AD erhalten Sie zusätzliche Funktionen für die Sicherheit, eine noch konsistentere Verwaltung und Möglichkeiten der Zusammenarbeit. Für optimale Ergebnisse wird empfohlen, dass Sie Ihre Apps von AD FS zu Azure AD migrieren. Das Überführen von Anwendungsauthentifizierung und -autorisierung zu Azure AD bietet die folgenden Vorteile:

* Kostenmanagement
* Risikomanagement
* Mehr Produktivität
* Erfüllung von Compliance und Governance

Weitere Informationen finden Sie im Whitepaper [Migrating Your Applications to Azure Active Directory](https://aka.ms/migrateapps/whitepaper) (Migrieren Ihrer Anwendungen zu Azure Active Directory).

### <a name="enable-secure-remote-access-to-apps"></a>Ermöglichen des sicheren Remotezugriffs auf Apps

Der [Azure AD-Anwendungsproxy](../manage-apps/what-is-application-proxy.md) bietet eine einfache Lösung, mit der Organisationen lokale Apps für Remotebenutzer, die Zugriff auf interne Apps benötigen, auf sichere Weise in der Cloud veröffentlichen können. Nach dem einmaligen Anmelden in Azure AD können Benutzer über eine externe URL oder ein internes Anwendungsportal auf Cloudanwendungen und lokale Anwendungen zugreifen.

Der Azure AD-Anwendungsproxy bietet die folgenden Vorteile:

* Erweitern von Azure AD um lokale Ressourcen
  * Sicherheit und Schutz auf Cloud-Ebene
  * Einfach zu aktivierende Features wie bedingter Zugriff und mehrstufige Authentifizierung
* Keine Komponenten im Umkreisnetzwerk wie z. B. VPN- und herkömmliche Reverseproxylösungen
* Keine Notwendigkeit eingehender Verbindungen
* Einmaliges Anmelden (SSO) für Geräte, Ressourcen und Apps in der Cloud und lokal
* Ermöglichen von zeit- und ortsunabhängigem produktivem Arbeiten für Endbenutzer

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Ermitteln von Schatten-IT mit Microsoft Cloud App Security

In modernen Unternehmen sind die IT-Abteilungen häufig nicht über alle Cloudanwendungen unterrichtet, die die Benutzer für ihre Arbeit verwenden. Wenn IT-Administratoren gefragt werden, wie viele Cloud-Apps ihrer Meinung nach von ihren Mitarbeitern verwendet werden, sagen sie im Durchschnitt: 30 bis 40. Tatsächlich liegt der Durchschnitt bei über 1.000 separaten Apps, die von Mitarbeitern in Ihrer Organisation genutzt werden. 80 % der Mitarbeiter verwenden nicht sanktionierte Apps, die von niemandem überprüft wurden und die möglicherweise nicht Ihren Sicherheits- und Compliancerichtlinien entsprechen.

[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) kann Ihnen helfen, nützliche Apps zu identifizieren, die bei den Benutzern beliebt sind. Diese können dann von der IT sanktioniert und dem Katalog der Unternehmensanwendungen hinzugefügt werden, damit die Benutzer von Funktionen wie SSO und bedingtem Zugriff profitieren.

<em>„Mit **Cloud App Security** können wir sicherstellen, dass unsere Mitarbeiter unsere Cloud- und SaaS-Anwendungen ordnungsgemäß nutzen. Die verfügbaren Möglichkeiten unterstützen unsere grundlegenden Sicherheitsrichtlinien zum Schutz von Accenture.“</em> --- [John Blasi, Managing Director, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Zusätzlich zum Erkennen von Schatten-IT können Sie mit MCAS auch die Risikostufe von Apps ermitteln, nicht autorisierte Zugriffe auf Unternehmensdaten verhindern, mögliche Datenlecks erkennen und andere Sicherheitsrisiken in Anwendungen aufdecken.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Schritt 2: Automatisches Einrichten einer Identität für jeden Benutzer

Das Zusammenführen von lokalen und cloudbasierten Verzeichnissen in einer Azure AD-Hybrididentitätslösung ermöglicht Ihnen, Ihre vorhandenen lokalen Active Directory-Ergebnisse wiederzuverwenden, indem Sie Ihre bestehenden Identitäten in der Cloud bereitstellen. Die Lösung synchronisiert die lokalen Identitäten mit Azure AD. Gleichzeitig führt das IT-Team das lokale Active Directory mit vorhandenen Governancelösungen weiter und nutzt es als primäre Quelle für die Bestätigung von Identitäten. Die Azure AD-Hybrididentitätslösung von Microsoft deckt sowohl lokale als auch cloudbasierte Funktionen ab. Damit wird eine gemeinsame Benutzeridentität für die standortunabhängige Authentifizierung und Autorisierung gegenüber allen Ressourcen erstellt.

Die Integration Ihrer lokalen Verzeichnisse mit Azure AD steigert die Produktivität Ihrer Benutzer und verhindert, dass Benutzer mehrere Konten für Apps und Dienste nutzen, da für den Zugriff auf die Cloud und lokale Ressourcen nur eine Identität benötigt wird. Die Verwendung mehrerer Konten stellt ein Problem für Endbenutzer und IT gleichermaßen dar. Aus Sicht der Endbenutzer bedeuten mehrere Konten auch, dass sie sich mehrere Kennwörter merken müssen. Um dies zu vermeiden, verwenden viele Benutzer dasselbe Kennwort für alle Konten – was vom Standpunkt der Sicherheit schlecht ist. Aus Sicht der IT führt die Wiederverwendung meist zu häufigeren Anforderungen für die Kennwortzurücksetzung und treibt damit die Kosten für den Helpdesk sowie die Anzahl der Beschwerden von Endbenutzern in die Höhe.

Azure AD Connect ist das Tool für das Synchronisieren Ihrer lokalen Identitäten mit Azure AD. Diese können dann für den Zugriff auf Cloudanwendungen verwendet werden. Sobald die Identitäten in Azure AD enthalten sind, können sie für die Bereitstellung von SaaS-Anwendungen wie Salesforce oder Concur verwendet werden.

In diesem Abschnitt werden einige Empfehlungen für die Bereitstellung von Hochverfügbarkeit, für eine moderne Authentifizierung in der Cloud und für die Reduzierung des lokalen Aufwands aufgeführt.

> [!NOTE]
> Wenn Sie mehr über Azure AD Connect erfahren möchten, lesen Sie unter [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](./how-to-connect-sync-whatis.md) nach.

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Einrichten und Aktualisieren eines Stagingservers für Azure AD Connect

Azure AD Connect spielt eine wichtige Rolle bei der Bereitstellung. Wenn der Synchronisierungsserver ausfällt, werden lokale Änderungen nicht in der Cloud aktualisiert und verursachen Probleme für die Benutzer. Es ist wichtig, eine Failoverstrategie festzulegen, die es Administratoren ermöglicht, die Synchronisierung schnell fortzusetzen, nachdem der Synchronisierungsserver offline geschaltet wurde.

Für Hochverfügbarkeit wird bei einem Ausfall des primären Azure AD Connect-Servers empfohlen, einen separaten [Stagingserver](./how-to-connect-sync-staging-server.md) für Azure AD Connect bereitzustellen. Durch das Bereitstellen eines Servers kann der Administrator den Stagingserver durch einen einfachen Konfigurationsschalter in die Produktion „heraufstufen“. Außerdem können Sie mit einem konfigurierten Standbyserver im Stagingmodus auch neue Konfigurationsänderungen testen und bereitstellen und einen neuen Server einführen, wenn der alte außer Betrieb genommen wird.

> [!TIP]
> Azure AD Connect wird regelmäßig aktualisiert. Aus diesem Grund wird dringend empfohlen, einen Stagingserver auf dem neuesten Stand zu halten, um von Leistungsverbesserungen, Fehlerbehebungen und neuen Funktionen zu profitieren, die jede neue Version bietet.

### <a name="enable-cloud-authentication"></a>Aktivieren der Cloudauthentifizierung

Organisationen mit lokalem Active Directory sollten ihr Verzeichnis mit Azure AD Connect auf Azure AD erweitern und die passende Authentifizierungsmethode konfigurieren. Das [Auswählen einer geeigneten Authentifizierungsmethode](./choose-ad-authn.md) für Ihre Organisation ist der erste Schritt Ihrer Journey zum Verschieben von Apps in die Cloud. Sie stellt eine wichtige Komponente dar, da sie den Zugriff auf alle Clouddaten und -ressourcen steuert.

Die einfachste und empfohlene Methode zum Aktivieren von Cloudauthentifizierung für lokale Verzeichnisobjekte in Azure AD stellt das Aktivieren der [Kennworthashsynchronisierung](./how-to-connect-password-hash-synchronization.md) (Password Hash Synchronization, PHS) dar. Einige Organisationen können auch das Aktivieren der [Pass-Through-Authentifizierung](./how-to-connect-pta-quick-start.md) (PTA) in Betracht ziehen.

Sie sollten jedoch sowohl bei PHS als auch bei PTA nicht vergessen, das [nahtlose einmalige Anmelden](./how-to-connect-sso.md) zu aktivieren, damit Benutzer auf Cloud-Apps zugreifen können, ohne ständig ihren Benutzernamen und ihr Kennwort in der App eingeben zu müssen, wenn sie Geräte mit Windows 7 oder 8 in Ihrem Unternehmensnetzwerk verwenden. Ohne einmaliges Anmelden müssen sich die Benutzer anwendungsspezifische Kennwörter merken und sich für jede Anwendung anmelden. Ebenso müssen IT-Mitarbeiter Benutzerkonten für jede Anwendung (z. B. Microsoft 365, Box und Salesforce) erstellen und aktualisieren. Benutzer müssen sich ihre Kennwörter merken und außerdem Zeit für die Anmeldung bei jeder Anwendung aufbringen. Ein standardisiertes Verfahren für das einmalige Anmelden im gesamten Unternehmen ist nicht nur für eine optimale Benutzererfahrung wichtig, sondern auch für das Verringern von Risiken, das Erstellen von Berichten und die Governance.

Falls Organisationen bereits AD FS oder einen anderen lokalen Authentifizierungsanbieter nutzen, können sie durch einen Umstieg auf Azure AD als Identitätsanbieter die Komplexität verringern und die Verfügbarkeit steigern. Sofern nicht spezifische Anwendungsfälle die Verwendung eines Verbunds erforderlich machen, wird das Migrieren von der Verbundauthentifizierung zu PHS und nahtlosem einmaligem Anmelden oder PTA und nahtlosem einmaligem Anmelden empfohlen. Sie profitieren damit von einem geringeren lokalen Aufwand und mehr Flexibilität, die die Cloud durch verbesserte Benutzerfunktionen bietet. Weitere Informationen finden Sie unter [Migrieren vom Verbund zur Kennworthashsynchronisierung für Azure Active Directory](./plan-migrate-adfs-password-hash-sync.md).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Aktivieren der automatischen Bereitstellung von Konten

Das Aktivieren der automatischen Bereitstellung und Bereitstellungsaufhebung in Ihren Anwendungen ist die beste Strategie für die Verwaltung des Lebenszyklus von Identitäten über mehrere Systeme hinweg. Azure AD unterstützt die [automatisierte, richtlinienbasierte Bereitstellung und Bereitstellungsaufhebung](../app-provisioning/configure-automatic-user-provisioning-portal.md) von Benutzerkonten für eine Vielzahl beliebter SaaS-Anwendungen wie z. B. ServiceNow und Salesforce sowie anderen, die das [SCIM 2.0-Protokoll](../app-provisioning/use-scim-to-provision-users-and-groups.md) implementieren. Im Gegensatz zu herkömmlichen Bereitstellungslösungen, die benutzerdefinierten Code oder das manuelle Hochladen von CSV-Dateien erfordern, wird der Bereitstellungsdienst in der Cloud gehostet. Er bietet vorab integrierte Connectors, die über das Azure-Portal eingerichtet und verwaltet werden können. Ein wichtiger Vorteil der automatischen Aufhebung von Bereitstellungen ist, dass Ihre Organisation besser geschützt wird, da Benutzeridentitäten sofort aus wichtigen SaaS-Apps entfernt werden können, wenn die zugehörigen Benutzer die Organisation verlassen.

Weitere Informationen zur automatisierten Benutzerbereitstellung sowie zu deren Funktionsweise finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="step-3---empower-your-users-securely"></a>Schritt 3: Sicheres Unterstützen Ihrer Benutzer

An heutigen digitalen Arbeitsplätzen ist es besonders wichtig, Sicherheit und Produktivität in Einklang zu bringen. Benutzer lehnen jedoch häufig Sicherheitsmaßnahmen ab, die ihre Produktivität und ihren Zugriff auf Cloud-Apps verlangsamen. Um dieses Problem zu umgehen, bietet Azure AD Self-Service-Funktionen, mit denen Benutzer produktiv bleiben können, während gleichzeitig der Verwaltungsaufwand minimiert wird.

In diesem Abschnitt werden Empfehlungen für das Beheben der Konflikte zwischen der Unterstützung der Benutzer und der Sicherheit der ganzen Organisation aufgeführt.

### <a name="enable-self-service-password-reset-for-all-users"></a>Aktivieren der Self-Service-Kennwortzurücksetzung für alle Benutzer

Die [Self-Service-Kennwortzurücksetzung](../authentication/tutorial-enable-sspr.md) (Self-Service Password Reset, SSPR) von Azure bietet für IT-Administratoren eine einfache Methode, Benutzern das Zurücksetzen und Entsperren ihrer Kennwörter oder Konten zu ermöglichen, ohne dass ein Administrator eingreifen muss. Das System verfügt über eine ausführliche Berichterstellung, bei der der Benutzerzugriff auf das System nachverfolgt wird, sowie über eine Benachrichtigungsfunktion, mit der Sie über eine fehlerhafte oder missbräuchliche Nutzung informiert werden.

Standardmäßig werden bei einer Kennwortzurücksetzung Konten von Azure AD entsperrt. Wenn Sie jedoch die [lokale Integration](../authentication/concept-sspr-howitworks.md#on-premises-integration) von Azure AD Connect aktivieren, haben Sie auch die Möglichkeit, diese beiden Vorgänge zu trennen. So können Benutzer ihr Konto entsperren, ohne ihr Kennwort zurücksetzen zu müssen.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Sicherstellen, dass alle Benutzer für MFA und SSPR registriert sind

Azure bietet Berichte, die Sie und Ihre Organisation verwenden können, um sicherzustellen, dass alle Benutzer für MFA und SSPR registriert sind. Benutzer, die nicht registriert wurden, müssen möglicherweise über den Vorgang informiert werden.

Der [Bericht zu MFA-Anmeldungen](../authentication/howto-mfa-reporting.md) enthält Informationen zur MFA-Nutzung, und er bietet Ihnen Einblicke in die Funktionsweise von MFA in Ihrem Unternehmen. Der Zugriff auf Anmeldeaktivitäten (sowie Überwachungen und Risikoerkennungen) für Azure AD ist für die Problembehandlung, Nutzungsanalysen und forensische Untersuchungen von entscheidender Bedeutung.

Ebenso kann der [Bericht zur Self-Service-Kennwortverwaltung](../authentication/howto-sspr-reporting.md) verwendet werden, um zu bestimmen, wer für SSPR registriert ist (und wer noch nicht).

### <a name="self-service-app-management"></a>Self-Service-App-Verwaltung

Damit Ihre Benutzer über ihren Zugriffsbereich Anwendungen selbst ermitteln können, müssen Sie den [Self-Service-Anwendungszugriff](../manage-apps/access-panel-manage-self-service-access.md) auf alle Anwendungen aktivieren, die Benutzer selbst ermitteln und für die sie den Zugriff anfordern können sollen. Der Self-Service-Anwendungszugriff bietet die Möglichkeit, dass Benutzer Anwendungen selbst ermitteln können und die entsprechende Geschäftseinheit den Zugriff auf diese Anwendungen optional genehmigen kann. Sie können der Geschäftseinheit ermöglichen, die Anmeldeinformationen zu verwalten, die Benutzern zugewiesen wurden, damit diese über ihren Zugriffsbereich direkt auf [Anwendungen mit einmaligem Anmelden](../manage-apps/troubleshoot-password-based-sso.md#automatically-capture-sign-in-fields-for-an-app) per Kennwort zugreifen können.

### <a name="self-service-group-management"></a>Self-Service-Gruppenverwaltung

Das Zuweisen von Benutzern zu Anwendungen erfolgt am besten mithilfe von Gruppen, da sie mehr Flexibilität sowie die Möglichkeit zu einer bedarfsorientierten Verwaltung bieten:

* Attributbasiert mithilfe dynamischer Gruppenmitgliedschaften
* Delegierung an App-Besitzer

Azure AD bietet die Möglichkeit, den Zugriff auf Ressourcen mithilfe von Sicherheitsgruppen und erstellen und Microsoft 365-Gruppen zu verwalten. Diese Gruppen können von einem Gruppenbesitzer verwaltet werden, der Mitgliedschaftsanforderungen genehmigen oder ablehnen und die Steuerung der Gruppenmitgliedschaft delegieren kann. Diese als [Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md) bezeichnete Funktion spart Zeit, da sie Gruppenbesitzern, denen keine Administratorrolle zugewiesen ist, ermöglicht, Gruppen zu erstellen und zu verwalten, ohne dass Administratoren ihre Anforderungen verarbeiten müssen.

## <a name="step-4---operationalize-your-insights"></a>Schritt 4: Operationalisieren Ihrer Erkenntnisse

Überwachung und Protokollierung von sicherheitsbezogenen Ereignissen und den zugehörigen Warnungen sind grundlegende Komponenten einer effizienten Strategie, um sicherzustellen, dass Benutzer produktiv bleiben und Ihre Organisation sicher ist. Sicherheitsprotokolle und -berichte können u. a. folgende Fragen beantworten:

* Nutzen Sie, wofür Sie bezahlen?
* Werden verdächtige oder böswillige Aktivitäten unter meinem Mandanten durchgeführt?
* Wer war von einem Sicherheitsvorfall betroffen?

Sicherheitsprotokolle und Berichte bieten eine elektronische Aufzeichnung verdächtiger Aktivitäten und helfen Ihnen bei der Erkennung von Mustern, die auf versuchte oder erfolgreiche externe Eindringversuche in das Netzwerk und auf interne Angriffe hinweisen können. Sie können im Rahmen der Überwachung entsprechende Benutzeraktivitäten überwachen, die Einhaltung gesetzlicher Bestimmungen dokumentieren, forensische Analysen durchführen und vieles mehr. Warnungen benachrichtigen Sie bei Sicherheitsereignissen.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Zuweisen der Administratorrollen mit den geringsten Berechtigungen für Vorgänge

Bei den Überlegungen zur Vorgehensweise beim Betrieb sind verschiedene Administrationsebenen zu berücksichtigen. Die erste Ebene umfasst den gesamten Verwaltungsaufwand Ihrer globalen Administratoren. Bei kleineren Unternehmen kann es sinnvoll sein, immer die globale Administratorrolle zu verwenden. In größeren Organisationen mit Helpdeskpersonal und Administratoren, die für unterschiedliche Aufgaben verantwortlich sind, kann das Zuweisen der Rolle eines globalen Administrators ein Sicherheitsrisiko darstellen, da es den entsprechenden Personen die Möglichkeit zur Verwaltung von Aufgaben bietet, die über ihren eigentlichen Verantwortungsbereich hinaus gehen.

Sie sollten in diesem Fall eine weitere Ebene der Verwaltung in Betracht ziehen. Mithilfe von Azure AD können Sie Benutzer als „Administratoren mit eingeschränkten Rechten“ festlegen, die Aufgaben in Rollen mit weniger Rechten verwalten können. Sie könnten beispielsweise Ihrem Helpdeskpersonal die Rolle [Benutzer mit Leseberechtigung für Sicherheitsfunktionen](../roles/permissions-reference.md#security-reader) zuweisen, um ihnen die Möglichkeit zum Verwalten von sicherheitsbezogenen Funktionen mit schreibgeschütztem Zugriff zu gewähren. Möglicherweise ist es auch sinnvoll, Personen die Rolle [Authentifizierungsadministrator](../roles/permissions-reference.md#authentication-administrator) zuzuweisen, um ihnen die Möglichkeit zum Zurücksetzen von Anmeldeinformationen (ohne Kennwörter) oder zum Lesen und Konfigurieren von Azure Service Health zu gewähren.

Weitere Informationen finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Überwachen von Hybridkomponenten (Azure AD Connect-Synchronisierung, AD FS) mit Azure AD Connect Health

Azure AD Connect und AD FS sind wichtige Komponenten, die zu schwerwiegenden Fehlern bei der Lebenszyklusverwaltung und Authentifizierung und damit letztlich zu Ausfällen führen können. Aus diesem Grund sollten Sie Azure AD Connect Health bereitstellen, um diese Komponenten überwachen und zugehörige Berichte erstellen zu können.

Weitere Informationen finden Sie unter [Überwachen von AD FS mithilfe von Azure AD Connect Health](./how-to-connect-health-adfs.md).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Verwenden von Azure Monitor zum Erfassen von Datenprotokollen für die Analyse

[Azure Monitor](../../azure-monitor/overview.md) ist ein zentrales Überwachungsportal für alle Azure AD-Protokolle, das umfassende Erkenntnisse, erweiterte Analysen und maschinelles Lernen bietet. Mit Azure Monitor können Sie Metriken und Protokolle im Portal und über APIs nutzen, um weiterführende Einblicke in den Zustand und die Leistung Ihrer Ressourcen zu erhalten. Der Dienst bietet eine zentrale Benutzeroberfläche im Portal und aktiviert gleichzeitig eine Vielzahl von Produktintegrationen über APIs und Datenexportoptionen, über die auch traditionelle SIEM-Systeme von Drittanbietern unterstützt werden. Darüber hinaus bietet Ihnen Azure Monitor die Möglichkeit, Warnungsregeln zu konfigurieren, um bei Problemen mit Ihren Ressourcen benachrichtigt zu werden oder automatisierte Aktionen auszuführen.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Erstellen benutzerdefinierter Dashboards für Ihre Führungsebene und Ihre tägliche Arbeit

Organisationen ohne eine SIEM-Lösung können das [Power BI-Inhaltspaket](../reports-monitoring/howto-use-azure-monitor-workbooks.md) für Azure AD herunterladen. Das Power BI-Inhaltspaket enthält vorkonfigurierte Berichte, mit denen Sie erkennen können, wie Ihre Benutzer Azure AD-Funktionen einführen und verwenden. Damit erhalten Sie Einblicke in alle Aktivitäten in Ihrem Verzeichnis. Sie können auch ein eigenes [benutzerdefiniertes Dashboard](/power-bi/service-dashboards) erstellen und für Ihre Vorgesetzten freigeben, um diese über Ihre Routineaufgaben zu informieren. Dashboards stellen eine hervorragende Möglichkeit dar, Ihr Geschäft zu überwachen und Ihre wichtigsten Metriken im Blick zu behalten. Als Grundlage für die Visualisierungen auf einem Dashboard können einzelne oder mehrere Datasets oder einzelne oder mehrere Berichte genutzt werden. Auf einem Dashboard werden lokale Daten und Clouddaten zusammengefasst, sodass Sie eine konsolidierte Ansicht erhalten, die unabhängig vom Speicherort der Daten ist.

![Benutzerdefiniertes Power BI-Dashboard](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Verstehen der Gründe für Supportanfragen

Sie sollten beim Implementieren einer Hybrididentitätslösung, wie der in diesem Artikel beschriebenen, letztendlich einen Rückgang bei Ihren Supportanfragen feststellen. Häufig auftretende Probleme wie z. B. vergessene Kennwörter und Kontosperrungen werden durch die Implementierung der Self-Service-Kennwortzurücksetzung von Azure reduziert. Gleichzeitig erlaubt die Aktivierung des Self-Service-Anwendungszugriffs Benutzern die selbstständige Ermittlung und Anforderung des Zugriffs auf Anwendungen – ohne auf die IT-Abteilung angewiesen zu sein.

Falls Sie keinen Rückgang der Supportanfragen feststellen, sollten Sie die Ursachen für diese Anfragen analysieren, um zu überprüfen, ob SSPR und der Self-Service-Anwendungszugriff ordnungsgemäß konfiguriert wurden oder ob andere neue Probleme auftreten, die systematisch behandelt werden können.

*„Wir benötigten bei unserer Digitalisierung einen zuverlässigen Identitäts- und Zugriffsverwaltungsanbieter, um eine nahtlose, aber gleichzeitig sichere Integration zwischen uns, unseren Partnern und Cloud-Dienstanbietern in einem effizienten Ökosystem umzusetzen. Azure AD war die beste Option, da es die nötigen Funktionen und die Einblicke bot, die es uns ermöglicht haben, Risiken zu erkennen und darauf zu reagieren.“* --- [Yazan Almasri, Global Information Security Director, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Überwachen der Nutzung von Apps für Erkenntnisse

Neben der Ermittlung von Schatten-IT kann die Überwachung der App-Nutzung in Ihrer Organisation mit [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) auch helfen, sämtliche Vorteile eines Umstiegs Ihrer Organisation von lokalen auf Cloudanwendungen zu nutzen. Sie behalten durch verbesserten Einblick in Aktivitäten volle Kontrolle über Ihre Ressourcen und können den Schutz wichtiger Daten in Cloudanwendungen erhöhen. Das Überwachen der App-Nutzung in Ihrer Organisation mit MCAS hilft Ihnen bei der Beantwortung der folgenden Fragen:

* Welche nicht sanktionierten Apps nutzen die Mitarbeiter zum Speichern von Daten?
* Wo und wann werden vertrauliche Daten in der Cloud gespeichert?
* Wer greift auf vertrauliche Daten in der Cloud zu?

*„Mit Cloud App Security können wir sehr schnell Anomalien erkennen und Maßnahmen ergreifen.“* --- [Eric LePenske, Senior Manager, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Zusammenfassung

Bei der Implementierung einer Hybrididentitätslösung sind viele Aspekte zu berücksichtigen. Diese Checkliste mit ihren vier Schritten hilft Ihnen aber dabei, schnell eine Identitätsinfrastruktur umzusetzen, mit der Benutzer noch produktiver und sicherer arbeiten können.

* Einfaches Verbinden mit Apps
* Automatisches Einrichten einer Identität für jeden Benutzer
* Sicheres Unterstützen Ihrer Benutzer
* Operationalisieren Ihrer Erkenntnisse

Wir hoffen, dass dieses Dokument eine nützliche Roadmap für die Einrichtung einer sicheren Identitätsbasis für Ihre Organisation darstellt.

## <a name="identity-checklist"></a>Identitätscheckliste

Wir empfehlen Ihnen, die folgende Checkliste zur Referenz auszudrucken, damit Sie sie bei Ihrer Journey zu einer stabilen Identitätsgrundlage für Ihre Organisation nutzen können.

### <a name="today"></a>Heute

|Fertig?|Element|
|:-|:-|
||Pilotversuch für Self-Service-Kennwortzurücksetzung (SSPR, Self-Service Password Reset) für eine Gruppe|
||Überwachen von Hybridkomponenten mithilfe von Azure AD Connect Health|
||Zuweisen der Administratorrollen mit den geringsten Berechtigungen für den Betrieb|
||Ermitteln von Schatten-IT mit Microsoft Cloud App Security|
||Verwenden von Azure Monitor zum Erfassen von Datenprotokollen für die Analyse|

### <a name="next-two-weeks"></a>Nächste zwei Wochen

|Fertig?|Element|
|:-|:-|
||Verfügbarmachen einer App für Ihre Benutzer|
||Pilotversuch für die Azure AD-Bereitstellung für eine beliebige SaaS-App|
||Einrichten und Aktualisieren eines Stagingservers für Azure AD Connect|
||Starten der Migration von Apps von AD FS zu Azure AD|
||Erstellen benutzerdefinierter Dashboards für Ihre Führungsebene und Ihre tägliche Arbeit|

### <a name="next-month"></a>Nächster Monat

|Fertig?|Element|
|:-|:-|
||Überwachen der Nutzung von Apps für Erkenntnisse|
||Pilotversuch für sicheren Remotezugriff auf Apps|
||Sicherstellen, dass alle Benutzer für MFA und SSPR registriert sind|
||Aktivieren der Cloudauthentifizierung|

### <a name="next-three-months"></a>Nächste drei Monate

|Fertig?|Element|
|:-|:-|
||Aktivieren der Self-Service-App-Verwaltung|
||Aktivieren der Self-Service-Gruppenverwaltung|
||Überwachen der Nutzung von Apps für Erkenntnisse|
||Verstehen der Gründe für Supportanfragen|

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihren Sicherheitsstatus erhöhen können, indem Sie die Funktionen von Azure Active Directory und diese Fünf-Schritte-Checkliste verwenden: [Fünf Schritte zum Sichern Ihrer Identitätsinfrastruktur](../../security/fundamentals/steps-secure-identity.md).

Erfahren Sie, wie Identitätsfunktionen in Azure AD helfen können, den Übergang zur cloudgesteuerten Verwaltung zu beschleunigen, indem sie die Lösungen und Funktionen bereitstellen, die es Organisationen erlauben, sich schnell anzupassen und zunehmend mehr von ihrer Identitätsverwaltung aus konventionellen, lokalen Systemen nach Azure AD zu verschieben: [So stellt Azure AD die über die Cloud gesteuerte Verwaltung für lokale Workloads bereit](./cloud-governed-management-for-on-premises.md).