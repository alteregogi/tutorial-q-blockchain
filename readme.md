# Tutorial Jadi Validator di Q Blockchain



[Dokumentasi](https://docs.qtestnet.org/how-to-setup-validator/)

[Artikel Terkait](https://medium.com/q-blockchain/q-blockchain-validator-onboarding-program-part-1-validator-incentivized-testnet-567ef6e4002e)



## 1. Pre install

Buka port ```30313```

```shell
sudo ufw allow 30313
```



Buat directory baru namanya QBlockchain

```shell
mkdir QBlockchain
```



masuk ke directory yang udah dibikin

```shell
cd QBlockchain
```



Clone Repository

```shell
git clone https://gitlab.com/q-dev/testnet-public-tools
```



masuk ke directory git yang barusan di clone

```shell
cd testnet-public-tools/testnet-validator
```



bikin password baru dan save ke file pwd.txt di folder keystore, password ini gunanya buat kombinasi bikin address QBlockchain kalian di testnet.

```shell
mkdir keystore; nano keystore/pwd.txt;
```

tulis password yang kalian mau kemudian save, ```ctrl + x ``` ``` y``` ,```enter```

make sure password udah ketulis di file pwd.txt

```shell
cat keystore/pwd.txt
```



#### Generate wallet address dari password

```shell
docker run --entrypoint="" --rm -v $PWD:/data -it qblockchain/q-client:testnet geth account new --datadir=/data --password=/data/keystore/pwd.txt
```



kalau berhasil bakal muncul logs seperti dibawah

```shell
Your new key was generated

Public address of the key:   0x______________________________C0b
Path of the secret key file: /data/keystore/UTC--2022-12-12T15-26-40.627546777Z--7_______________________________c0b

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```



## 2. Registrasi validator dan claim faucet

Import wallet yang sudah di generate tadi di metamask, download file yang start awalnya ```UTC--2022-*``` di dalam folder ```keystore```



1. ##### Buka metamask kalian, dan pilih Import Account

![](https://snipboard.io/1SBeqx.jpg)

2. ##### Pilih JSON File, upload file **UTC-** yang sudah di download, dan masukkan password yang sudah kalian bikin di file **pwd.txt**

   tunggu sesaat, wallet bakal ke import. kalau udah selesai, cek addressnya pastikan sudah sesuai sama logs di step create wallet address tadi.

![](https://snipboard.io/yUJKbg.jpg)



3. ##### Connect wallet kalian [disini](https://itn.qdev.li/) isi data nya, dan setelah itu sign message.

4. ##### simpan baik-baik message yang muncul setelah berhasil sign formnya.

   ![](https://snipboard.io/a1Yuf4.jpg)

5. ##### Claim faucet [disini](https://faucet.qtestnet.org/) , masukkan address testnet kalian yang udah di generate tadi.

   Cek di metamask, pastikan 5 Q sudah masuk di address kalian.

   ![](https://snipboard.io/H524bq.jpg)

## 3. Install Validator Node

#### 1. Konfigurasi file .env, config.json dan docker-compose.yml

Pastikan posisi terminal kalian di directory ```/testnet-validator```

1. ##### Edit file .env

   ```
   nano .env
   ```

   Edit file .env sesuai keterangan dibawah

   | Variable | Data yang harus di masukkan                             |
   | -------- | ------------------------------------------------------- |
   | ADDRESS  | Masukkan address testnet kalian tanpa ``0x``di depannya |
   | IP       | Masukkan IP VPS kalian                                  |

   kemudian save, ```ctrl + x ```, ```y``` , ```enter```

2. ##### Edit file config.json

   ```
   nano config.json
   ```

   Ganti address dan password kalian, password samakan seperti di file **pwd.txt** yang sudah di bikin

   kemudian save, ```ctrl + x ```, ```y``` , ```enter```

3. ##### Stake Q Token

   Jalankan command dibawah

   ```
   docker run --rm -v $PWD:/data -v $PWD/config.json:/build/config.json qblockchain/js-interface:testnet validators.js
   ```

   

4. ##### Edit file docker-compose.yaml

   buka file ```docker-compose.yaml```

   ```
   nano docker-compose.yaml
   ```

   

   dibagian ```entrypoint:``` setelalah ```["geth",``` tambahkan konfigurasi hasil kalian daftar form validator tadi,
   tinggal copy terus paste setelah ```geth```

   ![](https://snipboard.io/05AJuF.jpg)

   

   Pastikan formatnya seperti gambar dibawah, jangan lupa di tambah ```"``` di antara text yang kalian copy tadi dan ```,```  (koma) di akhir , lebih jelasnya cek gambar dibawah
   

![](https://snipboard.io/TcAUux.jpg)

​		kemudian save, ```ctrl + x ```, ```y``` , ```enter```



#### 2. Stake Q Token kalian

Jalankan command dibawah
```shell
docker run --rm -v $PWD:/data -v $PWD/config.json:/build/config.json qblockchain/js-interface:testnet validators.js
```



#### 3. Start validator node

Jalankan command dibawah

```
docker-compose up -d
```

Lalu cek log, pastikan log nya jalan, ```ctrl+c``` buat close logsnya

```
docker-compose logs -f
```



Cek nama kalian [disini](https://stats.qtestnet.org/)
Setelah selesai harusnya nama kalian sudah muncul di list, pastikan tanda **"centang"**nya ijo, itu artinya kalian berhasil stake token Q di node kalian
tunggu sampai node kalian sync, harusnya setelah itu nama kalian bakal warna ijo semua.
![](https://snipboard.io/QEB8K7.jpg)

## 4. Upgrade Validator Node

1. masuk ke directory`QBlockchain/testnet-public-tools/testnet-validator`
   buka `.env` file dan ganti versi client kalian ke `1.2.2` atau versi yang nanti bakal update

   ```
   nano .env
   ```

   cek bagian ``QCLIENT_IMAGE=qblockchain/q-client`
   ganti versinya sesuai versi terakhir ( sekarang latest versinya `1.2.2`)

   ```
   QCLIENT_IMAGE=qblockchain/q-client:1.2.2
   ```

   `ctrl + x` , `y` , `enter` untuk save file .env

2. Pull docker image
   ```shell
   docker-compose pull
   ```

3. Restart node dengan updated client yang terakhir
   ```
   docker-compose up -d
   ```

   

