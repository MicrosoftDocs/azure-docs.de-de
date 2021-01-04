---
title: Auswirkungen des Azure Stack Edge-Updates vom Januar 2021 | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu den Auswirkungen der IoT Edge-Rollenverwaltung auf Azure Stack Edge-Geräte nach der Installation des Updates vom Januar 2021.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578738"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>Änderungen der IoT Edge-Rollenverwaltung für Ihr Azure Stack Edge-Gerät

Verwenden Sie für die Azure IoT Edge-Rollenverwaltung für Ihr Azure Stack Edge-Gerät die aktualisierte Version von API, SDK und Azure PowerShell, deren Veröffentlichung für Januar 2021 geplant ist. 

Dieser Artikel enthält ausführliche Informationen zu den Änderungen, die vorgenommen werden müssen, um diese neueste Version verwenden zu können.

Das Update vom Januar 2021 ist nur für Geräte vom Typ „Azure Stack Edge Pro – GPU“, „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“ verfügbar. Die Informationen in diesem Artikel gelten nur für diese Geräte.

> [!NOTE]
> Das Upgrade auf die Version vom Januar 2021 ist optional. Wenn Sie weiterhin Ihre aktuelle Version verwenden möchten, hat dies keine Auswirkung auf die IoT Edge-Rollenverwaltung. Es empfiehlt sich jedoch, die neuere Version zu installieren, um die neuen Features nutzen zu können und die Sicherheit zu verbessern. 

## <a name="iot-edge-role-management-changes"></a>Änderungen der IoT Edge-Rollenverwaltung

Nachdem Sie das optionale Update vom Januar 2021 auf Ihrem Azure Stack Edge-Gerät installiert haben, muss für die IoT Edge-Rollenverwaltung die neueste Version von API, SDK und PowerShell-Cmdlets verwendet werden.

Die folgenden Änderungen sind nur erforderlich, wenn Sie das Update vom Januar 2021 anwenden:

- Falls Sie aktuell die Version&nbsp;2019-08-01 Rollenverwaltungs-API verwenden, führen Sie ein Upgrade auf die API-Version durch, die im Januar 2021 veröffentlicht wird. 
- Falls Sie die Rollenverwaltung aktuell mit der SDK-Version&nbsp;1.0.0 nutzen, führen Sie ein Upgrade auf die Version durch, die im Januar 2021 veröffentlicht wird.
- Falls Sie die Rollenverwaltung mit Azure PowerShell-Cmdlets (Vorschauversion) wie `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` oder `Remove-AzStackEdgeRole` nutzen, warten Sie auf die neuen Cmdlets, die im Februar 2021 veröffentlicht werden.

## <a name="api-usage"></a>API-Verwendung

Wenn Sie die IoT Edge-Rollenverwaltung derzeit per API durchführen, sollten Sie die neue API-Version 2020-12-01 verwenden, die zu einem späteren Zeitpunkt veröffentlicht wird. Bei Verwendung der aktuellen Rollen-API müssen Sie nach der Installation der kommenden Version der Gerätesoftware zur Kubernetes-Rolle „PUT“, „GET“ oder „DELETE“ wechseln – gefolgt von der IoT Add-On-API für „PUT“.

### <a name="for-the-put-method"></a>PUT-Methode

#### <a name="the-current-http-request"></a>Aktuelle HTTP-Anforderung 

- Die API-Aufrufe werden über diesen URI durchgeführt: https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01

- Der Anforderungstext sieht folgendermaßen aus:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>Kommende HTTP-Anforderung 

- Die API-Aufrufe für die Kubernetes-Rolle werden über den folgenden URI durchgeführt: 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    Der Anforderungstext sieht wie folgt aus:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- Die API-Aufrufe für das IoT Edge-Add-On werden über den folgenden URI durchgeführt: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    Der Anforderungstext sieht wie folgt aus:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>GET-Methode

#### <a name="the-current-http-response"></a>Aktuelle HTTP-Antwort

- Die API-Aufrufe werden über den folgenden URI durchgeführt: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- Der Antworttext sieht folgendermaßen aus:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>Kommende HTTP-Antwort

- Die API-Aufrufe werden über den folgenden URI durchgeführt:  

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- Der Antworttext sieht folgendermaßen aus: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>DELETE-Methode

### <a name="the-current-api-calls"></a>Aktuelle API-Aufrufe

Die API-Aufrufe werden über den folgenden URI durchgeführt:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>Kommende API-Aufrufe

Die API-Aufrufe werden über den folgenden URI durchgeführt:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK-Verwendung

Bei Verwendung des SDK müssen Sie nach der Installation des Updates vom Januar 2021 die Einrichtung der IoT Edge-Rolle ändern, wie im folgenden Beispiel gezeigt. Laden Sie anschließend das kommende NuGet-Paket herunter, und installieren Sie es, um zum neuen SDK zu wechseln, wie hier gezeigt.

### <a name="the-current-sdk-sample"></a>Beispiel für aktuelles SDK

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>Beispiel für neues SDK

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Cmdlet-Verwendung

Falls Sie aktuell das Cmdlet `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` oder `Remove-AzStackEdgeRole` verwenden, müssen Sie auf die neue Version warten, deren Veröffentlichung für Februar 2021 geplant ist.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Ich verwende Azure Stack Edge Pro – FPGA. Wirkt sich das Update vom Januar 2021 auf das FPGA-Modell aus?**

Nein. Das Update vom Januar 2021 betrifft nur Geräte vom Typ „Azure Stack Edge Pro – GPU“, „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“. Azure Stack Edge Pro – FPGA ist von diesem Update nicht betroffen, und es sind keine Änderungen der IoT Edge-Rollenverwaltung erforderlich.

**Hat die Aktualisierung von Azure Stack Edge Pro – GPU auf die neue Gerätesoftware vom Januar 2021 Auswirkungen auf bereits vorhandene Dienste?**

Nein. Ihre konfigurierten Dienste werden durch die Installation des Geräteupdates vom Januar 2021 nicht beeinträchtigt.

**Welche allgemeinen Änderungen gelten für API, SDK oder Cmdlets für die IoT Edge-Geräteverwaltung?**

Da es sich bei IoT Edge um ein Add-On unter der Kubernetes-Rolle handelt, müssen Sie zunächst sicherstellen, dass Kubernetes konfiguriert ist, und dann die IoT Edge-Konfiguration durchführen.


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Anwenden von Updates](azure-stack-edge-gpu-install-update.md).

