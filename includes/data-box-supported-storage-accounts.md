---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 25c2ea04cd062554a975c63aae9b97846e646d68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028569"
---
Im Folgenden finden Sie eine Liste der unterstützten Speicherkonten und Speichertypen für das Data Box-Gerät. Eine vollständige Liste aller verschiedenen Typen von Speicherkonten und ihren vollständigen Funktionen finden Sie unter [Speicherkontotypen](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Die folgende Tabelle zeigt die unterstützten Speicherkonten für Importaufträge.

| **Speicherkonto/unterstützte Speichertypen** | **Blockblob** |**Seitenblob** _ |_ *Azure-Dateien** |**Hinweise**|
| --- | --- | -- | -- | -- |
| Klassisch Standard | J | J | J |
| Universell V1 Standard  | J | J | J | Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt.|
| Universell V1 Premium  |  | J| | |
| Universell V2 Standard  | J | J | J | Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt.|
| Universell V2 Premium  |  |J | | |
| Blobspeicher Standard |J | | |Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt. |

\* *: In Seitenblobs hochgeladene Daten müssen einem ganzzahligen Vielfachen von 512 Byte entsprechen, z. B. VHDs.*

Die folgende Tabelle zeigt die unterstützten Speicherkonten für Exportaufträge.

| **Speicherkonto/unterstützte Speichertypen** | **Blockblob** |**Seitenblob** _ |_ *Azure-Dateien** |**Unterstützte Zugriffsebenen**|
| --- | --- | -- | -- | -- |
| Klassisch Standard | J | J | J | |
| Universell V1 Standard  | J | J | J | Heiße Ebene, kalte Ebene|
| Universell V1 Premium  |  | J| | |
| Universell V2 Standard  | J | J | J | Heiße Ebene, kalte Ebene|
| Universell V2 Premium  |  |J | | |
| Blobspeicher Standard |J | | |Heiße Ebene, kalte Ebene |
| Block Blob Storage Premium |J | | |Heiße Ebene, kalte Ebene |
| Page Blob Storage Premium | |J | | |

> [!IMPORTANT]
> - Data Box unterstützt Warteschlangen, Tabellen und Datenträger nicht als Speichertypen von Importaufträgen für universelle Konten. Data Box unterstützt Warteschlangen, Tabellen, Datenträger und Azure Data Lake Gen 2 nicht als Speichertypen von Exportaufträgen für universelle Konten.
> - Data Box unterstützt keine Anfügeblobs für Blob Storage- und Block Blob Storage-Konten.
> - Data Box unterstützt keine File Storage Premium-Konten.
> - In Seitenblobs hochgeladene Daten müssen einem ganzzahligen Vielfachen von 512 Byte entsprechen, z. B. VHDs.
> - Es können maximal 80 TB exportiert werden.
> - Dateiverlauf und Blobmomentaufnahmen werden nicht exportiert.