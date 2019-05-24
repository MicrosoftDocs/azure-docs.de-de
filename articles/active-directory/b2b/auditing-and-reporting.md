---
title: Überwachung eines B2B-Zusammenarbeitsbenutzers und Berichterstellung – Azure Active Directory | Microsoft-Dokumentation
description: Die Eigenschaften für Gastbenutzer sind in der Azure Active Directory B2B-Zusammenarbeit konfigurierbar.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 506721a23a5a18eaea76efaca61df49f20dc9228
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812470"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Überwachung eines B2B-Zusammenarbeitsbenutzers und Berichterstellung
Für Gastbenutzer können Sie vergleichbare Überwachungsfunktionen einrichten wie für Mitglieder. 

## <a name="access-reviews"></a>Zugriffsüberprüfungen
Mithilfe von Zugriffsüberprüfungen können Sie in regelmäßigen Abständen überprüfen, ob Gastbenutzer weiterhin Zugriff auf Ihre Ressourcen benötigen. Das Feature **Zugriffsüberprüfungen** steht in **Azure Active Directory** unter **Verwalten** > **Organisationsbeziehungen** zur Verfügung. (Sie können auch im Azure-Portal unter **Alle Dienste** nach „Zugriffsüberprüfungen“ suchen.) Weitere Informationen zu Zugriffsüberprüfungen finden Sie unter [Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Überwachungsprotokolle

Azure AD-Überwachungsprotokolle enthalten erfasste System- und Benutzeraktivitäten. Dazu zählen auch Aktivitäten, die von Gastbenutzern initiiert wurden. Die Überwachungsprotokolle finden Sie in **Azure Active Directory** unter **Überwachung** > **Überwachungsprotokolle**. Hier sehen Sie ein Beispiel für die Einladung und den Einlösungsverlauf des eingeladenen Benutzers „Sam Oogle“:

![Screenshot mit einem Beispiel für eine Überwachungsprotokollausgabe](./media/auditing-and-reporting/audit-log.png)

Sie können für jedes dieser Ereignisse weitere Details anzeigen. Sehen wir uns zum Beispiel einmal die Details zur Annahme an.

![Screenshot mit einem Beispiel für eine Aktivitätsdetailsausgabe](./media/auditing-and-reporting/activity-details.png)

Sie können diese Protokolle auch aus Azure AD exportieren und ein Berichterstellungstool Ihrer Wahl verwenden, um benutzerdefinierte Berichte zu erstellen.

### <a name="next-steps"></a>Nächste Schritte

- [Eigenschaften von B2B-Zusammenarbeitsbenutzern](user-properties.md)

