---
title: Referenz zur Azure Blockchain Workbench-Konfiguration
description: Übersicht über die Anwendungskonfiguration in Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4d29d8e86a30f105c4aa50ec9615f8165fa238d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578979"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referenz zur Azure Blockchain Workbench-Konfiguration

Azure Blockchain Workbench-Anwendungen sind Mehrparteienworkflows, die durch Konfigurationsmetadaten und intelligenten Vertragscode definiert sind. Konfigurationsmetadaten definieren die allgemeinen Workflows und das Interaktionsmodell der Blockchainanwendung. Intelligente Verträge stellen die Geschäftslogik der Blockchainanwendung dar. Mithilfe von Konfigurationsdaten und Smart Contract-Code generiert Workbench Benutzeroberflächen von Blockchainanwendungen.

In den Konfigurationsmetadaten sind für jede Blockchainanwendung die folgenden Informationen festgelegt:

* Name und Beschreibung der Blockchainanwendung
* Eindeutige Rollen für Benutzer, die innerhalb der Blockchainanwendung agieren oder teilnehmen können.
* Mindestens ein Workflow. Jeder Workflow fungiert als Zustandsautomat, um den Ablauf der Geschäftslogik zu steuern. Workflows können unabhängig sein oder miteinander interagieren.

Jeder definierte Workflow legt Folgendes fest:

* Name und Beschreibung des Workflows
* Zustände des Workflows.  Jeder Zustand ist eine Stufe in der Ablaufsteuerung der Geschäftslogik. 
* Aktionen für den Übergang zum nächsten Zustand
* Benutzerrollen, die jede Aktion initiieren dürfen.
* Intelligente Verträge, die die Geschäftslogik in Codedateien darstellen.

## <a name="application"></a>Anwendung

Eine Blockchainanwendung enthält Konfigurationsmetadaten, Workflows und Benutzerrollen, die innerhalb der Anwendung agieren oder teilnehmen können.

| Feld | BESCHREIBUNG | Erforderlich |
|-------|-------------|:--------:|
| ApplicationName | Eindeutiger Anwendungsname. Der entsprechende intelligente Vertrag muss den gleichen **Anwendungsnamen** für die jeweilige Vertragsklasse verwenden.  | Ja |
| DisplayName | Der Anzeigename der Anwendung | Ja |
| BESCHREIBUNG | Beschreibung der Anwendung | Nein |
| ApplicationRoles | Sammlung von [Anwendungsrollen](#application-roles). Benutzerrollen, die innerhalb der Anwendung agieren oder teilnehmen können.  | Ja |
| Workflows | Sammlung von [Workflows](#workflows). Jeder Workflow fungiert als Zustandsautomat, um den Ablauf der Geschäftslogik zu steuern. | Ja |

Ein Beispiel finden Sie unter [Beispielkonfigurationsdatei](#configuration-file-example).

## <a name="workflows"></a>Workflows

Die Geschäftslogik einer Anwendung kann als Zustandsautomat modelliert werden, bei der eine Aktion bewirkt, dass während des Ablaufs der Geschäftslogik von einem Zustand in einen anderen gewechselt wird. Ein Workflow ist eine Sammlung solcher Zustände und Aktionen. Jeder Workflow besteht aus mindestens einem intelligenten Vertrag, der die Geschäftslogik in Codedateien darstellt. Bei einem ausführbaren Vertrag handelt es sich um eine Instanz eines Workflows.

| Feld | BESCHREIBUNG | Erforderlich | Max. Länge |
|-------|-------------|:--------:|-----------:|
| NAME | Eindeutiger Workflowname. Der entsprechende intelligente Vertrag muss den gleichen **Namen** für die jeweilige Vertragsklasse verwenden. | Ja | 50 |
| DisplayName | Der Anzeigename des Workflows | Ja | 255 |
| BESCHREIBUNG | Die Beschreibung des Workflows | Nein | 255 |
| Initiators | Sammlung von [Anwendungsrollen](#application-roles). Rollen, die Benutzern zugeordnet sind, die zum Erstellen von Verträgen im Workflow berechtigt sind. | Ja | |
| StartState | Der Name des Anfangszustands des Workflows | Ja | |
| Eigenschaften | Sammlung von [Bezeichnern](#identifiers). Stellt Daten dar, die Off-Chain gelesen oder in einem Tool zum Erstellen von Benutzeroberflächen visualisiert werden können. | Ja | |
| Konstruktor | Definiert Eingabeparameter für die Erstellung einer Instanz des Workflows. | Ja | |
| Functions | Eine Sammlung von [Funktionen](#functions), die im Workflow ausgeführt werden können. | Ja | |
| Zustände | Eine Sammlung von [Zuständen](#states) des Workflows. | Ja | |

Ein Beispiel finden Sie unter [Beispielkonfigurationsdatei](#configuration-file-example).

## <a name="type"></a>Type

Unterstützte Datentypen:

| Type | BESCHREIBUNG |
|-------|-------------|
| address  | Blockchainadresstyp, z. B. *Verträge* oder *Benutzer*. |
| Array    | Array mit einer Ebene vom Typ „Int“, „bool“, „money“ oder „time“. Arrays können statisch oder dynamisch sein. Verwenden Sie **ElementType**, um den Datentyp der Elemente im Array anzugeben. Siehe hierzu die [Beispielkonfiguration](#example-configuration-of-type-array). |
| bool     | Boolean-Datentyp |
| contract | Adresse vom Typ „Vertrag“ |
| enum     | Aufgelisteter Satz von benannten Werten. Wenn Sie den enum-Typ verwenden, geben Sie auch eine Liste von „EnumValues“ an. Jeder Wert darf maximal 255 Zeichen lang sein. Gültige Zeichen für den Wert sind Groß-/Kleinbuchstaben (A-Z, a-z) und Zahlen (0-9). Informationen hierzu finden Sie unter [Beispielkonfiguration und Verwendung in Solidity](#example-configuration-of-type-enum). |
| int      | Integer-Datentyp |
| money    | Money-Datentyp |
| state    | Workflowstatus |
| Zeichenfolge  | String-Datentyp Maximal 4000 Zeichen Siehe hierzu die [Beispielkonfiguration](#example-configuration-of-type-string). |
| user     | Adresse vom Typ „Benutzer“ |
| time     | Time-Datentyp |
|`[ Application Role Name ]`| Ein beliebiger in der Anwendungsrolle festgelegter Name. Legt fest, dass Benutzer zu diesem Rollentyp gehören müssen. |

### <a name="example-configuration-of-type-array"></a>Beispielkonfiguration vom Typ „Array“

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Verwenden einer Eigenschaft vom Typ „Array“

Wenn Sie eine Eigenschaft vom Typ „Array“ in der Konfiguration definieren, müssen Sie eine explizite Get-Funktion angeben, um die öffentliche Eigenschaft des Arraytyps in Solidity zurückzugeben. Beispiel:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Beispielkonfiguration vom Typ Zeichenfolge

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Beispielkonfiguration vom Typ Enumeration

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Verwenden des Enumerationstyps in Solidity

Sobald in einer Konfiguration eine Enumeration definiert ist, können Sie Enumerationstypen in Solidity verwenden. Beispielsweise können Sie eine Enumeration namens „PropertyTypeEnum“ aufrufen.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Die Liste der Zeichenfolgen muss zwischen der Konfiguration und dem intelligenten Vertrag übereinstimmen, um gültige und konsistente Deklarationen in Blockchain Workbench zu erhalten.

Beispiel für Zuweisung:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Beispiel für Funktionsparameter: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Konstruktor

Definiert Eingabeparameter für eine Instanz eines Workflows.

| Feld | BESCHREIBUNG | Erforderlich |
|-------|-------------|:--------:|
| Parameter | Sammlung von [Bezeichnern](#identifiers), die zum Initiieren eines intelligenten Vertrags erforderlich sind. | Ja |

### <a name="constructor-example"></a>Konstruktorbeispiel

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

Definiert Funktionen, die im Workflow ausgeführt werden können.

| Feld | BESCHREIBUNG | Erforderlich | Max. Länge |
|-------|-------------|:--------:|-----------:|
| NAME | Der eindeutige Name der Funktion. Der entsprechende intelligente Vertrag muss den gleichen **Namen** für die jeweilige Funktion verwenden. | Ja | 50 |
| DisplayName | Der Anzeigename der Funktion | Ja | 255 |
| BESCHREIBUNG | Beschreibung der Funktion | Nein | 255 |
| Parameter | Sammlung von [Bezeichnern](#identifiers), die den Parametern der Funktion entsprechen. | Ja | |

### <a name="functions-example"></a>Funktionsbeispiel

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Zustände

Eine Sammlung von eindeutigen Zuständen innerhalb eines Workflows. Jeder Zustand erfasst einen Schritt in der Ablaufsteuerung der Geschäftslogik. 

| Feld | BESCHREIBUNG | Erforderlich | Max. Länge |
|-------|-------------|:--------:|-----------:|
| NAME | Eindeutiger Name des Zustands. Der entsprechende intelligente Vertrag muss den gleichen **Namen** für den jeweiligen Zustand verwenden. | Ja | 50 |
| DisplayName | Der Anzeigename des Zustands | Ja | 255 |
| BESCHREIBUNG | Beschreibung des Zustands | Nein | 255 |
| PercentComplete | Ein ganzzahliger Wert in der Blockchain Workbench-Benutzeroberfläche, der den Fortschritt der Ablaufsteuerung innerhalb der Geschäftslogik anzeigt. | Ja | |
| Style | Visueller Hinweis, ob es sich um einen erfolgreichen oder fehlerhaften Zustand handelt. Es gibt zwei gültige Werte: `Success` oder `Failure`. | Ja | |
| Transitions | Sammlung von verfügbaren [Übergängen](#transitions) von dem aktuellen Zustand in die nächsten Zustände. | Nein | |

### <a name="states-example"></a>Beispiel zu Zuständen (States)

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transitions

Verfügbare Aktionen zum Erreichen des nächsten Zustands. Eine oder mehrere Benutzerrollen können in jedem Zustand eine Aktion ausführen, wobei eine Aktion einen Zustand in einen anderen Zustand im Workflow überführen kann. 

| Feld | BESCHREIBUNG | Erforderlich |
|-------|-------------|:--------:|
| AllowedRoles | Liste der Anwendungsrollen, die einen Übergang initiieren dürfen. Alle Benutzer der angegebenen Rolle können die Aktion möglicherweise ausführen. | Nein |
| AllowedInstanceRoles | Liste der Benutzerrollen, die am intelligenten Vertrag teilnehmen oder darin festgelegt sind, um den Übergang zu initiieren. Instanzrollen werden in den **Eigenschaften** innerhalb von Workflows definiert. AllowedInstanceRoles stellt einen Benutzer dar, der bei einer Instanz eines Smart Contract involviert ist. Über AllowedInstanceRoles können Sie die Durchführung einer Aktion für eine Benutzerrolle in einer Vertragsinstanz beschränken.  Beispielsweise empfiehlt es sich, nur dem Benutzer, der den Vertrag erstellt hat (InstanceOwner), die Kündigung eines Vertrags zu ermöglichen, nicht allen Benutzern mit dem Rollentyp (Owner). Hierfür müssen Sie die Rolle in AllowedRoles angegeben haben. | Nein |
| DisplayName | Der Anzeigename des Übergangs | Ja |
| BESCHREIBUNG | Die Beschreibung des Übergangs | Nein |
| Funktion | Der Name der Funktion zum Initiieren des Übergangs | Ja |
| NextStates | Eine Sammlung möglicher nächster Zustände nach einem erfolgreichen Übergang | Ja |

### <a name="transitions-example"></a>Beispiel zu Übergängen (Transitions)

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Anwendungsrollen

Anwendungsrollen definieren eine Reihe von Rollen, die Benutzern zugewiesen werden können, die innerhalb der Anwendung agieren oder teilnehmen möchten. Anwendungsrollen können verwendet werden, um Aktionen und Teilnahme innerhalb der Blockchainanwendung und entsprechender Workflows einzuschränken. 

| Feld | BESCHREIBUNG | Erforderlich | Max. Länge |
|-------|-------------|:--------:|-----------:|
| NAME | Der eindeutige Name der Anwendungsrolle. Der entsprechende intelligente Vertrag muss den gleichen **Namen** für die jeweilige Rolle verwenden. Basistypnamen sind reserviert. Einer Anwendungsrolle kann nicht der Name eines [Typs](#type) gegeben werden.| Ja | 50 |
| BESCHREIBUNG | Die Beschreibung der Anwendungsrolle | Nein | 255 |

### <a name="application-roles-example"></a>Beispiel zu Anwendungsrollen (ApplicationRoles)

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Bezeichner

Bezeichner stellen eine Sammlung von Informationen zur Beschreibung von Workfloweigenschaften, Konstruktor und Funktionsparametern dar. 

| Feld | BESCHREIBUNG | Erforderlich | Max. Länge |
|-------|-------------|:--------:|-----------:|
| NAME | Der eindeutige Name der Eigenschaft bzw. des Parameters. Der entsprechende intelligente Vertrag muss den gleichen **Namen** für die jeweilige Eigenschaft bzw. den jeweiligen Parameter verwenden. | Ja | 50 |
| DisplayName | Der Anzeigename der Eigenschaft bzw. des Parameters | Ja | 255 |
| BESCHREIBUNG | Die Beschreibung der Eigenschaft bzw. des Parameters | Nein | 255 |

### <a name="identifiers-example"></a>Beispiel zu Bezeichnern

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Beispielkonfigurationsdatei

Die Güterübertragung ist ein Smart Contract-Szenario für den Kauf und Verkauf wertvoller Güter, die einen Prüfer und einen Gutachter erfordern. Verkäufer können ihre Güter auflisten, indem sie ein Smart Contract für die Güterübertragung instanziieren. Käufer können Angebote unterbreiten, indem sie eine Aktion für den Smart Contract durchführen und andere Parteien Aktionen zur Überprüfung oder Begutachtung der Ressource durchführen. Sobald das Gut als überprüft und begutachtet gekennzeichnet wurde, bestätigen Käufer und Verkäufer erneut den Verkauf, bevor der Vertrag auf abgeschlossen gesetzt wird. Jederzeit während des Prozesses können alle Teilnehmer Einblick in den Status des Vertrags erhalten, während dieser aktualisiert wird. 

Weitere Informationen, einschließlich der Codedateien, finden Sie unter [Beispiel für die Güterübertragung für Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer).

Die folgende Konfigurationsdatei dient als Beispiel für die Güterübertragung:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenz zur REST-API von Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

