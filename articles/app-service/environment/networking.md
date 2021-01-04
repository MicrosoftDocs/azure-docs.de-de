---
title: Netzwerk in der App Service-Umgebung
description: Details zum Netzwerk in der App Service-Umgebung.
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 61059c3e0f9737df6ace338f4252a338ea1f200c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663330"
---
# <a name="app-service-environment-networking"></a>Netzwerk in der App Service-Umgebung

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v3 (Vorschau) behandelt.
> 

Die App Service-Umgebung (ASE) ist eine Bereitstellung von Azure App Service für einen Mandanten, die Web-Apps, API-Apps und Funktions-Apps hostet. Wenn Sie eine ASE installieren, wählen Sie das Azure Virtual Network (VNet) aus, in dem sie bereitgestellt werden soll. Der gesamte ein- und ausgehende Datenverkehr der Anwendungen findet innerhalb des von Ihnen angegebenen VNets statt.  

Die ASEv3 verwendet zwei Subnetze.  Ein Subnetz wird für den privaten Endpunkt verwendet, der den eingehenden Datenverkehr verarbeitet. Dies könnte ein bereits vorhandenes oder ein neues Subnetz sein.  Das eingehende Subnetz kann für andere Zwecke als den privaten Endpunkt verwendet werden. Das ausgehende Subnetz kann nur für ausgehenden Datenverkehr aus der ASE verwendet werden. Im ausgehenden Subnetz der ASE kann nichts anderes verarbeitet werden.

## <a name="addresses"></a>Adressen 
Die ASE verfügt bei der Erstellung über die folgenden Adressen:

| Adresstyp | description |
|--------------|-------------|
| Adresse für eingehenden Datenverkehr | Die eingehende Adresse ist die Adresse des privaten Endpunkts, der von ihrer ASE verwendet wird. |
| Subnetz für ausgehenden Datenverkehr | Das ausgehende Subnetz ist auch das ASE-Subnetz. Während der Vorschauphase wird dieses Subnetz ausschließlich für ausgehenden Datenverkehr verwendet. |
| Windows-Adresse für ausgehenden Datenverkehr | Die Windows-Apps in dieser ASE verwenden diese Adresse standardmäßig, wenn sie ausgehende Aufrufe an das Internet vornehmen. |
| Linux-Adresse für ausgehenden Datenverkehr | Die Linux-Apps in dieser ASE verwenden diese Adresse standardmäßig, wenn sie ausgehende Aufrufe an das Internet vornehmen. |

Wenn Sie den privaten Endpunkt löschen, der von der ASE verwendet wird, können Sie die Apps in Ihrer ASE nicht mehr erreichen. Löschen Sie die private Azure DNS-Zone nicht, die mit Ihrer ASE verknüpft ist.  

Die ASE verwendet Adressen im ausgehenden Subnetz, um die von der ASE verwendete Infrastruktur zu unterstützen. Wenn Sie Ihre App Service-Pläne in Ihrer ASE hochskalieren, werden Sie mehr Adressen verwenden. Apps in der ASE verfügen über keine dedizierten Adressen im ausgehenden Subnetz. Die Adressen, die von einer App im ausgehenden Subnetz verwendet werden, ändern sich im Laufe der Zeit.

## <a name="ports"></a>Ports

Die ASE empfängt den Anwendungsdatenverkehr an den Ports 80 und 443.  Es gibt keine Anforderungen für ein- oder ausgehende Ports der ASE. 

## <a name="extra-configurations"></a>Zusätzliche Konfigurationen

Im Gegensatz zu ASEv2 können Sie mit ASEv3 Netzwerksicherheitsgruppen (NSGs) und Routingtabellen (UDRs) nach Ihrem Ermessen ohne Einschränkungen festlegen. Wenn Sie erzwingen möchten, tunneln Sie den gesamten ausgehenden Datenverkehr von Ihrer ASE zu einem NVA-Gerät. Sie können WAF-Geräte vor jeglichem eingehenden Datenverkehr an Ihre ASE platzieren. 

## <a name="dns"></a>DNS

Die Apps in Ihrer ASE verwenden das DNS, mit dem Ihr VNet konfiguriert ist. Wenn Sie möchten, dass einige Apps einen anderen DNS-Server verwenden, können Sie diesen mit den App-Einstellungen WEBSITE_DNS_SERVER und WEBSITE_DNS_ALT_SERVER manuell pro App festlegen. Die App-Einstellung WEBSITE_DNS_ALT_SERVER konfiguriert den sekundären DNS-Server. Der sekundäre DNS-Server wird nur verwendet, wenn keine Antwort vom primären DNS-Server vorliegt. 

## <a name="preview-limitation"></a>Einschränkungen der Vorschauversion

In ASEv3 sind ein paar Netzwerkfunktionen nicht verfügbar.  Zu den in ASEv3 nicht verfügbaren Funktionen gehören unter anderem:

• FTP • Remote Debuggen • Bereitstellung des externen Lastenausgleichs • Möglichkeit des Zugriffs auf eine private Containerregistrierung für Containerbereitstellungen • Möglichkeit zum Ausführen von Aufrufen an global gepeerte VNets • Möglichkeit zum Sichern/Wiederherstellen eines mit einem Dienstendpunkt oder einem privaten Endpunkt gesicherten Speicherkontos • Möglichkeit von App-Einstellungen keyvault-Verweisen auf mit einem Dienstendpunkt oder einem privaten Endpunkt gesicherte keyvault-Konten • Möglichkeit zur Verwendung von BYOS zu einem mit einem Dienstendpunkt oder einem privaten Endpunkt gesicherten Speicherkonto • Verwendung von Network Watcher oder NSG Flow für ausgehenden Datenverkehr
    
    

