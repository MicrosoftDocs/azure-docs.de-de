---
title: Zuweisen von Lizenzen zu einer Gruppe – Azure Active Directory | Microsoft-Dokumentation
description: Zuweisen von Lizenzen zu Benutzern mit der Gruppenlizenzierung von Azure Active Directory
services: active-directory
keywords: Azure AD-Lizenzierung
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c1a6957d347681f1c7dd9979429fffb153ccb39
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546606"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Zuweisen von Lizenzen zu Benutzer nach Gruppenmitgliedschaft in Azure Active Directory

Dieser Artikel führt Sie durch die Zuweisung von Produktlizenzen zu einer Benutzergruppe und die Überprüfung, ob die Lizenzierung in Azure Active Directory (Azure AD) ordnungsgemäß durchgeführt wurde.

In diesem Beispiel enthält die Azure AD-Organisation eine Sicherheitsgruppe namens **HR Department** (Personalabteilung). Diese Gruppe enthält alle Mitglieder der Personalabteilung (etwa 1.000 Benutzer). Sie möchten der gesamten Abteilung Office 365 Enterprise E3-Lizenzen zuweisen. Der in dem Produkt enthaltene Yammer Enterprise-Dienst muss vorübergehend deaktiviert werden, bis die Abteilung für dessen Verwendung bereit ist. Sie möchten außerdem Enterprise Mobility + Security-Lizenzen für die gleiche Benutzergruppe bereitstellen.

> [!NOTE]
> Einige Microsoft-Dienste sind nicht an allen Standorten verfügbar. Bevor einem Benutzer eine Lizenz zugewiesen werden kann, muss der Administrator die Eigenschaft „Verwendungsstandort“ für den Benutzer angeben.
>
> Bei der Gruppenlizenzzuweisung erben alle Benutzer ohne Verwendungsstandort den Standort des Verzeichnisses. Wenn Benutzer an mehreren Standorten vorhanden sind, empfiehlt es sich, den Verwendungsstandort immer im Rahmen des Benutzererstellungsablaufs in Azure AD (z.B. über die AAD Connect-Konfiguration) festzulegen. Dadurch wird sichergestellt, dass das Ergebnis der Lizenzzuweisung immer korrekt ist und die Benutzer keine Dienste für Standorte empfangen, die nicht zugelassen sind.

## <a name="step-1-assign-the-required-licenses"></a>Schritt 1: Zuweisen der erforderlichen Lizenzen

1. Melden Sie sich mit einem Lizenzadministratorkonto beim [**Azure AD Admin Center**](https://aad.portal.azure.com) an. Zum Verwalten von Lizenzen muss das Konto ein Lizenzadministrator, Benutzeradministrator oder globaler Administrator sein.

1. Wählen Sie **Lizenzen** aus, um eine Seite zu öffnen, auf der Sie alle lizenzierbaren Produkte in der Organisation anzeigen und verwalten können.

1. Wählen Sie unter **Alle Produkte** sowohl Office 365 Enterprise E5 als auch Enterprise Mobility + Security E3 aus, indem Sie die Produktnamen auswählen. Wählen Sie oben auf der Seite **Zuweisen** aus, um die Zuweisung zu starten.

   ![Auswählen von Produkten zum Zuweisen von Lizenzen](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Wählen Sie auf der Seite **Lizenz zuweisen** die Option **Benutzer und Gruppen** aus, um eine Liste der Benutzer und Gruppen zu öffnen.

1. Wählen Sie einen Benutzer oder eine Gruppe aus, und bestätigen Sie Ihre Auswahl dann oben auf der Seite über die Schaltfläche **Auswählen**.

1. Klicken Sie auf der Seite **Lizenz zuweisen** auf **Zuweisungsoptionen**. Daraufhin werden alle Dienstpläne angezeigt, die in den beiden zuvor ausgewählten Produkten enthalten sind. Wechseln Sie zu **Yammer Enterprise**, und legen Sie das Produkt auf **Aus** fest, um den Dienst in der Produktlizenz zu deaktivieren. Bestätigen Sie den Vorgang, indem Sie unten in **Lizenzoptionen** auf **OK** klicken.

   ![Auswählen von Dienstplänen für Lizenzen](./media/licensing-groups-assign/assignment-options.png)
  
1. Klicken Sie schließlich unten auf der Seite **Lizenz zuweisen** auf **Zuweisen**, um die Zuweisung abzuschließen.

1. Rechts oben wird eine Benachrichtigung mit dem Status und Ergebnis des Vorgangs angezeigt. Falls die Gruppenzuweisung nicht abgeschlossen werden konnte (beispielsweise aufgrund bereits vorhandener Lizenzen für die Gruppe), klicken Sie auf die Benachrichtigung, um Details zum Fehler anzuzeigen.

Beim Zuweisen von Lizenzen zu einer Gruppe werden in Azure AD alle vorhandenen Mitglieder dieser Gruppe verarbeitet. Je nach Größe der Gruppe kann dieser Vorgang einige Zeit dauern. Der nächste Schritt beschreibt, wie Sie überprüfen, ob der Vorgang abgeschlossen wurde und ob weitere Maßnahmen zur Problembehebung erforderlich sind.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Schritt 2: Überprüfen, ob die anfängliche Zuweisung erfolgt ist

1. Wechseln Sie zu **Azure Active Directory** > **Gruppen**. Wählen Sie die Gruppe aus, der Lizenzen zugewiesen wurden.

1. Wählen Sie auf der Seite der Gruppe die Option **Lizenzen** aus. Dadurch können Sie schnell überprüfen, ob die Lizenzen den Benutzern vollständig zugewiesen wurden und ob Fehler vorliegen, die untersucht werden müssen. Folgende Informationen stehen zur Verfügung:

   - Dienstlizenzen, die der Gruppe aktuell zugewiesen sind. Wählen Sie einen Eintrag aus, um die bestimmten Dienste anzuzeigen, die aktiviert wurden, und um Änderungen vorzunehmen.

   - Statusaktualisierungen der letzten Lizenzänderungen, die verfügbar sind, wenn die Änderungen verarbeitet werden oder wenn die Verarbeitung für alle Benutzermitglieder abgeschlossen ist.

   - Informationen zu Benutzerlizenzzuweisungen in einem Fehlerzustand.

   ![Lizenzierungsfehler und Lizenzstatus](./media/licensing-groups-assign/assignment-errors.png)

1. Ausführlichere Informationen zur Lizenzverarbeitung finden Sie unter **Azure Active Directory** > **Benutzer und Gruppen** > *Gruppenname* > **Überwachungsprotokolle**. Überprüfen Sie folgende Aktivitäten:

   - Aktivität: `Start applying group based license to users`. Wird protokolliert, wenn das System die Änderung der Lizenzzuweisung für die Gruppe verarbeitet und beginnt, diese für alle Benutzermitglieder zu übernehmen. Das Protokoll enthält Informationen über die erfolgte Änderung.

   - Aktivität: `Finish applying group based license to users`. Wird protokolliert, wenn das System alle Benutzer in der Gruppe verarbeitet hat. Das Protokoll enthält eine Übersicht über die Anzahl erfolgreich verarbeiteter Benutzer und über die Anzahl von Benutzern, denen keine Gruppenlizenzen zugewiesen werden konnten.

   [Lesen Sie diesen Abschnitt](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity), um mehr darüber zu erfahren, wie Überwachungsprotokolle dazu verwendet werden können, um durch die gruppenbasierte Lizenzierung vorgenommene Änderungen zu analysieren.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Schritt 3: Suchen nach Lizenzproblemen und Beheben der Probleme

1. Wechseln Sie zu **Azure Active Directory** > **Gruppen**, und suchen Sie die Gruppe, der Lizenzen zugewiesen wurden.
1. Wählen Sie auf der Seite der Gruppe die Option **Lizenzen** aus. Die Benachrichtigung oben auf der Seite zeigt, dass zehn Benutzern keine Lizenzen zugewiesen werden konnten. Öffnen Sie die Benachrichtigung, um für diese Gruppe eine Liste aller Benutzer mit dem Lizenzierungsstatus „Fehler“ anzuzeigen.
1. In der Spalte **Fehlerhafte Zuweisungen** ist zu sehen, dass den Benutzern beide Produktlizenzen nicht zugewiesen werden konnten. Die Spalte **Top reason for failure**(Häufigste Fehlerursache) enthält die Ursache des Fehlers. In diesem Fall: **Widersprüchliche Servicepläne**.

   ![Lizenzen, die nicht zugewiesen werden konnten](./media/licensing-groups-assign/failed-assignments.png)

1. Wählen Sie einen Benutzer aus, um die Seite **Lizenzen** des Benutzers zu öffnen. Auf dieser Seite werden alle Lizenzen angezeigt, die dem Benutzer derzeit zugewiesen sind. In diesem Beispiel hat der Benutzer die Office 365 Enterprise E1-Lizenz von der Gruppe **Kiosk users** (Kioskbenutzer) geerbt. Dies steht in Konflikt mit der E3-Lizenz, die über die Gruppe **HR Department** angewendet werden sollte. Daher wurde dem Benutzer keine der Lizenzen aus dieser Gruppe zugewiesen.

   ![Anzeigen aller Lizenzkonflikte für einen Benutzer](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Um dieses Problem zu beheben, entfernen Sie den Benutzer aus der Gruppe **Kiosk users** (Kioskbenutzer). Nachdem Azure AD die Änderung verarbeitet hat, sind die Lizenzen für **HR Department** richtig zugewiesen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Features für die Lizenzzuweisung mithilfe von Gruppen finden Sie in den folgenden Artikeln:

- [Was ist die gruppenbasierte Lizenzierung in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Bestimmen und Beheben von Lizenzproblemen für eine Gruppe in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Migrieren einzelner lizenzierter Benutzer zur gruppenbasierten Lizenzierung in Azure Active Directory](licensing-groups-migrate-users.md)
- [Sicheres Migrieren von Benutzern zwischen Produktlizenzen mithilfe von gruppenbasierter Lizenzierung in Azure Active Directory](licensing-groups-change-licenses.md)
- [Gruppenbasierte Azure Active Directory-Lizenzierung – zusätzliche Szenarien](licensing-group-advanced.md)
- [PowerShell-Beispiele für die gruppenbasierte Lizenzierung in Azure AD](licensing-ps-examples.md)