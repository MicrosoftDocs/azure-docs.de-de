---
title: Herstellen einer Verbindung mit Azure Blob Storage
description: Erstellen und Verwalten von Blobs in Azure-Speicherkonten mithilfe von Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677728"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Erstellen und Verwalten von Blobs in Azure Blob Storage mithilfe von Azure Logic Apps

In diesem Artikel wird veranschaulicht, wie Sie auf Dateien zugreifen und diese verwalten können, die als Blobs in Ihrem Azure-Speicherkonto gespeichert sind, indem Sie eine Logik-App und den Azure Blob Storage-Connector verwenden. Auf diese Weise können Sie Logik-Apps erstellen, mit denen Aufgaben und Workflows für das Verwalten Ihrer Dateien automatisiert werden. Beispielsweise können Sie Logik-Apps erstellen, mit denen Dateien in Ihrem Speicherkonto erstellt, abgerufen, aktualisiert und gelöscht werden.

Angenommen, Sie verfügen über ein Tool, das auf einer Azure-Website aktualisiert wird, die als Trigger für Ihre Logik-App fungiert. Wenn dieses Ereignis eintritt, können Sie über Ihre Logik-App eine Datei in Ihrem Blobspeichercontainer aktualisieren. Hierbei handelt es sich um eine Aktion in Ihrer Logik-App.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Connectorspezifische technische Informationen finden Sie in der [Referenz zu Azure Blob Storage](/connectors/azureblobconnector/).

> [!IMPORTANT]
> Logik-Apps können nicht direkt auf Speicherkonten zugreifen, die sich hinter Firewalls befinden, wenn sich beide in derselben Region befinden. Um dieses Problem zu umgehen, können Sie Ihre Logik-Apps und Ihr Speicherkonto in verschiedenen Regionen einrichten. Weitere Informationen zum Aktivieren des Zugriffs von Azure Logic Apps auf Speicherkonten hinter Firewalls finden Sie weiter unten in diesem Thema im Abschnitt [Zugriff auf Speicherkonten hinter Firewalls](#storage-firewalls).

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Einschränkungen

* Standardmäßig können Azure Blob Storage-Aktionen Dateien mit einer Größe von *50 MB oder kleiner* lesen oder schreiben. Zum Verarbeiten von Dateien, die größer als 50 MB und maximal 1.024 MB groß sind, unterstützen Azure Blob Storage-Aktionen [Nachrichtensegmentierung](../logic-apps/logic-apps-handle-large-messages.md). Die Aktion **Get blob content** (Blobinhalt abrufen) verwendet implizit die Blockerstellung.

* Azure Blob Storage-Trigger unterstützen keine Segmentierung. Trigger wählen beim Anfordern von Dateiinhalten nur Dateien aus, die 50 MB oder kleiner sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 50 MB sind:

  * Verwenden Sie einen Azure Blob Storage-Trigger, der Dateieigenschaften zurückgibt, z.B. **When a blob is added or modified (properties only)** (Beim Hinzufügen oder Ändern eines Blobs (nur Eigenschaften)).

  * Lassen Sie auf den Trigger die Azure Blob Storage-Aktion **Get blob content** (Blobinhalt abrufen) folgen, die die vollständige Datei liest und implizit Segmentierung verwendet.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein [Azure-Speicherkonto und -Speichercontainer](../storage/blobs/storage-quickstart-blobs-portal.md)

* Die Logik-App, für die Sie Zugriff auf Ihr Azure-Blobspeicherkonto benötigen. Um Ihre Logik-App mit einem Azure Blob Storage-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Hinzufügen eines Blob Storage-Triggers

In Azure Logic Apps muss jede Logik-App mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine Logik-App-Instanz und startet die Ausführung des Workflows Ihrer App.

In diesem Beispiel wird veranschaulicht, wie Sie einen Logik-App-Workflow mit dem Trigger **When a blob is added or modified (properties only)** (Beim Hinzufügen oder Ändern eines Blobs (nur Eigenschaften)) starten können, wenn die Eigenschaften eines Blobs in Ihrem Speichercontainer hinzugefügt oder aktualisiert werden.

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) oder in Visual Studio eine leere Logik-App, die den Logik-App-Designer öffnet. In diesem Beispiel wird das Azure-Portal verwendet.

2. Geben Sie im Suchfeld „azure blob“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus.

   In diesem Beispiel wird folgender Trigger verwendet: **When a blob is added or modified (properties only)** (Beim Hinzufügen oder Ändern eines Blobs (nur Eigenschaften))

   ![Auswählen des Triggers „Azure Blob Storage“](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Blobspeicherverbindung erstellen](#create-connection). Falls die Verbindung bereits besteht, können Sie die erforderlichen Informationen für den Trigger angeben.

   Wählen Sie für dieses Beispiel den Container und Ordner aus, den Sie überwachen möchten.

   1. Wählen Sie im Feld **Container** das Ordnersymbol.

   2. Wählen Sie in der Ordnerliste den Pfeil nach rechts ( **>** ), und navigieren Sie zum gewünschten Ordner.

      ![Auswählen des mit dem Trigger zu verwendenden Speicherordners](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Wählen Sie das Intervall und die Häufigkeit aus, um anzugeben, wie oft der Trigger den Ordner auf Änderungen überprüfen soll.

4. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

5. Fahren Sie nun damit fort, der Logik-App weitere Aktionen für die Aufgaben hinzuzufügen, die anhand der Triggerergebnisse durchgeführt werden sollen.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Hinzufügen einer Blobspeicheraktion

In Azure Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, der einem Trigger oder einer anderen Aktion folgt. Bei diesem Beispiel beginnt die Logik-App mit dem [Trigger „Wiederholung“](../connectors/connectors-native-recurrence.md).

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) oder in Visual Studio Ihre Logik-App im Logik-App-Designer. In diesem Beispiel wird das Azure-Portal verwendet.

2. Wählen Sie im Logik-App-Designer unter dem Trigger oder der Aktion die Option **Neuer Schritt** aus.

   ![Hinzufügen eines neuen Schritts zum Logik-App-Workflow](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Um eine Aktion zwischen vorhandenen Schritten hinzuzufügen, bewegen Sie den Mauszeiger über den Verbindungspfeil. Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) aus, und klicken Sie auf **Aktion hinzufügen** .

3. Geben Sie im Suchfeld „azure blob“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   In diesem Beispiel wird diese Aktion verwendet: **Get blob content** (Blobinhalt abrufen)

   ![Auswählen der Aktion „Azure Blob Storage“.](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Azure Blob Storage-Verbindung erstellen](#create-connection).
Falls Ihre Verbindung bereits besteht, können Sie die erforderlichen Informationen für die Aktion angeben.

   Wählen Sie für dieses Beispiel die gewünschte Datei aus.

   1. Wählen Sie im Feld **Blob** das Ordnersymbol.
  
      ![Auswählen des mit dem Trigger zu verwendenden Speicherordners](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Suchen Sie anhand der **ID** -Nummer des Blobs nach der gewünschten Datei, und wählen Sie sie aus. Sie finden diese **ID** -Nummer in den Blobmetadaten, die vom oben beschriebenen Blobspeichertrigger zurückgegeben werden.

5. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.
Stellen Sie zum Testen Ihrer Logik-App sicher, dass der ausgewählte Ordner ein Blob enthält.

In diesem Beispiel wird nur der Inhalt für ein Blob abgerufen. Fügen Sie zum Anzeigen des Inhalts eine weitere Aktion hinzu, die mit dem Blob eine Datei erstellt, indem ein anderer Connector verwendet wird. Fügen Sie beispielsweise eine OneDrive-Aktion hinzu, die basierend auf dem Blobinhalt eine Datei erstellt.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Herstellen einer Verbindung mit dem Speicherkonto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Wenn Sie zum Erstellen der Verbindung aufgefordert werden, geben Sie diese Informationen an:

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Verbindungsname** | Ja | <*connection-name*> | Der Name, der für Ihre Verbindung erstellt werden soll |
   | **Speicherkonto** | Ja | <*storage-account*> | Wählen Sie Ihr Speicherkonto aus der Liste aus. |
   ||||

   Beispiel:

   ![Erstellen der Azure Blob Storage-Kontoverbindung](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

1. Nachdem Sie die Verbindung erstellt haben, fahren Sie mit [Hinzufügen eines Blobspeichertriggers](#add-trigger) oder [Hinzufügen einer Blobspeicheraktion](#add-action) fort.

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/azureblobconnector/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Zugreifen auf Speicherkonten hinter Firewalls

Sie können Netzwerksicherheit zu einem Azure-Speicherkonto hinzufügen, indem Sie den Zugriff mit einer [Firewall und Firewallregeln](../storage/common/storage-network-security.md) einschränken. Dieses Setup stellt jedoch eine Herausforderung für Azure und andere Microsoft-Dienste dar, die Zugriff auf das Speicherkonto benötigen. Die lokale Kommunikation im Rechenzentrum abstrahiert die internen IP-Adressen, sodass Sie keine Firewallregeln mit IP-Einschränkungen einrichten können. Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../storage/common/storage-network-security.md).

Im Folgenden finden Sie verschiedene Optionen für den Zugriff auf Speicherkonten hinter Firewalls aus Azure Logic Apps unter Verwendung des Azure Blob Storage-Connectors oder anderer Lösungen:

* Azure Storage-Blobconnector

  * [Zugreifen auf Speicherkonten in anderen Regionen](#access-other-regions)
  * [Zugreifen auf Speicherkonten über ein vertrauenswürdiges virtuelles Netzwerk](#access-trusted-virtual-network)

* Andere Lösungen

  * [Zugreifen auf Speicherkonten als vertrauenswürdiger Dienst mit verwalteten Identitäten](#access-trusted-service)
  * [Zugreifen auf Speicherkonten über Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Probleme beim Zugriff auf Speicherkonten in derselben Region

Logik-Apps können nicht direkt auf Speicherkonten hinter Firewalls zugreifen, wenn sich beide in derselben Region befinden. Um dieses Problem zu umgehen, platzieren Sie Ihre Logik-Apps in einer Region, die sich von der Ihres Speicherkontos unterscheidet, und gewähren Sie Zugriff auf die [ausgehenden IP-Adressen für die verwalteten Connectors in Ihrer Region](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Diese Lösung gilt nicht für den Azure Table Storage-Connector und den Azure Queue Storage-Connector. Um auf ihren Table Storage oder Queue Storage zuzugreifen, können Sie stattdessen integrierte HTTP-Trigger und -Aktionen verwenden.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Zugreifen auf Speicherkonten über ein vertrauenswürdiges virtuelles Netzwerk

Sie können das Speicherkonto in einem virtuellen Azure-Netzwerk platzieren, das Sie verwalten, und dieses virtuelle Netzwerk dann der Liste vertrauenswürdiger virtueller Netzwerke hinzufügen. Damit Ihre Logik-App über ein [vertrauenswürdiges virtuelles Netzwerk](../virtual-network/virtual-networks-overview.md) auf das Speicherkonto zugreifen kann, müssen Sie diese Logik-App in einer [Integration Service Environment (ISE, Integrationsdienstumgebung)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) bereitstellen, die eine Verbindung mit Ressourcen in einem virtuellen Netzwerk herstellen kann. Anschließend können Sie die Subnetze in dieser ISE der Liste vertrauenswürdiger Netzwerke hinzufügen. Azure Storage-Connectors wie der Blob Storage-Connector können direkt auf den Speichercontainer zugreifen. Dieses Setup ist mit der Verwendung der Dienstendpunkte aus einer ISE heraus identisch.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Zugreifen auf Speicherkonten als vertrauenswürdiger Dienst mit verwalteten Identitäten

Um vertrauenswürdigen Microsoft-Diensten den Zugriff auf ein Speicherkonto durch eine Firewall zu gestatten, können Sie für diese Dienste eine Ausnahme für dieses Speicherkonto einrichten. Mit dieser Lösung können Azure-Dienste, die [verwaltete Identitäten für die Authentifizierung](../active-directory/managed-identities-azure-resources/overview.md) unterstützen, auf Speicherkonten hinter Firewalls als vertrauenswürdige Dienste zugreifen. Insbesondere damit eine Logik-App in Azure mit globalen mehreren Mandanten auf diese Speicherkonten zugreifen kann, [aktivieren Sie zuerst die Unterstützung für verwaltete Identitäten ](../logic-apps/create-managed-service-identity.md) in der Logik-App. Danach verwenden Sie die HTTP-Aktion oder den HTTP-Trigger in ihrer Logik-App und [legen deren Authentifizierungstyp auf die Verwendung der verwalteten Identität Ihrer Logik-App](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) fest. In diesem Szenario können Sie *nur* die HTTP-Aktion oder den HTTP-Trigger verwenden.

Um die Ausnahme und die Unterstützung für verwaltete Identitäten einzurichten, führen Sie diese allgemeinen Schritte aus:

1. Wählen Sie in Ihrem Speicherkonto unter **Einstellungen** die Option **Firewalls und virtuelle Netzwerke** aus. Wählen Sie unter **Zugriff zulassen von** die Option **Ausgewählte Netzwerke** aus, damit die zugehörigen Einstellungen angezeigt werden.

1. Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** , und wählen Sie dann **Speichern** aus.

   ![Auswählen der Ausnahme, die vertrauenswürdige Microsoft-Dienste zulässt](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. In den Einstellungen ihrer Logik-App [aktivieren Sie die Unterstützung für die verwaltete Identität](../logic-apps/create-managed-service-identity.md).

1. Fügen Sie im Workflow ihrer Logik-App die HTTP-Aktion oder den HTTP-Trigger für den Zugriff auf das Speicherkonto oder die Entität hinzu, und richten Sie diese ein.

   > [!IMPORTANT]
   > Stellen Sie für ausgehende HTTP-Aktions- oder -Triggeraufrufe an Azure-Speicherkonten sicher, dass der Anforderungsheader die Eigenschaft `x-ms-version` und die API-Version für den Vorgang enthält, den Sie in dem Speicherkonto ausführen möchten. Weitere Informationen finden Sie unter [Authentifizieren des Zugriffs mithilfe einer verwalteten Identität](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) und [Versionsverwaltung für Azure Storage-Dienste](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Bei dieser Aktion [wählen Sie die verwaltete Identität aus](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity), die für die Authentifizierung verwendet werden soll.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Zugreifen auf Speicherkonten über Azure API Management

Wenn Sie für [API Management](../api-management/api-management-key-concepts.md) einen Dedicated-Tarif verwenden, können Sie die Speicher-API als Front-End verwenden, indem Sie API Management verwenden und dessen IP-Adressen das Passieren der Firewall gestatten. Im Grunde fügen Sie das virtuelle Azure-Netzwerk, das von API Management verwendet wird, der Firewalleinstellung des Speicherkontos hinzu. Sie können dann entweder die API Management-Aktion oder die HTTP-Aktion verwenden, um die Azure Storage-APIs aufzurufen. Wenn Sie diese Option auswählen, müssen Sie den Authentifizierungsprozess jedoch selbst handhaben. Weitere Informationen finden Sie unter [Einfache Unternehmensintegrationsarchitektur](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)