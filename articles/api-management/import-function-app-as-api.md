---
title: Importieren einer Azure-Funktionen-App als API in Azure API Management | Microsoft-Dokumentation
description: Dieses Tutorial veranschaulicht, wie Sie eine Azure-Funktionen-App als API in Azure API Management importieren.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: apimpm
ms.openlocfilehash: e5e8ffc18db902eeaa8484122e3844f35046191f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243085"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importieren einer Azure-Funktionen-App als API in Azure API Management

Azure API Management unterstützt das Importieren von Azure-Funktionen-Apps als neue APIs und das Anfügen dieser Apps an vorhandene APIs. Bei diesem Prozess wird automatisch ein Hostschlüssel in der Azure-Funktionen-App generiert, der dann einem benannten Wert in Azure API Management zugewiesen wird.

In diesem Artikel werden die Schritte zum Importieren einer Azure-Funktionen-App als API in Azure API Management erläutert. Darüber hinaus wird der Prozess zum Testen beschrieben.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Importieren einer Azure-Funktionen-App als API
> * Anfügen einer Azure-Funktionen-App an eine API
> * Anzeigen des neuen Hostschlüssels der Azure-Funktionen-App und des benannten Azure API Management-Werts
> * Testen der API im Azure-Portal
> * Testen der API im Entwicklerportal

## <a name="prerequisites"></a>Voraussetzungen

* Absolvieren Sie den Schnellstart [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).
* Stellen Sie sicher, dass Ihr Abonnement eine Azure Functions-App enthält. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Sie muss Funktionen mit einem HTTP-Trigger enthalten, und die Einstellung für die Autorisierungsstufe muss auf *Anonym* oder *Funktion* festgelegt sein.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a> Importieren einer Azure-Funktionen-App als neue API

Führen Sie die folgenden Schritte aus, um auf der Grundlage einer Azure-Funktionen-App eine neue API zu erstellen.

1. Klicken Sie in Ihrer **Azure API Management**-Dienstinstanz im Menü auf der linken Seite auf **APIs**.

2. Wählen Sie in der Liste **Neue API hinzufügen** die Option **Funktionen-App** aus.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-01.png)

3. Klicken Sie auf **Durchsuchen**, um eine Funktionen-App für den Import auszuwählen.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-02.png)

4. Klicken Sie auf den Abschnitt **Funktionen-App**, um in der Liste der verfügbaren Funktionen-Apps eine App auszuwählen.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-03.png)

5. Suchen Sie die Funktionen-App, aus der Sie Funktionen importieren möchten, klicken Sie darauf, und klicken Sie auf **Auswählen**.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-04.png)

6. Wählen Sie die Funktionen aus, die Sie importieren möchten, und klicken Sie auf **Auswählen**.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Sie können nur Funktionen importieren, die auf dem HTTP-Trigger basieren und deren Einstellung für die Autorisierungsstufe auf *Anonym* oder *Funktion* festgelegt ist. Linux-Funktions-Apps werden momentan nicht unterstützt.

7. Wechseln Sie zur Ansicht **Vollständig**, und weisen Sie Ihrer neuen API **Produkt** zu. Bearbeiten Sie bei Bedarf vorab aufgefüllte Felder.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-06.png)

8. Klicken Sie auf **Create**.

## <a name="append-azure-function-app-to-api"></a> Anfügen einer Azure-Funktionen-App an eine vorhandene API

Führen Sie die folgenden Schritte aus, um eine Azure-Funktionen-App zu einer vorhandenen API hinzuzufügen.

1. Klicken Sie in Ihrer **Azure API Management**-Dienstinstanz im Menü auf der linken Seite auf **APIs**.

2. Wählen Sie eine API aus, in die Sie eine Azure-Funktionen-App importieren möchten. Klicken Sie auf **...** , und klicken Sie im Kontextmenü auf **Importieren**.

    ![Anfügen aus einer Funktionen-App](./media/import-function-app-as-api/append-01.png)

3. Klicken Sie auf die Kachel **Funktionen-App**.

    ![Anfügen aus einer Funktionen-App](./media/import-function-app-as-api/append-02.png)

4. Klicken Sie im Popupfenster auf **Durchsuchen**.

    ![Anfügen aus einer Funktionen-App](./media/import-function-app-as-api/append-03.png)

5. Klicken Sie auf den Abschnitt **Funktionen-App**, um in der Liste der verfügbaren Funktionen-Apps eine App auszuwählen.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-03.png)

6. Suchen Sie die Funktionen-App, aus der Sie Funktionen importieren möchten, klicken Sie darauf, und klicken Sie auf **Auswählen**.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-04.png)

7. Wählen Sie die Funktionen aus, die Sie importieren möchten, und klicken Sie auf **Auswählen**.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/add-05.png)

8. Klicken Sie auf **Importieren**.

    ![Anfügen aus einer Funktionen-App](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a> Autorisierung

Beim Importieren einer Azure-Funktionen-App wird automatisch Folgendes generiert:

* Hostschlüssel in der Funktionen-App mit dem Namen „apim-{*Name Ihrer Azure API Management-Dienstinstanz*}“
* Benannter Wert in der Azure API Management-Instanz mit dem Namen „{*Name der Instanz Ihrer Azure-Funktionen-App*}-key“, die den erstellten Hostschlüssel enthält

Bei APIs, die nach dem 4. April 2019 erstellt werden, wird der Hostschlüssel in HTTP-Anforderungen von API Management in einem Header an die Funktionen-App übergeben. Bei älteren APIs wird der Hostschlüssel als [Abfrageparameter](../azure-functions/functions-bindings-http-webhook.md#api-key-authorization) übergeben. Dieses Verhalten kann durch den `PATCH Backend`-[REST-API-Aufruf](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend/update#backendcredentialscontract) für die der Funktionen-App zugeordnete *Back-End-Entität* geändert werden.

> [!WARNING]
> Wenn Sie den Wert des Hostschlüssels der Azure-Funktionen-App oder den benannten Wert von Azure API Management entfernen oder ändern, wird die Kommunikation zwischen den Diensten unterbrochen. Die Werte werden nicht automatisch synchronisiert.
>
> Falls Sie den Hostschlüssel rotieren müssen, ändern Sie auch den benannten Wert in Azure API Management.

### <a name="access-azure-function-app-host-key"></a>Zugreifen auf den Hostschlüssel der Azure-Funktionen-App

1. Navigieren Sie zur Instanz Ihrer Azure-Funktionen-App.

2. Wählen Sie in der Übersicht **Funktionen-App-Einstellungen** aus.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/keys-02-a.png)

3. Der Schlüssel befindet sich im Abschnitt **Hostschlüssel**.

    ![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Zugreifen auf den benannten Wert in Azure API Management

Navigieren Sie zu Ihrer Azure API Management-Instanz, und klicken Sie im Menü auf der linken Seite auf **Benannte Werte**. Der Schlüssel der Azure-Funktionen-App ist dort gespeichert.

![Hinzufügen aus einer Funktionen-App](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a> Testen der neuen API Management-API im Azure-Portal

Sie können Vorgänge direkt über das Azure-Portal aufrufen. Das Azure-Portal bietet eine komfortable Möglichkeit, die Vorgänge einer API anzuzeigen und zu testen.  

1. Wählen Sie die im vorherigen Abschnitt erstellte API aus.

2. Wählen Sie die Registerkarte **Testen** aus.

3. Wählen Sie einen Vorgang aus.

    Die Seite zeigt Felder für Abfrageparameter und Felder für die Header. Einer der Header ist **Ocp-Apim-Subscription-Key**. Er steht für den Abonnementschlüssel des Produkts, das dieser API zugeordnet ist. Wenn Sie die API Management-Instanz erstellt haben, sind Sie bereits Administrator, sodass der Schlüssel automatisch eingetragen wird. 

4. Wählen Sie **Senden** aus.

    Das Back-End antwortet mit **200 OK** und einigen Daten.

## <a name="test-in-developer-portal"></a> Aufrufen eines Vorgangs über das Entwicklerportal

Sie können Vorgänge auch über das Entwicklerportal aufrufen, um APIs zu testen. 

1. Wählen Sie die API aus, die Sie im Schritt „Importieren und Veröffentlichen einer Back-End-API“ erstellt haben.

2. Klicken Sie auf **Entwicklerportal**.

    Die Website „Entwicklerportal“ wird geöffnet.

3. Wählen Sie die **API** aus, die Sie erstellt haben.

4. Wählen Sie den Vorgang aus, den Sie testen möchten.

5. Wählen Sie **Testen**.

6. Wählen Sie **Senden** aus.
    
    Nach dem Aufruf der Operation zeigt das Entwicklerportal den **Antwortstatus**, die **Antwortheader** sowie den **Antwortinhalt** an.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
