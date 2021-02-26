---
title: 'Tutorial: Überprüfen von Daten mit Azure Purview (Vorschau)'
description: In diesem Tutorial führen Sie ein Starter Kit aus, um einen Datenbestand einzurichten und anschließend Daten aus Datenquellen in Ihrem Azure Purview-Katalog zu überprüfen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: a88c837c986db346bcb4fbb1b15ceab6a304347d
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806222"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Tutorial: Überprüfen von Daten mit Azure Purview (Vorschau)

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Diese *fünfteilige Tutorialreihe* enthält grundlegende Informationen zur Verwaltung der Datengovernance in einem Datenbestand mithilfe von Azure Purview (Vorschau). Der in diesem Teil des Tutorials erstellte Datenbestand wird auch für die restliche Reihe verwendet.

In Teil 1 dieser Tutorialreihe führen Sie die folgenden Aktionen aus:

> [!div class="checklist"]
>
> * Erstellen eines Datenbestands mit verschiedenen Azure-Datenressourcen
> * Überprüfen von Daten in einem Katalog

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
* Ein [Azure Purview-Konto](create-catalog-portal.md)
* Das [Starter Kit](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip), mit dem Ihr Datenbestand bereitgestellt wird

> [!NOTE]
> Das Starter Kit ist nur für Windows verfügbar.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-data-estate"></a>Erstellen eines Datenbestands

In diesem Abschnitt führen Sie die Starter Kit-Skripts aus, um einen simulierten Datenbestand zu erstellen. Bei einem Datenbestand handelt es sich um ein Portfolio aller Daten, die sich im Besitz eines Unternehmens befinden. Das Starter Kit-Skript führt folgende Aktionen aus:

* Erstellen eines Azure Blob Storage-Kontos und Auffüllen des Kontos mit Daten
* Erstellen eines Azure Data Lake Storage Gen2-Kontos
* Erstellen einer Azure Data Factory-Instanz und Zuordnen der Instanz zu Azure Purview
* Einrichten und Auslösen einer Copy-Aktivitätspipeline zwischen dem Azure Blob Storage-Konto und dem Azure Data Lake Storage Gen2-Konto
* Pushen der zugehörigen Herkunft von Azure Data Factory an Azure Purview

### <a name="prepare-to-run-the-starter-kit"></a>Vorbereiten der Ausführung des Starter Kits

Führen Sie die folgenden Schritte aus, um die Starter Kit-Clientsoftware auf Ihrem Windows-Computer einzurichten:

1. [Laden Sie das Starter Kit](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip) herunter, und extrahieren Sie seinen Inhalt am Speicherort Ihrer Wahl.


1. Geben Sie auf Ihrem Computer **PowerShell** in das Suchfeld auf der Windows-Taskleiste ein. Klicken Sie in der Suchliste mit der rechten Maustaste auf **Windows PowerShell**, und wählen Sie **Als Administrator ausführen** aus.

1. Geben Sie im PowerShell-Fenster den folgenden Befehl ein, und ersetzen Sie dabei `<path-to-starter-kit>` durch den Ordnerpfad der extrahierten Starter Kit-Dateien.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Geben Sie den folgenden Befehl ein, um die Azure-Cmdlets zu installieren:

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Wird die Warnmeldung *Der NuGet-Anbieter ist erforderlich, um den Vorgang fortzusetzen.* angezeigt, geben Sie **J** ein, und drücken Sie die EINGABETASTE.

1. Wird die Warnmeldung *Nicht vertrauenswürdiges Repository* angezeigt, geben Sie **A** ein, und drücken Sie die EINGABETASTE.

Es kann bis zu einer Minute dauern, bis PowerShell die erforderlichen Module installiert hat.

### <a name="collect-data-needed-to-run-the-scripts"></a>Sammeln der zum Ausführen der Skripts erforderlichen Daten

Bevor Sie die PowerShell-Skripts für das Bootstrapping des Katalogs ausführen, müssen Sie die Werte der folgenden Argumente zur Verwendung in den Skripts abrufen:

* Mandanten-ID
   1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
   1. Wählen Sie im Abschnitt **Verwalten** des linken Navigationsbereichs die Option **Eigenschaften** aus. Wählen Sie dann das Kopiersymbol für **Mandanten-ID** aus, um den Wert in der Zwischenablage zu speichern. Fügen Sie den Wert zur späteren Verwendung in einen Text-Editor ein.

* SubscriptionID
   1. Suchen Sie im Azure-Portal nach dem Namen der Azure Purview-Instanz, die Sie unter „Voraussetzungen“ erstellt haben, und wählen Sie sie aus.
   1. Wählen Sie den Abschnitt **Übersicht** aus, und speichern Sie die GUID für die **Abonnement-ID**.

   > [!NOTE]
   > - Verwenden Sie unbedingt dasselbe Abonnement, unter dem Sie das Azure Purview-Konto erstellt haben. Dabei handelt es sich um dasselbe Abonnement, das in die Zulassungsliste aufgenommen wurde.
   > - In Azure Purview kann es vorkommen, dass nach dem Ausführen des Starter Kits die Herkunft fehlt. Das liegt daran, dass in Purview Berechtigungen für die durch das Starter Kit erstellte Data Factory fehlen. Wählen Sie [**diesen Dokumentlink**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections) aus, um sicherzustellen, dass die Data Factory korrekt konfiguriert und ihr die passende Rolle in Purview zugewiesen ist.


* CatalogName: Der Name des Azure Purview-Kontos, das Sie unter [Erstellen eines Azure Purview-Kontos](create-catalog-portal.md) erstellt haben

* CatalogResourceGroupName: Der Name der Ressourcengruppe, in der Sie das Azure Purview-Konto erstellt haben

### <a name="verify-permissions"></a>Überprüfen von Berechtigungen

Führen Sie die folgenden Schritte aus, um den Benutzer, der das Skript ausführt, dem Azure Purview-Konto hinzuzufügen, das unter „Voraussetzungen“ erstellt wurde. Benutzer benötigen sowohl die Rolle *Datenkurator für Purview* als auch die Rolle *Datenquellenadministrator für Purview*. 

Wenn Sie das Azure Purview-Konto selbst erstellt haben, erhalten Sie automatisch Zugriff und können diesen Abschnitt überspringen.

1. Navigieren Sie im Azure-Portal zur Seite Purview accounts (Purview-Konten), und wählen Sie das zu ändernde Azure Purview-Konto aus.

1. Wählen Sie auf der Seite des Kontos die Registerkarte **Zugriffssteuerung (IAM)** und dann **+ Hinzufügen** aus.

1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

1. Geben Sie unter *Rolle* die Rolle **Datenkurator für Purview** ein.
 
1. Übernehmen Sie für *Zugriff zuweisen zu* den Standardwert. Der Standardwert sollte **Benutzer, Gruppe oder Dienstprinzipal** lauten.

1. Geben Sie unter **Auswählen** den Namen des Benutzers ein, der das Skript ausführt.

1. Wählen Sie **Speichern** aus.

1. Wiederholen Sie die vorherigen Schritte. Legen Sie dabei *Rolle* auf **Datenquellenadministrator für Purview** fest.

Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>Ausführen der clientseitigen Setupskripts

Führen Sie nach Abschluss der Katalogkonfiguration die folgenden Skripts im PowerShell-Fenster aus, um die Ressourcen zu erstellen, und ersetzen Sie dabei die Platzhalter durch die zuvor gesammelten Werte.

1. Verwenden Sie den folgenden Befehl, um zum Verzeichnis des Starter Kits zu navigieren. Ersetzen Sie `path-to-starter-kit` durch den Ordnerpfad der extrahierten Datei.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. Mit dem folgenden Befehl wird die Ausführungsrichtlinie für den lokalen Computer festgelegt. Geben Sie **A** für *Ja, alle* ein, wenn Sie zur Änderung der Ausführungsrichtlinie aufgefordert werden.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Stellen Sie über den folgenden Befehl eine Verbindung mit Azure her. Ersetzen Sie die Platzhalter `TenantID` und `SubscriptionID` .

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Bei der Ausführung des Befehls wird möglicherweise ein Popupfenster angezeigt, in dem Sie sich mit Ihren Azure Active Directory-Anmeldeinformationen anmelden können.


1. Verwenden Sie den folgenden Befehl, um das Starter Kit auszuführen. Ersetzen Sie die Platzhalter `CatalogName`, `TenantID`, `SubscriptionID`, `NewResourceGroupName` und `CatalogResourceGroupName`. Verwenden Sie für `NewResourceGroupName` einen eindeutigen Namen (der ausschließlich aus alphanumerischen Kleinbuchstaben besteht) für die Ressourcengruppe, die den Datenbestand enthält.

   > [!IMPORTANT]
   > Für **newresourcegroupname** dürfen nur Zahlen und Kleinbuchstaben verwendet werden, und der Name darf maximal 17 Zeichen lang sein. **Großbuchstaben und Sonderzeichen sind nicht zulässig.** Diese Einschränkung stammt aus den Benennungsregeln für Speicherkonten.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Die Einrichtung der Umgebung kann bis zu zehn Minuten dauern. Während dieses Zeitraums werden unter Umständen verschiedene Popupfenster angezeigt, die Sie ignorieren können. Schließen Sie nicht das Fenster **BlobDataCreator.exe**. Es wird nach Abschluss des Vorgangs automatisch geschlossen.

Wird die Meldung `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` angezeigt, warten Sie, bis eine andere Instanz von **BlobDataCreator.exe** gestartet und ihre Ausführung beendet wurde.

> [!IMPORTANT]
> Falls sich die Anzahl der aktiven Aufgaben nicht mehr verringert, können Sie das Fenster für die Bloberstellung schließen und im PowerShell-Fenster die EINGABETASTE drücken.

Nach Abschluss des Vorgangs wird eine Ressourcengruppe mit dem von Ihnen angegebenen Namen erstellt. Das Azure Data Factory-Konto, das Azure Blob Storage-Konto und das Azure Data Lake Storage Gen2-Konto sind in dieser Ressourcengruppe enthalten. Die Ressourcengruppe ist in dem von Ihnen angegebenen Abonnement enthalten.

## <a name="scan-data-into-the-catalog"></a>Überprüfen von Daten im Katalog

Bei der Überprüfung wird vom Katalog nach einem vom Benutzer angegebenen Zeitplan direkt eine Verbindung mit einer Datenquelle hergestellt. Der Katalog gibt den Datenbestand eines Unternehmens durch Überprüfung, Herkunft, Portal und API an. Zu den Zielen gehören das Untersuchen der Inhalte, das Extrahieren von Schemas und das Nachvollziehen der Semantik. In diesem Abschnitt richten Sie eine Überprüfung der Datenquellen ein, die Sie mit dem Starter Kit generiert haben.

Mit dem von Ihnen ausgeführten Starter Kit-Skript wurden zwei Datenquellen erstellt: Azure Blob Storage und Azure Data Lake Storage Gen2. Diese Datenquellen können nacheinander im Katalog überprüft werden.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Authentifizieren beim Speicher mithilfe einer verwalteten Identität

Bei der Erstellung Ihres Kontos wird automatisch eine verwaltete Identität erstellt, die den gleichen Namen wie Ihr Azure Purview-Konto hat. Bevor Sie Ihre Daten überprüfen können, müssen Sie Ihren Speicherkonten Azure Purview-Rollenberechtigungen erteilen.

1. Navigieren Sie zu der Ressourcengruppe, die vom Starter Kit erstellt wurde, und wählen Sie Ihr Blobspeicherkonto aus.

1. Wählen Sie im linken Navigationsmenü **Zugriffssteuerung (IAM)** aus. Wählen Sie dann **+ Hinzufügen** aus.

1. Legen Sie als Rolle **Leser von Speicherblobdaten** fest, und geben Sie unter **Auswählen** den Namen Ihres Azure Purview-Kontos ein. Wählen Sie dann **Speichern** aus, um diese Rollenzuweisung für Ihr Purview-Konto festzulegen.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Hinzufügen der Rollenzuweisung":::

1. Wiederholen Sie die vorherigen Schritte für Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Überprüfen der Datenquellen

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure Purview-Ressource, und wählen Sie *Open Purview Studio* (Purview Studio öffnen) aus. Sie werden automatisch zur Startseite von Purview Studio weitergeleitet.

1. Wählen Sie auf der Webseite des Katalogs **Quellen** und dann **Registrieren** aus. Wählen Sie anschließend **Azure Blob Storage** und **Weiter** aus.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Registrieren von Blobspeicherressourcen":::

1. Geben Sie auf der Seite **Register sources** (Quellen registrieren) unter **Name** einen Namen ein. Wählen Sie unter **Speicherkontoname** den Namen des Azure Blob Storage-Kontos aus, das Sie zuvor mit dem Starter Kit erstellt haben. Das Konto hat den Namen `<YourResourceGroupName>adcblob`. Wählen Sie **Fertig stellen** aus.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Screenshot: Einstellungen zum Registrieren einer Azure Blob Storage-Datenquelle" border="true":::

1. Wählen Sie in der Zuordnungsansicht **Datenquellen** auf der Kachel „Azure Blob Storage“ die Option **New scan** (Neue Überprüfung) aus.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Screenshot: Auswählen eines Überprüfungssetups auf der Grundlage einer Datenquelle" border="true":::

1. Geben Sie auf der Seite **Überprüfung** einen Namen für die Überprüfung ein, und wählen Sie im Dropdownmenü **Anmeldeinformationen** Ihre verwaltete Identität und anschließend **Weiter** aus.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Überprüfen des Blobspeichers in Azure Purview":::

1. Sie können die Überprüfung auf einzelne Blobs beschränken. Übernehmen Sie für dieses Tutorial die Auswahl aller Ressourcen, damit alles überprüft wird, und wählen Sie **Weiter** aus.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Festlegen des Bereichs für die Speicherüberprüfung":::

1. Wählen Sie den Standardregelsatz für die Überprüfung und anschließend **Weiter** aus.

1. Sie können für regelmäßig wiederholte Überprüfungen einen Auslöser für die Überprüfung einrichten. Wählen Sie in diesem Tutorial **Einmalig** und anschließend **Weiter** aus.

1. Wählen Sie **Speichern und ausführen** aus, um die Überprüfung abzuschließen.

1. Wiederholen Sie die vorherigen Schritte zur Überprüfung des Azure Data Lake Storage Gen2-Kontos.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
>
> * Führen Sie das Starter Kit-Skript aus, um eine Umgebung für den Datenbestand einzurichten.
> * Überprüfen von Daten in einem Azure Purview-Katalog

Im nächsten Tutorial erfahren Sie, wie Sie auf der Startseite navigieren und nach einer Ressource suchen.

> [!div class="nextstepaction"]
> [Tutorial: Navigieren auf der Startseite von Azure Purview (Vorschau) und Suchen nach einer Ressource](tutorial-asset-search.md)
