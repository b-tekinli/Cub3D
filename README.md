# Cub3D

## Raycasting Nedir?

Raycasting, 3 boyutlu bir ortamda belirli bir noktadan farklı yönlerde ışınlar çıkartarak, bu ışınların nesnelerle etkileşimini hesaplayarak bir görüntü oluşturma yöntemidir. Bu yöntem genellikle 3 boyutlu oyunlarda, grafiksel gösterimlerde ve benzeri alanlarda kullanılır.
Örneğin, bir oyun yapmak istediğinizi varsayalım. Oyunumuz, kapalı bir labirentten oluşuyor ve oyuncu karakteri, birinci şahıs perspektifinden labirentte hareket edebilir. Oyuncunun etrafı görebilmesi için, raycasting yöntemi kullanılabilir.

Bu yöntemi uygulamak için, oyuncunun bakış yönüne doğru ışınlar oluşturulur. Bu ışınlar, labirentteki duvarlar veya engeller gibi nesnelerle etkileşime girerler ve bu etkileşimler sonucunda, nesnelerin konumları ve boyutlarına göre bir görüntü oluşur.

Örneğin, bir ışın bir duvara çarptığında, ışının duvarın konumuna ve boyutuna göre kırılması veya yansıması hesaplanabilir. Bu şekilde, her bir ışın için nesnelerin konumları ve boyutları hesaplanarak, bir görüntü oluşturulabilir.

Özellikle eski tarz video oyunlarında (örneğin, Doom, Wolfenstein 3D), raycasting yöntemi kullanılarak, 3 boyutlu bir etki yaratılmaya çalışılmıştır. Bu oyunlarda, birçok ışın kullanılarak, oyun dünyasındaki nesnelerin konumları ve boyutları hesaplanarak, oyuncuya bir 3 boyutlu hissiyat verilmiştir.

Raycasting yöntemi genellikle 3 boyutlu grafik motorları kullanılarak uygulanır. Ancak, basit bir 2 boyutlu örnek için, JavaScript kullanalım.

```js
// Örnek nesneler ve duvarlar
const objects = [
  { x: 100, y: 100, radius: 30, color: "red" },
  { x: 200, y: 200, radius: 40, color: "blue" },
  { x: 300, y: 150, radius: 20, color: "green" }
];
const walls = [
  { x1: 0, y1: 0, x2: 500, y2: 0 },
  { x1: 0, y1: 0, x2: 0, y2: 500 },
  { x1: 500, y1: 0, x2: 500, y2: 500 },
  { x1: 0, y1: 500, x2: 500, y2: 500 },
  { x1: 100, y1: 100, x2: 200, y2: 200 },
  { x1: 200, y1: 200, x2: 300, y2: 150 }
];
// Canvas öğesi ve 2D konteksi
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
// Raycasting fonksiyonu
function castRays() {
  const rayResults = [];
  
  // Her bir ışın için
  for (let i = 0; i < 360; i += 1) {
    const angle = i * Math.PI / 180;
    let distance = 0;
    
    // Duvarlarla çarpışana veya dünya dışına çıkana kadar ışını ilerlet
    while (distance < 1000) {
      const x = canvas.width / 2 + distance * Math.cos(angle);
      const y = canvas.height / 2 + distance * Math.sin(angle);
      
      if (x < 0 || x > canvas.width || y < 0 || y > canvas.height) {
        break;
      }
      
      for (const wall of walls) {
        const intersection = lineIntersection(canvas.width / 2, canvas.height / 2, x, y, wall.x1, wall.y1, wall.x2, wall.y2);
        if (intersection) {
          rayResults.push({ x: intersection.x, y: intersection.y, color: "grey" });
          break;
        }
      }
      
      distance++;
    }
  }
  
  return rayResults;
}
```


---


<!-- 
***cub3d.h*** dosyası Cub3D adlı bir 3 boyutlu oyunun motorunun temel özelliklerini içeren header dosyasıdır. libft ve mlx kütüphaneleri dahil edilmiştir. Tanımlanmış olan örneğin oyunun genişliği ve yüksekliği, harita boyutları, hücre boyutu ve bazı renkler gibi. Ayrıca, APPLE derleyicisi tanımlıysa, tuş atamaları farklı bir dizi anahtar kodu kullanır.
Çeşitli yönleri, renkleri ve konumları tanımlayan bir dizi enum yapısı vardır. Ayrıca oyun verilerini saklamak için kullanılan çeşitli yapılar vardır, örneğin: t_control, t_vector2, t_int_vector2, t_llocation, t_ray_data, t_xpm, t_imgdata ve t_mapdata.

---

***color_check.c*** dosyasında bir harita uygulamasına ait fonksiyonlar vardır. 

- check_all_num fonksiyonu, bir dizideki tüm karakterlerin sayısal olup olmadığını kontrol eder.
- color_nbr_check fonksiyonu, bir dizi kodun RGB renk değerlerini doğru bir şekilde içerip içermediğini kontrol eder.
- check_f ve check_c fonksiyonları, harita editörünün zemin ve tavan renklerini kontrol eder ve bu renkleri harita verilerine kaydeder.
- ft_check_rgb fonksiyonu, verilen girdi dizesindeki "F" veya "C" kodlarını arar ve bu kodlara göre zemin ve tavan renklerinin doğruluğunu kontrol eder.
Bu fonksiyonlar, harita editörü uygulamasının işlevselliğinin doğru çalışmasını sağlamak için yazılmıştır.

---

***draw.c*** dosyasında haritayı görselleştiren fonksiyonları içerir. 

- init_req fonksiyonu, haritadaki oyuncunun konumunu alır ve bu konumun piksel koordinatlarını hesaplar. Ayrıca, oyuncunun bakış açısı ve görüş açısına dayanarak, sinüs ve kosinüs hesaplar.
- draw_ray fonksiyonu, oyuncunun bakış açısı ile başlayan bir ışın çizer ve bu ışının haritadaki en yakın engelle karşılaşana kadar hareket ettirir. Işığın her adımı, haritada bir piksel oluşturur ve bu pikselin ne renk olacağı, o pikselin haritadaki değeri tarafından belirlenir.
- calculate_ray fonksiyonu, init_req ve draw_ray fonksiyonlarını birleştirir ve bir ışının haritada nasıl hareket ettiğini hesaplar. Bu hesaplamalar sonucunda, ışının haritada geçtiği son konum, ışının orijininden itibaren hareket ettiği mesafe ve ışının ilgili pikseldeki rengi gibi bilgileri içeren bir t_ray_data yapısı döndürür.
- ft_my_put_pixel fonksiyonu, verilen x ve y koordinatlarındaki pikselin rengini belirler.
- ft_my_get_pixel fonksiyonu, verilen x ve y koordinatlarındaki pikselin rengini okur.

---

***draw_others.c*** dosyası bir nevi grafik işleme kodları içerir. t_data ve t_ray_data adlı iki yapı kullanarak, birinci kişi bakış açısıyla bir labirenti oluşturmak ve onu ekrana çizmek için kullanılır. 
- draw_ceiling() fonksiyonu, tavanın rengini belirleyerek labirentin üst kısmını ekrana çizer. 
- draw_floor() fonksiyonu ise zeminin rengini belirleyerek labirentin alt kısmını ekrana çizer.
- draw_from_right_line() ve draw_from_left_line() işlevleri, labirentin duvarlarını çizmek için kullanılır. İki işlev de aynı koşulu kontrol eder: Bir önceki çizgi ile şu anki çizginin çizim konumları arasında duvar varsa, o duvarı çizer. İki işlev de verilen mesafeye ve ray_data adlı veri yapısına dayanarak, labirent duvarlarını çizer.
- draw_screen4() fonksiyonu ise, tüm ekranı labirent ile doldurur. Bu işlev, while döngüsü kullanarak, her bir yatay çizginin ekrana çizilmesi gereken tüm dikey çizgileri kontrol eder ve draw_from_right_line() veya draw_from_left_line() işlevlerini kullanarak labirentin duvarlarını çizer.

Son olarak, mlx_put_image_to_window() fonksiyonu, ekrana çizilen labirenti görüntüler. Bu işlev, verilen t_data yapısının mlx ve win4 öğelerine dayanarak, çizilen görüntüyü ekranda gösterir.
Bu kod, birinci kişi bakış açısıyla labirent oluşturmak için bir başlangıç ​​noktasıdır ve daha gelişmiş grafik işleme özellikleri eklemek için genişletilebilir.

---

***draw_walls.c*** dosyası birkaç bağımsız değişken alır ve resimleri çizmek için görüntü verilerine erişir. 

İlk önce draw_wall_n_img, draw_wall_e_img, draw_wall_w_img ve draw_wall_s_img fonksiyonları yer alır. Bu fonksiyonlar, bir açı ve mesafe ile birlikte, bir veri yapısı olan t_data ve t_ray_data değişkenlerini alır. Bu değişkenler, çizmek için kullanılacak olan 2 boyutlu görüntüleri tutarlar. Fonksiyonlar, her bir görüntünün yüksekliğini hesaplar ve ardından pikselleri bu yüksekliğe göre çizer.
Sonra, draw_walls fonksiyonu yer alır. Bu fonksiyon, belirli bir mesafedeki duvarları çizmek için, draw_wall_n_img, draw_wall_e_img, draw_wall_w_img ve draw_wall_s_img fonksiyonlarını kullanır. Bu fonksiyon, bir dizi ray_data ve distance değişkeni alır ve çizilecek olan görüntü verilerine erişir.
Fonksiyonların içinde yapılan matematik hesaplamaları, bir mesafenin kamera açısına göre ne kadar uzakta olduğunu ve bu mesafenin yüksekliğini hesaplamak için kullanılır. Bu yükseklik, görüntülerin boyutunu belirler ve piksellerin çizilme yüksekliğini hesaplamak için kullanılır.
Fonksiyonların sonunda, draw_ceiling ve draw_floor fonksiyonlar çağrılır. Bu fonksiyonlar, her bir duvarın üstünde ve altında boşlukları çizmek için kullanılır.

---

***init.c*** dosyasında fonksiyonlar, oyunu başlatmak için değişkenleri ve oyuncu konumunu başlatır. Ayrıca, duvarların ve tavanın rengini belirlemek ve görüntü kaynaklarını yüklemek için de fonksiyonlar barındırır.

- init_var() fonksiyonu oyunun gerekli değişkenlerinin ilk değerlerini ayarlar. Oyuncu pozisyonunu, görüş açısını, yönünü ve pencere boyutlarını tanımlar. Ayrıca, mlx_init() fonksiyonu ile pencere oluşturulur ve pencerenin boyutları belirlenir. Ardından, oyunun görüntüsü için bir görüntü alanı (image) oluşturulur ve bu alanın özellikleri belirlenir. Bu işlem, resimleri yüklemek ve görüntülemek için gereklidir. Daha sonra, tüm resimleri init_xpm() fonksiyonuyla yüklenir ve bu resimlerin bilgileri t_xpm yapısı altında saklanır.

- init_xpm() fonksiyonu, gerekli resimleri yükler ve bu resimlerin t_xpm yapısı içindeki özelliklerini belirler. Bu fonksiyon, "north", "south", "east" ve "west" yönlerindeki görüntülerin yüklenmesi için kullanılır.
- init_direction() fonksiyonu, harita verileri içinde oyuncunun yönünü belirler. Bu fonksiyon, haritadaki her bloğun türünü kontrol eder. Eğer blok oyuncunun yönünü belirleyen bir blok ise, oyuncunun yönü bu bloğa göre ayarlanır. Blok 'N' ise oyuncunun yönü 270, 'W' ise 180, 'E' ise 0, 'S' ise 90 derece olacak şekilde ayarlanır. Ayrıca, oyuncu yönünü belirleyen blok sayısını kontrol eder ve sadece bir blok olmalıdır. Aksi takdirde, işlev -1 değeriyle döndürülür.

---

***key_press_func.c*** dosyası içinde klavyeden bir tuşa basıldığında çağrılan ve tuşa göre bazı işlemler yapan fonksiyonu içerir.

- Eğer basılan tuş W, A, S veya D ise, ft_move fonksiyonu çağrılıyor ve oyuncu bu yönlere doğru hareket ediyor.
- Eğer basılan tuş sağ ok tuşu ise, oyuncunun yönü saat yönünde 3 derece arttırılıyor.
- Eğer basılan tuş sol ok tuşu ise, oyuncunun yönü saat yönünün tersine 3 derece azaltılıyor.
- Oyuncunun yönü 360 dereceden büyükse, 360 dereceye mod alınarak sıfıra geri döndürülüyor.
- Oyuncunun yönü 0 dereceden küçükse, 359 dereceye atanarak 360 dereceye geri döndürülüyor.
- Eğer basılan tuş ESC ise, ft_exit fonksiyonu çağrılıyor ve oyun kapatılıyor.
Son olarak, render_window fonksiyonu çağrılarak güncellenmiş oyun ekranı gösteriliyor.
Bu fonksiyonun geri dönüş değeri her zaman 0 olarak belirtilmiştir.

---

***map_control_utils.c*** 

- last_trim fonksiyonu, verilen bir karakter dizisini sondaki boşluk karakterleri ve okunamayan karakterlerden temizler. İlk önce karakter dizisinin uzunluğunu hesaplar ve sondan başlayarak dizi içindeki son karakterlere bakar. Eğer son karakter boşluk veya okunamayan bir karakterse, bu karakteri 0'a eşitler ve bir önceki karaktere bakar. Bu işlem son karakter alınana kadar devam eder. Son olarak temizlenmiş karakter dizisini döndürür.
- array_cleaner fonksiyonu, verilen bir çift işaretçi (void **) dizisindeki öğeleri ve diziyi kendisi yok eder. Eğer verilen işaretçi dizisi NULL ise fonksiyon hiçbir şey yapmadan geri döner. Aksi takdirde, işaretçi dizisi üzerinde bir döngü kullanarak her öğeyi serbest bırakır ve son olarak da kendisini serbest bırakır.
check_null fonksiyonu, verilen bir t_data yapısında bulunan xpm_dir dizisindeki herhangi bir öğenin NULL olup olmadığını kontrol eder. Eğer herhangi bir öğe NULL ise, fonksiyon -1 döndürür. Aksi takdirde 0 döndürür.
- ft_is_space fonksiyonu, verilen bir karakter dizisindeki başlangıçta bulunan boşluk karakterlerini ve diğer belirlenmiş karakterleri atlayarak, karakter dizisinin ilk karakterini döndürür.
longest_line fonksiyonu, verilen bir t_data yapısındaki map_data yapısı içindeki map dizisindeki en uzun satırın karakter uzunluğunu döndürür. İlk önce satır sayısını belirler, ardından her satırın uzunluğunu hesaplar ve en uzun satırın uzunluğunu tutar. Son olarak, en uzun satırın uzunluğunu döndürür.

---

***move.c*** dosyası birinci şahıs bakış açısı ile oynanan bir oyunun hareket mekanizmasını tanımlıyor.

ft_left, ft_right, ft_backward ve ft_forward fonksiyonları, sırasıyla sol, sağ, geri ve ileri hareketleri tanımlıyor. ft_move fonksiyonu, klavyeden alınan tuşa göre hangi hareket fonksiyonunun çalışacağına karar veriyor.
Her bir hareket fonksiyonu, hareket etmeden önce yapılacak olan kontrol işlemlerini gerçekleştiriyor. Bu kontroller, oyuncunun hareket etmesi durumunda, yolunda bir engel olup olmadığını kontrol ediyor. Eğer engel yoksa, oyuncunun konumunu güncelliyor ve ekranda hareket ettiriyor. Eğer engel varsa, hiçbir şey yapmıyor.
Hareketler, oyuncunun bakış açısına ve oyun içindeki haritaya göre belirleniyor. Oyuncunun bakış açısını ifade eden data->player.direction değişkeni kullanılarak, hareket yönü belirleniyor. Hareket miktarı, move değişkeninde tutuluyor ve oyuncunun konumuna ekleniyor veya çıkarılıyor. Hareketler, yüzde 5 oranında gerçekleştiriliyor.
Son olarak, hareketler sırasında harita üzerinde bulunan engeller kontrol ediliyor. Eğer oyuncu bir engelle karşılaşırsa hareket yapmadan önce duruyor.

---

***read_map.c*** dosyası oyunun harita verilerini okuyan ve kontrol eden bir fonksiyon koleksiyonu içerir. Kodda, önce xpm ve rgb dosyalarını kontrol eden fonksiyonlar yer alır. Daha sonra, harita verilerini okuyan ve işleyen fonksiyonlar vardır.
İlk fonksiyon "ft_check_xpm" adıyla tanımlanmıştır. Bu fonksiyon, bir dizi karakter içinde belirli bir dizenin olup olmadığını kontrol eder. Bu kontrol, "find" dizisi içindeki her öğe için yapılır. "data" yapısal verisi, "xpm_dir" adlı bir dizi içinde xpm dosyalarının yönünü tutar. Eğer bir xpm dosyasının yönü, daha önce belirtilmişse, bu fonksiyon "-1" döndürür. Aksi takdirde, xpm dosyasının yönünü kaydeder ve "0" döndürür.
- get_lines bir dosyayı açar ve dosyanın içeriğini bir diziye kaydeder. Fonksiyon, verilen "total_line" sayısı kadar bellek alanı tahsis eder ve her satırı diziye kopyalar. Ardından, bu diziyi döndürür.

- ft_check_xpm: Bu fonksiyon, bir satırda XPM dosya yolunu belirten ifadeyi arar ve bu ifadeyi uygun bir veri yapıya kaydeder. Bu işlem, dört farklı XPM dosyası yolu için tekrarlanır.
- get_lines: Bu fonksiyon, bir dosyanın tüm satırlarını okur ve bir diziye kaydeder.
- control: Bu fonksiyon, harita verilerinin doğruluğunu kontrol eder. Haritanın başlangıç ve bitiş satırlarını belirler ve harita verilerinde hatalar varsa, doğru bir şekilde hata kodlarını döndürür.
- ft_get_map: Bu fonksiyon, harita dosyasının tamamını okur ve harita verilerini doğru bir şekilde veri yapılarına dönüştürür.
- read_file: Bu fonksiyon, dosyayı açar ve ft_check_xpm ve ft_check_rgb fonksiyonlarını çağırarak dosyadaki verilerin doğruluğunu kontrol eder.
Bu kodların amacı, bir oyun motoru için gerekli olan harita verilerini doğru bir şekilde okumak ve işlemektir.

---

***read_map_utils.c*** dosyası harita verilerinin yüklenmesi ve kontrolüyle ilgili fonksiyonları içerir. Aşağıda, her fonksiyonun ne yaptığını açıklayacağım:
- int get_map_height(char *path): Bu işlev, harita verilerinin yüklenmesi için verilen dosya yolunu kullanarak dosya içindeki her satırda 1'ler ve 0'lar arayarak haritanın yüksekliğini hesaplar ve geri döndürür.
- int put_map(t_data *data, char **lines): Bu işlev, dosya yüklemesi sırasında toplanan satırların dizi halinde saklandığı lines parametresini kullanarak harita verilerini data->map_data.map dizisine kopyalar. Ardından, check_wall işlevini kullanarak harita verilerinin doğru olup olmadığını kontrol eder.
- int check_wall(t_data *data): Bu işlev, harita verilerinin doğru olup olmadığını kontrol eder. Bu kontrol, haritanın çevresinde duvar olup olmadığını, haritadaki tüm yol noktalarına ulaşılabilir olup olmadığını kontrol eder.
- int check_all_way(t_data *data, int i, int j): Bu işlev, belirli bir konumdaki noktanın çevresindeki tüm noktaların yol noktaları olup olmadığını kontrol eder.
- int ft_count_line(char *path): Bu işlev, verilen dosya yolunda kaç satır olduğunu sayar ve geri döndürür. Bu sayı, yüklenecek dosyanın boyutunu belirlemek için kullanılabilir.

---

***utils.c*** dosyasında matematik kısmı yapılır.
- deg2rad() fonksiyonu, bir açıyı dereceden radyana çevirmek için kullanılır. Fonksiyonun parametresi deg isimli bir tamsayıdır ve dönüştürülen radyan değeri bir ondalık sayı olarak döndürülür.
- render_window() fonksiyonu, oyunun bir karesel penceresinde bir görüntü render etmek için kullanılır. Fonksiyon, t_data türünde bir veri yapısına sahip bir işaretçi alır. Fonksiyon, angle isimli bir çift hassas sayı, ray isimli bir t_ray_data veri yapısına sahip bir işaretçi ve bir tam sayı olan i değişkenleri tanımlar. i değişkeni, GAMEWIDTH sabitindeki değere eşitlenir. Ardından, angle değişkeni data veri yapısındaki player yapısının fov (görüş alanı) değerine eşitlenir. ray işaretçisi, GAMEWIDTH kadar bellekte yer ayırmak için malloc() fonksiyonuyla bellekte ayrılır. Daha sonra, while döngüsü, i değeri sıfır olana kadar devam eder. Döngü içinde, ray işaretçisinin i indisindeki elemanı, calculate_ray() fonksiyonuna data veri yapısı ve angle değişkeni ile birlikte göndererek hesaplanır.  GAMEWIDTH'e bölünmüş hali kadar azaltılır. Döngü tamamlandıktan sonra, draw_screen4() fonksiyonu, data veri yapısı ve ray işaretçisi ile birlikte çağrılır. Daha sonra, data veri yapısındaki dist_l ve dist_r değerleri hesaplanır. Son olarak, ray işaretçisi serbest bırakılır.
- error_check() fonksiyonu, bir dosya okuma işlemi gerçekleştirir ve hata denetimi yapar. Fonksiyon, t_data türünde bir veri yapısına sahip bir işaretçi ve bir karakter dizisi olan path parametreleri alır. path değişkeni, okunacak dosyanın yolunu içerir. Fonksiyon, open() fonksiyonuyla path değişkenindeki dosyayı açar ve döndürülen dosya tanıtıcısı fd değişkenine atanır. Eğer dosya açılamazsa veya dosyanın uzantısı .buc değilse, hata mesajı yazdırılır ve -1 döndürülür.

---

***utils2.c*** dosyasında bellek yönetimi işlemleri gerçekleştirilir.

- free_func fonksiyonu, control_data değişkeni tarafından belirlenen kontrol bitleri ile işaretlenen değişkenlerin belleklerini serbest bırakıyor. xpm biti ayarlanmışsa, ft_xpm_cleaner fonksiyonunu çağırarak data yapısındaki map_data değişkenindeki xpm_dir dizisindeki bellek bloklarını serbest bırakır. int_map biti ayarlanmışsa, array_cleaner fonksiyonunu kullanarak data yapısındaki map_data değişkenindeki int_map dizisindeki bellek bloklarını serbest bırakır. map biti ayarlanmışsa, yine array_cleaner fonksiyonunu kullanarak data yapısındaki map_data değişkenindeki map dizisindeki bellek bloklarını serbest bırakır.
- array_len fonksiyonu, bir karakter dizisi (string) dizisinin boyutunu hesaplar ve geri döndürür.
- check_wall_xpm fonksiyonu, data yapısındaki map_data değişkenindeki xpm_dir dizisindeki her bir XPM dosyası için bir dosya açmaya çalışır. Dosya açılamazsa, xpm kontrol bitini döndürür ve bu bitin ayarlanmış olması bellek temizleme işlemlerinin yapılmasını sağlar.
- ft_recontrol fonksiyonu, t_control türünde bir yapıyı parametre olarak alır ve bu yapıdaki kontrol değişkenlerini yeniden ayarlar. read_file, check_wall, get_map ve set_map değişkenleri, -1'den farklı bir değerde ayarlanmışlarsa, yeniden 1 olarak ayarlanır. player_pos değişkeni, aynı şekilde değiştirilmeden geri döndürülür. Yeni t_control yapısı geri döndürülür.

---

***valid_map.c*** dosyası duvar kontrolü sağlamak ve harita üzerindeki sınır kontrolünü yapan fonksiyonları içerir.

- top_wall, haritanın en üst satırındaki duvarları kontrol eder ve eğer hiçbir duvar yoksa (-1) değerini döndürür.
İkinci fonksiyon, "bottom_wall", haritanın en alt satırındaki duvarları kontrol eder ve eğer hiçbir duvar yoksa (-1) değerini döndürür.
Üçüncü fonksiyon, "left_wall", haritanın sol tarafındaki duvarları kontrol eder ve eğer hiçbir duvar yoksa (-1) değerini döndürür.

Dördüncü fonksiyon, "ft_multi_map", harita üzerinde dolaşarak haritanın sonuna kadar sınır kontrolünü yapar ve harita üzerinde birden fazla bölüm varsa bunların doğru bir şekilde ayrılmış olup olmadığını kontrol eder. Eğer harita bölümleri doğru bir şekilde ayrılmış değilse (-1) değerini döndürür.

---

***main.c*** fonksiyonu, ac (argüman sayısı) ve av (argüman vektörü) parametreleri alır. Programa sadece bir argüman verildiğinde çalışır. init_var fonksiyonu çağrılarak değişkenlerin başlatılması sağlanır. error_check fonksiyonu, av[1] argümanını kontrol ederek dosyanın açılıp açılamayacağını kontrol eder. Eğer bir hata varsa, program hata mesajı yazdırarak sonlanır. Hata yoksa, init_xpm ve init_color fonksiyonları çağrılarak programın diğer ayarları yapılır.
render_window fonksiyonu çağrılarak oyunun ana penceresi oluşturulur. mlx_hook fonksiyonları, kullanıcının klavyedeki tuşlara basması veya pencereyi kapatması gibi olayları algılar. Klavyeden bir tuşa basıldığında, key_press_func fonksiyonu çağrılır. Eğer kullanıcı kapatma işlemini gerçekleştirirse, ft_exit fonksiyonu çağrılır ve program sonlanır. mlx_loop fonksiyonu, programın çalışmasını sağlar.

<br />
-->
