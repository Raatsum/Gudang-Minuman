#include <iostream>
using namespace std;

struct dnode
{

    int kode,harga,jmlhKardus;
    string nama;

    dnode* prev = nullptr;
    dnode* next = nullptr;
};

struct TreeNode
{

    string pertanyaan;
    string rekomendasi;
    TreeNode* yes;
    TreeNode* no;

    TreeNode(string p = "", string r = "") {
        pertanyaan = p;
        rekomendasi = r;
        yes = nullptr;
        no = nullptr;
    }
};

struct dllist
{

    dnode* head = nullptr; // pointer yang nunjuk ke elemen pertama
    dnode* tail = nullptr; // elemen terakhir

    void addMinum(int kode, string nama, int jmlhKardus, int harga)
    {
        dnode* tmp = new dnode;
        tmp->kode = kode;
        tmp->nama = nama;
        tmp->jmlhKardus = jmlhKardus;
        tmp->harga = harga;
        if (head == nullptr){
            head = tmp;
            tail = tmp;
        } else{
            tail->next = tmp;
            tmp->prev = tail;
            tail = tmp;
        }
    }

    void lihatData(){
        dnode* cur = head;
        cout << "\n=============================================================\n";
        cout << "KODE \t NAMA \t\t\t JUMLAH \t HARGA(per kardus)\n";
        while (cur != nullptr){
            cout << cur->kode << "\t" << cur->nama << "\t\t" << cur->jmlhKardus << "\tRp" << cur->harga << " \n";
            cur =  cur->next;
        }
        cout << "\n=============================================================\n";
    }
    
    void insertionSort() {
        if (head == nullptr || head->next == nullptr) return;

        dnode* cur = head->next;  // Mulai dari node kedua
        while (cur != nullptr) {
            dnode* walker = cur;

            while (walker->prev != nullptr && walker->prev->kode > walker->kode) {
                // Tukar isi data antara walker dan walker->prev
                int tmpKode = walker->kode;
                string tmpNama = walker->nama;
                int tmpJmlh = walker->jmlhKardus;
                int tmpHarga = walker->harga;

                walker->kode = walker->prev->kode;
                walker->nama = walker->prev->nama;
                walker->jmlhKardus = walker->prev->jmlhKardus;
                walker->harga = walker->prev->harga;

                walker->prev->kode = tmpKode;
                walker->prev->nama = tmpNama;
                walker->prev->jmlhKardus = tmpJmlh;
                walker->prev->harga = tmpHarga;

                walker = walker->prev;  // Geser ke kiri untuk lanjut membandingkan
            }
            cur = cur->next;  // Lanjut ke node berikutnya
        }
    }

    void cari(int kode){
        if (head==nullptr){
            cout << kode << " tidak ada data!!\n";
            return;
        }
        
        dnode* cur = head;
        while(cur != nullptr){
            if(cur->kode == kode){
                cout << "kode: " << cur->kode << ", Nama: " << cur->nama << ", Jumlah: " << cur->jmlhKardus << ", Harga: Rp" << cur->harga << " \n";
                return;
            }
            cur = cur->next;
        }
        cout << "KODE " << kode << " TIDAK DITEMUKAN!!\n";
    }
    
    void binarySearch(int kode){
        dnode* tmp = head;
        int panjang = 0;

        while(tmp != nullptr){
            panjang++;
            tmp = tmp->next;
        }

        int min = 0;  
        int max = panjang - 1;

        while(min<=max){
            int index_mid = min + ((max-min)/2); // 5, 7, 11, 15, 20 = 5,7 
            dnode* mid = head; // 5
            for (int i = 0; i<index_mid; i++){ // i = 1,2; i = 0;
                mid = mid->next; // 7, 11; 5;
            }
            if(mid->kode == kode){ // kode = 5; mid = 11; kode = 5 mid = 5
                cout << "kode: " << mid->kode << ", Nama: " << mid->nama << ", Jumlah: " << mid->jmlhKardus << ", Harga: Rp" << mid->harga << " \n";
                return;
            } else if(mid->kode > kode){ // 11  > 5
                max = index_mid - 1; // 7
            } else if(mid->kode < kode){ 
                min = index_mid + 1;
            }
        }
        cout << "KODE " << kode << " TIDAK DITEMUKAN!!\n";
    }

};

struct TreeRekomendasi
{

    TreeNode* root;
    TreeRekomendasi() {
        root = nullptr;
    }

    void buatPohonRekomendasi() {
        root = new TreeNode{"Apakah stok < 25?", ""};
        root->yes = new TreeNode{"", "Stok sedikit, segera isi ulang!"};
        root->no = new TreeNode{"Apakah harga < 20000?", ""};
        root->no->yes = new TreeNode{"", "Harga murah, cocok untuk promosi."};
        root->no->no = new TreeNode{"", "Stok cukup dan harga tinggi, cocok untuk grosir."};
    }

    void tampilkanRekomendasi(int stok, int harga) {
        TreeNode* cur = root;
        while (cur != nullptr) {
            if (!cur->pertanyaan.empty()) {
                if (cur->pertanyaan == "Apakah stok < 25?") {
                    cur = (stok < 25) ? cur->yes : cur->no;
                } else if (cur->pertanyaan == "Apakah harga < 20000?") {
                    cur = (harga < 20000) ? cur->yes : cur->no;
                } else {
                    break;
                }
            } else {
                break;
            }
        }
        if (cur != nullptr && !cur->rekomendasi.empty()) {
            cout << "Rekomendasi: " << cur->rekomendasi << "\n";
        } else {
            cout << "Rekomendasi: Tidak tersedia.\n";
        }
    }

    void tampilkanSemuaRekomendasi(dnode* head) {
        dnode* cur = head;
        cout << "\n======= Rekomendasi untuk Semua Minuman =======\n";
        while (cur != nullptr) {
            cout << "Kode: " << cur->kode << ", Nama: " << cur->nama
                 << ", Jumlah: " << cur->jmlhKardus << ", Harga: Rp" << cur->harga << "\n";
            tampilkanRekomendasi(cur->jmlhKardus, cur->harga);
            cout << "----------------------------------------\n";
            cur = cur->next;
        }
    }
};

int main(){

    dllist gudang;
    TreeRekomendasi pohon;
    pohon.buatPohonRekomendasi();

    int pilih, kode, harga, jmlhKardus;
    string nama;
    bool menu = true;
    char confirm = ' ';

    gudang.addMinum(1001,"Teh Botol Sosro",90,90000);
    gudang.addMinum(1009,"Susu Dancow",50,140000);
    gudang.addMinum(1008,"Pocari Sweat",20,150000);
    gudang.addMinum(1003,"Coca Cola",80,99000);

     while(menu){
        cout << "\n======================================\n";
        cout << "Silahkan Pilih Menu\n";
        cout << "0. Keluar\n";
        cout << "1. Tambah Data\n";
        cout << "2. Lihat Data\n";
        cout << "3. Sorting\n";
        cout << "4. Cari Data\n";
        cout << "5. Rekomendasi\n";
        cout << "\n======================================\n";
        cout << "Silahkan pilih menu (0-5): ";
        cin >> pilih;

        switch(pilih){
            case 1:
                confirm = ' ';
                while (confirm != 't')
                {
                    cout << "-----------------------------------------\n";
                    cout << "Kode Minuman (4 digit): ";
                    cin >> kode;
                    cout << "Merk Minuman: ";
                    cin.ignore();
                    getline(cin,nama);
                    cout << "jumlah stok kardus: ";
                    cin >> jmlhKardus;
                    cout << "Harga Rp";
                    cin >> harga;
                    gudang.addMinum(kode,nama,jmlhKardus,harga);
                    cout << "\n-----------------------------------------\n";
                    cout << "Apakah ingin menambah data lagi (y/t)? ";
                    cin >> confirm;

                }
                break;
            case 2:
                confirm = ' ';
                while (confirm !='y'){
                    gudang.lihatData();
                    cout << "Apakah ingin kembali ke menu (y/t)? ";
                    cin >> confirm;
                }
                break;
            case 3:
                gudang.insertionSort();
                cout << "Sorting berhasil. Kode telah diurutkan!\n";
                gudang.lihatData();
                break;
            case 4:
                int pilih4;
                cout << "1. kode\n";
                cout << "1. nama\n";
                cin >> pilih4;
                if (pilih4 == 1){
                    cout << "Masukkan kode: ";
                    cin >> kode;
                    gudang.cari(kode);
                } else if (pilih4 == 2){
                    cout << "Masukkan kode: ";
                    cin >> kode; 
                    gudang.binarySearch(kode);
                }
                break;
            case 5:
                pohon.tampilkanSemuaRekomendasi(gudang.head);
                break;
            case 0:
                menu = false;
                break;
        }
    
     }

    return 0;
}
