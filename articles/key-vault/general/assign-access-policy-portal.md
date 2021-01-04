---
title: Zuweisen einer Azure Key Vault-Zugriffsrichtlinie (Portal)
description: Erfahren Sie, wie Sie einem Dienstprinzipal oder einer Anwendungsidentität mithilfe des Azure-Portals eine Key Vault-Zugriffsrichtlinie zuweisen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 9277d100eb6744adfc20fbba9f96b822654e64a3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287699"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Zuweisen einer Key Vault-Zugriffsrichtlinie über das Azure-Portal

Eine Key Vault-Zugriffsrichtlinie legt fest, ob ein bestimmter Dienstprinzipal (eine Anwendung oder eine Benutzergruppe) verschiedene Vorgänge für [Geheimnisse](../secrets/index.yml), [Schlüssel](../keys/index.yml) und [Zertifikate](../certificates/index.yml) in Key Vault ausführen kann. Sie können mithilfe des Azure-Portals (dieser Artikel), der [Azure-Befehlszeilenschnittstelle](assign-access-policy-cli.md) oder von [Azure PowerShell](assign-access-policy-powershell.md) Zugriffsrichtlinien zuweisen.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Weitere Informationen zum Erstellen von Gruppen in Azure Active Directory über das Azure-Portal finden Sie unter [Erstellen einer einfachen Gruppe und Hinzufügen von Mitgliedern](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="assign-an-access-policy"></a>Zuweisen einer Zugriffsrichtlinie

1.  Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Key Vault-Ressource. 

1.  Wählen Sie unter **Einstellungen** die Option **Zugriffsrichtlinien** und dann **Zugriffsrichtlinie hinzufügen** aus:

    ![Auswählen von „Zugriffsrichtlinien“, Auswählen von „Rollenzuweisung hinzufügen“](../media/authentication/assign-policy-portal-01.png)

1.  Wählen Sie die gewünschten Berechtigungen unter **Zertifikatberechtigungen** , **Schlüsselberechtigungen** und **Geheimnisberechtigungen** aus. Sie können auch eine Vorlage auswählen, die gebräuchliche Kombinationen aus Berechtigungen enthält:

    ![Angeben von Zugriffsrichtlinienberechtigungen](../media/authentication/assign-policy-portal-02.png)

1. Wählen Sie unter **Prinzipal auswählen** den Link **Keine ausgewählt** aus, um den Auswahlbereich **Prinzipal** zu öffnen. Geben Sie den Namen der App oder des Dienstprinzipals in das Suchfeld ein, wählen Sie das passende Ergebnis aus, und wählen Sie dann **Auswählen** aus.

    ![Auswählen des Dienstprinzipals für die Zugriffsrichtlinie](../media/authentication/assign-policy-portal-03.png)

    Wenn Sie für die App eine verwaltete Identität verwenden, suchen Sie nach dem Namen der App, und wählen Sie ihn aus. (Weitere Informationen zu verwalteten Identitäten und Dienstprinzipalen finden Sie unter [Authentifizierung mit Key Vault: App-Identität und Dienstprinzipale](authentication.md#app-identity-and-security-principals).)
 
1.  Wählen Sie im Bereich **Zugriffsrichtlinie hinzufügen** die Option **Hinzufügen** aus, um die Zugriffsrichtlinie zu speichern.

    ![Hinzufügen der Zugriffsrichtlinie mit zugewiesenem Dienstprinzipal](../media/authentication/assign-policy-portal-04.png)

1. Vergewissern Sie sich auf der Seite **Zugriffsrichtlinien** , dass Ihre Zugriffsrichtlinie unter **Aktuelle Zugriffsrichtlinien** aufgeführt ist, und wählen Sie dann **Speichern** aus. Zugriffsrichtlinien werden erst angewandt, wenn Sie sie speichern.

    ![Speichern der Änderungen an den Zugriffsrichtlinien](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Nächste Schritte

- [Azure Key Vault – Sicherheit: Identitäts- und Zugriffsverwaltung](overview-security.md#identity-and-access-management)
- [Sicherer Zugriff auf einen Schlüsseltresor](secure-your-key-vault.md)
- [Entwicklerhandbuch zu Azure Key Vault](developers-guide.md)
- [Bewährte Methoden bei Azure Key Vault](best-practices.md)