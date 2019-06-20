---
title: Herstellen einer Verbindung mit lokalen Dateisystemen – Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die über das lokale Datengateway in Azure Logic Apps eine Verbindung mit lokalen Dateisystemen mit dem Dateisystem-Connector herstellen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: a933824e9553ee7f638da495b46ebed19e04169d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846772"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Herstellen einer Verbindung mit lokalen Dateisystemen mit Azure Logic Apps

Mit dem Dateisystem-Connector und Azure Logic Apps können Sie automatisierte Aufgaben und Workflows erstellen, die Dateien auf einer lokalen Dateifreigabe erstellen und verwalten, um beispielsweise folgende Aufgaben auszuführen:  

- Erstellen, Abrufen, Anfügen, Aktualisieren und Löschen von Dateien.
- Auflisten von Dateien in Ordnern oder Stammordnern
- Abrufen von Dateiinhalten und Metadaten

In diesem Artikel erfahren Sie anhand des folgenden Beispielszenarios, wie Sie eine Verbindung mit einem lokalen Dateisystem herstellen. In dem Szenario kopieren Sie eine in Dropbox hochgeladene Datei in eine Dateifreigabe und senden anschließend eine E-Mail. Logik-Apps verwenden das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md), um eine sichere Verbindung mit lokalen Systemen herzustellen und auf sie zuzugreifen. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Bevor Sie eine Verbindung zwischen Logik-Apps und lokalen Systemen wie etwa Ihrem Dateisystemserver herstellen können, müssen Sie [ein lokales Datengateway installieren und einrichten](../logic-apps/logic-apps-gateway-install.md). So können Sie beim Erstellen der Dateisystemverbindung in Ihrer Logik-App angeben, dass die Gatewayinstallation verwendet werden soll.

* Ein [Dropbox-Konto](https://www.dropbox.com/), für das Sie sich kostenlos registrieren können. Ihre Kontoanmeldeinformationen sind zum Herstellen einer Verbindung zwischen Ihrer Logik-App und Ihrem Dropbox-Konto erforderlich. 

* Zugriff auf den Computer mit dem Dateisystem, das Sie verwenden. Wenn Sie z.B. das Datengateway auf dem gleichen Computer installieren, auf dem sich Ihr Dateisystem befindet, benötigen Sie die Anmeldeinformationen für diesen Computer. 

* Ein E-Mail-Konto eines von Logic Apps unterstützten E-Mail-Anbieters wie etwa Office 365 Outlook, Outlook.com oder Gmail. Informationen zu Connectors für andere Anbieter finden Sie in [dieser Liste](https://docs.microsoft.com/connectors/). In dieser Logik-App verwenden wir ein Office 365 Outlook-Konto. Bei Verwendung eines anderen E-Mail-Kontos sind die Schritte im Großen und Ganzen identisch, aber die Benutzeroberfläche weicht ggf. etwas ab. 

* Grundlegende Kenntnisse über das [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Für dieses Beispiel benötigen Sie eine leere Logik-App.

## <a name="add-trigger"></a>Hinzufügen des Triggers

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Suchfeld den Begriff „dropbox“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Wenn eine Datei erstellt wird**

   ![Auswählen des Dropbox-Triggers](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Melden Sie sich mit den Anmeldeinformationen Ihres Dropbox-Kontos an, und autorisieren Sie den Zugriff auf Ihre Dropbox-Daten durch Azure Logic Apps. 

1. Geben Sie die erforderlichen Informationen für Ihren Trigger an.

   ![Dropbox-Trigger](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Hinzufügen von Aktionen

1. Klicken Sie unter dem Trigger auf **Nächster Schritt**. Geben Sie im Suchfeld „Dateisystem“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Datei erstellen – Dateisystem**

   ![Suchen nach dem Dateisystem-Connector](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Wenn noch keine Verbindung mit Ihrem Dateisystem besteht, werden Sie aufgefordert, eine Verbindung herzustellen.

   ![Erstellen der Verbindung](media/logic-apps-using-file-connector/file-system-connection.png)

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG | 
   | -------- | -------- | ----- | ----------- | 
   | **Verbindungsname** | Ja | <*verbindungsname*> | Der gewünschte Name für die Verbindung. | 
   | **Stammordner** | Ja | <*Name des Stammordners*> | Der Stammordner für Ihr Dateisystem – beispielsweise, wenn Sie Ihr lokales Datengateway installiert haben, ein lokaler Ordner auf dem Computer, auf dem das lokale Datengateway installiert ist, oder der Ordner für eine Netzwerkfreigabe, auf die der Computer zugreifen kann. <p>Beispiel: `\\PublicShare\\DropboxFiles` <p>Der Stammordner ist der übergeordnete Hauptordner, der bei allen dateibezogenen Aktionen für relative Pfade verwendet wird. | 
   | **Authentifizierungstyp** | Nein | <*Authentifizierungstyp*> | Die Art der Authentifizierung, die von Ihrem Dateisystem verwendet wird (beispielsweise **Windows**). | 
   | **Benutzername** | Ja | <*Domäne*>\\<*Benutzername*> | Der Benutzername für den Computer, auf dem sich das Dateisystem befindet | 
   | **Kennwort** | Ja | <*Ihr Kennwort*> | Das Kennwort für den Computer, auf dem sich das Dateisystem befindet | 
   | **Gateway** | Ja | <*Name des installierten Gateways*> | Der Name für das zuvor installierte Gateway. | 
   ||| 

1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

   Die Verbindung wird in Logic Apps konfiguriert und getestet, sodass sichergestellt ist, dass sie ordnungsgemäß funktioniert. 
   Wenn die Verbindung ordnungsgemäß eingerichtet ist, werden Optionen für die zuvor ausgewählte Aktion angezeigt. 

1. Geben Sie unter der Aktion **Datei erstellen** die Details an, die benötigt werden, um Dateien aus Dropbox in den Stammordner auf Ihrer lokalen Dateifreigabe zu kopieren. Um Ausgaben aus den vorherigen Schritten hinzuzufügen, klicken Sie auf die Felder. Daraufhin wird eine Liste mit dynamischen Inhalten angezeigt, in der Sie verfügbare Felder auswählen können.

   ![Erstellen einer Dateiaktion](media/logic-apps-using-file-connector/create-file-filled.png)

1. Fügen Sie nun eine Outlook-Aktion hinzu, die eine E-Mail sendet, damit die entsprechenden Benutzer über die neue Datei informiert werden. Geben Sie die Empfänger, den Titel und Text der E-Mail ein. Zu Testzwecken können Sie hier Ihre eigene E-Mail-Adresse angeben.

   ![Aktion „E-Mail senden“](media/logic-apps-using-file-connector/send-email.png)

1. Speichern Sie Ihre Logik-App. Testen Sie die App durch Hochladen einer Datei in Dropbox. 

   Ihre Logik-App sollte die Datei in Ihre lokale Dateifreigabe kopieren und den Empfängern eine E-Mail senden, um sie über die kopierte Datei zu informieren.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/fileconnector/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Zur Optimierung von Azure Logic Apps und Connectors können Sie auf der [Azure Logic Apps-Website für Benutzerfeedback](https://aka.ms/logicapps-wish) über Ideen abstimmen oder selbst Ideen einreichen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum [Herstellen einer Verbindung mit lokalen Daten](../logic-apps/logic-apps-gateway-connection.md) 
* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
