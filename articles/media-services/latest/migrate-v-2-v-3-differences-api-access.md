---
title: API-Zugriff zwischen Media Services V2 und V3
description: In diesem Artikel werden die Unterschiede beim API-Zugriff zwischen Azure Media Services V2 und V3 beschrieben.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 831bc737fbd54685cdc8358642bc37e7b2df2c42
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953715"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Unterschiede beim API-Zugriff zwischen Azure Media Services V2 und V3

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-2.svg)

In diesem Artikel werden die Unterschiede beim API-Zugriff zwischen Azure Media Services V2 und V3 beschrieben.

## <a name="api-access"></a>API-Zugriff

Alle Media Services-Konten haben Zugriff auf die V3-API. Es wird jedoch dringend empfohlen, für die migrationsbezogene Entwicklung ein neues Konto zu verwenden, bevor Sie aktualisierten Code auf ein vorhandenes V2-Konto anwenden. Der Grund hierfür ist, dass V3-Entitäten nicht abwärtskompatibel mit V2 sind. Einige V2-Entitäten wie Medienobjekte sind vorwärtskompatibel mit V3.
Sie können vorhandene Konten weiterhin verwenden, wenn Sie die V2- und V3-APIs nicht gemeinsam verwenden und dann versuchen, zu V2 zurückzukehren. Dies wird jedoch nicht empfohlen.

Der Zugriff auf die V2-API ist bis zur Einstellung im Jahr 2024 möglich.

Sie können während der Migration ein V3-Konto erstellen, das weiterhin Zugriff auf V2 hat.  Für das Erstellen des Kontos haben Sie folgende Möglichkeiten:

- REST-API mit älterer Version
- Aktivieren des Kontrollkästchens im Portal

> [!div class="mx-imgBorder"]
> [ ![Erstellen des Kontos im Portal](./media/migration-guide/v-3-v-2-access-account-creation-small.png) ](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Alle .NET-, Befehlszeilenschnittstellen- und sonstigen SDKs zielen auf die neueste API der Version 2020-05-01 ab, Sie müssen ältere API-Versionen also suchen oder konfigurieren.

> [!NOTE]
> Neue Konten, die mit der API 2020-05-01 erstellt wurden, können keine V2-APIs verwenden.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
