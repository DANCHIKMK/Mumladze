
import telebot
import random

# Вставьте свой токен бота ЗДЕСЬ!
BOT_TOKEN = "7901609476:AAHkFGf2JpWuUTRg0YHPKI4HHfe_FgMCLBE"  # Замените YOUR_BOT_TOKEN на фактический токен
if not BOT_TOKEN:
    print("Ошибка: Не указан токен бота. Пожалуйста, вставьте свой токен в переменную BOT_TOKEN.")
    exit()

# Создание экземпляра бота
bot = telebot.TeleBot(BOT_TOKEN)

# --- Структура данных для пользователей и аутентификации ---
user_credentials = {
    "admin": "admin",
    "Test": "Test",
    # Добавьте свои логины и пароли
}
authenticated_users = {}  # chat_id: login

# --- Состояния диалога ---
STATE_WAITING_LOGIN_PASSWORD = 1
user_states = {}  # chat_id: state

# --- Структура данных для пользователей и балансов ---
user_balances = {}  # login: balance
INITIAL_BALANCE = 3000
EXCHANGE_RATE = 100  # Курс обмена доллара на рубль

# --- Хранилище ключей ---
keys = {
    "vn_hack": {
        "1 DAY I": ["vn_hack_1day_1", "vn_hack_1day_2", "vn_hack_1day_3"],
        "7 DAY I": ["vn_hack_7day_1", "vn_hack_7day_2", "vn_hack_7day_3"],
        "30 DAY I": ["vn_hack_30day_1", "vn_hack_30day_2", "vn_hack_30day_3"]
    },
    "star": {
        "1 DAY I": ["star_1day_1", "star_1day_2", "star_1day_3"],
        "7 DAY I": ["star_7day_1", "star_7day_2", "star_7day_3"],
        "30 DAY I": ["star_30day_1", "star_30day_2", "star_30day_3"]
    },
    "oasis": {
        "1 DAY I": ["oasis_1day_1", "oasis_1day_2", "oasis_1day_3"],
        "7 DAY I": ["oasis_7day_1", "oasis_7day_2", "oasis_7day_3"],
        "30 DAY I": ["oasis_30day_1", "oasis_30day_2", "oasis_30day_3"]
    },
    "golden": {
        "1 DAY I": ["golden_1day_1", "golden_1day_2", "golden_1day_3"],
        "7 DAY I": ["golden_7day_1", "golden_7day_2", "golden_7day_3"],
        "30 DAY I": ["golden_30day_1", "golden_30day_2", "golden_30day_3"]
    },
    "cheto": {
        "1 DAY I": ["cheto_1day_1", "cheto_1day_2", "cheto_1day_3"],
        "7 DAY I": ["cheto_7day_1", "cheto_7day_2", "cheto_7day_3"],
        "30 DAY I": ["cheto_30day_1", "cheto_30day_2", "cheto_30day_3"]
    },
    "zolo": {
        "1 DAY": ["zolo_1day_1", "zolo_1day_2", "zolo_1day_3"],
        "3 DAY": ["zolo_3day_1", "zolo_3day_2", "zolo_3day_3"],
        "7 DAY": ["zolo_7day_1", "zolo_7day_2", "zolo_7day_3"],
        "14 DAY": ["zolo_14day_1", "zolo_14day_2", "zolo_14day_3"],
        "30 DAY": ["zolo_30day_1", "zolo_30day_2", "zolo_30day_3"],
        "60 DAY": ["zolo_60day_1", "zolo_60day_2", "zolo_60day_3"]
    },
    "fite": {
        "1 DAY": ["fite_1day_1", "fite_1day_2", "fite_1day_3"],
        "3 DAY": ["fite_3day_1", "fite_3day_2", "fite_3day_3"],
        "7 DAY": ["fite_7day_1", "fite_7day_2", "fite_7day_3"],
        "30 DAY": ["fite_30day_1", "fite_30day_2", "fite_30day_3"]
    },
    "universe": {
        "1 DAY": ["universe_1day_1", "universe_1day_2", "universe_1day_3"],
        "7 DAY": ["universe_7day_1", "universe_7day_2", "universe_7day_3"],
        "30 DAY": ["universe_30day_1", "universe_30day_2", "universe_30day_3"]
    },
    "nerohole": {
        "1 DAY": ["nerohole_1day_1", "nerohole_1day_2", "nerohole_1day_3"],
        "7 DAY": ["nerohole_7day_1", "nerohole_7day_2", "nerohole_7day_3"],
        "30 DAY": ["nerohole_30day_1", "nerohole_30day_2", "nerohole_30day_3"]
    },
    "impact": {
        "1 DAY": ["impact_1day_1", "impact_1day_2", "impact_1day_3"],
        "7 DAY": ["impact_7day_1", "impact_7day_2", "impact_7day_3"],
        "30 DAY": ["impact_30day_1", "impact_30day_2", "impact_30day_3"]
    },
    "cheat_ninja": {
        "1 day": ["cheat_ninja_1day_1", "cheat_ninja_1day_2", "cheat_ninja_1day_3"]
    },
    "kernel": {
        "1 day": ["kernel_1day_1", "kernel_1day_2", "kernel_1day_3"],
        "7 day": ["kernel_7day_1", "kernel_7day_2", "kernel_7day_3"],
        "30 day": ["kernel_30day_1", "kernel_30day_2", "kernel_30day_3"]
    },
    "anubis": {
        "1 day": ["anubis_1day_1", "anubis_1day_2", "anubis_1day_3"],
        "7 day": ["anubis_7day_1", "anubis_7day_2", "anubis_7day_3"],
        "30 day": ["anubis_30day_1", "anubis_30day_2", "anubis_30day_3"]
    }
}

# --- Функции для работы с товарами (ВРЕМЕННО) ---
def get_products_by_category(category):
   """Возвращает список товаров для указанной категории."""
   if category == "IOS":
       return [
           {"id": "vn_hack", "name": "VnHax", "prices": {"1 DAY I": 1.0 * EXCHANGE_RATE, "7 DAY I": 4.0 * EXCHANGE_RATE, "30 DAY I": 8.0 * EXCHANGE_RATE}}, # Цены в рублях
           {"id": "star", "name": "Star", "prices": {"1 DAY I": 1.0 * EXCHANGE_RATE, "7 DAY I": 4.0 * EXCHANGE_RATE, "30 DAY I": 8.0 * EXCHANGE_RATE}}, # Цены в рублях
           {"id": "oasis", "name": "Oasis", "prices": {"1 DAY I": 2.5 * EXCHANGE_RATE, "7 DAY I": 10.0 * EXCHANGE_RATE, "30 DAY I": 18.0 * EXCHANGE_RATE}}, # Цены в рублях
           {"id": "golden", "name": "Golden", "prices": {"1 DAY I": 1.0 * EXCHANGE_RATE, "7 DAY I": 4.0 * EXCHANGE_RATE, "30 DAY I": 8.0 * EXCHANGE_RATE}}, # Цены в рублях
           {"id": "cheto", "name": "Cheto", "prices": {"1 DAY I": 1.0 * EXCHANGE_RATE, "7 DAY I": 4.0 * EXCHANGE_RATE, "30 DAY I": 8.0 * EXCHANGE_RATE}}, # Цены в рублях
       ]
   elif category == "NON ROOT":
       return [
           {"id": "zolo", "name": "Zolo", "prices": {"1 DAY": 0.5 * EXCHANGE_RATE, "3 DAY": 1.2 * EXCHANGE_RATE, "7 DAY": 1.5 * EXCHANGE_RATE, "14 DAY": 2.5 * EXCHANGE_RATE, "30 DAY": 4.0 * EXCHANGE_RATE, "60 DAY": 6.0 * EXCHANGE_RATE }}, # Цены в рублях
           {"id": "fite", "name": "Fite [APK]", "prices": {"1 DAY": 0.5 * EXCHANGE_RATE, "3 DAY": 1.5 * EXCHANGE_RATE, "7 DAY": 1.8 * EXCHANGE_RATE, "30 DAY": 6.0 * EXCHANGE_RATE}}, # Цены в рублях
           {"id": "universe", "name": "Universe", "prices": {"1 DAY": 0.7 * EXCHANGE_RATE, "7 DAY": 3.5 * EXCHANGE_RATE, "30 DAY": 6.0 * EXCHANGE_RATE}}, # Цены в рублях
           {"id": "nerohole", "name": "Nerohole", "prices": {"1 DAY": 1.2 * EXCHANGE_RATE, "7 DAY": 5.0 * EXCHANGE_RATE, "30 DAY": 14.0 * EXCHANGE_RATE}}, # Цены в рублях
           {"id": "impact", "name": "Impact - THE BEST", "prices": {"1 DAY": 1.0 * EXCHANGE_RATE, "7 DAY": 5.0 * EXCHANGE_RATE, "30 DAY": 10.0 * EXCHANGE_RATE}}, # Цены в рублях
       ]
   elif category == "ROOT":
       return [
           {"id": "cheat_ninja", "name": "Cheat Ninja • SharpShooter Root", "prices": {"1 day": 1.5 * EXCHANGE_RATE}},
           {"id": "kernel", "name": "Kernel", "prices": {"1 day": 1.2 * EXCHANGE_RATE, "7 day": 5 * EXCHANGE_RATE, "30 day": 12 * EXCHANGE_RATE}},# Цены в рублях
       ]
   elif category == "PC SOFT (GAMELOOP)":
       return [
           {"id": "anubis", "name": "Anubis", "prices": {"1 day": 2.5 * EXCHANGE_RATE, "7 day": 12.0 * EXCHANGE_RATE, "30 day": 20.0 * EXCHANGE_RATE}}, # Цены в рублях
       ]
   else:
       return []


def find_product_by_id(product_id):
    """Ищет товар по его ID (ВРЕМЕННО)."""
    for category in ["IOS", "NON ROOT", "ROOT", "PC SOFT (GAMELOOP)"]:
        products = get_products_by_category(category)
        for product in products:
            if product['id'] == product_id:
                return product
    return None


# --- Функции для отображения интерфейса ---
def show_main_menu(chat_id):
    """Отображает главное меню."""
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add(telebot.types.KeyboardButton("Купить ключи"))
    markup.add(telebot.types.KeyboardButton("Правила"))
    markup.add(telebot.types.KeyboardButton("Личный Кабинет"))
    markup.add(telebot.types.KeyboardButton("Выйти"))
    bot.send_message(chat_id, "Выберите действие:", reply_markup=markup)


def show_profile(chat_id):
    """Отображает информацию о профиле пользователя."""
    login = authenticated_users.get(chat_id)
    if not login:
        bot.send_message(chat_id, "Вы не авторизованы. Используйте /start")
        return

    balance = user_balances.get(login, 0)
    message_text = f"Ваш аккаунт:\n- Логин: {login}\n- Баланс: {balance} руб."
    bot.send_message(chat_id, message_text)


def show_products_for_category(chat_id, category):
    """Отображает товары для выбранной категории (ReplyKeyboardMarkup)."""
    products = get_products_by_category(category)
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    for product in products:
        markup.add(telebot.types.KeyboardButton(product['name']))
    markup.add(telebot.types.KeyboardButton("Назад"))  # Добавим кнопку "Назад"
    bot.send_message(chat_id, f"Выберите товар в категории {category}:", reply_markup=markup)


def show_time_options(chat_id, product, category):
    """Отображает варианты времени действия (типа ключа)."""
    markup = telebot.types.InlineKeyboardMarkup()
    for duration, price in product['prices'].items():
        markup.add(telebot.types.InlineKeyboardButton(text=f"{duration} - {price:.0f} руб.", callback_data=f"time:{product['id']}:{duration}"))
    bot.send_message(chat_id, f"Выберите тип ключа для {product['name']}:", reply_markup=markup)


def show_categories(chat_id):
    """Отображает категории товаров."""
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add(telebot.types.KeyboardButton("IOS"))
    markup.add(telebot.types.KeyboardButton("NON ROOT"))
    markup.add(telebot.types.KeyboardButton("ROOT"))
    markup.add(telebot.types.KeyboardButton("PC SOFT (GAMELOOP)"))
    bot.send_message(chat_id, "Выберите категорию:", reply_markup=markup)

def show_quantity_selection(chat_id, product_id, duration):
    """Отображает клавиатуру для выбора количества ключей."""
    product = find_product_by_id(product_id)
    if not product:
        bot.send_message(chat_id, "Ошибка: Товар не найден.")
        return

    # Получаем количество доступных ключей
    available_keys = len(keys.get(product_id, {}).get(duration, []))
    if chat_id not in user_quantities:
        user_quantities[chat_id] = {}
    if product_id not in user_quantities[chat_id]:
         user_quantities[chat_id][product_id] = {"quantity": 1, "duration": duration, "max_quantity": available_keys}
    user_quantities[chat_id][product_id]["max_quantity"] = available_keys
    quantity = user_quantities[chat_id][product_id]["quantity"]

    markup = telebot.types.InlineKeyboardMarkup(row_width=2) # Устанавливаем количество кнопок в строке
    markup.add(
        telebot.types.InlineKeyboardButton("-10", callback_data=f"qty_change:-10:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("+10", callback_data=f"qty_change:10:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("-5", callback_data=f"qty_change:-5:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("+5", callback_data=f"qty_change:5:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("-1", callback_data=f"qty_change:-1:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("+1", callback_data=f"qty_change:1:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("Мин.", callback_data=f"qty_min:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("Макс.", callback_data=f"qty_max:{product_id}:{duration}"),
        telebot.types.InlineKeyboardButton("Купить", callback_data=f"confirm_pre:{product_id}:{duration}"),  # changed callback_data to "confirm_pre"
    )
    markup.add(telebot.types.InlineKeyboardButton("Назад", callback_data="back"))

    bot.send_message(
        chat_id,
        f"Выберите кол-во ключей для покупки:\n- Ключ: {product['name']} ({duration})\n- Доступно для покупки: {available_keys}\n- Выбранное количество: {quantity}",
        reply_markup=markup
    )


# --- Обработчики команд ---
@bot.message_handler(commands=['start'])
def start(message):
   """Обработчик команды /start."""
   chat_id = message.chat.id
   user_name = message.from_user.first_name or "Пользователь"

   if chat_id in authenticated_users:
       login = authenticated_users[chat_id]
       bot.send_message(chat_id, f"Привет, {login}! Вы уже авторизованы.")

       # Дальнейшая логика (приветствие и меню)
       if login not in user_balances:
           user_balances[login] = INITIAL_BALANCE
           bot.send_message(chat_id,
                            f"Привет, {user_name}! Добро пожаловать в наш магазин. На вашем балансе {INITIAL_BALANCE} руб.")
       else:
           bot.send_message(chat_id, f"Привет, {user_name}! С возвращением! На вашем балансе {user_balances[login]} руб.")

       # **Сначала отображаем главное меню, чтобы оно было доступно**
       show_main_menu(chat_id)
   else:
       markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
       markup.add(telebot.types.KeyboardButton("Войти"))
       bot.send_message(chat_id, "Введите данные, полученные от администратора в формате:\n\nЛОГИН\nПАРОЛЬ", reply_markup=markup)
       user_states[chat_id] = STATE_WAITING_LOGIN_PASSWORD


@bot.message_handler(commands=['profile'])
def profile(message):
   """Обработчик команды /profile."""
   show_profile(message.chat.id)


@bot.message_handler(commands=['balance'])
def balance(message):
   """Проверяет баланс пользователя."""
   chat_id = message.chat.id
   login = authenticated_users.get(chat_id)
   if not login:
       bot.send_message(chat_id, "Вы не авторизованы. Используйте /start")
       return

   if login in user_balances:
       bot.send_message(chat_id, f"Ваш баланс: {user_balances[login]} руб.")
   else:
       bot.send_message(chat_id, "Вы не зарегистрированы. Используйте /start")


# --- Обработчики сообщений ---
@bot.message_handler(func=lambda message: message.text == "Купить ключи")
def handle_buy_keys(message):
    """Обработчик нажатия кнопки 'Купить ключи'."""
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add(telebot.types.KeyboardButton("IOS"))
    markup.add(telebot.types.KeyboardButton("NON ROOT"))
    markup.add(telebot.types.KeyboardButton("ROOT"))
    markup.add(telebot.types.KeyboardButton("PC SOFT (GAMELOOP)"))
    markup.add(telebot.types.KeyboardButton("Назад"))  # Добавим кнопку "Назад"
    bot.send_message(message.chat.id, "Выберите категорию:", reply_markup=markup)


@bot.message_handler(func=lambda message: message.text == "Личный Кабинет")
def handle_profile(message):
    """Обработчик нажатия кнопки 'Личный Кабинет'."""
    show_profile(message.chat.id)


@bot.message_handler(func=lambda message: message.text == "Правила")
def handle_rules(message):
    """Обработчик нажатия кнопки 'Правила'."""
    # TODO: Implement the rules display logic
    bot.send_message(message.chat.id, "Здесь будут правила магазина.")


@bot.message_handler(func=lambda message: message.text == "Выйти")
def handle_exit(message):
    """Обработчик нажатия кнопки 'Выйти'."""
    # TODO: Implement the exit logic (if needed)
    bot.send_message(message.chat.id, "Вы вышли из магазина.")


@bot.message_handler(func=lambda message: message.text == "Назад")
def handle_back_to_main_menu(message):
    """Обработчик нажатия кнопки 'Назад'."""
    show_main_menu(message.chat.id)


@bot.message_handler(func=lambda message: message.text in ["IOS", "NON ROOT", "ROOT", "PC SOFT (GAMELOOP)"])
def handle_category(message):
    """Обработчик выбора категории."""
    category = message.text
    show_products_for_category(message.chat.id, category)


@bot.message_handler(func=lambda message: True)  # Обработчик для любого текстового сообщения
def handle_text(message):
    """Обработчик выбора товара."""
    chat_id = message.chat.id

    # Аутентификация
    if chat_id not in authenticated_users:
        # Если пользователь в процессе аутентификации
        if chat_id in user_states and user_states[chat_id] == STATE_WAITING_LOGIN_PASSWORD:
            try:
                login, password = message.text.split("\n", 1)
                login = login.strip()
                password = password.strip()

                if login in user_credentials and user_credentials[login] == password:
                    authenticated_users[chat_id] = login
                    del user_states[chat_id]
                    markup = telebot.types.ReplyKeyboardRemove()  # Remove the keyboard
                    bot.send_message(chat_id, "Вы успешно авторизованы!", reply_markup=markup)
                    start(message) # Запускаем логику /start после успешной авторизации
                else:
                    bot.send_message(chat_id, "Неверный логин или пароль. Попробуйте еще раз.")

            except ValueError:
                bot.send_message(chat_id, "Неверный формат ввода. Введите логин и пароль в формате:\n\nЛОГИН\nПАРОЛЬ")
        else:
            bot.send_message(chat_id, "Вы не авторизованы. Используйте /start")
        return

    category_names = ["IOS", "NON ROOT", "ROOT", "PC SOFT (GAMELOOP)"]
    if message.text not in category_names and message.text != "Назад":  # Check if not category and not back
        # Iterate over all categories and products to find the selected product
        selected_product = None
        selected_category = None
        for category in category_names:
            products = get_products_by_category(category)
            for product in products:
                if product['name'] == message.text:
                    selected_product = product
                    selected_category = category
                    break
            if selected_product:
                break

        if selected_product:
            # Product is found, show time options for the key
            show_time_options(message.chat.id, selected_product, selected_category)
        elif message.text != "Назад":
            bot.send_message(message.chat.id, "Товар не найден.")


# --- Обработчики callback-запросов (нажатий на кнопки) ---
@bot.callback_query_handler(func=lambda call: call.data.startswith("time:"))
def handle_time_selection(call):
    """Обработчик выбора времени действия ключа."""
    chat_id = call.message.chat.id
    login = authenticated_users.get(chat_id)

    if not login:
        bot.answer_callback_query(call.id, "Вы не авторизованы. Используйте /start")
        return

    product_id, duration = call.data[5:].split(":") # Извлекаем product_id и duration
    #Показываем клаву с количеством
    show_quantity_selection(chat_id, product_id, duration)

@bot.callback_query_handler(func=lambda call: call.data.startswith("qty_change:"))
def handle_quantity_change(call):
    """Обработчик изменения количества товара."""
    chat_id = call.message.chat.id
    login = authenticated_users.get(chat_id)

    if not login:
        bot.answer_callback_query(call.id, "Вы не авторизованы. Используйте /start")
        return

    qty_change, product_id, duration = call.data[11:].split(":") # Извлекаем изменение кол-ва, id товара и длительность
    qty_change = int(qty_change)

    #Обновляем количество
    user_quantities[chat_id][product_id]["quantity"] += qty_change

    #Ограничиваем количество
    max_quantity = user_quantities[chat_id][product_id]["max_quantity"]
    if user_quantities[chat_id][product_id]["quantity"] < 1:
        user_quantities[chat_id][product_id]["quantity"] = 1
    if user_quantities[chat_id][product_id]["quantity"] > max_quantity:
        user_quantities[chat_id][product_id]["quantity"] = max_quantity

    #Обновляем клавиатуру и сообщение
    show_quantity_selection(chat_id, product_id, duration)

@bot.callback_query_handler(func=lambda call: call.data.startswith("qty_min:"))
def handle_quantity_min(call):
    """Обработчик нажатия кнопки 'Мин.'"""
    chat_id = call.message.chat.id
    login = authenticated_users.get(chat_id)

    if not login:
        bot.answer_callback_query(call.id, "Вы не авторизованы. Используйте /start")
        return

    product_id, duration = call.data[8:].split(":")
    user_quantities[chat_id][product_id]["quantity"] = 1 # Устанавливаем минимальное значение
    show_quantity_selection(chat_id, product_id, duration) # Обновляем клавиатуру и сообщение

@bot.callback_query_handler(func=lambda call: call.data.startswith("qty_max:"))
def handle_quantity_max(call):
    """Обработчик нажатия кнопки 'Макс.'"""
    chat_id = call.message.chat.id
    login = authenticated_users.get(chat_id)

    if not login:
        bot.answer_callback_query(call.id, "Вы не авторизованы. Используйте /start")
        return

    product_id, duration = call.data[8:].split(":")
    max_quantity = user_quantities[chat_id][product_id]["max_quantity"] # Получаем максимальное количество
    user_quantities[chat_id][product_id]["quantity"] = max_quantity # Устанавливаем максимальное значение
    show_quantity_selection(chat_id, product_id, duration) # Обновляем клавиатуру и сообщение

@bot.callback_query_handler(func=lambda call: call.data == "back")
def handle_back(call):
    """Обработчик нажатия кнопки 'Назад'"""
    chat_id = call.message.chat.id
    login = authenticated_users.get(chat_id)

    if not login:
        bot.answer_callback_query(call.id, "Вы не авторизованы. Используйте /start")
        return
    # Показываем клаву выбора времени
    product_id = call.data[8:].split(":")[0]
    product = find_product_by_id(product_id)
    category = ""  # Подразумевается что вы знаете категорию для возврата, тут нужно доработать
    show_time_options(chat_id,product,category)

@bot.callback_query_handler(func=lambda call: call.data.startswith("confirm_pre:"))
def handle_confirm_pre(call):
   """Обработчик перед подтверждением покупки."""
   chat_id = call.message.chat.id
   login = authenticated_users.get(chat_id)

   if not login:
       bot.answer_callback_query(call.id, "Вы не авторизованы. Используйте /start")
       return

   product_id, duration = call.data[12:].split(":")
   quantity = user_quantities[chat_id][product_id]["quantity"]

   product = find_product_by_id(product_id)
   if not product:
       bot.answer_callback_query(call.id, "Ошибка: Товар не найден.")
       return

   price = product['prices'][duration]
   total_price = quantity * price

   # Создание inline клавиатуры для подтверждения
   markup = telebot.types.InlineKeyboardMarkup()
   markup.add(
       telebot.types.InlineKeyboardButton("Подтвердить", callback_data=f"confirm:{product_id}:{duration}"),
       telebot.types.InlineKeyboardButton("Отменить", callback_data="cancel")
   )

   bot.send_message(
       chat_id,
       f"Вы уверены, что хотите приобрести {quantity} ключ(ей) {product['name']} ({duration}) за {total_price:.0f} руб.?\n"
       f"На Вашем балансе {user_balances[login]:.0f} руб.",
       reply_markup=markup
   )

@bot.callback_query_handler(func=lambda call: call.data == "cancel")
def handle_cancel(call):
    """Обработчик отмены покупки."""
    bot.answer_callback_query(call.id, "Покупка отменена.")
    bot.send_message(call.message.chat.id, "Покупка отменена.")

@bot.callback_query_handler(func=lambda call: call.data.startswith("confirm:"))
def handle_confirm(call):
   """Обработчик подтверждения покупки."""
   chat_id = call.message.chat.id
   login = authenticated_users.get(chat_id)

   if not login:
       bot.answer_callback_query(call.id, "Вы не авторизованы. Используйте /start")
       return

   _, product_id, duration = call.data.split(":")
   quantity = user_quantities[chat_id][product_id]["quantity"]

   product = find_product_by_id(product_id)
   if not product:
       bot.answer_callback_query(call.id, "Ошибка: Товар не найден.")
       return

   price = product['prices'][duration]
   total_price = quantity * price

   if login not in user_balances:
       bot.answer_callback_query(call.id, "Ошибка: Вы не зарегистрированы. Используйте /start")
       return

   if user_balances[login] >= total_price:
       user_balances[login] -= total_price
       bot.answer_callback_query(call.id, "Покупка успешна!")
       bot.send_message(chat_id, f"Вы приобрели {quantity} ключ(ей) {product['name']} ({duration}) за {total_price:.0f} руб. Ваш текущий баланс: {user_balances[login]} руб.")

       # Выдача ключей
       key_list = keys.get(product_id, {}).get(duration, [])
       if key_list:
           for _ in range(quantity):
               if key_list:
                   # Выбираем случайный индекс из списка ключей
                   index = random.randint(0, len(key_list) - 1)
                   key = key_list.pop(index) # Извлекаем ключ по индексу и удаляем его из списка
                   bot.send_message(chat_id, f"Ваш ключ: {key}")
               else:
                   bot.send_message(chat_id, f"К сожалению, больше нет ключей для {product['name']} ({duration}).")
                   break # Если ключи закончились, прекращаем выдачу
       else:
           bot.send_message(chat_id, f"К сожалению, для данного товара ({product['name']}) и периода ({duration}) ключей не найдено.")

       # Очищаем информацию о количестве
       if chat_id in user_quantities and product_id in user_quantities[chat_id]:
           del user_quantities[chat_id][product_id]

   else:
       bot.answer_callback_query(call.id, "Недостаточно средств на балансе.")


# --- Запуск бота ---
if __name__ == '__main__':
   print("Бот запущен...")
   user_quantities = {}  # Инициализируем словарь user_quantities
   bot.infinity_polling()
