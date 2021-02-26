---
title: Fehlerreferenz für Registrierungsintegritätsprüfung
description: Fehlercodes und mögliche Lösungen für Probleme, die beim Ausführen des Diagnosebefehls az acr check-health in Azure Container Registry gefunden wurden.
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: 05ae5a7ac19bb7748d5313ccb4974b639ab52d9c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061867"
---
# <a name="health-check-error-reference"></a>Fehlerreferenz für die Integritätsprüfung

Im Folgenden finden Sie Informationen zu Fehlercodes, die vom Befehl [az acr check-health][az-acr-check-health] zurückgegeben werden. Für jeden Fehler werden mögliche Lösungen aufgeführt.

Informationen zum Ausführen von `az acr check-healh` finden Sie unter [Überprüfen der Integrität einer Azure-Containerregistrierung](container-registry-check-health.md).

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Dieser Fehler weist darauf hin, dass der Docker-Client für die CLI nicht gefunden wurde. Infolgedessen werden die folgenden zusätzlichen Prüfungen nicht durchgeführt: Ermittlung der Docker-Version, Auswertung des Docker-Daemonstatus und Ausführung eines docker pull-Befehls.

*Mögliche Lösungen*: Installieren Sie den Docker-Client. Fügen Sie den Systemvariablen den Docker-Pfad hinzu.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Dieser Fehler weist darauf hin, dass der Docker-Daemonstatus nicht verfügbar ist oder er nicht mit der CLI erreicht werden konnte. Infolgedessen sind Docker-Vorgänge (wie `docker login` und `docker pull`) über die CLI nicht verfügbar.

*Mögliche Lösungen*: Starten Sie den Docker-Daemon neu, oder bestätigen Sie, dass er ordnungsgemäß installiert ist.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Dieser Fehler weist darauf hin, dass die CLI den Befehl `docker --version` nicht ausführen konnte.

*Mögliche Lösungen*: Versuchen Sie, den Befehl manuell auszuführen, stellen Sie sicher, dass Sie über die neueste CLI-Version verfügen, und untersuchen Sie die Fehlermeldung.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Dieser Fehler weist darauf hin, dass die CLI kein Beispielimage in Ihre Umgebung pullen konnte.

*Mögliche Lösungen*: Vergewissern Sie sich, dass alle Komponenten, die erforderlich sind, um ein Image zu pullen, ordnungsgemäß ausgeführt werden.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Dieser Fehler weist darauf hin, dass der Helm-Client von der CLI nicht gefunden wurde, was andere Helm-Vorgänge ausschließt.

*Mögliche Lösungen*: Stellen Sie sicher, dass der Helm-Client installiert ist und der Pfad den Systemumgebungsvariablen hinzugefügt wurde.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Dieser Fehler weist darauf hin, dass die CLI die installierte Helm-Version nicht ermitteln konnte. Dies kann passieren, wenn die verwendete Version der Azure CLI (oder die Helm-Version) veraltet ist.

*Mögliche Lösungen*: Führen Sie ein Update auf die neueste Azure CLI-Version oder auf die empfohlene Helm-Version durch. Führen Sie den Befehl manuell aus, und untersuchen Sie die Fehlermeldung.

## <a name="cmk_error"></a>CMK_ERROR

Dieser Fehler weist darauf hin, dass die Registrierung nicht auf die benutzerseitig oder systemseitig zugewiesene verwaltete Identität zugreifen kann, die zum Konfigurieren der Registrierungsverschlüsselung mit einem kundenseitig verwalteten Schlüssel verwendet wurde. Die verwaltete Identität wurde möglicherweise gelöscht.  

*Mögliche Lösung*: Informationen zum Beheben des Problems und zum Rotieren des Schlüssels mithilfe einer anderen verwalteten Identität finden Sie in den Schritten zur Problembehandlung [der benutzerseitig zugewiesenen Identität](container-registry-customer-managed-keys.md#troubleshoot).

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Dieser Fehler weist darauf hin, dass das DNS für den angegebenen Registrierungsanmeldeserver gepingt wurde, aber nicht geantwortet hat, was bedeutet, dass er nicht verfügbar ist. Dies kann Konnektivitätsprobleme bedeuten. Alternativ kann die Registrierung auch nicht vorhanden sein, der Benutzer hat möglicherweise nicht die Berechtigungen für die Registrierung (um seinen Anmeldeserver ordnungsgemäß abzurufen), oder die Zielregistrierung befindet sich in einer anderen Cloud als die in der Azure CLI verwendete.

*Mögliche Lösungen*: Überprüfen Sie die Konnektivität, überprüfen Sie die Rechtschreibung der Registrierung und ob die Registrierung vorhanden ist, überprüfen Sie, ob der Benutzer über die richtigen Berechtigungen verfügt und ob die Cloud der Registrierung die gleiche ist, die in der Azure CLI verwendet wird.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Dieser Fehler weist darauf hin, dass der Abfrageendpunkt für die angegebene Registrierung mit dem HTTP-Status 403 Forbidden geantwortet hat. Dieser Fehler weist darauf hin, dass Benutzer keinen Zugriff auf die Registrierung besitzen, wahrscheinlich aufgrund der Konfiguration eines virtuellen Netzwerks oder weil der Zugriff auf den öffentlichen Endpunkt der Registrierung unzulässig ist. Führen Sie `az acr show --query networkRuleSet --name <registry>` aus, um die zurzeit konfigurierten Firewallregeln anzuzeigen.

*Mögliche Lösungen*: Entfernen Sie Regeln für virtuelle Netzwerke, oder fügen Sie die aktuelle IP-Clientadresse der Zulassungsliste hinzu.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Dieser Fehler weist darauf hin, dass der Abfrageendpunkt der Zielregistrierung keine Abfrage ausgegeben hat.

*Mögliche Lösungen*: Versuchen Sie es später noch einmal. Wenn der Fehler weiterhin auftritt, öffnen Sie ein Issue unter https://aka.ms/acr/issues.

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Dieser Fehler weist darauf hin, dass der Abfrageendpunkt der Zielregistrierung eine Abfrage ausgestellt hat, die Registrierung aber keine Azure Active Directory-Authentifizierung unterstützt.

*Mögliche Lösungen*: Versuchen Sie eine andere Authentifizierungsmöglichkeit, z.B. mit Administratoranmeldeinformationen. Öffnen Sie ein Problem unter https://aka.ms/acr/issues, wenn Benutzer die Authentifizierung mit Azure Active Directory ausführen müssen.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Dieser Fehler weist darauf hin, dass der Registrierungsanmeldeserver nicht mit einem Aktualisierungstoken geantwortet hat und der Zugriff auf die Zielregistrierung daher verweigert wurde. Dieser Fehler kann auftreten, wenn der Benutzer nicht über die richtigen Berechtigungen für die Registrierung verfügt oder wenn die Benutzeranmeldeinformationen für die Azure CLI veraltet sind.

*Mögliche Lösungen*: Überprüfen Sie, ob der Benutzer über die richtigen Berechtigungen für die Registrierung verfügt. Führen Sie `az login` aus, um Berechtigungen, Token und Anmeldeinformationen zu aktualisieren.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Dieser Fehler bedeutet, dass der Registrierungsanmeldeserver nicht mit einem Zugriffstoken geantwortet hat und der Zugriff auf die Zielregistrierung daher verweigert wurde. Dieser Fehler kann auftreten, wenn der Benutzer nicht über die richtigen Berechtigungen für die Registrierung verfügt oder wenn die Benutzeranmeldeinformationen für die Azure CLI veraltet sind.

*Mögliche Lösungen*: Überprüfen Sie, ob der Benutzer über die richtigen Berechtigungen für die Registrierung verfügt. Führen Sie `az login` aus, um Berechtigungen, Token und Anmeldeinformationen zu aktualisieren.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Dieser Fehler bedeutet, dass der Client keine sichere Verbindung mit der Containerregistrierung herstellen konnte. Dieser Fehler tritt im Allgemeinen auf, wenn Sie einen Proxyserver ausführen oder verwenden.

*Mögliche Lösungen*: Weitere Informationen zum Arbeiten hinter einem Proxy [finden Sie hier](/cli/azure/use-cli-effectively).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Dieser Fehler weist darauf hin, dass die CLI den Anmeldeserver der angegebenen Registrierung nicht finden konnte und kein Standardsuffix für die aktuelle Cloud gefunden wurde. Dieser Fehler kann auftreten, wenn die Registrierung nicht vorhanden ist, wenn der Benutzer nicht über die richtigen Berechtigungen für die Registrierung verfügt, wenn die Cloud der Registrierung und die aktuelle Azure CLI Cloud nicht übereinstimmen oder wenn die Version der Azure CLI veraltet ist.

*Mögliche Lösungen*: Vergewissern Sie sich, dass die Schreibweise richtig und die Registrierung vorhanden ist. Stellen Sie sicher, dass der Benutzer die richtigen Berechtigungen für die Registrierung besitzt und dass die Clouds der Registrierung und der CLI-Umgebung übereinstimmen. Aktualisieren Sie die Azure CLI auf die neueste Version.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Dieser Fehler bedeutet, dass die CLI nicht mit der aktuell installierten Version von Docker/Notary kompatibel ist. Versuchen Sie, die Version von „notary.exe“ auf eine Version vor 0.6.0 herabzustufen, indem Sie den Notary-Client Ihrer Docker-Installation manuell ersetzen, um dieses Problem zu beheben.

## <a name="next-steps"></a>Nächste Schritte

Optionen zum Überprüfen der Integrität einer Registrierung finden Sie unter [Überprüfen der Integrität einer Azure-Containerregistrierung](container-registry-check-health.md).

Häufig gestellte Fragen zu Azure Container Registry und andere bekannte Probleme finden Sie unter [FAQ](container-registry-faq.md).





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
