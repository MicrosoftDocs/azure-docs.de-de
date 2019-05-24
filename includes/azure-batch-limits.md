---
title: Includedatei
description: Includedatei
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 9ffb02fce41e8805dfccf1dfd6e982cf107039ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127544"
---
| **Ressource** | **Standardlimit** | **Maximales Limit** |
| --- | --- | --- |
| Azure Batch-Konten pro Region und Abonnement | 1-3 |50 |
| Dedizierte Kerne pro Batch-Konto | 10-100 | –<sup>1</sup> |
| Kerne mit niedriger Priorität pro Batch-Konto | 10-100 | N/V<sup>2</sup> |
| Aktive Aufträge und Auftragszeitpläne<sup>3</sup> pro Batch-Konto | 100-300 | 1.000<sup>4</sup> |
| Pools pro Batch-Konto | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Die Standardgrenzwerte variieren abhängig vom Abonnementtyp, den Sie zum Erstellen eines Batch-Kontos verwenden. Die angezeigten Kontingente für Kerne gelten für Batch-Konten im Batch-Dienstmodus. [Zeigen Sie die Kontingente in Ihrem Batch-Konto an](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>Die Anzahl der dedizierten Kerne pro Batch-Konto kann erhöht werden, aber die maximale Anzahl ist nicht festgelegt. Wenden Sie sich an den Azure-Support, um Fragen der Erhöhung zu erörtern.

<sup>2</sup>Die Anzahl der Kerne mit niedriger Priorität pro Batch-Konto kann erhöht werden, aber die maximale Anzahl ist nicht festgelegt. Wenden Sie sich an den Azure-Support, um Fragen der Erhöhung zu erörtern.

<sup>3</sup>Abgeschlossene Aufträge und Auftragszeitpläne sind nicht begrenzt.

<sup>4</sup>Wenden Sie sich an den Azure-Support, wenn Sie eine Heraufsetzung dieses Grenzwerts wünschen.
