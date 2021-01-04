---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: c1628894205754c1b8f1fc954796009afbdc8ed4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994310"
---
#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/) 

1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann die Webanwendung aus, die Zugriff auf die API haben soll. Beispiel: *webapp1*.
1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie unter **Konfigurierte Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Wählen Sie die Registerkarte **Meine APIs** aus.
1. Wählen Sie die API aus, für die der Webanwendung Zugriff gewährt werden soll. Beispiel: *webapi1*.
1. Erweitern Sie unter **Berechtigung** den Eintrag **Demo**, und wählen Sie dann die zuvor definierten Bereiche aus. Beispiel: *demo.read* und *demo.write*
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Wählen Sie **Administratorzustimmung für (Name Ihres Mandanten) erteilen** aus.
1. Wenn Sie zur Auswahl eines Kontos aufgefordert werden, wählen Sie das derzeit angemeldete Administratorkonto aus, oder melden Sie sich mit einem Konto bei Ihrem Azure AD B2C-Mandanten an, dem mindestens die Rolle *Cloudanwendungsadministrator* zugewiesen wurde.
1. Wählen Sie **Ja** aus.
1. Wählen Sie **Aktualisieren** aus, und vergewissern Sie sich, dass für beide Bereiche unter **Status** der Status „Gewährt für...“ angezeigt wird.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Wählen Sie **Anwendungen (Legacy)** aus, und wählen Sie dann die Webanwendung aus, die Zugriff auf die API haben soll. Beispiel: *webapp1*.
1. Wählen Sie **API-Zugriff** und dann **Hinzufügen** aus.
1. Wählen Sie in der Dropdownliste **API auswählen** die API aus, für die der Webanwendung Zugriff gewährt werden soll. Beispiel: *webapi1*.
1. Wählen Sie in der Dropdownliste **Bereiche auswählen** die Bereiche aus, die Sie zuvor definiert haben. Beispiel: *demo.read* und *demo.write*
1. Klicken Sie auf **OK**.
