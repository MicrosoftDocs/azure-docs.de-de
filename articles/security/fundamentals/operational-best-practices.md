---
title: Bewährte Methoden für die Sicherheit Ihrer Azure-Ressourcen
titleSuffix: Azure security
description: Dieser Artikel enthält eine Reihe von bewährten Methoden für den Schutz Ihrer Daten, Anwendungen und anderen Ressourcen in Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 86874a60d48ddcbdaca5ae779ad554ee58cc233b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498845"
---
# <a name="azure-operational-security-best-practices"></a>Bewährte Methoden für Azure Operational Security
Dieser Artikel enthält eine Reihe von bewährten Methoden für den Schutz Ihrer Daten, Anwendungen und anderen Ressourcen in Azure.

Die bewährten Methoden basieren auf einer gemeinsamen Linie und eignen sich für aktuelle Funktionen und Features der Azure-Plattform. Meinungen und Technologien ändern sich im Laufe der Zeit. Dieser Artikel wird daher regelmäßig aktualisiert, um diese Änderungen widerzuspiegeln.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definieren und Bereitstellen von Methoden für hohe Betriebssicherheit
Azure Operational Security bezieht sich auf die Dienste, Steuerelemente und Features, die Benutzern zum Schützen ihrer Daten, Anwendungen und anderen Ressourcen in Azure zur Verfügung stehen. Azure Operational Security basiert auf einem Framework, das Erkenntnisse verwendet, die aus einzigartigen Microsoft-Funktionen stammen, u.a. aus dem [Microsoft Security Development Lifecycle](https://www.microsoft.com/sdl), dem [Microsoft Security Response Center-Programm](https://www.microsoft.com/msrc?rtc=1) und den umfassenden Informationen zur Bedrohungslage hinsichtlich der Sicherheit im Internet.

## <a name="manage-and-monitor-user-passwords"></a>Verwalten und Überwachen von Benutzerkennwörtern
Die folgende Tabelle enthält einige bewährte Methoden in Bezug auf die Verwaltung von Benutzerkennwörtern:

**Bewährte Methode**: Stellen Sie sicher, dass Sie über den richtigen Grad an Kennwortschutz in der Cloud verfügen.   
**Detail**: Befolgen Sie die Anleitungen im [Microsoft-Kennwortleitfaden](https://www.microsoft.com/research/publication/password-guidance/), der für Benutzer der Microsoft Identity Platform gilt (Azure Active Directory, Active Directory und Microsoft-Konto).

**Bewährte Methode**: Führen Sie eine Überwachung auf verdächtige Aktionen im Zusammenhang mit Ihren Benutzerkonten durch.   
**Detail**: Führen Sie eine Überwachung auf [gefährdete Benutzer](../../active-directory/identity-protection/overview-identity-protection.md) und [riskante Anmeldungen](../../active-directory/identity-protection/overview-identity-protection.md) mithilfe von Azure AD-Sicherheitsberichten durch.

**Bewährte Methode**: Lassen Sie Kennwörter mit hohem Risiko automatisch erkennen und beheben.   
**Detail**: [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) ist ein Feature der Azure AD Premium P2-Edition, das Ihnen Folgendes ermöglicht:

- Ermitteln potenzieller Sicherheitsrisiken für Identitäten Ihrer Organisation
- Konfigurieren automatischer Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit den Identitäten Ihrer Organisation
- Untersuchen verdächtiger Incidents und Ergreifen entsprechender Maßnahmen zu deren Behebung

## <a name="receive-incident-notifications-from-microsoft"></a>Empfangen von Benachrichtigungen über Incidents von Microsoft
Achten Sie darauf, dass Ihr Security Operations-Team Benachrichtigungen über Azure-Incidents von Microsoft erhält. Durch eine solche Benachrichtigung erfährt Ihr Sicherheitsteam, dass Azure-Ressourcen gefährdet sind, sodass schnell reagiert und potenzielle Sicherheitsrisiken behoben werden können.

Im Azure-Registrierungsportal können Sie sicherstellen, dass die Kontaktinformationen für Administratoren solche Details enthalten, dass Security Operations benachrichtigt wird. Die Kontaktinformationen bestehen aus einer E-Mail-Adresse und einer Telefonnummer.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organisieren von Azure-Abonnements in Verwaltungsgruppen
Wenn Ihre Organisation über viele Abonnements verfügt, benötigen Sie möglicherweise eine Möglichkeit zur effizienten Verwaltung von Zugriff, Richtlinien und Konformität für diese Abonnements. [Azure-Verwaltungsgruppen](../../governance/management-groups/create-management-group-portal.md) stellen einen abonnementübergreifenden Bereich bereit. Sie organisieren Abonnements in Containern, die als Verwaltungsgruppen bezeichnet werden, und wenden Ihre Governancebedingungen auf die Verwaltungsgruppen an. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Verwaltungsgruppe angewendeten Bedingungen.

Sie können eine flexible Struktur von Verwaltungsgruppen und Abonnements in einem Verzeichnis erstellen. Jedes Verzeichnis erhält eine einzelne Verwaltungsgruppe auf oberster Ebene, die als Stammverwaltungsgruppe bezeichnet wird. Die Stammverwaltungsgruppe ist in die Hierarchie integriert, sodass ihr alle Verwaltungsgruppen und Abonnements untergeordnet sind. Die Stammverwaltungsgruppe ermöglicht das Anwenden von globalen Richtlinien und Azure-Rollenzuweisungen auf Verzeichnisebene.

Es folgen einige bewährte Methoden für die Verwendung von Verwaltungsgruppen:

**Bewährte Methode**: Stellen Sie sicher, dass beim Hinzufügen neuer Abonnements Governance-Elemente wie Richtlinien und Berechtigungen angewendet werden.   
**Detail**: Verwenden Sie die Stammverwaltungsgruppe, um unternehmensweite Sicherheitselemente zuzuweisen, die für alle Azure-Ressourcen gelten. Richtlinien und Berechtigungen sind Beispiele für Elemente.

**Bewährte Methode**: Passen Sie die oberen Ebenen der Verwaltungsgruppen an eine Segmentierungsstrategie an, um einen Punkt für Steuerung und Richtlinienkonsistenz innerhalb jedes Segments bereitzustellen.   
**Detail**: Erstellen Sie eine einzelne Verwaltungsgruppe für jedes Segment unter der Stammverwaltungsgruppe. Erstellen Sie keine anderen Verwaltungsgruppen unter der Stammverwaltungsgruppe.

**Bewährte Methode**: Begrenzen Sie die Tiefe der Verwaltungsgruppe, um Verwirrung zu vermeiden, die sowohl den Betrieb als auch die Sicherheit beeinträchtigt.   
**Detail**: Begrenzen Sie die Hierarchie auf drei Ebenen, einschließlich der Stammverwaltungsgruppe.

**Bewährte Methode**: Wählen Sie sorgfältig aus, welche Elemente mit der Stammverwaltungsgruppe auf das gesamte Unternehmen angewendet werden.   
**Detail**: Stellen Sie sicher, dass Elemente der Stammverwaltungsgruppe eindeutig für jede Ressource angewendet werden müssen und nur geringe Auswirkungen haben.

Geeignet sind:

- Gesetzliche Anforderungen, die eindeutige geschäftliche Auswirkungen haben (z.B. Einschränkungen im Zusammenhang mit der Datenhoheit)
- Anforderungen mit nahezu keinen potenziellen negativen Auswirkungen auf den Betrieb, wie Richtlinien mit der Auswirkung „audit“ oder Zuweisungen von Azure RBAC-Berechtigungen, die sorgfältig geprüft wurden

**Bewährte Methode**: Planen und testen Sie alle unternehmensweiten Änderungen an der Stammverwaltungsgruppe vor der Anwendung sehr sorgfältig (Richtlinie, Azure RBAC-Modell usw.).   
**Detail**: Änderungen in der Stammverwaltungsgruppe können sich auf jede Ressource in Azure auswirken. Obwohl sie eine leistungsstarke Möglichkeit bieten, Konsistenz im gesamten Unternehmen zu gewährleisten, können sich Fehler oder eine falsche Verwendung negativ auf Produktionsvorgänge auswirken. Testen Sie alle Änderungen an der Stammverwaltungsgruppe in einer Testumgebung oder einem Pilotbereich für die Produktion.

## <a name="streamline-environment-creation-with-blueprints"></a>Optimieren der Umgebungserstellung mit Blueprints
Der [Azure Blueprints](../../governance/blueprints/overview.md)-Dienst ermöglicht es Cloudarchitekten und zentralen IT-Gruppen, eine wiederholbare Gruppe von Azure-Ressourcen zu definieren, mit der die Standards, Muster und Anforderungen einer Organisation implementiert und erzwungen werden. Mit Azure Blueprints können Entwicklungsteams schnell neue Umgebungen mit einer Reihe integrierter Komponenten bereitstellen und einrichten und dabei darauf vertrauen, dass sie diese Umgebungen entsprechend den Konformitätsanforderungen der Organisation erstellen.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Überwachen von Speicherdiensten auf unerwartete Änderungen des Verhaltens
Das Diagnostizieren und Behandeln von Problemen kann in einer verteilten Anwendung, die in einer Cloudumgebung gehostet wird, komplexer sein als in herkömmlichen Umgebungen. Anwendungen können in einer PaaS- oder IaaS-Infrastruktur, lokal, auf einem mobilen Gerät oder in einer Kombination dieser Umgebungen bereitgestellt werden. Der Netzwerkdatenverkehr Ihrer Anwendung fließt möglicherweise durch öffentliche und private Netzwerke, und Ihre Anwendung verwendet möglicherweise mehrere Speichertechnologien.

Es wird empfohlen, die von Ihrer Anwendung verwendeten Speicherdienste fortlaufend zu überwachen, um unerwartete Änderungen am Verhalten zu erkennen (z.B. eine langsamere Reaktionszeit). Erfassen Sie mit der Protokollierung umfangreichere Daten, anhand derer Sie ein Problem genauer analysieren können. Die Diagnoseinformationen, die Sie sowohl aus der Überwachung als auch der Protokollierung erhalten, helfen Ihnen, die Ursache des Problems, das bei Ihrer Anwendung auftritt, zu bestimmen. Dann können Sie das Problem behandeln und die entsprechenden Schritte zur Beseitigung bestimmen.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) führt die Protokollierung durch und stellt Metrikdaten für ein Azure-Speicherkonto bereit. Es wird empfohlen, mit diesen Daten Anforderungen zu verfolgen, Verwendungstrends zu analysieren und Probleme mit dem Speicherkonto zu diagnostizieren.

## <a name="prevent-detect-and-respond-to-threats"></a>Verhindern, Erkennen und Reagieren auf Bedrohungen
[Azure Security Center](../../security-center/security-center-introduction.md) unterstützt Sie durch mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen dabei, Bedrohungen zu erkennen, zu verhindern und zu beheben. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt blieben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

Der Free-Tarif des Security Center bietet eingeschränkte Sicherheit nur für Ihre Azure-Ressourcen. Im Standard-Tarif ist auch die Sicherheit für lokale Ressourcen und Cloudressourcen enthalten. Security Center Standard hilft Ihnen, Sicherheitsrisiken zu finden und zu beseitigen, Zugriffs- und Anwendungssteuerungen anzuwenden, um böswillige Aktivitäten zu blockieren, Bedrohungen mithilfe von Analysen und intelligenter Funktionen zu erkennen und bei Angriffen schnell zu reagieren. Sie können Security Center Standard die ersten 60 Tage kostenlos testen. Es wird empfohlen, ein [Upgrade Ihres Azure-Abonnements auf Security Center Standard durchzuführen](../../security-center/security-center-get-started.md).

Im Security Center können Sie sich den Sicherheitsstatus Ihrer gesamten Azure-Ressourcen in einer zentralen Übersicht ansehen. Erfahren Sie auf einen Blick, ob die erforderlichen Sicherheitskontrollfunktionen implementiert und ordnungsgemäß konfiguriert sind, und ermitteln Sie schnell Ressourcen, die Ihre Aufmerksamkeit erfordern.

Security Center bietet auch Integration von [Microsoft Defender Advanced Threat Protection (ATP)](../../security-center/security-center-wdatp.md), die umfassende Funktionen für Endpoint Detection and Response (EDR) bereitstellt. Durch die ATP-Integration in Microsoft Defender können Sie Anomalien erkennen. Sie können auch erweiterte Angriffe auf Serverendpunkte, die von Security Center überwacht werden, erkennen und darauf reagieren.

Fast alle Unternehmen verfügen über ein SIEM-System (Security Information and Event Management) zur Identifizierung aufkommender Bedrohungen durch Konsolidierung von Protokollinformationen, die von verschiedenen Geräten zur Signalerfassung stammen. Die Protokolle werden dann von einem Datenanalysesystem analysiert, um zwischen Stördatenverkehr, der bei allen Protokollsammlungs- und Analyselösungen unvermeidlich ist, und „interessanten“ Daten zu unterscheiden.

[Azure Sentinel](../../sentinel/overview.md) ist eine skalierbare, cloudnative Lösung für Security Information and Event Management (SIEM) und die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Azure Sentinel bietet intelligente Sicherheits- und Bedrohungsanalysen über Warnungs- und Bedrohungserkennung, proaktive Suche und automatische Reaktion auf Bedrohungen.

Es folgen einige bewährte Methoden zum Verhindern, Erkennen und Reagieren auf Bedrohungen:

**Bewährte Methode**: Steigern Sie die Geschwindigkeit und Skalierbarkeit Ihrer SIEM-Lösung durch Verwendung von cloudbasiertem SIEM.   
**Detail**: Untersuchen Sie die Features und Funktionen von [Azure Sentinel](../../sentinel/overview.md), und vergleichen Sie diese mit den Funktionen der derzeit von Ihnen lokal verwendeten Lösung. Erwägen Sie den Einsatz von Azure Sentinel, wenn damit die SIEM-Anforderungen Ihrer Organisation erfüllt werden.

**Bewährte Methode**: Ermitteln Sie die schwerwiegendsten Sicherheitsrisiken, damit Sie die Untersuchung priorisieren können.   
**Detail**: Überprüfen Sie Ihren [Azure Secure Score](../../security-center/secure-score-security-controls.md), um die Empfehlungen zu sehen, die sich aus den in Azure Security Center integrierten Azure-Richtlinien und -Initiativen ergeben. Mithilfe dieser Empfehlungen können die wichtigsten Risiken wie Sicherheitsupdates, Endpunktschutz, Verschlüsselung, Sicherheitskonfigurationen, fehlende WAF, VMs mit Internetzugriff und vieles mehr angegangen werden.

Anhand der Sicherheitsbewertung, die auf CIS-Steuerelementen (Center for Internet Security) basiert, können Sie ein Benchmarking für die Azure-Sicherheit Ihrer Organisation gegenüber externen Quellen durchführen. Mithilfe einer externen Überprüfung können Sie die Sicherheitsstrategie Ihres Teams überprüfen und erweitern.

**Bewährte Methode**: Überwachen Sie den Sicherheitsstatus von Computern, Netzwerken, Speicher- und Datendiensten sowie Anwendungen, um potenzielle Sicherheitsprobleme aufzudecken und zu priorisieren.  
**Detail**: Befolgen Sie die [Sicherheitsempfehlungen](../../security-center/security-center-recommendations.md) in Security Center, beginnend mit den Elementen mit der höchsten Priorität.

**Bewährte Methode**: Integrieren Sie Security Center-Warnungen in Ihre SIEM-Lösung (Security Information and Event Management).   
**Detail**: Die meisten Organisationen mit einer SIEM-Lösung verwenden diese als ein zentrales Clearinghouse für Sicherheitswarnungen, die eine Analystenantwort erfordern. Von Security Center erzeugte verarbeitete Ereignisse werden im Azure-Aktivitätsprotokoll veröffentlicht, einem der über Azure Monitor verfügbaren Protokolle. Azure Monitor bietet eine konsolidierte Pipeline zum Routing beliebiger Überwachungsdaten zu einem SIEM-Tool. Eine Anleitung finden Sie unter [Streamen von Warnungen in eine SIEM-, SOAR- oder IT Service Management-Lösung](../../security-center/export-to-siem.md). Wenn Sie Azure Sentinel verwenden, finden Sie Informationen unter [Herstellen einer Verbindung mit Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Bewährte Methode**: Integrieren von Azure-Protokollen in Ihr SIEM-System.   
**Detail**: Verwenden Sie [Azure Monitor zum Sammeln und Exportieren von Daten](../../azure-monitor/overview.md#integrate-and-export-data). Diese Vorgehensweise ist wichtig, damit Sicherheitsincidents untersucht werden können. Außerdem ist die Aufbewahrung von Onlineprotokollen beschränkt. Wenn Sie Azure Sentinel verwenden, finden Sie Informationen unter [Herstellen einer Verbindung mit Datenquellen](../../sentinel/connect-data-sources.md).

**Bewährte Methode**: Beschleunigen Sie Ihre Untersuchungs- und Ermittlungsprozesse, und verringern Sie falsch positive Ergebnisse durch Integration von Funktionen für Endpoint Detection and Response (EDR) in Ihre Untersuchung von Angriffen.   
**Detail**: [Aktivieren Sie die Integration von Microsoft Defender für den Endpunkt](../../security-center/security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration) über Ihre Security Center-Sicherheitsrichtlinie. Ziehen Sie die Verwendung von Azure Sentinel zur Ermittlung von Bedrohungen und für die Reaktion auf Incidents in Betracht.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Szenariobasierte End-to-End-Netzwerküberwachung
Kunden erstellen ein End-to-End-Netzwerk in Azure, indem sie verschiedene Netzwerkressourcen wie z.B. virtuelle Netzwerke, ExpressRoute, Application Gateway und Load Balancer miteinander kombinieren. Überwachung steht für jede der Netzwerkressourcen zur Verfügung.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) ist ein regionaler Dienst. Mit diesem Dienst können Sie Bedingungen auf Netzwerkebene in Azure überwachen und diagnostizieren.

Im Folgenden sind bewährte Methoden für die Netzwerküberwachung und verfügbare Tools aufgelistet.

**Bewährte Methode**: Automatisieren der Remotenetzwerküberwachung per Paketerfassung.  
**Detail**: Überwachen und diagnostizieren Sie mithilfe von Network Watcher Netzwerkprobleme, ohne sich bei Ihren virtuellen Computern anmelden zu müssen. Lösen Sie mithilfe von Warnungen die [Paketerfassung](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) aus, und erhalten Sie Zugriff auf Leistungsinformationen in Echtzeit auf Paketebene. Wenn Sie ein Problem feststellen, können Sie dieses detailliert untersuchen, um es besser diagnostizieren zu können.

**Bewährte Methode**: Gewinnen von Einblicken in den Netzwerkdatenverkehr mithilfe von Datenflussprotokollen.  
**Detail**: Lernen Sie mithilfe von [Datenflussprotokollen von Netzwerksicherheitsgruppen](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) die Datenverkehrsmuster in Ihrem Netzwerk besser kennen. Mit den Daten aus den Datenflussprotokollen können Sie Erkenntnisse gewinnen, die Sie zum Erfüllen von Konformitätsanforderungen und zum Überwachen Ihres Netzwerksicherheitsprofils benötigen.

**Bewährte Methode**: Diagnostizieren von VPN-Konnektivitätsproblemen.  
**Detail**: Verwenden Sie Network Watcher, um die [am häufigsten auftretenden VPN Gateway- und Verbindungsprobleme zu diagnostizieren](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Sie können nicht nur das Problem identifizieren, sondern auch umfangreiche Protokolle verwenden, um das Problem ausführlich zu untersuchen.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Sichern der Bereitstellung mithilfe von bewährten DevOps-Tools
Mit den folgenden bewährten DevOps-Methoden können Sie sicherstellen, dass Ihr Unternehmen und Team produktiv und effizient arbeiten.

**Bewährte Methode**: Automatisieren der Erstellung und Bereitstellung von Diensten.  
**Detail**: [Infrastruktur als Code](/azure/devops/learn/what-is-infrastructure-as-code) sind Techniken und Methoden, die für IT-Experten die tägliche Erstellung und Verwaltung einer modularen Infrastruktur vereinfachen. IT-Experten können damit ihre moderne Serverumgebung so erstellen und verwalten, wie Softwareentwickler Anwendungscode erstellen und verwalten.

Mit dem [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) können Sie Ihre Anwendungen mit einer deklarativen Vorlage bereitstellen. In einer Vorlage können Sie mehrere Dienste zusammen mit ihren Abhängigkeiten bereitstellen. Sie verwenden die gleiche Vorlage, um Ihre Anwendung in jeder Phase des Anwendungslebenszyklus wiederholt bereitzustellen.

**Bewährte Methode**: Automatisches Erstellen und Bereitstellen von Azure-Web-Apps oder -Clouddiensten.  
**Detail**: Sie können Azure DevOps Projects für [automatisches Erstellen und Bereitstellen](/azure/devops/pipelines/index) in Azure-Web-Apps oder -Clouddiensten konfigurieren. Azure DevOps stellt die Binärdateien nach der Durchführung eines Builds in Azure nach jedem Einchecken von Code automatisch bereit. Der Prozess der Paketerstellung entspricht dem Befehl „Packen“ in Visual Studio, und die Veröffentlichungsschritte entsprechen dem Befehl „Veröffentlichen“ in Visual Studio.

**Bewährte Methode**: Automatisieren der Releaseverwaltung.  
**Detail**: [Azure Pipelines](/azure/devops/pipelines/index) ist eine Lösung für die Automatisierung einer mehrstufigen Bereitstellung und die Verwaltung des Releaseprozesses. Erstellen Sie verwaltete Continuous Deployment-Pipelines für schnelle, einfache und häufige Releases. Mit Azure Pipelines können Sie den Releaseprozess automatisieren und vordefinierte Genehmigungsworkflows einrichten. Stellen Sie lokal und in der Cloud bereit, und führen Sie nach Bedarf Erweiterungen und Anpassungen durch.

**Bewährte Methode**: Testen Sie die Leistung Ihrer Web-App, bevor Sie sie starten oder Updates für die Produktion bereitstellen.  
**Detail**: Führen Sie cloudbasierte [Auslastungstest](/azure/devops/test/load-test/overview#alternatives) für folgende Zwecke durch:

- Leistungsprobleme in Ihrer Web-App finden.
- Qualität der Bereitstellung verbessern.
- Verfügbarkeit Ihrer App sicherstellen.
- Stellen Sie sicher, dass Ihre App den Datenverkehr für die nächste Produkteinführung oder Marketingkampagne bewältigen kann.

[Apache JMeter](https://jmeter.apache.org/) ist ein kostenloses und beliebtes Open-Source-Tool mit starker Community-Unterstützung.

**Bewährte Methode**: Überwachen der Anwendungsleistung.  
**Detail**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) auf mehreren Plattformen. Sie können mit Application Insights Ihre Live-Web-App überwachen. Der Dienst erkennt automatisch Leistungsanomalien. Er verfügt über Analysetools, mit denen Sie Probleme diagnostizieren und nachvollziehen können, wie Ihre App von Benutzern verwendet wird. Der Dienst unterstützt Sie bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit Ihrer App.

## <a name="mitigate-and-protect-against-ddos"></a>Abwenden von und Schützen vor DDoS
Bei einem DDoS-Angriff versucht ein Angreifer, Anwendungsressourcen zu erschöpfen. Die Verfügbarkeit der Anwendung und ihre Fähigkeit, legitime Anforderungen zu verarbeiten, sollen beeinträchtigt werden. Diese Angriffe werden komplexer, ihr Ausmaß größer und ihre Auswirkungen gravierender. Jeder Endpunkt, der öffentlich über das Internet erreichbar ist, kann Ziel von DDoS-Angriffen werden.

Konzeptionen für Resilienz gegenüber DDoS-Angriffen setzen Planung und Entwürfe für eine Vielzahl von Fehlerzuständen voraus. Im Folgenden sind bewährte Methoden zum Erstellen von Anwendungen in Azure aufgelistet, die DDoS-Angriffen standhalten können.

**Bewährte Methode**: Stellen Sie sicher, dass die Sicherheit im gesamten Lebenszyklus einer Anwendung Priorität hat – von Entwurf und Implementierung bis hin zu Bereitstellung und Betrieb. Anwendungen können Fehler enthalten, die einer relativ geringen Anzahl von Anforderungen die Verwendung von vielen Ressourcen erlauben. Dies führt zu einem Dienstausfall.  
**Detail**: Um einen in Microsoft Azure ausgeführten Dienst zu schützen, sollten Sie Ihre Anwendungsarchitektur verstehen und sich auf die [five pillars of software quality (Fünf Säulen der Softwarequalität)](/azure/architecture/guide/pillars) konzentrieren. Sie sollten Folgendes kennen: das typische Datenverkehrsvolumen, das Modell der Konnektivität zwischen der Anwendung und anderen Anwendungen, sowie die Dienstendpunkte, die dem öffentlichen Internet verfügbar gemacht werden.

Es ist von größter Wichtigkeit sicherzustellen, dass eine Anwendung stabil genug ist, um mit einem Denial-of-Service-Angriff fertig zu werden. Sicherheit und Datenschutz sind direkt in der Azure-Plattform integriert, beginnend mit [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). Die SDL spricht Sicherheit in jeder Entwicklungsphase an und sorgt dafür, dass Azure kontinuierlich aktualisiert wird, um noch sicherer zu sein.

**Bewährte Methode**: Entwerfen der Anwendungen für eine [horizontale Skalierung](/azure/architecture/guide/design-principles/scale-out), um die Anforderungen einer verstärkten Auslastung zu erfüllen – insbesondere im Falle eines DDoS-Angriffs. Wenn Ihre Anwendung von einer einzelnen Instanz eines Diensts abhängig ist, entsteht dadurch ein Single Point of Failure. Durch Bereitstellen mehrerer Instanzen wird Ihr System stabiler und besser skalierbar.  
**Detail**: Wählen Sie für [Azure App Service](../../app-service/overview.md) einen [App Service-Plan](../../app-service/overview-hosting-plans.md) aus, der mehrere Instanzen bietet.

Konfigurieren Sie für Azure Cloud Services alle Rollen so, dass sie [mehrere Instanzen](../../cloud-services/cloud-services-choose-me.md) verwenden.

Stellen Sie für [Azure-VMS](../../virtual-machines/windows/overview.md) sicher, dass die VM-Architektur mehr als eine VM enthält und dass jede VM zu einer [Verfügbarkeitsgruppe](../../virtual-machines/windows/tutorial-availability-sets.md) gehört. Sie sollten VM-Skalierungsgruppen für Funktionen zur automatischen Skalierung verwenden.

**Bewährte Methode**: Abwehrmaßnahmen in die Schichten einer Anwendung zu integrieren reduziert die Wahrscheinlichkeit eines erfolgreichen Angriffs. Implementieren Sie sichere Entwürfe für Ihre Anwendungen mithilfe der integrierten Funktionen der Azure-Plattform.  
**Detail**: Das Angriffsrisiko steigt mit der Größe (Oberflächenbereich) der Anwendung. Sie können die Oberfläche durch Verwendung einer Genehmigungsliste verringern, um den verfügbar gemachten IP-Adressraum und die Überwachungsports zu schließen, die im Lastenausgleichsmodul ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) und [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)) nicht mehr benötigt werden.

Durch [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md) kann die angreifbare Oberfläche ebenfalls reduziert werden. Sie können mit [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) und [Anwendungssicherheitsgruppen](../../virtual-network/network-security-groups-overview.md#application-security-groups) das Erstellen von Sicherheitsregeln weniger komplex machen und Netzwerksicherheit als natürliche Erweiterung der Struktur einer Anwendung konfigurieren.

Sie sollten Azure-Dienste nach Möglichkeit in einem [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) bereitstellen. Mit dieser Methode können Dienstressourcen über private IP-Adressen kommunizieren. Standardmäßig werden für Datenverkehr von Azure-Diensten aus einem virtuellen Netzwerk öffentliche IP-Adressen als Quell-IP-Adressen verwendet.

Bei Verwendung von [Dienstendpunkten](../../virtual-network/virtual-network-service-endpoints-overview.md) wechselt der Dienstdatenverkehr zu privaten Adressen im virtuellen Netzwerk als Quell-IP-Adressen, wenn aus einem virtuellen Netzwerk auf den Azure-Dienst zugegriffen wird.

Häufig werden lokale Ressourcen von Kunden zusammen mit ihren Azure-Ressourcen angegriffen. Wenn Sie die Verbindung einer lokalen Umgebung mit Azure herstellen, machen Sie lokale Ressourcen so wenig wie möglich im öffentlichen Internet verfügbar.

Azure bietet zwei [DDoS-Dienstangebote](../../ddos-protection/ddos-protection-overview.md), die vor Netzwerkangriffen schützen:

- Basic-Schutz ist standardmäßig und ohne zusätzliche Kosten in der Azure-Plattform integriert. Die Skalierung und Kapazität des global bereitgestellten Azure-Netzwerks bietet Schutz vor häufigen Vermittlungsschichtangriffen durch Always On-Datenverkehrsüberwachung und Risikominderung in Echtzeit. Für den Basic-Schutz sind keine weiteren Konfigurationen oder Anwendungsänderungen von Seiten des Benutzers erforderlich. Mit diesem Schutz können Sie alle Azure-Dienste schützen, u.a. PaaS-Dienste wie Azure DNS.
- Der Standard-Schutz bietet erweiterte DDoS-Abwehrfunktionen gegen Netzwerkangriffe. Es wird automatisch optimiert, um Ihre spezifischen Azure-Ressourcen zu schützen. Der Schutz lässt sich einfach während der Erstellung virtueller Netzwerke aktivieren. Dies ist auch nach der Erstellung möglich und erfordert keine Änderungen der Anwendung oder Ressourcen.

## <a name="enable-azure-policy"></a>Aktivieren von Azure Policy
[Azure Policy](../../governance/policy/overview.md) ist ein Dienst in Azure, mit dem Sie Richtlinien erstellen, zuweisen und verwalten können. Mit diesen Richtlinien werden Regeln und Auswirkungen für Ihre Ressourcen erzwungen, damit diese stets mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel konform bleiben. Azure Policy erfüllt diese Anforderung, indem Ihre Ressourcen auf die Nichteinhaltung der zugewiesenen Richtlinien überprüft werden.

Aktivieren Sie Azure Policy zum Überwachen und Erzwingen der schriftlichen Richtlinie Ihrer Organisation. Dadurch wird die Einhaltung unternehmensspezifischer oder gesetzlicher Sicherheitsvorschriften sichergestellt, indem Sicherheitsrichtlinien für alle Hybridcloud-Workloads zentral verwaltet werden. Erfahren Sie mehr über das [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../../governance/policy/tutorials/create-and-manage.md). Eine Übersicht über die Elemente einer Richtlinie finden Sie unter [Struktur von Azure Policy-Definitionen](../../governance/policy/concepts/definition-structure.md).

Es folgen einige bewährte Sicherheitsmethoden, die nach der Aktivierung von Azure Policy zu befolgen sind:

**Bewährte Methode**: Policy unterstützt verschiedene Arten von Auswirkungen. Informationen dazu finden Sie unter [Struktur von Azure Policy-Definitionen](../../governance/policy/concepts/definition-structure.md#policy-rule). Geschäftsvorgänge können durch die Auswirkungen **deny** und **remediate** negativ beeinflusst werden. Beginnen Sie deshalb mit der Auswirkung **audit**, um das Risiko einer negativen Beeinflussung durch die Richtlinie zu begrenzen.   
**Detail**: [Beginnen Sie Richtlinienbereitstellungen im Überwachungsmodus (audit)](../../governance/policy/concepts/definition-structure.md#policy-rule), und fahren Sie dann später mit **deny** oder **remediate** fort. Testen und überprüfen Sie die Ergebnisse der Auswirkung „audit“, bevor Sie mit **deny** oder **remediate** fortfahren.

Weitere Informationen finden Sie unter [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../../governance/policy/tutorials/create-and-manage.md).

**Bewährte Methode**: Identifizieren Sie die Rollen, die dafür verantwortlich sind, dass Richtlinienverletzungen überwacht werden und sichergestellt ist, dass die richtige Korrekturaktion schnell ausgeführt wird.   
**Detail**: Lassen Sie über das [Azure-Portal](../../governance/policy/how-to/get-compliance-data.md#portal) oder über die [Befehlszeile](../../governance/policy/how-to/get-compliance-data.md#command-line) die zugewiesene Rolle die Einhaltung überwachen.

**Bewährte Methode**: Azure Policy ist eine technische Darstellung der schriftlichen Richtlinien einer Organisation. Ordnen Sie alle Azure Policy-Definitionen Organisationsrichtlinien zu, um Verwirrung zu vermeiden und die Konsistenz zu steigern.   
**Detail**: Dokumentieren Sie die Zuordnung in der Dokumentation Ihrer Organisation oder direkt in der Azure Policy-Definition, indem Sie einen Verweis auf die Organisationsrichtlinie in der [Richtliniendefinition](../../governance/policy/concepts/definition-structure.md#display-name-and-description) oder der Beschreibung der [Initiativendefinition](../../governance/policy/concepts/initiative-definition-structure.md#metadata) hinzufügen.

## <a name="monitor-azure-ad-risk-reports"></a>Überwachen der Azure AD-Risikoberichte
Die allermeisten Sicherheitsverletzungen kommen vor, wenn Angreifer Zugriff auf eine Umgebung erhalten, indem sie die Identität eines Benutzers stehlen. Die Ermittlung von kompromittierten Identitäten ist keine einfache Aufgabe. Azure AD verwendet adaptive Machine Learning-Algorithmen und -Heuristiken, um verdächtige Aktivitäten im Zusammenhang mit Ihren Benutzerkonten zu erkennen. Jede erkannte verdächtige Aktion wird in einem Datensatz gespeichert, der als [Risikoerkennung](../../active-directory/identity-protection/overview-identity-protection.md) bezeichnet wird. Risikoerkennungen werden in Azure AD-Sicherheitsberichten erfasst. Weitere Informationen finden Sie unter [Bericht „Benutzer mit Risikomarkierung“](../../active-directory/identity-protection/overview-identity-protection.md) und unter [Bericht „Riskante Anmeldungen“](../../active-directory/identity-protection/overview-identity-protection.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere bewährte Methoden für die Sicherheit, die Sie beim Entwerfen, Bereitstellen und Verwalten Ihrer Cloudlösungen mithilfe von Azure verwenden können, finden Sie unter [Sicherheit in Azure: bewährte Methoden und Muster](best-practices-and-patterns.md).

Die folgenden Ressourcen enthalten allgemeinere Informationen zur Sicherheit in Azure und verwandten Microsoft-Diensten:
* [Blog des Azure-Sicherheitsteams](/archive/blogs/azuresecurity/): Hier finden Sie Informationen über den aktuellen Stand der Azure-Sicherheit.
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): Hier können Sie Microsoft-Sicherheitsrisiken, z.B. Probleme mit Azure, melden oder eine E-Mail an secure@microsoft.com schreiben.