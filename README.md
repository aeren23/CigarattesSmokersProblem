# Cigarette Smokers Problem

Bu depo iki ayrı yaklaşımı yan yana gösterir: 
- **Naif (deadlock'lı) çözüm:** İki içici malzemeleri ayrı ayrı kaptığında masa boş sinyali gelmez ve sistem kilitlenir.
- **Parnas pushers çözümü:** Scoreboard + pushers ile hangi içicinin uyanacağı kesinleşir, deadlock engellenir.

## Klasör Yapısı
```
parnas-solution/     # Parnas (pushers) yaklaşımı
deadlock-solution/   # Naif, bilerek deadlock üreten yaklaşım
```

## Hızlı Çalıştırma (PowerShell)
- Parnas: `cd parnas-solution; javac *.java; java Main`
- Deadlock: `cd deadlock-solution; javac *.java; java Main`

## Adım Adım: Deadlock Senaryosu
1) Agent `tableEmpty` semaforunu alır ve rastgele iki malzeme bırakır (örn. TOBACCO + PAPER).
2) `Smoker-Tobacco` kağıdı alır, kibriti bekler; `Smoker-Match` tütünü alır, kağıdı bekler.
3) İki içici de ikinci malzemeyi beklerken `tableEmpty` sinyali verilmez, agent yeni tur başlatamaz ve thread'ler kilitlenir.

## Adım Adım: Parnas Pushers Akışı
1) Agent `agentSem` alır, iki malzeme bırakır.
2) İlgili pusher gelen malzemeyi `mutex` altında scoreboard'a işler.
3) İkinci pusher geldiğinde kombinasyonu görür ve doğru içiciyi uyandırır.
4) İçici sigarayı yapar, `agentSem.release()` ile agent'a devam izni verir; döngü deadlock olmadan sürer.

## Sunum İçin Öneri
- Önce `deadlock-solution`'ı çalıştırıp kilitlenme çıktısını gösterin.
- Ardından `parnas-solution` çıktısıyla farkı vurgulayın (akıcı, kilitsiz).