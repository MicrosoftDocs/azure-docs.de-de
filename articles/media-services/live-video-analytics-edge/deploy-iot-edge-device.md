---
title: 'Bereitstellen von Live Video Analytics auf einem IoT Edge-Gerät: Azure'
description: In diesem Artikel werden Schritte aufgelistet, die Sie bei der Bereitstellung von Live Video Analytics auf Ihrem IoT Edge-Gerät unterstützen. Dies können Sie beispielsweise tun, wenn Sie Zugriff auf einen lokalen Linux-Computer besitzen und/oder zuvor ein Azure Media Services-Konto erstellt haben.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 4fa4a9643976ba513b025706cacec26b2a50afb5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498318"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Bereitstellen von Live Video Analytics auf einem IoT Edge-Gerät

In diesem Artikel werden Schritte aufgelistet, die Sie bei der Bereitstellung von Live Video Analytics auf Ihrem IoT Edge-Gerät unterstützen. Dies können Sie beispielsweise tun, wenn Sie Zugriff auf einen lokalen Linux-Computer besitzen und/oder zuvor ein Azure Media Services-Konto erstellt haben.

> [!NOTE]
> ARM64-Geräte werden ab Build `1.0.4` von Live Video Analytics in IoT Edge unterstützt.
> Die Unterstützung für die Ausführung der Azure IoT Edge-Runtime auf ARM64-Geräten ist in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Ein x86-64- oder ARM64-Gerät, auf dem eines der [unterstützten Linux-Betriebssysteme ausgeführt wird](../../iot-edge/support.md#operating-systems)
* Ein Azure-Abonnement, für das Sie über [Besitzerrechte](../../role-based-access-control/built-in-roles.md#owner) verfügen
* [Ein erstellter und eingerichteter IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* [Registrierung bei einem IoT Edge-Gerät](../../iot-edge/how-to-manual-provision-symmetric-key.md)
* [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](../../iot-edge/how-to-install-iot-edge.md)
* [Erstellen eines Azure Media Services-Kontos](../latest/create-account-howto.md)

    * Verwenden Sie eine dieser Regionen: USA, Osten 2; USA, Osten; USA, Mitte; USA, Norden-Mitte; Japan, Osten; USA, Westen; USA, Westen 2; USA, Westen-Mitte, Kanada, Osten, Vereinigtes Königreich, Süden, Frankreich, Mitte, Frankreich, Süden, Schweiz, Norden, Schweiz, Westen und Japan, Westen.
    * Es wird empfohlen, dass Sie Speicherkonten vom Typ „Allgemein v2“ (GPv2) verwenden.

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Konfigurieren von Azure-Ressourcen für die Verwendung von Live Video Analytics

### <a name="create-custom-azure-resource-manager-role"></a>Erstellen einer benutzerdefinierten Azure Resource Manager-Rolle

Lesen Sie dazu [Erstellen einer benutzerdefinierten Azure Resource Manager-Rolle](create-custom-azure-resource-manager-role-how-to.md), und weisen Sie die Rolle einem Dienstprinzipal für die Verwendung von Live Video Analytics zu.

### <a name="set-up-a-premium-streaming-endpoint"></a>Einrichten eines Premium-Streamingendpunkts

Wenn Sie Live Video Analytics zum kontinuierlichen Aufzeichnen von Videos in der Cloud verwenden möchten, und anschließend [Abfrage-APIs](playback-recordings-how-to.md#query-api) verwenden, bevor Sie sie wiedergeben, empfiehlt es sich, Ihren Media Service für die Verwendung eines [Premium-Streamingendpunkts](../latest/streaming-endpoint-concept.md#types) zu aktualisieren.  

Dieser Schritt ist optional. Sie können für ihn diesen Azure CLI-Befehl verwenden:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Sie können diesen Befehl verwenden, um den Streamingendpunkt zu starten. 

> [!IMPORTANT]
> Ab diesem Zeitpunkt wird die Nutzung Ihrem Abonnement in Rechnung gestellt.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Führen Sie die Schritte in diesem Artikel aus, um Anmeldeinformationen für den Zugriff auf die Media Service-APIs zu erhalten: [Zugreifen auf die Media Service-APIs](../latest/access-api-howto.md?tabs=portal). Wählen Sie dann die Registerkarte „Portal“ aus.

## <a name="create-and-use-local-user-account-for-deployment"></a>Erstellen und Verwenden eines lokalen Benutzerkontos für die Bereitstellung
Erstellen Sie ein lokales Benutzerkonto mit möglichst geringen Berechtigungen, um Live Video Analytics im IoT Edge-Modul auszuführen. Führen Sie beispielsweise die folgenden Befehle auf Ihrem Linux-Computer aus:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Zuweisen von Berechtigungen für den Gerätespeicher

Nachdem Sie nun ein lokales Benutzerkonto erstellt haben, geht es wie folgt weiter: 

* Sie benötigen einen lokalen Ordner, in dem die Konfigurationsdaten der Anwendung gespeichert werden. Erstellen Sie einen Ordner, und erteilen Sie dem LocalUser-Konto mit den folgenden Befehlen Schreibberechtigungen für diesen Ordner:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* Sie benötigen außerdem einen Ordner zum [Aufzeichnen von Video in eine lokale Datei](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Verwenden Sie die folgenden Befehle, um einen lokalen Ordner für diesen Zweck zu erstellen:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Bereitstellen des Live Video Analytics-Edge-Moduls

Live Video Analytics in IoT Edge macht Eigenschaften von Modulzwillingen verfügbar, die unter [Konfigurationsschema für Modulzwillinge](module-twin-configuration-schema.md) dokumentiert sind. 

### <a name="deploy-using-the-azure-portal"></a>Bereitstellen über das Azure-Portal

Im Azure-Portal werden Sie durch das Erstellen eines Bereitstellungsmanifests und die Übertragung der Bereitstellung auf ein IoT Edge-Gerät per Pushvorgang geführt.  
#### <a name="select-your-device-and-set-modules"></a>Auswählen des Geräts und Festlegen von Modulen

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an, und navigieren Sie zu Ihrem IoT Hub.
1. Wählen Sie im Menü **IoT Edge** aus.
1. Klicken Sie in der Liste der Geräte auf die ID des Zielgeräts.
1. Wählen Sie **Module festlegen** aus.

#### <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Das Azure-Portal enthält einen Assistenten, der Sie durch die Erstellung eines Bereitstellungsmanifests führt. Der Vorgang besteht aus drei Schritten, die auf Registerkarten aufgeteilt sind: **Module**, **Routen** und **Überprüfen und erstellen**.

#### <a name="add-modules"></a>Hinzufügen von Modulen

1. Klicken Sie im Abschnitt **IoT Edge-Module** auf das Dropdownmenü **Hinzufügen**, und wählen Sie **IoT Edge-Modul** aus, um die Seite **IoT Edge-Modul hinzufügen** anzuzeigen.
1. Geben Sie auf der Registerkarte **Moduleinstellungen** einen Namen für das Modul und den URI für das Containerimage an:   
    Beispiele:
    
    * **Name des IoT Edge-Moduls**: lvaEdge
    * **Image-URI**: mcr.microsoft.com/media/live-video-analytics:1.0    
    
    ![Screenshot der Registerkarte „Moduleinstellungen“](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Klicken Sie erst dann auf **Hinzufügen**, wenn Sie auf den Registerkarten **Moduleinstellungen**, **Optionen für Containererstellung** und **Einstellungen für Modulzwilling** Werte eingegeben haben, wie in dieser Vorgehensweise beschrieben.
    
    > [!WARNING]
    > Bei Modulaufrufen für Azure IoT Edge muss die Groß-/Kleinschreibung beachtet werden. Notieren Sie sich die genaue Zeichenfolge, die Sie als Modulnamen verwenden.

1. Öffnen Sie die Registerkarte **Umgebungsvariablen**.
   
   Fügen Sie die folgenden Werte in die Eingabefelder für ![Umgebungsvariablen](./media/deploy-iot-edge-device/environment-variables.png) ein. 

1. Öffnen Sie die Registerkarte **Optionen für Containererstellung**.

    ![Optionen für die Containererstellung](./media/deploy-iot-edge-device/container-create-options.png)
 
    Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in das Feld ein, um die Größe der vom Modul erzeugten Protokolldateien einzuschränken.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   Der Abschnitt „Binds“ im JSON-Code enthält zwei Einträge:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": Hiermit werden die persistenten Anwendungskonfigurationsdaten aus dem Container gebunden und auf dem Edge-Gerät gespeichert.
   1. "/var/media:/var/media": Dadurch werden die Medienordner zwischen dem Edge-Gerät und dem Container gebunden. Dies wird zum Speichern der Videoaufzeichnungen verwendet, wenn Sie eine Mediengraphtopologie ausführen, die das Speichern von Videoclips auf dem Edge-Gerät unterstützt.
   
1. Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn auf der Registerkarte **Einstellungen für Modulzwilling** in das Feld ein.
 
    ![Zwillingseinstellungen](./media/deploy-iot-edge-device/twin-settings.png)

    Live Video Analytics in IoT Edge benötigt für die Ausführung eine Reihe obligatorischer Zwillingseigenschaften, die unter [Konfigurationsschema für Modulzwillinge](module-twin-configuration-schema.md) aufgeführt sind.  

    Der JSON-Code, den Sie im Editierfeld „Einstellungen für Modulzwilling“ eingeben müssen, sieht wie folgt aus:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Dies sind die **erforderlichen** Eigenschaften, und für den JSON-Code oben gelten folgende Zuordnungen:  
    * {subscriptionID}: Ihre Azure-Abonnement-ID
    * {resourcegroupname}: Die Ressourcengruppe, zu der Ihr Media Service-Konto gehört
    * {AMS-account-name}: Der Name Ihres Media Services-Kontos
    
    Informationen zum Abrufen der anderen Werte finden Sie unter [Zugriff auf Azure Media Services API](../latest/access-api-howto.md?tabs=portal). Wählen Sie dann die Registerkarte „Portal“ aus.  
    * aadTenantId: Dies ist die ID Ihres Mandanten, die mit der „AadTenantId“ aus dem Link oben übereinstimmt.
    * aadServicePrincipalAppId: Die App-ID des Dienstprinzipals für Ihr Media Service-Konto, die mit der „AadClientId“ aus dem Link oben übereinstimmt.
    * aadServicePrincipalSecret: Das Kennwort des Dienstprinzipals, das mit „AadSecret“ aus dem Link oben übereinstimmt.

    Unten finden Sie einige weitere **empfohlene** Eigenschaften, die dem JSON-Code hinzugefügt werden können und die Überwachung des Moduls unterstützen. Weitere Informationen finden Sie unter [Überwachung und Protokollierung](monitoring-logging.md).
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    Es folgen einige **optionale** Eigenschaften, die Sie dem JSON-Code hinzufügen können:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > Die Zwillingseigenschaft **allowUnsecuredEndpoints** wird für die Zwecke der Tutorials und Schnellstartanleitungen auf „true“ festgelegt.   
   Sie sollten diese Eigenschaft in Produktionsumgebungen auf **false** festlegen. Dadurch wird sichergestellt, dass die Anwendung alle nicht gesicherten Endpunkte blockiert und zum Ausführen der Graphtopologien gültige Anmeldeinformationen für die Verbindungen benötigt werden.  
   
    Wählen Sie „Hinzufügen“ aus, um die Eigenschaften für Modulzwillinge hinzuzufügen.
1. Klicken Sie auf **Weiter: Routen**, um mit dem Abschnitt über Routen fortzufahren.
    Geben Sie Routen an.

Behalten Sie die Standardrouten bei, und klicken Sie auf **Weiter: Überprüfen und erstellen**, um mit dem Abschnitt zur Überprüfung fortzufahren.

#### <a name="review-deployment"></a>Überprüfen der Bereitstellung

Im Abschnitt zur Überprüfung wird das JSON-Bereitstellungsmanifest angezeigt, das anhand Ihrer Auswahl in den vorherigen beiden Abschnitten erstellt wurde. Es wurden außerdem zwei Module deklariert, die Sie nicht hinzugefügt haben: $edgeAgent und $edgeHub. Diese beiden Module bilden die IoT Edge-Runtime und sind in jeder Bereitstellung erforderliche Standardvorgaben.

Überprüfen Sie Ihre Bereitstellungsinformationen, und wählen Sie „Erstellen“ aus.

### <a name="verify-your-deployment"></a>Überprüfen Ihrer Bereitstellung

Nach dem Erstellen der Bereitstellung kehren Sie zur Seite „IoT Edge“ Ihres IoT-Hubs zurück.

1. Wählen Sie das IoT Edge-Zielgerät für die Bereitstellung aus, um dessen Details zu öffnen.
2. Vergewissern Sie sich anhand der Gerätedetails, dass das Blob Storage-Modul als **In Bereitstellung angegeben und als Vom Gerät gemeldet** aufgeführt ist.

Unter Umständen dauert es einen Moment, bis das Modul auf dem Gerät gestartet und an IoT Hub gemeldet wurde. Aktualisieren Sie die Seite, um einen aktualisierten Status anzuzeigen.
Statuscode: 200: OK bedeutet, dass [die IoT Edge-Runtime](../../iot-edge/iot-edge-runtime.md) fehlerfrei ist und ordnungsgemäß funktioniert.

![Im Screenshot wird ein Statuswert für eine IoT Edge-Runtime angezeigt.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Aufrufen einer direkten Methode

Als nächstes testen Sie das Beispiel, indem Sie eine direkte Methode aufrufen. Ausführliche Informationen zu allen von Ihrem IvaEdge-Modul bereitgestellten direkten Methoden finden Sie unter [Direkte Methoden für Live Video Analytics in IoT Edge](direct-methods.md).

1. Wenn Sie auf das von Ihnen erstellte Edge-Modul klicken, gelangen Sie zu seiner Konfigurationsseite.  

    ![Im Screenshot wird die Konfigurationsseite eines Edge-Moduls angezeigt.](./media/deploy-iot-edge-device/modules.png)
1. Klicken Sie auf die Menüoption „Direkte Methode“.

    > [!NOTE] 
    > Sie müssen in den Abschnitten für die Verbindungszeichenfolge einen Wert hinzufügen, wie Sie auf der aktuellen Seite sehen können. Sie brauchen im Abschnitt **Einstellungsname** nichts auszublenden oder zu ändern. Es ist in Ordnung, diese Informationen öffentlich zu belassen.

    ![Direkte Methode](./media/deploy-iot-edge-device/module-details.png)
1. Geben Sie anschließend im Feld „Methodenname“ den Wert „GraphTopologyList“ ein.
1. Kopieren Sie anschließend die folgende JSON-Nutzlast, und fügen Sie sie in das Nutzlastfeld ein:
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. Klicken Sie oben auf der Seite auf die Option „Methode aufrufen“.

    ![Direkte Methoden](./media/deploy-iot-edge-device/direct-method.png)
1. Im Ergebnisfeld sollte eine 200-Statusmeldung angezeigt werden.

    ![Die 200-Statusmeldung](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erste Schritte mit Live Video Analytics in IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Wenn Sie mit der obigen Schnellstartanleitung fortfahren und die direkten Methoden mithilfe von Visual Studio Code aufrufen, verwenden Sie statt des standardmäßigen `lva-sample-device` das Gerät, das in diesem Artikel dem IoT Hub hinzugefügt wurde.