---
title: Sichere Zugriffsmethoden für Administratoren in Azure AD | Microsoft-Dokumentation
description: Stellen Sie sicher, dass Administratorzugriff und Administratorkonten Ihrer Organisation sicher sind. Für Systemarchitekten und IT-Profis, die Azure AD, Azure und Microsoft Online Services konfigurieren.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7304ffbb7718205ead48e27989d4a169375997
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861985"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD

Die Sicherheit der Geschäftsressourcen hängt von der Integrität der privilegierten Konten ab, mit denen Ihre IT-Systeme verwaltet werden. Cyberkriminelle haben es beim Diebstahl von Anmeldeinformationen besonders auf Administratorkonten und andere privilegierte Zugriffsmöglichkeiten abgesehen, um Zugriff auf sensible Daten zu erhalten.

Bei Clouddiensten sind Cloud-Dienstanbieter und Kunde gemeinsam verantwortlich für Prävention und Reaktion. Weitere Informationen zu den neuesten Bedrohungen von Endpunkten und Cloud finden Sie unter [Informieren Sie sich über die aktuellen Angriffe auf Endpunkte und auf die Cloud ](https://www.microsoft.com/security/operations/security-intelligence-report). Dieser Artikel kann Ihnen helfen, eine Roadmap zum Schließen der Lücken zwischen Ihren aktuellen Plänen und dem hier beschriebenen Leitfaden zu entwickeln.

> [!NOTE]
> Microsoft sieht sich in der Pflicht, den Ansprüchen von Vertrauen, Transparenz, Einhaltung von Standards und Einhaltung behördlicher Bestimmungen in höchstem Maße gerecht zu werden. Mehr darüber, wie das globale Incidentreaktionsteam von Microsoft die Auswirkungen von Angriffen auf Clouddienste abschwächt, und wie die Sicherheit in Businessprodukte und Clouddienste von Microsoft integriert ist, erfahren Sie unter [Sicherheit](https://www.microsoft.com/trustcenter/security), und mehr über Complianceziele von Microsoft unter [Compliance](https://www.microsoft.com/trustcenter/compliance).

Bisher haben sich Unternehmen bezüglich der Organisationssicherheit auf die Ein- und Ausstiegspunkte eines Netzwerks als Sicherheitsbereich konzentriert. Mit SaaS-Apps und persönlichen Geräten im Internet hat dieser Ansatz jedoch an Wirksamkeit verloren. In Azure AD wird der Netzwerksicherheitsbereich durch eine Authentifizierung in der Identitätsschicht Ihrer Organisation ersetzt. Für Steuerung und Kontrolle sind Benutzer zuständig, die privilegierten Administratorrollen zugewiesen sind. Deren Zugriff muss geschützt werden, und zwar unabhängig davon, ob es sich um eine lokale, hybride oder Cloudumgebung handelt.

Das Sichern des privilegierten Zugriffs erfordert Änderungen an folgenden Elementen:

* Prozessen, Verwaltungsmethoden und Wissensmanagement
* technischen Komponenten wie z.B. Maßnahmen zu Hostverteidigung, Kontenschutz und Identitätsverwaltung

Sichern Sie Ihren privilegierten Zugriff auf eine Art und Weise, die in den von Ihnen bevorzugten Microsoft-Diensten verwaltet und gemeldet wird. Wenn Sie über lokale Administratorkonten verfügen, beachten Sie unter [Schützen des privilegierten Zugriffs](/windows-server/identity/securing-privileged-access/securing-privileged-access) den Leitfaden für den lokalen und hybriden privilegierten Zugriff in Active Directory.

> [!NOTE]
> Der Leitfaden in diesem Artikel bezieht sich in erster Linie auf Features von Azure Active Directory, die in den Azure Active Directory Premium-Plänen P1 und P2 enthalten sind. Azure Active Directory Premium P2 ist in der EMS E5-Suite und Microsoft 365 E5-Suite enthalten. In diesem Leitfaden wird davon ausgegangen, dass Ihre Organisation bereits Azure AD Premium P2-Lizenzen für Ihre Benutzer erworben hat. Wenn Sie nicht über diese Lizenzen verfügen, gilt möglicherweise ein Teil dieses Leitfadens nicht für Ihre Organisation. In diesem Artikel bedeutet der Begriff „Globaler Administrator“ (oder „Globaler Admin“) das Gleiche wie „Unternehmensadministrator“ oder „Mandantenadministrator“.

## <a name="develop-a-roadmap"></a>Entwickeln einer Roadmap

Microsoft empfiehlt Ihnen, eine Roadmap zum Schützen des privilegierten Zugriffs gegenüber Cyberangreifern zu entwickeln und zu befolgen. Sie können Ihre Roadmap jederzeit den bestehenden Funktionen und spezifischen Anforderungen in Ihrer Organisation anpassen. Jede Phase der Roadmap sollte den Aufwand und Probleme erhöhen, die Angreifern beim Angriff auf den privilegierten Zugriff auf Ihre lokalen, Cloud- und Hybridressourcen entstehen. Microsoft empfiehlt die folgenden vier Roadmap-Phasen. Planen Sie zuerst die effektivsten und schnellsten Implementierungen. Dieser Artikel, der auf der Erfahrung von Microsoft mit Cyberangriffen und der Implementierung entsprechender Reaktionen basiert, kann dabei als Leitfaden dienen. Die Zeitpläne für diese Roadmap sind ungefähre Werte.

![Phasen der Roadmap mit Zeitplänen](./media/security-planning/roadmap-timeline.png)

* Phase 1 (24 bis 48 Stunden): Wichtige Dinge, die umgehend erledigt werden sollten

* Phase 2 (2 bis 4 Wochen): Gegenmaßnahmen für die gängigsten Angriffsstrategien

* Phase 3 (1 bis 3 Monate): Schaffung von Transparenz und uneingeschränkter Kontrolle über Administratoraktivitäten

* Phase 4 (sechs Monate und später): Fortsetzung des Aufbaus von Verteidigungsmaßnahmen zur weiteren Härtung Ihrer Sicherheitsplattform

Dieses Roadmapframework folgt dem Konzept, die Verwendung von Microsoft-Technologien zu maximieren, die Sie möglicherweise bereits bereitgestellt haben. Erwägen Sie, alle Sicherheitstools von anderen Anbietern einzubinden, die Sie schon bereitgestellt haben oder bereitstellen möchten.

## <a name="stage-1-critical-items-to-do-right-now"></a>Phase 1: Wichtige Dinge, die zuerst anstehen

![Phase 1: Wichtige als Erstes zu erledigende Dinge](./media/security-planning/stage-one.png)

Phase 1 der Roadmap konzentriert sich auf wichtige Aufgaben, die schnell und einfach zu implementieren sind. Sie sollten diese wenigen Elemente sofort innerhalb der ersten 24 bis 48 Stunden ausführen, um ein Basisniveau von geschütztem privilegiertem Zugriff sicherzustellen. Diese Phase der Roadmap für geschützten privilegierten Zugriff umfasst die folgenden Aktionen:

### <a name="general-preparation"></a>Allgemeine Vorbereitung

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Aktivieren von Azure AD Privileged Identity Management

Wir empfehlen, Azure AD Privileged Identity Management (PIM) in Ihrer Azure AD-Produktionsumgebung zu aktivieren. Nach der Aktivierung von PIM erhalten Sie bei Änderungen an privilegierten Zugriffsrollen eine Benachrichtigung per E-Mail. Benachrichtigungen stellen ein Frühwarnsystem dar, wenn zusätzliche Benutzer zu Rollen mit hohen Zugriffsrechten hinzugefügt werden.

Azure AD Privileged Identity Management ist in Azure AD Premium P2 oder EMS E5 enthalten. Um den Zugriff auf lokale und in der Cloud verfügbare Anwendungen und Ressourcen besser zu schützen, registrieren Sie sich für die [kostenlose 90-Tage-Testversion von Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Azure AD Privileged Identity Management und Azure AD Identity Protection überwachen mithilfe von Berichterstattung, Überwachung und Warnungen von Azure AD die Sicherheitsaktivitäten.

Gehen Sie nach dem Aktivieren von Azure AD Privileged Identity Management wie folgt vor:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) über ein Konto an, das als globaler Administrator Ihrer Azure AD-Produktionsorganisation konfiguriert ist.

2. Um die Azure AD-Organisation auszuwählen, in der Sie Privileged Identity Management verwenden möchten, wählen Sie im Azure-Portal in der oberen rechten Ecke Ihren Benutzernamen aus.

3. Wählen Sie im Menü des Azure-Portals **Alle Dienste** aus, und filtern Sie die Liste nach **Azure AD Privileged Identity Management**.

4. Öffnen Sie Privileged Identity Management in der Liste **Alle Dienste**, und heften Sie Privileged Identity Management an Ihr Dashboard.

Stellen Sie sicher, dass der Person in Ihrer Organisation, die PIM zum ersten Mal verwendet, automatisch die Rollen **Sicherheitsadministrator** und **Administrator für privilegierte Rollen** zugewiesen werden. Nur Administratoren für privilegierte Rollen können die Azure AD-Verzeichnisrollenzuweisungen von Benutzern verwalten. Der PIM-Sicherheitsassistent führt Sie durch die anfängliche Erkennungs- und Zuweisungsumgebung. Sie können den Assistenten zu diesem Zeitpunkt ohne weitere Änderungen beenden.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifizieren und Kategorisieren von Konten in stark privilegierten Rollen

Nachdem Sie Azure AD Privileged Identity Management aktiviert haben, können Sie Benutzer mit den folgenden Azure AD-Rollen anzeigen:

* Globaler Administrator
* Administrator für privilegierte Rollen
* Exchange-Administrator
* SharePoint-Administrator

Wenn in Ihrer Organisation Azure AD Privileged Identity Management nicht verwendet wird, können Sie die [PowerShell-API](/powershell/module/azuread/get-azureaddirectoryrolemember) nutzen. Beginnen Sie mit der Rolle „Globaler Administrator“, da ein globaler Administrator bei allen Clouddiensten, die Ihre Organisation abonniert hat, über gleiche Berechtigungen verfügt. Diese Berechtigungen werden unabhängig vom Ort der Zuweisung (Microsoft 365 Admin Center, Azure-Portal oder Azure AD-Modul für Microsoft PowerShell) erteilt.

Entfernen Sie alle Konten, die in diesen Rollen nicht mehr benötigt werden. Kategorisieren Sie anschließend die restlichen Konten, die Administratorrollen zugewiesen sind:

* Ist zwar Administratoren zugewiesen, wird aber auch für nicht administrative Zwecke (z. B. persönliche E-Mail) verwendet
* Ist Administratoren zugewiesen und wird nur für Verwaltungszwecke verwendet
* Für mehrere Benutzer freigegeben
* Für Notfallzugriffs-Szenarios
* Für automatisierte Skripts
* Für externe Benutzer

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definieren Sie mindestens zwei Notfallzugriffs-Konten

Es kann sein, dass ein Benutzer versehentlich aus seiner Rolle ausgesperrt wird. Wenn beispielsweise ein lokaler Verbundidentitätsanbieter nicht verfügbar ist, können sich Benutzer nicht anmelden oder ein vorhandenes Administratorkonto aktivieren. Sie können Vorbereitungen für eine unbeabsichtigte Situation ohne Zugriffsmöglichkeit treffen, indem Sie mindestens zwei Notfallzugriffskonten speichern.

Notfallzugriffskonten helfen Ihnen, den privilegierten Zugriff innerhalb einer Azure AD-Organisation einzuschränken. Diese Konten verfügen über hohe Zugriffsrechte und sind keinen bestimmten Einzelpersonen zugewiesen. Notfallzugriffskonten sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können. Stellen Sie sicher, dass Sie die Nutzung des Notfallkontos kontrollieren und auf die unbedingt erforderliche Zeit beschränken.

Bewerten Sie die Konten, die zugewiesen werden oder für die Rolle „globaler Administrator“ berechtigt sind. Wenn Ihnen über die Domäne „\*.onmicrosoft.com“ (für den Notfallzugriff) keine reinen Cloudkonten angezeigt werden, erstellen Sie diese. Weitere Informationen finden Sie unter [Verwalten von Administratorkonten für den Notfallzugriff in Azure AD](security-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Aktivieren Sie die mehrstufige Authentifizierung (MFA), und registrieren Sie alle anderen nicht verbundenen Einzelbenutzer-Administratorkonten mit hohen Privilegien.

Legen Sie Azure AD MFA bei der Anmeldung für alle Benutzer als erforderlich fest, die dauerhaft einzelnen oder mehreren der Azure AD-Administratorrollen zugewiesen sind: globaler Administrator, Administrator für privilegierte Rollen, Exchange-Administrator und SharePoint-Administrator. Nutzen Sie den Leitfaden zum Aktivieren [einer zweistufigen Überprüfung für einen Benutzer oder eine Gruppe](../authentication/howto-mfa-userstates.md), und stellen Sie sicher, dass alle Benutzer unter [https://aka.ms/mfasetup](https://aka.ms/mfasetup) registriert sind. Weitere Informationen finden Sie unter Schritt 2 und 3 des Handbuchs [Zugriffsschutz für Daten und Dienste in Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Phase 2: Gegenmaßnahmen für gängige Angriffsstrategien

![Phase 2: Gegenmaßnahmen für gängige Angriffsstrategien](./media/security-planning/stage-two.png)

Phase 2 der Roadmap konzentriert sich auf die Abwehr besonders häufig verwendeter Angriffstechniken zum Diebstahl und Missbrauch von Anmeldeinformationen und kann in etwa zwei bis vier Wochen implementiert werden. Diese Phase der Roadmap für geschützten privilegierten Zugriff umfasst die folgenden Aktionen.

### <a name="general-preparation"></a>Allgemeine Vorbereitung

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Durchführen einer Inventur der Dienste, Besitzer und Administratoren

Mit der Zunahme von BYOD- (Bring Your Own Device) und Homeoffice-Richtlinien sowie zunehmender WLAN-Konnektivität ist es wichtig zu überwachen, wer eine Verbindung mit Ihrem Netzwerk herstellt. Mit einer Sicherheitsüberprüfung können Geräte, Anwendungen und Programme in Ihrem Netzwerk angezeigt werden, die von Ihrer Organisation nicht unterstützt werden und ein hohes Risiko darstellen. Weitere Informationen finden Sie unter [Azure-Sicherheitsverwaltung und -Überwachung](../../security/fundamentals/management-monitoring-overview.md). Stellen Sie sicher, dass alle folgenden Aufgaben in Ihrem Inventurprozess enthalten sind.

* Identifizieren Sie die Benutzer mit Administratorrollen und die Dienste, über die sie verwalten können.
* Finden Sie mit Azure AD PIM heraus, welche Benutzer in Ihrer Organisation Administratorzugriff auf Azure AD haben.
* Neben den in Azure AD definierten Rollen enthält Microsoft 365 eine Reihe von Administratorrollen, die Sie Benutzern in Ihrer Organisation zuweisen können. Jede Administratorrolle ist allgemeinen Geschäftsfunktionen zugeordnet und stattet Benutzer in Ihrer Organisation mit Berechtigungen für bestimmte Aufgaben im [Microsoft 365 Admin Center](https://admin.microsoft.com) aus. Finden Sie im Microsoft 365 Admin Center heraus, welche Benutzer in Ihrer Organisation Administratorzugriff auf Microsoft 365 haben, einschließlich über Rollen, die nicht in Azure AD verwaltet werden. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) und [Bewährte Methoden für die Sicherheit von Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Machen Sie in Diensten, auf die sich Ihre Organisation verlässt (z. B. Azure, Intune oder Dynamics 365), eine Bestandsaufnahme.
* Stellen Sie sicher, dass für Ihre Konten, die zu Verwaltungszwecken verwendet werden, Folgendes gilt:

  * Den Konten sind geschäftliche E-Mail-Adressen zugeordnet
  * Die Konten sind für Azure AD Multi-Factor Authentication registriert oder verwenden lokale MFA
* Fragen Sie Benutzer nach der geschäftlichen Begründung für den Administratorzugriff.
* Entfernen Sie den Administratorzugriff für Personen und Dienste, die diesen nicht benötigen.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifizieren von Microsoft-Konten in Administratorrollen, die auf Arbeits- oder Schulkonten umgestellt werden müssen

Wenn Ihre anfänglichen globalen Administratoren die vorhandenen Anmeldeinformationen für ihr Microsoft-Konto, die sie bei der ersten Verwendung von Azure AD benutzt haben, wiederverwenden, ersetzen Sie die Microsoft-Konten durch einzelne cloudbasierte oder synchronisierte Konten.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Sicherstellen von separaten Benutzerkonten und E-Mail-Weiterleitung für globale Administratorkonten

Persönliche E-Mail-Konten werden regelmäßig von Cyberkriminellen ausgespäht und stellen ein Risiko dar. Daher sind persönliche E-Mail-Adressen für globale Administratorkonten nicht akzeptabel. Um Internetrisiken von Administratorrechten zu separieren, erstellen Sie dedizierte Konten für jeden Benutzer mit Administratorrechten.

* Stellen Sie sicher, dass Sie separate Konten für Benutzer erstellen, die globale Verwaltungsaufgaben ausführen sollen
* Stellen Sie sicher, dass Ihre globalen Administratoren nicht versehentlich mit ihren Administratorkonten E-Mails öffnen oder Programme ausführen
* Achten Sie darauf, dass die E-Mail-Nachrichten für diese Konten an ein Arbeitspostfach weitergeleitet werden

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Sicherstellen, dass die Kennwörter von Administratorkonten kürzlich geändert wurden

Stellen Sie sicher, dass alle Benutzer sich mindestens einmal in den letzten 90 Tagen bei ihren Administratorkonten angemeldet und ihre Kennwörter geändert haben. Vergewissern Sie sich außerdem, dass alle freigegebenen Konten kürzlich die Änderung der Kennwörter veranlasst haben.

#### <a name="turn-on-password-hash-synchronization"></a>Aktivieren der Kennworthashsynchronisierung

Azure AD Connect synchronisiert einen Hash eines Benutzerkennworthashs aus dem lokalen Active Directory mit einer cloudbasierten Azure AD-Organisation. Wenn Sie einen Verbund mit Active Directory Federation Services (AD FS) verwenden, können Sie die Kennworthashsynchronisierung als Sicherungsmaßnahme nutzen. Diese Sicherung kann nützlich sein, wenn Ihre lokalen Active Directory- oder AD FS-Server vorübergehend nicht verfügbar sind.

Die Kennworthashsynchronisierung ermöglicht Benutzern, sich bei einem Dienst mit dem gleichen Kennwort anzumelden, das sie zur Anmeldung bei Ihrer lokalen Active Directory-Instanz verwenden. Die Kennworthashsynchronisierung ermöglicht Azure AD Identity Protection die Erkennung kompromittierter Anmeldeinformationen, indem sie Kennworthashs mit Kennwörtern vergleicht, die bekanntermaßen kompromittiert sind. Weitere Informationen finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Anfordern der Multi-Factor Authentication für Benutzer mit privilegierten Rollen und hoher Gefährdung

Azure AD empfiehlt, Multi-Factor Authentication (MFA) für alle Benutzer anzufordern. Achten Sie darauf, die Benutzer zu berücksichtigen, bei denen die Kompromittierung ihres Kontos erhebliche Auswirkungen haben würde (z. B. Mitarbeiter der Finanzabteilung). MFA reduziert das Risiko eines Angriffs mithilfe eines kompromittierten Kennworts.

Aktivieren Sie:

* [Richtlinien für MFA mit bedingtem Zugriff](../authentication/howto-mfa-getstarted.md) für alle Benutzer in Ihrer Organisation.

Wenn Sie Windows Hello for Business verwenden, kann die MFA-Anforderung durch Verwendung der Windows Hello-Anmeldebenutzeroberfläche erfüllt werden. Weitere Informationen finden Sie unter [Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Konfigurieren von Identity Protection

Azure AD Identity Protection ist ein algorithmusbasiertes Überwachungs- und Berichterstattungstool zum Erkennen von potenziellen Sicherheitsrisiken, die Auswirkungen auf die Identitäten in Ihrer Organisation haben können. Sie können automatische Antworten auf diese erkannten verdächtigen Aktivitäten konfigurieren und entsprechende Maßnahmen zu deren Behebung ergreifen. Weitere Informationen finden Sie unter [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Abrufen Ihres Microsoft 365 Secure Score (bei Verwendung von Microsoft 365)

Secure Score prüft Ihre Einstellungen und Aktivitäten für Microsoft 365-Dienste und vergleicht diese mit einer von Microsoft aufgestellten Baseline. Das Ergebnis sagt aus, in welchem Maß Sie bewährte Sicherheitsmethoden befolgen. Jeder Benutzer, der über Administratorberechtigungen für ein Microsoft 365 Business Standard- oder Enterprise-Abonnement verfügt, kann auf [https://securescore.office.com](https://securescore.office.com/) auf Secure Score zugreifen.

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Überprüfen der Microsoft 365-Sicherheit und -Konformität (bei Verwendung von Microsoft 365)

Im [Plan für Sicherheit und Compliance](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) wird der Ansatz für die Konfiguration von Office 365 und die Aktivierung von anderen EMS-Funktionen für Office 365-Kunden behandelt. Lesen Sie dann die Schritte 3 bis 6 zum [Zugriffsschutz für Daten und Dienste in Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) und das Handbuch zum [Überwachen von Sicherheit und Compliance in Microsoft 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Konfigurieren der Microsoft 365-Aktivitätsüberwachung (bei Verwendung von Microsoft 365)

Überwachen Sie Ihre Organisation auf Benutzer, die Microsoft 365 zum Identifizieren von Mitarbeitern verwenden, die über ein Administratorkonto verfügen, aber möglicherweise keinen Zugriff auf Microsoft 365 benötigen, da sie sich nicht bei diesen Portalen anmelden. Weitere Informationen finden Sie unter [Aktivitätsberichte im Microsoft 365 Admin Center](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Einrichten von Notfallreaktionsplan-Besitzern

Das Einrichten einer Funktion für eine erfolgreiche Reaktion auf Vorfälle setzt eine umfangreiche Planung und erhebliche Ressourcen voraus. Sie müssen ständig Cyberangriffe überwachen und Prioritäten für die Behandlung von Vorfällen festlegen. Sammeln, analysieren und melden Sie Vorfallsdaten, um Beziehungen aufzubauen und für die Kommunikation mit anderen internen Gruppen und Planbesitzern zu sorgen. Weitere Informationen finden Sie unter [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Schützen von lokalen privilegierten Administratorkonten, falls noch nicht geschehen

Wenn Ihre Azure Active Directory-Organisation mit einem lokalen Active Directory synchronisiert wird, orientieren Sie sich an dem Leitfaden in [Schützen des privilegierten Zugriffs](/windows-server/identity/securing-privileged-access/securing-privileged-access): Diese Phase umfasst Folgendes:

* Erstellen separater Administratorkonten für Benutzer, die lokale Verwaltungsaufgaben durchführen müssen
* Bereitstellen von Privileged Access Workstations (PAW) für Active Directory-Administratoren
* Erstellen einmaliger lokaler Administratorkennwörter für Workstations und Server

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Zusätzliche Schritte für Organisationen, die den Zugriff auf Azure verwalten

#### <a name="complete-an-inventory-of-subscriptions"></a>Durchführen einer Abonnementinventur

Verwenden Sie Enterprise-Portal und Azure-Portal, um die Abonnements in Ihrer Organisation zu identifizieren, die Produktionsanwendungen hosten.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Entfernen von Microsoft-Konten aus Administratorrollen

Microsoft-Konten aus anderen Programmen wie Xbox, Live und Outlook sollten nicht als Administratorkonten für die Abonnements Ihrer Organisation verwendet werden. Entfernen Sie den Administratorstatus von allen Microsoft-Konten, und ersetzen Sie sie durch Azure AD-Geschäfts-, Schul- oder Unikonten (z. B. chris@contoso.com). Verlassen Sie sich zu Verwaltungszwecken auf Konten, die sich in Azure AD und nicht in anderen Diensten authentifizieren.

#### <a name="monitor-azure-activity"></a>Überwachen der Azure-Aktivität

Das Azure-Aktivitätsprotokoll zeigt den Verlauf der Ereignisse auf Abonnementebene in Azure an. Darüber hinaus enthält es Informationen darüber, welche Ressourcen von welcher Person zu einem bestimmten Zeitpunkt erstellt, aktualisiert und gelöscht wurden, und wann diese Ereignisse auftraten. Weitere Informationen finden Sie unter [Überwachen und Empfangen von Benachrichtigungen zu wichtigen Aktionen im Azure-Abonnement](../../azure-monitor/platform/alerts-activity-log.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Zusätzliche Schritte für Organisationen, die den Zugriff auf andere Cloud-Apps über Azure AD verwalten

#### <a name="configure-conditional-access-policies"></a>Konfigurieren von Richtlinien für bedingten Zugriff

Bereiten Sie Richtlinien für bedingten Zugriff für lokale und Cloud-gehostete Anwendungen vor. Wenn Sie über mit dem Arbeitsbereich verknüpfte persönliche Geräte von Benutzern verfügen, erhalten Sie weitere Informationen unter [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](../../active-directory-b2c/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Phase 3: Kontrolle der Administratoraktivitäten

![Phase 3: Kontrolle der Administratoraktivitäten](./media/security-planning/stage-three.png)

Phase 3 baut auf den Maßnahmen aus Phase 2 auf und sollte innerhalb von ca. 1 bis 3 Monaten implementiert werden. Diese Phase der Roadmap für geschützten privilegierten Zugriff umfasst die folgenden Komponenten.

### <a name="general-preparation"></a>Allgemeine Vorbereitung

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Durchführen einer Zugriffsüberprüfung von Benutzern in Administratorrollen

Mehr Unternehmensbenutzer erhalten privilegierten Zugriff über Clouddienste, was zu einem nicht verwalteten Zugriff führen kann. Benutzer können heutzutage globale Administratoren für Microsoft 365 oder Azure-Abonnementadministratoren werden oder Administratorzugriff auf virtuelle Computer oder über SaaS-Apps haben.

Ihre Organisation sollte dafür sorgen, dass alle Mitarbeiter normale geschäftliche Transaktionen als nicht privilegierte Benutzer ausführen und ihnen Administratorrechte nur bei Bedarf gewähren. Vervollständigen Sie die Zugriffsüberprüfungen, um die Benutzer zu identifizieren und zu bestätigen, die zum Aktivieren von Administratorrechten berechtigt sind.

Wir empfehlen Folgendes:

1. Bestimmen Sie, welche Benutzer Azure AD-Administratoren sind, aktivieren Sie bedarfsorientierten und Just-in-Time-Administratorzugriff sowie rollenbasierte Sicherheitssteuerungen.
2. Konvertieren Sie Benutzer, für deren Berechtigung zum privilegierten Administratorzugriff es keinen triftigen Grund gibt, in eine andere Rolle (wenn keine geeignete Rolle vorhanden ist, entfernen Sie sie).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Fortsetzen der Einführung einer sichereren Authentifizierung für alle Benutzer

Fordern Sie besonders gefährdete Benutzer auf, eine moderne, starke Authentifizierung wie Azure AD MFA oder Windows Hello zu verwenden. Zu den besonders gefährdeten Benutzern zählen zum Beispiel:

* Führungskräfte auf Vorstandsebene
* Hochrangige Manager
* Wichtige IT- und Sicherheitsmitarbeiter

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Verwenden dedizierter Arbeitsstationen für die Azure AD-Verwaltung

Angreifer haben es möglicherweise auf privilegierte Konten abgesehen, um Integrität und Authentizität von Daten zu zerstören. Sie verwenden häufig bösartigen Code, der die Programmlogik ändert oder den Administrator bei der Eingabe von Anmeldeinformationen ausspioniert. Privileged Access Workstations (PAWs) bieten für sensible Aufgaben ein dediziertes Betriebssystem, das vor Internetangriffen und Bedrohungsüberträgern geschützt ist. Die Trennung dieser sensiblen Aufgaben und Konten von den täglich genutzten Arbeitsstationen und Geräten sorgt für hohen Schutz vor folgenden Risiken:

* Phishingangriffe
* Sicherheitsrisiken in Anwendungen und beim Betriebssystem
* Angriffe durch Identitätswechsel
* Angriffe zum Diebstahl von Anmeldeinformationen wie Protokollierung von Tastaturanschlägen, Pass-the-Hash und Pass-the-Ticket

Durch die Bereitstellung von Privileged Access Workstations können Sie das Risiko reduzieren, dass Administratoren ihre Anmeldeinformationen in einer Desktopumgebung eingeben, die nicht speziell gesichert ist. Weitere Informationen finden Sie unter [Privileged Access Workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Lesen der Empfehlungen des National Institute of Standards and Technology zur Abwicklung von Vorfällen

Das National Institute of Standards and Technology (NIST) bietet Richtlinien für den Umgang mit Incidents, insbesondere für die Analyse incidentbezogener Daten und die Bestimmung der richtigen Reaktion auf jeden Incident. Weitere Informationen finden Sie unter [The (NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Handbuch zur Behandlung von Computersicherheitsvorfällen).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementieren von Privileged Identity Management (PIM) für JIT in zusätzlichen Administratorrollen

Verwenden Sie für Azure Active Directory die Funktion [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). Die zeitlich begrenzte Aktivierung von privilegierten Rollen ermöglicht Ihnen Folgendes:

* Aktivieren von Administratorrechten zum Ausführen einer bestimmten Aufgabe
* Erzwingen der mehrstufigen Authentifizierung während der Aktivierung
* Verwenden von Warnungen, um Administratoren über Out-of-band-Änderungen zu informieren
* Benutzern ermöglichen, den privilegierten Zugriff für eine vorkonfigurierte Zeitspanne beizubehalten
* Räumen Sie Sicherheitsadministratoren die folgenden Möglichkeiten ein:

  * Erkennen aller Identitäten mit hohen Zugriffsrechten
  * Anzeigen von Überwachungsberichten
  * Erstellen von Zugriffsüberprüfungen zum Identifizieren aller Benutzer, die zum Aktivieren von Administratorrechten berechtigt sind

Wenn Sie Azure AD Privileged Identity Management bereits verwenden, passen Sie die Zeitrahmen für zeitgebundene Berechtigungen nach Bedarf an (z. B. Wartungsfenster).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Ermitteln der Gefährdung durch kennwortbasierte Anmeldeprotokolle (bei Verwendung von Exchange Online)

Wir empfehlen, jeden potenziellen Benutzer zu identifizieren, bei dem die Kompromittierung der Anmeldeinformationen katastrophale Folgen für die Organisation haben kann. Etablieren Sie für diese Benutzer strenge Authentifizierungsanforderungen, und verwenden Sie den bedingten Zugriff von Azure AD, um zu verhindern, dass sie sich mit ihrem Benutzernamen und Kennwort bei ihrer E-Mail-Anwendung anmelden. Sie können die [Legacyauthentifizierung mit bedingtem Zugriff](../conditional-access/block-legacy-authentication.md) und die [Basisauthentifizierung](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) über Exchange Online blockieren.

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Durchführen einer Rollenbewertung für Microsoft 365-Rollen (bei Verwendung von Microsoft 365)

Bewerten Sie, ob alle Administratorenbenutzer die richtigen Rollen besitzen (löschen Sie Rollen anhand dieser Bewertung, und weisen Sie sie neu zu).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Machen Sie sich mit dem in Microsoft 365 verwendeten Ansatz zum Sicherheitsincidentmanagement vertraut, und vergleichen Sie ihn mit Ihrer eigenen Organisation.

Sie können diesen Bericht aus dem [Sicherheitsincidentmanagement in Microsoft 365](https://www.microsoft.com/download/details.aspx?id=54302) herunterladen.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Fortsetzen des Schützens von lokalen privilegierten Administratorkonten

Wenn Ihr Azure Active Directory-Mandant mit einem lokalen Active Directory verbunden ist, orientieren Sie sich an dem Leitfaden in [Schützen des privilegierten Zugriffs](/windows-server/identity/securing-privileged-access/securing-privileged-access): Phase 2. In dieser Phase führen Sie folgende Aktionen aus:

* Bereitstellen von Privileged Access Workstations für alle Administratoren
* Anfordern von MFA
* Verwenden von JEA (Just Enough Admin) für die Wartung von Domänencontrollern, um die Angriffsfläche der Domänen zu minimieren
* Bereitstellen von Advanced Threat Assessment für die Angriffserkennung

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Zusätzliche Schritte für Organisationen, die den Zugriff auf Azure verwalten

#### <a name="establish-integrated-monitoring"></a>Einrichten der integrierten Überwachung

[Azure Security Center](../../security-center/security-center-introduction.md):

* Bietet eine integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements
* Hilft bei der Erkennung von Bedrohungen, die sonst möglicherweise unbemerkt bleiben
* Funktioniert mit einer großen Palette von Sicherheitslösungen

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventarisieren Ihrer privilegierten Konten auf gehosteten virtuellen Computern

In der Regel müssen Sie Benutzern keine uneingeschränkten Berechtigungen für Ihre sämtlichen Azure-Abonnements oder -Ressourcen erteilen. Verwenden Sie Azure AD-Administratorrollen, um nur den Zugriff zu gewähren, den die Benutzer für ihre Arbeit benötigen. Mit Azure AD-Administratorrollen können Sie einem Administrator nur die Verwaltung virtueller Computer in einem Abonnement gestatten, während ein anderer im gleichen Abonnement SQL-Datenbanken verwalten kann. Weitere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure?](../../active-directory-b2c/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementieren von PIM für Azure AD-Administratorrollen

Verwenden Sie Privileged Identity Management mit Azure AD-Administratorrollen zum Verwalten, Steuern und Überwachen des Zugriffs auf Azure-Ressourcen. PIM bietet Schutz durch Herabsetzen der Gefährdungszeit für Berechtigungen und bietet mithilfe von Berichten und Warnungen einen größeren Einblick in die Verwendung dieser Berechtigungen. Weitere Informationen finden Sie unter [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Verwenden von Azure-Protokollintegrationen zum Senden von relevanten Azure-Protokollen an Ihre SIEM-Systeme

Mit der Azure-Protokollintegration können Sie nicht aufbereitete Protokolle von Ihren Azure-Ressourcen in die vorhandenen SIEM-Systeme (Security Information and Event Management) Ihrer Organisation integrieren. Mit der [Azure-Protokollintegration](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) erfassen Sie Windows-Ereignisse aus Protokollen der Windows-Ereignisanzeige und Azure-Ressourcen aus folgenden Quellen:

* Azure-Aktivitätsprotokolle
* Azure Security Center-Warnungen
* Azure-Ressourcenprotokolle

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Zusätzliche Schritte für Organisationen, die den Zugriff auf andere Cloud-Apps über Azure AD verwalten

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementieren der Benutzerbereitstellung für verbundene Apps

Azure AD bietet Ihnen die Möglichkeit, das Erstellen und Pflegen von Benutzeridentitäten in Cloud-Apps wie Dropbox, Salesforce und ServiceNow zu automatisieren. Weitere Informationen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrieren von Information Protection

Mit Microsoft Cloud App Security können Sie Dateien untersuchen und Richtlinien auf Grundlage von Klassifizierungsbezeichnungen von Azure Information Protection festlegen, sodass Sie einen größeren Einblick in Ihre Daten in der Cloud gewinnen und sie besser unter Kontrolle haben. Überprüfen und klassifizieren Sie Dateien in der Cloud und wenden Sie Azure Information Protection-Bezeichnungen an. Weitere Informationen finden Sie unter [Integration mit Azure Information Protection](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurieren des bedingten Zugriffs

Konfigurieren Sie den bedingten Zugriff basierend auf einer Gruppen-, Standort- und Anwendungsvertraulichkeit für [SaaS-Apps](https://azure.microsoft.com/overview/what-is-saas/) und verbundene Azure AD-Apps. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Überwachen der Aktivität in verbundenen Cloud-Apps

Wir empfehlen die Verwendung von [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security), um sicherzustellen, dass der Benutzerzugriff auch in verbundenen Anwendungen geschützt ist. Diese Funktion schützt den Zugriff Ihres Unternehmens auf Cloud-Apps, sichert Ihre Administratorkonten und bietet folgende Möglichkeiten:

* Erweitern des Einblicks in Cloud-Apps und der Kontrollmöglichkeiten
* Erstellen von Richtlinien für Zugriff, Aktivitäten und Datenfreigabe
* Automatisches Identifizieren von riskanten Aktivitäten, nicht normalem Verhalten und Bedrohungen
* Verhindern von Datenlecks
* Minimieren von Risiken, automatische Prävention und Durchsetzung von Richtlinien

Der SIEM-Agent von Cloud App Security integriert Cloud App Security in Ihren SIEM-Server, um zentralisierte Überwachung von Microsoft 365-Warnungen und Aktivitäten zu ermöglichen. Er wird auf dem Server ausgeführt, bezieht Warnungen und Aktivitäten aus Cloud App Security und streamt sie an den SIEM-Server. Weitere Informationen finden Sie unter [SIEM-Integration](/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>Phase 4: Weiterer Aufbau von Schutzmaßnahmen

![Phase 4: Schaffen eines aktiven Sicherheitsstatus](./media/security-planning/stage-four.png)

Phase 4 der Roadmap sollte nach 6 Monaten oder später implementiert werden. Vervollständigen Sie die Roadmap, um den Schutz des privilegierten Zugriffs vor den heute bekannten Angriffen zu erhöhen. In Hinblick auf zukünftige Sicherheitsbedrohungen empfehlen wir, die Sicherheit als fortwährenden Prozess zu betrachten, um die entsprechenden Gelder aufzubringen und die Erfolgsrate der Angriffe auf Ihre Umgebung zu reduzieren.

Das Sichern des privilegierten Zugriffs ist wichtig, um Sicherheitsgarantien für Ihre geschäftlichen Ressourcen zu etablieren. Diese Maßnahme sollte jedoch Teil eines umfassenden Sicherheitsprogramms sein, das für permanente Sicherheitsgarantien sorgt. Dieses Programm sollte sich u.a. auf folgende Elemente beziehen:

* Richtlinie
* Operationen (Operations)
* Informationssicherheit
* Server
* Anwendungen
* Computer
* Geräte
* Cloud-Fabric

Für die Verwaltung von Konten für den privilegierten Zugriff empfehlen wir die folgenden Methoden:

* Stellen Sie sicher, dass Administratoren ihre Routineaufgaben als Benutzer ohne Privilegien ausführen
* Gewähren Sie privilegierten Zugriff nur bei Bedarf, und entziehen sie ihn danach (Just-in-Time)
* Speichern Sie Aktivitätsüberwachungsprotokolle im Zusammenhang mit privilegierten Konten

Weitere Informationen zum Erstellen einer vollständigen Sicherheitsroadmap finden Sie unter [Ressourcen zur Cloud-IT-Architektur von Microsoft](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Um Unterstützung bei der Implementierung der einzelnen Phasen Ihrer Roadmap durch Microsoft-Dienste zu erhalten, wenden Sie sich an Ihren Microsoft-Vertriebsbeauftragten, oder lesen Sie [Erstellen von wichtigen Schutzmaßnahmen vor Cyberangriffen zum Schutz Ihres Unternehmens](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Diese letzte laufende Phase der Roadmap für geschützten privilegierten Zugriff umfasst die folgenden Komponenten.

### <a name="general-preparation"></a>Allgemeine Vorbereitung

#### <a name="review-admin-roles-in-azure-ad"></a>Überprüfen der Administratorrollen in Azure AD

Ermitteln Sie, ob die derzeit integrierten Azure AD-Administratorrollen noch auf dem neuesten Stand sind, und sorgen Sie dafür, dass die Benutzer nur den tatsächlich erforderlichen Rollen zugewiesen sind. Mit Azure AD können Sie separate Administratoren für unterschiedliche Funktionen zuweisen. Weitere Informationen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](permissions-reference.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Überprüfen von Benutzern, die in Azure AD eingebundene Geräte verwalten

Weitere Informationen finden Sie unter [Konfigurieren von in Azure Active Directory eingebundenen Hybridgeräten](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>Überprüfen der Mitglieder [integrierter Administratorrollen in Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Überspringen Sie diesen Schritt, wenn Sie Microsoft 365 nicht verwenden.
‎
#### <a name="validate-incident-response-plan"></a>Validieren des Notfallreaktionsplans

Um Ihren Plan zu verbessern, empfiehlt Microsoft, dass Sie regelmäßig überprüfen, ob Ihr Plan wie erwartet funktioniert:

* Prüfen Sie Ihre vorhandene Roadmap auf Lücken
* Ziehen Sie Bilanz, und überarbeiten Sie entweder vorhandene bewährte Methoden oder definieren Sie neue Methoden
* Stellen Sie sicher, dass Ihr aktualisierter Notfallreaktionsplan und Ihre bewährten Methoden in der gesamten Organisation verbreitet werden


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Zusätzliche Schritte für Organisationen, die den Zugriff auf Azure verwalten 

Bestimmen Sie, ob Sie [den Besitz eines Azure-Abonnements auf ein anderes Konto übertragen müssen](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>Was bei einem Notfall zu tun ist

![Konten für den Notfallzugriff](./media/security-planning/emergency.jpeg)

1. Versorgen Sie Führungskräfte und Sicherheitsbeauftragte mit Informationen zu dem Vorfall.

2. Überprüfen Sie Ihr Angriffs-Playbook.

3. Greifen Sie auf Ihre Benutzername-/Kennwortkombination für das Notfallkonto zu, um sich bei Azure AD anzumelden.

4. Bitten Sie Microsoft um Hilfe, indem Sie [eine Azure-Supportanfrage öffnen](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Werten Sie die [Azure AD-Anmeldeberichte aus](../reports-monitoring/overview-reports.md). Zwischen dem Auftreten eines Ereignisses und seiner Einbeziehung in den Bericht kann eine gewisse Zeit verstreichen.

6. Wenn Sie in einer Hybridumgebung arbeiten und Ihre lokale Verbundinfrastruktur und Ihr AD FS-Server nicht verfügbar sind, können Sie vorübergehend von der Verbundauthentifizierung zur Kennworthashsynchronisierung wechseln. Dadurch erfolgt ein Wechsel vom Domänenverbund zurück zur verwalteten Authentifizierung, bis der AD FS-Server wieder verfügbar ist.

7. Überwachen Sie E-Mail für privilegierte Konten.

8. Stellen Sie sicher, dass Sie Sicherungen der relevanten Protokolle für potenzielle forensische und rechtliche Untersuchungen speichern.

Weitere Informationen zur Behandlung von Sicherheitsvorfällen durch Microsoft Office 365 finden Sie unter [Sicherheitsvorfallmanagement in Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Häufig gestellte Fragen: Antworten zum Sichern des privilegierten Zugriffs  

**F:** Wie gehe ich vor, wenn ich noch keine Komponenten für den sicheren Zugriff implementiert habe?

**Antwort:** Definieren Sie mindestens zwei Notfallkonten, weisen Sie Ihren privilegierten Administratorkonten MFA zu, und trennen Sie Benutzerkonten von globalen Administratorkonten.

**F:** Welches Problem muss nach einer Sicherheitsverletzung zuerst behandelt werden?

**Antwort:** Stellen Sie sicher, dass Sie für Personen mit hohem Gefährdungspotenzial die sicherste Authentifizierung anfordern.

**F:** Was geschieht, wenn unsere privilegierten Administratoren deaktiviert wurden?

**Antwort:** Erstellen Sie ein globales Administratorkonto, das immer auf dem neuesten Stand gehalten wird.

**F:** Was geschieht, wenn nur noch ein einziger globaler Administrator vorhanden ist, dieser aber nicht erreicht werden kann?

**Antwort:** Verwenden Sie eines Ihrer Notfallkonten, um umgehend privilegierten Zugriff zu erhalten.

**F:** Wie kann ich Administratoren innerhalb meiner Organisation schützen?

**Antwort:** Achten Sie darauf, dass Administratoren ihre täglichen Routineaufgaben stets als Standardbenutzer ohne Privilegien ausführen.

**F:** Welche Methoden haben sich für das Erstellen von Administratorkonten in Azure AD bewährt?

**Antwort:** Reservieren Sie privilegierten Zugriff für bestimmte Administratoraufgaben.

**F:** Welche Tools stehen zur Reduzierung des beständigen Administratorzugriffs zur Verfügung?

**Antwort:** Privileged Identity Management (PIM) und Azure AD-Administratorrollen.

**F:** Welche Position bezieht Microsoft hinsichtlich der Synchronisierung von Administratorkonten mit Azure AD?

**Antwort:** Administratorkonten auf Ebene 0 werden nur für lokale AD-Konten verwendet. Diese Konten werden in der Regel nicht mit Azure AD in der Cloud synchronisiert. Zu den Administratorkonten auf Ebene 0 gehören Konten, Gruppen und andere Ressourcen, die direkt oder indirekt administrative Kontrolle über die lokale Active Directory-Gesamtstruktur, über Domänen oder Domänencontroller und Ressourcen ausüben.

**F:** Wie können wir verhindern, dass Administratoren im Portal willkürlich Administratorzugriff gewähren?

**Antwort:** Verwenden Sie nicht privilegierte Konten für alle Benutzer und die meisten Administratoren. Beginnen Sie mit der Entwicklung eines Bedarfs der Organisation, um festzustellen, welche Administratorkonten höchstens privilegiert sein sollten. Achten Sie auch auf neu erstellte Benutzer mit Administratorrechten.

## <a name="next-steps"></a>Nächste Schritte

* [Microsoft Trust Center für Produktsicherheit](https://www.microsoft.com/trustcenter/security) – Sicherheitsfeatures von Cloudprodukten und -diensten von Microsoft

* [Microsoft Trust Center – Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – die umfassenden Complianceangebote von Microsoft für Clouddienste

* [Leitfaden zum Ausführen einer Risikobewertung](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – Verwalten von Sicherheits- und Complianceanforderungen für Clouddienste von Microsoft

### <a name="other-microsoft-online-services"></a>Andere Microsoft Online Services

* [Microsoft Intune-Sicherheit](https://www.microsoft.com/trustcenter/security/intune-security) – Intune bietet cloudbasierte Verwaltungsfunktionen für mobile Geräte, mobile Anwendungen und PCs.

* [Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 ist die cloudbasierte Lösung von Microsoft, die Funktionen für Kundenbeziehungsmanagement (Customer Relationship Management, CRM) und Enterprise Resource Planning (ERP) vereinheitlicht.
