---
title: Exportieren von Azure Policy-Ressourcen
description: Erfahren Sie, wie Sie Azure Policy-Ressourcen wie Richtliniendefinitionen und Richtlinienzuweisungen nach GitHub exportieren.
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 923b063244f6f47def1c3e6a63d6e4d6b3b88083
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919564"
---
# <a name="export-azure-policy-resources"></a>Exportieren von Azure Policy-Ressourcen

Dieser Artikel enthält Informationen zum Exportieren vorhandener Azure Policy-Ressourcen. Das Exportieren Ihrer Ressourcen ist hilfreich und wird für die Sicherung empfohlen. Es ist aber auch ein wichtiger Schritt auf Ihrem Weg mit der Cloud Governance und bei der Behandlung Ihrer Richtlinien im Sinne von [Policy-as-Code](../concepts/policy-as-code.md). Azure Policy-Ressourcen können über das [Azure-Portal](#export-with-azure-portal), [Azure CLI](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell) und jedes der unterstützten SDKs exportiert werden.

## <a name="export-with-azure-portal"></a>Exportieren mit dem Azure-Portal

Führen Sie die folgenden Schritte aus, um eine Richtliniendefinition aus dem Azure-Portal zu exportieren:

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Richtlinie** suchen und die entsprechende Option auswählen.

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Definitionen** aus.

1. Verwenden Sie die Schaltfläche **Definitionen exportieren**, oder wählen Sie die Auslassungspunkte in der Zeile einer Richtliniendefinition und dann **Definition exportieren** aus.

1. Wählen Sie die Schaltfläche **Mit GitHub anmelden** aus. Wenn Sie sich noch nicht bei GitHub authentifiziert haben, um Azure Policy zum Exportieren der Ressource zu autorisieren, überprüfen Sie in dem neuen Fenster, das geöffnet wird, welchen Zugriff [GitHub Actions](https://github.com/features/actions) benötigt, und wählen Sie **Authorize AzureGitHubActions** (AzureGitHubActions autorisieren), um mit dem Exportvorgang fortzufahren. Sobald der Vorgang abgeschlossen ist, wird das neue Fenster automatisch geschlossen.

1. Legen Sie auf der Registerkarte **Grundlagen** die folgenden Optionen fest, und wählen Sie dann die Registerkarte **Richtlinien** oder die Schaltfläche **Weiter: Richtlinien** unten auf der Seite aus.

   - **Repositoryfilter**: Legen Sie hierfür _Meine Repositorys_ fest, sodass nur die Repositorys in Ihrem Besitz angezeigt werden, oder _Alle Repositorys_, um alle anzuzeigen, denen Sie den Zugriff auf GitHub Actions gewährt haben.
   - **Repository**: Legen Sie hierfür das Repository fest, in das Sie die Azure Policy-Ressourcen exportieren möchten.
   - **Branch**: Legen Sie den Branch im Repository fest. Die Verwendung eines anderen Branches als des standardmäßigen ist eine gute Möglichkeit, Ihre Updates zu validieren, bevor Sie sie in den Quellcode einbringen.
   - **Verzeichnis:** Der _Ordner auf Stammebene_, in den die Azure Policy-Ressourcen exportiert werden sollen. Die Unterordner in diesem Verzeichnis werden auf Basis der exportierten Ressourcen erstellt.

1. Legen Sie auf der Registerkarte **Richtlinien** den Suchbereich fest, indem Sie die Auslassungspunkte und dann eine Kombination von Verwaltungsgruppen, Abonnements oder Ressourcengruppen auswählen.
   
1. Suchen Sie mit der Schaltfläche **Richtliniendefinition(en) hinzufügen** den Bereich, für den Objekte exportiert werden sollen. Wählen Sie im daraufhin geöffneten seitlichen Fenster die einzelnen zu exportierenden Objekte aus. Filtern Sie die Auswahl nach Suchfeld oder Typ. Nachdem Sie alle zu exportierenden Objekte ausgewählt haben, verwenden Sie die Schaltfläche **Hinzufügen** am unteren Rand der Seite.

1. Wählen Sie für jedes ausgewählte Objekt die gewünschten Exportoptionen für eine Richtliniendefinition aus, z. B. _Nur Definition_ oder _Definition und Zuweisung(en)_ . Wählen Sie dann die Registerkarte **Überprüfen + exportieren** oder die Schaltfläche **Weiter: Überprüfen + exportieren** am unteren Rand der Seite aus.

   > [!NOTE]
   > Wenn die Option _Definition und Zuweisung(en)_ ausgewählt wird, werden nur die Richtlinienzuweisungen exportiert, die innerhalb des Bereichs liegen, der beim Hinzufügen der Richtliniendefinition vom Filter festgelegt wird.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + exportieren** die Übereinstimmung im Detail, und verwenden Sie dann die Schaltfläche **Exportieren** unten auf der Seite.

1. Überprüfen Sie das GitHub-Repository, den Branch und den _Ordner auf Stammebene_, um zu sehen, dass die ausgewählten Ressourcen nun in die Quellcodeverwaltung exportiert werden.

Die Azure Policy-Ressourcen werden in die folgende Struktur innerhalb des ausgewählten GitHub-Repositorys und des _Ordners auf Stammebene_ exportiert:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exportieren mit Azure CLI

Azure Policy-Definitionen, Initiativen und Zuweisungen können jeweils im JSON-Code mit [Azure CLI](/cli/azure/install-azure-cli) exportiert werden. Jeder dieser Befehle verwendet einen **name**-Parameter, um anzugeben, für welches Objekt der JSON-Code verwendet werden soll. Die **name**-Eigenschaft ist oft eine _GUID_ und nicht der **displayName** des Objekts.

- Definition – [az policy definition show](/cli/azure/policy/definition#az_policy_definition_show)
- Initiative – [az policy set-definition show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Zuweisung – [az policy assignment show](/cli/azure/policy/assignment#az_policy_assignment_show)

Im Folgenden finden Sie ein Beispiel für das Abrufen des JSON-Codes für eine Richtliniendefinition, wobei **name** den Wert _VirtualMachineStorage_ hat:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exportieren mit Azure PowerShell

Azure Policy-Definitionen, Initiativen und Zuweisungen können jeweils im JSON-Code mit [Azure PowerShell](/powershell/azure/) exportiert werden. Jedes dieser Cmdlets verwendet einen **Name**-Parameter, um anzugeben, für welches Objekt der JSON-Code verwendet werden soll. Die **Name**-Eigenschaft ist oft eine _GUID_ und nicht der **displayName** des Objekts.

- Definition – [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Initiative – [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Zuweisung – [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Im Folgenden finden Sie ein Beispiel für das Abrufen des JSON-Codes für eine Richtliniendefinition, wobei **Name** den Wert _VirtualMachineStorage_ hat:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](programmatically-create.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
