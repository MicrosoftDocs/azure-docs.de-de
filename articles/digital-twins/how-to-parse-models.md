---
title: Analysieren und Validieren von Modellen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie die Parserbibliothek zum Analysieren von DTDL-Modellen verwenden können.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2e13762698efd8d5df42ab6315c990d4096168cf
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145530"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Analysieren und Validieren von Modellen mit der DTDL-Parserbibliothek

[Modelle](concepts-models.md) in Azure Digital Twins werden mithilfe der auf JSON-LD basierenden Sprache DTDL (Digital Twins Definition Language) definiert. **Es wird empfohlen, Ihre Modelle offline zu validieren, bevor Sie sie in Ihre Azure Digital Twins-Instanz hochladen.**

Um dies zu unterstützen, wird eine clientseitige .NET DTDL-Bibliothek auf NuGet bereitgestellt: [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Sie können die Parserbibliothek direkt in Ihrem C#-Code verwenden, oder Sie verwenden das Beispielprojekt für die sprachagnostische Codeerstellung, das auf der Parserbibliothek basiert: [**DTDL-Validierungssteuerelement (Beispiel)**](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Verwenden des Beispiels des DTDL-Validierungssteuerelements

[**DTDL Validator**](/samples/azure-samples/dtdl-validator/dtdl-validator) ist ein Beispielprojekt für die Überprüfung von Modelldokumenten, um sicherzustellen, dass die DTDL gültig ist. Es basiert auf der .NET-Parserbibliothek und ist sprachunabhängig. Sie können es über die Schaltfläche *Download ZIP* (ZIP-Datei herunterladen) im Beispiellink abrufen.

Der Quellcode zeigt Beispiele für die Verwendung der Parserbibliothek. Sie können das Validierungsbeispiel als Befehlszeilenhilfsprogramm zum Überprüfen einer Verzeichnisstruktur mit DTDL-Dateien verwenden. Außerdem stellt es einen interaktiven Modus bereit.

Im Ordner des Beispiels für das DTDL-Validierungssteuerelement finden Sie in der Datei *readme.md* Anweisungen, wie das Beispiel in eine eigenständige ausführbare Datei gepackt werden kann.

Nachdem Sie ein eigenständiges Paket erstellt und die ausführbare Datei zu Ihrem Pfad hinzugefügt haben, können Sie das Validierungssteuerelement mit diesem Befehl in einer Konsole auf Ihrem Computer ausführen:

```cmd/sh
DTDLValidator
```

Bei den Standardoptionen sucht das Beispiel im aktuellen Verzeichnis und allen Unterverzeichnissen nach `*.json`-Dateien. Sie können auch die folgende Option hinzufügen, um das Beispiel im angegebenen Verzeichnis und allen Unterverzeichnissen nach Dateien mit der Erweiterung *.dtdl* suchen zu lassen:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Sie können die Option `-i` für das Beispiel hinzufügen, um in den interaktiven Modus zu wechseln:

```cmd/sh
DTDLValidator -i
```

Weitere Informationen zu diesem Beispiel finden Sie im Quellcode, oder führen Sie `DTDLValidator --help` aus.

## <a name="use-the-net-parser-library"></a>Verwenden der .NET-Parserbibliothek 

Die [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)-Bibliothek ermöglicht den Zugriff des Modells auf die DTDL-Definitionen und fungiert im Wesentlichen als Äquivalent der C#-Reflexion für DTDL. Diese Bibliothek kann unabhängig von jedem [Azure Digital Twins SDK](how-to-use-apis-sdks.md) verwendet werden, insbesondere für die DTDL-Validierung in einem grafischen- oder Texteditor. Sie ist nützlich, um sicherzustellen, dass Ihre Modelldefinitionsdateien gültig sind, ehe Sie versuchen, sie in den Dienst hochzuladen.

Um die Parserbibliothek verwenden zu können, müssen Sie ihr eine Gruppe von DTDL-Dokumenten zur Verfügung stellen. Normalerweise würden Sie diese Musterdokumente aus dem Dienst abrufen, aber Sie könnten sie auch lokal verfügbar haben, wenn Ihr Client in erster Linie für das Hochladen in den Dienst zuständig ist. 

Dies ist der allgemeine Workflow für die Verwendung des Parsers:
1. Rufen Sie einige oder alle DTDL-Dokumente aus dem Dienst ab.
2. Übergeben Sie die zurückgegebenen, speicherinternen DTDL-Dokumente an den Parser.
3. Der Parser überprüft die an ihn weitergegebene Dokumentgruppe und gibt detaillierte Fehlerinformationen zurück. Dies ist in Editorszenarien nützlich.
4. Verwenden Sie die Parser-APIs, um die Analyse der in der Dokumentengruppe enthaltenen Modelle fortzusetzen. 

Der Parser bietet die folgenden Funktionen:
* Abrufen aller implementierten Modellschnittstellen (Inhalt des Abschnitts `extends` der Schnittstelle)
* Abrufen sämtlicher im Modell deklarierter Eigenschaften, Telemetrie, Befehle, Komponenten und Beziehungen Dieser Befehl dient auch zum Abrufen aller Metadaten, die in diesen Definitionen enthalten sind, und berücksichtigt die Vererbung (`extends`-Abschnitte).
* Abrufen aller komplexer Modelldefinitionen
* Bestimmen, ob ein Modell aus einem anderen Modell zugewiesen werden kann

> [!NOTE]
> [IoT Plug & Play](../iot-pnp/overview-iot-plug-and-play.md)-Geräte (PnP) arbeiten mit einer geringfügig anderen Syntaxvariante, um ihre Funktionalität zu beschreiben. Diese Syntaxvariante ist eine semantisch kompatible Teilmenge der Sprache DTDL, die in Azure Digital Twins verwendet wird. Wenn Sie die Parserbibliothek nutzen, müssen Sie nicht wissen, welche Syntaxvariante zur Erstellung der DTDL für Ihren Digital Twin verwendet wurde. Der Parser gibt für die PnP- und Azure Digital Twins-Syntax standardmäßig stets dasselbe Modell zurück.

### <a name="code-with-the-parser-library"></a>Codieren mit der Parserbibliothek

Sie können die Parserbibliothek direkt verwenden, z. B. für die Überprüfung von Modellen in Ihrer eigenen Anwendung oder für die Generierung dynamischer, modellgesteuerter Benutzeroberflächen, Dashboards und Berichte.

Zur Unterstützung des nachstehenden Parsercodebeispiels betrachten Sie mehrere Modelle, die in einer Azure Digital Twins-Instanz definiert sind:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeMaker;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Component",
        "name": "coffeeMaker",
        "schema": "dtmi:com:contoso:coffeeMakerInterface;1"
      }
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeMakerInterface;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "waterTemp",
        "schema": "double"
      }
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeBar;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Relationship",
        "name": "foo",
        "target": "dtmi:com:contoso:coffeeMaker;1"
      },
      {
        "@type": "Property",
        "name": "capacity",
        "schema": "integer"
      }
    ]
  }
]
```

Der folgende Code zeigt ein Beispiel für die Verwendung der Parserbibliothek, um diese Definitionen in C# wiederzugeben:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<DigitalTwinsModelData> mdata = client.GetModelsAsync(new GetModelsOptions { IncludeModelDefinition = true });
        List<string> models = new List<string>();
        await foreach (DigitalTwinsModelData md in mdata)
            models.Add(md.DtdlModel);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit dem Schreiben Ihrer Modelle fertig sind, erfahren Sie, wie Sie sie mit den DigitalTwinsModels-APIs hochladen (und andere Verwaltungsvorgänge durchführen):
* [*Verwenden Verwalten von Azure Digital Twins-Modellen*](how-to-manage-model.md)