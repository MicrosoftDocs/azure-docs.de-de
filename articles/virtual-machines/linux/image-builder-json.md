---
title: Erstellen einer Azure Image Builder-Vorlage (Preview)
description: Erfahren Sie, wie Sie eine Vorlage für die Verwendung mit Azure Image Builder erstellen.
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: cf8264cbad3c5c88c58cff3b95cb5c68adf0686c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65538295"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Vorschau: Erstellen einer Azure Image Builder-Vorlage 

Azure Image Builder verwendet eine JSON-Datei, um Informationen an den Image Builder-Dienst zu übermitteln. In diesem Artikel werden die einzelnen Abschnitte der JSON-Datei erläutert, sodass Sie Ihre eigene erstellen können. Vollständige JSON-Beispieldateien finden Sie im [GitHub-Repository für Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Das grundlegende Format der Vorlage:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ und API-Version

`type` ist der Ressourcentyp, der `"Microsoft.VirtualMachineImages/imageTemplates"` entsprechen muss. `apiVersion` ändert sich im Laufe der Zeit, in der sich die API ändert. Für die Preview sollte jedoch `"2019-05-01-preview"` festgelegt sein.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

„Location“ entspricht der Region, in der das benutzerdefinierte Image erstellt wird. Die Image Builder-Preview unterstützt die folgenden Regionen:

- USA (Ost)
- USA (Ost) 2
- USA, Westen-Mitte
- USA (Westen)
- USA, Westen 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>dependsON (Optional)

Mit diesem optionalen Abschnitt kann sichergestellt werden, dass Abhängigkeiten durchgeführt werden, bevor der Vorgang fortgesetzt wird. 

```json
    "dependsOn": [],
```

Weitere Informationen finden Sie unter [Definieren der Reihenfolge für die Bereitstellung von Ressourcen in Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identity
Image Builder unterstützt standardmäßig die Verwendung von Skripts und das Kopieren von Dateien aus mehreren Quellen wie GitHub und Azure Storage. Diese müssen öffentlich zugänglich sein, damit sie verwendet werden können.

Sie können auch eine von Ihnen definierte benutzerseitig zugewiesene Azure-Identität verwenden, um Image Builder den Zugriff auf Azure Storage zu gewähren, sofern die Identität mindestens über die Rolle „Storage-Blobdatenleser“ für das Azure Storage-Konto verfügt. Das heißt, Sie müssen weder den externen Zugriff auf die Speicherblobs noch SAS-Tokens einrichten.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Ein vollständiges Beispiel finden Sie unter [Use an Azure User-Assigned Managed Identity to access files in Azure Storage (Verwenden einer benutzerseitig zugewiesenen verwalteten Azure-Identität für den Zugriff auf Dateien in Azure Storage)](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Die Image Builder-Unterstützung für eine benutzerseitig zugewiesene Identität: •   unterstützt nur eine einzelne Identität •   unterstützt keine benutzerdefinierten Domänennamen

Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Weitere Informationen zur Bereitstellung dieses Features finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Eigenschaften: source

Der Abschnitt `source` enthält Informationen über das Quellimage, das von Image Builder verwendet wird.

Die API erfordert eine SourceType-Eigenschaft, die die Quelle für die Imageerstellung definiert. Derzeit stehen die folgenden drei Typen zur Verfügung:
- ISO: Verwenden Sie diesen Typ, wenn es sich beim Quellimage um ein RHEL-ISO-Image handelt.
- PlatformImage: gibt an, dass es sich beim Quellimage um ein Marketplace-Image handelt.
- ManagedImage: Verwenden Sie diesen Typ, wenn Sie mit einem normal verwalteten Image beginnen.
- SharedImageVersion: Dieser Typ wird verwendet, wenn Sie eine Imageversion aus einem Katalog mit freigegebenen Images als Quellimage verwenden.

### <a name="iso-source"></a>ISO-Quelle

Azure Image Builder unterstützt in der Preview nur die Verwendung veröffentlichter, Binär-DVD-ISO-Dateien von Red Hat Enterprise Linux 7.x. Image Builder unterstützt:
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Öffnen Sie `https://access.redhat.com/downloads`, und wählen Sie dann das Produkt **Red Hat Enterprise Linux** und eine unterstützte Version aus, um die Werte `sourceURI` und `sha256Checksum` abzurufen. 

Sie müssen den Link für die Binär-DVD von Red Hat Enterprise Linux 7.x und die Prüfsumme aus der Liste der **Installer und Images für Red Hat Enterprise Linux-Server** kopieren.

> [!NOTE]
> Die Zugriffstoken für die Links werden regelmäßig aktualisiert. Sie müssen also jedes Mal prüfen, ob die RH-Linkadresse geändert wurde, wenn Sie eine Vorlage übermitteln möchten.
 
### <a name="platformimage-source"></a>PlatformImage-Quelle 
Azure Image Builder unterstützt die folgenden Azure Marketplace-Images:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Die Eigenschaften hier entsprechen denen, die zum Erstellen der VM verwendet wurden. Führen Sie den folgenden Befehl in der Azure CLI aus, um die Eigenschaften abzurufen: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Die Eigenschaft für die Version kann nicht „latest“ sein, Sie müssen den obigen Befehl verwenden, um eine Versionsnummer abzurufen. 

### <a name="managedimage-source"></a>ManagedImage-Quelle

Hiermit wird ein vorhandenes verwaltetes Image einer generalisierten VHD oder VM als Quellimage festgelegt. Das verwaltete Quellimage muss einem unterstützten Betriebssystem entsprechen und sich in derselben Region wie Ihre Azure Image Builder-Vorlage befinden. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` sollte dem ResourceId-Wert des verwalteten Images entsprechen. Verwenden Sie den Befehl `az image list`, um die verfügbaren Images aufzulisten.


### <a name="sharedimageversion-source"></a>SharedImageVersion-Quelle
Hiermit wird eine vorhandene Imageversion aus einem Katalog mit freigegebenen Images als Quellimage festgelegt. Die Imageversion muss einem unterstützten Betriebssystem entsprechen, und das Image muss in dieselbe Region wie Ihre Azure Image Builder-Vorlage repliziert werden. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` sollte dem ResourceId-Wert der Imageversion entsprechen. Verwenden Sie den Befehl [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list), um die Imageversionen aufzulisten.

## <a name="properties-customize"></a>Eigenschaften: customize


Image Builder unterstützt mehrere „Anpassungen“. Anpassungen sind Funktionen, die zum Anpassen Ihres Images verwendet werden. Dies umfasst beispielsweise das Ausführen von Skripts oder Neustarten von Servern. 

Folgendes ist bei Verwendung von `customize` zu beachten: 
- Sie können mehrere Anpassungen verwenden, die jedoch eine eindeutige `name`-Eigenschaft aufweisen müssen.
- Anpassungen werden nach der in der Vorlage festgelegten Reihenfolge ausgeführt.
- Wenn eine Anpassung fehlschlägt, schlägt die gesamte Anpassungskomponente fehl, und ein Fehler wird zurückgegeben.
- Beachten Sie, wie viel Zeit die Erstellung Ihres Images erfordert, und passen Sie die Eigenschaft „buildTimeoutInMinutes“ so an, dass ausreichend Zeit für die Imageerstellung zur Verfügung steht.
- Es wird dringend empfohlen, dass Sie das Skript gründlich testen, bevor Sie es in einer Vorlage verwenden. Das Debuggen des Skripts ist in Ihrer eigenen VM einfacher.
- Fügen Sie keine sensiblen Daten in die Skripts ein. 
- Die Speicherorte für die Skripts müssen öffentlich zugänglich sein, es sei denn, Sie verwenden die [verwaltete Dienstidentität](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Der Abschnitt „customize“ ist ein Array. Azure Image Builder durchläuft die Anpassungen in sequenzieller Reihenfolge. Wenn ein Fehler bei einer beliebigen Anpassung auftritt, schlägt der gesamte Buildprozess fehl. 
 
 
### <a name="shell-customizer"></a>Shellanpassung

Die Shellanpassung unterstützt das Ausführen von Shellskripts. Diese müssen öffentlich zugänglich sein, damit Image Builder auf diese zugreifen kann.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Betriebssystemunterstützung: Linux 
 
Anpassungseigenschaften:

- **type:** Shell 
- **name:** Name für die Nachverfolgung der Anpassung 
- **scriptUri:** URI für den Speicherort der Datei 
- **inline:** Array von Shellbefehlen, die durch Kommas getrennt sind
 
> [!NOTE]
> Wenn Sie die Shellanpassung mit einer RHEL-ISO-Quelle ausführen, müssen Sie sicherstellen, dass Ihre erste Anpassungsshell die Registrierung bei einem Red Hat-Berechtigungsserver durchführt, bevor Anpassungen vorgenommen werden. Sobald die Anpassung abgeschlossen ist, sollte die Registrierung des Skripts beim Berechtigungsserver aufgehoben werden.

### <a name="windows-restart-customizer"></a>Windows-Neustartanpassung 
Mithilfe der Neustartanpassung können Sie eine Windows-VMM neu starten und darauf warten, dass sie wieder online geschaltet wird. Dies ermöglicht die Installation von Software, für die ein Neustart erforderlich ist.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Betriebssystemunterstützung: Windows
 
Anpassungseigenschaften:
- **Typ**: WindowsRestart
- **restartCommand:** Hiermit wird der Befehl zum Ausführen des Neustarts festgelegt (Optional). Der Standardwert lautet `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand:** Befehl zur Überprüfung, ob der Neustart erfolgreich war (Optional). 
- **restartTimeout:** Zeichenfolge von Größe und Einheit des Timeouts für den Neustart, z. B. `5m` (5 Minuten) oder `2h` (2 Stunden). Der Standardwert lautet: „5m“


### <a name="powershell-customizer"></a>PowerShell-Anpassung 
Die Shellanpassung unterstützt das Ausführen von PowerShell-Skripts und Inlinebefehlen. Diese Skripts müssen öffentlich zugänglich sein, damit Image Builder auf diese zugreifen kann.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Betriebssystemunterstützung: Windows und Linux

Anpassungseigenschaften:

- **type:** PowerShell
- **scriptUri:** URI für den Speicherort der PowerShell-Skriptdatei 
- **inline:** Durch Kommas getrennte Inlinebefehle, die ausgeführt werden sollen
- **valid_exit_codes:** gültige Codes, die vom Skript oder Inlinebefehl zurückgegeben werden können, um die Meldung eines Fehlers im Skript oder Inlinebefehl zu umgehen (Optional)

### <a name="file-customizer"></a>Dateianpassung

Mithilfe der Dateianpassung kann Image Builder eine Datei aus GitHub oder Azure Storage herunterladen. Wenn Sie über eine Buildpipeline für Images verfügen, die Buildartefakte verwendet, können Sie die Dateianpassung für den Download aus der Buildfreigabe konfigurieren und die Artefakte in das Image verschieben.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Betriebssystemunterstützung: Linux und Windows 

Dateianpassungseigenschaften:

- **sourceUri:** ein Speicherendpunkt, auf den zugegriffen werden kann; hier kann GitHub oder Azure Storage verwendet werden. Sie können nur eine Datei herunterladen, kein ganzes Verzeichnis. Wenn Sie ein Verzeichnis herunterladen müssen, verwenden Sie eine komprimierte Datei, und dekomprimieren Sie diese dann mithilfe der Shell- oder PowerShell-Anpassungen. 
- **destination:** dies ist der vollständige Zielpfad mit dem Dateinamen. Alle referenzierten Pfade und Unterverzeichnisse müssen vorhanden sein. Verwenden Sie die Shell- oder PowerShell-Anpassungen, um diese im Voraus einzurichten. Sie können die Skriptanpassungen zum Erstellen des Pfads verwenden. 

Diese Vorgehensweise wird von Windows-Verzeichnissen und Linux-Pfaden unterstützt, jedoch gibt es einige Unterschiede: 
- Linux-Betriebssysteme: Image Builder kann nur in den Pfad „/tmp“ schreiben.
- Windows: Es besteht keine Pfadeinschränkung, jedoch muss der Pfad vorhanden sein.
 
 
Wenn beim Herunterladen der Datei oder beim Platzieren der Datei im festgelegten Verzeichnis ein Fehler auftritt, schlägt der Anpassungsschritt fehl. Dies wird im Protokoll „customization.log“ dokumentiert.

Dateien in der Dateianpassung können mithilfe der [verwalteten Dienstidentität](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage) aus Azure Storage heruntergeladen werden.

### <a name="generalize"></a>Generalize 
Azure Image Builder führt außerdem standardmäßig Code zum „Aufheben der Bereitstellung“ nach jeder Imageanpassungsphase aus, um das Image zu „generalisieren“. Das Generalisieren ist ein Prozess, bei dem das Image so eingerichtet wird, dass es für die Erstellung mehrerer VMs wiederverwendet werden kann. Azure Image Builder verwendet Sysprep für Windows-VMs. Für Linux führt Azure Image Builder „waagent -deprovision“ aus. 

Die Befehle, die Image Builder für die Generalisierung verwendet, eignen sich möglicherweise nicht für jede Situation, weshalb Azure Image Builder die Anpassung der Befehle bei Bedarf zulässt. 

Wenn Sie vorhandene Anpassungen migrieren und verschiedene Sysprep- oder waagent-Befehle verwenden, können Sie die allgemeinen Image Builder-Befehle verwenden. Und wenn die VM-Erstellung fehlschlägt, können Sie Ihre eigenen Sysprep- oder waagent-Befehle verwenden.

Wenn Azure Image Builder erfolgreich ein benutzerdefiniertes Windows-Image erstellt, Sie mit dem Image eine VM erstellen und die VM-Erstellung dann fehlschlägt bzw. nicht erfolgreich durchgeführt wird, müssen Sie die Dokumentation zu Windows Server-Sysprep zu Rate ziehen oder eine Supportanfrage beim Supportteam für den Kundendienst für Windows Server-Sysprep eröffnen, um Unterstützung bei der Problembehandlung und Tipps zur ordnungsgemäßen Nutzung von Sysprep zu erhalten.


#### <a name="default-sysprep-command"></a>Sysprep-Standardbefehl
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>deprovision-Standardbefehl für Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Überschreiben der Befehle
Verwenden Sie zum Überschreiben der Befehle die Shell- oder PowerShell-Skriptbereitstellungen, um die Befehlsdateien mit dem gleichen Dateinamen zu erstellen, und fügen Sie sie in die richtigen Verzeichnisse ein:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Diese Befehle werden von Image Builder gelesen und in den AIB-Protokollen („customization.log“) dokumentiert. Auf der Seite zur [Problembehandlung](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) erfahren Sie, wie Sie Protokolle erfassen.
 
## <a name="properties-distribute"></a>Eigenschaften: distribute

Azure Image Builder unterstützt drei Verteilungsziele: 

- **managedImage:** ein verwaltetes Image
- **sharedImage:** Katalog mit freigegebenen Images
- **VHD:** VHD in einem Speicherkonto

Sie können ein Image in einer Konfiguration an beide Zieltypen verteilen. Informationen dazu finden Sie in den [Beispielen](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Da Sie mehrere Verteilungsziele verwenden können, verwaltet Image Builder für jedes Verteilungsziel einen Zustand, auf den durch Abfragen von `runOutputName` zugegriffen werden kann.  `runOutputName` ist ein Objekt, das Sie nach der Verteilung abfragen können, um Informationen über die Verteilung abzurufen. Sie können beispielsweise den Speicherort der VHD oder die Regionen abfragen, in denen die Imageversion repliziert wurde. Dies ist eine Eigenschaft aller Verteilungsziele. `runOutputName` muss für jedes Verteilungsziel eindeutig sein.
 
### <a name="distribute-managedimage"></a>Distribute: managedImage

Die Imageausgabe ist eine verwaltete Imageressource.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Verteilungseigenschaften:
- **type:** managedImage 
- **imageId:** die Ressourcen-ID des Zielimages; das folgende Format wird erwartet: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **location:** der Speicherort des verwalteten Images.  
- **runOutputName:** eindeutiger Name zur Identifikation der Verteilung.  
- **artifactTags:** optionale vom Benutzer angegebene Schlüssel-Wert-Paar-Tags.
 
 
> [!NOTE]
> Die Zielressourcengruppe muss vorhanden sein.
> Wenn Sie ein Image in einer anderen Region verteilt werden soll, steigt auch die erforderliche Bereitstellungszeit. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
Der Azure-Katalog mit freigegebenen Images ist ein neuer Dienst für die Verwaltung von Images, mit dem die Imagereplikation in Regionen, die Versionskontrolle und die Freigabe benutzerdefinierter Images verwaltet werden können. Azure Image Builder unterstützt die Verteilung mit diesem Dienst. Sie können Images also in Regionen verteilen, die vom Katalog mit freigegebenen Images unterstützt werden. 
 
Ein Katalog mit freigegebenen Images besteht aus: 
 
- Katalog: Container für mehrere freigegebene Images. Ein Katalog wird in einer Region bereitgestellt.
- Imagedefinitionen: eine konzeptionelle Gruppierung von Images. 
- Imageversionen: ein Imagetyp für die Bereitstellung einer VM oder Skalierungsgruppe. Imageversionen können in andere Regionen repliziert werden, in denen VMs bereitgestellt werden müssen.
 
Bevor Sie ein Image an den Imagekatalog verteilen können, müssen Sie einen Katalog und eine Imagedefinition erstellen. Informationen dazu finden Sie unter [Erstellen eines Katalogs mit freigegebenen Images mit der Azure-Befehlszeilenschnittstelle](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Verteilungseigenschaften für Kataloge mit freigegebenen Images:

- **type:** sharedImage  
- **galleryImageId:** ID des Katalogs mit freigegebenen Images. Das Format lautet: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName:** eindeutiger Name zur Identifikation der Verteilung.  
- **artifactTags:** optionale vom Benutzer angegebene Schlüssel-Wert-Paar-Tags.
- **replicationRegions:** Array von Regionen für die Replikation. In einer der hier enthaltenen Regionen muss der Katalog bereitgestellt werden.
 
> [!NOTE]
> Sie können Azure Image Builder in einer Region verwenden, in der sich der Katalog nicht befindet, jedoch muss der Azure Image Builder-Dienst das Image zwischen den Rechenzentren übertragen, was länger dauert. Image Builder versieht das Image automatisch mit einer Version, basierend auf einem monotonen Integer, den Sie derzeit nicht festlegen können. 

### <a name="distribute-vhd"></a>Distribute: VHD  
Sie können die Ausgabe in einer VHD erstellen. Anschließend können Sie die VHD kopieren und für die Veröffentlichung im Azure Marketplace oder mit Azure Stack verwenden.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Betriebssystemunterstützung: Windows und Linux

VHD-Verteilungsparameter:

- **type:** VHD
- **runOutputName:** eindeutiger Name zur Identifikation der Verteilung.  
- **tags:** optionale vom Benutzer angegebene Schlüssel-Wert-Paar-Tags.
 
Azure Image Builder ermöglicht das Festlegen von Speicherkontostandorten durch Benutzer nicht, Sie können jedoch den Status von `runOutputs` abfragen, um den Standort abzurufen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Kopieren Sie die VHD so bald wie möglich an einen anderen Speicherort, sobald diese erstellt wurde. Die VHD wird in einem Speicherkonto in der temporären Ressourcengruppe gespeichert, die erstellt wird, wenn die Imagevorlage an den Azure Image Builder-Dienst übermittelt wird. Wenn Sie die Imagevorlage löschen, wird auch die VHD gelöscht. 
 
## <a name="next-steps"></a>Nächste Schritte

JSON-Beispieldateien für verschiedene Szenarios finden Sie im [GitHub-Repository für Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
