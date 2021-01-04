---
title: Problembehandlung bei Rollen, die nicht gestartet werden | Microsoft-Dokumentation
description: Hier finden Sie einige der häufigsten Gründe, aus denen eine Clouddienstrolle nicht gestartet wird. Es werden auch Lösungen für diese Probleme bereitgestellt.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: bedf4dd2a955a8ffe648b47c1691e77ac1fdb8f5
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187798"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Problembehandlung bei Clouddienstrollen, die nicht gestartet werden
In diesem Artikel finden Sie Informationen zu allgemeinen Problemen in Zusammenhang mit nicht gestarteten Azure-Clouddienstrollen sowie zur Lösung dieser Probleme.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Fehlende DLLs oder Abhängigkeiten
Nicht reagierende Rollen oder Rollen, die sich in einer Schleife zwischen den Status **Wird initialisiert**, **Ausgelastet** und **Wird beendet** befinden, können durch fehlende DLLs oder Assemblys verursacht werden.

Folgende Symptome können auf fehlende DLLs oder Assemblys hinweisen:

* Ihre Rolleninstanz durchläuft die Status **Wird initialisiert**, **Ausgelastet** und **Wird beendet**.
* Die Rolleninstanz befindet sich im Status **Bereit** , aber wenn Sie zu Ihrer Webanwendung navigieren, wird die Seite nicht angezeigt.

Zur Untersuchung dieser Probleme gibt es mehrere empfohlene Methoden.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostizieren von Problemen mit fehlenden DLLs in einer Webrolle
Wenn Sie zu einer Website navigieren, die in einer Webrolle bereitgestellt wird, und im Browser ein Serverfehler wie der folgende Fehler angezeigt wird, kann dies darauf hinweisen, dass eine DLL fehlt.

![Serverfehler in Anwendung '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostizieren von Problemen durch Deaktivieren von benutzerdefinierten Fehlern
Sie können umfassendere Fehlerinformationen anzeigen, indem Sie in der web.config-Datei für die Webrolle den Modus für benutzerdefinierte Fehler deaktivieren und den Dienst erneut bereitstellen.

So zeigen Sie umfassendere Fehlerinformationen an, ohne Remotedesktop zu verwenden:

1. Öffnen Sie die Projektmappe in Microsoft Visual Studio.
2. Suchen Sie im **Projektmappen-Explorer** die web.config-Datei, und öffnen Sie sie.
3. Suchen Sie in der web.config-Datei den Abschnitt „system.web“, und fügen Sie folgende Zeile hinzu:

    ```xml
    <customErrors mode="Off" />
    ```
4. Speichern Sie die Datei .
5. Packen Sie den Dienst neu, und stellen Sie ihn erneut bereit.

Nachdem der Dienst neu bereitgestellt wurde, sehen Sie eine Fehlermeldung mit dem Namen der fehlenden Assembly oder DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostizieren von Problemen durch Remoteanzeige des Fehlers
Sie können Remotedesktop verwenden, um remote auf die Rolle zuzugreifen und vollständige Fehlerinformationen anzuzeigen. Gehen Sie wie folgt vor, um Fehler mithilfe von Remotedesktop anzuzeigen:

1. Stellen Sie sicher, dass Azure SDK 1.3 oder höher installiert ist.
2. Aktivieren Sie Remotedesktop während der Bereitstellung der Projektmappe mit Visual Studio. Weitere Informationen finden Sie unter [Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services mit Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. Wenn die Instanz im Microsoft Azure-Portal den Status **Bereit** zeigt, stellen Sie eine Remoteverbindung mit der Instanz her. Weitere Informationen zur Verwendung des Remotedesktops mit Cloud Services finden Sie unter [Remotezugriff auf Rolleninstanzen](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Melden Sie sich mit den Anmeldeinformationen, die während der Remotedesktopkonfiguration angegeben wurden, an der virtuellen Maschine an.
6. Öffnen Sie ein Befehlsfenster.
7. Geben Sie `IPconfig`ein.
8. Notieren Sie sich den Wert der IPV4-Adresse.
9. Öffnen Sie Internet Explorer.
10. Geben Sie die Adresse und den Namen der Webanwendung ein. Beispiel: `http://<IPV4 Address>/default.aspx`.

Wenn Sie zur Website navigieren, werden nun ausführlichere Fehlermeldungen zurückgegeben:

* Serverfehler in Anwendung '/'.
* Beschreibung: Unbehandelte Ausnahme beim Ausführen der aktuellen Webanforderung. Überprüfen Sie die Stapelüberwachung, um weitere Informationen über diesen Fehler anzuzeigen und festzustellen, wo der Fehler im Code verursacht wurde.
* Ausnahmedetails: System.IO.FIleNotFoundException: Die Datei oder Assembly  "Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35" oder eine Abhängigkeit davon wurde nicht gefunden. Die angegebene Datei wurde nicht gefunden.

Beispiel:

![Expliziter Serverfehler in Anwendung '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostizieren von Problemen mit dem Serveremulator
Sie können den Microsoft Azure-Serveremulator verwenden, um Probleme mit fehlenden Abhängigkeiten sowie web.config-Fehler zu diagnostizieren und zu beheben.

Bei dieser Diagnosemethode erzielen Sie die besten Ergebnisse, wenn Sie einen physischen oder virtuellen Computer mit einer neuen Windows-Installation verwenden. Verwenden Sie Windows Server 2008 R2 x64, um die Azure-Umgebung möglichst optimal zu simulieren.

1. Installieren Sie die eigenständige Version des [Azure SDK](https://azure.microsoft.com/downloads/).
2. Erstellen Sie das Clouddienstprojekt auf dem Entwicklungscomputer.
3. Navigieren Sie im Windows-Explorer zum Ordner „bin\debug“ des Clouddienstprojekts.
4. Kopieren Sie den CSX-Ordner und die CSCFG-Datei auf den Computer, den Sie zum Debuggen von Problemen verwenden.
5. Öffnen Sie auf dem neu installierten Computer ein Azure SDK-Eingabeaufforderungsfenster, und geben Sie `csrun.exe /devstore:start`ein.
6. Geben Sie an der Eingabeaufforderung `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser` ein:
7. Wenn die Rolle gestartet wird, werden detaillierte Fehlerinformationen in Internet Explorer angezeigt. Für eine eingehendere Problemdiagnose können Sie auch standardmäßige Windows-Tools zur Problembehandlung verwenden.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostizieren von Problemen mit IntelliTrace
Für Worker- und Webrollen, die .NET Framework 4 verwenden, können Sie [IntelliTrace](/visualstudio/debugger/intellitrace) nutzen. Diese Funktion steht in Microsoft Visual Studio Enterprise zur Verfügung.

Gehen Sie folgendermaßen vor, um den Dienst mit aktiviertem IntelliTrace bereitzustellen:

1. Vergewissern Sie sich, dass Azure SDK 1.3 oder höher installiert ist.
2. Stellen Sie die Projektmappe mit Visual Studio bereit. Aktivieren Sie bei der Bereitstellung das Kontrollkästchen **IntelliTrace für .NET 4-Rollen aktivieren** .
3. Wenn die Instanz gestartet wurde, öffnen Sie den **Server-Explorer**.
4. Erweitern Sie den Knoten **Azure\\Cloud Services**, und suchen Sie die Bereitstellung.
5. Erweitern Sie die Bereitstellung, bis die Rolleninstanzen angezeigt werden. Klicken Sie mit der rechten Maustaste auf eine der Instanzen.
6. Wählen Sie **IntelliTrace-Protokolle anzeigen** aus. Die **IntelliTrace-Zusammenfassung** wird geöffnet.
7. Suchen Sie in der Zusammenfassung den Abschnitt mit den Ausnahmen. Wenn Ausnahmen vorhanden sind, ist der Abschnitt mit **Ausnahmedaten** gekennzeichnet.
8. Erweitern Sie **Ausnahmedaten**, und suchen Sie nach **System.IO.FileNotFoundException**-Fehlern ähnlich dem folgenden:

![Ausnahmedaten, fehlende Datei oder Assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Behandeln von Problemen mit fehlenden DLLs und Assemblys
Um Fehler mit fehlenden DLLs und Assemblys zu beheben, gehen Sie folgendermaßen vor:

1. Öffnen Sie die Projektmappe in Visual Studio.
2. Öffnen Sie im **Projektmappen-Explorer** den Ordner **Verweise**.
3. Klicken Sie auf die im Fehler angegebene Assembly.
4. Suchen Sie im Bereich **Eigenschaften** die Eigenschaft **Lokale Kopie**, und legen Sie den Wert auf **True** fest.
5. Stellen Sie den Clouddienst erneut bereit.

Nachdem Sie bestätigt haben, dass alle Fehler korrigiert wurden, können Sie den Dienst bereitstellen, ohne das Kontrollkästchen **IntelliTrace für .NET 4-Rollen aktivieren** zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich weitere [Artikel zur Problembehandlung](../index.yml?product=cloud-services&tag=top-support-issue) für Clouddienste an.

Erfahren Sie in der [Blogreihe von Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data), wie Sie Probleme bei Clouddienstrollen mit den Compute-Diagnosedaten von Azure-PaaS beheben.
