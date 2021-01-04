---
title: Steuerungen des FedRAMP Moderate-Blaupausenbeispiels
description: Steuerungszuordnung des FedRAMP Moderate-Blaupausenbeispiels. Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 10/26/2020
ms.topic: sample
ms.openlocfilehash: 3963e8915903ed91f28fc49bab33d6d8eeb4b12d
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675885"
---
# <a name="control-mapping-of-the-fedramp-moderate-blueprint-sample"></a>Steuerungszuordnung des FedRAMP Moderate-Blaupausenbeispiels

In diesem Artikel wird erläutert, wie das FedRAMP Moderate-Blaupausenbeispiel von Azure Blueprints den FedRAMP Moderate-Steuerungen zugeordnet wird. Weitere Informationen zu den Steuerungen finden Sie unter den [FedRAMP-Richtlinien für Sicherheitskontrollen](https://www.fedramp.gov/).

Die folgenden Zuordnungen gelten für die **FedRAMP Moderate** -Steuerungen. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **\[Vorschauversion\]: FedRAMP Moderate-Steuerungen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen** , und wählen Sie sie aus.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../../../policy/overview.md)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../../../policy/how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und Azure Policy-Definitionen für dieses Konformitätsblaupausenbeispiel können sich im Laufe der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/fedramp-m/control-mapping.md).

## <a name="ac-2-account-management"></a>AC-2 Kontoverwaltung

Diese Blaupause hilft Ihnen bei der Überprüfung von Konten, die u. U. nicht den Anforderungen Ihrer Organisation an die Kontoverwaltung entsprechen. Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zu, die externe Konten mit Lese-, Schreib- und Besitzerberechtigungen für ein Abonnement und veraltete Konten prüfen. Durch die Überprüfung der durch diese Richtlinien überwachten Konten können Sie geeignete Maßnahmen ergreifen, um sicherzustellen, dass die Anforderungen an die Kontoverwaltung erfüllt werden.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Kontoverwaltung | Rollenbasierte Schemas

Mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat. Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Mit dieser Blaupause werden auch [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um die Verwendung der Azure Active Directory-Authentifizierung für SQL Server-Instanzen und Service Fabric zu überwachen. Die Verwendung der Azure Active Directory-Authentifizierung ermöglicht eine vereinfachte Verwaltung von Berechtigungen und eine zentralisierte Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten. Mit dieser Blaupause wird zudem eine Azure Policy-Definition zugewiesen, um die Verwendung von benutzerdefinierten Azure RBAC-Regeln zu überwachen. Wenn Sie wissen, wo benutzerdefinierte Azure RBAC-Regeln implementiert sind, können Sie den Bedarf und die ordnungsgemäße Implementierung überprüfen, da benutzerdefinierte Azure RBAC-Regeln fehleranfällig sind.

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden
- Verwendung benutzerdefinierter RBAC-Regeln überwachen
- Service Fabric-Cluster sollten nur Azure Active Directory für die Clientauthentifizierung verwenden.

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Kontoverwaltung | Kontoüberwachung/ungewöhnliche Nutzung

Mit Just-In-Time-Zugriff (JIT) auf virtuelle Computer wird eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen. Alle JIT-Anforderungen des Zugriffs auf virtuelle Computer werden im Aktivitätsprotokoll protokolliert, sodass Sie atypische Nutzung überwachen können. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die Ihnen bei der Überwachung virtueller Computer hilft, die Just-In-Time-Zugriff unterstützen können, aber noch nicht konfiguriert wurden.

- Die Just-In-Time-Netzwerkzugriffssteuerung sollte auf virtuelle Computer angewendet werden.

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Erzwingung des Datenflusses

Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross Origin Resource Sharing, CORS) kann das Anfordern von App Services-Ressourcen von einer externen Domäne ermöglichen. Microsoft empfiehlt, nur erforderlichen Domänen die Interaktion mit Ihrer API, Funktion und Webanwendungen zu ermöglichen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die Ihnen hilft, die Zugriffsbeschränkungen für CORS-Ressourcen in Azure Security Center zu überwachen. Das Verstehen von CORS-Implementierungen kann Ihnen helfen, zu überprüfen, ob Informationsflusssteuerungen implementiert sind.

- Nicht jeder Ressource den Zugriff auf Ihre Webanwendung über CORS gestatten

## <a name="ac-5-separation-of-duties"></a>AC-5 Aufgabentrennung

Bei nur einem Azure-Abonnementbesitzer ist keine administrative Redundanz möglich. Im umgekehrten Fall können zu viele Azure-Abonnementbesitzer das Potenzial einer Sicherheitsverletzung durch ein kompromittiertes Besitzerkonto erhöhen. Mit dieser Blaupause können Sie eine angemessene Anzahl von Azure-Abonnementbesitzern beibehalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen die Anzahl der Besitzer für Azure-Abonnements überwacht wird. Diese Blaupause weist außerdem Azure Policy-Definitionen zu, mit denen Sie die Mitgliedschaft in der Administratorengruppe auf virtuellen Windows-Computern steuern können. Über die Verwaltung von Berechtigungen für Abonnementbesitzer und Administratoren von virtuellen Computern können Sie eine entsprechende Aufgabentrennung implementieren.

- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.
- Windows-VMs überwachen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Windows-VMs überwachen, bei denen die Gruppe „Administratoren“ nicht alle der angegebenen Mitglieder enthält
- Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ nicht alle der angegebenen Mitglieder enthält
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Remotezugriff | Automatisierte Überwachung/Steuerung

Diese Blaupause hilft Ihnen durch das Zuweisen von [Azure Policy](../../../policy/overview.md)-Definitionen bei der Überwachung und Steuerung des Remotezugriffs. Diese überwachen, dass das Remotedebuggen für die Azure App Service-Anwendung deaktiviert ist. Richtliniendefinitionen überwachen virtuelle Linux-Computer, die Remoteverbindungen von Konten ohne Kennwörter zulassen. Diese Blaupause weist auch eine Azure Policy-Definition zu, mit der Sie den uneingeschränkten Zugriff auf Speicherkonten überwachen können. Die Überwachung dieser Indikatoren kann Ihnen helfen, sicherzustellen, dass Remotezugriffsmethoden Ihrer Sicherheitsrichtlinie entsprechen.

- \[Vorschau\]: Linux-VMs überwachen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- \[Vorschau\]: Anforderungen zum Überwachen von Linux-VMs bereitstellen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen
- Remotedebuggen für API-App deaktivieren
- Remotedebuggen sollte für Funktions-Apps deaktiviert werden
- Remotedebuggen muss für Webanwendung deaktiviert werden

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen

Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zu, die Überprüfungs- und Ereignisprotokollierungskonfigurationen überwachen. Die Überwachung dieser Konfigurationen kann einen Indikator für einen Ausfall oder eine Fehlkonfiguration eines Überprüfungssystems bereitstellen und Ihnen helfen, Korrekturmaßnahmen zu ergreifen.

- Überwachen der Diagnoseeinstellung
- Die Überwachung in SQL Server muss aktiviert werden.
- Advanced Data Security muss für Ihre verwalteten Instanzen aktiviert werden.
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.

## <a name="au-12-audit-generation"></a>AU-12 Generierung von Überwachungsdatensätzen

Diese Blaupause hilft Ihnen, sicherzustellen, dass Systemereignisse protokolliert werden, indem Sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweisen, die die Einstellungen des Überwachungsprotokolls auf Azure-Ressourcen überprüfen.
Diese Richtliniendefinitionen überprüfen und erzwingen die Bereitstellung des Log Analytics-Agents auf virtuellen Azure-Computern und die Konfiguration von Überprüfungseinstellungen für andere Azure-Ressourcentypen. Diese Richtliniendefinitionen überprüfen auch die Konfiguration von Diagnoseprotokollen, um Einblick in Vorgänge zu erhalten, die innerhalb von Azure-Ressourcen ausgeführt werden. Darüber hinaus werden Überprüfung und Advanced Data Security auf SQL-Servern konfiguriert.

- \[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- \[Vorschau\]: Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- \[Vorschau\]: Überwachen des Log Analytics-Arbeitsbereichs für VM – Berichtskonflikt
- \[Vorschau\]: Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen
- \[Vorschau\]: Bereitstellen des Log Analytics-Agents für Linux-VMs
- \[Vorschau\]: Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen
- \[Vorschau\]: Bereitstellen des Log Analytics-Agents für Windows-VMs
- Überwachen der Diagnoseeinstellung
- Die Überwachung in SQL Server muss aktiviert werden.
- Advanced Data Security muss für Ihre verwalteten Instanzen aktiviert werden.
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern
- Bereitstellen von Überwachung auf SQL-Server-Instanzen
- Bereitstellen von Diagnoseeinstellungen für Netzwerksicherheitsgruppen

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Mindestfunktionalität | Unterbinden der Programmausführung

Die adaptive Anwendungssteuerung im Azure Security Center ist eine intelligente, automatisierte End-to-End-Filterlösung für Anwendungen, die die Ausführung bestimmter Software auf Ihren virtuellen Computern blockieren oder verhindern kann. Anwendungssteuerung kann in einem Erzwingungsmodus ausgeführt werden, der die Ausführung nicht genehmigter Anwendungen verhindert. Diese Blaupause weist eine Azure Policy-Definition zu, mit der Sie virtuelle Computer überwachen können, für die eine Anwendungszulassungsliste empfohlen wird, aber noch nicht konfiguriert wurde.

- Die adaptive Anwendungssteuerung sollte auf virtuellen Computern aktiviert werden.

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Mindestfunktionalität | Autorisierte Software und Whitelists

Die adaptive Anwendungssteuerung im Azure Security Center ist eine intelligente, automatisierte End-to-End-Filterlösung für Anwendungen, die die Ausführung bestimmter Software auf Ihren virtuellen Computern blockieren oder verhindern kann. Mit Anwendungssteuerung können Sie Listen von genehmigten Anwendungen für Ihre virtuellen Computer erstellen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, mit der Sie virtuelle Computer überwachen können, für die eine Anwendungszulassungsliste empfohlen wird, aber noch nicht konfiguriert wurde.

- Die adaptive Anwendungssteuerung sollte auf virtuellen Computern aktiviert werden.

## <a name="cm-11-user-installed-software"></a>CM-11 Von Benutzern installierte Software

Die adaptive Anwendungssteuerung im Azure Security Center ist eine intelligente, automatisierte End-to-End-Filterlösung für Anwendungen, die die Ausführung bestimmter Software auf Ihren virtuellen Computern blockieren oder verhindern kann. Mit Anwendungssteuerung können Sie die Einhaltung von Software-Einschränkungsrichtlinien durchsetzen und überwachen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, mit der Sie virtuelle Computer überwachen können, für die eine Anwendungszulassungsliste empfohlen wird, aber noch nicht konfiguriert wurde.

- Die adaptive Anwendungssteuerung sollte auf virtuellen Computern aktiviert werden.

## <a name="cp-7-alternate-processing-site"></a>CP-7 Alternativer Verarbeitungsstandort

Azure Site Recovery repliziert Workloads, die auf virtuellen Computern ausgeführt werden, von einem primären Standort an einen sekundären Standort. Bei einem Ausfall am primären Standort erfolgt ein Failover der Workload auf den sekundären Standort. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die virtuelle Computer ohne konfigurierte Notfallwiederherstellungskonfiguration überwacht. Durch Überwachung dieses Indikators können Sie sicherstellen, dass die erforderlichen Notfallsteuerungen vorhanden sind.

- Überwachen von virtuellen Computern ohne konfigurierte Notfallwiederherstellung

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf privilegierte Konten

Mit dieser Blaupause können Sie den privilegierten Zugriff einschränken und steuern, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, um Konten mit Besitzer- und/oder Schreibberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. Über die mehrstufige Authentifizierung können Konten geschützt werden, auch wenn bestimmte Authentifizierungsinformationen kompromittiert sind. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden.

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibberechtigungen für Ihr Abonnement aktiviert werden.

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf nicht privilegierte Konten

Mit dieser Blaupause können Sie den Zugriff einschränken und steuern, indem eine [Azure Policy](../../../policy/overview.md)-Definition zugewiesen wird, um Konten mit Leseberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. Über die mehrstufige Authentifizierung können Konten geschützt werden, auch wenn bestimmte Authentifizierungsinformationen kompromittiert sind. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden.

- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein

## <a name="ia-5-authenticator-management"></a>IA-5 Authentifikatorverwaltung

Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zum Überwachen virtueller Linux-Computer zu, die Remoteverbindungen von Konten ohne Kennwörter zulassen und/oder deren passwd-Datei falsche Berechtigungen enthält. Diese Blaupause weist auch Richtliniendefinitionen zu, die die Konfiguration des Kennwortverschlüsselungstyps für virtuelle Windows-Computer überwacht. Durch Überwachung dieser Indikatoren können Sie sicherstellen, dass die Systemauthentifikatoren die Identifizierung und Authentifizierungsrichtlinien Ihrer Organisation einhalten.

- \[Vorschau\]: Linux-VMs überwachen, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind
- \[Vorschau\]: Linux-VMs überwachen, die Konten ohne Kennwörter verwenden
- \[Vorschau\]: Windows-VMs überwachen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern
- \[Vorschau\]: Anforderungen zum Überwachen von Linux-VMs bereitstellen, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind
- \[Vorschau\]: Anforderungen zum Überwachen von Linux-VMs bereitstellen, die Konten ohne Kennwörter verwenden
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Authentifikatorverwaltung |Kennwortbasierte Authentifizierung

Mit dieser Blaupause können Sie sichere Kennwörter erzwingen, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen virtuelle Windows-Computer überwacht werden, auf denen keine Mindestsicherheitsanforderungen oder andere Anforderungen für Kennwörter erzwungen werden. Aufgrund der Informationen zu virtuellen Computern, die gegen die Richtlinie zur Kennwortsicherheit verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Kennwörter für alle Benutzerkonten auf virtuellen Computern mit der Kennwortrichtlinie Ihrer Organisation konform sind.

- \[Vorschau\]: Windows-VMs überwachen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen
- \[Vorschau\]: Windows-VMs überwachen, für die kein maximales Kennwortalter von 70 Tagen gilt
- \[Vorschau\]: Windows-VMs überwachen, die kein Mindestkennwortalter von 1 Tag verwenden
- \[Vorschau\]: Windows-VMs überwachen, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist
- \[Vorschau\]: Windows-VMs überwachen, für die keine Mindestkennwortlänge von 14 Zeichen festgelegt ist
- \[Vorschau\]: Windows-VMs überwachen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, für die kein maximales Kennwortalter von 70 Tagen gilt
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, die kein Mindestkennwortalter von 1 Tag verwenden
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, für die keine Mindestkennwortlänge von 14 Zeichen gilt
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Überprüfung auf Sicherheitsrisiken

Mit dieser Blaupause können Sie Sicherheitsrisiken im Informationssystem verwalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen.
Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten. Diese Blaupause weist außerdem Richtliniendefinitionen zu, die Advanced Data Security auf SQL-Servern überprüfen und erzwingen. Erweiterte Datensicherheit umfasst Sicherheitsrisikobewertung und erweiterte Funktionen zum Schutz vor Bedrohungen, damit Sie Sicherheitsrisiken in Ihren bereitgestellten Ressourcen besser verstehen.

- Advanced Data Security muss für Ihre verwalteten Instanzen aktiviert werden.
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre virtuellen Computer sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Schutz vor Denial-of-Service-Angriffen

Die DDoS-Standard-Tarif (Distributed Denial of Service) von Azure bietet mehr Features und Risikominderungsfunktionen als die Dienstebene „Basic“. Zu diesen zusätzlichen Features gehören die Integration von Azure Monitor und die Möglichkeit, Berichte zur Entschärfung nach einem Angriff zu überprüfen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die prüft, ob der DDoS-Standard-Tarif aktiviert ist. Das Verständnis des Funktionsunterschieds zwischen den Dienstebene kann Ihnen helfen, die beste Lösung für den Schutz vor Denial-of-Service-Angriffen für Ihre Azure-Umgebung auszuwählen.

- DDoS Protection Standard sollte aktiviert sein.

## <a name="sc-7-boundary-protection"></a>SC-7 Schutz von Grenzen

Mit dieser Blaupause können Sie die Systemgrenze verwalten und steuern, indem eine [Azure Policy](../../../policy/overview.md)-Definition zugewiesen wird, die Empfehlungen für die Härtung von Netzwerksicherheitsgruppen in Azure Security Center überprüft. Azure Security Center analysiert Datenverkehrsmuster von virtuellen Computern mit Internetanbindung und gibt Empfehlungen für Netzwerksicherheitsgruppen-Regeln, um die potenzielle Angriffsfläche zu reduzieren. Mit dieser Blaupause werden außerdem Richtliniendefinitionen zugewiesen, die nicht geschützte Endpunkte, Anwendungen und Speicherkonten überwachen. Endpunkte und Anwendungen, die nicht durch eine Firewall geschützt sind, sowie Speicherkonten mit uneingeschränktem Zugriff können einen unbeabsichtigten Zugriff auf Informationen im Informationssystem ermöglichen.

- NSG-Regeln für VMs mit Internetzugriff sollten verstärken werden.
- Zugriff über Endpunkt mit Internetzugriff sollte eingeschränkt werden
- Webports müssen für Netzwerksicherheitsgruppen beschränkt werden, die Ihrem virtuellen Computer zugeordnet sind.
- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Schutz von Grenzen | Zugriffspunkte

Mit Just-In-Time-Zugriff (JIT) auf virtuelle Computer wird eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen. JIT-Zugriff auf virtuelle Computer hilft Ihnen, die Anzahl von externen Verbindungen mit Ihren Ressourcen in Azure zu begrenzen. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die Ihnen bei der Überwachung virtueller Computer hilft, die Just-In-Time-Zugriff unterstützen können, aber noch nicht konfiguriert wurden.

- Die Just-In-Time-Netzwerkzugriffssteuerung sollte auf virtuelle Computer angewendet werden.

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Schutz von Grenzen | Externe Telekommunikationsdienste

Mit Just-In-Time-Zugriff (JIT) auf virtuelle Computer wird eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen. JIT-Zugriff auf virtuelle Computer hilft Ihnen bei der Verwaltung von Ausnahmen von Ihrer Datenverkehrsflussrichtlinie, indem die Zugriffsanforderungs- und Genehmigungsprozesse vereinfacht werden. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, die Ihnen bei der Überwachung virtueller Computer hilft, die Just-In-Time-Zugriff unterstützen können, aber noch nicht konfiguriert wurden.

- Die Just-In-Time-Netzwerkzugriffssteuerung sollte auf virtuelle Computer angewendet werden.

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Vertraulichkeit und Integrität von übertragenen Informationen | Kryptografischer oder alternativer physischer Schutz

Mithilfe dieser Blaupause können Sie die Vertraulichkeit und Integrität übertragener Informationen schützen. Weisen Sie dazu [Azure Policy](../../../policy/overview.md)-Definitionen zu, mit denen der für Kommunikationsprotokolle implementierte Kryptografiemechanismus überwacht werden kann. Die Sicherstellung der ordnungsgemäßen Verschlüsselung kann Sie bei der Erfüllung der Anforderungen Ihrer Organisation oder beim Schutz von Informationen vor nicht autorisierter Offenlegung und Änderung unterstützen.

- Auf API-Apps sollte nur über HTTPS zugegriffen werden können
- Windows-Webserver überwachen, die keine sicheren Kommunikationsprotokolle verwenden
- Anforderungen zum Überwachen von Windows-Webservern bereitstellen, die keine sicheren Kommunikationsprotokolle verwenden
- Zugriff auf Funktions-App nur über HTTPS gestatten
- Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.
- Zugriff auf Webanwendung nur über HTTPS gestatten

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Schutz von ruhenden Informationen | Kryptografischer Schutz

Mit dieser Blaupause können Sie die Richtlinie zur Verwendung von kryptografischen Steuerungen zum Schutz von ruhenden Informationen erzwingen, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die spezifische kryptografische Steuerungen erzwingen und die Verwendung schwacher kryptografischer Einstellungen überwachen. Wenn Sie wissen, wo Ihre Azure-Ressourcen möglicherweise nicht optimale kryptografische Konfigurationen aufweisen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Ressourcen entsprechend Ihrer Richtlinie zur Informationssicherheit konfiguriert sind. Für die von dieser Blaupause zugewiesenen Richtlinien gilt insbesondere Folgendes: Sie erfordern eine Verschlüsselung für Data Lake-Speicherkonten, sie erfordern eine transparente Datenverschlüsselung für SQL-Datenbanken, sie überwachen die fehlende Verschlüsselung für SQL-Datenbanken, VM-Datenträger und Variablen von Automation-Konten.

- Advanced Data Security muss für Ihre verwalteten Instanzen aktiviert werden.
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern
- Bereitstellen der transparenten SQL DB-Datenbankverschlüsselung
- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
- Verschlüsselung für Data Lake Store-Konten erforderlich
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.

## <a name="si-2-flaw-remediation"></a>SI-2 Fehlerbehebung

Mit dieser Blaupause können Sie Fehler im Informationssystem verwalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die fehlende Systemupdates, Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen. Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten. Diese Blaupause weist auch eine Richtliniendefinition zu, die das Patchen des Betriebssystems für VM-Skalierungsgruppen sicherstellt.

- Automatische Patches für Betriebssystemimages in Virtual Machine Scale Sets voraussetzen
- Systemupdates für VM-Skalierungsgruppen sollten installiert werden.
- Systemupdates sollten auf Ihren virtuellen Computern installiert sein.
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre virtuellen Computer sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.

## <a name="si-3-malicious-code-protection"></a>SI-3 Schutz vor schädlichem Code

Diese Blaupause hilft Ihnen bei der Verwaltung des Endpunktschutzes, einschließlich des Schutzes vor schädlichem Code, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die virtuelle Computer in Azure Security Center auf fehlenden Endpunktschutz überprüfen und die Microsoft-Antischadsoftwarelösung auf virtuellen Windows-Computern erzwingen.

- Bereitstellung der standardmäßigen Microsoft IaaSAntimalware-Erweiterung für Windows Server
- Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.
- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Schutz vor schädlichem Code | Zentrale Verwaltung

Diese Blaupause hilft Ihnen bei der Verwaltung des Endpunktschutzes, einschließlich des Schutzes vor schädlichem Code, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die virtuelle Computer in Azure Security Center auf fehlenden Endpunktschutz überprüfen. Azure Security Center umfasst Funktionen zur zentralisierten Verwaltung und Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten.

- Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.
- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen

## <a name="si-4-information-system-monitoring"></a>SI-4 Überwachung des Informationssystems

Diese Blaupause hilft Ihnen bei der Überwachung Ihres Systems, indem sie die Protokollierung und Datensicherheit für alle Azure-Ressourcen überprüft und erzwingt. Insbesondere weisen die Richtlinien Überwachung zu und erzwingen die Bereitstellung des Log Analytics-Agent sowie erweiterte Sicherheitseinstellungen für SQL-Datenbanken, Speicherkonten und Netzwerkressourcen. Diese Funktionen helfen Ihnen beim Erkennen von anomalem Verhalten und Indikatoren für Angriffe, sodass Sie geeignete Maßnahmen ergreifen können.

- \[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- \[Vorschau\]: Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- \[Vorschau\]: Überwachen des Log Analytics-Arbeitsbereichs für VM – Berichtskonflikt
- \[Vorschau\]: Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen
- \[Vorschau\]: Bereitstellen des Log Analytics-Agents für Linux-VMs
- \[Vorschau\]: Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen
- \[Vorschau\]: Bereitstellen des Log Analytics-Agents für Windows-VMs
- Advanced Data Security muss für Ihre verwalteten Instanzen aktiviert werden.
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Bereitstellen von Advanced Data Security auf SQL-Servern
- Bereitstellen von Advanced Threat Protection für Speicherkonten
- Bereitstellen von Überwachung auf SQL-Server-Instanzen
- Bereitstellen von Network Watcher beim Erstellen virtueller Netzwerke
- Bereitstellen von Bedrohungserkennung auf SQL-Servern

> [!NOTE]
> Die Verfügbarkeit spezifischer Azure Policy-Definitionen kann in Azure Government und anderen nationalen Clouds variieren. 

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun die Steuerungszuordnung der FedRAMP Moderate-Blaupause angesehen haben, lesen Sie die folgenden Artikel, um weitere Informationen zur Blaupause zu erhalten und zu erfahren, wie Sie dieses Beispiel bereitstellen:

> [!div class="nextstepaction"]
> [FedRAMP Moderate-Blaupause – Übersicht](./index.md)
> [FedRAMP Moderate-Blaupause – Bereitstellungsschritte](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).