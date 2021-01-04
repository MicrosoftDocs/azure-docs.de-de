---
title: Moderieren von Bildern mit der API-Konsole – Content Moderator
titleSuffix: Azure Cognitive Services
description: Initiieren Sie über die Bildmoderations-API in Azure Content Moderator die Überprüfungsmoderationsworkflows für Bildinhalte.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 542fadd5e5ab91be7b7113064bf8c998dae08d12
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912938"
---
# <a name="moderate-images-from-the-api-console"></a>Moderieren von Bildern über die API-Konsole

Initiieren Sie über die [Bildmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) in Azure Content Moderator die Überprüfungsmoderationsworkflows für Bildinhalte. Der Moderationsauftrag prüft Ihre Inhalte auf unerwünschte Ausdrücke und vergleicht sie mit benutzerdefinierten und gemeinsam genutzten Blocklisten.

## <a name="use-the-api-console"></a>Verwenden der API-Konsole
Bevor Sie die API in der Onlinekonsole testen können, benötigen Sie Ihren Abonnementschlüssel. Dieser befindet sich auf der Registerkarte **Einstellungen** im Feld **Ocp-Apim-Subscription-Key**. Weitere Informationen finden Sie in der [Übersicht](overview.md).

1. Navigieren Sie zur [Referenz für die Bildmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Die Bildmoderationsseite **Bild – Auswerten** wird geöffnet.

2. Wählen Sie für **API-Testkonsole öffnen** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Testen der Regionsauswahl auf der Seite „Bild – Auswerten“](images/test-drive-region.png)
  
   Die API-Konsole **Bild – Auswerten** wird geöffnet.

3. Geben Sie in das Feld **Ocp-Apim-Subscription-Key** Ihren Abonnementschlüssel ein.

   ![Testen des Abonnementschlüssel in der Konsole „Bild – Auswerten“](images/try-image-api-1.PNG)

4. Verwenden Sie im Feld **Anforderungstext** das Standardbeispielbild, oder legen Sie ein zu überprüfendes Bild fest. Sie können das Bild selbst im Binärdatenformat senden oder eine öffentlich zugängliche URL für ein Bild angeben. 

   Verwenden Sie in diesem Beispiel den Pfad, der im Feld **Anforderungstext** angegeben ist, und klicken Sie dann auf **Senden**. 

   ![Testen des Anforderungstexts in der Konsole „Bild – Auswerten“](images/try-image-api-2.PNG)

   Hierbei handelt es sich um das Bild unter folgender URL:

   ![Testen des Beispielbilds in der Konsole „Bild – Auswerten“](images/sample-image.jpg) 

5. Wählen Sie **Senden** aus.

6. Die API gibt eine Wahrscheinlichkeitsbewertung für jede Klassifizierung zurück. Sie gibt überdies zurück, ob das Bild die Bedingungen erfüllt ( **true** oder **false** ). 

   ![Testen der Wahrscheinlichkeitsbewertung in der Konsole „Bild – Auswerten“ und Feststellung der Erfüllung der Bedingungen](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Gesichtserkennung

Über die Bildmoderations-API können Sie Gesichter in einem Bild ausfindig machen. Diese Option kann hilfreich sein, wenn Sie Bedenken bezüglich des Datenschutzes haben und verhindern möchten, das bestimmte Gesichter auf Ihrer Plattform veröffentlicht werden. 

1. Wählen Sie in der [Referenz für die Bildmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) im Menü auf der linken Seite unter **Bild** die Option **Gesichter suchen** aus. 

   Die Seite **Bild – Gesichter suchen** wird geöffnet.

2. Wählen Sie für **API-Testkonsole öffnen** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Testen der Regionsauswahl auf der Seite „Bild – Gesichter suchen“](images/test-drive-region.png)

   Die API-Konsole **Bild – Gesichter suchen** wird geöffnet.

3. Geben Sie ein zu überprüfendes Bild an. Sie können das Bild selbst im Binärdatenformat senden oder eine öffentlich zugängliche URL für ein Bild angeben. Dieses Beispiel enthält Links zu einem Bild, das in einer CNN-Story verwendet wird.

   ![Testen des Beispielbilds auf der Seite „Bild – Gesichter suchen“](images/try-image-api-face-image.jpg)

   ![Testen der Beispielanforderung auf der Seite „Gesichter suchen“](images/try-image-api-face-request.png)

4. Wählen Sie **Senden** aus. In diesem Beispiel sucht die API nach zwei Gesichtern und gibt die jeweiligen Koordinaten innerhalb des Bilds zurück.

   ![Testen des Inhaltsbereichs der Beispielantwort auf der Seite „Gesichter suchen“](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Texterkennung über die OCR-Funktion

Mit der OCR-Funktion von Content Moderator können Sie Texte in Bildern erkennen.

1. Wählen Sie in der [Referenz für die Bildmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) im Menü auf der linken Seite unter **Bild** die Option **OCR** aus. 

   Die Seite **Bild – OCR** wird geöffnet.

2. Wählen Sie für **API-Testkonsole öffnen** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Regionsauswahl auf der Seite „Bild – OCR“](images/test-drive-region.png)

   Die API-Konsole **Bild – OCR** wird geöffnet.

3. Geben Sie in das Feld **Ocp-Apim-Subscription-Key** Ihren Abonnementschlüssel ein.

4. Verwenden Sie im Feld **Anforderungstext** das Standardbeispielbild. Dies ist das gleiche Bild, das im vorherigen Abschnitt verwendet wurde.

5. Wählen Sie **Senden** aus. Der extrahierte Text wird im JSON-Format angezeigt:

   ![Inhaltsbereich der Beispielantwort auf der Seite „Bild – OCR](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die REST-API im Code, oder befolgen Sie die Anweisungen im [.NET SDK-Schnellstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp), um Ihrer Anwendung Bildmoderation hinzuzufügen.