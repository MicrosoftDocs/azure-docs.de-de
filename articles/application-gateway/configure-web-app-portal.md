---
title: Verwalten von Datenverkehr zu mehrinstanzenfähigen Apps mithilfe des Portals
titleSuffix: Azure Application Gateway
description: In diesem Artikel erfahren Sie, wie Sie Azure App Service-Web-Apps als Mitglied in Back-End-Pools für ein vorhandenes oder neues Anwendungsgateway konfigurieren.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: victorh
ms.openlocfilehash: a72f0106088d26eb2ff53456840c598c3d9619a7
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397551"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurieren von App Service mit Application Gateway

Da es sich bei App Service nicht um eine dedizierte Bereitstellung, sondern um einen mehrinstanzenfähigen Dienst handelt, wird der Hostheader in der eingehenden Anforderung verwendet, um die Anforderung für den richtigen App Service-Endpunkt aufzulösen. In der Regel unterscheidet sich der DNS-Name der Anwendung – der wiederum dem DNS-Namen entspricht, der dem Anwendungsgateway für den App Service zugeordnet ist – vom Domänennamen des Back-End-Diensts. Deshalb ist der Hostheader, der in der vom Anwendungsgateway empfangenen ursprünglichen Anforderung enthalten ist, ein anderer als der Hostname des Back-End-Diensts. Wenn der Hostheader in der Anforderung vom Anwendungsgateway also nicht in den Hostnamen des Back-End-Diensts geändert wird, können mehrinstanzenfähige Back-Ends die Anforderung nicht zum richtigen Endpunkt auflösen.

Application Gateway verfügt über den Switch `Pick host name from backend target`, bei dem der Hostheader der Anforderung durch den Hostnamen des Back-Ends überschrieben wird, wenn die Anforderung vom Application Gateway an das Back-End geleitet wird. So werden mehrinstanzenfähige Back-Ends unterstützt, z. B. Azure App Service und API Management. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

- Bearbeiten eines Back-End-Pools und Hinzufügen eines App Service zu diesem
- Bearbeiten von HTTP-Einstellungen mit aktivierten „Pick Hostname“-Parameter (Hostnamen auswählen)

## <a name="prerequisites"></a>Voraussetzungen

- Anwendungsgateway: Erstellen eines Anwendungsgateways ohne ein Back-End-Poolziel. Weitere Informationen finden Sie unter [Quickstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal](quick-create-portal.md)

- App-Dienst: Wenn Sie noch keinen App-Dienst haben, finden Sie in der [App Service-Dokumentation](../app-service/index.yml) weitere Informationen.

## <a name="add-app-service-as-backend-pool"></a>Hinzufügen eines App-Diensts als Back-End-Pool

1. Wählen Sie im Azure-Portal Ihr Anwendungsgateway aus.

2. Wählen Sie unter **Back-End-Pools** den Back-End-Pool aus.

4. Wählen Sie unter **Zieltyp** die Option **App Services** aus.

5. Wählen Sie unter **Ziel** Ihren App Service aus.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="Screenshot: App-Dienst-Backend":::
   
   > [!NOTE]
   > Die Dropdownliste wird nur mit den App Services aufgefüllt, die sich unter demselben Abonnement wie Ihr Application Gateway befinden. Wenn Sie einen App Service verwenden möchten, der sich unter einem anderen Abonnement als dem Abonnement des Application Gateway befindet, sollten Sie anstelle von **App Services** in der Dropdownliste **Ziele** die Option **IP-Adresse oder Hostname** wählen und den Hostnamen des App Service eingeben (z. B. „azurewebsites.net“).
1. Wählen Sie **Speichern** aus.

## <a name="edit-http-settings-for-app-service"></a>Bearbeiten von HTTP-Einstellungen für App Service

1. Wählen Sie unter **HTTP-Einstellungen** die vorhandene HTTP-Einstellung aus.

2. Wählen Sie unter **Mit neuem Hostnamen überschreiben** die Option **Ja** aus.
3. Wählen Sie unter **Hostnamen überschreiben** die Option **Hostnamen aus Back-End-Ziel auswählen** aus.
4. Wählen Sie **Speichern** aus.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Hostnamen aus Back-End auswählen: HTTP-Einstellungen":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Zusätzliche Konfiguration bei einer Umleitung an den relativen App Service-Pfad

Wenn der App Service eine Umleitungsantwort an den Client sendet, um eine Umleitung zum relativen Pfad durchzuführen (z. B. eine Umleitung von `contoso.azurewebsites.net/path1` zu `contoso.azurewebsites.net/path2`), wird im Adressheader der Antwort der gleiche Hostname wie in der Anforderung verwendet, die vom Anwendungsgateway gesendet wurde. So sendet der Client die Anforderung direkt an `contoso.azurewebsites.net/path2` und durchläuft nicht das Anwendungsgateway (`contoso.com/path2`). Das Umgehen des Anwendungsgateways ist nicht wünschenswert.

Wenn in Ihrem Fall Szenarien vorhanden sind, in denen der App Service eine Umleitungsantwort an den Client senden muss, sollten Sie die [zusätzlichen Schritte zum erneuten Generieren des Adressheaders](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration) ausführen.

## <a name="restrict-access"></a>Beschränken des Zugriffs

Die in diesen Beispielen bereitgestellten Web-Apps verwenden öffentliche IP-Adressen, auf die aus dem Internet direkt zugegriffen werden kann. Dies hilft bei der Problembehandlung, wenn Sie von einem neuen Feature erfahren und neue Sachen ausprobieren. Wenn Sie jedoch ein Feature in einer Produktionsumgebung bereitstellen möchten, ist es sinnvoll, stärkere Einschränkungen vorzusehen.

Eine Möglichkeit, wie Sie den Zugriff auf Ihre Web-Apps einschränken können, besteht in der Verwendung von [Statischen Azure App Service-IP-Einschränkungen](../app-service/app-service-ip-restrictions.md). Beispielsweise können Sie die Web-App so einschränken, dass sie nur Datenverkehr vom Anwendungsgateway empfängt. Verwenden Sie die IP-Einschränkungsfunktion von App Service, um die Anwendungsgateway-VIP als einzige Adresse aufzulisten, auf die zugegriffen werden kann.

## <a name="next-steps"></a>Nächste Schritte

[In diesem Artikel](./application-gateway-web-app-overview.md) finden Sie weitere Informationen zu App Service und anderen Möglichkeiten der Application Gateway-Unterstützung für mehrinstanzenfähige Back-Ends.