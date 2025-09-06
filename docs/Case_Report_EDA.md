# Data Science Intern Case Study - EDA Raporu

## 1. Veri Seti Genel Bilgisi
- **Kaynak Dosya:** `data/Talent_Academy_Case_DT_2025.xlsx`
- **Toplam Gözlem:** 2235
- **Toplam Özellik (Sütun):** 13
- **Hedef Değişken:** TedaviSuresi (Tedavi süresi, seans sayısı olarak)  

### Sütunlar
- **HastaNo** → Anonim hasta kimliği
- **Yas** → Yaş
- **Cinsiyet** → Cinsiyet
- **KanGrubu** → Kan grubu
- **Uyruk** → Uyruk
- **KronikHastalik** → Kronik hastalık(lar), virgülle ayrılmış
- **Bolum** → Bölüm/klinik
- **Alerji** → Alerji(ler), virgülle ayrılmış
- **Tanilar** → Tanılar, virgülle ayrılmış
- **TedaviAdi** → Tedavi adı
- **TedaviSuresi** → Tedavi süresi (seans bazında)
- **UygulamaYerleri** → Uygulama yapılan bölgeler
- **UygulamaSuresi** → Uygulama süresi (dakika bazında)

---

## 2. Veri Tipi İncelemesi
- **Sayısal Değişkenler:** Yas, TedaviSuresi, UygulamaSuresi  
- **Kategorik Değişkenler:** Cinsiyet, KanGrubu, Uyruk, Bolum, TedaviAdi, UygulamaYerleri  
- **Multi-label Değişkenler:** KronikHastalik, Alerji, Tanilar  
- **ID:** HastaNo (analizlerde kullanılmayacak)

**Not:**  
- `TedaviAdi` (244 kategori) ve `UygulamaYerleri` (37 kategori) sütunları yüksek kardinaliteye sahip kategorik değişkenler olarak ele alinmistir.  
- EDA sürecinde kategorik olarak incelenmiştir, ancak preprocessing aşamasında bu değişkenler üzerinde ek temizlik, ayirma islemleri yapilabilir.

---

## 3. Eksik Değer Analizi
Eksik değer sayıları şu şekilde bulunmuştur:
- Alerji → **944**
- KanGrubu → **675**
- KronikHastalik → **611**
- UygulamaYerleri → **221**
- Cinsiyet → **169**
- Tanilar → **75**
- Bolum → **11**
- HastaNo, Yas, Uyruk, TedaviAdi, TedaviSuresi ve UygulamaSuresi sütunlarında eksik değer yok.  
- En çok eksik değer Alerji (944), KanGrubu (675) ve KronikHastalik (611) sütunlarında bulunuyor.  


---

## 4. Sayısal Değişken Donusumleri
- **TedaviSuresi** sütununda `"Seans"` ifadesi kaldırılmış ve integer tipe dönüştürülmüştür.  
- **UygulamaSuresi** sütununda `"Dakika"` ifadesi kaldırılmış ve integer tipe dönüştürülmüştür.  
- Böylece her iki sütun da sayisal sutun analizine uygun hale getirilmiştir.

---

## 5. Sayısal Değişken Analizi

### Yaş (Yas)
- Ortalama: 47
- Dağılım: 40–50 yaş arası yoğunlaşmış.
- Boxplot: 0–10 yaş (çocuklar) ve 80+ yaş (yaşlılar) outlier gibi gözükse de mantıklıdır.
- Sonuç: Yaş değişkeni normal dağılıma yakın ve kullanılabilir.

### Tedavi Süresi (TedaviSuresi)
- Ortalama: ~15 seans
- Dağılım: Çok büyük kısmı **15–20 seans** aralığında.
- Boxplot: 15 seans etrafında yoğunlaşma → Standart tedavi olabilir.
- Sonuç: Ağırlıklı tek bir değerde yoğunlaşma var, dağılım dengesiz.

### Uygulama Süresi (UygulamaSuresi)
- Ortalama: ~16 dakika
- Dağılım: Özellikle **20 dakika** öne çıkıyor.
- Boxplot: 40+ dakikalık değerler outlier gibi gözükse de özel tedavilerde normal olabilir.
- Sonuç: Standart uygulama suresi olabilir.

---

## 6. Kategorik Değişken Analizi

### Cinsiyet (Cinsiyet)
- Kadın: **1274 hasta (%57)**  
- Erkek: **792 hasta (%35)**  
- Eksik: **169 hasta (%7.5)**  
- Kadın hastalar çoğunluktadır. Veri genel olarak dengeli, ancak az da olsa eksik değer vardır.

### Kan Grubu (KanGrubu)
- En sık: **0 Rh+ (%25.9)** ve **A Rh+ (%24.1)**  
- Daha az görülen: AB Rh- (%0.36) gibi nadir kombinasyonlar  
- Eksik değer: **675 (%30.2)**  
- Kan grubu sütununda ciddi oranda eksik veri vardır, bu durum modelleme öncesi mutlaka ele alınmalıdır.

### Uyruk (Uyruk)
- Türkiye: **2173 hasta (%97.2)**  
- Diğer ülkeler: Tokelau (27), Arnavutluk (13), Azerbaycan (12), Libya (10)  
- Veri neredeyse tamamen Türkiye merkezlidir. Uluslararası genellenebilirlik açısından sınırlı bir veri söz konusudur.

### Bölüm (Bolum)
- En yoğun: **Fiziksel Tıp ve Rehabilitasyon, Solunum Merkezi (%91.5)**  
- Diğer bölümler: Ortopedi (%3.9), Dahiliye (%1.4), Nöroloji (%0.7), diğerleri %1’in altında  
- Eksik değer: 11  
- Bölüm sütununda ciddi bir **veri dengesizliği** vardır; modelleme aşamasında bu durum dikkate alınmalıdır.


### Uygulama Yerleri (UygulamaYerleri)
- En sık bölgeler: **Bel, Boyun, Diz**.  
- Çok sayıda nadir kategori mevcut (ör. “Sağ omuz bölgesi + sol bilek bölgesi”).  
- Eksik değer sayısı: 221.  
- Dağılım heterojen, ancak bazı bölgeler yoğun şekilde öne çıkıyor.
- **Not:** Bu sütunda, bazı satırlarda birden fazla uygulama yeri içerdigi gozlemlenmistir. (“Boyun, Bel” gibi). Bu nedenle ilerleyen aşamalarda multi-label encoding ile işlenmesi planlanmaktadır.

### Tedavi Adı (TedaviAdi)
- Toplam 244 farklı tedavi ismi vardır.  
- En sık ilk 20 tedavi arasında: **Dorsalji, Boyun+trapez, Bel tedavileri** öne çıkmaktadır.
- **Not:** TedaviAdi sütununda büyük/küçük harf farklılıkları, sembol kullanımı (+, -, 1-2 gibi) sebebiyle aynı tedavi birden fazla kategoriye bölünmüştür. Bu durum preprocessing aşamasında standardize edilecektir.
---


## 7. Multi-label Değişken Analizi

Bu veri setinde üç sütun birden fazla kategori içerebilmektedir: **KronikHastalik, Alerji, Tanilar**.  
Her satırda virgülle ayrılmış değerler yer almakta, bu nedenle sütunlar *multi-label* yapısındadır.  

### Kronik Hastalıklar (KronikHastalik)
- Toplam **15 farklı kronik hastalık kategorisi** bulundu.  
- En sık görülenler: **Aritmi, Hipotiroidizm, Limb-girdle musküler distrofi, Astım, Hipertiroidizm**.  

### Alerjiler (Alerji)
- Toplam **11 farklı alerji kategorisi** bulundu.  
- En sık görülenler: **Polen, Toz, Novalgin, Sucuk**.
- Veri girişinde yazım farklılıkları görülmektedir (ör. Voltaren / Volteren). Bu durum preprocessing aşamasında standardize edilmelidir.    

### Tanılar (Tanilar)
- Toplam **285 farklı tanı kategorisi** bulundu.  
- En sık görülenler: **Dorsalji, Diğer, Tanımlanmamış, Omuzun darbe sendromu, İntervertebral disk bozuklukları**.  
- Veri girişinde yazım farklılıkları görülmektedir (ör. Voltaren / Volteren). Bu durum preprocessing aşamasında standardize edilmelidir. 

---
## 8. Ikili Degisken Analizi

### 8.1. Sayısal – Sayısal Analiz

Üç sayısal değişken (Yaş, Tedavi Süresi, Uygulama Süresi) arasındaki ilişkiler incelenmiştir.  

- **Yaş ile Tedavi Süresi** arasında anlamlı bir korelasyon gözlenmemiştir (r ≈ -0.01). Scatter plot da tüm yaş gruplarında tedavi sürelerinin benzer şekilde dağıldığını göstermektedir.  
- **Yaş ile Uygulama Süresi** arasında ilişki bulunmamaktadır (r ≈ 0.00). Uygulama süresi genelde 20 dakika civarında toplanmaktadır.  
- **Tedavi Süresi ile Uygulama Süresi** arasında çok zayıf pozitif bir ilişki vardır (r ≈ 0.08). Ancak scatter plot incelendiğinde bu ilişkinin belirgin olmadığı, uygulama süresinin çoğunlukla standart sürelerde sabit kaldığı görülmektedir.  

- **Cikarim:** Sayısal değişkenler arasında güçlü bir ilişki bulunmamaktadır. 


### 8.2. Sayısal – Kategorik Analiz

Bu aşamada sayısal değişkenlerin (Yaş, Tedavi Süresi, Uygulama Süresi) belirli kategorik değişkenlere göre dağılımları incelenmiştir.  

#### Cinsiyet
- **Cinsiyet ile Uygulama Süresi** arasında belirgin bir farklılık gözlenmemiştir.  
- Kadın ve erkek hastalarda uygulama süreleri genellikle **20 dakika civarında yoğunlaşmış**, dağılımlar büyük ölçüde benzerdir.    

#### Bölüm
- **Bölüm ile Yaş**:  
  - Bölümlere göre yaş dağılımında farklılıklar gözlenmiştir.  
  - **İç Hastalıkları, Kardiyoloji, Onkoloji** bölümlerinde yaş ortalamaları daha yüksektir.  
  - **Ortopedi, Travmatoloji, Fiziksel Tıp ve Rehabilitasyon** bölümlerinde ise daha genç ve orta yaşlı hasta yoğunluğu vardır.  
  - Bu sonuç, bölümlerin farklı yaş gruplarına hizmet verdiğini göstermektedir.  

- **Bölüm ile Tedavi Süresi**:  
  - Tedavi süreleri bölüm bazında anlamlı şekilde değişmektedir.  
  - **Fiziksel Tıp ve Rehabilitasyon** bölümünde daha uzun süreli tedaviler uygulanırken,  
  - **İç Hastalıkları** bölümünde tedavi süreleri daha kısa kalmaktadır.  
  - Diğer bölümlerde tedavi süreleri orta seviyelerde yoğunlaşmıştır.  

**Genel Sonuç:** Bölüm, hem yaş hem de tedavi süresi üzerinde anlamlı farklılıklar göstermektedir. Buna karşın cinsiyet değişkeni sayısal değişkenler üzerinde belirgin bir etkiye sahip değildir.


### 8.3. Kategorik – Kategorik Analiz  

Bu bölümde iki kategorik değişken arasındaki ilişkiler incelenmiştir.  

- **Cinsiyet ve Bölüm Dağılımı**  
  - Hem kadın hem erkek hastaların büyük çoğunluğu **Fiziksel Tıp ve Rehabilitasyon / Solunum Merkezi** bölümünde tedavi almıştır.  
  - Kadınlarda bu oran erkeklere kıyasla daha yüksektir.  
  - Diğer bölümlerde (ör. Ortopedi, Nöroloji, İç Hastalıkları) cinsiyetler arasında belirgin bir farklılık yoktur.  

- **Cinsiyet ve Uygulama Yerleri**  
  - Uygulama bölgeleri açısından kadın ve erkek dağılımı benzerdir; en sık uygulama alanları **Bel, Boyun ve Diz** bölgeleridir.  
  - Kadınlarda Bel ve Boyun uygulamaları, erkeklerde ise Diz uygulamaları nispeten daha belirgindir.
  - Çok nadir uygulama bölgeleri her iki cinsiyette de sınırlı sayıda görülmüştür.  

- **Bölüm ve Uygulama Yerleri**  
  - **Fiziksel Tıp ve Rehabilitasyon / Solunum Merkezi** bölümü, uygulamaların büyük kısmını kapsamakta ve özellikle **Bel, Boyun ve Diz** bölgelerine yoğunlaşmaktadır.  
  - Ortopedi ve Travmatoloji bölümünde **Tüm Vücut Bölgesi** ve ekstremite uygulamaları öne çıkmaktadır.  
  - Diğer bölümlerde (ör. Göğüs Hastalıkları, Kardiyoloji, Onkoloji) uygulama sayıları oldukça düşüktür ve belirli bölgelere odaklanmıştır.  

**Genel Sonuç:**  
Cinsiyet – Bölüm ve Cinsiyet – Uygulama Yeri ilişkilerinde belirgin farklılıklar bulunmasa da, **Fiziksel Tıp ve Rehabilitasyon / Solunum Merkezi** bölümü ve **Bel, Boyun, Diz** bölgeleri öne çıkmaktadır. Bu durum, hastaların tedavi profillerinde bazı bölümlerin ve uygulama alanlarının baskın olduğunu göstermektedir.  
