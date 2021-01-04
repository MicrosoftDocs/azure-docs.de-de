---
title: Erfassen einer Browserablaufverfolgung zur Problembehandlung
description: Erfassen Sie Netzwerkinformationen aus einer Browserablaufverfolgung, um Probleme mit dem Azure-Portal zu beheben.
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.openlocfilehash: 504a32fb63022f97e8049c7c1ccc7721bbac31ab
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745994"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Erfassen einer Browserablaufverfolgung zur Problembehandlung

Wenn Sie ein Problem mit dem Azure-Portal beheben und sich an den Microsoft-Support wenden müssen, empfiehlt es sich, zuerst eine Browserablaufverfolgung und einige zusätzliche Informationen zu erfassen. Die erfassten Informationen können zu dem Zeitpunkt, an dem das Problem auftritt, wichtige Details zum Portal bereitstellen. Befolgen Sie die Schritte in diesem Artikel für die Entwicklertools in dem Browser, den Sie verwenden: Google Chrome oder Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML), Apple Safari oder Firefox.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome und Microsoft Edge (Chromium)

Google Chrome und Microsoft Edge (Chromium) basieren auf dem [Open-Source-Projekt Chromium](https://www.chromium.org/Home). In den folgenden Schritten wird gezeigt, wie die Entwicklertools verwendet werden, die in den beiden Browsern sehr ähnlich sind. Weitere Informationen finden Sie unter [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) und [Microsoft Edge-Entwicklertools (Chromium)](/microsoft-edge/devtools-guide-chromium).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Es ist wichtig, sich anzumelden, _bevor_ Sie die Ablaufverfolgung starten, damit die Ablaufverfolgung keine vertraulichen Informationen im Zusammenhang mit Ihrer Anmeldung enthält. 

1. Beginnen Sie mit der Aufzeichnung der Schritte, die Sie im Portal durchführen, mithilfe der [Schrittaufzeichnung](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Navigieren Sie im Portal zu dem Schritt, der unmittelbar vor dem Auftreten des Problems liegt.

1. Drücken Sie F12, oder wählen Sie ![Screenshot mit dem Symbol für die Browsereinstellungen](media/capture-browser-trace/chromium-icon-settings.png) aus. > **Weitere Tools** > **Entwicklertools**.

1. Standardmäßig speichert der Browser Ablaufverfolgungsinformationen nur für die Seite, die derzeit geladen ist. Legen Sie die folgenden Optionen fest, damit der Browser alle Ablaufverfolgungsionformationen beibehält, auch wenn Sie für Ihre Reproduktion zu mehreren Seiten navigieren müssen:

    1. Wählen Sie die Registerkarte **Netzwerk** aus, und wählen Sie dann **Protokoll beibehalten** aus.

          ![Screenshot der hervorgehobenen Option „Protokoll beibehalten“ auf der Registerkarte „Netzwerk“](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Wählen Sie die Registerkarte **Konsole** aus, wählen Sie **Konsoleneinstellungen** aus, und wählen Sie dann **Protokoll beibehalten** aus. Wählen Sie **Konsoleneinstellungen** erneut aus, um den Bereich „Einstellungen“ zu schließen.

          ![Screenshot der hervorgehobenen Option „Protokoll beibehalten“ auf der Registerkarte „Konsole“](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Wählen Sie die Registerkarte **Netzwerk** aus, und wählen Sie dann **Aufzeichnung des Netzwerkprotokolls beenden** und **Löschen** aus.

    ![Screenshot: „Aufzeichnung des Netzwerk Protokolls beenden“ und „Löschen“](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Wählen Sie **Netzwerkprotokoll aufzeichnen** aus, und reproduzieren Sie das Problem im Portal.

    ![Screenshot der Aufzeichnung des Netzwerkprotokolls](media/capture-browser-trace/chromium-start-session.png)

    Eine Sitzungsausgabe ähnlich der folgenden Abbildung wird angezeigt.

    ![Screenshot der Sitzungsausgabe](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Nachdem Sie das unerwartete Portalverhalten reproduziert haben, wählen Sie **Aufzeichnung des Netzwerkprotokolls beenden** aus, wählen Sie **HAR exportieren** aus, und speichern Sie die Datei.

    ![Screenshot des Exportierens als HAR-Datei auf der Registerkarte „Netzwerk“](media/capture-browser-trace/chromium-network-export-har.png)

1. Beenden Sie die Schrittaufzeichnung, und speichern Sie die Aufzeichnung.

1. Wählen Sie im Bereich mit dem Entwicklertools des Browsers die Registerkarte **Konsole** aus. Klicken Sie mit der rechten Maustaste auf eine der Nachrichten, wählen Sie **Speichern unter** aus, und speichern Sie die Konsolenausgabe in einer Textdatei.

    ![Screenshot mit hervorgehobener Registerkarte „Konsole“ mit dem Menü „Speichern unter“](media/capture-browser-trace/chromium-console-select.png)

1. Packen Sie die HAR-Datei, die Konsolenausgabe und die Bildschirmaufzeichnung in einem komprimierten Format wie ZIP, und teilen Sie sie dann mit dem Microsoft-Support.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

In den folgenden Schritten wird gezeigt, wie die Entwicklertools in Microsoft Edge (EdgeHTML) verwendet werden. Weitere Informationen finden Sie unter [Microsoft Edge-Entwicklertools (EdgeHTML)](/microsoft-edge/devtools-guide).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Es ist wichtig, sich anzumelden, _bevor_ Sie die Ablaufverfolgung starten, damit die Ablaufverfolgung keine vertraulichen Informationen im Zusammenhang mit Ihrer Anmeldung enthält. 

1. Beginnen Sie mit der Aufzeichnung der Schritte, die Sie im Portal durchführen, mithilfe der [Schrittaufzeichnung](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Navigieren Sie im Portal zu dem Schritt, der unmittelbar vor dem Auftreten des Problems liegt.

1. Drücken Sie F12, oder wählen Sie ![Screenshot mit dem Symbol für die Browsereinstellungen](media/capture-browser-trace/edge-icon-settings.png) aus. > **Weitere Tools** > **Entwicklertools**.

1. Standardmäßig speichert der Browser Ablaufverfolgungsinformationen nur für die Seite, die derzeit geladen ist. Legen Sie die folgenden Optionen fest, damit der Browser alle Ablaufverfolgungsionformationen beibehält, auch wenn Sie für Ihre Reproduktion zu mehreren Seiten navigieren müssen:

    1. Wählen Sie die Registerkarte **Netzwerk** aus, und deaktivieren Sie dann die Option **Einträge beim Navigieren löschen**.

          ![Screenshot: „Einträge beim Navigieren löschen“](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Wählen Sie die Registerkarte **Konsole** aus, und wählen Sie dann **Protokoll beibehalten** aus.

          ![Screenshot: „Protokoll beibehalten“](media/capture-browser-trace/edge-console-preserve-log.png)

1. Wählen Sie die Registerkarte **Netzwerk** aus, und wählen Sie dann **Profilerstellungssitzung beenden** und **Sitzung löschen** aus.

    ![Screenshot: „Profilerstellungssitzung beenden“ und „Sitzung löschen“](media/capture-browser-trace/edge-stop-clear-session.png)

1. Wählen Sie **Profilerstellungssitzung starten** aus, und reproduzieren Sie das Problem im Portal.

    ![Screenshot: „Profilerstellungssitzung starten“](media/capture-browser-trace/edge-start-session.png)

    Eine Sitzungsausgabe ähnlich der folgenden Abbildung wird angezeigt.

    ![Screenshot der Ausgabe für die Profilerstellungssitzung](media/capture-browser-trace/edge-browser-trace-results.png)

1. Nachdem Sie das unerwartete Portalverhalten reproduziert haben, wählen Sie **Profilerstellungssitzung beenden** aus, wählen Sie **Als HAR exportieren** aus, und speichern Sie die Datei.

    ![Screenshot: „Als HAR exportieren“](media/capture-browser-trace/edge-network-export-har.png)

1. Beenden Sie die Schrittaufzeichnung, und speichern Sie die Aufzeichnung.

1. Wählen Sie im Bereich mit dem Entwicklertools des Browsers die Registerkarte **Konsole** aus, und erweitern Sie das Fenster. Platzieren Sie den Cursor am Anfang der Konsolenausgabe, und markieren und wählen Sie dann den gesamten Inhalt der Ausgabe aus. Klicken Sie mit der rechten Maustaste, und wählen Sie dann **Kopieren** aus. Speichern Sie die Konsolenausgabe in einer Textdatei.

    ![Screenshot der hervorgehobenen Menüoption „Kopieren“](media/capture-browser-trace/edge-console-select.png)

1. Packen Sie die HAR-Datei, die Konsolenausgabe und die Bildschirmaufzeichnung in einem komprimierten Format wie ZIP, und teilen Sie sie dann mit dem Microsoft-Support.

## <a name="apple-safari"></a>Apple Safari

In den folgenden Schritten wird gezeigt, wie die Entwicklertools in Apple Safari verwendet werden. Weitere Informationen finden Sie unter [Safari-Entwicklertools: Übersicht](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Aktivieren Sie die Entwicklertools in Apple Safari:

    1. Wählen Sie **Safari** aus, und wählen Sie dann **Einstellungen** aus.

        ![Screenshot: Safari-Einstellungen](media/capture-browser-trace/safari-preferences.png)

    1. Wählen Sie die Registerkarte **Erweitert** aus, und wählen Sie dann **Entwicklungsmenü in Menüleiste anzeigen** aus.

        ![Screenshot: Erweiterte Safari-Einstellungen](media/capture-browser-trace/safari-show-develop-menu.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Es ist wichtig, sich anzumelden, _bevor_ Sie die Ablaufverfolgung starten, damit die Ablaufverfolgung keine vertraulichen Informationen im Zusammenhang mit Ihrer Anmeldung enthält. 

1. Beginnen Sie mit der Aufzeichnung der Schritte, die Sie im Portal durchführen. Weitere Informationen finden Sie unter [Aufzeichnen des Bildschirms auf einem Mac](https://support.apple.com/HT208721).

1. Navigieren Sie im Portal zu dem Schritt, der unmittelbar vor dem Auftreten des Problems liegt.

1. Wählen Sie **Entwicklung** aus, und wählen Sie dann **Webinformationen anzeigen** aus.

    ![Screenshot: „Webinformationen anzeigen“](media/capture-browser-trace/safari-show-web-inspector.png)

1. Standardmäßig speichert der Browser Ablaufverfolgungsinformationen nur für die Seite, die derzeit geladen ist. Legen Sie die folgenden Optionen fest, damit der Browser alle Ablaufverfolgungsionformationen beibehält, auch wenn Sie für Ihre Reproduktion zu mehreren Seiten navigieren müssen:

    1. Wählen Sie die Registerkarte **Netzwerk** aus, und wählen Sie dann **Protokoll beibehalten** aus.

          ![Screenshot der Option „Protokoll beibehalten“](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Wählen Sie die Registerkarte **Konsole** aus, und wählen Sie dann **Protokoll beibehalten** aus.

          ![Screenshot der Option „Protokoll beibehalten“ auf der Registerkarte „Konsole“](media/capture-browser-trace/safari-console-preserve-log.png)

1. Wählen Sie die Registerkarte **Netzwerk** aus, und wählen Sie dann **Netzwerkelemente löschen** aus.

    ![Screenshot: „Netzwerkelemente löschen“](media/capture-browser-trace/safari-clear-session.png)

1. Reproduzieren Sie das Problem im Portal. Eine Sitzungsausgabe ähnlich der folgenden Abbildung wird angezeigt.

    ![Screenshot der Ausgabe nach dem Reproduzieren des Problems](media/capture-browser-trace/safari-browser-trace-results.png)

1. Nachdem Sie das unerwartete Portalverhalten reproduziert haben, wählen Sie **Exportieren** aus, und speichern Sie die Datei.

    ![Screenshot: „Exportieren“](media/capture-browser-trace/safari-network-export-har.png)

1. Beenden Sie die Bildschirmaufzeichnung, und speichern Sie die Aufzeichnung.

1. Wählen Sie im Bereich mit dem Entwicklertools des Browsers die Registerkarte **Konsole** aus, und erweitern Sie das Fenster. Platzieren Sie den Cursor am Anfang der Konsolenausgabe, und markieren und wählen Sie dann den gesamten Inhalt der Ausgabe aus. Verwenden Sie Befehlstaste-C, um die Ausgabe zu kopieren, und speichern Sie sie in einer Textdatei.

    ![Screenshot mit hervorgehobenen Optionen zum Anzeigen und Kopieren der Ausgabe](media/capture-browser-trace/safari-console-select.png)

1. Packen Sie die HAR-Datei, die Konsolenausgabe und die Bildschirmaufzeichnung in einem komprimierten Format wie ZIP, und teilen Sie sie dann mit dem Microsoft-Support.

## <a name="firefox"></a>Firefox

In den folgenden Schritten wird das Verwenden der Entwicklertools in Firefox gezeigt. Weitere Informationen finden Sie unter [Firefox-Entwicklertools](https://developer.mozilla.org/docs/Tools).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Es ist wichtig, sich anzumelden, _bevor_ Sie die Ablaufverfolgung starten, damit die Ablaufverfolgung keine vertraulichen Informationen im Zusammenhang mit Ihrer Anmeldung enthält. 

1. Beginnen Sie mit der Aufzeichnung der Schritte, die Sie im Portal durchführen. Verwenden Sie die [Schrittaufzeichnung](https://support.microsoft.com/help/22878/windows-10-record-steps) unter Windows, oder lesen Sie [So machst du eine Aufnahme deines Mac-Bildschirms](https://support.apple.com/HT208721).

1. Navigieren Sie im Portal zu dem Schritt, der unmittelbar vor dem Auftreten des Problems liegt.

1. Drücken Sie F12, oder wählen Sie ![Screenshot des Symbols „Browsereinstellungen“](media/capture-browser-trace/firefox-icon-settings.png) > **Web Developer** > **Entwicklertools umschalten** aus.

1. Standardmäßig speichert der Browser Ablaufverfolgungsinformationen nur für die Seite, die derzeit geladen ist. Legen Sie die folgenden Optionen fest, damit der Browser alle Ablaufverfolgungsionformationen beibehält, auch wenn Sie für Ihre Reproduktion zu mehreren Seiten navigieren müssen:

    1. Wählen Sie die Registerkarte **Netzwerk** und dann **Persist Logs** (Protokolle beibehalten) aus.

          ![Screenshot der hervorgehobenen Option „Protokolle beibehalten“](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Wählen Sie die Registerkarte **Konsole**, anschließend **Konsoleneinstellungen** und dann **Persist Logs** (Protokolle beibehalten) aus.

          ![Screenshot von „Persist Logs“ (Protokolle beibehalten)](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Wählen Sie die Registerkarte **Netzwerk** und dann **Löschen** aus.

    ![Screenshot von „Löschen“](media/capture-browser-trace/firefox-clear-session.png)

1. Reproduzieren Sie das Problem im Portal. Eine Sitzungsausgabe ähnlich der folgenden Abbildung wird angezeigt.

    ![Screenshot der Ergebnisse der Browserablaufverfolgung](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Nachdem Sie das unerwartete Portalverhalten reproduziert haben, wählen Sie **HAR Export/Import** und dann **Save All As HAR** (Alles als HAR speichern) aus.

    ![Screenshot: „HAR exportieren“](media/capture-browser-trace/firefox-network-export-har.png)

1. Halten Sie die Schrittaufzeichnung unter Windows bzw. die Bildschirmaufzeichnung auf dem Mac an, und speichern Sie die Aufzeichnung.

1. Wählen Sie im Bereich mit dem Entwicklertools des Browsers die Registerkarte **Konsole** aus. Klicken Sie mit der rechten Maustaste auf eine der Nachrichten, wählen Sie **Export Visible Message To** (Sichtbare Nachricht exportieren nach) aus, und speichern Sie die Konsolenausgabe in einer Textdatei.

    ![Screenshot der Konsolenausgabe](media/capture-browser-trace/firefox-console-select.png)

1. Packen Sie die HAR-Datei, die Konsolenausgabe und die Bildschirmaufzeichnung in einem komprimierten Format wie ZIP, und teilen Sie sie dann mit dem Microsoft-Support.

## <a name="next-steps"></a>Nächste Schritte

[Azure-Portal – Übersicht](azure-portal-overview.md)