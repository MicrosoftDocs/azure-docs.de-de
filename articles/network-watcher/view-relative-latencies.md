---
title: Anzeigen der relativen Latenz für Azure-Regionen von bestimmten Standorten aus | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie relative Latenzen zwischen Internetanbietern und Azure-Regionen von bestimmten Standorten aus anzeigen können.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: b4a50657442422786f49c931aa6c2610d49846b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939876"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Anzeigen der relativen Latenz für Azure-Regionen von bestimmten Standorten aus

In diesem Tutorial erfahren Sie, wie Sie den Dienst [Azure Network Watcher](network-watcher-monitoring-overview.md) verwenden, um zu entscheiden, in welcher Azure-Region Sie Ihre Anwendung oder Ihren Dienst basierend auf Ihrer demographischen Benutzerstruktur bereitstellen sollten. Darüber hinaus können Sie den Dienst nutzen, um Verbindungen von Dienstanbietern mit Azure zu bewerten.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Erstellen einer Komponente zur Netzwerküberwachung

Wenn Sie bereits über eine Komponente zur Netzwerküberwachung in mindestens einer Azure-[Region](https://azure.microsoft.com/regions) verfügen, können Sie die Aufgaben in diesem Abschnitt überspringen. Erstellen Sie für die Komponente zur Netzwerküberwachung eine Ressourcengruppe. In diesem Beispiel wird die Ressourcengruppe in der Region „USA, Osten“ erstellt, doch Sie können sie auch in einer anderen Azure-Region anlegen.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Erstellen Sie eine Komponente zur Netzwerküberwachung. Sie müssen in mindestens einer Azure-Region eine Komponente zur Netzwerküberwachung erstellen. In diesem Beispiel wird eine Komponente zur Netzwerküberwachung in der Region „USA, Osten“ erstellt.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Vergleichen der relativen Netzwerklatenzen mit einer einzelnen Azure-Region von einem bestimmten Standort aus

Bewerten Sie Dienstanbieter, oder beheben Sie Fehler bei einem Benutzer, der ein Problem wie „Die Website war langsam“ meldet, von einem bestimmten Ort aus für eine Azure-Region, in der ein Dienst bereitgestellt wurde. Der folgende Befehl gibt beispielsweise die durchschnittlichen relativen Latenzen von Internetdienstanbietern zwischen dem US-Bundesstaat Washington und der Azure-Region „USA, Westen 2“ vom 13. bis 15. Dezember 2017 zurück:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Die Region, die Sie im vorherigen Befehl angeben, muss nicht mit der Region übereinstimmen, die Sie beim Abrufen der Komponente zur Netzwerküberwachung angegeben haben. Der vorherige Befehl erfordert lediglich, dass Sie eine vorhandene Komponente zur Netzwerküberwachung angeben. Die Komponente zur Netzwerküberwachung kann sich in einer beliebigen Region befinden. Für `-Country` und `-State` müssen gültige Werte angegeben werden. Bei den Werten wird Groß- und Kleinschreibung unterschieden. Daten sind für eine begrenzte Anzahl von Ländern/Regionen, US-Bundesstaaten und Städten verfügbar. Führen Sie die Befehle unter [Anzeigen verfügbarer Länder/Regionen, US-Bundesstaaten, Städte und Anbieter](#view-available) zum Anzeigen einer Liste verfügbarer Länder/Regionen, Städte und US-Bundesstaaten für die Verwendung mit dem vorherigen Befehl aus. 

> [!WARNING]
> Sie müssen für `-StartTime` und `-EndTime` ein Datum angeben, das maximal 30 Tage zurückliegt. Wenn Sie ein Datum angeben, das weiter zurückliegt, werden keine Daten zurückgegeben.

Die Ausgabe des vorherigen Befehls ist wie folgt:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

In der zurückgegebene Ausgabe ist der Wert für **Score** die relative Latenz verschiedener Regionen und Anbieter. Der Wert 1 steht für die höchste Latenz, der Wert 100 für die niedrigste. Die relativen Latenzen werden für den Tag gemittelt. Das vorige Beispiel zeigt zwar klar, dass die Latenzen an beiden Tagen gleich waren und dass es einen kleinen Unterschied zwischen den Latenzen der beiden Anbieter gibt. Es zeigt aber auch eindeutig, dass die Latenzen für beide Anbieter auf der Skala von 1-100 niedrig sind. Zwar ist dies zu erwarten, da der US-Bundesstaat Washington sich nahe der Azure-Region „USA, Westen 2“ befindet, doch mitunter sind die Ergebnisse nicht wie erwartet. Je größer der von Ihnen angegebene Datumsbereich ist, desto besser können Sie die durchschnittliche Latenzzeit im gesamten Zeitraum berechnen.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Vergleichen der relativen Netzwerklatenzen in Azure-Regionen von einem bestimmten Standort aus

Wenn Sie, anstatt die relativen Latenzen zwischen einem bestimmten Ort und einer bestimmten Azure-Region mit `-Location` anzugeben, die relativen Latenzen für alle Azure-Regionen von einem bestimmten Ort aus bestimmen möchten, können Sie dies auch tun. Zum Beispiel hilft Ihnen der folgende Befehl zu beurteilen, in welcher Azure-Region Sie einen Dienst bereitstellen können, wenn Ihre primären Benutzer Comcast-Benutzer im US-Bundesstaat Washington sind:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Anders als bei Angabe eines einzelnen Standorts müssen Sie, wenn Sie keinen Standort oder mehrere Standorte angeben, wie z.B. „USA, Westen 2“, „USA, Westen“, beim Ausführen des Befehls einen Internetdienstanbieter angeben. 

## <a name="view-available"></a>Anzeigen verfügbarer Länder/Regionen, US-Bundesstaaten, Städte und Anbieter

Daten sind für bestimmte Internetdienstanbieter, Länder/Regionen, US-Bundesstaaten und Städte verfügbar. Um eine Liste aller verfügbaren Internetdienstanbieter, Länder/Regionen, US-Bundesstaaten und Städte anzuzeigen, für die Sie Daten anzeigen können, geben Sie den folgenden Befehl ein:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Daten sind nur für die Länder/Regionen, US-Bundesstaaten und Städte verfügbar, die vom vorherigen Befehl zurückgegeben werden. Der vorherige Befehl erfordert, dass Sie eine vorhandene Komponente zur Netzwerküberwachung angeben. Im Beispiel wird die Komponente zur Netzwerküberwachung *NetworkWatcher_eastus* in einer Ressourcengruppe namens *NetworkWatcherRG* angegeben. Sie können jedoch eine beliebige vorhandene Komponente zur Netzwerküberwachung angeben. Wenn Sie noch nicht über eine Komponente zur Netzwerküberwachung verfügen, erstellen Sie eine, indem Sie die Aufgaben unter [Erstellen einer Komponente zur Netzwerküberwachung](#create-a-network-watcher) ausführen. 

Nachdem Sie den vorherigen Befehl ausgeführt haben, können Sie die zurückgegebene Ausgabe nach Wunsch filtern, indem Sie gültige Werte für **Country**, **State** und **City** angeben.  Um beispielsweise die Liste der in Seattle, Washington, USA, verfügbaren Internetdienstanbieter anzuzeigen, geben Sie den folgenden Befehl ein:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Der angegebene Wert für **Country** muss aus Groß- und Kleinbuchstaben bestehen. Die Werte für **State** und **City** müssen aus Kleinbuchstaben bestehen. Die Werte müssen in der Ausgabe aufgeführt werden, die nach Ausführung des Befehls ohne Werte für **Country**, **State** und **City** zurückgegeben wird. Wenn Sie die falsche Groß-/Kleinschreibung wählen oder einen Wert für **Country**, **State** oder **City** angeben, der nicht in der Ausgabe enthalten ist, die nach Ausführung des Befehls ohne Werte für diese Eigenschaften zurückgegeben wird, ist die zurückgegebene Ausgabe leer.
