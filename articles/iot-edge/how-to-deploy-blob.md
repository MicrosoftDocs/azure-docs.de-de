---
title: Bereitstellen von Blob Storage im Modul auf Ihrem Gerät (Azure IoT Edge)
description: Stellen Sie ein Azure Blob Storage-Modul für Ihr IoT Edge-Gerät bereit, um Daten am Edge zu speichern.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 12f0af5f051d02945eeb9b1f7d4bfc50ef98f281
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014685"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Bereitstellen des Moduls „Azure Blob Storage auf IoT Edge“ auf Ihrem Gerät

Es gibt mehrere Möglichkeiten, wie Sie Module auf einem IoT Edge-Gerät bereitstellen können. Alle sind für Azure Blob Storage auf IoT Edge-Module geeignet. Die beiden einfachsten Methoden stellen die Verwendung des Azure-Portals oder von Visual Studio Code-Vorlagen dar.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement.
- Ein IoT Edge-Gerät.

  Wenn Sie kein IoT Edge-Gerät eingerichtet haben, können Sie eines auf einem virtuellen Azure-Computer erstellen. Führen Sie die Schritte in einer der Schnellstartanleitungen zum [Erstellen eines virtuellen Linux-Geräts](quickstart-linux.md) oder [Erstellen eines virtuellen Windows-Geräts](quickstart.md) aus.

- [Visual Studio Code](https://code.visualstudio.com/) und die [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), wenn Sie die Bereitstellung über Visual Studio Code durchführen.

## <a name="deploy-from-the-azure-portal"></a>Bereitstellen über das Azure-Portal

Im Azure-Portal werden Sie durch das Erstellen eines Bereitstellungsmanifests und die Übertragung der Bereitstellung auf ein IoT Edge-Gerät per Pushvorgang geführt.

### <a name="select-your-device"></a>Auswählen Ihres Geräts

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.
1. Wählen Sie im Menü **IoT Edge** aus.
1. Klicken Sie in der Liste der Geräte auf die ID des Zielgeräts.
1. Wählen Sie **Module festlegen** aus.

### <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Das Azure-Portal enthält einen Assistenten, der Sie durch die Erstellung eines Bereitstellungsmanifests führt. Der Vorgang besteht aus drei Schritten, die auf Registerkarten aufgeteilt sind: **Module**, **Routen** und **Überprüfen und erstellen**.

#### <a name="add-modules"></a>Hinzufügen von Modulen

1. Klicken Sie im Abschnitt **IoT Edge-Module** auf das Dropdownmenü **Hinzufügen**, und wählen Sie **IoT Edge-Modul** aus, um die Seite **IoT Edge-Modul hinzufügen** anzuzeigen.

2. Geben Sie auf der Registerkarte **Moduleinstellungen** einen Namen für das Modul und den URI für das Containerimage an:

   Beispiele:
  
   - **Name des IoT Edge-Moduls**: `azureblobstorageoniotedge`
   - **Image-URI**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Screenshot: Registerkarte „Moduleinstellungen“ auf der Seite „IoT Edge-Modul hinzufügen“](./media/how-to-deploy-blob/addmodule-tab1.png)

   Klicken Sie erst dann auf **Hinzufügen**, wenn Sie auf den Registerkarten **Moduleinstellungen**, **Optionen für Containererstellung** und **Einstellungen für Modulzwilling** Werte eingegeben haben, wie in diesem Verfahren beschrieben.

   > [!IMPORTANT]
   > Bei Modulaufrufen für Azure IoT Edge muss die Groß-/Kleinschreibung beachtet werden, und auch für das Storage SDK werden standardmäßig Kleinbuchstaben verwendet. Der Name des Moduls im [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) lautet **AzureBlobStorageonIoTEdge**. Indem Sie den Namen aber ändern und Kleinbuchstaben verwenden, können Sie sicherstellen, dass Ihre Verbindungen mit dem Modul „Azure Blob Storage auf IoT Edge“ nicht unterbrochen werden.

3. Öffnen Sie die Registerkarte **Optionen für Containererstellung**.

   ![Screenshot: Registerkarte „Optionen für Containererstellung“ auf der Seite „IoT Edge-Modul hinzufügen“](./media/how-to-deploy-blob/addmodule-tab3.png)

   Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in das Feld ein, um Informationen zum Speicherkonto und eine Einbindung für den Speicher auf Ihrem Gerät bereitzustellen.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. Aktualisieren Sie den in **Optionen für Containererstellung** kopierten JSON-Code mit folgenden Informationen:

   - Ersetzen Sie `<your storage account name>` durch einen leicht zu merkenden Namen. Kontonamen sollten 3 bis 24 Zeichen lang sein und nur Kleinbuchstaben und Zahlen enthalten. Leerzeichen sind nicht zulässig.

   - Ersetzen Sie `<your storage account key>` durch einen Base64-Schlüssel mit 64 Bytes. Sie können einen Schlüssel mit Tools wie [GeneratePlus](https://generate.plus/en/base64) generieren. Mit diesen Anmeldeinformationen greifen Sie über andere Module auf Blob Storage zu.

   - Ersetzen Sie `<storage mount>` gemäß Ihrem Containerbetriebssystem. Geben Sie den Namen eines [Volumes](https://docs.docker.com/storage/volumes/) oder den absoluten Pfad zu einem vorhandenen Verzeichnis auf Ihrem IoT Edge-Gerät an, auf bzw. unter dem das Blobmodul seine Daten speichern wird. Die Speicherbereitstellung ordnet einen Ort auf Ihrem Gerät einem festen Ort im Modul zu.

     - Für Linux-Container lautet das Format „ **\<your storage path or volume>:/blobroot**“. Beispiel:
         - Verwenden einer [Volumebereitstellung](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - Verwenden einer [Bindungsbereitstellung](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` Führen Sie unbedingt die Schritte zum [Gewähren des Verzeichniszugriffs für den Containerbenutzer](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) aus.
     - Für Windows-Container lautet das Format „ **\<your storage path or volume>:C:/BlobRoot**“. Beispiel:
         - Verwenden einer [Volumebereitstellung](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot`
         - Verwenden einer [Bindungsbereitstellung](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot`
         - Sie können Ihre SMB-Netzwerkadresse zuordnen, statt das lokale Laufwerk zu verwenden. Weitere Informationen hierzu finden Sie unter [Verwenden der SMB-Freigabe als lokalen Speicher](how-to-store-data-blob.md#using-smb-share-as-your-local-storage).

     > [!IMPORTANT]
     > Die zweite Hälfte des Werts für die Speicherbereitstellung verweist auf einen bestimmten Ort im Modul „Blob Storage in IoT Edge“ und darf nicht geändert werden. Die Speicherbereitstellung muss immer auf **:/blobroot** (Linux-Container) bzw. auf **:C:/BlobRoot** (Windows-Container) enden.

5. Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn auf der Registerkarte **Einstellungen für Modulzwilling** in das Feld ein.

   ![Screenshot: Registerkarte „Einstellungen für Modulzwilling“ auf der Seite „IoT Edge-Modul hinzufügen“](./media/how-to-deploy-blob/addmodule-tab4.png)

   Konfigurieren Sie die jeweilige Eigenschaft mit einem geeigneten Wert, wie durch die Platzhalter angegeben. Wenn Sie den IoT Edge-Simulator verwenden, legen Sie die Werte auf die zugehörigen Umgebungsvariablen für diese Eigenschaften fest, wie durch [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) und [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) beschrieben.

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Informationen zum Konfigurieren von „deviceToCloudUploadProperties“ und „deviceAutoDeleteProperties“ nach Bereitstellung Ihres Moduls finden Sie unter [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) (Bearbeiten des Modulzwillings). Weitere Informationen zu gewünschten Eigenschaften finden Sie unter [Definieren oder Aktualisieren gewünschter Eigenschaften](module-composition.md#define-or-update-desired-properties).

6. Wählen Sie **Hinzufügen**.

7. Klicken Sie auf **Weiter: Routen**, um mit dem Abschnitt über Routen fortzufahren.

#### <a name="specify-routes"></a>Angeben von Routen

Behalten Sie die Standardrouten bei, und klicken Sie auf **Weiter: Überprüfen und erstellen**, um mit dem Abschnitt zur Überprüfung fortzufahren.

#### <a name="review-deployment"></a>Überprüfen der Bereitstellung

Im Abschnitt zur Überprüfung wird das JSON-Bereitstellungsmanifest angezeigt, das anhand Ihrer Auswahl in den vorherigen beiden Abschnitten erstellt wurde. Es wurden auch zwei Module deklariert, die Sie nicht hinzugefügt haben: **$edgeAgent** und **$edgeHub**. Diese beiden Module bilden die [IoT Edge-Runtime](iot-edge-runtime.md) und sind in jeder Bereitstellung erforderliche Standardvorgaben.

Überprüfen Sie Ihre Bereitstellungsinformationen, und klicken Sie dann auf **Erstellen**.

### <a name="verify-your-deployment"></a>Überprüfen Ihrer Bereitstellung

Nach dem Erstellen der Bereitstellung kehren Sie zur Seite **IoT Edge** Ihres IoT-Hubs zurück.

1. Wählen Sie das IoT Edge-Zielgerät für die Bereitstellung aus, um dessen Details zu öffnen.
1. Vergewissern Sie sich anhand der Gerätedetails, dass das Blob Storage-Modul als **In Bereitstellung angegeben** und als **Vom Gerät gemeldet** aufgeführt ist.

Unter Umständen dauert es einen Moment, bis das Modul auf dem Gerät gestartet und an IoT Hub gemeldet wurde. Aktualisieren Sie die Seite, um einen aktualisierten Status anzuzeigen.

## <a name="deploy-from-visual-studio-code"></a>Bereitstellen über Visual Studio Code

Azure IoT Edge bietet Vorlagen in Visual Studio Code, mit denen Sie Edgelösungen entwickeln können. Führen Sie die folgenden Schritte aus, um eine neue IoT Edge-Lösung mit einem Blob Storage-Modul zu erstellen und das Bereitstellungsmanifest zu konfigurieren.

1. Wählen Sie **Ansicht** > **Befehlspalette** aus.

1. Geben Sie in der Befehlspalette den folgenden Befehl ein, und führen Sie ihn aus: **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus.

   ![Ausführen einer neuen IoT Edge-Projektmappe](./media/how-to-develop-csharp-module/new-solution.png)

   Folgen Sie den Anweisungen in der Befehlspalette, um Ihre Projektmappe zu erstellen.

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein, oder übernehmen Sie den Standardnamen **EdgeSolution**. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie die Option **Vorhandenes Modul (vollständige Image-URL angeben)** aus. |
   | Provide a module name (Modulname angeben) | Geben Sie einen klein geschriebenen Namen für das Modul ein (beispielsweise **azureblobstorageoniotedge**).<br/><br/>Es ist wichtig, für das Modul „Azure Blob Storage auf IoT Edge“ einen Namen in Kleinbuchstaben zu verwenden. IoT Edge beachtet bei Modulverweisen die Groß-/Kleinschreibung, und das Storage SDK verwendet standardmäßig Kleinbuchstaben. |
   | Provide Docker image for the module (Docker-Image für das Modul angeben) | Geben Sie den Image-URI an: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code verwendet die angegebenen Informationen, erstellt eine IoT Edge-Projektmappe, und lädt diese in einem neuen Fenster. Die Lösungsvorlage erstellt eine Bereitstellungsmanifestvorlage, die Ihr Blob Storage-Modulimage enthält, allerdings müssen Sie die Erstellungsoptionen des Moduls konfigurieren.

1. Öffnen Sie *deployment.template.json* in Ihrem neuen Projektmappenarbeitsbereich, und machen Sie den Abschnitt **Module** ausfindig. Nehmen Sie folgende Konfigurationsänderungen vor:

   1. Löschen Sie das Modul **SimulatedTemperatureSensor**, da es für diese Bereitstellung nicht erforderlich ist.

   1. Kopieren Sie den folgenden Code, und fügen Sie ihn in das Feld `createOptions` ein:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Aktualisieren des Moduls „createOptions“: Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Ersetzen Sie `<your storage account name>` durch einen leicht zu merkenden Namen. Kontonamen sollten 3 bis 24 Zeichen lang sein und nur Kleinbuchstaben und Zahlen enthalten. Leerzeichen sind nicht zulässig.

1. Ersetzen Sie `<your storage account key>` durch einen Base64-Schlüssel mit 64 Bytes. Sie können einen Schlüssel mit Tools wie [GeneratePlus](https://generate.plus/en/base64) generieren. Mit diesen Anmeldeinformationen greifen Sie über andere Module auf Blob Storage zu.

1. Ersetzen Sie `<storage mount>` gemäß Ihrem Containerbetriebssystem. Geben Sie den Namen eines [Volumes](https://docs.docker.com/storage/volumes/) oder den absoluten Pfad zu einem Verzeichnis auf Ihrem IoT Edge-Gerät an, auf bzw. unter dem das Blobmodul Daten speichern soll. Die Speicherbereitstellung ordnet einen Ort auf Ihrem Gerät einem festen Ort im Modul zu.  

     - Für Linux-Container lautet das Format „ **\<your storage path or volume>:/blobroot**“. Beispiel:
         - Verwenden einer [Volumebereitstellung](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - Verwenden einer [Bindungsbereitstellung](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` Führen Sie unbedingt die Schritte zum [Gewähren des Verzeichniszugriffs für den Containerbenutzer](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) aus.
     - Für Windows-Container lautet das Format „ **\<your storage path or volume>:C:/BlobRoot**“. Beispiel:
         - Verwenden einer [Volumebereitstellung](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot`
         - Verwenden einer [Bindungsbereitstellung](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot`
         - Anstelle des lokalen Laufwerks können Sie Ihre SMB-Netzwerkadresse zuordnen. Weitere Informationen finden Sie unter [Verwenden von SMB-Freigaben als lokalen Speicher](how-to-store-data-blob.md#using-smb-share-as-your-local-storage).

     > [!IMPORTANT]
     > Die zweite Hälfte des Werts für die Speicherbereitstellung verweist auf einen bestimmten Ort im Modul „Blob Storage in IoT Edge“ und darf nicht geändert werden. Die Speicherbereitstellung muss immer auf **:/blobroot** (Linux-Container) bzw. auf **:C:/BlobRoot** (Windows-Container) enden.

1. Konfigurieren Sie [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) und [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) für Ihr Modul, indem Sie der Datei *deployment.template.json* den folgenden JSON-Code hinzufügen. Konfigurieren Sie die jeweilige Eigenschaft mit einem entsprechenden Wert, und speichern Sie die Datei. Wenn Sie den IoT Edge-Simulator verwenden, legen Sie die Werte auf die zugehörigen Umgebungsvariablen für diese Eigenschaften fest, die Sie im Abschnitt „Erläuterung“ von [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) und [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) finden können.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![Festlegen der gewünschten Eigenschaften für azureblobstorageoniotedge: Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Informationen zum Konfigurieren von „deviceToCloudUploadProperties“ und „deviceAutoDeleteProperties“ nach Bereitstellung Ihres Moduls finden Sie unter [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) (Bearbeiten des Modulzwillings). Weitere Informationen zu Containererstellungsoptionen, Neustartrichtlinien und gewünschtem Status finden Sie unter [Gewünschte EdgeAgent-Eigenschaften](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Speichern Sie die Datei *deployment.template.json*.

1. Klicken Sie mit der rechten Maustaste auf **deployment.template.json**, und klicken Sie dann auf **IoT Edge-Bereitstellungsmanifest generieren**.

1. Visual Studio Code erstellt anhand der Informationen, die Sie in der Datei *deployment.template.json* angegeben haben, eine neue Bereitstellungsmanifestdatei. Das Bereitstellungsmanifest wird in einem neuen **config**-Ordner in Ihrem Projektmappenarbeitsbereich erstellt. Sobald Sie über diese Datei verfügen, können Sie die Schritte unter [Bereitstellen von Azure IoT Edge-Modulen über Visual Studio Code](how-to-deploy-modules-vscode.md) oder [Bereitstellen von Azure IoT Edge-Modulen mit der Azure CLI](how-to-deploy-modules-cli.md) durchführen.

## <a name="deploy-multiple-module-instances"></a>Bereitstellen mehrerer Modulinstanzen

Wenn Sie mehrere Instanzen des Moduls „Azure Blob Storage auf IoT Edge“ bereitstellen möchten, müssen Sie einen anderen Speicherpfad angeben und den Wert `HostPort` ändern, an den das Modul gebunden wird. Die Blob Storage-Module machen immer Port 11002 im Container verfügbar, aber Sie können deklarieren, an welchen Port dieser auf dem Host gebunden wird.

Bearbeiten Sie das Feld **Optionen für Containererstellung** (im Azure-Portal) oder **createOptions** (in der Datei *deployment.template.json* in Visual Studio Code), um den Wert `HostPort` zu ändern:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Wenn Sie zusätzliche Blob Storage-Module verbinden, ändern Sie den Endpunkt dahingehend, dass er auf den aktualisierten Hostport zeigt.

## <a name="configure-proxy-support"></a>Konfigurieren der Proxyunterstützung

Wenn Ihre Organisation einen Proxyserver verwendet, müssen Sie die Proxyunterstützung für die Laufzeitmodule „edgeAgent“ und „edgeHub“ konfigurieren. Dieser Prozess besteht aus zwei Aufgaben:

- Konfigurieren Sie die Runtime-Daemons und den IoT Edge-Agent auf dem Gerät.
- Legen Sie die HTTPS_PROXY-Umgebungsvariable für Module in der JSON-Datei des Bereitstellungsmanifests fest.

Dieser Prozess wird in [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md) beschrieben.

Außerdem erfordert ein Blob Storage-Modul die Einstellung „HTTPS_PROXY“ in der Bereitstellungsdatei für das Manifest. Sie können diese Datei direkt bearbeiten oder dazu das Azure-Portal verwenden.

1. Navigieren Sie im Azure-Portal zu Ihrem IoT -Hub, und wählen Sie im Menü auf der linken Seite **IoT Edge** aus.

1. Wählen Sie das Gerät mit dem Modul aus, das Sie konfigurieren möchten.

1. Wählen Sie **Module festlegen** aus.

1. Wählen Sie im Abschnitt **IoT Edge-Module** der Seite das Blob Storage-Modul aus.

1. Wählen Sie auf der Seite **IoT Edge-Modul aktualisieren** die Registerkarte **Umgebungsvariablen** aus.

1. Wählen Sie `HTTPS_PROXY` als **Name** und Ihre Proxy-URL als **Wert** aus.

      ![Screenshot: Bereich „IoT Edge-Modul aktualisieren“, in dem Sie die angegebenen Werte eingeben können](./media/how-to-deploy-blob/https-proxy-config.png)

1. Klicken Sie auf **Aktualisieren** und dann auf **Bewerten + erstellen**.

1. Beachten Sie, dass der Proxy dem Modul im Bereitstellungsmanifest hinzugefügt wird, und wählen Sie **Erstellen** aus.

1. Überprüfen Sie die Einstellung, indem Sie das Modul auf der Seite „Gerätedetails“ auswählen. Wählen Sie dann im unteren Teil der Seite **IoT Edge-Module – Details** die Registerkarte **Umgebungsvariablen** aus.

      ![Screenshot: Registerkarte „Umgebungsvariablen“](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [Azure Blob Storage in IoT Edge](how-to-store-data-blob.md).

Weitere Informationen zur Funktionsweise und Erstellung von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](module-composition.md).
