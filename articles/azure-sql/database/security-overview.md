---
title: Sicherheitsübersicht
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Erfahren Sie mehr über die Sicherheit in Azure SQL-Datenbank und Azure SQL Managed Instance, einschließlich der Unterschiede gegenüber SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 39119f62fa938f5f4f6529539d4ca9a84bdf8fd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989189"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Übersicht über die Sicherheitsfunktionen von Azure SQL-Datenbank und SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In diesem Artikel werden die Grundlagen zum Schutz der Datenschicht einer Anwendung mit [Azure SQL-Datenbank](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) beschrieben. Die beschriebene Sicherheitsstrategie folgt dem Defense-in-Depth-Schichtenmodell, das in der folgenden Abbildung dargestellt ist und von außen nach innen durchlaufen wird:

![Diagramm des Defense-in-Depth-Schichtenmodells. Kundendaten werden in Schichten von Netzwerksicherheit, Zugriffsverwaltung sowie Bedrohungs- und Informationsschutz umschlossen.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Netzwerksicherheit

Microsoft Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics bieten einen Dienst für relationale Datenbanken für Cloud- und Unternehmensanwendungen. Zum Schutz von Kundendaten verhindern Firewalls den Netzwerkzugriff auf den Server, bis der Zugriff explizit basierend auf der IP-Adresse oder dem Ursprung des Datenverkehrs im virtuellen Azure-Netzwerk gewährt wird.

### <a name="ip-firewall-rules"></a>IP-Firewallregeln

IP-Firewallregeln gewähren den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank und Azure Synapse Analytics](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Firewallregeln für virtuelle Netzwerke

[VNET-Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md) erweitern Ihre VNET-Konnektivität über den Azure-Backbone hinaus und ermöglichen Azure SQL-Datenbank, das Subnetz des virtuellen Netzwerks zu identifizieren, aus dem der Datenverkehr stammt. Wenn Sie Datenverkehr an Azure SQL-Datenbank zulassen möchten, verwenden Sie die SQL-[Diensttags](../../virtual-network/network-security-groups-overview.md), um ausgehenden Datenverkehr über Netzwerksicherheitsgruppen zu ermöglichen.

Mithilfe von [VNET-Regeln](vnet-service-endpoint-rule-overview.md) kann Azure SQL-Datenbank nur Verbindungen akzeptieren, die von ausgewählten Subnetzen innerhalb eines virtuellen Netzwerks stammen.

> [!NOTE]
> Die Zugriffssteuerung mit Firewallregeln gilt *nicht* für **SQL Managed Instance**. Weitere Informationen zur erforderlichen Netzwerkkonfiguration finden Sie unter [Herstellen einer Verbindung mit einer verwalteten Instanz](../managed-instance/connect-application-instance.md).

## <a name="access-management"></a>Zugriffsverwaltung

> [!IMPORTANT]
> Die Verwaltung von Datenbanken und Servern in Azure wird über die Rollenzuweisungen in Ihrem Portalbenutzerkonto gesteuert. Weitere Informationen zu diesem Artikel finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure im Azure-Portal](../../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentifizierung

Die Authentifizierung ist der Prozess, bei dem bestätigt wird, dass der Benutzer derjenige ist, der er zu sein vorgibt. Azure SQL-Datenbank und SQL Managed Instance unterstützen zwei Arten der Authentifizierung:

- **SQL-Authentifizierung**:

    Der Begriff SQL-Authentifizierung bezieht sich auf die Authentifizierung eines Benutzers beim Herstellen einer Verbindung mit Azure SQL-Datenbank oder Azure SQL Managed Instance mithilfe von Benutzername und Kennwort. Beim Erstellen des Servers müssen Anmeldeinformationen für einen **Serveradministrator** mit einem Benutzernamen und einem Kennwort angegeben werden. Mit diesen Anmeldeinformationen kann sich ein **Serveradministrator** bei jeder Datenbank auf diesem Server oder dieser Instanz als Datenbankbesitzer authentifizieren. Danach können zusätzliche SQL-Anmeldungen und -Benutzer durch den Serveradministrator erstellt werden, sodass Benutzer eine Verbindung mithilfe von Benutzername und Kennwort herstellen können.

- **Azure Active Directory-Authentifizierung**:

    Die Azure Active Directory-Authentifizierung ist ein Mechanismus zum Herstellen einer Verbindung mit [Azure SQL-Datenbank](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) unter Verwendung von Identitäten in Azure Active Directory (Azure AD). Die Azure AD-Authentifizierung ermöglicht Administratoren die zentrale Verwaltung der Identitäten und Berechtigungen von Datenbankbenutzern und anderen Azure-Diensten. Dies umfasst auch die Minimierung der Kennwortspeicherung und ermöglicht Richtlinien zur zentralisierten Kennwortrotation.

     Sie müssen einen Serveradministrator (der als **Active Directory-Administrator** bezeichnet wird) erstellen, um die Azure AD-Authentifizierung mit SQL-Datenbank verwenden zu können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](authentication-aad-overview.md). Die Azure AD-Authentifizierung unterstützt sowohl verwaltete als auch Verbundkonten. Verbundkonten unterstützen Windows-Benutzer und -Gruppen für eine benutzerdefinierte Domäne im Verbund mit Azure AD.

    Zu den weiteren Möglichkeiten der Azure AD-Authentifizierung gehören Verbindungen mit der [universellen Active Directory-Authentifizierung für SQL Server Management Studio](authentication-mfa-ssms-overview.md), einschließlich [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) und [bedingtem Zugriff](conditional-access-configure.md).

> [!IMPORTANT]
> Die Verwaltung von Datenbanken und Servern in Azure wird über die Rollenzuweisungen in Ihrem Portalbenutzerkonto gesteuert. Weitere Informationen zu diesem Artikel finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure im Azure-Portal](../../role-based-access-control/overview.md). Die Zugriffssteuerung mit Firewallregeln gilt *nicht* für **SQL Managed Instance**. Weitere Informationen zur erforderlichen Netzwerkkonfiguration finden Sie im Artikel zum [Herstellen einer Verbindung mit einer verwalteten Instanz](../managed-instance/connect-application-instance.md).

## <a name="authorization"></a>Authorization

Der Begriff Autorisierung bezieht sich auf die Berechtigungen, die einem Benutzer in einer Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance zugewiesen sind und festlegen, welche Aktionen der Benutzer ausführen darf. Berechtigungen werden gesteuert, indem Benutzerkonten zu [Datenbankrollen](/sql/relational-databases/security/authentication-access/database-level-roles) hinzugefügt und diesen Rollen Berechtigungen auf Datenbankebene zugewiesen oder dem Benutzer bestimmte [Berechtigungen auf Objektebene](/sql/relational-databases/security/permissions-database-engine) erteilt werden. Weitere Informationen finden Sie unter [Anmeldungen und Benutzer](logins-create-manage.md).

Es hat sich bewährt, benutzerdefinierte Rollen bei Bedarf zu erstellen. Fügen Sie Benutzer dann derjenigen Rolle hinzu, die die geringsten Berechtigungen besitzt, um die erforderliche Aufgabe noch erfüllen zu können. Weisen Sie Benutzern Berechtigungen nicht direkt zu. Das Serveradministratorkonto gehört zur integrierten Rolle „db_owner“, die über umfassende Berechtigungen verfügt und nur wenigen Benutzern mit administrativen Aufgaben erteilt werden sollte. Verwenden Sie bei Anwendungen die Option [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) zur Angabe des Ausführungskontexts für das aufgerufene Modul, oder verwenden Sie [Anwendungsrollen](/sql/relational-databases/security/authentication-access/application-roles) mit eingeschränkten Berechtigungen. Durch diese Vorgehensweise wird sichergestellt, dass eine Anwendung, die eine Verbindung mit der Datenbank herstellt, nur über die geringsten Berechtigungen verfügt, die von der Anwendung benötigt werden. Diese bewährten Methoden fördern auch die Aufgabentrennung.

### <a name="row-level-security"></a>Sicherheit auf Zeilenebene

Mithilfe der Sicherheit auf Zeilenebene können Kunden den Zugriff auf die Zeilen in einer Datenbanktabelle auf Grundlage der Merkmale des Benutzers steuern, der eine Abfrage ausführt (z.B. Gruppenmitgliedschaft oder Ausführungskontext). Sicherheit auf Zeilenebene kann auch verwendet werden, um benutzerdefinierte bezeichnungsbasierte Sicherheitskonzepte zu implementieren. Weitere Informationen finden Sie unter [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security).

![Das Diagramm zeigt, dass die Sicherheit auf Zeilenebene einzelne Zeilen einer SQL-Datenbank vor dem Zugriff durch Benutzer über eine Client-App schützt.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Bedrohungsschutz

Zum Schutz der Kundendaten stellen SQL-Datenbank und SQL Managed Instance Funktionen für die Überwachung und Bedrohungserkennung bereit.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-Überwachung in Azure Monitor-Protokollen und Event Hubs

Bei der Überwachung von SQL-Datenbank und SQL Managed Instance werden Datenbankaktivitäten nachverfolgt, und Sie erhalten Unterstützung bei der Einhaltung von Sicherheitsstandards, indem Datenbankereignisse in einem Überwachungsprotokoll in einem Azure Storage-Konto des Kunden aufgezeichnet werden. Dank der Überwachung können Benutzer die fortlaufenden Datenbankaktivitäten überwachen und die Verlaufsaktivität analysieren und untersuchen, um potenzielle Bedrohungen oder vermutete Missbrauchsfälle und Sicherheitsverletzungen zu identifizieren. Weitere Informationen finden Sie in den ersten Schritten unter [Überwachung von SQL-Datenbank](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Erweiterter Schutz vor Bedrohungen

Advanced Threat Protection analysiert Ihre Protokolle auf ungewöhnliches Verhalten und potenziell schädliche Zugriffs- oder Offenlegungsversuche bei Datenbanken. Warnungen werden für verdächtige Aktivitäten wie SQL-Injektion, potenzielle Dateninfiltration und Brute-Force-Angriffe oder für Anomalien in den Zugriffsmustern erstellt, um Berechtigungserweiterungen und die Verwendung gefährdeter Anmeldeinformationen zu erfassen. Sie können die Warnungen im [Azure Security Center](https://azure.microsoft.com/services/security-center/) anzeigen. Dort finden Sie Details zu verdächtigen Aktivitäten und Empfehlungen für weitere Untersuchungen sowie zu Aktionen, mit denen das Risiko minimiert werden kann. Advanced Threat Protection kann pro Server für eine zusätzliche Gebühr aktiviert werden. Weitere Informationen finden Sie unter [Erste Schritte mit Advanced Threat Protection von SQL-Datenbank](threat-detection-configure.md).

![Das Diagramm zeigt, dass die SQL-Bedrohungserkennung den Zugriff auf die SQL-Datenbank für eine Web-App von einem externen Angreifer und böswilligen Insider überwacht.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (Verschlüsselung bei der Übertragung)

SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics schützen Kundendaten durch das Verschlüsseln von Daten bei der Übertragung mit [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics erzwingen die Verschlüsselung (SSL/TLS) jederzeit und für alle Verbindungen. Dadurch wird sichergestellt, dass alle Daten „im Übergang“ zwischen Client und Server verschlüsselt werden, und zwar unabhängig von der Einstellung von **Encrypt** oder **TrustServerCertificate** in der Verbindungszeichenfolge.

Als bewährte Methode wird empfohlen, in der von der Anwendung verwendeten Verbindungszeichenfolge eine verschlüsselte Verbindung anzugeben und dem Serverzertifikat _**nicht**_ zu vertrauen. Dies erzwingt, dass Ihre Anwendung das Serverzertifikat überprüft, und verhindert somit, dass Ihre Anwendung für Angriffe vom Typ „Man-in-the-Middle“ anfällig ist.

Bei Verwendung des ADO.NET-Treibers erfolgt dies z. B. über **Encrypt=True** und **TrustServerCertificate=False**. Wenn Sie Ihre Verbindungszeichenfolge über das Azure-Portal abrufen, verfügt sie bereits über die richtigen Einstellungen.

> [!IMPORTANT]
> Beachten Sie, dass einige Nicht-Microsoft-Treiber TLS möglicherweise nicht standardmäßig verwenden oder sich auf eine ältere Version von TLS (niedriger als 1.2) verlassen, um zu funktionieren. In diesem Fall erlaubt der Server weiterhin das Herstellen einer Verbindung mit der Datenbank. Wir empfehlen Ihnen jedoch, die Sicherheitsrisiken auszuwerten, die damit verbunden sind, wenn Sie solchen Treibern und Anwendungen eine Verbindung mit der SQL-Datenbank ermöglichen, insbesondere wenn Sie vertrauliche Daten speichern.
>
> Weitere Informationen zu TLS und zur Konnektivität finden Sie unter [Überlegungen zu TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (Verschlüsselung im Ruhezustand)

[Transparent Data Encryption (TDE) für SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics ](transparent-data-encryption-tde-overview.md) fügt eine zusätzliche Sicherheitsebene zum Schutz ruhender Daten vor nicht autorisiertem oder Offlinezugriff auf Rohdatendateien oder Sicherungen hinzu. Entsprechende Szenarien umfassen häufig den Diebstahl im Rechenzentrum oder die unsichere Entsorgung von Hardware oder Medien, wie z. B. Festplatten und Sicherungsbändern. TDE verschlüsselt die gesamte Datenbank mit einem AES-Verschlüsselungsalgorithmus. Dies erfordert von den Anwendungsentwicklern keine Änderungen an vorhandenen Anwendungen.

In Azure werden standardmäßig alle neu erstellten Datenbanken verschlüsselt, und der Datenbankverschlüsselungsschlüssel wird mit einem integrierten Serverzertifikat geschützt.  Zertifikatwartung und -rotation werden vom Dienst verwaltet und erfordern keine Eingaben des Benutzers. Kunden, die volle Kontrolle über die Verschlüsselungsschlüssel benötigen, können die Schlüssel in [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) verwalten.

### <a name="key-management-with-azure-key-vault"></a>Schlüsselverwaltung mit Azure Key Vault

[BYOK](transparent-data-encryption-byok-overview.md)-Unterstützung (Bring Your Own Key) für [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) ermöglicht Kunden, die Schlüsselverwaltung und -rotation mit  [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), dem externen, cloudbasierten Schlüsselverwaltungssystem von Azure, selbst zu übernehmen. Wenn der Zugriff der Datenbank auf den Schlüsseltresor widerrufen wird, kann eine Datenbank nicht entschlüsselt und in den Speicher gelesen lesen. Azure Key Vault bietet eine zentrale Plattform für die Schlüsselverwaltung, verwendet streng überwachte Hardwaresicherheitsmodule (HSMs) und ermöglicht die Aufgabentrennung zwischen dem Verwalten von Schlüsseln und Daten, um Sicherheitsbestimmungen zu erfüllen.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (Verschlüsselung während der Verwendung)

![Das Diagramm zeigt die Grundlagen des Always Encrypted Features. Auf eine SQL-Datenbank mit einer Sperre wird nur von einer App zugegriffen, die einen Schlüssel enthält.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) ist eine Funktion zum Schutz vor dem Zugriff auf vertrauliche Daten (z.B. Kreditkartennummern, nationale Identifikationsnummern oder Daten, die _nur bei Bedarf bekannt sein sollten_), die in bestimmten Datenbankspalten gespeichert sind. Dies schließt Datenbankadministratoren und anderen privilegierte Benutzer ein, die autorisiert sind, für Verwaltungsaufgaben auf die Datenbank zuzugreifen, jedoch keinen Zugriff auf die entsprechenden Daten in den verschlüsselten Spalten benötigen. Die Daten werden immer verschlüsselt. Das bedeutet, dass die verschlüsselten Daten nur für die Verarbeitung durch Clientanwendungen mit Zugriff auf den Verschlüsselungsschlüssel entschlüsselt werden. Der Verschlüsselungsschlüssel wird nie gegenüber SQL-Datenbank oder SQL Managed Instance offengelegt und kann im [Windows-Zertifikatspeicher](always-encrypted-certificate-store-configure.md) oder in [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) gespeichert werden.

### <a name="dynamic-data-masking"></a>Dynamische Datenmaskierung

![Das Diagramm zeigt die dynamische Datenmaskierung. Eine Geschäftsanwendung sendet Daten an eine SQL-Datenbank, die die Daten vor dem Zurücksenden an die Anwendung maskiert.](./media/security-overview/azure-database-ddm.png)

Die dynamische Datenmaskierung beschränkt die Offenlegung vertraulicher Daten, indem sie für nicht berechtigte Benutzer maskiert werden. Mit der dynamischen Datenmaskierung werden in Azure SQL-Datenbank und SQL Managed Instance automatisch potenziell vertrauliche Daten ermittelt und direkt umsetzbare Empfehlungen bereitgestellt, um diese Felder mit minimalen Auswirkungen auf die Anwendungsschicht zu maskieren. Hierzu werden die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern verborgen, ohne dass die Daten in der Datenbank geändert werden. Weitere Informationen finden Sie unter [Erste Schritte mit der dynamischen Datenmaskierung für SQL-Datenbank und SQL Managed Instance](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Sicherheitsverwaltung

### <a name="vulnerability-assessment"></a>Sicherheitsrisikobewertung

Die [Sicherheitsrisikobewertung](sql-vulnerability-assessment.md) ist ein einfach zu konfigurierender Dienst, mit dem potenzielle Schwachstellen in der Datenbank ermittelt, nachverfolgt und behoben werden können. Damit soll die allgemeine Datenbanksicherheit proaktiv verbessert werden. Die Sicherheitsrisikobewertung ist Bestandteil von Azure Defender für SQL. Dabei handelt es sich um ein vereinheitlichtes Paket mit erweiterten SQL-Sicherheitsfunktionen. Der Zugriff auf die Sicherheitsrisikobewertung und ihre Verwaltung sind über das Azure Defender für SQL-Portal möglich.

### <a name="data-discovery-and-classification"></a>Datenermittlung und -klassifizierung

Datenermittlung und -klassifizierung (derzeit in der Vorschauphase) bietet erweiterte Funktionen für Azure SQL-Datenbank und SQL Managed Instance zum Ermitteln, Klassifizieren, Bezeichnen und Schützen vertraulicher Daten in Ihren Datenbanken. Das Ermitteln und Klassifizieren Ihrer vertraulichen Daten (Geschäfts-/Finanz-/Gesundheits-, personenbezogene Daten usw.) kann eine entscheidende Rolle in der Strategie Ihrer Organisation zum Datenschutz spielen. Sie kann für Folgendes als Infrastruktur gelten:

- Verschiedene Sicherheitsszenarien, z.B. Überwachung und Warnungen bei abweichendem Zugriff auf sensible Daten
- Steuern des Zugriffs auf und Härten der Sicherheit von Datenbanken, die sensible Daten enthalten
- Unterstützen der Einhaltung von Datenschutzstandards und gesetzlicher Bestimmungen

Weitere Informationen finden Sie unter [Erste Schritte mit der Datenermittlung und -klassifizierung](data-discovery-and-classification-overview.md).

### <a name="compliance"></a>Compliance

Zusätzlich zu den oben aufgeführten Features und Funktionen, mit denen Ihre Anwendung eine Reihe von Sicherheitsanforderungen erfüllen kann, wird Azure SQL-Datenbank außerdem regelmäßigen Überprüfungen unterzogen und ist für eine Reihe von Compliancestandards zertifiziert. Weitere Informationen finden Sie im [Microsoft Azure Trust Center](https://www.microsoft.com/trust-center/compliance/compliance-overview), wo die aktuellste Liste von Compliance-Zertifizierungen für SQL-Datenbank angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

- Eine Erläuterung der Verwendung von Anmeldungen, Benutzerkonten, Datenbankrollen und Berechtigungen in SQL-Datenbank und SQL Managed Instance finden Sie unter [Verwalten von Anmeldungen und Benutzerkonten](logins-create-manage.md).
- Eine Erörterung der Datenbanküberwachung finden Sie unter [Überwachung](../../azure-sql/database/auditing-overview.md).
- Eine Erörterung der Bedrohungserkennung finden Sie unter [Bedrohungserkennung](threat-detection-configure.md).