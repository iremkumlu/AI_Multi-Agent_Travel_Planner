# AI Multi-Agent Travel Planner / Yapay Zeka Çoklu Ajan Seyahat Planlayıcısı

Bu proje, LangGraph kullanılarak oluşturulmuş çoklu ajan mimarisine sahip bir Yapay Zeka seyahat planlama asistanıdır. Kullanıcının doğal dildeki seyahat isteğini (hedef ve tarih belirterek) alır ve çeşitli API'lardan bilgi toplayarak kapsamlı, kişiselleştirilmiş bir seyahat planı raporu oluşturur.

## Özellikler

- **Doğal Dil Anlama:** Kullanıcının "gelecek cumartesi Paris" veya "3 gün sonra roma" gibi isteklerini anlar.
- **Otomatik Bilgi Çıkarma:** Google Gemini kullanarak istekten hedef şehri, ülkeyi ve seyahat tarihini çıkarır ve hesaplar.
- **Çoklu Bilgi Kaynağı:**
  - **Şehir Bilgisi:** Wikipedia API'si ile şehir hakkında özet bilgi alır.
  - **Otel Arama:** Tavily Search API ile web'de otel ve konaklama seçenekleri arar.
  - **Hava Durumu:** OpenWeatherMap API ile belirtilen tarih için hava durumu tahminini alır (API limitleri dahilinde).
  - **Döviz Kuru:** ExchangeRate-API (`v6.exchangerate-api.com`) kullanarak **kod içinde tanımlı belirli şehir/ülkeler** için yerel para birimini tespit eder ve TRY karşılığını hesaplar. (Tanımlı olmayan yerler için varsayılan olarak EUR kullanılır.)
- **Akıllı Raporlama:** Google Gemini kullanarak toplanan tüm bilgileri sentezler ve aşağıdaki bölümleri içeren detaylı bir rapor oluşturur:
  - Destinasyon Özeti
  - Konaklama Önerileri (Web arama sonuçlarına göre)
  - Hava Durumu ve Kıyafet Seçimi
  - Bütçe Planlama ve Döviz Kuru Yorumu
  - Gezilecek Yer Önerileri
  - Yerel İpuçları (Kültür, Ulaşım, Lezzetler)
- **Hata Yönetimi:** API hataları veya veri eksikliği durumunda yedek mekanizmalar (mock veri, genel tavsiye) kullanır ve sorunları raporda belirtir.
- **Dosyaya Kaydetme:** Oluşturulan raporu `.txt` dosyası olarak kaydetme seçeneği sunar.

## Kullanılan Teknolojiler ve API'ler

- **Programlama Dili:** Python 3
- **Orkestrasyon:** LangChain & LangGraph
- **Büyük Dil Modelleri (LLM):** Google Gemini API (gemini-1.5-flash, gemini-1.5-pro)
- **Web Arama (Otel):** Tavily Search API
- **Hava Durumu:** OpenWeatherMap API
- **Şehir Bilgisi:** Wikipedia API
- **Döviz Kuru:** ExchangeRate-API (`v6.exchangerate-api.com`)
- **Diğer Kütüphaneler:** `requests`, `python-dateutil`, `google-generativeai`, `langchain-google-genai` vb.

## Kurulum ve Başlatma

1.  **Gerekli Kütüphaneleri Yükleyin:**

    ```bash
    pip install langgraph langchain langchain-google-genai google-ai-generativelanguage tavily-python requests python-dateutil wikipedia-api
    ```

2.  **API Anahtarlarını Ayarlayın:**
    Bu proje çalışmak için birkaç API anahtarına ihtiyaç duyar. Bu anahtarları **güvenli bir şekilde** ayarlamanız gerekir.

    Aşağıdaki ortam değişkenlerini ayarlayın:

    - `GOOGLE_API_KEY`: Google Cloud veya Google AI Studio'dan aldığınız Gemini API anahtarınız. ([Google AI Studio](https://aistudio.google.com/app/apikey))
    - `TAVILY_API_KEY`: Tavily AI'dan aldığınız API anahtarınız. ([Tavily AI](https://tavily.com/))
    - `OPENWEATHERMAP_API_KEY`: OpenWeatherMap'ten aldığınız API anahtarınız. ([OpenWeatherMap](https://openweathermap.org/appid))
    - `EXCHANGERATE_API_KEY`: **ExchangeRate-API.com**'dan aldığınız API anahtarınız. ([ExchangeRate-API](https://www.exchangerate-api.com/))

    **Ortam Değişkenlerini Nasıl Ayarlarsınız?**

    - **Linux/macOS:** Terminalde `export ANAHTAR_ADI="anahtar_degeri"` komutunu kullanın veya `.bashrc`, `.zshrc` gibi profil dosyalarınıza ekleyin.
    - **Windows:** Sistem Özellikleri -> Gelişmiş -> Ortam Değişkenleri bölümünden ekleyin veya Komut İstemi'nde `set ANAHTAR_ADI=anahtar_degeri` komutunu kullanın (sadece o oturum için geçerli olur).
    - **Python Betiği İçinde (Sadece Test İçin, Güvenli Değil!):** Kodun başındaki `os.environ[...] = "..."` satırlarını kendi anahtarlarınızla güncelleyebilirsiniz, ancak bu **güvenlik riski** taşır.
    - **.env Dosyası (Önerilen Yöntem):** `python-dotenv` kütüphanesini yükleyip (`pip install python-dotenv`), proje dizininde `.env` adında bir dosya oluşturun ve içine `ANAHTAR_ADI=anahtar_degeri` formatında anahtarlarınızı yazın. Kodun başına `from dotenv import load_dotenv` ve `load_dotenv()` ekleyerek bu değişkenleri yükleyebilirsiniz.

3.  **Kodu Çalıştırın:**
    ```bash
    python AI_Multi-Agent_Travel_Planner.py
    ```
    _(Script dosyanızın adını `AI_Multi-Agent_Travel_Planner.ipynb.py` yerine yazın.)_

## Kullanım

1.  Betik çalıştırıldığında sizden seyahat planınızı doğal dilde girmeniz istenecektir.
    - Örnek: `önümüzdeki hafta sonu Berlin`, `üç gün sonrası için Barselona planı`, `5 gün sonra Londra`
2.  Asistan, isteğinizi işleyecek, gerekli bilgileri API'lerden toplayacak ve LLM'leri kullanarak adımları gerçekleştirecektir. Konsolda adımların ilerleyişini görebilirsiniz.
3.  İşlem tamamlandığında, detaylı seyahat planı raporu ekrana yazdırılacaktır.
4.  Son olarak, raporu bir `.txt` dosyasına kaydedip kaydetmek istemediğiniz sorulacaktır. `e` (evet) veya `h` (hayır) ile yanıt verebilirsiniz.

## Notlar ve Olası İyileştirmeler

- API anahtarlarınızın geçerli ve ilgili servislerde (özellikle Google Gemini API) etkinleştirilmiş olduğundan emin olun.
- API'lerin kullanım limitleri olabilir (özellikle ücretsiz planlarda). Yoğun kullanımda hatalar alınabilir.
- Hava durumu tahmini genellikle sadece yakın tarihler (örn. 5 gün) için API'lerden alınabilir. Daha ileriki tarihler için genel mevsimsel bilgi veya mock veri kullanılır.
- **Döviz Kuru Hesaplaması:** Döviz kuru özelliği, kod içindeki `country_currency_map` sözlüğünde **tanımlı olan şehirler ve ülkeler** için doğru yerel para birimini bulur. Eğer gidilecek yer bu sözlükte yoksa, varsayılan olarak **EUR** kuru kullanılır. Daha fazla yer için doğru kur hesaplaması isteniyorsa, bu sözlüğün genişletilmesi gerekir. Mevcut durumda ExchangeRate-API kullanılmaktadır.
- Otel arama sonuçları Tavily'nin web arama yeteneklerine bağlıdır ve her zaman spesifik otel detaylarını (fiyat, yıldız vb.) yapısal olarak döndürmeyebilir. LLM bu sonuçları yorumlar.
- Otel, hava durumu ve döviz için alternatif (belki daha spesifik veya ücretli) API'ler entegre edilebilir.
