---
title: Unterstützte Features von Azure SQL Edge
description: Hier erfahren Sie mehr über die Details der von Azure SQL Edge unterstützten Features.
keywords: Einführung in SQL Edge, was ist SQL Edge, Übersicht über SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392026"
---
# <a name="supported-features-of-azure-sql-edge"></a>Unterstützte Features von Azure SQL Edge 

Azure SQL Edge basiert auf der neuesten Version der SQL-Datenbank-Engine. Die Software unterstützt eine Teilmenge der in SQL Server 2019 unter Linux unterstützten Features sowie einige Features, die derzeit in SQL Server 2019 unter Linux (oder in SQL Server unter Windows) nicht unterstützt werden oder verfügbar sind.

Eine vollständige Liste der Funktionen, die in SQL Server für Linux unterstützt werden, finden Sie unter [Editionen und unterstützte Funktionen von SQL Server 2019 unter Linux](/sql/linux/sql-server-linux-editions-and-components-2019). Informationen zu den Editionen und unterstützten Features von SQL Server unter Windows finden Sie unter [Editionen und unterstützte Features von SQL Server 2019 (15.x)](/sql/sql-server/editions-and-components-of-sql-server-version-15).

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge-Editionen

SQL Database Edge wird in zwei unterschiedlichen Editionen oder Softwareplänen bereitgestellt. Diese Editionen weisen denselben Umfang an Features auf und unterscheiden sich lediglich in Bezug auf Nutzungsrechte und Menge an Arbeitsspeicher und Kernen, auf die sie auf dem Hostsystem zugreifen können.

   |**Planen**  |**Beschreibung**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  Nur für Entwicklungszwecke. Jeder Azure SQL Edge Developer-Container ist auf bis zu 4 Kerne und 32 GB Arbeitsspeicher beschränkt.  |
   |Azure SQL Edge    |  Für Produktionszwecke. Jeder Azure SQL Edge-Container ist auf bis zu 8 Kerne und 64 GB Arbeitsspeicher beschränkt.  |

## <a name="operating-system"></a>Betriebssystem

Azure SQL Edge-Container basieren auf Ubuntu 18.04 und werden daher nur für die Ausführung auf Docker-Hosts mit Ubuntu 18.04 LTS (empfohlene Version) oder Ubuntu 20.04 LTS unterstützt. Azure SQL Edge-Container können auf anderen Betriebssystemhosts ausgeführt werden, z. B. auf anderen Linux-Verteilungen oder unter Windows (mit Docker CE oder Docker EE). Dies wird von Windows jedoch nicht empfohlen, da diese Konfiguration nicht im vollen Umfang getestet wurde.

Die empfohlene Konfiguration für die Ausführung von Azure SQL Edge unter Windows besteht darin, ein Ubuntu-VM auf dem Windows-Host zu konfigurieren und dann Azure SQL Edge in der Linux-VM auszuführen.

Das für Azure SQL Edge empfohlene und unterstützte Dateisystem ist EXT4 und XFS. Wenn persistente Volumes verwendet werden, um den Azure SQL Edge-Datenbankspeicher zu sichern, muss das zugrunde liegende Hostdateisystem EXT4 und XFS sein.

## <a name="hardware-support"></a>Hardwareunterstützung

Azure SQL Edge erfordert einen 64-Bit-Prozessor (entweder x64 oder ARM64) mit mindestens einem Prozessor und 1 GB RAM auf dem Host. Während der Startspeicherbedarf von Azure SQL Edge ungefähr 450 MB beträgt, wird zusätzlicher Arbeitsspeicher für andere IoT Edge-Module oder -Prozesse benötigt, die auf dem Edge-Gerät ausgeführt werden. Die tatsächlichen Arbeitsspeicher- und CPU-Anforderungen für Azure SQL Edge variieren basierend auf der Komplexität der Workload und dem Datenvolume, das verarbeitet wird. Wenn Sie Hardware für Ihre Lösung auswählen, wird von Microsoft empfohlen, dass Sie umfangreiche Leistungstests durchführen, um sicherzustellen, dass die für Ihre Lösung erforderliche Leistung verfügbar ist.  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge-Komponenten

Azure SQL Edge unterstützt nur die Datenbank-Engine. Es bietet keine Unterstützung für andere Komponenten, die in SQL Server 2019 unter Windows oder Linux verfügbar sind. Insbesondere unterstützt Azure SQL Edge keine SQL Server-Komponenten wie Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (datenbankintern) und Machine Learning Server (eigenständig).

## <a name="supported-features"></a>Unterstützte Features

Zusätzlich zur Unterstützung einer Teilmenge der Features von SQL Server unter Linux bietet Azure SQL Edge Unterstützung für die folgenden neuen Features: 

- SQL-Streaming, das auf derselben Engine basiert, die Azure Stream Analytics unterstützt, ermöglicht Azure SQL Edge Datenstreamingfunktionen in Echtzeit. 
- Den T-SQL-Funktionsaufruf `Date_Bucket` für die Analyse von Zeitreihendaten.
- Funktionen für maschinelles Lernen über die ONNX-Runtime, die in der SQL Server-Engine enthalten ist.

## <a name="unsupported-features"></a>Nicht unterstützte Funktionen

In der folgenden Liste sind die Features von SQL Server 2019 unter Linux enthalten, die von Azure SQL Edge derzeit nicht unterstützt werden.

| Bereich | Nicht unterstütztes Feature oder Dienst |
|-----|-----|
| **Datenbankentwurf** | In-Memory-OLTP und zugehörige DDL-Befehle und Transact-SQL-Funktionen, Katalogsichten sowie dynamische Verwaltungssichten |
| &nbsp; | Datentyp `HierarchyID` und zugehörige DDL-Befehle und Transact-SQL-Funktionen, Katalogsichten sowie dynamische Verwaltungssichten |
| &nbsp; | Datentyp `Spatial` und zugehörige DDL-Befehle und Transact-SQL-Funktionen, Katalogsichten sowie dynamische Verwaltungssichten |
| &nbsp; | Stretch Database und zugehörige DDL-Befehle und Transact-SQL-Funktionen, Katalogsichten sowie dynamische Verwaltungssichten |
| &nbsp; | Volltextindizes und -suche und zugehörige DDL-Befehle und Transact-SQL-Funktionen, Katalogsichten sowie dynamische Verwaltungssichten|
| &nbsp; | `FileTable`, `FILESTREAM` und zugehörige DDL-Befehle und Transact-SQL-Funktionen, Katalogsichten sowie dynamische Verwaltungssichten|
| **Datenbank-Engine** | Replikation. Beachten Sie, dass Sie Azure SQL Edge als Pushabonnenten einer Replikationstopologie konfigurieren können. |
| &nbsp; | Polybase. Beachten Sie, dass Sie Azure SQL Edge als Ziel externer Tabellen in Polybase konfigurieren können. |
| &nbsp; | Spracherweiterbarkeit über Java und Spark |
| &nbsp; | Active Directory-Integration |
| &nbsp; | Automatisches Verkleinern der Datenbank. Die Eigenschaft „Auto shrink“ (Automatisch verkleinern) für eine Datenbank kann mit dem Befehl `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` festgelegt werden. Diese Änderung hat jedoch keine Auswirkungen. Der Task für automatische Verkleinerung wird für die Datenbank nicht ausgeführt. Benutzer können die Datenbankdateien aber mit den DBCC-Befehlen verkleinern. |
| &nbsp; | Datenbankmomentaufnahmen |
| &nbsp; | Unterstützung für persistenten Speicher |
| &nbsp; | Microsoft Distributed Transaction Coordinator |
| &nbsp; | Resource Governor und Ressourcenkontrolle für E/A-Vorgänge |
| &nbsp; | Pufferpoolerweiterung |
| &nbsp; | Verteilte Abfrage mit Drittanbieterverbindungen |
| &nbsp; | Verbindungsserver |
| &nbsp; | Erweiterte gespeicherte Systemprozeduren (wie z. B. `XP_CMDSHELL`) |
| &nbsp; | CLR-Assemblys und zugehörige DDL-Befehle und Transact-SQL-Funktionen, Katalogsichten sowie dynamische Verwaltungssichten |
| &nbsp; | CLR-abhängige T-SQL-Funktionen, z. B. `ASSEMBLYPROPERTY`, `FORMAT`, `PARSE` und `TRY_PARSE` |
| &nbsp; | CLR-abhängige Datums- und Uhrzeitkatalogsichten, Funktionen sowie Abfrageklauseln |
| &nbsp; | Pufferpoolerweiterung |
| &nbsp; | Datenbank-E-Mail |
| &nbsp; | Service Broker |
| &nbsp; | Richtlinienbasierte Verwaltung |
| &nbsp; | Verwaltungs-Data Warehouse |
| &nbsp; | Eigenständige Datenbanken |
| **SQL Server-Agent** |  Subsysteme: CmdExec, PowerShell, Queue Reader, SSIS, SSAS und SSRS |
| &nbsp; | Warnungen |
| &nbsp; | Verwaltete Sicherung |
| **Hochverfügbarkeit** | Always On-Verfügbarkeitsgruppen  |
| &nbsp; | Basis-Verfügbarkeitsgruppen |
| &nbsp; | Always On-Failoverclusterinstanz |
| &nbsp; | Datenbankspiegelung |
| &nbsp; | Hinzufügen von Speicher und CPU bei laufendem Systembetrieb |
| **Security** | Erweiterbare Schlüsselverwaltung |
| &nbsp; | Active Directory-Integration|
| &nbsp; | Unterstützung für Secure Enclaves|
| **Dienste** | SQL Server-Browser. |
| &nbsp; | Machine Learning über R und Python |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Data Quality Services |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **Verwaltbarkeit** | Steuerungspunkt für SQL Server-Hilfsprogramm |

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Azure SQL Edge](deploy-portal.md)
- [Konfigurieren von Azure SQL Edge](configure.md)
- [Herstellen einer Verbindung mit einer Instanz von Azure SQL Edge mithilfe von SQL Server-Clienttools](connect.md)
- [Sichern und Wiederherstellen von Datenbanken in Azure SQL Edge](backup-restore.md)