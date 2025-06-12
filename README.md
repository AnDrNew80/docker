🚀 Monitoring z Docker Compose
Spis treści
🌟 Opis Projektu
📂 Struktura Katalogów
✔️ Wymagania Wstępne
🚀 Uruchamianie Projektu
🌐 Dostęp do Interfejsów
💻 Konfiguracja Monitoringu Windows (Opcjonalnie)
🧹 Zatrzymywanie i Czyszczenie
🌟 Opis Projektu
Ten projekt dostarcza łatwe w użyciu i wydajne środowisko do monitorowania infrastruktury. Wykorzystuje Docker Compose do orkiestracji trzech kluczowych komponentów: Prometheus, Grafana i Node Exporter.

Prometheus: Potężny system do zbierania, przechowywania i przeszukiwania metryk opartych na szeregach czasowych. W tej konfiguracji monitoruje samego siebie oraz metryki hosta za pomocą Node Exportera.
Grafana: Lider w wizualizacji danych, integrujący się płynnie z Prometheus. Umożliwia tworzenie dynamicznych i interaktywnych dashboardów, które przekształcają surowe metryki w czytelne wykresy i wskaźniki.
Node Exporter: Agent zainstalowany na hoście, który zbiera i wystawia szeroki zakres metryk systemowych (takich jak użycie CPU, pamięci, dysku, ruch sieciowy) dla Prometheusa.
Dzięki konteneryzacji, cały stos monitoringu jest łatwy do wdrożenia, przenośny i minimalizuje konflikty z innymi aplikacjami na Twojej maszynie (pod warunkiem zwolnienia wymaganych portów).

📂 Struktura Katalogów
Projekt jest zorganizowany w przejrzysty sposób, co ułatwia zarządzanie konfiguracją:

monitoring-docker/
├── docker-compose.yml              # Główny plik Docker Compose definiujący wszystkie usługi
├── grafana/
│   ├── grafana.ini                 # Główny plik konfiguracyjny Grafany
│   └── provisioning/               # Katalog dla automatycznej konfiguracji Grafany
│       └── datasources/
│           └── datasource.yml      # Definicja źródła danych Prometheus dla Grafany
└── prometheus/
    └── prometheus.yml              # Plik konfiguracyjny Prometheusa z celami monitoringu
✔️ Wymagania Wstępne
Zanim rozpoczniesz, upewnij się, że masz zainstalowane i działające następujące narzędzia na swoim systemie hosta:

Docker Engine: Silnik kontenerowy.
Docker Compose Plugin: Narzędzie do definiowania i uruchamiania aplikacji wielokontenerowych (zazwyczaj instalowane wraz z Docker Engine jako docker compose).
⚠️ Ważna uwaga dotycząca portów:
Jeśli na Twoim hoście (np. maszynie Debian) masz już zainstalowane i działające lokalne instancje Prometheus, Grafana lub Node Exporter, MUSISZ JE ZATRZYMAĆ I WYŁĄCZYĆ, aby uniknąć konfliktów portów z kontenerami.

Przykładowe komendy dla systemd:

Bash

sudo systemctl stop prometheus && sudo systemctl disable prometheus
sudo systemctl stop grafana-server && sudo systemctl disable grafana-server
sudo systemctl stop node_exporter && sudo systemctl disable node_exporter
sudo systemctl daemon-reload # Odświeżenie konfiguracji systemd po zmianach
Jeśli po tych krokach porty (np. 9090, 3000, 9100) nadal są zajęte, użyj sudo lsof -i :<PORT> (np. sudo lsof -i :9100) lub sudo ss -tuln | grep <PORT> aby zidentyfikować i zabić proces (sudo kill -9 <PID>). W skrajnych przypadkach rozważ restart maszyny wirtualnej.

🚀 Uruchamianie Projektu
Wykonaj poniższe kroki, aby szybko uruchomić stos monitoringu:

Sklonuj to repozytorium lub utwórz ręcznie strukturę katalogów jak pokazano w sekcji Struktura Katalogów.
Upewnij się, że wszystkie pliki konfiguracyjne (prometheus.yml, grafana.ini, datasource.yml) są na swoim miejscu.
Przejdź do głównego katalogu projektu monitoring-docker w swoim terminalu:
Bash

cd /path/to/your/monitoring-docker # Zastąp ścieżką do Twojego katalogu
Uruchom wszystkie usługi za pomocą Docker Compose:
Bash

docker compose up -d
Opcja -d uruchamia kontenery w trybie odłączonym (w tle).
🌐 Dostęp do Interfejsów
Po pomyślnym uruchomieniu kontenerów możesz uzyskać dostęp do interfejsów webowych:

Prometheus UI: Otwórz przeglądarkę i przejdź do:
http://localhost:9090 (lub http://<IP_Twojej_Maszyny_Debian>:9090)

Przejdź do zakładki Status -> Targets, aby upewnić się, że node_exporter (dla hosta) i prometheus są w stanie UP.
Grafana Dashboard: Otwórz przeglądarkę i przejdź do:
http://localhost:3000 (lub http://<IP_Twojej_Maszyny_Debian>:3000)

Dostęp anonimowy jest włączony domyślnie. Prometheus powinien być już automatycznie skonfigurowany jako źródło danych.
Aby wizualizować metryki, możesz importować gotowe dashboardy z Grafana Labs Dashboards. Popularne ID dla Node Exportera to np. 1860 lub 11074.
💻 Konfiguracja Monitoringu Windows (Opcjonalnie)
Aby rozszerzyć monitoring o maszynę Windows (np. z adresem IP 192.168.0.103), na której działa windows_exporter (domyślnie na porcie 9182):

Upewnij się, że windows_exporter jest poprawnie zainstalowany i uruchomiony na maszynie Windows, a port 9182 jest otwarty w firewallu tej maszyny.

Edytuj plik prometheus/prometheus.yml i dodaj nową sekcję job_name w bloku scrape_configs:

YAML

# ... (istniejąca konfiguracja Prometheusa) ...

scrape_configs:
  # ... (istniejące joby, np. prometheus, node_exporter) ...

  - job_name: 'windows_server' # Nazwa joba - dowolna, ale opisowa
    static_configs:
      - targets: ['192.168.0.103:9182'] # Zastąp IP adresem swojej maszyny Windows
Zapisz plik prometheus/prometheus.yml.

Zrestartuj tylko kontener Prometheusa, aby wczytał nową konfigurację:

Bash

docker compose restart prometheus
Zweryfikuj w Prometheus UI (http://localhost:9090 -> Status -> Targets), czy nowy cel windows_server ma status UP.

W Grafanie możesz zaimportować dedykowane dashboardy dla Windows Exportera (np. ID 14603 lub 16262 z Grafana Labs), aby wizualizować te dane.

🧹 Zatrzymywanie i Czyszczenie
Zatrzymanie usług (zachowując dane woluminów):

Bash

docker compose stop
Zatrzymanie i usunięcie kontenerów (zachowując dane woluminów):

Bash

docker compose down
Zatrzymanie i usunięcie kontenerów, sieci ORAZ wszystkich danych woluminów (czyszczenie środowiska):

Bash

docker compose down --volumes --remove-orphans
⚠️ Użyj tej opcji ostrożnie! Spowoduje to usunięcie wszystkich zebranych metryk Prometheusa oraz wszystkich danych Grafany (dashboardów, użytkowników itp.), które nie są zdefiniowane jako bind mounts w plikach konfiguracyjnych.

