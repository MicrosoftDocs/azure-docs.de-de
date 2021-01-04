---
title: Erstellen von Vorhersagen mit dem AutoML-ONNX-Modell in .NET
description: Erfahren Sie, wie Sie mithilfe eines AutoML-ONNX-Modells Vorhersagen in .NET mit ML.NET erstellen.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358812"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Erstellen von Vorhersagen mit einem AutoML-ONNX-Modell in .NET

In diesem Artikel erfahren Sie, wie Sie mithilfe eines AutoML-ONNX-Modells (Open Neural Network Exchange) Vorhersagen in einer C#.NET Core-Konsolenanwendung mit ML.NET erstellen können.

[ML.NET](/dotnet/machine-learning/) ist ein plattformübergreifendes Open-Source-Framework für maschinelles Lernen für das .NET-Ökosystem, das es Ihnen ermöglicht, benutzerdefinierte Machine Learning-Modelle mithilfe eines Code First-Ansatzes in C# oder F# sowie mithilfe von Tools mit wenig Code wie [Model Builder](/dotnet/machine-learning/automate-training-with-model-builder) und der [ML.NET CLI](/dotnet/machine-learning/automate-training-with-cli) zu trainieren und zu verwenden. Das Framework ist auch erweiterbar und ermöglicht Ihnen die Nutzung anderer beliebter Frameworks für maschinelles Lernen wie TensorFlow und ONNX.

ONNX ist ein Open-Source-Format für KI-Modelle. ONNX unterstützt die Interoperabilität zwischen Frameworks. Das bedeutet, dass Sie ein Modell in einem der vielen beliebten Frameworks für maschinelles Lernen wie PyTorch trainieren, es in das ONNX-Format konvertieren und das ONNX-Modell in einem anderen Framework wie ML.NET verwenden können. Weitere Informationen finden Sie auf der [ONNX-Website](https://onnx.ai/).

## <a name="prerequisites"></a>Voraussetzungen

- [.NET Core SDK 3.1 oder höher](https://dotnet.microsoft.com/download)
- Text-Editor oder IDE (z. B. [Visual Studio](https://visualstudio.microsoft.com/vs/) oder [Visual Studio Code](https://code.visualstudio.com/Download))
- ONNX-Modell Informationen zum Trainieren eines AutoML-ONNX-Modells finden Sie im folgenden [Klassifizierungsnotebook für Bank Marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (optional)

## <a name="create-a-c-console-application"></a>Erstellen einer C#-Konsolenanwendung

In diesem Beispiel verwenden Sie die .NET Core-CLI, um Ihre Anwendung zu erstellen, aber Sie können dieselben Aufgaben mithilfe von Visual Studio ausführen. Erfahren Sie mehr über die [.NET Core-CLI](/dotnet/core/tools/).

1. Öffnen Sie ein Terminal, und erstellen Sie eine neue C#.NET Core-Konsolenanwendung. In diesem Beispiel lautet der Name der Anwendung `AutoMLONNXConsoleApp`. Es wird ein gleichnamiges Verzeichnis mit dem Inhalt Ihrer Anwendung erstellt.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. Navigieren Sie im Terminal zum Verzeichnis *AutoMLONNXConsoleApp*.

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Hinzufügen von Softwarepaketen

1. Installieren Sie die NuGet-Pakete **Microsoft.ML** , **Microsoft.ML.OnnxRuntime** und **Microsoft.ML.OnnxTransformer** mithilfe der .NET Core-CLI.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Diese Pakete enthalten die Abhängigkeiten, die erforderlich sind, um ein ONNX-Modell in einer .NET-Anwendung zu verwenden. ML.NET stellt eine API bereit, die die [ONNX-Runtime](https://github.com/Microsoft/onnxruntime) für Vorhersagen verwendet.

1. Öffnen Sie die Datei *Program.cs* , und fügen Sie oben die folgenden `using`-Anweisungen ein, um auf die entsprechenden Pakete zu verweisen.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Hinzufügen eines Verweises auf das ONNX-Modell

Eine Möglichkeit für die Konsolenanwendung für den Zugriff auf das ONNX-Modell besteht darin, das ONNX-Modell zum Buildausgabeverzeichnis hinzuzufügen.  Weitere Informationen zu allgemeinen MSBuild-Elementen finden Sie im [MSBuild-Leitfaden](/visualstudio/msbuild/common-msbuild-project-items).

Hinzufügen eines Verweises auf Ihre ONNX-Modelldatei in Ihrer Anwendung

1. Kopieren Sie Ihr ONNX-Modell in das Stammverzeichnis *AutoMLONNXConsoleApp* Ihrer Anwendung.
1. Öffnen Sie die Datei *AutoMLONNXConsoleApp.csproj* , und fügen Sie den folgenden Inhalt innerhalb des Knotens `Project` hinzu.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    In diesem Fall lautet der Name der ONNX-Modelldatei *automl-model.onnx*.

1. Öffnen Sie die Datei *Program.cs* , und fügen Sie die folgende Zeile innerhalb der Klasse `Program` ein.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>Initialisieren von MLContext

Erstellen Sie innerhalb der `Main`-Methode Ihrer `Program`-Klasse eine neue Instanz von [`MLContext`](xref:Microsoft.ML.MLContext).

```csharp
MLContext mlContext = new MLContext();
```

Die Klasse [`MLContext`](xref:Microsoft.ML.MLContext) ist ein Startpunkt für alle ML.NET-Vorgänge, und die Initialisierung von `mlContext` erstellt eine neue ML.NET-Umgebung, die während des gesamten Modelllebenszyklus gemeinsam genutzt werden kann. Es ist konzeptionell ähnlich wie DbContext in Entity Framework.

## <a name="define-the-model-data-schema"></a>Definieren des Modelldatenschemas

Ihr Modell erwartet Ihre Eingabe- und Ausgabedaten in einem bestimmten Format. ML.NET ermöglicht es Ihnen, das Format Ihrer Daten über Klassen zu definieren. Manchmal wissen Sie vielleicht schon, wie dieses Format aussieht. In Fällen, in denen Sie das Datenformat nicht kennen, können Sie Tools wie Netron verwenden, um Ihr ONNX-Modell zu untersuchen.

Das in diesem Beispiel verwendete Modell verwendet Daten aus dem Dataset für NYC TLC-Taxifahrten. Nachfolgend finden Sie eine Stichprobe der Daten:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15,5|
|VTS|1|1|480|2.72|CRD|10.0|
|VTS|1|1|1680|7,8|CSH|26,5|

### <a name="inspect-the-onnx-model-optional"></a>Untersuchen des ONNX-Modells (optional)

Verwenden Sie ein Tool wie Netron, um die Ein- und Ausgaben Ihres Modells zu untersuchen.

1. Öffnen Sie Netron.
1. Wählen Sie in der oberen Menüleiste **Datei > Öffnen** aus, und verwenden Sie den Dateibrowser zur Auswahl Ihres Modells.
1. Ihr Modell wird geöffnet. Die Struktur des Modells *automl-model.onnx* sieht z. B. wie folgt aus:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML-ONNX-Modell":::

1. Wählen Sie den letzten Knoten am unteren Rand des Graphen aus (in diesem Fall `variable_out1`), um die Metadaten des Modells anzuzeigen. Die Ein- und Ausgaben auf der Randleiste zeigen Ihnen die erwarteten Eingaben, Ausgaben und Datentypen des Modells. Verwenden Sie diese Informationen, um das Ein- und Ausgabeschema Ihres Modells zu definieren.

### <a name="define-model-input-schema"></a>Definieren des Modelleingabeschemas

Erstellen Sie eine neue Klasse namens `OnnxInput` mit den folgenden Eigenschaften innerhalb der Datei *Program.cs*.

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Jede der Eigenschaften wird einer Spalte im Dataset zugeordnet. Die Eigenschaften werden zudem mit Attributen versehen.

Mit dem [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute)-Attribut können Sie angeben, wie ML.NET auf die Spalte verweisen soll, wenn mit den Daten gearbeitet wird. Obwohl die Eigenschaft `TripDistance` z. B. den standardmäßigen .NET-Namenskonventionen folgt, kennt das Modell nur eine Spalte oder ein Feature, die bzw. das als `trip_distance` bekannt ist. Um diese Diskrepanz bei der Benennung zu beheben, ordnet das [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute)-Attribut die Eigenschaft `TripDistance` zu einer Spalte oder zu einem Feature namens `trip_distance` zu.
  
Bei numerischen Werten arbeitet ML.NET nur mit [`Single`](xref:System.Single)-Werttypen. Der ursprüngliche Datentyp einiger der Spalten sind jedoch ganze Zahlen. Das [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute)-Attribut ordnet Typen zwischen ONNX und ML.NET zu.

Weitere Informationen zu Datenattributen finden Sie im [ML.NET-Leitfaden zum Laden von Daten](/dotnet/machine-learning/how-to-guides/load-data-ml-net).

### <a name="define-model-output-schema"></a>Definieren des Modellausgabeschemas

Nachdem die Daten verarbeitet wurden, erzeugen sie eine Ausgabe in einem bestimmten Format. Definieren Sie Ihr Datenausgabeschema. Erstellen Sie eine neue Klasse namens `OnnxOutput` mit den folgenden Eigenschaften innerhalb der Datei *Program.cs*.

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Ähnlich wie bei `OnnxInput` verwenden Sie das Attribut [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute), um die Ausgabe `variable_out1` einem beschreibenderen `PredictedFare`-Namen zuzuordnen.

## <a name="define-a-prediction-pipeline"></a>Definieren einer Vorhersagepipeline

Eine Pipeline in ML.NET ist typischerweise eine Reihe von verketteten Transformationen, die mit den Eingabedaten arbeiten, um eine Ausgabe zu erzeugen. Weitere Informationen zu Datentransformationen finden Sie im [ML.NET-Leitfaden zur Datentransformation](/dotnet/machine-learning/resources/transforms).

1. Erstellen einer neuen Methode namens `GetPredictionPipeline` innerhalb der Klasse `Program`

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Definieren Sie den Namen der Ein- und Ausgabespalten. Fügen Sie der `GetPredictionPipeline`-Methode den folgenden Code hinzu.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Definieren Sie Ihre Pipeline. Ein [`IEstimator`](xref:Microsoft.ML.IEstimator%601) bietet eine Blaupause der Vorgänge sowie Ein- und Ausgabeschemas Ihrer Pipeline.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    In diesem Fall ist [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) die einzige Transformation in der Pipeline, die die Namen der Ein- und Ausgabespalten sowie den Pfad zur ONNX-Modelldatei übernimmt.

1. Ein [`IEstimator`](xref:Microsoft.ML.IEstimator%601) definiert nur den Satz von Vorgängen, der auf Ihre Daten angewendet werden soll. Was mit Ihren Daten arbeitet, wird als [`ITransformer`](xref:Microsoft.ML.ITransformer) bezeichnet. Verwenden Sie die `Fit`-Methode, um eine aus Ihrer `onnxPredictionPipeline` zu erstellen.

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    Die [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A)-Methode erwartet ein [`IDataView`](xref:Microsoft.ML.IDataView) als Eingabe, um die Vorgänge für auszuführen. Ein [`IDataView`](xref:Microsoft.ML.IDataView) ist eine Möglichkeit, Daten in ML.NET mithilfe eines Tabellenformats darzustellen. Da in diesem Fall die Pipeline nur für Vorhersagen verwendet wird, können Sie ein leeres [`IDataView`](xref:Microsoft.ML.IDataView) bereitstellen, um dem [`ITransformer`](xref:Microsoft.ML.ITransformer) die erforderlichen Informationen zum Ein- und Ausgabeschema zu geben. Das angepasste [`ITransformer`](xref:Microsoft.ML.ITransformer) wird dann zur weiteren Verwendung in Ihrer Anwendung zurückgegeben.

    > [!TIP]
    > In diesem Beispiel wird die Pipeline innerhalb derselben Anwendung definiert und verwendet. Es wird jedoch empfohlen, dass Sie separate Anwendungen zur Definition und Verwendung Ihrer Pipeline für Vorhersagen verwenden. In ML.NET können Ihre Pipelines serialisiert und zur weiteren Verwendung in anderen .NET-Endbenutzeranwendungen gespeichert werden. ML.NET unterstützt verschiedene Bereitstellungsziele wie Desktopanwendungen, Webdienste, WebAssembly-Anwendungen* und viele andere. Weitere Informationen zum Speichern von Pipelines finden Sie im [ML.NET-Leitfaden zum Speichern und Laden von trainierten Modellen](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net).
    >
    > *WebAssembly wird nur in .NET Core 5 oder höher unterstützt

1. Rufen Sie innerhalb der `Main`-Methode die `GetPredictionPipeline`-Methode mit den erforderlichen Parametern auf.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Verwenden des Modells für Vorhersagen

Nachdem Sie jetzt über eine Pipeline verfügen, ist es an der Zeit, diese für Vorhersagen zu verwenden. ML.NET stellt eine praktische API für die Erstellung von Vorhersagen für eine einzelne Dateninstanz namens [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) zur Verfügung.

1. Erstellen Sie innerhalb der `Main`-Methode eine [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) mithilfe der [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A)-Methode.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Erstellen Sie eine Testdateneingabe.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Verwenden Sie die `predictionEngine`, um Vorhersagen auf der Grundlage der neuen `testInput`-Daten mithilfe der [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A)-Methode zu treffen.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Geben Sie das Ergebnis Ihrer Vorhersage in der Konsole aus.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Verwenden Sie die .NET Core-CLI zur Ausführung Ihrer Anwendung.

    ```dotnetcli
    dotnet run
    ```

    Das Ergebnis sollte so ähnlich wie die folgende Ausgabe aussehen:

    ```text
    Predicted Fare: 15.621523
    ```

Weitere Informationen zur Erstellung von Vorhersagen in ML.NET finden Sie im [Leitfaden zum Verwenden eines Modells für Vorhersagen](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net).

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen Ihres Modells als ASP.NET Core-Web-API](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Bereitstellen Ihres Modells als serverlose .NET-Azure-Funktion](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)