# KODE PROGRAM
    import random #mengimpor library random yang digunakan untuk menghasilkan nilai secara acak
    import matplotlib.pyplot as plt #library ini digunakan untuk membuat visualisasi grafik

# DATA (SUDAH FIX)
    subjects = ["AI", "DB", "WEB", "ML", "IOT"] #daftar mata pelajaran yang ingin di jadwalkan

# ditambah agar cukup
    times = ["Pagi", "Siang", "Sore", "Malam", "Dini"] #daftar slot waktu yang tersedia

    POP_SIZE = 30 #menentukan jumlah individu dalam satu populasi
    GENERATIONS = 100 #jumlah maksimal iterasi evolusi
    MUTATION_RATE = 0.2 #menentukan peluang mutasi

# INIT
    def create_schedule(): #mendefinisikan fungsi untuk membuat satu jadwal
    return [random.choice(times) for _ in subjects] #loop sebanyak jumlah mata pelajaran

    def init_population(): #fungsi untuk membuat populasi awal
    return [create_schedule() for _ in range(POP_SIZE)] #membuat 30 jadwal acak

# HITUNG KONFLIK
    def count_conflicts(schedule): #fungsi untuk menghitung berapa banyak bentrok waktu
    used = set() #membuat set kosong
    conflicts = 0 #variabe untuk menghitung jumlah konflik

    for t in schedule: #loop setiap waktu dalam jadwal
        if t in used: #jika waktu sudah pernah di gunakan berarti bentrok
            conflicts += 1 #tambahkan jumlah konflik
        else:
            used.add(t) #jika belum di pakai simpan set

    return conflicts #mengembalikan jumlah konflik

# FITNESS
    def fitness(schedule): #fungsi untuk menilai kualitas jadwal
    conflicts = count_conflicts(schedule) #hitung jumlah konflik terlebih dahulu
    return max(100 - conflicts * 20, 0) #Baris ini dipakai untuk menghitung nilai fitness (kualitas jadwal).

# SELECTION
    def selection(pop): #fungsi untuk memilih individu terbaik
    return max(random.sample(pop, 3), key=fitness) #ambil 3 individu acak dan pilih fitnes paling tinggi

# CROSSOVER
    def crossover(p1, p2): #menggabungkan dua parent untuk membuat anak
    point = random.randint(1, len(p1)-1) #menentukan titik potong secara acak
    return p1[:point] + p2[point:] #menggabungkan bagian awal dari parent 1 dan bagian akhir parent 2

# MUTATION
    def mutate(schedule): #fungsi mutasi 
    for i in range(len(schedule)): #loop setiap gen
        if random.random() < MUTATION_RATE: #jika angka acak <0.2 berarti mutasi terjadi
            schedule[i] = random.choice(times) #mengganti waktu secara acak
    return schedule #kembalikan hasil mutasi

# DECODE
    def decode(schedule): #mengubah jadwal agar mudah di baca
    return list(zip(subjects, schedule)) #menggabungkan waktu dan mata pelajaran

# GA
    def GA(): #fungshi utama algoritma
    pop = init_population() #membuat populasi awal
    best_hist = [] #menyimpan riwayat fitness terbaik tap generasi

    print("\n===== OPTIMASI JADWAL BELAJAR =====\n")

    for gen in range(GENERATIONS): #loop evolusi 

        pop = sorted(pop, key=fitness, reverse=True) #urutkan populasi dari terbaik

        best = pop[0] #ambil individu terbaik
        best_fit = fitness(best) #hitung fitnes terbaik

        best_hist.append(best_fit) #simpan ke histori

        if gen % 5 == 0: #setiap 5 generasi tampilkan progres
            print(f"Gen {gen:3d} | Fitness: {best_fit}") 

        if best_fit == 100 and gen > 5: #jika sudah optimal berarti berhenti
            print("\n Jadwal optimal ditemukan!")
            break

        new_pop = pop[:2] #ambil 2 terbaik di sebut elitism

        while len(new_pop) < POP_SIZE: #isi populasi baru
            p1 = selection(pop)
            p2 = selection(pop) #pilih parent

            child = crossover(p1, p2) #crossover
            child = mutate(child) #mutasi

            new_pop.append(child) #masukkan ke populasi baru

        pop = new_pop #update populasi

  # HASIL AKHIR
    best = sorted(pop, key=fitness, reverse=True)[0] #mengurutkan semua jadwal dalam populasi berdasarkan milai fitness dari yang tertinggi ke terendah
    conflicts = count_conflicts(best) #menghilang jumlah bentrok dari jadwal terbaik

    print("\n===== HASIL AKHIR =====")
    print("Fitness :", fitness(best))

    for subj, t in decode(best): #loop untuk menampilkan satu per satu
        print(f"{subj} → {t}")

    print("\nPenjelasan:")
    if conflicts == 0: #mengecek apakh tidak bentrok
        print("Tidak ada bentrok → jadwal optimal.")
    else:
        print(f"Terdapat {conflicts} bentrok waktu.")
        if len(subjects) > len(times):
            print("Penyebab: slot waktu tidak cukup.") #mengecek apakah jumlah mata pelajaran lebih banyak dari slot waktu

  # VISUAL
    plt.figure() #membuat area untuk grafik
    plt.plot(best_hist) #menggambar grafik dari data 
    plt.title("Perkembangan Fitness") #memebri judul grafik
    plt.xlabel("Generasi") #label sumbu X yaitu gemerasi
    plt.ylabel("Fitness") #label sumbu Y yaitu nilai fitnes
    plt.grid() #menampilkan garis bantu agar grafik lebih mudah dibaca
    plt.show() #menampilkan grafik ke layar

# RUN
    GA() #menjalankan proses dari awal sampa akhir

   ===== OPTIMASI JADWAL BELAJAR =====

    Gen   0 | Fitness: 100
    Gen   5 | Fitness: 100

    Jadwal optimal ditemukan!

  ===== HASIL AKHIR =====

    Fitness : 100
    AI → Sore
    DB → Pagi
    WEB → Dini
    ML → Siang
    IOT → Malam

    Penjelasan:
    Tidak ada bentrok → jadwal optimal.
