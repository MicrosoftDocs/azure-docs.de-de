---
title: Verwalten von Datenverkehr von Anwendungen mit mehreren Mandanten wie App Service-Web-Apps mithilfe von Azure Application Gateway – Portal
description: In diesem Artikel erfahren Sie, wie Sie Azure App Service-Web-Apps als Mitglied in Back-End-Pools für ein vorhandenes oder neues Anwendungsgateway konfigurieren.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831188"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurieren von App Service mit Application Gateway

Mit Application Gateway können Sie eine Azure App Service-App oder andere mehrinstanzenfähigen Dienste als Back-End-Poolmitglied einrichten. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> - Erstellen eines Back-End-Pools und Hinzufügen eines App-Diensts
> - Erstellen von HTTP-Einstellungen und benutzerdefiniertes Testen mit aktivierten „Pick Hostname“-Parametern (Hostname auswählen)

## <a name="prerequisites"></a>Voraussetzungen

- Anwendungsgateway: Wenn Sie noch kein Anwendungsgateway haben, erhalten Sie [hier](https://docs.microsoft.com/azure/application-gateway/quick-create-portal) Informationen zum Erstellen eines Anwendungsgateways.
- App-Dienst: Wenn Sie noch keinen App-Dienst haben, finden Sie in der [App Service-Dokumentation](https://docs.microsoft.com/azure/app-service/) weitere Informationen.

## <a name="add-app-service-as-backend-pool"></a>Hinzufügen eines App-Diensts als Back-End-Pool

1. Öffnen Sie im Azure-Portal die Konfigurationsansicht Ihres Anwendungsgateways.

2. Klicken Sie im Bereich **Back-End-Pools** auf **Hinzufügen**, um einen neuen Back-End-Pool zu erstellen.

3. Geben Sie dem Back-End-Pool einen passenden Namen. 

4. Klicken Sie unter **Ziele** auf das Dropdown, und wählen Sie als Option **App-Dienste** aus.

5. Direkt unterhalb des **Ziele**-Dropdown wird ein weiteres Dropdown angezeigt, in dem eine Liste Ihrer App-Dienste enthalten ist. Wählen Sie in dieser Dropdownliste den App-Dienst aus, den Sie als Back-End-Poolmitglied hinzufügen möchten, und klicken Sie auf „Hinzufügen“.

   ![Screenshot: App-Dienst-Backend](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Erstellen von HTTP-Einstellungen für App-Dienste

1. Klicken Sie unter **HTTP-Einstellungen** auf **Hinzufügen**, um eine neue HTTP-Einstellung zu erstellen.

2. Geben Sie einen Namen für die HTTP-Einstellung ein, und aktivieren oder deaktivieren Sie je nach Ihren Anforderung die Option „Cookiebasierte Affinität“.

3. Wählen Sie je nach Anwendungsfall als Protokoll entweder HTTP oder HTTPS aus. 

4. Klicken Sie auf das Kästchen neben **Für App Service verwenden**. Dadurch werden die Optionen **Test mit „Hostnamen aus Back-End-Adresse auswählen“ erstellen** und **Hostnamen aus Back-End-Adresse auswählen** aktiviert. Durch diese Option wird außerdem automatisch mit aktiviertem Parameter ein Test erstellt, der dann dieser HTTP-Einstellung zugeordnet wird.

5. Klicken Sie auf **OK**, um die HTTP-Einstellung zu erstellen.

   ![Screenshot: HTTP-Einstellung 1](./media/configure-web-app-portal/http-setting1.png)

   ![Screenshot: HTTP-Einstellung 2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Erstellen einer Regel zum Binden des Listeners, des Back-End-Pools und der HTTP-Einstellung

1. Klicken Sie unter **Regeln** auf **Basic**, um eine neue Regel für den Tarif „Basic“ zu erstellen.

2. Wählen Sie einen passenden Namen aus, und wählen Sie den Listener aus, der die einkommenden Anforderungen für den App-Dienst entgegennimmt.

3. Wählen Sie im Dropdown für **Back-End-Pool** den Back-End-Pool aus, den Sie oben erstellt haben.

4. Wählen Sie im Dropdown für **HTTP-Einstellung** die HTTP-Einstellung aus, die Sie oben erstellt haben.

5. Klicken Sie auf **OK**, um diese Regel zu speichern.

   ![Regel](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Beschränken des Zugriffs

Die in diesen Beispielen bereitgestellten Web-Apps verwenden öffentliche IP-Adressen, auf die aus dem Internet direkt zugegriffen werden kann. Dies hilft bei der Problembehandlung, wenn Sie von einem neuen Feature erfahren und neue Sachen ausprobieren. Wenn Sie jedoch ein Feature in einer Produktionsumgebung bereitstellen möchten, ist es sinnvoll, stärkere Einschränkungen vorzusehen.

Eine Möglichkeit, wie Sie den Zugriff auf Ihre Web-Apps einschränken können, besteht in der Verwendung von [Statischen Azure App Service-IP-Einschränkungen](../app-service/app-service-ip-restrictions.md). Beispielsweise können Sie die Web-App so einschränken, dass sie nur Datenverkehr vom Anwendungsgateway empfängt. Verwenden Sie die IP-Einschränkungsfunktion von App Service, um die Anwendungsgateway-VIP als einzige Adresse aufzulisten, auf die zugegriffen werden kann.

## <a name="next-steps"></a>Nächste Schritte

[In diesem Artikel](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) finden Sie weitere Informationen zu App Service und anderen Möglichkeiten der Application Gateway-Unterstützung für mehrinstanzenfähige Back-Ends.

Falls Sie von der Antwort Ihres App-Diensts zur URL des App-Diensts weitergeleitet werden, finden Sie unter [Troubleshoot Application Gateway with App Service – Redirection to App Service’s URL (Troubleshooting in Application Gateway mit App Service – Weiterleitung zur URL eines App-Diensts)](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url) weitere Informationen.
