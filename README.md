# راهنمای اجرای Pocket TTS فارسی (ONNX) با کلون صدا

راهنمای اجرای مدل [`pocket-tts-farsi-v2-onnx`](https://huggingface.co/Nimaone/pocket-tts-farsi-v2-onnx) روی ویندوز (CPU) با استفاده از مخزن [`nimaone/persian_tts`](https://github.com/nimaone/persian_tts)، به‌علاوه یک نوتبوک برای Google Colab.

> مدل تحت مجوز **CC-BY-NC-4.0** منتشر شده است؛ فقط برای استفاده غیرتجاری.

## فایل‌ها

- [`pocket_tts_farsi_v2_onnx_voice_cloning_cpu_gpu.ipynb`](pocket_tts_farsi_v2_onnx_voice_cloning_cpu_gpu.ipynb) — نوتبوک Colab: نصب، دانلود مدل، آپلود صدای مرجع و تولید گفتار.

## اجرای محلی روی ویندوز

پیش‌نیاز: Python 3.10 و git. دستورها را داخل PowerShell یا Git Bash بزنید.

### ۱. دانلود کد پروژه

```bash
cd D:/
git clone https://github.com/nimaone/persian_tts.git
cd persian_tts
```

### ۲. ساخت محیط مجازی

```bash
py -3.10 -m venv env
```

### ۳. نصب کتابخانه‌ها (حدود ۲۰۰ مگابایت)

```bash
env/Scripts/python.exe -m pip install onnxruntime numpy scipy soundfile sentencepiece pedalboard typer fastapi uvicorn pydantic python-multipart "huggingface_hub[cli]"
```

### ۴. دانلود مدل ONNX (حدود ۴۹۰ مگابایت)

```bash
env/Scripts/hf.exe download Nimaone/pocket-tts-farsi-v2-onnx --local-dir model/onnx
```

### ۵. دانلود توکنایزر و نرمال‌ساز فارسی

```bash
env/Scripts/hf.exe download mehdi-hf/pocket-tts-farsi-v2 tokenizer_ph.model normalize_fa.py --local-dir model/v2
```

### ۶. تست با صدای آماده

```bash
env/Scripts/python.exe scripts/tts_onnx.py "سلام، حال شما چطور است؟"
```

خروجی: `output/tts_onnx.wav`

### ۷. کلون صدای خودتان

خط فرمان (فایل WAV تمیز، حدود ۵ ثانیه):

```bash
env/Scripts/python.exe scripts/tts_onnx.py "یکی بود یکی نبود" D:/my_voice.wav output/my_test.wav
```

یا وب‌دمو در http://127.0.0.1:8000 :

```bash
env/Scripts/python.exe scripts/server.py
```

> اگر یک محیط مجازی دیگر (مثلاً `(parsbert-env)`) در ترمینال فعال است، مشکلی ایجاد نمی‌کند چون پایتونِ `env/Scripts/python.exe` مستقیم صدا زده می‌شود؛ برای خاموش کردنش `deactivate` (یا `conda deactivate`) بزنید.

## بهتر کردن کیفیت گفتار

1. **صدای مرجع** (بیشترین تأثیر)
   - کمتر از ۵ ثانیه؛ صدای طولانی‌تر باعث می‌شود مدل جمله خودش را ادامه دهد.
   - اتاق ساکت، بدون اکو، میکروفون نزدیک، فقط یک گوینده.
   - یک جمله کامل و طبیعی با لحن ثابت؛ شروعش خیلی بلندتر از بقیه نباشد.
   - WAV با ۲۴ یا ۴۸ کیلوهرتز (نه MP3 فشرده).
2. **متن**
   - علامت‌گذاری درست: ویرگول مکث کوتاه، نقطه مکث متوسط، پایان جمله مکث بلند.
   - جمله‌های کوتاه.
   - عدد و مخفف را با حروف بنویسید («بیست و پنج» به‌جای «۲۵»).
   - نوشتار معیار به‌جای محاوره.
3. **تنظیمات**
   - حالت `pack` (گزینه `--pack`) روان‌تر از `split` است.
   - `pace` بین ۰٫۹ تا ۱٫۱.
   - مدل تصادفی است؛ با چند `--seed` بسازید و بهترین را نگه دارید.
4. **پس‌پردازش** (اختیاری): حذف نویز، Compressor ملایم، کمی EQ.
5. **Fine-tune**: با چند ساعت صدای تمیز یک گوینده؛ نیازمند GPU قوی (مثلاً Colab).

## دیتاست‌های صوتی فارسی

| دیتاست | حجم | گوینده | مجوز |
|---|---|---|---|
| [ManaTTS](https://huggingface.co/datasets/MahtaFetrat/Mana-TTS) | حدود ۸۶ ساعت، ۴۴٫۱ کیلوهرتز | یک گوینده زن | CC-0 |
| [ParsiGoo](https://huggingface.co/datasets/Kamtera/ParsiGoo) | حدود ۵ ساعت | ۶ گوینده | CC0 یا CC BY-SA (منابع متفاوت) |
| [ArmanTTS](https://arxiv.org/abs/2304.03585) | حدود ۳۰ ساعت خام | یک گوینده | بررسی شود |
| [ParsVoice](https://huggingface.co/datasets/MohammadJRanjbar/ParsVoice) | حدود ۲۲۰۰ ساعت | بیش از ۱۸۰۰ گوینده | نیازمند ثبت درخواست |

فهرست کامل‌تر: [awesome-Persian-Speech](https://github.com/karim23657/awesome-Persian-Speech)

## اعتبار

- موتور ONNX و وب‌دمو: [nimaone/persian_tts](https://github.com/nimaone/persian_tts)
- مدل اصلی: [mehdi-hf/pocket-tts-farsi-v2](https://huggingface.co/mehdi-hf/pocket-tts-farsi-v2)
