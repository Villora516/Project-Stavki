
#Импортируем необходимые библиотеки
import requests
from bs4 import BeautifulSoup
from fuzzy_sequence_matcher import SequenceMatcher
from collections import defaultdict

#Получаем html-код исходной страницы
def get_html(url):
    r = requests.get(url)
    return r.text

#Получаем игроков и их коэффициенты в первом букмекере
def get_all_event_marathonbet(html):
    all_players = []
    all_K = []
    soup = BeautifulSoup(html, 'lxml')
    all_event = soup.find('div',
    class_ = "sport-category-content").find_all('div',
                                class_='bg coupon-row')
    for players in all_event:
        players = players['data-event-name'].\
            replace('- ', '.').split('.')
        player_1 = players[1].strip()
        player_2 = players[3].strip()
        all_players.append(player_1)
        all_players.append(player_2)

    for g in all_event:
        K1 = g.find('td',colspan="1").find('span',
        class_="selection-link active-selection").text
        K2 = g.find('td', colspan="1").find('span',
        class_="selection-link active-selection").\
            findNext('span').text
        all_K.append(K1)
        all_K.append(K2)

    return all_players, all_K

#Получаем игроков и их коэффициенты во втором букмекере
def get_all_event_plusminus(arr_href):
    main_url = 'http://plusminus.by/bet.php'
    all_players = []
    all_K = []
    for href in arr_href:
        url = main_url+href
        html = get_html(url)
        soup = BeautifulSoup(html, 'lxml')
        tbodys = soup.find('table',class_ = 'line_table').find_all('tbody',onclick="ccolor(event,this);")
        for tbody in tbodys:
            couple = tbody.find('td',width="300").text.split(' ')
            if '(srl)' not in couple:
                K1 = tbody.find('td', width="300").findNext('td').text
                K2 = tbody.find('td', width="300").findNext('td').findNext('td').text

                all_players.append(couple[0])
                all_players.append(couple[couple.index('-') + 1])
                all_K.append(K1)
                all_K.append(K2)

    return all_players, all_K

#Из списка игроков формируем пары
def create_arr_couple(arr_players):
    arr_couple = []
    for i in range(0, len(arr_players), 2):
        arr_couple.append(arr_players[i] + ' V '
                          + arr_players[i + 1])
    return arr_couple

#Формируем словарь вида: {'Team Spirit V Cloud9': ['1.24', '3.92']}. (возвращает число от 0 до 1, которое показывает на сколько одна строка похоже на другую)
def create_dict(arr_couple, arr_key):
    cat = defaultdict(list)
    scet = 0
    try:
        for i in range(len(arr_couple)):
            cat[arr_couple[i]].append(arr_key[scet])
            cat[arr_couple[i]].append(arr_key[scet + 1])
            scet += 2
        return dict(cat)
    except IndexError:
        print('ERROR!')

#Находим общие события между двумя конторами
def similar(a, b):
    return SequenceMatcher(None, a, b).ratio()

#Считаем сумму 1/K1 +1/K2
def find_vilka(K1, K2):
    return 1/float(K1)+1/float(K2)

#Считаем, сколько составит чистый выигрыш
def profit(K, summa_max,summa_min):
    print("Выигрыш составит: "+
          str((float(K)*summa_max)-summa_min-summa_max))
    
#Рассчитываем, сколько и на какой коэффициент нужно ставить
def raschet_vilki(K1,K2,summa_max = 100):

    if K1<K2:
        summa_min = (float(K1)*summa_max)/float(K2)
        print('На коэффициент {}'.format(K1)+
              ' ставим {} '.format(summa_max))
        print('На коэффициент {}'.format(K2) + 
              ' ставим {} '.format(summa_min))

        profit(K1, summa_max, summa_min)

    else:
        summa_min = (float(K2) * summa_max) / float(K1)
        print('На коэффициент {}'.format(K1) + 
              ' ставим {} '.format(summa_min))
        print('На коэффициент {}'.format(K2) + 
              ' ставим {} '.format(summa_max))

        profit(K2, summa_max, summa_min)


