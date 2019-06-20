---
title: Übersicht über Azure Firewall-Diensttags
description: Dieser Artikel bietet eine Übersicht über die Azure Firewall-Diensttags.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193729"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall-Diensttags

Ein Diensttag steht für eine Gruppe von IP-Adressen und hat die Aufgabe, bei der Erstellung von Sicherheitsregeln die Komplexität zu verringern. Sie können kein eigenes Diensttag erstellen und auch nicht angeben, welche IP-Adressen in einem Tag enthalten sind. Microsoft verwaltet die Adresspräfixe, die mit dem Diensttag abgedeckt werden, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Azure Firewall-Diensttags können im Zielfeld für Netzwerkregeln verwendet werden. Sie können sie anstelle bestimmter IP-Adressen verwenden.

## <a name="supported-service-tags"></a>Unterstützte Diensttags

Die folgenden Diensttags können in Azure Firewall-Netzwerkregeln verwendet werden:

* **AzureCloud** (nur Resource Manager): Dieses Tag gibt den IP-Adressraum für Azure an, einschließlich aller [öffentlichen IP-Adressen für das Datencenter](https://www.microsoft.com/download/details.aspx?id=41653). Wenn Sie *AzureCloud* als Wert angeben, wird der Datenverkehr für öffentliche Azure-IP-Adressen zugelassen oder verweigert. Falls Sie den Zugriff auf AzureCloud nur für eine bestimmte [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region angeben. Falls Sie den Zugriff auf AzureCloud von Azure beispielsweise nur für die Region „USA, Osten“ zulassen möchten, können Sie *AzureCloud.EastUS* als Diensttag angeben. 
* **AzureTrafficManager** (nur Resource Manager): Dieses Tag gibt den IP-Adressraum für die Test-IP-Adressen des Azure Traffic Manager an. Weitere Informationen zu Test-IP-Adressen von Traffic Manager finden Sie unter [Häufig gestellte Fragen (FAQ) zu Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Storage** (nur Resource Manager): Dieses Tag gibt den IP-Adressraum für den Azure Storage-Dienst an. Wenn Sie *Storage* als Wert angeben, wird der Datenverkehr für den Speicher zugelassen oder verweigert. Falls Sie den Zugriff auf den Speicher nur für eine bestimmte [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region angeben. Wenn Sie den Zugriff auf Azure Storage beispielsweise nur für die Region „USA, Osten“ zulassen möchten, können Sie *Storage.EastUS* als Diensttag angeben. Das Tag steht für den Dienst, aber nicht für bestimmte Instanzen des Diensts. Beispielsweise steht das Tag für den Azure Storage-Dienst, aber nicht für ein bestimmtes Azure Storage-Konto.
* **Sql** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe der Azure SQL-Datenbank- und Azure SQL Data Warehouse-Dienste an. Wenn Sie *Sql* als Wert angeben, wird der Datenverkehr für Sql zugelassen oder verweigert. Falls Sie den Zugriff auf Sql nur für eine bestimmte [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region angeben. Wenn Sie den Zugriff auf Azure SQL-Datenbank beispielsweise nur für die Region „USA, Osten“ zulassen möchten, können Sie *Sql.EastUS* als Diensttag angeben. Das Tag steht für den Dienst, aber nicht für bestimmte Instanzen des Diensts. Beispielsweise steht das Tag für den Azure SQL-Datenbank-Dienst, aber nicht für eine bestimmte SQL-Datenbank oder einen bestimmten SQL-Server.
* **AzureCosmosDB** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Cosmos DB-Diensts an. Wenn Sie *AzureCosmosDB* als Wert angeben, wird der Datenverkehr für AzureCosmosDB zugelassen oder verweigert. Falls Sie den Zugriff auf AzureCosmosDB nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „AzureCosmosDB.[Name der Region]“ angeben.
* **AzureKeyVault** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Key Vault-Diensts an. Wenn Sie *AzureKeyVault* als Wert angeben, wird der Datenverkehr für AzureKeyVault zugelassen oder verweigert. Falls Sie den Zugriff auf AzureKeyVault nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „AzureKeyVault.[Name der Region]“ angeben.
* **EventHub** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Event Hub-Diensts an. Wenn Sie *EventHub* als Wert angeben, wird der Datenverkehr für EventHub zugelassen oder verweigert. Falls Sie den Zugriff auf EventHub nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „EventHub.[Name der Region]“ angeben. 
* **ServiceBus** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Service Bus-Diensts an. Wenn Sie *ServiceBus* als Wert angeben, wird der Datenverkehr für ServiceBus zugelassen oder verweigert. Falls Sie den Zugriff auf ServiceBus nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „ServiceBus.[Name der Region]“ angeben.
* **MicrosoftContainerRegistry** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Microsoft Container Registry-Diensts an. Wenn Sie *MicrosoftContainerRegistry* als Wert angeben, wird der Datenverkehr für MicrosoftContainerRegistry zugelassen oder verweigert. Falls Sie den Zugriff auf MicrosoftContainerRegistry nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „MicrosoftContainerRegistry.[Name der Region]“ angeben.
* **AzureContainerRegistry** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Container Registry-Diensts an. Wenn Sie *AzureContainerRegistry* als Wert angeben, wird der Datenverkehr für AzureContainerRegistry zugelassen oder verweigert. Falls Sie den Zugriff auf AzureContainerRegistry nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „AzureContainerRegistry.[Name der Region]“ angeben. 
* **AppService** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure App Service-Diensts an. Wenn Sie *AppService* als Wert angeben, wird der Datenverkehr für AppService zugelassen oder verweigert. Falls Sie den Zugriff auf AppService nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „AppService.[Name der Region]“ angeben. 
* **AppServiceManagement** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure App Service Management-Diensts an. Wenn Sie *AppServiceManagement* als Wert angeben, wird der Datenverkehr für AppServiceManagement zugelassen oder verweigert. 
* **ApiManagement** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure API Management-Diensts an. Wenn Sie *ApiManagement* als Wert angeben, wird der Datenverkehr für ApiManagement zugelassen oder verweigert.  
* **AzureConnectors** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Connectors-Diensts an. Wenn Sie *AzureConnectors* als Wert angeben, wird der Datenverkehr für AzureConnectors zugelassen oder verweigert. Falls Sie den Zugriff auf AzureConnectors nur in einer bestimmten [Region](https://azure.microsoft.com/regions) zulassen möchten, können Sie die Region mit dem Format „AzureConnectors.[Name der Region]“ angeben.
* **AzureDataLake** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Data Lake-Diensts an. Wenn Sie *AzureDataLake* als Wert angeben, wird der Datenverkehr für AzureDataLake zugelassen oder verweigert.
* **AzureActiveDirectory** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des Azure Active Directory-Diensts an. Wenn Sie *AzureActiveDirectory* als Wert angeben, wird der Datenverkehr für AzureActiveDirectory zugelassen oder verweigert.
* **AzureMonitor** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des AzureMonitor-Diensts an. Wenn Sie *AzureMonitor* als Wert angeben, wird der Datenverkehr für AzureMonitor zugelassen oder verweigert.
* **ServiceFabric** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des ServiceFabric-Diensts an. Wenn Sie *ServiceFabric* als Wert angeben, wird der Datenverkehr für ServiceFabric zugelassen oder verweigert.
* **AzureMachineLearning** (nur Resource Manager): Dieses Tag gibt die Adresspräfixe des AzureMachineLearning-Diensts an. Wenn Sie *AzureMachineLearning* als Wert angeben, wird der Datenverkehr für AzureMachineLearning zugelassen oder verweigert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Firewall-Regeln finden Sie unter [Logik für die Azure Firewall-Regelverarbeitung](rule-processing.md).