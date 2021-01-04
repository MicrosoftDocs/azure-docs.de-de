---
title: Tutorial zum Bestellen einer Azure Data Box | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie mehr über die Hybridlösung Azure Data Box, mit der Sie lokale Daten in Azure importieren können. Darüber hinaus wird erläutert, wie Sie Azure Data Box bestellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: alkohli
ms.openlocfilehash: db97a34a79ac45525177a79a59a17426003aeab0
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302626"
---
# <a name="tutorial-order-azure-data-box"></a>Tutorial: Bestellen von Azure Data Box

Azure Data Box ist eine Hybridlösung, mit der Sie Ihre lokalen Daten auf schnelle, einfache und zuverlässige Weise in Azure importieren können. Sie übertragen Ihre Daten auf ein von Microsoft bereitgestelltes Speichergerät mit 80 TB nutzbarer Kapazität und senden das Gerät anschließend zurück. Diese Daten werden dann in Azure hochgeladen.

In diesem Tutorial wird beschrieben, wie Sie eine Azure Data Box bestellen können. In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen für die Bereitstellung von Data Box
> * Bestellen einer Data Box
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/portal)

Bevor Sie das Gerät bereitstellen, müssen die folgenden Konfigurationsvoraussetzungen für den Data Box-Dienst und das Data Box-Gerät erfüllt sein:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Sie können sich bei Azure anmelden und Azure-CLI-Befehle ausführen. Dazu haben Sie zwei Möglichkeiten:

* Sie können die Befehlszeilenschnittstelle installieren und CLI-Befehle lokal ausführen.
* Sie können CLI-Befehle innerhalb des Azure-Portals in Azure Cloud Shell ausführen.

Für das Tutorial verwenden wir die Azure CLI über Windows PowerShell, aber Sie können auch eine andere Option wählen.

### <a name="for-azure-cli"></a>Für die Azure-Befehlszeilenschnittstelle (CLI)

Stellen Sie Folgendes sicher, bevor Sie beginnen:

#### <a name="install-the-cli-locally"></a>Lokales Installieren der Befehlszeilenschnittstelle

* Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli) (Version 2.0.67 oder höher). Alternativ können Sie auch die [MSI für die Installation verwenden](https://aka.ms/installazurecliwindows).

**Anmelden bei Azure**

Öffnen Sie ein Windows PowerShell-Befehlsfenster, und melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Azure an:

```azurecli
PS C:\Windows> az login
```

Hier ist die Ausgabe angegeben, die nach einer erfolgreichen Anmeldung angezeigt wird:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Installieren der Azure Data Box-CLI-Erweiterung**

Bevor Sie die Azure Data Box-CLI-Befehle verwenden können, müssen Sie die Erweiterung installieren. Mit Azure CLI-Erweiterungen erhalten Sie Zugriff auf experimentelle Befehle und Vorabversionen von Befehlen, die noch nicht als Bestandteil der Kern-CLI bereitgestellt wurden. Weitere Informationen zu Erweiterungen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).

Führen Sie den folgenden Befehl aus, um die Erweiterung für Azure Data Box zu installieren: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Wenn die Installation der Erweiterung erfolgreich war, wird die folgende Ausgabe angezeigt:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Verwenden von Azure Cloud Shell

Sie können [Azure Cloud Shell](https://shell.azure.com/) (in Azure gehostete interaktive Shellumgebung) über Ihren Browser verwenden, um CLI-Befehle auszuführen. Azure Cloud Shell unterstützt Bash oder Windows PowerShell mit Azure-Diensten. Die Azure-Befehlszeilenschnittstelle ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Wählen Sie im Azure-Portal im Menü oben rechts die Schaltfläche „Cloud Shell“ aus:

![Menüauswahl: Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Über die Schaltfläche wird eine interaktive Shell geöffnet, mit der Sie die in diesem Artikel beschriebenen Schritte ausführen können.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Für Azure PowerShell

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Installieren Sie Windows PowerShell 6.2.4 oder höher.
* Installieren Sie das Azure PowerShell-Modul (AZ).
* Installieren Sie das Azure Data Box-Modul (Az.DataBox).
* Melden Sie sich bei Azure an.

#### <a name="install-azure-powershell-and-modules-locally"></a>Lokales Installieren von Azure PowerShell und Modulen

**Installation oder Upgrade von Windows PowerShell**

Sie müssen Windows PowerShell Version 6.2.4 oder höher installiert haben. Führen Sie Folgendes aus, um herauszufinden, welche Version von PowerShell Sie installiert haben: `$PSVersionTable`.

Die folgende Ausgabe wird angezeigt:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Wenn Ihre Version niedriger als 6.2.4 ist, müssen Sie ein Upgrade Ihrer Version von Windows PowerShell durchführen. Informationen zur Installation der neuesten Version von Windows PowerShell finden Sie unter [Installieren von Azure PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7&preserve-view=true).

**Installieren von Azure PowerShell- und Data Box-Modulen**

Sie müssen die Azure PowerShell-Module installieren, um mit Azure PowerShell eine Azure Data Box bestellen zu können. So installieren Sie die Azure PowerShell-Module

1. Installieren Sie das [Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).
2. Installieren Sie dann Az.DataBox mit dem Befehl `Install-Module -Name Az.DataBox`.

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Öffnen Sie ein Windows PowerShell-Befehlsfenster, und melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) bei Azure an:

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Hier ist die Ausgabe angegeben, die nach einer erfolgreichen Anmeldung angezeigt wird:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Ausführliche Informationen zum Anmelden bei Azure mit Windows PowerShell finden Sie unter [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Bestellen einer Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Führen Sie die folgenden Schritte im Azure-Portal aus, um ein Gerät zu bestellen.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen unter folgender URL an: [https://portal.azure.com](https://portal.azure.com).
2. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie nach *Azure Data Box*. Wählen Sie **Azure Data Box** aus.

   ![Screenshot: Abschnitt „Neu“ mit „Azure Data Box“ im Suchfeld](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Klicken Sie auf **Erstellen**.

   ![Screenshot: Abschnitt „Azure Data Box“ mit hervorgehobener Option „Erstellen“](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Überprüfen Sie, ob der Data Box-Dienst in Ihrer Region verfügbar ist. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie anschließend **Übernehmen** aus.

    |Einstellung  |Wert  |
    |---------|---------|
    |Übertragungstyp     | Wählen Sie **Import in Azure** aus.        |
    |Subscription     | Wählen Sie ein EA-, CSP- oder Azure Sponsorship-Abonnement für den Data Box-Dienst aus. <br> Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft.       |
    |Ressourcengruppe | Wählen Sie eine vorhandene Ressourcengruppe aus. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. |
    |Quellland/Quellregion    |    Wählen Sie das Land/die Region aus, in dem/der sich Ihre Daten zurzeit befinden.         |
    |Azure-Zielregion     |     Wählen Sie die Azure-Region aus, in die Daten übertragen werden sollen. <br> Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](data-box-overview.md#region-availability).            |

    [![Starten eines Importauftrags für Azure Data Box](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Wählen Sie **Data Box** aus. Die maximal nutzbare Kapazität für eine einzelne Bestellung beträgt 80 TB. Sie können mehrere Bestellungen für größere Datenmengen erstellen.

    ![Verfügbare Datengrößen: „Data Box Disk“ (40 TB), „Data Box“ (100 TB), „Data Box Heavy“ (1.000 TB) und „Eigene Datenträger einsenden“ (1 TB)](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. Wechseln Sie in **Bestellung** zur Registerkarte **Grundlagen**. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie anschließend Folgendes aus: **Weiter: Datenziel>** .

    |Einstellung  |Wert  |
    |---------|---------|
    |Subscription      | Das Abonnement wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch eingetragen.|
    |Resource group    | Die Ressourcengruppe, die Sie zuvor ausgewählt haben. |
    |Name des Importauftrags | Geben Sie einen Anzeigenamen an, um die Bestellung nachzuverfolgen. <br> Der Name kann zwischen 3 und 24 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. <br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.    |

    ![Assistent für Data Box-Importaufträge, Bildschirm „Grundlagen“ mit den richtigen Informationen](media/data-box-deploy-ordered/select-data-box-import-06.png)<!--Generic subscription. Cut note. Box command.-->

7. Wählen Sie auf dem Bildschirm **Datenziel** das **Datenziel** aus: entweder Speicherkonten oder verwaltete Datenträger.

    Wenn Sie als Speicherziel ein oder mehrere **Speicherkonten** verwenden, wird Folgendes angezeigt:

    ![Assistent für Data Box-Importaufträge, Bildschirm „Datenziel“ mit ausgewählten Speicherkonten](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Wählen Sie basierend auf der angegebenen Azure-Region mindestens ein Speicherkonto in der gefilterten Liste eines vorhandenen Speicherkontos aus. Data Box kann mit bis zu zehn Speicherkonten verknüpft werden. Sie können auch ein neues Speicherkonto vom Typ **Universell V1** oder **Universell V2** oder ein **Blob Storage-Konto** erstellen.

    Speicherkonten mit virtuellen Netzwerken werden unterstützt. Aktivieren Sie in den Netzwerkfirewalleinstellungen des Speicherkontos die vertrauenswürdigen Dienste, um dem Data Box-Dienst die Verwendung geschützter Speicherkonten zu ermöglichen. Weitere Informationen zum Hinzufügen von Azure Data Box als vertrauenswürdiger Dienst finden Sie [hier](../storage/common/storage-network-security.md#exceptions).

    Wenn Sie Data Box verwenden, um auf der Grundlage lokaler virtueller Festplatten (VHDs) **verwaltete Datenträger** zu erstellen, sind außerdem folgende Informationen erforderlich:

    |Einstellung  |Wert  |
    |---------|---------|
    |Ressourcengruppen     | Erstellen Sie neue Ressourcengruppen, wenn Sie verwaltete Datenträger auf der Grundlage lokaler VHDs erstellen möchten. Eine bereits vorhandene Ressourcengruppe kann nur verwendet werden, wenn diese zuvor beim Erstellen eines Data Box-Auftrags für verwaltete Datenträger durch den Data Box-Dienst erstellt wurde. <br> Bei Angabe mehrerer Ressourcengruppen müssen diese jeweils durch ein Semikolon getrennt werden. Es werden maximal zehn Ressourcengruppen unterstützt.|

    ![Assistent für Data Box-Importaufträge, Bildschirm „Datenziel“, auf dem „Verwaltete Datenträger“ ausgewählt ist](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    Das für verwaltete Datenträger angegebene Speicherkonto wird als Stagingspeicherkonto verwendet. Der Data Box-Dienst lädt die VHDs als Seitenblobs in das Stagingspeicherkonto hoch, konvertiert es in verwaltete Datenträger und verschiebt es in die Ressourcengruppen. Weitere Informationen finden Sie unter [Überprüfen des Datenuploads in Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Wird ein Seitenblob nicht in einen verwalteten Datenträger konvertiert, bleibt es im Speicherkonto, und Ihnen wird der Speicher in Rechnung gestellt.

8. Klicken Sie auf **Weiter: Sicherheit**, um den Vorgang fortzusetzen.

    Auf dem Bildschirm **Sicherheit** können Sie Ihren eigenen Verschlüsselungsschlüssel und Ihre eigenen Geräte- und Freigabekennwörter verwenden und die doppelte Verschlüsselung aktivieren.

    Alle Einstellungen auf dem Bildschirm **Sicherheit** sind optional. Wenn Sie keine der Einstellungen ändern, werden die Standardeinstellungen verwendet.

    ![Bildschirm „Sicherheit“ für den Assistenten für Data Box-Importaufträge](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Erweitern Sie die Option **Verschlüsselungstyp**, wenn Sie Ihren eigenen kundenseitig verwalteten Schlüssel nutzen möchten, um den Hauptschlüssel zum Entsperren Ihrer neuen Ressource zu schützen.

    Das Konfigurieren eines kundenseitig verwalteten Schlüssels für Azure Data Box ist optional. Standardmäßig verwendet Data Box einen von Microsoft verwalteten Schlüssel zum Schützen des Hauptschlüssels für die Entsperrung.

    Ein kundenseitig verwalteter Schlüssel wirkt sich nicht darauf aus, wie Daten auf dem Gerät verschlüsselt werden. Der Schlüssel wird nur verwendet, um den Hauptschlüssel zum Entsperren des Geräts zu verschlüsseln.

    Springen Sie zu Schritt 15, falls Sie keinen kundenseitig verwalteten Schlüssel verwenden möchten.

   ![Bildschirm „Sicherheit“ mit Einstellungen für den Verschlüsselungstyp](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Wählen Sie **Kundenseitig verwalteter Schlüssel** als Schlüsseltyp aus. Wählen Sie anschließend die Option **Schlüsseltresor und Schlüssel auswählen** aus.
   
    ![Bildschirm „Sicherheit“: Einstellungen für einen kundenseitig verwalteten Schlüssel](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. Auf dem Blatt **Schlüssel aus Azure Key Vault auswählen** wird das Abonnementfeld automatisch aufgefüllt.

    - Für **Schlüsseltresor** können Sie einen vorhandenen Schlüsseltresor aus der Dropdownliste auswählen.

      ![Auswählen des Schlüssels auf dem Azure Key Vault-Bildschirm](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - Sie können auch **Neu erstellen** auswählen, um einen neuen Schlüsseltresor zu erstellen. Geben Sie auf dem Bildschirm **Schlüsseltresor erstellen** die Ressourcengruppe und einen Schlüsseltresornamen ein. Stellen Sie sicher, dass die Optionen **Vorläufiges Löschen** und **Löschschutz** aktiviert sind. Übernehmen Sie für alle anderen Einstellungen die Standardwerte, und wählen Sie dann **Überprüfen und erstellen** aus.

      ![Erstellen von neuen Azure Key Vault-Einstellungen](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Überprüfen Sie die Informationen für Ihren Schlüsseltresor, und wählen Sie **Erstellen** aus. Warten Sie einige Minuten, bis die Erstellung des Schlüsseltresors abgeschlossen ist.

      ![Bildschirm mit Übersicht für neuen Azure-Schlüsseltresor](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. Unter **Schlüssel aus Azure Key Vault auswählen** können Sie einen Schlüssel auswählen, der im Schlüsseltresor vorhanden ist.

    ![Auswählen eines vorhandenen Schlüssels aus Azure-Schlüsseltresor](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Wählen Sie die Option **Neu erstellen** aus, wenn Sie einen neuen Schlüssel erstellen möchten. Sie müssen einen RSA-Schlüssel verwenden. Der Wert für die Größe kann „2.048“ oder höher lauten. Geben Sie einen Namen für Ihren neuen Schlüssel ein, behalten Sie ansonsten die Standardeinstellungen bei, und wählen Sie **Erstellen** aus.

      ![Option zum Erstellen eines neuen Schlüssels](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Sie werden benachrichtigt, wenn der Schlüssel in Ihrem Schlüsseltresor erstellt wurde.

13. Wählen Sie die **Version** des zu verwendenden Schlüssels und dann die Option **Auswählen** aus.

      ![Neu erstellter Schlüssel im Schlüsseltresor](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Wählen Sie die Option **Neu erstellen** aus, wenn Sie eine neue Schlüsselversion erstellen möchten.

    ![Öffnen eines Dialogfelds zum Erstellen einer neuen Schlüsselversion](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Wählen Sie Einstellungen für die neue Schlüsselversion und dann die Option **Erstellen** aus.

    ![Erstellen einer neuen Schlüsselversion](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    Auf dem Bildschirm **Sicherheit** werden in den Einstellungen unter **Verschlüsselungstyp** Ihr Schlüsseltresor und der Schlüssel angezeigt.

    ![Schlüssel und Schlüsseltresor für einen kundenseitig verwalteten Schlüssel](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Wählen Sie eine Benutzeridentität aus, die Sie zum Verwalten des Zugriffs auf diese Ressource verwenden. Wählen Sie die Option **Benutzeridentität auswählen** aus. Wählen Sie im Bereich auf der rechten Seite das zu verwendende Abonnement und die verwaltete Identität aus. Wählen Sie anschließend **Auswählen** aus.

    Eine vom Benutzer zugewiesene verwaltete Identität ist eine eigenständige Azure-Ressource, die zum Verwalten mehrerer Ressourcen verwendet werden kann. Weitere Informationen finden Sie unter [Arten von verwalteten Identitäten](/azure/active-directory/managed-identities-azure-resources/overview).  

    Befolgen Sie die Anleitung unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität über das Azure-Portal](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal), wenn Sie eine neue verwaltete Identität erstellen müssen.
    
    ![Auswählen einer Benutzeridentität](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    Die Benutzeridentität wird in den Einstellungen unter **Verschlüsselungstyp** angezeigt.

    ![Ausgewählte Benutzeridentität in den Einstellungen unter „Verschlüsselungstyp“](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Wenn Sie nicht die systemseitig generierten Kennwörter verwenden möchten, die standardmäßig von Azure Data Box genutzt werden, erweitern Sie auf dem Bildschirm **Sicherheit** den Bereich **Bring Your Own Password** (Eigenes Kennwort verwenden).

    Die systemseitig generierten Kennwörter sind sicher und werden empfohlen, sofern in Ihrer Organisation keine anderen Vorgaben gelten.

    ![Erweiterte Optionen zur Verwendung eines eigenen Kennworts für einen Data Box-Importauftrag](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Wenn Sie für Ihr neues Gerät ein eigenes Kennwort verwenden möchten, wählen Sie unter **Set preference for the device password** (Einstellung für das Gerätekennwort festlegen) die Option **Use your own password** (Eigenes Kennwort verwenden) aus, und geben Sie ein Kennwort ein, das die Sicherheitsanforderungen erfüllt.
   
     ![Optionen für die Verwendung eines eigenen Gerätekennworts auf dem Bildschirm „Sicherheit“ für einen Data Box-Importauftrag](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - So verwenden Sie Ihre eigenen Kennwörter für Freigaben:

   - Wählen Sie unter **Set preference for share passwords** (Einstellung für Freigabekennwörter festlegen) die Option **Use your own passwords** (Eigene Kennwörter verwenden) und anschließend **Select passwords for the shares** (Kennwörter für die Freigaben auswählen) aus.
     
        ![Optionen für die Verwendung eigener Freigabekennwörter auf dem Bildschirm „Sicherheit“ für einen Data Box-Importauftrag](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    - Geben Sie ein Kennwort für jedes im Auftrag enthaltene Speicherkonto ein. Das Kennwort wird für alle Freigaben für das Speicherkonto verwendet.
     
        Wenn Sie das gleiche Kennwort für alle Speicherkonten verwenden möchten, wählen Sie **In alle kopieren** aus. Wählen Sie abschließend **Speichern** aus.
     
        ![Eingabebildschirm für Freigabekennwörter für einen Data Box-Importauftrag](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

       Auf dem Bildschirm **Sicherheit** können Sie mithilfe von **View or change passwords** (Kennwörter anzeigen oder ändern) die Kennwörter ändern.

16. Wenn Sie die softwarebasierte doppelte Verschlüsselung aktivieren möchten, erweitern Sie auf dem Bildschirm **Sicherheit** den Bereich **Double-encryption (for highly secure environments)**  (Doppelte Verschlüsselung (für Hochsicherheitsumgebungen)), und wählen Sie die Option **Doppelte Verschlüsselung für den Auftrag aktivieren** aus.

    ![Bildschirm „Sicherheit“ für Data Box-Import: Aktivieren der softwarebasierten Verschlüsselung für einen Data Box-Auftrag](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    Die softwarebasierte Verschlüsselung wird zusätzlich zur AES-256-Bit-Verschlüsselung der Daten auf der Data Box ausgeführt.

    > [!NOTE]
    > Wenn Sie diese Option aktivieren, können die Auftragsverarbeitung und das Kopieren von Daten länger dauern. Nach dem Erstellen des Auftrags können Sie diese Option nicht mehr ändern.

    Klicken Sie auf **Weiter: Kontaktdetails**, um fortzufahren.

17. Wählen Sie unter **Kontaktdetails** die Option **+ Lieferadresse hinzufügen** aus.

    ![Hinzufügen von Lieferadressen zu Ihrem Azure Data Box-Importauftrag auf dem Bildschirm „Kontaktdetails“](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. Geben Sie unter **Lieferadresse** Ihren Vor- und Nachnamen, den Namen und die Postanschrift des Unternehmens sowie eine gültige Telefonnummer an. Wählen Sie **Adresse überprüfen** aus. Der Dienst überprüft anhand der Lieferadresse die Verfügbarkeit des Diensts. Wenn der Dienst für die angegebene Lieferadresse verfügbar ist, erhalten Sie eine entsprechende Benachrichtigung.

    ![Screenshot: Dialogfeld „Lieferadresse hinzufügen“ mit Hervorhebung der gewünschten Versandoption und der Option „Lieferadresse hinzufügen“](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Wenn Sie den selbstverwalteten Versand ausgewählt haben, erhalten Sie nach der erfolgreichen Auftragserteilung eine E-Mail-Benachrichtigung. Weitere Informationen zum selbstverwalteten Versand finden Sie unter [Verwenden des selbstverwalteten Versands](data-box-portal-customer-managed-shipping.md).

19. Wählen Sie **Lieferadresse hinzufügen** aus, wenn die Versanddetails erfolgreich überprüft wurden. Sie kehren zur Registerkarte **Kontaktdetails** zurück.

20. Fügen Sie nach der Rückkehr zu **Kontaktdetails** mindestens eine E-Mail-Adresse hinzu. Der Dienst sendet E-Mail-Benachrichtigungen in Bezug auf Aktualisierungen des Auftragsstatus an die angegebenen E-Mail-Adressen.

    Es wird empfohlen, eine E-Mail-Gruppenadresse zu verwenden, damit Sie weiterhin Benachrichtigungen erhalten, wenn ein Administrator die Gruppe verlässt.

    ![Abschnitt „E-Mail“ unter „Kontaktdetails“ im Bestell-Assistenten](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Überprüfen Sie die Informationen in **Überprüfen + bestellen** im Zusammenhang mit der Bestellung, den Kontakt, die Benachrichtigungen und die Datenschutzbestimmungen. Aktivieren Sie das Kontrollkästchen für die Zustimmung zu den Datenschutzbestimmungen.

22. Wählen Sie **Bestellen** aus. Die Erstellung des Auftrags dauert einige Minuten.

    ![Bildschirm „Überprüfen + bestellen“ des Bestell-Assistenten](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie für die Azure CLI die folgenden Schritte aus, um ein Gerät zu bestellen:

1. Notieren Sie sich Ihre Einstellungen für Ihre Data Box-Bestellung. Zu diesen Einstellungen gehören Ihre persönlichen/geschäftlichen Informationen, der Abonnementname, die Geräteinformationen und die Versandinformationen. Sie müssen diese Einstellungen als Parameter verwenden, wenn Sie den CLI-Befehl ausführen, um die Data Box-Bestellung zu erstellen. In der folgenden Tabelle sind die Parametereinstellungen aufgeführt, die für `az databox job create` verwendet werden:

   | Einstellung (Parameter) | BESCHREIBUNG |  Beispielwert |
   |---|---|---|
   |resource-group| Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
   |name| Der Name der von Ihnen erstellten Bestellung. | „mydataboxorder“|
   |contact-name| Der Name, der der Lieferadresse zugeordnet ist. | „Gus Poland“|
   |phone| Die Telefonnummer der Person oder des Unternehmens, die bzw. das die Bestellung erhält.| „14255551234“
   |location| Die nächstgelegene Azure-Region, von der aus Ihr Gerät versendet wird.| „US West“|
   |sku| Das spezifische Data Box-Gerät, das Sie bestellen. Gültige Werte sind: „DataBox“, „DataBoxDisk“ und „DataBoxHeavy“| „DataBox“ |
   |email-list| Die E-Mail-Adressen, die der Bestellung zugeordnet sind.| "gusp@contoso.com" |
   |street-address1| Die Straße, an die die Bestellung gesendet wird. | „15700 NE 39th St“ |
   |street-address2| Die zusätzlichen Adressinformationen, z. B. Apartment- oder Gebäudenummer. | „Bld 123“ |
   |city| Der Ort, an den das Gerät gesendet wird. | „Redmond“ |
   |state-or-province| Das Bundesland oder der Kanton, an das bzw. den das Gerät gesendet wird (in diesem Beispiel: Bundesstaat).| „WA“ |
   |country| Das Land/die Region, an das/die das Gerät gesendet wird. | „USA“ |
   |postal-code| Die Postleitzahl, die zur Lieferadresse gehört.| „98052“|
   |company-name| Der Name des Unternehmens, für das Sie arbeiten.| „Contoso, LTD“ |
   |Speicherkonto| Das Azure Storage-Konto, aus dem Sie Daten importieren möchten.| „mystorageaccount“|
   |debug| Einfügen von Debuginformationen in die ausführliche Protokollierung  | --debug |
   |help| Zeigt Hilfeinformationen für diesen Befehl an. | --help -h |
   |only-show-errors| Nur Fehler anzeigen und Warnungen unterdrücken. | --only-show-errors |
   |output -o| Legt das Ausgabeformat fest.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“. Der Standardwert ist „json“. | --output „json“ |
   |Abfrage| JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter [JMESPath](http://jmespath.org/). | --query <string>|
   |Ausführlich| Einbinden der ausführlichen Protokollierung. | --verbose |

2. Führen Sie an der Eingabeaufforderung oder im Terminal Ihrer Wahl [az databox job create](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create&preserve-view=true) aus, um Ihre Azure Data Box-Bestellung zu erstellen.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Hier ist ein Beispiel für die Verwendung des Befehls angegeben:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Für alle Azure CLI-Befehle wird standardmäßig JSON als Ausgabeformat genutzt, sofern Sie dies nicht ändern. Sie können das Ausgabeformat ändern, indem Sie den globalen Parameter `--output <output-format>` verwenden. Wenn Sie das Format in „table“ ändern, wird die Lesbarkeit der Ausgabe verbessert.

   Hier ist der gleiche Befehl wie eben mit einer kleinen Optimierung in Bezug auf die Formatierung angegeben:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Führen Sie für Azure PowerShell die folgenden Schritte aus, um ein Gerät zu bestellen:

1. Bevor Sie den Importauftrag erstellen, müssen Sie Ihr Speicherkonto abrufen und das Speicherkontoobjekt in einer Variablen speichern.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Notieren Sie sich Ihre Einstellungen für Ihre Data Box-Bestellung. Zu diesen Einstellungen gehören Ihre persönlichen/geschäftlichen Informationen, der Abonnementname, die Geräteinformationen und die Versandinformationen. Sie müssen diese Einstellungen als Parameter verwenden, wenn Sie den PowerShell-Befehl ausführen, um die Data Box-Bestellung zu erstellen. In der folgenden Tabelle sind die Parametereinstellungen aufgeführt, die für [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) verwendet werden.

    | Einstellung (Parameter) | BESCHREIBUNG |  Beispielwert |
    |---|---|---|
    |ResourceGroupName [Erforderlich]| Eine vorhandene Ressourcengruppe verwenden. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
    |Name [Erforderlich]| Der Name der von Ihnen erstellten Bestellung. | „mydataboxorder“|
    |ContactName [Erforderlich]| Der Name, der der Lieferadresse zugeordnet ist. | „Gus Poland“|
    |PhoneNumber [Erforderlich]| Die Telefonnummer der Person oder des Unternehmens, die bzw. das die Bestellung erhält.| „14255551234“
    |Location [Erforderlich]| Die nächstgelegene Azure-Region, von der aus Ihr Gerät versendet wird.| „WestUS“|
    |DataBoxType [Erforderlich]| Das spezifische Data Box-Gerät, das Sie bestellen. Gültige Werte sind: „DataBox“, „DataBoxDisk“ und „DataBoxHeavy“| „DataBox“ |
    |EmailId [Erforderlich]| Die E-Mail-Adressen, die der Bestellung zugeordnet sind.| "gusp@contoso.com" |
    |StreetAddress1 [Erforderlich]| Die Straße, an die die Bestellung gesendet wird. | „15700 NE 39th St“ |
    |StreetAddress2| Die zusätzlichen Adressinformationen, z. B. Apartment- oder Gebäudenummer. | „Bld 123“ |
    |StreetAddress3| Die tertiären Adressinformationen. | |
    |City [Erforderlich]| Der Ort, an den das Gerät gesendet wird. | „Redmond“ |
    |StateOrProvinceCode [Erforderlich]| Das Bundesland oder der Kanton, an das bzw. den das Gerät gesendet wird (in diesem Beispiel: Bundesstaat).| „WA“ |
    |CountryCode [Erforderlich]| Das Land/die Region, an das/die das Gerät gesendet wird. | „USA“ |
    |PostalCode [Erforderlich]| Die Postleitzahl, die zur Lieferadresse gehört.| „98052“|
    |CompanyName| Der Name des Unternehmens, für das Sie arbeiten.| „Contoso, LTD“ |
    |StorageAccountResourceId [Erforderlich]| Die ID für das Azure Storage-Konto, aus dem Sie Daten importieren möchten.| <AzStorageAccount>.id |

3. Verwenden Sie in der Eingabeaufforderung oder im Terminal Ihrer Wahl den Befehl [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob), um Ihre Azure Data Box-Bestellung zu erstellen.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>Nachverfolgen der Bestellung

# <a name="portal"></a>[Portal](#tab/portal)

Nachdem Sie die Bestellung aufgegeben haben, können Sie ihren Status im Azure-Portal nachverfolgen. Navigieren Sie zu Ihrer Data Box-Bestellung, und navigieren Sie dann zu **Übersicht**, um den Status anzuzeigen. Die Bestellung wird im Portal mit dem Status **Bestellt** angezeigt.

Sollte das Gerät nicht verfügbar sein, erhalten Sie eine entsprechende Benachrichtigung. Wenn das Gerät verfügbar ist, identifiziert Microsoft das Gerät für den Versand und bereitet den Versand vor. Während der Vorbereitung des Geräts werden folgende Aktionen ausgeführt:

* Für jedes mit dem Gerät verknüpfte Speicherkonto werden SMB-Freigaben erstellt.
* Für jede Freigabe werden Anmeldeinformationen wie Benutzername und Kennwort generiert.
* Ein Gerätekennwort zum Entsperren des Geräts wird ebenfalls generiert.
* Die Data Box wird gesperrt, um nicht autorisierten Zugriff auf das Gerät zu verhindern.

Wenn die Vorbereitung des Geräts abgeschlossen ist, wird die Bestellung im Portal mit dem Status **Verarbeitet** angezeigt.

![Eine verarbeitete Data Box-Bestellung](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereitet dann Ihr Gerät vor und versendet es über einen regionalen Kurierdienst. Sobald das Gerät versandt wurde, erhalten Sie eine Nachverfolgungsnummer. Das Portal zeigt die Bestellung im Zustand **Versandt** an.

![Eine versendete Data Box-Bestellung](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="track-a-single-order"></a>Nachverfolgen einer einzelnen Bestellung

Führen Sie [az databox job show](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show&preserve-view=true) aus, um Nachverfolgungsinformationen zu einer einzelnen vorhandenen Azure Data Box-Bestellung zu erhalten. Mit dem Befehl werden Informationen zur Bestellung angezeigt, z. B. Name, Ressourcengruppe, Nachverfolgungsinformationen, Abonnement-ID, Kontaktinformationen, Versandtyp und Geräte-SKU.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   In der folgenden Tabelle sind die Parameterinformationen für `az databox job show` angegeben:

   | Parameter | BESCHREIBUNG |  Beispielwert |
   |---|---|---|
   |resource-group [erforderlich]| Der Name der Ressourcengruppe, die der Bestellung zugeordnet ist. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
   |name [erforderlich]| Der Name der anzuzeigenden Bestellung. | „mydataboxorder“|
   |debug| Einfügen von Debuginformationen in die ausführliche Protokollierung | --debug |
   |help| Zeigt Hilfeinformationen für diesen Befehl an. | --help -h |
   |only-show-errors| Nur Fehler anzeigen und Warnungen unterdrücken. | --only-show-errors |
   |output -o| Legt das Ausgabeformat fest.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“. Der Standardwert ist „json“. | --output „json“ |
   |Abfrage| JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter [JMESPath](http://jmespath.org/). | --query <string>|
   |Ausführlich| Einbinden der ausführlichen Protokollierung. | --verbose |

   Hier ist ein Beispiel für den Befehl mit Festlegung des Ausgabeformats auf „table“ angegeben:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Die Auflistung der Bestellungen kann auf Abonnementebene unterstützt werden, und dies macht die Ressourcengruppe zu einem optionalen Parameter (anstatt zu einem erforderlichen Parameter).

### <a name="list-all-orders"></a>Auflisten aller Bestellungen

Wenn Sie mehrere Geräte bestellt haben, können Sie [az databox job list](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list&preserve-view=true) ausführen, um Ihre gesamten Azure Data Box-Bestellungen anzuzeigen. Mit dem Befehl werden alle Bestellungen aufgelistet, die zu einer bestimmten Ressourcengruppe gehören. Weitere angezeigte Elemente in der Ausgabe: Name der Bestellung, Versandstatus, Azure-Region, Übermittlungstyp, Bestellstatus. Stornierte Bestellungen sind ebenfalls in der Liste enthalten.
Mit dem Befehl werden auch die Zeitstempel der einzelnen Bestellungen angezeigt.

```azurecli
az databox job list --resource-group <resource-group>
```

In der folgenden Tabelle sind die Parameterinformationen für `az databox job list` angegeben:

   | Parameter | BESCHREIBUNG |  Beispielwert |
   |---|---|---|
   |resource-group [erforderlich]| Der Name der Ressourcengruppe, die die Bestellungen enthält. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
   |debug| Einfügen von Debuginformationen in die ausführliche Protokollierung | --debug |
   |help| Zeigt Hilfeinformationen für diesen Befehl an. | --help -h |
   |only-show-errors| Nur Fehler anzeigen und Warnungen unterdrücken. | --only-show-errors |
   |output -o| Legt das Ausgabeformat fest.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“. Der Standardwert ist „json“. | --output „json“ |
   |Abfrage| JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter [JMESPath](http://jmespath.org/). | --query <string>|
   |Ausführlich| Einbinden der ausführlichen Protokollierung. | --verbose |

   Hier ist ein Beispiel für den Befehl mit Festlegung des Ausgabeformats auf „table“ angegeben:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Nachverfolgen einer einzelnen Bestellung

Führen Sie [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob) aus, um Nachverfolgungsinformationen zu einer einzelnen vorhandenen Azure Data Box-Bestellung zu erhalten. Mit dem Befehl werden Informationen zur Bestellung angezeigt, z. B. Name, Ressourcengruppe, Nachverfolgungsinformationen, Abonnement-ID, Kontaktinformationen, Versandtyp und Geräte-SKU.

> [!NOTE]
> `Get-AzDataBoxJob` wird zum Anzeigen von einzelnen und mehreren Bestellungen verwendet. Der Unterschied besteht darin, dass Sie bei Einzelbestellungen den Bestellungsnamen angeben.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   In der folgenden Tabelle sind die Parameterinformationen für `Get-AzDataBoxJob` angegeben:

   | Parameter | BESCHREIBUNG |  Beispielwert |
   |---|---|---|
   |ResourceGroup [Erforderlich]| Der Name der Ressourcengruppe, die der Bestellung zugeordnet ist. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
   |Name [Erforderlich]| Der Name der Bestellung, für die Informationen abgerufen werden sollen. | „mydataboxorder“|
   |resourceId| Die ID der Ressource, die der Bestellung zugeordnet ist. |  |

   Hier ist ein Beispiel für den Befehl mit der Ausgabe angegeben:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Auflisten aller Bestellungen

Wenn Sie mehrere Geräte bestellt haben, können Sie [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob) ausführen, um Ihre gesamten Azure Data Box-Bestellungen anzuzeigen. Mit dem Befehl werden alle Bestellungen aufgelistet, die zu einer bestimmten Ressourcengruppe gehören. Weitere angezeigte Elemente in der Ausgabe: Name der Bestellung, Versandstatus, Azure-Region, Übermittlungstyp, Bestellstatus. Stornierte Bestellungen sind ebenfalls in der Liste enthalten.
Mit dem Befehl werden auch die Zeitstempel der einzelnen Bestellungen angezeigt.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Hier ist ein Beispiel für den Befehl angegeben:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>Stornieren der Bestellung

# <a name="portal"></a>[Portal](#tab/portal)

Um die Bestellung zu stornieren, navigieren Sie im Azure-Portal zu **Übersicht** und wählen dann auf der Befehlsleiste die Option **Stornieren** aus.

Solange eine Bestellung noch nicht mit dem Status „Verarbeitet“ angezeigt wird, können Sie sie jederzeit stornieren.

Um eine stornierte Bestellung zu löschen, navigieren Sie zu **Übersicht** und wählen dann auf der Befehlsleiste die Option **Löschen** aus.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="cancel-an-order"></a>Abbrechen eines Auftrags

Führen Sie zum Stornieren einer Azure Data Box-Bestellung den Befehl [az databox job cancel](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel&preserve-view=true) aus. Sie müssen den Grund für die Stornierung der Bestellung angeben.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   In der folgenden Tabelle sind die Parameterinformationen für `az databox job cancel` angegeben:

   | Parameter | BESCHREIBUNG |  Beispielwert |
   |---|---|---|
   |resource-group [erforderlich]| Der Name der Ressourcengruppe, die der zu löschenden Bestellung zugeordnet ist. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
   |name [erforderlich]| Der Name der zu löschenden Bestellung. | „mydataboxorder“|
   |reason [erforderlich]| Der Grund für die Stornierung der Bestellung. | „Ich habe falsche Informationen eingegeben und musste die Bestellung daher stornieren.“ |
   |ja| Nicht zur Bestätigung auffordern | --yes (-y)| --yes -y |
   |debug| Einfügen von Debuginformationen in die ausführliche Protokollierung | --debug |
   |help| Zeigt Hilfeinformationen für diesen Befehl an. | --help -h |
   |only-show-errors| Nur Fehler anzeigen und Warnungen unterdrücken. | --only-show-errors |
   |output -o| Legt das Ausgabeformat fest.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“. Der Standardwert ist „json“. | --output „json“ |
   |Abfrage| JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter [JMESPath](http://jmespath.org/). | --query <string>|
   |Ausführlich| Einbinden der ausführlichen Protokollierung. | --verbose |

   Hier ist ein Beispiel für den Befehl mit der Ausgabe angegeben:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Löschen einer Bestellung

Wenn Sie eine Azure Data Box-Bestellung storniert haben, können Sie [az databox job delete](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete&preserve-view=true) ausführen, um die Bestellung zu löschen.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   In der folgenden Tabelle sind die Parameterinformationen für `az databox job delete` angegeben:

   | Parameter | BESCHREIBUNG |  Beispielwert |
   |---|---|---|
   |resource-group [erforderlich]| Der Name der Ressourcengruppe, die der zu löschenden Bestellung zugeordnet ist. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
   |name [erforderlich]| Der Name der zu löschenden Bestellung. | „mydataboxorder“|
   |Abonnement| Der Name oder die ID (GUID) Ihres Azure-Abonnements. | „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ |
   |ja| Nicht zur Bestätigung auffordern | --yes (-y)| --yes -y |
   |debug| Einfügen von Debuginformationen in die ausführliche Protokollierung | --debug |
   |help| Zeigt Hilfeinformationen für diesen Befehl an. | --help -h |
   |only-show-errors| Nur Fehler anzeigen und Warnungen unterdrücken. | --only-show-errors |
   |output -o| Legt das Ausgabeformat fest.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“. Der Standardwert ist „json“. | --output „json“ |
   |Abfrage| JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter [JMESPath](http://jmespath.org/). | --query <string>|
   |Ausführlich| Einbinden der ausführlichen Protokollierung. | --verbose |

Hier ist ein Beispiel für den Befehl mit der Ausgabe angegeben:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Abbrechen eines Auftrags

Um eine Azure Data Box-Bestellung abzubrechen, führen Sie [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob) aus. Sie müssen den Grund für die Stornierung der Bestellung angeben.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

In der folgenden Tabelle sind die Parameterinformationen für `Stop-AzDataBoxJob` angegeben:

| Parameter | BESCHREIBUNG |  Beispielwert |
|---|---|---|
|ResourceGroup [Erforderlich]| Der Name der Ressourcengruppe, die der abzubrechenden Bestellung zugeordnet ist. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
|Name [Erforderlich]| Der Name der zu löschenden Bestellung. | „mydataboxorder“|
|Reason [Erforderlich]| Der Grund für die Stornierung der Bestellung. | „Ich habe falsche Informationen eingegeben und musste die Bestellung daher stornieren.“ |
|Force | Erzwingt das Ausführen des Cmdlets ohne Benutzerbestätigung. | -Force |

Hier ist ein Beispiel für den Befehl mit der Ausgabe angegeben:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Löschen einer Bestellung

Wenn Sie eine Azure Data Box-Bestellung abgebrochen haben, können Sie [Remove-AzDataBoxJob](/powershell/module/az.databox/remove-azdataboxjob) ausführen, um die Bestellung zu löschen.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

In der folgenden Tabelle sind die Parameterinformationen für `Remove-AzDataBoxJob` angegeben:

| Parameter | BESCHREIBUNG |  Beispielwert |
|---|---|---|
|ResourceGroup [Erforderlich]| Der Name der Ressourcengruppe, die der zu löschenden Bestellung zugeordnet ist. Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. | „myresourcegroup“|
|Name [Erforderlich]| Der Name der zu löschenden Bestellung. | „mydataboxorder“|
|Force | Erzwingt das Ausführen des Cmdlets ohne Benutzerbestätigung. | -Force |

Hier ist ein Beispiel für den Befehl mit der Ausgabe angegeben:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Hier ist die Ausgabe nach der Ausführung des Befehls angegeben:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Artikeln erhalten, z. B.:

> [!div class="checklist"]
>
> * Voraussetzungen für die Bereitstellung von Data Box
> * Bestellen einer Data Box
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Data Box einrichten.

> [!div class="nextstepaction"]
> [Einrichten der Azure Data Box](./data-box-deploy-set-up.md)
