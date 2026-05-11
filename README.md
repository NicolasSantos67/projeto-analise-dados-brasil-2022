# ============================================================
# PROJETO DE ANÁLISE, DEPURAÇÃO E LIMPEZA DE DADOS
# Tema: Indicadores socioeconômicos dos municípios brasileiros - 2022
# ============================================================

import pandas as pd
import matplotlib.pyplot as plt
from google.colab import files

print("=" * 70)
print("PROJETO DE ANÁLISE E LIMPEZA DE DADOS")
print("=" * 70)

print("""
Tema:
Análise de indicadores socioeconômicos dos municípios brasileiros no ano de 2022.

Objetivo:
Realizar a leitura, exploração, depuração, limpeza e visualização dos dados
utilizando Python, Pandas e Matplotlib no Google Colaboratory.

Etapas realizadas:
1. Upload e leitura do arquivo CSV;
2. Exploração inicial dos dados;
3. Identificação de valores nulos e duplicados;
4. Padronização da base;
5. Remoção de dados vazios;
6. Geração de gráficos;
7. Exportação de novo arquivo CSV limpo.
""")

# ============================================================
# 1. UPLOAD DO ARQUIVO
# ============================================================

print("\nFaça o upload do arquivo brasil_2022.csv")
uploaded = files.upload()

arquivo = list(uploaded.keys())[0]

print(f"\nArquivo recebido: {arquivo}")

# ============================================================
# 2. LEITURA DO CSV
# ============================================================

try:
    df = pd.read_csv(arquivo, sep=";")
except:
    df = pd.read_csv(arquivo)

print("\nArquivo carregado com sucesso!")

# ============================================================
# 3. VISUALIZAÇÃO INICIAL
# ============================================================

print("\n" + "=" * 70)
print("PRIMEIRAS LINHAS DA BASE")
print("=" * 70)

display(df.head())

print("\nTamanho da base original:")
print(f"Linhas: {df.shape[0]}")
print(f"Colunas: {df.shape[1]}")

print("\nColunas disponíveis:")
print(df.columns.tolist())

print("\nInformações gerais da base:")
df.info()

# ============================================================
# 4. ENTENDENDO O PROBLEMA
# ============================================================

print("\n" + "=" * 70)
print("ENTENDENDO O PROBLEMA")
print("=" * 70)

print("""
Esta base apresenta informações relacionadas aos municípios brasileiros no ano de 2022.

A análise permite observar características municipais e indicadores sociais,
econômicos e populacionais. Para que os dados possam ser utilizados em análises
futuras, é necessário verificar possíveis problemas de qualidade, como:

- valores vazios ou nulos;
- registros duplicados;
- colunas completamente vazias;
- espaços em branco em textos;
- necessidade de padronização dos nomes das colunas;
- possíveis inconsistências nos formatos dos dados.

A etapa de limpeza tem como objetivo deixar a base mais organizada,
confiável e adequada para futuras análises.
""")

# ============================================================
# 5. VERIFICAÇÃO DE NULOS E DUPLICADOS
# ============================================================

print("\n" + "=" * 70)
print("VALORES NULOS POR COLUNA")
print("=" * 70)

print(df.isnull().sum())

print("\nQuantidade de linhas duplicadas:")
print(df.duplicated().sum())

# ============================================================
# 6. ESTATÍSTICAS GERAIS
# ============================================================

print("\n" + "=" * 70)
print("ESTATÍSTICAS GERAIS")
print("=" * 70)

display(df.describe(include="all"))

# ============================================================
# 7. LIMPEZA / DEPURAÇÃO DOS DADOS
# ============================================================

print("\n" + "=" * 70)
print("INICIANDO LIMPEZA DOS DADOS")
print("=" * 70)

df_limpo = df.copy()

# Remove linhas totalmente vazias
df_limpo = df_limpo.dropna(how="all")

# Remove colunas totalmente vazias
df_limpo = df_limpo.dropna(axis=1, how="all")

# Remove registros duplicados
df_limpo = df_limpo.drop_duplicates()

# Padroniza nomes das colunas
df_limpo.columns = (
    df_limpo.columns
    .str.strip()
    .str.upper()
    .str.replace(" ", "_")
    .str.replace("-", "_")
)

# Remove espaços em branco das colunas de texto
for coluna in df_limpo.select_dtypes(include="object").columns:
    df_limpo[coluna] = df_limpo[coluna].astype(str).str.strip()

# Substitui valores vazios por "Não informado"
df_limpo = df_limpo.fillna("Não informado")

print("Limpeza concluída com sucesso!")

# ============================================================
# 8. COMPARAÇÃO ANTES E DEPOIS
# ============================================================

print("\n" + "=" * 70)
print("COMPARAÇÃO ANTES E DEPOIS DA LIMPEZA")
print("=" * 70)

print("Linhas antes da limpeza:", df.shape[0])
print("Linhas depois da limpeza:", df_limpo.shape[0])

print("Colunas antes da limpeza:", df.shape[1])
print("Colunas depois da limpeza:", df_limpo.shape[1])

print("\nValores nulos após a limpeza:")
print(df_limpo.isnull().sum())

print("\nPrimeiras linhas da base limpa:")
display(df_limpo.head())

# ============================================================
# 9. GRÁFICO 1 - QUANTIDADE POR REGIÃO
# ============================================================

print("\n" + "=" * 70)
print("GRÁFICO 1 - QUANTIDADE DE MUNICÍPIOS POR REGIÃO")
print("=" * 70)

if "REGIAO" in df_limpo.columns:
    plt.figure(figsize=(10, 6))
    df_limpo["REGIAO"].value_counts().plot(kind="bar")
    plt.title("Quantidade de municípios por região")
    plt.xlabel("Região")
    plt.ylabel("Quantidade de municípios")
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
else:
    print("A coluna REGIAO não foi encontrada na base.")

# ============================================================
# 10. GRÁFICO 2 - MÉDIA DO IDHM POR REGIÃO
# ============================================================

print("\n" + "=" * 70)
print("GRÁFICO 2 - MÉDIA DO IDHM POR REGIÃO")
print("=" * 70)

if "IDHM" in df_limpo.columns and "REGIAO" in df_limpo.columns:
    df_limpo["IDHM"] = pd.to_numeric(df_limpo["IDHM"], errors="coerce")

    media_idhm = df_limpo.groupby("REGIAO")["IDHM"].mean().sort_values(ascending=False)

    plt.figure(figsize=(10, 6))
    media_idhm.plot(kind="bar")
    plt.title("Média do IDHM por região")
    plt.xlabel("Região")
    plt.ylabel("Média do IDHM")
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
else:
    print("As colunas IDHM e/ou REGIAO não foram encontradas na base.")

# ============================================================
# 11. GRÁFICO 3 - TOP 10 MUNICÍPIOS COM MAIOR IDHM
# ============================================================

print("\n" + "=" * 70)
print("GRÁFICO 3 - TOP 10 MUNICÍPIOS COM MAIOR IDHM")
print("=" * 70)

possiveis_colunas_municipio = ["MUNICIPIO", "CIDADE", "NOME", "NOME_MUNICIPIO"]

coluna_municipio = None

for col in possiveis_colunas_municipio:
    if col in df_limpo.columns:
        coluna_municipio = col
        break

if coluna_municipio and "IDHM" in df_limpo.columns:
    df_limpo["IDHM"] = pd.to_numeric(df_limpo["IDHM"], errors="coerce")

    top10 = df_limpo.dropna(subset=["IDHM"]).sort_values(by="IDHM", ascending=False).head(10)

    plt.figure(figsize=(12, 6))
    plt.bar(top10[coluna_municipio], top10["IDHM"])
    plt.title("Top 10 municípios com maior IDHM")
    plt.xlabel("Município")
    plt.ylabel("IDHM")
    plt.xticks(rotation=45, ha="right")
    plt.tight_layout()
    plt.show()
else:
    print("Não foi possível gerar o gráfico de Top 10 municípios.")

# ============================================================
# 12. CONCLUSÃO
# ============================================================

print("\n" + "=" * 70)
print("CONCLUSÃO")
print("=" * 70)

print("""
Durante o desenvolvimento deste projeto foi realizada a leitura e análise
de um arquivo CSV contendo dados de municípios brasileiros.

A base foi explorada inicialmente para identificação de sua estrutura,
quantidade de linhas, colunas, valores nulos e registros duplicados.

Em seguida, foi feita a depuração dos dados, com remoção de linhas e colunas
vazias, eliminação de duplicidades, padronização dos nomes das colunas e
tratamento dos valores ausentes.

Também foram gerados gráficos utilizando Matplotlib, permitindo melhor
visualização das informações presentes na base.

Por fim, foi criado um novo arquivo CSV limpo, que poderá ser enviado ao GitHub
e utilizado em futuras etapas do projeto.
""")

# ============================================================
# 13. EXPORTAÇÃO DO NOVO CSV LIMPO
# ============================================================

nome_saida = "brasil_2022_limpo.csv"

df_limpo.to_csv(nome_saida, index=False, encoding="utf-8-sig")

print("\nArquivo limpo gerado com sucesso:", nome_saida)

files.download(nome_saida)

print("\nPROJETO FINALIZADO COM SUCESSO!")
