# tendorethane-code.github.io

Ini "rumah" domain `https://tendorethane-code.github.io/`. Semua proyek GitHub Pages-ku tinggal
di bawahnya (`/jalur-maps/`, nanti proyek lain), tapi isi repo ini sendiri cuma tiga:

| File | Gunanya |
|---|---|
| `index.html` | Halaman depan: daftar aplikasi + link. |
| `.well-known/assetlinks.json` | Bukti buat Android bahwa APK-ku memang punya domain ini. Tanpa ini, APK (TWA) tetap jalan tapi ada bilah alamat browser di atas. |
| `.nojekyll` | **Wajib, jangan dihapus.** Tanpa file ini GitHub Pages diam-diam membuang folder yang namanya diawali titik, termasuk `.well-known`. |

Pages: **Settings → Pages → Deploy from a branch → `main` / `(root)`**.

## Daftar aplikasi

| Aplikasi | Web | Repo (rilis di Releases-nya) | Paket Android | Sidik jari kunci (SHA-256) |
|---|---|---|---|---|
| Jalur | [/jalur-maps/](https://tendorethane-code.github.io/jalur-maps/) | [jalur-maps](https://github.com/tendorethane-code/jalur-maps) | `io.github.tendorethane_code.jalur` | `C9:F3:5D:EF:ED:F0:E0:7D:8A:48:75:D5:04:A0:9B:06:09:04:3C:62:BD:70:55:4A:CB:6A:CD:11:BB:F3:25:D3` |

Unduhan APK Jalur terbaru selalu di
`https://github.com/tendorethane-code/jalur-maps/releases/latest/download/jalur.apk`. Halaman depan
nge-link ke situ. Pre-release (`-uji…`) gak pernah dihitung "latest".

Sidik jari itu publik, aman ditaruh di sini. Yang rahasia adalah file kuncinya dan password-nya.

## Layanan di luar GitHub

| Layanan | Di mana | Dipakai | Kode |
|---|---|---|---|
| `jalur-resolver` (buka link pendek Google Maps) | Cloudflare Workers, akun pribadi: `https://jalur-resolver.tendo-rethane.workers.dev/` | Jalur (`RESOLVER` di `public/config.js`) | `resolver/worker.js` di repo jalur-maps. Di-deploy manual: dashboard → worker → Edit code → tempel → Deploy |

Kalau `resolver/worker.js` diubah, isinya harus ditempel ulang di dashboard Cloudflare. Gak ada
deploy otomatis.

## Aturan

1. **File rilis (APK, EXE, ZIP) ditaruh di GitHub Releases repo proyeknya, bukan di sini.**
   Releases kuat sampai 2 GB per file dan gak bikin riwayat git bengkak. Repo ini cuma nge-link.
2. **Satu aplikasi, satu kunci tanda tangan.** Kunci dan password-nya disimpan di dua tempat:
   - GitHub Secrets di repo proyek: `ANDROID_KEYSTORE_BASE64` dan `ANDROID_KEYSTORE_PASSWORD`
     (Settings → Secrets and variables → Actions).
   - Cadangan offline (Google Drive pribadi, flashdisk, password manager).

   **Kalau kuncinya hilang, APK gak bisa di-update lagi.** Harus uninstall, lalu install APK
   baru dengan kunci baru, dan sidik jarinya di `assetlinks.json` harus diganti.
3. **Nambah APK baru:** tambah satu entri lagi di `assetlinks.json` (jangan timpa yang lama),
   terus tambah barisnya di tabel di atas.
4. **Jangan bikin folder di sini yang namanya sama dengan repo proyek** (misalnya `jalur-maps/`).
   Nanti bentrok sama situs proyek itu.
5. **Semua proyek di domain ini dianggap satu situs oleh browser.** Data browser
   (localStorage, cookies) dan izin (lokasi, clipboard) kebagi antar-proyek. Jadi:
   - Kasih awalan nama di localStorage (Jalur pakai `jalur.`).
   - Jangan simpan rahasia di browser.
6. **Key Google Maps Jalur dibatasi ke `https://tendorethane-code.github.io/*`.** Artinya proyek
   lain di domain ini juga bisa makai kuotanya. Kalau ada proyek lain yang butuh Google Maps:
   bikin key terpisah, lalu persempit key Jalur ke `https://tendorethane-code.github.io/jalur-maps/*`
   di Google Cloud Console.
7. **Jangan pasang custom domain (misalnya `namaku.com`) di repo ini tanpa rencana.** Kalau
   dipasang:
   - Semua proyek ikut pindah ke domain baru.
   - `assetlinks.json` harus ikut pindah.
   - APK yang udah terpasang gagal verifikasi (bilah alamat muncul lagi) sampai APK-nya dibikin
     ulang buat domain baru.
   - Pembatasan key Google Maps juga harus diganti.
8. **Kalau nanti masuk Play Store dengan Play App Signing**, Google menandatangani ulang APK
   pakai kuncinya sendiri. Sidik jari dari Play Console harus ditambahkan di `assetlinks.json`,
   di samping yang lama.

## Cek rutin

Jalanin tiap kali repo ini disambung ke sesi Claude, atau abis ngubah sesuatu di sini:

- [ ] `https://tendorethane-code.github.io/.well-known/assetlinks.json` kebuka (200) dan isinya JSON yang valid.
- [ ] Google bisa baca file itu:
      `https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://tendorethane-code.github.io&relation=delegate_permission/common.handle_all_urls`
      Hasilnya harus menyebut tiap paket di tabel, dengan sidik jari yang sama.
- [ ] Tiap aplikasi di tabel: halaman webnya kebuka, dan Releases-nya punya APK terbaru (kalau ada APK).
- [ ] `.nojekyll` masih ada.
- [ ] Tiap layanan di luar GitHub masih hidup. Resolver: link pendek Google Maps yang asli
      harus dijawab 200 dengan URL yang ada `!3d…!4d…`, dan link non-Google harus ditolak 400.
- [ ] Gak ada file kunci (`*.p12`, `*.jks`, `*.keystore`) atau password yang ke-commit di repo mana pun.
- [ ] Gak ada folder di sini yang namanya sama dengan repo proyek.
- [ ] Belum ada custom domain di Settings → Pages (atau kalau ada, poin 7 udah dikerjain).

Yang gak bisa dicek dari repo, jadi tanya pemiliknya:

- [ ] Cadangan kunci tiap APK masih ada di tempat aman?
- [ ] Batas kuota harian Google Maps di Google Cloud udah dipasang?
- [ ] Key Google Maps masih dibatasi sesuai poin 6?
