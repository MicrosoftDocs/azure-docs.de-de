---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061614"
---
Wenn Sie eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herstellen möchten, müssen Sie ein Clientzertifikat installieren. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde.

1. Suchen Sie die *PFX* -Datei, und kopieren Sie sie auf den Clientcomputer. Doppelklicken Sie auf dem Clientcomputer auf die *PFX* -Datei, um sie zu installieren. Behalten Sie für **Speicherort** die Einstellung **Aktueller Benutzer** bei, und klicken Sie auf **Weiter**.
1. Nehmen Sie auf der Seite **Zu importierende Datei** keine Änderungen vor. Klicken Sie auf **Weiter**.
1. Geben Sie auf der Seite **Schutz für den privaten Schlüssel** das Kennwort für das Zertifikat ein, oder überprüfen Sie die Korrektheit des Sicherheitsprinzipals, und klicken Sie anschließend auf **Weiter**.
1. Behalten Sie auf der Seite **Zertifikatspeicher** den Standardspeicherort bei, und klicken Sie dann auf **Weiter**.
1. Wählen Sie **Fertig stellen** aus. Klicken Sie in der **Sicherheitswarnung** für die Zertifikatinstallation auf **Ja**. Sie können problemlos „Ja“ für diese Sicherheitswarnung auswählen, da Sie das Zertifikat generiert haben.
1. Das Zertifikat wird nun erfolgreich importiert.
