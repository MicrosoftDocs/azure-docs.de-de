---
title: Verwalten nach der Migration
titleSuffix: Azure SQL Database
description: Hier erfahren Sie, wie Sie Ihre Einzel- und Pooldatenbanken nach der Migration zu Azure SQL-Datenbank verwalten.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: b34ac24cb26bf5db4a49a5ad5b531deb252f4695
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446123"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Neuer DBA in der Cloud – Verwalten von Azure SQL-Datenbank nach der Migration
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Der Umstieg von der herkömmlichen, selbstverwalteten, automatisch gesteuerten Umgebung auf eine PaaS-Umgebung kann anfangs eine Herausforderung darstellen. Als App-Entwickler oder DBA möchten Sie wissen, welche Plattformfunktionen in erster Linie dafür sorgen, dass Ihre Anwendung verfügbar, leistungsfähig, sicher und zuverlässig ist – und zwar zu jeder Zeit. Dieser Artikel soll Aufklärung leisten. Hier werden die Ressourcen kurz zusammengefasst, und Sie erhalten hilfreiche Informationen zur optimalen Verwendung der wichtigsten Funktionen von Azure SQL-Datenbank mit Einzel- und Pooldatenbanken. So können Sie Ihre Anwendung effizient verwalten und ausführen, um optimale Ergebnisse in der Cloud zu erzielen. Die typische Zielgruppe für diesen Artikel sind Nutzer, die

- die Migration ihrer Anwendung(en) zu Azure SQL-Datenbank evaluieren – Modernisierung der Anwendung(en)
- ihre Anwendung(en) gerade migrieren – Szenario für laufende Migration
- die Migration zu Azure SQL-Datenbank gerade abgeschlossen haben – Neuer DBA in der Cloud

Dieser Artikel behandelt einige der Hauptmerkmale von Azure SQL-Datenbank als Plattform, die bei der Arbeit mit Einzel- und Pooldatenbanken in Pools für elastische Datenbanken optimale Unterstützung bietet. darunter:

- Überwachen von Datenbanken über das Azure-Portal
- Geschäftskontinuität und Notfallwiederherstellung (Business Continuity Disaster Recovery, BCDR)
- Sicherheit und Compliance
- Intelligente Datenbanküberwachung und -wartung
- Datenverschiebung

## <a name="monitor-databases-using-the-azure-portal"></a>Überwachen von Datenbanken über das Azure-Portal

Im [Azure-Portal](https://portal.azure.com/) können Sie die Nutzung einer einzelnen Datenbank überwachen, indem Sie die Datenbank auswählen und auf das Diagramm **Überwachung** klicken. Dadurch wird das Fenster **Metrik** geöffnet, in dem Sie durch Klicken auf die Schaltfläche **Diagramm bearbeiten** Änderungen vornehmen können. Fügen Sie die folgenden Metriken hinzu:

- CPU-Prozentsatz
- DTU-Prozentsatz
- E/A-Prozentsatz für Daten
- Datenbankgröße als Prozentsatz

Nachdem Sie diese Metriken hinzugefügt haben, können Sie sie im Diagramm **Überwachung** mit weiteren Informationen im Fenster **Metrik** anzeigen. Alle vier Metriken geben die durchschnittliche prozentuale Nutzung relativ zur **DTU** der Datenbank an. In den Artikeln [DTU-basiertes Kaufmodell für Azure SQL-Datenbank](service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](service-tiers-vcore.md) finden Sie weitere Informationen zu Dienstebenen.  

![Tarifbezogenes Überwachen der Datenbankleistung.](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

Sie können zudem Benachrichtigungen für die Leistungsmetriken konfigurieren. Klicken Sie im Fenster **Metrik** auf die Schaltfläche **Warnung hinzufügen**. Befolgen Sie die Anweisungen des Assistenten, um die Benachrichtigung zu konfigurieren. Sie haben die Möglichkeit, Benachrichtigungen für den Fall zu konfigurieren, dass Metriken einen Schwellenwert überschreiten oder unterschreiten.

Wenn Sie beispielsweise einen Anstieg der Workload Ihrer Datenbank erwarten, können Sie eine E-Mail-Benachrichtigung konfigurieren, die immer dann gesendet wird, wenn eine der Leistungsmetriken der Datenbank 80 % erreicht. Sie können dies als Frühwarnung verwenden, um zu ermitteln, wann Sie eventuell zur nächsthöheren Computegröße wechseln müssen.

Anhand der Leistungsmetriken können Sie auch ermitteln, ob Sie möglicherweise eine Herabstufung auf eine niedrigere Computegröße vornehmen können. Angenommen, Sie verwenden eine Datenbank der Dienstebene "Standard S2", und alle Leistungsmetriken zeigen, dass die Datenbank zu jedem Zeitpunkt durchschnittlich nicht mehr als 10 % nutzt. Hier ist es wahrscheinlich, dass sich die Datenbank auch mit der Dienstebene "Standard S1" verwenden lässt. Bevor Sie sich für einen Wechsel zu einer niedrigeren Computegröße entscheiden, müssen Sie aber auch Workloads berücksichtigen, die Spitzen oder Schwankungen aufweisen können.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Geschäftskontinuität und Notfallwiederherstellung (Business Continuity Disaster Recovery, BCDR)

Dank der Funktionen für Geschäftskontinuität und Notfallwiederherstellung können Sie Ihre Geschäfte in einem Notfall wie gewohnt fortführen. Ein Notfall könnte auf Datenbankebene auftreten (ein Nutzer löscht z. B. versehentlich eine unternehmenswichtige Tabelle) oder das gesamte Datencenter betreffen (bei einer regionalen Katastrophe wie einem Tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Wie werden Sicherungen für SQL-Datenbank erstellt und verwaltet?

Sie erstellen unter Azure SQL-Datenbank keine Sicherungen, weil es nicht nötig ist. Ihre Datenbanken werden von SQL-Datenbank automatisch gesichert, sodass Sie sich nicht länger um das Planen, Erstellen und Verwalten von Sicherungen kümmern müssen. Die Plattform führt jede Woche eine vollständige Sicherung, alle paar Stunden eine differenzielle Sicherung und alle fünf Minuten eine Transaktionsprotokollsicherung durch. So ist gewährleistet, dass Daten nach einem Notfall effizient wiederhergestellt werden können und Datenverluste gering bleiben. Die erste vollständige Sicherung erfolgt direkt nach dem Erstellen einer Datenbank. Diese Sicherungen stehen Ihnen während eines bestimmten Zeitraums zur Verfügung, der sogenannten „Beibehaltungsdauer“, die je nach ausgewählter Dienstebene variiert. SQL-Datenbank bietet mit der [Zeitpunktwiederherstellung (Point in Time Recovery, PITR)](recovery-using-backups.md#point-in-time-restore) die Möglichkeit, Daten bis zu einem beliebigen Zeitpunkt innerhalb dieser Beibehaltungsdauer wiederherzustellen.

|Dienstebene|Beibehaltungsdauer in Tagen|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Außerdem können Sie über das Feature für die [langfristige Aufbewahrung](long-term-retention-overview.md) (Long-Term Retention, LTR) Sicherungsdateien über einen wesentlich längeren Zeitraum (bis zu zehn Jahre) aufbewahren und Daten zu einem beliebigen Zeitpunkt innerhalb dieser Frist aus den Sicherungen wiederherstellen. Darüber hinaus werden Datenbanksicherungen in georeplizierten Speichersystemen vorgehalten, um bei einer regionalen Katastrophe Datenresilienz zu gewährleisten. Die Sicherungen können auch in jeder Azure-Region zu einem beliebigen Zeitpunkt innerhalb der Beibehaltungsdauer wiederhergestellt werden. Weitere Informationen finden Sie unter [Business continuity overview (Übersicht über die Geschäftskontinuität)](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Wie wird Geschäftskontinuität bei einem Notfall in den Datencentern oder einer regionalen Katastrophe gewährleistet?

Da Ihre Datenbanksicherungen in einem georeplizierten Speicher gespeichert werden, ist sichergestellt, dass Sie die Sicherungen bei einem regionalen Notfall in einer anderen Azure-Region wiederherstellen können. Dieser Vorgang wird als Geowiederherstellung bezeichnet. Das RPO (Recovery Point Objective) dafür beträgt im Allgemeinen weniger als 1 Stunde und die geschätzte Wiederherstellungszeit (Estimated Recovery Time, ERT – wenige Minuten bis Stunden).

Für unternehmenskritische Datenbanken bietet Azure SQL-Datenbank aktive Georeplikation. Dabei wird im Wesentlichen eine georeplizierte sekundäre Kopie der ursprünglichen Datenbank in einer anderen Region erstellt. Angenommen, Ihre Datenbank wird anfangs in der Azure-Region „USA, Westen“ gehostet, und Sie benötigen Resilienz bei regionalen Notfällen. In diesem Fall erstellen Sie ein aktives geografisches Replikat der Datenbank aus „USA, Westen“ beispielsweise in „USA, Osten“. Falls dann ein Notfall in „USA, Westen“ auftritt, können Sie ein Failover auf die Region „USA, Osten“ ausführen. Die Konfiguration in einer Gruppe für automatische Failover bietet weitere Vorteile, da so sichergestellt wird, dass die Datenbank bei einem Notfall automatisch ein Failover auf das sekundäre Replikat in der Region „USA, Osten“ ausführt. Das RPO dafür beträgt weniger als 5 Sekunden und die ERT weniger als 30 Sekunden.

Wenn keine Gruppe für automatische Failover konfiguriert wird, muss Ihre Anwendung aktiv auf eine Katastrophe überwachen und ein Failover auf eine sekundäre Datenbank einleiten, wenn es zu einer Katastrophe kommen sollte. Sie können bis zu vier aktive geografische Replikate in verschiedenen Azure-Regionen erstellen. Allerdings haben Sie noch weitere Möglichkeiten. Sie können auch schreibgeschützt auf diese sekundären aktiven geografischen Replikate zugreifen. Dies ist sehr praktisch, um die Latenz bei einem Szenario mit einer geografisch verteilten Anwendung zu reduzieren.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Inwiefern ändert sich der lokale Notfallwiederherstellungsplan mit SQL-Datenbank?

Zusammenfassend lässt sich feststellen, dass die Verfügbarkeit bei der SQL Server-Konfiguration mithilfe von Features wie Failoverclustering, Datenbankspiegelung, Transaktionsreplikation und Protokollversand aktiv verwaltet werden muss und dass zur Aufrechterhaltung der Geschäftskontinuität Sicherungen vorgehalten und verwaltet werden müssen. Dank SQL-Datenbank werden diese Aufgaben von der Plattform übernommen, sodass Sie sich auf die Entwicklung und Optimierung Ihrer Datenbankanwendung konzentrieren können und sich weniger um die Verwaltung von Notfallszenarien kümmern müssen. Sicherungs- und Notfallwiederherstellungspläne können mit wenigen Klicks im Azure-Portal (bzw. mithilfe weniger Befehle über die PowerShell-APIs) konfiguriert und ausgeführt werden.

Weitere Informationen zur Notfallwiederherstellung finden Sie unter: [Notfallwiederherstellung für Azure SQL-Datenbank 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Sicherheit und Compliance

Sicherheit und Datenschutz haben in SQL-Datenbank einen hohen Stellenwert. Die Sicherheit in SQL-Datenbank kann auf Datenbankebene und auf Plattformebene angewendet werden und lässt sich am besten anhand der verschiedenen Ebenen erklären. Sie haben auf jeder Ebene die Möglichkeit, Ihre Anwendung zu kontrollieren und optimal zu schützen. Die Ebenen lauten:

- Identität und Authentifizierung ([SQL-Authentifizierung und Azure AD-Authentifizierung [Azure Active Directory]](logins-create-manage.md))
- Überwachung von Aktivitäten ([Überwachung](../../azure-sql/database/auditing-overview.md) und [Bedrohungserkennung](threat-detection-configure.md))
- Schutz von Daten ([Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) und [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine))
- Kontrollierter Zugriff auf sensible und vertrauliche Daten ([Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) und [dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking))

[Azure Security Center](https://azure.microsoft.com/services/security-center/) bietet eine zentralisierte Sicherheitsverwaltung über verschiedene Workloads hinweg an, die in Azure, lokal und in anderen Clouds ausgeführt werden können. Sie können nachvollziehen, ob grundlegende Schutzmaßnahmen von SQL-Datenbank, wie [Überwachung](../../azure-sql/database/auditing-overview.md) und [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) für alle Ressourcen konfiguriert wurden, und anhand Ihrer eigenen Anforderungen Richtlinien erstellen.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Welche Benutzerauthentifizierungsmethoden werden in SQL-Datenbank angeboten?

SQL-Datenbank bietet zwei Authentifizierungsmethoden:

- [Azure Active Directory-Authentifizierung](authentication-aad-overview.md)
- [SQL-Authentifizierung](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Die herkömmliche Windows-Authentifizierung wird nicht unterstützt. Azure Active Directory (Azure AD) ist ein Dienst für die zentrale Identitäts- und Zugriffsverwaltung. Er ermöglicht sämtlichen Mitarbeitern Ihrer Organisation den bequemen Zugriff per einmaliger Anmeldung (SSO, Single Sign-On). Dies vereinfacht die Authentifizierung, da die Anmeldeinformationen für alle Azure-Dienste gemeinsam genutzt werden. 

Azure AD unterstützt [Azure AD Multi-Factor Authentication](authentication-mfa-ssms-overview.md) und lässt sich mit [wenigen Klicks](../../active-directory/hybrid/how-to-connect-install-express.md) mit Windows Server Active Directory integrieren. Die SQL-Authentifizierung funktioniert genau so, wie Sie es aus der Vergangenheit gewohnt sind. Benutzer können sich mit ihrem Benutzernamen und Kennwort bei beliebigen Datenbanken auf einem bestimmten Server authentifizieren. Dadurch können SQL-Datenbank und Azure Synapse Analytics außerdem eine mehrstufige Authentifizierung und Benutzerkonten für Gäste innerhalb einer Azure AD-Domäne anbieten. Wenn Sie bereits über eine lokale Active Directory verfügen, können Sie dieses Verzeichnis mit Azure Active Directory verbinden und es auf Azure erweitern.

|**Wenn Sie...**|**SQL-Datenbank/Azure Synapse Analytics**|
|---|---|
|Azure Active Directory (Azure AD) lieber nicht in Azure verwenden möchten,|verwenden Sie [SQL-Authentifizierung](security-overview.md).|
|AD auf einem lokalen SQL-Server verwendet haben,|[Verbinden Sie AD mit Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md), und verwenden Sie die Azure AD-Authentifizierung. So können Sie auf die Funktion „Einmaliges Anmelden“ zurückgreifen.|
|eine mehrstufige Authentifizierung erzwingen müssen,|muss über den [bedingten Zugriff von Microsoft](conditional-access-configure.md) MFA als Richtlinie festgelegt sein, und Sie müssen die [universelle Authentifizierung mit MFA-Unterstützung in Azure AD](authentication-mfa-ssms-overview.md) verwenden.|
|über Gastkonten aus Microsoft-Konten (live.com, outlook.com) oder anderen Domänen (gmail.com) verfügen,|verwenden Sie [die universelle Authentifizierung von Azure AD](authentication-mfa-ssms-overview.md) in SQL-Datenbank/Data Warehouse, die die [Zusammenarbeit zwischen Azure AD und B2B unterstützt](../../active-directory/external-identities/what-is-b2b.md).|
|mit Ihren Azure AD-Anmeldeinformationen aus einer Verbunddomäne bei Windows angemeldet sind,|verwenden Sie [Azure AD integrated authentication (die integrierte Azure AD-Authentifizierung)](authentication-aad-configure.md).|
|mit Ihren Anmeldeinformationen aus einer Domäne, die nicht mit Azure verbunden ist, bei Windows angemeldet sind,|verwenden Sie [Azure AD integrated authentication (die integrierte Azure AD-Authentifizierung)](authentication-aad-configure.md).|
|über Dienste auf mittlerer Ebene verfügen, die eine Verbindung mit SQL-Datenbank oder Azure Synapse Analytics herstellen müssen,|verwenden Sie [Azure AD integrated authentication (die integrierte Azure AD-Authentifizierung)](authentication-aad-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Wie werden Verbindungen mit der Datenbank beschränkt oder kontrolliert?

Es gibt mehrere Methoden, um Verbindungszugriffe für Ihre Anwendung optimal zu organisieren.

- Firewallregeln
- VNET-Dienstendpunkte
- Reservierte IP-Adressen

#### <a name="firewall"></a>Firewall

Eine Firewall verhindert externe Zugriffe auf Ihren Server, indem nur bestimmten Entitäten Zugang zu Ihrem Server gewährt wird. Standardmäßig sind alle Verbindungen mit dem Server und darauf enthaltenen Datenbanken unzulässig – es sei denn (optionally7), es handelt sich um Verbindungen von anderen Azure-Diensten. Mit einer Firewallregel können Sie den Serverzugriff auf bestimmte Entitäten beschränken (z. B. auf einen Entwicklercomputer), indem Sie zulassen, dass der Computer mit der jeweiligen IP-Adresse die Firewall passieren darf. Zudem können Sie einen Bereich von IP-Adressen angeben, über die Sie den Zugriff auf den Server gewähren möchten. Beispielsweise können Sie die IP-Adressen aller Entwicklercomputer in Ihrer Organisation in einem Schritt hinzufügen, indem Sie auf der Seite für Firewalleinstellungen einen Bereich angeben.

Firewallregeln können auf Server- oder auf Datenbankebene erstellt werden. IP-Firewallregeln auf Serverebene können mithilfe des Azure-Portals oder mit SSMS erstellt werden. Weitere Informationen zum Festlegen von Firewallregeln auf Server- und Datenbankebene finden Sie unter: [Erstellen von IP-Firewallregeln in SQL-Datenbank](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Dienstendpunkte

Ihre Datenbank ist standardmäßig so konfiguriert, dass die Option „Azure-Diensten Zugriff auf den Server erlauben“ aktiviert ist. Das bedeutet, dass alle virtuellen Computer in Azure versuchen können, eine Verbindung mit Ihrer Datenbank herzustellen. Die Verbindungsversuche müssen jedoch noch authentifiziert werden. Wenn Sie verhindern möchten, dass über jede Azure-IP-Adresse auf Ihre Datenbank zugegriffen werden kann, deaktivieren Sie die Option „Azure-Diensten Zugriff auf Server erlauben“. Außerdem können Sie [VNET-Dienstendpunkte](vnet-service-endpoint-rule-overview.md) konfigurieren.

Mit Dienstendpunkten können Sie wichtige Azure-Ressourcen ausschließlich für Ihr eigenes privates virtuelles Netzwerk in Azure verfügbar machen. Auf diese Weise verhindern Sie praktisch den öffentlichen Zugriff auf Ihre Ressourcen. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und Azure erfolgt weiterhin über das Azure-Backbonenetzwerk. Ohne Dienstendpunkte würden Pakete mit erzwungenem Tunneling geroutet werden. Ihr virtuelles Netzwerk erzwingt, dass der an Ihre Organisation gerichtete Internetdatenverkehr und der Datenverkehr der Azure-Dienste über dieselbe Route transportiert werden. Mit Dienstendpunkten können Sie das Routing verbessern, da die Pakete direkt von Ihrem virtuellen Netzwerk zum Dienst im Azure-Backbonenetzwerk transportiert werden.

![VNET-Dienstendpunkte](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Reservierte IP-Adressen

Sie können auch [reservierte IP-Adressen](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) für Ihre virtuellen Computer bereitstellen und diese IP-Adressen für spezifische virtuelle Computer den Firewalleinstellungen des Servers hinzufügen. Das Zuweisen von reservierten IP-Adressen erspart Ihnen das Aktualisieren von Firewallregeln, wenn sich die IP-Adressen ändern.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Über welchen Port werden Verbindungen mit SQL-Datenbank hergestellt?

Über Port 1433. SQL-Datenbank kommuniziert über diesen Port. Um Verbindungen aus einem Unternehmensnetzwerk herzustellen, müssen Sie in den Firewalleinstellungen Ihrer Organisation eine ausgehende Regel hinzufügen. Port 1433 sollte grundsätzlich nicht außerhalb von Azure verfügbar gemacht werden.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Wie werden Aktivitäten auf dem Server und in der Datenbank in SQL-Datenbank überwacht und geregelt?

#### <a name="sql-database-auditing"></a>SQL-Datenbanküberwachung

In SQL-Datenbank kann die Überwachung aktiviert werden, um Datenbankereignisse nachzuverfolgen. Die [SQL-Datenbanküberwachung](../../azure-sql/database/auditing-overview.md) zeichnet Datenbankereignisse auf und schreibt sie in eine Überwachungsprotokolldatei unter Ihrem Azure Storage-Konto. Die Überwachung bietet optimale Unterstützung bei der Verfolgung möglicher Sicherheitsverletzungen und Richtlinienverstöße, bei der Einhaltung gesetzlicher Bestimmungen usw. Sie können bestimmte Ereigniskategorien definieren und konfigurieren, die Sie überwachen möchten, und auf Grundlage Ihrer Definitionen vorkonfigurierte Berichte abrufen oder ein Dashboard nutzen, um sich einen Überblick über die in der Datenbank auftretenden Ereignisse zu verschaffen. Die Überwachungsrichtlinien können auf Datenbank- oder auf Serverebene angewendet werden. Einen Leitfaden zum Aktivieren der Überwachung für Ihre Server/Datenbank finden Sie unter: [Aktivieren der SQL-Datenbanküberwachung](secure-database-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Bedrohungserkennung

Die [Bedrohungserkennung](threat-detection-configure.md) bietet eine einfache Möglichkeit, auf die von der Überwachung erkannten Sicherheits- oder Richtlinienverletzungen zu reagieren. Sie müssen kein Sicherheitsexperte sein, um potenzielle Bedrohungen oder Verstöße innerhalb Ihres Systems zu beseitigen. Die Bedrohungserkennung ist auch in der Lage, die Einschleusung von SQL-Befehlen zu erkennen. Mit der Einschleusung von SQL-Befehlen wird versucht, Daten zu manipulieren oder zu kompromittieren. Die Methode wird im Allgemeinen für Angriffe auf Datenbankanwendungen eingesetzt. Die Bedrohungserkennung führt mehrere verschiedene Algorithmen aus, die potentielle Sicherheitsrisiken, Angriffe mit Einschleusung von SQL-Befehlen und ungewöhnliche Muster für den Zugriff auf die Datenbank (wie der Zugriff von einem ungewöhnlichen Ort oder einem unbekannten Prinzipal aus) ermitteln. Sicherheitsbeauftragte oder andere vorgesehene Administratoren erhalten eine E-Mail-Benachrichtigung, wenn eine Bedrohung auf der Datenbank erkannt wird. Jede Benachrichtigung enthält Details zur verdächtigen Aktivität und Empfehlungen zur weiteren Untersuchung und Abwendung der Bedrohung. Informationen zum Aktivieren der Bedrohungserkennung finden Sie unter: [Aktivieren der Bedrohungserkennung](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Welchen grundsätzlichen Schutz bietet SQL-Datenbank für Daten?

Die Verschlüsselung bietet wirkungsvollen Schutz vor unbefugten Zugriffen auf sensible Daten. Ohne Entschlüsselungsschlüssel sind verschlüsselte Daten für Angreifer wertlos. Auf diese Weise bietet sie zusätzlichen Schutz und ergänzt die bereits in SQL-Datenbank integrierten Sicherheitsstufen. Der Schutz Ihrer Daten in SQL-Datenbank umfasst zwei Datenkategorien:

- In Daten- und Protokolldateien „ruhende“ Daten
- Aktive Daten

Ruhende Daten in den Daten- und Protokolldateien auf einem Speichersubsystem sind in SQL-Datenbank standardmäßig per [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) jederzeit vollständig verschlüsselt. Ihre Sicherungen werden ebenfalls verschlüsselt. Bei Verwendung von TDE sind keine Änderungen an den Anwendungen erforderlich, die auf diese Daten zugreifen. Wie der Name erkennen lässt, erfolgen die Verschlüsselung und Entschlüsselung transparent.
Zum Schutz sensibler Daten, die aktiv oder ruhend sein können, bietet SQL-Datenbank eine Funktion namens [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE ist eine Form der clientseitigen Verschlüsselung, die sensible Spalten in Ihrer Datenbank verschlüsselt (sodass sie für Datenbankadministratoren und nicht autorisierte Benutzer als Chiffretext dargestellt werden). Der Server empfängt zunächst die verschlüsselten Daten. Der Schlüssel für die Always Encrypted-Verschlüsselung wird ebenfalls auf Clientseite gespeichert, damit die sensiblen Spalten nur von autorisierten Clients entschlüsselt werden können. Server- und Datenadministratoren haben keinen Zugriff auf die sensiblen Daten, da die Verschlüsselungsschlüssel auf dem Client gespeichert sind. AE wendet eine End-to-End-Verschlüsselung auf sensible Tabellenspalten an – von nicht autorisierten Clients bis zum physischen Datenträger. Da AE aktuell Übereinstimmungsvergleiche unterstützt, können DBAs im Rahmen ihrer SQL-Befehle weiterhin verschlüsselte Spalten abfragen. Diese Verschlüsselungsmethode kann mit einer Vielzahl von Schlüsselspeicheroptionen wie [Azure Key Vault](always-encrypted-azure-key-vault-configure.md), dem Windows-Zertifikatspeicher sowie lokalen Hardware-Sicherheitsmodulen verwendet werden.

|**Merkmale**|**Always Encrypted**|**Transparente Datenverschlüsselung (TDE)**|
|---|---|---|
|**Umfang der Verschlüsselung**|Komplettlösung|Ruhende Daten|
|**Server kann auf sensible Daten zuzugreifen.**|Nein|Ja, da die Verschlüsselung ruhende Daten betrifft.|
|**Zulässige T-SQL-Vorgänge**|Gleichheitsvergleich|Die gesamte T-SQL-Oberfläche ist verfügbar.|
|**Sind zur Verwendung der Funktion Änderungen an Apps erforderlich?**|Wenig|Sehr wenig|
|**Granularität der Verschlüsselung**|Spaltenebene|Datenbankebene|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Wie wird der Zugriff auf sensible Daten in der Datenbank beschränkt?

Jede Anwendung verfügt zu einem gewissen Grad über sensible Daten in der Datenbank, die vor unbefugtem Zugriff geschützt werden müssen. Bestimmte Mitarbeiter innerhalb der Organisation benötigen Zugang zu diesen Daten, während sie anderen verborgen bleiben sollten. Ein Beispiel sind Mitarbeitergehälter. Ein Vorgesetzter muss auf die Gehaltsinformationen seiner Mitarbeiter zugreifen können; einzelne Teammitglieder dürfen allerdings keinen Zugang zu den Gehaltsinformationen ihrer Kollegen haben. In einem weiteren Szenario könnten Datenentwickler in der Entwicklungs- oder Testphase mit sensiblen Daten interagieren, z. B. mit Sozialversicherungsnummern von Kunden. Auch diese Informationen müssen dem Entwickler nicht verfügbar gemacht werden. In diesen Fällen müssen Ihre sensiblen Daten entweder maskiert oder überhaupt nicht bereitgestellt werden. SQL-Datenbank bietet zwei Methoden, um nicht autorisierten Benutzern den Zugang zu sensiblen Daten zu verweigern:

Die [dynamische Datenmaskierung](dynamic-data-masking-overview.md) ist eine Funktion zum Maskieren von Daten, mit der Sie die Offenlegung sensibler Daten beschränken können. So werden die Daten auf der Anwendungsschicht für nicht berechtigte Benutzer maskiert. Sie definieren eine Maskierungsregel, die ein Maskierungsmuster erstellen (z.B. zum Anzeigen nur der letzten vier Ziffern einer nationalen ID-SSN in der Form: XXX-XX-0000 und Markieren des größten Teils als „X“) und ermitteln kann, welche Benutzer von der Maskierungsregel ausgeschlossen werden sollen. Die Daten werden dynamisch maskiert. Für die verschiedenen Datenkategorien stehen mehrere Maskierungsfunktionen zur Verfügung. Dank der dynamischen Datenmaskierung können sensible Daten in Ihrer Datenbank automatisch erkannt und maskiert werden.

Durch die [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) können Sie den Zugriff auf Zeilenebene steuern. Dies bedeutet, dass bestimmte Zeilen in einer Datenbanktabelle basierend auf dem Benutzer ausgeblendet werden, der die Abfrage ausführt (Gruppenmitgliedschaft oder Ausführungskontext). Die Zugangsbeschränkung wird nicht auf Anwendungsebene, sondern auf Datenbankebene festgelegt, damit die App-Logik vereinfacht wird. Zunächst erstellen Sie ein Filterprädikat, das Zeilen herausfiltert, die nicht angezeigt werden sollen. Als Nächstes erstellen Sie die Sicherheitsrichtlinie, die definiert, wer Zugang zu den Zeilen erhält. Zuletzt führt der Endbenutzer seine Abfrage aus und kann die eingeschränkten Zeilen abhängig von seinen Benutzerberechtigungen entweder sehen oder nicht.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Wie werden Verschlüsselungsschlüssel in der Cloud verwaltet?

Es gibt sowohl für die Methode Always Encrypted (clienstseitige Verschlüsselung) als auch für Transparent Data Encryption (Verschlüsselung ruhender Daten) Optionen zur Schlüsselverwaltung. Es wird empfohlen, Verschlüsselungsschlüssel regelmäßig zu rotieren. Die Rotationsfrequenz sollte sowohl die internen Bestimmungen Ihrer Organisation als auch die Complianceanforderungen erfüllen.

#### <a name="transparent-data-encryption-tde"></a>TDE (Transparent Data Encryption)

In TDE liegt eine Zwei-Schlüssel-Hierarchie vor: Die Daten in allen Benutzerdatenbanken werden mit einem in der Datenbank eindeutigen symmetrischen AES 256-Datenbankverschlüsselungsschlüssel verschlüsselt, der wiederum von einem auf dem Server eindeutigen asymmetrischen RSA 2048-Masterschlüssel verschlüsselt wird. Der Hauptschlüssel kann wie folgt verwaltet werden:

- Automatisch von der Plattform – SQL-Datenbank
- Unter Verwendung von [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) als Schlüsselspeicher

Der Masterschlüssel für die Transparent Data Encryption-Methode wird der Einfachheit halber vom SQL-Datenbank-Dienst verwaltet. Wenn Ihre Organisation den Hauptschlüssel überwachen möchte, können Sie Azure Key Vault](always-encrypted-azure-key-vault-configure.md) als Schlüsselspeicher verwenden. Dadurch hat Ihre Organisation die Kontrolle über die Schlüsselbereitstellung, die Rotation und die Berechtigungen. [Die Rotation oder der Typwechsel des TDE-Masterschlüssels](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) geht schnell vonstatten, da nur Verschlüsselungsschlüssel wieder verschlüsselt werden. Für Organisationen, die die Funktionen der Sicherheits- und Datenverwaltung trennen, kann ein Sicherheitsadministrator das Schlüsselmaterial für den TDE-Hauptschlüssel in Azure Key Vault bereitstellen und dem Datenbankadministrator eine Azure Key Vault-Schlüsselkennung zur Verfügung stellen, die er zur Verschlüsselung ruhender Daten auf einem Server verwenden soll. Key Vault ist dafür ausgelegt, dass Microsoft Verschlüsselungsschlüssel weder anzeigt noch extrahiert. Zusätzlich können Sie eine zentrale Schlüsselverwaltung für Ihre Organisation nutzen.

#### <a name="always-encrypted"></a>Always Encrypted

Es gibt auch eine [Zwei-Schlüssel-Hierarchie](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) in Always Encrypted: Eine Spalte mit vertraulichen Daten wird mit einem AES 256-Spaltenverschlüsselungsschlüssel verschlüsselt, der wiederum mit einem Spaltenmasterschlüssel verschlüsselt wird. Die Clienttreiber, die für die Always Encrypted-Methode zur Verfügung gestellt werden, haben keine Beschränkungen im Hinblick auf die Länge des Spaltenhauptschlüssels. Der verschlüsselte Wert des Spaltenverschlüsselungsschlüssel wird in der Datenbank gespeichert, und der Spaltenhauptschlüssel wird in einem vertrauenswürdigen Schlüsselspeicher wie dem Windows-Zertifikatspeicher, Azure Key Vault oder einem Hardware-Sicherheitsmodul gespeichert.

- Sowohl der [Spaltenverschlüsselungsschlüssel als auch der Spaltenhauptschlüssel](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) können gedreht werden.
- Die Rotation des Spaltenverschlüsselungsschlüssels ist datenintensiv und kann abhängig von der Größe der Tabellen, die die verschlüsselten Spalten enthalten, zeitaufwendig sein. Daher empfiehlt es sich, Rotationen von Spaltenverschlüsselungsschlüsseln entsprechend zu planen.
- Die Rotation des Spaltenhauptschlüssels beeinträchtigt die Datenbankleistung dagegen nicht und kann mit getrennten Rollen durchgeführt werden.

Das folgende Diagramm stellt eine Übersicht über die wichtigsten Schlüsselspeicheroptionen für die Spaltenhauptschlüssel bei der Always Encrypted-Methode dar.

![Always Encrypted-Speicheranbieter für den Spaltenhauptschlüssel](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Wie wird der Datenverkehr zwischen der Organisation und SQL-Datenbank optimiert und geschützt?

Der Netzwerkdatenverkehr zwischen Ihrer Organisation und SQL-Datenbank würde normalerweise über das öffentliche Netzwerk geroutet werden. Wenn Sie diesen Pfad jedoch optimieren und sicherer gestalten möchten, sollten Sie Azure ExpressRoute in Betracht ziehen. Mit ExpressRoute lässt sich Ihr Unternehmensnetzwerk über eine private Verbindung praktisch auf die gesamte Azure-Plattform ausweiten. Auf diese Weise werden keine Daten über das öffentliche Internet übertragen. Zusätzlich erhöhen sich die Sicherheit und Zuverlässigkeit, und das Routing wird optimiert. So erzielen Sie geringere Netzwerklatenzen und höhere Geschwindigkeiten, als das öffentliche Internet normalerweise bietet. Wenn Sie beabsichtigen, einen erheblichen Datenblock zwischen Ihrer Organisation und Azure zu übertragen, bietet ExpressRoute möglicherweise Kostenvorteile. Für Verbindungen zwischen Ihrer Organisation und Azure stehen drei Konnektivitätsmodelle zur Auswahl:

- [Cloud Exchange und Colocation](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [n:n-Konnektivität](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Punkt-zu-Punkt-Konnektivität](../../expressroute/expressroute-connectivity-models.md#Ethernet)

Mit ExpressRoute können Sie Ihre kostenpflichtige Bandbreite ohne zusätzliche Gebühren bis auf das Doppelte steigern. Außerdem ist es möglich, die Verbindung regionsübergreifend mithilfe von ExpressRoute zu konfigurieren. Eine Liste von ExpressRoute-Konnektivitätsanbietern finden Sie unter: [ExpressRoute-Partner und Peeringstandorte](../../expressroute/expressroute-locations.md). In den folgenden Artikeln wird ExpressRoute ausführlicher beschrieben:

- [Einführung in Express Route](../../expressroute/expressroute-introduction.md)
- [Voraussetzungen](../../expressroute/expressroute-prerequisites.md)
- [Workflows](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Ist SQL-Datenbank mit allen gesetzlichen Anforderungen kompatibel, und inwiefern hilft dies dabei, die Kompatibilitätsvorgaben meiner eigenen Organisation einzuhalten?

Von SQL-Datenbank werden verschiedene gesetzliche Bestimmungen erfüllt. Im [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) erfahren Sie, welche Compliancestandards von SQL-Datenbank derzeit erfüllt werden. Dort können Sie sich informieren, ob die Complianceanforderungen Ihrer Organisation erfüllt werden, und ermitteln, ob SQL-Datenbank zu den konformen Azure-Diensten gehört. Wichtiger Hinweis: Obwohl SQL-Datenbank als konformer Dienst aufgeführt sein kann, unterstützt er den Dienst Ihrer Organisation lediglich bei der Umsetzung der Compliancestandards, ohne automatisch deren Einhaltung zu gewährleisten.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligente Datenbanküberwachung und -wartung nach der Migration

Nachdem Sie Ihre Datenbank zu SQL-Datenbank migriert haben, möchten Sie Ihre Datenbank überwachen (z. B. die Ressourcenverwendung prüfen oder DBCC-Überprüfungen ausführen) und regelmäßige Wartungsaufgaben durchführen (z. B. Indizes, Statistiken usw. neu erstellen oder organisieren). SQL-Datenbank arbeitet in dem Sinne intelligent, dass der Dienst historische Trends und aufgezeichnete Metriken und Statistiken nutzt, um proaktive Unterstützung bei der Datenbanküberwachung und -wartung zu leisten und zu gewährleisten, dass die Anwendung immer optimal funktioniert. In einigen Fällen können Wartungsaufgaben abhängig von der Konfiguration von Azure SQL-Datenbank automatisch ausgeführt werden. Die Überwachung Ihrer Datenbank in SQL-Datenbank umfasst drei Aspekte:

- Leistungsüberwachung und -optimierung
- Sicherheitsoptimierung
- Kostenoptimierung

### <a name="performance-monitoring-and-optimization"></a>Leistungsüberwachung und -optimierung

Query Performance Insight liefert maßgeschneiderte Empfehlungen zur Datenbankworkload, damit Anwendungen immer optimal ausgeführt werden. Sie können die Funktion auch so konfigurieren, dass die Empfehlungen automatisch angewendet werden und Sie sich keine Gedanken um Wartungsaufgaben machen müssen. Mit dem SQL Database Advisor können Sie automatisch Indexempfehlungen basierend auf Ihrer Workload implementieren. Dieser Vorgang wird als automatische Optimierung bezeichnet. Die Empfehlungen ändern sich parallel zur Arbeitsauslastung Ihrer Anwendung, sodass Sie immer relevante Vorschläge erhalten. Sie können diese Empfehlungen auch manuell überprüfen und nach Ihrem Ermessen anwenden.  

### <a name="security-optimization"></a>Sicherheitsoptimierung

SQL-Datenbank bietet handlungsrelevante Sicherheitsempfehlungen, die Sie beim Schutz Ihrer Daten unterstützen, sowie eine Bedrohungserkennung, mit der verdächtige Datenbankaktivitäten, die die Datenbank gefährden könnten, identifiziert und untersucht werden. Bei der [Bewertung von Sicherheitsrisiken](sql-vulnerability-assessment.md) handelt es sich um einen Überprüfungs- und Berichterstellungsdienst für Datenbanken, mit dessen Hilfe Sie den Sicherheitsstatus Ihrer Datenbanken effektiv überwachen sowie Sicherheitsrisiken und Abweichungen von der von Ihnen definierten Sicherheitsbaseline ermitteln können. Nach jeder Überprüfung wird neben einer benutzerdefinierte Liste mit zur Umsetzung empfohlenen Schritten und Wiederherstellungsskripts auch ein Bewertungsbericht zur Verfügung gestellt, der zur Einhaltung von Kompatibilitätsanforderungen verwendet werden kann.

Mit Azure Security Center können Sie Sicherheitsempfehlungen auf allgemeiner Basis ermitteln und mit einem einzigen Mausklick anwenden.

### <a name="cost-optimization"></a>Kostenoptimierung

Die Azure SQL-Plattform analysiert den Nutzungsverlauf übergreifend über die Datenbanken auf einem Server, um Optionen zur Kostenoptimierung zu bewerten und zu empfehlen. Die Analyse und Formulierung handlungsrelevanter Empfehlungen dauert normalerweise zwei Wochen. Der Pool für elastische Datenbanken gehört zu den möglichen Optionen. Die Empfehlung wird im Portal als Banner angezeigt:

![Empfehlungen zum Pool für elastische Datenbanken](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

Sie können die Analyse auch im Abschnitt „Advisor“ anzeigen:

![Empfehlungen zum Pool für elastische Datenbanken – Advisor](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Wie wird die Leistung und Ressourcenverwendung in SQL-Datenbank überwacht?

In SQL-Datenbank können Sie die Plattformfunktion „Intelligent Insights“ nutzen, um die Leistung zu überwachen und entsprechend zu optimieren. Sie können die Leistung und Ressourcenverwendung in SQL-Datenbank mithilfe der folgenden Methoden überwachen:

#### <a name="azure-portal"></a>Azure-Portal

Im Azure-Portal wird die Nutzung einer Datenbank angezeigt, indem Sie die Datenbank auswählen und im Bereich „Übersicht“ auf das Diagramm klicken. Sie können das Diagramm ändern, sodass mehrere Metriken z.B. zum CPU-, DTU- und Data IO-Prozentsatz sowie zu den Sitzungen und Datenbankgrößen in Prozent angezeigt werden.

![Überwachungsdiagramm](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![Überwachungsdiagramm 2](./media/manage-data-after-migrating-to-database/chart.png)

In diesem Diagramm können Sie außerdem Warnungen nach Ressourcen konfigurieren. Die Warnungen ermöglichen es Ihnen, auf Ressourcenbedingungen mit einer E-Mail oder einer Benachrichtigung an einen HTTPS-/HTTP-Endpunkt zu reagieren oder eine Aktion auszuführen. Weitere Informationen finden Sie unter [Warnungen erstellen](alerts-insights-configure-portal.md).

#### <a name="dynamic-management-views"></a>Dynamische Verwaltungssichten

Sie können die dynamische Verwaltungssicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) abfragen, um den statistischen Verlauf des Ressourcenverbrauchs der letzten Stunde zurückzugeben. Ebenso können Sie die Systemkatalogsicht [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) abfragen, um den Verlauf der letzten 14 Tage zurückzugeben.

#### <a name="query-performance-insight"></a>Query Performance Insight

Über [Query Performance Insight](query-performance-insight-use.md) (Statistik zur Abfrageleistung) können Sie den Verlauf der Abfragen, die den höchsten Ressourcenverbrauch aufweisen, und die Abfragen mit langer Laufzeit für eine bestimmte Datenbank sehen. Sie können schnell Abfragen mit der höchsten Ressourcenverwendung, Dauer und Ausführungshäufigkeit identifizieren. Sie können Abfragen nachverfolgen und Regressionen erkennen. Für diese Funktion muss der [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) aktiviert und für die Datenbank aktiv sein.

![Query Performance Insight](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL-Analyse (Vorschau) in Azure Monitor-Protokollen

Mit [Azure Monitor-Protokollen](../../azure-monitor/insights/azure-sql.md) können Sie wichtige Leistungsmetriken von Azure SQL-Datenbank erfassen und visualisieren. Dabei werden bis zu 150.000 Datenbanken und 5.000 Pools für elastische SQL-Datenbanken pro Arbeitsbereich unterstützt. Sie können darüber Benachrichtigungen überwachen und erhalten. Sie können Metriken für SQL-Datenbank und Pools für elastische Datenbanken über mehrere Azure-Abonnements und Pools für elastische Datenbanken hinweg überwachen und nutzen, um auf allen Ebenen eines Anwendungsstapels Probleme zu ermitteln.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Es fallen Probleme mit der Leistung auf: Wie unterscheiden sich die Methoden zur Problembehandlung bei SQL-Datenbank von den Methoden bei SQL Server?

Viele Problembehandlungen zur Diagnose von Problemen bei der Abfrage- und Datenbankleistung bleiben unverändert. Schließlich wird die Cloud von derselben Datenbank-Engine unterstützt. Die Plattform – Azure SQL-Datenbank – verfügt jedoch über integrierte „Intelligenz“. So lassen sich Leistungsprobleme noch einfacher diagnostizieren und beheben. Außerdem können einige Korrekturmaßnahmen für Sie ausgeführt und in einigen Fällen automatisch proaktiv behoben werden.

Die Behandlung von Leistungsproblemen kann von der kombinierten Verwendung intelligenter Funktionen wie [Query Performance Insight (QPI)](query-performance-insight-use.md) und [Database Advisor](database-advisor-implement-performance-recommendations.md) deutlich profitieren. Daher unterscheidet sich der Ansatz in der Hinsicht, dass Sie die grundlegenden Details, die Sie bei der Behandlung des vorliegenden Problems unterstützen könnten, nicht mehr manuell erarbeiten müssen. Diese Arbeit leistet die Plattform für Sie. Ein Beispiel hierfür ist QPI. Mit QPI können Sie Problemen bis auf die Abfrageebene nachgehen und mithilfe historischer Trends herausfinden, wann die Abfrageleistung nachgelassen hat. Database Advisor gibt Empfehlungen, die zur allgemeinen Verbesserung der Gesamtleistung beitragen können, beispielsweise, wenn Indizes fehlen oder gelöscht wurden bzw. bei parametrisierten Abfragen usw.

Bei der Behandlung von Leistungsproblemen muss festgestellt werden, ob die Anwendungsleistung lediglich durch die Anwendung oder die zugrunde liegende Datenbank beeinträchtigt wird. Häufig wird das Leistungsproblem durch die Anwendungsschicht verursacht, beispielsweise durch die Architektur oder das Datenzugriffsmuster. Angenommen, Sie verfügen über eine „sehr kommunikative“ Anwendung, die empfindlich auf Netzwerklatenz reagiert. In diesem Fall wird die Anwendung beeinträchtigt, da in einem überlasteten Netzwerk zwischen der Anwendung und dem Server viele kurze Anforderungen übertragen würden. Diese Roundtrips könnten sich schnell summieren. Um in diesem Fall die Leistung zu verbessern, verwenden Sie [Batchabfragen](performance-guidance.md#batch-queries). Batches sind sehr hilfreich, da Ihre Anforderungen jetzt in einem Batch verarbeitet werden, wodurch die Roundtriplatenz gesenkt und die Anwendungsleistung verbessert werden.

Wenn Sie außerdem eine Verringerung der Gesamtleistung der Datenbank beobachten, können Sie anhand der dynamischen Verwaltungssichten [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) und [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) die CPU-, E/A- und Speichernutzung nachvollziehen. Die Leistung kann beeinträchtigt sein, da der Datenbank keine Ressourcen zur Verfügung stehen. Möglicherweise müssen Sie die Computegröße und/oder Dienstebene abhängig von zunehmenden oder abnehmenden Workloads ändern.

Umfassende Empfehlungen zur Behebung von Leistungsproblemen finden Sie unter: [Optimieren der Datenbank](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Wie kann sichergestellt werden, dass die geeignete Dienstebene und Computegröße verwendet wird?

SQL-Datenbank verfügt über die Dienstebenen „Basic“, „Standard“ und „Premium“. Jede Dienstebene garantiert eine vorhersagbare Leistung, die an sie gebunden ist. Je nach Arbeitsauslastung können vermehrt Aktivitäten auftreten, bei denen die Ressourcenverwendung über die aktuell verwendete Computegröße hinausgeht. In solchen Fällen sollten Sie zunächst abwägen, ob sich das Problem durch eine Optimierung beheben lässt (beispielsweise, indem Sie einen Index hinzufügen oder ändern usw.). Wenn weiterhin Kapazitätsprobleme auftreten, sollten Sie den Wechsel zu einer höheren Dienstebene oder Computegröße in Betracht ziehen.

|**Dienstebene**|**Allgemeine Anwendungsszenarien**|
|---|---|
|**Grundlegend**|Anwendungen mit wenigen Benutzern und einer Datenbank, die keine hohen Anforderungen an Parallelität, Skalierung und Leistung stellt |
|**Standard**|Anwendungen mit hohen Anforderungen an Parallelität, Skalierung und Leistung und mittleren bis geringen Anforderungen an die E/A-Leistung |
|**Premium**|Anwendungen mit vielen gleichzeitigen Benutzern und hohen Anforderungen an CPU/Arbeitsspeicher und E/A. Apps, die von hoher Parallelität, hohem Durchsatz und niedrigen Latenzen abhängig sind, profitieren von der Premium-Ebene. |
|||

Um sicherzustellen, dass Sie die geeignete Computegröße verwenden, überwachen Sie die Nutzung von Abfrage- und Datenbankressourcen mithilfe einer der Methoden unter „Wie wird die Leistung und Ressourcenverwendung in SQL-Datenbank überwacht?“. Falls Ihre Abfragen/Datenbanken dauerhaft mehr CPU/Arbeitsspeicher usw. benötigen, sollten Sie den Wechsel zu einer höheren Computegröße in Erwägung ziehen. Wenn Sie feststellen, dass die Ressourcen selbst während Spitzenzeiten nicht ausgelastet werden, kommt für Sie wiederum ein Downgrade zu einer niedrigeren Computegröße infrage.

Falls Sie ein SaaS-App-Muster oder ein Szenario zur Datenbankkonsolidierung nutzen, wäre zur Kostenoptimierung ein Pool für elastische Datenbanken empfehlenswert. Ein Pool für elastische Datenbanken bietet optimale Voraussetzungen für die Datenbankkonsolidierung und Kostenoptimierung. Weitere Informationen zur Verwaltung mehrerer Datenbanken mit Pools für elastische Datenbanken finden Sie unter: [Verwalten von Pools und Datenbanken](elastic-pool-manage.md#azure-portal).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Wie oft müssen Integritätsprüfungen für Datenbanken ausgeführt werden?

SQL-Datenbank nutzt intelligente Technologien, mit denen bestimmte Datenbeschädigungen automatisch ohne Datenverluste behandelt werden können. Diese Technologien sind in den Dienst integriert und kommen bei Bedarf zum Einsatz. Ihre Datenbanksicherungen werden im gesamten Dienst regelmäßig getestet, indem sie wiederhergestellt und mit DBCC CHECKDB überprüft werden. Dabei werden Probleme von SQL-Datenbank proaktiv behoben. Mithilfe der [automatischen Seitenreparatur](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) werden Seiten repariert, die beschädigt sind oder Probleme mit der Datenintegrität aufweisen. Datenbankseiten werden immer mit der standardmäßigen CHECKSUM-Einstellung auf ihre Integrität überprüft. Die Datenintegrität Ihrer Datenbank wird von SQL-Datenbank proaktiv überwacht und überprüft. Auftretende Probleme werden mit der höchsten Priorität behandelt. Sie können nach Belieben zusätzlich eigene Integritätsprüfungen ausführen.  Weitere Informationen finden Sie im Thema zur [Datenintegrität in SQL-Datenbank](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="data-movement-after-migration"></a>Datenverschiebung nach der Migration

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Wie werden Daten als BACPAC-Dateien in SQL-Datenbank mithilfe des Azure-Portals exportiert und importiert?

- **Export**: Sie können Ihre Datenbank in Azure SQL-Datenbank als BACPAC-Datei aus dem Azure-Portal exportieren.

   ![Datenbankexport](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Import**: Sie können Daten im Azure-Portal auch als BACPAC-Datei in Ihre Datenbank in Azure SQL-Datenbank importieren.

   ![Datenbankimport](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Wie werden Daten zwischen SQL-Datenbank und SQL Server synchronisiert?

Hier gibt mehrere Möglichkeiten:

- **[Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md)** – Diese Funktion unterstützt Sie bei der bidirektionalen Synchronisierung von Daten zwischen mehreren SQL Server-Datenbanken und SQL-Datenbank. Um Daten mit SQL Server-Datenbanken zu synchronisieren, müssen Sie den Synchronisierungs-Agent auf einem lokalen Computer oder einem virtuellen Computer installieren und konfigurieren und den ausgehenden TCP-Port 1433 öffnen.
- **[Transaktionsreplikation](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – Mit der Transaktionsreplikation können Sie Ihre Daten von einer SQL Server-Datenbank mit Azure SQL Datenbank synchronisieren. Dabei stellt die SQL Server-Instanz den Verleger und Azure SQL-Datenbank den Abonnenten dar. Zurzeit wird nur dieses Szenario unterstützt. Weitere Informationen dazu, wie Sie Daten mit minimaler Ausfallzeit von einer SQL Server-Datenbank zu Azure SQL migrieren, finden Sie unter: [Verwenden der Transaktionsreplikation](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [SQL-Datenbank](sql-database-paas-overview.md)