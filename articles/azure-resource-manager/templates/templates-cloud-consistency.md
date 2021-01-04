---
title: Wiederverwenden von cloudübergreifenden Vorlagen
description: Entwickeln von Azure Resource Manager-Vorlagen, die in unterschiedlichen Cloudumgebungen konsistent funktionieren. Erstellen Sie neue, oder aktualisieren Sie vorhandene Vorlagen für Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: ea010a625c3e3cd6228513299d878733bf3775ce
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744749"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Entwickeln von ARM-Vorlagen für Cloudkonsistenz

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Ein wichtiger Vorteil von Azure ist Konsistenz. Von Investitionen in die Entwicklung an einem Standort können auch andere Standorte profitieren. Mit einer ARM-Vorlage (Azure Resource Manager) werden Ihre Bereitstellungen für alle Umgebungen konsistent und wiederholbar, einschließlich globaler Azure-Cloud, Azure Sovereign Clouds und Azure Stack. Um Vorlagen cloudübergreifend wiederverwenden zu können, müssen Sie jedoch cloudspezifische Abhängigkeiten berücksichtigen, was in diesem Leitfaden erläutert wird.

Microsoft bietet an vielen Standorten intelligente, betriebsbereite Clouddienste, so z.B.:

* Die globale Azure-Plattform, die von einem wachsenden Netzwerk mit von Microsoft verwalteten Rechenzentren in Regionen auf der ganzen Welt unterstützt wird.
* Isolierte Sovereign Clouds wie Azure Deutschland, Azure Government und Azure China 21Vianet. Sovereign Clouds bieten eine konsistente Plattform, die die meisten der überaus nützlichen Funktionen bietet, auf die Kunden der globalen Azure-Cloud Zugriff haben.
* Azure Stack, eine Hybrid Cloud-Plattform, mit der Sie Azure-Dienste über das Rechenzentrum Ihrer Organisation bereitstellen können. Unternehmen können Azure Stack in ihren eigenen Rechenzentren einrichten oder Azure-Dienste von Dienstanbietern nutzen, die Azure Stack in ihren Rechenzentren betreiben (was mitunter als gehostete Regionen bezeichnet wird).

Im Zentrum all dieser Clouds bietet Azure Resource Manager eine API, die eine Vielzahl von Benutzeroberflächen für die Kommunikation mit der Azure-Plattform ermöglicht. Diese API bietet Ihnen leistungsstarke Infrastruktur-als-Code-Funktionen. Jede Art von Ressource, die auf der Azure-Cloudplattform verfügbar ist, kann mit Azure Resource Manager bereitgestellt und konfiguriert werden. Mithilfe einer einzigen Vorlage können Sie Ihre komplette Anwendung in einen betriebsbereiten Endzustand bringen und konfigurieren.

![Azure-Umgebungen](./media/templates-cloud-consistency/environments.png)

Durch die Konsistenz der globalen Azure-Cloud, Sovereign Clouds, gehosteten Clouds und einer Cloud in Ihrem Rechenzentrum können Sie von den Vorteilen von Azure Resource Manager profitieren. Wenn Sie eine auf Vorlagen basierende Ressourcenbereitstellung und -konfiguration einrichten, können Sie von Ihren Entwicklungsinvestitionen in allen diesen Clouds durch Wiederverwendung profitieren.

Obwohl die globale Cloud sowie Sovereign, gehostete und Hybrid Clouds konsistente Dienste bieten, sind nicht alle Clouds identisch. Daher können Sie eine Vorlage mit Abhängigkeiten von Features erstellen, die nur in einer bestimmten Cloud verfügbar sind.

Im Rest dieses Leitfadens werden die Bereiche erörtert, die bei der Entwicklung neuer oder der Aktualisierung bestehender ARM-Vorlagen für Azure Stack zu berücksichtigen sind. Im Allgemeinen sollte Ihre Prüfliste die folgenden Punkte enthalten:

* Vergewissern Sie sich, dass die Funktionen, Endpunkte, Dienste und andere Ressourcen in Ihrer Vorlage an den Zielstandorten der Bereitstellung verfügbar sind.
* Speichern Sie geschachtelte Vorlagen und Konfigurationsartefakte an zugänglichen Speicherorten, um den Zugriff cloudübergreifend zu gewährleisten.
* Verwenden Sie dynamische Verweise anstelle hartcodierter Links und Elemente.
* Stellen Sie sicher, dass die Vorlagenparameter, die Sie verwenden, in den Zielclouds funktionieren.
* Vergewissern Sie sich, dass ressourcenspezifische Eigenschaften in den Zielclouds verfügbar sind.

Eine Einführung in ARM-Vorlagen finden Sie unter [Vorlagenbereitstellung](overview.md).

## <a name="ensure-template-functions-work"></a>Sicherstellen, dass Vorlagenfunktionen intakt sind

Die grundlegende Syntax einer ARM-Vorlage ist JSON. Vorlagen verwenden eine Obermenge von JSON, wobei die Syntax mit Ausdrücken und Funktionen erweitert wird. Der Prozessor für Vorlagensprachen wird häufig aktualisiert, um zusätzliche Vorlagenfunktionen zu unterstützen. Unter [Vorlagenfunktionen in Azure Resource Manager](template-functions.md) finden Sie eine detaillierte Erläuterung der verfügbaren Vorlagenfunktionen.

Neue Vorlagenfunktionen, die in Azure Resource Manager eingeführt wurden, sind nicht sofort in Sovereign Clouds oder Azure Stack verfügbar. Für eine erfolgreiche Bereitstellung einer Vorlage müssen alle Funktionen, auf die in der Vorlage verwiesen wird, in der Zielcloud verfügbar sein.

Azure Resource Manager-Funktionen werden immer zuerst in der globalen Azure-Cloud eingeführt. Mit dem folgenden PowerShell-Skript können Sie überprüfen, ob neu eingeführte Vorlagenfunktionen auch in Azure Stack verfügbar sind:

1. Erstellen Sie einen Klon des GitHub-Repositorys: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions).

1. Sobald Sie über einen lokalen Klon des Repositorys verfügen, stellen Sie am Ziel mit PowerShell eine Verbindung mit Azure Resource Manager her.

1. Importieren Sie das Modul „psm1“, und führen Sie das Cmdlet „Test-AzureRmTemplateFunctions“ aus:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Das Skript stellt mehrere, minimierte Vorlagen bereit, die jeweils nur eindeutige Vorlagenfunktionen enthalten. Die Ausgabe des Skripts meldet die unterstützten und nicht verfügbaren Vorlagenfunktionen.

## <a name="working-with-linked-artifacts"></a>Arbeiten mit verknüpften Artefakten

Eine Vorlage kann Verweise auf verknüpfte Artefakte und eine Bereitstellungsressource enthalten, die auf eine andere Vorlage verweist. Die verknüpften Vorlagen (auch geschachtelte Vorlagen genannt) werden vom Ressourcen-Manager zur Laufzeit abgerufen. Eine Vorlage kann auch Verweise auf Artefakte für VM-Erweiterungen enthalten. Diese Artefakte werden von der innerhalb der VM ausgeführten VM-Erweiterung zur Konfiguration der VM-Erweiterung während der Vorlagenbereitstellung abgerufen.

In den folgenden Abschnitten werden Aspekte zur cloudübergreifenden Konsistenz bei der Entwicklung von Vorlagen mit Artefakten erörtert, die für die Hauptbereitstellungsvorlage extern sind.

### <a name="use-nested-templates-across-regions"></a>Regionsübergreifendes Verwenden geschachtelter Vorlagen

Vorlagen können in kleine, wiederverwendbare Vorlagen zerlegt werden, die jeweils einen bestimmten Zweck erfüllen und in verschiedenen Bereitstellungsszenarien verwendet werden können. Um eine Bereitstellung durchzuführen, geben Sie eine einzelne Vorlage an, die als Haupt- oder Mastervorlage bezeichnet wird. Sie gibt die bereitzustellenden Ressourcen wie virtuelle Netzwerke, VMs und Web-Apps an. Die Hauptvorlage kann auch einen Link zu einer anderen Vorlage enthalten, d.h. Sie können Vorlagen schachteln. Eine geschachtelte Vorlage kann auch Links zu anderen Vorlagen enthalten. Sie können bis zu fünf Ebenen tief schachteln.

Der folgende Code zeigt, wie der „templateLink“-Parameter auf eine geschachtelte Vorlage verweist:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager wertet die Hauptvorlage zur Laufzeit aus, ruft jede geschachtelte Vorlage ab und wertet sie aus. Nachdem alle geschachtelten Vorlagen abgerufen wurden, wird die Vorlage vereinfacht und die weitere Verarbeitung eingeleitet.

### <a name="make-linked-templates-accessible-across-clouds"></a>Verknüpfte Vorlagen cloudübergreifend zugänglich machen

Überlegen Sie, wo und wie Sie die von Ihnen verwendeten verknüpften Vorlagen speichern. Zur Laufzeit ruft Azure Resource Manager alle verknüpften Vorlagen ab und benötigt daher direkten Zugriff auf diese. Gängige Praxis ist die Verwendung von GitHub zum Speichern der geschachtelten Vorlagen. Ein GitHub-Repository kann Dateien enthalten, die über eine URL öffentlich zugänglich sind. Obwohl diese Vorgehensweise für die öffentliche Cloud und Sovereign Clouds gut funktioniert, kann sich eine Azure Stack-Umgebung in einem Unternehmensnetzwerk oder an einem vom Netzwerk getrennten entfernten Standort ohne ausgehenden Internetzugang befinden. In diesen Fällen kann Azure Resource Manager die geschachtelten Vorlagen nicht abrufen.

Eine bessere Vorgehensweise für cloudübergreifende Bereitstellungen ist es, Ihre verknüpften Vorlagen an einem Speicherort abzulegen, der für die Zielcloud zugänglich ist. Im Idealfall werden alle Bereitstellungsartefakte mithilfe einer CI/CD-Pipeline (Continuous Integration/Continuous Development) verwaltet und bereitgestellt. Alternativ können Sie geschachtelte Vorlagen in einem Blog Storage-Container ablegen, aus dem Azure Resource Manager sie abrufen kann.

Da Blob Storage für jede Cloud einen anderen Endpunkt und vollqualifizierten Domänennamen (FQDN) verwendet, konfigurieren Sie die Vorlage mit dem Speicherort der verknüpften Vorlagen mit zwei Parametern. Parameter können Benutzereingaben zum Zeitpunkt der Bereitstellung akzeptieren. Vorlagen werden in der Regel von mehreren Personen erstellt und gemeinsam genutzt. Daher ist es empfehlenswert, einen Standardnamen für diese Parameter zu verwenden. Benennungskonventionen tragen dazu bei, dass Vorlagen in Regionen, Clouds und von Autoren besser wiederverwendet werden können.

Im folgenden Code wird `_artifactsLocation` verwendet, um auf einen einzelnen Speicherort zu verweisen, der alle mit der Bereitstellung zusammenhängenden Artefakte enthält. Beachten Sie, dass ein Standardwert bereitgestellt wird. Wenn zum Zeitpunkt der Bereitstellung kein Eingabewert für `_artifactsLocation` angegeben ist, wird der Standardwert verwendet. `_artifactsLocationSasToken` dient als Eingabe für `sasToken`. Der Standardwert muss eine leere Zeichenfolge für Szenarien sein, in denen `_artifactsLocation` nicht abgesichert ist, z.B. ein öffentliches GitHub-Repository.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

In der gesamten Vorlage werden Links generiert, indem der Basis-URI (aus dem `_artifactsLocation`-Parameter) mit einem artefaktbezogenen Pfad und `_artifactsLocationSasToken` kombiniert wird. Der folgende Code zeigt, wie der Link zur geschachtelten Vorlage mit der Vorlagenfunktion „uri“ angegeben wird:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Bei diesem Ansatz wird der Standardwert für den Parameter `_artifactsLocation` verwendet. Wenn die verknüpften Vorlagen aus einem anderen Speicherort abgerufen werden müssen, kann die Parametereingabe zum Zeitpunkt der Bereitstellung verwendet werden, um den Standardwert zu überschreiben. Eine Änderung der Vorlage selbst ist nicht erforderlich.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Verwenden von „_artifactsLocation“ anstelle hartcodierter Links

Neben der Verwendung für geschachtelte Vorlagen wird die URL im Parameter `_artifactsLocation` als Basis für alle zugehörigen Artefakte einer Bereitstellungsvorlage verwendet. Einige VM-Erweiterungen enthalten einen Link zu einem Skript, das außerhalb der Vorlage gespeichert wird. Für diese Erweiterungen sollten Sie die Links nicht hartcodieren. Die Erweiterungen „CustomScriptExtension“ und „PowerShell DSC“ können wie gezeigt mit einem externen Skript auf GitHub verknüpft sein:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Die Hardcodierung der Links zum Skript verhindert möglicherweise, dass die Vorlage erfolgreich an einem anderen Ort bereitgestellt wird. Während der Konfiguration der VM-Ressource veranlasst der in der VM ausgeführte VM-Agent das Herunterladen aller in der VM-Erweiterung verknüpften Skripts und speichert diese dann auf der lokalen Festplatte der VM. Dieser Ansatz funktioniert wie bei den Links zu geschachtelten Vorlagen, die bereits im Abschnitt „Regionsübergreifendes Verwenden geschachtelter Vorlagen“ erläutert wurden.

Der Ressourcen-Manager ruft geschachtelte Vorlagen zur Laufzeit ab. Für VM-Erweiterungen wird das Abrufen externer Elemente vom VM-Agent übernommen. Abgesehen vom unterschiedlichen Urheber des Artefaktabrufs ist die Lösung in der Vorlagendefinition identisch. Verwenden Sie den Parameter „_artifactsLocation“ mit einem Standardwert des Basispfades, in dem alle Artefakte gespeichert sind (einschließlich der VM-Erweiterungsskripts) und den Parameter `_artifactsLocationSasToken` als Eingabe für „sasToken“.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Um den absoluten URI eines Artefakts zu konstruieren, ist die bevorzugte Methode die Verwendung der Vorlagenfunktion „uri“ anstelle der Vorlagenfunktion „concat“. Durch Ersetzen hartcodierter Links zu den Skripts in der VM-Erweiterung durch die Vorlagenfunktion „uri“ wird diese Funktionalität in der Vorlage für cloudübergreifende Konsistenz konfiguriert.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Bei diesem Ansatz können alle Bereitstellungsartefakte, einschließlich Konfigurationsskripts, am gleichen Speicherort wie die Vorlage selbst gespeichert werden. Um den Speicherort aller Links zu ändern, müssen Sie nur eine andere Basis-URL für die _artifactsLocation_ -Parameter angeben.

## <a name="factor-in-differing-regional-capabilities"></a>Berücksichtigen verschiedener Funktionen je nach Region

Bei der agilen Entwicklung und dem kontinuierlichen Hinzufügen von Updates und neuen Diensten, die in Azure eingeführt werden, können sich [Regionen unterscheiden](https://azure.microsoft.com/regions/services/), was die Verfügbarkeit von Diensten oder Updates betrifft. Nach strengen internen Tests werden neue Dienste oder Updates bestehender Dienste in der Regel einer kleinen Gruppe von Kunden vorgestellt, die an einem Validierungsprogramm teilnehmen. Nach erfolgreicher Validierung durch Kunden werden die Dienste oder Updates in einer Teilmenge von Azure-Regionen zur Verfügung gestellt, dann in weiteren Regionen und in den Sovereign Clouds eingeführt und möglicherweise auch für Azure Stack-Kunden verfügbar gemacht.

Wohl wissend, dass sich Azure-Regionen und -Clouds in puncto verfügbarer Dienste möglicherweise unterscheiden, können Sie einige proaktive Entscheidungen zu Ihren Vorlagen treffen. Ein guter Ausgangspunkt ist die Untersuchung der verfügbaren Ressourcenanbieter für eine Cloud. Ein Ressourcenanbieter teilt Ihnen mit, welche Ressourcen und Vorgänge für einen Azure-Dienst verfügbar sind.

Eine Vorlage dient zum Bereitstellen und Konfigurieren von Ressourcen. Ein Ressourcentyp wird von einem Ressourcenanbieter bereitgestellt. Beispielsweise bietet der Computeressourcenanbieter (Microsoft.Compute) mehrere Ressourcentypen wie „virtualMachines“ und „availabilitySets“. Jeder Ressourcenanbieter stellt Azure Resource Manager eine API zur Verfügung, die durch einen gemeinsamen Vertrag definiert ist und allen Ressourcenanbietern eine konsistente, vereinheitlichte Erstellungsumgebung ermöglicht. Allerdings ist ein Ressourcenanbieter, der in der globalen Azure-Cloud verfügbar ist, möglicherweise nicht in einer Sovereign Cloud oder Azure Stack-Region verfügbar.

![Ressourcenanbieter](./media/templates-cloud-consistency/resource-providers.png)

Um die Ressourcenanbieter zu überprüfen, die in einer bestimmten Cloud verfügbar sind, führen Sie das folgende Skript an der Azure-Befehlszeilenschnittstelle ([CLI](/cli/azure/install-azure-cli)) aus:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Sie können auch das folgende PowerShell-Cmdlet verwenden, um verfügbare Ressourcenanbieter anzuzeigen:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Überprüfen der Version aller Ressourcentypen

Eine Gruppe von Eigenschaften haben alle Ressourcentypen gemeinsam, aber jede Ressource hat auch ihre eigenen spezifischen Eigenschaften. Neue Funktionen und zugehörige Eigenschaften werden über eine neue API-Version mitunter bestehenden Ressourcentypen hinzugefügt. Eine Ressource in einer Vorlage verfügt über eine eigene API-Versionseigenschaft: `apiVersion`. Diese Versionsverwaltung stellt sicher, dass eine bestehende Ressourcenkonfiguration in einer Vorlage nicht von Änderungen an der Plattform betroffen ist.

Neue API-Versionen, die in bestehende Ressourcentypen in der globalen Azure-Umgebung eingeführt werden, sind möglicherweise nicht sofort in allen Regionen, Sovereign Clouds oder Azure Stack verfügbar. Um eine Liste der verfügbaren Ressourcenanbieter, Ressourcentypen und API-Versionen für eine Cloud anzuzeigen, können Sie den Ressourcen-Explorer im Azure-Portal nutzen. Suchen Sie im Menü „Alle Dienste“ nach „Ressourcen-Explorer“. Erweitern Sie im Ressourcen-Explorer den Knoten „Anbieter“, um alle verfügbaren Ressourcenanbieter, Ressourcentypen und API-Versionen in der jeweiligen Cloud zurückzugeben.

Führen Sie zum Auflisten der verfügbaren API-Version für alle Ressourcentypen in einer bestimmten Cloud in der Azure CLI das folgende Skript aus:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Sie können auch das folgende PowerShell-Cmdlet verwenden:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Verweisen auf Ressourcenspeicherorte mit einem Parameter

Eine Vorlage wird immer in einer Ressourcengruppe bereitgestellt, die sich in einer Region befindet. Neben der Bereitstellung selbst verfügt jede Ressource in einer Vorlage auch über eine „location“-Eigenschaft, die Sie verwenden, um die Region anzugeben, in der die Bereitstellung erfolgt. Um Ihre Vorlage für cloudübergreifende Konsistenz zu entwickeln, benötigen Sie eine dynamische Methode, um auf Speicherorte von Ressourcen zu verweisen, da jede Azure Stack-Instanz eindeutige Speicherortnamen enthalten kann. Normalerweise werden Ressourcen in der gleichen Region wie die Ressourcengruppe bereitgestellt. Doch zur Unterstützung von Szenarien, wie z.B. überregionale Verfügbarkeit von Anwendungen, kann es sinnvoll sein, Ressourcen auf mehrere Regionen zu verteilen.

Auch wenn Sie die Regionsnamen bei der Angabe der Ressourceneigenschaften in einer Vorlage hartcodieren könnten, garantiert dieser Ansatz nicht, dass die Vorlage in anderen Azure Stack-Umgebungen eingesetzt werden kann, da der Regionsname dort höchstwahrscheinlich nicht vorhanden ist.

Um verschiedene Regionen zu berücksichtigen, fügen Sie der Vorlage den Eingabeparameter „location“ mit einem Standardwert hinzu. Der Standardwert wird verwendet, wenn bei der Bereitstellung kein Wert angegeben wird.

Die Vorlagenfunktion `[resourceGroup()]` gibt ein Objekt zurück, das die folgenden Schlüssel-Wert-Paare enthält:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Durch Verweisen auf den Speicherortschlüssel des Objekts im „defaultValue“ des Eingabeparameters ersetzt Azure Resource Manager zur Laufzeit die Vorlagenfunktion `[resourceGroup().location]` durch den Namen des Speicherorts der Ressourcengruppe, in der die Vorlage bereitgestellt wird.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Mithilfe dieser Vorlagenfunktion können Sie Ihre Vorlage in jeder beliebigen Cloud bereitstellen, ohne die Regionsnamen im Voraus kennen zu müssen. Außerdem kann sich ein Speicherort einer bestimmten Ressource in der Vorlage vom Speicherort der Ressourcengruppe unterscheiden. In diesem Fall können Sie ihn mit zusätzlichen Eingabeparametern für die jeweilige Ressource konfigurieren, während die anderen Ressourcen in der gleichen Vorlage weiterhin den ursprünglichen Eingabeparameter „location“ verwenden.

### <a name="track-versions-using-api-profiles"></a>Nachverfolgen von Versionen mithilfe von API-Profilen

Es kann sehr komplex sein, den Überblick über alle verfügbaren Ressourcenanbieter und die zugehörigen API-Versionen zu behalten, die in Azure Stack vorhanden sind. Zum Zeitpunkt des Verfassens ist beispielsweise die neueste API-Version für **Microsoft.Compute/availabilitySets** in Azure `2018-04-01`, während die verfügbare API-Version für Azure und Azure Stack `2016-03-30` ist. Die Standard-API-Version für **Microsoft.storage/storageAccounts** , die von allen Azure- und Azure Stack-Standorten gemeinsam genutzt wird, ist `2016-01-01`, während die neueste API-Version in Azure `2018-02-01` ist.

Aus diesem Grund wurde im Ressourcen-Manager das Konzept der API-Profile in Vorlagen eingeführt. Ohne API-Profile wird jede Ressource in einer Vorlage mit einem `apiVersion`-Element konfiguriert, das die API-Version für diese spezifische Ressource beschreibt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Eine API-Profilversion fungiert als Alias für eine einzelne API-Version pro Ressourcentyp, die Azure und Azure Stack gemeinsam haben. Anstatt für jede Ressource in einer Vorlage eine API-Version anzugeben, geben Sie nur die API-Profilversion in einem neuen Stammelement namens `apiProfile` an und lassen das Element `apiVersion` für die einzelnen Ressourcen weg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Das API-Profil stellt sicher, dass die API-Versionen standortübergreifend verfügbar sind, damit Sie die „apiVersions“, die an einem bestimmten Ort verfügbar sind, nicht manuell prüfen müssen. Um sicherzustellen, dass die von Ihrem API-Profil referenzierten API-Versionen in einer Azure Stack-Umgebung vorhanden sind, müssen die Zuständigen für Azure Stack die Lösung auf Grundlage der Supportrichtlinie auf dem neuesten Stand halten. Wenn ein System mehr als sechs Monate nicht auf dem neuesten Stand ist, gilt es als nicht mehr konform, weshalb die Umgebung aktualisiert werden muss.

Das API-Profil ist kein Pflichtelement einer Vorlage. Selbst wenn Sie das Element hinzufügen, wird es nur für Ressourcen verwendet, für die `apiVersion` nicht angegeben ist. Dieses Element ermöglicht graduelle Änderungen, erfordert aber keine Änderungen an bestehenden Vorlagen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Überprüfen von Endpunktverweisen

Ressourcen können Verweise auf andere Dienste auf der Plattform aufweisen. Beispielsweise kann einer öffentlichen IP-Adresse ein öffentlicher DNS-Name zugewiesen sein. Die öffentliche Cloud, Sovereign Clouds und Azure Stack-Lösungen verfügen über eigene unterschiedliche Namespaces für Endpunkte. In den meisten Fällen erfordert eine Ressource als Eingabe in der Vorlage nur ein Präfix. Azure Resource Manager fügt zur Laufzeit den Endpunktwert daran an. Einige Endpunktwerte müssen explizit in der Vorlage angegeben werden.

> [!NOTE]
> Um Vorlagen für cloudübergreifende Konsistenz zu entwickeln, sollten Sie Namespaces für Endpunkte nicht hartcodieren.

Die beiden folgenden Beispiele sind gängige Namespaces für Endpunkte, die beim Anlegen einer Ressource explizit angegeben werden müssen:

* Speicherkonten (Blob, Warteschlange, Tabelle und Datei)
* Verbindungszeichenfolgen für Datenbanken und Azure Cache for Redis

Namespaces für Endpunkte können auch in der Ausgabe einer Vorlage als Information für den Benutzer verwendet werden, sobald die Bereitstellung abgeschlossen ist. Im Folgenden sind allgemeine Beispiele aufgeführt:

* Speicherkonten (Blob, Warteschlange, Tabelle und Datei)
* Verbindungszeichenfolgen (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* „domainnamelabel“ einer öffentlichen IP-Adresse
* Clouddienste

Vermeiden Sie grundsätzlich hartcodierte Endpunkte in einer Vorlage. Es empfiehlt sich, die Referenzvorlagenfunktion zu verwenden, um die Endpunkte dynamisch abzurufen. Der Endpunkt, der am häufigsten hartcodiert wird, ist beispielsweise der Namespace von Endpunkten für Speicherkonten. Jedes Speicherkonto hat einen eindeutigen FQDN, der durch Verkettung des Namens des Speicherkontos mit dem Namespace des Endpunkts gebildet wird. Ein Blob Storage-Konto namens „mystorageaccount1“ führt je nach Cloud zu unterschiedlichen FQDNs:

* **mystorageaccount1.blob.core.windows.net** bei Erstellung in der globalen Azure-Cloud.
* **mystorageaccount1.blob.core.chinacloudapi.cn** bei Erstellung in der Cloud Azure China 21Vianet.

Die folgende Referenzvorlagenfunktion ruft den Namespace des Endpunkts vom Speicherressourcenanbieter ab:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Indem Sie den hartcodierten Wert des Endpunkts des Speicherkontos durch die Vorlagenfunktion `reference` ersetzen, können Sie dieselbe Vorlage für eine erfolgreiche Bereitstellung in verschiedenen Umgebungen verwenden, ohne Änderungen am Verweis auf den Endpunkt vorzunehmen.

### <a name="refer-to-existing-resources-by-unique-id"></a>Verweisen auf vorhandene Ressourcen über die eindeutige ID

Sie können auch auf eine vorhandene Ressource aus derselben oder einer anderen Ressourcengruppe und innerhalb desselben Abonnements oder eines anderen Abonnements innerhalb desselben Mandanten in derselben Cloud verweisen. Um die Ressourceneigenschaften abzurufen, müssen Sie die eindeutige ID für die Ressource selbst verwenden. Die Vorlagenfunktion `resourceId` ruft die eindeutige ID einer Ressource wie SQL Server wie im folgenden Code gezeigt ab:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Sie können dann die Funktion `resourceId` innerhalb der Vorlagenfunktion `reference` verwenden, um die Eigenschaften einer Datenbank abzurufen. Das Rückgabeobjekt enthält die Eigenschaft `fullyQualifiedDomainName`, die den vollständigen Endpunktwert enthält. Dieser Wert wird zur Laufzeit abgerufen und stellt den für die Cloudumgebung spezifischen Namespace für Endpunkte bereit. Um die Verbindungszeichenfolge ohne Hardcodierung des Namespaces für Endpunkte zu definieren, können Sie wie gezeigt in der Verbindungszeichenfolge direkt auf die Eigenschaft des Rückgabeobjekts verweisen:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Berücksichtigen von Ressourceneigenschaften

Bestimmte Ressourcen in Azure Stack-Umgebungen haben eindeutige Eigenschaften, die Sie in Ihrer Vorlage berücksichtigen müssen.

### <a name="ensure-vm-images-are-available"></a>Sicherstellen der Verfügbarkeit von VM-Images

Azure bietet eine umfassende Auswahl von VM-Images. Diese Images werden für die Bereitstellung durch Microsoft und seine Partner erstellt und vorbereitet. Die Images bilden die Grundlage für VMs auf der Plattform. Eine cloudübergreifend konsistente Vorlage sollte jedoch nur auf verfügbare Parameter verweisen, insbesondere auf die Parameter „publisher“, „offer“ und „SKU“ der VM-Images, die für die globale Azure-Cloud, Azure Sovereign Clouds oder eine Azure Stack-Lösung verfügbar sind.

Um eine Liste der an einem Standort verfügbaren VM-Images abzurufen, führen Sie den folgenden Azure CLI-Befehl aus:

```azurecli-interactive
az vm image list -all
```

Sie können die gleiche Liste mit dem Azure PowerShell-Cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) abrufen und den gewünschten Standort mit dem Parameter `-Location` angeben. Beispiel:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Dieser Befehl benötigt einige Minuten, um alle verfügbaren Images in der Region „Europa, Westen“ der globalen Azure-Cloud zurückzugeben.

Wenn Sie diese VM-Images für Azure Stack zur Verfügung stellen, wird der gesamte verfügbare Speicher belegt. Um selbst die kleinste Skalierungseinheit zu unterstützen, ermöglicht Azure Stack Ihnen die Auswahl der Images, die Sie einer Umgebung hinzufügen möchten.

Das folgende Codebeispiel zeigt einen konsistenten Ansatz, um auf die Parameter „publisher“, „offer“ und „SKU“ in Ihren ARM-Vorlagen zu verweisen:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Überprüfen lokaler VM-Größen

Um Ihre Vorlage für cloudübergreifende Konsistenz zu entwickeln, müssen Sie sicherstellen, dass die gewünschte VM-Größe in allen Zielumgebungen verfügbar ist. VM-Größen sind eine Gruppierung von Leistungsmerkmalen und Funktionen. Einige VM-Größen hängen von der Hardware ab, auf der die VM ausgeführt wird. Wenn Sie beispielsweise eine GPU-optimierte VM bereitstellen möchten, muss die Hardware, auf der der Hypervisor läuft, über die Hardware-GPUs verfügen.

Wenn Microsoft eine neue VM-Größe einführt, die bestimmte Hardwareabhängigkeiten aufweist, wird die VM-Größe normalerweise zuerst in einer kleinen Teilmenge von Regionen in der Azure-Cloud zur Verfügung gestellt. Später wird sie anderen Regionen und Clouds zur Verfügung gestellt. Um sicherzustellen, dass die VM-Größe in jeder Cloud vorhanden ist, in der die Bereitstellung erfolgen soll, können Sie die verfügbaren Größen mit dem folgenden Azure CLI-Befehl abrufen:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Verwenden Sie für Azure PowerShell Folgendes:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Eine vollständige Liste verfügbarer Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Überprüfen der Nutzung von Azure Managed Disks in Azure Stack

Die Speicherung in einem Azure-Mandanten erfolgt auf verwalteten Datenträgern. Anstatt explizit ein Speicherkonto anzulegen und den URI für eine virtuelle Festplatte (VHD) anzugeben, können Sie verwaltete Datenträger verwenden, um diese Aktionen implizit auszuführen, wenn Sie eine VM bereitstellen. Verwaltete Datenträger erhöhen die Verfügbarkeit, indem sie alle Datenträger von VMs in derselben Verfügbarkeitsgruppe in verschiedenen Speichereinheiten unterbringen. Darüber hinaus können bestehende VHDs mit deutlich weniger Ausfallzeiten von Standard auf Storage Premium umgestellt werden.

Obwohl verwaltete Datenträger auf dem Fahrplan für Azure Stack stehen, werden sie derzeit nicht unterstützt. Bis es soweit ist, können Sie cloudübergreifend konsistente Vorlagen für Azure Stack entwickeln, indem Sie VHDs wie gezeigt explizit mit dem Element `vhd` in der Vorlage für die VM-Ressource angeben:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Um dagegen eine Konfiguration verwalteter Datenträger in einer Vorlage anzugeben, entfernen Sie das Element `vhd` aus der Datenträgerkonfiguration.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Die gleichen Änderungen gelten auch für [Datenträger](../../virtual-machines/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Sicherstellen der Verfügbarkeit von VM-Erweiterungen in Azure Stack

Ein weiterer Aspekt bei der cloudübergreifenden Konsistenz ist die Verwendung von [VM-Erweiterungen](../../virtual-machines/extensions/features-windows.md) zur Konfiguration der Ressourcen innerhalb einer VM. In Azure Stack sind nicht alle VM-Erweiterungen verfügbar. In einer Vorlage können die Ressourcen für die VM-Erweiterung angegeben werden, wodurch Abhängigkeiten und Bedingungen innerhalb der Vorlage erzeugt werden.

Wenn Sie beispielsweise eine VM mit Microsoft SQL Server konfigurieren möchten, kann die VM-Erweiterung SQL Server im Rahmen der Vorlagenbereitstellung konfigurieren. Berücksichtigen Sie, was passiert, wenn die Bereitstellungsvorlage auch einen Anwendungsserver enthält, der so konfiguriert ist, dass er eine Datenbank in der VM erstellt, in der SQL Server ausgeführt wird. Neben der Verwendung einer VM-Erweiterung auch für die Anwendungsserver können Sie die Abhängigkeit des Anwendungsservers von der erfolgreichen Rückgabe der VM-Erweiterungsressource „SQL Server“ konfigurieren. Dieser Ansatz stellt sicher, dass die VM, in der SQL Server ausgeführt wird, konfiguriert und verfügbar ist, wenn der Anwendungsserver angewiesen wird, die Datenbank zu erstellen.

Der deklarative Ansatz der Vorlage erlaubt es, den Endzustand der Ressourcen und deren gegenseitige Abhängigkeiten zu definieren, während die Plattform sich um die für die Abhängigkeiten erforderliche Logik kümmert.

#### <a name="check-that-vm-extensions-are-available"></a>Überprüfen der Verfügbarkeit von VM-Erweiterungen

Es gibt viele Typen von VM-Erweiterungen. Wenn Sie eine Vorlage für cloudübergreifende Konsistenz entwickeln, stellen Sie sicher, dass Sie nur die Erweiterungen verwenden, die in allen Regionen verfügbar sind, auf die sich die Vorlage bezieht.

Um eine Liste der VM-Erweiterungen abzurufen, die für eine bestimmte Region verfügbar sind (in diesem Beispiel `myLocation`), führen Sie den folgenden Azure CLI-Befehl aus:

```azurecli-interactive
az vm extension image list --location myLocation
```

Sie können auch das Azure PowerShell-Cmdlet [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) ausführen und mit `-Location` den Speicherort des VM-Images angeben. Beispiel:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Sicherstellen der Verfügbarkeit von Versionen

Da VM-Erweiterungen Erstanbieterressourcen von Ressourcen-Manager sind, haben sie eigene API-Versionen. Wie der folgende Code zeigt, ist der VM-Erweiterungstyp eine geschachtelte Ressource im Ressourcenanbieter „Microsoft.Compute“.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Die API-Version der VM-Erweiterungsressource muss an allen Standorten vorhanden sein, die Ihre Vorlage als Ziel haben soll. Die Standortabhängigkeit funktioniert wie die Verfügbarkeit der API-Version des Ressourcenanbieters, die bereits im Abschnitt „Überprüfen der Version aller Ressourcentypen“ beschrieben wurde.

Um eine Liste der verfügbaren API-Versionen für die VM-Erweiterungsressource abzurufen, verwenden Sie wie gezeigt das Cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) mit dem Ressourcenanbieter **Microsoft.Compute** :

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

VM-Erweiterungen können auch in VM-Skalierungsgruppen verwendet werden. Es gelten dieselben Standortbedingungen. Um Ihre Vorlage für die cloudübergreifende Konsistenz zu entwickeln, stellen Sie sicher, dass die API-Versionen an allen Standorten verfügbar sind, an denen die Bereitstellung erfolgen soll. Um die API-Versionen der VM-Erweiterungsressource für Skalierungsgruppen abzurufen, verwenden Sie dasselbe Cmdlet wie zuvor, geben jedoch wie gezeigt den Ressourcentyp „VM-Skalierungsgruppen“ an:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Jede spezifische Erweiterung erhält eine eigene Version. Diese Version wird in der Eigenschaft `typeHandlerVersion` der VM-Erweiterung angezeigt. Stellen Sie sicher, dass die im Element `typeHandlerVersion` der VM-Erweiterungen Ihrer Vorlage angegebene Version an den Standorten verfügbar ist, an denen Sie die Vorlage bereitstellen möchten. Der folgende Code gibt beispielsweise Version 1.7 an:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Um eine Liste der verfügbaren Versionen für eine bestimmte VM-Erweiterung abzurufen, verwenden Sie das Cmdlet [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). Das folgende Beispiel ruft die verfügbaren Versionen für die VM-Erweiterung „PowerShell DSC“ (Desired State Configuration) von **myLocation** ab:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Verwenden Sie den Befehl [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), um eine Liste der Herausgeber anzuzeigen. Verwenden Sie zum Anfordern des Typs den Befehl [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype).

## <a name="tips-for-testing-and-automation"></a>Tipps für Tests und Automatisierung

Beim Erstellen einer Vorlage ist es eine Herausforderung, alle damit verbundenen Einstellungen, Fähigkeiten und Einschränkungen im Auge zu behalten. Die übliche Vorgehensweise ist, Vorlagen für eine einzelne Cloud zu entwickeln und zu testen, bevor andere Standorte ins Auge gefasst werden. Je früher diese Tests jedoch im Erstellungsprozess erfolgen, desto weniger Problembehebung und Neuschreiben von Code fallen für Ihr Entwicklungsteam an. Bei Bereitstellungen, die aufgrund von Standortabhängigkeiten misslingen, kann die Fehlerbehebung zeitaufwändig sein. Deshalb empfehlen wir, automatisierte Tests so früh wie möglich im Erstellungszyklus durchzuführen. Letztlich benötigen Sie weniger Entwicklungszeit und Ressourcen, und Ihre cloudübergreifend konsistenten Artefakte werden noch nützlicher.

Die folgende Abbildung zeigt ein typisches Beispiel eines Entwicklungsprozesses eines Teams mit einer integrierten Entwicklungsumgebung (IDE). In verschiedenen Phasen auf der Zeitachse erfolgen verschiedene Arten von Tests. Hier arbeiten zwei Entwickler an der gleichen Lösung, aber dieses Szenario gilt gleichermaßen für einen einzelnen Entwickler oder ein großes Team. Jeder Entwickler erstellt in der Regel eine lokale Kopie eines zentralen Repositorys, sodass jeder an der lokalen Kopie arbeiten kann, ohne die anderen zu beeinträchtigen, die möglicherweise an denselben Dateien arbeiten.

![Eine Abbildung zeigt jeweils zwei Komponententests und Integrationstests, die parallel in einer lokalen integrierten Entwicklungsumgebung ausgeführt werden und im CI/CD-Entwicklungsflow zuerst in Komponententests, dann in Integrationstests und dann in die Testbereitstellung mit anschließender Bereitstellung zusammenlaufen.](./media/templates-cloud-consistency/workflow.png)

Befolgen Sie die folgenden Tipps für Tests und Automatisierung:

* Verwenden Sie Testtools. Beispielsweise bieten Visual Studio Code und Visual Studio IntelliSense und andere Funktionen, die Ihnen bei der Validierung Ihrer Vorlagen helfen können.
* Um die Codequalität während der Entwicklung in der lokalen IDE zu verbessern, führen Sie statische Codeanalysen mit Komponenten- und Integrationstests durch.
* Für eine noch bessere Erfahrung während der ersten Entwicklungsphase sollten Komponenten- und Integrationstests nur eine Warnung ausgeben, wenn ein Problem gefunden wird, und mit den Tests fortfahren. Auf diese Weise können Sie die zu behandelnden Probleme identifizieren und die Reihenfolge der Änderungen priorisieren, was auch als testgesteuerte Bereitstellung (Test-Driven Deployment, TDD) bezeichnet wird.
* Denken Sie daran, dass einige Tests auch ohne Verbindung mit Azure Resource Manager erfolgen können. Andere, wie z.B. das Testen von Vorlagen, benötigen den Ressourcen-Manager, um bestimmte Aktionen auszuführen, die offline nicht möglich sind.
* Das Testen einer Bereitstellungsvorlage für die Validierungs-API ist nicht gleichbedeutend mit einer tatsächlichen Bereitstellung. Auch wenn Sie eine Vorlage aus einer lokalen Datei bereitstellen, werden alle Verweise auf geschachtelte Vorlagen in der Vorlage direkt vom Ressourcen-Manager abgerufen. Artefakte, auf die von VM-Erweiterungen verwiesen wird, werden vom VM-Agent abgerufen, der in der bereitgestellten VM ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

* [Aspekte zu Azure Resource Manager-Vorlagen](/azure-stack/user/azure-stack-develop-templates)
* [Bewährte Methoden für ARM-Vorlagen](template-syntax.md)
