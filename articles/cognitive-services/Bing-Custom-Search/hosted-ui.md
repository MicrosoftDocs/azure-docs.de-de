---
title: Konfigurieren einer gehostete Benutzeroberfläche für die benutzerdefinierte Bing-Suche | Microsoft-Dokumentation
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Artikel zum Konfigurieren und Integrieren einer gehosteten Benutzeroberfläche für die benutzerdefinierte Bing-Suche.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca3f9da681c60608b5b196b17191f0b4d549f305
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338366"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurieren der gehosteten Benutzeroberfläche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die benutzerdefinierte Bing-Suche bietet eine gehostete Benutzeroberfläche, die Sie problemlos als JavaScript-Codeausschnitt in Ihre Webseiten und -anwendungen integrieren können. Mithilfe des Portals für die benutzerdefinierte Bing-Suche können Sie das Layout, die Farbe und die Suchoptionen der Benutzeroberfläche konfigurieren.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurieren der benutzerdefinierten gehosteten Benutzeroberfläche

Gehen Sie folgendermaßen vor, um eine gehostete Benutzeroberfläche für Ihre Web-Anwendungen zu konfigurieren: Während Sie Änderungen vornehmen, erhalten Sie im Bereich auf der rechten Seite eine Vorschau auf die Benutzeroberfläche. Bei den angezeigten Ergebnissen handelt es sich nicht um echte Ergebnisse für Ihre Instanz.

1. Melden Sie sich am [Portal](https://customsearch.ai) für die benutzerdefinierte Bing-Suche an.  
  
2. Wählen Sie Ihre Instanz der benutzerdefinierten Bing-Suche aus.

3. Klicken Sie auf die Registerkarte **Gehostete Benutzeroberfläche**.  
  
4. Wählen Sie ein Layout aus.

    - Suchleiste und Ergebnisse (Standard): Zeigt ein Suchfeld mit Suchergebnissen darunter an.
    - Nur Ergebnisse: Zeigt nur Suchergebnisse ohne Suchfeld an. Bei Verwendung dieses Layouts müssen Sie die Suchabfrage (`&q=<query string>`) angeben. Fügen Sie den Abfrageparameter der Anforderungs-URL im JavaScript-Codeausschnitt oder im HTML-Endpunktlink hinzu.
    - Einblendung im Vordergrund: Stellt ein Suchfeld bereit und zeigt die Suchergebnisse in einer eingeblendeten Überlagerung an.

5. Wählen Sie ein Farbdesign aus. Sie können die Farben durch Klicken auf **Design anpassen** Ihrer Anwendung entsprechend anpassen. Zum Ändern einer Farbe geben Sie entweder den RGB-Hexadezimalwert der Farbe (z.B. `#366eb8`) ein, oder klicken Sie auf die Farbvorschau.

   Auf der rechten Seite des Portals können Sie eine Vorschau auf Ihre Änderungen anzeigen. Durch Klicken auf **Auf Standard zurücksetzen** werden wieder die Standardfarben für das ausgewählte Design verwendet.

   > [!NOTE]
   > Berücksichtigen Sie bei der Auswahl von Farben die Barrierefreiheit.

6. Geben Sie unter **Zusätzliche Konfigurationen** entsprechende Werte für Ihre App an. Diese Einstellungen sind optional. Die Auswirkungen der angewendeten oder entfernten Einstellungen sehen Sie im Vorschaubereich auf der rechten Seite. Folgende Konfigurationsoptionen stehen zur Verfügung:  

7. Geben Sie den Abonnementschlüssel für die Suche ein, oder wählen Sie einen in der Dropdownliste aus. Die Dropdownliste enthält Schlüssel aus den Abonnements Ihres Azure-Kontos. Weitere Informationen finden Sie unter [Create a Cognitive Services APIs account in the Azure portal](../cognitive-services-apis-create-account.md) (Erstellen eines Cognitive Services-APIs-Kontos im Azure-Portal).  

8. Wenn Sie die Vorschlagssuche aktiviert haben, geben Sie den Abonnementschlüssel für die Vorschlagssuche ein, oder wählen Sie einen in der Dropdownliste aus. Die Dropdownliste enthält Schlüssel aus den Abonnements Ihres Azure-Kontos. Für die benutzerdefinierte Vorschlagssuche muss ein bestimmter Abonnementtarif verwendet werden. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Nutzen der benutzerdefinierten Benutzeroberfläche

Die gehostete Benutzeroberfläche kann auf zwei Arten genutzt werden: 

- Schließen Sie das Skript in Ihre Webseite ein.  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Sie können auch die folgende URL in einem Webbrowser verwenden.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Fügen Sie nach Bedarf der URL die folgenden Abfrageparameter hinzu. Weitere Informationen zu diesen Parametern finden Sie in der Referenz zur [API für die benutzerdefinierte Suche](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters).
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > Es ist nicht möglich, auf der Seite Ihre Datenschutzrichtlinie oder andere Hinweise/Bedingungen anzuzeigen. Die Eignung für den gewünschten Zweck kann variieren.  

Weitere Informationen (einschließlich Ihrer benutzerdefinierten Konfigurations-ID) finden Sie auf der Registerkarte **Produktion** unter **Endpunkte**.

## <a name="configuration-options"></a>Konfigurationsoptionen

Sie können das Verhalten der gehosteten Benutzeroberfläche konfigurieren, indem Sie auf **Zusätzliche Konfigurationen** klicken und Werte bereitstellen. Diese Einstellungen sind optional. Die Auswirkungen der angewendeten oder entfernten Einstellungen sehen Sie im Vorschaubereich auf der rechten Seite. 

### <a name="web-search-configurations"></a>Konfigurationen für die Websuche

- Webergebnisse aktiviert: Legt fest, ob die Websuche aktiviert ist (am oberen Rand der Seite wird die Registerkarte „Web“ angezeigt).
- Vorschlagssuche aktivieren: Legt fest, ob die Vorschlagssuche aktiviert ist (die Zusatzkosten finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).
- Webergebnisse pro Seite: Anzahl der gleichzeitig angezeigten Websuchergebnisse (max. 50 Ergebnisse pro Seite).
- Bildtitel: Bestimmt, ob Bilder mit Suchergebnissen angezeigt werden.

Die folgenden Konfigurationen werden angezeigt, wenn Sie auf **Erweiterte Konfigurationen anzeigen** klicken:

- Wörter hervorheben: Legt fest, ob Suchbegriffe in den Ergebnissen fett formatiert werden.
- Linkziel: Legt fest, ob die Webseite auf einer neuen Browserregisterkarte („blank“) oder auf derselben Registerkarte („self“) geöffnet wird, wenn der Benutzer auf ein Suchergebnis klickt.

### <a name="image-search-configurations"></a>Konfigurationen für die Bildersuche

- Bildergebnisse aktiviert: Legt fest, ob die Bildersuche aktiviert ist (am oberen Rand der Seite wird die Registerkarte „Bilder“ angezeigt).
- Bildergebnisse pro Seite: Anzahl der gleichzeitig angezeigten Bildersuchergebnisse (max. 150 Ergebnisse pro Seite).

Die folgende Konfiguration wird angezeigt, wenn Sie auf **Erweiterte Konfigurationen anzeigen** klicken.  
  
- Filter aktivieren: Fügt Filter hinzu, mit denen der Benutzer die von Bing zurückgegebenen Bilder filtern kann. Beispielsweise kann der Benutzer die Ergebnisse nur nach animierten GIF-Dateien filtern.

### <a name="video-search-configurations"></a>Konfigurationen für die Videosuche

- Videoergebnisse aktiviert: Legt fest, ob die Videosuche aktiviert ist (am oberen Rand der Seite wird die Registerkarte „Videos“ angezeigt).
- Videoergebnisse pro Seite: Anzahl der gleichzeitig angezeigten Videoersuchergebnisse (max. 150 Ergebnisse pro Seite).

Die folgende Konfiguration wird angezeigt, wenn Sie auf **Erweiterte Konfigurationen anzeigen** klicken.  
  
- Filter aktivieren: Fügt Filter hinzu, mit denen der Benutzer die von Bing zurückgegebenen Videos filtern kann. Beispielsweise kann der Benutzer die Ergebnisse nach Videos mit einer bestimmten Auflösung oder nach Videos, die in den letzten 24 Stunden gefunden wurden, filtern.

### <a name="miscellaneous-configurations"></a>Sonstige Konfigurationen

- Seitentitel: Text, der im Titelbereich der Seite mit den Suchergebnissen (nicht beim Layout mit eingeblendeten Überlagerungen) angezeigt wird.
- Symbolleistendesign: Legt die Hintergrundfarbe des Titelbereichs der Seite mit den Suchergebnissen fest.

Die folgenden Konfigurationen werden angezeigt, wenn Sie auf **Erweiterte Konfigurationen anzeigen** klicken.  

|Column1  |Column2  |
|---------|---------|
|Textplatzhalter für das Suchfeld   | Text, der vor Beginn der Eingabe im Suchfeld angezeigt wird.        |
|Titellink-URL    |Ziel für den Titellink.         |
|Logo-URL     | Bild, das neben dem Titel angezeigt wird.         |
|Favicon    | Symbol, das auf der Titelleiste des Browsers angezeigt wird.          |

Die folgenden Konfigurationen gelten nur, wenn Sie die gehostete Benutzeroberfläche über den HTML-Endpunkt verwenden (gilt nicht bei Verwendung des JavaScript-Codeausschnitts).

- Seitentitel
- Symbolleistendesign
- Titellink-URL
- Logo-URL
- Favicon-URL  

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Dekorationsmarkierungen zum Hervorheben von Text](../bing-web-search/hit-highlighting.md)
- [Einteilen von Webseiten](../bing-web-search/paging-search-results.md)