---
title: Erstellen einer robusten Verwaltungsstrategie für die Zugriffssteuerung (Azure AD)
description: Dieses Dokument enthält Anleitungen zu Strategien, die eine Organisation übernehmen sollte, um das Risiko der Sperrung während unvorhergesehener Störungen zu reduzieren.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f70005f2c7f53833163dcd5f0d2ee89b3db37c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861288"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Erstellen einer robusten Verwaltungsstrategie für die Zugriffssteuerung in Azure Active Directory

>[!NOTE]
> Die in diesem Dokument enthaltenen Informationen stellen die Sicht der Microsoft Corporation der hier diskutierten Themen zum Zeitpunkt der Veröffentlichung dar. Da Microsoft auf wechselnde Marktbedingungen reagieren muss, sollten sie nicht als Verpflichtung seitens Microsoft interpretiert werden, und Microsoft kann die Genauigkeit der dargelegten Informationen nach dem Zeitpunkt der Veröffentlichung nicht garantieren.

Organisationen, bei denen der Schutz ihrer IT-Systeme auf einer einzelnen Zugriffssteuerung wie Multi-Factor Authentication (MFA) oder einem einzelnen Netzwerkstandort basiert, sind anfällig für Ausfälle des Zugriffs auf ihre Apps und Ressourcen, wenn diese einzelne Steuerung nicht mehr verfügbar oder falsch konfiguriert ist. Beispielsweise kann eine Naturkatastrophe dazu führen, dass große Segmente der Telekommunikationsinfrastruktur oder Unternehmensnetzwerke nicht mehr verfügbar sind. Solche eine Störung kann verhindern, dass Endbenutzer und Administratoren sich anmelden können.

Dieses Dokument enthält Anleitungen zu Strategien, die eine Organisation übernehmen sollte, um das Risiko der Sperrung während unvorhergesehener Störungen zu reduzieren, mit folgenden Szenarios:

 1. Organisationen können durch die Implementierung von Entschärfungsstrategien oder Notfallplänen die Resilienz steigern, um das Risiko einer Sperrung **vor einer Störung** zu verringern.
 2. Organisationen können mit Entschärfungsstrategien und Notfallplänen weiterhin auf Apps und Ressourcen zugreifen, die sie **während einer Unterbrechung** auswählen.
 3. Organisationen sollten sicherstellen, dass sie Daten wie z.B. Protokolle **nach einer Unterbrechung** und bevor sie implementierte Notfallpläne zurücksetzen beibehalten.
 4. Organisationen, die keine Vermeidungsstrategien oder alternative Pläne implementiert haben, sollten **Notfalloptionen** für den Umgang mit der Unterbrechung implementieren.

## <a name="key-guidance"></a>Wichtiger Leitfaden

Dieses Dokument behandelt vier wesentliche Punkte:

* Vermeiden Sie Administratorsperren durch Verwendung von Notfallzugriffs-Konten.
* Implementieren Sie MFA mithilfe des bedingten Zugriffs (CA) statt MFA pro Benutzer.
* Entschärfen Sie Benutzersperren durch mehrere Steuerungen des bedingten Zugriffs (CA).
* Entschärfen Sie Benutzersperren durch Bereitstellung mehrerer Authentifizierungsmethoden oder Entsprechungen für jeden Benutzer.

## <a name="before-a-disruption"></a>Vor einer Unterbrechung

Das Entschärfen einer tatsächlichen Unterbrechung muss für eine Organisation beim Behandeln potenzieller Probleme mit der Zugriffssteuerung Priorität haben. Zu diesem Entschärfen zählen die Planung für ein tatsächliches Ereignis sowie Implementierungsstrategien, um sicherzustellen, dass Zugriffssteuerungen und -vorgänge während der Unterbrechung nicht betroffen sind.

### <a name="why-do-you-need-resilient-access-control"></a>Warum benötigen Sie eine robuste Zugriffssteuerung?

 Die Identität ist die Steuerungsebene der Benutzer, die auf Apps und Ressourcen zugreifen. Ihr Identitätssystem steuert, welche Benutzer unter welchen Bedingungen, z.B. mit welchen Zugriffssteuerungen oder Authentifizierungsanforderungen, Zugriff auf die Anwendungen erhalten. Wenn eine oder mehrere Authentifizierungs- oder Zugriffssteuerungsanforderungen aufgrund unvorhergesehener Umstände nicht zur Authentifizierung von Benutzern verfügbar sind, können Organisationen mit einem der folgenden Probleme oder beiden konfrontiert werden:

* **Administratorsperre:** Administratoren können den Mandanten oder die Dienste nicht verwalten.
* **Benutzersperre:** Benutzer können nicht auf Apps oder Ressourcen zugreifen.

### <a name="administrator-lockout-contingency"></a>Notfallplan für die Administratorsperre

Um den Administratorzugriff auf Ihren Mandanten freizugeben, sollten Sie Notfallzugriffs-Konten erstellen. Diese *Notfallzugriffs-Konten* lassen den Zugriff auf die Verwaltung der Azure AD-Konfiguration zu, wenn die normalen Verfahren zum berechtigten Kontozugriff nicht zur Verfügung stehen. Mindestens zwei Notfallzugriffs-Konten sollten gemäß dem Artikel [Verwalten von Konten für den Notfallzugriff in Azure AD]( ../users-groups-roles/directory-emergency-access.md) erstellt werden.

### <a name="mitigating-user-lockout"></a>Entschärfen von Benutzersperren

 Um das Risiko von Benutzersperren zu entschärfen, verwenden Sie Richtlinien für bedingten Zugriff mit mehreren Steuerungen, damit Benutzer wählen können, wie sie auf Apps und Ressourcen zugreifen. Wenn Benutzer die Wahl haben, ob sie sich z.B. mit MFA **oder** über ein verwaltetes Gerät **oder** aus dem Unternehmensnetzwerk heraus anmelden, stehen ihnen Alternativen zur Fortsetzung ihrer Arbeit zur Verfügung, wenn eine der Zugriffssteuerungen nicht verfügbar ist.

#### <a name="microsoft-recommendations"></a>Empfehlungen von Microsoft

Integrieren Sie die folgenden Zugriffssteuerungen in Ihre vorhandenen Richtlinien für bedingten Zugriff in Ihrer Organisation:

1. Stellen Sie mehrere Authentifizierungsmethoden für jeden Benutzer bereit, die auf verschiedenen Kommunikationskanälen basieren, z.B. die Microsoft Authenticator-App (internetbasiert), das OATH-Token (auf dem Gerät generiert) und SMS (telefonisch). Mit dem folgenden PowerShell-Skript können Sie im Voraus identifizieren, für welche zusätzlichen Methoden sich Ihre Benutzer registrieren sollten: [Skript für die Analyse der Azure AD MFA-Authentifizierungsmethode](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).
2. Stellen Sie Windows Hello for Business auf Windows 10-Geräten bereit, um MFA-Anforderungen direkt bei der Geräteanmeldung zu erfüllen.
3. Verwenden Sie vertrauenswürdige Geräte über [Azure AD Hybrid Join](../devices/overview.md) oder [von Microsoft Intune verwaltete Geräte](/intune/planning-guide). Vertrauenswürdige Geräte verbessern die Benutzerfreundlichkeit, da das vertrauenswürdige Gerät selbst die strengen Authentifizierungsanforderungen der Richtlinie erfüllen kann, ohne dass der Benutzer die MFA durchlaufen muss. MFA ist dann erforderlich, wenn ein neues Gerät registriert wird und nicht vertrauenswürdige Geräte auf Apps oder Ressourcen zugreifen.
4. Verwenden Sie anstelle fester MFA-Richtlinien risikobasierte Azure AD-Identitätsschutz-Richtlinien, die den Zugriff verhindern, wenn mit Benutzer oder Anmeldung ein Risiko verbunden ist.
5. Wenn Sie den VPN-Zugriff mit der NPS-Erweiterung für Azure AD MFA schützen, sollten Sie für Ihre VPN-Lösung den Verbund als [SAML-App](../manage-apps/view-applications-portal.md) in Betracht ziehen und die App-Kategorie gemäß den folgenden Empfehlungen ermitteln. 

>[!NOTE]
> Risikobasierte Richtlinien erfordern [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)-Lizenzen.

Das folgende Beispiel beschreibt die Richtlinien, die Sie erstellen müssen, um eine robuste Zugriffssteuerung für den Zugriff von Benutzern auf ihre Apps und Ressourcen bereitzustellen. In diesem Beispiel benötigen Sie eine Sicherheitsgruppe **AppUsers** mit den Zielbenutzern, die Zugriff auf eine Gruppe mit dem Namen **CoreAdmins**, die die Core-Administratoren enthält, und eine Gruppe namens  **EmergencyAccess** mit den Notfallzugriffs-Konten erhalten sollen.
Dieser Beispielrichtliniensatz gewährt ausgewählten Benutzern in **AppUsers** Zugriff auf ausgewählte Apps, wenn sie die Verbindung von einem vertrauenswürdigen Gerät aus herstellen ODER eine strenge Authentifizierung wie MFA durchlaufen. Er schließt Notfallzugriffs-Konten und Core-Administratoren aus.

**CA-Entschärfungsrichtliniensatz:**

* Richtlinie 1: Blockieren des Zugriffs für Personen außerhalb der Zielgruppen
  * Benutzer und Gruppen: Alle Benutzer einbeziehen. AppUsers, CoreAdmins und EmergencyAccess ausschließen.
  * Cloud-Apps: Alle Apps einbeziehen.
  * Bedingungen: (Keine)
  * Steuerelement zur Rechteerteilung: Block
* Richtlinie 2: Gewähren des Zugriffs für AppUsers mit MFA ODER vertrauenswürdigem Gerät.
  * Benutzer und Gruppen: AppUsers einbeziehen. CoreAdmins und EmergencyAccess ausschließen.
  * Cloud-Apps: Alle Apps einbeziehen.
  * Bedingungen: (Keine)
  * Steuerelement zur Rechteerteilung: Zugriff gewähren, Multi-Factor Authentication anfordern, Konformität des Geräts anfordern. Für mehrere Steuerungen: Eine der ausgewählten Steuerungen anfordern.

### <a name="contingencies-for-user-lockout"></a>Notfallpläne für Benutzersperre

Alternativ kann Ihre Organisation auch Notfallplanrichtlinien erstellen. Um Notfallplanrichtlinien zu erstellen, müssen Sie die Kriterien des Kompromisses zwischen Geschäftskontinuität, Betriebskosten, Finanzierungskosten und Sicherheitsrisiken definieren. Sie können z.B. eine Notfallplanrichtlinie nur für eine Teilmenge von Benutzern, Apps, Clients oder Standorten aktivieren. Notfallplanrichtlinien gewähren bei einer Unterbrechung Administratoren und Endbenutzer Zugriff auf Apps und Ressourcen, wenn keine Entschärfungsmethode implementiert wurde. Microsoft empfiehlt, Notfallplanrichtlinien im [reinen Berichtsmodus](../conditional-access/howto-conditional-access-insights-reporting.md) zu aktivieren, wenn sie nicht verwendet werden, damit Administratoren die potenziellen Auswirkungen der Richtlinien überwachen können, falls diese aktiviert werden müssen.

 Wenn Sie bei einer Unterbrechung über die Gefährdung informiert sind, ist das Risiko geringer, und es ist ein wichtiger Bestandteil des Planungsprozesses. Um Ihren Notfallplan zu erstellen, bestimmen Sie zunächst die folgenden geschäftlichen Anforderungen Ihrer Organisation:

1. Ermitteln Sie im voraus Ihre unternehmenskritischen Apps: Auf welche Apps müssen Sie Zugriff gewähren, sogar mit einem höheren Risiko- und niedrigeren Sicherheitsstatus? Erstellen Sie eine Liste dieser Apps, und stellen Sie sicher, dass alle anderen Beteiligten (Business, Sicherheit, Rechtswesen, Geschäftsleitung) zustimmen, dass diese Apps auch bei Verlust der gesamten Zugriffssteuerung noch ausgeführt werden müssen. Wahrscheinlich gelangen Sie zu folgenden Kategorien:
   * **Kategorie 1: Unternehmenskritische Apps**, die nicht mehr als ein paar Minuten nicht verfügbar sein dürfen, z.B. Apps, die direkten Einfluss auf den Umsatz des Unternehmens haben.
   * **Kategorie 2: Wichtige Apps**, auf die das Unternehmen innerhalb weniger Stunden wieder zugreifen muss.
   * **Kategorie 3: Apps mit niedriger Priorität**, die eine Unterbrechung von einigen Tagen vertragen können.
2. Für Apps der Kategorie 1 und 2 empfiehlt Microsoft Ihnen, im voraus zu planen welche, Zugriffsebene Sie zulassen möchten:
   * Wünschen Sie Vollzugriff oder eingeschränkte Sitzungen, z.B. Downloadeinschränkungen?
   * Möchten Sie den Zugriff auf einen Teil der App, aber nicht die gesamte App erlauben?
   * Möchten Sie IT-Mitarbeitern den Zugriff gewähren und den Administratorzugriff blockieren, bis die Zugriffssteuerung wiederhergestellt ist?
3. Microsoft empfiehlt Ihnen auch, für solche Apps zu planen, welche Zugriffsmöglichkeiten Sie absichtlich öffnen, und welche Sie schließen werden:
   * Möchten Sie den Zugriff nur über den Browser zulassen und Rich Clients blockieren, die Daten offline speichern können?
   * Möchten Sie den Zugriff nur für Benutzer innerhalb des Unternehmensnetzwerks zulassen und externe Benutzer blockieren?
   * Möchten Sie den Zugriff während der Unterbrechung nur aus bestimmten Ländern oder Regionen zulassen?
   * Möchten Sie, dass Richtlinien für Notfallplanrichtlinien, insbesondere für unternehmenskritische Apps, ohne Erfolg oder erfolgreich sind, wenn eine alternative Zugriffssteuerung nicht verfügbar ist?

#### <a name="microsoft-recommendations"></a>Empfehlungen von Microsoft

Eine Notfallplanrichtlinie für bedingten Zugriff ist eine **Sicherungsrichtlinie**, die Azure AD MFA, mehrstufige Authentifizierungsmethoden von Drittanbietern sowie risikobasierte oder gerätebasierte Steuerungen nicht berücksichtigt. Um beim Aktivieren einer Notfallplanrichtlinie unerwartete Unterbrechungen auf ein Minimum zu beschränken, sollte die Richtlinie solange im reinen Berichtsmodus bleiben, bis sie verwendet wird. Administratoren können die potenziellen Auswirkungen ihrer Notfallplanrichtlinien mithilfe der Arbeitsmappe für Erkenntnisse zum bedingten Zugriff überwachen. Wenn Ihre Organisation entscheidet, den Notfallplan zu aktivieren, können Administratoren die Richtlinie aktivieren und die regulären steuerungsbasierten Richtlinien deaktivieren.

>[!IMPORTANT]
> Das Deaktivieren von Richtlinien, die die Sicherheit Ihrer Benutzer erzwingen, wenn auch nur vorübergehend, reduziert Ihren Sicherheitsstatus, während der Notfallplan in Kraft ist.

* Konfigurieren Sie einen Satz von Fallbackrichtlinien für den Fall, dass eine Unterbrechung in einem Anmeldeinformationentyp auftritt oder ein Zugriffssteuerungsmechanismus den Zugriff auf Ihre Apps beeinträchtigt. Konfigurieren Sie als Sicherung für eine aktive Richtlinie, die einen MFA-Drittanbieter erfordert, eine Richtlinie im reinen Berichtsmodus, die als Steuerelement den Domänenbeitritt voraussetzt.
* Reduzieren Sie das Risiko, dass Hacker Kennwörter erraten, wenn MFA nicht erforderlich ist, anhand der im Whitepaper [Password Guidance (Kennwortleitfaden)](https://aka.ms/passwordguidance) beschriebenen Methoden.
* Stellen Sie [Azure AD-Self-Service-Kennwortzurücksetzung](./tutorial-enable-sspr.md) und [Azure AD-Kennwortschutz](./howto-password-ban-bad-on-premises-deploy.md) bereit, um sicherzustellen, dass Benutzer keine gängigen Kennwörter und Begriffe verwenden, die Sie sperren möchten.
* Verwenden Sie Richtlinien, die den Zugriff innerhalb der Apps beschränken, wenn eine bestimmte Authentifizierungsebene nicht erreicht wird, statt eines einfachen Zurücksetzens auf Vollzugriff. Beispiel:
  * Konfigurieren Sie eine Sicherungsrichtlinie, die den eingeschränkten Sitzungsanspruch auf Exchange und SharePoint sendet.
  * Wenn Ihre Organisation Microsoft Cloud App Security verwendet, ziehen Sie ein Fallback auf eine Richtlinie in Betracht, die MCAS aktiviert, und dann erlaubt MCAS schreibgeschützten Zugriff, jedoch kein Hochladen.
* Benennen Sie Ihre Richtlinien so, dass Sie sie bei einer Unterbrechung problemlos wiederfinden. Der Richtlinienname muss die folgenden Elemente enthalten:
  * Eine *Bezeichnungsnummer* für die Richtlinie.
  * Anzuzeigender Text, diese Richtlinie dient nur für Notfälle. Beispiel: **IM NOTFALL AKTIVIEREN**
  * Die *Unterbrechung*, für die sie gilt. Beispiel: **Bei MFA-Unterbrechung**
  * Eine *Sequenznummer*, die angibt, in welcher Reihenfolge die Richtlinien aktiviert werden müssen.
  * Die *Apps*, für die sie gilt.
  * Die *Steuerelemente*, für die sie gilt.
  * Die *Bedingungen*, die sie erfordert.
  
Dieser Benennungsstandard für Notfallplanrichtlinien ist wie folgt: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Im folgenden Beispiel: **Beispiel A: Notfallplan-CA-Richtlinie, um den Zugriff auf unternehmenskritische Zusammenarbeits-Apps wiederherzustellen**, ist ein typischer Unternehmensnotfallplan. In diesem Szenario verlangt die Organisation in der Regel MFA für den gesamten Exchange Online- und SharePoint Online-Zugriff, und die Unterbrechung ist in diesem Fall ein Ausfall des MFA-Anbieters des Kunden (Azure AD MFA, lokaler MFA-Anbieter oder Drittanbieter-MFA). Diese Richtlinie entschärft diesen Ausfall, indem bestimmten Benutzern der Zugriff auf diese Apps über vertrauenswürdige Windows-Geräte nur dann ermöglicht wird, wenn sie über ihr vertrauenswürdiges Unternehmensnetzwerk auf die App zugreifen. Sie schließt auch Notfallkonten und Core-Administratoren von diesen Einschränkungen aus. Die Zielbenutzer erhalten dann Zugriff auf Exchange Online und SharePoint Online, während andere Benutzer aufgrund des Ausfalls weiterhin keinen Zugriff auf die Apps haben. Dieses Beispiel setzt einen benannten Netzwerkstandort **CorpNetwork** und eine Sicherheitsgruppe **ContingencyAccess** mit den Zielbenutzern, eine Gruppe mit dem Namen **CoreAdmins**, die die Core-Administratoren enthält, und eine Gruppe namens **EmergencyAccess** mit den Notfallzugriffs-Konten voraus. Der Notfallplan erfordert, dass vier Richtlinien den gewünschten Zugriff bieten. 

**Beispiel A: Notfallplan-CA-Richtlinien, um den Zugriff auf unternehmenskritische Zusammenarbeits-Apps wiederherzustellen:**

* Richtlinie 1: In die Domäne eingebundene Geräte für Exchange und SharePoint voraussetzen.
  * Name: EM001 – IM NOTFALL AKTIVIEREN: MFA-Unterbrechung[1/4] – Exchange SharePoint – Azure AD Hybrid Join erforderlich
  * Benutzer und Gruppen: ContingencyAccess einbeziehen. CoreAdmins und EmergencyAccess ausschließen.
  * Cloud-Apps: Exchange Online und SharePoint Online
  * Bedingungen: Any
  * Steuerelement zur Rechteerteilung: Einbindung in Domäne voraussetzen.
  * Status: Nur Bericht
* Richtlinie 2: Andere Plattformen als Windows blockieren.
  * Name: EM002 – IM NOTFALL AKTIVIEREN: MFA-Unterbrechung[2/4] – Exchange SharePoint – Zugriff blockieren außer Windows
  * Benutzer und Gruppen: Alle Benutzer einbeziehen. CoreAdmins und EmergencyAccess ausschließen.
  * Cloud-Apps: Exchange Online und SharePoint Online
  * Bedingungen: Als Geräteplattform alle Plattformen einbeziehen, Windows ausschließen.
  * Steuerelement zur Rechteerteilung: Block
  * Status: Nur Bericht
* Richtlinie 3: Andere Netzwerke als CorpNetwork blockieren.
  * Name: EM003 – IM NOTFALL AKTIVIEREN: MFA-Unterbrechung[3/4] – Exchange SharePoint – Zugriff blockieren außer Unternehmensnetzwerk
  * Benutzer und Gruppen: Alle Benutzer einbeziehen. CoreAdmins und EmergencyAccess ausschließen.
  * Cloud-Apps: Exchange Online und SharePoint Online
  * Bedingungen: Als Standorte alle Standorte einbeziehen, CorpNetwork ausschließen.
  * Steuerelement zur Rechteerteilung: Block
  * Status: Nur Bericht
* Richtlinie 4: EAS explizit blockieren.
  * Name: EM004 – IM NOTFALL AKTIVIEREN: MFA-Unterbrechung [4/4] – Exchange – EAS für alle Benutzer blockieren
  * Benutzer und Gruppen: Alle Benutzer einbeziehen.
  * Cloud-Apps: Exchange Online einbeziehen.
  * Bedingungen: Client-Apps: Exchange Active Sync
  * Steuerelement zur Rechteerteilung: Block
  * Status: Nur Bericht

Reihenfolge der Aktivierung:

1. ContingencyAccess, CoreAdmins und EmergencyAccess aus der vorhandenen MFA-Richtlinie ausschließen. Stellen Sie sicher, dass ein Benutzer in ContingencyAccess auf SharePoint Online und Exchange Online zugreifen kann.
2. Richtlinie 1 aktivieren: Stellen Sie sicher, dass Benutzer auf in die Domäne eingebundenen Geräten, die nicht zu den ausgeschlossenen Gruppen gehören, auf Exchange Online und SharePoint Online zugreifen können. Stellen Sie sicher, dass Benutzer in der ausgeschlossenen Gruppe auf einem beliebigen Gerät auf SharePoint Online und Exchange zugreifen können.
3. Richtlinie 2 aktivieren: Stellen Sie sicher, dass Benutzer, die nicht zu der ausgeschlossenen Gruppe gehören, nicht von ihren mobilen Geräten aus auf SharePoint Online und Exchange Online zugreifen können. Stellen Sie sicher, dass Benutzer in der ausgeschlossenen Gruppe auf einem beliebigen Gerät (Windows/iOS/Android) auf SharePoint und Exchange zugreifen können.
4. Richtlinie 3 aktivieren: Stellen Sie sicher, dass Benutzer, die nicht zu den ausgeschlossenen Gruppen gehören, nicht von außerhalb des Unternehmensnetzwerks auf SharePoint und Exchange zugreifen können, auch nicht mit einem in die Domäne eingebundenen Computer. Stellen Sie sicher, dass Benutzer in der ausgeschlossenen Gruppe von einem beliebigen Netzwerk aus auf SharePoint Online und Exchange zugreifen können.
5. Richtlinie 4 aktivieren: Stellen Sie sicher, dass alle Benutzer nicht von nativen Mailanwendungen auf mobilen Geräten aus auf Exchange Online zugreifen können.
6. Deaktivieren Sie die vorhandene MFA-Richtlinie für SharePoint Online und Exchange Online.

Im nächsten Beispiel, **Beispiel B: Notfallplan-CA-Richtlinien, um den mobilen Zugriff auf Salesforce zu gewähren**, wird der Zugriff auf eine Geschäftsanwendung wiederhergestellt. In diesem Szenario benötigt der Kunde in der Regel den Vertriebsmitarbeiterzugriff auf Salesforce (konfiguriert für das einmalige Anmelden mit Azure AD) von mobilen Geräten aus, der nur von konformen Geräten aus gewährt wird. Die Unterbrechung ist in diesem Fall ein Problem bei der Auswertung der Gerätekonformität, und der Ausfall geschieht zu einem kritischen Zeitpunkt, zu dem das Vertriebsteam Zugriff auf Salesforce benötigt, um Aufträge abzuschließen. Diese Notfallplanrichtlinien gewähren wichtigen Benutzern Zugriff auf Salesforce von einem mobilen Gerät aus, damit sie weiterhin Aufträge abschließen können und der Geschäftsbetrieb nicht unterbrochen wird. In diesem Beispiel enthält **SalesforceContingency** alle Vertriebsmitarbeiter, die Zugriff benötigen, und **SalesAdmins** enthält die erforderlichen Salesforce-Administratoren.

**Beispiel B: Notfallplan-CA-Richtlinien:**

* Richtlinie 1: Alle blockieren, die nicht zum SalesContingency-Team gehören.
  * Name: EM001 – IM NOTFALL AKTIVIEREN: Unterbrechung der Gerätecompliance[1/2] – Salesforce – Alle Benutzer außer SalesforceContingency blockieren
  * Benutzer und Gruppen: Alle Benutzer einbeziehen. SalesAdmins und SalesforceContingency ausschließen.
  * Cloud-Apps: Salesforce.
  * Bedingungen: Keine
  * Steuerelement zur Rechteerteilung: Block
  * Status: Nur Bericht
* Richtlinie 2: Blockieren Sie das Vertriebsteam von jeder beliebigen anderen Plattform als mobilen Geräten aus (zur Reduzierung der Angriffsfläche).
  * Name: EM002 – IM NOTFALL AKTIVIEREN: Unterbrechung der Gerätecompliance[2/2] – Salesforce – Alle Plattformen außer iOS und Android blockieren
  * Benutzer und Gruppen: SalesforceContingency einbeziehen. SalesAdmins ausschließen.
  * Cloud-Apps: Salesforce
  * Bedingungen: Als Geräteplattform alle Plattformen einbeziehen, iOS und Android ausschließen.
  * Steuerelement zur Rechteerteilung: Block
  * Status: Nur Bericht

Reihenfolge der Aktivierung:

1. Schließen Sie SalesAdmins und SalesforceContingency aus der bereits vorhandenen Gerätekonformitätsrichtlinie für Salesforce aus. Stellen Sie sicher, dass ein Benutzer in der Gruppe SalesforceContingency auf Salesforce zugreifen kann.
2. Richtlinie 1 aktivieren: Stellen Sie sicher, dass Benutzer außerhalb von SalesContingency nicht auf Salesforce zugreifen können. Stellen Sie sicher, dass Benutzer in SalesAdmins und SalesforceContingency auf Salesforce zugreifen können.
3. Richtlinie 2 aktivieren: Stellen Sie sicher, dass Benutzer in der Gruppe SalesContingency nicht mit ihren Windows-/Mac-Laptops auf Salesforce zugreifen können, aber weiterhin über ihre mobilen Geräte Zugriff haben. Stellen Sie sicher, dass SalesAdmin immer noch von einem beliebigen Gerät aus auf Salesforce zugreifen kann.
4. Deaktivieren Sie die vorhandene Gerätekonformitätsrichtlinie für Salesforce.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Notfallpläne für Benutzersperren bei lokalen Ressourcen (NPS-Erweiterung)

Wenn Sie den VPN-Zugriff mit der NPS-Erweiterung für Azure AD MFA schützen, sollten Sie für Ihre VPN-Lösung den Verbund als [SAML-App](../manage-apps/view-applications-portal.md) in Betracht ziehen und die App-Kategorie gemäß den folgenden Empfehlungen ermitteln. 

Wenn Sie die NPS-Erweiterung für Azure AD MFA bereitgestellt haben, um lokale Ressourcen wie VPN und Remotedesktopgateway mit MFA zu schützen, sollten Sie im Voraus berücksichtigen, ob Sie bereit sind, die MFA im Notfall zu deaktivieren.

In diesem Fall können Sie die NPS-Erweiterung deaktivieren, wodurch der NPS-Server nur die primäre Authentifizierung überprüft und keine MFA für die Benutzer erzwingt.

Deaktivieren der NPS-Erweiterung: 
-   Exportieren Sie den Registrierungsschlüssel HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters als Sicherung. 
-   Löschen Sie die Registrierungswerte für „AuthorizationDLLs“ und „ExtensionDLLs", nicht den Parameterschlüssel. 
-   Starten Sie den Netzwerkrichtliniendienst (IAS) neu, damit die Änderungen wirksam werden. 
-   Stellen Sie fest, ob die primäre VPN-Authentifizierung erfolgreich ist.

Nachdem der Dienst wieder hergestellt wurde und Sie bereit sind, die MFA für Ihre Benutzer erneut zu erzwingen, aktivieren Sie die NPS-Erweiterung: 
-   Importieren Sie den Registrierungsschlüssel HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters aus der Sicherungskopie. 
-   Starten Sie den Netzwerkrichtliniendienst (IAS) neu, damit die Änderungen wirksam werden. 
-   Stellen Sie fest, ob die primäre und sekundäre Authentifizierung für VPN erfolgreich sind.
-   Überprüfen Sie den NPS-Server und das VPN-Protokoll, um zu ermitteln, welche Benutzer sich im Notfallzeitraum angemeldet haben.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Stellen Sie die Kennworthash-Synchronisierung auch dann bereit, wenn Sie zu einem Verbund gehören oder Pass-Through-Authentifizierung verwenden.

Eine Benutzersperre kann auch auftreten, wenn die folgenden Bedingungen erfüllt sind:

- Ihre Organisation verwendet eine Hybrididentitätslösung mit Pass-Through-Authentifizierung oder Verbund.
- Ihre lokalen Identitätsysteme (z.B. Active Directory, AD FS oder eine abhängige Komponente) sind nicht verfügbar. 
 
Für höhere Stabilität sollte Ihre Organisation [Kennworthash-Synchronisierung aktivieren](../hybrid/choose-ad-authn.md), da Sie zur [Verwendung der Kennworthash-Synchronisierung wechseln](../hybrid/plan-connect-user-signin.md) können, wenn Ihre lokalen Identitätssysteme nicht verfügbar sind.

#### <a name="microsoft-recommendations"></a>Empfehlungen von Microsoft
 Aktivieren Sie die Kennworthash-Synchronisierung mit dem Azure AD Connect-Assistenten unabhängig davon, ob Ihre Organisation Verbund oder Pass-Through-Authentifizierung verwendet.

>[!IMPORTANT]
> Es ist nicht erforderlich, Benutzer vom Verbund zur verwalteten Authentifizierung zu konvertieren, um Kennworthash-Synchronisierung zu verwenden.

## <a name="during-a-disruption"></a>Während einer Unterbrechung

Wenn Sie entschieden haben, einen Entschärfungsplan zur implementieren, können Sie eine Unterbrechung der einzelnen Zugriffssteuerung automatisch überstehen. Aber wenn Sie entschieden haben, einen Notfallplan zu erstellen, können Sie Ihre Notfallplanrichtlinien während der Unterbrechung der Zugriffssteuerung aktivieren:

1. Aktivieren Sie Ihre Notfallplanrichtlinien, die Zielbenutzern den Zugriff auf bestimmte Apps aus bestimmten Netzwerken gewähren.
2. Deaktivieren Sie Ihre regulären steuerungsbasierten Richtlinien.

### <a name="microsoft-recommendations"></a>Empfehlungen von Microsoft

Je nachdem, welche Entschärfungen oder Notfallpläne bei einer Unterbrechung verwendet werden, kann Ihre Organisation Zugriff nur mit Kennwörtern gewähren. Keinen Schutz zu haben ist ein erhebliches Sicherheitsrisiko, das sorgfältig abgewogen werden muss. Organisationen müssen Folgendes tun:

1. Als Teil Ihrer Strategie zum Ändern der Steuerung dokumentieren Sie alle Änderungen und den vorherigen Status, damit Sie Notfallpläne, die Sie implementiert haben, zurücksetzen können, sobald die Zugriffssteuerungen wieder voll funktionstüchtig sind.
2. Gehen Sie davon aus, dass böswillige Akteure versuchen werden, Kennwörter über Kennwort-Spray- oder Phishing-Angriffe zu sammeln, während Sie MFA deaktiviert haben. Böswillige Akteure könnten möglicherweise auch bereits über Kennwörter verfügen, die zuvor keinen Zugriff auf Ressourcen zuließen, aber in diesem Zeitfenster ausprobiert werden könnten. Sie können dieses Risiko für kritische Benutzer wie Führungskräfte teilweise verringern, indem Sie deren Kennwörter zurücksetzen, bevor Sie MFA für Sie deaktivieren.
3. Archivieren Sie alle Anmeldeaktivitäten, um feststellen zu können, wer während des Zeitraums, in dem MFA deaktiviert war, auf was zugegriffen hat.
4. [Selektieren Sie alle gemeldeten Risikoerkennungen](../reports-monitoring/concept-sign-ins.md) in diesem Zeitfenster erfolgt sind.

## <a name="after-a-disruption"></a>Nach einer Unterbrechung

Machen Sie die Änderungen, die Sie als Teil des aktivierten Notfallplans vorgenommen haben, rückgängig, sobald der Dienst wiederhergestellt ist, der die Unterbrechung verursacht hat. 

1. Aktivieren der normalen Richtlinien
2. Deaktivieren Sie die Notfallplanrichtlinien, und stellen Sie den reinen Berichtsmodus wieder her. 
3. Setzen Sie alle anderen Änderungen zurück, die Sie während der Unterbrechung vorgenommen und dokumentiert haben.
4. Wenn Sie ein Konto für den Notfallzugriff verwendet haben, müssen Sie die Anmeldeinformationen neu generieren und die Details der neuen Anmeldeinformationen physisch als Teil Ihrer Notfallzugriffskonto-Verfahren sichern.
5. Setzen Sie nach der Unterbrechung die [Selektierung aller gemeldeten Risikoerkennungen](../reports-monitoring/concept-sign-ins.md) nach verdächtigen Aktivitäten fort.
6. Widerrufen Sie alle Aktualisierungstoken, die [mithilfe von PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) für eine Gruppe von Benutzern ausgegeben wurden. Das Widerrufen aller Aktualisierungstoken ist wichtig für privilegierte Konten, die während der Unterbrechung verwendet wurden, und so wird erzwungen, dass sie sich erneut authentifizieren und die Steuerungsanforderungen der wiederhergestellten Richtlinien erfüllen.

## <a name="emergency-options"></a>Notfalloptionen

 Wenn ein Notfall auftritt, und Ihre Organisation noch keine Entschärfung oder keinen Notfallplan implementiert hat, befolgen Sie die Empfehlungen im Abschnitt [Notfallpläne für Benutzersperre](#contingencies-for-user-lockout), wenn bereits die Richtlinien für bedingten Zugriff zum Erzwingen von MFA verwendet werden.
Wenn Ihre Organisation ältere, pro Benutzer geltende MFA-Richtlinien verwendet, können Sie die folgende Alternative erwägen:

1. Wenn Sie über die ausgehende IP-Adresse des Unternehmensnetzwerks verfügen, können Sie sie zum Aktivieren der ausschließlichen Authentifizierung bei dem Unternehmensnetzwerk als vertrauenswürdige IP-Adresse hinzufügen.
   1. Wenn Sie nicht über ausgehende IP-Adressen verfügen oder Sie das Aktivieren des Zugriffs innerhalb und außerhalb des Unternehmensnetzwerks benötigten, können Sie den gesamten IPv4-Adressraum als vertrauenswürdige IP-Adressen hinzufügen, indem Sie 0.0.0.0/1 und 128.0.0.0/1 angeben.

>[!IMPORTANT]
 > Wenn Sie die vertrauenswürdigen IP-Adressen erweitern, um die Blockierung des Zugriffs aufzuheben, werden keine Risikoerkennungen im Zusammenhang mit IP-Adressen (z. B. unmöglicher Ortswechsel oder unbekannte Orte) generiert.

>[!NOTE]
 > Das Konfigurieren von [vertrauenswürdigen IP-Adressen](./howto-mfa-mfasettings.md) für Azure AD MFA ist nur mit [Azure AD Premium-Lizenzen](./concept-mfa-licensing.md) verfügbar.

## <a name="learn-more"></a>Weitere Informationen

* [Dokumentation zur Azure AD-Authentifizierung](./howto-mfaserver-iis.md)
* [Verwalten von Administratorkonten für den Notfallzugriff in Azure AD](../roles/security-emergency-access.md)
* [Konfigurieren benannter Orte in Azure Active Directory](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings)
* [Konfigurieren von in Azure Active Directory eingebundenen Hybridgeräten](../devices/hybrid-azuread-join-plan.md)
* [Windows Hello for Business– Bereitstellungshandbuch](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Password Guidance (Kennwortleitfaden) – Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Was sind Bedingungen beim bedingten Zugriff in Azure Active Directory?](../conditional-access/concept-conditional-access-conditions.md)
* [Was sind die Zugriffssteuerungen beim bedingten Zugriff mit Azure Active Directory?](../conditional-access/controls.md)
* [Was ist der reine Berichtsmodus des bedingten Zugriffs?](../conditional-access/concept-conditional-access-report-only.md)
