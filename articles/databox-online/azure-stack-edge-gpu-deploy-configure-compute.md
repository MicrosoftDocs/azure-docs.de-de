---
title: Tutorial zum Filtern und Analysieren von Daten mit der Computerolle auf einem Azure Stack Edge Pro-Gerät mit GPU | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie die Computerolle für ein Azure Stack Edge Pro-Gerät mit GPU konfigurieren und verwenden, um Daten vor dem Senden an Azure zu transformieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/07/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: eb71db05a61a0e32f3f092f37a4da72bc04e581d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525754"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Tutorial: Konfigurieren der Computerolle auf einem Azure Stack Edge Pro-Gerät mit GPU

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In diesem Tutorial wird beschrieben, wie Sie auf Ihrem Azure Stack Edge Pro-Gerät eine Computerolle konfigurieren und einen Kubernetes-Cluster erstellen. 

Dieser Vorgang kann ca. 20 bis 30 Minuten dauern.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Abrufen der Kubernetes-Endpunkte

 
## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie eine Computerolle auf Ihrem Azure Stack Edge Pro-Gerät einrichten:

- Sie haben das Azure Stack Edge Pro-Gerät aktiviert, wie unter [Aktivieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) beschrieben.
- Stellen Sie sicher, dass Sie die Anweisungen unter [Aktivieren des Computenetzwerks](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) befolgt und diese Schritte ausgeführt haben:
    - Aktivieren einer Netzwerkschnittstelle für Compute
    - Zuweisen von IP-Adressen für Kubernetes-Knoten und externer Dienst-IP-Adressen für Kubernetes

## <a name="configure-compute"></a>Konfigurieren der Computeumgebung

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Abrufen der Kubernetes-Endpunkte

Um einen Client für den Zugriff auf den Kubernetes-Cluster zu konfigurieren, benötigen Sie den Kubernetes-Endpunkt. Führen Sie die folgenden Schritte aus, um den Kubernetes-API-Endpunkt über die lokale Benutzeroberfläche Ihres Azure Stack Edge Pro-Geräts abzurufen.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zur Seite **Geräte**.
2. Kopieren Sie unter **Geräteendpunkte** den Dienstendpunkt **Kubernetes-API**. Dieser Endpunkt ist eine Zeichenfolge im folgenden Format: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Seite „Gerät“ auf der lokalen Benutzeroberfläche](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Speichern Sie die Endpunktzeichenfolge. Sie werden diese Endpunktzeichenfolge später nutzen, wenn Sie einen Client für den Zugriff auf den Kubernetes-Cluster über kubectl konfigurieren.

4. Auf der lokalen Webbenutzeroberfläche können Sie folgende Aktionen ausführen:

    - Wechseln Sie zu „Kubernetes-API“, wählen Sie **Erweiterte Einstellungen** aus, und laden Sie eine erweiterte Konfigurationsdatei für Kubernetes herunter. 

        ![Seite „Gerät“ auf der lokalen Benutzeroberfläche 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Wenn Sie einen Schlüssel von Microsoft erhalten haben (was bei ausgewählten Benutzern der Fall sein kann), können Sie diese Konfigurationsdatei verwenden.

        ![Seite „Gerät“ auf der lokalen Benutzeroberfläche 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Sie können auch zum Endpunkt **Kubernetes-Dashboard** wechseln und eine `aseuser`-Konfigurationsdatei herunterladen. 
    
        ![Seite „Gerät“ auf der lokalen Benutzeroberfläche 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Sie können sich mit dieser Konfigurationsdatei beim Kubernetes-Dashboard anmelden oder Probleme in Ihrem Kubernetes-Cluster beheben. Weitere Informationen finden Sie unter [Zugreifen auf das Kubernetes-Dashboard](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Abrufen der Kubernetes-Endpunkte


Weitere Informationen zum Verwalten Ihres Azure Stack Edge Pro-Geräts finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwenden der lokalen Webbenutzeroberfläche zum Verwalten von Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
