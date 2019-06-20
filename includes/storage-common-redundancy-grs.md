---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178702"
---
Georedundanter Speicher (GRS) ist so konzipiert, dass er eine Dauerhaftigkeit von mindestens 99,99999999999999999999 % (16 mal die 9) von Objekten über ein gegebenes Jahr bereitstellt, indem Ihre Daten in eine sekundäre Region repliziert werden, die Hunderte Kilometer von der primären Region entfernt ist. Wenn für Ihr Speicherkonto GRS aktiviert ist, sind Ihre Daten beständig gespeichert, selbst bei einem regionalen Komplettausfall oder einem Notfall, nach dem die primäre Region nicht mehr wiederhergestellt werden kann.

Wenn Sie sich für GRS entscheiden, haben Sie zwei Optionen zur Auswahl:

* GRS repliziert Ihre Daten in ein anderes Rechenzentrum in einer sekundären Region, aber diese Daten sind nur schreibgeschützt verfügbar, wenn Microsoft ein Failover von der primären in die sekundäre Region initiiert. 
* Georedundanter Speicher mit Lesezugriff (RA-GRS) basiert auf GRS. RA-GRS repliziert Ihre Daten in ein anderes Rechenzentrum in einer sekundären Region und bietet Ihnen die Möglichkeit, aus der sekundären Region zu lesen. Mit RA-GRS können Sie aus der sekundären Region lesen, unabhängig davon, ob Microsoft ein Failover von der primären Region auf die sekundäre Region initiiert. 

Bei einem Speicherkonto mit aktiviertem GRS oder RA-GRS werden alle Daten zunächst mit dem lokal redundanten Speicher (LRS) repliziert. Ein Update wird zunächst an den primären Speicherort übertragen und mit LRS repliziert. Anschließend wird das Update asynchron mit GRS in die sekundäre Region repliziert. Wenn Daten in den sekundären Speicherort geschrieben werden, werden sie dort auch mit LRS repliziert. 

Sowohl in der primären als auch in der sekundären Region werden Replikate über separate Fehlerdomänen und Upgradedomänen hinweg in einer Speicherskalierungseinheit verwaltet. Die Speicherskalierungseinheit ist die grundlegende Replikationseinheit im Datencenter. Die Replikation auf dieser Ebene wird von LRS bereitgestellt. Weitere Informationen finden Sie unter [Lokal redundanter Speicher (LRS): Kostengünstige Datenredundanz für Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Beachten Sie diese Punkte, wenn Sie sich für eine Replikationsoption entscheiden:

* Zonenredundanter Speicher (ZRS) bietet eine hohe Verfügbarkeit mit synchroner Replikation und ist für einige Szenarien ggf. besser geeignet als GRS oder RA-GRS. Weitere Informationen zu ZRS finden Sie unter [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Die asynchrone Replikation beinhaltet eine Verzögerung zwischen dem Zeitpunkt, zu dem diese Daten in der primären Region geschrieben werden, und dem Zeitpunkt, zu dem sie in der sekundären Region repliziert werden. Bei einem regionalen Notfall gehen Änderungen, die noch nicht in der sekundären Region repliziert wurden, möglicherweise verloren, wenn die Daten nicht in der primären Region wiederhergestellt werden können.
* Mit GRS ist das Replikat nicht für den Lese- oder Schreibzugriff verfügbar, sofern von Microsoft kein Failover in der sekundären Region initiiert wird. Im Fall eines Failovers erhalten Sie nach Abschluss des Failovers Lese- und Schreibzugriff auf diese Daten. Weitere Informationen finden Sie im [Leitfaden zur Notfallwiederherstellung](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Wenn Ihre Anwendung aus der sekundären Region lesen soll, aktivieren Sie RA-GRS.