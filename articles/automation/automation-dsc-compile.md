---
title: Kompilieren von Konfigurationen in Azure Automation DSC
description: In diesem Artikel wird das Kompilieren von DSC-Konfigurationen (Desired State Configuration, Konfiguration des gewünschten Zustands) für Azure Automation beschrieben.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 847c928681451b4fef93198e2f2272d5bb04b1b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64919801"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilieren von DSC-Konfigurationen in Azure Automation DSC

Sie können DSC-Konfigurationen (Desired State Configuration) im Azure-Portal und mit Windows PowerShell mit Azure Automation DSC kompilieren. Die folgenden Informationen helfen bei der Entscheidung, welche Methode sich in welcher Situation am besten eignet:

**Azure-Portal**

- Einfachste Methode mit interaktiver Benutzeroberfläche
- Formular zum Bereitstellen von einfachen Parameterwerten
- Einfaches Nachverfolgen des Auftragsstatus
- Authentifizierter Zugriff über Azure-Anmeldung

**Windows PowerShell**

- Aufruf über Befehlszeile mit Windows PowerShell-Cmdlets
- Kann in eine automatisierte Lösung mit mehreren Schritten eingebunden werden
- Bereitstellen von einfachen und komplexen Parameterwerten
- Nachverfolgen des Auftragsstatus
- Client erforderlich zur Unterstützung der PowerShell-Befehle
- Übergeben von ConfigurationData-Werten
- Kompilieren von Konfigurationen mit Anmeldeinformationen

Wenn Sie sich für eine Kompilierungsmethode entschieden haben, folgen Sie den unten beschriebenen Verfahren, um mit der Kompilierung zu beginnen.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilieren einer DSC-Konfiguration mit dem Azure-Portal

1. Klicken Sie im Automation-Konto auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf die Registerkarte **Konfigurationen** und dann auf den Namen der Konfiguration, die kompiliert werden soll.
1. Klicken Sie auf **Kompilieren**.
1. Wenn die Konfiguration keine Parameter enthält, werden Sie dazu aufgefordert, zu bestätigen, dass Sie die Konfiguration kompilieren möchten. Wenn die Konfiguration Parameter enthält, wird das Blatt **Konfiguration kompilieren** geöffnet, auf dem Sie Parameterwerte angeben können. Weitere Informationen zu Parametern finden Sie im Abschnitt [**Grundlegende Parameter**](#basic-parameters) weiter unten.
1. Die Seite **Kompilierungsauftrag** wird geöffnet. Dort können Sie den Status des Kompilierungsauftrags und die Knotenkonfigurationen (MOF-Konfigurationsdokumente) nachverfolgen, die durch den Auftrag auf dem Azure Automation DSC-Pullserver platziert werden.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilieren einer DSC-Konfiguration mit Windows PowerShell

Sie können [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) verwenden, um mit der Kompilierung mit Windows PowerShell zu beginnen. Der folgende Beispielcode startet die Kompilierung einer DSC-Konfiguration namens **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` gibt ein Kompilierungsauftragsobjekt zurück, das zum Nachverfolgen des Auftragsstatus verwendet werden kann. Sie können dieses Kompilierungsauftragsobjekt anschließend mit [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) verwenden,
um den Status des Kompilierungsauftrags zu ermitteln, und [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput),
um die Datenströme (Ausgabe) anzuzeigen. Der folgende Beispielcode startet die Kompilierung der **SampleConfig**-Konfiguration, wartet, bis die Kompilierung abgeschlossen ist, und zeigt dann die Datenströme an.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Grundlegende Parameter

Die Parameterdeklaration in DSC-Konfigurationen, einschließlich der Parametertypen und -eigenschaften, funktioniert genauso wie in Azure Automation-Runbooks. Informationen zu Runbookparametern finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) .

Das folgende Beispiel verwendet zwei Parameter namens **FeatureName** und **IsPresent**, um die Werte der Eigenschaften in der während der Kompilierung generierten **ParametersExample.sample**-Knotenkonfiguration zu ermitteln.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Sie können DSC-Konfigurationen kompilieren, die grundlegende Parameter im Azure Automation DSC-Portal oder in Azure PowerShell verwenden:

### <a name="portal"></a>Portal

Im Portal können Sie Parameterwerte eingeben, nachdem Sie auf **Kompilieren**geklickt haben.

![Konfigurationsparameter für das Kompilieren](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell erfordert Parameter in einer [Hashtabelle](/powershell/module/microsoft.powershell.core/about/about_hash_tables) , in der der Schlüssel dem Parameternamen entspricht und der Wert gleich dem Parameterwert ist.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Informationen zum Übergeben von PSCredentials als Parameter finden Sie unten unter [Anmeldeinformationen](#credential-assets).

## <a name="composite-resources"></a>Zusammengesetzte Ressourcen

**Zusammengesetzte Ressourcen** ermöglichen es Ihnen, die DSC-Konfigurationen als geschachtelte Ressourcen innerhalb einer Konfiguration zu verwenden. Dadurch können Sie mehrere Konfigurationen auf eine einzelne Ressource anwenden. Unter [Zusammengesetzte Ressourcen: Verwenden einer DSC-Konfiguration als Ressource](/powershell/dsc/authoringresourcecomposite) erfahren Sie mehr über **zusammengesetzte Ressourcen**.

> [!NOTE]
> Damit **zusammengesetzte Ressourcen** korrekt kompiliert werden, müssen Sie zunächst sicherstellen, dass alle DSC-Ressourcen, auf denen die zusammengesetzten Elemente basieren, zuerst im Azure Automation-Kontomodulrepository installiert werden, da sie andernfalls nicht korrekt importiert werden.

Zum Hinzufügen einer **zusammengesetzten DSC-Ressource** müssen Sie das Ressourcenmodul zu einem Archiv (*.zip) hinzufügen. Wechseln Sie in Ihrem Azure Automation-Konto zum Modulrepository. Klicken Sie dann auf die Schaltfläche "Modul hinzufügen".

![Modul hinzufügen](./media/automation-dsc-compile/add_module.png)

Navigieren Sie zu dem Verzeichnis, in dem sich Ihr Archiv befindet. Wählen Sie die Archivdatei aus, und klicken Sie auf "OK".

![Modul auswählen](./media/automation-dsc-compile/select_dscresource.png)

Sie gelangen anschließend wieder zum Modulverzeichnis, wo Sie den Status Ihrer **zusammengesetzten Ressource** überwachen können, während diese entpackt und bei Azure Automation registriert wird.

![Zusammengesetzte Ressource importieren](./media/automation-dsc-compile/register_composite_resource.png)

Sobald das Modul registriert wurde, können Sie darauf klicken, um zu überprüfen, ob **zusammengesetzte Ressourcen** jetzt für die Verwendung in einer Konfiguration verfügbar sind.

![Zusammengesetzte Ressource überprüfen](./media/automation-dsc-compile/validate_composite_resource.png)

Rufen Sie anschließend die **zusammengesetzte Ressource** wie folgt in Ihrer Konfiguration auf:

```powershell
Node ($AllNodes.Where{$_.Role -eq 'WebServer'}).NodeName
{
    DomainConfig myCompositeConfig
    {
        DomainName = $DomainName
        Admincreds = $Admincreds
    }

    PSWAWebServer InstallPSWAWebServer
    {
        DependsOn = '[DomainConfig]myCompositeConfig'
    }
}
```

## <a name="configurationdata"></a>ConfigurationData

Mit **ConfigurationData** können Sie bei Verwendung von PowerShell DSC die Konfiguration der Struktur von jeglicher umgebungsspezifischer Konfiguration trennen. Informationen zu [ConfigurationData](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) finden Sie unter **Separating "What" from "Where" in PowerShell DSC**.

> [!NOTE]
> Sie können **ConfigurationData** beim Kompilieren in Azure Automation DSC mit Azure PowerShell verwenden, jedoch nicht im Azure-Portal.

Die folgende DSC-Beispielkonfiguration verwendet **ConfigurationData** über die Schlüsselwörter **$ConfigurationData** und **$AllNodes**. Für dieses Beispiel benötigen Sie außerdem das [Modul **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Sie können die vorherige DSC-Konfiguration über PowerShell kompilieren. Der folgende PowerShell-Befehl fügt dem Azure Automation DSC-Pullserver die beiden Knotenkonfigurationen **ConfigurationDataSample.MyVM1** und **ConfigurationDataSample.MyVM3** hinzu:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

## <a name="assets"></a>Objekte

Objektverweise in Azure Automation DSC und -Runbooks sind gleich. Weitere Informationen finden Sie unter den folgenden Links:

- [Zertifikate](automation-certificates.md)
- [Verbindungen](automation-connections.md)
- [Anmeldeinformationen](automation-credentials.md)
- [Variablen](automation-variables.md)

### <a name="credential-assets"></a>Anmeldeinformationen

DSC-Konfigurationen in Azure Automation können mithilfe des Cmdlets `Get-AutomationPSCredential` auf Automation-Anmeldeinformationsobjekte verweisen. Wenn eine Konfiguration einen Parameter aufweist, der den Typ **PSCredential** enthält, können Sie das Cmdlet `Get-AutomationPSCredential` verwenden, indem Sie den Zeichenfolgennamen eines Azure Automation-Anmeldeinformationsobjekts an das Cmdlet übergeben, um die Anmeldeinformationen abzurufen. Sie können dieses Objekt anschließend für den Parameter verwenden, der das **PSCredential**-Objekt erfordert. Im Hintergrund wird das Azure Automation-Anmeldeinformationsobjekt mit diesem Namen abgerufen und an die Konfiguration übergeben. Das folgende Beispiel stellt die praktische Anwendung dar.

Um die Sicherheit von Anmeldeinformationen in Knotenkonfigurationen (MOF-Konfigurationsdokumente) zu gewährleisten, müssen diese Informationen in der MOF-Datei der Knotenkonfiguration verschlüsselt werden. Zurzeit müssen Sie PowerShell DSC jedoch noch darüber informieren, dass die Anmeldeinformationen während der Generierung der Knotenkonfiguration-MOF-Datei im Klartext übergeben werden. PowerShell DSC hat keinerlei Informationen darüber, dass Azure Automation die gesamte MOF-Datei nach dem Generieren über einen Kompilierungsauftrag verschlüsselt.

Verwenden Sie [**ConfigurationData**](#configurationdata). Übergeben Sie `PSDscAllowPlainTextPassword = $true` über **ConfigurationData** für den Namen jedes Knotenblocks, der in der DSC-Konfiguration angezeigt wird und Anmeldeinformationen verwendet.

Das folgende Beispiel zeigt eine DSC-Konfiguration, die ein Automation-Anmeldeinformationsobjekt verwendet.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Sie können die vorherige DSC-Konfiguration über PowerShell kompilieren. Der folgende PowerShell-Befehl fügt dem Azure Automation DSC-Pullserver die beiden Knotenkonfigurationen **CredentialSample.MyVM1** und **CredentialSample.MyVM2** hinzu.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Wenn die Kompilierung abgeschlossen ist, wird möglicherweise eine Fehlermeldung angezeigt: **Das Modul „Microsoft.PowerShell.Management“ wurde nicht importiert, da das Snap-In „Microsoft.PowerShell.Management“ bereits importiert war.** Diese Warnung kann ignoriert werden.

## <a name="partial-configuration"></a>Teilkonfiguration

Azure Automation DSC unterstützt die Verwendung von [Teilkonfigurationen](https://docs.microsoft.com/powershell/dsc/pull-server/partialconfigs).
In diesem Szenario ist DSC so konfiguriert, dass mehrere Konfigurationen unabhängig voneinander verwaltet werden können, wobei jede Konfiguration von Azure Automation abgerufen wird.
Einem Knoten kann aber nur eine Konfiguration mittels Automation-Konto zugewiesen werden.
Dies bedeutet, dass Sie bei Verwendung von zwei Konfigurationen für einen Knoten zwei Automation-Kontos benötigen.
Weitere Informationen dazu, wie Teams zusammenarbeiten können, um Server gemeinsam mithilfe von Konfiguration als Code zu verwalten, finden Sie unter [Grundlegendes zu DSCs Rolle in einer CI/CD-Pipeline](https://docs.microsoft.com/powershell/dsc/overview/authoringadvanced).

## <a name="importing-node-configurations"></a>Importieren von Knotenkonfigurationen

Sie können auch Knotenkonfigurationen (MOF-Dateien) importieren, die außerhalb von Azure kompiliert wurden. Ein Vorteil besteht darin, dass diese Knotenkonfigurationen signiert werden können. Eine signierte Knotenkonfiguration wird lokal vom DSC-Agent auf einem verwalteten Knoten überprüft, um sicherzustellen, dass die auf den Knoten angewendete Konfiguration von einer autorisierten Quelle stammt.

> [!NOTE]
> Sie können signierte Konfigurationen in Ihr Azure Automation-Konto importieren, Azure Automation unterstützt aber derzeit nicht das Kompilieren von signierten Konfigurationen.

> [!NOTE]
> Knotenkonfigurationsdateien dürfen nicht größer als 1 MB sein, damit sie in Azure Automation importiert werden können.

Weitere Informationen zum Signieren von Knotenkonfigurationen finden Sie unter [in WMF 5.1 – So signieren Sie Konfigurationen und Module](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importieren von Knotenkonfigurationen im Azure-Portal

1. Klicken Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Konfigurationen** und dann auf **Hinzufügen**.
1. Klicken Sie auf der Seite **Importieren** auf das Ordnersymbol neben dem Textfeld **Knotenkonfigurationsdatei**, um eine Knotenkonfigurationsdatei (MOF) auf dem lokalen Computer zu suchen.

   ![Suchen einer lokalen Datei](./media/automation-dsc-compile/import-browse.png)

1. Geben Sie im Textfeld **Konfigurationsname** einen Namen ein. Dieser Name muss mit dem Namen der Konfiguration übereinstimmen, aus der die Knotenkonfiguration kompiliert wurde.
1. Klicken Sie auf **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importieren einer Knotenkonfiguration mit PowerShell

Sie können das Cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) verwenden, um eine Knotenkonfiguration in Ihr Automation-Konto zu importieren.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation DSC](automation-dsc-compile.md).
- Eine PowerShell-Cmdlet-Referenz ist unter [Azure Automation State Configuration-Cmdlets](/powershell/module/azurerm.automation/#automation) verfügbar.
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Verwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Continuous Deployment mit Azure Automation State Configuration und Chocolatey](automation-dsc-cd-chocolatey.md).
