---
title: Planen der Konfiguration von „Meine Apps“ in Azure Active Directory
description: Planungsleitfaden für die effektive Verwendung von „Meine Apps“ in Ihrem Unternehmen.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 10e548eb87b7ac4254fa916f804a6710252be7fc
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830718"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Planen der Konfiguration von „Meine Apps“ in Azure Active Directory

> [!NOTE]
> Dieser Artikel richtet sich an IT-Experten, die in ihrer Organisation die Konfiguration des Portals „Meine Apps“ planen müssen. 
>
> **Die Dokumentation für Endbenutzer finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](../user-help/my-apps-portal-end-user-access.md)** .

„Meine Apps“ von Azure Active Directory (Azure AD) ist ein webbasiertes Portal zum Starten und Verwalten von Apps. Die Seite „Meine Apps“ bietet den Benutzern einen zentralen Ort, an dem sie mit ihrer Arbeit beginnen können und alle Anwendungen finden, auf die sie Zugriff haben. Benutzer greifen über [https://myapps.microsoft.com](https://myapps.microsoft.com/) auf das Portal „Meine Apps“ zu.

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Warum muss das Portal „Meine Apps“ konfiguriert werden?

Das Portal „Meine Apps“ ist standardmäßig für Benutzer verfügbar und kann nicht deaktiviert werden. Es ist wichtig, es zu konfigurieren, um die bestmögliche Benutzererfahrung zu realisieren und den Nutzen des Portals zu erhalten. 

Eine Anwendung in der Liste der Azure Active Directory-Unternehmensanwendungen wird angezeigt, wenn die beiden folgenden Bedingungen erfüllt sind:

* Die Sichtbarkeitseigenschaft für die App ist auf TRUE eingestellt. 

* Die App ist einem Benutzer oder einer Gruppe zugewiesen. Sie wird den zugewiesenen Benutzern angezeigt.

Durch das Konfigurieren des Portals wird gewährleistet, dass die richtigen Personen problemlos die richtigen Apps finden können.

 
### <a name="how-is-the-my-apps-portal-used"></a>Warum wird das Portal „Meine Apps“ verwendet?

Benutzer greifen zu folgenden Zwecken auf das Portal „Meine Apps“ zu:

* Ermitteln aller mit Azure AD verbundenen Anwendungen ihres Unternehmens, auf die sie Zugriff haben, und Zugreifen auf diese Anwendungen.

   * Es empfiehlt sich, die Apps für einmaliges Anmelden (Single Sign-On, SSO) zu konfigurieren, um eine optimale Benutzererfahrung zu ermöglichen.

* Anfordern des Zugriffs auf neue Apps, die für Self-Service konfiguriert sind.

* Erstellen persönlicher Sammlungen von Apps.

* Verwalten des Zugriffs auf Apps für andere Personen, wenn ihnen die Rolle des Gruppenbesitzers oder die delegierte Steuerungsberechtigung für die Gruppe zugewiesen ist, die zum Gewähren des Zugriffs auf die Anwendung(en) verwendet wird.

Administratoren können Folgendes konfigurieren:

* [Zustimmungsprozesse ](../manage-apps/configure-user-consent.md) (einschließlich Vertragsbedingungen)

* [Self-Service-Anwendungsermittlung und -Zugriffsanforderungen](../manage-apps/access-panel-manage-self-service-access.md)

* [Anwendungssammlungen](../manage-apps/access-panel-collections.md)

* Zuweisen von Symbolen zu Anwendungen

* Benutzerfreundliche Namen für Anwendungen

* Im Portal „Meine Apps“ angezeigtes Unternehmensbranding

 

## <a name="plan-consent-configuration"></a>Planen der Zustimmungskonfiguration

Es gibt zwei Zustimmungsarten: Benutzerzustimmung und Zustimmung für Apps, die auf Daten zugreifen.

![Screenshot: Zustimmungskonfiguration](./media/my-apps-deployment-plan/my-apps-consent.png)

### <a name="user-consent-for-applications"></a>Benutzerzustimmung für Anwendungen 

Benutzer oder Administratoren müssen den Nutzungsbedingungen und den Datenschutzrichtlinien jeder Anwendung zustimmen. Sie müssen entscheiden, ob Benutzer oder nur Administratoren Anwendungen zustimmen können. **Wenn Ihre Geschäftsregeln dies zulassen, empfiehlt es sich, die Administratorzustimmung zu verwenden, um die Kontrolle über die Anwendungen in Ihrem Mandanten zu behalten**.

Damit Sie die Einwilligung des Administrators nutzen können, müssen Sie ein globaler Administrator der Organisation sein, und die Anwendungen müssen eine der folgenden Anforderungen erfüllen:

* Sie sind in Ihrer Organisation registriert.

* Sie sind in einer anderen Azure AD-Organisation registriert, und mindestens ein Benutzer hat zuvor seine Zustimmung erteilt.

Wenn Sie Benutzern die Zustimmung erlauben möchten, müssen Sie entscheiden, ob diese jeder App oder nur unter bestimmten Umständen zustimmen können.

Weitere Informationen finden Sie unter [Konfigurieren der Art der Endbenutzerzustimmung für eine Anwendung in Azure Active Directory](../manage-apps/configure-user-consent.md).

### <a name="group-owner-consent-for-apps-accessing-data"></a>Gruppenbesitzereinwilligung für Apps, die auf Daten zugreifen

Legen Sie fest, ob Besitzer von Azure AD-Sicherheitsgruppen oder M365-Gruppen Anwendungen den Zugriff auf Daten für die Gruppen, die sie besitzen, erlauben können. Mögliche Auswahl: Gruppenbenutzereinwilligung nicht zulassen,für alle Gruppenbesitzer zulassen oder für ausgewählte Gruppenbesitzer zulassen.

Weitere Informationen finden Sie unter [Konfigurieren der Gruppenbesitzereinwilligung](../manage-apps/configure-user-consent-groups.md).

Konfigurieren Sie dann Ihre [Einstellungen für die Benutzer- und Gruppenbesitzereinwilligung](https://portal.azure.com/) im Azure-Portal.

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Informieren Sie die Benutzer proaktiv darüber, wie und wann sich ihre Erfahrungen ändern werden und wie sie bei Bedarf Unterstützung erhalten können.

Obwohl „Meine Apps“ in der Regel keine Benutzerprobleme verursacht, ist es wichtig, sich darauf vorzubereiten. Erstellen Sie vor dem Start Leitfäden und eine Liste aller Ressourcen für Ihre Supportmitarbeiter.

#### <a name="communications-templates"></a>Kommunikationsvorlagen

Microsoft stellt [anpassbare Vorlagen für E-Mails und andere Kommunikationsformen](https://aka.ms/APTemplates) für „Meine Apps“ zur Verfügung. Sie können diese Ressourcen für die Nutzung in anderen Kommunikationskanälen entsprechend Ihrer Unternehmenskultur anpassen.

 

## <a name="plan-your-sso-configuration"></a>Planen der SSO-Konfiguration

Es empfiehlt sich, für alle Apps im Portal „Meine Apps“ einmaliges Anmelden (SSO) zu aktivieren, um Benutzern eine nahtlose Benutzererfahrung ohne erneute Eingabe der Anmeldeinformationen zu ermöglichen.

Azure AD unterstützt mehrere SSO-Optionen. 

* Weitere Informationen hierzu finden Sie unter [Optionen für einmaliges Anmelden in Azure AD](sso-options.md).

* Weitere Informationen dazu, wie Sie Azure AD als Identitätsanbieter für eine App verwenden, finden Sie unter [Schnellstartserie zur Anwendungsverwaltung](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Verwenden von Verbund-SSO (sofern möglich)

Zugunsten einer optimalen Benutzererfahrung auf der Seite „Meine Apps“ sollten Sie zunächst Cloudanwendungen integrieren, die für Verbund-SSO (OpenID Connect oder SAML) verfügbar sind. Verbund-SSO ermöglicht Benutzern über App-Startoberflächen hinweg eine einheitliche 1-Klick-Umgebung und ist hinsichtlich der Konfigurationssteuerung tendenziell stabiler.

Weitere Informationen zum Konfigurieren von SaaS-Anwendungen (Software-as-a-Service) für einmaliges Anmelden (SSO) finden Sie unter [SaaS-SSO-Bereitstellungsplan]../Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Überlegungen zu besonderen SSO-Bedingungen

> [!TIP]
> Verwenden Sie zugunsten einer besseren Benutzererfahrung Verbund-SSO mit Azure AD (OpenID Connect/SAML) anstelle von kennwortbasiertem SSO und ADFS, wenn eine Anwendung dies unterstützt.

Zum Anmelden bei Anwendungen mit kennwortbasierter einmaliger Anmeldung oder Anwendungen mit Zugriff über Azure AD-Anwendungsproxy müssen Benutzer die Erweiterung „Meine Apps“ für die sichere Anmeldung installieren und verwenden. Beim ersten Start der Anwendung mit kennwortbasierter einmaliger Anmeldung oder Anwendungsproxy werden die Benutzer aufgefordert, die Erweiterung zu installieren. 

![Screenshot](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Ausführliche Informationen zur Erweiterung finden Sie unter [Installieren der Browsererweiterung „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

Wenn Sie diese Anwendungen integrieren müssen, sollten Sie einen Mechanismus definieren, um die Erweiterung skalierbar mit [unterstützten Browsern](../user-help/my-apps-portal-end-user-access.md) bereitzustellen. Beispiele für Optionen:

* [Benutzergesteuerter Download und Konfiguration für Chrome, Firefox, Microsoft Edge oder Internet Explorer](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager für Internet Explorer](https://docs.microsoft.com/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Die Erweiterung ermöglicht den Benutzern, jede App über die Suchleiste zu starten, kürzlich verwendete Anwendungen zu finden und darauf zuzugreifen und einen Link zur Seite „Meine Apps“ zu verwenden.

![Screenshot: Suche in der Erweiterung „Meine Apps“](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Planen des mobilen Zugriffs

Bei Anwendungen, die kennwortbasiertes einmaliges Anmelden verwenden, oder auf die über den [Microsoft Azure AD-Anwendungsproxy](../manage-apps/application-proxy.md) zugegriffen wird, müssen Sie Microsoft Edge für Mobilgeräte verwenden. Bei anderen Anwendungen kann jeder mobile Browser verwendet werden. 

### <a name="linked-sso"></a>Verknüpftes einmaliges Anmelden

Anwendungen können mithilfe der Option „Verknüpftes einmaliges Anmelden“ hinzugefügt werden. Sie können eine Anwendungskachel konfigurieren, die auf die URL Ihrer vorhandenen Webanwendung verweist. Mit „Verknüpftes einmaliges Anmelden“ können Sie beginnen, Benutzer zum „Meine Apps“-Portal zu leiten, ohne alle Anwendungen nach Azure AD SSO migrieren zu müssen. Sie können schrittweise zu Azure AD-Anwendungen wechseln, für die einmaliges Anmelden konfiguriert wurde, ohne die Benutzererfahrung zu beeinträchtigen.

## <a name="plan-the-user-experience"></a>Planen der Benutzeroberfläche

Standardmäßig werden alle Anwendungen, auf die der Benutzer Zugriff hat, und alle Anwendungen, die für die Self-Service-Ermittlung konfiguriert sind, im Bereich „Meine Apps“ des Benutzers angezeigt. Bei vielen Unternehmen kann dies eine umfangreiche Liste sein, die ungeordnet sehr unübersichtlich werden kann.

### <a name="plan-my-apps-collections"></a>Planen von „Meine Apps“-Sammlungen

Jede Azure AD-Anwendung, auf die ein Benutzer Zugriff hat, wird in „Meine Apps“ in der Sammlung „Alle Apps“ angezeigt. Zum Gruppieren verwandter Anwendungen sollten Sie Sammlungen verwenden und diese auf einer separaten Registerkarte anordnen, damit sie leichter zu finden sind. So können Sie mit Sammlungen beispielsweise logische Gruppierungen von Anwendungen für bestimmte Positionen, Aufgaben, Projekte usw. erstellen. 

Endbenutzer können ihre Oberfläche auf folgende Weise ebenfalls anpassen:

* Erstellen eigener App-Sammlungen

* [Ausblenden und Neuanordnen von App-Sammlungen](access-panel-collections.md).

![Screenshot: Self-Service-Konfiguration](./media/my-apps-deployment-plan/collections.png)

Es gibt eine Option, mit der Sie Apps aus dem Portal „Meine Apps“ ausblenden, auf die Apps aber trotzdem noch an anderen Stellen (z. B. im Microsoft 365-Portal) zugreifen können. Weitere Informationen: [Ausblenden einer Anwendung auf der Benutzeroberfläche in Azure Active Directory](hide-application-from-user-portal.md)

> [!IMPORTANT]
> Über das Portal „Meine Apps“ können nur 950 Apps aufgerufen werden, auf die der Benutzer Zugriff hat. Dazu zählen auch die Apps, die durch den Benutzer oder den Administrator ausgeblendet wurden. 

### <a name="plan-self-service-group-management-membership"></a>Planen von Self-Service-Gruppenverwaltung und Mitgliedschaft

Sie können Benutzern die Erstellung und Verwaltung ihrer eigenen Sicherheitsgruppen oder Microsoft 365-Gruppen in Azure AD ermöglichen. Der Besitzer der Gruppe kann Mitgliedschaftsanforderungen genehmigen oder ablehnen sowie die Steuerung der Gruppenmitgliedschaft delegieren. Funktionen für die Self-Service-Gruppenverwaltung sind nicht für E-Mail-aktivierte Sicherheitsgruppen oder Verteilerlisten verfügbar.

Zum Planen der Self-Service-Gruppenmitgliedschaft legen Sie fest, ob Sie allen Benutzern in Ihrem Unternehmen oder nur einer Teilmenge von Benutzern erlauben, Gruppen zu erstellen und zu verwalten. Wenn Sie nur eine Teilmenge von Benutzern auswählen, müssen Sie eine Gruppe einrichten, der diese Personen hinzugefügt werden. 

Weitere Informationen zum Aktivieren dieser Szenarien finden Sie unter [Einrichten der Self-Service-Gruppenverwaltung in Azure Active Directory](../enterprise-users/groups-self-service-management.md).

### <a name="plan-self-service-application-access"></a>Planen des Self-Service-Anwendungszugriff

Sie können Benutzern ermöglichen, Anwendungen über den Bereich „Meine Apps“ zu ermitteln und Zugriff auf diese Anwendungen anzufordern. Dazu müssen Sie zuerst folgende Schritte ausführen: 

* Aktivieren der Self-Service-Gruppenverwaltung

* Aktivieren der App für einmaliges Anmelden (SSO)

* Erstellen einer Gruppe für den Anwendungszugriff

![Screenshot: Konfiguration der Self-Service-Funktionen in „Meine Apps“](./media/my-apps-deployment-plan/my-apps-self-service.png)

Beim Anfordern des Zugriffs fordern Benutzer den Zugriff auf die zugrundeliegende Gruppe an, und Gruppenbesitzer können die Berechtigung zum Verwalten der Gruppenmitgliedschaft und somit des Anwendungszugriffs delegieren. Für die ausdrückliche Genehmigung des Zugriffs auf Anwendungen stehen Genehmigungsworkflows zur Verfügung. Benutzer, die genehmigende Personen sind, erhalten Benachrichtigungen im Portal „Meine Apps“, wenn eine Anforderung für den Zugriff auf die Anwendung aussteht.

## <a name="plan-reporting-and-auditing"></a>Planen der Berichterstellung und Überwachung

Azure AD stellt [Berichte mit technischen und geschäftlichen Erkenntnissen](../reports-monitoring/overview-reports.md) zur Verfügung. Arbeiten Sie mit den Besitzern der geschäftlichen und technischen Anwendungen zusammen, um Besitzer dieser Berichte zu werden und sie regelmäßig zu nutzen. Die folgende Tabelle enthält einige Beispiele für typische Berichtsszenarien.

| Beispiel| Risikomanagement| Steigerung der Produktivität| Governance und Einhaltung |
| - | - | - | -|
| Berichtstypen| Anwendungsberechtigungen und -nutzung| Kontobereitstellungsaktivität| Überprüfen, wer auf die Anwendungen zugreift |
| Mögliche Aktionen| Überwachen des Zugriffs, Widerrufen von Berechtigungen| Beheben von Bereitstellungsfehlern| Widerrufen des Zugriffs |


Azure AD speichert die meisten Überwachungsdaten für 30 Tage. Die Daten stehen über das Azure-Verwaltungsportal oder die API zur Verfügung, die Sie in Ihre Analysesysteme herunterladen können.

#### <a name="auditing"></a>Überwachung

Die Überwachungsprotokolle für den Anwendungszugriff sind 30 Tage lang verfügbar. Wenn für Ihr Unternehmen eine längere Aufbewahrungszeit erforderlich ist, exportieren Sie die Protokolle in ein SIEM-Tool (Security Information Event and Management) wie Splunk oder ArcSight.

Dokumentieren Sie für Überwachungs- und Berichtszwecke sowie für Notfallwiederherstellungssicherungen die erforderliche Downloadhäufigkeit, das Zielsystem und wer für die Verwaltung der einzelnen Sicherungen verantwortlich ist. Möglicherweise benötigen Sie keine separaten Sicherungen für die Überwachung und Berichterstellung. Ihre Sicherung zur Notfallwiederherstellung sollte eine separate Entität sein.

## <a name="validate-your-deployment"></a>Überprüfen der Bereitstellung

Stellen Sie sicher, dass die Bereitstellung von „Meine Apps“ sorgfältig getestet wurde und ein Rollbackplan vorhanden ist.

Führen Sie die folgenden Tests sowohl mit firmeneigenen als auch mit privaten Geräten durch. Diese Testfälle sollten auch Ihre geschäftlichen Anwendungsfälle widerspiegeln. Nachstehend finden Sie einige Fälle, die auf typischen technischen Szenarien basieren. Fügen Sie weitere, auf Ihre Bedürfnisse zugeschnittene Testfälle hinzu.

#### <a name="application-sso-access-test-case-examples"></a>Beispiele für Testfälle mit SSO-Zugriff auf die Anwendung:


| Geschäftsszenario| Erwartetes Ergebnis |
| - | - |
| Benutzer meldet sich beim „Meine Apps“-Portal an| Benutzer kann sich anmelden und seine Anwendungen anzeigen |
| Benutzer startet eine Verbund-SSO-Anwendung| Benutzer wird automatisch bei der Anwendung angemeldet |
| Benutzer öffnet zum ersten Mal eine Kennwort-SSO-Anwendung| Benutzer muss die Erweiterung „Meine Apps“ installieren |
| Benutzer startet eine Kennwort-SSO-Anwendung zu einem späteren Zeitpunkt erneut| Benutzer wird automatisch bei der Anwendung angemeldet |
| Benutzer startet eine App über das Microsoft 365-Portal| Benutzer wird automatisch bei der Anwendung angemeldet |
| Benutzer startet eine App über den verwalteten Browser| Benutzer wird automatisch bei der Anwendung angemeldet |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Beispiele für Testfälle mit Self-Service-Funktionen für Anwendungen


| Geschäftsszenario| Erwartetes Ergebnis |
| - | - |
| Benutzer kann die Mitgliedschaft für die Anwendung verwalten| Benutzer kann Mitglieder hinzufügen/entfernen, die Zugriff auf die App haben |
| Benutzer kann die Anwendung bearbeiten| Benutzer kann die Beschreibung und Anmeldeinformationen für Kennwort-SSO-Anwendungen bearbeiten |


### <a name="rollback-steps"></a>Rollbackschritte

Es ist wichtig, zu planen, was zu tun ist, wenn Ihre Bereitstellung nicht wie gewünscht verläuft. Wenn bei der SSO-Konfiguration während der Bereitstellung ein Fehler auftritt, müssen Sie verstehen, wie Sie [SSO-Probleme beheben](../hybrid/tshoot-connect-sso.md) und die Auswirkungen auf Ihre Benutzer verringern können. In extremen Fällen müssen Sie möglicherweise für [SSO ein Rollback durchführen](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Verwalten Ihrer Implementierung

Verwenden Sie die am wenigsten privilegierte Rolle, um eine erforderliche Aufgabe innerhalb von Azure Active Directory auszuführen. [Überprüfen Sie die verschiedenen verfügbaren Rollen](../roles/permissions-reference.md), und wählen Sie die richtige aus, um Ihre Anforderungen für jede Persona für diese Anwendung zu erfüllen. Einige Rollen müssen unter Umständen nur vorübergehend angewendet werden und können nach Abschluss der Bereitstellung wieder entfernt werden.

| Personas| Rollen| Azure AD-Rolle |
| - | - | - |
| Helpdesk-Administrator| Support der Ebene 1| Keine |
| Identitätsadministrator| Konfigurieren und Debuggen, wenn sich Probleme auf Azure AD auswirken| Globaler Administrator |
| Anwendungsadministrator| Benutzernachweis in Anwendung, Konfiguration für Benutzer mit Berechtigungen| Keine |
| Infrastrukturadministratoren| Besitzer des Zertifikatrollovers| Globaler Administrator |
| Geschäftsbesitzer/Beteiligter| Benutzernachweis in Anwendung, Konfiguration für Benutzer mit Berechtigungen| Keine |


Sie können Ihre Rollen mithilfe von [Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) verwalten, um zusätzliche Überwachung, Kontrolle und Zugriffsprüfungen für Benutzer mit Verzeichnisberechtigungen bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

[Planen einer Bereitstellung von Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Planen der Bereitstellung eines Anwendungsproxys](application-proxy-deployment-plan.md)

 
