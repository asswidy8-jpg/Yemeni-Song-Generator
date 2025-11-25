# ===============================================================
# مشروع: مولد شيلات يمنية وخليجية
# الوصف: برنامج كامل لإنتاج شيلات بأسلوب وألحان مشابهة
# المؤدين: أبو شهاب الخبجي، عيسى الليث، بدر العزي، عبد الله الفروان، وغيرهم
# المخرجات: ملفات صوتية بصيغ MP3 و M4A
# متوافق مع: Glitch / StackBlitz / Codespaces
# ===============================================================

import os
from pathlib import Path
from gtts import gTTS  # لتحويل النص إلى كلام
from pydub import AudioSegment  # لمعالجة الصوت
import random

# ---------------------------
# إعداد مجلدات الإنتاج
# ---------------------------
output_dir = Path("output_shilat")
output_dir.mkdir(exist_ok=True)

# ---------------------------
# قاعدة بيانات بسيطة لألحان
# ---------------------------
melodies = {
    "الحروف الصادقة": "melody1.mp3",
    "شيلة بدوية": "melody2.mp3",
    "شيلة خليجية": "melody3.mp3"
}

# ---------------------------
# قاعدة بيانات أصوات مقترحة
# ---------------------------
voices = ["ar-xa", "ar-ye", "ar-sa"]  # لهجات عربية متنوعة

# ---------------------------
# وظيفة تحويل النص إلى كلام وإضافة اللحن
# ---------------------------
def generate_shila(text, melody_name="الحروف الصادقة", voice="ar-xa"):
    # 1. تحويل النص إلى كلام
    tts = gTTS(text=text, lang='ar', tld="com", slow=False)
    temp_tts_file = output_dir / "temp_tts.mp3"
    tts.save(str(temp_tts_file))
    
    # 2. إضافة اللحن
    melody_file = Path(melodies.get(melody_name, "melody1.mp3"))
    if not melody_file.exists():
        print(f"تنبيه: ملف اللحن {melody_file} غير موجود، سيتم استخدام صوت بدون لحن.")
        final_audio = AudioSegment.from_mp3(temp_tts_file)
    else:
        melody_audio = AudioSegment.from_mp3(melody_file)
        tts_audio = AudioSegment.from_mp3(temp_tts_file)
        # مزج الصوت مع اللحن
        final_audio = melody_audio.overlay(tts_audio)
    
    # 3. حفظ الملفات النهائية بصيغ متعددة
    output_mp3 = output_dir / f"shila_{random.randint(1000,9999)}.mp3"
    output_m4a = output_dir / f"shila_{random.randint(1000,9999)}.m4a"
    
    final_audio.export(output_mp3, format="mp3")
    final_audio.export(output_m4a, format="mp4")
    
    print(f"تم إنشاء الشيلة! MP3: {output_mp3}, M4A: {output_m4a}")
    return output_mp3, output_m4a

# ---------------------------
# واجهة الأوامر
# ---------------------------
def main():
    print("=== مولد الشيلات اليمنية والخليجية ===")
    text = input("اكتب نص الشيلة: ")
    print("اختر اللحن المتاح:")
    for idx, key in enumerate(melodies.keys(), start=1):
        print(f"{idx}. {key}")
    melody_choice = input("رقم اللحن: ")
    melody_name = list(melodies.keys())[int(melody_choice)-1]
    
    print("اختر اللهجة الصوتية:")
    for idx, voice in enumerate(voices, start=1):
        print(f"{idx}. {voice}")
    voice_choice = input("رقم اللهجة: ")
    voice_selected = voices[int(voice_choice)-1]
    
    generate_shila(text, melody_name, voice_selected)

if __name__ == "__main__":
    main()
