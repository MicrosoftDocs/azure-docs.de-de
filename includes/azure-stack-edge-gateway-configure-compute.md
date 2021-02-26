---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524928"
---
Zur Konfiguration der Computerolle auf Ihrem Azure Stack Edge Pro-Gerät erstellen Sie im Azure-Portal eine IoT Hub-Ressource.

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Übersicht**, und wählen Sie **IoT Edge** aus.

   ![Erste Schritte mit Compute](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. Wählen Sie unter **IoT Edge-Dienst aktivieren** die Option **Hinzufügen** aus.

   ![Konfigurieren der Computeumgebung](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Geben Sie auf dem Blatt **Edgecomputing konfigurieren** die folgenden Informationen ein:
   
    |Feld  |Wert  |
    |---------|---------|
    |Subscription     |Wählen Sie ein Abonnement für Ihre IoT Hub-Ressource aus. Sie können dasselbe Abonnement verwenden, das auch von der Azure Stack Edge-Ressource verwendet wird.         |
    |Resource group     |Wählen Sie eine Ressourcengruppe für Ihre IoT Hub-Ressource aus. Sie können dieselbe Ressourcengruppe verwenden, die auch von der Azure Stack Edge-Ressource verwendet wird.         |
    |IoT Hub     | Wählen Sie zwischen **Neu** und **Vorhanden**. <br> Standardmäßig wird ein Standard-Tarif (S1) verwendet, um eine IoT-Ressource zu erstellen. Wenn Sie eine IoT-Ressource im Free-Tarif verwenden möchten, können Sie diese erstellen und dann als vorhandene Ressource auswählen. <br> Die IoT Hub-Ressource nutzt in jedem Fall dasselbe Abonnement und dieselbe Ressourcengruppe wie die Azure Stack Edge-Ressource.     |
    |Name     |Übernehmen Sie den Standardnamen, oder geben Sie einen Namen für Ihre IoT Hub-Ressource ein.         |

   ![Erste Schritte mit Compute 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Wenn Sie die Einstellungen abgeschlossen haben, wählen Sie **Überprüfen + erstellen** aus. Überprüfen Sie die Einstellungen für Ihre IoT Hub-Ressource, und wählen Sie **Erstellen** aus.

   Die Ressourcenerstellung für eine IoT Hub-Ressource dauert einige Minuten. Nach der Erstellung der Ressource ist in der **Übersicht** angegeben, dass der IoT Edge-Dienst jetzt ausgeführt wird.

   ![Erste Schritte mit Compute 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Wenn Sie überprüfen möchten, ob die Rolle „Edgecomputing“ konfiguriert wurde, navigieren Sie zu **IoT Edge > Eigenschaften**.

   ![Erste Schritte mit Compute 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Wenn die Edge-Computerolle auf dem Edge-Gerät eingerichtet ist, werden zwei Geräte erstellt: ein IoT-Gerät und ein IoT Edge-Gerät. Beide Geräte können in der IoT Hub-Ressource angezeigt werden. Auf diesem IoT Edge-Gerät wird auch eine IoT Edge-Runtime ausgeführt. Derzeit ist für Ihr IoT Edge-Gerät nur die Linux-Plattform verfügbar.

Die Computekonfiguration kann 20 bis 30 Minuten dauern, da im Hintergrund virtuelle Computer und ein Kubernetes-Cluster erstellt werden.

Nachdem Sie die Computerolle erfolgreich im Azure-Portal konfiguriert haben, sind ein Kubernetes-Cluster und ein Standardbenutzer vorhanden, die dem IoT-Namespace (einem von Azure Stack Edge Pro kontrollierten Systemnamespace) zugeordnet sind.
