---
title: 'Herstellen einer Verbindung mit Windows Virtual Desktop (Vorschauversion) von Windows 10 oder Windows 7: Azure'
description: Erfahren Sie, wie Sie von Windows 10 oder Windows 7 eine Verbindung mit Windows Virtual Desktop (Vorschauversion) herstellen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145987"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Herstellen einer Verbindung über Windows 10 oder Windows 7

> Gilt für: Windows 7 und Windows 10.

Es ist ein Client als Download verfügbar, der den Zugriff auf Windows Virtual Desktop-Ressourcen (Vorschauversion) über Geräte ermöglicht, auf denen Windows 7 und Windows 10 ausgeführt wird.

> [!IMPORTANT]
> Verwenden Sie nicht **RemoteApp- und Desktopverbindungen (RADC)** oder **Remotedesktopverbindung (MSTSC)** für den Zugriff auf Windows Virtual Desktop-Ressourcen, da Windows Virtual Desktop keinen von beiden Clients unterstützt.

## <a name="install-the-client"></a>Installieren des Clients

Führen Sie den [Download](https://go.microsoft.com/fwlink/?linkid=2068602) des Clients und dann die Installation auf Ihrem lokalen PC durch. Für die Installation sind Administratorrechte erforderlich.

## <a name="subscribe-to-a-feed"></a>Abonnieren eines Feeds

Rufen Sie die Liste mit den verfügbaren verwalteten Ressourcen ab, indem Sie den von Ihrem Administrator bereitgestellten Feed abonnieren. Durch das Abonnieren werden die Ressourcen auf Ihrem lokalen PC verfügbar gemacht.

Abonnieren Sie einen Feed wie folgt:

1. Starten Sie den Client aus der Liste „Alle Apps“, suchen Sie nach **Remotedesktop**.
1. Wählen Sie auf der Hauptseite **Abonnieren** aus, um eine Verbindung mit dem Dienst herzustellen und Ihre Ressourcen abzurufen.
1. **Melden Sie sich**  mit Ihrem Benutzerkonto an, wenn Sie dazu aufgefordert werden.

Nach der erfolgreichen Authentifizierung sollte nun eine Liste mit den Ressourcen angezeigt werden, die Ihnen zur Verfügung stehen.

Sie können zwei Methoden nutzen, um Ressourcen zu starten.

- Doppelklicken Sie auf der Hauptseite des Clients auf eine Ressource, um sie zu starten.
- Starten Sie eine Ressource über das Startmenü, wie Sie dies sonst auch für andere Apps tun.
  - Sie können auch in der Suchleiste nach Apps suchen.

Nachdem Sie einen Feed abonniert haben, wird der Inhalt des Feeds in regelmäßigen Abständen automatisch aktualisiert. Ressourcen können basierend auf Änderungen durch Ihren Administrator hinzugefügt, geändert oder entfernt werden.

## <a name="view-the-details-of-a-feed"></a>Anzeigen von Details zu einem Feed

Nach Abschluss des Abonnements können Sie weitere Informationen zum Feed im Bereich „Details“ anzeigen.

1. Wählen Sie auf der Hauptseite des Clients die Auslassungspunkte ( **...** ) rechts neben dem Feednamen aus.
1. Wählen Sie im Dropdownmenü **Details** aus.
1. Der Bereich „Details“ wird auf der rechten Seite des Clients angezeigt.

Der Bereich „Details“ enthält nützliche Informationen zum Feed:

- URL und Benutzername zum Abonnement
- Anzahl der Apps und Desktops
- Datum/Uhrzeit der letzten Aktualisierung
- Status der letzten Aktualisierung

Bei Bedarf können Sie ein manuelles Update starten, indem Sie **Jetzt aktualisieren** auswählen.

## <a name="unsubscribe-from-a-feed"></a>Kündigen von Feeds

In diesem Abschnitt erfahren Sie, wie Sie einen Feed kündigen. Sie können das Abonnement kündigen, um es mit einem anderen Konto zu erneuern oder um Ihre Ressourcen aus dem System zu entfernen.

1. Wählen Sie auf der Hauptseite des Clients die Auslassungspunkte ( **...** ) rechts neben dem Feednamen aus.
1. Wählen Sie im Dropdownmenü **Abbestellen** aus.
1. Überprüfen Sie die Angaben, und wählen Sie im Dialogfeld **Weiter** aus.

## <a name="update-the-client"></a>Aktualisieren des Clients

Wenn eine neue Version des Clients verfügbar ist, werden Sie vom Client und über das Info-Center von Windows benachrichtigt. Wählen Sie die Benachrichtigung aus, um den Updateprozess zu starten.
