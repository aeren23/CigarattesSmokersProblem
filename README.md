# Cigarette Smokers Problem / Sigara İçenler Problemi

[English](#english) | [Türkçe](#turkish)

---

## English

### Overview
This project is a Java implementation of the classic **Cigarette Smokers Problem**, a synchronization problem in concurrent programming. It demonstrates the use of semaphores to solve a coordination challenge between multiple threads.

### The Problem
The Cigarette Smokers Problem involves:
- **One Agent**: Places two random ingredients on a table from {tobacco, paper, matches}
- **Three Smokers**: Each has an infinite supply of one ingredient: 
  - Smoker 1 has tobacco
  - Smoker 2 has paper
  - Smoker 3 has matches

To make and smoke a cigarette, a smoker needs all three ingredients. When the agent places two ingredients on the table, the smoker with the third ingredient can make and smoke a cigarette, then signals the agent to place new ingredients.

### Solution Architecture
This implementation uses **three pusher threads** to solve the problem elegantly: 

1. **Agent Thread**: Randomly selects and places two ingredients on the table
2. **Pusher Threads** (3): Monitor for ingredient combinations
   - `PusherTobacco`: Watches for tobacco on the table
   - `PusherPaper`: Watches for paper on the table
   - `PusherMatch`: Watches for matches on the table
3. **Smoker Threads** (3): Wait for their required ingredients and then smoke
4. **Scoreboard**: Tracks which ingredients are currently waiting on the table
5. **Semaphores**: Used for synchronization between all threads

### Project Structure
```
CigarattesSmokersProblem/
├── Main.java              # Entry point, initializes all threads and semaphores
├── Agent.java             # Agent that places ingredients
├── Smoker.java            # Smoker thread implementation
├── SmokerType.java        # Enum for smoker types
├── PusherTobacco.java     # Pusher for tobacco ingredient
├── PusherPaper.java       # Pusher for paper ingredient
├── PusherMatch.java       # Pusher for matches ingredient
└── Scoreboard.java        # Shared state for ingredient tracking
```

### How It Works
1. The agent acquires permission and places two random ingredients
2. Pusher threads detect the ingredients and update the scoreboard
3. When two ingredients are available, the corresponding smoker is signaled
4. The smoker makes a cigarette, smokes it, and signals the agent
5. The cycle repeats

### Example Walkthrough: Agent Places TOBACCO + PAPER

Let's trace through a complete execution step-by-step:

**Initial State:**
- Agent has permission to place ingredients
- All pushers are waiting for their respective ingredient semaphores
- All smokers are waiting for their signals
- Scoreboard flags: `isPaper = false`, `isMatch = false`, `isTobacco = false`

**Step 1 - Agent places ingredients:**
```
Agent:
  agentSem.acquire()           // Agent gets permission
  tobacco.release()            // Signal tobacco is available
  paper.release()              // Signal paper is available
  [Agent] puts TOBACCO + PAPER
```

**Step 2 - PusherTobacco runs first:**
```
PusherTobacco:
  tobacco.acquire()            // Detects tobacco
  mutex.acquire()              // Lock scoreboard
  Check: isPaper? false, isMatch? false
  isTobacco = true             // Mark tobacco as present
  mutex.release()              // Unlock scoreboard
  // No smoker signaled yet (waiting for second ingredient)
```

**Step 3 - PusherPaper runs:**
```
PusherPaper:
  paper.acquire()              // Detects paper
  mutex.acquire()              // Lock scoreboard
  Check: isTobacco == true     // Found tobacco already on table!
  isTobacco = false            // Reset the flag
  matchSmoker.release()        ✅ // Signal the Match smoker (who needs tobacco + paper)
  mutex.release()              // Unlock scoreboard
```

**Step 4 - Smoker-Match wakes up:**
```
Smoker-Match:
  matchSmoker.acquire()        // Receives signal
  [Smoker-Match] makes cigarette and smokes 🚬
  agentSem.release()           ✅ // Signal agent to place new ingredients
```

**Final State:**
- Agent can now start a new round
- Scoreboard is reset
- No deadlock occurred!

**Why This Works:**
- The two pushers coordinate through the shared scoreboard
- When the second pusher detects its ingredient, it sees the first ingredient's flag and knows which smoker to wake up
- The mutex prevents race conditions on the scoreboard
- Each smoker signals the agent when done, maintaining the cycle

### Running the Program
```bash
# Compile
javac *.java

# Run
java Main
```

### Example Output
```
[Agent] puts TOBACCO + PAPER
[Smoker-Match] makes cigarette and smokes
[Agent] puts TOBACCO + MATCH
[Smoker-Paper] makes cigarette and smokes
[Agent] puts PAPER + MATCH
[Smoker-Tobacco] makes cigarette and smokes
```

### Technologies
- Java
- Concurrent Programming with Semaphores
- Multi-threading

---

## Turkish

### Genel Bakış
Bu proje, eşzamanlı programlamada klasik bir senkronizasyon problemi olan **Sigara İçenler Problemi**'nin Java implementasyonudur. Birden fazla thread arasındaki koordinasyon zorluğunu semaforlar kullanarak çözmeyi gösterir.

### Problem
Sigara İçenler Problemi şunları içerir:
- **Bir Temsilci (Agent)**: Masaya {tütün, kağıt, kibrit} setinden rastgele iki malzeme koyar
- **Üç İçici (Smoker)**: Her birinin bir malzemeden sonsuz arzı vardır:
  - İçici 1'in tütünü var
  - İçici 2'nin kağıdı var
  - İçici 3'ün kibriti var

Sigara yapmak ve içmek için, bir içicinin üç malzemeye de ihtiyacı vardır. Temsilci masaya iki malzeme koyduğunda, üçüncü malzemeye sahip olan içici sigara yapıp içebilir, ardından temsilciye yeni malzemeler koyması için sinyal gönderir.

### Çözüm Mimarisi
Bu implementasyon, problemi zarif bir şekilde çözmek için **üç pusher thread** kullanır:

1. **Agent Thread**: Rastgele iki malzeme seçer ve masaya koyar
2. **Pusher Thread'leri** (3): Malzeme kombinasyonlarını izler
   - `PusherTobacco`: Masadaki tütünü izler
   - `PusherPaper`: Masadaki kağıdı izler
   - `PusherMatch`: Masadaki kibriti izler
3. **Smoker Thread'leri** (3): İhtiyaç duydukları malzemeleri bekler ve sonra içerler
4. **Scoreboard**: Masada şu anda bekleyen malzemeleri takip eder
5. **Semaforlar**: Tüm thread'ler arası senkronizasyon için kullanılır

### Proje Yapısı
```
CigarattesSmokersProblem/
├── Main.java              # Giriş noktası, tüm thread'leri ve semaforları başlatır
├── Agent.java             # Malzeme koyan temsilci
├── Smoker.java            # İçici thread implementasyonu
├── SmokerType.java        # İçici tipleri için enum
├── PusherTobacco.java     # Tütün malzemesi için pusher
├── PusherPaper.java       # Kağıt malzemesi için pusher
├── PusherMatch.java       # Kibrit malzemesi için pusher
└── Scoreboard.java        # Malzeme takibi için paylaşılan durum
```

### Nasıl Çalışır
1. Temsilci izin alır ve rastgele iki malzeme koyar
2. Pusher thread'leri malzemeleri algılar ve scoreboard'u günceller
3. İki malzeme hazır olduğunda, ilgili içiciye sinyal gönderilir
4. İçici sigara yapar, içer ve temsilciye sinyal gönderir
5. Döngü tekrarlanır

### Örnek Yürüyüş: Agent TÜTÜN + KAĞIT Koyuyor

Bir tam yürütmeyi adım adım takip edelim:

**Başlangıç Durumu:**
- Agent malzeme koymak için izne sahip
- Tüm pusher'lar kendi malzeme semaforlarını bekliyor
- Tüm içiciler sinyallerini bekliyor
- Scoreboard bayrakları: `isPaper = false`, `isMatch = false`, `isTobacco = false`

**Adım 1 - Agent malzemeleri koyar:**
```
Agent:
  agentSem.acquire()           // Agent izin alır
  tobacco.release()            // Tütünün hazır olduğunu sinyalle
  paper.release()              // Kağıdın hazır olduğunu sinyalle
  [Agent] puts TOBACCO + PAPER
```

**Adım 2 - PusherTobacco ilk çalışır:**
```
PusherTobacco:
  tobacco.acquire()            // Tütünü algılar
  mutex.acquire()              // Scoreboard'u kilitle
  Kontrol: isPaper? false, isMatch? false
  isTobacco = true             // Tütünün mevcut olduğunu işaretle
  mutex.release()              // Scoreboard kilidini aç
  // Henüz içici sinyallenmedi (ikinci malzeme bekleniyor)
```

**Adım 3 - PusherPaper çalışır:**
```
PusherPaper:
  paper.acquire()              // Kağıdı algılar
  mutex.acquire()              // Scoreboard'u kilitle
  Kontrol: isTobacco == true   // Masada tütün zaten var!
  isTobacco = false            // Bayrağı sıfırla
  matchSmoker.release()        ✅ // Kibrit içicisini sinyalle (tütün + kağıda ihtiyacı var)
  mutex.release()              // Scoreboard kilidini aç
```

**Adım 4 - Smoker-Match uyanır:**
```
Smoker-Match:
  matchSmoker.acquire()        // Sinyali alır
  [Smoker-Match] makes cigarette and smokes 🚬
  agentSem.release()           ✅ // Agent'a yeni malzeme koyması için sinyal gönder
```

**Son Durum:**
- Agent artık yeni bir tur başlatabilir
- Scoreboard sıfırlandı
- Deadlock oluşmadı!

**Neden Çalışır:**
- İki pusher paylaşılan scoreboard üzerinden koordine olur
- İkinci pusher malzemesini algıladığında, birinci malzemenin bayrağını görür ve hangi içiciyi uyandıracağını bilir
- Mutex, scoreboard üzerinde yarış koşullarını önler
- Her içici bittiğinde agent'a sinyal göndererek döngüyü sürdürür

### Programı Çalıştırma
```bash
# Derleme
javac *.java

# Çalıştırma
java Main
```

### Örnek Çıktı
```
[Agent] puts TOBACCO + PAPER
[Smoker-Match] makes cigarette and smokes
[Agent] puts TOBACCO + MATCH
[Smoker-Paper] makes cigarette and smokes
[Agent] puts PAPER + MATCH
[Smoker-Tobacco] makes cigarette and smokes
```

### Teknolojiler
- Java
- Semaforlar ile Eşzamanlı Programlama
- Çoklu Thread Programlama

---

## License / Lisans
This project is open source and available for educational purposes.

Bu proje açık kaynaklıdır ve eğitim amaçlı kullanılabilir.