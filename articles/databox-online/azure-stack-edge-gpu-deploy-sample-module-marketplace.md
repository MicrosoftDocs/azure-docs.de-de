---
title: Bereitstellen des GPU-Moduls auf Ihrem Microsoft Azure Stack Edge Pro-Gerät aus dem Azure Marketplace| Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie ein GPU-fähiges IoT Edge-Modul auf Ihrem Azure Stack Edge Pro-GPU-Gerät bereitstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/28/2021
ms.author: alkohli
ms.openlocfilehash: a2c46e8a7cae9ddba9606abf75ac022e804fde9c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062498"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Bereitstellen eines GPU-fähigen IoT-Moduls aus dem Azure Marketplace auf einem Azure Stack Edge Pro-GPU-Gerät

In diesem Artikel wird beschrieben, wie Sie ein GPU-fähiges (Graphics Processing Unit) IoT Edge-Modul aus dem Azure Marketplace auf Ihrem Azure Stack Edge Pro-Gerät bereitstellen. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:
  - Bereiten Sie Azure Stack Edge Pro auf das Ausführen eines GPU-Moduls vor.
  - Laden Sie das GPU-fähige IoT-Modul aus Azure Marketplace herunter, und stellen Sie es bereit.
  - Überwachen Sie die Modulausgabe.

## <a name="about-sample-module"></a>Informationen zum Beispielmodul

Das GPU-Beispielmodul in diesem Artikel enthält PyTorch und TensorFlow zum Vergleichen von Beispielcode für CPU und GPU.

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie über Folgendes verfügen:

- Sie haben Zugriff auf ein GPU-fähiges Azure Stack Edge-Gerät mit einem Knoten. Dieses Gerät wird mit einer Ressource in Azure aktiviert. 
- Sie haben Compute auf diesem Gerät konfiguriert. Führen Sie die Schritte im [Tutorial: Konfigurieren von Compute auf einem Azure Stack Edge-Gerät](azure-stack-edge-gpu-deploy-configure-compute.md) aus.
- Die folgenden Entwicklungsressourcen auf einem Windows-Client:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)   


## <a name="get-module-from-azure-marketplace"></a>Abrufen des Moduls aus Azure Marketplace

1. Durchsuchen Sie alle [Apps in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Apps in Azure Marketplace durchsuchen](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Suchen Sie nach **Getting Started with GPUs** (Erste Schritte mit GPUs).

    ![Nach GPU-Beispielmodul suchen](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Wählen Sie **Jetzt holen** aus.

    ![Beispielmodul holen](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Wählen Sie **Weiter** aus, um die Nutzungsbedingungen und die Datenschutzrichtlinie des Anbieters zu bestätigen. 

    ![Beispielmodul abrufen 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Wählen Sie das Abonnement aus, mit dem Sie Ihr Azure Stack Edge Pro-Gerät bereitgestellt haben.

    ![Auswählen des Abonnements](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Geben Sie den Namen des IoT Hub-Diensts ein, den Sie bei der Konfiguration Ihres Azure Stack Edge Pro-Geräts erstellt haben. Um den Namen dieses IoT Hub-Diensts zu finden, wechseln Sie zur Azure Stack Edge-Ressource, die Ihrem Gerät im Azure-Portal zugeordnet ist. 

    1. Wechseln Sie im linken Bereich in den Menüoptionen zu **Edge-Dienste > IoT Edge**. 

        ![Computekonfiguration anzeigen](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Wechseln Sie zu **Eigenschaften**. 

        1. Notieren Sie sich den IoT-Hub-Dienst, der erstellt wurde, als Sie Compute auf Ihrem Azure Stack Edge Pro-Gerät konfiguriert haben.
        2. Notieren Sie sich den Namen des IoT Edge-Geräts, das beim Konfigurieren von Compute erstellt wurde. Dieser Name wird im nachfolgenden Schritt verwendet.

        ![Konfiguration für Edgecomputing](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Wählen Sie **Für Gerät bereitstellen** aus.

11. Geben Sie den Namen des IoT Edge-Geräts ein, oder wählen Sie **Gerät suchen** aus, um die mit dem Hub registrierten Geräte zu durchsuchen.

    ![Gerät suchen](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Wählen Sie **Erstellen** aus, um den Standardprozess zum Konfigurieren eines Bereitstellungsmanifests fortzusetzen. Dazu gehört auch das Hinzufügen weiterer Module bei Bedarf. Details für das neue Modul, z.B. Image-URI, Erstellungsoptionen und gewünschte Eigenschaften, sind vordefiniert, können aber geändert werden.

    ![Klicken auf „Erstellen“](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Vergewissern Sie sich, dass das Modul in Ihrem IoT Hub im Azure-Portal bereitgestellt wird. Wählen Sie Ihr Gerät und **Module festlegen** aus. Dann sollte das Modul im Abschnitt **IoT Edge-Module** aufgeführt werden.

    ![„Erstellen“ auswählen 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Überwachen des Moduls  

1. Führen Sie in der Befehlspalette von VS Code den Befehl **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub auswählen) aus.

2. Wählen Sie das Abonnement und den IoT-Hub aus, die das zu konfigurierende IoT Edge-Gerät enthalten. Wählen Sie in diesem Fall das Abonnement aus, mit dem das Azure Stack Edge Pro-Gerät bereitgestellt wurde, und dann das für Ihr Azure Stack Edge Pro-Gerät erstellte IoT Edge-Gerät. Dies erfolgt, wenn Sie Compute über das Azure-Portal in den vorherigen Schritten konfigurieren.

3. Klappen Sie im Visual Studio Code-Explorer den Abschnitt „Azure IoT Hub“ auf. Unter **Geräte** sollte das IoT Edge-Gerät angezeigt werden, das Ihrem Azure Stack Edge Pro-Gerät entspricht. 

    1. Wählen Sie dieses Gerät aus, klicken Sie mit der rechten Maustaste, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus.
  
        ![Starten der Überwachung](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Wechseln Sie zu **Geräte > Module**. Hier sollte angezeigt werden, dass Ihr **GPU-Modul** ausgeführt wird.

    3. Im VS Code-Terminal sollten die IoT Hub-Ereignisse auch als Überwachungsausgabe für Ihr Azure Stack Edge Pro-Gerät angezeigt werden.

        ![Überwachungsausgabe](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Wie Sie sehen, ist die Zeit, die für die Ausführung derselben Gruppe von Vorgängen (5.000 Iterationen von Formtransformationen) von der GPU benötigt wird, wesentlich kürzer als bei der CPU.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zum [Konfigurieren der GPU für die Verwendung eines Moduls](azure-stack-edge-j-series-configure-gpu-modules.md).
