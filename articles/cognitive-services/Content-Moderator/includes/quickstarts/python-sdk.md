---
title: 'Schnellstart: Content Moderator-Clientbibliothek für Python'
titleSuffix: Azure Cognitive Services
description: Informieren Sie sich in diesem Schnellstart über die ersten Schritte mit der Azure Content Moderator-Clientbibliothek für Python. Integrieren Sie Software zur Inhaltsfilterung in Ihre App, um gesetzliche Vorschriften einzuhalten oder Benutzern die für sie beabsichtigte Umgebung zu bieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 324f89d2de56666242ed4f6231eff0855ec561b3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356476"
---
Hier erhalten Sie Informationen zu den ersten Schritten mit der Azure Content Moderator-Clientbibliothek für Python. Führen Sie die nachfolgenden Schritte zum Installieren des PiPy-Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. 

Content Moderator ist ein KI-Dienst für die Behandlung potenziell anstößiger, riskanter oder anderweitig unerwünschter Inhalte. Verwenden Sie den KI-gestützten Dienst zur Inhaltsmoderation, um Text, Bilder und Videos zu scannen und Inhaltsflags automatisch anzuwenden. Integrieren Sie dann das Review-Tool in Ihre App, eine Online-Moderatorumgebung für ein Team aus menschlichen Prüfern. Integrieren Sie Software zur Inhaltsfilterung in Ihre App, um gesetzliche Vorschriften einzuhalten oder Benutzern die für sie beabsichtigte Umgebung zu bieten.

Führen Sie mit der Content Moderator-Clientbibliothek für Python die folgenden Aktionen aus:

* Moderieren von Text
* Verwenden einer benutzerdefinierten Begriffsliste
* Moderieren von Bildern
* Verwenden einer benutzerdefinierten Bildliste
* Erstellen einer Überprüfung

[Referenzdokumentation](/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Wenn Sie über ein Azure-Abonnement verfügen, können Sie im Azure-Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Erstellen einer Content Moderator-Ressource"  target="_blank">eine Content Moderator-Ressource erstellen <span class="docon docon-navigate-external x-hidden-focus"></span></a>, um Ihren Schlüssel und Endpunkt abzurufen. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um für Ihre Anwendung eine Verbindung mit Content Moderator herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.


## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python können Sie die Content Moderator-Clientbibliothek mit dem folgenden Befehl installieren:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie ein neues Python-Skript, und öffnen Sie es in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Fügen Sie dann am Anfang der Datei die folgenden `import`-Anweisungen hinzu:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie als Nächstes Variablen für den Endpunktstandort und den Schlüssel der Ressource.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Content Moderator-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise [Azure Key Vault](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen werden einige Hauptfunktionen der Content Moderator-Clientbibliothek für Python verarbeitet:

|Name|BESCHREIBUNG|
|---|---|
|[ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Diese Klasse wird für alle Content Moderator-Funktionen benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Generieren von Instanzen anderer Klassen.|
|[ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Diese Klasse stellt die Funktionalität zum Analysieren von Bildern auf nicht jugendfreie Inhalte, persönliche Informationen oder menschliche Gesichter bereit.|
|[TextModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Diese Klasse stellt die Funktionalität zum Analysieren von Text auf Sprache, Obszönitäten, Fehler und persönliche Informationen bereit.|
[ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Diese Klasse stellt die Funktionalität der Überprüfungs-APIs bereit, einschließlich der Methoden zum Erstellen von Aufträgen, benutzerdefinierten Workflows und Überprüfungen durch Personen.|

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Content Moderator-Clientbibliothek für Python ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Moderieren von Text](#moderate-text)
* [Verwenden einer benutzerdefinierten Begriffsliste](#use-a-custom-terms-list)
* [Moderieren von Bildern](#moderate-images)
* [Verwenden einer benutzerdefinierten Bildliste](#use-a-custom-image-list)
* [Erstellen einer Überprüfung](#create-a-review)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python)-Objekt mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein [ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)-Objekt zu erstellen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Moderieren von Text

Der folgende Code verwendet einen Content Moderator-Client zum Analysieren von Text und Ausgeben der Ergebnisse in der Konsole. Erstellen Sie zunächst im Stammverzeichnis Ihres Projekts den Ordner **text_files/** , und fügen Sie die Datei *content_moderator_text_moderation.txt* hinzu. Fügen Sie dieser Datei Ihren eigenen Text hinzu, oder verwenden Sie den folgenden Beispieltext:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Fügen Sie einen Verweis auf den neuen Ordner hinzu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Fügen Sie anschließend Ihrem Python-Skript den folgenden Code hinzu:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Verwenden einer benutzerdefinierten Begriffsliste

Der folgende Code veranschaulicht, wie Sie eine Liste mit benutzerdefinierten Begriffen für die Textmoderation verwalten. Sie können mit der Klasse [ListManagementTermListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) eine Begriffsliste erstellen, die einzelnen Begriffe verwalten und anderen Text anhand der Liste überprüfen.

### <a name="get-sample-text"></a>Abrufen von Beispieltext

Wenn Sie dieses Beispiel verwenden möchten, müssen Sie im Stammverzeichnis Ihres Projekts den Ordner **text_files/** erstellen und ihm die Datei *content_moderator_text_moderation.txt* hinzufügen. Diese Datei sollte organischen Text enthalten, der mit der Liste der Begriffe verglichen wird. Sie können den folgenden Beispieltext verwenden:

```
This text contains the terms "term1" and "term2".
```

Fügen Sie einen Verweis auf den Ordner hinzu, sofern Sie noch keinen definiert haben.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Erstelle eine Liste.

Fügen Sie Ihrem Python-Skript den folgenden Code hinzu, um eine benutzerdefinierte Begriffsliste zu erstellen und ihren ID-Wert zu speichern:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definieren von Listendetails

Mit der ID einer Liste können Sie den Namen und die Beschreibung der Liste bearbeiten.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Hinzufügen eines Begriffs zu einer Liste

Der folgende Code fügt der Liste die Begriffe `"term1"` und `"term2"` hinzu:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Abrufen aller Begriffe in der Liste

Sie können die Listen-ID verwenden, um alle Begriffe in der Liste zurückzugeben.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Aktualisieren des Listenindex

Wenn Sie der Liste Begriffe hinzufügen bzw. Begriffe daraus entfernen, müssen Sie den Index aktualisieren, damit Sie die aktualisierte Liste verwenden können.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Überprüfen von Text anhand der Liste

Die benutzerdefinierte Begriffsliste dient hauptsächlich dazu, Text mit der Liste zu vergleichen und zu ermitteln, ob übereinstimmende Begriffe vorhanden sind. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Entfernen eines Begriffs aus einer Liste

Mit dem folgenden Code wird der Begriff `"term1"` aus der Liste entfernt:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Entfernen aller Begriffe aus einer Liste

Verwenden Sie den folgenden Code, um alle Begriffe in einer Liste zu löschen:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-a-list"></a>Löschen einer Liste

Löschen Sie eine benutzerdefinierte Begriffsliste mithilfe des folgenden Codes:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Moderieren von Bildern

Der folgende Code verwendet einen Content Moderator-Client und ein [ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)-Objekt, um Bilder auf nicht jugendfreie und freizügige Inhalte zu analysieren.

### <a name="get-sample-images"></a>Abrufen von Beispielbildern

Legen Sie einen Verweis auf einige zu analysierende Bilder fest.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Fügen Sie dann den folgenden Code hinzu, um die Bilder zu durchlaufen. Der Rest des Codes in diesem Abschnitt wird in diese Schleife eingefügt.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Überprüfen von Bildern auf nicht jugendfreie/freizügige Inhalte

Der folgende Code überprüft das Bild unter der angegebenen URL auf nicht jugendfreie oder freizügige Inhalte und gibt die Ergebnisse in der Konsole aus. Informationen zur dieser Begriffe finden Sie im Leitfaden zu [Bildmoderationskonzepten](../../image-moderation-api.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Überprüfen auf sichtbaren Text

Der folgende Code überprüft das Bild auf sichtbaren Text und gibt die Ergebnisse in der Konsole aus.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Überprüfen auf Gesichter

Der folgende Code überprüft das Bild auf menschliche Gesichter und gibt die Ergebnisse in der Konsole aus.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Verwenden einer benutzerdefinierten Bildliste

Der folgende Code veranschaulicht, wie Sie eine benutzerdefinierte Liste mit Bildern für die Bildmoderation verwalten. Dies ist ein nützliches Feature, wenn Ihre Plattform häufig die gleichen Bilder empfängt, die Sie herausfiltern möchten. Durch das Führen einer Liste mit diesen bestimmten Bildern können Sie die Leistung steigern. Mit der [ListManagementImageListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python)-Klasse können Sie eine Bildliste erstellen, die einzelnen Bilder in der Liste verwalten und andere Bilder damit vergleichen.

Erstellen Sie die folgenden Textvariablen zum Speichern der Bild-URLs, die Sie in diesem Szenario verwenden.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Dabei handelt sich nicht um die richtige Liste, sondern eine informelle Liste mit Bildern, die im Abschnitt `add images` des Codes hinzugefügt werden.


### <a name="create-an-image-list"></a>Erstellen einer Bildliste

Fügen Sie den folgenden Code hinzu, um eine Bildliste zu erstellen und einen Verweis auf ihre ID zu speichern.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Hinzufügen von Bildern zu einer Liste

Mit dem folgenden Code werden alle Bilder der Liste hinzugefügt.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Definieren Sie die Hilfsfunktion **add_images** an anderer Stelle im Skript.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Abrufen der Bilder in einer Liste

Der folgende Code gibt die Namen aller Bilder in der Liste aus.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Aktualisieren von Listendetails

Mit der Listen-ID können Sie den Namen und die Beschreibung der Liste aktualisieren.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Abrufen von Listendetails

Verwenden Sie den folgenden Code, um die aktuellen Details der Liste auszugeben:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Aktualisieren des Listenindex

Nach dem Hinzufügen oder Entfernen von Bildern müssen Sie den Listenindex aktualisieren, bevor Sie ihn zum Überprüfen anderer Bilder verwenden können.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Abgleichen der Bilder mit der Liste

Die Hauptfunktion von Bildlisten besteht darin, neue Bilder zu vergleichen und festzustellen, ob Übereinstimmungen vorhanden sind.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Entfernen eines Bilds aus der Liste

Mit dem folgenden Code wird ein Element aus der Liste entfernt. In diesem Fall handelt es sich um ein Bild, das nicht mit der Listenkategorie übereinstimmt.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Entfernen aller Bilder aus einer Liste

Verwenden Sie den folgenden Code, um eine Bildliste zu bereinigen:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Löschen der Bildliste

Verwenden Sie den folgenden Code, um eine bestimmte Bildliste zu löschen:

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Erstellen einer Überprüfung

Mithilfe der Content Moderator-Clientbibliothek für Python können Sie Inhalte in das [Überprüfungstool](https://contentmoderator.cognitive.microsoft.com) eingeben, damit sie von menschlichen Moderatoren überprüft werden können. Weitere Informationen zum Überprüfungstool finden Sie im [konzeptionellen Leitfaden zum Überprüfungstool](../../review-tool-user-guide/human-in-the-loop.md).

Der folgende Code verwendet nach dem Eingang menschlicher Eingaben über das Webportal des Überprüfungstools die Klasse [ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python), um eine Überprüfung zu erstellen, ihre ID abzurufen und ihre Details zu überprüfen.

### <a name="get-review-credentials"></a>Abrufen von Anmeldeinformationen für die Überprüfung

Melden Sie sich zuerst beim Überprüfungstool an, und rufen Sie den Teamnamen ab. Weisen Sie ihn dann der entsprechenden Variablen im Code zu. Optional können Sie einen Rückrufendpunkt einrichten, um Aktualisierungen für die Aktivität der Überprüfung zu empfangen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Erstellen einer Bildüberprüfung

Fügen Sie den folgenden Code hinzu, um eine Überprüfung für die angegebene Bild-URL zu erstellen und zu veröffentlichen. Der Code speichert einen Verweis auf die Überprüfungs-ID. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Abrufen von Bewertungsdetails

Verwenden Sie den folgenden Code, um die Details einer bestimmten Überprüfung zu überprüfen. Nachdem Sie die Überprüfung erstellt haben, können Sie selbst das Überprüfungstool aufrufen und mit dem Inhalt interagieren. Weitere Informationen hierzu finden Sie unter [Erstellen von Überprüfungen durch Personen](../../review-tool-user-guide/review-moderated-images.md). Wenn Sie fertig sind, können Sie diesen Code erneut ausführen. Die Ergebnisse des Überprüfungsvorgangs werden abrufen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Wenn Sie in diesem Szenario einen Rückrufendpunkt verwendet haben, sollte ein Ereignis im folgenden Format zurückgegeben werden:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `python` für die Schnellstartdatei aus.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Content Moderator-Python-Bibliothek zum Ausführen von Moderationsaufgaben verwenden. Im Anschluss können Sie sich im konzeptionellen Leitfaden ausführlicher über die Moderation von Bildern oder anderen Medien informieren.

> [!div class="nextstepaction"]
>[Lernen von Bildmoderationskonzepten](../../image-moderation-api.md)

* [Was ist Azure Content Moderator?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).