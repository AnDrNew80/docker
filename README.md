Projekt: Monitoring z Docker Compose (Prometheus, Grafana, Node Exporter)

Spis treści
Opis projektu
Struktura katalogów
Wymagania wstępne
Uruchamianie projektu
Dostęp do interfejsów
Konfiguracja monitoringu Windows (Opcjonalnie)
Zatrzymywanie i czyszczenie


Opis projektu
Ten projekt zapewnia proste i efektywne środowisko do monitorowania infrastruktury, wykorzystując Docker Compose do orkiestracji kluczowych komponentów: Prometheus, Grafana i Node Exporter.

Prometheus: System do zbierania i przechowywania metryk z różnych źródeł. W tej konfiguracji zbiera metryki z samego siebie oraz z Node Exportera działającego w kontenerze.
Grafana: Platforma do wizualizacji danych, która integruje się z Prometheus. Pozwala na tworzenie interaktywnych dashboardów.
Node Exporter: Narzędzie do wystawiania metryk systemowych (CPU, pamięć, dysk, sieć) z hosta Linux, na którym działa Docker.
Dzięki konteneryzacji, ten zestaw narzędzi jest łatwy do wdrożenia, przenośny i nie koliduje z innymi lokalnie zainstalowanymi usługami (pod warunkiem zwolnienia portów).


Struktura katalogów
Projekt jest zorganizowany w następujący sposób:

monitoring-docker/
├── docker-compose.yml              # Główny plik Docker Compose definiujący usługi
├── grafana/
│   ├── grafana.ini                 # Główny plik konfiguracyjny Grafany
│   └── provisioning/
│       └── datasources/
│           └── datasource.yml      # Automatyczna konfiguracja źródła danych Prometheus w Grafanie
└── prometheus/
    └── prometheus.yml              # Plik konfiguracyjny Prometheusa (cele scrapingu)
	

Wymagania wstępne
Zanim uruchomisz projekt, upewnij się, że masz zainstalowane i działające następujące oprogramowanie:

Docker Engine: Instrukcje instalacji
Docker Compose Plugin: (Zazwyczaj instalowany razem z Docker Engine jako docker compose)
Ważne: Jeśli masz lokalnie zainstalowane usługi Prometheus, Grafana lub Node Exporter na hoście, na którym uruchamiasz Dockera (np. Twoja maszyna Debian), MUSISZ je zatrzymać i wyłączyć, aby uniknąć konfliktów portów.
Przykład dla systemd:

Bash

sudo systemctl stop prometheus
sudo systemctl disable prometheus
sudo systemctl stop grafana-server
sudo systemctl disable grafana-server
sudo systemctl stop node_exporter
sudo systemctl disable node_exporter
sudo systemctl daemon-reload # Odświeżenie konfiguracji systemd
Jeśli mimo to porty są zajęte, możesz użyć sudo lsof -i :<PORT> (np. sudo lsof -i :9100) aby znaleźć PID procesu i zabić go (sudo kill -9 <PID>). W skrajnych przypadkach konieczny może być restart maszyny wirtualnej.

Uruchamianie projektu
Sklonuj lub utwórz strukturę katalogów jak pokazano w sekcji Struktura katalogów.
Uzupełnij pliki konfiguracyjne zgodnie z opisem. Pliki prometheus.yml, grafana.ini i datasource.yml są już przygotowane w repozytorium.
Przejdź do katalogu głównego projektu monitoring-docker:
Bash

cd monitoring-docker
Uruchom wszystkie usługi za pomocą Docker Compose:
Bash

docker compose up -d
Opcja -d uruchamia kontenery w trybie detached (w tle).
Dostęp do interfejsów
Po pomyślnym uruchomieniu kontenerów, możesz uzyskać dostęp do interfejsów webowych:

Prometheus UI: http://localhost:9090 (lub http://<IP_Twojego_Debiana>:9090)
Sprawdź Status -> Targets, aby upewnić się, że node_exporter i prometheus są UP.
Grafana Dashboard: http://localhost:3000 (lub http://<IP_Twojego_Debiana>:3000)
Anonimowy dostęp jest włączony domyślnie. Prometheus powinien być już skonfigurowany jako źródło danych. Możesz importować dashboardy dla Node Exportera (np. ID 1860 lub 11074 z Grafana Labs Dashboards).
Konfiguracja monitoringu Windows (Opcjonalnie)
Aby Prometheus w Dockerze monitorował Twoją maszynę Windows (np. 192.168.0.103) z windows_exporter działającym na porcie 9182:

Upewnij się, że windows_exporter działa na maszynie Windows i port 9182 jest otwarty w firewallu.

Edytuj plik prometheus/prometheus.yml i dodaj nową sekcję job_name:

YAML

# ... (pozostała konfiguracja) ...

scrape_configs:
  # ... (istniejące joby) ...

  - job_name: 'windows_server'
    static_configs:
      - targets: ['192.168.0.103:9182'] # Zastąp IP adresem Twojej maszyny Windows
Zapisz plik prometheus/prometheus.yml.

Zrestartuj tylko kontener Prometheusa, aby załadował nową konfigurację:

Bash

docker compose restart prometheus
Zweryfikuj w Prometheus UI -> Status -> Targets, czy windows_server ma status UP.

Zatrzymywanie i czyszczenie
Zatrzymanie usług (zachowując dane):

Bash

docker compose stop
Zatrzymanie i usunięcie kontenerów (zachowując dane woluminów):

Bash

docker compose down
Zatrzymanie i usunięcie kontenerów, sieci ORAZ wszystkich danych woluminów (czyszczenie):

Bash

docker compose down --volumes
Użyj tej opcji ostrożnie, ponieważ usunie wszystkie zebrane metryki Prometheusa i konfiguracje Grafany (dashboardy, użytkownicy, itp.), które nie są zdefiniowane w plikach konfiguracyjnych montowanych jako bind mounts.