---
title: Ausführen von Runbooks für Hybrid Runbook Worker in Azure Automation
description: Dieser Artikel enthält Informationen zur Ausführung von Runbooks auf Computern in Ihrem lokalen Datencenter oder bei Ihrem Cloudanbieter mit der Hybrid Runbook Worker-Rolle.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fab886de55cc524390093f7e7913c79f7af3fe78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60738501"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Ausführen von Runbooks auf einem Hybrid Runbook Worker

Es gibt keine Unterschiede in der Struktur von Runbooks, die in Azure Automation oder auf einem Hybrid-Runbook-Worker ausgeführt werden. Die Runbooks, die Sie in beiden Fällen verwenden, unterscheiden sich dagegen wahrscheinlich erheblich. Dies liegt daran, dass Runbooks, die für einen Hybrid Runbook Worker konzipiert sind, in der Regel Ressourcen auf dem lokalen Computer selbst oder in der lokalen Umgebung verwalten, in der der Computer bereitgestellt wurde. Runbooks in Azure Automation verwalten üblicherweise Ressourcen in der Azure-Cloud.

Wenn Sie Runbooks zur Ausführung auf einem Hybrid Runbook Worker erstellen, sollten Sie diese auf dem Computer testen, der den Hybridworker hostet. Auf dem Hostcomputer sind alle PowerShell-Module und der Netzwerkzugriff vorhanden, die Sie benötigen, um die lokalen Ressourcen zu verwalten und darauf zuzugreifen. Sobald ein Runbook auf dem Hybridworkercomputer getestet wurde, können Sie es in die Azure Automation-Umgebung hochladen, wo es zur Ausführung auf dem Hybridworker verfügbar ist. Sie müssen wissen, ob Aufträge im lokalen Systemkonto für Windows oder dem speziellen Benutzerkonto `nxautomation` unter Linux ausgeführt werden. Dieses Verhalten kann geringfügige Unterschiede beim Erstellen von Runbooks für einen Hybrid Runbook Worker bedeuten. Wenn Sie Ihre Runbooks schreiben, müssen Sie auf diese Änderungen achten.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Starten eines Runbooks auf einem Hybrid Runbook Worker

[Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) werden die verschiedenen Methoden zum Starten eines Runbooks beschrieben. Hybrid-Runbook-Worker fügt die Option **RunOn** hinzu, mit der Sie den Namen einer Hybrid-Runbook-Workergruppe angeben können. Beim Angeben einer Gruppe wird das Runbook von einem der Worker in dieser Gruppe abgerufen und ausgeführt. Wird diese Option nicht angegeben, erfolgt die Ausführung auf normale Weise in Azure Automation.

Wenn Sie ein Runbook im Azure-Portal starten, wird die Option **Ausführen auf** angezeigt. Sie können entweder **Azure** oder **Hybrid Worker** auswählen. Bei Auswahl von **Hybrid-Worker** können Sie anschließend aus einer Dropdownliste die gewünschte Gruppe auswählen.

Verwenden Sie den Parameter **RunOn**. Mit dem folgenden Befehl wird mithilfe von Windows PowerShell ein Runbook namens „Test-Runbook“ auf einer Hybrid Runbook Worker-Gruppe mit dem Namen „MyHybridGroup“ gestartet.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Der Parameter **RunOn** wurde dem Cmdlet **Start-AzureAutomationRunbook** in Version 0.9.1 von Microsoft Azure PowerShell hinzugefügt. Sie sollten die [neueste Version herunterladen](https://azure.microsoft.com/downloads/) , wenn auf Ihrem System eine Vorgängerversion installiert ist. Sie müssen diese Version nur auf der Arbeitsstation installieren, auf der Sie das Runbook aus PowerShell starten. Eine Installation auf dem Workercomputer ist nur erforderlich, wenn Sie Runbooks von diesem Computer starten möchten.

## <a name="runbook-permissions"></a>Runbookberechtigungen

Bei Runbooks, die auf einem Hybrid Runbook Worker ausgeführt werden, kann nicht dieselbe Methode verwendet werden, mit der Runbooks üblicherweise bei Azure-Ressourcen authentifiziert werden, da diese Runbooks auf Ressourcen zugreifen, die sich nicht in Azure befinden. Das Runbook kann entweder eine eigene Authentifizierung für lokale Ressourcen bereitstellen oder die Authentifizierung mithilfe von [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
) konfigurieren. Alternativ hierzu können Sie auch ein ausführendes Konto angeben, um einen Benutzerkontext für alle Runbooks bereitzustellen.

### <a name="runbook-authentication"></a>Authentifizierung von Runbooks

Runbooks werden standardmäßig im Kontext des lokalen Systemkontos für Windows und einem speziellen Benutzerkonto `nxautomation` auf dem lokalen Computer ausgeführt. Deshalb müssen sie sich bei Ressourcen authentifizieren, auf die sie zugreifen.

Sie können die [Anmeldeinformationsobjekte](automation-credentials.md) und [Zertifikatobjekte](automation-certificates.md) in Ihrem Runbook gemeinsam mit Cmdlets zur Angabe von Anmeldeinformationen verwenden, um eine Authentifizierung für verschiedene Ressourcen durchzuführen. Das folgende Beispiel zeigt einen Teil eines Runbooks, mit dem ein Computer neu gestartet wird. Es werden Anmeldeinformationen aus einem Anmeldeinformationsobjekt und der Name des Computers aus einem Variablenobjekt abgerufen, anschließend werden diese Werte im Cmdlet "Restart-Computer" eingesetzt.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Sie können auch [InlineScript](automation-powershell-workflow.md#inlinescript) verwenden, um Codeblöcke auf einem anderen Computer ausführen zu können. Die hierfür erforderlichen Anmeldeinformationen werden im [allgemeinen PSCredential-Parameter](/powershell/module/psworkflow/about/about_workflowcommonparameters) angegeben.

### <a name="runas-account"></a>„RunAs“-Konto

Standardmäßig verwendet der Hybrid Runbook Worker für die Runbookausführung das lokale System für Windows und ein spezielles Benutzerkonto `nxautomation` für Linux. Um zu vermeiden, dass Runbooks ihre eigene Authentifizierung für lokale Ressourcen bereitstellen müssen, können Sie für eine Hybrid Worker-Gruppe ein **RunAs**-Konto angeben. Sie geben ein [Anmeldeinformationsobjekt](automation-credentials.md) mit Zugriff auf lokale Ressourcen an. Alle Runbooks verwenden anschließend diese Anmeldeinformationen, wenn die Ausführung auf einem Hybrid Runbook Worker in der Gruppe erfolgt.

Der Benutzername für die Anmeldeinformationen muss eines der folgenden Formate haben:

* Domäne\Benutzername
* username@domain
* Benutzername (für Konten, die für den lokalen Computer lokal sind)

Verwenden Sie das folgende Verfahren, um ein „RunAs“-Konto für eine Hybrid Worker Gruppe anzugeben:

1. Erstellen Sie ein [Anmeldeinformationsobjekt](automation-credentials.md) mit Zugriff auf lokale Ressourcen.
2. Wählen Sie im Azure-Portal das Automation-Konto aus.
3. Wählen Sie die Kachel **Hybrid Worker-Gruppen** und dann die Gruppe aus.
4. Wählen Sie **Alle Einstellungen** und dann **Einstellungen für Hybrid Worker-Gruppe** aus.
5. Ändern Sie **Ausführen als** von **Standard** in **Benutzerdefiniert**.
6. Wählen Sie die Anmeldeinformationen aus, und klicken Sie auf **Speichern**.

### <a name="managed-identities-for-azure-resources"></a>Verwaltete Identitäten für Azure-Ressourcen

Für Hybrid Runbook Worker, die auf virtuellen Azure-Computern ausgeführt werden, können verwaltete Identitäten genutzt werden, um Azure-Ressourcen gegenüber anderen Azure-Ressourcen zu authentifizieren. Die Verwendung von verwalteten Identitäten für Azure-Ressourcen hat im Vergleich zu ausführenden Konten viele Vorteile.

* Der Export des Zertifikats für das ausführende Konto und der anschließende Import in Hybrid Runbook Worker ist nicht erforderlich.
* Die Verlängerung des vom ausführenden Konto verwendeten Zertifikats ist nicht erforderlich.
* Die Verarbeitung des Verbindungsobjekts für das ausführende Konto ist nicht erforderlich.

Führen Sie die folgenden Schritte aus, um eine verwaltete Identität für Azure-Ressourcen auf einem Hybrid Runbook Worker zu verwenden:

1. Erstellen einer Azure-VM
2. [Konfigurieren Sie verwaltete Identitäten für Azure-Ressourcen auf Ihrer VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. [Gewähren Sie den Zugriff auf eine Ressourcengruppe in Azure Resource Manager für Ihren virtuellen Computer](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. [Abrufen eines Zugriffstokens mithilfe der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Installieren Sie den Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) auf dem virtuellen Computer.

Nach Abschluss der obigen Schritte können Sie `Connect-AzureRmAccount -Identity` im Runbook verwenden, um die Azure-Ressourcen zu authentifizieren. Diese Konfiguration reduziert die Notwendigkeit zur Nutzung eines ausführenden Kontos und zur Verwaltung des Zertifikats für das ausführende Konto.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Ausführendes Automation-Konto

Im Rahmen Ihres automatisierten Buildprozesses für die Bereitstellung von Ressourcen in Azure ist es unter Umständen erforderlich, auf lokale Systeme zuzugreifen, um eine Aufgabe oder eine Reihe von Schritten in Ihrer Bereitstellungssequenz zu unterstützen. Um die Authentifizierung bei Azure mit einem ausführenden Konto zu unterstützen, müssen Sie das Zertifikat für das ausführende Konto installieren.

Das folgende PowerShell-Runbook, **Export-RunAsCertificateToHybridWorker**, exportiert das Zertifikat für das ausführende Konto aus Ihrem Azure Automation-Konto, lädt es herunter und importiert es in den Zertifikatspeicher für den lokalen Computer auf einem Hybrid Worker, der mit dem gleichen Konto verbunden ist. Sobald dieser Schritt abgeschlossen ist, überprüft das Runbook, ob der Worker sich unter Verwendung des ausführenden Kontos erfolgreich bei Azure authentifizieren kann.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** ist jetzt ein Alias für **Connect-AzureRMAccount**. Sollte **Connect-AzureRMAccount** beim Durchsuchen der Bibliothekselemente nicht angezeigt werden, können Sie **Add-AzureRmAccount** verwenden oder die Module in Ihrem Automation-Konto aktualisieren.

Speichern Sie das *Export-RunAsCertificateToHybridWorker*-Runbook mit der Erweiterung `.ps1` auf Ihrem Computer. Importieren Sie das Runbook in Ihr Automation-Konto, und bearbeiten Sie es. Ändern Sie dabei den Wert der Variablen `$Password` in Ihr eigenes Kennwort. Veröffentlichen Sie das Runbook, und führen Sie es dann aus. Verwenden Sie als Ziel die Hybrid Worker-Gruppe, die Runbooks über das ausführende Konto ausführt und authentifiziert. Der Auftragsdatenstrom meldet den Versuch, das Zertifikat in den Speicher des lokalen Computers zu importieren, und fügt weitere Zeilen mit Informationen hinzu. Dieses Verhalten richtet sich danach, wie viele Automation-Konten Sie in Ihrem Abonnement definieren und ob die Authentifizierung erfolgreich ist.

## <a name="job-behavior"></a>Auftragsverhalten

Aufträge werden auf Hybrid Runbook Workers etwas anders behandelt als bei der Ausführung in Azure Sandboxes. Ein Hauptunterschied besteht darin, dass die Auftragsdauer auf Hybrid Runbook Workers nicht begrenzt ist. In Azure-Sandboxes ausgeführte Runbooks sind zur Sicherstellung einer [gleichmäßigen Verteilung](automation-runbook-execution.md#fair-share) auf 3 Stunden beschränkt. Bei einem Runbook mit langer Ausführungsdauer sollten Sie sicherstellen, dass das Runbook auch nach einem möglichen Neustart stabil läuft. Dies gilt z.B. dann, wenn der Computer neu gestartet wird, der den Hybrid Worker hostet. Wenn der Hostcomputer des Hybridworkers neu gestartet wird, werden alle ausgeführten Runbookaufträge von vorne oder – im Fall von PowerShell-Workflow-Runbooks – beim letzten Prüfpunkt neu gestartet. Nachdem ein Runbookauftrag mehr als drei Mal neu gestartet wurde, wird er gestoppt.

## <a name="run-only-signed-runbooks"></a>Ausschließliches Ausführen signierter Runbooks

Hybrid Runbook Workers können dafür konfiguriert werden, nur signierte Runbooks auszuführen. In folgendem Abschnitt wird beschrieben, wie Sie Hybrid Runbook Worker für die Ausführung signierter [Windows Hybrid Runbook Worker](#windows-hybrid-runbook-worker) und [Linux Hybrid Runbook Worker](#linux-hybrid-runbook-worker) einrichten.

> [!NOTE]
> Sobald Sie einen Hybrid Runbook Worker dafür konfiguriert haben, nur signierte Runbooks auszuführen, schlägt die Ausführung von Runbooks, die **nicht** signiert sind, auf dem Worker fehl.

### <a name="windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker

#### <a name="create-signing-certificate"></a>Auswählen des Signaturzertifikats

In folgendem Beispiel wird ein selbstsigniertes Zertifikat erstellt, das für das Signieren von Runbooks verwendet werden kann. Im Beispiel wird das Zertifikat erstellt und exportiert. Das Zertifikat wird später in die Hybrid Runbook Workers importiert. Der Fingerabdruck wird ebenfalls zurückgegeben und später als Referenz für das Zertifikat verwendet.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

#### <a name="configure-the-hybrid-runbook-workers"></a>Konfigurieren der Hybrid Runbook Workers

Kopieren Sie das erstellte Zertifikat in jeden Hybrid Runbook Worker in einer Gruppe. Führen Sie folgendes Skript aus, um das Zertifikat zu importieren und den Hybrid Worker dafür zu konfigurieren, die Signaturüberprüfung auf Runbooks anzuwenden.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Signieren von Runbooks mithilfe des Zertifikats

Da die Hybrid Runbook Worker nur für die Verwendung von signierten Runbooks konfiguriert sind, müssen Sie Runbooks signieren, die für den Hybrid Runbook Worker verwendet werden sollen. Verwenden Sie folgenden PowerShell-Beispielcode, um Ihre Runbooks zu signieren.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Nachdem das Runbook signiert wurde, muss es in Ihr Automation-Konto importiert und innerhalb des Signaturblocks veröffentlicht werden. Weitere Informationen zum Importieren von Runbooks finden Sie unter [Importieren eines Runbooks aus einer Datei in Azure Automation](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker

Um Runbooks auf einem Linux Hybrid Runbook Worker zu signieren, muss für Ihren Hybrid Runbook Worker die ausführbare [GPG](https://gnupg.org/index.html)-Datei auf dem Computer vorhanden sein.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Erstellen eines GPG-Schlüsselbunds und -Schlüsselpaars

Sie müssen das Hybrid Runbook Worker-Konto `nxautomation` verwenden, um den Schlüsselbund und das Schlüsselpaar zu erstellen.

Verwenden Sie `sudo`, um sich mit dem `nxautomation`-Konto anzumelden.

```bash
sudo su – nxautomation
```

Sobald Sie das `nxautomation`-Konto verwenden, generieren Sie das GPG-Schlüsselpaar.

```bash
sudo gpg --generate-key
```

GPG führt Sie durch die Schritte zur Erstellung des Schlüsselpaares. Sie müssen einen Namen, eine E-Mail-Adresse, eine Ablaufzeit, eine Passphrase und ein Kennwort angeben und auf dem Computer auf eine ausreichende Entropie warten, damit der Schlüssel generiert werden kann.

Da das GPG-Verzeichnis mit sudo generiert wurde, müssen Sie seinen Besitzer in `nxautomation` ändern. 

Führen Sie den folgenden Befehl aus, um den Besitzer zu ändern.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Bereitstellen des Schlüsselbunds für den Hybrid Runbook Worker

Nachdem der Schlüsselbund erstellt wurde, müssen Sie ihn dem Hybrid Runbook Worker zur Verfügung stellen. Ändern Sie die Einstellungsdatei `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf`, um das folgende Beispiel in den Abschnitt `[worker-optional]` einzubeziehen.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Überprüfen Sie, ob die Signaturüberprüfung aktiviert ist.

Wenn die Signaturüberprüfung auf dem Computer deaktiviert wurde, müssen Sie sie aktivieren. Führen Sie den folgenden Befehl aus, um die Signaturüberprüfung zu aktivieren. Ersetzen Sie `<LogAnalyticsworkspaceId>` durch Ihre Arbeitsbereich-ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Signieren eines Runbooks

Nachdem die Signaturüberprüfung konfiguriert ist, können Sie mit dem folgenden Befehl ein Runbook signieren:

```bash
gpg –-clear-sign <runbook name>
```

Das signierte Runbook erhält den Namen `<runbook name>.asc`.

Das signierte Runbook kann jetzt in Azure Automation hochgeladen und wie ein normales Runbook ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den verschiedenen Methoden, die zum Starten eines Runbooks verwendet werden können, finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).
* Informationen dazu, wie Sie auf unterschiedliche Weise in einem Text-Editor mit PowerShell-Runbooks in Azure Automation arbeiten, finden Sie unter [Bearbeiten eines Runbooks in Azure Automation](automation-edit-textual-runbook.md).
* Wenn Ihre Runbooks nicht erfolgreich ausgeführt werden, lesen Sie den Problembehandlungsleitfaden zu [Fehlern bei der Ausführung von Runbooks](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).