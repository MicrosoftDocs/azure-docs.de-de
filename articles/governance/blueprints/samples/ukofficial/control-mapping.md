---
title: Steuerungen des Blaupausenbeispiels für UK OFFICIAL und UK NHS
description: Zuordnungssteuerung für UK OFFICIAL- und UK NHS-Blaupausenbeispiele. Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: bf10bcf7064306094664c1ff560187ef77645d4d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627565"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Zuordnungssteuerung für UK OFFICIAL- und UK NHS-Blaupausenbeispiele

Im folgenden Artikel wird im Detail beschrieben, wie die UK OFFICIAL- und UK NHS-Blaupausenbeispiele den UK OFFICIAL- und UK NHS-Steuerungen zugeordnet werden. Weitere Informationen zu den Steuerungen finden Sie unter [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Die folgenden Zuordnungen beziehen sich auf die **UK OFFICIAL**- und **UK NHS**-Steuerungen. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie dann nach **\[Vorschau\] Audit UK OFFICIAL- und UK-NHS-Steuerungen**, wählen Sie diese Option aus, und stellen Sie spezielle VM-Erweiterungen bereit, um die integrierte Richtlinieninitiative für Auditanforderungen zu unterstützen.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../../../policy/overview.md)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../../../policy/how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und Azure Policy-Definitionen für dieses Konformitätsblaupausenbeispiel können sich im Laufe der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Schutz von Daten während der Übertragung

Durch die Zuweisung von [Azure-Richtliniendefinitionen](../../../policy/overview.md) zum Überwachen von unsicheren Verbindungen zu Speicherkonten und Redis Cache hilft Ihnen die Blaupause, den sicheren Informationsfluss mit Azure-Diensten zu gewährleisten.

- Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.
- Überwachungsergebnisse von Windows-Webservern anzeigen, die keine sicheren Kommunikationsprotokolle verwenden
- Zugriff auf Webanwendung nur über HTTPS gestatten
- Zugriff auf Funktions-App nur über HTTPS gestatten
- Auf API-Apps sollte nur über HTTPS zugegriffen werden können

## <a name="23-data-at-rest-protection"></a>2.3 Schutz von ruhenden Daten

Durch die Zuweisung von [Azure-Richtliniendefinitionen](../../../policy/overview.md) zum Erzwingen bestimmter kryptografischer Steuerungen und zum Überwachen der Verwendung schwacher kryptografischer Einstellungen hilft Ihnen die Blaupause, Ihre Richtlinien für die Verwendung von kryptografischen Steuerungen durchzusetzen. Wenn Sie wissen, wo Ihre Azure-Ressourcen möglicherweise nicht optimale kryptografische Konfigurationen aufweisen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Ressourcen entsprechend Ihrer Richtlinie zur Informationssicherheit konfiguriert sind. Insbesondere setzen die durch diese Blaupause zugewiesenen Richtlinien Folgendes voraus: Verschlüsselung für Speicherkonten von Data Lake, transparente Datenverschlüsselung für SQL-Datenbanken, Überwachung auf fehlende Verschlüsselung für Speicherkonten, SQL-Datenbanken, Festplatten virtueller Computer und Automatisierungskontenvariablen, Überwachung auf unsichere Verbindungen zu Speicherkonten und Redis Cache, Überwachung schwacher Passwortverschlüsselungen für virtuelle Computer sowie Überwachung der unverschlüsselten Kommunikation mit Service Fabric.

- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
- Automation-Kontovariablen sollten verschlüsselt werden.
- Service Fabric Cluster sollten die Eigenschaft ClusterProtectionLevel auf EncryptAndSign setzen
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.
- Bereitstellen der transparenten SQL DB-Datenbankverschlüsselung
- Verschlüsselung für Data Lake Store-Konten erforderlich
- Zulässige Standorte (hartcodiert mit UK SOUTH und UK WEST)
- Zulässige Standorte für Ressourcengruppen (hartcodiert mit UK SOUTH und UK WEST)

## <a name="52-vulnerability-management"></a>5.2 Verwaltung von Sicherheitsrisiken

Durch die Zuweisung von [Azure-Richtliniendefinitionen](../../../policy/overview.md), die fehlenden Endpunktschutz, fehlende System-Updates, Sicherheitsrisiken im Betriebssystem sowie Sicherheitsrisiken bei SQL und virtuellen Computern überwachen, hilft Ihnen die Blaupause bei der Verwaltung von Sicherheitsrisiken im Informationssystem. Diese Erkenntnisse bieten Echtzeitinformationen zum Sicherheitsstatus Ihrer bereitgestellten Ressourcen und ermöglichen die Priorisierung von Aktionen zur Problembehebung.

- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen
- Systemupdates sollten auf Ihren Computern installiert sein.
- Systemupdates für VM-Skalierungsgruppen sollten installiert werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Für Ihre verwalteten SQL-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Für Ihre verwalteten SQL-Instanzen sollte Advanced Data Security aktiviert werden
- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.

## <a name="53-protective-monitoring"></a>5.3 Vorbeugende Überwachung

Durch die Zuweisung von [Azure-Richtliniendefinitionen](../../../policy/overview.md), die eine vorbeugende Überwachung auf nicht eingeschränkten Zugriff, von Zulassungslistenaktivitäten und Bedrohungen ermöglichen, hilft Ihnen die Blaupause beim Schutz der Ressourcen des Informationssystems.

- Netzwerkzugriff auf Speicherkonten einschränken
- Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein.
- Überwachen von virtuellen Computern ohne konfigurierte Notfallwiederherstellung
- Azure DDoS Protection Standard muss aktiviert sein
- Advanced Threat Protection-Typen sollten in den Advanced Data Security-Einstellungen für verwaltete SQL-Instanzen auf „Alle“ festgelegt werden.
- Advanced Threat Protection-Typen sollten in den Advanced Data Security-Einstellungen für SQL Server auf „Alle“ festgelegt werden.
- Bereitstellen von Bedrohungserkennung auf SQL-Servern
- Bereitstellung der standardmäßigen Microsoft IaaSAntimalware-Erweiterung für Windows Server

## <a name="9-secure-user-management"></a>9 Sichere Benutzerverwaltung 

Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat.
Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Durch die Zuweisung von [Azure-Richtliniendefinitionen](../../../policy/overview.md) zum Überwachen von externen Konten mit Besitzer- und/oder Lese-/Schreibberechtigungen sowie von Konten mit Besitzer-, Lese- und/oder Schreibberechtigungen, für die keine mehrstufige Authentifizierung (MFA) aktiviert ist, hilft Ihnen die Blaupause, Zugriffsrechte einzuschränken und zu steuern.

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden

## <a name="10-identity-and-authentication"></a>10 Identität und Authentifizierung

Durch die Zuweisung von [Azure-Richtliniendefinitionen](../../../policy/overview.md) zum Überwachen von externen Konten mit Besitzer- und/oder Lese-/Schreibberechtigungen sowie von Konten mit Besitzer-, Lese- und/oder Schreibberechtigungen, für die keine mehrstufige Authentifizierung (MFA) aktiviert ist, hilft Ihnen die Blaupause, Zugriffsrechte einzuschränken und zu steuern.

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden

Die Blaupause weist Azure-Richtliniendefinitionen zu, um die Verwendung der Azure Active Directory-Authentifizierung für SQL-Server und Service Fabric zu überwachen. Die Verwendung der Azure Active Directory-Authentifizierung ermöglicht eine vereinfachte Verwaltung von Berechtigungen und eine zentralisierte Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten.

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden
- Service Fabric-Cluster sollten nur Azure Active Directory für die Clientauthentifizierung verwenden.

Die Blaupause weist auch Azure-Richtliniendefinitionen zu Auditkonten zu, die zur Überprüfung priorisiert werden sollten (einschließlich veralteter Konten und externer Konten). Bei Bedarf kann die Anmeldung für Konten blockiert werden (oder Konten können entfernt werden), wodurch die Zugriffsrechte für Azure-Ressourcen sofort entfernt werden.
Die Blaupause weist zwei Azure-Richtliniendefinitionen zum Überwachen des veralteten Kontos zu, das bei der Entfernung berücksichtigt werden sollten.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.

Die Blaupause weist auch eine Azure-Richtliniendefinition zum Überwachen der Dateiberechtigungen für Passwörter von virtuellen Linux-Computern zu, um bei einer fehlerhaften Festlegung eine Warnung auszugeben. Dieses Design ermöglicht Ihnen Korrekturmaßnahmen, die sicherstellen, dass Authentifikatoren nicht kompromittiert werden.

- Überwachungsergebnisse von Linux-VMs anzeigen, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind

Durch die Zuweisung von Azure-Richtliniendefinitionen zum Überwachen virtueller Windows-Computer, die keine Mindestsicherheitsanforderungen oder andere Anforderungen an Passwörter erfüllen, hilft Ihnen die Blaupause bei der Durchsetzung sicherer Passwörter. Aufgrund der Informationen zu virtuellen Computern, die gegen die Richtlinie zur Kennwortsicherheit verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Kennwörter für alle Benutzerkonten auf virtuellen Computern mit der Richtlinie konform sind.

- Überwachungsergebnisse von Windows-VMs anzeigen, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist
- Überwachungsergebnisse von Windows-VMs anzeigen, für die kein maximales Kennwortalter von 70 Tagen gilt
- Überwachungsergebnisse von Windows-VMs anzeigen, für die kein minimales Kennwortalter von 1 Tag gilt
- Überwachungsergebnisse von Windows-VMs anzeigen, für die keine Mindestkennwortlänge von 14 Zeichen festgelegt ist
- Überwachungsergebnisse von Windows-VMs anzeigen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen

Durch die Zuweisung von Azure-Richtliniendefinitionen hilft Ihnen die Blaupause auch, den Zugriff auf Azure-Ressourcen zu steuern. Mit diesen Richtlinien wird die Verwendung von Ressourcentypen und Konfigurationen überwacht, die einen weniger restriktiven Zugriff auf Ressourcen ermöglichen. Durch Kenntnis der Ressourcen, die gegen diese Richtlinien verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass der Zugriff auf Azure-Ressourcen auf autorisierte Benutzer beschränkt ist.

- Überwachungsergebnisse von Linux-VMs anzeigen, die Konten ohne Kennwörter verwenden
- Überwachungsergebnisse von Linux-VMs anzeigen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Speicherkonten sollten zu neuen Azure Resource Manager-Ressourcen migriert werden.
- VMs sollten zu neuen Azure Resource Manager-Ressourcen migriert werden
- Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden

## <a name="11-external-interface-protection"></a>11 Schutz von externen Schnittstellen

Sofern Sie nicht mehr als 25 Richtlinien für eine angemessene sichere Benutzerverwaltung verwenden, unterstützt Sie die Blaupause durch die Zuweisung einer [Azure-Richtliniendefinition](../../../policy/overview.md), die Speicherkonten mit nicht eingeschränktem Zugriff überwacht, beim Schutz von Dienstschnittstellen vor unbefugtem Zugriff.
Speicherkonten mit nicht eingeschränktem Zugriff können einen unbeabsichtigten Zugriff auf die im Informationssystem enthaltenen Informationen zulassen. Die Blaupause weist auch eine Richtlinie zu, die adaptive Anwendungssteuerungen auf virtuellen Maschinen ermöglicht.

- Netzwerkzugriff auf Speicherkonten einschränken
- Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein.
- Zugriff über Endpunkt mit Internetzugriff sollte eingeschränkt werden
- Auf virtuelle Computer mit Internetzugang müssen Empfehlungen zur adaptiven Netzwerkhärtung angewendet werden.
- Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.
- Verwaltungsports virtueller Computer müssen mit der Just-In-Time-Netzwerkzugriffssteuerung geschützt werden
- Remotedebuggen sollte für Funktions-Apps deaktiviert sein.
- Remotedebuggen sollte für Webanwendungen deaktiviert sein.
- Remotedebuggen sollte für API-Apps deaktiviert sein.

## <a name="13-audit-information-for-users"></a>13 Überwachungsinformationen für Benutzer

Diese Blaupause hilft Ihnen, sicherzustellen, dass Systemereignisse protokolliert werden, indem Sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweisen, die die Einstellungen des Überwachungsprotokolls auf Azure-Ressourcen überprüfen.
Mit einer zugewiesenen Richtlinie wird zudem überwacht, dass virtuelle Computer keine Protokolle an einen angegebenen Log Analytics-Arbeitsbereich senden.

- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Überwachen der Diagnoseeinstellung
- \[Vorschau\]: Bereitstellen des Log Analytics-Agents für Linux-VMs
- \[Vorschau\]: Bereitstellen des Log Analytics-Agents für Windows-VMs
- Bereitstellen von Network Watcher beim Erstellen virtueller Netzwerke


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die Steuerungszuordnung der UK OFFICIAL- und UK NHS-Blaupausen kennen, lesen Sie die folgenden Artikel, um sich einen Überblick zu verschaffen und mehr über die Bereitstellung dieses Beispiels zu erfahren:

> [!div class="nextstepaction"]
> [UK OFFICIAL- und UK NHS-Blaupausen – Übersicht](./index.md)
> [UK OFFICIAL- und UK NHS-Blaupausen – Bereitstellungsschritte](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
