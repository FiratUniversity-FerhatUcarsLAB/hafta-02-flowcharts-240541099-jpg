BAŞLA

  // --- SİSTEM BAŞLATMA (INITIALIZATION) ---
  // Sistemin çalışması için gerekli tüm değişkenleri başlangıç durumuna getir.
  Sistem_Durumu = "KURULU"         // Sistemin aktif olup olmadığını kontrol eder.
  Ev_Sahibi_Durumu = "EVDE_DEGIL"  // Ev sahibinin konumunu belirler (yanlış alarmları önler).
  Alarm_Sesi = "KAPALI"            // Siren durumunu takip eder.
  Kamera_Durumu = "PASIF"          // Kamera sisteminin durumunu takip eder.
  Sensor_Verileri = {}             // Ev içindeki sensörlerden gelen anlık verileri tutar.
  Dis_Dunya_Verileri = {}          // Hava durumu, elektrik kesintisi gibi dış verileri tutar.

  // --- ANA KONTROL DÖNGÜSÜ (7/24 ÇALIŞMA) ---
  // Bu döngü, sistem çalıştığı sürece sonsuza dek devam eder.
  SONSUZ_DÖNGÜ BAŞLAT:

    // === ADIM 1: İSTİHBARAT TOPLAMA (DATA GATHERING) ===
    // Dış dünyadan ve evin içinden anlık verileri topla.
    Dis_Dunya_Verileri = Harici_Servisleri_Kontrol_Et() // Hava durumu, elektrik uyarısı vb.
    Sensor_Verileri = Tum_Sensorleri_Dinle()         // Hareket, kapı, pencere sensörleri vb.

    // === ADIM 2: DURUM DEĞERLENDİRMESİ VE KARAR VERME (ANALYSIS & DECISION) ===
    // Toplanan verilere göre bir tehdit seviyesi belirle. Her döngüde sıfırlanır.
    Tehdit_Seviyesi = "YOK"
    Tepki_Mesaji = ""
    Tehdit_Konumu = ""

    // --- SEVİYE 1: KRİTİK TEHDİT (Fiziksel Güvenlik İhlali Tespiti) ---
    // En yüksek öncelikli durum. Sadece sistem kurulu ve ev sahibi dışarıdaysa çalışır.
    EĞER Sistem_Durumu == "KURULU" VE Ev_Sahibi_Durumu == "EVDE_DEGIL" İSE:
      EĞER Sensor_Verileri.Hareket == "VAR" VEYA Sensor_Verileri.Kapi_Durumu == "AÇIK" VEYA Sensor_Verileri.Pencere_Durumu == "ZORLANDI" İSE:
        Tehdit_Seviyesi = "KRITIK"
        Tepki_Mesaji = "ACİL DURUM: Fiziksel güvenlik ihlali tespit edildi!"
        Tehdit_Konumu = Sensor_Verileri.Algilanan_Konum // Tehdidin kaynağını belirle (Örn: "Salon").
      SON_EĞER
    SON_EĞER

    // --- SEVİYE 2: ÖNLEM GEREKTİREN DURUM (Sistemsel Risk Tespiti) ---
    // Kritik bir tehdit yoksa, daha düşük öncelikli riskleri kontrol et.
    EĞER Tehdit_Seviyesi == "YOK" VE Dis_Dunya_Verileri.Elektrik_Kesintisi_Uyarisi == "VAR" İSE:
      Tehdit_Seviyesi = "ONLEM"
      Tepki_Mesaji = "BİLGİ: Planlı elektrik kesintisi uyarısı alındı. Cihaz koruması için önlem alınıyor."

    // --- SEVİYE 3: UYARI GEREKTİREN DURUM (Çevresel Risk Tespiti) ---
    EĞER Tehdit_Seviyesi == "YOK" VE Dis_Dunya_Verileri.Hava_Durumu == "Yagmurlu" VE Sensor_Verileri.Pencere_Durumu == "AÇIK" İSE:
      Tehdit_Seviyesi = "UYARI"
      Tepki_Mesaji = "TAVSİYE: Hava yağmurlu ve bir pencere açık. Kapatmanız önerilir."
    SON_EĞER


    // === ADIM 3: EYLEME GEÇİŞ (ACTION) ===
    // Belirlenen tehdit seviyesine göre en uygun tepkiyi ver.
    DURUMA_GORE (Tehdit_Seviyesi):
      DURUM "KRITIK":
        // Standart alarm prosedürlerini başlat.
        Sireni_Cal()
        Isiklari_Yak_Son()
        Alarm_Sesi = "AKTIF"

        // Görsel kanıt toplama ve doğrulama prosedürlerini başlat.
        Ilgili_Kamerayi_Aktive_Et(Tehdit_Konumu)
        Goruntu_Kaydini_Baslat(Tehdit_Konumu)
        Kamera_Durumu = "AKTIF"

        // Kullanıcıya anında, kanıt içeren bir bildirim gönder.
        Canli_Yayin_Linki = Canli_Goruntu_Linki_Olustur(Tehdit_Konumu)
        Kullaniciya_Bildirim_Gonder(Tepki_Mesaji + " Canlı izle: " + Canli_Yayin_Linki, "ACIL")
        BREAK // Bu seviye işlendi, diğerlerini kontrol etme.

      DURUM "ONLEM":
        // Proaktif olarak eyleme geç.
        Evin_Ana_Salterini_Kapat()
        Kullaniciya_Bildirim_Gonder(Tepki_Mesaji, "BILGI")
        BREAK

      DURUM "UYARI":
        // Kullanıcıyı bilgilendir.
        Kullaniciya_Bildirim_Gonder(Tepki_Mesaji, "TAVSIYE")
        BREAK

    SON_DURUM

    // Sistemin işlemciyi yormaması ve API limitlerini aşmaması için kısa bir mola.
    Bekle(3 saniye)

  DÖNGÜ_SONU

SON
