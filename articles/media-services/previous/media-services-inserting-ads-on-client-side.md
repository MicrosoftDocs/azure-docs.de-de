---
title: Einfügen von Anzeigen auf Clientseite | Microsoft Docs
description: In diesem Thema wird gezeigt, wie Anzeigen auf Clientseite eingefügt werden.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 49c836f5e9189104ba77e8f3d865f4db199c4060
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002974"
---
# <a name="inserting-ads-on-the-client-side"></a>Einfügen von Anzeigen auf Clientseite
Dieser Artikel enthält Informationen zum Einfügen verschiedener Typen von Anzeigen auf Clientseite.

Informationen zu Untertiteln und der Unterstützung von Werbeeinblendungen in Livestreaming-Videos finden Sie unter [Standards für unterstützte Untertitel und für Werbeeinblendungen](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player unterstützt derzeit keine Werbeeinblendungen.
> 
> 

## <a id="insert_ads_into_media"></a>Einfügen von Werbeeinblendungen in Ihre Medien
Azure Media Services bietet Unterstützung für das Einfügen von Werbeeinblendungen mithilfe der Windows Media-Plattform: Player-Frameworks. Player-Frameworks mit Unterstützung für Werbeeinblendungen sind für Windows 8, Silverlight, Windows Phone 8 und iOS-Geräte verfügbar. Jedes Player-Framework enthält Beispielcode, der zeigt, wie eine Playeranwendung implementiert werden kann. Es gibt drei verschiedene Arten von Anzeigen, die Sie in Ihre Medien einfügen können:

* **Linear** : Werbeeinblendungen auf dem kompletten Frame, die das Hauptvideo unterbrechen
* **Nicht linear** : Overlay-Werbeeinblendungen, die während der Wiedergabe angezeigt werden; normalerweise ein Logo oder ein anderes statisches Bild im Player
* **Begleitend** : Werbeeinblendungen, die außerhalb des Players angezeigt werden

Werbeeinblendungen können an jeder Stelle auf der Zeitachse des Hauptvideos platziert werden. Sie müssen den Player anweisen, welche Werbeeinblendungen wann wiedergegeben werden. Dies erfolgt mithilfe einer Reihe von Standarddateien im XML-Format: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) und Digital Video Player Ad Interface Definition (VPAID). VAST-Dateien geben die anzuzeigenden Werbeeinblendungen an. VMAP-Dateien geben an, wann die verschiedenen Werbeeinblendungen wiedergegeben werden, sie enthalten VAST-XML. MAST-Dateien sind eine weitere Möglichkeit zum Anordnen von Werbeeinblendungen, die auch VAST-XML enthalten können. VPAID-Dateien definieren eine Schnittstelle zwischen Video-Player und Werbeeinblendung oder Werbungsserver.

Jedes Player-Framework funktioniert anders und wird jeweils in einem eigenen Artikel behandelt. Dieser Artikel beschreibt die grundlegenden Mechanismen zum Einfügen von Werbeeinblendungen. Videoplayeranwendungen fordern Werbeeinblendungen von einem Werbeserver an. Der Adserver kann auf verschiedene Arten antworten:

* Rückgabe einer VAST-Datei
* Rückgabe einer VMAP-Datei (mit eingebettetem VAST)
* Rückgabe einer MAST-Datei (mit eingebettetem VAST)
* Rückgabe einer VAST-Datei mit VPAID-Werbung

### <a name="using-a-video-ad-service-template-vast-file"></a>Verwenden einer VAST-Datei (Video Ad Service Template)
Eine VAST-Datei gibt an, welche Werbeeinblendung bzw. Werbeeinblendungen angezeigt werden sollen. Der folgende XML-Code ist ein Beispiel für eine VAST-Datei für eine lineare Werbeeinblendung:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

Die lineare Werbeeinblendung wird durch das <**Linear**>-Element beschrieben. Es gibt die Dauer der Werbeeinblendung, Nachverfolgungsereignisse, die Durchklickrate, die Klickprotokollierung sowie eine Anzahl von **MediaFile**-Elementen an. Nachverfolgungsereignisse werden im <**TrackingEvents**>-Element angegeben und ermöglichen es einem Adserver, verschiedene Ereignisse zu verfolgen, die während der Anzeige der Werbung eintreten. In diesem Fall werden die Start-, Mittelpunkt-, Abschluss- und Erweiterungsereignisse nachverfolgt. Das Startereignis tritt ein, wenn die Anzeige angezeigt wird. Das Mittelpunktereignis tritt ein, wenn mindestens 50 % der Zeitleiste der Werbeeinblendung angezeigt wurden. Das Abschlussereignis tritt ein, wenn die Werbung bis zum Ende wiedergegeben wurde. Das Erweiterungsereignis tritt ein, wenn der Benutzer den Videoplayer in den Vollbildmodus erweitert. Durchklickraten werden in einem <**ClickThrough**>-Element innerhalb eines <**VideoClicks**>-Elements angegeben und geben einen URI zu einer Ressource an, die angezeigt werden soll, wenn der Benutzer auf die Anzeige klickt. Die Klickprotokollierung wird in einem <**ClickTracking**>-Element ebenfalls innerhalb des <**VideoClicks**>-Elements angegeben und gibt eine Nachverfolgungsressource an, die der Player anfordern soll, wenn der Benutzer auf die Anzeige klickt. Die <**MediaFile**>-Elemente geben Informationen zu einer bestimmten Codierung einer Werbeeinblendung an. Wenn mehrere <**MediaFile**>-Elemente vorhanden sind, kann der Videoplayer die beste Codierung für die Plattform auswählen.

Lineare Werbeeinblendungen können in einer bestimmten Reihenfolge angezeigt werden. Zu diesem Zweck fügen Sie der VAST-Datei zusätzliche `<Ad>`-Elemente hinzu und geben die Reihenfolge mit dem Reihenfolgeattribut an. Das folgende Beispiel veranschaulicht dies:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Nicht lineare Werbeeinblendungen werden ebenfalls in einem `<Creative>`-Element angegeben. Das folgende Beispiel zeigt ein `<Creative>`-Element, das eine nicht lineare Werbeeinblendung beschreibt.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

Das <**NonLinearAds**>-Element kann ein oder mehrere <**NonLinear**>-Elemente enthalten, von denen jedes eine nicht lineare Werbeeinblendung beschreiben kann. Das <**NonLinear**>-Element gibt die Ressource für die nicht lineare Werbeeinblendung an. Die Ressource kann vom Typ <**StaticResource**>, <**IFrameResource**> oder <**HTMLResource**> sein. \< **StaticResource** > beschreibt eine Nicht-HTML-Ressource und definiert ein creativeType-Attribut, das angibt, wie die Ressource angezeigt wird:

Image/gif, image/jpeg, image/png: Die Ressource wird in einem HTML-<**img**>-Tag angezeigt.

Application/x-javascript: Die Ressource wird in einem HTML-<**script**>-Tag angezeigt.

Application/x-shockwave-flash: Die Ressource wird in einem Flash-Player angezeigt.

**IFrameResource** beschreibt eine HTML-Ressource, die in einem IFrame angezeigt werden kann. **HTMLResource** beschreibt einen HTML-Code, der in eine Webseite eingefügt werden kann. **TrackingEvents** gibt Nachverfolgungsereignisse sowie den URI an, der angefordert wird, wenn das Ereignis eintritt. In diesem Beispiel werden die acceptInvitation- und collapse-Ereignisse nachverfolgt. Weitere Informationen zum **NonLinearAds**-Element und zu dessen untergeordneten Elementen finden Sie unter „IAB.NET/VAST“. Beachten Sie, dass sich das **TrackingEvents**-Element im **NonLinearAds**-Element und nicht im **NonLinear**-Element befindet.

Begleitende Werbeeinblendungen werden in einem `<CompanionAds>`-Element definiert. Das `<CompanionAds>`-Element kann ein oder mehrere `<Companion>`-Elemente enthalten. Jedes `<Companion>`-Element beschreibt eine begleitende Werbeeinblendung und kann eine `<StaticResource>`, `<IFrameResource>` oder `<HTMLResource>` enthalten, die auf die gleiche Weise wie bei einer nichtlinearen Werbeeinblendung angegeben werden. Eine VAST-Datei kann mehrere begleitende Werbeeinblendungen enthalten und die Playeranwendung kann die für die Anzeige am besten geeignete Werbeeinblendung auswählen. Weitere Informationen zu VAST finden Sie unter [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Verwenden einer VMAP-Datei (Digital Video Multiple Ad Playlist)
Mithilfe einer VMAP-Datei können Sie angeben, wann Werbepausen erfolgen, wie lange die einzelnen Pausen dauern, wie viele Anzeigen in einer Pause angezeigt werden können und welche Typen von Werbung während einer Pause angezeigt werden können. Im Folgenden finden Sie eine VMAP-Datei, die eine einzelne Werbepause definiert:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Eine VMAP-Datei beginnt mit einem `<VMAP>`-Element, das ein oder mehrere `<AdBreak>`-Elemente enthält, die jeweils eine Werbepause definieren. Jede Werbepause gibt einen Pausentyp, eine Pausen-ID und ein Zeitoffset an. Das breakType-Attribut gibt den Typ der Werbung an, die während der Pause wiedergegeben werden kann: linear, nicht linear oder Display-Anzeigen. Display-Anzeigen entsprechen begleitenden VAST-Werbeeinblendungen. Mehrere Werbungstypen können in einer durch Trennzeichen getrennten Liste (ohne Leerzeichen) angegeben werden. Die breakID ist ein optionaler Bezeichner für die Werbeeinblendung. Mit "timeOffset" wird angegeben, wann die Werbeeinblendung angezeigt werden soll. Dies kann auf eine der folgenden Arten angegeben werden:

1. Zeit: Im Format "hh:mm:ss" oder "hh:mm:ss.mmm", wobei ".mmm" Millisekunden sind. Der Wert dieses Attributs gibt die Zeit vom Anfang der Videozeitleiste bis zum Anfang der Werbepause an.
2. Prozentsatz: Im Format "n%", wobei "n" der Prozentsatz der Videozeitleiste ist, der vor der Wiedergabe der Werbeeinblendung abgespielt werden soll.
3. Start/Ende: Gibt an, dass eine Werbeeinblendung vor oder nach der Wiedergabe des Videos angezeigt werden soll.
4. Position: Gibt die Reihenfolge von Werbepausen an, wenn der Zeitpunkt der Pausen nicht bekannt ist, z. B. beim Livestreaming. Die Reihenfolge der einzelnen Werbepausen wird im Format "#n" angegeben, wobei "n" die ganze Zahl "1" oder größer ist. "1" bedeutet, dass die Werbeeinblendung bei der ersten Gelegenheit wiedergegeben werden soll, "2" gibt an, dass die Werbeeinblendung bei der zweiten Gelegenheit wiedergegeben werden soll usw.

Innerhalb des `<AdBreak>`-Elements kann sich ein <**AdSource**>-Element befinden. Das <**AdSource**>-Element enthält die folgenden Attribute:

1. Id: Gibt einen Bezeichner der Werbeeinblendungsquelle an.
2. allowMultipleAds: Ein boolescher Wert, der angibt, ob mehrere Werbeeinblendungen während der Werbepause angezeigt werden können.
3. followRedirects: Ein optionaler boolescher Wert, der angibt, ob der Videoplayer Weiterleitungen innerhalb einer Anzeigenantwort berücksichtigen soll.

Das <**AdSource**>-Element stellt dem Player eine Inline-Anzeigenantwort oder einen Verweis auf eine Anzeigenantwort bereit. Es kann eines der folgenden Elemente enthalten:

* `<VASTAdData>`: Gibt an, dass eine VAST-Anzeigenantwort in der VMAP-Datei eingebettet ist.
* `<AdTagURI>`: Ein URI, der auf eine Anzeigenantwort aus einem anderen System verweist.
* `<CustomAdData>`: Eine beliebige Zeichenfolge, die eine nicht-VAST-Antwort darstellt.

In diesem Beispiel wird eine Inline-Anzeigenantwort mit einem `<VASTAdData>`-Element angegeben, das eine VAST-Anzeigenantwort enthält. Weitere Informationen zu den anderen Elementen finden Sie unter [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

Das <**AdBreak**>-Element kann außerdem ein <**TrackingEvents**>-Element enthalten. Das <**TrackingEvents**>-Element ermöglicht es Ihnen, den Start oder das Ende einer Werbepause zu verfolgen. Sie können auch verfolgen, ob während der Werbepause ein Fehler aufgetreten ist. Das <**TrackingEvents**>-Element enthält ein oder mehrere <**Tracking**>-Elemente, die jeweils ein Nachverfolgungsereignis und einen Nachverfolgungs-URI angeben. Mögliche Nachverfolgungsereignisse sind:

1. breakStart: Verfolgt den Anfang einer Werbepause.
2. breakEnd: Verfolgt den Abschluss einer Werbepause.
3. error: Verfolgt Fehler, die während der Werbepause aufgetreten sind.

Das folgende Beispiel zeigt eine VMAP-Datei, die Nachverfolgungsereignisse angibt.

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Weitere Informationen zum <**TrackingEvents**>-Element und zu dessen untergeordneten Elementen finden Sie unter http://iab.net/VMAP.pdf.

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Verwenden einer MAST-Datei (Media Abstract Sequencing Template)
Mithilfe einer MAST-Datei können Sie Trigger angeben, die definieren, wann eine Werbeeinblendung angezeigt wird. Im Folgenden finden Sie eine MAST-Beispieldatei, die Trigger für eine Pre-Roll-, eine Mid-Roll- und eine Post-Roll-Werbeeinblendung enthält.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Eine MAST-Datei beginnt mit einem **MAST**-Element, das ein **triggers**-Element enthält. Das `<triggers>`-Element enthält ein oder mehrere **trigger**-Elemente, die definieren, wann eine Werbeeinblendung abgespielt werden soll.

Das **trigger**-Element enthält ein **startConditions**-Element, das angibt, wann die Wiedergabe einer Werbeeinblendung beginnen soll. Das **startConditions**-Element enthält ein oder mehrere `<condition>`-Elemente. Wenn jede `<condition>` als „true“ ausgewertet wird, wird ein Trigger initiiert oder widerrufen, abhängig davon, ob die `<condition>` in einem **startConditions**-Element oder in einem **endConditions**-Element enthalten ist. Wenn mehrere `<condition>`-Elemente vorhanden sind, werden diese als implizit behandelt, ODER alle Bedingungen, die als „true“ ausgewertet werden, führen zur Initiierung des Triggers. `<condition>`-Elemente können geschachtelt werden. Wenn untergeordnete `<condition>`-Elemente vordefiniert werden, werden diese als implizit behandelt UND alle Bedingungen müssen "true" ergeben, damit der Auslöser initiiert wird. Das `<condition>`-Element enthält die folgenden Attribute, die die Bedingung definieren:

1. **type**: Gibt den Typ der Bedingung, des Ereignisses oder der Eigenschaft an.
2. **name**: Der Name der Eigenschaft oder des Ereignisses, die bzw. das während der Auswertung verwendet wird.
3. **value** : Der Wert, der für die Auswertung einer Eigenschaft verwendet wird.
4. **operator**: Der während der Auswertung zu verwendende Vorgang: EQ (gleich), NEQ (nicht gleich), GTR (größer als), GEQ (größer oder gleich), LT (kleiner als), LEQ (kleiner oder gleich), MOD (Modulo)

**endConditions** enthält auch `<condition>`-Elemente. Wenn eine Bedingung als wahr (true) ausgewertet wird, wird der Trigger zurückgesetzt. Das `<trigger>`-Element enthält darüber hinaus ein `<sources>`-Element mit mindestens einem `<source>`-Element. Die `<source>`-Elemente definieren den URI und den Typ der Anzeigenantwort. In diesem Beispiel wird einer VAST-Antwort ein URI zugewiesen.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Verwenden von VPAID (Video Player-Ad Interface Definition)
VPAID ist eine API, mit der die Kommunikation von ausführbaren Werbeeinheiten mit einem Videoplayer aktiviert wird. Dies ermöglicht hochgradig interaktive Werbeerfahrungen. Der Benutzer kann mit der Werbeeinblendung interagieren und die Werbeeinblendung kann auf Aktionen des Betrachters reagieren. Beispielsweise können in einer Werbeeinblendung Schaltflächen angezeigt werden, über die der Benutzer weitere Informationen oder eine längere Version der Werbeeinblendung anzeigen kann. Der Videoplayer muss die VPAID-API unterstützen und die ausführbare Werbeeinheit muss die API implementieren. Wenn ein Player eine Werbeeinblendung von einem Adserver anfordert, kann der Server mit einer VAST-Antwort antworten, die eine VPAID-Werbung enthält.

Im Code wird eine ausführbare Werbeeinheit erstellt, die in einer Laufzeitumgebung wie Adobe Flash™ oder JavaScript ausgeführt werden muss, die in einem Webbrowser ausgeführt werden kann. Wenn ein Adserver eine VAST-Antwort zurückgibt, die eine VPAID-Werbung enthält, muss der Wert des apiFramework-Attributs im `<MediaFile>`-Element "VPAID" sein. Dieses Attribut gibt an, dass die enthaltene Werbeeinheit eine ausführbare VPAID-Werbung ist. Das type-Attribut muss auf den MIME-Typ der ausführbaren Datei festgelegt werden, z. B. "application/x-shockwave-flash" oder "application/x-javascript". Der folgende XML-Codeausschnitt zeigt das `<MediaFile>`-Element aus einer VAST-Antwort, die eine ausführbare VPAID-Werbung enthält.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Eine ausführbare Werbung kann mit dem `<AdParameters>`-Element innerhalb des `<Linear>`-Elements oder des `<NonLinear>`-Elements in einer VAST-Antwort initialisiert werden. Weitere Informationen zum `<AdParameters>`-Element finden Sie unter [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf). Weitere Informationen über die VPAID-API finden Sie unter [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementieren eines Windows- oder Windows Phone 8-Players mit Unterstützung für Werbeeinblendungen
Microsoft Media-Plattform: Player Framework für Windows 8 und Windows Phone 8 enthält eine Sammlung von Beispielanwendungen, die zeigen, wie Sie eine Videoplayeranwendung mithilfe des Frameworks implementieren. Sie können Player Framework und die Beispiele unter [Player Framework for Windows 8 and Windows Phone 8](https://playerframework.codeplex.com)(in englischer Sprache) herunterladen.

Wenn Sie die Microsoft.PlayerFramework.Xaml.Samples-Projektmappe öffnen, sehen Sie eine Reihe von Ordnern innerhalb des Projekts. Der Ordner "Advertising" enthält den Beispielcode, der für das Erstellen eines Videoplayers mit Unterstützung für Werbeeinblendungen relevant ist. Im Ordner "Advertising" befinden sich mehrere XAML/cs-Dateien, die jeweils verschiedene Arten für das Einfügen von Werbeeinblendungen zeigen. Diese werden in der folgende Liste beschrieben:

* "AdPodPage.xaml" zeigt, wie ein Ad Pod angezeigt wird.
* "AdSchedulingPage.xaml" zeigt, wie Werbeeinblendungen geplant werden.
* "FreeWheelPage.xaml" zeigt, wie das Plug-In "FreeWheel" zum Planen von Werbeeinblendungen verwendet wird.
* "MastPage.xaml" zeigt, wie Werbeeinblendungen mit einer MAST-Datei geplant werden.
* "ProgrammaticAdPage.xaml" zeigt, wie Sie Werbeeinblendungen programmgesteuert in einem Video planen.
* "ScheduleClipPage.xaml" zeigt, wie Sie eine Werbeeinblendung ohne eine VAST-Datei planen.
* "VastLinearCompanionPage.xaml" zeigt, wie Sie eine lineare und eine begleitende Werbeeinblendung einfügen.
* "VastNonLinearPage.xaml" zeigt, wie Sie eine nicht lineare Werbeeinblendung einfügen.
* "VmapPage.xaml" zeigt, wie Sie Werbeeinblendungen mit einer VMAP-Datei angeben.

In jedem dieser Beispiele wird die von Player Framework definierte MediaPlayer-Klasse verwendet. In den meisten Beispielen werden Plug-Ins verwendet, mit denen Unterstützung für verschiedene Anzeigenantwortformate hinzugefügt wird. Das Beispiel "ProgrammaticAdPage" interagiert programmgesteuert mit einer MediaPlayer-Instanz.

### <a name="adpodpage-sample"></a>AdPodPage-Beispiel
In diesem Beispiel wird "AdSchedulerPlugin" verwendet, um zu definieren, wann eine Werbeeinblendung angezeigt werden soll. In diesem Beispiel wird geplant, dass eine Mid-Roll-Werbung nach fünf Sekunden abgespielt werden soll. Der Ad Pod (eine Gruppe von nacheinander angezeigten Werbeeinblendungen) wird in einer VAST-Datei angegeben, die von einem Adserver zurückgegeben wird. Der URI für die VAST-Datei wird im `<RemoteAdSource>`-Element angegeben.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Weitere Informationen zu "AdSchedulerPlugin" finden Sie unter [Advertising in the Player Framework on Windows 8 and Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
In diesem Beispiel wird ebenfalls "AdSchedulerPlugin" verwendet. Geplant werden drei Werbeeinblendungen: eine Pre-Roll-, eine Mid-Roll- und eine Post-Roll-Werbeeinblendung. Der URI für die VAST-Datei für die einzelnen Werbeeinblendungen wird in einem `<RemoteAdSource>`-Element angegeben.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
Dieses Beispiel verwendet „FreeWheelPlugin“. Dieses Plug-In gibt ein Quellattribut an, das wiederum einen URI angibt, der auf eine SmartXML-Datei mit Werbungsinhalten sowie Informationen zur Werbungsplanung verweist.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Dieses Beispiel verwendet "MastSchedulerPlugin", das es Ihnen ermöglicht, eine MAST-Datei zu verwenden. Das Quellattribut gibt den Speicherort der MAST-Datei an.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Dieses Beispiel interagiert programmgesteuert mit MediaPlayer. Die Datei "ProgrammaticAdPage.xaml" instanziiert MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Die Datei „ProgrammaticAdPage.xaml.cs“ erstellt ein AdHandlerPlugin-Element und fügt ein TimelineMarker-Element hinzu, um anzugeben, wann eine Werbeeinblendung angezeigt werden soll. Anschließend fügt sie einen Handler für das MarkerReached-Ereignis hinzu, das ein RemoteAdSource-Element lädt, das einen URI für eine VAST-Datei angibt. Anschließend wird die Werbeeinblendung abgespielt.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
Dieses Beispiel verwendet "AdSchedulerPlugin" zum Planen einer Mid-Roll-Werbeeinblendung durch Angabe einer WMV-Datei, die die Werbeeinblendung enthält.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Dieses Beispiel veranschaulicht, wie Sie „AdSchedulerPlugin“ verwenden, um eine lineare Mid-Roll-Werbeeinblendung mit einer begleitenden Werbeeinblendung zu planen. Das `<RemoteAdSource>`-Element gibt den Speicherort der VAST-Datei an.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
In diesem Beispiel wird "AdSchedulerPlugin" zum Planen einer linearen und einer nicht linearen Werbeeinblendung verwendet. Der Speicherort der VAST-Datei wird mit dem `<RemoteAdSource>`-Element angegeben.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
In diesem Beispiel wird „VmapSchedulerPlugin“ verwendet, um Werbeeinblendungen mithilfe einer VMAP-Datei zu planen. Der URI der VMAP-Datei wird im Quellattribut des `<VmapSchedulerPlugin>`-Elements angegeben.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementieren eines iOS-Videoplayers mit Unterstützung für Werbeeinblendungen
Microsoft Media-Plattform: Player Framework für iOS enthält eine Sammlung von Beispielanwendungen, die zeigen, wie Sie eine Videoplayeranwendung mithilfe des Frameworks implementieren. Sie können das Player Framework und die Beispiele unter [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework)herunterladen. Die GitHub-Seite enthält einen Link zu einem Wiki, das zusätzliche Informationen zum Player Framework und eine Einführung in das Playerbeispiel enthält: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Planen von Werbeeinblendungen mit VMAP
Das folgende Beispiel zeigt, wie Sie Werbeeinblendungen mithilfe einer VMAP-Datei planen.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Planen von Werbeeinblendungen mit VAST
Das folgende Beispiel zeigt, wie Sie eine VAST-Werbeeinblendung mit später Bindung planen.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Das folgende Beispiel zeigt, wie Sie eine VAST-Werbeeinblendung mit früher Bindung planen.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Im folgenden Beispiel wird gezeigt, wie Sie eine Werbeeinblendung mithilfe der Schnitteditierung (RCE, Rough Cut Editing) einfügen.

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

Im folgenden Beispiel wird gezeigt, wie Sie einen Ad Pod planen.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Im folgenden Beispiel wird gezeigt, wie Sie eine Mid-Roll-Werbeeinblendung als "Nicht-Sticky-Ad" planen. Eine "Nicht-Sticky-Ad" wird nur einmal wiedergegeben, unabhängig davon, wie viele Suchvorgänge der Betrachter ausführt.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Im folgenden Beispiel wird gezeigt, wie Sie eine Mid-Roll-Werbeeinblendung als "Sticky Ad" planen. Eine „Sticky Ad“ wird immer dann angezeigt, wenn der angegebene Punkt in der Videozeitleiste erreicht wird.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Das folgende Beispiel zeigt, wie eine Post-Roll-Werbeeinblendung geplant wird.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Das folgende Beispiel zeigt, wie eine Pre-Roll-Werbeeinblendung geplant wird.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Das folgende Beispiel zeigt, wie eine Mid-Roll-Overlay-Werbeeinblendung geplant wird.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Siehe auch
[Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md)

