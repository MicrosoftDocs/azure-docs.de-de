---
title: 'Schnellstart: Clientbibliothek für die Azure-Verwaltung für Python'
description: In diesem Schnellstart werden die ersten Schritte mit der Clientbibliothek für die Azure-Verwaltung für Python erläutert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: d6dfa8ccc35b36bf7f1e85e6ab213bd4e4783eaf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95095872"
---
[Referenzdokumentation](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [Paket (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>Voraussetzungen

* Ein gültiges Azure-Abonnement: [Erstellen Sie ein kostenloses Abonnement](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie in Ihrem bevorzugten Editor oder in der IDE eine neue Python-Anwendung, und navigieren Sie in einem Konsolenfenster zu Ihrem Projekt.

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Sie können die Clientbibliothek wie folgt installieren:

```console
pip install azure-mgmt-cognitiveservices
```

Bei Verwendung der Visual Studio-IDE ist die Clientbibliothek als herunterladbares NuGet-Paket verfügbar.

### <a name="import-libraries"></a>Importbibliotheken

Öffnen Sie Ihr Python-Skript, und importieren Sie die folgenden Bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Fügen Sie dem Stamm des Skripts die folgenden Felder hinzu, und geben Sie die zugehörigen Werte unter Verwendung des zuvor erstellten Dienstprinzipals und Ihrer Azure-Kontoinformationen ein.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Fügen Sie dann den folgenden Code hinzu, um ein **CognitiveServicesManagementClient**-Objekt zu erstellen. Dieses Objekt wird für alle Azure-Verwaltungsvorgänge benötigt.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource"></a>Erstellen einer Cognitive Services-Ressource

### <a name="choose-a-service-and-pricing-tier"></a>Auswählen eines Diensts und eines Tarifs

Wenn Sie eine neue Ressource erstellen, müssen Sie die „Art“ des Diensts, den Sie verwenden möchten, sowie den gewünschten [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/) (oder die SKU) kennen. Diese und andere Informationen verwenden Sie beim Erstellen der Ressource als Parameter. Die folgende Funktion listet die verfügbaren Arten von Cognitive Services-Diensten auf.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Erstellen einer Cognitive Services-Ressource

Um eine neue Cognitive Services-Ressource zu erstellen und zu abonnieren, verwenden Sie die **Create**-Funktion. Diese Funktion fügt der Ressourcengruppe, die Sie übergeben, eine neue abrechenbare Ressource hinzu. Wenn Sie die neue Ressource erstellen, müssen Sie die „Art“ des Diensts, den Sie verwenden möchten, sowie dessen Tarif (oder die SKU) und einen Azure-Standort kennen. Die folgende Funktion akzeptiert all diese Argumente und erstellt eine Ressource.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

## <a name="view-your-resources"></a>Anzeigen Ihrer Ressourcen

Verwenden Sie die folgende Funktion, um alle Ressourcen in Ihrem Azure-Konto (in allen Ressourcengruppen) anzuzeigen:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Löschen einer Ressource

Die folgende Funktion löscht die angegebene Ressource aus der jeweiligen Ressourcengruppe.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Aufrufen von Verwaltungsfunktionen

Fügen Sie am Ende Ihres Skripts den folgenden Code hinzu, um die obigen Funktionen aufzurufen. Dieser Code listet verfügbare Ressourcen auf, erstellt eine Beispielressource, listet Ihre eigenen Ressourcen auf und löscht dann die Beispielressource.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie Ihre Anwendung über die Befehlszeile mit dem Befehl `python` aus.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Siehe auch

* [Referenzdokumentation für das Azure Management SDK](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Was ist Azure Cognitive Services?](../../what-are-cognitive-services.md)
* [Authentifizieren von Anforderungen an Azure Cognitive Services](../../authentication.md)
* [Erstellen einer neuen Ressource über das Azure-Portal](../../cognitive-services-apis-create-account.md)