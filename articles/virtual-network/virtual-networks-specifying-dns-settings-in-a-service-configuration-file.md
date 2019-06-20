---
title: Angeben von DNS-Einstellungen in einer Dienstkonfigurationsdatei | Microsoft Docs
description: Angeben benutzerdefinierter DNS-Einstellungen über die Dienstkonfigurationsdatei für ein virtuelles Netzwerk
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60232739"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Angeben von DNS-Einstellungen in einer Dienstkonfigurationsdatei
## <a name="dns-elements"></a>DNS-Elemente
Eine Dienstkonfigurationsdatei kann ein DnsServers-Element mit einer Liste von IPv4-Adressen für die DNS-Server (Domain Name System) enthalten, die der Dienst verwendet. Einstellungen in der Dienstkonfigurationsdatei überschreiben die Einstellungen in der Netzwerkkonfigurationsdatei. Weitere Informationen finden Sie unter [Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration-Element**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Das **name**-Attribut im **DnsServer**-Element wird nur als Verweisname verwendet. Es stellt nicht den Hostnamen für den DNS-Server dar. Jeder **DnsServer** -Attributwert muss im gesamten Microsoft Azure-Abonnement eindeutig sein.
> 
> 

## <a name="see-also"></a>Siehe auch
[Azure-Dienstkonfigurationsschema (CSCFG)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Konfigurationsschema für virtuelle Azure-Netzwerke](https://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](https://go.microsoft.com/fwlink/?LinkId=248094)

[Einstellungen für virtuelle Netzwerke im Verwaltungsportal](https://go.microsoft.com/fwlink/?LinkId=248092)

