---
title: Konfigurieren der Benutzeroberfläche für die benutzerdefinierte Bing-Suche | Microsoft-Dokumentation
titleSuffix: Azure Cognitive Services
description: 'Im Portal können Sie eine Suchinstanz erstellen, die die Websegmente angibt: Domänen, Unterseiten und Webseiten.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: b2d4eb75002474bbec57753bbf0255cc4c2b4a16
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338638"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurieren der Benutzeroberfläche für die benutzerdefinierte Bing-Suche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mit einer benutzerdefinierten Suchinstanz können Sie die Benutzeroberfläche für die Suche so anpassen, dass nur Inhalte von Websites einbezogen werden, die Ihre Benutzer interessieren. Anstatt eine webweite Suche durchzuführen, durchsucht Bing nur die Segmente des Webs, die für Sie interessant sind. Um Ihre benutzerdefinierte Ansicht des Webs zu erstellen, verwenden Sie das [Portal](https://www.customsearch.ai) für die benutzerdefinierte Bing-Suche.

Im Portal können Sie eine Suchinstanz erstellen, die die Websegmente angibt: Domänen, Unterseiten und Webseiten, die Bing durchsuchen soll, sowie diejenigen, die nicht durchsucht werden sollen. Das Portal kann auch Inhalt vorschlagen, der ggf. eingebunden werden soll.

Verwenden Sie beim Definieren Ihrer Websegmente Folgendes:

| Segmentname | BESCHREIBUNG                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Ein Domänensegment umfasst alle Inhalte, die innerhalb einer Internetdomäne gefunden werden. Beispiel: `www.microsoft.com`. Das Weglassen von `www.` bewirkt, dass Bing auch die Unterdomänen der Domäne durchsucht. Wenn Sie z.B. `microsoft.com` angeben, gibt Bing auch Ergebnisse von `support.microsoft.com` oder `technet.microsoft.com` zurück. |
| Unterseite    | Ein Unterseitensegment umfasst sämtliche Inhalte, die auf der Unterseite und in den Pfaden darunter gefunden werden. Sie können maximal zwei Unterseiten im Pfad angeben. Zum Beispiel, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Webseite    | Ein Webseitensegment kann nur diese Webseite in eine benutzerdefinierten Suche einschließen. Sie können optional angeben, ob Unterseiten enthalten sein sollen.                                                                                                                                                                                  |

> [!IMPORTANT]
> Alle Domänen, Unterseiten und Webseiten, die Sie angeben, müssen öffentlich und von Bing indiziert sein. Wenn Sie der Besitzer einer öffentlichen Website sind, die in der Suche enthalten sein soll, und Bing diese noch nicht indiziert hat, finden Sie in der Bing-[Webmasterdokumentation](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Details dazu, wie Bing die Website indizieren kann. Außerdem finden Sie in der Webmasterdokumentation Details dazu, wie Bing die durchforstete Site aktualisieren kann, wenn der Index nicht mehr aktuell ist.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Hinzufügen von Websegmenten zu Ihrer Instanz für die benutzerdefinierte Suche

Beim Erstellen Ihrer Instanz für die benutzerdefinierte Suche können Sie die Websegmente angeben: Domänen, Unterseiten und Webseiten, die Sie in Ihre Suchergebnisse einbinden oder blockieren möchten. 

Wenn Sie die Segmente kennen, die in Ihrer Instanz für die benutzerdefinierte Suche enthalten sein sollen, können Sie diese der Liste **Aktiv** Ihrer Instanz hinzufügen. 

Wenn Sie nicht sicher sind, welche Segmente einbezogen werden sollen, können Sie im Bereich **Vorschau** Suchabfragen an Bing senden und die gewünschten Segmente auswählen. Gehen Sie dazu folgendermaßen vor: 

1. Wählen Sie im Bereich „Vorschau“ in der Dropdownliste die Option „Bing“ aus, und geben Sie eine Suchabfrage ein.

2. Klicken Sie neben dem Ergebnis, das enthalten sein soll, auf **Website hinzufügen**. Klicken Sie dann auf „OK“.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Anpassen Ihrer Suchbenutzeroberfläche mit den Listen „Aktiv“ und „Blockiert“ 

Sie können auf die Liste mit aktiven und blockierten Segmenten zugreifen, indem Sie in Ihrer Instanz für die benutzerdefinierte Suche auf die Registerkarten **Aktiv** und **Blockiert** klicken. Die Segmente, die Sie der Liste „Aktiv“ hinzufügen, werden in Ihre benutzerdefinierte Suche einbezogen. Blockierte Segmente werden nicht durchsucht und nicht in Ihren Suchergebnissen angezeigt.

Klicken Sie auf die Registerkarte **Aktiv**, und fügen Sie mindestens eine URL hinzu, um die Websegmente anzugeben, die mit Bing durchsucht werden sollen. Verwenden Sie zum Bearbeiten oder Löschen von URLs die Optionen unter der Spalte **Steuerelemente**. 

Sie können der Liste **Aktiv** einzelne URLs oder mehrere URLs gleichzeitig hinzufügen, indem Sie über das entsprechende Symbol eine Textdatei hochladen.

![Registerkarte „Aktiv“ der benutzerdefinierten Bing-Suche](media/file-upload-icon.png)

Erstellen Sie für den Dateiupload eine Textdatei, und geben Sie nur eine Domäne, Unterseite oder Webseite pro Zeile an. Ihre Datei wird abgelehnt, falls sie nicht richtig formatiert ist.

> [!NOTE]
> * Sie können nur eine Datei in die Liste **Aktiv** hochladen. Sie können sie nicht verwenden, um der Liste **Blockiert** Segmente hinzuzufügen.  
> * Wenn die Liste **Blockiert** eine Domäne, Unterseite oder Webseite enthält, die Sie in der Uploaddatei angegeben haben, wird sie aus der Liste **Blockiert** entfernt und der Liste **Aktiv** hinzugefügt.
> * Doppelte Einträge in der Uploaddatei werden von der benutzerdefinierten Bing-Suche ignoriert. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Abrufen von Websitevorschlägen für Ihre Suchoberfläche

Nach dem Hinzufügen von Websegmenten zur Liste **Aktiv** werden im Portal für die benutzerdefinierte Bing-Suche unten auf der Registerkarte Vorschläge für Websites und Unterseiten generiert. Hierbei handelt es sich um Segmente, für die von der benutzerdefinierten Bing-Suche die Einbindung vorgeschlagen wird. Klicken Sie auf **Aktualisieren**, um aktualisierte Vorschläge erhalten, nachdem die Einstellungen Ihrer Instanz für die benutzerdefinierte Suche aktualisiert wurden. Dieser Abschnitt ist nur sichtbar, wenn Vorschläge verfügbar sind.

## <a name="search-for-images-and-videos"></a>Suchen nach Bildern und Videos

Sie können nach Bildern und Videos auf ähnliche Weise wie nach Webinhalten suchen, indem Sie die [API für die benutzerdefinierte Bing-Bildersuche](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) bzw. die [API für die benutzerdefinierte Bing-Videosuche](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) verwenden. Sie können diese Ergebnisse mit der [gehosteten Benutzeroberfläche](hosted-ui.md) oder den APIs anzeigen. 

Diese APIs ähneln den APIs für die nicht benutzerdefinierte [Bing-Bildersuche](../Bing-Image-Search/overview.md) und [Bing-Videosuche](../bing-video-search/overview.md). Es wird aber das gesamte Web durchsucht, und der Abfrageparameter `customConfig` ist nicht erforderlich. In diesen Dokumentationssätzen finden Sie auch weitere Informationen zur Arbeit mit Bildern und Videos. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testen Ihrer Suchinstanz mit dem Bereich „Vorschau“

Sie können Ihre Suchinstanz im Vorschaubereich auf der rechten Seite testen, um Suchabfragen zu übermitteln und die Ergebnisse anzuzeigen. 

1. Wählen Sie unterhalb des Suchfelds die Option **Meine Instanz**. Sie können die Ergebnisse Ihrer Suchbenutzeroberfläche mit Bing vergleichen, indem Sie die Option **Bing** wählen. 
2. Wählen Sie einen sicheren Suchfilter und den zu durchsuchenden Markt aus (siehe [Abfrageparameter](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Geben Sie eine Abfrage ein, und drücken Sie die EINGABETASTE, oder klicken Sie auf das Suchsymbol, um die Ergebnisse aus der aktuellen Konfiguration anzuzeigen. Sie können den Typ der von Ihnen durchgeführten Suche ändern, indem Sie auf **Web**, **Bild** oder **Video** klicken, um die entsprechenden Ergebnisse abzurufen. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Anpassen des Rangs von bestimmten Suchergebnissen

Über das Portal können Sie die Suchrangfolge des Inhalts bestimmter Domänen, Unterseiten und Webseiten anpassen. Nachdem Sie im Vorschaubereich eine Suchabfrage gesendet haben, enthält jedes Suchergebnis eine Liste mit Anpassungen, die Sie jeweils vornehmen können:  

| Anpassung | BESCHREIBUNG |
|------------|-------------|
| Block      | Verschiebt die Domäne, Unterseite oder Webseite in die Liste „Blockiert“. Bing schließt den Inhalt des ausgewählten Orts dann von der Anzeige in den Suchergebnissen aus.                    |
| Verstärken      | Zeigt Inhalte der Domäne oder Unterseite weiter oben in den Suchergebnissen an.                                                                                        |
| Tiefer stufen     | Stuft Inhalte aus der Domäne oder Unterseite tiefer und zeigt sie weiter unten in den Suchergebnissen an. Sie wählen aus, ob Inhalt aus der Domäne oder Unterseite, zu der die Webseite gehört, tiefer gestuft werden soll. |
| Oben anheften | Verschiebt die Domäne, Unterseite oder Webseite in die Liste **Angeheftet**. So wird erzwungen, dass die Webseite für eine bestimmte Suchabfrage als oberstes Suchergebnis angezeigt wird.                   |

Die Anpassung des Rangs ist für Bild- oder Videosuchen nicht verfügbar.

### <a name="boosting-and-demoting-search-results"></a>Verstärken und Tieferstufen von Suchergebnissen

Sie können jede Domäne oder Unterseite in der Liste **Aktiv** extrem verstärken, verstärken oder tiefer stufen. Standardmäßig werden alle Segmente ohne Rangfolgeanpassungen hinzugefügt. Websegmente, die extrem verstärkt oder verstärkt werden, erhalten in den Suchergebnissen einen höheren Rang (dabei führt das „extreme Verstärken“ zu einem höheren Rang als das „Verstärken“). Elemente, die tiefer gestuft werden, erhalten in den Suchergebnissen eine niedrigere Rangfolge.

Sie können Elemente extrem verstärken, verstärken oder tiefer stufen, indem Sie die Steuerelemente für die **Rangfolgeanpassung** in der Liste **Aktiv** oder die Steuerelemente für die Verstärkung und Tieferstufung im Vorschaubereich verwenden. Der Dienst fügt das Segment der Liste „Aktiv“ hinzu und passt die Rangfolge entsprechend an.

> [!NOTE] 
> Verstärken und Tieferstufen von Domänen und Unterseiten sind zwei der vielen Methoden, die von der benutzerdefinierten Bing-Suche verwendet werden, um die Reihenfolge von Suchergebnissen zu bestimmen. Aufgrund von anderen Faktoren, die sich auf die Rangfolge unterschiedlicher Webinhalte auswirken, können die Folgen der Anpassung variieren. Verwenden Sie den Bereich „Vorschau“, um die Auswirkungen der Rangzuweisung für Ihre Suchergebnisse zu testen. 

Das extreme Verstärken, Verstärken und Tieferstufen ist für die Bilder- und Videosuche nicht verfügbar.

## <a name="pin-slices-to-the-top-of-search-results"></a>Anheften von Segmenten oben in den Suchergebnissen

Im Portal können Sie für bestimmte Suchbegriffe auch URLs oben in den Suchergebnissen anheften, indem Sie die Registerkarte **Angeheftet** verwenden. Geben Sie eine URL und eine Abfrage ein, um die Webseite anzugeben, die als oberstes Ergebnis angezeigt wird. Beachten Sie, dass Sie maximal eine Webseite pro Suchabfrage anheften können und nur indizierte Webseiten in Suchen angezeigt werden. Das Anheften von Ergebnissen ist für Bild- oder Videosuchen nicht verfügbar.

Sie haben zwei Möglichkeiten, um eine Webseite im oberen Bereich anzuheften:

* Geben Sie auf der **angehefteten** Registerkarte die URL der Webseite, die oben angeheftet werden soll, und die zugehörige Abfrage ein.

* Geben Sie im Bereich **Vorschau** eine Suchabfrage ein, und klicken Sie auf „Suchen“. Suchen Sie die Webseite, die Sie für die Abfrage anheften möchten, und klicken Sie auf **Oben anheften**. Die Webseite und die Abfrage werden der Liste **Angeheftet** hinzugefügt.

### <a name="specify-the-pins-match-condition"></a>Angeben der Übereinstimmungsbedingung für das Anheften

Standardmäßig werden Webseiten nur dann oben in den Suchergebnissen angeheftet, wenn die Abfragezeichenfolge eines Benutzers genau mit einer Abfrage in der Liste **Angeheftet** übereinstimmt. Sie können dieses Verhalten ändern, indem Sie eine der folgenden Übereinstimmungsbedingungen angeben:

> [!NOTE]
> Bei allen Vergleichen zwischen der Suchabfrage des Benutzers und der angehefteten Suchabfrage wird die Groß-/Kleinschreibung berücksichtigt.

| Wert | BESCHREIBUNG                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Beginnt mit | Zum Anheften muss die Abfragezeichenfolge des Benutzers mit der Abfragezeichenfolge der Anheftung beginnen. |
| Endet mit   | Zum Anheften muss die Abfragezeichenfolge des Benutzers auf die Abfragezeichenfolge der Anheftung enden.  |
| Enthält    | Zum Anheften muss die Abfragezeichenfolge des Benutzers die Abfragezeichenfolge der Anheftung enthalten.   |


Klicken Sie auf das Symbol „Bearbeiten“, um die Übereinstimmungsbedingung der Anheftung zu ändern. Klicken Sie in der Spalte **Query match condition** (Übereinstimmungsbedingung für Abfrage) auf die Dropdownliste, und wählen Sie die neue Bedingung aus. Klicken Sie dann auf das Symbol zum Speichern, um die Änderung zu speichern.

### <a name="change-the-order-of-your-pinned-sites"></a>Ändern der Reihenfolge Ihrer angehefteten Orte

Zum Ändern der Reihenfolge Ihrer angehefteten Elemente können Sie diese per Drag & Drop verschieben oder die jeweilige Reihenfolgennummer bearbeiten, indem Sie in der Liste **Angeheftet** in der Spalte **Steuerelemente** auf das Symbol „Bearbeiten“ klicken.

Wenn mehrere angeheftete Elemente eine Übereinstimmungsbedingung erfüllen, wird für die benutzerdefinierte Bing-Suche das Element mit der höchsten Position in der Liste verwendet.

## <a name="view-statistics"></a>Anzeigen von Statistiken

Wenn Sie die benutzerdefinierte Suche auf der entsprechenden Ebene abonniert haben (siehe [Preisseiten](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), wird Ihren Produktionsinstanzen eine Registerkarte **Statistiken** hinzugefügt. Die Registerkarte „Statistiken“ zeigt Details zur Verwendung Ihrer benutzerdefinierten Suchendpunkte einschließlich Aufrufaufkommen, häufigste Abfragen, geografische Verteilung, Antwortcodes und sichere Suche an. Sie können die Details mit den bereitgestellten Steuerelementen filtern.

## <a name="usage-guidelines"></a>Verwendungsrichtlinien

- Für jede benutzerdefinierte Suchinstanz ist die maximale Anzahl von Rangfolgeanpassungen, die Sie an den Segmenten **Aktiv** und **Blockiert** vornehmen können, auf 400 beschränkt.
- Das Hinzufügen eines Segments zu den Registerkarten „Aktiv“ oder „Blockiert“ wird als eine Rangfolgeanpassung gezählt.
- Die Verstärkung und Tieferstufung wird als zwei Rangfolgeanpassungen gezählt.
- Für jede benutzerdefinierte Suchinstanz ist die maximale Anzahl von Anheftungen, die Sie vornehmen können, auf 200 beschränkt.

## <a name="next-steps"></a>Nächste Schritte

- [Aufrufen der benutzerdefinierten Suche](./search-your-custom-view.md)
- [Konfigurieren der gehosteten Benutzeroberfläche](./hosted-ui.md)
- [Verwenden von Dekorationsmarkierungen zum Hervorheben von Text](../bing-web-search/hit-highlighting.md)
- [Einteilen von Webseiten](../bing-web-search/paging-search-results.md)