---
title: Schützen von Azure SQL Edge
description: Informationen zur Sicherheit in Azure SQL Edge
keywords: SQL Edge, Sicherheit
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021013"
---
# <a name="securing-azure-sql-edge"></a>Schützen von Azure SQL Edge

Mit der zunehmenden Verbreitung von IoT- und Edgecomputing in den verschiedenen Branchen steigen auch die Anzahl der Geräte und die Menge der Daten, die von diesen Geräten generiert werden. Die zunehmende Datenmenge und die Anzahl der Geräteendpunkte stellen eine erhebliche Herausforderung in Bezug auf die Sicherheit der Daten und Geräte dar. 

Azure SQL Edge bietet mehrere Features und Funktionen, mit denen Sie die IoT-Daten in den SQL Server Datenbanken relativ einfach schützen können. Azure SQL Edge basiert auf der SQL-Engine, die auch den Kern von Microsoft SQL Server und Azure SQL bildet. Dadurch werden dieselben Sicherheitsfunktionen genutzt, wodurch es einfacher wird, dieselben Sicherheitsrichtlinien und -praktiken von der Cloud auf die Edge auszudehnen.

Analog zu Microsoft SQL Server und Azure SQL kann das Schützen von Azure SQL Edge-Bereitstellungen als eine Reihe von Schritten betrachtet werden, die vier Bereiche betreffen: die Plattform, die Authentifizierung, die Objekte (einschließlich der Daten) und die Anwendungen, die auf das System zugreifen. 

## <a name="platform-and-system-security"></a>Plattform- und Systemsicherheit

Die Plattform für Azure SQL Edge umfasst den physischen Docker-Host, das Betriebssystem auf dem Host und die Netzwerksysteme, die das physische Gerät mit Anwendungen und Clients verbinden. 

Das Implementieren der Plattformsicherheit beginnt mit dem Fernhalten unbefugter Benutzer vom Netzwerk. Zu den bewährten Methoden gehören u. a. die folgenden:
- Implementieren von Firewallregeln, um die Sicherheitsrichtlinien der Organisation einzuhalten 
- Sicherstellen, dass für das Betriebssystem auf dem physischen Gerät alle aktuellen Sicherheitsupdates angewendet wurden 
- Angeben und Einschränken von Hostports, die für Azure SQL Edge verwendet werden
- Sicherstellen, dass die richtige Zugriffssteuerung auf alle Datenvolumes angewendet wird, auf denen Azure SQL Edge-Daten gehostet werden 

Weitere Informationen zu Azure SQL Edge-Netzwerkprotokollen und TDS-Endpunkten finden Sie unter [Netzwerkprotokolle und TDS-Endpunkte](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung 

### <a name="authentication"></a>Authentifizierung  
Die Authentifizierung ist der Prozess, bei dem bestätigt wird, dass der Benutzer derjenige ist, der er zu sein vorgibt. Azure SQL Edge unterstützt derzeit nur den `SQL Authentication`-Mechanismus.

- *SQL-Authentifizierung*:

    SQL-Authentifizierung bezieht sich auf die Authentifizierung eines Benutzers beim Herstellen einer Verbindung mit Azure SQL Edge mithilfe von Benutzername und Kennwort. Das SQL-Anmeldekennwort für **sa** muss während der SQL Edge-Bereitstellung angegeben werden. Danach können zusätzliche SQL-Anmeldungen und -Benutzer durch den Serveradministrator erstellt werden, sodass Benutzer eine Verbindung mithilfe von Benutzername und Kennwort herstellen können.

    Weitere Informationen zum Erstellen und Verwalten von Anmeldungen und Benutzern in SQL Edge finden Sie unter [Erstellen eines Anmeldenamens](/sql/relational-databases/security/authentication-access/create-a-login) und [Erstellen eines Datenbankbenutzers](/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Autorisierung   

Der Begriff Autorisierung bezieht sich auf die Berechtigungen, die einem Benutzer in einer Datenbank in Azure SQL Edge zugeordnet sind und die festlegen, welche Aktionen der Benutzer ausführen darf. Berechtigungen werden gesteuert, indem Benutzerkonten zu [Datenbankrollen](/sql/relational-databases/security/authentication-access/database-level-roles) hinzugefügt und diesen Rollen Berechtigungen auf Datenbankebene zugewiesen oder dem Benutzer bestimmte [Berechtigungen auf Objektebene](/sql/relational-databases/security/permissions-database-engine) erteilt werden. Weitere Informationen finden Sie unter [Anmeldungen und Benutzer](../azure-sql/database/logins-create-manage.md).

Es hat sich bewährt, benutzerdefinierte Rollen bei Bedarf zu erstellen. Fügen Sie Benutzer dann derjenigen Rolle hinzu, die die geringsten Berechtigungen besitzt, um die erforderliche Aufgabe noch erfüllen zu können. Weisen Sie Benutzern Berechtigungen nicht direkt zu. Das Serveradministratorkonto gehört zur integrierten Rolle „db_owner“, die über umfassende Berechtigungen verfügt und nur wenigen Benutzern mit administrativen Aufgaben erteilt werden sollte. Verwenden Sie bei Anwendungen die Option [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) zur Angabe des Ausführungskontexts für das aufgerufene Modul, oder verwenden Sie [Anwendungsrollen](/sql/relational-databases/security/authentication-access/application-roles) mit eingeschränkten Berechtigungen. Durch diese Vorgehensweise wird sichergestellt, dass eine Anwendung, die eine Verbindung mit der Datenbank herstellt, nur über die geringsten Berechtigungen verfügt, die von der Anwendung benötigt werden. Diese bewährten Methoden fördern auch die Aufgabentrennung.

## <a name="database-object-security"></a>Sicherheit von Datenbankobjekten

Prinzipale sind die Personen, Gruppen und Prozesse, denen Zugriff auf SQL Edge erteilt wurde. „Sicherungsfähige Elemente“ sind der Server, die Datenbank und Objekte in der Datenbank. Jedes der Elemente verfügt über einen Berechtigungssatz, der zum Reduzieren des Oberflächenbereichs konfiguriert werden kann. In der folgenden Tabelle sind Informationen zu Prinzipalen und sicherungsfähigen Elementen enthalten.

|Informationen über|Finden Sie unter|  
|---------------------------|---------|  
|Benutzer, Rollen und Prozesse von Servern und Datenbanken|[Prinzipale (Datenbank-Engine)](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Server- und Datenbankobjektsicherheit|[Sicherungsfähige Elemente](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Verschlüsselung und Zertifikate  
 
Durch Verschlüsselung werden keine Probleme der Zugriffssteuerung gelöst. Sie erhöht jedoch die Sicherheit, indem Datenverluste selbst im seltenen Fall einer überbrückten Zugriffssteuerung beschränkt werden. Wenn der Datenbankhostcomputer beispielsweise falsch konfiguriert wurde und ein böswilliger Benutzer Zugriff auf sensible Daten wie Kreditkartennummern gewinnt, sind die gestohlenen Informationen nutzlos, wenn sie verschlüsselt wurden. Die folgende Tabelle enthält weitere Informationen über Verschlüsselung in Azure SQL Edge.  
  
|Informationen über|Finden Sie unter|  
|---------------------------|---------|  
|Implementieren sicherer Verbindungen|[Encrypting Connections (Verschlüsseln von Verbindungen)](/sql/linux/sql-server-linux-encrypted-connections)|  
|Verschlüsselungsfunktionen|[Kryptografiefunktionen &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Datenverschlüsselung ruhender Daten|[Transparente Datenverschlüsselung (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> Die Sicherheitseinschränkungen, die für [SQL Server für Linux](/sql/linux/sql-server-linux-security-overview) beschrieben sind, gelten auch für Azure SQL Edge. 


> [!NOTE]
> Azure SQL Edge umfasst nicht das mssql-conf-Hilfsprogramm. Alle Konfigurationen, einschließlich der verschlüsselungsbezogenen Konfiguration, müssen über die [Datei „mssql.conf“](configure.md#configure-by-using-an-mssqlconf-file) oder über [Umgebungsvariablen](configure.md#configure-by-using-environment-variables) vorgenommen werden. 


Analog zu Azure SQL und Microsoft SQL Server bietet Azure SQL Edge denselben Mechanismus zum Erstellen und Verwenden von Zertifikaten, um die Objekt- und Verbindungssicherheit zu verbessern. Weitere Informationen finden Sie unter [CREATE CERTIFICATE (TRANSACT-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Anwendungssicherheit

### <a name="client-programs"></a>Client-Programme

Zu den bewährten Methoden für Azure SQL Edge-Sicherheit gehört das Schreiben sicherer Clientanwendungen. Weitere Informationen zum Sichern von Clientanwendungen auf Netzwerkebene finden Sie unter [Client Network Configuration](/sql/database-engine/configure-windows/client-network-configuration).

### <a name="security-catalog-views-and-functions"></a>Sicherheitskatalogsichten und -funktionen  
Sicherheitsinformationen werden in zahlreichen Sichten und Funktionen verfügbar gemacht, die hinsichtlich Leistung und Dienstprogramm optimiert sind. Die folgende Tabelle enthält Informationen zu Sicherheitssichten und -funktionen in Azure SQL Edge.  
  
|Funktionen und Sichten|Links|  
|---------------------------|---------|  
|Sicherheitskatalogsichten, durch die Informationen zu Berechtigungen, Rollen, Prinzipalen usw. auf Datenbankebene und Serverebene zurückgegeben werden. Außerdem sind Katalogsichten mit Informationen zu Verschlüsselungsschlüsseln, Zertifikaten und Anmeldeinformationen vorhanden.|[Sicherheitskatalogsichten &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Sicherheitsfunktionen, durch die Informationen zum aktuellen Benutzer, zu Berechtigungen und zu Schemas zurückgegeben werden.|[Sicherheitsfunktionen &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Dynamische Verwaltungssichten für die Sicherheit.|[Sicherheitsbezogene dynamische Verwaltungssichten und -funktionen &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Überwachung 

Azure SQL Edge bietet dieselben Überwachungsmechanismen wie SQL Server. Weitere Informationen finden Sie unter [SQL Server Audit (Datenbank-Engine)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Nächste Schritte

- [Exemplarische Vorgehensweise für die Sicherheitsfeatures von SQL Server für Linux](/sql/linux/sql-server-linux-security-get-started)
- [Ausführen von Azure SQL Edge als Nicht-root-Benutzer](configure.md#run-azure-sql-edge-as-non-root-user)
- [Azure Security Center für IoT](../defender-for-iot/overview.md)