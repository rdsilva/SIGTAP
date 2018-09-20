Criando uma tabela única com os processos padronizados pelo SUS.
Rodrigo Silva 


# Sigtap

O objetivo deste estudo é criar uma tabela unificada com todos os parametros do Sigtap em uma única tabela. Isto irá gerar uma quantidade enorme de dados repetidos, contudo acredita-se que isto facilitará trabalhos futuros de consultas e processamento. Igualmente irá contribuir com a comunidade no que diz respeito a divulgação de um dataset importante para os estudos centrados no SUS. Por fim, o dataset a ser construído é praticamente fixo, onde não há a adição constante de novos valores visto que todo o tramite de atualização deve antes passar por portarias e normativas.

Os dados do `Sigtap` são organizados em um conjunto de pouco mais de 80 arquivos, disponibilizados mensalmente pelo próprio DataSUS. Desta forma iniciaremos trabalhando com os dados das tabelas *grupo*, *subgrupo*, *forma* *organizacional*, *procedimento* e *tuss*. No futuro irei adicionar as demais informações, tais como relação com cid, com nível de atenção e outros. 

<aside>
Competência : 09/2018
</aside>

## Dados do Sigtap

```{r biblioteca, eval=FALSE, message=TRUE, warning=FALSE, echo=TRUE}
library(dplyr)
library(textreadr)
```

```{r work directory, eval=FALSE, warning=FALSE, echo=TRUE}

setwd('TabelaUnificada_201809_v1809051203/')

grupo <- iconv(read_document('tb_grupo.txt'), "latin1", "UTF-8")
subgrupo <- iconv(read_document('tb_sub_grupo.txt'), "latin1", "UTF-8")
forma_organizacao <- iconv(read_document('tb_forma_organizacao.txt'), "latin1", "UTF-8")
procedimento <- iconv(read_document('tb_procedimento.txt'), "latin1", "UTF-8")

```

Os dados do Sigtap são disponibilizados em formato de texto (.txt) sem qualquer caracter separador que indique as colunas e valores. No entanto há em conjunto com os dados os documentos de *layout*, para cada uma das tabelas um documento associado.

Por hora irei fazer as transcrições no braço mesmo, em um futuro próximo ireis automatizar o processo de leitura do layout e do arquivo tabela para, já no processo de leitura, criar um data.frame. 

### Convertendo o arquivo TB_GRUPO

#### Layout do Arquivo

```{r layout grupo, message=TRUE, warning=FALSE, echo=FALSE, layout="l-body-outset"}

library(rmarkdown)

setwd('TabelaUnificada_201809_v1809051203/')

grupo.layout <- read.csv('tb_grupo_layout.txt', header = TRUE, sep = ',')

paged_table(grupo.layout)
```

```{r convert tb_grupo, eval=FALSE, warning=FALSE, echo=TRUE}

co_grupo <- substr(grupo, 1, 2)
no_grupo <- substr(grupo, 3, 102)
dt_competencia <- substr(grupo, 103, 108)

grupo.ds <- data.frame(co_grupo, no_grupo, dt_competencia)

```


### Convertendo o arquivo TB_SUB_GRUPO

#### Layout do Arquivo

```{r layout subgrupo, message=TRUE, warning=FALSE, echo=FALSE, layout="l-body-outset"}

library(rmarkdown)

setwd('TabelaUnificada_201809_v1809051203/')

subgrupo.layout <- read.csv('tb_sub_grupo_layout.txt', header = TRUE, sep = ',')

paged_table(subgrupo.layout)
```

```{r convert tb_subgrupo, eval=FALSE, warning=FALSE, echo=TRUE}

co_grupo <- substr(subgrupo, 1, 2)
co_sub_grupo <- substr(subgrupo, 3, 4)
no_sub_grupo <- substr(subgrupo, 5, 104)
dt_competencia <- substr(subgrupo, 105, 110)

subgrupo.ds <- data.frame(co_grupo, co_sub_grupo, no_sub_grupo, dt_competencia)

```


### Convertendo o arquivo TB_FORMA_ORGANIZACAO

#### Layout do Arquivo

```{r layout forma_organizacao, message=TRUE, warning=FALSE, echo=FALSE, layout="l-body-outset"}

library(rmarkdown)

setwd('TabelaUnificada_201809_v1809051203/')

forma_organizacao.layout <- read.csv('tb_forma_organizacao_layout.txt', header = TRUE, sep = ',')

paged_table(forma_organizacao.layout)
```

```{r convert tb_forma_organizacao, eval=FALSE, warning=FALSE, echo=TRUE}

co_grupo <- substr(forma_organizacao, 1, 2)
co_sub_grupo <- substr(forma_organizacao, 3, 4)
co_forma_organizacao <- substr(forma_organizacao, 5, 6)
no_forma_organizacao <- substr(forma_organizacao, 7, 106)
dt_competencia <- substr(forma_organizacao, 107, 112)

forma_organizacao.ds <- data.frame(co_grupo, co_sub_grupo, co_forma_organizacao,
                                   no_forma_organizacao, dt_competencia)

```


### Convertendo o arquivo TB_PROCEDIMENTO

#### Layout do Arquivo

```{r layout procedimento, message=TRUE, warning=FALSE, echo=FALSE, layout="l-body-outset"}

library(rmarkdown)

setwd('TabelaUnificada_201809_v1809051203/')

procedimento.layout <- read.csv('tb_procedimento_layout.txt', header = TRUE, sep = ',')

paged_table(procedimento.layout)
```

```{r convert tb_procedimento, eval=FALSE, warning=FALSE, echo=TRUE}

co_grupo <- substr(procedimento, 1, 2)
co_sub_grupo <- substr(procedimento, 3, 4)
co_forma_organizacao <- substr(procedimento, 5, 6)
co_procedimento <- substr(procedimento, 1, 10)
no_procedimento <- substr(procedimento, 11, 260)
qt_maxima_execucao <- substr(procedimento, 263, 266)
qt_dias_permanencia <- substr(procedimento, 267, 270)
qt_pontos <- substr(procedimento, 271, 274)
vl_idade_minima <- substr(procedimento, 275, 278)
vl_idade_maxima <- substr(procedimento, 279, 282)
vl_servico_hospitalar <- substr(procedimento, 283, 292)
vl_servico_ambulatorial <- substr(procedimento, 293, 302)
vl_servico_profissional <- substr(procedimento, 303, 312)
qt_tempo_permanencia <- substr(procedimento, 321, 324)
dt_competencia <- substr(procedimento, 325, 330)



procedimento.ds <- data.frame(co_grupo,
                              co_sub_grupo,
                              co_forma_organizacao,
                              co_procedimento,
                              no_procedimento,
                              qt_maxima_execucao,
                              qt_dias_permanencia,
                              qt_pontos,
                              vl_idade_minima,
                              vl_idade_maxima,
                              vl_servico_hospitalar,
                              vl_servico_ambulatorial,
                              vl_servico_profissional,
                              qt_tempo_permanencia,
                              dt_competencia)

```

## Organizando os dados

### Merging Grupo e Subgrupo

```{r merge grupo e subgrupo, eval=FALSE, warning=FALSE, echo=TRUE}
res_temp <- merge(grupo.ds, subgrupo.ds)
```

```{r resorting res_temp, eval=FALSE, warning=FALSE, echo=TRUE}
res_temp <- res_temp %>%
  select(co_grupo, no_grupo, co_sub_grupo, no_sub_grupo)
```

### Merging Forma Organizacional

```{r merge res_temp e organizacao, eval=FALSE, warning=FALSE, echo=TRUE}
res_temp <- merge(res_temp, forma_organizacao.ds, by = c('co_grupo', 'co_sub_grupo'))
```

```{r resorting res_temp 2, eval=FALSE, warning=FALSE, echo=TRUE}
res_temp <- res_temp %>%
  select(co_grupo, no_grupo, co_sub_grupo, no_sub_grupo, co_forma_organizacao, no_forma_organizacao)
```

### Merging os Procedimentos

```{r merge res_temp 2 e procedimentos, eval=FALSE, warning=FALSE, echo=TRUE}
result <- merge(res_temp, procedimento.ds, by = c('co_grupo', 'co_sub_grupo', 'co_forma_organizacao'))
```

```{r resorting resultado, eval=FALSE, warning=FALSE, echo=TRUE}
result <- result %>%
  select(co_grupo, no_grupo, co_sub_grupo, no_sub_grupo, co_forma_organizacao, no_forma_organizacao, co_procedimento, no_procedimento, 
         qt_maxima_execucao, qt_dias_permanencia, qt_pontos, vl_idade_minima, vl_idade_maxima, vl_servico_hospitalar, vl_servico_ambulatorial,
         vl_servico_profissional, qt_tempo_permanencia, dt_competencia)
```

## Salvando o arquivo

```{r salvando arquivo final, eval=FALSE, warning=FALSE, echo=TRUE}
write.csv2(result, 'tabela-sigtap-201809.csv', row.names = FALSE)
```

