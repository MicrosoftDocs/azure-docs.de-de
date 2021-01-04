---
title: Problembehandlung bei der selbstgehosteten Integration Runtime in Azure Data Factory
description: Hier erfahren Sie, wie Sie eine Problembehandlung im Fall von Problemen bei der selbstgehosteten Integration Runtime in Azure Data Factory durchführen können.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: 635178999398287649d8630fc5262a385afc48b2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341783"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problembehandlung bei der selbstgehosteten Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden allgemeine Methoden zur Problembehandlung bei selbstgehosteter Integration Runtime in Azure Data Factory erläutert.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Erfassen von selbstgehosteten IR-Protokollen aus Azure Data Factory

Bei fehlgeschlagenen Aktivitäten in selbstgehosteter IR/freigegebener IR unterstützt Azure Data Factory das Anzeigen und Hochladen von Fehlerprotokollen. Sie können die folgenden Schritte zum Abrufen der Fehlerberichts-ID ausführen und dann diese ID eingeben, um verwandte bekannte Probleme zu finden.

1. Wechseln Sie zur Seite **Aktivitätsausführungen**.

1. Klicken Sie unter der Spalte **FEHLER** auf die unten gezeigte Schaltfläche.

    ![Seite „Aktivitätsausführungen“](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Verwandte Protokolle für die fehlgeschlagene Aktivitätsausführung werden angezeigt. Klicken Sie auf die Schaltfläche **Protokolle senden**, um weitere Hilfe zu erhalten.

    ![Protokolle senden](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Sie können die Protokolle auswählen, die Sie senden möchten. Bei der *selbstgehosteten IR* können Sie Protokolle zu einer fehlgeschlagenen Aktivität oder alle Protokolle auf den Knoten der selbstgehosteten IR hochladen. Bei *Freigegebene IR* können Sie nur Protokolle hochladen, die sich auf eine fehlgeschlagene Aktivität beziehen.

    ![Protokolle auswählen](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Bewahren Sie beim Hochladen der Protokolle einen Datensatz der Berichts-ID auf, wenn Sie weitere Unterstützung bei der Problemlösung benötigen.

    ![Protokolle hochladen](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Die Anforderungen zum Anzeigen und Hochladen von Protokollen werden für alle selbstgehosteten Online-IR-Instanzen ausgeführt. Stellen Sie sicher, dass alle selbstgehosteten IR-Instanzen online sind, falls Protokolle fehlen. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Allgemeiner Fehler oder Fehler bei der selbstgehosteten IR

### <a name="out-of-memory-issue"></a>Arbeitsspeicherproblem

#### <a name="symptoms"></a>Symptome

Das Problem „OutOfMemoryException“ tritt auf, wenn Sie versuchen, die Lookup-Aktivität mit der verknüpften IR oder der selbstgehosteten IR auszuführen.

#### <a name="cause"></a>Ursache

Eine neue Aktivität kann auf ein OOM-Problem (OutOfMemory) stoßen, wenn der IR-Computer momentan eine hohe Speicherauslastung aufweist. Das Problem kann durch eine große Anzahl gleichzeitiger Aktivitätsausführungen verursacht werden, und der Fehler ist beabsichtigt.

#### <a name="resolution"></a>Lösung

Überprüfen Sie die Ressourcennutzung und gleichzeitige Aktivitätsausführung auf dem IR-Knoten. Passen Sie die interne und die Auslösezeit von Aktivitätsausführungen an, um zu viele gleichzeitige Ausführungen auf demselben IR-Knoten zu vermeiden.


### <a name="tlsssl-certificate-issue"></a>Problem bei TLS/SSL-Zertifikat

#### <a name="symptoms"></a>Symptome

Beim Versuch, das TLS/SSL-Zertifikat (erweitert) aus **Configuration Manager für selbstgehostete IR** -> **Remotezugriff über das Intranet** zu aktivieren, wird nach dem Auswählen des TLS/SSL-Zertifikats der folgende Fehler angezeigt:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

Im vorstehenden Fall verwendet der Benutzer ein Zertifikat mit „Microsoft.com“ als letztem Element.

#### <a name="cause"></a>Ursache

Dies ist ein bekanntes Problem in WCF: Die WCF-TLS/SSL-Überprüfung überprüft nur das letzte „DNSName“ im SAN. 

#### <a name="resolution"></a>Lösung

In der selbstgehosteten IR von Azure Data Factory v2 wird ein Platzhalterzertifikat unterstützt. Dieses Problem tritt normalerweise auf, weil das SSL-Zertifikat falsch ist. Das letzte „DNSName“ im SAN muss gültig sein. Führen Sie die folgenden Schritte aus, um dies zu überprüfen. 
1.  Öffnen Sie die Verwaltungskonsole, und überprüfen Sie in den „Zertifikatdetails“ sowohl *Betreff* als auch *Alternativer Antragstellername*. In diesem Fall ist beispielsweise das letzte Element in *Alternativer Antragstellername*, „DNS Name = microsoft.com.com“, nicht legitim.
2.  Bitten Sie das Unternehmen, von dem das Zertifikat ausgestellt wurde, den falschen DNS-Namen zu entfernen.

### <a name="concurrent-jobs-limit-issue"></a>Problem beim Limit für gleichzeitige Aufträge

#### <a name="symptoms"></a>Symptome

Beim Versuch, das Limit für gleichzeitige Aufträge über die Azure Data Factory-Benutzeroberfläche zu erhöhen, bleibt sie mit *Wird aktualisiert* hängen.
Der maximale Wert für gleichzeitige Aufträge wurde auf „24“ festgelegt, und Sie möchten die Anzahl erhöhen, damit Aufträge schneller ausgeführt werden können. Der minimale Wert, den Sie eingeben können, ist „3“, und der maximale Wert, den Sie eingeben können, ist „32“. Sie haben den Wert von „24“ auf „32“ erhöht und auf die Schaltfläche *Aktualisieren* geklickt. In der Benutzeroberfläche ist er auf *Wird aktualisiert* hängen geblieben, wie unten zu sehen ist. Nach der Aktualisierung wurde dem Kunden weiterhin der Wert „24“ angezeigt, der nie auf „32“ aktualisiert wurde.

![Status „Wird aktualisiert“](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Ursache

Es gibt eine Einschränkung bei der Einstellung, da der Wert abhängig ist von „logicCore“ (logischer Kern) und „Memory“ (Arbeitsspeicher) des Computers. Sie können ihn einfach auf einen kleineren Wert, z. B. „24“, anpassen und sich dann das Ergebnis ansehen.

> [!TIP] 
> - Ausführliche Informationen dazu, was die Anzahl der logischen Kerne ist und wie Sie die Anzahl der logischen Kerne des Computers ermitteln können, finden Sie in [diesem Artikel](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Ausführliche Informationen zum Berechnen der Datei „Math.log“ finden Sie in [diesem Artikel](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problem beim HA SSL-Zertifikat für selbstgehostete IR

#### <a name="symptoms"></a>Symptome

Der Arbeitsknoten der selbstgehosteten IR hat den folgenden Fehler gemeldet:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Ursache

Bei der Behandlung von Fällen im Zusammenhang mit dem SSL/TLS-Handshake könnten einige Probleme im Zusammenhang mit der Überprüfung der Zertifikatskette auftreten. 

#### <a name="resolution"></a>Lösung

- Hier ist eine schnelle und intuitive Möglichkeit zum Beheben von Fehlern beim Erstellen der X.509-Zertifikatkette.
 
    1. Exportieren Sie das Zertifikat, das überprüft werden muss. Wechseln Sie zu „Computerzertifikat verwalten“, suchen Sie das Zertifikat, das Sie überprüfen möchten, und klicken Sie mit der rechten Maustaste auf **Alle Tasks** -> **Exportieren**.
    
        ![Aufgaben exportieren](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Kopieren Sie das exportierte Zertifikat auf den Clientcomputer. 
    3. Führen Sie auf der Clientseite den folgenden Befehl in CMD aus. Dabei müssen Sie die Platzhalter *\<certificate path>* und *\<output txt file path>* durch verwandte Pfade ersetzen.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Beispiel:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Überprüfen Sie, ob in der TXT-Ausgabedatei ein Fehler vorliegt. Sie finden die Fehlerzusammenfassung am Ende der TXT-Datei.

        Beispiel: 

        ![Fehlerzusammenfassung](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Wenn am Ende der Protokolldatei kein Fehler angezeigt wird (siehe unten), wissen Sie, dass die Zertifikatskette auf dem Clientcomputer erfolgreich erstellt wurde.
        
        ![Kein Fehler in der Protokolldatei](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Wenn AIA, CDP und OCSP in der Zertifikatsdatei konfiguriert wurden, können Sie dies auf intuitivere Weise überprüfen.
 
    1. Sie können diese Informationen erhalten, indem Sie die Details eines Zertifikats überprüfen.
    
        ![Zertifikatdetail](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Führen Sie den Befehl unten aus. Dabei müssen Sie den Platzhalter *\<certificate path>* durch den zugehörigen Pfad des Zertifikats ersetzen.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Dann wird das **URL-Abruf-Tool** geöffnet. Sie können Zertifikate von AIA, CDP und OCSP durch Klicken auf die Schaltfläche **Abrufen** überprüfen.

        ![Schaltfläche „Abrufen“](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Die Zertifikatkette kann erfolgreich erstellt werden, wenn das Zertifikat von AIA „Überprüft“ und das Zertifikat von CDP oder OCSP ebenfalls „Überprüft“ lautet.

        Wenn beim Abrufen von AIA und CDP ein Fehler auftritt, wenden Sie sich an das Netzwerkteam, damit der Clientcomputer zum Verbinden mit der Ziel-URL vorbereitet wird. Es reicht aus, wenn der HTTP-Pfad oder der LDAP-Pfad überprüft werden kann.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Selbstgehostete IR konnte Datei oder Assembly nicht laden

#### <a name="symptoms"></a>Symptome

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Beispiel: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Ursache

Wenn Sie den Prozessmonitor verwenden, wird folgendes Ergebnis angezeigt:

[![Prozessmonitor](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Sie können den Filter so wie im folgenden Screenshot festlegen.
> Sie werden dadurch informiert, dass sich die DLL-Datei **System.ValueTuple** nicht im GAC-bezogenen Ordner, im Ordner *C:\Programme\Microsoft Integration Runtime\4.0\Gateway* oder im Ordner *C:\Programme\Microsoft Integration Runtime\4.0\Shared* befindet.
> Im Grunde genommen wird die DLL zuerst aus dem Ordner *GAC*, dann aus *Freigegeben* und schließlich aus dem Ordner *Gateway* geladen. Deshalb können Sie die DLL in jeden beliebigen Pfad einfügen, was hilfreich sein kann.

![Filter einrichten](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Lösung

Sie können feststellen, dass sich die Datei **System.ValueTuple.dll** unter dem Ordner *C:\Programme\Microsoft Integration Runtime\4.0\Gateway\DataScan* befindet. Kopieren Sie die Datei **System.ValueTuple.dll** in den Ordner *C:\Programme\Microsoft Integration Runtime\4.0\Gateway*, um das Problem zu lösen.

Mit derselben Methode können Sie andere Probleme des Typs „Datei oder Assembly fehlt“ lösen.

#### <a name="more-information"></a>Weitere Informationen

Der Grund für die Anzeige der Datei „System.ValueTuple.dll“ unter *%windir%\Microsoft.NET\assembly* und *%windir%\assembly* ist, dass es sich hier um ein .NET-Verhalten handelt. 

In der nachstehenden Fehlermeldung können Sie genau sehen, dass die Assembly *System.ValueTuple* nicht vorhanden ist. Ein solches Problem tritt also auf, wenn die Anwendung versucht, die Assembly *System.ValueTuple.dll* zu überprüfen.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Weitere Informationen zu GAC finden Sie [in diesem Artikel](/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>„Überwachen von Schlüssel für selbstgehostete IR“ fehlt

#### <a name="symptoms"></a>Symptome

Die selbstgehostete Integration Runtime wechselt plötzlich ohne Schlüssel in den Offlinemodus, und im Ereignisprotokoll wird folgende Fehlermeldung angezeigt: `Authentication Key is not assigned yet`

![Authentifizierungsschlüssel fehlt](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Ursache

- Der Knoten der selbstgehosteten IR oder die logische selbstgehostete IR im Portal wurde gelöscht.
- Eine saubere Deinstallation ist abgeschlossen.

#### <a name="resolution"></a>Lösung

Wenn keiner der vorstehenden Gründe zutrifft, können Sie zum Ordner *%ProgramData%\microsoft\data transfer\datamanagementgateway* wechseln und überprüfen, ob die Datei **Konfigurationen** gelöscht wurde. Wenn Sie gelöscht wurde, folgen Sie den Anweisungen [hier](https://www.netwrix.com/how_to_detect_who_deleted_file.html), um zu überprüfen, wer die Datei gelöscht hat.

![Datei „Konfigurationen“ überprüfen](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Zwei lokale Datenspeicher können mit der selbstgehosteten IR nicht überbrückt werden

#### <a name="symptoms"></a>Symptome

Nachdem Sie selbstgehostete IRs für Quell- und Zieldatenspeicher erstellt haben, möchten Sie die beiden IRs miteinander verbinden, um einen Kopiervorgang abzuschließen. Wenn die Datenspeicher in unterschiedlichen VNETs konfiguriert wurden oder den Gatewaymechanismus nicht verstehen können, treten Fehler auf wie: *der Treiber für die Quelle wird in der Ziel-IR nicht gefunden*; *die Ziel-IR kann auf die Quelle nicht zugreifen*.
 
#### <a name="cause"></a>Ursache

Die selbstgehostete IR ist als zentraler Knoten für eine Kopieraktivität konzipiert und nicht als Client-Agent, der für jeden Datenspeicher installiert werden muss.
 
Im vorstehenden Fall sollte der verknüpfte Dienst für jeden Datenspeicher mit derselben IR erstellt werden, und die IR sollte über das Netzwerk auf beide Datenspeicher zugreifen können. Unabhängig davon, ob die IR mit dem Quelldatenspeicher, dem Zieldatenspeicher oder auf einem dritten Computer installiert wurde – wenn zwei verknüpfte Dienste mit unterschiedlichen IRs erstellt, aber in derselben Kopieraktivität verwendet werden, wird die Ziel-IR verwendet, und die Treiber für beide Datenspeicher müssen auf dem IR-Zielcomputer installiert werden.

#### <a name="resolution"></a>Lösung

Installieren Sie Treiber für Quelle und Ziel auf der Ziel-IR, und stellen Sie sicher, dass sie auf den Quelldatenspeicher zugreifen kann.
 
Wenn der Datenverkehr das Netzwerk zwischen zwei Datenspeichern (die beispielsweise in zwei VNETs konfiguriert wurden) nicht durchlaufen kann, können Sie den Kopiervorgang in einer einzigen Aktivität eventuell auch dann nicht beenden, wenn die IR installiert wurde. In diesem Fall können Sie zwei Kopieraktivitäten mit zwei IRs jeweils in einem VNET erstellen: 1 IR zum Kopieren aus Datenspeicher 1 in Azure Blob Storage, eine weitere IR zum Kopieren aus Azure Blob Storage in Datenspeicher 2. Dies könnte die Anforderung zur Verwendung der IR zum Erstellen einer Brücke simulieren, die zwei getrennte Datenspeicher miteinander verbindet.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Synchronisierungsproblem bei Anmeldeinformationen führt zum Verlust von Anmeldeinformationen aus HA

#### <a name="symptoms"></a>Symptome

Die Datenquellen-Anmeldeinformationen „XXXXXXXXXX“ wurden aus dem aktuellen Integration Runtime-Knoten mit Nutzlast gelöscht, „als Sie den Verknüpfungsdienst im Azure-Portal gelöscht haben, oder der Task weist die falsche Nutzlast auf. Erstellen Sie einen neuen Linkdienst mit Ihren Anmeldeinformationen“.

#### <a name="cause"></a>Ursache

Ihre selbstgehostete IR wurde im Hochverfügbarkeitsmodus mit zwei Knoten erstellt, die sich aber nicht im Status „Synchronisierung von Anmeldeinformationen“ befinden. Dies bedeutet, dass die im Verteilerknoten gespeicherten Anmeldeinformationen mit anderen Workerknoten nicht synchronisiert werden. Wenn ein Failover vom Verteilerknoten zum Workerknoten erfolgt, die Anmeldeinformationen aber nur im vorherigen Verteilerknoten vorhanden waren, schlägt der Task beim Versuch eines Zugriffs auf die Anmeldeinformationen fehl, und der vorstehende Fehler wird angezeigt.

#### <a name="resolution"></a>Lösung

Die einzige Möglichkeit zur Vermeidung dieses Problems besteht darin sicherzustellen, dass zwei Knoten den Status „Synchronisierung von Anmeldeinformationen“ haben. Andernfalls müssen Sie die Anmeldeinformationen für den neuen Verteiler erneut einfügen.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Das Zertifikat kann nicht ausgewählt werden, weil der private Schlüssel fehlt

#### <a name="symptoms"></a>Symptome

1.  Importieren Sie eine PFX-Datei in den Zertifikatspeicher.
2.  Wenn Sie das Zertifikat über die Benutzeroberfläche von IR Configuration Manager auswählen, wird der folgende Fehler angezeigt:

    ![Privater Schlüssel fehlt](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Ursache

- Das Benutzerkonto hat eine geringfügige Berechtigung und kann auf den privaten Schlüssel nicht zugreifen.
- Das Zertifikat wurde als Signatur, aber nicht als Schlüsselaustausch, generiert.

#### <a name="resolution"></a>Lösung

1.  Verwenden Sie zur Ausführung der Benutzeroberfläche ein privilegiertes Konto, das auf den privaten Schlüssel zugreifen kann.
2.  Führen Sie folgenden Befehl zum Importieren des Zertifikats aus:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Einrichten der selbstgehosteten IR

### <a name="the-integration-runtime-registration-error"></a>Der Integration Runtime-Registrierungsfehler 

#### <a name="symptoms"></a>Symptome

Manchmal möchten Sie die selbstgehostete IR beispielsweise aus den folgenden Gründen in einem anderen Konto ausführen:
- Die Unternehmensrichtlinie lässt das Dienstkonto nicht zu.
- Eine Authentifizierung ist erforderlich.

Nachdem Sie im Dienstbereich das Dienstkonto geändert haben, stellen Sie möglicherweise fest, dass die Integration Runtime nicht mehr funktioniert.

![IR-Registrierungsfehler](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Ursache

Es gibt viele Ressourcen, bei denen der Zugriff nur dem Dienstkonto gewährt wird. Wenn Sie das Dienstkonto in ein anderes Konto ändern, bleibt die Berechtigung für alle abhängigen Ressourcen unverändert.

#### <a name="resolution"></a>Lösung

Wechseln Sie zum Integration Runtime-Ereignisprotokoll, um den Fehler zu überprüfen.

![IR-Ereignisprotokoll](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Wenn der Fehler wie oben als *UnauthorizedAccessException* angezeigt wird, führen Sie die folgenden Anweisungen aus:


1. Überprüfen Sie im Windows-Dienstbereich das Dienstanmeldekonto *DIAHostService*.

    ![Dienstanmeldekonto](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Überprüfen Sie, ob das Dienstanmeldekonto Lese-/Schreibberechtigung für den Ordner *%programdata%\Microsoft\DataTransfer\DataManagementGateway* hat.

    - Wenn das Dienstanmeldekonto nicht geändert wurde, sollte es standardmäßig Lese-/Schreibberechtigung haben.

        ![Dienstberechtigung](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Wenn Sie das Dienstanmeldekonto geändert haben, führen Sie die folgenden Schritte zur Behebung des Problems aus:
        1. Führen Sie eine saubere Deinstallation der aktuellen selbstgehosteten IR durch.
        1. Installieren Sie die selbstgehosteten IR-Bits.
        1. Führen Sie die Anweisungen unten zum Ändern des Dienstkontos aus: 
            1. Wechseln Sie zum Installationsordner für die selbstgehostete IR und darin zum Ordner *Microsoft Integration Runtime\4.0\Shared*.
            1. Starten Sie eine Befehlszeile mit erhöhten Rechten. Ersetzen Sie *\<user>* und *\<password>* durch Ihren eigenen Benutzernamen und Ihr Kennwort, und führen Sie dann den folgenden Befehl aus:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Wenn Sie zum Konto „LocalSystem“ wechseln möchten, müssen Sie für dieses Konto ein richtiges Format verwenden. Nachstehend finden Sie ein Beispiel für das richtige Format:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Verwenden Sie **nicht** das unten gezeigte Format:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. Da das lokale System höhere Berechtigungen als der Administrator hat, können Sie es alternativ auch direkt in „Dienste“ ändern.
            1. Sie können den lokalen Benutzer/Domänenbenutzer für das Anmeldekonto des IR-Diensts verwenden.            
        1. Registrieren Sie die Integration Runtime.

Wenn der Fehler so angezeigt wird: *Der Dienst „Integration Runtime Service“ (DIAHostService) konnte nicht gestartet werden. Überprüfen Sie, ob Sie ausreichende Berechtigungen zum Starten von Systemdiensten besitzen*. Führen Sie dazu die folgenden Anweisungen aus:

1. Überprüfen Sie im Windows-Dienstbereich das Dienstanmeldekonto *DIAHostService*.
   
    ![Dienstanmeldekonto](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Überprüfen Sie, ob das Dienstanmeldekonto die Berechtigung **Als Dienst anmelden** zum Starten des Windows-Diensts hat:

    ![Als Dienst anmelden](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Weitere Informationen

Wenn in Ihrem Fall keines der beiden vorstehenden Muster in der Auflösung zutrifft, versuchen Sie, die folgenden Windows-Ereignisprotokolle zu erfassen: 
- „Anwendungs- und Dienstprotokolle“> „Integration Runtime“
- „Windows-Protokolle“ > „Anwendung“

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Schaltfläche „Registrieren“ zum Registrieren einer selbstgehosteten IR wurde nicht gefunden    

#### <a name="symptoms"></a>Symptome

Bei der Registrierung einer selbstgehosteten IR wurde die Schaltfläche **Registrieren** in der Configuration Manager-Benutzeroberfläche nicht gefunden.

![Keine Schaltfläche „Registrieren“](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Ursache

Seit der Veröffentlichung der *Integration Runtime 3.0* wurde die Schaltfläche **Registrieren** auf einem vorhandenen Integration Runtime-Knoten entfernt, um eine übersichtlichere und sicherere Umgebung zu ermöglichen. Wenn ein Knoten für eine Integration Runtime registriert wurde (ganz gleich, ob online oder nicht), müssen Sie den vorherigen Knoten deinstallieren, anschließend installieren und registrieren, um ihn bei einer anderen Integration Runtime erneut zu registrieren.

#### <a name="resolution"></a>Lösung

1. Wechseln Sie zur Systemsteuerung, um die vorhandene Integration Runtime zu deinstallieren.

    > [!IMPORTANT] 
    > Wählen Sie im Prozess unten „Ja“ aus. Behalten Sie während des Deinstallationsvorgangs keine Daten bei.

    ![Löschen von Daten](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Wenn Sie keine Installer-MSI für die Integration Runtime haben, wechseln Sie zum [Download Center](https://www.microsoft.com/en-sg/download/details.aspx?id=39717), um die neueste Integration Runtime herunterzuladen.
1. Installieren Sie die MSI, und registrieren Sie die Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Die selbstgehostete IR kann aufgrund von „localhost“ nicht registriert werden.    

#### <a name="symptoms"></a>Symptome

Die selbstgehostete IR kann bei „get_LoopbackIpOrName“ auf einem neuen Computer nicht registriert werden.

**Debug:** Ein Laufzeitfehler ist aufgetreten.
Der Typinitialisierer für „Microsoft.DataTransfer.DIAgentHost.DataSourceCache“ hat eine Ausnahme ausgelöst.
Beim Datenbankaufruf ist ein nicht behebbarer Fehler aufgetreten.
 
**Ausnahmedetail:** System.TypeInitializationException: Der Typinitialisierer für „Microsoft.DataTransfer.DIAgentHost.DataSourceCache“ hat eine Ausnahme ausgelöst. ---> System.Net.Sockets.SocketException: Beim Datenbankaufruf unter „System.Net.Dns.GetAddrInfo(String Name)“ ist ein nicht behebbarer Fehler aufgetreten.

#### <a name="cause"></a>Ursache

Das Problem tritt normalerweise beim Auflösen von „localhost“ auf.

#### <a name="resolution"></a>Lösung

Verwenden Sie „Localhost 127.0.0.1“ zum Hosten der Datei und zur Lösung dieses Problems.


### <a name="self-hosted-setup-failed"></a>Fehler beim selbstgehosteten Setup    

#### <a name="symptoms"></a>Symptome

Eine vorhandene IR kann nicht deinstalliert, eine neue IR nicht installiert oder eine vorhandene IR nicht auf eine neue IR aktualisiert werden.

#### <a name="cause"></a>Ursache

Die Installation ist abhängig vom Windows Installer-Dienst. Es gibt verschiedene Gründe, die ein Installationsproblem verursachen können:
- Nicht genügend Speicherplatz auf dem Datenträger
- Fehlende Berechtigungen
- NT-Dienst aus irgendeinem Grund gesperrt
- CPU-Auslastung ist zu hoch
- MSI-Datei wird in einem langsamen Netzwerkspeicherort gehostet
- Auf einige Systemdateien oder Registrierungen wurde versehentlich zugegriffen


### <a name="ir-service-account-failed-to-fetch-certificate-access"></a>Fehler des IR-Dienstkontos beim Zertifikatszugriff

#### <a name="symptoms"></a>Symptome

Beim Installieren der selbstgehosteten IR über den Konfigurations-Manager für Microsoft Integration Runtime wird ein Zertifikat mit einer vertrauenswürdigen Zertifizierungsstelle generiert. Das Zertifikat konnte nicht angewendet werden, um die Kommunikation zwischen zwei Knoten zu verschlüsseln. 

Die Fehlerinformationen werden wie folgt angezeigt: 

`Failed to change Intranet communication encryption mode: Failed to grant Integration Runtime service account the access of to the certificate 'XXXXXXXXXX'. Error code 103`

![Fehler beim Gewähren des Zertifikatszugriffs für das IR-Dienstkonto](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Ursache

Das Zertifikat verwendet KSP (Schlüsselspeicheranbieter), was noch nicht unterstützt wird. Die selbstgehostete Integration Runtime unterstützt bisher nur das CSP-Zertifikat (Kryptografiedienstanbieter).

#### <a name="resolution"></a>Lösung

In diesem Fall wird das CSP-Zertifikat empfohlen.

**Lösung 1:** Verwenden Sie den folgenden Befehl, um das Zertifikat zu importieren:

```
Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx 
```

![Verwenden von „certutil“](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Lösung 2:** Konvertierung von Zertifikaten:

openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: *\<EnterPassword>*

openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx

Vor und nach der Konvertierung:

![Vor der Zertifikatänderung](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Nach der Zertifikatänderung](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Selbstgehostete Integration Runtime, Version 5.x
Für das Upgrade auf Version 5.x der selbstgehosteten Integration Runtime von Azure Data Factory wird mindestens **.NET Framework Runtime 4.7.2** benötigt. Auf der Downloadseite befinden sich Downloadlinks für die neueste 4.x-Version und die beiden neuesten 5.x-Versionen. 


Für ADF V2-Kunden:
- Wenn die automatische Aktualisierung aktiviert ist und Sie bereits ein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben, erfolgt automatisch ein Upgrade der selbstgehosteten Integration Runtime auf die neueste 5.x-Version.
- Wenn die automatische Aktualisierung aktiviert ist und Sie kein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben, erfolgt kein automatisches Upgrade der selbstgehosteten Integration Runtime auf die neueste 5.x-Version. Die selbstgehostete Integration Runtime verbleibt in der aktuellen 4.x-Version. Es wird eine Warnung zum Upgrade von .NET Framework Runtime im Portal und im selbstgehosteten Integration Runtime-Client angezeigt.
- Wenn die automatische Aktualisierung deaktiviert ist und Sie bereits ein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben, können Sie die neueste 5.x-Version manuell herunterladen und auf Ihrem Computer installieren.
- Wenn die automatische Aktualisierung deaktiviert ist und Sie kein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben. Wenn Sie versuchen, Version 5.x der selbstgehosteten Integration Runtime manuell zu installieren und den Schlüssel zu registrieren, müssen Sie zuerst ein Upgrade für .NET Framework Runtime durchführen.


Für ADF V1-Kunden:
- Version 5.x der selbstgehosteten Integration Runtime bietet keine Unterstützung für ADF V1.
- Es wird ein automatisches Upgrade der selbstgehosteten Integration Runtime auf die neueste 4.x-Version durchgeführt. Die letzte 4.x-Version läuft nicht ab. 
- Wenn Sie versuchen, Version 5.x der selbstgehosteten Integration Runtime manuell zu installieren und den Schlüssel zu registrieren, werden Sie darüber informiert, dass Version 5.x der selbstgehosteten Integration Runtime V1 nicht unterstützt.


## <a name="self-hosted-ir-connectivity-issues"></a>Konnektivitätsprobleme bei der selbstgehosteten Integration Runtime

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Selbstgehostete Integration Runtime kann keine Verbindung mit dem Clouddienst herstellen

#### <a name="symptoms"></a>Symptome

![Verbindungsproblem bei der selbstgehosteten Integration Runtime (IR)](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Ursache 

Die selbstgehostete Integration Runtime kann keine Verbindung zum Azure Data Factory-Dienst herstellen (Back-End). Dieses Problem wird in der Regel durch Netzwerkeinstellungen in der Firewall verursacht.

#### <a name="resolution"></a>Lösung

1. Überprüfen Sie, ob der Integration Runtime-Dienst ausgeführt wird.
    
   ![Ausführungsstatus des selbstgehosteten Integration Runtime-Diensts](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Wenn der Dienst ausgeführt wird, gehen Sie zu Schritt 3.

1. Wenn kein Proxy für die selbstgehostete Integration Runtime konfiguriert ist (Standardeinstellung), führen Sie den folgenden PowerShell-Befehl auf dem Computer aus, auf dem die selbstgehostete Integration Runtime installiert wurde:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Die Dienst-URL kann abhängig vom jeweiligen Data Factory-Speicherort abweichen. Sie finden die Dienst-URL unter **ADF-Benutzeroberfläche** > **Verbindungen** > **Integration Runtimes** > **Selfgehostete IR bearbeiten** > **Knoten** > **Dienst-URLs anzeigen**.
            
    Die folgende Antwort wird erwartet:
            
    ![Antwort des PowerShell-Befehls](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Wenn Sie nicht die erwartete Antwort erhalten, verwenden Sie abhängig von der jeweiligen Situation eine der folgenden Methoden:
            
    * Wenn Sie die Meldung erhalten, dass der Remotename nicht aufgelöst werden konnte, gibt es ein Problem mit dem Domain Name System (DNS). Wenden Sie sich an das Netzwerkteam, um dieses Problem zu beheben.
    * Wenn Sie die Meldung erhalten, dass das SSL/TLS-Zertifikat ist nicht vertrauenswürdig ist, überprüfen Sie, ob das Zertifikat für https://wu2.frontend.clouddatahub.net/ auf dem Computer vertrauenswürdig ist, und installieren Sie dann das öffentliche Zertifikat mit dem Zertifikat-Manager. Durch diese Aktion sollte das Problem behoben werden.
    * Wechseln Sie zu **Windows** > **Ereignisanzeige (Protokolle)**  > **Anwendungs- und Dienstprotokolle** > **Integration Runtime**, und prüfen Sie, ob Fehler auftreten, die durch DNS, eine Firewallregel oder Unternehmensnetzwerkeinstellungen verursacht werden. (Wenn Sie einen solchen Fehler feststellen, müssen Sie die Verbindung zwangsbeenden.) Da jedes Unternehmen über angepasste Netzwerkeinstellungen verfügt, wenden Sie sich an das Netzwerkteam, um diese Probleme zu beheben.

1. Wenn „Proxy“ in der selbstgehosteten Integration Runtime konfiguriert wurde, überprüfen Sie, ob Ihr Proxyserver auf den Dienstendpunkt zugreifen kann. Ein Beispiel für einen Befehl finden Sie unter [PowerShell, Webanforderungen und Proxys](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Die folgende Antwort wird erwartet:
            
![Antwort 2 des PowerShell-Befehls](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy-Aspekte:
> *    Überprüfen Sie, ob der Proxyserver in die Liste der sicheren Empfänger eingefügt werden muss. Stellen Sie in diesem Fall sicher, dass [diese Domänen](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) in der Liste der sicheren Empfänger enthalten sind.
> *    Überprüfen Sie, ob das TLS/SSL-Zertifikat „wu2.frontend.clouddatahub.net/“ auf dem Proxyserver vertrauenswürdig ist.
> *    Wenn Sie im Proxy die Active Directory-Authentifizierung verwenden, ändern Sie das Dienstkonto in das Benutzerkonto, das als „Integration Runtime-Dienst“ auf den Proxy zugreifen kann.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Fehlermeldung: „Self-hosted integration runtime node/ logical SHIR is in Inactive/ „Running (Limited)“ state“ (Knoten der selbstgehosteten Integration Runtime / logische SHIR befindet sich im Status „Inaktiv/Wird ausgeführt (eingeschränkt)“)

#### <a name="cause"></a>Ursache 

Der selbstgehostete Integrated Runtime-Knoten weist wie im folgenden Screenshot möglicherweise den Status **Inaktiv** auf:

![Inaktiver Knoten der selbstgehosteten IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Dieses Verhalten tritt auf, wenn Knoten nicht miteinander kommunizieren können.

#### <a name="resolution"></a>Lösung

1. Melden Sie sich bei dem virtuellen Computer an, der auf dem Knoten gehostet wird. Öffnen Sie unter **Anwendungs-und Dienstprotokolle** > **Integration Runtime** die Ereignisanzeige und filtern Sie alle Fehlerprotokolle.

1. Überprüfen Sie, ob das Fehlerprotokoll den folgenden Fehler enthält: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Wenn dieser Fehler angezeigt wird, führen Sie den folgenden Befehl in einer Befehlszeile aus: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Wenn der folgende Fehler angezeigt wird, wenden Sie sich an Ihre IT-Abteilung, um Hilfe bei der Lösung dieses Problems zu erhalten. Nachdem Sie Telnet erfolgreich eingerichtet haben und verwenden können, wenden Sie sich an den Microsoft-Support, wenn Sie weiterhin Probleme mit dem Status des Integrated Runtime-Knotens haben.
        
   ![Befehlszeilenfehler](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Überprüfen Sie, ob das Fehlerprotokoll Folgendes enthält:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Probieren Sie zur Lösung des Problems eine folgenden Methoden (oder beide) aus:
    - Platzieren Sie alle Knoten in derselben Domäne.
    - Fügen Sie die IP-Adresse zur Hostzuordnung in allen Hostdateien des gehosteten virtuellen Computers hinzu.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Konnektivitätsprobleme zwischen selbstgehosteter IR und Data Factory oder zwischen selbstgehosteter IR und Datenquelle/-senke

Um ein Problem mit der Netzwerkkonnektivität zu beheben, müssen Sie wissen, wie Sie die Netzwerkablaufverfolgung erfassen und verwenden und die [Netmon-Ablaufverfolgung analysieren](#how-to-analyze-netmon-trace), bevor Sie die Netmon-Tools in realen Fällen mit der selbstgehosteten IR anwenden.

#### <a name="symptoms"></a>Symptome

Bei der Behandlung von Konnektivitätsproblemen, wie z. B. zwischen der selbstgehosteten IR und Data Factory: 

![Fehler bei HTTP-Anforderung](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Oder zwischen der selbstgehosteten IR und der Datenquelle/-senke treten die folgenden Fehler auf:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Lösung:

Wenn die oben genannten Probleme auftreten, finden Sie im Folgenden Anweisungen zur Problembehandlung:

Führen Sie eine weitere Analyse der Netmon-Ablaufverfolgung durch.
- Legen Sie den Filter zunächst so fest, dass alle Zurücksetzungen vom Server auf die Clientseite angezeigt werden. Im folgenden Beispiel sehen Sie, dass die Serverseite der Data Factory-Server ist.

    ![Data Factory-Server](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Wenn Sie das Zurücksetzungspaket erhalten, finden Sie die Konversation unter TCP.

    ![Suchen der Konversation](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Anschließend können Sie die Konvertierung zwischen Client und Data Factory-Server abrufen, indem Sie den Filter entfernen.

    ![Abrufen der Konversation](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Basierend auf der erfassten Netmon-Ablaufverfolgung ist zu erkennen, dass die Gültigkeitsdauer (TimeToLive, TTL) 64 beträgt. Gemäß den **Standardwerten für die Gültigkeitsdauer und maximale Anzahl an Weiterleitungen**, die in [diesem Artikel](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) erwähnt werden (siehe unten), sehen wir, dass das Linux-System dafür verantwortlich ist, dass das Paket zurückgesetzt und die Verbindung getrennt wird.

    Die Standardwerte für die Gültigkeitsdauer und maximale Anzahl an Weiterleitungen variieren je nach Betriebssystem. Im Folgenden finden Sie einige Standardwerte:
    - Linux-Kernel 2.4 (etwa 2001): 255 für TCP, UDP und ICMP
    - Linux-Kernel 4.10 (2015): 64 für TCP, UDP und ICMP
    - Windows XP (2001): 128 für TCP, UDP und ICMP
    - Windows 10 (2015): 128 für TCP, UDP und ICMP
    - Windows Server 2008: 128 für TCP, UDP und ICMP
    - Windows Server 2019 (2018): 128 für TCP, UDP und ICMP
    - macOS (2001): 64 für TCP, UDP und ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Im obigen Beispiel ist 61 anstelle von 64 angezeigt, denn wenn das Netzwerkpaket sein Ziel erreicht, muss es verschiedene Hops wie Router/Netzwerkgeräte durchlaufen. Die Anzahl der Router/Netzwerkgeräte wird von der endgültigen Gültigkeitsdauer abgezogen.
    In diesem Fall sehen Sie, dass die Zurücksetzung vom Linux-System mit TTL 64 gesendet werden kann.

- Wir müssen den vierten Hop von der selbstgehosteten IR überprüfen, um zu bestätigen, woher das zurücksetzende Gerät stammt.
 
    *Netzwerkpaket von Linux-System A mit TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 selbstgehostete IR*

- Idealerweise ist die Gültigkeitsdauer 128, was bedeutet, dass die Data Factory vom Windows-System ausgeführt wird. *128 - 107 = 21 Hops*, wie im obigen Beispiel gezeigt, bedeutet, dass 21 Hops für das Paket während des TCP 3-Handshakes von Data Factory an die selbstgehostete IR gesendet wurden.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Wenden Sie sich daher an das Netzwerkteam, um zu überprüfen, welcher der vierte Hop von der selbstgehosteten IR ist. Wenn es sich um die Firewall als Linux-System handelt, überprüfen Sie die Protokolle dazu, warum dieses Gerät das Paket nach dem TCP 3-Handshake zurücksetzt. Wenn Sie jedoch nicht sicher sind, wo Sie die Untersuchung durchführen sollen, versuchen Sie, die Netmon-Ablaufverfolgung von der selbstgehosteten IR und der Firewall während der problematischen Zeit zusammenzustellen, um herauszufinden, welches Gerät dieses Paket zurücksetzt und die Verbindung trennt. In diesem Fall müssen Sie sich auch an Ihr Netzwerkteam wenden, um fortzufahren.

### <a name="how-to-analyze-netmon-trace"></a>Analysieren der Netmon-Ablaufverfolgung

> [!NOTE] 
> Die folgende Anweisung gilt für die Netmon-Ablaufverfolgung. Da die Netmon-Ablaufverfolgung derzeit nicht unterstützt wird, können Sie WireShark auf gleiche Weise nutzen.

Wenn Sie versuchen, Telnet **8.8.8.8 888** mit der erfassten Netmon-Ablaufverfolgung auszuführen, sollte Ihnen folgende Ablaufverfolgung angezeigt werden:

![Netmon-Ablaufverfolgung 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon-Ablaufverfolgung 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Dies bedeutet, dass Sie keine TCP-Verbindung mit der Serverseite **8.8.8.8** basierend auf Port **888** herstellen können. Daher sehen Sie zwei zusätzliche **SynReTransmit**-Pakete. Da die Quelle **SELF-HOST2** keine Verbindung mit **8.8.8.8** beim ersten Paket herstellen konnte, wird die Verbindung weiterhin hergestellt.

> [!TIP]
> - Klicken Sie auf **Filter laden** -> **Standard Filter (Standardfilter)**  -> **Adressen** -> **IPv4-Adressen**.
> - Geben Sie **IPv4.Address == 8.8.8.8** als Filter ein, und klicken Sie auf **Anwenden**. Anschließend wird Ihnen nur die Kommunikation vom lokalen Computer zum Ziel **8.8.8.8** angezeigt.

![Filteradressen 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Filteradressen 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Das folgende Beispiel zeigt, wie ein gutes Szenario aussehen würde. 

- Wenn Telnet **8.8.8.8 53** problemlos funktioniert, sehen Sie, dass ein TCP 3-Handshake stattfindet, und die Sitzung wird mit einem TCP 4-Handshake beendet.

    ![Gutes Szenario Beispiel 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Gutes Szenario Beispiel 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Basierend auf dem obigen TCP 3-Handshake sehen Sie den folgenden Workflow:

    ![TCP 3-Handshake-Workflow](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Der TCP 4-Handshake zum Beenden der Sitzung und der zugehörige Workflow werden wie folgt angezeigt:

    ![TCP 4-Handshake](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4-Handshake-Workflow](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>Empfang einer E-Mail zum Aktualisieren der Netzwerkkonfiguration, um die Kommunikation mit neuen IP-Adressen zuzulassen

#### <a name="email-notification-from-microsoft"></a>E-Mail-Benachrichtigung von Microsoft

Möglicherweise erhalten Sie die folgende E-Mail-Benachrichtigung, in der eine Aktualisierung der Netzwerkkonfiguration empfohlen wird, um die Kommunikation mit neuen IP-Adressen für Azure Data Factory zum 8. November 2020 zuzulassen:

   ![E-Mail-Benachrichtigung](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Ermitteln der Relevanz dieser Benachrichtigung für Ihre Situation

Diese Benachrichtigung betrifft die folgenden Szenarios:
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>Szenario 1: Von einer selbstgehosteten Integration Runtime ausgehende Kommunikation, die lokal hinter der Unternehmensfirewall ausgeführt wird
So ermitteln Sie, ob Sie Maßnahmen ergreifen müssen:
- Diese Benachrichtigung betrifft Sie nicht, wenn Sie Firewallregeln mithilfe des unter [Firewallkonfiguration und Einrichten einer Positivliste für IP-Adressen](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) beschriebenen Ansatzes auf Basis der vollqualifizierten Domänennamen definieren.
- Sie sind jedoch betroffen, wenn Sie die Positivliste für ausgehende IP-Adressen in der Unternehmensfirewall explizit aktiviert haben.

Erforderliche Maßnahme, wenn Sie betroffen sind: Benachrichtigen Sie das für die Netzwerkinfrastruktur zuständige Team, um Ihre Netzwerkkonfiguration zu aktualisieren, damit bis zum 8. November 2020 die neuesten Data Factory-IP-Adressen verwendet werden.  Navigieren Sie zum Herunterladen der aktuellen IP-Adressen zu [Downloadlink für Diensttags für IP-Adressbereiche](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>Szenario 2: Von einer selbstgehosteten Integration Runtime ausgehende Kommunikation, die auf einer Azure-VM innerhalb eines vom Kunden verwalteten virtuellen Azure-Netzwerks ausgeführt wird
So ermitteln Sie, ob Sie Maßnahmen ergreifen müssen:
- Überprüfen Sie, ob in Ihrem privaten Netzwerk, das die selbstgehostete Integration Runtime enthält, Regeln von Netzwerksicherheitsgruppen (NSG) für ausgehende Kommunikation gelten. Wenn keine Beschränkungen für ausgehende Kommunikation vorliegen, sind Sie nicht betroffen.
- Wenn Einschränkungen für Ausgangsregeln bestehen, prüfen Sie, ob Sie das Diensttag verwenden. Wenn Sie das Diensttag verwenden, muss nichts geändert oder hinzugefügt werden, da die neuen IP-Adressbereiche dem vorhandenen Diensttag unterliegen. 
 ![Zielüberprüfung](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- Sie müssen jedoch Maßnahmen ergreifen, wenn Sie die Positivliste für ausgehende IP-Adressen in den Einstellungen der NSG-Regeln im virtuellen Azure-Netzwerk explizit aktiviert haben.

Erforderliche Maßnahme, wenn Sie betroffen sind: Benachrichtigen Sie das für die Netzwerkinfrastruktur zuständige Team, um die NSG-Regeln in der Konfiguration Ihres virtuellen Azure-Netzwerks zu aktualisieren, damit bis zum 8. November 2020 die neuesten Data Factory-IP-Adressen verwendet werden.  Navigieren Sie zum Herunterladen der aktuellen IP-Adressen zu [Downloadlink für Diensttags für IP-Adressbereiche](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>Szenario 3: Von einer SSIS-Integration Runtime ausgehende Kommunikation, die in einem vom Kunden verwalteten virtuellen Azure-Netzwerk ausgeführt wird
- Überprüfen Sie, ob in Ihrem privaten Netzwerk, das die SSIS-Integration Runtime enthält, NSG-Regeln für ausgehende Kommunikation gelten. Wenn keine Beschränkungen für ausgehende Kommunikation vorliegen, sind Sie nicht betroffen.
- Wenn Einschränkungen für Ausgangsregeln bestehen, prüfen Sie, ob Sie das Diensttag verwenden. Wenn Sie das Diensttag verwenden, muss nichts geändert oder hinzugefügt werden, da die neuen IP-Adressbereiche dem vorhandenen Diensttag unterliegen.
- Sie müssen jedoch Maßnahmen ergreifen, wenn Sie die Positivliste für ausgehende IP-Adressen in den Einstellungen der NSG-Regeln im virtuellen Azure-Netzwerk explizit aktiviert haben.

Erforderliche Maßnahme, wenn Sie betroffen sind: Benachrichtigen Sie das für die Netzwerkinfrastruktur zuständige Team, um die NSG-Regeln in der Konfiguration Ihres virtuellen Azure-Netzwerks zu aktualisieren, damit bis zum 8. November 2020 die neuesten Data Factory-IP-Adressen verwendet werden.  Navigieren Sie zum Herunterladen der aktuellen IP-Adressen zu [Downloadlink für Diensttags für IP-Adressbereiche](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Für den geschützten SSL/TLS-Kanal konnte keine Vertrauensstellung hergestellt werden 

#### <a name="symptoms"></a>Symptome

Die selbstgehostete Integration Runtime konnte keine Verbindung zum ADF-Dienst herstellen.

Wenn Sie das Ereignisprotokoll der selbstgehosteten Integration Runtime oder die Clientbenachrichtigungsprotokolle in der Tabelle „CustomLogEvent“ überprüfen, finden Sie die folgende Fehlermeldung:

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

So überprüfen Sie das Serverzertifikat des ADF-Diensts:

Die einfachste Möglichkeit besteht darin, die URL des ADF-Diensts im Browser zu öffnen (z. B. https://eu.frontend.clouddatahub.net/ auf dem Computer, auf dem die selbstgehostete Integration Runtime installiert ist) und die Informationen zu Serverzertifikat anzuzeigen:

  ![Überprüfen des Serverzertifikats des ADF-Diensts](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Überprüfen des Pfads des Serverzertifikats](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Ursache

Es gibt zwei mögliche Gründe für dieses Problem:

- Auf dem Computer, auf dem die selbstgehostete Integration Runtime installiert ist, gilt die Stammzertifizierungsstelle des Serverzertifikats des ADF-Diensts nicht als vertrauenswürdig. 
- Sie verwenden einen Proxy in Ihrer Umgebung, und das Serverzertifikat des ADF-Diensts wird durch den Proxy ersetzt, während das ersetzte Serverzertifikat auf dem Computer, auf dem die selbstgehostete Integration Runtime installiert ist, als nicht vertrauenswürdig eingestuft wird.

#### <a name="resolution"></a>Lösung

- Stellen Sie für Grund 1 sicher, dass das ADF-Serverzertifikat und die dazugehörige Zertifikatkette auf dem Computer, auf dem die selbstgehostete Integration Runtime installiert ist, als vertrauenswürdig gelten.
- Stellen Sie für Grund 2 entweder auf dem Computer mit der selbstgehosteten Integration Runtime eine Vertrauensstellung zur ersetzten Stammzertifizierungsstelle her, oder konfigurieren Sie den Proxy so, dass das ADF-Serverzertifikat nicht ersetzt wird.

Ausführliche Informationen zum Herstellen einer Vertrauensbeziehung zu einem Zertifikat unter Windows finden Sie [in diesem Artikel](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-info"></a>Zusätzliche Informationen
Wir führen ein Rollout eines neuem SSL-Zertifikats aus, das von DigiCert signiert ist. Überprüfen Sie, ob „DigiCert Global Root G2“ in der vertrauenswürdigen Stammzertifizierungsstelle enthalten ist.

  ![DigiCert Global Root G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Falls dem nicht so ist, können Sie es [hier](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ) herunterladen. 


## <a name="self-hosted-ir-sharing"></a>Freigabe der selbstgehosteten Integration Runtime

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Eine Freigabe der selbstgehosteten Integration Runtime von einem anderen Mandanten aus wird nicht unterstützt. 

#### <a name="symptoms"></a>Symptome

Beim Versuch, die selbstgehostete Integration Runtime über die Azure Data Factory-Benutzeroberfläche freizugeben, diese Freigabe aber in Data Factorys auf verschiedenen Mandanten nicht möglich ist, werden Ihnen eventuell andere Data Factorys (auf verschiedenen Mandanten) angezeigt.

#### <a name="cause"></a>Ursache

Die selbstgehostete Integration Runtime kann nicht mandantenübergreifend freigegeben werden.



## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in den folgenden Ressourcen:

*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Anleitung zur Leistung der Mapping Data Flow-Funktion](concepts-data-flow-performance.md)