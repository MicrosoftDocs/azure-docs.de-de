---
title: 'Schnellstart: Erstellen einer Azure API Management-Instanz mithilfe der CLI (Vorschau)'
description: Hier erhalten Sie Informationen zum Erstellen einer Instanz des API Management-Diensts in Azure mithilfe der Azure CLI.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 863cb6cd10664e14966898045cb2b1861ef75b9f
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887449"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli-preview"></a>Schnellstart: Erstellen einer neuen Azure API Management-Dienstinstanz mithilfe der Azure CLI (Vorschau)

Azure API Management (APIM) unterstützt Organisationen beim Veröffentlichen von APIs für externe Entwickler, Partnerentwickler und interne Entwickler, um das volle Potenzial von Daten und Diensten ausschöpfen zu können. API Management bietet die Kernkompetenzen zur Sicherstellung eines erfolgreichen API-Programms in Form von Entwicklerengagement, geschäftlichen Erkenntnissen, Analysen, Sicherheit und Schutz. Mit APIM können Sie moderne API-Gateways für vorhandene Back-End-Dienste, die an einem beliebigen Ort gehostet werden, erstellen und verwalten. Weitere Informationen finden Sie in der [Übersicht](api-management-key-concepts.md).

In dieser Schnellstartanleitung werden die Schritte zum Erstellen einer neuen API Management-Instanz mithilfe der [az apim](/cli/azure/apim)-Befehle in der Azure CLI beschrieben. Die Befehle der `az apim`-Befehlsgruppe befinden sich aktuell in der Vorschau und können im Rahmen eines zukünftigen Releases geändert oder entfernt werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.11.1 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Azure API Management-Instanzen müssen wie alle Azure-Ressourcen in einer Ressourcengruppe bereitgestellt werden. Mit Ressourcengruppen können verwandte Azure-Ressourcen organisiert und verwaltet werden.

Erstellen Sie zunächst mit dem folgenden [az group create](/cli/azure/group#az-group-create)-Befehl eine Ressourcengruppe namens *myResourceGroup* am Standort „USA, Mitte“:

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Erstellen eines neuen Diensts

Da Sie nun über eine Ressourcengruppe verfügen, können Sie eine API Management-Dienstinstanz erstellen. Erstellen Sie eine solche Instanz mithilfe des [az apim create](/cli/azure/apim#az-apim-create)-Befehls, und geben Sie einen Dienstname sowie Details zum Herausgeber an. Der Dienstname muss in Azure eindeutig sein. 

Im folgenden Beispiel wird *myapim* als Dienstname verwendet. Ersetzen Sie diesen Namen durch einen eindeutigen Wert. Aktualisieren Sie außerdem den Namen der Organisation des Herausgebers der API sowie die E-Mail-Adresse, um Benachrichtigungen zu erhalten. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Standardmäßig erstellt der Befehl die Instanz im Developer-Tarif, wobei es sich um eine günstige Option zum Bewerten von Azure API Management handelt. Dieser Tarif ist nicht für die Produktion bestimmt. Weitere Informationen zum Skalieren der API Management-Tarife finden Sie unter [Upgrade and scale an API Management instance](upgrade-and-scale.md) (Upgraden und Skalieren einer API Management-Instanz). 

> [!TIP]
> Es dauert in der Regel zwischen 30 und 40 Minuten, einen API Management-Dienst mit diesem Tarif zu erstellen und zu aktivieren. Der vorherige Befehl nutzt die `--no-wait`-Option, sodass der Befehl sofort etwas zurückgibt, während der Dienst erstellt wird.

Überprüfen Sie den Bereitstellungsstatus, indem Sie den [az apim show](/cli/azure/apim#az-apim-show)-Befehl ausführen:

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Zunächst sieht die Ausgabe in etwa wie folgt aus und weist den `Activating`-Status auf:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Nach der Aktivierung lautet der Status `Online`, und die Dienstinstanz verfügt über eine Gatewayadresse und eine öffentliche IP-Adresse. Zu diesem Zeitpunkt machen diese Adressen keine Inhalte verfügbar. Zum Beispiel:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Sobald Ihre API Management-Dienstinstanz online ist, können Sie sie verwenden. Beginnen Sie mit dem Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und die API Management-Dienstinstanz nicht mehr benötigen, können Sie sie mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md)
