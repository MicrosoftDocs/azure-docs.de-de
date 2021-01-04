---
title: 'Tutorial: Erstellen einer Übersetzungs-App mit WPF, C# – Translator'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial erstellen Sie eine WPF-App, um Textübersetzung, Spracherkennung und Rechtschreibprüfung mit einem einzelnen Abonnementschlüssel durchzuführen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: devx-track-csharp
ms.openlocfilehash: d239b89aaf0bc140916d38583f4263f7bf660f1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023615"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Tutorial: Erstellen einer Übersetzungs-App mit WPF

In diesem Tutorial erstellen Sie eine WPF-App ([Windows Presentation Foundation](/visualstudio/designers/getting-started-with-wpf?view=vs-2019)), bei der Azure Cognitive Services für die Textübersetzung, Spracherkennung und Rechtschreibprüfung mit nur einem Abonnementschlüssel verwendet werden. Ihre App ruft APIs für Translator und die [Bing-Rechtschreibprüfung](https://azure.microsoft.com/services/cognitive-services/spell-check/) auf.

Was ist WPF? Hierbei handelt es sich um ein Framework für Benutzeroberflächen, mit dem Desktopclient-Apps erstellt werden. Die WPF-Entwicklungsplattform unterstützt viele verschiedene Features für die App-Entwicklung, z. B. ein App-Modell, Ressourcen, Steuerelemente, Grafiken, Layout, Datenbindung, Dokumente und Sicherheit. Hierbei handelt es sich um eine Teilmenge von .NET Framework. Falls Sie also bereits Apps mit .NET Framework unter Verwendung von ASP.NET oder Windows Forms entwickelt haben, sollte Ihnen die Programmierumgebung vertraut sein. Für WPF wird XAML (Extensible App Markup Language) genutzt, um ein deklaratives Modell für die App-Programmierung bereitzustellen. Dieses Modell wird in den nächsten Abschnitten beschrieben.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines WPF-Projekts in Visual Studio
> * Hinzufügen von Assemblys und NuGet-Paketen zu Ihrem Projekt
> * Erstellen der Benutzeroberfläche Ihrer App mit XAML
> * Verwenden von Translator zum Abrufen von Sprachen, Übersetzen von Text und Erkennen der Ausgangssprache
> * Verwenden der Bing-Rechtschreibprüfung-API zum Überprüfen Ihrer Eingabe und Verbessern der Genauigkeit von Übersetzungen
> * Ausführen Ihrer WPF-App

### <a name="cognitive-services-used-in-this-tutorial"></a>In diesem Tutorial genutzte Cognitive Services

Die Liste enthält die in diesem Tutorial verwendeten Cognitive Services. Verwenden Sie den Link, um für jedes Feature die API-Referenz zu durchsuchen.

| Dienst | Funktion | BESCHREIBUNG |
|---------|---------|-------------|
| Übersetzer | [Get Languages](./reference/v3-0-languages.md) | Ruft eine vollständige Liste mit den unterstützten Sprachen für die Textübersetzung ab. |
| Übersetzer | [Translate](./reference/v3-0-translate.md) | Übersetzt Text in mehr als 70 Sprachen. |
| Übersetzer | [Detect](./reference/v3-0-detect.md) | Erkennt die Sprache des Eingabetexts. Enthält eine Zuverlässigkeitsbewertung für die Erkennung. |
| Bing-Rechtschreibprüfung | [Spell Check](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Korrigiert Rechtschreibfehler, um die Genauigkeit der Übersetzung zu verbessern. |

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie fortfahren können, benötigen Sie Folgendes:

* Ein Azure Cognitive Services-Abonnement. [Beschaffen Sie einen Cognitive Services-Schlüssel](../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).
* Einen Windows-Computer
* [Visual Studio 2019](https://www.visualstudio.com/downloads/): Community oder Enterprise

> [!NOTE]
> Wir empfehlen Ihnen, das Abonnement für dieses Tutorial in der Region „USA, Westen“ zu erstellen. Andernfalls müssen Sie beim Durcharbeiten dieser Übung im Code Endpunkte und Regionen ändern.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Erstellen einer WPF-App in Visual Studio

Als Erstes richten wir unser Projekt in Visual Studio ein.

1. Öffnen Sie Visual Studio. Wählen Sie **Neues Projekt erstellen** aus.
1. Klicken Sie in **Neues Projekt erstellen** auf **WPF-App (.NET Framework)** . Sie können unter **Sprache** den Eintrag „C#“ auswählen, um die Optionen zu begrenzen.
1. Klicken Sie auf **Weiter**, und nennen Sie Ihr Projekt `MSTranslatorDemo`.
1. Legen Sie die Frameworkversion auf **.NET Framework 4.7.2** oder höher fest, und klicken Sie dann auf **Erstellen**.
   ![Eingeben des Namens und der Frameworkversion in Visual Studio](media/name-wpf-project-visual-studio.png)

Ihr Projekt wurde erstellt. Sie sehen, dass zwei Registerkarten geöffnet sind: `MainWindow.xaml` und `MainWindow.xaml.cs`. In diesem Tutorial fügen wir diesen beiden Dateien Code hinzu. Wir ändern `MainWindow.xaml` für die Benutzeroberfläche der App. Wir ändern `MainWindow.xaml.cs` für unsere Aufrufe von Translator und der Bing-Rechtschreibprüfung.
   ![Prüfen Ihrer Umgebung](media/blank-wpf-project.png)

Im nächsten Abschnitt fügen wir unserem Projekt Assemblys und ein NuGet-Paket hinzu, um weitere Funktionen zu erhalten, z.B. eine JSON-Analyse.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Hinzufügen von Verweisen und NuGet-Paketen zu Ihrem Projekt

Für unser Projekt benötigen wir einige .NET Framework-Assemblys und NewtonSoft.Json. Zum Installieren verwenden wir den NuGet-Paket-Manager.

### <a name="add-net-framework-assemblies"></a>Hinzufügen von .NET Framework-Assemblys

Wir fügen unserem Projekt Assemblys hinzu, um Objekte zu serialisieren und zu deserialisieren und HTTP-Anforderungen und -Antworten zu verwalten.

1. Suchen Sie im Projektmappen-Explorer von Visual Studio nach Ihrem Projekt. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Hinzufügen > Verweis** aus, um den **Verweis-Manager** zu öffnen.
1. Auf der Registerkarte **Assemblys** sind alle .NET Framework-Assemblys aufgelistet, die für Verweise verfügbar sind. Verwenden Sie die Suchleiste oben rechts, um nach Verweisen zu suchen.
   ![Hinzufügen von Assemblyverweisen](media/add-assemblies-2019.png)
1. Wählen Sie die folgenden Verweise für Ihr Projekt aus:
   * [System.Runtime.Serialization](/dotnet/api/system.runtime.serialization)
   * [System.Web](/dotnet/api/system.web)
   * System.Web.Extensions
   * [System.Windows](/dotnet/api/system.windows)
1. Nachdem Sie diese Verweise Ihrem Projekt hinzugefügt haben, können Sie auf **OK** klicken, um den **Verweis-Manager** zu schließen.

> [!NOTE]
> Weitere Informationen zu Assemblyverweisen finden Sie unter [Vorgehensweise: Hinzufügen und Entfernen von Verweisen mit dem Verweis-Manager](/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019).

### <a name="install-newtonsoftjson"></a>Installieren von NewtonSoft.Json

In unserer App wird NewtonSoft.Json verwendet, um JSON-Objekte zu deserialisieren. Verwenden Sie die hier angegebene Anleitung, um das Paket zu installieren.

1. Suchen Sie im Projektmappen-Explorer von Visual Studio nach Ihrem Projekt, und klicken Sie mit der rechten Maustaste darauf. Wählen Sie **NuGet-Pakete verwalten** aus.
1. Suchen Sie nach der Registerkarte **Durchsuchen**, und wählen Sie sie aus.
1. Geben Sie in der Suchleiste [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) ein.

    ![Suchen und Installieren von NewtonSoft.Json](media/nuget-package-manager.png)

1. Wählen Sie das Paket aus, und klicken Sie auf **Installieren**.
1. Schließen Sie die Registerkarte, nachdem die Installation abgeschlossen ist.

## <a name="create-a-wpf-form-using-xaml"></a>Erstellen eines WPF-Formulars per XAML

Für die Nutzung Ihrer App benötigen Sie eine Benutzeroberfläche. Per XAML erstellen wir ein Formular, mit dem Benutzer die Ausgangs- und Zielsprache auswählen und den zu übersetzenden Text eingeben können und über das die Übersetzungsausgabe angezeigt wird.

Wir sehen uns nun an, was erstellt werden soll.

![WPF-XAML-Benutzeroberfläche](media/translator-text-csharp-xaml.png)

Die Benutzeroberfläche enthält die folgenden Komponenten:

| Name | type | BESCHREIBUNG |
|------|------|-------------|
| `FromLanguageComboBox` | Kombinationsfeld | Zeigt eine Liste mit den Sprachen an, die von Microsoft Translator für Textübersetzungen unterstützt werden. Der Benutzer wählt die Ausgangssprache für die Übersetzung aus. |
| `ToLanguageComboBox` | Kombinationsfeld | Dient zum Auswählen der Zielsprache für die Übersetzung und zeigt die gleiche Sprachenliste wie `FromComboBox` an. |
| `TextToTranslate` | TextBox | Ermöglicht dem Benutzer das Eingeben des zu übersetzenden Texts. |
| `TranslateButton` | Taste | Verwenden Sie diese Schaltfläche, um Text zu übersetzen. |
| `TranslatedTextLabel` | Bezeichnung | Dient zum Anzeigen der Übersetzung. |
| `DetectedLanguageLabel` | Bezeichnung | Zeigt die erkannte Sprache des zu übersetzenden Texts an (`TextToTranslate`). |

> [!NOTE]
> Wir erstellen dieses Formular hier mit dem XAML-Quellcode, aber Sie können zum Erstellen des Formulars auch den Editor in Visual Studio nutzen.

Wir fügen den Code nun unserem Projekt hinzu.

1. Wählen Sie in Visual Studio die Registerkarte für `MainWindow.xaml`.
1. Kopieren Sie diesen Code in Ihr Projekt, und klicken Sie dann auf **Datei > „MainWindow.xaml“ speichern**, um Ihre Änderungen zu speichern.
   ```xaml
   <Window x:Class="MSTranslatorDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
Jetzt sollte eine Vorschau der App-Benutzeroberfläche in Visual Studio angezeigt werden. Sie sollte in etwa wie folgt aussehen.

Das ist alles. Ihr Formular ist fertig. Als Nächstes schreiben wir Code für die Nutzung der Textübersetzung und Bing-Rechtschreibprüfung.

> [!NOTE]
> Sie können dieses Formular auch anpassen oder ein eigenes Formular erstellen.

## <a name="create-your-app"></a>Erstellen Ihrer App

`MainWindow.xaml.cs` enthält den Code, mit dem unsere App gesteuert wird. In den nächsten Abschnitten fügen wir Code hinzu, um unsere Dropdownmenüs mit Daten zu füllen und einige APIs aufzurufen, die von Translator und der Bing-Rechtschreibprüfung verfügbar gemacht werden.

* Wenn das Programm startet und `MainWindow` instanziiert wird, wird die `Languages`-Methode von Translator aufgerufen, um unsere Dropdownmenüs mit der Sprachauswahl abzurufen und mit Daten zu füllen. Dies erfolgt einmal am Anfang jeder Sitzung.
* Nach dem Klicken auf die Schaltfläche **Übersetzen** werden die Sprachauswahl und der Text des Benutzers abgerufen, für die Eingabe wird eine Rechtschreibprüfung durchgeführt, und die Übersetzung und die erkannte Sprache werden dem Benutzer angezeigt.
  * Die `Translate`-Methode von Translator wird aufgerufen, um den Text unter `TextToTranslate` zu übersetzen. Dieser Aufruf enthält auch die Ausgangs- bzw. Zielsprache (`from` und `to`), die über die Dropdownmenüs ausgewählt wurde.
  * Die `Detect`-Methode von Translator wird aufgerufen, um die Sprache des Texts unter `TextToTranslate` zu ermitteln.
  * Die Bing-Rechtschreibprüfung wird verwendet, um `TextToTranslate` zu überprüfen und Rechtschreibfehler zu beheben.

Unser gesamtes Projekt ist in der `MainWindow : Window`-Klasse gekapselt. Wir beginnen, indem wir Code zum Festlegen Ihres Abonnementschlüssels hinzufügen, Endpunkte für Translator und die Bing-Rechtschreibprüfung deklarieren und die App initialisieren.

1. Wählen Sie in Visual Studio die Registerkarte für `MainWindow.xaml.cs`.
1. Ersetzen Sie die vorab aufgefüllten `using`-Anweisungen durch Folgendes.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. Suchen Sie nach der `MainWindow : Window`-Klasse, und ersetzen Sie sie durch diesen Code:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. Fügen Sie Ihren Cognitive Services-Abonnementschlüssel hinzu, und speichern Sie.

In diesem Codeblock haben wir zwei Membervariablen deklariert, die Informationen zu den verfügbaren Sprachen für die Übersetzung enthalten:

| Variable | type | BESCHREIBUNG |
|----------|------|-------------|
|`languageCodes` | Array von Zeichenfolgen |Dient zum Zwischenspeichern der Sprachcodes. Der Translator-Dienst identifiziert Sprachen mithilfe von Kurzcodes (etwa `en` für Englisch). |
|`languageCodesAndTitles` | Sortiertes Wörterbuch | Ordnet die Anzeigenamen auf der Benutzeroberfläche den in der API verwendeten Kurzcodes zu. Dabei wird die alphabetische Sortierung (ohne Berücksichtigung der Groß-/Kleinschreibung) eingehalten. |

Im Konstruktor `MainWindow` haben wir die Fehlerbehandlung dann mit `HandleExceptions` hinzugefügt. Diese Fehlerbehandlung stellt sicher, dass eine Warnung angezeigt wird, wenn eine Ausnahme nicht behandelt wird. Anschließend wird eine Überprüfung durchgeführt, um zu bestätigen, dass der angegebene Abonnementschlüssel 32 Zeichen lang ist. Ein Fehler wird ausgelöst, wenn der Schlüssel weniger oder mehr als 32 Zeichen enthält.

Falls Schlüssel vorhanden sind, die zumindest die richtige Länge haben, wird `InitializeComponent()` aufgerufen, um die Benutzeroberfläche zu initiieren. Hierzu wird die XAML-Beschreibung des App-Hauptfensters gesucht, geladen und instanziiert.

Abschließend haben wir Code zum Aufrufen von Methoden hinzugefügt, um Sprachen für die Übersetzung abzurufen und die Dropdownmenüs für die Benutzeroberfläche unserer App aufzufüllen. Keine Sorge. Wir kommen in Kürze zum Code hinter diesen Aufrufen.

## <a name="get-supported-languages"></a>Abrufen der unterstützten Sprachen

Übersetzer unterstützt derzeit mehr als 70 Sprachen. Da im Laufe der Zeit Unterstützung für neue Sprachen hinzugefügt wird, empfehlen wir Ihnen, die von Translator verfügbar gemachte Ressource „Languages“ aufzurufen, anstatt die Sprachenliste in Ihrer App hartzucodieren.

In diesem Abschnitt erstellen wir eine `GET`-Anforderung für die Ressource „Languages“ und geben an, dass für die Übersetzung eine Liste mit Sprachen verfügbar sein soll.

> [!NOTE]
> Mit der Ressource „Languages“ können Sie die Sprachunterstützung mit den folgenden Abfrageparametern filtern: „transliteration“ (Transliteration), „dictionary“ (Wörterbuch) und „translation“ (Übersetzung). Weitere Informationen finden Sie in der [API-Referenz](./reference/v3-0-languages.md).

Bevor wir fortfahren, sehen wir uns eine Beispielausgabe eines Aufrufs der Ressource „Languages“ an:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

Aus dieser Ausgabe können wir den Sprachcode und den Namen (`name`) einer bestimmten Sprache extrahieren. Für unsere App wird NewtonSoft.Json zum Deserialisieren des JSON-Objekts verwendet ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Wir setzen nun an dem Punkt fort, an dem wir im letzten Abschnitt aufgehört haben. Wir fügen unserer App eine Methode zum Abrufen der unterstützten Sprachen hinzu.

1. Öffnen Sie in Visual Studio eine Registerkarte für `MainWindow.xaml.cs`.
2. Fügen Sie Ihrem Projekt den folgenden Code hinzu:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Mit der `GetLanguagesForTranslate()`-Methode wird eine HTTP GET-Anforderung erstellt und der Abfragezeichenfolgen-Parameter `scope=translation` verwendet, um den Bereich der Anforderung auf die Sprachen zu beschränken, für die die Übersetzung unterstützt wird. Der `Accept-Language`-Header mit dem Wert `en` wird hinzugefügt, damit die unterstützten Sprachen auf Englisch zurückgegeben werden.

Die JSON-Antwort wird analysiert und in ein Wörterbuch konvertiert. Die Sprachcodes werden der Membervariablen `languageCodes` hinzugefügt. Die Schlüssel-Wert-Paare, die die Sprachcodes und die Anzeigenamen der Sprachen enthalten, werden in einer Schleife durchlaufen und der Membervariablen `languageCodesAndTitles` hinzugefügt. Die Dropdownmenüs im Formular enthalten zwar die Anzeigenamen, aber zum Anfordern der Übersetzung werden die Codes benötigt.

## <a name="populate-language-drop-down-menus"></a>Füllen der Dropdownmenüs für die Sprachen

Da die Benutzeroberfläche per XAML definiert wurde, müssen Sie für die Einrichtung nicht viel mehr tun und lediglich `InitializeComponent()` aufrufen. Der einzige erforderliche Schritt ist das Hinzufügen der Anzeigenamen der Sprachen zu den Dropdownmenüs **Translate from** (Übersetzen aus) und **Translate to** (Übersetzen in). Die `PopulateLanguageMenus()`-Methode fügt die Namen hinzu.

1. Öffnen Sie in Visual Studio eine Registerkarte für `MainWindow.xaml.cs`.
2. Fügen Sie diesen Code Ihrem Projekt unterhalb der `GetLanguagesForTranslate()`-Methode hinzu:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Mit dieser Methode wird das Wörterbuch `languageCodesAndTitles` durchlaufen, und jeder Schlüssel wird beiden Menüs hinzugefügt. Nachdem die Menüs aufgefüllt wurden, wird als Standardeinstellung für die Ausgangs- und Zielsprache **Detect** (Erkennen) bzw. **English** (Englisch) festgelegt.

> [!TIP]
> Ohne Standardauswahl für die Menüs kann der Benutzer auf **Translate** (Übersetzen) klicken, ohne zuerst eine Ausgangs- oder Zielsprache auszuwählen. Durch Festlegen von Standardwerten entsteht dieses Problem gar nicht erst.

Nachdem `MainWindow` initialisiert und die Benutzeroberfläche erstellt wurde, wird dieser Code erst ausgeführt, wenn auf die Schaltfläche **Translate** (Übersetzen) geklickt wird.

## <a name="detect-language-of-source-text"></a>Erkennen der Sprache des Quelltexts

Als Nächstes erstellen wir die Methode zum Erkennen der Sprache des Quelltexts (der in unserem Textbereich eingegebene Text) mit Translator. Der von dieser Anforderung zurückgegebene Wert wird später in unserer Übersetzungsanforderung verwendet.

1. Öffnen Sie in Visual Studio eine Registerkarte für `MainWindow.xaml.cs`.
2. Fügen Sie diesen Code Ihrem Projekt unterhalb der `PopulateLanguageMenus()`-Methode hinzu:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Mit dieser Methode wird eine HTTP-`POST`-Anforderung für die Ressource „Detect“ erstellt. Es wird nur ein Argument (`text`) verwendet und als Text der Anforderung übergeben. Später beim Erstellen unserer Übersetzungsanforderung wird der auf der Benutzeroberfläche eingegebene Text an diese Methode übergeben, damit die Spracherkennung durchgeführt werden kann.

Darüber hinaus wird mit dieser Methode auch die Zuverlässigkeitsbewertung der Antwort evaluiert. Wenn die Punktzahl größer als `0.5` ist, wird die erkannte Sprache auf unserer Benutzeroberfläche angezeigt.

## <a name="spell-check-the-source-text"></a>Rechtschreibprüfung für Quelltext

Nun erstellen wir eine Methode, mit der für den Quelltext anhand der Bing-Rechtschreibprüfungs-API eine Rechtschreibprüfung durchgeführt wird. Die Rechtschreibprüfung stellt sicher, dass Translator korrekte Texte liefert. Alle Korrekturen des Quelltexts werden mit unserer Übersetzungsanforderung übergeben, wenn auf die Schaltfläche **Translate** (Übersetzen) geklickt wird.

1. Öffnen Sie in Visual Studio eine Registerkarte für `MainWindow.xaml.cs`.
2. Fügen Sie diesen Code Ihrem Projekt unterhalb der `DetectLanguage()`-Methode hinzu:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Übersetzen von Text per Klick

Abschließend müssen wir eine Methode erstellen, die aufgerufen wird, wenn auf unserer Benutzeroberfläche auf die Schaltfläche **Translate** (Übersetzen) geklickt wird.

1. Öffnen Sie in Visual Studio eine Registerkarte für `MainWindow.xaml.cs`.
1. Fügen Sie diesen Code Ihrem Projekt unterhalb der `CorrectSpelling()`-Methode hinzu, und speichern Sie:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

Der erste Schritt ist das Abrufen der Ausgangs- und Zielsprache („from“ und „to“) sowie des Texts, der vom Benutzer in unser Formular eingegeben wurde. Wenn die Ausgangssprache auf **Detect** (Erkennen) festgelegt ist, wird `DetectLanguage()` aufgerufen, um die Sprache des Quelltexts zu bestimmen. Es kann sein, dass der Text in einer Sprache vorliegt, die von Translator nicht unterstützt wird. In diesem Fall sollte der Benutzer eine entsprechende Meldung erhalten und der Vorgang ohne Übersetzung des Texts beendet werden.

Wenn als Ausgangssprache Englisch angegeben oder erkannt wird, überprüfen Sie mithilfe von `CorrectSpelling()` die Rechtschreibung des Texts und wenden ggf. erforderliche Korrekturen an. Der korrigierte Text wird wieder in den Textbereich eingefügt, damit der Benutzer sieht, dass eine Korrektur vorgenommen wurde.

Der Code zum Übersetzen von Text sollte Ihnen vertraut vorkommen: Erstellen des URI, Erstellen einer Anforderung, Senden der Anforderung und Analysieren der Antwort. Das JSON-Array enthält unter Umständen mehr als ein Objekt für die Übersetzung, aber für unsere App wird nur ein Objekt benötigt.

Nach einer erfolgreichen Anforderung wird `TranslatedTextLabel.Content` durch die Übersetzung (`translation`) ersetzt und die Benutzeroberfläche aktualisiert, um den übersetzten Text anzuzeigen.

## <a name="run-your-wpf-app"></a>Ausführen Ihrer WPF-App

Fertig! Sie verfügen jetzt über eine funktionierende Übersetzungs-App, die per WPF erstellt wurde. Klicken Sie zum Ausführen Ihrer App in Visual Studio auf die Schaltfläche **Start**.

## <a name="source-code"></a>Quellcode

Der Quellcode für dieses Projekt ist auf GitHub verfügbar.

* [Erkunden des Quellcodes](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenz zu Microsoft Translator](./reference/v3-0-reference.md)