---
title: Kundenseitig verwaltete Schlüssel (Bring Your Own Key)
description: 'Sie können in Media Services einen vom Kunden verwalteten Schlüssel (also: „Bring Your Own Key“) verwenden.'
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 27d357279a54d7abc351370e7afda3a7961bac33
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428553"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Bring Your Own Key (vom Kunden verwaltete Schlüssel) in Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

„Bring Your Own Key“ (BYOK) ist eine im gesamten Bereich von Azure unterstützte Initiative, die Kunden dabei unterstützt, ihre Workloads in die Cloud auszulagern. Vom Kunden verwaltete Schlüssel ermöglichen es Kunden, die Konformitätsbestimmungen ihrer Branche einzuhalten, und verbessern die Mandantenisolation eines Diensts. Die Kontrolle der Verschlüsselungsschlüssel in die Hand der Kunden zu legen, ist eine Möglichkeit, unnötigen Zugriff zu minimieren und die Kontrolle und Vertrauenswürdigkeit von Microsoft-Diensten zu stärken.

## <a name="keys-and-key-management"></a>Schlüssel und Schlüsselverwaltung

Sie können eigene Schlüssel in Media Services verwenden, wenn Sie die Media Services-API 2020-05-01 einsetzen. Für alle Konten wird ein Standard-Kontoschlüssel erstellt, der mit einem Systemschlüssel verschlüsselt wird, der im Besitz von Media Services ist. Wenn Sie Ihren eigenen Schlüssel verwenden, wird der Kontoschlüssel mit Ihrem Schlüssel verschlüsselt. Inhaltsschlüssel werden mit dem Kontoschlüssel verschlüsselt. JobInputHttp-URLs und symmetrische Schlüssel zur Tokenüberprüfung werden ebenfalls verschlüsselt.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Ein vom System verwalteter Schlüssel wird durch einen vom Kunden verwalteten Schlüssel ersetzt":::

Media Services verwendet die verwaltete Identität des Media Services-Kontos, um Ihren Schlüssel aus einem Schlüsseltresor zu lesen, der sich in Ihrem Besitz befindet. Für Media Services ist es erforderlich, dass sich der Schlüsseltresor im gleichen Bereich wie das Konto befindet und dass vorläufiges Löschen und Schutz vor Bereinigung aktiviert sind.

Bei Ihrem Schlüssel kann es sich um einen 2048-, 3072- oder 4096-RSA-Schlüssel handeln, und sowohl HSM- als auch Softwareschlüssel werden unterstützt.

> [!NOTE]
> EC-Schlüssel werden nicht unterstützt.

Sie können wahlweise einen Schlüsselnamen und eine Schlüsselversion oder nur einen Schlüsselnamen angeben. Wenn Sie nur einen Schlüsselnamen verwendet, verwendet Media Services die aktuellste Schlüsselversion. Neue Versionen von Kundenschlüsseln werden automatisch erkannt, und der Kontoschlüssel wird erneut verschlüsselt.

> [!WARNING]
> Media Services überwacht den Zugriff auf den Kundenschlüssel. Wenn der Zugriff auf den Kundenschlüssel nicht möglich ist (beispielsweise, weil der Schlüssel gelöscht wurde, der Schlüsseltresor gelöscht wurde oder die Zugriffsberechtigung entzogen wurde), überführt Media Services das Konto in den Status „Customer Key Inaccessible“ (Kein Zugriff auf den Kundenschlüssel – effektiv ist das Konto damit deaktiviert). Allerdings kann das Konto in diesem Zustand gelöscht werden. Für das Konto werden lediglich die Vorgänge GET, LIST und DELETE unterstützt; alle anderen Anforderungen (Codierung, Streamen usw.) schlagen fehl, bis der Zugriff auf den Kontoschlüssel wiederhergestellt ist.

## <a name="double-encryption"></a>Doppelte Verschlüsselung

Media Services unterstützt die Mehrfachverschlüsselung automatisch. Bei ruhenden Daten wird von der ersten Verschlüsselungsebene abhängig von der Einstellung von `AccountEncryption` für das Konto ein kundenseitig verwalteter Schlüssel oder ein von Microsoft verwalteter Schlüssel verwendet.  Die zweite Verschlüsselungsebene für ruhende Daten wird automatisch mit einem separaten, von Microsoft verwalteten Schlüssel bereitgestellt. Weitere Informationen dazu finden Sie unter [Mehrfachverschlüsselung in Azure](../../security/fundamentals/double-encryption.md).

> [!NOTE]
> Die Mehrfachverschlüsselung wird automatisch für das Media Services-Konto aktiviert. Sie müssen jedoch den kundenseitig verwalteten Schlüssel und die Mehrfachverschlüsselung in Ihrem Speicherkonto separat konfigurieren. Weitere Informationen finden Sie unter [Speicherverschlüsselung](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="tutorials"></a>Tutorials

- [Tutorial: Verwenden von kundenseitig verwalteten Schlüsseln (BYOK) mit Media Services über das Azure-Portal](tutorial-byok-portal.md)
- [Tutorial: Verwenden von kundenseitig verwalteten Schlüsseln (BYOK) mit der Media Services-REST-API](tutorial-byok-postman.md)

## <a name="next-steps"></a>Nächste Schritte

[Schützen Sie Ihren Inhalt mit der dynamischen Verschlüsselung von Media Services](content-protection-overview.md)
