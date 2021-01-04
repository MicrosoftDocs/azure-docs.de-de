---
title: Konfigurieren von Azure Active Directory-Kennwortschutzlisten
description: In diesem Tutorial wird beschrieben, wie Sie als Schutzmaßnahme benutzerdefinierte Listen mit gesperrten Kennwörtern für Azure Active Directory konfigurieren, um die Nutzung von häufig verwendeten Wörtern für Ihre Umgebung auszuschließen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abdcf4c76c92c2ed967594527d323b2cd8be32
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741183"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Tutorial: Konfigurieren von benutzerdefinierten gesperrten Kennwörtern für den Azure Active Directory-Kennwortschutz

Benutzer erstellen häufig Kennwörter, in denen gängige Wörter mit regionalem Bezug verwendet werden, z. B. eine Schule, eine Sportmannschaft oder eine prominente Person. Diese Kennwörter sind leicht zu erraten und halten wörterbuchbasierten Angriffen oft nicht stand. Zum Erzwingen von sicheren Kennwörtern in Ihrer Organisation können Sie über die benutzerdefinierte Azure AD-Liste (Azure Active Directory) mit gesperrten Kennwörtern bestimmte Zeichenfolgen hinzufügen, die dann überprüft und gesperrt werden. Die Anforderung einer Kennwortänderung ist nicht erfolgreich, wenn sich eine Übereinstimmung mit der benutzerdefinierten Liste mit gesperrten Kennwörtern ergibt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren von benutzerdefinierten gesperrten Kennwörtern
> * Hinzufügen von Einträgen zur benutzerdefinierten Liste mit gesperrten Kennwörtern
> * Testen von Kennwortänderungen mit einem gesperrten Kennwort

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Einen funktionierenden Azure AD-Mandanten mit mindestens einer aktivierten Azure AD Premium P1- oder -Testlizenz.
    * Erstellen Sie ggf. [ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ein Konto mit Berechtigungen vom Typ *Globaler Administrator*.
* Ein Benutzer ohne Administratorrechte mit einem Ihnen bekannten Kennwort, wie z. B. *testuser*. Sie testen ein Kennwortänderungsereignis, indem Sie im Rahmen des Tutorials dieses Konto nutzen.
    * Wenn Sie einen Benutzer erstellen müssen, finden Sie weitere Informationen unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) weiter.
    * Zum Testen des Kennwortänderungsvorgangs mit einem gesperrten Kennwort muss für den Azure AD-Mandanten die [Self-Service-Kennwortzurücksetzung konfiguriert sein](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Was sind Listen mit gesperrten Kennwörtern?

Azure AD enthält eine globale Liste mit gesperrten Kennwörtern. Der Inhalt der globalen Liste mit gesperrten Kennwörtern basiert nicht auf einer externen Datenquelle. Stattdessen basiert die globale Liste mit gesperrten Kennwörtern auf den fortlaufenden Ergebnissen der Azure AD-Sicherheitstelemetriedaten und -Analysen. Wenn ein Benutzer oder Administrator versucht, seine Anmeldeinformationen zu ändern oder zurückzusetzen, wird das gewünschte Kennwort anhand der Liste mit gesperrten Kennwörtern überprüft. Die Anforderung einer Kennwortänderung ist nicht erfolgreich, wenn sich eine Übereinstimmung mit der globalen Liste mit gesperrten Kennwörtern ergibt. Sie können diese globale Standardliste gesperrter Kennwörter nicht bearbeiten.

Sie können auch eine benutzerdefinierte Liste mit gesperrten Kennwörtern definieren, um flexibel festzulegen, welche Kennwörter zulässig sind. Die benutzerdefinierte Liste mit gesperrten Kennwörtern wird zusammen mit der globalen Liste mit gesperrten Kennwörtern eingesetzt, um in Ihrer Organisation die Nutzung sicherer Kennwörter zu erzwingen. Der benutzerdefinierten Liste mit gesperrten Kennwörtern können organisationsspezifische Ausdrücke hinzugefügt werden, z. B.:

* Markennamen
* Produktnamen
* Standorte, z. B. die Hauptniederlassung des Unternehmens
* Interne unternehmensspezifische Ausdrücke
* Abkürzungen mit einer bestimmten unternehmensspezifischen Bedeutung

Wenn ein Benutzer versucht, ein Kennwort auf einen Ausdruck zurückzusetzen, der in der globalen oder benutzerdefinierten Liste mit gesperrten Kennwörtern enthalten ist, wird eine der folgenden Fehlermeldungen angezeigt:

* *Ihr Kennwort ist aufgrund eines enthaltenen Worts, Ausdrucks oder Musters leider leicht zu erraten. Wiederholen Sie den Vorgang mit einem anderen Kennwort.*
* *Sie können dieses Kennwort nicht verwenden, da es Wörter oder Zeichen enthält, die vom Administrator gesperrt wurden. Wiederholen Sie den Vorgang mit einem anderen Kennwort.*

Die benutzerdefinierte Liste mit gesperrten Kennwörtern ist auf maximal 1.000 Ausdrücke beschränkt. Sie ist nicht für lange Listen mit zu sperrenden Kennwörtern ausgelegt. Informieren Sie sich über die [Konzepte von benutzerdefinierten Listen mit gesperrten Kennwörtern](concept-password-ban-bad.md#custom-banned-password-list), und sehen Sie sich die [Übersicht über den Algorithmus für die Kennwortauswertung](concept-password-ban-bad.md#how-are-passwords-evaluated) an, um noch mehr Vorteile der benutzerdefinierten Liste mit gesperrten Kennwörtern zu nutzen.

## <a name="configure-custom-banned-passwords"></a>Konfigurieren von benutzerdefinierten gesperrten Kennwörtern

Wir aktivieren die benutzerdefinierte Liste mit gesperrten Kennwörtern und fügen einige Einträge hinzu. Sie können der benutzerdefinierten Liste mit gesperrten Kennwörtern jederzeit weitere Einträge hinzufügen.

Führen Sie die folgenden Schritte aus, um die benutzerdefinierte Liste mit gesperrten Kennwörtern zu aktivieren und ihr Einträge hinzuzufügen:

1. Melden Sie sich mit dem Konto mit den Berechtigungen vom Typ *Globaler Administrator* beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, wählen Sie den Eintrag aus, und wählen Sie anschließend im Menü auf der linken Seite die Option **Sicherheit** aus.
1. Wählen Sie unter der Menüüberschrift **Verwalten** die Option **Authentifizierungsmethoden** und dann **Kennwortschutz** aus.
1. Legen Sie die Option **Benutzerdefinierte Liste erzwingen** auf *Ja* fest.
1. Fügen Sie der **benutzerdefinierten Liste mit gesperrten Kennwörtern** Zeichenfolgen hinzu. Geben Sie eine Zeichenfolge pro Zeile ein. Für die benutzerdefinierte Liste mit gesperrten Kennwörtern gelten die folgenden Aspekte und Einschränkungen:

    * Die benutzerdefinierte Liste gesperrter Kennwörter kann bis zu 1.000 Ausdrücke umfassen.
    * Bei der benutzerdefinierten Liste gesperrter Kennwörter wird die Groß- und Kleinschreibung nicht beachtet.
    * Bei der benutzerdefinierten Liste mit gesperrten Kennwörtern wird die gängige Ersetzung von Zeichen berücksichtigt, z. B. „o“ und „0“ oder „a“ und „\@“.
    * Die Zeichenfolgen müssen mindestens vier und dürfen höchstens 16 Zeichen lang sein.

    Geben Sie Ihre eigenen benutzerdefinierten Kennwörter an, die gesperrt werden sollen. Dies ist im folgenden Beispiel dargestellt.

    [ ![Ändern der benutzerdefinierten Liste gesperrter Kennwörter unter „Authentifizierungsmethoden“ im Azure-Portal](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Übernehmen Sie für die Option **Kennwortschutz für Windows Server Active Directory aktivieren** die Einstellung *Nein*.
1. Wählen Sie **Speichern**, um die benutzerdefinierte Liste mit gesperrten Kennwörtern und Ihre Einträge zu aktivieren.

Es kann mehrere Stunden dauern, bis Updates der benutzerdefinierten Liste gesperrter Kennwörter angewendet werden.

Für eine Hybridumgebung können Sie den [Azure AD-Kennwortschutz auch in einer lokalen Umgebung bereitstellen](howto-password-ban-bad-on-premises-deploy.md). Für Anforderungen von Kennwortänderungen werden in der Cloud und in der lokalen Umgebung die gleichen globalen und benutzerdefinierten Listen mit gesperrten Kennwörtern verwendet.

## <a name="test-custom-banned-password-list"></a>Testen der benutzerdefinierten Liste mit gesperrten Kennwörtern

Ändern Sie ein Kennwort in eine Variante eines Kennworts, das Sie im vorherigen Abschnitt hinzugefügt haben, um die benutzerdefinierte Liste mit gesperrten Kennwörtern in Aktion zu sehen. Wenn Azure AD versucht, die Kennwortänderung zu verarbeiten, wird das Kennwort mit einem Eintrag in der benutzerdefinierten Liste mit gesperrten Kennwörtern abgeglichen. Dem Benutzer wird dann ein Fehler angezeigt.

> [!NOTE]
> Bevor ein Benutzer sein Kennwort im webbasierten Portal zurücksetzen kann, muss der Azure AD-Mandant für die [Self-Service-Kennwortzurücksetzung konfiguriert](tutorial-enable-sspr.md) werden. Der Benutzer kann sich dann bei Bedarf [unter https://aka.ms/ssprsetup für SSPR registrieren](https://aka.ms/ssprsetup).

1. Navigieren Sie zur Seite **Meine Apps** unter [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Klicken Sie in der oberen rechten Ecke auf Ihren Namen, und wählen Sie dann aus dem Dropdownmenü die Option **Profil** aus.

    ![Auswählen des Profils](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Klicken Sie auf der Seite **Profil** auf **Kennwort ändern**.
1. Geben Sie auf der Seite **Kennwort ändern** das vorhandene (alte) Kennwort ein. Geben Sie ein neues Kennwort ein (und bestätigen Sie es durch die erneute Eingabe), das in der benutzerdefinierten Liste mit gesperrten Kennwörtern aus dem vorherigen Abschnitt enthalten ist, und wählen Sie anschließend **Senden** aus.
1. Es wird eine Fehlermeldung mit dem Hinweis zurückgegeben, dass das Kennwort vom Administrator gesperrt wurde. Dies ist im folgenden Beispiel dargestellt:

    ![Fehlermeldung beim Versuch, ein Kennwort zu verwenden, das in der benutzerdefinierten Liste mit gesperrten Kennwörtern enthalten ist](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Schritte aus, wenn Sie die im Rahmen dieses Tutorials konfigurierte benutzerdefinierte Liste mit gesperrten Kennwörtern nicht mehr nutzen möchten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, wählen Sie den Eintrag aus, und wählen Sie anschließend im Menü auf der linken Seite die Option **Sicherheit** aus.
1. Wählen Sie unter der Menüüberschrift **Verwalten** die Option **Authentifizierungsmethoden** und dann **Kennwortschutz** aus.
1. Legen Sie die Option **Benutzerdefinierte Liste erzwingen** auf *Nein* fest.
1. Wählen Sie **Speichern** aus, um die Konfiguration für die benutzerdefinierten gesperrten Kennwörter zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie für Azure AD als Schutzmaßnahme benutzerdefinierte Listen mit gesperrten Kennwörtern aktiviert und konfiguriert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren von benutzerdefinierten gesperrten Kennwörtern
> * Hinzufügen von Einträgen zur benutzerdefinierten Liste mit gesperrten Kennwörtern
> * Testen von Kennwortänderungen mit einem gesperrten Kennwort

> [!div class="nextstepaction"]
> [Aktivieren von Azure AD Multi-Factor Authentication (risikobasiert)](./tutorial-enable-azure-mfa.md)