---
title: Steuerungen des Blaupausenbeispiels „SWIFT CSP-CSCF v2020“
description: Steuerungszuordnung des SWIFT CSP-CSCF v2020-Blaupausenbeispiels Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 08/18/2020
ms.topic: sample
ms.openlocfilehash: 5aa4ee556c4ec7348566f45592b5e9fbf00eaf20
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009397"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Steuerungszuordnung des SWIFT CSP-CSCF v2020-Blaupausenbeispiels

Im folgenden Artikel wird erläutert, wie das SWIFT CSP-CSCF v2020-Blaupausenbeispiel von Azure Blueprints den SWIFT CSP-CSCF v2020-Steuerungen zugeordnet wird. Weitere Informationen zu den Steuerungen finden Sie unter [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

Die folgenden Zuordnungen gelten für die Steuerungen unter **SWIFT CSP-CSCF v2020**. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **\[Vorschauversion\]: SWIFT CSP-CSCF v2020-Steuerungen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen**, und wählen Sie sie aus.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../../../policy/overview.md)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../../../policy/how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und Azure Policy-Definitionen für dieses Konformitätsblaupausenbeispiel können sich im Laufe der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1.2 und 5.1 Kontoverwaltung

Diese Blaupause hilft Ihnen bei der Überprüfung von Konten, die u. U. nicht den Anforderungen Ihrer Organisation an die Kontoverwaltung entsprechen. Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zu, die externe Konten mit Lese-, Schreib- und Besitzerberechtigungen für ein Abonnement und veraltete Konten prüfen. Durch die Überprüfung der durch diese Richtlinien überwachten Konten können Sie geeignete Maßnahmen ergreifen, um sicherzustellen, dass die Anforderungen an die Kontoverwaltung erfüllt werden.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1, 6.4 und 6.5A Kontoverwaltung | Rollenbasierte Schemas

Mithilfe der [rollenbasierten Zugriffssteuerung von Azure](../../../../role-based-access-control/overview.md) (Azure RBAC) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat. Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Mit dieser Blaupause werden auch [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um die Verwendung der Azure Active Directory-Authentifizierung für SQL Server-Instanzen und Service Fabric zu überwachen. Die Verwendung der Azure Active Directory-Authentifizierung ermöglicht eine vereinfachte Verwaltung von Berechtigungen und eine zentralisierte Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten. Mit dieser Blaupause wird zudem eine Azure Policy-Definition zugewiesen, um die Verwendung von benutzerdefinierten Azure RBAC-Regeln zu überwachen. Wenn Sie wissen, wo benutzerdefinierte Azure RBAC-Regeln implementiert sind, können Sie den Bedarf und die ordnungsgemäße Implementierung überprüfen, da benutzerdefinierte Azure RBAC-Regeln fehleranfällig sind.

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden
- Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden
- Service Fabric-Cluster sollten nur Azure Active Directory für die Clientauthentifizierung verwenden.

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A Kontoverwaltung | Kontoüberwachung/ungewöhnliche Nutzung

Mit Just-In-Time-Zugriff (JIT) auf virtuelle Computer wird eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen. Alle JIT-Anforderungen des Zugriffs auf virtuelle Computer werden im Aktivitätsprotokoll protokolliert, sodass Sie atypische Nutzung überwachen können. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die Ihnen hilft, virtuelle Computer zu überwachen, die Just-In-Time-Zugriff unterstützen können, aber noch nicht konfiguriert wurden.

- Verwaltungsports virtueller Computer müssen mit der Just-In-Time-Netzwerkzugriffssteuerung geschützt werden

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1 und 6.4 Aufgabentrennung

Bei nur einem Azure-Abonnementbesitzer ist keine administrative Redundanz möglich. Im umgekehrten Fall können zu viele Azure-Abonnementbesitzer das Potenzial einer Sicherheitsverletzung durch ein kompromittiertes Besitzerkonto erhöhen. Mit dieser Blaupause können Sie eine angemessene Anzahl von Azure-Abonnementbesitzern beibehalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen die Anzahl der Besitzer für Azure-Abonnements überwacht wird. Diese Blaupause weist außerdem Azure Policy-Definitionen zu, mit denen Sie die Mitgliedschaft in der Administratorengruppe auf virtuellen Windows-Computern steuern können. Über die Verwaltung von Berechtigungen für Abonnementbesitzer und Administratoren von virtuellen Computern können Sie eine entsprechende Aufgabentrennung implementieren.

- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.
- Überwachungsergebnisse von Windows-VMs anzeigen, bei denen die Gruppe „Administratoren“ nicht alle der angegebenen Mitglieder enthält
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ nicht alle der angegebenen Mitglieder enthält
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 und 6.4 Ansatz der geringsten Rechte | Kontrolle von Benutzerberechtigungen

Mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat. Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zu, um Konten zu überwachen, die zur Überprüfung priorisiert werden sollten. Die Überprüfung dieser Kontoindikatoren kann Ihnen helfen, sicherzustellen, dass die Steuerungen mit den geringsten Rechten implementiert sind.

- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.
- Überwachungsergebnisse von Windows-VMs anzeigen, die nicht in die angegebene Domäne eingebunden sind
- Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, die nicht in die angegebene Domäne eingebunden sind
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.

## <a name="22-and-27-security-attributes"></a>2.2 und 2.7 Sicherheitsattribute

Die Advanced Data Security-Funktion zur Datenermittlung und -klassifizierung für Azure SQL-Datenbank bietet Funktionen zum Ermitteln, Klassifizieren, Bezeichnen und Schützen sensibler Daten in Ihren Datenbanken. Das Feature kann Einblicke in den Zustand Ihrer Datenbankklassifizierung bereitstellen und den Zugriff auf sensible Daten innerhalb der Datenbank und außerhalb ihrer Grenzen verfolgen. Mit Advanced Data Security kann sichergestellt werden, dass Informationen den entsprechenden Sicherheitsattributen Ihrer Organisation zugeordnet werden. Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zu, um die Verwendung von Advanced Data Security auf SQL-Servern zu überprüfen und zu erzwingen.

- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7, 4.1 und 6.1 Remotezugriff | Automatisierte Überwachung/Steuerung

Diese Blaupause hilft Ihnen durch das Zuweisen von [Azure Policy](../../../policy/overview.md)-Definitionen bei der Überwachung und Steuerung des Remotezugriffs. Diese überwachen, dass das Remotedebuggen für die Azure App Service-Anwendung deaktiviert ist. Richtliniendefinitionen überwachen virtuelle Linux-Computer, die Remoteverbindungen von Konten ohne Kennwörter zulassen. Diese Blaupause weist auch eine Azure Policy-Definition zu, mit der Sie den uneingeschränkten Zugriff auf Speicherkonten überwachen können. Die Überwachung dieser Indikatoren kann Ihnen helfen, sicherzustellen, dass Remotezugriffsmethoden Ihrer Sicherheitsrichtlinie entsprechen.

- Überwachungsergebnisse von Linux-VMs anzeigen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Erforderliche Komponenten zum Überwachen von Linux-VMs bereitstellen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Netzwerkzugriff auf Speicherkonten einschränken
- Remotedebuggen für API-App deaktivieren
- Remotedebuggen sollte für Funktions-Apps deaktiviert werden
- Remotedebuggen muss für Webanwendung deaktiviert werden

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 und 6.4 Inhalt der Überwachungsdatensätze | Zentrale Verwaltung der Inhalte von Datensätzen der geplanten Überwachung

Von Azure Monitor erfasste Protokolldaten werden in einem Log Analytics-Arbeitsbereich gespeichert, um eine zentrale Konfiguration und Verwaltung zu ermöglichen. Mit dieser Blaupause können Sie sicherstellen, dass Ereignisse protokolliert werden, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die die Bereitstellung des Log Analytics-Agent auf virtuellen Azure-Computern überwachen und erzwingen.

- \[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellen des Log Analytics-Agents für Linux-VMs
- Bereitstellen des Log Analytics-Agents für Windows-VMs

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 und 6.4 Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen

Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zu, die Überprüfungs- und Ereignisprotokollierungskonfigurationen überwachen. Die Überwachung dieser Konfigurationen kann einen Indikator für einen Ausfall oder eine Fehlkonfiguration eines Überprüfungssystems bereitstellen und Ihnen helfen, Korrekturmaßnahmen zu ergreifen.

- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Überwachen der Diagnoseeinstellung
- Die Überwachung in SQL Server muss aktiviert werden.

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 und 6.4 Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Zentrale Überprüfung und Analyse

Von Azure Monitor erfasste Protokolldaten werden in einem Log Analytics-Arbeitsbereich gespeichert, um eine zentrale Berichterstattung und Analyse zu ermöglichen. Mit dieser Blaupause können Sie sicherstellen, dass Ereignisse protokolliert werden, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die die Bereitstellung des Log Analytics-Agent auf virtuellen Azure-Computern überwachen und erzwingen.

- \[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellen des Log Analytics-Agents für Linux-VMs
- Bereitstellen des Log Analytics-Agents für Windows-VMs

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2, 2.7, 6.4 und 6.5A Generierung von Überwachungsdatensätzen

Diese Blaupause hilft Ihnen, sicherzustellen, dass Systemereignisse protokolliert werden, indem Sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweisen, die die Einstellungen des Überwachungsprotokolls auf Azure-Ressourcen überprüfen.
Diese Richtliniendefinitionen überprüfen und erzwingen die Bereitstellung des Log Analytics-Agents auf virtuellen Azure-Computern und die Konfiguration von Überprüfungseinstellungen für andere Azure-Ressourcentypen. Diese Richtliniendefinitionen überprüfen auch die Konfiguration von Diagnoseprotokollen, um Einblick in Vorgänge zu erhalten, die innerhalb von Azure-Ressourcen ausgeführt werden. Darüber hinaus werden Überprüfung und Advanced Data Security auf SQL-Servern konfiguriert.

- Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen
- Bereitstellen des Log Analytics-Agents für Linux-VMs
- Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen
- Bereitstellen des Log Analytics-Agents für Windows-VMs
- Überwachen der Diagnoseeinstellung
- Überwachungseinstellungen auf SQL Server-Ebene überwachen
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern
- Die Überwachung in SQL Server muss aktiviert werden.
- Bereitstellen von Diagnoseeinstellungen für Netzwerksicherheitsgruppen

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 Mindestfunktionalität | Unterbinden der Programmausführung

Die adaptive Anwendungssteuerung im Azure Security Center ist eine intelligente, automatisierte End-to-End-Filterlösung für Anwendungen, die die Ausführung bestimmter Software auf Ihren virtuellen Computern blockieren oder verhindern kann. Anwendungssteuerung kann in einem Erzwingungsmodus ausgeführt werden, der die Ausführung nicht genehmigter Anwendungen verhindert. Diese Blaupause weist eine Azure Policy-Definition zu, mit der Sie virtuelle Computer überwachen können, für die eine Anwendungszulassungsliste empfohlen wird, aber noch nicht konfiguriert wurde.

- Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 Mindestfunktionalität | Autorisierte Software und Whitelists

Die adaptive Anwendungssteuerung im Azure Security Center ist eine intelligente, automatisierte End-to-End-Filterlösung für Anwendungen, die die Ausführung bestimmter Software auf Ihren virtuellen Computern blockieren oder verhindern kann. Mit Anwendungssteuerung können Sie Listen von genehmigten Anwendungen für Ihre virtuellen Computer erstellen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, mit der Sie virtuelle Computer überwachen können, für die eine Anwendungszulassungsliste empfohlen wird, aber noch nicht konfiguriert wurde.

- Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein

## <a name="11-user-installed-software"></a>1.1 Von Benutzern installierte Software

Die adaptive Anwendungssteuerung im Azure Security Center ist eine intelligente, automatisierte End-to-End-Filterlösung für Anwendungen, die die Ausführung bestimmter Software auf Ihren virtuellen Computern blockieren oder verhindern kann. Mit Anwendungssteuerung können Sie die Einhaltung von Software-Einschränkungsrichtlinien durchsetzen und überwachen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, mit der Sie virtuelle Computer überwachen können, für die eine Anwendungszulassungsliste empfohlen wird, aber noch nicht konfiguriert wurde.

- Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein
- VMs sollten zu neuen Azure Resource Manager-Ressourcen migriert werden

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf privilegierte Konten

Mit dieser Blaupause können Sie den privilegierten Zugriff einschränken und steuern, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, um Konten mit Besitzer- und/oder Schreibberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. Über die mehrstufige Authentifizierung können Konten geschützt werden, auch wenn bestimmte Authentifizierungsinformationen kompromittiert sind. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden.

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibberechtigungen für Ihr Abonnement aktiviert werden.

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf nicht privilegierte Konten

Mit dieser Blaupause können Sie den Zugriff einschränken und steuern, indem eine [Azure Policy](../../../policy/overview.md)-Definition zugewiesen wird, um Konten mit Leseberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. Über die mehrstufige Authentifizierung können Konten geschützt werden, auch wenn bestimmte Authentifizierungsinformationen kompromittiert sind. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden.

- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein

## <a name="23-and-41-authenticator-management"></a>2.3 und 4.1 Authentifikatorverwaltung

Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zum Überwachen virtueller Linux-Computer zu, die Remoteverbindungen von Konten ohne Kennwörter zulassen und/oder deren passwd-Datei falsche Berechtigungen enthält. Diese Blaupause weist auch Richtliniendefinitionen zu, die die Konfiguration des Kennwortverschlüsselungstyps für virtuelle Windows-Computer überwacht. Durch Überwachung dieser Indikatoren können Sie sicherstellen, dass die Systemauthentifikatoren die Identifizierung und Authentifizierungsrichtlinien Ihrer Organisation einhalten.

- Überwachungsergebnisse von Linux-VMs anzeigen, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind
- Anforderungen zum Überwachen von Linux-VMs bereitstellen, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind
- Überwachungsergebnisse von Linux-VMs anzeigen, die Konten ohne Kennwörter verwenden
- Anforderungen zum Überwachen von Linux-VMs bereitstellen, die Konten ohne Kennwörter verwenden
- Überwachungsergebnisse von Windows-VMs anzeigen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern
- Anforderungen zum Überwachen von Windows-VMs bereitstellen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3. und 4.1. Authentifikatorverwaltung |Kennwortbasierte Authentifizierung

Mit dieser Blaupause können Sie sichere Kennwörter erzwingen, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen virtuelle Windows-Computer überwacht werden, auf denen keine Mindestsicherheitsanforderungen oder andere Anforderungen für Kennwörter erzwungen werden. Aufgrund der Informationen zu virtuellen Computern, die gegen die Richtlinie zur Kennwortsicherheit verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Kennwörter für alle Benutzerkonten auf virtuellen Computern mit der Kennwortrichtlinie Ihrer Organisation konform sind.

- Überwachungsergebnisse von Windows-VMs anzeigen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen
- Überwachungsergebnisse von Windows-VMs anzeigen, für die kein maximales Kennwortalter von 70 Tagen gilt
- Überwachungsergebnisse von Windows-VMs anzeigen, für die kein minimales Kennwortalter von 1 Tag gilt
- Überwachungsergebnisse von Windows-VMs anzeigen, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist
- Überwachungsergebnisse von Windows-VMs anzeigen, für die keine Mindestkennwortlänge von 14 Zeichen festgelegt ist
- Überwachungsergebnisse von Windows-VMs anzeigen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, für die kein maximales Kennwortalter von 70 Tagen gilt
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, für die kein minimales Kennwortalter von 1 Tag gilt
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, für die keine Mindestkennwortlänge von 14 Zeichen gilt
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern

## <a name="22-and-27-vulnerability-scanning"></a>2.2. und 2.7 Überprüfung auf Sicherheitsrisiken

Mit dieser Blaupause können Sie Sicherheitsrisiken im Informationssystem verwalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen.
Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten. Diese Blaupause weist außerdem Richtliniendefinitionen zu, die Advanced Data Security auf SQL-Servern überprüfen und erzwingen. Erweiterte Datensicherheit umfasst Sicherheitsrisikobewertung und erweiterte Funktionen zum Schutz vor Bedrohungen, damit Sie Sicherheitsrisiken in Ihren bereitgestellten Ressourcen besser verstehen.

- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Die Überwachung in SQL Server muss aktiviert werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden. 
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.

## <a name="13-denial-of-service-protection"></a>1.3 Schutz vor Denial-of-Service-Angriffen

Die DDoS-Standard-Tarif (Distributed Denial of Service) von Azure bietet mehr Features und Risikominderungsfunktionen als die Dienstebene „Basic“. Zu diesen zusätzlichen Features gehören die Integration von Azure Monitor und die Möglichkeit, Berichte zur Entschärfung nach einem Angriff zu überprüfen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die prüft, ob der DDoS-Standard-Tarif aktiviert ist. Das Verständnis des Funktionsunterschieds zwischen den Dienstebene kann Ihnen helfen, die beste Lösung für den Schutz vor Denial-of-Service-Angriffen für Ihre Azure-Umgebung auszuwählen.

- Azure DDoS Protection Standard muss aktiviert sein

## <a name="11-and-61-boundary-protection"></a>1.1 und 6.1 Schutz von Grenzen

Mit dieser Blaupause können Sie die Systemgrenze verwalten und steuern, indem eine [Azure Policy](../../../policy/overview.md)-Definition zugewiesen wird, die Empfehlungen für die Härtung von Netzwerksicherheitsgruppen in Azure Security Center überprüft. Azure Security Center analysiert Datenverkehrsmuster von virtuellen Computern mit Internetanbindung und gibt Empfehlungen für Netzwerksicherheitsgruppen-Regeln, um die potenzielle Angriffsfläche zu reduzieren. Mit dieser Blaupause werden außerdem Richtliniendefinitionen zugewiesen, die nicht geschützte Endpunkte, Anwendungen und Speicherkonten überwachen. Endpunkte und Anwendungen, die nicht durch eine Firewall geschützt sind, sowie Speicherkonten mit uneingeschränktem Zugriff können einen unbeabsichtigten Zugriff auf Informationen im Informationssystem ermöglichen.

- Auf virtuelle Computer mit Internetzugang müssen Empfehlungen zur adaptiven Netzwerkhärtung angewendet werden.
- Zugriff über Endpunkt mit Internetzugriff sollte eingeschränkt werden
- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen

## <a name="29a-boundary-protection--access-points"></a>2.9A Schutz von Grenzen | Zugriffspunkte

Mit Just-In-Time-Zugriff (JIT) auf virtuelle Computer wird eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen. JIT-Zugriff auf virtuelle Computer hilft Ihnen, die Anzahl von externen Verbindungen mit Ihren Ressourcen in Azure zu begrenzen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die Ihnen hilft, virtuelle Computer zu überwachen, die Just-In-Time-Zugriff unterstützen können, aber noch nicht konfiguriert wurden.

- Verwaltungsports virtueller Computer müssen mit der Just-In-Time-Netzwerkzugriffssteuerung geschützt werden

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A Schutz von Grenzen | Externe Telekommunikationsdienste

Mit Just-In-Time-Zugriff (JIT) auf virtuelle Computer wird eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen. JIT-Zugriff auf virtuelle Computer hilft Ihnen bei der Verwaltung von Ausnahmen von Ihrer Datenverkehrsflussrichtlinie, indem die Zugriffsanforderungs- und Genehmigungsprozesse vereinfacht werden. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die Ihnen hilft, virtuelle Computer zu überwachen, die Just-In-Time-Zugriff unterstützen können, aber noch nicht konfiguriert wurden.

- Verwaltungsports virtueller Computer müssen mit der Just-In-Time-Netzwerkzugriffssteuerung geschützt werden

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4, 2.4A, 2.5A und 2.6 Vertraulichkeit und Integrität von übertragenen Informationen | Kryptografischer oder alternativer physischer Schutz

Mithilfe dieser Blaupause können Sie die Vertraulichkeit und Integrität übertragener Informationen schützen. Weisen Sie dazu [Azure Policy](../../../policy/overview.md)-Definitionen zu, mit denen der für Kommunikationsprotokolle implementierte Kryptografiemechanismus überwacht werden kann. Die Sicherstellung der ordnungsgemäßen Verschlüsselung kann Sie bei der Erfüllung der Anforderungen Ihrer Organisation oder beim Schutz von Informationen vor nicht autorisierter Offenlegung und Änderung unterstützen.

- Auf API-Apps sollte nur über HTTPS zugegriffen werden können
- Überwachungsergebnisse von Windows-Webservern anzeigen, die keine sicheren Kommunikationsprotokolle verwenden
- Erforderliche Komponenten zum Überwachen von Windows-Webservern bereitstellen, die keine sicheren Kommunikationsprotokolle verwenden
- Zugriff auf Funktions-App nur über HTTPS gestatten
- Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.
- Zugriff auf Webanwendung nur über HTTPS gestatten

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3, 2.5, 4.1 und 2.7 Schutz von ruhenden Informationen | Kryptografischer Schutz

Mit dieser Blaupause können Sie die Richtlinie zur Verwendung von kryptografischen Steuerungen zum Schutz von ruhenden Informationen erzwingen, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die spezifische kryptografische Steuerungen erzwingen und die Verwendung schwacher kryptografischer Einstellungen überwachen. Wenn Sie wissen, wo Ihre Azure-Ressourcen möglicherweise nicht optimale kryptografische Konfigurationen aufweisen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Ressourcen entsprechend Ihrer Richtlinie zur Informationssicherheit konfiguriert sind. Für die von dieser Blaupause zugewiesenen Richtlinien gilt insbesondere Folgendes: Sie erfordern eine Verschlüsselung für Data Lake-Speicherkonten, sie erfordern eine transparente Datenverschlüsselung für SQL-Datenbanken, sie überwachen die fehlende Verschlüsselung für SQL-Datenbanken, VM-Datenträger und Variablen von Automation-Konten.

- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern
- Bereitstellen der transparenten SQL DB-Datenbankverschlüsselung
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 und 2.7 Fehlerbehebung

Mit dieser Blaupause können Sie Fehler im Informationssystem verwalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die fehlende Systemupdates, Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen. Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten. Diese Blaupause weist auch eine Richtliniendefinition zu, die das Patchen des Betriebssystems für VM-Skalierungsgruppen sicherstellt.

- Automatische Patches für Betriebssystemimages in Virtual Machine Scale Sets voraussetzen
- Systemupdates für VM-Skalierungsgruppen sollten installiert werden.
- Systemupdates sollten auf Ihren virtuellen Computern installiert sein.
- Bereitstellung des Dependency-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Automation-Kontovariablen sollten verschlüsselt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre virtuellen Computer sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.

## <a name="61-malicious-code-protection"></a>6.1 Schutz vor schädlichem Code

Diese Blaupause hilft Ihnen bei der Verwaltung des Endpunktschutzes, einschließlich des Schutzes vor schädlichem Code, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die virtuelle Computer in Azure Security Center auf fehlenden Endpunktschutz überprüfen und die Microsoft-Antischadsoftwarelösung auf virtuellen Windows-Computern erzwingen.

- Bereitstellung der standardmäßigen Microsoft IaaSAntimalware-Erweiterung für Windows Server
- Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.
- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen
- Speicherkonten sollten zu neuen Azure Resource Manager-Ressourcen migriert werden.

## <a name="61-malicious-code-protection--central-management"></a>6.1 Schutz vor schädlichem Code | Zentrale Verwaltung

Diese Blaupause hilft Ihnen bei der Verwaltung des Endpunktschutzes, einschließlich des Schutzes vor schädlichem Code, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die virtuelle Computer in Azure Security Center auf fehlenden Endpunktschutz überprüfen. Azure Security Center umfasst Funktionen zur zentralisierten Verwaltung und Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten.

- Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.
- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3, 2.2, 2.7, 2.8 und 6.4 Überwachung des Informationssystems

Diese Blaupause hilft Ihnen bei der Überwachung Ihres Systems, indem sie die Protokollierung und Datensicherheit für alle Azure-Ressourcen überprüft und erzwingt. Insbesondere weisen die Richtlinien Überwachung zu und erzwingen die Bereitstellung des Log Analytics-Agent sowie erweiterte Sicherheitseinstellungen für SQL-Datenbanken, Speicherkonten und Netzwerkressourcen. Diese Funktionen helfen Ihnen beim Erkennen von anomalem Verhalten und Indikatoren für Angriffe, sodass Sie geeignete Maßnahmen ergreifen können.

- Überwachungsergebnisse von Windows-VMs anzeigen, auf denen der Log Analytics-Agent nicht wie erwartet verbunden ist
- Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen
- Bereitstellen des Log Analytics-Agents für Linux-VMs
- Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen
- Bereitstellen des Log Analytics-Agents für Windows-VMs
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Advanced Data Security-Einstellungen für SQL Server sollten eine E-Mail-Adresse für den Empfang von Sicherheitswarnungen enthalten.
- Diagnoseprotokolle in Azure Stream Analytics sollten aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern
- Bereitstellen von Überwachung auf SQL-Server-Instanzen
- Bereitstellen von Network Watcher beim Erstellen virtueller Netzwerke
- Bereitstellen von Bedrohungserkennung auf SQL-Servern

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 und 2.8 Überwachung des Informationssystems | Analyse des Datenverkehrs/verdeckte Ausschleusung

Advanced Threat Protection für Azure Storage erkennt ungewöhnliche und möglicherweise schädliche Versuchen, auf Speicherkonten zuzugreifen oder diese unbefugt zu nutzen. Zu den Schutzwarnungen gehören anomale Zugriffsmuster, anomale Extrahierungen/Uploads und verdächtige Speicheraktivitäten. Diese Indikatoren können Ihnen helfen, die verdeckte Exfiltration von Informationen zu erkennen.

- Bereitstellen von Bedrohungserkennung auf SQL-Servern

> [!NOTE]
> Die Verfügbarkeit spezifischer Azure Policy-Definitionen kann in Azure Government und anderen nationalen Clouds variieren.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich die Steuerungszuordnung der Blaupause „SWIFT CSP-CSCF v2020“ angesehen haben, lesen Sie nun die folgenden Artikel, um Informationen zur Blaupause zu erhalten und zu erfahren, wie Sie dieses Beispiel bereitstellen:

> [!div class="nextstepaction"]
> [Blaupause „SWIFT CSP-CSCF v2020“: Übersicht](./index.md)
> [Blaupause „SWIFT CSP-CSCF v2020“: Bereitstellungsschritte](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
