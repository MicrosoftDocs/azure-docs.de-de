---
title: Vermeiden von Dienstunterbrechungen bei Azure Stream Analytics-Aufträgen
description: Dieser Artikel bietet eine Anleitung für die Resilienz von Stream Analytics-Aufträgen bei Upgrades.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479455"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Gewährleisten der Zuverlässigkeit von Stream Analytics-Aufträgen während Dienstupdates

Ein vollständig verwalteter Dienst bietet unter anderem die Möglichkeit, dass er sich im Handumdrehen um neue Dienstfunktionen und Verbesserungen erweitern lässt. Somit kann für Stream Analytics wöchentlich (oder häufiger) eine Bereitstellung von Dienstupdates durchgeführt werden. Trotz vieler und gründlicher Tests lässt sich nicht gänzlich ausschließen, dass ein vorhandener, aktuell ausgeführter Auftrag aufgrund eines Fehlers unter Umständen unterbrochen wird. Für Kunden, die wichtige Streamingverarbeitungsaufträge ausführen, müssen derartige Risiken vermieden werden. Das Modell der **[Azure-Regionspaare](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** stellt eine Möglichkeit dar, mit der Kunden dieses Risiko verringern können. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Wie wirken Azure-Regionspaare diesem Risiko entgegen?

Mit Stream Analytics wird gewährleistet, dass Aufträge in Regionspaaren in separaten Batches aktualisiert werden. Dies führt zu einer ausreichenden Zeitspanne zwischen den Updates, sodass mögliche Unterbrechungsfehler ermittelt und behoben werden können.

_Mit Ausnahme der Region „Indien, Mitte“_ (deren gekoppelte Region „Indien, Süden“ für Stream Analytics nicht verfügbar ist), erfolgt die Bereitstellung eines Updates für Stream Analytics in einer Gruppe von Regionspaaren nicht gleichzeitig. Bereitstellungen in mehreren Regionen **in derselben Gruppe** können **gleichzeitig** erfolgen.

Der Artikel über **[Verfügbarkeit und Regionspaare](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** enthält die aktuellsten Informationen darüber, welche Regionen zugeordnet werden.

Für Kunden empfiehlt es sich, identische Aufträge für beide gekoppelte Regionen bereitzustellen. Neben den internen Überwachungsfunktionen von Stream Analytics wird Kunden zudem empfohlen, die Aufträge so zu überwachen, als handle es sich bei **beiden** um Produktionsaufträge. Wenn eine Unterbrechung als Folge des Stream Analytics-Dienstupdates ermittelt wird, werden die entsprechende Eskalation und ein Failover der Downstreamconsumer an die fehlerfreie Auftragsausgabe durchgeführt. Die Eskalation zum Support verhindert, dass die gekoppelte Region von der neuen Bereitstellung betroffen ist, und gewährleistet die Integrität der gekoppelten Aufträge.

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Referenz zur Stream Analytics-Abfragesprache](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur REST-API für die Stream Analytics-Verwaltung](https://msdn.microsoft.com/library/azure/dn835031.aspx)
