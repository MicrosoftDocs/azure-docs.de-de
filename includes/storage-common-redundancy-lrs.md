---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 124f5c01b7718f729094de1c02391946ff50cef4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178167"
---
Lokal redundanter Speicher (LRS) stellt eine Dauerhaftigkeit von mindestens 99,999999999 % (11 Neunen) für Objekte in einem bestimmten Jahr bereit. LRS stellt diese Objektdauerhaftigkeit bereit, indem Ihre Daten auf eine Speicherskalierungseinheit repliziert werden. Ein Datencenter in der Region, in der Sie Ihr Speicherkonto erstellt haben, hostet die Speicherskalierungseinheit. Eine Schreibanforderung an ein LRS-Speicherkonto wird erst dann erfolgreich zurückgegeben, nachdem die Daten in alle Replikate geschrieben wurden. Jedes Replikat befindet sich in separaten Fehler- und Upgradedomänen innerhalb einer Speicherskalierungseinheit.

Eine Speicherskalierungseinheit ist eine Sammlung von Speicherknotengestellen (Racks). Eine Fehlerdomäne (FD) ist eine Gruppe von Knoten, die eine physische Fehlereinheit darstellen. Stellen Sie sich eine Fehlerdomäne als Knoten vor, die zum gleichen physischen Rack gehören. Eine Upgradedomäne (UD) ist eine Gruppe von Knoten, die während des Vorgangs eines Dienstupgrades (Rollouts) gemeinsam aktualisiert werden. Die Replikate werden auf UDs und FDs in einer Speicherskalierungseinheit verteilt. Diese Architektur stellt sicher, dass Ihre Daten verfügbar sind, wenn ein Hardwareausfall ein einzelnes Rack betrifft, oder wenn Knoten bei einem Dienstupgrade aktualisiert werden.

LRS ist die kostengünstigste Replikationsmöglichkeit und bietet im Vergleich zu anderen Optionen die geringste Dauerhaftigkeit. Bei einem Katastrophenfall in einem Rechenzentrum (Feuer, Überschwemmung usw.) gehen ggf. alle Replikate verloren oder können nicht mehr wiederhergestellt werden. Um dieses Risiko zu minimieren, wird empfohlen, entweder zonenredundante Speicher (ZRS) oder georedundante Speicher (GRS) zu verwenden.

* Wenn Ihre Anwendung Daten speichert, die bei Datenverlust problemlos wiederhergestellt werden können, können Sie sich für LRS entscheiden.
* Einige Anwendungen sind aufgrund von Datenvorschriften auf die Replikation von Daten innerhalb eines Lands/einer Region beschränkt. In einigen Fällen können sich die gekoppelten Regionen, in denen die Daten für GRS-Konten repliziert werden, in einem anderen Land/einer anderen Region befinden. Weitere Informationen zu gekoppelten Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).
