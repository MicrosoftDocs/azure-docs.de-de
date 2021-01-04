---
title: Verwenden eines ONNX-Modells mit Windows ML – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie eine Windows-UWP-App erstellen, die ein aus Azure Cognitive Services exportiertes ONNX-Modell verwendet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616075"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Verwenden eines ONNX-Modells von Custom Vision mit Windows ML (Vorschauversion)

Es wird beschrieben, wie Sie ein aus Custom Vision exportiertes ONNX-Modell mit Windows ML (Vorschauversion) verwenden.

In diesem Leitfaden erfahren Sie, wie Sie eine ONNX-Datei, die aus dem Custom Vision Service exportiert wurde, mit Windows ML verwenden können. Sie werden die UWP-Beispielanwendung mit Ihrer eigenen trainierten Bildklassifizierung verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Windows 10, Version 1809 oder höher
* Windows SDK für Build 17763 oder höher
* Visual Studio 2017 Version 15.7 oder höher mit aktivierter Workload __Entwicklung für die universelle Windows-Plattform__.
* Auf Ihrem PC ist der Entwicklermodus aktiviert. Weitere Informationen finden Sie unter [Aktivieren von Geräten für die Entwicklung](/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Informationen zur Beispiel-App

Die enthaltene Anwendung ist eine generische Windows-UWP-App. Sie ermöglicht es Ihnen, ein Bild von Ihrem Computer auszuwählen, das dann von einem lokal gespeicherten Klassifizierungsmodell verarbeitet wird. Die vom Modell zurückgegebenen Tags und Bewertungen werden neben dem Bild angezeigt.

## <a name="get-the-example-code"></a>Abrufen des Beispielcodes

Die Beispielanwendung ist im Repository [Cognitive Services ONNX Custom Vision Sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) (Beispiel für kognitive Dienste für ONNX Custom Vision) auf GitHub verfügbar. Klonen Sie sie auf Ihren lokalen Computer, und öffnen Sie *SampleOnnxEvaluationApp.sln* in Visual Studio.

## <a name="test-the-application"></a>Testen der Anwendung

1. Verwenden Sie die Taste `F5`, um die Anwendung in Visual Studio zu starten. Unter Umständen werden Sie aufgefordert, den Entwicklermodus zu aktivieren.
1. Wenn die Anwendung startet, verwenden Sie die Schaltfläche, um ein Bild zur Bewertung auszuwählen. Das ONNX-Standardmodell ist darauf trainiert, verschiedene Arten von Plankton zu klassifizieren.

## <a name="use-your-own-model"></a>Verwenden eines eigenen Modells

Gehen Sie wie folgt vor, um Ihr eigenes Bildklassifizierungsmodell zu verwenden:

1. Erstellen und trainieren Sie eine Klassifizierung mit dem Custom Vision Service. Anweisungen dazu finden Sie unter [Erstellen und Trainieren einer Klassifizierung](./getting-started-build-a-classifier.md). Verwenden Sie eine der **kompakten** Domänen, z. B. **Allgemein (kompakt)** . 
   * Wenn Sie über eine vorhandene Klassifizierung verfügen, die eine andere Domäne verwendet, können Sie sie in den Projekteinstellungen in **kompakt** konvertieren. Trainieren Sie dann das Projekt erneut, bevor Sie fortfahren.
1. Exportieren Sie Ihr Modell. Wechseln Sie zur Registerkarte „Leistung“, und wählen Sie eine Iteration aus, die mit einer **kompakten** Domäne trainiert wurde. Wählen Sie die Schaltfläche **Exportieren** aus, die angezeigt wird. Wählen Sie dann **ONNX** und anschließend **Exportieren** aus. Sobald die Datei bereit ist, wählen Sie die Schaltfläche **Herunterladen**. Weitere Informationen zu Exportoptionen finden Sie unter [Exportieren Ihres Modells](./export-your-model.md).
1. Öffnen Sie die heruntergeladene *ZIP*-Datei, und extrahieren Sie daraus die Datei *model.onnx*. Diese Datei enthält das Klassifizierungsmodell.
1. Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf den Ordner **Ressourcen**, und wählen Sie __Vorhandenes Element hinzufügen__ aus. Wählen Sie Ihre ONNX-Datei aus.
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die ONNX-Datei, und wählen Sie **Eigenschaften** aus. Ändern Sie die folgenden Eigenschaften für die Datei:
   * __Buildvorgang__ -> __Inhalt__
   * __In Ausgabeverzeichnis kopieren__ -> __Kopieren, wenn neuer__
1. Öffnen Sie dann _MainPage.xaml.cs_, und ändern Sie den Wert von `_ourOnnxFileName` in den Namen Ihrer ONNX-Datei.
1. Verwenden Sie `F5`, um das Projekt zu erstellen und auszuführen.
1. Klicken Sie auf die Schaltfläche, um ein Bild für die Auswertung auszuwählen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Möglichkeiten zum Exportieren und Verwenden eines Custom Vision-Modells finden Sie in den folgenden Dokumenten:

* [Exportieren Ihres Modells](./export-your-model.md)
* [Use exported Tensorflow model in an Android application](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) (Verwenden des exportierten Tensorflow-Modells in einer Android-Anwendung)
* [Use exported CoreML model in a Swift iOS application](https://go.microsoft.com/fwlink/?linkid=857726) (Verwenden des exportierten CoreML-Modells in einer Swift iOS-Anwendung)
* [Use exported CoreML model in an iOS application with Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) (Verwenden des exportierten CoreML-Modells in einer iOS-Anwendung mit Xamarin)

Weitere Informationen zur Verwendung von ONNX-Modellen mit Windows ML finden Sie unter [Integrieren eines Modells in Ihre App mit Windows ML](/windows/ai/windows-ml/integrate-model).