---
title: Registrieren Ihrer Anwendung – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Eine schrittweise Anleitung zum Registrieren einer neuen App bei Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: c56aef180f5e16d2ec1931caabe04295ef288ca9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509772"
---
# <a name="register-your-application"></a>Anwendung registrieren

Wenn Sie Custom Decision Service für Ihre Anwendung verwenden möchten, registrieren Sie den Dienst im Portal. Die Vorgehensweise wird in diesem Artikel erläutert.

1. Navigieren Sie zur [Startseite](https://portal.ds.microsoft.com/) von Custom Decision Service. Klicken Sie auf dem Menüband auf **Mein Portal**, wie in der Abbildung hervorgehoben dargestellt:

    ![Mein Portal](./media/portal.png)

    Wenn Sie nicht bereits angemeldet sind, werden Sie im Portal aufgefordert, sich mit Ihrem [Microsoft-Konto](https://account.microsoft.com/account) anzumelden. Nachdem Sie sich angemeldet haben, wird im Portal Ihr Microsoft-Konto in der oberen rechten Ecke der Seite angezeigt.

2. Klicken Sie zum Registrieren Ihrer Anwendung auf die Schaltfläche **Neue App**.

3. Wählen Sie im Dialogfeld eine App-ID für Ihre Anwendung aus. Custom Decision Service erfordert eine eindeutige ID für jede Anwendung. Wenn eine andere Person diese ID bereits verwendet, werden Sie vom System aufgefordert, eine andere ID auszuwählen.

4. Geben Sie eine Aktionssatz-API an. Diese Einstellung ist ein RSS- oder Atom-Feed, der die verfügbaren Inhalte für Ihre Anwendung an Custom Decision Service kommuniziert. Geben Sie einen Namen für den Feed ein, und geben Sie die URL ein, über die er bereitgestellt wird. Um diesen Schritt später auszuführen, klicken Sie auf die Schaltfläche **Feeds**, und klicken Sie dann auf die Schaltfläche **Neuer Feed**. Ein Beispiel, in dem ein RSS-Feed erstellt wird, wird an späterer Stelle beschrieben.

5. Aktiveren Sie zum Registrieren Ihrer Anwendung das Kontrollkästchen **Benutzerdefinierte App** in der unteren linken Ecke. Geben Sie eine [Verbindungszeichenfolge](../../storage/common/storage-configure-connection-string.md) für das Azure-Speicherkonto ein, in dem Ihre Anwendungsdaten protokolliert werden. Weitere Informationen zum Erstellen eines Speicherkontos finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Nächste Schritte

* Erste Schritte zum Optimieren einer [Webseite](custom-decision-service-get-started-browser.md) oder einer [Smartphone-App](custom-decision-service-get-started-app.md).
* Abschließen Sie eines [Tutorials](custom-decision-service-tutorial-news.md), um ein tiefergehendes Beispiel kennenzulernen.
* Weitere Informationen zu den bereitgestellten Funktionen finden Sie in der [API-Referenz](custom-decision-service-api-reference.md).
