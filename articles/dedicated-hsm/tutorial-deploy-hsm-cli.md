---
title: 'Tutorial: Bereitstellen in einem vorhandenen virtuellen Netzwerk mithilfe von Azure CLI – Azure Dedicated HSM | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie ein dediziertes HSM mithilfe der Befehlszeilenschnittstelle in einem vorhandenen virtuellen Netzwerk bereitstellen.
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: mbaldwin
ms.openlocfilehash: b6f4610887092b1dac5cdc85622739318d5921d7
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852233"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>Tutorial: Bereitstellen von HSMs in einem vorhandenen virtuellen Netzwerk mithilfe der Azure CLI

Der Azure-Dienst für dedizierte HSMs stellt ein physisches Gerät für die Verwendung durch einen Kunden mit umfassender administrativer Kontrolle und vollständiger Verwaltungsverantwortung bereit. Aufgrund der Bereitstellung physischer Geräte muss Microsoft die Zuordnung dieser Geräte steuern, um eine effektive Kapazitätsverwaltung zu gewährleisten. Deshalb steht der Dienst für dedizierte HSMs innerhalb eines Azure-Abonnements standardmäßig nicht für die Ressourcenbereitstellung zur Verfügung. Azure-Kunden, die Zugriff auf den Dienst für dedizierte HSMs benötigen, müssen bei ihrem Microsoft-Kundenbetreuer die Registrierung für den Dienst für dedizierte HSMs beantragen. Die Bereitstellung ist erst nach Abschluss dieses Prozesses möglich. 

Dieses Tutorial zeigt einen typischen Bereitstellungsprozess mit folgenden Kriterien:

- Ein Kunde verfügt bereits über ein virtuelles Netzwerk.
- Der Kunde verfügt über einen virtuellen Computer.
- Der Kunde muss seiner vorhandenen Umgebung HSM-Ressourcen hinzufügen.

Eine typische Bereitstellungsarchitektur mit Hochverfügbarkeit und mehreren Regionen kann beispielsweise wie folgt aussehen:

![Bereitstellung mit mehreren Regionen](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Dieses Tutorial konzentriert sich auf die Integration eines HSM-Paars und des erforderlichen ExpressRoute-Gateways („Subnet 1“ in der obigen Abbildung) in ein vorhandenes virtuelles Netzwerk („VNET 1“ in der obigen Abbildung).  Alle anderen Ressourcen sind Azure-Standardressourcen. Der gleiche Integrationsprozess kann auch für HSMs in „Subnet 4“ für „VNET 3“ (ebenfalls in der obigen Abbildung) verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Der Azure-Dienst für dedizierte HSMs ist derzeit nicht im Azure-Portal verfügbar. Sämtliche Interaktionen mit dem Dienst werden über die Befehlszeile oder mithilfe von PowerShell abgewickelt. In diesem Tutorial wird die Befehlszeilenschnittstelle (Command Line Interface, CLI) in Azure Cloud Shell verwendet. Sollten Sie noch nicht mit der Azure-Befehlszeilenschnittstelle vertraut sein, informieren Sie sich hier über die ersten Schritte: [Erste Schritte mit der Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true).

Voraussetzungen:

- Sie haben den Registrierungsprozess für den Azure-Dienst für dedizierte HSMs durchlaufen.
- Sie sind zur Verwendung des Diensts berechtigt. Wenden Sie sich andernfalls an Ihren Microsoft-Kontobeauftragten.
- Sie haben eine Ressourcengruppe für die Ressourcen erstellt, und die neuen, die im Rahmen dieses Tutorials erstellt werden, werden dieser Gruppe hinzugefügt.
- Sie haben bereits das erforderliche virtuelle Netzwerk und das Subnetz sowie virtuelle Computer gemäß dem obigen Diagramm erstellt und möchten nun zwei HSMs in diese Bereitstellung integrieren.

Bei allen weiteren Schritten wird davon ausgegangen, dass Sie bereits zum Azure-Portal navigiert sind und Cloud Shell geöffnet haben (durch Auswählen von „\>\_“ rechts oben im Portal).

## <a name="provisioning-a-dedicated-hsm"></a>Bereitstellen eines dedizierten HSM

Die Bereitstellung von HSMs und deren Integration in ein vorhandenes virtuelles Netzwerk über ExpressRoute-Gateway wird mithilfe von ssh überprüft. Durch diese Überprüfung wird sichergestellt, dass das HSM-Gerät für weitere Konfigurationsaktivitäten erreichbar und grundsätzlich verfügbar ist.

### <a name="validating-feature-registration"></a>Überprüfen der Featureregistrierung

Der Dienst für dedizierte HSMs muss wie bereits erwähnt für Ihr Abonnement registriert sein, um Bereitstellungsaktivitäten ausführen zu können. Vergewissern Sie sich, dass diese Anforderung erfüllt ist, indem Sie im Azure-Portal die folgenden Befehle in Cloud Shell ausführen:

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Bei den Befehlen muss der Status „Registered“ zurückgegeben werden (wie in der folgenden Abbildung gezeigt). Wird für die Befehle nicht „Registered“ zurückgegeben, müssen Sie sich noch für den Dienst registrieren. Wenden Sie sich hierzu an Ihren Microsoft-Kontobeauftragten.

![Abonnementstatus](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Erstellen von HSM-Ressourcen

Bevor Sie HSM-Ressourcen erstellen, müssen Sie einige Voraussetzungen erfüllen. Sie benötigen ein virtuelles Netzwerk mit Subnetzbereichen für Compute, HSMs und Gateway. Die folgenden Beispielbefehle zeigen, wie Sie ein solches virtuelles Netzwerk erstellen:

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>Bei der Konfiguration für das virtuelle Netzwerk ist insbesondere darauf zu achten, dass Delegierungen für das Subnetz des HSM-Geräts auf „Microsoft.HardwareSecurityModules/dedicatedHSMs“ festgelegt sind.  Ohne diese Option funktioniert die HSM-Bereitstellung nicht.

Nachdem Sie Ihr Netzwerk konfiguriert haben, verwenden Sie diese Azure CLI-Befehle zur Bereitstellung Ihrer HSMs.

1. Verwenden Sie den Befehl [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) zur Bereitstellung des ersten HSMs. Das HSM hat die Bezeichnung „hsm1“. Ersetzen Sie Ihr Abonnement:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   Der Bereitstellungsvorgang dauert ca. 25 bis 30 Minuten, wobei die meiste Zeit für die HSM-Geräte aufgewendet wird.

1. Um ein aktuelles HSM anzuzeigen, führen Sie den Befehl [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) aus:

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. Stellen Sie das zweite HSM mit diesem Befehl bereit:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. Führen Sie den Befehl [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) aus, um Details zu Ihren aktuellen HSMs anzuzeigen:

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

Es gibt einige andere Befehle, die nützlich sein können. Verwenden Sie den Befehl [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update), um ein HSM zu aktualisieren:

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

Zum Löschen eines HSMs verwenden Sie den Befehl [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete):

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>Überprüfen der Bereitstellung

Führen Sie die folgenden Befehle aus, um sich zu vergewissern, dass die Geräte bereitgestellt wurden, und um die Geräteattribute anzuzeigen. Achten Sie darauf, dass die Ressourcengruppe korrekt festgelegt ist und der Ressourcenname exakt der Angabe in der Parameterdatei entspricht.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

Die Ausgabe sieht in etwa so aus wie die folgende Ausgabe:

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

Die Ressource wird nun auch im [Azure-Ressourcen-Explorer](https://resources.azure.com/) angezeigt.   Erweitern Sie im Explorer auf der linken Seite nacheinander die Option „Abonnements“, das Abonnement für dedizierte HSMs, die Option „Ressourcengruppen“ und die verwendete Ressourcengruppe, und wählen Sie anschließend das Element „Ressourcen“ aus.

## <a name="testing-the-deployment"></a>Testen der Bereitstellung

Zum Testen der Bereitstellung wird erst eine Verbindung mit einem virtuellen Computer, der auf die HSMs zugreifen kann, und anschließend eine direkte Verbindung mit dem HSM-Gerät hergestellt. Dadurch wird überprüft, ob das HSM erreichbar ist.
Die Verbindung mit dem virtuellen Computer wird mithilfe des ssh-Tools hergestellt. Der Befehl sieht in etwa wie folgt aus (allerdings mit dem Administratornamen und dem DNS-Namen aus dem Parameter):

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Anstelle des DNS-Namens kann im obigen Befehl auch die IP-Adresse des virtuellen Computers verwendet werden. Nach erfolgreicher Ausführung des Befehls werden Sie zur Eingabe eines Kennworts aufgefordert. Nachdem Sie sich bei dem virtuellen Computer angemeldet haben, können Sie sich unter Verwendung der privaten IP-Adresse, die im Portal für die dem HSM zugeordnete Netzwerkschnittstellenressource angegeben ist, beim HSM anmelden.

![Komponentenliste](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Das Kontrollkästchen „Ausgeblendete Typen anzeigen“ muss aktiviert sein, damit HSM-Ressourcen angezeigt werden.

Wenn Sie auf „HSM1_HSMnic“ oder „HSM2_HSMnic“ klicken, wird die entsprechende private IP-Adresse angezeigt. Dies ist im obigen Screenshot dargestellt. Alternativ kann die korrekte IP-Adresse auch mithilfe des weiter oben verwendeten Befehls `az resource show` ermittelt werden. 

Wenn Sie über die korrekte IP-Adresse verfügen, führen Sie den folgenden Befehl mit der ermittelten Adresse aus:

`ssh tenantadmin@10.0.2.4`

Ist der Vorgang erfolgreich, werden Sie zur Eingabe eines Kennworts aufgefordert. Das Standardkennwort lautet „PASSWORD“, und das HSM fordert Sie zur Änderung des Kennworts auf. Legen Sie ein sicheres Kennwort fest, und verwenden Sie die von Ihrer Organisation bevorzugte Vorgehensweise, um es zu speichern und sicher aufzubewahren.

>[!IMPORTANT]
>Sollte das Kennwort verloren gehen, muss das HSM zurückgesetzt werden, wodurch auch Ihre Schlüssel verloren gehen.

Führen Sie nach dem Herstellen der SSH-Verbindung mit dem HSM den folgenden Befehl aus, um sich zu vergewissern, dass das HSM betriebsbereit ist.

`hsm show`

Die Ausgabe sollte in etwa wie in der folgenden Abbildung aussehen:

![Der Screenshot zeigt die Ausgabe im PowerShell-Fenster.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Sie haben nun alle Ressourcen für eine hoch verfügbare Bereitstellung mit zwei HSMs zugeordnet und den Zugriff sowie die Betriebsbereitschaft geprüft. Die weiteren Konfigurations-/Testschritte erfordern eine intensivere Interaktion mit dem eigentlichen HSM-Gerät. Gehen Sie hierzu gemäß der Anleitung in Kapitel 7 des Administratorhandbuchs für Thales Luna Network HSM 7 vor, um das HSM zu initialisieren und Partitionen zu erstellen. Die gesamte Dokumentation und Software kann direkt von Thales heruntergeladen werden, nachdem Sie sich beim Kundensupportportal von Thales registriert und eine Kunden-ID erhalten haben. Laden Sie die Version 7.2 der Clientsoftware herunter, um alle erforderlichen Komponenten zu erhalten.

## <a name="delete-or-clean-up-resources"></a>Löschen oder Bereinigen von Ressourcen

Wenn Sie das HSM-Gerät nicht mehr benötigen, kann es als Ressource gelöscht und wieder dem freien Pool zugeführt werden. Ein wichtiger Aspekt bei diesem Schritt sind natürlich vertrauliche Kundendaten, die sich ggf. auf dem Gerät befinden. Ein Gerät lässt sich am besten durch dreimalige Falscheingabe des HSM-Administratorkennworts nullen. (Hinweis: Hierbei handelt es sich um das Kennwort des HSM-Administrators, nicht um das des Applianceadministrators.) Als Sicherheitsmaßnahme zum Schutz der Schlüsseldaten kann das Gerät erst als Azure-Ressource gelöscht werden, wenn es sich im genullten Zustand befindet.

> [!NOTE]
> Wenden Sie sich bei Problemen mit der Thales-Gerätekonfiguration an den [Thales-Kundensupport](https://safenet.gemalto.com/technical-support/).

Wenn Sie die Ressourcen in dieser Ressourcengruppe nicht mehr benötigen, können Sie alle mithilfe des folgenden Befehls entfernen:

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>Nächste Schritte

Nach Abschluss dieses Tutorials verfügen Sie über bereitgestellte dedizierte HSM-Ressourcen sowie über ein virtuelles Netzwerk mit den erforderlichen HSMs und weiteren Netzwerkkomponenten für die Kommunikation mit dem HSM.  Diese Bereitstellung kann nun nach Bedarf mit weiteren Ressourcen für Ihre bevorzugte Bereitstellungsarchitektur ergänzt werden. Weitere hilfreiche Informationen für die Bereitstellungsplanung finden Sie in den Konzeptdokumenten.
Es empfiehlt sich, zwei HSMs in einer primären Region (für die Verfügbarkeit auf Rackebene) und zwei HSMs in einer sekundären Region (für die regionale Verfügbarkeit) zu verwenden. 

* [Hochverfügbarkeit](high-availability.md)
* [Physische Sicherheit](physical-security.md)
* [Netzwerk](networking.md)
* [Unterstützungsmöglichkeiten](supportability.md)
* [Überwachung](monitoring.md)
