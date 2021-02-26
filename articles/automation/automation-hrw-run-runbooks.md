---
title: Ausführen von Azure Automation-Runbooks in einem Hybrid Runbook Worker
description: In diesem Artikel wird beschrieben, wie Sie Runbooks auf Computern in Ihrem lokalen Rechenzentrum oder bei anderen Cloudanbietern mit dem Hybrid Runbook Worker ausführen.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2021
ms.topic: conceptual
ms.openlocfilehash: 8ea668ab2266a1deae108542687c89f3a221568e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220972"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Ausführen von Runbooks in einer Hybrid Runbook Worker-Instanz

Von Runbooks, die in einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ausgeführt werden, werden in der Regel Ressourcen auf dem lokalen Computer oder in der lokalen Umgebung verwaltet, in der der Worker bereitgestellt ist. Runbooks in Azure Automation verwalten üblicherweise Ressourcen in der Azure-Cloud. Auch wenn sie unterschiedlich verwendet werden, sind Runbooks, die in Azure Automation ausgeführt werden, und Runbooks, die auf einem Hybrid Runbook Worker ausgeführt werden, von der Struktur her identisch.

Wenn Sie ein Runbook zur Ausführung auf einem Hybrid Runbook Worker erstellen, sollten Sie es auf dem Computer testen, der den Worker hostet. Auf dem Hostcomputer stehen alle PowerShell-Module sowie der Netzwerkzugriff zur Verfügung, die bzw. den Sie für die Verwaltung der lokalen Ressourcen benötigen. Sobald Sie das Runbook auf dem Hybrid Runbook Worker-Computer getestet haben, können Sie es in die Azure Automation-Umgebung hochladen, wo es auf dem Worker ausgeführt werden kann.

## <a name="plan-runbook-job-behavior"></a>Planen des Verhaltens von Runbookaufträgen

Azure Automation behandelt Aufträge in Hybrid Runbook Workern anders als Aufträge, die in Azure-Sandboxes ausgeführt werden. Bei einem zeitintensiven Runbook müssen Sie sicherstellen, dass das Runbook auch nach einem möglichen Neustart stabil läuft. Ausführliche Informationen zum Auftragsverhalten finden Sie unter [Hybrid Runbook Worker-Aufträge](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Aufträge für Hybrid Runbook Worker werden unter Windows mit dem lokalen Konto **System** oder unter Linux mit dem Konto **nxautomation** ausgeführt. Stellen Sie unter Linux sicher, dass das Konto **nxautomation** Zugriff auf den Speicherort der Runbookmodule hat. Wenn Sie das Cmdlet [Install-Module](/powershell/module/powershellget/install-module) verwenden, stellen Sie sicher, dass Sie „AllUsers“ für den Parameter `Scope` angeben, um sicherzustellen, dass das Konto **nxautomation** Zugriff hat. Weitere Informationen zu PowerShell unter Linux finden Sie unter [Bekannte Probleme bei PowerShell auf anderen Plattformen als Windows](/powershell/scripting/whats-new/what-s-new-in-powershell-70).

## <a name="configure-runbook-permissions"></a>Konfigurieren von Runbookberechtigungen

Definieren Sie Berechtigungen für Ihr Runbook, um die Ausführung im Hybrid Runbook Worker zu ermöglichen:

* Lassen Sie das Runbook sich selbst bei lokalen Ressourcen authentifizieren.
* Konfigurieren Sie die Authentifizierung mit [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Geben Sie ein ausführendes Konto an, um einen Benutzerkontext für alle Runbooks bereitzustellen.

### <a name="use-runbook-authentication-to-local-resources"></a>Verwenden der Runbookauthentifizierung bei lokalen Ressourcen

Wenn Sie ein Runbook vorbereiten, das sich selbst bei Ressourcen authentifiziert, verwenden Sie in Ihrem Runbook die Objekte für [Anmeldeinformationen](./shared-resources/credentials.md) und [Zertifikat](./shared-resources/certificates.md). Es gibt verschiedene Cmdlets, mit denen Sie Anmeldeinformationen angeben können, damit sich das Runbook bei verschiedenen Ressourcen authentifizieren kann. Das folgende Beispiel zeigt einen Teil eines Runbooks, mit dem ein Computer neu gestartet wird. Es werden Anmeldeinformationen aus einem Anmeldeinformationsobjekt und der Name des Computers aus einem Variablenobjekt abgerufen. Anschließend werden diese Werte im Cmdlet `Restart-Computer` eingesetzt.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Sie können auch eine Aktivität des Typs [InlineScript](automation-powershell-workflow.md#use-inlinescript) verwenden. `InlineScript` ermöglicht das Ausführen von Codeblöcken auf einem anderen Computer mit Anmeldeinformationen.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Verwenden der Runbookauthentifizierung mit verwalteten Identitäten

Von Hybrid Runbook Workern, die auf virtuellen Azure-Computern ausgeführt werden, können verwaltete Identitäten für die Authentifizierung bei Azure-Ressourcen genutzt werden. Der Einsatz verwalteter Identitäten für Azure-Ressourcen anstelle ausführender Konten bietet Vorteile, da die folgenden Schritte nicht erforderlich sind:

* Das Exportieren des Zertifikats für das ausführende Konto und anschließende Importieren in den Hybrid Runbook Worker.
* Das Verlängern des vom ausführenden Konto verwendeten Zertifikats.
* Das Verarbeiten des Verbindungsobjekts des ausführenden Kontos.

Führen Sie die folgenden Schritte aus, um eine verwaltete Identität für Azure-Ressourcen auf einem Hybrid Runbook Worker zu verwenden:

1. Erstellen Sie eine Azure-VM.
2. Konfigurieren Sie verwaltete Identitäten für Azure-Ressourcen auf der VM. Weitere Informationen finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Gewähren Sie der VM Zugriff auf eine Ressourcengruppe in Resource Manager. Weitere Informationen finden Sie unter [Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installieren Sie den Hybrid Runbook Worker auf dem virtuellen Computer. Weitere Informationen finden Sie unter [Bereitstellen eines Windows Hybrid Runbook Workers](automation-windows-hrw-install.md) bzw. unter [Bereitstellen eines Linux-Hybrid Runbook Workers](automation-linux-hrw-install.md).
5. Aktualisieren Sie das Runbook so, dass das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) mit dem Parameter `Identity` zur Authentifizierung bei Azure-Ressourcen verwendet wird. Diese Konfiguration reduziert die Notwendigkeit, ein ausführendes Konto zu verwenden und die zugehörige Kontoverwaltung durchzuführen.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` funktioniert für einen Hybrid Runbook Worker mit einer vom System und einer einzelnen vom Benutzer zugewiesenen Identität. Wenn Sie auf dem Hybrid Runbook Worker mehrere vom Benutzer zugewiesene Identitäten verwenden, muss Ihr Runbook für `Connect-AzAccount` den Parameter `AccountId` angeben, um eine bestimmte vom Benutzer zugewiesene Identität auszuwählen.

### <a name="use-runbook-authentication-with-run-as-account"></a>Verwenden der Runbookauthentifizierung mit ausführendem Konto

Um zu vermeiden, dass Ihre Runbooks ihre eigene Authentifizierung bei lokalen Ressourcen bereitstellen müssen, können Sie für eine Hybrid Runbook Worker-Gruppe ein ausführendes Konto angeben. Damit Sie ein ausführendes Konto angeben können, müssen Sie ein [Anmeldeinformationsobjekt](./shared-resources/credentials.md) mit Zugriff auf lokale Ressourcen definieren. Zu diesen Ressourcen gehören Zertifikatsspeicher und alle Runbooks, die unter diesen Anmeldeinformationen auf einem Hybrid Runbook Worker in der Gruppe ausgeführt werden.

Der Benutzername für die Anmeldeinformationen muss eines der folgenden Formate haben:

* Domäne\Benutzername
* username@domain
* Benutzername (für Konten, die für den lokalen Computer lokal sind)

Gehen Sie wie folgt vor, um ein ausführendes Konto für eine Hybrid Runbook Worker-Gruppe anzugeben:

1. Erstellen Sie ein [Anmeldeinformationsobjekt](./shared-resources/credentials.md) mit Zugriff auf lokale Ressourcen.
2. Wählen Sie im Azure-Portal das Automation-Konto aus.
3. Wählen Sie **Hybrid Worker-Gruppen** und dann eine bestimmte Gruppe aus.
4. Wählen Sie **Alle Einstellungen** gefolgt von **Einstellungen für Hybrid Worker-Gruppe** aus.
5. Ändern Sie die Einstellung von **Ausführen als** von **Standard** in **Benutzerdefiniert**.
6. Wählen Sie die Anmeldeinformationen aus, und klicken Sie auf **Speichern**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Installieren das Zertifikats für das ausführende Konto

Im Rahmen Ihres automatisierten Buildprozesses für die Bereitstellung von Ressourcen in Azure ist es unter Umständen erforderlich, auf lokale Systeme zuzugreifen, um eine Aufgabe oder eine Reihe von Schritten in Ihrer Bereitstellungssequenz zu unterstützen. Um die Authentifizierung bei Azure mit einem ausführenden Konto zu ermöglichen, müssen Sie das Zertifikat für das ausführende Konto installieren.

>[!NOTE]
>Dieses PowerShell-Runbook wird derzeit nicht auf Linux-Computern ausgeführt. Es wird nur auf Windows-Computern ausgeführt.
>

Das folgende PowerShell-Runbook, **Export-RunAsCertificateToHybridWorker**, exportiert das Zertifikat für das ausführende Konto aus Ihrem Azure Automation-Konto. Es lädt das Zertifikat herunter und importiert es in den Zertifikatspeicher für den lokalen Computer auf einem Hybrid Runbook Worker, der mit dem gleichen Konto verbunden ist. Nach diesem Schritt überprüft das Runbook, ob der Worker sich unter Verwendung des ausführenden Kontos erfolgreich bei Azure authentifizieren kann.

>[!NOTE]
>Dieses PowerShell-Runbook ist nicht für die Ausführung außerhalb Ihres Automation-Kontos als Skript auf dem Zielcomputer vorgesehen.
>

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
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

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Für PowerShell-Runbooks sind `Add-AzAccount` und `Add-AzureRMAccount` Aliase für `Connect-AzAccount`. Wenn `Connect-AzAccount` beim Durchsuchen der Bibliothekselemente nicht angezeigt wird, können Sie `Add-AzAccount` verwenden oder Ihre Module in Ihrem Automation-Konto aktualisieren.

So schließen Sie die Vorbereitung des ausführenden Kontos ab

1. Speichern Sie das Runbook **Export-RunAsCertificateToHybridWorker** mit der Erweiterung **.ps1** auf Ihrem Computer.
2. Importieren Sie es in Ihr Automation-Konto.
3. Bearbeiten Sie das Runbook, indem Sie den Wert der Variable `Password` in Ihr eigenes Kennwort ändern.
4. Veröffentlichen Sie das Runbook.
5. Führen Sie das Runbook dann mit der Hybrid Runbook Worker-Gruppe als Ziel aus, die Runbooks mithilfe des ausführenden Kontos ausführt und authentifiziert. 
6. Im Auftragsdatenstrom sehen Sie, dass versucht wurde, das Zertifikat in den Speicher des lokalen Computers zu importieren (gefolgt von mehreren Zeilen). Dieses Verhalten hängt von der Anzahl der Automation-Konten, die Sie in Ihrem Abonnement definieren, und vom Grad des Erfolgs der Authentifizierung ab.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Arbeiten mit signierten Runbooks auf einem Windows Hybrid Runbook Worker

Sie können eine Windows Hybrid Runbook Worker so konfigurieren, dass nur signierte Runbooks ausgeführt werden.

> [!IMPORTANT]
> Wenn Sie einen Hybrid Runbook Worker dafür konfiguriert haben, nur signierte Runbooks auszuführen, können von dem Worker keine nicht signierten Runbooks mehr ausgeführt werden.

### <a name="create-signing-certificate"></a>Erstellen des Signaturzertifikats

In folgendem Beispiel wird ein selbstsigniertes Zertifikat erstellt, das für das Signieren von Runbooks verwendet werden kann. Mit diesem Code wird das Zertifikat erstellt und exportiert, damit der Runbook Worker es später importieren kann. Der Fingerabdruck wird auch für die spätere Verwendung beim Verweisen auf das Zertifikat zurückgegeben.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importieren von Zertifikaten und Konfigurieren von Workern für die Signaturprüfung

Kopieren Sie das erstellte Zertifikat in jeden Hybrid Runbook Worker in einer Gruppe. Führen Sie das folgende Skript aus, um das Zertifikat zu importieren und die Worker so zu konfigurieren, dass die Signaturüberprüfung auf Runbooks verwendet wird.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Signieren von Runbooks mithilfe des Zertifikats

Da die Hybrid Runbook Worker nur für die Verwendung signierter Runbooks konfiguriert sind, müssen Sie Runbooks signieren, die auf dem Hybrid Runbook Worker verwendet werden sollen. Verwenden Sie den folgenden PowerShell-Beispielcode, um diese Runbooks zu signieren.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Nachdem ein Runbook signiert wurde, muss es in Ihr Automation-Konto importiert und mithilfe des Signaturblocks veröffentlicht werden. Informationen zum Importieren von Runbooks finden Sie unter [Importieren eines Runbooks](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Arbeiten mit signierten Runbooks auf einem Linux Hybrid Runbook Worker

Um mit signierten Runbooks arbeiten zu können, muss ein Linux Hybrid Runbook Worker auf dem lokalen Computer über die [GPG](https://gnupg.org/index.html)-Datei verfügen.

> [!IMPORTANT]
> Wenn Sie einen Hybrid Runbook Worker dafür konfiguriert haben, nur signierte Runbooks auszuführen, können von dem Worker keine nicht signierten Runbooks mehr ausgeführt werden.

Führen Sie die folgenden Schritte aus, um die Konfiguration abzuschließen:

* Erstellen eines GPG-Schlüsselbunds und -Schlüsselpaars
* Bereitstellen des Schlüsselbunds für den Hybrid Runbook Worker
* Überprüfen, ob die Signaturüberprüfung aktiviert ist
* Signieren eines Runbooks

### <a name="create-a-gpg-keyring-and-keypair"></a>Erstellen eines GPG-Schlüsselbunds und -Schlüsselpaars

Verwenden Sie das Hybrid Runbook Worker-Konto [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux), um den GPG-Schlüsselbund und das GPG-Schlüsselpaar zu erstellen.

1. Verwenden Sie die Anwendung sudo, um sich mit dem Konto **nxautomation** anzumelden.

    ```bash
    sudo su – nxautomation
    ```

2. Sobald Sie **nxautomation** verwenden, generieren Sie das GPG-Schlüsselpaar. GPG führt Sie durch die Schritte. Sie müssen Name, E-Mail-Adresse, Ablaufzeit und Passphrase angeben. Warten Sie anschließend, bis die Entropie auf dem Computer ausreicht, um den Schlüssel zu generieren.

    ```bash
    sudo gpg --generate-key
    ```

3. Da das GPG-Verzeichnis mit sudo generiert wurde, müssen Sie seinen Besitzer mit dem folgenden Befehl in **nxautomation** ändern.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Bereitstellen des Schlüsselbunds für den Hybrid Runbook Worker

Nachdem der Schlüsselbund erstellt wurde, stellen Sie ihn dem Hybrid Runbook Worker zur Verfügung. Ändern Sie die Einstellungsdatei **home/nxautomation/state/worker.conf** so, dass sie im Dateiabschnitt `[worker-optional]` den folgenden Beispielcode enthält.

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Überprüfen, ob die Signaturüberprüfung aktiviert ist

Wenn die Signaturprüfung auf dem Computer deaktiviert wurde, müssen Sie sie durch Ausführen des folgenden sudo-Befehls aktivieren. Ersetzen Sie `<LogAnalyticsworkspaceId>` durch die ID Ihres Arbeitsbereichs.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Signieren eines Runbooks

Signieren Sie das Runbook nach dem Konfigurieren der Signaturprüfung mit dem folgenden GPG-Befehl.

```bash
gpg –-clear-sign <runbook name>
```

Das signierte Runbook heißt **<runbook name>.asc**.

Sie können nun das signierte Runbook in Azure Automation hochladen und es wie ein herkömmliches Runbook ausführen.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Starten eines Runbooks auf einem Hybrid Runbook Worker

Unter [Starten eines Runbooks in Azure Automation](start-runbooks.md) werden die verschiedenen Methoden zum Starten eines Runbooks beschrieben. Beim Starten eines Runbooks auf einem Hybrid Runbook Worker wird die Option **Ausführen auf** verwendet, mit der Sie den Namen einer Hybrid Runbook Worker-Gruppe angeben können. Wenn eine Gruppe angegeben wird, ruft einer der Worker in dieser Gruppe das Runbook ab und führt es aus. Wenn Ihr Runbook diese Option nicht angibt, führt Azure Automation das Runbook wie gewohnt aus.

Wenn Sie ein Runbook im Azure-Portal starten, wird die Option **Ausführen auf** angezeigt, für die Sie entweder **Azure** oder **Hybrid Worker** auswählen können. Bei Auswahl von **Hybrid Worker** können Sie anschließend in einer Dropdownliste die gewünschte Hybrid Runbook Worker-Gruppe wählen.

Wenn Sie ein Runbook mit PowerShell starten, verwenden Sie das Cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) mit dem Parameter `RunOn`. Das folgende Beispiel verwendet Windows PowerShell, um ein Runbook namens **Test-Runbook** in einer Hybrid Runbook Worker-Gruppe namens MyHybridGroup zu starten.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Protokollierung

Zur Unterstützung der Problembehandlung bei Runbooks, die auf einem Hybrid Runbook Worker ausgeführt werden, werden Protokolle lokal am folgenden Speicherort gespeichert:

* Unter Windows unter `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` mit einer ausführlichen Protokollierung des Laufzeitprozesses des Auftrags. Allgemeine Runbook-Auftragsstatusereignisse werden in das Ereignisprotokoll **Application and Services Logs\Microsoft-Automation\Operations** geschrieben.

* Unter Linux finden Sie die Benutzerprotokolle für Hybrid Worker unter `/home/nxautomation/run/worker.log` und die Systemprotokolle für Runbook Worker unter `/var/opt/microsoft/omsagent/run/automationworker/worker.log`.

## <a name="next-steps"></a>Nächste Schritte

* Wenn Ihre Runbooks nicht erfolgreich ausgeführt werden, lesen Sie den Problembehandlungsleitfaden zu [Fehlern bei der Ausführung von Runbooks](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Weitere Informationen zu PowerShell, einschließlich Sprachverzeichnis und Lernmodulen, finden Sie in der [PowerShell-Dokumentation](/powershell/scripting/overview).
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
