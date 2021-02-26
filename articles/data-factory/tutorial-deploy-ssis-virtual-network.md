---
title: Tutorial zum Konfigurieren einer Azure-SSIS Integration Runtime für die Verknüpfung mit einem virtuellen Netzwerk
description: Erfahren Sie, wie Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Azure-Netzwerk verknüpfen.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391234"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Konfigurieren einer Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) für die Verknüpfung mit einem virtuellen Netzwerk

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Tutorial werden die grundlegenden Schritte für die Verwendung des Azure-Portals zum Konfigurieren einer Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) für die Verknüpfung mit einem virtuellen Netzwerk beschrieben.

Dazu müssen folgende Schritte ausgeführt werden:

- Konfigurieren Sie ein virtuelles Netzwerk.
- Verknüpfen Sie die Azure-SSIS IR über das Azure Data Factory-Portal mit einem virtuellen Netzwerk.

## <a name="prerequisites"></a>Voraussetzungen

- **Azure SSIS-Integration Runtime:** Wenn Sie keine Azure-SSIS Integration Runtime haben, müssen Sie zuerst [eine Azure-SSIS Integration Runtime in Azure Data Factory bereitstellen](tutorial-deploy-ssis-packages-azure.md).

- **Benutzerberechtigung**. Der Benutzer, der die Azure-SSIS IR erstellt, muss über die [Rollenzuweisung](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) mindestens für die Azure Data Factory-Ressource verfügen, und zwar mit einer der folgenden Optionen:

    - Verwenden Sie die integrierte Rolle „Netzwerkmitwirkender“. Diese Rolle umfasst die Berechtigung _Microsoft.Network/\*_ , die jedoch einen deutlich größeren Umfang als erforderlich hat.
    - Erstellen Sie eine benutzerdefinierte Rolle, die nur die erforderliche Berechtigung _Microsoft.Network/virtualNetworks/\*/join/action_ aufweist. Wenn Sie beim Verknüpfen der Azure-SSIS IR mit einem virtuellen Azure Resource Manager-Netzwerk Ihre eigenen öffentlichen IP-Adressen für die IR verwenden möchten, beziehen Sie auch die Berechtigung _Microsoft.Network/publicIPAddresses/*/join/action_ mit in die Rolle ein.

- **Virtuelles Netzwerk**.

    - Wenn Sie noch keines haben, [erstellen Sie ein virtuelles Netzwerk über das Azure-Portal](../virtual-network/quick-create-portal.md).

    - Stellen Sie sicher, dass bestimmte Azure-Netzwerkressourcen durch die Ressourcengruppe des virtuellen Netzwerks erstellt und gelöscht werden können.
    
        Die Azure SSIS-IR muss bestimmte Netzwerkressourcen unter der gleichen Ressourcengruppe erstellen wie das virtuelle Netzwerk. Diese Ressourcen umfassen Folgendes:
        - Einen Azure-Lastenausgleich mit dem Namen *\<Guid>-azurebatch-cloudserviceloadbalancer*
        - Eine Netzwerksicherheitsgruppe mit dem Namen *\<Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Eine öffentliche Azure-IP-Adresse mit dem Namen „-azurebatch-cloudservicepublicip“
    
        Diese Ressourcen werden beim Start Ihrer Azure-SSIS IR erstellt. Sie werden gelöscht, wenn die Azure-SSIS IR beendet wird. Damit Ihre Azure-SSIS IR ordnungsgemäß beendet werden kann, sollten Sie diese Netzwerkressourcen nicht in anderen Ressourcen wiederverwenden.

    - Stellen Sie sicher, dass für die Ressourcengruppe/das Abonnement, zu der bzw. dem das virtuelle Netzwerk gehört, keine [Ressourcensperre](../azure-resource-manager/management/lock-resources.md) besteht. Wenn Sie eine Schreibschutzsperre oder eine Löschsperre konfigurieren, kann beim Starten und Beenden Ihrer Azure-SSIS IR ein Fehler auftreten oder die IR nicht mehr reagieren.

    - Stellen Sie sicher, dass Sie keine Azure Policy-Zuweisung aufweisen, die verhindert, dass die folgenden Ressourcen unter der Ressourcengruppe/dem Abonnement erstellt werden, zu der bzw. dem das virtuelle Netzwerk gehört:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Die nachstehenden Szenarien einer **Netzwerkkonfiguration** werden in diesem Tutorial nicht behandelt:
    - Sie verwenden Ihre eigenen öffentlichen IP-Adressen für die Azure-SSIS IR.
    - Sie verwenden einen eigenen DNS-Server (Domain Name System).
    - Sie verwenden eine Netzwerksicherheitsgruppe (NSG) im Subnetz.
    - Sie verwenden Azure ExpressRoute oder eine benutzerdefinierte Route (User-Defined Route, UDR).
    - Sie verwenden eine benutzerdefinierte Azure-SSIS IR.
    
    Weitere Informationen finden Sie unter [Konfiguration von virtuellen Netzwerken](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Konfigurieren Sie ein virtuelles Netzwerk über das Azure-Portal, bevor Sie versuchen, eine Azure-SSIS IR mit dem Netzwerk zu verknüpfen.

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke**, und wählen Sie die Option aus.

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus.

1. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Eigenschaften** aus.

1. Wählen Sie für die **RESSOURCEN-ID** die Schaltfläche „Kopieren“ aus, um die Ressourcen-ID für das virtuelle Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei.

1. Wählen Sie im linken Menü **Subnetze** aus.

    - Stellen Sie sicher, dass das ausgewählte Subnetz ausreichend verfügbaren Adressraum für die Azure-SSIS IR aufweist. Sie benötigen mindestens doppelt so viele verfügbare IP-Adressen, wie IR-Knoten vorhanden sind. Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert. Diese Adressen können nicht verwendet werden. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus werden drei weitere Adressen für Azure-Dienste verwendet. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Sie sollten nicht das GatewaySubnet zum Bereitstellen einer Azure-SSIS IR auswählen, da es Gateways für virtuelle Netzwerke vorbehalten ist.
    - Verwenden Sie kein Subnetz, das ausschließlich von anderen Azure-Diensten genutzt wird (z. B. verwaltete SQL-Datenbank-Instanzen, App Service usw.).

1. Vergewissern Sie sich, dass dieser Azure Batch-Anbieter in dem Azure-Abonnement, das über das virtuelle Netzwerk verfügt, registriert ist. Alternativ können Sie auch den Azure Batch-Anbieter registrieren. Wenn in Ihrem Abonnement bereits ein Azure Batch-Konto enthalten ist, ist Ihr Abonnement für Azure Batch registriert. (Wenn Sie die Azure-SSIS Integration Runtime im Data Factory-Portal erstellen, wird der Azure Batch-Anbieter automatisch für Sie erstellt.)

   1. Wählen Sie im Azure-Portal im linken Menü **Abonnements** aus.

   1. Wählen Sie Ihr Abonnement aus.

   1. Wählen Sie auf der linken Seite **Ressourcenanbieter** aus, und bestätigen Sie, dass **Microsoft.Batch** ein registrierter Anbieter ist.

   ![Bestätigung des Status „Registriert“](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Wenn **Microsoft.Batch** nicht in der Liste angezeigt wird, erstellen Sie zum Registrieren [ein leeres Azure Batch-Konto](../batch/batch-account-create-portal.md) in Ihrem Abonnement. Sie können es später wieder löschen.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen der Azure SSIS-IR mit einem virtuellen Netzwerk

Nachdem Sie Ihr virtuelles Azure Resource Manager-Netzwerk oder klassisches virtuelles Netzwerk konfiguriert haben, können Sie die Azure-SSIS IR mit dem virtuellen Netzwerk verknüpfen:

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü **Data Factorys** aus. Wenn **Data Factorys** nicht im Menü angezeigt wird, wählen Sie **Weitere Dienste** und dann im Abschnitt **INTELLIGENCE + ANALYSE** die Option **Data Factorys** aus.

   ![Liste von Data Factorys](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wählen Sie Ihre Data Factory mit der Azure-SSIS IR in der Liste aus. Die Startseite für Ihre Data Factory wird angezeigt. Wählen Sie die Kachel **Erstellen und überwachen** aus. Die Data Factory-Benutzeroberfläche wird auf einer separaten Registerkarte angezeigt.

   ![Data Factory-Startseite](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Wechseln Sie in der Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**, klicken Sie auf **Verbindungen**, und wechseln Sie zur Registerkarte **Integration Runtimes**.

   ![Registerkarte „Integration Runtimes“](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Wenn Ihre Azure-SSIS IR ausgeführt wird, wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Beenden** aus. Sie können Ihre Azure-SSIS IR erst bearbeiten, wenn sie beendet wurde.

   ![Beenden der IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Bearbeiten** aus.

   ![Bearbeiten der Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Navigieren Sie im Bereich für die Integration Runtime-Einrichtung durch die Abschnitte **Allgemeine Einstellungen** und **SQL-Einstellungen**, indem Sie die Schaltfläche **Weiter** auswählen.

1. Gehen Sie im Abschnitt **Erweiterte Einstellungen** folgendermaßen vor:
   1. Aktivieren Sie das Kontrollkästchen **VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden**.

   1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

   1. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

   1. Wählen Sie für **Typ** den Typ des virtuellen Netzwerks aus: „Klassisch“ oder „Azure Resource Manager“. Sie sollten ein virtuelles Azure Resource Manager-Netzwerk auswählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

   1. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Es sollte mit dem Netzwerk übereinstimmen, das für SQL-Datenbank mit VNET-Dienstendpunkten oder die verwaltete SQL-Instanz mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Oder er sollte mit dem Namen des Netzwerks übereinstimmen, das mit Ihrem lokalen Netzwerk verbunden ist. Andernfalls kann es sich um ein beliebiges virtuelles Netzwerk handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Es sollte mit dem Subnetz übereinstimmen, das für SQL-Datenbank mit VNET-Dienstendpunkten zum Hosten der SSISDB verwendet wird. Oder es sollte ein anderes Subnetz sein als das, das für Ihre verwaltete SQL-Instanz mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Andernfalls kann es sich um ein beliebiges Subnetz handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Wählen Sie **VNET-Überprüfung** aus. Wenn die Überprüfung erfolgreich war, wählen Sie **Weiter** aus.

   ![Erweiterte Einstellungen mit einem virtuellen Netzwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Überprüfen Sie im Abschnitt **Zusammenfassung** alle Einstellungen für Ihre Azure-SSIS IR. Wählen Sie dann **Aktualisieren** aus.

1. Starten Sie die Azure-SSIS IR, indem Sie in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Starten** auswählen. Es dauert etwa 20 bis 30 Minuten, um die Azure-SSIS IR zu starten, die mit einem virtuellen Netzwerk verknüpft wird.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über das [Verknüpfen der Azure-SSIS IR mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).