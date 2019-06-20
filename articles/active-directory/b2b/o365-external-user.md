---
title: Externe Office 365-Freigaben und B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Erläutert die gemeinsame Verwendung von Ressourcen mit externen Partnern, die Office 365- und Azure Active Directory B2B-Zusammenarbeit verwenden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f6cdc782f091709ed00358dd309e9fd4ccfd0eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807694"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Externe Office 365-Freigaben und Azure Active Directory B2B-Zusammenarbeit

Externe Office 365-Freigaben (OneDrive, SharePoint Online, einheitliche Gruppen usw.) und die Verwendung der Azure AD B2B-Zusammenarbeit sind technisch gesehen das Gleiche. Bei allen externen Freigabetools (außer OneDrive/SharePoint Online), einschließlich Gästen in Office 365-Gruppen, werden bereits die Einladungs-APIs der Azure AD B2B-Zusammenarbeit für die Freigabe verwendet.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Wie unterscheidet sich Azure AD B2B von externen Freigaben in SharePoint Online?

OneDrive/SharePoint Online hat einen separaten Einladung-Manager. Unterstützung für die externe Freigabe in OneDrive/SharePoint Online begann, bevor die Unterstützung durch Azure AD entwickelt wurde. Im Lauf der Zeit wurde die externe OneDrive/SharePoint Online-Freigabe um eine Vielzahl von Features erweitert, und viele Millionen Benutzer verwenden das integrierte Freigabemuster des Produkts. Es gibt jedoch einige feine Unterschiede zwischen der Funktionsweise der externen OneDrive-/SharePoint Online-Freigaben und der Azure AD B2B-Kollaboration. Erfahren Sie mehr über externe OneDrive-/SharePoint Online-Freigaben in [Übersicht über externe Freigaben](https://docs.microsoft.com/sharepoint/external-sharing-overview). Ganz allgemein unterscheidet sich der Prozess von Azure AD B2B auf folgende Weise:

- OneDrive/SharePoint Online fügt Benutzer dem Verzeichnis hinzu, nachdem sie ihre Einladungen eingelöst haben. Deshalb werden die Benutzer vor dem Einlösen nicht im Azure AD-Portal angezeigt. Wenn der Benutzer in der Zwischenzeit von einer anderen Website eingeladen wird, wird eine neue Einladung generiert. Wenn Sie jedoch Azure AD B2B-Zusammenarbeit verwenden, werden Benutzer sofort bei Einladung hinzugefügt, sodass sie überall angezeigt werden.

- Die Einlösungserfahrung in OneDrive/SharePoint Online unterscheidet sich von der Erfahrung in Azure AD B2B-Zusammenarbeit. Nachdem ein Benutzer eine Einladung eingelöst hat, sind die Erfahrungen gleich.

- In Azure AD B2B-Zusammenarbeit eingeladene Benutzer können in Freigabedialogfeldern von OneDrive/SharePoint Online ausgewählt werden. Benutzer, die über OneDrive/SharePoint Online eingeladen wurden, werden nach dem Einlösen der Einladung auch in Azure AD angezeigt.

- Die Lizenzbedingungen unterscheiden sich. Für jede kostenpflichtige Azure AD-Lizenz können Sie bis zu 5 Gastbenutzern Zugriff auf Ihre kostenpflichtigen Azure AD-Features gewähren. Weitere Informationen zur Lizenzierung finden Sie unter [Azure AD B2B-Lizenzierung](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) und [„Was ist ein externer Benutzer?“ in der Übersicht über die externe SharePoint Online-Freigabe](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Um externe Freigaben in OneDrive/SharePoint Online mit der Azure AD B2B-Kollaboration zu verwalten, legen Sie die Einstellung für externe OneDrive-/SharePoint Online-Freigaben auf **Freigabe nur für externe Benutzer zulassen, die bereits im Verzeichnis Ihrer Organisation vorhanden sind** fest. Benutzer können zu extern freigegebenen Websites wechseln und aus externen Projektmitarbeitern auswählen, die der Administrator hinzugefügt hat. Ein Administrator kann die externen Projektmitarbeiter über die Einladungs-APIs der B2B-Zusammenarbeit hinzufügen.


![Die Einstellung der externen OneDrive/SharePoint Online-Freigabe](media/o365-external-user/odsp-sharing-setting.png)

Nach dem Aktivieren der externen Freigabe ist die Option, in der SharePoint Online-Personenauswahl (SPO) nach vorhandenen Gastbenutzern zu suchen, gemäß dem Verhalten in älteren Versionen standardmäßig deaktiviert.

Sie können dieses Feature mit der Einstellung „ShowPeoplePickerSuggestionsForGuestUsers“ auf Mandanten- und Websitesammlungsebene aktivieren. Sie können dieses Feature mithilfe der Cmdlets „Set-SPOTenant“ und „Set-SPOSite“ festlegen, damit Mitglieder das Verzeichnis nach allen vorhandenen Gastbenutzern durchsuchen können. Änderungen im Mandantenbereich wirken sich nicht auf bereits bereitgestellte SPO-Websites aus.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
* [Hinzufügen eines B2B-Zusammenarbeitsbenutzers zu einer Rolle](add-guest-to-role.md)
* [Delegieren von Einladungen zur B2B-Zusammenarbeit](delegate-invitations.md)
* [Dynamische Gruppen und B2B-Zusammenarbeit](use-dynamic-groups.md)
* [Problembehandlung für die Azure Active Directory B2B-Zusammenarbeit](troubleshoot.md)
