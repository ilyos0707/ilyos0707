-import requests
from telegram import Bot
import time

# Telegram bot tokeni va chat ID
bot_token = '7901327706:AAEHkncoMNa-Sg0vhgSp-XCeKpCFHOTLe8E'
chat_id = '933617482'

# Mahsulot sahifalarining URL manzillari
product_urls = [
    'https://uz.qlever.asia/store/show/598',
    'https://uz.qlever.asia/store/show/409',
    'https://uz.qlever.asia/store/show/544'
]

# Telegram botini yaratish
bot = Bot(token=bot_token)

# Mahsulot holatini tekshirish funksiyasi
def check_product_status():
    for url in product_urls:
        try:
            response = requests.get(url)
            response.raise_for_status()
            page_content = response.text

            # Sahifada "Sotilib ketdi" va "Xarid qilmoq" yozuvlarini tekshirish
            if "Sotilib ketdi" not in page_content and "Xarid qilmoq" in page_content:
                product_name = extract_product_name(page_content)
                message = f'{product_name} mavjud! {url}'
                bot.send_message(chat_id=chat_id, text=message)
        except requests.RequestException as e:
            print(f"Error fetching product at {url}: {e}")

# Mahsulot nomini sahifadan olish
def extract_product_name(page_content):
    start = page_content.find('<h1')
    end = page_content.find('</h1>', start)
    return page_content[start:end].split('>')[-1]

# Har 5 daqiqada mahsulot holatini tekshirish
while True:
    check_product_status()
    time.sleep(300)  # 5 daqiqa kutish
