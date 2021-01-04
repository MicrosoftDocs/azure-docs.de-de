---
title: Visualisieren der Azure-NSG-Flussprotokolle – Power BI
titleSuffix: Azure Network Watcher
description: Hier erfahren Sie, wie Sie mit Power BI Datenflussprotokolle für Netzwerksicherheitsgruppen visualisieren, um Informationen zum IP-Datenverkehr in Azure Network Watcher anzeigen zu können.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: a4c97b8baaf5265f984622e2dc47e6c8c714172a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995725"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualisieren der Datenflussprotokolle von Netzwerksicherheitsgruppen mit Power BI

Mit Datenflussprotokollen von Netzwerksicherheitsgruppen können Sie Informationen zu eingehendem und ausgehendem IP-Datenverkehr für Netzwerksicherheitsgruppen anzeigen. In diesen Datenflussprotokollen werden ausgehende und eingehende Datenflüsse pro Regel, die NIC, auf die sich der Datenfluss bezieht, 5-Tupel-Informationen zum Datenfluss (Quell-/Ziel-IP, Quell-/Zielport, Protokoll) und Informationen zu zugelassenem oder abgelehntem Datenverkehr angezeigt.

Es kann schwierig sein, Erkenntnisse zu Datenflussprotokoll-Daten zu gewinnen, indem die Protokolldateien manuell durchsucht werden. In diesem Artikel stellen wir eine Lösung vor, mit der Sie Ihre aktuellen Datenflussprotokolle visualisieren und sich über den Datenverkehr in Ihrem Netzwerk informieren können.

> [!Warning]  
> In den folgenden Schritten werden Flowprotokolle der Version 1 verwendet. Ausführliche Informationen finden Sie unter [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md). Die folgenden Anweisungen funktionieren ohne Änderungen nicht mit Version 2 der Protokolldateien.

## <a name="scenario"></a>Szenario

Im folgenden Szenario verbinden wir Power BI Desktop mit dem Speicherkonto, das wir als Senke für unsere Daten der NSG-Datenflussprotokollierung konfiguriert haben. Nachdem wir eine Verbindung mit unserem Speicherkonto hergestellt haben, lädt Power BI die Protokolle herunter und analysiert sie, um eine visuelle Darstellung des Datenverkehrs zu erzeugen, der von den Netzwerksicherheitsgruppen protokolliert wird.

Anhand der visuellen Elemente der Vorlage können Sie Folgendes untersuchen:

* „Top Talkers“
* Zeitreihen-Datenflussdaten nach Richtung und Regelentscheidung
* Datenflüsse nach MAC-Adresse der Netzwerkschnittstelle
* Datenflüsse nach NSG und Regel
* Datenflüsse nach Zielport

Die bereitgestellte Vorlage kann bearbeitet werden. Sie können sie also ändern, um neue Daten oder visuelle Elemente hinzuzufügen oder Abfragen an Ihre Anforderungen anzupassen.

## <a name="setup"></a>Einrichten

Bevor Sie beginnen, müssen Sie die NSG-Datenflussprotokollierung für mindestens eine Netzwerksicherheitsgruppe Ihres Kontos aktiviert haben. Eine Anleitung zum Aktivieren von NSG-Datenflussprotokollen finden Sie im Artikel [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md).

Außerdem muss der Power BI Desktop-Client auf Ihrem Computer installiert sein, und auf dem Computer muss ausreichend freier Speicherplatz vorhanden sein, um die Protokolldaten, die in Ihrem Speicherkonto vorhanden sind, herunterzuladen und zu laden.

![Visio-Diagramm][1]

### <a name="steps"></a>Schritte

1. Laden Sie die folgende Power BI-Vorlage in der Power BI Desktop-Anwendung herunter, und öffnen Sie sie: [Network Watcher PowerBI flow logs template](https://aka.ms/networkwatcherpowerbiflowlogstemplate) (Network Watcher – Vorlage für Power BI-Datenflussprotokolle).
1. Geben Sie die erforderlichen Abfrageparameter ein.
   1. **StorageAccountName**: Gibt den Namen des Speicherkontos mit den NSG-Datenflussprotokollen an, die geladen und visualisiert werden sollen.
   1. **NumberOfLogFiles**: Gibt die Anzahl von Protokolldateien an, die heruntergeladen und in Power BI visualisiert werden sollen. Wenn beispielsweise der Wert 50 angegeben ist, sind dies die letzten 50 Protokolldateien. Wenn zwei NSGs aktiviert und für das Senden von NSG-Datenflussprotokollen an dieses Konto konfiguriert sind, können die letzten 25 Stunden des Protokollverlaufs angezeigt werden.

      ![Power BI-Hauptfenster][2]

1. Geben Sie den Zugriffsschlüssel für Ihr Speicherkonto ein. Sie gelangen zu den gültigen Zugriffsschlüsseln, indem Sie im Azure-Portal zu Ihrem Speicherkonto navigieren und im Menü „Einstellungen“ die Option **Zugriffsschlüssel** wählen. Klicken Sie auf **Verbinden**, und wenden Sie anschließend die Änderungen an.

    ![Zugriffsschlüssel][3]

    ![Zugriffsschlüssel 2][4]

4. Ihre Protokolle werden heruntergeladen und analysiert, und Sie können die vorab erstellten visuellen Elemente nutzen.

## <a name="understanding-the-visuals"></a>Grundlegendes zu den visuellen Elementen

In der Vorlage ist eine Gruppe von visuellen Elementen angegeben, die dazu dienen, die Daten der NSG-Flowprotokollierung verständlich zu machen. Die folgenden Abbildungen zeigen ein Beispiel dafür, wie das Dashboard aussieht, wenn es mit Daten gefüllt wird. Im Folgenden wird jedes visuelle Element ausführlicher beschrieben. 

![Power BI][5]
 
Mit dem visuellen Element „Top Talkers“ werden die IP-Adressen dargestellt, die im angegebenen Zeitraum die meisten Verbindungen initiiert haben. Die Größe der Felder entspricht der relativen Anzahl von Verbindungen. 

![Top Talkers][6]

In den folgenden Zeitseriengraphen ist die Anzahl von Datenflüssen für den Zeitraum dargestellt. Der obere Graph ist nach der Datenflussrichtung segmentiert, und der untere Graph ist nach der getroffenen Entscheidung (Zulassen oder Ablehnen) segmentiert. Mit diesem visuellen Element können Sie Ihre Datenverkehrstrends in Abhängigkeit der Zeit untersuchen und ungewöhnliche Spitzen oder Rückgänge des Datenverkehrs bzw. der Datenverkehrssegmentierung erkennen.

![Datenflüsse in Abhängigkeit des Zeitraums][7]

Die folgenden Graphen zeigen die Datenflüsse pro Netzwerkschnittstelle, wobei der obere nach der Flussrichtung und der untere nach der getroffenen Entscheidung segmentiert ist. Mit diesen Informationen können Sie ermitteln, welche VM im Vergleich mit den anderen VMs am meisten kommuniziert hat und ob der Datenverkehr zu einer bestimmten VM zulässig oder nicht zulässig ist.

![Datenflüsse pro NIC][8]

Im folgenden Ringdiagramm sind die Datenflüsse nach Zielport unterteilt angegeben. Mit diesen Informationen können Sie die am häufigsten verwendeten Zielports anzeigen, die im angegebenen Zeitraum verwendet wurden.

![Ring][9]

Im folgenden Balkendiagramm ist der Datenfluss nach NSG und Regel dargestellt. Mit diesen Informationen können Sie verfolgen, welche NSGs für den meisten Datenverkehr verantwortlich sind und wie sich der Datenverkehr auf einer NSG nach der Regel verteilt.

![Balkendiagramm][10]
 
In den folgenden Informationsdiagrammen werden Informationen zu den NSGs in den Protokollen, zur Anzahl von erfassten Datenflüssen im Zeitraum und das Datum des ältesten erfassten Protokolls angezeigt. Anhand dieser Informationen wissen Sie, welche NSGs protokolliert werden und in welchem Datumsbereich sich die Datenflüsse bewegen.

![Infodiagramm 1][11]

![Infodiagramm 2][12]

Diese Vorlage enthält die folgenden Slicer, damit Sie bei Bedarf nur die Daten anzeigen können, die für Sie am interessantesten sind. Sie können nach Ressourcengruppen, NSGs und Regeln filtern. Außerdem können Sie nach 5-Tupel-Informationen, der Entscheidung und dem Zeitpunkt der Protokollerstellung filtern.

![Slicer][13]

## <a name="conclusion"></a>Zusammenfassung

In diesem Szenario wurde veranschaulicht, dass es durch die Verwendung von Datenflussprotokollen von Netzwerksicherheitsgruppen, die per Network Watcher und Power BI bereitgestellt werden, möglich ist, den Datenverkehr zu visualisieren und zu verstehen. Mithilfe der bereitgestellten Vorlage lädt Power BI die Protokolle direkt aus dem Speicher herunter und verarbeitet sie lokal. Die Zeit, die zum Laden der Vorlage benötigt wird, variiert je nach Anzahl angeforderter Dateien und Gesamtgröße der heruntergeladenen Dateien.

Sie können diese Vorlage an Ihre Anforderungen anpassen. Es gibt viele verschiedene Möglichkeiten, wie Sie Power BI mit Datenflussprotokollen für Netzwerksicherheitsgruppen verwenden können. 

## <a name="notes"></a>Notizen

* Protokolle werden standardmäßig unter `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/` gespeichert.

    * Falls noch andere Daten in einem anderen Verzeichnis vorhanden sind, müssen die Abfragen zum Abrufen und Verarbeiten der Daten geändert werden.

* Es ist nicht zu empfehlen, die bereitgestellte Vorlage mit mehr als 1 GB an Protokollen zu verwenden.

* Wenn Sie über eine größere Menge von Protokollen verfügen, raten wir Ihnen, die Verwendung einer Lösung mit einem anderen Datenspeicher wie Data Lake oder SQL Server zu prüfen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Visualisieren von Azure Network Watcher-NSG-Datenflussprotokollen mit Open-Source-Tools](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) darüber, wie Sie Ihre NSG-Datenflussprotokolle per Elastic Stack visualisieren.

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
