---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Pflichtfelder. Weitere Informationen finden Sie unter aka.ms/skyeye/meta.
Titel: Azure-Speicherkonten: Azure Media Services description: Erfahren Sie, wie Sie ein Azure-Speicherkonto für die Verwendung mit Azure Media Services erstellen.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 01/29/2021 ms.author: inhenkel
---

# <a name="azure-storage-accounts"></a>Azure Storage-Konten

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen. Wenn Sie ein Media Services-Konto erstellen, müssen Sie den Namen einer Azure Storage-Kontoressource angeben. Das angegebene Speicherkonto wird an Ihr Media Services-Konto angefügt.

Das Media Services-Konto und alle zugeordneten Speicherkonten müssen im gleichen Azure-Abonnement enthalten sein. Zur Vermeidung zusätzlicher Wartezeit und Kosten für ausgehenden Datenverkehr wird dringend empfohlen, Speicherkonten zu verwenden, die sich an demselben Ort wie das Media Services-Konto befinden.

Sie müssen über ein **primäres** Speicherkonto verfügen. Darüber hinaus können Sie beliebig viele **sekundäre** Speicherkonten an Ihr Media Services-Konto anfügen. Media Services unterstützt Konten des Typs **Allgemein v2** (GPv2) oder **Allgemein v1** (GPv1). Reine Blobkonten sind als **primäre** Konten nicht zulässig.

Wir empfehlen Ihnen, GPv2 zu verwenden, damit Sie in den Genuss der aktuellen Features und der Leistung kommen. Weitere Informationen zu Speicherkonten finden Sie in der [Übersicht über Azure Storage-Konten](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Nur die heiße Zugriffsebene wird für die Verwendung mit Azure Media Services unterstützt. Die anderen Zugriffsebenen können aber verwendet werden, um Speicherkosten für Inhalt zu reduzieren, der nicht aktiv genutzt wird.

Es gibt verschiedene SKUs, die Sie für Ihr Speicherkonto auswählen können. Falls Sie mit Speicherkonten experimentieren möchten, verwenden Sie `--sku Standard_LRS`. Wenn Sie eine SKU für die Produktion auswählen, sollten Sie jedoch die Verwendung von `--sku Standard_RAGRS` erwägen, da diese Option geografische Replikation zum Gewährleisten der Geschäftskontinuität bietet.

## <a name="assets-in-a-storage-account"></a>Medienobjekte in einem Speicherkonto

In Media Services v3 verwenden Sie Azure Storage-APIs zum Hochladen von Dateien in Medienobjekte. Weitere Informationen finden Sie unter [Medienobjekte in Azure Media Services v3](assets-concept.md).

> [!Note]
> Versuchen Sie nicht, den Inhalt von Blobcontainern, die mit dem Media Services SDK generiert wurden, ohne Media Service-APIs zu ändern.

## <a name="storage-side-encryption"></a>Speicherseitige Verschlüsselung

Zum Schutz Ihrer im Ruhezustand befindlichen Medienobjekte sollten die Medienobjekte durch die speicherseitige Verschlüsselung verschlüsselt werden. Die folgende Tabelle zeigt, wie die speicherseitige Verschlüsselung in Media Services v3 funktioniert:

|Verschlüsselungsoption|BESCHREIBUNG|Media Services v3|
|---|---|---|
|Media Services-Speicherverschlüsselung| AES-256-Verschlüsselung, Schlüssel von Media Services verwaltet. |Nicht unterstützt.<sup>1</sup>|
|[Speicherdienstverschlüsselung für ruhende Daten](../../storage/common/storage-service-encryption.md)|Durch Azure Storage angebotene serverseitige Verschlüsselung – Schlüssel wird von Azure oder vom Kunden verwaltet.|Unterstützt.|
|[Clientseitige Speicherverschlüsselung](../../storage/common/storage-client-side-encryption.md)|Durch Azure Storage angebotene clientseitige Verschlüsselung – Schlüssel wird vom Kunden in Key Vault verwaltet.|Wird nicht unterstützt.|

<sup>1</sup> In Media Services v3 wird Speicherverschlüsselung (AES-256-Verschlüsselung) nur für die Abwärtskompatibilität unterstützt, wenn Ihre Medienobjekte mit Media Services v2 erstellt wurden. Dies bedeutet, dass v3 mit vorhandenen speicherverschlüsselten Medienobjekten funktioniert, jedoch keine Erstellung von neuen zulässt.

## <a name="storage-account-double-encryption"></a>Speicherkonto: Mehrfachverschlüsselung

Speicherkonten unterstützen die Mehrfachverschlüsselung, aber die zweite Ebene muss explizit aktiviert werden. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#doubly-encrypt-data-with-infrastructure-encryption).  

## <a name="storage-account-errors"></a>Speicherkontofehler

Mit dem Status „Getrennt“ wird für ein Media Services-Konto angegeben, dass für das Konto aufgrund einer Änderung der Speicherzugriffsschlüssel kein Zugriff mehr auf mindestens ein angefügtes Speicherkonto besteht. Für Media Services werden aktuelle Speicherzugriffsschlüssel benötigt, um unter dem Konto viele verschiedene Aufgaben durchführen zu können.

Unten sind die wichtigsten Szenarien aufgeführt, die bewirken, dass ein Media Services-Konto keinen Zugriff auf angefügte Speicherkonten hat.

|Problem|Lösung|
|---|---|
|Das Media Services-Konto oder angefügte Speicherkonten wurden zu separaten Abonnements migriert. |Migrieren Sie das bzw. die Speicherkonten oder das Media Services-Konto, damit diese Komponenten sich alle unter demselben Abonnement befinden. |
|Für das Media Services-Konto wird ein angefügtes Speicherkonto unter einem anderen Abonnement verwendet, da es sich um ein frühes Media Services-Konto handelt, für das dies unterstützt wurde. Alle frühen Media Services-Konten wurden in moderne, Azure Resources Manager-basierte Konten konvertiert und weisen den Status „Getrennt“ auf. |Migrieren Sie das Speicherkonto oder Media Services-Konto, damit diese Komponenten sich alle unter demselben Abonnement befinden.|

## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie Sie ein Speicherkonto an Ihr Media Services-Konto anfügen, finden Sie unter [Erstellen eines Kontos](./create-account-howto.md).
