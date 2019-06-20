---
title: include file
description: include file
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 6016b13fe7d3e1f3b673bd2446d2f68b04878cd6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178374"
---
Die Bing-Statistik stellt Analysen für Bing-Suche-APIs bereit. Die Analysen enthalten das Aufrufvolumen, die häufigsten Abfragezeichenfolgen, die geografische Verteilung und vieles mehr. Wenn Sie die Bing-Statistik in Ihrem kostenpflichtigen Abonnement für die Bing-Suche aktivieren möchten, navigieren Sie zu Ihrem [Azure-Dashboard](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), wählen Sie Ihr kostenpflichtiges Abonnement aus, und klicken Sie auf „Enable Bing Statistics“ (Bing-Statistik aktivieren). Durch das Aktivieren der Bing-Statistik wird Ihre Abonnementrate leicht erhöht (siehe [Preise](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Die Bing-Statistik ist nur für kostenpflichtige Abonnements, nicht für kostenlose Testabonnements verfügbar.

> [!NOTE]
> Sie können die Daten, die über das Dashboard der Bing-Statistik verfügbar sind, nicht verwenden, um Anwendungen zu erstellen, die an Dritte verteilt werden sollen.

Bing aktualisiert die Analysedaten alle 24 Stunden und speichert den Verlauf für bis zu 13 Monate.

## <a name="accessing-your-analytics"></a>Zugriff auf Ihre Analysen

Navigieren Sie zu https://bingapistatistics.com, um auf Ihr Analysedashboard zuzugreifen. Stellen Sie sicher, dass Sie mit dem gleichen Microsoft-Konto angemeldet sind, das Sie für Ihr kostenpflichtiges Abonnement verwenden.

## <a name="filtering-the-data"></a>Filtern der Daten

Standardmäßig repräsentieren die Diagramme und Graphen alle Metrikdaten, auf die Sie Zugriff haben. Sie können die Daten in den Diagrammen und Graphen filtern, indem Sie die Ressourcen, Märkte, Endpunkte und Berichtszeiträume auswählen, an denen Sie interessiert sind. Die Diagramme und Graphen ändern sich den angewendeten Filtern entsprechend. Im Folgenden werden die Filter beschrieben, die Sie ändern können.

- **Ressourcen-ID:** Die eindeutige Ressourcen-ID, die Ihr Azure-Abonnement identifiziert. Die Liste enthält mehrere IDs, wenn Sie ein Abonnement für mehr als einen Tarif der Bing-Suche-API besitzen. Standardmäßig sind alle Ressourcen ausgewählt.  
  
- **Märkte:** Die Märkte, aus denen die Ergebnisse stammen, z.B. en-US (Englisch, USA). Standardmäßig sind alle Märkte ausgewählt. Beachten Sie, dass der Markt „en-WW“ der Markt ist, den Bing verwendet, wenn der Aufruf keinen Markt angibt und Bing den Markt des Benutzers nicht bestimmen kann.  
  
- **Endpunkte:** Die Endpunkte der Bing-Suche-API. Die Liste enthält alle Endpunkte, für die Sie ein kostenpflichtiges Abonnement besitzen. Standardmäßig sind alle Endpunkte ausgewählt.  

- **Zeitrahmen:** Der Berichtszeitraum. Sie können Folgendes angeben:
  - Gesamt: Enthält Daten aus bis zu 13 Monaten  
  - Letzte 24 Stunden: Enthält Analysen der letzten 24 Stunden  
  - Letzte Woche: Enthält Analysen der letzten sieben Tage  
  - Letzter Monat: Enthält Analysen der letzten 30 Tage  
  - Einen benutzerdefinierten Datenbereich: Enthält Analysen des angegebenen Datenbereichs (falls verfügbar)  

  > [!NOTE]  
  > Es kann bis zu 24 Stunden dauern, bis die Metriken auf dem Dashboard angezeigt werden. Das Dashboard zeigt das Datum und die Uhrzeit des letzten Updates der Daten an.  

  > [!NOTE]  
  > Metriken sind verfügbar, sobald Sie das Add-In „Bing-Statistik“ aktivieren.

## <a name="charts-and-graphs"></a>Diagramme und Graphen

Das Dashboard zeigt Diagramme und Graphen der Metriken an, die für den ausgewählten Endpunkt verfügbar sind. Nicht alle Metriken sind für alle Endpunkte verfügbar. Die Diagramme und Graphen für jeden Endpunkt sind statisch (d.h. Sie können nicht auswählen, welche Diagramme und Graphen angezeigt werden). Das Dashboard zeigt nur Diagramme und Graphen, für die Daten vorhanden sind.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Im Folgenden werden die möglichen Metriken beschrieben. Jede Metrik weist auf Endpunkteinschränkungen hin.

- **Aufrufvolumen:** Zeigt die Anzahl der Aufrufe auf, die während des Berichtszeitraums durchgeführt wurden. Wenn der Berichtszeitraum einen Tag beträgt, zeigt das Diagramm die Anzahl der Aufrufe pro Stunde. Andernfalls zeigt das Diagramm die Anzahl der Aufrufe pro Tag für den Berichtszeitraum.  
  
  > [!NOTE]
  > Das Aufrufvolumen kann sich von den Abrechnungsberichten unterscheiden, die in der Regel nur erfolgreiche Aufrufe beinhalten.

- **Wichtigste Abfragen:** Zeigt die häufigsten Abfragen und die Anzahl des Vorkommens jeder Abfrage während des Berichtszeitraums an. Sie können die Anzahl der angezeigten Abfragen konfigurieren. Sie können beispielsweise die 25, 50 oder 75 häufigsten Abfragen anzeigen lassen. Diese Option ist für folgende Endpunkte nicht verfügbar:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Einige Abfrageausdrücke werden unterdrückt, um vertrauliche Informationen wie z.B. E-Mail-Adressen, Telefonnummern, SSN zu entfernen.

- **Geografische Verteilung:** Die Märkte, aus denen die Ergebnisse stammen, z.B. en-US (Englisch, USA). Bing verwendet den Abfrageparameter `mkt`, um den Markt zu bestimmen (falls angegeben). Andernfalls verwendet Bing Signale wie die IP-Adresse des Aufrufers, um den Markt zu bestimmen.

- **Verteilung von Antwortcodes:** Die HTTP-Statuscodes aller Aufrufe während des Berichtszeitraums.

- **Verteilung des Aufrufursprungs:** Die verschiedenen Browser, die von den Benutzern verwendet werden, z.B. Microsoft Edge, Chrome, Safari und Firefox. Aufrufe, die von außerhalb eines Browsers durchgeführt werden, z.B. von Bots, Postman oder „curl“ über eine Konsolen-App, werden unter „Bibliotheken“ gruppiert. Der Ursprung wird mithilfe des Werts ermittelt, den der Header „User-Agent“ der Anforderung aufweist. Wenn die Anforderung den Header „User-Agent“ nicht enthält, versucht Bing, den Ursprung aus anderen Signalen abzuleiten.  

- **Verteilung der sicheren Suche:** Die Verteilung der Werte für sicheres Suchen, z.B. „Deaktiviert“, „Mittel“ oder „Streng“. Der Abfrageparameter `safeSearch` enthält den Wert (falls angegeben). Andernfalls legt Bing den Wert standardmäßig auf „Mittel“ fest.  

- **Verteilung der angeforderten Antworten:** Die Antworten der Websuche-API, die Sie im Abfrageparameter `responseFilter` angefordert haben.  

- **Verteilung der zurückgegebenen Antworten:** Die Antworten, die die Websuche-API zurückgegeben hat.

- **Verteilung der Antwortserver:** Der Anwendungsserver, der Ihre API-Anforderungen verarbeitet hat. Die möglichen Werte sind „Bing.com“ (für Datenverkehr von Desktopgeräten und Laptops) und „Bing.com-mobile“ (für Datenverkehr von mobilen Geräten). Der Server wird mithilfe des Werts ermittelt, den der Header „User-Agent“ der Anforderung aufweist. Wenn die Anforderung den Header „User-Agent“ nicht enthält, versucht Bing, den Server aus anderen Signalen abzuleiten.

Im Folgenden werden die Analysen dargestellt, die für jeden Endpunkt verfügbar sind.

![Verteilung nach Endpunkt (Unterstützungsmatrix)](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
