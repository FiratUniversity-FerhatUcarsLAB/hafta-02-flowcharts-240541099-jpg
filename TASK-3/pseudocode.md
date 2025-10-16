// ==========================================================
// HASTANE RANDEVU VE TAHLİL SİSTEMİ - BİRLEŞTİRİLMİŞ PSEUDOCODE
// ==========================================================

// --- ANA PROGRAM BAŞLANGICI ---
PROCEDURE ANA_SISTEM_BASLAT
  YAZ "Sisteme erişim için TC Kimlik Numaranızı girin:"
  OKU kullanici_tc
  
  EĞER kimlik_dogrulama(kullanici_tc) == BAŞARILI İSE
    YAZ "Kimlik doğrulama başarılı. Ana menü yükleniyor..."
    ANA_MENU(kullanici_tc) // Ana menüyü kullanıcı bilgisiyle başlat
  DEĞİLSE
    YAZ "Erişim reddedildi. Kimlik doğrulama başarısız."
  BİTİR EĞER
END PROCEDURE
// --- ANA PROGRAM SONU ---


// --- ANA MENÜ MODÜLÜ ---
PROCEDURE ANA_MENU(kullanici_tc)
  DÖNGÜ (kullanici_cikis_istemeyene_kadar)
    YAZ "--------------------------------------------------"
    YAZ "ANA MENÜ | İşlem Seçin:"
    YAZ "1- Randevu İşlemleri"
    YAZ "2- Tahlil Sonuçlarını Görüntüle"
    YAZ "3- Çıkış"
    OKU secim
      
    EĞER secim == 1 İSE
      RANDEVU_AL(kullanici_tc, null, null) // Standart randevu alma işlemi
    DEĞİLSE EĞER secim == 2 İSE
      TAHLIL_SONUCLARI_GOSTER(kullanici_tc)
    DEĞİLSE EĞER secim == 3 İSE
      YAZ "Güvenli çıkış yapılıyor... Bağlantı sonlandırıldı."
      break // Döngüden çık
    DEĞİLSE
      YAZ "Hatalı komut. Lütfen tekrar deneyin."
    BİTİR EĞER
  BİTİR DÖNGÜ
END PROCEDURE
// --- ANA MENÜ MODÜLÜ SONU ---


// --- RANDEVU MODÜLÜ ---
PROCEDURE RANDEVU_AL(kullanici_tc, on_secili_doktor, randevu_tipi)
  YAZ "--- Randevu Terminali Aktif ---"
  
  // Poliklinik seçimi
  DÖNGÜ
    YAZ "Poliklinikler: [1-Dahiliye, 2-Kardiyoloji, ..., 9-Geri]"
    OKU poliklinik_secimi
    EĞER poliklinik_secimi GEÇERLİ İSE break DEĞİLSE EĞER poliklinik_secimi == 9 İSE return DEĞİLSE YAZ "Hatalı seçim."
  BİTİR DÖNGÜ
  
  // Doktor seçimi
  DÖNGÜ
    YAZ secilen_poliklinik + " doktorları: [1-Dr. X, 2-Dr. Y, ..., 9-Geri]"
    OKU doktor_secimi
    EĞER doktor_secimi GEÇERLİ İSE break DEĞİLSE EĞER doktor_secimi == 9 İSE continue (üst döngüye dön) DEĞİLSE YAZ "Hatalı seçim."
  BİTİR DÖNGÜ
  
  // Saat seçimi ve onay
  YAZ secilen_doktor + " için uygun saatler: [09:00, 10:30, ...]"
  OKU saat_secimi
  
  EĞER secilen_saat GEÇERLİ VE UYGUN İSE
    YAZ "Onay: " + poliklinik_secimi + ", " + doktor_secimi + ", " + saat_secimi + ". Onaylıyor musunuz? (E/H)"
    OKU onay
    EĞER onay == 'E' İSE
      YAZ "Randevu oluşturuldu. Sistem veritabanına işlendi."
    DEĞİLSE
      YAZ "İşlem iptal edildi."
    BİTİR EĞER
  DEĞİLSE
    YAZ "Seçilen saat uygun değil veya geçersiz."
  BİTİR EĞER
END PROCEDURE
// --- RANDEVU MODÜLÜ SONU ---


// --- TAHLİL MODÜLÜ v2.0 ---
PROCEDURE TAHLIL_SONUCLARI_GOSTER(kullanici_tc)
  YAZ "--- Tahlil Sonuçları Terminali Aktif ---"
  tahlil_listesi = tahlilleri_getir(kullanici_tc)

  EĞER tahlil_listesi BOŞ DEĞİLSE
    DÖNGÜ
      FOR her_tahlil IN tahlil_listesi
        YAZ her_tahlil.id + " - " + her_tahlil.adi + " [Durum: " + her_tahlil.durum + "]"
      ENDFOR
      YAZ "İncelemek istediğiniz 'Hazır' tahlilin numarasını girin (Geri için 9):"
      OKU secilen_tahlil_id
      
      EĞER secilen_tahlil_id == 9 İSE break
      
      secilen_tahlil = tahlil_listesi.bul(secilen_tahlil_id)
      
      EĞER secilen_tahlil GEÇERLİ VE secilen_tahlil.durum == "Hazır" İSE
        sorumlu_doktor = secilen_tahlil.doktor
        EĞER doktor_musait_mi(sorumlu_doktor) == EVET İSE
          YAZ "Doktorunuz müsait. Görüntülü görüşme başlatılsın mı? (E/H)"
          OKU gorusme_onay
          EĞER gorusme_onay == 'E' İSE YAZ "Görüşme başlatılıyor..." // GÖRÜŞME SİMÜLASYONU
          DEĞİLSE YAZ "İşlem iptal edildi."
        DEĞİLSE
          YAZ "Doktorunuz müsait değil. 'Sonuç Görüşme Randevusu' oluşturulsun mu? (E/H)"
          OKU randevu_onay
          EĞER randevu_onay == 'E' İSE
            // Diğer modülü tetikle
            RANDEVU_AL(kullanici_tc, sorumlu_doktor, "Sonuç Değerlendirme")
          DEĞİLSE YAZ "İşlem iptal edildi."
        BİTİR EĞER
        break // İşlem bitti, döngüden çık
      DEĞİLSE
        YAZ "Hatalı seçim veya sonuçlar hazır değil."
      BİTİR EĞER
    BİTİR DÖNGÜ
  DEĞİLSE
    YAZ "Sistemde adınıza kayıtlı bir tahlil bulunamadı."
  BİTİR EĞER
END PROCEDURE
// --- TAHLİL MODÜLÜ v2.0 SONU ---


// --- PROGRAMI ÇALIŞTIR ---
BAŞLA
  ANA_SISTEM_BASLAT
BİTİR
