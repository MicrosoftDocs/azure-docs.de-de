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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170291"
---
## <a name="scenario"></a>Szenario
Um zu veranschaulichen, wie Sie NSGs erstellen, wird in diesem Dokument das folgende Szenario verwendet:

![VNet-Szenario](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

In diesem Szenario erstellen Sie wie folgt eine NSG für jedes Subnetz im virtuellen Netzwerk **TestVNet**: 

* **NSG-FrontEnd**. Die Front-End-NSG wird auf das Subnetz *FrontEnd* angewandt und enthält zwei Regeln:    
  * **rdp-rule**. Ermöglicht RDP-Datenverkehr in das Subnetz *FrontEnd*.
  * **web-rule**. Ermöglicht HTTP-Datenverkehr in das Subnetz *FrontEnd*.
* **NSG-BackEnd**. Die Back-End-NSG wird auf das Subnetz *BackEnd* angewandt und enthält zwei Regeln:    
  * **sql-rule**. SQL-Datenverkehr ist nur aus dem Subnetz *FrontEnd* zulässig.
  * **web-rule**. Verweigert den gesamten Internetdatenverkehr aus dem Subnetz *BackEnd*.

Durch die Kombination dieser Regeln wird ein DMZ-ähnliches Szenario erstellt, in dem das Back-End-Subnetz nur eingehenden Datenverkehr für SQL aus dem Front-End-Subnetz empfangen kann und keinen Zugriff auf das Internet hat, während das Front-End-Subnetz mit dem Internet kommunizieren und nur eingehende HTTP-Anforderungen empfangen kann.

