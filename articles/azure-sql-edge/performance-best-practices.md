---
title: 'Leistungsbezogene bewährte Methoden und Konfigurationsrichtlinien: Azure SQL Edge'
description: Erfahren Sie mehr über leistungsbezogene bewährte Methoden und Konfigurationsrichtlinien in Azure SQL Edge.
keywords: SQL Edge, Datenaufbewahrung
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392009"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Leistungsbezogene bewährte Methoden und Konfigurationsrichtlinien

Azure SQL Edge bietet mehrere Features und Funktionen, mit denen Sie die Leistung Ihrer SQL Edge-Bereitstellung verbessern können. Dieser Artikel enthält einige bewährte Methoden sowie Empfehlungen, um die Leistung zu maximieren. 

## <a name="best-practices"></a>Bewährte Methoden 

### <a name="configure-multiple-tempdb-data-files"></a>Konfigurieren mehrerer tempdb-Datendateien

Azure SQL Edge erstellt standardmäßig nur eine tempdb-Datendatei im Rahmen der Containerinitialisierung. Es empfiehlt sich, nach der Bereitstellung mehrere tempdb-Datendateien zu erstellen. Weitere Informationen finden Sie im Artikel [Empfehlungen zum Reduzieren von Konflikten bei der Zuweisung in tempdb-Datenbank für SQL Server](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Verwenden von gruppierten Columnstore-Indizes, wann immer dies möglich ist

IoT- und Edge-Geräte generieren tendenziell große Datenmengen, die in der Regel in einem Zeitfenster zur Analyse aggregiert werden. Einzelne Datenzeilen werden selten für irgendwelche Analysen verwendet. Columnstore-Indizes eignen sich ideal zum Speichern und Abfragen solcher großen Datasets. Dieser Index verwendet spaltenbasierte Datenspeicherung und Abfrageverarbeitung, um eine bis zu zehnmal höhere Abfrageleistung im Vergleich zur herkömmlichen zeilenorientierten Speicherung zu erzielen. Sie können im Vergleich zur unkomprimierten Datengröße außerdem eine bis zu 10-mal höhere Datenkomprimierung erzielen. Weitere Informationen finden Sie unter [Columnstore-Indizes: Übersicht](/sql/relational-databases/indexes/columnstore-indexes-overview).

Darüber hinaus profitieren andere Azure SQL Edge-Features wie Datenstreaming und Datenaufbewahrung von den Columnstore-Optimierungen im Zusammenhang mit Dateneinfügung und Datenentfernung. 

### <a name="simple-recovery-model"></a>Einfaches Wiederherstellungsmodell

Da der Speicher auf Edge-Geräten eingeschränkt sein kann, wird für alle Benutzerdatenbanken in Azure SQL Edge standardmäßig das einfache Wiederherstellungsmodell verwendet. Beim einfachen Wiederherstellungsmodell wird Protokollspeicherplatz automatisch freigegeben, um die Speicherplatzanforderungen gering zu halten. Hierdurch entfällt im Wesentlichen die Notwendigkeit, den Speicherplatz für Transaktionsprotokolle zu verwalten. Auf Edge-Geräten mit eingeschränktem Speicherplatz kann dies hilfreich sein. Weitere Informationen zum einfachen Wiederherstellungsmodell und zu weiteren verfügbaren Wiederherstellungsmodellen finden Sie unter [Wiederherstellungsmodelle](/sql/relational-databases/backup-restore/recovery-models-sql-server).

Vorgänge wie Protokollversand und Point-in-Time-Wiederherstellungen, die Transaktionsprotokollsicherungen erfordern, werden vom einfachen Wiederherstellungsmodell nicht unterstützt.  

## <a name="advanced-configuration"></a>Erweiterte Konfiguration 

### <a name="setting-memory-limits"></a>Festlegen von Arbeitsspeichereinschränkungen

Azure SQL Edge unterstützt bis zu 64 GB Arbeitsspeicher für den Pufferpool, während Satellitenprozesse, die im SQL Edge-Container ausgeführt werden, möglicherweise zusätzlichen Arbeitsspeicher benötigen. Für kleinere Edge-Geräten mit eingeschränktem Arbeitsspeicher empfiehlt es sich, den Arbeitsspeicher, der für SQL Edge-Container verfügbar ist, einzuschränken, sodass der Docker-Host und andere Edge-Prozesse oder -Module ordnungsgemäß funktionieren können. Die für SQL Edge verfügbare Arbeitsspeichermenge kann mit den folgenden Mechanismen gesteuert werden. 

- Einschränken des für die SQL Edge-Container verfügbaren Arbeitsspeichers: Die Arbeitsspeichermenge, die für einen SQL Edge-Container verfügbar ist, kann über die containerbezogene Laufzeitkonfigurationsoption `--memory` eingeschränkt werden. Weitere Informationen zum Einschränken des verfügbaren Arbeitsspeichers für einen SQL Edge-Container finden Sie unter [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/).

- Einschränken des Arbeitsspeichers, der für einen SQL-Prozess verfügbar ist, im Container: Standardmäßig werden für einen SQL-Prozess im Container nur 80 % des verfügbaren physischen RAMs verwendet. Für die Mehrzahl der Bereitstellungen ist die Standardkonfiguration völlig ausreichend. Es kann aber Szenarien geben, in denen zusätzlicher Arbeitsspeicher für das Datenstreaming und die ONNX-Prozesse erforderlich ist, die in den SQL Edge-Containern ausgeführt werden. In solchen Szenarien kann der für den SQL-Prozess verfügbare Arbeitsspeicher über die `memory.memorylimitmb`-Einstellung in der mssql-conf-Datei gesteuert werden. Weitere Informationen finden Sie unter [Konfigurieren mithilfe der Datei „mssql.conf“](configure.md#configure-by-using-an-mssqlconf-file).

Wenn Sie einen Arbeitsspeichergrenzwert festlegen, sollten Sie darauf achten, dass dieser Wert nicht zu niedrig ist. Wenn Sie nicht genügend Arbeitsspeicher für den SQL-Prozess festgelegt haben, kann die SQL-Leistung erheblich eingeschränkt sein.

### <a name="delayed-durability"></a>Verzögerte Dauerhaftigkeit

Transaktionen in Azure SQL Edge können entweder vollständig dauerhaft sein, was in SQL Server der Standardeinstellung entspricht, oder sie können verzögert dauerhaft sein (auch bekannt als verzögerter Commit).

Vollständig dauerhafte Transaktionscommits sind synchron, melden, dass ein COMMIT erfolgreich ausgeführt wurde, und geben die Steuerung erst an den Client zurück, nachdem die Protokolldatensätze für die Transaktion auf den Datenträger geschrieben wurden. Verzögert dauerhafte Transaktionscommits sind asynchron und melden, dass ein COMMIT erfolgreich ausgeführt wurde, bevor die Protokolldatensätze für die Transaktion auf den Datenträger geschrieben wurden. Damit eine Transaktion dauerhaft ist, müssen die Transaktionsprotokolleinträge auf dem Datenträger festgeschrieben werden. Verzögert dauerhafte Transaktionen werden dauerhaft, nachdem die Transaktionsprotokolleinträge auf den Datenträger geleert wurden. 

In Bereitstellungen, in denen **der Verlust einiger Daten** toleriert werden kann, oder auf Edgegeräten mit langsamem Speicher, kann verzögerte Dauerhaftigkeit verwendet werden, um Datenerfassung und datenaufbewahrungsbasierte Bereinigung zu optimieren. Weitere Informationen finden Sie im Thema [Steuern der Transaktionsdauerhaftigkeit](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Linux-Konfigurationen 

Verwenden Sie die folgenden [Konfigurationseinstellungen für das Linux-Betriebssystem](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration), um die beste Leistung für eine SQL-Installation zu erzielen.