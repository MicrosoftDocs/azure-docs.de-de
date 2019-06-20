---
title: include file
description: include file
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: b3788ede23a423bebf96661ea88b227bfb5fdf4c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178467"
---
Die folgende Tabelle informiert über die einzelnen zentralen Kontingente, Einschränkungen, Standardwerte und Drosselungen für Azure Scheduler:

| Resource | Beschreibung der Einschränkung |
| -------- | ----------------- |
| **Auftragsgröße** | Die maximale Auftragsgröße beträgt 16.000. Wenn ein PUT- oder PATCH-Vorgang zu einer Auftragsgröße führt, die größer als dieser Grenzwert ist, wird ein Statuscode 400 Bad Request (unzulässige Anforderung) zurückgegeben. | 
| **Auftragssammlungen** | Die maximale Anzahl von Auftragssammlungen pro Azure-Abonnement beträgt 200.000. | 
| **Aufträge pro Sammlung** | Standardmäßig beträgt die maximale Anzahl der Aufträge fünf Aufträge in einer kostenlosen Auftragssammlung und 50 Aufträge in einer Standardauftragssammlung. <p>Sie können die maximale Auftragsanzahl für eine Auftragssammlung ändern. Der für die Auftragssammlung festgelegte Wert gilt für alle Aufträge in der Auftragssammlung. Wenn Sie versuchen, mehr Aufträge als das maximale Auftragskontingent zu erstellen, schlägt die Anforderung mit dem Statuscode 409 Conflict (Konflikt) fehl. | 
| **Zeit bis zur Startzeit** | Der maximal zulässige Wert für die „Zeit bis zur Startzeit“ beträgt 18 Monate. |
| **Wiederholungsspanne** | Die maximal zulässige Wiederholungsspanne beträgt 18 Monate. | 
| **Frequency** | Standardmäßig beträgt das maximale Häufigkeitskontingent eine Stunde bei einer kostenlosen Auftragssammlung und eine Minute bei einer Standardauftragssammlung. <p>Sie können die maximale Häufigkeit für eine Auftragssammlung niedriger als den Maximalwert festlegen. Der für die Auftragssammlung festgelegte Wert gilt für alle Aufträge in der Auftragssammlung. Beim Versuch, einen Auftrag mit einer Häufigkeit zu erstellen, die die maximal zulässige Häufigkeit der Auftragssammlung übersteigt, ist die Anforderung nicht erfolgreich, und der Statuscode 409 Conflict (Konflikt) wird zurückgegeben. | 
| **Textlänge** | Die maximale Textgröße für eine Anforderung beträgt 8.192 Zeichen. |
| **Größe der Anforderungs-URL** | Die maximale Größe für eine Anforderungs-URL beträgt 2.048 Zeichen. |
| **Headeranzahl** | Die maximale Headeranzahl beträgt 50 Header. | 
| **Aggregierte Headergröße** | Die aggregierte Headergröße darf maximal 4.096 Zeichen umfassen. |
| **Timeout** | Das Anforderungstimeout ist statisch, d.h. nicht konfigurierbar, und beträgt 60 Sekunden für HTTP-Aktionen. Für Vorgänge mit längerer Ausführungszeit folgen Sie den asynchronen HTTP-Protokollen. Geben Sie z.B. sofort 202 zurück, arbeiten Sie aber weiterhin im Hintergrund. | 
| **Auftragsverlauf** | Die maximale Größe des im Auftragsverlauf gespeicherten Antworttexts beträgt 2.048 Bytes. |
| **Speicherung des Auftragsverlaufs** | Der Auftragsverlauf wird bis zu zwei Monate lang oder maximal für die letzten 1.000 Ausführungen beibehalten. | 
| **Speicherung abgeschlossener und fehlerhafter Aufträge** | Abgeschlossene und fehlerhafte Aufträge werden 60 Tage lang aufbewahrt. |
||| 

