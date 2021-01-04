---
title: Erste Schritte mit dem Storage-Explorer | Microsoft-Dokumentation
description: Beginnen Sie mit der Verwaltung von Azure-Speicherressourcen mit Storage-Explorer. Hier erfahren Sie, wie Sie Azure Storage-Explorer herunterladen und installieren, eine Verbindung mit einem Speicherkonto oder einem Dienst herstellen und vieles mehr.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545906"
---
# <a name="get-started-with-storage-explorer"></a>Erste Schritte mit dem Storage-Explorer

## <a name="overview"></a>Übersicht

Microsoft Azure Storage-Explorer ist eine eigenständige App, mit der Sie unter Windows, macOS und Linux komfortabel mit Azure Storage-Daten arbeiten können. In diesem Artikel werden mehrere Möglichkeiten zum Herstellen einer Verbindung mit und zum Verwalten Ihrer Azure-Speicherkonten beschrieben.

![Microsoft Azure Storage-Explorer][0]

## <a name="prerequisites"></a>Voraussetzungen

# <a name="windows"></a>[Windows](#tab/windows)

Storage-Explorer wird von folgenden Windows-Versionen unterstützt:

* Windows 10 (empfohlen)
* Windows 8
* Windows 7

Für alle Windows-Versionen ist für Storage-Explorer .NET Framework 4.7.2 oder höher erforderlich.

# <a name="macos"></a>[macOS](#tab/macos)

Storage-Explorer wird von folgenden macOS-Versionen unterstützt:

* macOS 10.12 Sierra und höher

# <a name="linux"></a>[Linux](#tab/linux)

Storage-Explorer ist für die meisten gängigen Linux-Distributionen im [Snap Store](https://snapcraft.io/storage-explorer) verfügbar. Es wird empfohlen, Snap Store für diese Installation zu verwenden. Das Storage-Explorer-Snap installiert alle Abhängigkeiten und Updates, sobald neue Versionen im Snap Store veröffentlicht werden.

Informationen zu den unterstützten Distributionen finden Sie auf der Seite [Installing snapd](https://snapcraft.io/docs/installing-snapd).

Für Storage-Explorer ist die Verwendung eines Kennwort-Managers erforderlich. Möglicherweise müssen Sie manuell eine Verbindung mit einem Kennwort-Manager herstellen. Sie können Storage-Explorer mit dem Kennwort-Manager Ihres Systems verbinden, indem Sie den folgenden Befehl ausführen:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage-Explorer ist auch als *.tar.gz*-Download verfügbar. Sie müssen die Abhängigkeiten manuell installieren. Die *.tar.gz*-Installation wird für die folgenden Linux-Distributionen unterstützt:

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

Die *.tar.gz*-Installation könnte auch bei anderen Distributionen funktionieren, wird aber nur für diese aufgeführten Distributionen offiziell unterstützt.

Weitere Hilfe bei der Installation von Storage-Explorer unter Linux finden Sie unter [Linux-Abhängigkeiten](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) im Leitfaden zur Problembehandlung für Azure Storage-Explorer.

---

## <a name="download-and-install"></a>Herunterladen und Installieren

Informationen zum Herunterladen und Installieren von Storage-Explorer finden Sie unter [Azure Storage-Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Herstellen der Verbindung mit einem Speicherkonto oder Dienst

Es gibt verschiedene Möglichkeiten, wie Sie den Storage-Explorer mit Speicherkonten verbinden können. Generell haben Sie folgende Möglichkeiten:

* [Sich bei Azure anmelden, um auf Ihre Abonnements und deren Ressourcen zuzugreifen](#sign-in-to-azure)
* [Eine bestimmte Azure Storage- oder CosmosDB-Ressource anfügen](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

> [!NOTE]
> Um nach der Anmeldung Vollzugriff auf Ressourcen zu haben, benötigt Storage-Explorer Berechtigungen für sowohl die Verwaltung (Azure Resource Manager) als auch Datenebenen. Das bedeutet, dass Sie Azure Active Directory-Berechtigungen (Azure AD) benötigen, die Ihnen Zugriff auf Ihr Speicherkonto, die Container im Konto und Daten in den Containern gewähren. Wenn Sie nur über Berechtigungen auf Datenebene verfügen, erwägen Sie das [Hinzufügen einer Ressource über Azure AD](#add-a-resource-via-azure-ad). Weitere Informationen zu den spezifischen Berechtigungen, die Storage-Explorer benötigt, finden Sie unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. Wählen Sie in Storage-Explorer **Anzeigen** > **Kontoverwaltung** aus, oder klicken Sie auf die Schaltfläche **Konten verwalten**.

    ![Konten verwalten][1]

1. Unter **KONTOVERWALTUNG** werden jetzt alle Azure-Konten angezeigt, bei denen Sie angemeldet sind. Wählen Sie zum Herstellen der Verbindung mit einem anderen Konto **Konto hinzufügen** aus.

1. Wählen Sie unter **Verbindung mit Azure Storage herstellen** eine Azure-Cloud aus **Azure-Umgebung** aus, um sich bei einer nationalen Cloud oder einer Azure Stack-Instanz anzumelden. Nachdem Sie die Umgebung ausgewählt haben, klicken Sie auf **Weiter**.

    ![Anmeldeoption][2]

    Storage-Explorer öffnet eine Seite, auf der Sie sich anmelden können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder einem Speicherkonto](/azure-stack/user/azure-stack-storage-connect-se).

1. Nach erfolgreicher Anmeldung bei einem Azure-Konto werden unter **KONTOVERWALTUNG** das Konto und die mit diesem Konto verknüpften Azure Stack-Abonnements angezeigt. Wählen Sie **Alle Abonnements** aus, um in der Liste zwischen allen bzw. keinen Azure-Abonnements umzuschalten. Wählen Sie die Azure-Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **Übernehmen**.

    ![Azure-Abonnements auswählen][3]

    Im **EXPLORER** werden die Speicherkonten angezeigt, die mit den ausgewählten Azure-Abonnements verknüpft sind.

    ![Ausgewählte Azure-Abonnements][4]

### <a name="attach-a-specific-resource"></a>Anfügen einer bestimmten Ressourcengruppe

Es gibt mehrere Möglichkeiten des Anfügens an eine Ressource in Storage-Explorer:

* [Ressource über Azure AD hinzufügen](#add-a-resource-via-azure-ad). Wenn Sie nur auf Datenebene über Berechtigungen verfügen, wählen Sie diese Option, um einen Blobcontainer oder einen Azure Data Lake Storage Gen2 Blob-Speichercontainer hinzuzufügen.
* [Verbindungszeichenfolge verwenden](#use-a-connection-string). Wählen Sie diese Option, wenn Sie eine Verbindungszeichenfolge für ein Speicherkonto haben. Storage-Explorer unterstützt sowohl Schlüssel- als auch [Shared Access Signature](./storage/common/storage-sas-overview.md)-Verbindungszeichenfolgen.
* [Verwenden eines Shared Access Signature-URIs](#use-a-shared-access-signature-uri). Wenn Sie einen [Shared Access Signature-URI](./storage/common/storage-sas-overview.md) für einen Blobcontainer, eine Dateifreigabe, eine Warteschlange oder eine Tabelle haben, führen Sie damit das Anfügen an die Ressource durch. Sie können [Storage-Explorer](#generate-a-sas-in-storage-explorer) oder das [Azure-Portal](https://portal.azure.com) verwenden, um einen Shared Access Signature-URI zu erhalten.
* [Namen und Schlüssel verwenden](#use-a-name-and-key). Wenn Sie einen der Kontoschlüssel für Ihr Speicherkonto kennen, können Sie sich damit schneller verbinden. Suchen Sie Ihre Schlüssel auf der Seite „Speicherkonto“, indem Sie im [Azure-Portal](https://portal.azure.com)**Einstellungen** > **Zugriffsschlüssel** auswählen.
* [An lokalen Emulator anfügen](#attach-to-a-local-emulator). Wenn Sie einen der verfügbaren Azure Storage-Emulatoren verwenden, können Sie diese Option nutzen, um sich leichter mit Ihrem Emulator zu verbinden.
* [Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto mithilfe einer Verbindungszeichenfolge](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Wählen Sie diese Option, wenn Sie eine Verbindungszeichenfolge für eine CosmosDB-Instanz haben.
* [Herstellen einer Verbindung mit Azure Data Lake Store über den URI](#connect-to-azure-data-lake-store-by-uri). Wählen Sie diese Option, wenn Sie einen URI für Azure Data Lake Store haben.

#### <a name="add-a-resource-via-azure-ad"></a>Hinzufügen einer Ressource über Azure AD

1. Wählen Sie das Symbol **Verbinden** aus, um **Verbindung mit Azure Storage herstellen** zu öffnen.

    ![Option „Mit Azure Storage verbinden“][9]

1. Falls noch nicht erfolgt, verwenden Sie die Option **Azure-Konto hinzufügen**, um sich beim Azure-Konto anzumelden, das Zugriff auf die Ressource hat. Kehren Sie nach der Anmeldung zu **Verbindung mit Azure Storage herstellen** zurück.

1. Wählen Sie die Option **Ressource über Azure Active Directory (Azure AD) hinzufügen** und klicken Sie auf **Weiter**.

1. Wählen Sie ein Azure-Konto und einen Mandanten aus. Diese Werte müssen Zugriff auf die Speicherressource haben, an die Sie anfügen möchten. Wählen Sie **Weiter** aus.

1. Wählen Sie den Ressourcentyp aus, den Sie anfügen möchten. Geben Sie die für das Herstellen der Verbindung erforderlichen Informationen ein. 

   Die Informationen, die Sie auf dieser Seite eingeben, hängen vom hinzugefügten Ressourcentyp ab. Achten Sie darauf, dass Sie den richtigen Ressourcentyp wählen. Klicken Sie auf **Weiter**, nachdem Sie alle erforderlichen Informationen eingegeben haben.

1. Überprüfen Sie in der **Verbindungszusammenfassung** alle Informationen auf Richtigkeit. Wenn alles stimmt, wählen Sie **Verbinden** aus. Klicken Sie andernfalls auf **Zurück**, um zu den vorherigen Seiten zurückzukehren und falsche Informationen zu korrigieren.

Nachdem die Verbindung erfolgreich hinzugefügt wurde, wechselt die Ressourcenstruktur zum Knoten, der die Verbindung darstellt. Die Ressource wird unter **Lokal und angefügt** > **Speicherkonten** >  **(Angefügte Container)**  > **Blobcontainer** angezeigt. Wenn Storage-Explorer Ihre Verbindung nicht hinzufügen konnte oder Sie nach erfolgreichem Hinzufügen der Verbindung nicht auf Ihre Daten zugreifen können, lesen Sie die Informationen unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-connection-string"></a>Verwenden einer Verbindungszeichenfolge

1. Wählen Sie das Symbol **Verbinden** aus, um **Verbindung mit Azure Storage herstellen** zu öffnen.

    ![Option „Mit Azure Storage verbinden“][9]

1. Wählen Sie **Verbindungszeichenfolge verwenden** aus, und klicken Sie dann auf **Weiter**.

1. Wählen Sie einen Anzeigenamen für Ihre Verbindung, und geben Sie Ihre Verbindungszeichenfolge ein. Klicken Sie anschließend auf **Weiter**.

1. Überprüfen Sie in der **Verbindungszusammenfassung** alle Informationen auf Richtigkeit. Wenn alles stimmt, wählen Sie **Verbinden** aus. Klicken Sie andernfalls auf **Zurück**, um zu den vorherigen Seiten zurückzukehren und falsche Informationen zu korrigieren.

Nachdem die Verbindung erfolgreich hinzugefügt wurde, wechselt die Ressourcenstruktur zum Knoten, der die Verbindung darstellt. Die Ressource wird unter **Lokal & angefügt** > **Speicherkonten** angezeigt. Wenn Storage-Explorer Ihre Verbindung nicht hinzufügen konnte oder Sie nach erfolgreichem Hinzufügen der Verbindung nicht auf Ihre Daten zugreifen können, lesen Sie die Informationen unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-shared-access-signature-uri"></a>Verwenden eines Shared Access Signature-URIs

1. Wählen Sie das Symbol **Verbinden** aus, um **Verbindung mit Azure Storage herstellen** zu öffnen.

    ![Option „Mit Azure Storage verbinden“][9]

1. Wählen Sie **SAS-URI (Shared Access Signature) verwenden** aus, und klicken Sie auf **Weiter**.

1. Wählen Sie einen Anzeigenamen für Ihre Verbindung, und geben Sie Ihren Shared Access Signature-URI ein. Der Dienstendpunkt für den Ressourcentyp, den Sie anfügen, sollte automatisch eingetragen werden. Wenn Sie einen benutzerdefinierten Endpunkt verwenden, erfolgt dies möglicherweise nicht. Wählen Sie **Weiter** aus.

1. Überprüfen Sie in der **Verbindungszusammenfassung** alle Informationen auf Richtigkeit. Wenn alles stimmt, wählen Sie **Verbinden** aus. Klicken Sie andernfalls auf **Zurück**, um zu den vorherigen Seiten zurückzukehren und falsche Informationen zu korrigieren.

Nachdem die Verbindung erfolgreich hinzugefügt wurde, wechselt die Ressourcenstruktur zum Knoten, der die Verbindung darstellt. Die Ressource wird unter **Lokal und angefügt** > **Speicherkonten** >  **(Angefügte Container)**  > *der Dienstknoten für den Containertyp, den Sie angefügt haben* angezeigt. Wenn Storage-Explorer die Verbindung nicht hinzufügen konnten, finden Sie weitere Informationen unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md). Wenn Sie nach dem erfolgreichen Hinzufügen der Verbindung nicht auf Ihre Daten zugreifen können, finden Sie weitere Informationen im Leitfaden zur Problembehandlung.

#### <a name="use-a-name-and-key"></a>Verwenden eines Namens und Schlüssels

1. Wählen Sie das Symbol **Verbinden** aus, um **Verbindung mit Azure Storage herstellen** zu öffnen.

    ![Option „Mit Azure Storage verbinden“][9]

1. Aktivieren Sie **Einen Speicherkontonamen und -schlüssel verwenden**, und klicken Sie dann auf **Weiter**.

1. Wählen Sie einen Anzeigenamen für Ihre Verbindung.

1. Geben Sie Ihren Speicherkontonamen und einen seiner Zugriffsschlüssel ein.

1. Wählen Sie die **Speicherdomäne**, die Sie nutzen möchten, und klicken Sie dann auf **Weiter**.

1. Überprüfen Sie in der **Verbindungszusammenfassung** alle Informationen auf Richtigkeit. Wenn alles stimmt, wählen Sie **Verbinden** aus. Klicken Sie andernfalls auf **Zurück**, um zu den vorherigen Seiten zurückzukehren und falsche Informationen zu korrigieren.

Nachdem die Verbindung erfolgreich hinzugefügt wurde, wechselt die Ressourcenstruktur zum Knoten, der die Verbindung darstellt. Die Ressource wird unter **Lokal & angefügt** > **Speicherkonten** angezeigt. Wenn Storage-Explorer Ihre Verbindung nicht hinzufügen konnte oder Sie nach erfolgreichem Hinzufügen der Verbindung nicht auf Ihre Daten zugreifen können, lesen Sie die Informationen unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="attach-to-a-local-emulator"></a>Anfügen an einen lokalen Emulator

Storage-Explorer unterstützt derzeit zwei offizielle Speicheremulatoren:

* [Azure Storage-Emulator](storage/common/storage-use-emulator.md) (nur Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS oder Linux)

Wenn Ihr Emulator an den Standardports lauscht, können Sie den Knoten **Emulator – Standardports** verwenden, um auf Ihren Emulator zuzugreifen. Suchen Sie nach **Emulator – Standardports** unter **Lokal & angefügt** > **Speicherkonten**.

Wenn Sie einen anderen Namen für Ihre Verbindung verwenden möchten oder Ihr Emulator nicht auf den Standardports ausgeführt wird, führen Sie diese Schritte durch:

1. Starten Sie Ihren Emulator. Geben Sie den Befehl `AzureStorageEmulator.exe status` ein, um die Ports für die einzelnen Diensttypen anzuzeigen.

   > [!IMPORTANT]
   > Storage-Explorer startet Ihren Emulator nicht automatisch. Sie müssen ihn manuell starten.

1. Wählen Sie das Symbol **Verbinden** aus, um **Verbindung mit Azure Storage herstellen** zu öffnen.

    ![Option „Mit Azure Storage verbinden“][9]

1. Wählen Sie **An einen lokalen Emulator anfügen** aus, und klicken Sie dann auf **Weiter**.

1. Wählen Sie einen Anzeigenamen für Ihre Verbindung, und geben Sie die Ports ein, an denen Ihr Emulator auf die einzelnen Diensttypen lauscht. **An lokalen Emulator anfügen** schlägt die Standardportwerte für die meisten Emulatoren vor. Das Feld **Files-Port** ist leer, da keiner der offiziellen Emulatoren derzeit den Azure Files-Dienst unterstützt. Wenn der von Ihnen verwendete Emulator Azure Files unterstützt, können Sie den verwendeten Port eingeben. Klicken Sie anschließend auf **Weiter**.

1. Überprüfen Sie in der **Verbindungszusammenfassung** alle Informationen auf Richtigkeit. Wenn alles stimmt, wählen Sie **Verbinden** aus. Klicken Sie andernfalls auf **Zurück**, um zu den vorherigen Seiten zurückzukehren und falsche Informationen zu korrigieren.

Nachdem die Verbindung erfolgreich hinzugefügt wurde, wechselt die Ressourcenstruktur zum Knoten, der die Verbindung darstellt. Der Knoten sollte unter **Lokal & angefügt** > **Speicherkonten** angezeigt werden. Wenn Storage-Explorer Ihre Verbindung nicht hinzufügen konnte oder Sie nach erfolgreichem Hinzufügen der Verbindung nicht auf Ihre Daten zugreifen können, lesen Sie die Informationen unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto mithilfe einer Verbindungszeichenfolge

Anstatt Azure Cosmos DB-Konten über ein Azure-Abonnement zu verwalten, können Sie sich auch über eine Verbindungszeichenfolge mit Azure Cosmos DB verbinden. Führen Sie die folgenden Schritte aus, um eine Verbindung herzustellen:

1. Erweitern Sie im **EXPLORER** **Lokal und angefügt**. Klicken Sie mit der rechten Maustaste auf **Cosmos DB-Konten**, und wählen Sie **Connect to Azure Cosmos DB** (Mit Azure Cosmos DB verbinden) aus.

    ![Herstellen einer Verbindung mit Azure Cosmos DB mithilfe einer Verbindungszeichenfolge][21]

1. Wählen Sie die Azure Cosmos DB-API aus, fügen Sie Ihre **Verbindungszeichenfolge** ein, und klicken Sie dann auf **OK**, um eine Verbindung mit dem Azure Cosmos DB-Konto herzustellen. Informationen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Verwalten eines Azure Cosmos-Kontos](./cosmos-db/how-to-manage-database-account.md).

    ![Verbindungszeichenfolge][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Herstellen einer Verbindung mit Azure Data Lake Store über den URI

Sie können auf Ressourcen zugreifen, die nicht in Ihrem Abonnement enthalten sind. Sie benötigen eine Person mit Zugriff auf die relevante Ressource, die Ihnen den Ressourcen-URI bereitstellt. Stellen Sich nach der Anmeldung über den URI eine Verbindung mit Data Lake Store her. Führen Sie die folgenden Schritte aus, um eine Verbindung herzustellen:

1. Erweitern Sie im **EXPLORER** den Eintrag **Lokal & angefügt**.

1. Klicken Sie mit der rechten Maustaste auf **Data Lake Storage Gen1**, und wählen Sie **Connect to Data Lake Storage Gen1** (Mit Data Lake Storage Gen1 verbinden) aus.

    ![Kontextmenü „Mit Data Lake Store verbinden“](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Geben Sie den URI ein, und wählen Sie dann **OK** aus. Ihr Data Lake Store wird unter **Data Lake Storage** angezeigt.

    ![Mit Data Lake Store verbinden: Ergebnis](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

In diesem Beispiel wird Data Lake Storage Gen1 verwendet. Azure Data Lake Storage Gen2 ist jetzt verfügbar. Weitere Informationen finden Sie in der [Was ist Azure Data Lake Storage Gen1?](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generieren einer Shared Access Signature-Definition in Storage-Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Shared Access Signature auf Kontoebene

1. Klicken Sie mit der rechten Maustaste auf das Speicherkonto, das Sie freigeben möchten, und wählen Sie **Shared Access Signature abrufen** aus.

    ![Kontextmenüoption „Shared Access Signature abrufen“][14]

1. Geben Sie unter **Shared Access Signature** den Zeitrahmen und die gewünschten Berechtigungen für das Konto an, und wählen Sie **Erstellen** aus.

    ![Abrufen einer Shared Access Signature][15]

1. Kopieren Sie entweder die **Verbindungszeichenfolge** oder die unformatierte **Abfragezeichenfolge** in die Zwischenablage.

### <a name="service-level-shared-access-signature"></a>Shared Access Signature auf Dienstebene

Sie können eine Shared Access Signature auf Dienstebene abrufen. Weitere Informationen finden Sie unter [Abrufen der SAS für einen Blobcontainer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Suchen nach Speicherkonten

Sie können im Bereich **EXPLORER** nach Speicherressourcen suchen.

Sobald Sie Text in das Suchfeld eingeben, werden im Storage-Explorer alle Ressourcen angezeigt, die dem bislang eingegebenen Suchwert entsprechen. Dieses Beispiel zeigt eine Suche nach **Endpunkten**:

![Speicherkontosuche][23]

> [!NOTE]
> Deaktivieren Sie zum Beschleunigen Ihrer Suche im Bereich **Kontoverwaltung** alle Abonnements, die das gesuchte Element nicht enthalten. Sie können auch mit der rechten Maustaste auf einen Knoten klicken, und **Ab hier suchen** wählen, um die Suche ab einem bestimmten Knoten zu starten.
>
>

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Azure Blob Storage-Ressourcen mit Storage-Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Arbeiten mit Daten unter Verwendung von Azure Storage-Explorer](./cosmos-db/storage-explorer.md)
* [Verwalten von Azure Data Lake Store-Ressourcen mit Storage-Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png