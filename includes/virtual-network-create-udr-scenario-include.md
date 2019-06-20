---
title: include file
description: include file
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178020"
---
## <a name="scenario"></a>Szenario
Um zu veranschaulichen, wie Sie UDRs erstellen, wird in diesem Dokument das folgende Szenario verwendet:

![BILDBESCHREIBUNG](./media/virtual-network-create-udr-scenario-include/figure1.png)

In diesem Szenario erstellen Sie wie folgt eine benutzerdefinierte Route für das *Front-End-Subnetz* und eine weitere für das *Back-End-Subnetz*: 

* **UDR-FrontEnd**. Die Front-End-UDR wird auf das Subnetz *FrontEnd* angewandt und enthält eine Route:    
  * **RouteToBackend**. Diese Route sendet den gesamten an das Back-End-Subnetz gerichteten Datenverkehr an den virtuellen Computer **FW1**.
* **UDR-BackEnd**. Die Back-End-UDR wird auf das Subnetz *BackEnd* angewandt und enthält eine Route:    
  * **RouteToFrontend**. Diese Route sendet den gesamten an das Front-End-Subnetz gerichteten Datenverkehr an den virtuellen Computer **FW1**.

Durch die Kombination dieser Routen wird sichergestellt, dass der gesamte Datenverkehr von einem Subnetz zu einem anderen an den virtuellen Computer **FW1** weitergeleitet wird, der als virtuelles Gerät fungiert. Sie müssen auch die IP-Weiterleitung für den virtuellen Computer **FW1** aktivieren, damit er den an andere virtuelle Computer gerichteten Datenverkehr empfangen kann.

