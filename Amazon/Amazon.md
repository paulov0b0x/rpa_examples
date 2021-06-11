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
            if lista_div[i].find('span', attrs=filter_preco_offscreen) is None:
                precos.append(lista_div[i].find('span', attrs='a-price').find('span', attrs='a-price-whole').text)
            else:
                precos.append(lista_div[i].find('span', attrs=filter_preco_offscreen).text)
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


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-7-5daa97b552c1> in <module>
         26 
         27 anexa_produtos()
    ---> 28 anexa_precos()
         29 anexa_conteudos()
         30 # Fim da Logica de Processamento de Dados
    

    <ipython-input-7-5daa97b552c1> in anexa_precos()
         14     for i in range(len(lista_div)-1):
         15         if "R$" in lista_div[i].text:
    ---> 16             if lista_div[i].find('span', attrs=filter_preco_offscreen) is None:
         17                 precos.append(lista_div[i].find('span', attrs='a-price').find('span', attrs='a-price-whole').text)
         18             else:
    

    NameError: name 'filter_preco_offscreen' is not defined


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
files.download('produtos_amazon.csv')
df = pd.read_csv('produtos_amazon.csv')
df
```

## **6. Fragilidades Associadas ao Modelo**

* **O modelo não reconhece páginas de pesquisa que contem apenas itens ordenados em lista vertical.**

Um exemplo disso pode ser observado ao tentar substituir a palavra-chave por algum item mais generico (ie. "Bucha".) Por isso, recomenda-se utilizar palavras-chave mais especificas, como, por exemplo, "Thinkpad". 

* **O modelo depende do limiar associado a quantidade de requests permitidas pela Amazon num dado intervalo de tempo.**

Devido a isto, torna-se dificil implementar a paginação ou voltamos ao problema das **requests**, discutido na parte 1 - a chance de se receber códigos de resposta 403 ou 503 é grande se fizermos muitas requests em pouco tempo. Seria interessante, entao, utilizar de proxies/SOCKS e espacar as requests em alguns segundos.


## **7. Referências**
**[1]** - Valor da Capitalização de Mercado da Amazon - https://ycharts.com/companies/AMZN/market_cap

**[2]** - Documentação da Biblioteca Requests - https://requests.readthedocs.io/pt_BR/latest/user/quickstart.html

**[3]** - Documentação da Biblioteca bs4 - https://www.crummy.com/software/BeautifulSoup/bs4/doc/
