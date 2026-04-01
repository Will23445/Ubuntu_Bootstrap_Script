# Ubuntu Server Bootstrap & Hardening Script

Questo repository contiene il mio primo progetto pratico da aspirante sistemista. Ho sviluppato questo script Bash per automatizzare la configurazione iniziale e l'hardening di una macchina Ubuntu Server, riducendo gli errori umani legati al setup manuale.

## Obiettivo del progetto

Lo script è progettato per essere eseguito al primo avvio di un server "nudo" (ad esempio una VPS o una VM locale). Il suo scopo è portare il sistema a un livello di sicurezza base accettabile prima di esporlo in rete, seguendo il principio del minimo privilegio.

Le operazioni eseguite in sequenza sono:

* **Aggiornamento di sistema:** Esecuzione di `apt update` e `upgrade` per applicare subito le ultime patch di sicurezza.
* **Gestione utenze:** Creazione di un utente amministrativo non-root dedicato (`admin_luca`) e assegnazione al gruppo `sudo`.
* **Autenticazione SSH:** Creazione della directory `.ssh` con permessi rigidi per compiacere il demone SSH (700 per la directory, 600 per `authorized_keys`) e inserimento della chiave pubblica Ed25519 per l'accesso remoto.
* **Hardening SSH:** Modifica del file `sshd_config` tramite stream editor (`sed`). Ho utilizzato un'espressione regolare (`^#*PasswordAuthentication.*`) per assicurarmi di sovrascrivere la direttiva anche qualora fosse commentata o formattata diversamente di default. La direttiva viene forzata su `no` e il servizio SSH viene riavviato tramite `systemctl` per applicare immediatamente le restrizioni contro gli attacchi brute-force.
* **Configurazione Firewall (UFW):** Impostazione di una policy "default deny" in entrata, aprendo esclusivamente le porte necessarie (22 per SSH, 80 e 443 per il traffico web).
* **Utility di base:** Installazione di strumenti essenziali per l'amministrazione (`htop`, `curl`, `git`, `vim`).

## Utilizzo

1. Generare una coppia di chiavi SSH sul client locale.
2. Scaricare lo script `.sh` sul server di destinazione.
3. Aprire lo script e sostituire l'attuale stringa della chiave pubblica nella direttiva `echo` con la propria.
4. Rendere lo script eseguibile e lanciarlo con privilegi di root:

```bash
chmod +x setup_server.sh
sudo ./setup_server.sh
