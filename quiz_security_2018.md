## Kuis 

From : https://web.facebook.com/fal.sadikin/posts/10211185760540329

Jawab 3 pertanyaan berikut dengan tepat:

  1. Untuk bidang Web hacking: Apa perbedaan antara cross site scripting (XSS) dan cross site request forgery (CSRF). Berikan juga contoh tekniknya masing masing, sekaligus cara mencegahnya (countermeasures).

  2. Untuk bidang IoT hacking: jelaskan teknik Touchlinking Comissioning attacks, contoh serangannya dan kemungkinan teknik deteksinya.

  3. Untuk bidang code/computer program analysis: jelaskan apa itu memory corruption. Sebutkan salah satu attack vector-nya, bagaimana teknik serangannya, dan juga cara mencegahnya.


## Jawaban 

### Cross-Site Scripting (XSS) 

Cross-Site Scripting (XSS) adalah istilah yang digunakan untuk menggambarkan jenis serangan sisi klien (client side) dengan cara menggunakan kode HTML atau *client script code* lainnya ke suatu situs. Serangan ini akan seolah-olah datang dari situs itu sendiri. Hal ini biasanya dicapai dengan menyimpan skrip jahat dalam basis data yang kemudian diambil dan ditampilkan ke pengguna lain, atau dengan meminta pengguna mengeklik tautan yang akan menyebabkan kode JavaScript penyerang dijalankan oleh peramban pengguna. Akibat serangan ini antara lain penyerang dapat mem-bypass keamanan di sisi klien, mendapatkan informasi sensitif, atau menjalankan aplikasi berbahaya.

### Teknik serangan XSS
 
  * Stored atau persistent
  * Reflected atau nonpersistent

#### Stored XSS

Stored XSS, atau serangan terus-menerus(persistent), adalah saat kode berbahaya disimpan di server target melalui database atau masuk dan diambil oleh pengguna secara otomatis. Tipe ini agak intuitif. Kapanpun pengguna meminta data dari server yang menyertakan informasi yang disimpan, mereka akan terkena kode jahat.

#### Reflected XSS 

Reflected XSS terjadi ketika kode dijalankan dan skrip jahat "dipantulkan" dari server dan dikembalikan ke klien sebagai bagian dari permintaan masukan oleh pengguna. Misalnya, jika pengguna mengklik tautan berbahaya, kode XSS yang dipantulkan dikirim bersama dengan permintaan klien ke server, yang kemudian dipantulkan kembali ke peramban klien.

**Contoh :**

Misalkan pengembang memiliki kode untuk variabel isian `last_name` pada form sebagai berikut:

```
<div> ${last_name}</div>
```

Jika ada pengguna yang secara iseng memasukkan kode yang juga berisikan tag HTML `<script>` ke dalam kolom isian Last Name    

```
James<script>alert('XSS attack!');</script>,Bond
```

Maka kode berikut yang tidak aman akan dikirimkan ke peramban.

```
<div><script>alert('XSS attack!');</script></div>
```

Akibatnya peramban akan mengeksekusi kode JavaScript dalam tag `<script> ('XSS attack!'); </script>` . Dengan demikian secara tidak sengaja pengguna telah menyuntikkan kode tertentu ke halaman yang akan menampilkan peringatan pop-up, yang seharusnya tidak diizinkan.

Karena serangan ini dapat berisi kode JavaScript arbitrer yang akan dijalankan oleh peramban dengan tingkat kepercayaan yang sama seperti JavaScript yang dikirim dari aplikasi asli, ia memiliki potensi untuk melakukan sesuatu yang jauh lebih berbahaya daripada sekedar menampilkan pop-up. Contohnya mungkin untuk mencuri dan mengirim email ke cookie pengguna.   

#### Proteksi terhadap XSS 

Beberapa hal yang dapat dilakukan sebagai langkah pencegahan terhadap serangan XSS antara lain:

  1. Menggunakan teknik filter/seleksi pelolosan (escaping) tipe data
 
     Mengetahui jenis data apa yang dimiliki (misalnya, HTML, teks biasa, atau JSON). Pilih fungsi filter yang tepat berdasarkan detail ini.

     **HTML escaping**
       
       a. Manual

       Berikut adalah teknik HTML-escaping manual untuk semua ekspresi pada halaman menggunakan filter `h`. Ini adalah solusi untuk contoh serangan XSS diatas.

       ```
       <%page expression_filter="h"/>
       ...
       <div>${last_name}</div>
       ```
       
       b. Automatic HTML escaping

       ```
       {% autoescape %}
          Hello {{ last_name }}
       {% endautoescape %}
       ```

       Kedua teknik di atas akan menghasilkan kode yang aman sebagai berikut.

       ```
       <div>&lt;script&gt;alert(&#39;XSS!&#39;);&lt;/script&gt;</div>
       ```
       
       Kali ini, peramban tidak akan menafsirkan tag `<script>` sebagai konteks JavaScript, dan sebagai gantinya hanya menampilkan string asli di halaman.
       
     **JavaScript escaping**
     
     Teknik escape menggunakan JavaScript
     
     ```
     <%! from js_utils import js_escaped_string %>
     ...
     <script type="text/javascript">
        var lastName = "${last_name | n, js_escaped_string}";
        ...
      </script>
     ```
     
     Kode di atas akan menghasilkan kode sumber halaman yang aman sebagai berikut:
     
     ```
     <script type="text/javascript">
       var lastName = "\u0022\u003Balert(\u0027XSS attack!\u0027)\u003B\u0022\u0022\u003B";
       ...
     </script>
     
     ```
  
  2. Selalu melakukan validasi terhadap masukan dari pengguna
     
     Mekanisme pertahanan ini sangat berguna untuk menangkal serangan XSS khususnya tipe reflected XSS.

     ```
     raise ValidationError([
         ValidationError(_('Error 1'), code='error1'),
         ValidationError(_('Error 2'), code='error2'),
      ])
     ```

  3. Tidak mengizinkan pengguna untuk memasukkan data yang akan ditampilkan kembali tanpa adanya validasi tambahan.
     
     Serangan stored XSS terjadi saat pengguna diizinkan untuk memasukkan data yang akan ditampilkan kembali. Contohnya adalah pada message board, buku tamu, dll. Penyerang memasukkan kode HTML atau client script code lainnya pada posting mereka.          
  
  4. Menggunakan *security encoding libraries* misalnya:
     * [python-xss-filter](https://github.com/phith0n/python-xss-filter) 
     * [xss-catcher](https://github.com/bannsec/xss_catcher)
     
___
### Cross-Site Request Forgery (CSRF)

Cross-Site Request Forgery(CSRF) adalah tipe serangan yang memaksa pengguna akhir untuk melakukan tindakan yang tidak diinginkan pada aplikasi web yang telah diautentikasi.  

Pada CSRF serangan dirancang sedemikian rupa sehingga pengguna mengirim permintaan jahat ke situs web target tanpa menyadari tentang serangan tersebut. Beberapa hal dapat dilakukan oleh penyerang dengan memanfaatkan CSRF, misalnya memposting beberapa konten ke papan pesan suatu forum, dan mengirimkan e-card. Salah satu cara paling umum untuk melakukan serangan CSRF adalah menggunakan tag gambar HTML atau objek gambar JavaScript. Kerentanan semacam ini tidak hanya terbatas pada browser. Scripting jahat juga dapat dilakukan melalui file dokumen, video, dll. 

#### Teknik serangan CSRF

Contoh :

**CSRF silent form.**

```
<iframe style="display:none" name="csrf-frame"></iframe>
<form method='POST' action='https://yourbank.com/transfer' target="csrf-frame" id="csrf-form">
  <input type='hidden' name='criticaltoggle' value='true'>
  <input type="text" name="amount" value="100.00" />
  <input type="text name="destination_account_number" value="5647382" />
  <input type='submit' value='submit'>
</form>
<script>document.getElementById("csrf-form").submit()</script>
```

Pada contoh di atas pengguna akan diarahkan untuk melakukan transfer sejumlah uang ke rekening tertentu.

**Real case:**

![Alt text](/images/cryforhelp.png "Optional title")

User salah satu e-commerce di Indonesia yaitu Bukalapak diarahkan oleh penjual nakal untuk mengunjungi halaman palsu dan mengisi data-data penting ke dalam form login palsu yang mirip dengan form login di bukalapak, yg bertujuan menampung email dan password korban yg diisikan di form. Akibatnya attacker dapat mengambil alih akun korban.

**Perbandingan kode form asli dengan yang palsu.**

![Alt text](/images/kode_form.png "Optional title") 

Script **verifikasi.php** akan mengirimkan data login berupa username dan password yang diisikan oleh korban ke form ke dalam database milik penyerang.


#### Proteksi terhadap CSRF 

Langkah pencegahan terhadap serangan CSRF antara lain:

  1. Menggunakan teknik `tokenizing`

     Proteksi dilakukan dengan menambahkan template tag `{% csrf_token %}` pada halaman yang memuat form isian. 

     **templates/login.html**
  
     ```
      <form action="{% url "login" %}" method="post" accept-charset="utf-8">
  
     {% csrf_token %}   
       <input type="hidden" name="next" value="{{ next }}"> 
       <button type="submit" class="btn btn-primary">Login</button> 

     </form>
     ```

     Token tersebut akan ditampilkan dalam HTML seperti yang ditunjukkan di bawah ini, dengan nilai yang khusus untuk pengguna di peramban.

     ```
     <input type='hidden' name='csrfmiddlewaretoken' value='0QRWHnYVg776y2l66mcvZqp8alrv4lb8S8lZ4ZJUWGZFA5VHrVfL2mpH29YZ39PW' />
     ```

  2. Pengaktifan hash(hashing) terhadap semua form (session id, function name, server-side secret).
  
     ```python
     
     class AccountCreateView(FormView):
     
     form_class = AccountCreateForm
   
        def form_valid(self, form):       
        hashed_secret = myHashfunction(unhashed_secret)
        user = self.request.user   # current session user

        Account.objects.create(
            user=user,
            secret=hashed_secret,
        )

        return super(AccountCreateView,self).form_valid(form)
     ```
___
### XSS vs CSRF

Beberapa perbedaan antara CSRF dan XSS adalah:

  *  XSS dirancang untuk mengeksploitasi kepercayaan yang dimiliki pengguna untuk situs tertentu sementara CSRF bertujuan untuk mengeksploitasi kepercayaan yang dimiliki situs web di peramban pengunjung. Jadi, perbedaan utamanya terletak di dalam peramban korban.
    
  * Serangan CSRF terjadi dalam sesi yang diautentikasi ketika server mempercayai pengguna/peramban, sementara XSS tidak memerlukan sesi yang diautentikasi dan dapat dieksploitasi ketika sebuah situs web tidak melakukan langkah-langkah validasi tambahan terhadap masukan dari pengguna.
  
  * Dalam kasus XSS, ketika server tidak memvalidasi atau meloloskan masukan sebagai kontrol utama, penyerang dapat mengirim masukan melalui parameter permintaan atau jenis bidang masukan sisi klien (yang dapat berupa cookie, kolom formulir atau url params). Masukan ilegal tersebut selanjutnya dapat ditampilkan kembali ke antarmuka, disimpan dalam database atau dijalankan dari jarak jauh. 
  
  
___
### Touchlinking comissioning attacks

Beberapa bentuk serangan touchlinking comissioning:

 * Reset perangkat ke seting awal dari pabrikan (Factory-New reset)
 * Penggantian nomor saluran
 * Diskoneksi permanen
 * Network Key Extraction

#### Teknik touchlinking comissioning attacks

Langkah-langkah serangan touchlinking comissioning

  * Mencari nomor saluran perangkat target (scanning)

    Tujuan awal penyerang adalah untuk mengidentifikasi saluran yang digunakan oleh perangkat target untuk berkomunikasi. Perangkat IoT seperti ZigBee menggunakan total 16 saluran dari pita 2,4 GHz untuk komunikasi.

    **Scanning**

    Contoh *scanning* menggunakan framework [Z3sec](https://github.com/IoTsec/Z3sec)

    ```
    z3sec_touchlink --sdr -c all scan
    ```
    
    Jika disekitar gawai scanner terdapat perangkat touchlink-enabled maka hasilnya:

    ```
    Scan responses overview:
    # |RSSI |channel |src_pan_id |src_addr                |
    =======================================================
    0 |0    |12      |0xe21      |00:17:88:01:10:4f:44:e8 |
    ```
    
  * Menangkap paket 

    Setelah penyerang berhasil mengidentifikasi saluran, misalkan saluran nomor 12, langkah berikutnya adalah menangkap paket yang dikirim dan diterima oleh perangkat.

  * Memutar ulang paket untuk mengendalikan perangkat IoT

    Sekarang penyerang itu telah berhasil menangkap paket saat melakukan tindakan menggunakan aplikasi seluler, dia dapat memutar ulang paket untuk melakukan apa yang disebut sebagai serangan replay.


### Deteksi:

Deteksi dapat dilakukan dengan menggunakan beberapa *penetration testing framework*  untuk mengevaluasi keamanan perangkat IoT, misalnya protokol zigbee. Tool tersebut antara lain:

  * [Z3sec](https://github.com/IoTsec/Z3sec) 
  * [Attify](https://github.com/attify/Attify-Zigbee-Framework)
  * [killerbee](https://github.com/riverloopsec/killerbee/)

----     
### Memory Corruption

**Memory corruption** adalah kerusakan memori akibat kesalahan penggunaan alokasi (segmentasi) memori pada program. Hal ini terjadi saat lokasi memori dimodifikasi akibat dari perilaku program berbeda dari konstruksi program asli, misalnya penulisan masukan(input) yang berlebihan ke dalam buffer memory.

Pada dasarnya, pengalokasi memori mengalokasikan halaman memori sekaligus untuk digunakan oleh program, dan memberikan pointer di dalamnya. Karena halaman-halaman ini biasanya lebih besar dari 8 KB, eksekusi program yang memiliki ukuran kecil tidak akan menemui masalah. 

Tetapi jika program berukuran besar mengalokasikan jumlah memori yang lebih besar dan menulis lebih jauh melewati kapasitas ruang yang dialokasikan, proses akan berakhir dengan mencoba untuk menulis ke memori yang tidak teralokasikan, atau memori yang digunakan oleh program lain

Program yang ditulis dalam bahasa C sangat rentan terhadap serangan karena bahasa C menyediakan akses langsung ke low-level memory dan pointer arithmetic tanpa adanya *bounds checking*, sehingga fungsi-fungsi dari library standar C, seperti `gets` dan `strcpy`, yang dimodifikasi oleh penyusup, dapat menulis jumlah yang tak terbatas dari user input ke buffer, sehingga merusak memori.

Jenis-jenis kerentanan yang dapat mengakibatkan *memory corruption* antara lain:
  
  * Menggunakan memori yang tidak terinisialisasi
  * Menggunakan memori tak bertuan (none-owned memory)
  * Adanya kegagalan pada manajemen memory heap  
  * Menggunakan memori di luar memori yang dialokasikan (buffer overflow)

#### Teknik serangan 

Contoh: 

**Buffer overflow.** 

Dengan memanipulasi fungsi eksekusi `gets`, maka **userid** dapat dibanjiri dengan sejumlah besar karakter yang mengakibatkan terjadinya kelebihan muatan (buffer overflow).

```
#include <stdio.h> 
int main () {     
    char userid[8];     
    int allow = 0;     
    printf external link("Enter your userID, please: ");     
    gets(userid);      
    if (grantAccess(userid)) {         
        allow = 1;     }     
        if (allow != 0) {          
            privilegedAction();     
            }     
            return 0; 
        } 
```

#### Pencegahan terhadap memory corruption

  1. Gunakan fungsi setara yang aman, yang memeriksa panjang buffer(bounds checking), yaitu:

     * `gets()` -> `fgets()`
     * `strcpy()` -> `strncpy()`
     * `strcat()` -> `strncat()`
     * `sprintf()` -> `snprintf()`

     Contoh listing program di atas dapat dibuat menjadi lebih aman dari serangan BO dengan penggunaan fungsi yang lebih aman, dan pendefinisian panjang buffer.

     ```
     #include <stdio.h> 
     #include <stdlib.h> 
     #define LENGTH 8 
     int main () {
         char* userid, *nlptr;     
         int allow = 0;       
         userid = malloc(LENGTH * sizeof(*userid));     
         if (!userid)         
             return EXIT_FAILURE;     
         printf external link("Enter your userid, please: ");     
         fgets(userid,LENGTH, stdin);     
         nlptr = strchr(userid, '\n');     
         if (nlptr) *nlptr = '\0'; 

         if (grantAccess(userid)) {         
            allow = 1;     
           }     
         if (allow != 0) {         
            priviledgedAction();
           }       

           free(userid);       
           return 0; 

         } 
     ```

  2. Penggunaan [libsafe](https://github.com/tagatac/libsafe-CVE-2005-1125)

  3. Melakukan tindakan kontrol keamanan memori yang dapat digunakan untuk mencegah kerentanan korupsi-memori, misalnya: 

     * Memanfaatkan flag compiler yang aman seperti -fPIE, -fstack-protector-all, -Wl, -z, noexecstack, -Wl, -z, noexecheap.
     * Menggunakan sistem-on-chip (SoC) dan mikrokontroler (MCU) yang berisi unit manajemen memori (MMU). MMU mengisolasi thread dan proses untuk mengurangi permukaan serangan jika bug memori dieksploitasi.
     * Menggunakan sistem-on-chip (SoC) dan mikrokontroler (MCU) yang berisi unit perlindungan memori (MPU). MPU memberlakukan aturan akses untuk memori dan proses terpisah serta menegakkan aturan-aturan khusus.   
     * Jika tidak ada MMU atau MPU yang tersedia, pantau tumpukan (stack) menggunakan bit yang diketahui untuk memantau berapa banyak tumpukan yang sedang digunakan dengan menentukan berapa banyak tumpukan tidak lagi mengandung bit yang diketahui.   
     * Menggunakan teknik *Address Space Layout Randomization* (ASLR). ASLR secara acak mengatur posisi ruang alamat dari area data utama dari suatu proses, termasuk dasar dari eksekusi dan posisi tumpukan, tumpukan dan pustaka.

___
### Referensi
  * https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)
  * https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)
  * https://github.com/IoTsec/Z3sec/wiki
  * https://www.owasp.org/index.php/Buffer_Overflows
  * https://directory.fsf.org/wiki/Libsafe
