from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import ApplicationBuilder, CallbackQueryHandler, CommandHandler, ContextTypes
import logging

# Включаем логирование (для отслеживания ошибок)
logging.basicConfig(format="%(asctime)s - %(name)s - %(levelname)s - %(message)s", level=logging.INFO)
logger = logging.getLogger(__name__)

# Словарь для хранения баллов пользователей и рефералов
user_scores = {}
user_referrals = {}

# Функция для проверки подписки на канал
async def check_subscription(update: Update, user_id: int) -> bool:
    chat_member = await update.bot.get_chat_member(chat_id="@your_channel_username", user_id=user_id)
    return chat_member.status in ["member", "administrator", "creator"]

# Команда /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:
    user_id = update.effective_user.id
    referral_code = context.args[0] if context.args else None  # Получаем реферальный код, если есть
    
    if referral_code:
        if referral_code not in user_referrals:
            user_referrals[referral_code] = 0
        user_referrals[referral_code] += 1  # Увеличиваем количество рефералов
    
    user_scores[user_id] = user_scores.get(user_id, 0)  # Инициализируем баллы пользователя
    
    # Проверка подписки на канал
    if await check_subscription(update, user_id):
        await update.message.reply_text("Добро пожаловать! Вы можете продолжить использовать бота.")
    else:
        keyboard = [[InlineKeyboardButton("Подписаться на канал", url="https://t.me/your_channel_username")]]
        reply_markup = InlineKeyboardMarkup(keyboard)
        await update.message.reply_text("Пожалуйста, подпишитесь на канал и продолжите:", reply_markup=reply_markup)
    
    # Кнопка для начала игры
    keyboard = [[InlineKeyboardButton("Нажать!", callback_data="click")]]
    reply_markup = InlineKeyboardMarkup(keyboard)
    await update.message.reply_text("Приветствуем вас в кликер-боте! Нажмите кнопку, чтобы начать:", reply_markup=reply_markup)

# Функция для обработки нажатий на кнопку
async def button_click(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:
    query = update.callback_query
    user_id = query.from_user.id
    
    # Увеличиваем баллы за клик
    if await check_subscription(update, user_id):
        if user_id in user_scores:
            user_scores[user_id] += 1
        else:
            user_scores[user_id] = 1
        await query.answer(f"Ваши баллы: {user_scores[user_id]}")
        await query.edit_message_text(f"Ваши баллы: {user_scores[user_id]}")
    else:
        await query.answer("Необходимо подписаться на канал для получения монет за клики!")

# Функция для проверки подписки и начисления 100 монет
async def referral(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:
    user_id = update.effective_user.id
    if await check_subscription(update, user_id):
        if user_id not in user_scores:
            user_scores[user_id] = 0
        # Начисление 100 монет за подписку на канал
        user_scores[user_id] += 100
        await update.message.reply_text(f"Вы подписались на канал и получили 100 монет! Ваши баллы: {user_scores[user_id]}")
    else:
        await update.message.reply_text("Пожалуйста, подпишитесь на канал для получения монет.")

# Основная программа
if __name__ == "__main__":
    TOKEN = "7767626944:AAFUj3q-qweoo39px-By9ao1qELoT0q-XYc"  # Вставьте сюда ваш API токен

    app = ApplicationBuilder().token(TOKEN).build()

    # Добавляем обработчики команд
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CallbackQueryHandler(button_click))
    app.add_handler(CommandHandler("referal", referral))

    print("Бот OneCoin запущен!")
    app.run_polling()