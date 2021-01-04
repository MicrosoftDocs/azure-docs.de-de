---
title: Schließen eines Geschäfts-, Schul- oder Unikontos in einer nicht verwalteten Azure AD-Organisation
description: Es wird beschrieben, wie Sie Ihr Geschäfts-, Schul- oder Unikonto in einer nicht verwalteten Azure Active Directory-Instanz schließen.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/03/2020
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 252c993f5679d80cda97a6fade08f5f92489840d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575583"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>Schließen Ihres Geschäfts-, Schul- oder Unikontos in einer nicht verwalteten Azure AD-Organisation

Wenn Sie ein Benutzer in einer Organisation mit einer nicht verwalteten Azure AD-Instanz (Azure Active Directory) sind und Sie die Apps dieser Organisation nicht mehr benötigen bzw. keine Verbindung damit mehr aufrechterhalten müssen, können Sie Ihr Konto jederzeit schließen. Eine nicht verwaltete Organisation verfügt nicht über einen Unternehmensadministrator. Benutzer mit einer nicht verwalteten Organisation können ihre Konten selbst schließen, ohne sich an einen Administrator wenden zu müssen.

Benutzer, die einer nicht verwalteten Organisation zugeordnet sind, werden häufig im Rahmen der Self-Service-Registrierung erstellt. Ein Beispiel hierfür ist ein Information-Worker in einer Organisation, der sich für einen kostenlosen Dienst registriert. Weitere Informationen zur Self-Service-Registrierung finden Sie unter [Was ist die Self-Service-Registrierung für Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie Ihr Konto schließen, sollten Sie Folgendes sicherstellen:

* Vergewissern Sie sich, dass Sie ein Benutzer einer nicht verwalteten Azure AD-Organisation sind. Sie können Ihr Konto nicht schließen, wenn Sie einer verwalteten Organisation zugeordnet sind. Falls eine Zuordnung zu einer verwalteten Organisation besteht und Sie Ihr Konto schließen möchten, müssen Sie sich an Ihren Administrator wenden. Informationen zur Ermittlung, ob Sie einer nicht verwalteten Organisation zugeordnet sind, finden Sie unter [Löschen des Benutzers aus einem nicht verwalteten Mandanten](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Speichern Sie alle Daten, die Sie beibehalten möchten. Informationen dazu, wie Sie eine Exportanforderung übermitteln, finden Sie unter [Accessing and exporting system-generated logs for Unmanaged Tenants](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants) (Zugreifen auf und Exportieren von Systemprotokollen für nicht verwaltete Mandanten).

> [!WARNING]
> Das Schließen Ihres Kontos kann nicht rückgängig gemacht werden. Wenn Sie Ihr Konto schließen, werden alle persönlichen Daten entfernt. Sie haben keinen Zugriff mehr auf Ihr Konto und die zugehörigen Daten.

## <a name="close-your-account"></a>Schließen Ihres Kontos

Führen Sie diese Schritte aus, um ein nicht verwaltetes Geschäfts-, Schul- oder Unikonto zu schließen:

1. Melden Sie sich an der [Seite für das Schließen Ihres Kontos](https://go.microsoft.com/fwlink/?linkid=873123) mit dem zu schließenden Konto an.

1. Wählen Sie unter **Meine Datenanforderungen**  die Option **Konto schließen**.

    ![Meine Datenanforderungen: Konto schließen](./media/users-close-account/close-account.png)

1. Sehen Sie sich die Bestätigungsmeldung an, und wählen Sie anschließend **Ja**.

    ![Meine Datenanforderungen: Schließen bestätigen](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Self-Service-Registrierung für Azure Active Directory?](directory-self-service-signup.md)
- [Löschen des Benutzers aus einem nicht verwalteten Mandanten](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Accessing and exporting system-generated logs for Unmanaged Tenants](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants) (Zugreifen auf und Exportieren von Systemprotokollen für nicht verwaltete Mandanten)