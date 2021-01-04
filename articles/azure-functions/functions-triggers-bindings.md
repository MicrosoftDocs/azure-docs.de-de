---
title: Trigger und Bindungen in Azure Functions
description: Erfahren Sie, wie Sie Trigger und Bindungen verwenden, um eine Verbindung Ihrer Azure Functions-Funktion mit Onlineereignissen und cloudbasierten Diensten herzustellen.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: aa0d78d52ec13c91b82e6a8d10720269076f59a1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353543"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Konzepte für Azure Functions-Trigger und -Bindungen

In diesem Artikel lernen Sie die allgemeinen Konzepte rund um Funktionstrigger und Bindungen kennen.

Trigger sind das, was die Ausführung einer Funktion bewirkt. Ein Trigger definiert, wie eine Funktion aufgerufen wird, und eine Funktion muss genau einen Trigger aufweisen. Trigger weisen zugeordnete Daten auf, die oftmals als Nutzlast der Funktion übergeben werden. 

Die Bindung an eine Funktion stellt eine Möglichkeit dar, eine andere Ressource deklarativ mit der Funktion zu verbinden; Bindungen können als *Eingabebindungen*, *Ausgabebindungen* oder beides verbunden werden. Daten von Bindungen werden der Funktion als Parameter bereitgestellt.

Sie können verschiedene Bindungen nach Belieben kombinieren, wie es Ihren Anforderungen entspricht. Bindungen sind optional, und eine Funktion kann mehrere Eingabe- und/oder Ausgabebindungen haben.

Mit Triggern und Bindungen können Sie den hartcodieren Zugriff auf andere Dienste vermeiden. Ihre Funktion empfängt Daten (z.B. den Inhalt einer Warteschlangennachricht) in Funktionsparametern. Sie senden Daten (z.B., um eine Warteschlangennachricht zu erstellen), indem Sie den Rückgabewert der Funktion verwenden. 

Betrachten Sie die folgenden Beispiele, wie Sie verschiedene Funktionen implementieren könnten.

| Beispielszenario | Trigger | Eingabebindung | Ausgabebindung |
|-------------|---------|---------------|----------------|
| Eine neue Warteschlangennachricht trifft ein, die eine Funktion ausführt, die das Schreiben in eine andere Warteschlange bewirkt. | Warteschlange<sup>*</sup> | *None* | Warteschlange<sup>*</sup> |
|Ein geplanter Auftrag liest den Inhalt von Blob-Speicher und erstellt ein neues Cosmos DB-Dokument. | Zeitgeber | Blob Storage | Cosmos DB |
|Das Event Grid wird verwendet, um ein Image aus Blob-Speicher und ein Dokument aus Cosmos DB zu lesen, um eine E-Mail zu senden. | Event Grid | BLOB Storage und Cosmos DB | SendGrid |
| Ein Webhook, der Microsoft Graph verwendet, um ein Excel-Arbeitsblatt zu aktualisieren. | HTTP | *None* | Microsoft Graph |

<sup>\*</sup> Stellt verschiedene Warteschlangen dar

Diese Beispiele sollen nicht erschöpfend sein, werden aber bereitgestellt, um die gemeinsame Verwendung von Triggern und Bindungen zu veranschaulichen.

###  <a name="trigger-and-binding-definitions"></a>Definitionen für Trigger und Bindungen

Trigger und Bindungen werden je nach Entwicklungsansatz unterschiedlich definiert.

| Plattform | Trigger und Bindungen werden in dieser Weise konfiguriert... |
|-------------|--------------------------------------------|
| C#-Klassenbibliothek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Versehen von Methoden und Parametern mit C#-Attributen |
| Alle anderen (einschließlich Azure-Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Aktualisieren von [function.json](./functions-reference.md) ([Schema](http://json.schemastore.org/function)) |

Das Portal stellt eine Benutzeroberfläche für diese Konfiguration zur Verfügung, Sie können die Datei jedoch direkt bearbeiten, indem Sie den **erweiterten Editor** öffnen, der auf der Registerkarte **Integrieren** Ihrer Funktion zur Verfügung steht.

In .NET definiert der Parametertyp den Datentyp für Eingabedaten. Verwenden Sie zum Beispiel `string`, um eine Bindung mit dem Text eines Warteschlangentriggers zu erstellen – einem Bytearray zum Lesen im Binärformat und einem benutzerdefinierten Typ zum Deserialisieren in einem Objekt.

Für dynamisch typisierte Sprachen wie JavaScript verwenden Sie die Eigenschaft `dataType` in der *function.json*-Datei. Um z.B. den Inhalt einer HTTP-Anforderung im Binärformat zu lesen, legen Sie `dataType` auf `binary` fest:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andere Optionen für `dataType` sind `stream` und `string`.

## <a name="binding-direction"></a>Bindungsrichtung

Alle Trigger und Bindungen enthalten eine `direction`-Eigenschaft in der Datei [function.json](./functions-reference.md):

- Für Trigger ist die Richtung immer gleich `in`.
- Für Eingabe- und Ausgabebindungen werden `in` und `out` verwendet.
- Einige Bindungen unterstützen die spezielle Richtung `inout`. Wenn Sie `inout` verwenden, ist nur **Erweiterter Editor** auf der Registerkarte **Integrieren** im Portal verfügbar.

Wenn Sie zum Konfigurieren von Triggern und Bindungen [Attribute in einer Klassenbibliothek](functions-dotnet-class-library.md) verwenden, wird die Richtung in einem Attributkonstruktor angegeben oder aus dem Parametertyp abgeleitet.

## <a name="add-bindings-to-a-function"></a>Hinzufügen von Bindungen zu einer Funktion

Sie können Ihre Funktion mit anderen Diensten verbinden, indem Sie Eingabe- oder Ausgabebindungen verwenden. Fügen Sie eine Bindung hinzu, indem Sie Ihrer Funktion ihre spezifischen Definitionen hinzufügen. Informationen zur Vorgehensweise finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Unterstützte Bindungen

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Informationen darüber, welche Bindungen sich in der Vorschauversion befinden oder für die Produktion zugelassen sind, finden Sie unter [Unterstützte Sprachen](supported-languages.md).

## <a name="bindings-code-examples"></a>Codebeispiele für Bindungen

Verwenden Sie die folgende Tabelle, um Beispiele für bestimmte Bindungstypen zu finden, die Ihnen zeigen, wie Sie mit Bindungen in Ihren Funktionen arbeiten. Wählen Sie zunächst die Registerkarte „Sprache“ aus, die Ihrem Projekt entspricht. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Benutzerdefinierte Bindungen

Sie können benutzerdefinierte Eingabe- und Ausgabebindungen erstellen. Bindungen müssen in .NET erstellt werden, können aber von jeder unterstützten Sprache genutzt werden. Weitere Informationen zum Erstellen benutzerdefinierter Bindungen finden Sie unter [Creating custom input and output bindings](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings) (Erstellen benutzerdefinierter Eingabe- und Ausgabebindungen).

## <a name="resources"></a>Ressourcen
- [Bindungsausdrücke und Muster](./functions-bindings-expressions-patterns.md)
- [Verwenden des Rückgabewerts einer Azure-Funktion](./functions-bindings-return-value.md)
- [Vorgehensweise: Registrieren eines Bindungsausdrucks](./functions-bindings-register.md)
- Test:
  - [Strategien zum Testen Ihres Codes in Azure Functions](functions-test-a-function.md)
  - [Manuelles Ausführen einer Funktion ohne HTTP-Trigger](functions-manually-run-non-http.md)
- [Beheben von Bindungsfehlern](./functions-bindings-errors.md)

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Registrieren von Bindungserweiterungen von Azure Functions](./functions-bindings-register.md)
