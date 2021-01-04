---
title: Struktur von Azure-Dashboards
description: Durchlaufen Sie die JSON-Struktur eines Azure-Dashboards mithilfe eines Beispieldashboards. Enthält Verweise auf Ressourceneigenschaften.
ms.topic: conceptual
ms.date: 12/20/2019
ms.openlocfilehash: d37e2fd9c9f6ef6e7ddea6dea002f26f20cd66a7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745960"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktur von Azure-Dashboards
In diesem Dokument wird die Struktur eines Azure-Dashboards beschrieben. Dabei wird das folgende Dashboard als Beispiel verwendet:

![Beispieldashboard](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Da freigegebene [Azure-Dashboards Ressourcen sind](../azure-resource-manager/management/overview.md), kann dieses Dashboard als JSON-Code dargestellt werden.  Der folgende JSON-Code stellt das oben visualisierte Dashboard dar.

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
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
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

## <a name="common-resource-properties"></a>Allgemeine Ressourceneigenschaften

Wir unterteilen die relevanten Abschnitte der JSON-Darstellung.  Die Eigenschaften der obersten Ebene, die __id__-, __name__-, __type__-, __location__- und __tags__-Eigenschaften sind für alle Azure-Ressourcentypen freigegeben. Das heißt, sie haben wenig zu tun mit dem Inhalt des Dashboards.

### <a name="the-id-property"></a>Die ID-Eigenschaft.

Die Azure-Ressourcen-ID; unterliegt den [Namenskonventionen für Azure-Ressourcen](/azure/architecture/best-practices/resource-naming). Wenn im Portal ein Dashboard erstellt wird, wird in der Regel eine ID in Form einer GUID erstellt. Sie können aber jeden gültigen Namen verwenden, wenn Sie Dashboards programmgesteuert erstellen. 

### <a name="the-name-property"></a>Die „name“-Eigenschaft
Die name-Eigenschaft ist das Segment der Ressourcen-ID, das keine Informationen zum Abonnement, Ressourcentyp oder der Ressourcengruppe enthält. Im Wesentlichen handelt es sich um das letzte Segment der Ressourcen-ID.

### <a name="the-type-property"></a>Die „type“-Eigenschaft
Alle Dashboards weisen den Typ __Microsoft.Portal/dashboards__ auf.

### <a name="the-location-property"></a>Die „location“-Eigenschaft
Im Gegensatz zu anderen Ressourcen verfügen Dashboards über keine Laufzeitkomponente.  Für Dashboards gibt die „location“-Eigenschaft den primären geografischen Standort an, an dem die JSON-Darstellung des Dashboards gespeichert wird. Der Wert muss einer der Standortcodes sein, die mit der [Standort-API für die Abonnementressource](/rest/api/resources/subscriptions) abgerufen werden können.

### <a name="the-tags-property"></a>Die „tags“-Eigenschaft
Tags sind eine gebräuchliche Funktion von Azure-Ressourcen, mit denen Sie die Ressource nach beliebigen Name-Wert-Paaren organisieren können. Für Dashboards ist ein spezielles Tag mit dem Namen __hidden-title__ vorhanden. Wenn diese Eigenschaft in Ihrem Dashboard ausgefüllt ist, wird sie als Anzeigename des Dashboards im Portal verwendet. Azure-Ressourcen-IDs können nicht umbenannt werden, Tags dagegen schon. Dieses Tag ermöglicht einen Anzeigenamen für das Dashboard, der umbenannt werden kann.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Das „properties“-Objekt
Das „properties“-Objekt enthält zwei Eigenschaften: __lenses__ und __metadata__. Die __lenses__-Eigenschaft enthält Informationen zu den Kacheln im Dashboard.  Die __metadata__-Eigenschaft ist für mögliche künftige Funktionen vorhanden.

### <a name="the-lenses-property"></a>Die „lenses“-Eigenschaft
Die __lenses__-Eigenschaft enthält das Dashboard. Beachten Sie, dass das „lenses“-Objekt in diesem Beispiel eine einzige Eigenschaft mit dem Namen „0“ enthält. Fokusbereiche (lenses) stellen ein Gruppierungskonzept dar, das derzeit in Dashboards nicht implementiert ist. Daher weisen all Ihre Dashboards aktuell diese einzige Eigenschaft mit dem Namen „0“ für das „lens“-Objekt auf.

### <a name="the-lens-object"></a>Das „lens“-Objekt
Das Objekt unterhalb von „0“ enthält zwei Eigenschaften: __order__ und __parts__.  In der aktuellen Version von Dashboards ist __order__ immer auf „0“ festgelegt. Die __parts__-Eigenschaft enthält ein Objekt, das die einzelnen Elemente (auch als Kacheln bezeichnet) im Dashboard definiert.

Das __parts__-Objekt enthält eine Eigenschaft für jeden Teil. Der Name der Eigenschaft ist dabei eine Zahl. Diese Zahl ist nicht wichtig. 

### <a name="the-part-object"></a>Das „part“-Objekt
Jedes einzelne „part“-Objekt verfügt über ein __position__- und __metadata__-Objekt.

### <a name="the-position-object"></a>Das „position“-Objekt
Die __position__-Eigenschaft enthält die Informationen zu Größe und Position des Teils und wird mit __x__, __y__, __rowSpan__ und __colSpan__ angegeben. Die Werte beziehen sich auf Rastereinheiten. Diese Rastereinheiten sind sichtbar, wenn sich das Dashboard wie hier gezeigt im Anpassungsmodus befindet. Wenn eine Kachel eine Breite von zwei Rastereinheiten und eine Höhe von einer Rastereinheit haben und sich oben links im Dashboard befinden soll, sieht das „position“-Objekt wie folgt aus:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![Der Screenshot zeigt eine Nahaufnahme des Rasters, wobei eine quadratische Rastereinheit hervorgehoben ist.](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Das „metadata“-Objekt
Jeder Teil verfügt über eine „metadata“-Eigenschaft. Ein Objekt weist nur eine erforderliche Eigenschaft mit dem Namen __type__ auf. Mit dieser Zeichenfolge wird im Portal festgelegt, welche Kachel angezeigt werden soll. Im Beispieldashboard werden folgende Typen von Kacheln verwendet:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`: Wird zum Anzeigen von Überwachungsmetriken verwendet
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`: Wird zum Anzeigen von Text oder Bildern mit einfachen Formatierungen für Listen, Links usw. angezeigt
1. `Extension[azure]/HubsExtension/PartType/VideoPart`: Wird zum Anzeigen von Videos von YouTube und Channel9 sowie anderen Arten von Videos angezeigt, die in einem HTML-Videotag ausgeführt werden können
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`: Wird zum Anzeigen des Namens und Status eines virtuellen Azure-Computers verwendet

Jeder Teiltyp verfügt über eine eigene Konfiguration. Mögliche Konfigurationseigenschaften sind __inputs__, __settings__ und __asset__. 

### <a name="the-inputs-object"></a>Das „inputs“-Objekt
Das „inputs“-Objekt enthält im Allgemeinen Informationen, anhand derer eine Kachel an eine Ressourceninstanz gebunden wird.  Das Element für den virtuellen Computer im Beispieldashboard enthält eine einzelne Eingabe, bei der die Bindung mithilfe der Azure-Ressourcen-ID angegeben wird.  Dieses Ressourcen-ID-Format ist für alle Azure-Ressourcen identisch.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Der Teil für das Metrikdiagramm enthält eine einzelne Eingabe, mit der die Ressource für die Bindung sowie Informationen zu den angezeigten Metriken angegeben werden. Es folgt die Eingabe für die Kachel, auf der die Metriken „Network In“ und „Network Out“ angezeigt werden.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
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
]

```

### <a name="the-settings-object"></a>Das „settings“-Objekt
Das „settings“-Objekt enthält die konfigurierbaren Elemente eines Teils.  Im Beispieldashboard werden im Markdownteil Einstellungen zum Speichern des benutzerdefinierten Markdowninhalts sowie ein konfigurierbarer Titel und Untertitel verwendet.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

In ähnlicher Weise verfügt die Videokachel über spezifische Einstellungen, die einen Zeiger auf das wiederzugebende Video, eine Einstellung für die automatische Wiedergabe und optionale Informationen zum Titel enthalten.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Das „asset“-Objekt
Für Kacheln, die an verwaltbare Portalobjekte erster Klasse (sogenannte Assets) gebunden sind, wird diese Beziehung über das „asset“-Objekt angegeben.  Im Beispieldashboard enthält die Kachel für den virtuellen Computer die folgende Beschreibung für „asset“.  Die __idInputName__-Eigenschaft gibt im Portal an, dass die ID-Eingabe den eindeutigen Bezeichner für das Objekt enthält, in diesem Fall die Ressourcen-ID. Für die meisten Azure-Ressourcentypen sind im Portal Assets definiert.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`