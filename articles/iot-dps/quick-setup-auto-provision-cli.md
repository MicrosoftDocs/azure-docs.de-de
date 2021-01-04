---
title: 'Schnellstart: Einrichten von Azure IoT Hub Device Provisioning Service mithilfe der Azure-Befehlszeilenschnittstelle'
description: 'Schnellstart: Einrichten von Azure IoT Hub Device Provisioning Service (DPS) mithilfe der Azure-Befehlszeilenschnittstelle'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 611068fa020321be88be6e1d6da663266029c658
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660184"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Schnellstart: Einrichten des IoT Hub Device Provisioning-Diensts mithilfe der Azure-Befehlszeilenschnittstelle

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In diesem Schnellstart erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle einen IoT-Hub und eine IoT Hub Device Provisioning Service-Instanz erstellen und die beiden Dienste miteinander verknüpfen. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Sowohl der IoT-Hub als auch der Bereitstellungsdienst, die Sie im Rahmen dieses Schnellstarts erstellen, sind öffentlich als DNS-Endpunkte ermittelbar. Vermeiden Sie daher die Verwendung vertraulicher Informationen, falls Sie die Namen für diese Ressourcen ändern.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Im folgenden Beispiel wird am Standort *westus* eine Ressourcengruppe mit dem Namen *my-sample-resource-group* erstellt.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Die Ressourcengruppe wird in diesem Beispiel am Standort „USA, Westen“ erstellt. Mit dem Befehl `az account list-locations -o table` können Sie eine Liste der verfügbaren Standorte anzeigen.
>
>

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Erstellen Sie mithilfe des Befehls [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) eine IoT Hub-Instanz.

Im folgenden Beispiel wird am Standort *westus* eine IoT Hub-Instanz mit dem Namen *my-sample-hub* erstellt. Ein IoT-Hub-Name muss in Azure global eindeutig sein, daher können Sie dem Beispielnamen ein eindeutiges Präfix oder Suffix hinzufügen oder einen ganz neuen Namen auswählen. Achten Sie darauf, dass der Name den Benennungskonventionen für IoT-Hubs entspricht: Er muss 3 bis 50 Zeichen lang sein und darf nur alphanumerische Zeichen (Klein- und Großbuchstaben) oder Bindestriche („-“) enthalten. 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Erstellen einer Device Provisioning Service-Instanz

Erstellen Sie mithilfe des Befehls [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) eine Device Provisioning Service-Instanz. 

Im folgenden Beispiel wird am Standort *westus* ein Bereitstellungsdienst mit dem Namen *my-sample-dps* erstellt. Sie müssen außerdem einen global eindeutigen Namen für Ihren eigenen Bereitstellungsdienst auswählen. Achten Sie darauf, dass er den Namenskonventionen für IoT Hub Device Provisioning Service entspricht: Er muss 3 bis 64 Zeichen lang sein und darf nur alphanumerische Zeichen (Klein- und Großbuchstaben) oder Bindestriche („-“) enthalten.

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Der Bereitstellungsdienst wird in diesem Beispiel am Standort „USA, Westen“ erstellt. Sie können eine Liste mit den verfügbaren Standorten anzeigen. Führen Sie hierzu entweder den Befehl `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` aus, oder navigieren Sie zur Seite [Azure-Status](https://azure.microsoft.com/status/), und suchen Sie nach „Device Provisioning-Dienst“. In Befehlen können Standorte entweder als einzelnes Wort oder mit mehreren Wörtern (beispielsweise „westus“, „West US“, „WEST US“ usw.) angegeben werden. Die Groß-/Kleinschreibung spielt hierbei keine Rolle. Wenn Sie den Standort in einem Format mit mehreren Wörtern angeben, müssen Sie den Wert in Anführungszeichen einschließen, wie im folgenden Beispiel zu sehen: `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Abrufen der Verbindungszeichenfolge für die IoT Hub-Instanz

Die Verbindungszeichenfolge Ihrer IoT Hub-Instanz wird benötigt, um sie mit der Device Provisioning Service-Instanz zu verknüpfen. Rufen Sie mithilfe des Befehls [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) die Verbindungszeichenfolge ab, und verwenden Sie die Ausgabe des Befehls, um eine Variable festzulegen, die später beim Verknüpfen der beiden Ressourcen verwendet wird. 

Im folgenden Beispiel wird die Variable *hubConnectionString* auf den Wert der Verbindungszeichenfolge für den primären Schlüssel für die Richtlinie *iothubowner* des Hubs festgelegt. (Der Parameter `--policy-name` kann zum Angeben einer anderen Richtlinie verwendet werden.) Ersetzen Sie *my-sample-hub* durch den eindeutigen Namen des IoT-Hubs, den Sie zuvor ausgewählt haben. Der Befehl verwendet die Optionen [query](/cli/azure/query-azure-cli) und [output](/cli/azure/format-output-azure-cli#tsv-output-format) der Azure-Befehlszeilenschnittstelle, um die Verbindungszeichenfolge aus der Befehlsausgabe zu extrahieren.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Sie können den Befehl `echo` verwenden, um die Verbindungszeichenfolge anzuzeigen.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Diese beiden Befehle eignen sich für einen unter Bash ausgeführten Host.
> 
> Passen Sie bei Verwendung einer lokalen Windows-/Befehlsshell oder eines PowerShell-Hosts die Befehle an die korrekte Syntax für die jeweilige Umgebung an.
>
> Überprüfen Sie bei Verwendung von Azure Cloud Shell, ob im Dropdownmenü für Umgebungen auf der linken Seite des Shellfensters **Bash** ausgewählt ist.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Verknüpfen der IoT Hub-Instanz und des Bereitstellungsdiensts

Verknüpfen Sie die IoT Hub-Instanz und Ihren Bereitstellungsdienst mithilfe des Befehls [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

Im folgenden Beispiel wird eine IoT Hub-Instanz namens *my-sample-hub* am Standort *westus* mit dem Gerätebereitstellungsdienst *my-sample-dps* verknüpft. Ersetzen Sie diese Namen durch die eindeutigen Namen des IoT-Hubs und des Gerätebereitstellungsdiensts, die Sie zuvor ausgewählt haben. Im Befehl wird die Verbindungszeichenfolge für Ihren IoT-Hub verwendet, die im vorherigen Schritt in der Variable *hubConnectionString* gespeichert wurde.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.

## <a name="verify-the-provisioning-service"></a>Überprüfen des Bereitstellungsdiensts

Rufen Sie mithilfe des Befehls [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show) die Details Ihres Bereitstellungsdiensts ab.

Im folgenden Beispiel werden die Details eines Bereitstellungsdiensts namens *my-sample-dps* abgerufen. Ersetzen Sie diesen Namen durch Ihren eigenen Namen für den Gerätebereitstellungdienst.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Die verknüpfte IoT Hub-Instanz wird in der Sammlung *properties.iotHubs* angezeigt.

![Überprüfen des Bereitstellungsdiensts](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstarts in dieser Sammlung bauen auf diesem Schnellstart auf. Wenn Sie planen, mit den nachfolgenden Schnellstarts oder Tutorials fortzufahren, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Wenn Sie nicht fortfahren möchten, können Sie die folgenden Befehle verwenden, um den Bereitstellungsdienst, die IoT Hub-Instanz oder die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen. Ersetzen Sie die Namen der unten aufgeführten Ressourcen durch die Namen Ihrer eigenen Ressourcen.

Führen Sie zum Löschen des Bereitstellungsdiensts den Befehl [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) aus:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Führen Sie zum Löschen der IoT Hub-Instanz den Befehl [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) aus:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Führen Sie zum Löschen einer Ressourcengruppe und all ihrer Ressourcen den Befehl [az group delete](/cli/azure/group#az-group-delete) aus:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen IoT-Hub und einen Gerätebereitstellungsdienst bereitgestellt und die beiden Ressourcen verknüpft. Sie können mit dem Schnellstart zur Erstellung eines simulierten Geräts fortfahren, um zu erfahren, wie Sie diese Einrichtung zum Bereitstellen eines simulierten Geräts verwenden.

> [!div class="nextstepaction"]
> [Schnellstart zum Erstellen eines simulierten Geräts](./quick-create-simulated-device.md)
