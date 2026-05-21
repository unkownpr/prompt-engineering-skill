# Chain of Thought (CoT) Teknikleri

Modelden son cevabı vermeden önce **düşünmesini** istemek mantıksal hataları engeller. Özellikle kod, matematik, çok adımlı analiz için kritik.

## 1. XML Scratchpad ile İzole Etme

Modelin akıl yürütme sürecini nihai çıktıdan ayır:

```xml
Bu problemi şu sırayla çöz:

1. <thinking> etiketleri arasında:
   - Problemin parçalarını listele
   - Hangi yaklaşımı kullanacağını gerekçele
   - Edge case'leri düşün

2. <answer> etiketleri arasında:
   - Sadece nihai cevabı/kodu ver
   - Açıklama ekleme

Problem: {{problem}}
```

**Neden çalışır:** Model, "karalama defteri"ne mantığını döker; gözden geçirmiş olur ve nihai çıktıyı daha temiz verir.

## 2. "Kapsamlı Düşün" (Think Thoroughly)

Adım adım katı plan vermek yerine, modelin kendi mantığını kurmasına izin ver:

```
Bu problemi çözmeden önce **kapsamlı bir şekilde düşün**
(think thoroughly). Hangi yaklaşımın daha iyi olduğunu
tartış, sonra en iyisini uygula.
```

**Ne zaman kullan:** Model zaten güçlü reasoning yetisine sahip (Claude Opus, GPT-4, MiniMax M2). Aşırı kuralcılık modelin doğal mantığını boğar.

## 3. Self-Check (Kendi Kendini Denetleme)

CoT'un sonuna **doğrulama adımı** ekle:

```
Cevabını/kodunu yazdıktan sonra şu kontrolü yap:

<self_check>
- Test kriterlerini karşılıyor mu? [evet/hayır]
- Kod derlenecek mi? [evet/hayır + neden]
- Edge case'ler ele alındı mı? [hangileri]
- Bir hata varsa düzelt ve <answer> bölümünü yeniden yaz.
</self_check>
```

**Kod için zorunlu** — model gözden kaçan hataları kendisi yakalar.

## 4. Few-Shot CoT (Örneklere Düşünce Ekle)

Sadece girdi-çıktı çifti vermek yetmez. Örneğin içinde **mantığı** da göster:

```xml
<example>
<input>FizzBuzz 1-15 arası yaz</input>

<thinking>
- 3'e bölünen: Fizz
- 5'e bölünen: Buzz
- 15'e (3 ve 5) bölünen: FizzBuzz
- Diğer: sayının kendisi
- Sıra önemli: önce 15 kontrolü
</thinking>

<answer>
for i in range(1, 16):
    if i % 15 == 0:
        print("FizzBuzz")
    elif i % 3 == 0:
        print("Fizz")
    elif i % 5 == 0:
        print("Buzz")
    else:
        print(i)
</answer>
</example>
```

Model senin problem çözme **stilini** taklit eder, sadece formatı değil.

## 5. Plan-Act-Reflect (Otonom Ajanlar İçin)

Tool-using ajanlar için CoT döngüsü:

```
Her görev için şu döngüyü uygula:

<plan>
- Hangi tool'u, hangi sırayla kullanacağım?
- Hangi bilgiye ihtiyacım var?
</plan>

<act>
[Tool çağrısı]
</act>

<reflect>
- Sonuç beklediğim gibi mi?
- Yanlışsa, yaklaşımı değiştir.
- Doğruysa, sonraki adıma geç.
</reflect>
```

**Özellikle uygun:** test-fix döngüleri (compile → test → fix → re-test), web scraping, çok adımlı agent görevleri.

## 6. Prompt Chaining (Çok Dosyalı Projeler)

Tek dev prompt yerine ardışık zincir:

```
Adım 1: Mimari taslağı oluştur (high-level blueprint + requirements.txt)
        ↓
Adım 2: Sadece backend (app.py) yaz, Adım 1 çıktısını bağlam olarak ver
        ↓
Adım 3: Sadece frontend yaz, önceki iki çıktıyı bağlam olarak ver
        ↓
Adım 4: Tüm dosyaları model'e geri ver, import/değişken tutarlılığını kontrol ettir
```

**Neden:** Tek devasa prompt'ta model bağlamı kaybeder. Adımlara bölmek tutarlılığı artırır.

## CoT Kullanmama Gereken Durumlar

- Basit lookup ("Türkiye'nin başkenti?") — gereksiz token harcaması
- Yaratıcı yazım (şiir, hikaye) — mantık değil sezgi gerekir
- Sabit format dönüşüm (JSON parse) — kural-bazlı, düşünme gerekmez

CoT pahalıdır (token + latency). **Sadece** karmaşık reasoning gereken yerlerde kullan.
