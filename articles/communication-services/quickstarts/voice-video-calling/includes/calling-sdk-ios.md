---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: a9af249aac18c847bf353f22b23ee67ab6e264c4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915515"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md).
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../access-tokens.md)
- Optional: Gehen Sie den Schnellstart [Erste Schritte beim Hinzufügen von Anruffunktionen zu einer Anwendung](../getting-started-with-calling.md) durch.

## <a name="setting-up"></a>Einrichten

### <a name="creating-the-xcode-project"></a>Erstellen des Xcode-Projekts

Erstellen Sie in Xcode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View App** aus. In diesem Schnellstart wird das [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/) verwendet, weshalb Sie **Language** auf **Swift** und **User Interface** auf **SwiftUI** festlegen müssen. Während dieses Schnellstarts werden keine Komponenten- oder Benutzeroberflächentests erstellt. Sie können daher **Include Unit Tests** und auch **Include UI Tests** deaktivieren.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot des Fensters „Create new project“ in Xcode":::

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

Damit Sie auf das Mikrofon des Geräts zugreifen zu können, müssen Sie die Liste der Informationseigenschaften Ihrer App mit `NSMicrophoneUsageDescription` aktualisieren. Legen Sie den zugehörigen Wert auf eine Zeichenfolge (`string`) fest. Dies wird in den Dialog aufgenommen wird, den das System verwendet, um den Zugriff beim Benutzer anzufordern.

Klicken Sie mit der rechten Maustaste auf den Eintrag `Info.plist` der Projektstruktur, und wählen Sie anschließend **Open As** (Öffnen als)  > **Source Code** (Quellcode) aus. Fügen Sie die folgenden Zeilen im Abschnitt `<dict>` der obersten Ebene hinzu, und speichern anschließend Sie die Datei.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Öffnen Sie die Datei **ContentView.swift** Ihres Projekts, und fügen Sie am Anfang der Datei eine `import`-Deklaration hinzu, um die `AzureCommunicationCalling library` zu importieren. Importieren Sie außerdem `AVFoundation`, was wir für die Berechtigungsanforderung für Audio im Code benötigen.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features der Azure Communication Services-Clientbibliothek „Calling“ für iOS.


| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient ist der Haupteinstiegspunkt in die Clientbibliothek „Calling“.|
| ACSCallAgent | ACSCallAgent dient zum Starten und Verwalten von Anrufen. |
| CommunicationUserCredential | „CommunicationUserCredential“ dient als tokengestützte Anmeldeinformation zum Instanziieren von „CallAgent“.| 
| CommunicationIndentifier | CommunicationIndentifier wird zur Darstellung der Identität des Benutzers verwendet, die eine der folgenden sein: CommunicationUser/PhoneNumber/CallingApplication. |

> [!NOTE]
> Bei der Implementierung von Ereignisdelegaten muss die Anwendung einen eindeutigen Verweis auf die Objekte enthalten, die Ereignisabonnements erfordern. Wenn beispielsweise ein `ACSRemoteParticipant`-Objekt beim Aufrufen der `call.addParticipant`-Methode zurückgegeben wird und die Anwendung den Delegaten auf das Lauschen auf `ACSRemoteParticipantDelegate` festlegt, muss die Anwendung einen eindeutigen Verweis auf das `ACSRemoteParticipant`-Objekt enthalten. Andernfalls löst der Delegat, wenn dieses Objekt erfasst wird, eine schwerwiegende Ausnahme aus, sobald das aufrufende SDK versucht, das Objekt aufzurufen.

## <a name="initialize-the-acscallagent"></a>Initialisieren von ACSCallAgent

Um eine `ACSCallAgent`-Instanz anhand von `ACSCallClient` zu erzeugen, müssen Sie die `callClient.createCallAgent`-Methode verwenden, die ein `ACSCallAgent`-Objekt asynchron zurückgibt, nachdem es initialisiert wurde.

Um einen Anrufclient zu erstellen, müssen Sie ein `CommunicationUserCredential`-Objekt übergeben.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Übergeben des zuvor erstellten CommunicationUserCredential-Objekts an ACSCallClient

```swift

callClient = CallClient()
callClient?.createCallAgent(with: userCredential!,
    completionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Tätigen eines ausgehenden Anrufs

Um einen Anruf zu erstellen und zu starten, müssen Sie eine der APIs für `ACSCallAgent` aufrufen und die Communication Services-Identität eines Benutzers angeben, den Sie über die Communication Services-Clientbibliothek „Administration“ bereitgestellt haben.

Erstellung und Start des Anrufs erfolgen synchron. Sie erhalten eine Anrufinstanz, die es Ihnen ermöglicht, alle Ereignisse im Zusammenhang mit dem Anruf zu abonnieren.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Tätigen eines 1:1-Anrufs eines Benutzers oder eines 1:n-Anrufs mit Benutzern und Festnetznummern

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1:n-Anruf mit Benutzern und Festnetznummern
Um den Anruf im Telefonfestnetz zu tätigen, müssen Sie die Telefonnummer angeben, die Sie mit Communication Services bezogen haben.
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Tätigen eines 1:1-Anrufs mit Video
Informationen zum Abrufen der Geräte-Manager-Instanz finden Sie [hier](#device-management).

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf
Um an einem Anruf teilzunehmen, müssen Sie eine der APIs für *CallAgent* aufrufen.

```swift

let groupCallContext = GroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: JoinCallOptions())

```

## <a name="push-notification"></a>Pushbenachrichtigung

Eine mobile Pushbenachrichtigung ist die Popupbenachrichtigung, die Sie auf dem Mobilgerät erhalten. Bei Anruffunktionen konzentrieren wir uns auf VoIP-Pushbenachrichtigungen (Voice over Internet Protocol). Wir bieten Ihnen die Möglichkeit, sich für Pushbenachrichtigungen zu registrieren, diese zu bearbeiten und ihre Registrierung aufzuheben.

### <a name="prerequisite"></a>Voraussetzungen

- Schritt 1: Xcode -> Signing & Capabilities -> Add Capability -> Push Notifications
- Schritt 2: Xcode -> Signing & Capabilities -> Add Capability -> Background Modes
- Schritt 3: Background Modes -> „Voice over IP“ und „Remote notifications“ auswählen

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Screenshot, der das Hinzufügen von Funktionen in Xcode zeigt" lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registrieren für Pushbenachrichtigungen

Um sich für Pushbenachrichtigungen zu registrieren, rufen Sie registerPushNotification() für eine *CallAgent*-Instanz mit einem Geräteregistrierungstoken auf.

Die Registrierung für die Pushbenachrichtigung muss nach erfolgreicher Initialisierung aufgerufen werden. Sobald das `callAgent`-Objekt zerstört wurde, wird `logout` aufgerufen, wodurch die Registrierung von Pushbenachrichtigungen automatisch aufgehoben wird.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken,
                completionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Behandlung von Pushbenachrichtigungen
Um Pushbenachrichtigungen für eingehende Anrufe zu empfangen, rufen Sie *handlePushNotification()* für eine *CallAgent*-Instanz mit Wörterbuchnutzdaten auf.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(payload: dictionaryPayload, completionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Aufheben der Registrierung der Pushbenachrichtigung

Anwendungen können die Registrierung der Pushbenachrichtigung jederzeit aufheben. Rufen Sie einfach die `unRegisterPushNotification`-Methode für *CallAgent* auf.
> [!NOTE]
> Die Registrierung von Anwendungen für Pushbenachrichtigungen wird bei der Abmeldung nicht automatisch aufgehoben.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Vorgänge im Verlauf des Anrufs

Sie können während eines Anrufs verschiedene Vorgänge ausführen, um Einstellungen für Video und Audio zu verwalten.

### <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Zum Stummschalten oder Aufheben der Stummschaltung des lokalen Endpunkts können Sie die asynchronen APIs `mute` und `unmute` verwenden:

```swift
call!.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

[Asynchron] Lokale Stummschaltung

```swift
call!.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Starten und Beenden des Sendens von lokalem Video

Um damit zu beginnen, lokale Videos an andere Anrufteilnehmer zu senden, verwenden Sie die `startVideo`-API, und übergeben Sie `localVideoStream` mit `camera`.

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Sobald Sie mit dem Senden von Video beginnen, wird die `ACSLocalVideoStream`-Instanz der `localVideoStreams`-Sammlung für eine Anrufinstanz hinzugefügt:

```swift

call.localVideoStreams[0]

```

[Asynchron] Um lokales Video zu beenden, übergeben Sie den `localVideoStream`, der vom Aufruf von `call.startVideo` zurückgegeben wurde:

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Verwaltung von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `ACSRemoteParticipant` dargestellt und sind über die `remoteParticipants`-Sammlung für eine Anrufinstanz verfügbar:

### <a name="list-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Eigenschaften von Remoteteilnehmern

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Hinzufügen eines Teilnehmers zu einem Anruf

Um einen Teilnehmer einem Anruf hinzuzufügen (entweder als Benutzer oder Telefonnummer), können Sie `addParticipant` aufrufen. Dadurch wird die Instanz eines Remoteteilnehmers synchron zurückgegeben.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Entfernen eines Teilnehmers aus einem Anruf
Um einen Teilnehmer aus einem Anruf zu entfernen (entweder als Benutzer oder Telefonnummer), können Sie die API `removeParticipant` aufrufen. Dieser Vorgang wird asynchron aufgelöst.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Rendern von Videostreams von Remoteteilnehmern

Remoteeilnehmer können während eines Anrufs eine Video- oder Bildschirmübertragung veranlassen.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Verarbeiten von Video-/Bildschirmübertragungs-Streams für Remoteteilnehmer

Prüfen Sie die `videoStreams`-Sammlungen, um die Streams von Remoteteilnehmern aufzulisten:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Eigenschaften von Remotevideostreams

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Rendern von Streams von Remoteteilnehmern

So beginnen Sie mit dem Rendern von Streams von Remoteteilnehmern

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Methoden und Eigenschaften für den Renderer für Remotevideo

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Geräteverwaltung

`DeviceManager` ermöglicht Ihnen das Aufzählen lokaler Geräte, die in einem Anruf zur Übertragung von Audio-/Videostreams verwendet werden können. Mithilfe des Geräte-Managers können Sie auch beim Benutzer die Berechtigung für den Zugriff auf Mikrofon/Kamera anfordern. Sie können im `callClient`-Objekt auf `deviceManager` zugreifen:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Aufzählen lokaler Geräte

Für den Zugriff auf lokale Geräte können Sie Enumerationsmethoden für den Geräte-Manager verwenden. Enumeration ist ein synchroner Vorgang.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Festlegen des Standardmikrofons/-lautsprechers

Mit dem Geräte-Manager können Sie ein Standardgerät festlegen, das beim Starten eines Anrufs verwendet wird. Wenn keine Stapelstandardwerte festgelegt sind, nutzt Communication Services die Standardeinstellungen des Betriebssystems.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Vorschau auf lokaler Kamera

Sie können mit `ACSRenderer` mit dem Rendern eines Streams von Ihrer lokalen Kamera beginnen. Dieser Stream wird nicht an andere Teilnehmer gesendet. Es handelt sich um einen lokalen Vorschaufeed. Dies ist ein asynchroner Vorgang.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Eigenschaften der Vorschau auf lokale Kamera

Der Renderer verfügt über eine Reihe von Eigenschaften und Methoden, mit denen Sie das Rendering steuern können:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Ereignismodell

Sie können die meisten Objekte und Sammlungen abonnieren, um bei Änderungen von Werten benachrichtigt zu werden.

### <a name="properties"></a>Eigenschaften
So abonnieren Sie `property changed`-Ereignisse

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Auflistungen
So abonnieren Sie `collection updated`-Ereignisse

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
