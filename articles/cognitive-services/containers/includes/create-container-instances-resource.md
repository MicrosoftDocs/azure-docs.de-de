---
title: Containerunterstützung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie eine Azure Container Instances-Ressource erstellen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009865"
---
## <a name="create-an-azure-container-instance-resource"></a>Erstellen einer Azure Container Instances-Ressource

1. Wechseln Sie zur Seite [Erstellen](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) für Container Instances.

2. Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen ein:

    |Einstellung|Wert|
    |--|--|
    |Subscription|Wählen Sie Ihr Abonnement aus.|
    |Resource group|Wählen Sie die verfügbare Ressource aus, oder erstellen Sie eine neue wie z.B. `cognitive-services`.|
    |Containername|Geben Sie einen Namen ein (beispielsweise `cognitive-container-instance`). Der Name muss aus Kleinbuchstaben bestehen.|
    |Standort|Wählen Sie eine Region für die Bereitstellung aus.|
    |Imagetyp|Wenn Ihr Containerimage in einer Containerregistrierung gespeichert ist, die keine Anmeldeinformationen erfordert, wählen Sie `Public`. Wenn der Zugriff auf Ihr Containerimage Anmeldeinformationen erfordert, wählen Sie `Private`. Weitere Informationen darüber, ob das Containerimage `Public` oder `Private` („Öffentliche Vorschau“) ist, finden Sie unter [Containerrepositorys und -images](../../cognitive-services-container-support.md#container-repositories-and-images). |
    |Imagename|Geben Sie den Speicherort des Cognitive Services-Containers an. Der Speicherort wird als Argument für den Befehl `docker pull` verwendet. Die verfügbaren Imagenamen und entsprechenden Repositorys finden Sie unter [Containerrepositorys und -images](../../cognitive-services-container-support.md#container-repositories-and-images).<br><br>Der Imagename muss vollqualifiziert sein und drei Teile angeben. Zuerst die Containerregistrierung, dann das Repository und abschließend der Imagename: `<container-registry>/<repository>/<image-name>`.<br><br>Beispiel: `mcr.microsoft.com/azure-cognitive-services/keyphrase` würde das Image für die Schlüsselbegriffserkennung in der Microsoft-Containerregistrierung unter dem Azure Cognitive Services-Repository darstellen. Weiteres Beispiel: `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` würde das Spracherkennungsimage im Microsoft-Repository der Container Preview-Containerregistrierung darstellen. |
    |Betriebssystemtyp|`Linux`|
    |Size|Ändern Sie die Größe entsprechend den vorgeschlagenen Empfehlungen für Ihren spezifischen Cognitive Services-Container:<br>2 CPU-Kerne<br>4 GB

3. Geben Sie auf der Registerkarte **Netzwerk** die folgenden Informationen ein:

    |Einstellung|Wert|
    |--|--|
    |Ports|Legen Sie den TCP-Port auf `5000` fest. Macht den Container an Port 5000 verfügbar.|

4. Geben Sie auf der Registerkarte **Erweitert** die erforderlichen **Umgebungsvariablen** für die Abrechnungseinstellungen der Azure Container Instance-Ressource ein:

    | Schlüssel | Wert |
    |--|--|
    |`apikey`|Kopiert von der Seite **Schlüssel** der Ressource. Es handelt sich um eine aus 32 alphanumerischen Zeichen bestehende Zeichenfolge ohne Leerzeichen oder Bindestriche, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopiert von der Seite **Übersicht** der Ressource.|
    |`eula`|`accept`|

5. Klicken Sie auf **Überprüfen und erstellen**.
6. Nachdem die Überprüfung abgeschlossen ist, klicken Sie auf **Erstellen**, um den Erstellungsprozess abzuschließen.
7. Nach ihrer erfolgreicher Bereitstellung ist die Ressource bereit.
