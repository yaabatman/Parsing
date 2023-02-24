'''Сбор данных всех категорий, всех страниц и всех карточек сайта'''


import csv
import requests
from bs4 import BeautifulSoup


def get_soup_for_url(url):
    response = requests.get(url, headers=headers)
    response.encoding = 'utf-8'
    soup = BeautifulSoup(response.text, 'lxml')
    return soup


def split_and_strip(obj):
    return obj.text.split(':')[1].strip()


start_url = 'https://parsinger.ru/html/index1_page_1.html'
headers = {"user-agent": "Mozilla/5.0",
           }

rows = (
    'Наименование', 'Артикул', 'Бренд', 'Модель',
    'Наличие', 'Цена', 'Старая цена', 'Ссылка на карточку с товаром',
)

start_soup = get_soup_for_url(start_url)
count_divs = len(start_soup.select('.nav_menu a'))

with open('res.csv', 'a', newline='', encoding='utf-8') as f:
    writer = csv.writer(f, delimiter=';')

    writer.writerow(rows)

    for i in range(1, count_divs + 1):
        soup_div = get_soup_for_url(f'https://parsinger.ru/html/index{i}_page_1.html')
        number_last_page = int(soup_div.select_one('div.pagen a:last-child').text)

        for j in range(1, number_last_page + 1):
            soup_page = get_soup_for_url(f'https://parsinger.ru/html/index{i}_page_{j}.html')
            items = tuple(item.select_one('a')['href'] for item in soup_page.select('div.img_box'))

            for item in items:
                link_for_item = f'https://parsinger.ru/html/{item}'
                soup_item = get_soup_for_url(link_for_item)
                name = soup_item.select_one('#p_header').text
                article = split_and_strip(soup_item.select_one('.article'))
                brand = split_and_strip(soup_item.select_one('#brand'))
                model = split_and_strip(soup_item.select_one('#model'))
                in_stock = split_and_strip(soup_item.select_one('#in_stock'))
                price = soup_item.select_one('#price').text.strip()
                old_price = soup_item.select_one('#old_price').text.strip()

                writer.writerow([name, article,
                                 brand, model, in_stock,
                                 price, old_price,
                                 link_for_item,
                                 ])
