---
title: Azure Compute – Linux-Diagnoseerweiterung 3.0
description: Erfahren Sie, wie Sie die Linux-Diagnoseerweiterung 3.0 (Linux Diagnostic Extension, LAD) von Azure zum Erfassen von Metriken und Protokollieren von Ereignissen von in Azure ausgeführten virtuellen Linux-Computern konfigurieren.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 88f8d62a6321deae8a46d32441625c5960547694
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99982075"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Verwenden der Linux-Diagnoseerweiterung 3.0 zum Überwachen von Metriken und Protokollen

Dieses Dokument beschreibt Version 3.0 und höher der Linux-Diagnoseerweiterung.

> [!IMPORTANT]
> Weitere Informationen zu Version 2.3 und zu älteren Versionen finden Sie in [diesem Dokument](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Einführung

Mit der Linux-Diagnoseerweiterung können Benutzer die Integrität von Linux-VMs überwachen, die in Microsoft Azure ausgeführt werden. Sie verfügt über die folgenden Funktionen:

* Erfassen von Leistungsmetriken des Systems vom virtuellen Computer und Speichern dieser in einer bestimmten Tabelle in einem angegebenen Speicherkonto
* Abrufen von Protokollereignissen aus Syslog und Speichern dieser in einer bestimmten Tabelle im angegebenen Speicherkonto
* Anpassen der zu sammelnden und hochzuladenden Datenmetriken durch Benutzer
* Anpassen der Syslog-Funktionen und der Schweregrade von Ereignissen, die gesammelt und hochgeladen werden
* Hochladen angegebener Protokolldateien in eine festgelegte Speichertabelle durch Benutzer
* Senden von Metriken und Protokollereignissen an beliebige EventHub-Endpunkte und JSON-formatierte Blobs im angegebenen Speicherkonto

Diese Erweiterung funktioniert mit beiden Azure-Bereitstellungsmodellen.

## <a name="installing-the-extension-in-your-vm"></a>Installieren der Erweiterung auf Ihrem virtuellen Computer

Sie können diese Erweiterung über Azure PowerShell-Cmdlets, Azure-Befehlszeilenschnittstellenskripts, Azure Resource Manager-Vorlagen oder das Azure-Portal aktivieren. Weitere Informationen finden Sie unter [Erweiterungsfeatures](features-linux.md).

>[!NOTE]
>Einige Komponenten der VM-Erweiterung für Diagnosen werden auch mit der [Log Analytics-VM-Erweiterung](./oms-linux.md) geliefert. Aufgrund dieser Architektur können Konflikte auftreten, wenn beide Erweiterungen in derselben ARM-Vorlage instanziiert werden. Um solche Konflikte zur Installationszeit zu vermeiden, verwenden Sie die [`dependsOn`-Anweisung](../../azure-resource-manager/templates/define-resource-dependency.md#dependson), um sicherzustellen, dass die Erweiterungen nacheinander installiert werden. Die Erweiterungen können in beliebiger Reihenfolge installiert werden.

Diese Installationsanweisungen konfigurieren mithilfe einer [herunterladbare Beispielkonfiguration](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 3.0 für Folgendes:

* Erfassen und Speichern derselben Metriken wie in LAD 2.3
* Erfassen eines nützlichen Satzes von Dateisystemmetriken (neu in LAD 3.0)
* Erfassen der Syslog-Standardsammlung, die durch LAD 2.3 aktiviert wird
* Aktivieren von Diagrammen und Warnungen zu VM-Metriken in der Azure-Portal-Umgebung

Die herunterladbare Konfiguration ist nur ein Beispiel. Passen Sie sie an Ihre eigenen Bedürfnisse an.

### <a name="supported-linux-distributions"></a>Unterstützte Linux-Distributionen

Die Linux-Diagnoseerweiterung unterstützt die folgenden Distributionen und Versionen. Die Liste der Distributionen und Versionen gilt nur für von Azure unterstützte Images von Linux-Anbietern. BYOL- und BYOS-Images von Drittanbietern, wie Appliances, werden im Allgemeinen für die Linux-Diagnoseerweiterung nicht unterstützt.

Eine Distribution, die nur Hauptversionen auflistet, z. B. Debian 7, wird auch für alle Nebenversionen unterstützt. Wird eine bestimmte Nebenversion angegeben, wird nur diese angegebene Version unterstützt. Wenn "+" angefügt ist, werden Nebenversionen, die gleich oder höher als die angegebene Version sind, unterstützt.

Unterstützte Distributionen und Versionen:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6.7+

### <a name="prerequisites"></a>Voraussetzungen

* **Azure Linux Agent ab Version 2.2.0**. Die meisten Images des Azure-Katalogs für virtuelle Linux-Computer enthalten Version 2.2.7 oder höher. Führen Sie `/usr/sbin/waagent -version` aus, um die auf dem virtuellen Computer installierte Version zu überprüfen. Wenn der virtuelle Computer unter einer älteren Version des Gast-Agents ausgeführt wird, führen Sie [diese Anweisungen](./update-linux-agent.md) aus, um ihn zu aktualisieren.
* **Azure-Befehlszeilenschnittstelle**. [Richten Sie Azure CLI](/cli/azure/install-azure-cli) auf dem Computer ein.
* Der wget-Befehl, sofern Sie ihn noch nicht ausgeführt haben: Führen Sie `sudo apt-get install wget` aus.
* Ein vorhandenes Azure-Abonnement und ein vorhandenes universelles Speicherkonto zum Speichern der Daten.  Universelle Speicherkonten unterstützen den erforderlichen Tabellenspeicher.  Ein Blobspeicherkonto kann nicht verwendet werden.
* Python 2

### <a name="python-requirement"></a>Python-Anforderung

Die Linux-Diagnoseerweiterung erfordert Python 2. Wenn Ihr virtueller Computer eine Distribution verwendet, in der Python 2 nicht standardmäßig enthalten ist, müssen Sie die Sprache installieren. Mithilfe der folgenden Beispielbefehle wird Python 2 auf verschiedenen Distributionen installiert.    

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

Die ausführbare python2-Datei muss dem Alias *python* zugewiesen werden. Mit der folgenden Methode können Sie diesen Alias festlegen:

1. Führen Sie den folgenden Befehl aus, um eventuell vorhandene Aliase zu entfernen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Führen Sie den folgenden Befehl aus, um den Alias zu erstellen.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Beispielinstallation

> [!NOTE]
> Geben Sie vor der Ausführung bei beiden Beispielen im ersten Abschnitt die richtigen Werte für die Variablen ein. 

Die in diesen Beispielen heruntergeladene Beispielkonfiguration sammelt eine Reihe von Standarddaten und sendet diese an den Tabellenspeicher. Die URL für die Beispielkonfiguration und ihr Inhalt können geändert werden. In den meisten Fällen sollten Sie eine Kopie der JSON-Datei der Portaleinstellungen herunterladen und an Ihre Anforderungen anpassen. Verwenden Sie dann selbst erstellte Vorlagen oder Automation für Ihre Version der Konfigurationsdatei, anstatt diese URL jedes Mal herunterzuladen.

#### <a name="azure-cli-sample"></a>Azure CLI-Beispiel

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-virtual-machine-scale-set-instance"></a>Azure CLI-Beispiel für die Installation der LAD 3.0-Erweiterung in der VMSS-Instanz

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell-Beispiel

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Aktualisieren der Erweiterungseinstellungen

Nachdem Sie Ihre geschützten oder öffentlichen Einstellungen geändert haben, stellen Sie sie auf dem virtuellen Computer bereit, indem Sie denselben Befehl ausführen. Wenn Änderungen an den Einstellungen vorgenommen wurden, werden diese an die Erweiterung übermittelt. LAD lädt die Konfiguration erneut und startet sich selbst neu.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrieren von früheren Versionen der Erweiterung

Die neueste Version der Erweiterung ist **3.0**. **Ältere Versionen (2.x) gelten als veraltet und werden voraussichtlich ab dem 31. Juli 2018 nicht mehr unterstützt**.

> [!IMPORTANT]
> Diese Erweiterung enthält grundlegende Änderungen an der Konfiguration der Erweiterung. Eine dieser Änderungen betrifft die Verbesserung der Sicherheit der Erweiterung. Daher konnte keine Abwärtskompatibilität mit Version 2.x beibehalten werden. Darüber hinaus unterscheidet sich der Herausgeber dieser Erweiterung von dem für die Versionen 2.x.
>
> Zum Migrieren von 2.x zu dieser neuen Version der Erweiterung müssen Sie die alte Erweiterung (unter dem Namen des alten Herausgebers) deinstallieren und anschließend Version 3 der Erweiterung installieren.

Empfehlungen:

* Installieren Sie die Erweiterung mit aktivierten automatischen Nebenversionsupgrades.
  * Auf VMs im klassischen Bereitstellungsmodus geben Sie dazu als Version „3.*“ an, wenn Sie die Erweiterung über die plattformübergreifende Azure-Befehlszeilenschnittstelle oder über PowerShell installieren.
  * Bei virtuellen Computern im Azure Resource Manager-Bereitstellungsmodell fügen Sie „"autoUpgradeMinorVersion": true“ in die VM-Bereitstellungsvorlage ein.
* Verwenden Sie für LAD 3.0 ein neues bzw. anderes Speicherkonto. Es gibt mehrere kleine Inkompatibilitäten zwischen LAD 2.3 und LAD 3.0, die bei einer gemeinsamen Nutzung eines Kontos zu Konflikten führen können:
  * LAD 3.0 speichert Syslog-Ereignisse in einer Tabelle mit einem anderen Namen.
  * Die counterSpecifier-Zeichenfolgen für `builtin`-Metriken unterscheiden sich in LAD 3.0.

## <a name="protected-settings"></a>Geschützte Einstellungen

Dieser Satz von Konfigurationsinformationen enthält vertrauliche Informationen, die nicht öffentlich einsehbar sein dürfen. Dazu gehören Anmeldeinformationen für Storage. Diese Einstellungen werden von der Erweiterung in verschlüsselter Form übertragen und gespeichert.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Wert
---- | -----
storageAccountName | Der Name des Speicherkontos, in dem von der Erweiterung Daten geschrieben werden
storageAccountEndPoint | (optional:) Der Endpunkt, der die Cloud mit dem Speicherkonto angibt. Wenn diese Einstellung fehlt, verwendet LAD standardmäßig die öffentliche Azure-Cloud `https://core.windows.net`. Um ein Speicherkonto in Azure Deutschland, Azure Government oder Azure China zu verwenden, legen Sie diesen Wert entsprechend fest.
storageAccountSasToken | Ein [Konto-SAS-Token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) für Blob-und Tabellendienste (`ss='bt'`); gilt für Container und Objekte (`srt='co'`) und gewährt die Berechtigungen zum Hinzuzufügen, Erstellen, Auflisten, Aktualisieren und Schreiben (`sp='acluw'`). Fügen Sie *nicht* das vorangestellte Fragezeichen (?) ein.
mdsdHttpProxy | (optional:) HTTP-Proxyinformationen, die erforderlich sind, damit die Erweiterung Verbindungen mit dem angegebenen Speicherkonto und dem Endpunkt herstellen kann
sinksConfig | (optional:) Details zu alternativen Zielen, an die Metriken und Ereignisse übermittelt werden können. Die spezifischen Details der einzelnen Datensenken, die von der Erweiterung unterstützt werden, sind in den folgenden Abschnitten beschrieben.

Verwenden Sie die **listAccountSas**-Funktion, um ein SAS-Token innerhalb einer Resource Manager-Vorlage zu erhalten. Eine Beispielvorlage finden Sie unter [Beispiel für eine Listenfunktion](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Sie können das erforderliche SAS-Token einfach über das Azure-Portal erstellen.

1. Wählen Sie das allgemeine Speicherkonto aus, in das die Erweiterung schreiben soll.
1. Wählen Sie im Einstellungsbereich im Menü auf der linken Seite „Shared Access Signature“ aus.
1. Passen Sie die entsprechenden Abschnitte wie zuvor beschrieben an.
1. Klicken Sie auf die Schaltfläche „SAS generieren“.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Screenshot der Shared Access Signature-Seite mit „SAS generieren“.":::

Kopieren Sie die generierte SAS in das Feld storageAccountSasToken. Entfernen Sie das vorangestellte-Fragezeichen („?“).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

In diesem optionalen Abschnitt werden zusätzliche Ziele definiert, an die die Erweiterung die gesammelten Informationen sendet. Das Array „sink“ enthält ein Objekt für jede zusätzliche Datensenke. Das Attribut „type“ bestimmt die anderen Attribute im Objekt.

Element | Wert
------- | -----
name | Eine Zeichenfolge, die zum Verweisen auf diese Senke an anderer Stelle in der Konfiguration der Erweiterung verwendet wird
type | Der Typ der Senke, die definiert wird. Bestimmt die anderen Werte in Instanzen dieses Typs (sofern vorhanden).

Version 3.0 der Linux-Diagnoseerweiterung unterstützt zwei Senkentypen: „EventHub“ und „JsonBlob“.

#### <a name="the-eventhub-sink"></a>Die EventHub-Senke

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

Der Eintrag „sasURL“ enthält die vollständige URL, einschließlich des SAS-Tokens für den Event Hub, an den die Daten veröffentlicht werden sollen. LAD erfordert eine SAS-Benennungsrichtlinie, die den Anspruch „Senden“ aktiviert. Beispiel:

* Erstellen Sie den Event Hubs-Namespace `contosohub`.
* Erstellen Sie einen Event Hub im Namespace `syslogmsgs`.
* Erstellen Sie auf dem Event Hub die SAS-Richtlinie `writer`, die den Anspruch „Senden“ aktiviert.

Wenn Sie eine SAS erstellt haben, die bis Mitternacht (UTC) am 1. Januar 2018 gilt, lautet der sasURL-Wert in etwa wie folgt:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Weitere Informationen zum Generieren von SAS-Token und Abrufen von Informationen für SAS-Token für Event Hubs finden Sie auf [dieser Webseite](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>Die JsonBlob-Senke

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Die an die JsonBlob-Senke weitergeleiteten Daten werden in Blobs in Azure Storage gespeichert. Jede LAD-Instanz erstellt stündlich ein Blob für jeden Senkennamen. Jedes Blob enthält immer ein syntaktisch gültiges JSON-Array von Objekten. Neue Einträge werden dem Array automatisch hinzugefügt. Blobs werden in einem Container mit dem gleichen Namen wie die Senke gespeichert. Die Azure Storage-Regeln für Blobcontainernamen gelten auch für die Namen der JsonBlob-Senken: zwischen 3 und 63 alphanumerische ASCII-Zeichen in Kleinschreibung oder Bindestriche.

## <a name="public-settings"></a>Öffentliche Einstellungen

Diese Struktur enthält verschiedene Blöcke von Einstellungen zur Steuerung der von der Erweiterung gesammelten Informationen. Diese Einstellungen sind optional. Bei Angabe von `ladCfg` müssen Sie auch `StorageAccount` angeben.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Wert
------- | -----
StorageAccount | Der Name des Speicherkontos, in dem von der Erweiterung Daten geschrieben werden Er muss mit dem Namen übereinstimmen, der in den [geschützten Einstellungen](#protected-settings) festgelegt wurde.
mdsdHttpProxy | (optional:) Wie bei den [geschützten Einstellungen](#protected-settings). Der öffentliche Wert wird durch den geschützten Wert überschrieben, sofern dieser festgelegt wurde. Nehmen Sie Proxyeinstellungen, die ein Geheimnis (z.B. ein Kennwort) enthalten, in den [geschützten Einstellungen](#protected-settings) vor.

Diese übrigen Elemente werden in den folgenden Abschnitten ausführlich beschrieben.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Diese optionale Struktur steuert das Sammeln von Metriken und Protokollen für die Übermittlung an den Azure-Metrikendienst und andere Datensenken. Sie müssen `performanceCounters` und/oder `syslogEvents` angeben. Sie müssen die `metrics`-Struktur angeben.

Element | Wert
------- | -----
eventVolume | (optional:) Steuert die Anzahl der Partitionen, die innerhalb der Speichertabelle erstellt werden. Muss `"Large"`, `"Medium"` oder `"Small"` sein. Wenn Sie hier nichts angeben, lautet der Standardwert `"Medium"`.
sampleRateInSeconds | (optional:) Das Standardintervall zwischen der Erfassung von unformatierten (nicht aggregierten) Metriken. Die kleinste unterstützte Erfassungsrate beträgt 15 Sekunden. Wenn Sie hier nichts angeben, lautet der Standardwert `15`.

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Wert
------- | -----
resourceId | Die Azure Resource Manager-Ressourcen-ID des virtuellen Computers oder der VM-Skalierungsgruppe, zu der der virtuelle Computer gehört. Diese Einstellung muss ebenfalls angegeben werden, wenn in der Konfiguration eine JsonBlob-Senke verwendet wird.
scheduledTransferPeriod | Die Häufigkeit, mit der aggregierte Metriken berechnet und an den Azure-Metrikendienst übertragen werden, als ein Zeitintervall im Format ISO 8601. Das kleinste Übertragungsintervall ist 60 Sekunden, d.h. PT1M. Sie müssen mindestens einen scheduledTransferPeriod-Wert angeben.

Die Werte für die Metriken, die im Abschnitt performanceCounters angegeben wurden, werden alle 15 Sekunden oder in der explizit für den Leistungsindikator festgelegten Häufigkeit erfasst. Wenn mehrere scheduledTransferPeriod-Werte (wie im Beispiel) angegeben wurden, wird jede Aggregation unabhängig von den anderen berechnet.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Mit diesem optionalen Abschnitt wird die Erfassung von Metriken gesteuert. Die unformatierten Daten werden für jede [scheduledTransferPeriod](#metrics) aggregiert, um diese Werte zu generieren:

* mean
* minimum
* maximum
* letzter erfasster Wert
* Anzahl der unformatierten Daten für die Aggregatberechnung

Element | Wert
------- | -----
sinks | (optional:) Eine durch Trennzeichen getrennte Liste der Namen der Senken, an die LAD die aggregierten Metrikergebnisse übermittelt. Alle aggregierten Metriken werden an jede aufgeführte Senke veröffentlicht. Siehe [sinksConfig](#sinksconfig). Beispiel: `"EHsink1, myjsonsink"`.
type | Gibt den tatsächlichen Anbieter der Metrik an.
class | Gibt zusammen mit „counter“ die jeweilige Metrik im Namespace des Anbieters an
Zähler | Gibt zusammen mit „class“ die jeweilige Metrik im Namespace des Anbieters an
counterSpecifier | Gibt die jeweilige Metrik im Azure-Metrikennamespace an
condition | (optional:) Wählt eine bestimmte Instanz des Objekts, für die die Metrik gilt, oder die Aggregation für alle Instanzen des betreffenden Objekts aus. Weitere Informationen finden Sie in den `builtin` Metrikdefinitionen.
sampleRate | ISO-8601-Intervall, das die Häufigkeit festlegt, mit der unformatierte Daten für diese Metrik gesammelt werden. Wenn keine Festlegung erfolgt, wird das Erfassungsintervall durch den Wert von [sampleRateInSeconds](#ladcfg) festgelegt. Die kleinste unterstützte Erfassungsrate beträgt 15 Sekunden (PT15S).
unit | Sollte eine der folgenden Zeichenfolgen sein: „Count“, „Bytes“, „Seconds“, „Percent“, „CountPerSecond“, „BytesPerSecond“, „Millisecond“. Definiert die Einheit für die Metrik. Consumer der gesammelten Daten erwarten, dass die gesammelten Datenwerte diesen Einheiten entsprechen. LAD ignoriert dieses Feld.
displayName | Die Bezeichnung (in der Sprache, die durch die zugehörige Gebietsschemaeinstellung festgelegt wird), die an diese Daten in Azure-Metriken angefügt wird. LAD ignoriert dieses Feld.

Der counterSpecifier ist ein frei wählbarer Bezeichner. Consumer von Metriken, wie das Azure-Portal-Feature für Diagramme und Warnungen, verwenden counterSpecifier als „Schlüssel“, der eine Metrik oder eine Instanz einer Metrik identifiziert. Für `builtin`-Metriken werden counterSpecifier-Werte empfohlen, die mit `/builtin/` beginnen. Wenn Sie eine bestimmte Instanz einer Metrik erfassen, sollten Sie den Bezeichner der Instanz an den counterSpecifier-Wert anfügen. Einige Beispiele:

* `/builtin/Processor/PercentIdleTime` – Leerlaufzeit (Durchschnitt für alle vCPUs)
* `/builtin/Disk/FreeSpace(/mnt)` – freier Speicherplatz für das Dateisystem „/mnt“
* `/builtin/Disk/FreeSpace` – freier Speicherplatz (Durchschnitt für alle bereitgestellten Dateisysteme)

Weder LAD noch das Azure-Portal erwarten, dass der counterSpecifier-Wert einem bestimmten Muster folgt. Sie sollten sich allerdings bei der Vergabe von counterSpecifier-Werten an eine Konvention halten.

Bei Angabe von `performanceCounters` schreibt LAD Daten immer in eine Tabelle in Azure Storage. Sie können dieselben Daten in JSON-Blobs und/oder Event Hubs schreiben, aber Sie können nicht das Speichern von Daten in einer Tabelle deaktivieren. Alle Instanzen der Diagnoseerweiterung, die für die Verwendung desselben Speicherkontonamens und Endpunkts konfiguriert wurden, fügen ihre Metriken und Protokolle derselben Tabelle hinzu. Wenn zu viele virtuelle Computer in dieselbe Tabellenpartition schreiben, kann Azure Schreibvorgänge in dieser Partition drosseln. Die eventVolume-Einstellung bewirkt, dass Einträge auf 1 (Small), 10 (Medium) oder 100 (Large) verschiedene Partitionen aufgeteilt werden. In der Regel reicht „Medium“ aus, um sicherzustellen, dass der Datenverkehr nicht gedrosselt wird. Das Azure-Metrikenfeature im Azure-Portal verwendet die Daten in dieser Tabelle, um Graphen zu erstellen oder Warnungen auszulösen. Der Tabellenname ist eine Verkettung dieser Zeichenfolgen:

* `WADMetrics`
* Die scheduledTransferPeriod für die aggregierten Werte, die in der Tabelle gespeichert werden
* `P10DV2S`
* Ein Datum im Format „JJJJMMTT“, das sich alle 10 Tage ändert

Beispiele sind `WADMetricsPT1HP10DV2S20170410` und `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Mit diesem optionalen Abschnitt wird die Erfassung von Protokollereignissen von Syslog gesteuert. Wenn dieser Abschnitt ausgelassen wird, werden Syslog-Ereignisse überhaupt nicht erfasst.

Die syslogEventConfiguration-Sammlung enthält einen Eintrag für jede gewünschte Syslog-Funktion. Wenn minSeverity für eine bestimmte Funktion „NONE“ lautet oder wenn diese Funktion im Element nicht enthalten ist, werden keine Ereignisse von dieser Funktion erfasst.

Element | Wert
------- | -----
sinks | Eine durch Trennzeichen getrennte Liste der Namen von Senken, an die einzelne Protokollereignisse veröffentlicht werden. Alle Protokollereignisse, die den Einschränkungen in syslogEventConfiguration entsprechen, werden an alle aufgeführten Senken veröffentlicht. Beispiel: „EHforsyslog“
facilityName | Ein Syslog-Funktionsname (z.B. „LOG\_USER“ oder „LOG\_LOCAL0“). Im Abschnitt „facility“ in der [Manpage von Syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) finden Sie die vollständige Liste.
minSeverity | Ein Syslog-Schweregrad (z.B. „LOG\_ERR“ oder „LOG\_INFO“). Im Abschnitt „level“ in der [Manpage von Syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) finden Sie die vollständige Liste. Die Erweiterung erfasst Ereignisse ab dem angegebenen Grad, die an die Funktion gesendet werden.

Bei Angabe von `syslogEvents` schreibt LAD Daten immer in eine Tabelle in Azure Storage. Sie können dieselben Daten in JSON-Blobs und/oder Event Hubs schreiben, aber Sie können nicht das Speichern von Daten in einer Tabelle deaktivieren. Die Partitionierung für diese Tabelle entspricht der Beschreibung für `performanceCounters`. Der Tabellenname ist eine Verkettung dieser Zeichenfolgen:

* `LinuxSyslog`
* Ein Datum im Format „JJJJMMTT“, das sich alle 10 Tage ändert

Beispiele sind `LinuxSyslog20170410` und `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Dieser optionale Abschnitt steuert die Ausführung von beliebigen [OMI](https://github.com/Microsoft/omi)-Abfragen.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Wert
------- | -----
Namespace | (optional:) Der OMI-Namespace, in dem die Abfrage ausgeführt werden soll. Falls keine Angabe erfolgt, lautet der Standardwert „root/scx“, der von den [plattformübergreifenden System Center-Anbietern](https://github.com/Microsoft/SCXcore) implementiert wird.
Abfrage | Die OMI-Abfrage, die ausgeführt werden soll.
table | (optional:) Die Azure Storage-Tabelle im angegebenen Speicherkonto (siehe [geschützte Einstellungen](#protected-settings)).
frequency | (optional:) Die Anzahl von Sekunden zwischen der Ausführung der Abfrage. Der Standardwert ist 300 (fünf Minuten), der Mindestwert beträgt 15 Sekunden.
sinks | (optional:) Eine durch Trennzeichen getrennte Liste der Namen von zusätzlichen Senken, an die unformatierte Metrikergebnisse veröffentlicht werden sollen. Es erfolgt keine Aggregation dieser unformatierten Daten durch die Erweiterung oder den Azure-Metrikendienst.

Sie müssen „table“ und/oder „sinks“ angeben.

### <a name="filelogs"></a>fileLogs

Steuert die Erfassung von Protokolldateien. LAD erfasst neue Textzeilen so, wie sie in die Datei geschrieben werden, und schreibt sie in die Tabellenzeilen und/oder alle angegebenen Senken (JsonBlob oder EventHub).

> [!NOTE]
> Dateiprotokolle (fileLogs) werden durch eine Unterkomponente von LAD namens `omsagent` erfasst. Zur Erfassung von Dateiprotokollen muss der Benutzer `omsagent` über Leseberechtigungen für die von Ihnen angegebenen Dateien sowie über Ausführungsberechtigungen für alle Verzeichnisse im Pfad zu dieser Datei verfügen. Dies kann nach der Installation von LAD durch Ausführen von `sudo su omsagent -c 'cat /path/to/file'` überprüft werden.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Wert
------- | -----
file | Der vollständige Pfadname der Protokolldatei, die überwacht und erfasst werden soll. Beim Pfadnamen muss es sich um eine einzelne Datei handeln. Er darf keinen Verzeichnisnamen oder Platzhalter enthalten. Das Benutzerkonto „omsagent“ muss über Lesezugriff auf den Dateipfad verfügen.
table | (optional:) Die Azure Storage-Tabelle im angegebenen Speicherkonto (wie in der geschützten Konfiguration angegeben), in die neue Zeilen vom Ende der Datei geschrieben werden.
sinks | (optional:) Eine durch Trennzeichen getrennte Liste der Namen zusätzlicher Senken, an die Protokollzeilen gesendet werden.

Sie müssen „table“ und/oder „sinks“ angeben.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriken, die vom integrierten Anbieter unterstützt werden

Der integrierte Metrikanbieter ist eine Quelle von Metriken, die für eine Vielzahl von Benutzern von Interesse sind. Diese Metriken werden in fünf Klassen unterteilt:

* Prozessor
* Arbeitsspeicher
* Netzwerk
* Dateisystem
* Datenträger

### <a name="builtin-metrics-for-the-processor-class"></a>Integrierte Metriken der Prozessorklasse

Die Prozessorklasse von Metriken bietet Informationen zur Prozessorauslastung auf dem virtuellen Computer. Beim Aggregieren von Prozentsätzen ist das Ergebnis der Durchschnitt für alle CPUs. Wenn bei einem virtuellen Computer mit zwei vCPUs eine vCPU zu 100 % ausgelastet und die andere zu 100 % im Leerlauf ist, wird für „PercentIdleTime“ der Wert 50 zurückgegeben. Wenn jede vCPU im gleichen Zeitraum zu 50 % ausgelastet wäre, würde das gemeldete Ergebnis ebenfalls 50 lauten. Wenn bei einem virtuellen Computer mit vier vCPUs eine vCPU zu 100 % ausgelastet ist und die anderen im Leerlauf sind, wird für „PercentIdleTime“ der Wert 75 zurückgegeben.

Zähler | Bedeutung
------- | -------
PercentIdleTime | Prozentsatz der Zeit während des Aggregationszeitraums, in der die Prozessoren die Kernel-Leerlaufschleife ausgeführt haben
PercentProcessorTime | Prozentsatz der Zeit für die Ausführung von anderen Threads als dem Leerlaufthread
PercentIOWaitTime | Prozentsatz der Zeit für das Warten auf den Abschluss von E/A-Vorgängen
PercentInterruptTime | Prozentsatz der Zeit für die Ausführung von Hardware- und Softwareinterrupts und DPCs (zurückgestellte Prozeduraufrufe)
PercentUserTime | Prozentsatz der Zeit, die während des Aggregationszeitraums nicht im Leerlauf verbracht wurde, sondern durch Benutzer stärker bei normaler Priorität
PercentNiceTime | Prozentsatz der Zeit, die nicht im Leerlauf aufgewendet wurde, mit geringerer Priorität (Nice)
PercentPrivilegedTime | Prozentsatz der Zeit, die nicht im Leerlauf aufgewendet wurde, im privilegierten (Kernel-)Modus

Die ersten vier Leistungsindikatoren sollten in der Summe 100 % ergeben. Die letzten drei Leistungsindikatoren ergeben als Summe ebenfalls 100 %. Sie unterteilen die Summe aus PercentProcessorTime, PercentIOWaitTime und PercentInterruptTime.

Um eine einzelne Metrik zu erhalten, die über alle Prozessoren hinweg aggregiert wird, legen Sie `"condition": "IsAggregate=TRUE"` fest. Um eine Metrik für einen bestimmten Prozessor zu erhalten, z.B. den zweiten logischen Prozessor eines virtuellen Computers mit vier vCPUs, legen Sie `"condition": "Name=\\"1\\""` fest. Die Nummern der logischen Prozessoren liegen im Bereich `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>Integrierte Metriken der Arbeitsspeicherklasse

Die Arbeitsspeicherklasse der Metriken bietet Informationen zur Speicherauslastung, zum Paging und zum Swapping.

Zähler | Bedeutung
------- | -------
AvailableMemory | Verfügbarer physischer Speicher in MB
PercentAvailableMemory | Verfügbarer physischer Speicher als Prozentsatz des gesamten Arbeitsspeichers
UsedMemory | Verwendeter physischer Speicher (MB)
PercentUsedMemory | Verwendeter physischer Speicher als Prozentsatz des gesamten Arbeitsspeichers
PagesPerSec | Paging insgesamt (Lesen/Schreiben)
PagesReadPerSec | Aus dem Sicherungsspeicher gelesene Seiten (Auslagerungsdatei, Programmdatei, im Speicher abgebildete Datei usw.)
PagesWrittenPerSec | In den Sicherungsspeicher geschriebene Seiten (Auslagerungsdatei, im Speicher abgebildete Datei usw.)
AvailableSwap | Nicht verwendeter Auslagerungsbereich (MB)
PercentAvailableSwap | Nicht verwendeter Auslagerungsbereich als Prozentsatz des gesamten Auslagerungsbereichs
UsedSwap | Verwendeter Auslagerungsbereich (MB)
PercentUsedSwap | Verwendeter Auslagerungsbereich als Prozentsatz des gesamten Auslagerungsbereichs

Diese Klasse von Metriken hat nur eine einzige Instanz. Das „condition“-Attribut weist keine nützlichen Einstellungen auf und sollte ausgelassen werden.

### <a name="builtin-metrics-for-the-network-class"></a>Integrierte Metriken der Netzwerkklasse

Die Metriken der Netzwerkklasse liefern Informationen zu Netzwerkaktivitäten an einer einzelnen Netzwerkschnittstelle seit dem Start. LAD macht keine Bandbreitenmetriken verfügbar – diese können aus den Hostmetriken abgerufen werden.

Zähler | Bedeutung
------- | -------
BytesTransmitted | Gesamtanzahl der seit dem Start gesendeten Bytes
BytesReceived | Gesamtanzahl der seit dem Start empfangenen Bytes
BytesTotal | Gesamtanzahl der seit dem Start gesendeten und empfangenen Bytes
PacketsTransmitted | Gesamtanzahl der seit dem Start gesendeten Pakete
PacketsReceived | Gesamtanzahl der seit dem Start empfangenen Pakete
TotalRxErrors | Anzahl der Empfangsfehler seit dem Start
TotalTxErrors | Anzahl der Übertragungsfehler seit dem Start
TotalCollisions | Anzahl der Konflikte, die seit dem Start von Netzwerkports gemeldet wurden

 Obwohl diese Klasse instanziiert wird, unterstützt LAD nicht das Erfassen von aggregierten Netzwerkmetriken für alle Netzwerkgeräte. Legen Sie `"condition": "InstanceID=\\"eth0\\""` fest, um Metriken für eine bestimmte Schnittstelle abzurufen, z.B. eth0.

### <a name="builtin-metrics-for-the-filesystem-class"></a>Integrierte Metriken der Dateisystemklasse

Die Dateisystemklasse von Metriken liefert Informationen zur Nutzung des Dateisystems. Absolute und prozentuale Werte werden so erfasst, wie sie einem normalen Benutzer (nicht root) angezeigt werden würden.

Zähler | Bedeutung
------- | -------
FreeSpace | Verfügbarer Speicherplatz in Byte
UsedSpace | Verwendeter Speicherplatz in Byte
PercentFreeSpace | Prozentsatz des freien Speicherplatzes
PercentUsedSpace | Prozentsatz des belegten Speicherplatzes
PercentFreeInodes | Prozentsatz der nicht verwendeten I-Knoten
PercentUsedInodes | Prozentsatz der zugeordneten (verwendeten) I-Knoten als Summe aus sämtlichen Dateisystemen
BytesReadPerSecond | Pro Sekunde gelesene Bytes
BytesWrittenPerSecond | Pro Sekunde geschriebene Bytes
Bytes pro Sekunde | Pro Sekunde gelesene und geschriebene Bytes
ReadsPerSecond | Lesevorgänge pro Sekunde
WritesPerSecond | Schreibvorgänge pro Sekunde
TransfersPerSecond | Lese- oder Schreibvorgänge pro Sekunde

Aggregierte Werte für alle Dateisysteme erhalten Sie, indem Sie `"condition": "IsAggregate=True"` festlegen. Werte für ein bestimmtes eingebundenes Dateisystem, wie z.B. „/mnt“, erhalten Sie, indem Sie `"condition": 'Name="/mnt"'` angeben. 

> [!NOTE]
> Wenn Sie nicht JSON, sondern das Azure-Portal verwenden, ist „Name='/mnt'“ die richtige Form des Bedingungsfelds.

### <a name="builtin-metrics-for-the-disk-class"></a>Integrierte Metriken der Datenträgerklasse

Die Datenträgerklasse von Metriken liefert Informationen zur Nutzung von Datenträgergeräten. Diese Statistiken gelten für das gesamte Laufwerk. Wenn ein Gerät mehrere Dateisysteme enthält, sind die Leistungsindikatoren für das Gerät effektiv über alle aggregiert.

Zähler | Bedeutung
------- | -------
ReadsPerSecond | Lesevorgänge pro Sekunde
WritesPerSecond | Schreibvorgänge pro Sekunde
TransfersPerSecond | Vorgänge gesamt pro Sekunde
AverageReadTime | Durchschnittliche Anzahl von Sekunden pro Lesevorgang
AverageWriteTime | Durchschnittliche Anzahl von Sekunden pro Schreibvorgang
AverageTransferTime | Durchschnittliche Anzahl von Sekunden pro Vorgang
AverageDiskQueueLength | Durchschnittliche Anzahl von Datenträgervorgängen in der Warteschlange
ReadBytesPerSecond | Anzahl von pro Sekunde gelesenen Bytes
WriteBytesPerSecond | Anzahl von pro Sekunde geschriebenen Bytes
Bytes pro Sekunde | Anzahl von pro Sekunde gelesenen und geschriebenen Bytes

Aggregierte Werte für alle Datenträger erhalten Sie, indem Sie `"condition": "IsAggregate=True"` festlegen. Legen Sie zum Abrufen von Informationen für ein bestimmtes Gerät (z.B. „/dev/sdf1“) `"condition": "Name=\\"/dev/sdf1\\""` fest.

## <a name="installing-and-configuring-lad-30"></a>Installieren und Konfigurieren von LAD 3.0

### <a name="azure-cli"></a>Azure CLI

Wenn Ihre geschützten Einstellungen in der Datei „ProtectedSettings.json“ und Ihre öffentlichen Konfigurationsinformationen in „PublicSettings.json“ gespeichert sind, führen Sie den folgenden Befehl aus:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Bei diesem Befehl wird davon ausgegangen, dass Sie den Azure-Ressourcenverwaltungsmodus der Azure CLI verwenden. Wenn Sie LAD für virtuelle Computer im klassischen Bereitstellungsmodell (ASM) konfigurieren möchten, wechseln Sie in den ASM-Modus (`azure config mode asm`) und lassen den Namen der Ressourcengruppe im Befehl aus. Weitere Informationen finden Sie in der [Dokumentation zur plattformübergreifenden Azure-Befehlszeilenschnittstelle](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Wenn sich Ihre geschützten Einstellungen in der Variablen `$protectedSettings` und Ihre öffentlichen Konfigurationsinformationen in der Variablen `$publicSettings` befinden, führen Sie den folgenden Befehl aus:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>Eine Beispielkonfiguration für LAD 3.0

Die folgende Beispielkonfiguration für LAD 3.0 mit einigen Erläuterungen basiert auf den oben angegebenen Definitionen. Um dieses Beispiel in Ihrem Szenario anzuwenden, sollten Sie Ihre eigenen Speicherkontonamen, SAS-Kontotoken und EventHubs-SAS-Token verwenden.

> [!NOTE]
> Die Methode zum Bereitstellen von öffentlichen und geschützten Einstellungen unterscheidet sich abhängig davon, ob Sie LAD über die Azure-Befehlszeilenschnittstelle oder mithilfe von PowerShell installieren. Speichern Sie bei Verwendung der Azure-Befehlszeilenschnittstelle die folgenden Einstellungen in „ProtectedSettings.json“ und „PublicSettings.json“ für die Verwendung mit dem obigen Beispielbefehl. Führen Sie bei Verwendung von PowerShell `$protectedSettings = '{ ... }'` aus, um die Einstellungen in `$protectedSettings` und `$publicSettings` zu speichern.

### <a name="protected-settings"></a>Geschützte Einstellungen

Durch diese geschützten Einstellungen wird Folgendes konfiguriert:

* ein Speicherkonto
* ein zugehöriges SAS-Kontotoken
* mehrere Senken (JsonBlob oder EventHubs mit SAS-Token)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Öffentliche Einstellungen

Diese öffentlichen Einstellungen weisen LAD zu Folgendem an:

* Hochladen von Metriken zur prozentualen Prozessorzeit und zum verwendeten Speicherplatz in die Tabelle `WADMetrics*`
* Hochladen von Benachrichtigungen der Syslog-Funktion „user“ mit dem Schweregrad „info“ in die Tabelle `LinuxSyslog*`
* Hochladen von unformatierten OMI-Abfrageergebnissen (PercentProcessorTime und PercentIdleTime) in die Tabelle `LinuxCPU`
* Hochladen von angefügten Zeilen in der Datei `/var/log/myladtestlog` in die Tabelle `MyLadTestLog`

In jedem Fall werden außerdem Daten hochgeladen nach:

* Azure Blob Storage (Containername gemäß Definition in der JsonBlob-Senke)
* EventHubs-Endpunkt (gemäß Angabe in der EventHubs-Senke)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Die `resourceId` in der Konfiguration muss mit der des virtuellen Computers oder der VM-Skalierungsgruppe übereinstimmen.

* Die Funktion für Diagramme und Warnungen zu Azure-Plattformmetriken kennt die resourceId des virtuellen Computers, mit dem Sie arbeiten. Sie geht davon aus, die Daten zu Ihrem virtuellen Computer mithilfe der resourceId als Suchschlüssel zu finden.
* Bei Verwendung der automatischen Skalierung von Azure muss die resourceId in der Konfiguration für die automatische Skalierung mit der von LAD verwendeten resourceId übereinstimmen.
* Die resourceId wird in die Namen der JsonBlobs integriert, die von LAD geschrieben werden.

## <a name="view-your-data"></a>Prüfen der Daten

Sie verwenden das Azure-Portal, um die Leistungsdaten anzuzeigen oder Warnungen festzulegen:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Screenshot des Azure-Portals mit Auswahl des verwendeten Speicherplatzes auf dem Datenträger als Metrik und dem resultierenden Diagramm.":::

Die `performanceCounters`-Daten werden immer in einer Azure Storage-Tabelle gespeichert. Azure Storage-APIs sind für viele Sprachen und Plattformen verfügbar.

Die an JsonBlob-Senken gesendeten Daten werden in Blobs in dem Speicherkonto gespeichert, das in den [geschützten Einstellungen](#protected-settings) angegeben wurde. Sie können die Blobdaten mithilfe von Azure Blob Storage-APIs nutzen.

Darüber hinaus können Sie folgende Tools mit grafischer Benutzeroberfläche für den Zugriff auf Daten in Azure Storage verwenden:

* Server-Explorer von Visual Studio
* [Screenshot mit Containern und Tabellen in Azure Storage Explorer.](https://azurestorageexplorer.codeplex.com/ "Azure Storage-Explorer")

Diese Momentaufnahme einer Sitzung im Microsoft Azure Storage-Explorer zeigt die generierten Azure Storage-Tabellen und -Container einer ordnungsgemäß konfigurierten LAD 3.0-Erweiterung auf einem virtuellen Testcomputer. Das Bild stimmt nicht genau mit der [LAD 3.0-Beispielkonfiguration](#an-example-lad-30-configuration) überein.

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Screenshot von Azure Storage-Explorer":::


In der entsprechenden [EventHubs-Dokumentation](../../event-hubs/event-hubs-about.md) finden Sie Informationen zum Verarbeiten von Meldungen, die an einen EventHubs-Endpunkt veröffentlicht wurden.

## <a name="next-steps"></a>Nächste Schritte

* Erstellen Sie Metrikwarnungen in [Azure Monitor](../../azure-monitor/platform/alerts-classic-portal.md) für die von Ihnen erfassten Metriken.
* Erstellen Sie [Überwachungsdiagramme](../../azure-monitor/platform/data-platform.md) für Ihre Metriken.
* Erfahren Sie, wie Sie mithilfe Ihrer Metriken [eine VM-Skalierungsgruppe](../linux/tutorial-create-vmss.md) erstellen, um die automatische Skalierung zu steuern.
