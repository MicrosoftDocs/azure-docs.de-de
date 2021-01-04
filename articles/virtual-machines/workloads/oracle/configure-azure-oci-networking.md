---
title: Integration von Azure ExpressRoute und Oracle Cloud Infrastructure | Microsoft-Dokumentation
description: Integration von Azure ExpressRoute und Oracle Cloud Infrastructure (OCI) FastConnect unterstützt cloudübergreifende Oracle-Anwendungslösungen
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.reviewer: cynthn
ms.openlocfilehash: 2717ba307cac82465e0c5df996ef3b668af5e7d4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963707"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Einrichten einer direkten Verbindung zwischen Azure und Oracle Cloud Infrastructure  

Microsoft und Oracle bieten mit [ExpressRoute](../../../expressroute/expressroute-introduction.md) und [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) die Möglichkeit, eine direkte Verbindung zwischen Azure und Oracle Cloud Infrastructure (OCI) herzustellen und so eine [integrierte Multi-Cloud-Umgebung](oracle-oci-overview.md) bereitzustellen. Dank der Integration von ExpressRoute und FastConnect profitieren Kunden von geringer Latenz, hohem Durchsatz und privaten Direktverbindungen zwischen den beiden Clouds.

> [!IMPORTANT]
> Oracle zertifiziert diese Anwendungen für die Ausführung in Azure, wenn die Azure/Oracle Cloud Interconnect-Lösung bis Mai 2020 verwendet wird.
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Oracle Retail-Anwendungen
> * Oracle Hyperion Financial Management

Die folgende Abbildung enthält eine allgemeine Übersicht über die Verbindung:

![Cloudübergreifende Netzwerkverbindung](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Voraussetzungen

* Um die Verbindung zwischen Azure und OCI herzustellen, benötigen Sie ein aktives Azure-Abonnement und einen aktiven OCI-Mandanten.

* Die Verbindung ist nur möglich, wenn sich ein Azure ExpressRoute-Peeringstandort in der Nähe oder am gleichen Peeringstandort wie OCI FastConnect befindet. Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurieren der direkten Konnektivität zwischen ExpressRoute und FastConnect

1. Erstellen Sie in Ihrem Azure-Abonnement in einer Ressourcengruppe eine ExpressRoute-Standardverbindung. 
    * Wählen Sie beim Erstellen der ExpressRoute-Verbindung **Oracle Cloud FastConnect** als Dienstanbieter aus. Informationen zum Erstellen einer ExpressRoute-Verbindung finden Sie in der [schrittweisen Anleitung](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Eine Azure ExpressRoute-Verbindung bietet differenzierte Bandbreitenoptionen, während FastConnect 1, 2, 5 oder 10 GBit/s unterstützt. Aus diesem Grund wird empfohlen, für ExpressRoute eine passende Bandbreitenoption auszuwählen.

    ![Erstellen einer ExpressRoute-Verbindung](media/configure-azure-oci-networking/exr-create-new.png)
1. Notieren Sie sich Ihren ExpressRoute-**Dienstschlüssel**. Sie müssen den Schlüssel bei der Konfiguration der FastConnect-Verbindung angeben.

    ![ExpressRoute-Dienstschlüssel](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Für ExpressRoute fallen Gebühren an, sobald die ExpressRoute-Verbindung bereitgestellt wird (selbst wenn der **Anbieterstatus** **Nicht bereitgestellt** lautet).

1. Erstellen Sie jeweils zwei private /30-IP-Adressräume, die nicht mit dem IP-Adressraum von Azure Virtual Network oder OCI Virtual Cloud Network überlappen. Wir bezeichnen den ersten IP-Adressraum als primären Adressraum und den zweiten IP-Adressraum als sekundären Adressraum. Notieren Sie sich die Adressen, da Sie diese für die Konfiguration der FastConnect-Verbindung benötigen.
1. Erstellen Sie ein DRG (Dynamic Routing Gateway). Sie benötigen es beim Erstellen Ihrer FastConnect-Verbindung. Weitere Informationen finden Sie in der Dokumentation zum [Dynamic Routing Gateway](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm).
1. Erstellen Sie eine FastConnect-Verbindung in Ihrem Oracle-Mandanten. Weitere Informationen finden Sie in der [Oracle-Dokumentation](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Wählen Sie in der FastConnect-Konfiguration **Microsoft Azure: ExpressRoute** als Anbieter aus.
    * Wählen Sie das DRG (Dynamic Routing Gateway) aus, das Sie im vorherigen Schritt bereitgestellt haben.
    * Wählen Sie die Bandbreite aus, die bereitgestellt werden soll. Um eine optimale Leistung zu erzielen, muss die Bandbreite mit der Bandbreite übereinstimmen, die beim Erstellen der ExpressRoute-Verbindung ausgewählt wurde.
    * Fügen Sie den ExpressRoute-Dienstschlüssel in das Feld für den **Anbieterdienstschlüssel** ein.
    * Verwenden Sie den ersten privaten /30-IP-Adressraum, den Sie in einem vorherigen Schritt erstellt haben, für die **Primary BGP IP Address** (Primäre BGP-IP-Adresse) und den zweiten privaten /30-IP-Adressraum für die **Secondary BGP IP Address** (Sekundäre BGP-IP-Adresse).
        * Weisen Sie die erste verwendbare Adresse aus den beiden Adressräumen der Oracle-BGP-IP-Adresse (primär und sekundär) und die zweite Adresse der Kunden-BGP-IP-Adresse zu (aus der Perspektive von FastConnect). Die erste verwendbare IP-Adresse entspricht der zweiten IP-Adresse im /30-Adressraum (die erste IP-Adresse wird von Microsoft reserviert).
    * Klicken Sie auf **Erstellen**.
1. Schließen Sie die Verknüpfung von FastConnect mit Virtual Cloud Network im Oracle-Mandanten ab. Verwenden Sie dazu ein DRG (Dynamic Routing Gateway) und eine Routingtabelle.
1. Navigieren Sie zu Azure, und stellen sicher, dass der **Anbieterstatus** für die ExpressRoute-Verbindung in **Bereitgestellt** geändert und dass der Peeringtyp **Azure, privat** bereitgestellt wurde. Dies ist eine Voraussetzung für die nächsten Schritte.

    ![Status des ExpressRoute-Anbieters](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klicken Sie auf das Peering **Azure, privat**. Sie sehen, dass die Peeringdetails basierend auf den Informationen, die Sie beim Einrichten der FastConnect-Verbindung eingegeben haben, automatisch konfiguriert wurden.

    ![Einstellungen für Peering vom Typ „Azure, privat“](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Verbinden des virtuellen Netzwerks mit ExpressRoute

1. Erstellen Sie ein virtuelles Netzwerk und ein Gateway für virtuelle Netzwerke, sofern dies noch nicht geschehen ist. Ausführliche Informationen finden Sie in der [schrittweisen Anleitung](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Richten Sie die Verbindung zwischen dem Gateway für virtuelle Netzwerke und ExpressRoute ein, indem Sie das [Terraform-Skript](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) oder den PowerShell-Befehl zum [Konfigurieren von ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath) ausführen.

Nachdem Sie die Netzwerkkonfiguration abgeschlossen haben, können Sie diese überprüfen, indem Sie im Azure-Portal auf dem ExpressRoute-Blatt „Privates Peering“ auf **ARP-Datensätze abrufen** und **Routingtabelle abrufen** klicken.

## <a name="automation"></a>Automation

Microsoft hat die Terraform-Skripts entwickelt, um die automatisierte Bereitstellung der Netzwerkverbindung zu ermöglichen. Die Terraform-Skripts müssen vor der Ausführung bei Azure authentifiziert werden, da sie geeignete Berechtigungen für das Azure-Abonnement benötigen. Die Authentifizierung kann mithilfe eines [Azure Active Directory-Dienstprinzipals](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oder über die Azure CLI ausgeführt werden. Weitere Informationen finden Sie in der [Terraform-Dokumentation](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Die Terraform-Skripts und die zugehörige Dokumentation zum Bereitstellen der Verbindung finden Sie in diesem [GitHub-Repository](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Überwachung

Bei der Installation der Agents in beiden Clouds können Sie den [Netzwerkleistungsmonitor (NPM)](../../../expressroute/how-to-npm.md) in Azure nutzen, um die Leistung des End-to-End-Netzwerks zu überwachen. Der NPM hilft, Netzwerkprobleme zu erkennen und zu beseitigen.

## <a name="delete-the-interconnect-link"></a>Löschen der Verbindung

Um die Verbindung zu löschen, müssen die folgenden Schritte in der angegebenen Reihenfolge ausgeführt werden. Andernfalls wird die ExpressRoute-Verbindung in einen Fehlerzustand versetzt.

1. Löschen Sie die ExpressRoute-Verbindung. Sie löschen die Verbindung, indem Sie auf der zugehörigen Seite auf das Symbol **Löschen** klicken. Weitere Informationen finden Sie in der [Dokumentation zu ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources).
1. Löschen Sie Oracle FastConnect in der Oracle Cloud Console.
1. Nachdem die Oracle FastConnect-Verbindung gelöscht wurde, können Sie die Azure ExpressRoute-Verbindung löschen.

An diesem Punkt haben Sie den Löschvorgang abgeschlossen und die Bereitstellung aufgehoben.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur cloudübergreifenden Verbindung zwischen OCI und Azure finden Sie in der [Oracle-Dokumentation](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Verwenden Sie [Terraform-Skripts](https://aka.ms/azureociinterconnecttf), um eine Infrastruktur für gezielte Oracle-Anwendungen über Azure bereitzustellen und die Netzwerkverbindung zu konfigurieren. 
