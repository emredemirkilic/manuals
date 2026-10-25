# LENOVO THINKPAD SL500

## Debian 13 Minimal + Amiberry 8.4.2 Kurulum Rehberi

**Sürüm:** 1.1 — 25 Ekim 2026  
**Referans:** Başarılı Lenovo SL500 kurulumu  
**Amaç:** Masaüstü ortamı olmadan, doğrudan Amiberry çalıştıran bağımsız bir Amiga emülasyon sistemi.

---

## 1. Donanım ve başarılı kurulumun özeti

| Bileşen | Lenovo SL500 |
| --- | --- |
| İşlemci | Intel Core 2 Duo P8600, 2.40 GHz |
| RAM | 4 GB |
| Depolama | SSD |
| Ekran kartı | NVIDIA GeForce G 105M |
| Grafik sürücüsü | Nouveau |
| İşletim sistemi | Debian 13 Trixie, amd64 |
| Grafik sistemi | Minimal X11 |
| Emülatör | Amiberry 8.4.2 |
| Ses | ALSA |
| Uzaktan yönetim | SSH / PuTTY |
| Dosya aktarımı | SFTP / FileZilla |
| Başlatma | Fiziksel konsoldan otomatik Amiberry |

**Başarılı son durum:** Amiberry 8.4.2 açılıyor, A500 ve A1200 yapılandırmaları çalışıyor, görüntü ve ses düzgün, oyunlar akıcı, emülatörden çıkınca AMIGA CONTROL CENTER menüsü kullanılabiliyor.

### Temel mimari

```text
LENOVO SL500
    |
    v
Debian 13 Minimal
    |
    +-- OpenSSH / SFTP
    |
    +-- Nouveau ekran sürücüsü
    |
    +-- ALSA ses sistemi
    |
    +-- Minimal X11
    |
    +-- Amiberry 8.4.2
    |
    +-- Otomatik başlatma
            |
            v
       AMIGA CONTROL CENTER
```

Tam masaüstü ortamı, GNOME, KDE veya ayrı bir pencere yöneticisi kurmadık.

---

## 2. Debian 13 minimal kurulumu

Debian 13 Trixie'nin **amd64** kurulumunu kullan.

Kurulum sırasında:

1. SSD üzerine Debian'ı kur.
2. Root şifresini ve normal kullanıcı hesabını yapılandır.
3. Yazılım seçim ekranında GNOME, KDE ve diğer masaüstü ortamlarını seçme.
4. SSH server ve standard system utilities seçeneklerini seçebilirsin.
5. Kurulumu tamamla ve terminale giriş yap.

İlk güncelleme:

```bash
su -             # Root hesabına geç.
apt update       # APT paket listelerini güncelle.
apt upgrade -y   # Kurulu paketleri güncelle ve onayları otomatik kabul et.
```

Temel yönetim araçları:

```bash
# Temel terminal, indirme, sistem izleme ve donanım tanılama araçlarını kur.
apt install -y \
  sudo nano vim less \
  bash-completion curl wget \
  ca-certificates htop \
  pciutils usbutils
```

**Kontrol:**

```bash
cat /etc/os-release  # İşletim sistemi ve sürüm bilgisini göster.
uname -m             # Sistem mimarisini göster; x86_64 beklenir.
free -h              # RAM ve swap kullanımını okunabilir biçimde göster.
lsblk                 # Disk ve bölüm yapısını listeler.
```

Beklenen mimari `x86_64` olmalıdır.

---

## 3. Ağ, SSH ve uzaktan yönetim

SL500'de SSH üzerinden PuTTY ile bağlantı kurduk.

### OpenSSH kurulumu

```bash
apt install -y openssh-server  # SSH sunucusunu kur.
```

Servisi etkinleştir:

```bash
systemctl enable --now ssh  # SSH servisini şimdi başlat ve açılışta otomatik çalıştır.
```

Kontrol:

```bash
systemctl status ssh --no-pager  # SSH servisinin durumunu göster.
```

Başarılı sonuç:

```text
Active: active (running)
```

IP adresini öğren:

```bash
hostname -I  # Sistemin atanmış IP adreslerini kısa biçimde göster.
```

veya:

```bash
ip -4 addr  # Ağ arayüzlerinin IPv4 adreslerini ayrıntılı göster.
```

### 3.1 Ethernet ve Wi-Fi için sabit IP yapılandırması

SL500 üzerinde kullandığımız sabit adresler:

| Bağlantı | Arayüz | Sabit IP |
| --- | --- | --- |
| Ethernet | `enp12s0` | `192.168.1.30` |
| Wi-Fi | `wlp3s0` | `192.168.1.31` |

Ağ geçidi:

```text
192.168.1.1
```

Ağ maskesi:

```text
255.255.255.0
```

Önemli: Bu adresleri modeminin DHCP havuzundan ayırmak veya modem üzerinden rezerve etmek gerekir. Aksi hâlde modem aynı IP adresini başka bir cihaza dağıtabilir.

#### Mevcut ağ yapılandırmasını yedekle

```bash
cp /etc/network/interfaces /etc/network/interfaces.bak  # Mevcut ağ yapılandırmasının yedeğini oluştur.
```

#### Ethernet için statik IP

```bash
cat > /etc/network/interfaces <<'EOF'
# Ek ağ yapılandırma dosyalarını da dahil et.
source /etc/network/interfaces.d/*

# Loopback arayüzünü otomatik başlat.
auto lo
iface lo inet loopback

# Ethernet arayüzünü otomatik başlat.
auto enp12s0

# Ethernet için statik IPv4 yapılandırması kullan.
iface enp12s0 inet static
    address 192.168.1.30
    netmask 255.255.255.0
    gateway 192.168.1.1
EOF
```

Dosyayı kontrol et:

```bash
cat /etc/network/interfaces  # Oluşturulan ağ yapılandırmasını ekrana yaz.
```

#### Wi-Fi için statik IP

Wi-Fi arayüzü:

```text
wlp3s0
```

Wi-Fi bağlantısını `wpa_supplicant` ile yönettik.

Yapılandırma dosyasını düzenle:

```bash
nano /etc/wpa_supplicant/wpa_supplicant-wlp3s0.conf  # Wi-Fi kimlik doğrulama ayarlarını düzenle.
```

Örnek yapı:

```conf
# wpa_supplicant'ın kontrol arayüzünü tanımla.
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev

# Yapılandırmanın güncellenmesine izin ver.
update_config=1

# Kablosuz ağ ülke kodunu tanımla.
country=TR

network={
    # Wi-Fi ağ adını yaz.
    ssid="WIFI_ADI"

    # Wi-Fi şifresini yaz.
    psk="WIFI_SIFRESI"
}
```

`/etc/network/interfaces` dosyasına Wi-Fi bölümünü ekle:

```bash
nano /etc/network/interfaces  # Ağ yapılandırma dosyasını yeniden aç.
```

Dosyada aşağıdaki Wi-Fi bölümünü ekle:

```conf
# Wi-Fi arayüzünü otomatik başlat.
auto wlp3s0

# Wi-Fi için statik IPv4 yapılandırması kullan.
iface wlp3s0 inet static
    address 192.168.1.31
    netmask 255.255.255.0
    wpa-conf /etc/wpa_supplicant/wpa_supplicant-wlp3s0.conf
```

Wi-Fi servisinin açılışta çalışmasını sağla:

```bash
systemctl enable wpa_supplicant@wlp3s0  # wlp3s0 için wpa_supplicant servisini açılışa ekle.
```

Ağ yapılandırmasını kontrol et:

```bash
ip -4 addr show enp12s0  # Ethernet arayüzünün IPv4 adresini göster.
ip -4 addr show wlp3s0   # Wi-Fi arayüzünün IPv4 adresini göster.
ip route                 # Varsayılan ağ geçidi ve yönlendirme tablosunu göster.
```

Beklenen:

```text
enp12s0 -> 192.168.1.30
wlp3s0  -> 192.168.1.31
```

Not: İki arayüz aynı anda aktif olduğunda yönlendirme tablosunda birden fazla varsayılan rota görülebilir. `ip route` ile kontrol et.

### PuTTY ile bağlanma

PuTTY'de:

```text
Host: 192.168.1.30 veya 192.168.1.31
Port: 22
Connection type: SSH
```

İlk bağlantıyı normal kullanıcı hesabıyla test et.

### İsteğe bağlı: root SSH erişimi

Önceki SL500 kurulumunda root SSH erişimini de açmıştık. Bu, FileZilla üzerinden sistem dizinlerine erişmeyi kolaylaştırdı.

Ancak **root hesabıyla şifreli SSH girişi güvenlik riskini artırır**. Tercih edilen yöntem normal kullanıcıyla SFTP kullanmak ve gerektiğinde `sudo` ile yönetim yapmaktır.

Yine de önceki davranışı birebir oluşturmak istiyorsan, yalnızca güvenilir yerel ağda ve internete 22 numaralı portu açmadan:

```bash
nano /etc/ssh/sshd_config.d/00-root-login.conf  # Root SSH giriş ayarları için ayrı dosya oluştur.
```

İçerik:

```text
PermitRootLogin yes
PasswordAuthentication yes
```

Yapılandırmayı doğrula:

```bash
sshd -t  # SSH yapılandırmasında sözdizimi hatası olup olmadığını kontrol et.
sshd -T | grep -E 'permitrootlogin|passwordauthentication'  # Etkin root ve parola ayarlarını göster.
```

Servis yeniden yüklemesini ancak hata olmadığını gördükten sonra yap:

```bash
systemctl reload ssh  # SSH servisini bağlantıları mümkün olduğunca koruyarak yeniden yükle.
```

Yeni root bağlantısını ayrı bir PuTTY penceresinde doğrulamadan mevcut SSH oturumunu kapatma.

### FileZilla

```text
Protocol: SFTP
Host: 192.168.1.30 veya 192.168.1.31
Port: 22
Username: normal kullanıcı veya izin verilmişse root
Password: ilgili hesap şifresi
```

---

## 4. Ekran kartı ve Nouveau sürücüsü

SL500'ün ekran kartı:

**NVIDIA GeForce G 105M**

Başarılı kurulumda kullanılan sürücü:

**Nouveau — açık kaynak Linux NVIDIA sürücüsü**

Kartı kontrol et:

```bash
lspci | grep -Ei 'vga|3d|display'  # Grafik denetleyicisini PCI aygıtları içinde bul.
```

Nouveau modülünü kontrol et:

```bash
lsmod | grep nouveau  # Nouveau çekirdek modülünün yüklü olup olmadığını göster.
```

DRM aygıtları:

```bash
ls -l /dev/dri/  # Grafik aygıtlarının DRM düğümlerini listeler.
```

Bizim kurulumumuzda Nouveau düzgün çalıştı. NVIDIA'nın eski proprietary sürücüsünü ayrıca yüklemedik.

### Dahili ekran çözünürlüğü

SL500'de başarılı şekilde kullandığımız çözünürlük:

**1024 × 768 @ 60 Hz**

Dahili panel için `LVDS-1` çıkışını kullandık.

GRUB dosyasını düzenle:

```bash
nano /etc/default/grub  # GRUB çekirdek başlangıç parametrelerini düzenle.
```

`GRUB_CMDLINE_LINUX_DEFAULT` satırındaki mevcut parametreleri koruyarak şu parametreyi ekle:

```text
video=LVDS-1:1024x768@60
```

Örneğin:

```text
GRUB_CMDLINE_LINUX_DEFAULT="quiet video=LVDS-1:1024x768@60"
```

Kaydet ve uygula:

```bash
update-grub  # GRUB yapılandırmasını yeniden oluştur.
reboot       # Yeni görüntü parametresini uygulamak için sistemi yeniden başlat.
```

**Doğrulanmış sonuç:** Yeniden başlatma sonrasında SL500'de 1024 × 768 çözünürlük elde edildi.

Not: Bu parametre SL500'de doğrulanmıştır. Farklı ekran çıkışı veya farklı bilgisayar için aynı `LVDS-1` adını varsaymamak gerekir.

---

## 5. Minimal X11 kurulumu

Tam masaüstü kurmadan Amiberry'yi çalıştırmamızın temel adımıydı.

Kullandığımız çekirdek paketler:

```bash
# X sunucusu, giriş aygıtları, X başlatma araçları, OpenGL ve ALSA kütüphanelerini kur.
apt install -y \
  xserver-xorg-core \
  xserver-xorg-input-libinput \
  xinit xauth \
  libgl1-mesa-dri libgl1 \
  libasound2t64
```

Test için Xterm:

```bash
apt install -y xterm  # X11 ortamını test etmek için xterm terminalini kur.
```

### X11 testi

SL500'ün fiziksel Linux konsolunda:

```bash
startx /usr/bin/xterm -- :0  # X sunucusunu :0 ekranında başlat ve xterm aç.
```

Kontrol ettiklerimiz:

- X11 grafik ekranı açılıyor.
- Xterm penceresi görünüyor.
- Fare imleci çalışıyor.
- Klavye çalışıyor.
- Sistem kilitlenmiyor.

**SL500'de test başarılıydı.**

Xterm'den çıkmak için:

```bash
exit  # xterm oturumunu sonlandır; X oturumu da kapanır.
```

### Bu yaklaşım neden önemli?

Amiberry'nin ihtiyacı olan grafik ortamını sağladık fakat normal bir Linux masaüstünün ek işlemci ve bellek yükünü eklemedik.

---

## 6. Ses kartı işlemleri — ALSA

SL500 kurulumunda yaşadığımız önemli sorunlardan biri sesin kapalı olmasıydı.

Çözüm, ALSA mikserindeki sessize alınmış kanalları açmaktı.

### ALSA araçlarını kur

```bash
apt install -y alsa-utils  # alsamixer, aplay, speaker-test ve alsactl araçlarını kur.
```

Ses kartlarını listele:

```bash
aplay -l  # ALSA tarafından tanınan ses oynatma aygıtlarını listeler.
```

```bash
cat /proc/asound/cards  # Çekirdeğin tanıdığı ALSA ses kartlarını göster.
```

### Alsamixer

```bash
alsamixer  # Etkileşimli ALSA ses mikserini aç.
```

Klavye kontrolleri:

| Tuş | İşlem |
| --- | --- |
| F6 | Ses kartı seç |
| Sol / Sağ | Ses kanalını değiştir |
| Yukarı / Aşağı | Ses seviyesini ayarla |
| M | Mute aç / kapat |
| Esc | Çık |

Özellikle şu kanalları kontrol ettik:

- Master
- PCM
- Speaker
- Headphone veya Front (varsa)

`MM` görüyorsan kanal sessizdir. `M` tuşuyla aç.

Ses seviyesini başlangıç için yaklaşık **%80** yap.

### Ses testi

```bash
speaker-test -c 2 -t wav  # İki kanallı WAV örneğiyle ses çıkışını test et.
```

Testi durdurmak için `Ctrl+C`.

### Ses ayarlarını kalıcı kaydet

```bash
alsactl store  # Mevcut ALSA mikser ayarlarını kalıcı olarak kaydet.
```

**Doğrulanmış sonuç:** SL500'de mute kaldırıldıktan sonra Amiberry'den ses alındı.

> Yeniden kurulumda ses yoksa önce ALSA mikserini kontrol et. PipeWire, PulseAudio veya başka bir ses altyapısı kurarak başlamaya gerek yok.

---

## 7. Amiberry 8.4.2 kurulumu

SL500 üzerinde başarıyla kullandığımız yöntem **resmî Amiberry APT deposuydu**.

Derleme yapmadık.

### Gerekli araçlar

```bash
apt install -y curl ca-certificates  # HTTPS üzerinden depo kurulum betiğini indirmek için gereken araçları kur.
```

### Resmî depoyu ekleme

Başarılı kurulumda kullandığımız komut:

```bash
curl -fsSL https://packages.amiberry.com/install.sh | sh  # Resmî Amiberry depo kurulum betiğini indirip çalıştır.
```

Sonraki bir yeniden kurulumda betiği önce indirip incelemek daha güvenlidir:

```bash
# Resmî kurulum betiğini /tmp altına indir; henüz çalıştırma.
curl -fsSL https://packages.amiberry.com/install.sh \
  -o /tmp/amiberry-install.sh
```

İçeriğini kontrol ettikten sonra:

```bash
sh /tmp/amiberry-install.sh  # İndirilen Amiberry depo kurulum betiğini çalıştır.
```

### Paket listesini güncelle

```bash
apt update  # Yeni eklenen depo dahil tüm paket listelerini yenile.
```

Paketi kontrol et:

```bash
apt policy amiberry  # Amiberry paketinin aday sürümünü ve depo kaynağını göster.
```

### Amiberry kurulumu

```bash
apt install -y amiberry  # Amiberry paketini kur.
```

### Kurulumu doğrulama

```bash
command -v amiberry  # Amiberry çalıştırılabilir dosyasının yolunu göster.
```

Beklenen:

```text
/usr/bin/amiberry
```

Sürüm:

```bash
amiberry --version  # Kurulu Amiberry sürümünü göster.
```

Başarılı SL500 kurulumundaki sürüm:

**Amiberry 8.4.2**

Not: Gelecekte depo daha yeni bir sürüm sunabilir. Tam aynı sürümü yeniden kurmak için 8.4.2 paketinin depoda veya arşivde bulunması gerekir.

---

## 8. Amiberry'yi başlatma

SL500'deki başarılı ve basit başlatma komutumuz:

```bash
startx /usr/bin/amiberry -- :0  # X11 ekranı :0 üzerinde Amiberry'yi doğrudan başlat.
```

Bunu bilgisayarın fiziksel terminalinden çalıştırdık.

**Sonuç:** Amiberry grafik arayüzü açıldı ve kullanılabildi.

### `amb` kısayolunu oluşturma

```bash
cat > /usr/local/bin/amb <<'EOF'
#!/bin/bash
# Amiberry'yi minimal X11 ortamında başlat.
exec startx /usr/bin/amiberry -- :0
EOF
```

Çalıştırılabilir yap:

```bash
chmod +x /usr/local/bin/amb  # amb betiğine çalıştırma izni ver.
```

Artık fiziksel terminalde:

```bash
amb  # Amiberry'yi kısa komutla başlat.
```

yazmak yeterli.

### ROM ve yapılandırmalar

Kurulumda Amiberry'nin ROM dizinini arayüzde doğru göstermemiz gerekir.

Olası dizinleri kontrol et:

```bash
# /usr/share, /root ve /home altında Amiberry, roms ve kickstarts dizinlerini ara.
find /usr/share /root /home \
  -type d \
  \( -name roms -o -name kickstarts -o -iname '*amiberry*' \) \
  2>/dev/null
```

Kickstart dosyalarını yalnızca kullanım hakkına sahip olduğun kaynaklardan ekle.

A500 ve A1200 yapılandırmalarını ayrı ayrı oluşturup test et.

---

## 9. Performans işlemleri

Burada SL500 kurulumu için en önemli ölçümlü sonucu elde ettik.

### 9.1 Gereksiz masaüstü yükünü kaldırma

Kurulumda zaten tam masaüstü yüklemediğimiz için Amiberry:

```text
Debian konsol
    ↓
Minimal X11
    ↓
Amiberry
```

üzerinden çalışıyordu.

Bu, tercih ettiğimiz hafif yapıydı.

### 9.2 CPU kullanımını ölçme

```bash
apt install -y htop  # Etkileşimli CPU ve RAM izleme aracını kur.
```

```bash
htop  # İşlemci, RAM ve çalışan süreçleri canlı izle.
```

Veya Amiberry çalışırken SSH üzerinden:

```bash
top -p $(pgrep -o amiberry)  # En eski Amiberry sürecinin CPU/RAM kullanımını izle.
```

Burada CPU ve RAM kullanımını izledik.

### 9.3 Cycle-exact ayarı

**SL500'de doğruladığımız en önemli Amiberry performans değişikliği buydu.**

Amiberry'nin CPU / chipset ile ilgili ayarlarında:

```text
Cycle-exact: OFF
```

Yaptığımız ölçümlerde yaklaşık:

| Ölçüm | Önce | Sonra |
| --- | ---: | ---: |
| Amiberry ana işlem CPU kullanımı | %89,4 | %73,5 |
| RAM kullanımı | 333 MB | 349 MB |
| Ses ve görüntü | Test edildi | Akıcı ve düzgün |

**Sonuç:** Cycle-exact kapatıldığında ölçülen CPU kullanımı yaklaşık **15,9 yüzde puan** azaldı.

Bu değerler aynı test ortamına aittir; tüm oyunlar ve yapılandırmalar için garanti edilen performans değerleri değildir.

### 9.4 Cycle-exact neden kapalı?

Cycle-exact, Amiga donanımının zamanlamasını daha hassas taklit etmeye çalışır.

Bu doğruluk ekstra işlem yükü oluşturabilir.

Bizim kullanım hedefimiz:

- Oyunları akıcı çalıştırmak
- Düşük CPU yükü
- Düzgün ses
- Düzgün görüntü

olduğu için SL500 üzerinde kapatmak iyi sonuç verdi.

Ancak çok hassas donanım zamanlaması isteyen oyun veya demolarda tekrar açmak gerekebilir.

### 9.5 Yapmadığımız veya doğrulamadığımız işlemler

SL500 için başarılı sonucun parçası olarak belgeleyebildiğim işlemler dışında şunları gerekliymiş gibi göstermiyorum:

- CPU overclock
- Özel kernel derleme
- NVIDIA proprietary sürücü
- Rastgele kernel parametreleri
- Özel SDL2 derleme
- Gereksiz servisleri toplu kapatma
- Agresif swap, scheduler veya governor ayarları

Bunlar bu referans kurulumun doğrulanmış performans reçetesi değildir.

---

## 10. Otomatik root konsol girişi

SL500'de fiziksel ekran açıldığında kullanıcı adı ve şifre sormadan Amiberry'yi başlatacak bir düzen kurduk.

Bu düzen **yalnızca tty1 fiziksel konsolu** için tasarlandı.

Önemli güvenlik notu: Root otomatik girişi, bilgisayarın başına geçen birine tam sistem erişimi verir. Bunu yalnızca güvenilir ortamda kullanılan, özel amaçlı emülasyon cihazında değerlendir.

### Getty override dizini

```bash
mkdir -p /etc/systemd/system/getty@tty1.service.d  # tty1 için systemd override dizinini oluştur.
```

Dosyayı oluştur:

```bash
nano /etc/systemd/system/getty@tty1.service.d/autologin.conf  # tty1 otomatik giriş ayarını düzenle.
```

İçerik:

```ini
# systemd getty servis ayarları.
[Service]

# Varsayılan ExecStart satırını temizle.
ExecStart=

# tty1 üzerinde root kullanıcısını otomatik oturum açtır.
ExecStart=-/sbin/agetty --autologin root --noclear %I $TERM
```

Systemd yapılandırmasını yenile:

```bash
systemctl daemon-reload  # systemd servis tanımlarını yeniden yükle.
```

Bir sonraki açılışta fiziksel `tty1` root olarak otomatik giriş yapar.

---

## 11. AMIGA CONTROL CENTER

Başarılı SL500 kurulumunun son aşamasında basit bir terminal kontrol menüsü oluşturduk.

İki temel işlem:

```text
1 - START AMIBERRY
2 - SYSTEM INFORMATION
```

Menünün kendisi normal Linux terminalini kullanıyordu. Ayrı bir masaüstü uygulaması kurmadık.

### 11.1 Menü komutu

```bash
nano /usr/local/bin/menu  # AMIGA CONTROL CENTER menü betiğini oluştur/düzenle.
```

Aşağıdaki içerik, önceki menünün yeniden kurulabilir bir sürümüdür:

```bash
#!/bin/bash
# Basit Amiga kontrol menüsü.

clear  # Terminal ekranını temizle.
echo "========================================"  # Üst ayırıcı çizgiyi yazdır.
echo "          AMIGA CONTROL CENTER"          # Menü başlığını yazdır.
echo "========================================"  # Alt ayırıcı çizgiyi yazdır.
echo                                              # Boş satır yazdır.
echo "  1 - START AMIBERRY"                        # Amiberry başlatma seçeneğini göster.
echo "  2 - SYSTEM INFORMATION"                    # Sistem bilgisi seçeneğini göster.
echo                                              # Boş satır yazdır.
echo "========================================"  # Alt ayırıcı çizgiyi yazdır.
echo "  Komut girmek icin 1 veya 2 yaz."           # Kullanım açıklamasını yazdır.
echo                                              # Boş satır yazdır.
```

İzin ver:

```bash
chmod +x /usr/local/bin/menu  # Menü betiğine çalıştırma izni ver.
```

### 11.2 `1` komutu — Amiberry başlatma

```bash
nano /usr/local/bin/1  # 1 komutunun betiğini oluştur/düzenle.
```

İçerik:

```bash
#!/bin/bash
# Amiberry'yi yalnızca fiziksel tty1 konsolundan başlat.

if [[ "$(tty)" != "/dev/tty1" ]]; then  # Aktif terminal tty1 değilse devam etme.
    echo "Amiberry fiziksel tty1 konsolundan baslatilmali."  # Uyarı mesajını göster.
    exit 1  # Betiği hata koduyla sonlandır.
fi

/usr/local/bin/amb   # Amiberry'yi başlat.
/usr/local/bin/menu  # Amiberry kapanınca kontrol menüsünü yeniden göster.
```

İzin:

```bash
chmod +x /usr/local/bin/1  # 1 betiğine çalıştırma izni ver.
```

### 11.3 `2` komutu — Sistem bilgileri

```bash
nano /usr/local/bin/2  # 2 komutunun sistem bilgisi betiğini oluştur/düzenle.
```

İçerik:

```bash
#!/bin/bash
# Temel sistem bilgilerini terminalde göster.

echo  # Boş satır yazdır.
echo "========================================"  # Üst ayırıcı çizgiyi yazdır.
echo "          SYSTEM INFORMATION"             # Başlığı yazdır.
echo "========================================"  # Alt ayırıcı çizgiyi yazdır.

echo  # Boş satır yazdır.
echo "CPU:"  # CPU başlığını yazdır.
lscpu | grep -E 'Model name|CPU\(s\):'  # İşlemci modeli ve CPU sayısını göster.

echo  # Boş satır yazdır.
echo "MEMORY:"  # Bellek başlığını yazdır.
free -h  # RAM ve swap kullanımını göster.

echo  # Boş satır yazdır.
echo "DISK:"  # Disk başlığını yazdır.
df -h /  # Kök dosya sisteminin disk kullanımını göster.

echo  # Boş satır yazdır.
echo "NETWORK:"  # Ağ başlığını yazdır.
ip -4 -br addr  # IPv4 adreslerini kısa biçimde göster.

echo  # Boş satır yazdır.
echo "KERNEL:"  # Kernel başlığını yazdır.
uname -r  # Çalışan Linux çekirdeği sürümünü göster.

echo  # Son boş satırı yazdır.
```

İzin:

```bash
chmod +x /usr/local/bin/2  # 2 betiğine çalıştırma izni ver.
```

Menü testi:

```bash
menu  # AMIGA CONTROL CENTER menüsünü göster.
```

Ardından:

```text
1
```

veya:

```text
2
```

komutları kullanılabilir.

---

## 12. Açılışta Amiberry'nin otomatik başlaması

Bu, SL500'deki son başarılı açılış davranışımızdır.

Hedef:

```text
Power ON
    |
    v
Debian başlıyor
    |
    v
tty1 root otomatik giriş
    |
    v
Amiberry otomatik açılıyor
    |
    v
Amiga kullanımı
    |
    v
Amiberry QUIT
    |
    v
AMIGA CONTROL CENTER
```

### Root başlangıç dosyası

```bash
nano /root/.bash_profile  # Root kullanıcısının oturum açılış betiğini düzenle.
```

Aşağıdaki bloğu dosyaya ekle:

```bash
# AMIGA startup - physical tty1 only

# SSH oturumu değilse ve fiziksel tty1 kullanılıyorsa otomatik başlat.
if [[ -z "$SSH_CONNECTION" && "$(tty)" == "/dev/tty1" ]]; then
    /usr/local/bin/amb   # Amiberry'yi otomatik başlat.
    /usr/local/bin/menu  # Amiberry kapanınca kontrol menüsünü göster.
fi
```

**Bu koşul çok önemli.**

`SSH_CONNECTION` kontrolü sayesinde SSH ile bağlanan PuTTY oturumunda Amiberry açılmaya çalışmaz.

`tty1` kontrolü ise otomatik başlatmayı bilgisayarın fiziksel birinci konsoluyla sınırlar.

Bu yapılandırmayla:

- Bilgisayar açılınca Amiberry otomatik başlar.
- Amiberry'den Quit ile çıkınca kontrol menüsü görünür.
- `1` yazınca yeniden Amiberry açılır.
- `2` yazınca sistem bilgileri gösterilir.
- PuTTY normal Linux terminali olarak çalışmaya devam eder.

---

## 13. Yeniden kurulum sonrası test listesi

Kurulumun bittiğini söylemeden önce sırayla bunları doğrula:

- [ ] Debian 13 minimal açılıyor.
- [ ] SSH üzerinden bağlanılıyor.
- [ ] Ethernet `192.168.1.30` ile çalışıyor.
- [ ] Wi-Fi `192.168.1.31` ile çalışıyor.
- [ ] NVIDIA G 105M, Nouveau ile tanınıyor.
- [ ] Dahili ekran 1024 × 768 çalışıyor.
- [ ] Xterm ile X11, klavye ve fare testi başarılı.
- [ ] `amiberry --version` beklenen sürümü gösteriyor.
- [ ] `amb` ile Amiberry açılıyor.
- [ ] Kickstart ROM'ları tanınıyor.
- [ ] A500 oyunu çalışıyor.
- [ ] A1200 yapılandırması çalışıyor.
- [ ] ALSA mute kaldırıldı ve ses geliyor.
- [ ] Cycle-exact kapalıyken ses ve görüntü akıcı.
- [ ] Amiberry'den çıkınca kontrol menüsü geliyor.
- [ ] Yeniden başlatmada Amiberry otomatik açılıyor.
- [ ] SSH oturumu otomatik başlatmadan etkilenmiyor.

---

## 14. Sorun giderme

| Sorun | İlk kontrol |
| --- | --- |
| Amiberry açılmıyor | Fiziksel konsolda `amb` |
| X11 açılmıyor | `startx /usr/bin/xterm -- :0` |
| Ekran çözünürlüğü yanlış | GRUB `video=LVDS-1:1024x768@60` |
| Ses yok | `alsamixer`, Master / PCM mute |
| Ses yeniden kapanıyor | `alsactl store` |
| CPU kullanımı yüksek | `htop`, Cycle-exact |
| SSH bağlantısı yok | `systemctl status ssh` |
| Ethernet IP yanlış | `ip -4 addr show enp12s0` |
| Wi-Fi IP yanlış | `ip -4 addr show wlp3s0` |
| FileZilla bağlanmıyor | IP, port 22, hesap yetkisi |
| Boot'ta Amiberry açılmıyor | tty1 autologin ve `/root/.bash_profile` |
| Amiberry kapanınca menü yok | `/usr/local/bin/menu` ve başlangıç dosyası |

---

## 15. Sonuç — SL500 referans yapılandırması

**İşletim sistemi:** Debian 13 minimal  
**Grafik sürücüsü:** Nouveau  
**Grafik altyapısı:** Minimal X11  
**Amiberry:** 8.4.2  
**Ses:** ALSA / alsamixer  
**Ethernet:** `enp12s0` → `192.168.1.30`  
**Wi-Fi:** `wlp3s0` → `192.168.1.31`  
**Performans:** Cycle-exact kapalı  
**Başlatma komutu:** `amb`  
**Otomatik açılış:** tty1 + `.bash_profile`  
**Kontrol:** AMIGA CONTROL CENTER  
**Uzaktan erişim:** OpenSSH + SFTP

### Referans kurulumun temel ilkeleri

1. Gerekli olmayan masaüstü bileşenlerini kurma.
2. Debian'ın çalışan standart sürücülerini tercih et.
3. Önce X11, ses ve ağ altyapısını birbirinden bağımsız test et.
4. Amiberry'yi resmî paket deposundan kur.
5. Performans ayarlarını ölçerek değiştir.
6. Önce manuel başlatmayı doğrula, otomasyonu en son ekle.
7. Her başarılı aşamadan sonra yeni bir sorun yaratabilecek gereksiz değişiklikler yapma.

**Bu rehber yalnızca Lenovo ThinkPad SL500 üzerinde başarıyla kullanılan referans kurulumu belgeler.**
