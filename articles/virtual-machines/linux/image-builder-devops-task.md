---
title: DevOps-Task des Azure Image Builder-Diensts
description: Mit diesem Azure DevOps-Task zum Einfügen von Buildartefakten in ein VM-Image können Sie Ihre Anwendung und Ihr Betriebssystem installieren und konfigurieren.
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: a3016900b6265bfd56ad1a5a71f70efc01181af5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499253"
---
# <a name="azure-image-builder-service-devops-task"></a>DevOps-Task des Azure Image Builder-Diensts

In diesem Artikel erfahren Sie, wie Sie mit einem Azure DevOps-Task zum Einfügen von Buildartefakten in ein VM-Image Ihre Anwendung und Ihr Betriebssystem installieren und konfigurieren können.

## <a name="devops-task-versions"></a>DevOps-Taskversionen
In Azure VM Image Builder (AIB) gibt es zwei DevOps-Tasks:

* Mithilfe des [„stabilen“ AIB-Tasks](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder) lassen sich die neuesten Updates und Features aufspielen. Kunden erhalten die Möglichkeit, diese zu testen, bevor sie etwa eine Woche später als „stabiler“ Task eingestuft werden. 


* Mithilfe des [„instabilen“ AIB-Tasks](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary) lassen sich die neuesten Updates und Features aufspielen. Kunden erhalten die Möglichkeit, diese zu testen, bevor sie als „stabiler“ Task eingestuft werden. Wenn es keine gemeldeten Probleme gibt und die Telemetrie keine Probleme aufweist, wird der Taskcode etwa eine Woche später als „stabil“ eingestuft. 

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie den [stabilen DevOps-Task aus Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* Sie müssen über ein VSTS-DevOps-Konto verfügen und eine Buildpipeline erstellt haben.
* Registrieren Sie sich, und aktivieren Sie die erforderlichen Image Builder-Features in dem von den Pipelines verwendeten Abonnement:
    * [Azure PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Azure CLI](../windows/image-builder.md#register-the-features)
    
* Erstellen Sie ein Azure Storage-Standard-Konto in der Ressourcengruppe des Quellimages. Sie können andere Ressourcengruppen/Speicherkonten verwenden. Das Speicherkonto wird verwendet, um die Buildartefakte von der DevOps-Aufgabe zum Image zu übertragen.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Hinzufügen eines Tasks zur Releasepipeline

Wählen Sie **Releasepipeline** > **Bearbeiten** aus.

Wählen Sie im Benutzer-Agent die Option *+* zum Hinzufügen aus, und suchen Sie dann nach **Image Builder**. Wählen Sie **Hinzufügen**.

Legen Sie die folgenden Taskeigenschaften fest:

### <a name="azure-subscription"></a>Azure-Abonnement

Wählen Sie aus dem Dropdownmenü aus, für welches Abonnement Sie Image Builder ausführen möchten. Verwenden Sie dasselbe Abonnement dort, wo sich Ihre Quellimages befinden und wo die Images verteilt werden sollen. Sie müssen dem Image Builder-Mitwirkenden den Zugriff auf das Abonnement oder die Ressourcengruppe gewähren.

### <a name="resource-group"></a>Ressourcengruppe

Verwenden Sie die Ressourcengruppe, in der das Artefakt der temporären Imagevorlage gespeichert wird. Beim Erstellen eines Vorlagenartefakts wird eine zusätzliche temporäre Image Builder-Ressourcengruppe `IT_<DestinationResourceGroup>_<TemplateName>_guid` erstellt. Die temporäre Ressourcengruppe speichert die Imagemetadaten, wie zu. B. Skripts. Nach Abschluss des Tasks werden das Imagevorlagenartefakt und die temporäre Image Builder-Ressourcengruppe gelöscht.
 
### <a name="location"></a>Standort

Der Standort entspricht der Region, in der Image Builder ausgeführt wird. Es wird nur eine festgelegte Anzahl von [Regionen](../windows/image-builder-overview.md#regions) unterstützt. Die Quellimages müssen an diesem Standort vorhanden sein. Wenn Sie zum Beispiel Shared Image Gallery verwenden, muss in der Region ein Replikat vorhanden sein.

### <a name="managed-identity-required"></a>Verwaltete Identität (erforderlich)
Image Builder erfordert eine verwaltete Identität, die zum Lesen von benutzerdefinierten Quellimages, zum Herstellen einer Verbindung mit Azure Storage und zum Erstellen von benutzerdefinierten Images verwendet wird. Ausführlichere Informationen finden Sie [hier](./image-builder-overview.md#permissions).

### <a name="vnet-support"></a>VNET-Unterstützung

Derzeit wird die Angabe eines vorhandenen Subnetzes von dem DevOps-Task nicht unterstützt. Wenn Sie jedoch ein vorhandenes VNET verwenden möchten, können Sie eine ARM-Vorlage mit einer darin geschachtelten Image Builder-Vorlage verwenden. Weitere Informationen dazu finden Sie in den Windows Image Builder-Vorlagebeispielen, oder verwenden Sie alternativ [Azure AIB PowerShell](../windows/image-builder-powershell.md).

### <a name="source"></a>Quelle

Die Quellimages müssen von den unterstützten Image Builder-Betriebssystemen stammen. Sie können vorhandene benutzerdefinierte Images in derselben Region auswählen, von der aus Image Builder ausgeführt wird:
* Verwaltetes Image: Sie müssen die Ressourcen-ID übergeben, z. B.:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure Shared Image Gallery: Sie müssen die Ressourcen-ID der Imageversion übergeben, z. B.:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Wenn Sie die neueste Version von Shared Image Gallery benötigen, können Sie einen Azure PowerShell- oder Azure CLI-Task ausführen, bevor dieser die neueste Version abruft und eine DevOps-Variable festlegt. Verwenden Sie die Variable in dem DevOps-Task von Azure VM Image Builder. Weitere Informationen finden Sie in den [Beispielen](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery).

* (Marketplace) Basisimage: Es gibt eine Dropdownliste mit beliebten Images, welche stets die „neueste“ Version der unterstützten Betriebssysteme verwenden. 

    Wenn das Basisimage nicht in der Liste enthalten ist, können Sie das genaue Image mit `Publisher:Offer:Sku` angeben.

    Basisimageversion (optional): Sie können die Version des zu verwendenden Images angeben. Der Standardwert ist `latest`.

### <a name="customize"></a>Anpassen

#### <a name="provisioner"></a>Bereitstellung

Anfänglich werden zwei Anpassungen unterstützt: **Shell** und **PowerShell**. Nur Inlinebefehle werden unterstützt. Wenn Sie Skripts herunterladen möchten, können Sie hierfür Inlinebefehle übergeben.

Wählen Sie passend zu Ihrem Betriebssystem PowerShell bzw. Shell aus.

#### <a name="windows-update-task"></a>Windows Update-Task

Nur für Windows führt der Task Windows Update am Ende der Anpassungen aus. Dabei werden die erforderlichen Neustarts ausgeführt.

Die folgende Windows Update-Konfiguration wird ausgeführt:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Dabei werden wichtige und empfohlene Windows-Updates installiert, die keine Vorschauversion sind.

#### <a name="handling-reboots"></a>Ausführen von Neustarts
Derzeit bietet der DevOps-Task keine Unterstützung für den Neustart von Windows-Builds. Wenn Sie den Neustart mit PowerShell-Code versuchen, tritt ein Buildfehler auf. Linux-Builds können Sie jedoch mithilfe von Code neu starten.

#### <a name="build-path"></a>Buildpfad

Der Task ist so konzipiert, dass sie Releaseartefakte des DevOps-Builds in das Image einfügen kann. Damit dies funktioniert, müssen Sie eine Buildpipeline einrichten. Bei der Einrichtung der Releasepipeline müssen Sie das Repository der Buildartefakte hinzufügen.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Auswahl zum Hinzufügen eines Artefakts in der Releasepipeline":::

Wählen Sie die Schaltfläche **Buildpfad** aus, um den Buildordner auszuwählen, den Sie im Image platzieren möchten. Alle Dateien und Verzeichnisse innerhalb des Image Builder-Tasks werden kopiert. Wenn das Image erstellt wird, werden die Dateien und Verzeichnisse von Image Builder abhängig vom Betriebssystem in unterschiedlichen Pfaden bereitgestellt.

> [!IMPORTANT]
> Beim Hinzufügen eines Repositoryartefakts sehen Sie möglicherweise, dass dem Verzeichnis ein Unterstrich *_* vorangestellt ist. Der Unterstrich kann Probleme mit Inlinebefehlen verursachen. Verwenden Sie in den Befehlen die entsprechenden Anführungszeichen.
> 

Das folgende Beispiel veranschaulicht die Funktionsweise:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Eine Verzeichnisstruktur mit einer Hierarchie":::


* Windows: Die Dateien sind unter `C:\` gespeichert. Es wird ein Verzeichnis mit dem Namen `buildArtifacts` erstellt, das das `webapp`-Verzeichnis enthält.

* Linux: Die Dateien sind unter `/tmp` gespeichert. Es wird das `webapp`-Verzeichnis erstellt, in dem sich alle Dateien und Verzeichnisse befinden. Sie müssen die Dateien aus diesem Verzeichnis verschieben. Andernfalls werden sie gelöscht, da sie sich im temporären Verzeichnis befinden.

#### <a name="inline-customization-script"></a>Inline-Anpassungsskript

* Windows: Sie können PowerShell-Inlinebefehle, die durch Kommas getrennt sind, eingeben. Wenn Sie in Ihrem Buildverzeichnis ein Skript ausführen möchten, können Sie Folgendes verwenden:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux: Die Buildartefakte sind im `/tmp`-Verzeichnis abgelegt. Bei vielen Linux-Betriebssystemen wird der Inhalt des /tmp-Verzeichnisses jedoch bei einem Neustart gelöscht. Wenn Sie möchten, dass die Artefakte im Image vorhanden sind, müssen Sie ein weiteres Verzeichnis erstellen, die Artefakte kopieren und in das neue Verzeichnis einfügen.  Beispiel:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Wenn Sie einfach das /tmp-Verzeichnis verwenden möchten, können Sie das Skript mit dem untenstehenden Code ausführen.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Was geschieht mit den Buildartefakten nach der Imageerstellung?

> [!NOTE]
> Die Buildartefakte werden von Image Builder nicht automatisch entfernt. Es wird dringend empfohlen, dass Sie immer über Code zum Entfernen der Buildartefakte verfügen.
> 

* Windows: Image Builder stellt die Dateien im `c:\buildArtifacts`-Verzeichnis bereit. Das Verzeichnis wird persistent gespeichert. Sie müssen das Verzeichnis entfernen. Sie können es in dem von Ihnen ausgeführten Skript entfernen. Beispiel:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux: Die Buildartefakte werden im `/tmp`-Verzeichnis abgelegt. Bei vielen Linux-Betriebssystemen wird der Inhalt des `/tmp`-Verzeichnisses jedoch bei einem Neustart gelöscht. Es wird empfohlen, dass Sie über Code zum Entfernen der Inhalte verfügen, damit Sie sich nicht auf das Betriebssystem verlassen müssen, um den Inhalt zu entfernen. Beispiel:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Gesamtdauer der Imageerstellung

Die Gesamtdauer kann in dem DevOps-Pipelinetask noch nicht geändert werden. Diese verwendet den Standardwert von 240 Minuten. Wenn Sie den Parameter [buildTimeoutInMinutes](./image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-buildtimeoutinminutes) erhöhen möchten, können Sie einen Azure CLI-Task in der Releasepipeline verwenden. Konfigurieren Sie den Task, um eine Vorlage zu kopieren und zu übermitteln. Ein Beispiel finden Sie in dieser [Lösung](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder), oder verwenden Sie Azure PowerShell.


#### <a name="storage-account"></a>Speicherkonto

Wählen Sie das Speicherkonto aus, das Sie in den Voraussetzungen erstellt haben. Wenn nicht in der Liste angezeigt wird, hat Image Builder dafür keine Zugriffsberechtigungen.

Wenn der Build gestartet wird, erstellt Image Builder einen Container namens `imagebuilder-vststask`. Der Container befindet sich am selben Speicherort wie die Buildartefakte aus dem Repository.

> [!NOTE]
> Sie müssen das Speicherkonto oder den Container nach jedem Build manuell löschen.
>

### <a name="distribute"></a>Verteilen

Es werden drei Verteilertypen unterstützt.

#### <a name="managed-image"></a>Verwaltetes Image

* ResourceID:
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Standorte

#### <a name="azure-shared-image-gallery"></a>Azure Shared Image Gallery

Die Shared Image Gallery-Instanz **muss** bereits vorhanden sein.

* ResourceID: 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regionen: Liste der Regionen, durch Kommas getrennt. Beispiele: westus, eastus, centralus

#### <a name="vhd"></a>VHD

An diese können Sie keine Werte übergeben. Image Builder gibt die VHD an die temporäre Image Builder-Ressourcengruppe `IT_<DestinationResourceGroup>_<TemplateName>` im Container *vhds* aus. Wenn Sie den Releasebuild starten, gibt Image Builder Protokolle aus. Nach Abschluss des Vorgangs wird die VHD-URL ausgegeben.

### <a name="optional-settings"></a>Optionale Einstellungen

* [VM-Größe](image-builder-json.md#vmprofile): Sie können die VM-Größe aus der Voreinstellung *Standard_D1_v2* außer Kraft setzen. Möglicherweise möchten Sie diese außer Kraft setzen, um die Gesamtanpassungszeit zu reduzieren, oder weil Sie die Images erstellen möchten, die von bestimmten VM-Größen wie GPU/HPC usw. abhängen.

## <a name="how-it-works"></a>Funktionsweise

Bei der Releaseerstellung erstellt der Task einen Container im Speicherkonto mit dem Namen *imagebuilder-vststask*. Dabei werden Ihre Buildartefakte gezippt und hochgeladen, und es wird ein SAS-Token für die ZIP-Datei erstellt.

Der Task verwendet die Eigenschaften, die an den Task übermittelt werden, um das Image Builder-Vorlagenartefakt zu erstellen. Dieser Task führt Folgendes aus:
* Sie lädt die Buildartefakt-ZIP-Datei und alle damit verbundenen Skripts herunter. Die Dateien werden in einem Speicherkonto in der temporären Image Builder-Ressourcengruppe `IT_<DestinationResourceGroup>_<TemplateName>` gespeichert.
* Erstellt eine Vorlage mit dem Präfix *t_* und einer 10-stelligen monotonen Ganzzahl. Die Vorlage wird in der Ressourcengruppe gespeichert, die Sie ausgewählt haben. Die Vorlage ist für die Dauer des Builds in der Ressourcengruppe vorhanden. 

Beispielausgabe:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Wenn der Imagebuild gestartet wird, wird in den Releaseprotokollen der Status „wird ausgeführt“ angezeigt:

```text
starting run template...
```

Nach Abschluss des Imagebuilds erhalten Sie eine Ausgabe ähnlich der folgenden:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

Die Imagevorlage und `IT_<DestinationResourceGroup>_<TemplateName>` werden gelöscht.

Sie können die VSTS-Variable „$(imageUri)“ für den nächsten Task verwenden oder einfach den Wert verwenden und eine VM erstellen.

## <a name="output-devops-variables"></a>Ausgabe von DevOps-Variablen

Herausgeber/Angebot/SKU/Version des Marketplace-Quellimages:
* $(pirPublisher)
* $(pirOffer)
* $(pirSku)
* $(pirVersion)

Image-URI: die Ressourcen-ID des verteilten Images:
* $(imageUri)

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Kann ich eine vorhandene Imagevorlage verwenden, die ich außerhalb von DevOps bereits erstellt habe?

Derzeit leider nicht.

### <a name="can-i-specify-the-image-template-name"></a>Kann ich den Namen der Imagevorlage festlegen?

Nein. Ein eindeutiger Vorlagenname wird verwendet und anschließend gelöscht.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Image Builder ist fehlgeschlagen. Wie behebe ich dieses Problem?

Bei einem Buildfehler wird die Stagingressourcengruppe nicht von dem DevOps-Task gelöscht. Sie können auf die Stagingressourcengruppe zugreifen, die das Buildanpassungsprotokoll enthält.

Im DevOps-Protokoll wird für die VM-Task des Image Builder ein Fehler und der Speicherort der Datei „customization.log“ angezeigt. Beispiel:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Beispiel für die Meldung eines Fehlers des DevOps-Tasks":::

Weitere Informationen zur Problembehandlung finden Sie unter [Problembehandlung für den Azure Image Builder-Dienst](image-builder-troubleshoot.md). 

Nachdem Sie den Fehler untersucht haben, können Sie die Stagingressourcengruppe löschen. Löschen Sie zunächst das Ressourcenartefakt der Imagevorlage. Dem Artefakt ist ein *t_* vorangestellt, und es kann im Buildprotokoll des DevOps-Tasks gefunden werden:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

Das Ressourcenartefakt der Imagevorlage befindet sich in der anfangs in dem Task festgelegten Ressourcengruppe. Wenn Sie das Problem behoben haben, löschen Sie das Artefakt. Wenn Sie es über das Azure-Portal innerhalb der Ressourcengruppe löschen, wählen Sie **Show Hidden Types** (Ausgeblendete Typen anzeigen), um das Artefakt anzuzeigen.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über Azure Image Builder](image-builder-overview.md).