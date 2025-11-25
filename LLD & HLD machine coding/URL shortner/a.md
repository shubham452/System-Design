Here is a simple, modular Low-Level Design (LLD) for a URL Shortener in Java.

This design focuses on **Separation of Concerns**:

1.  **Model:** Holds the data.
2.  **Strategy:** Handles unique key generation.
3.  **Service:** Handles business logic (storage, expiration, retrieval).

### High-Level Flow

-----

### The Java Code

#### 1\. The Model (Data Class)

We need a class to store the original URL and its expiration timestamp.

```java
import java.time.LocalDateTime;

class UrlMapping {
    private String longUrl;
    private LocalDateTime expiryTime;

    public UrlMapping(String longUrl, LocalDateTime expiryTime) {
        this.longUrl = longUrl;
        this.expiryTime = expiryTime;
    }

    public String getLongUrl() {
        return longUrl;
    }

    public boolean isExpired() {
        return LocalDateTime.now().isAfter(expiryTime);
    }
}
```

#### 2\. The Strategy (Key Generator)

To keep the code modular, we abstract *how* the key is generated. This allows us to change the algorithm later (e.g., from Random to Base62) without breaking the rest of the code.

```java
import java.util.Random;

class KeyGenerator {
    private static final String ALLOWED_CHARS = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    private static final int KEY_LENGTH = 6;
    private Random random = new Random();

    public String generateKey() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < KEY_LENGTH; i++) {
            int index = random.nextInt(ALLOWED_CHARS.length());
            sb.append(ALLOWED_CHARS.charAt(index));
        }
        return sb.toString();
    }
}
```

#### 3\. The Service (Business Logic)

This handles storing the URL, checking uniqueness, and validating expiration. We use `ConcurrentHashMap` to make it thread-safe.

```java
import java.time.LocalDateTime;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

class UrlShortenerService {
    private static final String DOMAIN = "http://tiny.url/";
    private Map<String, UrlMapping> urlMap;
    private KeyGenerator keyGenerator;

    public UrlShortenerService() {
        // Thread-safe storage
        this.urlMap = new ConcurrentHashMap<>();
        this.keyGenerator = new KeyGenerator();
    }

    // 1. Generate Short URL
    public String shortenUrl(String longUrl, int ttlSeconds) {
        String key = keyGenerator.generateKey();
        
        // Collision handling: If key exists, generate a new one
        while (urlMap.containsKey(key)) {
            key = keyGenerator.generateKey();
        }

        LocalDateTime expiry = LocalDateTime.now().plusSeconds(ttlSeconds);
        urlMap.put(key, new UrlMapping(longUrl, expiry));
        
        return DOMAIN + key;
    }

    // 2. Redirect (Get Original URL)
    public String getOriginalUrl(String shortUrl) {
        String key = extractKey(shortUrl);
        
        if (!urlMap.containsKey(key)) {
            return "Error: URL not found.";
        }

        UrlMapping mapping = urlMap.get(key);

        // 3. Check Expiration
        if (mapping.isExpired()) {
            urlMap.remove(key); // Clean up
            return "Error: URL has expired.";
        }

        return mapping.getLongUrl();
    }

    // Helper to parse the key from the full URL
    private String extractKey(String shortUrl) {
        return shortUrl.replace(DOMAIN, "");
    }
}
```

#### 4\. The Driver (Main Class)

Simulating the user interaction.

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        UrlShortenerService service = new UrlShortenerService();

        String longUrl = "https://www.google.com/search?q=system+design+interview";
        
        // 1. Create a short link with 2 seconds expiration
        System.out.println("--- Creating Link ---");
        String shortUrl = service.shortenUrl(longUrl, 2);
        System.out.println("Short URL: " + shortUrl);

        // 2. Access immediately (Should work)
        System.out.println("\n--- Accessing Link Immediately ---");
        System.out.println("Redirecting to: " + service.getOriginalUrl(shortUrl));

        // 3. Wait for 3 seconds to simulate expiration
        System.out.println("\n--- Waiting 3 seconds... ---");
        Thread.sleep(3000);

        // 4. Access after expiration (Should fail)
        System.out.println("--- Accessing Link After Expiry ---");
        System.out.println("Result: " + service.getOriginalUrl(shortUrl));
    }
}
```

-----

### Why this is good LLD:

1.  **Modular:** The logic for generating keys is separate from the logic for storing data. You can easily swap the `KeyGenerator` with a `Base62Encoder` without touching the Service.
2.  **Clean Entity (`UrlMapping`):** The check for expiration (`isExpired()`) is encapsulated inside the data object, adhering to OOD principles (Tell, Don't Ask).
3.  **Thread Safety:** Uses `ConcurrentHashMap`, making it safe if multiple users try to shorten links simultaneously.
4.  **Lazy Cleanup:** Expiration is checked on access (Passive Expiration). This is simpler than running a background background thread to clean DB rows for a simple interview scope.

**Would you like me to create a JUnit test case for this service to demonstrate testing best practices?**
