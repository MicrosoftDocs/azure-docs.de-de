---
title: Hinzufügen von Überwachung und Diagnose zu einem virtuellen Azure-Computer
description: Verwenden Sie eine Azure Resource Manager-Vorlage, um einen neuen virtuellen Windows-Computer mit Azure-Diagnoseerweiterung zu erstellen.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: mimckitt
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 181f226a4d7aa37ffd8c667db4736a96450e2be5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955955"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Verwenden von Überwachung und Diagnose bei einer Windows-VM und Azure Resource Manager-Vorlagen
Die Azure-Diagnoseerweiterung stellt Überwachungs- und Diagnosefunktionen auf einem Windows-basierten virtuellen Azure-Computer bereit. Sie können diese Funktionen auf dem virtuellen Computer nutzen, indem Sie die Erweiterung in die Azure Resource Manager-Vorlage einbinden. Weitere Informationen zum Einbinden von Erweiterungen in eine Vorlage für virtuelle Computer finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen mit VM-Erweiterungen](../windows/template-description.md#extensions) . Dieser Artikel beschreibt das Hinzufügen der Azure-Diagnoseerweiterung zu einer Vorlage für virtuelle Windows-Computer.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Hinzufügen der Azure-Diagnoseerweiterung zur VM-Ressourcendefinition
Um die Diagnoseerweiterung auf einem virtuellen Windows-Computer zu aktivieren, müssen Sie die Erweiterung der Resource Manager-Vorlage als VM-Ressource hinzufügen.

Bei einem einfachen, auf Ressourcen-Manager basierenden virtuellen Computer fügen Sie die Erweiterungskonfiguration dem Array *resources* für den virtuellen Computer hinzu. 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Eine weitere gängige Konvention ist es, die Erweiterungskonfiguration im Stammressourcenknoten der Vorlage hinzuzufügen, statt sie unterhalb des Ressourcenknotens des virtuellen Computers zu definieren. Bei diesem Ansatz müssen Sie mithilfe der Werte *name* und *type* explizit eine hierarchische Beziehung zwischen der Erweiterung und dem virtuellen Computer angeben. Beispiel: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Die Erweiterung ist immer dem virtuellen Computer zugeordnet. Sie können diese direkt unterhalb des Ressourcenknotens des virtuellen Computers definieren. Alternativ dazu können Sie sie auch auf der Basisebene definieren und die hierarchische Benennungskonvention verwenden, um sie dem virtuellen Computer zuzuordnen.

Bei VM-Skalierungsgruppen wird die Erweiterungskonfiguration in der *extensionProfile*-Eigenschaft von *VirtualMachineProfile* angegeben.

Die *publisher*-Eigenschaft mit dem Wert **Microsoft.Azure.Diagnostics** und die *type*-Eigenschaft mit dem Wert **IaaSDiagnostics** definieren die Azure-Diagnoseerweiterung eindeutig.

Der Wert der *name* -Eigenschaft kann zum Verweisen auf die Erweiterung in der Ressourcengruppe verwendet werden. Wenn Sie diese Eigenschaft auf **Microsoft.Insights.VMDiagnosticsSettings** festlegen, kann sie problemlos im Azure-Portal identifiziert werden – so wird sichergestellt, dass die Überwachungsdiagramme im Azure-Portal richtig angezeigt werden.

Die Eigenschaft *typeHandlerVersion* gibt die Version der Erweiterung an, die Sie verwenden möchten. Indem Sie *autoUpgradeMinorVersion* auf **true** festlegen, stellen Sie sicher, dass Sie die neueste verfügbare Nebenversion der Erweiterung erhalten. Es wird dringend empfohlen, *autoUpgradeMinorVersion* immer auf **true** festzulegen, damit Sie jederzeit die neueste verfügbare Diagnoseerweiterung mit allen neuen Funktionen und Bugfixes verwenden. 

Das Element *settings* enhält Konfigurationseigenschaften für die Erweiterung, die festgelegt und in die Erweiterung eingelesen werden können (wird zuweilen als „öffentliche Konfiguration“ bezeichnet). Die Eigenschaft *xmlcfg* enthält die XML-basierte Konfiguration für die Diagnoseprotokolle, Leistungsindikatoren usw., die vom Diagnose-Agent gesammelt werden. Weitere Informationen zu dem XML-Schema finden Sie unter [Diagnosekonfigurationsschema](../../azure-monitor/platform/diagnostics-extension-schema-windows.md) . Üblicherweise wird die tatsächliche XML-Konfiguration als Variable in der Azure-Ressourcen-Manager-Vorlage gespeichert und dann verkettet und mit Base64 codiert, um den Wert für *xmlcfg* festzulegen. Informationen zum Speichern der XML-Konfiguration in Variablen finden Sie im Abschnitt zu [Diagnosekonfigurationsvariablen](#diagnostics-configuration-variables) . Die *storageAccount*-Eigenschaft gibt den Namen des Speicherkontos an, in das die Diagnosedaten übertragen werden. 

Die Eigenschaften in *protectedSettings* (zuweilen als „private Konfiguration“ bezeichnet) können festgelegt, danach jedoch nicht eingelesen werden. Da *protectedSettings* schreibgeschützt ist, eignet sich diese Eigenschaft sehr gut zum Speichern von Geheimnissen wie etwa des Schlüssels für das Speicherkonto, in das die Diagnosedaten geschrieben werden.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Angeben des Diagnosespeicherkontos als Parameter
Der oben stehende JSON-Codeausschnitt für die Diagnoseerweiterung setzt das Vorhandensein der beiden Parameter *existingdiagnosticsStorageAccountName* und *existingdiagnosticsStorageResourceGroup* voraus, um das Diagnosespeicherkonto anzugeben, in dem Diagnosedaten gespeichert werden. Wenn Sie das Diagnosespeicherkonto als Parameter angeben, lässt es sich für verschiedene Umgebungen ganz einfach ändern – so können Sie z.B. ein Konto zum Testen und ein anderes für Ihre Produktionsbereitstellung verwenden.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Es empfiehlt sich, ein Diagnosespeicherkonto in einer anderen Ressourcengruppe als der Ressourcengruppe für den virtuellen Computer anzugeben. Eine Ressourcengruppe kann als Bereitstellungseinheit mit eigener Lebensdauer angesehen werden. Ein virtueller Computer kann bereitgestellt und bei Konfigurationsupdates erneut bereitgestellt werden. Möglicherweise möchten Sie die Diagnosedaten jedoch für alle VM-Bereitstellungen weiterhin im gleichen Speicherkonto speichern. Indem Sie das Speicherkonto in einer anderen Ressourcengruppe bereitstellen, können Daten aus verschiedenen VM-Bereitstellungen in diesem Konto gespeichert werden, und die Behandlung von Problemen über die verschiedenen Versionen hinweg wird vereinfacht.

> [!NOTE]
> Wenn Sie eine Vorlage für einen virtuellen Windows-Computer in Visual Studio erstellen, kann das standardmäßige Speicherkonto so eingerichtet werden, dass es das gleiche Speicherkonto verwendet, in das die VHD des virtuellen Computers hochgeladen wurde. Dadurch wird die anfängliche Einrichtung des virtuellen Computers vereinfacht. Bearbeiten Sie die Vorlage, um ein anderes Speicherkonto anzugeben, das als Parameter übergeben werden kann. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Diagnosekonfigurationsvariablen
Der oben angegebene JSON-Codeausschnitt für die Diagnoseerweiterung definiert eine Variable vom Typ *accountid*, um das Abrufen des Speicherkontoschlüssels für den Diagnosespeicher zu vereinfachen:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Die *xmlcfg* -Eigenschaft für die Diagnoseerweiterung wird mithilfe von mehreren miteinander verketteten Variablen definiert. Die Werte dieser Variablen liegen im XML-Format vor und müssen daher beim Festlegen der JSON-Variablen ordnungsgemäß mit Escapezeichen versehen werden.

Das folgende Beispiel veranschaulicht den XML-Code für die Diagnosekonfiguration, der standardmäßige Leistungsindikatoren auf Systemebene sowie einige Windows-Ereignisprotokolle und Diagnoseinfrastrukturprotokolle erfasst. Der Code wurde mit Escapezeichen versehen und ordnungsgemäß formatiert, sodass die Konfiguration direkt in den Variablenabschnitt Ihrer Vorlage kopiert werden kann. Ein visuell besser lesbares Beispiel einer XML-Konfigurationsdatei finden Sie unter [Diagnosekonfigurationsschema](../../azure-monitor/platform/diagnostics-extension-schema-windows.md) .

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Der XML-Knoten für die Metrikdefinition in der oben stehenden Konfiguration ist ein wichtiges Konfigurationselement, da er festlegt, wie die weiter oben im XML-Code im Knoten *PerformanceCounter* definierten Leistungsindikatoren aggregiert und gespeichert werden. 

> [!IMPORTANT]
> Diese Metriken stellen die Grundlage für die Überwachungsdiagramme und Warnungen im Azure-Portal dar.  Der Knoten **Metriken** muss mit *resourceID* und **MetricAggregation** in der Diagnosekonfiguration für den virtuellen Computer enthalten sein, wenn Sie die Überwachungsdaten für den virtuellen Computer im Azure-Portal anzeigen möchten. 
> 
> 

Das folgende Beispiel zeigt den XML-Code für Metrikdefinitionen: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Das *resourceID*-Attribut wird verwendet, um den virtuellen Computer in Ihrem Abonnement eindeutig zu identifizieren. Verwenden Sie unbedingt die Funktionen „subscription()“ und „resourceGroup()“, damit die Vorlage diese Werte auf der Grundlage des Abonnements und der Ressourcengruppe für die Bereitstellung automatisch aktualisiert.

Wenn Sie mehrere virtuelle Computer in einer Schleife erstellen, müssen Sie den *resourceID*-Wert mit einer copyIndex()-Funktion auffüllen, um die einzelnen VMs voneinander zu unterscheiden. Der *xmlCfg* -Wert kann folgendermaßen aktualisiert werden, um diese Funktionalität zu unterstützen:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Die MetricAggregation-Werte *PT1M* und *PT1H* weisen auf eine Aggregierung während einer Minute bzw. eine Aggregierung während einer Stunde hin.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics-Tabellen im Speicher
Mit der oben angegebenen Metrikkonfiguration werden Tabellen mit den folgenden Benennungskonventionen in Ihrem Diagnosespeicherkonto erstellt:

* **WADMetrics**: Standardpräfix für alle WADMetrics-Tabellen
* **PT1H** oder **PT1M**: Weist darauf hin, dass die Tabelle Daten enthält, die im Verlauf einer Stunde bzw. einer Minute aggregiert wurden.
* **P10D**: Gibt an, dass die Tabelle Daten für einen Zeitraum von 10 Tagen ab dem Tag enthält, an dem die Tabelle mit dem Sammeln von Daten begonnen hat.
* **V2S**: Zeichenfolgenkonstante.
* **jjjjmmtt**: Das Datum, an dem die Tabelle mit dem Sammeln von Daten begonnen hat.

Beispiel: *WADMetricsPT1HP10DV2S20151108* enthält Metrikdaten, die ab dem 11. November 2015 zehn Tage lang gesammelt und innerhalb einer Stunde aggregiert wurden.    

Jede WADMetrics-Tabelle enthält die folgenden Spalten:

* **PartitionKey**: Der Partitionsschlüssel wird basierend auf dem *resourceID*-Wert gebildet, um die VM-Ressource eindeutig zu identifizieren. Beispiel: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Weist das Format `<Descending time tick>:<Performance Counter Name>` auf. Zur Berechnung der absteigenden Zeiteinheiten werden die maximalen Zeiteinheiten abzüglich der Anfangszeit des Aggregationszeitraums herangezogen. Wenn der Stichprobenzeitraum beispielsweise am 10. November 2015 um 00:00 (UTC) startet, lautet die Berechnung: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Für den Leistungsindikator zum verfügbaren Arbeitsspeicher in Bytes sieht der Zeilenschlüssel so aus: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`.
* **CounterName**: Ist der Name des Leistungsindikators. Entspricht dem in der XML-Konfiguration definierten *counterSpecifier* -Wert.
* **Maximum**: Der Höchstwert des Leistungsindikators während des Aggregationszeitraums.
* **Minimum**: Der Minimalwert des Leistungsindikators während des Aggregationszeitraums.
* **Total**: Die Summe aller Werte des Leistungsindikators, die während des Aggregationszeitraums gemeldet wurden.
* **Count**: Die Gesamtzahl der Werte, die für den Leistungsindikator gemeldet wurden.
* **Average**: Der Durchschnittswert (Total/Count) des Leistungsindikators während des Aggregationszeitraums.

## <a name="next-steps"></a>Nächste Schritte
* Eine vollständige Beispielvorlage für einen virtuellen Windows-Computer mit Diagnoseerweiterung finden Sie unter [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension).   
* Stellen Sie die Azure Resource Manager-Vorlage über [Azure PowerShell](../windows/ps-template.md) oder die [Azure-Befehlszeile](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bereit.
* Weitere Informationen zum [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md)