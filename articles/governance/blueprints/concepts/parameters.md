---
title: Verwenden von Parametern für das Erstellen dynamischer Blaupausen
description: Hier erhalten Sie Informationen zu statischen und dynamischen Parametern und dazu, wie deren Verwendung zur Erstellung dynamischer Blaupausen führt.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5c1bb1f959f920ea9bce23082ec531dc83d873ad
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356977"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Erstellen dynamischer Blaupausen mithilfe von Parametern

Eine vollständig definierte Blaupause mit verschiedenen Artefakten (etwa Ressourcengruppen, Resource Manager-Vorlagen, Richtlinien oder Rollenzuweisungen) ermöglicht eine schnelle und konsistente Bereitstellung von Objekten in Azure. Um eine flexible Verwendung dieser wiederverwendbaren Entwurfsmuster und Container zu ermöglichen, unterstützt Azure Blueprint die Verwendung von Parametern. Der Parameter gewährleistet sowohl bei der Definition als auch bei der Zuweisung die Flexibilität, Eigenschaften für die von der Blaupause bereitgestellten Artefakte zu ändern.

Ein einfaches Beispiel ist das Ressourcengruppenartefakt. Bei der Erstellung einer Ressourcengruppe müssen zwei erforderliche Werte angegeben werden: Name und Standort. Gäbe es keine Parameter, müssten Sie beim Hinzufügen einer Ressourcengruppe zur Blaupause diesen Namen und Standort bei jeder Verwendung der Blaupause definieren. Diese Wiederholung würde dazu führen, dass bei jeder Verwendung der Blaupause Artefakte in derselben Ressourcengruppe erstellt werden. Ressourcen innerhalb der Ressourcengruppe würden dadurch dupliziert und einen Konflikt verursachen.

> [!NOTE]
> Zwei verschiedene Blaupausen können eine Ressourcengruppe desselben Namens enthalten.
> Wenn eine in einer Blaupause enthaltene Ressourcengruppe bereits vorhanden ist, erstellt die Blaupause weiterhin die entsprechenden Artefakte in dieser Ressourcengruppe. Dies kann zu einem Konflikt führen, weil zwei Ressourcen mit demselben Namen und demselben Ressourcentyp nicht innerhalb eines Abonnements vorhanden sein dürfen.

Dieses Problem lässt sich mithilfe von Parametern lösen. Mit Blueprints können Sie den Wert für jede Eigenschaft des Artefakts im Rahmen der Zuweisung zu einem Abonnement definieren. Der Parameter ermöglicht die Wiederverwendung einer Blaupause, die eine Ressourcengruppe und andere Ressourcen innerhalb eines einzelnen Abonnements erstellt, ohne einen Konflikt zu verursachen.

## <a name="blueprint-parameters"></a>Blaupausenparameter

Über die REST-API können Parameter für die eigentliche Blaupause erstellt werden. Diese Parameter unterscheiden sich von den Parametern für die einzelnen unterstützten Artefakte. Wenn ein Parameter in der Blaupause erstellt wird, kann er von den Artefakten in dieser Blaupause verwendet werden. Ein Beispiel ist das Präfix für das Benennen der Ressourcengruppe. Das Artefakt kann mithilfe des Blaupausenparameters einen „größtenteils dynamischen“ Parameter erstellen. Da der Parameter auch im Rahmen der Zuweisung definiert werden kann, bietet dieses Muster eine Konsistenz, die die Einhaltung von Benennungsregeln ermöglicht. Anweisungen hierzu finden Sie unter [Festlegen statischer Parameter: Parameter auf Blaupausenebene](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Verwenden der Parameter „secureString“ und „secureObject“

Während ein _Artefakt_ der Resource Manager-Vorlage Parameter der Typen **secureString** und **secureObject** unterstützt, müssen diese in Azure Blueprint jeweils mit einem Azure-Schlüsseltresor verbunden sein.
Diese Sicherheitsmaßnahme verhindert die unsichere Vorgehensweise, Geheimnisse zusammen mit der Blaupause zu speichern, und fördert die Verwendung sicherer Muster. Azure Blueprints unterstützt diese Sicherheitsmaßnahme und erkennt, ob ein _Artefakt_ einer Resource Manager-Vorlage einen der sicheren Parameter enthält. Während der Zuweisung fordert der Dienst dann bei jedem erkannten sicheren Parameter zur Eingabe der folgenden Key Vault-Eigenschaften auf:

- Key Vault-Ressourcen-ID
- Name des Key Vault-Geheimnisses
- Version des Key Vault-Geheimnisses

Wenn für die Blaupausenzuweisung eine **systemseitig zugewiesene verwaltete Identität** verwendet wird, _muss_ der Key Vault, auf den verwiesen wird, im selben Abonnement vorhanden sein, dem auch die Blaupausendefinition zugewiesen ist.

Wenn für die Blaupausenzuweisung eine **benutzerseitig zugewiesene verwaltete Identität** verwendet wird, _kann_ der Key Vault, auf den verwiesen wird, in einem zentralen Abonnement vorhanden sein. Der verwalteten Identität müssen vor der Blaupausenzuweisung die entsprechenden Rechte für den Key Vault gewährt werden.

> [!IMPORTANT]
> In beiden Fällen muss für den Key Vault auf der Seite **Zugriffsrichtlinien** die Option **Zugriff auf Azure Resource Manager für Vorlagenbereitstellung aktivieren** konfiguriert sein. Eine Anleitung zum Aktivieren dieses Features finden Sie unter [Key Vault – Aktivieren der Vorlagenbereitstellung](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Weitere Informationen zu Azure Key Vault finden Sie im [Überblick über Azure Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Parametertypen

### <a name="static-parameters"></a>Statische Parameter

Ein Parameterwert, der in der Definition einer Blaupause definiert ist, wird als **statischer Parameter** bezeichnet, da das Artefakt bei jeder Verwendung der Blaupause mit diesem statischen Wert bereitgestellt wird. Im Beispiel mit der Ressourcengruppe ist dies zwar für den Namen der Ressourcengruppe nicht sonderlich sinnvoll, möglicherweise aber für den Standort. In diesem Fall wird durch jede Zuweisung der Blaupause eine Ressourcengruppe an demselben Standort erstellt – unter dem jeweiligen Namen, der bei der Zuweisung angegeben wird. Dadurch können Sie flexibel entscheiden, was Sie als erforderlich definieren möchten und was bei der Zuweisung geändert werden kann.

#### <a name="setting-static-parameters-in-the-portal"></a>Festlegen statischer Parameter im Portal

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie auf der Seite links die Option **Blaupausendefinitionen**.

1. Klicken Sie auf eine vorhandene Blaupause, und klicken Sie anschließend auf **Blaupause bearbeiten**, oder klicken Sie auf **+ Blaupause erstellen**, und geben Sie die Informationen auf der Registerkarte **Grundlagen** ein.

1. Klicken Sie auf **Weiter: Artefakte**, oder klicken Sie auf die Registerkarte **Artefakte**.

1. Bei Artefakten, die der Blaupause mit Parameteroptionen hinzugefügt werden, wird **X von Y Parametern aufgefüllt** in der Spalte **Parameter** angezeigt. Klicken Sie auf die Artefaktzeile, um die Artefaktparameter zu bearbeiten.

   ![Blaupausenparameter in einer Blaupausendefinition](../media/parameters/parameter-column.png)

1. Die Seite **Artefakt bearbeiten** zeigt Wertoptionen an, die für das ausgewählte Artefakt geeignet sind. Jeder Parameter des Artefakts weist einen Titel, ein Wertfeld und ein Kontrollkästchen auf. Deaktivieren Sie das Kontrollkästchen, um einen **statischen Parameter** zu definieren. Im folgenden Beispiel ist nur _Standort_ ein **statischer Parameter**, weil das Kontrollkästchen deaktiviert ist. Das Kontrollkästchen für _Ressourcengruppenname_ ist aktiviert.

   ![Statische Blaupausenparameter in einem Blaupausenartefakt](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Festlegen statischer Parameter über die REST-API

In jedem REST-API-URI gibt es Variablen, die Sie durch Ihre eigenen Werte ersetzen müssen:

- Ersetzen Sie `{YourMG}` durch den Namen Ihrer Verwaltungsgruppe.
- Ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID.

##### <a name="blueprint-level-parameter"></a>Parameter auf Blaupausenebene

Beim Erstellen einer Blaupause über die REST-API können [Blaupausenparameter](#blueprint-parameters) erstellt werden. Verwenden Sie hierzu das folgende Format für REST-API-URI und Text:

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Anforderungstext

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Nachdem ein Parameter auf Blaupausenebene erstellt wurde, kann er für die dieser Blaupause hinzugefügten Artefakte verwendet werden.
Im folgenden REST-API-Beispiel wird ein Rollenzuweisungsartefakt für die Blaupause erstellt und der Parameter auf Blaupausenebene verwendet.

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Anforderungstext

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

In diesem Beispiel verwendet die Eigenschaft **principalIds** den Blaupausenparameter **owners** durch die Verwendung des Werts `[parameters('owners')]`. Das Festlegen eines Parameters für ein Artefakt unter Verwendung eines Parameters auf Blaupausenebene ist immer noch ein Beispiel für einen **statischen Parameter**. Der Parameter auf Blaupausenebene kann nicht während der Blaupausenzuweisung festgelegt werden und ist bei jeder Zuweisung gleich.

##### <a name="artifact-level-parameter"></a>Parameter auf Artefaktebene

Das Erstellen **statischer Parameter** für ein Artefakt ist ähnlich, akzeptiert jedoch einen direkten Wert anstelle der `parameters()`-Funktion. Im folgenden Beispiel werden zwei statische Parameter erstellt: **tagName** und **tagValue**. Der Wert wird jeweils direkt bereitgestellt und verwendet keinen Funktionsaufruf.

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Anforderungstext

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dynamische Parameter

Das Gegenteil eines statischen Parameters ist ein **dynamischer Parameter**. Dieser Parameter ist nicht in der Blaupause definiert, sondern wird bei jeder Zuweisung der Blaupause definiert. Im Beispiel mit der Ressourcengruppe ist die Verwendung eines **dynamischen Parameters** für den Ressourcengruppennamen sinnvoll. Dadurch wird bei jeder Zuweisung der Blaupause ein anderer Name angegeben. Eine Liste der Blaupausenfunktionen finden Sie in der Referenz [Blaupausenfunktionen](../reference/blueprint-functions.md).

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Festlegen dynamischer Parameter im Portal

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie auf der Seite links die Option **Blaupausendefinitionen**.

1. Klicken Sie mit der rechten Maustaste auf die Blaupause, die Sie zuweisen möchten. Wählen Sie **Blaupause zuweisen** aus, oder klicken Sie auf die Blaupause, die Sie zuweisen möchten, und klicken Sie dann auf die Schaltfläche **Blaupause zuweisen**.

1. Auf der Seite **Blaupause zuweisen** finden Sie den Abschnitt **Artefaktparameter**. Für jedes Artefakt mit mindestens einem **dynamischen Parameter** werden das Artefakt und die zugehörigen Konfigurationsoptionen angezeigt. Geben Sie erforderliche Werte für die Parameter an, bevor Sie die Blaupause zuweisen. Im folgenden Beispiel ist _Name_ ein **dynamischer Parameter**, der definiert werden muss, um die Blaupausenzuweisung abzuschließen.

   ![Dynamische Blaupausenparameter während der Blaupausenzuweisung](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Festlegen dynamischer Parameter über die REST-API

Zum Festlegen von **dynamischen Parametern** während der Zuweisung wird der jeweilige Wert direkt eingegeben. Anstelle einer Funktion wie [parameters()](../reference/blueprint-functions.md#parameters) wird eine passende Zeichenfolge als Wert angegeben. Artefakte für eine Ressourcengruppe werden mit einem Vorlagennamen und den Eigenschaften **name** und **location** definiert. Alle anderen Parameter für das enthaltene Artefakt werden unter **parameters** mit einem **\<Name\>** -**Wert**-Schlüsselpaar definiert. Wenn die Blaupause für einen dynamischen Parameter konfiguriert ist, der bei der Zuweisung nicht angegeben wird, tritt bei der Zuweisung ein Fehler auf.

- REST-API-URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Anforderungstext

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der Liste der [Blaupausenfunktionen](../reference/blueprint-functions.md).
- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](lifecycle.md).
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).