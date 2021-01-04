---
title: 'Bekannte Probleme: Azure Digital Twins'
description: Hilfreiche Informationen zur Erkennung und Behebung von bekannten Problemen mit Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 549e1808a3b449f7d29b968cde76ef29391880b3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100614"
---
# <a name="known-issues-in-azure-digital-twins"></a>Bekannte Probleme in Azure Digital Twins

Dieser Artikel enthält Informationen zu bekannten Problemen mit Azure Digital Twins.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>„400 Clientfehler: Ungültige Anforderung“ in Cloud Shell

Für Befehle in Cloud Shell, die auf *https://shell.azure.com* ausgeführt werden, kann zeitweilig der folgende Fehler auftreten: „400 Clientfehler: Ungültige Anforderung für URL: http://localhost:50342/oauth2/token“ (gefolgt von der vollständigen Stapelüberwachung).

Insbesondere bei Azure Digital Twins betrifft dies die folgenden Befehlsgruppen:
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Sie können dieses Problem beheben, indem Sie den Befehl `az login` in Cloud Shell erneut ausführen und die nachfolgenden Anmeldeschritte ausführen. Anschließend sollten Sie in der Lage sein, den Befehl erneut auszuführen.

Alternativ dazu können Sie den Cloud Shell-Bereich im Azure-Portal öffnen und Ihre Arbeit in Cloud Shell dort fortsetzen:

:::image type="content" source="media/includes/portal-cloud-shell.png" alt-text="Ansicht des Azure-Portals mit hervorgehobenem Symbol „Cloud Shell“ und Anzeige von Cloud Shell im unteren Bereich des Portalfensters" lightbox="media/includes/portal-cloud-shell.png":::

Eine weitere Lösung besteht darin, [die Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) auf Ihrem Computer zu installieren, sodass Sie Azure CLI-Befehle lokal ausführen können. Bei der lokalen CLI tritt dieses Problem nicht auf.

### <a name="possible-causes"></a>Mögliche Ursachen

Dies ist das Ergebnis eines bekannten Problems in Cloud Shell: [*Beim Abrufen des Tokens aus Cloud Shell tritt zeitweilig „400 Clientfehler: Ungültige Anforderung“ auf*](https://github.com/Azure/azure-cli/issues/11749).

Dies stellt ein Problem mit Authentifizierungstoken der Azure Digital Twins-Instanz und der standardmäßigen Authentifizierung von Cloud Shell dar, die auf [verwalteten Identitäten](../active-directory/managed-identities-azure-resources/overview.md) basiert. Der Problembehandlungsschritt der Ausführung von `az login` befreit Sie von der Authentifizierung über verwaltete Identitäten und überspringt so dieses Problem.

Dies betrifft nicht die Azure Digital Twins-Befehle aus den Befehlsgruppen `az dt` oder `az dt endpoint`, da sie eine andere Art von Authentifizierungstoken (ARM-basiert) verwenden, die kein Problem mit der Authentifizierung über verwaltete Identitäten von Cloud Shell hat.

## <a name="missing-role-assignment-after-scripted-setup"></a>Fehlende Rollenzuweisung nach Setup per Skript

Bei einigen Benutzern treten möglicherweise Probleme bei der Rollenzuweisung gemäß folgender Anleitung auf: [ *Einrichten einer Instanz und der Authentifizierung (per Skript)*](how-to-set-up-instance-scripted.md). Das Skript gibt keinen Fehler an, die Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) wird dem Benutzer jedoch nicht erfolgreich zugewiesen. Dieses Problem beeinträchtigt die Fähigkeit zum Erstellen weiterer Ressourcen.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Um zu ermitteln, ob die Rollenzuweisung nach dem Ausführen des Skripts erfolgreich eingerichtet wurde, befolgen Sie die Anweisungen im Abschnitt [*Überprüfen der Benutzerrollenzuweisung*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) des Artikels zum Setup. Wenn der Benutzer nicht mit dieser Rolle angezeigt wird, wirkt sich dieses Problem auf Sie aus.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Um das Problem zu beheben, können Sie die Rollenzuweisung manuell mithilfe der CLI oder des Azure-Portals einrichten. 

Befolgen Sie diese Anweisungen:
* [BEFEHLSZEILENSCHNITTSTELLE (CLI)](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Mögliche Ursachen

Für Benutzer, die sich mit einem persönlichen [Microsoft-Konto (MSA)](https://account.microsoft.com/account) angemeldet haben, unterscheidet sich die Prinzipal-ID des Benutzers, die ihn in Befehlen wie diesem identifiziert, möglicherweise von der E-Mail-Adresse für die Anmeldung des Benutzers. Dadurch ist es für das Skript schwierig, diese zu ermitteln und die Rolle richtig zuzuweisen.

## <a name="issue-with-interactive-browser-authentication"></a>Problem bei interaktiver Browserauthentifizierung

Beim Schreiben von Authentifizierungscode in Azure Digital Twins-Anwendungen mit Version  **1.2.0** der **Bibliothek [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)** treten möglicherweise Probleme mit der [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)-Methode auf.

Dies ist nicht die neueste Version der Bibliothek. Die neueste Version ist **1.2.2**.

Die betroffene Methode wird in den folgenden Artikeln verwendet: 
* [*Tutorial: Codieren einer Client-App*](tutorial-code.md)
* [*Verwenden Schreiben von App-Authentifizierungscode*](how-to-authenticate-client.md)
* [*Verwenden der Azure Digital Twins-APIs und SDKs*](how-to-use-apis-sdks.md)

Das Problem umfasst die Fehlermeldung „Azure.Identity.AuthenticationFailedException“, wenn versucht wird, sich in einem Browserfenster zu authentifizieren. Möglicherweise wird das Browserfenster nicht vollständig gestartet, oder der Benutzer wird scheinbar erfolgreich authentifiziert, während in der Clientanwendung weiterhin der Fehler auftritt.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Um das Problem zu beheben, aktualisieren Sie Ihre Anwendungen für die Verwendung von `Azure.Identity`, Version **1.2.2**. Mit dieser Version der Bibliothek sollte der Browser wie erwartet geladen werden und die Authentifizierung durchführen.

### <a name="possible-causes"></a>Mögliche Ursachen

Dies bezieht sich auf ein offenes Problem mit der neuesten Version der Bibliothek `Azure.Identity` (Version  **1.2.0** ): [*Bei der Verwendung von InteractiveBrowserCredential kann keine Authentifizierung durchgeführt werden*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Dieses Problem tritt auf, wenn Sie in der Azure Digital Twins-Anwendung Version  **1.2.0** verwenden oder wenn Sie dem Projekt die Bibliothek hinzufügen, ohne eine Version anzugeben (da dann ebenfalls standardmäßig die neueste Version verwendet wird).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über die Sicherheit und Berechtigungen in Azure Digital Twins:
* [*Konzepte: Sicherheit für Azure Digital Twins-Lösungen*](concepts-security.md)