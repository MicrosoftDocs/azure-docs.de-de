---
title: 'Tutorial: Verwenden der benutzerdefinierten Logoerkennung zum Erkennen von Azure-Diensten: Custom Vision'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial arbeiten Sie Schritt für Schritt eine Beispiel-App durch, die Custom Vision im Rahmen eines Szenarios zur Logoerkennung verwendet. Erfahren Sie, wie Custom Vision mit anderen Komponenten verwendet wird, um eine End-to-End-Anwendung bereitzustellen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: e5e344aa84bfc6912266720083f0cd15f575883c
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95740249"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutorial: Erkennen von Azure-Dienstlogos in Bildern von der Kamera

In diesem Tutorial erkunden Sie eine Beispiel-App, die Custom Vision im Rahmen eines größeren Szenarios verwendet. Die AI Visual Provision-App (eine Xamarin.Forms-App für mobile Plattformen) analysiert Kamerabilder von Azure-Dienstlogos und stellt dann die eigentlichen Dienste im Azure-Konto des Benutzers bereit. Hier erfahren Sie, wie Custom Vision in Verbindung mit anderen Komponenten verwendet wird, um eine nützliche End-to-End-Anwendung bereitzustellen. Sie können das gesamte App-Szenario für sich selbst ausführen oder lediglich den Custom Vision-Teil des Setups absolvieren und sich ansehen, wie dieser von der App genutzt wird.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> - Erstellen einer benutzerdefinierten Objekterkennung zum Erkennen von Azure-Dienstlogos
> - Verbinden Ihrer App mit der Maschinelles Sehen-API von Azure und Custom Vision
> - Erstellen eines Azure-Dienstprinzipalkontos zum Bereitstellen von Azure-Diensten über die App

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/)
- Xamarin-Workload für Visual Studio (siehe [Installieren von Xamarin](/xamarin/cross-platform/get-started/installation/windows))
- iOS- oder Android-Emulator für Visual Studio
- [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (optional)

## <a name="get-the-source-code"></a>Herunterladen des Quellcodes

Wenn Sie die bereitgestellte Web-App verwenden möchten, können Sie den Quellcode der App aus dem Repository für [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) auf GitHub klonen oder herunterladen. Öffnen Sie die Datei *Source/VisualProvision.sln* in Visual Studio. Später bearbeiten Sie einige Projektdateien, damit Sie die App ausführen können.

## <a name="create-an-object-detector"></a>Erstellen einer Objekterkennung

Melden Sie sich bei der [Custom Vision-Website](https://customvision.ai/) an, und erstellen Sie ein neues Projekt. Geben Sie ein Projekt zur Objekterkennung an, und verwenden Sie die Domäne „Logo“ (dadurch kann der Dienst einen für die Logoerkennung optimierten Algorithmus verwenden). 

![Fenster für ein neues Projekt auf der Custom Vision-Website im Chrome-Browser](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Hochladen und Kennzeichnen von Bildern

Trainieren Sie als Nächstes den Algorithmus für die Logoerkennung, indem Sie Bilder von Azure-Dienstlogos hochladen und manuell markieren bzw. taggen. Im AIVisualProvision-Repository finden Sie eine Reihe von Trainingsbildern, die Sie hierfür verwenden können. Wählen Sie auf der Website auf der Registerkarte **Training Images** (Trainingsbilder) die Schaltfläche **Bilder hinzufügen**. Navigieren Sie anschließend zum Ordner **Documents/Images/Training_DataSet** des Repositorys. Sie müssen die Logos manuell in jedem Bild markieren. Wenn Sie dieses Projekt nur testen möchten, kann es daher ratsam sein, nur eine Teilmenge der Bilder hochzuladen. Laden Sie mindestens 15 Instanzen jedes Tags hoch, das Sie verwenden möchten.

Wählen Sie nach dem Hochladen der Trainingsbilder das erste Bild in der Anzeige aus. Daraufhin wird das Markierungsfenster geöffnet. Zeichnen Sie Rahmen, und weisen Sie Tags für die Logos in jedem Bild zu. 

![Markieren von Logos auf der Custom Vision-Website](media/azure-logo-tutorial/tag-logos.png)

Die App ist für die Verwendung mit bestimmten Tagzeichenfolgen konfiguriert. Sie finden die Definitionen in der Datei *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Navigieren Sie nach dem Markieren eines Bilds nach rechts, um das nächste Bild zu markieren. Schließen Sie das Markierungsfenster, wenn Sie fertig sind.

## <a name="train-the-object-detector"></a>Trainieren der Objekterkennung

Legen Sie im linken Bereich den Schalter **Tags** auf **Markiert** fest, um Ihre Bilder anzuzeigen. Wählen Sie anschließend die grüne Schaltfläche am oberen Rand der Seite, um das Modell zu trainieren. Der Algorithmus wird darauf trainiert, die gleichen Tags in neuen Bildern zu erkennen. Zudem wird das Modell anhand einiger Ihrer vorhandenen Bilder getestet, um Genauigkeitsbewertungen zu generieren.

![Custom Vision-Website mit Registerkarte „Training Images“ (Trainingsbilder). In diesem Screenshot ist die Schaltfläche „Train“ (Trainieren) hervorgehoben.](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Abrufen der Vorhersage-URL

Nachdem Sie das Modell trainiert haben, können Sie es in Ihre App integrieren. Sie müssen die Endpunkt-URL (die Adresse des Modells, das von der App abgefragt wird) und den Vorhersageschlüssel (um der App Zugriff auf Vorhersageanforderungen zu gewähren) abrufen. Wählen Sie auf der Registerkarte **Leistung** oben auf der Seite die Schaltfläche **Prediction URL** (Vorhersage-URL).

![Custom Vision-Website mit dem Fenster für die Vorhersage-API, in dem eine URL-Adresse und ein API-Schlüssel angezeigt werden](media/azure-logo-tutorial/cusvis-endpoint.png)

Kopieren Sie die Endpunkt-URL und den Wert **Prediction-Key** in die entsprechenden Felder in der Datei *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Überprüfen der Custom Vision-Nutzung

Öffnen Sie die Datei *Source/VisualProvision/Services/Recognition/CustomVisionService.cs*, um zu überprüfen, wie Ihr Custom Vision-Schlüssel und Ihre Endpunkt-URL von der App verwendet werden. Die **PredictImageContentsAsync**-Methode akzeptiert einen Bytestream einer Bilddatei sowie ein Abbruchtoken (für die asynchrone Aufgabenverwaltung), ruft die Custom Vision-Vorhersage-API auf und gibt das Ergebnis der Vorhersage zurück. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Dieses Ergebnis wird in Form einer **PredictionResult**-Instanz zurückgegeben, die eine Liste von **Vorhersageinstanzen** enthält. Eine **Vorhersage** enthält ein erkanntes Tag und den zugehörigen Begrenzungsrahmen für die Position im Bild.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Beginnen Sie mit der **GetResourcesAsync**-Methode, um weitere Informationen zur Verarbeitung dieser Daten mit der App zu erhalten. Diese Methode ist in der Datei *Source/VisualProvision/Services/Recognition/RecognitionService.cs* definiert.  

## <a name="add-computer-vision"></a>Hinzufügen von maschinellem Sehen

Der Custom Vision-Teil des Tutorials ist fertig. Wenn Sie die App ausführen möchten, müssen Sie auch den Dienst „Maschinelles Sehen“ integrieren. Die App nutzt das Feature für die Texterkennung per maschinellem Sehen als Erweiterung für den Logoerkennungsprozess. Ein Azure-Logo kann anhand seines Aussehens *oder* des in der Nähe gedruckten Texts erkannt werden. Anders als bei Custom Vision-Modellen wird das maschinelle Sehen vorab trainiert, um bestimmte Vorgänge für Bilder oder Videos durchzuführen.

Abonnieren Sie den Dienst für maschinelles Sehen, um einen Schlüssel und eine Endpunkt-URL zu erhalten. Hilfe zu diesem Schritt finden Sie unter [Gewusst wie: Erhalten von Abonnementschlüsseln](../cognitive-services-apis-create-account.md?tabs=singleservice%2Cwindows).

![Dienst „Maschinelles Sehen“ im Azure-Portal mit ausgewähltem Schnellstartmenü. Ein Link für Schlüssel und die API-Endpunkt-URL sind hervorgehoben.](media/azure-logo-tutorial/comvis-keys.png)

Öffnen Sie als Nächstes die Datei *Source\VisualProvision\AppSettings.cs*, und füllen Sie die Variablen `ComputerVisionEndpoint` und `ComputerVisionKey` mit den richtigen Werten auf.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Die App erfordert ein Azure-Dienstprinzipalkonto, um Dienste in Ihrem Azure-Abonnement bereitzustellen. Mit einem Dienstprinzipal können Sie mithilfe der rollenbasierten Zugriffssteuerung in Azure bestimmte Berechtigungen an eine App delegieren. Weitere Informationen finden Sie unter [Gewähren des Anwendungszugriffs auf Azure Stack-Ressourcen durch Erstellen von Dienstprinzipalen](/azure-stack/operator/azure-stack-create-service-principals).

Sie können einen Dienstprinzipal wie im Folgenden beschrieben mit Azure Cloud Shell oder mit der Azure CLI erstellen. Melden Sie sich zunächst an, und wählen Sie das gewünschte Abonnement aus.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Erstellen Sie anschließend Ihren Dienstprinzipal. (Dieser Vorgang kann einige Zeit dauern.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Nach der erfolgreichen Erstellung sollte die folgende JSON-Ausgabe mit den erforderlichen Anmeldeinformationen angezeigt werden.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Notieren Sie sich die Werte von `clientId` und `tenantId`. Kopieren Sie die Werte in die entsprechenden Felder in der Datei *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Ausführen der App

An diesem Punkt haben Sie der App Zugriff auf Folgendes gewährt:

- Ein trainiertes Custom Vision-Modell
- Den Dienst für maschinelles Sehen
- Ein Dienstprinzipalkonto

Gehen Sie folgendermaßen vor, um die App auszuführen:

1. Wählen Sie im Projektmappen-Explorer von Visual Studio entweder das Projekt **VisualProvision.Android** oder **VisualProvision.iOS** aus. Wählen Sie in der Hauptsymbolleiste im Dropdownmenü einen entsprechenden Emulator oder ein verbundenes mobiles Gerät aus. Führen Sie anschließend die App aus.

    > [!NOTE]
    > Sie benötigen ein macOS-Gerät, um einen iOS-Emulator auszuführen.

1. Geben Sie auf dem ersten Bildschirm die Client-ID Ihres Dienstprinzipals, die Mandanten-ID und das Kennwort ein. Wählen Sie die Schaltfläche **Anmelden**.

    > [!NOTE]
    > In einigen Emulatoren wird die Schaltfläche **Anmelden** in diesem Schritt ggf. nicht aktiviert. Beenden Sie in diesem Fall die App, öffnen Sie die Datei *Source/VisualProvision/Pages/LoginPage.xaml*, suchen Sie nach dem `Button`-Element mit der Bezeichnung **LOGIN BUTTON**, und entfernen Sie die folgende Zeile, bevor Sie die App erneut ausführen.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![App-Bildschirm mit Feldern für die Anmeldeinformationen des Dienstprinzipals](media/azure-logo-tutorial/app-credentials.png)

1. Wählen Sie auf dem nächsten Bildschirm im Dropdownmenü Ihr Azure-Abonnement aus. (Dieses Menü sollte alle Abonnements enthalten, auf die Ihr Dienstprinzipal Zugriff hat.) Wählen Sie die Schaltfläche **Weiter**. Die App fordert Sie nun ggf. auf, den Zugriff auf die Kamera und den Fotospeicher des Geräts zu gewähren. Erteilen Sie die Zugriffsberechtigungen.

    ![App-Bildschirm mit einem Dropdownfeld für das Azure-Zielabonnement](media/azure-logo-tutorial/app-az-subscription.png)


1. Die Kamera auf Ihrem Gerät wird aktiviert. Nehmen Sie ein Foto von einem der Azure-Dienstlogos auf, die Sie trainiert haben. Sie sollten nun in einem Bereitstellungsfenster aufgefordert werden, eine Region und eine Ressourcengruppe für die neuen Dienste auszuwählen (wie bei der Bereitstellung über das Azure-Portal). 

    ![Smartphone-Kamerabildschirm, der auf zwei Ausschnitte von Azure-Logos ausgerichtet ist](media/azure-logo-tutorial/app-camera-capture.png)

    ![App-Bildschirm mit Feldern für die Bereitstellungsregion und Ressourcengruppe](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie alle Schritte dieses Szenarios ausgeführt und die App zum Bereitstellen von Azure-Diensten für Ihr Konto verwendet haben, können Sie als Nächstes zum [Azure-Portal](https://ms.portal.azure.com/) navigieren. Kündigen Sie dort die Dienste, die Sie nicht nutzen möchten.

Wenn Sie die Erstellung eines eigenen Objekterkennungsprojekts mit Custom Vision planen, kann es ratsam sein, das Logoerkennungsprojekt zu löschen, das Sie in diesem Tutorial erstellt haben. Im kostenlosen Abonnement von Custom Vision können nur zwei Projekte erstellt werden. Öffnen Sie zum Löschen des Logoerkennungsprojekts auf der [Custom Vision-Website](https://customvision.ai) die Option **Projects** (Projekte), und wählen Sie dann unter **My New Project** (Mein neues Projekt) das Papierkorbsymbol.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine voll funktionsfähige Xamarin.Forms-App eingerichtet und kennen gelernt, die den Custom Vision Service zum Erkennen von Logos in Bildern von der Kamera eines mobilen Geräts verwendet. Befassen Sie sich als Nächstes mit den bewährten Methoden für das Erstellen eines Custom Vision-Modells, damit Sie eine eigene leistungsfähige App mit hoher Genauigkeit erstellen können.

> [!div class="nextstepaction"]
> [Verbessern der Klassifizierung](getting-started-improving-your-classifier.md)