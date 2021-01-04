---
title: Troubleshooting für den Remotedesktopclient in Windows Virtual Desktop – Azure
description: Informationen zum Beheben von Problemen beim Einrichten von Clientverbindungen in einer Windows Virtual Desktop-Mandantenumgebung
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6083dbcc270c0e9dde1da45ed01369d03146237
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108965"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Troubleshooting für den Remotedesktopclient

In diesem Artikel werden häufige Probleme mit dem Remotedesktopclient sowie deren Behebung beschrieben.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Der Remotedesktopclient für Windows 7 oder Windows 10 reagiert nicht mehr oder kann nicht geöffnet werden.

Ab Version 1.2.790 können Sie die Benutzerdaten über die Seite „Info“ oder mithilfe eines Befehls zurücksetzen.

Verwenden Sie den folgenden Befehl, um Ihre Benutzerdaten zu entfernen, die Standardeinstellungen wiederherzustellen und sich bei allen Arbeitsbereichen auszutragen.

```cmd
msrdcw.exe /reset [/f]
```

Wenn Sie eine frühere Version des Remotedesktopclients verwenden, empfehlen wir Ihnen, den Client zu deinstallieren und neu zu installieren.

## <a name="web-client-wont-open"></a>Webclient wird nicht geöffnet.

Testen Sie zunächst Ihre Internetverbindung, indem Sie eine andere Website in Ihrem Browser öffnen, z. B. [www.bing.com](https://www.bing.com).

Bestätigen Sie mit **nslookup**, dass das DNS den FQDN auflösen kann:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Versuchen Sie, eine Verbindung mit einem anderen Client herzustellen, z. B. dem Remotedesktopclient für Windows 7 oder Windows 10, und überprüfen Sie, ob Sie den Webclient öffnen können.

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>Bei vorhandener Verbindung mit dem Webclient können keine anderen Websites geöffnet werden

Wenn Sie keine anderen Websites öffnen können, während eine Verbindung mit dem Webclient besteht, liegen möglicherweise Netzwerkverbindungsprobleme oder ein Netzwerkausfall vor. Es wird empfohlen, sich an den Netzwerksupport zu wenden.

### <a name="nslookup-cant-resolve-the-name"></a>nslookup kann den Namen nicht auflösen

Wenn nslookup den Namen nicht auflösen kann, liegen möglicherweise Netzwerkverbindungsprobleme oder ein Netzwerkausfall vor. Es wird empfohlen, sich an den Netzwerksupport zu wenden.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Ihr Client kann keine Verbindung herstellen, andere Clients in Ihrem Netzwerk hingegen schon.

Wenn bei der Verwendung des Webclients Probleme mit Ihrem Browser auftreten oder dieser nicht mehr funktioniert, befolgen Sie die folgenden Anweisungen zum Troubleshooting:

1. Starten Sie den Browser neu.
2. Löschen Sie die Browsercookies. Weitere Informationen finden Sie unter [Löschen von Cookiedateien in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Löschen Sie den Browsercache. Weitere Informationen finden Sie unter [Leeren des Browsercache für Ihren Browser](https://binged.it/2RKyfdU).
4. Öffnen Sie den Browser im privaten Modus.

## <a name="client-doesnt-show-my-resources"></a>Der Client zeigt meine Ressourcen nicht an

Überprüfen Sie zunächst das Azure Active Directory-Konto, das Sie verwenden. Wenn Sie sich bereits mit einem anderen Azure Active Directory-Konto angemeldet haben als dem, das Sie für Windows Virtual Desktop verwenden möchten, sollten Sie sich entweder abmelden oder ein privates Browserfenster verwenden.

Wenn Sie Windows Virtual Desktop (klassisch) verwenden, verwenden Sie den Webclientlink in [diesem Artikel](./virtual-desktop-fall-2019/connect-web-2019.md), um eine Verbindung mit Ihren Ressourcen herzustellen.

Wenn das nicht funktioniert, stellen Sie sicher, dass Ihre App-Gruppe einem Arbeitsbereich zugeordnet ist.

## <a name="web-client-stops-responding-or-disconnects"></a>Der Webclient reagiert nicht mehr oder trennt die Verbindung.

Versuchen Sie, eine Verbindung mit einem anderen Browser oder Client herzustellen.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Andere Browser und Clients weisen ebenfalls Fehlfunktionen auf oder können nicht geöffnet werden.

Wenn selbst nach einem Browserwechsel weiterhin Probleme auftreten, liegt es möglicherweise nicht an Ihrem Browser, sondern am Netzwerk. Es wird empfohlen, sich an den Netzwerksupport zu wenden.

## <a name="web-client-keeps-prompting-for-credentials"></a>Der Webclient fordert immer wieder zur Eingabe von Anmeldeinformationen auf.

Wenn der Webclient immer wieder zur Eingabe von Anmeldeinformationen auffordert, gehen Sie wie folgt vor:

1. Vergewissern Sie sich, dass die URL des Webclients richtig ist.
2. Vergewissern Sie sich, dass die verwendeten Anmeldeinformationen für die an die URL gebundene Windows Virtual Desktop-Umgebung gültig sind.
3. Löschen Sie die Browsercookies. Weitere Informationen finden Sie unter [Löschen von Cookiedateien in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Löschen Sie den Browsercache. Weitere Informationen finden Sie unter [Leeren des Browsercaches für Ihren Browser](https://binged.it/2RKyfdU).
5. Öffnen Sie den Browser im privaten Modus.

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>Windows-Client blockiert Feed für Windows Virtual Desktop (klassisch)

Wenn der Windows-Clientfeed keine Apps für Windows Virtual Desktop (klassisch) anzeigt, befolgen Sie die folgenden Anweisungen:

1. Überprüfen Sie, ob die Richtlinie für bedingten Zugriff die App-IDs umfasst, die mit Windows Virtual Desktop (klassisch) verknüpft sind.
2. Überprüfen Sie, ob die Richtlinie für bedingten Zugriff den gesamten Zugriff außer von App-IDs von Windows Virtual Desktop (klassisch) blockiert. In diesem Fall müssen Sie der Richtlinie die App-ID **9cdead84-a844-4324-93f2-b2e6bb768d07** hinzufügen, damit der Client die Feeds ermitteln kann.

Wenn Sie die App-ID „9cdead84-a844-4324-93f2-b2e6bb768d07“ nicht in der Liste finden, müssen Sie den Windows Virtual Desktop-Ressourcenanbieter registrieren. So registrieren Sie den Ressourcenanbieter:

1. Melden Sie sich beim Azure-Portal an.
2. Wechseln Sie zu **Abonnements**, und wählen Sie Ihr Abonnement aus.
3. Wählen Sie im Menü links auf der Seite die Option **Ressourcenanbieter** aus.
4. Suchen Sie nach **Microsoft.DesktopVirtualization**, wählen Sie die Option aus, und wählen Sie dann **Erneut registrieren** aus.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung beim Erstellen einer Windows Virtual Desktop-Umgebung und eines Hostpools in einer Windows Virtual Desktop-Umgebung finden Sie unter [Umgebungs- und Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop – PowerShell](troubleshoot-powershell.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
