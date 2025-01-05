#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char adlar[100][50];               
char soyadlar[100][50];             
char dogumTarihleri[100][11];      
char pasaportNumaralari[100][10];   

char kullaniciAdlari[3][20] = {"admin", "kullanici01", "kullanici02"};
char sifreler[3][20] = {"2668", "12345678", "87654321"};

void ogrenciBilgileriniAl(int index) {
    printf("\n--- Ogrenci Bilgilerini Giriniz! ---\n");
    printf("Ad: ");
    scanf("%49s", adlar[index]);
    printf("Soyad: ");
    scanf("%49s", soyadlar[index]);
    printf("Dogum Tarihi (GG/AA/YYYY): ");
    scanf("%10s", dogumTarihleri[index]);
    printf("Pasaport Numarasi: ");
    scanf("%9s", pasaportNumaralari[index]);
    printf("Bilgiler Basariyla Kaydedildi!\n");
}

int gerekliBelgeleriKontrolEt() {
    char belgeler[3][50] = {"Kabul Mektubu", "Finansal Durum Kaniti", "Saglik Sigortasi Policesi"};
    char cevap;

    printf("\n--- Belgeler Kontrol Ediliyor ---\n");
    int i;
	for (i = 0; i < 3; i++) {
        printf("%s mevcut mu? (E/H): ", belgeler[i]);
        getchar();  
        scanf("%c", &cevap);

        if (cevap == 'h' || cevap == 'H') {
            printf("Belgeler Eksik! Lutfen Tamamlayip Tekrar Deneyiniz.\n");
            return 0;
        }
    }
    printf("Belgeler Tamamlandi.\n");
    return 1;
}

void randevuTalebiAl(char *tarih, char *saat) {
    printf("\n--- Randevu Talebi ---\n");
    printf("Randevu Tarihi (GG/AA/YYYY): ");
    scanf("%14s", tarih);
    printf("Randevu Saati (SS:DD): ");
    scanf("%5s", saat);
}

int randevuUygunlukKontrol(char *tarih, char *saat) {
    char uygunTarih[15] = "17/01/2025";
    char uygunSaat[6] = "09:30";

    if (strcmp(tarih, uygunTarih) == 0 && strcmp(saat, uygunSaat) == 0) {
        printf("Talep Edilen Randevu Uygun Degildir, Reddedildi!\n");
        return 0;
    }
    printf("Randevu Uygun. Kayit Islemini Gerceklestirebilirsiniz.\n");
    return 1;
}

void dosyayaKaydet(int index, char *tarih, char *saat) {
    FILE *dosya = fopen("randevu_kaydi.txt", "a");
    if (dosya == NULL) {
        printf("Dosya açilamadi!\n");
        return;
    }

    fprintf(dosya, "\n--- Randevu Kaydi ---\n");
    fprintf(dosya, "Ad: %s\n", adlar[index]);
    fprintf(dosya, "Soyad: %s\n", soyadlar[index]);
    fprintf(dosya, "Dogum Tarihi: %s\n", dogumTarihleri[index]);
    fprintf(dosya, "Pasaport Numarasi: %s\n", pasaportNumaralari[index]);
    fprintf(dosya, "Randevu Tarihi: %s\n", tarih);
    fprintf(dosya, "Randevu Saati: %s\n", saat);
    fclose(dosya);

    printf("Randevunuz Basariyla Kaydedildi!\n");
}

int kullaniciGirisi() {
    char kullaniciAdi[20], sifre[20];

    printf("\n--- Sisteme Giris Yapiniz ---\n");
    printf("Kullanici Adi: ");
    scanf("%19s", kullaniciAdi);
    printf("Sifre: ");
    scanf("%19s", sifre);
    int i;
    for (i = 0; i < 3; i++) {
        if (strcmp(kullaniciAdi, kullaniciAdlari[i]) == 0 && strcmp(sifre, sifreler[i]) == 0) {
            printf("Giris Basarili! Hos geldiniz! %s!\n", kullaniciAdi);
            return 1;
        }
    }
    printf("Giris Basarisiz! Kullanici Adi Veya Sifre Hatali.\n");
    return 0;
}

int main() {
    char tarih[15], saat[6];
    int secim, belgelerTam, randevuUygun, ogrenciSayisi = 0;

    printf("\n--- VIZE RANDEVU SISTEMI ---\n");

    if (!kullaniciGirisi()) {
        printf("Geçerli Giris Yapmadiginiz Icin Islemler Yapilamaz.\n");
        return 0;
    }

    do {
        printf("\nLutfen Yapmak Istediginiz Islemi Seciniz:\n");
        printf("1. Ogrenci Bilgilerini Al\n");
        printf("2. Belgeleri Kontrol Et\n");
        printf("3. Randevu Talep Et\n");
        printf("4. Randevuyu Kaydet\n");
        printf("5. Cikis\n");
        printf("Seçiminiz: ");
        scanf("%d", &secim);

        switch (secim) {
            case 1:
                if (ogrenciSayisi < 100) {
                    ogrenciBilgileriniAl(ogrenciSayisi);
                    ogrenciSayisi++;
                } else {
                    printf("Maksimum Ogrenci Sayisina Ulasildi!\n");
                }
                break;
            case 2:
                belgelerTam = gerekliBelgeleriKontrolEt();
                break;
            case 3:
                randevuTalebiAl(tarih, saat);
                break;
            case 4:
                if (ogrenciSayisi > 0) {
                    randevuUygun = randevuUygunlukKontrol(tarih, saat);
                    if (randevuUygun) {
                        dosyayaKaydet(ogrenciSayisi - 1, tarih, saat);
                    }
                } else {
                    printf("Once Ogrenci Bilgilerini Giriniz!\n");
                }
                break;
            case 5:
                printf("Cikiliyor...\n");
                break;
            default:
                printf("Gecersiz Secim! Lutfen Tekrar Deneyiniz.\n");
        }
    } while (secim != 5);

    return 0;
}
