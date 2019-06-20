---
title: Netzwerksicherheit in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Grundlegendes zur Funktionsweise der Integration virtueller Netzwerke in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 7d6c826df2a509ffb378809e3682073bd5ab1301
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60612540"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integration eines virtuellen Netzwerks für Azure Data Lake Storage Gen1

Dieser Artikel enthält eine Einführung in die Integration virtueller Netzwerke für Azure Data Lake Storage Gen1. Bei der Integration virtueller Netzwerke können Sie Ihre Konten so konfigurieren, dass Datenverkehr nur von bestimmten virtuellen Netzwerken und Subnetzen akzeptiert wird. 

Mit diesem Feature können Sie Ihr Data Lake Storage-Konto vor externen Bedrohungen schützen.

Die Integration virtueller Netzwerke für Data Lake Storage Gen1 nutzt die Sicherheit von VNET-Dienstendpunkten zwischen Ihrem virtuellen Netzwerk und Azure Active Directory (Azure AD), um zusätzliche Sicherheitsansprüche im Zugriffstoken zu generieren. Diese Ansprüche werden dann genutzt, um Ihr virtuelles Netzwerk mit Ihrem Data Lake Storage Gen1-Konto zu authentifizieren und den Zugriff zu ermöglichen.

> [!NOTE]
> Für die Verwendung dieser Funktionen fallen keine zusätzlichen Gebühren an. Ihrem Konto wird der Standardsatz für Data Lake Storage Gen1 berechnet. Weitere Informationen finden Sie unter [Azure Data Lake Storage – Preise](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Preisinformationen zu allen anderen Azure-Diensten, die Sie nutzen, finden Sie unter [Azure-Preise](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Szenarien für die Integration virtueller Netzwerke für Data Lake Storage Gen1

Mit der Integration virtueller Netzwerke per Data Lake Storage Gen1 können Sie den Zugriff auf Ihr Data Lake Storage Gen1-Konto für bestimmte virtuelle Netzwerke und Subnetze einschränken. Nachdem Ihr Konto für das angegebene VNET-Subnetz gesperrt wurde, haben auch andere virtuelle Netzwerke bzw. VMs in Azure keinen Zugriff. Im Hinblick auf die Funktion ermöglicht die Integration virtueller Netzwerke per Data Lake Storage Gen1 das gleiche Szenario wie mit [VNET-Dienstendpunkten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Einige wichtige Unterschiede werden in den folgenden Abschnitten ausführlich beschrieben. 

![Szenariodiagramm für die Integration virtueller Netzwerke per Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Die vorhandenen IP-Firewallregeln können zusätzlich zu VNET-Regeln verwendet werden, um auch Zugriff aus lokalen Netzwerken zuzulassen. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Optimales Routing mit Integration virtueller Netzwerke per Data Lake Storage Gen1

Ein Hauptvorteil von VNET-Dienstendpunkten ist das [optimale Routing](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) aus Ihrem virtuellen Netzwerk. Sie können die gleiche Routenoptimierung für Data Lake Storage Gen1-Konten durchführen. Verwenden Sie die folgenden [benutzerdefinierten Routen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) aus Ihrem virtuellen Netzwerk zu Ihrem Data Lake Storage Gen1-Konto.

**Öffentliche IP-Adresse für Data Lake Storage**: Verwenden Sie die öffentliche IP-Adresse für Ihre Data Lake Storage Gen1-Zielkonten. [Lösen Sie die DNS-Namen Ihrer Konten auf](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity), um die IP-Adressen für Ihr Data Lake Storage Gen1-Konto zu identifizieren. Erstellen Sie für jede Adresse einen separaten Eintrag.

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Herausfilterung von Daten aus dem virtuellen Netzwerk des Kunden

Neben dem Schützen der Data Lake Storage-Konten vor dem Zugriff aus dem virtuellen Netzwerk sind Sie unter Umständen auch daran interessiert sicherzustellen, dass keine Herausfilterung für ein nicht autorisiertes Konto erfolgt.

Verwenden Sie eine Firewalllösung in Ihrem virtuellen Netzwerk, um den ausgehenden Datenverkehr basierend auf der Zielkonto-URL zu filtern. Lassen Sie den Zugriff nur für genehmigte Data Lake Storage Gen1-Konten zu.

Einige der verfügbaren Optionen sind:
- [Azure Firewall:](https://docs.microsoft.com/azure/firewall/overview) [Stellen Sie eine Azure Firewall für Ihr virtuelles Netzwerk bereit, und konfigurieren Sie sie](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal). Schützen Sie den ausgehenden Data Lake Storage-Datenverkehr, und sperren Sie ihn für die bekannte und genehmigte Konto-URL.
- Firewall für [virtuelle Netzwerkgeräte](https://azure.microsoft.com/solutions/network-appliances/): Ihr Administrator lässt unter Umständen nur die Verwendung der Produkte von bestimmten kommerziellen Firewallanbietern zu. Verwenden Sie eine Firewalllösung vom Azure Marketplace, die auf einem virtuellen Netzwerkgerät basiert, um die gleiche Funktion durchzuführen.

> [!NOTE]
> Mit der Nutzung von Firewalls im Datenpfad wird darin ein zusätzlicher Hop eingeführt. Dies kann beim End-to-End-Datenaustausch die Netzwerkleistung beeinträchtigen. Unter Umständen ergeben sich Auswirkungen auf die Durchsatzverfügbarkeit und Verbindungslatenz. 

## <a name="limitations"></a>Einschränkungen

- HDInsight-Cluster, die erstellt wurden, bevor die Unterstützung für die Integration von virtuellen Netzwerken per Data Lake Storage Gen1 verfügbar war, müssen für dieses neue Feature neu erstellt werden.
 
- Wenn Sie einen neuen HDInsight-Cluster erstellen und ein Data Lake Storage Gen1-Konto mit aktivierter Integration virtueller Netzwerke auswählen, tritt für den Prozess ein Fehler auf. Deaktivieren Sie zuerst die VNET-Regel. Sie können auch auf dem Blatt **Firewall und virtuelle Netzwerke** des Data Lake Storage-Kontos die Option **Zugriff erlauben von: Alle Netzwerke und Dienste** wählen. Erstellen Sie dann den HDInsight-Cluster, bevor Sie abschließend die VNET-Regel erneut aktivieren oder die Option **Zugriff erlauben von: Alle Netzwerke und Dienste** deaktivieren. Weitere Informationen finden Sie im Abschnitt [Ausnahmen](#exceptions).

- Die Integration virtueller Netzwerke per Data Lake Storage Gen1 funktioniert nicht mit [verwalteten Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
  
- Auf Datei- und Ordnerdaten in Ihrem VNET-fähigen Data Lake Storage Gen1-Konto kann über das Portal nicht zugegriffen werden. Diese Einschränkung gilt auch für den Zugriff von einem virtuellen Computer innerhalb des virtuellen Netzwerks und für Aktivitäten wie die Verwendung des Daten-Explorers. Aktivitäten zur Kontoverwaltung funktionieren weiterhin. Auf die Datei- und Ordnerdaten in Ihrem VNET-fähigen Data Lake Storage-Konto kann über alle Ressourcen außerhalb des Portals zugegriffen werden. Zu diesen Ressourcen gehören SDK-Zugriff, PowerShell-Skripts und andere Azure-Dienste, sofern sie nicht über das Portal verlaufen. 

## <a name="configuration"></a>Konfiguration

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Schritt 1: Konfigurieren Ihres virtuellen Netzwerks für die Verwendung eines Azure AD-Dienstendpunkts

1.  Navigieren Sie zum Azure-Portal, und melden Sie sich an Ihrem Konto an.
 
2.  [Erstellen Sie ein neues virtuelles Netzwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in Ihrem Abonnement. Sie können auch zu einem vorhandenen virtuellen Netzwerk navigieren. Das virtuelle Netzwerk muss sich in derselben Region wie das Data Lake Storage Gen 1-Konto befinden.
 
3.  Wählen Sie auf dem Blatt **Virtuelles Netzwerk** die Option **Dienstendpunkte**.
 
4.  Wählen Sie **Hinzufügen**, um einen neuen Dienstendpunkt hinzuzufügen.

    ![Hinzufügen eines VNET-Dienstendpunkts](media/data-lake-store-network-security/config-vnet-1.png)

5.  Wählen Sie **Microsoft.AzureActiveDirectory** als Dienst für den Endpunkt aus.

     ![Auswählen des Dienstendpunkts „Microsoft.AzureActiveDirectory“](media/data-lake-store-network-security/config-vnet-2.png)

6.  Wählen Sie die Subnetze aus, für die Sie Verbindungen zulassen möchten. Wählen Sie **Hinzufügen**.

    ![Auswählen des Subnetzes](media/data-lake-store-network-security/config-vnet-3.png)

7.  Das Hinzufügen des Dienstendpunkts kann bis zu 15 Minuten dauern. Nachdem er hinzugefügt wurde, wird er in der Liste angezeigt. Vergewissern Sie sich, dass er angezeigt wird und alle Details der Konfiguration entsprechen.
 
    ![Erfolgreiches Hinzufügen des Dienstendpunkts](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Schritt 2: Einrichten des zulässigen virtuellen Netzwerks oder Subnetzes für Ihr Data Lake Storage Gen1-Konto

1.  Nachdem Sie Ihr virtuelles Netzwerk konfiguriert haben, können Sie unter Ihrem Abonnement [ein neues Azure Data Lake Storage Gen1-Konto erstellen](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account). Alternativ dazu können Sie auch zu einem vorhandenen Data Lake Storage Gen1-Konto navigieren. Das Data Lake Storage Gen 1-Konto muss sich in derselben Region wie das virtuelle Netzwerk befinden.
 
2.  Wählen Sie **Firewall und virtuelle Netzwerke**.

    > [!NOTE]
    > Melden Sie sich vom Portal ab, falls die Option **Firewall und virtuelle Netzwerke** in den Einstellungen nicht angezeigt wird. Schließen Sie den Browser, und löschen Sie den Browsercache. Starten Sie den Computer neu, und versuchen Sie es erneut.

       ![Hinzufügen einer VNET-Regel zu Ihrem Data Lake Storage-Konto](media/data-lake-store-network-security/config-adls-1.png)

3.  Klicken Sie auf **Ausgewählte Netzwerke**.
 
4.  Wählen Sie **Vorhandenes virtuelles Netzwerk hinzufügen** aus.

    ![Hinzufügen eines vorhandenen virtuellen Netzwerks](media/data-lake-store-network-security/config-adls-2.png)

5.  Wählen Sie die virtuellen Netzwerke und Subnetze aus, für die die Verbindung zugelassen werden soll. Wählen Sie **Hinzufügen**.

    ![Überprüfen des virtuellen Netzwerks und der Subnetze](media/data-lake-store-network-security/config-adls-3.png)

6.  Stellen Sie sicher, dass die virtuellen Netzwerke und Subnetze in der Liste korrekt angezeigt werden. Wählen Sie **Speichern** aus.

    ![Speichern der neuen Regel](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Nach dem Speichern kann es bis zu fünf Minuten dauern, bis die Einstellungen wirksam werden.

7.  [Optional] Auf der Seite **Firewall und virtuelle Netzwerke** im Abschnitt **Firewall** können Sie die Konnektivität für bestimmte IP-Adressen zulassen. 

## <a name="exceptions"></a>Ausnahmen
Sie können die Konnektivität von Azure-Diensten und VMs außerhalb Ihrer ausgewählten virtuellen Netzwerke ermöglichen. Wählen Sie auf dem Blatt **Firewall und virtuelle Netzwerke** im Bereich **Ausnahmen** unter zwei Optionen:
 
- **Hiermit wird allen Azure-Diensten Zugriff auf dieses Data Lake Storage Gen1-Konto gewährt**. Diese Option ermöglicht Azure-Diensten, z.B. Azure Data Factory, Azure Event Hubs und allen Azure-VMs, die Kommunikation mit Ihrem Data Lake Storage-Konto.

- **Hiermit wird Azure Data Lake Analytics Zugriff auf dieses Data Lake Storage Gen1-Konto gewährt.** Diese Option ermöglicht Data Lake Analytics-Konnektivität mit diesem Data Lake Storage-Konto. 

  ![Ausnahmen für Firewall und virtuelle Netzwerke](media/data-lake-store-network-security/firewall-exceptions.png)

Es wird empfohlen, diese Ausnahmen deaktiviert zu lassen. Aktivieren Sie sie nur, falls Sie Konnektivität von diesen anderen Diensten von außerhalb Ihres virtuellen Netzwerks benötigen.
