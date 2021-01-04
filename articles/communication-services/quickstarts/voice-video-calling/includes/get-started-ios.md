---
title: Schnellstart – Hinzufügen von Telefonie zu einer iOS-App mithilfe von Azure Communication Services
description: In diesem Schnellstart erfahren Sie, wie Sie die Clientbibliothek für Telefonie von Azure Communication Services für iOS verwenden.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5f604847faf01d1b267e6cbb73481d57ef397bd9
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95556897"
---
In diesem Schnellstart erfahren Sie, wie Sie einen Anruf mithilfe der Clientbibliothek für Telefonie von Azure Communication Services für iOS beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Einen Mac mit [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) zusammen mit einem gültigen in Ihrer Keychain installierten Entwicklerzertifikat.
- Eine bereitgestellte Communication Services-Ressource. [Erstellen einer Communication Services-Ressource](../../create-communication-resource.md)
- Ein [Benutzerzugriffstoken](../../access-tokens.md) für Ihren Azure Communication Service

## <a name="setting-up"></a>Einrichten

### <a name="creating-the-xcode-project"></a>Erstellen des Xcode-Projekts

Erstellen Sie in Xcode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View App** (Einzelansicht-App) aus. In diesem Tutorial wird das [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/) verwendet, weshalb Sie **Language** (Sprache) auf **Swift** und **User Interface** (Benutzeroberfläche) auf **SwiftUI** festlegen müssen. Im Rahmen dieses Schnellstarts werden keine Tests erstellt. Sie können die Option **Tests einschließen** daher deaktivieren.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot des Fensters „Neues Projekt“ in Xcode":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installieren des Pakets und der Abhängigkeiten mit CocoaPods

1. Erstellen Sie wie folgt eine Podfile für die Anwendung:

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. Führen Sie `pod install` aus.
3. Öffnen Sie `.xcworkspace` mit Xcode.

### <a name="request-access-to-the-microphone"></a>Anfordern des Zugriffs auf das Mikrofon

Damit Sie auf das Mikrofon des Geräts zugreifen zu können, müssen Sie die Liste der Informationseigenschaften Ihrer App mit `NSMicrophoneUsageDescription` aktualisieren. Legen Sie den zugehörigen Wert auf eine Zeichenfolge (`string`) fest. Diese wird in den Dialog aufgenommen, mit dem das System um den Zugriff beim Benutzer anfordert.

Klicken Sie mit der rechten Maustaste auf den Eintrag `Info.plist` der Projektstruktur, und wählen Sie anschließend **Open As** (Öffnen als)  > **Source Code** (Quellcode) aus. Fügen Sie die folgenden Zeilen im Abschnitt `<dict>` der obersten Ebene hinzu, und speichern anschließend Sie die Datei.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Öffnen Sie die Datei **ContentView.swift** Ihres Projekts, und fügen Sie am Anfang der Datei eine `import`-Deklaration hinzu, um `AzureCommunicationCalling library` zu importieren. Importieren Sie außerdem `AVFoundation`, was wir für die Berechtigungsanforderung für Audio im Code benötigen.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Ersetzen Sie die Implementierung der `ContentView`-Struktur durch einige einfache Benutzeroberflächen-Steuerelemente, die einem Benutzer das Initiieren und Beenden eines Anrufs ermöglichen. In diesem Schnellstart fügen wir eine Geschäftslogik an diese Steuerelemente an.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features der Azure Communication Services-Clientbibliothek „Calling“:

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | „CallClient“ ist der Haupteinstiegspunkt der Clientbibliothek für Telefonie.|
| ACSCallAgent | „CallAgent“ dient zum Starten und Verwalten von Anrufen. |
| CommunicationUserCredential | „CommunicationUserCredential“ dient als tokengestützte Anmeldeinformation zum Instanziieren von „CallAgent“.| 
| CommunicationIdentifier | „CommunicationIdentifier“ wird zur Darstellung der Identität des Benutzers verwendet, wie u. a. die folgenden: CommunicationUser/PhoneNumber/CallingApplication. |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Initialisieren Sie eine `CallAgent`-Instanz mit einem Benutzerzugriffstoken, das es uns ermöglicht, Anrufe zu tätigen und zu empfangen. Fügen Sie den folgenden Code zum `onAppear`-Rückruf in **ContentView.swift** hinzu:

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Sie müssen `<USER ACCESS TOKEN>` durch ein gültiges Benutzerzugriffstoken für Ihre Ressource ersetzen. Wenn Sie noch über kein Token verfügen, finden Sie unter [Benutzerzugriffstoken](../../access-tokens.md) weitere Informationen.

## <a name="start-a-call"></a>Beginnen eines Anrufs

Die Methode `startCall` wird als die Aktion festgelegt, die ausgeführt wird, wenn auf die Schaltfläche *Start Call* (Anruf beginnen) getippt wird. Aktualisieren Sie die Implementierung, um einen Anruf mit `ASACallAgent` zu beginnen:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: StartCallOptions())
        }
    }
}
```

Sie können auch die Eigenschaften in `StartCallOptions` verwenden, um die Anfangsoptionen für den Anruf festzulegen (d. h. Sie können den Anruf mit stummgeschaltetem Mikrofon beginnen).

## <a name="end-a-call"></a>Beenden eines Anrufs

Implementieren Sie die Methode `endCall`, um den aktuellen Anruf zu beenden, wenn auf die Schaltfläche *End Call* (Anruf beenden) getippt wird.

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Ausführen des Codes

Sie können Ihre App auf dem iOS-Simulator erstellen und ausführen, indem Sie **Product** (Produkt)  > **Run** (Ausführen) auswählen oder die Tastenkombination (&#8984;-R) verwenden.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Ergebnis der Schnellstart-App":::

Sie können einen ausgehenden VoIP-Anruf tätigen, indem Sie eine Benutzer-ID im Textfeld eingeben und auf die Schaltfläche **StartCall** (Anruf beginnen) tippen. Wenn Sie `8:echo123` anrufen, werden Sie mit einem Echobot verbunden. Dies eignet sich hervorragend für die ersten Schritte und zum Überprüfen, ob Ihre Audiogeräte funktionieren. 

> [!NOTE]
> Wenn Sie zum ersten Mal einen Anruf tätigen, fordert das System Zugriff auf das Mikrofon an. Verwenden Sie in einer Produktionsanwendung die `AVAudioSession`-API[, um den Berechtigungsstatus zu überprüfen](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) und das Verhalten Ihrer Anwendung entsprechend zu aktualisieren, wenn die Berechtigung nicht erteilt wird.

## <a name="sample-code"></a>Beispielcode

Sie können die Beispiel-App von [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling) herunterladen.
