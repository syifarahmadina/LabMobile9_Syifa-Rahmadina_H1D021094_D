**Nama  : Syifa Rahmadina**

**NIM   : H1D021094**

**Tugas Praktikum Mobile Pertemuan 9**



**1. Tambah Mahasiswa (Create)**

Proses ini pengguna mengisi form tambah mahasiswa dan menekan tombol tambah mahasiswa dan data tersebut akan dikirimkan ke server untuk di simpan di database.


**TAMBAH MAHASISWA**

Form Tambah Mahasiswa

![Screenshot (4226)](https://github.com/user-attachments/assets/4932fc77-7df4-4c42-8006-58735aa8e599)

Form Tambah Mahasiswa yang Telah Diisi:

![Screenshot (4227)](https://github.com/user-attachments/assets/e5cd820f-c1b2-4108-a424-814497b51374)

Hasil Tambah Data Mahasiswa:

![Screenshot (4228)](https://github.com/user-attachments/assets/ff73158a-6313-4d44-b54e-af4cbbcc50ae)


**1.1 Client Side (Ionic)**

> Form Input di mahasiswa.page.html

Disini kita memasukan data nama mahasiswa dan jurusan pada form ini dan mengirimkan hasil formnya dengan menekan toombol.

Code yang digunakan:

<ion-item>
  
  <ion-label position="floating">Nama Mahasiswa</ion-label>
  
  <ion-input [(ngModel)]="nama" required></ion-input>

</ion-item>

<ion-item>

  <ion-label position="floating">Jurusan</ion-label>
  
  <ion-input [(ngModel)]="jurusan" required></ion-input>

</ion-item>

<ion-button (click)="tambahMahasiswa()" color="primary" expand="block">Tambah Mahasiswa</ion-button>


> Fungsi tambahMahasiswa() di mahasiswa.page.ts disini fungsi akan mengambil data dari inputan yang kita masukan lalu menggunakan ApiService untuk mengirim data ke endpoint tambah.php di server. Berikut sedikit codenya:

tambahMahasiswa() {

  this.apiService.tambah({ nama: this.nama, jurusan: this.jurusan }, 'tambah.php').subscribe(response => {
  
    if (response) {
    
      this.getDataMahasiswa(); // Refresh data mahasiswa setelah penambahan
    
    }
  
  });

}


> Metode tambah() di api.service.ts disini menggunakan metode HttpClient untuk mengirim permintaan POST ke server. Dengan code berikut:

tambah(data: any, endpoint: string) {

  return this.http.post(this.apiURL() + '/' + endpoint, data);

}


**1.2 Server Side**

> tambah.php digunakan untuk menerima data JSON, lalu menambahkan data ke dalam database db_mhs dalam tabel mahasiswa. Berikut sedikit codenya:

require 'koneksi.php';

$input = file_get_contents('php://input');

$data = json_decode($input, true);

$nama = trim($data['nama']);

$jurusan = trim($data['jurusan']);

http_response_code(201);

if ($nama != '' and $jurusan != '') {

    $query = mysqli_query($koneksi, "insert into mahasiswa(nama,jurusan) values('$nama','$jurusan')");
    
    $pesan = true;

} else {

    $pesan = false;

}

echo json_encode($pesan);



**2. Tampil Data Mahasiswa (Read)**

Disini terjadi saat halaman aplikasi terbuka, aplikasi mengambil data dari server untuk ditampilkan.

**SS Tampil Data Mahasiswa**

![Screenshot (4230)](https://github.com/user-attachments/assets/e8b0bab6-8076-4d71-a811-7a74b2f53053)


**2.1 Client Side (Ionic)**

> Tampilan Daftar Mahasiswa di mahasiswa.page.html yang ditampilkan dalam elemen ion-card berikut codenya:

<ion-card *ngFor="let item of dataMahasiswa">
 
  <ion-item>
  
    <ion-label>
    
      {{item.nama}}
      
      <p>{{item.jurusan}}</p>
    
    </ion-label>
  
  </ion-item>

</ion-card>


> Fungsi getDataMahasiswa() di mahasiswa.page.ts yang digunakan untuk memanggil ApiService untuk mengambil data mahasiswa dari server. Berikut codenya:

getDataMahasiswa() {

  this.apiService.tampil('tampil.php').subscribe(data => {
  
    this.dataMahasiswa = data;
  
  });

}


> Metode tampil() di api.service.ts digunakan untuk permintaan get dikirim ke endpoint tampil.php untuk mengambil semua data mahasiswa. Berikut codenya:

tampil(endpoint: string): Observable<any> {

  return this.http.get(this.apiURL() + '/' + endpoint);

}


**2.2 Server Side (PHP)**

> tampil.php ini digunakan untuk mengambil semua data mahasiswa dari tabel mahasiswa dan mengembalikannya dalam format JSON. Berikut codenya:

require 'koneksi.php';

$data = [];

$query = mysqli_query($koneksi, "select * from mahasiswa");

while ($row = mysqli_fetch_object($query)) {

    $data[] = $row;

}

echo json_encode($data);



**3. Edit Mahasiswa (Update)**

Disini digunakan untuk memilih mahasiswa yang akan diedit lalu mengubah data dan mengirimkannya ke server untuk diperbarui.

**SS EDIT MAHASISWA**

SS Form Edit Sebelum Di Edit:

![Screenshot (4231)](https://github.com/user-attachments/assets/4a75a5ad-1221-46a8-a223-f6927ee205d5)

SS Form Edit Sesudah Di Edit:

![Screenshot (4232)](https://github.com/user-attachments/assets/a9fb638c-4062-482b-9116-7499bb4da951)

SS Tampil Setelah Berhasil Di Edit:

![Screenshot (4233)](https://github.com/user-attachments/assets/feaee699-e21d-4753-9b49-9445a3e4ec7f)


**3.1 Client Side (Ionic)**

> Fungsi editMahasiswa() di mahasiswa.page.ts yang digunakan untuk mengirimkan data mahasiswa yang telah diperbaharui ke server. Berikut codenya:

editMahasiswa() {

  this.apiService.edit({ id: this.id, nama: this.nama, jurusan: this.jurusan }, 'edit.php').subscribe(response => {
  
    if (response) {
    
      this.getDataMahasiswa(); // Refresh data
    
    }
  
  });

}


> Metode edit() di api.service.ts ini digunakan untuk mengirimkan permintaan PUT dengan data JSON yang telah diperbarui. Berikut codenya:

edit(data: any, endpoint: string) {
 
  return this.http.put(this.apiURL() + '/' + endpoint, data);

}


**3.2 Server Side (PHP)**

> edit.php disini script menerima data yang telah diperbarui dan memperbarui entri database berdasarkan ID. Berikut codenya:

require 'koneksi.php';

$input = file_get_contents('php://input');

$data = json_decode($input, true);

$id = trim($data['id']);

$nama = trim($data['nama']);

$jurusan = trim($data['jurusan']);


http_response_code(201);

if ($nama != '' and $jurusan != '') {

    $query = mysqli_query($koneksi, "update mahasiswa set nama='$nama', jurusan='$jurusan' where id='$id'");
    
    $pesan = true;

} else {

    $pesan = false;

}

echo json_encode($pesan);



**4. Hapus Mahasiswa (Delete)**

Disini kita memilih data mahasiswa untuk dihapus lalu aplikasi mengirimkan permintaan ke server untuk menghapus data tersebut.

**SS Hapus Mahasiswa**

SS Tampil Data Sebelum di Hapus

![Screenshot (4233)](https://github.com/user-attachments/assets/b87c7016-83e0-45ff-a2f7-5bed8e624d2e)

SS Tampil Data Setelah Data Xu Minghao di Hapus

![Screenshot (4234)](https://github.com/user-attachments/assets/19e813b1-0d66-4323-9704-04e83bfec208)


**4.1 Client Side (Ionic)**

> Fungsi hapusMahasiswa() di mahasiswa.page.ts digunakan untuk mengirimkan permintaan ke server untuk menghapus data berdasarkan ID mahasiswa. Berikut codenya:

hapusMahasiswa(id: any) {

  this.apiService.hapus(id, 'hapus.php').subscribe(response => {
  
    if (response.status === 'sukses') {
    
      this.getDataMahasiswa(); // Refresh data
    
    }
  
  });

}


> Metode hapus() di api.service.ts yang digunakan untuk mengirimkan permintaan delete ke endpoint hapus.php. Berikut codenya:

hapus(id: any, endpoint: string) {
 
  return this.http.delete(this.apiURL() + '/' + endpoint + '' + id);

}


**4.2 Server Side (PHP)**

> hapus.php; script ini digunakan untuk menghapus entri data dari tabel mahasiswa berdasarkan ID yang diterima. Berikut codenya:

require 'koneksi.php';

$id = $_GET['id'];

$query = mysqli_query($koneksi, "delete from mahasiswa where id='$id'");

$pesan['status'] = $query ? 'sukses' : 'gagal';

echo json_encode($pesan);


 
