---
title: 'Azure Backup: Wiederherstellen virtueller Azure-Computer mithilfe der REST-API'
description: Verwalten von Wiederherstellungsvorgängen der Azure-VM-Sicherung mit der REST-API
services: backup
author: pvrk
manager: shivamg
keywords: REST-API; Azure-VM-Sicherung; Azure-VM-Wiederherstellung;
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: pullabhk
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4a65e8a855b9be797c1ceeacf4b74fea74697d00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646655"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Wiederherstellen virtueller Azure-Computer mit der REST-API

Nachdem die Sicherung eines virtuellen Azure-Computers mit Azure Backup abgeschlossen ist, können Sie vollständige virtuelle Azure-Computer oder Datenträger über die gleiche Sicherungskopie wiederherstellen. In diesem Artikel wird beschrieben, wie Sie einen virtuellen Azure-Computer oder Datenträger mit der REST-API wiederherstellen.

Für jeden Wiederherstellungsvorgang muss zunächst der entsprechende Wiederherstellungspunkt angegeben werden.

## <a name="select-recovery-point"></a>Auswählen eines Wiederherstellungspunkts

Die verfügbaren Wiederherstellungspunkte eines Sicherungselements können mithilfe der [REST-API zum Auflisten von Wiederherstellungspunkten](https://docs.microsoft.com/rest/api/backup/recoverypoints/list) aufgelistet werden. Dabei handelt es sich um einen einfachen *GET*-Vorgang mit allen relevanten Werten.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

Informationen zur Erstellung von `{containerName}` und `{protectedItemName}` finden Sie [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` ist „Azure“.

Der *GET*-URI enthält alle erforderlichen Parameter. Ein zusätzlicher Anforderungstext ist nicht erforderlich.

### <a name="responses"></a>Antworten

|NAME  |Type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Beispielantwort

Wenn der *GET*-URI übermittelt wird, wird die Antwort „200 (OK)“ zurückgegeben.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Der Wiederherstellungspunkt wird durch das Feld `{name}` in der obigen Antwort angegeben.

## <a name="restore-disks"></a>Wiederherstellen von Datenträgern

Muss die Erstellung eines virtuellen Computers auf der Grundlage der Sicherungsdaten angepasst werden, können Sie Datenträger einfach in einem ausgewählten Speicherkonto wiederherstellen und basierend auf diesen Datenträgern einen virtuellen Computer erstellen, der die Anforderungen der virtuellen Datenträger erfüllt. Das Speicherkonto muss sich in derselben Region wie der Recovery Services-Tresor befinden und darf nicht zonenredundant sein. Die Datenträger und die Konfiguration des gesicherten virtuellen Computers (vmconfig.json) werden im angegebenen Speicherkonto gespeichert.

Das Auslösen der Datenträgerwiederherstellung erfolgt über eine *POST*-Anforderung. Weitere Informationen zum Wiederherstellen von Datenträgern finden Sie im Artikel zur [REST-API zum Auslösen der Wiederherstellung](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

Informationen zur Erstellung von `{containerName}` und `{protectedItemName}` finden Sie [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` ist „Azure“ und `{recoveryPointId}` ist das Feld `{name}` des [oben](#example-response) erwähnten Wiederherstellungspunkts.

### <a name="create-request-body"></a>Erstellen des Anforderungstexts

Zum Auslösen einer Datenträgerwiederherstellung auf der Grundlage einer Azure-VM-Sicherung werden im Folgenden die Komponenten des Anforderungstexts angegeben.

|NAME  |Type  |BESCHREIBUNG  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Die vollständige Liste mit Definitionen des Anforderungstexts und weitere Einzelheiten finden Sie im [Dokument zur REST-API zum Auslösen der Wiederherstellung](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Beispielanforderung

Der folgende Anforderungstext definiert Eigenschaften, die zum Auslösen einer Datenträgerwiederherstellung erforderlich sind.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="response"></a>response

Das Auslösen einer Datenträgerwiederherstellung ist ein [asynchroner Vorgang](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|NAME  |Type  |BESCHREIBUNG  |
|---------|---------|---------|
|202 – Akzeptiert     |         |     Zulässig    |

#### <a name="example-responses"></a>Beispielantworten

Nachdem Sie den *POST*-URI für das Auslösen der Datenträgerwiederherstellung gesendet haben, wird als erste Antwort „202 (Akzeptiert)“ mit einem location- oder Azure-async-Header zurückgegeben.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Verfolgen Sie anschließend den resultierenden Vorgang mithilfe des location- oder Azure-AsyncOperation-Headers mit einem einfachen *GET*-Befehl nach.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

Wenn der Vorgang abgeschlossen ist, wird „200 (OK)“ mit der ID des resultierenden Wiederherstellungsauftrags im Antworttext zurückgegeben.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Da es sich beim Sicherungsauftrag um einen Vorgang mit langer Ausführungsdauer handelt, muss er gemäß der Erläuterung im [Dokument zur Auftragsüberwachung mit der REST-API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job) nachverfolgt werden.

Sobald der Vorgang mit langer Ausführungsdauer abgeschlossen ist, sind die Datenträger und die Konfiguration des gesicherten virtuellen Computers (VMConfig.json) im angegebenen Speicherkonto vorhanden.

## <a name="restore-as-another-virtual-machine"></a>Wiederherstellen als ein anderer virtueller Computer

[Wählen Sie den Wiederherstellungspunkt aus](#select-recovery-point), und erstellen Sie den Anforderungstext wie unten angegeben, um einen anderen virtuellen Azure-Computer mit den Daten des Wiederherstellungspunkts zu erstellen.

Der folgende Anforderungstext definiert Eigenschaften, die zum Auslösen der Wiederherstellung eines virtuellen Computers erforderlich sind.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2016-12-01",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

Die Antwort muss gemäß der Erläuterung im obigen Abschnitt zum [Wiederherstellen von Datenträgern](#response) verarbeitet werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Dokumenten:

- [Azure Recovery Services-Anbieter – REST-API](/rest/api/recoveryservices/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)