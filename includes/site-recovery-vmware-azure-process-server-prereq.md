---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008475"
---
In diesem Artikel wird Folgendes vorausgesetzt:

1. Sie haben zwischen Ihrem lokalen Netzwerk und Azure Virtual Network bereits eine **Site-to-Site-VPN**- oder **ExpressRoute**-Verbindung eingerichtet.
2. Ihr Benutzerkonto verfügt über die erforderlichen Berechtigungen, um einen neuen virtuellen Computer in dem Azure-Abonnement zu erstellen, auf das das Failover der virtuellen Computer erfolgt ist.
3. Ihr Abonnement umfasst mindestens 8 verfügbare Kerne, um einen neuen virtuellen Prozessservercomputer zu starten.
4. Sie haben die **Passphrase des Konfigurationsservers** zur Hand.

> [!TIP]
> Vergewissern Sie sich, dass Sie über die Azure Virtual Network-Instanz, an die das Failover der virtuellen Computer erfolgt ist, eine Verbindung mit Port 443 des (lokal ausgeführten) Konfigurationsservers herstellen können.
