---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177899"
---
Mit Azure Resource Manager definieren Sie die Parameter für Werte, die Sie bei der Bereitstellung der Vorlage angeben möchten. Die Vorlage enthält einen Abschnitt namens "Parameters", der alle Parameterwerte enthält.
Definieren Sie einen Parameter für die Werte, die basierend auf dem bereitgestellten Projekt oder der bereitgestellten Umgebung variieren. Definieren Sie keine Parameter für Werte, die sich nicht ändern. Jeder Parameterwert wird in der Vorlage verwendet, um die bereitgestellten Ressourcen zu definieren. 

Verwenden Sie beim Definieren von Parametern das Feld **allowedValues** , um anzugeben, welche Werte ein Benutzer während der Bereitstellung angeben kann. Verwenden Sie das Feld **defaultValue** , um dem Parameter einen Wert zuweisen, wenn kein Wert während der Bereitstellung angegeben wird.

Jeder Parameter wird in der Vorlage beschrieben.

### <a name="sitename"></a>siteName
Der Name der Web-App, die Sie erstellen möchten.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Der Name des App Service-Plans zum Hosten der Web-App.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
Der Tarif für den Hostingplan.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind, und weist einen Standardwert (S1) zu, wenn kein Wert angegeben wird.

### <a name="workersize"></a>workerSize
Die Instanzgröße des Hostingplans (klein, mittel oder groß).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (0, 1 oder 2), und weist einen Standardwert (0) zu, wenn kein Wert angegeben wird. Die Werte entsprechen klein, mittel und groß.

