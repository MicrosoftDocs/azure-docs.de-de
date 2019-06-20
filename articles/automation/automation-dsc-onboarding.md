---
title: Onboarding von Computern zur Verwaltung durch Azure Automation DSC
description: Hier erfahren Sie, wie Sie Computer mit Azure Automation DSC (Desired State Configuration) für die Verwaltung einrichten.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 8a505e88ff92c5227d3b42da2adaf1dce58e6fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65441516"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboarding von Computern zur Verwaltung durch Azure Automation DSC

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Gründe für das Verwalten von Computern mit Azure Automation DSC

Azure Automation DSC ist ein Konfigurationsverwaltungsdienst für DSC-Knoten in jedem beliebigen Cloud- oder lokalen Rechenzentrum.
Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen.
Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.
Der Azure Automation DSC-Dienst ist für DSC, was die Azure Automation-Runbooks für PowerShell-Skripts sind.
So hilft Azure Automation auf die gleiche Weise wie beim Verwalten von PowerShell-Skripts bei der Verwaltung von DSC-Konfigurationen.
Weitere Informationen zu den Vorteilen von Azure Automation DSC finden Sie unter [Azure Automation DSC – Übersicht](automation-dsc-overview.md).

Mit Azure Automation DSC können zahlreiche Computer verwaltet werden:

- Azure Virtual Machines
- Virtuelle Azure-Computer (klassisch)
- Amazon Web Services-EC2-Instanzen (AWS)
- Physische/virtuelle Windows-Computer, lokal oder in einer anderen Cloud als Azure/AWS
- Physische/virtuelle Linux-Computer, lokal, in Azure oder in einer anderen Cloud als Azure

Wenn Sie Computerkonfigurationen noch nicht in der Cloud verwalten möchten, können Sie Azure Automation DSC auch ausschließlich als Endpunkt für Berichte verwenden.
Dadurch können Sie Konfigurationen mittels DSC festlegen (Push) und Berichterstellungsdetails in Azure Automation anzeigen.

> [!NOTE]
> Die Verwaltung von Azure-VMs mit DSC ist kostenlos inbegriffen, wenn die installierte DSC-Erweiterung für virtuelle Computer eine höhere Version als 2.70 aufweist. Weitere Informationen finden Sie auf der [**Preisseite für Automation**](https://azure.microsoft.com/pricing/details/automation/).

In den folgenden Abschnitten wird beschrieben, wie Sie jeden Computertyp in Azure Automation DSC integrieren können.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Mit Azure Automation DSC können Sie problemlos Azure-VMs für die Konfigurationsverwaltung integrieren, indem Sie das Azure-Portal, Azure Resource Manager-Vorlagen oder PowerShell verwenden. Ohne Remotezugriff eines Administrators auf den virtuellen Computer registriert die Azure-VM-Erweiterung für DSC den virtuellen Computer bei Azure Automation DSC.
Da die Azure-VM-Erweiterung für DSC asynchron ausgeführt wird, finden Sie die Schritte zum Verfolgen des Fortschritts oder zur Problembehandlung im Abschnitt [**Problembehandlung bei der Integration virtueller Azure-Computer**](#troubleshooting-azure-virtual-machine-onboarding) weiter unten.

### <a name="azure-portal"></a>Azure-Portal

Wechseln Sie im [Azure-Portal](https://portal.azure.com/)zu dem Azure Automation-Konto, in das Sie virtuelle Computer integrieren möchten. Klicken Sie auf der DSC-Seite auf der Registerkarte **Knoten** auf **Hinzufügen**.

Wählen Sie einen virtuellen Azure-Computer aus, der integriert werden soll.

Wenn auf dem Computer nicht die PowerShell-Erweiterung für Konfiguration des gewünschten Zustands installiert ist und der Energiezustand ausgeführt wird, klicken Sie auf **Verbinden**.

Geben Sie unter **Registrierung**die Werte des [lokalen Konfigurations-Managers von PowerShell DSC](/powershell/dsc/metaconfig4) ein, die für Ihren Anwendungsfall erforderlich sind, und geben Sie optional eine Knotenkonfiguration an, die dem virtuellen Computer zugewiesen werden soll.

![Onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen

Azure-VMs können über Azure Resource Manager-Vorlagen in Azure Automation DSC bereitgestellt und integriert werden. Unter [Vom DSC-Dienst verwalteter Server](https://azure.microsoft.com/resources/templates/101-automation-configuration/) finden Sie eine Beispielvorlage zum Integrieren eines vorhandenen virtuellen Computers in Azure Automation DSC.
Wenn Sie eine VM-Skalierungsgruppe verwalten, finden Sie Informationen in der Beispielvorlage [Von Azure Automation verwaltete VM-Skalierungsgruppenkonfiguration](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

Mit dem Cmdlet [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) können virtuelle Computer im Azure-Portal über PowerShell integriert werden.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrieren von virtuellen Computern über Azure-Abonnements hinweg

Die beste Methode zum Registrieren virtueller Computer aus anderen Azure-Abonnements ist die Verwendung der DSC-Erweiterung in einer Azure Resource Manager-Bereitstellungsvorlage.
Beispiele finden Sie in [Erweiterung zum Konfigurieren des gewünschten Zustands mit Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Informationen zum Suchen von Registrierungsschlüssel und Registrierungs-URL, die als Parameter in der Vorlage verwendet werden, finden Sie im Abschnitt [**Sichere Registrierung**](#secure-registration) weiter unten.

## <a name="amazon-web-services-aws-virtual-machines"></a>Virtuelle Computer von Amazon Web Services (AWS)

Sie können VMs von Amazon Web Services mithilfe des AWS DSC-Toolkits einfach in die Konfigurationsverwaltung von Azure Automation DSC integrieren. Weitere Informationen zu diesem Toolkit finden Sie [hier](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Physische/virtuelle Windows-Computer, lokal oder in einer anderen Cloud als Azure/AWS

Windows-Server, die lokal oder in anderen Cloudumgebungen ausgeführt werden, können ebenfalls in Azure Automation State Configuration integriert werden, solange sie über [ausgehenden Zugriff auf Azure](automation-dsc-overview.md#network-planning) verfügen:

1. Überprüfen Sie, ob die neueste Version von [WMF 5](https://aka.ms/wmf5latest) auf den Computern installiert ist, die Sie in Azure Automation DSC integrieren möchten.
1. Führen Sie die Schritte im Abschnitt [**Generieren von DSC-Metakonfigurationen**](#generating-dsc-metaconfigurations) weiter unten aus, um einen Ordner mit den erforderlichen DSC-Metakonfigurationen zu generieren.
1. Wenden Sie die PowerShell DSC-Metakonfiguration remote auf die Computer an, die Sie integrieren möchten. **Auf dem Computer, auf dem dieser Befehl ausgeführt wird, muss die neueste Version von [WMF 5](https://aka.ms/wmf5latest) installiert sein**

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Wenn Sie die PowerShell DSC-Metakonfigurationen nicht remote anwenden können, kopieren Sie den Metakonfigurationsordner aus Schritt 2 auf jeden Computer, den Sie integrieren möchten. Rufen Sie dann **Set-DscLocalConfigurationManager** lokal auf jedem Computer auf, den Sie integrieren möchten.
1. Überprüfen Sie mit dem Azure-Portal oder den Cmdlets, ob die Computer, die Sie integrieren möchten, jetzt als DSC-Knoten angezeigt werden, die in Ihrem Azure Automation-Konto registriert sind.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Physische/virtuelle Linux-Computer, lokal oder in einer anderen Cloud als Azure

Linux-Server, die lokal oder in anderen Cloudumgebungen ausgeführt werden, können ebenfalls in Azure Automation State Configuration integriert werden, solange sie über [ausgehenden Zugriff auf Azure](automation-dsc-overview.md#network-planning) verfügen:

1. Überprüfen Sie, ob die neueste Version der [PowerShell Desired State Configuration für Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) auf den Computern installiert ist, die Sie in Azure Automation DSC integrieren möchten.
1. Gehen Sie folgendermaßen vor, wenn die [Standardwerte des lokalen Konfigurations-Managers von PowerShell DSC](/powershell/dsc/metaconfig4) zu Ihrem Anwendungsfall passen, und Sie Computer so integrieren möchten, dass diese **sowohl** Informationen von Azure Automation DSC abrufen als auch Informationen an Azure Automation DSC senden:

   - Verwenden Sie auf jedem Linux-Computer, den Sie in Azure Automation State Configuration integrieren möchten, `Register.py` für das Onboarding mit den Standardwerten des lokalen Konfigurations-Managers von PowerShell DSC:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Informationen zum Suchen von Registrierungsschlüssel und Registrierungs-URL für Ihr Automation-Konto finden Sie im Abschnitt [**Sichere Registrierung**](#secure-registration) weiter unten.

     Wenn die Standardwerte des lokalen Konfigurations-Managers von PowerShell DSC **nicht** zu Ihrem Anwendungsfall passen, oder Sie Computer so integrieren möchten, dass diese Informationen an Azure Automation DSC senden, befolgen Sie die Schritte 3 bis 6. Fahren Sie andernfalls direkt mit Schritt 6 fort.

1. Führen Sie die Schritte im Abschnitt [**Generieren von DSC-Metakonfigurationen**](#generating-dsc-metaconfigurations) weiter unten aus, um einen Ordner mit den erforderlichen DSC-Metakonfigurationen zu generieren.
1. Wenden Sie die PowerShell DSC-Metakonfiguration remote auf die Computer an, die Sie integrieren möchten:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Auf dem Computer, auf dem dieser Befehl ausgeführt wird, muss die neueste Version von [WMF 5](https://aka.ms/wmf5latest) installiert sein.

1. Wenn Sie die PowerShell DSC-Metakonfigurationen nicht remote anwenden können, kopieren Sie die Metakonfiguration für diesen Computer aus dem Ordner in Schritt 5 auf den Linux-Computer. Rufen Sie anschließend `SetDscLocalConfigurationManager.py` lokal auf jedem Linux-Computer auf, den Sie in Azure Automation DSC integrieren möchten:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Überprüfen Sie mit dem Azure-Portal oder den Cmdlets, ob die Computer, die Sie integrieren möchten, jetzt als DSC-Knoten angezeigt werden, die in Ihrem Azure Automation-Konto registriert sind.

## <a name="generating-dsc-metaconfigurations"></a>Generieren von DSC-Metakonfigurationen

Sie können eine [DSC-Metakonfiguration](/powershell/dsc/metaconfig) generieren, um einen beliebigen Computer allgemein in Azure Automation DSC zu integrieren, die den DSC-Agent anweist, Informationen von Azure Automation DSC abzurufen und/oder Informationen an Azure Automation DSC zu senden. Die DSC-Metakonfigurationen für Azure Automation DSC können sowohl über eine PowerShell DSC-Konfiguration als auch über die Azure Automation PowerShell-Cmdlets generiert werden.

> [!NOTE]
> DSC-Metakonfigurationen enthalten die notwendigen geheimen Schlüssel, um einen Computer in ein Automation-Konto für die Verwaltung zu integrieren. Stellen Sie den ordnungsgemäßen Schutz aller von Ihnen generierten DSC-Metakonfigurationen sicher oder löschen Sie diese nach der Verwendung.

### <a name="using-a-dsc-configuration"></a>Verwenden einer DSC-Konfiguration

1. Öffnen Sie VS Code (oder einen anderen Editor) als Administrator auf einem Computer in Ihrer lokalen Umgebung. Auf dem Computer muss die neueste Version von [WMF 5](https://aka.ms/wmf5latest) installiert sein.
1. Kopieren Sie das folgende Skript in Ihre lokale Umgebung. Dieses Skript enthält eine PowerShell DSC-Konfiguration zum Erstellen von Metakonfigurationen und einen Befehl zum Starten der Metakonfigurationserstellung.

> [!NOTE]
> Bei DSC-Knotenkonfigurationsnamen wird im Portal die Groß-/Kleinschreibung beachtet. Bei abweichender Groß-/Kleinschreibung wird der Knoten nicht auf der Registerkarte **Knoten** angezeigt.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Geben Sie den Registrierungsschlüssel und die URL für Ihr Automation-Konto sowie die Namen der Computer ein, die integriert werden sollen. Alle anderen Parameter sind optional. Informationen zum Suchen von Registrierungsschlüssel und Registrierungs-URL für Ihr Automation-Konto finden Sie im Abschnitt [**Sichere Registrierung**](#secure-registration) weiter unten.
1. Wenn die Computer zwar DSC-Statusinformationen an Azure Automation DSC senden, jedoch keine Konfiguration oder PowerShell-Module abrufen sollen, legen Sie den **ReportOnly**-Parameter auf TRUE fest.
1. Führen Sie das Skript aus. Ihr Arbeitsverzeichnis sollte nun einen Ordner namens **DscMetaConfigs** mit den PowerShell DSC-Metakonfigurationen der zu integrierenden Computer (als Administrator) enthalten.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Verwenden der Azure Automation-Cmdlets

Wenn die Standardwerte des lokalen Konfigurations-Managers von PowerShell DSC zu Ihrem Anwendungsfall passen, und Sie Computer so integrieren möchten, dass diese sowohl Informationen von Azure Automation DSC abrufen als auch Informationen an Azure Automation DSC senden, können Sie mit den Azure Automation-Cmdlets die benötigten DSC-Metakonfigurationen einfach generieren:

1. Öffnen Sie die PowerShell-Konsole oder VS Code als Administrator auf einem Computer in Ihrer lokalen Umgebung.
2. Stellen Sie mit `Connect-AzureRmAccount` eine Verbindung mit Azure Resource Manager her.
3. Laden Sie von dem Automation-Konto, in das Sie Knoten integrieren möchten, die PowerShell DSC-Metakonfigurationen für die Computer herunter, die Sie integrieren möchten:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Ihr Arbeitsverzeichnis sollte nun einen Ordner namens ***DscMetaConfigs***mit den PowerShell DSC-Metakonfigurationen der zu integrierenden Computer (als Administrator) enthalten.

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Sichere Registrierung

Computer können über das WMF 5-DSC-Registrierungsprotokoll sicher in ein Azure Automation-Konto integriert werden. Mit diesem Protokoll kann ein DSC-Knoten bei einem PowerShell DSC-Pull- oder -Berichtsserver authentifiziert werden (einschließlich Azure Automation DSC). Der Knoten wird beim Server an einer **Registrierungs-URL** registriert und mit einem **Registrierungsschlüssel** authentifiziert. Während der Registrierung handeln der DSC-Knoten und der DSC-Pull-/-Berichtsserver ein eindeutiges Zertifikat für diesen Knoten aus, das zur Authentifizierung beim Server nach der Registrierung verwendet wird. Durch diesen Prozess wird verhindert, dass integrierte Knoten die Identität eines anderen Knotens annehmen, z. B. wenn ein Knoten kompromittiert wurde und böswillige Absichten verfolgt. Nach der Registrierung wird der Registrierungsschlüssel nicht wieder für die Authentifizierung verwendet und vom Knoten gelöscht.

Die für das DSC-Registrierungsprotokoll erforderlichen Informationen finden Sie im Azure-Portal in den **Kontoeinstellungen** unter **Schlüssel**. Öffnen Sie dieses Blatt, indem Sie im Bereich **Essentials** für das Automation-Konto auf das Schlüsselsymbol klicken.

![Azure Automation-Schlüssel und -URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Die Registrierungs-URL ist das Feld "URL" auf dem Blatt "Schlüssel verwalten".
- Der Registrierungsschlüssel ist der primäre Zugriffsschlüssel oder der sekundäre Zugriffsschlüssel auf dem Blatt "Schlüssel verwalten". Beide Schlüssel können verwendet werden.

Für eine verbesserte Sicherheit können die primären und sekundären Zugriffsschlüssel eines Automation-Kontos jederzeit generiert werden (auf der Seite **Schlüssel verwalten**), um künftige Knotenregistrierungen mit alten Schlüsseln zu verhindern.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Problembehandlung bei der Integration virtueller Azure-Computer

Mit Azure Automation DSC können Sie problemlos Azure-VMs für die Konfigurationsverwaltung integrieren. Die Azure-VM-Erweiterung für DSC wird verwendet, um virtuelle Computer bei Azure Automation DSC zu registrieren. Da die Azure-VM-Erweiterung für DSC asynchron ausgeführt wird, können die Überwachung des Fortschritts und die Problembehandlung bei der Ausführung wichtig sein.

> [!NOTE]
> Beim Onboarding einer Azure-VM in Azure Automation DSC mithilfe der Azure-VM-Erweiterung für DSC kann es bis zu einer Stunde dauern, bis angezeigt wird, dass der Knoten in Azure Automation registriert ist. Dies liegt an der Installation von Windows Management Framework 5.0 auf dem virtuellen Computer durch die Azure-VM-Erweiterung für DSC, mit der der virtuelle Computer in Azure Automation DSC integriert werden muss.

Zur Problembehandlung oder Anzeige des Status der Azure-VM-Erweiterung für DSC navigieren Sie im Azure-Portal zu dem virtuellen Computer, der integriert wird, und klicken Sie dann auf unter **Einstellungen** auf **Erweiterungen**. Klicken Sie dann je nach Betriebssystem auf **DSC** oder **DSCForLinux**. Weitere Details erhalten Sie, indem Sie auf **Detaillierten Status anzeigen**klicken.

## <a name="certificate-expiration-and-reregistration"></a>Ablauf und erneute Registrierung eines Zertifikats

Nach der Registrierung eines Computers als DSC-Knoten in Azure Automation DSC gibt es eine Reihe von Gründen, warum Sie diesen Knoten in Zukunft erneut registrieren müssen:

- Nach der Registrierung handelt jeder Knoten automatisch ein eindeutiges Zertifikat für die Authentifizierung aus, die nach einem Jahr abläuft. Gegenwärtig kann das PowerShell DSC-Registrierungsprotokoll Zertifikate nicht automatisch erneuern, wenn sie sich dem Ablaufdatum nähern, weshalb Sie die Knoten nach einem Jahr erneut registrieren müssen. Stellen Sie vor einer erneuten Registrierung sicher, dass jeder Knoten Windows Management Framework 5.0 RTM ausführt. Wenn das Authentifizierungszertifikat eines Knotens abläuft und er nicht erneut registriert wird, kann der Knoten nicht mit Azure Automation kommunizieren und wird als „Nicht reagierend“ markiert. Eine erneute Registrierung, die 90 Tage oder weniger vor Ablaufzeitpunkt des Zertifikats oder zu einem beliebigen Zeitpunkt nach dessen Ablaufzeitpunkt durchgeführt wird, hat zur Folge, dass ein neues Zertifikat generiert und verwendet wird.
- Zum Ändern von [Werten des lokalen Konfigurations-Managers von PowerShell DSC](/powershell/dsc/metaconfig4), die während der anfänglichen Registrierung des Knotens festgelegt wurden, z.B. „ConfigurationMode“. Diese DSC-Agent-Werte können derzeit nur über eine erneute Registrierung geändert werden. Die einzige Ausnahme ist die Knotenkonfiguration, die dem Knoten zugewiesen ist. Diese kann in Azure Automation DSC direkt geändert werden.

Eine erneute Registrierung kann auf die gleiche Weise wie beim ersten Registrieren des Knotens mithilfe einer der in diesem Dokument beschriebenen Methoden für die Integration erfolgen. Sie müssen die Registrierung eines Knotens in Azure Automation DSC nicht aufheben, bevor Sie ihn erneut registrieren.

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation DSC](automation-dsc-compile.md).
- Eine PowerShell-Cmdlet-Referenz ist unter [Azure Automation State Configuration-Cmdlets](/powershell/module/azurerm.automation/#automation) verfügbar.
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Verwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Continuous Deployment mit Azure Automation State Configuration und Chocolatey](automation-dsc-cd-chocolatey.md).
