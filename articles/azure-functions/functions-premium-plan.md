---
title: Premium-Plan für Azure Functions (Vorschau) | Microsoft-Dokumentation
description: Details und Konfigurationsoptionen (VNet, kein Kaltstart, unbegrenzte Ausführungsdauer) für den Premium-Plan (Premium-Tarif) für Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: dab7561db8f223bff87f41ef756605359c3478e4
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492702"
---
# <a name="azure-functions-premium-plan-preview"></a>Premium-Plan (Premium-Tarif) für Azure Functions (Vorschau)

Der Premium-Plan für Azure Functions ist eine Hostingoption für Funktions-Apps. Der Premium-Plan bietet Features wie VNet-Konnektivität, keinen Kaltstart und Premium-Hardware.  Mehrere Funktions-Apps können im selben Premium-Plan bereitgestellt werden, und der Tarif ermöglicht es Ihnen, die Compute-Instanzgröße, Basisplangröße und maximale Plangröße zu konfigurieren.  Einen Vergleich des Premium-Plans und anderer Pläne und Hostingtypen finden Sie unter [Skalierung und Hosting von Azure Functions](functions-scale.md).

> [!NOTE]
> Die Vorschauversion des Premium-Plans unterstützt derzeit die Funktionen, die in .NET, Node oder Java über Windows-Infrastruktur ausgeführt werden.

## <a name="create-a-premium-plan"></a>Erstellen eines Premium-Plans

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Sie können einen Premium-Plan auch über die Azure CLI erstellen.

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Features

Die folgenden Features sind für Funktions-Apps verfügbar, die für einen Premium-Plan bereitgestellt werden.

### <a name="pre-warmed-instances"></a>Vorab aufgewärmte Instanzen

Wenn heute keine Ereignisse und Ausführungen im Verbrauchsplan ausgeführt werden, wird Ihre App möglicherweise auf null Instanzen herunterskaliert. Wenn neue Ereignisse eingehen, muss eine neue Instanz zum Ausführen Ihrer App eingerichtet werden.  Das Einrichten einer neuen Instanz kann je nach App einige Zeit dauern.  Diese zusätzliche Wartezeit beim ersten Aufruf wird häufig als App-Kaltstart bezeichnet.

Im Premium-Plan können Sie Ihre App in einem „vorab aufgewärmten“ Zustand für eine angegebene Anzahl von Instanzen vorhalten, bis zur Größe Ihres Mindestplans.  Mit vorab aufgewärmten Instanzen können Sie eine App auch vor hoher Last vorab skalieren. Erfolgt eine Erweiterung für die App, wird die App zunächst in die vorab aufgewärmten Instanzen skaliert. In Vorbereitung auf den nächsten Skalierungsvorgang werden sofort weitere Instanzen gepuffert und aufgewärmt. Dadurch, dass es einen Puffer mit vorab aufgewärmten Instanzen gibt, können Sie Kaltstartwartezeiten effektiv vermeiden.  Vorab aufgewärmte Instanzen ist ein Feature für den Premium-Plan, und Sie müssen während der gesamten Zeit, in der der Plan aktiv ist, mindestens eine Instanz aktiv und verfügbar halten.

Sie können die Anzahl der vorab aufgewärmten Instanzen im Azure-Portal konfigurieren, indem Sie Ihre **Funktions-App** auswählen, zur Registerkarte **Plattformfeatures** wechseln und die **Horizontal skalieren**-Optionen auswählen. Im Bearbeitungsfenster für eine Funktions-App gelten vorab aufgewärmte Instanzen speziell für diese App, aber die Mindestanzahl und die maximale Anzahl von Instanzen gelten für Ihren gesamten Plan.

![Einstellungen für elastisches Skalieren](./media/functions-premium-plan/scale-out.png)

Sie können vorab aufgewärmte Instanzen für eine App auch mit der Azure CLI konfigurieren.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Private Netzwerkkonnektivität

Für Azure Functions, bereitgestellt in einem Premium-Plan, wird die [neue VNET-Integration für Web-Apps](../app-service/web-sites-integrate-with-vnet.md) genutzt.  Ist diese Integration konfiguriert, kann Ihre App mit Ressourcen in Ihrem VNET oder geschützt über Dienstendpunkte kommunizieren.  IP-Einschränkungen sind ebenfalls für die App verfügbar, um eingehenden Datenverkehr zu beschränken.

Wenn Sie Ihrer Funktions-App in einem Premium-Plan ein Subnetz zuweisen, benötigen Sie ein Subnetz mit genügend IP-Adressen für jede mögliche-Instanz. Obwohl die maximale Anzahl von Instanzen in der Vorschauphase variieren kann, ist ein IP-Adressblock mit mindestens 100 verfügbaren Adressen erforderlich.

Weitere Informationen finden Sie unter [Integrieren einer Funktions-App in ein Azure Virtual Network](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Schnelle elastische Skalierung

Weitere Compute-Instanzen werden automatisch für Ihre App hinzugefügt. Dazu wird die gleiche Logik für schnelle Skalierung verwendet wie für den Verbrauchsplan.  Weitere Informationen zur Funktionsweise von Skalierung finden Sie unter [Skalierung und Hosting von Azure Functions](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Unbegrenzte Ausführungsdauer

Azure Functions in einem Verbrauchsplan sind auf 10 Minuten für eine einzelne Ausführung beschränkt.  Im Premium-Plan wird die Ausführungsdauer standardmäßig auf 30 Minuten festgelegt, um Endlosausführungen zu verhindern. Sie können jedoch [die host.json-Konfiguration ändern](./functions-host-json.md#functiontimeout), um die Ausführungsdauer für Premium-Plan-Apps auf unbegrenzt festzulegen.

In der Vorschauversion wird die Dauer nicht über 12 Minuten hinaus garantiert, und es besteht die beste Chance, dass die Ausführung länger als 30 Minuten erfolgt, wenn Ihre App nicht über ihre minimale Workeranzahl hinaus skaliert ist.

## <a name="plan-and-sku-settings"></a>Plan- und SKU-Einstellungen

Wenn Sie den Plan erstellen, konfigurieren Sie zwei Einstellungen: die Mindestanzahl von Instanzen (oder Plangröße) und den maximalen Burstgrenzwert.  Die Mindestanzahl von Instanzen für einen Premium-Plan ist 1, und der maximale Burst für die Vorschauversion ist 20.  So viele Instanzen, wie die Mindestanzahl von Instanzen angibt, werden reserviert und immer ausgeführt.

> [!IMPORTANT]
> Ihnen wird jede Instanz, die entsprechend der Mindestanzahl von Instanzen zugeordnet ist, in Rechnung gestellt, unabhängig davon, ob Funktionen ausgeführt werden oder nicht.

Wenn Ihre App mehr Instanzen erfordert, als Ihre Plangröße vorgibt, kann diese erweitert werden, bis die Anzahl von Instanzen den maximalen Burstgrenzwert erreicht hat.  Instanzen, die sich außerhalb Ihrer Plangröße befinden, werden Ihnen nur in Rechnung gestellt, während sie ausgeführt werden und für Sie bereitgestellt sind.  Es wird versucht, ein Erweitern Ihrer App bis zu deren definiertem maximalen Grenzwert bestmöglich vorzunehmen, während die im Plan festgelegte Mindestanzahl von Instanzen für Ihre App garantiert ist.

Sie können die Plangröße und die Maximalwerte im Azure-Portal konfigurieren, indem Sie die **Horizontal skalieren**-Optionen im Plan oder eine Funktions-App auswählen, die für diesen Plan bereitgestellt ist (unter **Plattformfeatures**).

Sie können auch den maximalen Burstgrenzwert über die Azure-Befehlszeilenschnittstelle erhöhen:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Verfügbare Instanz-SKUs

Wenn Sie Ihren Plan erstellen oder skalieren, können Sie zwischen drei Instanzgrößen wählen.  Ihnen werden die Gesamtanzahl von Kernen und der Arbeitsspeicher in Rechnung gestellt, die pro Sekunde genutzt werden.  Ihre App kann automatisch nach Bedarf auf mehrere Instanzen hochskaliert werden.  

|SKU|Kerne|Arbeitsspeicher|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>Regionen

Nachstehend sind die derzeit unterstützten Regionen für die öffentliche Vorschau aufgeführt.

|Region|
|--|
|Australien (Osten)|
|Australien, Südosten|
|Kanada, Mitte|
|USA (Mitte)|
|Asien, Osten|
|USA (Ost) 2|
|Frankreich, Mitte|
|Japan, Westen|
|Korea, Mitte|
|USA Nord Mitte|
|Nordeuropa|
|USA Süd Mitte|
|Indien (Süden)|
|Asien, Südosten|
|UK, Westen|
|Europa, Westen|
|Indien, Westen|
|USA (Westen)|

## <a name="known-issues"></a>Bekannte Probleme

Sie können den Status der bekannten Probleme der [öffentlichen Vorschauversion auf GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues) verfolgen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Grundlegendes zu Skalierung und Hosting von Azure Functions](functions-scale.md)
