---
title: Programmgesteuertes Erstellen von Azure-Dashboards | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Azure-Dashboards programmgesteuert erstellt werden.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: b24a0397a1365479907fedc6348caa54508dbbb0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60552197"
---
# <a name="programmatically-create-azure-dashboards"></a>Programmgesteuertes Erstellen von Azure-Dashboards

In diesem Dokument wird der Vorgang der programmgesteuerten Erstellung und Veröffentlichung von Azure-Dashboards Schritt für Schritt beschrieben. Als Bezug dient im gesamten Dokument das folgende Dashboard.

![Beispieldashboard](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Übersicht

Freigegebene Dashboards in Azure sind [Ressourcen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) genau wie virtuelle Computer und Speicherkonten.  Daher können sie zur einfacheren Ressourcenverwaltung programmgesteuert über die [Azure Resource Manager-REST-APIs](/rest/api/), die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure), [Azure PowerShell-Befehle](https://docs.microsoft.com/powershell/azure/get-started-azureps) und zusätzlich zu den APIs auch über viele Funktionen im [Azure-Portal](https://portal.azure.com) verwaltet werden.  

Alle diese APIs und Tools bieten Möglichkeiten zum Erstellen, Auflisten, Abrufen, Ändern und Löschen von Ressourcen.  Da Dashboards Ressourcen sind, können Sie Ihre bevorzugte API bzw. Ihr bevorzugtes Tool verwenden.

Unabhängig vom verwendeten Tool müssen Sie eine JSON-Darstellung des Dashboardobjekts erstellen, damit Sie eine API zur Ressourcenerstellung aufrufen können. Dieses Objekt enthält Informationen zu den Teilen (auch als Kacheln bezeichnet) im Dashboard. Es umfasst Größen, Positionen, angebundene Ressourcen und jegliche Benutzeranpassungen.

Dieses JSON-Dokument lässt sich am praktischsten im [Portal](https://portal.azure.com/) erstellen. Dort können Sie die Kacheln interaktiv hinzufügen und positionieren. Dann exportieren Sie das JSON-Objekt. Schließlich erstellen Sie eine Vorlage des Ergebnisses zur späteren Verwendung in Skripts, Programmen und Bereitstellungstools.

## <a name="create-a-dashboard"></a>Erstellen eines Dashboards

Verwenden Sie zum Erstellen eines neuen Dashboards den Befehl „Neues Dashboard“ auf dem Hauptbildschirm des Portals.

![Befehl „Neues Dashboard“](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Dann können Sie über den Kachelkatalog Kacheln suchen und hinzufügen. Kacheln werden per Drag & Drop hinzugefügt. Bei einigen Kacheln kann die Größe über Ziehpunkte geändert werden. Andere unterstützen feste Größen, die im jeweiligen Kontextmenü angezeigt werden.

### <a name="drag-handle"></a>Ziehpunkt
![Ziehpunkt](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Feste Größen im Kontextmenü
![Größen im Kontextmenü](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Freigeben des Dashboards

Nachdem Sie das Dashboard nach Ihren Vorstellungen konfiguriert haben, wird es in den nächsten Schritten veröffentlicht (mit dem Befehl „Freigeben“) und dann mit dem Ressourcen-Explorer die JSON-Darstellung abgerufen.

![Befehl „Freigeben“](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Durch Klicken auf den Befehl „Freigeben“ wird ein Dialogfeld angezeigt, in dem Sie zum Auswählen des Abonnements und der Ressourcengruppe aufgefordert werden, in denen die Veröffentlichung erfolgen soll. Beachten Sie, dass Sie für das Abonnement und die Ressourcengruppe, die Sie auswählen, [über Schreibzugriff verfügen müssen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

![Freigabe und Zugriffssteuerung](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Abrufen der JSON-Darstellung des Dashboards

Die Veröffentlichung dauert nur wenige Sekunden.  Anschließend navigieren Sie im nächsten Schritt zum [Ressourcen-Explorer](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade), um die JSON-Darstellung abzurufen.

![Durchsuchen im Ressourcen-Explorer](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Navigieren Sie im Ressourcen-Explorer zu dem Abonnement und der Ressourcengruppe, die Sie ausgewählt haben. Klicken Sie dann auf die neu erstellte Dashboardressource, um die JSON-Darstellung anzuzeigen.

![JSON-Darstellung im Ressourcen-Explorer](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Erstellen einer Vorlage aus der JSON-Darstellung

Im nächsten Schritt wird aus dieser JSON-Darstellung eine Vorlage erstellt, sodass diese mit den entsprechenden Ressourcenverwaltungs-APIs, Befehlszeilentools oder im Portal programmgesteuert wiederverwendet werden kann.

Zum Erstellen einer Vorlage ist es nicht erforderlich, dass Sie bis ins kleinste Detail mit der JSON-Struktur des Dashboards vertraut sind. In den meisten Fällen behalten Sie die Struktur und Konfiguration der einzelnen Kacheln bei und parametrisieren dann die Gruppe der Azure-Ressourcen, auf die Sie verweisen. Sehen Sie sich das exportierte JSON-Dashboard an, und suchen Sie alle Vorkommen von Azure-Ressourcen-IDs. Das Beispieldashboard umfasst mehrere Kacheln, die alle auf einen einzelnen virtuellen Azure-Computer verweisen. Dies liegt daran, dass das Dashboard nur diese einzelne Ressource umfasst. Wenn Sie den JSON-Beispielcode (am Ende des Dokuments eingefügt) nach „/subscriptions“ durchsuchen, finden Sie mehrere Vorkommen dieser ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Um dieses Dashboard für alle künftig verwendeten virtuellen Computer zu veröffentlichen, müssen Sie jedes Vorkommen dieser Zeichenfolge im JSON-Code parametrisieren. 

Es gibt zwei Arten von APIs, die Ressourcen in Azure erstellen. [Imperative APIs](https://docs.microsoft.com/rest/api/resources/resources), die jeweils eine Ressource erstellen, und ein System zur [vorlagenbasierten Bereitstellung](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), mit dem die Erstellung mehrerer abhängiger Ressourcen mit einem einzigen API-Aufruf orchestriert werden kann. Das Letztere unterstützt die Parametrisierung und Verwendung von Vorlagen auf native Weise, daher wird dieses System für das Beispiel verwendet.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programmgesteuertes Erstellen eines Dashboards über die Vorlage mithilfe einer Vorlagenbereitstellung

Azure bietet die Möglichkeit, die Bereitstellung von mehreren Ressourcen zu orchestrieren. Sie erstellen eine Bereitstellungsvorlage, die die Gruppe der bereitzustellenden Ressourcen sowie die Beziehungen zwischen ihnen darstellt.  Das JSON-Format der einzelnen Ressourcen entspricht jeweils dem Format beim Erstellen der Ressourcen nacheinander. Der Unterschied besteht darin, dass mit der [Vorlagensprache](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) einige Konzepte wie Variablen, Parameter, allgemeine Funktionen usw. hinzugefügt werden. Diese erweiterte Syntax wird nur im Kontext einer Vorlagenbereitstellung unterstützt und funktioniert bei Verwendung mit den oben erwähnten imperativen APIs nicht.

Bei diesem Ansatz muss die Parametrisierung über die Parametersyntax der Vorlage erfolgen.  Sie ersetzen alle zuvor gefundenen Instanzen der Ressourcen-ID wie im Folgenden gezeigt.

### <a name="example-json-property-with-hard-coded-resource-id"></a>JSON-Beispieleigenschaft mit hartcodierter Ressourcen-ID
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>JSON-Beispieleigenschaft basierend auf Vorlagenparametern in eine parametrisierte Version konvertiert

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Sie müssen zudem einige erforderliche Vorlagenmetadaten und die Parameter oben in der JSON-Vorlage wie folgt deklarieren:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Die vollständige funktionierende Vorlage ist am Ende des Dokuments eingefügt.__

Nachdem Sie die Vorlage erstellt haben, können Sie sie mit den [REST-APIs](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), der [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create) oder auf der [Vorlagenbereitstellungsseite des Portals](https://portal.azure.com/#create/Microsoft.Template) bereitstellen.

Hier sehen Sie zwei Versionen unseres Beispiel-Dashboards JSON. Die erste ist die Version, die wir im Portal erstellt haben und die bereits an eine Ressource gebunden war. Die zweite ist die Vorlagenversion, die programmgesteuert an jeden virtuellen Computer gebunden und mithilfe von Azure Resource Manager bereitgestellt werden kann.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-Darstellung des Beispieldashboards (vor der Vorlagenerstellung)

Sie können von folgendem Ergebnis ausgehen, wenn Sie die Anweisungen oben zum Abrufen der JSON-Darstellung eines bereits bereitgestellten Dashboards befolgen. Beachten Sie die hartcodierten Ressourcen-IDs, die angeben, dass das Dashboard auf einen bestimmten virtuellen Azure-Computer verweist.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Vorlagendarstellung des Beispieldashboards

In der Vorlagenversion des Dashboards sind drei Parameter definiert: __virtualMachineName__, __virtualMachineResourceGroup__ und __dashboardName__.  Mit den Parametern können Sie festlegen, dass dieses Dashboard bei jeder Bereitstellung auf einen unterschiedlichen virtuellen Azure-Computer verweist. Die parametrisierten IDs sind hervorgehoben, um anzugeben, dass das Dashboard programmgesteuert so konfiguriert und bereitgestellt werden kann, dass es auf einen beliebigen virtuellen Azure-Computer verweist. Diese Funktion können Sie am einfachsten testen, indem Sie die folgende Vorlage kopieren und in die [Vorlagenbereitstellungsseite des Azure-Portals](https://portal.azure.com/#create/Microsoft.Template) kopieren. 

In diesem Beispiel wird ein Dashboard bereitgestellt. Über die Vorlagensprache können Sie jedoch mehrere Ressourcen bereitstellen und ein oder mehrere Dashboards neben ihnen bündeln. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
