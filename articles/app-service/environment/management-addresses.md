---
title: App Service-Umgebung Management-Adressen – Azure
description: Listet die Management-Adressen auf, die verwendet werden, um eine App Service-Umgebung zu befehligen
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60766050"
---
# <a name="app-service-environment-management-addresses"></a>App Service-Umgebung Management-Adressen

Die App Service-Umgebung (ASE) ist eine Bereitstellung von Azure App Service für einen Mandanten, die in Ihrer Azure Virtual Network-Instanz (VNET) ausgeführt wird.  Die ASE wird zwar in Ihrem VNET ausgeführt, muss aber von mehreren dedizierten IP-Adressen aus zugänglich sein, über die Azure App Service den Dienst verwaltet.  Bei einer ASE durchläuft der Verwaltungsdatenverkehr das benutzergesteuerte Netzwerk. Wenn dieser Datenverkehr blockiert oder falsch weitergeleitet wird, wird die ASE angehalten. Weitere Informationen zu den Netzwerkabhängigkeiten der App Service-Umgebung finden Sie unter [Überlegungen zum Netzwerkbetrieb in der App Service-Umgebung][networking]. Allgemeine Informationen zur ASE finden Sie unter [Einführung in die App Service-Umgebung][intro].

Alle ASEs besitzen eine öffentliche VIP, an der Verwaltungsdatenverkehr eingeht. Der eingehende Verwaltungsdatenverkehr von diesen Adressen geht an den Ports 454 und 455 der öffentlichen VIP Ihrer ASE ein. Dieses Dokument listet die App Service-Quelladressen für den Verwaltungsdatenverkehr zur ASE auf. Diese Adressen sind auch im IP-Diensttag AppServiceManagement enthalten.

Die unten aufgeführten Adressen können in einer Routingtabelle konfiguriert werden, um Probleme durch asymmetrisches Routing des Verwaltungsdatenverkehrs zu vermeiden. Die Routen verarbeiten den Datenverkehr auf IP-Ebene und sind unabhängig von der Richtung des Datenverkehrs oder der Tatsache, dass der Datenverkehr Teil einer TCP-Antwortnachricht ist. Wenn die Antwortadresse für eine TCP-Anforderung von der Adresse, an die sie gesendet wurde ist, abweicht, liegt ein Problem durch asymmetrisches Routing vor. Um Probleme durch asymmetrisches Routing des ASE-Verwaltungsdatenverkehrs zu vermeiden, müssen Sie sicherstellen, dass Antworten von derselben Adresse aus gesendet werden, an die die Anforderungen gesendet wurden. Ausführliche Informationen zum Konfigurieren Ihrer ASE für den Betrieb in einer Umgebung, in der ausgehender Datenverkehr lokal gesendet wird, finden Sie unter [Konfigurieren Ihrer App Service-Umgebung mit erzwungenem Tunneling][forcedtunnel]

## <a name="list-of-management-addresses"></a>Liste der Verwaltungsadressen ##

| Region | Adressen |
|--------|-----------|
| Alle öffentlichen Regionen | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfigurieren einer Netzwerksicherheitsgruppe

Mit Netzwerksicherheitsgruppen müssen Sie sich nicht um die einzelnen Adressen kümmern oder eine eigene Konfiguration verwalten. Das IP-Diensttag AppServiceManagement wird stets bei allen Adressen auf dem neuesten Stand gehalten. Um dieses IP-Diensttag in Ihrer NSG zu verwenden, wechseln Sie zum Portal, öffnen Sie die Benutzeroberfläche für Netzwerksicherheitsgruppen, und wählen Sie „Eingangssicherheitsregeln“ aus. Wenn bereits eine Regel für den eingehenden Verwaltungsdatenverkehr vorhanden ist, bearbeiten Sie sie. Wenn diese NSG nicht mit Ihrer ASE erstellt wurde oder neu ist, wählen Sie **Hinzufügen** aus. Wählen Sie in der Dropdownliste „Quelle“ die Option **Diensttag** aus.  Wählen Sie für das Diensttag „Quelle“ die Option **AppServiceManagement** aus. Legen Sie die Quellportbereiche auf \*, das Ziel auf **Alle**, die Zielportbereiche auf **454–455**, das Protokoll auf **TCP** und die Aktion auf **Zulassen** fest. Wenn Sie die Regel erstellen, müssen Sie die Priorität festlegen. 

![Erstellen einer NSG mit dem Diensttag][1]

## <a name="configuring-a-route-table"></a>Erstellen einer Routingtabelle

Die Verwaltungsadressen können in einer Routingtabelle mit dem nächsten Hop „Internet“ platziert werden, um sicherzustellen, dass sämtlicher eingehender Verwaltungsdatenverkehr über denselben Pfad zurückgeleitet werden kann. Diese Routen sind beim Konfigurieren der Tunnelerzwingung erforderlich. Zum Erstellen der Routingtabelle können Sie das Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden.  Die Befehle zum Erstellen einer Routingtabelle mithilfe der Azure-Befehlszeilenschnittstelle über eine PowerShell-Eingabeaufforderung sind unten aufgeführt. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

Nach dem Erstellen Ihrer Routingtabelle müssen Sie sie in Ihrem ASE-Subnetz festlegen.  

## <a name="get-your-management-addresses-from-api"></a>Abrufen der Verwaltungsadressen über eine API ##

Sie können die Verwaltungsadressen, die zu Ihrer App Service-Umgebung passen, mit dem folgenden API-Aufruf auflisten.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Die API gibt ein JSON-Dokument zurück, das alle eingehenden IP-Adressen für Ihre App Service-Umgebung enthält. Die Adressliste enthält die Verwaltungsadressen, die die virtuelle IP Ihrer App Service-Umgebung und der Subnetz-Adressbereich der App Service-Umgebung selbst verwendet haben.  

Verwenden Sie die folgenden Befehle zum Aufrufen der API mit [armclient](https://github.com/projectkudu/ARMClient), setzen Sie jedoch Ihre Abonnement-ID, Ihre Ressourcengruppe und den Namen Ihrer App Service-Umgebung ein.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
