import requests, time, re, os

TELEGRAM_BOT_TOKEN = os.getenv("7886061937:AAFqHvzEs2kxffLMmzKHTNHiSCBKYoZGy5c")
TELEGRAM_CHAT_ID = os.getenv("8042792215")
CHECK_INTERVAL = 5  # วินาที

products = [
    {
        "name": "POP MART THE MONSTERS Big into Energy Series Vinyl Plush Pendant Blind Box",
        "url": "https://shopee.co.th/POP-MART-THE-MONSTERS-Big-into-Energy-Series-Vinyl-Plush-Pendant-Blind-Box-i.569947420.27234772347"
    },
    {
        "name": "POP MART THE MONSTERS Big in Energy Series ชุดคอมโบจี๊ตุกตาไวนิล",
        "url": "https://shopee.co.th/POP-MART-THE-MONSTERS-Big-in-Energy-Series-%E0%B8%8A%E0%B8%B8%E0%B8%94%E0%B8%84%E0%B8%AD%E0%B8%A1%E0%B9%82%E0%B8%9A%E0%B8%88%E0%B8%B5%E0%B9%89%E0%B8%95%E0%B8%B8%E0%B9%8A%E0%B8%81%E0%B8%95%E0%B8%B2%E0%B9%84%E0%B8%A7%E0%B8%99%E0%B8%B4%E0%B8%A5-i.569947420.40252271039"
    },
    {
        "name": "POP MART THE MONSTERS Exciting Macarons Vinyl Face Blind Box",
        "url": "https://shopee.co.th/POP-MART-THE-MONSTERS-Exciting-Macarons-Vinyl-Face-Blind-Box-i.569947420.25744569563"
    },
    {
        "name": "POP MART THE MONSTERS FALL IN WILD SERIES Vinyl",
        "url": "https://shopee.co.th/%E0%B8%88%E0%B8%B5%E0%B9%89%E0%B8%95%E0%B8%B8%E0%B9%8A%E0%B8%81%E0%B8%95%E0%B8%B2%E0%B9%84%E0%B8%A7%E0%B8%99%E0%B8%B4%E0%B8%A5-POP-MART-THE-MONSTERS-FALL-IN-WILD-SERIES-Vinyl-i.569947420.24023612607"
    },
    {
        "name": "POP MART THE MONSTERS มีกล่องตาบอดตุ๊กตาไวนิลที่นั่ง",
        "url": "https://shopee.co.th/POP-MART-THE-MONSTERS-%E0%B8%A1%E0%B8%B5%E0%B8%81%E0%B8%A5%E0%B9%88%E0%B8%AD%E0%B8%87%E0%B8%95%E0%B8%B2%E0%B8%9A%E0%B8%AD%E0%B8%94%E0%B8%95%E0%B8%B8%E0%B9%8A%E0%B8%81%E0%B8%95%E0%B8%B2%E0%B9%84%E0%B8%A7%E0%B8%99%E0%B8%B4%E0%B8%A5%E0%B8%97%E0%B8%B5%E0%B9%88%E0%B8%99%E0%B8%B1%E0%B9%88%E0%B8%87-i.569947420.27769216275"
    },
    {
        "name": "POP MART THE MONSTERS Big into Energy Series Vinyl Plush Pendant Blind Box (Whole Set)",
        "url": "https://shopee.co.th/POP-MART-THE-MONSTERS-Big-into-Energy-Series-Vinyl-Plush-Pendant-Blind-Box-(Whole-Set)-i.569947420.28334763803"
    },
    {
        "name": "POP MART THE MONSTERS Tasty Macarons Vinyl Face Blind Box (Full set)",
        "url": "https://shopee.co.th/POP-MART-THE-MONSTERS-Tasty-Macarons-Vinyl-Face-Blind-Box(Full-set-link)-i.569947420.28360279305"
    },
    {
        "name": "POP MART THE MONSTERS Have a Seat Vinyl Plush Blind Box (Whole set)",
        "url": "https://shopee.co.th/POP-MART-THE-MONSTERS-Have-a-Seat-Vinyl-Plush-Blind-Box-(whole-set-link)-i.569947420.26959110842"
    },
    {
        "name": "POP MART Bikini Bottom Buddies Whimsical Plush Blind Box",
        "url": "https://shopee.co.th/POP-MART-Bikini-Bottom-Buddies-Whimsical-Plush-Blind-Box-Action-Toys-Figure-Birthday-Gift-Kid-Toy-i.569947420.41800799230"
    },
    {
        "name": "POP MART CRYBABY Wild but Cutie Series Vinyl Plush Pendant Blind Box",
        "url": "https://shopee.co.th/POP-MART-CRYBABY-Wild-but-Cutie-Series-Vinyl-Plush-Pendant-Blind-Box-i.569947420.44450804839"
    }
]

notified_flags = {p["url"]: False for p in products}

def send_telegram(product):
    msg = f"📦 มีสินค้าเข้าแล้ว!\n\n{product['name']}\n🔗 {product['url']}"
    url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"
    data = {"chat_id": TELEGRAM_CHAT_ID, "text": msg}
    requests.post(url, data=data)
    print(f"✅ แจ้งเตือน: {product['name']}")

def check_stock(url):
    try:
        res = requests.get(url, headers={"User-Agent": "Mozilla/5.0"})
        return not re.search(r"สินค้าหมด|out of stock", res.text, re.IGNORECASE)
    except Exception as e:
        print(f"❌ Error: {e}")
        return False

while True:
    for p in products:
        in_stock = check_stock(p["url"])
        if in_stock and not notified_flags[p["url"]]:
            send_telegram(p)
            notified_flags[p["url"]] = True
        elif not in_stock:
            print(f"❌ หมดสต็อก: {p['name']}")
            notified_flags[p["url"]] = False
    print("⏳ รอรอบถัดไป...\n")
    time.sleep(CHECK_INTERVAL)
