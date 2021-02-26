---
title: Bereitstellen von Vorlagen mithilfe des Azure-Bereitstellungs-Managers
description: Hier erfahren Sie, wie Sie Resource Manager-Vorlagen mit dem Azure-Bereitstellungs-Manager verwenden, um Azure-Ressourcen bereitzustellen.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627582"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Tutorial: Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen (Public Preview)

Hier erfahren Sie, wie Sie Ihre Anwendungen mit dem [Azure-Bereitstellungs-Manager](./deployment-manager-overview.md) über mehrere Regionen hinweg bereitstellen. Wenn Sie einen schnelleren Ansatz bevorzugen, erstellen Sie mit dem [Schnellstart für den Azure-Bereitstellungs-Manager](https://github.com/Azure-Samples/adm-quickstart) die erforderlichen Konfigurationen in Ihrem Abonnement und passt die Artefakte an, um eine Anwendung über mehrere Regionen hinweg bereitzustellen. Im Schnellstart werden dieselben Aufgaben wie in diesem Tutorial ausgeführt.

Um den Bereitstellungs-Manager verwenden zu können, müssen zwei Vorlagen erstellt werden:

* **Eine Topologievorlage:** Beschreibt, welche Azure-Ressourcen Ihre Anwendungen umfassen und wo sie bereitgestellt werden sollen.
* **Eine Rolloutvorlage:** Beschreibt die Schritte, die beim Bereitstellen Ihrer Anwendungen ausgeführt werden müssen.

> [!IMPORTANT]
> Falls Ihr Abonnement als Canary-Abonnement zum Testen neuer Azure-Features markiert ist, kann der Azure-Bereitstellungs-Manager nur zur Bereitstellung in den Canary-Regionen verwendet werden.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Das Szenario
> * Herunterladen der Tutorialdateien
> * Vorbereiten der Artefakte
> * Erstellen der benutzerdefinierten verwalteten Identität
> * Erstellen der Diensttopologievorlage
> * Erstellen der Rolloutvorlage
> * Bereitstellen der Vorlagen
> * Überprüfen der Bereitstellung
> * Bereitstellen der neueren Version
> * Bereinigen von Ressourcen

Zusätzliche Ressourcen:

* [REST-API-Referenz für den Azure-Bereitstellungs-Manager](/rest/api/deploymentmanager/)
* [Tutorial: Verwenden der Integritätsprüfung im Azure-Bereitstellungs-Manager (Public Preview)](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Etwas Erfahrung mit der Entwicklung von [Azure Resource Manager-Vorlagen](overview.md).
* Azure PowerShell. Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).
* Bereitstellungs-Manager-Cmdlets. Um diese vorab veröffentlichten Cmdlets installieren zu können, benötigen Sie die neueste Version von PowerShellGet. Informationen zum Beziehen der neuesten Version finden Sie unter [Installieren von PowerShellGet](/powershell/scripting/gallery/installing-psget). Schließen Sie nach dem Installieren von PowerShellGet das PowerShell-Fenster. Öffnen Sie ein neues PowerShell-Fenster mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Das Szenario

Die Diensttopologievorlage beschreibt, welche Azure-Ressourcen Ihr Dienst umfasst und wo sie bereitgestellt werden sollen. Die Hierarchie der Diensttopologiedefinition sieht wie folgt aus:

* Diensttopologie
  * Dienste
    * Diensteinheiten

Das folgende Diagramm veranschaulicht die in diesem Tutorial verwendete Diensttopologie:

![Tutorial für den Azure-Bereitstellungs-Manager: Szenario (Diagramm)](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Es sind zwei Dienste zugeordnet: einer am Standort „USA, Westen“ und einer am Standort „USA, Osten“. Jeder Dienst verfügt über zwei Diensteinheiten: eine Front-End-Webanwendung und ein Back-End-Speicherkonto. Die Diensteinheitendefinitionen enthalten Links zu den Vorlagen- und Parameterdateien, mit denen die Webanwendungen und Speicherkonten erstellt werden.

## <a name="download-the-tutorial-files"></a>Herunterladen der Tutorialdateien

1. Laden Sie die [Vorlagen und Artefakte](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) herunter, die in diesem Tutorial verwendet werden.
1. Entzippen Sie die Dateien auf Ihrem lokalen Computer.

Unterhalb des Stammordners befinden sich zwei Ordner:

* _ADMTemplates_: Enthält folgende Bereitstellungs-Manager-Vorlagen:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: Enthält sowohl die Vorlagenartefakte als auch die binären Artefakte. Weitere Informationen finden Sie unter [Vorbereiten der Artefakte](#prepare-the-artifacts).

Es gibt zwei Vorlagensätze: Ein Satz umfasst die Bereitstellungs-Manager-Vorlagen zum Bereitstellen der Diensttopologie und des Rollouts. Der andere Satz wird über die Diensteinheiten aufgerufen, um Webdienste und Speicherkonten zu erstellen.

## <a name="prepare-the-artifacts"></a>Vorbereiten der Artefakte

Der Ordner „ArtifactStore“ aus dem Download enthält zwei Ordner:

![Tutorial für den Azure-Bereitstellungs-Manager: Artefaktquelle (Diagramm)](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Ordner _templates_: Enthält die Vorlagenartefakte. Die Ordner _1.0.0.0_ und _1.0.0.1_ stellen die beiden Versionen der binären Artefakte dar. Jede Version verfügt über einen eigenen Ordner für den jeweiligen Dienst: _ServiceEUS_ (Dienst „USA, Osten“) und _ServiceWUS_ (Dienst „USA, Westen“). Jeder Dienst verfügt über ein Paar aus Vorlagen- und Parameterdateien zum Erstellen eines Speicherkontos sowie über ein weiteres Paar zum Erstellen einer Webanwendung. Die Webanwendungsvorlage ruft ein komprimiertes Paket auf, das die Webanwendungsdateien enthält. Bei der komprimierten Datei handelt es sich um ein binäres Artefakt, das im Ordner „binaries“ gespeichert ist.
* Ordner _binaries_: Enthält die binären Artefakte. Die Ordner _1.0.0.0_ und _1.0.0.1_ stellen die beiden Versionen der binären Artefakte dar. In jeder Version gibt es eine ZIP-Datei zum Erstellen der Webanwendung am Standort „USA, Westen“ sowie eine weitere ZIP-Datei zum Erstellen der Webanwendung am Standort „USA, Osten“.

Die beiden Versionen (1.0.0.0 und 1.0.0.1) dienen zur [Revisionsbereitstellung](#deploy-the-revision). Zwar verfügen sowohl die Vorlagenartefakte als auch die binären Artefakte über zwei Versionen, aber nur die binären Artefakte unterscheiden sich zwischen den beiden Versionen. In der Praxis werden binäre Artefakte häufiger aktualisiert als Vorlagenartefakte.

1. Öffnen Sie _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ in einem Text-Editor. Es handelt sich dabei um eine einfache Vorlage zum Erstellen eines Speicherkontos.
1. Öffnen Sie _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Tutorial für den Azure-Bereitstellungs-Manager: Erstellen einer Webanwendungsvorlage](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Die Vorlage ruft ein Bereitstellungspaket ab, das die Dateien der Webanwendung enthält. In diesem Tutorial enthält das komprimierte Paket nur die Datei _index.html_.
1. Öffnen Sie _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![Tutorial für den Azure-Bereitstellungs-Manager: Erstellen von Webanwendungsvorlagenparametern – containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Der Wert von `deployPackageUri` ist der Pfad des Bereitstellungspakets. Der Parameter enthält eine Variable vom Typ `$containerRoot`. Der Wert von `$containerRoot` wird in der [Rolloutvorlage](#create-the-rollout-template) angegeben, indem der SAS-Speicherort der Artefaktquelle mit dem Artefaktstamm und `deployPackageUri` verkettet wird.
1. Öffnen Sie _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    Der HTML-Code enthält Informationen zum Speicherort und zur Version. Die Binärdatei im Ordner _1.0.0.1_ hat die _Version 1.0.0.1_. Nach Bereitstellung des Diensts können Sie zu diesen Seiten navigieren.
1. Sehen Sie sich weitere Artefaktdateien an. Dadurch können Sie das Szenario besser nachvollziehen.

Vorlagenartefakte werden von der Diensttopologievorlage verwendet, binäre Artefakte von der Rolloutvorlage. Sowohl die Topologievorlage als auch die Rolloutvorlage definiert eine Azure-Ressource vom Typ „Artefaktquelle“. Dabei handelt es sich um eine Ressource, die Resource Manager auf die Vorlagenartefakte und binären Artefakte verweist, die in der Bereitstellung verwendet werden. Zur Vereinfachung des Tutorials wird zum Speichern der Vorlagenartefakte und der binären Artefakte ein einzelnes Speicherkonto verwendet. Beide Artefaktquellen verweisen also auf das gleiche Speicherkonto.

Führen Sie das folgende PowerShell-Skript aus, um eine Ressourcengruppe, einen Speichercontainer und einen Blobcontainer zu erstellen, die heruntergeladenen Dateien hochzuladen und dann ein SAS-Token zu erstellen.

> [!IMPORTANT]
> `projectName` im PowerShell-Skript dient zum Generieren von Namen für die Azure-Dienste, die in diesem Tutorial bereitgestellt werden. Die verschiedenen Azure-Dienste haben jeweils unterschiedliche Namensanforderungen. Um sicherzustellen, dass die Bereitstellung erfolgreich ist, wählen Sie einen Namen mit weniger als 12 Zeichen, der ausschließlich aus Kleinbuchstaben und Zahlen besteht.
> Speichern Sie eine Kopie des Projektnamens. Der Projektname (`projectName`) bleibt während des gesamten Tutorials gleich.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Notieren Sie sich die URL mit dem SAS-Token. Diese URL wird zum Auffüllen eines Felds in den beiden Parameterdateien (Topologieparameterdatei und Rolloutparameterdatei) benötigt.

Öffnen Sie den Container im Azure-Portal, und überprüfen Sie, ob die Ordner _binaries_ und _templates_ sowie die Dateien hochgeladen wurden.

## <a name="create-the-user-assigned-managed-identity"></a>Erstellen der benutzerseitig zugewiesenen verwalteten Identität

Im weiteren Verlauf des Tutorials wird ein Rollout bereitgestellt. Eine benutzerseitig zugewiesene verwaltete Identität ist zum Ausführen der Bereitstellungsaktionen (also etwa zum Bereitstellen der Webanwendungen und des Speicherkontos) erforderlich. Dieser Identität muss Zugriff auf das Azure-Abonnement gewährt werden, für das Sie den Dienst bereitstellen, und sie muss über die nötigen Berechtigungen zur Durchführung der Artefaktbereitstellung verfügen.

Sie müssen eine benutzerseitig zugewiesene verwaltete Identität erstellen und die Zugriffssteuerung für Ihr Abonnement konfigurieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Erstellen Sie eine [benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
1. Klicken Sie im linken Menü des Portals auf **Abonnements**, und wählen Sie anschließend Ihr Abonnement aus.
1. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **Rollenzuweisung hinzufügen** aus.
1. Geben Sie folgende Werte ein bzw. wählen diese aus:

    ![Tutorial für den Azure-Bereitstellungs-Manager: Benutzerseitig zugewiesene verwaltete Identität – Zugriffssteuerung](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rolle**: Gewähren Sie ausreichende Berechtigungen für die Durchführung der Artefaktbereitstellung (Webanwendungen und Speicherkonten). Wählen Sie in diesem Tutorial die Option **Mitwirkender** aus. In der Praxis empfiehlt es sich, die Berechtigungen auf ein Minimum beschränken.
    * **Zugriff zuweisen zu**: Wählen Sie **Benutzerseitig zugewiesene verwaltete Identität** aus.
    * Wählen Sie die benutzerseitig zugewiesene verwaltete Identität aus, die Sie zuvor in diesem Tutorial erstellt haben.
1. Wählen Sie **Speichern** aus.

## <a name="create-the-service-topology-template"></a>Erstellen der Diensttopologievorlage

Öffnen Sie _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>Die Parameter

Die Vorlage enthält folgende Parameter:

* `projectName`: Dieser Name wird zur Erstellung der Namen für die Bereitstellungs-Manager-Ressourcen verwendet. Bei Verwendung von **demo** lautet der Diensttopologiename beispielsweise „**demo** ServiceTopology“. Die Ressourcennamen werden im Abschnitt `variables` der Vorlage definiert.
* `azureResourcelocation`: Zur Vereinfachung des Tutorials verwenden alle Ressourcen diesen Standort, wenn nichts anderes angegeben ist.
* `artifactSourceSASLocation`: Der SAS-URI des Blobcontainers, unter dem die Diensteinheitenvorlage und die Parameterdateien für die Bereitstellung gespeichert sind. Weitere Informationen finden Sie unter [Vorbereiten der Artefakte](#prepare-the-artifacts).
* `templateArtifactRoot`: Der Offsetpfad des Blobcontainers, unter dem die Vorlagen und Parameter gespeichert sind. Standardwert: _templates/1.0.0.0_. Ändern Sie diesen Wert nur, wenn Sie die unter [Vorbereiten der Artefakte](#prepare-the-artifacts) erläuterte Ordnerstruktur ändern möchten. In diesem Tutorial werden relative Pfade verwendet. Der vollständige Pfad ist eine Verkettung aus `artifactSourceSASLocation`, `templateArtifactRoot` und `templateArtifactSourceRelativePath` (oder `parametersArtifactSourceRelativePath`).
* `targetSubscriptionID`: Die Abonnement-ID, unter der die Bereitstellungs-Manager-Ressourcen bereitgestellt und abgerechnet werden. Verwenden Sie in diesem Tutorial Ihre Abonnement-ID.

### <a name="the-variables"></a>Die Variablen

Im Variablenabschnitt werden die Namen der Ressourcen, die Azure-Standorte für die beiden Dienste (`ServiceWUS` und `ServiceEUS`) sowie die Artefaktpfade definiert:

![Tutorial für den Azure-Bereitstellungs-Manager: Variablen der Topologievorlage](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Vergleichen Sie die Artefaktpfade mit der Ordnerstruktur, die Sie in das Speicherkonto hochgeladen haben. Wie Sie sehen, handelt es sich bei den Artefaktpfaden um relative Pfade. Der vollständige Pfad ist eine Verkettung aus `artifactSourceSASLocation`, `templateArtifactRoot` und `templateArtifactSourceRelativePath` (oder `parametersArtifactSourceRelativePath`).

### <a name="the-resources"></a>Die Ressourcen

Auf der Stammebene sind zwei Ressourcen definiert: eine *Artefaktquelle* und eine *Diensttopologie*.

Definition der Artefaktquelle:

![Tutorial für den Azure-Bereitstellungs-Manager: Topologievorlagenressourcen – Artefaktquelle](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Der folgende Screenshot zeigt Teile der Diensttopologie, Dienste und Diensteinheitendefinitionen:

![Tutorial für den Azure-Bereitstellungs-Manager: Topologievorlagenressourcen – Diensttopologie](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Dient dazu, die Artefaktquellressource der Diensttopologieressource zuzuordnen.
* `dependsOn` Alle Diensttopologieressourcen hängen von der Artefaktquellressource ab.
* `artifacts`: Verweist auf die Vorlagenartefakte. Hier werden relative Pfade verwendet. Der vollständige Pfad ist eine Verkettung aus `artifactSourceSASLocation` (in der Artefaktquelle definiert), `artifactRoot` (in der Artefaktquelle definiert) und `templateArtifactSourceRelativePath` (oder `parametersArtifactSourceRelativePath`).

### <a name="topology-parameters-file"></a>Topologieparameterdatei

Sie erstellen eine Parameterdatei, die mit der Topologievorlage verwendet wird.

1. Öffnen Sie _\ADMTemplates\CreateADMServiceTopology.Parameters.json_ in Visual Studio Code oder einem beliebigen Text-Editor.
1. Geben Sie die Parameterwerte ein:

    * `projectName`: Geben Sie eine Zeichenfolge mit vier bis fünf Zeichen ein. Dieser Name dient zur Erstellung eindeutiger Azure-Ressourcennamen.
    * `azureResourceLocation`: Sollten Sie nicht mit Azure-Standorten vertraut sein, verwenden Sie in diesem Tutorial **centralus**.
    * `artifactSourceSASLocation`: Geben Sie den SAS-URI des Stammverzeichnisses (Blobcontainer) ein, unter dem die Diensteinheitenvorlage und die Parameterdateien für die Bereitstellung gespeichert sind.  Weitere Informationen finden Sie unter [Vorbereiten der Artefakte](#prepare-the-artifacts).
    * `templateArtifactRoot`: Verwenden Sie in diesem Tutorial _templates/1.0.0.0_, es sei denn, Sie möchten die Ordnerstruktur der Artefakte ändern.

> [!IMPORTANT]
> In der Topologie- und der Rolloutvorlage werden teilweise die gleichen Parameter verwendet. Diese Parameter müssen die gleichen Werte besitzen. Betroffene Parameter: `projectName`, `azureResourceLocation` und `artifactSourceSASLocation`. (In diesem Tutorial verwenden beide Artefaktquellen das gleiche Speicherkonto.)

## <a name="create-the-rollout-template"></a>Erstellen der Rolloutvorlage

Öffnen Sie _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>Die Parameter

Die Vorlage enthält folgende Parameter:

![Tutorial für den Azure-Bereitstellungs-Manager: Parameter der Rolloutvorlage](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Dieser Name wird zur Erstellung der Namen für die Bereitstellungs-Manager-Ressourcen verwendet. Bei Verwendung von **demo** lautet der Rolloutname beispielsweise „**demo** Rollout“. Die Namen werden im Abschnitt `variables` der Vorlage definiert.
* `azureResourcelocation`: Zur Vereinfachung des Tutorials verwenden alle Bereitstellungs-Manager-Ressourcen diesen Standort, wenn nichts anderes angegeben ist.
* `artifactSourceSASLocation`: Der SAS-URI des Stammverzeichnisses (Blobcontainer), unter dem die Diensteinheitenvorlage und die Parameterdateien für die Bereitstellung gespeichert sind. Weitere Informationen finden Sie unter [Vorbereiten der Artefakte](#prepare-the-artifacts).
* `binaryArtifactRoot`: Der Standardwert lautet _binaries/1.0.0.0_. Ändern Sie diesen Wert nur, wenn Sie die unter [Vorbereiten der Artefakte](#prepare-the-artifacts) erläuterte Ordnerstruktur ändern möchten. In diesem Tutorial werden relative Pfade verwendet. Der vollständige Pfad ist eine Verkettung aus `artifactSourceSASLocation`, `binaryArtifactRoot` und `deployPackageUri` (aus _CreateWebApplicationParameters.json_). Weitere Informationen finden Sie unter [Vorbereiten der Artefakte](#prepare-the-artifacts).
* `managedIdentityID`: Die benutzerseitig zugewiesene verwaltete Identität, die die Bereitstellungsaktionen ausführt. Weitere Informationen finden Sie unter [Erstellen der benutzerseitig zugewiesenen verwalteten Identität](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Die Variablen

Im Abschnitt `variables` sind die Namen der Ressourcen definiert. Vergewissern Sie sich, dass der Diensttopologiename sowie die Dienst- und die Diensteinheitennamen den Namen aus der [Topologievorlage](#create-the-service-topology-template) entsprechen.

![Tutorial für den Azure-Bereitstellungs-Manager: Variablen der Rolloutvorlage](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Die Ressourcen

Auf der Stammebene sind drei Ressourcen definiert: eine Artefaktquelle, ein Schritt und ein Rollout.

Die Artefaktquellendefinition ist mit der Definition in der Topologievorlage identisch. Weitere Informationen finden Sie unter [Erstellen der Diensttopologievorlage](#create-the-service-topology-template).

Der folgende Screenshot zeigt die Definition des Schritts `wait`:

![Tutorial für den Azure-Bereitstellungs-Manager: Ressourcen der Rolloutvorlage – Warteschritt](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Für die Dauer wird der [ISO 8601-Standard](https://en.wikipedia.org/wiki/ISO_8601#Durations) verwendet. **PT1M** (muss in Großbuchstaben angegeben werden) ist ein Beispiel für eine Wartezeit von einer Minute.

Der folgende Screenshot zeigt Teile der Rolloutdefinition:

![Tutorial für den Azure-Bereitstellungs-Manager: Ressourcen der Rolloutvorlage – Rollout](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn` Die Rolloutressource hängt von der Artefaktquellressource sowie von den ggf. definierten Schritten ab.
* `artifactSourceId`: Dient dazu, die Artefaktquellressource der Rolloutressource zuzuordnen.
* `targetServiceTopologyId`: Dient dazu, die Diensttopologieressource der Rolloutressource zuzuordnen.
* `deploymentTargetId`: Die Diensteinheitenressourcen-ID der Diensttopologieressource
* `preDeploymentSteps` und `postDeploymentSteps`: Enthalten die Rolloutschritte. In der Vorlage wird der Schritt `wait` aufgerufen.
* `dependsOnStepGroups`: Konfigurieren Sie die Abhängigkeiten zwischen den Schrittgruppen.

### <a name="rollout-parameters-file"></a>Rolloutparameterdatei

Sie erstellen eine Parameterdatei, die mit der Rolloutvorlage verwendet wird.

1. Öffnen Sie _\ADMTemplates\CreateADMRollout.Parameters.json_ in Visual Studio Code oder einem beliebigen Text-Editor.
1. Geben Sie die Parameterwerte ein:

    * `projectName`: Geben Sie eine Zeichenfolge mit vier bis fünf Zeichen ein. Dieser Name dient zur Erstellung eindeutiger Azure-Ressourcennamen.
    * `azureResourceLocation`: Geben Sie einen Azure-Standort an.
    * `artifactSourceSASLocation`: Geben Sie den SAS-URI des Stammverzeichnisses (Blobcontainer) ein, unter dem die Diensteinheitenvorlage und die Parameterdateien für die Bereitstellung gespeichert sind.  Weitere Informationen finden Sie unter [Vorbereiten der Artefakte](#prepare-the-artifacts).
    * `binaryArtifactRoot`: Verwenden Sie in diesem Tutorial _binaries/1.0.0.0_, es sei denn, Sie möchten die Ordnerstruktur der Artefakte ändern.
    * `managedIdentityID`: Geben Sie die benutzerseitig zugewiesene verwaltete Identität ein. Weitere Informationen finden Sie unter [Erstellen der benutzerseitig zugewiesenen verwalteten Identität](#create-the-user-assigned-managed-identity). Die Syntax ist:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> In der Topologie- und der Rolloutvorlage werden teilweise die gleichen Parameter verwendet. Diese Parameter müssen die gleichen Werte besitzen. Betroffene Parameter: `projectName`, `azureResourceLocation` und `artifactSourceSASLocation`. (In diesem Tutorial verwenden beide Artefaktquellen das gleiche Speicherkonto.)

## <a name="deploy-the-templates"></a>Bereitstellen der Vorlagen

Die Vorlagen können mithilfe von Azure PowerShell bereitgestellt werden.

1. Führen Sie das Skript aus, um die Diensttopologie bereitzustellen.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Wenn Sie dieses Skript über eine andere PowerShell-Sitzung als die Sitzung ausführen, die Sie für die Ausführung des Skripts [Vorbereiten der Artefakte](#prepare-the-artifacts) verwendet haben, müssen Sie die Variablen zunächst erneut auffüllen (u. a. `$resourceGroupName` und `$filePath`).

    > [!NOTE]
    > `New-AzResourceGroupDeployment` ist ein asynchroner Aufruf. Die Erfolgsmeldung bedeutet nur, dass die Bereitstellung erfolgreich gestartet wurde. Informationen zum Überprüfen der Bereitstellung finden Sie in Schritt 2 und 4 dieses Verfahrens.

1. Vergewissern Sie sich im Azure-Portal, dass die Diensttopologie und die zugrunde liegenden Ressourcen erfolgreich erstellt wurden:

    ![Tutorial für den Azure-Bereitstellungs-Manager: bereitgestellte Diensttopologieressourcen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    **Ausgeblendete Typen anzeigen** muss aktiviert sein, damit die Ressourcen angezeigt werden.

1. <a id="deploy-the-rollout-template"></a>Bereitstellen der Rolloutvorlage:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Überprüfen Sie den Rolloutstatus mithilfe des folgenden PowerShell-Skripts:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Die PowerShell-Cmdlets für den Bereitstellungs-Manager müssen installiert sein, um dieses Cmdlet ausführen zu können. Siehe [Voraussetzungen](#prerequisites). Der Parameter `-Verbose` kann verwendet werden, um die gesamte Ausgabe anzuzeigen.

    Das folgende Beispiel zeigt den Ausführungsstatus:

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Nach erfolgreicher Bereitstellung des Rollouts sehen Sie, dass zwei weitere Ressourcengruppen erstellt wurden (jeweils eine für jeden Dienst).

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu den neu erstellten Webanwendungen unter den neuen Ressourcengruppen, die durch die Rolloutbereitstellung erstellt wurden.
1. Öffnen Sie die Webanwendung in einem Webbrowser. Überprüfen Sie den Speicherort und die Version der Datei _index.html_.

## <a name="deploy-the-revision"></a>Bereitstellen der Revision

Wenn Sie über eine neue Version (1.0.0.1) für die Webanwendung verfügen, können Sie die Webanwendung wie folgt erneut bereitstellen:

1. Öffnen Sie _CreateADMRollout.Parameters.json_.
1. Aktualisieren Sie `binaryArtifactRoot` auf _binaries/1.0.0.1_.
1. Stellen Sie das Rollout gemäß der Anleitung unter [Bereitstellen der Vorlagen](#deploy-the-rollout-template) erneut bereit.
1. Überprüfen Sie die Bereitstellung gemäß der Anleitung unter [Überprüfen der Bereitstellung](#verify-the-deployment). Auf der Webseite sollte nun die Version 1.0.0.1 angezeigt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
1. Filtern Sie mithilfe des Felds **Nach Name filtern** nach den Ressourcengruppen, die in diesem Tutorial erstellt wurden.

    * **&lt;projectName>rg**: Enthält die Bereitstellungs-Manager-Ressourcen.
    * **&lt;projectName>ServiceWUSrg**: Enthält die durch „ServiceWUS“ definierten Ressourcen.
    * **&lt;projectName>ServiceEUSrg**: Enthält die durch „ServiceWUS“ definierten Ressourcen.
    * Die Ressourcengruppe für die benutzerdefinierte verwaltete Identität.
1. Klicken Sie auf den Namen der Ressourcengruppe.
1. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.
1. Wiederholen Sie die letzten beiden Schritte, um die anderen Ressourcengruppen zu löschen, die in diesem Tutorial erstellt wurden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie sich mit der Verwendung des Azure-Bereitstellungs-Managers vertraut gemacht. Informationen zum Integrieren der Integritätsüberwachung in den Azure-Bereitstellungs-Manager finden Sie unter [Tutorial: Verwenden der Integritätsprüfung im Azure-Bereitstellungs-Manager (Public Preview)](./deployment-manager-tutorial-health-check.md).
