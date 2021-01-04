---
title: include file
description: include file
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 2ec602f056b339a1b1dcb78d6b8d7583aeaf0434
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009104"
---
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Menü auf der linken Seite die Option **Alle Dienste** und dann im Abschnitt **Mobil** die Option **Notification Hubs** aus. Wählen Sie das Sternsymbol neben dem Dienstnamen aus, um den Dienst im linken Menü zum Abschnitt **FAVORITEN** hinzuzufügen. Nachdem **Notification Hubs** unter **FAVORITEN** hinzugefügt wurde, können Sie diesen Eintrag im Menü auf der linken Seite auswählen.

      ![Azure-Portal – Auswählen von Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Wählen Sie auf der Seite **Notification Hubs** in der Symbolleiste die Option **Hinzufügen**.

      ![Notification Hubs – Symbolleistenschaltfläche „Hinzufügen“](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Führen Sie auf der Seite **Notification Hub** die folgenden Schritte aus:

    1. Geben Sie ins Feld **Notification Hub** einen Namen ein.  

    1. Geben Sie ins Feld **Neuen Namespace erstellen** einen Namen ein. Ein Namespace enthält mindestens einen Hub.

    1. Wählen Sie im Dropdown-Listenfeld **Standort** einen Wert aus. Dieser Wert gibt den Standort an, an dem der Hub erstellt werden soll.

    1. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie einen Namen für eine neue Ressourcengruppe.

    1. Klicken Sie auf **Erstellen**.

        ![Azure-Portal – Festlegen von Eigenschaften für den Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Wählen Sie **Benachrichtigungen** (Glockensymbol) und dann **Zu Ressource wechseln** aus. Sie können auch die Liste auf der Seite **Notification Hubs** aktualisieren und Ihren Hub auswählen.

      ![Azure-Portal: zu Ressource navigieren](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Wählen Sie in der Liste die Option **Zugriffsrichtlinien** aus. Notieren Sie sich die beiden Verbindungszeichenfolgen, die für Sie verfügbar sind. Sie werden später für die Behandlung von Pushbenachrichtigungen benötigt.

      >[!IMPORTANT]
      >Verwenden Sie *nicht* die Richtlinie **DefaultFullSharedAccessSignature** in Ihrer Anwendung. Diese ist nur für die Verwendung in Ihrem Back-End vorgesehen.
      >

      ![Azure-Portal – Verbindungszeichenfolgen für den Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
