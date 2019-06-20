---
title: Bewährte Methoden für Azure Resource Manager-Vorlagen
description: Beschreibt die empfohlenen Vorgehensweisen zum Erstellen von Azure Resource Manager-Vorlagen. Bietet Vorschläge zur Vermeidung häufig auftretender Probleme bei der Verwendung von Vorlagen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: tomfitz
ms.openlocfilehash: bcc529b02505359e6e4e320d4991a082797c5261
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60389575"
---
# <a name="azure-resource-manager-template-best-practices"></a>Bewährte Methoden für Azure Resource Manager-Vorlagen

Dieser Artikel bietet Empfehlungen für die Erstellung Ihrer Resource Manager-Vorlage. Anhand dieser Empfehlungen können Sie häufig auftretende Probleme vermeiden, wenn Sie eine Vorlage zum Bereitstellen einer Lösung verwenden.

Empfehlungen zur Verwaltung Ihrer Azure-Abonnements finden Sie unter [Azure-Unternehmensgerüst – präskriptive Abonnementgovernance](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Empfehlungen zur Erstellung von Vorlagen, die in allen Azure-Cloudumgebungen funktionieren, finden Sie unter [Informationen zum Entwickeln von Azure Resource Manager-Vorlagen für cloudübergreifende Konsistenz](templates-cloud-consistency.md).

## <a name="template-limits"></a>Vorlagengrenzwerte

Begrenzen Sie die Größe der Vorlage auf 1 MB und die jeder Parameterdatei auf 64 KB. Die 1-MB-Beschränkung gilt für den endgültigen Status der Vorlage, nachdem sie durch iterative Ressourcendefinitionen und Werte für variables und Parameter erweitert wurde. 

Außerdem gelten folgenden Beschränkungen:

* 256 Parameter
* 256 Variablen
* 800 Ressourcen (einschließlich copy-Anzahl)
* 64 Ausgabewerte
* 24\.576 Zeichen in einem Vorlagenausdruck

Sie können einige Vorlagengrenzwerte überschreiten, indem Sie eine geschachtelte Vorlage verwenden. Weitere Informationen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](resource-group-linked-templates.md). Um die Anzahl von Parametern, Variablen oder Ausgaben zu reduzieren, können Sie mehrere Werte in einem Objekt kombinieren. Weitere Informationen finden Sie unter [Objekte als Parameter](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Ressourcengruppe

Wenn Sie Ressourcen für eine Ressourcengruppe bereitstellen, speichert die Ressourcengruppe Metadaten zu den Ressourcen. Die Metadaten werden am Standort der Ressourcengruppe gespeichert.

Ist die Region der Ressourcengruppe vorübergehend nicht verfügbar, können Sie keine Ressourcen in der Ressourcengruppe aktualisieren, da die Metadaten nicht verfügbar sind. Die Ressourcen in anderen Regionen funktionieren weiterhin wie erwartet, doch können Sie diese nicht aktualisieren. Um das Risiko zu minimieren, platzieren Sie Ihre Ressourcengruppe und Ressourcen in der gleichen Region.

## <a name="parameters"></a>Parameter
Die Informationen in diesem Abschnitt können bei der Verwendung von [Parametern](resource-group-authoring-templates.md#parameters) hilfreich sein.

### <a name="general-recommendations-for-parameters"></a>Allgemeine Empfehlungen für Parameter

* Verwenden Sie möglichst wenig Parameter. Verwenden Sie stattdessen Variablen oder Literalwerte für Eigenschaften, die während der Bereitstellung nicht angegeben werden müssen.

* Verwenden Sie die camel-Schreibweise für Parameternamen.

* Verwenden Sie Parameter für Einstellungen, die je nach Umgebung variieren, z.B. SKU, Größe oder Kapazität.

* Verwenden Sie Parameter für Ressourcennamen, die Sie zur einfachen Identifizierung angeben möchten.

* Geben Sie für jeden Parameter in den Metadaten eine Beschreibung an.

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definieren Sie Standardwerte für Parameter, die nicht vertraulich sind. Durch die Angabe eines Standardwerts kann die Vorlage einfacher bereitgestellt werden, und Benutzern Ihrer Vorlage wird ein Beispiel für einen passenden Wert angezeigt. Alle Standardwerte für einen Parameter müssen für alle Benutzer in der Standardbereitstellungskonfiguration gültig sein. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Verwenden Sie keine leere Zeichenfolge als Standardwert, um einen optionalen Parameter anzugeben. Verwenden Sie stattdessen einen Literalwert oder einen Sprachausdruck, um einen Wert zu erstellen.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Verwenden Sie keinen Parameter für die API-Version eines Ressourcentyps. Ressourceneigenschaften und -werte können je nach Versionsnummer variieren. Mithilfe von IntelliSense kann in Code-Editoren nicht das richtige Schema ermittelt werden, wenn die API-Version auf einen Parameter festgelegt ist. Stattdessen sollten Sie die API-Version in der Vorlage hartcodieren.

* Setzen Sie `allowedValues` sparsam ein. Verwenden sie dies nur, wenn Sie sicherstellen müssen, dass einige Werte nicht in die zulässigen Optionen eingeschlossen werden. Wenn Sie `allowedValues` zu großzügig verwenden, werden möglicherweise gültige Bereitstellungen blockiert, wenn Ihre Liste nicht auf dem neuesten Stand gehalten wird.

* Wenn ein Parametername in Ihrer Vorlage einem Parameter im PowerShell-Bereitstellungsbefehl entspricht, löst Resource Manager diesen Namenskonflikt auf, indem dem Vorlagenparameter das Postfix **FromTemplate** hinzugefügt wird. Beispiel: Falls Sie einen Parameter namens **ResourceGroupName** in Ihrer Vorlage einfügen, wird ein Konflikt mit dem Parameter **ResourceGroupName** im Cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) verursacht. Während der Bereitstellung werden Sie zur Eingabe eines Werts für **ResourceGroupNameFromTemplate** aufgefordert.

### <a name="security-recommendations-for-parameters"></a>Sicherheitsempfehlungen für Parameter

* Verwenden Sie immer Parameter für Benutzernamen und Kennwörter (oder Geheimnisse).

* Verwenden Sie `securestring` für alle Kennwörter und Geheimnisse. Wenn Sie vertrauliche Daten in einem JSON-Objekt übergeben, verwenden Sie den Typ `secureObject`. Vorlagenparameter des Typs „securestring“ oder „secureObject“ können nach der Bereitstellung der Ressource nicht mehr gelesen werden. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Geben Sie keine Standardwerte für Benutzernamen, Kennwörter oder einen anderen Wert an, der einen `secureString`-Typ erfordert.

* Geben Sie keine Standardwerte für Eigenschaften an, die die Angriffsfläche der Anwendung erhöhen.

### <a name="location-recommendations-for-parameters"></a>Empfehlungen zu Standortparametern

* Verwenden Sie einen Parameter zur Angabe des Standorts für Ressourcen, und legen den Standardwert auf `resourceGroup().location` fest. Indem Sie einen location-Parameter angeben, können Benutzer der Vorlage einen Standort angeben, für den Sie die Bereitstellungsberechtigung besitzen.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Geben Sie nicht `allowedValues` für den location-Parameter an. Die von Ihnen angegebenen Standorte sind möglicherweise nicht in allen Clouds verfügbar.

* Verwenden Sie den location-Parameterwert für Ressourcen, die sich wahrscheinlich am selben Standort befinden. Durch diese Vorgehensweise werden Benutzer weniger häufig zur Angabe von Speicherortinformationen aufgefordert.

* Verwenden Sie für Ressourcen, die nicht an allen Standorten verfügbar sind, einen separaten Parameter, oder geben Sie einen Literalwert für den Standort ein.

## <a name="variables"></a>Variables

Die folgenden Informationen können bei der Arbeit mit [Variablen](resource-group-authoring-templates.md#variables) hilfreich sein:

* Verwenden Sie Variablen für Werte, die Sie mehr als einmal in einer Vorlage verwenden müssen. Wenn ein Wert nur einmal verwendet wird, erleichtert ein hartcodierter Wert das Lesen Ihrer Vorlage.

* Verwenden Sie Variablen für Werte, die Sie aus einer komplexen Anordnung von Vorlagenfunktionen erstellen. Die Vorlage ist einfacher zu lesen, wenn der komplexe Ausdruck nur in Variablen angezeigt wird.

* Verwenden Sie keine Variablen für `apiVersion` für eine Ressource. Die API-Version legt das Schema der Ressource fest. Häufig kann die Version nicht geändert werden, ohne die Eigenschaften für die Ressource zu ändern.

* Im Vorlagenabschnitt **variables** können Sie die [reference](resource-group-template-functions-resource.md#reference)-Funktion nicht nutzen. Die **reference**-Funktion leitet ihren Wert aus dem Laufzeitstatus der Ressource ab. Variablen werden jedoch während der ersten Analyse der Vorlage aufgelöst. Erstellen Sie Werte, die die **reference**-Funktion direkt in den Abschnitten **resources** oder **outputs** der Vorlage benötigen.

* Beziehen Sie Variablen für Ressourcennamen ein, die eindeutig sein müssen.

* Verwenden Sie eine [copy-Schleife in Variablen](resource-group-create-multiple.md#variable-iteration), um ein wiederholtes Muster von JSON-Objekten zu erstellen.

* Entfernen Sie nicht verwendete Variablen.

## <a name="resource-dependencies"></a>Ressourcenabhängigkeiten

Verwenden Sie die folgenden Richtlinien für die Entscheidung, welche [Abhängigkeiten](resource-group-define-dependencies.md) festgelegt werden sollen:

* Verwenden Sie die **reference**-Funktion, und übergeben Sie den Ressourcennamen, um eine implizite Abhängigkeit zwischen Ressourcen festzulegen, die eine Eigenschaft gemeinsam nutzen müssen. Fügen Sie kein explizites `dependsOn`-Element hinzu, wenn Sie bereits eine implizite Abhängigkeit definiert haben. Dieser Ansatz reduziert das Risiko unnötiger Abhängigkeiten.

* Legen Sie für eine untergeordnete Ressource eine Abhängigkeit von der übergeordneten Ressource fest.

* Ressourcen, deren [condition-Element](resource-group-authoring-templates.md#condition) auf FALSE festgelegt ist, werden automatisch aus der Reihenfolge der Abhängigkeiten entfernt. Legen Sie die Abhängigkeiten so fest, als würde die Ressource immer bereitgestellt.

* Lassen Sie die Abhängigkeiten ohne explizites Festlegen überlappen. Beispiel: Ihr virtueller Computer hängt von einer virtuellen Netzwerkschnittstelle ab, die wiederum von einem virtuellen Netzwerk und einer öffentlichen IP-Adressen abhängt. Deshalb wird der virtuelle Computer nach allen drei Ressourcen bereitgestellt, es wird aber nicht explizit festgelegt, dass er von allen drei Ressourcen abhängig ist. Dieser Ansatz verdeutlicht die Reihenfolge der Abhängigkeiten und vereinfacht spätere Änderungen an der Vorlage.

* Falls vor der Bereitstellung ein Wert bestimmt werden kann, versuchen Sie, die Ressource ohne Abhängigkeit bereitzustellen. Beispiel: Wenn ein Konfigurationswert den Namen einer anderen Ressource benötigt, ist möglicherweise keine Abhängigkeit erforderlich. Dieses Vorgehen funktioniert nicht immer, da einige Ressourcen überprüfen, ob die andere Ressource vorhanden ist. Wenn Sie einen Fehler erhalten, fügen Sie eine Abhängigkeit hinzu.

## <a name="resources"></a>Ressourcen

Die folgenden Informationen können bei der Arbeit mit [Ressourcen](resource-group-authoring-templates.md#resources) hilfreich sein:

* Geben Sie in der Vorlage **Kommentare** für jede Ressource ein, damit andere Mitwirkende den Zweck der Ressource verstehen.
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Wenn Sie einen *öffentlichen Endpunkt* in Ihrer Vorlage verwenden (z.B. einen öffentlichen Azure Blob Storage-Endpunkt), dürfen Sie den Namespace *nicht hartcodieren*. Verwenden Sie die **reference**-Funktion, um den Namespace dynamisch abzurufen. Mit diesem Ansatz können Sie die Vorlage in anderen öffentlichen Namespace-Umgebungen bereitstellen, ohne den Endpunkt in der Vorlage manuell zu ändern. Legen Sie die API-Version auf die Version fest, die Sie für das Speicherkonto in der Vorlage verwenden:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Wenn das Speicherkonto in der Vorlage bereitgestellt wird, die Sie gerade erstellen, müssen Sie beim Verweisen auf die Ressource nicht den Namespace des Anbieters angeben. Das folgende Beispiel zeigt die vereinfachte Syntax:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Wenn die Vorlage andere Werte enthält, die für die Verwendung eines öffentlichen Namespace konfiguriert sind, ändern Sie diese Werte entsprechend der **reference**-Funktion. Beispiel: Sie können die **storageUri**-Eigenschaft des Diagnoseprofils des virtuellen Computers festlegen:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Sie können auch auf ein in einer anderen Ressourcengruppe vorhandenes Speicherkonto verweisen:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Weisen Sie einem virtuellen Computer nur dann öffentliche IP-Adressen zu, wenn dies für eine Anwendung erforderlich ist. Verwenden Sie zum Herstellen einer Verbindung mit einem virtuellen Computer für das Debuggen, die Verwaltung oder administrative Zwecke entweder NAT-Eingangsregeln, ein Gateway für virtuelle Netzwerke oder eine Jumpbox.
   
     Weitere Informationen zum Herstellen einer Verbindung mit virtuellen Computern finden Sie unter:
   
   * [Run Windows VMs for an N-tier application](../guidance/guidance-compute-n-tier-vm.md) (Ausführen virtueller Windows-Computer in einer Architektur mit n Ebenen in Azure)
   * [Einrichten des Zugriffs auf WinRM für virtuelle Computer in Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Öffnen von Ports für einen virtuellen Computer in Azure mithilfe des Azure-Portals](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Öffnen von Ports und Endpunkten für einen virtuellen Computer in Azure mithilfe von PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Öffnen von Ports und Endpunkten für einen virtuellen Linux-Computer mithilfe der Azure CLI](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Die Eigenschaft **domainNameLabel** für öffentliche IP-Adressen muss eindeutig sein. Der Wert **domainNameLabel** muss 3 bis 63 Zeichen lang sein und den Regeln des regulären Ausdrucks `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` entsprechen. Da die **uniqueString**-Funktion eine Zeichenfolge mit 13 Zeichen erstellt, ist der **dnsPrefixString**-Parameter auf 50 Zeichen beschränkt:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Verwenden Sie beim Hinzufügen eines Kennworts zu einer benutzerdefinierten Skripterweiterung die **commandToExecute**-Eigenschaft in **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Zum Sicherzustellen der Verschlüsselung von Geheimnissen, die als Parameter an virtuelle Computer und Erweiterungen übergeben werden, verwenden Sie die **protectedSettings**-Eigenschaft der entsprechenden Erweiterungen.
   > 
   > 

## <a name="outputs"></a>Ausgaben

Wenn Sie öffentliche IP-Adressen mithilfe einer Vorlage erstellen, sollte diese einen [Ausgabeabschnitt](resource-group-authoring-templates.md#outputs) enthalten, der Details zur IP-Adresse und den vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) zurückgibt. Mit Ausgabewerten können Sie nach der Bereitstellung ganz einfach Details zu öffentlichen IP-Adressen und FQDNs abrufen.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Struktur der Resource Manager-Vorlagendatei finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
* Empfehlungen zur Erstellung von Vorlagen, die in allen Azure-Cloudumgebungen funktionieren, finden Sie unter [Informationen zum Entwickeln von Azure Resource Manager-Vorlagen für cloudübergreifende Konsistenz](templates-cloud-consistency.md).
