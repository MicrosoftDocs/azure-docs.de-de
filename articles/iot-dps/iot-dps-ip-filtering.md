---
title: Azure IoT DPS – IP-Verbindungsfilter | Microsoft-Dokumentation
description: Informieren Sie sich, wie Sie die IP-Filterung zum Blockieren von Verbindungen von bestimmten IP-Adressen zu Ihrer Azure IoT DPS-Instanz verwenden. Sie können Verbindungen von einzelnen IP-Adressen oder Bereichen von IP-Adressen blockieren.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e1b175a176255da465433b2db45cb3cb67d360d1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934501"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Verwenden von IP-Verbindungsfiltern in Azure IoT DPS

Sicherheit ist ein wichtiger Aspekt jeder IoT-Lösung. Manchmal müssen Sie im Rahmen der Sicherheitskonfiguration die IP-Adressen explizit angeben, über die Geräte eine Verbindung herstellen können. Mit dem Feature *IP-Filter* für einen Azure IoT Hub Device Provisioning-Dienst (Device Provisioning Service, DPS) können Sie Regeln zur Ablehnung oder Akzeptierung des Datenverkehrs von bestimmten IPv4-Adressen konfigurieren.

## <a name="when-to-use"></a>Verwendung

Es gibt zwei spezielle Anwendungsfälle, in denen es nützlich ist, Verbindungen von bestimmten IP-Adressen zu einem DPS-Endpunkt zu blockieren:

* Ihr DPS sollte Datenverkehr nur aus einem bestimmten IP-Adressbereich empfangen und jeglichen anderen Datenverkehr ablehnen. Beispielsweise verwenden Sie Ihren DPS bei [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), um private Verbindungen zwischen einem DPS und Ihren Geräten zu erstellen.

* Sie müssen Datenverkehr von IP-Adressen ablehnen, die vom DPS-Administrator als verdächtig eingestuft wurden.

## <a name="how-filter-rules-are-applied"></a>Anwenden von Filterregeln

Die IP-Filterregeln werden auf DPS-Instanzebene angewendet. Daher gelten die IP-Filterregeln für alle Verbindungen von Geräten und Back-End-Apps mit allen unterstützten Protokollen.

Jeder Verbindungsversuch über eine IP-Adresse, der mit einer IP-Ablehnungsregel in Ihrer DPS-Instanz übereinstimmt, wird mit dem Statuscode „Unauthorized 401“ (401 – Nicht autorisiert) und einer Beschreibung versehen. In der Antwortnachricht wird die IP-Regel nicht erwähnt.

## <a name="default-setting"></a>Standardeinstellung

Das Raster **IP-Filter** im Portal ist für DPS standardmäßig leer. Diese Standardeinstellung bedeutet, dass Ihr DPS Verbindungen von allen IP-Adressen akzeptiert. Die Standardeinstellung entspricht einer Regel, bei der der IP-Adressbereich 0.0.0.0/0 zulässig ist.

![Standardeinstellungen der IP-Filterung von IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Hinzufügen oder Bearbeiten einer IP-Filterregel

Zum Hinzufügen einer IP-Filterregel wählen Sie **+ IP-Filterregel hinzufügen** aus.

![Hinzufügen einer IP-Filterregel zu einem IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Nachdem Sie **IP-Filterregel hinzufügen** ausgewählt haben, füllen Sie die Felder aus.

![Nach dem Auswählen von „IP-Filterregel hinzufügen“](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Geben Sie einen **Namen** für die IP-Filterregel an. Er muss eine eindeutige alphanumerische Zeichenfolge ohne Beachtung von Groß-/Kleinschreibung sein, die bis zu 128 Zeichen umfassen kann. Nur alphanumerische ASCII 7-Bit-Zeichen und die Zeichen `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sind zulässig.

* Geben Sie eine einzelne IPv4-Adresse oder einen Block von IP-Adressen in CIDR-Notation ein. In CIDR-Notation steht 192.168.100.0/22 beispielsweise für die 1.024 IPv4-Adressen von 192.168.100.0 bis 192.168.103.255.

* Wählen Sie **Zulassen** oder **Blockieren** als **Aktion** für die IP-Filterregel aus.

Nachdem Sie die Felder ausgefüllt haben, wählen Sie **Speichern** aus, um die Regel zu speichern. Daraufhin wird eine Warnung mit dem Hinweis angezeigt, dass der Updatevorgang läuft.

![Benachrichtigung über das Speichern einer IP-Filterregel](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Die Option **Hinzufügen** ist deaktiviert, wenn Sie das Maximum von 100 IP-Filterregeln erreichen.

Wenn Sie eine vorhandene Regel bearbeiten möchten, wählen Sie die zu ändernden Daten aus, nehmen Sie die Änderung vor, und wählen Sie **Speichern** aus, um Ihre Bearbeitung zu speichern.

> [!NOTE]
> Durch die Ablehnung von IP-Adressen lässt sich verhindern, dass andere Azure-Dienste mit der DPS-Instanz interagieren.

## <a name="delete-an-ip-filter-rule"></a>Löschen einer IP-Filterregel

Wählen Sie zum Löschen einer IP-Filterregel das Papierkorbsymbol in dieser Zeile und dann **Speichern** aus. Die Regel wird entfernt und die Änderung gespeichert.

![Löschen einer IP-Filterregel für IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aktualisieren von IP-Filterregeln in Code

Möglicherweise müssen Sie Ihren IP-Filter für DPS über den REST-Endpunkt des Azure-Ressourcenanbieters abrufen und ändern. Lesen Sie dazu `properties.ipFilterRules` unter [createorupdate-Methode](/rest/api/iot-dps/iotdpsresource/createorupdate).

Eine Aktualisierung von IP-Filterregeln für DPS wird bei Azure CLI oder Azure PowerShell derzeit nicht unterstützt, kann jedoch mit Azure Resource Manager Vorlagen erreicht werden. Unter [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md) finden Sie Anleitungen zur Verwendung von Resource Manager-Vorlagen. Die folgenden Vorlagenbeispiele zeigen, wie Sie IP-Filterregeln für DPS erstellen, bearbeiten und löschen.

### <a name="add-an-ip-filter-rule"></a>Hinzufügen einer IP-Filterregel

Im folgenden Vorlagenbeispiel wird die neue IP-Filterregel „AllowAll“ (AlleZulassen) erstellt, die den gesamten Datenverkehr akzeptiert.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Aktualisieren Sie die Attribute für die IP-Filterregel der Vorlage aufgrund Ihrer Anforderungen.

| attribute                | BESCHREIBUNG |
| ------------------------ | ----------- |
| **FilterName**           | Geben Sie einen Namen für die IP-Filterregel an. Er muss eine eindeutige alphanumerische Zeichenfolge ohne Beachtung von Groß-/Kleinschreibung sein, die bis zu 128 Zeichen umfassen kann. Nur die alphanumerischen ASCII-7-Bit-Zeichen sowie `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` werden akzeptiert. |
| **Aktion**               | Akzeptierte Werte sind **Accept**  (Akzeptieren) oder  **Reject**  (Ablehnen) als Aktion für die IP-Filterregel. |
| **ipMask**               | Geben Sie eine einzelne IPv4-Adresse oder einen Block von IP-Adressen in CIDR-Notation ein. In CIDR-Notation steht 192.168.100.0/22 beispielsweise für die 1.024 IPv4-Adressen von 192.168.100.0 bis 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualisieren einer IP-Filterregel

Im folgenden Vorlagenbeispiel wird die oben gezeigte IP-Filterregel „AllowAll“ aktualisiert, um den gesamten Datenverkehr abzulehnen.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Löschen einer IP-Filterregel

Im folgenden Vorlagenbeispiel werden alle IP-Filterregeln für die DPS-Instanz gelöscht.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Auswertung von IP-Filterregeln

IP-Filterregeln werden der Reihenfolge nach angewendet, und die erste Regel, die eine Übereinstimmung mit der IP-Adresse ergibt, bestimmt die Aktion (Zulassen oder Ablehnen).

Wenn Sie beispielsweise Adressen im Bereich 192.168.100.0/22 zulassen und alle anderen Adressen ablehnen möchten, sollte die erste Regel im Raster lauten, dass der Adressbereich 192.168.100.0/22 zulässig ist. Mit der nächsten Regel sollten alle Adressen abgelehnt werden, indem der Bereich 0.0.0.0/0 verwendet wird.

Sie können die Reihenfolge der IP-Filterregeln im Raster ändern, indem Sie auf die drei vertikal angeordneten Punkte am Anfang der Zeile klicken und Drag & Drop nutzen.

Klicken Sie auf **Speichern**, um die neue Reihenfolge der IP-Filterregeln zu speichern.

![Ändern der Reihenfolge Ihrer IP-Filterregeln für DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von DPS finden Sie unter:

* [Understanding IoT DPS IP addresses](iot-dps-understand-ip-address.md) (Informationen zu IP-Adressen für IoT DPS)
* [Einrichten von DPS über die Azure CLI](quick-setup-auto-provision-cli.md)
* [Steuern des Zugriffs auf DPS](how-to-control-access.md)
