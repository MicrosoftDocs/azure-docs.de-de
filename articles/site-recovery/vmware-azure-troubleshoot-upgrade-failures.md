---
title: Behandlung von Problemen beim Upgrade von Microsoft Azure Site Recovery-Anbietern | Microsoft-Dokumentation
description: Grundlegende Informationen und
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: fc50be2a960784895947f3f154a0251f41716fc7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565700"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Behandlung von Problemen beim Upgrade von Microsoft Azure Site Recovery-Anbietern

Dieser Artikel hilft Ihnen, Probleme zu beheben, die zu Fehlern während eines Microsoft Azure Site Recovery-Anbieterupgrades führen können.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Das Upgrade schlägt fehl, wenn gemeldet wird, dass der neueste Site Recovery-Anbieter bereits installiert ist.

Beim Aktualisieren des Microsoft Azure Site Recovery-Anbieters (DRA) schlägt das Upgrade des einheitlichen Setups fehl und gibt die folgende Fehlermeldung aus:

Das Upgrade wird nicht unterstützt, da bereits eine höhere Version der Software installiert ist.

Gehen Sie folgendermaßen vor, um das Upgrade durchzuführen:

1. Laden Sie das einheitliche Setup von Microsoft Azure Site Recovery herunter:
   1. Wählen Sie im Abschnitt „Links zu aktuell unterstützten Updaterollups“ des Artikels [Dienstupdates in Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) den Anbieter aus, auf den Sie aktualisieren möchten.
   2. Suchen Sie auf der Rollupseite den Abschnitt **Updateinformationen**, und laden Sie das Updaterollup für das einheitliche Setup von Microsoft Azure Site Recovery herunter.

2. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Ordner, in den Sie die Datei für das einheitliche Setup heruntergeladen haben. Extrahieren Sie die Setupdateien aus dem Download mit dem folgenden Befehl: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;Verzeichnispfad für die extrahierten Dateien&gt;.
    
    Beispielbefehl:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. Navigieren Sie in der Eingabeaufforderung zu dem Ordner, in den Sie die Dateien extrahiert haben, und führen Sie die folgenden Installationsbefehle aus:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Kehren Sie zu dem Ordner zurück, in den Sie das einheitliche Setup heruntergeladen haben, und führen Sie „MicrosoftAzureSiteRecoveryUnifiedSetup.exe“ aus, um das Upgrade abzuschließen. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Upgradefehler aufgrund der Umbenennung des Drittanbieterordners

Damit das Upgrade erfolgreich ist, darf der Drittanbieterordner nicht umbenannt werden.

So lösen Sie das Problem:

1. Starten Sie den Registrierungs-Editor (regedit.exe), und öffnen Sie den Zweig HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Untersuchen Sie den `Build_Version`-Schlüsselwert. Wenn es auf die neueste Version festgelegt ist, reduzieren Sie die Versionsnummer. Wenn beispielsweise die neueste Version 9.22.\* ist und der Schlüssel `Build_Version` auf diesen Wert festgelegt ist, reduzieren Sie ihn auf 9.21.\*.
1. Laden Sie das neueste einheitliche Setup für Microsoft Azure Site Recovery herunter:
   1. Wählen Sie im Abschnitt „Links zu aktuell unterstützten Updaterollups“ des Artikels [Dienstupdates in Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) den Anbieter aus, auf den Sie aktualisieren möchten.
   2. Suchen Sie auf der Rollupseite den Abschnitt **Updateinformationen**, und laden Sie das Updaterollup für das einheitliche Setup für Microsoft Azure Site Recovery herunter.
1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Ordner, in den Sie die Datei für das einheitliche Setup heruntergeladen haben. Anschließend extrahieren Sie die Setupdateien aus dem Download mit dem folgenden Befehl: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;Verzeichnispfad für die extrahierten Dateien&gt;.

    Beispielbefehl:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. Navigieren Sie in der Eingabeaufforderung zu dem Ordner, in den Sie die Dateien extrahiert haben, und führen Sie die folgenden Installationsbefehle aus:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Verwenden Sie den Task-Manager, um den Fortschritt der Installation zu überwachen. Wenn der Prozess für CX_THIRDPARTY_SETUP.EXE im Task-Manager nicht mehr sichtbar ist, fahren Sie mit dem nächsten Schritt fort.
1. Stellen Sie sicher, dass „C:\thirdparty“ existiert und dass der Ordner die RRD-Bibliotheken enthält.
1. Kehren Sie zu dem Ordner zurück, in den Sie das einheitliche Setup heruntergeladen haben, und führen Sie „MicrosoftAzureSiteRecoveryUnifiedSetup.exe“ aus, um das Upgrade abzuschließen. 