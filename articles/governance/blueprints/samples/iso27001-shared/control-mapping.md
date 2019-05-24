---
title: 'Beispiel: Blaupause für „ISO 27001: Gemeinsame Dienste“ – Steuerelementzuordnung'
description: 'Steuerelementzuordnung des Beispiels „Blaupause für ISO 27001: gemeinsame Dienste“, Azure-Richtlinien und RBAC.'
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c40efca9abd418c8b48f931d327b1f81805b38fb
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520399"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Steuerelementzuordnung für das Beispiel „Blaupause für ISO 27001: gemeinsame Dienste“

In diesem Artikel wird erläutert, wie das Azure Blueprints-Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“ den ISO 27001-Steuerungen zugeordnet wird. Weitere Informationen zu den Steuerungen finden Sie unter [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Die folgenden Zuordnungen gelten für die Steuerungen unter **ISO 27001:2013**. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **[Vorschau] ISO 27001:2013-Steuerelemente überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen**, und wählen Sie sie aus.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Aufgabentrennung

Bei nur einem Azure-Abonnementbesitzer ist keine administrative Redundanz möglich. Im umgekehrten Fall können zu viele Azure-Abonnementbesitzer das Potenzial einer Sicherheitsverletzung durch ein kompromittiertes Besitzerkonto erhöhen. Mit dieser Blaupause können Sie eine angemessene Anzahl von Azure-Abonnementbesitzern beibehalten, indem zwei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen die Anzahl der Besitzer für Azure-Abonnements überwacht wird. Über die Verwaltung von Berechtigungen für Abonnementbesitzer können Sie eine entsprechende Aufgabentrennung implementieren.

- [Vorschau]: Audit minimum number of owners for a subscription
- [Vorschau]: Audit maximum number of owners for a subscription

## <a name="a821-classification-of-information"></a>A.8.2.1 Klassifizierung von Informationen

Mithilfe des [SQL-Sicherheitsrisikobewertungsdiensts](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) von Azure können Sie sensible Daten finden, die in Ihren Datenbanken gespeichert sind, und erhalten Empfehlungen zur Klassifizierung dieser Daten. Diese Blaupause weist eine [Azure Policy](../../../policy/overview.md)-Definition zu, um zu überwachen, dass während der Überprüfung der SQL-Sicherheitsrisikobewertung identifizierte Sicherheitslücken geschlossen werden.

- [Vorschau]: Monitor SQL vulnerability assessment results in Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Zugriff auf Netzwerke und Netzwerkdienste

In Azure ist die [rollenbasierte Zugriffssteuerung ](../../../../role-based-access-control/overview.md) (RBAC) zur Verwaltung des Zugriffs auf Azure-Ressourcen implementiert. Mit dieser Blaupause können Sie den Zugriff auf Azure-Ressourcen durch Zuweisen von sieben [Azure Policy](../../../policy/overview.md)-Definitionen steuern. Mit diesen Richtlinien wird die Verwendung von Ressourcentypen und Konfigurationen überwacht, die einen weniger restriktiven Zugriff auf Ressourcen ermöglichen.
Durch Kenntnis der Ressourcen, die gegen diese Richtlinien verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass der Zugriff auf Azure-Ressourcen auf autorisierte Benutzer beschränkt ist.

- [Vorschau]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Vorschau]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Kennwörters
- [Vorschau]: Audit Linux VM accounts with no passwords
- [Vorschau]: Audit Linux VM allowing remote connections from accounts with no passwords
- Verwendung von klassischen Speicherkonten überwachen
- Verwendung klassischer VMs überwachen
- Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Verwaltung von Rechten für den privilegierten Zugriff

Mit dieser Blaupause können Sie Rechte für den privilegierten Zugriff einschränken und steuern, indem vier [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, um externe Konten mit Besitzer- und/oder Schreibberechtigungen und Konten mit Besitzer- und/oder Schreibberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. In Azure ist die rollenbasierte Zugriffssteuerung (RBAC) zur Verwaltung des Zugriffs auf Azure-Ressourcen implementiert. Mit dieser Blaupause werden auch drei Azure Policy-Definitionen zugewiesen, um die Verwendung der Azure Active Directory-Authentifizierung für SQL Server-Instanzen und Service Fabric zu überwachen. Die Verwendung der Azure Active Directory-Authentifizierung ermöglicht eine vereinfachte Verwaltung von Berechtigungen und eine zentralisierte Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten. Mit dieser Blaupause wird zudem eine Azure Policy-Definition zugewiesen, um die Verwendung von benutzerdefinierten RBAC-Regeln zu überwachen. Wenn Sie wissen, wo benutzerdefinierte RBAC-Regeln implementiert sind, können Sie den Bedarf und die ordnungsgemäße Implementierung überprüfen, da benutzerdefinierte RBAC-Regeln fehleranfällig sind.

- [Vorschau]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Vorschau]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Vorschau]: Audit external accounts with owner permissions on a subscription
- [Vorschau]: Audit external accounts with write permissions on a subscription
- Bereitstellung eines Azure Active Directory-Administrators für SQL Server überwachen
- Verwendung von Azure Active Directory für Clientauthentifizierung in Service Fabric überwachen
- Verwendung benutzerdefinierter RBAC-Regeln überwachen

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Verwaltung von Informationen von Benutzern zur Authentifizierung über Geheimnisse

Mit dieser Blaupause werden drei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um Konten ohne aktivierte mehrstufige Authentifizierung zu überwachen. Über die mehrstufige Authentifizierung können Konten geschützt werden, auch wenn bestimmte Authentifizierungsinformationen kompromittiert sind. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden. Mit dieser Blaupause werden zudem zwei Azure Policy-Definitionen zugewiesen, über die Berechtigungen für Kennwortdateien für virtuelle Linux-Computer überwacht werden und eine Benachrichtigung erfolgt, wenn sie nicht ordnungsgemäß festgelegt sind. Durch diese Einrichtung können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass Authentifikatoren nicht kompromittiert sind.

- [Vorschau]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Vorschau]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Vorschau]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Vorschau]: Deploy VM extension to audit Linux VM passwd file permissions
- [Vorschau]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Überprüfung der Zugriffsrechte für Benutzer

In Azure ist die [rollenbasierte Zugriffssteuerung ](../../../../role-based-access-control/overview.md) (RBAC) zur Verwaltung des Zugriffs auf Azure-Ressourcen implementiert. Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Mit dieser Blaupause werden vier [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um Konten zu überwachen, die für die Überprüfung priorisiert sind, einschließlich veralteter Konten und externer Konten mit erhöhten Rechten.

- [Vorschau]: Audit deprecated accounts on a subscription
- [Vorschau]: Audit deprecated accounts with owner permissions on a subscription
- [Vorschau]: Audit external accounts with owner permissions on a subscription
- [Vorschau]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Entfernung oder Anpassung von Zugriffsrechten

In Azure ist die [rollenbasierte Zugriffssteuerung ](../../../../role-based-access-control/overview.md) (RBAC) zur Verwaltung des Zugriffs auf Azure-Ressourcen implementiert. Mithilfe von [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) und RBAC können Sie Benutzerrollen aktualisieren, um Organisationsänderungen umzusetzen. Bei Bedarf kann die Anmeldung für Konten blockiert werden (oder Konten können entfernt werden), wodurch die Zugriffsrechte für Azure-Ressourcen sofort entfernt werden. Mit dieser Blaupause werden zwei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um veraltete Konten zu überwachen, die bei der Entfernung berücksichtigt werden sollten.

- [Vorschau]: Audit deprecated accounts on a subscription
- [Vorschau]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Sichere Anmeldungsprozeduren

Mit dieser Blaupause werden drei Azure Policy-Definitionen zugewiesen, um Konten ohne aktivierte mehrstufige Authentifizierung zu überwachen. Indem Azure Multi-Factor Authentication eine zweite Form der Authentifizierung verlangt, bietet das Verfahren zusätzliche Sicherheit und eine zuverlässige Authentifizierung. Durch die Überwachung von Konten ohne aktivierte mehrstufige Authentifizierung können Sie die Konten identifizieren, die möglicherweise eher kompromittiert werden.

- [Vorschau]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Vorschau]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Vorschau]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 Kennwortverwaltungssystem

Mit dieser Blaupause können Sie sichere Kennwörter erzwingen, indem 10 [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, mit denen virtuelle Windows-Computer überwacht werden, auf denen keine Mindestsicherheitsanforderungen oder andere Anforderungen für Kennwörter erzwungen werden. Aufgrund der Informationen zu virtuellen Computern, die gegen die Richtlinie zur Kennwortsicherheit verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Kennwörter für alle Benutzerkonten auf virtuellen Computern mit der Richtlinie konform sind.

- [Vorschau]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Vorschau]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Vorschau]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Vorschau]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Vorschau]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Vorschau]: Audit Windows VM enforces password complexity requirements
- [Vorschau]: Audit Windows VM maximum password age 70 days
- [Vorschau]: Audit Windows VM minimum password age 1 day
- [Vorschau]: Audit Windows VM passwords must be at least 14 characters
- [Vorschau]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Richtlinie zur Verwendung von kryptografischen Steuerungen

Mit dieser Blaupause können Sie die Richtlinie zur Verwendung von kryptografischen Steuerungen erzwingen, indem 13 [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die spezifische kryptografische Steuerungen erzwingen und die Verwendung schwacher kryptografischer Einstellungen überwachen.
Wenn Sie wissen, wo Ihre Azure-Ressourcen möglicherweise nicht optimale kryptografische Konfigurationen aufweisen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Ressourcen entsprechend Ihrer Richtlinie zur Informationssicherheit konfiguriert sind. Für die mit dieser Blaupause zugewiesenen Richtlinien gilt Folgendes: Sie erfordern eine Verschlüsselung für Blob Storage-Konten und Data Lake Storage-Konten, sie erfordern eine transparente Datenverschlüsselung für SQL-Datenbanken, sie überwachen die fehlende Verschlüsselung für Speicherkonten, SQL-Datenbanken, VM-Datenträger und Variablen von Automation-Konten, sie überwachen unsichere Verbindungen mit Speicherkonten, Funktions-Apps, Web-Apps, API-Apps und Redis Cache, sie überwachen die schwache Kennwortverschlüsselung bei virtuellen Computern, und sie überwachen die unverschlüsselte Kommunikation in Service Fabric.

- [Vorschau]: Audit HTTPS only access for a Function App
- [Vorschau]: Audit HTTPS only access for a Web Application
- [Vorschau]: Audit HTTPS only access for an API App
- [Vorschau]: Audit missing blob encryption for storage accounts
- [Vorschau]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  Verschlüsselungn
- [Vorschau]: Audit Windows VM should not store passwords using reversible encryption
- [Vorschau]: Monitor unencrypted SQL database in Azure Security Center
- [Vorschau]: Monitor unencrypted VM Disks in Azure Security Center
- Aktivierung der Verschlüsselung für Variablen von Automation-Konten überwachen
- Ausschließliche Aktivierung sicherer Verbindungen mit Redis Cache überwachen
- Sichere Übertragung in Speicherkonten überwachen
- Festlegung der ClusterProtectionLevel-Eigenschaft auf EncryptAndSign in Service Fabric überwachen
- Überwachen des Status der transparenten Datenverschlüsselung

## <a name="a1241-event-logging"></a>A.12.4.1 Ereignisprotokollierung

Mit dieser Blaupause können Sie sicherstellen, dass Systemereignisse protokolliert werden, indem Sie sieben [Azure Policy](../../../policy/overview.md)-Definitionen zuweisen, mit denen die Protokolleinstellungen für Azure-Ressourcen überwacht werden.
Diagnoseprotokolle bieten Einblicke in Vorgänge, die in Azure-Ressourcen ausgeführt werden.

- [Vorschau]: Bereitstellung des Dependency-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Dependency-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Monitor unaudited SQL database in Azure Security Center
- Überwachen der Diagnoseeinstellung
- Überwachungseinstellungen auf SQL Server-Ebene überwachen

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Administrator- und Operatorprotokolle

Mit dieser Blaupause können Sie sicherstellen, dass Systemereignisse protokolliert werden, indem Sie sieben Azure Policy-Definitionen zuweisen, mit denen die Protokolleinstellungen für Azure-Ressourcen überwacht werden. Diagnoseprotokolle bieten Einblicke in Vorgänge, die in Azure-Ressourcen ausgeführt werden.

- [Vorschau]: Bereitstellung des Dependency-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Dependency-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Monitor unaudited SQL database in Azure Security Center
- Überwachen der Diagnoseeinstellung
- Überwachungseinstellungen auf SQL Server-Ebene überwachen

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Uhrsynchronisierung

Mit dieser Blaupause können Sie sicherstellen, dass Systemereignisse protokolliert werden, indem Sie sieben Azure Policy-Definitionen zuweisen, mit denen die Protokolleinstellungen für Azure-Ressourcen überwacht werden. Azure-Protokolle basieren auf synchronisierten internen Uhren, um eine zeitkorrelierte Aufzeichnung von Ereignissen über Ressourcen hinweg zu erstellen.

- [Vorschau]: Bereitstellung des Dependency-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Dependency-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- [Vorschau]: Monitor unaudited SQL database in Azure Security Center
- Überwachen der Diagnoseeinstellung
- Überwachungseinstellungen auf SQL Server-Ebene überwachen

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Installation von Software auf Betriebssystemen

Die adaptive Anwendungssteuerung ist die Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren virtuellen Computern in Azure ausgeführt werden können. Mit dieser Blaupause wird eine Azure Policy-Definition zugewiesen, die Änderungen an der Gruppe der zulässigen Anwendungen überwacht. Mit dieser Funktion können Sie die Installation von Software und Anwendungen auf virtuellen Azure-Computern steuern.

- [Vorschau]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Verwaltung von technischen Sicherheitsrisiken

Mit dieser Blaupause können Sie Sicherheitsrisiken im Informationssystem verwalten, indem fünf [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die fehlende Systemupdates, Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen. Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten.

- [Vorschau]: Monitor missing Endpoint Protection in Azure Security Center
- [Vorschau]: Monitor missing system updates in Azure Security Center
- [Vorschau]: Monitor OS vulnerabilities in Azure Security Center
- [Vorschau]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Vorschau]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Einschränkungen bei der Softwareinstallation

Die adaptive Anwendungssteuerung ist die Lösung von Azure Security Center, mit der Sie steuern können, welche Anwendungen auf Ihren virtuellen Computern in Azure ausgeführt werden können. Mit dieser Blaupause wird eine Azure Policy-Definition zugewiesen, die Änderungen an der Gruppe der zulässigen Anwendungen überwacht. Durch Einschränkungen bei der Softwareinstallation kann die Wahrscheinlichkeit der Einführung von Sicherheitsrisiken in der Software reduziert werden.

- [Vorschau]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Netzwerksteuerungen

Mit dieser Blaupause können Sie Netzwerke verwalten und steuern, indem eine [Azure Policy](../../../policy/overview.md)-Definition zugewiesen wird, die Netzwerksicherheitsgruppen mit wenig einschränkenden Regeln überwacht. Regeln, die zu wenig einschränkend sind, können einen unbeabsichtigten Netzwerkzugriff ermöglichen und sollten überprüft werden. Mit dieser Blaupause werden auch drei Azure Policy-Definitionen zugewiesen, die nicht geschützte Endpunkte, Anwendungen und Speicherkonten überwachen. Endpunkte und Anwendungen, die nicht durch eine Firewall geschützt sind, sowie Speicherkonten mit uneingeschränktem Zugriff können einen unbeabsichtigten Zugriff auf Informationen im Informationssystem ermöglichen.

- [Vorschau]: Monitor permissive network access in Azure Security Center
- [Vorschau]: Monitor unprotected network endpoints in Azure Security Center
- [Vorschau]: Monitor unprotected web application in Azure Security Center
- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Richtlinien und Verfahren zur Übertragung von Informationen

Mit dieser Blaupause können Sie sicherstellen, dass die Übertragung von Informationen mit Azure-Diensten sicher ist, indem zwei [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die unsichere Verbindungen mit Speicherkonten und Redis Cache überwachen.

- Ausschließliche Aktivierung sicherer Verbindungen mit Redis Cache überwachen
- Sichere Übertragung in Speicherkonten überwachen

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Steuerungszuordnung des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“ vertraut gemacht haben, finden Sie nun in den folgenden Artikeln Informationen zur Architektur und zur Bereitstellung dieses Beispiels:

> [!div class="nextstepaction"]
> [Beispiel: Blaupause „ISO 27001: Gemeinsame Dienste“ – Übersicht](./index.md)
> [Beispiel: Blaupause „ISO 27001: Gemeinsame Dienste“ – Bereitstellungsschritte](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).