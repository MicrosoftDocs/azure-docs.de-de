---
title: Auswählen von Linux-VM-Images mit der Azure-CLI
description: Erfahren Sie mehr über die Verwendung der Azure CLI, um den Herausgeber, das Angebot, die SKU und die Version für Marketplace-VM-Images zu ermitteln.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 56d2aa9f7aa36808774876ac0f5cfc596887ff26
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906385"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Suchen nach Linux-VM-Images im Azure Marketplace mit der Azure CLI

Dieses Thema beschreibt, wie Sie mit der Azure-Befehlszeilenschnittstelle im Azure Marketplace nach VM-Images suchen. Verwenden Sie diese Informationen, um ein Marketplace-Image anzugeben, wenn Sie einen virtuellen Computer programmgesteuert mit der Befehlszeilenschnittstelle, mit Resource Manager-Vorlagen oder anderen Tools erstellen.

Suchen Sie über die [Azure Marketplace](https://azuremarketplace.microsoft.com/) Storefront, das [Azure-Portal](https://portal.azure.com) oder [Azure PowerShell](../windows/cli-ps-findimage.md) nach den verfügbaren Images und Angeboten. 

Stellen Sie sicher, dass Sie die aktuelle Version der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) installiert haben und bei einem Azure-Konto (`az login`) angemeldet sind.

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="deploy-from-a-vhd-using-purchase-plan-parameters"></a>Bereitstellen von einer VHD mithilfe von Erwerbsplanparametern

Wenn eine Ihrer virtuellen Festplatten mit einem kostenpflichtigen Azure Marketplace-Image erstellt wurde, müssen Sie beim Erstellen einer neuen VM von dieser VHD möglicherweise die Informationen zu Ihrem Erwerbsplan angeben. 

Wenn Sie noch über die ursprüngliche VM oder eine andere VM verfügen, die mit demselben Marketplace-Image erstellt wurde, können Sie den Plannamen, den Herausgeber und die Produktinformationen mithilfe von [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) abrufen. Dieses Beispiel ruft eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* ab und zeigt dann die Erwerbsplaninformationen an.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Wenn Sie die Planinformationen nicht vor dem Löschen der ursprünglichen VM abgerufen haben, können Sie eine [Supportanfrage](https://ms.portal.azure.com/#create/Microsoft.Support) erstellen. Sie benötigen dazu den VM-Namen, die Abonnement-ID und den Zeitstempel des Löschvorgangs.

Wenn Sie die Planinformationen haben, können Sie die neue VM erstellen, indem Sie die VHD mit dem Parameter `--attach-os-disk` angeben.

```azurecli-interactive
az vm create \
   --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```

## <a name="deploy-a-new-vm-using-purchase-plan-parameters"></a>Bereitstellen einer neuen VM mithilfe von Erwerbsplanparametern

Wenn Sie bereits über alle Informationen zum Image verfügen, können Sie es mit dem Befehl `az vm create` bereitstellen. In diesem Beispiel geben Sie zum Bereitstellen einer VM mit dem von Bitnami zertifizierten RabbitMQ-Image Folgendes an:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

Wenn Sie in einer Meldung aufgefordert werden, die Nutzungsbedingungen für das Image zu akzeptieren, finden Sie weiter unten in diesem Artikel im Abschnitt zum [Akzeptieren der Nutzungsbedingungen](#accept-the-terms) weitere Informationen.

## <a name="list-popular-images"></a>Liste von beliebten Images

Führen Sie den Befehl [az vm image list](/cli/azure/vm/image) ohne die Option `--all` aus, um eine Liste beliebter VM-Images im Azure Marketplace anzuzeigen. Führen Sie beispielsweise den folgenden Befehl aus, um eine zwischengespeicherte Liste beliebter Images im Tabellenformat anzuzeigen:

```azurecli
az vm image list --output table
```

Die Ausgabe enthält den Image-URN (Wert in der Spalte *Urn*). Beim Erstellen eines virtuellen Computers mit einem der beliebten Marketplace-Images können Sie alternativ das *UrnAlias*-Element angeben, eine Kurzform wie etwa *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Suchen nach bestimmten Images

Wenn Sie ein bestimmtes VM-Image im Marketplace suchen möchten, verwenden Sie den Befehl `az vm image list` mit der Option `--all`. Die Ausführung dieser Befehlsversion dauert einige Zeit und kann eine umfangreiche Ausgabe zurückgeben. Daher filtern Sie in der Regel die Liste nach `--publisher` oder einem anderen Parameter. 

Mit dem folgenden Code werden beispielsweise alle Debian-Angebote angezeigt (zur Erinnerung: Ohne den Switch `--all` wird nur der lokale Cache von allgemeinen Images durchsucht):

```azurecli
az vm image list --offer Debian --all --output table 

```

Hier sehen Sie einen Teil der Ausgabe: 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Wenden Sie ähnliche Filter mit den Optionen `--location`, `--publisher` und `--sku` an. Sie können Teilübereinstimmungen für einen Filter durchführen, etwa bei der Suche nach `--offer Deb`, um alle Debian-Images zu finden.

Wenn Sie keinen bestimmten Standort mit der Option `--location` angeben, werden die Werte für den Standardstandort zurückgegeben. (Legen Sie einen anderen Standardstandort fest, indem Sie `az configure --defaults location=<location>` ausführen.)

Beispielsweise listet der folgende Befehl alle Debian 8-SKUs am Standort „Europa, Westen“ auf:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Hier sehen Sie einen Teil der Ausgabe:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Navigieren zu den Images
 
Eine weitere Möglichkeit für die Suche nach einem Image an einem Standort besteht darin, die Befehle [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) und [az vm image list-skus](/cli/azure/vm/image) nacheinander auszuführen. Mit diesen Befehlen ermitteln Sie folgende Werte:

1. Auflistung der Herausgeber von Images
2. Auflistung der Angebote eines bestimmten Anbieters
3. Auflistung der SKUs eines bestimmten Angebots

Anschließend können Sie für eine ausgewählte SKU eine bereitzustellende Version auswählen.

Beispielsweise listet der folgende Befehl die Herausgeber von Images für den Standort „USA, Westen“ auf:

```azurecli
az vm image list-publishers --location westus --output table
```

Hier sehen Sie einen Teil der Ausgabe:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Verwenden Sie diese Informationen, um nach Angeboten eines bestimmten Herausgebers zu suchen. Für den Herausgeber *Canonical* am Standort „USA, Westen“ suchen Sie beispielsweise durch Ausführen von `azure vm image list-offers` nach dessen Angeboten. Übergeben Sie den Standort und den Herausgeber, wie im folgenden Beispiel gezeigt wird:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Ausgabe:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Sie sehen, dass in der Region „USA, Westen“ Canonical das *UbuntuServer*-Angebot in Azure herausgibt. Aber welche SKUs? Um diese Werte zu erhalten, führen Sie `azure vm image list-skus` aus, und legen Sie den Standort, Herausgeber und das von Ihnen entdeckte Angebot fest:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Ausgabe:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Verwenden Sie abschließend den Befehl `az vm image list`, um nach einer bestimmten gewünschten Version der SKU zu suchen, z.B. *18.04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Hier sehen Sie einen Teil der Ausgabe:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Jetzt können Sie genau das Image auswählen, das Sie verwenden möchten. Notieren Sie sich hierzu den Wert des URN. Übergeben Sie diesen Wert mit dem Parameter `--image`, wenn Sie einen virtuellen Computer mit dem Befehl [az vm create](/cli/azure/vm) erstellen. Beachten Sie, dass Sie optional die Versionsnummer im URN durch „latest“ ersetzen können. Diese Version ist immer die neueste Version des Images. 

Wenn Sie einen virtuellen Computer mithilfe einer Resource Manager-Vorlage bereitstellen, legen Sie die Imageparameter einzeln in den `imageReference`-Eigenschaften fest. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Anzeigen von Planeigenschaften

Um die Kaufplaninformationen eines Images anzuzeigen, führen Sie den Befehl [az vm image show](/cli/azure/image) aus. Wenn die `plan`-Eigenschaft in der Ausgabe nicht `null` ist, müssen Sie vor der programmgesteuerten Bereitstellung Bedingungen des Images akzeptieren.

Das Image „Canonical Ubuntu Server 18.04 LTS“ verfügt beispielsweise nicht über zusätzliche Bedingungen, da `null` für `plan` angegeben ist:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Ausgabe:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Wenn Sie einen ähnlichen Befehl für das von Bitnami zertifizierte RabbitMQ-Image ausführen, werden die folgenden `plan`-Eigenschaften angezeigt: `name`, `product` und `publisher`. (Einige Images besitzen auch eine `promotion code`-Eigenschaft.) Sehen Sie sich zum Bereitstellen dieses Images die folgenden Abschnitte an, um die Bedingungen zu akzeptieren und die programmgesteuerte Bereitstellung zu ermöglichen.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Ausgabe:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

## <a name="accept-the-terms"></a>Akzeptieren der Bedingungen

Verwenden Sie zum Anzeigen und Akzeptieren der Lizenzbedingungen den Befehl [az vm image accept-terms](/cli/azure/vm/image?). Wenn Sie die Bedingungen akzeptieren, ermöglichen Sie die programmgesteuerte Bereitstellung in Ihrem Abonnement. Sie müssen für das Image die Bedingungen nur einmal pro Abonnement akzeptieren. Beispiel:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Die Ausgabe enthält ein `licenseTextLink`-Element für die Lizenzbedingungen und gibt an, dass für `accepted` der Wert `true` angegeben ist:

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

## <a name="next-steps"></a>Nächste Schritte
Um schnell mithilfe der Imageinformationen einen virtuellen Computer zu erstellen, lesen Sie die Informationen unter [Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle](tutorial-manage-vm.md).
