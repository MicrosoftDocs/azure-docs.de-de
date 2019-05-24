---
title: Behandeln von Problemen mit der Azure-Zugriffsbereichserweiterung für IE | Microsoft-Dokumentation
description: So stellen Sie das Internet Explorer-Add-On für das Portal "Meine Apps" mithilfe von Gruppenrichtlinien bereit
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: f08a02842c97b0f4076a1b311aa918df6d83c592
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824503"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Behandeln von Problemen mit der Zugriffsbereichserweiterung für Internet Explorer

Dieser Artikel hilft Ihnen bei der Behandlung der folgenden Probleme:

* Während der Verwendung von Internet Explorer ist nicht möglich, über das Portal "Meine Apps" auf Ihre Apps zuzugreifen.
* Die Meldung „Software installieren“ wird angezeigt, obwohl Sie die Software bereits installiert haben.

Wenn Sie Administrator sind, lesen Sie [Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mithilfe von Gruppenrichtlinien](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Ausführen des Diagnosetools

Sie können Probleme bei der Installation der Zugriffsbereichserweiterung diagnostizieren, indem Sie das Diagnosetool für den Zugriffsbereich herunterladen und ausführen. 

Gehen Sie wie folgt vor, um das Diagnosetool herunterzuladen und zu installieren:

1. [Wählen Sie diesen Link zum Herunterladen des Diagnosetools aus.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Öffnen Sie die Datei, und extrahieren Sie den Inhalt auf Ihrem Computer.
   
3. Klicken Sie zum Ausführen des Tools mit der rechten Maustaste auf die Datei mit dem Namen *AccessPanelExtensionDiagnosticTool.js*, und wählen Sie dann **Öffnen mit** > **Microsoft Windows Script Host** aus.
   
    ![Öffnen mit > Microsoft Windows Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. Überprüfen Sie die angezeigten Diagnoseergebnisse, und wählen Sie **Ja** aus, um die Probleme zu beheben. Das Dialogfeld **Ergebnisse überprüfen** wird mit Informationen zu Maßnahmen angezeigt, die Sie ergreifen können, falls die Erweiterung nicht funktioniert.  

5. Lesen Sie die Meldung, und wählen Sie **OK** aus.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Überprüfen, ob die Zugriffsbereichserweiterung aktiviert ist

Gehen Sie wie folgt vor, um zu überprüfen, ob die Zugriffsbereichserweiterung in Internet Explorer aktiviert ist:

1. Wählen Sie in Internet Explorer in der oberen rechten Ecke des Fensters das **Zahnradsymbol** aus, und wählen Sie dann **Internetoptionen** aus.
   
2. Wechseln Sie zur Registerkarte **Programme**, und wählen Sie **Add-Ons verwalten** aus.
   
3. Wählen Sie im Abschnitt **Microsoft Corporation** die Option **Zugriffsbereichserweiterung** aus, und wählen Sie dann **Aktivieren** aus.
   
4. Schließen Sie zum Speichern der Änderungen alle geöffneten Internet Explorer-Browserfenster. Die Änderung wird beim nächsten Öffnen von Internet Explorer wirksam.

## <a name="enable-extensions-for-inprivate-browsing"></a>Aktivieren von Erweiterungen für InPrivate-Browsen

Gehen Sie wie folgt vor, um Erweiterungen für InPrivate-Browsen zu aktivieren:

1. Wählen Sie in Internet Explorer in der oberen rechten Ecke des Fensters das **Zahnradsymbol** aus, und wählen Sie dann **Internetoptionen** aus.
   
2. Wechseln Sie zur Registerkarte **Datenschutz**, und vergewissern Sie sich, dass das Kontrollkästchen **Symbolleisten und Erweiterungen beim Starten des InPrivate-Browsens deaktivieren** deaktiviert ist.
   
3.  Schließen Sie zum Speichern der Änderungen alle geöffneten Internet Explorer-Browserfenster. Die Änderung wird beim nächsten Öffnen von Internet Explorer wirksam.

## <a name="uninstall-the-access-panel-extension"></a>Deinstallieren der Zugriffsbereichserweiterung

Gehen Sie wie folgt vor, um die Zugriffsbereichserweiterung auf Ihrem Computer zu deinstallieren:

1. Suchen Sie in der Systemsteuerung nach *deinstallieren*. 

2. Wählen Sie in den Suchergebnissen den Eintrag **Programm deinstallieren** aus.
   
    ![Suchen Sie nach „Programm deinstallieren“.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. Wählen Sie in der Liste den Eintrag **Zugriffsbereichserweiterung** aus, und wählen Sie dann **Deinstallieren** aus.

    ![Deinstallieren Sie die Zugriffsbereichserweiterung.](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. Sie können dann versuchen, die Erweiterung erneut zu installieren, um festzustellen, ob das Problem behoben wurde.

Wenn beim Deinstallieren der Erweiterung Probleme auftreten, können Sie die Erweiterung auch mithilfe des Tools [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) entfernen.

## <a name="related-articles"></a>Verwandte Artikel
* [Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](what-is-single-sign-on.md)
* [Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mithilfe von Gruppenrichtlinien](deploy-access-panel-browser-extension.md)

