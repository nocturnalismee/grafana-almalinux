## Langkah 1: Buat Pengguna Prometheus

Buat pengguna baru untuk menjalankan Prometheus.

```bash
sudo useradd --no-create-home --shell /bin/false prometheus
sudo groupadd --system prometheus
sudo usermod -a -G prometheus prometheus
```

## Langkah 2: Buat Direktori untuk Prometheus

Buat direktori untuk file konfigurasi dan data Prometheus.

```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```

## Langkah 3: Unduh dan Ekstrak Prometheus

Unduh versi terbaru dari Prometheus dari situs web resmi atau gunakan `wget`.

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.37.1/prometheus-2.37.1.linux-amd64.tar.gz
```

Sesuaikan URL unduhan dengan versi terbaru yang tersedia.

Ekstrak file yang diunduh:

```bash
tar xvfz prometheus-2.37.1.linux-amd64.tar.gz
```

## Langkah 4: Pindahkan File Biner dan Konfigurasi

Pindahkan file biner Prometheus ke direktori yang sesuai dan atur izin.

```bash
sudo mv prometheus-2.37.1.linux-amd64/prometheus /usr/local/bin/
sudo mv prometheus-2.37.1.linux-amd64/promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```

Pindahkan file konfigurasi contoh dan atur izin.

```bash
sudo mv prometheus-2.37.1.linux-amd64/consoles /etc/prometheus/
sudo mv prometheus-2.37.1.linux-amd64/console_libraries /etc/prometheus/
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
```

Buat file konfigurasi Prometheus utama.

```bash
sudo mv prometheus-2.37.1.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
sudo chown prometheus:prometheus /etc/prometheus/prometheus.yml
```

Atur izin untuk direktori data.

```bash
sudo chown prometheus:prometheus /var/lib/prometheus
```

## Langkah 5: Buat Unit Systemd untuk Prometheus

Buat file unit Systemd untuk menjalankan Prometheus sebagai layanan.

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Masukkan konten berikut ke dalam file:

```ini
[Unit]
Description=Prometheus
Documentation=https://prometheus.io/docs/
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

Simpan dan tutup file.

Muat ulang konfigurasi Systemd:

```bash
sudo systemctl daemon-reload
```

Mulai dan aktifkan layanan Prometheus:

```bash
sudo systemctl start prometheus
sudo systemctl enable prometheus
```

Verifikasi status layanan Prometheus:

```bash
sudo systemctl status prometheus
```

Jika semuanya berjalan dengan benar, Anda akan melihat status `active (running)`.

## Langkah 6: Konfigurasi Firewall (Opsional)

Jika Anda menggunakan firewall, buka port default Prometheus (9090).

```bash
sudo firewall-cmd --permanent --add-port=9090/tcp
sudo firewall-cmd --reload
```

## Langkah 7: Akses Antarmuka Web Prometheus

Anda sekarang dapat mengakses antarmuka web Prometheus melalui browser web dengan alamat IP atau nama host server Anda di port 9090.

```
http://localhost:9090
```

## Konfigurasi Dasar Prometheus

File konfigurasi utama Prometheus adalah `/etc/prometheus/prometheus.yml`. Berikut adalah contoh konfigurasi dasar:

```yaml
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. Default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090']
```

Anda dapat mengedit file ini untuk menambahkan target scraping baru (misalnya, Node Exporter, server web, dll.) dan mengonfigurasi aturan peringatan. Setelah melakukan perubahan, muat ulang konfigurasi Prometheus:

```bash
sudo systemctl reload prometheus
```

## Kesimpulan

Anda sekarang telah berhasil menginstal dan mengonfigurasi Prometheus di AlmaLinux 9. Anda dapat mulai menambahkan target ke konfigurasi Anda dan memanfaatkan fitur pemantauan dan peringatan Prometheus.

