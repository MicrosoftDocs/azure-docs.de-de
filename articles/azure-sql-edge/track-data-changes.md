---
title: Nachverfolgen von Datenänderungen in Azure SQL Edge
description: Erfahren Sie mehr zu Änderungsnachverfolgung und Change Data Capture in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 86e84c60aec99246f58b5dc9d67584b23a3969f3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394923"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Nachverfolgen von Datenänderungen in Azure SQL Edge

Azure SQL Edge unterstützt die beiden SQL Server-Features, mit denen Änderungen an Daten in einer Datenbank nachverfolgt werden: [Änderungsnachverfolgung](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) und [Change Data Capture](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Mit diesen Funktionen können Anwendungen die DML-Änderungen (Einfüge-, Aktualisierungs- und Löschvorgänge) ermitteln, die an Benutzertabellen in einer Datenbank vorgenommen wurden. Change Data Capture und die Änderungsnachverfolgung können für dieselbe Datenbank aktiviert werden. Dabei sind keine besonderen Aspekte zu berücksichtigen.

Damit bestimmte Anwendungen effizient ausgeführt werden können, muss eine wichtige Anforderung erfüllt sein: Die Anwendungen müssen in der Lage sein, Daten abzufragen, die in einer Datenbank geändert wurden. Zum Ermitteln von Datenänderungen mussten Anwendungsentwickler normalerweise eine benutzerdefinierte Nachverfolgungsmethode in ihren Anwendungen implementieren, wobei sie eine Kombination von Triggern, Zeitstempelspalten und zusätzlichen Tabellen verwendeten. Die Erstellung solcher Anwendungen ist normalerweise sehr arbeitsintensiv, führt zu Schemaupdates und ist häufig mit hohem Verwaltungsaufwand verbunden.

Bei einer IoT-Lösung, bei der Daten regelmäßig aus der Edgeumgebung in eine Cloud oder ein Rechenzentrum verschoben werden müssen, kann Änderungsnachverfolgung sehr nützlich sein. Benutzer können schnell und effektiv nur die Änderungen der letzten Synchronisierung abfragen und diese Änderungen in das Ziel in der Cloud oder im Rechenzentrum hochladen. Weitere Informationen finden Sie unter [Vorteile der Verwendung von Change Data Capture oder der Änderungsnachverfolgung](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Diese beiden Features sind nicht identisch. Weitere Informationen finden Sie unter [Funktionsunterschiede zwischen Change Data Capture und Änderungsnachverfolgung](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking).

## <a name="change-data-capture"></a>Change Data Capture

Einzelheiten zur Funktionsweise dieses Features finden Sie unter [Informationen zu Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Informationen zum Aktivieren oder Deaktivieren dieses Features finden Sie unter [Aktivieren und Deaktivieren von Change Data Capture](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Informationen zum Verwalten und Überwachen dieses Features finden Sie unter [Verwalten und Überwachen von Change Data Capture](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Informationen dazu, wie Sie die geänderten Daten abfragen und mit ihnen arbeiten, finden Sie unter [Arbeiten mit Änderungsdaten](/sql/relational-databases/track-changes/work-with-change-data-sql-server).

## <a name="change-tracking"></a>Änderungsnachverfolgung

Einzelheiten zur Funktionsweise dieses Features finden Sie unter [Informationen zur Änderungsnachverfolgung](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Informationen zum Aktivieren oder Deaktivieren dieses Features finden Sie unter [Aktivieren und Deaktivieren von Änderungsnachverfolgung](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Informationen zum Verwalten und Überwachen dieses Features finden Sie unter [Verwalten und Überwachen der Änderungsnachverfolgung](/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

Informationen dazu, wie Sie die geänderten Daten abfragen und mit ihnen arbeiten, finden Sie unter [Arbeiten mit Änderungsdaten](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server).

## <a name="temporal-tables"></a>Temporale Tabellen

Azure SQL Edge unterstützt auch das SQL Server-Feature „Temporale Tabellen“. Dieses Feature (auch als *temporale Tabellen mit Versionsverwaltung durch das System* bezeichnet) bietet integrierte Unterstützung für das Bereitstellen von Informationen über die zu jedem Zeitpunkt in der Tabelle gespeicherten Daten. Das Feature liefert nicht einfach nur Informationen über nur die Daten, die zum aktuellen Zeitpunkt stimmen.

Eine temporale Tabelle mit Versionsverwaltung durch das System ist ein Benutzertabellentyp, der darauf ausgelegt ist, den Verlauf aller Datenänderungen lückenlos zu speichern und einfache zeitpunktbezogene Analysen zu ermöglichen. Bei diesem Typ temporaler Tabelle ist die Rede von einer Versionsverwaltung durch das System, da die Gültigkeitsdauer für jede Zeile vom System (d. h. von der Datenbank-Engine) verwaltet wird.

Jede temporale Tabelle weist zwei explizit definierte Spalten auf, die beide den Datentyp `datetime2` haben. Diese Spalten werden als *Zeitraumspalten* bezeichnet. Das System nutzt diese Zeitraumspalten ausschließlich, um bei jeder Zeilenänderung den Gültigkeitszeitraum für jede Zeile aufzuzeichnen.

Über diese Zeitraumspalten hinaus enthält eine temporale Tabelle außerdem einen Verweis auf eine weitere Tabelle mit einem gespiegelten Schema. Das System verwendet diese Tabelle, um bei jeder Aktualisierung oder Löschung einer Zeile in der temporalen Tabelle automatisch die Vorversion der Zeile zu speichern. Diese zusätzliche Tabelle wird als die *Verlaufstabelle* bezeichnet, während die Haupttabelle, die die aktuellen (gültigen) Zeilenversionen speichert, als die *aktuelle Tabelle* oder einfach als die temporale Tabelle bezeichnet wird. Bei der Erstellung temporaler Tabellen können Benutzer eine vorhandene Verlaufstabelle (deren Schema kompatibel sein muss) angeben oder vom System eine standardmäßige Verlaufstabelle erstellen lassen.

Weitere Informationen finden Sie unter [Temporale Tabellen](/sql/relational-databases/tables/temporal-tables).

## <a name="next-steps"></a>Nächste Schritte

- [Datenstreaming in Azure SQL Edge](stream-data.md)
- [Maschinelles Lernen und KI mit ONNX in Azure SQL Edge](onnx-overview.md)
- [Konfigurieren der Replikation in Azure SQL Edge](configure-replication.md)
- [Sichern und Wiederherstellen von Datenbanken in Azure SQL Edge](backup-restore.md)