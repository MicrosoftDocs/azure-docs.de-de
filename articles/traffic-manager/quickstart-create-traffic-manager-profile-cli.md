---
title: 'Schnellstart: Erstellen eines Profils für die Hochverfügbarkeit von Anwendungen – Azure CLI – Azure Traffic Manager'
description: In diesem Schnellstartartikel wird beschrieben, wie Sie mithilfe der Azure CLI ein Traffic Manager-Profil erstellen, um eine hochverfügbare Webanwendung zu entwickeln.
services: traffic-manager
author: duongau
mnager: kumud
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7dabf94c711972f9fe543edac0d7b95469fc2d35
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661102"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Schnellstart: Erstellen eines Traffic Manager-Profils für eine hochverfügbare Webanwendung mit der Azure-Befehlszeilenschnittstelle

In dieser Schnellstartanleitung wird beschrieben, wie Sie ein Traffic Manager-Profil erstellen, mit dem die Hochverfügbarkeit für Ihre Webanwendung sichergestellt wird.

In dieser Schnellstartanleitung erstellen Sie zwei Instanzen einer Webanwendung. Jede Instanz wird in einer anderen Azure-Region ausgeführt. Sie erstellen ein Traffic Manager-Profil basierend auf der [Endpunktpriorität](traffic-manager-routing-methods.md#priority-traffic-routing-method). Das Profil leitet den Benutzerdatenverkehr an den primären Standort, an dem die Webanwendung ausgeführt wird. Die Webanwendung wird von Traffic Manager ständig überwacht. Wenn der primäre Standort nicht verfügbar ist, erfolgt automatisch ein Failover zum Sicherungsstandort.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Erstellen Sie mit [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) ein Traffic Manager-Profil, das Benutzerdatenverkehr basierend auf der Endpunktpriorität weiterleitet.

Ersetzen Sie **<profile_name>** im folgenden Beispiel durch einen eindeutigen Traffic Manager-Profilnamen.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Erstellen von Web-Apps

Für diese Schnellstartanleitung benötigen Sie zwei Instanzen einer Webanwendung, die in zwei unterschiedlichen Azure-Regionen bereitgestellt werden (*USA, Osten* und *Europa, Westen*). Jede dient jeweils als primärer bzw. Failoverendpunkt für Traffic Manager.

### <a name="create-web-app-service-plans"></a>Erstellen von Web-App-Dienstplänen
Erstellen Sie Web-App-Dienstpläne, indem Sie [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) für die beiden Instanzen der Webanwendung verwenden, die Sie in zwei unterschiedlichen Azure-Regionen bereitstellen.

Ersetzen Sie **<appspname_eastus>** und **<appspname_westeurope>** im folgenden Beispiel durch einen eindeutigen App Service-Plannamen.

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Erstellen einer Web-App im App Service-Plan
Erstellen Sie zwei Instanzen der Webanwendung, indem Sie [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in den App Service-Plänen in den Azure-Regionen *USA, Osten* und *USA, Westen* verwenden.

Ersetzen Sie **<app1name_eastus>** und **<app2name_westeurope>** im folgenden Beispiel durch einen eindeutigen App-Namen und **<appspname_eastus>** und **<appspname_westeurope>** durch den Namen, den Sie zum Erstellen der App Service-Pläne im vorherigen Abschnitt verwendet haben.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten
Fügen Sie die beiden Web-Apps dem Traffic Manager-Profil wie folgt als Traffic Manager-Endpunkte hinzu, indem Sie [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) verwenden:

- Ermitteln Sie die Web-App-ID, und fügen Sie die Web-App aus der Azure-Region *USA, Osten* als primären Endpunkt für das Routing des gesamten Benutzerdatenverkehrs hinzu. 
- Ermitteln Sie die Web-App-ID, und fügen Sie die Web-App aus der Azure-Region *Europa, Westen* als Failoverendpunkt hinzu. 

Wenn der primäre Endpunkt nicht verfügbar ist, wird der Datenverkehr automatisch an den Failoverendpunkt weitergeleitet.

Ersetzen Sie im folgenden Beispiel **<app1name_eastus>** und **<app2name_westeurope>** durch die App-Namen, die Sie im vorherigen Abschnitt für die einzelnen Regionen erstellt haben. Ersetzen Sie dann **<profile_name>** durch den im vorherigen Abschnitt verwendeten Profilnamen. 

**Endpunkt „USA, Osten“**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Notieren Sie sich die in der Ausgabe angezeigte ID, und verwenden Sie sie im folgenden Befehl zum Hinzufügen des Endpunkts:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Endpunkt „Europa, Westen“**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Notieren Sie sich die in der Ausgabe angezeigte ID, und verwenden Sie sie im folgenden Befehl zum Hinzufügen des Endpunkts:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Testen Ihres Traffic Manager-Profils

In diesem Abschnitt überprüfen Sie den Domänennamen Ihres Traffic Manager-Profils. Außerdem konfigurieren Sie den primären Endpunkt so, dass er nicht verfügbar ist. Abschließend können Sie sehen, dass die Web-App weiterhin verfügbar ist. Dies liegt daran, dass Traffic Manager den Datenverkehr an den Failoverendpunkt sendet.

Ersetzen Sie im folgenden Beispiel **<app1name_eastus>** und **<app2name_westeurope>** durch die App-Namen, die Sie im vorherigen Abschnitt für die einzelnen Regionen erstellt haben. Ersetzen Sie dann **<profile_name>** durch den im vorherigen Abschnitt verwendeten Profilnamen.

### <a name="determine-the-dns-name"></a>Ermitteln des DNS-Namens

Ermitteln Sie den DNS-Namen des Traffic Manager-Profils mit [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Kopieren Sie den Wert von **RelativeDnsName**. Der DNS-Name Ihres Traffic Manager-Profils lautet „*http://<* relativednsname *>.trafficmanager.net*“. 

### <a name="view-traffic-manager-in-action"></a>Anzeigen von Traffic Manager in Aktion
1. Geben Sie in einem Webbrowser den DNS-Namen Ihres Traffic Manager-Profils (*http://<* relativednsname *>.trafficmanager.net*) ein, um die Standardwebsite Ihrer Web-App anzuzeigen.

    > [!NOTE]
    > In diesem Schnellstartszenario werden alle Anforderungen an den primären Endpunkt weitergeleitet. Es ist **Priorität 1** festgelegt.
2. Deaktivieren Sie Ihren primären Standort mit [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update), um das Traffic Manager-Failover in Aktion zu sehen.

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Kopieren Sie den DNS-Namen Ihres Traffic Manager-Profils (*http://<* relativednsname *>.trafficmanager.net*), um die Website in einer neuen Browsersitzung anzuzeigen.
4. Vergewissern Sie sich, dass die Web-App weiterhin verfügbar ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppen, Webanwendungen und alle dazugehörigen Ressourcen mit [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete), wenn Sie fertig sind.

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Traffic Manager-Profil erstellt, um für Ihre Webanwendung für Hochverfügbarkeit zu sorgen. Weitere Informationen zum Weiterleiten des Datenverkehrs finden Sie in den Tutorials zu Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager-Tutorials](tutorial-traffic-manager-improve-website-response.md)
