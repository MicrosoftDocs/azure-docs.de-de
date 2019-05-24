---
title: Includedatei
description: Includedatei
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: a5d29e77f6ba10ed3069cb3f5a3c8089f49c237d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140603"
---
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü auf der linken Seite die Option **Alle Dienste**, und wählen Sie im Abschnitt **Mobil** die Option **Notification Hubs**. Wählen Sie das Sternchen (`*`) neben dem Dienstnamen, um den Dienst im linken Menü dem Abschnitt **FAVORITEN** hinzuzufügen. Nachdem **Notification Hubs** unter **FAVORITEN** hinzugefügt wurde, können Sie diesen Eintrag im Menü auf der linken Seite auswählen. 

      ![Azure-Portal – Auswählen von Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Wählen Sie auf der Seite **Notification Hubs** in der Symbolleiste die Option **Hinzufügen**. 

      ![Notification Hubs – Symbolleistenschaltfläche „Hinzufügen“](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Führen Sie auf der Seite **Notification Hub** die folgenden Schritte aus: 
    1. Geben Sie einen **Namen** für den **Notification Hub** an.  
    2. Geben Sie einen **Namen** für den **Namespace** an. Ein Namespace enthält mindestens einen Hub. 
    3. Wählen Sie einen **Ort** aus, an dem der Notification Hub erstellt werden soll. 
    4. Wählen Sie eine vorhandene Ressourcengruppe aus, oder geben Sie einen Namen für die neue **Ressourcengruppe** ein.
    5. Klicken Sie auf **Erstellen**. 

        ![Azure-Portal – Festlegen von Eigenschaften für den Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Wählen Sie **Benachrichtigungen** (Glockensymbol) und **Zu Ressource wechseln** aus. Sie können auch die Liste auf der Seite **Notification Hubs** aktualisieren und Ihren Notification Hub auswählen. 

      ![Azure-Portal – Benachrichtigungen -> Zu Ressource wechseln](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Wählen Sie in der Liste die Option **Zugriffsrichtlinien** aus. Notieren Sie sich die beiden Verbindungszeichenfolgen, die für Sie verfügbar sind. Sie werden später für die Behandlung von Pushbenachrichtigungen benötigt.

      >[!IMPORTANT]
      >Verwenden Sie **NICHT** „DefaultFullSharedAccessSignature“ in Ihrer Anwendung. Diese ist nur für die Verwendung in Ihrem Back-End vorgesehen.
      >

      ![Azure-Portal – Verbindungszeichenfolgen für den Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
