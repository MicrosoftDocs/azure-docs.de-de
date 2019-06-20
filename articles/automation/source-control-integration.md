---
title: Integration der Quellcodeverwaltung in Azure Automation
description: In diesem Artikel wird die Integration der Quellcodeverwaltung mit GitHub in Azure Automation erläutert.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94912d5aa10ddd2e67c33bcbb416f007c85f105c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574108"
---
# <a name="source-control-integration-in-azure-automation"></a>Integration der Quellcodeverwaltung in Azure Automation

Mit der Quellcodeverwaltung halten Sie die Runbooks in Ihrem Automation-Konto auf demselben aktuellen Stand wie Ihre Skripts in der Quellcodeverwaltung Ihres GitHub- oder Azure Repos-Repositorys. Sie können mit der Quellcodeverwaltung einfach mit Ihrem Team zusammenarbeiten, Änderungen nachverfolgen und Ihre Runbooks auf frühere Versionen zurücksetzen. Mit der Quellcodeverwaltung können Sie beispielsweise verschiedene Verzweigungen in der Quellcodeverwaltung mit Ihren Automation-Entwicklungs- oder Testkonten synchronisieren. So ist es einfach, Code, der in Ihrer Entwicklungsumgebung getestet wurde, in Ihr Automation-Produktionskonto höher zu stufen. Bei der Integration der Quellcodeverwaltung mit Automatisierung wird die unidirektionale Synchronisierung aus Ihrem Repository der Quellcodeverwaltung unterstützt.

Azure Automation unterstützt drei Arten von Quellcodeverwaltung:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Voraussetzungen

* Ein Quellcodeverwaltungsrepository (GitHub oder Azure Repos)
* Ein [ausführendes Konto](manage-runas-account.md)
* Stellen Sie sicher, dass Sie im Automation-Konto über die neuesten [Azure-Module](automation-update-azure-modules.md) verfügen.

> [!NOTE]
> Synchronisierungsaufträge für die Quellcodeverwaltung werden unter dem Automation-Konto eines Benutzers ausgeführt und mit der gleichen Rate wie andere Automation-Aufträge berechnet.

## <a name="configure-source-control---azure-portal"></a>Konfigurieren der Quellcodeverwaltung – Azure-Portal

Wählen Sie in Ihrem Automation-Konto die Option **Quellcodeverwaltung**, und klicken Sie auf **+ Hinzufügen**.

![Auswählen der Quellcodeverwaltung](./media/source-control-integration/select-source-control.png)

Wählen Sie **Typ der Quellcodeverwaltung** aus, und klicken Sie auf **Authentifizieren**. Ein Browserfenster wird geöffnet, in dem Sie zum Anmelden aufgefordert werden. Führen Sie die angegebenen Schritte für die Authentifizierung aus.

Geben Sie auf der Seite **Quellcodeverwaltung – Übersicht** die Informationen ein, und klicken Sie auf **Speichern**. Die folgende Tabelle enthält eine Beschreibung der verfügbaren Felder.

|Eigenschaft  |BESCHREIBUNG  |
|---------|---------|
|Name der Quellcodeverwaltung     | Ein Anzeigename für die Quellcodeverwaltung. *Dieser Name darf nur Buchstaben und Zahlen enthalten.*        |
|Typ der Quellcodeverwaltung     | Der Typ der Quelle für die Quellcodeverwaltung. Die verfügbaren Optionen lauten wie folgt:</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|Repository     | Der Name des Repositorys oder Projekts. Die ersten 200 Repositorys werden zurückgegeben. Geben Sie zum Suchen nach einem Repository den Namen in das Feld ein, und klicken Sie auf **Search on GitHub** (Auf GitHub suchen).|
|Verzweigung     | Die Verzweigung, von der die Quelldateien abgerufen werden. Die Ausrichtung auf Verzweigungen ist für den TFVC-Quellcodeverwaltungstyp nicht verfügbar.          |
|Ordnerpfad     | Der Ordner, der die Runbooks für die Synchronisierung enthält. Beispiel: /Runbooks </br>*Nur im Ordner angegebene Runbooks werden synchronisiert. Die Rekursion wird nicht unterstützt.*        |
|Automatische Synchronisierung<sup>1</sup>     | Aktiviert oder deaktiviert die automatische Synchronisierung, wenn im Repository für die Quellcodeverwaltung ein Commit durchgeführt wird.         |
|Runbook veröffentlichen     | Wenn diese Option auf **Ein** festgelegt ist, werden die Runbooks automatisch veröffentlicht, nachdem sie aus der Quellcodeverwaltung synchronisiert wurden.         |
|BESCHREIBUNG     | Ein Textfeld, in dem weitere Details bereitgestellt werden können.        |

<sup>1</sup> Um die automatische Synchronisierung beim Konfigurieren der Integration der Quellcodeverwaltung in Azure Repos zu aktivieren, müssen Sie Projektadministrator sein.

![Zusammenfassung der Quellcodeverwaltung](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Ihre Anmeldung für Ihr Quellcodeverwaltungs-Repository kann von Ihrer Anmeldung beim Azure-Portal abweichen. Stellen Sie sicher, dass Sie mit dem richtigen Konto für Ihr Quellcodeverwaltungs-Repository angemeldet sind, wenn Sie die Quellcodeverwaltung konfigurieren. Wenn Sie diesbezüglich unsicher sind, öffnen Sie in Ihrem Browser eine neue Registerkarte, und melden Sie sich von „visualstudio.com“ oder „github.com“ ab, und stellen Sie erneut eine Verbindung mit der Quellcodeverwaltung her.

## <a name="configure-source-control---powershell"></a>Konfigurieren der Quellcodeverwaltung – PowerShell

Sie können auch PowerShell verwenden, um die Quellcodeverwaltung in Azure Automation zu konfigurieren. Zum Konfigurieren der Quellcodeverwaltung mit den PowerShell-Cmdlets benötigen Sie ein persönliches Zugriffstoken (Personal Access Token, PAT). Sie verwenden [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl), um die Verbindung für die Quellcodeverwaltung zu erstellen. Für das Cmdlet ist eine sichere Zeichenfolge des persönlichen Zugriffstokens erforderlich. Eine Beschreibung der Erstellung einer sicheren Zeichenfolge finden Sie unter [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Berechtigungen für persönliche Zugriffstoken

Die Quellcodeverwaltung erfordert einige Mindestberechtigungen für persönliche Zugriffstoken. Die folgenden Tabellen enthalten die für GitHub und Azure Repos erforderlichen Mindestberechtigungen.

#### <a name="github"></a>GitHub

Weitere Informationen zur Erstellung eines persönlichen Zugriffstokens in GitHub finden Sie unter [Creating a personal access token for the command line](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) (Erstellen eines persönlichen Zugriffstokens für die Befehlszeile).

|`Scope`  |BESCHREIBUNG  |
|---------|---------|
|**Repository**     |         |
|repo:status     | Zugriff auf den Commitstatus         |
|repo_deployment      | Zugriff auf den Bereitstellungsstatus         |
|public_repo     | Zugriff auf öffentliche Repositorys         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Schreiben von Repositoryhooks         |
|read:repo_hook|Lesen von Repositoryhooks|

#### <a name="azure-repos"></a>Azure Repos

Weitere Informationen zur Erstellung eines persönlichen Zugriffstokens in Azure Repos finden Sie unter [Authenticate access with personal access tokens](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) (Authentifizieren des Zugriffs mit persönlichen Zugriffstoken).

|`Scope`  |
|---------|
|Code (Lesen)     |
|Projekt und Team (Lesen)|
|Identität (Lesen)      |
|Benutzerprofil (Lesen)     |
|Arbeitselemente (Lesen)    |
|Dienstverbindungen (Lesen, Abfragen und Verwalten)<sup>1</sup>    |

<sup>1</sup> Die Berechtigung für Dienstverbindungen ist nur erforderlich, wenn die automatische Synchronisierung aktiviert ist.

## <a name="syncing"></a>Synchronisierung wird durchgeführt

Wählen Sie die Quelle in der Tabelle auf der Seite **Quellcodeverwaltung** aus. Klicken Sie auf **Synchronisierung starten**, um den Synchronisierungsprozess zu starten.

Sie können den Status des aktuellen Synchronisierungsauftrags oder von früheren Aufträgen anzeigen, indem Sie auf die Registerkarte **Synchronisierungsaufträge** klicken. Wählen Sie in der Dropdownliste **Quellcodeverwaltung** eine Quellcodeverwaltung aus.

![Synchronisierungsstatus](./media/source-control-integration/sync-status.png)

Sie können die Ausgabe des Auftrags anzeigen, indem Sie auf einen Auftrag klicken. Dies ist ein Beispiel für die Ausgabe eines Synchronisierungsauftrags der Quellcodeverwaltung:

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

Eine zusätzliche Protokollierung ist verfügbar, indem Sie auf der Seite **Quellcodeverwaltung – Übersicht zu Synchronisierungsauftrag** die Option **Alle Protokolle** wählen. Diese zusätzlichen Protokolleinträge können Ihnen als Hilfe beim Behandeln von Problemen dienen, die bei Verwendung der Quellcodeverwaltung auftreten können.

## <a name="disconnecting-source-control"></a>Trennen der Verbindung zur Quellcodeverwaltung

Öffnen Sie zum Trennen der Verbindung mit dem Repository der Quellcodeverwaltung in Ihrem Automation-Konto unter **Kontoeinstellungen** die Option **Quellcodeverwaltung**.

Wählen Sie die Quellcodeverwaltung aus, die Sie entfernen möchten. Klicken Sie auf der Seite **Quellcodeverwaltung – Übersicht** auf **Löschen**.

## <a name="encoding"></a>Codieren

Wenn in Ihrem Repository der Quellcodeverwaltung mehrere Personen Runbooks mit unterschiedlichen Editoren bearbeiten, kann es sein, dass Codierungsprobleme auftreten. Dies kann zu falschen Zeichen in Ihrem Runbook führen. Weitere Informationen hierzu finden Sie unter [Häufige Gründe für Codierungsprobleme](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="next-steps"></a>Nächste Schritte

Informationen über die verschiedenen Runbooktypen, ihre Vorteile und Einschränkungen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md)
