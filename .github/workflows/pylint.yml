from telebot import TeleBot, types
import threading, time, random, re

# --- BOT TOKENS ---
MEMBER_BOT_TOKEN = "8085370815:AAFc5Tl4kKBR2KTQ44grdDPtyipU6nFDzLE"
ADMIN_BOT_TOKEN  = "8368537732:AAF_7HR3dj2TH5I9Z-88oKCO5DzuchF0TtA"
GAME_BOT_TOKEN   = "8239936335:AAE8UEq0OtM0h07IUG_IoKKLP8T62Yco4Y0"

# --- CHAT IDS ---
ADMIN_CHAT_ID = 6385759989
ADMIN_LINK = "@Lotteryfather100000000"
GAME_GROUP_ID = -1002861733704

# --- BOT OBJECTS ---
member_bot = TeleBot(MEMBER_BOT_TOKEN)
admin_bot  = TeleBot(ADMIN_BOT_TOKEN)
game_bot   = TeleBot(GAME_BOT_TOKEN)

# --- DATABASE ---
users = {}       # chatid -> {name,phone,bank,balance,locked}
bets = {}        # round_number -> list of bets
round_number = 1
current_result = None

# --- MEMBER BOT ---
@member_bot.message_handler(commands=["start"])
def start_member(m):
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add("အကောင့်အသေးစိတ်",
               "လက်ကျန် စစ်ရန်",
               "ငွေထုတ်ရန်",
               "ငွေထည့်ရန်",
               "ဂိမ်းဆော့နည်းများ")
    member_bot.send_message(m.chat.id,
        "အာလုံး မင်္ဂလာပါ 🙏\n📌 အကောင့်အသေးစိတ်\n"
        "📌 လက်ကျန် စစ်ရန်\n📌 ငွေထုတ်တောင်းရန်\n"
        "📌 ငွေထည့်ရန်\n📌 ဂိမ်းဆော့နည်းများ",
        reply_markup=markup
    )

# အကောင့်အသေးစိတ်
@member_bot.message_handler(func=lambda msg: msg.text.startswith("အကောင့်အသေးစိတ်"))
def check_account(m):
    cid = m.chat.id
    if cid in users:
        u = users[cid]
        txt = (f"✅ သင့်အကောင့်\n\n🆔 {cid}\n👤 {u['name']}\n"
               f"📞 {u['phone']}\n🏦 {u['bank']}\n💰 {u['balance']} Ks\n"
               f"🔒 Bet Locked: {u['locked']} Ks")
        member_bot.send_message(cid, txt)
    else:
        member_bot.send_message(cid, f"❌ သင့်အကောင့်မရှိပါ\n👉 Admin - {ADMIN_LINK}\n👉 chatid - {cid}")

# လက်ကျန် စစ်ရန်
@member_bot.message_handler(func=lambda msg: msg.text.startswith("လက်ကျန် စစ်ရန်"))
def check_balance(m):
    cid = m.chat.id
    if cid in users:
        u = users[cid]
        member_bot.send_message(cid, f"💰 လက်ကျန်: {u['balance']} Ks\n🔒 Locked: {u['locked']} Ks")
    else:
        member_bot.send_message(cid, f"❌ Admin က မဖွင့်ပေးသေးပါ\n👉 {ADMIN_LINK}\n👉 chatid - {cid}")

# ငွေထုတ်ရန်
@member_bot.message_handler(func=lambda msg: msg.text.startswith("ငွေထုတ်ရန်"))
def withdraw_request(m):
    cid = m.chat.id
    if cid not in users:
        member_bot.send_message(cid, f"❌ သင့်အကောင့်မရှိပါ\n👉 {ADMIN_LINK}\n👉 chatid - {cid}")
        return
    u = users[cid]
    if u["locked"] > 0:
        member_bot.send_message(cid, f"❌ Locked bet {u['locked']} Ks ရှိသေးတာကြောင့် ထုတ်မရသေးပါ")
        return
    msg = member_bot.send_message(cid, "💵 ထုတ်ချင်တဲ့ ငွေပမာဏ ရိုက်ထည့်ပါ (min: 5000)")
    member_bot.register_next_step_handler(msg, process_withdraw)

def process_withdraw(m):
    cid = m.chat.id
    if cid not in users: return
    try:
        amount = int(m.text)
        if amount < 5000:
            member_bot.send_message(cid, "❌ 5000 Ks အထက်သာ ထုတ်နိုင်ပါသည်")
            return
        if amount > users[cid]["balance"]:
            member_bot.send_message(cid, f"❌ လက်ကျန်မလုံလောက်ပါ ({users[cid]['balance']} Ks)")
            return
        u = users[cid]
        admin_bot.send_message(
            ADMIN_CHAT_ID,
            f"💸 ငွေထုတ်တောင်းဆိုမှု\n👤 {u['name']}\n📞 {u['phone']}\n🏦 {u['bank']}\n🆔 {cid}\nပမာဏ: {amount} Ks"
        )
        member_bot.send_message(cid, "✅ Admin သို့ ငွေထုတ်တောင်းဆိုမှု ပေးပို့ပြီးပါပြီ")
    except:
        member_bot.send_message(cid, "❌ ငွေပမာဏ format မမှန်ပါ")

# ငွေထည့်ရန်
@member_bot.message_handler(func=lambda msg: msg.text.startswith("ငွေထည့်ရန်"))
def deposit_request(m):
    cid = m.chat.id
    if cid not in users:
        member_bot.send_message(cid, f"❌ သင့်အကောင့်မရှိပါ\n👉 Admin - {ADMIN_LINK}\n👉 chatid - {cid}")
        return
    member_bot.send_message(cid, f"💳 ငွေထည့်ရန် 👉 Admin ကို ဆက်သွယ်ပါ: {ADMIN_LINK}")

# ဂိမ်းဆော့နည်းမျာ
@member_bot.message_handler(func=lambda msg: msg.text.startswith("ဂိမ်းဆော့နည်းများ"))
def rules(m):
    txt = ("🎮 ဂိမ်းဆော့နည်းများ\n\n👉 Group ထဲမှာ\nP\nChoice Amount\nလိုရေးပြီး လောင်းနိုင်မယ်\n\n"
           "Available Choices:\n1. Big / Small\n2. Tiger / Dragon\n3. Even / Odd\n\n"
           "➡️ တစ်ပွဲ = တစ်ခါသာ လောင်းခွင့်\n➡️ တစ်ခါ = အများဆုံး 2 choice\n"
           "➡️ အနည်းဆုံး bet 30 Ks\n➡️ Withdraw အနည်းဆုံး 5000 Ks\n"
           "➡️ ငွေထည့်ရင် လောင်းကြေးတင်ပြီးမှ အသုံးပြုနိုင်မယ်\n\n"
           "👉 Game Link: https://t.me/+eqsl7zTfu5VjNTJl")
    member_bot.send_message(m.chat.id, txt)

# --- ADMIN BOT ---
@admin_bot.message_handler(func=lambda m: m.text.startswith("/ACC_"))
def acc_create(m):
    if m.chat.id != ADMIN_CHAT_ID: return
    try:
        parts = m.text.split()
        chatid = int(parts[0].replace("/ACC_",""))
        name, phone, bank, balance = parts[1], parts[2], parts[3], int(parts[4])
        users[chatid] = {"name":name,"phone":phone,"bank":bank,"balance":balance,"locked":0}
        admin_bot.send_message(m.chat.id, f"🔎 အကောင့်ဖွင့်ပြီး {name} ({chatid})")
        member_bot.send_message(chatid, f"🎉 သင့်အကောင့်ဖွင့်ပြီးပါပြီ!\n💰 {balance} Ks")
    except Exception as e:
        admin_bot.send_message(m.chat.id, f"❌ Error: {e}")

@admin_bot.message_handler(func=lambda m: m.text.startswith("/DP_"))
def deposit(m):
    if m.chat.id != ADMIN_CHAT_ID: return
    try:
        text = m.text.replace("/DP_","")
        chatid_str, amount_str = text.split("_")
        chatid = int(chatid_str); amount = int(amount_str.replace("k","").replace("K",""))
        if chatid not in users: 
            admin_bot.send_message(m.chat.id, f"❌ User {chatid} မရှိပါ"); return
        users[chatid]["balance"] += amount
        bal = users[chatid]["balance"]
        admin_bot.send_message(m.chat.id, f"✅ {users[chatid]['name']} ({chatid}) +{amount} Ks\nBalance: {bal}")
        member_bot.send_message(chatid, f"💳 {amount} Ks ထည့်ပြီးပါပြီ\n💰 လက်ကျန်: {bal} Ks")
    except Exception as e:
        admin_bot.send_message(m.chat.id, f"❌ Error: {e}")

# --- GAME BOT ---
@game_bot.message_handler(func=lambda m: m.chat.id==GAME_GROUP_ID)
def handle_bet(m):
    global bets, round_number
    try:
        lines = m.text.strip().split("\n")
        if len(lines)<2: return
        if not lines[0].startswith("P"): return
        rn = int(re.sub(r"\D","",lines[0]))
        cid = m.from_user.id
        if cid not in users:
            game_bot.reply_to(m, f"❌ သင့်အကောင့်မရှိပါ\n👉 {ADMIN_LINK}\nchatid {cid}")
            return
        if rn != round_number:
            game_bot.reply_to(m, f"❌ P {round_number} အတွက်သာ လောင်းနိုင်")
            return
        for line in lines[1:]:
            choice, amount = line.split()
            choice=choice.lower(); amount=int(amount)
            if amount<30:
                game_bot.reply_to(m,"❌ အနည်းဆုံး 30 Ks"); return
            if users[cid]["balance"]<amount:
                game_bot.reply_to(m,f"❌ လက်ကျန်မလုံလောက် {users[cid]['balance']} Ks"); return
            users[cid]["balance"]-=amount
            users[cid]["locked"]+=amount
            bets.setdefault(rn,[]).append({"chatid":cid,"choice":choice,"amount":amount})
        game_bot.reply_to(m,f"✅ Bet success {users[cid]['name']} ({cid})")
    except Exception as e:
        print("bet error:", e)

def decide_result(rn):
    totals={"big":0,"small":0,"tiger":0,"dragon":0,"even":0,"odd":0}
    players=set()
    if rn in bets:
        for b in bets[rn]:
            totals[b["choice"]]+=b["amount"]; players.add(b["chatid"])
    # Single player
    if len(players)==1:
        if random.random()<0.88: # lose
            only=bets[rn][0]["choice"]
            if only=="big": return ("small","tiger","even")
            if only=="small": return ("big","dragon","odd")
            if only=="tiger": return ("dragon","big","odd")
            if only=="dragon": return ("tiger","small","even")
            if only=="even": return ("odd","big","tiger")
            if only=="odd": return ("even","small","dragon")
        else:
            return (bets[rn][0]["choice"],"tiger","even")
    # Multi player
    big_small="big" if totals["big"]<=totals["small"] else "small"
    tiger_dragon="tiger" if totals["tiger"]<=totals["dragon"] else "dragon"
    even_odd="even" if totals["even"]<=totals["odd"] else "odd"
    return (big_small,tiger_dragon,even_odd)

def game_loop():
    global round_number,current_result
    while True:
        current_result=decide_result(round_number)
        try:
            game_bot.send_message(GAME_GROUP_ID,f"🎲 P {round_number}\n👉 ရလဒ်: {current_result}")
        except: pass
        if round_number in bets:
            for b in bets[round_number]:
                cid,choice,amount=b["chatid"],b["choice"],b["amount"]
                win=False
                if choice in current_result: win=True
                users[cid]["locked"]-=amount
                if win:
                    payout=amount*2; users[cid]["balance"]+=payout
                    game_bot.send_message(GAME_GROUP_ID,f"🏆 {users[cid]['name']} ({cid}) +{payout} Ks အနိုင်")
                    member_bot.send_message(cid,f"🏆 အနိုင် {payout} Ks\n💰 {users[cid]['balance']}")
                else:
                    game_bot.send_message(GAME_GROUP_ID,f"❌ {users[cid]['name']} ({cid}) အရှုံး")
                    member_bot.send_message(cid,f"❌ အရှုံး\n💰 {users[cid]['balance']}")
        bets.pop(round_number,None)
        round_number+=1
        time.sleep(5)
        try: game_bot.send_message(GAME_GROUP_ID,f"🎲 P {round_number} စတင်လောင်းနိုင်ပါပြီ\nBig/Small\nTiger/Dragon\nEven/Odd")
        except: pass
        time.sleep(55)

# --- START THREADS ---
threading.Thread(target=member_bot.polling,daemon=True).start()
threading.Thread(target=admin_bot.polling,daemon=True).start()
threading.Thread(target=game_loop,daemon=True).start()
game_bot.polling()
