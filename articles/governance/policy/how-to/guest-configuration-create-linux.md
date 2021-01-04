---
title: Erstellen von Richtlinien für Gastkonfigurationen für Linux
description: Hier wird beschrieben, wie Sie eine Azure Policy-Richtlinie für Gastkonfigurationen für Linux erstellen.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1f6308250717d35dc725b097575bf3921646c6a0
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302707"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Erstellen von Richtlinien für Gastkonfigurationen für Linux

Lesen Sie vor dem Erstellen benutzerdefinierter Richtlinien die allgemeinen Informationen zur [Azure Policy-Gastkonfiguration](../concepts/guest-configuration.md).
 
Informationen zum Erstellen von Richtlinien für Gastkonfigurationen für Windows finden Sie auf der Seite [Erstellen von Richtlinien für Gastkonfigurationen für Windows](./guest-configuration-create.md).

Beim Überwachen von Linux verwendet die Gastkonfiguration [Chef InSpec](https://www.inspec.io/). Das InSpec-Profil definiert den Zustand, in dem sich der Computer befinden soll. Wenn bei der Auswertung der Konfiguration ein Fehler auftritt, wird die Richtlinienauswirkung **auditIfNotExists** ausgelöst, und der Computer wird als **nicht konform** eingestuft.

Die [Azure Policy-Gastkonfiguration](../concepts/guest-configuration.md) kann nur zur Überwachung von Einstellungen in Computern verwendet werden. Die Wiederherstellung von Einstellungen in Computern ist noch nicht verfügbar.

Verwenden Sie die folgenden Aktionen, um Ihre eigene Konfiguration zum Überprüfen des Zustands eines Azure- oder Nicht-Azure-Computers zu erstellen.

> [!IMPORTANT]
> Bei benutzerdefinierten Richtliniendefinitionen mit Gastkonfiguration in den Umgebungen „Azure Government“ und „Azure China“ handelt es sich um eine Previewfunktion.
>
> Die Gastkonfigurationserweiterung ist zum Durchführen von Überprüfungen in virtuellen Azure-Computern erforderlich. Weisen Sie die folgende Richtliniendefinition zu, um die Erweiterung auf allen Linux-Computern im gewünschten Umfang bereitzustellen: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`
> 
> Verwenden Sie in Paketen mit benutzerdefiniertem Inhalt keine Geheimnisse oder vertraulichen Informationen.

## <a name="install-the-powershell-module"></a>Installieren des PowerShell-Moduls

Mit dem Modul „Gastkonfiguration“ wird die Erstellung von benutzerdefinierten Inhalten automatisiert, z. B.:

- Erstellen eines Inhaltsartefakts für die Gastkonfiguration (ZIP-Datei)
- Durchführen eines automatisierten Tests des Artefakts
- Erstellen einer Richtliniendefinition
- Veröffentlichen der Richtlinie

Das Modul kann auf einem Computer installiert werden, auf dem Windows, macOS oder Linux mit PowerShell 6.2 oder höher lokal oder mit [Azure Cloud Shell](https://shell.azure.com) oder dem [Azure PowerShell Core Docker-Image](https://hub.docker.com/r/azuresdk/azure-powershell-core) ausgeführt wird.

> [!NOTE]
> Die Kompilierung von Konfigurationen wird unter Linux nicht unterstützt.

### <a name="base-requirements"></a>Basisanforderungen

Betriebssysteme, unter denen das Modul installiert werden kann:

- Linux
- macOS
- Windows

> [!NOTE]
> Für das Cmdlet `Test-GuestConfigurationPackage` ist die OpenSSL-Version 1.0 erforderlich, da eine Abhängigkeit von OMI besteht. Dies führt zu einem Fehler in Umgebungen mit OpenSSL 1.1 oder höher.
>
> Das Ausführen des Cmdlets `Test-GuestConfigurationPackage` wird nur unter Windows für die Version 2.1.0 des Gastkonfigurationsmoduls unterstützt.

Für das Ressourcenmodul für Gastkonfigurationen wird die folgende Software benötigt:

- PowerShell 6.2 oder höher. Falls es noch nicht installiert ist, befolgen Sie [diese Anweisungen](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 oder höher. Falls es noch nicht installiert ist, befolgen Sie [diese Anweisungen](/powershell/azure/install-az-ps).
  - Nur die Az-Module „Az.Accounts“ und „Az.Resources“ sind erforderlich.

### <a name="install-the-module"></a>Installieren des Moduls

Führen Sie zum Installieren des Moduls **GuestConfiguration** in PowerShell die folgenden Schritte aus:

1. Führen Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl aus:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Überprüfen Sie, ob das Modul importiert wurde:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefakte und Richtlinien für Gastkonfigurationen für Linux

Auch in Linux-Umgebungen verwendet die Gastkonfiguration die Konfiguration des gewünschten Zustands (Desired State Configuration, DSC) als Sprachabstraktion. Die Implementierung basiert auf nativem Code (C++), sodass ein Laden von PowerShell nicht erforderlich ist. Es wird jedoch eine MOF-Konfiguration benötigt, die Details der Umgebung beschreibt.
DSC fungiert als Wrapper für InSpec, um dessen Ausführung, die Bereitstellung von Parametern und die Rückgabe von Ausgaben an den Dienst zu standardisieren. Bei der Arbeit mit benutzerdefinierten InSpec-Inhalten sind nur geringe Kenntnisse über DSC erforderlich.

#### <a name="configuration-requirements"></a>Konfigurationsanforderungen

Der Name der benutzerdefinierten Konfiguration muss überall einheitlich sein. Der Name der ZIP-Datei für das Inhaltspaket, der Konfigurationsname in der MOF-Datei und der Name der Gastzuweisung in der Azure Resource Manager-Vorlage (ARM-Vorlage) müssen identisch sein.

Zum Erstellen des Pakets können PowerShell-Cmdlets verwendet werden.
Es ist kein Ordner auf Stammebene oder Versionsordner erforderlich.
Das Paketformat muss eine ZIP-Datei sein. Die Gesamtgröße darf unkomprimiert 100 MB nicht überschreiten.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Benutzerdefinierte Konfiguration für Gastkonfigurationen unter Linux

Für die Gastkonfiguration unter Linux wird die Ressource `ChefInSpecResource` verwendet, um die Engine mit dem Namen des [InSpec-Profils](https://www.inspec.io/docs/reference/profiles/) anzugeben. **Name** ist die einzige erforderliche Ressourceneigenschaft. Erstellen Sie eine YAML-Datei und eine Ruby-Skriptdatei wie nachfolgend beschrieben.

Erstellen Sie zuerst die YAML-Datei, die von InSpec verwendet wird. Die Datei enthält grundlegende Informationen zur Umgebung. Nachfolgend sehen Sie ein Beispiel:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Speichern Sie diese Datei mit dem Namen `inspec.yml` in einem Ordner namens `linux-path` in Ihrem Projektverzeichnis.

Erstellen Sie dann die Ruby-Datei mit der InSpec-Sprachabstraktion, die zum Überwachen des Computers verwendet wird.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Speichern Sie diese Datei mit dem Namen `linux-path.rb` in einem neuen Ordner namens `controls` im Verzeichnis `linux-path`.

Erstellen Sie abschließend eine Konfiguration, importieren Sie das Ressourcenmodul **PSDesiredStateConfiguration**, und kompilieren Sie die Konfiguration.

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

Speichern Sie diese Datei mit dem Namen `config.ps1` im Projektordner. Führen Sie sie in PowerShell aus, indem Sie `./config.ps1` im Terminal ausführen. Eine neue MOF-Datei wird erstellt.

Der Befehl `Node AuditFilePathExists` ist aus technischer Sicht nicht erforderlich, doch wird damit eine Datei namens `AuditFilePathExists.mof` anstelle der Standarddatei `localhost.mof` erstellt. Wenn der Name der MOF-Datei der Konfiguration folgt, können beim Arbeiten in großem Umfang viele Dateien problemlos organisiert werden.

Sie sollten jetzt über folgende Projektstruktur verfügen:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb 
```

Die unterstützenden Dateien müssen in einem Paket zusammengefasst werden. Das fertige Paket wird von der Gastkonfiguration verwendet, um die Azure Policy-Definitionen zu erstellen.

Mit dem Cmdlet `New-GuestConfigurationPackage` wird das Paket erstellt. Parameter des Cmdlets `New-GuestConfigurationPackage` beim Erstellen von Linux-Inhalten:

- **Name**: Name des Pakets mit der Gastkonfiguration.
- **Konfiguration:** Vollständiger Pfad für das kompilierte Konfigurationsdokument.
- **Pfad**: Pfad des Ausgabeordners. Dieser Parameter ist optional. Wenn er nicht angegeben ist, wird das Paket im aktuellen Verzeichnis erstellt.
- **ChefInspecProfilePath**: Vollständiger Pfad zum InSpec-Profil. Dieser Parameter wird nur unterstützt, wenn Inhalt für die Linux-Überprüfung erstellt wird.

Führen Sie den folgenden Befehl aus, um ein Paket mit der im vorherigen Schritt angegebenen Konfiguration zu erstellen:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Nach dem Erstellen des Konfigurationspakets – und vor der Veröffentlichung in Azure – können Sie das Paket über Ihre Arbeitsstation oder CI/CD-Umgebung (Continuous Integration/Continuous Deployment) testen. Das GuestConfiguration-Cmdlet `Test-GuestConfigurationPackage` enthält denselben Agent in Ihrer Entwicklungsumgebung, der auch auf Azure-Computern genutzt wird. Mit dieser Lösung können Sie Integrationstests lokal durchführen, bevor die Veröffentlichung für kostenpflichtige Cloudumgebungen erfolgt.

Da der Agent tatsächlich die lokale Umgebung auswertet, müssen Sie in den meisten Fällen das Test-Cmdlet auf derselben Betriebssystemplattform ausführen, die Sie überwachen möchten.

Parameter des Cmdlets `Test-GuestConfigurationPackage`:

- **Name**: Name der Richtlinie für Gastkonfigurationen.
- **Parameter**: Richtlinienparameter im Hashtabellenformat.
- **Pfad**: Vollständiger Pfad des Pakets mit der Gastkonfiguration.

Führen Sie den folgenden Befehl aus, um das im vorherigen Schritt erstellte Paket zu testen:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Das Cmdlet unterstützt auch Eingaben aus der PowerShell-Pipeline. Fügen Sie die Ausgabe des Cmdlets `New-GuestConfigurationPackage` per Pipezeichen an das Cmdlet `Test-GuestConfigurationPackage` an.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

Im nächsten Schritt wird die Datei in Azure Blob Storage veröffentlicht.  Für den Befehl `Publish-GuestConfigurationPackage` ist das `Az.Storage`-Modul erforderlich.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Nachdem ein benutzerdefiniertes Richtlinienpaket für Gastkonfigurationen erstellt und hochgeladen wurde, erstellen Sie die Richtliniendefinition für Gastkonfigurationen. Das Cmdlet `New-GuestConfigurationPolicy` verwendet ein benutzerdefiniertes Richtlinienpaket und erstellt eine Richtliniendefinition.

Parameter des Cmdlets `New-GuestConfigurationPolicy`:

- **ContentUri**: Öffentlicher HTTP(S)-URI des Pakets mit dem Inhalt der Gastkonfiguration.
- **DisplayName**: Anzeigename der Richtlinie.
- **Beschreibung**: Beschreibung der Richtlinie.
- **Parameter**: Richtlinienparameter im Hashtabellenformat.
- **Version**: Version der Richtlinie.
- **Pfad**: Zielpfad, unter dem Richtliniendefinitionen erstellt werden.
- **Plattform**: Zielplattform (Windows/Linux) für das Paket mit den Richtlinien und dem Inhalt der Gastkonfiguration.
- Mit **Tag** werden der Richtliniendefinition ein oder mehrere Tags hinzugefügt.
- Mit **Category** wird das Feld mit den Kategoriemetadaten in der Richtliniendefinition festgelegt.

Im folgenden Beispiel werden die Richtliniendefinitionen in einem angegebenen Pfad aus einem benutzerdefinierten Richtlinienpaket erstellt:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Mit `New-GuestConfigurationPolicy` werden die folgenden Dateien erstellt:

- **auditIfNotExists.json**

In der Ausgabe des Cmdlets wird ein Objekt zurückgegeben, das den Anzeigenamen der Initiative und den Pfad der Richtliniendateien enthält.

Abschließend veröffentlichen Sie die Richtliniendefinitionen mit dem Cmdlet `Publish-GuestConfigurationPolicy`. Das Cmdlet verfügt nur über den Parameter **Path**, mit dem auf den Speicherort der JSON-Dateien verwiesen wird, die mit `New-GuestConfigurationPolicy` erstellt werden.

Um den Veröffentlichungsbefehl auszuführen, benötigen Sie Zugriff zum Erstellen von Richtlinien in Azure. Die entsprechenden Autorisierungsanforderungen sind auf der Seite mit der [Übersicht über Azure Policy](../overview.md) dokumentiert. Die beste integrierte Rolle ist **Mitwirkender bei Ressourcenrichtlinien**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

 Das Cmdlet `Publish-GuestConfigurationPolicy` akzeptiert den Pfad von der PowerShell-Pipeline. Dank dieses Features können Sie die Richtliniendateien erstellen und veröffentlichen, indem Sie nur eine Befehlszeile verwenden, in der die Befehle per Pipezeichen verknüpft sind.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Bei der in Azure erstellten Richtlinie ist der letzte Schritt das Zuweisen der Definition. Informieren Sie sich darüber, wie Sie die Definition über das [Portal](../assign-policy-portal.md), die [Azure CLI](../assign-policy-azurecli.md) oder über [Azure PowerShell](../assign-policy-powershell.md) zuweisen können.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Verwenden von Parametern in benutzerdefinierten Richtlinien für Gastkonfigurationen

Die Gastkonfiguration unterstützt das Außerkraftsetzen von Eigenschaften einer Konfiguration während der Laufzeit. Dieses Feature bewirkt, dass die Werte in der MOF-Datei im Paket nicht als statisch angesehen werden müssen. Die Überschreibungswerte werden über Azure Policy bereitgestellt und wirken sich nicht darauf aus, wie die Konfigurationen erstellt oder kompiliert werden.

Mit InSpec werden Parameter in der Regel entweder als Eingabe zur Laufzeit oder als Code mithilfe von Attributen gehandhabt. Die Gastkonfiguration verbirgt diesen Prozess, sodass die Eingabe beim Zuweisen der Richtlinie bereitgestellt werden kann. Es wird automatisch eine Attributdatei auf dem Computer erstellt. Sie müssen keine Datei in Ihrem Projekt erstellen und hinzufügen. Zum Hinzufügen von Parametern zum Linux-Überwachungsprojekt müssen zwei Schritte ausgeführt werden.

Definieren Sie die Eingabe in der Ruby-Datei, in der Sie festlegen, was auf dem Computer überwacht werden soll. Nachfolgend sehen Sie ein Beispiel.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Die Cmdlets `New-GuestConfigurationPolicy` und `Test-GuestConfigurationPolicyPackage` enthalten einen Parameter mit dem Namen **Parameter**. Für diesen Parameter wird eine Hashtabelle verwendet, die alle Details zu den einzelnen Parametern enthält, und es werden automatisch alle erforderlichen Abschnitte der Dateien erstellt, die für die Erstellung der einzelnen Azure Policy-Definitionen verwendet werden.

Im folgenden Beispiel wird eine Richtliniendefinition zum Überwachen eines Dateipfads erstellt, wobei der Benutzer den Pfad zum Zeitpunkt der Richtlinienzuweisung angibt.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

Fügen Sie für Linux-Richtlinien die **AttributesYmlContent**-Eigenschaft in Ihre Konfiguration ein, und überschreiben Sie die Werte nach Bedarf. Der Gastkonfigurations-Agent erstellt automatisch die YAML-Datei, die von InSpec zum Speichern der Attribute genutzt wird. Betrachten Sie das folgende Beispiel.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Lebenszyklus von Richtlinien

Wenn Sie eine Aktualisierung für die Richtliniendefinition veröffentlichen möchten, sind drei Felder von Bedeutung.

> [!NOTE]
> Die Eigenschaft `version` der Gastkonfigurationszuweisung wirkt sich nur auf Pakete aus, die von Microsoft gehostet werden. Bei der Versionsverwaltung für benutzerdefinierte Inhalte hat sich die Best Practice etabliert, die Version in den Dateinamen aufzunehmen.

- **Version**: Beim Ausführen des Cmdlets `New-GuestConfigurationPolicy` müssen Sie eine Versionsnummer angeben, die höher als die der derzeitigen Veröffentlichung ist.
- **contentUri**: Wenn Sie das Cmdlet `New-GuestConfigurationPolicy` ausführen, müssen Sie einen URI zum Speicherort des Pakets angeben. Durch Einschließen einer Paketversion in den Dateinamen wird sichergestellt, dass sich der Wert dieser Eigenschaft in jedem Release ändert.
- **contentHash**: Diese Eigenschaft wird vom Cmdlet `New-GuestConfigurationPolicy` automatisch aktualisiert. Es handelt sich um einen Hashwert des Pakets, das mit `New-GuestConfigurationPackage` erstellt wurde. Diese Eigenschaft muss für die von Ihnen veröffentlichte Datei vom Typ `.zip` stimmen. Wenn nur die Eigenschaft **contentUri** aktualisiert wird, akzeptiert die Erweiterung das Inhaltspaket nicht.

Die einfachste Möglichkeit zum Freigeben eines aktualisierten Pakets ist das Wiederholen des Prozesses in diesem Artikel und das Angeben einer aktualisierten Versionsnummer. Mit dieser Vorgehensweise wird sichergestellt, dass alle Eigenschaften richtig aktualisiert wurden.

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtern von Richtlinien der Gastkonfiguration mit Tags

Die mit Cmdlets im Gastkonfigurationsmodul erstellten Richtlinien können optional einen Filter für Tags enthalten. Der Parameter **-Tag** von `New-GuestConfigurationPolicy` unterstützt ein Array mit Hashtabellen, die die einzelnen Tageinträge enthalten. Die Tags werden dem Abschnitt `If` der Richtliniendefinition hinzugefügt und können nicht per Richtlinienzuweisung geändert werden.

Unten ist ein Beispielcodeausschnitt einer Richtliniendefinition angegeben, mit der nach Tags gefiltert wird.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Optional: Signieren von Paketen für Gastkonfigurationen

Benutzerdefinierte Richtlinien für Gastkonfigurationen verwenden SHA256-Hash, um zu überprüfen, ob sich das Richtlinienpaket nicht geändert hat.
Optional können Kunden auch ein Zertifikat nutzen, um Pakete zu signieren und für die Gastkonfigurationserweiterung zu erzwingen, dass nur signierte Inhalte zulässig sind.

Für dieses Szenario müssen Sie zwei Schritte ausführen. Führen Sie das Cmdlet zum Signieren des Inhaltspakets aus, und fügen Sie ein Tag an die Computer an, auf denen das Signieren von Code erzwungen werden soll.

Führen Sie zum Verwenden des Features für die Signaturüberprüfung das Cmdlet `Protect-GuestConfigurationPackage` aus, um das Paket vor der Veröffentlichung zu signieren. Für dieses Cmdlet ist ein Zertifikat für das „Codesignieren“ erforderlich.

Parameter des Cmdlets `Protect-GuestConfigurationPackage`:

- **Pfad**: Vollständiger Pfad des Pakets mit der Gastkonfiguration.
- **PublicGpgKeyPath**: Pfad des öffentlichen GPG-Schlüssels. Dieser Parameter wird nur beim Signieren von Inhalt für Linux unterstützt.

Eine gute Referenz zur Erstellung von GPG-Schlüsseln für die Nutzung mit Linux-Computern ist der Artikel [Generating a new GPG key](https://help.github.com/en/articles/generating-a-new-gpg-key) (Generieren eines neuen GPG-Schlüssels) auf GitHub.

Vom GuestConfiguration-Agent wird erwartet, dass der öffentliche Schlüssel des Zertifikats auf Linux-Computern im Pfad `/usr/local/share/ca-certificates/extra` vorhanden ist. Damit auf dem Knoten signierter Inhalt überprüft werden kann, müssen Sie den öffentlichen Schlüssel des Zertifikats auf dem Computer installieren, bevor Sie die benutzerdefinierte Richtlinie anwenden. Dieser Prozess kann mit einem beliebigen Verfahren auf der VM oder mit Azure Policy durchgeführt werden. Eine Beispielvorlage finden Sie [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
In der Key Vault-Zugriffsrichtlinie muss es für den Computeressourcenanbieter zulässig sein, bei Bereitstellungen auf Zertifikate zuzugreifen. Informationen zu den ausführlichen Schritten finden Sie unter [Einrichten des Schlüsseltresors für virtuelle Computer in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Fügen Sie nach dem Veröffentlichen Ihres Inhalts ein Tag mit dem Namen `GuestConfigPolicyCertificateValidation` und dem Wert `enabled` an alle virtuellen Computer an, für die das Codesignieren erforderlich ist. Informationen darüber, wie Tags mithilfe von Azure Policy im großen Stil bereitgestellt werden können, finden Sie in den [Tagbeispielen](../samples/built-in-policies.md#tags). Wenn dieses Tag vorhanden ist, ermöglicht die Richtliniendefinition, die mit dem Cmdlet `New-GuestConfigurationPolicy` generiert wurde, die Anforderung über die Gastkonfigurationserweiterung.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die Überprüfung von VMs mit [Gastkonfiguration](../concepts/guest-configuration.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](./programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](./get-compliance-data.md).
