---
title: 'Schnellstart: WildFly in CentOS'
description: Bereitstellen von Java-Anwendungen für WildFly auf CentOS-VM
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: ddd6f277bc71467060aa2279d93f9410a1327dde
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486605"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Schnellstart: WildFly in CentOS 8

Dieser Schnellstart zeigt Ihnen, wie Sie den eigenständigen Knoten von WildFly auf CentOS 8-VMs bereitstellen können. Er ist ideal für die Entwicklung und das Testen von Java-Unternehmensanwendungen in Azure geeignet. Ein Anwendungsserverabonnement ist für die Bereitstellung dieses Schnellstarts nicht erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder [kostenlos ein Konto erstellen](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Anwendungsfall

WildFly ist ideal für die Entwicklung und das Testen von Java-Unternehmensanwendungen in Azure geeignet. Listen der in den WildFly 18-Serverkonfigurationsprofilen verfügbaren Technologien finden Sie im [WildFly-Leitfaden für erste Schritte](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

Sie können WildFly je nach Anwendungsfall entweder im eigenständigen oder im Clustermodus verwenden. Sie können die Hochverfügbarkeit von kritischen Jakarta EE-Anwendungen durch WildFly in einem Cluster von Knoten sicherstellen. Nehmen Sie eine geringe Anzahl von Änderungen an der Anwendungskonfiguration vor, und stellen Sie dann die Anwendung im Cluster bereit. Weitere Informationen hierzu finden Sie im [WildFly-Leitfaden für Hochverfügbarkeit](https://docs.wildfly.org/18/High_Availability_Guide.html).

## <a name="configuration-choice"></a>Konfigurationsauswahl

WildFly kann im Modus **Eigenständiger Server** gestartet werden. Eine eigenständige Serverinstanz ist ein unabhängiger Prozess, ähnlich wie eine Instanz von JBoss Application Server (AS) 3, 4, 5 oder 6. Eigenständige Instanzen können über die Startskripts „standalone.sh“ oder „standalone.bat“ gestartet werden. Bei mehr als einer eigenständigen Instanz liegt es in der Verantwortung des Benutzers, die Verwaltung mehrerer Server über die Server hinweg zu koordinieren.

Sie können die WildFly-Instanz auch mit einer alternativen Konfiguration starten, indem Sie die im Konfigurationsordner verfügbaren Konfigurationsdateien verwenden.

Nachfolgend finden Sie die Konfigurationsdateien des eigenständigen Servers:

- standalone.xml (Standard) – Diese Konfiguration ist die zum Starten der WildFly-Instanz verwendete Standarddatei. Sie enthält eine von Jakarta Web Profile zertifizierte Konfiguration mit den erforderlichen Technologien.
   
- standalone-ha.xml – Für Jakarta EE Web Profile 8 zertifizierte Konfiguration mit Hochverfügbarkeit (ausgerichtet auf Webanwendungen)
   
- standalone-full.xml – Für Jakarta EE Platform 8 zertifizierte Konfiguration, einschließlich aller erforderlichen Technologien für das Hosting von Jakarta EE-Anwendungen.

- standalone-full-ha.xml – Für Jakarta EE Platform 8 zertifizierte Konfiguration mit Hochverfügbarkeit für das Hosting von Jakarta EE-Anwendungen.

Verwenden Sie das Argument „--server-config“ mit der „server-config“-Datei, um Ihren eigenständigen WildFly-Server mit einer anderen bereitgestellten Konfiguration zu starten.

Verwenden Sie z. B. den folgenden Befehl, um die Jakarta EE Platform 8 mit Clusteringfunktionen zu verwenden:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Weitere Informationen zu den Konfigurationen finden Sie im [WildFly-Leitfaden für erste Schritte](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Hinweise zu Lizenzierung, Support und Abonnement

Das Azure CentOS 8-Image ist ein VM-Image mit nutzungsbasierter Zahlung (Pay-As-You-Go, PAYG) und erfordert vom Benutzer keine Lizenz. Wenn der virtuelle Computer zum ersten Mal gestartet wird, wird die Betriebssystemlizenz des virtuellen Computers automatisch aktiviert und ein Stundensatz berechnet. Dies erfolgt zusätzlich zu den VM-Stundensätzen für Linux von Microsoft. Klicken Sie auf [Virtuelle Linux-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux), um Details zu erhalten. WildFly kann kostenlos heruntergeladen und verwendet werden und erfordert kein Abonnement oder eine Lizenz von Red Hat.

## <a name="how-to-consume"></a>Nutzen

Sie können die Vorlage auf die folgenden drei Arten bereitstellen:

- PowerShell verwenden – Stellen Sie die Vorlage mithilfe der folgenden Befehle bereit: (Informationen zur Installation und Konfiguration von Azure PowerShell finden Sie unter [Azure PowerShell](/powershell/azure/)).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Azure CLI verwenden – Stellen Sie die Vorlage mithilfe der folgenden Befehle bereit: (Informationen zur Installation und Konfiguration der plattformübergreifenden Azure-Befehlszeilenschnittstelle finden Sie unter [Plattformübergreifende Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Azure-Portal verwenden – Stellen Sie die Vorlage bereit, indem Sie <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">hier</a> klicken, und melden Sie sich bei Ihrem Azure-Portal an.

## <a name="arm-template"></a>ARM-Vorlage

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 in CentOS 8 (eigenständige VM)</a> – Dies ist eine Schnellstartvorlage, die einen eigenständigen Knoten von WildFly 18.0.1.Final auf einer CentOS 8-VM in Ihrer Ressourcengruppe (RG) erstellt, die eine private IP-Adresse für die VM, das virtuelle Netzwerk und das Diagnosespeicherkonto enthält. Sie stellt auch eine Java-Beispielanwendung namens „JBoss-EAP on Azure“ in WildFly bereit.

## <a name="resource-links"></a>Ressourcenlinks

* Weitere Informationen zu [WildFly 18](https://docs.wildfly.org/18/)
* Weitere Informationen zu [Linux-Distributionen in Azure](../../linux/endorsed-distros.md)
* [Azure-Dokumentation für Java-Entwickler](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Produktionsumgebung finden Sie in den Azure ARM-Schnellstartvorlagen für Red Hat JBoss EAP:

Eigenständiger virtueller RHEL-Computer mit Beispielanwendung:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP unter RHEL (eigenständige VM)</a>

Gruppierte virtuelle RHEL-Computer mit Beispielanwendung:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP unter RHEL (gruppierte VMs)</a>

Gruppierte RHEL-VM-Skalierungsgruppe mit Beispielanwendung:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP unter RHEL (gruppierte VM-Skalierungsgruppe)</a>