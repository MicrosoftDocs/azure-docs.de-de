---
title: Importieren einer OpenAPI-Spezifikation mit dem Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine OpenAPI-Spezifikation mit API Management importieren und die API dann im Azure- und im Entwicklerportal testen.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 05295efec3d7651c3a77dd5ad6e2c72b57a6c5a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994692"
---
# <a name="import-an-openapi-specification"></a>Importieren einer OpenAPI-Spezifikation

In diesem Artikel erfahren Sie, wie Sie eine Back-End-API mit OpenAPI-Spezifikation aus https://conferenceapi.azurewebsites.net?format=json importieren. Diese Back-End-API wird von Microsoft bereitgestellt und unter Azure gehostet. Der Artikel zeigt außerdem, wie Sie die APIM-API testen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Importieren einer Back-End-API vom Typ „OpenAPI-Spezifikation“
> * Testen der API im Azure-Portal
> * Testen der API im Entwicklerportal

## <a name="prerequisites"></a>Voraussetzungen

Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importieren und Veröffentlichen einer Back-End-API

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.
2. Wählen Sie in der Liste **Add a new API** (Neue API hinzufügen) die Option **OpenAPI-Spezifikation**.

    ![OpenAPI-Spezifikation](./media/import-api-from-oas/oas-api.png)
3. Geben Sie API-Einstellungen ein. Sie können die Werte während der Erstellung festlegen oder später über die Registerkarte **Einstellungen** konfigurieren. Die Einstellungen werden im Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md#import-and-publish-a-backend-api) erläutert.
4. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Die Einschränkungen beim API-Import sind [in einem anderen Artikel](api-management-api-import-restrictions.md) dokumentiert.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
