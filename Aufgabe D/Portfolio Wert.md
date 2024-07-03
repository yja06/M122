#!/bin/bash

# URLs
nvidia_url="https://www.alphavantage.co/query?function=TIME_SERIES_INTRADAY&symbol=NVDA&interval=1min&apikey=H3BLGHRZFVS6LKMZ"
usd_to_chf_url="https://www.alphavantage.co/query?function=CURRENCY_EXCHANGE_RATE&from_currency=USD&to_currency=CHF&apikey=H3BLGHRZFVS6LKMZ"
solana_url="https://api.coingecko.com/api/v3/simple/price?ids=solana&vs_currencies=usd"

# Anzahl
aktien_nvidia=5938
usd=3000
solana=14399

# Kaufpreise in CHF
kaufpreis_nvidia=73
kaufpreis_solana=21
kaufpreis_usd=0.9  # Beispielwert, als USD in CHF umgerechnet wurde

# Log-Datei
log_file="portfolio_log.txt"

# Funktion für NVIDIA PREIS
get_nvidia_price() {
    curl -s "$nvidia_url" | jq -r '.["Time Series (1min)"] | to_entries | .[0] | .value["4. close"]'
}

# Funktion für Solana Preis
get_solana_price() {
    curl -s "$solana_url" | jq -r '.solana.usd'
}

# Funktion für USD zu CHF Wechselkurs
get_usd_to_chf_rate() {
    curl -s "$usd_to_chf_url" | jq -r '.["Realtime Currency Exchange Rate"]["5. Exchange Rate"]'
}

calculate_portfolio_value() {
    timestamp=$(date +"%Y-%m-%d %H:%M:%S")

    # NVIDIA Preis abrufen
    nvidia_price=$(get_nvidia_price)
    if [[ -z "$nvidia_price" ]]; then
        echo "Error: Could not retrieve NVIDIA price."
        exit 1
    fi

    # USD zu CHF Wechselkurs abrufen
    usd_to_chf_rate=$(get_usd_to_chf_rate)
    if [[ -z "$usd_to_chf_rate" ]]; then
        echo "Error: Could not retrieve USD to CHF exchange rate."
        exit 1
    fi
    nvidia_price_chf=$(echo "$nvidia_price * $usd_to_chf_rate" | bc)

    # Solana Preis abrufen
    solana_price=$(get_solana_price)
    if [[ -z "$solana_price" ]]; then
        echo "Error: Could not retrieve Solana price."
        exit 1
    fi
    solana_price_chf=$(echo "$solana_price * $usd_to_chf_rate" | bc)

    # Werte in CHF berechnen
    nvidia_value_chf=$(echo "$aktien_nvidia * $nvidia_price_chf" | bc)
    usd_value_chf=$usd
    solana_value_chf=$(echo "$solana * $solana_price_chf" | bc)

    total_portfolio_value_chf=$(echo "$nvidia_value_chf + $usd_value_chf + $solana_value_chf" | bc)

    # Berechnung des Gewinns/Verlusts
    initial_nvidia_value_chf=$(echo "$aktien_nvidia * $kaufpreis_nvidia" | bc)
    initial_usd_value_chf=$usd
    initial_solana_value_chf=$(echo "$solana * $kaufpreis_solana" | bc)
    total_initial_value_chf=$(echo "$initial_nvidia_value_chf + $initial_usd_value_chf + $initial_solana_value_chf" | bc)
    profit_loss=$(echo "$total_portfolio_value_chf - $total_initial_value_chf" | bc)

    # Ergebnisse ausgeben und ins Logfile schreiben
    echo "Timestamp: $timestamp"
    echo "NVIDIA Aktien ($aktien_nvidia Stück): CHF $nvidia_value_chf"
    echo "USD ($usd): CHF $usd_value_chf"
    echo "Solana ($solana SOL): CHF $solana_value_chf"
    echo "Gesamter Depotwert in CHF: $total_portfolio_value_chf"
    echo "Initialer Depotwert in CHF: $total_initial_value_chf"
    echo "Gewinn/Verlust in CHF: $profit_loss"

    # Ergebnisse ins Logfile schreiben
    echo "$timestamp, $nvidia_value_chf, $usd_value_chf, $solana_value_chf, $total_portfolio_value_chf, $total_initial_value_chf, $profit_loss" >> $log_file
}

calculate_portfolio_value


# Script regelmässig machen
crontab -e

# alle 6 Stunden
0 */6 * * * /path/to/your/script/portfolioWert.sh
