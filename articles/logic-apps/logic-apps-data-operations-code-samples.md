---
title: Beispiele für Datenvorgänge – Azure Logic Apps | Microsoft-Dokumentation
description: Codebeispiele für Aktionsdefinitionen von Datenvorgängen in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8de70e8df50b79f27c19c74307d9321ff8cd1eb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128548"
---
# <a name="data-operation-code-samples-for-azure-logic-apps"></a>Codebeispiele für Datenvorgänge in Azure Logic Apps

Hier sind die Codebeispiele für die Aktionsdefinitionen von Datenvorgängen des Artikels [Durchführen von Datenvorgängen](../logic-apps/logic-apps-perform-data-operations.md) angegeben. Sie können diese Beispiele verwenden, um sie für die zugrunde liegende Workflowdefinition Ihrer eigenen Logik-App, Ihr Azure-Abonnement und die API-Verbindungen auszuprobieren. Kopieren Sie diese Aktionsdefinitionen einfach, und fügen Sie sie in den Codeansicht-Editor für die Workflowdefinition Ihrer Logik-App ein. Ändern Sie die Definitionen dann gemäß Ihrem spezifischen Workflow. 

Diese Aktionsdefinitionen werden basierend auf den JSON-Standards (JavaScript Object Notation) in alphabetischer Reihenfolge angezeigt. Im Logik-App-Designer wird jede Definition für Ihren Workflow aber in der richtigen Abfolge angezeigt, da mit der `runAfter`-Eigenschaft einer Aktionsdefinition jeweils die Ausführungsreihenfolge angegeben wird. 

<a name="compose-action-example"></a>

## <a name="compose"></a>Compose

Für das [Beispiel zur Aktion **Compose**](../logic-apps/logic-apps-perform-data-operations.md#compose-action) können Sie die folgenden Aktionsdefinitionen verwenden:

```json
"actions": {
  "Compose": {
    "type": "Compose",
    "inputs": {
      "age": "@variables('ageVar')",
      "fullName": "@{variables('lastNameVar')}, @{variables('firstNameVar')}"
    },
    "runAfter": {
      "Initialize_variable_-_ageVar": [
          "Succeeded"
      ]
    }
  },
  "Initialize_variable_-_ageVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "ageVar",
          "type": "Integer",
          "value": 35
        }
      ]
    },
    "runAfter": {
      "Initialize_variable_-_lastNameVar": [
        "Succeeded"
      ]
    }
  },
  "Initialize_variable_-_firstNameVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "firstNameVar",
          "type": "String",
          "value": "Sophie "
        }
      ]
    },
    "runAfter": {}
  },
  "Initialize_variable_-_lastNameVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "lastNameVar",
          "type": "String",
          "value": "Owen"
        }
      ]
    },
    "runAfter": {
      "Initialize_variable_-_firstNameVar": [
        "Succeeded"
      ]
    }
  }
},
```

<a name="create-csv-table-action-example"></a>

## <a name="create-csv-table"></a>Create CSV table

Für das [Beispiel zur Aktion **Create CSV table**](../logic-apps/logic-apps-perform-data-operations.md#create-csv-table-action) können Sie die folgenden Aktionsdefinitionen verwenden:

```json
"actions": {
   "Create_CSV_table": {
      "type": "Table",     
      "inputs": {
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      },
      "runAfter": {
         "Initialize_variable_-_JSON_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_JSON_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myJSONArray",
               "type": "Array",
                  "value": [
                     {
                        "Description": "Apples",
                        "Product_ID": 1
                     },
                     {
                        "Description": "Oranges",
                        "Product_ID": 2
                     }
                  ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="create-html-table-action-example"></a>

## <a name="create-html-table"></a>Create HTML table

Für das [Beispiel zur Aktion **Create HTML table**](../logic-apps/logic-apps-perform-data-operations.md#create-html-table-action) können Sie die folgenden Aktionsdefinitionen verwenden:

```json
"actions": {
   "Create_HTML_table": {
      "type": "Table",     
      "inputs": {
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      },
      "runAfter": {
         "Initialize_variable_-_JSON_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_JSON_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myJSONArray",
               "type": "Array",
                  "value": [
                     {
                        "Description": "Apples",
                        "Product_ID": 1
                     },
                     {
                        "Description": "Oranges",
                        "Product_ID": 2
                     }
                  ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="filter-array-action-example"></a>

## <a name="filter-array"></a>Array filtern

Für das [Beispiel zur Aktion **Filter array**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) können Sie die folgenden Aktionsdefinitionen verwenden:

```json
"actions": {
   "Filter_array": {
      "type": "Query",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "where": "@greater(item(), 1)"
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="join-action-example"></a>

## <a name="join"></a>Join

Für das [Beispiel zur Aktion **Join**](../logic-apps/logic-apps-perform-data-operations.md#join-action) können Sie die folgenden Aktionsdefinitionen verwenden:

```json
"actions": {
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   },
   "Join": {
      "type": "Join",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "joinWith": ":"
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
             "Succeeded"
         ]
      }
   }
},
```

<a name="parse-json-action-example"></a>

## <a name="parse-json"></a>JSON-Analyse

Für das [Beispiel zur Aktion **Parse JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) können Sie die folgenden Aktionsdefinitionen verwenden:

```json
"actions": {
   "Initialize_variable_-_JSON_object": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [
            {
               "name": "myJSONObject",
               "type": "Object",
               "value": {
                  "Member": {
                     "Email": "Sophie.Owen@contoso.com",
                     "FirstName": "Sophie",
                     "LastName": "Owen"
                  }
               }
            }
         ]
      },
      "runAfter": {}
   },
   "Parse_JSON": {
      "type": "ParseJson",
      "inputs": {
         "content": "@variables('myJSONObject')",
         "schema": {
            "type": "object",
            "properties": {
               "Member": {
                  "type": "object",
                  "properties": {
                     "Email": {
                        "type": "string"
                     },
                     "FirstName": {
                        "type": "string"
                     },
                     "LastName": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      },
      "runAfter": {
         "Initialize_variable_-_JSON_object": [
            "Succeeded"
         ]
      }
},
```

<a name="select-action-example"></a>

## <a name="select"></a>Select

Für das [Beispiel zur Aktion **Select**](../logic-apps/logic-apps-perform-data-operations.md#select-action) können Sie die folgenden Aktionsdefinitionen verwenden:

```json
"actions": {
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   },
   "Select": {
      "type": "Select",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "select": {
            "Product_ID": "@item()"
         }
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
           "Succeeded"
         ]
      }
   }
},
```

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen von Datenvorgängen](../logic-apps/logic-apps-perform-data-operations.md)
