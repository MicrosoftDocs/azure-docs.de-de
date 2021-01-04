---
title: Microsoft Threat Modeling Tool, Release 9.4.2019
titleSuffix: Azure
description: Dokumentation der Versionshinweise für das Threat Modeling Tool-Release 7.1.60408.1
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 69dd2c6cdba41779849b4eb6b889cde9b1d6e5c9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913567"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool, Updaterelease 7.1.60408.1: 09.04.2019

Version 7.1.60408.1 für das Microsoft Threat Modeling Tool (TMT) wurde am 9. April 2019 veröffentlicht und umfasst die folgenden Änderungen:

- Neue Schablonen für Azure Key Vault und Azure Traffic Manager
- Die TMT-Versionsnummer wird jetzt auf dem Startbildschirm angezeigt
- Die Supportlinks wurden aktualisiert
- Behebung von Programmfehlern

## <a name="feature-changes"></a>Funktionsänderungen

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Neue Schablonen für Azure Key Vault und Azure Traffic Manager

![Screenshot: Symbole für Azure Key Vault und Azure Traffic Manager](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Der Azure-Schablonensammlung wurden neue Schablonen und Bedrohungen für Azure Key Vault und Azure Traffic Manager hinzugefügt. Beim Öffnen von Modellen, die auf der Azure-Schablonensammlung basieren, werden Benutzer zum Aktualisieren der dem Modell zugeordneten Vorlage aufgefordert. Das Aktualisieren eines auf der Azure-Schablonensammlung basierenden Modells kann auch manuell mithilfe des Befehls „Vorlage übernehmen“ im Menü „Datei“ und erneutes Anwenden der neuesten Azure Cloud Services.tb7-Datei erfolgen.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Die TMT-Versionsnummer wird jetzt auf dem Startbildschirm angezeigt

Die Clientversion des Threat Modeling Tools wird jetzt zwecks bequemem Zugriff auf der Startseite der Anwendung angezeigt.

![Screenshot: Microsoft Threat Modeling Tool mit der Clientversionsnummer](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Die Supportlinks wurden aktualisiert

Alle Supportlinks innerhalb des Tools wurden aktualisiert, um Benutzer an [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) statt an ein MSDN-Forum zu leiten.

## <a name="system-requirements"></a>Systemanforderungen

- Unterstützte Betriebssysteme
  - [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) oder höher
- Erforderliche .NET-Version
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) oder höher
- Zusätzliche Anforderungen
  - Eine Internetverbindung ist erforderlich, um Updates für das Tool sowie Vorlagen zu erhalten.

## <a name="documentation-and-feedback"></a>Dokumentation und Feedback

- Dokumentation für das Threat Modeling Tool befindet sich auf [docs.microsoft.com](threat-modeling-tool.md) und umfasst Informationen [zur Verwendung des Tools](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nächste Schritte

Laden Sie die aktuelle Version des [Microsoft Threat Modeling Tools](https://aka.ms/threatmodelingtool) herunter.
