---
title: Integrieren von Branchenstandardmodellen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Branchenstandardmodelle in DTDL für Azure Digital Twins integrieren, indem Sie entweder spezielle DTDL-Ontologien verwenden oder vorhandene Ontologien konvertieren.
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f5bfe128ddc04e8048bb89a8e39035434dfd2b92
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352880"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Integrieren von Branchenstandardmodellen in DTDL für Azure Digital Twins

Die Verwendung von Modellen, die auf Branchenstandards basieren oder eine standardmäßige Ontologiedarstellung nutzen (z. B. RDF oder OWL), bietet einen guten Ausgangspunkt für den Entwurf Ihrer Azure Digital Twins-Modelle. Die Verwendung von Branchenmodellen unterstützt außerdem die Standardisierung und Informationsfreigabe.

Für die Verwendung in Azure Digital Twins muss ein Modell in der auf JSON-LD basierenden Sprache [**DTDL (Digital Twins Definition Language)**](concepts-models.md) dargestellt werden. Deshalb wird in diesem Artikel beschrieben, wie Sie die Branchenstandardmodelle in DTDL darstellen und die vorhandenen Branchenkonzepte in die DTDL-Semantik integrieren, sodass diese von Azure Digital Twins verwendet werden können. Das DTDL-Modell dient dann als allgemeingültige Datenbasis für das Modell in Azure Digital Twins.

Es gibt drei Möglichkeiten für das Integrieren von Branchenstandardmodellen in DTDL:
* **Übernehmen**: Sie können Ihre Lösung mit einer Open-Source-DTDL-Ontologie beginnen, die auf weit verbreiteten Branchenstandards basiert. 
* **Konvertieren**: Wenn Sie bereits über vorhandene Modelle verfügen, müssen Sie diese in DTDL konvertieren.
* **Erstellen**: Sie können eigene benutzerdefinierte DTDL-Modelle von Grund auf neu entwickeln. Eine Beschreibung finden Sie unter [*Vorgehensweise: Verwalten von Azure Digital Twins-Modellen*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Übernehmen einer Open-Source-DTDL-Ontologie

Häufig ist es einfacher, mit einer Open-Source-DTDL-Ontologie zu beginnen als mit einer leeren Seite. 

Beispielsweise können Lösungen für intelligente Gebäude die auf [**Open-Source-DTDL basierende RealEstateCore-Ontologie**](https://github.com/Azure/opendigitaltwins-building) nutzen, die eine gemeinsame Grundlage für die Modellierung von intelligenten Gebäuden bietet und Branchenstandards verwendet, um eine Neuentwicklung zu vermeiden. 

Diese Open-Source-DTDL-Ontologien bieten auch bewährte Methoden für die Nutzung und ordnungsgemäße Erweiterung der Modelle. 

## <a name="convert-existing-models-to-dtdl"></a>Konvertieren vorhandener Modelle in DTDL

Die meisten Branchenmodelle (auch als **Ontologien** bezeichnet) basieren auf semantischen Webstandards wie [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF) und [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Damit ein Modell mit Azure Digital Twins verwendet werden kann, muss es im DTDL-Format vorliegen. Dieser Abschnitt bietet einen allgemeinen Leitfaden für den Entwurf in Form eines **Konvertierungsmusters** zur Konvertierung von RDF-basierten Modellen in DTDL, damit diese mit Azure Digital Twins verwendet werden können. 

Er enthält außerdem einen [**Beispielkonvertercode** für einen RDF-Konverter](#sample-converter-application), der für das [Brick](https://brickschema.org/ontology/)-Schema überprüft wurde und für andere Schemas in der Baubranche erweitert werden kann.

### <a name="conversion-pattern"></a>Konvertierungsmuster

Zur Konvertierung von RDF-basierten Modellen in DTDL können mehrere Bibliotheken von Drittanbietern verwendet werden. Mit einigen dieser Bibliotheken können Sie die Modelldatei in ein Diagramm laden. Sie können das Diagramm durchlaufen, um nach spezifischen RDFS- und OWL-Konstrukten zu suchen und diese in DTDL zu konvertieren.   

In der folgenden Tabelle sind Beispiele dafür aufgeführt, wie RDFS- und OWL-Konstrukte in DTDL zugeordnet werden können. 

| RDFS/OWL-Konzept | RDFS/OWL-Konstrukt | DTDL-Konzept | DTDL-Konstrukt |
| --- | --- | --- | --- |
| Klassen | `owl:Class`<br>IRI-Suffix<br>``rdfs:label``<br>``rdfs:comment`` | Schnittstelle | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Unterklassen von  werden erstellt. | `owl:Class`<br>IRI-Suffix<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Schnittstelle | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Datentypeigenschaften | `owl:DatatypeProperty`<br>`rdfs:label` oder `INode`<br>`rdfs:label`<br>`rdfs:range` | Schnittstelleneigenschaften | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objekteigenschaften | `owl:ObjectProperty`<br>`rdfs:label` oder `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (oder weggelassen, wenn ohne `rdfs:range`)<br>`comment`<br>`displayName`<br>

Im folgenden C#-Codeausschnitt wird eine RDF-Modelldatei unter Verwendung der [**dotNetRDF**](https://www.dotnetrdf.org/)-Bibliothek in ein Diagramm geladen und in DTDL konvertiert. 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

### <a name="sample-converter-application"></a>Beispielkonverteranwendung 

Es steht eine Beispielanwendung zur Verfügung, mit der eine RDF-basierte Modelldatei für die Verwendung im Azure Digital Twins-Dienst in [DTDL (Version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) konvertiert werden kann. Diese wurde für das [Brick](https://brickschema.org/ontology/)-Schema überprüft und kann für andere Schemas in der Baubranche erweitert werden (z. B. [Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/), [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/) oder [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Dabei handelt es sich um die .NET Core-Befehlszeilenanwendung **RdfToDtdlConverter**.

Hier können Sie die Beispielanwendung herunterladen: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Um den Code auf Ihren Computer herunterzuladen, klicken Sie auf die Schaltfläche *ZIP herunterladen* unterhalb des Titels auf der Landing Page der Beispielanwendung. Dadurch wird eine *ZIP-Datei* mit dem Namen *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* heruntergeladen, die Sie dann entzippen und ausprobieren können.

Sie können sich mit dieser Beispielanwendung die Konvertierungsmuster im Kontext ansehen und als Baustein für Ihre eigenen Anwendungen verwenden. Außerdem können Sie entsprechend Ihren spezifischen Anforderungen Modellkonvertierungen durchführen.

## <a name="validate-and-upload-dtdl-models"></a>Überprüfen und Hochladen von DTDL-Modellen

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Nachdem ein Modell konvertiert und überprüft wurde, können Sie **es in Ihre Azure Digital Twins-Instanz hochladen**. Weitere Informationen zu diesem Prozess finden Sie im Abschnitt [*Hochladen von Modellen*](how-to-manage-model.md#upload-models) unter *Verwalten benutzerdefinierter Modelle*.

## <a name="next-steps"></a>Nächste Schritte 

Erfahren Sie mehr über das Entwerfen und Verwalten von Modellen digitaler Zwillinge:
 
* [*Konzepte: Grundlegendes zu Zwillingsmodellen in Azure Digital Twins*](concepts-models.md)
* [*Verwenden Verwalten von Azure Digital Twins-Modellen*](how-to-manage-model.md)
* [*Verwenden Analysieren und Validieren von Modellen*](how-to-parse-models.md)