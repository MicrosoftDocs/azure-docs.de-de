---
title: Bereitstellen und Konfigurieren von Azure VMware Solution
description: Erfahren Sie, wie Sie die in der Planungsphase gesammelten Informationen verwenden, um die private Azure VMware Solution-Cloud bereitzustellen.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 7e31b9236a3c75009d15bde35019036b6db55cab
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861516"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Bereitstellen und Konfigurieren von Azure VMware Solution

In diesem Artikel verwenden Sie die Informationen aus dem [Planungsprozess](production-ready-deployment-steps.md), um Azure VMware Solution bereitzustellen. Wenn Sie die Informationen noch nicht definiert haben, wechseln Sie zurück zum [Planungsprozess](production-ready-deployment-steps.md), bevor Sie den Vorgang fortsetzen.

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Bereitstellen von Azure VMware Solution

Verwenden Sie die Informationen, die Sie im Artikel [Planen der Azure VMware Solution-Bereitstellung](production-ready-deployment-steps.md) gesammelt haben:

>[!NOTE]
>Damit Sie Azure VMware Solution bereitstellen können, müssen Sie mindestens die Rolle „Mitwirkender“ im Abonnement haben.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Eine umfassende Übersicht über diesen Schritt vermittelt das Video [Azure VMware Solution: Bereitstellen](https://www.youtube.com/embed/gng7JjxgayI).

## <a name="create-the-jump-box"></a>Erstellen der Jumpbox (Jump Server)

>[!IMPORTANT]
>Wenn Sie die Option **Virtuelles Netzwerk** während des ersten Bereitstellungsschritts auf der Seite **Private VMWare-Cloud erstellen** nicht ausgefüllt haben, arbeiten Sie das Tutorial [Konfigurieren des Netzwerks für Ihre private VMware-Cloud in Azure](tutorial-configure-networking.md) durch, **bevor** Sie mit diesem Abschnitt fortfahren.  

Nachdem Sie Azure VMware Solution bereitgestellt haben, erstellen Sie die Jumpbox des virtuellen Netzwerks, über die Verbindungen mit vCenter und NSX hergestellt werden. Nachdem Sie ExpressRoute-Leitungen und ExpressRoute Global Reach konfiguriert haben, ist die Jumpbox nicht mehr erforderlich.  Sie ist jedoch praktisch, vCenter und NSX in ihrer Azure VMware Solution-Umgebung zu erreichen.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Die Azure VMware Solution-Jumpbox erstellen" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Gehen Sie wie folgt vor, um einen virtuellen Computer (Virtual Machine, VM) in dem virtuellen Netzwerk zu erstellen, das Sie [im Rahmen des Bereitstellungsprozesses bestimmt oder erstellt haben](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution): 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Herstellen einer Verbindung mit einem virtuellen Netzwerk über ExpressRoute

Wenn Sie im Bereitstellungsschritt kein virtuelles Netzwerk definiert und die Absicht haben, die ExpressRoute-Leitung von Azure VMware Solution mit einem vorhandenen ExpressRoute-Gateway zu verbinden, führen Sie die folgenden Schritte aus.

Wenn Sie bereits ein virtuelles Netzwerk auf der Bereitstellungsseite in Azure definiert haben, springen Sie zum nächsten Abschnitt.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Überprüfen der aufgeführten Netzwerkrouten

Die Jumpbox befindet sich in dem virtuellen Netzwerk, in dem Azure VMware Solution über seine ExpressRoute-Leitung eine Verbindung herstellt.  Wechseln Sie in Azure zur Netzwerkschnittstelle der Jumpbox, und [zeigen Sie die effektiven Routen an](../virtual-network/manage-route-table.md#view-effective-routes).

In der Liste der effektive Routen sollten die Netzwerke angezeigt werden, die während der Azure VMware Solution-Bereitstellung erstellt wurden. Es werden mehrere Netzwerke angezeigt, die aus dem [`/22`-Netzwerk abgeleitet wurden, das Sie](production-ready-deployment-steps.md#ip-address-segment) während des [Bereitstellungsschritts](#deploy-azure-vmware-solution) weiter oben in diesem Artikel definiert haben.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Netzwerkrouten überprüfen, die von Azure VMware Solution zu Azure Virtual Network aufgeführt sind" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

In diesem Beispiel wird für das 10.74.72.0/22-Netzwerk, das bei der Bereitstellung eingegeben wurde, das /24-Netzwerk abgeleitet.  Wenn Sie eine ähnliche Anzeige sehen, können Sie eine Verbindung mit vCenter in Azure VMware Solution herstellen.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Verbinden mit und Anmelden bei vCenter und NSX-T

Melden Sie sich bei der Jumpbox an, die Sie im früheren Schritt erstellt haben. Nachdem Sie sich angemeldet haben, öffnen Sie einen Webbrowser, navigieren Sie sowohl zu vCenter als auch zur NSX-T-Administratorkonsole, und melden Sie sich bei beiden an.  

Sie können die IP-Adresse und Anmeldeinformationen von vCenter und der NSX-T-Administratorkonsole im Azure-Portal ermitteln.  Wählen Sie Ihre private Cloud aus, und navigieren Sie in der Ansicht **Übersicht** zu **Identität > Standard**. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Erstellen eines Netzwerksegments in Ihrer Azure VMware Solution-Umgebung

Sie verwenden NSX-T, um neue Netzwerksegmente in ihrer Azure VMware Solution-Umgebung zu erstellen.  Sie haben die Netzwerke, die Sie erstellen möchten, im [Planungsprozess](production-ready-deployment-steps.md) definiert.  Wenn Sie die Netzwerke noch nicht definiert haben, wechseln Sie zurück zum [Planungsprozess](production-ready-deployment-steps.md), bevor Sie den Vorgang fortsetzen.

>[!IMPORTANT]
>Vergewissern Sie sich, dass sich der von Ihnen definierte CIDR-Netzwerkadressblock nicht mit irgendwelchen Adressen in ihren Azure- oder lokalen Umgebungen überlappt.  

Führen Sie die Schritte im Tutorial [Erstellen eines NSX-T-Netzwerksegments in Azure VMware Solution](tutorial-nsx-t-network-segment.md) aus, um ein NSX-T-Netzwerksegment in Azure VMware Solution zu erstellen.

## <a name="verify-advertised-nsx-t-segment"></a>Überprüfen des aufgeführten NSX-T-Segments

Wechseln Sie zurück zum Schritt [Überprüfen der aufgeführten Netzwerkrouten](#verify-network-routes-advertised). Es werden weitere Routen in der Liste angezeigt, die den Netzwerksegmenten entsprechen, die Sie im vorherigen Schritt erstellt haben.  

Bei virtuellen Computern weisen Sie die Segmente zu, die Sie im Schritt [Erstellen eines Netzwerksegments in Azure VMware Solution](#create-a-network-segment-on-azure-vmware-solution) erstellt haben.  

Da DNS erforderlich ist, müssen Sie den DNS-Server bestimmen, den Sie verwenden möchten.  

- Wenn Sie ExpressRoute Global Reach konfiguriert haben, können Sie jeden DNS-Server verwenden, den Sie lokal verwenden würden.  
- Haben Sie einen DNS-Server in Azure, verwenden Sie diesen.  
- Haben Sie weder einen lokalen DNS-Server noch einen DNS-Server in Azure, verwenden Sie den DNS-Server, der von der Jumpbox verwendet wird.

>[!NOTE]
>In diesem Schritt wird der DNS-Server bestimmt. Es werden keine Konfigurationen vorgenommen.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>(Optional) Bereitstellen von DHCP-Diensten für das NSX-T-Netzwerksegment

Wenn Sie DHCP in Ihren NSX-T-Segmenten verwenden möchten, gehen Sie gemäß diesem Abschnitt vor. Andernfalls springen Sie zum Abschnitt [Hinzufügen eines virtuellen Computers im NSX-T-Netzwerksegment](#add-a-vm-on-the-nsx-t-network-segment).  

Nachdem Sie das NSX-T-Netzwerksegment erstellt haben, gibt es zwei Möglichkeiten zum Erstellen und Verwalten von DHCP in Azure VMware Solution:

* Bei Verwendung von NSX-T zum Hosten Ihres DHCP-Servers müssen Sie [einen DHCP-Server erstellen](manage-dhcp.md#create-a-dhcp-server) und [den Relaydienst zu diesem Server erstellen](manage-dhcp.md#create-dhcp-relay-service). 
* Wenn Sie einen externen DHCP-Server eines Drittanbieters in Ihrem Netzwerk verwenden, müssen Sie [einen DHCP-Relaydienst erstellen](manage-dhcp.md#create-dhcp-relay-service).  Für diese Option [nehmen Sie nur die Relaykonfiguration vor](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Hinzufügen eines virtuellen Computers im NSX-T-Netzwerksegment

Stellen Sie in Ihrer Azure VMware Solution vCenter-Lösung einen virtuellen Computer bereit, und verwenden Sie diesen zum Überprüfen der Konnektivität von Ihren Azure VMware Solution-Netzwerken mit:

- Dem Internet
- Virtuelle Azure-Netzwerke
- Lokal  

Stellen Sie die VM wie in jeder beliebigen vSphere-Umgebung bereit.  Ordnen Sie die VM einem der Netzwerksegmente zu, die Sie zuvor in NSX-T erstellt haben.  

>[!NOTE]
>Wenn Sie einen DHCP-Server eingerichtet haben, erhalten Sie die Netzwerkkonfiguration für die VM von diesem (vergessen Sie nicht, den Bereich einzurichten).  Wenn Sie statisch konfigurieren möchten, konfigurieren Sie wie gewohnt.

## <a name="test-the-nsx-t-segment-connectivity"></a>Testen der NSX-T-Segmentkonnektivität

Melden Sie sich bei der im vorherigen Schritt erstellten VM an, und überprüfen Sie die Konnektivität.

1. Pingen Sie eine IP-Adresse im Internet.
2. Navigieren Sie über einen Webbrowser zu einer Internet-Website.
3. Pingen Sie die Jumpbox, die sich im virtuellen Azure-Netzwerk befindet.

>[!IMPORTANT]
>An diesem Punkt ist Azure VMware Solution einsatzbereit, und Sie haben erfolgreich eine Verbindung zwischen dem virtuellen Azure-Netzwerk und dem Internet hergestellt.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Abschnitt verbinden Sie Azure VMware Solution über ExpressRoute mit Ihrem lokalen Netzwerk.
> [!div class="nextstepaction"]
> [Verbinden von Azure VMware Solution mit Ihrer lokalen Umgebung](azure-vmware-solution-on-premises.md)
