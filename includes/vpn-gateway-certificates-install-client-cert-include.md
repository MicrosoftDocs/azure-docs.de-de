---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0068bd151c3d7d243b05c326ec73a201f4131296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146798"
---
Wenn Sie eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herstellen möchten, müssen Sie ein Clientzertifikat installieren. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde.

1. Suchen Sie die *PFX* -Datei, und kopieren Sie sie auf den Clientcomputer. Doppelklicken Sie auf dem Clientcomputer auf die *PFX* -Datei, um sie zu installieren. Behalten Sie für **Speicherort** die Einstellung **Aktueller Benutzer** bei, und klicken Sie auf **Weiter**.
2. Nehmen Sie auf der Seite **Zu importierende Datei** keine Änderungen vor. Klicken Sie auf **Weiter**.
3. Geben Sie auf der Seite **Schutz für den privaten Schlüssel** das Kennwort für das Zertifikat ein, oder überprüfen Sie die Korrektheit des Sicherheitsprinzipals, und klicken Sie anschließend auf **Weiter**.
4. Behalten Sie auf der Seite **Zertifikatspeicher** den Standardspeicherort bei, und klicken Sie dann auf **Weiter**.
5. Klicken Sie auf **Fertig stellen**. Klicken Sie in der **Sicherheitswarnung** für die Zertifikatinstallation auf **Ja**. Sie können beruhigt auf „Ja“ klicken, da Sie das Zertifikat generiert haben. Das Zertifikat wird nun erfolgreich importiert.