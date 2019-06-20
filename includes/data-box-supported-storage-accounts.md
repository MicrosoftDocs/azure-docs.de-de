---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242123"
---
Im Folgenden finden Sie eine Liste der unterstützten Speicherkonten und Speichertypen für das Data Box-Gerät. Eine vollständige Liste aller verschiedenen Typen von Speicherkonten und ihren vollständigen Funktionen finden Sie unter [Speicherkontotypen](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Speicherkonto/unterstützte Speichertypen** | **Blockblob** |**Seitenblob*** |**Azure Files** |**Hinweise**|
| --- | --- | -- | -- | -- |
| Klassisch Standard | J | J | J |
| Universell V1 Standard  | J | J | J | Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt.|
| Universell V1 Premium  |  | J| | |
| Universell V2 Standard  | J | J | J | Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt.|
| Universell V2 Premium  |  |J | | |
| Blobspeicher Standard |J | | |Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt. |

\* *: In Seitenblobs hochgeladene Daten müssen einem ganzzahligen Vielfachen von 512 Byte entsprechen, z.B. VHDs.*

>[!NOTE]
> Azure Data Lake Storage Gen 2-Konten werden nicht unterstützt.
