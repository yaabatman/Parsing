'''Сбор данных всех категорий, всех страниц и всех карточек сайта'''


import json
import requests
from bs4 import BeautifulSoup


def get_soup_from_url(url):
    response = requests.get(url, headers=headers)
    response.encoding = 'utf-8'
    soup = BeautifulSoup(response.text, 'lxml')
    return soup


def get_mapping_item(item):
    return item.split(':')[1].strip()


start_url = 'https://parsinger.ru/html/index1_page_1.html'
headers = {"user-agent": "Mozilla/5.0",
           }

start_soup = get_soup_from_url(start_url)
categories = tuple(x['id'] for x in start_soup.select('.nav_menu a div'))

res_json = []

for ind, category in enumerate(categories, 1):
    url_category = f'https://parsinger.ru/html/index{ind}_page_1.html'
    category_soup = get_soup_from_url(url_category)
    last_pagen = int(category_soup.select_one('.pagen a:last-child').text)

    for i in range(1, last_pagen + 1):
        page_soup = get_soup_from_url(f'https://parsinger.ru/html/index{ind}_page_{i}.html')
        links = tuple(item.select_one('a')['href'] for item in page_soup.select('div.img_box'))

        for link in links:
            link_for_item = f'https://parsinger.ru/html/{link}'
            soup_item = get_soup_from_url(link_for_item)
            name = soup_item.select_one('#p_header').text
            article = get_mapping_item(soup_item.select_one('.article').text)
            description_keys = tuple(map(lambda x: x['id'], soup_item.select('#description li')))
            description_values = tuple(map(lambda x: get_mapping_item(x.text), soup_item.select('#description li')))
            description_res = dict(zip(description_keys, description_values))
            count = get_mapping_item(soup_item.select_one('#in_stock').text)
            price = soup_item.select_one('#price').text
            old_price = soup_item.select_one('#old_price').text

            res_json.append({
                'categories': category,
                'name': name,
                'article': article,
                'description': description_res,
                'count': count,
                'price': price,
                'old_price': old_price,
                'link': link_for_item
            })
        print(f'Идёт сбор информации: категория {category}, страница {i}')

with open('res.json', 'w', encoding='utf-8') as f:
    json.dump(res_json, f, indent=4, ensure_ascii=False)

print('файл "res.json" создан')
