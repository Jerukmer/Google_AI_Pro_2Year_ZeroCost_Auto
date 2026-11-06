# ⚠️ CATATAN BAHASA INDONESIA — BACA DULU SEBELUM APA PUN

> **Repo ini adalah fork untuk keperluan dokumentasi & analisis — BUKAN ajakan untuk menjalankan alatnya.**
> Dokumen asli (Bahasa Mandarin) dipertahankan utuh di bawah tanpa diterjemahkan. Analisis lengkap tersedia di **[ANALISIS.md](./ANALISIS.md)**.

## Apa repo/alat ini?

`Google_AI_Pro_2Year_ZeroCost_Auto` adalah konsol otomasi berbasis browser (Python + FastAPI + Playwright) yang dirancang untuk mengklaim langganan **Google AI Pro (5 TB)** gratis hingga **24 bulan per akun**, dan memproses **banyak akun Google sekaligus secara massal**. Berdasarkan pembacaan kodenya, alat ini bekerja dengan memakai *offer token* Google One di luar penerima yang berhak, menyuntik JavaScript untuk memanipulasi halaman checkout `one.google.com`, menumpuk tiga lapis paket gratis (12+6+6 bulan), serta menyamarkan lokasi lewat proxy residensial Amerika Serikat.

## Status hukum & ketentuan layanan

Alat ini **melanggar Ketentuan Layanan Google** (penyalahgunaan promo, multi-akun massal, otomasi tanpa izin, penyelubungan region). Memperoleh layanan berbayar melalui manipulasi seperti ini dapat dikategorikan ketidakwajaran/penipuan di banyak yurisdiksi. "Harganya US$0.00" tidak mengubah sifatnya.

## Risiko nyata bagi siapa pun yang menjalankannya

| Risiko | Dampak |
|---|---|
| Banned & clawback | Langganan dicabat, akun bisa dinonaktifkan, **Google dapat menagih penuh ke kartu** |
| Paparan kredensial | Password, email pemulihan, **seed 2FA**, dan **nomor kartu + CVC** disimpan **plaintext** di `config.json` |
| Kode tak dapat diaudit | ±96 KB script dari sumber anonim yang terhubung toko kartu grey-market; versi baru bisa berubah kapan saja |
| Eksposur hukum | Penggunaan maupun distribusi alat fraud berpotensi bermasalah secara hukum |

## Rekomendasi

1. **Jangan jalankan** alat ini dengan data kartu atau akun asli.
2. Jika pernah menjalankan: ganti password akun terkait, **regenerasi seed 2FA**, dan **blokir/ganti kartu** yang pernah dimasukkan.
3. Baca analisis teknis lengkapnya di **[ANALISIS.md](./ANALISIS.md)**.
4. Butuh akses AI secara resmi? Gunakan jalur legal: tier gratis Google AI Studio / Gemini API, trial resmi, atau paket edukasi.

---

# Konten Asli (Bahasa Mandarin, tidak diubah)

========================================================================
  Google AI Pro (5 TB) 零成本两年 (US$0.00 -> 24个月) 全自动控制台
========================================================================

【一、运行环境要求】
1. Windows 10 / 11 系统
2. 已安装 Google Chrome 浏览器（默认路径即可）
3. 已安装 Python 3.10 或更高版本（安装时勾选 Add Python to PATH）
4. 网络环境：本机开启美区（US）代理，或在控制台左侧填写代理模板

【二、快速启动步骤】
1. 首次使用请双击运行：【1_首次运行_安装依赖并启动.bat】
2. 之后日常使用直接双击：【2_启动控制台.bat】
3. 浏览器会自动打开控制台页面：http://127.0.0.1:8765

【三、自定义填写支付卡 & 跑号说明】
1. 填写支付卡（必填）：
   - 在网页左侧【全局配置与内联免税绑卡】区域，填入你自己的 Visa 或 Mastercard 外币卡：
     * 卡号（16位数字）
     * 有效期（月 / 年，如 08 / 29）
     * 安全码（3位 CVC）
     * 持卡人英文姓名
     * 免税邮编默认已填好 97201（美国俄勒冈免税州），无需修改
   - 填写后点击【💾 保存配置】。
   - 注：脚本内置严格 0 元消费保护，仅在结账弹窗确认为 US$0.00 时才会点击订阅，全程 0 扣费。

2. 批量导入 Google 账号：
   - 在网页右侧【批量放号导入区】粘贴账号（每行一个），支持以下任意格式：
     * 邮箱----密码----辅助邮箱
     * 邮箱----密码----辅助邮箱----2FA密钥
     * 邮箱----密码----2FA密钥
   - 点击【➕ 一键导入账号列表】。

3. 开始全自动执行：
   - 点击单个账号右侧的【▶ 执行】或顶部的【▶ 批量全自动执行】。
   - 脚本会自动完成：
     Step 1: 自动登录 Google 并通过辅助邮箱 / 2FA 验证
     Step 2: 内联免税绑卡 + 首层 12 个月免费包 (US$0.00)
     Step 3: 叠加二层 6 个月免费包 (US$0.00 -> 18个月)
     Step 4: 叠加三层 6 个月免费包 (US$0.00 -> 24个月封顶)
     Step 5: 自动前往 Google Play 取消自动续订，锁死 2 年到期权益并截图验真


# 卡网：https://faka.cdkey.si
# 博客：https://blog.caowo.de
# TG群：@Hello_Florence
