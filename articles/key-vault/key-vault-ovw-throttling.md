---
title: Anleitung zur Drosselung von Azure Key Vault
description: Die Key Vault-Drosselung schränkt die Anzahl gleichzeitiger Aufrufe ein, um eine übermäßige Nutzung von Ressourcen zu verhindern.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: 06b96ac6ea0202b74638b9331d754c0e0cc28ad0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695243"
---
# <a name="azure-key-vault-throttling-guidance"></a>Anleitung zur Drosselung von Azure Key Vault

Die Drosselung ist ein Vorgang, den Sie einleiten, um die Anzahl gleichzeitiger Aufrufe für den Azure-Dienst einzuschränken und so eine übermäßige Nutzung von Ressourcen zu verhindern. Azure Key Vault (AKV) dient der Verarbeitung einer große Anzahl von Anforderungen. Sollten jedoch zu viele Anforderungen vorliegen, wird mittels Drosselung die optimale Leistung und Zuverlässigkeit des AKV-Diensts bewahrt.

Die Grenzwerte für die Drosselung sind je nach Szenario unterschiedlich. So ist eine Drosselung beispielsweise wahrscheinlicher, wenn eine Vielzahl von Schreibvorgängen durchgeführt werden, als wenn nur Lesevorgänge durchgeführt werden.

## <a name="how-does-key-vault-handle-its-limits"></a>Wie geht Key Vault mit den Grenzen um?

Bei Key Vault wird mithilfe von Diensteinschränkungen der Missbrauch von Ressourcen verhindert und die Qualität des Dienstes für alle Clients von Key Vault sichergestellt. Wenn der Schwellenwert für einen Dienst überschritten wird, begrenzt Key Vault weitere Anforderungen von diesem Client für eine bestimmte Zeitdauer. In diesem Fall gibt Key Vault den HTTP-Statuscode 429 (zu viele Anforderungen) zurück, und die Anforderungen schlagen fehl. Auch fehlgeschlagene Anforderungen, die 429 zurückgeben, zählen zu dem von Key Vault überwachten Drosselungsgrenzwert. 

Wenn bei Ihnen ein gültiger Geschäftsvorgang für höhere Drosselungsgrenzwerte vorliegt, wenden Sie sich bitte an uns.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Drosseln einer App als Reaktion auf Diensteinschränkungen

Nachfolgend sind die **bewährten Methoden** aufgeführt, die Sie implementieren sollten, wenn Ihr Dienst gedrosselt ist:
- Reduzieren Sie die Anzahl von Vorgängen pro Anforderung.
- Reduzieren Sie die Häufigkeit der Anforderungen.
- Vermeiden Sie schnelle Wiederholungsversuche. 
    - Alle Anforderungen zählen zu Ihren Nutzungseinschränkungen.

Verwenden Sie zur Implementierung der Fehlerbehandlung bei Ihrer App den HTTP-Fehlercode 429, um zu ermitteln, ob eine clientseitige Drosselung erforderlich ist. Wenn die Anforderung mit dem HTTP-Fehlercode 429 erneut fehlschlägt, liegt weiterhin eine Azure-Diensteinschränkung vor. Verwenden Sie weiterhin die empfohlene clientseitige Drosselungsmethode, und wiederholen Sie die Anforderung, bis sie erfolgreich abgeschlossen wird.

Code zur Implementierung eines exponentiellen Backoffs wird unten dargestellt. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


Die Verwendung dieses Codes in einer C\#-Clientanwendung ist einfach. Das folgende Beispiel zeigt dies mit der HttpClient-Klasse.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Denken Sie daran, dass dieser Code nur als Proof of Concept geeignet ist. 

### <a name="recommended-client-side-throttling-method"></a>Empfohlene Methode für die clientseitige Drosselungsmethode

Wenn der HTTP-Fehlercode 429 vorliegt, beginnen Sie die Drosselung Ihres Clients mithilfe eines exponentiellen Backoff-Konzepts:

1. 1 Sekunde warten, Anforderung wiederholen
2. Liegt weiterhin eine Drosselung vor: 2 Sekunden warten, Anforderung wiederholen
3. Liegt weiterhin eine Drosselung vor: 4 Sekunden warten, Anforderung wiederholen
4. Liegt weiterhin eine Drosselung vor: 8 Sekunden warten, Anforderung wiederholen
5. Liegt weiterhin eine Drosselung vor: 16 Sekunden warten, Anforderung wiederholen

Nun sollte der HTTP-Antwortcode 429 nicht mehr angezeigt werden.

## <a name="see-also"></a>Weitere Informationen

Ausführlichere Informationen zur Drosselung in der Microsoft Cloud finden Sie unter [Throttling Pattern (Drosselungsmuster)](https://docs.microsoft.com/azure/architecture/patterns/throttling).

