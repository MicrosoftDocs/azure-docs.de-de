---
title: 'Tutorial: Bereitstellen von Azure Spring Cloud im virtuellen Netzwerk'
description: Bereitstellen von Azure Spring Cloud im virtuellen Netzwerk (VNET-Einschleusung)
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6e2df9168b880e565ea9b70c82c2c0c1b55b4db8
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737242"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Tutorial: Bereitstellen von Azure Spring Cloud im virtuellen Azure-Netzwerk (VNET-Einschleusung)

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In diesem Tutorial wird erläutert, wie Sie eine Azure Spring Cloud-Dienstinstanz in Ihrem virtuellen Netzwerk bereitstellen. Dies wird manchmal als VNET-Einschleusung bezeichnet.  

Die Bereitstellung ermöglicht Folgendes:

* Isolierung von Azure Spring Cloud-Apps und der Dienstruntime vom Internet in Ihrem Unternehmensnetzwerk
* Azure Spring Cloud-Interaktion mit Systemen in lokalen Rechenzentren und/oder Azure-Diensten in anderen virtuellen Netzwerken
* Befähigung von Kunden zum Steuern der eingehenden und ausgehenden Netzwerkkommunikation für Azure Spring Cloud

## <a name="prerequisites"></a>Voraussetzungen
Sie müssen die Azure Spring Cloud-Ressourcenanbieter *Microsoft.AppPlatform* und *Microsoft.ContainerService* gemäß den Anweisungen zum [Registrieren des Ressourcenanbieters im Azure-Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) oder durch Ausführung des folgenden Azure CLI-Befehls registrieren:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```
## <a name="virtual-network-requirements"></a>Anforderungen für virtuelle Netzwerke
Das virtuelle Netzwerk, in dem Sie die Azure Spring Cloud-Dienstinstanz bereitstellen, muss die folgenden Anforderungen erfüllen:

* **Standort**: Das virtuelle Netzwerk muss sich am selben Ort wie die Azure Spring Cloud-Dienstinstanz befinden.
* **Abonnement**: Das virtuelle Netzwerk muss sich im selben Abonnement wie die Azure Spring Cloud-Dienstinstanz befinden.
* **Subnetze**: Das virtuelle Netzwerk muss zwei Subnetze enthalten, die für eine Azure Spring Cloud-Dienstinstanz bestimmt sind: 
    * Eins für die Dienstruntime
    * Eins für Ihre Spring Boot-Microserviceanwendungen 
    * Zwischen diesen Subnetzen und einer Azure Spring Cloud-Dienstinstanz besteht eine 1:1-Beziehung. Sie müssen für jede bereitgestellte Dienstinstanz ein neues Subnetz verwenden, und jedes Subnetz darf nur eine einzelne Dienstinstanz enthalten.
* **Adressraum:** Ein CIDR-Block (bis zu /28) für das Subnetz der Dienstruntime und ein weiterer CIDR-Block (bis zu /24) für das Subnetz der Spring Boot-Microserviceanwendungen
* **Routingtabelle**: Den Subnetzen darf keine vorhandene Routingtabelle zugeordnet sein.

In den folgenden Verfahren wird das Einrichten des virtuellen Netzwerks beschrieben, das die Instanz von Azure Spring Cloud enthalten soll.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
Wenn Sie bereits über ein virtuelles Netzwerk zum Hosten der Azure Spring Cloud-Dienstinstanz verfügen, überspringen Sie Schritt 1, 2 und 3. Sie können mit Schritt 4 beginnen, um Subnetze für das virtuelle Netzwerk vorzubereiten.

1. Wählen Sie im Menü des Azure-Portals die Option **Ressource erstellen** aus. Wählen Sie im Azure Marketplace die Option **Netzwerk** > **Virtuelles Netzwerk** aus.

1. Geben Sie im Dialogfeld **Virtuelles Netzwerk erstellen** die folgenden Informationen an, oder wählen Sie sie aus:

    |Einstellung          |Wert                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |Wählen Sie Ihr Abonnement aus.                         |
    |Resource group   |Wählen Sie Ihre Ressourcengruppe aus, oder erstellen Sie eine neue.  |
    |Name             |Geben Sie *azure-spring-cloud-vnet* ein.                   |
    |Position         |Wählen Sie **USA, Osten** aus.                                |

1. Klicken Sie auf **Weiter: IP-Adressen >** . 
 
1. Geben Sie unter „IPv4-Adressraum“ den Adressraum „10.1.0.0/16“ ein.

1. Wählen Sie **Subnetz hinzufügen** aus, und geben Sie unter **Subnetzname** den Namen *service-runtime-subnet* und unter **Subnetzadressbereich** den Bereich „10.1.0.0/24“ ein. Klicken Sie anschließend auf **Hinzufügen**.

1. Wählen Sie erneut **Subnetz hinzufügen** aus, und geben Sie **Subnetzname** und **Subnetzadressbereich** ein, etwa *apps-subnet* und 10.1.1.0/24.  Klicken Sie auf **Hinzufügen**.

1. Klicken Sie auf **Überprüfen + erstellen**. Übernehmen Sie die restlichen Standardeinstellungen, und klicken Sie auf **Erstellen**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Erteilen der Dienstberechtigung für das virtuelle Netzwerk

Wählen Sie das virtuelle Netzwerk *azure-spring-cloud-vnet* aus, das Sie zuvor erstellt haben.

1. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **Hinzufügen > Rollenzuweisung hinzufügen** aus.

    ![Zugriffssteuerung für das VNET](./media/spring-cloud-v-net-injection/access-control.png)

2. Geben Sie im Dialogfeld **Rollenzuweisung hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    |Einstellung  |Wert                                             |
    |---------|--------------------------------------------------|
    |Role     |Wählen Sie **Besitzer** aus.                                  |
    |Select   |Geben Sie *Azure Spring Cloud-Ressourcenanbieter* ein.      |

    Wählen Sie dann *Azure Spring Cloud-Ressourcenanbieter* aus, und klicken Sie auf **Speichern**.

    ![Zuweisen des Azure Spring Cloud-Ressourcenanbieters zum VNET](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Das gleiche Ergebnis erzielen Sie, wenn Sie den folgenden Azure CLI-Befehl ausführen:

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Bereitstellen der Azure Spring Cloud-Dienstinstanz im virtuellen Netzwerk

1. Öffnen Sie das Azure-Portal unter https://ms.portal.azure.com.

1. Suchen Sie über das obere Suchfeld nach **Azure Spring Cloud**, und wählen Sie in den Ergebnissen **Azure Spring Cloud** aus.

1. Wählen Sie auf der Seite **Azure Spring Cloud** die Option **+ Hinzufügen** aus.

1. Füllen Sie das Formular auf der Azure Spring Cloud-Seite **Erstellen** aus. 

1. Wählen Sie die gleiche Ressourcengruppe und die gleiche Region wie für das virtuelle Netzwerk aus.

1. Wählen Sie unter **Dienstdetails** für **Name** die Option *azure-spring-cloud-vnet* aus.

1. Wählen Sie die Registerkarte **Netzwerk** und dann Folgendes aus:

    |Einstellung                                |Wert                                             |
    |---------------------------------------|--------------------------------------------------|
    |Bereitstellen im eigenen virtuellen Netzwerk     |Klicken Sie auf **Ja**.                                    |
    |Virtuelles Netzwerk                        |Wählen Sie *azure-spring-cloud-vnet* aus.                  |
    |Subnetz der Dienstruntime                 |Wählen Sie *service-runtime-subnet* aus.                   |
    |Subnetz für Spring Boot-Microservice-Apps   |Wählen Sie *apps-subnet* aus.                              |

    ![Registerkarte „Netzwerk“ für die Erstellung](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Klicken Sie auf **Überprüfen und erstellen**.

1. Überprüfen Sie Ihre Spezifikationen, und klicken Sie auf **Erstellen**.

Nach der Bereitstellung werden zwei zusätzliche Ressourcengruppen in Ihrem Abonnement erstellt, um die Netzwerkressourcen für die Azure Spring Cloud-Dienstinstanz zu hosten.  Navigieren Sie zur **Startseite**, und wählen Sie dann **Ressourcengruppen** aus den oberen Menüelementen aus, um die folgenden neuen Ressourcengruppen zu suchen.

Die Ressourcengruppe mit dem Namen *ap-svc-rt_{Dienstinstanzname}_{Dienstinstanzregion}* enthält Netzwerkressourcen für die Dienstruntime der Dienstinstanz.

  ![Dienstruntime](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Die Ressourcengruppe mit dem Namen *ap-app_{Dienstinstanzname}_{Dienstinstanzregion}* enthält Netzwerkressourcen für die Spring Boot-Microserviceanwendungen der Dienstinstanz.

  ![Apps-Ressourcengruppe](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Diese Netzwerkressourcen sind mit dem oben erstellten virtuellen Netzwerk verbunden.

  ![VNET mit verbundenem Gerät](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Die Ressourcengruppen werden vollständig vom Azure Spring Cloud-Dienst verwaltet. Die Ressourcen darin dürfen NICHT manuell gelöscht oder geändert werden.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen der Anwendung in Azure Spring Cloud in Ihrem VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Weitere Informationen

- [Problembehandlung für Azure Spring Cloud im VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)