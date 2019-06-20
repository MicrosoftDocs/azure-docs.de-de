---
title: 'Tutorial: Verwenden der Azure Batch-Clientbibliothek für Node.js | Microsoft-Dokumentation'
description: Erfahren Sie mehr über die grundlegenden Konzepte von Azure Batch, und erstellen Sie eine einfache Lösung mit Node.js.
services: batch
author: shwetams
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: nodejs
ms.topic: conceptual
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: shwetams
ms.openlocfilehash: bb0bfa5eac3dd9031718fb12f270f5fc03bbaea6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775229"
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Erste Schritte mit dem Batch SDK für Node.js

Hier werden die Grundlagen für die Erstellung eines Batch-Clients in Node.js mit dem [Node.js SDK für Azure Batch](/javascript/api/overview/azure/batch) vermittelt. Zum besseren Verständnis gehen wir ein Szenario für eine Batch-Anwendung Schritt für Schritt durch und richten sie dann mithilfe eines Node.js-Clients ein.  

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie über Node.js-Kenntnisse verfügen und mit Linux vertraut sind. Außerdem wird davon ausgegangen, dass Sie über ein Azure-Konto mit Zugriffsrechten für die Erstellung von Batches und Speicherdiensten verfügen.

Sehen Sie sich am besten zuerst die [technische Übersicht zu Azure Batch](batch-technical-overview.md) an, bevor Sie sich mit den Schritten in diesem Artikel beschäftigen.

## <a name="the-tutorial-scenario"></a>Das Tutorialszenario
Das Batch-Workflowszenario sieht wie folgt aus: Wir haben ein einfaches, in Python geschriebenes Skript, das alle CSV-Dateien aus einem Azure Blob Storage-Container herunterlädt und sie in das JSON-Format konvertiert. Zur parallelen Verarbeitung mehrerer Speicherkontocontainer stellen wir das Skript als Azure Batch-Auftrag bereit.

## <a name="azure-batch-architecture"></a>Azure Batch-Architektur
Das folgende Diagramm zeigt, wie sich das Python-Skript mit Azure Batch und einem Node.js-Client skalieren lässt.

![Azure Batch-Szenario](./media/batch-nodejs-get-started/BatchScenario.png)

Der Node.js-Client stellt einen Batchauftrag mit einer Vorbereitungsaufgabe (wird weiter unten noch ausführlicher erläutert) und einer Reihe von Aufgaben (abhängig von der Containeranzahl im Speicherkonto) bereit. Die Skripts können aus dem GitHub-Repository heruntergeladen werden.

* [Node.js-Client](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/nodejs_batch_client_sample.js)
* [Shellskripts für die Vorbereitungsaufgabe](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/startup_prereq.sh)
* [Python-Prozessor für die Konvertierung von CSV in JSON](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/processcsv.py)

> [!TIP]
> Der Node.js-Client unter dem angegebenen Link enthält keinen spezifischen Code für die Bereitstellung als Azure-Funktionen-App. Erstellungsanweisungen für eine solche App finden Sie unter folgenden Links:
> - [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-create-first-azure-function.md)
> - [Planen der Ausführung von Code mit Azure Functions](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>Erstellen der Anwendung

Sehen wir uns nun Schritt für Schritt den Prozess zur Erstellung des Node.js-Clients an:

### <a name="step-1-install-azure-batch-sdk"></a>Schritt 1: Installieren des Azure Batch SDK

Das Azure Batch SDK für Node.js kann mithilfe des Befehls „npm install“ installiert werden.

`npm install azure-batch`

Dieser Befehl installiert die neueste Version des Azure Batch SDKs für Node.js.

>[!Tip]
> In einer Azure-Funktionen-App können auf der Registerkarte „Einstellungen“ der Azure-Funktion zur Kudu-Konsole navigieren, um Befehle vom Typ „npm install“ auszuführen. In diesem Fall installieren Sie so das Azure Batch SDK für Node.js.
>
>

### <a name="step-2-create-an-azure-batch-account"></a>Schritt 2: Erstellen eines Azure Batch-Kontos

Ein Azure Batch-Konto kann über das [Azure-Portal](batch-account-create-portal.md) oder über die Befehlszeile ([PowerShell](batch-powershell-cmdlets-get-started.md) /[Azure-Befehlszeilenschnittstelle](/cli/azure)) erstellt werden.

Im Anschluss finden Sie die Befehle für die Erstellung über die Azure-Befehlszeilenschnittstelle.

Erstellen Sie eine Ressourcengruppe. (Falls Sie bereits über eine Ressourcengruppe verfügen, in der Sie das Batch-Konto erstellen möchten, können Sie diesen Schritt überspringen.)

`az group create -n "<resource-group-name>" -l "<location>"`

Erstellen Sie als Nächstes ein Azure Batch-Konto.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Jedes Batch-Konto verfügt über entsprechende Zugriffsschlüssel. Diese Schlüssel werden für die Erstellung weiterer Ressourcen im Azure Batch-Konto benötigt. In Produktionsumgebungen empfiehlt es sich, diese Schlüssel mit Azure Key Vault zu speichern. Anschließend können Sie einen Dienstprinzipal für die Anwendung erstellen. Mit diesem Dienstprinzipal kann die Anwendung ein OAuth-Token für den Zugriff auf Schlüssel aus dem Schlüsseltresor erstellen.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Kopieren Sie den Schlüssel, und speichern Sie ihn für die späteren Schritte.

### <a name="step-3-create-an-azure-batch-service-client"></a>Schritt 3: Erstellen eines Azure Batch-Dienstclients
Der folgende Codeausschnitt importiert zunächst das Node.js-Modul „azure-batch“ und erstellt anschließend einen Batch-Dienstclient. Sie müssen zuerst ein SharedKeyCredentials-Objekt mit dem Batch-Kontoschlüssel erstellen, den Sie im vorherigen Schritt kopiert haben.

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Den Azure Batch-URI finden Sie auf der Registerkarte „Übersicht“ des Azure-Portals. Er liegt im folgenden Format vor:

`https://accountname.location.batch.azure.com`

Screenshot:

![Azure Batch-URI](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>Schritt 4: Erstellen eines Azure Batch-Pools
Ein Azure Batch-Pool besteht aus mehreren virtuellen Computern (auch Batch-Knoten genannt). Der Azure Batch-Dienst stellt die Aufgaben auf diesen Knoten bereit und verwaltet sie. Sie können für Ihren Pool folgende Konfigurationsparameter definieren:

* Art des VM-Images
* Größe des VM-Images
* Anzahl von VM-Knoten

> [!Tip]
> Größe und Anzahl der VM-Knoten hängen größtenteils von der Anzahl von Aufgaben ab, die parallel ausgeführt werden sollen, sowie von der Aufgabe selbst. Die optimale Anzahl und Größe können Sie mithilfe von Tests ermitteln.
>
>

Der folgende Codeausschnitt erstellt die Konfigurationsparameterobjekte:

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> Eine Liste mit den für Azure Batch verfügbaren Linux-VM-Images und ihren SKU-IDs finden Sie unter [Liste mit VM-Images](batch-linux-nodes.md#list-of-virtual-machine-images).
>
>

Nach dem Definieren der Poolkonfiguration können Sie den Azure Batch-Pool erstellen. Der Batch-Poolbefehl erstellt die Azure-VM-Knoten und bereitet sie für den Empfang auszuführender Aufgaben vor. Jeder Pool muss über eine eindeutige ID verfügen, damit in späteren Schritten auf ihn verwiesen werden kann.

Der folgende Codeausschnitt erstellt einen Azure Batch-Pool:

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Sie können den Status des erstellten Pools überprüfen und sich vergewissern, dass er sich im Zustand „Aktiv“ befindet, bevor Sie einen Auftrag an diesen Pool übermitteln.

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");    

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Im Anschluss sehen Sie ein Beispielergebnisobjekt, das von der Funktion „pool.get“ zurückgegeben wurde:

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>Schritt 4: Übermitteln eines Azure Batch-Auftrags
Ein Azure Batch-Auftrag ist eine logische Gruppe ähnlicher Aufgaben. In diesem Szenario handelt es sich dabei um die Aufgabe „CSV in JSON konvertieren“. Jede dieser Aufgabe kann CSV-Dateien aus den einzelnen Azure Storage-Containern verarbeiten.

Diese Aufgaben werden parallel ausgeführt, über mehrere Knoten hinweg bereitgestellt und vom Azure Batch-Dienst koordiniert.

> [!Tip]
> Mit der Eigenschaft [maxTasksPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) können Sie die maximale Anzahl von Aufgaben angeben, die auf einem einzelnen Knoten gleichzeitig ausgeführt werden können.
>
>

#### <a name="preparation-task"></a>Vorbereitungsaufgabe

Bei den erstellten VM-Knoten handelt es sich um leere Ubuntu-Knoten. Oftmals müssen zur Vorbereitung bestimmte Programme installiert werden.
Für Linux-Knoten können Sie in der Regel ein Shellskript verwenden, das vor der Ausführung der eigentlichen Aufgaben die erforderlichen Komponenten installiert. Sie können jedoch auch eine andere programmierbare ausführbare Datei verwenden.
Das [Shellskript](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) in diesem Beispiel installiert „python-pip“ und das Azure Storage SDK für Python.

Sie können das Skript in ein Azure Storage-Konto hochladen und einen SAS-URI für den Zugriff auf das Skript generieren. Dieser Prozess kann mit dem Azure Storage SDK für Node.js auch automatisiert werden.

> [!Tip]
> Eine Vorbereitungsaufgabe für einen Auftrag wird nur auf den VM-Knoten ausgeführt, auf denen diese spezielle Aufgabe ausgeführt werden muss. Falls erforderliche Komponenten unabhängig von den ausgeführten Aufgaben auf allen Knoten installiert werden sollen, können Sie beim Hinzufügen eines Pools die Eigenschaft [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) verwenden. Verwenden Sie bei Bedarf die folgende Vorbereitungsaufgabendefinition als Referenz.
>
>

Eine Vorbereitungsaufgabe wird im Rahmen der Azure Batch-Auftragsübermittlung angegeben. Für die Vorbereitungsaufgabe stehen folgende Konfigurationsparameter zur Verfügung:

* **ID**: Ein eindeutiger Bezeichner für die Vorbereitungsaufgabe.
* **commandLine**: Befehlszeile zum Ausführen der ausführbaren Aufgabendatei.
* **resourceFiles**: Array mit Objekten, die Details zu Dateien angeben, welche zum Ausführen dieser Aufgabe heruntergeladen werden müssen.  Verfügbare Optionen:
    - blobSource: Der SAS-URI der Datei.
    - filePath: Lokaler Pfad zum Herunterladen und Speichern der Datei.
    - fileMode: Nur relevant für Linux-Knoten. „fileMode“ wird im Oktalformat mit dem Standardwert „0770“ angegeben.
* **waitForSuccess**: Wird dieser Parameter auf „true“ festgelegt, wird die Aufgabe nicht ausgeführt, wenn bei der Vorbereitungsaufgabe ein Fehler auftritt.
* **runElevated**: Legen Sie diesen Parameter auf „true“ fest, falls für die Aufgabenausführung erhöhte Rechte erforderlich sind.

Der folgende Codeausschnitt zeigt das Skriptkonfigurationsbeispiel für die Vorbereitungsaufgabe:

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Wenn zur Ausführung Ihrer Aufgaben keine erforderlichen Komponenten installiert werden müssen, können Sie die Vorbereitungsaufgaben überspringen. Der folgende Code erstellt einen Auftrag mit dem Anzeigenamen „process csv files“:

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```


### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Schritt 5: Übermitteln von Azure Batch-Aufgaben für einen Auftrag

Nach der Erstellung des CSV-Verarbeitungsauftrags können wir nun Aufgaben für diesen Auftrag erstellen. Wenn wir beispielsweise über vier Container verfügen, müssen wir vier Aufgaben erstellen (jeweils eine pro Container).

Das [Python-Skript](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py) akzeptiert zwei Parameter:

* container_name: Der Storage-Container zum Herunterladen von Dateien.
* pattern: Ein optionaler Parameter für das Dateinamensmuster.

Der folgende Code zeigt die Übermittlung von Aufgaben an den zuvor erstellten Azure Batch-Auftrag „process csv“ für vier Container („con1“, „con2“, „con3“ und „con4“):

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){           

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);     
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

Der Code fügt dem Pool mehrere Aufgaben hinzu. Jede dieser Aufgaben wird auf einem Knoten im Pool mit erstellten virtuellen Computern ausgeführt. Falls die Anzahl von Aufgaben die Anzahl virtueller Computer in einem Pool oder den Wert der Eigenschaft „maxTasksPerNode“ übersteigt, wird gewartet, bis ein Knoten verfügbar gemacht wird. Diese Orchestrierung wird von Azure Batch automatisch durchgeführt.

Im Portal stehen detaillierte Ansichten für den Aufgaben- und Auftragsstatus zur Verfügung. Sie können aber auch die list- und get-Funktionen im Azure Node SDK verwenden. Ausführliche Informationen finden Sie in der [Dokumentation](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html).

## <a name="next-steps"></a>Nächste Schritte

- Falls Sie noch keine Erfahrung mit dem Dienst haben, helfen Ihnen die Informationen im Artikel [Übersicht über Azure Batch-Features](batch-api-basics.md) weiter.
- Informationen zur Batch-API finden Sie in der [Batch-Referenz für Node.js](/javascript/api/overview/azure/batch).

