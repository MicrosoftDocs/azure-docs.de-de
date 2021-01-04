---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ac91ef5a56fe234cba47b430b78e74ce81c9d504
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96536924"
---
Azure Storage stellt in Azure Monitor folgende Transaktionsmetriken bereit:

| Metrik | BESCHREIBUNG |
| ------------------- | ----------------- |
| Transaktionen | Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und nicht erfolgreiche Anforderungen sowie Anforderungen, die zu Fehlern geführt haben. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Verfügbare Dimensionen: ResponseType, GeoType, ApiName, Authentication ([Definition](#metrics-dimensions))<br/> Beispielwert: 1024 |
| Eingehende Daten | Die eingehende Datenmenge. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Gesamt <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| Ausgehende Daten | Die ausgehende Datenmenge. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Gesamt <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| SuccessServerLatency | Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz. <br/><br/> Einheit: Millisekunden <br/> Aggregationstyp: Average <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| SuccessE2ELatency | Die durchschnittliche End-to-End-Wartezeit (in Millisekunden) bei erfolgreichen Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung. Der Unterschied zwischen SuccessE2ELatency- und SuccessServerLatency-Werten ist die Latenz, die wahrscheinlich durch das Netzwerk und den Client verursacht wird.<br/><br/> Einheit: Millisekunden <br/> Aggregationstyp: Average <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| Verfügbarkeit | Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Zur Berechnung der Verfügbarkeit wird der Wert der gesamten gebührenpflichtigen Anforderungen durch die Anzahl entsprechender Anforderungen geteilt. Dabei werden auch Anforderungen einbezogen, die zu unerwarteten Fehlern geführt haben. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang. <br/><br/> Einheit: Percent <br/> Aggregationstyp: Average <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 99,99 |
