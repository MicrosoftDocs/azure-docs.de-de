---
title: Verwalten von Datenfeeds in Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Datenfeeds verwalten, die Sie zu Metrics Advisor hinzugefügt haben.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184721"
---
# <a name="how-to-manage-your-data-feeds"></a>Gewusst wie: Verwalten Ihrer Datenfeeds

Erfahren Sie, wie Sie Ihre integrierten Datenfeeds in Metrics Advisor verwalten. Dieser Artikel führt Sie durch die Verwaltung von Datenfeeds in Metrics Advisor.

## <a name="edit-a-data-feed"></a>Bearbeiten eines Datenfeeds

> [!NOTE]
> Die folgenden Details können nach der Erstellung eines Datenfeeds nicht geändert werden. 
> * Datenfeed-ID
> * Erstellungszeit
> * Dimension
> * Quellentyp
> * Granularität

Nur der Administrator eines Datenfeeds darf Änderungen daran vornehmen. 

So halten Sie einen Datenfeed an oder aktivieren ihn erneut:

1. Klicken Sie auf der Seite mit der Datenfeedliste auf den Vorgang, den Sie für den Datenfeed ausführen möchten.

2. Klicken Sie auf der Datenfeed-Detailseite auf die Schaltfläche **Status**.

So löschen Sie einen Datenfeed: 

1. Klicken Sie auf der Seite mit der Datenfeedliste auf **Löschen** für den Datenfeed.

2. Klicken Sie auf der Datenfeed-Detailseite auf **Löschen**.

Wenn Sie die Startzeit ändern, müssen Sie das Schema erneut überprüfen. Sie können es mithilfe von **Parameter bearbeiten** ändern.

##  <a name="backfill-your-data-feed"></a>Abgleich des Datenfeeds

Wählen Sie die Schaltfläche **Backfill** (Abgleich) aus, um eine sofortige Erfassung für einen Zeitstempel auszulösen und eine fehlerhafte Erfassung zu beheben oder die vorhandenen Daten zu überschreiben.
- Die Startzeit ist eingeschlossen.
- Die Endzeit ist ausgeschlossen.
- Die Anomalieerkennung wird nur für den ausgewählten Bereich erneut ausgelöst.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Abgleich des Datenfeeds":::

## <a name="manage-permission-of-a-data-feed"></a>Verwalten der Berechtigung eines Datenfeeds

Der Zugriff auf den Arbeitsbereich wird durch die Metrics Advisor-Ressource gesteuert, die Azure Active Directory für die Authentifizierung verwendet. Auf Metrikdaten wird eine weitere Berechtigungsebene angewendet.

Mit Metrics Advisor können Sie verschiedenen Gruppen von Personen Berechtigungen für verschiedene Datenfeeds erteilen. Die folgenden beiden Rollen stehen zur Verfügung: 

- Administrator: Verfügt über vollständige Berechtigungen zum Verwalten eines Datenfeeds, einschließlich Ändern und Löschen
- Anzeigender Benutzer: Hat Zugriff auf eine schreibgeschützte Ansicht des Datenfeeds
 

## <a name="advanced-settings"></a>Erweiterte Einstellungen

Beim Erstellen eines neuen Datenfeeds gibt es mehrere optionale erweiterte Einstellungen. Diese können auf der Datenfeed-Detailseite geändert werden.

### <a name="ingestion-options"></a>Erfassungsoptionen

* **Ingestion time offset** (Erfassungszeit-Offset): Standardmäßig werden Daten der angegebenen Granularität entsprechend erfasst. Beispielsweise wird eine Metrik mit einem *täglichen* Zeitstempel einen Tag nach dem Zeitstempel erfasst. Mit dem Offset können Sie die Erfassungszeit mit einer *positiven* Zahl nach hinten oder mit einer *negativen* Zahl nach vorne verschieben.

* **Maximale Parallelität**: Legen Sie diesen Parameter fest, wenn Ihre Datenquelle eingeschränkte Parallelität unterstützt. Behalten Sie andernfalls die Standardeinstellung bei.

* **Stop retry after** (Wiederholungsversuche beenden): Wenn die Datenerfassung fehlgeschlagen ist, wird der Vorgang innerhalb eines bestimmten Zeitraums automatisch wiederholt. Der Anfang dieses Zeitraums ist der Zeitpunkt, zu dem die erste Datenerfassung stattgefunden hat. Die Länge des Zeitraums wird der Granularität entsprechend definiert. Wenn Sie den Standardwert (-1) beibehalten, wird der Wert entsprechend der unten angegebenen Granularität bestimmt.
    
    | Granularität       | Wiederholungsversuche beenden           |
    | :------------ | :--------------- |
    | Täglich, Benutzerdefiniert (>= 1 Tag), Wöchentlich, Monatlich, Jährlich     | 7 Tage          |
    | Stündlich, Benutzerdefiniert (< 1 Tag)       | 72 Stunden |

* **Min retry interval** (Min. Wiederholungsintervall): Sie können das Mindestintervall angeben, zu dem das Pullen von Daten von der Quelle erneut versucht werden soll. Wenn Sie den Standardwert (-1) beibehalten, wird das Wiederholungsintervall entsprechend der unten angegebenen Granularität bestimmt.
    
    | Granularität       | Mindestwiederholungsintervall           |
    | :------------ | :--------------- |
    | Täglich, Benutzerdefiniert (>= 1 Tag), Wöchentlich, Monatlich     | 30 Minuten          |
    | Stündlich, Benutzerdefiniert (< 1 Tag)      | 10 Minuten |
    | Jährlich | 1 Tag          |
 
### <a name="fill-gap-when-detecting"></a>Fill gap when detecting (Lücke beim Erkennen schließen): 

> [!NOTE]
> Diese Einstellung wirkt sich nicht auf Ihre Datenquelle und die Datendiagramme aus, die im Portal angezeigt werden. Das automatische Füllen tritt nur bei der Anomalieerkennung auf.

Einige Zeitreihen sind nicht kontinuierlich. Wenn Datenpunkte fehlen, verwendet Metrics Advisor den angegebenen Wert, um sie vor der Anomalieerkennung aufzufüllen, um die Genauigkeit zu erhöhen.
Die Optionen sind: 

* Verwenden des Werts aus dem vorherigen tatsächlichen Datenpunkt. Diese Option wird standardmäßig verwendet.
* Verwenden eines spezifischen Werts

### <a name="action-link-template"></a>Aktionslinkvorlage: 

Aktionslinkvorlagen werden verwendet, um ausführbare HTTP-URLs vorab zu definieren, die aus den Platzhaltern `%datafeed`, `%metric`, `%timestamp`, `%detect_config`und `%tagset` bestehen. Sie können die Vorlage verwenden, um bei einer Anomalie oder einem Incident eine Weiterleitung zu einer bestimmten URL durchzuführen, um einen Drilldown auszuführen.

:::image type="content" source="../media/action-link-template.png" alt-text="Aktionslinkvorlage" lightbox="../media/action-link-template.png":::

Nachdem Sie den Aktionslink ausgefüllt haben, klicken Sie auf **Go to action link** (Zu Aktionslink wechseln) in der Aktionsoption des Incidentlinks und im Kontextmenü der Incidentstruktur. Ersetzen Sie die Platzhalter in der Aktionslinkvorlage durch die entsprechenden Werte der Anomalie oder des Incidents.

| Platzhalter | Beispiele | Kommentar |
| ---------- | -------- | ------- |
| `%datafeed` | - | Datenfeed-ID |
| `%metric` | - | Metrik-ID |
| `%detect_config` | - | Erkennen der Konfigurations-ID |
| `%timestamp` | - | Zeitstempel einer Anomalie oder Endzeit eines permanenten Incidents |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Dimensionswerte einer Anomalie oder oberste Anomalie eines Vorfalls.   <br> `filterVal` wird verwendet, um übereinstimmende Werte innerhalb der eckigen Klammern herauszufiltern.   |

Beispiele:

* Bei der Aktionslinkvorlage `https://action-link/metric/%metric?detectConfigId=%detect_config`
  * Der Aktionslink `https://action-link/metric/1234?detectConfigId=2345` wechselt zu Anomalien oder Incidents unter der Metrik `1234` und erkennt die Konfiguration `2345`.

* Bei der Aktionslinkvorlage `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` 
    * Der Aktionslink ist `https://action-link?Dim1=Val1&Dim2=Val2`, wenn die Anomalie `{ "Dim1": "Val1", "Dim2": "Val2" }` ist. 
    * Der Aktionslink ist `https://action-link?Dim2=Val2`, wenn die Anomalie `{ "Dim1": "", "Dim2": "Val2" } ` ist, da `[Dim1=***&]` für den Dimensionswert einer leeren Zeichenfolge übersprungen wird. 

* Bei der Aktionslinkvorlage `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` 
    * Der Aktionslink ist `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'`, wenn die Anomalie `{ "Dim1": "Val1", "Dim2": "Val2" }` ist. 
    * Der Aktionslink ist `https://action-link?filter=Name/Dim2 eq 'Val2'`, wenn die Anomalie `{ "Dim1": "", "Dim2": "Val2" }` ist, da `[Name/Dim1 eq '***' and ]` für den Dimensionswert einer leeren Zeichenfolge übersprungen wird. 
   
### <a name="data-feed-not-available-alert-settings"></a>Warnungseinstellungen für einen nicht verfügbaren Datenfeed

Ein Datenfeed wird als nicht verfügbar betrachtet, wenn innerhalb der Toleranzperiode, die ab dem Zeitpunkt, zu dem die Erfassung der Datenfeeds beginnt, keine Daten von der Quelle erfasst werden. In dem Fall wird eine Warnung ausgelöst.

Zum Konfigurieren einer Warnung müssen Sie zuerst [einen Hook erstellen](alerts.md#create-a-hook). Warnungen werden über den konfigurierten Hook gesendet.

* **Toleranzperiode**: Die Einstellung für die Toleranzperiode wird verwendet, um zu bestimmen, wann eine Warnung gesendet werden soll, wenn keine Datenpunkte erfasst werden. Der Bezugspunkt ist der Zeitpunkt der ersten Erfassung. Wenn bei einer Erfassung ein Fehler auftritt, wiederholt Metrics Advisor die Versuche in regelmäßigen Abständen, die durch die Granularität festgelegt sind. Wenn beim Vorgang nach der Toleranzperiode weiterhin Fehler auftreten, wird eine Warnung gesendet.

* **Automatisch erneut erinnern**: Wenn diese Option auf Null festgelegt ist, löst jeder Zeitstempel mit *Nicht verfügbar* eine Warnung aus. Wenn eine andere Einstellung als Null angegeben ist, werden kontinuierliche Zeitstempel nach dem ersten Zeitstempel mit *Nicht verfügbar* nicht entsprechend der angegebenen Einstellung ausgelöst.

## <a name="next-steps"></a>Nächste Schritte
- [Konfigurieren von Metriken und Optimieren der Konfigurationserkennung](configure-metrics.md)
- [Anpassen der Anomalieerkennung anhand von Feedback](anomaly-feedback.md)
- [Diagnostizieren eines Incidents](diagnose-incident.md)
