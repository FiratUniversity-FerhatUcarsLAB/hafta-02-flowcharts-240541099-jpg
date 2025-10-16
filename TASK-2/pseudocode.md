// --- E-TİCARET SİSTEMİ PSEUDOCODE (Resmi Versiyon) ---

BAŞLA

    // Kullanıcı Giriş Kontrolü Aşaması
    EĞER kullanici_giris_yapti_mi DEĞİLSE
        YAZ "Giriş yapmanız gerekiyor."
        Giris_Yap()
        EĞER giris_basarili DEĞİLSE
            YAZ "Giriş başarısız. İşlem sonlandırıldı."
            BİTİR
        BİTİR EĞER
    BİTİR EĞER

    // Ürün Seçim ve Sepete Ekleme Aşaması
    DÖNGÜ alisverise_devam_etmek_istiyor SÜRECE
        Urunleri_Listele()
        OKU secilen_urun

        // Stok Kontrolü
        EĞER stok_var_mi(secilen_urun) İSE
            Sepete_Ekle(secilen_urun)
            YAZ "Ürün sepete eklendi."
        DEĞİLSE
            YAZ "Ürün stokta yok."
        BİTİR EĞER
        
        YAZ "Alışverişe devam mı? (EVET/HAYIR)"
        OKU devam_karari
        EĞER devam_karari == "HAYIR" İSE
            alisverise_devam_etmek_istiyor = HAYIR
        BİTİR EĞER
    BİTİR DÖNGÜ

    // Sepet ve Ödeme Öncesi Kontrol Aşaması
    Sepeti_Goster()
    
    YAZ "İndirim kodunuz var mı? (EVET/HAYIR)"
    OKU kupon_cevabi
    EĞER kupon_cevabi == "EVET" İSE
        OKU girilen_kod
        EĞER kod_gecerli_mi(girilen_kod) İSE
            Indirimi_Uygula()
            YAZ "İndirim uygulandı."
        DEĞİLSE
            YAZ "Geçersiz kod."
        BİTİR EĞER
    BİTİR EĞER

    // Minimum Sepet Tutarı Kontrolü
    toplam_tutar = Tutar_Hesapla()
    EĞER toplam_tutar < 50 İSE
        YAZ "Minimum sepet tutarı 50 TL olmalıdır."
        BİTİR
    BİTİR EĞER

    // Kargo Ücreti Hesaplama
    EĞER toplam_tutar >= 200 İSE
        kargo_ucreti = 0
        YAZ "Kargonuz ücretsizdir."
    DEĞİLSE
        kargo_ucreti = STANDART_UCRET
        YAZ "Kargo ücreti eklendi."
    BİTİR EĞER

    // Ödeme Aşaması
    odenecek_tutar = toplam_tutar + kargo_ucreti
    YAZ "Toplam ödenecek tutar: " + odenecek_tutar
    
    YAZ "Ödeme yöntemini seçin."
    OKU secilen_odeme_yontemi
    
    // Ödeme İşlemi ve Sonuç
    odeme_sonucu = Odeme_Yap(secilen_odeme_yontemi, odenecek_tutar)
    
    EĞER odeme_sonucu == "BAŞARILI" İSE
        Siparisi_Onayla()
        YAZ "Siparişiniz başarıyla oluşturuldu."
    DEĞİLSE
        YAZ "Ödeme başarısız oldu."
    BİTİR EĞER

BİTİR
