# Museu de Percurso Raphael Arcuri

No fim de semana, foi inaugurado oficialmente o [Museu de Percurso
Raphael
Arcuri](https://www.instagram.com/museuraphaelarcuri?igsh=MWRjNWV1cnZnczE5aQ==)
, projeto desenvolvido por [Letícia
Rabelo](https://www.instagram.com/leticiarabelo.arq?igsh=dndsYTdsemM4ZWdw)
que consiste em um passeio guiado, para explorar a pé as fachadas de
edificações consideradas como patrimônio histórico da cidade, de acordo
com o roteiro baseado em algumas obras do arquiteto ítalo-brasileiro,
reconhecido por projetar diversas construções em Juiz de Fora, no início
do século XX.

## Mapa interativo

Inspirados na série de artigos escrita por **Carlos Jimenez Uribe**,
intitulada *An intelligent decision support system for tourism in
Python*, reproduzimos o trajeto em um mapa interativo, para fins
didáticos, por meio de bibliotecas do Python

### Importamos as bibliotecas

``` python
import pandas as pd
import folium
from folium.plugins import Fullscreen
from typing import Tuple, List, Dict

pd.set_option('display.precision', 15)
```

### Definimos o data frame

Criamos o *data frame* contendo as coordenadas geográficas das obras de
*Raphael Arcuri*.

``` python

df_obras = pd.DataFrame(
    [['Paço Municipal', -21.76147732383513, -43.349845686905425],
    ['ED. CIAMPI', -21.760658258306233, -43.349631277710635],
    ['Galeria Pio X', -21.76024609457489, -43.348784136608764],
    ['Cine Theatro Central', -21.761301064584814, -43.347923147523424],
    ['Palacete Pinho', -21.760579286799263, -43.34682958935376],
    ['Cia. Dias Cardoso', -21.760207713387064, -43.34475343168256],
    ['Hotel Príncipe', -21.7599733, -43.3440394],
    ['Associação Comercial', -21.759722222222223, -43.344166666666666],
    ['Cia. Pantaleone Arcuri', -21.762723242493465, -43.342729824323776],
    ['Vila Iracema', -21.763148754507743, -43.34462311819184],
    ['Palacete dos Fellet', -21.76334208845321, -43.34573104693755],
    ['Residência Raphael Arcuri', -21.76393346168443, -43.34205031818927],
    ['Castelinho dos Bracher', -21.763894356739176, -43.341967760518045],
    ['Casa D´Itália', -21.764444444444443, -43.34861111111111]],
    columns=pd.Index(['obra', 'latitude', 'longitude'], name='JF')
)
```

### Inspecionamos o *data frame*

``` python
df_obras
```

```{=html}
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
      <th>JF</th>
      <th>obra</th>
      <th>latitude</th>
      <th>longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Paço Municipal</td>
      <td>-21.761477323835130</td>
      <td>-43.349845686905425</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ED. CIAMPI</td>
      <td>-21.760658258306233</td>
      <td>-43.349631277710635</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Galeria Pio X</td>
      <td>-21.760246094574889</td>
      <td>-43.348784136608764</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Cine Theatro Central</td>
      <td>-21.761301064584814</td>
      <td>-43.347923147523424</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Palacete Pinho</td>
      <td>-21.760579286799263</td>
      <td>-43.346829589353760</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Cia. Dias Cardoso</td>
      <td>-21.760207713387064</td>
      <td>-43.344753431682562</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Hotel Príncipe</td>
      <td>-21.759973299999999</td>
      <td>-43.344039400000000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Associação Comercial</td>
      <td>-21.759722222222223</td>
      <td>-43.344166666666666</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Cia. Pantaleone Arcuri</td>
      <td>-21.762723242493465</td>
      <td>-43.342729824323776</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Vila Iracema</td>
      <td>-21.763148754507743</td>
      <td>-43.344623118191841</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Palacete dos Fellet</td>
      <td>-21.763342088453211</td>
      <td>-43.345731046937551</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Residência Raphael Arcuri</td>
      <td>-21.763933461684431</td>
      <td>-43.342050318189273</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Castelinho dos Bracher</td>
      <td>-21.763894356739176</td>
      <td>-43.341967760518045</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Casa D´Itália</td>
      <td>-21.764444444444443</td>
      <td>-43.348611111111111</td>
    </tr>
  </tbody>
</table>
</div>
```

### Centralizamos o mapa

``` python
avg_location = df_obras[['latitude', 'longitude']].mean()
coords_list = avg_location.tolist()

map_jf = folium.Map(location=coords_list, zoom_start=13)
```

### Definimos os marcadores

Utilizamos marcadores padronizados para o mapa básico.

``` python
for obra in df_obras.itertuples():
    marker = folium.Marker(location=(obra.latitude, obra.longitude),
                           tooltip=obra.obra)
    marker.add_to(map_jf)

map_jf
```
![](mpra_1.png)


### Definimos o roteiro

Inicialmente, definimos o roteiro com catorze paradas, embora o programa
oficial contemple apenas doze. Por outro lado, repetimos o circuito
aberto, em comum acordo com o material de divulgação, ao contrário do
circuito fechado, que sempre retorna ao ponto de partida. Os índices
determinan a sequência do circuito.

``` python
df_route = df_obras.copy()
df_route.index.name = 'sequência'

df_route
```

::: {.output .execute_result execution_count="45"}
```{=html}
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
      <th>JF</th>
      <th>obra</th>
      <th>latitude</th>
      <th>longitude</th>
    </tr>
    <tr>
      <th>sequência</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Paço Municipal</td>
      <td>-21.761477323835130</td>
      <td>-43.349845686905425</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ED. CIAMPI</td>
      <td>-21.760658258306233</td>
      <td>-43.349631277710635</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Galeria Pio X</td>
      <td>-21.760246094574889</td>
      <td>-43.348784136608764</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Cine Theatro Central</td>
      <td>-21.761301064584814</td>
      <td>-43.347923147523424</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Palacete Pinho</td>
      <td>-21.760579286799263</td>
      <td>-43.346829589353760</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Cia. Dias Cardoso</td>
      <td>-21.760207713387064</td>
      <td>-43.344753431682562</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Hotel Príncipe</td>
      <td>-21.759973299999999</td>
      <td>-43.344039400000000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Associação Comercial</td>
      <td>-21.759722222222223</td>
      <td>-43.344166666666666</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Cia. Pantaleone Arcuri</td>
      <td>-21.762723242493465</td>
      <td>-43.342729824323776</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Vila Iracema</td>
      <td>-21.763148754507743</td>
      <td>-43.344623118191841</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Palacete dos Fellet</td>
      <td>-21.763342088453211</td>
      <td>-43.345731046937551</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Residência Raphael Arcuri</td>
      <td>-21.763933461684431</td>
      <td>-43.342050318189273</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Castelinho dos Bracher</td>
      <td>-21.763894356739176</td>
      <td>-43.341967760518045</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Casa D´Itália</td>
      <td>-21.764444444444443</td>
      <td>-43.348611111111111</td>
    </tr>
  </tbody>
</table>
</div>
```

### Definimos os etapas do passeio

``` python
df_route_segments = df_route.join(
    df_route.shift(-1),  # map each stop to its next stop
    rsuffix='_next'
).dropna()  # last stop has no "next one", so drop it

df_route_segments
```

::: {.output .execute_result execution_count="36"}
```{=html}
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
      <th>JF</th>
      <th>obra</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>obra_next</th>
      <th>latitude_next</th>
      <th>longitude_next</th>
    </tr>
    <tr>
      <th>Sequência</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Paço Municipal</td>
      <td>-21.761477323835130</td>
      <td>-43.349845686905425</td>
      <td>ED. CIAMPI</td>
      <td>-21.760658258306233</td>
      <td>-43.349631277710635</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ED. CIAMPI</td>
      <td>-21.760658258306233</td>
      <td>-43.349631277710635</td>
      <td>Galeria Pio X</td>
      <td>-21.760246094574889</td>
      <td>-43.348784136608764</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Galeria Pio X</td>
      <td>-21.760246094574889</td>
      <td>-43.348784136608764</td>
      <td>Cine Theatro Central</td>
      <td>-21.761301064584814</td>
      <td>-43.347923147523424</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Cine Theatro Central</td>
      <td>-21.761301064584814</td>
      <td>-43.347923147523424</td>
      <td>Palacete Pinho</td>
      <td>-21.760579286799263</td>
      <td>-43.346829589353760</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Palacete Pinho</td>
      <td>-21.760579286799263</td>
      <td>-43.346829589353760</td>
      <td>Cia. Dias Cardoso</td>
      <td>-21.760207713387064</td>
      <td>-43.344753431682562</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Cia. Dias Cardoso</td>
      <td>-21.760207713387064</td>
      <td>-43.344753431682562</td>
      <td>Hotel Príncipe</td>
      <td>-21.759973299999999</td>
      <td>-43.344039400000000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Hotel Príncipe</td>
      <td>-21.759973299999999</td>
      <td>-43.344039400000000</td>
      <td>Associação Comercial</td>
      <td>-21.759722222222223</td>
      <td>-43.344166666666666</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Associação Comercial</td>
      <td>-21.759722222222223</td>
      <td>-43.344166666666666</td>
      <td>Cia. Pantaleone Arcuri</td>
      <td>-21.762723242493465</td>
      <td>-43.342729824323776</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Cia. Pantaleone Arcuri</td>
      <td>-21.762723242493465</td>
      <td>-43.342729824323776</td>
      <td>Vila Iracema</td>
      <td>-21.763148754507743</td>
      <td>-43.344623118191841</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Vila Iracema</td>
      <td>-21.763148754507743</td>
      <td>-43.344623118191841</td>
      <td>Palacete dos Fellet</td>
      <td>-21.763342088453211</td>
      <td>-43.345731046937551</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Palacete dos Fellet</td>
      <td>-21.763342088453211</td>
      <td>-43.345731046937551</td>
      <td>Residência Raphael Arcuri</td>
      <td>-21.763933461684431</td>
      <td>-43.342050318189273</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Residência Raphael Arcuri</td>
      <td>-21.763933461684431</td>
      <td>-43.342050318189273</td>
      <td>Castelinho dos Bracher</td>
      <td>-21.763894356739176</td>
      <td>-43.341967760518045</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Castelinho dos Bracher</td>
      <td>-21.763894356739176</td>
      <td>-43.341967760518045</td>
      <td>Casa D´Itália</td>
      <td>-21.764444444444443</td>
      <td>-43.348611111111111</td>
    </tr>
  </tbody>
</table>
</div>
```

### Criamos o mapa interativo com marcadors personalizados

``` python
# Converta avg_location para uma tupla se for uma série do pandas
if hasattr(avg_location, 'iloc'):
    avg_location = (avg_location.iloc[0], avg_location.iloc[1])

map_jf = folium.Map(location=avg_location, zoom_start=13)

# Obter o número total de pontos na rota
total_points = len(df_route)

for i, stop in enumerate(df_route_segments.itertuples()):
    # Definir a cor do ícone baseado na posição na rota
    if i == 0:  # Primeiro ponto - azul
        icon_color = "blue"
    elif i == total_points - 1:  # Penúltimo ponto (o último segmento)
        icon_color = "red"
    else:  # Pontos intermediários - verde
        icon_color = "green"
    
    # Definir o marcador da parada atual
    marker = folium.Marker(location=(stop.latitude, stop.longitude),
                           tooltip=stop.obra,
                           icon=folium.Icon(color=icon_color, icon="monument", prefix="fa"))
    
    # Representar cada etapa do trajeto por um segmento de reta
    line = folium.PolyLine(
        locations=[(stop.latitude, stop.longitude), 
                   (stop.latitude_next, stop.longitude_next)],
        tooltip=f"De {stop.obra} até {stop.obra_next}",
    )
    # Adiconar elementos ao mapa
    marker.add_to(map_jf)
    line.add_to(map_jf)

# Definir o marcador da última parada - usando cor vermelha
folium.Marker(location=(stop.latitude_next, stop.longitude_next),
              tooltip=stop.obra_next,
              icon=folium.Icon(color="red", icon="monument", prefix="fa")).add_to(map_jf)

map_jf
```
![](mpra_2.png)


### Definimos a função para cálculo da distância
:::

::: {#6ed79f1a-e1aa-438c-a601-259a1e2f4e3b .cell .code execution_count="38"}
``` python
from geopy.distance import geodesic

_Location = Tuple[float, float]


def ellipsoidal_distance(point1: _Location, point2: _Location) -> float:
    """Calculate ellipsoidal distance (in meters) between point1 and 
    point2 where each point is represented as a tuple (lat, lon)"""
    return geodesic(point1, point2).meters
```
:::

::: {#706aa5b7-b2d0-46ff-b870-a0ae099cd36a .cell .markdown}
### Calculamos a matriz de distância
:::

::: {#75a741b9-1b23-401e-bc87-8448781efeb6 .cell .markdown}
Calculamos a distância de cada etapa do circuito.
:::

::: {#cffd4dfb-e46c-4841-934f-b0ea5a695b14 .cell .code execution_count="52"}
``` python
df_route_segments['distance_seg'] = df_route_segments.apply(
    lambda stop: ellipsoidal_distance(
        (stop.latitude, stop.longitude), 
        (stop.latitude_next, stop.longitude_next)), 
    axis=1
)

print(df_route_segments)
```

::: {.output .stream .stdout}
    JF                              obra            latitude           longitude  \
    Sequência                                                                      
    0                     Paço Municipal -21.761477323835130 -43.349845686905425   
    1                         ED. CIAMPI -21.760658258306233 -43.349631277710635   
    2                      Galeria Pio X -21.760246094574889 -43.348784136608764   
    3               Cine Theatro Central -21.761301064584814 -43.347923147523424   
    4                     Palacete Pinho -21.760579286799263 -43.346829589353760   
    5                  Cia. Dias Cardoso -21.760207713387064 -43.344753431682562   
    6                     Hotel Príncipe -21.759973299999999 -43.344039400000000   
    7               Associação Comercial -21.759722222222223 -43.344166666666666   
    8             Cia. Pantaleone Arcuri -21.762723242493465 -43.342729824323776   
    9                       Vila Iracema -21.763148754507743 -43.344623118191841   
    10               Palacete dos Fellet -21.763342088453211 -43.345731046937551   
    11         Residência Raphael Arcuri -21.763933461684431 -43.342050318189273   
    12            Castelinho dos Bracher -21.763894356739176 -43.341967760518045   

    JF                         obra_next       latitude_next      longitude_next  \
    Sequência                                                                      
    0                         ED. CIAMPI -21.760658258306233 -43.349631277710635   
    1                      Galeria Pio X -21.760246094574889 -43.348784136608764   
    2               Cine Theatro Central -21.761301064584814 -43.347923147523424   
    3                     Palacete Pinho -21.760579286799263 -43.346829589353760   
    4                  Cia. Dias Cardoso -21.760207713387064 -43.344753431682562   
    5                     Hotel Príncipe -21.759973299999999 -43.344039400000000   
    6               Associação Comercial -21.759722222222223 -43.344166666666666   
    7             Cia. Pantaleone Arcuri -21.762723242493465 -43.342729824323776   
    8                       Vila Iracema -21.763148754507743 -43.344623118191841   
    9                Palacete dos Fellet -21.763342088453211 -43.345731046937551   
    10         Residência Raphael Arcuri -21.763933461684431 -43.342050318189273   
    11            Castelinho dos Bracher -21.763894356739176 -43.341967760518045   
    12                     Casa D´Itália -21.764444444444443 -43.348611111111111   

    JF                distance_seg  
    Sequência                       
    0           93.364875537012907  
    1           98.796363455731793  
    2          146.889090368273486  
    3          138.497147441194926  
    4          218.652565544818799  
    5           78.284040089994051  
    6           30.760167821265693  
    7          364.014944247991536  
    8          201.417064728352244  
    9          116.578398952466472  
    10         386.296860376926702  
    11           9.574199224964975  
    12         689.829427309359630  
:::
:::

::: {#d1d646a2-0d91-4e39-acc6-c9b18e6b8645 .cell .markdown}
### Calculamos a distância total a ser percorrida (km)
:::

::: {#b7982770-ad14-43b8-9f6a-e3aa3d76d34b .cell .code execution_count="49"}
``` python
total_distance = df_route_segments['distance_seg'].sum()/1000
print(total_distance)
```

::: {.output .stream .stdout}
    2.572955145098353
:::
:::

::: {#ddcfe062-6df8-474b-bfd9-c3efa33d3239 .cell .markdown}
### Criamos o mapa definitivo personalizado
:::

::: {#83adcaf1-fe59-447a-a175-9f7a6c63b6e3 .cell .code execution_count="41"}
``` python
map_jf = folium.Map(location=avg_location, zoom_start=13)

# Adicionar apenas o plugin de tela cheia
Fullscreen(
    position='bottomleft',
    title='Expandir',
    title_cancel='Sair',
    force_separate_button=True
).add_to(map_jf)

# Adicionar título no canto superior esquerdo
title_html = '''
    <div style="position: fixed; 
                top: 10px; left: 50px; width: auto; height: auto; 
                background-color: white; padding: 10px; z-index:9999;
                border: 2px solid grey; border-radius: 5px;
                font-family: Arial; font-size: 14px">
        <b>Museu de Percurso</b><br>
        <b><i>Raphael Arcuri</b></i><br>
        Projeto: <i>Letícia Rabelo</i>
    </div>
'''
map_jf.get_root().html.add_child(folium.Element(title_html))

# Adicionar distância percorrida no canto inferior direito
distance_html = f'''
    <div style="position: fixed; 
                bottom: 10px; right: 10px; width: auto; height: auto; 
                background-color: white; padding: 10px; z-index:9999;
                border: 2px solid grey; border-radius: 5px;
                font-family: Arial; font-size: 14px">
        <b>Distância Percorrida</b><br>
        {total_distance:.2f} km
    </div>
'''
map_jf.get_root().html.add_child(folium.Element(distance_html))


for stop in df_route_segments.itertuples():
    initial_stop = stop.Index == 0
    # marker for current stop
    icon = folium.Icon(icon='monument',
                       color='cadetblue' if initial_stop else 'orange',
                      prefix="fa")
                       
    marker = folium.Marker(
        location=(stop.latitude, stop.longitude),
        icon=icon, 
        # Exibir o nome e o número de cada parada
        tooltip=f"<b>Nome</b>: {stop.obra} <br>" \
              + f"<b>Parada</b>: {stop.Index} <br>"
    )
    # Representar cada etapa do trajeto por um segmento de reta
    line = folium.PolyLine(
        locations=[(stop.latitude, stop.longitude), 
                   (stop.latitude_next, stop.longitude_next)],
        # display the start, end, and distance of each segment
        tooltip=f"<b>De</b>: {stop.obra} <br>" \
              + f"<b>Até</b>: {stop.obra_next} <br>" \
              + f"<b>Distância</b>: {stop.distance_seg:.0f} m",
    )
    # Adicionar elementos ao mapa
    marker.add_to(map_jf)
    line.add_to(map_jf)

# Definir a última parada do trajeto com um marcador especial
folium.Marker(
    location=(stop.latitude_next, stop.longitude_next),
    tooltip=f"<b>Nome</b>: {stop.obra_next} <br>" \
          + f"<b>Parada</b>: {stop.Index + 1} <br>", 
    icon = folium.Icon(icon='monument', color='red', prefix="fa")
).add_to(map_jf);

# Salvar mapa
map_jf.save("roteiro_mpra.html")

map_jf  # show map
```

::: {.output .execute_result execution_count="41"}
```{=html}
<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    
    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-3.7.1.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap-glyphicons.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;
    
            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_e96b6e0dc3ac0ba1767b8257851f39f3 {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            &lt;/style&gt;

            &lt;style&gt;html, body {
                width: 100%;
                height: 100%;
                margin: 0;
                padding: 0;
            }
            &lt;/style&gt;

            &lt;style&gt;#map {
                position:absolute;
                top:0;
                bottom:0;
                right:0;
                left:0;
                }
            &lt;/style&gt;

            &lt;script&gt;
                L_NO_TOUCH = false;
                L_DISABLE_3D = false;
            &lt;/script&gt;

        
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet.fullscreen@3.0.0/Control.FullScreen.min.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet.fullscreen@3.0.0/Control.FullScreen.css&quot;/&gt;
&lt;/head&gt;
&lt;body&gt;
    
    
    &lt;div style=&quot;position: fixed; 
                top: 10px; left: 50px; width: auto; height: auto; 
                background-color: white; padding: 10px; z-index:9999;
                border: 2px solid grey; border-radius: 5px;
                font-family: Arial; font-size: 14px&quot;&gt;
        &lt;b&gt;Museu de Percurso&lt;/b&gt;&lt;br&gt;
        &lt;b&gt;&lt;i&gt;Raphael Arcuri&lt;/b&gt;&lt;/i&gt;&lt;br&gt;
        Projeto: &lt;i&gt;Letícia Rabelo&lt;/i&gt;
    &lt;/div&gt;
    
    &lt;div style=&quot;position: fixed; 
                bottom: 10px; right: 10px; width: auto; height: auto; 
                background-color: white; padding: 10px; z-index:9999;
                border: 2px solid grey; border-radius: 5px;
                font-family: Arial; font-size: 14px&quot;&gt;
        &lt;b&gt;Distância Percorrida&lt;/b&gt;&lt;br&gt;
        2.57 km
    &lt;/div&gt;
    
            &lt;div class=&quot;folium-map&quot; id=&quot;map_e96b6e0dc3ac0ba1767b8257851f39f3&quot; &gt;&lt;/div&gt;
        
&lt;/body&gt;
&lt;script&gt;
    
    
            var map_e96b6e0dc3ac0ba1767b8257851f39f3 = L.map(
                &quot;map_e96b6e0dc3ac0ba1767b8257851f39f3&quot;,
                {
                    center: [-21.761832258002293, -43.345834751123064],
                    crs: L.CRS.EPSG3857,
                    ...{
  &quot;zoom&quot;: 13,
  &quot;zoomControl&quot;: true,
  &quot;preferCanvas&quot;: false,
}

                }
            );

            

        
    
            var tile_layer_e2ace2beee8a8e0186d2fba5b0709859 = L.tileLayer(
                &quot;https://tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {
  &quot;minZoom&quot;: 0,
  &quot;maxZoom&quot;: 19,
  &quot;maxNativeZoom&quot;: 19,
  &quot;noWrap&quot;: false,
  &quot;attribution&quot;: &quot;\u0026copy; \u003ca href=\&quot;https://www.openstreetmap.org/copyright\&quot;\u003eOpenStreetMap\u003c/a\u003e contributors&quot;,
  &quot;subdomains&quot;: &quot;abc&quot;,
  &quot;detectRetina&quot;: false,
  &quot;tms&quot;: false,
  &quot;opacity&quot;: 1,
}

            );
        
    
            tile_layer_e2ace2beee8a8e0186d2fba5b0709859.addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            L.control.fullscreen(
                {
  &quot;position&quot;: &quot;bottomleft&quot;,
  &quot;title&quot;: &quot;Expandir&quot;,
  &quot;titleCancel&quot;: &quot;Sair&quot;,
  &quot;forceSeparateButton&quot;: true,
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var marker_19b8fd6a51bc2bb6f18aa646852e5256 = L.marker(
                [-21.76147732383513, -43.349845686905425],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_a7ead8ce5680fb5f1d23896f1b77911a = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;cadetblue&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_19b8fd6a51bc2bb6f18aa646852e5256.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Paço Municipal &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 0 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_19b8fd6a51bc2bb6f18aa646852e5256.setIcon(icon_a7ead8ce5680fb5f1d23896f1b77911a);
            
    
            var poly_line_545ea2b5d521879ec3ee69f76cbc3e38 = L.polyline(
                [[-21.76147732383513, -43.349845686905425], [-21.760658258306233, -43.349631277710635]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_545ea2b5d521879ec3ee69f76cbc3e38.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Paço Municipal &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: ED. CIAMPI &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 93 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_3ac5ba317781081a145004a72d4a55ef = L.marker(
                [-21.760658258306233, -43.349631277710635],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_ab915f8f494c8aaa0cd47bca9e2a9822 = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_3ac5ba317781081a145004a72d4a55ef.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: ED. CIAMPI &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 1 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_3ac5ba317781081a145004a72d4a55ef.setIcon(icon_ab915f8f494c8aaa0cd47bca9e2a9822);
            
    
            var poly_line_b0109229e1627f792a34d4fadb2ee274 = L.polyline(
                [[-21.760658258306233, -43.349631277710635], [-21.76024609457489, -43.348784136608764]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_b0109229e1627f792a34d4fadb2ee274.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: ED. CIAMPI &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Galeria Pio X &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 99 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_eabd3789ac82e4c3bc8e23aeaa624aa2 = L.marker(
                [-21.76024609457489, -43.348784136608764],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_3c384211a2ec87fbf3cd204e171ee51f = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_eabd3789ac82e4c3bc8e23aeaa624aa2.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Galeria Pio X &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 2 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_eabd3789ac82e4c3bc8e23aeaa624aa2.setIcon(icon_3c384211a2ec87fbf3cd204e171ee51f);
            
    
            var poly_line_a2d0980443f51d35bcbfb04bed70b958 = L.polyline(
                [[-21.76024609457489, -43.348784136608764], [-21.761301064584814, -43.347923147523424]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_a2d0980443f51d35bcbfb04bed70b958.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Galeria Pio X &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Cine Theatro Central &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 147 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_f251f4bb43bc6332d10648de3787ffa9 = L.marker(
                [-21.761301064584814, -43.347923147523424],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_d825058af7708a877d0dc44afdd9e794 = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_f251f4bb43bc6332d10648de3787ffa9.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Cine Theatro Central &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 3 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_f251f4bb43bc6332d10648de3787ffa9.setIcon(icon_d825058af7708a877d0dc44afdd9e794);
            
    
            var poly_line_9f393bebd53621986de394191ef3fc53 = L.polyline(
                [[-21.761301064584814, -43.347923147523424], [-21.760579286799263, -43.34682958935376]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_9f393bebd53621986de394191ef3fc53.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Cine Theatro Central &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Palacete Pinho &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 138 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_482be07ffbfd1553930102b724b35375 = L.marker(
                [-21.760579286799263, -43.34682958935376],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_955e37c22729523473a6a03c18a6f407 = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_482be07ffbfd1553930102b724b35375.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Palacete Pinho &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 4 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_482be07ffbfd1553930102b724b35375.setIcon(icon_955e37c22729523473a6a03c18a6f407);
            
    
            var poly_line_d328a360c3b16b19986d2d86103d69ea = L.polyline(
                [[-21.760579286799263, -43.34682958935376], [-21.760207713387064, -43.34475343168256]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_d328a360c3b16b19986d2d86103d69ea.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Palacete Pinho &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Cia. Dias Cardoso &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 219 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_e97b29cc2a523f2891078d41d5156914 = L.marker(
                [-21.760207713387064, -43.34475343168256],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_883d101a1c1dd714bd1a187a1bf6446b = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_e97b29cc2a523f2891078d41d5156914.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Cia. Dias Cardoso &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 5 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_e97b29cc2a523f2891078d41d5156914.setIcon(icon_883d101a1c1dd714bd1a187a1bf6446b);
            
    
            var poly_line_a80e22ffceb12d3cf0f43da67a0ac11a = L.polyline(
                [[-21.760207713387064, -43.34475343168256], [-21.7599733, -43.3440394]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_a80e22ffceb12d3cf0f43da67a0ac11a.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Cia. Dias Cardoso &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Hotel Príncipe &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 78 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_fb87de6dd316766b9c36bc2b5cccca17 = L.marker(
                [-21.7599733, -43.3440394],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_8eab406b60bf8595734bcffc94cbbb9e = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_fb87de6dd316766b9c36bc2b5cccca17.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Hotel Príncipe &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 6 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_fb87de6dd316766b9c36bc2b5cccca17.setIcon(icon_8eab406b60bf8595734bcffc94cbbb9e);
            
    
            var poly_line_a0897930af6d11a33b29f607bc379230 = L.polyline(
                [[-21.7599733, -43.3440394], [-21.759722222222223, -43.344166666666666]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_a0897930af6d11a33b29f607bc379230.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Hotel Príncipe &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Associação Comercial &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 31 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_f9c9f6e7d96ef1ef3e0ec77b721eae59 = L.marker(
                [-21.759722222222223, -43.344166666666666],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_4334e8c4f60ffc36b8c1e5faaf7dba9f = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_f9c9f6e7d96ef1ef3e0ec77b721eae59.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Associação Comercial &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 7 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_f9c9f6e7d96ef1ef3e0ec77b721eae59.setIcon(icon_4334e8c4f60ffc36b8c1e5faaf7dba9f);
            
    
            var poly_line_5aebad6424778580dd52f5a3561c6501 = L.polyline(
                [[-21.759722222222223, -43.344166666666666], [-21.762723242493465, -43.342729824323776]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_5aebad6424778580dd52f5a3561c6501.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Associação Comercial &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Cia. Pantaleone Arcuri &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 364 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_2bc6b6a349f0845737a429c0d19f90b2 = L.marker(
                [-21.762723242493465, -43.342729824323776],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_e6d1acc1738b0b7779095d85ba8dd771 = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_2bc6b6a349f0845737a429c0d19f90b2.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Cia. Pantaleone Arcuri &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 8 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_2bc6b6a349f0845737a429c0d19f90b2.setIcon(icon_e6d1acc1738b0b7779095d85ba8dd771);
            
    
            var poly_line_783bee695fc42ff1cf51fcb82e5173db = L.polyline(
                [[-21.762723242493465, -43.342729824323776], [-21.763148754507743, -43.34462311819184]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_783bee695fc42ff1cf51fcb82e5173db.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Cia. Pantaleone Arcuri &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Vila Iracema &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 201 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_551654a90335f20fc374b0f8908b68e8 = L.marker(
                [-21.763148754507743, -43.34462311819184],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_634eaa46aee60d1f5be84d072857acfb = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_551654a90335f20fc374b0f8908b68e8.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Vila Iracema &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 9 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_551654a90335f20fc374b0f8908b68e8.setIcon(icon_634eaa46aee60d1f5be84d072857acfb);
            
    
            var poly_line_795199fbb33123c52f89c8047a585967 = L.polyline(
                [[-21.763148754507743, -43.34462311819184], [-21.76334208845321, -43.34573104693755]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_795199fbb33123c52f89c8047a585967.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Vila Iracema &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Palacete dos Fellet &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 117 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_17adc63245ea6c86316395e859fe113b = L.marker(
                [-21.76334208845321, -43.34573104693755],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_5a0e11f2ff56d654b0b7535e323e0887 = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_17adc63245ea6c86316395e859fe113b.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Palacete dos Fellet &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 10 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_17adc63245ea6c86316395e859fe113b.setIcon(icon_5a0e11f2ff56d654b0b7535e323e0887);
            
    
            var poly_line_8631d4619b75a0aad3d3c9ef417c6fea = L.polyline(
                [[-21.76334208845321, -43.34573104693755], [-21.76393346168443, -43.34205031818927]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_8631d4619b75a0aad3d3c9ef417c6fea.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Palacete dos Fellet &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Residência Raphael Arcuri &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 386 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_dbdf48d02775f8dc33464b44c4d80824 = L.marker(
                [-21.76393346168443, -43.34205031818927],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_18b12cdad77c4631b179cf4d775bb0c6 = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_dbdf48d02775f8dc33464b44c4d80824.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Residência Raphael Arcuri &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 11 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_dbdf48d02775f8dc33464b44c4d80824.setIcon(icon_18b12cdad77c4631b179cf4d775bb0c6);
            
    
            var poly_line_404bd842fe0a6cba62b08f108fb00579 = L.polyline(
                [[-21.76393346168443, -43.34205031818927], [-21.763894356739176, -43.341967760518045]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_404bd842fe0a6cba62b08f108fb00579.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Residência Raphael Arcuri &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Castelinho dos Bracher &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 10 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_8ae387c7d4c890303a25098b531cd0db = L.marker(
                [-21.763894356739176, -43.341967760518045],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_71dc5133d6069debe7ebf9423a8dad25 = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;orange&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_8ae387c7d4c890303a25098b531cd0db.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Castelinho dos Bracher &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 12 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_8ae387c7d4c890303a25098b531cd0db.setIcon(icon_71dc5133d6069debe7ebf9423a8dad25);
            
    
            var poly_line_0fc301a61ce7e635ab0bcf845f2bcc03 = L.polyline(
                [[-21.763894356739176, -43.341967760518045], [-21.764444444444443, -43.34861111111111]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            poly_line_0fc301a61ce7e635ab0bcf845f2bcc03.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;De&lt;/b&gt;: Castelinho dos Bracher &lt;br&gt;&lt;b&gt;Até&lt;/b&gt;: Casa D´Itália &lt;br&gt;&lt;b&gt;Distância&lt;/b&gt;: 690 m
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
            var marker_80add67c90e85601c8547d6f9634e7d5 = L.marker(
                [-21.764444444444443, -43.34861111111111],
                {
}
            ).addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
            var icon_b26b7a1cd721e5e9c3a41769c599ec7a = L.AwesomeMarkers.icon(
                {
  &quot;markerColor&quot;: &quot;red&quot;,
  &quot;iconColor&quot;: &quot;white&quot;,
  &quot;icon&quot;: &quot;monument&quot;,
  &quot;prefix&quot;: &quot;fa&quot;,
  &quot;extraClasses&quot;: &quot;fa-rotate-0&quot;,
}
            );
        
    
            marker_80add67c90e85601c8547d6f9634e7d5.bindTooltip(
                `&lt;div&gt;
                     &lt;b&gt;Nome&lt;/b&gt;: Casa D´Itália &lt;br&gt;&lt;b&gt;Parada&lt;/b&gt;: 13 &lt;br&gt;
                 &lt;/div&gt;`,
                {
  &quot;sticky&quot;: true,
}
            );
        
    
                marker_80add67c90e85601c8547d6f9634e7d5.setIcon(icon_b26b7a1cd721e5e9c3a41769c599ec7a);
            
    
            tile_layer_e2ace2beee8a8e0186d2fba5b0709859.addTo(map_e96b6e0dc3ac0ba1767b8257851f39f3);
        
    
                marker_19b8fd6a51bc2bb6f18aa646852e5256.setIcon(icon_a7ead8ce5680fb5f1d23896f1b77911a);
            
    
                marker_3ac5ba317781081a145004a72d4a55ef.setIcon(icon_ab915f8f494c8aaa0cd47bca9e2a9822);
            
    
                marker_eabd3789ac82e4c3bc8e23aeaa624aa2.setIcon(icon_3c384211a2ec87fbf3cd204e171ee51f);
            
    
                marker_f251f4bb43bc6332d10648de3787ffa9.setIcon(icon_d825058af7708a877d0dc44afdd9e794);
            
    
                marker_482be07ffbfd1553930102b724b35375.setIcon(icon_955e37c22729523473a6a03c18a6f407);
            
    
                marker_e97b29cc2a523f2891078d41d5156914.setIcon(icon_883d101a1c1dd714bd1a187a1bf6446b);
            
    
                marker_fb87de6dd316766b9c36bc2b5cccca17.setIcon(icon_8eab406b60bf8595734bcffc94cbbb9e);
            
    
                marker_f9c9f6e7d96ef1ef3e0ec77b721eae59.setIcon(icon_4334e8c4f60ffc36b8c1e5faaf7dba9f);
            
    
                marker_2bc6b6a349f0845737a429c0d19f90b2.setIcon(icon_e6d1acc1738b0b7779095d85ba8dd771);
            
    
                marker_551654a90335f20fc374b0f8908b68e8.setIcon(icon_634eaa46aee60d1f5be84d072857acfb);
            
    
                marker_17adc63245ea6c86316395e859fe113b.setIcon(icon_5a0e11f2ff56d654b0b7535e323e0887);
            
    
                marker_dbdf48d02775f8dc33464b44c4d80824.setIcon(icon_18b12cdad77c4631b179cf4d775bb0c6);
            
    
                marker_8ae387c7d4c890303a25098b531cd0db.setIcon(icon_71dc5133d6069debe7ebf9423a8dad25);
            
    
                marker_80add67c90e85601c8547d6f9634e7d5.setIcon(icon_b26b7a1cd721e5e9c3a41769c599ec7a);
            
&lt;/script&gt;
&lt;/html&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>
```

**Referências**

Uribe, C. J. (2024). *An intelligent decision support system for tourism
in Python*. Publicado em Jan 16, 2024. Disponível em:
<https://medium.com/@carlosjuribe/list/an-intelligent-decision-support-system-for-tourism-in-python-b6ba165b4236>

``` python
```

