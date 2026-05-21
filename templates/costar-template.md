# COSTAR Template

Genel amaçlı prompt iskelet. Aşağıdaki XML'i kopyala, `{{...}}` yerlerini doldur.

```xml
<context>
{{Görevle ilgili arka plan. Hangi şirket/proje/durum?
 Modelin bilmesi gereken zorunlu bilgi.}}
</context>

<objective>
{{Net ve tek bir görev. Belirsiz olmayan eylem fiili kullan
 (özetle, listele, karşılaştır, oluştur).}}
</objective>

<style>
{{Hangi yazar/uzman tarzında? Örn: "McKinsey raporu",
 "Hemingway sade üslubu", "Akademik makale".}}
</style>

<tone>
{{Profesyonel | empatik | mizahi | ikna edici | nötr |
 eyleme yönelik | analitik}}
</tone>

<audience>
{{Çıktıyı kim okuyacak? Bilgi seviyesi, ilgi alanı.
 Örn: "Teknik olmayan C-level yöneticiler".}}
</audience>

<response_format>
{{Çıktının kesin formatı:
 - Markdown / JSON / düz metin?
 - Max kelime/satır sayısı?
 - Başlık yapısı?
 - Madde işaretleri?}}
</response_format>

<constraints>
{{İstemediğin şeyler:
 - Kişisel görüş ekleme
 - Belirli kelimeler kullanma
 - {{X}} konusuna girme}}
</constraints>

<examples>
{{2-3 örnek giriş ve çıktı. Format taklidi için kritik.
 İlk seferde çalışmıyorsa örnek sayısını artır.}}
</examples>

ÖNEMLİ HATIRLATMA (recency için sona):
- {{En kritik kuralı tekrar yaz}}
- {{Eğer X koşulu yoksa Y yap}}
```

## Doldurulmuş Örnek

```xml
<context>
SaaS şirketimiz son ay 47 müşteri kaybetti. CSV'de churn nedenleri
ve kullanım metrikleri var.
</context>

<objective>
Churn'ün top 3 nedenini ve her biri için somut bir geri kazanım
aksiyonu öner.
</objective>

<style>McKinsey raporu, veri-odaklı, kanıtlanabilir iddialar</style>
<tone>Profesyonel, eyleme yönelik</tone>
<audience>CEO ve CPO, teknik detay istemiyorlar</audience>

<response_format>
Markdown:
## Neden 1: [başlık]
- %X churn payı | Gözlem | Aksiyon (2 cümle)

## Neden 2: ...

Max 400 kelime.
</response_format>

<constraints>
- "Belki", "muhtemelen" gibi belirsiz ifade kullanma
- Veri olmayan iddialar yapma
</constraints>

<examples>
<example>
İyi: "Onboarding tamamlama oranı %35 → personalized welcome flow ile %60 hedef"
Kötü: "Belki onboarding sorunlu olabilir"
</example>
</examples>

HATIRLATMA: Her iddianı CSV'deki spesifik metriğe bağla.
```
