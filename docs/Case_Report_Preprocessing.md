# Data Science Intern Case Study - Preprocessing Raporu


## 1. Eksik Değer İşlemleri

### 1.1 Sayısal Sütunlar
- **Yas**, **TedaviSuresi** ve **UygulamaSuresi** sütunlarında eksik değer bulunmadığı için herhangi bir işlem yapılmamıştır.


### 1.2 Kategorik Sütunlar
- **Bolum** sütunundaki eksik değerler en sık görülen kategori (mode) ile dolduruldu.  
- **Cinsiyet** sütunundaki eksik değerler en sık görülen kategori (mode) ile dolduruldu.  
- **KanGrubu** sütunundaki eksik değerler `"Bilinmiyor"` kategorisi ile dolduruldu.  
- **UygulamaYerleri** sütunundaki eksik değerler `"Bilinmiyor"` kategorisi ile dolduruldu.  


### 1.3 Multi-label Eksik Değer İşlemleri

- **Tanilar, KronikHastalik, Alerji** sütunlarındaki eksik değerler `"yok"` etiketi ile dolduruldu.  
- Bu yöntem, eksik değerlerin aslında **ilgili alanda herhangi bir kaydın olmadığını** ifade ettiği için tercih edildi.  


## 2. Kategorik Değişkenlerde Temizlik

### 2.1 TedaviAdi
- `TedaviAdi` sütununda yazım farklılıkları, karakter sorunları ve hatalı girişler incelenmiştir.  
- Temizlik sürecinde **yalnızca yeni kategori oluşumuna sebep olan hatalar düzeltilmiştir**.  
  - Örn: `"Eklem Ağrsıı"` → `"Eklem Ağrısı"`.  
  - Anlamsız girdiler (`"Xx"`, `"Deneme"`, `"Onur"`) `"Bilinmiyor"` kategorisine taşınmıştır.  
- **Tıbbi anlam içerebilecek varyantlar (ör. `"Koksartroz"` vs `"Koksartroz1"`, `"Aşil Rüptürü-1"` vs `"Aşil Rüptürü-2"`) korunmuştur**.  
- Bu yaklaşım, hem kategori sayısını yapay olarak şişiren hataları önlemiş hem de verinin tıbbi doğruluğunu korumuştur.  

Sonuç: Temizlik sonrası **229 benzersiz kategori** elde edilmiştir. Bu sayı, hatalı girişler düzeltilerek daha gerçekçi bir seviyeye çekilmiştir.  

### 2.2 UygulamaYerleri
- `UygulamaYerleri` sütununda da aynı temizlik işlemleri uygulanmıştır.  

### 2.3 Diğer Kategorik Değişkenler

- **Cinsiyet, KanGrubu, Uyruk ve Bolum** sütunları incelendi.  
- Bu sütunlarda ciddi bir yazım veya format hatası bulunmamıştır.  
- Mevcut değerler olduğu gibi korunmuş, ek bir temizlik işlemi uygulanmamıştır.


## 3. Multi-label Sütunların Hazırlanması

- **Tanilar, KronikHastalik, Alerji** sütunları virgülle ayrılmış değerlerden oluştuğu için liste formatına dönüştürülmüştür.  
  - Örn: `"Astım, Aritmi"` → `["Astım", "Aritmi"]`  
  - Eksik değerler önceden `"yok"` ile doldurulduğu için `"yok"` da liste formatına çevrilmiştir (`["yok"]`).  
- **UygulamaYerleri** sütunu da aynı şekilde virgüllerden ayrılarak liste formatına dönüştürülmüştür.  
  - Örn: `"Sağ Kalça Bölgesi,Sol Kalça Bölgesi"` → `["Sağ Kalça Bölgesi", "Sol Kalça Bölgesi"]`.  
- Bu dönüşümler sayesinde her gözlemde birden fazla etiket doğru şekilde temsil edilebilmekte, ileride **MultiLabelBinarizer** ile encoding yapılmasına uygun hale getirilmiştir.  


## 4. Rare Category Handling Islemi

### 4.1 TedaviAdi
- `TedaviAdi` sütununda başlangıçta **229 farklı kategori** bulunmaktaydı.  
- Frekansı **10’dan az olan kategoriler** `"Nadir"` etiketi altında birleştirilmiştir.  
- Bu işlem sonucunda kategori sayısı **229 → 33** olarak azalmıştır.  
- Böylece modelleme aşamasında nadir görülen tedavi isimlerinin gürültü yaratması engellenmiştir.  

### 4.2 UygulamaYerleri
- `UygulamaYerleri` sütunu başlangıçta **38 farklı kategori** içermekteydi.  
- Frekansı **10’dan az olan kategoriler** `"Nadir"` etiketi altında birleştirilmiştir.  
- Bu işlem sonucunda kategori sayısı **38 → 23** olarak azalmıştır.  
- Böylece seyrek görülen uygulama bölgelerinin modelleme sürecinde gürültü yaratmasının önüne geçilmiştir.  


## 5. Encoding İşlemleri

### 5.1 Kategorik Sütunlar (OneHotEncoder)  
- **Cinsiyet, KanGrubu, Uyruk, Bolum, TedaviAdi** sütunları **OneHotEncoder** ile encode edilmiştir.  
- Böylece her kategori için yeni binary sütunlar oluşturulmuş ve modellemeye uygun hale getirilmiştir.  

### 5.2 Multi-label Sütunlar (MultiLabelBinarizer)  
- **Tanilar, KronikHastalik, Alerji, UygulamaYerleri** sütunları **MultiLabelBinarizer** ile encode edilmiştir.  
- Her bir kategori için ayrı sütun açılmış ve gözlemde kategori mevcutsa **1**, yoksa **0** olarak işaretlenmiştir.  
- Bu sayede çoklu etiket yapısı makine öğrenmesi algoritmalarına uygun hale getirilmiştir.  

---

## 6. Sayısal Sütunların Ölçeklenmesi  

- **Yas, UygulamaSuresi** sütunları **StandardScaler** ile ölçeklenmiştir.  
- Bu işlem sonucunda Ortalama = 0  Standart sapma = 1 olacak şekilde dönüşüm yapılmıştır.  
- Böylece farklı ölçeklerdeki sayısal değişkenler aynı ölçeğe getirilmiştir ve model performansı için uygun hale gelmiştir.  
