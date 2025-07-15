# Laporan Proyek Machine Learning - Sistem Rekomendasi Buku
- **Nama:** Fitria Irfani Mutashim
- **ID Dicoding:** MC008D5X1800

## Project Overview

### I. Latar Belakang Masalah
Selama beberapa dekade terakhir, dengan munculnya Youtube, Amazon, Netflix, dan banyak layanan web lainnya, sistem rekomendasi semakin banyak digunakan dalam kehidupan kita. Mulai dari e-commerce (menyarankan artikel yang menarik bagi pembeli) hingga iklan daring (menyarankan konten yang tepat kepada pengguna, yang sesuai dengan preferensi mereka), sistem rekomendasi kini tidak dapat dihindari dalam perjalanan daring kita sehari-hari.
Secara umum, sistem rekomendasi adalah algoritma yang ditujukan untuk menyarankan item yang relevan kepada pengguna (item tersebut berupa film untuk ditonton, teks untuk dibaca, produk untuk dibeli, atau apa pun, tergantung pada industrinya).

Sistem rekomendasi sangat penting dalam beberapa industri karena dapat menghasilkan pendapatan yang sangat besar jika efisien atau juga menjadi cara untuk menonjol secara signifikan dari pesaing. Sebagai bukti pentingnya sistem rekomendasi, dalam konteks layanan perpustakaan digital atau toko buku daring dengan ratusan ribu bahkan jutaan judul buku, sistem rekomendasi ini membantu pengguna menemukan buku yang tepat.

### II. Referensi
> 1. Akbar, R. dkk (2023).*Sistem Rekomendasi Buku Dengan Collaborative Filtering Menggunakan Metode Singular Value Decomposition (SVD)*. e-Proceeding of Engineering. ISSN: 2355-9365 Vol.10, No.5.
> 2. A. H. Ritdrix, and P. W. Wirawan (2018). *Sistem Rekomendasi Buku Menggunakan Metode Item-Based Collaborative Filtering*. Jurnal Masyarakat Informatika. DOI: https://doi.org/10.14710/jmasif.9.2.31482.

## **Business Understanding**

### I. Problem Statements
1. Bagaimana sistem dapat merekomendasikan buku-buku yang paling populer dan disukai secara umum oleh pengguna?
2. Dapatkah sistem menyaring dan merekomendasikan buku berkualitas tinggi berdasarkan penilaian dari reviewer ahli (pengguna aktif)?

### II. Goals
1. Menghasilkan daftar rekomendasi buku berdasarkan popularitas.
2. Menghasilkan daftar rekomendasi buku yang terkurasi, berdasarkan rating dari reviewer ahli yang telah memberikan ulasan dalam jumlah signifikan.

### III. Solution Statements
- Mengembangkan sistem rekomendasi berbasis popularitas (Content Based).
- Mengembangkan sistem filtering berbasis reviewer ahli dengan cara menyaring ulasan dari reviewer ahli (Filtering Based).
- Menyusun dua daftar rekomendasi:
1. Buku terpopuler secara umum.
2. Buku dengan rating tertinggi dari reviewer ahli.
- Sistem ini tidak bergantung pada histori personal pengguna sehingga sangat cocok untuk mengatasi masalah cold-start (pengguna baru).

## Data Understanding

Buku diidentifikasi berdasarkan ISBN. Beberapa informasi berbasis konten tersedia dalam dataset ini (Judul Buku, Penulis Buku, Tahun Terbit, Penerbit) yang diperoleh dari Amazon Web Services. Pada dataset ini, jika ada beberapa penulis maka hanya penulis pertama yang dituliskan. Terdapat URL yang menghubungkan ke gambar sampul buku, ada tiga bentuk berbeda (URL Gambar-S, URL Gambar-M, URL Gambar-L), yaitu kecil, sedang, besar. URL ini mengarah ke situs web Amazon.

Dataset yang digunakan dalam proyek ini bersumber dari Kaggle: [Book Recommendation Dataset](https://www.kaggle.com/datasets/arashnic/book-recommendation-dataset/data).

Dataset terdiri dari 3 file dengan masing-masing berisi:
1. **Users:** 278.858 sampel
2. **Books:** 271.360 sampel
3. **Ratings:** 1.149.780 sampel

### Variabel

Dataset yang digunakan terdiri dari 3 file berikut:
1. **Users:** Berisi fitur ID pengguna (`User-ID`), Lokasi Pengguna (`Location`), dan Usia (`Age`).
2.  **Books:** Berisi fitur `ISBN`, Judul Buku (`Book-Title`), Penulis Buku (`Book-Author`), Tahun Terbit (`Year-Of-Publication`), Penerbit (`Publisher`), dan URL yang menautkan ke gambar sampul terdapat tiga bentuk berbeda (`Image-URL-S`, `Image-URL-M`, dan `Image-URL-L`).
3. **Ratings:** Berisi fitur ID pengguna (`User-ID`), `ISBN`, dan Hasil Penilaian (`Book-Rating`) yang dinyatakan dalam skala 1-10 (nilai yang lebih tinggi menunjukkan apresiasi yang lebih tinggi).

### EDA
- Dataset `users` berisi informasi tentang pengguna, terdiri dari **278.858 baris** dan **3 kolom** dengan fitur:
    1. **User-ID** bertipe data Integer. Merupakan identifikasi unik untuk setiap pengguna.
    2. **Location** bertipe data Object (string). Berisi informasi lokasi pengguna dalam format `kota, provinsi, negara`.
    3. **Age** bertipe data Float. Usia pengguna dicatat dalam satuan tahun.

- Dataset `books` terdiri dari **271.360 entri** dengan **8 kolom** yang semuanya bertipe *object* (string). Kolom-kolom ini mencakup informasi seperti:
    1. `ISBN`: Nomor unik identifikasi buku.
    2. `Book-Title`: Judul buku.
    3. `Book-Author`: Nama penulis buku (2 nilai kosong).
    4. `Year-Of-Publication`: Tahun terbit buku.
    5. `Publisher`: Nama penerbit (2 nilai kosong).
    6. `Image-URL-S`, `Image-URL-M`, `Image-URL-L`: URL gambar buku dalam tiga ukuran berbeda (3 nilai kosong pada URL-L).

    Secara keseluruhan, dataset ini cukup bersih dan kaya akan informasi metadata buku yang bermanfaat untuk sistem rekomendasi berbasis konten maupun kolaboratif.

- Dataset `ratings` terdiri dari **1.149.780 entri** dengan **3 kolom**:
    1. `User-ID`: Identifikasi pengguna (int64).
    2. `ISBN`: Identifikasi buku (object).
    3. `Book-Rating`: Nilai rating yang diberikan (int64).

    Semua kolom tidak memiliki nilai kosong. Statistik dari kolom `Book-Rating` menunjukkan:
    - **Mean**: 2.87
    - **Standard Deviation**: 3.85
    - **Minimum**: 0
    - **Maximum**: 10
    - **50% nilai rating (median)**: 0
    
    Mayoritas nilai rating bernilai 0, mengindikasikan bahwa sebagian besar entri kemungkinan merupakan *implicit feedback* (misalnya buku dilihat tetapi tidak diberi rating eksplisit). Hal ini menunjukkan bahwa data rating bersifat sparse dan perlu penanganan lanjutan, seperti pemisahan antara rating eksplisit dan implisit, untuk meningkatkan kualitas hasil rekomendasi.

- Dilakukan pengecekan missing value pada data `books`, karena data `books` merupakan data utama yang digunakan. Kemudian dari hasi pengecekan missing value terdapat sebanyak 7 data, dengan perincian 2 data kosong pada fitur `Book-Author`, 2 data kosong pada fitur `Publisher`, dan 3 data kosong pada fitur `Image-URL-L`
- Dilakukan pengecekan apakah terdapat duplikasi data pada data `books`. Namun, tidak ditemukan adanya data yang duplikasi.
- Dari hasil EDA yang dilakukan, dapat disimpulkan bahwa:
1. **Dataset yang digunakan**:
  - `ratings`: memberikan informasi tentang penilaian pengguna terhadap buku.
  - `books`: memberikan metadata buku, termasuk judul buku (`Book-Title`) yang digunakan sebagai kunci analisis.
2. **Dataset yang tidak digunakan**: 
  - `users`: tidak dilibatkan dalam proses ini karena fokus analisis adalah pada buku, bukan pengguna.


## Data Preparation

- Baris data yang hilang dilakukan dropping data
    ```python
    books.dropna(inplace = True)
    ```
    dari data `books` awal sebanyak 271.360 data menjadi 271.353.
- Penggabungan Data `ratings` dan `books`
    ```python
    ratings_with_name = ratings.merge(books, on='ISBN')
    ```
    Langkah ini menggabungkan dataframe `ratings` dan `books` berdasarkan kolom ISBN. Hasilnya adalah dataframe `ratings_with_name` yang berisi data rating yang telah dilengkapi informasi buku seperti judul, penulis, dan penerbit.
- Tahap pembagian Train dan Test Data
    ```python
    from sklearn.model_selection import train_test_split
    # Pisahkan data interaksi menjadi train & test
    train_data, test_data = train_test_split(ratings_with_name, test_size=0.2)
    ```
    Tahapan ini membagi data interaksi pengguna dan buku (`ratings_with_name`) menjadi dua bagian: `train_data` (80%) untuk melatih sistem dan `test_data` (20%) untuk menguji efektivitas rekomendasi.

    1. Proses Pengolahan Data Training
        ```python
        num_rating_train = train_data.groupby('Book-Title').count()['Book-Rating'].reset_index()
        num_rating_train.rename(columns={'Book-Rating':'Num-Ratings'}, inplace=True)
        ```
        Menghitung jumlah rating yang diterima masing-masing buku dalam data latih, kemudian mengganti nama kolom menjadi `Num-Ratings`.

        ```python
        avg_rating_train = (
        train_data.groupby('Book-Title')['Book-Rating']
        .mean()
        .reset_index()
        )
        avg_rating_train.rename(columns={'Book-Rating': 'Avg-Rating'}, inplace=True)
        ```
        Menghitung rata-rata rating untuk setiap buku dalam data latih, hasilnya disimpan sebagai kolom `Avg-Rating`.

        ```python
        popular_books_train = num_rating_train.merge(avg_rating_train, on="Book-Title")
        ```
        Menggabungkan data jumlah rating dan rata-rata rating menjadi satu dataframe berdasarkan judul buku.
        
        ```python
        most_popular_books_train = popular_books_train[popular_books_train['Num-Ratings'] >= 400].sort_values('Avg-Rating', ascending=False)
        ```
        Menyaring buku yang memiliki minimal 400 rating dan mengurutkannya berdasarkan rating tertinggi terlebih dahulu. Ini digunakan untuk mendeteksi buku populer berdasarkan persepsi banyak pengguna.

        ```python
        most_popular_books_train = most_popular_books_train.merge(books, on="Book-Title").drop_duplicates('Book-Title')[['Book-Title', 'Book-Author', 'Image-URL-M', 'Num-Ratings', 'Avg-Rating']]
        ```
        Menggabungkan informasi detail buku (penulis dan gambar) dari dataframe `books`, lalu memilih kolom yang relevan dan menghapus duplikat berdasarkan judul buku.
    2. Proses Pengolahan Data Testing
        ```python
        num_rating_test = test_data.groupby('Book-Title').count()['Book-Rating'].reset_index()num_rating_test.rename(columns={'Book-Rating':'Num-Ratings'}, inplace=True)
        ```
        Langkah ini identik dengan proses training, namun diterapkan pada data testing untuk menghitung jumlah rating tiap buku.

        ```python
        avg_rating_test = (
            test_data.groupby('Book-Title')['Book-Rating']
            .mean()
            .reset_index()
        )
        avg_rating_test.rename(columns={'Book-Rating': 'Avg-Rating'}, inplace=True)
        ```
        Menghitung rata-rata rating buku berdasarkan data testing.

        ```python
        popular_books_test = num_rating_test.merge(avg_rating_test, on="Book-Title")
        ```
        Menggabungkan jumlah rating dan rata-rata rating dalam test set berdasarkan judul buku.
        
        ```python
        most_popular_books_test = popular_books_test[popular_books_test['Num-Ratings'] >= 80].sort_values('Avg-Rating', ascending=False)
        ```
        Menyaring buku yang setidaknya memiliki 80 rating pada test set dan mengurutkannya berdasarkan rating tertinggi.

        ```python
        most_popular_books_test = most_popular_books_test.merge(books, on="Book-Title").drop_duplicates('Book-Title')[
            ['Book-Title', 'Book-Author', 'Image-URL-M', 'Num-Ratings', 'Avg-Rating']
        ]
        ```
        Menggabungkan hasil rekomendasi dari test set dengan metadata buku untuk menampilkan rekomendasi yang utuh.
    
    3. Inisialisasi Hasil Train dan Test
        ```python
        # Menampilkan hasil train dan test
        relevant = most_popular_books_train['Book-Title'].head(10).tolist()
        recommended = most_popular_books_test['Book-Title'].head(10).tolist()
        ```
        Baris kode ini mengambil 10 judul buku teratas berdasarkan nilai rata-rata rating dari data train dan test.
        - `relevant` merepresentasikan daftar buku populer dari data training, yang dianggap relevan sebagai acuan sistem rekomendasi.
        - `recommended` merepresentasikan daftar buku rekomendasi hasil dari model menggunakan data testing.
        Tujuannya adalah untuk membandingkan hasil rekomendasi model (recommended) dengan buku-buku yang memang populer dan disukai pengguna (relevant) dari data pelatihan, sebagai bagian dari proses evaluasi sistem.

- Menyaring Reviewer Ahli (Experienced Users)
    ```python
    x = ratings_with_name.groupby("User-ID").count()['Book-Rating'] > 200
    experienced_users = x[x].index
    ```
    Di sini sistem mengidentifikasi pengguna aktif atau reviewer ahli, yaitu mereka yang telah memberikan banyak penilaian (>200 buku). Ini berguna untuk filtering berdasarkan kepercayaan terhadap pengguna aktif.
    
    ``` python
    filtered_ratings = ratings_with_name[ratings_with_name['User-ID'].isin(experienced_users)]
    filtered_ratings
    ```
    Hasilnya adalah data rating yang hanya berasal dari reviewer ahli. Ini meningkatkan kualitas evaluasi buku karena fokus pada penilaian dari pengguna yang terlibat aktif.
- Menyaring Buku yang Sering Dinilai oleh Reviewer Ahli
    ``` python
    y = filtered_ratings.groupby('Book-Title').count()['Book-Rating']>=100
    famous_books = y[y].index
    ```
    Buku-buku yang masuk dalam `famous_books` dianggap valid untuk analisis lanjut karena memiliki banyak penilaian dari reviewer ahli (≥100). Ini menghindari bias dari jumlah penilaian yang terlalu kecil.

    ```python
    final_ratings = filtered_ratings[filtered_ratings['Book-Title'].isin(famous_books)]
    ```
    Menyaring data rating buku, hanya menyisakan buku-buku yang dianggap populer, yaitu buku yang telah menerima setidaknya 100 rating dari pengguna berpengalaman (pengguna yang telah memberi lebih dari 200 rating) dimasukkan dalam dataframe bernama `final_ratings`.

## Modeling dan Hasil

Pada tahap modeling, saya akan menggunakan dua pendekatan sistem rekomendasi yang saling melengkapi untuk menghasilkan hasil yang lebih robust, yaitu:

1. **Content-Based Filtering**
    Pendekatan ini digunakan untuk merekomendasikan buku berdasarkan karakteristik atau atribut tertentu dari buku itu sendiri, dalam hal ini jumlah rating terbanyak dan rata-rata rating tertinggi. Buku yang sering dinilai dan mendapatkan rating tinggi diasumsikan memiliki kualitas atau daya tarik yang tinggi di mata banyak pengguna, sehingga layak untuk direkomendasikan secara umum. Pendekatan ini sangat berguna terutama saat informasi interaksi pengguna masih terbatas atau ketika ingin memberikan rekomendasi populer yang bersifat umum.
2. **Collaborative-Based Filtering (menggunakan SVD)**
    Pendekatan ini memanfaatkan data historis interaksi pengguna–dalam bentuk rating–untuk mempelajari pola preferensi dan merekomendasikan buku yang serupa dengan buku yang disukai oleh pengguna lain yang memiliki pola perilaku serupa. Dengan menerapkan algoritma Singular Value Decomposition (SVD), sistem dapat mengisi kekosongan data (sparse matrix) dan memprediksi rating yang mungkin diberikan oleh pengguna terhadap buku yang belum mereka nilai. Pendekatan ini lebih bersifat personal dan akurat karena mempertimbangkan pengalaman pengguna lain sebagai dasar rekomendasi.

Dengan menggabungkan kedua pendekatan ini, sistem dapat memberikan rekomendasi buku yang tidak hanya populer secara umum tetapi juga relevan secara personal bagi masing-masing pengguna.

### I. Content Filtering

Pada proses ini, digunakan pendekatan sederhana berbasis **content filtering** yang menyeleksi buku-buku berdasarkan dua fitur utama: **jumlah rating terbanyak** dan **rata-rata rating tertinggi**. Pendekatan ini mengasumsikan bahwa buku yang sering dinilai dan mendapat rating tinggi cenderung disukai oleh banyak pengguna, sehingga layak untuk direkomendasikan.

- Menampilkan 10 buku teratas dari hasil seleksi
    ```python
    # Melihat 10 profil buku rekomendasi yang memiliki penilaian terbanyak dengan nilai tertinggi
    for i in range(10):
        print(f"\nBuku #{i+1}")
        print("Judul        :", most_popular_books_test['Book-Title'].iloc[i])
        print("Penulis      :", most_popular_books_test['Book-Author'].iloc[i])
        print("Jumlah Rating:", most_popular_books_test['Num-Ratings'].iloc[i])
        print("Rata-rata    :", round(most_popular_books_test['Avg-Rating'].iloc[i], 2))
        display(Image(url=most_popular_books_test['Image-URL-M'].iloc[i]))
    ```
    Akhirnya, ditampilkan sepuluh buku teratas berdasarkan seleksi tersebut, mencakup informasi judul, penulis, jumlah penilaian, rating rata-rata, serta gambar sampulnya. Berikut hasil yang ditampilkan:
    1. Buku #1
        - Judul        : Harry Potter and the Prisoner of Azkaban (Book 3)
        - Penulis      : J. K. Rowling
        - Jumlah Rating: 88
        - Rata-rata    : 6.12
    2. Buku #2
        - Judul        : Harry Potter and the Chamber of Secrets (Book 2)
        - Penulis      : J. K. Rowling
        - Jumlah Rating: 125
        - Rata-rata    : 5.42
    3. Buku #3
        - Judul        : To Kill a Mockingbird
        - Penulis      : Harper Lee
        - Jumlah Rating: 103
        - Rata-rata    : 5.26
    4. Buku #4
        - Judul        : The Catcher in the Rye
        - Penulis      : J.D. Salinger
        - Jumlah Rating: 105
        - Rata-rata    : 5.03
    5. Buku #5
        - Judul        : Tuesdays with Morrie: An Old Man, a Young Man, and Life's Greatest Lesson
        - Penulis      : MITCH ALBOM
        - Jumlah Rating: 100
        - Rata-rata    : 4.94
    6. Buku #6
        - Judul        : Harry Potter and the Sorcerer's Stone (Harry Potter (Paperback))
        - Penulis      : J. K. Rowling
        - Jumlah Rating: 111
        - Rata-rata    : 4.93
    7. Buku #7
        - Judul        : The Five People You Meet in Heaven
        - Penulis      : Mitch Albom
        - Jumlah Rating: 82
        - Rata-rata    : 4.85
    8. Buku #8
        - Judul        : The Da Vinci Code
        - Penulis      : Dan Brown
        - Jumlah Rating: 178
        - Rata-rata    : 4.76
    9. Buku #9
        - Judul        : Life of Pi
        - Penulis      : Yann Martel
        - Jumlah Rating: 142
        - Rata-rata    : 4.7
    10. Buku #10
        - Judul        : Fahrenheit 451
        - Penulis      : Ray Bradbury
        - Jumlah Rating: 81
        - Rata-rata    : 4.62

### II. Collaborative Filtering

Collaborative Filtering merupakan salah satu pendekatan paling populer dalam sistem rekomendasi yang berfokus pada pola interaksi antar pengguna dan item (dalam hal ini, buku). Pendekatan ini tidak memerlukan informasi konten dari buku, melainkan hanya mengandalkan rating atau feedback yang diberikan pengguna. Prinsip utamanya adalah: jika dua pengguna memiliki preferensi yang mirip di masa lalu, maka mereka cenderung menyukai item yang sama di masa depan.

Dalam proyek ini, collaborative filtering diterapkan menggunakan algoritma Singular Value Decomposition (SVD) dari library Surprise. SVD mampu mengatasi permasalahan umum dalam data rating seperti sparsity (banyaknya nilai kosong) dengan memetakan pengguna dan item ke dalam dimensi laten. Hasilnya adalah model yang dapat memprediksi seberapa besar kemungkinan seorang pengguna akan menyukai buku tertentu, meskipun belum pernah memberi rating untuk buku tersebut.

- Modeling
    
    Berikut adalah penjelasan setiap langkah dalam proses modeling dengan algoritma SVD (Singular Value Decomposition) menggunakan library Surprise, yang digunakan untuk membangun sistem rekomendasi berbasis collaborative filtering:
    ```python
    from surprise.model_selection import train_test_split
    ```
    Import fungsi `train_test_split` dari `surprise`.model_selection yang digunakan untuk membagi data menjadi data latih (training) dan data uji (testing).

    ```python
    # Define the rating scale
    reader = Reader(rating_scale=(0, 10))
    ```
    Inisialisasi objek `Reader` yang digunakan untuk mendefinisikan skala rating yang digunakan dalam dataset, yaitu dari 0 sampai 10. Ini penting agar Surprise tahu rentang nilai rating yang sah.

    ```python
    # Load the data into Surprise's dataset format
    data = Dataset.load_from_df(final_ratings[['User-ID', 'Book-Title', 'Book-Rating']], reader)
    ```
    Mengubah data `final_ratings` ke format khusus yang dikenali oleh library Surprise. Format yang digunakan hanya mencakup tiga kolom: `User-ID`, `Book-Title`, dan `Book-Rating`.
    
    ```python
    # Split the dataset into training and testing sets
    train_set, test_set = train_test_split(data, test_size=0.20, random_state=42)
    ```
    Membagi dataset menjadi dua bagian: **80% data latih** dan **20% data uji**, agar performa model bisa dievaluasi. Parameter `random_state=42` digunakan untuk memastikan bahwa pembagian data bersifat reproducible.

    ```python
    # Define the SVD algorithm
    model = SVD()
    ```
    Mendefinisikan model SVD (Singular Value Decomposition) yang digunakan untuk memprediksi rating yang belum diberikan oleh pengguna berdasarkan pola rating tersembunyi (latent features) yang ditemukan dari data training.

    ```python
    # Train the algorithm on the training set
    model.fit(train_set)
    ```
    Melatih model SVD menggunakan data latih. Setelah model ini dilatih, ia dapat memprediksi berapa nilai rating yang kemungkinan besar akan diberikan oleh seorang pengguna terhadap buku yang belum pernah ia baca/rating sebelumnya.

- Membuat Fungsi Rekomendasi
    ```python
    def recommend_books(user_id, n=10):
        # List all unique book titles
        all_books = final_ratings['Book-Title'].unique()

        # Remove books already rated by the user
        rated_books = final_ratings[final_ratings['User-ID'] == user_id]['Book-Title'].values
        books_to_predict = [book for book in all_books if book not in rated_books]

        # Predict ratings for remaining books
        predictions = []
        for book in books_to_predict:
            pred = model.predict(user_id, book)
            predictions.append((book, pred.est))

        # Sort predictions by estimated rating
        predictions.sort(key=lambda x: x[1], reverse=True)

        # Get top N recommendations
        top_n = predictions[:n]

        return top_n
    ```
    Penjelasan Fungsi:
    - `all_books = final_ratings['Book-Title'].unique()` digunakan untuk mengambil semua judul buku unik dari data rating akhir (`final_ratings`) sebagai kandidat yang akan dievaluasi untuk rekomendasi.
    - Menghindari duplikasi rekomendasi
        ```python
        rated_books = final_ratings[final_ratings['User-ID'] == user_id]['Book-Title'].values
        books_to_predict = [book for book in all_books if book not in rated_books]
        ```
        Digunakan untuk mengecualikan buku-buku yang sudah pernah dinilai oleh pengguna, karena sistem hanya ingin merekomendasikan buku baru yang belum dibaca.
    - Prediksi Rating oleh Model SVD:
        ```python
        for book in books_to_predict:
            pred = model.predict(user_id, book)
            predictions.append((book, pred.est))
        ```
        SVD memprediksi skor rating pengguna terhadap buku yang belum pernah dia nilai berdasarkan pola interaksi pengguna lain yang mirip.
    - Pengurutan berdasarkan skor prediksi tertinggi:
        ```python
        predictions.sort(key=lambda x: x[1], reverse=True)
        top_n = predictions[:n]
        ```
        Dari seluruh prediksi, sistem mengurutkan buku dengan estimasi rating tertinggi dan mengambil n teratas sebagai hasil rekomendasi.

- Menjalankan Fungsi untuk Melihat Hasil Rekomendasi
    ```python
    from IPython.display import Image, display
    ```
    Import modul `Image` dan `display` dari IPython, digunakan untuk menampilkan gambar sampul buku secara visual di Google Colab atau Jupyter Notebook.

    ```python
    # Make book recommendations
    user_id = 271705
    recommended_books = recommend_books(user_id)
    ```
    Memanggil fungsi `recommend_books()` dengan `user_id = 271705`, yang akan mengembalikan 10 buku teratas yang direkomendasikan untuk pengguna tersebut berdasarkan prediksi dari model SVD. Output-nya adalah list berisi pasangan (`Book-Title`, `estimated_rating`).

- Menampilkan 10 buku teratas dari hasil seleksi
    ```python
    print(f"Top 10 recommended books for user {user_id}:\n")
    for i, (title, _) in enumerate(recommended_books, start=1):
        # Filter baris data berdasarkan judul
        book_info = final_ratings[final_ratings['Book-Title'] == title].iloc[0]

        # Ambil nama penulis dan URL gambar
        author_name = book_info['Book-Author'] if 'Book-Author' in book_info else "Tidak diketahui"
        image_url = book_info['Image-URL-M'] if 'Image-URL-M' in book_info else None

        # Tampilkan informasi
        print(f"Buku #{i}")
        print(f"Judul  : {title}")
        print(f"Penulis: {author_name}\n")

        # Tampilkan gambar jika URL tersedia
        if image_url:
            display(Image(url=image_url))
    ```
    Akhirnya, ditampilkan sepuluh buku yang direkomendasikan, mencakup informasi judul, penulis, serta gambar sampulnya. Berikut hasil yang ditampilkan:
    1. Buku #1
        - Judul  : Harry Potter and the Chamber of Secrets (Book 2)
        - Penulis: J. K. Rowling
    2. Buku #2
        - Judul  : Harry Potter and the Goblet of Fire (Book 4)
        - Penulis: J. K. Rowling
    3. Buku #3
        - Judul  : Harry Potter and the Order of the Phoenix (Book 5)
        - Penulis: J. K. Rowling
    4. Buku #4
        - Judul  : The Firm
        - Penulis: John Grisham
    5. Buku #5
        - Judul  : The Prince of Tides
        - Penulis: Pat Conroy
    6. Buku #6
        - Judul  : The Gift
        - Penulis: Madeena S. Nolan
    7. Buku #7
        - Judul  : The Color Purple
        - Penulis: Alice Walker
    8. Buku #8
        - Judul  : Interview with the Vampire
        - Penulis: Anne Rice
    9. Buku #9
        - Judul  : All I Really Need to Know
        - Penulis: ROBERT FULGHUM
    10. Buku #10
        - Judul  : A Painted House
        - Penulis: JOHN GRISHAM   

## Evaluation

Evaluasi dilakukan untuk mengukur kinerja dan efektivitas dari dua pendekatan sistem rekomendasi yang telah dibangun, yaitu Content-Based Filtering dan Collaborative Filtering berbasis SVD. Masing-masing pendekatan memiliki karakteristik dan metrik evaluasi yang berbeda, disesuaikan dengan tujuan dan data yang digunakan.

## 1. **Evaluasi Content-Based Filtering**

  Pada pendekatan content-based, sistem merekomendasikan buku berdasarkan jumlah rating terbanyak dan rata-rata rating tertinggi. Evaluasi dilakukan menggunakan dua metrik umum yaitu:
  
  1. Precision@K
  
  Precision@K mengukur seberapa relevan hasil rekomendasi yang diberikan, dengan rumus:

  $$
  \text{Precision@K} = \frac{|\text{Recommended}_k \cap \text{Relevant}|}{K}
  $$
 
  Semakin tinggi nilai precision, maka semakin besar proporsi buku yang direkomendasikan benar-benar relevan.

  2. Recall@K

  Recall@K mengukur seberapa banyak dari total item relevan yang berhasil direkomendasikan oleh sistem:

  $$
  \text{Recall@K} = \frac{|\text{Recommended}_k \cap \text{Relevant}|}{|\text{Relevant}|}
  $$
 
  Nilai recall yang tinggi menunjukkan sistem mampu menjangkau sebagian besar buku yang relevan.

## 2. **Evaluasi Collaborative-Based Filtering (SVD)**

  Berbeda dari pendekatan sebelumnya, collaborative filtering berbasis Singular Value Decomposition (SVD) tidak bergantung pada informasi konten buku, melainkan memanfaatkan interaksi historis antar pengguna dan item. Evaluasi dilakukan menggunakan metrik Root Mean Squared Error (RMSE), yang menghitung seberapa jauh prediksi sistem dari nilai rating aktual.

  Rumus matematis RMSE adalah:
  $$
  \text{RMSE} = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (\hat{y}_i - y_i)^2}
  $$

  Di mana:  
  - $n$ adalah jumlah total prediksi,  
  - $\hat{y}_i$ adalah nilai prediksi ke-i,  
  - $y_i$ adalah nilai aktual ke-i.

### Evaluasi Content-Based Filtering

Kode Evaluasi
```python
def precision_at_k(recommended_items, relevant_items, k):
    top_k = recommended_items[:k]
    relevant = set(top_k) & set(relevant_items)
    return len(relevant) / k

def recall_at_k(recommended_items, relevant_items, k):
    top_k = recommended_items[:k]
    relevant = set(top_k) & set(relevant_items)
    return len(relevant) / len(relevant_items)

print("Precision@3:", precision_at_k(recommended, relevant, 10))
print("Recall@3:", recall_at_k(recommended, relevant, 10))
```
Penjelasan:
- Fungsi `precision_at_k()` menghitung seberapa banyak item yang direkomendasikan dalam `top-k` yang benar-benar relevan bagi user.
- Fungsi `recall_at_k()` menghitung seberapa banyak item relevan (dari semua yang relevan) yang berhasil ditangkap dalam `top-k` rekomendasi.
- Variabel `recommended` berisi 10 buku hasil rekomendasi dari sistem.
- Variabel `relevant` berisi 10 buku yang dianggap relevan (dalam konteks ini diambil dari data pelatihan).
- Kode ini mengevaluasi sistem rekomendasi dengan **K = 10**.

```python
Hasil Evaluasi:
    Precision@3: 0.5
    Recall@3: 0.5
```
- Precision@10 = 0.5 → 5 dari 10 rekomendasi termasuk buku relevan.
- Recall@10 = 0.5 → Dari 10 buku relevan, 5 berhasil direkomendasikan sistem.
- Metrik ini menunjukkan bahwa sistem mampu memberikan rekomendasi yang cukup tepat dan relevan terhadap preferensi pengguna berdasarkan karakteristik buku (misalnya judul dan genre).

### Evaluasi Collaborative-Based Filtering

Kode evaluasi
```python
# Make predictions on the test set
predictions = model.test(test_set)

# Evaluate the model
accuracy.rmse(predictions)
```
```python
Hasil Evaluasi:
    RMSE: 3.5880
    3.5879665729920953
```
- Nilai **RMSE yang lebih kecil** menunjukkan bahwa prediksi model lebih dekat dengan data aktual.
- Dalam hasil di atas, nilai **RMSE = 3.5880**, artinya secara rata-rata, prediksi model menyimpang sekitar 3.59 satuan dari nilai sebenarnya.
- Meskipun sistem dapat mempelajari pola dari data interaksi pengguna, nilai RMSE tersebut tergolong cukup besar, yang berarti akurasi prediksi rating masih kurang optimal. Hal ini bisa disebabkan oleh data yang sparse (jarang) atau kurangnya informasi pengguna/buku yang cukup beragam.

## Penutup

### Kesimpulan

Content-Based Filtering memberikan hasil yang lebih terkontrol dan dapat dijelaskan (explainable), karena berdasarkan informasi buku yang sudah diketahui.

Collaborative Filtering (SVD) berpotensi memberikan rekomendasi yang lebih personal, tetapi lebih rentan terhadap masalah data sparsity dan cold-start (pengguna/buku baru).

### Saran

Untuk hasil yang lebih optimal, dapat dipertimbangkan pendekatan hybrid yang menggabungkan kedua metode agar saling melengkapi kekurangan masing-masing.