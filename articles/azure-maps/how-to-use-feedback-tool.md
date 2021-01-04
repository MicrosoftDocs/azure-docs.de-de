---
title: Bereitstellen von Datenfeedback in Azure Maps | Microsoft Azure Maps
description: Stellen Sie Datenfeedback mithilfe des Microsoft Azure Maps-Feedbacktools bereit.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4be775d8a6a8c476bcc659a902c7f43c12a6d6d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905331"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Bereitstellen von Datenfeedback in Azure Maps

Azure Maps ist seit Mai 2018 verfügbar. Azure Maps bietet aktuelle Kartendaten, einfach zu verwendende REST-APIs und leistungsstarke SDKs zur Unterstützung unserer Unternehmenskunden in einer Vielzahl von geschäftlichen Anwendungsfällen. Die reale Welt ändert sich jede Sekunde, und es ist entscheidend für uns, dass wir für unsere Kunden eine tatsachenbasierte digitale Repräsentation bereitstellen. Unsere Kunden, die planen, Standorte zu öffnen oder zu schließen, benötigen unsere Karten, um schnell auf dem neuesten Stand zu sein. So können sie effizient Lieferung, Wartung oder Kundendienst an den richtigen Standorten planen. Wir haben die Website für Datenfeedback zu Azure Maps erstellt, um unseren Kunden die Bereitstellung von direktem Datenfeedback zu ermöglichen. Das Datenfeedback der Kunden geht direkt an unsere Datenanbieter und ihre Karten-Editoren. Sie können Feedback schnell auswerten und in unsere Mapping-Produkte integrieren.  

Die [Feedback-Website für Azure Maps-Daten (Vorschau)](https://feedback.azuremaps.com) bietet unseren Kunden eine einfache Möglichkeit, Feedback zu Kartendaten zu geben, insbesondere für geschäftliche POIs (Point of Interest) und Privatadressen. In diesem Artikel erhalten Sie Informationen dazu, wie Sie mit der Website für Feedback zu Azure Maps auf verschiedene Weisen Feedback bereitstellen können.

## <a name="add-a-business-place-or-a-residential-address"></a>Hinzufügen eines Geschäftsorts oder einer Wohnadresse 

Möglicherweise möchten Sie Feedback zu einem fehlenden Point of Interest oder einer fehlenden Wohnadresse geben. Dafür gibt es zwei Möglichkeiten. Öffnen Sie die Website für Datenfeedback zu Azure Maps, suchen Sie nach den Koordinaten des fehlenden Standorts, und klicken Sie dann auf „Ort hinzufügen“.

  ![Suchen eines fehlenden Orts](./media/how-to-use-feedback-tool/search-poi.png)

Alternativ können Sie mit der Karte interagieren. Klicken Sie auf den Ort, um eine Stecknadel an den Koordinaten abzulegen, und dann auf „Ort hinzufügen“.

  ![Hinzufügen einer Stecknadel](./media/how-to-use-feedback-tool/add-poi.png)

Nachdem Sie geklickt haben, werden Sie zu einem Formular weitergeleitet, um die entsprechenden Details für den Ort bereitzustellen.

  ![Hinzufügen eines Orts](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Korrigieren eines Geschäftsorts oder einer Wohnadresse 

Auf der Feedbacksite können Sie auch einen Geschäftsort oder eine Adresse suchen. Wenn sie nicht korrekt sind, können Sie Feedback senden, um die Adresse oder die Stecknadelposition zu korrigieren. Um Feedback zur Korrektur der Adresse bereitzustellen, verwenden Sie die Suchleiste, um nach einem Geschäftsort oder einer Wohnadresse zu suchen. Klicken Sie in der Ergebnisliste auf den gewünschten Ort. Klicken Sie auf „Ort korrigieren“.

  ![Suchen des zu korrigierenden Orts](./media/how-to-use-feedback-tool/fix-place.png)

Wenn Sie Feedback zum Korrigieren der Adresse senden möchten, füllen Sie das Formular „Ort korrigieren“ aus, und klicken Sie dann auf die Schaltfläche „Absenden“.

  ![Korrekturformular](./media/how-to-use-feedback-tool/fix-form.png)

Wenn die Position der Stecknadel für den Ort falsch ist, aktivieren Sie in dem Formular „Ort korrigieren“ das Kontrollkästchen „Stecknadelposition ist falsch“. Verschieben Sie die Stecknadel an die richtige Position, und klicken Sie dann auf die Schaltfläche „Senden“.

  ![Verschieben der Stecknadelposition](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Hinzufügen eines Kommentars 

Zusätzlich zur Suchmöglichkeit gestattet Ihnen das Feedbacktool, einen frei formulierten Kommentartext hinzuzufügen, um auf den Ort bezogene Details anzugeben. Um einen Kommentar hinzuzufügen, suchen Sie nach dem Speicherort, oder klicken Sie darauf. Klicken Sie auf „Kommentar hinzufügen“, schreiben Sie einen Kommentar, und klicken Sie dann auf „Senden“.

  ![Hinzufügen eines Kommentars](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Nachverfolgen des Status 

Sie können auch den Status Ihrer Anforderung überprüfen, indem Sie das Kontrollkästchen „Ich möchte den Status nachverfolgen“ aktivieren und Ihre E-Mail-Adresse während des Stellens der Anforderung angeben. Sie erhalten einen Nachverfolgungslink in der E-Mail, der einen aktuellen Status Ihrer Anforderung bereitstellt. 

  ![Feedbackstatus](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Nächste Schritte

Um technische Fragen im Zusammenhang mit Azure Maps bereitzustellen, besuchen Sie:

* [Microsoft Q & A](/answers/topics/azure-maps.html)