---
title: Azure Instance Metadata Service für Windows
description: Erfahren Sie mehr über den Azure Instance Metadata Service und darüber, wie er Informationen zu Instanzen virtueller Computer bereitstellt, die derzeit in Windows ausgeführt werden.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435228"
---
# <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst

Der Azure Instance Metadata Service (IMDS) stellt Informationen zu Instanzen virtueller Computer bereit, die derzeit ausgeführt werden. Sie können ihn zur Verwaltung und Konfiguration Ihrer virtuellen Computer verwenden.
Hierzu gehören die SKU, der Speicher, Netzwerkkonfigurationen und bevorstehende Wartungsereignisse. Eine vollständige Liste der verfügbaren Daten finden Sie unter [Metadaten-APIs](#metadata-apis).


Der IMDS ist für das Ausführen von Instanzen virtueller Computer (VMs) und VM-Skalierungsgruppen verfügbar. Alle APIs unterstützen VMs, die mit [Azure Resource Manager](/rest/api/resources/) erstellt und verwaltet werden. Nur die bestätigten und Netzwerkendpunkte unterstützen VMs, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden. Beim bestätigten Endpunkt erfolgt dies nur im begrenzten Umfang.

IMDS ist ein REST-Endpunkt, der unter einer bekannten, nicht routingfähigen IP-Adresse (`169.254.169.254`) verfügbar ist. Sie greifen nur vom virtuellen Computer aus darauf zu. Die Kommunikation zwischen dem virtuellen Computer und IMDS verlässt niemals den Host.
Die HTTP-Clients sollten bei Abfragen von IMDS Webproxys innerhalb des virtuellen Computers umgehen und `169.254.169.254` gleich behandeln wie [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="security"></a>Sicherheit

Auf den IMDS-Endpunkt kann nur innerhalb der ausgeführten Instanz des virtuellen Computers an einer nicht routingfähigen IP-Adresse zugegriffen werden. Darüber hinaus wird jede Anforderung mit einem `X-Forwarded-For`-Header vom Dienst abgelehnt.
Zudem müssen Anforderungen einen `Metadata: true`-Header enthalten, um sicherzustellen, dass die eigentliche Anforderung direkt vorgesehen war und nicht Teil einer unbeabsichtigten Umleitung ist.

> [!IMPORTANT]
> IMDS ist kein Kanal für vertrauliche Daten. Der Endpunkt ist für alle Prozesse auf der VM offen. Betrachten Sie Informationen, die über diesen Dienst verfügbar gemacht werden, als freigegebene Informationen für alle Anwendungen, die in der VM ausgeführt werden.

## <a name="usage"></a>Verwendung

### <a name="access-azure-instance-metadata-service"></a>Zugreifen auf den Azure Instance Metadata Service

Um auf den IMDS zuzugreifen, erstellen Sie eine VM über [Azure Resource Manager](/rest/api/resources/) oder das [Azure-Portal](https://portal.azure.com), und verwenden Sie die folgenden Beispiele.
Weitere Beispiele finden Sie unter [Azure Instance Metadata Samples](https://github.com/microsoft/azureimds).

Hier finden Sie das Codebeispiel zum Abrufen aller Metadaten für eine Instanz. Informationen zum Zugriff auf eine bestimmte Datenquelle finden Sie im Abschnitt [Metadata-API](#metadata-apis). 

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> Das `-NoProxy`-Flag ist nur in PowerShell 6 oder höher verfügbar. Wenn Sie keinen Proxy eingerichtet haben, können Sie das Flag weglassen.

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Übergeben Sie Ihre REST-Abfrage über das `ConvertTo-Json`-Cmdlet, um eine ansehnliche Ausgabe zu erhalten.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Datenausgabe

Standardmäßig gibt der IMDS Daten im JSON-Format (`Content-Type: application/json`) zurück. Falls angefordert, können einige APIs jedoch Daten in verschiedenen Formaten zurückgeben.
In der folgenden Tabelle sind weitere Datenformate aufgelistet, die von APIs unterstützt werden.

API | Standarddatenformat | Andere Formate
--------|---------------------|--------------
/attested | json | none
/identity | json | none
/instance | json | text
/scheduledevents | json | none

Um auf ein nicht standardmäßiges Antwortformat zuzugreifen, geben Sie das angeforderte Format als QueryString-Parameter in der Anforderung an. Beispiel:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Für Blattknoten in `/metadata/instance` funktioniert `format=json` nicht. Für diese Abfragen muss `format=text` explizit angegeben werden, da das Standardformat JSON ist.

### <a name="version"></a>Version

Der Instance Metadata Service (IMDS) verwendet Versionsangaben, und die Angabe der API-Version in der HTTP-Anforderung ist zwingend erforderlich.

Die unterstützten API-Versionen lauten wie folgt: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 11.03.2019
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> Die Version 2020-10-01 ist möglicherweise noch nicht in jeder Region verfügbar.

Wenn neuere Versionen hinzugefügt werden, können Sie weiterhin auf ältere Versionen aus Kompatibilitätsgründen zugreifen, falls Ihre Skripts von bestimmten Datenformaten abhängig sind.

Wenn Sie keine Version angeben, erhalten Sie eine Fehlermeldung mit einer Liste der neuesten unterstützten Versionen.

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Das folgende Beispiel zeigt die Fehlerbedingung an, wenn die Version nicht angegeben ist. Die Antwort ist zur besseren Lesbarkeit im Schöndruck ausgegeben.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**Antwort**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>Metadaten-APIs

IMDS enthält mehrere APIs, die verschiedene Datenquellen darstellen.

API | BESCHREIBUNG | Eingeführt in Version
----|-------------|-----------------------
/attested | Siehe [Bestätigte Daten](#attested-data) | 2018-10-01
/identity | Siehe [Abrufen eines Zugriffstokens](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Siehe [Instanz-API](#instance-api) | 2017-04-02
/scheduledevents | Siehe [Geplante Ereignisse](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Instanz-API

Die Instanz-API macht die wichtigen Metadaten für die VM-Instanzen verfügbar, einschließlich VM, Netzwerk und Speicher. Auf die folgenden Kategorien können Sie über `instance/compute` zugreifen:

Daten | BESCHREIBUNG | Eingeführt in Version
-----|-------------|-----------------------
azEnvironment | Die Azure-Umgebung, in der die VM ausgeführt wird. | 2018-10-01
customData | Diese Funktion ist derzeit deaktiviert. | 2019-02-01
isHostCompatibilityLayerVm | Gibt an, ob die VM auf der Hostkompatibilitätsebene ausgeführt wird | 2020-06-01
licenseType | Der Typ der Lizenz für [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit). Beachten Sie, dass dies nur für AHB-aktivierte VMs verfügbar ist. | 2020-09-01
location | Die Azure-Region, in der die VM ausgeführt wird. | 2017-04-02
name | Der Name der VM. | 2017-04-02
offer | Stellen Sie Informationen für das VM-Image bereit. Dies ist nur für Images vorhanden, die mithilfe des Azure-Imagekatalogs bereitgestellt werden. | 2017-04-02
osProfile.adminUsername | Gibt den Namen des Administratorkontos an. | 2020-07-15
osProfile.computerName | Gibt den Namen des Computers an. | 2020-07-15
osProfile.disablePasswordAuthentication | Gibt an, ob die Kennwortauthentifizierung deaktiviert ist. Beachten Sie, dass dies nur für Linux-VMs verfügbar ist. | 2020-10-01
osType | Linux oder Windows. | 2017-04-02
placementGroupId | [Platzierungsgruppe](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) der VM-Skalierungsgruppe. | 2017-08-01
Tarif | Der [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) mit Name, Produkt und Herausgeber für einen virtuellen Computer, wenn es sich um ein Azure Marketplace-Image handelt. | 2018-04-02
platformUpdateDomain |  Die [Updatedomäne](../manage-availability.md), in der die VM ausgeführt wird. | 2017-04-02
platformFaultDomain | Die [Fehlerdomäne](../manage-availability.md), in der die VM ausgeführt wird. | 2017-04-02
Provider | Der Anbieter der VM. | 2018-10-01
publicKeys | [Sammlung von öffentlichen Schlüsseln](/rest/api/compute/virtualmachines/createorupdate#sshpublickey), die dem virtuellen Computer und den entsprechenden Pfaden zugewiesen ist. | 2018-04-02
publisher | Der Herausgeber des VM-Images. | 2017-04-02
resourceGroupName | Die [Ressourcengruppe](../../azure-resource-manager/management/overview.md) für Ihre VM. | 2017-08-01
resourceId | Die [vollqualifizierte](/rest/api/resources/resources/getbyid) ID der Ressource. | 11.03.2019
sku | Die spezifische SKU für das VM-Image. | 2017-04-02
securityProfile.secureBootEnabled | Gibt an, ob UEFI Secure Boot auf der VM aktiviert ist. | 2020-06-01
securityProfile.virtualTpmEnabled | Gibt an, ob das virtuelle Trusted Platform Module (TPM) auf der VM aktiviert ist. | 2020-06-01
storageProfile | Siehe [Speicherprofil](#storage-metadata) | 2019-06-01
subscriptionId | Das Azure-Abonnement für die VM. | 2017-08-01
tags | Die [Tags](../../azure-resource-manager/management/tag-resources.md) für Ihre VM.  | 2017-08-01
tagsList | Tags, die zur einfacheren programmgesteuerten Analyse als JSON-Arrays formatiert sind.  | 2019-06-04
version | Die Version des VM-Images. | 2017-04-02
vmId | Der [eindeutiger Bezeichner](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) für die VM. | 2017-04-02
vmScaleSetName | Der [Name Ihrer VM-Skalierungsgruppe](../../virtual-machine-scale-sets/overview.md). | 2017-12-01
vmSize | Siehe [VM-Größe](../sizes.md) | 2017-04-02
Zone | Die [Verfügbarkeitszone](../../availability-zones/az-overview.md) Ihrer VM. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Beispiel 1: Nachverfolgen einer unter Azure ausgeführten VM

Als Dienstanbieter müssen Sie möglicherweise die Anzahl von VMs nachverfolgen, auf denen Ihre Software ausgeführt wird, oder benötigen Agents, die die Eindeutigkeit der VM überwachen müssen. Um eine eindeutige ID für eine VM abrufen zu können, verwenden Sie das Feld `vmId` des IMDS.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Antwort**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Beispiel 2: Platzierung unterschiedlicher Datenreplikate

In bestimmten Szenarien ist die Platzierung unterschiedlicher Datenreplikate von primärer Bedeutung. Beispielsweise müssen Sie bei der [HDFS-Replikatplatzierung](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) oder bei der Containerplatzierung über einen [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) die `platformFaultDomain` und die `platformUpdateDomain` kennen, auf denen die VM ausgeführt wird.
Sie können auch [Verfügbarkeitszonen](../../availability-zones/az-overview.md) für die Instanzen nutzen, um diese Entscheidungen zu treffen.
Sie können diese Daten direkt über den IMDS abfragen.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Antwort**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Beispiel 3: Abrufen von weiteren Informationen zur VM während einer Supportanfrage

Als Dienstanbieter erhalten Sie möglicherweise eine Supportanfrage, für die Sie weitere Informationen zur VM erfahren möchten. In diesem Fall kann es nützlich sein, den Kunden aufzufordern, die Computemetadaten freizugeben.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Beispiel 4: Abrufen der Azure-Umgebung, in der die VM ausgeführt wird

Azure verfügt über verschiedene Sovereign Clouds wie [Azure Government](https://azure.microsoft.com/overview/clouds/government/). In manchen Fällen muss die Azure-Umgebung gewisse Laufzeitentscheidungen treffen. Im folgenden Beispiel wird gezeigt, wie Sie dieses Verhalten erzielen können.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Antwort**

```text
AzurePublicCloud
```

Die Cloud und die Werte der Azure-Umgebung sind hier aufgelistet.

 Cloud   | Azure-Umgebung
---------|-----------------
[Alle allgemein verfügbaren globalen Azure-Regionen](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Netzwerkmetadaten 

Netzwerkmetadaten sind Teil der Instanz-API. Die folgenden Netzwerkkategorien werden über den `instance/network`-Endpunkt zur Verfügung gestellt.

Daten | BESCHREIBUNG | Eingeführt in Version
-----|-------------|-----------------------
ipv4/privateIpAddress | Die lokale IPv4-Adresse der VM. | 2017-04-02
ipv4/publicIpAddress | Die öffentliche IPv4-Adresse der VM. | 2017-04-02
subnet/address | Die Subnetzadresse der VM. | 2017-04-02
subnet/prefix | Das Subnetzpräfix. Beispiel: 24 | 2017-04-02
ipv6/ipAddress | Die lokale IPv6-Adresse der VM. | 2017-04-02
macAddress | Die MAC-Adresse der VM. | 2017-04-02

> [!NOTE]
> Alle API-Antworten stellen JSON-Zeichenfolgen dar. Alle folgenden Beispielantworten werden zur besseren Lesbarkeit im Schöndruck gedruckt.

#### <a name="sample-1-retrieve-network-information"></a>Beispiel 1: Abrufen von Netzwerkinformationen

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Antwort**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieve-public-ip-address"></a>Beispiel 2: Abrufen der öffentlichen IP-Adresse

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Speichermetadaten

Speichermetadaten sind Teil der Instanz-API unter dem Endpunkt `instance/compute/storageProfile`.
Er kann Details zu den Speicherdatenträgern bereitstellen, die der VM zugeordnet sind. 

Das Speicherprofil eines virtuellen Computers ist in drei Kategorien unterteilt: Imagereferenz, Betriebssystemdatenträger und Datenträger für Daten.

Das Imagereferenzobjekt enthält die folgenden Informationen zum Betriebssystemimage:

Daten    | BESCHREIBUNG
--------|-----------------
id      | Ressourcen-ID
offer   | Das Angebot der Plattform oder des Images.
publisher | Imageherausgeber
sku     | Image-SKU
version | Die Version der Plattform oder des Images.

Das Objekt des Betriebssystemdatenträgers enthält die folgenden Informationen über den von der VM verwendeten Betriebssystemdatenträger:

Daten    | BESCHREIBUNG
--------|-----------------
caching | Cachinganforderungen
createOption | Informationen zur Erstellung des virtuellen Computers
diffDiskSettings | Einstellungen für kurzlebige Datenträger
diskSizeGB | Größe des Datenträgers in GB
encryptionSettings | Verschlüsselungseinstellungen für den Datenträger
image   | Virtuelle Festplatte des Quellenbenutzerimages
managedDisk | Parameter für verwaltete Datenträger
name    | Name des Datenträgers
osType  | Der Typ des auf dem Datenträger enthaltenen Betriebssystems.
vhd     | Virtuelle Festplatte
writeAcceleratorEnabled | Ob `writeAccelerator` auf dem Datenträger aktiviert ist.

Das Datenträgerarray für Daten enthält eine Liste der Datenträger für Daten, die an den virtuellen Computer angefügt sind. Jedes Datenträgerobjekt enthält die folgenden Informationen:

Daten    | BESCHREIBUNG
--------|-----------------
caching | Cachinganforderungen
createOption | Informationen zur Erstellung des virtuellen Computers
diffDiskSettings | Einstellungen für kurzlebige Datenträger
diskSizeGB | Größe des Datenträgers in GB
image   | Virtuelle Festplatte des Quellenbenutzerimages
lun     | Logische Gerätenummer des Datenträgers
managedDisk | Parameter für verwaltete Datenträger
name    | Name des Datenträgers
vhd     | Virtuelle Festplatte
writeAcceleratorEnabled | Ob `writeAccelerator` auf dem Datenträger aktiviert ist.

Im folgenden Beispiel wird die Abfrage der Speicherinformationen des virtuellen Computers gezeigt.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM-Tags

VM-Tags sind in der Instanz-API unter dem Endpunkt `instance/compute/tags` enthalten.
Möglicherweise wurden Tags auf Ihre Azure-VM angewendet, um sie logisch in einer Taxonomie zu strukturieren. Sie können die einer VM zugewiesenen Tags mithilfe der folgenden Anforderung abrufen.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Antwort**

```text
Department:IT;Environment:Test;Role:WebRole
```

Das `tags` Feld ist eine Zeichenfolge, in der die Tags durch Semikolons getrennt sind. Diese Ausgabe kann ein Problem darstellen, wenn in den Tags Semikolons verwendet werden. Wenn ein Parser geschrieben wurde, um die Tags programmgesteuert zu extrahieren, sollten Sie auf das Feld `tagsList` zurückgreifen. Das Feld `tagsList` ist ein JSON-Array ohne Trennzeichen und folglich einfacher zu analysieren.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**Antwort**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Bestätigte Daten

Mithilfe des IMDS kann sichergestellt werden, dass die bereitgestellten Daten aus Azure stammen. Microsoft signiert einen Teil dieser Informationen, sodass Sie bestätigen können, dass ein Image in Azure Marketplace dasjenige ist, das Sie auf Azure ausführen.

### <a name="sample-1-get-attested-data"></a>Beispiel 1: Abrufen bestätigter Daten

> [!NOTE]
> Alle API-Antworten stellen JSON-Zeichenfolgen dar. Die folgenden Beispielantworten werden zur besseren Lesbarkeit automatische strukturiert und eingerückt.

**Anforderung**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> Wegen des Cachemechanismus von IMDS kann ein zuvor zwischengespeicherter `nonce`-Wert zurückgegeben werden.

`Api-version` ist ein obligatorisches Feld. Informationen zu unterstützten API-Versionen finden Sie im [Abschnitt zur Verwendung](#usage).
`Nonce` ist eine optionale 10-stellige Zeichenfolge. Wenn sie nicht bereitgestellt wird, gibt der IMDS an ihrer Stelle den aktuellen Zeitstempel der koordinierten Weltzeit zurück.

**Antwort**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Das Signaturblob ist eine signierte [pkcs7](https://aka.ms/pkcs7)-Version des Dokuments. Es enthält das zum Signieren verwendete Zertifikat zusammen mit bestimmten VM-spezifischen Details. 

Für VMs, die mithilfe von Azure Resource Manager erstellt wurden, umfasst dies `vmId`, `sku`, `nonce`, `subscriptionId`, `timeStamp` für die Erstellung und den Ablauf des Dokuments sowie die Planinformationen über das Image. Die Planinformationen werden nur für Azure Marketplace-Images ausgefüllt. 

Für VMs, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden, wird sichergestellt, dass nur `vmId` ausgefüllt wird. Sie können das Zertifikat aus der Antwort extrahieren und mithilfe dieses Zertifikats bestätigen, dass die Antwort gültig ist und von Azure stammt.

Das Dokument enthält die folgenden Felder:

Daten | BESCHREIBUNG | Eingeführt in Version
-----|-------------|-----------------------
licenseType | Typ der Lizenz für [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit). Beachten Sie, dass dies nur für AHB-aktivierte VMs verfügbar ist. | 2020-09-01
nonce | Eine Zeichenfolge, die optional mit der Anforderung angegeben werden kann. Wenn kein `nonce` angegeben wurde, wird der aktuelle Zeitstempel der koordinierten Weltzeit verwendet. | 2018-10-01
Tarif | Der [Plan für das Azure Marketplace-Image](/rest/api/compute/virtualmachines/createorupdate#plan). Enthält die Plan-ID (name), das Produktimage oder -angebot (product) und die Herausgeber-ID (publisher). | 2018-10-01
timestamp/createdOn | Der Zeitstempel der koordinierten Weltzeit für den Zeitpunkt, an dem das signierte Dokument erstellt wurde. | 2018-20-01
timestamp/expiresOn | Der Zeitstempel der koordinierten Weltzeit für den Zeitpunkt, an dem das signierte Dokument abläuft. | 2018-10-01
vmId |  Der [eindeutiger Bezeichner](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) für die VM. | 2018-10-01
subscriptionId | Das Azure-Abonnement für die VM. | 2019-04-30
sku | Die spezifische SKU für das VM-Image. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Beispiel 2: Überprüfen der Ausführung des virtuellen Computers in Azure

Anbieter in Azure Marketplace möchten sicherstellen, dass ihre Software nur für die Ausführung in Azure lizenziert ist. Wenn jemand die virtuelle Festplatte (VHD) in eine lokale Umgebung kopiert, muss der Anbieter in der Lage sein, dies zu erkennen. Über den IMDS können diese Anbieter signierte Daten erhalten, die sicherstellen, dass Antworten nur von Azure stammen.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Vergewissern Sie sich, dass die Signatur von Microsoft Azure stammt, und überprüfen Sie die Zertifikatkette auf Fehler.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> Wegen des Cachemechanismus von IMDS kann ein zuvor zwischengespeicherter `nonce`-Wert zurückgegeben werden.

Die `nonce` im signierten Dokument kann verglichen werden, wenn Sie in der ursprünglichen Anforderung einen `nonce`-Parameter angegeben haben.

> [!NOTE]
> Die Zertifikate für die öffentliche Cloud und jede Sovereign Cloud unterscheiden sich voneinander.

Cloud | Zertifikat
------|------------
[Alle allgemein verfügbaren globalen Azure-Regionen](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Die Zertifikate stimmen mit `metadata.azure.com` für die öffentliche Cloud möglicherweise nicht exakt überein. Aus diesem Grund sollte die Überprüfung der Zertifizierung einen allgemeinen Namen aus einer beliebigen Unterdomäne von `.metadata.azure.com` zulassen.

Falls das Zwischenzertifikat bei der Überprüfung aufgrund von Netzwerkeinschränkungen nicht heruntergeladen werden kann, können Sie das Zwischenzertifikat anheften. Beachten Sie, dass Azure ein Rollover für die Zertifikate durchführt, was eine standardmäßige PKI-Methode darstellt. Sie müssen die angehefteten Zertifikate aktualisieren, wenn ein Rollover erfolgt. Wenn eine Änderung geplant ist, bei der das Zwischenzertifikat aktualisiert werden muss, wird der Azure-Blog auf den neuesten Stand gebracht, und Azure-Kunden werden benachrichtigt. 

Die Zwischenzertifikate finden Sie im [PKI-Repository](https://www.microsoft.com/pki/mscorp/cps/default.htm). Die Zwischenzertifikate können sich für die einzelnen Regionen unterscheiden.

> [!NOTE]
> Das Zwischenzertifikat für Azure China 21ViaNet stammt von DigiCert Global Root CA anstelle von Baltimore.
Wenn Sie im Rahmen der Änderung der Stammzertifizierungsstelle Zwischenzertifikate für Azure China angeheftet haben, müssen zudem die Zwischenzertifikate aktualisiert werden.

## <a name="failover-clustering-in-windows-server"></a>Failoverclustering in Windows Server

Wenn Sie den IMDS mit Failoverclustering abfragen, ist es gelegentlich erforderlich, der Routingtabelle eine Route hinzuzufügen. Gehen Sie dabei folgendermaßen vor:

1. Öffnen Sie eine Eingabeaufforderung mit Administratorrechten.

1. Führen Sie den folgenden Befehl aus, und notieren Sie die Adresse der Schnittstelle für das Netzwerkziel (`0.0.0.0`) in der IPv4-Routingtabelle.

```bat
route print
```

> [!NOTE]
> Die folgende Beispielausgabe stammt aus einer Windows Server-VM mit aktiviertem Failovercluster. Der Einfachheit halber enthält die Ausgabe nur die IPv4-Routingtabelle.

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Führen Sie den folgenden Befehl aus, und verwenden Sie dabei die Adresse der Schnittstelle für das Netzwerkziel (`0.0.0.0`), in diesem Beispiel `10.0.1.10`.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>Verwaltete Identität

Eine verwaltete Identität, die vom System zugewiesen wird, kann auf der VM aktiviert werden. Sie können der VM auch eine oder mehrere vom Benutzer zugeordnete verwaltete Identitäten zuweisen.
Sie können dann Token für verwaltete Identitäten vom IMDS anfordern. Verwenden Sie diese Token für die Authentifizierung bei anderen Azure-Diensten, z. B. Azure Key Vault.

Ausführliche Schritte zum Aktivieren dieses Features finden Sie unter [Abrufen eines Zugriffstokens](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Geplante Ereignisse
Sie können den Status der geplanten Ereignisse mithilfe des IMDS abrufen. Dann kann der Benutzer eine Reihe von Aktionen festlegen, die bei diesen Ereignissen ausgeführt werden sollen. Weitere Informationen finden Sie unter [Geplante Ereignisse](scheduled-events.md). 

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Der Dienst ist in allen Azure-Clouds allgemein verfügbar.

## <a name="sample-code-in-different-languages"></a>Beispielcode in verschiedenen Sprachen

Die folgende Tabelle listet Beispiele für den Aufruf des IMDS mithilfe verschiedener Sprachen innerhalb der VM auf:

Sprache      | Beispiel
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>Fehler und Debuggen

Wenn ein Datenelement nicht gefunden wird oder eine falsch formatierte Anforderung vorliegt, gibt der IMDS HTTP-Standardfehler zurück. Zum Beispiel:

HTTP-Statuscode | `Reason`
-----------------|-------
200 – OK |
400 – Ungültige Anforderung | Fehlender `Metadata: true`-Header oder fehlender `format=json`-Parameter beim Abfragen eines Blattknotens.
404 – Nicht gefunden  | Das angeforderte Element ist nicht vorhanden.
405 – Methode unzulässig | Es werden ausschließlich `GET`-Anforderungen unterstützt.
410 Nicht mehr vorhanden | Wiederholen Sie den Vorgang später (nach max. 70 Sekunden).
429 – Zu viele Anforderungen | Die API unterstützt derzeit maximal 5 Abfragen pro Sekunde.
500 – Dienstfehler     | Wiederholen Sie den Vorgang später.

### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Ich erhalte die Fehlermeldung `400 Bad Request, Required metadata header not specified`. Was bedeutet das?**

Für den IMDS muss der Header `Metadata: true` in der Anforderung übergeben werden. Die Übergabe dieses Headers im REST-Aufruf ermöglicht den Zugriff auf den IMDS.

**Warum erhalte ich keine Compute-Informationen für meine VM?**

Derzeit unterstützt der IMDS nur Instanzen, die mit Azure Resource Manager erstellt wurden.

**Ich habe meine VM vor einiger Zeit mithilfe von Azure Resource Manager erstellt. Warum sehe ich keine Computemetadateninformationen?**

Wenn Sie Ihre VM nach September 2016 erstellt haben, fügen Sie ein [Tag](../../azure-resource-manager/management/tag-resources.md) hinzu, damit Computemetadaten angezeigt werden. Wenn Sie Ihre VM vor September 2016 erstellt haben, fügen Sie der VM-Instanz Erweiterungen oder Datenträger für Daten hinzu oder entfernen Sie diese, um die Metadaten zu aktualisieren.

**Warum werden nicht alle Daten für eine neue Version ausgefüllt?**

Wenn Sie Ihre VM nach September 2016 erstellt haben, fügen Sie ein [Tag](../../azure-resource-manager/management/tag-resources.md) hinzu, damit Computemetadaten angezeigt werden. Wenn Sie Ihre VM vor September 2016 erstellt haben, fügen Sie der VM-Instanz Erweiterungen oder Datenträger für Daten hinzu oder entfernen Sie diese, um die Metadaten zu aktualisieren.

**Warum erhalte ich die Fehler `500 Internal Server Error` oder `410 Resource Gone`?**

Wiederholen Sie die Anforderung. Weitere Informationen finden Sie unter [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults). Wenn das Problem weiterhin besteht, erstellen Sie im Azure-Portal ein Supportproblem für den virtuellen Computer.

**Gilt dies auch für VM-Skalierungsgruppeninstanzen?**

Ja, IMDS ist für Instanzen von VM-Skalierungsgruppen verfügbar.

**Ich habe die Tags in VM-Skalierungsgruppen aktualisiert, aber sie werden (im Gegensatz zu Einzelinstanz-VMs) nicht in den Instanzen angezeigt. Mache ich etwas falsch?**

Derzeit werden Tags für VM-Skalierungsgruppen nur nach einem Neustart, einer Neuerstellung des Images oder der Änderung eines Datenträgers für die Instanz angezeigt.

**Warum ist bei meiner Anforderung für meinen Dienstaufruf ein Timeout aufgetreten?**

Metadatenaufrufe müssen über die primäre IP-Adresse erfolgen, die der primären Netzwerkkarte des virtuellen Computers zugewiesen ist. Wenn Sie die Routen geändert haben, muss außerdem eine Route für die Adresse 169.254.169.254/32 in der lokalen Routingtabelle des virtuellen Computers vorhanden sein.
   * <details>
        <summary>Überprüfen der Routingtabelle</summary>

        1. Erstellen Sie eine Sicherung der lokalen Routingtabelle, und suchen Sie den IMDS-Eintrag. Zum Beispiel:
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Überprüfen Sie, ob eine Route für `169.254.169.254` vorhanden ist, und notieren Sie sich die entsprechende Netzwerkschnittstelle (z. B. `172.16.69.7`).
        1. Erstellen Sie eine Sicherung der Schnittstellenkonfiguration, und suchen Sie die Schnittstelle, die der in der Routingtabelle referenzierten Schnittstelle entspricht. Notieren Sie sich die (physische) MAC-Adresse.
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Vergewissern Sie sich, dass die Schnittstelle der primären NIC und der primären IP-Adresse des virtuellen Computers entspricht. Sie finden die primäre NIC und primäre IP-Adresse in der Netzwerkkonfiguration im Azure-Portal oder durch Suchen mithilfe der Azure-Befehlszeilenschnittstelle. Notieren Sie sich die öffentlichen und privaten IP-Adressen (und bei Verwendung der Azure-Befehlszeilenschnittstelle die MAC-Adresse). Hier ist ein PowerShell-CLI-Beispiel:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Wenn sie nicht übereinstimmen, aktualisieren Sie die Routingtabelle so, dass die primäre NIC und die primäre IP-Adresse als Ziel festgelegt sind.
    </details>

## <a name="support"></a>Support

Wenn Sie nach mehreren Versuchen keine Antwort auf die Metadaten erhalten, können Sie ein Supportproblem im Azure-Portal erstellen.
Wählen Sie für **Problemtyp** die Option **Verwaltung** aus. Wählen Sie für **Kategorie** die Option **Instance Metadata Service** aus.

![Screenshot der Instance Metadata Service-Unterstützung](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Nächste Schritte

[Abrufen eines Zugriffstokens für die VM](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Geplante Ereignisse](scheduled-events.md)
