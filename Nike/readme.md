## **Webscraping com Python**
Python é uma linguagem de programação de propósitos gerais, bastante versátil e eficaz. Para a tecnologia Webscraping, o Python disponibiliza diversos módulos sendo um deles o **Selenium** que iremos utilizar para coletar informações do site da Amazon. Neste caso, iremos coletar os nomes dos produtos e seus respectivos valores, para então salvá-los em uma planilha, utilizando outro módulo eficaz do Python, o **CSV** - CSV File Reading and Writing. Por final, utilizamos os módulos Panda e xlsxwriter para converter o CSV em uma planilha.



# **Selenium**
O Selenium fornece uma API simples para automatizar tarefas relacionadas aos browsers (ou navegadores). De modo eficaz, pela API do Selenium é possível acessar as funcionalidades do Selenium Webdriver de forma intuitiva. Essa API é utilizada pra acessar Webdrivers do Selenium como Firefox, Chrome, IE, Remote, etc. Além disso, possui suporte para as versões Python 2.7, 3.5 e em diante.  Como o Selenium necessita de um driver para interagir com o browser na finalidade de executar algumas ações, é necessário baixar os drivers associados ao navegador utilizado.


## **Primeiro passo**
*  Para que possamos ter acesso ao site, é necessário primeiro utilizarmos um browser para abrir a página que queremos mapear e adquirir o código HTML para identificar os elementos que serão coletados. Para isso, é necessário identificar o driver específico do navegador que será utilizado, neste caso, o **ChromeDriver** pois utilizaremos o Chrome. Por final, instalamos também o módulo Selenium. Após instalado o ChromeDriver e passado o path para o sistema, podemos prosseguir.




```
# Instala os módulos

# Selenium
# Instala o módulo Selenium
!pip install selenium 
!pip install panda
!pip install xlsxwriter
# Atualiza o sistema para instalar os pacotes da forma correta
!apt-get update 
# Instala o driver do navegador Chrome
!apt install chromium-chromedriver 
!cp /usr/lib/chromium-browser/chromedriver /usr/bin
import sys
sys.path.insert(0,'/usr/lib/chromium-browser/chromedriver') # Inserindo o path para o driver
```

    Collecting selenium
    [?25l  Downloading https://files.pythonhosted.org/packages/80/d6/4294f0b4bce4de0abf13e17190289f9d0613b0a44e5dd6a7f5ca98459853/selenium-3.141.0-py2.py3-none-any.whl (904kB)
    [K     |████████████████████████████████| 911kB 6.9MB/s 
    [?25hRequirement already satisfied: urllib3 in /usr/local/lib/python3.6/dist-packages (from selenium) (1.24.3)
    Installing collected packages: selenium
    Successfully installed selenium-3.141.0
    Collecting panda
      Downloading https://files.pythonhosted.org/packages/79/03/74996420528fe488ce17c42b6400531c8067d7eb661c304fa3aa8fdad17c/panda-0.3.1.tar.gz
    Requirement already satisfied: setuptools in /usr/local/lib/python3.6/dist-packages (from panda) (53.0.0)
    Requirement already satisfied: requests in /usr/local/lib/python3.6/dist-packages (from panda) (2.23.0)
    Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /usr/local/lib/python3.6/dist-packages (from requests->panda) (1.24.3)
    Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.6/dist-packages (from requests->panda) (2020.12.5)
    Requirement already satisfied: chardet<4,>=3.0.2 in /usr/local/lib/python3.6/dist-packages (from requests->panda) (3.0.4)
    Requirement already satisfied: idna<3,>=2.5 in /usr/local/lib/python3.6/dist-packages (from requests->panda) (2.10)
    Building wheels for collected packages: panda
      Building wheel for panda (setup.py) ... [?25l[?25hdone
      Created wheel for panda: filename=panda-0.3.1-cp36-none-any.whl size=7259 sha256=c102fc7a631b3fa36ba896ca4403c96ad52a3240b2993d88edb3075d192884a1
      Stored in directory: /root/.cache/pip/wheels/c6/c8/45/06ed898b0bb401c1ff207dbb05b1587ff28860a236d98b1996
    Successfully built panda
    Installing collected packages: panda
    Successfully installed panda-0.3.1
    Collecting xlsxwriter
    [?25l  Downloading https://files.pythonhosted.org/packages/6b/41/bf1aae04932d1eaffee1fc5f8b38ca47bbbf07d765129539bc4bcce1ce0c/XlsxWriter-1.3.7-py2.py3-none-any.whl (144kB)
    [K     |████████████████████████████████| 153kB 6.3MB/s 
    [?25hInstalling collected packages: xlsxwriter
    Successfully installed xlsxwriter-1.3.7
    Get:1 https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/ InRelease [3,626 B]
    Ign:2 https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64  InRelease
    Ign:3 https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64  InRelease
    Get:4 https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64  Release [697 B]
    Get:5 http://ppa.launchpad.net/c2d4u.team/c2d4u4.0+/ubuntu bionic InRelease [15.9 kB]
    Hit:6 https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64  Release
    Get:7 https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64  Release.gpg [836 B]
    Hit:8 http://archive.ubuntu.com/ubuntu bionic InRelease
    Get:9 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
    Get:10 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
    Get:11 https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/ Packages [44.8 kB]
    Hit:12 http://ppa.launchpad.net/cran/libgit2/ubuntu bionic InRelease
    Hit:14 http://ppa.launchpad.net/graphics-drivers/ppa/ubuntu bionic InRelease
    Get:15 http://archive.ubuntu.com/ubuntu bionic-backports InRelease [74.6 kB]
    Ign:16 https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64  Packages
    Get:16 https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64  Packages [552 kB]
    Get:17 http://ppa.launchpad.net/c2d4u.team/c2d4u4.0+/ubuntu bionic/main Sources [1,725 kB]
    Get:18 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 Packages [2,352 kB]
    Get:19 http://ppa.launchpad.net/c2d4u.team/c2d4u4.0+/ubuntu bionic/main amd64 Packages [883 kB]
    Get:20 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 Packages [2,158 kB]
    Fetched 7,987 kB in 3s (2,670 kB/s)
    Reading package lists... Done
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following additional packages will be installed:
      chromium-browser chromium-browser-l10n chromium-codecs-ffmpeg-extra
    Suggested packages:
      webaccounts-chromium-extension unity-chromium-extension adobe-flashplugin
    The following NEW packages will be installed:
      chromium-browser chromium-browser-l10n chromium-chromedriver
      chromium-codecs-ffmpeg-extra
    0 upgraded, 4 newly installed, 0 to remove and 18 not upgraded.
    Need to get 81.0 MB of archives.
    After this operation, 273 MB of additional disk space will be used.
    Get:1 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 chromium-codecs-ffmpeg-extra amd64 87.0.4280.66-0ubuntu0.18.04.1 [1,122 kB]
    Get:2 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 chromium-browser amd64 87.0.4280.66-0ubuntu0.18.04.1 [71.7 MB]
    Get:3 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 chromium-browser-l10n all 87.0.4280.66-0ubuntu0.18.04.1 [3,716 kB]
    Get:4 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 chromium-chromedriver amd64 87.0.4280.66-0ubuntu0.18.04.1 [4,488 kB]
    Fetched 81.0 MB in 4s (19.5 MB/s)
    Selecting previously unselected package chromium-codecs-ffmpeg-extra.
    (Reading database ... 146442 files and directories currently installed.)
    Preparing to unpack .../chromium-codecs-ffmpeg-extra_87.0.4280.66-0ubuntu0.18.04.1_amd64.deb ...
    Unpacking chromium-codecs-ffmpeg-extra (87.0.4280.66-0ubuntu0.18.04.1) ...
    Selecting previously unselected package chromium-browser.
    Preparing to unpack .../chromium-browser_87.0.4280.66-0ubuntu0.18.04.1_amd64.deb ...
    Unpacking chromium-browser (87.0.4280.66-0ubuntu0.18.04.1) ...
    Selecting previously unselected package chromium-browser-l10n.
    Preparing to unpack .../chromium-browser-l10n_87.0.4280.66-0ubuntu0.18.04.1_all.deb ...
    Unpacking chromium-browser-l10n (87.0.4280.66-0ubuntu0.18.04.1) ...
    Selecting previously unselected package chromium-chromedriver.
    Preparing to unpack .../chromium-chromedriver_87.0.4280.66-0ubuntu0.18.04.1_amd64.deb ...
    Unpacking chromium-chromedriver (87.0.4280.66-0ubuntu0.18.04.1) ...
    Setting up chromium-codecs-ffmpeg-extra (87.0.4280.66-0ubuntu0.18.04.1) ...
    Setting up chromium-browser (87.0.4280.66-0ubuntu0.18.04.1) ...
    update-alternatives: using /usr/bin/chromium-browser to provide /usr/bin/x-www-browser (x-www-browser) in auto mode
    update-alternatives: using /usr/bin/chromium-browser to provide /usr/bin/gnome-www-browser (gnome-www-browser) in auto mode
    Setting up chromium-chromedriver (87.0.4280.66-0ubuntu0.18.04.1) ...
    Setting up chromium-browser-l10n (87.0.4280.66-0ubuntu0.18.04.1) ...
    Processing triggers for hicolor-icon-theme (0.17-2) ...
    Processing triggers for mime-support (3.60ubuntu1) ...
    Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
    cp: '/usr/lib/chromium-browser/chromedriver' and '/usr/bin/chromedriver' are the same file
    

## **Partindo para o código**



```
# Importa os módulos
from selenium import webdriver
from google.colab  import files
import csv
import pandas as pd
import xlsxwriter
import os
import re
import time
```

O código acima importa os módulos instalados anteriormente para utilizarmos no processo de coleta de dados.

## **Definindo o driver e abrindo o navegador**
Definimos o navegador que será utilizado com o módulo Webdriver e então armazenamos este valor na variável "driver". Após isso, usamos a função get para abrir o browser e com o link do site como parâmetro.


```
# Define o navegador utilizado - Chrome
chrome_options = webdriver.ChromeOptions() 
chrome_options.add_argument('--headless')
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--disable-dev-shm-usage')
driver = webdriver.Chrome('chromedriver',options=chrome_options)
# Abre o navegador Chrome e navega até a página
driver.get("https://www.nike.com.br/Snkrs")
```

## **Extraindo a lista de nomes e preços com o XPath**
Para que possamos filtrar as informações que serão coletadas, usaremos algumas funções do Selenium para identificar o elemento pelo XPath. O XPath (XML Path Language) passa um caminho de identificação do elemento no código HTML. Com isso, é possível obter somente os dados que queremos de forma mais eficaz do que identificar os elementos por meio de classe, nome ou até mesmo por ID.


```
# Extrai as listas de nome se preços com XPath
tenis = driver.find_elements_by_xpath('//h3[@class="button button--border produto__detalhe-botao"]')
array_links = []
link_tenis = ''
data_lancamento = []

for i in range(len(tenis)):
  if i == 0:
    pass
  else:
    link_tenis = str(tenis[i].get_attribute('onclick'))
    link_tenis = re.sub('\w+[.]\w+[.]\w+', '', link_tenis, 1)
    link_tenis = re.sub('[="]+', '', link_tenis)
    print(link_tenis)
    array_links.append(link_tenis)


!touch saidas.csv

with open('./saidas.csv', 'w+', newline='\n') as csvfile:
    wr = csv.writer(csvfile, dialect='excel')
    for link in array_links:
        wr.writerow([link,])

print(len(array_links))
```

    https://www.nike.com.br/Snkrs/Produto/Lahar-Low-Feminino/1-16-210-305330
    https://www.nike.com.br/Snkrs/Produto/Lahar-Low-Feminino/1-16-210-305308
    https://www.nike.com.br/Snkrs/Produto/Kobe-6-Protro/153-169-211-295320
    https://www.nike.com.br/Snkrs/Produto/Air-Jordan-4-Feminino/1-16-210-296284
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1-Experimental/153-169-211-294049
    https://www.nike.com.br/Snkrs/Produto/NOCTA/153-169-211-284276
    https://www.nike.com.br/Snkrs/Produto/NOCTA/153-169-211-284266
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283440
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283425
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283408
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283392
    https://www.nike.com.br/Snkrs/Produto/Nike-x-Undercover/153-169-211-284598
    https://www.nike.com.br/Snkrs/Produto/Nike-x-Undercover/153-169-211-284584
    https://www.nike.com.br/Snkrs/Produto/Air-Flight-89/153-169-211-302967
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-302171
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-301551
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294219
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294204
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294190
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1/153-169-211-293174
    https://www.nike.com.br/Snkrs/Produto/Kyrie-7/153-169-211-291464
    https://www.nike.com.br/Snkrs/Produto/Air-Jordan-1-Zoom/153-169-211-291583
    https://www.nike.com.br/Snkrs/Landing/MYSTERY-BOX/6
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1-Experimental/153-169-211-294052
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283394
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283408
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283425
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283442
    https://www.nike.com.br/Snkrs/Produto/Nike-x-Undercover/153-169-211-284586
    https://www.nike.com.br/Snkrs/Produto/Nike-x-Undercover/153-169-211-284598
    https://www.nike.com.br/Snkrs/Produto/Air-Flight-89/153-169-211-302967
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1/153-169-211-293175
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294220
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294204
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-302171
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294190
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-301551
    https://www.nike.com.br/Snkrs/Produto/Kyrie-7/153-169-211-291466
    https://www.nike.com.br/Snkrs/Produto/Jordan-Why-Not-Zer04/153-169-211-291223
    https://www.nike.com.br/Snkrs/Produto/Air-Jordan-9/153-169-211-292125
    https://www.nike.com.br/Snkrs/Produto/Lebron-18/153-169-211-291416
    https://www.nike.com.br/Snkrs/Produto/Fontanka-Edge/1-16-210-292239
    https://www.nike.com.br/Snkrs/Produto/Dunk-Low-Infantil-26-335/67-80-445-295184
    https://www.nike.com.br/Snkrs/Produto/PG-5/153-169-211-303936
    https://www.nike.com.br/Snkrs/Produto/LeBron-18/153-169-211-280004
    https://www.nike.com.br/Snkrs/Produto/Why-Not-Zer04/153-169-211-287076
    https://www.nike.com.br/Snkrs/Produto/Air-Max-97-x-UNDEFEATED/153-169-211-304079
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1-Experimental/153-169-211-294066
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283400
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283418
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283433
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283449
    https://www.nike.com.br/Snkrs/Produto/NOCTA/153-169-211-284273
    https://www.nike.com.br/Snkrs/Produto/NOCTA/153-169-211-284284
    https://www.nike.com.br/Snkrs/Produto/Air-Jordan-4-Feminino/1-16-210-296294
    https://www.nike.com.br/Snkrs/Produto/Kobe-6-Protro/153-169-211-295337
    https://www.nike.com.br/Snkrs/Produto/Lahar-Low-Feminino/1-16-210-305323
    https://www.nike.com.br/Snkrs/Produto/Lahar-Low-Feminino/1-16-210-305345
    https://www.nike.com.br/Snkrs/Produto/Zoom-Freak-2/153-169-211-305455
    59
    


```
import csv
link_list = []
with open('./saidas.csv') as csvfile:
    reader = csv.reader(csvfile, delimiter='\n')
    for row in reader:
        link_list.append(row)

driver = webdriver.Chrome('chromedriver',options=chrome_options)
produtos = []
i = len(link_list) - 1
while i > 0:
    link = str(link_list[i][0])
    try:
        nome = ""
        data_disponibilidade = ""
        valor = ""
        driver.get(str(link_list[i][0]))
        while nome == "":
            try:
                 nome = driver.find_element_by_xpath('//div[@class="nome-preco-produto"]')
            except:
                 break
        try:
             data_disponibilidade = driver.find_element_by_xpath('//h3[@class="detalhes-produto__disponibilidade"]')
        except:
             data_disponibilidade = driver.find_element_by_xpath('//h3[@class="detalhes-produto__disponibilidade-unidades mt-5"]')
        while "R$ " not in valor:
             valor = driver.execute_script("a = $('.js-valor-por').text(); return a;")
        print(nome.text)
        print(data_disponibilidade.text)
        print(valor)
        print(link)
        produtos.append([nome.text, data_disponibilidade.text, valor, link])
        i = i - 1
    except:
      pass

print(produtos)
```

    
    
    R$ 649,99
    https://www.nike.com.br/Snkrs/Produto/Zoom-Freak-2/153-169-211-305455
    LAHAR LOW FEMININO
    BLACK
    DISPONÍVEL EM 17/02 ÀS 10:00H
    R$ 799,99
    https://www.nike.com.br/Snkrs/Produto/Lahar-Low-Feminino/1-16-210-305345
    LAHAR LOW FEMININO
    WHEAT
    DISPONÍVEL EM 17/02 ÀS 10:00H
    R$ 799,99
    https://www.nike.com.br/Snkrs/Produto/Lahar-Low-Feminino/1-16-210-305323
    KOBE 6 PROTRO
    GREEN APPLE
    DISPONÍVEL EM 13/02 ÀS 10:00H
    R$ 999,99
    https://www.nike.com.br/Snkrs/Produto/Kobe-6-Protro/153-169-211-295337
    AIR JORDAN 4 FEMININO
    STARFISH
    DISPONÍVEL EM 09/02 ÀS 10:00H
    R$ 1.099,99
    https://www.nike.com.br/Snkrs/Produto/Air-Jordan-4-Feminino/1-16-210-296294
    NOCTA
    CAMISETA
    
    R$ 229,99
    https://www.nike.com.br/Snkrs/Produto/NOCTA/153-169-211-284284
    NOCTA
    CAMISETA
    
    R$ 229,99
    https://www.nike.com.br/Snkrs/Produto/NOCTA/153-169-211-284273
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283449
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283433
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283418
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283400
    AIR FORCE 1 EXPERIMENTAL
    RACER PINK
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 649,99
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1-Experimental/153-169-211-294066
    AIR MAX 97 X UNDEFEATED
    WHITE
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 999,99
    https://www.nike.com.br/Snkrs/Produto/Air-Max-97-x-UNDEFEATED/153-169-211-304079
    WHY NOT ZER0.4
    UPBRINGING
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 749,99
    https://www.nike.com.br/Snkrs/Produto/Why-Not-Zer04/153-169-211-287076
    LEBRON 18
    THE CHOSEN 2
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 1.199,99
    https://www.nike.com.br/Snkrs/Produto/LeBron-18/153-169-211-280004
    PG 5
    BLACK/WHITE/BARELY GREEN
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 649,99
    https://www.nike.com.br/Snkrs/Produto/PG-5/153-169-211-303936
    DUNK LOW INFANTIL (26-33,5)
    BLACK
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 349,99
    https://www.nike.com.br/Snkrs/Produto/Dunk-Low-Infantil-26-335/67-80-445-295184
    FONTANKA EDGE
    LIGHT ARCTIC PINK
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 749,99
    https://www.nike.com.br/Snkrs/Produto/Fontanka-Edge/1-16-210-292239
    LEBRON 18
    CHLORINE BLUE/BLACK
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 1.299,99
    https://www.nike.com.br/Snkrs/Produto/Lebron-18/153-169-211-291416
    AIR JORDAN 9
    UNIVERSITY GOLD
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 1.099,99
    https://www.nike.com.br/Snkrs/Produto/Air-Jordan-9/153-169-211-292125
    JORDAN WHY NOT? ZER0.4
    FAMILY
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 749,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Why-Not-Zer04/153-169-211-291223
    KYRIE 7
    RAYGUNS
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 749,99
    https://www.nike.com.br/Snkrs/Produto/Kyrie-7/153-169-211-291466
    RAYGUNS
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 149,99
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-301551
    RAYGUNS
    BLUSÃO
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 499,99
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294190
    RAYGUNS
    BONÉ
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 139,99
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-302171
    RAYGUNS
    SHORTS
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 399,99
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294204
    RAYGUNS
    REGATA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 479,99
    https://www.nike.com.br/Snkrs/Produto/Rayguns/153-169-211-294220
    AIR FORCE 1
    RAYGUNS
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 599,99
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1/153-169-211-293175
    AIR FLIGHT 89
    RAYGUNS
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 649,99
    https://www.nike.com.br/Snkrs/Produto/Air-Flight-89/153-169-211-302967
    NIKE X UNDERCOVER
    JAQUETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 3.199,99
    https://www.nike.com.br/Snkrs/Produto/Nike-x-Undercover/153-169-211-284598
    NIKE X UNDERCOVER
    JAQUETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 3.199,99
    https://www.nike.com.br/Snkrs/Produto/Nike-x-Undercover/153-169-211-284586
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283442
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283425
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283408
    JORDAN VINTAGE
    CAMISETA
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 199,99
    https://www.nike.com.br/Snkrs/Produto/Jordan-Vintage/153-169-211-283394
    AIR FORCE 1 EXPERIMENTAL
    RACER PINK
    MÁXIMO DE 1 UNIDADE POR CPF
    R$ 649,99
    https://www.nike.com.br/Snkrs/Produto/Air-Force-1-Experimental/153-169-211-294052
    

##**Organizando os dados coletados e transferindo a uma planilha**
Uma vez que os dados coletados foram salvos nas variáveis nomes e precos  podemos organizar os dados para que fiquem adaptados à planilha e então salvá-los na planilha utilizando CSV. É necessário notar que precisamos  utilizar a função "text" do selenium para converter os dados de bytes para string. Após isso, fechamos o navegador com a função "quit".





```
!cat saidas.csv
```

## **Referências**
[1] - https://selenium-python.readthedocs.io/installation

[2] - https://docs.python.org/3/library/csv.html

[3] - https://chromedriver.chromium.org/
