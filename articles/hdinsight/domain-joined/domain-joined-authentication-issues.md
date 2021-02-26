---
title: Authentifizierungsprobleme in Azure HDInsight
description: Authentifizierungsprobleme in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: b5a55b3b5f9affcd9f34e1c0d4c439a7ada8c0b9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095123"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Authentifizierungsprobleme in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

Bei sicheren Clustern, die durch Azure Data Lake (Gen1 oder Gen2) unterstützt werden, versucht das HDI-Gateway zunächst, ein OAuth-Token aus Azure Active Directory (Azure AD) und anschließend ein Kerberos-Ticket aus Azure AD DS abzurufen, wenn sich Domänenbenutzer über das HDI-Gateway bei den Clusterdiensten anmelden (z.B. Anmelden am Apache Ambari-Portal). Die Authentifizierung kann in beiden Phasen fehlschlagen. Dieser Artikel soll Ihnen dabei helfen, einige dieser Probleme zu debuggen.

Wenn die Authentifizierung fehlschlägt, werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Wenn Sie dieses Dialogfeld abbrechen, wird die Fehlermeldung ausgegeben. Im Anschluss finden Sie einige gängige Fehlermeldungen:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant oder unauthorized_client, 50126

### <a name="issue"></a>Problem

Fehler bei der Anmeldung für Verbundbenutzer mit dem Fehlercode 50126 (Anmeldung ist für Cloudbenutzer erfolgreich). Die Fehlermeldung ähnelt der folgenden:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Ursache

Azure AD-Fehlercode 50126 bedeutet, dass die `AllowCloudPasswordValidation`-Richtlinie nicht vom Mandanten festgelegt wurde.

### <a name="resolution"></a>Lösung

Der globale Administrator des Azure AD-Mandanten muss Azure AD für die Verwendung von Kennworthashes für durch ADFS unterstützte Benutzer aktivieren.  Wenden Sie die `AllowCloudPasswordValidationPolicy` wie im Artikel [Verwenden des Enterprise-Sicherheitspakets in HDInsight](../domain-joined/apache-domain-joined-architecture.md) gezeigt an.

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant oder unauthorized_client, 50034

### <a name="issue"></a>Problem

Bei der Anmeldung tritt der Fehlercode 50034 auf. Die Fehlermeldung ähnelt der folgenden:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Ursache

Der Benutzername ist falsch (nicht vorhanden). Der Benutzer verwendet nicht denselben Benutzernamen, der im Azure-Portal verwendet wird.

### <a name="resolution"></a>Lösung

Verwenden Sie den gleichen Benutzernamen, der in diesem Portal funktioniert.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant oder unauthorized_client, 50053

### <a name="issue"></a>Problem

Das Benutzerkonto ist gesperrt, Fehlercode 50053. Die Fehlermeldung ähnelt der folgenden:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Ursache

Zu viele Anmeldeversuche mit einem falschen Kennwort.

### <a name="resolution"></a>Lösung

Warten Sie ungefähr 30 Minuten, und beenden Sie alle Anwendungen, die möglicherweise versuchen, sich zu authentifizieren.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant oder unauthorized_client, 50053 (#2)

### <a name="issue"></a>Problem

Das Kennwort ist abgelaufen, Fehlercode 50053. Die Fehlermeldung ähnelt der folgenden:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Ursache

Das Kennwort ist abgelaufen.

### <a name="resolution"></a>Lösung

Ändern Sie das Kennwort im Azure-Portal (auf Ihrem lokalen System), und warten Sie 30 Minuten, bis die Synchronisierung abgeschlossen ist.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problem

Sie erhalten die Fehlermeldung `interaction_required`.

### <a name="cause"></a>Ursache

Die Richtlinie für bedingten Zugriff oder die MFA wird auf den Benutzer angewendet. Da die interaktive Authentifizierung noch nicht unterstützt wird, muss der Benutzer oder der Cluster von MFA/bedingtem Zugriff ausgenommen werden. Wenn Sie den Cluster (auf der IP-Adresse basierende Ausnahmerichtlinie) ausnehmen möchten, vergewissern Sie sich, dass die AD-`ServiceEndpoints` für dieses VNET aktiviert sind.

### <a name="resolution"></a>Lösung

Verwenden Sie die Richtlinie für bedingten Zugriff, und schließen Sie die HDInsight-Cluster von MFA aus, wie unter [Konfigurieren eines HDInsight-Clusters mit dem Enterprise-Sicherheitspaket mithilfe von Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md) gezeigt.

---

## <a name="sign-in-denied"></a>Anmeldung verweigert

### <a name="issue"></a>Problem

Die Anmeldung wird verweigert.

### <a name="cause"></a>Ursache

In dieser Phase ist die OAuth-Authentifizierung nicht das Problem, sondern die Kerberos-Authentifizierung. Wenn dieser Cluster durch ADLS gestützt wird, war die OAuth-Anmeldung erfolgreich, bevor die Kerberos-Authentifizierung versucht wird. Bei WASB-Clustern wird die OAuth-Anmeldung nicht versucht. Es kann viele Gründe für Kerberos-Fehler geben: nicht synchronisierte Kennworthashes, das Benutzerkonto ist in Azure AD DS gesperrt usw. Kennworthashes werden nur synchronisiert, wenn der Benutzer das Kennwort ändert. Wenn Sie die Azure AD DS-Instanz erstellen, wird damit begonnen, Kennwörter zu synchronisieren, die nach der Erstellung geändert werden. Kennwörter, die davor festgelegt wurden, werden nicht rückwirkend synchronisiert.

### <a name="resolution"></a>Lösung

Wenn Sie der Ansicht sind, dass Kennwörter ggf. nicht synchron sind, ändern Sie das Kennwort, und warten Sie einige Minuten, bis die Synchronisierung abgeschlossen ist.

Verwenden Sie SSH. Versuchen Sie, sich mit den gleichen Benutzeranmeldeinformationen über einen Computer zu authentifizieren (kinit), der in die Domäne eingebunden ist. Verwenden Sie SSH für den Haupt-/Edgeknoten als lokaler Benutzer, und führen Sie dann kinit aus.

---

## <a name="kinit-fails"></a>kinit schlägt fehl

### <a name="issue"></a>Problem

Kinit schlägt fehl.

### <a name="cause"></a>Ursache

Verschiedene Ursachen.

### <a name="resolution"></a>Lösung

Damit kinit erfolgreich ist, müssen Sie Ihren `sAMAccountName` kennen (dies ist der kurze Kontoname ohne Bereich). `sAMAccountName` ist normalerweise das Kontopräfix (z.B. bob in `bob@contoso.com`). Für einige Benutzer ist dies möglicherweise anders. Sie müssen im Verzeichnis browsen bzw. es durchsuchen können, um Ihren `sAMAccountName` zu ermitteln.

Möglichkeiten zur Ermittlung des `sAMAccountName`:

* Wenn Sie sich als lokaler Ambari-Administrator bei Ambari anmelden können, sehen Sie sich die Liste der Benutzer an.

* Wenn Sie über einen [in die Domäne eingebundenen Windows-Computer](../../active-directory-domain-services/tutorial-create-management-vm.md) verfügen, können Sie die Windows AD-Standardtools zum Browsen verwenden. Hierfür ist ein funktionierendes Konto in der Domäne erforderlich.

* Im Hauptknoten können Sie mithilfe von SAMBA-Befehlen suchen. Dies erfordert eine gültige Kerberos-Sitzung (erfolgreiches kinit). net ads search "(userPrincipalName=bob*)"

    Die Such-/Browsergebnisse sollten das `sAMAccountName`-Attribut anzeigen. Sie können auch andere Attribute wie `pwdLastSet`, `badPasswordTime`,`userPrincipalName` usw. untersuchen, um festzustellen, ob diese Eigenschaften Ihren Erwartungen entsprechen.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit schlägt mit einem Fehler bei der Vorauthentifizierung fehl

### <a name="issue"></a>Problem

Kinit schlägt mit dem Fehler `Preauthentication` fehl.

### <a name="cause"></a>Ursache

Falscher Benutzername oder falsches Kennwort.

### <a name="resolution"></a>Lösung

Überprüfen Sie Ihren Benutzernamen und Ihr Kennwort. Überprüfen Sie außerdem die oben beschriebenen Eigenschaften. Um ausführliches Debuggen zu aktivieren, führen Sie `export KRB5_TRACE=/tmp/krb.log` aus der Sitzung aus, bevor Sie kinit versuchen.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Fehler beim Auftrag/HDFS-Befehl aufgrund von TokenNotFoundException

### <a name="issue"></a>Problem

Der Auftrag/HDFS-Befehl schlägt aufgrund von `TokenNotFoundException` fehl.

### <a name="cause"></a>Ursache

Das erforderliche OAuth-Zugriffstoken wurde für die erfolgreiche Ausführung des Auftrags bzw. Befehls nicht gefunden. Der ADLS-/ABFS-Treiber versucht, das OAuth-Zugriffstoken aus dem Anmeldeinformationendienst abzurufen, bevor Speicheranforderungen ausgegeben werden. Dieses Token wird registriert, wenn Sie sich als derselbe Benutzer am Ambari-Portal anmelden.

### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass Sie sich mit dem Benutzernamen, dessen Identität zum Ausführen des Auftrags verwendet wird, erfolgreich am Ambari-Portal angemeldet haben.

---

## <a name="error-fetching-access-token"></a>Fehler beim Abrufen des Zugriffstokens

### <a name="issue"></a>Problem

Benutzer erhält Fehlermeldung `Error fetching access token`.

### <a name="cause"></a>Ursache

Dieser Fehler tritt zeitweilig auf, wenn Benutzer versuchen, mithilfe von ACLs auf ADLS Gen2 zuzugreifen, und das Kerberos-Token abgelaufen ist.

### <a name="resolution"></a>Lösung

* Bereinigen Sie für Azure Data Lake Storage Gen1 den Browsercache, und melden Sie sich erneut bei Ambari an.

* Führen Sie für Azure Data Lake Storage Gen2 `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` für den Benutzer aus, als der sich der Benutzer anmelden möchte.

---

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]