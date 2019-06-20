---
title: Migrieren von Benutzern zu Produktlizenzen mithilfe von Gruppen – Azure Active Directory | Microsoft-Dokumentation
description: Beschreibt die empfohlene Vorgehensweise beim Migrieren von Benutzern innerhalb einer Gruppe zu verschiedenen Produktlizenzen (Office 365 Enterprise E1 und E3) mithilfe von gruppenbasierter Lizenzierung
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
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b65eb38b6c8102295f40b5e169ae7c32a2342a2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60471136"
---
# <a name="change-the-license-for-a-single-user-in-a-licensed-group-in-azure-active-directory"></a>Ändern der Lizenz für einen einzelnen Benutzer in einer lizenzierten Gruppe in Azure Active Directory

Dieser Artikel beschreibt die empfohlene Methode zum Verschieben von Benutzern zwischen Produktlizenzen bei Verwendung von gruppenbasierter Lizenzierung. Das Ziel dieses Ansatzes besteht darin, sicherzustellen, dass die Migration ohne Dienst- und Datenverlust abläuft – für einen nahtlosen Benutzerwechsel zwischen den Produkten. Zwei Varianten des Migrationsprozesses werden behandelt:

- Einfache Migration zwischen Produktlizenzen, die keine widersprüchlichen Servicepläne enthalten, z.B eine Migration zwischen Office 365 Enterprise E3 und Office 365 Enterprise E5.

- Komplexere Migration zwischen Produktlizenzen, die widersprüchlichen Servicepläne enthalten, z.B eine Migration zwischen Office 365 Enterprise E1 und Office 365 Enterprise E3. Weitere Informationen zu Konflikten finden Sie unter [In Konflikt stehende Diensteeinstellungen](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans) und [Servicepläne, die nicht zur selben Zeit zugewiesen werden können](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-product-and-service-plan-reference#service-plans-that-cannot-be-assigned-at-the-same-time).

Dieser Artikel enthält PowerShell-Beispielcode, der zum Ausführen der Migrations- und Überprüfungsschritte verwendet werden kann. Der Code besonders für umfangreiche Vorgänge nützlich, in denen eine manuelle Ausführung der Schritte nicht möglich ist.

## <a name="before-you-begin"></a>Voraussetzungen
Vor dem Beginn der Migration ist es wichtig, sicherzustellen, dass bestimmte Annahmen für alle zu migrierenden Benutzer zutreffen. Wenn die Annahmen nicht für alle Benutzer zutreffen, kann die Migration für einige davon fehlschlagen. Infolgedessen könnten einige der Benutzer den Zugriff auf Dienste oder Daten verlieren. Die folgenden Annahmen müssen überprüft werden:

- Benutzer verfügen über die *Quelllizenz*, die mithilfe der gruppenbasierten Lizenzierung zugewiesen wird. Die Lizenzen für das Produkt, aus dem die Benutzer verschoben werden sollen, werden aus einer einzigen Quellgruppe geerbt und nicht direkt zugewiesen.

    >[!NOTE]
    >Wenn Lizenzen auch direkt zugewiesen sind, verhindern sie möglicherweise die Anwendung der *Ziellizenz*. Erfahren Sie mehr über [direkte und gruppenbasierte Lizenzzuweisung](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-advanced#direct-licenses-coexist-with-group-licenses). Es empfiehlt sich, mithilfe eines [PowerShell-Skripts](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-ps-examples#check-if-user-license-is-assigned-directly-or-inherited-from-a-group) zu prüfen, ob Benutzer über direkte Lizenzen verfügen.

- Sie haben genügend verfügbare Lizenzen für das Zielprodukt. Wenn Sie nicht über genügend Lizenzen verfügen, erhalten einige Benutzer möglicherweise die *Ziellizenz* nicht. Sie können [die Anzahl der verfügbaren Lizenzen überprüfen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products).

- Benutzern sind keine anderen Lizenzen zugewiesen, die u.U. mit der *Ziellizenz* in Konflikt stehen oder die Entfernung der *Quelllizenz* verhindern. Beispiel: Eine Lizenz für ein Add-On-Produkt wie Workplace Analytics oder Project Online, die von einem anderen Produkt abhängig ist.

- Sie sind darüber informiert, wie Gruppen in Ihrer Umgebung verwaltet werden. Wenn Sie beispielsweise Gruppen lokal verwalten und über Azure AD Connect in Azure Active Directory (Azure AD) synchronisieren, erfolgt das Hinzufügen/Entfernen von Benutzern in Ihrem lokalen System. Das Synchronisieren der Änderungen in Azure AD und ihre Erfassung durch die gruppenbasierte Lizenzierung dauert eine Zeit. Bei Verwendung von dynamischen Gruppenmitgliedschaften in Azure AD erfolgt das Hinzufügen/Entfernen von Benutzern stattdessen durch das Ändern ihrer Attribute. Der allgemeine Migrationsvorgang bleibt jedoch gleich. Der einzige Unterschied besteht beim Hinzufügen/Entfernen von Benutzern für die Gruppenmitgliedschaft.

## <a name="migrate-users-between-products-that-dont-have-conflicting-service-plans"></a>Migrieren von Benutzern zwischen Produkten ohne widersprüchliche Servicepläne

Das Migrationsziel ist es, mithilfe der gruppenbasierten Lizenzierung die Benutzerlizenzen von einer *Quelllizenz* (in diesem Beispiel: Office 365 Enterprise E3) in eine *Ziellizenz* (in diesem Beispiel: Office 365 Enterprise E5). Die beiden Produkte in diesem Szenario enthalten keine widersprüchlichen Servicepläne, sodass sie vollständig und ohne Konflikte gleichzeitig zugewiesen werden können. Während der Migration sollten Benutzer zu keinem Zeitpunkt die Zugriffsmöglichkeit für Dienste oder Daten verlieren. Die Migration erfolgt in kleinen „Batches“. Sie können das Ergebnis für jeden Batch überprüfen und die Anzahl möglicher Probleme einschränken, die während des Prozesses auftreten können. Im Ganzen verläuft der Prozess dann wie folgt:

1.  Benutzer sind Mitglieder einer Quellgruppe und erben die *Quelllizenz* aus dieser Gruppe.

2.  Erstellen Sie eine Zielgruppe mit der *Ziellizenz* jedoch ohne Mitglieder.

3.  Fügen Sie der Zielgruppe einen Batch von Benutzern hinzu. Die gruppenbasierte Lizenzierung übernimmt die Änderung und weist die *Ziellizenz* zu. Dieser Prozess kann je nach Größe des Batches und anderen Aktivitäten im Mandanten längere Zeit dauern.

4.  Stellen Sie sicher, dass der Batch von Benutzern von vollständig von der gruppenbasierten Lizenzierung verarbeitet wird. Vergewissern Sie sich, dass jedem Benutzer die *Ziellizenz* zugewiesen ist. Stellen Sie sicher, dass bei den Benutzern keine Fehler aufgetreten sind, z.B. durch Konflikte mit anderen Produkten oder eine zu geringe Anzahl von Lizenzen. Weitere Informationen zu Fehlern finden Sie unter [Beheben von Lizenzzuweisungsproblemen für eine Gruppe in Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal).

5.  Nun sind den Benutzern sowohl die *Quell-* als auch die *Ziellizenzen* zugewiesen.

6.  Entfernen Sie den gleichen Benutzerbatch aus der Quellgruppe. Die gruppenbasierte Lizenzierung reagiert auf die Änderung und die *Quelllizenzen* werden von den Benutzern entfernt.

7.  Wiederholen Sie den Prozess für nachfolgende Benutzerbatches.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Migrieren eines einzelnen Benutzer mit dem Azure-Portal

Dies ist eine einfache exemplarische Vorgehensweise für die Migration eines einzelnen Benutzers.

**SCHRITT 1**: Der Benutzer hat eine *Quelllizenz* aus der Gruppe geerbt. Es sind keine direkten Zuweisungen für die Lizenz vorhanden:

![Benutzer mit einer aus der Gruppe geerbten Quelllizenz](./media/licensing-groups-change-licenses/UserWithSourceLicenseInherited.png)

**SCHRITT 2**: Der Benutzer wird der Zielgruppe hinzugefügt, und die gruppenbasierte Lizenzierung verarbeitet die Änderung. Der Benutzer verfügt nun sowohl über die *Quell-* als auch die *Ziellizenz*, die von Gruppen geerbt werden:

![Benutzer mit einer aus Gruppen geerbten Quell- und Ziellizenz](./media/licensing-groups-change-licenses/UserWithBothSourceAndTargetLicense.png)

**SCHRITT 3**: Der Benutzer wird aus der Quellgruppe entfernt, und die gruppenbasierte Lizenzierung verarbeitet die Änderung. Der Benutzer hat jetzt nur die *Ziellizenz*:

![Benutzer mit einer aus der Gruppe geerbten Ziellizenz](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssigned.png)

### <a name="automate-migration-by-using-azure-powershell"></a>Automatisieren der Migration mithilfe von Azure PowerShell
Der folgende Codeausschnitt veranschaulicht, wie der Migrationsprozess bei größerem Umfang automatisiert werden kann.

> [!NOTE]
> Der Beispielcode verwendet PowerShell-Funktionen, die im [letzten Abschnitt](#powershell-automation-of-migration-and-verification-steps) dieses Artikels enthalten sind.

```powershell
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com','MigrationUser2@tailspinonline.com'

############### NON-CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:ENTERPRISEPACK'      # <-- This is the Office 365 Enterprise E3 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPREMIUM'   # <-- This is the Office 365 Enterprise E5 product.

if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will assign the target license $targetSkuId to all users"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceandTargetLicensePresent} 'STEP 2: Checking if all users still have the source license and now also have the target license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**Beispielausgabe (Migration von zwei Benutzern)**

```powershell
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPREMIUM licenses available (13) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.

STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will assign the target license TailspinOnline:ENTERPRISEPREMIUM to all users
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:ENTERPRISEPACK.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

## <a name="migrate-users-between-products-that-have-conflicting-service-plans"></a>Migrieren von Benutzern zwischen Produkten mit widersprüchlichen Serviceplänen
Das Migrationsziel ist es, mithilfe der gruppenbasierten Lizenzierung die Benutzerlizenzen von einer *Quelllizenz* (in diesem Beispiel: Office 365 Enterprise E1) in eine *Ziellizenz* (in diesem Beispiel: Office 365 Enterprise E3). Die beiden Produkte in diesem Szenario enthalten widersprüchliche Servicepläne, sodass eine Problemumgehung gefunden werden muss, um eine nahtlose Benutzermigration zu ermöglichen. Weitere Informationen zu diesen Konflikten finden Sie unter [Beheben von Lizenzzuweisungsproblemen für eine Gruppe in Active Directory: In Konflikt stehende Diensteeinstellungen](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans). Während der Migration sollten Benutzer zu keinem Zeitpunkt die Zugriffsmöglichkeit für Dienste oder Daten verlieren. Die Migration erfolgt in kleinen „Batches“. Sie können das Ergebnis für jeden Batch überprüfen und die Anzahl möglicher Probleme einschränken, die während des Prozesses auftreten können. Im Ganzen verläuft der Prozess dann wie folgt:

1.  Benutzer sind Mitglieder einer Quellgruppe und erben die *Quelllizenz* aus dieser Gruppe.

2.  Erstellen Sie eine Zielgruppe mit der *Ziellizenz* jedoch ohne Mitglieder.

3.  Fügen Sie der Zielgruppe einen Batch von Benutzern hinzu. Die gruppenbasierte Lizenzierung übernimmt die Änderung und versucht, die *Ziellizenz* zuzuweisen. Die Zuweisung schlägt aufgrund von Konflikten zwischen Diensten in den beiden Produkten fehl. Die gruppenbasierte Lizenzierung zeichnet das Fehlschlagen für jeden Benutzer als Fehler auf. Dieser Prozess kann je nach Größe des Batches und anderen Aktivitäten im Mandanten längere Zeit dauern.

4.  Stellen Sie sicher, dass der Batch von Benutzern von vollständig von der gruppenbasierten Lizenzierung verarbeitet wird. Vergewissern Sie sich, dass der Konfliktfehler für jeden Benutzer aufgezeichnet wurde. Stellen Sie sicher, dass für keinen Benutzer ein unerwarteter Fehler aufgetreten ist. Weitere Informationen zu Fehlern finden Sie unter [Beheben von Lizenzzuweisungsproblemen für eine Gruppe in Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal).

5.  An diesem Punkt ist den Benutzern weiterhin die *Quelllizenz* zugewiesen und es liegt ein Fehler aufgrund eines Konflikts für die *Ziellizenz* vor. Die *Ziellizenz* ist den Benutzern also noch nicht zugewiesen.

6.  Entfernen Sie den gleichen Benutzerbatch aus der Quellgruppe. Die gruppenbasierte Lizenzierung reagiert auf die Änderung und die *Quelllizenz* wird von den einzelnen Benutzern entfernt. Der Konfliktfehler wird zur gleichen Zeit entfernt (sofern keine andere Produktlizenz den Fehler mitverursacht), und die *Ziellizenz* wird zugewiesen. So wird sichergestellt, dass während des Übergangs keine Dienste oder Daten verloren gehen.

7.  Wiederholen Sie den Prozess für nachfolgende Benutzerbatches.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Migrieren eines einzelnen Benutzer mit dem Azure-Portal
Dies ist eine einfache exemplarische Vorgehensweise für die Migration eines einzelnen Benutzers.

**SCHRITT 1**: Der Benutzer hat eine *Quelllizenz* aus der Gruppe geerbt. Es sind keine direkten Zuweisungen für die Lizenz vorhanden:

![Benutzer mit einer aus der Gruppe geerbten Quelllizenz](./media/licensing-groups-change-licenses/UserWithSourceLicenseInheritedConflictScenario.png)

**SCHRITT 2**: Der Benutzer wird der Zielgruppe hinzugefügt, und die gruppenbasierte Lizenzierung verarbeitet die Änderung. Da der Benutzer immer noch über die *Quelllizenz* verfügt, befindet sich die *Ziellizenz* aufgrund des Konflikts in einem Fehlerzustand:

![Benutzer mit einer von der Gruppe geerbten Quelllizenz und der Ziellizenz in einem Fehlerzustand](./media/licensing-groups-change-licenses/UserWithSourceLicenseAndTargetLicenseInConflict.png)

**SCHRITT 3**: Der Benutzer wird aus der Quellgruppe entfernt, und die gruppenbasierte Lizenzierung verarbeitet die Änderung. Die *Ziellizenz* wird auf den Benutzer angewendet:

![Benutzer mit einer aus der Gruppe geerbten Ziellizenz](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssignedConflictScenario.png)


### <a name="automate-migration-by-using-azure-powershell"></a>Automatisieren der Migration mithilfe von Azure PowerShell
Der folgende Codeausschnitt veranschaulicht, wie der Migrationsprozess bei größerem Umfang automatisiert werden kann.

> [!NOTE]
> Der Beispielcode verwendet PowerShell-Funktionen, die im [letzten Abschnitt](#powershell-automation-of-migration-and-verification-steps) dieses Artikels enthalten sind.

```powershell
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**Beispielausgabe (Migration von zwei Benutzern)**

```powershell
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPACK licenses available (61) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.
STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will put target license TailspinOnline:ENTERPRISEPACK in conflict state with the source license TailspinOnline:STANDARDPACK
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:STANDARDPACK and remove the conflict on target license TailspinOnline:ENTERPRISEPACK which will become assigned.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

<h2 id="powershell-automation-of-migration-and-verification-steps">Ausführen von PowerShell-Code zum Automatisieren und Überprüfen der Migration</h2>

Dieser Abschnitt enthält den erforderlichen PowerShell-Code zum Ausführen der in diesem Artikel beschriebenen Skripts.

>[!WARNING]
>Dieser Code dient als Beispiel zu Demonstrationszwecken. Wenn Sie ihn in Ihrer Umgebung verwenden möchten, sollten Sie den Code zunächst in kleinerem Umfang oder in einem separaten Testmandanten testen. Passen Sie den Code hierzu ggf. an die spezifischen Anforderungen Ihrer Umgebung an.

Folgen Sie zum Ausführen des Codes den Anweisungen in den [Bibliotheken für Azure AD PowerShell v1.0](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0). Führen Sie vor dem Ausführen des Skripts das Cmdlet `connect-msolservice` aus, um sich beim Mandanten anzumelden.

```powershell
# BEGIN: Helper functions that are used in the scripts.

# GetUserObject function
# Retrieve a user object based on the ObjectId or UserPrincipalName.
function GetUserObject
{
    [OutputType([Microsoft.Online.Administration.User])]
    Param([object]$userId)

    $userIdType = $userId.GetType()

    if($userIdType -eq [Guid])
    {
        return Get-MsolUser -ObjectId $userId
    }
    elseif($userIdType -eq [string])
    {
        return Get-MsolUser -UserPrincipalName $userId
    }
    else
    {
        throw "User Id type must be a Guid or a string (UserPrincipalName). The user id type was: $($userIdType.Name)"
    }
}

# GetGuidUserId function
# Get a Guid objectId for a user, even when a UserPrincipal string is passed in.
# Guid ids are needed for group membership manipulation, where UPNs cannot be used.
function GetGuidUserId
{
    [OutputType([Guid])]
    Param([object]$userId)

    [Guid]$guidId = [Guid]::Empty
    if($userId.GetType() -eq [String])
    {
        $user = GetUserObject $userId
        return $user.ObjectId
    }
    elseif($userId.GetType() -eq [Guid])
    {
        return $userId
    }
    else
    {
        throw "UserId type must be a Guid or a string (UserPrincipalName). The user id type was: $($userId.GetType().Name)"
    }
}

# AddUsersToGroup function
# Add a collection of users to a group.
# Note: This function fails if a user is already a member of the specified group.
function AddUsersToGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Adding user $userId to group $groupId"
        Add-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# RemoveUsersFromGroup function
# Remove a collection of users from a group.
# Note: This function fails if a user is not a member of the specified group.
function RemoveUsersFromGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Removing user $userId from group $groupId"
        Remove-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# GetUserLicense function
# Return the license object that corresponds to the skuId.
# Return NULL if no object is found.
function GetUserLicense
{
    [OutputType([Microsoft.Online.Administration.UserLicense])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    # Look for the specific license SKU in all of the licenses that are assigned to the user.
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

# IsLicenseAssignedToUser function
# Check if the specific SKU license is assigned to the user,
#    regardless of how the license is assigned (directly or via GBL).
function IsLicenseAssignedToUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [string]$skuId)

    $license = GetUserLicense $user $skuId

    return ($license -ne $null)
}

# GetObjectIdsAssigningLicense function
function GetObjectIdsAssigningLicense
{
    [OutputType([Guid[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        return [Guid[]]$license.GroupsAssigningLicense
    }
    return [Array]::CreateInstance([Guid],0)
}

# UserHasLicenseAssignedFromThisGroup function
# Return TRUE if the user inherits the license from the specific group.
# Note: This function returns true only if the license is successfully assigned from the group.
#       If the license is in an error state, the function return false.
function UserHasLicenseAssignedFromThisGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    [Guid[]]$objectsAssigningLicense = GetObjectIdsAssigningLicense $user $skuId

    # GroupsAssigningLicense contains a collection of object IDs for assigning the license.
    # This could be a group object or a user object (contrary to what the name suggests).
    foreach ($assignmentSource in $objectsAssigningLicense)
    {
        # If the collection contains at least one ID that doesn't match the user ID, the license is inherited from a group.
        # Note: The license might also be assigned directly, in addition to being inherited.
        if ($assignmentSource -ieq $groupId)
        {
            return $true
        }
    }
    return $false
}

# GetErrorsForLicense function
# Return error objects for a specific license.
function GetErrorsForLicense
{
    [OutputType([Microsoft.Online.Administration.IndirectLicenseError[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    [Microsoft.Online.Administration.IndirectLicenseError[]] $errorObjects = $user.IndirectLicenseErrors | Where {"$($_.AccountSku.AccountName):$($_.AccountSku.SkuPartNumber)" -ieq $skuId}

    if($errorObjects -eq $null)
    {
        #there are no errors at all
        return [Array]::CreateInstance([Microsoft.Online.Administration.IndirectLicenseError],0)
    }

    return $errorObjects
}

# GetErrorForLicenseFromGroup function
# Return an error label that's associated with a specific license that's inherited from a specific group.
# Return $null if there's no error.
function GetErrorForLicenseFromGroup
{
    [OutputType([string])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId)


    # There are some errors. Check if any of the errors are associated with the group.
    foreach($licenseError in GetErrorsForLicense $user $skuId)
    {
        if($licenseError.ReferencedObjectId -eq $groupId)
        {
            return $licenseError.Error
        }
    }
    return $null
}

# IsExpectedLicenseStateForGroup function
# Check if the license is in an expected state for a given group.
# If expectedError is set to a value, the function checks if the license is in the specific error state for the group.
# If expectedError is NULL, the function checks if the license is successfully assigned from the group.
function IsExpectedLicenseStateForGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId, [string]$expectedError)

    # The license is expected to be fully assigned from the group and not in an error state.
    if([string]::IsNullOrEmpty($expectedError))
    {
        # Check if the assigned license is inherited from the expected group and without an error on it.
        return (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
    }
    # The license is expected to be in the specific error state on the specific group.
    else
    {
        $error = GetErrorForLicenseFromGroup $user $groupId $skuId
        return ($error -ieq $expectedError)
    }
}

# VerifySourceLicensePresentAndTargetLicenseInConflictState function
# Detect if the licenses are in a specific state where the source license is assigned, but the target license is in a conflict state.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       The conflict state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceLicensePresentAndTargetLicenseInConflictState
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if the user still has the source license. If not, abort the script because something is seriously wrong.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is in conflict, as expected.
    $conflictError = 'MutuallyExclusiveViolation'
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $conflictError)
}

# VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup function
# Detect if the licenses are in a specific state where the source license isn't present,
#    but the target license is correctly assigned.
# Note: If the source license is gone and the target license isn't present,
#       the function throws an exception to abort the script.
#       Something went wrong and the user may have lost access to services.
function VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if user has the source license completely removed, which is a prerequisite to the target license eventually kicking in.
    if(IsLicenseAssignedToUser $user $sourceSkuId)
    {
        return $false
    }

    # Check if the user has the target license at all. If not, abort the script because something is seriously wrong.
    if(-Not (IsLicenseAssignedToUser $user $targetSkuId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $targetSkuId assigned, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is assigned from the expected target group, and no longer in an error state.
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $null)
}

# VerifySourceandTargetLicensePresent function
# Detect if the licenses are in the specific state where the source license is assigned and the target license is also assigned.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       This state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceandTargetLicensePresent
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check user still has source license - if not, abort because something is seriously wrong
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is also assigned from the target group
    return (UserHasLicenseAssignedFromThisGroup $user $targetSkuId $targetGroupId)
}

# VerifyAssumptionsForUser function
# Verify basic assumptions that should be true for a user before we execute the migration process.
# The function prints details about the verification steps.
# Return TRUE if all of the assumptions are true.
function VerifyAssumptionsForUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    $userName = $user.UserPrincipalName
    # 1. The user has the source license assigned from the source group.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        Write-Host "$userName does not have source license $sourceSkuId assigned from source group $sourceGroupId."
        return $false
    }

    # 2. The user doesn't have the same source license assigned from another group at the same time,
    #    and the user doesn't have the source license assigned directly.
    [Guid[]]$otherObjectsAssigningLicense = GetObjectIdsAssigningLicense $user $sourceSkuId | Where {$_ -ne $sourceGroupId}
    foreach($otherObject in $otherObjectsAssigningLicense)
    {
        if($otherObject -eq $user.ObjectId)
        {
            Write-Host "$userName has source license assigned directly to the user."
        }
        else
        {
            Write-Host "$username has source license assigned from an additional unexpected group $otherObject."
        }
    }
    if($otherObjectsAssigningLicense -and $otherObjectsAssigningLicense.Count -gt 0)
    {
        return $false
    }

    # 3. The user doesn't have the target license assigned.
    if(IsLicenseAssignedToUser $user $targetSkuId)
    {
        Write-Host "$userName already has target license assigned."
        return $false
    }

    # 4. The user doesn't have the target license in an error state from some groups.
    [Microsoft.Online.Administration.IndirectLicenseError[]]$licenseErrors = GetErrorsForLicense $user $targetSkuId
    foreach($licenseError in $licenseErrors)
    {
        Write-Host "$userName has target license in error state $($licenseError.Error) from unexpected group $($licenseError.ReferencedObjectId)."
    }
    if($licenseErrors -and $licenseErrors.Count -gt 0)
    {
        return $false
    }

    Write-Host "$userName`t`tOK"
    return $true
}

# VerifyAssumptions function
# Check if all of the users to be migrated are in a correct state.
function VerifyAssumptions
{
    [OutputType([bool])]
    Param([object[]]$userIds, [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    Write-Host "Verifying initial assumptions:"

    # Check if there are enough target licenses for all of the users.
    $skuState = Get-MsolAccountSku | Where {$_.AccountSkuId -ieq $targetSkuId}

    if($skuState -eq $null)
    {
        Write-Host "Target license SKU $targetSkuId does not exist in the tenant at all."
        return $false
    }

    $availableLicenses = $skuState.ActiveUnits - $skuState.ConsumedUnits

    if($userIds.Count -gt $availableLicenses)
    {
        Write-Host "Not enough licenses for all users. User count: $($userIds.Count), licenses available: $availableLicenses"
        return $false
    }
    else
    {
        Write-Host "Enough $targetSkuId licenses available ($availableLicenses) for users: $($userIds.Count)."
    }

    # Check if each user to be migrated is in an expected state.
    $usersOK = 0
    $usersNotOK = 0
    foreach($userId in $userIds)
    {
        $user = GetUserObject $userId
        if(VerifyAssumptionsForUser $user $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId)
        {
            ++$usersOK
        }
        else
        {
            ++$usersNotOK
        }
    }
    if($usersNotOK -gt 0)
    {
        Write-Host "Checks passed for $usersOK users, but failed for $usersNotOK users."
        return $false
    }
    Write-Host "Checks passed for all $usersOK users."
    return $true
}

# ExecuteVerificationLoop function
# Execute a verification function (passed in as a delegate by using $checkFunction) for each user.
# The function tracks how many users passed/failed verification.
# The function repeats the verification loop until all of the users have passed the check.
#   The loop may never terminate if some users never reach the expected state.
#   If the loop doesn't terminate, you should investigate to determine the cause.
# Note: If the verification function fails with an exception,
#       such as the function detects an unexpected user state,
#       the loop terminates and investigation into the user state is needed.
function ExecuteVerificationLoop
{
    Param([System.Management.Automation.ScriptBlock]$checkFunction, [string]$consoleMessage, [object[]]$userIds,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # How long to wait until the loop is retried.
    $sleepIntervalSeconds = 60
    $retryIteration = 1

    While($true)
    {
        Write-Host "`n$consoleMessage. Check iteration: $retryIteration`n"

        $usersInExpectedState = 0
        $usersNotYet = 0

        foreach ($userId in $userIds)
        {
            $user = GetUserObject $userId
            if($checkFunction.InvokeReturnAsIs($user, $sourceGroupId, $sourceSkuId, $targetGroupId, $targetSkuId))
            {
                Write-Host "$userId`t`tExpected state: YES"
                ++$usersInExpectedState
            }
            else
            {
                Write-Host "$userId`t`tExpected state: NO"
                ++$usersNotYet
            }
        }

        Write-Host "`nTotal users checked: $($userIds.Count). In expected state: $usersInExpectedState. Not yet: $usersNotYet"

        if($usersNotYet -eq 0)
        {
            Write-Host "Check passed for all users. Exiting check loop."
            return
        }

        ++$retryIteration
        Write-Host "Not all users are in expected state. Waiting $sleepIntervalSeconds seconds to try again."
        Start-Sleep -Seconds $sleepIntervalSeconds
    }
}
# END: Helper functions that are used in the script.

# BEGIN: Execute the script.

# Enable strict execution mode.
Set-StrictMode -Version latest
# Stop the script when the first exception is thrown.
$ErrorActionPreference = "Stop"

# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# END: Execute the script.
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Szenarien für die Lizenzverwaltung über Gruppen finden Sie in den folgenden Artikeln:

* [Zuweisen von Lizenzen zu einer Gruppe in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Bestimmen und Beheben von Lizenzproblemen für eine Gruppe in Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Migrieren einzelner lizenzierter Benutzer zur gruppenbasierten Lizenzierung in Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Gruppenbasierte Azure Active Directory-Lizenzierung – zusätzliche Szenarien](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell-Beispiele für die gruppenbasierte Lizenzierung in Azure AD](../users-groups-roles/licensing-ps-examples.md)
