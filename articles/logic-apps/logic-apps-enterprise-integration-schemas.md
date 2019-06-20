---
title: 'Validieren von XML-Schemas: Azure Logic Apps | Microsoft-Dokumentation'
description: Hinzufügen von Schemas zum Überprüfen von XML-Dokumenten in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 3cca995b353b88cc481cbda68df4211a724f7f09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846369"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Überprüfen von XML mit Schemas in Azure Logic Apps mit Enterprise Integration Pack

Um sicherzustellen, dass Dokumente gültige XML verwenden und über Daten im vordefinierten Format verfügen, das für Enterprise Integration-Szenarien in Azure Logic Apps erwartet wird, kann Ihre Logik-App Schemas verwenden. Ein Schema kann darüber hinaus Nachrichten überprüfen, die Logik-Apps in Business-to-Business (B2B)-Szenarien austauschen.

Grenzwerte für Integrationskonten und Artefakte wie Schemas finden Sie unter [Grenzwert- und Konfigurationsinformationen für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), in dem Sie Ihre Schemas und andere Artefakte für Enterprise Integration- und B2B (Business-to-Business)-Lösungen speichern. 

  Wenn Ihr Schema [2 MB oder kleiner](#smaller-schema) ist, können Sie Ihr Schema dem Integrationskonto direkt über das Azure-Portal hinzufügen. Wenn Ihr Schema jedoch größer als 2 MB ist, den [Grenzwert für die Schemagröße](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) jedoch nicht überschreitet, können Sie Ihr Schema in ein Azure-Speicherkonto hochladen. 
  Um dieses Schema Ihrem Integrationskonto hinzuzufügen, können Sie Ihr Speicherkonto mit Ihrem Integrationskonto verknüpfen. 
  Für diese Aufgabe benötigen Sie Folgendes: 

  * Ein [Azure-Speicherkonto](../storage/common/storage-account-overview.md), in dem Sie einen Blobcontainer für Ihr Schema erstellen. Hier erfahren Sie, wie Sie ein [Speicherkonto erstellen](../storage/common/storage-quickstart-create-account.md). 

  * Einen Blobcontainer zum Speichern Ihres Schemas. Hier erfahren Sie, wie Sie einen [Blobcontainer erstellen](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Wenn Sie das Schema später Ihrem Integrationskonto hinzufügen, benötigen Sie den Inhalts-URI Ihres Containers.

  * [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zur Verwaltung von Speicherkonten und Blobcontainern. 
  Zur Verwendung des Storage-Explorers wählen Sie eine der folgenden Optionen:
  
    * Suchen Sie im Azure-Portal Ihr Speicherkonto, und wählen Sie es aus. 
    Wählen Sie im Menü „Speicherkonto“ **Storage-Explorer** aus.

    * Bei der Desktopversion [laden Sie den Azure Storage-Explorer herunter und installieren ihn](https://www.storageexplorer.com/). 
    Verbinden Sie dann den Storage-Explorer mit Ihrem Speicherkonto, indem Sie die Schritte unter [Erste Schritte mit dem Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) ausführen. 
    Weitere Informationen finden Sie unter [Schnellstart: Verwenden von Azure Storage-Explorer zum Erstellen eines Blobs im Objektspeicher](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Zum Erstellen und Hinzufügen von Schemas benötigen Sie keine Logik-App. Damit Sie ein Schema verwenden können, muss Ihre Logik-App jedoch mit einem Integrationskonto verknüpft sein, in dem dieses Schema gespeichert ist. Erfahren Sie, wie Sie [Logik-Apps mit Integrationskonten verknüpfen](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Wenn Sie noch nicht über eine Logik-App verfügen, lesen Sie den Artikel zum [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Hinzufügen von Schemas

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> an.

1. Wählen Sie im Azure-Hauptmenü **Alle Dienste** aus, um Ihr Integrationskonto zu suchen und zu öffnen. Geben Sie im Suchfeld „Integrationskonto“ ein. Wählen Sie **Integrationskonten** aus.

   ![Suchen von Integrationskonten](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Wählen Sie das Integrationskonto aus, dem Sie Ihr Schema hinzufügen möchten. Beispiel:

   ![Auswählen des Integrationskontos](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Wählen Sie auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** die Kachel **Schemas** aus.

   ![Auswählen von „Schemas“](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Nachdem die Seite **Schemas** geöffnet wurde, wählen Sie **Hinzufügen** aus.

   ![„Hinzufügen“ auswählen](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Je nach Größe Ihrer Schemadatei (.xsd) führen Sie die Schritte zum Upload eines Schemas aus, das entweder [maximal 2 MB](#smaller-schema) groß oder [größer als 2 MB (maximal 8 MB)](#larger-schema) ist.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Hinzufügen von Schemas bis maximal 2 MB

1. Geben Sie unter **Schema hinzufügen** den Namen Ihres Schemas ein. 
   Lassen Sie **Kleine Datei** ausgewählt. Wählen Sie neben dem Feld **Schema** das Ordnersymbol aus. Suchen und wählen Sie das Schema zum Upload aus. Beispiel:

   ![Hochladen eines kleineren Schemas](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Wählen Sie abschließend **OK** aus.

   Nachdem der Upload Ihres Schemas abgeschlossen ist, wird das Schema in der Liste **Schemas** angezeigt.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Hinzufügen von Schemas mit mehr als 2 MB

Wenn Sie größere Schemas hinzufügen möchten, können Sie Ihr Schema in einen Azure-Blobcontainer in Ihrem Azure-Speicherkonto hochladen. Die Schritte zum Hinzufügen von Schemas unterscheiden sich abhängig davon, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt. Überprüfen Sie deshalb zuerst anhand der folgenden Schritte, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt: [Festlegen der öffentlichen Zugriffsebene für Blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Überprüfen der Containerzugriffsebene

1. Öffnen Sie den Azure Storage-Explorer. Klappen Sie im Explorer-Fenster Ihr Azure-Abonnement auf, sofern es noch nicht aufgeklappt ist.

1. Klappen Sie **Speicherkonten** > {*Ihr-Speicherkonto*} > **Blobcontainer** auf. Wählen Sie Ihren Blobcontainer aus.

1. Wählen Sie im Kontextmenü des Blobcontainers **Öffentliche Zugriffsebene festlegen** aus.

   * Wenn Ihr Blobcontainer mindestens über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container mit öffentlichem Zugriff](#public-access)

     ![Öffentlicher Zugriff](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Wenn Ihr Blobcontainer nicht über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container ohne öffentlichen Zugriff](#public-access)

     ![Kein öffentlicher Zugriff](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Hochladen in Container mit öffentlichem Zugriff

1. Laden Sie das Schema in Ihr Speicherkonto hoch. 
   Wählen Sie im rechten Fenster **Hochladen** aus.

1. Wählen Sie das hochgeladene Schema aus, nachdem der Upload abgeschlossen ist. Wählen Sie auf der Symbolleiste **URL kopieren** aus, um die URL des Schemas zu kopieren.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Schema hinzufügen** geöffnet ist. 
   Geben Sie einen Namen für Ihre Assembly ein. 
   Wählen Sie **Große Datei (größer als 2 MB)** aus. 

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Schema** angezeigt.

1. Fügen Sie die URL Ihres Schemas in das Feld **Inhalts-URI** ein. 
   Schließen Sie das Hinzufügen Ihres Schemas ab.

Nachdem der Upload Ihres Schemas abgeschlossen ist, wird das Schema in der Liste **Schemas** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** wird jetzt auf der Kachel **Schemas** die Anzahl hochgeladener Schemas angezeigt.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Hochladen in Container ohne öffentlichen Zugriff

1. Laden Sie das Schema in Ihr Speicherkonto hoch. 
   Wählen Sie im rechten Fenster **Hochladen** aus.

1. Nachdem der Upload abgeschlossen ist, generieren Sie eine Shared Access Signature (SAS) für Ihr Schema. 
   Wählen Sie im Kontextmenü des Schemas **Shared Access Signature abrufen** aus.

1. Wählen Sie im Bereich **Shared Access Signature** die Option **URI für Shared Access Signature auf Containerebene generieren** > **Erstellen** aus. 
   Nachdem die SAS-URL generiert wurde, wählen Sie neben dem Feld **URL** die Option **Kopieren** aus.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Schema hinzufügen** geöffnet ist. Wählen Sie **Große Datei** aus.

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Schema** angezeigt.

1. Fügen Sie im Feld **Inhalts-URI** den zuvor generierten SAS-URI ein. Schließen Sie das Hinzufügen Ihres Schemas ab.

Nachdem der Upload Ihres Schemas abgeschlossen ist, wird das Schema in der Liste **Schemas** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** wird jetzt auf der Kachel **Schemas** die Anzahl hochgeladener Schemas angezeigt.

## <a name="edit-schemas"></a>Bearbeiten von Schemas

Wenn Sie ein vorhandenes Schema aktualisieren möchten, müssen Sie eine neue Schemadatei hochladen, die über die gewünschten Änderungen verfügt. Allerdings können Sie zuerst das vorhandene Schema zum Bearbeiten herunterladen.

1. Suchen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> Ihr Integrationskonto, und öffnen Sie es, falls es noch nicht geöffnet ist.

1. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. 
   Geben Sie im Suchfeld „Integrationskonto“ ein. 
   Wählen Sie **Integrationskonten** aus.

1. Wählen Sie das Integrationskonto aus, unter dem Sie Ihr Schema aktualisieren möchten.

1. Wählen Sie auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** die Kachel **Schemas** aus.

1. Nachdem die Seite **Schemas** geöffnet wurde, wählen Sie Ihr Schema aus. 
   Um das Schema zuerst herunterzuladen und zu bearbeiten, wählen Sie **Herunterladen** aus, und speichern Sie das Schema.

1. Wenn Sie bereit für den Upload des aktualisierten Schemas sind, wählen Sie auf der Seite **Schemas** das zu aktualisierende Schema und dann **Aktualisieren** aus.

1. Suchen und wählen Sie das aktualisierte Schema aus, das Sie hochladen möchten. 
   Nachdem der Upload Ihrer Schemadatei abgeschlossen ist, wird das aktualisierte Schema in der Liste **Schemas** angezeigt.

## <a name="delete-schemas"></a>Löschen von Schemas

1. Suchen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> Ihr Integrationskonto, und öffnen Sie es, falls es noch nicht geöffnet ist.

1. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. 
   Geben Sie im Suchfeld „Integrationskonto“ ein. 
   Wählen Sie **Integrationskonten** aus.

1. Wählen Sie das Integrationskonto aus, unter dem Sie Ihr Schema löschen möchten.

1. Wählen Sie auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** die Kachel **Schemas** aus.

1. Nachdem die Seite **Schemas** geöffnet wurde, wählen Sie Ihr Schema und dann **Löschen** aus.

1. Um zu bestätigen, dass Sie das Schema löschen möchten, wählen Sie **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zum Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Weitere Informationen über Zuordnungen](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Weitere Informationen über Transformationen](../logic-apps/logic-apps-enterprise-integration-transform.md)
