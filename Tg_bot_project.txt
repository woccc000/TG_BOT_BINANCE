from aiogram import Bot, types
from aiogram.dispatcher import Dispatcher
from aiogram.types import ParseMode
from aiogram.types import ReplyKeyboardMarkup, KeyboardButton
import requests

bot_token = '6085121520:AAHNNf3NAd2GXRVqbl9iEwQV_Rn7s7QdU0g'

currency_buttons = [KeyboardButton('USD'),
                    KeyboardButton('EUR'),
                    KeyboardButton('RUB')]
currency_markup = ReplyKeyboardMarkup(keyboard=[currency_buttons],
                                      resize_keyboard=True)

bot = Bot(token=bot_token, parse_mode=ParseMode.HTML)
dp = Dispatcher(bot)

async def get_rates(message: types.Message):
    response_usd = requests.get('https://api.binance.com/api/v3/ticker/price', params={'symbol': 'BTCUSDT'})
    usd_rate = float(response_usd.json()['price'])
    response_eur = requests.get('https://api.binance.com/api/v3/ticker/price', params={'symbol': 'BTCEUR'})
    eur_rate = float(response_eur.json()['price'])
    response_rub = requests.get('https://api.binance.com/api/v3/ticker/price', params={'symbol': 'BTCRUB'})
    rub_rate = float(response_rub.json()['price'])
    message_text = (
        "<b>Курсы 3-х валют:</b>\n\n"
        f"Доллар: {usd_rate:.2f}\n"
        f"Евро: {eur_rate:.2f}\n"
        f"Рубль: {rub_rate:.2f}\n\n"
        f"Выберите валюту:"
    )
    await message.reply(message_text, reply_markup=currency_markup)

@dp.message_handler(commands=['rates'])
async def get_rates(message: types.Message):
    response_usd = requests.get('https://api.binance.com/api/v3/ticker/price', params={'symbol': 'BTCUSDT'})
    usd_rate = float(response_usd.json()['price'])
    response_eur = requests.get('https://api.binance.com/api/v3/ticker/price', params={'symbol': 'BTCEUR'})
    eur_rate = float(response_eur.json()['price'])
    response_rub = requests.get('https://api.binance.com/api/v3/ticker/price', params={'symbol': 'BTCRUB'})
    rub_rate = float(response_rub.json()['price'])

    message_text = f"<b>Курсы 3-х валют:</b>\n\n" \
                   f"Доллар: {usd_rate:.2f}\n" \
                   f"Евро: {eur_rate:.2f}\n" \
                   f"Рубль: {rub_rate:.2f}"

    await message.reply(message_text)


if __name__ == '__main__':
    from aiogram import executor

    executor.start_polling(dp, skip_updates=True)
