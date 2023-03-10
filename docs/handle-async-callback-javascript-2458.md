# 处理异步回调 Javascript

> 原文：<https://dev.to/guntur/handle-async-callback-javascript-2458>

Di javascript sudah tak asing lagi dengan istilah `callback` . Callback sendiri adalah function yang disisipkan function sebagai `paramater` atau `argument` . Callback sangat erat kaitannya dengan `asynchronous` .

Sebuah fungsi yang menerima `function` sebagai `parameter` -nya, merupakan ciri dari `asynchoronous` function, sehingga dapat kita berikan `parameter` tersebut sebagai `anonymous` atau `naming` function.

```
function cekTahun(callback) {
    setTimeout(function () {
        callback((new Date()).getFullYear());
    }, 2000);
} 
```

Function `cekTahun` menerima sebuah `parameter` atau `argument` dengan nama `callback` bertipe `function` . Pada saat `callback` di `invoke` , akan mengembalikan nilai dari tahun.

Contoh menggunakan `anonymous` function.

```
cekTahun(function (tahun) {
    console.log('Sekarang adalah tahun:', tahun);
}); 
```

[![handle-async-callback](img/bf3c899053b027474eabe558c130fcd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zHnWySZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/11140873/56917923-ed6ee180-6ae6-11e9-818f-fb9f0601760c.png)

Contoh dengan menggunakan `naming` function.

```
function tampilkanTahun(tahun) {
    console.log('Sekarang adalah tahun:', tahun);
}

cekTahun(tampilkanTahun); 
```

[![handle-async-callback_2](img/82b8cd50747176973c2269c1fe0e0166.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cNwENHkx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/11140873/56917928-efd13b80-6ae6-11e9-88d6-5929b862727a.png)

Terlihat pada gambar, tidak ada perbedaan dari kedua hasil gambar diatas, baik `anonymous` maupun `naming` function. Ini hanyalah cara anda untuk mengorganisir setiap baris kode agar tertata rapi dan mudah dibaca.

### Kekeliruan Umum Mengolah Asynchronous

Kekeliruan umum pada saat menghandle `asynchronous` operation, adalah menghandlenya layaknya `synchronous` . Misalnya...

```
function cekTanggal() {
    return setImmediate(function () {
        return (new Date()).getDate();
    });
}

const tanggal = cekTanggal();
console.log('Sekarang tanggal:', tanggal); 
```

[![handle-async-callback_mistake](img/b9f07b1ca61867e30779266f69385edf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fmlvnXdg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/11140873/56921284-a0433d80-6aef-11e9-9f37-aaf1210cf480.png)

Function `cekTanggal` mengembalikan nilai dari function `setImmediate` , yang tentu saja tidak sesuai ekspetasi anda. Ini adalah kekeliruan yang umum, bahkan saya pribadi juga pernah keliru tentang ini.

## Terkait

*   [Memahami 同步和异步 Javascript](https://dev.to/guntur/memahami-synchronous-dan-asynchronous-javascript-4kjj)
*   [办理异步登安承诺](https://dev.to/guntur/handle-async-promises-javascript-7ck)