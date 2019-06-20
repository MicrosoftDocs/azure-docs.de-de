---
title: Schnellere Datenbankwiederherstellung – Azure SQL-Datenbank | Microsoft-Dokumentation
description: Die Azure SQL-Datenbank verfügt über ein neues Feature, das eine schnelle und einheitliche Datenbankwiederherstellung, den sofortigen Transaktionsrollback und eine aggressive Protokollkürzung für Einzel- und Pooldatenbanken in Azure SQL-Datenbank sowie Datenbanken in Azure SQL Data Warehouse ermöglicht.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77bc33747964a5f4ee1a67aba777dc3ed76b9a51
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073465"
---
# <a name="accelerated-database-recovery"></a>Schnellere Datenbankwiederherstellung

Die **schnellere Datenbankwiederherstellung (Accelerated Database Recovery, ADR)** ist eine neue Funktion der SQL-Datenbank-Engine. Mit dieser Funktion wird die Datenbankverfügbarkeit aufgrund eines neuen Entwurfs des Wiederherstellungsprozesses der SQL-Datenbank-Engine erheblich verbessert, insbesondere bei Transaktionen mit langer Ausführungsdauer. ADR steht derzeit für Einzel- und Pooldatenbanken in Azure SQL-Datenbank sowie Datenbanken in Azure SQL Data Warehouse zur Verfügung. Die wichtigsten Vorteile von ADR sind:

- **Schnelle und einheitliche Datenbankwiederherstellung**

  Bei ADR wirken sich Transaktionen mit langer Ausführungsdauer nicht auf die Gesamtzeit der Wiederherstellung aus und ermöglichen so eine schnelle und einheitliche Datenbankwiederherstellung – unabhängig von der Anzahl und Größe aktiver Transaktionen im System.

- **Sofortiger Transaktionsrollback**

  Bei ADR erfolgt der Transaktionsrollback sofort, und zwar unabhängig davon, wie lange die Transaktion aktiv war oder wie viele Updates durchgeführt wurden.

- **Aggressive Protokollkürzung**

  Bei ADR wird das Transaktionsprotokoll aggressiv gekürzt. Dies gilt auch für das Vorhandensein von aktiven Transaktionen mit langer Ausführungsdauer, um zu verhindern, dass der Vorgang nicht mehr kontrollierbar ist.

## <a name="the-current-database-recovery-process"></a>Aktueller Prozess für die Datenbankwiederherstellung

Die Datenbankwiederherstellung in SQL Server basiert auf dem [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf)-Wiederherstellungsmodell und umfasst drei Phasen. Diese Phasen sind im folgenden Diagramm dargestellt und werden im darauffolgenden Diagramm ausführlicher erläutert.

![Aktueller Wiederherstellungsprozess](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analysephase**

  Vorwärts-Scan des Transaktionsprotokolls ab dem Beginn des letzten erfolgreichen Prüfpunkts (oder der ältesten modifizierten Seiten-LSN) bis zum Ende, um den Zustand jeder Transaktion zum Beendigungszeitpunkt von SQL Server zu ermitteln.

- **Wiederholungsphase**

  Vorwärts-Scan des Transaktionsprotokolls von der ältesten Transaktion ohne Commit bis zum Ende, um die Datenbank in den Zustand zum Zeitpunkt des Absturzes zu versetzen, indem alle Commit-Vorgänge erneut durchgeführt werden.

- **Phase des Rückgängigmachens**

  Für jede Transaktion, die zum Zeitpunkt des Absturzes aktiv war, wird das Protokoll in umgekehrter Richtung durchlaufen, um die von dieser Transaktion durchgeführten Vorgänge rückgängig zu machen.

Basierend auf diesem Entwurf ist die Zeit, die das SQL-Datenbankmodul für die Wiederherstellung nach einem unerwarteten Neustart benötigt, (ungefähr) proportional zur Größe der Transaktion, die zum Zeitpunkt des Absturzes im System am längsten aktiv war. Für die Wiederherstellung ist ein Rollback aller nicht abgeschlossenen Transaktionen erforderlich. Die benötigte Zeit ist proportional zu der Arbeit, die von der Transaktion durchgeführt wurde, und zum Zeitraum ihrer Aktivität. Daher kann der SQL Server-Wiederherstellungsprozess bei Vorhandensein von Transaktionen mit langer Ausführungsdauer (z.B. Vorgänge mit umfangreichen Masseneinfügungen oder Vorgänge für die Indexerstellung einer großen Tabelle) sehr viel Zeit in Anspruch nehmen.

Auch das Abbrechen bzw. Durchführen eines Rollbacks für eine Transaktion kann bei diesem Entwurf sehr lange dauern, da die gleiche Rückgängig/Wiederherstellen-Phase wie oben beschrieben genutzt wird.

Darüber hinaus kann das SQL-Datenbankmodul das Transaktionsprotokoll nicht kürzen, wenn Transaktionen mit langer Ausführungsdauer vorhanden sind, da die entsprechenden Protokolldatensätze für die Wiederherstellungs- und Rollbackprozesse benötigt werden. Aufgrund dieses Entwurfs des SQL-Datenbankmoduls haben einige Kunden das Problem, dass das Transaktionsprotokoll sehr groß wird und sehr viel Festplattenspeicher belegt.

## <a name="the-accelerated-database-recovery-process"></a>Prozess für die schnellere Datenbankwiederherstellung

Mit ADR wird auf die obigen Probleme reagiert, indem der Wiederherstellungsprozess des SQL-Datenbankmoduls vollständig neu entworfen wird, um Folgendes zu erreichen:

- Erzielen einer konstanten bzw. sehr kurzen Dauer, weil es vermieden wird, dass das Protokoll bis zum Anfang bzw. ab der ältesten aktiven Transaktion gescannt werden muss. Bei ADR wird das Transaktionsprotokoll nur ab dem letzten erfolgreichen Prüfpunkt verarbeitet (bzw. ab der Protokollfolgenummer (Log Sequence Number, LSN) der ältesten modifizierten Seite). Aus diesem Grund wird die Wiederherstellungszeit von Transaktionen mit langer Ausführungsdauer nicht beeinträchtigt.
- Verringern des erforderlichen Speichers für Transaktionsprotokolle, da das Protokoll nicht mehr für die gesamte Transaktion verarbeitet werden muss. Daher kann das Transaktionsprotokoll aggressiv gekürzt werden, wenn Prüfpunkte gesetzt und Sicherungen durchgeführt werden.

In allgemeiner Hinsicht wird die schnelle Datenbankwiederherstellung mit ADR erreicht, indem alle physischen Datenbankänderungen mit Versionsangaben versehen werden und nur logische Vorgänge rückgängig gemacht werden, die begrenzt sind und für die das nahezu sofortige Rückgängigmachen möglich ist. Alle Transaktionen, die zum Zeitpunkt des Absturzes aktiv waren, werden als „Abgebrochen“ gekennzeichnet. Daher können Versionen, die von diesen Transaktionen generiert werden, von gleichzeitigen Benutzerabfragen ignoriert werden.

Der ADR-Wiederherstellungsprozess verfügt über die gleichen drei Phasen wie der aktuelle Wiederherstellungsprozess. Wie diese Phasen für ADR durchgeführt werden, ist im folgenden Diagramm dargestellt und wird im darauffolgenden Diagramm ausführlicher erläutert.

![ADR-Wiederherstellungsprozess](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analysephase**

  Der Prozess bleibt quasi unverändert, aber es werden die sLog-Wiederherstellung und das Kopieren von Protokolldatensätzen für Vorgänge ohne Versionsangabe hinzugefügt.
  
- **Wiederholungsphase**

  Aufgeteilt in zwei Phasen (P)
  - Phase 1

      Wiederholung über sLog (älteste Transaktion ohne Commit bis zum letzten Prüfpunkt). Die Wiederholung ist ein schneller Vorgang, weil nur einige Datensätze aus dem sLog verarbeitet werden müssen.
      
  - Phase 2

     Die Wiederholung über das Transaktionsprotokoll beginnt ab dem letzten Prüfpunkt (anstatt ab der ältesten Transaktion ohne Commit).
     
- **Phase des Rückgängigmachens**

   Die Phase „Rückgängig“ mit ADR wird nahezu sofort durchgeführt, indem sLog genutzt wird, um Vorgänge ohne Versionsangabe und persistenten Versionsspeicher mit logischer Wiederherstellung rückgängig zu machen. Auf diese Weise erfolgt das versionsbasierte Rückgängigmachen auf Zeilenebene.

## <a name="adr-recovery-components"></a>ADR-Wiederherstellungskomponenten

Die vier wichtigsten Komponenten von ADR sind:

- **Persistenter Versionsspeicher**

  Der persistente Versionsspeicher ist ein neuer Mechanismus der SQL-Datenbank-Engine, mit dem die in der Datenbank generierten Zeilenversionen persistent gemacht werden, anstatt im herkömmlichen `tempdb`-Versionsspeicher. Mit dem persistenten Versionsspeicher wird die Ressourcenisolation ermöglicht und die Verfügbarkeit von lesbaren sekundären Replikaten verbessert.

- **Logische Wiederherstellung**

  Die logische Wiederherstellung ist der asynchrone Prozess zur Durchführung des Rückgängigmachens basierend auf der Zeilenebenenversion – mit sofortigem Transaktionsrollback und Rückgängig-Vorgang für alle Vorgänge mit Versionsangabe.

  - Nachverfolgung aller abgebrochenen Transaktionen
  - Durchführung eines Rollbacks mit persistentem Versionsspeicher für alle Benutzertransaktionen
  - Aufhebung aller Sperren sofort nach dem Abbruch der Transaktion

- **sLog**

  Bei sLog handelt es sich um einen sekundären In-Memory-Protokolldatenstrom, der Protokolldatensätze für Vorgänge ohne Versionsangabe speichert (z.B. Metadaten-Cacheinvalidierung, Abruf einer Sperre usw.). Für sLog gilt Folgendes:

  - Geringer Umfang und „in-memory“
  - Wird dauerhaft auf dem Datenträger gespeichert, indem während des Prüfpunktprozesses eine Serialisierung durchgeführt wird
  - Wird bei Transaktionscommits regelmäßig gekürzt
  - Erhöht die Geschwindigkeit von Wiederholen- und Rückgängig-Vorgängen, indem nur die Vorgänge ohne Versionsangabe verarbeitet werden  
  - Ermöglicht eine aggressive Kürzung des Transaktionsprotokolls, indem nur die erforderlichen Protokolldatensätze beibehalten werden

- **Bereinigung**

  Die Bereinigung (Cleaner) ist der asynchrone Prozess, der regelmäßig aufgerufen wird, um nicht mehr benötigte Seitenversionen zu bereinigen.

## <a name="who-should-consider-accelerated-database-recovery"></a>Für wen ist die schnellere Datenbankwiederherstellung geeignet?

Kunden, für die Folgendes gilt, sollten die Nutzung der schnelleren Datenbankwiederherstellung erwägen:

- Kunden, deren Workloads über Transaktionen mit langer Ausführungsdauer verfügen
- Kunden mit Fällen, in denen aktive Transaktionen bewirken, dass das Transaktionsprotokoll erheblich an Größe zunimmt  
- Kunden mit langen Zeiträumen, in denen die Datenbank aufgrund einer langwierigen SQL Server-Wiederherstellung (z.B. bei einem unerwarteten SQL Server-Neustart oder einem manuellen Transaktionsrollback) nicht verfügbar ist

