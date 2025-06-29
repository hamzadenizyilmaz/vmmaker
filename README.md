# VM Maker | Revolutionary IoT API Solution for ESP32
ESP32 projeleriniz için devrim niteliğinde API çözümümüz çok yakında hizmetinizde!

sequenceDiagram
    participant C as Client
    participant A as API
    participant D as Database
    participant CF as Cloudflare
    
    C->>CF: İstek (HTTPS)
    CF->>CF: DDoS Kontrolü
    CF->>CF: IP Whitelist Kontrolü
    CF->>CF: WAF Kuralları
    CF->>A: İstek İletildi
    
    alt JWT Doğrulama
        A->>A: JWT Çözümleme
        A->>D: Kullanıcı Kontrolü
        D-->>A: Kullanıcı Bilgisi
        A->>A: Yetki Kontrolü
    else API Key
        A->>D: API Key Kontrolü
        D-->>A: Key Bilgisi
        A->>A: Rate Limit Kontrolü
    end
    
    A-->>C: Yanıt
