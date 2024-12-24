import requests
from telegram import Update
from telegram.ext import Updater, CommandHandler, CallbackContext

# ضع هنا Token البوت الذي حصلت عليه من BotFather
TOKEN = 'YOUR_BOT_API_TOKEN'

# دالة لجلب سعر العملة الرقمية من CoinGecko
def get_crypto_price(crypto: str):
    url = f"https://api.coingecko.com/api/v3/simple/price?ids={crypto}&vs_currencies=usd"
    response = requests.get(url)
    data = response.json()
    if crypto in data:
        price = data[crypto]['usd']
        return price
    return None

# دالة للتعامل مع أمر /price في التلغرام
def price(update: Update, context: CallbackContext):
    if context.args:
        crypto = context.args[0].lower()
        price = get_crypto_price(crypto)
        if price:
            update.message.reply_text(f"سعر {crypto.upper()} الحالي هو ${price}")
        else:
            update.message.reply_text(f"لا توجد بيانات عن {crypto.upper()}. تأكد من كتابة اسم العملة بشكل صحيح.")
    else:
        update.message.reply_text("يرجى إدخال اسم العملة الرقمية بعد الأمر. مثال: /price bitcoin")

# دالة لبدء البوت
def start(update: Update, context: CallbackContext):
    update.message.reply_text('مرحباً! أنا بوت لتحليل العملات الرقمية. استخدم /price متبوعاً باسم العملة لمعرفة سعرها.')

def main():
    # إنشاء Updater مع Token
    updater = Updater(TOKEN, use_context=True)
    dispatcher = updater.dispatcher

    # إضافة الأوامر
    dispatcher.add_handler(CommandHandler("start", start))
    dispatcher.add_handler(CommandHandler("price", price))

    # تشغيل البوت
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
