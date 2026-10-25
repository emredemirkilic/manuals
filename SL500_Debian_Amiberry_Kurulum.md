# LENOVO THINKPAD SL500

## Debian 13 Minimal + Amiberry 8.4.2 Kurulum Rehberi

**Sürüm:** 1.0 — 25 Ekim 2026\
**Referans:** Başarılı Lenovo SL500 kurulumu\
**Amaç:** Masaüstü ortamı olmadan, doğrudan Amiberry çalıştıran bağımsız bir Amiga emülasyon sistemi.

---

## 1. Donanım ve başarılı kurulumun özeti

| Bileşen         | Lenovo SL500                         |
| --------------- | ------------------------------------ |
| İşlemci         | Intel Core 2 Duo P8600, 2.40 GHz     |
| RAM             | 4 GB                                 |
| Depolama        | SSD                                  |
| Ekran kartı     | NVIDIA GeForce G 105M                |
| Grafik sürücüsü | Nouveau                              |
| İşletim sistemi | Debian 13 Trixie, amd64              |
| Grafik sistemi  | Minimal X11                          |
| Emülatör        | Amiberry 8.4.2                       |
| Ses             | ALSA                                 |
| Uzaktan yönetim | SSH / PuTTY                          |
| Dosya aktarımı  | SFTP / FileZilla                     |
| Başlatma        | Fiziksel konsoldan otomatik Amiberry |

**Başarılı son durum:** Amiberry 8.4.2 açılıyor, A500 ve A1200 yapılandırmaları çalışıyor, görüntü ve ses düzgün, oyunlar akıcı, emülatörden çıkınca AMIGA CONTROL CENTER menüsü kullanılabiliyor.

### Temel mimari

```
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
su -             # Root hesabına geç; sonraki sistem komutlarını yönetici yetkisiyle çalıştır.
apt update      # APT paket listelerini depolardan yenile.
apt upgrade -y  # Kurulu paketleri güncelle; onay sorularını otomatik kabul et.
```

Temel yönetim araçları:

```bash
# sudo: yetki yükseltme; nano/vim: editör; less: metin görüntüleyici.
# bash-completion: komut tamamlama; curl/wget: indirme; ca-certificates: HTTPS sertifikaları.
# htop: kaynak izleme; pciutils/usbutils: PCI ve USB donanım bilgileri.
apt install -y \
  sudo nano vim less \
  bash-completion curl wget \
  ca-certificates htop \
  pciutils usbutils
```

**Kontrol:**

```bash
cat /etc/os-release  # Debian dağıtımını ve sürümünü göster.
uname -m             # İşlemci/sistem mimarisini göster (beklenen: x86_64).
free -h              # RAM ve takas alanını okunabilir birimlerle göster.
lsblk                # Diskleri ve bölümlerini listeler.
```

Beklenen mimari `x86_64` olmalıdır.

---

## 3. Ağ, SSH ve uzaktan yönetim

SL500'de SSH üzerinden PuTTY ile bağlantı kurduk.

### OpenSSH kurulumu

```bash
apt install -y openssh-server  # SSH sunucusunu kur; PuTTY/SFTP bağlantısını mümkün kıl.
```

Servisi etkinleştir:

```bash
systemctl enable --now ssh  # SSH'yi hemen başlat ve sonraki açılışlarda otomatik çalıştır.
```

Kontrol:

```bash
systemctl status ssh --no-pager  # SSH servisinin çalışma durumunu sayfalayıcı açmadan göster.
```

Başarılı sonuç:

```
Active: active (running)
```

IP adresini öğren:

```bash
hostname -I  # Makinenin atanmış IP adreslerini listeler.
```

veya:

```bash
ip -4 addr  # Ağ arayüzleri üzerindeki IPv4 adreslerini ayrıntılı gösterir.
```

Önceki SL500 kurulumunda kullanılan IP adresleri:

- Ethernet: `192.168.1.30`
- Wi-Fi: `192.168.1.31`

Bunlar o kurulumda görülen adreslerdir; yeni kurulumda DHCP nedeniyle değişebilir.

### PuTTY ile bağlanma

PuTTY'de:

```
Host: SL500 IP adresi
Port: 22
Connection type: SSH
```

İlk bağlantıyı normal kullanıcı hesabıyla test et.

### İsteğe bağlı: root SSH erişimi

Önceki SL500 kurulumunda root SSH erişimini de açmıştık. Bu, FileZilla üzerinden sistem dizinlerine erişmeyi kolaylaştırdı.

Ancak **root hesabıyla şifreli SSH girişi güvenlik riskini artırır**. Tercih edilen yöntem normal kullanıcıyla SFTP kullanmak ve gerektiğinde `sudo` ile yönetim yapmaktır.

Yine de önceki davranışı birebir oluşturmak istiyorsan, yalnızca güvenilir yerel ağda ve internete 22 numaralı portu açmadan:

```bash
nano /etc/ssh/sshd_config.d/00-root-login.conf  # Root SSH ayarları için ayrı yapılandırma dosyasını aç.
```

İçerik:

```text
# Root hesabının SSH ile girişine izin ver.
PermitRootLogin yes
# SSH üzerinden parola ile kimlik doğrulamaya izin ver.
PasswordAuthentication yes
```

Yapılandırmayı doğrula:

```bash
sshd -t  # SSH yapılandırmasının sözdizimini kontrol et; doğruysa çıktı vermez.
sshd -T | grep -E 'permitrootlogin|passwordauthentication'  # Etkin root/parola ayarlarını göster.
```

Servis yeniden yüklemesini ancak hata olmadığını gördükten sonra yap:

```bash
systemctl reload ssh  # Çalışan SSH servisine yeni yapılandırmayı uygula.
```

Yeni root bağlantısını ayrı bir PuTTY penceresinde doğrulamadan mevcut SSH oturumunu kapatma.

### FileZilla

```
Protocol: SFTP
Host: SL500 IP adresi
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
lspci | grep -Ei 'vga|3d|display'  # PCI aygıtlarında grafik/görüntü denetleyicilerini bul.
```

Nouveau modülünü kontrol et:

```bash
lsmod | grep nouveau  # Nouveau çekirdek sürücüsünün yüklenip yüklenmediğini göster.
```

DRM aygıtları:

```bash
ls -l /dev/dri/  # Ekran kartının DRM aygıt düğümlerini listeler.
```

Bizim kurulumumuzda Nouveau düzgün çalıştı. NVIDIA'nın eski proprietary sürücüsünü ayrıca yüklemedik.

### Dahili ekran çözünürlüğü

SL500'de başarılı şekilde kullandığımız çözünürlük:

**1024 × 768 @ 60 Hz**

Dahili panel için `LVDS-1` çıkışını kullandık.

GRUB dosyasını düzenle:

```bash
nano /etc/default/grub  # GRUB açılış parametrelerinin tanımlandığı dosyayı düzenle.
```

`GRUB_CMDLINE_LINUX_DEFAULT` satırındaki mevcut parametreleri koruyarak şu parametreyi ekle:

```
video=LVDS-1:1024x768@60
```

Örneğin:

```text
# Çekirdeği dahili LVDS-1 ekranında 1024x768 ve 60 Hz kullanacak biçimde başlatır.
GRUB_CMDLINE_LINUX_DEFAULT="quiet video=LVDS-1:1024x768@60"
```

Kaydet ve uygula:

```bash
update-grub  # Değişen GRUB parametrelerinden önyükleme yapılandırmasını yeniden üret.
reboot       # Yeni ekran parametresinin uygulanması için bilgisayarı yeniden başlat.
```

**Doğrulanmış sonuç:** Yeniden başlatma sonrasında SL500'de 1024 × 768 çözünürlük elde edildi.

Not: Bu parametre SL500'de doğrulanmıştır. Farklı ekran çıkışı veya farklı bilgisayar için aynı `LVDS-1` adını varsaymamak gerekir.

---

## 5. Minimal X11 kurulumu

Tam masaüstü kurmadan Amiberry'yi çalıştırmamızın temel adımıydı.

Kullandığımız çekirdek paketler:

```bash
# xserver-xorg-core: X11 sunucusu; xserver-xorg-input-libinput: klavye/fare girişi.
# xinit: X11 başlatma; xauth: X11 oturum yetkilendirmesi.
# libgl1-mesa-dri ve libgl1: grafik/OpenGL kütüphaneleri; libasound2t64: ALSA ses kütüphanesi.
apt install -y \
  xserver-xorg-core \
  xserver-xorg-input-libinput \
  xinit xauth \
  libgl1-mesa-dri libgl1 \
  libasound2t64
```

Test için Xterm:

```bash
apt install -y xterm  # X11'in açıldığını sınamak için basit terminal penceresi kur.
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
exit  # Xterm kabuğundan çıkar; test penceresini ve X oturumunu sonlandırır.
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
aplay -l  # ALSA tarafından bulunan ses çalma aygıtlarını listeler.
```

```bash
cat /proc/asound/cards  # Çekirdeğin tanıdığı ALSA ses kartlarını göster.
```

### Alsamixer

```bash
alsamixer  # Ses kanallarının seviyesini ve mute durumunu etkileşimli değiştir.
```

Klavye kontrolleri:

| Tuş            | İşlem                 |
| -------------- | --------------------- |
| F6             | Ses kartı seç         |
| Sol / Sağ      | Ses kanalını değiştir |
| Yukarı / Aşağı | Ses seviyesini ayarla |
| M              | Mute aç / kapat       |
| Esc            | Çık                   |

Özellikle şu kanalları kontrol ettik:

- Master
- PCM
- Speaker
- Headphone veya Front (varsa)

`MM` görüyorsan kanal sessizdir. `M` tuşuyla aç.

Ses seviyesini başlangıç için yaklaşık **%80** yap.

### Ses testi

```bash
speaker-test -c 2 -t wav  # İki kanallı (stereo) WAV örnekleriyle ses çıkışını test et.
```

Testi durdurmak için `Ctrl+C`.

### Ses ayarlarını kalıcı kaydet

```bash
alsactl store  # Mevcut ALSA seviye/mute ayarlarını sonraki açılışlar için kaydet.
```

**Doğrulanmış sonuç:** SL500'de mute kaldırıldıktan sonra Amiberry'den ses alındı.

> Yeniden kurulumda ses yoksa önce ALSA mikserini kontrol et. PipeWire, PulseAudio veya başka bir ses altyapısı kurarak başlamaya gerek yok.

---

## 7. Amiberry 8.4.2 kurulumu

SL500 üzerinde başarıyla kullandığımız yöntem **resmî Amiberry APT deposuydu**.

Derleme yapmadık.

### Gerekli araçlar

```bash
apt install -y curl ca-certificates  # HTTPS üzerinden depo betiğini indirmek için araçları kur.
```

### Resmî depoyu ekleme

Başarılı kurulumda kullandığımız komut:

```bash
curl -fsSL https://packages.amiberry.com/install.sh | sh  # Resmî depo kurulum betiğini indirip kabukta çalıştırır.
```

Sonraki bir yeniden kurulumda betiği önce indirip incelemek daha güvenlidir:

```bash
# Resmî depo betiğini yürütmeden önce /tmp içine indir.
curl -fsSL https://packages.amiberry.com/install.sh \
  -o /tmp/amiberry-install.sh
```

İçeriğini kontrol ettikten sonra:

```bash
sh /tmp/amiberry-install.sh  # İndirilen Amiberry APT depo kurulum betiğini çalıştır.
```

### Paket listesini güncelle

```bash
apt update  # Yeni eklenen Amiberry deposundaki paket listesini al.
```

Paketi kontrol et:

```bash
apt policy amiberry  # Amiberry'nin aday sürümünü ve hangi depodan geldiğini göster.
```

### Amiberry kurulumu

```bash
apt install -y amiberry  # Resmî depodaki Amiberry paketini kur.
```

### Kurulumu doğrulama

```bash
command -v amiberry  # Çalıştırılabilir Amiberry dosyasının yolunu göster.
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
startx /usr/bin/amiberry -- :0  # X11 ekranı :0 üzerinde Amiberry'yi başlat.
```

Bunu bilgisayarın fiziksel terminalinden çalıştırdık.

**Sonuç:** Amiberry grafik arayüzü açıldı ve kullanılabildi.

### `amb` kısayolunu oluşturma

```bash
cat > /usr/local/bin/amb <<'EOF'  # /usr/local/bin/amb adlı başlatıcı betiği oluştur.
#!/bin/bash
# Betiği Bash ile çalıştır (shebang ilk satırda kalmalıdır).
exec startx /usr/bin/amiberry -- :0  # Kabuk sürecini X11 üzerinde çalışan Amiberry ile değiştir.
EOF
```

Çalıştırılabilir yap:

```bash
chmod +x /usr/local/bin/amb  # 'amb' dosyasına çalıştırma izni ver.
```

Artık fiziksel terminalde:

```bash
amb  # Oluşturulan kısayol ile Amiberry'yi başlat.
```

yazmak yeterli.

### ROM ve yapılandırmalar

Kurulumda Amiberry'nin ROM dizinini arayüzde doğru göstermemiz gerekir.

Olası dizinleri kontrol et:

```bash
# /usr/share, /root ve /home altında ROM/kickstart/Amiberry klasörlerini ara.
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

```
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
apt install -y htop  # İşlemci ve RAM kullanımını etkileşimli izleme aracını kur.
```

```bash
htop  # İşlem bazında CPU ve bellek kullanımını göster.
```

Veya Amiberry çalışırken SSH üzerinden:

```bash
top -p $(pgrep -o amiberry)  # İlk Amiberry sürecinin PID'sini bul ve top ile izle.
```

Burada CPU ve RAM kullanımını izledik.

### 9.3 Cycle-exact ayarı

**SL500'de doğruladığımız en önemli Amiberry performans değişikliği buydu.**

Amiberry'nin CPU / chipset ile ilgili ayarlarında:

```
Cycle-exact: OFF
```

Yaptığımız ölçümlerde yaklaşık:

| Ölçüm                            |        Önce |           Sonra |
| -------------------------------- | ----------: | --------------: |
| Amiberry ana işlem CPU kullanımı |       %89,4 |           %73,5 |
| RAM kullanımı                    |      333 MB |          349 MB |
| Ses ve görüntü                   | Test edildi | Akıcı ve düzgün |

\<ChartNotePlaceholder/>

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
mkdir -p /etc/systemd/system/getty@tty1.service.d  # tty1 servis ayarları için override dizinini oluştur.
```

Dosyayı oluştur:

```bash
nano /etc/systemd/system/getty@tty1.service.d/autologin.conf  # Otomatik giriş yapılandırmasını düzenle.
```

İçerik:

```ini
[Service]
# Önce varsayılan ExecStart değerini temizle.
ExecStart=
# tty1'de root hesabını parola sormadan açan agetty komutunu tanımla.
ExecStart=-/sbin/agetty --autologin root --noclear %I $TERM
```

Systemd yapılandırmasını yenile:

```bash
systemctl daemon-reload  # systemd'nin yeni tty1 servis override dosyasını okumasını sağla.
```

Bir sonraki açılışta fiziksel `tty1` root olarak otomatik giriş yapar.

---

## 11. AMIGA CONTROL CENTER

Başarılı SL500 kurulumunun son aşamasında basit bir terminal kontrol menüsü oluşturduk.

İki temel işlem:

```
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
# Bu dosya Bash ile çalıştırılan basit terminal menüsüdür.

clear  # Terminal ekranını temizle.
echo "========================================"  # Üst ayırıcı çizgisini yazdır.
echo "          AMIGA CONTROL CENTER"  # Menü başlığını yazdır.
echo "========================================"  # Başlığın alt çizgisini yazdır.
echo  # Boş satır bırak.
echo "  1 - START AMIBERRY"  # 1 komutunun Amiberry'yi başlattığını göster.
echo "  2 - SYSTEM INFORMATION"  # 2 komutunun sistem bilgilerini gösterdiğini belirt.
echo  # Boş satır bırak.
echo "========================================"  # Alt ayırıcı çizgisini yazdır.
echo "  Komut girmek icin 1 veya 2 yaz."  # Kullanıcıya komut seçimini açıkla.
echo  # Son boş satırı bırak.
```

İzin ver:

```bash
chmod +x /usr/local/bin/menu  # Menü betiğini çalıştırılabilir yap.
```

### 11.2 `1` komutu — Amiberry başlatma

```bash
nano /usr/local/bin/1  # '1' yazınca çalışacak Amiberry başlatıcı betiğini düzenle.
```

İçerik:

```bash
#!/bin/bash
# Bu betik yalnızca fiziksel tty1 konsolundan Amiberry başlatır.

if [[ "$(tty)" != "/dev/tty1" ]]; then  # Oturumun gerçek tty1 olup olmadığını kontrol et.
    echo "Amiberry fiziksel tty1 konsolundan baslatilmali."  # Yanlış terminalde uyarı göster.
    exit 1  # Hatalı kullanımda betiği sonlandır.
fi  # Konsol denetimini bitir.

/usr/local/bin/amb   # Amiberry'yi X11 üzerinde başlat.
/usr/local/bin/menu  # Amiberry'den çıkılınca menüyü tekrar göster.
```

İzin:

```bash
chmod +x /usr/local/bin/1  # '1' komutunu çalıştırılabilir yap.
```

### 11.3 `2` komutu — Sistem bilgileri

```bash
nano /usr/local/bin/2  # '2' sistem bilgileri betiğini oluştur/düzenle.
```

İçerik:

```bash
#!/bin/bash
# Sistem donanımı, bellek, disk, ağ ve çekirdek bilgilerini yazdırır.

echo  # Boş satır.
echo "========================================"  # Başlık üst çizgisi.
echo "          SYSTEM INFORMATION"  # Bölüm başlığı.
echo "========================================"  # Başlık alt çizgisi.

echo  # Boş satır.
echo "CPU:"  # CPU bölüm etiketi.
lscpu | grep -E 'Model name|CPU\(s\):'  # İşlemci modeli ve mantıksal CPU sayısını göster.

echo  # Boş satır.
echo "MEMORY:"  # Bellek bölüm etiketi.
free -h  # RAM ve swap kullanımını insan-okunur biçimde göster.

echo  # Boş satır.
echo "DISK:"  # Disk bölüm etiketi.
df -h /  # Kök dosya sisteminin disk kullanımını göster.

echo  # Boş satır.
echo "NETWORK:"  # Ağ bölüm etiketi.
ip -4 -br addr  # IPv4 adreslerini arayüz başına kısa biçimde göster.

echo  # Boş satır.
echo "KERNEL:"  # Çekirdek bölüm etiketi.
uname -r  # Çalışan Linux çekirdeğinin sürümünü göster.

echo  # Son boş satır.
```

İzin:

```bash
chmod +x /usr/local/bin/2  # '2' komutunu çalıştırılabilir yap.
```

Menü testi:

```bash
menu  # AMIGA CONTROL CENTER başlığını ve seçenekleri göster.
```

Ardından:

```
1
```

veya:

```
2
```

komutları kullanılabilir.

---

## 12. Açılışta Amiberry'nin otomatik başlaması

Bu, SL500'deki son başarılı açılış davranışımızdır.

Hedef:

```
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
nano /root/.bash_profile  # Root giriş kabuğunun başlangıç komutlarını düzenle.
```

Aşağıdaki bloğu dosyaya ekle:

```bash
# AMIGA startup - physical tty1 only
# SSH oturumları hariç, yalnızca fiziksel tty1'de otomatik başlat.

if [[ -z "$SSH_CONNECTION" && "$(tty)" == "/dev/tty1" ]]; then  # SSH değilse ve konsol tty1 ise çalış.
    /usr/local/bin/amb   # X11 üzerinden Amiberry'yi aç.
    /usr/local/bin/menu  # Amiberry kapanınca kontrol menüsünü göster.
fi  # Otomatik açılış koşulunu bitir.
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
- [ ] Ethernet / Wi-Fi erişimi çalışıyor.
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

| Sorun                       | İlk kontrol                                |
| --------------------------- | ------------------------------------------ |
| Amiberry açılmıyor          | Fiziksel konsolda `amb`                    |
| X11 açılmıyor               | `startx /usr/bin/xterm -- :0`              |
| Ekran çözünürlüğü yanlış    | GRUB `video=LVDS-1:1024x768@60`            |
| Ses yok                     | `alsamixer`, Master / PCM mute             |
| Ses yeniden kapanıyor       | `alsactl store`                            |
| CPU kullanımı yüksek        | `htop`, Cycle-exact                        |
| SSH bağlantısı yok          | `systemctl status ssh`                     |
| FileZilla bağlanmıyor       | IP, port 22, hesap yetkisi                 |
| Boot'ta Amiberry açılmıyor  | tty1 autologin ve `/root/.bash_profile`    |
| Amiberry kapanınca menü yok | `/usr/local/bin/menu` ve başlangıç dosyası |

---

## 15. Sonuç — SL500 referans yapılandırması

**İşletim sistemi:** Debian 13 minimal\
**Grafik sürücüsü:** Nouveau\
**Grafik altyapısı:** Minimal X11\
**Amiberry:** 8.4.2\
**Ses:** ALSA / alsamixer\
**Performans:** Cycle-exact kapalı\
**Başlatma komutu:** `amb`\
**Otomatik açılış:** tty1 + `.bash_profile`\
**Kontrol:** AMIGA CONTROL CENTER\
**Uzaktan erişim:** OpenSSH + SFTP

### Referans kurulumun temel ilkeleri

1. Gerekli olmayan masaüstü bileşenlerini kurma.
2. Debian'ın çalışan standart sürücülerini tercih et.
3. Önce X11, ses ve ağ altyapısını birbirinden bağımsız test et.
4. Amiberry'yi resmî paket deposundan kur.
5. Performans ayarlarını ölçerek değiştir.
6. Önce manuel başlatmayı doğrula, otomasyonu en son ekle.
7. Her başarılı aşamadan sonra yeni bir sorun yaratabilecek gereksiz değişiklikler yapma.

**Bu rehberin referansı Lenovo SL500'dür. Q1900G2-P üzerinde denediğimiz KMSDRM, Amiberry Lite ve özel SDL2 kütüphanesi müdahaleleri bu başarılı kurulumun parçası değildir.**
