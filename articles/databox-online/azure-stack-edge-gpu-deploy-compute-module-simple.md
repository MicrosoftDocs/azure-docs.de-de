---
title: Verwenden eines IoT Edge-Moduls zur Bereitstellung von Computeworkloads in Azure Stack Edge Pro mit GPU | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Computeworkload mithilfe eines vorab erstellten IoT Edge-Moduls auf Ihrem Azure Stack Edge Pro-GPU-Gerät ausführen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 44265efd4d125bb2701742a490fc883c48f1877b
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550991"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Tutorial: Ausführen einer Computeworkload mit einem IoT Edge-Modul auf einem Azure Stack Edge Pro-GPU-Gerät

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In diesem Tutorial wird beschrieben, wie Sie eine Computeworkload mithilfe eines IoT Edge-Moduls auf Ihrem Azure Stack Edge Pro-GPU-Gerät ausführen. Nach dem Konfigurieren der Computeressource kann das Gerät Daten vor dem Senden an Azure transformieren.

Dieser Vorgang kann ca. 10 bis 15 Minuten dauern.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Hinzufügen von Freigaben
> * Hinzufügen eines Computemoduls
> * Überprüfen der Datentransformation und -übertragung

 
## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie eine Computerolle auf Ihrem Azure Stack Edge Pro-GPU-Gerät einrichten:

- Sie haben das Azure Stack Edge Pro-Gerät wie unter [Aktivieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) beschrieben aktiviert.
- Sie verfügen über ein IoT Edge-Modul, das Sie für Ihre Daten ausführen können. In diesem Tutorial haben wir ein Modul vom Typ `filemove2` verwendet, das Daten von der lokalen Edge-Freigabe auf Ihrem Gerät in eine Edge-Freigabe verschiebt, von der die Daten in das Azure Storage-Konto übertragen werden.


## <a name="configure-compute"></a>Konfigurieren der Computeumgebung

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Hinzufügen von Freigaben

Für die einfache Bereitstellung in diesem Tutorial benötigen Sie zwei Freigaben: eine Edgefreigabe und eine weitere lokale Edgefreigabe.

1. Fügen Sie wie folgt auf dem Gerät eine Edge-Freigabe hinzu:

    1. Wechseln Sie in Ihrer Azure Stack Edge-Ressource zu **Cloudspeichergateway > Freigaben**.
    2. Wählen Sie auf der Befehlsleiste **+ Freigabe hinzufügen** aus.
    3. Geben Sie auf dem Blatt **Freigabe hinzufügen** den Freigabenamen an, und wählen Sie den Freigabetyp aus.
    4. Aktivieren Sie zum Bereitstellen der Edgefreigabe das Kontrollkästchen für **Freigabe mit Edgecomputing verwenden**.
    5. Wählen Sie das **Speicherkonto**, den **Speicherdienst**, einen vorhandenen Benutzer und dann die Option **Erstellen** aus.

        ![Hinzufügen einer Edgefreigabe](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Um eine NFS-Freigabe in das Computenetzwerk einzubinden, muss es im gleichen Subnetz wie die virtuelle IP-Adresse von NFS konfiguriert werden. Ausführliche Informationen zum Konfigurieren des Computenetzwerk finden Sie unter [Aktivieren des Computenetzwerks auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    Die Edgefreigabe wird erstellt, und Sie erhalten eine Benachrichtigung zur erfolgreichen Erstellung. Die Freigabeliste wird unter Umständen aktualisiert. Sie müssen jedoch warten, bis die Erstellung der Freigabe abgeschlossen ist.

2. Wiederholen Sie zum Hinzufügen einer lokale Edge-Freigabe auf dem Gerät alle Schritte des vorherigen Schritts, und aktivieren Sie das Kontrollkästchen **Als lokale Edge-Freigabe konfigurieren**. Die Daten auf der lokalen Dateifreigabe verbleiben auf dem Gerät.

    ![Hinzufügen einer lokalen Edgefreigabe](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Wenn Sie eine lokale NFS-Freigabe erstellt haben, können Sie die folgende rsync-Befehlsoption verwenden, um Dateien auf die Freigabe zu kopieren:

    `rsync <source file path> < destination file path>`

    Weitere Informationen zum Befehl `rsync` finden Sie in der [Dokumentation zu Rsync](https://www.computerhope.com/unix/rsync.htm).
 
3. Navigieren Sie zu **Cloudspeichergateway > Freigaben**, um die aktualisierte Liste der Freigaben anzuzeigen.

    ![Aktualisierte Liste der Freigaben](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Hinzufügen eines Moduls

Sie können ein benutzerdefiniertes oder ein vorgefertigtes Modul hinzufügen. Das Gerät verfügt nicht über vorgefertigte oder benutzerdefinierte Module. Informationen zum Erstellen eines benutzerdefinierten Moduls finden Sie unter [Entwickeln eines C#-Moduls für Ihr Azure Stack Edge Pro-Gerät](azure-stack-edge-j-series-create-iot-edge-module.md).

In diesem Abschnitt fügen Sie dem IoT Edge-Gerät, das Sie unter [Entwickeln eines C#-Moduls für Ihr Azure Stack Edge Pro-Gerät](azure-stack-edge-j-series-create-iot-edge-module.md) erstellt haben, ein benutzerdefiniertes Modul hinzu. Dieses benutzerdefinierte Modul verwendet Dateien von einer lokalen Edgefreigabe auf dem Edgegerät und verschiebt sie auf eine Edgefreigabe (Cloudfreigabe) auf dem Gerät. Die Cloudfreigabe überträgt die Dateien dann per Pushvorgang an das Azure-Speicherkonto, das der Cloudfreigabe zugeordnet ist.

Führen Sie zum Hinzufügen eines Moduls die folgenden Schritte aus:

1. Navigieren Sie zu **IoT Edge > Module**. Wählen Sie auf der Befehlsleiste **+ Modul hinzufügen** aus. 

2. Geben Sie auf dem Blatt **Modul hinzufügen** die folgenden Werte ein:

    
    |Feld  |Wert  |
    |---------|---------|
    |Name     | Ein eindeutiger Name für das Modul. Dieses Modul ist ein Docker-Container, den Sie auf dem IoT Edge-Gerät bereitstellen können, das Ihrer Azure Stack Edge Pro-Instanz zugeordnet ist.        |
    |Image-URI     | Der Image-URI für das entsprechende Containerimage für das Modul.        |
    |Anmeldeinformationen erforderlich     | Wenn dieses Kontrollkästchen aktiviert ist, werden ein Benutzername und ein Kennwort verwendet, um Module mit einer übereinstimmenden URL abzurufen.        |
    |Eingabefreigabe     | Wählen Sie eine Eingabefreigabe aus. Die lokale Edgefreigabe ist in diesem Fall die Eingabefreigabe. Mit dem hier verwendeten Modul werden Dateien von der lokalen Edgefreigabe auf eine Edgefreigabe verschoben, von der sie in die Cloud hochgeladen werden.        |
    |Ausgabefreigabe     | Wählen Sie eine Ausgabefreigabe aus. Die Edgefreigabe ist in diesem Fall die Ausgabefreigabe.        |
    |Triggertyp     | Wählen Sie zwischen den Optionen **Datei** und **Zeitplan**. Es wird jeweils ein Dateitrigger ausgelöst, wenn ein Dateiereignis eintritt, z. B. das Schreiben einer Datei auf die Eingabefreigabe. Ein geplanter Trigger wird nach einem von Ihnen festgelegten Zeitplan ausgelöst.         |
    |Triggername     | Ein eindeutiger Name für Ihren Trigger.         |
    |Umgebungsvariablen| Optionale Informationen zum Definieren der Umgebung, in der Ihr Modul ausgeführt wird.   |

    ![Hinzufügen und Konfigurieren des Moduls](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Wählen Sie **Hinzufügen**. Das Modul wird hinzugefügt. Die Seite **IoT Edge > Module** wird aktualisiert, um anzugeben, dass das Modul bereitgestellt wurde. Der Laufzeitstatus des hinzugefügten Moduls sollte *Wird ausgeführt* lauten.

    ![Modul bereitgestellt](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Überprüfen der Datentransformation und -übertragung

Im letzten Schritt wird sichergestellt, dass das Modul wie erwartet ausgeführt wird und Daten verarbeitet. Der Laufzeitstatus des Moduls sollte für Ihr IoT Edge-Gerät auf der IoT Hub-Ressource „Wird ausgeführt“ lauten.

Überprüfen Sie wie folgt, ob das Modul erwartungsgemäß ausgeführt wird und Daten verarbeitet:


1. Stellen Sie im Datei-Explorer eine Verbindung mit den lokalen und anderen Edgefreigaben her, die Sie zuvor erstellt haben. Weitere Informationen finden Sie in den folgenden Schritten: 

    ![Herstellen einer Verbindung mit lokalen Edge-Freigaben und Edge-Cloudfreigaben](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Fügen Sie der lokalen Freigabe Daten hinzu.

    ![Datei auf lokale Edge-Freigabe kopiert](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Die Daten werden auf die Cloudfreigabe verschoben.

    ![Datei in Edge-Cloudfreigabe kopiert](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Anschließend werden die Daten per Pushvorgang von der Cloudfreigabe in das Speicherkonto übertragen. Sie können die Daten im Storage-Explorer oder in Azure Storage im Portal anzeigen.

    ![Überprüfen von Daten im Speicherkonto](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
Sie haben den Überprüfungsvorgang abgeschlossen.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Hinzufügen von Freigaben
> * Hinzufügen eines Computemoduls
> * Überprüfen der Datentransformation und -übertragung

Weitere Informationen zum Verwalten Ihres Azure Stack Edge Pro-Geräts finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwenden der lokalen Webbenutzeroberfläche zum Verwalten von Azure Stack Edge Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)
