---
title: Erstellen, Bearbeiten oder Erweitern von Logik-App JSON-Workflowdefinitionen
description: So schreiben, bearbeiten und erweitern Sie JSON-Workflowdefinitionen Ihrer Logik-Apps in Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 9163071237041d7c8510a644c573e3763434bb0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014396"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Erstellen, Bearbeiten oder Erweitern von JSON-Code für Logik-App-Workflowdefinitionen in Azure Logic Apps

Wenn Sie Enterprise Integration-Lösungen mit automatisierten Workflows in [Azure Logic Apps](../logic-apps/logic-apps-overview.md) erstellen, verwenden die zugrunde liegenden Logik-App-Definitionen einfachen und deklarativen JSON-Code (JavaScript Object Notation) sowie das [Schema der Definitionssprache für Workflows](../logic-apps/logic-apps-workflow-definition-language.md) zur Beschreibung und Überprüfung der Definitionen. Diese Formate erleichtern Benutzern ohne umfassende Codekenntnisse das Lesen und Verständnis von Logik-App-Definitionen.
Wenn Sie die Erstellung und Bereitstellung von Logik-Apps automatisieren möchten, können Sie Logik-App-Definitionen als [Azure-Ressourcen](../azure-resource-manager/management/overview.md) in [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md) einbinden.
Zum Erstellen, Verwalten und Bereitstellen von Logik-Apps können Sie dann [Azure PowerShell](/powershell/module/az.logicapp), die [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) oder die [Azure Logic Apps-REST-APIs](/rest/api/logic/) verwenden.

Um mit Logik-App-Definitionen in JSON zu arbeiten, öffnen Sie im Azure-Portal oder in Visual Studio den Codeansichts-Editor, oder kopieren Sie die Definition in einen Editor Ihrer Wahl.
Falls Sie noch nicht mit Logik-Apps gearbeitet haben, sollten Sie zunächst die Schnellstartanleitung zum [Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) lesen.

> [!NOTE]
> Einige Funktionen von Azure Logic Apps (beispielsweise das Definieren von Parametern und mehreren Triggern in Logik-App-Definitionen) sind nicht im Designer für Logik-Apps, sondern nur in JSON verfügbar.
> Für diese Aufgaben müssen Sie daher in der Codeansicht oder einem anderen Editor arbeiten.

## <a name="edit-json---azure-portal"></a>Bearbeiten von JSON-Code – Azure-Portal

1. Melden Sie sich beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> an.

2. Klicken Sie im Menü auf der linken Seite auf **Alle Dienste**.
Suchen Sie mithilfe des Suchfelds nach „Logik-Apps“, und wählen Sie Ihre Logik-App dann in den Suchergebnissen aus.

3. Klicken Sie im Menü Ihrer Logik-App unter **Entwicklungstools** auf **Logik-App-Codeansicht**.

   Der Codeansichts-Editor wird geöffnet und zeigt die Definition der Logik-App im JSON-Format an.

## <a name="edit-json---visual-studio"></a>Bearbeiten von JSON-Code – Visual Studio

Bevor Sie die Definition Ihrer Logik-App in Visual Studio bearbeiten können, müssen Sie sicherstellen, dass [die erforderlichen Tools installiert](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) sind.
Informationen zum Erstellen einer Logik-App mit Visual Studio finden Sie unter [Schnellstart: Automatisieren von Aufgaben und Prozessen mit Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

In Visual Studio können Sie Logik-Apps öffnen, die direkt über das Azure-Portal oder als Azure Resource Manager-Projekte über Visual Studio erstellt und bereitgestellt wurden.

1. Öffnen Sie die Visual Studio-Projektmappe oder das [Azure-Ressourcengruppenprojekt](../azure-resource-manager/management/overview.md) mit der Logik-App.

2. Suchen Sie nach der Definition Ihrer Logik-App, und öffnen Sie sie. Die Definition wird standardmäßig in einer [Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md) mit dem Namen **LogicApp.json** angezeigt.
Sie können diese Vorlage für die Bereitstellung in verschiedenen Umgebungen anpassen.

3. Öffnen Sie das Kontextmenü für Ihre Logik-App-Definition und Vorlage.
Wählen Sie **Öffnen mit Logik-App-Designer** aus.

   ![Öffnen der Logik-App in einer Visual Studio-Projektmappe](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Sie über die neuesten Updates für Visual Studio verfügen.

4. Klicken Sie unten im Designer auf **Codeansicht**.

   Der Codeansichts-Editor wird geöffnet und zeigt die Definition der Logik-App im JSON-Format an.

5. Um zur Designeransicht zurückzukehren, klicken Sie unten im Codeansichts-Editor auf **Entwurf**.

## <a name="parameters"></a>Parameter

Der Bereitstellungslebenszyklus umfasst in der Regel verschiedene Umgebungen zur Entwicklung, zum Testen, Staging und für die Produktion. Wenn Sie Werte haben, die Sie in ihrer Logik-App wieder verwenden möchten, ohne hartcodieren oder – je nach Ihren Bereitstellungsanforderungen – variieren zu müssen, können Sie eine [Azure Resource Manager-Vorlage](../azure-resource-manager/management/overview.md) für die Workflowdefinition erstellen, um auch die Bereitstellung von Logik-Apps zu automatisieren.

Führen Sie diese allgemeinen Schritte aus, um stattdessen diese Werte zu *parametrisieren* bzw. Parameter für diese zu definieren und zu verwenden. Anschließend können Sie die Werte in einer separaten Parameterdatei bereitstellen, die diese Werte an Ihre Vorlage übergibt. Auf diese Weise können Sie diese Werte einfacher ändern, ohne Ihre Logik-App aktualisieren und erneut bereitstellen zu müssen. Ausführlichere Informationen finden Sie unter [Overview: Automatisieren der Bereitstellung für Logik-Apps mit Azure Resource Manager-Vorlagen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. Definieren Sie in Ihrer Vorlage Vorlagenparameter und Workflowdefinitionsparameter, um die Werte zu akzeptieren, die zur Bereitstellung bzw. zur Laufzeit verwendet werden sollen.

   Vorlagenparameter werden in einem Parameterabschnitt definiert, der sich außerhalb der Workflowdefinition befindet, wohingegen Workflowdefinitionsparameter in einem Parameterabschnitt definiert werden, der sich in der Workflow definition befindet.

1. Ersetzen Sie die hartcodierten Werte durch Ausdrücke, die auf diese Parameter verweisen. Vorlagenausdrücke verwenden Syntax, die von Workflowdefinitionsausdrücken abweicht.

   Verkomplizieren Sie Ihren Code nicht, indem Sie in Workflowdefinitionsausdrücken, die bei der Bereitstellung ausgewertet werden, keine Vorlagenausdrücke verwenden, die bei zur Laufzeit ausgewertet werden. Verwenden Sie nur Vorlagenausdrücke außerhalb der Workflowdefinition. Verwenden Sie nur Workflowdefinitionsausdrücke in der Workflowdefinition.

   Wenn Sie die Werte für die Workflowdefinitionsparameter angeben, können Sie auf Vorlagenparameter verweisen, indem Sie den Parameterabschnitt verwenden, der sich außerhalb der Workflowdefinition befindet, sich aber noch in der Ressourcendefinition Ihrer Logik-App befindet. Auf diese Weise können Sie Vorlagenparameterwerte an die Workflowdefinitionsparameter übergeben.

1. Speichern Sie die Werte für die Parameter in einer [separaten Parameterdatei](../azure-resource-manager/templates/parameter-files.md), und schließen Sie diese Datei in die Bereitstellung ein.

## <a name="process-strings-with-functions"></a>Verarbeiten von Zeichenfolgen mit Funktionen

Logic Apps verfügt über verschiedene Funktionen zum Arbeiten mit Zeichenfolgen.
Nehmen Sie z.B. an, dass Sie einen Firmennamen aus einem Auftrag einem anderen System übergeben möchten.
Allerdings sind Sie sich nicht über die ordnungsgemäße Behandlung für die Zeichencodierung sicher.
Sie könnten eine base64-Codierung für diese Zeichenfolge ausführen, aber um Escapezeichen in der URL zu vermeiden, können Sie stattdessen mehrere Zeichen ersetzen. Außerdem müssen Sie nur eine Teilzeichenfolge des Firmennamens verwenden, da die ersten fünf Zeichen nicht verwendet werden.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

In diesem Beispiel werden die Schritte beschrieben, in denen diese Zeichenfolge von innen nach außen verarbeitet wird:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Rufen Sie die [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) des Firmennamens ab, um die Gesamtzahl der Zeichen zu erhalten.

2. Um eine kürzere Zeichenfolge zu erhalten, subtrahieren Sie `5`.

3. Nun erhalten Sie [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md).
Beginnen Sie bei Index `5`, und fahren Sie mit dem Rest der Zeichenfolge fort.

4. Wandeln Sie diese Teilzeichenfolge in eine [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md)-Zeichenfolge um.

5. Ersetzen Sie mit [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) alle `+`-Zeichen durch `-`-Zeichen.

6. Ersetzen Sie schließlich mit [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) alle `/`-Zeichen durch `_`-Zeichen.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Zuordnen von Listenelementen zu Eigenschaftswerten und Verwenden von Zuordnungen als Parameter

Um basierend auf einem Eigenschaftswert unterschiedliche Ergebnisse zu erzielen, können Sie eine Zuordnung erstellen, die für jeden Eigenschaftswert nach der Übereinstimmung mit einem Ergebnis sucht, und dann diese Zuordnung als Parameter verwenden.

Dieser Workflow definiert z.B. einige Kategorien als Parameter und eine Zuordnung, die für jede dieser Kategorien nach der Übereinstimmung mit einer bestimmten URL sucht.
Der Workflow ruft zunächst eine Liste von Artikeln auf. Anschließend verwendet der Workflow die Zuordnung, um für jeden Artikel die URL zu finden, die mit der Kategorie übereinstimmt.

*   Mit der Funktion [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) wird überprüft, ob die Kategorie einer bekannten definierten Kategorie entspricht.

*   Nach Eingang einer übereinstimmenden Kategorie ruft das Beispiel das Element mithilfe eckiger Klammern aus der Zuordnung ab: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Abrufen von Daten mit Date-Funktionen

Zum Abrufen von Daten aus einer Datenquelle, die nicht nativ *Trigger* unterstützt, können Sie stattdessen Date-Funktionen zum Arbeiten mit Uhrzeiten und Datumsangaben verwenden.
Dieser Ausdruck ermittelt z.B., wie lange die Schritte dieses Workflows von innen nach außen dauern:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Extrahieren Sie von der `order`-Aktion aus die `startTime`.
2. Rufen Sie die aktuelle Uhrzeit mit `utcNow()` ab.
3. Subtrahieren Sie eine Sekunde:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Sie können auch andere Zeiteinheiten verwenden, z.B. `minutes` oder `hours`.

3. Jetzt können Sie diese beiden Werte vergleichen.

   Wenn der erste Wert kleiner als der zweite Wert ist, bedeutet dies, dass mehr als eine Sekunde verstrichen ist, seit der Auftrag erteilt wurde.

Zum Formatieren von Datumsangaben können Sie Zeichenfolgenformatierer verwenden. Zum Abrufen von RFC1123 verwenden Sie beispielsweise [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md).
Erfahren Sie mehr über die [Formatierung von Datumsangaben](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen von Schritten basierend auf einer Bedingung (Bedingungsanweisungen)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Ausführen von Schritten basierend auf verschiedenen Werten (switch-Anweisungen)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ausführen und Wiederholen von Schritten (Schleifen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ausführen oder Zusammenführen paralleler Schritte (Verzweigungen)](../logic-apps/logic-apps-control-flow-branches.md)
* [Ausführen von Schritten basierend auf gruppierten Aktionsstatus (Bereiche)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Erfahren Sie mehr über die [Definitionssprache für Workflows für Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).
* Erfahren Sie mehr über die [Workflowaktionen und -trigger für Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md).
