# Bilgisayar ve Yedek Donanım Envanteri

**Son güncelleme:** 26 Ekim 2026  
**Kapsam:** 7 bilgisayar/anakart modeli, toplam 10 cihaz; 26 adet fotoğraflanmış yedek parça.

> Bu envanter kişisel donanım stokunu izlemek içindir. `Doğrulanacak`, `?` ve `planlanan` ifadeleri bilinçli olarak kullanılmıştır. Model ailesinin teknik özellikleri, eldeki cihazda takılı olan parçalarla her zaman aynı değildir.

## 1. Bilgisayarlar

| Kısa ad | Marka ve model | Adet | İşlemci | Mevcut RAM | Mevcut depolama |
|---|---|---:|---|---|---|
| **SL500** | Lenovo ThinkPad SL500 | 1 | Intel işlemci; tam model doğrulanacak | 4 GB **DDR2** | SSD; kapasitesi doğrulanacak |
| **J1900** | Q1900G2-P V2.0 | 1 | Intel Celeron **J1900**, 4 çekirdek, 2,00 GHz | 4 GB DDR3 | 64 GB SSD |
| **Fujitsu** | Fujitsu Siemens FUTRO S500 / TCS-D2703 | 1 | AMD Mobile Sempron **2100+** (model bilgisi teyit edilecek) | 1 GB DDR2-667 | 4 GB CompactFlash |
| **HP** | HP t510 Thin Client | 1 | VIA Eden X2 **U4200**, 2 çekirdek, 1,00 GHz | Mevcut kapasite bilinmiyor | 1 GB Apacer IDE Flash |
| **Dell Wyse** | Wyse Cx0 ailesi (olası C90LE7) | **3** | VIA C7 / Eden sınıfı, yaklaşık 1,00 GHz; tam varyant teyit edilecek | Her birinde 2 GB DDR2 | IDE Flash DOM; kapasitesi bilinmiyor |
| **ASRock** | ASRock Industrial IMB-151 Rev. 1.00 | **2** | Intel Celeron **J1900** veya **N2930**; varyant teyit edilecek | DDR3L SO-DIMM; kapasitesi bilinmiyor | Doğrulanmadı |
| **Jetway** | Jetway NF9C-2800 | 1 | Intel Atom **N2800**, 2 çekirdek / 4 izlek, 1,86 GHz | RAM takılı değil | Disk takılı değil |
| **TOPLAM** | **7 model** | **10** | | | |

### Cihaz bazında notlar

- **SL500:** Intel işlemcinin tam kodu bilinmiyor. **4 GB DDR2** ve SSD mevcut. Lenovo'nun SL500 donanım belgesi DDR2-667 belleği doğruluyor; daha önce DDR3 diye kaydedilmiş olan bellek türü düzeltildi.
- **J1900:** Q1900G2-P V2.0, 4 GB DDR3 RAM ve 64 GB SSD ile kayıtlı.
- **Fujitsu:** S26361-K528-V101 ürün kodlu, 20 V / 3,25 A güç girişli. Takılı RAM 1 GB PC2-5300S, depolama 4 GB CF. CPU modeli açılışta kesinleştirilecek.
- **HP:** HSTNC-012-TC; 1 GB Apacer IDE Flash takılı. **4 GB DDR3 SO-DIMM** takılması planlanıyor. Elde **256 GB klasik 2,5 inç SATA SSD** ve bağlantı kablosu mevcut; montaj henüz doğrulanmadı. Planlanan depolama, mevcut 1 GB flash ile karıştırılmamalı.
- **Dell Wyse 1 / 2 / 3:** Üçünde de aynı konfigürasyon ve 2 GB DDR2 RAM olduğu kullanıcı tarafından belirtildi. Cx0 ailesi, C90LE7 alt modeli olası; flash kapasitesi henüz okunmadı.
- **ASRock 1 / 2:** İki IMB-151 kartı aynı konfigürasyonda. J1900/N2930 işlemci varyantı, RAM kapasitesi ve depolama durumu henüz kesinleşmedi.
- **Jetway:** Fotoğraflarda iki DDR3 SO-DIMM yuvası boş; SATA bağlantıları mevcut. Kart üzerinde depolama veya RAM takılı değil.

### Planlanan değişiklikler

| Cihaz | Parça | Plan | Durum |
|---|---|---|---|
| HP | 4 GB DDR3 SO-DIMM RAM | RAM yükseltmesi | Planlandı; takıldığı doğrulanmadı |
| HP | 256 GB 2,5 inç SATA SSD | SSD ve bağlantı kablosu elde mevcut | Planlandı; takıldığı doğrulanmadı |

### Model belgeleri

Bu bağlantılar **model ailesinin** özelliklerini doğrulamak içindir; cihazın içindeki parçaları kesinleştirmez.

- [Lenovo ThinkPad SL500 — donanım bakım kılavuzu](https://thinkpads.com/support/hmm/hmm_pdf/43y6762_03.pdf)
- [HP t510 — HP teknik özellikleri (PDF)](https://www.hp.com/hpinfo/newsroom/press_kits/2012/GPC/HP_t510_datasheet.pdf)
- [ASRock IMB-151 — üretici teknik sayfası](https://www.asrockind.com.cn/en-gb/IMB-151)
- [Jetway NF9C — üretici teknik sayfası](https://jetwayipc.com/products/nf9c-series/)

## 2. Bilgisayarlar Arası Karşılaştırma

### 2.1 İşlemci karşılaştırması

| Kısa ad | İşlemci / işlemci ailesi | Çekirdek / izlek | Temel saat | Kesinlik |
|---|---|---|---|---|
| SL500 | Intel Celeron / Core 2 ailesinden varyanta göre değişebilir | **?** | **?** | Tam CPU doğrulanmadı |
| J1900 | Intel Celeron J1900 | **4 / 4** | **2,00 GHz** | Kayıtlı model |
| Fujitsu | AMD Mobile Sempron 2100+ | **1 / 1** (beklenen) | Yaklaşık **1,00 GHz** | Açılışta doğrulanacak |
| HP | VIA Eden X2 U4200 | **2 / 2** | **1,00 GHz** | Model belgeleriyle doğrulandı |
| Dell Wyse ×3 | VIA C7 / Eden (Cx0 varyantı) | **1 / 1** (beklenen) | Yaklaşık **1,00 GHz** | Alt model doğrulanacak |
| ASRock ×2 | Intel Celeron J1900 **veya** N2930 | **4 / 4** | **2,00 veya 1,83 GHz** | Kartın CPU varyantı bilinmiyor |
| Jetway | Intel Atom N2800 | **2 / 4** | **1,86 GHz** | Anakart modelinden belirlendi |

**İşlemci karşılaştırmasına dair not:** GHz ve çekirdek sayısı tek başına gerçek işlemci performans sıralaması değildir. Özellikle SL500 ve ASRock modellerinde işlemci varyantı kesinleşmeden sayısal performans sıralaması yapılmadı. ASRock'un J1900 varyantı, J1900 adlı diğer bilgisayarla aynı işlemciyi kullanır.

### 2.2 Bellek ve depolama karşılaştırması

| Kısa ad | Adet | Mevcut RAM | Bellek türü | Mevcut depolama | Ek açıklama |
|---|---:|---|---|---|---|
| SL500 | 1 | 4 GB | DDR2 | SSD; kapasite ? | Dizüstü bilgisayar |
| J1900 | 1 | 4 GB | DDR3 | 64 GB SSD | Kayıtlı sistem |
| Fujitsu | 1 | 1 GB | DDR2 | 4 GB CF | CF depolama |
| HP | 1 | ? | DDR3 | 1 GB IDE Flash | 4 GB RAM + 256 GB SATA SSD **planlı** |
| Dell Wyse | 3 | Her biri 2 GB | DDR2 | IDE Flash; kapasite ? | Üçü aynı konfigürasyon |
| ASRock | 2 | ? | DDR3L | ? | İkisi aynı konfigürasyon |
| Jetway | 1 | Takılı değil | DDR3 | Takılı değil | Boş anakart |

### 2.3 Donanım özellikleriyle kısa kıyas

| Karşılaştırma | Sonuç |
|---|---|
| **Aynı CPU olasılığı** | J1900 ile ASRock IMB-151D aynı **Celeron J1900** işlemciyi kullanır; ASRock kartlarının D/N varyantı henüz bilinmiyor. |
| **Dört çekirdekli modeller** | J1900 kesin; ASRock varyantlarının ikisi de dört çekirdekli Celeron ailesinde. |
| **İki çekirdekli modeller** | HP t510 (VIA Eden X2) ve Jetway (Atom N2800). |
| **Tek çekirdekli eski modeller** | Fujitsu ve Dell Wyse için beklenen işlemci yapısı. |
| **En yüksek mevcut doğrulanmış RAM kapasitesi** | SL500 ve J1900: 4'er GB. |
| **En çok bulunan model** | Dell Wyse: 3 adet. |
| **Model başına ikinci cihaz** | ASRock: 2 adet. |
| **RAM / disk takılı olmayan kart** | Jetway. |

---

## 3. Yedek Parça Envanteri

Aşağıdaki parçalar bilgisayarlara **takılı olduğu kaydedilen donanımlardan ayrı**, fotoğraflanan parçaları ifade eder. Bir parçanın çalıştığı, özellikle test edilmediği sürece varsayılmaz.

### 3.1 SO-DIMM RAM (11 adet)

| No | Marka / tanım | Kapasite | Bellek türü | Etiket hızı | Durum |
|---|---|---:|---|---|---|
| R01 | Samsung (fotoğrafta solda dik) | 8 GB | DDR3L | 1600 MHz / PC3L-12800S | **ARIZALI** |
| R02 | Samsung | 4 GB | DDR3 | 1333 MHz / PC3-10600S | Test edilmedi |
| R03 | Lenovo OEM | 4 GB | DDR3 | 1333 MHz / PC3-10600 | Test edilmedi |
| R04 | Elpida | 2 GB | DDR3 | 1066 MHz / PC3-8500S | Test edilmedi |
| R05 | Micron | 2 GB | DDR3 | 1600 MHz / PC3-12800S | Test edilmedi |
| R06 | Micron | 2 GB | DDR3 | 1600 MHz / PC3-12800S | Test edilmedi |
| R07 | Micron | 2 GB | DDR3L | 1600 MHz / PC3L-12800S | Test edilmedi |
| R08 | Micron | 1 GB | DDR3 | 1066 MHz / PC3-8500S | Test edilmedi |
| R09 | SK Hynix | 2 GB | DDR3 | 1333 MHz / PC3-10600S | Test edilmedi |
| R10 | Fotoğrafta en sağ üst modül | ? | **DDR2** | ? | Test edilmedi |
| R11 | Fotoğrafta en sağ alt modül | ? | **DDR2** | ? | Test edilmedi |

**RAM özeti:** 7 adet DDR3 (17 GB); 2 adet DDR3L (2 GB test edilmedi + 8 GB **arızalı**); 2 adet DDR2 (kapasiteleri belirsiz). Toplam 11 modül. Arızalı modül dışındaki DDR3/DDR3L kapasitesi **19 GB**; test edilmiş çalışır kapasite anlamına gelmez.

### 3.2 CompactFlash kartlar (2 adet)

| No | Marka / model | Kapasite | Tür | Durum |
|---|---|---:|---|---|
| CF01 | PQI Hi-Speed 40 | 1 GB | CompactFlash | Test edilmedi |
| CF02 | MemoRight CF Fast C-temp | 32 GB | CompactFlash | Test edilmedi |

### 3.3 M.2 ve özel bağlantılı SSD'ler (3 adet)

| No | Marka / model | Kapasite | Bağlantı | Durum |
|---|---|---:|---|---|
| M201 | Samsung MZ-VLW2560 | 256 GB | M.2 NVMe | Test edilmedi |
| M202 | Samsung MZ-VLB256B | 256 GB | M.2 NVMe | Test edilmedi |
| M203 | Apple tipi blade SSD (siyah PCB) | ? | Apple'a özgü bağlantı | Model/kapasite ve çalışma durumu doğrulanacak |

### 3.4 mSATA SSD'ler (7 adet)

| No | Tanım | Kapasite | Bağlantı | Durum |
|---|---|---:|---|---|
| MS01 | 256 GB etiketli mSATA | 256 GB | mSATA | Test edilmedi |
| MS02 | 64 GB etiketli mSATA | 64 GB | mSATA | Test edilmedi |
| MS03 | 128 GB mSATA | 128 GB | mSATA | Test edilmedi |
| MS04 | 128 GB mSATA | 128 GB | mSATA | Test edilmedi |
| MS05 | 128 GB mSATA | 128 GB | mSATA | Test edilmedi |
| MS06 | 128 GB mSATA | 128 GB | mSATA | Test edilmedi |
| MS07 | 128 GB mSATA | 128 GB | mSATA | Test edilmedi |

> mSATA modülleri fiziksel olarak mini-PCIe kartlarına benzeyebilir; yuva uyumluluğu otomatik olarak varsayılmamalıdır.

### 3.5 2,5 inç SATA SSD'ler (3 adet)

| No | Marka / model | Kapasite | Bağlantı | Durum |
|---|---|---:|---|---|
| S01 | Kioxia TR200 | 240 GB | 2,5 inç SATA | Test edilmedi |
| S02 | Kırmızı kasalı SSD 1 | ? | 2,5 inç SATA | Model/kapasite doğrulanacak |
| S03 | Kırmızı kasalı SSD 2 | ? | 2,5 inç SATA | Model/kapasite doğrulanacak |

> HP için kullanılması planlanan **256 GB SATA SSD** bu üç fotoğraflanmış diskten biri olabilir, ancak hangisi olduğu henüz eşleştirilmedi. Bu yüzden toplam envantere ayrıca yeni bir disk eklenmedi.

### 3.6 Yedek parça sayımı

| Kategori | Adet |
|---|---:|
| SO-DIMM RAM | 11 |
| CompactFlash kart | 2 |
| M.2 / özel bağlantılı SSD | 3 |
| mSATA SSD | 7 |
| 2,5 inç SATA SSD | 3 |
| **TOPLAM** | **26** |

**Kayıt ilkesi:** Bilinmeyenleri tahminle doldurma; montaj veya test sonrasında `?`, `Test edilmedi` ve `planlanan` alanlarını güncelle. Arızalı 8 GB DDR3L RAM'i kullanılabilir yedek olarak sayma.
