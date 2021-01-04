---
title: Installieren eines lokalen Datengateways für Azure Analysis Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein lokales Datengateway installieren und konfigurieren, um eine Verbindung zu lokalen Datenquellen von einem Azure Analysis Services-Server herzustellen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 64bd9e4a4cf78d2628e946af30c2d290ff002cf7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081143"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installieren und Konfigurieren eines lokalen Datengateways

Ein lokales Datengateway ist erforderlich, wenn sich mindestens ein Azure Analysis Services-Server in der gleichen Region mit lokalen Datenquellen verbindet.  Auch wenn das installierte Gateway für alle weiteren Dienste – z.B. Power BI, Power Apps und Logic Apps – identisch ist, sind für Azure Analysis Services und Logic Apps einige zusätzliche Schritte erforderlich. Die Informationen in diesem Artikel gelten speziell für **Azure Analysis Services**. 

Weitere Informationen zur Zusammenarbeit von Azure Analysis Services mit dem Gateway finden Sie unter [Herstellen einer Verbindung mit lokalen Datenquellen](analysis-services-gateway.md). Weitere Informationen zu erweiterten Installationsszenarien und zum Gateway im Allgemeinen finden Sie unter [Dokumentation zu lokalen Datengateways](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Voraussetzungen

**Mindestanforderungen.**

* .NET 4.5 Framework
* 64-Bit-Version von Windows 8/Windows Server 2012 R2 (oder höher)

**Empfohlen.**

* 8-Kern-CPU
* 8 GB Arbeitsspeicher
* 64-Bit-Version von Windows 8/Windows Server 2012 R2 (oder höher)

**Wichtige Hinweise:**

* Wenn sich Ihr Gateway während des Setups bei Azure registriert, wird die Standardregion für Ihr Abonnement ausgewählt. Sie können ein anderes Abonnement und eine andere Region auswählen. Wenn Sie Server in mehreren Regionen haben, müssen Sie für jede Region ein Gateway installieren. 
* Das Gateway darf nicht auf einem Domänencontroller installiert werden.
* Auf einem einzelnen Computer kann nur ein Gateway installiert werden.
* Installieren Sie das Gateway auf einem Computer, der eingeschaltet bleibt und nicht in den Ruhezustand versetzt wird.
* Installieren Sie das Gateway nicht auf einem Computer, der ausschließlich über eine Drahtlosverbindung mit dem Netzwerk verfügt. Die Leistung kann beeinträchtigt werden.
* Wenn Sie das Gateway installieren, muss das Benutzerkonto, unter dem Sie auf Ihrem Computer angemeldet sind, über die Berechtigungen „Anmelden als Dienst“ verfügen. Wenn die Installation abgeschlossen ist, verwendet der lokale Datengatewaydienst das Konto „NT SERVICE\PBIEgwService“, um sich als Dienst anzumelden. Ein anderes Konto kann während des Setups oder nach dem Setup in „Dienste“ angegeben werden. Stellen Sie sicher, dass die Gruppenrichtlinieneinstellungen sowohl das Konto, mit dem Sie sich beim Installieren angemeldet haben, als auch das Dienstkonto, das Sie für die Berechtigungen „Anmelden als Dienst“ ausgewählt haben, zulassen.
* Melden Sie sich bei Azure an. Verwenden Sie dazu ein Konto in Azure AD mit dem gleichen [Mandanten](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant), der auch für das Abonnement verwendet wird, unter dem Sie das Gateway registrieren. Für das Installieren und Registrieren eines Gateways werden keine Azure B2B (Gast)-Konten unterstützt.
* Wenn sich die Datenquellen in einem Azure Virtual Network (VNET) befinden, müssen Sie die [AlwaysUseGateway](analysis-services-vnet-gateway.md)-Servereigenschaft konfigurieren.

## <a name="download"></a>Download

 [Gateway herunterladen](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Installieren

1. Führen Sie das Setup aus.

2. Wählen Sie **Lokales Datengateway** aus.

   ![Select](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Wählen Sie einen Speicherort, akzeptieren Sie die Lizenzbedingungen, und klicken Sie dann auf **Installieren**.

   ![Installationsspeicherort und Lizenzbedingungen](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Melden Sie sich bei Azure an. Das Konto muss sich in Ihrem Azure Active Directory-Mandanten befinden. Dieses Konto wird vom Gatewayadministrator verwendet. Für das Installieren und Registrieren des Gateways werden keine Azure B2B (Gast)-Konten unterstützt.

   ![Anmelden bei Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Wenn Sie sich mit einem Domänenkonto anmelden, wird es Ihrem Unternehmenskonto in Azure AD zugeordnet. Ihr Unternehmenskonto wird vom Gatewayadministrator verwendet.

## <a name="register"></a>Register

Um eine Gatewayressource in Azure zu erstellen, müssen Sie die lokale Instanz registrieren, die Sie mit dem Gateway-Clouddienst installiert haben. 

1.  Wählen Sie **Ein neues Gateway auf diesem Computer registrieren** aus.

    ![Screenshot: Hervorgehobene Option „Ein neues Gateway auf diesem Computer registrieren“](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Geben Sie einen Namen und Wiederherstellungsschlüssel für Ihr Gateway ein. Standardmäßig verwendet das Gateway die Standardregion Ihres Abonnements. Wenn Sie eine andere Region auswählen müssen, wählen Sie **Region ändern** aus.

    > [!IMPORTANT]
    > Bewahren Sie Ihren Wiederherstellungsschlüssel an einem sicheren Ort auf. Der Wiederherstellungsschlüssel ist erforderlich, um ein Gateway zu übernehmen, zu migrieren oder wiederherzustellen. 

   ![Register](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Erstellen einer Azure-Gatewayressource

Nachdem Sie Ihr Gateway registriert und installiert haben, müssen Sie in Azure eine Gatewayressource erstellen. Melden Sie sich bei Azure mit dem Konto an, das Sie beim Registrieren des Gateways verwendet haben.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** , suchen Sie nach **Lokales Datengateway** , und klicken Sie dann auf **Erstellen**.

   ![Erstellen einer Gatewayressource](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Geben Sie in **Verbindungsgateway erstellen** diese Einstellungen ein:

   * **Name** : Geben Sie einen Namen für die Gatewayressource ein. 

   * **Abonnement** : Wählen Sie das Azure-Abonnement aus, das Sie der Gatewayressource zuordnen möchten. 
   
     Das standardmäßige Abonnement basiert auf dem Azure-Konto, das Sie zum Anmelden verwendet haben.

   * **Ressourcengruppe** : Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.

   * **Standort** : Wählen Sie die Region aus, in der Sie Ihr Gateway registriert haben.

   * **Installationsname:** Wenn Ihre Gatewayinstallation nicht bereits ausgewählt ist, wählen Sie das Gateway aus, das Sie auf Ihrem Computer installiert und registriert haben. 

     Klicken Sie auf **Erstellen** , wenn Sie fertig sind.

## <a name="connect-gateway-resource-to-server"></a>Verbinden der Gatewayressource mit dem Server

> [!NOTE]
> Das Herstellen einer Verbindung mit einer Gatewayressource in einem anderen Abonnement als dem, in dem sich Ihr Server befindet, wird im Portal nicht unterstützt, bei Verwendung von PowerShell hingegen schon.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klicken Sie in der Übersicht über Ihre Azure Analysis Services-Server auf **Lokales Datengateway**.

   ![Verbinden des Servers mit dem Gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Wählen Sie in **Lokales Datengateway für die Verbindung auswählen** Ihre Gatewayressource aus, und klicken Sie dann auf **Ausgewähltes Gateway verbinden**.

   ![Verbinden des Servers mit der Gatewayressource](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Wenn Ihr Gateway nicht in der Liste nicht angezeigt wird, befindet sich Ihr Server wahrscheinlich nicht in der Region, die Sie beim Registrieren des Gateways angegeben haben.

    Wenn die Verbindung zwischen Ihrem Server und der Gatewayressource erfolgreich ist, wird der Status als **Verbunden** angezeigt.


    ![Erfolgreiche Verbindung des Servers mit der Gatewayressource](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie [Get-AzResource](/powershell/module/az.resources/get-azresource), um die ResourceID für das Gateway abzurufen. Verbinden Sie dann die Gatewayressource mit einem vorhandenen oder neuen Server, indem Sie **-GatewayResourceID** in [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) oder [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) angeben.

So rufen Sie die ID der Gatewayressource ab:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

So konfigurieren Sie einen vorhandenen Server:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

Das ist alles. Wenn Sie Ports öffnen oder eine Problembehandlung durchführen müssen, lesen Sie den Artikel zum [lokalen Datengateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Analysis Services](analysis-services-manage.md)   
* [Abrufen von Daten aus Azure Analysis Services](analysis-services-connect.md)   
* [Verwenden eines Gateways für Datenquellen in einer Azure Virtual Network-Instanz](analysis-services-vnet-gateway.md)