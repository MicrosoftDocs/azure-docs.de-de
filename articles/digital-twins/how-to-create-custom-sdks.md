---
title: Erstellen von benutzerdefinierten SDKs für Azure Digital Twins mit AutoRest
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie benutzerdefinierte SDKs generieren, um Azure Digital Twins mit anderen Sprachen als C# zu verwenden.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: e0c0d18dbb3596733d02430554fd40ec16180c64
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980659"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Erstellen von benutzerdefinierten SDKs für Azure Digital Twins mit AutoRest

Zurzeit sind die einzigen veröffentlichten Datenebenen-SDKs für die Interaktion mit den APIs für Azure Digital Twins für .NET (C#), JavaScript und Java bestimmt. Informationen zum diesen SDKs und die APIs im Allgemeinen finden Sie unter [*Vorgehensweise: Verwenden der Azure Digital Twins-APIs und SDKs*](how-to-use-apis-sdks.md). Wenn Sie in einer anderen Sprache arbeiten, erfahren Sie in diesem Artikel, wie Sie mithilfe von AutoRest ein eigenes Datenebenen-SDK in der Sprache Ihrer Wahl generieren.

>[!NOTE]
> Wenn Sie möchten, können Sie auch AutoRest verwenden, um ein Steuerungsebenen-SDK zu generieren. Führen Sie dazu die Schritte in diesem Artikel mithilfe der neuesten **Swagger-Datei für die Steuerungsebene** (OpenAPI) im [Swagger-Ordner der Steuerungsebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) anstelle der Datei für die Datenebene aus.

## <a name="set-up-your-machine"></a>Einrichten Ihres Computers

Zum Generieren eines SDK benötigen Sie Folgendes:
* [AutoRest](https://github.com/Azure/autorest), Version 2.0.4413 (Version 3 wird derzeit nicht unterstützt)
* [Node.js](https://nodejs.org) als erforderliche Komponente für AutoRest
* Die neueste **Azure Digital Twins-Swagger-Datei für die Datenebene** (OpenAPI) im [Swagger-Ordner auf Datenebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) und die zugehörigen Ordner mit Beispielen.  Laden Sie die Swagger-Datei *digitaltwins.json* und den zugehörigen Ordner mit Beispielen auf Ihren lokalen Computer herunter.

Sobald der Computer alle Voraussetzungen gemäß der obigen Liste erfüllt, können Sie mit AutoRest das SDK erstellen.

## <a name="create-the-sdk-with-autorest"></a>Erstellen des SDK mit AutoRest 

Wenn Sie Node.js installiert haben, können Sie diesen Befehl ausführen, um sicherzustellen, dass Sie die richtige Version von AutoRest installiert haben:
```cmd/sh
npm install -g autorest@2.0.4413
```

Führen Sie die folgenden Schritte aus, um AutoRest für die Azure Digital Twins-Swagger-Datei auszuführen:
1. Kopieren Sie die Azure Digital Twins-Swagger-Datei und den zugehörigen Ordner mit Beispielen in ein Arbeitsverzeichnis.
2. Wechseln Sie in einem Eingabeaufforderungsfenster zu diesem Arbeitsverzeichnis.
3. Führen Sie AutoRest mithilfe des folgenden Befehls aus. Ersetzen Sie den `<language>`-Platzhalter durch Ihre bevorzugte Sprache: `python`, `java`, `go` usw. (Die vollständige Liste der Optionen finden Sie in der [AutoRest-Infodatei](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Daraufhin wird ein neuer Ordner mit dem Namen *ADTApi* in Ihrem Arbeitsverzeichnis angezeigt. Die generierten SDK-Dateien verwenden den Namespace *ADTApi*. Sie verwenden diesen Namespace auch in den restlichen Verwendungsbeispielen in diesem Artikel.

AutoRest unterstützt eine breite Palette von Sprachcodegeneratoren.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Hinzufügen des SDK zu einem Visual Studio-Projekt

Sie können die von AutoRest generierten Dateien direkt in eine .NET-Projektmappe einschließen. Wahrscheinlich möchten Sie das Azure Digital Twins SDK jedoch in mehrere separate Projekte (Ihre Client-Apps, Azure Functions-Apps usw.) einbinden. Aus diesem Grund kann es hilfreich sein, ein separates Projekt (eine .NET-Klassenbibliothek) aus den generierten Dateien zu erstellen. Sie können dieses Klassenbibliotheksprojekt dann als Projektverweis in mehrere Projektmappen einbinden.

Dieser Abschnitt enthält Anweisungen zum Erstellen des SDK als Klassenbibliothek, wobei es sich um ein eigenes Projekt handelt, das in andere Projekte eingebunden werden kann. Diese Schritte basieren auf **Visual Studio** ([hier](https://visualstudio.microsoft.com/downloads/) können Sie die neueste Version installieren).

Im Folgenden werden die Schritte aufgeführt:

1. Erstellen einer neuen Visual Studio-Projektmappe für eine Klassenbibliothek
2. Verwenden von *ADTApi* als Projektnamen
3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt *ADTApi* der generierten Lösung, und wählen Sie *Hinzufügen > Vorhandenes Element*.
4. Suchen Sie den Ordner, in dem Sie das SDK generiert haben, und wählen Sie die Dateien auf der Stammebene aus.
5. Klicken Sie auf „OK“.
6. Fügen Sie dem Projekt einen Ordner hinzu (klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie *Hinzufügen > Neuer Ordner*).
7. Geben Sie dem Ordner den Namen *Models*.
8. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner *Models*, und wählen Sie *Hinzufügen > Vorhandenes Element* aus.
9. Wählen Sie die Dateien im Ordner *Models* des generierten SDK aus, und klicken Sie auf „OK“.

Um das SDK erfolgreich zu erstellen, benötigt das Projekt die folgenden Verweise:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Um diese Verweise hinzuzufügen, öffnen Sie *Tools > NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten*.

1. Vergewissern Sie sich im Bereich, dass die Registerkarte *Durchsuchen* ausgewählt ist.
2. Suchen Sie nach *Microsoft.Rest*.
3. Wählen Sie die `ClientRuntime`- und `ClientRuntime.Azure`-Pakete aus, und fügen Sie sie der Projektmappe hinzu.

Sie können nun das Projekt erstellen und als Projektverweis in eine beliebige Azure Digital Twins-Anwendung einschließen, die Sie schreiben.

## <a name="general-guidelines-for-generated-sdks"></a>Allgemeine Richtlinien für generierte SDKs

Dieser Abschnitt enthält allgemeine Informationen und Richtlinien zur Verwendung des generierten SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Synchrone und asynchrone Aufrufe

Alle SDK-Funktionen sind in synchronen und asynchronen Versionen enthalten.

### <a name="typed-and-untyped-data"></a>Typisierte und nicht typisierte Daten

REST-API-Aufrufe geben im Allgemeinen stark typisierte Objekte zurück. Da Benutzer jedoch mit Azure Digital Twins eigene benutzerdefinierte Typen für Zwillinge definieren können, gibt es keine Möglichkeit, statische Rückgabedaten für viele Aufrufe von Azure Digital Twins vorab zu definieren. Stattdessen geben die APIs stark typisierte Wrappertypen zurück, sofern zutreffend, und die benutzertypisierten Zwillingsdaten befinden sich in Json.NET-Objekten (wird überall dort verwendet, wo der Datentyp „object" in den API-Signaturen angezeigt wird). Sie können diese Objekte entsprechend in Ihren Code umwandeln.

### <a name="error-handling"></a>Fehlerbehandlung

Wenn ein Fehler im SDK auftritt (einschließlich HTTP-Fehlern wie z. B. 404), löst das SDK eine Ausnahme aus. Aus diesem Grund ist es wichtig, alle API-Aufrufe in try/catch-Blöcken zu kapseln.

Hier sehen Sie einen Codeausschnitt, der versucht, einen Zwilling hinzuzufügen und Fehler in diesem Prozess abfängt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Paging

AutoRest generiert zwei Arten von Pagingmustern für das SDK:
* Eine für alle APIs außer der Abfrage-API
* Eine für die Abfrage-API

Im nicht abfragenden Pagingmuster zeigt die folgende Beispielmethode, wie Sie eine ausgelagerte Liste von ausgehenden Beziehungen von Azure Digital Twins abrufen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Das zweite Muster wird nur für die Abfrage-API generiert. Es verwendet explizit ein `continuationToken`.

>[!TIP]
> Ein Hauptgrund für das Abrufen von Seiten ist die Berechnung der [Gebühren für Abfrageeinheiten](concepts-query-units.md) für einen Abfrage-API-Befehl.

Hier ist ein Beispiel mit diesem Muster:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Nächste Schritte

Führen Sie die Schritte zum Erstellen einer Client-App aus, in der Sie Ihr SDK verwenden können:
* [*Tutorial: Codieren einer Client-App*](tutorial-code.md)
