# Jarvisa iCloud Mail Cloud

Server MCP personale per leggere la posta iCloud da ChatGPT, ospitato su Cloudflare Workers. Il Mac può restare spento. Ogni persona deve pubblicare la propria istanza e usare le proprie credenziali.

## Funzioni

Cartelle, ricerca e lettura messaggi tramite IMAP TLS; preparazione del testo di una bozza (non salvata in iCloud). OAuth con PKCE e password separata del proprietario. L’invio è disattivato nella configurazione distribuita. Il codice contiene un percorso SMTP opzionale, non verificato per invio reale in questa migrazione.

## Installazione

Servono Node.js 22 o successivo, pnpm, un account Cloudflare Free e una password specifica per app Apple per iCloud Mail. Usa il browser normale per completare OAuth se quello integrato non conserva correttamente il consenso.

1. Esegui `pnpm install --frozen-lockfile`, poi `pnpm exec wrangler login`.
2. Crea KV: `pnpm exec wrangler kv namespace create OAUTH_KV`.
3. Copia `wrangler.jsonc` in `wrangler.local.jsonc` e aggiungi `kv_namespaces` con binding `OAUTH_KV` e l’ID restituito. Il file locale è escluso da Git.
4. Inserisci i segreti uno alla volta con `pnpm exec wrangler secret put NOME --config wrangler.local.jsonc`: `ICLOUD_EMAIL`, `ICLOUD_USERNAME`, `ICLOUD_PASSWORD`, `OWNER_PASSWORD`. Usa i dati IMAP del tuo account Apple. La password Jarvisa deve essere diversa da quella Apple, casuale e lunga almeno 24 caratteri. Non inserire segreti nei file del repository.
5. Pubblica: `pnpm exec wrangler deploy --config wrangler.local.jsonc`. Mantieni `SEND_ENABLED` uguale a `false`.
6. Aggiungi una connessione MCP personalizzata in ChatGPT con URL `https://IL-TUO-WORKER.workers.dev/mcp`, autenticazione OAuth. La disponibilità delle connessioni personalizzate dipende dal tuo account ChatGPT. Autorizza con la password Jarvisa.
7. Cerca e leggi una mail tramite la nuova connessione. Ripeti dall’iPhone con il Mac spento prima di rimuovere eventuali tunnel precedenti.

## Piano gratuito e limiti

Il progetto è progettato per Workers Free e Workers KV Free, senza dominio a pagamento. Verifica piano e quote correnti nel tuo account: non attivare Workers Paid. Quote esaurite possono interrompere il servizio. Il costo o l’accesso al tuo piano ChatGPT è separato. Lettura massima 5 MB per messaggio e 50.000 caratteri di testo; allegati elencati, non scaricati.

## Verifica

`pnpm test` esegue i controlli di validazione. `node test/runtime.mjs` verifica OAuth/MCP con credenziali sintetiche e chiude il runtime temporaneo. `pnpm check` compila senza pubblicare. Il test runtime usa la porta locale 8788.

Una prova personale ha confermato la lettura da ChatGPT su iPhone con il Mac spento. Non garantisce compatibilità con ogni account o disponibilità futura dei servizi.

## Privacy

Credenziali conservate in Cloudflare Worker Secrets; autorizzazioni OAuth in KV. La posta passa attraverso il Worker e ChatGPT quando richiesta. IMAP apre la cartella in sola lettura e usa BODY.PEEK. Non pubblicare configurazioni personali, screenshot, token, log o file di stato del deploy. Non è un servizio ufficiale Apple, OpenAI o Cloudflare.
