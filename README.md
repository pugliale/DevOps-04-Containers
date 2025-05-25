# 🐳 DevOps 04 – Containerisierung mit Docker

## 👥 Zusammenarbeit

### 📄 Person und Repository

|                          |                                                                                              |
|--------------------------|----------------------------------------------------------------------------------------------|
| Bearbeiter               | Alessio Pugliese                                                                             |
| Repository               | [DevOps-04-Containers](https://github.com/pugliale/DevOps-04-Containers)                    |

---

## 💻 Verwendete Systeme & Tools

| Tool / Technologie     | Beschreibung                                                                                   |
|------------------------|-----------------------------------------------------------------------------------------------|
| **Docker**             | Container-Laufzeitumgebung zur Ausführung isolierter Anwendungen                             |
| **Docker Compose**     | Orchestrierungstool zum gleichzeitigen Start mehrerer Container über YAML-Datei              |
| **MySQL**              | Relationale Datenbank, als Container betrieben                                                |
| **Adminer**            | Weboberfläche zur Verwaltung von Datenbanken im Container                                    |
| **Visual Studio Code** | Entwicklungsumgebung mit Docker-Integration                                                  |
| **Git & GitHub**       | Versionskontrolle und Quellcodeverwaltung                                                    |

---

## 📖 Theoretischer Hintergrund

### 📦 Containerisierung

**Container** bieten eine leichtgewichtige Virtualisierung, die Anwendungen und deren Abhängigkeiten in abgeschlossene Umgebungen kapselt. Im Gegensatz zu virtuellen Maschinen benötigen Container kein eigenes Betriebssystem – sie teilen sich den Kernel des Hosts und sind dadurch deutlich ressourcenschonender.

Vorteile:
- Konsistente Laufzeitumgebung (egal ob lokal, Test oder Produktion)
- Schnelles Deployment
- Leichte Portabilität

### 🐳 Docker

**Docker** ist die derzeit führende Plattform für Containerisierung. Es erlaubt:
- das Erstellen von Images (über ein `Dockerfile`)
- das Starten und Verwalten von Containern
- das Verbinden von Containern über Netzwerke
- das Speichern und Verteilen von Images über Docker Hub

Zentrale Begriffe:
- **Image**: Vorlage eines Containers (z. B. `mysql:8.0`)
- **Container**: laufende Instanz eines Images
- **Volume**: persistente Speicherung ausserhalb des Containers
- **Network**: Kommunikation zwischen Containern

### 🔧 Docker Compose

**Docker Compose** erlaubt das deklarative Starten und Verwalten mehrerer Container über eine zentrale Konfigurationsdatei (`docker-compose.yml`). Damit können:
- mehrere Dienste gleichzeitig gestartet werden
- Abhängigkeiten (z. B. DB + Adminer UI) definiert werden
- Netzwerke und Volumes angelegt werden

Beispielhafte Vorteile:
- Einfache Wiederverwendbarkeit und Wartbarkeit
- Übersichtliche Strukturierung komplexer Anwendungen
- Reduzierung manueller CLI-Befehle auf einen zentralen Startbefehl:  
  ```bash
  docker-compose up
  ```

---

## 🔧 Praktische Umsetzung

### 🐳 Image Pulls & Vorbereitung

Im ersten Schritt wurden die benötigten Images mit dem Docker CLI-Befehl `docker pull` lokal heruntergeladen. Dies garantiert, dass die Container auch ohne Internetverbindung oder bei Netzwerkproblemen verwendet werden können.

1. `mysql:latest` wurde für die Datenbank verwendet  
2. `adminer:latest` als grafische Oberfläche zur Verwaltung der MySQL-Datenbank

```bash
docker pull mysql
docker pull adminer
```

<img src="Images/Bild1.png" width="600">
<img src="Images/Bild2.png" width="600">
<img src="Images/Bild3.png" width="600">

### ⚙️ Setup MySQL Container

Ein erster MySQL-Container wurde manuell über die CLI gestartet. Wichtige Parameter dabei:

- `--name` zur Vergabe eines Container-Namens
- `-e` zur Übergabe von Umgebungsvariablen wie `MYSQL_ROOT_PASSWORD`
- `-p` zur Portfreigabe
- `-d` für den Detached-Modus

```bash
docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -d mysql
```

<img src="Images/Bild5.png" width="600">
<img src="Images/Bild6.png" width="600">

### 🖥️ Adminer starten und verbinden

Anschliessend wurde `Adminer` gestartet und mit dem laufenden MySQL-Container verbunden:

```bash
docker run --name adminer-container -p 8080:8080 -d adminer
```

Die Oberfläche war via `http://localhost:8080` erreichbar. Verbindung zu MySQL erfolgte über `localhost`, Benutzer `root`, Passwort `root`.

<img src="Images/Bild7.png" width="600">
<img src="Images/Bild8.png" width="600">
<img src="Images/Bild9.png" width="600">

### 🗄️ Datenbank und Tabelle anlegen

In Adminer wurde über die Weboberfläche:
- eine neue Datenbank angelegt
- eine Tabelle mit Attributen erstellt

Die Eingaben wurden korrekt gespeichert und dargestellt.

<img src="Images/Bild10.png" width="600">
<img src="Images/Bild11.png" width="600">
<img src="Images/Bild12.png" width="600">

---

### 📑 docker-compose.yml erstellen

Zur Vereinfachung des Container-Setups wurde anschliessend eine `docker-compose.yml` erstellt. Ziel war es, beide Container (MySQL + Adminer) mit einem einzigen Befehl zu starten – inklusive Volumes und Netzwerken.

**Inhalt der Datei:**

```yaml
version: '3.1'

services:
  mysql:
    image: mysql
    container_name: mysql-container
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3306:3306"

  adminer:
    image: adminer
    container_name: adminer-container
    restart: always
    ports:
      - "8080:8080"
```

```bash
docker-compose up -d
```

Mit diesem Befehl wurden beide Container automatisch erstellt und gestartet. Die Verbindung und Datenbankstruktur funktionierte weiterhin wie zuvor, allerdings war das Setup jetzt reproduzierbar und deutlich einfacher zu verwalten.

<img src="Images/Bild13.png" width="600">
<img src="Images/Bild14.png" width="600">

---

## 🧠 Erkenntnisse und Fazit

### 🔍 Was habe ich gelernt?

- Die Arbeit mit Containern bietet **enorme Vorteile in Bezug auf Portabilität, Konsistenz und Wartbarkeit**.
- Mit **Docker** lassen sich Umgebungen in Sekundenschnelle erstellen – unabhängig vom Host-System.
- Durch den Einsatz von **Docker Compose** konnte ich mehrere Container gleichzeitig starten und konfigurieren – mit nur einer Datei.
- Die Anbindung von **Adminer** ermöglichte einen schnellen, grafischen Zugang zur Datenbank und erleichterte die Überprüfung und Verwaltung erheblich.
- Auch die **CLI-Kommandos** und das Verhalten bei Netzwerk- und Portfreigaben wurden durch praktisches Arbeiten vertieft verstanden.

### 💬 Persönliche Reflexion

Die Übung war sehr praxisorientiert und hat mir geholfen, das Konzept der Containerisierung nicht nur theoretisch, sondern auch im Detail praktisch nachzuvollziehen. Insbesondere die Verbindung von Datenbank und Adminoberfläche sowie die Umstellung auf Docker Compose zeigten mir, wie einfach heute komplexe Infrastrukturen abgebildet werden können.

> 🧩 *„Container sind keine Blackboxes – sie sind definierte, transparente und kontrollierbare Bausteine moderner Softwareentwicklung.“*

---

📅 *Frühjahrssemester 2025 – ZHAW School of Management and Law*
