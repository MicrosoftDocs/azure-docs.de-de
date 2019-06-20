---
title: 'Transformieren von XML mit XSLT-Zuordnungen: Azure-Logik-Apps | Microsoft-Dokumentation'
description: Hinzufügen von XSLT-Zuordnungen zum Transformieren von XML in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: d0d40ca0ae6ccd4f709d7d94d52764d4affcc215
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244696"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformieren von XML mit Zuordnungen in Azure Logic Apps mithilfe von Enterprise Integration Pack

Zur Übertragung von XML-Daten zwischen Formaten für Enterprise Integration-Szenarien in Azure Logic Apps kann Ihre Logik-App Zuordnungen verwenden. Genauer gesagt handelt es sich dabei um XSLT (Extensible Stylesheet Language Transformations)-Zuordnungen. Eine Zuordnung ist ein XML-Dokument, das die Konvertierung von Daten von einem XML-Dokument in ein anderes Format beschreibt. 

Stellen Sie sich beispielsweise vor, dass Sie regelmäßige B2B-Aufträge oder -Rechnungen von einem Kunden erhalten, der das Format JJJMMTT für Datumsangaben verwendet. Ihre Organisation verwendet jedoch das Datumsformat MMTTJJJ. Mithilfe einer Zuordnung können Sie das Datumsformat JJJMMTT in das Format MMTTJJJ transformieren, bevor Auftrags- oder Rechnungsdetails in Ihrer Kundenaktivitätsdatenbank gespeichert werden.

Grenzwerte für Integrationskonten und Artefakte wie Zuordnungen finden Sie unter [Grenzwert- und Konfigurationsinformationen für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), in dem Sie Ihre Zuordnungen und andere Artefakte für Enterprise Integration- und B2B (Business-to-Business)-Lösungen speichern.

* Wenn Ihre Zuordnung auf eine externe Assembly verweist, müssen Sie *sowohl die Assembly als auch die Zuordnung* in Ihr Integrationskonto hochladen. Sie müssen die [*Assembly zuerst hochladen*](#add-assembly), bevor Sie den Upload für die Zuordnung durchführen, in der auf die Assembly verwiesen wird.

  Wenn Ihre Assembly 2 MB oder kleiner ist, können Sie Ihre Assembly dem Integrationskonto *direkt* über das Azure-Portal hinzufügen. Ist Ihre Assembly oder Zuordnung jedoch größer als 2 MB, ohne jedoch das [Größenlimit für Assemblys oder Zuordnungen](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) zu überschreiten, haben Sie die folgenden Optionen:

  * Für Assemblys benötigen Sie einen Azure-Blobcontainer, in den Sie Ihre Assembly hochladen können, und den Containerspeicherort. Auf diese Weise können Sie diesen Speicherort später bereitstellen, wenn Sie die Assembly Ihrem Integrationskonto hinzufügen. 
  Für diese Aufgabe benötigen Sie folgende Elemente:

    | Item | BESCHREIBUNG |
    |------|-------------|
    | [Azure-Speicherkonto](../storage/common/storage-account-overview.md) | Unter diesem Konto erstellen Sie einen Azure-Blobcontainer für Ihre Assembly. Hier erfahren Sie, wie Sie ein [Speicherkonto erstellen](../storage/common/storage-quickstart-create-account.md). |
    | BLOB-Container | In diesen Container können Sie Ihre Assembly hochladen. Außerdem benötigen Sie den Containerspeicherort, wenn Sie die Assembly Ihrem Integrationskonto hinzufügen. Hier erfahren Sie, wie Sie einen [Blobcontainer erstellen](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Mit diesem Tool lassen sich Speicherkonten und Blobcontainer einfacher verwalten. Eine Möglichkeit zur Verwendung des Storage-Explorers besteht darin, [den Azure Storage-Explorer herunterzuladen und zu installieren](https://www.storageexplorer.com/). Verbinden Sie dann den Storage-Explorer mit Ihrem Speicherkonto, indem Sie die Schritte unter [Erste Schritte mit dem Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) ausführen. Weitere Informationen finden Sie unter [Schnellstart: Verwenden von Azure Storage-Explorer zum Erstellen eines Blobs im Objektspeicher](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Alternativ können Sie Ihr Speicherkonto im Azure-Portal suchen und auswählen. Wählen Sie im Menü „Speicherkonto“ **Storage-Explorer** aus. |
    |||

  * Im Fall von Zuordnungen lassen sich derzeit größere Zuordnungen mithilfe von [Azure Logic Apps REST API – Zuordnungen](https://docs.microsoft.com/rest/api/logic/maps/createorupdate) hinzufügen.

Zum Erstellen und Hinzufügen von Zuordnungen benötigen Sie keine Logik-App. Damit Sie eine Zuordnung verwenden können, muss Ihre Logik-App jedoch mit einem Integrationskonto verknüpft sein, in dem diese Zuordnung gespeichert ist. Erfahren Sie, wie Sie [Logik-Apps mit Integrationskonten verknüpfen](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Wenn Sie noch nicht über eine Logik-App verfügen, lesen Sie den Artikel zum [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Hinzufügen referenzierter Assemblys

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Azure-Hauptmenü **Alle Dienste** aus, um Ihr Integrationskonto zu suchen und zu öffnen. 
   Geben Sie im Suchfeld „Integrationskonto“ ein. 
   Wählen Sie **Integrationskonten** aus.

   ![Suchen von Integrationskonten](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wählen Sie das Integrationskonto aus, dem Sie Ihre Assembly hinzufügen möchten. Beispiel:

   ![Auswählen des Integrationskontos](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Wählen Sie auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** die Kachel **Assemblys** aus.

   ![Auswählen von „Assemblys“](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Nachdem die Seite **Assemblys** geöffnet wurde, wählen Sie **Hinzufügen** aus.

   ![„Hinzufügen“ auswählen](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Je nach Größe Ihrer Assemblydatei führen Sie die Schritte zum Upload einer Assembly aus, die entweder [maximal 2 MB](#smaller-assembly) groß oder [größer als 2 MB (maximal 8 MB)](#larger-assembly) ist.
Die Grenzwerte für die Anzahl von Assemblys in Integrationskonten finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Wenn Sie Ihre Assembly ändern, müssen Sie auch Ihre Zuordnung aktualisieren, und zwar unabhängig davon, ob die Zuordnung Änderungen enthält.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Hinzufügen von Assemblys bis zu 2 MB

1. Geben Sie unter **Assembly hinzufügen** einen Namen für Ihre Assembly ein. Lassen Sie **Kleine Datei** ausgewählt. Wählen Sie neben dem Feld **Assembly** das Ordnersymbol aus. Suchen und wählen Sie die Assembly zum Upload aus. Beispiel:

   ![Hochladen kleinerer Assemblys](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Nachdem Sie die Assembly ausgewählt haben, wird der Dateiname der Assembly automatisch in der **Assemblyname**-Eigenschaft angezeigt.

1. Wählen Sie abschließend **OK** aus.

   Nachdem der Upload Ihrer Assemblydatei abgeschlossen ist, wird die Assembly in der Liste **Assemblys** angezeigt.

   ![Liste der hochgeladenen Assemblys](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Komponenten** auf der Kachel **Assemblys** jetzt die Anzahl hochgeladener Assemblys angezeigt. Beispiel:

   ![Hochgeladene Assemblys](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Hinzufügen von Assemblys mit mehr als 2 MB

Wenn Sie größere Assemblys hinzufügen möchten, können Sie Ihre Assembly in einen Azure-Blobcontainer in Ihrem Azure-Speicherkonto hochladen. Die Schritte zum Hinzufügen von Assemblys unterscheiden sich abhängig davon, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt. Überprüfen Sie deshalb zuerst anhand der folgenden Schritte, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt: [Festlegen der öffentlichen Zugriffsebene für Blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Überprüfen der Containerzugriffsebene

1. Öffnen Sie den Azure Storage-Explorer. Klappen Sie im Explorer-Fenster Ihr Azure-Abonnement auf, sofern es noch nicht aufgeklappt ist.

1. Klappen Sie **Speicherkonten** > {*Ihr-Speicherkonto*} > **Blobcontainer** auf. Wählen Sie Ihren Blobcontainer aus.

1. Wählen Sie im Kontextmenü des Blobcontainers **Öffentliche Zugriffsebene festlegen** aus.

   * Wenn Ihr Blobcontainer mindestens über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container mit öffentlichem Zugriff](#public-access-assemblies)

     ![Öffentlicher Zugriff](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Wenn Ihr Blobcontainer nicht über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container ohne öffentlichen Zugriff](#no-public-access-assemblies)

     ![Kein öffentlicher Zugriff](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Hochladen in Container mit öffentlichem Zugriff

1. Laden Sie die Assembly in Ihr Speicherkonto hoch. 
   Wählen Sie im rechten Fenster **Hochladen** aus.

1. Wählen Sie die hochgeladene Assembly aus, nachdem der Upload abgeschlossen ist. Wählen Sie auf der Symbolleiste **URL kopieren** aus, um die URL der Assembly zu kopieren.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Assembly hinzufügen** geöffnet ist. 
   Geben Sie einen Namen für Ihre Assembly ein. 
   Wählen Sie **Große Datei (größer als 2 MB)** aus.

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Assembly** angezeigt.

1. Fügen Sie die URL Ihrer Assembly in das Feld **Inhalts-URI** ein. 
   Schließen Sie das Hinzufügen Ihrer Assembly ab.

Nachdem der Upload Ihrer Assembly abgeschlossen ist, wird die Assembly in der Liste **Assemblys** angezeigt.
Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Komponenten** auf der Kachel **Assemblys** jetzt die Anzahl hochgeladener Assemblys angezeigt.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Hochladen in Container ohne öffentlichen Zugriff

1. Laden Sie die Assembly in Ihr Speicherkonto hoch. 
   Wählen Sie im rechten Fenster **Hochladen** aus.

1. Nachdem der Upload abgeschlossen ist, generieren Sie eine Shared Access Signature (SAS) für Ihre Assembly. 
   Wählen Sie im Kontextmenü der Assembly **Shared Access Signature abrufen** aus.

1. Wählen Sie im Bereich **Shared Access Signature** die Option **URI für Shared Access Signature auf Containerebene generieren** > **Erstellen** aus. 
   Nachdem die SAS-URL generiert wurde, wählen Sie neben dem Feld **URL** die Option **Kopieren** aus.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Assembly hinzufügen** geöffnet ist. 
   Geben Sie einen Namen für Ihre Assembly ein. 
   Wählen Sie **Große Datei (größer als 2 MB)** aus.

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Assembly** angezeigt.

1. Fügen Sie im Feld **Inhalts-URI** den zuvor generierten SAS-URI ein. Schließen Sie das Hinzufügen Ihrer Assembly ab.

Nachdem der Upload Ihrer Assembly abgeschlossen ist, wird die Assembly in der Liste **Assemblys** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Komponenten** auf der Kachel **Assemblys** jetzt die Anzahl hochgeladener Assemblys angezeigt.

## <a name="create-maps"></a>Erstellen von Zuordnungen

Zum Erstellen eines XSLT-Dokuments, das als Zuordnung verwendet werden kann, können Sie in Visual Studio 2015 unter Verwendung von [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) ein BizTalk-Integrationsprojekt anlegen. In diesem Projekt können Sie eine Integrationszuordnungsdatei erstellen, mit deren Hilfe Sie Elemente zwischen zwei XML-Schemadateien visuell zuordnen können. Nachdem Sie dieses Projekt erstellt haben, verfügen Sie über ein XSLT-Dokument.
Die Grenzwerte für die Anzahl von Zuordnungen in Integrationskonten finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Hinzufügen von Zuordnungen

Nachdem Sie alle Assemblys hochgeladen haben, auf die in Ihrer Zuordnung verwiesen wird, können Sie nun Ihre Zuordnung hochladen.

1. Falls Sie noch nicht angemeldet sind, melden Sie sich jetzt mit Ihrem Azure-Kontoanmeldeinformationen beim [Azure-Portal](https://portal.azure.com) an. 

1. Wenn Ihr Integrationskonto noch nicht geöffnet ist, wählen Sie im Azure-Hauptmenü **Alle Dienste** aus. 
   Geben Sie im Suchfeld „Integrationskonto“ ein. 
   Wählen Sie **Integrationskonten** aus.

   ![Suchen von Integrationskonten](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wählen Sie das Integrationskonto aus, dem Sie Ihre Zuordnung hinzufügen möchten. Beispiel:

   ![Auswählen des Integrationskontos](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Wählen Sie auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** die Kachel **Zuordnungen** aus.

   ![Auswählen von „Zuordnungen“](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Wählen Sie **Hinzufügen** aus, nachdem die Seite **Zuordnungen** geöffnet wurde.

   ![„Hinzufügen“ auswählen](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Hinzufügen von Zuordnungen bis zu 2 MB

1. Geben Sie unter **Zuordnung hinzufügen** einen Namen für Ihre Zuordnung ein. 

1. Wählen Sie unter **Zuordnungstyp** den Typ aus. Beispiel: **Liquid**, **XSLT**, **XSLT 2.0** oder **XSLT 3.0**.

1. Lassen Sie **Kleine Datei** ausgewählt. Wählen Sie neben dem Feld **Zuordnung** das Ordnersymbol aus. Suchen und wählen Sie die Zuordnung zum Upload aus. Beispiel:

   ![Hochladen der Zuordnung](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Wenn die **Name**-Eigenschaft nicht angegeben wurde, wird der Dateiname der Zuordnung automatisch als Eigenschaftswert angezeigt, nachdem Sie die Zuordnungsdatei ausgewählt haben. 
   Sie können jedoch einen beliebigen eindeutigen Namen verwenden.

1. Wählen Sie abschließend **OK** aus. 
   Nachdem der Upload Ihrer Zuordnungsdatei abgeschlossen ist, wird die Zuordnung in der Liste **Zuordnungen** angezeigt.

   ![Liste der hochgeladenen Zuordnungen](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Komponenten** auf der Kachel **Zuordnungen** jetzt die Anzahl hochgeladener Zuordnungen angezeigt. Beispiel:

   ![Hochgeladene Zuordnungen](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Hinzufügen von Zuordnungen mit mehr als 2 MB

Größere Zuordnungen lassen sich derzeit mit [Azure Logic Apps REST API – Zuordnungen](https://docs.microsoft.com/rest/api/logic/maps/createorupdate) hinzufügen.

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Bearbeiten von Zuordnungen

Wenn Sie eine vorhandene Zuordnung aktualisieren möchten, müssen Sie eine neue Zuordnungsdatei hochladen, die über die gewünschten Änderungen verfügt. Allerdings können Sie zuerst die vorhandene Zuordnung zum Bearbeiten herunterladen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto, und öffnen Sie es, falls es noch nicht geöffnet ist.

1. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. Geben Sie im Suchfeld „Integrationskonto“ ein. Wählen Sie **Integrationskonten** aus.

1. Wählen Sie das Integrationskonto aus, unter dem Sie Ihre Zuordnung aktualisieren möchten.

1. Wählen Sie auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** die Kachel **Zuordnungen** aus.

1. Nachdem die Seite **Zuordnungen** geöffnet wurde, wählen Sie Ihre Zuordnung aus. 
   Um die Zuordnung zuerst herunterzuladen und zu bearbeiten, wählen Sie **Herunterladen** aus, und speichern Sie die Zuordnung.

1. Wenn Sie bereit für den Upload der aktualisierten Zuordnung sind, wählen Sie auf der Seite **Zuordnungen** die zu aktualisierende Zuordnung und dann **Aktualisieren** aus.

1. Suchen und wählen Sie die aktualisierte Zuordnung aus, die Sie hochladen möchten. 
   Nachdem der Upload Ihrer Zuordnungsdatei abgeschlossen ist, wird die aktualisierte Zuordnung in der Liste **Zuordnungen** angezeigt.

## <a name="delete-maps"></a>Löschen von Zuordnungen

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto, und öffnen Sie es, falls es noch nicht geöffnet ist.

1. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. 
   Geben Sie im Suchfeld „Integrationskonto“ ein. 
   Wählen Sie **Integrationskonten** aus.

1. Wählen Sie das Integrationskonto aus, unter dem Sie Ihre Zuordnung löschen möchten.

1. Wählen Sie auf der Seite **Übersicht** Ihres Integrationskontos unter **Komponenten** die Kachel **Zuordnungen** aus.

1. Nachdem die Seite **Zuordnungen** geöffnet wurde, wählen Sie Ihre Zuordnung und dann **Löschen** aus.

1. Um zu bestätigen, dass Sie die Zuordnung löschen möchten, wählen **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Weitere Informationen zu Schemas](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Weitere Informationen über Transformationen](../logic-apps/logic-apps-enterprise-integration-transform.md)
