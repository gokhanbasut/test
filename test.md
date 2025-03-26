# API PORTAL TEST - Kullanım Kılavuzu
Bu belge, **API PORTAL TEST** Swagger arayüzünü kullanarak servisleri nasıl test edebileceğinizi adım adım açıklar.
---
## 1. Giriş ve Kimlik Doğrulama
API, OAuth2 (Password Flow) kimlik doğrulaması kullanır.
### Token Alma (Authorize İşlemi)
1. Swagger UI ekranında sağ üstte yer alan **"Authorize"** butonuna tıklayın.
2. Aşağıdaki alanları doldurun:
  - **Username**: Kullanıcı adı
  - **Password**: Kullanıcı şifresi
  - **Client ID**: `demo-client-id` *(varsayılan değer)*
  - **Client Secret**: `demo-client-secret` *(varsayılan değer)*
3. `retail-customer` ve `openId` scope'larını işaretleyin.
4. **Authorize** butonuna basın ve ardından pencereyi kapatın.
5. Artık OAuth2 erişim token’ınız alınmış olacak ve her API isteğine otomatik olarak eklenecek.
---
## 2. Servis Listesi ve Kullanımı
### A. `/OpenBankingConsentHHS/ProcessConsentStatus`  
#### (POST) Hesap Bilgisi Rızasının Hazırlanması
**Açıklama:**  
Müşteri tarafından verilen hesap bilgisi paylaşım rızasının başlatılması için kullanılır.
#### Header Bilgileri:
| Header Adı           | Açıklama                                                             | Zorunlu |
|----------------------|----------------------------------------------------------------------|---------|
| `X-Request-ID`       | Çağrıya özgü talep kimliği                                           | Evet    |
| `X-Group-ID`         | İşlem akışına özgü kimlik                                            | Evet    |
| `PSU-Session-ID`     | (Opsiyonel) Oturuma özgü ID                                          | Hayır   |
| `X-ASPSP-Code`       | Hesap hizmeti sağlayıcısı kodu (4 haneli)                            | Evet    |
| `X-TPP-Code`         | Yetkili ödeme hizmeti sağlayıcısı kodu (4 haneli)                    | Evet    |
| `PSU-Auth-Date`      | Müşterinin son oturum açma tarihi (RFC7231 formatında)               | Hayır   |
| `PSU-IP-Address`     | IP adresi (min 7 max 15 karakter)                                    | Hayır   |
| `PSU-IP-Port`        | Port numarası                                                        | Hayır   |
| `PSU-GEO-Location`   | Cihazın konumu (`GEO:enlem,boylam`)                                  | Hayır   |
#### Body Örneği:
```json
{
 "katilimciBlg": { "hhsKod": "8000", "yosKod": "8000" },
 "gkd": { "yetYntm": "Y", "yonAdr": "https://example.com" },
 "kmlk": { "kmlkTur": "K", "kmlkVrs": "77121323400", "ohkTur": "B" },
 "hspBlg": {
   "iznBlg": {
     "iznTur": ["01", "03"],
     "erisimIzniSonTrh": "2021-06-30T06:30:22+03:00"
   }
 }
}
```
---
### B. `/preprod-fora/DigitalServices/AccountService.svc/hesaplar/{accountNumber}`  
#### (GET) Hesap Bilgilerini Getir
**Açıklama:**  
Belirtilen hesap numarasına ait detaylı hesap bilgilerini getirir.
#### Path Parametresi:
- `accountNumber`: Hesap numarası (örneğin: `20221992120`)
#### Zorunlu Header’lar:
| Header Adı     | Varsayılan Değer        |
|----------------|--------------------------|
| `user`         | `EBT\INTERNETUSER`       |
| `CHANNEL`      | `INTERNET`               |
| `branch`       | `2000`                   |
| `Content-Type` | `application/json`       |
| `izinTur`      | `D`                      |
| `Cookie`       | `ASP.NET_SessionId=...`  |
> Bu alanlar Swagger UI’da otomatik olarak gönderilir, kullanıcıdan girişi beklenmez.
---
### C. `/OpenBankingConsentHHS/GetCustomerBalancesByTckn/{tckn}`  
#### (GET) Müşteri Bakiyelerini Getir
**Açıklama:**  
TCKN ile müşteri bakiyelerini getirir.
#### Path Parametresi:
- `tckn`: Müşteriye ait Türkiye Cumhuriyeti Kimlik Numarası
#### Otomatik Gönderilen Header’lar:
- `accept: */*`
- `Cookie: d765907c0cd58c256b4df03b591a1a44=...`
> Bu header’lar arka planda otomatik olarak eklenir. Kullanıcının elle girmesi gerekmez.
---
## 3. Sık Karşılaşılan Hatalar
| HTTP Kod | Anlamı                | Açıklama                                      |
|----------|------------------------|-----------------------------------------------|
| 200      | OK                     | İşlem başarıyla tamamlandı.                   |
| 400      | Bad Request            | Eksik veya hatalı parametre girdiniz.         |
| 401      | Unauthorized           | Geçersiz veya eksik token.                    |
| 500      | Internal Server Error  | Sunucu tarafında beklenmeyen bir hata oluştu. |
---
## 4. Notlar
- `tokenUrl`: `https://nonprod-apisix.burgan.com.tr/ebanking/token`
- Swagger UI ortamında tüm sabit header'lar ve token gönderimi arka planda otomatikleştirilmiştir.
- Token ile erişim sağlandıktan sonra, `Authorize` penceresini kapatmayı unutmayın.
---
Hazırsan Swagger UI'ya girip test etmeye başlayabilirsin!
