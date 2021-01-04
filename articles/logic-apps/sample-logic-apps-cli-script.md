---
title: Azure CLI-Skriptbeispiel – Erstellen einer Logik-App
description: Beispielskript zum Erstellen einer Logik-App über die Logic Apps-Erweiterung in der Azure CLI.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: e66edb1325d1c603e89f877f1d34f60c136eb1db
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740722"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI-Skriptbeispiel – Erstellen einer Logik-App

Dieses Skript erstellt eine Beispiel-Logik-App über die [Logic Apps-Erweiterung in der Azure CLI](/cli/azure/ext/logic/logic?view=azure-cli-latest) (`az logic`). Eine ausführliche Anleitung zum Erstellen und Verwalten von Logik-Apps über die Azure CLI finden Sie im [Logic Apps-Schnellstart für die Azure CLI](quickstart-logic-apps-azure-cli.md).

> [!WARNING]
> Die Logic Apps-Erweiterung für die Azure-Befehlszeilenschnittstelle ist derzeit eine *experimentelle Erweiterung* , für die *kein Kundensupport* bereitgestellt wird. Verwenden Sie diese CLI-Erweiterung mit Bedacht – insbesondere in Produktionsumgebungen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installation der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest) auf Ihrem lokalen Computer.
* Installation der [Logic Apps-Erweiterung für die Azure-Befehlszeilenschnittstelle](/cli/azure/azure-cli-extensions-list?view=azure-cli-latest) auf Ihrem Computer. Verwenden Sie zum Installieren dieser Erweiterung den folgenden Befehl: `az extension add --name logic`
* Eine [Workflowdefinition](quickstart-logic-apps-azure-cli.md#workflow-definition) für Ihre Logik-App. Diese JSON-Datei muss das [Schema für die Workflowdefinitionssprache](logic-apps-workflow-definition-language.md) einhalten.
* Eine API-Verbindung zu einem E-Mail-Konto über einen unterstützten [Logic Apps-Connector](../connectors/apis-list.md) in derselben Ressourcengruppe wie Ihre Logik-App. In diesem Beispiel wird der [Outlook 365](../connectors/connectors-create-api-office365-outlook.md)-Connector verwendet, aber Sie können auch andere Connectors wie [Outlook.com](../connectors/connectors-create-api-outlook.md) verwenden.

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Überprüfen Sie Ihre Umgebung, bevor Sie beginnen:

* Melden Sie sich beim Azure-Portal an, und führen Sie `az login` aus, um sich zu vergewissern, dass Ihr Abonnement aktiv ist.

* Überprüfen Sie Ihre Version der Azure-Befehlszeilenschnittstelle, indem Sie in einem Terminal oder Befehlsfenster `az --version`ausführen. Die neueste Version finden Sie unter [Versionshinweise für die Azure CLI](/cli/azure/release-notes-azure-cli?tabs=azure-cli&view=azure-cli-latest).

  * Sollten Sie nicht über die neueste Version verfügen, aktualisieren Sie Ihre Installation wie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) beschrieben.

### <a name="sample-workflow-explanation"></a>Erläuterung des Beispielworkflows

Diese Definitionsdatei für den Beispielworkflow erstellt dieselbe einfache Logik-App wie der [Logic Apps-Schnellstart für das Azure-Portal](quickstart-create-first-logic-app-workflow.md). 

Dieser Beispielworkflow: 

1. Gibt ein Schema namens `$schema` für die Logik-App an.

1. Definiert einen Trigger für die Logik-App in der Liste der Trigger (`triggers`). Der Trigger wird alle 3 Stunden wiederholt (`recurrence`). Die Aktionen werden ausgelöst, wenn ein neues Feedelement für den angegebenen RSS-Feed (`feedUrl`) veröffentlicht wird (`When_a_feed_item_is_published`).

1. Definiert eine Aktion für die Logik-App in der Liste der Aktionen (`actions`). Die Aktion sendet eine E-Mail (`Send_an_email_(V2)`) über Microsoft 365 mit Details aus den RSS-Feedelementen, wie im Textabschnitt (`body`) der Eingaben (`inputs`) der Aktion angegeben.

## <a name="sample-workflow-definition"></a>Definition des Beispielworkflows

Bevor Sie das Beispielskript ausführen, müssen Sie zunächst eine Beispiel[workflowdefinition](#prerequisites) erstellen.

1. Erstellen Sie eine JSON-Datei namens `testDefinition.json` auf Ihrem Computer. 

1. Kopieren Sie den folgenden Inhalt in die JSON-Datei: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Aktualisieren Sie die Platzhalterwerte durch Ihre eigenen Informationen:

    1. Ersetzen Sie die Platzhalteradresse (`"To": "test@example.com"`). Sie müssen eine E-Mail-Adresse verwenden, die mit Logic Apps-Connectors kompatibel ist. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).

    1. Ersetzen Sie zusätzliche Connectordetails, wenn Sie einen anderen E-Mail-Connector als den Office 365 Outlook-Connector verwenden.

    1. Ersetzen Sie die Platzhalter-Abonnementwerte (`00000000-0000-0000-0000-000000000000`) für Ihre Verbindungsbezeichner (`connectionId` und `id`) unter dem connections-Parameter (`$connections`) durch Ihre eigenen Abonnementwerte.

1. Speichern Sie die Änderungen.

## <a name="sample-script"></a>Beispielskript

> [!NOTE]
> Dieses Beispiel wurde für die `bash`-Shell geschrieben. Wenn Sie dieses Beispiel in einer anderen Shell ausführen möchten, z. B. in Windows PowerShell oder in der Eingabeaufforderung, müssen Sie möglicherweise Änderungen am Skript vornehmen.

Bevor Sie dieses Beispielskript ausführen, führen Sie diesen Befehl aus, um eine Verbindung mit Azure herzustellen:

```azurecli-interactive

az login

```

Navigieren Sie als Nächstes zu dem Verzeichnis, in dem Sie Ihre Workflowdefinition erstellt haben. Wenn Sie z. B. die JSON-Workflowdefinitionsdatei auf Ihrem Desktop erstellt haben:

```azurecli

cd ~/Desktop

```

Führen Sie dieses Skript aus, um eine Logik-App zu erstellen. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nachdem Sie die Verwendung des Beispielskripts beendet haben, führen Sie den folgenden Befehl aus, um Ihre Ressourcengruppe und alle deren geschachtelten Ressourcen, einschließlich der Logik-App, zu entfernen.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Beispielskript werden die folgenden Befehle verwendet, um eine neue Ressourcengruppe und eine Logik-App zu erstellen.

| Get-Help | Notizen |
| ------- | ----- |
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Erstellt eine Ressourcengruppe, in der die Ressourcen Ihrer Logik-App gespeichert sind. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create) | Erstellt eine Logik-App auf Grundlage des im Parameter `--definition` definierten Workflows. |
| [`az group delete`](/cli/azure/vm/extension?view=azure-cli-latest) | Löscht eine Ressourcengruppe und alle ihre geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure-Befehlszeilenschnittstelle finden Sie in der [Azure CLI-Dokumentation](/cli/azure/?view=azure-cli-latest).

Weitere CLI-Skriptbeispiele für Logik-Apps finden Sie [Browser für Codebeispiele von Microsoft](/samples/browse/?products=azure-logic-apps).
