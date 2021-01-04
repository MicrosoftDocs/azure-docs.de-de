---
title: Importieren und Exportieren von Daten in Azure Cache for Redis
description: Enthält Informationen zum Importieren und Exportieren von Daten in und aus dem Blob-Speicher mit Azure Cache for Redis-Instanzen des Premium-Tarifs.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327507"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importieren und Exportieren von Daten in Azure Cache for Redis
Import/Export ist ein Vorgang der Azure Cache for Redis-Datenverwaltung, bei dem Sie Daten in Azure Cache for Redis importieren oder aus Azure Cache for Redis exportieren können. Hierzu importieren bzw. exportieren Sie eine Momentaufnahme der Azure Cache for Redis-Datenbank (RDB) aus einem Premium-Cache in ein Blob in einem Azure Storage-Konto.

- **Exportieren:** Sie können Ihre Azure Cache for Redis RDB-Momentaufnahmen in ein Seitenblob exportieren.
- **Importieren:** Sie können die Azure Cache for Redis RDB-Momentaufnahmen aus einem Seitenblob oder einem Blockblob importieren.

So können Sie zwischen verschiedenen Azure Cache for Redis-Instanzen migrieren oder den Cache vor der Nutzung mit Daten auffüllen.

Dieser Artikel enthält eine Anleitung zum Importieren und Exportieren von Daten mit Azure Cache for Redis und Antworten auf häufig gestellte Fragen.

> [!IMPORTANT]
> Import/Export ist nur für Caches im [Premium-Tarif](cache-overview.md#service-tiers) verfügbar.
>
>

## <a name="import"></a>Importieren
Die Importfunktion kann verwendet werden, um Redis-kompatible RDB-Dateien von beliebigen Redis-Servern zu importieren, die in einer Cloud oder Umgebung ausgeführt werden, z.B. Redis unter Linux oder Windows oder bei einem Cloudanbieter wie Amazon Web Services und anderen. Das Importieren von Daten ist eine einfache Möglichkeit zum Erstellen eines Cache mit vorab aufgefüllten Daten. Während des Importvorgangs lädt Azure Cache for Redis die RDB-Dateien aus Azure Storage in den Arbeitsspeicher und fügt die Schlüssel anschließend in den Cache ein.

> [!NOTE]
> Stellen Sie vor Beginn des Importvorgangs sicher, dass die Redis-Datenbankdateien (RDB) in Seiten- oder Blockblobs in einen Azure-Speicher hochgeladen wurden, der sich in derselben Region und im gleichen Abonnement wie Ihre Azure Cache for Redis-Instanz befindet. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Wenn Sie die RDB-Datei mit der [Azure Cache for Redis-Exportfunktion](#export) exportiert haben, ist Ihre RDB-Datei bereits in einem Seitenblob gespeichert und für den Import bereit.
>
>

1. Um ein oder mehrere exportierte Cacheblobs zu importieren, [navigieren Sie im Azure-Portal zum Cache](cache-configure.md#configure-azure-cache-for-redis-settings), und klicken Sie im **Ressourcenmenü** auf **Daten importieren**.

    ![Daten importieren](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Klicken Sie auf **Blob(s) auswählen** , und wählen Sie das Speicherkonto aus, das die zu importierenden Daten enthält.

    ![Speicherkonto auswählen](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Klicken Sie auf den Container, der die zu importierenden Daten enthält.

    ![Container auswählen](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Wählen Sie ein oder mehrere Blobs für den Import aus, indem Sie jeweils auf den Bereich links vom Blobnamen und dann auf **Auswählen** klicken.

    ![Blobs auswählen](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Klicken Sie auf **Importieren** , um den Importvorgang zu starten.

   > [!IMPORTANT]
   > Während des Importvorgangs können Cacheclients nicht auf den Cache zugreifen, und alle im Cache enthaltenen Daten werden gelöscht.
   >
   >

    ![Importieren](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Sie können den Status des Importvorgangs überwachen, indem Sie die Benachrichtigungen im Azure-Portal verfolgen oder die Ereignisse im [Überwachungsprotokoll](../azure-resource-manager/management/view-activity-logs.md) anzeigen.

    ![Importstatus](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportieren
Mit der Exportfunktion können Sie die in Azure Cache for Redis gespeicherten Daten als Redis-kompatible RDB-Dateien exportieren. Sie können diese Funktion nutzen, um Daten von einer Azure Cache for Redis-Instanz zur anderen oder auf einen anderen Redis-Server zu verschieben. Während des Exportvorgangs wird auf dem virtuellen Computer, der die Azure Cache for Redis-Serverinstanz hostet, eine temporäre Datei erstellt, die dann in das angegebene Speicherkonto hochgeladen wird. Nachdem der Exportvorgang mit dem Status „Erfolg“ oder „Fehler“ abgeschlossen wurde, wird die temporäre Datei gelöscht.

1. Um den aktuellen Inhalt des Caches in den Speicher zu exportieren, [navigieren Sie im Azure-Portal zum Cache](cache-configure.md#configure-azure-cache-for-redis-settings), und klicken Sie im **Ressourcenmenü** auf **Daten exportieren**.

    ![Im Navigationsbereich für contoso5premium ist die Option „Daten exportieren“ in der Liste „Verwaltung“ hervorgehoben.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Klicken Sie auf **Speichercontainer auswählen** , und wählen Sie das gewünschte Speicherkonto aus. Das Speicherkonto muss sich in demselben Abonnement und derselben Region wie der Cache befinden.

   > [!IMPORTANT]
   > Das Exportieren funktioniert in Verbindung mit Seitenblobs. Diese werden in klassischen Speicherkonten sowie in Resource Manager-Speicherkonten, aber derzeit noch nicht in Blob Storage-Konten unterstützt. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../storage/common/storage-account-overview.md).
   >

    ![Speicherkonto](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Markieren Sie den gewünschten Blobcontainer, und klicken Sie auf **Auswählen**. Klicken Sie zum Verwenden eines neuen Containers auf **Container hinzufügen** , damit Sie diesen zuerst hinzufügen und dann in der Liste auswählen können.

    ![Bei Containern für contoso55 ist die Option „+ Container“ hervorgehoben. Es gibt einen Container in der Liste(cachesaves), und er ist ausgewählt und hervorgehoben. Die Option „Auswahl“ ist ausgewählt und hervorgehoben.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Geben Sie ein **Blobnamenspräfix** ein, und klicken Sie auf **Exportieren**, um den Exportvorgang zu starten. Das Blobnamenpräfix wird als Präfix für die Namen der Dateien verwendet, die während des Exportvorgangs generiert werden.

    ![Exportieren](./media/cache-how-to-import-export-data/cache-export-data.png)

    Sie können den Status des Exportvorgangs überwachen, indem Sie die Benachrichtigungen im Azure-Portal verfolgen oder die Ereignisse im [Überwachungsprotokoll](../azure-resource-manager/management/view-activity-logs.md) anzeigen.

    ![Export der Daten abgeschlossen](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Caches können während des Exportvorgangs weiterhin genutzt werden.

## <a name="importexport-faq"></a>Import/Export – Häufig gestellte Fragen
Dieser Abschnitt enthält häufig gestellte Fragen zum Import/Export-Feature.

* [Bei welchen Tarifen kann Import/Export genutzt werden?](#what-pricing-tiers-can-use-importexport)
* [Kann ich Daten von beliebigen Redis-Servern importieren?](#can-i-import-data-from-any-redis-server)
* [Welche RDB-Versionen kann ich importieren?](#what-rdb-versions-can-i-import)
* [Ist der Cache während eines Import/Export-Vorgangs verfügbar?](#is-my-cache-available-during-an-importexport-operation)
* [Kann ich Import/Export mit dem Redis-Cluster nutzen?](#can-i-use-importexport-with-redis-cluster)
* [Wie funktioniert Import/Export bei einer benutzerdefinierten Einstellung für Datenbanken?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Wie unterscheidet sich Import/Export von der Redis-Persistenz?](#how-is-importexport-different-from-redis-persistence)
* [Kann ich Import/Export mit PowerShell, per CLI oder mit anderen Verwaltungsclients automatisieren?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Ich habe während des Import/Export-Vorgangs einen Zeitüberschreitungsfehler erhalten. Was bedeutet das?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Ich habe beim Exportieren der Daten in Azure Blob Storage einen Fehler erhalten. Was ist passiert?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Bei welchen Tarifen kann Import/Export genutzt werden?
Import/Export ist nur für den Premium-Tarif verfügbar.

### <a name="can-i-import-data-from-any-redis-server"></a>Kann ich Daten von beliebigen Redis-Servern importieren?
Ja. Zusätzlich zum Importieren von Daten, die aus Azure Cache for Redis-Instanzen exportiert wurden, können Sie RDB-Dateien von Redis-Servern in beliebigen Clouds oder Umgebungen importieren, z.B. Linux, Windows oder von Cloudanbietern wie Amazon Web Services. Laden Sie hierzu die RDB-Datei vom gewünschten Redis-Server in ein Seiten- oder Blockblob eines Azure Storage-Kontos hoch, und importieren Sie sie dann in Ihre Premium-Instanz von Azure Cache for Redis. Es kann beispielsweise sein, dass Sie die Daten aus dem Produktionscache exportieren und in einen Cache importieren möchten, der als Teil der Stagingumgebung zum Testen oder für die Migration verwendet wird.

> [!IMPORTANT]
> Um die exportierten Daten von Redis-Servern, die nicht Azure Cache for Redis verwenden, mithilfe eines Seitenblobs zu importieren, muss die Größe des Seitenblobs an einen 512-Byte-Grenzwert angepasst werden. Beispielcode für die erforderliche Byteauffüllung finden Sie unter [Beispiel für das Hochladen in ein Seitenblob](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Welche RDB-Versionen kann ich importieren?

Azure Cache for Redis unterstützt RDB-Import bis RDB-Version 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Ist der Cache während eines Import/Export-Vorgangs verfügbar?
* **Export:** Caches bleiben verfügbar, und Sie können Sie den Cache während eines Exportvorgangs weiter nutzen.
* **Import:** Caches sind ab dem Start eines Importvorgangs nicht mehr verfügbar und können wieder genutzt werden, nachdem der Importvorgang abgeschlossen ist.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Kann ich Import/Export mit dem Redis-Cluster nutzen?
Ja. Sie können einen Import bzw. Export auch zwischen einem gruppierten Cache und einem nicht gruppierten Cache durchführen. Da der Redis-Cluster [nur Datenbank 0 unterstützt](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), werden Daten ausschließlich in die Datenbank 0 importiert. Wenn gruppierte Cachedaten importiert werden, werden die Schlüssel auf die Shards des Clusters verteilt.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Wie funktioniert Import/Export bei einer benutzerdefinierten Einstellung für Datenbanken?
Bei einigen Tarifen gibt es unterschiedliche [Grenzwerte für Datenbanken](cache-configure.md#databases). Wenn Sie bei der Cacheerstellung einen benutzerdefinierten Wert für die `databases`-Einstellung konfiguriert haben, müssen Sie beim Importieren einige Punkte beachten.

* Beim Importieren in einen Tarif mit einem niedrigeren `databases` -Grenzwert als bei dem Tarif, aus dem Sie exportieren:
  * Wenn Sie die Standardanzahl für `databases` verwenden (sie beträgt bei allen Tarifen 16), gehen keine Daten verloren.
  * Wenn Sie eine benutzerdefinierte Anzahl für `databases` verwenden, die innerhalb der Grenzwerte für den Tarif liegt, in den Sie importieren, gehen keine Daten verloren.
  * Wenn Ihre exportierten Daten Daten in einer Datenbank enthalten, die die Grenzwerte des neuen Tarifs überschreitet, werden die Daten aus diesen höheren Datenbanken nicht importiert.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Wie unterscheidet sich Import/Export von der Redis-Persistenz?
Mit der Azure Cache for Redis-Persistenz können Sie unter Redis gespeicherte Daten dauerhaft in Azure Storage speichern. Wenn Persistenz konfiguriert ist, speichert Azure Cache for Redis basierend auf einer wählbaren Sicherungshäufigkeit eine Momentaufnahme des Azure Cache for Redis in einem binären Redis-Format dauerhaft auf dem Datenträger. Bei einem schwerwiegenden Fehler, bei dem sowohl der primäre Cache als auch der Replikatcache deaktiviert werden, werden die Cachedaten automatisch wiederhergestellt, indem die aktuellste Momentaufnahme verwendet wird. Weitere Informationen finden Sie unter [Konfigurieren von Datenpersistenz für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-persistence.md).

Mit Import/Export können Sie Daten in Azure Cache for Redis importieren oder daraus exportieren. Die Funktion dient nicht zum Konfigurieren der Sicherung oder für die Wiederherstellen per Redis-Persistenz.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kann ich Import/Export mit PowerShell, per CLI oder mit anderen Verwaltungsclients automatisieren?
Ja. Anweisungen für PowerShell finden Sie unter [Importieren eines Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) und [Exportieren eines Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Ich habe während des Import/Export-Vorgangs einen Zeitüberschreitungsfehler erhalten. Was bedeutet das?
Wenn Sie sich vor dem Initiieren des Vorgangs länger als 15 Minuten auf dem Blatt **Daten importieren** bzw. **Daten exportieren** aufhalten, wird eine Fehlermeldung angezeigt, die dem folgenden Beispiel ähnelt:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Initiieren Sie den Import- oder Exportvorgang, bevor die 15 Minuten abgelaufen sind.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Ich habe beim Exportieren der Daten in Azure Blob Storage einen Fehler erhalten. Was ist passiert?
Das Exportieren funktioniert nur mit RDB-Dateien, die als Seitenblobs gespeichert sind. Andere Blobtypen, einschließlich Blobspeicherkonten mit heißen und kalten Ebenen, werden derzeit nicht unterstützt. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Azure Cache for Redis-Features.

* [Azure Cache for Redis-Dienstebenen](cache-overview.md#service-tiers)