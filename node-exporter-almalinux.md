
## Langkah 1: Instal Node Exporter di Almalinux

1.  **Perbarui sistem:**

    ```bash
    sudo dnf update -y
    ```

2.  **Unduh Node Exporter:**

    ```bash
    wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
    ```

3.  **Ekstrak arsip:**

    ```bash
    tar xvfz node_exporter-1.9.1.linux-amd64.tar.gz
    ```

4.  **Pindahkan berkas biner ke direktori sistem:**

    ```bash
    sudo mv node_exporter-1.9.1.linux-amd64/node_exporter /usr/local/bin/
    ```

5.  **Bersihkan berkas yang diekstrak:**

    ```bash
    rm -rf node_exporter-1.9.1.linux-amd64.tar.gz node_exporter-1.9.1.linux-amd64
    ```

## Langkah 2: Buat Layanan Systemd untuk Node Exporter

Untuk mengelola Node Exporter sebagai sebuah layanan, buat berkas layanan systemd.

1.  **Buat berkas layanan:**

    ```bash
    sudo nano /etc/systemd/system/node_exporter.service
    ```

2.  **Tambahkan konten berikut ke berkas:**

    ```ini
    [Unit]
    Description=Node Exporter
    Wants=network-online.target
    After=network-online.target

    [Service]
    User=nobody
    Group=nobody
    Type=simple
    ExecStart=/usr/local/bin/node_exporter

    [Install]
    WantedBy=multi-user.target
    ```

3.  **Simpan dan tutup berkas.**

4.  **Muat ulang konfigurasi systemd:**

    ```bash
    sudo systemctl daemon-reload
    ```

5.  **Mulai layanan Node Exporter:**

    ```bash
    sudo systemctl start node_exporter
    ```

6.  **Aktifkan layanan agar berjalan saat boot:**

    ```bash
    sudo systemctl enable node_exporter
    ```

7.  **Verifikasi status layanan:**

    ```bash
    sudo systemctl status node_exporter
    ```

    Anda akan melihat status "active (running)".

## Langkah 3: Konfigurasi Firewall (jika ada)

Jika Anda menjalankan firewall di server AlmaLinux Anda, Anda perlu mengizinkan lalu lintas pada port default Node Exporter (9100).

```bash
sudo firewall-cmd --permanent --add-port=9100/tcp
sudo firewall-cmd --reload
