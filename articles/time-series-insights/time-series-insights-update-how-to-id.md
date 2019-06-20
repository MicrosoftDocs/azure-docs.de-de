---
title: Bewährte Methoden für die Auswahl einer Time Series-ID in Azure Time Series Insights (Vorschauversion) | Microsoft-Dokumentation
description: Grundlegendes zu bewährten Methoden, wenn Sie eine Time Series-ID in Azure Time Series Insights Preview auswählen.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: af540267e4afc1b248b66b1c6f4989b832c38b58
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237582"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Bewährte Methoden für die Auswahl einer Time Series-ID

Dieser Artikel behandelt den Partitionsschlüssel für Azure Time Series Insights Preview, die Time Series-ID und bewährte Methoden für deren Auswahl.

## <a name="choose-a-time-series-id"></a>Auswählen einer Time Series-ID

Das Auswählen einer Time Series-ID ist wie das Auswählen eines Partitionsschlüssels für eine Datenbank. Es ist eine wichtige Entscheidung, die zur Entwurfszeit getroffen werden sollte. Sie können eine vorhandene Time Series Insights Preview-Umgebung nicht so aktualisieren, dass sie eine andere Time Series-ID verwendet. Das heißt, wenn eine Umgebung mit einer Time Series-ID erstellt wird, ist die Richtlinie eine unveränderliche Eigenschaft, die nicht mehr geändert werden kann.

> [!IMPORTANT]
> Die Time Series-ID beachtet Groß-/Kleinschreibung und ist unveränderlich (sie kann nach ihrer Festlegung nicht mehr geändert werden).

Unter diesem Aspekt ist die Auswahl der geeigneten Time Series-ID entscheidend. Wenn Sie einen Time Series-ID auswählen, berücksichtigen Sie die folgenden bewährten Methoden:

* Wählen Sie einen Eigenschaftsnamen mit einem breiten Wertespektrum und gleichmäßigen Zugriffsmustern. Es hat sich bewährt, einen Partitionsschlüssel mit vielen unterschiedlichen Werten (z. B. Hunderte oder Tausende) auszuwählen. Für viele Kunden wird dies etwas wie die „DeviceID“ oder „SensorID“ in Ihrem JSON-Objekt sein.
* Die Time Series-ID sollte auf Blattknotenebene Ihres [Zeitreihenmodells](./time-series-insights-update-tsm.md) eindeutig sein.
* Die Zeichenfolge eines Time Series-ID-Eigenschaftsnamens kann bis zu 128 Zeichen lang sein, und Time Series-ID-Eigenschaftswerte können aus bis zu 1024 Zeichen bestehen.
* Fehlende eindeutige Time Series-ID-Eigenschaftswerte werden als Nullwerte behandelt und als solche in die Eindeutigkeitseinschränkung einbezogen.

Darüber hinaus können Sie bis zu *drei* (3) Schlüsseleigenschaften als Ihre Time Series-ID auswählen.

  > [!NOTE]
  > Ihre *drei* (3) Schlüsseleigenschaften müssen Zeichenfolgen sein.

Die folgenden Szenarien beschreiben die Auswahl von mehr als einer Schlüsseleigenschaft als Ihre Time Series-ID:  

### <a name="scenario-one"></a>Szenario 1

* Sie besitzen Legacygruppen von Ressourcen mit jeweils eindeutigen Schlüsseln.
* Eine Gruppe wird z. B. eindeutig identifiziert durch die Eigenschaft *deviceId* und eine andere durch die eindeutige Eigenschaft *objectId*. Keine der Gruppen enthält die eindeutige Eigenschaft der anderen Gruppe. In diesem Beispiel würden Sie zwei Schlüssel, die „deviceId“ und die „objectId“, als eindeutige Schlüssel auswählen.
* Wir akzeptieren Nullwerte, und das Fehlen einer Eigenschaft in der Ereignisnutzlast zählt als ein `null`-Wert. Dies ist auch die geeignete Methode, um das Senden von Daten an zwei unterschiedliche Ereignisquellen zu handhaben, wobei die Daten in jeder Ereignisquelle eine eindeutige Time Series-ID haben.

### <a name="scenario-two"></a>Szenario 2

* Ihre Anforderung ist, dass mehrere Eigenschaften innerhalb derselben Gruppe von Ressourcen eindeutig sind. 
* Nehmen wir an, Sie sind Hersteller von intelligenten Gebäuden und stellen in jedem Raum Sensoren bereit. In jedem Raum haben Sie normalerweise dieselben Werte für *sensorId*, z. B. *sensor1*, *sensor2* und *sensor3*.
* Darüber hinaus besitzt Ihr Gebäude sich überlappende Stockwerk- und Raumnummern zwischen Standorten in der Eigenschaft *flrRm*, die Werte wie z. B. *1a*, *2b*, *3a* usw. aufweisen.
* Schließlich haben Sie noch eine Eigenschaft, *location* (Ort), die Werte wie *Redmond*, *Barcelona* und *Tokio* enthält. Um Eindeutigkeit herzustellen, würden Sie die folgenden drei Eigenschaften als Ihre Time Series-ID-Schlüssel festlegen: *sensorId*, *flrRm* und *location*.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Datenmodellierung](./time-series-insights-update-tsm.md).

* Planen Ihrer [Azure Time Series Insights (Preview)-Umgebung](./time-series-insights-update-plan.md).