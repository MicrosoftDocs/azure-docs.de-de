---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66139834"
---
Standardmäßig können APIs auf Mobile Apps-Back-Ends anonym aufgerufen werden. Als Nächstes müssen Sie den Zugriff auf authentifizierte Clients beschränken.  

* **Node.js-Back-End (über das Azure-Portal):**  

    Klicken Sie in den Einstellungen von Mobile Apps auf **Einfache Tabellen**, und wählen Sie Ihre Tabelle aus. Klicken Sie auf **Berechtigungen ändern**, wählen Sie für alle Berechtigungen **Authenticated access only** (Nur authentifizierter Zugriff) aus, und klicken Sie auf **Speichern**.
* **.NET-Back-End (C#):**  

    Navigieren Sie im Serverprojekt zu **Controller** > **TodoItemController.cs**. Fügen Sie das `[Authorize]`-Attribut der **TodoItemController**-Klasse wie folgt hinzu: Um den Zugriff auf bestimmte Methoden zu beschränken, können Sie dieses Attribut auch nur auf diese Methoden anstelle der Klasse anwenden. Veröffentlichen Sie das Serverprojekt erneut.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js-Back-End (über Node.js-Code)** :  

    Um für den Zugriff auf Tabellen eine Authentifizierung anzufordern, fügen Sie die folgende Zeile in das Node.js-Serverskript ein:

        table.access = 'authenticated';

    Weitere Details finden Sie unter [Gewusst wie: Erzwingen der Authentifizierung für den Zugriff auf Tabellen](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Informationen zum Herunterladen des Schnellstart-Codeprojekts von Ihrer Website finden Sie unter [Gewusst wie:  Herunterladen des Schnellstart-Codeprojekts für das Node.js-Back-End mithilfe von Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
