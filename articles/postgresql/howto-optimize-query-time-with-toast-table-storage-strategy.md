---
title: Optimieren der Abfragezeit mithilfe der Strategie für TOAST-Tabellenspeicher in Azure Database for PostgreSQL (Einzelserver)
description: In diesem Artikel wird beschrieben, wie Sie die Abfragezeit mithilfe der Strategie für TOAST-Tabellenspeicher auf einem Azure Database for PostgreSQL-Einzelserver optimieren.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65066964"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optimieren der Abfragezeit mithilfe der Strategie für TOAST-Tabellenspeicher 
In diesem Artikel wird beschrieben, wie Sie die Abfragezeit mithilfe der Strategie für TOAST-Tabellenspeicher optimieren.

## <a name="toast-table-storage-strategies"></a>Strategien für TOAST-Tabellenspeicher
Es werden vier verschiedene Strategien verwendet, um Spalten auf dem Datenträger zu speichern, die TOAST verwenden können. Sie stellen verschiedene Kombinationen zwischen Komprimierung und Out-of-Line-Speicherung dar. Die Strategie kann auf der Ebene des Datentyps und auf der Spaltenebene festgelegt werden.
- **Nur-Text** verhindert entweder Komprimierung oder Out-of-Line-Speicherung. Diese Option deaktiviert die Verwendung von Einzelbyte-Headern für Varlena-Typen. „Nur-Text“ ist die einzige mögliche Strategie für Spalten von Datentypen, die TOAST nicht verwenden können.
- **Erweitert** ermöglicht sowohl die Komprimierung als auch die Out-of-Line-Speicherung. „Erweitert“ ist die Standardeinstellung für die meisten Datentypen, die TOAST verwenden können. Zuerst wird eine Komprimierung versucht. Die Out-of-Line Speicherung wird versucht, wenn die Zeile weiterhin zu groß ist.
- **Extern** ermöglicht die Out-of-Line-Speicherung, aber keine Komprimierung. Die Verwendung von „Extern“ beschleunigt Operationen für untergeordnete Zeichenfolgen mit breitem Text und bytea-Spalten. Diese erhöhte Geschwindigkeit hat jedoch höhere Speicherplatzanforderungen zur Folge. Diese Operationen sind so optimiert, dass nur die erforderlichen Teile des Out-of-Line-Wertes abgerufen werden, wenn er nicht komprimiert ist.
- **Haupt** ermöglicht die Komprimierung, aber keine Out-of-Line-Speicherung. Die Out-of-Line-Speicherung wird für derartige Spalten weiterhin durchgeführt, jedoch nur als letzte Option. Sie tritt auf, wenn es keine andere Möglichkeit gibt, die Zeile so klein zu gestalten, dass sie auf eine Seite passt.

## <a name="use-toast-table-storage-strategies"></a>Verwenden von Strategien für TOAST-Tabellenspeicher
Wenn Ihre Abfragen auf Datentypen zugreifen, die TOAST verwenden können, sollten Sie anstelle der Option „Erweitert“ ggf. die Strategie „Haupt“ verwenden, um die Abfragezeiten zu reduzieren. „Haupt“ schließt eine Out-of-Line-Speicherung nicht aus. Wenn Ihre Abfragen nicht auf Datentypen zugreifen, die TOAST verwenden können, kann es sinnvoll sein, die Option „Erweitert“ beizubehalten. Dies verbessert die Leistung, da ein größerer Teil der Zeilen der Haupttabelle in den freigegebenen Puffercache passt.

Wenn Sie über eine Workload verfügen, die ein Schema mit breiten Tabellen und hoher Zeichenanzahl verwendet, sollten Sie die Verwendung von PostgreSQL-TOAST-Tabellen erwägen. Eine Beispielkundentabelle wies mehr als 350 Spalten auf, die zum Teil 255 Zeichen enthielten. Nachdem sie in die TOAST-Tabellenstrategie „Haupt“ konvertiert wurde, verringerte sich die Benchmarkabfragezeit von 4203 Sekunden auf 467 Sekunden. Das ist eine Verbesserung um 89 Prozent.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie, ob Ihre Workload die vorherigen Eigenschaften aufweist. 

Sehen Sie sich die folgende PostgreSQL-Dokumentation an: 
- [Chapter 68, Database physical storage](https://www.postgresql.org/docs/current/storage-toast.html) (Kapitel 68: Physischer Datenbankspeicher) 