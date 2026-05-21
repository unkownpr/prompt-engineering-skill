# Prompt Çerçeveleri (Frameworks)

Sistematik bir yapı kullanmak, modelin performansını önemli ölçüde artırır. İki ana çerçeve:

## A. COSTAR Çerçevesi (Alibaba Qwen)

Eksik bilgi bırakmamak için 6 alan doldurulur:

| Alan | Anlam | Örnek |
|------|-------|-------|
| **C**ontext | Görevle ilgili arka plan bilgisi | "Bir SaaS şirketi için kayıp müşteri analizi yapıyoruz" |
| **O**bjective | Modelin yapması gereken net iş | "Son 30 günde churn eden müşterilerin ortak özelliklerini listele" |
| **S**tyle | Üslup / referans yazar | "McKinsey raporu tarzında, veri-odaklı" |
| **T**one | Duygu tonu | "Profesyonel, eyleme yönelik, suçlayıcı değil" |
| **A**udience | Çıktıyı kim okuyacak | "C-level yöneticiler, teknik olmayan" |
| **R**esponse | Çıktı formatı | "Markdown başlıklı, max 5 madde, her madde 2 cümle" |

**Tam örnek:**

```xml
<context>
SaaS şirketimizin son 30 günde 47 müşteri kaybetti. Veri eklenen
CSV'de churn nedenleri ve kullanım metrikleri var.
</context>

<objective>
Churn'ün ana 3 nedenini ve her biri için somut bir geri kazanım
aksiyonu öner.
</objective>

<style>McKinsey raporu, veri-odaklı, kanıtlanabilir iddialar</style>
<tone>Profesyonel, eyleme yönelik</tone>
<audience>CEO ve CPO, teknik detay istemiyorlar</audience>

<response_format>
- Markdown başlık
- Her neden için: %X churn payı | gözlem | aksiyon
- Max 400 kelime
</response_format>
```

## B. Anthropic (Claude) Prompt Yapısı

Tek seferde doğru sonuç (zero-shot) için bu sıralama:

1. **Görev tanımı** — modelin rolü ve işi
2. **İçerik** — analiz edilecek metin/veri (XML ile sarmalanır)
3. **Detaylı talimatlar** — adım adım nasıl yapılacak
4. **Örnekler (few-shot)** — istenen çıktı formatı için 3-5 örnek
5. **Hatırlatmalar** — en kritik kurallar promptun **sonunda** tekrar (recency bias)

**Tam örnek:**

```xml
Sen kıdemli bir teknik yazarsın. Sana verilen API dokümanını
geliştirici-dostu bir quickstart rehberine çevireceksin.

<api_doc>
{{burada API dokümanı}}
</api_doc>

Adımlar:
1. Önce <thinking> içinde hangi endpoint'lerin quickstart için
   kritik olduğunu belirle.
2. Sonra <answer> içinde maksimum 3 örnek ile rehberi yaz.
3. Her örnekte curl + Python + JavaScript göster.

<example>
<input>POST /users API</input>
<output>
## Kullanıcı oluştur

`curl -X POST ...`
`requests.post(...)`
`fetch(...)`
</output>
</example>

ÖNEMLİ HATIRLATMA:
- API'de olmayan bir endpoint'i UYDURMA.
- Eğer doc'ta authentication bilgisi yoksa, "doc'ta belirtilmemiş" yaz.
- Cevabı 500 kelimeyi geçme.
```

## Hangi Framework Ne Zaman?

- **COSTAR** → çıktı stil/ton/audience odaklı görevlerde (pazarlama, içerik, rapor)
- **Anthropic yapısı** → teknik analiz, kod, doküman dönüşümü, RAG cevaplarında
- **İkisi birden** → karmaşık iş akışlarında — COSTAR ile bağlamı tanımla, Anthropic yapısı ile teknik detayı

## Ortak Kurallar (İki Framework İçin de)

- **XML etiketleri** ile bölümleri ayır — model hangi metnin veri, hangisinin komut olduğunu bilir
- Her bölümü tek bir sorumluluk ile sınırla
- "Kritik" kuralları **sonda** tekrar et — modeller son talimatları daha güçlü tutar
- Her promptu en az 2-3 farklı input ile test et
