---
title: Senden von Anforderungen mit Postman
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie Postman konfigurieren und zum Testen von Azure Digital Twins-APIs verwenden.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: fe79797aeef2b0c148aece0319d6b340b663b184
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763722"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Verwenden von Postman zum Senden von Anforderungen an die Azure Digital Twins-APIs

[Postman](https://www.getpostman.com/) ist ein REST-Testtool, das wichtige HTTP-Anforderungsfunktionen in einer desktop- und pluginbasierten grafischen Benutzeroberfläche bietet. Sie können es zum Erstellen von HTTP-Anforderungen verwenden und diese an die [REST-APIs von Azure Digital Twins](how-to-use-apis-sdks.md) übermitteln.

In diesem Artikel wird beschrieben, wie Sie über die folgenden Schritte den [Postman-REST-Client](https://www.getpostman.com/) für die Interaktion mit den Azure Digital Twins-APIs konfigurieren:

1. Verwenden Sie die [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), um ein Bearertoken zu erhalten, das Sie für API-Anforderungen in Postman verwenden können.
1. Richten Sie eine Postman-Sammlung ein, und konfigurieren Sie den Postman-REST-Client, um das Bearertoken für die Authentifizierung zu verwenden.
1. Verwenden Sie das konfigurierte Postman-Tool zum Erstellen und Senden einer Anforderung an die Azure Digital Twins-APIs.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen eine Azure Digital Twins-Instanz einrichten und Postman herunterladen, um Postman weiterhin für den Zugriff auf die Azure Digital Twins-APIs zu verwenden. Im restlichen Teil dieses Abschnitts werden diese Schritte beschrieben.

### <a name="set-up-azure-digital-twins-instance"></a>Einrichten einer Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Herunterladen von Postman

Laden Sie anschließend die Desktopversion des Postman-Clients herunter. Navigieren Sie zu [*www.getpostman.com/apps*](https://www.getpostman.com/apps), und befolgen Sie die Anweisungen, um die App herunterzuladen.

## <a name="get-bearer-token"></a>Abrufen des Bearertokens

Nachdem Sie Postman und die Azure Digital Twins-Instanz eingerichtet haben, müssen Sie ein Bearertoken abrufen, das von Postman-Anforderungen zur Autorisierung mit den Azure Digital Twins-APIs verwendet werden kann.

Es gibt mehrere Möglichkeiten, dieses Token abzurufen. In diesem Artikel wird die [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) verwendet, um sich bei Ihrem Azure-Konto anzumelden und auf diese Weise ein Token zu erhalten.

Wenn Sie eine Azure CLI [lokal installiert](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) haben, können Sie eine Eingabeaufforderung auf Ihrem Computer starten, um die folgenden Befehle auszuführen.
Andernfalls können Sie ein [Azure Cloud Shell](https://shell.azure.com)-Fenster im Browser öffnen und dort die Befehle ausführen.

1. Stellen Sie zunächst sicher, dass Sie mit den entsprechenden Anmeldeinformationen bei Azure angemeldet sind, indem Sie den folgenden Befehl ausführen:

    ```azurecli-interactive
    az login
    ```

1. Verwenden Sie als Nächstes den Befehl [az account get-access-token](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token), um ein Bearertoken mit Zugriff auf den Azure Digital Twins-Dienst abzurufen.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Kopieren Sie den Wert `accessToken` in das Ergebnis, und speichern Sie ihn für die Verwendung im nächsten Abschnitt. Dies ist der **Tokenwert**, den Sie dem Postman-Tool bereitstellen, um Ihre Anforderungen zu authentifizieren.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Ansicht eines lokalen Konsolenfensters, das das Ergebnis des Befehls „az account get-access-token“ anzeigt. Eines der Felder im Ergebnis wird als „accessToken“ bezeichnet, und der Beispielwert, der mit „ey“ beginnt, wird hervorgehoben.":::

>[!TIP]
>Dieses Token ist für mindestens 5 Minuten und höchstens 60 Minuten gültig. Wenn die für das aktuelle Token zugewiesene Zeit abgelaufen ist, können Sie die Schritte in diesem Abschnitt wiederholen, um ein neues Token zu erhalten.

## <a name="set-up-postman-collection-and-authorization"></a>Einrichten der Postman-Sammlung und -Autorisierung

Richten Sie als Nächstes Postman ein, um API-Anforderungen zu stellen.
Diese Schritte werden in Ihrer lokalen Postman-Anwendung ausgeführt. Öffnen Sie die Postman-Anwendung auf dem Computer.

### <a name="create-a-postman-collection"></a>Erstellen einer Postman-Sammlung

Anforderungen in Postman werden in **Sammlungen** (Gruppen von Anforderungen) gespeichert. Wenn Sie eine Sammlung erstellen, um Ihre Anforderungen zu gruppieren, können Sie allgemeine Einstellungen auf viele Anforderungen gleichzeitig anwenden. Dies kann die Autorisierung erheblich vereinfachen, wenn Sie planen, mehr als eine Anforderung mit der Azure Digital Twins-API zu erstellen, da Sie die Authentifizierung nur einmal für die gesamte Sammlung konfigurieren müssen.

1. Klicken Sie auf die Schaltfläche *+ Neue Sammlung*, um eine Sammlung zu erstellen.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Ansicht eines neu geöffneten Postman-Fensters. Die Schaltfläche „Neue Sammlung“ wird hervorgehoben.":::

1. Geben Sie im darauf folgenden Fenster *NEUE SAMMLUNG ERSTELLEN* einen **Namen** und eine optionale **Beschreibung** für die Sammlung ein.

Fahren Sie anschließend mit dem nächsten Abschnitt fort, um der Sammlung ein Bearertoken für die Autorisierung hinzuzufügen.

### <a name="add-authorization-token-and-finish-collection"></a>Hinzufügen eines Autorisierungstoken und Beenden der Sammlung

1. Wechseln Sie im Dialogfeld *NEUE SAMMLUNG ERSTELLEN* zur Registerkarte *Autorisierung*. An dieser Stelle platzieren Sie den **Tokenwert**, den Sie im Abschnitt [Abrufen des Bearertokens](#get-bearer-token) abgerufen haben, um ihn für alle API-Anforderungen in der Sammlung zu verwenden.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="Das Postman-Fenster „NEUE SAMMLUNG ERSTELLEN“, das die Registerkarte „Autorisierung“ anzeigt":::

1. Legen Sie den *Typ* auf _**OAuth 2.0**_ fest, und fügen Sie den Zugriffstoken in das Feld *Zugriffstoken* ein.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="Das Postman-Fenster „NEUE SAMMLUNG ERSTELLEN“, das die Registerkarte „Autorisierung“ anzeigt. Der Typ „OAuth 2,0“ ist ausgewählt, und das Feld „Zugriffstoken“, in das der Zugriffstokenwert eingefügt werden kann, wird hervorgehoben.":::

1. Klicken Sie nach dem Einfügen des Bearertokens auf *Erstellen*, um die Erstellung der Sammlung zu beenden.

Die neue Sammlung wird nun in ihrer Hauptansicht von Postman unter *Sammlungen* angezeigt.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Ansicht des Hauptfensters von Postman. Die neu erstellte Sammlung wird auf der Registerkarte „Sammlungen“ hervorgehoben.":::

## <a name="create-a-request"></a>Erstellen einer Anforderung

Nachdem Sie die vorherigen Schritte abgeschlossen haben, können Sie Anforderungen für die Azure Digital Twins-APIs erstellen.

1. Klicken Sie auf die Schaltfläche *+ Neu*, um eine Anforderung zu erstellen.

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Ansicht des Hauptfensters von Postman. Die Schaltfläche „Neu“ wird hervorgehoben.":::

1. Klicken Sie auf *Anforderung*.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Ansicht der Optionen, die Sie auswählen können, um etwas Neues zu erstellen. Die Option „Anforderung“ wird hervorgehoben.":::

1. Mit dieser Aktion wird das Fenster *Anforderung speichern* geöffnet, in dem Sie einen Namen und eine optionale Beschreibung für die Anforderung eingeben können. Zudem können Sie die Sammlung auswählen, zu der sie gehört. Geben Sie die Informationen ein, und speichern Sie die Anforderung in der Sammlung, die Sie zuvor erstellt haben.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Ansicht des Fensters „Anforderung speichern“, in dem Sie die beschriebenen Felder ausfüllen können. Die Schaltfläche „Save to Azure Digital Twins collection“ (In Azure Digital Twins-Sammlung speichern) wird hervorgehoben.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Nun können Sie die Anforderung in der Sammlung anzeigen und diese auswählen, um die bearbeitbaren Details aufzurufen.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Ansicht des Hauptfensters von Postman. Die Azure Digital Twins-Sammlung ist erweitert und die Anforderung „Query twins“ wird hervorgehoben. Informationen zur Anforderung werden in der Mitte der Seite angezeigt." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Festlegen von Anforderungsdetails

Sie benötigen die URL der API und die dafür erforderlichen Informationen, um eine Postman-Anforderung in einer der Azure Digital Twins-APIs zu stellen. Diese Informationen finden Sie in der [Referenzdokumentation für die REST-APIs von Azure Digital Twins](/rest/api/azure-digitaltwins/).

In diesem Artikel wird die Abfrage-API und die entsprechende [Referenzdokumentation](/rest/api/digital-twins/dataplane/query/querytwins) als Beispielabfrage verwendet, um alle digitalen Zwillinge in einer Instanz abzufragen.

1. Die URL und den Typ der Anforderung erhalten Sie in der Referenzdokumentation. Für die Abfrage-API ist dies derzeit *POST `https://digitaltwins-hostname/query?api-version=2020-10-31`* .
1. Legen Sie in Postman den Typ für die Anforderung fest, und geben Sie die Anforderungs-URL ein. Füllen Sie dabei die Platzhalter in der URL nach Bedarf aus. Hier verwenden Sie den **Hostnamen** der Instanz aus dem Abschnitt [*Voraussetzungen*](#prerequisites).
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Bei den Informationen der neuen Anforderung wurde die Abfrage-URL aus der Referenzdokumentation in das Feld „Anforderungs-URL“ eingegeben." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Überprüfen Sie, ob die Parameter, die für die Anforderung auf der Registerkarte *Parameter* angezeigt werden, den in der Referenzdokumentation beschriebenen Parametern entsprechen. Für diese Anforderung in Postman wurde der Parameter `api-version` automatisch aufgefüllt, als die Anforderungs-URL im vorherigen Schritt eingegeben wurde. Bei der Abfrage-API ist dies der einzige erforderliche Parameter, sodass dieser Schritt erledigt ist.
1. Legen Sie auf der Registerkarte *Autorisierung* den *Typ* auf *Authentifizierung von der übergeordneten Instanz erben*. Dies gibt an, dass für diese Anforderung die Authentifizierung verwendet wird, die Sie zuvor für die gesamte Sammlung eingerichtet haben.
1. Überprüfen Sie, ob die Header, die für die Anforderung auf der Registerkarte *Header* angezeigt werden, den in der Referenzdokumentation beschriebenen entsprechen. Für diese Anforderung wurden mehrere Header automatisch ausgefüllt. Bei der Abfrage-API ist keine der Headeroptionen erforderlich, sodass dieser Schritt erledigt ist.
1. Überprüfen Sie, ob der Textkörper, der für die Anforderung in der Registerkarte *Textkörper* angezeigt wird, den in der Referenzdokumentation beschriebenen Anforderungen entspricht. Für die Abfrage-API ist ein JSON-Textkörper erforderlich, um den Abfragetext bereitzustellen. Nachfolgend finden Sie ein Beispieltextkörper für diese Anforderung, die alle digitalen Zwillinge in der Instanz abfragt:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="In den Details der neuen Anforderung wird die Registerkarte „Textkörper“ angezeigt. Sie enthält einen unformatierten JSON-Textkörper mit einer Abfrage von „SELECT * FROM DIGITALTWINS“."lightbox="media/how-to-use-postman/postman-request-body.png":::

   Weitere Informationen zum Erstellen von Azure Digital Twins-Abfragen finden Sie unter [*Abfragen des Zwillingsdiagramms von Azure Digital Twins*](how-to-query-graph.md).

1. Überprüfen Sie die Referenzdokumentation für alle anderen Felder, die möglicherweise für den Typ der Anforderung erforderlich sind. Bei der Abfrage-API sind nun alle Anforderungen in der Postman-Anforderung erfüllt, sodass dieser Schritt erledigt ist.
1. Verwenden Sie die Schaltfläche *Senden*, um die abgeschlossene Anforderung zu senden.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Neben den Details der neuen Anforderung wird die Schaltfläche „Senden“ hervorgehoben." lightbox="media/how-to-use-postman/postman-request-send.png":::

Nachdem die Anforderung gesendet wurde, werden die Antwortinformationen im Postman-Fenster unterhalb der Anforderung angezeigt. Sie können den Statuscode der Antwort und jeden Textkörper anzeigen.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Unterhalb der Details der gesendeten Anforderung werden die Details der Antwort hervorgehoben. Der Status „200 OK“ und ein Textkörper, der die digitalen Zwillinge beschreibt, die von der Abfrage zurückgegeben wurden, wird angezeigt." lightbox="media/how-to-use-postman/postman-request-response.png":::

Sie können auch die Antwort mit den erwarteten Antwortdaten vergleichen, die in der Referenzdokumentation angegeben sind, um das Ergebnis zu überprüfen oder weitere Informationen zu auftretenden Fehlern zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über die Digital Twins-APIS finden Sie unter [ *Verwenden der Azure Digital Twins-APIs und SDKs*](how-to-use-apis-sdks.md) oder unter der [Referenzdokumentation für die REST-APIs](/rest/api/azure-digitaltwins/).