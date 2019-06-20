---
title: Erste Schritte mit dem Storage-Explorer | Microsoft-Dokumentation
description: Verwalten von Azure Storage-Ressourcen mit dem Storage-Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 4ee91a91a20cbffebf7453ba573266962aa35806
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924734"
---
# <a name="get-started-with-storage-explorer"></a>Erste Schritte mit dem Storage-Explorer

## <a name="overview"></a>Übersicht

Beim Azure Storage-Explorer handelt es sich um eine eigenständige App, über die Sie unter Windows, macOS und Linux komfortabel mit Azure Storage-Daten arbeiten können. In diesem Artikel werden mehrere Möglichkeiten zum Verbinden und Verwalten Ihrer Azure-Speicherkonten beschrieben.

![Microsoft Azure Storage-Explorer][0]

## <a name="prerequisites"></a>Voraussetzungen

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Der Azure Storage-Explorer wird in folgenden Windows-Versionen unterstützt:

* Windows 10 (empfohlen)
* Windows 8
* Windows 7

Für alle Versionen von Windows ist .NET Framework 4.6.2 oder höher erforderlich.

[Herunterladen und Installieren des Storage-Explorers](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Der Azure Storage-Explorer wird in folgenden macOS-Versionen unterstützt:

* macOS 10.12 „Sierra“ und höher

[Herunterladen und Installieren des Storage-Explorers](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Der Azure Storage-Explorer wird in folgenden Linux-Verteilungen unterstützt:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Der Azure Storage-Explorer funktioniert möglicherweise mit anderen Verteilungen, es werden jedoch nur die oben aufgeführten Versionen offiziell unterstützt.

Weitere Informationen zum Installieren von Storage-Explorer unter Linux finden Sie im [Leitfaden zur Problembehandlung](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Die [Versionshinweise](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) des Azure Storage-Explorers enthalten für einige Verteilungen spezielle Schritte.

[Herunterladen und Installieren des Storage-Explorers](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Herstellen der Verbindung mit einem Speicherkonto oder Dienst

Es gibt verschiedene Möglichkeiten, wie Sie den Storage-Explorer mit Speicherkonten verbinden können. Sie haben beispielsweise folgende Möglichkeiten:

* Verbinden mit Speicherkonten, die Ihren Azure-Abonnements zugeordnet sind
* Verbinden mit freigegebenen Speicherkonten und Diensten aus anderen Azure-Abonnements
* Verbinden mit und Verwalten von lokalem Speicher mithilfe des Azure-Speicheremulators

Darüber hinaus können Sie mit Speicherkonten in Azure (global und national) arbeiten:

* [Herstellen einer Verbindung mit einem Azure-Abonnement](#connect-to-an-azure-subscription): Verwalten Sie Speicherressourcen, die zu Ihrem Azure-Abonnement gehören.
* [Verwenden von lokalem Entwicklungsspeicher](#work-with-local-development-storage): Verwalten Sie lokalen Speicher mit dem Azure-Speicheremulator.
* [Anfügen an externen Speicher](#attach-or-detach-an-external-storage-account): Verwalten Sie Speicherressourcen eines anderen Azure-Abonnements oder in nationalen Azure-Clouds unter Verwendung des Namens und Schlüssels sowie der Endpunkte des Speicherkontos.
* [Anfügen eines Speicherkontos unter Verwendung einer SAS](#attach-a-storage-account-by-using-a-shared-access-signature-sas): Verwalten Sie Speicherressourcen eines anderen Azure-Abonnements unter Verwendung einer Shared Access Signature (SAS).
* [Anfügen eines Diensts unter Verwendung einer SAS](#attach-a-service-by-using-a-shared-access-signature-sas): Verwalten Sie einen bestimmten Speicherdienst (Blobcontainer, Warteschlange oder Tabelle) eines anderen Azure-Abonnements unter Verwendung einer SAS.
* [Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto mithilfe einer Verbindungszeichenfolge](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Verwalten Sie ein Cosmos DB-Konto mithilfe einer Verbindungszeichenfolge.

## <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

> [!NOTE]
> Wenn Sie kein Azure-Konto haben, können Sie sich [für eine kostenlose Testversion registrieren](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oder Ihre [Visual Studio-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. Wählen Sie im Storage-Explorer **Konten verwalten** aus, um zum **Bereich für die Kontoverwaltung** zu wechseln.

    ![Konten verwalten][1]

2. Im linken Bereich werden jetzt alle Azure-Konten angezeigt, bei denen Sie angemeldet sind. Wählen Sie zum Herstellen der Verbindung mit einem anderen Konto die Option **Konto hinzufügen**.

3. Wenn Sie sich bei einer nationalen Cloud oder Azure Stack anmelden möchten, klicken Sie auf die Dropdownliste **Azure-Umgebung**, um die gewünschte Azure-Cloud auszuwählen. Nachdem Sie Ihre Umgebung gewählt haben, klicken Sie auf die Schaltfläche **Anmelden...** . Weitere Informationen zur Anmeldung bei Azure Stack finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement](/azure-stack/user/azure-stack-storage-connect-se).

    ![Anmeldeoption][2]

4. Nach erfolgreicher Anmeldung mit einem Azure-Konto werden im linken Bereich das Konto und die mit diesem Konto verknüpften Azure Stack-Abonnements angezeigt. Wählen Sie die Azure-Abonnements, mit denen Sie arbeiten möchten, und klicken Sie dann auf **Anwenden** (durch Auswahl von **Alle Abonnements** werden alle bzw. keine der aufgelisteten Azure-Abonnements ausgewählt).

    ![Azure-Abonnements auswählen][3]

    Im linken Bereich werden die Speicherkonten angezeigt, die mit den ausgewählten Azure-Abonnements verknüpft sind.

    ![Ausgewählte Azure-Abonnements][4]

## <a name="work-with-local-development-storage"></a>Verwenden von lokalem Entwicklungsspeicher

Mit dem Storage-Explorer können Sie unter Verwendung eines Emulators Schritte für den lokalen Speicher ausführen. Dadurch können Sie die Verwendung von Azure Storage simulieren, ohne dass unbedingt ein Speicherkonto in Azure bereitgestellt werden muss.

Ab Version 1.1.0 unterstützt der Storage-Explorer die Verwendung eines lokalen Speicheremulators auf allen Plattformen. Mit dem Storage-Explorer kann eine Verbindung mit einem beliebigen emulierten Dienst hergestellt werden, der auf zugehörige lokale standardmäßige Speicherendpunkte lauscht.

Der [Azure-Speicheremulator](storage/common/storage-use-emulator.md) wird derzeit nur unter Windows ausgeführt. Eine Option für diejenigen, die einen Speicheremulator für Linux in Betracht ziehen, ist der von der Community unterstützte Open Source-Standardemulator [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Die Unterstützung für Speicherdienste und -funktionen kann abhängig von Ihrer Auswahl eines Emulators stark variieren. Stellen Sie sicher, dass Ihr Emulator die Dienste und Funktionen unterstützt, mit denen Sie arbeiten möchten.

1. Konfigurieren Sie die Dienste des Emulators Ihrer Wahl, um über einen nicht genutzten Port zu lauschen.

   Emulierter Dienst | Standardendpunkt
   -----------------|-------------------------
   Blobs (in englischer Sprache)            | `http://127.0.0.1:10000`
   Warteschlangen           | `http://127.0.0.1:10001`
   Tabellen           | `http://127.0.0.1:10002`

2. Starten Sie den Emulator.
   > [!IMPORTANT]
   > Storage-Explorer startet Ihren Emulator nicht automatisch. Sie müssen ihn selbst starten.

3. Klicken Sie im Storage-Explorer auf die Schaltfläche **Konto hinzufügen**. Wählen Sie **Attach to a local emulator** (An einen lokalen Emulator anfügen), und klicken Sie dann auf **Weiter**.

4. Geben Sie die Portnummern für die Dienste, die Sie oben konfiguriert haben (lassen Sie dies leer, wenn Sie nicht beabsichtigen, diesen Dienst zu nutzen). Klicken Sie auf **Weiter** und dann auf **Verbinden**, um die Verbindung zu erstellen.

5. Erweitern Sie **Local & Attached (Lokal und angefügt)**  > **Speicherkonten** > „Knoten“ und dann die Dienstknoten unter dem Knoten, der Ihrer Emulatorverbindung entspricht.

   Sie können mithilfe dieses Knotens lokale Blobs, Warteschlangen und Tabellen erstellen und mit diesen arbeiten. Weitere Informationen zur Verwendung der einzelnen Speicherkontotypen finden Sie in den folgenden Anleitungen:

   * [Verwalten von Azure Blob Storage-Ressourcen](vs-azure-tools-storage-explorer-blobs.md)
   * [Verwalten von Azure File-Speicherressourcen](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Anfügen oder Trennen eines externen Speicherkontos

Mit dem Storage-Explorer können Sie das Anfügen an externe Speicherkonten durchführen, damit Speicherkonten auf einfache Weise für die gemeinsame Nutzung freigegeben werden können. In diesem Abschnitt wird das Anfügen an externe Speicherkonten und das spätere Trennen beschrieben.

### <a name="get-the-storage-account-credentials"></a>Beschaffen der Anmeldeinformationen für Speicherkonten

Um ein externes Speicherkonto freigeben zu können, muss der Besitzer des Kontos zuerst die Anmeldeinformationen (Kontoname und Schlüssel) für das Konto ermitteln und diese Informationen dann an die Person weitergeben, die das Anfügen an das genannte Konto durchführen möchte. Gehen Sie im Azure-Portal wie folgt vor, um die Anmeldeinformationen für das Speicherkonto abzurufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Durchsuchen**.

3. Wählen Sie **Speicherkonten**.

4. Wählen Sie in der Liste **Speicherkonten** das gewünschte Speicherkonto aus.

5. Wählen Sie unter **Einstellungen** die Option **Zugriffsschlüssel** aus.

    ![Option „Zugriffsschlüssel“][7]

6. Kopieren Sie die Werte für **Speicherkontoname** und **Schlüssel1**.

    ![Zugriffsschlüssel][8]

### <a name="attach-to-an-external-storage-account"></a>Anfügen an ein externes Speicherkonto

Zum Anfügen an ein externes Speicherkonto benötigen Sie den Namen und Schlüssel des Kontos. Im Abschnitt „Beschaffen der Anmeldeinformationen für Speicherkonten“ erfahren Sie, wie Sie diese Werte über das Azure-Portal abrufen. Im Portal heißt der Kontoschlüssel allerdings **Schlüssel1**. Wenn im Storage-Explorer also ein Kontoschlüssel verlangt wird, geben Sie den Wert von **Schlüssel1** ein.

1. Öffnen Sie im Storage-Explorer das Dialogfeld **Verbinden**.

    ![Option „Mit Azure Storage verbinden“][9]

2. Wählen Sie im Dialogfeld **Verbinden** die Option **Verwenden eines Speicherkontonamens und -schlüssels**.

    ![Option „Mit Name und Schlüssel hinzufügen“][10]

3. Fügen Sie Ihren Kontonamen im Textfeld **Kontoname** und Ihren Kontoschlüssel (den Wert von **Schlüssel1** aus dem Azure-Portal) im Textfeld **Kontoschlüssel** ein. Klicken Sie anschließend auf **Weiter**.

    ![Seite „Name und Schlüssel“][11]

    > [!NOTE]
    > Wenn Sie einen Namen und einen Schlüssel aus einer nationalen Cloud verwenden möchten, klicken Sie auf die Dropdownliste **Domäne der Speicherendpunkte**, um die entsprechende Domäne der Endpunkte auszuwählen:
    >
    >

4. Überprüfen Sie die Angaben im Dialogfeld **Verbindungszusammenfassung**. Wenn Sie Änderungen vornehmen möchten, wählen Sie **Zurück** aus und geben die gewünschten Einstellungen erneut ein.

5. Wählen Sie **Verbinden**aus.

6. Nachdem das Speicherkonto erfolgreich angefügt wurde, wird es angezeigt. (Dabei wird **(Extern)** an den Speicherkontonamen angehängt.)

    ![Ergebnis der Verbindung mit einem externen Speicherkonto][12]

### <a name="detach-from-an-external-storage-account"></a>Trennen von einem externen Speicherkonto

1. Klicken Sie mit der rechten Maustaste auf das externe Speicherkonto, das Sie trennen möchten, und wählen Sie anschließend **Trennen** aus.

    ![Option zum Trennen vom Speicher][13]

2. Wählen Sie in der Bestätigungsmeldung die Option **Ja** aus, um das Trennen vom externen Speicherkonto zu bestätigen.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Anfügen eines Speicherkontos unter Verwendung einer Shared Access Signature (SAS)

Mithilfe einer Shared Access Signature, oder [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), kann der Administrator eines Azure-Abonnements temporären Zugriff auf ein Speicherkonto gewähren, ohne dass hierfür die zugehörigen Anmeldeinformationen angeben werden müssen.

Ein Beispiel: Benutzer A ist Administrator eines Azure-Abonnements, und Benutzer A möchte Benutzer B für einen begrenzten Zeitraum und mit bestimmten Berechtigungen Zugriff auf ein Speicherkonto gewähren:

1. Benutzer A generiert eine SAS-Verbindungszeichenfolge für den entsprechenden Zeitraum und mit den gewünschten Berechtigungen.

2. Benutzer A gibt die SAS an die Person weiter, die auf das Speicherkonto zugreifen möchte (in diesem Beispiel also an Benutzer B).

3. Benutzer B verwendet den Storage-Explorer, um das Anfügen an das Konto von Benutzer A mit der bereitgestellten SAS durchzuführen.

### <a name="generate-a-sas-query-string-for-the-account-you-want-to-share"></a>Generieren einer SAS-Abfragezeichenfolge für das freizugebende Konto

1. Klicken Sie im Storage-Explorer mit der rechten Maustaste auf das Speicherkonto, das Sie freigeben möchten, und wählen Sie **Shared Access Signature abrufen...** aus.

    ![Kontextmenüoption „SAS abrufen“][14]

2. Geben Sie im Dialogfeld **Shared Access Signature generieren** den Zeitrahmen und die gewünschten Berechtigungen für das Konto an, und klicken Sie anschließend auf die Schaltfläche **Erstellen**.

    ![Dialogfeld „SAS abrufen“][15]

3. Wählen Sie neben dem Textfeld **Abfragezeichenfolge** die Option **Kopieren** aus, um die Abfragezeichenfolge in die Zwischenablage zu kopieren, und klicken Sie anschließend auf **Schließen**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Anfügen an ein Speicherkonto unter Verwendung einer SAS-Verbindungszeichenfolge

1. Öffnen Sie im Storage-Explorer das Dialogfeld **Verbinden**.

    ![Option „Mit Azure Storage verbinden“][9]

2. Wählen Sie im Dialogfeld **Verbinden** die Option **Verwenden einer Verbindungszeichenfolge oder eines Shared Access Signature-URI**, und klicken Sie dann auf **Weiter**.

    ![Dialogfeld „Verbindung mit Azure-Speicher herstellen“][16]

3. Wählen Sie **Verwenden einer Verbindungszeichenfolge** aus, und fügen Sie die Verbindungszeichenfolge in das Feld **Verbindungszeichenfolge:** ein. Klicken Sie auf die Schaltfläche **Weiter**.

    ![Dialogfeld „Verbindung mit Azure-Speicher herstellen“][17]

4. Überprüfen Sie die Angaben im Dialogfeld **Verbindungszusammenfassung**. Wenn Sie etwas ändern möchten, wählen Sie **Zurück** aus, und geben Sie die gewünschten Einstellungen ein.

5. Wählen Sie **Verbinden**aus.

6. Nachdem das Speicherkonto erfolgreich angefügt wurde, wird es angezeigt. (Dabei wird **(SAS)** an den Speicherkontonamen angehängt.)

    ![Ergebnis des Anfügens an ein Konto unter Verwendung der SAS][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Anfügen eines Diensts unter Verwendung einer Shared Access Signature (SAS)

Im Abschnitt „Anfügen eines Speicherkontos unter Verwendung einer SAS“ wird beschrieben, wie ein Administrator eines Azure-Abonnements vorübergehenden Zugriff auf ein Speicherkonto gewähren kann, indem er eine SAS für das Speicherkonto generiert und weitergibt. Auf ähnliche Weise kann eine SAS für einen bestimmten Dienst (Blobcontainer, Warteschlange, Tabelle oder Dateifreigabe) in einem Speicherkonto generiert werden.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generieren einer SAS für den Dienst, den Sie freigeben möchten

In diesem Kontext kann es sich bei einem Dienst um einen Blobcontainer, eine Warteschlange, eine Tabelle oder eine Dateifreigabe handeln. Informationen zum Generieren der SAS für einen aufgeführten Dienst finden Sie in den folgenden Themen:

* [Abrufen der SAS für einen Blobcontainer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Anfügen an den freigegebenen Kontodienst unter Verwendung eines SAS-URI

1. Öffnen Sie im Storage-Explorer das Dialogfeld **Verbinden**.

    ![Option „Mit Azure Storage verbinden“][9]

2. Wählen Sie im Dialogfeld **Verbinden** die Option **Verwenden einer Verbindungszeichenfolge oder eines Shared Access Signature-URI**, und klicken Sie dann auf **Weiter**.

    ![Dialogfeld „Verbindung mit Azure-Speicher herstellen“][16]

3. Wählen Sie **Verwenden eines SAS-URI** aus, und fügen Sie Ihren URI in das Feld **URI:** ein. Klicken Sie auf die Schaltfläche **Weiter**.

    ![Dialogfeld „Verbindung mit Azure-Speicher herstellen“][19]

4. Überprüfen Sie die Angaben im Dialogfeld **Verbindungszusammenfassung**. Wenn Sie etwas ändern möchten, wählen Sie **Zurück** aus, und geben Sie die gewünschten Einstellungen ein.

5. Wählen Sie **Verbinden**aus.

6. Nachdem der Dienst erfolgreich angefügt ist, wird er unter dem Knoten **(Mit SAS angefügte Dienste)** angezeigt.

    ![Ergebnis des Anfügens an einen freigegebenen Dienst unter Verwendung einer SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto mithilfe einer Verbindungszeichenfolge

Neben der Verwaltung von Azure Cosmos DB-Konten über das Azure-Abonnement kann eine Verbindung mit einer Azure Cosmos DB alternativ auch mit einer Verbindungszeichenfolge hergestellt werden. Gehen Sie folgendermaßen vor, um eine Verbindung mithilfe einer Verbindungszeichenfolge herzustellen.

1. Suchen Sie in der linken Struktur nach **Lokal und angefügt**, klicken Sie mit der rechten Maustaste auf **Azure Cosmos DB-Konten**, und wählen Sie **Mit Azure-Cosmos-Datenbank verbinden** aus.

    ![Herstellen einer Verbindung mit Azure Cosmos DB mithilfe einer Verbindungszeichenfolge][21]

2. Wählen Sie die Azure Cosmos DB-API aus, fügen Sie Ihre **Verbindungszeichenfolge** ein, und klicken Sie dann auf **OK**, um eine Verbindung mit dem Azure Cosmos DB-Konto herzustellen. Informationen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen der Verbindungszeichenfolge](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Herstellen einer Verbindung mit Azure Data Lake Store über den URI

Wenn Sie auf Ressourcen zugreifen möchten, die in Ihrem Abonnement nicht vorhanden sind, müssen andere Personen Ihnen die Berechtigung zum Abrufen des URI für die Ressourcen gewähren. In diesem Fall können Sie eine Verbindung mit der Data Lake Store-Instanz herstellen, indem Sie nach dem Anmelden den URI verwenden. Führen Sie die folgenden Schritte aus.

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich die Option **Local and Attached** (Lokal und angefügt).
3. Klicken Sie mit der rechten Maustaste auf **Data Lake Store**, und wählen Sie im Kontextmenü die Option **Connect to Data Lake Store...** (Verbindung mit Data Lake Store herstellen...).

    ![Kontextmenü „Connect to Data Lake Store...“ (Verbindung mit Data Lake Store herstellen...)](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Geben Sie den URI ein. Das Tool navigiert zum gerade eingegebenen URL-Speicherort.

    ![Dialogfeld „Connect to Data Lake Store...“ (Verbindung mit Data Lake Store herstellen...)](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Verbindung mit Data Lake Store herstellen – Ergebnis](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>Suchen nach Speicherkonten

Wenn Sie eine Speicherressource suchen müssen und nicht wissen, wo sie sich befindet, können Sie hierfür das Suchfeld oben im linken Bereich verwenden.

Sobald Sie Text in das Suchfeld eingeben, werden im linken Bereich alle Ressourcen angezeigt, die dem bislang eingegebenen Suchwert entsprechen. Im folgenden Screenshot wird zum Beispiel die Suche nach dem Begriff **Endpunkte** gezeigt:

![Speicherkontosuche][23]

> [!NOTE]
> Deaktivieren Sie über das **Panel für Kontoverwaltung** alle Abonnements, die das gesuchte Element nicht enthalten, um die Ausführungsdauer Ihres Suchvorgangs zu verkürzen. Alternativ können Sie mit der rechten Maustaste auf einen Knoten klicken und **Ab hier suchen** wählen, um die Suche von einem bestimmten Knoten aus zu starten.
>
>

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Azure Blob Storage-Ressourcen mit dem Storage-Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Verwalten von Azure Cosmos DB in Azure Storage-Explorer (Vorschau)](./cosmos-db/storage-explorer.md)
* [Verwalten von Azure Data Lake Store-Ressourcen mit Storage-Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
