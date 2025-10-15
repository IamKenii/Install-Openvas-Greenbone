## Docker Installeren

### Stap 1: Voorbereiden van de sleutelmap

Maak een map aan voor de APT-sleutels met de juiste rechten:
```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### Stap 2: Docker GPG-sleutel downloaden

Download en sla de officiële Docker GPG-sleutel op:
```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### Stap 3: Leesrechten instellen

Geef alle gebruikers leesrechten voor de sleutel:
```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

### Stap 4: Docker-repository toevoegen

Voeg de Docker-repository toe aan je APT-bronnen:
```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Stap 5: Pakketlijst updaten

Update de softwarelijst:
```bash
sudo apt update
```

### Stap 6: Docker installeren

Installeer Docker en alle benodigde componenten:
```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

---

## Greenbone Vulnerability Manager Installeren

### Stap 1: Installatiescript downloaden

Download het officiële installatiescript:
```bash
curl -f -O https://greenbone.github.io/docs/latest/_static/setup-and-start-greenbone-community-edition.sh
```

### Stap 2: Script uitvoerbaar maken

Geef uitvoerrechten aan het script:
```bash
chmod u+x setup-and-start-greenbone-community-edition.sh
```

### Stap 3: Installatie uitvoeren

Voer het script uit met sudo-rechten:
```bash
sudo ./setup-and-start-greenbone-community-edition.sh
```

### Stap 4: Toegang tot de webinterface

Na succesvolle installatie is de webinterface beschikbaar op:

**http://127.0.0.1:9392**

---

## Externe Toegang Configureren 
Als je toegang wilt vanaf een andere machine, moet je de poortconfiguratie aanpassen.

### Docker Compose configuratie aanpassen

1. **Schakel over naar root en navigeer naar de juiste map:**
```bash
sudo su
cd
cd greenbone-community-container
nano docker-compose.yml
```

2. **Zoek de GSA-service configuratie:**
```yaml
gsa:
  image: registry.community.greenbone.net/community/gsa:stable
  restart: on-failure
  ports:
    - "9392:80"
  volumes:
    - gvmd_socket_vol:/run/gvmd
  depends_on:
    - gvmd
```

3. **Pas de poortconfiguratie aan:**
Wijzig `"127.0.0.1:9392:80"` naar `"9392:80"` voor externe toegang.

4. **Sla het bestand op:**
    - Druk op `Ctrl + S` om op te slaan
    - Druk op `Ctrl + X` om nano af te sluiten

### Docker Compose herstarten

Stop de containers:
```bash
sudo docker compose down
```

Start de containers opnieuw op:
```bash
sudo docker compose up -d
```

### Probleemoplossing: Dubbele container

Als je een foutmelding krijgt over een dubbele 'gsa-1' container:
```bash
sudo docker rm -f greenbone-community-edition-gsa-1
```

Start daarna opnieuw op:
```bash
sudo docker compose up -d
```

### Status controleren

Controleer of alle containers correct draaien:
```bash
sudo docker ps
```

---

## Belangrijke opmerking

**Het initiële ophalen van alle CVE's duurt 30 tot 60 minuten.** Wees geduldig!
