---
title: Ändern des Tarifs bei Notification Hubs-Namespace | Microsoft-Dokumentation
description: Informationen zum Ändern des Tarifs bei Azure Notification Hubs-Namespace.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: 99ea21b3eb01a674a89c70a1b923f02e600cc3c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60234454"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Ändern des Tarifs bei Azure Notification Hubs-Namespace
Notification Hubs wird in drei Tarifen angeboten: **Free**, **Basic** und **Standard**. In diesem Artikel wird das Ändern des Tarifs für einen Azure Notification Hubs-Namespace veranschaulicht. 

## <a name="overview"></a>Übersicht
In Azure Notification Hubs ist ein **Hub** die kleinste Ressource/Entität. Er ist in der Regel einer einzigen Anwendung zugeordnet und kann für jedes Plattformbenachrichtigungssystem, das für die App unterstützt wird, ein Zertifikat aufnehmen. Die Anwendung kann hybrid oder nativ und plattformübergreifend sein.

Ein **Namespace** ist eine Sammlung von Benachrichtigungshubs. Jeder Namespace besteht in der Regel aus Hubs, die im Zusammenhang stehen und für einen bestimmten Zweck verwendet werden. Sie können z.B. drei verschiedene Namespaces für Entwicklungs-, Test- und Produktionszwecke haben. 

Sie können einen Tarif auf Namespaceebene zuordnen. Notification Hubs unterstützt drei Tarife: **Free**, **Basic** und **Standard**. Sie können den Tarif für einen Namespace verwenden, der Ihren Anforderungen am besten entspricht. Die folgenden Abschnitte zeigen Ihnen, wie Sie den Tarif eines Notification Hubs-Namespace ändern. 

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals 
Wenn Sie das Azure-Portal verwenden, können Sie den Tarif für einen Namespace auf der Namespaceseite oder einer Hubseite ändern.  Wenn Sie ihn auf einer Hubseite ändern, ändern Sie ihn tatsächlich auf Namespaceebene. Der Tarif wird für den Namespace und alle Hubs im Namespace geändert. 

### <a name="change-tier-on-the-namespace-page"></a>Ändern des Tarifs auf der Namespaceseite
Mit den Schritten des folgenden Verfahrens ändern Sie den Tarif für einen Namespace auf der Namespaceseite. Wenn Sie den Tarif für einen Namespace ändern, gilt dies für alle Hubs im Namespace.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü links **Alle Dienste** aus. 
3. Wählen Sie **Notification Hub-Namespaces** im Abschnitt **Internet der Dinge** aus. Bei Auswahl des Sterns (`*`) neben dem Text wird der Eintrag der linken Navigationsleiste unter **Favoriten** hinzugefügt. Dann können Sie ab dem nächsten Mal schneller auf die Namespacesseite zugreifen. Wählen Sie **Notification Hub-Namespaces** aus, nachdem Sie den Eintrag den Favoriten hinzugefügt haben. 

    ![Alle Dienste -> Notification Hub-Namespaces](./media/change-pricing-tier/all-services-nhub.png)
1. Wählen Sie auf der Seite **Notification Hub-Namespaces** den Namespace aus, für den Sie den Tarif ändern möchten. 
2. Auf der Seite **Notification Hub-Namespace** für Ihren Namespace finden Sie im Abschnitt **Essentials** den aktuellen Tarif für den Namespace. In der folgenden Abbildung sehen Sie, dass dem Namespace der Tarif **Free** zugeordnet ist. 

    ![Ändern des aktuellen Tarifs auf der Namespaceseite](./media/change-pricing-tier/pricing-tier-before.png)
1. Wählen Sie auf der Seite **Notification Hub-Namespace** für Ihren Namespace **Tarif** im Abschnitt **Verwalten** aus. 

    ![Auswählen des Tarifs auf der Namespaceseite](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Ändern Sie Ihren Tarif, und klicken Sie auf die Schaltfläche **Auswählen**.    
7. Sie sehen den Status der Tarifänderung unter **Warnungen**. 
8. Wechseln Sie zur Seite **Übersicht**. Vergewissern Sie sich, dass der neue Tarif für das **Tarif**-Feld im Abschnitt **Essentials** angezeigt wird.     
1. Dieser Schritt ist optional. Wählen Sie einen beliebigen Hub im Namespace aus. Überprüfen Sie, ob derselbe Tarif im Abschnitt **Essentials** angezeigt wird. Derselbe Tarif sollte für alle Hubs im Namespace angezeigt werden. 

### <a name="change-tier-on-the-hub-page"></a>Ändern des Tarifs auf der Hubseite
Mit den Schritten des folgenden Verfahrens ändern Sie den Tarif für einen Namespace auf der Hubseite. Auch wenn Sie die folgenden Schritte auf der Hubseite beginnen, ändern Sie den Tarif für den Namespace und alle Hubs tatsächlich im Namespace. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü links **Alle Dienste** aus.
3. Wählen Sie **Notification Hubs** im Abschnitt **Internet der Dinge** aus. 
4. Wählen Sie Ihre Notification **Hub**-Instanz aus. 
5. Wählen Sie **Tarif** im linken Menü aus. 
6. Ändern Sie den Tarif, und klicken Sie auf die Schaltfläche **Auswählen**. Diese Aktion ändert die Einstellung des Tarifs für den Namespace, der den Hub enthält. Darum sehen Sie den neuen Tarif auf der Namespaceseite und allen Hubseiten. 

## <a name="use-rest-api"></a>REST-API
Sie können mit den folgenden Ressourcenanbieter-REST-APIs den aktuellen Tarif abrufen und ihn aktualisieren. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Abrufen des aktuellen Tarif für einen Namespace
Zum Abrufen des **aktuellen Namespacetarifs** senden Sie einen GET-Befehl, wie im folgenden Beispiel gezeigt: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aktualisieren des Tarifs für einen Namespace
Zum **Aktualisieren des Namespacetarifs** senden Sie einen PUT-Befehl, wie im folgenden Beispiel gezeigt: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu diesen Tarifen und Preisen finden Sie unter [Notification Hubs – Preise](https://azure.microsoft.com/pricing/details/notification-hubs/).
