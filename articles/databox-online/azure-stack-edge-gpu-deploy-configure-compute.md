---
title: Tutorial zum Filtern und Analysieren von Daten mit der Computerolle auf einem Azure Stack Edge Pro-Gerät mit GPU | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie die Computerolle für ein Azure Stack Edge Pro-Gerät mit GPU konfigurieren und verwenden, um Daten vor dem Senden an Azure zu transformieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 711da24b3edf08f4867109d0d70165955236c39a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184653"
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

Zur Konfiguration der Computerolle auf Ihrem Azure Stack Edge Pro-Gerät erstellen Sie im Azure-Portal eine IoT Hub-Ressource.

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Übersicht**. Wählen Sie im Bereich auf der rechten Seite auf der Kachel **Compute** die Option **Erste Schritte**.

    ![Erste Schritte mit Compute](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Wählen Sie auf der Kachel **Edgecomputing konfigurieren** die Option **Compute konfigurieren**.

    ![Konfigurieren der Computeumgebung](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Geben Sie auf dem Blatt **Edgecomputing konfigurieren** Folgendes ein:

   
    |Feld  |Wert  |
    |---------|---------|
    |IoT Hub     | Wählen Sie zwischen **Neu** und **Vorhanden**. <br> Standardmäßig wird ein Standard-Tarif (S1) verwendet, um eine IoT-Ressource zu erstellen. Wenn Sie eine IoT-Ressource im Free-Tarif verwenden möchten, können Sie diese erstellen und dann als vorhandene Ressource auswählen. <br> Die IoT Hub-Ressource nutzt in jedem Fall dasselbe Abonnement und dieselbe Ressourcengruppe wie die Azure Stack Edge-Ressource.     |
    |Name     |Geben Sie einen Namen für Ihre IoT Hub-Ressource ein.         |

    ![Erste Schritte mit Compute 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Klicken Sie auf **Erstellen**. Die Erstellung der IoT Hub-Ressource dauert einige Minuten. Nachdem die IoT Hub-Ressource erstellt wurde, wird die Kachel **Compute konfigurieren** aktualisiert, um die Computekonfiguration anzuzeigen. 

    ![Erste Schritte mit Compute 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Wählen Sie auf der Kachel **Compute konfigurieren** die Option **View Compute** (Compute anzeigen), um sich zu vergewissern, dass die Edgecomputing-Rolle konfiguriert wurde.
    
    ![Erste Schritte mit Compute 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Wird das Dialogfeld **Compute konfigurieren** geschlossen, bevor die IoT Hub-Instanz mit dem Azure Stack Edge Pro-Gerät verknüpft wurde, wird die IoT Hub-Instanz erstellt, aber nicht in der Computekonfiguration angezeigt. 
    
Wenn die Edge-Computerolle auf dem Edge-Gerät eingerichtet ist, werden zwei Geräte erstellt: ein IoT-Gerät und ein IoT Edge-Gerät. Beide Geräte können in der IoT Hub-Ressource angezeigt werden. Auf diesem IoT Edge-Gerät wird auch eine IoT Edge-Runtime ausgeführt. Derzeit ist für Ihr IoT Edge-Gerät nur die Linux-Plattform verfügbar.

Die Computekonfiguration kann 20-30 Minuten dauern, da im Hintergrund virtuelle Computer und Kubernetes-Cluster erstellt werden. 

Nachdem Sie die Computerolle erfolgreich im Azure-Portal konfiguriert haben, sind ein Kubernetes-Cluster und ein Standardbenutzer vorhanden, die dem IoT-Namespace (einem von Azure Stack Edge Pro kontrollierten Systemnamespace) zugeordnet sind. 

## <a name="get-kubernetes-endpoints"></a>Abrufen der Kubernetes-Endpunkte

Um einen Client für den Zugriff auf den Kubernetes-Cluster zu konfigurieren, benötigen Sie den Kubernetes-Endpunkt. Führen Sie die folgenden Schritte aus, um den Kubernetes-API-Endpunkt über die lokale Benutzeroberfläche Ihres Azure Stack Edge Pro-Geräts abzurufen.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zur Seite **Geräte**.
2. Kopieren Sie unter **Geräteendpunkte** den Dienstendpunkt **Kubernetes-API**. Dieser Endpunkt ist eine Zeichenfolge im folgenden Format: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Seite „Gerät“ auf der lokalen Benutzeroberfläche](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Speichern Sie die Endpunktzeichenfolge. Sie werden sie später nutzen, wenn Sie einen Client für den Zugriff auf den Cluster Kubernetes über kubectl konfigurieren.

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
