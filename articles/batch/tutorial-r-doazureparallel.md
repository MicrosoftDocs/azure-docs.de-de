---
title: Parallele R-Simulation mit Azure Batch
description: Tutorial – Schritt-für-Schritt-Anleitung zur Ausführung einer Monte Carlo-Finanzsimulation in Azure Batch mit dem R-Paket doAzureParallel
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: a3f760f31bcd27c5f3d3289aa799d6886df17e15
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779974"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Tutorial: Ausführen einer parallelen R-Simulation mit Azure Batch 

Führen Sie Ihre parallelen R-Workloads bedarfsabhängig mit [doAzureParallel](https://www.github.com/Azure/doAzureParallel) aus. Hierbei handelt es sich um ein einfaches R-Paket, mit dem Sie Azure Batch direkt in Ihrer R-Sitzung nutzen können. Das Paket doAzureParallel basiert auf dem beliebten R-Paket [foreach](https://cran.r-project.org/web/packages/foreach/index.html). Mit doAzureParallel wird jede Iteration der foreach-Schleife an eine Azure Batch-Aufgabe übermittelt.

In diesem Tutorial wird veranschaulicht, wie Sie einen Batch-Pool bereitstellen und einen parallelen R-Auftrag in Azure Batch direkt in RStudio ausführen. Folgendes wird vermittelt:
 

> [!div class="checklist"]
> * Installieren von doAzureParallel und Konfigurieren des Tools für den Zugriff auf Ihre Batch- und Storage-Konten
> * Erstellen eines Batch-Pools als paralleles Back-End für Ihre R-Sitzung
> * Ausführen einer parallelen Beispielsimulation im Pool

## <a name="prerequisites"></a>Voraussetzungen

* Eine installierte [R](https://www.r-project.org/)-Distribution, z.B. [Microsoft R Open](https://mran.microsoft.com/open). Verwenden Sie die R-Version 3.3.1 oder höher.

* [RStudio](https://www.rstudio.com/), entweder die kommerzielle Edition oder die Open-Source-Version [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop). 

* Ein Azure Batch-Konto und ein Azure Storage-Konto. Informationen zur Erstellung dieser Konten finden Sie in den Batch-Schnellstartanleitungen zum [Azure-Portal](quick-create-portal.md) und zur [Azure CLI](quick-create-cli.md). 

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>Installieren von doAzureParallel

Installieren Sie in der RStudio-Konsole das [GitHub-Paket doAzureParallel](https://www.github.com/Azure/doAzureParallel). Mit den folgenden Befehlen werden das Paket und die dazugehörigen Abhängigkeiten in Ihre aktuelle R-Sitzung heruntergeladen und installiert: 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
Die Installation kann einige Minuten dauern.

Generieren Sie eine Konfigurationsdatei mit dem Namen *credentials.json* in Ihrem Arbeitsverzeichnis, um doAzureParallel mit den zuvor beschafften Anmeldeinformationen für das Konto zu konfigurieren: 

```R
generateCredentialsConfig("credentials.json") 
``` 

Fügen Sie in diese Datei die Namen und Schlüssel Ihrer Batch- und Storage-Konten ein. Lassen Sie die Einstellung `githubAuthenticationToken` unverändert.

Nach Abschluss des Vorgangs sieht die Datei mit den Anmeldeinformationen in etwa wie folgt aus: 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Speichern Sie die Datei . Führen Sie anschließend den folgenden Befehl aus, um die Anmeldeinformationen für Ihre aktuelle R-Sitzung festzulegen: 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Batch-Pool erstellen 

doAzureParallel enthält eine Funktion zum Generieren eines Azure Batch-Pools (Cluster) für die Ausführung von parallelen R-Aufträgen. Auf den Knoten wird ein Ubuntu-basierter [virtueller Computer für Azure Data Science](../machine-learning/data-science-virtual-machine/overview.md) ausgeführt. Microsoft R Open und beliebte R-Pakete sind auf diesem Image vorinstalliert. Sie können bestimmte Clustereinstellungen anzeigen oder anpassen, z.B. die Anzahl und Größe von Knoten. 

Gehen Sie wie folgt vor, um eine JSON-Datei für die Clusterkonfiguration in Ihrem Arbeitsverzeichnis zu generieren: 
 
```R
generateClusterConfig("cluster.json")
``` 
 
Öffnen Sie die Datei, um die Standardkonfiguration anzuzeigen, die drei dedizierte Knoten und drei Konten mit [niedriger Priorität](batch-low-pri-vms.md) umfasst. Diese Einstellungen sind nur Beispiele, mit denen Sie experimentieren oder die Sie ändern können. Dedizierte Knoten sind für Ihren Pool reserviert. Knoten mit niedriger Priorität werden zu einem reduzierten Preis basierend auf überschüssiger VM-Kapazität in Azure angeboten. Knoten mit niedriger Priorität sind nicht mehr verfügbar, wenn in Azure nicht genügend Kapazität vorhanden ist. 

Ändern Sie die Konfiguration für dieses Tutorial wie folgt:

* Erhöhen Sie `maxTasksPerNode` auf *2*, um auf jedem Knoten beide Kerne zu nutzen.
* Legen Sie `dedicatedNodes` auf *0* fest, um die für Batch verfügbaren VMs mit niedriger Priorität auszuprobieren. Legen Sie `min` für `lowPriorityNodes` auf *5* fest. Legen Sie `max` auf *10* fest, oder wählen Sie bei Bedarf niedrigere Werte. 

Behalten Sie für die verbleibenden Einstellungen die Standardwerte bei, und speichern Sie die Datei. Es sollte in etwa wie folgt aussehen:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Erstellen Sie jetzt den Cluster. Batch erstellt den Pool sofort, aber es dauert einige Minuten, bis die Computeknoten zugeordnet und gestartet wurden. Wenn der Cluster verfügbar ist, können Sie ihn als paralleles Back-End für Ihre R-Sitzung registrieren. 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

In der Ausgabe wird die Anzahl von „Ausführungsworkern“ für doAzureParallel angezeigt. Dies ist die Anzahl von Knoten multipliziert mit dem Wert von `maxTasksPerNode`. Wenn Sie die Clusterkonfiguration wie oben beschrieben geändert haben, beträgt die Anzahl *10*. 
 
## <a name="run-a-parallel-simulation"></a>Ausführen einer parallelen Simulation

Nachdem Ihr Cluster erstellt wurde, können Sie Ihre foreach-Schleife mit Ihrem registrierten parallelen Back-End (Azure Batch-Pool) ausführen. Führen Sie als Beispiel eine Monte Carlo-Finanzsimulation aus, indem Sie zuerst lokal eine foreach-Standardschleife verwenden und dann foreach mit Batch ausführen. Dieses Beispiel ist eine vereinfachte Version zum Vorhersagen eines Aktienkurses, indem eine große Zahl von unterschiedlichen Ergebnissen nach einem Zeitraum von 5 Jahren simuliert wird.

Angenommen, die Aktie der Contoso Corporation steigt jeden Tag durchschnittlich um das 1,001-fache ihres Anfangskurses und weist eine Volatilität (Standardabweichung) von 0,01 auf. Verwenden Sie für einen Anfangskurs von 100 US-Dollar eine Monte Carlo-Kurssimulation, um den Kurs der Contoso-Aktie nach Ablauf von fünf Jahren zu ermitteln.

Parameter für die Monte Carlo-Simulation:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

Definieren Sie die folgende Funktion, um Schlusskurse zu simulieren:

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

Führen Sie zuerst lokal 10.000 Simulationen aus, indem Sie eine foreach-Standardschleife mit dem Schlüsselwort `%do%` verwenden:

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


Plotten Sie die Schlusskurse in einem Histogramm, um die Ergebnisverteilung darzustellen:

```R
hist(closingPrices_s)
``` 

Die Ausgabe sieht in etwa wie folgt aus:

![Verteilung von Schlusskursen](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Die Durchführung einer lokalen Simulation dauert maximal einige Sekunden:

```R
difftime(end_s, start_s) 
```

Die geschätzte lokale Laufzeit für 10 Millionen Ergebnisse beträgt bei einer linearen Näherung ca. 30 Minuten:

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


Führen Sie den Code jetzt per `foreach` mit dem Schlüsselwort `%dopar%` aus, um zu vergleichen, wie lange es dauert, 10 Millionen Simulationen in Azure auszuführen. Führen Sie 100 Iterationen von 100.000 Simulationen aus, um die Simulation mit Batch zu parallelisieren:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

Die Simulation verteilt Aufgaben auf die Knoten im Batch-Pool. Sie können die Aktivität im Wärmebild für den Pool im Azure-Portal anzeigen. Navigieren Sie zu **Batch-Konten** > *myBatchAccount*. Klicken Sie auf **Pools** > *myPoolName*. 

![Wärmebild für einen Pool mit Ausführung von parallelen R-Aufgaben](media/tutorial-r-doazureparallel/pool.png)

Die Simulation ist nach wenigen Minuten abgeschlossen. Das Paket führt die Ergebnisse automatisch zusammen und ruft sie per Pullvorgang von den Knoten ab. Anschließend können Sie die Ergebnisse in Ihrer R-Sitzung verwenden. 

```R
hist(closingPrices_p) 
```

Die Ausgabe sieht in etwa wie folgt aus:

![Verteilung von Schlusskursen](media/tutorial-r-doazureparallel/closing-prices.png)

Wie lange hat die parallele Simulation gedauert? 

```R
difftime(end_p, start_p, unit = "min")  
```

Es sollte erkennbar sein, dass Sie beim Ausführen der Simulation im Batch-Pool eine erhebliche Leistungssteigerung im Vergleich zur erwarteten Dauer für die lokale Ausführung der Simulation erzielen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Der Auftrag wird automatisch gelöscht, nachdem er abgeschlossen wurde. Wenn der Cluster nicht mehr benötigt wird, können Sie die Funktion `stopCluster` im doAzureParallel-Paket aufrufen, um ihn zu löschen:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> Installieren von doAzureParallel und Konfigurieren des Tools für den Zugriff auf Ihre Batch- und Storage-Konten
> * Erstellen eines Batch-Pools als paralleles Back-End für Ihre R-Sitzung
> * Ausführen einer parallelen Beispielsimulation im Pool


Weitere Informationen zu doAzureParallel finden Sie in der Dokumentation und in den Beispielen auf GitHub.

> [!div class="nextstepaction"]
> [Paket doAzureParallel](https://github.com/Azure/doAzureParallel/)




