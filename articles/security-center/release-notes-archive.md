---
title: Archiv der Neuerungen in Azure Security Center
description: Enthält eine Beschreibung der Neuerungen und Änderungen in Azure Security Center, die älter als sechs Monate sind.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2020
ms.author: memildin
ms.openlocfilehash: 854926c64b50cf4b8e7df9fa82da58b924ddbd83
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510419"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archiv der Neuerungen in Azure Security Center

Die Hauptseite mit Versionshinweisen zu [Neuerungen in Azure Security Center](release-notes.md) enthält Aktualisierungen aus den letzten sechs Monaten. Ältere Einträge finden Sie hingegen auf dieser Seite.

Auf dieser Seite finden Sie folgende Informationen:

- Neue Funktionen
- Behebung von Programmfehlern
- Veraltete Funktionen


## <a name="june-2020"></a>Juni 2020

Zu den Updates im Juni gehören:
- [Sicherheitsbewertungs-API (Vorschau)](#secure-score-api-preview)
- [Erweiterte Datensicherheit für SQL-Computer (Azure, andere Clouds und lokal) (Vorschau)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Zwei neue Empfehlungen zum Bereitstellen des Log Analytics-Agent auf Azure Arc-Computern (Vorschau)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Neue Richtlinien zum Erstellen von Konfigurationen für fortlaufenden Export und Workflowautomatisierung im großen Stil](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Neue Empfehlung zum Verwenden von NSGs zum Schützen von virtuellen Computern ohne Internetzugriff](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Neue Richtlinien zum Aktivieren von Bedrohungsschutz und erweiterter Datensicherheit](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Sicherheitsbewertungs-API (Vorschau)

Sie können jetzt auf Ihre Bewertung über die [Sicherheitsbewertungs-API](/rest/api/securitycenter/securescores/) (derzeit in der Vorschau) zugreifen. Die API-Methoden bieten die Flexibilität, die Daten abzufragen und im Laufe der Zeit einen eigenen Berichtsmechanismus für Ihre Sicherheitsbewertungen zu erstellen. Beispielsweise können Sie die API **Sicherheitsbewertungen** verwenden, um die Bewertung für ein bestimmtes Abonnement zu erhalten. Darüber hinaus können Sie die API **Sicherheitsbewertungs-Steuerelemente** verwenden, um die Sicherheitssteuerelemente und die aktuelle Bewertung Ihrer Abonnements aufzulisten.

Beispiele für externe Tools, die mit der Sicherheitsbewertungs-API verwendet werden können, finden Sie im [Bereich zu den Sicherheitsbewertungen in unserer GitHub-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Weitere Informationen finden Sie unter [Erweiterter Secure Score (Vorschau) in Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Erweiterte Datensicherheit für SQL-Computer (Azure, andere Clouds und lokal) (Vorschau)

Advanced Data Security für SQL-Computer im Azure Security Center schützt jetzt in Azure, in anderen Cloudumgebungen und sogar auf lokalen Computern gehostete SQL-Server-Instanzen. Dies erweitert den Schutz für Ihre nativen Azure-SQL Server-Instanzen, um vollständige Unterstützung für Hybridumgebungen bereitzustellen.

Erweiterte Datensicherheit bietet eine Sicherheitsrisikobewertung und erweiterten Bedrohungsschutz für Ihre SQL-Computer, wo immer sie sich befinden.

Das Setup umfasst zwei Schritte:

1. Stellen Sie den Log Analytics-Agent auf dem Hostcomputer Ihrer SQL Server-Instanz bereit, um die Verbindung mit dem Azure-Konto herzustellen.

1. Aktivieren Sie das optionale Paket auf der Security Center-Seite „Preise und Einstellungen“.

Weitere Informationen finden Sie unter [Erweiterte Datensicherheit für SQL Server-Instanzen in Azure Virtual Machines (Vorschau)](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Zwei neue Empfehlungen zum Bereitstellen des Log Analytics-Agent auf Azure Arc-Computern (Vorschau)

Es wurden zwei neue Empfehlungen hinzugefügt, die Ihnen helfen, den [Log Analytics-Agent](../azure-monitor/platform/log-analytics-agent.md) auf Ihren Azure Arc-Computern bereitzustellen und sicherzustellen, dass sie durch Azure Security Center geschützt sind:

- **Log Analytics-Agent muss auf Ihren Windows-basierten Azure Arc-Computern installiert sein (Vorschau)**
- **Log Analytics-Agent muss auf Ihren Linux-basierten Azure Arc-Computern installiert sein (Vorschau)**

Diese neuen Empfehlungen werden in denselben vier Sicherheitssteuerelementen angezeigt wie die vorhandene (verwandte) Empfehlung, **Monitoring Agent sollte auf ihren Computern installiert werden**: Sicherheitskonfigurationen reparieren, adaptive Anwendungssteuerung anwenden, Systemupdates anwenden und Endpunktschutz aktivieren.

Die Empfehlungen umfassen auch die Schnellkorrekturfunktion, um den Bereitstellungsprozess zu beschleunigen. 

Weitere Informationen zu diesen beiden neuen Empfehlungen finden Sie in der Tabelle [Compute- und App-Empfehlungen](recommendations-reference.md#recs-computeapp).

In [Was ist der Log Analytics-Agent?](faq-data-collection-agents.md#what-is-the-log-analytics-agent) erfahren Sie mehr darüber, wie Azure Security Center den Agent verwendet.

Erfahren Sie mehr über [Erweiterungen für Azure Arc-Computer](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Neue Richtlinien zum Erstellen von Konfigurationen für fortlaufenden Export und Workflowautomatisierung im großen Stil

Die Automatisierung der Prozesse Ihrer Organisation zur Überwachung und Reaktion auf Vorfälle kann die Zeit, die zum Untersuchen von Sicherheitsvorfällen und zur Durchführung entsprechender Gegenmaßnahmen benötigt wird, erheblich verkürzen.

Zum Bereitstellen Ihrer Automatisierungskonfigurationen in Ihrer Organisation verwenden Sie diese integrierten „DeployIfdNotExist“-Azure-Richtlinien zum Erstellen und Konfigurieren von Prozeduren für [fortlaufenden Export](continuous-export.md) und [Workflowautomatisierung](workflow-automation.md):

Die Richtlinien finden Sie in der Azure-Richtlinie:


|Zielsetzung  |Richtlinie  |Richtlinien-ID  |
|---------|---------|---------|
|Fortlaufender Export zu einem Event Hub|[Bereitstellen eines Exports für Azure Security Center-Warnungen und -Empfehlungen in Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Fortlaufender Export in einen Log Analytics-Arbeitsbereich|[Bereitstellen eines Exports für Azure Security Center-Warnungen und -Empfehlungen in Log Analytics-Arbeitsbereichen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Workflowautomatisierung für Sicherheitswarnungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Workflowautomatisierung für Sicherheitsempfehlungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Empfehlungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Beginnen Sie mit [Vorlagen zur Workflowautomatisierung](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Weitere Informationen über die Verwendung der beiden Exportrichtlinien finden Sie unter [Konfigurieren der Workflowautomatisierung in großem Stile mit Hilfe der bereitgestellten Richtlinien](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) und [Einrichten eines fortlaufenden Exports](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Neue Empfehlung zum Verwenden von NSGs zum Schützen von virtuellen Computern ohne Internetzugriff

Das Sicherheitssteuerelement „Bewährte Sicherheitsmethoden implementieren“ enthält jetzt die folgende neue Empfehlung:

- **Virtuelle Computer ohne Internetzugang sollten über Netzwerksicherheitsgruppen geschützt werden.**

Eine vorhandene Empfehlung **VMs mit Internetzugang sollten mithilfe von Netzwerksicherheitsgruppen geschützt werden** unterschied nicht zwischen virtuellen Computern mit und ohne Internetzugriff. Für beide wurde eine Empfehlung mit hohem Schweregrad generiert, wenn eine VM keiner Netzwerksicherheitsgruppe zugewiesen war. Diese neue Empfehlung separiert die Computer ohne Internetzugriff, um falsch positive Ergebnisse zu reduzieren und unnötige Warnungen mit hohem Schweregrad zu vermeiden.

Weitere Informationen finden Sie in der Tabelle [Netzwerkempfehlungen](recommendations-reference.md#recs-network).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Neue Richtlinien zum Aktivieren von Bedrohungsschutz und erweiterter Datensicherheit

Die folgenden neuen Richtlinien wurden der ASC-Standardinitiative hinzugefügt und dienen zur Unterstützung beim Aktivieren von Bedrohungsschutz oder erweiterter Datensicherheit für die relevanten Ressourcentypen.

Die Richtlinien finden Sie in der Azure-Richtlinie:


| Richtlinie                                                                                                                                                                                                                                                                | Richtlinien-ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Für Azure SQL-Datenbank-Server muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Advanced Data Security muss für SQL Server-Instanzen auf Computern aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection muss für Azure Storage-Konten aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection muss in Azure Key Vault-Instanzen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection muss in Azure App Service-Plänen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Advanced Threat Protection muss in Azure Container Registry-Instanzen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Advanced Threat Protection muss in Azure Kubernetes Service-Clustern aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cdq1-3 23-492b5a539-13118b6d1e3a |
| [Advanced Threat Protection muss für Virtual Machines aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Erfahren Sie mehr über [Bedrohungsschutz in Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Mai 2020

Zu den Updates im Mai gehören:
- [Regeln zur Warnungsunterdrückung (Vorschau)](#alert-suppression-rules-preview)
- [Sicherheitsrisikobewertung für virtuelle Computer ist jetzt allgemein verfügbar](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Änderungen am JIT-VM-Zugriff (Just-In-Time)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Benutzerdefinierte Empfehlungen wurden in ein separates Sicherheitssteuerelement verschoben](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Umschalter zum Anzeigen von Empfehlungen in Steuerelementen oder als flache Liste hinzugefügt](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Erweitertes Sicherheitssteuerelement „Bewährte Sicherheitsmethoden implementieren“](#expanded-security-control-implement-security-best-practices)
- [Benutzerdefinierte Richtlinien mit benutzerdefinierten Metadaten sind nun allgemein verfügbar](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migration der Funktionen für die Absturzabbildanalyse zur Erkennung dateiloser Angriffe](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regeln zur Warnungsunterdrückung (Vorschau)

Dieses neue Feature (derzeit in der Vorschauphase) trägt zur Reduzierung der „Warnungsmüdigkeit“ bei. Verwenden Sie Regeln, damit Warnungen, die auf harmlose Fehler hinweisen oder sich auf normale Aktivitäten Ihrer Organisation beziehen, automatisch ausgeblendet werden. Auf diese Weise können Sie sich auf die relevantesten Bedrohungen konzentrieren. 

Warnungen, die den aktivierten Unterdrückungsregeln entsprechen, werden zwar weiterhin generiert, aber ihr Status lautet „Geschlossen“. Der Status wird im Azure-Portal bzw. an dem Ort angezeigt, über den Sie auf die Security Center-Sicherheitswarnungen zugreifen.

Mit Unterdrückungsregeln werden die Kriterien definiert, nach denen Warnungen automatisch geschlossen werden sollen. Normalerweise verwenden Sie in folgenden Fällen eine Unterdrückungsregel:

- Unterdrücken von Warnungen, die Sie als falsch positiv identifiziert haben

- Unterdrücken von Warnungen, die zu oft ausgelöst werden und daher nicht nützlich sind

Weitere Informationen finden Sie unter [Unterdrücken von Warnungen aus dem Threat Protection-Modul von Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Sicherheitsrisikobewertung für virtuelle Computer ist jetzt allgemein verfügbar

Der Standardtarif von Security Center enthält jetzt eine integrierte Sicherheitsrisikobewertung, für die keine zusätzlichen Gebühren anfallen. Diese Erweiterung basiert auf Qualys, aber die Ergebnisse werden direkt an Security Center gemeldet. Sie benötigen keine Qualys-Lizenz und auch kein Qualys-Konto – alles erfolgt nahtlos innerhalb von Security Center.

Mit der neuen Lösung können Ihre virtuellen Computer fortlaufend gescannt werden, um Sicherheitsrisiken zu ermitteln, und die Ergebnisse werden in Security Center bereitgestellt. 

Verwenden Sie zum Bereitstellen der Lösung die neue Sicherheitsempfehlung:

„Integrierte Lösung zur Sicherheitsrisikobewertung (unterstützt von Qualys) auf virtuellen Computern aktivieren“

Informieren Sie sich über die [integrierte Sicherheitsrisikobewertung für virtuelle Computer von Security Center](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Änderungen am JIT-VM-Zugriff (Just-In-Time)

Security Center enthält ein optionales Feature zum Schützen der Verwaltungsports Ihrer virtuellen Computer. Dies ist eine Verteidigungsmaßnahme gegen die häufigste Form von Brute-Force-Angriffen.

Mit diesem Update werden die folgenden Änderungen an diesem Feature vorgenommen:

- Die Empfehlung, in der Ihnen zur JIT-Aktivierung auf einer VM geraten wird, wurde umbenannt. „Just-In-Time-Netzwerkzugriffssteuerung auf virtuelle Computer anwenden“ wurde geändert in: „Verwaltungsports virtueller Computer müssen mit der Just-In-Time-Netzwerkzugriffssteuerung geschützt werden“.

- Die Empfehlung wird nur ausgelöst, wenn offene Verwaltungsports vorhanden sind.

Erfahren Sie mehr zum [Feature für JIT-Zugriff](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Benutzerdefinierte Empfehlungen wurden in ein separates Sicherheitssteuerelement verschoben

Eine Sicherheitssteuerung, die im Rahmen der erweiterten Sicherheitsbewertung eingeführt wurde, ist „Best Practices für die Sicherheit implementieren“. Alle benutzerdefinierten Empfehlungen, die für Ihre Abonnements erstellt wurden, wurden automatisch in dieses Steuerelement eingefügt. 

Um Ihnen die Suche nach Ihren benutzerdefinierten Empfehlungen zu erleichtern, haben wir diese in das dedizierte Sicherheitssteuerelement „Benutzerdefinierte Empfehlungen“ verschoben. Dieses Steuerelement wirkt sich nicht auf Ihre Sicherheitsbewertung aus.

Weitere Informationen zu Sicherheitssteuerelementen finden Sie unter [Erweiterte Sicherheitsbewertung (Vorschau) in Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Umschalter zum Anzeigen von Empfehlungen in Steuerelementen oder als flache Liste hinzugefügt

Bei Sicherheitssteuerelementen handelt es sich um logische Gruppen mit zusammengehörigen Sicherheitsempfehlungen. Diese spiegeln Ihre anfälligen Angriffsflächen wider. Eine Sicherheitskontrolle umfasst eine Reihe von Sicherheitsempfehlungen mit Anweisungen, mit denen Sie diese Empfehlungen implementieren können.

Um sofort zu sehen, wie gut jede einzelne Angriffsfläche in Ihrer Organisation geschützt ist, sehen Sie sich die Bewertungen für die einzelnen Sicherheitskontrollen an.

Ihre Empfehlungen werden standardmäßig in den Sicherheitssteuerelementen angezeigt. Ab diesem Update können Sie sie auch als Liste anzeigen. Verwenden Sie den neuen Umschalter, mit dem Sie „Nach Steuerelementen gruppieren“ können, um sie als einfache Liste anzuzeigen, die nach dem Integritätsstatus der betroffenen Ressourcen sortiert ist. Der Umschalter befindet sich oberhalb der Liste im Portal.

Die Sicherheitssteuerelemente – und dieser Umschalter – sind Teil der neuen Benutzeroberfläche für die Sicherheitsbewertung. Denken Sie daran, uns über das Portal Ihr Feedback hierzu zu senden.

Weitere Informationen zu Sicherheitssteuerelementen finden Sie unter [Erweiterte Sicherheitsbewertung (Vorschau) in Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Umschalter „Nach Kontrollen gruppieren“ für Empfehlungen":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Erweitertes Sicherheitssteuerelement „Bewährte Sicherheitsmethoden implementieren“ 

Eine Sicherheitssteuerung, die im Rahmen der erweiterten Sicherheitsbewertung eingeführt wurde, ist „Best Practices für die Sicherheit implementieren“. Wenn eine Empfehlung in diesem Steuerelement angeordnet ist, wirkt sie sich nicht auf die Sicherheitsbewertung aus. 

Mit diesem Update wurden drei Empfehlungen aus den Steuerelementen, in denen sie ursprünglich angeordnet waren, in dieses Steuerelement für die bewährte Vorgehensweise verschoben. Wir haben dies durchgeführt, weil wir festgestellt haben, dass das Risiko bei diesen drei Empfehlungen niedriger als anfänglich gedacht ist.

Außerdem wurden zwei neue Empfehlungen eingeführt und diesem Steuerelement hinzugefügt.

Die folgenden drei Empfehlungen wurden verschoben:

- **Für Konten mit Leseberechtigungen für Ihr Abonnement muss MFA aktiviert sein** (ursprünglich im Steuerelement „MFA aktivieren“ enthalten)
- **Externe Konten mit Leseberechtigungen müssen aus Ihrem Abonnement entfernt werden** (ursprünglich im Steuerelement „Zugriff und Berechtigungen verwalten“ enthalten)
- **Für Ihr Abonnement dürfen maximal 3 Besitzer festgelegt werden** (ursprünglich im Steuerelement „Zugriff und Berechtigungen verwalten“ enthalten)

Die beiden folgenden neuen Empfehlungen wurden dem Steuerelement hinzugefügt:

- **Die Erweiterung Guest Configuration sollte auf Windows-VMs installiert sein (Vorschau)** : [Guest Configuration von Azure Policy](../governance/policy/concepts/guest-configuration.md) ermöglicht auf virtuellen Computern einen Einblick in Server- und Anwendungseinstellungen (nur Windows).

- **Windows Defender Exploit Guard sollte auf Ihren Computern aktiviert sein (Vorschau)** : Für Windows Defender Exploit Guard wird der Guest Configuration-Agent von Azure Policy genutzt. Exploit Guard verfügt über vier Komponenten für die Absicherung von Geräten gegen viele verschiedene Angriffsvektoren und Blockierungsverhalten, mit denen bei Angriffen mit Schadsoftware häufig zu rechnen ist. Darüber hinaus ermöglichen diese Komponenten es Unternehmen, die richtige Balance zwischen Sicherheitsrisiken und Produktivitätsanforderungen zu finden (nur Windows).

Weitere Informationen zu Windows Defender Exploit Guard finden Sie unter [Erstellen und Bereitstellen einer Exploit Guard-Richtlinie](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Weitere Informationen zu Sicherheitssteuerelementen finden Sie unter [Erweiterte Sicherheitsbewertung (Vorschau)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Benutzerdefinierte Richtlinien mit benutzerdefinierten Metadaten sind nun allgemein verfügbar

Benutzerdefinierte Richtlinien sind nun Bestandteil der Security Center-Oberfläche mit den Empfehlungen, der Sicherheitsbewertung und des Dashboards mit den Standards zur Einhaltung gesetzlicher Bestimmungen. Dieses Feature ist jetzt allgemein verfügbar und ermöglicht es Ihnen, die Abdeckung in Bezug auf die Sicherheitsbewertung Ihrer Organisation in Security Center zu erweitern. 

Erstellen Sie in Azure Policy eine benutzerdefinierte Initiative, und fügen Sie ihr Richtlinien hinzu. Führen Sie anschließend das Onboarding in Azure Security Center und die Visualisierung in Form von Empfehlungen durch.

Wir haben jetzt auch die Option zum Bearbeiten der benutzerdefinierten Metadaten für die Empfehlungen hinzugefügt. Zu den Metadatenoptionen zählen Schweregrad, Problembehandlungsschritte, Bedrohungsinformationen und mehr.  

Informieren Sie sich über das [Verbessern der benutzerdefinierten Empfehlungen mit ausführlichen Informationen](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migration der Funktionen für die Absturzabbildanalyse zur Erkennung dateiloser Angriffe 

Wir integrieren die Erkennungsfunktionen der Windows-Absturzabbildanalyse mit der [Erkennung dateiloser Angriffe](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Die Analyse zur Erkennung dateiloser Angriffe verfügt über verbesserte Versionen der folgenden Sicherheitswarnungen für Windows-Computer: „Erkennung einer Codeinjektion“, „Erkennung eines Windows-Maskerademoduls“, „Erkennung von Shellcode“ und „Verdächtiges Codesegment erkannt“.

Diese Umstellung hat u. a. folgende Vorteile:

- **Proaktive und rechtzeitige Erkennung von Schadsoftware**: Bei der Absturzabbildanalyse wurde gewartet, bis es zu einem Absturz gekommen ist, und erst dann wurde die Analyse durchgeführt, um bösartige Artefakte zu ermitteln. Bei der Erkennung von dateilosen Angriffen werden In-Memory-Bedrohungen proaktiv während der Ausführung identifiziert. 

- **Erweiterte Warnungen**: Die Sicherheitswarnungen bei der Erkennung von dateilosen Angriffen verfügen über erweiterte Elemente, die bei der Absturzabbildanalyse nicht vorhanden sind, z. B. Informationen zu aktiven Netzwerkverbindungen. 

- **Warnungsaggregation**: Wenn bei der Absturzabbildanalyse in einem Absturzabbild mehrere Angriffsmuster erkannt wurden, wurden mehrere Sicherheitswarnungen ausgelöst. Bei der Erkennung von dateilosen Angriffen werden alle identifizierten Angriffsmuster eines Prozesses in einer Warnung zusammengefasst, damit nicht mehrere Warnungen korreliert werden müssen.

- **Verringerung der Anforderungen in Ihrem Log Analytics-Arbeitsbereich**: Absturzabbilder mit potenziell vertraulichen Daten werden nicht mehr in Ihren Log Analytics-Arbeitsbereich hochgeladen.






## <a name="april-2020"></a>April 2020

Zu den Updates im April gehören:
- [Dynamische Compliancepakete sind jetzt allgemein verfügbar](#dynamic-compliance-packages-are-now-generally-available)
- [Empfehlungen zur Identität sind in Azure Security Center jetzt im Free-Tarif enthalten](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamische Compliancepakete sind jetzt allgemein verfügbar

Das Azure Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen enthält jetzt **dynamische Compliancepakete** (allgemein verfügbar), um weitere branchenbezogene und gesetzliche Standards nachverfolgen zu können.

Dynamische Compliancepakete können über die Security Center-Seite mit den Sicherheitsrichtlinien Ihrem Abonnement oder Ihrer Verwaltungsgruppe hinzugefügt werden. Nachdem Sie das Onboarding für einen Standard oder einen Benchmarkwert durchgeführt haben, wird der Standard in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen mit allen zugeordneten Compliancedaten in Form von Bewertungen angezeigt. Ein zusammenfassender Bericht für alle Standards, für die das Onboarding durchgeführt wurde, wird als Download bereitgestellt.

Sie können nun beispielsweise folgende Standards hinzufügen:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official und UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (neu)** (eine vollständigere Darstellung von Azure CIS 1.1.0)

Zusätzlich haben wir vor Kurzem den **Azure-Sicherheitsvergleichstest** hinzugefügt. Dies sind von Microsoft erstellte Azure-spezifische Richtlinien zu den bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Compliance-Frameworks basieren. Weitere Standards werden im Dashboard unterstützt, sobald sie verfügbar sind.  
 
Erfahren Sie mehr zum [Aktualisieren auf dynamische Compliancepakete in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Empfehlungen zur Identität sind in Azure Security Center jetzt im Free-Tarif enthalten

Sicherheitsempfehlungen zur Identität und zum Zugriff sind im Free-Tarif von Azure Security Center nun allgemein verfügbar. Dies ist Teil der Maßnahme, mit der die Features für die Verwaltung des Cloudsicherheitsstatus kostenlos zugänglich gemacht werden sollen. Bisher waren diese Empfehlungen nur im Standard-Tarif verfügbar.

Beispiele für Empfehlungen zur Identität und zum Zugriff sind:

- „Für Konten mit Besitzerberechtigungen für Ihr Abonnement muss MFA aktiviert sein“
- „Für Ihr Abonnement dürfen maximal 3 Besitzer festgelegt werden“
- „Veraltete Konten müssen aus Ihrem Abonnement entfernt werden“

Bei Abonnements mit Free-Tarif wirkt sich diese Änderung auf deren Sicherheitsbewertungen aus, weil die Sicherheit in Bezug auf die Identität und den Zugriff hierfür bisher noch nicht bewertet wurde.

Weitere Informationen finden Sie in den [Empfehlungen zur Identität und zum Zugriff](recommendations-reference.md#recs-identity).

Erfahren Sie mehr zum [Überwachen der Identität und des Zugriffs](security-center-identity-access.md).



## <a name="march-2020"></a>März 2020

Zu den Updates im März gehören:

- [Workflowautomatisierung ist nun allgemein verfügbar](#workflow-automation-is-now-generally-available)
- [Integration von Azure Security Center mit Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Schutz für Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Verbesserte Just-In-Time-Umgebung](#improved-just-in-time-experience)
- [Zwei Sicherheitsempfehlungen für Webanwendungen als veraltet gekennzeichnet](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Workflowautomatisierung ist nun allgemein verfügbar

Das Feature „Workflowautomatisierung“ von Azure Security Center ist jetzt allgemein verfügbar. Sie können es verwenden, um Logic Apps bei Sicherheitswarnungen und Empfehlungen automatisch auszulösen. Darüber hinaus sind manuelle Trigger für Warnungen und alle Empfehlungen verfügbar, für die die „Schnellkorrektur“ verfügbar ist.

Jedes Sicherheitsprogramm umfasst mehrere Workflows für die Reaktion auf Vorfälle. Diese Prozesse können das Benachrichtigen relevanter Stakeholder, das Starten eines Change Management-Prozesses und das Anwenden spezifischer Korrekturschritte umfassen. Sicherheitsexperten empfehlen, möglichst viele Schritte dieser Verfahren zu automatisieren. Durch die Automatisierung wird der Aufwand reduziert. Außerdem können Sie die Sicherheit erhöhen, indem Sie sicherstellen, dass die Prozessschritte schnell, einheitlich und gemäß Ihren vordefinierten Anforderungen ausgeführt werden.

Weitere Informationen zu den automatischen und manuellen Security Center-Funktionen zum Ausführen von Workflows finden Sie unter [Workflowautomatisierung](workflow-automation.md).

Erfahren Sie mehr zum [Erstellen von Logik-Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integration von Azure Security Center mit Windows Admin Center

Es ist jetzt möglich, Ihre lokalen Windows-Server aus Windows Admin Center direkt in Azure Security Center zu verschieben. Security Center wird somit zu Ihrer zentralen Benutzeroberfläche für die Anzeige von Sicherheitsinformationen für Ihre gesamten Windows Admin Center-Ressourcen, z. B. lokale Server, virtuelle Computer und weitere PaaS-Workloads.

Nach dem Verschieben eines Servers aus Windows Admin Center in Azure Security Center haben Sie folgende Möglichkeiten:

- Anzeigen von Sicherheitswarnungen und -empfehlungen in der Security Center-Erweiterung von Windows Admin Center
- Anzeigen des Sicherheitsstatus und Abrufen weiterer detaillierter Informationen zu Ihren mit Windows Admin Center verwalteten Servern in Security Center im Azure-Portal (oder über eine API)

Erfahren Sie mehr zum [Integrieren von Azure Security Center mit Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Schutz für Azure Kubernetes Service

Für Azure Security Center werden die Containersicherheitsfeatures als Schutz für Azure Kubernetes Service (AKS) erweitert.

Die beliebte Open-Source-Plattform Kubernetes wurde so stark genutzt, dass sie zu einem Branchenstandard für die Containerorchestrierung geworden ist. Trotz dieser weit verbreiteten Implementierung gibt es immer noch Defizite, was das Schützen einer Kubernetes-Umgebung betrifft. Zum Verteidigen der Angriffsflächen einer Containeranwendung sind bestimmte Kenntnisse erforderlich, um sicherstellen zu können, dass die Infrastruktur sicher konfiguriert ist und ständig auf potenzielle Bedrohungen überwacht wird.

Die Verteidigung für Security Center umfasst Folgendes:

- **Ermittlung und Transparenz**: Kontinuierliche Ermittlung von verwalteten AKS-Instanzen in den für Security Center registrierten Abonnements.
- **Sicherheitsempfehlungen**: Direkt umsetzbare Empfehlungen, damit Sie für AKS die bewährten Methoden in Bezug auf die Sicherheit anwenden können. Diese Empfehlungen sind in Ihrer Sicherheitsbewertung enthalten, um dafür zu sorgen, dass sie als Teil des Sicherheitsstatus Ihres Unternehmens angesehen werden. Ein Beispiel für eine Empfehlung mit AKS-Bezug, die ggf. angezeigt wird, ist „Die rollenbasierte Zugriffssteuerung sollte genutzt werden, um den Zugriff auf einen Kubernetes Service-Cluster einzuschränken“.
- **Bedrohungsschutz**: Security Center analysiert Ihre AKS-Bereitstellung ständig und warnt Sie vor Bedrohungen und schädlichen Aktivitäten, die auf dem Host und auf der AKS-Clusterebene erkannt werden.

Erfahren Sie mehr zur [Integration von Security Center in Azure Kubernetes Service](defender-for-kubernetes-introduction.md).

Erfahren Sie mehr zu den [Containersicherheitsfeatures von Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Verbesserte Just-In-Time-Umgebung

Die Features, der Betrieb und die Benutzeroberfläche der Just-In-Time-Tools von Azure Security Center, mit denen Ihre Verwaltungsports geschützt werden, wurden wie folgt verbessert: 

- **Feld für Begründung**: Beim Anfordern des Zugriffs auf einen virtuellen Computer (VM) über die Just-In-Time-Seite des Azure-Portals ist ein neues optionales Feld verfügbar, in dem eine Begründung für die Anforderung eingegeben werden kann. Die in diesem Feld eingegebenen Informationen können im Aktivitätsprotokoll nachverfolgt werden. 
- **Automatische Bereinigung von redundanten Just-In-Time-Regeln (JIT)** : Bei jedem Update einer JIT-Richtlinie wird automatisch ein Bereinigungstool ausgeführt, um die Gültigkeit des gesamten Regelsatzes zu überprüfen. Das Tool sucht nach Konflikten zwischen den Regeln in Ihrer Richtlinie und den Regeln in der NSG. Wenn das Bereinigungstool einen Konflikt feststellt, ermittelt es die Ursache und entfernt integrierte Regeln, die nicht mehr benötigt werden – sofern dies auf sichere Weise möglich ist. Der Cleaner löscht niemals Regeln, die Sie erstellt haben. 

Erfahren Sie mehr zum [Feature für JIT-Zugriff](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Zwei Sicherheitsempfehlungen für Webanwendungen als veraltet gekennzeichnet

Zwei Sicherheitsempfehlungen zu Webanwendungen wurden als veraltet gekennzeichnet: 

- Regeln für Webanwendungen in IaaS-NSGs müssen verstärkt werden.
    (Zugehörige Richtlinie: Für Webanwendungen in IaaS müssen die NSG-Regeln verstärkt werden)

- Zugriff auf App Services muss eingeschränkt sein.
    (Zugehörige Richtlinie: Zugriff auf App Services muss eingeschränkt sein [Vorschau])

Diese Empfehlungen werden nicht mehr in der Security Center-Liste mit den Empfehlungen angezeigt. Die zugehörigen Richtlinien sind nicht mehr in der Initiative mit dem Namen „Security Center-Standardrichtlinie“ enthalten.

Erfahren Sie mehr über [Sicherheitsempfehlungen](recommendations-reference.md).




## <a name="february-2020"></a>Februar 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Erkennung von dateilosen Angriffen für Linux (Vorschau)

Da Angreifer immer häufiger verdeckte Methoden nutzen, um einer Entdeckung zu entgehen, wird die Erkennung von dateilosen Angriffen für Azure Security Center zusätzlich zu Windows auch auf Linux erweitert. Bei dateilosen Angriffen werden Sicherheitsrisiken in der Software ausgenutzt, schädliche Nutzlasten in unkritische Systemprozesse eingeschleust und Angriffe im Arbeitsspeicher verborgen. Diese Vorgehensweise soll Folgendes bewirken:

- Verringerung oder Beseitigung der Spuren von Schadsoftware auf Datenträgern
- Erhebliche Verringerung der Wahrscheinlichkeit, dass Angreifer von datenträgerbasierten Schadsoftware-Scanlösungen erkannt werden

Als Reaktion auf diese Bedrohung wurde die Azure Security Center-Erkennung von dateilosen Angriffen für Windows im Oktober 2018 veröffentlicht und nun auch auf Linux ausgeweitet. 



## <a name="january-2020"></a>Januar 2020

### <a name="enhanced-secure-score-preview"></a>Erweiterte Sicherheitsbewertung (Vorschau)

Eine erweiterte Version der Sicherheitsbewertung von Azure Security Center ist jetzt verfügbar und befindet sich in der Vorschauphase. Bei dieser Version werden mehrere Empfehlungen zu Sicherheitssteuerelementen gruppiert, die Ihre anfälligen Angriffsflächen besser widerspiegeln (z. B. Einschränkung des Zugriffs auf Verwaltungsports).

Machen Sie sich mit den Änderungen vertraut, die während der Vorschauphase für die Sicherheitsbewertung vorgenommen werden, und ermitteln Sie andere Lösungen, mit denen Sie Ihre Umgebung noch besser schützen können.

Erfahren Sie mehr über die [erweiterte Sicherheitsbewertung (Vorschau)](secure-score-security-controls.md).



## <a name="november-2019"></a>November 2019

Updates im November:
 - [Threat Protection für Azure Key Vault in Nordamerika (Vorschau)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Threat Protection für Azure Storage mit zuverlässigem Malwarescreening](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Workflowautomatisierung mit Azure Logic Apps (Vorschau)](#workflow-automation-with-logic-apps-preview)
 - [Ressourcenübergreifende schnelle Problembehebung allgemein verfügbar](#quick-fix-for-bulk-resources-generally-available)
 - [Überprüfen von Containerimages auf Sicherheitsrisiken (Vorschau)](#scan-container-images-for-vulnerabilities-preview)
 - [Zusätzliche Standards zur Einhaltung gesetzlicher Bestimmungen (Vorschau)](#additional-regulatory-compliance-standards-preview)
 - [Threat Protection-Unterstützung für Azure Kubernetes Service (AKS) (Vorschau)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Sicherheitsrisikobewertung für virtuelle Computer (Vorschau)](#virtual-machine-vulnerability-assessment-preview)
 - [Advanced Data Security für SQL-Server auf Azure-VMs (Vorschau)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Unterstützung für benutzerdefinierte Richtlinien (Vorschau)](#support-for-custom-policies-preview)
 - [Erweitertes Azure Security Center-Angebot: Plattform für die Community und Partner](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Erweiterte Integration über den Export von Security Center-Empfehlungen und -Warnungen (Vorschau)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Onboarding lokaler Server in Security Center über Windows Admin Center (Vorschau)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Threat Protection für Azure Key Vault in Nordamerika (Vorschau)

Azure Key Vault ist ein zum Schutz von Daten und zur Verbesserung der Leistung von Cloudanwendungen unabdingbarer Dienst, der die Möglichkeit bietet, Schlüssel, Geheimnisse, Kryptografieschlüssel und Richtlinien zentral in der Cloud zu verwalten. Da in Azure Key Vault vertrauliche und geschäftskritische Daten gespeichert werden, ist für die Schlüsseltresore und die darin gespeicherten Daten höchste Sicherheit erforderlich.

Die Unterstützung des Bedrohungsschutzes für Azure Storage in Azure Security Center bietet eine zusätzliche Ebene intelligenter Sicherheitsfunktionen, die ungewöhnliche und möglicherweise schädliche Versuche erkennen, auf Schlüsseltresore zuzugreifen und diese missbräuchlich zu nutzen. Aufgrund dieser neuen Schutzebene können Kunden auch ohne Sicherheitsexpertise oder die Verwaltung von Sicherheitsüberwachungssystemen effektiv auf Bedrohungen ihrer Schlüsseltresore reagieren. Dieses Feature ist in Nordamerika als öffentliche Vorschauversion verfügbar.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Threat Protection für Azure Storage umfasst zuverlässiges Malwarescreening

Threat Protection für Azure Storage bietet mithilfe der Analyse für Hashbewertung und verdächtige Zugriffsmuster von einem aktiven Tor-Exitknoten (anonymisierender Proxy) von Microsoft Threat Intelligence neue Funktionen zum Erkennen von Malwareuploads in Azure Storage. Sie können jetzt mithilfe des Azure Security Center in Speicherkonten entdeckte Malware anzeigen lassen.


### <a name="workflow-automation-with-logic-apps-preview"></a>Workflowautomatisierung mit Azure Logic Apps (Vorschauversion)

Organisationen mit zentral verwalteten Sicherheits- und IT-Abläufen implementieren interne Workflowprozesse, um erforderliche Aktionen innerhalb der Organisation voranzutreiben, wenn Abweichungen in der Umgebung entdeckt werden. In vielen Fällen handelt es sich bei diesen Workflows um wiederholbare Prozesse, und durch die Automatisierung können Prozesse in der Organisation deutlich optimiert werden.

Wir führen heute eine neue Funktion in Security Center ein, die Kunden unter Verwendung von Azure Logic Apps das Erstellen von Automatisierungskonfigurationen und Richtlinien ermöglicht, die basierend auf bestimmten ASC-Ergebnissen wie beispielsweise Empfehlungen oder Warnungen automatisch ausgelöst werden. Azure Logic Apps kann so konfiguriert werden, dass eine beliebige von der großen Logic App-Connectors-Community unterstützte benutzerdefinierte Aktion ausgeführt oder eine der von Security Center bereitgestellten Vorlagen wie das Senden einer E-Mail oder das Öffnen eines ServiceNow™-Tickets verwendet wird.

Weitere Informationen zu den automatischen und manuellen Security Center-Funktionen zum Ausführen von Workflows finden Sie unter [Workflowautomatisierung](workflow-automation.md).

Informationen zum Erstellen von Logic Apps finden Sie unter [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Ressourcenübergreifendes schnelle Problembehebung allgemein verfügbar

Die Sicherheitsbewertung kann sehr umfangreich sein, wodurch es schwierig wird, Probleme ressourcenübergreifend zu beheben.

Mithilfe der schnellen Problembehebung werden die Vorgänge zum Beheben von fehlerhaften Sicherheitskonfigurationen vereinfacht, und Sie können schnell Empfehlungen ressourcenübergreifend umsetzen und Ihre Sicherheitsbewertung verbessern.

Mit dieser Vorgehensweise können Sie die Ressourcen auswählen, auf die Sie die Wartung anwenden möchten, und dann die Wartungsaktion starten. Die Konfiguration erfolgt automatisch, ohne dass Sie eingreifen müssen.

Die schnelle Problembehebung steht Kunden ab heute über die Seite „Security Center-Empfehlungen“ zur Verfügung.

Im [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) erfahren Sie, für welche Empfehlungen die schnelle Problembehebung aktiviert ist.


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Überprüfen von Containerimages auf Sicherheitsrisiken (Vorschauversion)

Azure Security Center kann nun Containerimages in Azure Container Registry auf Sicherheitsrisiken überprüfen.

Bei der Überprüfung von Images wird die Containerimagedatei analysiert und anschließend auf bekannte Sicherheitsrisiken überprüft (unterstützt durch Qualys).

Die Überprüfung selbst wird automatisch ausgelöst, wenn neue Containerimages per Push an Azure Container Registry übertragen werden. Erkannte Sicherheitsrisiken werden als Security Center-Empfehlungen angezeigt und sind zusammen mit Informationen zum Patchen dieser Risiken zur Reduzierung der gebotenen Angriffsfläche in der Azure-Sicherheitsbewertung enthalten.


### <a name="additional-regulatory-compliance-standards-preview"></a>Zusätzliche Standards zur Einhaltung gesetzlicher Bestimmungen (Vorschauversion)

Das Dashboard „Einhaltung gesetzlicher Bestimmungen“ bietet Erkenntnisse über Ihren Konformitätsstatus, die auf Security Center-Bewertungen basieren. Das Dashboard zeigt, inwieweit Ihre Umgebung mit den Steuerelementen und Anforderungen bestimmter gesetzlicher Standards und Branchenbenchmarks übereinstimmt und bietet ausführliche Empfehlungen zum Erfüllen dieser Anforderungen.

Das Dashboard „Einhaltung gesetzlicher Bestimmungen“ verfügt daher über vier integrierte und unterstützte Standards:  Azure CIS 1.1.0, PCI-DSS, ISO 27001 und SOC-TSP. Wir künden hiermit die öffentliche Vorschauversion zusätzlicher unterstützter Standards an: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM und UK Official zusammen mit UK NHS. Außerdem veröffentlichen wir eine aktualisierte Version von Azure CIS 1.1.0, in der weitere Steuerelemente der optimierten und der Standarderweiterbarkeit verfügbar sind.

[Weitere Informationen über das Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md)


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Threat Protection-Unterstützung für Azure Kubernetes Service (AKS) (Vorschauversion)

Kubernetes entwickelt sich in Rekordzeit zum neuen Standard für die Softwarebereitstellung und -verwaltung in der Cloud. Derzeit gibt es noch wenige Benutzer, die umfassende Erfahrungen mit Kubernetes vorweisen können. Viele konzentrieren sich auf die allgemeine Entwicklung und Verwaltung und vernachlässigen dabei den Sicherheitsaspekt. Eine Kubernetes-Umgebung muss sorgfältig konfiguriert werden, damit sie wirklich sicher ist. So wird sichergestellt, dass keine Angriffsfläche für Angriffe auf Container geboten wird. Die Unterstützung für den Containerbereich in Security Center wird um einen der am schnellsten wachsenden Dienst in Azure ausgebaut: Azure Kubernetes Service (AKS).

Die öffentliche Vorschauversion umfasst folgende Funktionen:

- **Ermittlung und Sichtbarkeit:** Continuous Discovery für verwaltete AKS-Instanzen in den registrierten Security Center-Abonnements
- **Empfehlungen zur Sicherheitsbewertung**: Nützliche Hinweise, mit denen Kunden die Best Practices für Sicherheit in AKS einhalten und ihre Sicherheitsbewertung erhöhen können. Ein Beispiel für diese Empfehlungen lautet: „Die rollenbasierte Zugriffssteuerung sollte genutzt werden, um den Zugriff auf einen Kubernetes Service-Cluster einzuschränken“.
- **Bedrohungserkennung:** host- und clusterbasierte Analysen (z. B. „Ein privilegierter Container wurde erkannt.“)


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Sicherheitsrisikobewertung für virtuelle Computer (Vorschauversion)

Auf virtuellen Computern installierte Anwendungen sind oft anfällig für Sicherheitsrisiken, die zu einer Kompromittierung des gesamten virtuellen Computers führen können. Heute geben wir bekannt, dass der Security Center-Standard-Tarif eine kostenlose integrierte Sicherheitsrisikobewertung für virtuelle Computer enthält. Die Sicherheitsrisikobewertung in der öffentlichen Vorschauversion basiert auf Qualys und ermöglicht das kontinuierliche Scannen aller installierten Anwendungen auf einem virtuellen Computer, um anfällige Anwendungen zu finden und die Ergebnisse auf der Benutzeroberfläche im Security Center-Portal anzuzeigen. Das Security Center berücksichtigt dabei alle Bereitstellungsvorgänge, sodass der Benutzer keine weiteren Maßnahmen ergreifen muss. Für die Zukunft planen wir die Bereitstellung von Sicherheitsrisikobewertungsoptionen, um die individuellen Geschäftsanforderungen unserer Kunden zu erfüllen.

[Weitere Informationen über Sicherheitsrisikobewertungen für Ihre virtuellen Azure-Computer](deploy-vulnerability-assessment-vm.md)


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Advanced Data Security für SQL-Server auf Azure-VMs (Vorschauversion)

Die Unterstützung von Azure Security Center für die Bedrohungsschutz- und Sicherheitsrisikobewertung für SQL-Datenbanken, die auf IaaS-VMs ausgeführt werden, befindet sich jetzt in der Vorschauphase.

[Sicherheitsrisikobewertung](../azure-sql/database/sql-vulnerability-assessment.md) ist ein einfach zu konfigurierender Dienst, mit dem potenzielle Schwachstellen in der Datenbank ermittelt, nachverfolgt und behoben werden können. Er bietet Einblicke in Ihren Sicherheitsstatus als Teil der Azure-Sicherheitsbewertung, enthält die Schritte zum Beheben von Sicherheitsproblemen und verbessert Ihre Datenbanksicherheit.

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf SQL Server zuzugreifen oder diesen zu nutzen. Sie überwacht Ihre Datenbank fortlaufend auf verdächtige Aktivitäten und stellt handlungsorientierte Sicherheitswarnungen bei anomalen Datenbankzugriffsmustern bereit. Diese Warnungen enthalten Details zur verdächtigen Aktivität sowie empfohlene Maßnahmen zur Untersuchung und Abwehr der Bedrohung.


### <a name="support-for-custom-policies-preview"></a>Unterstützung für benutzerdefinierte Richtlinien (Vorschauversion)

Azure Security Center unterstützt jetzt benutzerdefinierte Richtlinien (Vorschauphase).

Unsere Kunden haben sich gewünscht, die Abdeckung ihrer aktuellen Sicherheitsbewertungen in Security Center um ihre eigenen Sicherheitsbewertungen zu erweitern, die auf Richtlinien basieren, die sie in Azure Policy erstellen. Da nun benutzerdefinierte Richtlinien unterstützt werden, ist dies jetzt möglich.

Diese neuen Richtlinien sollen Teil der Security Center-Empfehlungen, Sicherheitsbewertung und dem Dashboard für Standards zur Einhaltung gesetzlicher Bestimmungen werden. Durch die Unterstützung benutzerdefinierter Richtlinien können Sie jetzt benutzerdefinierte Initiativen in Azure Policy erstellen, diese als Richtlinien zu Security Center hinzufügen und anschließend als Empfehlungen visualisieren.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Erweitertes Azure Security Center-Angebot: Plattform für die Community und Partner

Verwenden Sie Security Center, um Empfehlungen nicht nur von Microsoft, sondern auch aus bestehenden Lösungen von Partnern wie Check Point, Tenable und CyberArk zu erhalten, wobei viele weitere Integrationen vorgesehen sind.  Der einfache Security Center-Onboardingflow ermöglicht das Verbinden Ihrer vorhandenen Lösungen mit Security Center, sodass Sie alle Sicherheitsstatusempfehlungen an einem Ort anzeigen, vereinheitlichte Berichte ausführen und alle Security Center-Funktionen sowohl für integrierte als auch Partnerempfehlungen nutzen können. Sie können Security Center-Empfehlungen auch für Partnerprodukte exportieren.

[Weitere Informationen zur Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association)



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Erweiterte Integration über den Export von Security Center-Empfehlungen und -Warnungen (Vorschauversion)

Für Szenarios auf Unternehmensebene, die auf Security Center basieren, können jetzt Warnungen und Empfehlungen von Security Center auch außerhalb des Azure-Portals oder der Azure-API verarbeitet werden. Sie können sie direkt in einen Event Hub oder in Log Analytics-Arbeitsbereiche exportieren. Hier finden Sie Beispiele für Workflows, die Sie mithilfe dieser neuen Funktionen erstellen können:

- Durch den Export in Log Analytics-Arbeitsbereiche können Sie mithilfe von Power BI benutzerdefinierte Dashboards erstellen.
- Durch den Export in einen Event Hub können Sie Warnungen und Empfehlungen für Security Center in SIEM-Produkte von Drittanbietern, in Echtzeit in Lösungen von Drittanbietern oder in Azure Data Explorer exportieren.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Onboarding lokaler Server in Security Center über Windows Admin Center (Vorschauversion)

Windows Admin Center ist ein Verwaltungsportal für Windows-Server, die nicht in Azure bereitgestellt wurden, und bietet diesen mehrere Azure-Verwaltungsfunktionen wie Sicherungen und Systemupdates. Wir haben kürzlich eine Funktion zum Onboarding dieser Nicht-Azure-Server hinzugefügt, damit diese direkt über Windows Admin Center vom Azure Security Center geschützt werden.

Durch diese neue Funktion können Benutzer für einen WAC-Server das Onboarding für Azure Security Center durchführen und die Anzeige dessen Sicherheitswarnungen und -empfehlungen direkt über Windows Admin Center aktivieren.


## <a name="september-2019"></a>September 2019

Updates im September:

 - [Verbesserte Verwaltung von Regeln mit der adaptiven Anwendungssteuerung](#managing-rules-with-adaptive-application-controls-improvements)
 - [Steuern von Empfehlungen für die Containersicherheit mit Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Verbesserte Verwaltung von Regeln mit der adaptiven Anwendungssteuerung

Die Funktion zum Verwalten von Regeln für virtuelle Computer mithilfe der adaptiven Anwendungssteuerung wurde verbessert. Die adaptive Anwendungssteuerung ist ein Feature von Azure Security Center, mit dem Sie steuern können, welche Anwendungen auf Ihren virtuellen Computern ausgeführt werden können. Neben der allgemeinen Verbesserung der Regelverwaltung können Sie dank einer neuen Funktion nun steuern, welche Dateitypen geschützt werden, wenn Sie eine neue Regel hinzufügen.

[Weitere Informationen über Adaptive Anwendungssteuerungen](security-center-adaptive-application.md)


### <a name="control-container-security-recommendation-using-azure-policy"></a>Steuern von Empfehlungen für die Containersicherheit mit Azure Policy

Die Empfehlungen von Azure Security Center zum Korrigieren von Sicherheitsrisiken bei Containern können nun mit Azure Policy aktiviert und deaktiviert werden.

Öffnen Sie zum Anzeigen der von Ihnen aktivierten Sicherheitsrichtlinien in Security Center die Seite „Sicherheitsrichtlinie“.


## <a name="august-2019"></a>August 2019

Updates im August:

 - [Just-In-Time-Zugriff (JIT) auf virtuelle Computer für Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Ein-Klick-Wartung zur Verbesserung Ihres Sicherheitsstatus (Vorschau)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Mandantenübergreifende Verwaltung](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Just-In-Time-Zugriff (JIT) auf virtuelle Computer für Azure Firewall 

Just-In-Time-Zugriff (JIT) auf virtuelle Computer für Azure Firewall ist jetzt allgemein verfügbar. Schützen Sie damit Ihre durch Azure Firewall geschützten Umgebungen zusätzlich zu Ihren durch Netzwerksicherheitsgruppen (NSG) geschützten Umgebungen.

JIT-VM-Zugriff sorgt für ein geringeres Risiko durch volumetrische Netzwerkangriffe, da mit dieser Steuerung nur der wirklich erforderliche Zugriff auf Ihre VMs gewährt wird und dabei Ihre NSG- und Azure Firewall-Regeln angewandt werden.

Beim Aktivieren von JIT für Ihre VMs erstellen Sie eine Richtlinie, die festlegt, welche Ports geschützt werden sollen, wie lange die Ports geöffnet sein sollen und von welchen genehmigten IP-Adressen aus auf diese Ports zugegriffen werden kann. Mit dieser Richtlinie behalten Sie volle Kontrolle darüber, was Ihre Benutzer ausführen dürfen, wenn sie Zugriff anfordern.

Anforderungen werden im Azure-Aktivitätsprotokoll erfasst, sodass Sie den Zugriff ganz einfach überwachen und überprüfen können. Außerdem können Sie auf der JIT-Seite sehr schnell ermitteln, auf welchen vorhandenen VMs JIT aktiviert ist und auf welchen JIT empfohlen wird.

[Weitere Informationen über Azure Firewall](../firewall/overview.md)


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Ein-Klick-Wartung zur Verbesserung Ihres Sicherheitsstatus (Vorschauversion)

Secure Score ist ein Tool, mit dem Sie den Sicherheitsstatus bewerten können. Es überprüft Ihre Sicherheitsempfehlungen und priorisiert sie für Sie, damit Sie wissen, welche Empfehlungen Sie zuerst durchführen sollten. Dies hilft Ihnen, die schwerwiegendsten Sicherheitsrisiken zu finden, um die Untersuchung priorisieren zu können.

Zur Vereinfachung der Wartung von Fehlkonfigurationen bei der Sicherheit und für eine schnelle Verbesserung Ihrer Sicherheitsbewertung haben wir außerdem eine neue Funktion hinzugefügt, mit der Sie einzelne Empfehlungen mit nur einem Klick für einen ganzen Stapel von Ressourcen ausführen können.

Mit dieser Vorgehensweise können Sie die Ressourcen auswählen, auf die Sie die Wartung anwenden möchten, und dann die Wartungsaktion starten. Die Konfiguration erfolgt automatisch, ohne dass Sie eingreifen müssen.

Im [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) erfahren Sie, für welche Empfehlungen die schnelle Problembehebung aktiviert ist.


### <a name="cross-tenant-management"></a>Mandantenübergreifende Verwaltung

Security Center unterstützt jetzt Szenarien mit mandantenübergreifender Verwaltung als Teil von Azure Lighthouse. Dadurch können Sie in Security Center mehrere Mandanten einsehen und ihren Sicherheitsstatus verwalten. 

[Weitere Informationen über mandantenübergreifende Verwaltungsmöglichkeiten](security-center-cross-tenant-management.md)


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Updates für Netzwerkempfehlungen

Azure Security Center (ASC) hat neue Netzwerkempfehlungen veröffentlicht und bestehende verbessert. Damit trägt Security Center noch mehr zum Schutz Ihres Netzwerks und Ihrer Ressourcen bei. 

[Weitere Informationen über Netzwerkempfehlungen](recommendations-reference.md#recs-network)


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Adaptive Netzwerkhärtung allgemein verfügbar

Eine der größten Angriffsflächen für Workloads, die in der öffentlichen Cloud ausgeführt werden, sind Verbindungen mit und aus dem öffentlichen Internet. Unsere Kunden finden es schwierig zu entscheiden, welche Netzwerksicherheitsgruppenregeln gelten sollten, damit Azure-Workloads auch wirklich nur für die erforderlichen Quellbereiche verfügbar sind. Mit diesem Feature werden der Netzwerkdatenverkehr und Verbindungsmuster von Azure-Workloads von Security Center analysiert und Empfehlungen für Netzwerksicherheitsgruppenregeln für VMs gemacht, die mit dem Internet verbunden sind. Dadurch können Kunden ihre Netzwerkzugriffsrichtlinien besser konfigurieren und die Anfälligkeit für Angriffe minimieren. 

[Weitere Informationen über die adaptive Netzwerkhärtung](security-center-adaptive-network-hardening.md)