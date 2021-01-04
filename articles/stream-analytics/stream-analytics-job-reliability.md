---
title: Vermeiden von Dienstunterbrechungen bei Azure Stream Analytics-Aufträgen
description: Dieser Artikel bietet eine Anleitung für die Resilienz von Stream Analytics-Aufträgen bei Upgrades.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 889e298f64689748340713de6318f8ffcd181001
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006454"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Gewährleisten der Zuverlässigkeit von Stream Analytics-Aufträgen während Dienstupdates

Ein vollständig verwalteter Dienst bietet unter anderem die Möglichkeit, dass er sich im Handumdrehen um neue Dienstfunktionen und Verbesserungen erweitern lässt. Somit kann für Stream Analytics wöchentlich (oder häufiger) eine Bereitstellung von Dienstupdates durchgeführt werden. Trotz vieler und gründlicher Tests lässt sich nicht gänzlich ausschließen, dass ein vorhandener, aktuell ausgeführter Auftrag aufgrund eines Fehlers unter Umständen unterbrochen wird. Wenn Sie unternehmenskritische Aufträge ausführen, darf dieses Risiko nicht bestehen. Sie können es verringern, indem Sie sich an das Azure-Modell für **[Regionspaare](../best-practices-availability-paired-regions.md)** halten. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Wie wirken Azure-Regionspaare diesem Risiko entgegen?

Mit Stream Analytics wird gewährleistet, dass Aufträge in Regionspaaren in separaten Batches aktualisiert werden. Dies führt zu einer ausreichenden Zeitspanne zwischen den Updates, sodass mögliche Probleme ermittelt und behoben werden können.

_Mit Ausnahme der Region „Indien, Mitte“_ (deren gekoppelte Region „Indien, Süden“ für Stream Analytics nicht verfügbar ist), erfolgt die Bereitstellung eines Updates für Stream Analytics in einer Gruppe von Regionspaaren nicht gleichzeitig. Bereitstellungen in mehreren Regionen **in derselben Gruppe** können **gleichzeitig** erfolgen.

Der Artikel über **[Verfügbarkeit und Regionspaare](../best-practices-availability-paired-regions.md)** enthält die aktuellsten Informationen darüber, welche Regionen zugeordnet werden.

Es wird empfohlen, identische Aufträge für beide gekoppelten Regionen bereitzustellen. Sie sollten diese Aufträge anschließend [überwachen](./stream-analytics-set-up-alerts.md#scenarios-to-monitor), damit Sie benachrichtigt werden, sobald etwas Unerwartetes passiert. Wenn einer dieser Aufträge nach einem Dienstupdate für Stream Analytics mit dem Status [Fehler](./job-states.md) endet, können Sie den Kundendienst kontaktieren, damit dieser Ihnen dabei hilft, die Grundursache für diesen Fehler zu finden. Sie sollten außerdem ein Failover für mögliche Downstreamconsumer auf die fehlerfreie Auftragsausgabe ausführen.

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Referenz zur Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur REST-API für die Stream Analytics-Verwaltung](/rest/api/streamanalytics/)