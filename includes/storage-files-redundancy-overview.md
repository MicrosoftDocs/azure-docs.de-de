---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011719"
---
Um die Daten in Ihren Azure-Dateifreigaben vor Datenverlusten oder Beschädigungen zu schützen, werden für alle Azure-Dateifreigaben beim Schreiben mehrere Kopien der einzelnen Dateien gespeichert. Abhängig von den Anforderungen Ihrer Workload können Sie zusätzliche Redundanzgrade auswählen. Azure Files unterstützt derzeit die folgenden Datenredundanzoptionen:

- **Lokal redundant:** Lokal redundanter Speicher, häufig als LRS bezeichnet, bedeutet, dass jede Datei in einem Azure Storage-Cluster dreimal gespeichert wird. Dies schützt vor Datenverlusten aufgrund von Hardwarefehlern, z. B. bei einem schadhaften Laufwerk.
- **Zonenredundant:** Zonenredundanter Speicher, häufig als ZRS bezeichnet, bedeutet, dass jede Datei in drei verschiedenen Azure Storage-Clustern dreimal gespeichert wird. Genau wie bei lokal redundantem Speicher erhalten Sie mit Zonenredundanz drei Kopien jeder Datei. Diese Kopien werden jedoch physisch in drei verschiedenen Speicherclustern in verschiedenen Azure-*Verfügbarkeitszonen* isoliert. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Ein Schreibvorgang in den Speicher wird erst akzeptiert, wenn die Daten in allen drei Verfügbarkeitszonen in die Speichercluster geschrieben wurden. 
- **Georedundant:** Georedundanter Speicher (häufig als GRS bezeichnet) ähnelt dem lokal redundanten Speicher, da eine Datei in einem Azure Storage-Cluster in der primären Region dreimal gespeichert wird. Alle Schreibvorgänge werden dann asynchron in eine von Microsoft definierte sekundäre Region repliziert. Georedundanter Speicher bietet sechs Kopien Ihrer Daten, die auf zwei Azure-Regionen verteilt sind. Bei einem größeren Notfall, z. B. dem permanenten Verlust einer Azure-Region aufgrund einer Naturkatastrophe oder eines ähnlichen Ereignisses, führt Microsoft ein Failover durch, sodass das sekundäre Replikat zum primären Replikat wird, das für alle Vorgänge verwendet wird. Da die Replikation zwischen der primären und der sekundären Region asynchron ist, gehen die Daten, die noch nicht in die sekundäre Region repliziert wurden, bei einem größeren Notfall verloren. Sie können auch ein manuelles Failover eines georedundanten Speicherkontos ausführen.
- **Geozonenredundant:** Geozonenredundanter Speicher (häufig als GZRS bezeichnet) ähnelt dem zonenredundanten Speicher, da eine Datei in drei verschiedenen Storage-Clustern in der primären Region dreimal gespeichert wird. Alle Schreibvorgänge werden dann asynchron in eine von Microsoft definierte sekundäre Region repliziert. Der Failoverprozess für geozonenredundanten Speicher funktioniert genauso wie beim georedundanten Speicher.

Azure-Standarddateifreigaben unterstützen alle vier Redundanztypen, während Azure-Premium-Dateifreigaben nur lokal redundanten Speicher und zonenredundanten Speicher unterstützen.

Speicherkonten des Typs Universell v2 bieten zwei zusätzliche Redundanzoptionen, die nicht von Azure Files unterstützt werden: georedundanter Speicher mit Lesezugriff, häufig als RA-GRS bezeichnet, und geozonenredundanter Speicher mit Lesezugriff, häufig als RA-GZRS bezeichnet. Sie können Azure-Dateifreigaben in Speicherkonten mit diesen Optionen bereitstellen, Azure Files unterstützt jedoch nicht das Lesen aus der sekundären Region. Azure-Dateifreigaben, die in georedundanten oder geozonenredundanten Speicherkonten mit Lesezugriff bereitgestellt werden, werden als georedundanter bzw. geozonenredundanter Speicher abgerechnet.