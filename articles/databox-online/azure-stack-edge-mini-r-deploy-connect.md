---
title: Tutorial zum Herstellen einer Verbindung mit Azure Stack Edge Mini R im Azure-Portal
description: Hier erfahren Sie, wie Sie über die lokale Webbenutzeroberfläche eine Verbindung mit Ihrem Azure Stack Edge Mini R-Gerät herstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464239"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Tutorial: Herstellen einer Verbindung mit Azure Stack Edge Mini R

In diesem Tutorial erfahren Sie, wie Sie über die lokale Webbenutzeroberfläche eine Verbindung mit Ihrem Azure Stack Edge Mini R-Gerät herstellen.

Der Verbindungsvorgang dauert ca. 5 Minuten.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Herstellen einer Verbindung mit einem physischen Gerät



## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie Folgendes, bevor Sie ein Azure Stack Edge-Gerät konfigurieren und einrichten:

* Sie haben Ihr physisches Gerät gemäß der Anleitung unter [Tutorial: Installieren von Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md) installiert.


## <a name="connect-to-the-local-web-ui-setup"></a>Herstellen einer Verbindung mit der lokalen Webbenutzeroberfläche

1. Konfigurieren Sie den Ethernet-Adapter auf Ihrem Computer, um das Azure Stack Edge Pro-Gerät mit der statischen IP-Adresse 192.168.100.5 und dem Subnetz 255.255.255.0 zu verbinden.

2. Verbinden Sie den Computer mit PORT 1 auf Ihrem Gerät. Wenn Sie den Computer direkt (ohne Switch) mit dem Gerät verbinden, verwenden Sie ein Kreuzkabel oder einen USB-Ethernet-Adapter. Verwenden Sie die folgende Abbildung, um PORT 1 auf Ihrem Gerät zu identifizieren.

    ![Verkabelung für WLAN](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Herstellen einer Verbindung mit einem physischen Gerät


Informationen zum Konfigurieren der Netzwerkeinstellungen auf Ihrem Gerät finden Sie hier:

> [!div class="nextstepaction"]
> [Konfigurieren des Netzwerks](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
