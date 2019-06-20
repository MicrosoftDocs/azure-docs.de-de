---
title: Threat Modeling Tool-Releases – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Dokumentation der Versionshinweise für das Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586226"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, Updaterelease 7.1.60126.1 – 1/29/2019

Version 7.1.60126.1 für das Microsoft Threat Modeling Tool wurde am 29. Januar 2019 veröffentlicht und umfasst die folgenden Änderungen:

- Die erforderliche Mindestversion von .NET wurde auf [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) erhöht.
- Die erforderliche Mindestversion von Windows wurde aufgrund der .NET-Abhängigkeit auf [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) erhöht.
- Dem Optionsmenü des Tools wurde eine Funktion zum Umschalten der Modellvalidierung hinzugefügt.
- Verschiedene Links in den Bedrohungseigenschaften wurden aktualisiert.
- Auf dem Startbildschirm des Tools wurden kleine Änderungen an der Benutzeroberfläche vorgenommen.
- Das Threat Modeling Tool erbt nun die TLS-Einstellungen des Hostbetriebssystems und wird in Umgebungen unterstützt, in denen TLS 1.2 oder höher erforderlich ist.

## <a name="feature-changes"></a>Funktionsänderungen

### <a name="model-validation-option"></a>Option zur Modellvalidierung

Basierend auf Kundenfeedback wurde dem Tool eine Option hinzugefügt, mit der die Modellvalidierung aktiviert oder deaktiviert werden kann. Bisher erhielten Sie möglicherweise eine der folgenden ähnliche Fehlermeldung im Nachrichtenfenster, wenn Ihre Vorlage einen einzigen unidirektionalen Datenfluss zwischen zwei Objekten verwendete: ObjectsName requires at least one 'Any'. (Für ObjectsName ist mindestens ein Any-Element erforderlich.) Durch das Deaktivieren der Modellvalidierung werden diese Warnungen nicht in der Ansicht angezeigt.

Die Option zum Aktivieren/Deaktivieren der Modellvalidierung befindet sich im Menü „File“ > „Settings“ > „Options“ („Datei“ > „Einstellungen“ > „Optionen“). Die Einstellung ist standardmäßig deaktiviert.

![Option zur Modellvalidierung](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Systemanforderungen

- Unterstützte Betriebssysteme
  - [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) oder höher
- Erforderliche .NET-Version
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)
- Zusätzliche Anforderungen
  - Eine Internetverbindung ist erforderlich, um Updates für das Tool sowie Vorlagen zu erhalten.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="unsupported-systems"></a>Nicht unterstützte Systeme

#### <a name="issue"></a>Problem

Benutzer von Windows 10-Systemen, die .NET 4.7.1 oder höher nicht installieren können – z.B. Windows 10 Enterprise LTSB, Version 1507 –, können das Tool nach dem Upgrade nicht öffnen. Diese älteren Windows-Versionen werden nicht mehr als Plattform für das Threat Modeling Tool unterstützt, deshalb sollte das neueste Update nicht installiert werden.

#### <a name="workaround"></a>Problemumgehung

Benutzer von Windows 10 Enterprise LTSB (Version 1507), die das neueste Update installiert haben, können über das Dialogfeld zum Deinstallieren in „Apps & Features“ eine Zurücksetzung auf die vorherige Threat Modeling Tool-Version durchführen.

## <a name="documentation-and-feedback"></a>Dokumentation und Feedback

- Dokumentation für das Threat Modeling Tool befindet sich auf [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) und umfasst Informationen [zur Verwendung des Tools](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nächste Schritte

Laden Sie die aktuelle Version des [Microsoft Threat Modeling Tools](https://aka.ms/threatmodelingtool) herunter.
