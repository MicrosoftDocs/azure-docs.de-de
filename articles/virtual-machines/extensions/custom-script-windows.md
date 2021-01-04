---
title: Benutzerdefinierte Skripterweiterung von Azure für Windows
description: Automatisieren von Konfigurationsaufgaben für virtuelle Windows-Computer mithilfe der benutzerdefinierten Skripterweiterung
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: 8d11ff6eaab8ed6a13c3c2aa1b712cc57e7825ea
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960970"
---
# <a name="custom-script-extension-for-windows"></a>CustomScript-Erweiterung für Windows

Die benutzerdefinierte Skripterweiterung lädt Skripts auf virtuelle Azure-Computer herunter und führt sie aus. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations- oder Verwaltungsaufgaben. Skripts können aus Azure Storage oder GitHub heruntergeladen oder dem Azure-Portal zur Laufzeit für die Erweiterung bereitgestellt werden. Die benutzerdefinierte Skripterweiterung kann in Azure Resource Manager-Vorlagen integriert und über die Azure-Befehlszeilenschnittstelle, PowerShell, das Azure-Portal oder die Azure Virtual Machines-REST-API ausgeführt werden.

In diesem Dokument erfahren Sie, wie Sie die benutzerdefinierte Skripterweiterung über das Azure PowerShell-Modul ausführen. Sie erfahren, wie Sie eine Azure Resource Manager-Vorlage verwenden, und erhalten Informationen zu Problembehandlungsschritten für Windows-Systeme.

## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]  
> Verwenden Sie die benutzerdefinierte Skripterweiterung nicht, um „Update-AzVM“ mit der gleichen VM als Parameter auszuführen, da der Befehl dann auf sich selbst wartet.  

### <a name="operating-system"></a>Betriebssystem

Die Erweiterung für benutzerdefinierte Skripts für Windows kann auf den von Erweiterungen unterstützten Betriebssystemen ausgeführt werden.
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Speicherort des Skripts

Sie können die Erweiterung so konfigurieren, dass mit Ihren Azure Blob Storage-Anmeldeinformationen auf Azure Blob Storage zugegriffen wird. Das Skript kann an einem beliebigen Ort gespeichert werden, solange der virtuelle Computer ein Routing an diesen Endpunkt (GitHub oder interner Dateiserver) durchführen kann.

### <a name="internet-connectivity"></a>Internetverbindung

Wenn Sie ein Skript extern herunterladen möchten, etwa von GitHub oder Azure Storage, müssen zusätzliche Firewall-/Netzwerksicherheitsgruppen-Ports geöffnet werden. Wenn sich Ihr Skript beispielsweise in Azure Storage befindet, können Sie Zugriff über Azure-NSG-Diensttags für [Storage](../../virtual-network/network-security-groups-overview.md#service-tags) gewähren.

Befindet sich Ihr Skript auf einem lokalen Server, müssen eventuell dennoch weitere Firewall- und Netzwerksicherheitsgruppen-Ports geöffnet werden.

### <a name="tips-and-tricks"></a>Tipps und Tricks

* Die meisten Fehler für diese Erweiterung sind auf Syntaxfehler im Skript zurückzuführen. Vergewissern Sie sich, dass das Skript fehlerfrei ausgeführt wird, und versehen Sie es zusätzlich mit einer Protokollierung, um die Fehlersuche zu erleichtern.
* Schreiben Sie idempotente Skripts. Dadurch wird sichergestellt, dass bei versehentlicher erneuter Ausführung keine Systemänderungen auftreten.
* Stellen Sie sicher, dass während der Skriptausführung keine Benutzereingaben erforderlich sind.
* Die Skriptausführung darf maximal 90 Minuten dauern. Danach gilt die Bereitstellung der Erweiterung als nicht erfolgreich.
* Das Skript darf keine Systemneustarts enthalten, da diese Aktion zu Problemen mit anderen Erweiterungen führt, die installiert werden. Außerdem wird die Erweiterung nach dem Neustart nicht fortgesetzt.
* Wenn Sie ein Skript haben, das zu einem Neustart führt und dann Anwendungen installiert und Skripts ausführt, können Sie den Neustart mit einer Windows-Planungsaufgabe planen oder Tools wie DSC, Chef oder Puppet-Erweiterungen verwenden.
* Es wird nicht empfohlen, ein Skript auszuführen, das bewirkt, dass der VM-Agent angehalten oder aktualisiert wird. Das kann dazu führen, dass die Erweiterung in einem Übergangszustand verbleibt, wodurch ein Timeout verursacht wird.
* Die Erweiterung führt jedes Skript nur einmal aus. Soll ein Skript bei jedem Neustart des Systems ausgeführt werden, müssen Sie die Erweiterung zum Erstellen eines geplanten Windows-Tasks verwenden.
* Wenn Sie den Ausführungszeitpunkt eines Skripts planen möchten, erstellen Sie mithilfe der Erweiterung einen geplanten Windows-Task.
* Während der Skriptausführung wird im Azure-Portal sowie in der CLI nur ein Übergangsstatus für die Erweiterung angezeigt. Sollten Sie häufigere Statusaktualisierungen für ein ausgeführtes Skript benötigen, müssen Sie eine eigene Lösung erstellen.
* Die Erweiterung für benutzerdefinierte Skripts verfügt über keine native Proxyserverunterstützung. Sie können innerhalb Ihres Skripts jedoch ein Dateiübertragungstool mit Proxyserverunterstützung verwenden (beispielsweise *cURL*).
* Achten Sie auf nicht standardmäßige Verzeichnispfade, von denen Ihre Skripts oder Befehle gegebenenfalls abhängen, und verwenden Sie eine entsprechende Logik, um diese Situation zu behandeln.
* Die benutzerdefinierte Skripterweiterung wird unter dem Konto „LocalSystem“ ausgeführt.
* Wenn Sie die Eigenschaften *storageAccountName* und *storageAccountKey* verwenden möchten, müssen diese Eigenschaften in *protectedSettings* angeordnet sein.

## <a name="extension-schema"></a>Erweiterungsschema

In der Konfiguration der benutzerdefinierten Skripterweiterung werden Aspekte wie der Skriptspeicherort und der auszuführende Befehl angegeben. Sie können diese Konfiguration in Konfigurationsdateien speichern oder sie in der Befehlszeile oder in einer Azure Resource Manager-Vorlage angeben.

Sie können sensible Daten in einer geschützten Konfiguration speichern. Diese ist verschlüsselt und wird nur auf dem virtuellen Computer entschlüsselt. Die geschützte Konfiguration ist hilfreich, wenn der Ausführungsbefehl vertrauliche Informationen (beispielsweise ein Kennwort) enthält.

Diese Elemente müssen als vertrauliche Daten behandelt und in der Konfiguration mit den geschützten Einstellungen der Erweiterung angegeben werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> Die managedIdentity-Eigenschaft **darf nicht** in Verbindung mit den Eigenschaften „storageAccountName“ oder "storageAccountKey" verwendet werden.

> [!NOTE]
> Auf einem virtuellen Computer kann jeweils nur eine Version einer Erweiterung installiert werden. Wenn Sie die Erweiterung für benutzerdefinierte Skripts zweimal in derselben Resource Manager-Vorlage für denselben Computer angeben, treten Fehler auf.

> [!NOTE]
> Dieses Schema kann in der VirtualMachine-Ressource oder als eigenständige Ressource verwendet werden. Der Name der Ressource muss das Format „virtualMachineName/extensionName“ haben, wenn diese Erweiterung als eigenständige Ressource in der ARM-Vorlage verwendet wird.

### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | Zeichenfolge |
| type | CustomScriptExtension | Zeichenfolge |
| typeHandlerVersion | 1.10 | INT |
| fileUris (Beispiel) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (Beispiel) | 123456789 | 32-bit integer |
| commandToExecute (Beispiel) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | Zeichenfolge |
| storageAccountName (Beispiel) | examplestorageacct | Zeichenfolge |
| storageAccountKey (Beispiel) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | Zeichenfolge |
| managedIdentity (z. B.) | { } oder { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } oder { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | JSON-Objekt |

>[!NOTE]
>Bei Eigenschaftennamen wird zwischen Groß- und Kleinschreibung unterschieden. Um Bereitstellungsprobleme zu vermeiden, verwenden Sie die Namen wie hier gezeigt.

#### <a name="property-value-details"></a>Details zu Eigenschaftswerten

* `commandToExecute` (**erforderlich**, Zeichenfolge): Das auszuführende Skript für den Einstiegspunkt. Verwenden Sie dieses Feld, wenn der Befehl Geheimnisse (z.B. Kennwörter) enthält oder die „fileUris“ vertraulich sind.
* `fileUris` (optional, Zeichenfolgenarray): die URLs für die herunterzuladenden Dateien.
* `timestamp` (optional, 32-Bit-Integer): Durch Ändern dieses Felds können Sie eine erneute Ausführung des Skripts auslösen.  Jeder Integerwert ist akzeptabel; er muss sich lediglich vom vorherigen Wert unterscheiden.
* `storageAccountName` (optional, Zeichenfolge): der Name des Speicherkontos. Wenn Sie Speicheranmeldeinformationen angeben, muss es sich bei allen `fileUris` um URLs für Azure-Blobs handeln.
* `storageAccountKey` (optional, Zeichenfolge): der Zugriffsschlüssel des Speicherkontos.
* `managedIdentity` (optional, JSON-Objekt): die [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) zum Herunterladen von Dateien
  * `clientId` (optional, Zeichenfolge): die Client-ID der verwalteten Identität
  * `objectId` (optional, Zeichenfolge): die Objekt-ID der verwalteten Identität

Die folgenden Werte können in öffentlichen oder geschützten Einstellungen festgelegt werden. Die Erweiterung lehnt jedoch jede Konfiguration ab, bei der die Werte sowohl in öffentlichen als auch in geschützten Einstellungen festgelegt sind.

* `commandToExecute`

Die Verwendung öffentlicher Einstellungen kann zwar beim Debuggen hilfreich sein, es wird jedoch empfohlen, geschützte Einstellungen zu verwenden.

Öffentliche Einstellungen werden in Klartext an den virtuellen Computer gesendet, auf dem das Skript ausgeführt wird.  Geschützte Einstellungen werden mit einem Schlüssel verschlüsselt, der nur in Azure und auf dem virtuellen Computer bekannt ist. Die Einstellungen werden unverändert auf dem virtuellen Computer gespeichert. Waren die Einstellungen also verschlüsselt, werden sie auch verschlüsselt auf dem virtuellen Computer gespeichert. Das Zertifikat zum Entschlüsseln der verschlüsselten Werte wird auf dem virtuellen Computer gespeichert und gegebenenfalls zur Laufzeit zum Entschlüsseln der Einstellungen verwendet.

####  <a name="property-managedidentity"></a>Eigenschaft: managedIdentity
> [!NOTE]
> Diese Eigenschaft **muss** nur in geschützten Einstellungen angegeben werden.

CustomScript (ab Version 1.10) unterstützt [verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) zum Herunterladen von Dateien von URLs, die in der Einstellung „fileUris“ angegeben werden. So kann CustomScript auf private Azure Storage-Blobs oder -Container zugreifen, ohne dass der Benutzer Geheimnisse wie SAS-Token oder Speicherkontoschlüssel übergeben muss.

Um diese Funktion verwenden zu können, muss der Benutzer der VM oder VMSS, auf der CustomScript ausgeführt werden soll, eine [vom System zugewiesene](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) oder [vom Benutzer zugewiesene](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) Identität hinzufügen und [der verwalteten Identität Zugriff auf den Azure Storage-Container oder das -Blob gewähren](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Um die vom System zugewiesene Identität für die Ziel-VM/VMSS zu verwenden, legen Sie das Feld „managedidentity“ auf ein leeres JSON-Objekt fest. 

> Beispiel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Um die vom Benutzer zugewiesene Identität für die Ziel-VM/VMSS zu verwenden, konfigurieren Sie das Feld „managedidentity“ mit der Client-ID oder der Objekt-ID der verwalteten Identität.

> Beispiele:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> Die managedIdentity-Eigenschaft **darf nicht** in Verbindung mit den Eigenschaften storageAccountName oder storageAccountKey verwendet werden.

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt erläuterte JSON-Schema kann in einer Azure Resource Manager-Vorlage zum Ausführen der benutzerdefinierten Skripterweiterung bei der Bereitstellung verwendet werden. Die folgenden Beispiele veranschaulichen die Verwendung der Erweiterung für benutzerdefinierte Skripts:

* [Tutorial: Bereitstellen von VM-Erweiterungen mithilfe von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Deploy Two Tier Application on Windows and Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) (Bereitstellen einer Zwei-Ebenen-Anwendung unter Windows und Azure SQL-Datenbank)

## <a name="powershell-deployment"></a>PowerShell-Bereitstellung

Mit dem `Set-AzVMCustomScriptExtension`-Befehl können Sie die benutzerdefinierte Skripterweiterung einem vorhandenen virtuellen Computer bereitstellen. Weitere Informationen finden Sie unter [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Weitere Beispiele

### <a name="using-multiple-scripts"></a>Verwenden mehrerer Skripts

In diesem Beispiel verfügen Sie über drei Skripts, die zum Erstellen des Servers verwendet werden. **commandToExecute** ruft das erste Skript auf, danach stehen mehrere Optionen zum Aufruf der anderen zur Verfügung. Beispielsweise könnten Sie über ein Masterskript die Ausführung steuern und die richtige Fehlerbehandlung, Protokollierung und Zustandsverwaltung sicherstellen. Die Skripts werden zum Ausführen auf den lokalen Computer heruntergeladen. Beispielsweise können Sie in `1_Add_Tools.ps1` das Skript `2_Add_Features.ps1` durch Hinzufügen von `.\2_Add_Features.ps1` zum Skript aufrufen und diesen Vorgang für die anderen in `$settings` definierten Skripts wiederholen.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Ausführen von Skripts über eine lokale Freigabe

In diesem Beispiel sollten Sie eventuell einen lokalen SMB-Server für Ihren Skriptspeicherort verwenden. Dadurch müssen Sie keine weiteren Einstellungen außer **commandToExecute** angeben.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Mehrmaliges Ausführen der Erweiterung für benutzerdefinierte Skripts über die Befehlszeilenschnittstelle

Wenn Sie die Erweiterung für benutzerdefinierte Skripts mehrmals ausführen möchten, ist diese Aktion nur unter folgenden Bedingungen möglich:

* Der Parameter **Name** der Erweiterung ist identisch mit dem der vorherigen Bereitstellung der Erweiterung.
* Sie müssen die Konfiguration aktualisieren, andernfalls wird der Befehl nicht erneut ausgeführt. In dem Befehl können Sie eine dynamische Eigenschaft hinzufügen, z.B. „timestamp“.

Alternativ können Sie die [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag)-Eigenschaft auf **true** festlegen.

### <a name="using-invoke-webrequest"></a>Verwenden von Invoke-WebRequest

Wenn Sie [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) in Ihrem Skript verwenden, müssen Sie den Parameter `-UseBasicParsing` angeben. Andernfalls erhalten Sie den folgenden Fehler beim Überprüfen des detaillierten Status:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Informationen zum Bereitstellen der benutzerdefinierten Skripterweiterung für eine Skalierungsgruppe finden Sie unter [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0).

## <a name="classic-vms"></a>Klassische virtuelle Computer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Zum Bereitstellen der benutzerdefinierten Skripterweiterung auf klassischen virtuellen Computern können Sie das Azure-Portal oder klassische Azure PowerShell-Cmdlets verwenden.

### <a name="azure-portal"></a>Azure-Portal

Navigieren Sie zu Ihrer klassischen VM-Ressource. Wählen Sie unter **Einstellungen** die Option **Erweiterungen** aus.

Klicken Sie auf **+ Hinzufügen**, und wählen Sie in der Liste der Ressourcen **Benutzerdefinierte Skripterweiterung** aus.

Wählen Sie auf der Seite **Erweiterung installieren** die lokale PowerShell-Datei aus, geben Sie alle Argumente ein, und klicken Sie auf **Ok**.

### <a name="powershell"></a>PowerShell

Mit dem Cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) können Sie die benutzerdefinierte Skripterweiterung für einen vorhandenen virtuellen Computer bereitstellen.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe des Azure PowerShell-Moduls abgerufen werden. Führen Sie den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Die Ausgabe der Erweiterung wird in Dateien im folgenden Ordner auf dem virtuellen Zielcomputer protokolliert.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Die angegebenen Dateien werden in den folgenden Ordner auf dem virtuellen Zielcomputer heruntergeladen.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

Hierbei ist `<n>` eine dezimale Ganzzahl, die sich zwischen verschiedenen Ausführungen der Erweiterung ändern kann.  Der Wert `1.*` entspricht dem tatsächlichen aktuellen `typeHandlerVersion`-Wert der Erweiterung.  Das tatsächliche Verzeichnis könnte z.B. folgendermaßen lauten: `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Beim Ausführen des Befehls `commandToExecute` legt die Erweiterung dieses Verzeichnis (z.B. `...\Downloads\2`) als aktuelles Arbeitsverzeichnis fest. Dieser Prozess ermöglicht die Verwendung relativer Pfade, um die heruntergeladenen Dateien mithilfe der `fileURIs`-Eigenschaft aufzufinden. Beispiele hierfür finden Sie in der unten stehenden Tabelle.

Da sich der absolute Downloadpfad im Lauf der Zeit ändern kann, empfiehlt es sich, in der Zeichenfolge `commandToExecute` nach Möglichkeit relative Pfade zu Skripts und Dateien zu verwenden. Beispiel:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Pfadinformationen nach dem ersten URI-Segment werden für Dateien beibehalten, die über die Eigenschaftenliste `fileUris` heruntergeladen werden.  Wie in der Tabelle unten gezeigt, werden heruntergeladene Dateien zu Unterverzeichnissen für den Download zugeordnet, um die Struktur der `fileUris`-Werte widerzuspiegeln.  

#### <a name="examples-of-downloaded-files"></a>Beispiele für heruntergeladene Dateien

| URI in „fileUris“ | Relativer Speicherort für Downloads | Absoluter Speicherort für Downloads<sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Absolute Verzeichnispfade ändern sich während der Lebensdauer eines virtuellen Computers, nicht jedoch innerhalb einer einzelnen Ausführung der CustomScript-Erweiterung.

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Sie können auch einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).