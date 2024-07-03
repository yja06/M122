#!/bin/bash

# Script für die Ausgabe der Systeminformationen in einer Tabelle
print_system_info() {
    # Aktuelle Systemlaufzeit und Systemzeit
    uptime=$(uptime -p)
    current_time=$(date '+%Y-%m-%d %H:%M:%S')

    # Speicherplatzinfos
    disk_space=$(df -h / | awk 'NR==2 {print $2 " insgesamt, " $4 " frei"}')

    # Hostname und IP Adresse
    hostname=$(hostname)
    ip_address=$(hostname -I | awk '{print $1}')

    # Betriebssystemname und -version
    os_name=$(cat /etc/*-release | grep PRETTY_NAME | cut -d '=' -f 2 | tr -d '"')

    # CPU Informationen
    cpu_model=$(cat /proc/cpuinfo | grep "model name" | uniq | cut -d ':' -f 2 | tr -s ' ')
    cpu_cores=$(nproc)

    # Arbeitsspeicher
    mem_total=$(free -h | awk 'NR==2 {print $2}')
    mem_used=$(free -h | awk 'NR==2 {print $3}')

    # Tabelle ausgeben
    printf "%-25s %s\n" "Systemlaufzeit:" "$uptime"
    printf "%-25s %s\n" "Systemzeit:" "$current_time"
    printf "%-25s %s\n" "Speicherplatz:" "$disk_space"
    printf "%-25s %s\n" "Hostname:" "$hostname"
    printf "%-25s %s\n" "IP-Adresse:" "$ip_address"
    printf "%-25s %s\n" "Betriebssystem:" "$os_name"
    printf "%-25s %s\n" "CPU-Modell:" "$cpu_model"
    printf "%-25s %s\n" "CPU-Cores:" "$cpu_cores"
    printf "%-25s %s\n" "Arbeitsspeicher insgesamt:" "$mem_total"
    printf "%-25s %s\n\n" "Arbeitsspeicher genutzt:" "$mem_used"
}

# Hauptprogramm
print_system_info

# Option -f für Dateiausgabe prüfen und verarbeiten
if [ "$1" == "-f" ]; then
    log_file="$(date '+%Y-%m')-sys-$(hostname).log"
    print_system_info >> "$log_file"
    echo "Systeminformationen wurden in die Datei $log_file geschrieben."
fi


# Script ausführbar machen
chmod +x system_leistungsabfrage.sh

# Script testen
./system_leistungsabfrage.sh

# Script ausführen mit Logfile
./system_leistungsabfrage.sh -f

# Script regelmässig machen
crontab -e

# In cron einfügen
## täglich um 6 Uhr wird der Script ausgeführt
0 6 * * * /pfad/zum/skript/system_leistungsabfrage.sh -f
