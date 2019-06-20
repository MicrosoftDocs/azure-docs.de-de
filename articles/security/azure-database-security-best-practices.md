---
title: Bewährte Methoden für die Datenbanksicherheit – Microsoft Azure
description: Dieser Artikel enthält bewährte Methoden zur Azure-Datenbanksicherheit.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 9cd02172af5246c60b93a6e4696988268abed506
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258684"
---
# <a name="azure-database-security-best-practices"></a>Bewährte Methoden für die Azure-Datenbanksicherheit
In diesem Artikel werden bewährte Methoden für die Datenbanksicherheit beschrieben.

Die bewährten Methoden basieren auf einer gemeinsamen Linie und eignen sich für aktuelle Funktionen und Features der Azure-Plattform. Meinungen und Technologien ändern sich im Laufe der Zeit. Dieser Artikel wird daher regelmäßig aktualisiert, um diese Änderungen widerzuspiegeln.

## <a name="secure-databases"></a>Schützen von Datenbanken
Sicherheit ist einer der wichtigsten Aspekte beim Verwalten von Datenbanken und hat auch bei [Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/) höchste Priorität. Ihre Datenbanken können umfassend geschützt werden, um die meisten gesetzlichen oder sicherheitsbezogenen Anforderungen (HIPAA, ISO 27001/27002, PCI-DSS Level 1) zu erfüllen. Eine aktuelle Liste mit Sicherheitszertifizierungen finden Sie auf der [Microsoft Trust Center-Website](https://azure.microsoft.com/support/trust-center/services/). Sie können Ihre Datenbanken auch gemäß den jeweils geltenden rechtlichen Anforderungen in spezifischen Azure-Datencentern anordnen.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Verwenden von Firewallregeln zum Einschränken des Datenbankzugriffs
Microsoft Azure SQL-Datenbank ist ein Dienst für relationale Datenbanken für Azure und andere internetbasierte Anwendungen. Zur Gewährleistung der Zugriffssicherheit steuert SQL-Datenbank den Zugriff durch die Verwendung von:

- Firewallregeln, die die Konnektivität nach IP-Adresse einschränken.
- Authentifizierungsmechanismen, bei denen die Benutzer ihre Identität nachweisen müssen.
- Autorisierungsmechanismen, die Benutzer auf bestimmte Aktionen und Daten begrenzen.

Firewalls verhindern jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.

Die folgende Abbildung veranschaulicht, wo Sie eine Firewallregel in SQL-Datenbank festlegen:

![Firewallregeln](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Der Dienst Azure SQL-Datenbank ist nur über TCP-Port 1433 verfügbar. Wenn Sie von Ihrem Computer auf eine SQL-Datenbank zugreifen möchten, stellen Sie sicher, dass die Firewall Ihres Clientcomputers die ausgehende TCP-Kommunikation über TCP-Port 1433 zulässt. Blockieren Sie eingehende Verbindungen auf TCP-Port 1433 mithilfe von Firewallregeln, wenn Sie diese Verbindungen nicht für andere Anwendungen benötigen.

Im Rahmen des Verbindungsprozesses werden Verbindungen von virtuellen Azure-Computern an eine andere IP-Adresse und einen Port umgeleitet, wobei diese Angaben für jede Workerrolle jeweils eindeutig sind. Die Portnummer liegt im Bereich von 11000 bis 11999. Weitere Informationen zu TCP-Ports finden Sie unter [Andere Ports als 1433 für ADO.NET 4.5](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Weitere Informationen zu Firewallregeln in SQL-Datenbank finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Zusätzlich zu den IP-Regeln verwaltet die Firewall VNET-Regeln. VNET-Regeln basieren auf den VNET-Dienstendpunkten. VNET-Regeln sollten den IP-Regeln in manchen Fällen vorgezogen werden. Weitere Informationen finden Sie unter [Virtual Network service endpoints and rules for Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) (VNET-Dienstendpunkte und -Regeln für Azure SQL-Datenbank).

## <a name="enable-database-authentication"></a>Aktivieren der Datenbankauthentifizierung
SQL-Datenbank unterstützt zwei Authentifizierungsarten: die SQL Server-Authentifizierung und die Azure AD-Authentifizierung.

### <a name="sql-server-authentication"></a>*SQL Server-Authentifizierung*

Daraus ergeben sich u. a. die folgenden Vorteile:

- Durch diese Authentifizierung kann SQL-Datenbank Umgebungen mit gemischten Betriebssystemen unterstützen, bei denen nicht alle Benutzer von einer Windows-Domäne authentifiziert werden.
- Außerdem kann SQL-Datenbank ältere Anwendungen sowie Drittanbieteranwendungen unterstützen, für die eine SQL Server-Authentifizierung erforderlich ist.
- Ermöglicht es Benutzern, von unbekannten oder nicht vertrauenswürdigen Domänen aus eine Verbindung herzustellen. Beispiel: Eine Anwendung, bei der etablierte Kunden eine Verbindung mit zugewiesenen SQL Server-Anmeldungen herstellen, um den Status ihrer Bestellungen zu erhalten.
- Ermöglicht SQL-Datenbank die Unterstützung webbasierter Anwendungen, für die Benutzer ihre eigenen Identitäten erstellen.
- Ermöglicht Softwareentwicklern die Verteilung ihrer Anwendungen mithilfe einer komplexen Berechtigungshierarchie, die auf bekannten, voreingestellten SQL Server-Anmeldungen basiert.

> [!NOTE]
> Für die SQL Server-Authentifizierung kann das Kerberos-Sicherheitsprotokoll nicht verwendet werden.

Bei Verwendung der SQL Server-Authentifizierung ist Folgendes erforderlich:

- Sie verwalten die sicheren Anmeldeinformationen selbst.
- Sie schützen die Anmeldeinformationen in der Verbindungszeichenfolge.
- Sie schützen (falls zutreffend) die Anmeldeinformationen, die über das Netzwerk vom Webserver an die Datenbank übergeben werden. Weitere Informationen finden Sie unter [Gewusst wie: Herstellen einer Verbindung mit SQL Server mit SQL-Authentifizierung in ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory-Authentifizierung*
Die [Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)-Authentifizierung ist ein Mechanismus zum Herstellen einer Verbindung mit Azure SQL-Datenbank und SQL Data Warehouse unter Verwendung von Identitäten in Azure Active Directory (Azure AD). Mithilfe der Azure AD-Authentifizierung können Sie die Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten zentral verwalten. Die zentrale ID-Verwaltung ermöglicht eine einheitliche Verwaltung von Datenbankbenutzern und vereinfacht die Berechtigungsverwaltung.

> [!NOTE]
> Es wird empfohlen, die Azure AD-Authentifizierung zu verwenden.

Daraus ergeben sich u. a. die folgenden Vorteile:

- Es wird eine Alternative zur SQL Server-Authentifizierung bereitgestellt.
- Dadurch wird die datenbankserverübergreifenden Vermehrung von Benutzeridentitäten vermieden.
- Die Kennwortrotation wird über eine zentrale Stelle ermöglicht.
- Kunden können Datenbankberechtigungen mithilfe von externen Gruppen (Azure AD) verwalten.
- Durch das Aktivieren der integrierten Windows-Authentifizierung und andere von Azure Active Directory unterstützte Authentifizierungsformen wird das Speichern von Kennwörtern überflüssig.
- Zum Authentifizieren von Identitäten auf Datenbankebene werden eigenständige Datenbankbenutzer verwendet.
- Die tokenbasierte Authentifizierung für Anwendungen, die eine Verbindung mit SQL-Datenbank herstellen, wird unterstützt.
- Sie unterstützt AD FS (Domänenverbund) sowie native Benutzer-/Kennwortauthentifizierung für eine lokale Azure Active Directory-Instanz ohne Domänensynchronisierung.
- Azure AD unterstützt Verbindungen von SQL Server Management Studio, bei denen universelle Active Directory-Authentifizierungsverfahren verwendet werden, zu denen auch die mehrstufige Authentifizierung gehört. Die mehrstufige Authentifizierung bietet eine sichere Authentifizierung über unterschiedliche Überprüfungsoptionen – Telefonanruf, SMS, Smartcards mit PIN oder Benachrichtigung in einer mobilen App. Weitere Informationen finden Sie unter [Universelle Authentifizierung bei SQL-Datenbank und SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Die Konfigurationsschritte schließen die folgenden Verfahren zum Konfigurieren und Verwenden der Azure AD-Authentifizierung ein:

- Erstellen und Auffüllen von Azure AD
- Optional: Ordnen Sie die Active Directory-Instanz zu oder ändern Sie diejenige, die derzeit Ihrem Azure-Abonnement zugeordnet ist.
- Erstellen Sie einen Azure Active Directory-Administrators für Azure SQL-Datenbank oder [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurieren der Clientcomputer
- Erstellen eigenständiger Datenbankbenutzer in der Datenbank, die Azure AD-Identitäten zugeordnet sind
- Herstellen einer Verbindung mit Ihrer Datenbank unter Verwendung von Azure AD-Identitäten

Ausführlichere Informationen finden Sie unter [Verwenden der Azure Active Directory-Authentifizierung für die Authentifizierung bei SQL-Datenbank, der verwalteten Instanz oder SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Schützen Ihrer Daten durch Verschlüsselung und Sicherheit auf Zeilenebene
Durch [Transparent Data Encryption in Azure SQL-Datenbank](../sql-database/transparent-data-encryption-azure-sql.md) können Sie Daten auf einem Datenträger schützen und gleichzeitig unbefugten Hardwarezugriff vermeiden. TDE ver- und entschlüsselt die Datenbank, die zugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit, ohne dass Änderungen an der Anwendung erforderlich sind. Die transparente Datenverschlüsselung verschlüsselt den Speicher einer gesamten Datenbank mithilfe eines symmetrischen Schlüssels, der als Datenbankverschlüsselungsschlüssel bezeichnet wird.

Auch wenn der gesamte Speicher verschlüsselt ist, ist es sehr wichtig, dass Sie Ihre Datenbank selbst ebenfalls verschlüsseln. Dies ist eine Implementierung des Defense-in-Depth-Ansatzes (mehrschichtiger Schutz) für den Datenschutz. Falls Sie Azure SQL-Datenbank verwenden und sensible Daten wie beispielsweise Kreditkarteninformationen oder US-Sozialversicherungsnummern schützen möchten, können Sie Datenbanken mithilfe der FIPS 140-2-zertifizierten AES-Verschlüsselung (256 Bit) verschlüsseln. Diese Verschlüsselung erfüllt die Anforderungen aller Industriestandards (z.B. HIPAA und PCI).

Dateien, die mit der [Pufferpoolerweiterung (Buffer Pool Extension, BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) in Zusammenhang stehen, werden nicht verschlüsselt, wenn Sie eine Datenbank mit Transparent Data Encryption verschlüsseln. Sie müssen Verschlüsselungstools auf Dateisystemebene wie [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) oder das [Verschlüsselnde Dateisystem (Encrypting File System, EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) für Dateien verwenden, die mit der Pufferpoolerweiterung in Zusammenhang stehen.

Da ein autorisierter Benutzer, wie beispielsweise ein Sicherheits- oder Datenbankadministrator, auf die Daten zugreifen kann, selbst wenn Sie mit TDE verschlüsselt sind, sollten Sie auch die folgenden Empfehlungen befolgen:

- Aktivieren Sie die SQL Server-Authentifizierung auf Datenbankebene.
- Verwenden Sie die Azure AD-Authentifizierung mithilfe von [RBAC-Rollen](../role-based-access-control/overview.md).
- Achten Sie darauf, dass Benutzer und Anwendungen verschiedene Konten verwenden, um sich zu authentifizieren. Auf diese Weise können Sie die Anzahl von Benutzern und Anwendungen gewährten Berechtigungen beschränken und das Risiko böswilliger Aktivitäten reduzieren.
- Implementieren Sie Sicherheit auf Datenbankebene mithilfe von festen Datenbankrollen (wie z.B. db_datareader oder db_datawriter). Alternativ können sie benutzerdefinierte Rollen für Ihre Anwendung erstellen, um bestimmten Datenbankobjekten explizite Berechtigungen zu gewähren.

Es gibt noch weitere Möglichkeiten zum Absichern Ihrer Daten:

- [Verschlüsselung auf Zellenebene](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) werden bestimmte Spalten oder sogar Zellen, die Daten enthalten, mit unterschiedlichen Verschlüsselungsschlüsseln verschlüsselt.
- Mit [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) können Clients vertrauliche Daten in Clientanwendungen verschlüsseln, ohne die Verschlüsselungsschlüssel für die Datenbank-Engine (SQL-Datenbank oder SQL Server) jemals offenzulegen. Zu diesem Zweck ermöglicht Always Encrypted eine Trennung zwischen den Besitzern der Daten (die diese anzeigen dürfen) und den Personen, die die Daten verwalten (aber ansonsten keinen Zugriff haben).
- Bei der [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) können Kunden den Zugriff auf Zeilen in einer Datenbanktabelle basierend auf den Merkmalen des Benutzers steuern, der eine Abfrage ausführt. (Beispielmerkmale sind eine Gruppenmitgliedschaft und ein Ausführungskontext.)

Organisationen, die keine Verschlüsselung auf Datenbankebene verwenden, sind möglicherweise anfälliger für Angriffe, die in SQL-Datenbanken gespeicherte Daten kompromittieren.

Erfahren Sie mehr über die SQL-TDE-Verschlüsselung im Artikel [Azure SQL-Datenbank Transparent Data Encryption](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Aktivieren der Datenbanküberwachung
Die Überwachung einer Instanz der SQL Server-Datenbank-Engine oder einer einzelnen Datenbank umfasst das Nachverfolgen und Protokollieren von Ereignissen. Sie können für SQL Server Überwachungen erstellen, die Spezifikationen für Ereignisse auf Serverebene und auf Datenbankebene enthalten. Überwachte Ereignisse können in die Ereignisprotokolle oder Überwachungsdateien geschrieben werden.

Es gibt mehrere Ebenen der Überwachung für SQL Server. Dies richtet sich nach den behördlichen Anforderungen bzw. den Standardvorgaben für Ihre Installation. SQL Server-Überwachungen stellen Tools und Prozesse zu Verfügung, die Sie benötigen, um Überwachungen für verschiedene Server- und Datenbankobjekte zu aktivieren, zu speichern und anzuzeigen.

Die [Überwachung von Azure SQL-Datenbank](../sql-database/sql-database-auditing.md) verfolgt Datenbankereignisse nach und schreibt sie in ein Überwachungsprotokoll in Ihrem Azure-Speicherkonto.

Die Überwachung kann Sie dabei unterstützen, gesetzliche Bestimmungen einzuhalten, Datenbankaktivitäten nachzuvollziehen und Abweichungen und Anomalien zu bestimmen, die auf geschäftliche Probleme oder Sicherheitsverstöße hinweisen können. Die Überwachung unterstützt die Einhaltung von Standards, garantiert diese aber nicht.

Unter [Erste Schritte bei der Überwachung von SQL-Datenbank](../sql-database/sql-database-auditing.md) können Sie mehr zur Datenbanküberwachung und der Aktivierung erfahren.

## <a name="enable-database-threat-detection"></a>Aktivieren der Bedrohungserkennung für Datenbanken
Der Bedrohungsschutz ist mehr als nur das Erkennen von Bedrohungen. Der Bedrohungsschutz bei Datenbanken umfasst Folgendes:

- Ermitteln und Klassifizieren Ihrer sensiblen Daten, um diese schützen zu können.
- Implementieren sicherer Konfigurationen in Ihrer Datenbank, um diese schützen zu können.
- Ermitteln von und sofortiges Reagieren auf mögliche Bedrohungen, um deren Auswirkungen zu minimieren.

**Bewährte Methode**: Ermitteln, klassifizieren und kennzeichnen Sie sensible Daten in Ihrer Datenbank.   
**Detail**: Klassifizieren Sie die Daten in Ihrer SQL-Datenbank-Instanz, indem Sie die [Datenermittlung und -klassifizierung](../sql-database/sql-database-data-discovery-and-classification.md) in Azure SQL-Datenbank aktivieren. Sie können den Zugriff auf Ihre sensiblen Daten auf dem Azure-Dashboard überwachen oder Berichte herunterladen.

**Bewährte Methode**: Verfolgen Sie Datenbanksicherheitsrisiken, um Ihre Datenbanksicherheit proaktiv zu verbessern.   
**Detail**: Verwenden Sie den Azure SQL-Datenbank-Dienst [Sicherheitsrisikobewertung](../sql-database/sql-vulnerability-assessment.md), der Ihre Datenbank auf mögliche Sicherheitsrisiken prüft. Der Dienst verwendet eine Wissensdatenbank, die aus Regeln besteht, die Sicherheitsrisiken kennzeichnen und Abweichungen von bewährten Methoden hervorheben, z.B. Fehlkonfigurationen, übermäßige Berechtigungen und ungeschützte vertrauliche Daten.

Die Regeln basieren auf bewährten Methoden von Microsoft und konzentrieren sich auf die Sicherheitsprobleme, die das größte Risiko für Ihre Datenbank und deren wertvolle Daten darstellen. Sie betreffen sowohl Sicherheitsprobleme auf Datenbankebene als auch auf Serverebene wie Server-Firewalleinstellungen und Berechtigungen auf Serverebene. Diese Regeln stellen darüber hinaus viele der Anforderung von Regulierungsbehörden dar, um deren Konformitätsstandards zu erfüllen.

**Bewährte Methode**: Aktivieren Sie die Bedrohungserkennung.  
**Detail**:  Aktivieren Sie die [Bedrohungserkennung](../sql-database/sql-database-threat-detection.md) in Azure SQL-Datenbank, um Sicherheitswarnungen und -empfehlungen bezüglich der Untersuchung und Abwehr von Bedrohungen zu erhalten. Dann erhalten Sie Warnungen zu verdächtigen Datenbankaktivitäten, potenziellen Sicherheitsrisiken sowie Angriffen durch Einschleusung von SQL-Befehlen und ungewöhnlichen Datenbankzugriffs- und -abfragemustern.

[Advanced Threat Protection](../sql-database/sql-advanced-threat-protection.md) ist ein einheitliches Paket für erweiterte SQL-Sicherheitsfunktionen. Es enthält die zuvor erwähnten Dienste: Datenermittlung und -klassifizierung, Sicherheitsrisikobewertung und Bedrohungserkennung. Hier können Sie diese Funktionen an einer Stelle aktivieren und verwalten.

Wenn Sie diese Funktionen aktivieren, werden Sie bei Folgendem unterstützt:

- Einhalten von Datenschutzstandards und gesetzlichen Bestimmungen.
- Zugriffssteuerung für Ihre Datenbanken und Erhöhen der Datenbanksicherheit.
- Überwachen einer dynamischen Datenbankumgebung, bei der Änderungen schwer nachzuverfolgen sind.
- Erkennen von und Reagieren auf Bedrohungen.

Darüber hinaus integriert die Bedrohungserkennung Warnungen in das Azure Security Center, damit Sie an einer Stelle einen Überblick über den Sicherheitsstatus Ihrer Azure-Ressourcen erhalten.

## <a name="enable-feature-restrictions"></a>Aktivieren der Featureeinschränkungen

Die in Ihren Datenbanken enthaltenen Daten können Angreifern über Angriffsvektoren zugänglich gemacht werden, die Datenbankfehler und Abfrageausführungszeiten nutzen. Azure SQL-Datenbank bietet eine Reihe von Featureeinschränkungsmechanismen zum Schutz Ihrer Datenbank. Weitere Informationen finden Sie unter [Einschränkungen für SQL-Datenbankfeatures](../sql-database/sql-database-feature-restrictions.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere bewährte Methoden für die Sicherheit, die Sie beim Entwerfen, Bereitstellen und Verwalten Ihrer Cloudlösungen mithilfe von Azure verwenden können, finden Sie unter [Sicherheit in Azure: bewährte Methoden und Muster](security-best-practices-and-patterns.md).

Die folgenden Ressourcen enthalten allgemeinere Informationen zur Sicherheit in Azure und verwandten Microsoft-Diensten:
* [Blog des Azure-Sicherheitsteams](https://blogs.msdn.microsoft.com/azuresecurity/): Hier finden Sie Informationen über den aktuellen Stand der Azure-Sicherheit.
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): Hier können Sie Microsoft-Sicherheitsrisiken, z.B. Probleme mit Azure, melden oder eine E-Mail an secure@microsoft.com schreiben.
