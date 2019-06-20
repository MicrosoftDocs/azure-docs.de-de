---
title: Tutorial zum Filtern und Analysieren von Daten mit Compute auf Azure Data Box Edge | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Computerolle in Data Box Edge konfigurieren und verwenden, um Daten vor dem Senden an Azure zu transformieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 7b3d725eb05c811d3fdd44516c1bde9a8dfbaaac
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924334"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Tutorial: Transformieren von Daten mit Azure Data Box Edge

In diesem Tutorial wird beschrieben, wie Sie eine Computerolle auf Ihrem Azure Data Box Edge-Gerät konfigurieren. Nach dem Konfigurieren der Computerolle kann Data Box Edge Daten vor dem Senden an Azure transformieren.

Dieser Vorgang kann ca. 10 bis 15 Minuten dauern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Hinzufügen von Freigaben
> * Hinzufügen eines Computemoduls
> * Überprüfen der Datentransformation und -übertragung

 
## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie eine Computerolle auf Ihrem Data Box Edge-Gerät einrichten:

- Das Data Box Edge-Gerät ist aktiviert, wie unter [Tutorial: Verbinden, Einrichten und Aktivieren von Azure Data Box Edge (Vorschau)](data-box-edge-deploy-connect-setup-activate.md) beschrieben.


## <a name="configure-compute"></a>Konfigurieren der Computeumgebung

Um die Computeumgebung auf Ihrer Data Box Edge-Instanz zu konfigurieren, erstellen Sie eine IoT Hub-Ressource.

1. Navigieren Sie im Azure-Portal für Ihre Data Box Edge-Ressource zu „Übersicht“. Wählen Sie im Bereich auf der rechten Seite auf der Kachel **Compute** die Option **Erste Schritte**.

    ![Erste Schritte mit Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Wählen Sie auf der Kachel **Edgecomputing konfigurieren** die Option **Compute konfigurieren**.
3. Geben Sie auf dem Blatt **Edgecomputing konfigurieren** Folgendes ein:

   
    |Feld  |Wert  |
    |---------|---------|
    |IoT Hub     | Wählen Sie zwischen **Neu** und **Vorhanden**. <br> Standardmäßig wird ein Standard-Tarif (S1) verwendet, um eine IoT-Ressource zu erstellen. Wenn Sie eine IoT-Ressource im Free-Tarif verwenden möchten, können Sie diese erstellen und dann als vorhandene Ressource auswählen. <br> Die IoT Hub-Ressource nutzt in jedem Fall dasselbe Abonnement und die Ressourcengruppe wie die Data Box Edge-Ressource.     |
    |NAME     |Geben Sie einen Namen für Ihre IoT Hub-Ressource ein.         |

    ![Erste Schritte mit Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Klicken Sie auf **Erstellen**. Die Erstellung der IoT Hub-Ressource dauert einige Minuten. Nachdem die IoT Hub-Ressource erstellt wurde, wird die Kachel **Compute konfigurieren** aktualisiert, um die Computekonfiguration anzuzeigen. Wählen Sie auf der Kachel **Compute konfigurieren** die Option **View Compute** (Compute anzeigen), um sich zu vergewissern, dass die Edgecomputing-Rolle konfiguriert wurde.
    
    ![Erste Schritte mit Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Wenn die Edge-Computerolle auf dem Edge-Gerät eingerichtet ist, werden zwei Geräte erstellt: ein IoT-Gerät und ein IoT Edge-Gerät. Beide Geräte können in der IoT Hub-Ressource angezeigt werden. Auf diesem IoT Edge-Gerät wird auch eine IoT Edge-Runtime ausgeführt. Derzeit ist für Ihr IoT Edge-Gerät nur die Linux-Plattform verfügbar.


## <a name="add-shares"></a>Hinzufügen von Freigaben

Für die einfache Bereitstellung in diesem Tutorial benötigen Sie zwei Freigaben: eine Edgefreigabe und eine weitere lokale Edgefreigabe.

1. Fügen Sie auf dem Gerät eine Edgefreigabe hinzu, indem Sie die folgenden Schritte ausführen:

    1. Navigieren Sie auf Ihrer Data Box Edge-Ressource zu **Edgecomputing > Erste Schritte**.
    2. Wählen Sie auf der Kachel **Freigabe(n) hinzufügen** die Option **Hinzufügen**.
    3. Geben Sie auf dem Blatt **Freigabe hinzufügen** den Freigabenamen an, und wählen Sie den Freigabetyp aus.
    4. Aktivieren Sie zum Bereitstellen der Edgefreigabe das Kontrollkästchen für **Freigabe mit Edgecomputing verwenden**.
    5. Wählen Sie das **Speicherkonto**, den **Speicherdienst**, einen vorhandenen Benutzer und dann die Option **Erstellen** aus.

        ![Hinzufügen einer Edgefreigabe](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Wenn Sie eine lokale NFS-Freigabe erstellt haben, können Sie die folgende rsync-Befehlsoption verwenden, um Dateien auf die Freigabe zu kopieren:

    `rsync <source file path> < destination file path>`

    Weitere Informationen zum rsync-Befehl finden Sie in der [Dokumentation zu rsync](https://www.computerhope.com/unix/rsync.htm).

    Die Edgefreigabe wird erstellt, und Sie erhalten eine Benachrichtigung zur erfolgreichen Erstellung. Die Freigabeliste wird unter Umständen aktualisiert. Sie müssen jedoch warten, bis die Erstellung der Freigabe abgeschlossen ist.

2. Fügen Sie auf dem Edgegerät eine lokale Edgefreigabe hinzu, indem Sie alle Schritte des vorherigen Schritts wiederholen und das Kontrollkästchen **Als lokale Edge-Freigabe konfigurieren** aktivieren. Die Daten auf der lokalen Dateifreigabe verbleiben auf dem Gerät.

    ![Hinzufügen einer lokalen Edgefreigabe](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Wählen Sie **Freigabe(n) hinzufügen**, um die aktualisierte Liste mit den Freigaben anzuzeigen.

    ![Aktualisierte Liste der Freigaben](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Hinzufügen eines Moduls

Sie können ein benutzerdefiniertes oder ein vorgefertigtes Modul hinzufügen. Auf diesem Edgegerät sind keine benutzerdefinierten Module vorhanden. Informationen zum Erstellen eines benutzerdefinierten Moduls finden Sie unter [Entwickeln eines C#-IoT Edge-Moduls zum Verschieben von Dateien in Data Box Edge (Vorschau)](data-box-edge-create-iot-edge-module.md).

In diesem Abschnitt fügen Sie dem IoT Edge-Gerät, das Sie unter [Entwickeln eines C#-IoT Edge-Moduls zum Verschieben von Dateien in Data Box Edge (Vorschau)](data-box-edge-create-iot-edge-module.md) erstellt haben, ein benutzerdefiniertes Modul hinzu. Dieses benutzerdefinierte Modul verwendet Dateien von einer lokalen Edgefreigabe auf dem Edgegerät und verschiebt sie auf eine Edgefreigabe (Cloudfreigabe) auf dem Gerät. Die Cloudfreigabe überträgt die Dateien dann per Pushvorgang an das Azure-Speicherkonto, das der Cloudfreigabe zugeordnet ist.

1. Navigieren Sie zu **Edgecomputing > Erste Schritte**. Wählen Sie auf der Kachel **Module hinzufügen** als Szenariotyp die Option **Einfach** aus. Wählen Sie **Hinzufügen**.
2. Geben Sie auf dem Blatt **Modul konfigurieren und hinzufügen** die folgenden Werte ein:

    
    |Feld  |Wert  |
    |---------|---------|
    |NAME     | Ein eindeutiger Name für das Modul. Dieses Modul ist ein Docker-Container, den Sie auf dem IoT Edge-Gerät bereitstellen können, das Ihrer Data Box Edge-Instanz zugeordnet ist.        |
    |Image-URI     | Der Image-URI für das entsprechende Containerimage für das Modul.        |
    |Anmeldeinformationen erforderlich     | Wenn dieses Kontrollkästchen aktiviert ist, werden ein Benutzername und ein Kennwort verwendet, um Module mit einer übereinstimmenden URL abzurufen.        |
    |Eingabefreigabe     | Wählen Sie eine Eingabefreigabe aus. Die lokale Edgefreigabe ist in diesem Fall die Eingabefreigabe. Mit dem hier verwendeten Modul werden Dateien von der lokalen Edgefreigabe auf eine Edgefreigabe verschoben, von der sie in die Cloud hochgeladen werden.        |
    |Ausgabefreigabe     | Wählen Sie eine Ausgabefreigabe aus. Die Edgefreigabe ist in diesem Fall die Ausgabefreigabe.        |
    |Triggertyp     | Wählen Sie zwischen den Optionen **Datei** und **Zeitplan**. Es wird jeweils ein Dateitrigger ausgelöst, wenn ein Dateiereignis eintritt, z. B. das Schreiben einer Datei auf die Eingabefreigabe. Ein geplanter Trigger wird nach einem von Ihnen festgelegten Zeitplan ausgelöst.         |
    |Triggername     | Ein eindeutiger Name für Ihren Trigger.         |
    |Umgebungsvariablen| Optionale Informationen zum Definieren der Umgebung, in der Ihr Modul ausgeführt wird.   |

    ![Hinzufügen und Konfigurieren des Moduls](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Wählen Sie **Hinzufügen**. Das Modul wird hinzugefügt. Die Kachel **Modul hinzufügen** wird aktualisiert, um anzugeben, dass das Modul bereitgestellt wurde. 

    ![Modul bereitgestellt](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Überprüfen der Datentransformation und -übertragung

Im letzten Schritt wird sichergestellt, dass das Modul verbunden ist und wie erwartet ausgeführt wird. Der Laufzeitstatus des Moduls sollte für Ihr IoT Edge-Gerät auf der IoT Hub-Ressource „Wird ausgeführt“ lauten.

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob das Modul ausgeführt wird:

1. Wählen Sie die Kachel **Modul hinzufügen** aus. Das Blatt **Module** wird geöffnet. Identifizieren Sie in der Liste mit den Modulen das von Ihnen bereitgestellte Modul. Der Laufzeitstatus des hinzugefügten Moduls sollte *Wird ausgeführt* lauten.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  Stellen Sie im Datei-Explorer eine Verbindung mit den lokalen und anderen Edgefreigaben her, die Sie zuvor erstellt haben.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Fügen Sie der lokalen Freigabe Daten hinzu.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Die Daten werden auf die Cloudfreigabe verschoben.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Anschließend werden die Daten per Pushvorgang von der Cloudfreigabe in das Speicherkonto übertragen. Wechseln Sie zum Speicher-Explorer, um die Daten anzuzeigen.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Sie haben den Überprüfungsvorgang abgeschlossen.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Hinzufügen von Freigaben
> * Hinzufügen eines Computemoduls
> * Überprüfen der Datentransformation und -übertragung

Weitere Informationen zum Verwalten Ihres Data Box Edge-Geräts finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwenden der lokalen Webbenutzeroberfläche zum Verwalten einer Data Box Edge-Instanz](data-box-edge-manage-access-power-connectivity-mode.md)
