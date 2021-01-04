---
title: Verschieben von Azure Analysis Services in eine andere Region | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Azure Analysis Services-Ressource in eine andere Region verschieben.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497927"
---
# <a name="move-analysis-services-to-a-different-region"></a>Verschieben von Analysis Services in eine andere Region

In diesem Artikel wird beschrieben, wie Sie eine Analysis Services-Serverressource in eine andere Azure-Region verschieben. Für das Verschieben Ihres Servers in eine andere Region kann es verschiedene Gründe geben, beispielsweise um eine Azure-Region zu nutzen, die weniger weit von den Benutzern entfernt ist, um Servicepläne zu verwenden, die nur in bestimmten Regionen unterstützt werden, oder um interne Richtlinien- und Governanceanforderungen zu erfüllen. 

In diesem und in den dazugehörigen verknüpften Artikeln werden folgende Schritte erläutert:

> [!div class="checklist"]
> 
> * Sichern einer Quellserver-Modelldatenbank in [Blob Storage](../storage/blobs/storage-blobs-introduction.md)
> * Exportieren einer Quellserver-[Ressourcenvorlage](../azure-resource-manager/templates/overview.md)
> * Abrufen einer [Shared Access Signature (SAS)](../storage/common/storage-sas-overview.md) für Speicher
> * Ändern der Ressourcenvorlage
> * Bereitstellen der Vorlage, um einen neuen Zielserver zu erstellen
> * Wiederherstellen einer Modelldatenbank auf dem neuen Zielserver
> * Überprüfen des neuen Zielservers und der neuen Zieldatenbank
> * Löschen des Quellservers

In diesem Artikel wird beschrieben, wie Sie mithilfe einer Ressourcenvorlage einen einzelnen Analysis Services-Server mit einer **Basiskonfiguration** zu einer anderen Region *und* Ressourcengruppe im gleichen Abonnement migrieren. Durch die Verwendung einer Vorlage stellen Sie sicher, dass konfigurierte Servereigenschaften beibehalten werden und der Zielserver – mit Ausnahme der Region und Ressourcengruppe – mit den gleichen Eigenschaften wie der Quellserver konfiguriert wird. In diesem Artikel wird nicht beschrieben, wie Sie zugeordnete Ressourcen verschieben, die zur gleichen Ressourcengruppe gehören (z. B. Datenquellen-, Speicher- und Gatewayressourcen). 

Bevor Sie einen Server in eine andere Region verschieben, sollten Sie einen detaillierten Plan erstellen. Berücksichtigen Sie zusätzliche Ressourcen wie Gateways und Speicher, die möglicherweise auch verschoben werden müssen. Bei jedem Plan ist es wichtig, die Verschiebung mindestens einmal mit Testservern zu testen, bevor Sie einen Produktionsserver verschieben.

> [!IMPORTANT]
> Clientanwendungen und -verbindungszeichenfolgen stellen anhand des vollständigen Servernamens (ein URI, der die Region des Servers enthält) eine Verbindung mit Analysis Services her. Beispiel: `asazure://westcentralus.asazure.windows.net/advworks01`. Wenn Sie einen Server in eine andere Region verschieben, erstellen Sie dadurch eine neue Serverressource in einer anderen Region, deren Servernamen-URI eine andere Region enthält. In Skripts verwendete Clientanwendungen und -verbindungszeichenfolgen müssen anhand des neuen Servernamen-URI eine Verbindung mit dem neuen Server herstellen. Durch die Verwendung eines [Servernamenalias](analysis-services-server-alias.md) kann die Anzahl von Stellen verringert werden, an denen der Servernamen-URI geändert werden muss, der Alias muss jedoch vor dem Verschieben in eine andere Region implementiert werden.

> [!IMPORTANT]
> Azure-Regionen verwenden unterschiedliche IP-Adressbereiche. Wenn für die Region, in der sich Ihr Server und/oder Ihr Speicherkonto befinden, Firewallausnahmen konfiguriert sind, müssen Sie möglicherweise einen anderen IP-Adressbereich konfigurieren. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Netzwerkkonnektivität von Analysis Services](analysis-services-network-faq.md).

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie auf einem Zielserver eine Datenbanksicherung aus einem Speichercontainer in der Region des Quellservers wiederherstellen. In einigen Fällen kann die Leistung beim Wiederherstellen von Sicherungen aus einer anderen Region schlecht sein, insbesondere bei großen Datenbanken. Migrieren Sie zu einem neuen Speichercontainer in der Region des Zielservers, oder erstellen Sie dort einen neuen Speichercontainer, um bei der Datenbankwiederherstellung die bestmögliche Leistung zu erzielen. Kopieren Sie die ABF-Sicherungsdateien aus dem Speichercontainer in der Quellregion in den Speichercontainer in der Zielregion, bevor Sie die Datenbank auf dem Zielserver wiederherstellen. In manchen Fällen (insbesondere bei sehr großen Datenbanken) können das Erstellen eines Skripts für eine Datenbank vom Quellserver, das Neuerstellen und anschließende Verarbeiten auf dem Zielserver zum Laden von Datenbankdaten kostengünstiger sein als die Sicherung/Wiederherstellung.

> [!NOTE]
> Wenn Sie ein lokales Datengateway zum Herstellen einer Verbindung mit Datenquellen verwenden, müssen Sie auch die Gatewayressource in die Region des Zielservers verschieben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren eines lokalen Datengateways](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Speicherkonto**: Ist zum Speichern einer ABF-Sicherungsdatei erforderlich.
- **SQL Server Management Studio (SSMS)** : Ist zum Sichern und Wiederherstellen von Modelldatenbanken erforderlich.
- **Azure PowerShell**. Ist nur erforderlich, wenn Sie diese Aufgabe mit PowerShell durchführen.

## <a name="prepare"></a>Vorbereiten

### <a name="backup-model-databases"></a>Sichern von Modelldatenbanken

Falls noch keine **Speichereinstellungen** für den Quellserver konfiguriert sind, folgen Sie den Schritten unter [Konfigurieren von Speichereinstellungen](analysis-services-backup.md#configure-storage-settings).

Wenn Speichereinstellungen konfiguriert sind, folgen Sie den Schritten unter [Backup](analysis-services-backup.md#backup), um eine ABF-Sicherungsdatei einer Modelldatenbank in Ihrem Speichercontainer zu erstellen. Die ABF-Sicherung stellen Sie später auf Ihrem neuen Zielserver wieder her.


### <a name="export-template"></a>Exportieren der Vorlage

Die Vorlage enthält Konfigurationseigenschaften des Quellservers.

# <a name="portal"></a>[Portal](#tab/azure-portal)

So exportieren Sie eine Vorlage mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Alle Ressourcen** und dann Ihren Analysis Services-Server aus.

3. Wählen Sie **Einstellungen** > **Vorlage exportieren** aus.

4. Wählen Sie **Herunterladen** auf dem Blatt **Vorlage exportieren** aus.

5. Suchen Sie nach der ZIP-Datei, die Sie aus dem Portal heruntergeladen haben, und entzippen Sie sie in einen Ordner.

   Die ZIP-Datei enthält die JSON-Dateien mit der Vorlage und den erforderlichen Parametern zum Bereitstellen eines neuen Servers.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

So exportieren Sie eine Vorlage mithilfe von PowerShell

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement der Serverressource fest, die Sie verschieben möchten.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportieren Sie die Vorlage Ihres Quellservers. Durch diese Befehle wird in Ihrem aktuellen Verzeichnis eine JSON-Vorlage mit dem Namen der Ressourcengruppe (ResourceGroupName) als Dateiname gespeichert.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Abrufen einer Shared Access Signature (SAS) für Speicher

Wenn Sie einen Zielserver anhand einer Vorlage bereitstellen, ist ein SAS-Token für die Benutzerdelegierung (in Form eines URI) erforderlich, um den Speichercontainer mit der Datenbanksicherung anzugeben.

# <a name="portal"></a>[Portal](#tab/azure-portal)

So rufen Sie eine Shared Access Signature über das Portal ab:

1. Wählen Sie im Portal das Speicherkonto aus, das zum Sichern Ihrer Serverdatenbank verwendet wird.

2. Wählen Sie **Storage-Explorer** aus, und erweitern Sie dann **BLOB-Container**. 

3. Klicken Sie mit der rechten Maustaste auf den Speichercontainer, und wählen Sie dann **Shared Access Signature abrufen** aus.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Abrufen einer SAS":::

4. Wählen Sie unter **Shared Access Signature** die Option **Erstellen** aus. Standardmäßig läuft die SAS nach 24 Stunden ab.

5. Kopieren und speichern Sie den **URI**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine Shared Access Signature mithilfe von PowerShell abzurufen, folgen Sie den Schritten unter [Erstellen einer SAS für die Benutzerdelegierung für ein Blob mit PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Ändern der Vorlage

Verwenden Sie einen Text-Editor, um in der Datei „template.json“, die Sie exportiert haben, die Regions- und BLOB-Container-Eigenschaften zu ändern. 

So ändern Sie die Vorlage:

1. Geben Sie in einem Text-Editor in der Eigenschaft **location** die neue Zielregion an. Fügen Sie in der Eigenschaft **backupBlobContainerUri** den Speichercontainer-URI mit dem SAS-Schlüssel ein. 

    Im folgenden Beispiel wird die Zielregion für den Server „advworks1“ auf `South Central US` festgelegt und der Speichercontainer-URI mit der SAS angegeben: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Speichern Sie die Vorlage.

#### <a name="regions"></a>Regions

Die Azure-Regionen finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/). Um Regionen mithilfe von PowerShell abzurufen, führen Sie den Befehl [Get-AzLocation](/powershell/module/az.resources/get-azlocation) aus.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Move

Um eine neue Serverressource in einer anderen Region bereitzustellen, verwenden Sie die Datei **template.json**, die Sie in den vorherigen Abschnitten exportiert und geändert haben.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im Portal **Ressource erstellen** aus.

2. Geben Sie in **Marketplace durchsuchen** den Begriff **Vorlagenbereitstellung** ein, und drücken Sie dann die **EINGABETASTE**.

3. Wählen Sie **Vorlagenbereitstellung** aus.

4. Klicken Sie auf **Erstellen**.

5. Wählen Sie **Eigene Vorlage im Editor erstellen**.

6. Wählen Sie **Datei laden** aus, und folgen Sie dann den Anweisungen, um die Datei **template.json** zu laden, die Sie zuvor exportiert und geändert haben.

7. Vergewissern Sie sich, dass im Vorlagen-Editor die richtigen Eigenschaften für den neuen Zielserver angezeigt werden.

8. Wählen Sie **Speichern** aus.

9. Geben Sie die Eigenschaftswerte ein oder wählen Sie sie aus:

    - **Abonnement**: Wählen Sie das Azure-Abonnement aus.
    
    - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie dann einen Ressourcengruppennamen ein. Sie können eine vorhandene Ressourcengruppe auswählen, sofern diese nicht bereits einen Analysis Services-Server mit demselben Namen enthält.
    
    - **Standort**: Wählen Sie die Region aus, die Sie in der Vorlage angegeben haben.

10. Klicken Sie auf **Überprüfen und erstellen**.

11. Lesen Sie die Geschäftsbedingungen, überprüfen Sie die grundlegenden Einstellungen, und wählen Sie dann **Erstellen** aus.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie diese Befehle, um Ihre Vorlage bereitzustellen:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Abrufen des Zielserver-URI

Um zum Wiederherstellen der Modelldatenbank in SSMS eine Verbindung mit dem neuen Zielserver herzustellen, müssen Sie den URI des neuen Zielservers abrufen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

So rufen Sie den Server-URI über das Portal ab:

1. Wechseln Sie im Portal zur neuen Zielserverressource.

2. Kopieren Sie auf der Seite **Übersicht** den URI **Servername**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um den Server-URI mithilfe von PowerShell abzurufen, verwenden Sie die folgenden Befehle:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Kopieren Sie den vollständigen Servernamen (**ServerFullName**) in der Ausgabe.

---

### <a name="restore-model-database"></a>Wiederherstellen der Modelldatenbank

Folgen Sie den Schritten unter [Restore](analysis-services-backup.md#restore), um die ABF-Sicherungsdatei der Modelldatenbank auf dem neuen Zielserver wiederherzustellen.

Optional: Verarbeiten Sie nach dem Wiederherstellen der Modelldatenbank das Modell und die Tabellen, um Daten aus den Datenquellen zu aktualisieren. So verarbeiten Sie das Modell und die Tabelle mithilfe von SSMS:

1. Klicken Sie in SSMS mit der rechten Maustaste auf die Modelldatenbank, und wählen Sie dann **Datenbank verarbeiten** aus.

2. Erweitern Sie **Tabellen**, und klicken Sie mit der rechten Maustaste auf eine Tabelle. Wählen Sie unter **Tabelle(n) verarbeiten** alle Tabellen aus, und klicken Sie dann auf **OK**.

## <a name="verify"></a>Überprüfen

1. Wechseln Sie im Portal zum neuen Zielserver.

2. Überprüfen Sie auf der Seite „Übersicht“, ob die wiederhergestellten Modelle unter **Modelle auf Analysis Services-Server** angezeigt werden.

3. Verwenden Sie eine Clientanwendung wie Power BI oder Excel, um eine Verbindung mit dem Modell auf dem neuen Server herzustellen. Überprüfen Sie, ob Modellobjekte wie Tabellen, Measures und Hierarchien angezeigt werden. 

4. Führen Sie beliebige Automatisierungsskripts aus. Vergewissern Sie sich, dass sie erfolgreich ausgeführt wurden.

Optional: Das [ALM-Toolkit](http://alm-toolkit.com/) ist ein *Open Source*-Tool, mit dem Sie Power BI-Datasets *und* tabellarische Analysis Services-Modelldatenbanken vergleichen und verwalten können. Verwenden Sie das Toolkit, um eine Verbindung mit Datenbanken auf dem Quell- und Zielserver herzustellen und sie zu vergleichen. Wenn Ihre Datenbankmigration erfolgreich war, weisen Modellobjekte die gleiche Definition auf. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM-Toolkit":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie sich vergewissert haben, dass Clientanwendungen eine Verbindung mit dem neuen Server herstellen können und Automatisierungsskripts korrekt ausgeführt werden, löschen Sie Ihren Quellserver. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

So löschen Sie den Quellserver über das Portal:

Wählen Sie auf der Seite **Übersicht** des Quellservers die Option **Löschen** aus.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um den Quellserver mithilfe von PowerShell zu löschen, führen Sie den Befehl „Remove-AzAnalysisServicesServer“ aus.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Es wird empfohlen, nach dem Verschieben in eine andere Region für den neuen Zielserver einen Speichercontainer in derselben Region für Sicherungen zu verwenden anstelle des Speichercontainers in der Region des Quellservers.