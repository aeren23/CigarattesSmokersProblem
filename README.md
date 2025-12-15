# 🚬 Cigarette Smokers Problem - Concurrent Programming Implementation

[🇹🇷 Türkçe](#turkish) | [🇬🇧 English](#english)

---

<a name="english"></a>
## 🇬🇧 English

### 📖 About The Project

This repository demonstrates a classic concurrency problem in computer science: **The Cigarette Smokers Problem**. The project implements two different approaches to showcase the importance of proper synchronization mechanisms in concurrent programming.

### 🎯 The Problem

There are three smokers and one agent: 
- **Smoker 1** has infinite tobacco
- **Smoker 2** has infinite paper
- **Smoker 3** has infinite matches

The agent randomly places two different ingredients on the table.  The smoker who has the third ingredient should pick up the other two, make a cigarette, and smoke it.  When done, the agent can place new ingredients.

### 🏗️ Project Structure

```
CigarattesSmokersProblem/
├── parnas-solution/          # ✅ Correct solution using Parnas Pushers
│   ├── Main.java
│   ├── Agent.java
│   ├── Smoker.java
│   ├── SmokerType.java
│   ├── Scoreboard.java
│   ├── PusherTobacco.java
│   ├── PusherPaper. java
│   ├── PusherMatch.java
│   └── README.md
│
└── deadlock-solution/        # ❌ Naive solution demonstrating deadlock
    ├── Main.java
    ├── Agent.java
    ├── Smoker.java
    └── README.md
```

### 🔴 Deadlock Solution (Naive Approach)

**Location:** `deadlock-solution/`

This implementation demonstrates what **NOT** to do.  It shows a common mistake where each smoker tries to acquire ingredients separately. 

#### Why It Fails:
1. Agent puts **TOBACCO + PAPER** on the table
2. **Smoker-Tobacco** grabs the paper and waits for matches
3. **Smoker-Match** grabs the tobacco and waits for paper
4. **Deadlock! ** Both smokers wait forever, agent cannot proceed

#### Run It:
```bash
cd deadlock-solution
javac *.java
java Main
```

You'll observe the system getting stuck after a few iterations - this demonstrates the deadlock scenario.

---

### ✅ Parnas Pushers Solution (Correct Approach)

**Location:** `parnas-solution/`

This implementation uses the **Parnas Pushers pattern** to prevent deadlock and race conditions.

#### Key Components: 

1. **Agent**:  Places two random ingredients on the table
2. **Pushers** (3 threads): Each monitors one ingredient
   - `PusherTobacco`
   - `PusherPaper`
   - `PusherMatch`
3. **Scoreboard**: Shared state protected by mutex, tracks which ingredients are on the table
4. **Smokers** (3 threads): Each has one ingredient and waits for the other two

#### How It Works: 

1. **Agent** acquires `agentSem` and places two ingredients (e.g., TOBACCO + PAPER)
2. **First pusher** (e.g., `PusherTobacco`) sees tobacco: 
   - Acquires `mutex`
   - Marks tobacco on scoreboard
   - Releases `mutex`
3. **Second pusher** (e.g., `PusherPaper`) sees paper:
   - Acquires `mutex`
   - Sees tobacco is already on scoreboard
   - Determines MATCH smoker should wake up
   - Signals `matchSmoker` semaphore
   - Clears scoreboard
   - Releases `mutex`
4. **Smoker-Match** wakes up: 
   - Makes and smokes cigarette
   - Releases `agentSem` to allow agent to continue
5. **Cycle repeats** without deadlock! 

#### Run It:
```bash
cd parnas-solution
javac *. java
java Main
```

The system runs indefinitely without deadlock, with proper synchronization. 

---

### 🔧 Technologies

- **Language:** Java
- **Concurrency Primitives:** 
  - `java.util.concurrent.Semaphore`
  - `Thread`
  - Mutex pattern

### 📚 Learning Objectives

- Understanding classic concurrency problems
- Identifying deadlock scenarios
- Implementing proper synchronization
- Using semaphores and mutex for thread coordination
- Parnas solution pattern for complex synchronization

### 🎓 Use Cases

- Computer Science education
- Operating Systems courses
- Concurrent Programming demonstrations
- Interview preparation

### 📝 License

This project is open source and available for educational purposes.

---

<a name="turkish"></a>
## 🇹🇷 Türkçe

### 📖 Proje Hakkında

Bu depo, bilgisayar bilimlerinde klasik bir eşzamanlılık problemini göstermektedir: **Sigara İçenler Problemi**. Proje, eşzamanlı programlamada doğru senkronizasyon mekanizmalarının önemini göstermek için iki farklı yaklaşım uygular.

### 🎯 Problem

Üç sigara içen ve bir ajan var:
- **İçici 1**'in sonsuz tütünü var
- **İçici 2**'nin sonsuz kağıdı var
- **İçici 3**'ün sonsuz kibriti var

Ajan rastgele iki farklı malzemeyi masaya koyar.  Üçüncü malzemeye sahip olan içici, diğer ikisini alıp sigara yapmalı ve içmelidir. İşi bitince ajan yeni malzemeler koyabilir.

### 🏗️ Proje Yapısı

```
CigarattesSmokersProblem/
├── parnas-solution/          # ✅ Parnas Pushers kullanarak doğru çözüm
│   ├── Main.java
│   ├── Agent. java
│   ├── Smoker.java
│   ├── SmokerType.java
│   ├── Scoreboard.java
│   ├── PusherTobacco. java
│   ├── PusherPaper.java
│   ├── PusherMatch.java
│   └── README.md
│
└── deadlock-solution/        # ❌ Deadlock gösteren naif çözüm
    ├── Main.java
    ├── Agent.java
    ├── Smoker.java
    └── README. md
```

### 🔴 Deadlock Çözümü (Naif Yaklaşım)

**Konum:** `deadlock-solution/`

Bu uygulama, **ne yapılmaması** gerektiğini gösterir. Her içicinin malzemeleri ayrı ayrı almaya çalıştığı yaygın bir hatayı sergiler.

#### Neden Başarısız Olur:
1. Ajan masaya **TÜTÜN + KAĞIT** koyar
2. **İçici-Tütün** kağıdı alır ve kibrit bekler
3. **İçici-Kibrit** tütünü alır ve kağıt bekler
4. **Deadlock!** İki içici sonsuza kadar bekler, ajan devam edemez

#### Çalıştırma: 
```bash
cd deadlock-solution
javac *.java
java Main
```

Birkaç iterasyondan sonra sistemin takıldığını gözlemleyeceksiniz - bu deadlock senaryosunu gösterir.

---

### ✅ Parnas Pushers Çözümü (Doğru Yaklaşım)

**Konum:** `parnas-solution/`

Bu uygulama, deadlock ve yarış koşullarını önlemek için **Parnas Pushers desenini** kullanır.

#### Ana Bileşenler: 

1. **Agent (Ajan)**: Masaya rastgele iki malzeme koyar
2. **Pushers (İticiler)** (3 thread): Her biri bir malzemeyi izler
   - `PusherTobacco` (Tütün İtici)
   - `PusherPaper` (Kağıt İtici)
   - `PusherMatch` (Kibrit İtici)
3. **Scoreboard (Skor Tahtası)**: Mutex ile korunan paylaşımlı durum, masadaki malzemeleri takip eder
4. **Smokers (İçiciler)** (3 thread): Her birinin bir malzemesi var ve diğer ikisini bekler

#### Nasıl Çalışır:

1. **Ajan** `agentSem`'i alır ve iki malzeme koyar (örn.  TÜTÜN + KAĞIT)
2. **İlk pusher** (örn. `PusherTobacco`) tütünü görür:
   - `mutex`'i alır
   - Scoreboard'da tütünü işaretler
   - `mutex`'i bırakır
3. **İkinci pusher** (örn.  `PusherPaper`) kağıdı görür: 
   - `mutex`'i alır
   - Scoreboard'da tütünün olduğunu görür
   - KİBRİT içicisinin uyanması gerektiğini belirler
   - `matchSmoker` semaforunu serbest bırakır
   - Scoreboard'ı temizler
   - `mutex`'i bırakır
4. **İçici-Kibrit** uyanır:
   - Sigarayı yapar ve içer
   - Ajanın devam etmesi için `agentSem`'i serbest bırakır
5. **Döngü deadlock olmadan tekrarlar! **

#### Çalıştırma:
```bash
cd parnas-solution
javac *.java
java Main
```

Sistem, doğru senkronizasyonla deadlock olmadan süresiz çalışır.

---

### 🔧 Teknolojiler

- **Dil:** Java
- **Eşzamanlılık İlkelleri:** 
  - `java.util.concurrent.Semaphore`
  - `Thread`
  - Mutex deseni

### 📚 Öğrenme Hedefleri

- Klasik eşzamanlılık problemlerini anlama
- Deadlock senaryolarını tespit etme
- Doğru senkronizasyon uygulama
- Thread koordinasyonu için semafor ve mutex kullanma
- Karmaşık senkronizasyon için Parnas çözüm deseni

### 🎓 Kullanım Alanları

- Bilgisayar Bilimleri eğitimi
- İşletim Sistemleri dersleri
- Eşzamanlı Programlama gösterimleri
- Mülakat hazırlığı

### 📝 Lisans

Bu proje açık kaynaklıdır ve eğitim amaçlıdır. 

---

## 🤝 Contributing / Katkıda Bulunma

Contributions are welcome! Feel free to open issues or submit pull requests. 

Katkılarınızı bekliyoruz! Issue açabilir veya pull request gönderebilirsiniz. 

---

**⭐ If you find this project helpful, please give it a star! **

**⭐ Bu projeyi faydalı bulduysanız, lütfen yıldız verin!**
