---
title: Hinzufügen einzelner lizenzierter Benutzer zur gruppenbasierten Lizenzierung in Azure Active Directory | Microsoft-Dokumentation
description: Informationen zum Migrieren einzelner Benutzerlizenzen zur gruppenbasierten Lizenzierung mithilfe von Azure Active Directory
services: active-directory
keywords: Azure AD-Lizenzierung
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333f0ae0153073b57740446ecf47e36a1f9ce590
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192451"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Gewusst wie: Migrieren von Benutzern mit einzelnen Lizenzen zur gruppenbasierten Lizenzierung

Sie haben möglicherweise einzelnen Benutzern in den Abteilungen bereits mit „direkte Zuweisung“ Lizenzen zugewiesen, z.B. mithilfe von PowerShell-Skripts oder anderen Tools. Bevor Sie mit der gruppenbasierten Lizenzierung zur Verwaltung von Lizenzen in Ihrer Organisation beginnen, können Sie mit diesem Migrationsplan bestehende Lösungen nahtlos durch gruppenbasierte Lizenzen ersetzen.

Der wichtigste zu beachtende Aspekt ist, dass Sie unbedingt eine Situation vermeiden müssen, in der die gruppenbasierte Lizenzierung dazu führt, dass Benutzer ihre derzeit zugewiesenen Lizenzen vorübergehend nicht nutzen können. Jeder Vorgang, der zum Entfernen von Lizenzen führt, muss vermieden werden, damit kein Risiko besteht, dass Benutzer keinen Zugriff mehr auf Dienste und ihre Daten haben.

## <a name="recommended-migration-process"></a>Empfohlener Migrationsprozess

1. Sie haben (z.B. mit PowerShell) die Verwaltung der Zuweisung und Entziehung von Lizenzen für Benutzer automatisiert. Lassen Sie diese Einrichtung unverändert.

2. Erstellen Sie eine neue Lizenzierungsgruppe (oder entscheiden Sie, welche vorhandene Gruppen genutzt werden soll), und stellen Sie sicher, dass alle erforderlichen Benutzer als Mitglieder hinzugefügt werden.

3. Weisen Sie die benötigten Lizenzen diesen Gruppen zu. Ziel muss es sein, denselben Lizenzierungszustand zu erreichen, den die bestehende Automatisierungslösung (z.B. PowerShell) diesen Benutzern zuweist.

4. Stellen Sie sicher, dass Lizenzen allen Benutzer in diesen Gruppen zugewiesen wurden. Überprüfen Sie hierzu den Verarbeitungsstatus der einzelnen Gruppen oder die Überwachungsprotokolle.

   - Sie können einzelne Benutzer stichprobenartig überprüfen, indem Sie sich ihre Lizenzdetails ansehen. Sie erkennen dann, ob ihnen dieselben Lizenzen entweder direkt oder über Gruppen zugewiesen wurden.

   - Sie können ein PowerShell-Skript ausführen, um zu [überprüfen, wie Benutzern Lizenzen zugewiesen sind](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Wenn die gleichen Produktlizenz dem Benutzer direkt und über eine Gruppe zugewiesen ist, wird vom Benutzer nur eine Lizenz genutzt. Daher sind keine zusätzlichen Lizenzen erforderlich, um die Migration auszuführen.

5. Vergewissern Sie sich, dass Lizenzzuweisungen nicht misslungen sind, indem Sie jede Gruppe auf Benutzer mit dem Status „Fehler“ überprüfen. Weitere Informationen finden Sie unter [Bestimmen und Beheben von Lizenzproblemen für eine Gruppe](licensing-groups-resolve-problems.md).

6. Erwägen Sie die Aufhebung direkter Zuweisungen. Dies kann gestaffelt, also nach und nach, erfolgen, um zunächst das Ergebnis für eine Teilmenge von Benutzern zu prüfen.

   Sie können die ursprünglichen direkten Zuweisungen für Benutzer beibehalten. Wenn die Benutzer jedoch ihre lizenzierten Gruppen verlassen, behalten sie die ursprüngliche Lizenz, was Sie möglicherweise nicht möchten.

## <a name="an-example"></a>Beispiel

Eine Organisation verfügt über 1.000 Benutzer. Alle Benutzer benötigen EMS-Lizenzen (Enterprise Mobility + Security). 200 Benutzer in der Finanzabteilung benötigen Office 365 Enterprise E3-Lizenzen. Derzeit verfügt die Organisation über ein lokal ausgeführtes PowerShell-Skript, mit dem Benutzern den Anforderungen entsprechend Lizenzen zugewiesen und entzogen werden. Die Organisation möchte das Skript aber durch die gruppenbasierte Lizenzierung ersetzen, damit die Lizenzen automatisch mit Azure AD verwaltet werden können.

Der Migrationsvorgang kann wie folgt aussehen:

1. Weisen Sie im Azure-Portal in Azure AD die EMS-Lizenz der Gruppe **Alle Benutzer** zu. Weisen Sie die E3-Lizenz der Gruppe **Finanzabteilung** zu, die alle erforderlichen Benutzer enthält.

2. Vergewissern Sie sich für jede Gruppe, dass die Lizenzzuweisung für alle Benutzer abgeschlossen wurde. Wechseln Sie zum Blatt jeder Gruppe, wählen Sie **Lizenzen** aus, und überprüfen Sie oben im Blatt **Lizenzen** den Verarbeitungsstatus.

   - Suchen Sie nach „Latest license changes have been applied to all users“ (Die letzten Lizenzänderungen wurden von allen Benutzern übernommen), um zu bestätigen, dass die Verarbeitung abgeschlossen ist.

   - Suchen Sie oben nach einer Benachrichtigung zu Benutzern, denen Lizenzen möglicherweise nicht erfolgreich zugewiesen wurden. Haben wir für einige Benutzer nicht genügend Lizenzen? Verfügen einige Benutzer über in Konflikt stehende Lizenz-SKUs, die verhindern, dass diese Gruppenlizenzen erben können?

3. Überprüfen Sie stichprobenartig einige Benutzer, um zu prüfen, ob ihnen sowohl Lizenzen direkt als auch per Gruppe zugewiesen wurden. Wechseln Sie zum Blatt eines Benutzers, wählen Sie **Lizenzen** aus, und untersuchen Sie den Status der Lizenzen.

   - Dies ist der erwartete Benutzerzustand während der Migration:

      ![der erwartete Benutzerzustand während der Migration](./media/licensing-groups-migrate-users/expected-user-state.png)

   Dies bestätigt, dass der Benutzer sowohl über direkt zugewiesene als auch über von der Gruppe übernommene Lizenzen verfügt. Wir sehen, dass sowohl **EMS** als auch **E3** zugewiesen sind.

   - Wählen Sie die einzelnen Lizenzen aus, um Details zu den aktivierten Diensten einzublenden. Dies dient zum Überprüfen, ob die direkt oder per Gruppe zugewiesenen Lizenzen exakt dieselben Servicepläne für den Benutzer aktivieren.

      ![Überprüfen von Serviceplänen für den Benutzer](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Nachdem Sie sichergestellt haben, dass sowohl die direkt als auch die per Gruppe zugewiesenen Lizenzen äquivalent sind, können Sie beginnen, den Benutzern direkt zugewiesene Lizenzen zu entziehen. Sie können dies testen, indem Sie sie für einzelne Benutzer im Portal entziehen und anschließend Automatisierungsskripts ausführen, um sie in einem Massenvorgang zu entziehen. Hier ist ein Beispiel des gleichen Benutzers, dem die direkt zugewiesenen Lizenzen mithilfe des Portals entzogen werden. Beachten Sie, dass der Lizenzstatus unverändert bleibt, aber keine direkten Zuweisungen mehr zu sehen sind.

   ![Sicherstellen, dass direkte Lizenzen entfernt wurden](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu anderen Szenarien für die Lizenzverwaltung über Gruppen finden Sie in folgenden Artikeln.

* [Was ist die gruppenbasierte Lizenzierung in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Zuweisen von Lizenzen zu einer Gruppe in Azure Active Directory](licensing-groups-assign.md)
* [Bestimmen und Beheben von Lizenzproblemen für eine Gruppe in Azure Active Directory](licensing-groups-resolve-problems.md)
* [Sicheres Migrieren von Benutzern zwischen Produktlizenzen mithilfe von gruppenbasierter Lizenzierung in Azure Active Directory](licensing-groups-change-licenses.md)
* [Gruppenbasierte Azure Active Directory-Lizenzierung – zusätzliche Szenarien](licensing-group-advanced.md)
* [PowerShell-Beispiele für die gruppenbasierte Lizenzierung in Azure AD](licensing-ps-examples.md)
