import telebot
import random
import time
from telebot import types
from datetime import datetime, timedelta

TOKEN = "8228552053:AAH_uCJjG98SPLLqY9UDOc3Z49P3e-aRBXI"

bot = telebot.TeleBot(TOKEN)

users = {}
cooldowns = {}

# --- Проверка админа ---
def is_admin(message):
    admins = bot.get_chat_administrators(message.chat.id)
    return any(admin.user.id == message.from_user.id for admin in admins)


# --- Основной обработчик ---
@bot.message_handler(content_types=['text'])
def handle_text(message):
    text = message.text.lower().strip()
    chat_id = message.chat.id
    user_id = message.from_user.id

    print("DEBUG:", text)

    # сохраняем пользователей
    if chat_id not in users:
        users[chat_id] = set()
    users[chat_id].add(user_id)

    # --- БАН ---
    if text == "бан":
        if not is_admin(message):
            bot.reply_to(message, "Ты не админ")
            return

        if not message.reply_to_message:
            bot.reply_to(message, "Ответь на сообщение пользователя")
            return

        target = message.reply_to_message.from_user.id

        if is_admin(message.reply_to_message):
            bot.reply_to(message, "Нельзя банить админа")
            return

        bot.ban_chat_member(chat_id, target)
        bot.send_message(chat_id, "Пользователь забанен 🥀")
        return

    # --- МУТ ---
    if text.startswith("мут"):
        if not is_admin(message):
            bot.reply_to(message, "Ты не админ")
            return

        if not message.reply_to_message:
            bot.reply_to(message, "Ответь на сообщение пользователя")
            return

        parts = text.split()
        minutes = 15

        if len(parts) > 1:
            try:
                minutes = int(parts[1])
            except:
                pass

        target = message.reply_to_message.from_user.id

        if is_admin(message.reply_to_message):
            bot.reply_to(message, "Нельзя мутить админа")
            return

        bot.restrict_chat_member(
            chat_id,
            target,
            permissions=types.ChatPermissions(can_send_messages=False),
            until_date=datetime.now() + timedelta(minutes=minutes)
        )

        bot.send_message(chat_id, f"Мут на {minutes} минут 🔇")
        return

    # --- РАЗМУТ ---
    if text == "размут":
        if not is_admin(message):
            return

        if not message.reply_to_message:
            return

        target = message.reply_to_message.from_user.id

        bot.restrict_chat_member(
            chat_id,
            target,
            permissions=types.ChatPermissions(
                can_send_messages=True,
                can_send_media_messages=True,
                can_send_other_messages=True,
                can_add_web_page_previews=True
            )
        )

        bot.send_message(chat_id, "Мут снят 🔊")
        return

    # --- РАЗБАН ---
    if text == "разбан":
        if not is_admin(message):
            return

        if not message.reply_to_message:
            return

        target = message.reply_to_message.from_user.id

        bot.unban_chat_member(chat_id, target)
        bot.send_message(chat_id, "Пользователь разбанен 🟢")
        return

    # сохраняем пользователей
    if chat_id not in users:
        users[chat_id] = set()
    users[chat_id].add(user_id)

    # --- ГП ---
    if text.startswith("гп"):
        if chat_id not in users or len(users[chat_id]) == 0:
            bot.send_message(chat_id, "Нет пользователей")
            return

        user_list = list(users[chat_id])
        winner_id = random.choice(user_list)

        try:
            user = bot.get_chat_member(chat_id, winner_id).user
            name = f"@{user.username}" if user.username else user.first_name
        except:
            name = "кто-то"

        templates = [
            "☝ Я уверен, что {user} {word}",
            "🤔 Мне кажется, что {user} — {word}",
            "⚡ Скорее всего {user} — {word}",
            "🔥 Без сомнений, {user} является {word}",
            "👀 По моим данным, {user} — {word}",
            "💡 Я думаю, что {user} {word}",
            "📊 Вероятно, {user} — {word}",
            "🎯 Мой выбор: {user} = {word}",
        ]

        parts = text.split(maxsplit=1)
        word = parts[1] if len(parts) > 1 else "пилот"

        template = random.choice(templates)

        bot.send_message(
            chat_id,
            template.format(user=name, word=word)
        )
        return

    # (тут остаются твои бан/мут/шиппер и т.д.)

bot.infinity_polling() 
