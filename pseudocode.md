BAŞLA Kayitİslemi(Ogrenci, SecilenDerslerListesi)
    // Başlangıç değişkenlerini tanımla
    ToplamKredi = 0
    OnaylananDersler = Boş Liste
    HataMesajlari = Boş Liste

    // Her bir ders için doğrulama döngüsünü başlat
    DÖNGÜ: SecilenDerslerListesi içindeki HER BİR 'Ders' İÇİN:

        // 1. Kontenjan Kontrolü
        EĞER Ders.MevcutKapasite < Ders.MaksimumKapasite İSE:

            // 2. Önkoşul Kontrolü
            EĞER Ogrenci.GecmisDersleriIceriyorMu(Ders.Onkosullar) == DOĞRU İSE:

                // 3. Program Çakışma Kontrolü
                EĞER ProgramCakisiyorMu(OnaylananDersler, Ders.ZamanDilimi) == YANLIŞ İSE:

                    // 4. Kredi Limiti Kontrolü
                    EĞER ToplamKredi + Ders.KrediDegeri <= Ogrenci.MaksimumKrediLimiti İSE:

                        // Tüm kontrollerden geçti, dersi listeye ekle ve krediyi güncelle
                        OnaylananDersler.Ekle(Ders)
                        ToplamKredi = ToplamKredi + Ders.KrediDegeri

                    DEĞİLSE:
                        HataMesajlari.Ekle(Ders.Ad + ": Kredi limiti aşıldığı için eklenemedi.")
                    BİTİR EĞER

                DEĞİLSE:
                    HataMesajlari.Ekle(Ders.Ad + ": Mevcut programınızdaki başka bir dersle çakışıyor.")
                BİTİR EĞER

            DEĞİLSE:
                HataMesajlari.Ekle(Ders.Ad + ": Gerekli önkoşul derslerini henüz tamamlamadınız.")
            BİTİR EĞER

        DEĞİLSE:
            HataMesajlari.Ekle(Ders.Ad + ": Kontenjan dolu.")
            // Yedek listeye (waitlist) ekleme fonksiyonu buraya çağrılabilir
        BİTİR EĞER

    DÖNGÜ BİTİR

    // 5. Danışman Onayı Aşaması
    EĞER OnaylananDersler.OgeSayisi > 0 İSE:
        // Dersler danışmanın ekranına gönderilir
        DanismanKarari = DanismanaOnayaGonder(Ogrenci, OnaylananDersler)

        EĞER DanismanKarari == "ONAYLANDI" İSE:
            VeritabaninaKaydet(Ogrenci, OnaylananDersler)
            YAZDIR "Kayıt işlemi başarıyla tamamlandı."
        DEĞİLSE:
            YAZDIR "Kayıt beklemede. Danışmanınız taslağı reddetti veya değişiklik talep etti."
        BİTİR EĞER

    DEĞİLSE:
        YAZDIR "Kayıt başarısız. Seçilen hiçbir ders doğrulama adımlarını geçemedi."
    BİTİR EĞER

    // Öğrenciye süreçte oluşan hataları göster
    EĞER HataMesajlari.OgeSayisi > 0 İSE:
        YAZDIR "Aşağıdaki hatalar oluştu:"
        YAZDIR HataMesajlari
    BİTİR EĞER

BİTİR Kayitİslemi
