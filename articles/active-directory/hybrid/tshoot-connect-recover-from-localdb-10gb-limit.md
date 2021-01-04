---
title: 'Azure AD Connect: Wiederherstellen nach einem Problem aufgrund der LocalDB-Obergrenze von 10 GB | Microsoft-Dokumentation'
description: In diesem Thema wird beschrieben, wie Sie Azure AD Connect Synchronization Service wiederherstellen, wenn ein Problem aufgrund der LocalDB-Obergrenze von 10 GB auftritt.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b45decd2f2cf9c99cffb0e08d4d6a5c5cfafc67
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858398"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Wiederherstellen der LocalDB-Obergrenze von 10 GB
Azure AD Connect erfordert eine SQL Server-Datenbank zum Speichern von Identitätsdaten. Sie können entweder die mit Azure AD Connect installierte SQL Server 2012 Express LocalDB-Standardinstanz oder Ihre eigene vollständige Version von SQL Server verwenden. In SQL Server Express gilt eine Größenbeschränkung von 10 GB. Wenn Sie LocalDB verwenden und dieser Grenzwert erreicht ist, kann Azure AD Connect Synchronization Service nicht mehr ordnungsgemäß gestartet oder synchronisiert werden. In diesem Artikel werden die Schritte zur Wiederherstellung beschrieben.

## <a name="symptoms"></a>Symptome
Es gibt zwei häufige Symptome:

* Azure AD Connect Synchronization Service **wird ausgeführt**, bei der Synchronisierung tritt jedoch der Fehler *stopped-database-disk-full* auf.

* Azure AD Connect Synchronization Service **kann nicht gestartet werden**. Wenn Sie versuchen, den Dienst zu starten, tritt ein Fehler mit dem Ereignis 6323 auf, und die Fehlermeldung *Serverfehler, da für SQL Server nicht ausreichend Speicherplatz vorhanden ist.* wird angezeigt.

## <a name="short-term-recovery-steps"></a>Schritte zur kurzfristigen Wiederherstellung
Dieser Abschnitt enthält die Schritte zum Freigeben von Datenbankspeicherplatz, der für Azure AD Connect Synchronization Service zum Fortsetzen des Vorgangs erforderlich ist. Dazu müssen folgende Schritte ausgeführt werden:
1. [Ermitteln des Synchronization Service-Status](#determine-the-synchronization-service-status)
2. [Verkleinern der Datenbank](#shrink-the-database)
3. [Löschen der Ausführungsverlaufsdaten](#delete-run-history-data)
4. [Verkürzen des Aufbewahrungszeitraums für Ausführungsverlaufsdaten](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Ermitteln des Synchronization Service-Status
Bestimmen Sie zunächst, ob Synchronization Service noch ausgeführt wird:

1. Melden Sie sich bei Ihrem Azure AD Connect-Server als Administrator an.

2. Navigieren Sie zu **Dienststeuerungs-Manager**.

3. Überprüfen Sie den Status von **Microsoft Azure AD Sync**.


4. Wird der Dienst ausgeführt, dürfen Sie ihn weder beenden noch neu starten. Überspringen Sie den Schritt [Verkleinern der Datenbank](#shrink-the-database), und fahren Sie mit dem Schritt [Löschen der Ausführungsverlaufsdaten](#delete-run-history-data) fort.

5. Wird der Dienst nicht ausgeführt, versuchen Sie, ihn neu zu starten. Wird der Dienst gestartet, überspringen Sie den Schritt [Verkleinern der Datenbank](#shrink-the-database), und fahren Sie mit dem Schritt [Löschen der Ausführungsverlaufsdaten](#delete-run-history-data) fort. Andernfalls führen Sie den Schritt zum [Verkleinern der Datenbank](#shrink-the-database) aus.

### <a name="shrink-the-database"></a>Verkleinern der Datenbank
Verwenden Sie den Verkleinerungsvorgang, um genügend Datenbankspeicherplatz zum Starten von Synchronization Service freizugeben. Datenbankspeicherplatz wird durch Entfernen von Leerzeichen in der Datenbank freigegeben. Dieser Schritt ist eine Empfehlung, es kann jedoch nicht garantiert werden, dass immer Speicherplatz freigegeben werden kann. Weitere Informationen zum Verkleinerungsvorgang finden Sie im Artikel [Verkleinern einer Datenbank](/sql/relational-databases/databases/shrink-a-database).

> [!IMPORTANT]
> Überspringen Sie diesen Schritt, wenn Sie Synchronization Service ausführen können. Es wird nicht empfohlen, die SQL-Datenbank zu verkleinern, da dies aufgrund erhöhter Fragmentierung zu Leistungseinbußen führen kann.

Der Name der für Azure AD Connect erstellten Datenbank lautet **ADSync**. Um eine Verkleinerung vorzunehmen, müssen Sie sich entweder als Systemadministrator der Datenbank oder als Datenbankbesitzer anmelden. Während der Installation von Azure AD Connect werden den folgenden Konten Systemadministratorrechte gewährt:
* Lokalen Administratoren
* Dem Benutzerkonto, das zum Ausführen der Azure AD Connect-Installation verwendet wurde
* Dem Sync Service-Konto, das als Betriebskontext von Azure AD Connect Synchronization Service verwendet wird
* Der lokalen Gruppe „ADSyncAdmins“, die während der Installation erstellt wurde

1. Sichern Sie die Datenbank, indem Sie die Dateien **ADSync.mdf** und **ADSync_log.ldf** unter `%ProgramFiles%\Microsoft Azure AD Sync\Data` an einen sicheren Speicherort kopieren.

2. Starten Sie eine neue PowerShell-Sitzung.

3. Navigieren Sie zum Ordner `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Starten Sie das Hilfsprogramm **sqlcmd** mithilfe des Befehls `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`. Verwenden Sie dabei die Anmeldeinformationen eines Systemadministrators oder des Datenbankbesitzers.

5. Geben Sie zum Verkleinern der Datenbank an der sqlcmd-Eingabeaufforderung (1>) `DBCC Shrinkdatabase(ADSync,1);` gefolgt von `GO` in der nächsten Zeile ein.

6. Wenn der Vorgang erfolgreich ist, versuchen Sie erneut, Synchronization Service zu starten. Wenn Synchronization Service gestartet werden kann, fahren Sie mit dem Schritt [Löschen der Ausführungsverlaufsdaten](#delete-run-history-data) fort. Kontaktieren Sie andernfalls den Support.

### <a name="delete-run-history-data"></a>Löschen der Ausführungsverlaufsdaten
Standardmäßig werden in Azure AD Connect Ausführungsverlaufsdaten der letzten sieben Tage aufbewahrt. In diesem Schritt werden die Ausführungsverlaufsdaten gelöscht, um Datenbankspeicherplatz freizugeben, damit Azure AD Connect Synchronization Service wieder mit der Synchronisierung beginnen kann.

1. Starten Sie **Synchronization Service Manager**, indem Sie zu „START“ > „Synchronization Service“ navigieren.

2. Rufen Sie die Registerkarte **Vorgänge** auf.

3. Klicken Sie unter **Aktionen** auf **Clear Runs** (Ausführungen löschen).

4. Sie können entweder **Clear all runs** (Alle Ausführungen löschen) oder **Clear runs before… \<date>** (Ausführungen löschen vor...) auswählen. Es wird empfohlen, zunächst Ausführungsverlaufsdaten zu löschen, die älter als zwei Tage sind. Falls weiterhin Probleme aufgrund der Datenbankgröße auftreten, wählen Sie die Option **Clear all runs** (Alle Ausführungen löschen).

### <a name="shorten-retention-period-for-run-history-data"></a>Verkürzen des Aufbewahrungszeitraums für Ausführungsverlaufsdaten
Mit diesem Schritt soll die Wahrscheinlichkeit reduziert werden, dass nach mehreren Synchronisierungszyklen ein Problem aufgrund der Obergrenze von 10 GB auftritt.

1. Öffnen Sie eine neue PowerShell-Sitzung.

2. Führen Sie `Get-ADSyncScheduler` aus, und notieren Sie sich die PurgeRunHistoryInterval-Eigenschaft, die den aktuellen Aufbewahrungszeitraum angibt.

3. Führen Sie `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` aus, um den Aufbewahrungszeitraum auf zwei Tage festzulegen. Passen Sie den Aufbewahrungszeitraum nach Bedarf an.

## <a name="long-term-solution--migrate-to-full-sql"></a>Langfristige Lösung – Migrieren zur vollständigen SQL Server-Version
Im Allgemeinen weist das Problem darauf hin, dass eine Datenbankgröße von 10 GB nicht mehr zur Synchronisierung Ihrer lokalen Active Directory-Instanz mit Azure AD durch Azure AD Connect ausreicht. Es wird empfohlen, eine vollständige Version von SQL Server zu verwenden. Die LocalDB-Instanz einer vorhandenen Azure AD Connect-Bereitstellung kann nicht direkt durch die Datenbank der vollständigen SQL Server-Version ersetzt werden. Stattdessen müssen Sie einen neuen Azure AD Connect-Server mit der vollständigen Version von SQL Server bereitstellen. Es wird empfohlen, eine Swing-Migration auszuführen, bei der der neue Azure AD Connect-Server (mit SQL-Datenbank) als Stagingserver zusätzlich zum vorhandenen Azure AD Connect-Server (mit LocalDB) bereitgestellt wird. 
* Anweisungen zum Konfigurieren einer SQL Server-Remoteinstanz mit Azure AD Connect finden Sie im Artikel [Benutzerdefinierte Installation von Azure AD Connect](./how-to-connect-install-custom.md).
* Eine Anleitung zur Swing-Migration für ein Azure AD Connect-Upgrade finden Sie im Artikel [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](./how-to-upgrade-previous-version.md#swing-migration).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
