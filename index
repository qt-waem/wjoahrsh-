import cv2 import pytesseract import numpy as np

إعدادات Tesseract

pytesseract.pytesseract.tesseract_cmd = '/usr/bin/tesseract'  # تأكد من مسار التثبيت في الاستضافة

def extract_rsi_stochastic(image_path): image = cv2.imread(image_path) gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

# تحسين الصورة للنصوص الصغيرة
gray = cv2.GaussianBlur(gray, (3, 3), 0)
_, thresh = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY_INV)

# قراءة النص
text = pytesseract.image_to_string(thresh, config='--psm 6')

# استخراج قيمة RSI من النص
rsi_value = None
stochastic_values = []

for line in text.split("\n"):
    if "RSI" in line.upper():
        for token in line.split():
            if token.replace('.', '', 1).isdigit():
                rsi_value = float(token)
    if any(s in line.lower() for s in ["stochastic", "ستوكاستيك"]):
        for token in line.split():
            if token.replace('.', '', 1).isdigit():
                stochastic_values.append(float(token))

return rsi_value, stochastic_values[:2]  # %K و%D

def analyze_signals(rsi, stochastic): rsi_signal = "" if rsi: if rsi >= 70: rsi_signal = f"RSI = {rsi} (تشبع شراء)" elif rsi <= 30: rsi_signal = f"RSI = {rsi} (تشبع بيع)" else: rsi_signal = f"RSI = {rsi} (حيادي)"

stoch_signal = ""
if len(stochastic) == 2:
    k, d = stochastic
    if k < 20 and d < 20 and k > d:
        stoch_signal = "Stochastic تقاطع صعودي عند منطقة تشبع بيع"
    elif k > 80 and d > 80 and k < d:
        stoch_signal = "Stochastic تقاطع هبوطي عند منطقة تشبع شراء"
    else:
        stoch_signal = f"Stochastic %K={k}, %D={d} (لا توجد إشارة واضحة)"

# نعتبر الاتجاه من شكل السعر نفسه في النسخة التالية (مرحليًا يدوي)
trend = "الاتجاه: هابط ⬇️"

# التوصية
if rsi and rsi <= 30 and len(stochastic) == 2 and stochastic[0] > stochastic[1]:
    recommendation = "✅ التوصية: شراء في بداية الشمعة القادمة"
elif rsi and rsi >= 70 and len(stochastic) == 2 and stochastic[0] < stochastic[1]:
    recommendation = "✅ التوصية: بيع في بداية الشمعة القادمة"
else:
    recommendation = "⚠️ لا توجد توصية واضحة حالياً"

result = f"🔍 التحليل الفني:\n- {rsi_signal}\n- {stoch_signal}\n- {trend}\n{recommendation}"
return result

مثال استخدام

image_path = "chart_sample.jpg" rsi_val, stoch_vals = extract_rsi_stochastic(image_path) result = analyze_signals(rsi_val, stoch_vals) print(result)
