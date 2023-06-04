# Token Asli
## Perkenalan
*Token Asli* adalah fitur baru yang memungkinkan transaksi multi-aset di Novo. Pengguna dapat bertransaksi dengan novo, dan token yang ditentukan pengguna (kustom) dalam jumlah tak terbatas secara native.

Dukungan asli menawarkan keuntungan berbeda bagi pengembang: tidak perlu membuat kontrak pintar untuk menangani token khusus, kontrak pintar dapat berfokus pada penerapan fungsi bisnis, yang menghilangkan lapisan kerumitan tambahan dan potensi kesalahan manual karena buku besar menangani semua yang terkait dengan token Kegunaan. Biaya transaksi juga akan lebih rendah saat mencetak dan menggunakan token.

Token asli (dibuat khusus) mewakili beberapa nilai dan bertindak sebagai unit akuntansi, yang dapat digunakan untuk pembayaran, transaksi, dan dapat digunakan dengan kontrak pintar.

Kami memperluas struktur UTXO untuk membuat token setara dengan novo.

Di setiap keluaran pada transaksi (TxOut), ada bagian kontrak opsional yang mencatat informasi terkait token asli. Bidangnya adalah sebagai berikut:

    ContractType       (uint64, Defined with: FT, NFT, FT_MINT, NFT_MINT)
    ContractID         (txid+vout 36 bytes, the outpoint when the contract is published)
    ContractValue      (uint256, the value of FT, the tokenID of NFT, and the total issued amount of MINT.)
    ContractMaxSupply  (uint256, the FT/NFT maximum circulation)
    ContractMetadata   (string n bytes, any data less than 1KB, initialized once when created, and then read-only)

- Data token yang baru ditambahkan bersifat opsional dan masih kompatibel dengan struktur aslinya. Output tidak boleh berisi atau hanya satu jenis token.
- Setelah kontrak dibuat, ContractID bersifat unik secara global dan digunakan untuk membedakan token yang berbeda.
- Saat mentransfer token antar alamat, biaya transaksi harus dibayar dengan novo, dan minimum 0,4368 novo (batas debu) diperlukan dalam output token.


## Menggunakan

### Contoh: Membuat token yang sepadan dengan Anda sendiri

Cara membuat token yang sepadan menggunakan alat perintah node semudah mengirim Novo ke alamat. Membuat token memerlukan novo untuk biaya transaksi.

Pertama-tama, kita perlu memastikan bahwa saldo dompet mencukupi, kita dapat menjalankan perintah berikut untuk membuat total 21000000 FT, dan cukup menandainya sebagai TESTCOIN, alamat pembuatnya adalah `miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6`

```
./novo-cli createtoken FT 21000000 '{"name":"TESTCOIN","symbol":"TC","decimal":8}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0
```

Pengidentifikasi unik token adalah `c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0`. Yaitu, contractID.

Token saat pertama kali dibuat tidak memiliki persediaan. Mari kita membuat beberapa. Mint 1000000 dan 50 token ke alamat.

```
./novo-cli minttoken FT c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0 1000000 msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

./novo-cli minttoken FT c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0 50 miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
```

### Contoh: Mentransfer token ke pengguna lain

Penerima mendapatkan alamat dompet mereka dengan menjalankan `novo-cli getnewaddress` dan memberikannya kepada pengirim. Pengirim kemudian menjalankan:

    $ ./novo-cli transfertoken FT c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0 100 msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

### Contoh: Membuat token yang tidak dapat dipertukarkan

Menggunakan tipe yang berbeda dalam perintah createtoken, kita dapat membuat NFT. Misalnya, buat NFT dengan total sirkulasi 100, dan cukup tandai sebagai TESTCARD, dan alamat pembuatnya adalah `miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6`：

```
./novo-cli createtoken NFT 100 '{"name":"TESTCARD",description:""}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0
```

Pengidentifikasi unik token adalah `d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0`. Yaitu, contractID.

Token yang tidak dapat dipertukarkan saat pertama kali dibuat tidak memiliki persediaan. Mari kita mint satu per satu. Mint 3 token ke alamat, dengan metadata berbeda D1/D2/D3:

```
./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"D1",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"D2",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"D3",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx
```

Nomor yang bertambah otomatis untuk pencetakan NFT sebagai tokenID-nya.

### Contoh: Mentransfer NFT ke pengguna lain

Ketika NFT tertentu perlu ditransfer, itu harus ditentukan melalui contractID dan tokenID.

Penerima mendapatkan alamat dompet mereka dengan menjalankan `novo-cli getnewaddress` dan memberikannya kepada pengirim. Pengirim kemudian menjalankan:

```
./novo-cli transfertoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 1 n1RZhxvsNRVXFgB1GKbE9XtscYUKMMcSGx
```

### Contoh: Lihat semua Token yang Anda miliki

Setelah menerima token, Anda dapat menggunakan perintah `listcontractunspent` untuk mendapatkan UTXO dari semua token.
```
./novo-cli listcontractunspent
[
  {
	"txid": "7e305ff908f3251967b5026ed38311bf513f86873368389f1f083dbfb0d8f601",
	"vout": 0,
	"address": "n1RZhxvsNRVXFgB1GKbE9XtscYUKMMcSGx",
	"account": "",
	"scriptPubKey": "76a914da5d94a04ada05253448c6cee108c67cfdc25d0588ac",
	"amount": 0.4368,
	"contractType": "NFT",
	"contractID": "d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0",
	"contractValue": "0",
	"contractMaxSupply": "100",
	"contractMetadata": '{"name":"D1",description:""}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
  {
	"txid": "df8194645691a3d65613ed3b2cae8c9b8797121d53d384d2e0d65c8ca7c4f908",
	"vout": 1,
	"address": "miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6",
	"account": "",
	"scriptPubKey": "76a91421aab58a9e80ca6a7f28c5ebcbcc94286d410f6988ac",
	"amount": 0.4368,
	"contractType": "NFT_MINT",
	"contractID": "d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0",
	"contractValue": "3",
	"contractMaxSupply": "100",
	"contractMetadata": '{"name":"NFT_CARD",description:""}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
  {
	"txid": "478ef48c0e753c2956c681c92a02ed101059b11044f59dbdf1254b3c84711e53",
	"vout": 1,
	"address": "miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6",
	"account": "",
	"scriptPubKey": "76a91421aab58a9e80ca6a7f28c5ebcbcc94286d410f6988ac",
	"amount": 0.4368,
	"contractType": "FT_MINT",
	"contractID": "c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0",
	"contractValue": "4300",
	"contractMaxSupply": "21000000",
	"contractMetadata": '{"name":"TESTCOIN","symbol":"TC","decimal":8}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
  {
	"txid": "29506301f26f6fe593440d197e427d9212875c32203e3629e1235260f61c417b",
	"vout": 1,
	"address": "msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx",
	"account": "",
	"scriptPubKey": "76a91485487c71ae9a6ab0652df0a95dfd681d6285236388ac",
	"amount": 0.4368,
	"contractType": "FT",
	"contractID": "c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0",
	"contractValue": "100",
	"contractMaxSupply": "21000000",
	"contractMetadata": '{"name":"TESTCOIN","symbol":"TC","decimal":8}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
...
```

*Selain itu, kreasi khusus dari transaksi mentah juga dapat digunakan untuk mencapai tujuan ini. Saat ini, perlu untuk memahami struktur keluaran dari token asli.*

* createrawtransaction
* signrawtransaction
* sendrawtransaction
* decoderawtransaction


## Kontrak
### FT_MINT
Saat Anda menggunakan perintah `createtoken FT`, kontrak pencetakan tipe FT_MINT akan dibuat. Kontrak FT_MINT hanya dapat digunakan untuk membuat FT, bukan FT itu sendiri. Aturan kontrak adalah sebagai berikut:
- `ContractType`: Is `FT_MINT`, immutable.
- `ContractID`: Unique, immutable.
- `ContractValue`: The amount of tokens minted, which increases with the issuance of tokens.
- `ContractMaxSupply`: The maximum circulation, immutable
- `ContractMetadata`: The metadata, immutable. Please refer to the [Standard](#the-fungible-token-standard)

### FT
Keluaran bertipe FT berarti ada FT yang terpasang pada keluaran. Saat Anda menggunakan perintah `minttoken FT`, UTXO dari FT_MINT akan digunakan, dan UTXO baru dari FT_MINT dan UTXO dari FT yang baru dicetak akan dibuat. Aturan kontrak FT adalah sebagai berikut:
- `ContractType`: Is `FT`, immutable.
- `ContractID`: Unique, immutable. Inherited from FT_MINT, consistent with it.
- `ContractValue`: The amount of this token, changes when a transfer occurs.
- `ContractMaxSupply`: The maximum circulation, immutable. Inherited from FT_MINT, consistent with it.
- `ContractMetadata`: The metadata, immutable. Inherited from FT_MINT, consistent with it.

### NFT_MINT
Saat Anda menggunakan perintah `createToken NFT`, kontrak pencetakan tipe NFT_MINT akan dibuat. Kontrak NFT_MINT hanya dapat digunakan untuk membuat NFT, ini bukan NFT itu sendiri. Aturan kontrak adalah sebagai berikut:
- `ContractType`: Is `NFT_MINT`, immutable.
- `ContractID`: Unique, immutable.
- `ContractValue`: The number of minted NFTs, which increases with the issuance of NFTs.
- `ContractMaxSupply`: The maximum circulation, immutable
- `ContractMetadata`: The metadata of the series of NFTs, this is optional. Please refer to the [Standard](#the-non-fungible-collection-standard)

### NFT
Keluaran bertipe NFT berarti ada NFT yang terpasang pada keluaran. Saat Anda menggunakan perintah `minttoken`, UTXO dari NFT_MINT akan digunakan, dan UTXO baru dari NFT_MINT dan UTXO dari NFT yang baru dicetak akan dihasilkan. Aturan kontrak NFT adalah sebagai berikut:
- `ContractType`: Is `NFT`, immutable.
- `ContractID`: Unique, immutable. Inherited from NFT_MINT, consistent with it.
- `ContractValue`: The tokenID of this NFT, immutable. Start from 1.
- `ContractMaxSupply`: The maximum circulation, immutable. Inherited from NFT_MINT, consistent with it.
- `ContractMetadata`: The metadata of this NFT. Please refer to the [Standard](#the-non-fungible-standard)

## Standar Token
### Standar Token Sepadan
Kami merekomendasikan Serial JSON dengan struktur berikut.

|Field|Type|Description|
|--|--|--|
|name|string|Name of the token.|
|symbol|string|Symbol of the token.|
|decimal|number|Decimal of the token.|

contoh:
```
./novo-cli createtoken FT 21000000 '{"name":"TESTCOIN","symbol":"TC","decimal":8}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0
```

### Standar yang Tidak Dapat Disepadankan
Kami merekomendasikan bahwa metadata untuk NFT dapat berupa A uri:
`https://ipfs.io/ipfs/QmeSjSinHpPnmXmspMjwiXyN6zS4E9zccariGR3jxcaWtq/1`
atau serial JSON dengan struktur berikut:

|Field|Type|Description|
|--|--|--|
|name|string|Name of the NFT.|
|description|string|Description of the NFT.|
|image|string|URI pointing to the NFT.|

contoh:
```
./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"TESTCARD-01",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx
```

### Standar Pengumpulan yang Tidak Dapat Disepadankan
Saat membuat kumpulan NFT, pesan umum dapat diatur untuk menampilkan info dasar.
Kami merekomendasikan serial JSON dengan struktur berikut.

|Field|Type|Description|
|--|--|--|
|name|string|Name of the collection.|
|description|string|Description of the collection.|
|image|string|URI pointing to the collection.|

Example:
```
./novo-cli createtoken NFT 100 '{"name":"TESTCARD",description:""}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0
```


## Panduan Pengembang
Untuk mendukung token asli, hal-hal berikut perlu diperhatikan.

### TxOutput
Struktur keluaran transaksi telah berubah. Output tertentu menyertakan token asli, yang berarti bahwa selain nilai dan skrip, ada juga segmen data token di output.

### SignatureHash
Selama serialisasi transaksi, keluaran transaksi yang berisi token asli akan membuat serialisasi data token sebelum membuat serialisasi nilainya. Saat menghitung SignatureHash yang ditandatangani, jika UTXO yang dihabiskan berisi token asli, data token juga akan ditambahkan sebelum nilai serial. Silakan merujuk ke bip143, di mana `6. nilai` dan`8. hashOutputs` dapat dipengaruhi oleh data token.
