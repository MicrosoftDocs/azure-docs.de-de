---
title: Verwalten des Gastzugriffs mit Zugriffsüberprüfungen – Azure AD
description: Verwalten von Gastbenutzern als Mitglieder einer Gruppe oder einer Anwendung zugewiesene Benutzer mit Azure Active Directory-Zugriffsüberprüfungen
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb9bc252c733f3445cbf8b27281c2f455d5f2f36
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696964"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen


Mit Azure Active Directory (Azure AD) können Sie problemlos die Zusammenarbeit über Organisationsgrenzen hinweg ermöglichen, indem Sie das Feature [Azure AD B2B](../external-identities/what-is-b2b.md) verwenden. Gastbenutzer von anderen Mandanten können von [Administratoren](../external-identities/add-users-administrator.md) oder [anderen Benutzern eingeladen](../external-identities/what-is-b2b.md) werden. Dies gilt auch für soziale Identitäten wie z.B. Microsoft-Konten.

Sie können auch auf einfache Weise sicherstellen, dass Gastbenutzer über entsprechenden Zugriff verfügen. Hierzu können Sie die Gäste selbst oder einen Entscheidungsträger bitten, an einer Zugriffsüberprüfung teilzunehmen und den Zugriff des Gasts erneut zu zertifizieren (oder zu „bescheinigen“). Basierend auf Vorschlägen von Azure AD können die Prüfer die Notwendigkeit des weiteren Zugriffs der einzelnen Benutzer abwägen. Nach Abschluss einer Zugriffsüberprüfung können Sie dann Änderungen vornehmen und Zugriffsrechte für Gäste entfernen, die diese nicht mehr benötigen.

> [!NOTE]
> Dieses Dokument beschäftigt sich mit der Überprüfung des Zugriffs von Gastbenutzern. Wenn Sie den Zugriff aller Benutzer überprüfen möchten (und nicht nur den von Gästen), lesen Sie [Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen](manage-user-access-with-access-reviews.md). Wenn Sie die Benutzermitgliedschaft in Administratorrollen wie „Globaler Administrator“ überprüfen möchten, finden Sie entsprechende Informationen unter [Starten einer Zugriffsüberprüfung in Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P2

Weitere Informationen finden Sie unter [Lizenzanforderungen](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Erstellen und Durchführen einer Zugriffsüberprüfung für Gäste

Ihnen muss eine der folgenden Rollen zugewiesen sein:
- Globaler Administrator
- Benutzeradministrator
- (Vorschau) M365- oder AAD-Sicherheitsgruppenbesitzer der zu überprüfenden Gruppe

Navigieren Sie zur [Seite „Identity Governance“](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), und vergewissern Sie sich, dass Ihre Organisation Zugriffsüberprüfungen verwenden kann.

Azure AD ermöglicht mehrere Szenarien für die Überprüfung von Gastbenutzern.

Sie können Folgendes überprüfen:

 - Eine Gruppe in Azure AD mit mindestens einem Gast als Mitglied
 - Eine mit Azure AD verbundene Anwendung, der mindestens ein Gastbenutzer zugewiesen ist 

Sie können dann entscheiden, ob Sie jeden Gast bitten, seinen eigenen Zugriff zu überprüfen, oder ob Sie einen oder mehrere Benutzer bitten, den Zugriff von jedem Gast zu überprüfen.

 Diese Szenarien werden in den folgenden Abschnitten behandelt.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Auffordern von Gästen zur Überprüfung ihrer eigenen Mitgliedschaft in einer Gruppe

Mithilfe von Zugriffsüberprüfungen können Sie sicherstellen, dass Benutzer, die eingeladen und einer Gruppe hinzugefügt wurden, weiterhin Zugriff benötigen. Sie können Gäste ganz einfach darum bitten, ihre eigene Mitgliedschaft in dieser Gruppe zu überprüfen.

1. Um eine Zugriffsüberprüfung für die Gruppe zu erstellen, geben Sie an, dass die Überprüfung nur Gastbenutzermitglieder umfasst und die Mitglieder sich selbst überprüfen. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).

2. Bitten Sie jeden Gast, seine eigene Mitgliedschaft zu überprüfen. Standardmäßig erhält jeder Gast, der eine Einladung angenommen hat, eine E-Mail von Azure AD mit einem Link zu einer Zugriffsüberprüfung. Azure AD stellt Anweisungen für Gastbenutzer bereit, mit denen sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](perform-access-review.md) können.

3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).

4. Zusätzlich zu den Benutzern, die angegeben haben, dass sie keinen weiteren Zugriff benötigen, können Sie auch Benutzer entfernen, die nicht reagiert haben. Benutzer, die nicht reagieren, erhalten möglicherweise keine E-Mails mehr.

5. Wenn die Gruppe nicht für die Zugriffsverwaltung verwendet wird, können Sie auch Benutzer entfernen, die nicht ausgewählt wurden, an der Überprüfung teilzunehmen, weil sie ihre Einladung nicht angenommen haben. Ein solches Versäumnis kann darauf hindeuten, dass die E-Mail-Adresse des eingeladenen Benutzers einen Tippfehler enthielt. Wenn eine Gruppe als Verteilerliste verwendet wird, wurden einige Gastbenutzer vermutlich nicht für eine Teilnahme ausgewählt, weil es sich bei ihnen um Kontaktobjekte handelt.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Auffordern eines Sponsors zum Überprüfen der Mitgliedschaft eines Gasts in einer Gruppe

Sie können einen Sponsor (z.B. den Besitzer einer Gruppe) bitten, die Notwendigkeit einer weitergehenden Mitgliedschaft eines Gasts in einer Gruppe zu überprüfen.

1. Um eine Zugriffsüberprüfung für die Gruppe zu erstellen, geben Sie an, dass die Überprüfung nur Gastbenutzermitglieder umfasst. Geben Sie dann mindestens einen Prüfer an. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).

2. Bitten Sie die Prüfer, ihre Einschätzung abzugeben. Standardmäßig erhalten alle Prüfer eine E-Mail von Azure AD mit einem Link zu dem Zugriffsbereich, in dem sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](perform-access-review.md).

3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Auffordern von Gästen zum Überprüfen ihres eigenen Zugriffs auf eine Anwendung

Mithilfe von Zugriffsüberprüfungen können Sie sicherstellen, dass Benutzer, die für eine bestimmte Anwendung eingeladen wurden, weiterhin Zugriff benötigen. Sie können die Gäste ganz einfach bitten, ihren eigenen Zugriffsbedarf selbst zu überprüfen.

1. Um eine Zugriffsüberprüfung für die Anwendung zu erstellen, geben Sie an, dass die Überprüfung nur Gäste umfasst und die Benutzer ihren eigenen Zugriff überprüfen. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).

2. Bitten Sie Gäste, ihren eigenen Zugriff auf die Anwendung zu überprüfen. Standardmäßig erhält jeder Gast, der eine Einladung angenommen hat, eine E-Mail von Azure AD. Diese E-Mail enthält einen Link zu der Zugriffsüberprüfung im Zugriffsbereich Ihrer Organisation. Azure AD stellt Anweisungen für Gastbenutzer bereit, mit denen sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](perform-access-review.md) können.

3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).

4. Zusätzlich zu den Benutzern, die angegeben haben, dass sie keinen weiteren Zugriff benötigen, können Sie auch Gastbenutzer entfernen, die nicht reagiert haben. Benutzer, die nicht reagieren, erhalten möglicherweise keine E-Mails mehr. Sie können auch Gastbenutzer entfernen, die nicht für eine Teilnahme ausgewählt wurden, insbesondere, wenn diese in letzter Zeit nicht eingeladen wurden. Diese Benutzer haben ihre Einladung nicht angenommen und hatten daher keinen Zugriff auf die Anwendung. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Auffordern eines Sponsors zum Überprüfen des Zugriffs eines Gasts auf eine Anwendung

Sie können einen Sponsor – z.B. den Besitzer einer Anwendung – bitten, den Bedarf eines Gasts für den weiteren Zugriff auf die Anwendung zu überprüfen.

1. Um eine Zugriffsüberprüfung für die Anwendung zu erstellen, geben Sie an, dass die Überprüfung nur Gäste umfasst. Geben Sie dann mindestens einen Benutzer als Prüfer an. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).

2. Bitten Sie die Prüfer, ihre Einschätzung abzugeben. Standardmäßig erhalten alle Prüfer eine E-Mail von Azure AD mit einem Link zu dem Zugriffsbereich, in dem sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](perform-access-review.md).

3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Auffordern von Gästen zur Überprüfung ihres allgemeinen Zugriffsbedarfs

In einigen Organisationen sind Gästen ihre Gruppenmitgliedschaften möglicherweise nicht bekannt.

> [!NOTE]
> In früheren Versionen des Azure-Portals war ein Administratorzugriff durch Benutzer mit dem UserType-Wert „Guest“ nicht zulässig. In einigen Fällen hat ein Administrator in Ihrem Verzeichnis möglicherweise den UserType-Wert eines Gasts mithilfe von PowerShell in „Member“ geändert. Wenn diese Änderung zuvor in Ihrem Verzeichnis vorgenommen wurde, enthält die vorherige Abfrage möglicherweise nicht alle Gastbenutzer, die in der Vergangenheit über Administratorrechte verfügten. In diesem Fall müssen Sie entweder den UserType-Wert des Gasts ändern oder den Gast manuell in die Gruppenmitgliedschaft aufnehmen.

1. Erstellen Sie eine Sicherheitsgruppe mit den Gästen als Mitgliedern in Azure AD, wenn noch keine geeignete Gruppe vorhanden ist. Sie können z.B. eine Gruppe mit einer manuell verwalteten Mitgliedschaft von Gästen erstellen. Oder Sie können eine dynamische Gruppe mit einem Namen wie „Gäste von Contoso“ für Benutzer im Contoso-Mandanten erstellen, deren UserType-Attributwert „Guest“ lautet.  Stellen Sie aus Gründen der Effizienz sicher, dass die Gruppe vorwiegend aus Gästen besteht. Wählen Sie keine Gruppe mit Mitgliedsbenutzern aus, da Mitgliedsbenutzer nicht überprüft werden müssen.  Beachten Sie außerdem, dass einem Gastbenutzer, der ein Mitglied der Gruppe ist, die anderen Mitglieder der Gruppe angezeigt werden.

2. Um eine Zugriffsüberprüfung für diese Gruppe zu erstellen, geben Sie an, dass die Prüfer selbst Mitglieder sind. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).

3. Bitten Sie jeden Gast, seine eigene Mitgliedschaft zu überprüfen. Standardmäßig erhält jeder Gast, der eine Einladung angenommen hat, eine E-Mail von Azure AD mit einem Link zu der Zugriffsüberprüfung im Zugriffsbereich Ihrer Organisation. Azure AD stellt Anweisungen für Gastbenutzer bereit, mit denen sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](perform-access-review.md) können.  Gäste, die ihre Einladung nicht angenommen haben, werden in den Überprüfungsergebnissen als „nicht benachrichtigt“ angezeigt.

4. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).

5. Entfernen Sie den Gastzugriff für Gäste, die abgelehnt wurden, die Überprüfung nicht abgeschlossen haben oder ihre Einladung zuvor nicht angenommen haben. Wenn einige der Gäste Kontakte sind, die für die Teilnahme an der Überprüfung ausgewählt wurden oder die eine Einladung zuvor nicht angenommen haben, können Sie deren Konten über das Azure-Portal oder mit PowerShell deaktivieren. Wenn ein Gast keinen Zugriff mehr benötigt und kein Kontakt ist, können Sie das zugehörige Benutzerobjekt aus dem Verzeichnis entfernen, indem Sie das Gastbenutzerobjekt über das Azure-Portal oder mit PowerShell löschen.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md)