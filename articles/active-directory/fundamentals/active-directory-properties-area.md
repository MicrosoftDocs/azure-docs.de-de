---
title: Hinzufügen der Datenschutzinformationen Ihrer Organisation – Azure Active Directory | Microsoft-Dokumentation
description: Dieser Artikel enthält Anleitungen, wie Sie Datenschutzinformationen Ihrer Organisation im Bereich „Eigenschaften“ von Azure Active Directory hinzufügen.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996752"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Hinzufügen der Datenschutzinformationen Ihrer Organisation mit Azure Active Directory
In diesem Artikel wird erläutert, wie ein Mandantenadministrator dem Azure Active Directory-Mandanten (Azure AD) einer Organisation über das Azure-Portal Informationen in Bezug auf den Datenschutz hinzufügen kann.

Wir empfehlen Ihnen dringend, sowohl die Kontaktdaten Ihres globalen Ansprechpartners für den Datenschutz als auch die Datenschutzerklärung Ihrer Organisation hinzuzufügen, damit Ihre internen Mitarbeiter und externen Gäste Ihre Richtlinien lesen können. Da Datenschutzerklärungen individuell erstellt werden und auf das jeweilige Unternehmen zugeschnitten sind, wird dringend empfohlen, zu diesem Sachverhalt einen Anwalt hinzuziehen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Hinzufügen Ihrer Datenschutzinformationen in Azure AD
Sie fügen die Datenschutzinformationen Ihrer Organisation im Bereich **Eigenschaften** von Azure AD hinzu.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>So greifen Sie auf den Bereich „Eigenschaften“ zu und fügen die Datenschutzinformationen hinzu

1. Melden Sie sich als Mandantenadministrator beim Azure-Portal an.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** und dann **Eigenschaften** aus.

    Der Bereich **Eigenschaften** wird angezeigt.

    ![Bereich „Eigenschaften“ in Azure AD mit hervorgehobenem Bereich „Datenschutzinformationen“](media/active-directory-properties-area/properties-area.png)

3. Fügen Sie Ihre Datenschutzinformationen für Ihre Mitarbeiter hinzu:

    - **Technischer Kontakt**: Geben Sie die E-Mail-Adresse der Person ein, die bei Anfragen zum technischen Support innerhalb Ihrer Organisation zu kontaktieren ist.
    
    - **Globaler Datenschutzkontakt**: Geben Sie die E-Mail-Adresse der Person ein, die bei Anfragen zum Schutz persönlicher Daten zu kontaktieren ist. Diese Person wird von Microsoft auch kontaktiert, wenn ein Verstoß gegen die Datenschutzbestimmungen vorliegt. Wenn keine Person hier aufgeführt wird, kontaktiert Microsoft Ihre globalen Administratoren.

    - **URL zur Datenschutzerklärung**: Geben Sie den Link zum Dokument Ihrer Organisation an, in dem beschrieben wird, wie Ihre Organisation die Daten von internen und externen Gästen schützt.

        >[!Important]
        >Wenn Sie Ihre Datenschutzerklärung oder Ihren Datenschutzkontakt nicht angeben, wird Ihren externen Gästen im Feld **Berechtigungen überprüfen** eine Meldung angezeigt, dass **<_Name Ihrer Organisation_> keine Links zu den Geschäftsbedingungen für Gäste angegeben hat**. Diese Meldung wird beispielsweise einem Gastbenutzer angezeigt, wenn er über die B2B-Zusammenarbeit eine Einladung für den Zugang zu einer Organisation erhält.

        ![Feld „Berechtigungen überprüfen“ in der B2B-Zusammenarbeit mit Meldung](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte
- [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../external-identities/redemption-experience.md)
- [Hinzufügen oder Ändern von Profilinformationen für einen Benutzer in Azure Active Directory](active-directory-users-profile-azure-portal.md)