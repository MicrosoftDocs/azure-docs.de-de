---
title: Includedatei
description: Includedatei
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116878"
---
## <a name="scenario"></a>Szenario

Um zu veranschaulichen, wie Sie ein VNET und Subnetze erstellen, wird in diesem Dokument das folgende Szenario verwendet:

![VNet-Szenario](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

In diesem Szenario erstellen Sie ein VNET namens **TestVNet** mit dem reservierten CIDR-Block  **192.168.0.0./16**. Das VNET enthält die folgenden Subnetze: 

* **FrontEnd**, mit **192.168.1.0/24** als CIDR-Block.
* **BackEnd**, mit **192.168.2.0/24** als CIDR-Block.

