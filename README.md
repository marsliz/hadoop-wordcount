# WordCount Uygulaması

**Bu çok çok basit bir Map-Reduce uygulamasıdır. Bu uygulamanın geliştirilme aşamaları aşağıdaki gibidir. Cloudera, Maven ve Java'nın kurulu olduğu varsayılmıştır. Uygulama Cloudera üzerinde geliştirilmiştir.**

### 1. adım : Maven ile proje oluştur
```
$ mvn archetype:generate -DgroupId=app -DartifactId=wordcount –Dversion=v1.0 -DarchetypeArtifactId=maven-
archetype-quickstart -DinteractiveMode=false
```

### 2. adım : Map-Reduce uygulamasını geliştir

Map-Reduce, HDFS üzerindeki büyük verileri işleyebilmek amacıyla kullanılan bir yöntemdir. İstediğimiz verileri filtrelemek için kullanılan Map fonksiyonu ve bu verilerden sonuç elde etmemizi sağlayan Reduce fonksiyonlarından oluşan program yazıldıktan sonra Hadoop üzerinde çalıştırılır.

Klasik bir WordCount uygulaması aşağıdaki adımlardan oluşur:
- Input : veri
- Splitting : verinin bölünmesi/bloklara ayrılması
- Mapping : verinin key (kelime), value (1) şeklinde ayrılması
- Shuffling : aynı özellikteki verilerin bir araya toplanması
- Reducing : aynı özellikteki verilerin key (kelime), value(sayısı) şeklinde birleştirilmesi
- Final Result : sonuçlar


Projenin oluşturulduğu dizine gir :
```
$ cd wordcount
```

- [pom.xml](https://github.com/marsliz/hadoop-wordcount/blob/master/pom.xml)
- [WordMapper.java](https://github.com/marsliz/hadoop-wordcount/blob/master/src/main/java/app/WordMapper.java)
- [WordReduce.java](https://github.com/marsliz/hadoop-wordcount/blob/master/src/main/java/app/WordReduce.java)
- [WordCount.java](https://github.com/marsliz/hadoop-wordcount/blob/master/src/main/java/app/WordCount.java)

### 3. adım : Maven ile uygulamayı derle

Uygulama derlendiğinde sonraki adımlarda kullanılacak .jar dosyası oluşturulmaktadır, dosya `wordcount/target` dizini altında bulunabilir.
```
$ mvn package
```

### 4. adım : HDFS sisteminde girdi ve çıktı dizinlerini oluştur

Girdi dizini :
```
$ hdfs dfs -mkdir /user/w_input
```

Çıktı dizini :
```
$ hdfs dfs -mkdir /user/w_output
```

### 5. adım : Girdileri HDFS sistemine kopyala

Üst dizine gidip örnek verilerin olduğu bir dizin ve içinde iki .txt dosyası oluştur :
```
$ cd ..
$ mkdir data
$ touch data/file1.txt data/file2.txt
```

.txt dosyalarının içine metin yaz :
```
$ echo "dünya düzdür dünya düz ise güneş neden düz değildir yoksa düz müdür öyleyse marsta düzdür aslında 
her şey düzdür" > data/file1.txt

$ echo "dünya yuvarlak değildir ancak güneş yuvarlaktır marsta yuvarlaktır öyleyse dünya neden yuvarlak 
değildir" > data/file2.txt
```

Girdileri HDFS sistemine kopyala :
```
$ hdfs dfs -copyFromLocal data/* /user/w_input
```

### 6. adım : Uygulamayı çalıştır
```
$ hadoop jar wordcount/target/wordcount-v1.0.jar /user/w_input /user/w_output/result
```

### 7. adım : Sonuçları gör

Aşağıdaki komutu girdiğimizde iki adet dosya oluştuğunu göreceğiz:
```
$ hdfs dfs -ls /user/w_output/result/
```

Bir işi başarıyla tamamlayan Map-Reduce uygulaması, çıktı dizininde bir `_SUCCESS` dosyası oluşturur. `part-r-00000` sonuçların yazıldığı dosyadır.

Sonuçlar aşağıdaki komut ile görülebilir :
```
$ hdfs dfs -cat /user/w_output/result/part-r-00000
```

Dikkat edilirse çıktının alfabetik olarak sıralandığı göze çarpmaktadır :
```
ancak	1
aslında	1
değildir	3
dünya	4
düz	3
düzdür	3
güneş	2
her	1
ise	1
marsta	2
müdür	1
neden	2
yoksa	1
yuvarlak	2
yuvarlaktır	2
öyleyse	2
şey	1
```
