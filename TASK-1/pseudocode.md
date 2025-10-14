 // --- Değişkenlerin Başlatılması ---
  GECERLI_PIN = "1234" // Bu, gerçek sistemde karttan okunan veya veritabanından gelen ÖRNEK bir PIN'dir.
  HESAP_BAKIYESI = 1500
  GUNLUK_CEKILEN_TUTAR = 0
  GUNLUK_LIMIT = 1000
  PIN_DENEME_SAYISI = 0
  ISLEM_DEVAM = DOGRU

  // --- Başlangıç Mesajları ---
  YAZ "ATM Sistemine Hos Geldiniz!"
  YAZ "Lutfen kartinizi takiniz..."
  // Kartın her zaman geçerli olduğu varsayılır.

  // --- ANA İŞLEM DÖNGÜSÜ ---
  // Kullanıcı çıkış yapana veya kart bloke olana kadar devam eder.
  DONGU (ISLEM_DEVAM == DOGRU)

    // --- PIN DOĞRULAMA BÖLÜMÜ ---
    PIN_DENEME_SAYISI = 0
    DONGU (PIN_DENEME_SAYISI < 3)

      YAZ "Lutfen 4 haneli PIN kodunuzu giriniz: "
      OKU KULLANICI_PIN

      EGER-ISE (KULLANICI_PIN == GECERLI_PIN)
        YAZ "PIN dogrulandi. Hos geldiniz!"
        KIR // PIN doğrulama döngüsünden çık.
      DEGILSE
        // *** DÜZELTİLMİŞ MANTIK BAŞLANGICI ***
        // 1. Sayaç artırılır.
        PIN_DENEME_SAYISI = PIN_DENEME_SAYISI + 1

        // 2. Sayaç artırıldıktan HEMEN SONRA kalan hak kontrol edilir.
        EGER-ISE (PIN_DENEME_SAYISI < 3)
          // Hala hak varsa: Hata mesajı verilir ve döngü devam eder.
          YAZ "Hatali PIN. Kalan hakkiniz: " + (3 - PIN_DENEME_SAYISI)
        DEGILSE
          // Hak kalmadıysa (3. yanlış giriş yapıldıysa):
          YAZ "PIN'inizi 3 kez hatali girdiniz. Kartiniz bloke olmustur. Lutfen bankanizla iletisime geciniz."
          ISLEM_DEVAM = YANLIS // Ana işlem döngüsünü sonlandır.
        BITIR
        // *** DÜZELTİLMİŞ MANTIK SONU ***
      BITIR

    BITIR // PIN doğrulama döngüsü sonu

    // --- PIN SONRASI KONTROL ---
    // Eğer kart bloke olduysa (ISLEM_DEVAM = YANLIS ise) para çekme adımlarına geçilmez.
    EGER-ISE (ISLEM_DEVAM == DOGRU)

      // --- PARA ÇEKME İŞLEMLERİ ---
      YAZ "Mevcut Bakiyeniz: " + HESAP_BAKIYESI + " TL"
      YAZ "Bugun cekilen tutar: " + GUNLUK_CEKILEN_TUTAR + " TL"
      YAZ "Kalan gunluk limitiniz: " + (GUNLUK_LIMIT - GUNLUK_CEKILEN_TUTAR) + " TL"

      YAZ "Lutfen cekmek istediginiz tutari giriniz (20 TL'nin katlari olmalidir): "
      OKU CEKILECEK_TUTAR

      // Tutar Kontrolü (20'nin katı mı?)
      EGER-ISE (CEKILECEK_TUTAR MOD 20 == 0 VE CEKILECEK_TUTAR > 0)

        // Bakiye Kontrolü
        EGER-ISE (CEKILECEK_TUTAR <= HESAP_BAKIYESI)

          // Günlük Limit Kontrolü
          EGER-ISE ((GUNLUK_CEKILEN_TUTAR + CEKILECEK_TUTAR) <= GUNLUK_LIMIT)

            // Tüm kontroller başarılıysa: İşlem gerçekleştirilir.
            HESAP_BAKIYESI = HESAP_BAKIYESI - CEKILECEK_TUTAR
            GUNLUK_CEKILEN_TUTAR = GUNLUK_CEKILEN_TUTAR + CEKILECEK_TUTAR
            YAZ CEKILECEK_TUTAR + " TL cekildi. Yeni bakiyeniz: " + HESAP_BAKIYESI + " TL"
            YAZ "Lutfen paranizi aliniz."

          DEGILSE
            YAZ "Uzgunuz, gunluk cekim limitinizi astiniz."
          BITIR

        DEGILSE
          YAZ "Uzgunuz, bakiyeniz yetersiz."
        BITIR

      DEGILSE
        YAZ "Girdiginiz tutar gecersiz. Lutfen 20 TL'nin katlari olan pozitif bir tutar giriniz."
      BITIR

      // --- İŞLEM TEKRARI SEÇENEĞİ ---
      YAZ "Baska bir islem yapmak ister misiniz? (E/H)"
      OKU SECENEK

      EGER-ISE (SECENEK == 'H' VEYA SECENEK == 'h')
        ISLEM_DEVAM = YANLIS // Kullanıcı çıkmak istedi, ana döngüyü sonlandır.
      BITIR

    BITIR // PIN sonrası kontrolün sonu

  BITIR // Ana işlem döngüsü sonu

  // --- Bitiş Mesajı ---
  YAZ "Iyi gunler dileriz!"

SON
