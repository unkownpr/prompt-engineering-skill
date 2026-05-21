# Chain of Thought Template

Karmaşık reasoning, kod, matematik görevleri için. `<thinking>` + `<answer>` ayrımı.

```xml
Sen {{rol — örn: kıdemli yazılım mimarı}}sın.

<task>
{{Çözülecek problem. Net ve tek cümle.}}
</task>

<context>
{{Gerekli arka plan, mevcut kod, kısıtlar.}}
</context>

<instructions>
Bu problemi şu sırayla çöz:

1. <thinking> etiketleri arasında:
   - Problemin parçalarını listele
   - Hangi yaklaşımları düşündüğünü ve neden seçtiğini açıkla
   - Edge case'leri belirle
   - Olası tuzakları not et
   - Mantığını test et

2. <answer> etiketleri arasında:
   - Sadece nihai cevabı/kodu ver
   - Açıklama ekleme (gerekçe <thinking> içinde kaldı)

3. <self_check> etiketleri arasında:
   - Cevabın test kriterlerini karşılıyor mu?
   - Edge case'ler ele alındı mı?
   - Bir hata varsa <answer> bölümünü düzelt ve yeniden yaz
</instructions>

<examples>
<example>
<input>{{örnek problem}}</input>

<thinking>
{{örnek akıl yürütme — gerçek mantığı göster}}
</thinking>

<answer>
{{örnek cevap}}
</answer>

<self_check>
- Test 1: ✓
- Edge case (boş input): ✓
- Performans: O(n) ✓
</self_check>
</example>
</examples>

HATIRLATMA: <thinking> içinde özgürce düşün, <answer> içinde net ol.
```

## Doldurulmuş Örnek — Kod Görevi

```xml
Sen kıdemli bir Python geliştiricisisin.

<task>
Verilen URL listesinde aynı anda en fazla 5 paralel istek atarak
HTTP status koddunu döndüren bir async fonksiyon yaz.
</task>

<context>
- Python 3.11+
- aiohttp kullan
- Hatalı URL'leri atla, exception fırlatma
- Timeout: 10 saniye
- Çıktı: {url: status_code} dict
</context>

<instructions>
1. <thinking>: yaklaşımı planla
2. <answer>: kodu yaz
3. <self_check>: kodu test kriterlerine karşı kontrol et
</instructions>

HATIRLATMA: aiohttp ClientSession'ı her görev için değil, tek context'te aç.
```

## Ne Zaman CoT Kullanma

- Basit lookup
- Yaratıcı yazım
- Sabit format dönüşümü

CoT pahalı. Sadece reasoning gerekiyorsa kullan.
