---
title: Tutorial zum Filtern und Analysieren von Daten für erweiterte Bereitstellung mit Compute auf Azure Data Box Edge | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Computerolle in Data Box Edge konfigurieren und verwenden, um Daten vor dem Senden an Azure für einen erweiterten Bereitstellungsablauf zu transformieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950728"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Tutorial: Transformieren von Daten mit Azure Data Box Edge für erweiterten Bereitstellungsablauf

In diesem Tutorial wird beschrieben, wie Sie eine Computerolle für einen erweiterten Bereitstellungsablauf auf Ihrem Azure Data Box Edge-Gerät konfigurieren. Nach dem Konfigurieren der Computerolle kann Data Box Edge Daten vor dem Senden an Azure transformieren.

Compute kann für einfachen oder erweiterten Bereitstellungsablauf auf Ihrem Gerät konfiguriert werden.

|                  | Einfache Bereitstellung                                | Erweiterte Bereitstellung                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Vorgesehen für     | IT-Administratoren                                | Entwickler                            |
| Type             | Verwenden des Data Box Edge-Diensts zum Bereitstellen von Modulen      | Verwenden des IoT Hub-Diensts zum Bereitstellen von Modulen |
| Bereitgestellte Module | Single                                           | Verkettet oder mehrere Module           |


Dieser Vorgang kann ca. 20 bis 30 Minuten dauern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Hinzufügen von Freigaben
> * Hinzufügen eines Triggers
> * Hinzufügen eines Computemoduls
> * Überprüfen der Datentransformation und -übertragung

 
## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie eine Computerolle auf Ihrem Data Box Edge-Gerät einrichten:

- Das Data Box Edge-Gerät ist aktiviert, wie unter [Tutorial: Verbinden, Einrichten und Aktivieren von Azure Data Box Edge (Vorschau)](data-box-edge-deploy-connect-setup-activate.md) beschrieben.


## <a name="configure-compute"></a>Konfigurieren der Computeumgebung

Um die Computeumgebung auf Ihrer Data Box Edge-Instanz zu konfigurieren, erstellen Sie eine IoT Hub-Ressource.

1. Navigieren Sie im Azure-Portal für Ihre Data Box Edge-Ressource zu **Übersicht**. Wählen Sie im Bereich auf der rechten Seite auf der Kachel **Compute** die Option **Erste Schritte**.

    ![Erste Schritte mit Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Wählen Sie auf der Kachel **Edgecomputing konfigurieren** die Option **Compute konfigurieren**.

    ![Erste Schritte mit Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Geben Sie auf dem Blatt **Edgecomputing konfigurieren** Folgendes ein:

   
    |Feld  |Wert  |
    |---------|---------|
    |IoT Hub     | Wählen Sie zwischen **Neu** und **Vorhanden**. <br> Standardmäßig wird ein Standard-Tarif (S1) verwendet, um eine IoT-Ressource zu erstellen. Wenn Sie eine IoT-Ressource im Free-Tarif verwenden möchten, können Sie diese erstellen und dann als vorhandene Ressource auswählen. <br> Die IoT Hub-Ressource nutzt in jedem Fall dasselbe Abonnement und die Ressourcengruppe wie die Data Box Edge-Ressource.     |
    |NAME     |Geben Sie einen Namen für Ihre IoT Hub-Ressource ein.         |

    ![Erste Schritte mit Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Klicken Sie auf **Erstellen**. Die Erstellung der IoT Hub-Ressource dauert einige Minuten. Nachdem die IoT Hub-Ressource erstellt wurde, wird die Kachel **Edgecomputing konfigurieren** aktualisiert, um die Computekonfiguration anzuzeigen. Wählen Sie auf der Kachel **Compute konfigurieren** die Option **Konfiguration anzeigen** aus, um sich zu vergewissern, dass die Edge-Computerolle konfiguriert wurde.
    
    ![Erste Schritte mit Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Wenn die Edge-Computerolle auf dem Edge-Gerät eingerichtet ist, werden zwei Geräte erstellt: ein IoT-Gerät und ein IoT Edge-Gerät. Beide Geräte können in der IoT Hub-Ressource angezeigt werden. Auf diesem IoT Edge-Gerät wird auch eine IoT Edge-Runtime ausgeführt.

    Derzeit ist für Ihr IoT Edge-Gerät nur die Linux-Plattform verfügbar.


## <a name="add-shares"></a>Hinzufügen von Freigaben

Für die erweiterte Bereitstellung in diesem Tutorial benötigen Sie zwei Freigaben: eine Edgefreigabe und eine weitere lokale Edgefreigabe.

1. Fügen Sie auf dem Gerät eine Edgefreigabe hinzu, indem Sie die folgenden Schritte ausführen:

    1. Navigieren Sie auf Ihrer Data Box Edge-Ressource zu **Edgecomputing > Erste Schritte**.
    2. Wählen Sie auf der Kachel **Freigabe(n) hinzufügen** die Option **Hinzufügen**.
    3. Geben Sie auf dem Blatt **Freigabe hinzufügen** den Freigabenamen an, und wählen Sie den Freigabetyp aus.
    4. Aktivieren Sie zum Bereitstellen der Edgefreigabe das Kontrollkästchen für **Freigabe mit Edgecomputing verwenden**.
    5. Wählen Sie das **Speicherkonto**, den **Speicherdienst**, einen vorhandenen Benutzer und dann die Option **Erstellen** aus.

        ![Hinzufügen einer Edgefreigabe](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Nachdem die Edgefreigabe erstellt wurde, erhalten Sie eine Benachrichtigung zur erfolgreichen Erstellung. Die Freigabeliste wird aktualisiert, um die neue Freigabe anzuzeigen.

2. Fügen Sie auf dem Edgegerät eine lokale Edgefreigabe hinzu, indem Sie alle Schritte des vorherigen Schritts wiederholen und das Kontrollkästchen **Als lokale Edge-Freigabe konfigurieren** aktivieren. Die Daten auf der lokalen Dateifreigabe verbleiben auf dem Gerät.

    ![Hinzufügen einer lokalen Edgefreigabe](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Auf dem Blatt **Freigaben** wird die aktualisierte Liste der Freigaben angezeigt.

    ![Aktualisierte Liste der Freigaben](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Um die Eigenschaften der neu erstellten lokalen Freigabe anzuzeigen, wählen Sie sie aus der Liste aus. Kopieren Sie im Feld **Lokaler Bereitstellungspunkt für Edgecomputingmodule** den Wert für diese Freigabe.

    Sie verwenden diesen lokalen Bereitstellungspunkt beim Bereitstellen des Moduls.

    ![Feld „Lokaler Bereitstellungspunkt für Edgecomputingmodule“](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Um die Eigenschaften der von Ihnen erstellten Edge-Freigabe anzuzeigen, wählen Sie sie aus der Liste aus. Kopieren Sie im Feld **Lokaler Bereitstellungspunkt für Edgecomputingmodule** den Wert für diese Freigabe.

    Sie verwenden diesen lokalen Bereitstellungspunkt beim Bereitstellen des Moduls.

    ![Hinzufügen eines benutzerdefinierten Moduls](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Hinzufügen eines Triggers

1. Wechseln Sie zu **Edgecomputing > Trigger**. Wählen Sie **+ Trigger hinzufügen** aus.

    ![Hinzufügen des Triggers](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Geben Sie auf dem Blatt **Trigger hinzufügen** die folgenden Werte ein.

    |Feld  |Wert  |
    |---------|---------|
    |Triggername     | Ein eindeutiger Name für Ihren Trigger.         |
    |Triggertyp     | Wählen Sie den Trigger **Datei** aus. Es wird jeweils ein Dateitrigger ausgelöst, wenn ein Dateiereignis eintritt, z. B. das Schreiben einer Datei auf die Eingabefreigabe. Andererseits wird ein geplanter Trigger anhand eines von Ihnen festgelegten Zeitplans ausgelöst. In diesem Beispiel wird ein Dateitrigger benötigt.    |
    |Eingabefreigabe     | Wählen Sie eine Eingabefreigabe aus. Die lokale Edgefreigabe ist in diesem Fall die Eingabefreigabe. Mit dem hier verwendeten Modul werden Dateien von der lokalen Edgefreigabe auf eine Edgefreigabe verschoben, von der sie in die Cloud hochgeladen werden.        |

    ![Hinzufügen des Triggers](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Nachdem der Trigger erstellt wurde, werden Sie entsprechend benachrichtigt. Die Liste der Trigger wird aktualisiert, um den neu erstellten Trigger anzuzeigen. Wählen Sie den gerade erstellten Trigger aus.

    ![Hinzufügen des Triggers](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Kopieren und speichern Sie die Beispielroute. Sie werden diese Beispielroute ändern und zu einem späteren Zeitpunkt im IoT Hub verwenden.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Hinzufügen des Triggers](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Hinzufügen eines Moduls

Auf diesem Edgegerät sind keine benutzerdefinierten Module vorhanden. Sie können ein benutzerdefiniertes oder ein vorgefertigtes Modul hinzufügen. Informationen zum Erstellen eines benutzerdefinierten Moduls finden Sie unter [Entwickeln eines C#-IoT Edge-Moduls zum Verschieben von Dateien in Data Box Edge (Vorschau)](data-box-edge-create-iot-edge-module.md).

In diesem Abschnitt fügen Sie dem IoT Edge-Gerät, das Sie unter [Entwickeln eines C#-IoT Edge-Moduls zum Verschieben von Dateien in Data Box Edge (Vorschau)](data-box-edge-create-iot-edge-module.md) erstellt haben, ein benutzerdefiniertes Modul hinzu. Dieses benutzerdefinierte Modul verwendet Dateien von einer lokalen Edgefreigabe auf dem Edgegerät und verschiebt sie auf eine Edgefreigabe (Cloudfreigabe) auf dem Gerät. Die Cloudfreigabe überträgt die Dateien dann per Pushvorgang an das Azure-Speicherkonto, das der Cloudfreigabe zugeordnet ist.

1. Navigieren Sie zu **Edgecomputing > Erste Schritte**. Wählen Sie auf der Kachel **Module hinzufügen** als Szenariotyp die Option **Erweitert** aus. Wählen Sie **Zu IoT Hub wechseln** aus.

    ![Auswählen der erweiterten Bereitstellung](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Wechseln Sie in Ihrer IoT Hub-Ressource zu **IoT Edge-Gerät**, und wählen Sie Ihr IoT Edge-Gerät aus.

    ![Wechseln zum IoT Edge-Gerät in IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Wählen Sie auf der Seite **Gerätedetails** den Link **Module festlegen** aus.

    ![Link „Module festlegen“](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Führen Sie unter **Module hinzufügen** die folgenden Schritte aus:

    1. Geben Sie für das benutzerdefinierte Modul in den Containerregistrierungseinstellungen den Namen, die Adresse, den Benutzernamen und das Kennwort ein.
    Der Name, die Adresse und die aufgeführten Anmeldeinformationen werden verwendet, um Module mit einer übereinstimmenden URL abzurufen. Wählen Sie zum Bereitstellen dieses Moduls unter **Bereitstellungsmodule** die Option **IoT Edge-Modul**. Dieses IoT Edge-Modul ist ein Docker-Container, den Sie auf dem IoT Edge-Gerät bereitstellen können, das Ihrem Data Box Edge-Gerät zugeordnet ist.

        ![Seite „Module festlegen“](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Geben Sie die Einstellungen für das benutzerdefinierte IoT Edge-Modul an. Geben Sie die folgenden Werte ein.
     
        |Feld  |Wert  |
        |---------|---------|
        |NAME     | Ein eindeutiger Name für das Modul. Dieses Modul ist ein Docker-Container, den Sie auf dem IoT Edge-Gerät bereitstellen können, das Ihrer Data Box Edge-Instanz zugeordnet ist.        |
        |Image-URI     | Der Image-URI für das entsprechende Containerimage für das Modul.        |
        |Anmeldeinformationen erforderlich     | Wenn dieses Kontrollkästchen aktiviert ist, werden ein Benutzername und ein Kennwort verwendet, um Module mit einer übereinstimmenden URL abzurufen.        |
    
        Geben Sie im Feld **Optionen für Containererstellung** die lokalen Bereitstellungspunkte für die Edge-Module ein, die Sie in den vorhergehenden Schritten für die Edgefreigabe und die lokale Edgefreigabe kopiert haben.

        > [!IMPORTANT]
        > Die hier verwendeten Pfade werden in Ihrem Container bereitgestellt. Deshalb müssen sie mit den für die Funktionalität in Ihrem Container erwarteten Bedingungen übereinstimmen. Wenn Sie der Beschreibung in [Erstellen eines benutzerdefinierten Moduls](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code) folgen, müssen für den in diesem Modul angegebenen Code die kopierten Pfade verwendet werden. Ändern Sie deshalb diese Pfade nicht.
    
        Im Feld **Optionen für Containererstellung** können Sie das folgende Beispiel einfügen:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Geben Sie alle Umgebungsvariablen an, die für Ihr Modul verwendet werden. Umgebungsvariablen stellen optionale Informationen bereit, die Ihnen bei der Definition der Umgebung helfen, in der Ihr Modul ausgeführt wird.

        ![Feld „Optionen für Containererstellung“](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Konfigurieren Sie die erweiterten Einstellungen für die Edge-Laufzeit, falls erforderlich, und klicken Sie dann auf **Weiter**.

        ![Hinzufügen eines benutzerdefinierten Moduls](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Legen Sie unter **Routen angeben** die Routen zwischen den Modulen fest.  
    
    ![Abschnitt „Routen angeben“](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Sie können *route* durch die nachstehende „route“-Zeichenfolge ersetzen, die Sie zuvor kopiert haben. Geben Sie in diesem Beispiel den Namen der lokalen Freigabe ein, über die Daten per Pushvorgang an die Cloudfreigabe übertragen werden. Ersetzen Sie `modulename` durch den Namen des Moduls. Klicken Sie auf **Weiter**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Abschnitt „Routen angeben“](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Sehen Sie sich unter **Bereitstellung prüfen** alle Einstellungen an, und wählen Sie dann **Übermitteln**, um das Modul für die Bereitstellung zu übermitteln.

    ![Seite „Module festlegen“](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Diese Aktion startet die Bereitstellung des Moduls. Nach Abschluss der Bereitstellung lautet der **Laufzeitstatus** des Moduls **Wird ausgeführt**.

    ![Hinzufügen eines benutzerdefinierten Moduls](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Überprüfen der Datentransformation und -übertragung

Im letzten Schritt wird sichergestellt, dass das Modul verbunden ist und wie erwartet ausgeführt wird. Der Laufzeitstatus des Moduls sollte für Ihr IoT Edge-Gerät auf der IoT Hub-Ressource „Wird ausgeführt“ lauten.

Führen Sie die folgenden Schritte aus, um die Datentransformation und -übertragung an Azure zu überprüfen.
 
1.  Stellen Sie im Datei-Explorer eine Verbindung mit den lokalen und anderen Edgefreigaben her, die Sie zuvor erstellt haben.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Fügen Sie der lokalen Freigabe Daten hinzu.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Die Daten werden auf die Cloudfreigabe verschoben.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Anschließend werden die Daten per Pushvorgang von der Cloudfreigabe in das Speicherkonto übertragen. Um die Daten anzuzeigen, wechseln Sie zu Ihrem Speicherkonto, und wählen Sie **Speicher-Explorer** aus. Sie können die hochgeladenen Daten in Ihrem Speicherkonto anzeigen.

    ![Überprüfen der Datentransformation](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Sie haben den Überprüfungsvorgang abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren der Computeumgebung
> * Hinzufügen von Freigaben
> * Hinzufügen eines Triggers
> * Hinzufügen eines Computemoduls
> * Überprüfen der Datentransformation und -übertragung

Weitere Informationen zum Verwalten Ihres Data Box Edge-Geräts finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwenden der lokalen Webbenutzeroberfläche zum Verwalten einer Data Box Edge-Instanz](data-box-edge-manage-access-power-connectivity-mode.md)
