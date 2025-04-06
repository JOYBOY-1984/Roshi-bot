# Roshi-bot
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes
import random
import os

# Load bot token from environment variable
BOT_TOKEN = os.getenv("6510639679:AAG9HL7b6h1KW6lEqjkHkh9nh51VRPBVoE0")

# In-memory user data
users = {}

def get_form(power):
    if power < 1000:
        return "Earthling"
    elif power < 5000:
        return "Saiyan"
    elif power < 10000:
        return "Super Saiyan"
    elif power < 50000:
        return "Super Saiyan 2"
    elif power < 100000:
        return "Super Saiyan 3"
    elif power < 500000:
        return "Ultra Instinct"
    else:
        return "Legendary Saiyan God"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    if user.id not in users:
        users[user.id] = {"name": user.first_name, "power": 0}
        await update.message.reply_text(
            f"Welcome to *Dragon Ball Bounty Hunt*, {user.first_name}!\nUse /train to increase your power level!",
            parse_mode="Markdown"
        )
    else:
        await update.message.reply_text("You're already registered, warrior!")

async def train(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.effective_user.id
    if user_id not in users:
        await update.message.reply_text("Use /start to begin your training.")
        return

    gained = random.randint(500, 3000)
    users[user_id]["power"] += gained
    form = get_form(users[user_id]["power"])
    await update.message.reply_text(
        f"You trained and gained {gained} power!\n"
        f"Your current power level: {users[user_id]['power']}\n"
        f"Form: {form}"
    )

async def profile(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.effective_user.id
    if user_id not in users:
        await update.message.reply_text("Use /start to begin your journey.")
        return

    power = users[user_id]["power"]
    form = get_form(power)
    await update.message.reply_text(
        f"Your Profile:\nPower Level: {power}\nForm: {form}"
    )

async def rank(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if not users:
        await update.message.reply_text("No warriors have trained yet.")
        return

    sorted_users = sorted(users.items(), key=lambda x: x[1]["power"], reverse=True)
    message = "Top Warriors:\n"
    for i, (uid, data) in enumerate(sorted_users[:5], 1):
        message += f"{i}. {data['name']} – {data['power']} PL\n"
    await update.message.reply_text(message)

if name == 'main':
    app = ApplicationBuilder().token6510639679:AAG9HL7b6h1KW6lEqjkHkh9nh51VRPBVoE0).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("train", train))
    app.add_handler(CommandHandler("profile", profile))
    app.add_handler(CommandHandler("rank", rank))
    app.run_polling()
