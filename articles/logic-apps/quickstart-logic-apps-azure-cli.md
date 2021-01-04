---
title: Erstellen und Verwalten von Logik-Apps mit der Azure-Befehlszeilenschnittstelle
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine Logik-App erstellen und sie mithilfe von Vorgängen wie Auflisten, Anzeigen (Abrufen), Aktualisieren und Löschen verwalten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: bc172fd1702addf8f4e34094452a779b09320a4d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033373"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Schnellstart: Erstellen und Verwalten von Logik-Apps mithilfe der Azure-Befehlszeilenschnittstelle

In dieser Schnellstartanleitung erfahren Sie, wie Sie Logik-Apps mithilfe der [Logic Apps-Erweiterung für die Azure-Befehlszeilenschnittstelle](/cli/azure/ext/logic/logic) (`az logic`) erstellen und verwalten. Über die Befehlszeile können Sie eine Logik-App erstellen und dabei die JSON-Datei für eine Logik-App-Workflowdefinition verwenden. Anschließend können Sie Ihre Logik-App durch Ausführen von Vorgängen wie `list`, `show` (`get`), `update` und `delete` über die Befehlszeile verwalten.

> [!WARNING]
> Die Logic Apps-Erweiterung für die Azure-Befehlszeilenschnittstelle ist derzeit eine *experimentelle Erweiterung*, für die *kein Kundensupport* bereitgestellt wird. Verwenden Sie diese CLI-Erweiterung mit Bedacht – insbesondere in Produktionsumgebungen.

Wenn Sie noch nicht mit Logic Apps vertraut sind, können Sie sich auch darüber informieren, wie Sie Ihre ersten Logik-Apps [über das Azure-Portal](quickstart-create-first-logic-app-workflow.md), [in Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) und [in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installation der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) auf Ihrem lokalen Computer.
* Installation der [Logic Apps-Erweiterung für die Azure-Befehlszeilenschnittstelle](/cli/azure/azure-cli-extensions-list) auf Ihrem Computer. Verwenden Sie zum Installieren dieser Erweiterung den folgenden Befehl: `az extension add --name logic`
* Eine [Azure-Ressourcengruppe](#example---create-resource-group) für die Erstellung Ihrer Logik-App.

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Überprüfen Sie Ihre Umgebung, bevor Sie beginnen:

* Melden Sie sich beim Azure-Portal an, und führen Sie `az login` aus, um sich zu vergewissern, dass Ihr Abonnement aktiv ist.
* Überprüfen Sie Ihre Version der Azure-Befehlszeilenschnittstelle, indem Sie in einem Terminal oder Befehlsfenster `az --version`ausführen. Die neueste Version finden Sie unter [Versionshinweise für die Azure CLI](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Sollten Sie nicht über die neueste Version verfügen, aktualisieren Sie Ihre Installation wie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben.

### <a name="example---create-resource-group"></a>Beispiel: Erstellen einer Ressourcengruppe

Sollten Sie noch nicht über eine Ressourcengruppe für Ihre Logik-App verfügen, erstellen Sie die Gruppe mithilfe des Befehls `az group create`. Mit dem folgenden Befehl wird beispielsweise eine Ressourcengruppe namens `testResourceGroup` am Standort `westus` erstellt:

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

In der Ausgabe wird `provisioningState` als `Succeeded` angezeigt, wenn Ihre Ressourcengruppe erfolgreich erstellt wurde:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Workflowdefinition

Zum [Erstellen einer neuen Logik-App](#create-logic-apps-from-cli) oder [Aktualisieren einer vorhandenen Logik-App](#update-logic-apps-from-cli) über die Azure-Befehlszeilenschnittstelle benötigen Sie eine Workflowdefinition für Ihre Logik-App. Im Azure-Portal können Sie die zugrunde liegende Workflowdefinition Ihrer Logik-App im JSON-Format anzeigen, indem Sie von der Ansicht **Designeransicht** zur **Codeansicht** wechseln.

Beim Ausführen der Befehle zum Erstellen oder Aktualisieren Ihrer Logik-App wird Ihre Workflowdefinition als erforderlicher Parameter (`--definition`) hochgeladen. Die Workflowdefinition muss als JSON-Datei erstellt werden, die den Angaben im [Schemareferenzleitfaden für die Workflowdefinitionssprache in Azure Logic Apps](./logic-apps-workflow-definition-language.md) entspricht.

## <a name="create-logic-apps-from-cli"></a>Erstellen von Logik-Apps über die Befehlszeilenschnittstelle

Für die Erstellung eines Logik-App-Workflows über die Azure-Befehlszeilenschnittstelle können Sie den Befehl [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) mit einer JSON-Datei für die Definition verwenden.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

Der Befehl muss die folgenden [erforderlichen Parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) enthalten:

| Parameter | Wert | BESCHREIBUNG |
| --------- | ----- | ----------- |
| Workflowdefinition | `--definition` | Eine JSON-Datei mit der [Workflowdefinition](#workflow-definition) Ihrer Logik-App. |
| Standort | `--location -l` | Die Azure-Region, in der sich Ihre Logik-App befindet. |
| Name | `--name -n` | Der Name Ihrer Logik-App. Der Name darf nur Buchstaben, Ziffern, Bindestriche (`-`), Unterstriche (`_`), Klammern (`()`) und Punkte (`.`) enthalten. Außerdem muss der Name regionsübergreifend eindeutig sein. |
| Ressourcengruppenname | `--resource-group -g` | Die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), in der Sie Ihre Logik-App erstellen möchten. Sollte noch keine Ressourcengruppe für Ihre Logik-App vorhanden sein, [erstellen Sie eine Ressourcengruppe](#example---create-resource-group), bevor Sie beginnen. |

Sie können auch zusätzliche [optionale Parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) hinzufügen, um die Zugriffssteuerung, die Endpunkte, das Integrationskonto, die Integrationsdienstumgebung, den Zustand und die Ressourcentags Ihrer Logik-App zu konfigurieren.

### <a name="example---create-logic-app"></a>Beispiel: Erstellen einer Logik-App

In diesem Beispiel wird ein Workflow namens `testLogicApp` in der Ressourcengruppe `testResourceGroup` am Standort `westus` erstellt. Die JSON-Datei `testDefinition.json` enthält die Workflowdefinition.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Wenn Ihr Workflow erfolgreich erstellt wurde, wird von der Befehlszeilenschnittstelle der JSON-Code Ihrer neuen Workflowdefinition angezeigt. War die Workflowerstellung nicht erfolgreich, finden Sie weitere Informationen in der [Liste möglicher Fehler](#errors).

## <a name="update-logic-apps-from-cli"></a>Aktualisieren von Logik-Apps über die Befehlszeilenschnittstelle

Der Workflow einer Logik-App kann über die Azure-Befehlszeilenschnittstelle auch aktualisiert werden. Verwenden Sie hierzu den Befehl [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create).

Der Befehl muss die gleichen [erforderlichen Parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) enthalten wie beim [Erstellen von Logik-Apps über die Befehlszeilenschnittstelle](#create-logic-apps-from-cli). Darüber hinaus können Sie die gleichen [optionalen Parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) hinzufügen wie beim Erstellen einer Logik-App.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Beispiel: Aktualisieren einer Logik-App

In diesem Beispiel wird der [im vorherigen Abschnitt erstellte Beispielworkflow](#example---create-logic-app) mit einer anderen JSON-Definitionsdatei (`newTestDefinition.json`) aktualisiert. Außerdem werden zwei Ressourcentags (`testTag1` und `testTag2`) mit Beschreibungswerten hinzugefügt.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Wenn Ihr Workflow erfolgreich aktualisiert wurde, wird von der Befehlszeilenschnittstelle die aktualisierte Workflowdefinition Ihrer Logik-App angezeigt. War die Aktualisierung nicht erfolgreich, finden Sie weitere Informationen in der [Liste möglicher Fehler](#errors).

## <a name="delete-logic-apps-from-cli"></a>Löschen von Logik-Apps über die Befehlszeilenschnittstelle

Der Workflow einer Logik-App kann über die Azure-Befehlszeilenschnittstelle mithilfe des Befehls [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) gelöscht werden.

Der Befehl muss die folgenden [erforderlichen Parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters) enthalten:

| Parameter | Wert | BESCHREIBUNG |
| --------- | ----- | ----------- |
| Name | `--name -n` | Der Name Ihrer Logik-App. |
| Ressourcengruppenname | `-resource-group -g` | Die Ressourcengruppe, in der sich Ihre Logik-App befindet. |

Sie können auch den [optionalen Parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) `--yes -y` einschließen, um Bestätigungsaufforderungen zu überspringen.

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

Anschließend werden Sie von der Befehlszeilenschnittstelle aufgefordert, das Löschen Ihrer Logik-App zu bestätigen. Wenn Sie die Bestätigungsaufforderung überspringen möchten, können Sie dem Befehl den optionalen Parameter `--yes -y` hinzufügen.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Zur Bestätigung der Löschung einer Logik-App können Sie [Ihre Logik-Apps über die Befehlszeilenschnittstelle auflisten](#list-logic-apps-in-cli) oder Ihre Logik-Apps im Azure-Portal anzeigen.

### <a name="example---delete-logic-app"></a>Beispiel: Löschen einer Logik-App

In diesem Beispiel wird der [in einem vorherigen Abschnitt erstellte Beispielworkflow](#example---create-logic-app) gelöscht.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Nachdem Sie auf die Bestätigungsaufforderung mit `y` reagiert haben, wird die Logik-App gelöscht.

## <a name="show-logic-apps-in-cli"></a>Anzeigen von Logik-Apps über die Befehlszeilenschnittstelle

Ein bestimmter Logik-App-Workflow kann mithilfe des Befehls [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) abgerufen werden.

```azurecli

az logic workflow show --name
                       --resource-group

```

Der Befehl muss die folgenden [erforderlichen Parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters) enthalten:

| Parameter | Wert | BESCHREIBUNG |
| --------- | ----- | ----------- |
| Name | `--name -n` | Der Name Ihrer Logik-App. |
| Ressourcengruppenname | `--resource-group -g` | Der Name der Ressourcengruppe, in der sich Ihre Logik-App befindet. |

### <a name="example---get-logic-app"></a>Beispiel: Abrufen einer Logik-App

In diesem Beispiel wird die Logik-App `testLogicApp` in der Ressourcengruppe `testResourceGroup` mit vollständigen Protokollen für das Debuggen zurückgegeben.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Auflisten von Logik-Apps über die Befehlszeilenschnittstelle

Mithilfe des Befehls [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) können Sie Ihre Logik-Apps nach Abonnement auflisten. Durch diesen Befehl wird der JSON-Code für die Workflows Ihrer Logik-Apps zurückgegeben.

Die Ergebnisse können nach folgenden [optionalen Parametern](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters) gefiltert werden:

| Parameter | Wert | BESCHREIBUNG |
| --------- | ----- | ----------- |
| Ressourcengruppenname | `--resource-group -g` | Der Name der Ressourcengruppe, nach der Sie Ihre Ergebnisse filtern möchten. |
| Anzahl von Elementen | `--top` | Die Anzahl von Elementen in Ihren Ergebnissen. |
| Filtern | `--filter` | Die Art von Filter, den Sie für Ihre Liste verwenden möchten. Sie können nach Zustand (`State`), nach Trigger (`Trigger`) und nach dem Bezeichner der referenzierten Ressource (`ReferencedResourceId`) filtern. |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Beispiel: Auflisten von Logik-Apps

In diesem Beispiel werden alle aktivierten Workflows in der Ressourcengruppe `testResourceGroup` in einem ASCII-Tabellenformat zurückgegeben.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Errors

Der folgende Fehler gibt an, dass die Logic Apps-Erweiterung für die Azure-Befehlszeilenschnittstelle nicht installiert ist. [Installieren Sie die Logic Apps-Erweiterung](#prerequisites) auf Ihrem Computer, wie in den Voraussetzungen beschrieben.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

Der folgende Fehler deutet ggf. darauf hin, dass der Dateipfad zum Hochladen Ihrer Workflowdefinition nicht korrekt ist:

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Globale Parameter

Befehle vom Typ `az logic` können mit folgenden optionalen globalen Parametern für die Azure-Befehlszeilenschnittstelle verwendet werden:

| Parameter | Wert | BESCHREIBUNG |
| --------- | ----- | ----------- |
| Ausgabeformat | `--output -o` | Ermöglicht das Ändern des [Ausgabeformats](/cli/azure/format-output-azure-cli) (Standardformat: JSON). |
| Nur Fehler anzeigen | `--only-show-errors` | Ermöglicht das Unterdrücken von Warnungen, sodass nur Fehler angezeigt werden. |
| Ausführlich | `--verbose` | Dient zum Anzeigen ausführlicher Protokolle. |
| Debuggen | `--debug` | Dient zum Anzeigen aller Debugprotokolle. |
| Hilfemeldung | `--help -h` | Dient zum Anzeigen des Hilfedialogfelds. |
| Abfrage | `--query` | Dient zum Festlegen einer JMESPath-Abfragezeichenfolge für eine JSON-Ausgabe. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure-Befehlszeilenschnittstelle finden Sie in der [Azure CLI-Dokumentation](/cli/azure/).

Weitere CLI-Skriptbeispiele für Logik-Apps finden Sie [Browser für Codebeispiele von Microsoft](/samples/browse/?products=azure-logic-apps).

Als Nächstes können Sie mithilfe eines Beispielskripts und einer Workflowdefinition eine Beispiel-Logik-App über die Azure CLI erstellen.

> [!div class="nextstepaction"]
> [Azure CLI-Skriptbeispiel – Erstellen einer Logik-App](sample-logic-apps-cli-script.md)
