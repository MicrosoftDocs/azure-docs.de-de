---
title: Übersicht über die Sicherheit der Azure-Datenbank | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Sicherheitsfeatures der Azure-Datenbank.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 7e0e93c82279ec1a4fbecbbf27c7a1866286b2f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587603"
---
# <a name="azure-database-security-overview"></a>Übersicht über die Sicherheit der Azure-Datenbank

Sicherheit ist einer der wichtigsten Aspekte beim Verwalten von Datenbanken und hat auch bei Azure SQL-Datenbank höchste Priorität. Azure SQL-Datenbank unterstützt Verbindungssicherheit mit Firewallregeln und Verbindungsverschlüsselung. Die Lösung unterstützt sowohl die Authentifizierung mit Benutzername und Kennwort als auch die Azure Active Directory-Authentifizierung mit Identitäten, die von Azure Active Directory (Azure AD) verwaltet werden. Für die Autorisierung wird eine rollenbasierte Zugriffssteuerung verwendet.

Zur Unterstützung der Verschlüsselung werden von Azure SQL-Datenbank Datenbanken, die dazugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit ver- und entschlüsselt, ohne dass Änderungen an der Anwendung erforderlich sind.

Darüber hinaus bietet Microsoft noch weitere Verschlüsselungsmöglichkeiten für Unternehmensdaten:

-   Die Verschlüsselung auf Zellenebene ist verfügbar, um bestimmte Datenspalten oder sogar Datenzellen mit verschiedenen Verschlüsselungsschlüsseln zu verschlüsseln.
-   Wenn Sie ein Hardwaresicherheitsmodul oder eine zentrale Verwaltung Ihrer Verschlüsselungsschlüsselhierarchie benötigen, können Sie Azure Key Vault mit SQL Server auf einem virtuellen Azure-Computer (Virtual Machine, VM) verwenden.
-   Always Encrypted (aktuell in der Vorschauversion verfügbar) macht die Verschlüsselung für Anwendungen transparent. Darüber hinaus ermöglicht dieses Feature Clients die Verschlüsselung vertraulicher Daten in Clientanwendungen, ohne die Verschlüsselungsschlüssel für SQL-Datenbank freizugeben.

Mit der Azure SQL-Datenbank-Überwachung können Unternehmen Ereignisse in einem Überwachungsprotokoll in Azure Storage erfassen. SQL-Datenbanküberwachung kann auch in Microsoft Power BI integriert werden, um detaillierte Berichte und Analysen leichter zu ermöglichen.

Azure SQL-Datenbanken können umfassend geschützt werden, um die meisten gesetzlichen oder sicherheitsbezogenen Anforderungen (einschließlich HIPAA, ISO 27001/27002 und PCI-DSS Level 1) zu erfüllen. Eine aktuelle Liste mit Sicherheitszertifizierungen finden Sie im [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/services/).

In diesem Artikel werden die Grundlagen für den Schutz von Microsoft Azure SQL-Datenbanken für strukturierte, tabellarische und relationale Daten erörtert. In diesem Artikel erhalten Sie vor allem die ersten Informationen zu den Ressourcen zum Schützen von Daten, zum Steuern des Zugriffs und für die proaktive Überwachung.

## <a name="protection-of-data"></a>Schutz von Daten

Mit SQL-Datenbank können Sie Ihre Daten mithilfe der folgenden Verschlüsselungsmöglichkeiten schützen:

- Verschlüsseln von Daten während der Übertragung durch [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- Verschlüsseln von ruhenden Daten durch [transparente Datenverschlüsselung](https://go.microsoft.com/fwlink/?LinkId=526242).
- Verschlüsseln verwendeter Daten durch [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Es gibt noch weitere Möglichkeiten zum Verschlüsseln Ihrer Daten:

-   [Verschlüsselung auf Zellenebene](https://msdn.microsoft.com/library/ms179331.aspx) werden bestimmte Spalten oder sogar Zellen, die Daten enthalten, mit unterschiedlichen Verschlüsselungsschlüsseln verschlüsselt.
-   Verwenden Sie [Azure Key Vault mit SQL Server auf einem virtuellen Azure-Computer](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), wenn Sie ein Hardwaresicherheitsmodul oder eine zentrale Verwaltung Ihrer Verschlüsselungsschlüsselhierarchie benötigen.

### <a name="encryption-in-motion"></a>Verschlüsselung von Daten während der Übertragung

Ein häufiges Problem von Client-/Serveranwendungen ist der Datenschutz während der Übertragung über öffentliche und private Netzwerke. Wenn Daten bei der Übertragung über ein Netzwerk nicht verschlüsselt sind, können sie von nicht autorisierten Benutzern erfasst und gestohlen werden. Wenn Sie Datenbankdienste nutzen, stellen Sie sicher, dass die Daten zwischen Datenbankclient und -server verschlüsselt sind. Stellen Sie außerdem sicher, dass die Daten zwischen den Datenbankservern, die miteinander und mit Anwendungen der mittleren Ebene kommunizieren, verschlüsselt sind.

Ein Problem bei der Netzwerkverwaltung ist der Schutz von Daten, die zwischen Anwendungen über ein nicht vertrauenswürdiges Netzwerk gesendet werden. Sie können [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) zur Authentifizierung von Servern und Clients verwenden und damit anschließend Nachrichten zwischen den authentifizierten Parteien verschlüsseln.

Beim Authentifizierungsvorgang sendet ein TLS-/SSL-Client eine Nachricht an einen TLS-/SSL-Server. Der Server antwortet mit den Informationen, die der Server benötigt, um sich zu authentifizieren. Client und Server tauschen zusätzlich Sitzungsschlüssel aus, und der Authentifizierungsdialog wird beendet. Nach Abschluss der Authentifizierung kann die durch SSL geschützte Kommunikation zwischen Server und Client unter Verwendung der symmetrischen Verschlüsselungsschlüssel beginnen, die während der Authentifizierung eingerichtet wurden.

Für alle Verbindungen mit Azure SQL-Datenbank ist eine Verschlüsselung (TLS/SSL) erforderlich, und zwar zu jedem Zeitpunkt während der Übertragung von Daten in die und aus der Datenbank. SQL-Datenbank verwendet TLS/SSL zur Authentifizierung von Servern und Clients und verschlüsselt damit anschließend Nachrichten zwischen den authentifizierten Parteien. 

Sie müssen in der Verbindungszeichenfolge Ihrer Anwendung Parameter angeben, damit die Verbindung verschlüsselt und dem Serverzertifikat nicht vertraut wird. (Dies erfolgt, wenn Sie die Verbindungszeichenfolge aus dem Azure-Portal kopieren.) Andernfalls wird die Identität des Servers nicht überprüft, und die Verbindung ist für Man-in-the-Middle-Angriffe anfällig. Für den ADO.NET-Treiber lauten diese Verbindungszeichenfolgenparameter beispielsweise `Encrypt=True` und `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Sie können verschiedene Vorsichtsmaßnahmen zum Schützen der Datenbank ergreifen. So können Sie beispielsweise ein sicheres System entwerfen, vertrauliche Ressourcen verschlüsseln und eine Firewall für die Datenbankserver erstellen. In einem Szenario, in dem physische Medien (wie Festplatten oder Sicherungsbänder) gestohlen werden, kann eine böswillige Partei jedoch die Datenbank einfach wiederherstellen oder anfügen und die Daten durchsuchen.

Eine Lösung besteht darin, die vertraulichen Daten in der Datenbank zu verschlüsseln und die Schlüssel zu schützen, die zum Verschlüsseln der Daten mit einem Zertifikat verwendet werden. Bei dieser Lösung wird verhindert, dass Personen ohne Schlüssel die Daten verwenden. Diese Art des Schutzes muss allerdings geplant werden.

Zur Lösung dieses Problems unterstützen SQL Server und SQL-Datenbank die [transparente Datenverschlüsselung (Transparent Data Encryption, TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Die transparente Datenverschlüsselung verschlüsselt Datendateien von SQL Server und SQL-Datenbank. Dies wird als Verschlüsselung ruhender Daten bezeichnet.

Die transparente Datenverschlüsselung bietet Schutz vor Bedrohungen durch böswillige Aktivitäten. TDE ver- und entschlüsselt die Datenbank, die zugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit, ohne dass Änderungen an der Anwendung erforderlich sind.  

Die transparente Datenverschlüsselung verschlüsselt den Speicher einer gesamten Datenbank mithilfe eines symmetrischen Schlüssels, der als Datenbankverschlüsselungsschlüssel bezeichnet wird. In SQL-Datenbank wird der Datenbankverschlüsselungsschlüssel mit einem integrierten Serverzertifikat geschützt. Das integrierte Serverzertifikat ist für jeden SQL-Datenbank-Server eindeutig.

Wenn eine Datenbank Teil einer Geo-DR-Beziehung ist, wird sie auf jedem Server durch einen anderen Schlüssel geschützt. Sind zwei Datenbanken mit dem gleichen Server verbunden, verwenden sie das gleiche integrierte Zertifikat. Microsoft führt für diese Zertifikate nach spätestens 90 Tagen automatisch eine Rotation durch. 

Weitere Informationen finden Sie unter [Transparente Datenverschlüsselung (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Verschlüsselung verwendeter Daten (Client)

Bei einer Verletzung der Datensicherheit werden meistens wichtige Daten wie Kreditkartennummern oder personenbezogene Informationen entwendet. Datenbanken können sich als wahre Goldgrube für vertrauliche Informationen erweisen. Sie können persönliche Daten von Kunden (z. B. nationale Identifikationsnummern), vertrauliche wettbewerbsrelevante Informationen und geistiges Eigentum enthalten. Verloren gegangene oder gestohlene Daten können die eigene Marke beschädigen, zu Wettbewerbsnachteilen führen und hohe Geldstrafen und sogar Gerichtsverfahren nach sich ziehen. Das gilt insbesondere für Kundendaten.

![Darstellung des Features Always Encrypted als Schloss und Schlüssel](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) ist ein Feature zum Schutz vertraulicher Daten, die in Azure SQL-Datenbank oder in SQL Server-Datenbanken gespeichert sind. Mit Always Encrypted können Clients vertrauliche Daten in Clientanwendungen verschlüsseln, ohne die Verschlüsselungsschlüssel für das Datenbankmodul (SQL-Datenbank oder SQL Server) jemals offenzulegen.

Always Encrypted ermöglicht eine Trennung zwischen den Personen, die die Daten besitzen (und anzeigen können), und den Personen, die die Daten verwalten (aber ansonsten keinen Zugriff haben sollten). Dadurch wird sichergestellt, dass lokale Datenbankadministratoren, Clouddatenbankbetreiber und andere nicht autorisierte Benutzer mit umfangreichen Berechtigungen nicht auf die verschlüsselten Daten zugreifen können.

Darüber hinaus macht Always Encrypted die Verschlüsselung für Anwendungen transparent. Auf dem Clientcomputer wird ein Always Encrypted-fähiger Treiber installiert, um die automatische Ver- und Entschlüsselung vertraulicher Daten in der Clientanwendung zu ermöglichen. Der Treiber verschlüsselt die Daten in vertraulichen Spalten, bevor die Daten an das Datenbankmodul übergeben werden. Der Treiber schreibt Abfragen automatisch neu, sodass die Semantik der Anwendung beibehalten wird. Analog dazu werden in verschlüsselten Datenbankspalten gespeicherte Daten aus Abfrageergebnissen durch den Treiber transparent entschlüsselt.

## <a name="access-control"></a>Zugriffssteuerung

Zur Gewährleistung der Sicherheit steuert SQL-Datenbank den Zugriff durch die Verwendung von:

- Firewallregeln, die die Konnektivität nach IP-Adresse einschränken.
- Authentifizierungsmechanismen, bei denen die Benutzer ihre Identität nachweisen müssen.
- Autorisierungsmechanismen, die Benutzer auf bestimmte Aktionen und Daten begrenzen.

### <a name="database-access"></a>Datenbankzugriff

Datenschutz beginnt mit der Steuerung des Zugriffs auf die Daten. Das Rechenzentrum, in dem Ihre Daten gehostet werden, verwaltet den physischen Zugriff. Sie können eine Firewall zum Verwalten der Sicherheit auf der Netzwerkebene konfigurieren. Darüber hinaus steuern Sie den Zugriff durch Konfigurieren von Anmeldungen für die Authentifizierung sowie durch Definieren von Berechtigungen für Server- und Datenbankrollen.

#### <a name="firewall-and-firewall-rules"></a>Firewall und Firewallregeln

[Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) bietet einen relationalen Datenbankdienst für Azure und andere internetbasierte Anwendungen. Zum Schutz Ihrer Daten verhindern Firewalls jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Der Dienst Azure SQL-Datenbank ist nur über TCP-Port 1433 verfügbar. Wenn Sie von Ihrem Computer auf eine SQL-Datenbank zugreifen möchten, stellen Sie sicher, dass die Firewall Ihres Clientcomputers die ausgehende TCP-Kommunikation über TCP-Port 1433 zulässt. Blockieren Sie eingehende Verbindungen über TCP-Port 1433, wenn diese nicht für andere Anwendungen benötigt werden.

#### <a name="authentication"></a>Authentication

Authentifizierung bezieht sich darauf, auf welche Weise Sie Ihre Identität beim Herstellen der Verbindung mit der Datenbank nachweisen. SQL-Datenbank unterstützt zwei Arten der Authentifizierung:

-   **SQL Server-Authentifizierung**: Bei der Erstellung einer logischen SQL-Instanz wird ein einzelnes Anmeldekonto erstellt, das als Azure SQL-Datenbank-Abonnentenkonto bezeichnet wird. Dieses Konto stellt die Verbindung unter Verwendung der [SQL Server-Authentifizierung](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (Benutzername und Kennwort) her. Dieses Konto ist ein Administrator auf der logischen Serverinstanz und in allen Benutzerdatenbanken, die an diese Instanz angefügt sind. Die Berechtigungen des Abonnentenkontos können nicht eingeschränkt werden. Nur eines dieser Konten kann vorhanden sein.
-   **Azure Active Directory-Authentifizierung**: Die [Azure Active Directory-Authentifizierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ist ein Mechanismus zum Herstellen einer Verbindung mit Azure SQL-Datenbank und Azure SQL Data Warehouse unter Verwendung von Identitäten in Azure Active Directory (Azure AD). Damit können Sie die Identitäten von Datenbankbenutzern zentral verwalten.

![Azure AD-Authentifizierung mit SQL-Datenbank](./media/azure-databse-security-overview/azure-database-fig2.png)

 Die Azure AD-Authentifizierung bietet unter anderem folgende Vorteile:
  - Es wird eine Alternative zur SQL Server-Authentifizierung bereitgestellt.
  - Sie unterbindet die datenbankserverübergreifende Verbreitung von Benutzeridentitäten und ermöglicht Kennwortrotationen an einem zentralen Ort.
  - Sie können Datenbankberechtigungen mithilfe von externen Gruppen (Azure AD) verwalten.
  - Durch Aktivieren der integrierten Windows-Authentifizierung und anderer Authentifizierungsformen, die Azure AD unterstützt, wird das Speichern von Kennwörtern überflüssig.

#### <a name="authorization"></a>Autorisierung

[Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) bezieht sich auf die Aktionen, die ein Benutzer in einer Azure SQL-Datenbank ausführen kann. Dies wird durch die [Rollenmitgliedschaften](https://msdn.microsoft.com/library/ms189121) und [objektebenenspezifischen Berechtigungen](https://msdn.microsoft.com/library/ms191291.aspx) der Datenbank Ihres Benutzerkontos gesteuert. Im Zuge der Autorisierung wird bestimmt, auf welche sicherungsfähigen Ressourcen ein Prinzipal zugreifen kann und welche Vorgänge für diese Ressourcen zulässig sind.

### <a name="application-access"></a>Anwendungszugriff

#### <a name="dynamic-data-masking"></a>Dynamische Datenmaskierung

Ein Servicemitarbeiter in einem Callcenter kann Anrufer anhand mehrerer Ziffern ihrer US-Sozialversicherungsnummer oder Kreditkartennummer identifizieren. Diese Datenelemente sollten dem Servicemitarbeiter jedoch nicht vollständig angezeigt werden.

Sie können eine Maskierungsregel definieren, mit der die US-Sozialversicherungsnummer oder Kreditkartennummer im Resultset einer Abfrage bis auf die letzten vier Ziffern ausgeblendet wird.

![Maskierung einer Nummer, die zwischen einer SQL-Datenbank und Geschäfts-Apps gesendet wird](./media/azure-databse-security-overview/azure-database-fig3.png)

In einem weiteren Beispiel kann eine entsprechende Datenmaske zum Schutz personenbezogener Informationen definiert werden. Ein Entwickler kann dann Produktionsumgebungen zu Problembehandlungszwecken abfragen, ohne Compliancebestimmungen zu verletzen.

Die [dynamische Datenmaskierung in SQL-Datenbanken](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) schränkt die Offenlegung sensibler Daten ein, indem diese für nicht berechtigte Benutzer maskiert werden. Die dynamische Datenmaskierung wird für die Version V12 von Azure SQL-Datenbank unterstützt.

Mit der [dynamischen Datenmaskierung](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) können Sie mit minimalen Auswirkungen auf die Anwendungsschicht festlegen, wie viel von den sensiblen Daten offengelegt werden soll. Dies trägt zur Verhinderung des unbefugten Zugriffs auf sensible Daten bei. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, ohne dass die Daten in der Datenbank geändert werden.

> [!Note]
> Die dynamische Datenmaskierung kann von den Rollen "Azure-Datenbankadministrator", "Serveradministrator" oder "Sicherheitsbeauftragter" konfiguriert werden.

#### <a name="row-level-security"></a>Sicherheit auf Zeilenebene

Eine weitere gängige Sicherheitsanforderung für mehrinstanzenfähige Datenbanken ist die [Sicherheit auf Zeilenebene](https://msdn.microsoft.com/library/dn765131.aspx). Mit diesem Feature können Sie den Zugriff auf Zeilen in einer Datenbanktabelle auf der Grundlage von Merkmalen des Benutzers steuern, der eine Abfrage ausführt. (Beispielmerkmale sind eine Gruppenmitgliedschaft und ein Ausführungskontext.)

![Sicherheit auf Zeilenebene, die einem Benutzer den Zugriff auf Zeilen in einer Tabelle über eine Client-App ermöglicht](./media/azure-databse-security-overview/azure-database-fig4.png)

Die Zugriffsbeschränkungslogik befindet sich auf der Datenbankebene und nicht auf einer anderen, von den Daten getrennten Anwendungsebene. Das Datenbanksystem wendet die Zugriffsbeschränkungen unabhängig von der Ebene bei jedem Datenzugriffsversuch an. Dadurch bietet Ihr Sicherheitssystem eine geringere Angriffsfläche und ist zuverlässiger und robuster.

Die Sicherheit auf Zeilenebene führt die prädikatbasierte Zugriffssteuerung ein. Diese zeichnet sich durch eine flexible, zentrale Auswertung unter Einbeziehung von Metadaten oder anderen Kriterien aus, die der Administrator für geeignet hält. Das Prädikat wird als Kriterium verwendet, um anhand von Benutzerattributen zu bestimmen, ob der Benutzer über die erforderlichen Datenzugriffsberechtigungen verfügt. Sie können mithilfe der prädikatbasierten Zugriffssteuerung eine bezeichnerbasierte Zugriffssteuerung implementieren.

## <a name="proactive-monitoring"></a>Proaktive Überwachung

Zum Schutz Ihrer Daten stellt SQL-Datenbank Funktionen für die *Überwachung* und *Bedrohungserkennung* bereit.

### <a name="auditing"></a>Überwachung

Durch die [Überwachung von Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) können Sie bessere Einblicke in Ereignisse und Änderungen in der Datenbank gewinnen. Beispiele hierfür sind Datenaktualisierungen und -abfragen.

Die SQL-Datenbanküberwachung verfolgt Datenbankereignisse und schreibt diese in ein Überwachungsprotokoll in Ihrem Azure-Speicherkonto. Die Überwachung kann Sie dabei unterstützen, gesetzliche Bestimmungen einzuhalten, Datenbankaktivitäten nachzuvollziehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftliche Probleme oder mutmaßliche Sicherheitsverstöße hinweisen können. Die Überwachung ermöglicht und unterstützt die Einhaltung von Standards, garantiert diese aber nicht.

Sie können die SQL-Datenbanküberwachung für folgende Zwecke verwenden:

- **Beibehalten** eines Überwachungspfads von ausgewählten Ereignissen. Sie können Kategorien für zu protokollierende Datenbankaktionen und -ereignisse konfigurieren.
- **Melden** von Datenbankaktivitäten. Sie können vorkonfigurierte Berichte und ein Dashboard verwenden, um schnell mit der Berichterstattung über Aktivitäten und Ereignisse zu beginnen.
- **Analysieren** von Berichten. Sie können nach verdächtigen Ereignissen, ungewöhnliche Aktivitäten und Trends suchen.

Es stehen zwei Überwachungsmethoden zur Verfügung:

-   **Blobüberwachung**: Die Protokolle werden in Azure Blob Storage geschrieben. Dies ist eine neuere Überwachungsmethode. Sie bietet eine höhere Leistung, unterstützt eine detailliertere Überwachung auf Objektebene und ist kostengünstiger.
-   **Tabellenüberwachung**: Protokolle werden in Azure-Tabellenspeicher geschrieben.

### <a name="threat-detection"></a>Bedrohungserkennung

[Advanced Threat Protection für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection) erkennt verdächtige Aktivitäten, die auf potenzielle Sicherheitsbedrohungen hinweisen. Mit der Bedrohungserkennung können Sie auf verdächtige Ereignisse in der Datenbank (wie die Einschleusung von SQL-Befehlen) reagieren, sobald sie auftreten. Sie bietet Warnungen und ermöglicht die Verwendung der Azure SQL-Datenbank-Überwachung zur Untersuchung verdächtiger Ereignisse.

![Bedrohungserkennung für SQL-Datenbank und eine Web-App mit einem externen Angreifer und einem böswilligen Insider](./media/azure-databse-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) stellt eine Reihe erweiterter SQL-Sicherheitsfunktionen bereit, darunter Datenermittlung und -klassifizierung, Risikoanalyse und Erkennung von Bedrohungen. 

- [Datenermittlung und -klassifizierung](../sql-database/sql-database-data-discovery-and-classification.md)
- [Sicherheitsrisikobewertung](../sql-database/sql-vulnerability-assessment.md)  
- [Bedrohungserkennung](../sql-database/sql-database-threat-detection.md)

[Advanced Threat Protection für Azure Database for PostgreSQL](../postgresql/concepts-data-access-and-security-threat-protection.md) bietet eine neue Sicherheitsebene, die es Ihnen ermöglicht, potenzielle Bedrohungen zu erkennen und darauf zu reagieren. Zu diesem Zweck werden Sicherheitswarnungen zu anomalen Aktivitäten bereitgestellt. Benutzer erhalten Warnungen zu verdächtigen Datenbankaktivitäten und potenziellen Sicherheitsrisiken sowie zu anomalen Datenbankzugriffs- und -abfragemustern. Warnungen von Advanced Threat Protection für Azure Database for PostgreSQL sind in Azure Security Center integriert. Die Typen von Warnungen umfassen Folgendes:

- Zugriff über einen ungewöhnlichen Ort
- Zugriff über ein ungewöhnliches Azure-Rechenzentrum 
- Zugriff über einen unbekannten Prinzipal 
- Zugriff über eine potenziell schädliche Anwendung 
- Brute-Force-Angriffe auf Azure Database for PostgreSQL-Anmeldeinformationen 

[Advanced Threat Protection für Azure Database for MySQL](../mysql/concepts-data-access-and-security-threat-protection.md) bietet einen ähnlichen Schutz wie PostgreSQL Advanced Protection.  

## <a name="centralized-security-management"></a>Zentralisierte Sicherheitsverwaltung

Mit [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) können Sie Bedrohungen verhindern, erkennen und bekämpfen. Es bietet eine integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements. Er unterstützt Sie bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem umfassenden Spektrum von Sicherheitslösungen verwendet werden.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) bietet Einblicke in die Sicherheit Ihrer Server und Datenbanken und trägt so zum Schutz der Daten in SQL-Datenbank bei. Mit Security Center haben Sie folgende Möglichkeiten:

- Definieren von Richtlinien für die Verschlüsselung und Überwachung in SQL-Datenbank
- Überwachen der Sicherheit von SQL-Datenbank-Ressourcen in allen Abonnements
- Schnelles Erkennen und Beheben von Sicherheitsproblemen
- Integrieren von Warnungen aus der [Bedrohungserkennung von Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)

Security Center unterstützt rollenbasierten Zugriff.

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) ermittelt und klassifiziert automatisch potenziell sensible Daten, stellt einen Bezeichnungsmechanismus zum dauerhaften Kennzeichnen der sensiblen Daten mit Klassifizierungsattributen bereit und bietet ein detailliertes Dashboard, das den Klassifizierungsstatus der Datenbank zeigt.  

Darüber hinaus wird die Vertraulichkeit des Resultsets von SQL-Abfragen berechnet, sodass Abfragen, die sensible Daten extrahieren, explizit überwacht und die Daten geschützt werden können. Weitere Informationen zu SQL Information Protection finden Sie unter „Azure SQL-Datenbank – Datenermittlung und -klassifizierung“.

Sie können [SQL Information Protection-Richtlinien](../security-center/security-center-info-protection-policy.md) in Azure Security Center konfigurieren.

## <a name="azure-marketplace"></a>Azure Marketplace

Der Azure Marketplace ist ein Onlinemarktplatz für Anwendungen und Dienste, über den Startups und unabhängige Softwareanbieter (ISVs) ihre Lösungen Azure-Kunden auf der ganzen Welt anbieten können.
Der Azure Marketplace integriert die einzelnen Ökosysteme von Microsoft Azure-Partnerunternehmen in einer einheitlichen Plattform und kann unseren Kunden und Partnern auf diese Weise noch bessere Dienste leisten. Sie können [eine Suche ausführen](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1), um die auf dem Azure Marketplace verfügbaren Datenbanksicherheitsprodukte anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- [Schützen der Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center und Azure SQL-Datenbank-Dienst](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Bedrohungserkennung von SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Optimieren der Leistung von SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
