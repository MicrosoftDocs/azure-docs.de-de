---
title: Schnellstart – Bereitstellen eines simulierten X.509-Geräts für Azure IoT Hub mithilfe von Node.js
description: Hier erfahren Sie, wie Sie ein simuliertes X.509-Gerät mithilfe des Node.js-Geräte-SDK für Azure IoT Hub Device Provisioning Service (DPS) erstellen und bereitstellen. In dieser Schnellstartanleitung werden individuelle Registrierungen verwendet.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc, devx-track-js
ms.openlocfilehash: fe5279725a3d01001b44074eca8656e88d778cab
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968161"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Schnellstart: Erstellen und Bereitstellen eines simulierten X.509-Geräts mithilfe des Node.js-Geräte-SDK für den IoT Hub Device Provisioning-Dienst

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In dieser Schnellstartanleitung erstellen Sie ein simuliertes X.509-Gerät auf einem Windows-Computer. Sie verwenden Node.js-Beispielcode für das Gerät, um dieses simulierte Gerät mit Ihrem IoT-Hub zu verbinden, indem eine einzelne Registrierung beim Device Provisioning Service (DPS) verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

- Mit Konzepten für die [Bereitstellung](about-iot-dps.md#provisioning-process) vertraut sein.
- Ausführung der Schritte unter [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md).
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).
- [OpenSSL](https://www.openssl.org/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung 

1. Führen Sie zunächst die Schritte unter [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md) aus, bevor Sie mit dieser Anleitung fortfahren.

2. Stellen Sie sicher, dass auf Ihrem Computer [Node.js v4.0 oder höher](https://nodejs.org) installiert ist.

3. Vergewissern Sie sich, dass [Git](https://git-scm.com/download/) auf Ihrem Computer installiert ist und den Umgebungsvariablen hinzugefügt wurde, auf die das Befehlsfenster Zugriff hat. 

4. Vergewissern Sie sich, dass [OpenSSL](https://www.openssl.org/) auf Ihrem Computer installiert ist und den Umgebungsvariablen hinzugefügt wurde, auf die das Befehlsfenster Zugriff hat. Diese Bibliothek kann entweder über die Quelle erstellt und installiert oder über einen [Drittanbieter](https://wiki.openssl.org/index.php/Binaries) (z.B. [hier](https://sourceforge.net/projects/openssl/)) heruntergeladen und installiert werden. 

    > [!NOTE]
    > Wenn Sie Ihre X.509-Zertifikate vom Typ _Stammzertifikat_, _Zwischenzertifikat_ bzw. _Untergeordnetes Zertifikat_ bereits erstellt haben, können Sie diesen und alle weiteren Schritte zur Zertifikaterstellung überspringen.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Erstellen eines selbstsignierten X.509-Gerätezertifikats und eines individuellen Registrierungseintrags

In diesem Abschnitt verwenden Sie ein selbstsigniertes X.509-Zertifikat, wobei Folgendes zu beachten ist:

* Selbstsignierte Zertifikate dienen nur zu Testzwecken und sollten nicht in der Produktion verwendet werden.
* Die Standardgültigkeitsdauer für ein selbstsigniertes Zertifikat beträgt ein Jahr.

Sie verwenden Beispielcode aus dem [Azure IoT SDK für Node.js](https://github.com/Azure/azure-iot-sdk-node.git), um das Zertifikat zu erstellen, das mit dem individuellen Registrierungseintrag für das simulierte Gerät verwendet werden soll.

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:

- [Registrierungsgruppen:](concepts-service.md#enrollment-group) Werden zum Registrieren mehrerer verwandter Geräte verwendet.
- [Individuelle Registrierungen](concepts-service.md#individual-enrollment): Werden zum Registrieren eines einzelnen Geräts verwendet.

In diesem Artikel werden individuelle Registrierungen veranschaulicht.

1. Öffnen Sie eine Eingabeaufforderung. Klonen Sie das GitHub-Repository für die Codebeispiele:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Navigieren Sie zum Zertifikatgeneratorskript, und erstellen Sie das Projekt. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Erstellen Sie das _untergeordnete_ X.509-Zertifikat, indem Sie das Skript mit Ihrem eigenen Zertifikatnamen (_certificate-name_) ausführen. Der allgemeine Name des untergeordneten Zertifikats wird zur [Registrierungs-ID](./concepts-service.md#registration-id). Achten Sie daher darauf, dass Sie nur Kleinbuchstaben, Zahlen und Bindestriche verwenden.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, wählen Sie im Menü links die Schaltfläche **Alle Ressourcen** aus, und öffnen Sie Ihre Device Provisioning Service-Instanz.

5. Wählen Sie im Menü von Device Provisioning Service die Option **Registrierungen verwalten** aus. Wählen Sie die Registerkarte **Individuelle Registrierungen** und dann oben die Schaltfläche **Individuelle Registrierung hinzufügen** aus. 

6. Geben Sie im Bereich **Registrierung hinzufügen** die folgenden Informationen ein:
   - Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
   - Wählen Sie unter *Primäres Zertifikat (PEM- oder CER-Datei)* die Option *Datei auswählen* aus, und wählen Sie dann die in den vorherigen Schritten erstellte Zertifikatsdatei **{Zertifikatname}_cert.pem** aus.  
   - Optional können Sie die folgenden Informationen angeben:
     - Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
     - Geben Sie eine eindeutige Geräte-ID ein. Achten Sie darauf, dass Sie beim Benennen Ihres Geräts keine sensiblen Daten angeben. 
     - Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.
     - Klicken Sie abschließend auf die Schaltfläche **Speichern**. 

     [![Hinzufügen einer individuellen Registrierung für den X.509-Nachweis im Portal](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     Nach der erfolgreichen Registrierung wird Ihr X.509-Gerät als **{certificatename}** auf der Registerkarte *Individuelle Registrierungen* in der Spalte *Registrierungs-ID* angezeigt. Notieren Sie sich diesen Wert zur späteren Verwendung.

## <a name="simulate-the-device"></a>Simulieren des Geräts

Die Verwendung des [Node.js-Geräte-SDK für Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node) ist eine einfache Möglichkeit zum Simulieren eines Geräts. Weitere Informationen finden Sie unter [Konzepte für Geräte mit dem IoT Hub Device Provisioning-Dienst](./concepts-service.md).

1. Wählen Sie im Azure-Portal das Blatt **Übersicht** für Ihren Gerätebereitstellungsdienst aus, und notieren Sie sich die Werte für **_Globaler Geräteendpunkt_** und **_ID-Bereich_** .

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Kopieren Sie Ihr _Zertifikat_ und den _Schlüssel_ in den Beispielordner.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Navigieren Sie zum Gerätetestskript, und erstellen Sie das Projekt. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Bearbeiten Sie die Datei **register\_x509.js**. Speichern Sie die Datei, nachdem Sie die folgenden Änderungen vorgenommen haben.
    - Ersetzen Sie `provisioning host` durch den **_Globalen Geräteendpunkt_** aus **Schritt 1** oben.
    - Ersetzen Sie `id scope` durch den **_ID-Bereich_** aus **Schritt 1** oben. 
    - Ersetzen Sie `registration id` durch die **_Registrierungs-ID_** aus dem vorherigen Abschnitt.
    - Ersetzen Sie `cert filename` und `key filename` durch die Dateien, die Sie oben in **Schritt 2** kopiert haben. 

5. Führen Sie das Skript aus, und stellen Sie sicher, dass das Gerät erfolgreich bereitgestellt wurde.

    ```cmd/sh
    node register_x509.js
    ```   

6. Navigieren Sie im Portal zu dem mit Ihrem Bereitstellungsdienst verknüpften IoT-Hub, und öffnen Sie das Blatt **IoT-Geräte**. Nach erfolgreicher Bereitstellung des simulierten X.509-Geräts auf dem Hub wird die zugehörige Geräte-ID auf dem Blatt **IoT-Geräte** angezeigt, und der *STATUS* lautet **Aktiviert**. Falls das Blatt bereits vor dem Ausführen der Beispielgeräteanwendung geöffnet war, müssen Sie unter Umständen im oberen Bereich auf die Schaltfläche **Aktualisieren** klicken. 

    ![Geräteregistrierung bei der IoT Hub-Instanz](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Wenn Sie den *anfänglichen Gerätezwillingsstatus* im Registrierungseintrag für Ihr Gerät gegenüber dem Standardwert geändert haben, kann der gewünschte Zwillingsstatus vom Hub abgerufen werden, und es können entsprechende Aktionen durchgeführt werden. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Geräteclientbeispiel weiterhin verwenden und erkunden möchten, überspringen Sie die Bereinigung der in diesem Schnellstart erstellten Ressourcen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieses Schnellstarts erstellt wurden.

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.
2. Wählen Sie im Azure-Portal im Menü links **Alle Ressourcen** und dann Ihren Gerätebereitstellungsdienst aus. Öffnen Sie das Blatt **Registrierungen verwalten** für Ihren Dienst, und wählen Sie die Registerkarte **Individuelle Registrierungen** aus. Aktivieren Sie das Kontrollkästchen *REGISTRIERUNGS-ID* für das Gerät, das Sie in diesem Schnellstart registriert haben, und wählen Sie oben im Bereich die Schaltfläche **Löschen** aus. 
3. Wählen Sie im linken Menü im Azure-Portal **Alle Ressourcen** und dann Ihren IoT-Hub aus. Öffnen Sie das Blatt **IoT-Geräte** für Ihren Hub, aktivieren Sie das Kontrollkästchen *GERÄTE-ID* für das Gerät, das Sie in diesem Schnellstart registriert haben, und wählen Sie dann oben im Bereich die Schaltfläche **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie ein simuliertes X.509-Gerät erstellt und mithilfe von Azure IoT Hub Device Provisioning Service im Portal für Ihren IoT-Hub bereitgestellt. Informationen zum programmgesteuerten Registrieren Ihres X.509-Geräts finden Sie im Schnellstart für die programmgesteuerte Registrierung von X.509-Geräten. 

> [!div class="nextstepaction"]
> [Azure-Schnellstart: Registrieren von X.509-Geräten bei Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-node.md)