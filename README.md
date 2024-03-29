# Digital_Image_Processing

Esta página é dedicada à publicar os trabalhos realizados para a disciplina de Processamento Digital de Imagens, ministrada pelo professor Agostinho Brito, e realizado pelos alunos Rhendson Alexandre e Mário Marques, utilizando a biblioteca OpenCV em linguagens como Python e C++.

## 1. Manipulando Pixels de uma Imagem

### 1.1. Inverter Cores da Região Especificada

Primeiramente, pegaremos a imagem do personagem Maui de Moana para realizar a primeira manipulação, a de selecionar a área de um retângulo dentro da imagem a qual determinará a área que terá as cores invertidas.
<div align="center">
<img src="imgs/maui.jpg" alt="Imagem Original" width="600"/>
</div>
<div align="center">
<figcaption>Imagem Original</figcaption>
</div>

```python
from tempfile import tempdir
import numpy as np
import cv2 as cv

def showImage( img ):
    from matplotlib import pyplot as plt
    plt.imshow(img)
    plt.show()
    
def getColor(img, i, j):
    return img.item(i, j, 0), img.item(i, j, 1), img.item(i, j, 2)

```
O programa começa chamando as bibliotecas necessárias, criando uma função ```showImage()```, usada apenas para plotar a imagem recebida como parâmetro, e a função ```getColor()```, a qual armazenas os canais de cores da imagem separadamente.
```python
def setColor(img, i, j, b, g, r):
    img.itemset((i, j, 0), b)
    img.itemset((i, j, 1), g)
    img.itemset((i, j, 2), r)
    return 

def invertRegions( img ):
    y1 = input('y1: ')
    x1 = input('x1: ')
    y2 = input('y2: ')
    x2 = input('x2: ')

    rectangle = img[int(y1):int(y2), int(x1):int(x2)]
    altura, largura = rectangle.shape

    for i in range (0, altura):
        for j in range(0, largura):
            rectangle.itemset((i, j), 255-(rectangle.item(i, j)))

    img[int(y1):int(y2), int(x1):int(x2)] = rectangle

    cv.imwrite("imgs/maui_inverted.png", img)

    return img 

img = cv.imread("imgs/maui.jpg", 0)
showImage(invertRegions(img))
    
    
```
Em seguida, a função ```setColor()``` seleciona a área a ser modificada de acordo com os inputs do usuário usando a função ```invertRegions()```, a qual também realiza a inversão das cores ponto a ponto com o looping. Ao final de tudo, a imagem será salva em tons de cinza e com as cores invertidas, como no exemplo abaixo:

<div align="center">
<img src="imgs/maui_inverted.png" alt="Imagem com Cores Invertidas" width="600"/>
</div>
<div align="center">
<figcaption>Imagem com cores invertidas e em tons de cinza</figcaption>
</div>

### 1.2. Trocar Regiões da Imagem

Se baseando novamente na mesma imagem original, este algoritmo troca posições dividindo a imagem em 4 pedaços iguais e embaralhando-os. Para isso, as mesmas bibliotecas do exercício 1.1 serão usadas.

```python
from tempfile import tempdir
import numpy as np
import cv2 as cv

def showImage( img ):
    from matplotlib import pyplot as plt
    plt.imshow(img)
    plt.show()

def cutOffQuarter( img, quarter ):
    altura, largura = img.shape

    first_quarter = img[0:int(altura/2), 0:int(largura/2)]
    second_quarter = img[0:int(altura/2), int(largura/2):largura]
    third_quarter = img[int(altura/2):altura, 0:int(largura/2)]
    fourth_quarter = img[int(altura/2):altura, int(largura/2):largura]

    if quarter == 1 :    
        return first_quarter 
    elif quarter == 2 :    
        return second_quarter 
    elif quarter == 3 :    
        return third_quarter 
    elif quarter == 4 :    
        return fourth_quarter 
```
A primeira grande mudança em relação ao exercício anterior é a função ```cutOffQuarter()``` a qual divide a imagem original em 4 pedaços iguais usando metade da altura e de largura para realizar os cortes nomeados de 1 a 4.
```python

def trocaRegioes( img ):
    cv.imwrite("imgs/temp_shift.png", img)
    tempImg = cv.imread("imgs/temp_shift.png", 0)

    altura, largura = img.shape

    tempImg[0:int(altura/2), 0:int(largura/2)] = cutOffQuarter(img, 4)
    tempImg[0:int(altura/2), int(largura/2):largura] = cutOffQuarter(img, 3)
    tempImg[int(altura/2):altura, 0:int(largura/2)] = cutOffQuarter(img, 2)
    tempImg[int(altura/2):altura, int(largura/2):largura] = cutOffQuarter(img, 1)

    return tempImg

def main():
    img = cv.imread("imgs/maui.jpg", 0)
    cv.imwrite("imgs/maui_shifted.png", trocaRegioes(img))
    showImage(trocaRegioes(img))

main()
```
A segunda grande parte do algoritmo é focado na função ```trocaRegioes()```, que pega os parâmetros obtidos por meio da função anterior ao calcular o tamanho de pixels máximos de altura e largura, copia a imagem original em tons de cinza para um arquivo temporário de imagem ```temp_sift.png``` e usar ela de base para trocar a posição dos pedaços gerados e salvar o resultado em uma outra imagem, disponível abaixo:

<div align="center">
<img src="imgs/maui_shifted.png" alt="Imagem com Regiões Trocadas" width="600"/>
</div>
<div align="center">
<figcaption>Imagem com as regiões trocadas e em tons de cinza</figcaption>
</div>

## 2. Rotulação de Objetos usando Floodfill

<div align="center">
<img src="imgs/bolhas.png" alt="Bolhas"/>
</div>

```python
from turtle import color
import numpy as np
import cv2 as cv

img = cv.imread("imgs/bolhas.png", 0)

floodfil = img.copy()

h, w = img.shape[:2]
mask = np.zeros((h+2, w+2), np.uint8)

colorToFill = 1
for i in range (0, h):
    for j in range(0, w):
        if (img.item(i, j) == 255):
            cv.floodFill(floodfil, mask, (j,i), colorToFill)
            if (img.item(i+1, j+1) == colorToFill):
                colorToFill += 1

cv.imshow('Mask', mask)
cv.imshow('Original', img)
cv.imshow('FloodFill', floodfill)

cv.waitKey()

cv.destroyAllWindows()

```
