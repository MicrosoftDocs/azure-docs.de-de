---
title: Konfigurieren und Genehmigen des Just-In-Time-Zugriffs für Azure Managed Applications
description: Beschreibt, wie Consumer von Azure Managed Applications Anforderungen für den Just-In-time-Zugriff auf eine verwaltete Anwendung genehmigen.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482036"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurieren und Genehmigen des Just-In-Time-Zugriffs für Azure Managed Applications

Als Consumer einer verwalteten Anwendung ist es für Sie möglicherweise unangenehm, dem Herausgeber dauerhaften Zugriff auf die Gruppe der verwalteten Ressourcen zu gewähren. Damit Sie eine umfassendere Kontrolle über die Erteilung des Zugriffs auf verwaltete Ressourcen haben, bietet Azure Managed Applications ein Feature namens Just-In-time-Zugriff (JIT), das sich derzeit in der Vorschauversion befindet. Sie können damit Zeitpunkt und Dauer des Zugriffs auf die Ressourcengruppe für den Herausgeber genehmigen. Der Herausgeber kann die erforderlichen Updates während dieser Zeit durchführen, aber nach Ablauf dieser Frist endet die Zugriffsberechtigung des Herausgebers.

Der Workflow für die Erteilung des Zugriffs ist:

1. Der Herausgeber fügt dem Marketplace eine verwaltete Anwendung hinzu und gibt an, dass der JIT-Zugriff verfügbar ist.

1. Während der Bereitstellung aktivieren Sie den JIT-Zugriff für Ihre Instanz der verwalteten Anwendung.

1. Nach der Bereitstellung können Sie die Einstellungen für den JIT-Zugriff ändern.

1. Der Herausgeber sendet eine Zugriffsanforderung.

1. Sie genehmigen die Anforderung.

Dieser Artikel konzentriert sich auf die Maßnahmen, die Consumer durchführen, um den JIT-Zugriff zu ermöglichen und Anforderungen zu genehmigen. Informationen zum Veröffentlichen einer verwalteten Anwendung mit JIT-Zugriff finden Sie unter [Anfordern des Just-In-Time-Zugriffs in Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Um den Just-In-time-Zugriff nutzen zu können, benötigen Sie eine [Azure Active Directory P2-Lizenz](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Aktivieren während der Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie einen Marketplace-Eintrag für eine verwaltete Anwendung mit aktiviertem JIT. Klicken Sie auf **Erstellen**.

1. Während Sie Werte für die neue verwaltete Anwendung bereitstellen, können Sie mit dem Schritt **JIT-Konfiguration** den JIT-Zugriff für die verwaltete Anwendung aktivieren oder deaktivieren. Wählen Sie **Ja** für **JIT-Zugriff aktivieren** aus. Diese Option ist standardmäßig für verwaltete Anwendungen aktiviert, die im Marketplace mit aktiviertem JIT definiert sind.

   ![Konfigurieren des Zugriffs](./media/approve-just-in-time-access/configure-jit-access.png)

   Sie können den JIT-Zugriff nur während der Bereitstellung aktivieren. Wenn Sie **Nein** auswählen, erhält der Herausgeber permanenten Zugriff auf die verwaltete Ressourcengruppe. Sie können den JIT-Zugriff später nicht mehr aktivieren.

1. Um die Standardgenehmigungseinstellungen zu ändern, wählen Sie **JIT-Konfiguration anpassen** aus.

   ![Anpassen des Zugriffs](./media/approve-just-in-time-access/customize-jit-access.png)

   Standardmäßig weist eine verwaltete Anwendung mit aktiviertem JIT die folgenden Einstellungen auf:

   * Genehmigungsmodus – automatisch
   * Maximale Zugriffsdauer – 8 Stunden
   * Genehmigende Personen – Keine

   Wenn der Genehmigungsmodus auf **automatisch** festgelegt ist, erhalten die genehmigenden Personen eine Benachrichtigung für alle Anforderungen, aber die Anforderung wird automatisch genehmigt. Wenn diese Option auf **manuell** festgelegt ist, erhalten die genehmigenden Personen eine Benachrichtigung für die einzelnen Anforderungen, und eine dieser Personen muss diese jeweils genehmigen.

   Die maximale Aktivierungsdauer gibt die maximale Zeitspanne an, die ein Herausgeber für den Zugriff auf die verwaltete Ressourcengruppe anfordern kann.

   Die Liste der genehmigenden Personen sind die Azure Active Directory-Benutzer, die JIT-Zugriffsanforderungen genehmigen können. Um eine genehmigende Person hinzuzufügen, wählen Sie **Genehmigende Person hinzufügen** aus, und suchen Sie nach dem Benutzer.

   Wählen Sie nach dem Aktualisieren der Einstellung **Speichern** aus.

## <a name="update-after-deployment"></a>Aktualisieren nach der Bereitstellung

Sie können die Werte für die Genehmigung von Anforderungen ändern. Wenn Sie jedoch den JIT-Zugriff während der Bereitstellung nicht aktiviert haben, können Sie ihn später nicht aktivieren.

So ändern Sie die Einstellungen für eine bereitgestellte verwaltete Anwendung

1. Wählen Sie im Portal die verwaltete Anwendung aus.

1. Wählen Sie **JIT-Konfiguration** aus, und ändern Sie die Einstellungen nach Bedarf.

   ![Ändern der Zugriffseinstellungen](./media/approve-just-in-time-access/change-settings.png)

1. Wählen Sie dann die Option **Schließen**.

## <a name="approve-requests"></a>Genehmigen von Anforderungen

Wenn der Herausgeber den Zugriff anfordert, werden Sie über die Anforderung informiert. Sie können JIT-Zugriffsanforderungen entweder direkt über die verwaltete Anwendung oder über alle verwalteten Anwendungen hinweg über den Azure AD Privileged Identity Management-Dienst genehmigen. Um den Just-In-time-Zugriff nutzen zu können, benötigen Sie eine [Azure Active Directory P2-Lizenz](../active-directory/privileged-identity-management/subscription-requirements.md).

So genehmigen Sie Anforderungen über die verwaltete Anwendung

1. Wählen Sie **JIT-Zugriff** für die verwaltete Anwendung und dann **Anforderungen genehmigen** aus.

   ![Genehmigen von Anforderungen](./media/approve-just-in-time-access/approve-requests.png)
 
1. Wählen Sie die zu genehmigende Anforderung aus.

   ![Auswählen der Anforderung](./media/approve-just-in-time-access/select-request.png)

1. Geben Sie im Formular den Grund für die Genehmigung an, und wählen Sie **Genehmigen** aus.

So genehmigen Sie Anforderungen über Azure AD Privileged Identity Management

1. Wählen Sie **Alle Dienste** aus, und suchen Sie nach **Azure AD Privileged Identity Management**. Wählen Sie es aus den verfügbaren Optionen aus.

   ![Suchen nach dem Dienst](./media/approve-just-in-time-access/search.png)

1. Wählen Sie **Anforderungen genehmigen** aus.

   ![Auswählen der Genehmigung von Anforderungen](./media/approve-just-in-time-access/select-approve-requests.png)

1. Wählen Sie **Azure Managed Applications** und dann die zu genehmigende Anforderung aus.

   ![Auswählen von Anforderungen](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Veröffentlichen einer verwalteten Anwendung mit JIT-Zugriff finden Sie unter [Anfordern des Just-In-Time-Zugriffs in Azure Managed Applications](request-just-in-time-access.md).
