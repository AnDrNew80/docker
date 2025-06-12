# 🚀 Monitoring z Docker Compose

---

## Spis treści
* [🌟 Opis Projektu](#-opis-projektu)
* [📂 Struktura Katalogów](#-struktura-katalogów)
* [✔️ Wymagania Wstępne](#️-wymagania-wstępne)
* [🚀 Uruchamianie Projektu](#-uruchamianie-projektu)
* [🌐 Dostęp do Interfejsów](#-dostęp-do-interfejsów)
* [💻 Konfiguracja Monitoringu Windows (Opcjonalnie)](#-konfiguracja-monitoringu-windows-opcjonalnie)
* [🧹 Zatrzymywanie i Czyszczenie](#-zatrzymywanie-i-czyszczenie)

---

## 🌟 Opis Projektu

Ten projekt dostarcza łatwe w użyciu i wydajne środowisko do monitorowania infrastruktury. Wykorzystuje **Docker Compose** do orkiestracji trzech kluczowych komponentów: **Prometheus**, **Grafana** i **Node Exporter**.

* **Prometheus:** Potężny system do zbierania, przechowywania i przeszukiwania metryk opartych na szeregach czasowych. W tej konfiguracji monitoruje samego siebie oraz metryki hosta za pomocą Node Exportera.
* **Grafana:** Lider w wizualizacji danych, integrujący się płynnie z Prometheus. Umożliwia tworzenie dynamicznych i interaktywnych dashboardów, które przekształcają surowe metryki w czytelne wykresy i wskaźniki.
* **Node Exporter:** Agent zainstalowany na hoście, który zbiera i wystawia szeroki zakres metryk systemowych (takich jak użycie CPU, pamięci, dysku, ruch sieciowy) dla Prometheusa.

Dzięki konteneryzacji, cały stos monitoringu jest **łatwy do wdrożenia, przenośny** i minimalizuje konflikty z innymi aplikacjami na Twojej maszynie (pod warunkiem zwolnienia wymaganych portów).

---

## 📂 Struktura Katalogów

Projekt jest zorganizowany w przejrzysty sposób, co ułatwia zarządzanie konfiguracją: