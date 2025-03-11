# PterodactylPowerAction

Ein Velocity-Plugin zum Starten und Stoppen von Servern über die [Pterodactyl](https://pterodactyl.io/) Client-API.

## Funktionsweise  

Dieses Plugin stoppt einen Server nach einer bestimmten Verzögerung (standardmäßig 1 Stunde), wenn er leer ist, nachdem ein Spieler den Server verlassen oder gewechselt hat.  

Versucht ein Spieler, sich mit einem gestoppten Server zu verbinden, wird er zu einem Warte-Server wie [Limbo](https://www.spigotmc.org/resources/82468/) weitergeleitet und automatisch verbunden, sobald der gewünschte Server gestartet ist.  

Falls sich der Spieler bereits im Netzwerk befindet, erhält er eine Nachricht mit der Information, dass er automatisch weitergeleitet wird, sobald der Server bereit ist.  

Sollte der Server nicht erfolgreich starten, wird der Spieler darüber informiert und muss es erneut versuchen.  

Das Plugin kann außerdem Spieler, die von einem Backend-Server gekickt wurden, zum Warte-Server weiterleiten. Diese Funktion kann in der Konfigurationsdatei aktiviert oder deaktiviert werden.  

## Konfiguration  

### Pterodactyl-Panel  

Falls du ein Pterodactyl-Panel zur Verwaltung deiner Server nutzt, kannst du diese Methode verwenden.  

1. Erstelle einen Client-API-Schlüssel unter „Account Settings“ → „API Credentials“. Die URL ist `/account/api`.  
2. Richte einen Warte-Server in deiner `velocity.toml`-Datei ein, z. B.:  

   ```toml
   [servers]
   limbo = "localhost:30066"
   survival = "localhost:30067"

   try = ["survival"]

   [forced-hosts]
   "localhost" = ["survival"]
   ```

3. Installiere das Plugin auf deinem Velocity-Proxy. Beim ersten Start wird automatisch eine Standardkonfiguration erstellt.  
4. Bearbeite die Konfigurationsdatei des Plugins und trage deine Pterodactyl-Anmeldedaten ein:  

   ```yaml
   type: "pterodactyl" # Alternativ "shell" – siehe README für Details
   # Client-API-Schlüssel aus "Account Settings" → "API Credentials" abrufen
   pterodactyl_api_key: "ptlc_xxx"
   pterodactyl_client_api_base_url: "https://example.com/api/client"
   servers:
     # "survival" ist der Name des Servers aus der "velocity.toml"
     # "Server ID" ist die Kennung des Servers im Pterodactyl-Panel (siehe "Debug Information" unter "Settings")
     survival: "Server ID"
   waiting_server_name: "limbo" # Warte-Server aus der "velocity.toml"
   maximum_ping_duration: 60 # Standard: 1 Minute
   shutdown_after_duration: 3_600 # Standard: 1 Stunde
   redirect_to_waiting_server_on_kick: true # Standard: false
   ```

> ⚠ **Achtung**  
> Falls dein Pterodactyl-Panel hinter einem Proxy wie CloudFlare läuft, kann das Plugin möglicherweise keine Server starten. Fehler wie der folgende können auftreten:  
> ```
> Ein Fehler ist aufgetreten beim Starten des Servers "survival"
> java.net.ConnectException: Connection refused
> ```

### Shell-Befehle  

Falls du kein Pterodactyl-Panel nutzt und deine Server direkt über die Linux-Shell startest, kannst du stattdessen Shell-Befehle in der Konfiguration angeben.  

🚨 **Hinweis:**  
Dies wurde nicht unter Windows getestet. Der `cd`-Befehl funktioniert nicht – verwende stattdessen `working_directory`.

**Beispiel mit Docker Compose:**  

```yaml
type: "shell"
servers:
  survival:
    # Falls nicht angegeben, wird das aktuelle Arbeitsverzeichnis genutzt
    working_directory: /path/to/docker/compose
    start: docker compose start survival
    stop: docker compose stop survival
waiting_server_name: "limbo"
maximum_ping_duration: 60
shutdown_after_duration: 3_600
redirect_to_waiting_server_on_kick: true
```

## Warte-Server  

Empfohlene lightweight Server-Software für den Warte-Server:  

- [Limbo](https://www.spigotmc.org/resources/82468/)  
- [NanoLimbo](https://www.spigotmc.org/resources/86198/)  
- [Quozul/McServer](https://github.com/Quozul/McServer)  

## Motivation  

Ich habe das Plugin gefunden und wollte für mich eine Deutsche version. Ansonsten wollte ich hiermit auch eine Möglichkeit für meine Muttersprache geben dieses Plugin zu verwenden.
