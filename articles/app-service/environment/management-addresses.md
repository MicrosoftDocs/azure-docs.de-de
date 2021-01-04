---
title: Verwaltungsadressen
description: Erfahren Sie, wie Sie Verwaltungsadressen zum Steuern einer App Service-Umgebung finden. Konfigurieren Sie diese in einer Routingtabelle, um asymmetrische Routingprobleme zu vermeiden.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/20/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 80d71293467582de5d62176075619e78d9fd5b5f
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511830"
---
# <a name="app-service-environment-management-addresses"></a>App Service-Umgebung Management-Adressen

Die App Service-Umgebung (ASE) ist eine Bereitstellung von Azure App Service für einen Mandanten, die in Ihrer Azure Virtual Network-Instanz (VNET) ausgeführt wird.  Die ASE wird zwar in Ihrem VNET ausgeführt, muss aber von mehreren dedizierten IP-Adressen aus zugänglich sein, über die Azure App Service den Dienst verwaltet.  Bei einer ASE durchläuft der Verwaltungsdatenverkehr das benutzergesteuerte Netzwerk. Wenn dieser Datenverkehr blockiert oder falsch weitergeleitet wird, wird die ASE angehalten. Weitere Informationen zu den Netzwerkabhängigkeiten der App Service-Umgebung finden Sie unter [Überlegungen zum Netzwerkbetrieb in der App Service-Umgebung][networking]. Allgemeine Informationen zur ASE finden Sie unter [Einführung in die App Service-Umgebung][intro].

Alle ASEs besitzen eine öffentliche VIP, an der Verwaltungsdatenverkehr eingeht. Der eingehende Verwaltungsdatenverkehr von diesen Adressen geht an den Ports 454 und 455 der öffentlichen VIP Ihrer ASE ein. Dieses Dokument listet die App Service-Quelladressen für den Verwaltungsdatenverkehr zur ASE auf. Diese Adressen sind auch im IP-Diensttag AppServiceManagement enthalten.

Die unten aufgeführten Adressen können in einer Routingtabelle konfiguriert werden, um Probleme durch asymmetrisches Routing des Verwaltungsdatenverkehrs zu vermeiden. Die Routen verarbeiten den Datenverkehr auf IP-Ebene und sind unabhängig von der Richtung des Datenverkehrs oder der Tatsache, dass der Datenverkehr Teil einer TCP-Antwortnachricht ist. Wenn die Antwortadresse für eine TCP-Anforderung von der Adresse, an die sie gesendet wurde ist, abweicht, liegt ein Problem durch asymmetrisches Routing vor. Um Probleme durch asymmetrisches Routing des ASE-Verwaltungsdatenverkehrs zu vermeiden, müssen Sie sicherstellen, dass Antworten von derselben Adresse aus gesendet werden, an die die Anforderungen gesendet wurden. Ausführliche Informationen zum Konfigurieren Ihrer ASE für den Betrieb in einer Umgebung, in der ausgehender Datenverkehr lokal gesendet wird, finden Sie unter [Konfigurieren Ihrer App Service-Umgebung mit erzwungenem Tunneling][forcedtunnel].

## <a name="list-of-management-addresses"></a>Liste der Verwaltungsadressen ##

| Region | Adressen |
|--------|-----------|
| Alle öffentlichen Regionen | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.203.64, 191.236.154.88, 52.181.183.11 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |

## <a name="configuring-a-network-security-group"></a>Konfigurieren einer Netzwerksicherheitsgruppe

Mit Netzwerksicherheitsgruppen müssen Sie sich nicht um die einzelnen Adressen kümmern oder eine eigene Konfiguration verwalten. Das IP-Diensttag AppServiceManagement wird stets bei allen Adressen auf dem neuesten Stand gehalten. Um dieses IP-Diensttag in Ihrer NSG zu verwenden, wechseln Sie zum Portal, öffnen Sie die Benutzeroberfläche für Netzwerksicherheitsgruppen, und wählen Sie „Eingangssicherheitsregeln“ aus. Wenn bereits eine Regel für den eingehenden Verwaltungsdatenverkehr vorhanden ist, bearbeiten Sie sie. Wenn diese NSG nicht mit Ihrer ASE erstellt wurde oder neu ist, wählen Sie **Hinzufügen** aus. Wählen Sie in der Dropdownliste „Quelle“ die Option **Diensttag** aus.  Wählen Sie für das Diensttag „Quelle“ die Option **AppServiceManagement** aus. Legen Sie die Quellportbereiche auf \*, das Ziel auf **Alle**, die Zielportbereiche auf **454–455**, das Protokoll auf **TCP** und die Aktion auf **Zulassen** fest. Wenn Sie die Regel erstellen, müssen Sie die Priorität festlegen. 

![Erstellen einer NSG mit dem Diensttag][1]

## <a name="configuring-a-route-table"></a>Erstellen einer Routingtabelle

Die Verwaltungsadressen können in einer Routingtabelle mit dem nächsten Hop „Internet“ platziert werden, um sicherzustellen, dass sämtlicher eingehender Verwaltungsdatenverkehr über denselben Pfad zurückgeleitet werden kann. Diese Routen sind beim Konfigurieren der Tunnelerzwingung erforderlich. Zum Erstellen der Routingtabelle können Sie das Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden.  Die Befehle zum Erstellen einer Routingtabelle mithilfe der Azure-Befehlszeilenschnittstelle über eine PowerShell-Eingabeaufforderung sind unten aufgeführt. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.203.64", "191.236.154.88", "52.181.183.11"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Nach dem Erstellen Ihrer Routingtabelle müssen Sie sie in Ihrem ASE-Subnetz festlegen.  

## <a name="get-your-management-addresses-from-api"></a>Abrufen der Verwaltungsadressen über eine API ##

Sie können die Verwaltungsadressen, die zu Ihrer App Service-Umgebung passen, mit dem folgenden API-Aufruf auflisten.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

Die API gibt ein JSON-Dokument zurück, das alle eingehenden IP-Adressen für Ihre App Service-Umgebung enthält. Die Adressliste enthält die Verwaltungsadressen, die die virtuelle IP Ihrer App Service-Umgebung und der Subnetz-Adressbereich der App Service-Umgebung selbst verwendet haben.  

Verwenden Sie die folgenden Befehle zum Aufrufen der API mit [armclient](https://github.com/projectkudu/ARMClient), setzen Sie jedoch Ihre Abonnement-ID, Ihre Ressourcengruppe und den Namen Ihrer App Service-Umgebung ein.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
