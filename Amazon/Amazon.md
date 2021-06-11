## **Coletando Dados da Amazon** - Um Estudo de Caso

  A Amazon é uma das empresas que saiu, literalmente, de uma garagem, como uma empresa de venda de livros pela internet e se tornou um dos maiores empreendimentos capitalistas da história. Liderada por Jeff Bezos, hoje a empresa acumula uma capitalização de mercado de mais de U$1 trilhão de dólares. [[1]](https://ycharts.com/companies/AMZN/market_cap) 

  Neste estudo de caso iremos abordar como coletar dados da plataforma da Amazon diretamente por meio de **web scraping**, fazendo uso dos componentes **requests** [[2]](https://requests.readthedocs.io/pt_BR/latest/user/quickstart.html) e **bs4** - Beautiful Soup [[3]](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) Python 3. 



## **1. Entendendo o Problema**
* **Qual é o nosso caso?**

Neste nosso caso, necessitamos consumir os dados apenas da **primeira pagina**, de acordo com uma **palavra-chave**, que no nosso caso é 'IPHONE' e corresponde à variável **keyword**, no codigo.

* **O problema das requests**

Caso tentemos realizar uma request para o site da Amazon sem o devido *header*, ou cabeçalho, o site nos retorna uma página sem nenhum item de pesquisa. Isso se deve a algum mecanismo interno do próprio site/API que nos impede, afortunadamente, de acessar os dados. Para solucionar isto, fingimos ter um User-Agent diferente daquele o qual o componente **requests** utiliza para capturar os dados de requisições.

## **2. Exploração dos Dados**
Os dados que receberemos serão todos do tipo **string**. Estes dados vem do tipo <class 'bs4.element.Tag'>, que corresponde ao tipo retornado pelo plugin **Beautiful Soup** ao se realizar uma busca com o metodo **find**. Conseguimos extrair a string contida dentro desse tipo ao utilizarmos o identificador **.text**. Todos esses valores, ao serem capturados pelo script serão passados para **duas listas**: uma contendo o título de cada **produto** (produtos) e outra contendo o **preco** (precos) de cada produto. Ao final do processamento destes dados, teremos uma lista que coalesce todos esses valores (lista_conteudo) em uma única lista, para então escrevermos os dados em um arquivo .csv.

Portanto, teremos os dados:
* **produto** - str
* **preco** - str
* **produtos** - List
* **precos** - List
* **lista_conteudo** - List


```python
!pip install bs4
```

    Collecting bs4
      Downloading bs4-0.0.1.tar.gz (1.1 kB)
    Collecting beautifulsoup4
      Downloading beautifulsoup4-4.9.3-py3-none-any.whl (115 kB)
    Collecting soupsieve>1.2
      Downloading soupsieve-2.2.1-py3-none-any.whl (33 kB)
    Building wheels for collected packages: bs4
      Building wheel for bs4 (setup.py): started
      Building wheel for bs4 (setup.py): finished with status 'done'
      Created wheel for bs4: filename=bs4-0.0.1-py3-none-any.whl size=1273 sha256=23bc3badf87fc6166fcba019518a05fe6b624cabc546c6c31d902cbff591a9a4
      Stored in directory: c:\users\viere\appdata\local\pip\cache\wheels\73\2b\cb\099980278a0c9a3e57ff1a89875ec07bfa0b6fcbebb9a8cad3
    Successfully built bs4
    Installing collected packages: soupsieve, beautifulsoup4, bs4
    Successfully installed beautifulsoup4-4.9.3 bs4-0.0.1 soupsieve-2.2.1
    


```python
# Importamos os componentes necessarios
import requests
import csv as commaseparated
import pandas as pd
from bs4 import BeautifulSoup

# Aquisicao dos Dados
# Definicao do Cabecalho
headers = {'User-Agent':'Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:72.0) Gecko/20100101 Firefox/72.0'}
```

Neste caso utilizei o **User-Agent** do meu próprio computador para realizar as requisições. Após definirmos as variáveis de aquisição dos dados, podemos prosseguir e realizar a requisição dos dados de acordo com os parâmetros definidos acima.


```python
amazon_request = requests.get('https://www.amazon.com.br/s?k=iphone', headers=headers)
```

## **3. Preparo dos Dados**

Receberemos a página inteira ao realizar a requisição, portanto precisamos instanciar a classe BeautifulSoup, que irá transformar o arquivo HTML em uma **árvore complexa** de objetos Python. Após isso, filtramos a árvore para que ela contenha apenas os **resultados de nossa pesquisa**, fazemos isso utilizando o metodo **find**, para que então contenha apenas o span de atributo **data-component-type** igual a **'s-search-results'**, ou seja, o elemento do DOM que contem apenas os resultados de pesquisa.

Então, finalmente, normalizamos os resultados encontrados pelo método **find** para uma **string** para então instanciar outra classe BeautifulSoup, que irá conter apenas os elementos desejados - nossos **resultados**.

Após isso, declaramos os identificadores dos tipos lista da parte 2.



```python
# Normalizacao dos Dados
soup = BeautifulSoup(amazon_request.text, "lxml")
```

## **4. Modelo e Lógica de Processamento de Dados**

Apos prepararmos os dados, necessitamos de um modelo para filtrarmos apenas os dados da nossa pesquisa os quais desejamos.

## anexa_produtos()
Utilizamos do metodo **find_all** para filtrar todos os spans contendo o nome dos produtos (ie. os que contem o atributo **'a-size-base-plus'**) em uma lista chamada **lista_produtos**

Para a função **anexa_produtos()**, apenas iteramos sobre o conteúdo da tag **lista_produtos** e os anexamos a lista **produtos**.

## anexa_precos()
Já para a função **anexa_precos()** a lógica de processamento, devido às circunstâncias, necessita ser mais robusta. Dada a quantidade díspar de valores de precos, implementamos um metodo que busca dentro das divs que contem o atributo **'sg-col-inner'**, correspondente a classe que possui cada produto, em uma lista de nome **lista_div**. A razão da escolha do último filtro sera apresentada a seguir.

Ocorrem então três questões:

* **Como lidar com produtos que possuem múltiplos preços?**

Por exemplo, há produtos que possuem um preço com os descontos incluídos e o preço normal. A nossa opção foi considerar apenas o preço com os descontos incluídos, obviamente.

* **Como lidar com produtos que não possuem precos?**

Para estes produtos, normalizamos o preço como "R$ 0,00", para que não houvesse valores nulos dentro da nossa planilha.

* **Como implementar a lógica para filtrar os produtos que possuem apenas um preço daqueles que possuem multiplos preços ou não possuem um preço?**

A lógica implementada para lidar com essas duas questões segue no diagrama abaixo.

![Fluxo de Controle da funçã anexa_precos()](https://i.imgur.com/PC0u59u.png)

## anexa_conteudos()

A função anexa_conteudos() apenas itera sobre os valores de cada lista após estes terem sido processados e coalesce os valores em uma lista (lista_conteudo) com todos os pares **[produto, preco]** encontrados. Isto foi necessário para dar mais legibilidade ao código e facilitar a interpretação da função que armazena os dados no arquivo .csv.

Segue abaixo o código.

## Filtros
O filtro **filter_preco_offscreen** corresponde ao filtro dos produtos que possuem preço, independentemente de haver desconto ou não. Estes estão unidos sob o mesmo span, 'a-off-screen'.

O filtro **filter_preco_secondary** corresponde ao filtro da classe pai 'a-row a-size-base a-color-secondary' dos produtos que possuem o preço sob o texto 'Mais opções de compra'.

O filtro **filter_preco_a_base** corresponde ao filtro do span dentro das classes definidas pelo filter_preco_secondary, ou seja, é o conteúdo do preço dos produtos que são precificados sob o texto 'Mais opções de compra'.




```python
# Inicio da Logica de Processamento de Dados
lista_produtos = soup.body.find_all('span', attrs={'class':'a-size-base-plus'})
lista_div = soup.body.find_all('div', attrs={'class':'sg-col-inner'})
filter_preco = {'class': 'a-price-whole'}
produtos = []
precos = []
lista_conteudo = []

def anexa_produtos():
    for produto in lista_produtos:
        produtos.append(produto.text)

def anexa_precos():
    for i in range(len(lista_div)-1):
        if "R$" in lista_div[i].text:
                precos.append('R$ ' + lista_div[i].find('span', attrs='a-price').find('span', attrs='a-price-whole').text)
        else:
            precos.append("R$ 0,00")

def anexa_conteudos():
    for produto, preco in zip(produtos, precos):
        lista_conteudo.append([produto, preco])
        
anexa_produtos()
anexa_precos()
anexa_conteudos()
# Fim da Logica de Processamento de Dados
```

## **5. Armazenamento dos Dados**

Para o armazenamento dos dados, escolhemos o tipo **CSV (Comma-separated Values)** por se tratar de um tipo de arquivo universal, que, ao ser aberto em qualquer editor de texto - incluindo-se nisto o **MS Excel** - tem seus dados exibidos de forma padronizada.


```python
# Armazenamento dos dados em um arquivo .csv
def salva_em_csv():
    with open('produtos_amazon.csv', 'w', newline='', encoding='utf-8') as csvfile:
        spamwriter = commaseparated.writer(csvfile, delimiter= ',')
        spamwriter.writerow(["Produto", "Preco"])
        for produto, preco in lista_conteudo:
            spamwriter.writerow([produto, preco])
            
salva_em_csv()
df = pd.read_csv('produtos_amazon.csv')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Produto</th>
      <th>Preco</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Iphone 11 Apple Preto, 128gb Desbloqueado - Mh...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple iPhone 8 Plus, 64GB, Gold</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iphone 11 Apple Preto, 64gb Desbloqueado - Mhd...</td>
      <td>R$ 4.940,</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Iphone 11 Apple Branco, 64gb Desbloqueado - Mh...</td>
      <td>R$ 4.940,</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Iphone 12 Pro Apple Azul-pacífico, 128gb Desbl...</td>
      <td>R$ 3.898,</td>
    </tr>
    <tr>
      <th>5</th>
      <td>iPhone SE Branco, com Tela de 4,7, 4G, 64 GB e...</td>
      <td>R$ 4.499,</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Novo Apple iPhone 12 (128 GB, Azul)</td>
      <td>R$ 4.533,</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Iphone Xr Apple Branco, 128gb Desbloqueado - M...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Novo Apple iPhone 12 Pro Max (128 GB, Azul Pac...</td>
      <td>R$ 2.899,</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Novo Apple iPhone 12 Pro Max (128 GB, Dourado)</td>
      <td>R$ 7.299,</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Iphone Xr Apple (product) Vermelho, 64gb Desbl...</td>
      <td>R$ 4.149,</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Celular Apple iPhone 11 64gb / Tela 6.1'' / 12...</td>
      <td>R$ 8.299,</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Novo Apple iPhone 12 Pro Max (128 GB, Grafite)</td>
      <td>R$ 8.400,</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Novo Apple iPhone 12 (128 GB, Branco)</td>
      <td>R$ 3.814,</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Xiaomi Redmi Note 9 128GB 4GB RAM - Versión Gl...</td>
      <td>R$ 4.599,</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Cabo para iPhone USB-C to Lightning Apple Orig...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>16</th>
      <td>iPhone 11 Branco, com Tela de 6,1, 4G, 128 GB ...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Iphone Xr Apple Branco, 128gb Desbloqueado - M...</td>
      <td>R$ 1.210,</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Capa Capinha Protetora Para Iphone 12 e 12 Pro...</td>
      <td>R$ 94,</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Novo Apple iPhone 12 Pro Max (256 GB, Grafite)</td>
      <td>R$ 4.973,</td>
    </tr>
    <tr>
      <th>20</th>
      <td>CAPA CASE CAPINHA SILICONE AVELUDADO IPHONE 11...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Iphone Xr Apple Preto, 64gb Desbloqueado - Mry...</td>
      <td>R$ 54,</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Apple Iphone 12 (128GB Preto) - Desbloqueado A...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Novo Apple iPhone 12 Pro Max (256 GB, Azul Pac...</td>
      <td>R$ 25,</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Carregador Original iPhone 12 Turbo Usb-c20w</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>25</th>
      <td>iPhone 11 64GB Preto iOS 4G Câmera 12MP - Apple</td>
      <td>R$ 6.499,</td>
    </tr>
    <tr>
      <th>26</th>
      <td>iPhone SE 64GB Black Novo Desbloqueado Tela 4,...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Novo Apple iPhone 12 Pro (256 GB, Grafite)</td>
      <td>R$ 75,</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Iphone 11 128Gb Preto iOS 4G Câmera 12Mp - Apple</td>
      <td>R$ 4.799,</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Novo Apple iPhone 12 Pro (256 GB, Azul Marinho)</td>
      <td>R$ 2.999,</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Smartphone Apple Iphone 7 Plus 128GB 4G iOS 10...</td>
      <td>R$ 8.350,</td>
    </tr>
    <tr>
      <th>31</th>
      <td>iPhone X 256GB Cinza Espacial</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Iphone 11 Pro Max Apple Dourado, 256gb Desbloq...</td>
      <td>R$ 8.899,</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Carregador de Iphone Original 20w Usb-c SE XR ...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Spigen Capa Ultra Hybrid Projectada para Apple...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>35</th>
      <td>iPhone 8 Plus Apple 64GB Cinza Espacial Tela R...</td>
      <td>R$ 7.999,</td>
    </tr>
    <tr>
      <th>36</th>
      <td>iPhone 8 256GB Apple Tela 4.7 iOS 11 Câmera 12MP</td>
      <td>R$ 160,</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Capa Protetora, Shield Preta com Pelicula, Iph...</td>
      <td>R$ 86,</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pelicula Vidro Temperado Hprime Apple iPhone 1...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Iphone 11 Apple Vermelho, 64gb Desbloqueado - ...</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>40</th>
      <td>iPhone 7 128GB Dourado</td>
      <td>R$ 99,</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Celular Xiaomi Poco X3 6GB/128GB NFC - Shadow ...</td>
      <td>R$ 39,</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Fone de Ouvido Apple EarPods Lightning</td>
      <td>R$ 4.533,</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Iphone 8 Plus 64gb Original Apple - De Vitrine...</td>
      <td>R$ 2.394,</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Apple MacBook Air 13.3", Chip M1, 8GB RAM, 256...</td>
      <td>R$ 1.770,</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Smartphone, Apple, iPhone 7 MN952BR/A, 128 GB,...</td>
      <td>R$ 178,</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Novo Apple iPhone 12 Pro (128 GB, Grafite)</td>
      <td>R$ 0,00</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Smartphone, Apple, iPhone 7 PLUS MN4M2BZ/A, 12...</td>
      <td>R$ 7.960,</td>
    </tr>
  </tbody>
</table>
</div>



## **6. Fragilidades Associadas ao Modelo**

* **O modelo não reconhece páginas de pesquisa que contem apenas itens ordenados em lista vertical.**

Um exemplo disso pode ser observado ao tentar substituir a palavra-chave por algum item mais generico (ie. "Bucha".) Por isso, recomenda-se utilizar palavras-chave mais especificas, como, por exemplo, "Thinkpad". 

* **O modelo depende do limiar associado a quantidade de requests permitidas pela Amazon num dado intervalo de tempo.**

Devido a isto, torna-se dificil implementar a paginação ou voltamos ao problema das **requests**, discutido na parte 1 - a chance de se receber códigos de resposta 403 ou 503 é grande se fizermos muitas requests em pouco tempo. Seria interessante, entao, utilizar de proxies/SOCKS e espacar as requests em alguns segundos.


## **7. Referências**
**[1]** - Valor da Capitalização de Mercado da Amazon - https://ycharts.com/companies/AMZN/market_cap

**[2]** - Documentação da Biblioteca Requests - https://requests.readthedocs.io/pt_BR/latest/user/quickstart.html

**[3]** - Documentação da Biblioteca bs4 - https://www.crummy.com/software/BeautifulSoup/bs4/doc/


```python

```
