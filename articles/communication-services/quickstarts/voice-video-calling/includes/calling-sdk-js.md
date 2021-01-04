---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: ff9eca855269597477bc42a319c99c886576d92c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482656"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md).
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../access-tokens.md)
- Optional: Gehen Sie den Schnellstart [Erste Schritte beim Hinzufügen von Anruffunktionen zu einer Anwendung](../getting-started-with-calling.md) durch.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Verwenden Sie den Befehl `npm install`, um die Azure Communication Services-Clientbibliotheken „Calling“ und „Common“ für JavaScript zu installieren.

```console
npm install @azure/communication-common --save

npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features der Azure Communication Services-Clientbibliothek „Calling“:

| Name                             | Beschreibung                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient ist der Haupteinstiegspunkt in die Clientbibliothek „Calling“.                                                                       |
| CallAgent                        | CallAgent dient zum Starten und Verwalten von Anrufen.                                                                                            |
| AzureCommunicationUserCredential | Die AzureCommunicationUserCredential-Klasse implementiert die CommunicationUserCredential-Schnittstelle, die zum Instanziieren von CallAgent verwendet wird. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Initialisieren von CallClient, Erstellen von CallAgent und Zugreifen auf DeviceManager

Instanziieren Sie eine neue `CallClient`-Instanz. Sie können sie mit benutzerdefinierten Optionen wie eine Protokollierungsinstanz konfigurieren.
Nachdem ein `CallClient` instanziiert wurde, können Sie eine `CallAgent`-Instanz erstellen, indem Sie die `createCallAgent`-Methode für die `CallClient`-Instanz aufrufen. Dadurch wird ein `CallAgent`-Instanzobjekt asynchron zurückgegeben.
Die `createCallAgent`-Methode verwendet `CommunicationUserCredential` als Argument, das ein [Benutzerzugriffstoken](../../access-tokens.md) akzeptiert.
Um auf `DeviceManager` zugreifen zu können, muss zunächst eine callAgent-Instanz erstellt werden. Anschließend können Sie die `getDeviceManager`-Methode für die `CallClient`-Instanz ausführen, um DeviceManager abzurufen.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential);
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Tätigen eines ausgehenden Anrufs

Um einen Anruf zu erstellen und zu starten, müssen Sie eine der APIs für CallAgent aufrufen und einen Benutzer bereitstellen, den Sie über die Communication Services-Clientbibliothek „Administration“ erstellt haben.

Erstellung und Start des Anrufs erfolgen synchron. Die Anrufinstanz ermöglicht Ihnen das Abonnieren aller Ereignisse im Rahmen des Anrufs.

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Tätigen eines 1:1-Anrufs eines Benutzers oder eines 1:n-Anrufs mit Benutzern und Festnetznummern

Um einen Anruf mit einem anderen Communication Services-Benutzer zu tätigen, rufen Sie die Methode `call` für `callAgent` auf, und übergeben den CommunicationUser, den Sie [mit der Communication Services-Bibliothek „Administration“ erstellt haben](../../access-tokens.md).

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1:n-Anruf mit Benutzern und Festnetznummern

Um einen 1:n-Anruf an einen Benutzer und eine Nummer im Telefonfestnetz zu tätigen, müssen Sie für beide Angerufenen eine CommunicationUser-Instanz und eine Telefonnummer angeben.

Die Communication Services-Ressource muss so konfiguriert werden, dass Anrufe über das Telefonfestnetz möglich sind.
```js

const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callAgent.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-video-camera"></a>Tätigen eines 1:1-Anrufs mit Videokamera
> [!WARNING]
> Derzeit ist nicht mehr als ein ausgehender lokaler Videostream möglich.
Um einen Videoanruf zu tätigen, müssen Sie die lokalen Kameras mit der deviceManager-API `getCameraList` auflisten.
Sobald Sie die gewünschte Kamera ausgewählt haben, erstellen Sie damit eine `LocalVideoStream`-Instanz und übergeben diese innerhalb von `videoOptions` als Element innerhalb des `localVideoStream`-Arrays an die `call`-Methode.
Sobald die Anrufverbindung hergestellt ist, wird automatisch ein Videostream von der ausgewählten Kamera an andere Teilnehmer gesendet.
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf
Um einen neuen Gruppenanruf zu starten oder an einem laufenden Gruppenanruf teilzunehmen, müssen Sie die „join“-Methode aufrufen und ein Objekt mit einer `groupId`-Eigenschaft übergeben. Der Wert muss eine GUID sein.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

## <a name="call-management"></a>Anrufverwaltung

Sie können auf Anrufeigenschaften zugreifen und während eines Anrufs verschiedene Vorgänge ausführen, um Einstellungen für Video und Audio zu verwalten.

### <a name="call-properties"></a>Anrufeigenschaften
* Rufen Sie die eindeutige ID (Zeichenfolge) für diesen Anruf ab.
```js

const callId: string = call.id;

```

* Um mehr über andere Anrufteilnehmer zu erfahren, sehen Sie sich die `remoteParticipant`-Sammlung für die `call`-Instanz an: Das Array enthält `RemoteParticipant`-Listenobjekte
```js
const remoteParticipants = call.remoteParticipants;
```

* Die Identität des Anrufers bei einem eingehenden Anruf. „Identity“ ist einer der `Identifier`-Typen
```js

const callerIdentity = call.callerIdentity;

```

* Ruft den Zustand des Anrufs ab.
```js

const callState = call.state;

```
Gibt eine Zeichenfolge zurück, die den aktuellen Zustand eines Anrufs darstellt:
* None: ursprünglicher Anrufzustand
* Incoming: gibt an, dass ein Anruf eingeht, der entweder angenommen oder abgelehnt werden muss
* Connecting: anfänglicher Übergangszustand, sobald ein Anruf getätigt oder angenommen wird
* Ringing: für einen ausgehenden Anruf; gibt an, dass für den Anruf bei Remoteteilnehmern ein Klingeln ertönt; auf ihrer Seite ist der Status „Incoming“.
* EarlyMedia: gibt einen Zustand an, bei dem eine Ansage wiedergegeben wird, bevor der Anruf verbunden wird
* Connected: der Anruf wurde verbunden
* Hold: der Anruf wird gehalten; es werden keine Medien zwischen lokalem Endpunkt und Remoteteilnehmern übertragen
* Disconnecting: Übergangsstatus, ehe der Anruf in den Zustand „Disconnected“ wechselt.
* Disconnected: der Endzustand des Anrufs
   * Wenn die Netzwerkverbindung unterbrochen wird, wechselt der Status nach ungefähr 2 Minuten in „Disconnected“.


* Um zu ermitteln, warum ein bestimmter Anruf beendet wurde, überprüfen Sie die `callEndReason`-Eigenschaft.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Um festzustellen, ob es sich bei dem aktuellen Anruf um einen eingehenden Anruf handelt, prüfen Sie die `isIncoming`-Eigenschaft; sie gibt `Boolean` zurück.
```js
const isIncoming = call.isIncoming;
```

*  Um festzustellen, ob das aktuelle Mikrofon stummgeschaltet ist, prüfen Sie die `muted`-Eigenschaft; sie gibt `Boolean` zurück.
```js

const muted = call.isMicrophoneMuted;

```

* Um zu bestimmen, ob der Bildschirmübertragungs-Stream von einem bestimmten Endpunkt aus gesendet wird, überprüfen Sie die `isScreenSharingOn`-Eigenschaft; sie gibt `Boolean` zurück.
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Um aktive Videostreams zu untersuchen, überprüfen Sie die `localVideoStreams`-Sammlung; sie enthält `LocalVideoStream`-Objekte.
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Zum Stummschalten oder Aufheben der Stummschaltung des lokalen Endpunkts können Sie die asynchronen APIs `mute` und `unmute` verwenden:

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Starten und Beenden des Sendens von lokalem Video


Um ein Video zu starten, müssen Sie Kameras mit der `getCameraList`-Methode für das `deviceManager`-Objekt aufzählen. Erstellen Sie dann eine neue Instanz von `LocalVideoStream`, indem Sie die gewünschte Kamera als Argument an die `startVideo`-Methode übergeben:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Sobald Sie erfolgreich mit dem Senden von Video beginnen, wird eine `LocalVideoStream`-Instanz der `localVideoStreams`-Sammlung für eine Anrufinstanz hinzugefügt.

```js

call.localVideoStreams[0] === localVideoStream;

```

Um das lokale Video anzuhalten, übergeben Sie die `localVideoStream`-Instanz, die in der `localVideoStreams`-Sammlung verfügbar ist:

```js

await call.stopVideo(localVideoStream);

```

Sie können während des Sendens von Video auf ein anderes Kameragerät umschalten, indem Sie `switchSource` für eine `localVideoStream`-Instanz aufrufen:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```
### <a name="faq"></a>Häufig gestellte Fragen
 * Ändert sich der Status des Anrufs in „Disconnected“, wenn die Netzwerkverbindung unterbrochen wird?
    * Ja, wenn die Netzwerkverbindung länger als 2 Minuten unterbrochen wird, geht der Anruf in den Status „Disconnected“ über und wird beendet.

## <a name="remote-participants-management"></a>Verwaltung von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die `remoteParticipants`-Sammlung für eine Anrufinstanz verfügbar.

### <a name="list-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf
Die `remoteParticipants`-Sammlung gibt eine Liste der Remoteteilnehmer an einem angegebenen Anruf zurück:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Eigenschaften von Remoteteilnehmern
Einem Remoteteilnehmer sind eine Reihe von Eigenschaften und Sammlungen zugeordnet.

* Ruft den Bezeichner dieses Remoteteilnehmers ab.
„Identity“ ist einer der „Identifier“-Typen:
```js
const identifier = remoteParticipant.identifier;
//It can be one of:
// { communicationUserId: '<ACS_USER_ID'> } - object representing ACS User
// { phoneNumber: '<E.164>' } - object representing phone number in E.164 format
```

* Ruft den Zustand dieses Remoteteilnehmers ab.
```js

const state = remoteParticipant.state;
```
Folgende Werte sind hierfür möglich:
* Idle: Anfangszustand
* Connecting: Übergangszustand, während sich der Teilnehmer mit dem Anruf verbindet
* Connected: Teilnehmer ist mit dem Anruf verbunden
* Hold: Teilnehmer wird gehalten
* EarlyMedia: Ansage wird wiedergegeben, bevor der Teilnehmer mit dem Anruf verbunden ist
* Disconnected: In diesem Endzustand wird der Teilnehmer vom Anruf getrennt.
   * Wenn die Netzwerkverbindung des Remoteteilnehmers unterbrochen wird, ändert sich dessen Status nach etwa 2 Minuten in „Disconnected“.

Um zu erfahren, warum ein Teilnehmer den Anruf verlassen hat, prüfen Sie die `callEndReason`-Eigenschaft:
```js

const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Um zu prüfen, ob dieser Remoteteilnehmer stummgeschaltet ist oder nicht, prüfen Sie die `isMuted`-Eigenschaft; sie gibt `Boolean` zurück.
```js
const isMuted = remoteParticipant.isMuted;
```

* Um zu prüfen, ob dieser Remoteteilnehmer spricht oder nicht, prüfen Sie die `isSpeaking`-Eigenschaft; sie gibt `Boolean` zurück.
```js

const isSpeaking = remoteParticipant.isSpeaking;

```

* Um alle Videostreams zu prüfen, die ein bestimmter Teilnehmer im Rahmen dieses Anrufs sendet, sehen Sie sich die `videoStreams`-Sammlung an; sie enthält `RemoteVideoStream`-Objekte.
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Hinzufügen eines Teilnehmers zu einem Anruf

Um einen Teilnehmer einem Anruf hinzuzufügen (entweder als Benutzer oder Telefonnummer), können Sie `addParticipant` aufrufen.
Geben Sie einen der „Identifier“-Typen an.
Dadurch wird die Instanz des Remoteteilnehmers synchron zurückgegeben.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);
```

### <a name="remove-participant-from-a-call"></a>Entfernen eines Teilnehmers aus einem Anruf

Um einen Teilnehmer aus einem Anruf zu entfernen (entweder als Benutzer oder Telefonnummer), können Sie `removeParticipant` aufrufen.
Sie müssen einen der „Identifier“-Typen übergeben. Dieser wird asynchron aufgelöst, sobald der Teilnehmer aus dem Anruf entfernt wird.
Der Teilnehmer wird auch aus der `remoteParticipants`-Sammlung entfernt.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Rendern von Videostreams von Remoteteilnehmern

Um die Video- und Bildschirmübertragungs-Streams der Remoteteilnehmer aufzulisten, sehen Sie sich die `videoStreams`-Sammlungen an:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Zum Rendern eines `RemoteVideoStream` müssen Sie ein `isAvailableChanged`-Ereignis abonnieren.
Wenn sich die Eigenschaft `isAvailable` in `true` ändert, sendet ein Remoteteilnehmer einen Stream.
Sobald dies passiert, erstellen Sie eine neue Instanz von `Renderer` und anschließend mit der asynchronen `createView`-Methode eine neue `RendererView`-Instanz.  Sie können dann `view.target` an ein beliebiges Benutzeroberflächenelement anfügen.
Immer wenn sich die Verfügbarkeit eines Remotestreams ändert, können Sie wählen, ob Sie den gesamten Renderer, eine bestimmte `RendererView` zerstören oder sie behalten möchten, was jedoch zur Anzeige leerer Videobilder führt.

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Eigenschaften von Remotevideostreams
Für Remotevideostreams gibt es die folgenden Eigenschaften:

* `Id`: ID eines Remotevideostreams
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize`: Größe (Breite/Höhe) eines Remotevideostreams
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType`: kann „Video“ oder „ScreenSharing“ sein
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable`: gibt an, ob der Endpunkt des Remoteteilnehmers einen Stream aktiv sendet
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Methoden und Eigenschaften des Renderers

* Erstellt eine `RendererView`-Instanz, die später auf der Benutzeroberfläche der Anwendung angefügt werden kann, um Remotevideostreams zu rendern.
```js
renderer.createView()
```

* Löscht den Renderer und alle zugeordneten `RendererView`-Instanzen.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Methoden und Eigenschaften von RendererView
Beim Erstellen einer `RendererView` können Sie die Eigenschaften `scalingMode` und `mirrored` angeben.
Das Skalierungsmodus kann „Stretch“, „Crop“ oder „Fit“ sein. Wenn `Mirrored` angegeben ist, wird der gerenderte Stream vertikal gespiegelt.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Eine `RendererView`-Instanz verfügt über eine `target`-Eigenschaft, die die Renderingoberfläche darstellt. Diese muss auf der Benutzeroberfläche der Anwendung angefügt werden:
```js
document.body.appendChild(rendererView.target);
```

Sie können den Skalierungsmodus später aktualisieren, indem Sie die `updateScalingMode`-Methode aufrufen.
```js
view.updateScalingMode('Crop')
```
### <a name="faq"></a>Häufig gestellte Fragen
* Ändert sich der Status eines Remoteteilnehmers in „Disconnected“, wenn seine Netzwerkverbindung unterbrochen wird?
    * Ja, wenn die Netzwerkverbindung eines Remoteteilnehmers mehr als 2 Minuten lang unterbrochen ist, ändert sich sein Status in „Disconnected“, und er wird aus dem Anruf entfernt.
## <a name="device-management"></a>Geräteverwaltung

`DeviceManager` ermöglicht Ihnen das Aufzählen lokaler Geräte, die in einem Anruf zur Übertragung Ihrer Audio-/Videostreams verwendet werden können. DeviceManager erlaubt Ihnen auch, von einem Benutzer die Berechtigung für den Zugriff auf sein Mikrofon und seine Kamera über die native Browser-API anzufordern.

Sie können auf `deviceManager` zugreifen, indem Sie die `callClient.getDeviceManager()`-Methode aufrufen.
> [!WARNING]
> Derzeit muss zuerst ein `callAgent`-Objekt instanziiert werden, um Zugriff auf DeviceManager zu erhalten.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Aufzählen lokaler Geräte

Für den Zugriff auf lokale Geräte können Sie Enumerationsmethoden für den Geräte-Manager verwenden. Enumeration ist ein synchroner Vorgang.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Festlegen des Standardmikrofons/-lautsprechers

Mit dem Geräte-Manager können Sie ein Standardgerät festlegen, das beim Starten eines Anrufs verwendet wird.
Wenn keine Clientstandardwerte festgelegt sind, nutzt Communication Services die Standardeinstellungen des Betriebssystems.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Vorschau auf lokaler Kamera

Sie können `DeviceManager` und `Renderer` verwenden, um mit dem Rendern von Streams über Ihre lokale Kamera zu beginnen. Dieser Stream wird nicht an andere Teilnehmer gesendet. Es handelt sich um einen lokalen Vorschaufeed. Dies ist ein asynchroner Vorgang.

```js
const localVideoDevice = deviceManager().getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Anfordern der Berechtigung für Kamera/Mikrofon

Fordern Sie den Benutzer auf, Berechtigungen für Kamera/Mikrofon mit Folgendem zu erteilen:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Wird asynchron mit einem Objekt aufgelöst, das angibt, ob die Berechtigungen `audio` und `video` erteilt wurden:
```js
console.log(result.audio);
console.log(result.video);
```

Sie können den aktuellen Berechtigungszustand für einen bestimmten Typ überprüfen, indem Sie `getPermissionState` aufrufen:

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>Ereignismodell

Sie können die meisten Objekte und Sammlungen abonnieren, um bei Änderungen von Werten benachrichtigt zu werden.

### <a name="properties"></a>Eigenschaften
So abonnieren Sie `property changed`-Ereignisse

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Auflistungen
So abonnieren Sie `collection updated`-Ereignisse

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
