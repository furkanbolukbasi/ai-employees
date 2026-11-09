# TalentForge AI - HR Agency (ai-employees reposu ile)

Repodaki 8 hazır çalışan kiti (`employees/`) HR ajansının 8 rolüne eşlendi. Repoda HR'a özel kit yok; kitler değiştirilmedi (guardrail: kit dosyalarına elle dokunulmaz), HR bağlamı kurulum sırasında verilir ve köprü `bridge/` altında ayrı durur.

| # | Repo çalışanı (klasör) | HR rolü | HR'daki işi |
|---|---|---|---|
| 1 | Chief of Staff (`chief-of-staff`) | Proje Yöneticisi | 7 çalışanı denetler, durmuş rutinleri adlandırır, günlük brief yazar |
| 2 | SEO/AEO Employee (`seo-employee`) | Lead Researcher | İş unvanı anahtar kelimeleri, kariyer sayfası makaleleri, AI yanıtlarında işveren görünürlüğü |
| 3 | Social Media Employee (`social-media-employee`) | Content Writer | İş ilanı ve işveren markası paylaşımları (yayın üyenin tıklamasına kadar bekler) |
| 4 | Ad Manager Employee (`ad-manager-employee`) | Campaign Builder | İş ilanı reklam setleri ve build sheet (para onaysız hareket etmez) |
| 5 | Web Dev Employee (`web-dev-employee`) | Technical Auditor | Kariyer sitesi sağlığı, bozuk başvuru formları, hata triyajı |
| 6 | Customer Satisfaction Employee (`customer-satisfaction-employee`) | Support Triage | İK gelen kutusu taraması, aday yanıtları, aday kaybı riski |
| 7 | Sales Employee (`sales-employee`) | Outbound Strategist | Aday arama (sourcing), ilk temas taslakları, takip |
| 8 | GTM Engineer (`gtm-engineer`) | Performance Analyst | İşe alım kampanyası panosu, haftalık skor tablosu, darboğaz işaretleri |

## 1. Kurulum (yapıldı)

```
node installer/cli.mjs hire <slug> --to employees/<slug>
```
8 çalışan için çalıştırıldı. Her klasörde installer'ın ürettiği gerçek `.installed.json` var (dosya özetleri ile). Kendi makinenizde tekrar etmek için repoyu klonlayıp aynı komutu 8 slug ile çalıştırın (klasör OneDrive/Dropbox/Google Drive dışında olmalı).

## 2. Her çalışanı etkinleştirme

Her klasörde ajanınızı (Claude Code) açıp şunu deyin; HR bağlamını ekleyin:

> Install the <Employee> from this folder. Our business is TalentForge AI, an HR / recruiting agency. Treat "customers" as candidates and hiring managers, "leads" as candidates, "products" as open roles. Role focus: <tablodaki HR'daki işi>.

Kurulum kendi rutinlerini kendisi zamanlar. Önce Chief of Staff'ı kurun (diğerlerini `fleet.json` ile görür).

## 3. Proje Yöneticisi köprüsü (Sheet + e-posta)

1. Google Sheet açın, Extensions > Apps Script, `bridge/apps-script/Code.gs` içeriğini yapıştırın.
2. Project Settings > Script properties: `TOKEN` = uzun rastgele bir metin.
3. Deploy > New deployment > Web app, Execute as: Me, Access: Anyone. `/exec` URL'sini kopyalayın.
4. Ortam değişkenleri (dosyaya yazmayın):
   - `HR_SHEET_WEBHOOK` = `/exec` URL'si
   - `HR_WEBHOOK_TOKEN` = `TOKEN` değeri
5. Bağlantı testi (sadece kit örnekleriyle): `node bridge/hr-bridge.mjs demo`
6. Günlük zamanlama (çalışanların sabah rutinlerinden sonra):
   - Linux/macOS cron: `30 11 * * 1-5 cd /path/hr-agency-repo && node bridge/hr-bridge.mjs daily`
   - Windows: `schtasks /create /tn HRBridge /tr "node C:\path\bridge\hr-bridge.mjs daily" /sc daily /st 11:30`

`hr-bridge.mjs` her çalışanın `runlog.jsonl` dosyasını okur, yeni kayıtları `[Timestamp | Agent Name | Task Performed | Output Status]` olarak `bridge/ledger.csv` ve Google Sheet'e ekler, sonra Chief of Staff'ın `brief-latest.md` dosyasını özet tablosuyla kendi adresinize e-postalar. Kendi adresinize brief göndermek repo standardında "outbound send" sayılmaz.

## 4. Teslim kanıtları (sizin toplayacağınız)

- Sheet linki ve gelen kutusuna düşen e-posta kopyaları
- Ekran kaydı: `hire` komutları, `--selftest` çıktıları, `hr-bridge.mjs daily`, Sheet'te satırların belirmesi
- GitHub'a `employees/*/.installed.json`, `hr-agency.config.json`, `bridge/` push edilmesi
