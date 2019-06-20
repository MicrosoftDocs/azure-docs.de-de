---
title: Aktualisieren von Azure Analysis Services-Modellen mit Logic Apps | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die asynchrone Aktualisierung mit Azure Logic Apps programmieren.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142482"
---
# <a name="refresh-with-logic-apps"></a>Aktualisieren mit Logic Apps

Mit Logic Apps und REST-Aufrufen können Sie automatisierte Datenaktualisierungsvorgänge für Ihre tabellarischen Azure Analysis Services-Modelle durchführen, einschließlich der Synchronisierung von schreibgeschützten Replikaten zum horizontalen Skalieren von Abfragen.

Weitere Informationen zur Verwendung von REST-APIs mit Azure Analysis Services finden Sie unter [Asynchrones Aktualisieren mit der REST-API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Alle Aufrufe müssen mit einem gültigen Azure Active Directory-Token (OAuth 2) authentifiziert werden.  In den Beispielen in diesem Artikel wird ein Dienstprinzipalname (Service Principal Name, SPN) für die Authentifizierung bei Azure Analysis Services verwendet. Weitere Informationen finden Sie unter [Erstellen eines Dienstprinzipals im Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Entwerfen der Logik-App

> [!IMPORTANT]
> Die folgenden Beispiele setzen voraus, dass die Azure Analysis Services-Firewall deaktiviert ist.  Wenn die Firewall aktiviert ist, muss die öffentliche IP-Adresse des Anforderungsinitiators in der Whitelist der Azure Analysis Services-Firewall enthalten sein. Weitere Informationen zu den Logik-App-IP-Adressbereichen pro Region finden Sie unter [Grenzwert- und Konfigurationsinformationen für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Voraussetzungen

#### <a name="create-a-service-principal-spn"></a>Erstellen eines Dienstprinzipals (SPN)

Informationen zum Erstellen eines Dienstprinzipals finden Sie unter [Erstellen eines Dienstprinzipals im Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurieren von Berechtigungen in Azure Analysis Services
 
Der von Ihnen erstellte Dienstprinzipal muss über Serveradministratorberechtigungen auf dem Server verfügen. Weitere Informationen finden Sie unter [Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurieren der Logik-App

In diesem Beispiel ist die Logik-App so konzipiert, dass sie beim Empfang einer HTTP-Anforderung ausgelöst wird. Daher kann ein Orchestrierungstool wie Azure Data Factory verwendet werden, um die Aktualisierung des Azure Analysis Services-Modells auszulösen.

Nach dem Erstellen einer Logik-App:

1. Wählen Sie im Logik-App-Designer als erste Aktion **Beim Empfang einer HTTP-Anforderung** aus.

   ![Hinzufügen der Aktivität „Beim Empfang einer HTTP-Anforderung“](./media/analysis-services-async-refresh-logic-app/1.png)

Dieser Schritt wird mit der HTTP-POST-URL aufgefüllt, sobald die Logik-App gespeichert wurde.

2. Fügen Sie einen neuen Schritt hinzu, und suchen Sie nach **HTTP**.  

   ![Hinzufügen einer Aktivität „HTTP“](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Hinzufügen einer Aktivität „HTTP“](./media/analysis-services-async-refresh-logic-app/10.png)

3. Wählen Sie **HTTP** aus, um diese Aktion hinzuzufügen.

   ![Hinzufügen einer Aktivität „HTTP“](./media/analysis-services-async-refresh-logic-app/2.png)

Konfigurieren Sie die Aktivität „HTTP“ wie folgt:

|Eigenschaft  |Wert  |
|---------|---------|
|**Methode**     |POST         |
|**URI**     | https://*Ihre Serverregion*/servers/*Name des AAS-Servers*/models/*Name Ihrer Datenbank*/ <br /> <br /> Beispiel: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Header**     |   Content-Type, application/json <br /> <br />  ![Header](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Text**     |   Weitere Informationen zum Bilden des Anforderungstexts finden Sie unter [Asynchrones Aktualisieren mit der REST-API – POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Authentifizierung**     |Active Directory OAuth         |
|**Mandant**     |Geben Sie Ihre Azure Active Directory-Mandanten-ID ein.         |
|**Zielgruppe**     |https://*.asazure.windows.net         |
|**Client-ID**     |Geben Sie die Client-ID für Ihren Dienstprinzipalnamen ein.         |
|**Typ der Anmeldeinformationen**     |`Secret`         |
|**Geheimnis**     |Geben Sie das Geheimnis für Ihren Dienstprinzipalnamen ein.         |

Beispiel:

![Fertige Aktivität „HTTP“](./media/analysis-services-async-refresh-logic-app/7.png)

Testen Sie nun die Logik-App.  Klicken Sie im Logik-App-Designer auf **Ausführen**.

![Testen der Logik-App](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Nutzen der Logik-App mit Azure Data Factory

Nachdem die Logik-App gespeichert wurde, überprüfen Sie die Aktivität **Beim Empfang einer HTTP-Anforderung**, und kopieren Sie dann die **HTTP-POST-URL**, die jetzt generiert wurde.  Diese URL kann von Azure Data Factory verwendet werden, um den asynchronen Aufruf zum Auslösen der Logik-App auszuführen.

Hier sehen Sie ein Beispiel für eine Azure Data Factory-Webaktivität, die diese Aktion ausführt.

![Data Factory-Webaktivität](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Verwenden einer eigenständigen Logik-App

Wenn Sie nicht vorhaben, ein Orchestrierungstool wie Data Factory zum Auslösen der Modellaktualisierung zu verwenden, können Sie die Logik-App so einrichten, dass sie die Aktualisierung basierend auf einem Zeitplan auslöst.

Löschen Sie im obigen Beispiel die erste Aktivität, und ersetzen Sie sie durch eine Aktivität **Zeitplan**.

![Aktivität „Zeitplan“](./media/analysis-services-async-refresh-logic-app/12.png)

![Aktivität „Zeitplan“](./media/analysis-services-async-refresh-logic-app/13.png)

In diesem Beispiel wird **Wiederholung** verwendet.

Nachdem die Aktivität hinzugefügt wurde, konfigurieren Sie das Intervall und die Häufigkeit, fügen Sie dann einen neuen Parameter hinzu, und wählen Sie **Zu diesen Stunden** aus.

![Aktivität „Zeitplan“](./media/analysis-services-async-refresh-logic-app/16.png)

Wählen Sie die gewünschten Stunden aus.

![Aktivität „Zeitplan“](./media/analysis-services-async-refresh-logic-app/15.png)

Speichern Sie die Logik-App.

## <a name="next-steps"></a>Nächste Schritte

[Beispiele](analysis-services-samples.md)  
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)
