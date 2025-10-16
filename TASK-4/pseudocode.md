BAŞLA

  YAZ "Öğrenci No Giriniz:"
  OKU ogrenciNo

  YAZ "Şifre Giriniz:"
  OKU sifre

  EĞER ogrenciNo ve sifre doğru DEĞİLSE
    YAZ "Hatalı giriş. Program sonlandırılıyor."
    BİTİR
  DEĞİLSE
    YAZ "Giriş başarılı."

    mevcutDersler = ogrenciVeritabanindanCek(ogrenciNo)
    toplamKredi = mevcutKredileriHesapla(mevcutDersler)
    gpa = ogrenciGPAHesapla(ogrenciNo)

    // <-- İYİLEŞTİRME: Kullanıcıyı başında bilgilendirelim
    EĞER gpa < 2.5 İSE
      YAZ "BİLGİ: GPA'niz 2.5'in altında. Kaydı tamamlamak için danışman onayı gerekecektir."
    SON-EĞER

    DÖNGÜ (kullanici "Kaydı Tamamla" seçene kadar)

      // <-- İYİLEŞTİRME: Her döngü başında güncel durumu göster
      YAZ "--- Mevcut Dersleriniz ---"
      YAZ mevcutDersler
      YAZ "Toplam Kredi: " + toplamKredi
      YAZ "--------------------------"
      
      YAZ "1- Ders Listele"
      YAZ "2- Ders Ekle"
      YAZ "3- Ders Çıkar"
      YAZ "4- Kaydı Tamamla"
      OKU secim

      EĞER secim == 1 İSE
        dersListesi = tumDersleriGoster()
      DEĞİLSE EĞER secim == 2 İSE
        YAZ "Eklemek istediğiniz dersin kodunu girin:"
        OKU dersKodu

        // --- İyileştirilmiş Kontrol Mekanizması ---
        
        // <-- İYİLEŞTİRME: Dersin var olup olmadığını kontrol et
        EĞER dersSistemdeVarMi(dersKodu) == false İSE
          YAZ "Hata: Girdiğiniz ders kodu sistemde bulunamadı."
          DEVAM ET // Döngünün başına dön
        SON-EĞER
        
        EĞER dersKontenjaniDoluMu(dersKodu) İSE
          YAZ "Hata: Dersin kontenjanı dolu."
          DEVAM ET // Döngünün başına dön
        SON-EĞER

        EĞER onKosulGereksinimiVarMi(dersKodu) VE onKosulAlinmisMi(ogrenciNo, dersKodu) == false İSE
          YAZ "Hata: Bu ders için ön koşul dersini geçmeniz gerekiyor."
          DEVAM ET // Döngünün başına dön
        SON-EĞER

        EĞER zamanCakismasiVarMi(dersKodu, mevcutDersler) İSE
          YAZ "Hata: Seçtiğiniz ders, mevcut derslerinizle çakışıyor."
          DEVAM ET // Döngünün başına dön
        SON-EĞER

        dersKredisi = dersBilgisiGetir(dersKodu).kredi
        EĞER (toplamKredi + dersKredisi) > 35 İSE
          YAZ "Hata: Kredi limitini (35) aşıyorsunuz."
          DEVAM ET // Döngünün başına dön
        SON-EĞER
        
        // Tüm kontrollerden geçti, dersi ekle
        mevcutDersler.ekle(dersKodu)
        toplamKredi = toplamKredi + dersKredisi
        YAZ dersKodu + " kodlu ders başarıyla eklendi."
        
      DEĞİLSE EĞER secim == 3 İSE
        YAZ "Çıkarmak istediğiniz dersin kodunu girin:"
        OKU cikarilacakDersKodu

        // <-- İYİLEŞTİRME: Ders çıkarma için onay iste
        YAZ cikarilacakDersKodu + " dersini silmek istediğinize emin misiniz? (E/H)"
        OKU onay
        EĞER onay == "E" İSE
          dersKredisi = dersBilgisiGetir(cikarilacakDersKodu).kredi
          mevcutDersler.cikar(cikarilacakDersKodu)
          toplamKredi = toplamKredi - dersKredisi
          YAZ cikarilacakDersKodu + " kodlu ders başarıyla çıkarıldı."
        DEĞİLSE
          YAZ "İşlem iptal edildi."
        SON-EĞER
        
      DEĞİLSE EĞER secim == 4 İSE
        EĞER gpa < 2.5 İSE
          YAZ "GPA'niz 2.5'in altında olduğu için danışman onayı gerekiyor."
          YAZ "Kayıt özeti danışmanınıza iletildi. Onay bekleniyor."
        DEĞİLSE
          YAZ "Ders kaydınız başarıyla tamamlandı."
        SON-EĞER
        
        YAZ "--- Son Kayıt Özeti ---"
        YAZ mevcutDersler
        YAZ "Toplam Kredi: " + toplamKredi
        DÖNGÜDEN ÇIK
        
      SON-EĞER
    SON-DÖNGÜ
    
  SON-EĞER

BİTİR
