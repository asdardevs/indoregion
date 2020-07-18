# azishapidin/indoregion
[![Build Status](https://travis-ci.org/azishapidin/indoregion.svg?branch=master)](https://travis-ci.org/azishapidin/indoregion) [![StyleCI](https://styleci.io/repos/90970565/shield?branch=master)](https://styleci.io/repos/90970565) [![License](https://poser.pugx.org/azishapidin/indoregion/license)](https://packagist.org/packages/azishapidin/indoregion) [![Latest Stable Version](https://poser.pugx.org/azishapidin/indoregion/v/stable)](https://packagist.org/packages/azishapidin/indoregion) [![Total Downloads](https://poser.pugx.org/azishapidin/indoregion/downloads)](https://packagist.org/packages/azishapidin/indoregion)

```azishapidin/indoregion``` adalah sebuah package Laravel untuk menyimpan data wilayah Indonesia mulai dari Provinsi, Kabupaten/Kota, Kecamatan/Distrik, sampai Desa/Kelurahan. Package akan menambahkan migrations, seeder (untuk import data ke database) dan Model pada project Anda.

Semua data akan disimpan di database, untuk mengambil data tersebut sama dengan mengambil data lewat Model pada umum-nya (Lihat bagian Usage).

Data ini diambil dari situs Pemutakhiran MFD dan MBS Badan Pusat Statistik (http://mfdonline.bps.go.id/) pada 11 Januari 2018. Sumber: [https://github.com/edwardsamuel/Wilayah-Administratif-Indonesia](https://github.com/edwardsamuel/Wilayah-Administratif-Indonesia)

## Quick Instalation

Buka Command Line kemudian jalankan perintah dibawah untuk melakukan instalasi package:
```
composer require azishapidin/indoregion
```

### Register Service Provider

#### Laravel

Jika Anda menggunakan Laravel versi 5.5 keatas Anda bisa skip bagian ini karena package indoregion sudah menggunakan Package Auto Discovery.  
  
Tapi jika kebetulan Project yang Anda kerjakan masih menggunakan versi dibawah 5.5 maka silahkan untuk membuka file **config/app.php** lalu tambahkan Class ```IndoRegionServiceProvider``` kedalam array Service Providers:
```php
// Provider Lain
AzisHapidin\IndoRegion\IndoRegionServiceProvider::class,
```

```php
// Alias
'IndoRegion' => AzisHapidin\IndoRegion\Facades\IndoRegionFacade::class,
```

#### Lumen

Jika Anda ingin menggunakan Package ini pada project Lumen, maka Anda harus melakukan register Service Provider pada file ```bootstrap/app.php``` dengan menambahkan ini:

```php
$app->register(AzisHapidin\IndoRegion\IndoRegionServiceProvider::class);
```

### Publish File
Jalankan perintah dibawah di Command Line:

```
php artisan indoregion:publish
``` 
  
Saat perintah diatas dijalankan, indoregion akan menyalin:

* Files migration dari ```/packages/azishapidin/indoregion/src/database/migrations``` ke ```/database/migrations```
* Files seeder dari ```/packages/azishapidin/indoregion/src/database/seeds``` ke ```/database/seeds```

Setelah itu jalankan perintah dibawah:
```
composer dump-autoload
```

### Populate
Jalankan perintah dibaawah ini untuk melakukan migrate & seed :
```
php artisan indoregion:populate
```

## Basic Usage
Anda bisa gunakan class dibawah seperti model pada umum-nya.
  
```php
<?php

use AzisHapidin\IndoRegion\Models\Province;
use AzisHapidin\IndoRegion\Models\Regency;
use AzisHapidin\IndoRegion\Models\District;
use AzisHapidin\IndoRegion\Models\Village;

// Get semua data
$provinces = Province::all();
$regencies = Regency::all();
$districts = District::all();
$villages = Village::all();

// Cari berdasarkan nama
$provinces = Province::where('name', 'JAWA BARAT')->first();
$regencies = Regency::where('name', 'LIKE', '%CIANJUR%')->first();
$districts = District::where('name', 'LIKE', 'BANDUNG%')->get();
$villages = Village::where('name', 'BOJONGHERANG')->first();

```
### IndoRegion Facade

```php
<?php

use IndoRegion;

$provinces = IndoRegion::getAllProvince();

$findProvince = IndoRegion::findProvinceById(61); // return KALIMANTAN BARAT provinces instanse 

```

## Advance Usage

```php
<?php

// Get Kecamatan dari sebuah Provinsi.
$districts = $province->districts;

// Cek jika provinsi memiliki kabupaten terkait menggunakan logika OR bedasarkan nama kabupaten.
$province->hasDistrictName(["SELAKAU TIMUR", "PEMANGKAT", "SEMPARUK", "JAWAK"]);

// Cek jika provinsi memiliki kabupaten terkait menggunakan logika AND bedasarkan nama kabupaten.
$province->hasDistrictName(["SELAKAU TIMUR", "PEMANGKAT", "SEMPARUK", "JAWAI"], true);

// Cek jika provinsi memiliki kabupaten terkait menggunakan logika OR bedasarkan id kabupaten.
$province->hasDistrictId([6101, 6102, 6103, 6104]);

// Cek jika provinsi memiliki kabupaten terkait menggunakan logika AND bedasarkan id kabupaten.
$province->hasDistrictId([6101, 6102, 6103, 6104], true);

// Get Kabupaten/Kota dari sebuah Provinsi
$regencies = $province->regencies;

// Get Kecamatan dari sebuah Kabupaten/Kota
$districts = $regency->districts;

// Get Desa/Kelurahan dari sebuah Kabupaten/Kota
$villages = $regency->villages;

// Cek jika kabupaten memiliki desa/kelurahan terkait menggunakan logika AND bedasarkan nama desa/kelurahan.
$regency->hasVillageName(["PARIT SETIA", "PELIMPAAN", "SEMPARUK"], true);

// Cek jika kabupaten memiliki desa/kelurahan terkait menggunakan logika AND bedasarkan id desa/kelurahan.
$regency->hasVillageId([6101050014, 6101040025, 6101060023, 6101020014]);

// Get Desa/Kelurahan dari sebuah Kecamatan
$villages = $district->villages;

// Cek Desa ada di sebuah Provinsi
$village->isProvince(61);

// Cek Desa ada di sebuah Kabupaten
$village->isRegency(6102);

// Cek Desa ada di sebuah Kecamatan
$village->isDistrict(6101050);

// Cek Kecamatan ada di sebuah Provinsi
$district->isProvince(61);

// Cek Kecamatan ada di sebuah Kabupaten
$village->isRegency(6102);

// Get Kabupaten dari sebuah Desa
$village->regency;

// Get Provinsi dari sebuah Desa
$village->province;

// Get Provinsi dari sebuah Kecamatan
$district->province;
```
