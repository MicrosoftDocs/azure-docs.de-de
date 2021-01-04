---
title: Hinzufügen eines Artefaktrepositorys zu Ihrem Lab in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihr eigenes Artefaktrepository für Ihr Lab in Azure DevTest Labs angeben, um Tools zu speichern, die im öffentlichen Artefaktrepository nicht verfügbar sind.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5dd7d89020bf077e29b177f6871f43b52467b0d8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97512010"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Hinzufügen eines Artefaktrepositorys zu Ihrem Lab in DevTest Labs
DevTest Labs ermöglicht das Angeben eines Artefakts, das einem virtuellen Computer hinzugefügt werden soll – entweder beim Erstellen des virtuellen Computers oder nach Erstellung des virtuellen Computers. Bei diesem Artefakt kann es sich beispielsweise um ein Tool oder um eine Anwendung handeln, das bzw. die Sie auf dem virtuellen Computer installieren möchten. Artefakte werden in einer JSON-Datei definiert, die aus einem GitHub- oder Azure DevOps-Git-Repository geladen wird.

Das von DevTest Labs gepflegte [öffentliche Artefaktrepository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) enthält zahlreiche gängige Tools für Windows und Linux. Ein Link zu diesem Repository wird Ihrem Lab automatisch hinzugefügt. Sie können Ihr eigenes Artefaktrepository mit speziellen Tools erstellen, die im öffentlichen Artefaktrepository nicht zur Verfügung stehen. Informationen zum Erstellen benutzerdefinierter Artefakte finden Sie unter [Erstellen benutzerdefinierter Artefakte](devtest-lab-artifact-author.md).

In diesem Artikel erfahren Sie, wie Sie Ihr benutzerdefiniertes Artefaktrepository über das Azure-Portal sowie mithilfe von Azure Resource Manager-Vorlagen und Azure PowerShell hinzufügen. Das Hinzufügen eines Artefaktrepositorys zu einem Lab lässt sich mithilfe von PowerShell- oder CLI-Skripts automatisieren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie Ihrem Lab ein Repository hinzufügen können, rufen Sie zunächst wichtige Informationen aus dem Repository ab. In den folgenden Abschnitten wird beschrieben, wie Sie die erforderlichen Informationen für Repositorys abrufen, die auf **GitHub** oder in **Azure DevOps** gehostet werden.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Abrufen der Klon-URL und des persönlichen Zugriffstokens für das GitHub-Repository

1. Navigieren Sie zur Startseite des GitHub-Repositorys, das die Artefakt- oder Resource Manager-Vorlagendefinitionen enthält.
2. Wählen Sie **Clone or download** aus.
3. Um die URL in die Zwischenablage zu kopieren, klicken Sie auf die Schaltfläche **HTTPS-Klon-URL**. Speichern Sie die URL für die spätere Verwendung.
4. Wählen Sie das Profilbild in der oberen rechten Ecke von GitHub aus, und wählen Sie **Einstellungen**.
5. Wählen Sie im Menü **Personal settings** (Persönliche Einstellungen) auf der linken Seite die Option **Developer Settings** (Entwicklereinstellungen) aus.
6. Wählen Sie im linken Menü die Option **Personal access tokens** (Persönliche Zugriffstoken) aus.
7. Wählen Sie **Generate new token**.
8. Geben Sie auf der Seite **Neues persönliches Zugriffstoken** unter **Tokenbeschreibung** eine Tokenbeschreibung ein. Übernehmen Sie die Standardelemente unter **Bereiche auswählen**, und wählen Sie dann **Token generieren** aus.
9. Speichern Sie das generierte Token. Das Token benötigen Sie später.
10. Schließen Sie GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Abrufen der Klon-URL und des persönlichen Zugriffstokens für Azure Repos
1. Wechseln Sie zur Startseite Ihrer Teamsammlung (z. B. `https://contoso-web-team.visualstudio.com`), und wählen Sie dann das Projekt aus.
2. Wählen Sie auf der Startseite des Projekts **Code**.
3. Um die Klon-URL für das Projekt anzuzeigen, wählen Sie auf der Seite **Code** die Option **Klonen** aus.
4. Speichern Sie die URL. Sie benötigen die URL später.
5. Um ein persönliches Zugriffstoken zu erstellen, wählen Sie im Dropdownmenü Ihres Benutzerkontos **Mein Profil**.
6. Wählen Sie auf der Seite mit den Profilinformationen **Sicherheit**.
7. Wählen Sie auf der Registerkarte **Sicherheit > Persönliche Zugriffstoken** die Option **+ Neues Token** aus.
8. Gehen Sie auf der Seite **Neues persönliches Zugriffstoken erstellen** folgendermaßen vor:
   1. Geben Sie unter **Name** einen Namen für das Token ein.
   2. Wählen Sie in der Liste **Organisation** die Option **Alle zugänglichen Organisationen** aus.
   3. Wählen Sie in der Liste **Ablaufzeit (UTC)** die Option **90 Tage** oder einen benutzerdefinierten Zeitraum aus.
   4. Wählen Sie für „Bereiche“ die Option **Vollzugriff** aus.
   5. Klicken Sie auf **Erstellen**.
9. Das neue Token wird in der Liste **Persönliche Zugriffstokens** angezeigt. Wählen Sie **Token kopieren**, und speichern Sie den Tokenwert für die spätere Verwendung.
10. Fahren Sie mit dem Abschnitt zum Herstellen der Verbindung zwischen Ihrem Lab und dem Repository fort.

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
In diesem Abschnitt erfahren Sie, wie Sie einem Lab über das Azure-Portal ein Artefaktrepository hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Weitere Dienste** und dann in der Liste der Dienste **DevTest Labs** aus.
3. Wählen Sie in der Liste mit den Labs Ihr Lab aus.
4. Wählen Sie im linken Menü die Option **Konfiguration und Richtlinien** aus.
5. Wählen Sie im linken Menü unter dem Abschnitt **Externe Ressourcen** die Option **Repositorys** aus.
6. Wählen Sie auf der Symbolleiste die Option **+ Hinzufügen** aus.

    ![Schaltfläche zum Hinzufügen eines Repositorys](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Geben Sie auf der Seite **Repositorys** Folgendes an:
   1. **Name**: Geben Sie einen Namen für das Repository ein.
   2. **Git-Klon-URL**. Geben Sie die Git-HTTPS-Klon-URL ein, die Sie zuvor aus GitHub oder Azure DevOps Services kopiert haben.
   3. **Branch**. Um zu Ihren Definitionen zu gelangen, geben Sie den Branch an.
   4. **Persönliches Zugriffstoken**. Geben Sie das persönliche Zugriffstoken ein, das Sie zuvor aus GitHub oder Azure DevOps Services abgerufen haben.
   5. **Ordnerpfade**. Geben Sie mindestens einen Ordnerpfad relativ zur Klon-URL an, der die Artefakt- oder Resource Manager-Vorlagendefinitionen enthält. Wenn Sie ein Unterverzeichnis angeben, müssen Sie den Schrägstrich im Ordnerpfad angeben.

        ![Bereich „Repositorys“](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wählen Sie **Speichern** aus.

## <a name="use-azure-resource-manager-template"></a>Verwenden von Azure Resource Manager-Vorlagen
Vorlagen für die Azure-Ressourcenverwaltung (Azure Resource Manager-Vorlagen) sind JSON-Dateien, die Ressourcen in Azure beschreiben, die Sie erstellen möchten. Weitere Informationen zu diesen Vorlagen finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md).

In diesem Abschnitt erfahren Sie, wie Sie einem Lab unter Verwendung einer Azure Resource Manager-Vorlage ein Artefaktrepository hinzufügen.  Die Vorlage erstellt das Lab, falls es noch nicht vorhanden ist.

### <a name="template"></a>Vorlage
Die in diesem Artikel verwendete Beispielvorlage sammelt über Parameter folgende Informationen. Die meisten Parameter besitzen zwar intelligente Standardwerte, einige Werte müssen jedoch angegeben werden. Sie müssen den Labnamen, den URI für das Artefaktrepository und das Sicherheitstoken für das Repository angeben.

- Labname:
- Anzeigename für das Artefaktrepository auf der Benutzeroberfläche von DevTest Labs. Standardwert: `Team Repository`.
- URI für das Repository (beispielsweise `https://github.com/<myteam>/<nameofrepo>.git` oder `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`).
- Repository-Branch mit Artefakten. Standardwert: `master`.
- Name des Ordners mit Artefakten. Standardwert: `/Artifacts`.
- Art des Repositorys. Zulässige Werte: `VsoGit` und `GitHub`.
- Zugriffstoken für das Repository.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Bereitstellen der Vorlage
Es gibt mehrere Möglichkeiten, die Vorlage in Azure bereitzustellen und die Ressource zu erstellen (falls sie noch nicht vorhanden ist) oder zu aktualisieren (falls sie bereits vorhanden ist). Details finden Sie in den folgenden Artikeln:

- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-CLI](../azure-resource-manager/templates/deploy-cli.md)
- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Resource Manager-REST-API](../azure-resource-manager/templates/deploy-rest.md)

Hier beschäftigen wir uns als Nächstes mit der Bereitstellung der Vorlage in PowerShell. Die Cmdlets für die Vorlagenbereitstellung sind kontextspezifisch. Daher werden der aktuelle Mandant und das aktuelle Abonnement verwendet. Verwenden Sie vor dem Bereitstellen der Vorlage ggf. [Set-AzContext](/powershell/module/az.accounts/set-azcontext), um den Kontext zu ändern.

Erstellen Sie zunächst mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. Ist die Ressourcengruppe, die Sie verwenden möchten, bereits vorhanden, überspringen Sie diesen Schritt.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Erstellen Sie als Nächstes mit [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) eine Bereitstellung für die Ressourcengruppe. Dieses Cmdlet wendet die Ressourcenänderungen auf Azure an. Für jede Ressourcengruppe können mehrere Ressourcenbereitstellungen ausgeführt werden. Falls Sie mehrmals etwas in der gleichen Ressourcengruppe bereitstellen, achten Sie darauf, dass der Name jeder Bereitstellung eindeutig ist.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Nachdem „New-AzResourceGroupDeployment“ erfolgreich ausgeführt wurde, gibt der Befehl wichtige Informationen wie Bereitstellungsstatus (sollte „Erfolgreich“ lauten) und Ausgaben für die Vorlage aus.

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell
Dieser Abschnitt enthält ein PowerShell-Beispielskript, mit dem Sie einem Lab ein Artefaktrepository hinzufügen können. Sollten Sie nicht über Azure PowerShell verfügen, finden Sie unter [Overview of Azure PowerShell](/powershell/azure/?view=azps-1.2.0) (Übersicht über Azure PowerShell) eine ausführliche Installationsanleitung.

### <a name="full-script"></a>Vollständiges Skript
Hier sehen Sie das vollständige Skript (einschließlich einiger ausführlicher Meldungen und Kommentare):

**New-DevTestLabArtifactRepository.ps1:**

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the repository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Ausführen des PowerShell-Skripts
Das folgende Beispiel zeigt, wie Sie das Skript ausführen:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parameter
Das PowerShell-Beispielskript in diesem Artikel verwendet folgende Parameter:

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| LabName | Der Name des Labs. |
| ArtifactRepositoryName | Der Name für das neue Artefaktrepository. Ohne Angabe erstellt das Skript einen Zufallsnamen für das Repository. |
| ArtifactRepositoryDisplayName | Der Anzeigename für das Artefaktrepository. Dieser Name wird im Azure-Portal (https://portal.azure.com) angezeigt, wenn alle Artefaktrepositorys für ein Lab angezeigt werden. |
| RepositoryUri | Der URI für das Repository. Beispiele: `https://github.com/<myteam>/<nameofrepo>.git`, `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.|
| RepositoryBranch | Branch, in dem sich Artefaktdateien befinden. Standardwert: „master“. |
| FolderPath | Ordner, in dem sich Artefakte befinden. Standardwert: „/Artifacts“. |
| PersonalAccessToken | Das Sicherheitstoken für den Zugriff auf das GitHub- oder VSO Git-Repository. Eine Anleitung zum Abrufen des persönlichen Zugriffstokens finden Sie im Abschnitt „Voraussetzungen“. |
| SourceType | Gibt an, ob sich das Artefakt in einem VSO Git- oder in einem GitHub-Repository befindet. |

Das Repository selbst muss zur Identifizierung über einen internen Namen verfügen. Dieser unterscheidet sich von dem Namen, der im Azure-Portal angezeigt wird. Der interne Name wird bei Verwendung von Azure-REST-APIs sowie bei Verwendung von Azure PowerShell angezeigt, nicht aber im Azure-Portal. Falls der Skriptbenutzer keinen Namen angibt, wird durch das Skript ein Name festgelegt.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Im Skript verwendete PowerShell-Befehle

| PowerShell-Befehl | Notizen |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Dieser Befehl dient zum Abrufen von Labdetails (beispielsweise des Standorts). |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Für das Hinzufügen von Artefaktrepositorys gibt es keinen speziellen Befehl. Hierzu kann das generische Cmdlet [New-AzResource](/powershell/module/az.resources/new-azresource) verwendet werden. Für dieses Cmdlet muss entweder die Ressourcen-ID (**ResourceId**) oder die Kombination aus Ressourcenname (**ResourceName**) und Ressourcentyp (**ResourceType**) angegeben werden, damit klar ist, welche Art von Ressource erstellt werden soll. In diesem Beispiel wird die Kombination aus Ressourcenname und Ressourcentyp verwendet. <br/><br/>Beachten Sie, dass die Quelle des Artefaktrepositorys am gleichen Standort und unter der gleichen Ressourcengruppe erstellt wird wie das Lab.|

Das Skript fügt dem aktuellen Abonnement eine neue Ressource hinzu. Verwenden Sie [Get-AzContext](/powershell/module/az.accounts/get-azcontext), um diese Informationen anzuzeigen. Verwenden Sie [Set-AzContext](/powershell/module/az.accounts/set-azcontext), um den aktuellen Mandanten und das Abonnement festzulegen.

Name und Art der Ressource ermitteln Sie am besten über den [REST-API-Browser](https://azure.github.io/projects/apis/). Überprüfen Sie den Anbieter [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis), um die verfügbaren REST-APIs für den DevTest Labs-Anbieter anzuzeigen. Das Skript verwendet die folgende Ressourcen-ID:

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Der Ressourcentyp ist alles, was im URI nach „providers“ angegeben ist (mit Ausnahme der Elemente in den geschweiften Klammern). Der Ressourcenname ist alles, was in den geschweiften Klammern angegeben ist. Falls für den Ressourcennamen mehrere Elemente erwartet werden, trennen Sie die einzelnen Elemente jeweils durch einen Schrägstrich, wie hier gezeigt.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Nächste Schritte
- [Festlegen verbindlicher Artefakte für Ihr Lab in Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Erstellen benutzerdefinierter Artefakte für Ihren virtuellen DevTest Labs-Computer](devtest-lab-artifact-author.md)
- [Diagnostizieren von Artefaktfehlern im Lab](devtest-lab-troubleshoot-artifact-failure.md)
