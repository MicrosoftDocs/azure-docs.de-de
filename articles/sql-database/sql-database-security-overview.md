---
title: Übersicht über die Sicherheit der Azure SQL-Datenbank | Microsoft-Dokumentation
description: Informationen zu Azure SQL-Datenbank und zur SQL Server-Sicherheit, z.B. Unterschiede zwischen der Cloud und lokalem SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 05/14/2019
ms.openlocfilehash: af14d5eb521d531f86433712a0d6c325ae7a1cd6
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258628"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Eine Übersicht über die Sicherheitsfunktionen von Azure SQL-Datenbank

Dieser Artikel beschreibt die Grundlagen zum Sichern der Datenschicht einer Anwendung mit Azure SQL-Datenbank. Die beschriebene Sicherheitsstrategie folgt dem Defense-in-Depth-Schichtenmodell, das in der folgenden Abbildung dargestellt ist und von außen nach innen durchlaufen wird:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Netzwerksicherheit

Microsoft Azure SQL-Datenbank ist ein Dienst für relationale Datenbanken für Cloud- und Unternehmensanwendungen. Zum Schutz von Kundendaten verhindern Firewalls den Netzwerkzugriff auf den Datenbankserver, bis der Zugriff explizit basierend auf der IP-Adresse oder dem Ursprung des Datenverkehrs im virtuellen Azure-Netzwerk gewährt wird.

### <a name="ip-firewall-rules"></a>IP-Firewallregeln

IP-Firewallregeln gewähren den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank und SQL Data Warehouse](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Firewallregeln für virtuelle Netzwerke

[VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) erweitern Ihre VNET-Konnektivität über den Azure-Backbone hinaus und ermöglichen Azure SQL-Datenbank, das Subnetz des virtuellen Netzwerks zu identifizieren, aus dem der Datenverkehr stammt. Wenn Sie Datenverkehr an Azure SQL-Datenbank zulassen möchten, verwenden Sie die SQL-[Diensttags](../virtual-network/security-overview.md), um ausgehenden Datenverkehr über Netzwerksicherheitsgruppen zu ermöglichen.

Mithilfe von [VNET-Regeln](sql-database-vnet-service-endpoint-rule-overview.md) kann Azure SQL-Datenbank nur Verbindungen akzeptieren, die von ausgewählten Subnetzen innerhalb eines virtuellen Netzwerks stammen.

> [!NOTE]
> Die Zugriffskontrolle mit Firewallregeln gilt *nicht* für **eine verwaltete Instanz**. Weitere Informationen zur erforderlichen Netzwerkkonfiguration finden Sie unter [Herstellen einer Verbindung mit einer verwalteten Instanz](sql-database-managed-instance-connect-app.md).

## <a name="access-management"></a>Zugriffsverwaltung

> [!IMPORTANT]
> Die Verwaltung von Datenbanken und Datenbankservern in Azure wird über die Rollenzuweisungen in Ihrem Portalbenutzerkonto gesteuert. Weitere Informationen zu diesem Artikel finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Die Authentifizierung ist der Prozess, bei dem bestätigt wird, dass der Benutzer derjenige ist, der er zu sein vorgibt. Azure SQL-Datenbank unterstützt zwei Arten der Authentifizierung:

- **SQL-Authentifizierung**:

    SQL-Datenbank-Authentifizierung bezieht sich auf die Authentifizierung eines Benutzers beim Herstellen einer Verbindung mit [Azure SQL-Datenbank](sql-database-technical-overview.md) mithilfe von Benutzername und Kennwort. Während der Erstellung des Datenbankservers für die Datenbank muss eine Anmeldung als Serveradministrator mit Benutzername und Kennwort angegeben werden. Mit diesen Anmeldeinformationen kann sich der Serveradministrator bei jeder Datenbank auf diesem Datenbankserver als Datenbankbesitzer authentifizieren. Danach können zusätzliche SQL-Anmeldungen und -Benutzer durch den Serveradministrator erstellt werden, sodass Benutzer eine Verbindung mithilfe von Benutzername und Kennwort herstellen können.

- **Azure Active Directory-Authentifizierung**:

    Die Azure Active Directory-Authentifizierung ist ein Mechanismus zum Herstellen einer Verbindung mit [Azure SQL-Datenbank](sql-database-technical-overview.md) und [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) unter Verwendung von Identitäten in Azure Active Directory (Azure AD). Die Azure AD-Authentifizierung ermöglicht Administratoren die zentrale Verwaltung der Identitäten und Berechtigungen von Datenbankbenutzern und anderen Microsoft-Diensten. Dies umfasst auch die Minimierung der Kennwortspeicherung und ermöglicht Richtlinien zur zentralisierten Kennwortrotation.

     Sie müssen einen Serveradministrator (der als **Active Directory-Administrator** bezeichnet wird) erstellen, um die Azure AD-Authentifizierung mit SQL-Datenbank verwenden zu können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md). Die Azure AD-Authentifizierung unterstützt sowohl verwaltete als auch Verbundkonten. Verbundkonten unterstützen Windows-Benutzer und -Gruppen für eine benutzerdefinierte Domäne im Verbund mit Azure AD.

    Zu den weiteren Möglichkeiten der Azure AD-Authentifizierung gehören Verbindungen mit der [universellen Active Directory-Authentifizierung für SQL Server Management Studio](sql-database-ssms-mfa-authentication.md), einschließlich [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) und [bedingtem Zugriff](sql-database-conditional-access.md).

> [!IMPORTANT]
> Die Verwaltung von Datenbanken und Servern in Azure wird über die Rollenzuweisungen in Ihrem Portalbenutzerkonto gesteuert. Weitere Informationen zu diesem Artikel finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Portal](../role-based-access-control/overview.md). Die Zugriffskontrolle mit Firewallregeln gilt *nicht* für **eine verwaltete Instanz**. Weitere Informationen zur erforderlichen Netzwerkkonfiguration finden Sie im Artikel zum [Herstellen einer Verbindung mit einer verwalteten Instanz](sql-database-managed-instance-connect-app.md).

## <a name="authorization"></a>Autorisierung

Der Begriff Autorisierung bezieht sich auf die Berechtigungen, die einem Benutzer in Azure SQL-Datenbank zugeordnet sind und die festlegen, welche Aktionen der Benutzer ausführen darf. Berechtigungen werden gesteuert, indem Benutzerkonten zu [Datenbankrollen](/sql/relational-databases/security/authentication-access/database-level-roles) hinzugefügt und diesen Rollen Berechtigungen auf Datenbankebene zugewiesen oder dem Benutzer bestimmte [Berechtigungen auf Objektebene](/sql/relational-databases/security/permissions-database-engine) erteilt werden. Weitere Informationen finden Sie unter [Anmeldungen und Benutzer](sql-database-manage-logins.md).

Es hat sich bewährt, benutzerdefinierte Rollen bei Bedarf zu erstellen. Fügen Sie dann der Rolle Benutzer mit den geringsten Berechtigungen hinzu, die sie für ihre Aufgaben benötigen. Weisen Sie Benutzern Berechtigungen nicht direkt zu. Das Serveradministratorkonto gehört zur integrierten Rolle „db_owner“, die über umfassende Berechtigungen verfügt und nur wenigen Benutzern mit administrativen Aufgaben erteilt werden sollte. Verwenden Sie bei Azure SQL-Datenbank-Anwendungen die Option [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) zur Angabe des Ausführungskontexts für das aufgerufene Modul, oder verwenden Sie [Anwendungsrollen](/sql/relational-databases/security/authentication-access/application-roles) mit eingeschränkten Berechtigungen. Durch diese Vorgehensweise wird sichergestellt, dass eine Anwendung, die eine Verbindung mit der Datenbank herstellt, nur über die geringsten Berechtigungen verfügt, die von der Anwendung benötigt werden. Diese bewährten Methoden fördern auch die Aufgabentrennung.

### <a name="row-level-security"></a>Sicherheit auf Zeilenebene

Bei der Sicherheit auf Zeilenebene können Kunden den Zugriff auf Zeilen in einer Datenbanktabelle basierend auf den Merkmalen des Benutzers steuern, der eine Abfrage ausführt (etwa Gruppenmitgliedschaft oder Ausführungskontext). Sicherheit auf Zeilenebene kann auch verwendet werden, um benutzerdefinierte bezeichnungsbasierte Sicherheitskonzepte zu implementieren. Weitere Informationen finden Sie unter [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Bedrohungsschutz

Zum Schutz der Kundendaten stellt SQL-Datenbank Funktionen für die Überwachung und Bedrohungserkennung bereit.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-Überwachung in Azure Monitor-Protokollen und Event Hubs

Bei der Überwachung von SQL-Datenbank werden Datenbankaktivitäten nachverfolgt, und Sie erhalten Unterstützung bei der Einhaltung von Sicherheitsstandards, indem Datenbankereignisse in einem Überwachungsprotokoll in einem Azure Storage-Konto des Kunden aufgezeichnet werden. Dank der Überwachung können Benutzer die fortlaufenden Datenbankaktivitäten überwachen und die Verlaufsaktivität analysieren und untersuchen, um potenzielle Bedrohungen oder vermutete Missbrauchsfälle und Sicherheitsverletzungen zu identifizieren. Weitere Informationen finden Sie in den ersten Schritten unter [Überwachung von SQL-Datenbank](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Erweiterter Schutz vor Bedrohungen

Advanced Threat Protection analysiert Ihre SQL Server-Protokolle auf ungewöhnliches Verhalten und potenziell schädliche Zugriffs- oder Offenlegungsversuche bei Datenbanken. Warnungen werden für verdächtige Aktivitäten wie SQL-Injektion, potenzielle Dateninfiltration und Brute-Force-Angriffe oder für Anomalien in den Zugriffsmustern erstellt, um Berechtigungserweiterungen und die Verwendung gefährdeter Anmeldeinformationen zu erfassen. Sie können die Warnungen im [Azure Security Center](https://azure.microsoft.com/services/security-center/) anzeigen. Dort finden Sie Details zu verdächtigen Aktivitäten und Empfehlungen für weitere Untersuchungen sowie zu Aktionen, mit denen das Risiko minimiert werden kann. Advanced Threat Protection kann pro Server für eine zusätzliche Gebühr aktiviert werden. Weitere Informationen finden Sie unter [Erste Schritte mit Advanced Threat Protection von SQL-Datenbank](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security, TLS (Verschlüsselung bei der Übertragung)

SQL-Datenbank schützt Kundendaten durch das Verschlüsseln von Daten bei der Übertragung mit [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Server erzwingt die Verschlüsselung (SSL/TLS) jederzeit für alle Verbindungen. Dadurch wird sichergestellt, dass alle Daten „im Übergang“ zwischen Client und Server verschlüsselt werden, und zwar unabhängig von der Einstellung von **Encrypt** oder **TrustServerCertificate** in der Verbindungszeichenfolge.

Als bewährte Methode empfehlen wir, dass Sie in der Verbindungszeichenfolge Ihrer Anwendung eine verschlüsselte Verbindung angeben und dem Serverzertifikat _**nicht**_ vertrauen. Dies erzwingt, dass Ihre Anwendung das Serverzertifikat überprüft und verhindert somit, dass Ihre Anwendung für Angriffe vom Typ „Man-in-the-Middle“ anfällig ist.

Wenn Sie beispielsweise den ADO.NET-Treiber verwenden, geschieht dies über **Encrypt=True** und **TrustServerCertificate=False**. Wenn Sie Ihre Verbindungszeichenfolge aus dem Azure-Portal abrufen, werden die richtigen Einstellungen verwendet.

> [!IMPORTANT]
> Beachten Sie, dass einige Nicht-Microsoft-Treiber TLS möglicherweise nicht standardmäßig verwenden oder sich auf eine ältere Version von TLS (niedriger als 1.2) verlassen, um zu funktionieren. In diesem Fall erlaubt SQL Server Ihnen weiterhin, eine Verbindung mit Ihrer Datenbank herzustellen. Wir empfehlen Ihnen jedoch, die Sicherheitsrisiken auszuwerten, die damit verbunden sind, wenn Sie solchen Treibern und Anwendungen eine Verbindung mit der SQL-Datenbank ermöglichen, insbesondere wenn Sie vertrauliche Daten speichern. 
>
> Weitere Informationen zu TLS und zur Konnektivität finden Sie unter [Überlegungen zu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (Verschlüsselung im Ruhezustand)

[Transparent Data Encryption (TDE) für Azure SQL-Datenbank](transparent-data-encryption-azure-sql.md) fügt eine zusätzliche Sicherheitsebene zum Schutz ruhender Daten vor nicht autorisiertem oder Offlinezugriff auf Rohdatendateien oder Sicherungen hinzu. Entsprechende Szenarien umfassen häufig den Diebstahl im Rechenzentrum oder die unsichere Entsorgung von Hardware oder Medien wie z.B. Festplatten und Sicherungsbändern. TDE verschlüsselt die gesamte Datenbank mit einem AES-Verschlüsselungsalgorithmus. Dies erfordert von den Anwendungsentwicklern keine Änderungen an vorhandenen Anwendungen.

In Azure werden standardmäßig alle neu erstellten SQL-Datenbanken verschlüsselt, und der Datenbankverschlüsselungsschlüssel wird mit einem integrierten Serverzertifikat geschützt.  Zertifikatwartung und -rotation werden vom Dienst verwaltet und erfordern keine Aktion durch den Benutzer. Kunden, die volle Kontrolle über die Verschlüsselungsschlüssel benötigen, können die Schlüssel in [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) verwalten.

### <a name="key-management-with-azure-key-vault"></a>Schlüsselverwaltung mit Azure Key Vault

[BYOK](transparent-data-encryption-byok-azure-sql.md)-Unterstützung (Bring Your Own Key) für [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) ermöglicht Kunden, die Schlüsselverwaltung und -rotation mit  [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), dem externen Schlüsselverwaltungssystem auf Grundlage der Azure-Cloud, selbst zu übernehmen. Wenn der Zugriff der Datenbank auf den Schlüsseltresor widerrufen wird, kann eine Datenbank nicht entschlüsselt und in den Speicher gelesen lesen. Azure Key Vault bietet eine zentrale Plattform für die Schlüsselverwaltung, verwendet streng überwachte Hardwaresicherheitsmodule (HSMs) und ermöglicht die Aufgabentrennung zwischen dem Verwalten von Schlüsseln und Daten, um Sicherheitsbestimmungen zu erfüllen.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (Verschlüsselung während der Verwendung)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) ist eine Funktion zum Schutz vor dem Zugriff auf vertrauliche Daten (z.B. Kreditkartennummern, nationale Identifikationsnummern oder Daten, die _nur bei Bedarf bekannt sein sollten_), die in bestimmten Datenbankspalten gespeichert sind. Dies schließt Datenbankadministratoren und anderen privilegierte Benutzer ein, die autorisiert sind, für Verwaltungsaufgaben auf die Datenbank zuzugreifen, jedoch keinen Zugriff auf die entsprechenden Daten in den verschlüsselten Spalten benötigen. Die Daten werden immer verschlüsselt. Das bedeutet, dass die verschlüsselten Daten nur für die Verarbeitung durch Clientanwendungen mit Zugriff auf den Verschlüsselungsschlüssel entschlüsselt werden.  Der Verschlüsselungsschlüssel wird nie für SQL verfügbar gemacht und kann im [Windows-Zertifikatspeicher](sql-database-always-encrypted.md) oder in [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) gespeichert werden.

### <a name="dynamic-data-masking"></a>Dynamische Datenmaskierung

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

Die dynamische Datenmaskierung für SQL-Datenbank schränkt die Offenlegung vertraulicher Daten ein, indem sie für nicht berechtigte Benutzer maskiert werden. Mit der dynamischen Datenmaskierung werden in Azure SQL-Datenbank automatisch potenziell sensible Daten ermittelt und direkt umsetzbare Empfehlungen bereitgestellt, um diese Felder mit minimalen Auswirkungen auf die Anwendungsschicht zu maskieren. Hierzu werden die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern verborgen, ohne dass die Daten in der Datenbank geändert werden. Weitere Informationen finden Sie unter [Dynamische Datenmaskierung für SQL-Datenbank](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Sicherheitsverwaltung

### <a name="vulnerability-assessment"></a>Sicherheitsrisikobewertung

Die [Sicherheitsrisikobewertung](sql-vulnerability-assessment.md) ist ein einfach zu konfigurierender Dienst, mit dem potenzielle Schwachstellen in der Datenbank ermittelt, nachverfolgt und behoben werden können. Damit soll die allgemeine Datenbanksicherheit proaktiv verbessert werden. Die Sicherheitsrisikobewertung ist Bestandteil des Advanced Data Security-Angebots (ADS). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf die Sicherheitsrisikobewertung und ihre Verwaltung sind über das zentrale SQL ADS-Portal möglich.

### <a name="data-discovery--classification"></a>Datenermittlung und -klassifizierung

Datenermittlung und -klassifizierung (zurzeit in der Vorschau) bietet erweiterte Funktionen für Azure SQL-Datenbank zum Ermitteln, Klassifizieren, Bezeichnen und Schützen sensibler Daten in Ihren Datenbanken. Das Ermitteln und Klassifizieren Ihrer vertraulichen Daten (Geschäfts-/Finanz-/Gesundheits-, personenbezogene Daten usw.) kann eine entscheidende Rolle in der Strategie Ihrer Organisation zum Datenschutz spielen. Das Feature kann als Infrastruktur für Folgendes dienen:

- Verschiedene Sicherheitsszenarien, z.B. Überwachung und Warnungen bei abweichendem Zugriff auf sensible Daten
- Steuern des Zugriffs auf und Härten der Sicherheit von Datenbanken, die sensible Daten enthalten
- Unterstützen der Einhaltung von Datenschutzstandards und gesetzlicher Bestimmungen

Weitere Informationen finden Sie unter [Erste Schritte bei der Datenermittlung und -klassifizierung](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Compliance

Zusätzlich zu den oben aufgeführten Features und Funktionen, mit denen Ihre Anwendung eine Reihe von Sicherheitsanforderungen erfüllen kann, wird Azure SQL-Datenbank außerdem regelmäßigen Überprüfungen unterzogen und ist für eine Reihe von Compliancestandards zertifiziert. Weitere Informationen finden Sie im [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), wo die aktuellste Liste von Compliance-Zertifizierungen für SQL-Datenbank angezeigt wird.

### <a name="feature-restrictions"></a>Featureeinschränkungen

Featureeinschränkungen schützen bei einigen Formen der Einschleusung von SQL-Befehlen vor Preisgabe von Informationen über die Datenbank, selbst wenn die Einschleusung von SQL-Befehlen erfolgreich ist. Weitere Informationen finden Sie unter [Featureeinschränkungen für Azure SQL-Datenbank](sql-database-feature-restrictions.md).

## <a name="next-steps"></a>Nächste Schritte

- Eine Beschreibung der Nutzung von Zugriffssteuerungsfunktionen in SQL-Datenbank finden Sie unter [Steuern des Zugriffs](sql-database-control-access.md).
- Eine Erörterung zur Datenbanküberwachung finden Sie unter [Überwachung von SQL-Datenbank](sql-database-auditing.md).
- Eine Erörterung zur Bedrohungserkennung finden Sie unter [Bedrohungserkennung von SQL-Datenbank](sql-database-threat-detection.md).
