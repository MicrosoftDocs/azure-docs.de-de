---
title: Hinzufügen der Abfrageaktion in Logik-Apps | Microsoft Docs
description: Übersicht über die Abfrageaktion zum Ausführen von Aktionen wie „Array filtern“.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 2a82afe396039857e5b9ad6b8a6d0e710573037f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60538242"
---
# <a name="get-started-with-the-query-action"></a>Erste Schritte mit der Abfrageaktion
Mit der Abfrageaktion können Sie Batches und Arrays zum Ausführen von Workflows nutzen:

* Erstellen Sie eine Aufgabe für alle Datensätze mit hoher Priorität aus einer Datenbank.
* Speichern Sie alle PDF-Anlagen für E-Mails in einem Azure-Blob.

Wenn Sie die Abfrageaktion in einer Logik-App verwenden möchten, müssen Sie zunächst eine [Logik-App erstellen](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Verwenden der Abfrageaktion
Eine Aktion ist ein Vorgang, der durch den in einer Logik-App definierten Workflow ausgeführt wird. 
Weitere Informationen zu Aktionen finden Sie [hier](../connectors/apis-list.md).  

Für die Abfrageaktion ist derzeit ein Vorgang („Array filtern“) im Designer verfügbar. Dadurch können Sie ein Array abfragen und einen Satz gefilterter Ergebnisse zurückgeben.

Im Folgenden wird erläutert, wie Sie die Aktion in einer Logik-App hinzufügen können:

1. Wählen Sie die Schaltfläche **Neuer Schritt** aus.
2. Wählen Sie **Aktion hinzufügen**aus.
3. Geben Sie in das Aktionssuchfeld **Filter** ein, um die Aktion **Array filtern** anzuzeigen.
   
    ![Auswählen der Abfrageaktion](./media/connectors-native-query/using-action-1.png)
4. Wählen Sie ein zu filterndes Array aus. (Das folgende Bildschirmfoto zeigt das Array mit Ergebnissen aus einer Twitter-Suche.)
5. Erstellen Sie eine für jedes Element auszuwertende Bedingung. (Im folgenden Bildschirmfoto werden Tweets von Benutzern mit mehr als 100 Followern gefiltert.)
   
    ![Ausführen der Abfrageaktion](./media/connectors-native-query/using-action-2.png)
   
    Die Aktion gibt ein neues Array aus, das nur Ergebnisse enthält, die die Filteranforderungen erfüllen.
6. Klicken Sie zum Speichern links oben auf die Symbolleiste. Dadurch wird Ihre Logik-App gespeichert und veröffentlicht (aktiviert).

\* Wenn Sie einen HTTP-Endpunkt aufrufen und eine JSON-Antwort empfangen, analysieren Sie die JSON-Antwort mit der Aktion _JSON analysieren_. Ohne diesen Schritt erkennt _Array filtern_ nur Text und nicht die Struktur der JSON-Nutzlast.

## <a name="query-action"></a>Abfrageaktion
Hier finden Sie Details zu der Aktion, die dieser Connector unterstützt. Der Connector verfügt über eine mögliche Aktion.

| Aktion | BESCHREIBUNG |
| --- | --- |
| Array filtern |Auswerten einer Bedingung für jedes Element in einem Array und Zurückgeben der Ergebnisse |

## <a name="action-details"></a>Aktionsdetails
Für die Abfrageaktion steht eine mögliche Aktion zur Verfügung. Die folgenden Tabellen beschreiben die erforderlichen und optionalen Eingabefelder für die Aktion und die entsprechenden Ausgabedetails, die der Verwendung dieser Aktion zugeordnet sind.

### <a name="filter-array"></a>Array filtern
Im Folgenden werden die Eingabefelder für die Aktion angegeben, die eine ausgehende HTTP-Anforderung ausführt.
Ein * bedeutet, dass es sich um ein Pflichtfeld handelt.

| `Display name` | Eigenschaftenname | BESCHREIBUNG |
| --- | --- | --- |
| Von* |from |Das zu filternde Array |
| Bedingung* |Hierbei gilt: |Die für jedes Element auszuwertende Bedingung |

<br>

### <a name="output-details"></a>Ausgabedetails
Im Folgenden werden die Ausgabedetails für die HTTP-Antwort angegeben.

| Eigenschaftenname | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| Array gefiltert |Array |Array, das ein Objekt für jedes gefilterte Ergebnis enthält |

## <a name="next-steps"></a>Nächste Schritte
Testen Sie nun die Plattform, und [erstellen Sie eine Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Machen Sie sich ggf. anhand unserer [API-Liste](apis-list.md) mit den anderen verfügbaren Connectors für Logik-Apps vertraut.

