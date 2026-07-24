## Koordinat Homogen dan Model Homografi

Kamera fisheye pada subsistem lokalisasi dipasang di langit-langit dengan orientasi menghadap tegak lurus ke lantai gudang pada ketinggian tetap 60 cm. Dalam kondisi ideal, apabila sumbu optik kamera benar-benar sejajar dengan bidang lantai dan tidak terdapat distorsi lensa sama sekali, pemetaan dari koordinat piksel citra ke koordinat fisik lantai akan bersifat affine sederhana, yaitu hanya berupa kombinasi translasi, rotasi, dan penskalaan seragam. Pada kenyataannya, kondisi ideal tersebut sangat sulit dicapai karena kamera tidak pernah terpasang dengan kesejajaran sempurna terhadap bidang lantai. Ketidaksejajaran ini menghasilkan citra berbentuk trapesium, di mana objek yang berada jauh dari sumbu optik kamera akan tampak lebih terkompresi dibandingkan objek yang berada tepat di bawah kamera. Fenomena ini dikenal sebagai perspective foreshortening. Setelah distorsi radial lensa fisheye dikoreksi melalui proses kalibrasi, efek foreshortening tersebut masih tersisa dan tidak dapat dikoreksi dengan transformasi affine biasa, sehingga diperlukan sebuah transformasi proyektif yang disebut homografi.

Secara matematis, homografi didefinisikan pada ruang koordinat homogen, bukan pada koordinat Kartesian biasa. Penggunaan koordinat homogen diperlukan karena transformasi proyektif melibatkan pembagian oleh suku yang juga bergantung pada posisi titik itu sendiri, sehingga tidak dapat direpresentasikan sebagai transformasi linear murni dalam koordinat Kartesian dua dimensi. Sebuah titik piksel $(u, v)$ pada citra kamera direpresentasikan sebagai vektor homogen tiga dimensi $\tilde{p} = (u, v, 1)^T$, sedangkan sebuah titik pada bidang grid lantai gudang $(g_x, g_y)$ direpresentasikan sebagai $\tilde{g} = (g_x, g_y, 1)^T$. Hubungan antara kedua representasi tersebut dinyatakan sebagai:

$$
\lambda \begin{bmatrix} g_x \\ g_y \\ 1 \end{bmatrix} = \mathbf{H} \begin{bmatrix} u \\ v \\ 1 \end{bmatrix}, \qquad
\mathbf{H} = \begin{bmatrix} h_{00} & h_{01} & h_{02} \\ h_{10} & h_{11} & h_{12} \\ h_{20} & h_{21} & h_{22} \end{bmatrix}
$$

dengan $\lambda \neq 0$ merupakan faktor skala homogen yang muncul secara alami dari representasi proyektif dan tidak memiliki makna fisik tersendiri di luar proses normalisasi. Matriks $\mathbf{H}$ berukuran $3 \times 3$ sehingga secara nominal memiliki sembilan entri, namun karena homografi hanya terdefinisi hingga faktor skala, yaitu mengalikan seluruh entri matriks dengan sebuah konstanta bukan nol tidak mengubah transformasi yang diwakilinya, maka jumlah derajat kebebasan aktual dari $\mathbf{H}$ hanyalah delapan, bukan sembilan. Untuk menghilangkan ambiguitas skala ini, digunakan konvensi normalisasi dengan menetapkan entri terakhir $h_{22} = 1$, sehingga bentuk matriks homografi yang digunakan pada sistem ini menjadi:

$$
\mathbf{H} = \begin{bmatrix} h_{00} & h_{01} & h_{02} \\ h_{10} & h_{11} & h_{12} \\ h_{20} & h_{21} & 1 \end{bmatrix}
$$

Untuk memperoleh bentuk transformasi yang dapat langsung digunakan secara praktis, faktor skala homogen $\lambda$ perlu dieliminasi dari persamaan matriks di atas. Perkalian matriks pada baris ketiga memberikan hubungan $\lambda = h_{20}u + h_{21}v + 1$. Dengan membagi hasil perkalian pada baris pertama dan baris kedua dengan nilai $\lambda$ tersebut, diperoleh bentuk rasional atau bentuk non-homogen dari homografi, yaitu:

$$
g_x = \frac{h_{00}u + h_{01}v + h_{02}}{h_{20}u + h_{21}v + 1}, \qquad
g_y = \frac{h_{10}u + h_{11}v + h_{12}}{h_{20}u + h_{21}v + 1}
$$

Perlu diperhatikan bahwa kedua persamaan di atas memiliki penyebut yang identik, yaitu $h_{20}u + h_{21}v + 1$. Kesamaan penyebut inilah yang menjadi ciri khas dari transformasi proyektif dan yang membedakannya secara fundamental dari transformasi affine. Pembagian oleh suku linear yang bergantung pada posisi piksel inilah yang secara matematis mengoreksi efek trapesium akibat perspective foreshortening yang telah dijelaskan sebelumnya. Sebagai ilustrasi, apabila koefisien $h_{20}$ dan $h_{21}$ bernilai nol, maka penyebut pada kedua persamaan di atas akan selalu bernilai satu, dan bentuk transformasi akan tereduksi menjadi transformasi affine biasa yang hanya terdiri atas kombinasi translasi, rotasi, penskalaan, dan shear, tanpa kemampuan mengoreksi distorsi perspektif. Dengan demikian, keberadaan koefisien $h_{20}$ dan $h_{21}$ yang tidak nol menjadi indikator matematis bahwa sistem memang membutuhkan koreksi perspektif, sesuai dengan kondisi pemasangan kamera pada subsistem lokalisasi yang tidak sepenuhnya tegak lurus terhadap bidang lantai.

## Pemetaan Piksel ke Koordinat Grid dan Diskritisasi Sel

Setelah matriks homografi $\mathbf{H}$ diperoleh melalui proses kalibrasi, setiap piksel penanda AprilTag yang terdeteksi pada frame kamera dapat dipetakan ke koordinat grid kontinu menggunakan bentuk rasional homografi yang telah diturunkan sebelumnya. Untuk sebuah titik piksel hasil deteksi $(u, v)$, koordinat grid kontinu $(g_x, g_y)$ dihitung sebagai:

$$
g_x = \frac{h_{00}u + h_{01}v + h_{02}}{h_{20}u + h_{21}v + 1}, \qquad
g_y = \frac{h_{10}u + h_{11}v + h_{12}}{h_{20}u + h_{21}v + 1}
$$

Perlu ditekankan bahwa nilai $(g_x, g_y)$ yang dihasilkan dari persamaan di atas merupakan bilangan real kontinu, bukan indeks sel grid diskrit. Nilai ini merepresentasikan posisi fisik penanda pada bidang lantai gudang dalam satuan sel grid, dengan domain yang dibatasi oleh dimensi fisik area kerja yang valid. Mengingat grid lokalisasi terdiri atas 8 kolom dan 4 baris yang merepresentasikan lantai gudang berukuran 2,4 meter kali 1,2 meter, dengan setiap sel berukuran 0,3 meter kali 0,3 meter, maka domain koordinat grid kontinu yang valid adalah $g_x \in [0, 8]$ dan $g_y \in [0, 4]$.

Karena keperluan sistem adalah menetapkan setiap penanda ke sebuah sel grid diskrit dengan indeks integer, bukan posisi kontinu, diperlukan sebuah proses diskritisasi lanjutan yang mengubah koordinat kontinu tersebut menjadi indeks sel $(c, r)$, dengan $c \in \{0, 1, \dots, 7\}$ untuk kolom dan $r \in \{0, 1, 2, 3\}$ untuk baris. Setiap sel grid $(c, r)$ didefinisikan memiliki titik pusat pada koordinat grid kontinu $(c + 0{,}5,\; r + 0{,}5)$. Sebagai contoh, sel dengan indeks kolom $c = 0$ memiliki titik pusat pada $g_x = 0{,}5$, sel dengan indeks kolom $c = 1$ memiliki titik pusat pada $g_x = 1{,}5$, dan seterusnya mengikuti pola yang sama hingga kolom terakhir.

Aturan penetapan sel yang digunakan pada sistem ini adalah aturan titik pusat terdekat, yaitu sebuah titik koordinat kontinu $g_x$ ditetapkan ke sel dengan indeks $c$ yang titik pusatnya paling dekat dengan $g_x$. Secara matematis, aturan ini dinyatakan sebagai:

$$
c^\ast = \arg\min_{c \,\in\, \mathbb{Z}} \left| g_x - (c + 0{,}5) \right|
$$

Persamaan pencarian nilai minimum di atas dapat disederhanakan menjadi operasi pembulatan biasa melalui substitusi variabel. Misalkan $y = g_x - 0{,}5$, maka suku di dalam tanda mutlak pada persamaan di atas dapat ditulis ulang menjadi $\left| g_x - (c+0{,}5) \right| = \left| y - c \right|$. Mencari nilai $c$ integer yang meminimumkan $\left| y - c \right|$ pada dasarnya adalah definisi dari operasi pembulatan ke bilangan bulat terdekat terhadap $y$. Dengan demikian dapat disimpulkan bahwa:

$$
c^\ast = \mathrm{round}(g_x - 0{,}5)
$$

Hasil ini persis merupakan formula diskritisasi yang digunakan pada sistem, dengan tambahan operasi pemangkasan nilai atau clipping agar indeks sel yang dihasilkan selalu berada pada rentang indeks yang valid meskipun terdapat noise pengukuran atau posisi penanda yang berada tepat di tepi area grid. Formula lengkap untuk kolom dan baris masing-masing dinyatakan sebagai:

$$
c = \mathrm{clip}\big(\mathrm{round}(g_x - 0{,}5),\ 0,\ 7\big)
$$

$$
r = \mathrm{clip}\big(\mathrm{round}(g_y - 0{,}5),\ 0,\ 3\big)
$$

Operasi clip pada kedua persamaan di atas diperlukan karena hasil deteksi penanda pada praktiknya tidak selalu berada tepat pada rentang koordinat grid yang ideal. Ketidaksempurnaan kalibrasi, noise pada citra, maupun posisi fisik penanda yang berada sangat dekat dengan batas terluar area kerja dapat menghasilkan nilai $g_x$ yang sedikit kurang dari nol atau sedikit lebih dari delapan. Tanpa adanya operasi clip, kondisi tersebut akan menghasilkan indeks sel yang tidak valid, misalnya $c = -1$ atau $c = 8$, yang tidak merepresentasikan sel manapun pada grid fisik yang sebenarnya berukuran 8 kolom dan 4 baris.

Aturan diskritisasi di atas memiliki sifat penting yang menjamin tidak adanya area kosong atau dead zone pada bidang grid, yaitu area di mana sebuah posisi fisik tidak dapat ditetapkan ke sel manapun. Sifat ini dapat dibuktikan dengan meninjau dua sel yang saling bertetangga, misalnya sel dengan indeks $c$ dan sel dengan indeks $c+1$, yang masing-masing memiliki titik pusat pada $c + 0{,}5$ dan $c + 1{,}5$. Batas atau garis pemisah di antara kedua sel tersebut secara alami berada pada titik tengah di antara kedua titik pusat tersebut, yang dapat dihitung sebagai:

$$
\text{titik tengah} = \frac{(c + 0{,}5) + (c + 1{,}5)}{2} = c + 1
$$

Hasil perhitungan di atas menunjukkan bahwa titik tengah antara dua sel yang bertetangga selalu jatuh tepat pada garis grid integer, yaitu $g_x = c + 1$, yang secara fisik merupakan batas nyata antara dua sel berukuran 0,3 meter kali 0,3 meter pada lantai gudang. Karena fungsi pembulatan terdefinisi untuk setiap nilai bilangan real, kecuali pada kasus khusus di mana nilai tersebut berada tepat pada titik tengah dua bilangan bulat yang ditangani melalui konvensi pembulatan standar, maka setiap titik koordinat kontinu pada bidang grid akan selalu dipetakan ke tepat satu sel diskrit, tanpa terkecuali. Struktur pembagian ruang seperti ini, di mana setiap titik pada bidang dipetakan ke wilayah dengan titik pusat terdekat, dikenal dalam literatur geometri komputasi sebagai partisi Voronoi. Konsekuensi praktis dari sifat ini adalah bahwa perpindahan indeks sel pada sistem lokalisasi hanya akan terjadi ketika posisi fisik penanda benar-benar melewati garis batas tengah antar sel, sehingga pergerakan penanda pada lantai gudang akan selalu terepresentasikan secara konsisten dan tidak pernah terjebak pada wilayah yang tidak terdefinisi.
