---
title: Typische Szenarien für die Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, welche allgemeinen Schritte Sie in typischen Szenarien für die Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) ausführen müssen.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96442a9d49581da6841fa7acb8329354ec727f60
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918476"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Typische Szenarien für die Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Es gibt mehrere Möglichkeiten, wie Sie die Berechtigungsverwaltung für Ihre Organisation konfigurieren können. Für den Einstieg ist es jedoch hilfreich, die typischen Szenarien für Administratoren, genehmigende Personen und Anforderer zu verstehen.

## <a name="administrators"></a>Administratoren

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Ich bin noch nicht mit der Berechtigungsverwaltung vertraut und benötige Hilfe bei den ersten Schritten

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | [Durcharbeiten des Tutorials zum Erstellen Ihres ersten Zugriffspakets](entitlement-management-access-package-first.md) | [![Azure-Portal, Screenshot](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Ich möchte, dass die Benutzer in meinem Verzeichnis den Zugriff auf Gruppen, Anwendungen oder SharePoint-Websites anfordern können

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** [Erstellen eines neuen Zugriffspakets in einem Katalog](entitlement-management-access-package-create.md#start-new-access-package) | ![Erstellen eines Zugriffspakets](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Hinzufügen von Ressourcenrollen zu einem Zugriffspaket](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Gruppen</li><li>ANWENDUNGEN</li><li>SharePoint-Websites</li></ul> | ![Hinzufügen von Ressourcenrollen](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Hinzufügen einer Richtlinie](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Für Benutzer in Ihrem Verzeichnis</li><li>Genehmigung anfordern</li><li>Ablaufeinstellungen</li></ul> | ![Richtlinie hinzufügen](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Ich möchte, dass die Benutzer meiner Geschäftspartner einschließlich der Benutzer, die sich noch nicht in meinem Verzeichnis befinden, den Zugriff auf Gruppen, Anwendungen oder SharePoint-Websites anfordern können

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** [Erstellen eines neuen Zugriffspakets in einem Katalog](entitlement-management-access-package-create.md#start-new-access-package) | ![Erstellen eines Zugriffspakets](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Hinzufügen von Ressourcenrollen zu einem Zugriffspaket](entitlement-management-access-package-edit.md#add-resource-roles) | ![Hinzufügen von Ressourcenrollen](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Hinzufügen einer Richtlinie für externe Benutzer](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Für Benutzer, die sich nicht in Ihrem Verzeichnis befinden</li><li>Genehmigung anfordern</li><li>Ablaufeinstellungen</li></ul> | ![Hinzufügen einer Richtlinie für externe Benutzer](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Senden des Links zum Portal „Mein Zugriff“zum Anfordern des Zugriffspakets an Ihren Geschäftspartner](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Der Geschäftspartner kann den Link an seine Benutzer weiterleiten.</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Ich möchte die Gruppen, Anwendungen oder SharePoint-Websites in einem Zugriffspaket ändern

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** Öffnen des Zugriffspakets | ![Hinzufügen von Ressourcenrollen](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Hinzufügen oder Entfernen von Ressourcenrollen](entitlement-management-access-package-edit.md#add-resource-roles) | ![Hinzufügen von Ressourcenrollen](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Ich möchte anzeigen, wer über eine Zuweisung für Gruppen, Anwendungen oder SharePoint-Websites verfügt

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** Öffnen eines Zugriffspakets | ![Hinzufügen von Ressourcenrollen](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Anzeigen von Zuweisungen](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Anzeigen, welche Benutzer Zugriff auf ein Zugriffspaket haben</li><li>Anzeigen, für welchen Benutzer der Zugriff abgelaufen ist</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Ich möchte die Gruppen, Anwendungen oder SharePoint-Websites anzeigen, auf die ein Benutzer zugreifen kann

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | [Anzeigen des Bericht zu Benutzerzuweisungen](entitlement-management-reports.md)<ul><li>Anzeigen, wann die Anforderung erfolgte und wer sie genehmigt hat</li></ul> |  |

## <a name="approvers"></a>Genehmigende Personen

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Ich möchte Anforderungen für den Zugriff auf Gruppen, Anwendungen oder SharePoint-Websites genehmigen

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** [Öffnen der Anforderung im Portal „Mein Zugriff“](entitlement-management-request-approve.md#open-request) | [![Portal „Mein Zugriff“, Screenshot](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Genehmigen der Zugriffsanforderung](entitlement-management-request-approve.md#approve-or-deny-request) | ![Genehmigen des Zugriffs](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Anforderer

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Ich möchte die mir zur Verfügung stehenden Gruppen, Anwendungen oder SharePoint-Websites anzeigen und Zugriff anfordern

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** [Anmelden beim Portal „Mein Zugriff“](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Portal „Mein Zugriff“, Screenshot](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Suchen des Zugriffspakets |  |
> | **3.** [Zugriff anfordern](entitlement-management-request-access.md#request-an-access-package) | ![Zugriff anfordern](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Ich bin ein externer Benutzer und möchte den Zugriff auf Gruppen, Anwendungen oder SharePoint-Websites über einen direkten Link anfordern

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** [Suchen nach dem erhaltenen Link zum Portal „Mein Zugriff“](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Anmelden beim Portal „Mein Zugriff“](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Portal „Mein Zugriff“, Screenshot](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Zugriff anfordern](entitlement-management-request-access.md#request-an-access-package) | ![Anfordern des Zugriffs als externer Benutzer](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Ich möchte die Gruppen, Anwendungen oder SharePoint-Websites anzeigen, auf die ich bereits Zugriff habe

> [!div class="mx-tableFixed"]
> | Schritte | Beispiel |
> | --- | --- |
> | **1.** [Anmelden beim Portal „Mein Zugriff“](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Portal „Mein Zugriff“, Screenshot](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Anzeigen aktiver Zugriffspakete |  |

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen Ihres ersten Zugriffspakets](entitlement-management-access-package-first.md)
- [Bearbeiten und Verwalten eines vorhandenen Zugriffspakets](entitlement-management-access-package-edit.md)
