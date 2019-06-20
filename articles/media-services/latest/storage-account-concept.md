---
title: Clouduploads und Cloudspeicherung mit Azure Media Services | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Konzepte für Clouduploads und Cloudspeicherung.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: 9cbb995eb3310a2263185d6fd6dba20efce37f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550146"
---
# <a name="cloud-upload-and-storage"></a>Clouduploads und Cloudspeicherung

Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen. Wenn Sie ein Media Services-Konto erstellen, müssen Sie den Namen einer Azure Storage-Kontoressource angeben. Das angegebene Speicherkonto wird an Ihr Media Services-Konto angefügt. 

Das Media Services-Konto und alle zugeordneten Speicherkonten müssen im gleichen Azure-Abonnement enthalten sein. Zur Vermeidung zusätzlicher Wartezeiten und Kosten für ausgehenden Datenverkehr wird dringend empfohlen, Speicherkonten zu verwenden, die sich am selben Ort wie das Media Services-Konto befinden.

Sie müssen über ein **primäres** Speicherkonto verfügen. Darüber hinaus können Sie beliebig viele **sekundäre** Speicherkonten an Ihr Media Services-Konto anfügen. Media Services unterstützt Konten des Typs **Allgemein v2** (GPv2) oder **Allgemein v1** (GPv1). 

>[!NOTE]
> Reine Blobkonten sind als **primäre** Konten nicht zulässig. 

Wir empfehlen die Verwendung von GPv2, damit Sie zwischen „heißen“ und „kalten“ Speicherebenen wählen können. Weitere Informationen zu Speicherkonten finden Sie in der [Übersicht über Azure Storage-Konten](../../storage/common/storage-account-overview.md). 

Es gibt verschiedene SKUs, die Sie für Ihr Speicherkonto auswählen können. Weitere Informationen finden Sie unter [Storage accounts](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) (Speicherkonten). Falls Sie mit Speicherkonten experimentieren möchten, verwenden Sie `--sku Standard_LRS`. Wenn Sie eine SKU für die Produktion auswählen, sollten Sie jedoch die Verwendung von `--sku Standard_RAGRS` erwägen, da diese Option geografische Replikation zum Gewährleisten der Geschäftskontinuität bietet. 

## <a name="assets-in-a-storage-account"></a>Medienobjekte in einem Speicherkonto

In Media Services v3 verwenden Sie Azure Storage-APIs zum Hochladen von Dateien in Medienobjekte. Weitere Informationen finden Sie unter [Konzept von Medienobjekten](assets-concept.md).

> [!Note]
> Sie können den Inhalt von Blobcontainern, die mit dem Media Services SDK generiert wurden, nur mithilfe von Media Service-APIs ändern.
 
## <a name="storage-side-encryption"></a>Speicherseitige Verschlüsselung

Zum Schutz Ihrer im Ruhezustand befindlichen Ressourcen sollten die Ressourcen durch die speicherseitige Verschlüsselung verschlüsselt werden. Die folgende Tabelle zeigt, wie die speicherseitige Verschlüsselung in Media Services v3 funktioniert:

|Verschlüsselungsoption|BESCHREIBUNG|Media Services v3|
|---|---|---|
|Media Services-Speicherverschlüsselung| AES-256-Verschlüsselung, Schlüssel von Media Services verwaltet|Nicht unterstützt<sup>(1)</sup>|
|[Speicherdienstverschlüsselung für ruhende Daten](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Durch Azure Storage angebotene serverseitige Verschlüsselung – Schlüssel wird von Azure oder vom Kunden verwaltet|Unterstützt|
|[Clientseitige Storage-Verschlüsselung](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Durch Azure Storage angebotene clientseitige Verschlüsselung – Schlüssel wird vom Kunden in Key Vault verwaltet|Nicht unterstützt|

<sup>1</sup> In Media Services v3 wird Speicherverschlüsselung (AES-256-Verschlüsselung) nur für die Abwärtskompatibilität unterstützt, wenn Ihre Ressourcen mit Media Services v2 erstellt wurden. Dies bedeutet, dass v3 mit vorhandenen speicherverschlüsselten Ressourcen funktioniert, jedoch nicht die Erstellung neuer zulässt.

## <a name="storage-account-errors"></a>Speicherkontofehler

Mit dem Status „Getrennt“ wird für ein Media Services-Konto angegeben, dass für das Konto aufgrund einer Änderung der Speicherzugriffsschlüssel kein Zugriff mehr auf mindestens ein angefügtes Speicherkonto besteht. Für Media Services werden aktuelle Speicherzugriffsschlüssel benötigt, um unter dem Konto viele verschiedene Aufgaben durchführen zu können.

Unten sind die wichtigsten Szenarien aufgeführt, die bewirken, dass ein Media Services-Konto keinen Zugriff auf angefügte Speicherkonten hat. 

|Problem|Lösung|
|---|---|
|Das Media Services-Konto oder angefügte Speicherkonten wurden zu separaten Abonnements migriert. |Migrieren Sie das bzw. die Speicherkonten oder das Media Services-Konto, damit diese Komponenten sich alle unter demselben Abonnement befinden. |
|Für das Media Services-Konto wird ein angefügtes Speicherkonto unter einem anderen Abonnement verwendet, da es sich um ein frühes Media Services-Konto handelt, für das dies unterstützt wurde. Alle frühen Media Services-Konten wurden in moderne ARM-basierte Konten (Azure Resources Manager) konvertiert und weisen den Status „Getrennt“ auf. |Migrieren Sie das Speicherkonto oder Media Services-Konto, damit diese Komponenten sich alle unter demselben Abonnement befinden.|

## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie Sie ein Speicherkonto an Ihr Media Services-Konto anfügen, finden Sie unter [Erstellen eines Kontos](create-account-cli-quickstart.md).
