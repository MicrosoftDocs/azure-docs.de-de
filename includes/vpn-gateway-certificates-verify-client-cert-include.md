---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177950"
---
Überprüfen Sie die folgenden Punkte, falls bei der Verbindungsherstellung Probleme auftreten:

- Falls Sie ein Clientzertifikat mit dem **Zertifikatexport-Assistenten** exportiert haben, vergewissern Sie sich, dass Sie es als PFX-Datei exportiert und **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** ausgewählt haben. Bei Verwendung dieses Werts werden auch die Stammzertifikatinformationen exportiert. Nach dem Installieren des Zertifikats auf dem Clientcomputer wird dann auch das Stammzertifikat in der PFX-Datei installiert. Um sicherzustellen, dass das Stammzertifikat installiert ist, öffnen Sie **Benutzerzertifikate verwalten** und wählen **Trusted Root Certification Authorities\Certificates** aus. Überprüfen Sie, ob das Stammzertifikat aufgelistet ist, denn sonst funktioniert die Authentifizierung nicht.

- Wenn Sie ein Zertifikat verwendet haben, das mit einer Lösung einer Unternehmenszertifizierungsstelle ausgestellt wurde, und können sich nicht authentifizieren, überprüfen Sie die Authentifizierungsreihenfolge auf dem Clientzertifikat. Überprüfen Sie die Reihenfolge der Authentifizierungsliste, indem Sie auf das Clientzertifikat doppelklicken, die Registerkarte **Details** und dann **Erweiterte Schlüsselverwendung** auswählen. Achten Sie darauf, dass in der Liste *Clientauthentifizierung* als erster Eintrag aufgeführt ist. Wenn nicht, müssen Sie ein Clientzertifikat basierend auf der Benutzervorlage ausstellen, die als ersten Eintrag in der Liste *Clientauthentifizierung* enthält.

- Zusätzliche Informationen zur Problembehandlung für P2S finden Sie unter [Problembehandlung: Azure Punkt-zu-Standort-Verbindungsprobleme](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).