---
title: 'Schnellstart: Erstellen, Bereitstellen und Verwenden eines benutzerdefinierten Modells: Benutzerdefinierter Translator'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung wird der Prozess zum Erstellen eines Übersetzungssystems mit Custom Translator Schritt für Schritt beschrieben.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: 4fbac2c2d77d838ac7fc4f3caaa73983633c3e03
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366044"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Schnellstart: Erstellen, Bereitstellen und Verwenden eines benutzerdefinierten Modells für die Übersetzung

Dieser Artikel enthält ausführliche Anweisungen zum Erstellen eines Übersetzungssystem mit Custom Translator.

## <a name="prerequisites"></a>Voraussetzungen

1. Für die Verwendung des [Custom Translator](https://portal.customtranslator.azure.ai)-Portals benötigen Sie ein [Microsoft-Konto](https://signup.live.com) oder [Azure AD-Konto](../../../active-directory/fundamentals/active-directory-whatis.md) (in Azure gehostetes Organisationskonto), um sich anzumelden.

2. Abonnement für die Textübersetzungs-API über das Azure-Portal. Sie benötigen den Abonnementschlüssel der Textübersetzungs-API für die Zuordnung zu Ihrem Arbeitsbereich in Custom Translator. [Hier](../translator-how-to-signup.md) erfahren Sie, wie Sie sich für die Textübersetzungs-API registrieren.

3. Sind die beiden Komponenten oben vorhanden, melden Sie sich beim Portal für den [benutzerdefinierten Translator](https://portal.customtranslator.azure.ai) an, um Arbeitsbereiche und Projekte zu erstellen, Dateien hochzuladen sowie Modelle zu erstellen und bereitzustellen.

>[!Note]
>Der benutzerdefinierte Translator unterstützt nicht das Erstellen eines Arbeitsbereichs für eine Textübersetzungs-API-Ressource, die innerhalb von [Enabled VNET](../../../api-management/api-management-using-with-vnet.md) erstellt wurde.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Als Erstbenutzer werden Sie aufgefordert, den Vertragsbedingungen zuzustimmen, um einen Arbeitsbereich zu erstellen und dem Abonnement für die Microsoft-Textübersetzungs-API zuzuordnen.

![Arbeitsbereich erstellen](media/quickstart/terms-of-service.png)
![Arbeitsbereich erstellen (Bild 1)](media/quickstart/create-workspace-1.png)
![Arbeitsbereich erstellen (Bild 2)](media/quickstart/create-workspace-2.png)
![Arbeitsbereich erstellen (Bild 3)](media/quickstart/create-workspace-3.png)
![Arbeitsbereich erstellen (Bild 4)](media/quickstart/create-workspace-4.png)
![Arbeitsbereich erstellen (Bild 5)](media/quickstart/create-workspace-5.png)
![Arbeitsbereich erstellen (Bild 6)](media/quickstart/create-workspace-6.png)

Navigieren Sie bei nachfolgenden Besuchen im Portal für den benutzerdefinierten Translator zur Seite „Einstellungen“. Dort können Sie den Arbeitsbereich verwalten, weitere Arbeitsbereiche erstellen, den Abonnementschlüssel der Microsoft-Textübersetzungs-API Ihren Arbeitsbereichen zuordnen, Mitbesitzer hinzufügen und Abonnementschlüssel ändern.

## <a name="create-a-project"></a>Erstellen eines Projekts

Klicken Sie auf der Startseite des Custom Translator-Portals auf „Neues Projekt“. Im Dialogfeld können Sie den gewünschten Projektnamen, das Sprachpaar, die Kategorie und weitere relevante Felder ausfüllen. Speichern Sie anschließend Ihr Projekt. Weitere Informationen finden Sie unter [Erstellen des Projekts](how-to-create-project.md).

![Projekt erstellen](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Hochladen von Dokumenten

Laden Sie als Nächstes Dokumente für [Training](training-and-model.md#training-document-type-for-custom-translator), [Optimierung](training-and-model.md#tuning-document-type-for-custom-translator) und [Tests](training-and-model.md#testing-dataset-for-custom-translator) hoch. Sie können sowohl [parallele](what-are-parallel-documents.md) als auch kombinierte Dokumente hochladen. Darüber hinaus können Sie ein [Wörterbuch](what-is-dictionary.md) hochladen.

Dokumente können entweder über die Registerkarte für Dokumente oder über die Seite eines bestimmten Projekts hochgeladen werden.

![Hochladen von Dokumenten](media/quickstart/ct-how-to-upload.png)

Wählen Sie beim Hochladen von Dokumenten den Dokumenttyp (Training, Optimierung oder Test) und das Sprachpaar aus. Beim Hochladen paralleler Dokumente müssen Sie darüber hinaus einen Dokumentnamen angeben. Weitere Informationen finden Sie unter [Upload document](how-to-upload-document.md) (Hochladen eines Dokuments).

## <a name="create-a-model"></a>Erstellen eines Modells

Wurden alle erforderlichen Dokumente hochgeladen, erstellen Sie als Nächstes Ihr Modell.

Wählen Sie das von Ihnen erstellte Projekt aus. Sie sehen alle hochgeladenen Dokumente, die das gleiche Sprachpaar wie dieses Projekt aufweisen. Wählen Sie die Dokumente aus, die im Modell enthalten sein sollen. Sie können [Trainingsdaten](training-and-model.md#training-document-type-for-custom-translator), [Optimierungsdaten](training-and-model.md#tuning-document-type-for-custom-translator) und [Testdaten](training-and-model.md#testing-dataset-for-custom-translator) oder nur Trainingsdaten auswählen und von Custom Translator automatisch Optimierungs- und Testsätze für Ihr Modell erstellen lassen.

![Erstellen eines Modells](media/quickstart/ct-how-to-train.png)

Wenn Sie die gewünschten Dokumente ausgewählt haben, klicken Sie auf die Schaltfläche „Create Model“ (Modell erstellen), um Ihr Modell zu erstellen und mit dem Training zu beginnen. Auf der Registerkarte „Modelle“ können Sie den Status des Trainings und Details zu allen trainierten Modellen anzeigen.

Weitere Informationen finden Sie unter [Modellerstellung](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analysieren Ihres Modells

Sehen Sie sich nach Abschluss des Trainings die Ergebnisse an. Die BLEU-Bewertung ist eine Metrik, die die Qualität der Übersetzung angibt. Sie können die von Ihrem benutzerdefinierten Modell angefertigten Übersetzungen auch manuell mit den in den Testsätzen bereitgestellten Übersetzungen vergleichen. Navigieren Sie dazu zur Registerkarte „Test“, und klicken Sie auf „System Results“ (Systemergebnisse). Durch die manuelle Überprüfung dieser Übersetzungen können Sie sich ein Bild von der Qualität der von Ihrem System generierten Übersetzungen machen. Ausführlichere Informationen finden Sie unter [System Test Results](how-to-view-system-test-results.md) (Systemtestergebnisse).

## <a name="deploy-a-trained-model"></a>Bereitstellen eines trainierten Modells

Wenn Sie das trainierte Modell bereitstellen möchten, klicken Sie auf die Schaltfläche „Bereitstellen“. Pro Projekt ist ein bereitgestelltes Modell zulässig, und Sie können den Status Ihrer Bereitstellung in der Spalte „Status“ anzeigen. Ausführlichere Informationen finden Sie unter [Modellimplementierung](how-to-view-system-test-results.md#deploy-a-model).

![Bereitstellen eines trainierten Modells](media/quickstart/ct-how-to-deploy.png)

## <a name="swap-deployed-model"></a>Austauschen eines bereitgestellten Modells

Klicken Sie auf die Schaltfläche „Austauschen“ neben einem bereitgestellten Modell, um dieses innerhalb eines Projekts durch ein anderes auszutauschen. Während des Austauschs ist das bereitgestellte Modell weiterhin verfügbar, um Übersetzungsanforderungen zu verarbeiten. 

![Austauschen eines bereitgestellten Modells](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>Verwenden eines bereitgestellten Modells

Auf bereitgestellte Modelle kann über die [Microsoft-Textübersetzungs-API V3 durch Angabe der Kategorie-ID](../reference/v3-0-translate.md?tabs=curl) zugegriffen werden. Weitere Informationen zur Textübersetzungs-API finden Sie auf der Webseite mit der [API-Referenz](../reference/v3-0-reference.md).

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit der Navigation im [Custom Translator-Arbeitsbereich und der Verwaltung Ihrer Projekte](workspace-and-project.md) vertraut.