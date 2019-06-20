---
title: 'Azure AD Connect: Synchronisierungsdienstinstanzen | Microsoft-Dokumentation'
description: Diese Seite beschreibt besondere Überlegungen für Azure AD-Instanzen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298829"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Besondere Überlegungen zu Instanzen
Azure AD Connect wird am häufigsten mit der weltweiten Instanz von Azure AD und Office 365 verwendet. Es gibt jedoch noch weitere Instanzen, und für diese gelten andere Anforderungen an URLs sowie weitere besondere Überlegungen.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Deutschland
Die [Microsoft Cloud Deutschland](https://www.microsoft.de/cloud-deutschland) ist eine unabhängige Cloud, die von einem deutschen Datentreuhänder betrieben wird.

| URLs, die im Proxyserver geöffnet werden müssen |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Zertifikatsperrlisten |

Wenn Sie sich bei Ihrem Azure AD-Mandanten anmelden, müssen Sie ein Konto in der Domäne „onmicrosoft.de“ verwenden.

In der Microsoft Cloud Deutschland derzeit nicht enthaltene Features:

* **Kennwortrückschreiben** ist für die Vorschauversion mit Azure AD Connect-Version 1.1.570.0 und höher verfügbar.
* Andere Azure AD Premium-Dienste sind nicht verfügbar.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
Die [Microsoft Azure Government-Cloud](https://azure.microsoft.com/features/gov/) ist eine Cloud für US-Regierungsbehörden.

Diese Cloud wurde von früheren Versionen von DirSync unterstützt. Ab Build 1.1.180 von Azure AD Connect wird die nächste Generation der Cloud unterstützt. Diese Generation verwendet ausschließlich in den USA basierte Endpunkte und weist eine andere Liste von URLs auf, die Sie in Ihrem Proxyserver öffnen müssen.

| URLs, die im Proxyserver geöffnet werden müssen |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (erforderlich für die automatische Erkennung von Azure Government-Mandanten) |
| \*.gov.us.microsoftonline.com |
| +Zertifikatsperrlisten |

> [!NOTE]
> Wie bei Version 1.1.647.0 von Azure AD Connect ist die Festlegung des AzureInstance-Werts in der Registrierung nicht mehr erforderlich, sofern *.windows.net auf Ihren Proxyservern geöffnet ist. Kunden, die keine Internetverbindung von ihren Azure AD Connect-Servern erlauben, können jedoch die folgende manuelle Konfiguration verwenden.

### <a name="manual-configuration"></a>Manuelle Konfiguration

Mit den folgenden manuellen Konfigurationsschritten wird sichergestellt, dass Azure AD Connect die Azure Government-Synchronisierungsendpunkte verwendet.

1. Starten Sie die Azure AD Connect-Installation.
2. Wenn die erste Seite angezeigt wird, auf der Sie aufgefordert werden, den Endbenutzer-Lizenzvertrag zu akzeptieren, fahren Sie nicht fort, sondern lassen Sie den Installations-Assistenten offen.
3. Starten Sie den Registrierungs-Editor, und ändern Sie den Registrierungsschlüssel `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` in den Wert `4`.
4. Wechseln Sie zurück zum Azure AD Connect-Installations-Assistenten, akzeptieren Sie den Endbenutzer-Lizenzvertrag, und fahren Sie fort. Stellen Sie bei der Installation sicher, dass der Installationspfad zur **benutzerdefinierten Konfiguration** verwendet wird (nicht die Expressinstallation), und setzen Sie dann die Installation wie üblich fort.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
