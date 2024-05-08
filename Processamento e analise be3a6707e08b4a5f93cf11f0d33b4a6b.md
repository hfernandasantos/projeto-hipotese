# Processamento e analise

## Contexto

Num mundo onde a indústria musical é extremamente competitiva e em constante evolução, a capacidade de tomar decisões baseadas em dados tornou-se um ativo inestimável. 

Uma gravadora enfrenta o emocionante desafio de lançar um novo artista no cenário musical global. 

Felizmente, ela tem uma ferramenta poderosa em seu arsenal: um extenso conjunto de dados do Spotify com informações sobre as músicas mais ouvidas em 2023.

## Hipóteses

Músicas com BPM (Batidas Por Minuto) mais altos fazem mais sucesso em termos de número de streams no Spotify.

As músicas mais populares no ranking do Spotify também possuem um comportamento semelhante em outras plataformas, como a Deezer.

A presença de uma música em um maior número de playlists está correlacionada com um maior número de streams.

Artistas com um maior número de músicas no Spotify têm mais streams.

As características da música influenciam o sucesso em termos de número de streams no Spotify.

## Objetivo

Validar as hipóteses através da análise de dados e fornecer recomendações estratégicas com base nas descobertas e tomar decisões informadas que aumentem as chances de alcançar o “sucesso”.

## Estrutura dos dados

Neste banco de dados temos 3 tabelas: Competition, Spotify e Technical.

## Etapas do projeto

### **Primeiros passos**

1. [Coleta de dados](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
2. [Importações](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
3. [Tratamento dos dad](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)dos

### **Análise dos dados**

1. [Criar variáveis categóricas](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
2. [Unir tabelas](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
3. [Aplicar medidas de tendência central](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
4. [Aplicar medidas de dispersão](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
5. [Calcular quartis, decis ou percentis](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
6. [Calcular correlação entre variáveis](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21) 

### **Validando hipóteses**

1. [Aplicar segmentação Power Bi](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)
2. [Validar hipótese Power Bi](https://www.notion.so/Processamento-e-analise-be3a6707e08b4a5f93cf11f0d33b4a6b?pvs=21)

## Etapas do projeto

1. **Coleta de dados (Big Query)**
    
    A base de dados do Spotify foi disponibilizada pela Laboratoría.
    
    **Coleta de dados (Colab)**
    
    ```python
    df = pd.read_csv('hipotese.csv')
    ```
    

1. **Importações**
    
    Importamos 3 arquivos em formato CSV para o BIGQUERY.
    

1. **Tratamento dos dados**
    
    **Identificar e tratar valores nulos**
    
    Utilizamos os códigos abaixo  para encontrar valores nulos nas tabelas.
    
    Na tabela *track_in_competition* encontramos 50 nulos.
    
    ```sql
    SELECT COUNT (*)
    FROM `projeto-hipotese.origem.track_in_competition`
    WHERE track_id IS NULL OR in_apple_playlists IS NULL OR in_apple_charts IS NULL OR in_deezer_playlists IS NULL OR in_deezer_charts IS NULL OR in_shazam_charts IS NUL;
    ```
    
    Na tabela *track_technical_info* encontramos 95 nulos.
    
    ```sql
    SELECT COUNT(*)
    FROM `projeto-hipotese.origem.track_technical_info` AS technical
    WHERE track_id IS NULL OR bpm IS NULL OR technical.key IS NULL OR mode IS NULL OR danceability__ IS NULL OR valence__ IS NULL OR energy__ IS NULL OR acousticness__ IS NULL OR instrumentalness__ IS NULL OR liveness__ IS NULL OR speechiness__ IS NULL;
    ```
    
    Na tabela *track_in_spotify não* encontramos nulos.
    
    ```sql
    SELECT COUNT(*)
    FROM `projeto-hipotese.origem.track_in_spotify`
    WHERE track_id IS NULL OR track_name IS NULL OR artist_s__name IS NULL OR artist_count IS NULL OR released_year IS NULL OR released_month IS NULL OR released_day IS NULL OR in_spotify_playlists IS NULL OR in_spotify_charts IS NULL OR streams IS NULL;
    ```
    
    <aside>
    ❗ Deixamos os valores nulos ao reparar que o total de valores nulos correspondem cerca de 10% dos dados totais e a retirada das músicas podem influenciar na tomada de decisão.
    
    </aside>
    
    **Identificar e tratar valores duplicados**
    
    Utilizamos os códigos abaixo  para encontrar valores duplicados nas tabelas.
    
    Na tabela *track_in_competition não* encontramos valores duplicados.
    
    ```sql
    SELECT track_id, COUNT(*) 
    FROM `projeto-hipotese.origem.track_in_competition` 
    GROUP BY track_id
    HAVING COUNT(*);
    ```
    
    Na tabela *track_technical_info não* encontramos valores duplicados.
    
    ```sql
    SELECT COUNT (*)
    FROM `projeto-hipotese.origem.track_technical_info` 
    GROUP BY track_id
    HAVING COUNT(*) >1;
    ```
    
    Na tabela *track_in_spotify* encontramos 4 valores duplicados.
    
    ```sql
    SELECT  track_name,artist_s__name,released_year,
    COUNT(*)  AS Conte, max(track_id)id1, min(track_id) AS id2 
    FROM `projeto-hipotese.origem.track_in_spotify` 
    GROUP BY  track_name,artist_s__name, released_year 
    HAVING COUNT(*) >1;
    ```
    
    <aside>
    ❗ Consideramos dados duplicados para os dois primeiros artistas. Mantemos as músicas dos 2 últimos por terem dados diferentes na base e/ou não terem dados nulos em colunas relevantes.
    
    </aside>
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled.png)
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%201.png)
    
    Músicas excluídas da tabela *track_in_spotify.*
    
    **Identificar e tratar dados fora do escopo de análise**
    
    <aside>
    ❗ Mantivemos todas as variáveis para a análise. Chegamos a conclusão que excluir uma variável neste momento pode comprometer análises posteriores.
    
    </aside>
    
    **Identificar e tratar dados discrepantes em variáveis categóricas**
    
    Utilizamos o código abaixo nas para encontrar valores discrepantes na tabela *track_in_spotify*.
    
    ```sql
    SELECT track_id,REGEXP_REPLACE(track_name,r'[^A-Za-z0-9\s]','') AS track_name_clean, REGEXP_REPLACE(artist_s__name, r'[^A-Za-z0-9\s]','') AS artist_s__name_clean, artist_count, released_year, released_month, released_day, in_spotify_playlists,in_spotify_charts,streams
    FROM `projeto-hipotese.origem.track_in_spotify`;
    ```
    
    <aside>
    ❗ Foram encontramos caracteres no nome de algumas musicas e artistas, usamos a formula acima para remover os caracteres especiais.
    
    </aside>
    
    Depois de utlizar a formula encontramos 2 linhas em branco, optamos por deixar devido a somente o track_name_clean estar em branco.
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%202.png)
    
    **Verificar e alterar o tipo de dados**
    
    Utilizamos o código abaixo para encontrar tipos de dados incorretos na tabela *track_in_spotify*.
    
    ```sql
    SELECT * EXCEPT(streams), SAFE_CAST(streams AS INT64) AS streams_clean
    FROM `projeto-hipotese.origem.track_in_spotify`;
    ```
    
    <aside>
    ❗ Encontramos uma variável  STRING onde deveria ser INTEGGER. Alteramos o dado da variável para NULL.
    
    </aside>
    
    **Identificar e tratar dados discrepantes em variáveis numéricas com MAX, MIN e AVG.**
    
    Utilizamos o código abaixo  para encontrar valores mínimo, máximo e média nas tabelas.
    
    Código para a tabela *spotify*.
    
    ```sql
    SELECT 
    MAX(artist_count),
    MAX(released_year),
    MAX(released_month),
    MAX(released_day),
    MAX(in_spotify_playlists),
    MAX(in_spotify_charts),
    MAX (streams_clean) ,
    
    MIN(artist_count),
    MIN(released_year),
    MIN(released_month),
    MIN(released_day),
    MIN(in_spotify_playlists),
    MIN(in_spotify_charts),
    MIN (streams_clean) ,
    
    AVG(artist_count),
    AVG(released_year),
    AVG(released_month),
    AVG(released_day),
    AVG(in_spotify_playlists),
    AVG(in_spotify_charts),
    AVG (streams_clean),
    
    FROM `projeto-hipotese.tratados.spotify`;
    ```
    
    Código para a tabela *track_in_competition.*
    
    ```sql
    SELECT 
    MAX(in_apple_playlists),
    MAX(in_apple_charts),
    MAX(in_deezer_playlists),
    MAX(in_deezer_charts),
    MAX(in_shazam_charts) ,
    
    MIN(in_apple_playlists),
    MIN(in_apple_charts),
    MIN(in_deezer_playlists),
    MIN(in_deezer_charts),
    MIN(in_shazam_charts),
    
    AVG(in_apple_playlists),
    AVG(in_apple_charts),
    AVG(in_deezer_playlists),
    AVG(in_deezer_charts),
    AVG(in_shazam_charts) ,
    
    FROM  `projeto-hipotese.tratados.track_in_competition`;
    ```
    
    Código para a tabela *technical.*
    
    ```sql
    SELECT
    MAX(bpm),
    MAX(danceability__),
    MAX(valence__),
    MAX(energy__),
    MAX(acousticness__),
    MAX(instrumentalness__),
    MAX(liveness__),
    MAX(speechiness__),
    
    MIN(bpm),
    MIN(danceability__),
    MIN(valence__),
    MIN(energy__),
    MIN(acousticness__),
    MIN(instrumentalness__),
    MIN(liveness__),
    MIN(speechiness__),
    
    AVG(bpm),
    AVG(danceability__),
    AVG(valence__),
    AVG(energy__),
    AVG(acousticness__),
    AVG(instrumentalness__),
    AVG(liveness__),
    AVG(speechiness__),
    
    FROM `projeto-hipotese.tratados.technical`;
    ```
    
    <aside>
    ❗ Encontramos uma linha na tabela spotify que continha o ano de lançamento 1930, mas o lançamento da musica foi em 2022.
    
    </aside>
    

1. **Criar novas variáveis**
    
    No código abaixo criamos a variável ano de lançamento no formato ano/mês/dia
    
    ```sql
    SELECT track_id, DATE(CONCAT(CAST(released_year AS STRING), "-", CAST(released_month AS STRING), "-", CAST(released_day AS STRING))) AS released_date
    FROM `projeto-hipotese.tratados.spotify`;
    ```
    
    No código abaixa criamos a variável que soma as playlists de todos os streams. 
    
    ```sql
    SELECT track_id,in_apple_playlists,in_deezer_playlists, in_apple_playlists + in_deezer_playlists AS sum_playlists
    FROM `projeto-hipotese.tratados.tratatos_track_in_competition`;
    ```
    
     
    
    No código abaixo criamos uma variável para contar a quantidade total de música por artista.
    
    ```sql
    SELECT artist_s__name_clean, COUNT(*) AS counta
    FROM `projeto-hipotese.tratados.spotify` 
    GROUP BY artist_s__name_clean;
    ```
    

1. **Unir Tabelas**
    
    No código unimos as 3 tabelas e incluímos as variáveis que somam as playlists e a de data.
    
    ```sql
    SELECT spotify.track_id, track_name_clean, artist_s__name_clean, artist_count, released_year, released_month, released_day, in_spotify_playlists,
    in_spotify_charts,streams_clean,in_apple_playlists, in_apple_charts, in_deezer_playlists, in_deezer_charts, in_shazam_charts,bpm, technical.key, mode, danceability__
    valence__, energy__,acousticness__, instrumentalness__, liveness__, speechiness__,released_date,
    spotify.in_spotify_playlists + competition.in_apple_playlists + competition.in_deezer_playlists AS soma_playlist
    
    FROM `projeto-hipotese.tratados.spotify` AS spotify
    LEFT JOIN `projeto-hipotese.track_in_competition.track_in_competition` competition ON spotify. track_id = competition.track_id
    LEFT JOIN `projeto-hipotese.track_in_competition.track_technical_info`AS technical ON competition.track_id = technical.track_id
    LEFT JOIN `projeto-hipotese.tratados.realeased_date` AS released_date ON  technical.track_id = released_date.track_id;
    ```
    

> Agora no Power BI, começamos a aplicar tabelas e gráficos para a visualização dos dados.
> 

1.  **Aplicar medidas de tendência central**
    
     Aplicamos medidas como média, mediana para variável streams e soma_playlist.
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%203.png)
    
    <aside>
    ❗ A média está consideravelmente distante da mediana, é um sinal de que os dados podem conter valores extremos, ou seja, outliers.
    
    </aside>
    

**Ver distribuição**

Através do histrograma criado em python podemos ver a distrubuição das variaveis abaixo

![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%204.png)

![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%205.png)

**7.Aplicar medidas de dispersão** 

![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%206.png)

<aside>
❗ O desvio padrão nas duas variáveis aponta para uma dispersão significativa em relação à mediana, indicando uma ampla diversidade nos dados da amostra. Isso sugere que os valores individuais estão consideravelmente distantes da mediana, o que reflete uma considerável variabilidade e uma ampla gama de valores na amostra.

</aside>

**Visualizar dados ao longo do tempo**

![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%207.png)

<aside>
❗ No gráfico acima conseguimos visualizar como as plataformas de streams se comportaram ao longo do tempo e como o ano de lançamento da música afeta a visibilidade que a plataforma tem.

</aside>

**8.Calcular quartis, decis ou percentis**

Query para calcular os quartis para as variáveis dos atributos musicais e para as streams, categorizando-os posteriormente.

```sql
WITH Quartiles AS (
  SELECT 
    streams_clean,
    bpm,
   danceability__,
   valence__,
   energy__,
   acousticness__,
   instrumentalness__,
   liveness__,
   speechiness__,
    NTILE(4) OVER (ORDER BY streams_clean) AS quartile_streams_clean,
    NTILE(4) OVER (ORDER BY bpm) AS quartile_bpm,
    NTILE(4) OVER (ORDER BY danceability__) AS quartile_danceability__,
    NTILE(4) OVER (ORDER BY valence__) AS quartile_valence__,
    NTILE(4) OVER (ORDER BY energy__) AS quartile_energy__,
    NTILE(4) OVER (ORDER BY acousticness__) AS quartile_acousticness__,
    NTILE(4) OVER (ORDER BY instrumentalness__) AS quartile_instrumentalness__,
    NTILE(4) OVER (ORDER BY liveness__) AS quartile_liveness__,
    NTILE(4) OVER (ORDER BY speechiness__) AS quartile_speechiness__,
    
    ROW_NUMBER() OVER (PARTITION BY streams_clean ORDER BY streams_clean) AS rn
  FROM `projeto-hipotese.tratados.teste`
)

SELECT
  a.*,
  Quartiles.quartile_streams_clean,
   CASE 
    WHEN quartile_streams_clean<= 2 THEN 'Baixo'
    WHEN quartile_streams_clean >= 3 THEN 'Alto'
END AS categoria_streams_clean,
  
  Quartiles.quartile_bpm,
  CASE 
    WHEN quartile_bpm <= 2 THEN 'Baixo'
    WHEN quartile_bpm >= 3 THEN 'Alto'
END AS categoria_bpm,

  Quartiles.quartile_danceability__,
   CASE 
    WHEN quartile_danceability__ <= 2 THEN 'Baixo'
    WHEN quartile_danceability__>= 3 THEN 'Alto'
  
END AS categoria_danceability__,

  Quartiles.quartile_valence__,
     CASE 
    WHEN quartile_valence__ <= 2 THEN 'Baixo'
    WHEN quartile_valence__ >= 3 THEN 'Alto'
   
END AS categoria_valence__ ,

  Quartiles.quartile_energy__,
       CASE 
    WHEN quartile_energy__ <= 2 THEN 'Baixo'
    WHEN quartile_energy__ >= 3 THEN 'Alto'

END AS categoria_energy__ ,

  Quartiles.quartile_acousticness__,
       CASE 
    WHEN quartile_acousticness__<=2 THEN 'Baixo'
    WHEN quartile_acousticness__>=3 THEN 'Alto'
  
END AS categoria_acousticness__ ,

  Quartiles.quartile_instrumentalness__,
        CASE 
    WHEN quartile_instrumentalness__<= 2 THEN 'Baixo'
    WHEN quartile_instrumentalness__>= 3 THEN 'Alto'
  
END AS categoria_instrumentalness__ ,

  Quartiles.quartile_liveness__,
 CASE 
    WHEN quartile_liveness__<= 2 THEN 'Baixo'
    WHEN quartile_liveness__>= 3 THEN 'Alto'

END AS categoria_liveness__ ,

  Quartiles.quartile_speechiness__,
   CASE 
    WHEN quartile_speechiness__<= 2 THEN 'Baixo' 
    WHEN quartile_speechiness__>= 3 THEN 'Alto'
    
END AS categoria_speechiness__ ,

FROM `projeto-hipotese.tratados.teste` a
LEFT JOIN (
  SELECT * FROM Quartiles WHERE rn = 1
) Quartiles
ON a.streams_clean = Quartiles.streams_clean;
```

<aside>
❗ Criamos os quartis, para compreender a distribuição dos dados e aplicamos categorias, essas categorias serão essenciais para uma análise mais aprofundada posteriormente.

</aside>

1. **Calcular correlação entre variáveis** 
    
    No código abaixo calculamos a correlação da soma das playlists e de stream.
    
    ```sql
    SELECT CORR(streams_clean, soma_playlist) AS correlacao_playlist, CORR (streams_clean, danceability__) AS correlacao_danceability
    FROM `projeto-hipotese.tratados.tabelas_unificadas`;
    ```
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%208.png)
    
    <aside>
    ❗ Na análise da correlação entre streams e playlists, observamos uma associação, indicando uma relação significativa entre essas duas variáveis. Por outro lado, ao examinarmos a correlação com a de streams e danceability, notamos uma associação mais fraca, sugerindo uma ligação menos marcante entre as variaveis.
    
    </aside>
    

1. **Aplicar segmentação**
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%209.png)
    
    <aside>
    ❗ Aplicamos a segmentação efetuada com os quartis nas categorias musicais
    
    </aside>
    

1. **Validar hipótese**
    
    Abaixo apresentaremos as hipóteses e as análises feitas para validar ou revogar uma hipótese.
    
    > *Músicas com BPM (Batidas Por Minuto) mais altos fazem mais sucesso em termos de número de streams no Spotify.*
    > 
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%2010.png)
    
    <aside>
    ❗ A hipótese foi anulada após a análise do gráfico de dispersão, que revelou a ausência de correlação direta entre o número de streams e o BPM (Batidas por minuto). Os dados sugerem que outros fatores podem influenciar o número de streams, enquanto o BPM não parece desempenhar um papel significativo neste contexto.
    
    </aside>
    
    > *As músicas mais populares no ranking do Spotify também possuem um comportamento semelhante em outras plataformas, como a Deezer.*
    > 
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%2011.png)
    
    <aside>
    ❗ Não necessariamente, as músicas top 1 no Spotify possuem um ranking bem variável na Apple sendo a maioria abaixo do top 20, já no Deezer vemos que a maioria possui ranking 0 (pode ser um erro ou 0 representa 1).
    
    </aside>
    
    > *A presença de uma música em um maior número de playlists está correlacionada com um maior número de streams.*
    > 
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%2012.png)
    
    <aside>
    ❗ A hipótese está correta. Conforme o gráfico acima, podemos verificar que quanto maior o número de playlists maior o número de streams. A linha de tendência central indica exatamente esse comportamento.
    
    </aside>
    
    > *Artistas com um maior número de músicas no Spotify têm mais streams.*
    > 
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%2013.png)
    
    <aside>
    ❗ Correto. Ao analisar os dados, verificamos que os artistas com maiores número de música possuem um maior número de stream.
    
    </aside>
    
    > *As características da música influenciam o sucesso em termos de número de streams no Spotify.*
    > 
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%2014.png)
    
    ![Untitled](Processamento%20e%20analise%20be3a6707e08b4a5f93cf11f0d33b4a6b/Untitled%2015.png)
    
    > *As características da música influenciam o sucesso em termos de número de streams no Spotify.*
    > 
    
    <aside>
    ❗ As características tem uma correlação, tanto positiva ou para negativa, porém não necessariamente significa que seguindo as características musicais alcançará o sucesso já que depende também de fatores externos.
    
    </aside>