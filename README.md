Merhabalar, yakın zamanda [https://coder.com/](https://coder.com/)  "code-server" adını verdikleri uzak sunucuda kod  yazabileceğiniz ve çalıştırabileceğiniz VSCode geliştirme ortamını tanıttı.

Ben de, digitalocean üzerinde basit bir sunucu kiralayarak, bu geliştirme ortamını nasıl kurabileceğiniz konusunda sizlere yardımcı olmaya çalışacağım.

## Neden?

Geliştirme ortamının kurulumuna geçmeden önce, neden böyle bir şeye ihtiyacımız var ya da var mı konusundan kısaca bahsetmek doğru olacaktır.

> If you can open a browser, you can use Coder.

geliştirici firmanın websitesinde de görebileceğiniz bu slogan, aslında çok şey ifade ediyor. Artık, web tarayıcı çalıştırabildiğiniz her türlü cihazda (tablet, bilgisayar vs.) VSCode üzerinde kod yazabilirsiniz.

Yazdığınız kodlar, yüksek performans gerektiren işlemlerde, bilgisayarınızın kaynak kısıtlamalarından bağımsız şekilde çalışır hale gelir.  

## Adımlar

1. DigitalOcean hesabı oluşturarak, bir sunucu kiralayacağız.
2. Sunucumuzun konfigurasyonunu yapacağız, aynı zamanda SSH anahtarımızı üreteceğiz.
3. Sunucumuza bağlantı sağlayacağız.
4. Code-server binary dosyalarını çalıştırıp, kullanıma hazır hale getireceğiz.
5. Boş bir proje oluşturarak, web tarayıcı üzerinde geliştirme ortamımızı ayağa kaldıracağız. 
6. Kendinden imzalı (self-signed) TLS/SSL sertifikası oluşturacağız.

## Nasıl?

### DigitalOcean hesabı oluşturup, sunucu kiralamak

Sunucu hizmetinizi, istediğiniz bir yerden kiralayabilirsiniz. (AWS, Azure, Google Cloud vs.) Demomuz için, benim tercihim DigitalOcean oldu.

- [https://www.digitalocean.com](https://www.digitalocean.com) 'a girip üyelik oluşturarak başlayalım.
- Kullanıcı hesabınızı oluşturup, giriş yaptığınızda bir dashboard ile karşılaşacaksınız.
- Sol tarafta bulunan "+New Project" 'e tıklayarak istediğiniz isimde bir proje oluşturabilirsiniz. Ben projemiz için, **VSCode-Server-Demo** adını verdim.

- Projemiz oluştu, anasayfada görüntüleyebilirsiniz.
- **Get started with a Droplet**
butonuna tıklayarak, sunucumuzu oluşturabiliriz.

- Create Droplets sayfasında:

### Sunucu konfigurasyonunun yapılması

**Distributions:** Ubuntu 18.04.2 x64

**Choose a Plan:** Standart $10/mo ( Spoiler: $5/mo seçtiğinizde out of memory hatası alma ihtimaliniz çok yüksek. )

**Choose a datacenter region:** New York ( Daha iyi verim alacağınızı düşündüğünüz bir bölgeyi seçebilirsiniz )

**Add your SSH keys:** SSH anahtarınızı, sunucunuzu ayağa kaldırdıktan sonra da oluşturabilirsiniz. Yine de, SSH anahtarınızı nasıl oluşturacağınızı anlatmak istiyorum.

Terminalinizde:

    ssh-keygen -t rsa

çalıştırdığınızda **/home/{user}/.ssh/id_rsa** altına anahtarınızı kaydeder. İsterseniz anahtarınızın olduğu dosyayı da şifreleyebilirsiniz. ( Oluşturma aşamasında size soracaktır )

> Not: Windows işletim sistemi kullananlar, PuTTY programından faydalanarak SSH anahtarlarını üretebilirler.

SSH anahtarınızın oluşturulduğu klasöre gittiginizde,  "**id_rsa.pub**" uzantılı bir dosya göreceksiniz. İçeriğini, herhangi bir metin düzenleyici ile açıp kopyalamalısınız.

Daha sonra New SSH Key ' e tıkladığınızda, kopyaladığınız içeriği SSH content kısmına yapıştırabilirsiniz. Ben isim olarak **vscode-sshkey** verdim, siz dilediğiniz ismi verebilirsiniz.

1 dakikadan az bir süre sonra sunucunuz ayağa kalkmış hale geliyor.

### Sunucuya bağlantının yapılması

Artık terminalden

    ssh root@{sunucunuzun_ip_adresi}

çalıştırarak, sunucunuza bağlantı sağlayabilirsiniz. 

Şöyle bir ekranla karşılaşmanız gerekmekte: 

Sunucumuza bağlandık, sırada code-server binaryilerimizi indirip, geliştirme ortamımızı ayağa kaldırmakta.

### Geliştirme ortamının çalışabilir hale gelmesi

[https://github.com/codercom/code-server/releases](https://github.com/codercom/code-server/releases) linkinden size uygun olan dosyayı indirebilirsiniz. Ben aşağıdaki komutu kullandım:

    wget {code-server1.604-vsc1.32.0-linux-x64.tar.gz}

    ls

 yazarak kontrol edelim.

Dosyamızın indirmesi başarıyla tamamlandı, aşağıdaki komutu kullanarak tar'dan çıkarabiliriz.

    tar xvfz code-server1.604-vsc1.32.0-linux-x64.tar.gz

code-server'ımızı herhangi bir proje yolundan çalıştırabilmemiz için,  **/bin** altına taşımamız gerekiyor. 

Tar'dan çıkardığımız **code-server1.604-vsc1.32.0-linux-x64** içine girip, **mv** komutuyla taşıma işlemini gerçekleştirelim.

    cd code-server1.604-vsc1.32.0-linux-x64.tar.gz
    mv code-server /bin

Daha sonra ana dizinimizde proje oluşturarak, code-server'ımızı başlatabiliriz.

    cd ..
    mkdir vscode-demo
    cd vscode-demo
    code-server

Uzayıp giden bir ekranla karşılaşacaksınız, aşağıda ekran görüntüsünü verdiğim kısım, kullanacağımız port adresi.

TLS/SSL sertifikamız olmadığı için,

> No certificate specified. Documentation on securing....

gibi bir uyarı göreceksiniz. Şimdilik önemsemeyelim.

Daha sonra, sunucu IP adresimiz ile birlikte, belirtilen port'la web tarayıcımız üzerinden giriş yapmaya çalışacağız. 

> {ip_adresi}:8443

aşağıdaki ekran görüntüsünde de görebileceğiniz gibi, bağlantınızın güvenli olmadığına dair bir uyarı alacaksınız.

Şu an için bunu ihmal edelim ve **Advanced→Proceed to {ip_adresi}(unsafe)** diyerek devam edelim.

Sunucu parolası girmemizi isteyecek, terminalimizde code-server çalıştırdığımızda bize bunu da sağladı, oraya gidip **Password:**  yazan kısmı bularak, parolamız ile giriş yapabiliriz.

Evet bu kadar! 

Tarayıcı üzerinde geliştirmenizi yapabilirsiniz. 

> TLS/SSL sertifikasını oluşturup, bağlantı uyarısı almak istemeyen arkadaşlar, okumaya devam edebilirler.

### TLS/SSL sertifikasının oluşturulması

[https://github.com/codercom/code-server/blob/master/doc/security/ssl.md](https://github.com/codercom/code-server/blob/master/doc/security/ssl.md) linkine gidelim. 

Adımları takip ederek sertifikanızı oluşturabilirsiniz, bu sayfadan ayrılmak istemeyenler içinse, adımları listeledim.

1. Sertifikamızı ve anahtarımızı saklamak için, bir dosya yolu oluşturalım. ( İstediğiniz dosya adını ve yolunu verebilirsiniz. Ben ana dizinde oluşturdum )

    mkdir ~/sertifika 
    cd ~/sertifika

  2. TLS sertifikası ve anahtar oluşturma. ( sertifika klasörünün içerisinde ) 

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ~/sertifika/MyKey.key -out ~/sertifika/MyCertificate.crt

   3. Sertifika ve anahtarımızı kullanarak code-serverımızı başlatalım. Proje klasörümüze gidelim. ( en son sertifika klasöründeydik )

    cd ~/vcode-demo
    code-server --cert=/root/sertifika/MyCertificate.crt --cert-key=/root/sertifika/MyKey.key

Sertifika uyarısı almıyoruz artık.


Ancak tarayıcınızı açtığınızda, yine bağlantınızın güvenli olmadığına dair bir uyarı alacaksınız. 

Bunu engellemek için (firefox): 

- Preferences → Privacy and Security → Certificates →View Certificates → Servers → Add Exception

kısmına sunucunuzun ip adresini ve port'unu girip **Get Certificate** dedikten sonra, Confirm Security Exception diyerek işlemi tamamlayabilirsiniz.

> VSCode sunucunuza bağlandığınızda, güvenlik uyarısı almadan direk giriş ekranı sizi karşılayacak!

## Sonuç

İstediğiniz makine ile, istediğiniz yerde, arzu ettiğiniz dil/framework kullanarak geliştirme yapabilirsiniz. 

Bu çalışma, ilk blog yazıma vesile oldu. Yazıda bulunan hata ve/veya eksiklerle ilgili yorumlarınızı bekliyorum.

Herkese iyi haftalar.

## Faydalanılan Linkler

[https://github.com/codercom/code-server](https://github.com/codercom/code-server)

[https://github.com/codercom/code-server/blob/master/doc/security/ssl.md](https://github.com/codercom/code-server/blob/master/doc/security/ssl.md)

[https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/)

[https://support.mozilla.org/en-US/questions/1055526](https://support.mozilla.org/en-US/questions/1055526)

[https://www.youtube.com/watch?v=9Emn2YQNDl0&t=903s](https://www.youtube.com/watch?v=9Emn2YQNDl0&t=903s)
