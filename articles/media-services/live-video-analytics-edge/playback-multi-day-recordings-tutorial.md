---
title: 'Wiedergeben von mehrtägigen Aufzeichnungen: Azure'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Media Service-APIs verwenden, um eine mehrtägige fortlaufende Videoaufzeichnung wiederzugeben.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 81a778b40649c1318b3738a289f0db37fd35376a
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492788"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Tutorial: Wiedergeben von mehrtägigen Aufzeichnungen  

Dieses Tutorial baut auf dem Tutorial zur [fortlaufenden Videoaufzeichnung](continuous-video-recording-concept.md) (Continuous Video Recording, CVR) auf. In diesem Tutorial erfahren Sie, wie Sie Azure Media Service-APIs verwenden, um eine mehrtägige fortlaufende Videoaufzeichnung aus der Cloud wiederzugeben. 

Dies ist nützlich für Szenarien wie etwa in der öffentlichen Sicherheit, bei denen es erforderlich ist, eine Aufzeichnung des Bildmaterials einer Kamera für mehrere Tage (oder Wochen) aufzubewahren, und bei denen gelegentlich bestimmte Teile dieses Materials betrachtet werden müssen.

Das Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Ausführen der Beispiel-App, die veranschaulicht, wie Sie die Inhalte einer mehrtägigen Aufzeichnung durchsuchen können
> * Anzeigen ausgewählter Teile dieser Aufzeichnung

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Es empfiehlt sich, sich mit den folgenden Dokumentationsseiten vertraut zu machen:

* [Übersicht zu Live Video Analytics in IoT Edge](overview.md)
* [Terminologie zu Live Video Analytics in IoT Edge](terminology.md)
* [Mediengraph: Konzepte](media-graph-concept.md)
* [Fortlaufende Videoaufzeichnung](continuous-video-recording-concept.md) 
* [Leitfaden: Wiedergabe von Aufzeichnungen](playback-recordings-how-to.md)
* [Tutorial: Fortlaufende Videoaufzeichnung](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Voraussetzungen

* Schließen Sie das [CVR-Tutorial](continuous-video-recording-tutorial.md) ab. Während dieses Tutorial und die relevanten APIs, die im [Tutorial: Fortlaufende Videoaufzeichnung](continuous-video-recording-tutorial.md) erörtert werden, sich auf Aufzeichnungen von mindestens 5 Minuten Länge beziehen, empfiehlt es sich, dass Sie mindestens 5 Stunden Videomaterial aufzeichnen. Die zum Durchsuchen von Aufzeichnungen verwendeten APIs werden idealerweise mit langen Aufzeichnungen vorgeführt.
* Wir empfehlen Ihnen, dieses Tutorial auszuführen, während das [Tutorial: Fortlaufende Videoaufzeichnung](continuous-video-recording-tutorial.md) noch läuft – d. h., während Sie weiterhin Videomaterial in der Cloud aufzeichnen.

## <a name="run-the-sample"></a>Ausführen des Beispiels 

Im Rahmen des [CVR-Tutorials](continuous-video-recording-tutorial.md) haben Sie ein Media Service-Konto erstellt. Für dieses Tutorial benötigen Sie vollständigen API-Zugriff auf dieses Konto. Sie können mithilfe der Schritte in [Abrufen von Anmeldeinformationen für den Zugriff auf die Media Services-API](../latest/access-api-howto.md?tabs=portal) einen Dienstprinzipal erstellen. Sie sollten einen JSON-Block aus dem Azure-Portal abrufen können, der wie folgt aussieht:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Öffnen Sie dann in Visual Studio „src/ams-asset-player“. Dieser Ordner enthält die erforderlichen Dateien für dieses Tutorial. Öffnen Sie die Datei „appsettings.json“, und kopieren Sie den Inhalt in eine neue Datei: „appsettings.development.json“. Nehmen Sie an der neu erstellten Datei „appsettings.development.json“ die folgenden Änderungen vor:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG + UMSCHALT + X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. <!--In Visual Studio Code, you can click-->Klicken Sie auf das Ausführen-Symbol auf der linken Seite (oder drücken Sie STRG+UMSCHALT+D), um die Ausführung der verfügbaren Anwendungen zu starten:

    ![Screenshot: Menü in Visual Studio Code mit dem ausgewählten Ausführungselement](./media/playback-multi-day-recordings-tutorial/run.png)
1. Wählen Sie im Dropdownfeld die AMS Asset Player-Anwendung aus, wie unten dargestellt, und drücken Sie F5, um das Debuggen einzuleiten.

    ![Screenshot: Menü in Visual Studio Code mit ausgewählter AMS Asset Player-Anwendung](./media/playback-multi-day-recordings-tutorial/debug.png)

Die Beispielanwendung wird erstellt, startet Ihre Standardbrowser-App und öffnet die Seite des AMS Asset Players.

> [!NOTE]
> Abhängig von den Sicherheitseinstellungen in Ihrem Browser wird möglicherweise eine Warnmeldung angezeigt. Da die Webseite lokal ausgeführt wird, können Sie die Warnung ignorieren.

Der AMS Asset Player fordert Sie auf, den Namen eines Media Service-Medienobjekts einzugeben. Sie sollten den Namen des Medienobjekts verwenden, das Sie für die Aufzeichnung von Video im [Tutorial: Fortlaufende Videoaufzeichnung](continuous-video-recording-tutorial.md) verwendet haben.

Nach Eingabe des Medienobjektnamens und Klicken auf „Senden“ lädt der Code des Players die Streaming-URL. Weitere Informationen finden Sie im [Leitfaden: Wiedergabe von Aufzeichnungen](playback-recordings-how-to.md). Wenn Sie, wie empfohlen, immer noch in das Medienobjekt aufzeichnen, erkennt der Player dies und versucht, die Wiedergabe im aktuellsten Teil des aufgezeichneten Videoclips zu starten. Sie können den Zeitstempel (in UTC) oben links im Player sehen. Beachten Sie im nachfolgenden Screenshot, dass die Schaltfläche „Live“ aktiviert ist.

![STREAM](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
Auf der rechten Seite des Players werden die Steuerelemente zum Durchsuchen des Archivs angezeigt. Die Jahres-, Monats- und Datumswerte in diesem Steuerelement werden mithilfe der availableMedia-API aufgefüllt, die im [Leitfaden: Wiedergabe von Aufzeichnungen](playback-recordings-how-to.md) dokumentiert ist.
Wenn Sie den Tag aufklappen und das CVR-Tutorial schon seit mehreren Stunden ausgeführt wird, sehen Sie ein Ergebnis ähnlich dem folgenden:

![Archiv durchsuchen](./media/playback-multi-day-recordings-tutorial/results.png)

Die Quelle für den Videofeed im Tutorial ist eine MKV-Datei. Wenn der RSTP-Simulator (siehe [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) das Ende der Datei erreicht, beendet er den Datenstrom. Der RTSP-Quellknoten im Mediengraph erkennt dies und stellt die Verbindung wieder her. Daraufhin wird die Videowiedergabe fortgesetzt. Zwischen jedem dieser Dateienden und der erneuten Verbindung gibt es eine Lücke im Archiv der Aufzeichnungen, die in den availableMedia-Ergebnissen als neuer Eintrag erscheint.

![Ergebnisse](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Wenn Sie auf einen der Einträge in der Liste klicken, erstellt die Anwendung eine Streaming-URL mit dem passenden Filter, etwa „https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS)“. Der Player lädt diese URL, und die Wiedergabe beginnt an der gewünschten startTime.

Alternativ können Sie über die Steuerelemente am unteren Rand der Seite bestimmte Start- und Endzeiten eingeben. Sie können die Ergebnisse des Aufrufs von availableMedia, die auf der rechten Seite aufgeführt sind, als Leitfaden für die zulässigen Werte für Anfangs- und Endezeiten verwenden. Die Einschränkungen für die startTime- und endTime-Filter sind ausführlich im [Leitfaden: Wiedergabe von Aufzeichnungen](playback-recordings-how-to.md) dokumentiert. Nachdem Sie die Zeitwerte ausgewählt haben, lädt die Anwendung nach dem Klicken auf „Senden“ den Player mit einer Streaming-URL wie: https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS), und die Wiedergabe beginnt an der gewünschten startTime.

## <a name="next-steps"></a>Nächste Schritte

[Ereignisbasierte Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud](event-based-video-recording-tutorial.md)
