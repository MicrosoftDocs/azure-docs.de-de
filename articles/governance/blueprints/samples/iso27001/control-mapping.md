---
title: Steuerungen des Blaupausenbeispiels „ISO 27001“
description: Steuerungszuordnung des ISO 27001-Blaupausenbeispiels. Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 11/05/2020
ms.topic: sample
ms.openlocfilehash: 0c1c20ae8b740ce019441dbee880f883947af160
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842497"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>Steuerungszuordnung des ISO 27001-Blaupausenbeispiels

In diesem Artikel wird erläutert, wie das Azure Blueprints-Blaupausenbeispiel „ISO 27001“ den ISO 27001-Steuerungen zugeordnet wird. Weitere Informationen zu den Steuerungen finden Sie unter [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Die folgenden Zuordnungen gelten für die Steuerungen unter **ISO 27001:2013**. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **\[Vorschau\] ISO 27001:2013-Steuerelemente überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen**, und wählen Sie sie aus.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../../../policy/overview.md)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../../../policy/how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und Azure Policy-Definitionen für dieses Konformitätsblaupausenbeispiel können sich im Laufe der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Aufgabentrennung

Bei nur einem Azure-Abonnementbesitzer ist keine administrative Redundanz möglich. Im umgekehrten Fall können zu viele Azure-Abonnementbesitzer das Potenzial einer Sicherheitsverletzung durch ein kompromittiertes Besitzerkonto erhöhen. Mit dieser Blaupause können Sie eine angemessene Anzahl von Azure-Abonnementbesitzern beibehalten, indem zwei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen die Anzahl der Besitzer für Azure-Abonnements überwacht wird. Über die Verwaltung von Berechtigungen für Abonnementbesitzer können Sie eine entsprechende Aufgabentrennung implementieren.

- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.

## <a name="a821-classification-of-information"></a>A.8.2.1 Klassifizierung von Informationen

Mithilfe des [SQL-Sicherheitsrisikobewertungsdiensts](../../../../azure-sql/database/sql-vulnerability-assessment.md) von Azure können Sie sensible Daten finden, die in Ihren Datenbanken gespeichert sind, und erhalten Empfehlungen zur Klassifizierung dieser Daten. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, um zu überwachen, dass während der Überprüfung der SQL-Sicherheitsrisikobewertung identifizierte Sicherheitslücken geschlossen werden.

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Zugriff auf Netzwerke und Netzwerkdienste

In Azure ist die [rollenbasierte Azure-Zugriffssteuerung (Azure RBAC)](../../../../role-based-access-control/overview.md) zur Verwaltung des Zugriffs auf Azure-Ressourcen implementiert. Mit dieser Blaupause können Sie den Zugriff auf Azure-Ressourcen durch Zuweisen von sieben [Azure Policy](../../../policy/overview.md)-Definitionen steuern. Mit diesen Richtlinien wird die Verwendung von Ressourcentypen und Konfigurationen überwacht, die einen weniger restriktiven Zugriff auf Ressourcen ermöglichen.
Durch Kenntnis der Ressourcen, die gegen diese Richtlinien verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass der Zugriff auf Azure-Ressourcen auf autorisierte Benutzer beschränkt ist.

- Erforderliche Komponenten für die Überwachung von Linux-VMs bereitstellen, die Konten ohne Kennwörter verwenden
- Erforderliche Komponenten zum Überwachen von Linux-VMs bereitstellen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Überwachungsergebnisse von Linux-VMs anzeigen, die Konten ohne Kennwörter verwenden
- Überwachungsergebnisse von Linux-VMs anzeigen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Speicherkonten sollten zu neuen Azure Resource Manager-Ressourcen migriert werden.
- VMs sollten zu neuen Azure Resource Manager-Ressourcen migriert werden
- Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Verwaltung von Rechten für den privilegierten Zugriff

Mit dieser Blaupause können Sie Rechte für den privilegierten Zugriff einschränken und steuern, indem vier [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, um externe Konten mit Besitzer- und/oder Schreibberechtigungen und Konten mit Besitzer- und/oder Schreibberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie verwalten, wer Zugriff auf Azure-Ressourcen hat. Mit dieser Blaupause werden auch drei Azure Policy-Definitionen zugewiesen, um die Verwendung der Azure Active Directory-Authentifizierung für SQL Server-Instanzen und Service Fabric zu überwachen. Die Verwendung der Azure Active Directory-Authentifizierung ermöglicht eine vereinfachte Verwaltung von Berechtigungen und eine zentralisierte Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten. Mit dieser Blaupause wird zudem eine Azure Policy-Definition zugewiesen, um die Verwendung von benutzerdefinierten Azure RBAC-Regeln zu überwachen. Wenn Sie wissen, wo benutzerdefinierte Azure RBAC-Regeln implementiert sind, können Sie den Bedarf und die ordnungsgemäße Implementierung überprüfen, da benutzerdefinierte Azure RBAC-Regeln fehleranfällig sind.

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden
- Service Fabric-Cluster sollten nur Azure Active Directory für die Clientauthentifizierung verwenden.
- Verwendung benutzerdefinierter RBAC-Regeln überwachen

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Verwaltung von Informationen von Benutzern zur Authentifizierung über Geheimnisse

Mit dieser Blaupause werden drei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um Konten ohne aktivierte mehrstufige Authentifizierung zu überwachen. Über die mehrstufige Authentifizierung können Konten geschützt werden, auch wenn bestimmte Authentifizierungsinformationen kompromittiert sind. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden. Mit dieser Blaupause werden zudem zwei Azure Policy-Definitionen zugewiesen, über die Berechtigungen für Kennwortdateien für virtuelle Linux-Computer überwacht werden und eine Benachrichtigung erfolgt, wenn sie nicht ordnungsgemäß festgelegt sind. Durch diese Einrichtung können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass Authentifikatoren nicht kompromittiert sind.

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- Überwachungsergebnisse von Linux-VMs anzeigen, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Überprüfung der Zugriffsrechte für Benutzer

Mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat. Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Mit dieser Blaupause werden vier [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um Konten zu überwachen, die für die Überprüfung priorisiert sind, einschließlich veralteter Konten und externer Konten mit erhöhten Rechten.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Entfernung oder Anpassung von Zugriffsrechten

Mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat. Mithilfe von [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) und Azure RBAC können Sie Benutzerrollen aktualisieren, um Organisationsänderungen umzusetzen. Bei Bedarf kann die Anmeldung für Konten blockiert werden (oder Konten können entfernt werden), wodurch die Zugriffsrechte für Azure-Ressourcen sofort entfernt werden. Mit dieser Blaupause werden zwei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um veraltete Konten zu überwachen, die bei der Entfernung berücksichtigt werden sollten.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Sichere Anmeldungsprozeduren

Mit dieser Blaupause werden drei Azure Policy-Definitionen zugewiesen, um Konten ohne aktivierte mehrstufige Authentifizierung zu überwachen. Indem Azure AD Multi-Factor Authentication eine zweite Form der Authentifizierung verlangt, bietet das Verfahren zusätzliche Sicherheit und eine zuverlässige Authentifizierung. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden.

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden

## <a name="a943-password-management-system"></a>A.9.4.3 Kennwortverwaltungssystem

Mit dieser Blaupause können Sie sichere Kennwörter erzwingen, indem 10 [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen virtuelle Windows-Computer überwacht werden, auf denen keine Mindestsicherheitsanforderungen oder andere Anforderungen für Kennwörter erzwungen werden. Aufgrund der Informationen zu virtuellen Computern, die gegen die Richtlinie zur Kennwortsicherheit verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Kennwörter für alle Benutzerkonten auf virtuellen Computern mit der Richtlinie konform sind.

- Überwachungsergebnisse von Windows-VMs anzeigen, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist
- Überwachungsergebnisse von Windows-VMs anzeigen, für die kein maximales Kennwortalter von 70 Tagen gilt
- Überwachungsergebnisse von Windows-VMs anzeigen, für die kein minimales Kennwortalter von 1 Tag gilt
- Überwachungsergebnisse von Windows-VMs anzeigen, für die keine Mindestkennwortlänge von 14 Zeichen festgelegt ist
- Überwachungsergebnisse von Windows-VMs anzeigen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Richtlinie zur Verwendung von kryptografischen Steuerungen

Mit dieser Blaupause können Sie die Richtlinie zur Verwendung von kryptografischen Steuerungen erzwingen, indem 13 [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die spezifische kryptografische Steuerungen erzwingen und die Verwendung schwacher kryptografischer Einstellungen überwachen. Wenn Sie wissen, wo Ihre Azure-Ressourcen möglicherweise nicht optimale kryptografische Konfigurationen aufweisen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Ressourcen entsprechend Ihrer Richtlinie zur Informationssicherheit konfiguriert sind. Für die mit dieser Blaupause zugewiesenen Richtlinien gilt Folgendes: Sie erfordern eine Verschlüsselung für Blob Storage-Konten und Data Lake Storage-Konten, sie erfordern eine transparente Datenverschlüsselung für SQL-Datenbanken, sie überwachen die fehlende Verschlüsselung für Speicherkonten, SQL-Datenbanken, VM-Datenträger und Variablen von Automation-Konten, sie überwachen unsichere Verbindungen mit Speicherkonten, Funktions-Apps, Web-Apps, API-Apps und Redis Cache, sie überwachen die schwache Kennwortverschlüsselung bei virtuellen Computern, und sie überwachen die unverschlüsselte Kommunikation in Service Fabric.

- Zugriff auf Funktions-App nur über HTTPS gestatten
- Zugriff auf Webanwendung nur über HTTPS gestatten
- Auf API-Apps sollte nur über HTTPS zugegriffen werden können
- Überwachungsergebnisse von Windows-VMs anzeigen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern
- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
- Automation-Kontovariablen sollten verschlüsselt werden.
- Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.
- Service Fabric Cluster sollten die Eigenschaft ClusterProtectionLevel auf EncryptAndSign setzen
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.

## <a name="a1241-event-logging"></a>A.12.4.1 Ereignisprotokollierung

Mit dieser Blaupause können Sie sicherstellen, dass Systemereignisse protokolliert werden, indem Sie sieben [Azure Policy](../../../policy/overview.md)-Definitionen zuweisen, mit denen die Protokolleinstellungen für Azure-Ressourcen überwacht werden.
Diagnoseprotokolle bieten Einblicke in Vorgänge, die in Azure-Ressourcen ausgeführt werden.

- Bereitstellung des Dependency-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellung des Dependency-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Überwachen der Diagnoseeinstellung
- Die Überwachung in SQL Server muss aktiviert werden.

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Administrator- und Operatorprotokolle

Mit dieser Blaupause können Sie sicherstellen, dass Systemereignisse protokolliert werden, indem Sie sieben Azure Policy-Definitionen zuweisen, mit denen die Protokolleinstellungen für Azure-Ressourcen überwacht werden. Diagnoseprotokolle bieten Einblicke in Vorgänge, die in Azure-Ressourcen ausgeführt werden.

- Bereitstellung des Dependency-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellung des Dependency-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Überwachen der Diagnoseeinstellung
- Die Überwachung in SQL Server muss aktiviert werden.

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Uhrsynchronisierung

Mit dieser Blaupause können Sie sicherstellen, dass Systemereignisse protokolliert werden, indem Sie sieben Azure Policy-Definitionen zuweisen, mit denen die Protokolleinstellungen für Azure-Ressourcen überwacht werden. Azure-Protokolle basieren auf synchronisierten internen Uhren, um eine zeitkorrelierte Aufzeichnung von Ereignissen über Ressourcen hinweg zu erstellen.

- Bereitstellung des Dependency-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellung des Dependency-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Überwachen der Diagnoseeinstellung
- Die Überwachung in SQL Server muss aktiviert werden.

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Installation von Software auf Betriebssystemen

Die adaptive Anwendungssteuerung ist die Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren virtuellen Computern in Azure ausgeführt werden können. Mit dieser Blaupause wird eine Azure Policy-Definition zugewiesen, die Änderungen an der Gruppe der zulässigen Anwendungen überwacht. Mit dieser Funktion können Sie die Installation von Software und Anwendungen auf virtuellen Azure-Computern steuern.

- Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Verwaltung von technischen Sicherheitsrisiken

Mit dieser Blaupause können Sie Sicherheitsrisiken im Informationssystem verwalten, indem fünf [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die fehlende Systemupdates, Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen. Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten.

- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen
- Systemupdates sollten auf Ihren Computern installiert sein.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Einschränkungen bei der Softwareinstallation

Die adaptive Anwendungssteuerung ist die Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren virtuellen Computern in Azure ausgeführt werden können. Mit dieser Blaupause wird eine Azure Policy-Definition zugewiesen, die Änderungen an der Gruppe der zulässigen Anwendungen überwacht. Durch Einschränkungen bei der Softwareinstallation kann die Wahrscheinlichkeit der Einführung von Sicherheitsrisiken in der Software reduziert werden.

- Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein

## <a name="a1311-network-controls"></a>A.13.1.1 Netzwerksteuerungen

Mit dieser Blaupause können Sie Netzwerke verwalten und steuern, indem eine [Azure Policy](../../../policy/overview.md)-Definition zugewiesen wird, die Netzwerksicherheitsgruppen mit wenig einschränkenden Regeln überwacht. Regeln, die zu wenig einschränkend sind, können einen unbeabsichtigten Netzwerkzugriff ermöglichen und sollten überprüft werden. Mit dieser Blaupause werden auch drei Azure Policy-Definitionen zugewiesen, die nicht geschützte Endpunkte, Anwendungen und Speicherkonten überwachen. Endpunkte und Anwendungen, die nicht durch eine Firewall geschützt sind, sowie Speicherkonten mit uneingeschränktem Zugriff können einen unbeabsichtigten Zugriff auf Informationen im Informationssystem ermöglichen.

- Zugriff über Endpunkt mit Internetzugriff sollte eingeschränkt werden
- Netzwerkzugriff auf Speicherkonten einschränken

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Richtlinien und Verfahren zur Übertragung von Informationen

Mit dieser Blaupause können Sie sicherstellen, dass die Übertragung von Informationen mit Azure-Diensten sicher ist, indem zwei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die unsichere Verbindungen mit Speicherkonten und Redis Cache überwachen.

- Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Steuerungszuordnung des Blaupausenbeispiels „ISO 27001“ vertraut gemacht haben, finden Sie nun in den folgenden Artikeln Informationen zur Architektur und zur Bereitstellung dieses Beispiels:

> [!div class="nextstepaction"]
> [Übersicht über das ISO 27001-Blaupausenbeispiel](./index.md)
> [Deploy the ISO 27001 blueprint sample (Bereitstellen des ISO 27001-Blaupausenbeispiels)](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
