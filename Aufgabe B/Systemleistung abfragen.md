#!/bin/bash

# Funktion für Systeminfos
collect_info() {
    local uptime=$(uptime -p)
    local sys_time=$(date '+%Y-%m-%d %H:%M:%S')
    local disk_info=$(df -h --total | grep 'total')
    local disk_space_total=$(echo "$disk_info" | awk '{print $2}')
    local disk_space_free=$(echo "$disk_info" | awk '{print $4}')
    local hostname=$(hostname)
    local ip_address=$(hostname -I | awk '{print $1}')
    local os_name=$(cat /etc/*-release | grep PRETTY_NAME | cut -d '=' -f 2 | tr -d '"')
    local cpu_model=$(lscpu | grep 'Model name:' | sed 's/Model name:\s*//')
    local cpu_cores=$(nproc)
    local mem_info=$(free -h | grep 'Mem:')
    local total_mem=$(echo "$mem_info" | awk '{print $2}')
    local used_mem=$(echo "$mem_info" | awk '{print $3}')

    # Tabellenkopf
    printf "%-20s | %-40s\n" "Metrik" "Wert"
    printf "%-20s | %-40s\n" "--------" "----------------------------------------"

    # Tabelle
    printf "%-20s | %-40s\n" "Systemlaufzeit" "$uptime"
    printf "%-20s | %-40s\n" "Systemzeit" "$sys_time"
    printf "%-20s | %-40s\n" "Disk Speicher Total" "$disk_space_total"
    printf "%-20s | %-40s\n" "Disk Speicher Frei" "$disk_space_free"
    printf "%-20s | %-40s\n" "Hostname" "$hostname"
    printf "%-20s | %-40s\n" "IP-Adresse" "$ip_address"
    printf "%-20s | %-40s\n" "OS-Name" "$os_name"
    printf "%-20s | %-40s\n" "CPU Modell" "$cpu_model"
    printf "%-20s | %-40s\n" "CPU Cores" "$cpu_cores"
    printf "%-20s | %-40s\n" "Arbeitsspeicher Total" "$total_mem"
    printf "%-20s | %-40s\n" "Arbeitsspeicher Genutzt" "$used_mem"
}

# Hauptprogramm
collect_info

# Option -f für Log Datei
if [ "$1" == "-f" ]; then
    log_file="$(date '+%Y-%m')-sys-$(hostname).log"
    collect_info >> "$log_file"
    echo "Systeminformationen wurden in die Datei $log_file geschrieben."
fi
